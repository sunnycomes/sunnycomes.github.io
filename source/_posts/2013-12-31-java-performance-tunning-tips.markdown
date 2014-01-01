---
layout: post
title: "Java Performance Tuning Tips"
date: 2013-11-11 20:59:44 +0800
comments: true
categories: coding
---
The first version is completed on 2013.11.10 21:55, the main content is from [here](http://www.exforsys.com/tutorials/j2ee/performance-tuning-of-java-applications.html), thanks to the origin author.

Ever since the first version of Java Technology hit the streets, performance has been an important issue for Java developers. Java has improved dramatically and continually but, performance tuning is very essential to get the best results, especially when we think of J2EE applications.

<!-- more -->

##Introduction:

Java Performance Tuning (abbreviated as JPT), 2nd edition provides a comprehensive guide to eliminate all the types of performance problems. By considering real-life examples JPT shows how to get rid off all the types of performances problems. For example JPT shows tricks such as how to minimize object creation and replacing strings with arrays can really pay off in improving code performance.

Few of important fundamentals and guidelines included in Java Performance Tuning are:

* Tuning tips for object creation.
* Tuning in JDBC.
* Web services performance tips.
* Tuning in EJB.
* Tuning in J2EE.
* Tuning in JMS.
* Tuning in RMI.

Guidelines for tuning java code without destroying program's skeleton is efficiently presented in second edition of Java Performance Tuning. It includes how to use threads effectively, how to optimize the use of strings, minimizing the creation of objects in program, avoiding bottleneck operation by including all the important fundamentals of Software Engineering to re-pioneer the code, issues of speed of Servlets and JSPs etc. That provides very crucial guidelines in performance tuning for java developers.

##Tuning Tips for Object Creation:

Object Creation is one of the most basic and essential thing while developing a Java Application, as such; object defines the physical reality of class. Pay proper attention while declaring class' methods and variables because careless work carried out at this stage, can cost you in terms of speed and performance because variables and methods unwontedly declared and initialize can create overhead in overall speed. Object should always be created early when there is spare time in the application, object once created should be in hold position until it is required. Care should be taken while defining methods that can accept the reusable objects to be filled with the data rather than methods that return objects holding that data, immutable objects can also be used here. Object should be created only when class logically needs. Constructor of class should always be simple. Methods that alter objects directly should always be preferred. Use classes that handle primitive data types rather than wrapping the primitive data types.

##Performance Tuning in JDBC:

Java Database Connectivity (JDBC) is mainly used in most of the Java application. To keep trace on performance tuning in JDBC becomes very crucial and prime issue when Java developer realizes that most of the processing time should not be wasted behind data processing over the network. For more details, check out  [here](/blog/2013/10/31/basic-practical-usage-of-jdbc-api/)

Here are few of the tuning tips for JDBC that can improve the over all performance in Java application.

* SQL statements should be tuned to minimize the data that is return from the database.
* Use of prepared statements and parameterized SQL statements can speed up the over all process in data processing.
* Transaction conflicts should always be avoided.
* Usage of stored procedures, connection pooling, selection of fastest JDBC driver should be encouraged.
* Any of the open resources that is not in use in Java application can keep processor engaged unwontedly, any of the resource that is open and not required to perform any of database activity should be closed like Connections, Statements, ResultSet etc.

##Web Services performance tips:

Because of Java's outstanding performance in web services there are few of the performance tips that are to be considered while developing a web service application. Here are few of the tips given for improving the web services performance.

* Avoid using XML messaging, this helps to achieve fine-grained RPC.
* Frequency of the messaging should be taken into consideration with the replication of the data as necessary.
* Always try to retrieve data during off-hours this helps in course gained transactions.
* Overall system performance should never be neglected and optimized until we know where the bottlenecks are present.
* Asynchronous messaging model should always be taken into account when transport is slow or / and unreliable, or when processing is complex and long running.

##Tips for Quality of services for Web Services

The main requirements in quality of service for web services are:

* Availability and accessibility.
* Integrity and reliability. This ensures that weather program will crash or not while it is running, if so, how often can it crash.
* Number of simultaneous request that can be made to application by the user i.e. "throughput" and what will be the response time to process this request by application i.e. "latency".
* Security issues.
* HTTP is a best-effort delivery service as far as web services issue is concerned. The main reason behind this is that request could simply be dropped. Messaging in web services should always be Asynchronous because Asynchronous messaging can improve throughput no matter at the cost of latency.
* DOM based parsers are slower than SAX based ones.
* Requests results should be cached whenever it is possible.
* Extreme care should be taken to make sure that resources are not locked for long periods of time to avoid serious scalability problems.

Other factor that affects web service performance are:

* Response time of web server.
* Availability of web server.
* Execution time of web application.
* Backend database.
* Scaling web services Tips

Use of faster communication protocol, like plain socket, should always be preferred. Whenever there is requirement of sending large number of documents over the network, basic load-balancing scheme should be achieved, all the documents to be sent should have different URL hosts i.e. binding addresses. For scalability of server better and speedy hardware should be preferred though there is limitation of scalability of server,  that most application performance does not scale linearly with increases in the hardware power. Most of the times in web related services cluster of more than one server is used.

##Performance Tuning tips for EJB:

While developing an EJB application if EJB services for an object is not required than plain Java object should be replaced in place of EJB object. Multiple remote method calls should be changed into one remote method call with all the data combined into a parameter object to enhance the overall process. There should be proper tuning in EJB Server thread count; Use Stateless session beans pool size to minimize the creation and destruction of the beans. When multiple EJB remote calls have to be changed into one session bean remote call and several local calls(SessionFacade), wrap multiple entity beans in a session bean. Transactional time-out should be set previously. Use HttpSession object rather than Stateful session bean to maintain client state. Bulk updating should be used to reduce the overall database calls to fetch and retrieving the data. When dealing with large amounts of data such as searching large database JDBC should be directly used rather than using entity beans.

##J2EE Performance tuning tips:

Here few of the important tuning tips for J2EE in points.

* Entity beans from session beans should always be accessed.
* When you no longer need to use session call HttpSession.invalidate() to clean up a session.
* Save resources by turning off automatic session creation using `< % @page session="false" % >` for web pages that don't require session tracking.
* Use compile time directive `< % @include file="copyleft.html" % >` where possible.
* Whenever beans are co-located in the same JVM, use local entity beans.
* Proprietary stubs can be used for caching and batching data.
* To generate unique primary keys dedicated remote object should be used.
* Whenever possible use cache tagging.
* User JDBC directly instead of using an entity bean only for data access.

##Tuning tips for JMS:

For developing an efficient JMS application transient variables should be used to reduce serialization overheads. For receiving messages asynchronously implement MessageListener. To avoid persistency overhead choose non-durable (NON_PERSISTENT) messages wherever appropriate. It is practically efficient to use `DUPS_OK_ACKNOWLEDGE AND AUTO_ACKNOWLEDGE` than `CLIENT_ACKNOWLEDGE` as far as issue of performance is concerned. Separate transactional sessions and non-transactional sessions for transactional and non-transactional messages should be used separately. Because of the fact that "a higher redelivery delay and lower redelivery limit reduces the overhead" remember to tune the destination parameters. Open java resources can claim for more system resources never forget to close all the resources whenever they are not in use. The last point to be kept while developing a JMS application is that consumer should always start before we start the producer so that the initial messages do not need to be queued up.

##RMI tuning performance tips:

To improve the performance in RMI application always consider altering the Tcp WindowSize parameter. To measure the bandwidth of network netp erf should be used. By setting the properties sun.rmi.dgc.client.gcInterval and sun.rmi.dgc.server.gcInterval RMI garbage collection should be configured in a proper manner. Since sending the object over network may consume much of the time in a big application sending groups of objects together rather than one object at a time is advisable. To speed up the transfers, implement Externalize interface. To handle special cases such as singleton or reusable objects use special codes. To improve overall development quality never try to add the extra complications once the performance target have been met.