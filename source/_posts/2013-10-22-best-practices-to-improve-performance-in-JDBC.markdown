---
layout: post
title: "Best practices to improve performance in JDBC"
date: 2013-10-31 14:54:53 +0800
comments: true
categories: coding
---
The 1st version is completed on 2013.10.22 20:38, the main content is from ***[Performance Tuning for the JDBC API](http://alumnus.caltech.edu/~chamness/JDBC_Tuning.pdf)***.

The 2nd version is completed on 2013.11.22 21:18, which modified completely, the main content is from ***[Best practices to improve performance in JDBC](http://www.precisejava.com/javaperf/j2ee/JDBC.htm)***, this is a great article about JDBC tuning.

This post is dedicated to illustrate techniques for optimizing JDBC API-based calls from the Java platform. As a result of this presentation, you will:

* Design better JDBC implementations
* Recognize potential performance bottlenecks
* Reduce cost during development

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

##<a name='why'></a>Why Optimize?##

* On average, a web request performs 4 database queries.
* Experience has shown that database calls are typical performance bottleneck.
* Bad JDBC can overwhelm the database.

<!-- more -->

##<a name="steps"></a>Basic Steps to Write a JDBC Program

The following are the basic steps to write a JDBC program

1. Import java.sql and javax.sql packages
2. Load JDBC driver
3. Establish connection to the database using Connection interface
4. Create a Statement by passing SQL query
5. Execute the Statement
6. Retrieve results by using ResultSet interface
7. Close Statement and Connection

We will look at these areas one by one, what type of driver you need to load, how to use Connection interface in the best manner, how to use different Statement interfaces, how to process results using ResultSet and finally how to optimize SQL queries to improve JDBC performance.

###<a name="driver"></a>Choosing the right Driver

*Type* | *Tier* | *Driver mechanism* | *Description*
:----------|:------------:|----------:
1 |2  |JDBC-ODBC	|This driver converts JDBC calls to ODBC calls through JDBC-ODBC Bridge driver which in turn converts to database calls. Client requires ODBC libraries.
2 |2  |Native API - Partly - Java driver|This driver converts JDBC calls to database specific native calls. Client requires database specific libraries.
3 |3  |JDBC - Net -All Java driver|This driver passes calls to proxy server through network protocol which in turn converts to database calls and passes through database specific protocol. Client doesn't require any driver.
4 |2  |Native protocol - All - Java driver|This driver directly calls database. Client doesn't require any driver.

Type 3 & 4 drivers are faster than other drivers because Type 3 gives facility for optimization techniques provided by application server such as connection pooling, caching, load balancing etc and Type 4 driver need not translate database calls to ODBC or native connectivity interface. Type 1 drivers are slow because they have to convert JDBC calls to ODBC through JDBC-ODBC Bridge driver initially and then ODBC Driver converts them into database specific calls. Type 2 drivers give average performance when compared to Type 3 & 4 drivers because the database calls have to be converted into database specific calls. Type 2 drivers give better performance than Type 1 drivers.

###<a name="connection"></a>Optimization with Connection

We have different approaches to establish a connection with the database, the first type of approach is :

1. DriverManager.getConnection(String url)
2. DriverManager.getConnection(String url, Properties props)
3. DriverManager.getConnection(String url, String user, String password)
4. Driver.connect(String url, Properties props)

When you use this approach, you can pass database specific information to the database by passing properties using Properties object to improve performance. For example, when you use oracle database you can pass default number of rows that must be pre-fetched from the database server and the default batch value that triggers an execution request. Oracle has default value as 10 for both properties. By increasing the value of these properties, you can reduce the number of database calls which in turn improves performance. The following code snippet illustrates this approach.
        java.util.Properties props = new java.util.Properties();
        props.put("user","scott");
        props.put("password","tiger");
        props.put("defaultRowPrefetch","30");
        props.put("defaultBatchValue","5");
        Connection con = DriverManger.getConnection("jdbc:oracle:thin:@hoststring", props);
You need to figure out appropriate values for above properties for better performance depending on application's requirement. Suppose, you want to set these properties for search facility, you can increase defaultRowPrefetch so that you can increase performance significantly.
The second type of approach is to get connection from DataSource.
You can get the connection using javax.sql.DataSource interface. The advantage of getting connection from this approach is that the DataSource works with JNDI. The implementation of DataSource is done by vendor, for example you can find this feature in weblogic, websphere etc. The vendor simply creates DataSource implementation class and binds it to the JNDI tree. The following code shows how a vendor creates implementation class and binds it to JNDI tree.
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

###Use Connection Pool
Creating a connection to the database server is expensive. It is even more expensive if the server is located on another machine. Connection pool contains a number of open database connections with minimum and maximum connections, that means the connection pool has open connections between minimum and maximum number that you specify. The pool expands and shrinks between minimum and maximum size depending on incremental capacity. You need to give minimum, maximum and incremental sizes as properties to the pool in order to maintain that functionality. You get the connection from the pool rather directly .For example, if you give properties like min, max and incremental sizes as 3, 10 and 1 then pool is created with size 3 initially and if it reaches it's capacity 3 and if a client requests a connection concurrently, it increments its capacity by 1 till it reaches 10 and later on it puts all its clients in a queue. 

In general, transaction represents one unit of work or bunch of code in the program that executes in it's entirety or none at all. To be precise, it is all or no work. In JDBC, transaction is a set of one or more Statements that execute as a single unit.
java.sql.Connection interface provides some methods to control transaction they are
        public interface Connection {
            boolean getAutoCommit();
            void        setAutoCommit(boolean autocommit);
            void        commit();
            void        rollback();
        }

####JDBC's default mechanism for transactions:

By default in JDBC transaction starts and commits after each statement's  execution on a connection. That is the AutoCommit mode is true. Programmer need not write a commit() method explicitly after each statement.Obviously this default mechanism gives good facility for programmers if they want to execute a single statement. But it gives poor performance when multiple statements on a connection are to be executed because commit is issued after each statement by default, that in turn reduces performance by issuing unnecessary commits. The remedy is to flip it back to AutoCommit mode as false and issue commit() method after a set of statements execute, this is called as batch transaction. Use rollback() in catch block to rollback the transaction whenever an exception occurs in your program. The following code illustrates the batch transaction approach.This batch transaction gives good performance by reducing commit calls after each statement's execution.

###Choose optimal isolation level
Isolation level represent how a database maintains data integrity against the problems like dirty reads, phantom reads and non-repeatable reads which can occur due to concurrent transactions. java.sql.Connection interface provides  methods and constants to avoid the above mentioned problems by setting different isolation levels.
public interface Connection {
        public static final int  TRANSACTION_NONE                             = 0
        public static final int  TRANSACTION_READ_COMMITTED         = 2
        public static final int  TRANSACTION_READ_UNCOMMITTED     = 1
        public static final int  TRANSACTION_REPEATABLE_READ       = 4
        public static final int  TRANSACTION_SERIALIZABLE                 = 8
        int          getTransactionIsolation();
        void        setTransactionIsolation(int isolationlevelconstant);
}

<a name='api_tech'></a>##Basic API techniques

![most_versatile_most_optimized](/images/upload/most_versatile_most_optimized.png)


###SQL Statements###

* Most flexible
* Least reliable
* Must be recompiled in database for each use

####PreparedStatement###

* Represents a precompiled SQL statement
* Can be used to efficiently execute statement multiple times
* Somewhat flexible \- can create new ones as needed


###CallableStatement###

* Used to execute SQL stored procedures. Use stored procedures when they can be created in a standard manner. Do watch out for DB specific SP definitions that can cause migration headaches.
* Same syntax as PreparedStatement.
* Least flexible.
* Most optimized DB call.


###Cache

Keep data within client to reduce the number of round-trips to the database.  
Lesson: The less JDBC the better.


##<a name='design_tech'></a>Basic Design Techniques

###Use Database Connection Pool

* Necessity for any production system.
* Don't use DriverManager.getConnection() often. JDBC connections can take 0.5 to 2 seconds to create.
* Create Pool of Connections and reuse them.


###Use multi-threading with Connection Pooling to address network latency

Threads can issue queries over separate database connections. This improves performance to a point.

	Single-batch Transactions
		BEGIN TRANSACTION
			UPDATE TABLE1... 
			INSERT INTO TABLE2...
			DELETE TABLE3
		COMMIT


###Single-batch Transactions---Significantly different effects!###

* DB obtains necessary locks on rows and tables, uses and releases them in one step
* Depending on transaction type, separate statements and commits can result in more DB calls and hold DB locks longer


###Don't have transaction span user input

Application sends BEGIN TRAN and SQL, locking rows or tables for update
Application waits for user to press key before committing transaction


####Solution: Optimistic locking####

1. Optimistic locking employs timestamps and triggers in queries and updates
1. Queries select data with timestamp value
1. Prepare a transaction based on that value, without locking data in a transaction
1. If that value changed, this update is invalid and you should query again


###Smart Queries##

* Make queries as specific as possible,Select only required columns rather than using select * from Tablexyz.  
* Put more logic into SQL statements	
* DB are designed to use SQL efficiently	
* Proper use of SQL can avoid performance problems

Smart Query Ex: get employees in ENG dept, Instead of:	

	SELECT * FROM employees; 
	SELECT * FROM dept;
(and joining on Java application side), **use database join:**	

	SELECT employees.* FROM employees E, dept D 
	WHERE E.DEPTNO = D.DEPTNO AND D.DEPTTYPE = 'ENG';

Minimize ResultSet before crossing network 
Many performance problems come from moving raw data around needlessly


###Smart Query Guidelines###

* Use DB for filtering
* Use Java for business logic
* DB does filtering very well
* DB business logic is poor(At least very inconsistent between database vendors.)


###Keep operational data set small as possible###

* Move non-current data to other tables and do joins for rarer historical queries
* Otherwise, index and cluster so frequently used data is logically and physically localized


##<a name='strategies'></a>Optimization Stategies

1. Leverage the strengths of the DB
1. Use the full range of java.sql API
1. Design for Performance \- Connection Pools, Multi-Threading, etc. 
1. Always close Statement and ResultSet objects as soon as possible.
1. Test your code with debug statements to determine the time it takes to execute your query and so on to help in tuning your code. Also use query plan functionality if available.
1. Use proper ( and a single standard if possible ) formats, especially for dates.
1. Use proper data types for specific kind of data. For example, store birthdate as a date type rather than, say, varchar.


##References:
* [Best practices to improve performance in JDBC](http://www.precisejava.com/javaperf/j2ee/JDBC.htm)(I love this most.)
* [Jdbc Driver Types](http://www.tutorialspoint.com/jdbc/jdbc-driver-types.htm)
* [JDBC Transactions](https://access.redhat.com/site/documentation/en-US/JBoss_Enterprise_Data_Services/5/html/Data_Services_Client_Developer_Guide/chap-JDBC_Transactions.html)
* [WebSphere Application Server Development Best Practices for Performance and Scalability](http://www-01.ibm.com/software/webservers/appserv/ws_bestpractices.pdf)
* [What areas should I focus on for the best performance in a JDBC application?](http://www.jguru.com/faq/view.jsp?EID=131579)  