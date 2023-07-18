![[images/Screenshot 2023-07-17 at 9.06.22 PM.png]]
- AWS Lambda + API Gateway: No infrastructure to manage
- Support for the WebSocket Protocol
- Handle API versioning (v1, v2...)  
- Handle different environments (dev, test, prod...)
- Handle security (Authentication and Authorization)
- Create API keys, handle request throttling
- Swagger / Open API import to quickly define APIs
- Transform and validate requests and responses
- Generate SDK and API specifications
- Cache API responses

### Integration High Level
- Lambda Function
	- Invoke Lambda function
	- Easy way to expose REST API backed by AWS Lambda
- HTTP
	- Expose HTTP endpoints in the backend
	- Example: internal HTTP API on premise, Application Load Balancer...
	- Why? Add rate limiting, caching, user authentications, API keys, etc...
- AWS Service
	- Expose any AWS API through the API Gateway
	- Example: start an AWS Step Function workflow, post a message to SQS
	- Why? Add authentication, deploy publicly, rate control...
	- AWS Service Integration Kinesis Data Streams example![[images/Screenshot 2023-07-17 at 9.09.21 PM.png]]

### Endpoint Types
- Edge-Optimized (default): For global clients
	- Requests are routed through the CloudFront Edge locations (improves latency)
	- The API Gateway still lives in only one region
- Regional:
	- For clients within the same region
	- Could manually combine with CloudFront (more control over the caching strategies and the distribution)
- Private:
	- Can only be accessed from your VPC using an interface VPC endpoint (ENI)
	- Use a resource policy to define access

### Security
- User Authentication through  IAM Roles (useful for internal applications)
	- Cognito (identity for external users – example mobile users)
	- Custom Authorizer (your own logic)
- Custom Domain Name HTTPS security through integration with AWS Certificate Manager (ACM)
	- If using Edge-Optimized endpoint, then the certificate must be in us-east-1
	- If using Regional endpoint, the certificate must be in the API Gateway region
	- Must setup CNAME or A-alias record in Route 53

### Deployment Stages
- Making changes in the API Gateway does not mean they’re effective • You need to make a “deployment” for them to be in effect
- It’s a common source of confusion
- Changes are deployed to “Stages” (as many as you want)
- Use the naming you like for stages (dev, test, prod)
- Each stage has its own configuration parameters
- Stages can be rolled back as a history of deployments is kept

### Stages v1 and v2 API breaking change![[images/Screenshot 2023-07-17 at 9.13.16 PM.png]]

### Stage Variables
- Stage variables are like environment variables for API Gateway
- Use them to change often changing configuration values
- They can be used in:
	- Lambda function ARN
	- HTTP Endpoint
	- Parameter mapping templates
- Use cases:
	- Configure HTTP endpoints your stages talk to (dev, test, prod...)
	- Pass configuration parameters to AWS Lambda through mapping templates
- Stage variables are passed to the ”context” object in AWS Lambda
- Format: ${stageVariables.variableName}

### API Gateway Stage Variables & Lambda Aliases
- We create a stage variable to indicate the corresponding Lambda alias
- Our API gateway will automatically invoke the right Lambda function!![[images/Screenshot 2023-07-17 at 9.15.10 PM.png]]


### Canary Deployment
- Possibility to enable canary deployments for any stage (usually prod)
- Choose the % of traffic the canary channel receives![[images/Screenshot 2023-07-17 at 9.16.14 PM.png]]
- Metrics & Logs are separate (for better monitoring)
- Possibility to override stage variables for canary
- This is blue / green deployment with AWS Lambda & API Gateway

### IntegrationTypes
- Integration Type MOCK
	- API Gateway returns a response without sending the request to the backend
- IntegrationType HTTP / AWS (Lambda & AWS Services)
	- you must configure both the integration request and integration response
	- Setup data mapping using mapping templates for the request & response![[images/Screenshot 2023-07-17 at 9.18.42 PM.png]]
- Integration Type AWS_PROXY (Lambda Proxy):
	- incoming request from the client is the input to Lambda
	- The function is responsible for the logic of request / response
	- No mapping template, headers, query string parameters... are passed as arguments

![Hellos](Screenshot%202023-07-17%20at%209.23.52%20PM.png)
