# Security & Compliance

> **Part of:** [iac-skill](../SKILL.md)
> **Purpose:** Security best practices and compliance patterns for Terraform/OpenTofu

This document provides security hardening guidance and compliance automation strategies for infrastructure-as-code.

## Common Security Issues

### ❌ DON'T: Store Secrets in Variables

```hcl
# BAD: Secret in plaintext
variable "database_password" {
  type    = string
  default = "SuperSecret123!"  # ❌ Never do this
}
```

### ✅ DO: Use Secrets Manager

```hcl
# Good: Reference secrets from AWS Secrets Manager
data "aws_secretsmanager_secret_version" "db_password" {
  secret_id = "prod/database/password"
}

resource "aws_db_instance" "this" {
  password = data.aws_secretsmanager_secret_version.db_password.secret_string
}
```

### ❌ DON'T: Use Default VPC

```hcl
# BAD: Default VPC has public subnets
resource "aws_instance" "app" {
  ami           = "ami-12345"
  subnet_id     = "subnet-default"  # ❌ Avoid default resources
}
```

### ✅ DO: Create Dedicated VPCs

```hcl
# Good: Custom VPC with private subnets
resource "aws_vpc" "this" {
  cidr_block           = "10.0.0.0/16"
  enable_dns_hostnames = true
}

resource "aws_subnet" "private" {
  vpc_id            = aws_vpc.this.id
  cidr_block        = "10.0.1.0/24"
  availability_zone = "us-east-1a"
}
```

### ❌ DON'T: Skip Encryption

```hcl
# BAD: Unencrypted S3 bucket
resource "aws_s3_bucket" "data" {
  bucket = "my-data-bucket"
  # ❌ No encryption configured
}
```

### ✅ DO: Enable Encryption at Rest

```hcl
# Good: Enable encryption
resource "aws_s3_bucket" "data" {
  bucket = "my-data-bucket"
}

resource "aws_s3_bucket_server_side_encryption_configuration" "data" {
  bucket = aws_s3_bucket.data.id

  rule {
    apply_server_side_encryption_by_default {
      sse_algorithm = "AES256"
    }
  }
}
```

### ❌ DON'T: Open Security Groups to Internet

```hcl
# BAD: Security group open to internet
resource "aws_security_group_rule" "allow_all" {
  type              = "ingress"
  from_port         = 0
  to_port           = 65535
  protocol          = "tcp"
  cidr_blocks       = ["0.0.0.0/0"]  # ❌ Never do this
  security_group_id = aws_security_group.this.id
}
```

### ✅ DO: Use Least-Privilege Security Groups

```hcl
# Good: Restrict to specific ports and sources
resource "aws_security_group_rule" "app_https" {
  type              = "ingress"
  from_port         = 443
  to_port           = 443
  protocol          = "tcp"
  cidr_blocks       = ["10.0.0.0/16"]  # ✅ Internal only
  security_group_id = aws_security_group.this.id
}
```

## Secrets Management

### AWS Secrets Manager Pattern

```hcl
ephemeral "random_password" "db_password" {
  length           = 16
  override_special = "!#$%&*()-_=+[]{}<>:?"
}

resource "aws_secretsmanager_secret" "db_password" {
  name = "db_password"
}

resource "aws_secretsmanager_secret_version" "db_password" {
  secret_id                = aws_secretsmanager_secret.db_password.id
  secret_string_wo         = ephemeral.random_password.db_password.result
  secret_string_wo_version = 1
}

ephemeral "aws_secretsmanager_secret_version" "db_password" {
  secret_id = aws_secretsmanager_secret_version.db_password.secret_id
}

resource "aws_db_instance" "example" {
  instance_class      = "db.t3.micro"
  allocated_storage   = "5"
  engine              = "postgres"
  username            = "example"
  skip_final_snapshot = true
  password_wo         = ephemeral.aws_secretsmanager_secret_version.db_password.secret_string
  password_wo_version = aws_secretsmanager_secret_version.db_password.secret_string_wo_version
}
```

### Environment Variables

```bash
# Never commit these
export TF_VAR_database_password="secret123"
export AWS_ACCESS_KEY_ID="AKIAIOSFODNN7EXAMPLE"
export AWS_SECRET_ACCESS_KEY="wJalrXUtnFEMI/K7MDENG/bPxRfiCYEXAMPLEKEY"
```

**In .gitignore:**

```text
*.tfvars
.env
secrets/
```

## State File Security

### Encrypt State at Rest

```hcl
# backend.tf
terraform {
  backend "s3" {
    bucket         = "my-terraform-state"
    key            = "prod/terraform.tfstate"
    region         = "us-east-1"
    dynamodb_table = "terraform-locks"
    encrypt        = true  # ✅ Always enable encryption
  }
}
```

### Secure State Bucket

```hcl
resource "aws_s3_bucket" "terraform_state" {
  bucket = "my-terraform-state"
}

# Enable versioning (protect against accidental deletion)
resource "aws_s3_bucket_versioning" "terraform_state" {
  bucket = aws_s3_bucket.terraform_state.id

  versioning_configuration {
    status = "Enabled"
  }
}

# Enable encryption
resource "aws_s3_bucket_server_side_encryption_configuration" "terraform_state" {
  bucket = aws_s3_bucket.terraform_state.id

  rule {
    apply_server_side_encryption_by_default {
      sse_algorithm = "AES256"
    }
  }
}

# Block public access
resource "aws_s3_bucket_public_access_block" "terraform_state" {
  bucket = aws_s3_bucket.terraform_state.id

  block_public_acls       = true
  block_public_policy     = true
  ignore_public_acls      = true
  restrict_public_buckets = true
}
```

### Restrict State Access

```json
{
  "Version": "2012-10-17",
  "Statement": [
    {
      "Effect": "Allow",
      "Principal": {
        "AWS": "arn:aws:iam::123456789012:role/TerraformRole"
      },
      "Action": [
        "s3:ListBucket",
        "s3:GetObject",
        "s3:PutObject"
      ],
      "Resource": [
        "arn:aws:s3:::my-terraform-state",
        "arn:aws:s3:::my-terraform-state/*"
      ]
    }
  ]
}
```

---

## IAM Best Practices

### ✅ DO: Use Least Privilege

```hcl
# Good: Specific permissions only
resource "aws_iam_policy" "app_policy" {
  name = "app-policy"

  policy = jsonencode({
    Version = "2012-10-17"
    Statement = [
      {
        Effect = "Allow"
        Action = [
          "s3:GetObject",
          "s3:PutObject"
        ]
        Resource = "arn:aws:s3:::my-app-bucket/*"
      }
    ]
  })
}
```

### ❌ DON'T: Use Wildcard Permissions

```hcl
# BAD: Overly broad permissions
resource "aws_iam_policy" "bad_policy" {
  policy = jsonencode({
    Statement = [
      {
        Effect   = "Allow"
        Action   = "*"  # ❌ Never use wildcard
        Resource = "*"
      }
    ]
  })
}
```

## Compliance Checklists

### SOC 2 Compliance

- [ ] Encryption at rest for all data stores
- [ ] Encryption in transit (TLS/SSL)
- [ ] IAM policies follow least privilege
- [ ] Logging enabled for all resources
- [ ] MFA required for privileged access
- [ ] Regular security scanning in CI/CD

### HIPAA Compliance

- [ ] PHI encrypted at rest and in transit
- [ ] Access logs enabled
- [ ] Dedicated VPC with private subnets
- [ ] Regular backup and retention policies
- [ ] Audit trail for all infrastructure changes

### PCI-DSS Compliance

- [ ] Network segmentation (separate VPCs)
- [ ] No default passwords
- [ ] Strong encryption algorithms
- [ ] Regular security scanning
- [ ] Access control and monitoring

## Resources

- Use available tools for Azure (`#azureterraformbestpractices`, `#microsoft-learn/*`) and AWS best practices to retrieve up-to-date information on security best practices and patterns.

**Back to:** [Main Skill File](../SKILL.md)
