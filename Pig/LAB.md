# LAB

---
## 1. Data ingest with Hadoop Tools
---

### 1.1. Importing Database Tables into HDFS with Sqoop

#### "employees, customers, products, orders" Tables
```console

$ sqoop import \
> --connect jdbc:mysql://localhost/dualcore \
> --username training --password training \
> --fields-terminated-by '\t' \
> --warehouse-dir /dualcore \
> --table employees

19/04/09 21:43:54 INFO mapreduce.ImportJobBase: Transferred 6.3954 MB in 49.7046 seconds (131.756 KB/sec)
19/04/09 21:43:54 INFO mapreduce.ImportJobBase: Retrieved 61712 records.

19/04/09 21:48:59 INFO mapreduce.ImportJobBase: Transferred 60.8369 KB in 33.0854 seconds (1.8388 KB/sec)
19/04/09 21:48:59 INFO mapreduce.ImportJobBase: Retrieved 1114 records.

19/04/09 21:50:04 INFO mapreduce.ImportJobBase: Transferred 60.2647 MB in 46.957 seconds (1.2834 MB/sec)
19/04/09 21:50:04 INFO mapreduce.ImportJobBase: Retrieved 1662951 records.


```
#### Occur Importing "order_details" table error

```console
ERROR tool.ImportTool: Error during import: No primary key could be found for table order_details. Please specify one with --split-by or perform a sequential import with '-m 1'.
```

#### Split-by option
```console
$ sqoop eval \
> --connect jdbc:mysql://localhost/dualcore \
> --username training --password training \
> --query "DESC order_details"

---------------------------------------------------------------------------------------------------------
| Field                | Type                 | Null | Key | Default              | Extra                | 
---------------------------------------------------------------------------------------------------------
| order_id             | int(11)              | NO  |     | (null)               |                      | 
| prod_id              | int(11)              | NO  |     | (null)               |                      | 
---------------------------------------------------------------------------------------------------------
```
> We use "order_id" column

```console
$ sqoop import \
> --connect jdbc:mysql://localhost/dualcore \
> --username training --password training \
> --fields-terminated-by '\t' \
> --warehouse-dir /dualcore \
> --table order_details \
> --split-by order_id

19/04/09 21:57:24 INFO mapreduce.ImportJobBase: Transferred 50.8613 MB in 39.7815 seconds (1.2785 MB/sec)
19/04/09 21:57:24 INFO mapreduce.ImportJobBase: Retrieved 3333244 records.
```

#### Performance single task
```console
19/04/09 22:05:41 INFO mapreduce.ImportJobBase: Transferred 50.8613 MB in 22.4856 seconds (2.262 MB/sec)
19/04/09 22:05:41 INFO mapreduce.ImportJobBase: Retrieved 3333244 records.
```

#### Why single task(22.4s) faster than split-by task(39.7s)?
*(Maybe)* This is because the **overhead** for the distributed processing is larger than the gain obtained by the distributed processing.
- overhead : initializing mapper, range query in MySQL



---
## 2. Using Pig for ETL Processing
---

### 2.1. Working in the Grunt Shell

```console
$ pig-x local

grunt> data = LOAD 'sampl1.txt';
grunt> DUMP data;

(...skip)
(dualcore,C4,05/01/2013,00:02:54,filmport.example.com,0,58,USA,SIDE)
(pictures,A2,05/01/2013,00:02:58,salestiger.example.com,0,72,USA,SIDE)
(present,B7,05/01/2013,00:03:00,burritofinder.example.com,0,100,USA,TOP)
(pictures,B8,05/01/2013,00:03:01,dvdreview.example.com,0,66,USA,SIDE)
(bluetooth,B8,05/01/2013,00:03:05,audiophile.example.com,0,82,USA,TOP)
(lightweight,B9,05/01/2013,00:03:06,audioexpert.example.com,0,88,USA,TOP)

grunt> first_2_columns = LOAD 'sample1.txt' AS
>> (keyword:chararray, campaign_id:chararray);
grunt> DUMP first_2_columns;

(...skip)
(pictures,B8)
(bluetooth,B8)
(lightweight,B9)

grunt> DESCRIBE first_2_columns;
first_2_columns: {keyword: chararray,campaign_id: chararray}

grunt> QUIT;
```

### 2.2. Processing Input Data from the First Ad Network

#### Add data to HDFS
```console
$ hdfs dfs -put $ADIR/data/ad_data1.txt /dualcore/
$ hdfs dfs -ls /dualcore/ | grep ad_data1.txt

-rw-rw-rw-   1 training supergroup   31540980 2019-04-09 23:18 /dualcore/ad_data1.txt
```

#### first_etl.pig
```pig
data = LOAD '/dualcore/ad_data1.txt'
       AS (keyword : chararray,
           campaign_id : chararray,
           date : chararray,
           time : chararray,
           display_site : chararray,
           was_clicked : int,
           cpc : int,
           country : chararray,
           placement : chararray);

onlyUSA = FILTER data BY country == 'USA';

transformedData = FOREACH onlyUSA
                  GENERATE campaign_id,
                           date,
                           time,
                           UPPER(TRIM(keyword)),
                           display_site,
                           placement,
                           was_clicked,
                           cpc;

STORE transformedData INTO '/dualcore/ad_data1'
USING PigStorage('\t');
```

#### Run pig file with HDFS file
```console
$ pig first_etl.pig
$ hdfs dfs -cat /dualcore/ad_data1/part* | head -20

(...skip)
B4	05/01/2013	00:02:45	APPS	bytewiz.example.com	SIDE	0	54
C4	05/01/2013	00:02:54	DUALCORE	filmport.example.com	SIDE058
A2	05/01/2013	00:02:58	PICTURES	salestiger.example.com	SIDE072
```

### 2.3. Processing Input Data from the Second Ad Network

```console
$ hdfs dfs -put $ADIR/data/ad_data2.txt /dualcore/
$ hdfs dfs -ls /dualcore | grep ad_data2.txt

-rw-rw-rw-   1 training supergroup   23769018 2019-04-09 23:43 /dualcore/ad_data2.txt
```

#### second_etl.pig
```pig
data = LOAD '/dualcore/ad_data2.txt'
       USING PigStorage(',')
       AS (campaign_id : chararray,
           date : chararray,
           time : chararray,
           display_site : chararray,
           placement : chararray,
           was_clicked : int,
           cpc : int,
           keyword : chararray);

uniqueData = DISTINCT data;

transformedData = FOREACH uniqueData
                  GENERATE campaign_id,
                           REPLACE(date, '-', '/'),
                           time,
                           UPPER(TRIM(keyword)),
                           display_site,
                           placement,
                           was_clicked,
                           cpc;

STORE transformedData INTO '/dualcore/ad_data2'
USING PigStorage('\t');
```

#### Run pig file with HDFS file
```console
$ hdfs dfs -cat /dualcore/ad_data2/part* | head -20
A1	05/01/2013	00:22:44	E-BOOK	technews.example.com	SIDE	0	60
A1	05/01/2013	00:23:35	DEAL	supportwiz.example.com	TOP	0	80
A1	05/01/2013	00:23:47	TRAVEL	siliconcenter.example.com	TOP	082
(...skip)
```

---
## 3. Analyzing Ad Campaign Data with Pig
---
### 3.1. Finding Low-Cost Websites

#### low_cost_sites.pig
```pig
data = LOAD 'test_ad_data.txt'
       AS (campaign_id : chararray,
           date : chararray,
           time : chararray,
           keyword : chararray,
           display_site : chararray,
           placement : chararray,
           was_clicked : int,
           cpc : int);

clickOnce = FILTER data BY was_clicked == 1;

groupedByDS = GROUP clickOnce BY display_site;

aggregateData = FOREACH groupedByDS
                GENERATE group AS display_site,
                         SUM(clickOnce.was_clicked) AS clickSum;

orderedData = ORDER aggregateData BY clickSum ASC;

topThree = LIMIT orderedData 3;

DUMP topThree;
```

#### Run Pig file
```console
$ pig -x local low_cost_sites.pig

(megawave.example.com,1)
(megasource.example.com,1)
(diskcentral.example.com,1)
```


