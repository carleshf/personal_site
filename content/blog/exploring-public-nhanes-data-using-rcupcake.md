---
title: "Exploring public NHANES data using Rcupcake"
date: 2017-10-30T00:00:00+02:00
draft: false
tags:
  - R
---

The `Rcupcake` package contains functions to query different databases through the BD2K RESTful API. BD2K RESTful API is an interface that provides access to different data sources, making easier data accessibility, analysis reproducibility and scalability.

The package is installed via `devtools` using it's GitHub URL (`hms-dbmi/Rcupcake`) or following their guide (also in GiHub - [here](https://github.com/hms-dbmi/Rcupcake)).

```R
library( Rcupcake )
```

```R
library( knitr )
library( stringr )
opts_chunk$set(
    fig.path='Figs/',
    echo=TRUE, 
    warning=FALSE, 
    message=FALSE,
    cache=FALSE
)
```

`Rcupcake` package follows a four-step process to retrieve the data from a database:

  1. Start session
  2. Select the variables of interest
  3. Build the JSON query
  4. Run the query to obtain the data


The `start.session` function establishes a connection to the database of interest and it requires two arguments:

  - `url`: the url of the database of interest.
  - `apiKey`: the key to successfully access to the data.

It works as follows

```R
sessionEx <- start.session( 
    url    = "url", 
    apiKey = "key"
)
```

In this case, the URL corresponds to `https://nhanes.hms.harvard.edu/`.

```R
sessionEx
```

```
## [1] "Start Session: success"
```

With the connection opened we can proceed to explores the existing variables. To this end we use `get.children`.

```R
nhanesCategories <- get.children( 
   url       = "https://nhanes.hms.harvard.edu/", 
   fieldname = "/nhanes/Demo"
)
nhanesCategories
```

```
## [1] "/nhanes/Demo/demographics/demographics/"  
## [2] "/nhanes/Demo/examination/examination/"    
## [3] "/nhanes/Demo/laboratory/laboratory/"      
## [4] "/nhanes/Demo/questionnaire/questionnaire/"
```


As you can see, the content of the `fieldname` it not really clear since it includes a _Demo_ and the results includes two times the _category_. Let's hope the `Rcupcake`'s paper puts some light here.

Using the same strategy we can obtain the list of laboratory tests:

```R
nhanesLaboratory <- get.children( 
   url       = "https://nhanes.hms.harvard.edu/", 
   fieldname = "/nhanes/Demo/laboratory/laboratory/"
)
nhanesLaboratory
```

```
##  [1] "/nhanes/Demo/laboratory/laboratory/acrylamide/"           
##  [2] "/nhanes/Demo/laboratory/laboratory/aging/"                
##  [3] "/nhanes/Demo/laboratory/laboratory/allergen test/"        
##  [4] "/nhanes/Demo/laboratory/laboratory/bacterial infection/"  
##  [5] "/nhanes/Demo/laboratory/laboratory/biochemistry/"         
##  [6] "/nhanes/Demo/laboratory/laboratory/blood/"                
##  [7] "/nhanes/Demo/laboratory/laboratory/cotinine/"             
##  [8] "/nhanes/Demo/laboratory/laboratory/diakyl/"               
##  [9] "/nhanes/Demo/laboratory/laboratory/dioxins/"              
## [10] "/nhanes/Demo/laboratory/laboratory/furans/"               
## [11] "/nhanes/Demo/laboratory/laboratory/heavy metals/"         
## [12] "/nhanes/Demo/laboratory/laboratory/hormone/"              
## [13] "/nhanes/Demo/laboratory/laboratory/hydrocarbons/"         
## [14] "/nhanes/Demo/laboratory/laboratory/melamine/"             
## [15] "/nhanes/Demo/laboratory/laboratory/nutrients/"            
## [16] "/nhanes/Demo/laboratory/laboratory/pcbs/"                 
## [17] "/nhanes/Demo/laboratory/laboratory/perchlorate/"          
## [18] "/nhanes/Demo/laboratory/laboratory/pesticides/"           
## [19] "/nhanes/Demo/laboratory/laboratory/phenols/"              
## [20] "/nhanes/Demo/laboratory/laboratory/phthalates/"           
## [21] "/nhanes/Demo/laboratory/laboratory/phytoestrogens/"       
## [22] "/nhanes/Demo/laboratory/laboratory/polybrominated ethers/"
## [23] "/nhanes/Demo/laboratory/laboratory/polyflourochemicals/"  
## [24] "/nhanes/Demo/laboratory/laboratory/viral infection/"      
## [25] "/nhanes/Demo/laboratory/laboratory/volatile compounds/"
```

Or the demographic information:

```R
nhanesDemographic <- get.children( 
   url       = "https://nhanes.hms.harvard.edu/", 
   fieldname = "/nhanes/Demo/demographics/demographics/"
)
nhanesDemographic
```

```
##  [1] "/nhanes/Demo/demographics/demographics/AGE/"      
##  [2] "/nhanes/Demo/demographics/demographics/area/"     
##  [3] "/nhanes/Demo/demographics/demographics/DMDBORN/"  
##  [4] "/nhanes/Demo/demographics/demographics/DMDMARTL/" 
##  [5] "/nhanes/Demo/demographics/demographics/education/"
##  [6] "/nhanes/Demo/demographics/demographics/INDFMPIR/" 
##  [7] "/nhanes/Demo/demographics/demographics/RACE/"     
##  [8] "/nhanes/Demo/demographics/demographics/SDDSRVYR/" 
##  [9] "/nhanes/Demo/demographics/demographics/SDMVPSU/"  
## [10] "/nhanes/Demo/demographics/demographics/SDMVSTRA/" 
## [11] "/nhanes/Demo/demographics/demographics/SES_LEVEL/"
## [12] "/nhanes/Demo/demographics/demographics/SEX/"      
## [13] "/nhanes/Demo/demographics/demographics/WTMEC2YR/" 
## [14] "/nhanes/Demo/demographics/demographics/WTMEC4YR/"
```

```R
nhanesExamination <- get.children( 
   url       = "https://nhanes.hms.harvard.edu/", 
   fieldname = "/nhanes/Demo/examination/examination/"
)
nhanesExamination
```

```
## [1] "/nhanes/Demo/examination/examination/blood pressure/"  
## [2] "/nhanes/Demo/examination/examination/body measures/"   
## [3] "/nhanes/Demo/examination/examination/physical fitness/"
```

Once we now the _paths_ of the variables we want to use we put them in a vector.

```R
nhanesPressure <- get.children( 
    url = "https://nhanes.hms.harvard.edu/", 
    fieldname = "/nhanes/Demo/examination/examination/blood pressure/"
)
nhanesMetals <- get.children( 
    url = "https://nhanes.hms.harvard.edu/", 
    fieldname = "/nhanes/Demo/laboratory/laboratory/heavy metals/"
)
nhanesVector <- c(
    nhanesDemographic,
    nhanesPressure,
    nhanesMetals
)
```

This vector indicates to `my.query` where to locate the *fields* we want to retrieve to from the database. The *field* must be indicated into `myfields`, separated using the vertical columns character (`|`).

At this point we need to now that the first *field* filters the output by existing value. This means that the samples with missing data in the first *field* will be dropped by default.

```R
query <- my.query( 
    myfields = "AGE|SEX|mean diastolic|Mercury, hair (ppm)|Lead (ug per dL)", 
    myvector = nhanesVector, 
    url      = "https://nhanes.hms.harvard.edu/"
)
```

The function returns the JSON structure of the query:

```R
query
```

```
## {
##   "select": [
##     {
##       "field": {
##         "pui": "/nhanes/Demo/demographics/demographics/AGE/",
##         "dataType": "string"
##       },
##       "alias": "/nhanes/Demo/demographics/demographics/AGE/"
##     },
##     {
##       "field": {
##         "pui": "/nhanes/Demo/demographics/demographics/SEX/female/",
##         "dataType": "string"
##       },
##       "alias": "/nhanes/Demo/demographics/demographics/SEX/"
##     },
##     {
##       "field": {
##         "pui": "/nhanes/Demo/demographics/demographics/SEX/male/",
##         "dataType": "string"
##       },
##       "alias": "/nhanes/Demo/demographics/demographics/SEX/"
##     },
##     {
##       "field": {
##         "pui": "/nhanes/Demo/examination/examination/blood pressure/mean diastolic/",
##         "dataType": "string"
##       },
##       "alias": "/nhanes/Demo/examination/examination/blood pressure/mean diastolic/"
##     }
##   ],
##   "where": [
##     {
##       "field": {
##         "pui": "/nhanes/Demo/demographics/demographics/AGE/",
##         "dataType": "STRING"
##       },
##       "predicate": "CONTAINS",
##       "fields": {
##         "ENOUNTER": "NO"
##       }
##     }
##   ]
## }
```

The last step is to retrieve the data. Thai is done with `my.data`, it retrieves he data as a `data.frame` and save it into a file.

```R
data <- my.data( 
    query          = query,
    url            = "https://nhanes.hms.harvard.edu/", 
    responseFormat = "CSV", 
    outputPath     = "~/dataNHANES.txt"
)
```

The variabte data can be explored as standard `data.frame`:

```R
dim( data )
```
```
## [1] 41474     4
```
```R
colnames( data )
```
```
## [1] "patient_id"                                                          
## [2] "X.nhanes.Demo.demographics.demographics.SEX."                        
## [3] "X.nhanes.Demo.examination.examination.blood.pressure.mean.diastolic."
## [4] "X.nhanes.Demo.demographics.demographics.AGE."
```

```R
# Gender Proportion
prop.table( table( data[ , 2] ) )
```
```
## 
##    female      male 
## 0.5114047 0.4885953
```

```R
# Range of age
summary( data[ , 4] )
```
```
##    Min. 1st Qu.  Median    Mean 3rd Qu.    Max. 
##    0.00   10.00   19.00   29.25   47.00   85.00
```

```R
# Range of Blood Diastolic Mean
summary( data[ , 3 ] )
```
```
##    Min. 1st Qu.  Median    Mean 3rd Qu.    Max.    NA's 
##    0.00   57.33   65.33   64.98   73.33  132.00   15438
```
