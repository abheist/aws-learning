AWS - Amazon Cloud Providers . A cloud provider.

![Pasted image 20230530221826](images%201/Pasted%20image%2020230530221826.png)

- Must know Services
	- [IAM](IAM/IAM.md)
	- [EC2](EC2/EC2.md)
		- [AMIs](EC2/AMIs.md)
		- [Load Balancers](Load%20Balancer%20and%20ASG/Load%20Balancers.md)
		- [Auto Scaling Group](Load%20Balancer%20and%20ASG/Auto%20Scaling%20Group.md)
		- [EBS](EC2/EBS.md)
		- [Elastic IPs](Elastic%20IPs)
		- [System Manager](System%20Manager)
	- [S3](S3.md)
	- [Route 53](Route%2053/Route%2053.md)
	- [VPC](VPC/VPC.md)
	- [CloudFront](CloudFront.md)
	- [CloudWatch](Monitoring%20&%20Logs/CloudWatch.md)
- Managed Services
	- [RDS](Databases/RDS.md)
	- [EMR](EMR)
	- [ElasticSearch](ElasticSearch)
	- [ElastiCache](Databases/ElastiCache.md)
- Optional But important infrastructure
	- [Lambda](Lambda/Lambda.md)
	- [CloudTrail](Monitoring%20&%20Logs/CloudTrail.md)
	- [CloudFormation](CloudFormation.md)
	- [Elastic Beanstalk](Elastic%20Beanstalk.md)
	- [EFS](EC2/EFS.md)
	- [ECS](Containers/ECS.md)
	- [EKS](Containers/EKS.md)
	- [ECR](Containers/ECR.md)
	- [Config](Config)
	- [X-Ray](Monitoring%20&%20Logs/X-Ray.md)
- Special purpose Infrastructure
	- [DynamoDB](Databases/DynamoDB.md)
	- [Aurora](Databases/Aurora.md)
	- [Glacier](Glacier)
	- [Kinesis](Messaging%20&%20Queues/Kinesis.md)
	- [SQS](Messaging%20&%20Queues/SQS.md)
	- [Redshift](Redshift)
	- [QuickSight](QuickSight)
	- [SES](SES)
	- [API Gateway](API%20Gateway.md)
	- [IoT](IoT)
	- [WAF](WAF)
	- [KMS](KMS)
	- [Inspector](Inspector)
	- [Trusted Advisor](Trusted%20Advisor)
	- [Certificate Manager](Certificate%20Manager)
	- [Fargate](Containers/Fargate.md)
	- [AWS Step Functions](AWS%20Step%20Functions)
- Compound Services
	- [Machine Learning](Machine%20Learning)
	- [Lex](Lex)
	- [Rekognition](Rekognition)
	- [Data Pipeline](Data%20Pipeline)
	- [SWF](SWF)
	- [Lumberyard](Lumberyard)
- Mobile/App Development
	- [SNS](Messaging%20&%20Queues/SNS.md)
	- [Cognito](Cognito)
	- [Device Farm](Device%20Farm)
	- [Mobile Analytics](Mobile%20Analytics)
	- [Mobile Hub](Mobile%20Hub)
- Enterprise Services
	- [AppStream](AppStream)
	- [Workspaces](Workspaces)
	- [WorkDocs](WorkDocs)
	- [WorkMail](WorkMail)
	- [Directory Service](Directory%20Service)
	- [Direct Connect](Direct%20Connect)
	- [Storage Gateway](Storage%20Gateway)
	- [Service Catalog](Service%20Catalog)
- Probably don't need to know
	- [Snowball](Snowball)
	- [Snowmobile](Snowmobile)
	- [CodeCommit](CodeCommit)
	- [CodeBuild](CodeBuild)
	- [CodePipeline](CodePipeline)
	- [CodeDeploy](CodeDeploy)
	- [OpsWorker](OpsWorker)

### Courses
- [Starting your Career with AWS Cloud on EDX](https://www.edx.org/xseries/aws-starting-your-career-with-aws-cloud?webview=false&campaign=Starting+your+Career+with+AWS+Cloud&source=edx&product_category=xseries&placement_url=https%3A%2F%2Fwww.edx.org%2Fschool%2Faws)
- [Professional Certificate in Cloud Solutions Architecture](https://www.edx.org/professional-certificate/aws-cloud-solutions-architect-professional-certificate?webview=false&campaign=Cloud+Solutions+Architecture&source=edx&product_category=professional-certificate&placement_url=https://www.edx.org/school/aws)

## Learning

### [AWS Infrastructure](AWS%20Infrastructure)
- [AWS global infrastructure](https://aws.amazon.com/about-aws/global-infrastructure/regions_az/)
- [Regions](Regions.md)
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
- [Availability Zones](Availability%20Zones)
	- Each region has many availability zones
		- usually 3, min is 3, max is 6.
	- Each availability zone (AZ) is one or more discrete [data centers](data%20centers) with redundant power, networking, and connectivity.
	- They are separate from each other, so that they-re isolated from disasters.
	- They are connected with high bandwidth, ultra-low latency networking
- [Data Centers](Data%20Centers)
- [Point of Presence](Edge%20Locations.md)
	- aws has 400+ Point of Presence  (400+ edge locations & 10+ Regional Caches) in 90+ cities across 40+ countries
	- Content is delivered to end users with lower latency