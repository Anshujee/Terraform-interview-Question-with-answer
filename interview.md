# Terraform Interview Notes 📘

This README is a **complete revision guide** for common Terraform interview questions, written in a **hands-on, experience-based way**.
You can safely use this for:

* Interview preparation
* Quick revision
* GitHub reference
* Explaining concepts confidently as if you have real project experience

---

## Table of Contents

1. Managing Multiple Environments (Dev, Staging, Prod)
2. How to Create Environments in Terraform (Steps & Commands)
3. Terraform Variables & tfvars Explained
4. Terraform Workspaces vs tfvars
5. Terraform State Management
6. `for_each` vs `for` in Terraform
7. Terraform Modules – How They Work & Why We Use Them

---

## 1️⃣ How Do You Manage Multiple Environments in Terraform?

### Real-World Explanation

In real projects, environments like **dev, staging, and prod** are treated as **separate systems**. We manage them using:

* Same Terraform code
* Different configurations
* Separate state files

### Key Principles

* Isolation between environments
* Same architecture across environments
* Different capacity & behavior using variables
* No manual changes

### Common Approach

```text
terraform-infra/
├── modules/
├── dev/
├── staging/
└── prod/
```

Each folder represents one environment.

---

## 2️⃣ How Do You Create Different Environments in Terraform?

Terraform does NOT have a built-in "environment" concept.

> **An environment is created by how we organize code, state, and variables.**

### Steps to Create Environments

1. Create environment folders (`dev`, `staging`, `prod`)
2. Use the same `main.tf`
3. Use different `backend.tf` per environment
4. Use different `.tfvars` files
5. Run Terraform separately in each folder

### Commands

```bash
cd dev
terraform init
terraform plan -var-file="dev.tfvars"
terraform apply -var-file="dev.tfvars"
```

Repeat the same steps for staging and prod.

---

## 3️⃣ What Goes Inside dev.tfvars, staging.tfvars, prod.tfvars?

### Purpose of tfvars

`.tfvars` files define **environment-specific values**.

> Terraform code stays same, values change per environment.

### Example Variables

```hcl
variable "environment" {}
variable "instance_type" {}
variable "instance_count" {}
```

### dev.tfvars

```hcl
environment    = "dev"
instance_type  = "t3.micro"
instance_count = 1
```

### staging.tfvars

```hcl
environment    = "staging"
instance_type  = "t3.small"
instance_count = 2
```

### prod.tfvars

```hcl
environment    = "prod"
instance_type  = "t3.large"
instance_count = 4
```

---

## 4️⃣ Terraform Workspaces vs tfvars

### Can Workspaces Create Environments?

Yes, but **not recommended for production**.

### What Workspaces Do

* Create separate Terraform state files
* Use the same code

```bash
terraform workspace new dev
terraform workspace select dev
```

### Why Teams Avoid Workspaces for Prod

* High risk of applying to wrong environment
* Harder CI/CD integration
* Complex conditional logic

### Interview-Safe Line

> "We prefer folder-based separation with separate state and tfvars for production environments."

---

## 5️⃣ How Did You Manage Terraform State?

### Key Idea

Terraform state is a **critical production asset**.

### What We Avoided

* Local `.tfstate` files
* Manual state editing

### What We Used

* Remote backend
* State locking
* Environment-level isolation

### Best Practices

* One state per environment
* State stored centrally
* Encrypted & access-controlled
* Terraform runs via CI/CD for prod

### Interview Answer

> "We managed Terraform state using a remote backend with locking enabled and separate state per environment to ensure safety and collaboration."

---

## 6️⃣ Difference Between `for_each` and `for` in Terraform

### Core Difference

| Feature | for_each           | for            |
| ------- | ------------------ | -------------- |
| Purpose | Create resources   | Transform data |
| Level   | Resource / module  | Expression     |
| Output  | Multiple resources | List / map     |

---

### `for_each` Example

```hcl
resource "aws_instance" "app" {
  for_each      = var.instances
  instance_type = each.value
}
```

### `for` Example

```hcl
locals {
  instance_map = { for env in var.envs : env => "t3.micro" }
}
```

### Interview Line

> "We use `for` to prepare data and `for_each` to create resources."

---

## 7️⃣ Terraform Modules – How They Work & Why We Use Them

### What Is a Module?

A Terraform module is a **reusable infrastructure blueprint**.

> A module is like a function in programming.

---

### Module Structure

```text
modules/ec2/
├── main.tf
├── variables.tf
└── outputs.tf
```

### Calling a Module

```hcl
module "ec2" {
  source        = "../modules/ec2"
  instance_type = "t3.micro"
  environment   = "dev"
}
```

---

### Why We Use Modules

* Reusability
* Consistency
* Maintainability
* Cleaner root modules
* Team collaboration

### Best Practice

> Keep modules generic and environment-agnostic.

---

## Final Interview Summary (Use This Anywhere)

> "We manage Terraform infrastructure using reusable modules, environment-specific configurations, and isolated remote state. Environments are separated using folders and tfvars, state is securely managed with locking, and infrastructure is deployed safely through controlled workflows."

---

## How to Use This README

* Revise before interviews
* Upload to GitHub as reference
* Explain concepts confidently
* Treat this as your Terraform foundation

---

Happy Learning & Best of Luck 🚀
