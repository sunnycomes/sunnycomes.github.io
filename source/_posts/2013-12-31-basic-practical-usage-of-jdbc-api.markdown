---
layout: post
title: "Basic Practical Usage of JDBC API"
date: 2013-10-31 14:54:53 +0800
comments: true
categories: coding
---
The first version is completed on 2013.12.22 20:38, the main content is from ***[here](http://alumnus.caltech.edu/~chamness/JDBC_Tuning.pdf)***, thanks to the origin author Mark Chamness.This post is dedicated to illustrate techniques for optimizing JDBC API-based calls from the Java platform. As a result of this presentation, you will:

* Design better JDBC implementations
* Recognize potential performance bottlenecks
* Make more money

#Agenda#
1. Why optimize?
1. Basic API techniques.
1. Design Strategies.

##Why Optimize?##
* On average, a web request performs 4 database queries.
* Experience has shown that database calls are typical performance bottleneck.
* Bad JDBC can overwhelm the database.

###SQL Statements###
Most flexible
Least reliable
Must be recompiled in database for each use

####PreparedStatement###
Represents a precompiled SQL statement
Can be used to efficiently execute statement multiple times
Somewhat flexible ¨Ccan create new ones as needed

###CallableStatement###
Used to execute SQL stored procedures. 
Same syntax as PreparedStatement.
Least flexible.
Most optimized DB call.

##Cache##
Keep data within client to reduce the number of round-trips to the database.
Lesson: The less JDBC the better.

##Use Database Connection Pool##
Don¡¯t use DriverManager.getConnection() often. JDBC connections can take 0.5 to 2 seconds to create.
Create Pool of Connections and reuse them.
Necessity for any production system.

<!-- more -->

##Use multi-threading with Connection Pooling to address network latency##
Threads can issue queries over separate database connections.
This improves performance to a point.

	Single-batch Transactions
		BEGIN TRANSACTION
			UPDATE TABLE1... 
			INSERT INTO TABLE2¡­
			DELETE TABLE3
		COMMIT

###Single-batch Transactions###
DB obtains necessary locks on rows and tables, uses and releases them in one step
Depending on transaction type, separate statements and commits can result in more DB calls and hold DB locks longer

###Single-batch Transaction Types---Significantly different effects!###
####java.sql.Connection####

	TRANSACTION_READ_COMMITTED 
	TRANSACTION_READ_UNCOMMITTED 
	TRANSACTION_REPEATABLE_READ 
	TRANSACTION_SERIALIZABLE

###Don¡¯t have transaction span user input###
Application sends BEGIN TRAN and SQL, locking rows or tables for update
Application waits for user to press key before committing transaction

####Solution: Optimistic locking####
Optimistic locking employs timestamps and triggers in queries and updates
Queries select data with timestamp values
Prepare a transaction based on that data, without locking data in a transaction

##Smart Queries##
* Make queries as specific as possible	
* Put more logic into SQL statements	
* DB are designed to use SQL efficiently	
* Proper use of SQL can avoid performance problems

Smart Query Ex: get employees in ENG dept	, Instead of:	

	SELECT * FROM employees; 
	SELECT * FROM dept;
(and joining on Java application side), **use database join:**	

	SELECT employees.* FROM employees E, dept D 
	WHERE E.DEPTNO = D.DEPTNO AND D.DEPTTYPE = ¡®ENG¡¯;

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


#Optimization Stages#
1. Leverage the strengths of the DB
1. Use the full range of java.sql API
1. Design for Performance ¨CConnection Pools, Multi-Threading, etc. 