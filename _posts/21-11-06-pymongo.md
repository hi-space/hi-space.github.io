---
title: "[MongoDB] pymongo"
tags: python db
---

<!--more-->

# Install 

#### 1. [MongoDB Setup](/2021/11/06/ubuntu-mongodb.html)

#### 2. Python Package

```sh
pip install pymongo
```

# Samples

## Connect

```py
from pymongo import MongoClient

client = MongoClient('mongodb://localhost:27017/')
print(client.list_database_names())

# If it doesn't exsit, it is created automatically
test_db = client.test
collection = test_db.test_collection
```

## Create

```py
# create a item
collection.insert_one({'name': 'hi', 'desc': 'space'})

# create items
collection.insert_many([
    { 'name': '1', 'desc': 'one' },
    { 'name': '2', 'desc': 'two' },
])
```

## Read

```py
# get a item
item = collection.find_one()

# get all items
collection.find()

# get all items with sort by name desc
collection.find().sort('name', -1)

# only show that count
collection.find().limit(2)

# skip first data
collection.find().skip(1)

# and
collection.find({ 'name': 'hi', 'desc': 'space' })

# or
collection.find({ '$or': [ {'name': 'hi'}, {'desc': 'two'} ] })

# compare (gt, gte, lt, lte)
collection.find({ 'name': { '$gt': '1', '$lte': '2' } })
```

## Update

```py
# override (other values disappear)
collection.update( {'name': '2'}, {'name': '3'} )

# find a top column and change only specific data
collection.update( {'name': '1'}, { '$set': { 'desc': 'updated one' } } )

# find all columns and change only specific data
collection.updateMany( {'name': 'hi'}, { '$set': { 'desc': 'updated space' } } )

# upsert (update + insert)
# find columns and update it, if it doesn't exist, create it ()
collection.update( {'name': '1'}, { '$set': { 'desc': 'updated one' } },
                        upsert=True,
                        multi=True )
```

## Delete

```py
# find a top column and remove it
collection.delete_one( {'name': 'hi'} )

# find all columns and remove it
collection.delete_many( {'name': 'hi'} )
collection.remove( {'name': 'hi'} )

# remove all data
collection.remove({})
```

# References

- [PyMongo Documentation](https://pymongo.readthedocs.io/en/stable/)
