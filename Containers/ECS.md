---
alias:
- Elastic Container Service
---
- ECS: Elastic Container Service
- Read about [[Docker]] before moving forward

### EC2 Launch Type
- Launch Docker containers on AWS = Launch ECS Tasks on ECS Clusters
- EC2 Launch Type: you must provision & maintain the infrastructure (the EC2 instances)
- Each EC2 instance must run the ECS Agent to register in the ECS Cluster
- AWS takes care of starting / stopping containers![[Screenshot 2023-07-03 at 2.05.39 PM.png]]

- #### Auto Scaling EC2 Instances
	- Accommodate ECS Service Scaling by adding underlying EC2 Instances
	- Auto Scaling Group Scaling
		- Scale your ASG based on CPU Utilization
		- Add EC2 instances over time
	- ECS Cluster Capacity Provider
		- Used to automatically provision and scale the infrastructure for your ECS Tasks
		- Capacity Provider Paired with an Auto Scaling Group
		- Add EC2 Instances when you're missing capacity (CPU, RAM, ...)
	- ECS Scaling - Service CPU Usage Example![[Screenshot 2023-07-03 at 4.32.54 PM.png]]

### Fargate Launch Type
- Launch Docker containers on AWS
- You do not provision the infrastructure (no EC2 instances to Manage)
- It's all Serverless (it is called Serverless because we are not managing it)
- You just create task definitions
- AWS just run ECS Tasks for you based on the CPU/RAM you need
- To scale, just increase the number of tasks. Simple - no more EC2 instances![[Screenshot 2023-07-03 at 2.08.45 PM.png]]

### IAM Roles for ECS
- EC2 instance profile (EC2 Launch Type only)
	- Used by the ECS agent
	- Makes API calls to ECS service
	- Send container logs to CloudWatch Logs
	- Pull Docker image from ECR
	- Reference sensitive data in Secrets Manager or SSM Parameter Store
- ECS Task Role:
	- Allows each task to have a specific role
	- Use different roles for the different ECS Services you run
	- Task Role is defined in the task definition![[Screenshot 2023-07-03 at 2.14.41 PM.png]]


### Load Balancer Integration
- [[ALB]] supported and works for most use cases
- [[NLB]] recommended only for high throughput / high performance use cases, or to pair it with AWS Private Link
- [[CLB]] supported but no recommended (no advanced features - no [[Fargate]])![[Screenshot 2023-07-03 at 2.19.17 PM.png]]

### Data Volumes ([[EFS]])
- Mount EFS file systems onto ECS tasks
- Works for both EC2 and Fargate launch types
- Tasks running in any AZ will share the same data in the EFS file system
- Fargate + EFS = Serverless
- Use cases: persistent multi-AZ shared storage for your containers
- Note:
	- Amazon [[S3]] cannot be mounted as a file system ![[Screenshot 2023-07-03 at 2.22.56 PM.png]]

### Creating ECS Cluster - Hands on
- Go to ECS dashboard
- Click on `Create Cluster`, a new form will open
- Cluster Name
- Networking
	- Choose VPC
	- Subnets
- Infrastructure
	- AWS Fargate (serverless)
		Pay as you go, Use if you have tiny, batch, or burst workloads or for zero maintenance overhead. The cluster has Fargate and Fargate Spot capacity providers by default.
	- Amazon EC2 instances
		Manual configurations. Use for large workloads with consistent resource demands
		- If you select this, you need to create [[Auto Scaling Group]] from the dropdown, and it will create one for you, you can check that in ASG in ASG Dashboard once the Cluster is created
	- External instances using ECS Anywhere
		Manual configurations. Use to add data center compute
- Monitoring (optional)
- Tags (optional)
- Create
- Once the cluster is created, open it
- Under infrastructure tab, check `Capacity Providers`, under it you'll find
	- FARGATE
	- FARGATE_SPOT
	- ASGProvider
- Once the EC2 instance under ASG is created, it can be visible under `Container Instances` under cluster infra
- But you'll see there is no task running under EC2 instance

### Creating ECS Service / Task definition
- Go to ECS dashboard
- To create an ECS service, click on `Task definition` in sidebar
- Create new task definition, a new form will open
- Task definition family name
- Container 1 (container details)
	- Name (`nginxdemos-hello`)
	- Image URI (`nginxdemos/hello`)
	- Essential Container
	- Port mapping
		- Container port (80)
		- Protocol (TCP)
	- Not add any more containers
	- Next
- Configure environment, storage, monitoring, tags
	- App environment (select AWS Fargate)
	- Operating system (Linux/x86_64)
	- Task size
		- CPU (0.5 vCPU)
		- Memory (1GB)
	- Next
- Review and Create
- Once the task definition is created
- Go to EC2 dashboard
- Create new [[Security Groups]]
	- `alb-ecs-sg`
		- Allow port 80 from anywhere
		- Create
	- `nginx-demo-sg`
		- Allow on `ALL TCP` from `alb-ecs-sg` (the SG group created above)
		- Create
- Now go to the cluster we created above, under services tab, click on deploy, a new form will open
- Environment
	- Existing cluster
- Deployment configuration
	- Application type
		- Service
			Launch a group of tasks handling a long running computing work that can be stopped and restarted. For example, a web application.
		- Task
			Launch a standalone task that runs and terminates. For example, a batch job.
	- Family, select `nginxdemos-hello` which we created above
	- Service name (`nginxdemos`)
	- Desired tasks (1)
	- For `Deployment Options`, keep it as it is.
- Load balancing
	- Select ALB
	- Create a new ALB `DemoALBforECS`
	- Port 80
	- target group: `nginx-ecs`
	- Protocol HTTP
	- Health Check path `/`
	- Health check grace period `20 seconds`
- Networking
	- VPC
	- Subnets
	- Security groups
- Create
- Once service is created
- Click on service, check status

### ECS Service Auto Scaling
- Automatically increase/decrease the desired number of ECS tasks
- Amazon ECS Auto Scaling uses AWS Application Auto Scaling
	- ECS Service Average CPU utilization
	- ECS Service Average Memory Utilization - Scale on RAM
	- ALB Request Count Per Target - metric coming from the ALB
- Target Tracking - scale based on target value for a specific CloudWatch metric
- Step Scaling - scale based on a specified CloudWatch Alarm
- Scheduled Scaling - scale based on a specified date/time (predictable changes)
- ECS Service Auto Scaling (task level) != EC2 Auto Scaling (EC2 instance level)
- Fargate Auto Scaling is much easier to setup (because Serverless)

### Rolling Updates
- When updating from v1 to v2, we can control how many tasks can be started and stopped, and in which order!![[Screenshot 2023-07-03 at 4.34.41 PM.png]]![[Screenshot 2023-07-03 at 4.34.54 PM.png]]
- Min 50%, max 100%, starting number tasks: 4![[Screenshot 2023-07-03 at 4.37.15 PM.png]]
- Min 100%, Max 150%, Starting number of tasks: 4![[Screenshot 2023-07-03 at 4.38.44 PM.png]]

### Solution Architectures
- ECS tasks invoked by Event Bridge![[Screenshot 2023-07-03 at 4.40.27 PM.png]]
- ECS tasks invoked by Event Bridge Schedule![[Screenshot 2023-07-03 at 4.41.19 PM.png]]
- SQS Queue Example![[Screenshot 2023-07-03 at 4.42.02 PM.png]]
- Intercept Stopped tasks using EventBridge![[Screenshot 2023-07-03 at 4.43.16 PM.png]]


## Task Definitions
- Task definitions are metadata in JSON form to tell ECS how to run a Docker container
- It contains crucial information, such as:
	- Image Name
	- Port Binding for Container and Host
	- Memory and CPU required
	- Environment variables
	- Networking information
	- IAM Role
	- Logging configuration (eg: CloudWatch)
- Can define up to 10 containers in a Task Definition

### Load Balancing (EC2 Launch Type)
- We get a Dynamic Host Port Mapping if you define only the container port in the task definition
- The ALB finds the right port on your EC2 instances
- You must allow on the EC2 instance's Security Group any port from the ALB's Security Group![[Screenshot 2023-07-04 at 12.49.06 PM.png]]
### Load Balancing (Fargate)
- Each task has a unique private IP
- Only define the container port (host port is not applicable)
- Example
	- ECS ENI Security Group
		- Allow port 80 from the ALB
	- ALB Security Group
		- Allow port 80/443 from web![[Screenshot 2023-07-04 at 12.51.32 PM.png]]

### One IAM Role per Task Definition![[Screenshot 2023-07-04 at 12.52.42 PM.png]]


### Environment Variables
- Hardcoded - eg: URLs
- SSM Parameter Store - sensitive variables (eg: API keys, shared configs)
- Secrets Manager - sensitive variables (eg: DB passwords)
- Environment Files (bulk) - Amazon S3![[Screenshot 2023-07-04 at 12.55.06 PM.png]]


### Data Volumes (Bind Mount)
- Share data between multiple containers in the same Task Definition
- Works for both EC2 and Fargate tasks
- EC2 Tasks - using EC2 instance storage
	- Data are tied to the lifecycle of the EC2 instance
- Fargate Tasks - using ephemeral storage
	- Data are tied to the container(s) using them
	- 20 GiB - 200 GiB (default 20 GiB)
- Use cases:
	- Share ephemeral data between multiple containers
	- "Sidecar" container pattern, where the "Sidecar" container used to send metrics/logs to other destinations (separation of concerns)![[Screenshot 2023-07-04 at 12.58.59 PM.png]]

### ECS Tasks Placement
- When a task of type EC2 is launched, ECS must determine where to place it, with the constraints of CPU, memory, and available port.
- Similarly, when a service scales in, ECS needs to determine which task to terminate.
- To assist with this, you can define a task placement strategy and task placement constraints
- Note: this is only for ECS with EC2, not for Fargate
- #### Task placement process
	- Task placement strategies are a best effort
	- When Amazon ECS places tasks, it uses the following process to select container instances
	1. Identify the instances that satisfy the CPU, memory, and port requirements in the task definition.
	2. Identify the instances that satisfy the task placement constraints
	3. Identify the instances that satisfy the task placement strategies.
	4. Select the instances for task placement.

### ECS Task Placement Strategies
- #### Binpack
	- Place tasks based on the least available amount of CPU or memory
	- This minimizes the number of instances in use (cost savings)![[Screenshot 2023-07-04 at 4.21.44 PM.png]]
	```json
	"placementStrategy": [
		{
			"field": "memory",
			"type": "binpack"
		}
	]
	```
- #### Random
	- Place the task randomly![[Screenshot 2023-07-04 at 4.23.25 PM.png]]
	```json
	"placementStrategy": [
		{
			"type": "random"
		}
	]
	```
- #### Spread
	- Place the task evenly based on the specified value
	- Example: instanceId, `attribute:ecs.availability-zone`![[Screenshot 2023-07-04 at 4.25.11 PM.png]]
	```json
	"placementStrategy": [
		{
			"field": "attribute:ecs.availability-zone",
			"type": "spread"
		}
	]
	```

- You can mix these strategies together![[Screenshot 2023-07-04 at 4.26.48 PM.png]]
### ECS Task Placement Constraints
- **distinctInstance**: place each task on a different container instance
	```json
"placementConstraints": [
	{
		"type": "distinctInstance"
	}
]
	```
- **memberOf**: places task on instances that satisfy an expression
	- Uses the Cluster Query Language (advanced)
	```json
	"placementConstraints": [
		{
			"expression": "attribute:ecs.instance-type =~ t2.**",
			"type": "memberOf"
		}
	]
	```
	