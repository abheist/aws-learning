---
aliases:
- Elastic Container Registry
---
- Store and manage Docker images on AWS
- Private and Public repository (Amazon ECR Public Gallery https://gallery.ecr.aws)
- Fully integrated with ECS, backed by Amazon S3
- Access is controlled through IAM (permission errors => policy)
- Supports image vulnerability scanning, versioning, image tags, image lifecycle,...![[Screenshot 2023-07-04 at 4.35.49 PM.png]]

### Hands on
- Using AWS CLI
	- AWS CLI v2
		- `aws ecr get-login-password --region region | docker login --username AWS --password-stdin aws_account_id.dkr.ecr.region.amazonaws.com`
	- Docker Commands
		- Push: `docker push aws_account_id.dkr.ecr.region.amazonaws.com/demo:latest`
		- Pull: `docker pull aws_account_id.dkr.ecr.region.amazonaws.com/demo:latest`
	- In case an EC2 instance (or you) can't pull a Docker image, check IAM permissions
- Go to `Amazon ECR Repositores` section
- Create repository, a form will open
- Visibility settings
	- Private (select)
	- Public
- Repo name
- Create
- Once created, click on the repo, go to `images` section
- Upload the docker image and you'll get the Image URI which can be used in ECS