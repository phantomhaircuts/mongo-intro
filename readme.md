# MongoDB

![Mongo-logo](http://www.screen-i.com/blog/wp-content/uploads/2015/07/mongodb-standard-logo-565.png)

## Learning Objectives

- Compare and contrast relational to document based (NoSql) databases
- Setup local MongoDB server
- Define what a document is in the context of MongoDB
- CRUD documents using Mongo CLI
- Build a simple node CLI to query MongoDB

## Framing

Well, we've come full circle ... again. What we're learning today isn't
fundamentally that different from what we know. We're going to learn a
different way to store information. This time, via a document-based, non-relational way.

We've learned a considerable amount of information about relational databases with Postgres. We've seen that schema's in SQL(relational DB's) are fairly rigid. Adding columns can be taxing(migrations). Also if we delete a row in a table that is being used as a foreign key in another table, we must delete all the rows associated with that foreign key before we can delete the parent object. With SQL, we can also join on foreign keys for relational tables in order to query our database. One of the negatives about these join queries, is that they can get really expensive and therefore slow down our app.

**When dealing with less complex associations, non relational databases can be more
effective**.  Mongo provides a more flexible, scalable solution for less complex
domain models.

> This is not to say that Mongo is a better solution than Postgres or other
SQL libraries, but an alternative solution.

MongoDB is an open-source **document database** that provides:
- High Performance
- High Availability
- Automatic Scaling

In a nutshell, you should use Mongo if you are working with a flexible data model, that involves similar, but different objects.

## [Document Database](https://docs.mongodb.com/manual/introduction/)?

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

[Documentation](https://www.mongodb.org)

## Documents

### A record in MongoDB is a document
![mongo-document](https://docs.mongodb.com/manual/_images/crud-annotated-document.png)

- a data structure composed of field(key) and value pairs.
  - similar to JSON objects.
  - stored as BJSON
- fields may include other documents, arrays, and arrays of documents.
- analogous to rows in a table

## Collections

MongoDB stores documents in collections.

![mongo-collections](https://docs.mongodb.com/manual/_images/crud-annotated-collection.png)

- Collections are analogous to tables in relational databases.
- does **NOT** require its documents to have the same schema.
- documents stored in a collection must have a unique `_id` field that acts
as a primary key.

###  MongoDB & NoSQL vs. SQL and Relational Databases

    A SQL  ___________            ...is like...           A Mongo  ____________
![mongo-sql-compared](http://4.bp.blogspot.com/-edz2_QrFvCE/UnzBhKZE3FI/AAAAAAAAAEs/bTEsqnZFTXw/s1600/SQL-MongoDB+Correspondence.PNG)

---

Great, now that we have a high level understanding of what Mongo is and what purpose it serves, let's look at how to use it!

## Installation
- [Mac OS X](https://docs.mongodb.org/getting-started/shell/tutorial/install-mongodb-on-os-x/)
- [Linux](https://docs.mongodb.org/manual/tutorial/install-mongodb-on-ubuntu/)

### Start mongo:

```
$ mongod
```

You should see:

`a bunch of output but with the prompt hanging`
> This is good news, `mongod` just starts up a mongo server locally. **NOTE**: you need this running in order to use the mongo cli

### More info?

```
$ brew info mongo
```

## Mongo shell

### Start the shell

```
$ mongo
```

> feels a little bit like a JS REPL

You should see:

```
MongoDB shell version: 3.x.x
connecting to: test
>
```

### Help

```
> help
```

#### ThinkShare (2min):
Based on what you see in the help menu:
- What jumps out as important?
- What might be useful for debugging?

---

### What jumped out to me:
- `show dbs`: show database names
- `show collections`:  show collections in current database
- `use <db_name>`: set current database
- `db.foo.find()`: list objects in collection foo

Also:

- `<tab>` key completion
- `<up-arrow>` and the `<down-arrow>` for history.

### CLI: Creating a Database

In the Mongo REPL, let's go ahead and create our first database, one which we will be using to store information about restaurants.

In order to create/connect to a new database, we have to tell mongo to `use` a specific database that we want to work with:

```
> use restaurant_db
```
> **Note**: `use` will create the database it received as an argument if not already initialized and connect to it

Verify:
```
> db
restaurant_db
```
> **Note**: the `db` variable is provided by mongo and will point to the currently connected database

Common Gotcha, what happens when we run:

```
$ show dbs
```

> **Note** we don't see `restaurant_db` listed. It isn't until we add a document to
our database that our  db will show up in `show dbs`

## CLI: Create a record

### Insert

- use `insert()` to add documents to a collection

### Insert a restaurant

``` json
> db.restaurants.insert(
   {
      "name": "Cookies Corner",
      "address" : {
         "street" : "1970 2nd St NW",
         "zipcode" : 20001,
      },
      "yelp": "http://www.yelp.com/biz/cookies-corner-washington"
   })
```

**Important to note**:
> The `db` is the database we’re connected to. In this case, `restaurant_db`.
`.restaurants` is then referring to a collection in our `restaurant_db`. We
use the `.insert()` to add the document inside the parentheses.

### Verify the insert
```bash
> show collections
restaurants
```

Note: `restaurants` was saved as a collection

```js
> db.restaurants.find()
```

Returns documents with the following fields:
- `name`
- `address`
- `yelp`

**Q**. What is surprising/unexpected?

- where did `restaurants` come from?
- `_id`?
- [ObjectId](https://docs.mongodb.org/manual/reference/object-id/)

## Review `insert`
```js
// insert
> db.your_collection_name.insert({ data as json })
// find
> db.your_collection_name.find()
```

New Record:
- If the document passed to the `insert()` method does not contain the `_id` field the mongo shell automatically adds the field to the document and sets the field’s value to a generated `ObjectId`.

New collection:
- If you attempt to add documents to a collection that does not exist,
MongoDB will create the collection for you.

## Dropping a Database

```bash
> use random_db
> db.dropDatabase()
```

Drops the **current** database.

### Exercise (5 minutes): Add a few more restaurants.

Using the Mongo Shell CLI, add at least 4 new restaurant documents to your `restaurants` collection.

**ProTip**: I recommend you construct your statements in your editor and copy /
paste. It will help you now & later.

---

## Break

---

> Prompt: Did anyone insert multiple at one time?

Let's recreate the steps together:

**Q**. How can we tell which database we are connected to currently?
> `db`

1. Create DB
2. Use the appropriate DB
3. Insert multiple restaurants

``` json
db.restaurants.remove({});
db.restaurants.insert([
  {
    "name": "Cookies Corner",
    "address": {
      "street" : "1970 2nd St NW",
      "zipcode" : 20001
    },
    "yelp": "http://www.yelp.com/biz/cookies-corner-washington"
  },
  {
    "name": "The Blind Dog Cafe",
    "address": {
      "street": "944 Florida Ave",
      "zipcode": 20001
    },
    "yelp": "http://www.yelp.com/biz/the-blind-dog-cafe-washington-2?osq=cookies"
  },
  {
    "name": "Birch & Barley",
    "address": {
      "street": "1337 14th St NW",
      "zipcode": 20005
    },
    "yelp": "http://www.yelp.com/biz/birch-and-barley-washington?osq=Restaurants+cookies"
  },
  {
    "name": "Captain Cookie and the Milk Man",
    "address": {
      "street": "Dupont Circle",
      "zipcode": 20036
    },
    "yelp": "http://www.yelp.com/biz/captain-cookie-and-the-milk-man-washington-5"
  },
  {
    "name": "J's Cookies",
    "address": {
      "street": "1700 N Moore St",
      "zipcode": 22209
    },
    "yelp": "http://www.yelp.com/biz/js-cookies-arlington" }
])

> db.restaurants.count()
```

## [Primary key](http://docs.mongodb.org/manual/reference/glossary/#term-primary-key)

- A record’s unique immutable identifier.
- In relational databases: usually *id* field, typically an *Integer*
- In MongoDB: the *_id* field, usually a *[BSON](http://docs.mongodb.org/manual/reference/glossary/#term-bson) [ObjectId](http://docs.mongodb.org/manual/reference/glossary/#term-objectid)*.

## CLI: QUERY for Records

Breaking down the anatomy of a typical query with Mongo:

   collection + operation + modification = results

![mongo-queries](https://docs.mongodb.com/manual/_images/crud-query-stages.png)

In order to Find all restaurants:
```js
> db.restaurants.find()
```

> **Note**: we can format our output to be a little nicer on the eyes by chaining the `.pretty()` method to end of our query like so: `db.restaurants.find().pretty()`

### Find by Conditions (like SQL's `where`)

We can add conditions to our query to target documents based on matching key-value pairs:

```js
> db.restaurants.find({name: "Cookies Corner"});
> db.restaurants.find({"address.zipcode": 20001});
```

### CLI: Update a record(s)

http://docs.mongodb.org/manual/core/write-operations-introduction/

```
> db.your_collection.update(
  { criteria },
  {
    $set: { assignments }
  },
  { options }
)
```

> **Note**: the first key value pair is the condition on which to find the document you'd like to update, the second
is what values you'd like to set, and third is any additional options

### You do (10 min):

Take time to think about and execute the appropriate commands so that you:

- Update all restaurants to have a new key-value pair `{state: "DC"}`
- Add a property of `rating` to at least 2 documents and give it a numerical value between 1-5
- Change the street `address` of a specific restaurant

**Bonus**
- Add nested sub-documents to each restaurant to that it has many `reviews`
- Store important information about each `review`
---

> **Note** this what a sample update might look like:

```js
> db.restaurants.update(
  {"name": "Cookies Corner"},
  { $set: { state: "DC" }}
)
```

> **Note**: In order to update multiple documents at a time, make sure to pass the `multi` option as true, like so:

```js
db.restaurants.update(
  {},
  {
    $set: { "state": "DC" }
  },
  {multi: true}
)
```

Verify:

```js
> db.restaurants.find().pretty()
```

### CLI: Remove records

```
> db.restaurants.remove({ conditions })
```

### CLI: Add a nested object

> We already did this! (The address 'object' / 'subdocument')

## Closing: Review Mongo's Key Advantages

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

> Interested in learning more about [No SQL?](https://www.mongodb.com/nosql-explained)

### TPS (5mins): Which database would you choose for?

 What database type would make sense for the following apps:
- Blog: Posts have_many Comments
- HR app: Companies have_many Managers have_many Employees
- Gallery: Artists have_many Paintings

---

## Break

---

## Bonus Exercise

### (We Do) Build a CLI Mongo App w/ Node

Great, now that we have had some good practice with Mongo, let's connect to the
`restaurant_db` we just setup via the `mongodb` node module and build a little
CLI app that will allow a user to enter commands to query our database.

Let's start by creating our app directory and main `js` file.
Then we will install the `mongodb` node module

```bash
$ mkdir restaurants_cli
$ cd restaurants_cli
$ touch app.js
$ npm init
$ npm install --save mongodb
```

Great, now let's use an instance of the MongoClient to connect to our `restaurant_db` and search for all restaurants

```js
// app.js

var mongo = require("mongodb").MongoClient
var url = "mongodb://localhost:27017/restaurant_db"

mongo.connect(url, function(err, db){
  var collection = db.collection('restaurants');
  collection.find().toArray(function(err, docs){
    console.log(docs)
  })
})
```

If you see all the documents outputed to your server logs, you're in great shape!
> If not, double check your connection url and that your database is in fact there and populated!

Now, let's add in the functionality to prompt the user for some input.

To do that, we are going to use a node module that will allow us to use the `prompt`
method, much like how we used it w/ front-end JS in the browser.

```
$ npm install --save prompt-sync
```

Now let's create a little menu prompt that will return all documents in our collection
if the user enters in the right input:

```js
// app.js

var mongo = require("mongodb").MongoClient
var url = "mongodb://localhost:27017/test"

mongo.connect(url, function(err, db){
  var collection = db.collection('restaurants');
  var number = prompt("Type 1 and press enter to display all restaurants' names: ")
  if(number == "1"){
    collection.find().toArray(function(err, doc){
      console.log(doc)
    })
  }
})
```

## You do:

Add another prompt to let the user view more information about a restaurant.

### Bonus!

Allow users to add their own restaurants

### Double Bonus!

Allow users to edit/delete restaurants

## Helpful References

- [Mongo to SQL Mapping Chart](http://docs.mongodb.org/manual/reference/sql-comparison/)
- [CRUD Intro](http://docs.mongodb.org/manual/core/crud-introduction/)
- [CRUD Commands](http://docs.mongodb.org/manual/reference/crud/)
- [bios Collection](http://docs.mongodb.org/manual/reference/bios-example-collection/)
