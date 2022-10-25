## Lab 20 Challenge

Replace your `locals` block with this.

```
locals {
  avengers = {
               "Iron Man"= {
                "power"= "money"
                "enemy"= "Iron Monger"}
               "Black Panther"= {
                "power"= "vibranium suit"
                "enemy"= "War Monger"}
               "She-Hulk"= {
                "power"= "super strength"
                "enemy"= "Abomination"}
             }
}
```

Reconfigure your resource block so that in the trigger section it prints out the name, power, and enemy of each character.
> HINT: use each.key, each.value, and each.value.xxx

```
Outputs:

avengers = {
  "Black Panther" = {
    "id" = "3091107916042106755"
    "triggers" = tomap({
      "Enemy" = "War Monger"
      "Name" = "Black Panther"
      "Power" = "vibranium suit"
    })
  }
  "Iron Man" = {
    "id" = "1943189066931450445"
    "triggers" = tomap({
      "Enemy" = "Iron Monger"
      "Name" = "Iron Man"
      "Power" = "money"
    })
  }
  "She-Hulk" = {
    "id" = "1178594512809605325"
    "triggers" = tomap({
      "Enemy" = "Abomination"
      "Name" = "She-Hulk"
      "Power" = "super strength"
    })
  }
}
```


### SOLUTION

```
/* Alta3 Research - rzfeeser@alta3.com
   Working with "for_each" within a null_resource */

/* Terraform block */
terraform {
  required_providers {
    null = {
      source = "hashicorp/null"
      version = "3.1.1"
    }
  }
}

/* provider block */
provider "null" {
  # Configuration options
}

/* a list of local variables */
locals {
  avengers = {
               "Iron Man"= {
                "power"= "money"
                "enemy"= "Iron Monger"}
               "Black Panther"= {
                "power"= "vibranium suit"
                "enemy"= "War Monger"}
               "She-Hulk"= {
                "power"= "super strength"
                "enemy"= "Abomination"}
             }
}


resource "null_resource" "avengers" {
  for_each = local.avengers
  triggers = {
    Name= each.key
    Power= each.value.power
    Enemy= each.value.enemy
  }
}

/* We want these outputs */
output "avengers" {
  value = null_resource.avengers
}

output "justheroes" {
  value = keys(null_resource.avengers)
}
```
