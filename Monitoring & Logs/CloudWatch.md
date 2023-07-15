CloudWatch is the central monitoring and logging service in AWS. Each AWS Service reports its metrics and metrics directly to CloudWatch

CloudWatch Products
### Logs
Collect, monitor, analyze and store log files
The central logging place. Each service logs to CloudWatch. You can also add agents to your custom services to log into CloudWatch.
- Log Event
	The actual log itself
- Log Stream
	A stream of multiple log events
- Log Group
	A log group is a container that holds multiple log streams. Typically, one resource (like on Lambda function) has one dedicated log group.

- Log Event
	**Each log event contains the following information**
	- **Timestamp**: the time of your log
	- **Start**: an identifier when your logs event starts
	- **End**: an identifier once your log event ends
	- **RequestID**: A unique request ID to this one logs event

- Log groups: arbitrary name, usually representing an application
- Log stream: Instances within application / log files / containers
- Can define log expiration policies (never expire, 1 day to 10 years...)
- CloudWatch Logs can send logs to:
	- Amazon S3 (exports)
	- Kinesis Data Streams
	- Kinesis Data Firehose
	- AWS Lambda
	- OpenSearch
- Logs are encrypted by default
- Can setup KMS-based encryption with your own keys
- Sources:
	- SDK, CloudWatch Logs Agent, CloudWatch Unified Agent
	- Elastic Beanstalk: collection of logs from application
	- ECS: collection from containers
	- AWS Lambda: collection from function logs
	- APC Flow Logs: VPC specific logs
	- API Gateway
	- CloudTrail based on filter
	- Route53: Log DNS queries
- CloudWatch Logs Insight
	  ![[Screenshot 2023-07-12 at 8.14.32 PM.png]]
	- Search and analyze logs data stored in CloudWatch Logs
	- Example: find a specific IP inside a logs, count occurrences of "ERROR" in your logs...
	- Provides a purpose-build query language
		- Automatically discovers fields from AWS services and JSON log events
		- Fetch desired event fields, filter based on conditions, calculate aggregate statistics, sort events, limit number of events...
		- Can save queries and add them to CloudWatch Dashboards
	- Can query multiple Log Group in different AWS accounts
	- It's a query engine, not a real-time engine
- Exports
	- S3 Export
		- Log data can take up to 12 hours to become available for export
		- The API call is CreateExportTask
		- Not near real-time or real-time... use Logs Subscription instead
- CloudWatch Logs Subscriptions:
	- Get a real-time log events from CloudWatch Logs for processing and analysis
	- Send to Kinesis Data Streams, Kinesis Data Firehose, or Lambda
	- Subscription Filter - filter which logs are events delivered to your destination
		  ![[Screenshot 2023-07-12 at 8.22.02 PM.png]]
- CloudWatch Logs Aggregation Multiple-Account & Multi Region
	  ![[Screenshot 2023-07-12 at 8.23.34 PM.png]]
- CloudWatch Logs Subscriptions
	  ![[Screenshot 2023-07-12 at 8.24.35 PM.png]]
	- Cross Account Subscription: send log events to resources in a different AWS account (KDS, KDF)
- CloudWatch Logs to EC2
	- By Default, no logs from your EC2 machine will go to CloudWatch
	- You need to run a CloudWatch agent on EC2 to push the log files you want
	- Make sure IAM permissions are correct
	- The CloudWatch logs agent can be setup on-premises too.
		  ![[Screenshot 2023-07-12 at 8.31.24 PM.png]]
- CloudWatch Logs Agent & Unified Agent
	- For virtual servers (EC2 instances, on-premise servers...)
	- CloudWatch Logs Agent
		- Old version of the agent
		- Can only send to CloudWatch Logs
	- CloudWatch Unified Agent
		- Collect additional system-level metrics such as RAM, processes, etc...
		- Collect logs to send to CloudWatch Logs
		- Centralized configuration using SSM Parameter Store
		- Metrics:
			- Collect directly on your Linux server / EC2 instance
			- CPU (active, guest, idle, system, user, steal)
			- Disk metrics (free, used, total), Disk IO (writes, reads, bytes, iops)
			- RAM (free, inactive, used, total, cached)
			- Netstat (number of TCP and UDP connections, net packets, bytes)
			- Processes (total, dead, bloqued, idle, running, sleep)
			- Swap Space (free, used, used%)
			- Reminder: out-of-the-box metrics for EC2 - disk, CPU, network (high level)
- CloudWatch Logs Metric Filter:
	- CloudWatch logs can use filter expressions
		- For example, find a specific IP inside of a log
		- Or count occurrences of "ERROR" in your logs
		- Metric filter can be used to trigger alarms
	- Filters do not retroactively filter data. Filters only publish the metric data points for events that happen after the filter was created
	- Ability to specify up to 3 dimensions for the Metric Filter (optional)
		  ![[Screenshot 2023-07-12 at 8.40.33 PM.png]] 

### Metrics
Collect and track key metrics
Each service collects metrics. For example, AWS Lambda collects the number of innovations, errors, and timeouts. These can be really helpful for understanding how your system behaves.
- CloudWatch provides metrics for every services in AWS
- Metric is a variable to monitor (CPUUtilization, Networking..)
- Metrics belongs to namespaces
- Dimension in an attribute of a metric (instance id, environment, etc...)
- Up to 30 dimension per metric
- Metrics have timestamps
- Can create CloudWatch dashboard of metrics
#### EC2 Detailed monitoring
- EC2 instance metrics have metrics "every 5 minutes"
- With detailed monitoring (for a cost), you get data "every 1 minute"
- Use detailed monitoring if you want to scale faster for you ASG!
- The AWS Free Tier allows us to have 10 detailed monitoring metrics
- The AWS Free Tier allows us to have 10 detailed monitoring metrics
- Note: EC2 Memory usage is by default not pushed (must be pushed from inside the instance as a custom metric)
#### CloudWatch Custom Metrics
- Possibility to define and send your own custom metrics to CloudWatch
- Example: memory (RAM) usage, disk space, number of logged in users...
- Use API call PutMetricData
- Ability to use dimensions (attributes) to segment metrics
	- Instance.id
	- Environment.name
- Metric resolution (StorageResolution API parameter - two possible value):
	- Standard: 1 minute (60 seconds)
	- High Resolution: 1/5/10/30 second(s) - High cost
- Important: Accept metric data point two weeks in the past and two hours in the future (make sure to configure your EC2 instance time correctly)
- 



## Alarms
React in real-time to metrics / events
Alarms notify you in case of outages or problems. Alarms are based on metrics.
CloudWatch Alarms are important as they notify you before your customers experience any issues in your system.

![[Pasted image 20230531193356.png]]

They act on top of CloudWatch metrics and notify you when a system or service reaches a pre-defined threshold like errors or usage.

**Defining Alarm States**
Alarms can have three states:
- ALARM: Alarm, check it out.
- OK: Everything good.
- INSUFFICIENT_DATA: Data is missing for evaluating (can also be treaded as ALARM  or OK)

#### Types of Alarm
- [[Metric Alarms]]: are based on one metric![[Pasted image 20230531193356.png]]
- [[Composite Alarms]]: take several alarms into account and their states ![[Pasted image 20230531193326.png]]

#### Examples:
Alarms are highly dependent on your business logic. Some good examples are:
- Messages available in DLQ
- Concurrent executions near limit in Lambda
- Errors in Lambda
- Throttles in DynamoDB
- 500 errors in API Gateway
Make sure to take in account all AWS quotas (service, regional, account)

#### CloudWatch Alarms and [[SNS]]
CloudWatch alarms uses SNS to send notifications like emails, SMS, or In-app Notifications.
You can also attach a Lambda function to the SNS topic to send notifications via Slack, MS Teams, or Discord.
![[Pasted image 20230531192701.png]]
#### Testing Alarms
You can test alarms with the [[AWS CLI]]. Set the alarm with the alarm name into ALARM and see if you receive the notification like expected.
```shell
aws cloudwatch set-alarm-state --alarm-name XX --state-reason --state-value ALARM|OK
```


### Resources
- https://blog.awsfundamentals.com/the-benefits-of-using-cloudwatch-alarms-in-your-aws-environment

- Alarms are used to trigger notifications for any metric
- Various options (sampling, %, max, min, etc...)
- Alarm States:
	- OK
	- INSUFFICIENT_DATA
	- ALARM
- Period
	- Length of time in seconds to evaluate the metic
	- High resolution custom metrics: 10 sec, 30 sec or multiple of 60 sec
- CloudWatch Alarm Targets
	- Stop, Terminate, Reboot or Recover an EC2 instance
	- Trigger Auto Scaling Action
	- Send notification to SNS (from which you can do pretty much anything)
- Composite Alarms
	- CloudWatch Alarms are on a single metric
	- Composite Alarms are monitoring the states of multiple other alarms
	- AND and OR conditions
	- Helpful to reduce "alarm noise" by creating complex composite alarms
- EC2 Instance Recovery
	- Status Check:
		- Instance status = check the EC2 VM
		- System status = check the underlying hardware
			  ![[Screenshot 2023-07-12 at 8.51.58 PM.png]]
		- Recovery: same private, public, Elastic IP, metadata, placement group
- Good to know
	- Alarms can be created based on CloudWatch Logs Metics Filters
	- To test alarms and notifications, set the alarm state to Alarm using CLI
	```sh
	aws cloudwatch set-alarm-state --alarm-name "myalarm" --state-value ALARM --state-region "testing purposes"
	```

## Events
Send notifications when certain events happen in your AWS

- [[X-Ray]]
	X-Ray helps you trace requests across your different services.
- [[Synthetics]]
	  Synthetics are custom scripts or browser-based scripts that check your application's health regularly.
	 - Configure script that monitor your APIs, URLs, Websites...
	 - Reproduce what your customer do programmatically to find issues before customers are impacted
	 - Checks the availability and latency of your endpoints and can store load time data and screenshots of the UI
	 - Integration with CloudWatch Alarms
	 - Scripts written in NodeJS or Python
	 - Programmatically access to a headless Google Chrome Browser
		   ![[Screenshot 2023-07-12 at 9.00.03 PM.png]]
	- Blueprints
		- Heartbeat Monitor - load URL, store screenshot and an HTTP archive file
		- API Canary - test basic read and write functions of REST APIs
		- Broken Link Checker - check all links inside the URL that you are testing
		- Visual Monitoring - compare a screenshot taken during a canary run with a baseline screenshot
		- Canary Recorder - used with CloudWatch Synthetics Recorder (record your actions on a website and automatically generates a script for that)
		- GUI Workflow Builder - verifies that actions can be taken on your webpage (eg: test a webpage with a login form)
- [[Evidently]]
	Evidently allows you to try out different configurations for different customers.


### Benefits to CloudWatch
- Central Logging space for all the AWS services and can be used to store logs of your application as well
- [[CloudWatch Alarms]] :  Trigger emails to devs before user know the service is down or any error occurs to the user.

### Resources
- [Understanding CloudWatch: A Comprehensive Guide to AWS Monitoring Service](https://blog.awsfundamentals.com/aws-cloudwatch-monitoring)
- [Analyze Your Logs with Ease with CloudWatch Insights](https://blog.awsfundamentals.com/analyze-your-logs-with-ease-with-cloudwatch-insights)
- [AWS CloudWatch Logs: The Comprehensive Guide for Log Analysis and Insights](https://blog.awsfundamentals.com/aws-cloudwatch-logs-the-comprehensive-guide-for-log-analysis-and-insights)
- [CloudWatch Log Browsing via your Terminal](https://blog.awsfundamentals.com/cloudwatch-log-browsing-via-your-terminal)

