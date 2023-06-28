# Warming up with Child Modules!

Create a new directory and move into it:

`student@bchd:~$` `mkdir -p ~/mycode/day3-warmup && cd ~/mycode/day3-warmup`

Clone a solution that contains a simple Terraform root and child module (we used this yesterday).

`student@bchd:~/mycode/day3-warmup$` `git clone https://github.com/rzfeeser/basic_terraform_module .`

Make a new child module directory in the folder you just downloaded.

`student@bchd:~/mycode/day3-warmup$` `mkdir ~/mycode/day3-warmup/basic_terraform_module/my_modules/thedudeabides`

### OBJECTIVE:

1. Create a `main.tf` file in `day3-warmup/basic_terraform_module/my_modules/thedudeabides`. Create a null resource block!

```hcl
resource "null_resource" "lebowski" {
   triggers= {
         thatsjustlike= "youropinionman"
             }
}
```

2. In the `main.tf` file in the ROOT module, add a module block to run this very simple child block in addition to everything else this module is creating.
