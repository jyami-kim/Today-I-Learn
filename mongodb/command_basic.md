# commands basic



### MongoDB 서버 접속

```shell
$ mongo
>
```

mongo 커맨드를 입력하면 mongoDB shell 창이 나타난다.



### Database

1. Database 생성

   ```shell
   > use myDatabase
   switched to db myDatabase
   ```

2. 현재 사용중인 Database 확인하기

   ```shell
   > db
   myDatabase
   ```

3. 내가 만든 데이터베이스 리스트를 확인한다.

   ```shell
   > show dbs
   local  0.000GB
   ```

   > 리스트에서 방금 만든 DB를 보려면 최소 한개의 Document를 생성해야한다.

4. 데이터베이스 삭제

   ```shell
   > use myDatabase
   switched to db myDatabase
   > db.dropDatabase();
   { "dropped" : "myDatabase", "ok" : 1 }
   ```

   Database를 제거할 땐 ```db.dropDatabase()``` 를 사용한다. 명령어 사용전에 use를 사용해서 drop 하려는 db를 선택한다.



### Collection

1. collection 생성하기

   ```shell
   > use myDatabase
   switched to db myDatabase
   > db.createCollection("books")
   { "ok" : 1 }
   ```

   collection을 생성하려는 db를 선택하고, ```db.createCollection(NAME, [Option])```명령어를 사용해서 Collections을 생성한다. Option은 선택적인 변수이다.

2. Document 생성과 함께 Collection 생성하기

   ```shell
   > db.book.insert({"name": "MongoDB Tutorial", "author": "velopert"});
   WriteResult({ "nInserted" : 1 })
   > show dbs
   local             0.000GB
   myDatabase		  0.000GB
   ```

   book 이라는 collection을 생성하고, 이 안에 document를 insert 한다.

3. 생성한 collections 조회하기

   ```shell
   > show collections
   articles
   books
   people
   ```

4. Collection 삭제하기

   ```shell
   > use myDatabase
   switched to db test
   > show collections
   articles
   books
   people
   > db.people.drop()
   true
   > show collections
   articles
   books
   ```

   Collection을 제거할 땐 drop() 메소드를 사용한다.



### Document

1. Document 추가

   ```shell
   > db.books.insert({"name": "NodeJS Guide", "author": "Velopert"})
   WriteResult({ "nInserted" : 1 })
   ```

   ```shell
   > db.books.insert([
   ... {"name": "Book1", "author": "Velopert"},
   ... {"name": "Book2", "author": "Velopert"}
   ... ]);
   BulkWriteResult({
           "writeErrors" : [ ],
           "writeConcernErrors" : [ ],
           "nInserted" : 2,
           "nUpserted" : 0,
           "nMatched" : 0,
           "nModified" : 0,
           "nRemoved" : 0,
           "upserted" : [ ]
   })
   ```

   ```db.COLLECTION_NAME.insert(document)``` : insert 메소드를 사용해서 추가할 수 있다. 여러개를 한번에 추가할 수도 있다.

2. Collection의 Document 리스트 확인

   ```
   > db.books.find()
   { "_id" : ObjectId("56c08f3a4d6b67aafdeb88a3"), "name" : "MongoDB Guide", "author" : "Velopert" }
   { "_id" : ObjectId("56c08f474d6b67aafdeb88a4"), "name" : "NodeJS Guide", "author" : "Velopert" }
   { "_id" : ObjectId("56c0903d4d6b67aafdeb88a5"), "name" : "Book1", "author" : "Velopert" }
   { "_id" : ObjectId("56c0903d4d6b67aafdeb88a6"), "name" : "Book2", "author" : "Velopert" }
   ```

    ```db.COLLECTION_NAME.find()``` : 리스트를 확인 할 수 있다.

3. Document 제거

   ```shell
   > db.books.find({"name": "Book1"})
   { "_id" : ObjectId("56c097f94d6b67aafdeb88ac"), "name" : "Book1", "author" : "Velopert" }
   > db.books.remove({"name": "Book1"})
   WriteResult({ "nRemoved" : 1 })
   ```

   ```db.COLLECTION_NAME.remove()``` : document를 제거가능 하다

