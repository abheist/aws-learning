- Formerly CloudWatch Events
- Schedule : Cron Jobs (Scheduled Scripts)
	  ![[Screenshot 2023-07-12 at 10.20.11 PM.png]]
- Event Pattern: Event rules to react to a service doing something
	  ![[Screenshot 2023-07-12 at 10.20.20 PM.png]]
- Trigger Lambda functions, send SQS/SNS messages...
- Amazon EventBridge Rules
	  ![[Screenshot 2023-07-12 at 10.22.26 PM.png]]
- Event buses can be accessed by other AWS accounts using resource-based Policies
- You can archive events (all/filter) sent to an event bus (indefinitely or set period)
- Ability to replay achieved events
	  ![[Screenshot 2023-07-12 at 10.23.34 PM.png]]
- Schema Registry
	- EventBridge can analyze the events in your bus and infer the schema
	- The Schema Registry allows you to generate code for your application, that will know in advance how data ia structured in the event bus
	- Schema can be versioned
- Resource based policy
	- Manage permissions for a specific Event Bus
	- Example: allow/deny events from another AWS account or AWS region
	- Use case: aggregate all events from your AWS Organization in a single AWS
		  ![[Screenshot 2023-07-12 at 10.28.03 PM.png]]
- Multi-account aggregation
	  ![[Screenshot 2023-07-12 at 10.38.34 PM.png]]
