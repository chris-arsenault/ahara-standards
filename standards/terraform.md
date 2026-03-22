# Terraform Standards

## Version

```hcl
terraform {
  required_version = ">= 1.12"
  required_providers {
    aws = {
      source  = "hashicorp/aws"
      version = "~> 6.0"
    }
  }
}
```

## Backend

Single shared state bucket. Key must use the namespaced path convention:

```hcl
backend "s3" {
  region       = "us-east-1"
  key          = "projects/<name>.tfstate"
  encrypt      = true
  use_lockfile = true
}
```

The `bucket` is passed via `-backend-config` in the deploy script.

Never use bare `terraform.tfstate` as a key.

## Default Tags

```hcl
provider "aws" {
  region = "us-east-1"
  default_tags {
    tags = {
      Project   = "<name>"
      ManagedBy = "Terraform"
    }
  }
}
```

## Formatting

```bash
terraform fmt -check -recursive infrastructure/terraform/
```

Enforced by pre-commit hooks and CI.

## Resource Naming

All resources use the project prefix: `<prefix>-<resource>`.

## State

- Never use `terraform_remote_state` — read from SSM.
- State bucket defaults are baked into `scripts/deploy.sh`.
- Use `-reconfigure` on `terraform init` to avoid interactive prompts.
