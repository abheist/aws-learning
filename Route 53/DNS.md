- Domain Name System
- DNS which translates the human friendly hostnames into the machine IP addresses
- www.google.com → 172.217.18.36
- DNS is the backbone of the internet
- DNS uses hierarchical naming structure
	- .com
	- example.com
	- www.example.com
	- api.example.com
- DNS terminologies
	- Domain Registrar
		- Amazon route 53, GoDaddy, Hover, etc
	- DNS Records: A, AAAA, CNAME, NS, etc
	- Zone File: contains DNS records
	- Name Server: resolves DNS queries (Authoritative or Non-Authoritative)
	- Top Level Domain (TLD):
		- .com
		- .us
		- .in
		- .gov
		- .org, etc
	- Second Level Domain (SLD)
		- amazon.com
		- google.com
		- abheist.com, etc
![[Screenshot 2023-06-21 at 6.05.24 PM.png]]

### DNS record types:
- A
	- maps a hostname to IPv4
- AAAA
	- maps a hostname to IPv6
- CNAME
	- maps a hostname to another hostname
	- The target is a domain name which must have an A or AAAA record
	- Can't create a CNAME record for the top node of a DNS namespace (Zone Apex)
	- Example: you can't create for example.com, but you can create for www.example.com
- NS
	- Name Servers for the Hosted Zone
	- Control how traffic is routed for a domain
- CAA
- DS
- MX
- NAPTR
- PTR
- SOA
- TXT
- SPF
- SRV

## TTL (Time to Live)![[Screenshot 2023-06-22 at 11.13.25 AM.png]]
- TTL is time to cache which will be used by the client machine
- High TTL - eg: 24 hr
	- Less traffic on Route 53
	- Possibly outdated records
- Low TTL - eg: 60 sec
	- More traffic on Route 53 (more $)
	- Records are outdated for less time
	- Easy to change records
- Except for Alias records, TTL is mandatory for each DNS record

### Hands-on - TTL
- [[Route 53#^6b2eec|Create a Record]]
- Choose A, and add an IP of one of the instance
- set TTL to 120 seconds
- Create
- do `dig demo.abheist.com` in shell
- now go and change the DNS record and point it to another IP
- you'll see that event the IP has changed, the IP will be same for `dig` command
- Since it's cached for 120 seconds

## Difference between CNAME and Alias
- AWS resource (Load Balancer, CloudFront...) expose and AWS hostname
- `lb1.us-east.elb.amazonaws.com` and you want `myapp.mydomain.com`

CNAME | Alias
--- |---
Points a hostname to any other hostname. (app.mydomain.com → blabla.anything.com) | Points a hostname to any other hostname. (app.mydomain.com → blabla.anything.com)
Only for non root domain (aka.something.mydomain.com) | ==Works for root domain and non root domain (aka mydomain.com)==
--- | Free of charge
--- | Native health check

- Maps a hostname to an AWS resource
- An extension to DNS functionality
- Automatically recognizes changes in the resource's IP addresses
- Unlike CNAME, it can be used for the top node of a DNS namespace (Zone Apex), eg: example.com
- Alias Record is always of type A/AAAA for AWS resources (IPv4 / IPv6)
- You can't set the TTL
![[Screenshot 2023-06-22 at 11.48.00 AM.png]]
- Alias Records Target
	- Elastic Load Balancers
	- CloudFront Distribution
	- API Gateway
	- Elastic Beanstalk environments
	- S3 websites
	- VPC interface endpoints
	- Global Accelerator
	- Route 53 record in the same hosted zone
- You cannot set an ALIAS record for an EC2 DNS name![[Screenshot 2023-06-22 at 11.51.11 AM.png]]
- ![[Screenshot 2023-06-22 at 11.53.58 AM.png]]
- ![[Screenshot 2023-06-22 at 11.55.49 AM.png]]
- 
