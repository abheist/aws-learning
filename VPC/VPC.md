---
aliases:
	- Virtual Private Cloud
---
- VPC is something you should know in depth for the AWS Certified Solutions Architect Associate & AWS Certified SysOps Administrator
- At the AWS Certified Developer Level, you should know about:
	- [[VPC]]
	- [[Subnets]]
	- [[Internet Gateways]]
	- [[NAT Gateways]]
	- [[Security Groups]]
	- [[Network ACL]] (NACL)
	- [[VPC Flow Logs]]
	- [[Site to Site VPN]]
	- [[Direct Connect]]

- VPC: private network to deploy your resources (regional resource)
- Default VPC is automatically created for us in every region.
- [[Subnets]]:
	- Subnets allow you to partition your network inside your VPC (Availability Zone Resource)
	- A [[Subnets#^d34127|Public Subnet]] is a subnet that is accessible from the internet
		- Public subnet can access www
		- www can access Public subnet
	- A [[Subnets#^ecf709|Private Subnet]] is a subnet that is not accessible from the internet
	- To define access to the internet and between subnets, we use [[Route Tables]]![[Screenshot 2023-06-23 at 1.02.19 PM.png]]
- VPC Diagram![[Screenshot 2023-06-23 at 1.03.15 PM.png]]
- [[Internet Gateways]] & [[NAT Gateways]]
	- Internet Gateways helps out VPC instances connect with the internet
	- Public Subnets have a route to the internet gateway
	- NAT Gateways (AWS Managed) & NAT Instances (self-managed) allow your instances in your Private Subnets to access the internet while remaining private![[Screenshot 2023-06-23 at 1.09.04 PM.png]]
- [[Network ACL]] & [[Security Groups]]
	- [[Network ACL]]
		- A firewall which controls traffic from and to subnet
		- Can have ALLOW and DENY rules
		- Are attached at the Subnet level
		- Rules only include IP addresses
	- [[Security Groups]]
		- A firewall that controls traffic to and from an ENI / am EC2 instances
		- Can have only ALLOW rules
		- Rules include IP addresses and other security groups![[Screenshot 2023-06-23 at 1.12.41 PM.png]]

Security Group | Network ACL
---|--- 
Operates at the instance level | Operates at the subnet level
Supports allow rules only | Supports allow rules and deny rules
Is stateful: Return traffic is automatically allowed, regardless of any rules | Is stateless: Return traffic must be explicitly allowed by rules
We evaluate all rules before deciding whether to allow traffic | We process rules in number order when deciding whether to allow traffic
Applies to an instance only if someone specifies the security group when launching the instance, or associates the security group with the instance later on | Automatically applies to all instances in the subnets it's associated with (therefore, you don't have to rely on users to specify the security group)

- [[VPC Flow Logs]]
	- Capture information about IP traffic going into your interfaces:
		- VPC Flow logs
		- Subnet Flow logs
		- Elastic Network Interface Flow logs
	- Helps to monitor & troubleshoot connectivity issues.
		- Example:
		- Subnets to internet
		- Subnets to subnets
		- Internet to Subnets
	- Capture network information from AWS managed interfaces too: Elastic Load Balancer, ElastiCache, RDS, Aurora, etc...
	- VPC Flow logs data can go to S3, CloudWatch Logs, and Kinesis Data Firehose
- VPC Peering
	- Connect two VPC, privately using AWS' network
	- Make them behave as if they were in the same network
	- Must not have overlapping CIDR (IP address ranges)
	- VPC Peering connection is not transitive (must be established for each VPC that need to communicate with one another)![[Screenshot 2023-06-23 at 1.28.56 PM.png]]
- VPC Endpoints
	- Endpoints allows you to connect to AWS Services using a private network instead of the public www network
	- This gives you enhanced security and lower latency to access AWS services
	- VPC Endpoint gateway: S3 & DynamoDB
	- VPC Endpoint Interface: the rest
	- Only used within your VPC![[Screenshot 2023-06-23 at 1.33.11 PM.png]]
- [[Site to Site VPN]] & [[Direct Connect]]
	- Site to Site VPN
		- Connect an on-premises VPN to AWS
		- The connection is automatically encrypted
		- Goes over the public internet
	- Direct Connect (DX)
		- Establish a physical connection between on-premises and AWS
		- The connection is private, secure and fast
		- Goes over a private network
		- Takes at least a month to establish
	- Note: Site-to-site VPN and Direct Connect cannot access VPC endpoints![[Screenshot 2023-06-23 at 1.37.41 PM.png]]

### VPC Cheat Sheet & Closing Comments
- VPC: Virtual Private Cloud
- Subnets: Tied to an AZ, network partition of the VPC
- Internet Gateway: at the VPC level, provide Internet Access
- NAT Gateway / Instances: give internet access to private subnets
- NACL: Stateless, subnet rules for inbound and outbound
- Security groups: Stateful, operate at the EC2 instance level or ENI
- VPC Peering: Connect two VOC with non overlapping IP ranges, non transitive
- VPC Endpoints: Provide private access to AWS Services within VPC
- VLPC Flow Logs: network traffic logs
- Site to Site VPN: VPN over public internet between on-premises DB and AWS
- Direct Connect: direct private connection to a AWS
