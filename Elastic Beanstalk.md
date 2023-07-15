---
alias:
- Beanstalk
---

- Elastic Beanstalk
- Developer problems on AWS which beanstalk can solve
	- Managing Infrastructure
	- Deploying Code
	- Configuring all the databases, load balancers, etc
	- Scaling concerns
	- Most web apps have the same architecture (ALB + ASG)
	- All the developers want is for their code to run!
	- Possibly, consistently across different applications and environments
- Elastic Beanstalk is a developer centric view of deploying an application on AWS
- It uses all the component's we've seen before: EC2, ASG, ELB, RDS,...
- Managed service
	- Automatically handles capacity provisioning, load balancing, scaling, application health monitoring, instance configuration,...
	- Just the application code is the responsibility of the developer
- We still have full control over the configuration
- Beanstalk is free but you pay for the underlying instances

### Components
- Application - collection of Elastic Beanstalk components (environments, versions, configurations,...)
- Application version - an iteration of your application code
- Environment
	- Collection of AWS resources running an application version (only one application version at a time)
	- Tiers: Web Server Environment Tier & Worker Environment Tier
	- You can create multiple environments (dev, test, prod, etc)![[Screenshot 2023-07-05 at 10.36.43 PM.png]]

### Supported Platform
- Go
- Java SE
- Java with Tomcat
- .Net Core on Linux
- .Net on Windows server
- NodeJS
- PHP
- Python
- Ruby
- Packer Builder
- Single Container Docker
- Multi-Container Docker
- Preconfigured Docker
- If not supported, you can write custom platform (advanced)

### Web Server Tier vs. Worker Tier![[Screenshot 2023-07-05 at 10.39.45 PM.png]]

### Elastic Beanstalk Deployment Modes
- Single Instance (great for dev environment)
- High availability with Load Balancer (Great for Prod)

### First Environment
- Go to Beanstalk dashboard
- Click on `Create application`, a new form will open
- Configure Environment
	- Environment Tier
		- Web server environment (select)
			Run a website, web application, or web API that serves HTTP requests.
		- Worker environment
			Run a worker application that processes long-running workloads on demand or performs tasks on a schedule
	- Application information
		- Application Name (myApplication)
		- Application tags
	- Environment Information
		- Environment name (MyApplication-dev)
		- Domain (if leave blank, it will be automatically generated)
	- Platform
		- Platform type
			- Managed platform (select)
				Platforms published and maintained by Amazon Elastic Beanstalk
			- Custom platform
				Platforms created and owned by you. This option is unavailable if you have no platforms
		- Platform (select one) Choose node js or python according to need
		- Platform branch
		- Platform version
	- Application code
		- Sample application (select)
		- Existing Version
		- Upload your code
	- Presets
		- Single instance (free tier eligible) (select)
		- Single instance (using spot instance)
		- High availability
		- High availability (using spot and on-demand instances)
		- Custom configuration
	- Next
- Configure service access
		Service Access:
		IAM roles, assumed by Elastic Beanstalk as a service role, and EC2 instance profiles allow Elastic Beanstalk to create and manage your environment. Both the IAM role and instance profile must be attached to IAM managed policies that contain the required permissions.
	- Service role
		- Create and use new service role (select)
		- Use an existing service role
	- Service role name (it'll be automatically filled when creating a new role, but you can change the value)
	- EC2 instance profile will also be filled an automatically be created, it not then create one role for Elastic Beanstalk
	- Skip to review
- Submit
- It'll create our first Beanstalk environment
- This whole thing is being created by CloudFormation, if you go and open CloudFormation and check the stacks, you'll find one created by Beanstalk. Here CloudFormation is creating resources behind the scenes.

### Second Environment
- [[Elastic Beanstalk#First Environment|Create a new environment again]] for prod
- But select a `High availability` preset
- Set up networking, database, and tags
	- Select VPC, default one
	- Select Subnets we want to launch
	- No need to assign public IP addresses as we'll be using Load Balancers
	- Do not create database from here because if we delete the BeanStalk, our database also be deleted.
	- Next
- Configure instance traffic and scaling
	- Instances, select None
	-  Auto Scaling Group
		- Select `Load Balanced` Environment type
		- 1 min, 4 max instances
		- Fleet composition, select on-demand instances
		- All other settings for [[Auto Scaling Group|ASG]] can be done from this section
	- Load balancer
		- Public
		- Select all subnets needed
	- Load Balancer Type
		- Select [[ALB]] with `Dedicated` (select) or `shared` environment
	- Next
- Configure updates, monitoring, and logging
	- Health reporting
		- System
			- Enhanced (select)
			- Basic
	- Review and Next
- Create

### Beanstalk deployment options for updates
- **All at once** (deploy all in one go): fastest, but instances aren't available to serve traffic for a bit (downtime)
	- Fastest deployment
	- Application has downtime
	- Great for quick iteration in development environment
	- No additional cost![[Screenshot 2023-07-06 at 11.19.31 AM.png]]
- **Rolling**: update a few instances at a time (bucket), and them move onto the next bucket once the first bucket is healthy
	- Application is running below capacity
	- Can set the bucket size
	- Application is running both versions simultaneously
	- No additional cost
	- Long deployment![[Screenshot 2023-07-06 at 11.20.31 AM.png]]
- **Rolling with additional batches**: like rolling, but spins up new instances to move the batch (so that the old application is still available)
	- Application is running at capacity
	- Can set the bucket size
	- Application is running both versions
	- Small additional cost
	- Additional batch is removed at the end of the deployment
	- Longer deployment
	- Good for prod![[Screenshot 2023-07-06 at 11.23.57 AM.png]]
- **Immutable**: spins up new instances in a new ASG, deploys version to these instances, and then swaps all the instances when everything is healthy
	- Zero downtime
	- New code is deployed to new instances on a temporary ASG
	- High cost, double capacity
	- Longest deployment
	- Quick rollback in case of failures (just terminate new ASG)
	- Great for prod![[Screenshot 2023-07-06 at 11.26.14 AM.png]]
- **Blue / Green**: create a new environment and switch over when ready
	- Not a "direct feature" of Elastic Beanstalk
	- Not a "direct feature" of Elastic Beanstalk
	- Zero downtime and release facility
	- Create a new "stage" environment and deploy v2 there
	- The new environment (green) can be validated independently and roll back if issues
	- Route 53 can be setup using weighted policies to redirect a little but of traffic to the stage environment
	- Using Beanstalk, "swap URLs" when done with the environment test![[Screenshot 2023-07-06 at 11.29.52 AM.png]]
- **Traffic splitting**: canary testing - send a small % of traffic to new deployment
	- New application version is deployed to a temporary ASG with the same capacity
	- A small % of traffic is sent to the temporary ASG for a configurable amount of time
	- Deployment health is monitored
	- If there's a deployment failure, this triggers an automated rollback (very quick)
	- No application downtime
	- New instances are migrated from the temporary to the original ASG
	- Old application version is then terminated![[Screenshot 2023-07-06 at 11.40.36 AM.png]]
	![[Screenshot 2023-07-06 at 11.41.59 AM.png]]
	![[Screenshot 2023-07-06 at 11.42.25 AM.png]]
- #### Hands on
	- Open the Beanstalk app environment, you want to deploy
	- Go to configurations from sidebar, there is an option called `Rolling updates and deployments`
	- You can configure the deployment in that section.
	- Deploy

### Elastic Beanstalk CLI
- We can install an additional CLI called the `EB cli` which makes working with Beanstalk from the CLI easier
- Basic commands are:
	- eb create
	- eb status
	- eb health
	- eb events
	- eb logs
	- eb open
	- eb deploy
	- en config
	- eb terminate
- It's helpful for your automated deployment pipelines!
- #### Elastic Beanstalk Deployment Process
	- Describe dependencies
		(requirements.txt for python, package.json for NodeJS)
	- Package code as zip, and describe dependencies
		- python: requirement.txt
		- NodeJS: package.json
	- Console: upload a zip file (create new app version), and then deploy
	- CLI: create new app version using CLI (uploads zip), and then deploy
	- Elastic Beanstalk will deploy the zip on each EC2 instance, resolve dependencies and start the application

### Beanstalk Lifecycle Policy
- Elastic Beanstalk can store at most 1000 application versions
- If you don't remove old versions, you won't be able to deploy anymore
- To phase out old application versions, use a lifecycle policy
	- Based on time (old versions are removed)
	- Based on space (when you have too many versions)
- Versions that are currently used won't be deleted
- Option not to delete the source bundle in S3 to prevent data loss
- #### Hands on
	- Go to Beanstalk
	- Open the application, click on `Settings` button at the top right
	- There is an option to control the `Applicaiton version lifecycle policy`
	- Activate it
	- Set Lifecycle rules
	- Save

### Elastic Beanstalk Extensions
- A zip file containing our code must be deployed to Elastic Beanstalk
- All the parameters set in the UI can be configured with code using files
- Requirements:
	- in the `.ebextensions/`  directory in the root of source code
	- YAM / JSON format
	- `.config` extensions (example: `logging.config`)
	- Able to modify some default settings using: `option_settings`
	- Ability to add resources such as RDS, ElastiCache, DynamoDB, etc...
- Resources managed by `.ebextensions` get deleted if the environment goes away


### Elastic Beanstalk Under the Hood
- Under the hood, Elastic Beanstalk relies on [[CloudFormation]]
- CloudFormation is used to provision other AWS services (we'll see later)
- Use case: you can define CloudFormation resources in your `.ebextensions`

### Elastic Beanstalk Cloning
- Clone an environment with the exact same configurations
- Useful for deploying a "test" version of your application
- All resources and configuration are preserved:
	- Load Balancer type and configuration
	- RDS database type (but the data is not preserved)
	- Environment variables
- After closing an environment, you can change settings
- Cloning can be done from `Action` button

### Beanstalk Migrations
- #### Load Balancer
	- After creating and Elastic Beanstalk environment, you cannot change the Elastic [[Load Balancers]] type
	- To migrate:
		- Create a new environment with the same configuration except LB (can't clone)
		- Deploy your application onto the new environment
		- Perform a CNAME swap or Route 53 update.![[Screenshot 2023-07-06 at 3.02.54 PM.png]]
- #### RDS
	- RDS can be provisioned with Beanstalk, which is great for dev/test
	- This is not great for prod as the database lifecycle is tied to the Beanstalk environment lifecycle
	- The best for prod is to separately create an RDS database and provide our EB application with the connection string.
- #### Decouple RDS
	- Create a snapshot of RDS DB (as a safeguard)
	- Go to the RDS console and protect the RDS database from deletion
	- Create a new Elastic Beanstalk environment, without RDS, point your application to existing RDS
	- Perform a CNAME swap (blue/green) or Route 53 update, confirm working
	- Terminate the old environment (RDS won't be deleted)
	- Delete CloudFormation stack (in DELETE_FAILED state)

### Beanstalk cleanup
- Go to application, delete the application from `action` button

