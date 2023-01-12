# Build Real Time Streaming Pipeline with Apache Hudi Kinesis and Flink 


<img width="725" alt="1" src="https://user-images.githubusercontent.com/39345855/212204944-af76dbb6-b9d8-4d23-885a-86965c8c4777.PNG">

# Watch the video Guide 

# Steps 
### Step 1: Create kinesis Streams 
### step 2: upload the jar provided in github repo to S3
### step 3: Head over to Kinesis Data Analytics and create a Notebook and upload the jar files while creating notebook 

### step 4 : execute sql commands 

```
%flink.conf
execution.checkpointing.interval 5000

```

```
%flink.ssql(type=update)

DROP TABLE if exists stock_table;

CREATE TABLE stock_table (
    uuid varchar,
    ticker VARCHAR,
    price DOUBLE,
    event_time TIMESTAMP(3),
    WATERMARK FOR event_time AS event_time - INTERVAL '5' SECOND
)
PARTITIONED BY (ticker)
WITH (
    'connector' = 'kinesis',
    'stream' = 'input-streams',
    'aws.region' = 'us-west-2',
    'scan.stream.initpos' = 'LATEST',
    'format' = 'json',
    'json.timestamp-format.standard' = 'ISO-8601'
);
```

```

%flink.ssql(type=update)

DROP TABLE if exists stock_table_hudi;

CREATE TABLE stock_table_hudi(
    uuid varchar  ,
    ticker VARCHAR,
    price DOUBLE,
    event_time TIMESTAMP(3)
)
PARTITIONED BY (ticker)
WITH (
    'connector' = 'hudi',
    'path' = 's3a://XXXXXXXX/tmp/',
    'table.type' = 'MERGE_ON_READ' ,
    'hoodie.embed.timeline.server' = 'false'
);

```

```
%ssql
INSERT INTO stock_table_hudi SELECT * FROM stock_table;

```


## Enjoy 
