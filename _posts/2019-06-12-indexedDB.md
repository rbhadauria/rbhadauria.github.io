---
title: IndexedDB ---What? When? How?
description: Brief intro of indexedDB browser API
categories:
 - knowledge
tags:
 - indexedDB
 - Browser API
---

# IndexedDB
IndexedDB is client-side storage solution provided by browser to store structured data ( including files/blobs ). It is a transactional database similar to MySql but it stores JSON objects. it uses indexes to allow fast searches in data.
Other notable storage options available in browser include localstorage, session storage, cookies etc. 

IndexedDB lets you store and retrieve objects that are indexed with a key. Database schema needs to be defined. we open a connection to the database and perform series of transaction on the db to store/update the data.

Now that we know a little bit about indexedDB lets see how to use it in our codebase.

# Usage
 refer [this link](https://developer.mozilla.org/en-US/docs/Web/API/IndexedDB_API/Using_IndexedDB) on how to use indexedDB in your project.
 
## Performing a wildcard search in indexDB
  ```sh
  var result = [];
db.transaction(['table'], 'readonly')
  .objectStore('table')
  .openCursor(
    IDBKeyRange.bound(searchTerm, searchTerm + '\uffff'), // The important part, thank Velmont to point out
    'prev')
  .onsuccess = function (e) {
    e || (e = event);
    var cursor = e.target.result;
    if (cursor) {
      result.push([cursor.value.column1, cursor.value.sortcolumn]);
      cursor.continue();
    } else {
      if (result.length) {
        result.sort(function (a, b) {
          return a[1] - b[2];
        });
      }

      // Process code here
    }
  };
  ```
  [stackoverflow link](https://stackoverflow.com/questions/7086180/indexeddb-fuzzy-search)
  
## Performing queries on multiple conditions 
  ```sh
  // In onupgradeneeded
var store = db.createObjectStore('mystore');
store.createIndex('myindex', ['prop1','prop2'], {unique:false});

// In your query section
var transaction = db.transaction('mystore','readonly');
var store = transaction.objectStore('mystore');
var index = store.index('myindex');
// Select only those records where prop1=value1 and prop2=value2
var request = index.openCursor(IDBKeyRange.only([value1, value2]));
// Select the first matching record
var request = index.get(IDBKeyRange.only([value1, value2]));
```
[stackoverflow link](https://stackoverflow.com/questions/6405650/query-using-multiple-conditions)
