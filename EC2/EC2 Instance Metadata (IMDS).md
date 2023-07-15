---
alias:
	- EC2 Metadata
	- IMDS
---

- AWS EC2 Instance Metadata (IMDS) is powerful but one of the least known features to developers
- It allows AWS EC2 instances to "learn about themselves" without using an IAM Role for that purpose
- The URL is http://169.254.169.254/latest/meta-data
- You can retrieve the IAM Role name from the metadata, but you cannot retrieve the IAM policy
- Metadata = Info about the EC2 instance
- UserData = launch script of the EC2 instance

### IMDSv1 IMDSv2
- IMDSv1 is accessing http://169.254.169.254/latest/meta-data directly
- IMDSv2 is more secure and is done in two steps
	- Get Session Token (limited validity) - using headers & PUT
	```sh
TOKEN=`curl -X PUT "http://169.254.169.254/latest/api/token" -H "X-aws-ec2-metadata-token-ttl-seconds: 21600"`
	```
	- Use Session Token in IMDSv2 calls - using headers
	```sh
curl http://169.254.169.254/latest/meta-data/profile -H "X-aws-ec2-metadata-token: $TOKEN"
	```

### Hands on
- Create and EC2 instance
- Connect with Ec2 instance
- hit `curl http://169.254.169.254/latest/meta-data`
- It'll give error, to address this
- Retrieve a token
```sh
TOKEN=`curl -X PUT "http://169.254.169.254/latest/api/token" -H "X-aws-ec2-metadata-token-ttl-seconds: 21600"`  
```
- try `echo TOKEN`
- Now run
```sh
curl http://169.254.169.254/latest/meta-data/profile -H "X-aws-ec2-metadata-token: $TOKEN"
```
- It should work
- You'll get all the keys you need the info from and hit you hit with that key in the end of url, you'll get the result.
