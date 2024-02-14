# Warming up with Child Modules!

<img src="https://i.imgflip.com/7qvcap.jpg" alt="drawing" width="300"/>



Head to your home directory.

`student@bchd:~$` `cd`

Clone a solution that contains a simple Terraform root and child module.
> **IMPORTANT-** you must include the "." at the end of this command!

`student@bchd:~/day3-warmup$` `git clone https://github.com/rzfeeser/basic_terraform_module ~/day3-warmup`

Make a new child module directory in the folder you just downloaded.

`student@bchd:~/day3-warmup$` `mkdir -p ~/day3-warmup/my_modules/thedudeabides`

### OBJECTIVE:

1. Create a `main.tf` file in `day3-warmup/my_modules/thedudeabides`. Create a null resource block!

```hcl
resource "null_resource" "lebowski" {
   triggers= {
         thatsjustlike= "youropinionman"
             }
}
```

2. In the `main.tf` file in the ROOT module, add a module block to run this very simple child block in addition to everything else this module is creating.

<details>
<summary>SOLUTION</summary>
<br>
   
Create a .tf file inside your *thedudeabides* child module directory and place your resource block into it:

`student@bchd:~/day3-warmup$` `vim ~/day3-warmup/my_modules/thedudeabides/main.tf`

```hcl
resource "null_resource" "lebowski" {
   triggers= {
         thatsjustlike= "youropinionman"
             }
}
```

Edit your `main.tf` file in your root module and add a module block that calls it:

`student@bchd:~/day3-warmup$` `vim ~/day3-warmup/main.tf`

```hcl
# ADD AT THE END OF THE DOCUMENT

module "nullresourcemaker" {
    source = "./my_modules/thedudeabides"
}
```

Back at your command line, run your module!

`terraform init`

`terraform apply -auto-approve`

Now check the objects you made. You should have the following:

`terraform state list`

```
docker_container.simplegoservice
docker_image.simplegoservice
module.my_container.docker_container.nginx
module.my_container.docker_image.nginx
module.nullresourcemaker.null_resource.lebowski
```

</details>
