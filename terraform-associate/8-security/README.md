- [Terraform Provider Use Case](#terraform-provider-use-case)
  - [Single Provider multiple configuration](#single-provider-multiple-configuration)
  - [Alias Variable](#alias-variable)
- [Multiple AWS Profiles with Providers](#multiple-aws-profiles-with-providers)
- [Terraform with STS](#terraform-with-sts)
- [Sensitive Parameter](#sensitive-parameter)
- [Hashicorp Vault Overview](#hashicorp-vault-overview)
- [Terraform and Vault integration](#terraform-and-vault-integration)
- [Dependency Lock file](#dependency-lock-file)
# Terraform Provider Use Case
## Single Provider multiple configuration
- Till now, we've been hardcoding the aws-region parameter within the providers.tf
- This means that resources would be created in the region specified in the providers.tf file.
- What if we want resources to be created in multiple regions.
## Alias Variable
```hcl
provider "aws" {
    region = "eu-north-1"
}
provider "aws" {
    alias = "mumbai"
    region = "ap-south-1"
}
resource "aws_eip" "myeip"{
    vpc = "true"
}
resource "aws_eip" "myeip01"{
    vpc = "true"
    provider = aws.mumbai
}
```

# Multiple AWS Profiles with Providers

- Create a credentials file: `credentials`
```env
[default]
aws_access_key_id = <ACCESSKEY>
aws_secret_access_key = <SECRETKEY>

[account02]
aws_access_key_id = <ACCESSKEY>
aws_secret_access_key = <SECRETKEY>
```
- And now fetch profiles in provider block
```hcl
provider "aws" {
    region = "eu-north-1"
}
provider "aws" {
    alias = "mumbai"
    region = "ap-south-1"
    profile = "account02"
}
```

# Terraform with STS
- Let's say we've multiple aws accounts.
- Instead of using each account secret/access key we can use single set of credentials using identity.
```sh
provider "aws" {
    region = "eu-north-1"
    assume_role{
        role_arn = "<ROLE_ARN>"
        session_name = "my-session"
    } 
}
```

# Sensitive Parameter
- With organization managing their entire infrastructure in terraform, it is likely that we will see some sensitive information embedded in the code.
- When working with a field that contains information likely to be considered sensitive, it is best to set the Sensitive property on its schema to true.

```hcl
output "db_password"{
    value = aws_db_instance.db.password
    description = "The password for logging in to the database"
    sensitive = true
}
```

- Setting the `sensitive` to `true` will prevent the field's values from showing up in CLI output and in Terraform Cloud.
- It will not encrypt or obscure the value in the state, however,
- It will not show the sensitive information in the outputs.

# Hashicorp Vault Overview
- Hashicorp vault allows organizations to securely store secrets like tokens, passwords, certificates along with access management for protecting secrets.
- One of the common challenges nowadays in an organization is "Secret Management"
- Secrets can include, database passwords, AWS access/secret keys, API tokens, encryption keys and others.
- Once vault is integrated with multiple backends, your life will become much easier and you can focus more on the right work.
- Major aspects related to Access Management can be taken over by vault.

# Terraform and Vault integration
- There's a vault provider also in terraform.
- The vault provider allows terraform to read from, write to, and configure Hashicorp vault.
# Dependency Lock file
- Dependency lock file - `.terraform.lock.hcl`
- Provider plugins and terraform are managed independently and have different release cycle.
- Let's say we have
  - Terraform v1.2
  - AWS Plugin v1
- Now aws provider plugin is release as v2
- So terraform version and provider version are independent.
- The AWS code written in terraform is working perfectly well with AWS Plugin v1
- It can happen that same code might have some issues with newer AWS Plugins.
- By Default latest version of  provider plugin will be downloaded on `terraform init`
- Version constaints within the configuration itself determine which versions of dependencies are potentially compatible.
```hcl
terraform{
    required_provider{
        aws = {
            source = "hashicorp/aws"
            version = "~> 4.0"
        }
    }
}
```
- After selecting a specific version of each dependendy terraform remembers the decisions it made in the dependency lock file so that it can(by default) make the same decision again in future.
- What happens if you update the TF file with version that doesn't match the terraform.lock.hcl?(ERROR)
- If there's a requirement to use newer or downgrade a provider, can override that behaviour by adding the -upgrade option when we run `terraform init`, in which case terraform will desregard the existing selection.
  - `terraform init -upgrade`
- When installing a particular provider for the first time, terraform will pre-populate the hashes value with any checksums that are covered by the provider developer's cryptographic signature, which usually covers all of the available packages for the provider version across all supported platform.
- At present, the dependency lock file tracks only provider dependencies.
- Terraform doesn't remember version selections for remote modules, and so terraform will always select the newest available module version that meets the specified version constaints.


