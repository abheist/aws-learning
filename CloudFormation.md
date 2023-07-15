### Infrastructure as a Code
- Currently, we have been doing a lot of manual work
- All this manual work will be very tough to reproduce
	- In another region
	- in another AWS account
	- Within the same region if everything was deleted
- Wouldn't it be great, if all our infrastructure was... code ?
- That code would be deployed and create / update / delete our infrastructure

### What is CloudFormation
- CloudFormation is a declarative way of outlining your AWS Infrastructure, for any resources (most of them are supported)
- For example, within a CloudFormation, you say:
	- I want a security group
	- I want two EC2 machines using this security group
	- I want two Elastic IPs for these EC2 machines
	- I want an S3 bucket
	- I want a load balancer (ELB) in front of these machines
- Then CloudFormation creates those for you, in the right order, with the exact configuration that you specify.

### Benefits of AWS CloudFormation
- Infrastructure as code
	- No resource are manually created, which is excellent for control
	- The code can be version controlled for example using git
	- Changes to the infrastructure are reviewed through code
- Cost
	- Each resources within the stack is staged with an identifier so you can easily see how much a stack costs you
	- You can estimate the costs of your resources using the CloudFormation template
	- Saving strategy: In Dev, you could automation deletion of templates at 5PM and recreated at 8 AM, safely.
- Productivity
	- Ability to destroy and re-create an infrastructure on the cloud on the fly
	- Automated generation of Diagram for your templates!
	- Declarative programming (no need to figure out ordering and orchestration)
- Separation of concern: create many stacks for the many apps, and many layers. Ec:
	- VPX stacks
	- Network stacks
	- App stacks
- Don't re-invent the wheel
	- Leverage existing templates on the web
	- Leverage the documentation

### How CloudFormation Works
- Templates have to be updated in S3 and then referenced in CloudFormation
- To update a template, we can't edit previous ones. We have to re-upload a new version of the template to AWS
- Stacks are identified by a name
- Deleting a stack deletes every single artifact that was created by CloudFormation

### Deploying CloudFormation templates
- Manual way:
	- Editing templates in the CloudFormation Designer
	- Using the console to input parameters, etc
- Automated way:
	- Editing templates in a YAML file
	- Using the AWS CLI (Command Line Interface) to deploy the templates
	- Recommended way when you fully want to automate your flow

### CloudFormation Building Blocks
- Resources: your AWS resources declared in the template (Mandatory)
- Parameters: the dynamic inout for your template
- Mapping: the static variables for your template
- Outputs: References to what has been created
- Conditionals: List of conditions to perform resource creation
- Metadata

- Templates helpers
	- References
	- Functions

### Hands on
- #### Introductory Example
- We are going to create a simple [[EC2]] instance
- Then we're going to create to add an Elastic IP to it
- And we're going to add two [[Security Groups]] to it
- For now, forget about the code syntax
- We'll look at the structure of the files later on
- Change the region to `us-east-1` for practice
- `Create stack with standard`, a form will open
- Specify template
	- Prepare template (options below)
		- Template is ready (select)
			- Template source
				- Amazon S3 URL
				- Upload a template file (select and then upload the sample file)
		- Use a sample template
			- Select a sample template
			- Next
		- Create template in Designer
			- Create template in designer section
			- Next
	- Next
- Specify stack details
	- Enter stack name
	- Next
- Configure stack options
	- Skip this and Next
- Review and Create
- Stack will start creating, you can see the update in Events tab

### Resources
- Resources are the core of your CloudFormation template (mandatory) 
- They represent the different AWS components that will be created and configured
- Resources are declared and can reference each other
- AWS figures out creation, updates and deletes of resources for us
- There are over 224 types of resources (!)
- Resource types identifiers are of the form:
	`AWS::aws-product-name::data-type-name`
- All the resources can be found here:
	[AWS resources](https://docs.aws.amazon.com/AWSCloudFormation/latest/UserGuide/aws-template-resource-type-ref.html)
- [Example](https://docs.aws.amazon.com/AWSCloudFormation/latest/UserGuide/example_cross_ApiGatewayDataTracker_section.html)

### Parameters
- Parameters are a way to provide inputs to your AWS CloudFormation template
- They're important to know about if:
	- You want to reuse your template across the company
	- Some inputs can not be determined ahead of time
- Parameters are extremely powerful, controlled, and can prevent errors from happening in your templates, thanks to types.
- Parameters settings, parameter can be controlled by all these settings
	- Type
		- String
		- Number
		- CommaDelimitedList
		- List
		- AWS Parameter (to help catch invalid values - match against existing values in the AWS Account)
	- Description
	- Constraints
	- ConstraintDescription (String)
	- Min/MaxLength
	- Min/MaxValue
	- Defaults
	- AllowedValues (array)
	- AllowedPattern (regexp)
	- NoEcho (Boolean)
	- #### How to Reference a Parameter
		- The Fn::Ref function can be leveraged to reference parameters
		- Parameters can be used anywhere in a template
		- The shorthand for this in YAML is `!Ref`
		- The function can also reference other elements within the template![[Screenshot 2023-07-07 at 9.55.28 AM.png]]

#### Pseudo Parameters
- AWS offers us pseudo parameters in any CloudFormation template.
- These can be used at any time and are enabled by default![[Screenshot 2023-07-07 at 10.00.00 AM.png]]

### What are mappings ?
- Mappings are fixed variables within your CloudFormation Template.
- They're very handy to differentiate between different environments (dev vs prod), region (AWS region).

### When would you use mapping vs parameters ?
- Mapping are great when you know in advance all the values that can be taken and that they can be deduced from variables such as
	- Region
	- Availability Zone
	- AWS Account
	- Environment (dev vs prod)
	- Etc...
- They allow safer control over the template
- Use parameters when the values are really user specific
- Fn::FindInMap ^3a176f
	- Accessing Mapping Values
	- We use Fn::FindInMap to return a named value from a specific key
	- !FindInMap [MapName, TopLevelKey, SecondLevelKey]![[Screenshot 2023-07-07 at 10.08.31 PM.png]]

### What are Outputs
-  The outputs section declares *optional* outputs values that we can import into other stacks (if you export them first)
- You can also view the outputs in the AWS Console or in using the AWS CLI
- They are very useful for example if you define a network CloudFormation, and output the variables such as VPC ID and your Subnet IDs
- It's the best way to perform some collaboration cross stack, as you let expert handle their own part of the stack
- You can't delete a CloudFormation stack if its output are being referenced by another CloudFormation stack
- #### Output example
- Creating a SSH Security Group as part of one template
- We create an output that references that security group
```yaml
Outputs:
	StackSSHSecurotyGroup:
		Description: The SSH Security Group for our Company
		Value: !Ref MyCompanyWideSSHSecurityGroup
		Export:
			Name: SSHSecurityGroup
```
- #### Cross Stack Reference
- We can create a second template that leverages that security group
- For this, we use the `Fn::ImportValue` function
- You can't delete the underlying stack until all the references are deleted too.![[Screenshot 2023-07-07 at 1.03.51 PM.png]]

### Conditions
- Conditions are used to control the creation of resources or outputs based on a condition
- Conditions can be whatever you want them to be, but common ones are:
	- Environment (dev, test, prod)
	- AWS Region
	- Any parameter value
- Each condition can reference another condition, parameter value or mapping
- How to define condition:
```
Conditions:
	CreateProdResources: !Equal [ !Ref EnvType, prod ]
```
- The logical ID is for you to choose. It's how you name condition
- The intrinsic function (logical) can be any of the following
	- `Fn::And`
	- `Fn::Equals`
	- `Fn::If`
	- `Fn::Not`
	- `Fn::Or`
- Conditions can be applied to resources, outputs, etc... and are defined on the same level, eg:
```
Resources:
	MountPoint:
		Type: "AWS::EC2::VolumeAttachment"
		Condition: CreateProdResources
```
- Here resource will only be get created if condition is true

### Intrinsic functions
- `Fn::Ref`
	- The `Fn::Ref` function can be leveraged to reference
		- Parameters => returns the value of the parameter
		- Resources => returns the physical ID of the underlying resource (ex: EC2 ID)
	- The shorthand for this in YAML is !Ref
	```YAML
	DbSubnet1:
		Type: AWS::EC2::Subnet
		Properties:
			VpcId: !Ref MyVPC 
	```
- `Fn::GetAtt`
	- Attribute are attached to any resources you create
	- To know the attributes of your resources, the best place to look at is the documentation
	- For example: the AZ of an EC2 machine!
	```yaml
	Resources:
		EC2Instance:
			Type: "AWS::EC2::Instance"
			Properties:
				ImageId: ami-1234567
				InstanceType: t2.micro
	```
	
	```YAML
	NewVolume:
		Type: "AWS::EC2::Volume"
		Condition: CreateProdResources
		Properties:
			Size: 100
			AvailabilityZone:
				!GetAtt: EC2Instance.AvailabilityZone
	```
- [[#^3a176f|Fn::FindInMap]]
- `Fn::ImportValue`
	- Import values that are exported in other templates
	- For this, we use the `Fn::ImportValue` function
	```yaml
	Resources:
		MySecureInstance:
			Type: AWS::EC2::Instance
			Properties:
				AvailabilityZone: us-east-1a
				ImageId: ami-a4c7edb2
				InstanceType: t2.micro
				SecurityGroup:
					- !ImportValue: SSHSecurityGroup
	```
- `Fn::Join`
	- Join values with a delimiter
	```yaml
	!Join [delimiter, [ comma-delimited list of values ]]
	```
	- This creates `a:b:c`
	```yaml
	!Join [":", [a, b, c]]
	```
- `Fn::Sub`
	- `Fn::Sub` or `!Sub` as a shorthand, is used to substitute variables from a text. It's a very handy function that will allow you to fully customize your templates.
	- For example, you can combine `Fn::Sub` with References or AWS Pseudo variables!
	- String must contain `${VariableName}` and will substitute them
	```yaml
	!Sub
		- String
		- { Var1Name: Var1Value, Var2Name: Var2Value }
	```
- [[CloudFormation#Conditions|Condition]] Functions (`Fn::If`, `Fn::Not`, `Fn::Equals`, etc...)

### Rollbacks
- Stack Creation Fails
	- Default: everything rolls back (gets deleted). We can look at the log
	- Option to disable rollback and troubleshoot what happened
- Stack Update Fails:
	- The stack automatically rolls back to the previous known working state
	- Ability to see in the log what happened and error messages

### CloudFormation Stack Notifications
- Send Stack events to SNS Topic (Email, Lambda,...)
- Enable SNS Integration using Stack Options![[Screenshot 2023-07-07 at 10.30.49 PM.png]]

### ChangeSets
- When you update a stack, you need to know what changes before it happens for greater confidence
- ChangeSets won't say if the update will be successful![[Screenshot 2023-07-07 at 10.32.17 PM.png]]

### Nested Stacks
- Nested stacks are stacks as a part of other stacks
- They allow you to isolate repeated patterns / common components in separate stacks and call them from other stacks
- Example:
	- Load Balancer configuration that is re-used
	- Security group that is re-used
- Nested stacks are considered best practice
- To update a nested stack, always update the parent (root stack)

### StackSets
- Create, update or delete stacks across multiple accounts and regions with a single operation
- Administrator account to create StackSets
- Trusted accounts to create, update, delete stack instances from StackSets
- When you update a stackSet, all associated stack instances are updated throughout all accounts and regions.

### CloudFormation Drift
- CloudFormation allows you to create infrastructure
- But it doesn't protect you against manual configuration changes
- How do we know if our resources have drifted
- We can use CloudFormation drift!
- Not all resources are supported yet: https://docs.aws.amazon.com/AWSCloudFormation/latest/UserGuide/resource-import-supported-resources.html
- You can check drift from the CloudFormation sidebar, drifts section

### Stack policies
- During a CloudFormation Stack update, all update actions are allowed on all resources (default)
- A Stack Policy is a json document that defines the update actions that are allowed on specific resources during stack updates.
- Eg: Allow updates on all resources except the production Database.
```json
"Statement": [
	{
		"Effect": "Allow",
		"Action": "Update:*",
		"Principal": "**",
		"Resource": "**"
	},
	{
		"Effect": "Deny",
		"Action": "Update:*",
		"Principal": "*",
		"Resource": "LogicalResourceId/ProductionDatabase"
	}
]
```
- Protect resources from unintentional updates
- When you set a Stack Policy, all resources in the Stack are protected by default
- Specify an explicit ALLOW for the resources you want to be allowed to be updated