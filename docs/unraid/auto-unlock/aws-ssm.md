---
title: AWS Systems Manager & Secrets Manager Piece Storage
description: Storing Automatic Disk Unlock pieces in AWS Systems Manager Parameter Store or AWS Secrets Manager.
---

# AWS SSM & Secrets Manager Piece Storage

AWS provides two services for securely storing Automatic Disk Unlock pieces: Systems Manager (SSM) Parameter Store and Secrets Manager. Both services offer secure storage with fine-grained access control through IAM policies.

## Overview

You store your base64-encoded piece in either an SSM Parameter or a Secrets Manager secret. During boot, Automatic Disk Unlock authenticates using IAM access keys and retrieves the piece from AWS.

## Configuration Format

### SSM Parameter Store

```text
aws-ssm://ACCESS_KEY_ID:SECRET_ACCESS_KEY@REGION/PARAMETER_NAME
```

### AWS Secrets Manager

```text
aws-secrets://ACCESS_KEY_ID:SECRET_ACCESS_KEY@REGION/SECRET_NAME
```

- `ACCESS_KEY_ID`: Your IAM access key ID
- `SECRET_ACCESS_KEY`: Your IAM secret access key
- `REGION`: AWS region (e.g., `us-east-1`, `eu-west-1`)
- `PARAMETER_NAME`: Name of the SSM parameter (must start with `/`)
- `SECRET_NAME`: Name of the Secrets Manager secret

## Which Service Should I Use?

### SSM Parameter Store

!!! success "Recommended for Most Users"
    SSM Parameter Store is typically the better choice for Automatic Disk Unlock:

    - **Free Tier**: Standard parameters are always free (up to 10,000 parameters)
    - **Simple**: Designed for configuration data and simple secrets
    - **Sufficient**: Provides adequate security for unlock pieces
    - **Cost**: $0/month for standard parameters

### AWS Secrets Manager

!!! warning "Consider Cost"
    Secrets Manager is more feature-rich but comes with costs:

    - **Cost**: $0.40 per secret per month + $0.05 per 10,000 API calls
    - **Features**: Automatic rotation, cross-region replication, more auditing
    - **Use Case**: Better suited for application secrets requiring rotation

For most home users, **SSM Parameter Store** is the recommended choice.

## Setup Instructions

The easiest way to set up is using CloudFormation templates that automatically create all necessary AWS resources.

### CloudFormation

CloudFormation templates create the parameter/secret, IAM user, and necessary policies automatically.

#### Step 1: Choose Your Template

Select the template for your preferred service:

**SSM Parameter Store**

- **US East (N. Virginia)**: [Launch Stack](https://console.aws.amazon.com/cloudformation/home?region=us-east-1#/stacks/create/review?templateURL=https://cf-templates-unraid-auto-unlock.s3.us-east-1.amazonaws.com/templates/v1.0.0/aws-ssm-template.yaml){ .md-button }
- **US West (Oregon)**: [Launch Stack](https://console.aws.amazon.com/cloudformation/home?region=us-west-2#/stacks/create/review?templateURL=https://cf-templates-unraid-auto-unlock.s3.us-east-1.amazonaws.com/templates/v1.0.0/aws-ssm-template.yaml){ .md-button }
- **EU (Ireland)**: [Launch Stack](https://console.aws.amazon.com/cloudformation/home?region=eu-west-1#/stacks/create/review?templateURL=https://cf-templates-unraid-auto-unlock.s3.us-east-1.amazonaws.com/templates/v1.0.0/aws-ssm-template.yaml){ .md-button }

Or download the template: [aws-ssm-template.yaml](https://raw.githubusercontent.com/dkaser/unraid-auto-unlock/refs/heads/main/templates/aws-ssm-template.yaml)

**Secrets Manager**

- **US East (N. Virginia)**: [Launch Stack](https://console.aws.amazon.com/cloudformation/home?region=us-east-1#/stacks/create/review?templateURL=https://cf-templates-unraid-auto-unlock.s3.us-east-1.amazonaws.com/templates/v1.0.0/aws-secrets-template.yaml){ .md-button }
- **US West (Oregon)**: [Launch Stack](https://console.aws.amazon.com/cloudformation/home?region=us-west-2#/stacks/create/review?templateURL=https://cf-templates-unraid-auto-unlock.s3.us-east-1.amazonaws.com/templates/v1.0.0/aws-secrets-template.yaml){ .md-button }
- **EU (Ireland)**: [Launch Stack](https://console.aws.amazon.com/cloudformation/home?region=eu-west-1#/stacks/create/review?templateURL=https://cf-templates-unraid-auto-unlock.s3.us-east-1.amazonaws.com/templates/v1.0.0/aws-secrets-template.yaml){ .md-button }

Or download the template: [aws-secrets-template.yaml](https://raw.githubusercontent.com/dkaser/unraid-auto-unlock/refs/heads/main/templates/aws-secrets-template.yaml)

!!! note "Template Storage Cost"
    The CloudFormation templates are hosted in a requester-pays S3 bucket. Launching a stack can incur a minimal cost to retrieve the template (less than $0.01).

#### Step 2: Configure Stack Parameters

On the CloudFormation stack creation page, configure the following parameters:

| Parameter | Description | Example |
|-----------|-------------|---------|
| **Stack name** | Name for the CloudFormation stack | `unraid-autounlock-ssm` |
| **ParameterName** / **SecretName** | Name of the parameter/secret<br>*For SSM, must start with `/`* | `/unraid/autounlock/piece1` |
| **ParameterValue** / **SecretValue** | Your base64-encoded unlock piece | |
| **GenerateAccessKey** | Automatically generate access keys<br>*Set to `false` for manual key creation* | `true` |

!!! tip "Stack Naming"
    Use descriptive stack names to identify which share each stack is for, such as `unraid-autounlock-share1-ssm`.

#### Step 3: Review and Create

1. Check the box acknowledging that CloudFormation will create IAM resources.
2. Click **Create Stack** to create the stack

#### Step 4: Retrieve Connection String

1. Wait for the stack status to show **CREATE_COMPLETE** (typically 1-2 minutes)
2. Navigate to the **Outputs** tab
3. Copy the value from the **ConnectionString** output
4. This is your complete location string ready to use in Unraid

**Example output:**

```text
aws-ssm://ACCESS_KEY_ID:SECRET_ACCESS_KEY@us-east-1//unraid/autounlock/piece1
```

!!! note "Security: Access Keys in Outputs"
    The secret access key is visible in CloudFormation outputs. For most home users, this is acceptable since the IAM user has minimal permissions. However, if you have stricter security requirements, consider the following alternative:
    
    1. Set **GenerateAccessKey** to `false` during stack creation
    2. After the stack is created, go to the **Resources** tab
    3. Click on the IAM user resource to open it in the IAM console
    4. Create an access key manually under **Security credentials**
    5. Build the connection string using your manually created keys

#### Step 5: Add to Automatic Disk Unlock Configuration

--8<-- "include/auto-unlock/add-entry.snip"

## Security Considerations

!!! success "Strong Security Model"
    Both SSM and Secrets Manager provide enterprise-grade security:

    - **Encryption in Transit**: All API calls use TLS
    - **IAM Access Control**: Fine-grained permissions per parameter/secret
    - **Regional Isolation**: Data stays in your chosen AWS region

!!! tip "Best Practices"
    - Use a dedicated IAM user with minimal permissions (read-only access to specific parameters/secrets)
    - Use multi-factor authentication (MFA) on your AWS account
    - Consider using SSM SecureString with a custom KMS key for additional control

!!! note "Parameter Type Trade-offs"
    For SSM Parameter Store:
    
    - **String**: Simpler, no KMS permissions needed, suitable for most users
    - **SecureString**: Encrypted at rest with KMS, requires additional IAM permissions for KMS decrypt operations, cannot be created via CloudFormation
    
    Since each piece is only part of the full unlock key and access is controlled by IAM policies, `String` type provides adequate security for most use cases. String is used by the CloudFormation
    template as SecureString is not supported.
