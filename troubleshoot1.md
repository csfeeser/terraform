# Morning Warmup: TROUBLESHOOTING!

<img src="https://preview.redd.it/0ual75xi5ri01.jpg?auto=webp&s=7dca4f4d1a1b53bb36674b9431112e961a53f26d" alt="drawing" width="300"/>

1. Chances are we still have a lot of Docker containers running from un-destroyed TerraForm deployments. Run this command to **stop** and **remove** all containers.

    `student@bchd:~$` `sudo docker stop $(docker ps -aq) && sudo docker rm $(docker ps -aq)`

0. Create a new directory and move into it.

    `student@bchd:~$` `mkdir -p ~/mycode/troubleshoot && cd ~/mycode/troubleshoot`
    
0. Run the following command to grab a busted Terraform module! Your job is to repair the code blocks and get it running!

    `student@bchd:~$` `wget https://raw.githubusercontent.com/csfeeser/terraform/main/scripts/main ~/mycode/troubleshoot/main -q`

0. **Here's my recommendation.** Use `terraform init` and `terraform validate` (or try to anyway) and observe the errors. Rather than trying to fix every error you see right away, try to fix whatever issue is causing the error message each time you try to run Terraform.

0. When the command `curl localhost:8089` returns HTML to an nginx welcome page, you know you've succeeded!

**BONUS**: Can you get this to run without a deprecation error? 

- [docker/kreuzwerker docker_image syntax](https://registry.terraform.io/providers/kreuzwerker/docker/latest/docs/resources/image)
- [docker/kreuzwerker docker_container syntax](https://registry.terraform.io/providers/kreuzwerker/docker/latest/docs/resources/container)
- The most recent version of nginx is `1.23.2`

<!--
### SOLUTION:

Make sure you are in the `docker` group and rename `main` to `main.tf`

```
terraform {
  required_providers {
    docker = {
      source  = "kreuzwerker/docker"
      version = "~> 2.13.0"
    }
  }
}


provider "docker" {}

variable "container_name" {
  description = "Value of the name for the Docker container"
  # basic types include string, number and bool
  type    = string
  default = "ExampleNginxContainer"
  }

output "container_id" {
  description = "ID of the Docker container"
  value       = docker_container.nginx.id
}

output "image_id" {
  description = "ID of the Docker image"
  value       = docker_image.nginx.id
}

resource "docker_image" "nginx" {
  name         = "nginx:latest"
  keep_locally = true
}

resource "docker_container" "nginx" {
  image = docker_image.nginx.latest
  name  = var.container_name
  ports {
    internal = 80
    external = 8089
  }
}
```
