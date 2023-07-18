---
aliases:
- Simple Notification Service
---

- Simple Notification Service
- What if you want to send one message to many receivers ?
	  ![Screenshot 2023-07-10 at 3.17.33 PM](../images%201/Screenshot%202023-07-10%20at%203.17.33%20PM.png)
- The "Event Producer" only sends message to one SNS topic
- As many "Event Receivers" (subscriptions) as we want to listen to the SNS topic notifications
- Each subscriber to the topic will get all the messages (note: new feature to filter messages)
- Up to 12,500,000 subscriptions per topic
	- 100,000 topics limit![Screenshot 2023-07-10 at 3.20.10 PM](../images%201/Screenshot%202023-07-10%20at%203.20.10%20PM.png)
- SNS integrates with a lot of AWS services, Many AWS services can send data directly to SNS for notifications
	  ![Screenshot 2023-07-10 at 3.21.11 PM](../images%201/Screenshot%202023-07-10%20at%203.21.11%20PM.png)
- #### How to Publish
- Topic Publish (using the SDK)
	- Create a topic
	- Create a subscription (or many)
	- Publish to the topic
- Direct Publish (for mobile app SDK)
	- Create a platform application
	- Create a platform endpoint
	- Publish to the platform endpoint
	- Works with Google GCM, Apple APNS, Amazon ADM...
- ### Security
- Encryption
	- In-flight encryption using HTTPS API
	- At-rest encryption using KMS keys
	- Client-side encryption if the client wants to perform encryption/decryption itself
- Access Control: IAM policies to regulate access to the SNS API
- ANS Access Policies (similar to S3 bucket policies)
	- Useful for cross-account access to SNS topics
	- Useful for allowing other services (S3...) to write to an SNS topic
- ### SNS + SQS: Fan Out Pattern
- Push once in SNS, receive in all SQS queues that are subscribers
	  ![Screenshot 2023-07-10 at 3.27.00 PM](../images%201/Screenshot%202023-07-10%20at%203.27.00%20PM.png)
- Fully decoupled, no data loss
- SQS allows for: data persistence, delayed processing and retries of work
- Ability to add more SQS subscribers over time
- Make sure your SQS queue access policy allows for SNS to write
- Cross region delivery:L works with SQS queues in other regions
- ##### SNS FIFO + SQS FIFO: Fan Out Pattern
- In case you need fan out + ordering + deduplication
	  ![Screenshot 2023-07-10 at 6.00.44 PM](../images%201/Screenshot%202023-07-10%20at%206.00.44%20PM.png)
- ##### Application: S3 Events to multiple queues
- For the same combinations of: event type (eg: object create) and prefix (eg: images/) you can only have ==one S3 Event rule.==
- If you want to send the same S3 event to many SQS queues, use fan out pattern
	  ![Screenshot 2023-07-10 at 3.30.54 PM](../images%201/Screenshot%202023-07-10%20at%203.30.54%20PM.png)
- ##### Application: SNS to Amazon S3 through Kinesis Data Firehose
- SNS can send to [Kinesis](Kinesis.md) and therefore we can have the following solutions architecture:
	  ![Screenshot 2023-07-10 at 5.57.03 PM](../images%201/Screenshot%202023-07-10%20at%205.57.03%20PM.png)
- #### FIFO Topic
- Similar features as SQS FIFO:
	- Ordering by Message Group ID (all messages in the same group are ordered)
	- Deduplication using Deduplication ID or Content Based Deduplication
- Can only have SQS FIFO queues as subscribers
- Limited throughput (same throughput as SQS FIFO)
	  ![Screenshot 2023-07-10 at 5.59.05 PM](../images%201/Screenshot%202023-07-10%20at%205.59.05%20PM.png)
- ### Message Filtering
- JSON policy used to filter messages sent to SNS topic's subscriptions
- If a subscription doesn't have a filter policy, it receives every message
	  ![Screenshot 2023-07-10 at 6.02.40 PM](../images%201/Screenshot%202023-07-10%20at%206.02.40%20PM.png)
- ### Hands on
- Go to SNS Dashboard
- Fill the topic name and create SNS topic, a form will open
- SNS type
	- FIFO
	- Standard
- Name.fifo
- Other configurations and create
- 