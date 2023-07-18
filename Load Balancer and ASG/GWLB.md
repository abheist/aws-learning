---
aliases: Gateway Load Balancer
---
- Gateway Load Balancer
- Deploy, scale and manage a fleet of 3rd party network virtual appliances in AWS
- Example: Firewalls, Intrusion Detection and Prevention Systems, Deep Packet Inspection Systems, Payload Manipulation, etc.
- Operates at Layer 3 (Network Layer) - IP packets
- Combines the following functions
	- Transparent Network Gateway - single entry/exit for all traffic
	- Load Balancer - distributes traffic to your virtual appliances
- Uses the GENEVE protocol on the port 6081![Screenshot 2023-06-12 at 10.47.32 PM](../images%201/Screenshot%202023-06-12%20at%2010.47.32%20PM.png)
- If you see the above diagram
	- Check the traffic is getting filter from Gateway load balancers to 3rd party security virtual appliances target group
	- Once it's checked, then only it will reach to the application, otherwise the request will be dropped.
- [Target Groups](Target%20Groups.md) can be
	- EC2 instances / 3rd party instances
	- IP Addresses - must be private IPs![Screenshot 2023-06-12 at 10.49.56 PM](../images%201/Screenshot%202023-06-12%20at%2010.49.56%20PM.png)

