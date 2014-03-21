---
layout: post
title: "Best practices to improve performance in JDBC"
date: 2013-10-31 14:54:53 +0800
comments: true
tags: Java

---
The 1st version is completed on 2013.11.1 20:38.

The 2nd version is completed on 2013.11.22, which modified completely, the main content is from ***[Best practices to improve performance in JDBC](http://www.precisejava.com/javaperf/j2ee/JDBC.htm)***, this is a great article about JDBC tuning.

This post is dedicated to illustrate techniques for optimizing JDBC API-based calls from the Java platform. As a result of this presentation, you will:

* Design better JDBC implementations
* Recognize potential performance bottlenecks
* Reduce cost during development

<!-- more -->

##Agenda##
1. [Why optimize?](#why)
1. [Basic Steps to Write a JDBC Program.](#steps)
	1. [Choosing the right Driver](#driver)
	1. [Optimization with Connection](#connection)
	1. [Optimization with Statement](#statement)
	1. [Optimization with ResultSet](#result_set)
	1. [Optimization with SQL Query](#sql_query)
1. [Other Means of Optimization.](#other_means)
1. [Key Points](#key_points)

###<a name='why'></a>Why Optimize?##

* On average, a web request performs 4 database queries.
* Experience has shown that database calls are typical performance bottleneck.
* Bad JDBC can overwhelm the database.

###<a name="steps"></a>Basic Steps to Write a JDBC Program

The following are the basic steps to write a JDBC program

1. Import java.sql and javax.sql packages
2. Load JDBC driver
3. Establish connection to the database using Connection interface
4. Create a Statement by passing SQL query
5. Execute the Statement
6. Retrieve results by using ResultSet interface
7. Close Statement and Connection


We will look at these areas one by one, what type of driver you need to load, how to use Connection interface in the best manner, how to use different Statement interfaces, how to process results using ResultSet and finally how to optimize SQL queries to improve JDBC performance.

####<a name="driver"></a>Choosing the right Driver

*Type* | *Tier* | *Driver mechanism* | *Description*
:----------:|:------------:|:---------- |:--------- 
1 |2  |JDBC-ODBC	|This driver converts JDBC calls to ODBC calls through JDBC-ODBC Bridge driver which in turn converts to database calls. Client requires ODBC libraries.
2 |2  |Native API - Partly - Java driver|This driver converts JDBC calls to database specific native calls. Client requires database specific libraries.
3 |3  |JDBC - Net -All Java driver|This driver passes calls to proxy server through network protocol which in turn converts to database calls and passes through database specific protocol. Client doesn't require any driver.
4 |2  |Native protocol - All - Java driver|This driver directly calls database. Client doesn't require any driver.


Type 3 & 4 drivers are faster than other drivers because Type 3 gives facility for optimization techniques provided by application server such as connection pooling, caching, load balancing etc and Type 4 driver need not translate database calls to ODBC or native connectivity interface. Type 1 drivers are slow because they have to convert JDBC calls to ODBC through JDBC-ODBC Bridge driver initially and then ODBC Driver converts them into database specific calls. Type 2 drivers give average performance when compared to Type 3 & 4 drivers because the database calls have to be converted into database specific calls. Type 2 drivers give better performance than Type 1 drivers.


####<a name="connection"></a>Optimization with Connection

We have different approaches to establish a connection with the database, the **first** type of approach is :

>1. DriverManager.getConnection(String url)
2. DriverManager.getConnection(String url, Properties props)
3. DriverManager.getConnection(String url, String user, String password)
4. Driver.connect(String url, Properties props)

When you use this approach, you can pass database specific information to the database by passing properties using Properties object. The following code snippet illustrates this approach for MySQL.
    Properties props = new Properties();
    FileInputStream in = new FileInputStream("/external/configuration/dir/db.properties");
    props.load(in);
    in.close();
    Connection con = DriverManger.getConnection("jdbc:mysql://localhost:3306/test", props);

The db.properties is quite like this:
>drivername=com.mysql.jdbc.Driver
url=jdbc:mysql://127.0.0.1:3306/test
username=root
password=123456

You can add extra properties for better performance depending on application's requirement, of course, you need to figure out if the database support such configuration first. 

The **second** type of approach is to get connection from DataSource(not tested yet). You can get the connection using javax.sql.DataSource interface. The advantage of getting connection from this approach is that the DataSource works with JNDI. The implementation of DataSource is done by vendor, for example you can find this feature in weblogic, websphere etc. The vendor simply creates DataSource implementation class and binds it to the JNDI tree. The following code shows how a vendor creates implementation class and binds it to JNDI tree.
    DataSourceImpl dsi = new DataSourceImpl();
    dsi.setServerName("oracle8i");
    dsi.setDatabaseName("Demo");
    Context ctx = new InitialContext();
    ctx.bind("jdbc/demoDB", dsi);
This code registers the DataSourceImpl object to the JNDI tree, then the programmer can get the DataSource reference from JNDI tree without knowledge of the underlying technology.
    Context ctx = new InitialContext();
    DataSource ds = (DataSource)ctx.lookup("jdbc/demoDB");
    Connection con = ds.getConnection();
By using this approach we can improve performance. Nearly all major vendor application servers like weblogic, webshpere implement the DataSource by taking connection from connection pool rather than a single connection every time. The application server creates connection pool by default. We will discuss the advantage of connection pool to improve performance in the next section.


#####Use Connection Pool

Creating a connection to the database server is expensive. It is even more expensive if the server is located on another machine. Connection pool contains a number of open database connections with minimum and maximum connections, that means the connection pool has open connections between minimum and maximum number that you specify. The pool expands and shrinks between minimum and maximum size depending on incremental capacity. You need to give minimum, maximum and incremental sizes as properties to the pool in order to maintain that functionality. You get the connection from the pool rather directly .For example, if you give properties like min, max and incremental sizes as 3, 10 and 1 then pool is created with size 3 initially and if it reaches it's capacity 3 and if a client requests a connection concurrently, it increments its capacity by 1 till it reaches 10 and later on it puts all its clients in a queue. 

There are a few choices when using connection pool.

1. You can depend on application server if it supports this feature, generally all the application servers support connection pools. Application server creates the connection pool on behalf of you when it starts. You need to give properties like min, max and incremental sizes to the application server.
1. You  can use JDBC 2.0 interfaces, ConnectionPoolDataSource and PooledConnection if your driver implements these interfaces
1. Or  you can create your own connection pool if you are not using any application server or JDBC 2.0  compatible driver.

By using any of these options, you can increase performance significantly. You need to take care of properties like min, max and incremental sizes. The maximum number of connections to be given depends on your application's  requirement that means how many concurrent clients can access  your database and also it depends up on your database's capability to provide maximum number of connections.

See more about the ***[JDBC connection pool benchmarks](http://jolbox.com/index.html?page=http://jolbox.com/benchmarks.html)***

##### Control transaction

In general, transaction represents one unit of work or bunch of code in the program that executes in it's entirety or none at all. To be precise, it is all or no work. In JDBC, transaction is a set of one or more Statements that execute as a single unit.
java.sql.Connection interface provides some methods to control transaction they are
    public interface Connection {
    	boolean getAutoCommit();
    	void        setAutoCommit(boolean autocommit);
    	void        commit();
    	void        rollback();
    }

By default in JDBC transaction starts and commits after each statement's  execution on a connection. That is the AutoCommit mode is true. Programmer need not write a commit() method explicitly after each statement.Obviously this default mechanism gives good facility for programmers if they want to execute a single statement. But it gives poor performance when multiple statements on a connection are to be executed because commit is issued after each statement by default, that in turn reduces performance by issuing unnecessary commits. The remedy is to flip it back to AutoCommit mode as false and issue commit() method after a set of statements execute, this is called as **batch transaction**. Use rollback() in catch block to rollback the transaction whenever an exception occurs in your program. See more about [JDBC - Transactions](http://www.tutorialspoint.com/jdbc/jdbc-transactions.htm)

#####Choose optimal isolation level

Isolation level represent how a database maintains data integrity against the problems like dirty reads, phantom reads and non-repeatable reads which can occur due to concurrent transactions. java.sql.Connection interface provides  methods and constants to avoid the above mentioned problems by setting different isolation levels.
    public interface Connection {
        public static final int  TRANSACTION_NONE                 = 0
        public static final int  TRANSACTION_READ_UNCOMMITTED     = 1
        public static final int  TRANSACTION_READ_COMMITTED       = 2
        public static final int  TRANSACTION_REPEATABLE_READ      = 4
        public static final int  TRANSACTION_SERIALIZABLE         = 8
        int    getTransactionIsolation();
        void   setTransactionIsolation(int isolationlevelconstant);
    }

More details are covered in ***[Optimal JDBC Transaction Isolation Level](/blog/2013/10/11/optimal-isolation-level/) and [Optimistic and Pessimistic Locking]***(/blog/2013/10/21/optimistic-and-pessimistic-locking/)

####Optimization with Statement

Statement interface represents SQL query and execution and they provide number of methods and constants to work with queries. They also provide some methods to fine tune performance. Programmer may overlook these fine tuning methods that result in poor performance. The following are the tips to improve performance by using statement interfaces

1. Choose the right Statement interface
1. Do batch update
1. Do batch retrieval using Statement
1. Close Statement when finished

#####1. Choose right Statement interface

There are three types of Statement interfaces in JDBC to represent the SQL query and execute that query, they are Statement, PreparedStatement and CallableStatement.

1. Statement is used for static SQL statement with no input and output parameters, PreparedStatement is used for dynamic SQL statement with input parameters and CallableStatement is used for dynamic SQL satement with both input and output parameters, but PreparedStatement and CallableStatement can be used for static SQL statements as well. CallableStatement is mainly meant for stored procedures.

1. PreparedStatement gives better performance when compared to Statement because it is pre-parsed and pre-compiled by the database once for the first time and then onwards it reuses the parsed and compiled statement. Because of this feature, it significantly improves performance when a statement executes repeatedly, It reduces the overload incurred by parsing and compiling.

1. CallableStatement gives better performance when compared to PreparedStatement and Statement when there is a requirement for single request to process multiple complex statements. It parses and stores the stored procedures in the database and does all the work at database itself that in turn improves performance. But we loose java portability and we have to depend up on database specific stored procedures.

#####2. Do batch update

You can send multiple queries to the database at a time using batch update feature of statement objects this reduces the number of JDBC calls and improves performance. Here is an example of how you can do batch update,
	statement.addBatch( "sql query1");
	statement.addBatch(" sql query2");
	statement.addBatch(" sql query3");
	statement.executeBatch();
**All** three types of statements have these methods to do batch update.

#####3. Do batch retrieval using Statement

You can get the default number of rows that is provided by the driver. You can improve performance by increasing number of rows to be fetched at a time from database using `setFetchSize()` method of the statement object.Initially find the default size by using `Statement.getFetchSize();`   and then set the size as per your requirement `Statement.setFetchSize(30);`Here it retrieves 30 rows at a time for all result sets of this statement.

#####4. Close Statement when finished

Close statement object as soon as you finish working with that, it explicitly gives a chance to garbage collector to recollect memory as early as possible which in turn effects performance. Statement.close();

####Optimization with ResultSet

ResultSet interface represents data that contains the results of executing an SQL Query and it provides a number of methods and constants to work with that data. It also provides methods to fine tune retrieval of data to improve performance. The following are the fine tuning tips to improve performance by using ResultSet interface.
1. Do batch retrieval using ResultSet
2. Set up proper direction for processing the rows
3. Use proper get methods
4. Close ResultSet when finished

#####Do batch retrieval using ResultSet

ResultSet interface also provides batch retrieval facility like Statement as mentioned above. It overrides the Statement behaviour. Initially find the default size by using
`ResultSet.getFetchSize();` and then set the size as per requirement `ResultSet.setFetchSize(50);` This feature significantly improves performance when you are dealing with retrieval of large number of rows like search functionality.

#####Setup proper direction of processing rows

ResultSet has the capability of setting the direction in which you want to process the results, it has three constants for this purpose, they are `FETCH_FORWARD`, `FETCH_REVERSE`, `FETCH_UNKNOWN`. Initially find the direction by using `ResultSet.getFetchDirection();` and then set the direction accordingly `ResultSet.setFetchDirection(FETCH_REVERSE);`

#####Use proper `getxxx()` methods

ResultSet interface provides lot of getxxx() methods to get and convert database data types to java data types and is flexibile in converting non feasible data types. For example, `getString(String columnName)` returns java String object. `columnName` is recommended to be a `VARCHAR` or `CHAR` type of database but it can also be a `NUMERIC`, `DATE` etc.If you give non recommended parameters, it needs to cast it to proper java data type that is  expensive. For example consider that  you select  a product's id from huge database which returns millions of records from search functionality, it needs to convert all these records that is very expensive.
So always use proper getxxx() methods according to JDBC recommendations.

#####Close ResultSet when finished

Close ResultSet object as soon as you finish working with ResultSet object even though Statement object closes the ResultSet object implicitly when it closes, closing ResultSet explicitly gives chance to garbage collector to recollect memory as early as possible because ResultSet object may occupy lot of memory depending on query.
`ResultSet.close();`

See more details about ***[JDBC: ResultSet](http://tutorials.jenkov.com/jdbc/resultset.html)***

####Optimization with SQL Query

This is one of the area where programmers generally make a mistake. If you give a query like
	Statement stmt = connection.createStatement();
	ResultSet rs = stmt.executeQuery("select * from employee where name=RR");
The returned result set contains all the columns data. you may not need all the column data and want **only** salary for RR.
The better query is `"select salary from employee where name=RR"`
It returns the required data  and reduces unnecessary data retrieval. Make your query specific.

Another smart query ex: get employees in ENG dept
Instead of: 
	SELECT * FROM employees;
	SELECT * FROM dept;
(and joining on Java application side)
Use database join:
	SELECT employees.* FROM employees E, 
	dept D WHERE E.DEPTNO = D.DEPTNO AND 
	D.DEPTTYPE = 'ENG';

#####Smart Query Guidelines

* Make queries as specific as possible
* Put more logic into SQL statements
* DB are designed to use SQL efficiently
* Use DB for filtering
* Use Java for business logic,DB business logic is poor(At least very inconsistent between database vendors.)
* DB does filtering very well

###<a name='other_means'></a>Other Means of Optimization.

There are also some other techniques you can rely on, for example, caching, iteratively fetching, stored procedures, database table sharding.

####Cache the read-only and read-mostly data

Every database schema generally has read-only and read-mostly tables. These tables are called as lookup tables. Read-only tables contain static data that never changes in its life time. Read-mostly tables contain semi dynamic data that changes often. There will not be any sort of writing operations in these tables.
If an application reads data from these tables for every client request, then it is redundant, unnecessary and expensive. The solution for this problem is to cache the read-only table data by reading the data from that table once and caching the read-mostly table data by reading and refreshing with time limit. This solution improves performance significantly.

You can tweak this code as per application requirement. For read-only data, you need not refresh data in its life time. For read-mostly data, you need to refresh the data with time limit. It is better to set this refreshing time limit in properties file so that it can be changed at any time.

####Fetch small amount of data iteratively instead of fetching whole data at once

Applications generally require to retrieve huge data from the database using JDBC in operations  like searching data. If the client request for a search, the application might return the whole result set at once. This process takes lot of time and has an impact on performance. The solution for the problem is

1. Cache the search data at the server-side and return the data iteratively to the client. For example, the search returns 1000 records, return data to the client in 10 iterations where each iteration has 100 records.
2. Use Stored procedures to return data iteratively. This does not use server-side caching rather server-side application uses Stored procedures to return small amount of data iteratively.

Out of these solutions the second solution gives better performance because it need not keep the data in the cache (in-memory). The first procedure is useful when the total amount of data to be returned is not huge.

####Keep operational data set small as possible 

* Move non-current data to other tables and do joins for rarer historical queries
* Otherwise, index and cluster so frequently used data is logically and physically localized

###<a name='key_points'></a>Key Points

* Use Type two driver for two tiered applications to communicate from java client to database that gives better performance than Type1 driver.
* Use Type four driver for applet to database communication that is two tiered applications and three tiered applications when compared to other drivers.
* Use Type one driver if you don't have a driver for your database. This is a rare situation because all major databases support drivers or you will get a driver from third party vendors.
* Use Type three driver to communicate between client and proxy server ( weblogic, websphere etc) for three tiered applications that gives better performance when compared to Type 1&2 drivers.
* Pass database specific properties like defaultPrefetch if your database supports any of them.
* Get database connection from connection pool rather than getting it directly
* Use batch transactions.
* Choose right isolation level as per your requirement. `TRANSACTION_READ_UNCOMMITED` gives best performance for concurrent transaction based applications. `TRANSACTION_NONE` gives best performance for non-concurrent transaction based applications. Also, Have a good understanding of optimistic and pessimistic locking since such techniques definitely improve the robustness of your program.
* Your database server may not support all isolation levels, be aware of your database server features.
* Use PreparedStatement when you execute the same statement more than once.
* Use CallableStatement when you want result from multiple and complex statements for a single request.
* Use batch update facility available in Statements.
* Use batch retrieval facility available in Statements or ResultSet.
* Set up proper direction for processing rows.
* Use proper getXXX() methods.
* Close ResultSet, Statement and Connection whenever you finish your work with them.
* Write precise SQL queries, let the DB do the logic since it's designed for those stuffs.
* Cache read-only and read-mostly tables data, split database tables for efficiency purposes.
* Fetch small amount of data iteratively rather than whole data at once when retrieving large amount of data like searching database etc.


##References:
* [Performance Tuning for the JDBC API](http://alumnus.caltech.edu/~chamness/JDBC_Tuning.pdf)
* [Best practices to improve performance in JDBC](http://www.precisejava.com/javaperf/j2ee/JDBC.htm)(I love this most.)
* [Jdbc Driver Types](http://www.tutorialspoint.com/jdbc/jdbc-driver-types.htm)
* [JDBC Transactions](https://access.redhat.com/site/documentation/en-US/JBoss_Enterprise_Data_Services/5/html/Data_Services_Client_Developer_Guide/chap-JDBC_Transactions.html)
* [The JDBC Connection Pool Benchmarks](http://jolbox.com/index.html?page=http://jolbox.com/benchmarks.html)
* [JDBC - Transactions](http://www.tutorialspoint.com/jdbc/jdbc-transactions.htm)
* [WebSphere Application Server Development Best Practices for Performance and Scalability](http://www-01.ibm.com/software/webservers/appserv/ws_bestpractices.pdf)
* [What areas should I focus on for the best performance in a JDBC application?](http://www.jguru.com/faq/view.jsp?EID=131579)  
* [Develop a generic caching service to improve performance](http://www.javaworld.com/javaworld/jw-07-2001/jw-0720-cache.html)