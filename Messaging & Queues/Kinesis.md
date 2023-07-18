- Makes it easy to collect, process and analyze streaming data in real time
- Ingest real-time data such as : Application logs, Metrics, Website clickstreams, IoT telemetry data...
- There are 4 Kineses streams
- ### Kinesis Data Streams
	- Capture, process and store data streams
		  ![Screenshot 2023-07-10 at 6.26.55 PM](../images%201/Screenshot%202023-07-10%20at%206.26.55%20PM.png)
	- Retention between 1 day to 365 days
	- Ability to reprocess (replay) data
	- Once data is inserted in Kinesis, it can't be deleted (immutability)
	- Data that shares the same partition goes to the same shard (ordering)
	- Producers: AWS SDK, Kinesis Producer Library (KPL), Kinesis Agent
	- Consumers:
		- Write your own: Kinesis Client Library (KCL), AWS SDK
		- Managed: AWS Lambda, Kinesis Data Firehose, Kinesis Data Analytics
	- #### Capacity Modes
	- Provisioned Mode:
		- You choose the number of shards provisioned, scale manually or using API
		- Each shard gets 1MB/s in (or 1000 records per second)
		- Each shard gets 2MB/s out (classic or enhanced fan-out consumer)
		- You pay per shard provisioned per hour
	- On-demand mode
		- No need to provision or manage the capacity
		- Default capacity provisioned (4 MB/s in or 4000 records per second)
		- Scales automatically based on observed throughput peak during the last 30 days
		- Pay per stream per hour & data in/out per GB
	- #### Security
	- Control access / authorization using IAM policies
	- Encryption in flight using HTTPS endpoints
	- Encryption at rest using KMS
	- You can implement encryption/decryption of data on client side (harder)
	- VPC Endpoints available for Kinesis to access within VPC
	- Monitor API calls using CloudTrail
	- #### Hands on
	- Go to Kinesis dashboard
	- Select Kinesis Data Streams and Create
	- Name
	- Stream Capacity
		- On-demand
		- Provisioned
	- Create
	- Once created, under application tab, there are three options for producers
	- Producers
		- Amazon Kinesis Agent
		- AWS SDK
		- Amazon Kinesis Producer Library (KPL)
	- Consumers
		- Amazon Kinesis Data Analytics
		- Amazon Kinesis Data Firehose
		- Amazon Kinesis Client Library (KCL)
- ### Kinesis Data Firehose ^0a78d3
	- Load data streams into AWS data stores
		  ![Screenshot 2023-07-10 at 7.37.05 PM](../images%201/Screenshot%202023-07-10%20at%207.37.05%20PM.png)
	- Fully managed service, no administration, automatic scaling, Serverless
		- AWS: Redshift / Amazon S3 / OpenSearch
		- 3rd party partner: Splunk / MongoDB / DataDog / NewRelic / ...
		- Custom: send to any HTTP endpoint
	- Pay for data going through Firehose
	- Near Real Time
		- 60 seconds latency minimum for non-full batches
		- Or minimum 1 MB of data at a time
	- Supports many data formats, conversions, transformations, compression
	- Supports custom data transformation using AWS Lambda
	- Can send failed or all data to a backup S3 bucket
- ### Kinesis Data Analytics
	- Analyze data streams with SQL or Apache Flink
		  ![Screenshot 2023-07-10 at 7.51.38 PM](../images%201/Screenshot%202023-07-10%20at%207.51.38%20PM.png)
	- SQL Application
		- Real time analytics on Kinesis Data Streams & Firehose using SQL
		- Add reference data from Amazon S3 to enrich streaming data
		- Fully managed, no servers to provision
		- Automatic scaling
		- Pay for actual consumption rate
		- Output
			- Kinesis Data Streams: create streams out of the real-time analytics queries
			- Kinesis Data Firehose: send analytics query results to destinations
		- Use cases:
			- Time-series analytics
			- Real-time dashboards
			- Real-time metrics
	- Apache Flink
		- Use Flink (Java, Scala or SQL) to process and analyze streaming data
			  ![Screenshot 2023-07-10 at 7.55.50 PM](../images%201/Screenshot%202023-07-10%20at%207.55.50%20PM.png)
		- Run any Apache Flink application on a managed cluster on AWS
			- Provisioning compute resources, parallel computation, automatic scaling
			- Application backups (implemented as checkpoints and snapshots)
			- Use any Apache Flink programming features
			- Flink does not read from Firehose (use Kinesis Analytics for SQL instead)
- ### Kinesis Video Streams
	- Capture, process, and store video streams

## Kinesis Producers
- Puts data records into data streams
- Data record consists of
	- Sequence number (unique per partition-key shard)
	- Partition key (must specify while put records into stream)
	- Data blob (up to 1MB)
- Producers:
	- AWS SDK: simple producer
	- Kinesis Producer Library (KPL): C++, Java, batch, compression, retries
	- Kinesis Agent: monitor log files
- Write throughput: 1 MB/sec or 1000 records/sec per shard
- PutRecord API
- Use batching with PutRecords API to reduce costs & increase throughput
	  ![Screenshot 2023-07-10 at 6.50.20 PM](../images%201/Screenshot%202023-07-10%20at%206.50.20%20PM.png)
- ProvisionedThroughputExceeded
	- Use highly distributed partition key
	- Retried with exponential backoff
	- Increase shards (scaling)
		  ![Screenshot 2023-07-10 at 6.52.07 PM](../images%201/Screenshot%202023-07-10%20at%206.52.07%20PM.png)

## Kinesis Consumers
- Get data records from data streams and process them
- AWS Lambda
- Kinesis Data Analytics
- Kinesis Data Firehose
- Custom Consumer (AWS SDK) - Classic or Enhanced Fan-Out
- Kinesis Client Library (KCL): Library to simplify reading from data stream
- Custom Consumer
	- Shared (Classic) Fan-Out Consumer
		- Low number of consuming applications
		- Read throughput: 2MB/sec per shard across all consumers
		- Max. 5 GetRecords API calls/sec
		- Latency ~200 ms
		- Minimize cost ($)
		- Consumers poll data from Kinesis using GetRecords API call
		- Returns up to 10 MB (then throttle for 5 seconds) or up to 10000 records
		  ![Screenshot 2023-07-10 at 6.55.27 PM](../images%201/Screenshot%202023-07-10%20at%206.55.27%20PM.png)
	- Enhanced Fan-out Consumer
		- Multiple consuming applications for same streams
		- 2 MB/sec per consumer per shard
		- Latency ~70ms
		- Higher costs (\$\$\$)
		- Kinesis pushes data to consumers over HTTP/2 (SubscribeToShardAPI)
		- Soft limit of 5 consumer applications (KCL) per data stream (default)
		  ![Screenshot 2023-07-10 at 6.55.50 PM](../images%201/Screenshot%202023-07-10%20at%206.55.50%20PM.png)
- #### Consumer - AWS Lambda
- Supports Classic & Enhanced fan-out consumers
- Read records in batches
- Can configure batch size and batch windows
- If error occurs, Lambda retries until succeeds or data expired
- Can process up to 10 batches per shard simultaneously
	  ![Screenshot 2023-07-10 at 7.02.20 PM](../images%201/Screenshot%202023-07-10%20at%207.02.20%20PM.png)

## Kinesis Client Library (KCL)
- A Java library that helps read record from a Kinesis Data Stream with distributed applications sharing the read workload
- Each shard is to be read by only one KCL instance
	- 4 shards = max. 4 KCL instances
	- 6 shards = max. 6 KCL instances
- Progress is check-pointed into DynamoDB (needs IAM access)
- Track other workers and share the work amongst shards using DynamoDB
- KCL can run on EC2, Elastic Beanstalk, and on-premises
- Records are read in order at the shard level
- Versions:
	- KCL 1.x (supports shared consumer)
	- KCL 2.x (Supports shared & enhanced fan-out consumer)
- KCL Example: 4 shards
	  ![Screenshot 2023-07-10 at 7.23.39 PM](../images%201/Screenshot%202023-07-10%20at%207.23.39%20PM.png)
- 4 shards, Scaling KCL App
	  ![Screenshot 2023-07-10 at 7.24.10 PM](../images%201/Screenshot%202023-07-10%20at%207.24.10%20PM.png)  
- 6 shards, Scaling Kinesis
	  ![Screenshot 2023-07-10 at 7.25.03 PM](../images%201/Screenshot%202023-07-10%20at%207.25.03%20PM.png)
- 6 shards, scaling KCL App
	  ![Screenshot 2023-07-10 at 7.25.30 PM](../images%201/Screenshot%202023-07-10%20at%207.25.30%20PM.png)

## Kinesis Operations - Shard Splitting
- Used to increase the Stream capacity (1MB/s data in per shard)
- Used to divide a "Hot shard"
- The old shard is closed and will be deleted once the data is expired
- No automatic scaling (manually increase/decrease capacity)
- Can't split into more than two shards in a single operation
	  ![Screenshot 2023-07-10 at 7.33.04 PM](../images%201/Screenshot%202023-07-10%20at%207.33.04%20PM.png)
### Kinesis Operations - Merging Shards
- Decrease the Stream capacity and save costs
- Can be used to group two shards with low traffic (cold shards)
- Old shards are closed and will be deleted once the data is expired
- Can't merge more than two shards in a single operation

### Kinesis Data Stream vs Firehose

Kinesis Data Stream | Kinesis Data Firehose
--- |---
Streaming service for ingest at scale | Load streaming data into S3, Redshift, OpenSearch, 3rd party, custom HTTP
Write custom code (producer / consumer) | Fully managed
Real-time (~200ms) | Near real-time (buffer time min 60 sec)
Manage scaling (shard splitting / merging) | Automatic scaling
Data storage for 1 to 365 days | No data storage
Supports replay capability | Doesn't support replay capability

# [SQS](SQS.md) vs [SNS](SNS.md) vs [Kinesis](.md)

SQS | SNS | Kinesis
--- |--- |---
Consumer "puil data" | Push data to many subscribers | Standard: pull data: 2 MB per shard
Data is deleted after being consumed | Up to 12,500, 000 subscribers | Enhanced fan-out: push data: 2 MB per shard per consumer
Can have as many workers (consumers) as we want | Data is not persisted (list if not delivered) | Possibility to replay data
No need to provision throughput | Pub/Sub | Meant for real-time big data, analytics and ETL
Ordering guarantees only on FIFO queues | Up to 100,000 topics | Ordering at the shard level
Individual message delay capabilty | No need to provision throughput | Data expires after X days
-- | Integrates with SQS for fan-out architecture pattern | Provisioned mode or on-demand capacity mode
-- | FIFO capability for SQS FIFO | --
