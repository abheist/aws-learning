- go to `.aws` directory
- there will be two files
	- config
		- It'll contain the CLI default configs
	- credentials
		- It'll contain the credentials of the IAM roles / profiles for CLI
		- access key
		- secret access key
- `aws configure` - this command is used to configure default profile
- `aws configure --profile my-other-aws-profile` - to configure a new AWS profile
- `aws s3 ls` will hit based on default profile
- to hit based on different profile use `--profile different-profile-name`
- `aws s3 ls --profile profile-2-name`

### MFA with CLI
- To use MFA with the CLI, you must create a temporary session
- To do so, you must run the STS GetSessionToken API call
- `aws sts get-session-token --serial-number arn-of-the-mfa-device --token-code code-from-token --duration-seconds 3600`
```json
{
	"Credentials": {
		"SecretAccessKey": "secret-access-key",
		"SessionToken": "temp-session-token",
		"Expiration": "expiration-date-time",
		"AccessKeyId": "access-key-id"
	}
}
```
- Hands on
	- Go to User under IAM
	- Select the user you want to assign MFA
	- Under Security Credentials
	- Assign MFA
	- scan and enter the codes and save
	- Copy the MFA ARN code, go to CLI
	```sh
	aws sts get-session-token --serial-number arn:aws:iam::234234242:mfa/abheist --token-code 123123
	``` 
	- You'll see the credentials here
	- To configure these token under credential profile, open credential file, under needed profile add `aws_session_token` key and assign the session token.


### Credentials Provider Chain
- The CLI will look for credentials in this order
	1. Command Line Options: --region, --output and --profile
	2. Environment Variables: AWS_ACCESS_KEY_ID, AWS_SECRET_ACCESS_KEY, and AWS_SESSION_TOKEN
	4. CLI credentials file: aws configure
	5. CLI configuration file: aws configure
	6. Container Credentials: for ECS tasks
	7. Instance profile credentials: for EC2 instance profiles
- AWS SDK default credentials provider chain, the Java SDK example
	1. Java system properties
	2. Environment variables
	3. The default credentials profile file
	4. Amazon ECS container credentials
	5. Instance profile credentials

### AWS Credential Best Practices
- Overall, Never ever store AWS credentials in your code
- Best practice is for credentials to be inherited from the credentials chain
- Is using working with AWS, use IAM Roles
	- EC2 instances role for EC2 instances
	- EC2 roles for ECS tasks
	- Lambda Roles for Lambda functions
- If working outside AWS, use environment variables / named profiles