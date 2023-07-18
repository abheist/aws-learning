#AWS #terraform 

- Install [Terraform](Terraform.md) CLI
    - [https://developer.hashicorp.com/terraform/tutorials/aws-get-started/install-cli](https://developer.hashicorp.com/terraform/tutorials/aws-get-started/install-cli)
- Install AWS CLI
    - [https://docs.aws.amazon.com/cli/latest/userguide/getting-started-install.html](https://docs.aws.amazon.com/cli/latest/userguide/getting-started-install.html)
    - [https://formulae.brew.sh/formula/awscli](https://formulae.brew.sh/formula/awscli)
    - once installed, check with the following command
    - `which aws`
        - configure [AWS](../AWS.md) by `aws configure`
        - For starting create a admin user from IAM service
        - Pick `Access Key` and `Secret` from AWS IAM service for the newly created admin user. Try not to use root user.
- create a project folder
    - `mkdir learn-terraform-aws-instance`
- change to project directory
    - `cd learn-terraform-aws-instance`
- create new terraform file
    - `touch main.tf`
- paste the following into the `main.tf` file.

```json
terraform {
  required_providers {
    aws = {
      source  = "hashicorp/aws"
      version = "~> 4.16"
    }
  }

  required_version = ">= 1.2.0"
}

provider "aws" {
  region  = "us-west-2"
}

resource "aws_instance" "app_server" {
  ami           = "ami-830c94e3"
  instance_type = "t2.micro"

  tags = {
    Name = "ExampleAppServerInstance"
  }
}
```

- `terraform {}` block contains terraform settings. Contains required providers and provider versions. These providers can be found on [terraform registry](https://registry.terraform.io/) page. Provide the source and version, if version is not defined, then the latest version will be installed.
    
- `provider {}` block configures the specific providers defined under `terraform {}` block. You can use multiple `provider {}` block to configure different providers. or can use different providers together, eg: can pass the IP address of your AWS EC2 instance to a monitoring resource from DataDog.
    
- `resource {}` block to define components of your infrastructure. A resource might be a physical or virtual component, such as EC2 instance, or a Heroku application. Resource block have two strings before the block:
    
    - `resource type` : in the above example, resource type is `aws_instance`
    - `resource name` : in the above example, resource name is `app_server`
    
    together with the `resource type` and `resource name` form a unique ID for the resource. For example, the ID for your EC2 instance is `aws_instance.app_server`.
    

### Initialize the directory

When you create a new configuration - or check our an existing configuration from version control - you need to initialize the directory with `terraform init`.

It downloads and install the providers defined in the configuration. and creates a lock file named `.terraform.lock.hcl`

### Format and validate configuration

`terraform fmt` command automatically updates configuration in the current directory for readability and consistency.

`terraform validate` validates the syntax and configuration.

### Create infrastructure

`terraform apply` applies the changes to the infrastructure. before it applies, it prints out the execution plan which describes the action terraform will take in order to change your infra to match the configuration.

Terraform lays out the plan and pause for the approval. Once approved, it proceeds further to execution.

### Inspect state

Once the configurations are applied, terraform writes the data into a file called `terraform.tfstate`. Terraform stores the IDs and properties of the resources it manages in this file, so that it can update or destroy those resources going forward.

this files is the only way terraform can track which resources it manages. and often contains sensitive information, so you must store your state file securely and restrict access to only trusted team members. This file can be stored safely on Terraform Cloud or other [backend it supports](https://developer.hashicorp.com/terraform/language/settings/backends/configuration).

`terraform show` can show you the current state.

### Manually managing state

`terraform state` is a management command can be used for checkout current settings. eg: `terraform state list` will show the current resource.

### Resources:

- [https://developer.hashicorp.com/terraform/language](https://developer.hashicorp.com/terraform/language)
- [https://developer.hashicorp.com/terraform/language/providers](https://developer.hashicorp.com/terraform/language/providers)
- [https://developer.hashicorp.com/terraform/intro/use-cases](https://developer.hashicorp.com/terraform/intro/use-cases)
- [https://registry.terraform.io/providers/hashicorp/aws/latest/docs#authentication](https://registry.terraform.io/providers/hashicorp/aws/latest/docs#authentication)
- [https://developer.hashicorp.com/terraform/cli/commands/state](https://developer.hashicorp.com/terraform/cli/commands/state)

## Infrastructure change

To change anything in the Infra, change the `[main.tf](<http://main.tf>)` file and `apply`.

eg: change the ami from above to below:

```json
-  ami           = "ami-830c94e3"
+  ami           = "ami-08d70e59c07c61a3a"
```

and now run the `terraform apply` , terraform will prompt with the change it’ll perform and ask for the approval, once approved, it’ll apply the changes to the AWS.

## Destroy Infrastructure

`terraform destroy` command destroy all the resources of the infrastructure. Once the command is executed, terraform will layout the plan in which it’ll preform the actions and prompt for approval, once approved, it’ll destroy the infra and execute, so that it will not have dependency error.

## Define Input variables

create a new file `[variables.tf](<http://variables.tf>)` and define the following inside it

```json
variable "instance_name" {
  description = "Value of the Name tag for the EC2 instance"
  type        = string
  default     = "ExampleAppServerInstance"
}
```

and update the hard-coded value with variable in `main.tf`

```json
tags = {
	-    Name = "ExampleAppServerInstance"
	+    Name = var.instance_name
}
```

Once done, run `terraform apply` . It will not show any difference, since the hard-coded value and new variable values are same.

To see the changes, run the following command in the shell.

```json
terraform apply -var "instance_name=YetAnotherName"
```

It’ll ask for the approval, approve it and it’ll update the name to new one on aws.

### Resources:

- [https://developer.hashicorp.com/terraform/tutorials/configuration-language/variables](https://developer.hashicorp.com/terraform/tutorials/configuration-language/variables)

## Query data with Outputs

Create a new file named `outputs.tf`, which will have the variable/keys which you need to store from the AWS. Like, instance_id, instance_public_ip. Define the following in the file:

```json
output "instance_id" {
  description = "ID of the EC2 instance"
  value       = aws_instance.app_server.id
}

output "instance_public_ip" {
  description = "Public IP address of the EC2 instance"
  value       = aws_instance.app_server.public_ip
}
```

once done, run `terraform apply`. It’ll store the required output. and you can check those output with `terraform output` command. and can be used at different places.