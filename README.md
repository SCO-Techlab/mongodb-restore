# @sco-techlab/mongodb-restore

Simple pure js module to restore MongoDB dumps from BSON files. No extra tools required (i.e. mongo-tools).


## Installation

    npm install --save @sco-techlab/mongodb-restore


## Resources

* [Changelog](https://github.com/sco-techlab/mongodb-restore/blob/2.x/CHANGELOG.md)



## Usage

```javascript
const restore = require('@sco-techlab/mongodb-restore'),
      uri = 'mongodb://127.0.0.1:27001/';

// restore multi-database dump
await restore.dump({
    uri,
    from: '../dumps/mydump/'
});

// restore single database
await restore.database({
    uri,
    database: 'my-database-name',
    from: '../dumps/mydump/db-1/'
});

// restore single collection
await restore.collection({
    uri,
    database: 'my-database-name',
    collection: 'foo',
    from: '../dumps/mydump/db-1/foo.bson'
});

// restore from a buffer containing bson
var myBuffer = fs.readFileSync('./foo.bson')
await restore.buffer({
    uri,
    database: 'my-database-name',
    collection: 'foo',
    from: myBuffer
});
```

## Parameters
```javascript
await restore.dump({
    con: await MongoClient.connect(uri),
                  // An existing mongodb connection to use.
                  // Either this or "uri" must be provided
    uri: 'mongodb://127.0.0.1:27001/',
                  // URI to the Server to use.
                  // Either this or "con" must be provided.
    from: '../dumps/my-dump',
                  // path to the server dump, contains sub-directories
                  // that themselves contain individual database
                  // dumps
    clean: true,   // wether the collections should be cleaned before
                  // inserting the documents from the dump files
                  // optional; default = true
    onCollectionExists: 'throw',
                  // how to handle collections that already exist
                  // on the server; when set to "throw" (default)
                  // the restore will throw an error;
                  // when set to "overwrite" the restore will proceed
                  // and insert the dump data to the collection
                  // (and remove existing items when clean == true)
                  // optional, default: 'throw'
                  // available options:
                  //    'throw'
                  //    'overwrite'
    transformDocuments: (doc, { collection, database }) => {
        var transformed = doStuff(doc);
        return transformed;
    },            // transform the documents while restoring them
});

await retore.database({
    // same as in dump()
    con, uri, clean, onCollectionExists, transformDocuments
    //
    
    database: 'my-database-name',
                  // name of the database that will be created
                  // on the mongodb server from the dump
    from: '../dumps/mydump/db-1/',
                  // path to the database dump directory
                  // should contain one or more bson files
                  // to restore
});

await restore.collection({
    // same as in database()
    con,
    uri,
    clean,
    onCollectionExists,
    transformDocuments,
    database,
    //
    
    collection: 'my-collection-name',
                  // name of the collection that the documents
                  // will be restored into
    from: '../dumps/mydump/db-1/foo.bson',
                  // bson file containing the documents
                  // to be restored
    limit: 100,   // optionally you can choose to not
                  // restore all the documents but just
                  // the first e.g. 100
                  // optional; default = no limit
});

await restore.buffer({
    // same as in collection()
    con,
    uri,
    clean,
    onCollectionExists,
    transformDocuments,
    database,
    collection,
    limit
    //
    
    from: myBuffer,
                  // a buffer with bson documents that was
                  // created somehere in advance
                  // e.g. by fs.readFileSync()
});
```


## LICENSE

[MIT](LICENSE)
