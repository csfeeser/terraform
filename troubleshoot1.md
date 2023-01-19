# Morning Warmup: TROUBLESHOOTING!

<img src="https://blog.scottlogic.com/cmeehan/assets/meme.jpg" width="400"/>

1. Chances are we still have a lot of Docker containers and images running from un-destroyed TerraForm deployments. Run this command to **remove** all containers/images.

    `student@bchd:~$` `docker rm -vf $(docker ps -aq) && docker rmi -f $(docker images -aq)`

0. Create a new directory and move into it.

    `student@bchd:~$` `mkdir -p ~/mycode/troubleshoot && cd ~/mycode/troubleshoot`
    
0. Run the following command to grab a busted Terraform module! Your job is to repair the code blocks and get it running!

    `student@bchd:~$` `wget https://raw.githubusercontent.com/csfeeser/terraform/main/scripts/main ~/mycode/troubleshoot/main -q`

0. **Here's my recommendation.** Use `terraform init` and `terraform validate` (or try to anyway) and observe the errors. Rather than trying to fix every error you see right away, try to fix whatever issue is causing the error message each time you try to run Terraform.

0. When the command `curl localhost:8089` returns HTML to an nginx welcome page, you know you've succeeded!

**BONUS**: Can you get this to run without a deprecation error? 

- [docker/kreuzwerker docker_image syntax](https://registry.terraform.io/providers/kreuzwerker/docker/latest/docs/resources/image)
- [docker/kreuzwerker docker_container syntax](https://registry.terraform.io/providers/kreuzwerker/docker/latest/docs/resources/container)
- A recent, stable version of nginx is `1.23.2`


<details>
<summary>SOLUTION</summary>
<br>  
    
***FIRST, rename `main` to `main.tf` -->*** `mv main main.tf`

```java
terraform {                             # TERRAFORM BLOCK WAS MISSING
  required_providers {
    docker = {
      source  = "kreuzwerker/docker"
      version = "~> 2.22.0"             $ USE THE CORRECT VERSION
    }
  }
}                                       

provider "docker" {}

variable "container_name" {                                         # THIS
  description = "Value of the name for the Docker container"        # VARIABLE
  # basic types include string, number and bool                     # BLOCK
  type    = string                                                  # WAS
  default = "ExampleNginxContainer"                                 # MISSING
  }                                                                 # !

output "container_id" {
  description = "ID of the Docker container"
  value       = docker_container.nginx.id
}

output "image_id" {
  description = "ID of the Docker image"
  value       = docker_image.nginx.id      # TYPO- IT'S "docker_image" NOT "dockerimage"
}

resource "docker_image" "nginx" {
  name         = "nginx:1.23.2"            # USING VERSION NUMBER INSTEAD OF "LATEST"
  keep_locally = true
}

resource "docker_container" "nginx" {
  image = docker_image.nginx.image_id      # CHANGE "LATEST" TO "IMAGE_ID"
  name  = var.container_name
  ports {
    internal = 80
    external = 8089
  }
}                                          # MISSING CLOSING }
```
</details>
