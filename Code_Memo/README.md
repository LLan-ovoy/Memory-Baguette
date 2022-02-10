# NoSQL Code Summary

## Basic

#### CRUD basic w/o considerin array

```sql
-- -- create database
db.createCollection('friends')

-- -- insert records
db.friends.insertMany([diane, yannet])
db.friends.insertOne(shan)
-- template
db..insertMany([])

-- -- read records
db.friends.find({'address.city':'San Francisco'})
-- template
db..find(
  {},
  {'_id':0})


-- -- update records
db.friends.updateMany({"name":"Diane MK Woodbridge"}, 
                      {$set:{"title":"Assistant Professor"}})
-- template
db..updateMany(
  {},
  {$set:{'':''}},
  {upsert:0})


-- -- delete records
db.friend.deleteMany({"officeAddress.city":"San Francisco"})

-- -- drop database
db.business.drop()
```

#### Insert/delete inside array

```sql
-- -- push/add in array
db.business.update({'name':'White Castle','address.street':'Pennsylvania Avenue'},
                   {$push:{'grades':{'date':new Date(),'grade':'A','score':NumberInt(9)}}},
                   {})
-- template
db..updateMany(
  {},
  {$push:{'grades':{'date':'today'}}},
  {})


-- -- pop/remove 1 from array
db.business.updateMany({"name":"White Castle", "address.street":"Pennsylvania Avenue"},														 {$pop:{'grades':1}},
                       {})  
-- template
db..updateMany(
  {},
  {$pop:{'grades':1}},
  {})


-- -- pull/remove all from array
db.business.update({'restaurant_id':'40364467'},
                   {$pull:{'grades':{'grade':'C'}}},
                   {})
-- template
db..update(
  {},
  {$pull:{'grades':{'grade':'C'}}},
  {})
```

#### Update inside array

```sql
db.business.find({"restaurant_id": "40356483", "grades.score" : 10}) 
-- will find array inside at least 1 matches
```

```sql
-- -- $ change first one matches
db.business.updateMany({"restaurant_id": "40356483", "grades.score" : 10}, 
                       {$set:{"grades.$.score": 11}}) 
-- template
db..updateMany(
  {'grades.score': 10},
  {$set:{'grades.$.score':11}},
  {})                       
                 
       
-- -- $[] change all inside which at least one matches
db.business.updateMany({"restaurant_id": "40356483", "grades.score" : 10},
		   								 {$set:{"grades.$[].score": 11}})
-- template
db..updateMany(
  {'grades.score': 10},
  {$set:{'grades.$[].score':11}},
  {})		   
  
  
-- -- $[elem] change only matches
db.business.updateMany({"restaurant_id":"40356483"}, 
           						 {$set:{"grades.$[element].score":11}},
		   								 {arrayFilters:[{"element.score":10}]})
-- template
db..updateMany(
  {'grades.score': 10},
  {$set:{"grades.$[element].score":11}},
  {arrayFilters:[{"element.score":10}]}})		   		 
```

#### General operator

```sql
-- -- cursor method
db..find().count()
db..find().pretty()
db..find().limit()
db..find().sort({'':1})

-- -- range operation
db..find({'':{$gte:}})

-- -- logic operation
db..find(
  {$or:[{'':''}, {'':''}]},
  {'_id':0})
  
db..find(
  {'':{$in:['','']}},
  {'_id':0})  
  
-- -- regular expression
db..find(
  {'':{$regex:"\w*project\w*", $options:'imxs'}},
	{'_id':0})

-- -- document
majorsector_percent_input = {"Name" : "Health and other social services",
"Percent" : NumberInt(100)}

db..find({'':majorsector_percent_input})

```

#### Array projection

```sql
-- -- return nth item in array, only one
db.world_bank_project.find({'project_name':{$regex:'project$', $options:'i'}},
                           {'theme_namecode':{$slice:-1}, 'project_name':1, '_id':0})
-- template
db..find(
  {'':''},
  {'':{$slice:[0,1]}, '_id':0})


-- -- return the first matches item in array, only first 1
db.world_bank_project.find({"majorsector_percent.Percent":{$gte:70}}, 
                           {"majorsector_percent.$":true, "project_name":true, "_id":false})
-- template
db..find(
  {'grades.grade':''},
  {'grades.grade.$':1, '_id':0})


-- -- return matches inside doc, only first 1
db.world_bank_project.find({"majorsector_percent.Percent":{$gte:70}},
                           {"projectdocs":{$elemMatch:{"DocType" : "PID", "DocDate":{$regex:"2013$"}}}, 
                            "project_name":true, "_id":false})
-- template
db..find(
  {},
  {'':{$elemMatch:{'':''}}, '_id':0})
```



## Aggregate

```sql
```

