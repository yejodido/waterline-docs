---
layout: default
---

# Query Language

The Waterline Query language is an object based criteria used to retrive the records from any of
the supported database adapters. This means that you can use the same query on MySQL as you do on
Redis or MongoDb. This allows you to change your database without changing your codebase.

All queries inside of Waterline are case insensitive. This allows for easier querying but makes
indexing strings tough. This is something to be aware of if you are indexing and searching on string
fields.

## Query Language Basics

The criteria objects are formed using one of four types of object keys. These are the top level
keys used in a query object. It is loosely based on the criteria used in MongoDB with a few slight
variations.

Queries can be built using either a `where` key to specify attributes, which will allow you to also
use query options such as `limit` and `skip` or if `where` is excluded the entire object will be
treated as a `where` criteria.

```javascript
Model.find({ where: { name: 'foo' }, skip: 20, limit: 10, sort: 'name DESC' });

// OR

Model.find({ name: 'foo' })
```

### Key Pairs

A key pair can be used to search records for values matching exactly what is specified. This is the
base of a criteria object where the key represents an attribute on a model and the value is a strict
equality check of the records for matching values.

```javascript
Model.find({ name: 'walter' })
```

They can be used together to search multiple attributes.

```javascript
Model.find({ name: 'walter', state: 'new mexico' })
```

### Modified Pairs

Modified pairs also have model attributes for keys but they also use any of the supported criteria
modifiers to perform queries where a strict equality check wouldn't work.

```javascript
Model.find({
  name : {
    'contains' : 'alt'
  }
})
```

### In Pairs

IN queries work similarly to mysql 'in queries'. Each element in the array is treated as 'or'.

```javascript
Model.find({
  name : ['Walter', 'Skyler']
});
```

### Not-In Pairs

Not-In queries work similar to `in` queries, except for the nested object criteria.

```javascript
Model.find({
  name: { '!' : ['Walter', 'Skyler'] }
});
```

### Or Pairs

Performing `OR` queries is done by using an array of query pairs. Results will be returned that
match any of the criteria objects inside the array.

```javascript
Model.find({
  or : [
    { name: 'walter' },
    { occupation: 'teacher' }
  ]
})
```

## Criteria Modifiers

The following modifiers are available to use when building queries.

* `'<'` / `'lessThan'`
* `'<='` / `'lessThanOrEqual'`
* `'>'` / `'greaterThan'`
* `'>='` / `'greaterThanOrEqual'`
* `'!'` / `'not'`
* `'like'`
* `'contains'`
* `'startsWith'`
* `'endsWith'`


### '<' / 'lessThan'

Searches for records where the value is less than the value specified.

```javascript
Model.find({ age: { '<': 30 }})
```

### '<=' / 'lessThanOrEqual'

Searches for records where the value is less or equal to the value specified.

```javascript
Model.find({ age: { '<=': 21 }})
```

### '>' / 'greaterThan'

Searches for records where the value is more than the value specified.

```javascript
Model.find({ age: { '>': 18 }})
```

### '>=' / 'greaterThanOrEqual'

Searches for records where the value is more or equal to the value specified.

```javascript
Model.find({ age: { '>=': 21 }})
```

### '!' / 'not'

Searches for records where the value is not equal to the value specified.

```javascript
Model.find({ name: { '!': 'foo' }})
```

### 'like'

Searches for records using pattern matching with the `%` sign.

```javascript
Model.find({ food: { 'like': '%beans' }})
```

### 'contains'

A shorthand for pattern matching both sides of a string. Will return records where the value
contains the string anywhere inside of it.

```javascript
Model.find({ class: { 'contains': 'history' }})

// The same as

Model.find({ class: { 'like': '%history%' }})
```

### 'startsWith'

A shorthand for pattern matching the right side of a string. Will return records where the value
starts with the supplied string value.

```javascript
Model.find({ class: { 'startsWith': 'american' }})

// The same as

Model.find({ class: { 'like': 'american%' }})
```

### 'endsWith'

A shorthand for pattern matching the left side of a string. Will return records where the value
ends with the supplied string value.

```javascript
Model.find({ class: { 'endsWith': 'can' }})

// The same as

Model.find({ class: { 'like': '%can' }})
```

### 'Date Ranges'

You can do date range queries using the comparison operators.

```javascript
Model.find({ date: { '>': new Date('2/4/2014'), '<': new Date('2/7/2014') } })
```

## Query Options

Query options allow you refine the results that are returned from a query. The current options
available are:

* `limit`
* `skip`
* `sort`
* `select`

### Limit

Limits the number of results returned from a query.

```javascript
Model.find({ where: { name: 'foo' }, limit: 20 })
```

### Skip

Returns all the results excluding the number of items to skip.

```javascript
Model.find({ where: { name: 'foo' }, skip: 10 });
```

### Pagination

`skip` and `limit` can be used together to build up a pagination system.

```javascript
Model.find({ where: { name: 'foo' }, limit: 10, skip: 10 });
```

### Sort

Results can be sorted by attribute name. Simply specify an attribute name for natural (ascending)
sort, or specify an `asc` or `desc` flag for ascending or descending orders respectively.

```javascript
// Sort by name in ascending order
Model.find({ where: { name: 'foo' }, sort: 'name' });

// Sort by name in descending order
Model.find({ where: { name: 'foo' }, sort: 'name DESC' });

// Sort by name in ascending order
Model.find({ where: { name: 'foo' }, sort: 'name ASC' });

// Sort by binary notation
Model.find({ where: { name: 'foo' }, sort: { 'name': 1 }});

// Sort by multiple attributes
Model.find({ where: { name: 'foo' }, sort: { name:  1, age: 0 });
```

### Select

Apply a projection to a waterline query.

```javascript
// Returns only the field name
Model.find({ where: { age: { '<': 30 } }, select: ['name'] })
```
