---
aliases: Network Load Balancer
---

- Network Load balancer
- Network load balancer (Layer 4) allow to:
	- Forward TCP & UDP traffic to your instances
	- Handle millions of request per second
	- Less latency ~100 ms (vs 400 ms for ALB)
- NLB has one static IP per AZ and supports assigning Elastic IP
  (helpful for whitelisting specific IP)
- NLB are used for extreme performance, TCP or UDP traffic
- Not included in the AWS free tier![Screenshot 2023-06-12 at 10.08.12 PM](../images%201/Screenshot%202023-06-12%20at%2010.08.12%20PM.png)
- [Target Groups](Target%20Groups.md) can be
	- EC2 instances
	- IP addresses - must be private IPs
		- Here you can one instance of EC2 and other instance of your own personal server
	- Application Load Balancer![Screenshot 2023-06-12 at 10.10.42 PM](../images%201/Screenshot%202023-06-12%20at%2010.10.42%20PM.png)
	- Health check support the TCP, HTTP, HTTPS protocol

### Hands on
- Go to Load balancer page
- Create a new Load Balancer, a new form will appear
- Select Network Load Balancer
- Basic Configurations
	- Load Balancer Name
	- Scheme
		- Internet facing (select)
		- Internal
	- IP Address Type
		- IPv4 (select)
		- Dualstack
- Network mapping
	- VPC, let it be default
	- Mappings
		- Select all the needed AZs
- Listeners and routing
	- [](Target%20Groups.md#Create%20a%20target%20group%7CCreate%20a%20new%20target%20group) for this
		- While creating, select TCP protocol on port 80
- Review the settings and create load balancer,
- Once created, open the DNS name
- It'll not work, let's investigate
- If you go to EC2 instances's security groups
- You'll see the traffic is only allowed from the ALB we created
- So edit that and add a new rule to allow HTTP on port 80 form anywhere
- Save this rule and now try the NLBs DNS, it should work.
