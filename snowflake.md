### CREATE WAREHOUSE

```
CREATE WAREHOUSE x WITH WAREHOUSE_SIZE = 'XLARGE' WAREHOUSE_TYPE = 'STANDARD' AUTO_SUSPEND = 600 AUTO_RESUME = TRUE MIN_CLUSTER_COUNT = 1 MAX_CLUSTER_COUNT = 2 SCALING_POLICY = 'ECONOMY';
```

**scaling policies**
1. Standard
2. Economy

### SET STANDARD CONTEXT

```
USE ROLE SECURITYADMIN;

ALTER USER JAGUAR
    SET
    DEFAULT_ROLE=TRAINING_ROLE
    DEFAULT_NAMESPACE=JAGUAR_DB.PUBLIC
    DEFAULT_WAREHOUSE=JAGUAR_WH;
```

*will set default context for new worksheets
**need to logout and login

### INITIALLY SUSPEND

create warehouse via sql command - can set initially_suspended = true.
create warehoue via UI - automatically starts

```
ALTER SESSION SET USE_CACHED_RESULT=FALSE;
```

### Snow SQL

`~/.snowsql/config`

```
accountname = [account]
rolename = TRAINING_ROLE
username = JAGUAR
dbname = JAGUAR_DB
schemaname = PUBLIC
warehousename = JAGUAR_WH
```
[account] = account.region except for US West Oregon

**add connection**

```
[connections.SYSADMIN]
accountname=[account]
username = JAGUAR
```

`snowsql -c SYSADMIN`

`snowsql -f script.sql`

`!exit`

### cache

1. metadata cache
2. data cache
3. query result cache
    - disabled using `ALTER SESSION SET USE_CACHED_RESULT = FALSE;`

Answer: The query cache was used, because it was run by someone in the same role. So the query cache can be used across sessions as long as it is in the same role.

`CREATE TABLE .. LIKE` creates a table with the same column definitions as the source table, without copying any of the data.

### PIVOT

```
select * from weekly_sales
pivot(sum(amount) for day in ('MON', 'TUE', 'WED', 'THU', 'FRI'))
```

### CREATE A FILEFORMAT

```
CREATE OR REPLACE FILE FORMAT MYPIPEFORMAT
TYPE = CSV
COMPRESSION = NONE
FIELD_DELIMITER = '|'
FILE_EXTENSION = 'tbl'
ERROR_ON_COLUMN_COUNT_MISMATCH = FALSE
```

### STAGE

```
LIST @training_db.traininglab.ed_stage/load/lab_files/ pattern='.*region.*'
```

```
COPY INTO REGION
FROM @training_db.traininglab.ed_stage/load/lab_files/
FILES = ('region.tbl')
FILE_FORMAT = (FORMAT_NAME = MYPIPEFORMAT)
```

### UNLOAD TO TABLE STAGE

```
COPY INTO @%TABLE
FROM TABLE
FILE_FORMAT = (FORMAT_NAME = MYPIPEFORMAT);
```

A table stage is automatically created for each table.

```
LIST @%table
SNOWSQL> GET @%region file:///<path to dir> ;
REMOVE @%region
```

### CREATE TASK

```
CREATE TASK task_minute
  WAREHOUSE = JAGUAR_LOAD_WH
  SCHEDULE = '1 minute'
AS
INSERT INTO JAGUAR_DB.public.timestamp_example(TIMESTAMP) VALUES(CURRENT_TIMESTAMP);
```

Tasks are created initially suspended.

```
ALTER TASK task_minute RESUME;
ALTER TASK task_minute SUSPEND;
```

### whitelist

```
SELECT SYSTEM$WHITELIST();
```

### stored procedure

```
create or replace procedure ChangeWHSize(wh_name STRING, wh_size STRING)
returns string
language javascript
```

dynamic sql in jsvascript procedure
```
var sql_command = "ALTER WAREHOUSE IF EXISTS " + WH_NAME + " SET WAREHOUE SIZE = " + WH_SIZE;
snowflake.execute({sqlText: sql_command})
```

### HISTORY

```
SHOW TABLES HISTORY;
```

shows dropped_on column

```
undrop table tablename;
```

### time travel
```
SELECT * FROM TABLENAME
BEFORE (statement => '<queryid>')
```

```
CREATE TABLE NEWTABLE
CLONE TABLE 
BEFORE (statement => '<queryid>')
```

### rename

```
alter table tablename rename to newtablename
```

### WAREHOUSE

```
ALTER WAREHOUSE JAGUAR_WH
SET WAREHOUSE_SIZE = XSMALL
ALTER WAREHOUSE JAGUAR_WH SUSPEND;
ALTER WAREHOUSE JAGUAR_WH RESUME;
ALTER SESSION SET USE_CACHED_RESULT = FALSE;
ALTER SESSION SET QUERY_TAG = 'JAGUAR_WH_Sizing';
```

xsmall - 4mm 16GB spilled to local storage
m = 1m, 8g local, 12g network
l = 33s, 14g network
xl = 18s, 16g network

### group by select expression

```
select year(start_time)||'-'||lpad(month(start_time), 2, '0') month,
    sum(credits_used) total_credits_used
from table(result_scan(last_query_id()))
group by month
order by month
```

### session parameters

```
show parameters in session
show parameters like 'DATE_OUTPUT_FORMAT' in session
ALTER SESSION SET DATE_OUTPUT_FORMAT = 'DD MON YYYY';
```

### monitoring

```
SELECT *
FROM TABLE(INFORMATION_SCHEMA.WAREHOUSE_METERING_HISTORY
   (DATEADD('DAYS', -180, CURRENT_DATE())));
```

Row |START_TIME|END_TIME|WAREHOUSE_NAME|CREDITS_USED|CREDITS_USED_COMPUTE|CREDITS_USED_CLOUD_SERVICES
--|--|--|--|--|--|--
1|2021-05-14 12:00:00.000 -0700|2021-05-14 13:00:00.000 -0700|RABBIT_SHARED_WH|0.167174445|0.166666667|0.000507778

```
SELECT *
FROM TABLE (information_schema.database_storage_usage_history
   (DATEADD('days', -10, CURRENT_DATE()), CURRENT_DATE()));
```

Row|USAGE_DATE|DATABASE_NAME|AVERAGE_DATABASE_BYTES|AVERAGE_FAILSAFE_BYTES
--|--|--|--|--
676|2021-05-15|WAYBILL|301182973|0

```
SELECT *
FROM TABLE(INFORMATION_SCHEMA.STAGE_STORAGE_USAGE_HISTORY
(DATE_RANGE_START=>'YYYY-MM-01',
DATE_RANGE_END=>'YYYY-MM-30'));
```

Row|USAGE_DATE|AVERAGE_STAGE_BYTES
--|--|--
11|2021-05-15|719985388

```
SELECT *
FROM SNOWFLAKE.ACCOUNT_USAGE.STORAGE_USAGE
```

Row|USAGE_DATE|STORAGE_BYTES|STAGE_BYTES|FAILSAFE_BYTES
--|--|--|--|--
8|2021-05-15|0.000000|0.000000

```
SELECT *
FROM TABLE(information_schema.warehouse_metering_history
(date_range_start=>date_trunc(month, current_date),
date_range_end=>dateadd(month,1,date_trunc(month, current_date)),'JAGUAR_WH'))
```

Row|START_TIME|END_TIME|WAREHOUSE_NAME|CREDITS_USED|CREDITS_USED_COMPUTE|CREDITS_USED_CLOUD_SERVICES
--|--|--|--|--|--|--
1|2021-05-13 19:00:00.000 -0700|2021-05-13 20:00:00.000 -0700|JAGUAR_WH|0.000051389|0.000000000|0.000051389

```
use snowflake.account_usage
desc login_history
```

```
+------------------------------+-------------------+--------+-------+---------+-------------+------------+-------+------------+---------+-------------+
| name                         | type              | kind   | null? | default | primary key | unique key | check | expression | comment | policy name |
|------------------------------+-------------------+--------+-------+---------+-------------+------------+-------+------------+---------+-------------|
| EVENT_ID                     | NUMBER(38,0)      | COLUMN | Y     | NULL    | N           | N          | NULL  | NULL       | NULL    | NULL        |
| EVENT_TIMESTAMP              | TIMESTAMP_LTZ(6)  | COLUMN | Y     | NULL    | N           | N          | NULL  | NULL       | NULL    | NULL        |
| EVENT_TYPE                   | VARCHAR(16777216) | COLUMN | Y     | NULL    | N           | N          | NULL  | NULL       | NULL    | NULL        |
| USER_NAME                    | VARCHAR(16777216) | COLUMN | Y     | NULL    | N           | N          | NULL  | NULL       | NULL    | NULL        |
| CLIENT_IP                    | VARCHAR(16777216) | COLUMN | Y     | NULL    | N           | N          | NULL  | NULL       | NULL    | NULL        |
| REPORTED_CLIENT_TYPE         | VARCHAR(16777216) | COLUMN | Y     | NULL    | N           | N          | NULL  | NULL       | NULL    | NULL        |
| REPORTED_CLIENT_VERSION      | VARCHAR(16777216) | COLUMN | Y     | NULL    | N           | N          | NULL  | NULL       | NULL    | NULL        |
| FIRST_AUTHENTICATION_FACTOR  | VARCHAR(16777216) | COLUMN | Y     | NULL    | N           | N          | NULL  | NULL       | NULL    | NULL        |
| SECOND_AUTHENTICATION_FACTOR | VARCHAR(16777216) | COLUMN | Y     | NULL    | N           | N          | NULL  | NULL       | NULL    | NULL        |
| IS_SUCCESS                   | VARCHAR(3)        | COLUMN | Y     | NULL    | N           | N          | NULL  | NULL       | NULL    | NULL        |
| ERROR_CODE                   | NUMBER(38,0)      | COLUMN | Y     | NULL    | N           | N          | NULL  | NULL       | NULL    | NULL        |
| ERROR_MESSAGE                | VARCHAR(16777216) | COLUMN | Y     | NULL    | N           | N          | NULL  | NULL       | NULL    | NULL        |
| RELATED_EVENT_ID             | NUMBER(38,0)      | COLUMN | Y     | NULL    | N           | N          | NULL  | NULL       | NULL    | NULL        |
+------------------------------+-------------------+--------+-------+---------+-------------+------------+-------+------------+---------+-------------+
```

```
desc query_history
```

```
+----------------------------------------+-------------------+--------+-------+---------+-------------+------------+-------+------------+---------+-------------+
| name                                   | type              | kind   | null? | default | primary key | unique key | check | expression | comment | policy name |
|----------------------------------------+-------------------+--------+-------+---------+-------------+------------+-------+------------+---------+-------------|
| QUERY_ID                               | VARCHAR(16777216) | COLUMN | Y     | NULL    | N           | N          | NULL  | NULL       | NULL    | NULL        |
| QUERY_TEXT                             | VARCHAR(16777216) | COLUMN | Y     | NULL    | N           | N          | NULL  | NULL       | NULL    | NULL        |
| DATABASE_ID                            | NUMBER(38,0)      | COLUMN | Y     | NULL    | N           | N          | NULL  | NULL       | NULL    | NULL        |
| DATABASE_NAME                          | VARCHAR(16777216) | COLUMN | Y     | NULL    | N           | N          | NULL  | NULL       | NULL    | NULL        |
| SCHEMA_ID                              | NUMBER(38,0)      | COLUMN | Y     | NULL    | N           | N          | NULL  | NULL       | NULL    | NULL        |
| SCHEMA_NAME                            | VARCHAR(16777216) | COLUMN | Y     | NULL    | N           | N          | NULL  | NULL       | NULL    | NULL        |
| QUERY_TYPE                             | VARCHAR(16777216) | COLUMN | Y     | NULL    | N           | N          | NULL  | NULL       | NULL    | NULL        |
| SESSION_ID                             | NUMBER(38,0)      | COLUMN | Y     | NULL    | N           | N          | NULL  | NULL       | NULL    | NULL        |
| USER_NAME                              | VARCHAR(16777216) | COLUMN | Y     | NULL    | N           | N          | NULL  | NULL       | NULL    | NULL        |
| ROLE_NAME                              | VARCHAR(16777216) | COLUMN | Y     | NULL    | N           | N          | NULL  | NULL       | NULL    | NULL        |
| WAREHOUSE_ID                           | NUMBER(38,0)      | COLUMN | Y     | NULL    | N           | N          | NULL  | NULL       | NULL    | NULL        |
| WAREHOUSE_NAME                         | VARCHAR(16777216) | COLUMN | Y     | NULL    | N           | N          | NULL  | NULL       | NULL    | NULL        |
| WAREHOUSE_SIZE                         | VARCHAR(16777216) | COLUMN | Y     | NULL    | N           | N          | NULL  | NULL       | NULL    | NULL        |
| WAREHOUSE_TYPE                         | VARCHAR(16777216) | COLUMN | Y     | NULL    | N           | N          | NULL  | NULL       | NULL    | NULL        |
| CLUSTER_NUMBER                         | NUMBER(38,0)      | COLUMN | Y     | NULL    | N           | N          | NULL  | NULL       | NULL    | NULL        |
| QUERY_TAG                              | VARCHAR(16777216) | COLUMN | Y     | NULL    | N           | N          | NULL  | NULL       | NULL    | NULL        |
| EXECUTION_STATUS                       | VARCHAR(16777216) | COLUMN | Y     | NULL    | N           | N          | NULL  | NULL       | NULL    | NULL        |
| ERROR_CODE                             | VARCHAR(16777216) | COLUMN | Y     | NULL    | N           | N          | NULL  | NULL       | NULL    | NULL        |
| ERROR_MESSAGE                          | VARCHAR(16777216) | COLUMN | Y     | NULL    | N           | N          | NULL  | NULL       | NULL    | NULL        |
| START_TIME                             | TIMESTAMP_LTZ(6)  | COLUMN | Y     | NULL    | N           | N          | NULL  | NULL       | NULL    | NULL        |
| END_TIME                               | TIMESTAMP_LTZ(6)  | COLUMN | Y     | NULL    | N           | N          | NULL  | NULL       | NULL    | NULL        |
| TOTAL_ELAPSED_TIME                     | NUMBER(38,0)      | COLUMN | Y     | NULL    | N           | N          | NULL  | NULL       | NULL    | NULL        |
| BYTES_SCANNED                          | NUMBER(38,0)      | COLUMN | Y     | NULL    | N           | N          | NULL  | NULL       | NULL    | NULL        |
| PERCENTAGE_SCANNED_FROM_CACHE          | FLOAT             | COLUMN | Y     | NULL    | N           | N          | NULL  | NULL       | NULL    | NULL        |
| BYTES_WRITTEN                          | NUMBER(38,0)      | COLUMN | Y     | NULL    | N           | N          | NULL  | NULL       | NULL    | NULL        |
| BYTES_WRITTEN_TO_RESULT                | NUMBER(38,0)      | COLUMN | Y     | NULL    | N           | N          | NULL  | NULL       | NULL    | NULL        |
| BYTES_READ_FROM_RESULT                 | NUMBER(38,0)      | COLUMN | Y     | NULL    | N           | N          | NULL  | NULL       | NULL    | NULL        |
| ROWS_PRODUCED                          | NUMBER(38,0)      | COLUMN | Y     | NULL    | N           | N          | NULL  | NULL       | NULL    | NULL        |
| ROWS_INSERTED                          | NUMBER(38,0)      | COLUMN | Y     | NULL    | N           | N          | NULL  | NULL       | NULL    | NULL        |
| ROWS_UPDATED                           | NUMBER(38,0)      | COLUMN | Y     | NULL    | N           | N          | NULL  | NULL       | NULL    | NULL        |
| ROWS_DELETED                           | NUMBER(38,0)      | COLUMN | Y     | NULL    | N           | N          | NULL  | NULL       | NULL    | NULL        |
| ROWS_UNLOADED                          | NUMBER(38,0)      | COLUMN | Y     | NULL    | N           | N          | NULL  | NULL       | NULL    | NULL        |
| BYTES_DELETED                          | NUMBER(38,0)      | COLUMN | Y     | NULL    | N           | N          | NULL  | NULL       | NULL    | NULL        |
| PARTITIONS_SCANNED                     | NUMBER(38,0)      | COLUMN | Y     | NULL    | N           | N          | NULL  | NULL       | NULL    | NULL        |
| PARTITIONS_TOTAL                       | NUMBER(38,0)      | COLUMN | Y     | NULL    | N           | N          | NULL  | NULL       | NULL    | NULL        |
| BYTES_SPILLED_TO_LOCAL_STORAGE         | NUMBER(38,0)      | COLUMN | Y     | NULL    | N           | N          | NULL  | NULL       | NULL    | NULL        |
| BYTES_SPILLED_TO_REMOTE_STORAGE        | NUMBER(38,0)      | COLUMN | Y     | NULL    | N           | N          | NULL  | NULL       | NULL    | NULL        |
| BYTES_SENT_OVER_THE_NETWORK            | NUMBER(38,0)      | COLUMN | Y     | NULL    | N           | N          | NULL  | NULL       | NULL    | NULL        |
| COMPILATION_TIME                       | NUMBER(38,0)      | COLUMN | Y     | NULL    | N           | N          | NULL  | NULL       | NULL    | NULL        |
| EXECUTION_TIME                         | NUMBER(38,0)      | COLUMN | Y     | NULL    | N           | N          | NULL  | NULL       | NULL    | NULL        |
| QUEUED_PROVISIONING_TIME               | NUMBER(38,0)      | COLUMN | Y     | NULL    | N           | N          | NULL  | NULL       | NULL    | NULL        |
| QUEUED_REPAIR_TIME                     | NUMBER(38,0)      | COLUMN | Y     | NULL    | N           | N          | NULL  | NULL       | NULL    | NULL        |
| QUEUED_OVERLOAD_TIME                   | NUMBER(38,0)      | COLUMN | Y     | NULL    | N           | N          | NULL  | NULL       | NULL    | NULL        |
| TRANSACTION_BLOCKED_TIME               | NUMBER(38,0)      | COLUMN | Y     | NULL    | N           | N          | NULL  | NULL       | NULL    | NULL        |
| OUTBOUND_DATA_TRANSFER_CLOUD           | VARCHAR(16777216) | COLUMN | Y     | NULL    | N           | N          | NULL  | NULL       | NULL    | NULL        |
| OUTBOUND_DATA_TRANSFER_REGION          | VARCHAR(16777216) | COLUMN | Y     | NULL    | N           | N          | NULL  | NULL       | NULL    | NULL        |
| OUTBOUND_DATA_TRANSFER_BYTES           | NUMBER(38,0)      | COLUMN | Y     | NULL    | N           | N          | NULL  | NULL       | NULL    | NULL        |
| INBOUND_DATA_TRANSFER_CLOUD            | VARCHAR(16777216) | COLUMN | Y     | NULL    | N           | N          | NULL  | NULL       | NULL    | NULL        |
| INBOUND_DATA_TRANSFER_REGION           | VARCHAR(16777216) | COLUMN | Y     | NULL    | N           | N          | NULL  | NULL       | NULL    | NULL        |
| INBOUND_DATA_TRANSFER_BYTES            | NUMBER(38,0)      | COLUMN | Y     | NULL    | N           | N          | NULL  | NULL       | NULL    | NULL        |
| LIST_EXTERNAL_FILES_TIME               | NUMBER(38,0)      | COLUMN | Y     | NULL    | N           | N          | NULL  | NULL       | NULL    | NULL        |
| CREDITS_USED_CLOUD_SERVICES            | FLOAT             | COLUMN | Y     | NULL    | N           | N          | NULL  | NULL       | NULL    | NULL        |
| RELEASE_VERSION                        | VARCHAR(16777216) | COLUMN | Y     | NULL    | N           | N          | NULL  | NULL       | NULL    | NULL        |
| EXTERNAL_FUNCTION_TOTAL_INVOCATIONS    | NUMBER(38,0)      | COLUMN | Y     | NULL    | N           | N          | NULL  | NULL       | NULL    | NULL        |
| EXTERNAL_FUNCTION_TOTAL_SENT_ROWS      | NUMBER(38,0)      | COLUMN | Y     | NULL    | N           | N          | NULL  | NULL       | NULL    | NULL        |
| EXTERNAL_FUNCTION_TOTAL_RECEIVED_ROWS  | NUMBER(38,0)      | COLUMN | Y     | NULL    | N           | N          | NULL  | NULL       | NULL    | NULL        |
| EXTERNAL_FUNCTION_TOTAL_SENT_BYTES     | NUMBER(38,0)      | COLUMN | Y     | NULL    | N           | N          | NULL  | NULL       | NULL    | NULL        |
| EXTERNAL_FUNCTION_TOTAL_RECEIVED_BYTES | NUMBER(38,0)      | COLUMN | Y     | NULL    | N           | N          | NULL  | NULL       | NULL    | NULL        |
| QUERY_LOAD_PERCENT                     | NUMBER(38,0)      | COLUMN | Y     | NULL    | N           | N          | NULL  | NULL       | NULL    | NULL        |
| IS_CLIENT_GENERATED_STATEMENT          | BOOLEAN           | COLUMN | Y     | NULL    | N           | N          | NULL  | NULL       | NULL    | NULL        |
+----------------------------------------+-------------------+--------+-------+---------+-------------+------------+-------+------------+---------+-------------+
```

```
desc WAREHOUSE_METERING_HISTORY
```

```
+-----------------------------+-------------------+--------+-------+---------+-------------+------------+-------+------------+---------+-------------+
| name                        | type              | kind   | null? | default | primary key | unique key | check | expression | comment | policy name |
|-----------------------------+-------------------+--------+-------+---------+-------------+------------+-------+------------+---------+-------------|
| START_TIME                  | TIMESTAMP_LTZ(0)  | COLUMN | Y     | NULL    | N           | N          | NULL  | NULL       | NULL    | NULL        |
| END_TIME                    | TIMESTAMP_LTZ(0)  | COLUMN | Y     | NULL    | N           | N          | NULL  | NULL       | NULL    | NULL        |
| WAREHOUSE_ID                | NUMBER(38,0)      | COLUMN | Y     | NULL    | N           | N          | NULL  | NULL       | NULL    | NULL        |
| WAREHOUSE_NAME              | VARCHAR(16777216) | COLUMN | Y     | NULL    | N           | N          | NULL  | NULL       | NULL    | NULL        |
| CREDITS_USED                | NUMBER(38,9)      | COLUMN | Y     | NULL    | N           | N          | NULL  | NULL       | NULL    | NULL        |
| CREDITS_USED_COMPUTE        | NUMBER(38,9)      | COLUMN | Y     | NULL    | N           | N          | NULL  | NULL       | NULL    | NULL        |
| CREDITS_USED_CLOUD_SERVICES | NUMBER(38,9)      | COLUMN | Y     | NULL    | N           | N          | NULL  | NULL       | NULL    | NULL        |
+-----------------------------+-------------------+--------+-------+---------+-------------+------------+-------+------------+---------+-------------+
```

```
SELECT date_trunc(month, usage_date) AS usage_month,
ROUND(AVG(storage_bytes)/power(1024, 4), 3)
  AS billable_database_tb, ROUND(AVG(failsafe_bytes)/power(1024, 4), 3)
  AS billable_failsafe_tb, ROUND(AVG(stage_bytes)/power(1024, 4), 3)
  AS billable_stage_tb, $storage_price *
  (billable_database_tb + billable_failsafe_tb + billable_stage_tb)
  AS total_billable_storage_usd
FROM SNOWFLAKE.ACCOUNT_USAGE.STORAGE_USAGE
WHERE usage_date >= date_trunc(month, dateadd(month, -3, current_timestamp))
AND usage_date < date_trunc(month, current_timestamp)
GROUP BY 1;
```