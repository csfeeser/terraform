## Warming up with Loops!

<img src="https://miro.medium.com/max/651/1*feDY6Noa9b7WmizyIMcASQ.jpeg" alt="drawing" width="300"/>

Yesterday we started exploring the wonderful world of loops in Terraform! Today we'll try to apply it a bit more practically!

#### PART 1:
- Take the module below. We'll use `null_resource`, but pretend we're creating resource groups in Azure. 
- Add a `for_each` meta-argument to the resource block; loop over the `rgs` variable in `locals`.
- `[alpha, bravo, charlie]` are the names of the "resource groups," and `[eastus, southindia, westus2]` are the regions!

```terraform
locals {  rgs = {
          "alpha" = "eastus"
          "bravo" = "southindia"
          "charlie" = "westus2"
       }}

resource "null_resource" "dummy_rgs" {
  triggers = {
               name= "???"
               region= "???"
}
}
```

<details>
<summary>PART 1 SOLUTION</summary>
<br>
          
```terraform
locals {  rgs = {
          "alpha" = "eastus"
          "bravo" = "southindia"
          "charlie" = "westus2"
       }}

resource "null_resource" "dummy_rgs" {
  for_each = tomap(local.rgs)
  triggers = {
               name= each.key
               region= each.value
}
}
```
          
</details>

#### PART 2:
A glutton for punishment, eh?

- Take the module below. We'll use `null_resource` again but this time we'remaking resource groups AND vnets! 
- The data in locals is different this time. Create the three resource groups as before.
- Now we need to create *vnets* (still only using `null_resource`). Again using `for_each`, create three vnets from the `locals` block. Respectively, the vnet name/region should be `{omega: eastus, psi: southindia, chi: westus2}`

```terraform
locals {  rgs = {
          "alpha" = { "region" ="eastus"
                      "vnet" ="omega" }
          "bravo" = { "region" ="southindia"
                      "vnet" ="psi" }
          "charlie" = {"region" = "westus2"
                       "vnet" ="chi" }
                }}

resource "null_resource" "dummy_rgs" {
  triggers = {
               name= "???"     // alpha, bravo, charlie
               region= "???"   // eastus, southindia, westus2
}
}

resource "null_resource" "dummy_vnets" {
  triggers = {
               name= "???"        // use value of "vnet" above
               region= "???"      // use value of "region" above
}
}
```

### SOLUTIONS




<details>
<summary>PART 2 SOLUTION</summary>
<br>
          
```terraform
locals {  rgs = {
          "alpha" = { "region" ="eastus"
                      "vnet" ="omega" }
          "bravo" = { "region" ="southindia"
                      "vnet" ="psi" }
          "charlie" = {"region" = "westus2"
                       "vnet" ="chi" }
                }}

resource "null_resource" "dummy_rgs" {
  for_each = local.rgs
  triggers = {
               name= each.key     // alpha, bravo, charlie
               region= each.value.region   // eastus, southindia, westus2
}
}

resource "null_resource" "dummy_vnets" {
  for_each = local.rgs
  triggers = {
               name= each.value.vnet        // use value of "vnet" above
               region= each.value.region      // use value of "region" above
               rg= each.key
}
}
```
          
</details>
