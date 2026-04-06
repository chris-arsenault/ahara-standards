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

## Shared Modules

Use the [ahara-tf-patterns](https://github.com/chris-arsenault/ahara-tf-patterns) modules for standard platform integrations:

```hcl
source = "git::https://github.com/chris-arsenault/ahara-tf-patterns.git//modules/<module>"
```

| Module | Purpose |
|--------|---------|
| `platform-context` | Reads shared platform resources (VPC, ALB, Cognito, RDS) |
| `alb-api` | Lambda API(s) behind the shared ALB with JWT auth |
| `lambda` | Standardized Lambda function (used by alb-api and standalone) |
| `website` | Site on CloudFront + S3 with custom domain, optional OG server for dynamic meta tags |
| `cognito-app` | Register an app client with the shared Cognito pool |

## Resource Discovery Tags

Platform infrastructure uses tags for cross-project resource discovery. Prefer tag-based lookups over SSM where available.

| Tag | Values | Resource Type | Example |
|-----|--------|---------------|---------|
| `vpc:role` | `platform` | VPC | `data "aws_vpc" { filter { name = "tag:vpc:role" values = ["platform"] } }` |
| `lb:role` | `platform` | ALB | `data "aws_lb" { tags = { "lb:role" = "platform" } }` |
| `subnet:access` | `private`, `public` | Subnets | `data "aws_subnets" { filter { name = "tag:subnet:access" values = ["private"] } }` |
| `sg:role` + `sg:scope` | See below | Security Groups | `data "aws_security_group" { filter { ... } }` |

**Security group role/scope pairs:** `lambda`/`platform`, `vpn-client`/`platform`, `alb`/`public`, `rds`/`platform`, `nat`/`internet`, `reverse-proxy`/`base`, `wireguard`/`truenas`.

Route53 zone is discovered by name: `data "aws_route53_zone" { name = "ahara.io." }`.

The `platform-context` module handles all of these lookups — use it instead of writing raw data sources.

## State

- Never use `terraform_remote_state` — use tag-based lookups or SSM via `platform-context`.
- State bucket defaults are baked into `scripts/deploy.sh`.
- Use `-reconfigure` on `terraform init` to avoid interactive prompts.
