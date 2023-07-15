#docker #terraform

To use [[Docker]] with [[Terraform]]. Follow the following steps:
- Create a new directory `mkdir learn-terraform-docker-container`
- Go inside that directory
- Create a new file `main.tf`
	```json
	terraform {
		required_providers {
			docker = {
				source = "kreuzwerker/docker"
				version = "~> 3.0.1"
			}
		}
	}

	provider "docker" {}

	resource "docker_image" "nginx" {
		name = "nginx"
		keep_locally = false
	}

	resource "docker_container" "nginx" {
		image = docker_image.nginx.image_id
		name = "tutorial"

		ports {
			internal = 80
			external = 8000
		}
	}
	```

- Initialize the project
```zsh
terraform init
```

- now provision the NGINX server with `apply`. When terraform request for approval, type `yes`
```shell
terraforma apply
```

- Verify the existence of the NGINX container by visiting the `localhost:8000` in your web browser or running `docker ps` to see the container
```shell
docker ps
```

- to stop the container, run `terraform destroy`
