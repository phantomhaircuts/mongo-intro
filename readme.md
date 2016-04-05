# MongoDB
## Learning Objectives

- Compare and contrast relational to document based (NoSql)
- Setup local mongo db server
- CRUD documents using mongo CLI
- Build a simple node CLI to query mongodb

## Opening

Well, we've come full circle ... again. What we're learning today isn't
fundamentally that different from what we know. We're going to learn a
different way to store information. A document-based non-relational way. We've
learned a considerable amount of information about relational databases.
We join on foreign keys in relational databases in order to query our database
. Sometimes these joins can get really expensive to query the database. When
dealing with less complex associations, non relational databases can be more
effective. We've also seen that schema's in SQL(relational DB's) are fairly
rigid. Adding columns can be taxing(migrations). Also if we delete a row in a
table that is being used as a foreign key in another table, we must delete
all the rows associated with that foreign key before we can delete the parent
object. Mongo provides a more flexible, scalable solution for less complex
domain models.

> This is not to say that mongo is a better solution than postgres or other
SQL libraries, but an alternative solution.


MongoDB is an open-source **document database** that provides:
- High Performance
- High Availability
- Automatic Scaling

## Document Database?

### A basic example of a `Person` document:

```json
{
  "name": "Sue",
  "age": 26,
  "status": "Active",
  "groups": ["sass", "express"]
}
```
---
TPS: What do you see?

### A Document

- json
- different data types
- even arrays

### More complicated example of a `Restaurant` document:

```json
{
   "_id" : ObjectId("54c955492b7c8eb21818bd09"),
   "address" : {
      "street" : "2 Avenue",
      "zipcode" : "10075",
      "building" : "1480",
      "coord" : [ -73.9557413, 40.7720266 ],
   },
   "borough" : "Manhattan",
   "cuisine" : "Italian",
   "grades" : [
      {
         "date" : ISODate("2014-10-01T00:00:00Z"),
         "grade" : "A",
         "score" : 11
      },
      {
         "date" : ISODate("2014-01-16T00:00:00Z"),
         "grade" : "B",
         "score" : 17
      }
   ],
   "name" : "Vella",
   "restaurant_id" : "41704620"
}
```

## Documentation

https://www.mongodb.org

## Documents

### A record in MongoDB is a document

- a data structure composed of field(key) and value pairs.
  - similar to JSON objects.
  - stored as BJSON
- fields may include other documents, arrays, and arrays of documents.
- analogous to rows in a table

## Collections

MongoDB stores documents in collections.

- Collections are analogous to tables in relational databases.
- does **NOT** require its documents to have the same schema.
- documents stored in a collection must have a unique `_id` field that acts
as a primary key.

## Installation
- [Mac OS X](https://docs.mongodb.org/getting-started/shell/tutorial/install-mongodb-on-os-x/)
- [Linux](https://docs.mongodb.org/manual/tutorial/install-mongodb-on-ubuntu/)

### Start mongo:

```
mongod --config /usr/local/etc/mongod.conf
```

You should see:

`nothing`
> no news is good news

### More info?

```
brew info mongo
```

## Mongo shell

### Start the shell

```
$ mongo
```

> feels a little bit like a JS REPL

You should see:

```
MongoDB shell version: 3.0.3
connecting to: test
>
```

### Help

```
help
```

ThinkShare (2min):
- What jumps out as important?
- Try it

## What jumped out to me
- `show dbs`: show database names
- `show collections`:  show collections in current database
- `use <db_name>`: set current database
- `db.foo.find()`: list objects in collection foo

Also:

- `<tab>` key completion
- `<up-arrow>` and the `<down-arrow>` for history.

In the mongo REPL we want to connect to create/connect to a database.

We *want* to work with the `restaurants` database:

```
use restaurant_db
```

Verify:
```
> db
restaurant_db
```

Common Mistake:
`show dbs`
> note we don't see restaurants listed. It isn't until we add a document to
our database does it list the DB in `show dbs`

## CLI: Create a record

### Insert

- use `insert()` to add documents to a collection

### Insert a restaurant

``` json
db.restaurants.insert(
   {
      name: "Cookies Corner",
      "address" : {
         "street" : "1970 2nd St NW",
         "zipcode" : 20001,
      },
      yelp: "http://www.yelp.com/biz/cookies-corner-washington",
});
```

> The db is the database we’re connected to. In this case, `restaurant_db`.
`.restaurants` is then referring to a collection in our `restaurant_db`. We
use the `.insert()` to add the document inside the parentheses.

### Verify the insert
```
> show collections
restaurants
system.indexes
```
Note: restaurants

```
> db.restaurants.find()
```

- name
- address
- yelp

What is surprising/unexpected?

- where did restaurants come from?
- `_id`?
- [ObjectId](https://docs.mongodb.org/manual/reference/object-id/)

## Review `insert`
```
// insert
db.your_collection_name.insert({ data as json })
// find
db.your_collection_name.find()
```

New Record:
- If the document passed to the insert() method does not contain the _id field
, the mongo shell automatically adds the field to the document and sets the
field’s value to a generated ObjectId.

New collection:
- If you attempt to add documents to a collection that does not exist,
MongoDB will create the collection for you.

## Dropping a Database

```
use milk-n-cookies
db.dropDatabase()
```

Drops the **current** database.

### Exercise (5 minutes): Add a few more restaurants.

ProTip: I recommend you construct your statements in your editor and copy/
paste.  It will help you now & later.

> Prompt: Did anyone insert multiple at one time?

Let's recreate the steps together:
Where are we now?
```
db
```

1. Create DB
2. Use the appropriate DB
3. Insert multiple restaurants

``` mongo
db.restaurants.remove({});
db.restaurants.insert([
  {
      name: "Cookies Corner",
      "address" : {
         "street" : "1970 2nd St NW",
         "zipcode" : 20001,
      },
      yelp: "http://www.yelp.com/biz/cookies-corner-washington"
  },
  { name: "The Blind Dog Cafe", address: { street: "944 Florida Ave",
        zipcode: 20001 }, yelp: "http://www.yelp.com/biz/the-blind-dog-cafe-washington-2?osq=cookies" },
  {name: "Birch & Barley", address: { street: "1337 14th St NW", zipcode: 20005}, yelp: "http://www.yelp.com/biz/birch-and-barley-washington?osq=Restaurants+cookies"},
  {name: "Captain Cookie and the Milk Man", address: { street: "Dupont Circle", zipcode: 20036 }, yelp: "http://www.yelp.com/biz/captain-cookie-and-the-milk-man-washington-5" },
  {name: "J’s Cookies", address: { street: "1700 N Moore St", zipcode: 22209}, yelp: "http://www.yelp.com/biz/js-cookies-arlington" }
])
db.restaurants.count()
```

## [Primary key](http://docs.mongodb.org/manual/reference/glossary/#term-primary-key)

- A record’s unique immutable identifier.
- RDBMS: usually *id* field, typically an *Integer*
- MongoDB: the *_id* field, usually a *[BSON](http://docs.mongodb.org/manual/reference/glossary/#term-bson) [ObjectId](http://docs.mongodb.org/manual/reference/glossary/#term-objectid)*.

## CLI: Find records

Find all:
```mongo
db.restaurants.find()
```

### Find by Conditions (like `where`)

Key: Value pairs
```mongo
db.restaurants.find({name: "Cookies Corner"});
db.restaurants.find({"address.zipcode": 20001});
```

## Helpful References
- [Mongo to SQL Mapping Chart](http://docs.mongodb.org/manual/reference/sql-comparison/)
- [CRUD Intro](http://docs.mongodb.org/manual/core/crud-introduction/)
- [CRUD Commands](http://docs.mongodb.org/manual/reference/crud/)
- [bios Collection](http://docs.mongodb.org/manual/reference/bios-example-collection/)


## CLI: Update a record(s)

http://docs.mongodb.org/manual/core/write-operations-introduction/

```
> db.your_collection.update(
  { criteria }, 
  { $set: { assignments }},
  { options }
)
```

### You do (10 min):

Update a restaurants to have a new key-value par `{state: "DC"}`

```
> db.restaurants.update(
  {"name": "Cookies Corner"},
  { $set: { state: "DC" }}
)
```
```
WriteResult({ "nMatched" : 1, "nUpserted" : 0, "nModified" : 1 })
```

> the first key value pair find the document you'd like to update, the second
is what values you'd like to set and third is any additional options

Verify:
```
> db.restaurants.find()
```

## CLI: Remove records

```
db.restaurants.remove({ conditions })
```

## CLI: Add a nested object

> We already did this! (The address 'object' / 'subdocument')

## Key Advantages

- Usability
- High Performance
- High Availability
- Automatic Scaling
- No SQL :)

### Usability

- Documents (i.e. objects) correspond to native data types in many
programming languages.
- Schema-less, less need to manage migrations
- Dynamic schema supports fluent polymorphism.

### High Performance

- Embedded documents and arrays reduce need for expensive joins (reduces I/O).
- Indexes support faster queries and can include keys from embedded documents
and arrays.

### High Availability

MongoDB’s replication facility, called replica sets, provide:

- automatic failover.
- data redundancy.

replica set:
> is a group of MongoDB servers that maintain the same data set, providing
redundancy and increasing data availability.

### Automatic Scaling

- Automatic sharding distributes data across a cluster of machines.
- Replica sets can provide eventually-consistent reads for low-latency high
throughput deployments.

# [No SQL?](https://www.mongodb.com/nosql-explained)

## ST-WG (10min): Which database would you choose for?
- Blog: Posts have_many Comments
- HR app: Companies have_many Managers have_many Employees
- Gallery: Artists have_many Paintings

## Come up with your own examples, for both


## Vote for your best example for:

- RDBMS
- Document Database
