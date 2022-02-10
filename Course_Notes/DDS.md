```
mongod --dbpath=/Users/caoyanan/Desktop/USF/697_distributed_datasys/data/db

mongoimport --db msds697 --file /Users/caoyanan/Desktop/USF/697_distributed_datasys/HW/HW2/business.json

mongoimport --db msds697 --file /Users/caoyanan/Desktop/USF/697_distributed_datasys/msds697_distributed_data_systems_2022/Data/world_bank_project.json
```

# Part 1 NoSQL

* Why NoSQL? 
  * review relational db - ACID
    * relation = records, and every relation has **schema** (col name and types, condition like >0)
    * CRUD
    * ACID - atomic - all/none, consistent - rules, Isolated - conoccurance, durable - damage
  * Pro/con
    * pro
      * standard - language&operations
    * Con
      * big data: 
        * volume(TB/EB), variety(unstructure) and Velocity
        * Scale out: distributed computing - cheaper, reliable, faster
          * *distrbuted is scale out, while parallel can be done in a single computer*
          * *data are duplicated when distributed*
      * mismatch: relational database is not the in-memory data structure and hard to scale out 
        * *relational database is good for retrieveing more efficiently - index*
* What is NoSQL?
  * benefits of nosql
    * Generally,
      * Take schemaless data. 
      * Non-relational.
      * Open-source.
      * Trade off traditional consistency for other properties. 
      * Run on clusters
  * Compare sql and nosql
    * Sql: good(ACID, standard); bad(fix schema, hard to scale out, mismatch structure)
    * NoSql: good(schemaless, scalable, cluster, open source), bad(unmatured)
  * postgress and nosql
* Types of NoSQL
  * Types: aggregate(key-value, document-MongoDB, columnar) - relationship(graph)
  * what is aggregate?  Collection of related objects treated as a unit.
    * why: 
      * 1 unit's information stay in the same place. So on cluster, they are in the same node
      * a single aggregate is a atomic unit
    * pro/con
      * pro: clearer semantics; good on cluster
      * con: hard to draw boundaries; need clear data analysis/management goal; no ACID
  * relationship nosql - 
    * complex schema
    * graph database - 
      * node/object and edges/relationships
      * focus on graph traverse, if in RDBMS there will be many joins and poor performance
      * run on a sinble server rather than distributed
* Choice of DBMS (SQL and NoSQL) 
  * based on the way data is going to be used
    * Orders - aggregate
    * social networ - graph
    * item price and orders - relational

  * cannot just replace

* Document Database
  * related to a one doc - Jason
  * Schema less - some attributes don't exist in another doc; new attributes can be added without being defined



# Part 2 CRUD

* MongoDB 

  * terminology
    * database - database
    * collection - table
    * document - row
    * Field - column
  * why mongoDB?
    * easy use - no defined schema
    * Scaling: balance scale(where to store),  to cluster, distirbuting documents, route to the correct machine
    * many features: CRUD, index, pipeline, special collection types, file storage
    * drawbacks: possibly outdated since stored in different places
  
* Supported Data Types

  * common
    * null, boolean,Number, string, date
    * **array** - [],atomic level; both ordered and unordered; contain diff data types
    * **embedded document** - {} 
    * binary data(string), 
    * code
    * Regular expression, 
    * **object id** - automated added - timestamp+machine+processID+imcrement

* Operations - CRUD

  * Create 

    * ```sql
      use database_name -- choose database
      db -- check current db in use
      db.createCollection(business, options) -- create
      db.collection_name -- access
      db.collection_name.insertOne(document)
      db.collection_name.insertMany([document1,document2])
      -- db.friend.insertMany([diane, yannet])
      ```
  
    * ```bash
      $ mongoimport --db msds697 --collection business --file --mode insert/upsert
      ```
  
  * Read
  
    * ```sql
      db.collection_name.find(query, projection) -- query {key: value}; omit this parameter or pass an empty document ({}); 
      db.collection_name.findOne(query, projection)
      projection = {'_id':False} -- projection {key : true/false}
      ```
  
  * Update 
  
    * All write operations in MongoDB are atomic on the level of a **single** **document**.
  
    * ```sql
      db.collection_name.updateOne(filter, update, options)
      db.collection_name.updateMany(filter, update, options)
      * options = {upsert: true}
      db.business.update(filter,{upsert: true, multi: true})
      
      -- $set:{} = change/upsert; $unset = remove {$unset: {<field1>: "", ... } }
      -- $inc:  {$inc: {<field1>: <amount1>, <field2>: <amount2>, ... } }
      -- $rename: {$rename: {<field1>: <newName1>, <field2>: <newName2>, ... } }
      -- $min => take the min between
      -- $max => take the max between
      ```
      
    * array
  
      * once one of the element meet the criteria, then all array will be returned
  
      ```sql
      $push -- add one {$push : {<field1> : <value1>, ...}}
      $pop -- remove the latest added {$pop : {<field> : <-1|1>, ...}} first/last
      $pull -- remove all the element meet the condition
      e.g. {$pull:{"grades": {'grade':'C'}}}
      $ - special holder
      ```
  
    * ```sql
      new Date(): 2012-01-12T00:00:00.000+0000
      Date(): Tue Feb 01 2022 10:40:52 GMT-0800 (PST)
      ```
  
    * ```sql
      $ -- update the first one meet
      $[] -- update entire no matter meet or not
      $[indentifier] -- update ones meet condition
      
      db.collection.update({'restaurant_id': '40364467','grades.score':11},{$set: {"grades".$.score: 11}})
      -- the first one meet the condition of the array meet the condition
      db.collection.update({'restaurant_id': '40364467','grades.score':11},{$set: {"grades".$[].score: 11}})
      -- at lease one of the array meet the condition
      db.collection.updateMany({},{$set: {"grades".$[element].score: 11}},{arrayFilters:[{"element.score":10}]})
      ```
  
    * ```sql
      db.business.update({"address.zipcode" : "10462", "address.street" : "Castle Hill Avenue"}, {$set:{'grades.$[element].grade':'A+'}},{arrayFilters:[{"element.score":{$gt:10}}], multi:true})
      
      ```
  
  * Delete
  
    * ```sql
      db.collection_name.deleteMany(filter)
      db.collection_name.deleteOne(filter)
      db.collection_name.drop()
      ```
  
    * drop is more efficient, it operates without concerning how data is stored

​			

# Part 3 Read/Find

* Operation - Read
  * Cursor method
    
    * find() returns a cursor
    
      * ```sql
        db.collection.find().method()
        -- count()
        -- pretty()
        -- Sort({field:value}) => 1 ASC; -1 DESC
        -- limit(1)
        ```
    
  * Query
    * Range operator
      * ```sql
        db.collection_name.find({field:{range_operator:value}})
        -- $lt, lte; gt, gte, eq, ne
        ```
      
    * logical operator
      * ```sql
        db.collection_name.find({field:{operators:[condition_1, condition_2, ...]}})
        db.collection_name.find({operators:[condition_1, condition_2, ...]})
        -- $or, nor, in, nin, and, not
        ```
  
  * Type-specific Queries
  
    * regular expression
  
      * ```sql
        db.collection_name.find({field:{$regex: 'pattern', $options: '<options>'})
        -- options 
        -- - i=>case insensitive 
        -- - m=>for multiple values/lines
        -- - x=>regard comments as comments
        -- - s=>allow the dot character 
        
        db.name.find({'name':{$regex:'^burger'}})
        ```
  
      * ```
        ^ start
        $ end
        
        --- type of character0
        . a single character
        \s a whitespace character
        \S non-whitespace
        \d a digit
        \D non-digit
        \w a word a-z A-Z 0-9 _
        \W non-word
        
        --- frequency
        * zero or more of the previous
        + one or more of the previous
        {n,m} matches between n and m of the previous thing
        
        http://www.pcre.org
        ```
  
      * [http://www.pcre.org](http://www.pcre.org)
  
    * Embedded Documents
  
      * ```sql
        find({query},{projection}): 
        -- Returns subset of document in a collection.
        ```
  
    * Type-specific **Projection**
  
      * **be carefull , it is in projection{}**
  
      * Array
  
        * `$slice`: but still regarding the whole array, not the ones meet the criteria
  
          * ```sql
            db.collection_name.find(criteria, {array_name:{”$slice”: N}})
            
            {array_name:{”$slice”: [N_to_skip, N_to_return]}}
            ```
  
        * `$`: Return the **first** array element matched your criteria
  
          * no need to know the index, but `$slice`
        
          * ```sql
            db.business.find({'grades.grade':'A', 'grades.score':{$gt:10}},{'_id':0, 'grades.$':1})
            -- two criteria filtering can lead to unpredictable behavior, seems the 2nd overwrite the 1st or 'or'
            ```
  
          * Thus:
        
            * only one array field
            * only single condition
            * only one positional $ 
  
        * `$elemMatch`: one element matches all, return documents
        
          * ```sql
            db.business.find({},{'grades':{$elemMatch:{'grade':'A', 'score':{$gt:10}}}})
            -- first find all and the select projection, doing multi-criteria on different fields
            --  but '_id' still there
            ```

# Code Summary so far

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









# Part 4 Aggregation

* **Aggregation Operations (aggregation pipeline)**

  * aggreagte pipeline

    * 'in manhattan , calculate the avg score for each grade in grades'
      * filter manhattan 
      * project grades only
      * unwind grades: 1 doc with 3 record to 3 docs with 1 in each
      * groupby 
      * Average


  * ```sql
    db.collecion_name.aggregate({transform_operator_1 : criteria_1}, 
                                {transform_operator_2 : criteria_2}, 
                                ...)
                                
    db.area.aggregate({$match:{'city':Manhattan}},
                      {$project:{'grades':1, '_id':0}},
                     	{$unwind: '$grades'},
                     	{$group:{'_id':'$grade', 
                      				 'avg':{'$avg': 'score'}}}
           					 )
           					 				 
    -- with $<field> dollar sign, it will point to the value in side this field 
    -- so unwind each value inside grades and put then in each one a doc
    ```

  * transform_operators performs operations including 
    * filtering, transformation, grouping, sorting 
      * `$match, $project, $group, $unwind, $sort, $lookup(outer join) etc.`

  * match

  * ```sql
    { $match: { <query> } }
    
    db.world_bank_project.aggregate([{$match:{'mjtheme_namecode.name':"Human development"}}]}
    ```

  * project

    * project output or add new fields

    * ```sql
      { $project : { <specification(s)> } }
      -- specification:
      -- {‘field' : ‘true'/'false'}
      -- {‘new_field' : {$operator: expression}}
      ```

    * syntax can be different with what we learned in part 2

    ```sql
    $add -- add nums, dates
    -- {$add: ["$<field1>", "$<field2>" ] }
    $eq/gt/lt -- takes []***
    -- { $gt: [ "$<field>", <value> ] }
    $filter 
    -- {$filter: { input:"$<array_field>" , as: "<new-varname>”, cond: <expression> } }
    $size
    --  {$size: "$<array_field>" }
    $cond
    --  {$cond: [<boolean-expression>, <true-case>, <false-case>]}
    $concat
    -- {$concat: ["$<field1>", "$<field2>", ... ] }
    $dateDiff
    --  { $dateDiff: { startDate: "$<field1>", endDate: "$<field2>", unit: "day"}
    $convert
    -- {$convert : {input : "$<field>”, to: "data_type”}}
    
    ```

    * Example, $$ goes even further to next level

    * ```sql
      db.world_bank_project.aggregate(
                   {$project:{'id':1, '_id':0,
                              'human_development_project':
                              {$filter:{input:'$mjtheme_namecode',
                                        as: 'mjtheme_namecode_name', 
                                        cond:{$eq:['$$mjtheme_namecode_name.name','Human development']}}
        											}}},
                    {$project:{'id':1, 'human_development_project':1, 
                               'array_size':{$size:'$human_development_project'}}},
                    {$match:{'array_size': {$gt:0}}},
                    {$project:{'array_size':0}}
                    )
      ```

  * Group

    * ```sql
      {$group: {_id: “$<field>”,
      <new_field_name>: {<accumulator1> : <expression1> }, ... } }
      -- Accumulator:
      -- $sum, $avg, $min, $max, $first, $last
      
      db.world_bank_project.aggregate([{$group:{'_id': '$countrycode', 'count': {$sum:1}}}])                            
      ```


  * unwind

    * ```sql
      {$unwind: '$<field_path>'}
      
      
      db.world_bank_project.aggregate([{$unwind: '$theme_namecode'}, 
                                       {$group:{'_id':'$theme_namecode',' 
                                       					'count':{$sum:1}}}])
      ```

  * Sort

    * ```sql
      {$sort: {<field1>: <sort order>, <field2>: <sort order> ... } }
      -- sort order : 1 (ascending), -1 (descending)
      
      db.world_bank_project.aggregate([{$project:{'borrower':1,'id':1,'_id':0,
                                     							'approvalfy_num':{$convert:{input:'$approvalfy', 
        																																	    to:'double'}}}},
                          						 {$sort:{'approvalfy_num':-1}}])
      ```

  * lookup

    * ```sql
      db.<collection_1>.aggregate({$lookup:{localField: “<field_1>”, 
                                            from: “<collection_2>”,
                                            foreignField: “<field_2>”,
                                            as: “<output_field>”}} )
                                            
      
      db.world_bank_project.aggregate([{$match:{'countrycode':'CN'}},
                                       {$lookup:{localField:'countrycode',
                                                 from:'country_code',
                                                 foreignField:'code',
                                                 as:'country_phone_info'}}])                                                          
      ```

* Index

  * 
