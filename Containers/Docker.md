- Docker is a software development platform to deploy apps
- App are packaged in containers that can be run on any OS
- Apps run the same, regardless of where they run
	- Any machine
	- No compatibility issue
	- Predictable behaviour
	- Less work
	- Easier to maintain and deploy
	- Works with any language, any OS, any technology
- Use cases: micro-services architecture, list and shift apps from on-premises to the AWS cloud.
- Docker images are stored in Docker Repositories
	- Docker Hub (https://hub.docker.com)
		- Public repository
		- Support private repositories as well.
		- Find base images for many technologies or OS (eg: Ubuntu, MySQL...)
	- Amazon [ECR](ECR.md) (Amazon Elastic Container Registry)
		- Private repository
		- Public repository (Amazon ECR public Gallery https://gallery.ecr.aws)

### Docker v/s Virtual machines
- Docker is sort of virtualization technology, but not exactly
- Resources are shared with the host → many containers on one server![Screenshot 2023-07-03 at 1.54.28 PM](../images%201/Screenshot%202023-07-03%20at%201.54.28%20PM.png)

### Get started with Docker![Screenshot 2023-07-03 at 1.55.51 PM](../images%201/Screenshot%202023-07-03%20at%201.55.51%20PM.png)

### Docker Containers Management on AWS
- Amazon Elastic Container Service (Amazon [ECS](ECS.md))
	- Amazon's own container platform
- Amazon Elastic Kubernetes Service (Amazon [EKS](EKS.md))
	- Amazon's managed [Kubernetes](Kubernetes) (open source)
- AWS [Fargate](Fargate.md)
	- Amazon's own Serverless container platform
	- Works with ECS and with EKS
- Amazon [ECR](ECR.md)
	- Store container images