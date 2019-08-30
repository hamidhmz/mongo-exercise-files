mongo shell is based on javascript 
1.mongo // to access to mongo shell
2.show databases // for show all dbs except that doesn't have any collection and data
3.use <db name> // for connect to existing or doesn't existing db . if that doesn't exist that will be create.(e.g:use shop)
4.db.<collection name>.insertOne({<your key without or with quotation mark>:"<your value>"(,<<your key without or with quotation mark>:"<your value>">(,<<your key without or with quotation mark>:"<your value>">(,<<your key without or with quotation mark>:"<your value>">)))}) // this is for insert document to collection if your collection doesn't exist mongo will create it (e.g: db.products.insertOne({name:"shampoo"})  or  db.products.insertOne({"name":"shampoo",price:12.99})  )//mongodb always generate for you an id as _id this is a uniq id but if you want to you can change that field and write your id but be careful if you write a duplicate id mongodb would return to you an error 
5.db.<collection name>.find()//if you don't insert any thing to find this will give you all document that would find (e.g:db.products.find() ) // this command would not gave you all document it just gave you a cursor but you can add .toArray to it till that would gave you all documents in an array // 
db.<collection name>.find().toArray() // db.<collectionName>.find().forEach() this command allow you do query like code //e.g : db.passengers.find().forEach((a)=>{printjson(a)}) //printjson is a function in mongodb // db.passengers.find().forEach((a)=>{printjson(a.age)})  

Projection:
in find method you can do Projection by using find actually in Projection you says which key and value you wanna gets
db.<collectionName>.find({},{name:1}) // this command would gave us all documents with _id and name 
if you wanna just see their  names you must type this command :db.<collectionName>.find({},{name:1,_id:0})

#important : if you wanna access to nested documents you show that with . to access child but you must put all field into quotation marks e.g: db.<collection name>.find({"status.description":"10 miles away"})

6.<any thing>.pretty() // this will give you this feature that can you easily to read and pretty version
7.cls // if you want to clear your mongo bash this command will help you to do that  

create:
insertOne(data,options)
insertMany(data,options)
insert() //this is for old approach and this is not a good approach this method work with either to previous method but with different result 

Options:
1.ordered:in insertMany for default ordered is true and it's means if some of the insert document got an error the rest of documents must be failed even if they are not have error with them just those are before document with error but when you changed to false all would be inserted except those have error // e.g: db.<collectionName>.insertMany([{_id:"yoga",name:"Yoga"},{_id:"gym",name:"Gym"},{_id:"cooking",name:"Cooking"}],{ordered:false})
2.writeConcern:this option actually give a control on your response               
db.<collectionName>.insertOne({name:"chrissy",age:41},{writeConcern:{w:0}}) : in this example this will return to you acknowledged:false even though it actually inserted this document but this would be not wait for response this is very fast but it might be some of data got lost 
db.<collectionName>.insertOne({name:"chrissy",age:41},{writeConcern:{w:0}}) : this is default behavior 
db.<collectionName>.insertOne({name:"chrissy",age:41},{writeConcern:{w:1,j:true}}) : this will be waiting for journal so it would be little bit slower than before 
db.<collectionName>.insertOne({name:"chrissy",age:41},{writeConcern:{w:1,j:false}}) : this is default behavior  
db.<collectionName>.insertOne({name:"chrissy",age:41},{writeConcern:{w:1,j:false,wtimeout:200}}) : this option can give the operation a timeOut that allow to wait for response
![write concern option](./writeConcern.PNG "write concern option")

3.Atomicity: when we insert a document or multiple docs mongo db will ensure that whole of one doc will be insert or none of element in doc will insert 
this ensure is just for doc level
![Atomicity](./Atomicity.PNG "Atomicity")


read:
find(filter,options)
findOne(filter,options)
db.<collectionName>.<method(like find)>(<filter({field:value})>)
db.<collectionName>.<method(like find)>(<filter({field:{operator:value}})>)

search in embedded documents inside an object field would be like this : "rating.total.average" e.g: 
{
  "_id":ObjectId("51451515151644484847"),
  "rating":{
    "total":{
      "average":50
    }
  }
}
db.<collectionName>.find({"rating.total.average": 50})

search in array that some value contain in array it would be same as regular element e.g:
db.<collectionName>.find({"genre": "Drama"})
{
  "_id":ObjectId("51451515151644484847"),
  "genre":[
    "Drama",
    "Action",
    "Anime",
    "Horror"
  ]
}
but when we wanna an array contain with one element we would treat like this e.g:
db.<collectionName>.find({"genre": ["Drama","Action"]})
{
  "_id":ObjectId("51451515151644484847"),
  "genre":[
    "Drama",
    "Action"
  ]
}
but this would not return array with opp order like this

{
  "_id":ObjectId("51451515151644484847"),
  "genre":[
    "Action",
    "Drama"
  ]
}
if we wanna return with don't care about order we have to use find like this 
db.<collectionName>.find({"genre": {$all: ["Drama","Action"]} })

if we wanna search in array that contain object or something els :
db.<collectionName>.find({"genre": {$elemMatch: {title:"Sports",frequency:{$gte:3}}} })
this would return something like this 
{
  "_id":ObjectId("484623135646844631554648"),
  "name":"Max",
  "hobbies":[
    {
      "title":"Sports",
      "frequency":3
    },
    {
      "title":"Cooking",
      "frequency":6
    }
  ],
  "phone":094152044884
}



Operators:

![Operators](./Operators.PNG "Operators")
![OperatorsQueryAndProjection](./OperatorsQueryAndProjection.PNG "OperatorsQueryAndProjection")

comparison Operators:

* $eq	Matches values that are equal to a specified value.
* $gt	Matches values that are greater than a specified value.
* $gte	Matches values that are greater than or equal to a specified value.
* $in	Matches any of the values specified in an array.e.g: db.<collectionName>.find({"runtime": {$in: {[35,42]}}})
* $nin	Matches none of the values specified in an array.e.g: db.<collectionName>.find({"runtime": {$nin: {[35,42]}}})
* $lt	Matches values that are less than a specified value.
* $lte	Matches values that are less than or equal to a specified value.
* $ne	Matches all values that are not equal to a specified value. 

logical operators:

* $and Matches all any of these condition inside array but with or law actually it should have several condition at same time.e.g: db.<collectionName>.find({$and:[{"runtime": {$nin: {[35,42]}}},{"runtime":35}]) --------- we have an alternative for this operator we can use this without and operator like this : db.<collectionName>.find({"runtime": {$nin: {[35,42]}},{"runtime":35}})
* $not when we add not to any operators it would return opp of that result e.g: db.<collectionName>.find({"runtime": {"$not":{"$nin": {[35,42]}}}})
* $nor (opp of $or )not Matches all any of these condition inside array but with or low.e.g: db.<collectionName>.find({$or:[{"runtime": {$nin: {[35,42]}}},{"runtime":37}])
* $or Matches all any of these condition inside array but with or law.e.g: db.<collectionName>.find({$or:[{"runtime": {$nin: {[35,42]}}},{"runtime":37}])

element operators:

* exists will give you all doc that contain that specific element e.g: db.getCollection("imi_sub_3080_316").find({"test":{"$exists":true}})
* type will give you all doc that contain that specific element type e.g: db.getCollection("imi_sub_3080_316").find({"phone":{"$type":"number"}}) we can also specify multiple type like this db.getCollection("imi_sub_3080_316").find({"phone":{"$type":["double","number"]}})
  
Double	1	“double”	 
String	2	“string”	 
Object	3	“object”	 
Array	4	“array”	 
Binary data	5	“binData”	 
Undefined	6	“undefined”	Deprecated.
ObjectId	7	“objectId”	 
Boolean	8	“bool”	 
Date	9	“date”	 
Null	10	“null”	 
Regular Expression	11	“regex”	 
DBPointer	12	“dbPointer”	Deprecated.
JavaScript	13	“javascript”	 
Symbol	14	“symbol”	Deprecated.
JavaScript (with scope)	15	“javascriptWithScope”	 
32-bit integer	16	“int”	 
Timestamp	17	“timestamp”	 
64-bit integer	18	“long”	 
Decimal128	19	“decimal”	New in version 3.4.
Min key	-1	“minKey”	 
Max key	127	“maxKey”

evaluation operators:

* $expr allows use off aggregation within the query language ... it useful for compare two element in 1 document e.g:
  db.sales.find({$expr:{$gt:["$volume","$target"]}}) this just return all doc that are volume element grater than target element 
  another example : db.sales.find({$expr:{$gt: [{$cond: {if: {$gte: ["$volume",190] }, then: {$subtract: ["$volume",10]}, else: "$volume" } }, "$target" ] }})
* $jsonSchema validate documents against the given JSON Schema
* $mod performs a modulo operation on the value of a field and selects documents with a specified result 
* $regex Selects documents where values match a specified regular expression 
    { <field>: { $regex: /pattern/, $options: '<options>' } }
    { <field>: { $regex: 'pattern', $options: '<options>' } }
    { <field>: { $regex: /pattern/<options> } }

    { name: { $regex: /acme.*corp/i, $nin: [ 'acmeblahcorp' ] } }
    { name: { $regex: /acme.*corp/, $options: 'i', $nin: [ 'acmeblahcorp' ] } }
    { name: { $regex: 'acme.*corp', $options: 'i', $nin: [ 'acmeblahcorp' ] } }

    { name: { $regex: '(?i)a(?-i)cme' } }

    db.inventory.find( { item: { $not: { $regex: "^p.*" } } } )
    db.inventory.find( { item: { $not: { $regex: /^p.*/ } } } ) 

    db.inventory.find( { item: { $not: /^p.*/ } } )
* $text performs text search 
* $where Matches documents that satisfy a JavaScript expression 

** important querying Arrays

1. when you wanna have this document with filtering title:
{
  "_id":ObjectId("484623135646844631554648"),
  "name":"Max",
  "hobbies":[
    {
      "title":"Sports",
      "frequency":3
    },
    {
      "title":"Cooking",
      "frequency":6
    }
  ],
  "phone":094152044884
}
you must search for hobbies.title like this 
db.users.find({"hobbies.title": "Sports" })

2. if you wanna just filter size of array you can use $size e.g:db.users.find({hobbies: {$size:3} })

3. if we wanna search in array that contain object or something els :
db.<collectionName>.find({"genre": {$elemMatch: {title:"Sports",frequency:{$gte:3}}} })
this would return something like this 
{
  "_id":ObjectId("484623135646844631554648"),
  "name":"Max",
  "hobbies":[
    {
      "title":"Sports",
      "frequency":3
    },
    {
      "title":"Cooking",
      "frequency":6
    }
  ],
  "phone":094152044884
}

**Sort,Skip,Limit:
we can sort and skip and limit our result like this db.movies.find().sort({"rating.average":1,runtime:-1}).skip(100).limit(10)

**Projection and shape our results:

db.movies.find({},{name:1,genres:1, runtime:1,rating:1})
this will return all data we mention it and plus _id which is an exception if you wanna exclude the _id you must mention it e.g:
db.movies.find({},{name:1,genres:1, runtime:1,rating:1,_id:0})

update:
update(filter,data,options)
updateOne(filter,data,options)
updateMany(filter,data,options)
replaceOne(filter,data,options)

delete:
deleteOne(filter,options)
deleteMany(filter,options)

8.show collections // this command will give you list of all collections that db you have used
9.db.<collectionName>.deleteOne(filter,options) // e.g : db.products.deleteOne({name:"shampoo"}) // this command will find all documents and delete them
10.db.<collectionName>.deleteMany(filter,options) // e.g : db.products.deleteMany({marker:"toDelete"}) // this command will find all documents and delete them

11.db.<collectionName>.updateOne(filter,data,options) // e.g : db.<collectionName>.updateOne({name:"shampoo"},{$set:{marker:"toDelete"}}) //this command will find first document and update that
12.db.<collectionName>.updateMany(filter,data,options) // e.g : db.<collectionName>.updateMany({},{$set:{marker:"toDelete"}}) // this command will find all documents and set marker field to them or change marker fields

13.db.<collectionName>.insertMany(<data as array>,options) // e.g : db.products.insertMany([{"name" : "shampoo3", "price" : 14.15},{"name" : "shampoo4", "price" : 16.15}]) this will enter whole array to this collection

14.db.<collectionName>.find({distance: {$gt:10000}}) // all $ reserved by mongodb and do an action this case tell us find for me all documents that the distance is be grater than 10000 this characters called Comparison Query Operators

* $eq	Matches values that are equal to a specified value.
* $gt	Matches values that are greater than a specified value.
* $gte	Matches values that are greater than or equal to a specified value.
* $in	Matches any of the values specified in an array.
* $lt	Matches values that are less than a specified value.
* $lte	Matches values that are less than or equal to a specified value.
* $ne	Matches all values that are not equal to a specified value.
* $nin	Matches none of the values specified in an array.

15.db.<collectionName>.update(filter,data,options) // e.g : db.<collectionName>.update({name:"shampoo"},{marker:"toDelete"}) // this command will work without $set but this command will replace all object with this new object
16.db.<collectionName>.replaceOne(filter,data,options) // this command is as like as update command 

17.in mongo we can have date and time stamps also we can have array and object// e.g:db.<collectionName>.insertOne({name:"apple",isStartup:true,funding:1234567890123456789(we cannot store this number if you wanna store number you must use strings or multiple field for that),details:{ceo:"mark super"},tags:[{title:"super"},{title:"perfect"},10,"all"],foundingDate:new Date(),insertedAt: new Timestamp()})

18.db.stats() // this command will give you information about DB
19.typeof db.<collectionName>.findOne({}).<fieldName> // this command will gave you type of the thing that return: e.g:typeof db.passengers.findOne({}).name
20.db.<collectionName>.aggregate([{$lookup:{from:<nameOfAnotherCollectionThatHaveRelationWithUs>,localField:<theNameOfTheLocalFieldThatContainAuthorsKeys>,foreignField:<theNameOfTheForienFieldThatContainAuthors>,as:<theNameThatYouWannaDisplayAsNewField>}}])// if you wanna have relation and you wanna show that first you must implement aggregate framework  e.g: db.books.aggregate([{$lookup:{from:"authors",localField:"authors",foreignField:"_id",as:"creators"}}])

21.you can add validator to collection insertion like this:
db.createCollection('posts', {
  validator: {
    $jsonSchema: {
      bsonType: 'object',
      required: ['title', 'text', 'creator', 'comments'],
      properties: {
        title: {
          bsonType: 'string',
          description: 'must be a string and is required'
        },
        text: {
          bsonType: 'string',
          description: 'must be a string and is required'
        },
        creator: {
          bsonType: 'objectId',
          description: 'must be an objectid and is required'
        },
        comments: {
          bsonType: 'array',
          description: 'must be an array and is required',
          items: {
            bsonType: 'object',
            required: ['text', 'author'],
            properties: {
              text: {
                bsonType: 'string',
                description: 'must be a string and is required'
              },
              author: {
                bsonType: 'objectId',
                description: 'must be an objectid and is required'
              }
            }
          }
        }
      }
    }
  }
});

22.of course you can add validation after collection creation :
db.runCommand({collMod:"posts",{
  validator: {
    $jsonSchema: {
      bsonType: 'object',
      required: ['title', 'text', 'creator', 'comments'],
      properties: {
        title: {
          bsonType: 'string',
          description: 'must be a string and is required'
        },
        text: {
          bsonType: 'string',
          description: 'must be a string and is required'
        },
        creator: {
          bsonType: 'objectId',
          description: 'must be an objectid and is required'
        },
        comments: {
          bsonType: 'array',
          description: 'must be an array and is required',
          items: {
            bsonType: 'object',
            required: ['text', 'author'],
            properties: {
              text: {
                bsonType: 'string',
                description: 'must be a string and is required'
              },
              author: {
                bsonType: 'objectId',
                description: 'must be an objectid and is required'
              }
            }
          }
        }
      }
    }
  }
}})

23.you can do validation action to warn
db.runCommand({collMod:"posts",{
  validator: {
    $jsonSchema: {
      bsonType: 'object',
      required: ['title', 'text', 'creator', 'comments'],
      properties: {
        title: {
          bsonType: 'string',
          description: 'must be a string and is required'
        },
        text: {
          bsonType: 'string',
          description: 'must be a string and is required'
        },
        creator: {
          bsonType: 'objectId',
          description: 'must be an objectid and is required'
        },
        comments: {
          bsonType: 'array',
          description: 'must be an array and is required',
          items: {
            bsonType: 'object',
            required: ['text', 'author'],
            properties: {
              text: {
                bsonType: 'string',
                description: 'must be a string and is required'
              },
              author: {
                bsonType: 'objectId',
                description: 'must be an objectid and is required'
              }
            }
          }
        }
      }
    }
  }
},validationAction:"warn"})

24.db.dropDatabase()//  this is for when you wanna drop that database you already in use it  

25.mongoimport --db <dbName> --collection <collectionName> --authenticationDatabase admin --username <user> --password <password> --file <fileName>.json --jsonArray(this means there are several docs to import not just one) --drop(this command is dangerous and will drop previous collection and will create new one)