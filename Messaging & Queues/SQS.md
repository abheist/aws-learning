---
aliases:
- Simple Queue Service
---

- Simple Queue Service![[Screenshot 2023-07-09 at 10.48.46 AM.png]]
## Standard Queue
- Oldest offering
- Fully managed service, used to decouple applications
- Attributes
	- Unlimited throughput, unlimited number of messages in queue
	- Default retention of messages: 4 days, maximum of 14 days
	- Low latency (<10 ms on publish and receive)
	- Limitations of 256KB per message sent
- Can have duplicate messages (at least once delivery occasionally)
- Can have out of order messages (best effort ordering)
- #### Producing messages
- Produced to SQS using the SDK (SendMessage API)
- The message is persisted in SQS until a consumer deletes it
- Message retention: default 4 days, up to 14 days
- Example: send an order to be processed
	- order id
	- customer id
	- any attributes you want
- SQS standard: unlimited throughput
- #### Consuming messages
- Consumers (running on EC2 instances, servers, or AWS Lambda)
- Poll SQS for messages (receive up to 10 messages at a time)
- Process the messages (example: insert the message into an RDS database)
- Delete the messages using the DeleteMessage API![[Screenshot 2023-07-09 at 10.57.11 AM.png]]
- #### Multiple EC2 instances consumers
- Consumers receive and process messages in parallel
- At least once delivery
- Best effort message ordering
- Consumers delete messages after processing them
- We can scale consumers horizontally to improve throughput of processing![[Screenshot 2023-07-09 at 10.59.45 AM.png]]
- #### SQS with [[Auto Scaling Group|AutoScaling Group]] (ASG)
		  ![[Screenshot 2023-07-09 at 11.02.08 AM.png]]
- #### SQS to decouple between application tiers
		  ![[Screenshot 2023-07-09 at 12.17.28 PM.png]]

### Security
- Encryption
	- in-flight encryption using HTTPS API
	- At-rest encryption using KMS keys
	- Client-side encryption if the client wants to perform encryption/decryption itself
- Access Control: IAM policies to regulate access to the SQS API
- SQS Access Policies (similar to S3 policies)
	- Useful for cross-account access to SQS queues
	- Useful for allowing other services (SNS, S3...) to write to an SQS queue

### Hands on
- Got to SQS dashboard
- Click on `Create queue`, a form will be open
- Details
	- Type
		- Standard
			  At-least-once delivery, message ordering isn't preserved (bEst effort ordering)
		- FIFO
			First-in-first-out delivery, message ordering is preserved
	- Name
- Configuration
	- Visibility timeout
	- Message retention period
	- Delivery delay
	- Maximum message size
	- Receive message wait time
- Encryption
	  Amazon SQS provides in-transit encryption be default. To add at-rest encryption to your queue, enable server-side encryption
	- Enabled/disabled
	- Encryption key type
		- Amazon SQS Key (SSE-SQS)
		- AWS Key management service key (SSE-KMS)
- Access Policy
	- Basic
		- Define who can send message to the queue
			- Only the queue owner
			- Only the specified AWS accounts, IAM users and roles
		- Define who can receive messages from the queue
			- Only the queue owner
			- Only the specified AWS accounts, IAM users and roles
	- Advanced
		  Use a JSON object to define an advanced access policy
- Skip other steps and create
- Once created, you can see queue details under tabs
- Click on `Send and receive message` button at top right, a form will open
- Send message
	- Message body
	- Delivery delay
- Receive messages
	- You can click on `Poll for messages` button to pick the message

### SQS Queue Access Policy
- Cross Account Access
	  ![[Screenshot 2023-07-09 at 4.58.24 PM.png]]
- Public S3 Event Notifications to SQS Queue
	- For triggering an event from S3, you need to go to S3 bucket and setup a Event notification setttings
	  ![[Screenshot 2023-07-09 at 4.59.10 PM.png]]

### Message Visibility Timeout
- After a message is polled by a consumer, it becomes invisible to other consumers
- By default, the "message visibility" timeout is 30 seconds
- That means the message has 30 seconds to be processed
- After the message visibility timeout is over, the message is "visible" in SQS![[Screenshot 2023-07-09 at 5.06.21 PM.png]]
- If a message is not processed within the visibility timeout, it will be processed twice
- A consumer could call the `ChangeMessageVisibility` API to get more time
- If visibility timeout is high (hours), and consumer crashes, re-processing will take time.
- If visibility timeout is too low (seconds), we may get duplicates

### Dead Letter Queue (DLQ)
- If a consumer fails to process a message within the Visibility Timeout.. the message goes back to the queue!
- We can set a threshold of how many times a message can go back to the queue
- After the MaximumReceives threshold is exceeded, the message goes into a dead letter queue (DLQ)
- Useful for debugging!
- DLQ of a FIFO queue must also be a FIFO queue
- DQL of a standard queue must also be a standard queue
- Make sure to process the messages in the DLQ before they expire
- New Queue needs to be made as a DLQ and DLQ settings can be set in the original main queue and can select the new DLQ queue from main queue settings.![[Screenshot 2023-07-09 at 5.21.58 PM.png]]
- #### Redrive to Source
- Feature to help consume messages int he DLQ to understand what is wrong with them
- When our code is fixed, we can redrive the message from the DLQ back into the source queue (or any other queue) in batches without writing custom code![[Screenshot 2023-07-09 at 5.24.08 PM.png]]
- In the DLQ queue, there is button at the top right called `Start DQL redrive` to redrive the messages to the main queue

### Delay Queue
- Delay a message (consumer don't see it immediately) to to 15 minutes
- Default is 0 seconds (message is available right away)
- Can set a default at queue level
- Can override the default on send using the `DelaySeconds` parameter![[Screenshot 2023-07-09 at 5.31.06 PM.png]]
- This setting can be done under `Configurations` when creating a queue to edit the configurations later

### Long Polling
- When a consumer requests messages from the queue, it can optionally "wait" for messages to arrive if there are none in the queue
- This is called Long Polling
- LongPolling decreases the number of API calls made to SQS while increasing the efficiency and latency of your application
- The wait time can be between 1 sec to 20 sec (20 sec preferable)
- Long polling is preferable to short polling
- Long polling can be enabled at the queue level or at the API level using `ReceiveMessageWaitTimeSeconds`![[Screenshot 2023-07-09 at 5.34.46 PM.png]]

### SQS Extended Client
- Message size limit is 256KB, how to send large message, eg: 1GB
- Using the SQS Extended Client
- You can upload the file on S3 and send the pointer to that file in the SQS message, with the help of pointer, consumer can pick that file from the S3 bucket![[Screenshot 2023-07-09 at 5.37.21 PM.png]]

### Must know API
- `CreateQueue` (`MessageRetentionPeriod`)
- `DeleteQueue`
- `PurgeQueue`: delete all the messages in queue
- `SendMessage`
- `ReceieveMessage`
- `DeleteMessage`
- `MaxNumberOfMessages` : default 1, max 10 (for ReceiveMessage API)
- `ReceieveMessageWaitTimeSeconds` : Long Polling
- `ChangeMessageVisibility` : change the message timeout
- Batch APIs for `SendMessage`, `DeleteMessage`, `ChangeMessageVisibility` helps decrease your costs



## FIFO Queue
- First In First Out (ordering of messages in the queue)![[Screenshot 2023-07-09 at 5.45.19 PM.png]]
- Limited throughput 300 msg/s without batching, 3000 msg/s with batching
- Exactly-once send capability (by removing duplicates)
- Messages are processed in order by the consumer
-  Deduplication
	- De-duplication interval is 5 minutes
	- Two de-duplication methods
		- Content-based deduplication: will do a SHA-256 hash of the message body
		- Explicitly provide a Message Deduplication ID![[Screenshot 2023-07-09 at 5.51.24 PM.png]]
- Message Grouping
	- If you specify the same value of `MessageGroupID` in an SQS FIFO queue, you can only have one consumer, and all the messages are in order
	- To get ordering at the level of the subset of messages, specify different values for `MessageGroupID`
		- Messages that share a common message GroupID will be in order within the group
		- Each Group ID can have a different consumer (parallel processing!)
		- Ordering across groups is not guaranteed![[Screenshot 2023-07-09 at 5.54.56 PM.png]]
- #### Hands on
- Select `FIFO` as type while creating a queue
- When giving a name to the fifo queue, it should end with `.fifo` extension, otherwise it will not work
	- eg: `DemoQueue.fifo`
- Check other configurations and create
