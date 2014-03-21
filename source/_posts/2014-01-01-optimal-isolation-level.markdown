---
layout: post
title: "Optimal JDBC Transaction Isolation Level"
date: 2013-10-11 16:09:47 +0800
comments: true
categories: coding
---
##Choose optimal isolation level

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

<!-- more -->

You can get the existing isolation level with getTransactionIsolation() method and set the isolation level with setTransactionIsolation(int isolationlevelconstant) by passing above constants to this method.
The following table describes isolation level against the problem that it prevents :
 
Transaction Level		| Dirty reads	|Non  Repeatable reads  | Phantom reads |Performance impact
:----:				|:--:		|:--:			|:----:		|:--:
TRANSACTION_NONE		| N/A		| N/A			| N/A		| FASTEST
TRANSACTION_READ_UNCOMMITED	| YES		| YES			| YES		| FASTEST
TRANSACTION_READ_COMMITED	| NO		| YES			| YES		| FAST
TRANSACTION_REPEATABLE_READ	| NO		| NO			| YES		| MEDIUM
TRANSACTION_SERIALIZABLE	| NO		| NO			| NO		| SLOW
<br/>
* YES means that the Isolation level does not prevent the problem
* NO means that the Isolation level prevents the problem

By setting isolation levels, you are having an impact on the performance as mentioned in the above table. Database use read and write locks to control above isolation levels. Let us have a look at each of these problems and then look at the impact on the performance.

###Dirty read problem :

The following figure illustrates Dirty read problem  :

 ![dirty_read](/images/upload/dirty_read.gif)

	Step 1:    Database row has PRODUCT = A001 and PRICE = 10
	Step 2:    Connection1 starts  Transaction1 (T1) .
	Step 3:    Connection2 starts  Transaction2 (T2) .
	Step 4:    T1 updates PRICE =20 for PRODUCT = A001
	Step 5:    Database has now PRICE = 20 for PRODUCT = A001
	Step 6:    T2 reads PRICE = 20 for PRODUCT = A001
	Step 7:    T2 commits transaction
	Step 8:    T1 rollbacks the transaction because of some problem

The problem is that T2 gets wrong PRICE=20 for PRODUCT = A001 instead of 10 because of uncommitted read.Obviously it is very dangerous in critical transactions if you read inconsistent data. If you  are sure about not accessing data concurrently  then you can allow this problem by setting TRANSACTION_READ_UNCOMMITED or TRANSACTION_NONE that in turn improves performance otherwise you have to use TRANSACTION_READ_COMMITED to avoid this problem.
 
###Unrepeatable read problem :

The following figure illustrates Unrepeatable read problem  :
 
![unrepeatable_read](/images/upload/unrepeatable_read.gif)

	Step 1:    Database row has PRODUCT = A001 and PRICE = 10
	Step 2:    Connection1 starts  Transaction1 (T1) .
	Step 3:    Connection2 starts  Transaction2 (T2) .
	Step 4:    T1 reads PRICE =10 for PRODUCT = A001
	Step 5:    T2 updates PRICE = 20 for PRODUCT = A001
	Step 6:    T2 commits transaction
	Step 7:    Database row has PRODUCT = A001 and PRICE = 20
	Step 8:    T1 reads PRICE = 20 for PRODUCT = A001
	Step 9:    T1 commits transaction

Here the problem is that Transaction1 reads 10 first time and reads 20 second time but it is supposed to be 10 always whenever it reads a record in that transaction. You can control this problem by setting isolation level as TRANSACTION_REPEATABLE_READ(setting for which one? Of course both).

###Phantom read problem :

The following figure illustrates Phantom read problem  :

![phantom_read](/images/upload/phantom_read.gif)

	Step 1:    Database has a row PRODUCT = A001 and COMPANY_ID = 10
	Step 2:    Connection1 starts  Transaction1 (T1) .
	Step 3:    Connection2 starts  Transaction2 (T2) .
	Step 4:    T1 selects a row with a condition SELECT PRODUCT WHERE COMPANY_ID = 10
	Step 5:    T2 inserts a row with a condition INSERT PRODUCT=A002  WHERE COMPANY_ID= 10
	Step 6:    T2 commits transaction
	Step 7:    Database has 2 rows with that condition
	Step 8:    T1 select again with a condition SELECT PRODUCT WHERE COMPANY_ID=10 and gets 2 rows instead of 1 row
	Step 9:    T1 commits transaction

Here the problem is that T1 gets 2 rows instead of 1 row up on selecting the same condition second time. You can control this problem by setting isolation level as `TRANSACTION_SERIALIZABLE`

##Choosing a right isolation level for your program:

Choosing a right isolation level for your program depends upon your application's requirement. In single application itself the requirement generally changes, suppose if you write a program for searching a product catalog from your database then you can easily choose `TRANSACTION_READ_UNCOMMITED` because you need not worry about the problems that are mentioned above, some other program can insert records at the same time, you don't have to bother much about that insertion. Obviously this improves performance significantly.

If you write a critical program like bank or stocks analysis program where you want to control all of the above mentioned problems, you can choose TRANSACTION_SERIALIZABLE for maximum safety. Here it is the tradeoff between the safety and performance. Ultimately we need safety here.
If you don't have to deal with concurrent transactions your application, then the best choice is `TRANSACTION_NONE` to improve performance.
Other two isolation levels need good understanding of your requirement. If your application needs only committed records, then `TRANSACTION_READ_COMMITED` isolation is the good choice. If your application needs to read a row exclusively till you  finish your work, then `TRANSACTION_REPEATABLE_READ` is the best choice.
Note: Be aware of your database server's support for these isolation levels. Database servers may not support all of these isolation levels. Oracle server supports only two isolation levels, `TRANSACTION_READ_COMMITED` and `TRANSACTION_SERIALIZABLE` isolation level, default isolation level is `TRANSACTION_READ_COMMITED`.


##References

* [Best practices to improve performance in JDBC](http://www.precisejava.com/javaperf/j2ee/JDBC.htm#JDBC102)
* [6 ways of doing locking in .NET (Pessimistic and optimistic)](http://www.codeproject.com/Articles/114262/6-ways-of-doing-locking-in-NET-Pessimistic-and-opt#Solution%20number%202:-%20Use%20timestamp%20data%20type)