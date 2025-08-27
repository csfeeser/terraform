# Lab: Terraform State — Backend Bulletproofing for Safe Infrastructure Management

### Objective

Learn how Terraform state works, what happens if you lose it, and how to protect it with S3 and DynamoDB for collaboration and locking.

## Part 1: Initialize a Terraform Project

1. Make a project directory:

   ```bash
   mkdir ~/demo1 && cd ~/demo1
   ```

2. Create `main.tf`:

   ```hcl
   provider "aws" {
     region = "us-west-2"
   }

   resource "aws_instance" "example_instance" {
     ami           = "ami-830c94e3"
     instance_type = "t2.micro"
     tags = {
       Name = "bravo"
     }
   }

   output "id" {
     description = "the unique ID of the instance"
     value       = aws_instance.example_instance.id
   }
   ```

3. Set AWS environment variables (use your own keys):

   ```bash
   export AWS_ACCESS_KEY_ID="YOUR_KEY"
   export AWS_SECRET_ACCESS_KEY="YOUR_SECRET"
   export AWS_DEFAULT_REGION="us-west-2"
   ```

4. Initialize and apply:

   ```bash
   terraform init
   terraform apply
   ```

5. Save the instance ID output in your notes.

6. Copy the state file so you can read it:

   ```bash
   cp terraform.tfstate ~/static/terraform.tfstate.json
   ```

7. Run `terraform apply` again. No changes should occur.

---

## Part 2: Simulate Losing State

1. Delete the state file:

   ```bash
   rm terraform.tfstate
   ```

2. Run:

   ```bash
   terraform plan
   ```

   Notice the errors: Terraform no longer knows what exists.

---

## Part 3: Recover State

1. Import the resource back into state (replace with your saved ID):

   ```bash
   terraform import aws_instance.example_instance i-0123456789abcdef0
   ```

---

## Part 4: Store State in S3

1. In the AWS Console, create an S3 bucket:

   * Name: `omega-bucket`
   * Region: `us-west-2`
   * Disable “Block all public access”
   * Enable **versioning**

2. Add backend config to `main.tf`:

   ```hcl
   terraform {
     backend "s3" {
       bucket = "omega-bucket"
       key    = "wakkawakka.tfstate"
       region = "us-west-2"
     }
   }
   ```

3. Reinitialize with migration:

   ```bash
   terraform init
   ```

   Terraform moves your local state into the bucket.

4. Apply again and confirm the state file exists in S3.

---

## Part 5: Collaboration and Locking

1. Make a copy of your project:

   ```bash
   cp -r ~/demo1 ~/demo2
   cd ~/demo2
   ```

2. Run `terraform apply` in both directories at the same time. Both will succeed because there’s no locking yet.

---

## Part 6: Enable Locking with DynamoDB

1. In the AWS Console, create a DynamoDB table:

   * Table name: `terraform-state-lock`
   * Partition key: `LockID` (string)

2. Update `main.tf` backend config:

   ```hcl
   terraform {
     backend "s3" {
       bucket         = "omega-bucket"
       key            = "wakkawakka.tfstate"
       region         = "us-west-2"
       dynamodb_table = "terraform-state-lock"
     }
   }
   ```

3. Reinitialize with migration:

   ```bash
   terraform init -migrate-state
   ```

4. Now try running `terraform apply` from both directories at the same time:

   * The first succeeds.
   * The second fails with a locking error.

### Wrap-Up

* **Local state is fragile.**
* **S3 backend + versioning protects it.**
* **DynamoDB lock prevents collisions in teamwork.**
