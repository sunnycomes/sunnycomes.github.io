---
layout: post
title: "Optimistic and Pessimistic Locking"
date: 2013-10-21 21:40:48 +0800
comments: true
categories: coding
---
##Locking Introduction

This article talks about serveral ways of doing locking. It starts with concurrency problems and then discusses about 2 ways of doing optimistic locking. As optimistic locking does not solve the concurrency issues from roots, it introduces pessimistic locking. It then moves ahead to explain how isolation levels can help us implement pessimistic locking. Each isolation level is explained with sample demonstration to make concepts clearer.

##Why do we need locking?

In multi-user environment it's possible that multiple users can update the same record at the same time causing confusion between users. This issue is termed as concurrency.

<!-- more -->

##How can we solve concurrency problems?

Concurrency problems can be solved by implementing proper "Locking strategy". Locks prevent action on a resource to be performed when some other resource is already performing some action on it.

##Optimistic Locking

As the name suggests "optimistic" it assumes that multiple transaction will work without affecting each other. In other words no locks are enforced while doing optimistic locking. The transaction just verifies that no other transaction has modified the data. In case of modification the transaction is rolled back.

###How does optimistic lock work?

You can implement optimistic locking by numerous ways but the fundamental to implement optimistic locking remains same. It's a 4 step process as shown below:

1. Record the current timestamp.
1. Start changing the values.
1. Before updating check whether anyone else has changed the values by checking the old time stamp and new time stamp or the old value and the current value in database.
1. If it's not equal rollbacks or else commit.

![optimistic_locking](/images/upload/optimistic_locking.jpg)

	update tbl_items set itemname='itemname' where CurrentTimestamp='OldTimeStamp'

	update tbl_items set itemname=@itemname where itemname=@OldItemNameValue


##Pessimistic Locking

But it looks like by using optimistic locking concurrency problems are not really solved?Yes, you said right. By using optimistic locking you only detect the concurrency problem. To solve concurrency issues from the roots itself we need to use pessimistic locking. Optimistic is like prevention while pessimistic locking is actually the cure.
Pessimistic locking assumes that concurrency/collision issues will happen so a lock is placed on the records and then data is updated.

###How can we do pessimistic locking?

We can do pessimistic locking by specifying "IsolationLevel" in SQL Server stored procedures, ADO.NET level or by using transaction scope object, in java the JDBC Connection.

###What kind of locks can be acquired by using pessimistic locking?

There are 4 kinds of locks you can acquire Shared, Exclusive, Update and intent. The first two are actual locks while the other two are hybrid locks and marker. Correspondingly, in SQL there are 4 kinds of transaction isolation level, below is a simple table which shows when to use them and what locks they put.

Isolation Level		 |	Read					|Update		|Insert
:----:			 |:----:					|:----:		|:----:
Read Uncommitted	 |	Reads data which is yet not committed.	|Allowed	|Allowed
Read Committed ( Default)|	Reads data which is committed.		|Allowed	|Allowed
Repeatable Read		 |	Reads data which is committed.		|Not Allowed	|Allowed
Serializable		 |	Reads data which is committed.		|Not Allowed	|Not Allowed





More details are covered in this post[Optimal Isolation Level](/blog/2013/10/11/optimal-isolation-level/);


##References:

[6 ways of doing locking in .NET (Pessimistic and optimistic)](http://www.codeproject.com/Articles/114262/6-ways-of-doing-locking-in-NET-Pessimistic-and-opt#Solution%20number%202:-%20Use%20timestamp%20data%20type)