# Morning Warmup: TROUBLESHOOTING!

<img src="https://preview.redd.it/0ual75xi5ri01.jpg?auto=webp&s=7dca4f4d1a1b53bb36674b9431112e961a53f26d" alt="drawing" width="300"/>

### Getting Set Up

1. First check and see if you have any containers still running from yesterday.

    `student@bchd:~$` `sudo docker ps`

0. If there are any, let's wipe them out (no need to use `terraform destroy` in this case). Check your `groups` to see if you are part of the `docker` group.

    `student@bchd:~$` `groups`

0. Do you see `docker` listed? If not, run these steps:

    `student@bchd:~$` `sudo usermod -aG docker student`  
    `student@bchd:~$` `su - $USER`  
    > password is alta3

0. Now run this command to **stop** and **remove** all containers.

    `student@bchd:~$` `sudo docker stop $(docker ps -aq) && sudo docker rm $(docker ps -aq)`

0. Close any open tmux tabs you have! Keep typing `exit` or press *ctrl d* repeatedly until this shows up:

    <img src="https://github.com/csfeeser/terraform/blob/main/scripts/connectionclosed.png?raw=true" alt="drawing" width="300"/>

0. But don't worry! Just refresh your browser and you're good to go!

### WARMUP OBJECTIVE:

1. Create a new directory and move into it.

    `student@bchd:~$` `mkdir -p ~/mycode/troubleshoot && cd ~/mycode/troubleshoot`
    
0. Run the following command to grab a busted Terraform module! Your job is to repair the code blocks and get it running!

    `student@bchd:~$` `wget https://raw.githubusercontent.com/csfeeser/terraform/main/scripts/main ~/mycode/troubleshoot/main -q`

0. **Here's my recommendation.** Use `terraform init` and `terraform apply` (or try to anyway) and observe the errors. Rather than trying to fix every error you see, try to fix whatever issue is causing the error message each time you try to run Terraform.

0. When `sudo docker ps` shows a running container then you'll know you've succeeded :)

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



provider "docker" {}
```
