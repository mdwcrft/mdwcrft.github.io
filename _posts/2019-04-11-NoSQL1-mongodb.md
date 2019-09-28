---
title: ""
date: 2019-02-01
tags: []
excerpt: ""
encoding: UTF-8
---
# RDBMS

Relational Database Management Systems are based on *related* data tables. These relations often have *constraints*, for example primary keys must be unique or a column must be one data type.  

Constraints often enforce business rules and all the constraints together are called the *schema*. An RDBMS with a schema is called a *schema on write* system.  

NoSQL purposely ignores schema on write, instead enforcing *schema on read*.

## Integrity

### Declaritative

Constraints on a DB object suggests *declaritative* integrity. This is robust but inflexable.

### Procedural

Applying constraints to the front end input form is a *procedural* integrity. This ia flexible but vulnerable to hacking.

Javascript is used for client side scripting as it runs on the user machine. Enforcing integrity at this point is efficient as it doesn't need a 'round trip' to the data server to work out if an entry is allowed.

# MongoDB

Top 'document' (JSON) storage DB.  

Set up:
1. Make `C:\data` folder
2. Download & install MongoDB

Running:
1. Start Mongo Daemon (mongod.exe)
2. Start client (mongo)  
Python package is `pymongo`

Storage:

- Server
  - Database
    - Collection
      - Document { }

The Mongo shell is all javascript so it will accept normal javascript statements. 

### Mongo commands

The inbuilt variable `db` is assigned to that database currently in use.

- Show databases: `show dbs;`  
- Show collections: `show collections;`
- Create database 'kubrick' with collection 'people' containing document with name: `use kubrick;` + `db.people.insert("Name":"Chad");`  
- Query: `db.collection.find(<query>, <projection>);`. Here `query` is the filter, `projection` 
- Import data file `mongoimport --db kubrick --collection restaurants --drop --file restaurants.json;`

### Equality

`$eq`, `$neq`, `$gt`, `$gte`, `$lt`, `$lte`, `$in`, `$nin`

- Find documents with age > 20: `db.people.find({age : {$gt:20}}, {});`
- Find documents with an age key: `db.people.find({age : {$exists:true}}, {});`

### .find() Query and Projection

- Select top 5 `db.restaurants.find().limit(5)`
- Select second top 5, display as pretty `db.restaurants.find().skip(5).limit(5).pretty()`
- Show only _id, 'cuisine', 'borough', & 'name' (projection): `db.restaurants.find({},{cuisine:1, borough:1, name:1}).pretty()`
- Show nested features: `db.restaurants.find({},{"grades.score":1})`
- Filter to only show Indian cuisine AND in Brooklyn AND with the first grades.score over 15: `db.restaurants.find({cuisine:"Indian", "borough":"Brooklyn", "grades.score":{$gte:15},{cuisine:1, name:1, "grades.0.score":1}).pretty()`
- Same as above with OR: `db.restaurants.find({$or: [{cuisine:"Indian"}, {"borough":"Brooklyn"}], "grades.score":{$gte:15}},{cuisine:1, borough:1, name:1, "grades.0.score":1}).pretty()`
- Filter to show those with a grades array of size 5: `db.restaurants.find({cuisine:"Indian", grades:{$size:5}},{cuisine:1, name:1, "grades.score":1}).pretty()`

### Updates and deletes

`db.collection.update({filter},{change})`

- Alter existing element: `db.people.update({name:"megan"},{$set: {age: 64}})`
- Increment by 1: same as above with `$inc`
- Increment all elements in array by 1: `db.restaurants.update({cuisine:"Indian", "borough":"Brooklyn"},{$inc: {"grades.$[].score":1}}, {multi:true})`
- Add new element:

### Aggregation Pipeline

A pipeline is a series of data processing steps.

> *Example*: `$match` -> `$group` -> `sum()` -> `$project`

```mongodb
db.orders.aggregate( [
                        { $match: { status: "A"} }
                        { $group: { _id: "$cust_id", total: {$sum: "$amount"} } }
                    ] )
```

Here the `$` before variable statements means take the *value of* that variable.

- Show only Brooklyn restaurants: `db.restaurants.aggregate( [ {$match: {"borough":"Brooklyn"} } ] ).pretty()`
- Show the count of Brooklyn restaurants by cuisine: `db.restaurants.aggregate( [ {$match: {borough:"Brooklyn"} }, {$group: {_id: "$cuisine", numof: {$sum: 1} } } ] ).pretty()`
- Then show only where numof greater than 5, sorted descending: `db.restaurants.aggregate( [ {$match: {borough:"Brooklyn"} }, {$group: {_id: "$cuisine", numof: {$sum: 1} } }, {$match: {numof: {$gte: 10} } }, {$sort: {numof: -1} } ] ).pretty()`

- Unwind the grades of a selected restaurant into seperate documents: `db.restaurants.aggregate( [ {$match: {"restaurant_id": "40364305"}}, {$unwind: "$grades"} ] ).pretty()`
- Average score of all grade scores sorted: `db.restaurants.aggregate( [ {$unwind: "$grades"}, {$group: {_id: "$name", average_score: {$avg: "$grades.score"} } }, {$sort: {"average_score": -1} } ] ).pretty()`