---
title: "Some 'must' methods for JavaScript array"
date: 2019-11-07T00:00:00+02:00
draft: false
tags:
  - javascript
---

I recently been working a lot with JavaScript and I missed some methods for the JavaScript's array. 

First I missed three core methods related of the content of the array. A method to get the unique elements in the array (`arr.unique()`), a method that returns the number of occurrences of a given element (`arr.count(val)`), and a method that returns some sort of table with the number of occurrences of each unique element in the array - see this one as a combination of the previous two - (`arr.table()`).

Then I missed a couple of methods that allows for further flexibility on playing with the content of the array. For instance a method to check if an element of the array has a property/attribute with a given value (`arr.includesObject(val, key)`), and a method to sort the array given a property/attribute of the items in the array (`arr.sortObject(key)`).

I am writing here some basic implementations of what I've been missing:

## Method '_unique_'

Implementation:

```javascript
Object.defineProperties(Array.prototype, {
    unique: {
        value: function() {
            /* Returns the unique values set in the array. 
            */
            return [... new Set(this)];
        }
    }
});
```

Example:

```
var arr = [1, 3, 4, 5, 1, 2, 3, 4]
arr.unique()
>> Array(5) [ 1, 3, 4, 5, 2 ]
```

## Method '_count_'

Implementation:

```javascript
Object.defineProperties(Array.prototype, {
    count: {
        value: function(val) {
            /* Counts number of occurrences of a given value (val) in array
               Uses the javascript == notion of equality.
            */
            var count = 0;
            for(let ii = 0; ii < this.length; ii++) {
                if (this[ii] == val) {
                    count++;
                }
            }
            return count;
        }
    }
});
```

Example:

```
var arr = [1, 3, 4, 5, 1, 2, 3, 4]
arr.count(1)
>> 2
arr.count(5)
>> 1
```

## Method '_table_'

Implementation:

```javascript
Object.defineProperties(Array.prototype, {
    table: {
        value: function() {
            /* Returns a map with pairs of unique element and the
                number of its occurrences in the array.
            */
            let x = this.unique();
            return x.map( (y) => [y, this.count(y)] );
        }
    }
});
```

Example:

```
var arr = [1, 3, 4, 5, 1, 2, 3, 4]
arr.table()
>> (5) […]
	0: Array [ 1, 2 ]
	1: Array [ 3, 2 ]
	2: Array [ 4, 2 ]
	3: Array [ 5, 1 ]
	4: Array [ 2, 1 ]
	length: 5
	<prototype>: Array []
```

## Method '_includesObject_'

Implementation:

```javascript
Object.defineProperties(Array.prototype, {
    includesObject: {
        value: function(val, key) {
            /* Returns true if found object in array having a property (key)
                with specific value (val).
                Uses the javascript == notion of equality.
            */
            return this.some( (item) => item[key] == val );
        }
    }
});
```

Example:

```
var arr = [{'n': 'a', 'v': 2, 'k': 3.2}, 
    {'n': 'b', 'v': 3, 'k': 0.1}, 
    {'n': 'c', 'v': 4, 'k': 1.8}
]
arr.includesObject('a', 'n')
>> true
arr.includesObject('z', 'n')
>> false
```

## Method '_sortObject_'

Implementation:

```javascript
Object.defineProperties(Array.prototype, {
    sortObject: {
        value: function(key, rev = false) {
            /* Sorts the array by using a specific property (key) of the
                items in the array. Can be reversed (rev).
            */
            return this.sort(function(a, b){
                let keyA = a[key];
                let keyB = b[key];
                
                if(keyA < keyB) {
                    return rev ? 1 : -1;
                } else if(keyA > keyB) {
                    return rev ? -1 : 1;
                } else {
                    return 0;
                }
            });
        }
    }
});
```

Example:

```
var arr = [{'n': 'a', 'v': 2, 'k': 3.2}, 
    {'n': 'b', 'v': 3, 'k': 0.1}, 
    {'n': 'c', 'v': 4, 'k': 1.8}
]
arr.sortObject('v')
>> (3) […]
	​0: Object { n: "a", v: 2, k: 3.2 }
	​1: Object { n: "b", v: 3, k: 0.1 }
	​2: Object { n: "c", v: 4, k: 1.8 }
	length: 3
	<prototype>: Array []
arr.sortObject('k')
>> (3) […]
	​0: Object { n: "b", v: 3, k: 0.1 }
	​1: Object { n: "c", v: 4, k: 1.8 }
	​2: Object { n: "a", v: 2, k: 3.2 }
	length: 3
	<prototype>: Array []
```