# Spark SQL for Big Data Engineering

## SQL on Hadoop Parse and Optimization

SQL on Hadoop to use SQL on HDFS to analysis big data
  * Connector to Hadoop: Oracle SQL Connector to Hadoop HDFS and Teradata Connector to Hadoop
  * SQL and Hadoop: Hadapt, Rainstor, Citus Data, Splice Machine and PolyBase
  * SQL on Hadoop: Hive, SparkSQL, Impala, Presto, VectorH, Drill, HAWQ, IBM's Bid SQL 

How SQL query and parse on execution
1. Semantic parsed
2. Metadata binding
3. Optimizing executive strategy
4. Execute

Flink SQL
* SQL API: Calcite Catalog -> SQL Parser by Calcite & Validator -> Calcite Logical Plan ->  Calcite Optimizer -> Data Stream
* Table API: Calcite Catalog -> Table API Validator -> Calcite Logical Plan ->  Calcite Optimizer -> DataSet

Hive
1. Parser: transfer SQL to abstract syntax tree - AST
2. Semantic Analyzer: transfer AST to QB
3. Calcite Plan Generator (Logical Plan Generator): OP to RelNode
4. Logical Optimizer: optimize Logical Plan - OP Tree
5. Physical Plan Generator: Task Tree
6. Physical Optimizer: Task Tree

Calcite and SQL Parser
* Apache Calcite: SQL API and JDBC
* Pluggable Catalog, Avatica, Adapter, SQL Dialect
* Pig, Datalog, Morel
* parse SQL -> AST (SQLNode) -> Sanmetic Validator & Analyzed (RelNode - Logical Plan) -> Logical Plan Optimized -> Execute (Phycial Plan and Executiion)

SQL Optimization & Calcite
* Relational Algebra in SQL - RelNode
* Relational Expression - Logica Plan
* Rule Based Optimization (RBO) & Cost Based Optimization (CBO)
* Predicate Pushdown, Constant Folding, Column Purning
* RelOptPlanner, RBO - HepPlanner & Spark Catalyst
* Volcano/Cascade Optimizer

### Spark SQL

* Spark Architecture
  * Spark Core (RDD)
  * Spark SQL: Catalyst, SQL, DataFrame/DataSet
  * ML Pipeline, Structed Streaming, GraphFrames
  
 * Catalyst
   * SQL AST, DataFrame, DataSet
   * Unresolved Logical Plan, Catalog, Logical Plan, Optimized Logical Plan, Physical Plans, Cost Model -> RDDs
   * Catalyst: Analysis, Logical Optimization, Physical Planning -> Code Eeneration 

* SQL Parser and ANYLR
  * Another tool for language recongnition, Semantic Parser (Scanner, Lexer, Tokenizer) / Compiler, AST Tree Iterator
  * Spark Parser: ANTLR4 -> Tokens -> AST 
  * SQL text by SQL Parser -> Unresolved Logical Plan (AstBuilder -> LogcialPlan) -> Analyzer (Resolved Logical Plan with Catalog)
  * Data Binding - Catalog : Function resource  and Metadata (Database??? Table, View???Partition, and Function) such as SessionCatalog ( Temp Table, View???Partition, Hive Metasotre)
  * Optimizer: Rules Batches applyed -> Optimized LogicalPlan


### General steps for SQL optimization

1. Locate those SQL statements with low execution efficiency through slow check logs, etc.
2. Explain to analyze the execution plan of SQL

   * Need to focus on type, rows, filtered, extra.
   
4. show profile analysis
5. trace
6. Identify the problem and take appropriate action

**Type from top to bottom, the efficiency is getting higher and higher**

  * ALL - full table scan
  * index - index full scan
  * range - index range scan, commonly used <,<=,>=,between,in and other operations
  * ref - uses a non-unique index scan or a unique index prefix scan to return a single record, often in relational queries
  * eq_ref is similar to ref, the difference is that it uses a unique index and an associated query using the primary key
  * const/system For a single record, the system will treat other columns in the matching row as constants, such as primary key or unique index query
  * null - MySQL does not access any table or index and returns the result directly

Although the efficiency is getting higher and higher from top to bottom, according to the cost model, assuming there are two indexes `idx1(a, b, c)`, `idx2(a, c)`, the SQL is `select * from t where a = 1 and b in (1, 2) order by c`; if idx1 is used, then type is range, if idx2 is used, type is ref; when the number of rows to be scanned, idx2 is about 5 times more than idx1, idx1 will be used, Otherwise, idx2 will be used

**Extra**

  * Using filesort: MySQL requires an extra pass to figure out how to retrieve rows in sorted order. Sorting is done by browsing all rows according to the join type and saving the sort key and row pointer for all rows matching the WHERE clause. The keys are then sorted and rows are retrieved in sorted order.
  * Using temporary: The temporary table is used to save the intermediate results, the performance is particularly poor, and it needs to be optimized.
  * Using index: Indicates that a covering index (Coveing Index) is used in the corresponding select operation to avoid accessing the data rows of the table, and the efficiency is good! If using where appears at the same time, it means that the qualified data cannot be queried directly through index search.
  * Using index condition: The new ICP after MySQL 5.6, using index condtion is to use ICP (index pushdown) to filter data at the storage engine layer instead of at the service layer, and use the existing data of the index to reduce the return table The data.

**show profile analysis**

  * Understand the status of the thread of SQL execution and the time consumed.
  * The default is off, open the statement "set profiling = 1;"
  ```
  SHOW PROFILES ;
  SHOW PROFILE FOR QUERY  #{id};
  ```
  
**trace**

The trace analyzes how the optimizer chooses the execution plan, and the trace file can further understand why the coupon chooses the A execution plan but not the B execution plan.  

```
set optimizer_trace="enabled=on";
set optimizer_trace_max_mem_size=1000000;
select * from information_schema.optimizer_trace;
```

**Identify the problem and take appropriate action**

optimize index
Optimize SQL statements: modify SQL, IN query segmentation, time query segmentation, filter based on the last data
Use other implementation methods: ES, data warehouse, etc.
data fragmentation

Scenario Analysis

  * Case 1. Leftmost match
    *index*

    `KEY `idx_shopid_orderno` (`shop_id`,`order_no`)`
    `select * from _t where orderno='' `
    
    The query matching is matched from left to right. To use order_no to go to the index, the query condition must carry shop_id or index (shop_id, order_no) to change the order.
    
  * Case 2. Implicit conversion
    *index*
    `KEY `idx_mobile` (`mobile`)`
    `select * from _user where mobile=12345678901`
    
    Implicit conversion is equivalent to doing an operation on the index, which will invalidate the index. mobile is a character type and uses numbers. String matching should be used, otherwise MySQL will use implicit substitution, which will cause the index to fail.
    
  * Case 3. Large pagination
    *index*
    `KEY `idx_a_b_c` (`a`, `b`, `c`)`
    `select * from _t where a = 1 and b = 2 order by c desc limit 10000, 10;`
    
    For large paging scenarios, you can give priority to product optimization requirements. If there is no optimization, there are the following two optimization methods:  
    One is to pass the last data of the last time, that is, the above c, and then do "c < xxx" processing, but this generally requires changing the interface protocol, which is not necessarily feasible.
    The other is to use the delayed association method to reduce the SQL return to the table, but remember that the index needs to be completely covered to be effective. The SQL changes are as follows

  ```
  SELECT t1.*
  FROM _t t1,
 ( SELECT id FROM _t WHERE a = 1 AND b = 2 ORDER BY c DESC LIMIT 10000, 10 ) t2
  WHERE t1.id = t2.id;
 ```
 
 *  Case 4. in + order by
    *index*
    `KEY `idx_shopid_status_created` (`shop_id`, `order_status`, `created_at`)`
    ```
    SELECT *
    FROM _order
    WHERE shop_id = 1 AND order_status IN ( 1, 2, 3 )
    ORDER BY created_at DESC
    LIMIT 10
    ```
 
 The in query is searched by n*m at the bottom of MySQL, which is similar to union, but more efficient than union.

 When the in query performs cost calculation (cost = number of tuples * average value of IO), the number of tuples is obtained by querying the values contained in in, so this calculation process will be relatively slow, so MySQL set A critical value (eq_range_index_dive_limit) is set. After 5.6, the cost of the column will not participate in the calculation after this critical value is exceeded.

As a result, the execution plan selection will be inaccurate. The default is 200, that is, the in condition exceeds 200 data, which will cause problems in the cost calculation of in, which may cause the index selected by Mysql to be inaccurate.

The processing method can exchange the order before and after (order_status, created_at), and adjust the SQL to delay the association.

  * Case 5. Range queries are blocked, and subsequent fields cannot be indexed
    *index*
    `KEY `idx_shopid_created_status` (`shop_id`, `created_at`, `order_status`)`
    ```
    SELECT  *
    FROM _order
    WHERE shop_id = 1 AND created_at > '2021-01-01 00:00:00' AND order_status = 10
     ```
 
 Range queries also have "IN, between"
 
 *  Case 6, does not equal, does not contain fast searches that cannot use indexes. (ICP can be used)
    `select * from _order where shop_id=1 and order_status not in (1,2)`
    `select * from _order where shop_id=1 and order_status != 1`
    
    On indexes, avoid NOT, !=, <>, !<, !>, NOT EXISTS, NOT IN, NOT LIKE, etc.

 *  Case 7. The optimizer chooses not to use the index
    
    If the amount of data required to be accessed is small, the optimizer will still choose the auxiliary index, but when the accessed data accounts for a large part of the data in the entire table (usually about 20%), the optimizer will choose to search through the clustered index data.

    `select * from _order where  order_status = 1`
    
    Query all unpaid orders. Generally, there are very few such orders. Even if an index is built, the index cannot be used.
    
 * Case 8. Complex query
  `select sum(amt) from _t where a = 1 and b in (1, 2, 3) and c > '2020-01-01';`

  `select * from _t where a = 1 and b in (1, 2, 3) and c > '2020-01-01' limit 10;`
  
    If it is to count some data, it may be solved by using a data warehouse;

    If there is such a complex query in the business, it may not be recommended to continue to use SQL, but to solve it in other ways, such as using ES to solve it.

 *  Case 9, mixed use of asc and desc

    `select * from _t where a=1 order by b desc, c asc`
    
    When desc and asc are mixed, the index will be invalid

* Case 10. Big data

`For the data storage of the push business, the amount of data may be very large. If you choose a plan, you will finally choose to store it on MySQL, and save it with a validity period of 7 days.

Then it should be noted that frequent data cleaning will cause data fragmentation, and you need to contact DBA for data fragmentation processing.

### SQL statement optimization

1. Create an index in the table, giving priority to the fields used by where group by.
2. Try to avoid using select * when querying, and only query the fields that need to be used.
3. Avoid using fuzzy queries with % on both sides of keywords in the where clause, and try to use fuzzy queries after keywords.
4. Try to avoid using IN and NOT IN in where clauses: Optimization: You can use between but not in; Using exists clauses in subqueries.
5. Try to avoid using or, optimization: you can use union instead of or.
6. Try to avoid using expression operations in where clauses.
7. Try to avoid using null judgments in where clauses, optimization: add default values to fields, and judge the default values.
8. Try not to perform expression and function operations on the left side of the equal sign in the where condition.
9. Try to avoid using where 1=1, optimization: use code to splicing sql, add where where you need where, and add where where you need and
10. Try to avoid large transaction operations to improve concurrency.
11. It is best not to have more than 6 indexes in a table.
12. You should try to avoid using != or <> in where clauses.
13. When using an index field as a condition, if the index is a composite index, the first one of the indexes must be used. The system can only use the index when the field is used as a condition, otherwise the index will not be used, and the order of the fields should be consistent with the order of the index as much as possible.
14. Update statement, if you only change 1 or 2 fields, do not update all fields, otherwise frequent calls will cause significant performance consumption and bring a lot of logs.
15. For table JOIN with a large amount of data (hundreds are considered large), it is necessary to divide the page before JOIN, otherwise the logical read will be very high and the performance will be poor.
16. Use numeric fields as much as possible, and try not to design character fields for fields that only contain numeric information, which will reduce query and connection performance and increase storage overhead. This is because the engine compares each character in the string one by one when processing queries and joins, whereas only one comparison is required for numeric types.
17. Try to avoid using cursors, because the efficiency of cursors is poor. If the data operated by cursors exceeds 10,000 rows, you should consider rewriting. A common use of cursors is to save query results for later use. The result set of the cursor is generated by the SELECT statement. If the processing process needs to use a recordset repeatedly, creating a cursor and using it several times is much faster than repeatedly querying the database.
18. Try to avoid returning a large amount of data to the client. If the amount of data is too large, you should consider whether the corresponding demand is reasonable.
19. When to create an index?
    * Tables are frequently SELECTed.
    * The table is very large (too many records), and the record content is widely distributed.
    * Column names often appear in WHERE clauses or join conditions.
When do you don't create an index?
    * Tables are frequently INSERT/UPDATE/DELETE operations.
    * The table is very small (very few records).
    * Column names do not often appear as join conditions or in WHERE clauses.
20. Index pros and cons:
    * Indexes speed up database retrieval.
    * Indexes slow down maintenance tasks such as inserts, deletes, and modifications.
    * The unique index can ensure the uniqueness of each row of data. By using the index, optimization hiders can be used in the query process to improve the performance of the system.
    * Indexes need to occupy physical and data space, and although indexes can improve query speed, they can also cause the performance of database systems to update data to degrade, because most data updates need to update indexes at the same time.
