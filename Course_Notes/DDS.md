```bash
mongod --dbpath=/Users/caoyanan/Desktop/USF/697_distributed_datasys/data/db

mongoimport --db msds697 --file /Users/caoyanan/Desktop/USF/697_distributed_datasys/HW/HW2/business.json

mongoimport --db msds697 --file /Users/caoyanan/Downloads/usf_locations.js
```

```bash
spark-submit quiz1.py > result.txt
diff result.txt output.txt
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

  * Perform several stages of operations: each stage's perform output are passed to next stage
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
    * Field Path $\<field\> in criteria
      *  "\$\<field\>" is equivalent to "\$$CURRENT.\<field\>"
    
  * match

  * ```sql
    {$match: {<query>}}
    
    db.world_bank_project.aggregate([{$match:{'mjtheme_namecode.name':"Human development"}}]}
    ```

  * project

    * project output or add new fields, or rename

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
    -- {$filter: {input:"$<array_field>" , as: "<new-varname>”, cond: <expression>}}
    $size
    --  {$size: "$<array_field>" }
    $cond
    --  {$cond: [<boolean-expression>, <true-case>, <false-case>]}
    $concat
    -- {$concat: ["$<field1>", "$<field2>", ... ] }
    $dateDiff
    --  { $dateDiff: {startDate: "$<field1>", endDate: "$<field2>", unit: "day"}
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


  * unwind: from 1:[1,2,3] to 1:1, 1:2, 1:3

    * ```sql
      {$unwind: '$<field_path>'} -- when need to group igorning array
      
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

  * lookup: left outer join:  adds a new array field

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

  
  * without field, MongoDB perform a collection scan: scan every document in a collection, to select those documents that match the query statement
  
  * which field you want to use, interested in for retrieving
  
  * B-tree: binary based on range
  
  * hash into buckets
  
  * ```sql
    db.business.createIndex({'field':value})
    -- Range-based indexes: B-tree data structure by default
    	-- Direction : 1 (Ascending), -1 (Descending)
    -- Hashed indexes – “hashed”
    -- other index types including text index, 2d index
    
    db.business.getIndexs()
    -- Check which indexes exist on a collection
    -- Default : _id (Cannot be deleted)
    
    db.business.dropIndex({'field': direction})
    ```
  



# Part 5

* single server: easy to manage operations, graph db

* distributeds models
  
  * Aggregate: collet of related object as a unit; natural unit
  * two ways for data distributed
    * sharding: different data on different nodes
    * Replication: copy same data over multiple nodes
      * primary-secondary replication
      * Peer to peer replication
  * sharding: Improves scalability
    * distirbuted data to diff server, each has own read and write
    * consider: Locate the data commonly accessed together on the same node/ physical location; keep load even
    * Pro/cons
      * Pros : Improves read and writes.
      * Cons : Low resilience, if one small-db dies, all lose
    * mongoDB auto-sharing: autoshard, balance, access to the right one
      * mongod: Primary database process (a daemon) that runs on an individual server.
      * mongos: Routing process to manage storing different data on different servers and query against the right server.
  * Replication: redundancy but increase acailability
    * Provides fault tolerance against the loss of a single database server
    * type:
      * Primary-secondary replication
      * if secondary dead, then route to the primary. But **write** action **only** to the primary replication, and primary Synchronize to secondary, some **time-delay** may lead to **inconsistance**.
      * Primary
        - Authoritative source for the data.
        - Responsible for processing updates.
        - if primary dead, will automated choose ganother secondary as primary
      * Secondaries
        - Contains copied data from a master.
      * pro/con:
        * Pro: scalability, intensive read; resilience
        * Cons: poor intensive write, inconsistency
      * Peer-to-peer replication
  
* sharding + replications = **Scalability + Fault Tolerance**
  *  Primary-Secondary replication and sharding
    * Multiple nodes.
      * Each data only has one master.
      * A node can be a master for some data and slave for others.

* CAP Theorem => only get two

  * ACID addresses an individual node's data consistency. CAP addresses cluster-wide data consistency 
    * Consistancy
      * all nodes on-dated
      * when updating, cannot write (sacrifies availability)

    * Availability
      * Non-failing node must response

    * Partition Tolerance - communication
      * survive from **communication** breakages



    * MongoDB: mainly Consistancy and partition Tolerance, but can configure






# Part 6

* Spark SQL

  * DataFrame

    * Keywords:
      * Handle structured, distributed data 
      * a table-like representation
      * named column declared with column types.
    * can query DataFrame register from different source

  * Creating DataFrames 

    * load data as text, parse the lines, and identify elements for processing

    * ```sql
      -- initiate rdd
      from pyspark import SparkContext
      sc = SparkContext.getOrCreate()
      
      -- initiate SparkSession
      from pyspark.sql import SparkSession
      ss = SparkSession.builder.getOrCreate()
      
      -- we need both
      ```

    * create DataFrames from RDD.toDF()

      ```sql
      -- RDD to DataFrame 
      RDD.toDF(['id','name','class_id']) -- cannot specify the data type
      
      -- DataFrame to RDD
      DataFrame.rdd
      ```

      * Limitation

        * All the columns are string type.
        * All the columns are nullable.

      * ```sql
        -- check the schema using 
        DF.printSchema()
        -- see the data frame using 
        DF.show(num_rows)
        ```

    * create DataFrames from sc.createDataFrame()

      ```sql
      from pyspark.sql.types import *
      -- DataType: NullType(), StringType(), BinaryType(), BooleanType(), DateType(),TimstampType(),DoubleType(), FloatType(), IntegerType(), ArrayType() etc.
      
      schema = StructType([StructField("name", StringType(), nullable=True), StructField("age", IntegerType(), True)])
      sc.createDataFrame(data, schema=schema, samplingRatio=None, verifySchema=True)
      	-- data: RDD of Row/tuple/list/dict, list, orpandas.DataFrame
      	-- schema: StructType
      	-- samlingRatio: used for inferring the schema
      	-- verifySchema: verify data types of every row against schema, if not satisfied, then won't insert all things, ERROR
      ```

  * DataFrame API Basics

    * only changing the output, not the original data

    ```sql
    -- select
    DF.select('id','name')
    DF.select(df['id'],df['name'])
    
    -- drop column
    DF.drop('id')
    
    -- condition
    DF.filter('zip == 94123')
    DF.where('zip == 94123')
    
    -- rename columns
    DF.withColumnRenamed('zip','zip_code')
    
    -- add columns
    Df.withColumn('odd_zip', business_df['zip'] % 2) 
    
    -- sort values
    DF.orderBy()
    DF.sort(['name','id'],ascending=[True, False])
    ```

    

# Part 7

* SQL functions with DataFrame: Use SQL functions for calculation

  ```python
  from pyspark.sql.functions import *
  from pyspark.sql.window import Window
  ```

  * Scalar functions : 

    ```python
    from pyspark.sql.functions import *
    ```

    * Return a single value for each row based on calculations on one or more columns

      ```sql
      -- math: abs, log, avg, count;
      
      -- string: length, concat, trim, contains;
      business_df.withColumn('onHoward', col('street').contains('Howard')).show(5)
      
      -- time: year, date_add, datediff, next_day
      ```

  * Aggregate functions : 

    * Return a single value for a group of rows.

      ```sql
      DF.groupBy() -- Take column names or a list of column objects
      DF.agg(list_of_aggregate_fuctions)
      -- aggregate function: min(), max(), sum(), count()
      
      DF.groupBy(col).max(col).min(col) # doesn't work
      DF.groupBy('id').agg(min('num_bikes'), max('num_bikes'), avg('num_bikes')).show(5)
      ```

  * Window functions : 

    ```python
    from pyspark.sql.window import Window
    ```

    * Return several values for a group of rows.

      ```sql
      Window.partitionBy().orderBy(“col_name”).rowsBetween(start, end) 
      
      Window.partitionBy().orderBy(“col_name”).rangeBetween(start, end)
      -- rangeBetween() doesn’t work with non-ordered windows, need to orderBy first then can use this
      window.unbounded_preceeding/following
      count('zip').over(Window.partitionBy('state').orderBy('zip').rowsBetween(-1,1))
      
      -- Window functions
      lag(col, offset)
      lead(col, offset)
      row_number()
      rank()
      percent_rank()
      dense_rank()
      
      -- narrow down to smaller dataset
      ```

  * User-defined functions (UDF) :

    ```sql
    udf(f=None, returnType=StringType) 
        
    udf_name = udf(lambda function definition or function name, returnType)
                 data_frame.select(udf_name(col))
    ```

    * Generate custom scalar or aggregate functions.

      ```sql
      def check_sf(x):
          if ('SF' in x) or ('San Francisco' in x):
              return True
          else:
              return False
      
      check_sf = udf(check_sf)        
              
      check_sf = udf(lambda x: ('SF' in x) or ('San Francisco' in x))   
      
      business_df.select('name', check_sf('city')).show(5)
      ```

  * Join : Join two data frames.

    ```sql
    DF.join(dataframe, condition, join_type)
    -- Join types: inner (default), left_outer, right_outer, leftsemi
      -- outer: return unmatched rows from both tables
      -- leftsemi: get the names within left table that appear in right table.
    ```



```sql
from pyspark.sql.window import Window
from pyspark.sql.functions import s*

-- aggregation
DF.groupBy() -- Take column names or a list of column objects
DF.agg(list_of_aggregate_fuctions)
-- aggregate function: min(), max(), sum(), count()
DF.groupBy('id').agg(min('num_bikes'), max('num_bikes'), avg('num_bikes')).show(5)

-- window
from pyspark.sql.window import Window
Window.partitionBy().orderBy(“col_name”).rowsBetween(start, end) 
Window.partitionBy().orderBy(“col_name”).rangeBetween(start, end)
-- rangeBetween() doesn’t work with non-ordered windows, need to orderBy first then can use this
window.unbounded_preceeding/following
count('zip').over(Window.partitionBy('state').orderBy('zip').rowsBetween(-1,1))
-- Window functions
lag(col, offset)
lead(col, offset)
row_number()
rank()
percent_rank()
dense_rank()
-- narrow down to smaller dataset

-- udf
def check_sf(x):
    if ('SF' in x) or ('San Francisco' in x):
        return True
    else:
        return False
check_sf = udf(check_sf)        
check_sf = udf(lambda x: ('SF' in x) or ('San Francisco' in x))   
business_df.select('name', check_sf('city')).show(5)


-- join
DF.join(dataframe, condition, join_type)
-- Join types: inner (default), left_outer, right_outer, leftsemi
  -- outer: return unmatched rows from both tables
  -- leftsemi: get the names within left table that appear in right table.
```



* Registering DataFrame in the Table Catalog

  * save as table

    ```sql
    -- Save as table: create a spark warehouse directory
    DF.write.saveAsTable(name, format=None, mode=None, partitionBy=None)
    
    business_df.write.saveAsTable('Business')
    ```

  * query by SQL after registering

    ```sql
    ss.sql('SELECT * FROM table_name')
    ```

  * To specific directory instead of current path

    * Register a table **permanently (in a parquet format)**.
    
    ```sql
    DF.write.option("path", "/spark-warehouse/business").saveAsTable(name)
    
    ss.sql("SELECT * FROM parquet.`/spark-warehouse/business`").show(5)
    -- "`" not "'"
    ```
    
    * Spark will write data to a default table path. When the table is dropped, the default table path will be removed too.
    * The table definitions will survive your application’s restarts and are persistent.
      * Parquet : Column-oriented data storage format ; Efficient compression and encoding scheme.

* Loading/Writing Data using SparkSQL

  * Datatypes 

    * JSON 

      ```sql
      -- {key: value}, no fixed schema
      world_bank_prj = ss.read.json('../Data/world_bank_project.json')
      
      world_bank_prj.write.json('world_bank_project')
      ```

      * look through all the field and create a data frame, it is autoinfering the schema, **cannot assign schema**

    * CSV： **coalesce**

      ```sql
      business_df = ss.read.csv('../Data/SF_business/filtered_registered_business_sf.csv')
      -- can assign schema, but it won't work, if want, need to do createDataFrame from RDD
      -- business_schedma = StructType([StructField("zip", IntegerType(), True), StructField("name", StringType(), True),StructField("street", StringType(), True), StructField("city", StringType(), True),StructField("state", StringType(), True)])
      
      business_df.coalesce(1).write.csv('sf_business')
      -- coalesce minimizes the data shuffle, while repartition is more
      ```

    * Parquet： **only directory** 

      ```sql
      supervisor_df = ss.read.parquet('/Users/caoyanan/Desktop/USF/697_distributed_datasys/msds697_distributed_data_systems_2022/Day8/spark-warehouse/supervisor')
      -- only input the directory, since there are more than one parquet
      
      df.write.parquet(<file_path>, etc)
      ```

      

  * Data Sources

    * S3

      * **s3a**: Hadoop’s client which offers high-performance IO against Amazon S3 object store.

      * configuration

        * **JAR files** - add aws-java-sdk-bundle and hadoop-aws jars - **version**

          ```sql
          ss = SparkSession.builder.config("spark.jars.packages", "com.amazonaws:aws-java-sdk-bundle:1.11.901,org.apache.hadoop:hadoop-aws:3.3.1").getOrCreate()
          
          -- version 1.11.901 should be comportable with version 3.3.1
          ```

          * Check whether matching: https://mvnrepository.com/artifact/com.amazonaws/aws-java-sdk

        * Add AWS Access Key and Secret Key.

          ```sql
          ss._jsc.hadoopConfiguration().set("fs.s3a.access.key", os.environ['AWS_ACCESS_KEY_ID'])
          ss._jsc.hadoopConfiguration().set("fs.s3a.secret.key", os.environ['AWS_SECRET_ACCESS_KEY'])
          ```

        * Use 

          ````sql
          ss.csv.read('s3a://bucket/path')
          ````

    * Relational Databases and Other DB (MongoDB) with JDBC

      ```sql
      from pyspark.sql import SparkSession
      
      spark = SparkSession \
          .builder \
          .appName("myApp") \
          .config("spark.jars.packages", "org.mongodb.spark:mongo-spark-connector_2.12:2.4.0")\ 
          				-- for databrick, avoid connection be restarted
          .config("spark.mongodb.input.uri", "mongodb+srv://UPDATE:UPDATE@UPDATE/msds697.business")\
          .config("spark.mongodb.output.uri", "mongodb+srv://UPDATE:UPDATE@UPDATE/msds697.filtered_business")\
          .config("spark.network.timeout", "3600s")\
          .getOrCreate()
      ```

      * Read and write: everytime goes wrong, restart kernel

        ```sql
        df = spark.read.format('com.mongodb.spark.sql.DefaultSource').load() -- load from input uri
        
        df.write.format("com.mongodb.spark.sql.DefaultSource")\ -- write to output uri
                .mode("overwrite")\
                .option("database","db_name")\
                .option("collection", "collection_name")\
                .save()
        ```

        

```
DF.na.fill(0, ['count'])
```



# Part 8

* Machine Learning With Spark (Spark ML)

  * MLlib DateFrame-based, not cover MLlib Rdd-based

  * Main Components

    * 3 ML component

      * Estimators (ML algorithm itself): Algorithms that produce transformers by fitting on a dataset., return a transformer
      * Fransformers: return transformed data
        * feature transformer: return new columns like feature vectors
        * learning model: return with predicted label
    * Evaluators: metrics
    * 2 architecture 
  
      * ML parameters: hyper parameter
        * `ParamGridBuilder()` to select in the set of parameter in `CrossValidator()` => return the best model among the hyperparameters
      * ML pipeline
        * string indexer: we want `cat.code without order`, to remove order use `one-hot encoding`
    
  * Algorithms
  
    > * Feature  Extractors, Transformers, Selector and Locality Sensitive Hashing.
    >
    >   - Feature Extractors : 
    >     - [TF-IDF](https://spark.apache.org/docs/2.0.0-preview/ml-features.html#tf-idf), 
    >     - Word2Vec, 
    >     - CountVectorizer, 
    >     - FeatureHasher
    >
    >   - Feature Transformer : 
    >     - Tokenize, 
    >     - StopWordsRemover, 
    >     - n-gram, 
    >     - Binarizer, 
    >     - PCA, 
    >     - PolynomialExpansion, 
    >     - Discrete Cosine Transform (DCT), 
    >     - StringIndexer, 
    >     - IndexToString, 
    >     - OneHotEncoder , 
    >     - OneHotEncoderEstimator, 
    >     - VectorIndexer, 
    >     - Interaction, 
    >     - Normalizer, 
    >     - StandardScaler, 
    >     - MinMaxScaler, MaxAbsScaler, 
    >     - Bucketizer, 
    >     - ElementwiseProduct, 
    >     - SQLTransformer, 
    >     - VectorAssembler, 
    >     - VectorSizeHint, uantileDiscretizer, 
    >     - Imputer
    >
    >   - Feature Selectors : 
    >     - VectorSlicer, 
    >     - Rformula,
    >     -  ChiSqSelector
    >
    >   - Locality Sensitive Hashing :
    >     - LSH Operations : 
    >       - Feature Transformation, 
    >       - Approximate Similarity Join, 
    >       - Approximate Nearest Neighbor Search
    >     - LSH Algorithms : 
    >       - Bucketed Random Projection for Euclidean Distance, 
    >       - MinHash for Jaccard Distance
    >
    >   * Classification and Regression
    >     - Classification : 
    >       - Logistic regression, 
    >       - Decision tree classifier, 
    >       - Random forest classifier, 
    >       - Gradient-boosted tree classifier, 
    >       - Multilayer perceptron classifier, 
    >       - One-vs-Rest classifier (a.k.a. One-vs-All), 
    >       - Naive Bayes, 
    >       - Linear Support Vector Machine
    >     - Regression: 
    >       - Linear regression, 
    >       - Generalized linear regression, 
    >       - Decision tree regression, 
    >       - Random forest regression, 
    >       - Gradient-boosted tree regression, 
    >       - Survival regression , 
    >       - Isotonic regression
    >     - Decision trees
    >     - Tree Ensembles: 
    >       - Random Forests, 
    >       - Gradient-Boosted Trees (GBTs)
    >   * Clustering : 
    >     * K-means, 
    >       Latent Dirichlet allocation (LDA), 
    >     * Bisecting k-means,
    >     * Gaussian Mixture Model (GMM)
    >   * Collaborative filtering for Recommender Systems 
    >   * Frequent Pattern Mining : 
    >     * FP-Growth,
    >     *  PrefixSpan
  
  * Logistic Regression
  
    * Step1: $adult.dat \Rightarrow spark.DF$
  
      * create RDD, change data type, define schema, create DF
  
        ```sql
        census_raw = sc.textFile('adult.raw').map(lambda x: x.split(', ')).map(lambda x: [toDoubleSafe(i) for i in x])
        
        adultschema = ...
        
        dfraw = ss.createDataFrame(census_raw, adultschema)
        ```
  
      * missing value: mode imputing
  
        ```sql
        -- check missing
        for col in dfraw.columns:
        dfraw.groupby('{}'.format(col)).count().sort('count', ascending=False).show()
        
        -- replace
        dfraw = dfraw.replace(['?'],['Private'], ['workclass'])
        dfraw.groupby('workclass').count().sort('count').show()
        ```
  
      * categorical string: 
  
        * `StringIndexerModel() `: string categorical values into integer indexes. algorithm is estimator, it creates a transformer
  
          ```python
          #converting strings to numeric values
          from pyspark.ml.feature import StringIndexer
          
          def stringindex_trans(df, cols):
              new_df = df # copy df
              for c in cols:
                  str_ind = StringIndexer(inputCol=c, outputCol=c+'_num')
                  transf_si = str_ind.fit(new_df)
                  
                  new_df = transf_si.transform(new_df).drop(c).withColumnRenamed(c+'_num', c)
              return new_df
          
          
          cat_col = ["workclass", "education","marital_status", "occupation", "relationship", "race", "sex",  "native_country", "income"]
          
          df_num = stringindex_trans(dfraw, cat_col)
          ```
  
        * `OneHortEncoding `
  
          ```python
          from pyspark.ml.feature import OneHotEncoder
          
          def onehotenc_trans(df, cols):
              new_df = df
              for c in cols:
                  # dropLast : Whether to drop the last category in the encoded vector (default: true)
                  one_hot = OneHotEncoder(inputCol=c, outputCol=c+'_onehot', dropLast=False)
                  transf_ohe = one_hot.fit(new_df)
          
                  new_df = transf_ohe.transform(new_df).drop(c).withColumnRenamed(c+'_onehot', c)
              return new_df
          
          
          onehot_col = ["workclass", "education", "marital_status", "occupation", "relationship", "race",  "native_country"]
          
          df_onehot = onehotenc_trans(df_num, onehot_col)
          ```
  
        * `VectorAssembler`: transformer
  
          ```python
          # Merging the data with Vector Assembler.
          from pyspark.ml.feature import VectorAssembler
          input_cols=["age","capital_gain","capital_loss","fnlwgt","hours_per_week","sex","workclass","education","marital_status","occupation","relationship","native_country","race"]
          
          v_ass = VectorAssembler(outputCol='features', inputCols=input_cols)
          
          labeled_df = v_ass.transform(df_onehot).selectExpr('features','income label')
          ```
  
    * step2: ML algorithm
  
      * train val split`Split(0.8, 0.2)`
  
        ```python
        splits = labeled_df.randomSplit([0.8, 0.2])
        
        adulttrain = splits[0].cache()
        adultvalid = splits[1].cache()
        
        adulttrain.write.saveAsTable("adulttrain")
        adultvalid.write.saveAsTable("adultvalid")
        ```
  
      * model: algorithm is just a estimator, it create a transformer (data in data with prediction out)
  
        ```python
        from pyspark.ml.classification import LogisticRegression
        
        lr = LogisticRegression(regParam=0.01, maxIter=1000, fitIntercept=True)# now is just an estimator
        lrmodel = lr.fit(adulttrain) # after fitting, it is a transformer now
        
        validpredicts = lrmodel.transform(adultvalid) # transformer returns a data frame
        validpredicts.show()
        ```
        
        * return df with `probability` and `predict`
  
    * step3: Hyperparameter
  
      * cross validation
  
        ```python
        from pyspark.ml.tuning import CrossValidator
        from pyspark.ml.tuning import ParamGridBuilder
        cv = CrossValidator().setEstimator(lr).setEvaluator(bceval).setNumFolds(5)
        
        #ParamGridBuilder() – combinations of parameters and their values.
        paramGrid = ParamGridBuilder().addGrid(lr.maxIter, [1000]).addGrid(lr.regParam, [0.0001, 0.001, 0.005, 0.01, 0.05, 0.1, 0.5]).build()
        
        #setEstimatorParamMaps() takes ParamGridBuilder().
        cv.setEstimatorParamMaps(paramGrid)
        cvmodel = cv.fit(adulttrain)
        
        BinaryClassificationEvaluator().setMetricName("areaUnderPR").evaluate(cvmodel.bestModel.transform(adultvalid))
        ```
  
        
  
    * Step4: evaluate the model
  
      ```python
      #Evaluate the model. default metric : Area Under ROC
      from pyspark.ml.evaluation import BinaryClassificationEvaluator
      bceval = BinaryClassificationEvaluator().setMetricName('f1')
      print (bceval.getMetricName() +":" + str(bceval.evaluate(validpredicts)))
      
      #Evaluate the model. metric : Area Under PR
      bceval.setMetricName("areaUnderPR")
      print (bceval.getMetricName() +":" + str(bceval.evaluate(validpredicts)))
      ```
  
  * Pipeline: 
  
    ```python
    va = VectorAssembler(inputCols=dfpen.columns[:-1], outputCol="features") 
    dt = DecisionTreeClassifier()
    
    pipeline = Pipeline(stages=[va,dt])
    ```
  
  * Decision Tree
  
  * Random Forest
  
  * K-Mean Clustering

# Part ?

* H2O and Sparkling Water 
  * what? Fast, scalable, open-source machine learning and deep learning library
    * Fast: in memory
    * Scalable: spark(Local, Standalone, YARN)
      * H2O services on each node of a Spark cluster, copy and compress data in to certain format
    * ML libraries: linear reg, logistic reg, NaiveB..
    * language: R, python, scala,

* Sparkling Water Workflow

  * Create H2OFrame 

    ```sql
    -- create from spark DataFrame
    hc.asH2OFrame(dataframe, "name", ...)
    
    -- create from file
    import h2o
    h2oframe = h2o.import_file(path="path", ...)
    h2oframe.set_names(["col_1", ... , "col_n"])
    
    -- convert to spark DataFrame
    df = hc.asSparkFrame(h2o_frame)
    ```

    ```python
    splitted_adult = adult_h2o.split_frame([0.8], seed=1)
    adult_train_h2o = splitted_adult[0]
    adult_valid_h2o =  splitted_adult[1]
    
    from h2o.automl import H2OAutoML
    model_automl = H2OAutoML(max_models=10, max_runtime_secs=120, seed=1, nfolds=5)
    
    predictors = adult_train_h2o.names[:]
    response = "income"
    predictors.remove(response)
    
    model_automl.train(x=predictors, y=response, training_frame=adult_train_h2o)
    ```

    ```python
    df.summary()
    df.describe()
    df.shape()
    df.columns()
    df.types()
    df.columns_by_type(coltype='numeric')
    df.set_name(col=index/name, name=new_name)
    df.set_names(names)
    df.split_frame(ratios=None,destination_frames=None)
    df.asfactor()
    df.ascharacter()
    df.as_date(format)
    df.asnumeric()
    
    ```

    

  * Apply ML Algorithms

    * AutoML





```python
from pyspark.sql import SparkSession
from pyspark.sql.types import *

ss = SparkSession.builder.getOrCreate()
sc = ss.sparkContext

#load and convert the data
census_raw = sc.textFile("../Data/adult.raw", 4)\		
								.map(lambda x:  x.split(", "))
census_raw = census_raw.map(lambda row: [toDoubleSafe(x) for x in row])

from pyspark.sql.types import *
adultschema = StructType([
    StructField("age",DoubleType(),True),
    StructField("workclass",StringType(),True),
    StructField("fnlwgt",DoubleType(),True),
    StructField("education",StringType(),True),
    StructField("marital_status",StringType(),True),
    StructField("occupation",StringType(),True),
    StructField("relationship",StringType(),True),
    StructField("race",StringType(),True),
    StructField("sex",StringType(),True),
    StructField("capital_gain",DoubleType(),True),
    StructField("capital_loss",DoubleType(),True),
    StructField("hours_per_week",DoubleType(),True),
    StructField("native_country",StringType(),True),
    StructField("income",StringType(),True)
])

dfraw = ss.createDataFrame(census_raw, adultschema)

```

```python
# clear data

#Check the most commonly used vals.
dfraw.groupBy(dfraw["workclass"]).count()\
		.orderBy("count",ascending=False).show()
dfraw.groupBy(dfraw["occupation"]).count()\
		.orderBy("count",ascending=False).show()
dfraw.groupBy(dfraw["native_country"]).count()\
		.orderBy("count",ascending=False).show()

#Missing data imputation - Impute the most common row for "?".
dfrawrp = dfraw.replace(["?"], ["Private"], ["workclass"])
dfrawrpl = dfrawrp.replace(["?"], ["Prof-specialty"], ["occupation"])
dfrawnona = dfrawrpl.replace(["?"], ["United-States"], ["native_country"])
```

```python
# converting strings to numeric values
from pyspark.ml.feature import StringIndexer

def indexStringColumns(df, cols):
    # variable newdf will be updated several times
    newdf = df
    
    for c in cols:
        # For each given colum, fits StringIndexerModel.
        si = StringIndexer(inputCol=c, outputCol=c+"-num")
        sm = si.fit(newdf)
        
        # Creates a DataFame by putting the transformed values in the new colum with suffix "-num" 
        # and then drops the original columns.
        # and drop the "-num" suffix. 
        newdf = sm.transform(newdf).drop(c)
        newdf = newdf.withColumnRenamed(c+"-num", c)
    return newdf

dfnumeric = indexStringColumns(dfrawnona, ["workclass", "education",
                                           "marital_status", "occupation",
                                           "relationship", "race", "sex", 
                                           "native_country", "income"])
```

```python
from pyspark.ml.feature import OneHotEncoder

def oneHotEncodeColumns(df, cols):
    newdf = df
    for c in cols:
        # For each given colum, create OneHotEncoder. 
        # dropLast : Whether to drop the last category in the encoded vector (default: true)
        ohe = OneHotEncoder(inputCol=c, outputCol=c+"-onehot", dropLast=False)
        ohe_model = ohe.fit(newdf)
        #Creates a DataFame by putting the transformed values in the new colum with suffix "-onehot" 
        #and then drops the original columns.
        #and drop the "-onehot" suffix. 
        newdf = ohe_model.transform(newdf).drop(c)
        newdf = newdf.withColumnRenamed(c+"-onehot", c)
    return newdf

dfhot = oneHotEncodeColumns(dfnumeric, ["workclass", "education", 
                                        "marital_status", "occupation", 
                                        "relationship", "race", "native_country"])        
```

```python
# Merging the data with Vector Assembler.
from pyspark.ml.feature import VectorAssembler
input_cols=["age","capital_gain","capital_loss",
            "fnlwgt","hours_per_week","sex",
            "workclass","education","marital_status",
            "occupation","relationship","native_country","race"]

#VectorAssembler takes a number of collumn names(inputCols) and output column name (outputCol)
#and transforms a DataFrame to assemble the values in inputCols into one single vector with outputCol.

va = VectorAssembler(outputCol="features", 
                     inputCols=input_cols)
#lpoints - labeled data.
lpoints = va.transform(dfhot)
						.select("features", "income")
  					.withColumnRenamed("income", "label")
```

```python
# Fit the pipeline to training documents.
from pyspark.ml import Pipeline
pipeline = Pipeline(stages=[va,dt])
```



```python
#Divide the dataset into training and vaildation sets.
splits = lpoints.randomSplit([0.8, 0.2])

#cache() : the algorithm is interative and training and data sets are going to be reused many times.
adulttrain = splits[0].cache()
adultvalid = splits[1].cache()
```

```python
#Train the model.

##### logistic regression #####
from pyspark.ml.classification import LogisticRegression

lr = LogisticRegression(regParam=0.01, 
                        maxIter=1000, 
                        fitIntercept=True)
lrmodel = lr.fit(adulttrain)
#The above lines are same as..
#lr = LogisticRegression()
#lrmodel = lr.setParams(regParam=0.01, maxIter=1000, fitIntercept=True).fit(adulttrain)

#Interpret the model parameters
print(lrmodel.coefficients)
print(lrmodel.intercept)

#Evaluate models using test dataset.
#First, transform the validation set.
validpredicts = lrmodel.transform(adultvalid)
validpredicts.show()
################################

##### decision tree #####
# Train the data.
from pyspark.ml.classification import DecisionTreeClassifier
# Paramenters
# maxDepth : maximum tree depth (default : 5).
# maxBins : maximum number of bins when binning continuous features (default : 32).
# minInstancesPerNode : minimum number of dataset samples 
#     each branch needs to have after a split (default : 1).
# minInfoGain : minimum information gain for a split (default : 0).
dt = DecisionTreeClassifier(maxDepth=20, maxBins= 32, minInstancesPerNode=1, minInfoGain = 0)
dtmodel = dt.fit(pendttrain)
print(dtmodel.toDebugString)
dtpredicts = dtmodel.transform(pendtvalid)
################################

##### random forest #####
# Train the model.
from pyspark.ml.classification import RandomForestClassifier
rf = RandomForestClassifier(maxDepth=20)
rfmodel = rf.fit(pendttrain)
print(rfmodel.toDebugString)
rfpredicts = rfmodel.transform(pendtvalid)
################################

##### kmeans #####
kmeans =  KMeans(k = 10, maxIter = 200, tol = 0.1) 
# k = 10 as there are 10 different handwritten numbers.
model = kmeans.fit(penlpoints)
predictions = model.transform(penlpoints)
centers = model.clusterCenters()
print("Cluster Centers: ")
for center in centers:
    print(center)
    
from pyspark.ml.evaluation import ClusteringEvaluator
evaluator = ClusteringEvaluator()
silhouette = evaluator.evaluate(predictions)
print("Silhouette with squared euclidean distance = " + str(silhouette))

# prediction is a group, not an actual label.
predictions.select('label', 'prediction')\
           .groupBy('label', 'prediction')\
           .count()\
           .show(100)
```

```python
#Evaluate the model. default metric : Area Under ROC
####### Binary #######
from pyspark.ml.evaluation import BinaryClassificationEvaluator

bceval = BinaryClassificationEvaluator()
print (bceval.getMetricName() +":" + 
       str(bceval.evaluate(validpredicts)))

#Evaluate the model. metric : Area Under PR
bceval.setMetricName("areaUnderPR")
print (bceval.getMetricName() +":" + 
       str(bceval.evaluate(validpredicts)))

####### multiclass #######
from pyspark.ml.evaluation import MulticlassClassificationEvaluator
# expects two input columns: prediction and label.

# f1|accuracy(defulat)|weightedPrecision|weightedRecall|
# weightedTruePositiveRate| 
# weightedFalsePositiveRate|weightedFMeasure|truePositiveRateByLabel| 
# falsePositiveRateByLabel|precisionByLabel|recallByLabel|fMeasureByLabel| 
# logLoss|hammingLoss
metric_name = "f1"

metrics = MulticlassClassificationEvaluator()\
                .setLabelCol("label")\
                .setPredictionCol("prediction")
metrics.setMetricName(metric_name) 

metrics.evaluate(dtpredicts)
```

```python
from pyspark.ml.tuning import CrossValidator
from pyspark.ml.tuning import ParamGridBuilder

#cv = CrossValidator().setEstimator(lr)
#											.setEvaluator(bceval)# what used for one time metric
#  										.setNumFolds(5)

#ParamGridBuilder() – combinations of parameters and their values.
paramGrid = ParamGridBuilder().addGrid(lr.maxIter, [1000])\
															.addGrid(lr.regParam, 
                                       [0.0001, 0.001, 0.005, 
                                        0.01, 0.05, 0.1, 0.5])\
  														.build()
    
evaluator = MulticlassClassificationEvaluator()\
                .setLabelCol("label")\
                .setPredictionCol("prediction")
    
#setEstimatorParamMaps() takes ParamGridBuilder().
cv = CrossValidator(estimator=dt, 
                    evaluator=evaluator, 
                    numFolds=5, 
                    estimatorParamMaps=paramGrid)

cvmodel = cv.fit(adulttrain)

print(cvmodel.bestModel.coefficients)
print(cvmodel.bestModel.intercept)
print(cvmodel.bestModel.getMaxIter())
print(cvmodel.bestModel.getRegParam(),
      cvmodel.bestModel.getMaxDepth)

dtpredicts = cvmodel.bestModel.transform(pendtvalid)

```

```python
BinaryClassificationEvaluator().evaluate(
												cvmodel.bestModel\
												.transform(adultvalid))


BinaryClassificationEvaluator().setMetricName("areaUnderPR")\
															.evaluate(cvmodel.bestModel.\
                                        transform(adultvalid))
```

```python
from pyspark.mllib.evaluation import MulticlassMetrics

#prediction and label
prediction_label = dtpredicts.select("prediction", "label").rdd

metrics = MulticlassMetrics(prediction_label)

confusionMetrics = metrics.confusionMatrix()

print("Confusion Metrics = \n%s" % confusionMetrics)
```



