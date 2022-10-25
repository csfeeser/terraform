**Student Request:** *Can the output of `curl http://0.0.0.0:5432/alta3` be made into an output value?*

### Original Challenge Solution Code

```
/* Challenge Solution - Terraform and Docker
   main.tf */

terraform {
  required_providers {
    docker = {
      source  = "kreuzwerker/docker"
      version = "~> 2.22.0"
    }
  }
}

provider "docker" {}

resource "docker_image" "simpleflaskservice" {
  name         = "registry.gitlab.com/alta3research/simpleflaskservice:1.0"
  keep_locally = true      // keep image after "destroy"
}

resource "docker_container" "simpleflaskservice" {
  image = docker_image.simpleflaskservice.image_id
  name = var.container_name
  ports {
    internal = var.internal_port
    external = var.external_port
  }
}

# default value of container_name should be AltaResearchWebService
variable "container_name" {
  description = "name of the container"
  type        = string
  default     = "AltaResearchWebService"
}

# default value of internal_port should be 9876
variable "internal_port" {
  description = "internal port to be used"
  type        = number
  default     = 9876
}

# default value of internal_port should be 5432
variable "external_port" {
  description = "external port to be used"
  type        = number
  default     = 5432
}
```

### Additional blocks to make a HTTP response as output

```
provider "http" {}
provider "time" {}

data "http" "example" {
  url = "http://${docker_container.simpleflaskservice.ports[0].ip}:${docker_container.simpleflaskservice.ports[0].external}/alta3" 
  depends_on= [time_sleep.wait]
  # Optional request headers
  request_headers = {
    Accept = "application/json"
  }
}

resource "time_sleep" "wait" {
  depends_on = [docker_container.simpleflaskservice]
  create_duration = "10s"
}

output "api_request" {
  value       = data.http.example.response_body
}
```

### New Output:

```
Outputs:

api_request = <<EOT
{"alta3":{"homepage":"https://alta3.com","posters":"https://alta3.com/posters","youtube":"https://www.youtube.com/user/Alta3Research/videos"},"thanks":"Thank you for training with Alta3 Research!","version":"0.1"}

EOT
```
