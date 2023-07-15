AWS - Amazon Cloud Providers . A cloud provider.

![[Pasted image 20230530221826.png]]

- Must know Services
	- [[IAM]]
	- [[EC2]]
		- [[AMIs]]
		- [[Load Balancers]]
		- [[Auto Scaling Group]]
		- [[EBS]]
		- [[Elastic IPs]]
		- [[System Manager]]
	- [[S3]]
	- [[Route 53]]
	- [[VPC]]
	- [[CloudFront]]
	- [[CloudWatch]]
- Managed Services
	- [[RDS]]
	- [[EMR]]
	- [[ElasticSearch]]
	- [[ElastiCache]]
- Optional But important infrastructure
	- [[Lambda]]
	- [[CloudTrail]]
	- [[CloudFormation]]
	- [[Elastic Beanstalk]]
	- [[EFS]]
	- [[ECS]]
	- [[EKS]]
	- [[ECR]]
	- [[Config]]
	- [[X-Ray]]
- Special purpose Infrastructure
	- [[DynamoDB]]
	- [[Aurora]]
	- [[Glacier]]
	- [[Kinesis]]
	- [[SQS]]
	- [[Redshift]]
	- [[QuickSight]]
	- [[SES]]
	- [[API Gateway]]
	- [[IoT]]
	- [[WAF]]
	- [[KMS]]
	- [[Inspector]]
	- [[Trusted Advisor]]
	- [[Certificate Manager]]
	- [[Fargate]]
	- [[AWS Step Functions]]
- Compound Services
	- [[Machine Learning]]
	- [[Lex]]
	- [[Rekognition]]
	- [[Data Pipeline]]
	- [[SWF]]
	- [[Lumberyard]]
- Mobile/App Development
	- [[SNS]]
	- [[Cognito]]
	- [[Device Farm]]
	- [[Mobile Analytics]]
	- [[Mobile Hub]]
- Enterprise Services
	- [[AppStream]]
	- [[Workspaces]]
	- [[WorkDocs]]
	- [[WorkMail]]
	- [[Directory Service]]
	- [[Direct Connect]]
	- [[Storage Gateway]]
	- [[Service Catalog]]
- Probably don't need to know
	- [[Snowball]]
	- [[Snowmobile]]
	- [[CodeCommit]]
	- [[CodeBuild]]
	- [[CodePipeline]]
	- [[CodeDeploy]]
	- [[OpsWorker]]

### Courses
- [Starting your Career with AWS Cloud on EDX](https://www.edx.org/xseries/aws-starting-your-career-with-aws-cloud?webview=false&campaign=Starting+your+Career+with+AWS+Cloud&source=edx&product_category=xseries&placement_url=https%3A%2F%2Fwww.edx.org%2Fschool%2Faws)
- [Professional Certificate in Cloud Solutions Architecture](https://www.edx.org/professional-certificate/aws-cloud-solutions-architect-professional-certificate?webview=false&campaign=Cloud+Solutions+Architecture&source=edx&product_category=professional-certificate&placement_url=https://www.edx.org/school/aws)

## Learning

### [[AWS Infrastructure]]
- [AWS global infrastructure](https://aws.amazon.com/about-aws/global-infrastructure/regions_az/)
- [[Regions]]
	- Regions are all around the world
	- Cluster of data-centers
	- Most AWS services are region scoped
	- How to choose AWS regions
		- Compliance with data governance and legal requirements. 
			Data never leaves a region without your explicit permission.
		- Proximity to customer.
			Reduced latency
		- Available services within a region.
			new services and new features aren't available in every region
		- Pricing
			Pricing varies region to region and is transparent in the service pricing page.
- [[Availability Zones]]
	- Each region has many availability zones
		- usually 3, min is 3, max is 6.
	- Each availability zone (AZ) is one or more discrete [[data centers]] with redundant power, networking, and connectivity.
	- They are separate from each other, so that they-re isolated from disasters.
	- They are connected with high bandwidth, ultra-low latency networking
- [[Data Centers]]
- [[Edge Locations|Point of Presence]]
	- aws has 400+ Point of Presence  (400+ edge locations & 10+ Regional Caches) in 90+ cities across 40+ countries
	- Content is delivered to end users with lower latency