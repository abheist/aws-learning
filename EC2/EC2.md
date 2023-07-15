> [!info] 
> Before starting, it's better to setup a billing budget alerts, check them out [[Billing#To get the alerts about the upcoming cost|here]]


- EC2 is one of the most popular of AWS offering
- EC2 = Elastic Compute Cloud = Infrastructure as a Service
- It mainly can be used for the following:
	- Renting virtual machines (EC2)
	- Storing data on virtual drives (EBS)
	- Distributing load across machine (ELB)
	- Scaling the services using an auto-scaling group (ASG)
- Knowing EC2 is fundamental to understand how the cloud works.

- EC2 sizing & configuration options
	- Operating System (OS): Linux, Windows or Mac OS
	- How much compute power & cores (CPU)
	- How much RAM needed
	- How much storage needed
		- Network attached (EBS & EFS)
		- Hardware (EC2 instance store)
	- Network card: speed of the card, Public IP address
	- Firewall rules: security group
	- Bootstrap script (configure at first launch): EC2 User Data
	- 

### EC2 User Data
- It is possible to bootstrap our instances using an EC2 User Data script
- Bootstrapping means launching commands when a machine starts
- That script is only run once at the instance first start
- EC2 user data is used to automate boot tasks such as
	- Installing updates
	- Installing software
	- Downloading common files from the internet
	- Anything you can think of
- The EC2 user data script runs with the root user
- Example ![[Screenshot 2023-06-05 at 7.43.12 PM.png]]
- For learning, user t2.micro which is part of free-tier (up to 750 hours per month)

### Hands-on - Launch an EC2 instance running Linux
- Search for `EC2` service, go to dashboard
- Go to `instances` from sidebar
- Click on `Launch Instance` button, form will open
	- Add a name and tag
	- Application and OS Image, you can use as per your need, for now go with below
		- Quick State → Amazon Linux
		- From AMIs (Amazon Machine Image)
		- Choose `Amazon Linux 2 AMI` which is free-tier
		- Choose 64 bit (x86) architecture
	- Choose instance type, use `t2.micro` as its free tier
	- Key pair (to login via SSH), Click on `Create new key pair`, form will open
		- Enter pair name
		- Key pair type, select `RSA` ^0c4488
			- RSA
			- ED25519
		- Private key file format, select `.pem`
			- .`pem`
			- `.ppk`
		- Once filled, click on create, the file will be downloaded for future use.
	- Network settings, select the checkboxes in this setting as per need.
		- Check the `Allow SSH traffic from anywhere`
		- Check the `Allow HTTPs traffic from the internet`
		- Check the `Allow HTTP traffic from internet`
	- Configure storage, keep it as it is for now.
	- Advanced settings
		- Skip other details for now, directly go to `user data section` under advanced settings
		- Under user data, put following information ^0c5fa4
		 ```sh
		#!/bin/bash
		# Install httpd (linux 2 version)
		yum update -y
		yum install -y httpd
		systemctl start httpd
		systemctl enable httpd
		echo "<h1>Hello world from $(hostname -f)</h1>" > /var/www/html/index.html
		```
	- Summary section, it'll ask for number of instance we need to start for the above selected settings, let it be 1 for now.
	- Review the settings selected
	- Click on `Launch Instance` button.
	- Once instance is created, you can see it in the instances list.


### Some useful Information about the instance can checkout on instance click
- Details
	- Instance ID
	- Public IP4 address
	- Private IP4 address
	- Hostname
	- Private DNS
	- Instance type
	- AMI ID
	- Key pair 
- Security
	- Security Group
	- Inbound rules
	- Outbound rules
- Storage
	- The volume we created can be visible here.
- If you copy and paste the public IP on internet, you can see the server running as we have put some details to start the server in [[EC2#^0c5fa4|user data section]]

> [!info] Public IP changes every time we stop and start an instance

### EC2 Instance types
- Can be found here [Instance types](https://aws.amazon.com/ec2/instance-types/)
- You can choose from instance types as per your need
- AWS had following name convention
	- m: instance class
	- 5: generation (AWS improves them over time)
	- 2xlarge: size within the instance class
- General purpose instance
	- Great for diversity of workloads such as web servers or code repositories
	- Balance between
		- Compute
		- Memory
		- Networking![[Screenshot 2023-06-05 at 8.26.59 PM.png]]
- Compute Optimized
	- Great for compute intensive tasks that require high performance processors:
		- Batch processing workloads
		- Media transcoding
		- High performance web servers
		- High performance computing (HPC)
		- Scientific modeling & machine learning
		- Dedicated gaming servers![[Screenshot 2023-06-05 at 8.26.40 PM.png]]
- Memory Optimized
	- Fast performance for workload that process large data sets in memory
	- Use cases:
		- High performance, relational/non-relational databases
		- Distributed web scale cache stores
		- In-memory databases optimized for BI (business intelligence)
		- Applications performing real-time processing of big unstructured data ![[Screenshot 2023-06-05 at 8.40.08 PM.png]]
- Storage Optimized
	- Great for storage-intensive tasks that require high, sequential read and write access to large data sets on local storage
	- Use cases
		- High frequency Online Transaction Processing (OLTP) systems
		- Relational & NoSQL databases
		- Cache for in-memory databases (for example: REDIS)
		- Data warehousing applications
		- Distributed file systems![[Screenshot 2023-06-05 at 8.45.52 PM.png]]
- Example comparison, to compare in more details, check this out: [Instances Comparison](https://instances.vantage.sh/)  ![[Screenshot 2023-06-05 at 8.46.15 PM.png]]

- [[Security Groups]]
- [[SSH]] / [[Connect to EC2 instance by AWS Connect]]
- Associate IAM role with EC2 instance
	- EC2 instance comes with AWS CLI installed
	- you can check it by running in terminal `aws --version`
	- Never ever `aws configure` with your personal IAM role, better to create separate IAM Role for EC2 instance.
	- To associate IAM role to EC2 instance, do the following:
		- Go to EC2 dashboard
		- Select EC2 instance
		- Check under instance's security tab, is any IAM roles associated with it.
		- If not, then
		- Keep the instance selected, at the top header there is a dropdown `Actions` → `Security` → `Modify IAM Role`
		- Once you click on Modify IAM role, new screen will open for adding/modifying IAM role
		- Select IAM Role to attach or [[Roles#^c51cea|Create a new role]].
		- Now click on save.
	- Once the role is associated, you can run the AWS CLI commands in EC2 instance which are allowed by the roles you recently associated. Suppose you attached the role which allows the read-only access to IAM, then you can run `aws iam list-users` to check.
- [[EBS]]
- [[AMIs]]
- EC2 Instance Store
	- EBS volumes are network drives with good but "limited" performance
	- If you need a high performance hardware disk, use EC2 instance store
	- Better I/O performance
	- EC2 instance store lose their storage if they're stopped (ephemeral)
	- Good for buffer / cache / scratch data / temporary content
	- Risk of data loss if hardware fails
	- Backups and replication are your responsibility![[Screenshot 2023-06-06 at 9.16.23 PM.png]]
	- 
	- 


### EC2 Instances Purchases Options
- On-Demand Instances - short workload, predictable pricing, pay by second
	- Pay for what you use:
		- Linux or Windows - billing per second, after the first minute
		- All other operating systems - billing per hour
	- Has the highest cost but no upfront payment
	- No long term commitment
	- Recommended for short term and un-interrupted workloads, where you can't predict how the application will behave.
- Reserved (1 & 3 years)
	- Reserved Instances - Long workload
	- Convertible Reserved Instances - Long workloads with flexible instances
	- Up to 72% (this number can be changed over time by amazon) discount compared to on-demand
	- You reserve a specific instance attribute (instance type, region, tenancy, OS)
	- Reservation period - 1 year (+discount) or 3 years (+++discount)
	- Payment options - No upfront (+discount), partial upfront (++discount), all upfront (+++discount)
	- Reserved Instance's Scope - Regional or Zonal (reserve capacity in an AZ)
	- Recommended for steady-state usage applications (think database)
	- You can buy and sell in the Reserved Instance Marketplace
	- Convertible Reserved Instance
		- Can change the EC2 instance type, instance family, OS, scope and tenancy
		- up to 66% discount
- Savings Plan (1 & 3 years) - commitment to an amount of usage, long workload
	- Get a discount based on long term usage (up to 72% - same as RIs)
	- Commit to a certain type of usage ($10/hour for 1 or 3 years)
	- Usage beyond EC2 savings plans is billed at the on-demand price
	- Locked to a specific instance family & AWS region (eg: M5 in us-east-1)
	- Flexible across
		- Instance size (eg: M5.xLarge, m5.2xLarge)
		- OS (eg: Linux, Windows)
		- Tenancy (Host, Dedicated, Default)
- Spot Instances - short workload, cheap, can lose instances (less reliable)
	- Can get a discount of up to 90% compared to on-demand
	- Instances that you can `lose` at any point of time if you max-price is less than the current spot price
	- The MOST cost efficient instances in AWS
	- Useful for workload that are resilient to failure
		- Batch Jobs
		- Data analysis
		- Image processing
		- Any distributed workloads
		- Workloads with a flexible start and end time
	- Not suitable for critical jobs or databases.
- Dedicated Hosts - book an entire physical server, control instance placement
	- A physical server with EC2 instance capacity fully dedicated to your use
	- Allows you address compliance requirements and use your existing server-bound software licenses (per-socket, per-core, pe -- VM software licences)
	- Purchasing options
		- On Demand - pay per second for active Dedicated host
		- Reserved - 1 or 3 years (No upfront, partial upfront, all upfront)
	- The most expensive option
	- Useful for software that have complicated licensing model (BYOL - Bring your own license)
	- Or for the companies that have strong regulatory or compliance needs
- Dedicated Instances - no other customers will share your hardware
	- Instances run on hardware that's dedicated to you
	- May share hardware with other instances in the same account
	- No control over instance placement (can move hardware after start/stop)![[Screenshot 2023-06-06 at 12.11.55 AM.png]]
- Capacity Reservations - reserve capacity in a specific AZ for any duration.
	- Reserve on-demand instances capacity in a specific AZ for any duration
	- You always have access to EC2 capacity when you need it
	- No time commitment (create/cancel anytime), no billing discounts
	- Combine with Regional Reserved Instances and Savings Plans to benefit from billing discounts
	- You're charged at on-demand rate whether you run instances or not
	- Suitable for short-term, uninterrupted workloads that needs to be in a specific AZ.
- **Which purchasing option is right for me ? take a resort analogy**
	- On demand: coming and staying in resort whenever we like, we pay the full price.
	- Reserved: like planning ahead and if we plan to stay for a long time, we may get a good discount
	- Savings Plans: pay a certain amount per hour for certain period and stay in any room type (eg: King, Suite, Sea View, etc)
	- Spot instances: the resort allows people to bid for the empty rooms and the highest bidder keeps the rooms. You can get kicked out at any time.
	- Dedicated Hosts: We book and entire building of the resort.
	- Capacity reservations: you book a room for a period with a full price even you don't stay in it.
- Example price comparison![[Screenshot 2023-06-06 at 12.21.55 AM.png]]


