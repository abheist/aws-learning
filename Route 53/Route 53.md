- [[DNS]] - read more here before moving forward

- Route 53 is highly available, scalable, fully managed and Authoritative DNS
	- Authoritative = the customer (you) can update the DNS records![[Screenshot 2023-06-21 at 11.23.08 PM.png]]
- Route 53 is also a Domain Registrar
- Ability to check the health of your resources
- The only AWS service which provides 100% availability SLA
- Why Route 53?
	- 53 is a reference to the traditional DNS port.

### Records
- How you want to route traffic for a domain
- Each record contains:
	- Domain/subdomain Name - eg: example.com
	- Record type - eg: A or AAAA
	- Value - eg: 12.34.56.78
	- Routing policy - How Route 53 responds to queries
	- TTL - amount of time the record cached at DNS Resolvers
- Route 53 supports the following [[DNS#DNS record types|DNS record types]]:
	- Must know
		- A
		- AAA
		- CNAME
		- NS
	- Advanced
		- CAA
		- DS
		- MX
		- NAPTR
		- PTR
		- SOA
		- TXT
		- SPF
		- SRV

### Hosted Zones
- A container for records that define how to route traffic to a domain and its subdomain
- Public Hosted Zones - contains records that specify how to route traffic on the internet (public domain names)
	  application1.mypublicdomain.com
- Private Hosted Zones - contain records that specify how you route traffic within one or more VPCs (private domain names)
	  application1.company.internal
- You pay $0.5 per month per hosted zone

#### Public v/s Private hosted Zones![[Screenshot 2023-06-21 at 11.39.41 PM.png]]

### Hands on - register a domain
- Go to Route 53 dashboard
-  From sidebar, go to `Registered Domains` under `Domains`
- Click on `Register Domain` button, a new form will open
	- Choose a domain name - check
	- Select a domain, check the cost, click next
	- Enter contact details
	- Enable privacy protection
	- Review details and click `Complete Order`
- Once the domain is registered, it'll be available on the dashboard
- Click on the purchased domain, you can see other details about it
- Now to go `Hosted Zones` from sidebar
- Select the domain and you'll 2 records already created for the domain
	- NS record
	- SOA record
- To create a record, click on `Create Record` button, a new form will open ^6b2eec
	- Record Name: let it be test
	- Record type, select A
	- Value: 11.22.33.44
	- TTL: 300 seconds
	- Routing policy, select `Simple Routing`
	- Create
- 

### Hands on - Creating our first records
- [[EC2#Hands-on - Launch an EC2 instance running Linux|Launch 3 EC2 instance]] in 3 separate regions
	- eg: Frankfurt
	- Virginia
	- Singapore
- [[ALB|Launch and Application Load Balancer]] in Frankfurt
	- Create new target group for the above instances we created before
- Now go to ALB, copy DNS name and check

### Routing policies
- Define how Route 53 responds to DNS queries
- Don't get confused by the work "Routing"
	- It's not the same as Load Balancer routing which routes the traffic
	- DNS does not route any traffic, it only responds to the DNS queries
- Route 53 Supports the following Routing Policies
	- Simple
		- Typically, route traffic to a single resource
		- Can specify multiple value in the same record
		- If multiple values are returned, a random one is chosen by the client
		- When Alias is enabled, specify only one AWS resource
		- Can't be associated with Health Checks![[Screenshot 2023-06-22 at 12.13.18 PM.png]]
	- Weighted
		- Control the % of the requests that go to each specific resource
		- Assign each record a relative weight
			- $traffic \ (\%) = \frac{weight\ for\ a\ specific\ record}{sum\ of\ all\ the\ weights\ for\ all\ records}$
			- Weights don't need to sum up to 100
		- DNS records must have the same name and type
		- Can be associated with Health Checks
		- Use cases: Load balancing between regions, testing new application versions
		- Assign a weight of 0 to a record to stop sending traffic to a resource
		- If all records have weight of 0, then all records will be returned equally
	- Failover (Active-Passive)
		- The DR server will only get used, if the primary server is down.![[Screenshot 2023-06-22 at 5.16.48 PM.png]]
	- Latency based
		- Redirect to the resource that has the least latency close to us
		- Super helpful when latency for users is a priority
		- Latency is based on traffic between users and AWS
		- Germany users may be directed to the US (if that's the lowest latency)
		- Can be associated with Health Checks (has a failover capability) 
	- Geolocation
		- Different from Latency based!
		- This routing is based on user location
		- Specify location by Continent, Country or by US State (if there's overlapping, most precise location selected)
		- Should create a "Default" record (in case there's no match on location)
		- Use cases: website localization, restrict content distribution, load balancing, etc
		- Can be associated with Health Checks
	- Multi-Value
		- Use when routing traffic to multiple resources
		- Route 53 return multiple values/resources
		- Can be associated with Health checks (return only values for healthy resources)
		- Up to 8 healthy records are returned for each Multi-value query
		- Multi-Value is not a substitute for having an ELB![[Screenshot 2023-06-22 at 6.09.58 PM.png]]
	- Ip-based routing
		- Routing is based on clients' IP addresses
		- You provide a list of CIDRs for your clients and the corresponding endpoints/locations (user-IP-to-endpoint mappings)
		- Use cases: Optimize performance, reduce network costs...
		- Example: route end users from a particular ISP to a specific endpoint![[Screenshot 2023-06-22 at 6.06.37 PM.png]]
	- Geo-proximity (using Route 53 traffic flow feature)
		- Route traffic to your resources based on the geographic location of users and resources
		- Ability to shift more traffic to resources based on the defined bias
		- To change the size of the geographic region, specify bias values:
			- To expand (1 to 99) - more traffic to the resource
			- To shrink (-1 to -99) - less traffic to the resource
		- Resource can be:
			- AWS resources (specify AWS region)
			- Non-AWS resources (specify Latitude and Longitude)
		- You must use Route 53 Traffic Flow (advanced) to use this feature![[Screenshot 2023-06-22 at 5.46.51 PM.png]]
		- Hands on (geo-proximity and traffic flow)
			- Simplify the process of creating and maintaining records in large and complex configurations
			- Visual editor to manage complex routing decision trees
			- Configurations can be saved as Traffic Flow Policy
				- Can be applied to different Route 53 Hosted Zones (different domain names)
				- Supports versioning
			- In Route 53 dashboard, under sidebar, go to `Traffic Policies`
			- Click on `Create Traffic Policy`, a form will open
			- Name policy
				- Policy name
				- version
				- Version description
				- Next
			- Diagram tool will open
				- Start point
					- Choose DNS type, choose A record
					- + Geo-proximity rule
					- under region 1
					- Chose Endpoint location, select US East 1
					- +  Endpoint, enter the us-east1 IP value
					- leave the bias as 0
					- under region 2
					- Select endpoint as Asia pacific
					- + endpoint of southeast instance
					- if you crease the bias in any side, you can see in the map the area getting covered by each instance
			-  Create

### [[Health Checks]]
