MongoDB Installation
===

## install MongoDB Community Edition in MacOS

* official mongodb INSTALLATION AND INSTRO: https://docs.mongodb.com/manual/tutorial/install-mongodb-on-os-x/
* start to run MongoDB: brew services start mongodb-community@4.4
* stop the MongoDB: brew services stop mongodb-community@4.4
* to run MongoDB manually: mongod --config /usr/local/etc/mongod.conf --fork
* connect and use MongoDB: mongo

Fundamental MongoDB
===

## Relational v.s. Non-relatonal database 
> Relational database: SQL, MySQL, PostgreSQL
> non-relational database: NoSQL(MongoDB, Redis)

* similarities
	1. Database v.s. Database
		
		use databasename v.s. CREATE DATABASE databasename ==> create a new database
		use databasename v.s. use databasename ==> get into the database
	
	2. Collection v.s. Table
		
		db.createCollection("collectionName") v.s. CREATE TABLE tableName(columName varchar(255));

	3. Document v.s. Record
		
		db.collectionName.insert({"columnName1":"data1", "columnName2":"data2"}) v.s. 
		INSERT INTO tableName columName values("data");
		each document will have a default object id from the mongodb and it is unique

* Difference
	
	1. relational DB(SQL) is more structured n unchanging
	2. NoSQL is document-orientation, store with key and value(key-value module)
	3. for the relational DB, the table cannot be revised for unique item; the column name is fixed
	4. for noSQL, we can add the document direct to the collection even there is no column for it, but it would be created.
		
		ex. Collection: Posts
						
						{"_ id":"1","title":"我的第一篇博客"} // ＿和id間沒有空白間
						
						{"_ id":"2","title":"我的第二篇博客"}
						
						{"_ id":"3","title":"我的第三篇博客","delflg":1} 
						
	!! the delflg is a new column for the collection cause there is no in the first and second


## simple command tool explanation

$ mongo ==> enter to mongo enviornment

\>help ==> to know the instruction of the mongo

\>exit ==> exit the mongo enviornment

$mongo ==> enter to mongo enviornment again

\>show dbs; ==> to check if there is any database existed

\>use komablog; 
==> jump to the komablog database,
if there is no this databasethen, a komablog database is created

\> db.createCollection("posts");

\> db.createCollection("categories");

\> db.createCollection("tags");
==> create the collection in the komablog database

\> show collections; ==> check collections

\> show dbs; ==> check how manay database is existed

\> db.stats(); ==> check the all the source state in the database

\> db.dropDatabase();

\> show dbs;

#### create/show/drop Database

\> use komablog; ==> create and use the komablog database

\> show dbs; ==> check all the existed database

\> db.stats(); ==> show the state of all the source in the database

\> db.dropDatabase(); ==> drop/delete the komablog database

#### create/rename/drop Collection

\> db.createCollection("users"); ==> create a collection in the db

\> db.users.renameCollection("staff"); ==> get into users collection and rename it to staff

\> db.staff.drop(); ==> drop/delete the staff collection

\> show collections; ==> check if any collection in the db

#### insert/find/remove/count document

\> db.createCollection("posts"); ==> create the posts collection

\> db.posts.insert({title:"my first blog", content:"it's my first blog"}); 
	==> insert the content into the posts, which is json format

\> db.posts.find(); ==> to check if any data inside the post collection

\> db.posts.insert({title:"my second blog", content:"it's my second blog", tag:["non"]};); 
	==> insert a second data to the collection and add a new column with tag list

\> 	add all the data into a same collection then we can use **for loop in JAVA/kotlin**

 	for(var i=3; i<=10;i++){
		db.posts.insert(
			{title:"my "+i+" blog"}
			);
 	} 
	==> the insert format must be *json* format

\> db.posts.count(); ==> to get the total quantities in the document

\> db.posts.remove(); ==> remove all the document in the collection



#### simple conditional Document
* db.collectionName.find({"columnName":"data"}) 
	==> to find the specific document in the collection ps. must be json format{"":""}
* $gte, $gt, $lte, $lt ==> >=, >, <=, <
* $eq, $ne ==> ==, !=
* Regular Expression: /k/, /^k/
* db.collectionName.distinct("field_name") ==> get the unique document 

##### practicing the confitional operation

\> use komablog

\> db.posts.remove()  ==> remove all the post in the komablog collection

\> db.posts.insert(["title":"trayer","rank":2,"tag":"game"]) ==> insert a new document

\> db.posts.insert(["title":"player","rank":3,"tag":"game"])

\> db.posts.find() ==> find all the documents in the collection

\> db.posts.find({"tag":"game"}) ==> use {} to find the specific data

\> db.posts.find({"rank":{%gte:2}}) ==> example to find the rank score >= 4

\> db.posts.find({"title":/u/})  ==> title content contains "u"

\> db.posts.find({"title": /^t/}) ==> title start with "t"

\> db.posts.distinct("tag")  ==> to find what kind of the name in the tag

#### complicate conditional Document
* db.[collection_name].find({"coloumnName":"dataName", "coloumnName":"dataName"})

		db.posts.find({"title":/u/},{"rank":{$gte:5}})
		--> title with "u" AND rank >= 5

* db.[collection_name].find({$or:[{...},{...}]})
	
		db.posts.find($or:[{"title":/u/},{"rank":{$gte:4}}])
		--> title with "u" OR rank >=4

* db.[collection_name].find({"":{$in:[...]}})
	
		db.posts.find({"title":{$in:[3,4]}})
		--> find the posts that rank in 3 and 4

* db.[collection_name].find({"":{$exists:true}})
	
		db.posts.insert({"title":"hey","istop":true})
		db.posts.find({"title":{$exists:true}})
		--> find the data that with true inside

* db.[collection_name].find({},{field1:true,field2:1}) ==> only get the require data in the field that pointed

		ex. db.posts.find({},{title:true,rank:1}) : get the title and rank only from the document
		    --> output: _id: objectId("..."), "title":"...", rank: "..."

		    db.posts.find({},{title:true,rank:1,_id:0}) : if put 0, then it won't be showed
		    --> output: "title":"...", rank: "..."

#### method of the document

* sort()

		db.posts.find({},{ _ id:0}.sort({rank:1})): get all the data and sorted by the rank asc
		
		db.posts.find({},{ _ id:0}.sort({rank:-1})): get all the data and sorted by the rank des

* limit()

		db.posts.find({},{_ id:0}.limit(3)): get the first 3 data on the document which is default order
		
		db.posts.find({},{ _ id:0}.sort({rank:1})).limit(3): sort the order first and get the first 3 data
		
		db.posts.findOne({},{_ id:0}) : get only first one

* skip()
	
		db.posts.find({},{_ id:0}.skip(3).limit(3)): skip the first 3 data and get the next 3 data

* update(<filter>,<update>,<option>) : 
	"filter" :to find the data that need to be updated
	"update" :the updated data
	"option" :to set if there is any other condition
		
		db.posts.update({"title":"trayer",{$set:{"rank":10}}})
		==> get the data title is trayer and use "$set" to update the specific area
		
		db.posts.update({"title":"trayer","rank":99})
		==> if we don't use $set to update, then the data for "trayer" is covered by rank:99 
		
		db.posts.update({"tag":"it",{$set:{"rank":50}}})
		==> only update the first data with the rank 50

		db.posts.update({"tag":"it",{$set:{"rank":50}},{multi:true}})
		==> with multi: true to update all the data with tag is it
	!! $set is important to protect that data won't be rewritten

#### special function

* $inc: increase

		db.posts.update({title:"trayer"},{$inc:{rank:10}})
		==> original rank: 1
			updated rank: 11 (1+10)


* $mul: multify ( * )
		
		db.posts.update({title:"trayer"},{$mul:{rank:2}})
		==> original rank: 11
			updated rank: 22 (11 * 2)


* $rename: change the name
	
		db.posts.update({title:"trayer"},{$rename:{"rank":"score"}})
		==> updated: title: trayer, score: 22

* $set: add or revise
	
		db.posts.update({title:"trayer"},{$set:{"istop":true}})
		==> updated: title: trayer, score: 22, istop: true

* $unset: delete
	
		db.posts.update({title:"trayer"},{$unset:{"istop":true}})
		==>updated: title: trayer, score: 22

* upsert: update+insert ==> if there is the record then update, if no, then add
	
		db.posts.update({"title":"player2","rank":4,"tag":"game"},{upsert:true})
		==> insert the post into the document
	
		db.posts.update({"title":"player2"},{"title":"player2","rank":5,"tag":"game"},{upsert:true})
		==> update the rank to 5

* remove(): delete the specific data

#### get Index

* getIndexes() :get all the current index
	
		db.posts.getIndexes()
		==> get all the current index

* createIndex({...},{...}): create the index of the document
	
		db.posts.createIndex({rank:-1})
		==> create a index base on rank dec
	
		db.posts.createIndex({title:1},{unique:true})
		==> unique is true, which mean there will be no duplicated data

* dropIndex({...}): delete the index
	
		db.posts.dropIndex({rank:-1})
		==> delete the index key

#### backup & restore

* mongodump: backup
	
		mkdir backupDB ==> create a document of the backup db
		cd backupDB ==> got to the backupDB
		mongodump -d komablog ==> copy all the document in the komablog collection

* mongorestore
	!! if delete the collection accidentally, then mongostore can restore it
	
		db.posts.remove({}) ==> delete the posts document
		mongorestore --drop ==> restore the posts
		mongo komablog ==> get into the document and check again
		db.posts.find({}.{_ id:0}) 


** note from: https://www.youtube.com/watch?v=uJppiHeKRrI&list=PLliocbKHJNwvYvA3paPKUg86qLKrwuNsd&index=15
