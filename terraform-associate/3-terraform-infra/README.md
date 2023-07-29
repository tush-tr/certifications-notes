- [Authentication and Authorization](#authentication-and-authorization)
  - [Basics](#basics)
  - [Authentication and Authorization in terraform](#authentication-and-authorization-in-terraform)
  - [Access credentials in terraform](#access-credentials-in-terraform)
- [Creating User in AWS](#creating-user-in-aws)
- [Launch VM using terraform](#launch-vm-using-terraform)
  - [Using AWS Provider and authentication](#using-aws-provider-and-authentication)
  - [Add resource block for aws instance](#add-resource-block-for-aws-instance)
  - [Use terraform to create the instance](#use-terraform-to-create-the-instance)
  - [Make changes to the instance](#make-changes-to-the-instance)
- [Providers and Resources](#providers-and-resources)
  - [Providers](#providers)
    - [Provider Plugins(Terraform init)](#provider-pluginsterraform-init)
  - [Resource](#resource)
  - [Issues and Bugs with Providers](#issues-and-bugs-with-providers)
- [Provider Tiers](#provider-tiers)
  - [Provider Maintainers](#provider-maintainers)
  - [Provider Namespaces](#provider-namespaces)
  - [Required Providers Block](#required-providers-block)
- [Creating Github Repository with Terraform](#creating-github-repository-with-terraform)
- [Terraform Destroy](#terraform-destroy)
  - [Destroy All](#destroy-all)
  - [Destroy some with target](#destroy-some-with-target)
  - [Destroying by removing resource blocks](#destroying-by-removing-resource-blocks)
- [Terraform State File](#terraform-state-file)
  - [Example State file](#example-state-file)
- [Desired State and Current State](#desired-state-and-current-state)
- [Challenges with the current state on Computed Values](#challenges-with-the-current-state-on-computed-values)
- [Terraform Provider Versioning](#terraform-provider-versioning)
  - [Provider Architecture](#provider-architecture)
  - [Provider Versioning Overview](#provider-versioning-overview)
  - [Explicitly Setting Provider Version](#explicitly-setting-provider-version)
  - [Arguments for Specifying Provider](#arguments-for-specifying-provider)
  - [Dependency Lock File](#dependency-lock-file)
- [Terraform Refresh](#terraform-refresh)
- [AWS Provider Authentication configuration](#aws-provider-authentication-configuration)
  - [Hardcoding credentials Approach](#hardcoding-credentials-approach)
  - [Shared Credentials File approach](#shared-credentials-file-approach)
  - [Default Configurations](#default-configurations)
  - [AWS CLI](#aws-cli)
# Authentication and Authorization
## Basics
- Authentication is the process of verifying who a user is.
- Authorization is the process of verifying what they have access to.
- Example: Alice is a user in AWS with no access to any service.
```mermaid
flowchart LR;
A[Terraform]--Create new Server-->B[AWS Cloud]--Authenticate first-->A
```

## Authentication and Authorization in terraform
- Terraform needs access credentials with relevant permissions to create and manage environments.
```mermaid
flowchart LR;
A[Terraform]--create a new server-->B[AWS Cloud]--done-->A
A-->D[username & password]
```

## Access credentials in terraform
- Depending on the provider, the type of access credentials would change.

| Provider      | Access Credentials                  |
|---------------|-------------------------------------|
| AWS           | Access keys and Secret keys         |
| GitHub        | Tokens                              |
| Kubernetes    | Kubeconfig file, credentials config |
| Digital Ocean | Tokens                              |

- In terraform there're 1ks of providers available.
- We can check in documentation, what are the things we need to authenticate.

# Creating User in AWS
- IAM > Users > Create User
- Click on user > Generate access key
- Then Use access key & Secret key

# Launch VM using terraform
- EC2 stands for Elastic Compute Cloud.
- It's a name for a virtual server that we launch in AWS.
- Available regions:
  - Cloud providers offers multiple regions in which we can create our resource.
  - You need to decide the region in terraform would create the resource.
- VM Configuration:
  - CPU
  - Memory
  - Storage
  - Operating System

## Using AWS Provider and authentication
- Using access key and secret key defined in the code
  ```hcl
  provider "aws"{
    region = "us-west-2"
    access_key = ""
    secret_key = ""
  }
  ```
- Use environment variables
  - Set these environment variables:
    ```hcl
    export AWS_ACCESS_KEY_ID="<ACCESS_KEY>"
    export AWS_SECRET_ACCESS_KEY="<SECRET_KEY>"
    export AWS_REGION="us-west-2"
    ```
  - Don't pass config in provider block
    ```hcl
    provider "aws" {}
    ```
  - Check more about environment variables: https://registry.terraform.io/providers/hashicorp/aws/latest/docs#aws-configuration-reference

## Add resource block for aws instance
```hcl
provider "aws" {
  region = "eu-north-1"
  # access_key = ""
  # secret_key = ""
}
resource "aws_instance" "myec2" {
    ami = "ami-0989fb15ce71ba39e"
    instance_type = "t3.micro"
}
```

## Use terraform to create the instance
- Terraform init
  - ```terraform init```
- Terraform Plan
  - ```terraform plan```
  - Verify if everything is correct to start creating the resource
  - Not create the resource but show what is to be created
- Terraform Apply
  - ```terraform apply```
  - Create the resource

## Make changes to the instance
- Add tags to the instance now
  ```hcl
    provider "aws" {
      region = "eu-north-1"
      # access_key = ""
      # secret_key = ""
    }
    resource "aws_instance" "myec2" {
        ami = "ami-0989fb15ce71ba39e"
        instance_type = "t3.micro"
        tags = {
          Name = "my-first-vm-using-tf"
        }
    }
  ```
- Now when we run ```terraform plan``` and ```terraform apply```, It will modify our resource.

# Providers and Resources
## Providers
- Terraform supports multiple providers.
- Depending on what type of infrastructure we want to launch, we have to use appropriate providers accordingly.
  - AWS
  - Azure
  - GCP
  - kubernetes
### Provider Plugins(Terraform init)
- A Provider is a plugin that lets Terraform manage an external API.
- When we run  ```terraform init```, plugin required for the provider are automatically downloaded and saved locally to a ```.terraform``` directory.
  - ```.terraform/providers/registry.terraform.io/hashicorp/aws/5.6.2/darwin_arm64/  ```
- To download a specific or multiple providers you only need to define provider block in the tf file: 
  ```hcl
  provider "aws"{}
  ```
## Resource
- Resource block describes one or more infrastructure objects.
  ```hcl
  resource "aws_instance" "myec2"{
    ami = "ami-id"
    instance_type = "t3.micro"
  }
  ```
- Example resource blocks:
  - resource aws_instance
  - resource aws_alb
  - resource iam_user
  - resource digitalocean_droplet
- A resource block declare a resource of a given type ("aws_instance") with a given local name ("myec2")
  ```hcl
  resource <RESOURCE_TYPE> <LOCAL_NAME>{}
  ```
- Resource type and Name together serve as an identifier for a given resource and so must be unique.
  ```hcl
  # EC2 Instance Number 1
  resource "aws_instance" "myec2"{
    ami = "ami-id"
    instance_type = "t3.micro"
  }
  # EC Instance number 2
  resource "aws_instance" "web"{
    ami = "ami-123"
    instance_type = "t3.micro"
  }
  ```
- You can only use the resource that are supported by a specific provider.
- resource type = provider_resource ex: ```aws_instance```
>The core concepts, standard syntax remains similar across all providers.
## Issues and Bugs with Providers
- A Provider that is maintained by Hashicorp doesn't mean it has no bugs
- It can happen that there are inconsistencies from your output and things mentioned in documentation. You can raise issue at Provider page.

# Provider Tiers
## Provider Maintainers
- There're 3 primary type of provider tiers in Terraform.

| Provider Tiers | Description                                                                                  |
|----------------|----------------------------------------------------------------------------------------------|
| Official       | Owned and maintained by Hashicorp.                                                           |
| Partner        | Owned and Maintained by Technology Company that maintains direct partnership with Hashicorp. |
| Community      | Owned and Maintained by Individual Contributors.                                             |


## Provider Namespaces
- Namespaces are used to help identify the organization or publisher responsible for the integration.

| Tier      | Description                                                         |
|-----------|---------------------------------------------------------------------|
| Official  | Hashicorp                                                           |
| Partner   | Third Party organization: mongodb/mongodbatlas                      |
| Community | Maintainer's individual or organization account, eg DeviaVir/gsuite |

## Required Providers Block
- Terraform requires explicit source information for any providers that are not Hashicorp maintained, using a new syntax in the ```required_providers``` nested block inside terraform configuration block.
  ```hcl
  terraform{
      required_providers{
          digitalocean = {
              source = "digitalocean/digitalocean"
          }
      }
  }
  provider "digitalocean"{}
  ```
- If we don't define required_providers block with source of the provider which is not hashicorp maintained, then terraform will give error because by default it will search the provider in the hashicorp namespace.


# Creating Github Repository with Terraform
- Define terraform provider block and run ```terraform init``` to download and configure github provider
  ```hcl
  terraform {
    required_providers {
      github = {
        source  = "integrations/github"
        version = "~> 5.0"
      }
    }
  }

  # Configure the GitHub Provider
  provider "github" {
      token = "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"
  }
  ```
- Add resource block for creating the github repository.
  ```hcl
  terraform {
    required_providers {
      github = {
        source  = "integrations/github"
        version = "~> 5.0"
      }
    }
  }
  # Configure the GitHub Provider
  provider "github" {
      token = "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"
  }
  resource "github_repository" "example" {
    name        = "example-by-terraform"
    description = "My awesome codebase"
    visibility = "public"
  }
  ```

# Terraform Destroy
- If we keep the infrastructure running, we will get charged for it.
- Hence, it is important for us to also know on how we can delete the infrastructure resources created via terraform.
## Destroy All
- ```terraform destroy``` allows us to destroy all the resources that are created within the folder(directory).
```hcl
terraform destroy
```
## Destroy some with target
- ```terraform destroy``` with ```-target``` flag allows us to destroy specific resources.
```hcl
terraform destroy -target aws_instance.myec2
```
- The ```-target``` option can be used to focus Terraform's attention on only a subset of resources.
- Combination of: Resource Type + Local Resource Name

| Resource Type     | Local Resource Name |
|-------------------|---------------------|
| aws_instance      | myec2               |
| github_repository | example             |

## Destroying by removing resource blocks
- If we remove some resource block from terraform scripts that is already created using terraform. Then after ```terraform apply``` terraform will automatically delete the resource if it doesn't find the configuration for the created resource in the tf files.


# Terraform State File
- Terraform stores the state of infrastructure that is being created from the TF files.
- This state allows terraform to map real-world resource to your existing configuration.
- Let's consider when created a EC2 instance, terraform updated state file with instance configuration that is created.
```mermaid
flowchart LR;
A[EC2 Instance]<-->B[State file: instance-id = absc1 instance-type = t3.micro etc.]
```
- Now Let's say we destroyed using ```terraform destroy``` the instance, now terraform will update the state file
```mermaid
flowchart LR;
A[EC2 Instance]<-->B[State file: Empty]
```

## Example State file
<details>
<summary>When Github repo is created via terraform, state file looks like</summary>

```hcl
{
  "version": 4,
  "terraform_version": "1.4.3",
  "serial": 5,
  "lineage": "afbd6782-d651-96f0-8a44-0da261c53885",
  "outputs": {},
  "resources": [
    {
      "mode": "managed",
      "type": "github_repository",
      "name": "example",
      "provider": "provider[\"registry.terraform.io/integrations/github\"]",
      "instances": [
        {
          "schema_version": 1,
          "attributes": {
            "allow_auto_merge": false,
            "allow_merge_commit": true,
            "allow_rebase_merge": true,
            "allow_squash_merge": true,
            "allow_update_branch": false,
            "archive_on_destroy": null,
            "archived": false,
            "auto_init": null,
            "default_branch": "main",
            "delete_branch_on_merge": false,
            "description": "My awesome codebase",
            "etag": "W/\"7dd631d3b17aac9df0d34c381910252f2a346d410ac8a96facdd5dffae96e2c3\"",
            "full_name": "tush-tr/example-by-terraform",
            "git_clone_url": "git://github.com/tush-tr/example-by-terraform.git",
            "gitignore_template": null,
            "has_discussions": false,
            "has_downloads": false,
            "has_issues": false,
            "has_projects": false,
            "has_wiki": false,
            "homepage_url": "",
            "html_url": "https://github.com/tush-tr/example-by-terraform",
            "http_clone_url": "https://github.com/tush-tr/example-by-terraform.git",
            "id": "example-by-terraform",
            "ignore_vulnerability_alerts_during_read": null,
            "is_template": false,
            "license_template": null,
            "merge_commit_message": "PR_TITLE",
            "merge_commit_title": "MERGE_MESSAGE",
            "name": "example-by-terraform",
            "node_id": "R_kgDOJ4DZHg",
            "pages": [],
            "private": false,
            "repo_id": 662755614,
            "security_and_analysis": [
              {
                "advanced_security": [],
                "secret_scanning": [
                  {
                    "status": "disabled"
                  }
                ],
                "secret_scanning_push_protection": [
                  {
                    "status": "disabled"
                  }
                ]
              }
            ],
            "squash_merge_commit_message": "COMMIT_MESSAGES",
            "squash_merge_commit_title": "COMMIT_OR_PR_TITLE",
            "ssh_clone_url": "git@github.com:tush-tr/example-by-terraform.git",
            "svn_url": "https://github.com/tush-tr/example-by-terraform",
            "template": [],
            "topics": null,
            "visibility": "public",
            "vulnerability_alerts": false
          },
          "sensitive_attributes": [],
          "private": "eyJzY2hlbWFfdmVyc2lvbiI6IjEifQ=="
        }
      ]
    }
  ],
  "check_results": null
}

```
</details>

<details>
<summary>Now after deleting the github repo by running terraform destroy, state file looks like</summary>

```hcl
{
  "version": 4,
  "terraform_version": "1.4.3",
  "serial": 7,
  "lineage": "afbd6782-d651-96f0-8a44-0da261c53885",
  "outputs": {},
  "resources": [],
  "check_results": null
}
```
</details>


# Desired State and Current State
- Terraform's primary function is to create, modify, and destroy infrastructure resources to match the desired state described in a Terraform configuration.
- Current State is the actual state of a resource that is currently deployed.
- Terraform tries to ensure that the deployed infrastructure is based on the desired state.
- If there's a difference between the two, terraform plan presents a description of the changes necessary to achieve the desired state.

# Challenges with the current state on Computed Values
- Let's say we've created a EC2 instance using terraform with default security group.
- Now I've manually changed EC2 instance security group from default to custom.
- What will terraform do now?
  - NOT: Terraform will change the security group from custom to default again.
  - It will say no changes, infra is up to date because we've not defined in desired state that security group should be default.
# Terraform Provider Versioning
## Provider Architecture

```mermaid
flowchart LR;
A[droplettf]-->T[Terraform]<-->D[Digital Ocean Provider]<--API Interactions-->I[Digital Ocean New Server]
```

## Provider Versioning Overview
- Provider plugins are released separately from Terraform itself.
- They have different set of version numbers.
- Digital Ocean
  - v1
  - v2
## Explicitly Setting Provider Version
- During ```terraform init```, if version argument is not specified, the most recent provider will be downloaded during initialization.
- For production use, you should constrain the acceptable provider versions via configuration, to ensure  that new versions with breaking changes will not be automatically installed.
  ```hcl
  terraform {
    required_providers {
      aws = {
        source = "hashicorp/aws"
        version = "~>3.0"
      }
    }
  }
  ```
## Arguments for Specifying Provider
- There're multiple ways for specifying the version of a provider.
  
| Version Number Arguments | Description                       |
|--------------------------|-----------------------------------|
| >=1.0                    | Greater than equal to the version |
| <=1.0                    | Less than equal to the version    |
| ~>2.0                    | Any Version in the 2.X range      |
| >=2.10,<=2.30            | Any Version between 2.10 and 2.30 |

## Dependency Lock File
- Terraform dependency lock file(```.terraform.lock.hcl```) allows us to lock to a specific version of the provider.
- If a particular provider already has a selection recorded in the lock file, Terraform will always  re-select that version for installation, even if a newer version has become available.
- You can override that behaviour by adding the ```-upgrade``` option when you run ```terraform init```

# Terraform Refresh
- Terraform can create an infrastructure based on the configuration you specified.
- It can happen that the infrastructure gets modified manually.
- The ```terraform refresh``` command will check the latest state of your infrastructure and update the state file accordingly.
- REMEMBER: You shouldn't typically need to use this command, because Terraform automatically performs the same refreshing actions as a part of creating a plan in both the ```terraform plan``` and ```terraform apply``` commands.
- Then what is usage of ```terraform refresh```?
  - The ```terraform refresh``` command is deprecated in newer version of terraform.
  - The ```-refresh-only``` option for terraform plan and terraform apply was introduced in Terraform v.15.4

# AWS Provider Authentication configuration
## Hardcoding credentials Approach
- At this stage, we have manually hardcoding the access/secret keys within the provider block.
  ```hcl
  provider "aws" {
    region = "eu-north-1"
    access_key = ""
    secret_key = ""
  }
  ```
- It is not optimal from security point of view.
- We want our code to run successfully without  hardcoding the secrets in the provider block.
  ```hcl
  provider "aws" {
    region = "eu-north-1"
  }
  ```

## Shared Credentials File approach
- The AWS Provider can source credentials and other settings from the shared configuration and credentials files.
  ```hcl
  provider "aws" {
    region = "eu-north-1"
    shared_config_files = ["/Users/tf_user/.aws/conf"]
    shared_credentials_files = ["/Users/tf_user/.aws/creds"]
    profile = "customprofile"
  }
  ```
## Default Configurations
- If shared files lines are not added to provider block, by default, Terraform will locate these files at $HOME/.aws/config and $HOME/.aws/credentials on Linux and MacOS.
  - ```%USERPROFILE%\.aws\config``` and ```%USERPROFILE%\.aws\credentials``` on Windows.

## AWS CLI
- AWS CLI allows customers to manage AWS resources directly from CLI.
- When we configure access/secret keys in AWS CLI, the location in which these credentials are stored is the same default location that Terraform searches the credentials from.
