---
title: "Using XGBoost models with python's Flower"
date: 2023-03-03T00:00:00+02:00
draft: false
tags:
  - python
  - fl
  - ml
---


The idea here is to use Flower to create a federated infrastructure to run an `XGBClassifier` in a series of datasets located in exclusive-access nodes. 

This means that a central server, the one that will connect and aggregate the models, is required as well as the local client, the one training the models on the exclusive-access datasets.

# Client code

The code assumes that there is an environment-variables called `DATA_PATH` pointing to a CSV-file having the data to be used. With this in mind, the first step is to create the `XGBClient`, wrapping a `NumPyClient`'s from `flwr`.

We should start with the imports:

```python
import os

import flwr as fl
import xgboost as xgb

from utils_flwr_xgboost import *
```

We will use the `os` mode to access the `DATA_PATH` variables, the `flwr` to create the wrapper and start the client, `xgb` to access the models class, and `utils_flwr_xgboost` is a utility module that has five functions: `load_data`, `prepare_data`, `get_parameters`, `model_from_paramaters`, and `get_metrics_from_model`. We will describe them during the implementation of the client and the server.

We will start with the generic code to load the data and start the client:

```python
tmp_path = '/home/.../tmp-cl/'
X_train, X_test, y_train, y_test = prepare_data( os.getenv( 'DATA_PATH' ) )
```

The function `prepare_data` calls the `load_data` and then splits the data into training and testing sets:

```python
from sklearn.model_selection import train_test_split

def load_data( data_path ):
    data = pd.read_csv( data_path )
    code_id = 'ID'
    code_outcome = 'class'
    X = data.drop( [ code_id, code_outcome ], axis = 1 )
    y = data[ code_outcome ]
    return X, y

def prepare_data( data_path, test_size = 0.2, random_state = 42 ):
    X, y = load_data( data_path )
    X_train, X_test, y_train, y_test = train_test_split( X, y, test_size = test_size, random_state = random_state )
    return X_train, X_test, y_train, y_test
```

Now, let's go for the `XGBClient`'s implementation:


```python
class XGBClient( fl.client.NumPyClient ):
    def __init__( self ):
        self.model = xgb.XGBClassifier()
        self.model.fit( X_train, y_train )

    def get_parameters( self, config ):
        return get_parameters( self.model, tmp_path )

    def fit( self, parameters, config ):
        self.model = model_from_paramaters( parameters, tmp_path )
        self.model.fit( X_train, y_train )
        return (
            get_parameters( self.model, tmp_path ),
            len( X_train ),
            get_metrics_from_model( self.model, X_test, y_test )
        )

    def evaluate(self, parameters, config):
        self.model = model_from_paramaters( parameters, tmp_path )
        metrics = get_metrics_from_model( self.model, X_test, y_test )
        return (
            metrics[ 'loss' ], 
            int( len( X_test ) ), 
            metrics
        )
```

Here the fun starts! 

Our `XGBClient` is implementing 3 required methods so the infrastructure can communicate the updates on the model from client to server and vice-versa: `get_parameters`, `fit`, and `evaluate`.

### `get_parameters`

The method, calling the `get_parameters` from our utility module, is packaging the "parameters" of the model. The name "parameters" can be a bit confusing or create a misunderstanding. For the XGBoost family of models those "parameters" are the _weights of the leaves_. Another example would be that for a linear or logistic model, those "parameters" would, be the _coefficients_ of the models.

In our case, the one for XGBoost models, we need to obtain the full definition of the forest of trees. Following XGBoost's documentation we see that we could use [`dump_model`](https://xgboost.readthedocs.io/en/stable/python/python_api.html#xgboost.Booster.dump_model) or [`save_model`](https://xgboost.readthedocs.io/en/stable/python/python_api.html#xgboost.XGBClassifier.save_model). But if we read properly, only the definition created using the `save_model` can be then loaded again to recreate the model.

```python
from pathlib import Path

def get_parameters( model, tmp_path ):
    tmp_model = Path( tmp_path ) / 'model_tmp_prms.json'
    model.get_booster().save_model( tmp_model )
    with open( tmp_model, 'r' ) as fr:
        params = json.load( fr )
    return [ json.dumps( params ) ]
```

Therefore we need a temporary JSON file where the model is saved. We follow by reading this file and returning it serialized. See that the `get_paramaters` method from our client needs to return a list.

### `fit`

This method is the one in charge to train the model given a set of parameters. This method needs, first, to recreate a model from the set of parameters. The function `model_from_paramaters` is the one in charge of doing so:

```python
from pathlib import Path

def model_from_paramaters( parameters, tmp_path ):
    parameters = parameters[ 0 ]
    tmp_model = Path( tmp_path ) / 'model_tmp_fit.json'
    parameters = json.loads( str( parameters ) )
    with open( tmp_model, 'w' ) as fw:
        json.dump( parameters, fw )
    model = xgb.XGBClassifier()
    model.load_model( tmp_model )
    return model
```

We start by loading the parameters to a dictionary from the serialized JSON that is given. Then, we save the model definition to a file and load this file to recreate the model using the method [`load_model`](https://xgboost.readthedocs.io/en/stable/python/python_api.html#xgboost.XGBClassifier.load_model). 

Once the model is created from the set of parameters, we fit it and return the required results: the newly refined parameters, the size of the training set, and some quality metrics.

```python
from sklearn.metrics import log_loss

def get_metrics_from_model( model, X_test, y_test ):
    loss = log_loss( y_test, model.predict_proba( X_test ) )
    accuracy = float( model.score( X_test, y_test ) )
    return { 'loss': float( loss ), 'accuracy': accuracy }
```

### `evaluate`

This method evaluates a given set of parameters on the local dataset and returns the metrics.

# Server code

Lastly, we will write the code used on the server side. As for our client, the code assumes that there is an environment-variables called `DATA_PATH` pointing to a CSV-file having the data to be used.

The following imports are needed by the server script.

```python
import os

import flwr as fl
import numpy as np
import xgboost as xgb

from flwr.common import ndarrays_to_parameters, parameters_to_ndarrays

from utils_flwr_xgboost import *
```

Now, we will be implementing not a `flwr`'s server but a `flwr`'s strategy. A strategy is in change of defining how the federation works as well as initializing parameters and aggregating models. We will use the `FedAvg` strategy as a base strategy.


```python
class XGBAvgStrategy( fl.server.strategy.FedAvg ):
    def aggregate_fit( self, rnd, results, failures ):
        if not results:
            return None, {}
        status       = [ x[1].status for x in results ]
        parameters   = [ json.loads( parameters_to_ndarrays( x[1].parameters )[ 0 ].item( 0 ) ) for x in results ]
        num_examples = [ x[1].num_examples for x in results ]
        metrics      = [ x[1].metrics for x in results ]
        aggregated_params = self.aggregate( status, parameters, num_examples, metrics )
        model = model_from_paramaters( aggregated_params, tmp_path )
        model.fit( X_train, y_train )
        return ndarrays_to_parameters( np.array( get_parameters( model, tmp_path ) ) ), {}

    def aggregate( self, status, parameters, num_examples, metrics ):
        # [ this one is up to you :-) ]
        parameters = json.dumps( parameters[ 0 ] )
        return [ parameters ]
```

### `aggregate_fit`

This method is responsible to aggregate the models obtained from the multiple nodes and return a single model from them. In our implementation, we put here all the steps required to obtain all the required data from each of the nodes and models. For instance: the `status` variable indicates if the transmission between each node and the server worked or failed, `parameters` will have all the dictionaries loaded from the transmitted JSONs, `num_examples` will provide the number of samples used to train each model, and `metrics` will have the metrics of each node.

Then, we call our custom `aggregate` method providing the previous lists, and create a new model from the aggregated models before sending it back to the clients to be trained again.

The tricky steps here are being able to set the parameters to be read from and sent to the clients. Take a look and see that to read the parameters obtained from the clients we are using `parameters_to_ndarrays` and `ndarrays_to_parameters` to send them to the clients. 

### `aggregate`

Here is where magic happens and all the `XGBClassifier` objects are averaged and a single new set of parameters is returned.

Finally, we only need to load the data used to assign the initial set of parameters and run the strategy.

```python
tmp_path = '/home/.../xgboost/tmp-sr/'
X_train, X_test, y_train, y_test = prepare_data( os.getenv( 'DATA_PATH' ) )

fl.server.start_server(
    server_address = "[::]:8080", 
    config         = fl.server.ServerConfig(num_rounds=3), 
    strategy       = XGBAvgStrategy()
)
```

# Resources

* [Flower Documentation](https://flower.dev/docs/index.html)
* [Scikit-learn's logistic regression on flower](https://flower.dev/blog/2021-07-21-federated-scikit-learn-using-flower/)
* [XGBoost's documentation](https://xgboost.readthedocs.io/en/stable/index.html)