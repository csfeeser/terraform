## Warming up with Loops!

Yesterday we were able to put Terraform into actual practice by automating the creation of infrastructure on Azure. The next step you'll want to take is by doing more with less... by using loops!

#### PART 1:
- Take the module below. We'll use `null_resource` because not everyone has access to Azure. 
- Add a `for_each` meta-argument to the resource block; loop over the `rgs` variable in `locals`.
- `[alpha, bravo, charlie]` are the names of the "resource groups," and `[eastus, southindia, westus2]` are the regions!

```
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

#### PART 2:
A glutton for punishment, eh?

- Take the module below. We'll use `null_resource` because not everyone has access to Azure. 
- You'll need to tweak your `for_each` with the "dummy_rgs" resource block; loop over the `rgs` variable in `locals`.
- Now we need to create *vnets* (still only using `null_resource`). Again using `for_each`, create three vnets from the `locals` block. Respectively, the vnet name/region should be `{omega: eastus, psi: southindia, chi: westus2}`

```
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
