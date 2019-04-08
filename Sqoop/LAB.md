## Lab
___
1. From the <b>accounts table</b>, import only the primary key, along with the first name, last name to HDFS directory /loudacre/accounts/user_info.  
Please save the file in <u>text format</u> with <u>tab delimiters</u>
---

### 1.1.  테이블 리스트 조회 : list-tables

```console
$ sqoop list-tables \
> --connect jdbc:mysql://localhost/loudacre \
> --username training --password training

accountdevice
accounts
basestations
customerservicerep
device
knowledgebase
mostactivestations
webpage
```

### 1.2. 쿼리 입력 : --query " QUERY CLAUSE "  
<i>( MySQL table description query : DESC TableName )</i>

```console
$ sqoop eval \
> --connect jdbc:mysql://localhost/loudacre \
> --username training --password training \
> --query "DESC accounts"

---------------------------------------------------------------------------------------------------------
| Field                | Type                 | Null | Key | Default              | Extra                | 
---------------------------------------------------------------------------------------------------------
| acct_num             | int(11)              | NO  | PRI | (null)               |                      | 
| acct_create_dt       | datetime             | NO  |     | (null)               |                      | 
| acct_close_dt        | datetime             | YES |     | (null)               |                      | 
| first_name           | varchar(255)         | NO  |     | (null)               |                      | 
| last_name            | varchar(255)         | NO  |     | (null)               |                      | 
| address              | varchar(255)         | NO  |     | (null)               |                      | 
| city                 | varchar(255)         | NO  |     | (null)               |                      | 
| state                | varchar(255)         | NO  |     | (null)               |                      | 
| zipcode              | varchar(255)         | NO  |     | (null)               |                      | 
| phone_number         | varchar(255)         | NO  |     | (null)               |                      | 
| created              | datetime             | NO  |     | (null)               |                      | 
| modified             | datetime             | NO  |     | (null)               |                      | 
---------------------------------------------------------------------------------------------------------
```
> Primary key : acct_num
### 1.3.  acct_num, first_name, last_name 컬럼 추출 후 text file, tab delimeter 로 저장

```console
$ sqoop import \
> --connect jdbc:mysql://localhost/loudacre \
> --username training --password training \
> --table accounts \
> --columns "acct_num, first_name, last_name" \
> --target-dir /loudacre/accounts/user_info \
> --as-textfile \
> --fields-terminated-by "\t"


19/04/07 22:26:57 INFO sqoop.Sqoop: Running Sqoop version: 1.4.6-cdh5.7.0
19/04/07 22:26:57 WARN tool.BaseSqoopTool: Setting your password on the command-line is insecure. Consider using -P instead.
19/04/07 22:26:58 INFO manager.MySQLManager: Preparing to use a MySQL streaming resultset.
19/04/07 22:26:58 INFO tool.CodeGenTool: Beginning code generation
19/04/07 22:26:58 INFO manager.SqlManager: Executing SQL statement: SELECT t.* FROM `accounts` AS t LIMIT 1
19/04/07 22:26:58 INFO manager.SqlManager: Executing SQL statement: SELECT t.* FROM `accounts` AS t LIMIT 1
19/04/07 22:26:58 INFO orm.CompilationManager: HADOOP_MAPRED_HOME is /usr/lib/hadoop-mapreduce
Note: /tmp/sqoop-training/compile/8066cc181785266388394385c0f367c6/accounts.java uses or overrides a deprecated API.
Note: Recompile with -Xlint:deprecation for details.
19/04/07 22:27:00 INFO orm.CompilationManager: Writing jar file: /tmp/sqoop-training/compile/8066cc181785266388394385c0f367c6/accounts.jar
19/04/07 22:27:00 WARN manager.MySQLManager: It looks like you are importing from mysql.
19/04/07 22:27:00 WARN manager.MySQLManager: This transfer can be faster! Use the --direct
19/04/07 22:27:00 WARN manager.MySQLManager: option to exercise a MySQL-specific fast path.
19/04/07 22:27:00 INFO manager.MySQLManager: Setting zero DATETIME behavior to convertToNull (mysql)
19/04/07 22:27:00 INFO mapreduce.ImportJobBase: Beginning import of accounts
19/04/07 22:27:00 INFO Configuration.deprecation: mapred.job.tracker is deprecated. Instead, use mapreduce.jobtracker.address
19/04/07 22:27:01 INFO Configuration.deprecation: mapred.jar is deprecated. Instead, use mapreduce.job.jar
19/04/07 22:27:01 INFO Configuration.deprecation: mapred.map.tasks is deprecated. Instead, use mapreduce.job.maps
19/04/07 22:27:02 INFO client.RMProxy: Connecting to ResourceManager at /0.0.0.0:8032
19/04/07 22:27:04 INFO db.DBInputFormat: Using read commited transaction isolation
19/04/07 22:27:04 INFO db.DataDrivenDBInputFormat: BoundingValsQuery: SELECT MIN(`acct_num`), MAX(`acct_num`) FROM `accounts`
19/04/07 22:27:04 INFO db.IntegerSplitter: Split size: 32440; Num splits: 4 from: 1 to: 129761
19/04/07 22:27:04 INFO mapreduce.JobSubmitter: number of splits:4
19/04/07 22:27:04 INFO mapreduce.JobSubmitter: Submitting tokens for job: job_1554697767201_0003
19/04/07 22:27:05 INFO impl.YarnClientImpl: Submitted application application_1554697767201_0003
19/04/07 22:27:05 INFO mapreduce.Job: The url to track the job: http://localhost:8088/proxy/application_1554697767201_0003/
19/04/07 22:27:05 INFO mapreduce.Job: Running job: job_1554697767201_0003
19/04/07 22:27:14 INFO mapreduce.Job: Job job_1554697767201_0003 running in uber mode : false
19/04/07 22:27:14 INFO mapreduce.Job:  map 0% reduce 0%
19/04/07 22:27:22 INFO mapreduce.Job:  map 25% reduce 0%
19/04/07 22:27:28 INFO mapreduce.Job:  map 50% reduce 0%
19/04/07 22:27:34 INFO mapreduce.Job:  map 75% reduce 0%
19/04/07 22:27:38 INFO mapreduce.Job:  map 100% reduce 0%
19/04/07 22:27:38 INFO mapreduce.Job: Job job_1554697767201_0003 completed successfully
19/04/07 22:27:38 INFO mapreduce.Job: Counters: 30
	File System Counters
		FILE: Number of bytes read=0
		FILE: Number of bytes written=560464
		FILE: Number of read operations=0
		FILE: Number of large read operations=0
		FILE: Number of write operations=0
		HDFS: Number of bytes read=470
		HDFS: Number of bytes written=2615920
		HDFS: Number of read operations=16
		HDFS: Number of large read operations=0
		HDFS: Number of write operations=8
	Job Counters 
		Launched map tasks=4
		Other local map tasks=4
		Total time spent by all maps in occupied slots (ms)=0
		Total time spent by all reduces in occupied slots (ms)=0
		Total time spent by all map tasks (ms)=16827
		Total vcore-seconds taken by all map tasks=16827
		Total megabyte-seconds taken by all map tasks=4307712
	Map-Reduce Framework
		Map input records=129761
		Map output records=129761
		Input split bytes=470
		Spilled Records=0
		Failed Shuffles=0
		Merged Map outputs=0
		GC time elapsed (ms)=315
		CPU time spent (ms)=3790
		Physical memory (bytes) snapshot=498491392
		Virtual memory (bytes) snapshot=8262041600
		Total committed heap usage (bytes)=251920384
	File Input Format Counters 
		Bytes Read=0
	File Output Format Counters 
		Bytes Written=2615920
19/04/07 22:27:38 INFO mapreduce.ImportJobBase: Transferred 2.4947 MB in 36.7048 seconds (69.5987 KB/sec)
19/04/07 22:27:38 INFO mapreduce.ImportJobBase: Retrieved 129761 records.
```

### 1.4. 저장 내용 확인

```console
$  hdfs dfs -ls /loudacre/accounts/user_info

-rw-rw-rw-   1 training supergroup          0 2019-04-07 22:27 /loudacre/accounts/user_info/_SUCCESS
-rw-rw-rw-   1 training supergroup     638090 2019-04-07 22:27 /loudacre/accounts/user_info/part-m-00000
-rw-rw-rw-   1 training supergroup     649567 2019-04-07 22:27 /loudacre/accounts/user_info/part-m-00001
-rw-rw-rw-   1 training supergroup     649000 2019-04-07 22:27 /loudacre/accounts/user_info/part-m-00002
-rw-rw-rw-   1 training supergroup     679263 2019-04-07 22:27 /loudacre/accounts/user_info/part-m-00003

$ hdfs dfs -tail /loudacre/accounts/user_info/part-m-00000

lsh
32390	Olga	Lipson
32391	Eddie	Hedrick
32392	Alvin	Phillips
32393	Travis	Ainsworth
... (생략)


```


---
2. This time save the same in <u>parquet format</u> with <u>snappy compression</u>.  
Save it in /loudacre/accounts/user_compressed. Provide a screenshot of HUE with the new directory created.
---

### 2.1. parquet format, snappy compression 이용하여 저장

```console
sqoop import \
> --connect jdbc:mysql://localhost/loudacre \
> --username training --password training \
> --table accounts \
> --columns "acct_num, first_name, last_name" \
> --target-dir /loudacre/accounts/user_compressed \
> --fields-terminated-by "\t" \
> --as-parquetfile \
> --compression-codec org.apache.hadoop.io.compress.SnappyCodec

19/04/07 23:04:11 INFO sqoop.Sqoop: Running Sqoop version: 1.4.6-cdh5.7.0
19/04/07 23:04:11 WARN tool.BaseSqoopTool: Setting your password on the command-line is insecure. Consider using -P instead.
19/04/07 23:04:11 INFO manager.MySQLManager: Preparing to use a MySQL streaming resultset.
19/04/07 23:04:11 INFO tool.CodeGenTool: Beginning code generation
19/04/07 23:04:11 INFO tool.CodeGenTool: Will generate java class as codegen_accounts
19/04/07 23:04:11 INFO manager.SqlManager: Executing SQL statement: SELECT t.* FROM `accounts` AS t LIMIT 1
19/04/07 23:04:11 INFO manager.SqlManager: Executing SQL statement: SELECT t.* FROM `accounts` AS t LIMIT 1
19/04/07 23:04:11 INFO orm.CompilationManager: HADOOP_MAPRED_HOME is /usr/lib/hadoop-mapreduce
Note: /tmp/sqoop-training/compile/a2f859460f94510d937622a2ffd84e25/codegen_accounts.java uses or overrides a deprecated API.
Note: Recompile with -Xlint:deprecation for details.
19/04/07 23:04:13 INFO orm.CompilationManager: Writing jar file: /tmp/sqoop-training/compile/a2f859460f94510d937622a2ffd84e25/codegen_accounts.jar
19/04/07 23:04:13 WARN manager.MySQLManager: It looks like you are importing from mysql.
19/04/07 23:04:13 WARN manager.MySQLManager: This transfer can be faster! Use the --direct
19/04/07 23:04:13 WARN manager.MySQLManager: option to exercise a MySQL-specific fast path.
19/04/07 23:04:13 INFO manager.MySQLManager: Setting zero DATETIME behavior to convertToNull (mysql)
19/04/07 23:04:13 INFO mapreduce.ImportJobBase: Beginning import of accounts
19/04/07 23:04:13 INFO Configuration.deprecation: mapred.job.tracker is deprecated. Instead, use mapreduce.jobtracker.address
19/04/07 23:04:14 INFO Configuration.deprecation: mapred.jar is deprecated. Instead, use mapreduce.job.jar
19/04/07 23:04:15 INFO manager.SqlManager: Executing SQL statement: SELECT t.* FROM `accounts` AS t LIMIT 1
19/04/07 23:04:15 INFO manager.SqlManager: Executing SQL statement: SELECT t.* FROM `accounts` AS t LIMIT 1
19/04/07 23:04:16 INFO Configuration.deprecation: mapred.map.tasks is deprecated. Instead, use mapreduce.job.maps
19/04/07 23:04:16 INFO client.RMProxy: Connecting to ResourceManager at /0.0.0.0:8032
19/04/07 23:04:18 INFO db.DBInputFormat: Using read commited transaction isolation
19/04/07 23:04:18 INFO db.DataDrivenDBInputFormat: BoundingValsQuery: SELECT MIN(`acct_num`), MAX(`acct_num`) FROM `accounts`
19/04/07 23:04:18 INFO db.IntegerSplitter: Split size: 32440; Num splits: 4 from: 1 to: 129761
19/04/07 23:04:18 INFO mapreduce.JobSubmitter: number of splits:4
19/04/07 23:04:18 INFO mapreduce.JobSubmitter: Submitting tokens for job: job_1554697767201_0005
19/04/07 23:04:19 INFO impl.YarnClientImpl: Submitted application application_1554697767201_0005
19/04/07 23:04:19 INFO mapreduce.Job: The url to track the job: http://localhost:8088/proxy/application_1554697767201_0005/
19/04/07 23:04:19 INFO mapreduce.Job: Running job: job_1554697767201_0005
19/04/07 23:04:27 INFO mapreduce.Job: Job job_1554697767201_0005 running in uber mode : false
19/04/07 23:04:27 INFO mapreduce.Job:  map 0% reduce 0%
19/04/07 23:04:35 INFO mapreduce.Job:  map 25% reduce 0%
19/04/07 23:04:43 INFO mapreduce.Job:  map 50% reduce 0%
19/04/07 23:04:50 INFO mapreduce.Job:  map 75% reduce 0%
19/04/07 23:04:57 INFO mapreduce.Job:  map 100% reduce 0%
19/04/07 23:04:58 INFO mapreduce.Job: Job job_1554697767201_0005 completed successfully
19/04/07 23:04:58 INFO mapreduce.Job: Counters: 30
	File System Counters
		FILE: Number of bytes read=0
		FILE: Number of bytes written=565908
		FILE: Number of read operations=0
		FILE: Number of large read operations=0
		FILE: Number of write operations=0
		HDFS: Number of bytes read=25234
		HDFS: Number of bytes written=1305047
		HDFS: Number of read operations=272
		HDFS: Number of large read operations=0
		HDFS: Number of write operations=40
	Job Counters 
		Launched map tasks=4
		Other local map tasks=4
		Total time spent by all maps in occupied slots (ms)=0
		Total time spent by all reduces in occupied slots (ms)=0
		Total time spent by all map tasks (ms)=23179
		Total vcore-seconds taken by all map tasks=23179
		Total megabyte-seconds taken by all map tasks=5933824
	Map-Reduce Framework
		Map input records=129761
		Map output records=129761
		Input split bytes=470
		Spilled Records=0
		Failed Shuffles=0
		Merged Map outputs=0
		GC time elapsed (ms)=534
		CPU time spent (ms)=8580
		Physical memory (bytes) snapshot=668413952
		Virtual memory (bytes) snapshot=8296525824
		Total committed heap usage (bytes)=251920384
	File Input Format Counters 
		Bytes Read=0
	File Output Format Counters 
		Bytes Written=0
19/04/07 23:04:58 INFO mapreduce.ImportJobBase: Transferred 1.2446 MB in 42.446 seconds (30.0255 KB/sec)
19/04/07 23:04:58 INFO mapreduce.ImportJobBase: Retrieved 129761 records.
```
> 위의 1.3. 에서는 <b>2.4947 MB</b> 를 사용했으나  
> 2.1. (compressed) 에서는 <b>1.2446 MB</b> 만을 사용하여 저장하였다.

### 2.2. Hue 에서 Directory 확인
![image](https://user-images.githubusercontent.com/28293389/55702265-05d06c80-5a11-11e9-8721-74f208064e34.png)



---
3. Finally save in /loudacre/accounts/CA only clients whose state is from <b>California</b>.  
Save the file in <u>avro format</u> and <u>compressed using snappy</u>.  
From the terminal, display some of the records that you just imported. Take a screenshot and save it as CA_only
---

### 3.1. State가 CA(California)인 Row만 추출

```Console
$ sqoop import \
> --connect jdbc:mysql://localhost/loudacre \
> --username training --password training \
> --table accounts \
> --columns "acct_num, first_name, last_name" \
> --where " state ='CA' " \
> --target-dir /loudacre/accounts/CA \
> --fields-terminated-by "\t" \
> --as-avrodatafile \
> --compression-codec org.apache.hadoop.io.compress.SnappyCodec

19/04/07 23:25:55 INFO sqoop.Sqoop: Running Sqoop version: 1.4.6-cdh5.7.0
19/04/07 23:25:55 WARN tool.BaseSqoopTool: Setting your password on the command-line is insecure. Consider using -P instead.
19/04/07 23:25:55 INFO manager.MySQLManager: Preparing to use a MySQL streaming resultset.
19/04/07 23:25:55 INFO tool.CodeGenTool: Beginning code generation
19/04/07 23:25:56 INFO manager.SqlManager: Executing SQL statement: SELECT t.* FROM `accounts` AS t LIMIT 1
19/04/07 23:25:56 INFO manager.SqlManager: Executing SQL statement: SELECT t.* FROM `accounts` AS t LIMIT 1
19/04/07 23:25:56 INFO orm.CompilationManager: HADOOP_MAPRED_HOME is /usr/lib/hadoop-mapreduce
Note: /tmp/sqoop-training/compile/974073d3d921acb1d45471c2ff08f335/accounts.java uses or overrides a deprecated API.
Note: Recompile with -Xlint:deprecation for details.
19/04/07 23:25:58 INFO orm.CompilationManager: Writing jar file: /tmp/sqoop-training/compile/974073d3d921acb1d45471c2ff08f335/accounts.jar
19/04/07 23:25:58 WARN manager.MySQLManager: It looks like you are importing from mysql.
19/04/07 23:25:58 WARN manager.MySQLManager: This transfer can be faster! Use the --direct
19/04/07 23:25:58 WARN manager.MySQLManager: option to exercise a MySQL-specific fast path.
19/04/07 23:25:58 INFO manager.MySQLManager: Setting zero DATETIME behavior to convertToNull (mysql)
19/04/07 23:25:58 INFO mapreduce.ImportJobBase: Beginning import of accounts
19/04/07 23:25:58 INFO Configuration.deprecation: mapred.job.tracker is deprecated. Instead, use mapreduce.jobtracker.address
19/04/07 23:25:59 INFO Configuration.deprecation: mapred.jar is deprecated. Instead, use mapreduce.job.jar
19/04/07 23:25:59 INFO manager.SqlManager: Executing SQL statement: SELECT t.* FROM `accounts` AS t LIMIT 1
19/04/07 23:25:59 INFO manager.SqlManager: Executing SQL statement: SELECT t.* FROM `accounts` AS t LIMIT 1
19/04/07 23:26:00 INFO mapreduce.DataDrivenImportJob: Writing Avro schema file: /tmp/sqoop-training/compile/974073d3d921acb1d45471c2ff08f335/accounts.avsc
19/04/07 23:26:00 INFO Configuration.deprecation: mapred.map.tasks is deprecated. Instead, use mapreduce.job.maps
19/04/07 23:26:00 INFO client.RMProxy: Connecting to ResourceManager at /0.0.0.0:8032
19/04/07 23:26:02 INFO db.DBInputFormat: Using read commited transaction isolation
19/04/07 23:26:02 INFO db.DataDrivenDBInputFormat: BoundingValsQuery: SELECT MIN(`acct_num`), MAX(`acct_num`) FROM `accounts` WHERE (  state ='CA'  )
19/04/07 23:26:02 INFO db.IntegerSplitter: Split size: 32439; Num splits: 4 from: 1 to: 129760
19/04/07 23:26:02 INFO mapreduce.JobSubmitter: number of splits:4
19/04/07 23:26:02 INFO mapreduce.JobSubmitter: Submitting tokens for job: job_1554697767201_0006
19/04/07 23:26:02 INFO impl.YarnClientImpl: Submitted application application_1554697767201_0006
19/04/07 23:26:02 INFO mapreduce.Job: The url to track the job: http://localhost:8088/proxy/application_1554697767201_0006/
19/04/07 23:26:02 INFO mapreduce.Job: Running job: job_1554697767201_0006
19/04/07 23:26:11 INFO mapreduce.Job: Job job_1554697767201_0006 running in uber mode : false
19/04/07 23:26:11 INFO mapreduce.Job:  map 0% reduce 0%
19/04/07 23:26:17 INFO mapreduce.Job:  map 25% reduce 0%
19/04/07 23:26:23 INFO mapreduce.Job:  map 50% reduce 0%
19/04/07 23:26:29 INFO mapreduce.Job:  map 75% reduce 0%
19/04/07 23:26:35 INFO mapreduce.Job:  map 100% reduce 0%
19/04/07 23:26:35 INFO mapreduce.Job: Job job_1554697767201_0006 completed successfully
19/04/07 23:26:35 INFO mapreduce.Job: Counters: 30
	File System Counters
		FILE: Number of bytes read=0
		FILE: Number of bytes written=563576
		FILE: Number of read operations=0
		FILE: Number of large read operations=0
		FILE: Number of write operations=0
		HDFS: Number of bytes read=470
		HDFS: Number of bytes written=1350990
		HDFS: Number of read operations=16
		HDFS: Number of large read operations=0
		HDFS: Number of write operations=8
	Job Counters 
		Launched map tasks=4
		Other local map tasks=4
		Total time spent by all maps in occupied slots (ms)=0
		Total time spent by all reduces in occupied slots (ms)=0
		Total time spent by all map tasks (ms)=19338
		Total vcore-seconds taken by all map tasks=19338
		Total megabyte-seconds taken by all map tasks=4950528
	Map-Reduce Framework
		Map input records=92416
		Map output records=92416
		Input split bytes=470
		Spilled Records=0
		Failed Shuffles=0
		Merged Map outputs=0
		GC time elapsed (ms)=288
		CPU time spent (ms)=7260
		Physical memory (bytes) snapshot=629596160
		Virtual memory (bytes) snapshot=8286052352
		Total committed heap usage (bytes)=251920384
	File Input Format Counters 
		Bytes Read=0
	File Output Format Counters 
		Bytes Written=1350990
19/04/07 23:26:35 INFO mapreduce.ImportJobBase: Transferred 1.2884 MB in 35.5188 seconds (37.1444 KB/sec)
19/04/07 23:26:35 INFO mapreduce.ImportJobBase: Retrieved 92416 records.

```

### 3.2 Avro To Json : avro-tools tojson

```console
$ avro-tools tojson hdfs://localhost/loudacre/accounts/CA/part-m-00000.avro
```

![image](https://user-images.githubusercontent.com/28293389/55703465-a7a58880-5a14-11e9-8ddb-dfb7e156c950.png)