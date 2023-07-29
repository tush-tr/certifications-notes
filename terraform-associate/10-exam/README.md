- [Hashicorp Exams Overview](#hashicorp-exams-overview)
  - [Delta type of question](#delta-type-of-question)
  - [Important rules to be followed](#important-rules-to-be-followed)
  - [Registration Process](#registration-process)
- [Important Pointers for Exam -1](#important-pointers-for-exam--1)
  - [Terraform Providers](#terraform-providers)
  - [Terraform Init](#terraform-init)
  - [Terraform plan](#terraform-plan)
  - [Terraform apply](#terraform-apply)
  - [Terraform refresh](#terraform-refresh)
  - [Terraform destroy](#terraform-destroy)
  - [Terraform format](#terraform-format)
  - [Terraform validate](#terraform-validate)
  - [Terraform provisioners](#terraform-provisioners)
- [Important Pointers for exam - 2](#important-pointers-for-exam---2)
  - [Overview of debugging in terraform](#overview-of-debugging-in-terraform)
  - [Terraform Import](#terraform-import)
  - [Local Values](#local-values)
  - [Data Types](#data-types)
  - [Terraform Workspaces](#terraform-workspaces)
  - [Terraform Modules](#terraform-modules)
    - [ROOT and CHILD](#root-and-child)
    - [Module - Accessing Output Values](#module---accessing-output-values)
  - [Suppressing values in CLI output](#suppressing-values-in-cli-output)
  - [Module versions](#module-versions)
  - [Terraform registry](#terraform-registry)
  - [Private registry for module sources](#private-registry-for-module-sources)
- [Important pointers for the exam - 3](#important-pointers-for-the-exam---3)
  - [Terraform Functions](#terraform-functions)
  - [Count and Count Index](#count-and-count-index)
  - [Find the issue - Use-case](#find-the-issue---use-case)
  - [Terraform Lock](#terraform-lock)
  - [Use Case - Resources Deleted out of terraform](#use-case---resources-deleted-out-of-terraform)
  - [Resource Block](#resource-block)
  - [Sentinel](#sentinel)
  - [Sensitive Data in State file](#sensitive-data-in-state-file)
  - [Dealing with credentials in Config](#dealing-with-credentials-in-config)
  - [Remote backend for terraform cloud](#remote-backend-for-terraform-cloud)
  - [Imp Pointers](#imp-pointers)
- [Important pointers for the exam - 4](#important-pointers-for-the-exam---4)
  - [Terraform graph](#terraform-graph)
  - [Splat Expressions](#splat-expressions)
  - [Terraform terminologies](#terraform-terminologies)
  - [Provider Configuration](#provider-configuration)
  - [Terraform output](#terraform-output)
  - [Terraform Unlock](#terraform-unlock)
  - [Other Imp Pointers](#other-imp-pointers)
- [Important Pointers for exam - 5](#important-pointers-for-exam---5)
  - [Terraform Enterprise \& Terraform Cloud](#terraform-enterprise--terraform-cloud)
  - [Vairables with undefined values](#vairables-with-undefined-values)
  - [Environment Variables](#environment-variables)
  - [Structural Data Types](#structural-data-types)
  - [Backend Configuration](#backend-configuration)
    - [Backend Configuration type 1](#backend-configuration-type-1)
    - [Backend configuration type 2](#backend-configuration-type-2)
  - [Terraform Taint](#terraform-taint)
  - [Local Provisioner](#local-provisioner)
  - [Remote Provisioner](#remote-provisioner)
  - [Provisioner Failure behaviour](#provisioner-failure-behaviour)
  - [Provisioner types: Destroy time/Creation time](#provisioner-types-destroy-timecreation-time)
    - [Creation Time Provisioner](#creation-time-provisioner)
    - [Destroy Time provisioners](#destroy-time-provisioners)
  - [Variable definition precedence](#variable-definition-precedence)
  - [Versioning arguments](#versioning-arguments)
  - [Terraform plan destroy](#terraform-plan-destroy)
# Hashicorp Exams Overview
- Overview of the basic exam related information

| Assessment Type | Description      |
|-----------------|------------------|
| Type of Exams   | Multiple Choice  |
| Format          | Online Proctored |
| Duration        | 1 hour           |
| Questions       | 57               |
| Price           | 70 USD + taxes   |
| Language        | English          |
| Expiration      | 2 years          |

- Multiple Choice
  - True or false
  - Multiple choice
  - Fill in the blank

## Delta type of question
- Example 1
  - Demo software stores information in which type of backend?
## Important rules to be followed
- You're alone in the room.
- Your desk and work area are clear
- You're connected to a power source
- No Phones or headphones
- No Dual monitors
- No Leaving your seat
- No talking
- Webcam, speakers, and microphone must remain on throughout the test.
- The proctor must be able to see you for the duration of the rest.

## Registration Process
- The high-level steps for registering for the exams are as follows:
  - Login to hashicorp certification page
  - Register for exams
  - Check system requirements
  - Download PSI software
  - Best of luck 

# Important Pointers for Exam -1
## Terraform Providers
- A provider is responsible for understanding API interactions and exposing resources.
- Most of the available providers correspond to one cloud or on-premises infrastructure platform, and offer resource types that correspond to each of the features of that platform.
- You can explicitly set a specific version of the provider within the provider block.
- To upgrade to the latest acceptable version of each provider, run `terraform init -upgrade`

- We can have multiple provider instances with the help of alias
```hcl
provider "aws"{
    region = "us-east-1"
}
provider "aws"{
    alias = "west"
    region = "us-west-2"
}
```
- The provider block without alias set is known as the default provider configuration. When an alias is set, it creates an additional provider configuration.

## Terraform Init
- The terraform init command is used to initialize a working directory containing terraform configuration files.
- During init, the configuration is searched for module blocks, and the source code for referenced modules is retrieved from the locations given in their source arguments.
- Terraform must initialize the provider before it can be used.
- Initialization downloads and installs the provider's plugin so that it can later be executed.
- It will not create any sample files like example.tf.

## Terraform plan
- The terraform plan command is used to create an execution plan.
- It will not modify things in infrastructure.
- Terraform performs a refresh, unless explicitly disabled, and then determines what actions are necessary to achieve the desired state specified in the configuration files.
- This command is convenient way to check whether the execution plan for a set of changes matches your expectations without making any changes to real resources or to the state.

## Terraform apply
- The terraform apply command is used to apply the changes required to reach the desired state of the configuration.
- Terraform apply will also write data to the `terraform.tfstate` file.
- Once apply is completed, resource are immediately available.

## Terraform refresh
- The terraform refresh command is used to reconcile the state terraform knows about (via its state file) with the real world infrastructure.
- This doesn't modify infrastructure but does modify the state file.

## Terraform destroy
- The terraform destroy command is used to destroy the terraform managed infrastructure.
- Terraform destroy command is not the only command through which infrastructure can be destroyed.

## Terraform format
- `terraform fmt` command is used to rewrite terraform configuration files to a canonical format and style.
- For use-case, where all configuration written by team members need to have a proper style of code, terraform fmt can be used.
## Terraform validate
- The `terraform validate` command validates the configuration files in the directory.
- Validate runs checks that verify whether a configuration is syntactically valid and thus primarily useful for general verification of reusable modules, including the correctness of attribute names and value types.
- It is safe to run this command automatically, for example, as a post-save check in a text editor or as a test step for a reusable module in a CI system. It can run before terraform plan.
- Validation requires an initialized working directory with any referenced plugins and modules installed.
## Terraform provisioners
- Provisioners can be used to model specific actions on the local machine or on a remote machine in order to prepare servers or other infrastructure objects for service.
- Provisioners should only be used as a last resort. For most common situations, there are better alternatives.
  - Provisioners are inside the resource block.
  - local and remote provisioner.

# Important Pointers for exam - 2
## Overview of debugging in terraform
- Terraform has detailed logs that can be enabled by setting the `TF_LOG` environment variable to any value.
- You can set `TF_LOG` to one of the log levels `TRACE`, `DEBUG`, `INFO`, `WARN` or `ERROR` to change the verbosity of the logs.
- Example:
  - `TF_LOG=TRACE`
- To persist logged output, you can set `TF_LOG_PATH`

## Terraform Import
- Terraform is able to import existing infrastructure.
- This allows you take resource that you've created by some other means and bring it under terraform management.
- the current implementation of terraform import can only import resources into the state. It doesn't generate configuration.
- Because of this, prior to running terraform import, it is necessary to write a resource configuration block manually for the resource, to which the imported object will be mapped.
- ```terraform import aws_instance.myec2 instance_id```

## Local Values
- A local value assigns a name to an expression, allowing it to be used multiple times within a module without repeating it.
- The expression of a local value can refer to other locals, but as usual reference cycles are not allowed. That is, a local can't refer to itself or to a variable that refers (directly or indirectly) back to it.
- It's recommended to group together logically-related local values into a single block, particularly if they depend on each other.

## Data Types
| Type Keywords | Description                                                                             |
|---------------|-----------------------------------------------------------------------------------------|
| string        | sequence of unicode characters representing some text, like "hello"                     |
| list          | sequencial list of values identified by their position. Starts with 0 ["mumbai",delhi"] |
| map           | a group of values identified by named labels, like {name = "Mabel", age = 52}           |
| number        | Example: 200                                                                            |

## Terraform Workspaces
- Terraform allows us to have multiple workspaces, with each of the workspaces, we can have a different set of environment variables associated.
- Workspaces allow multiple state files of a single configuration.

## Terraform Modules
- We can centralize the terraform resources and can call out from TF Files whenever required.

### ROOT and CHILD
- Every terraform configuration has at least one module, known as it's root module, which consists of the resource defined in the `.tf` files in the main working directory.
- A module can call other modules, which lets you include the child module's resources into the configuration in a concise way.
- A module that includes a module block like this is the calling module of the child module.

```hcl
module "servers"{
    source = "./app-cluster"
    servers = 5
}
```

### Module - Accessing Output Values
- The resources defined in a module are encapsulated, so the calling module can't access their attributes directly.
- However, the child module can declare output values to selectively export values to be accessed by the calling module.
```hcl
output "instance_ip_addr"{
    value = aws_instance.server.private_ip
}
```

## Suppressing values in CLI output
- An output can be marked as containing sensitive material using the option `sensitive` argument
```hcl
output "db_password"{
    value = aws_db_instance.db.password
    sensitive = true
}
```
- Setting an output value in the root module as sensitive prevents terraform from showing its value in the list of outputs at the end of terraform apply.
- Sensitive output values are still recorded in the state, and so will be visible to anyone who is able to access the state data.

## Module versions
- It is recommended to explicitly constaint the acceptable version numbers for each external module to avoid unexpected or unwanted changes.
- Version constraints are supported only for modules installed from a module registry, such as the terraform registry or terraform cloud's private module registry.

```hcl
module "consul"{
    source = "hashicorp/consul/aws"
    version = "0.0.5"
    servers = 5
}
```

## Terraform registry
- Terraform Registry is integrated directly into terraform.
- the syntax for referencing a registry module is 
  - `<NAMESPACE>/<NAME>/<PROVIDER>`
  
## Private registry for module sources
- You can also use modules from private registry, like the one provided by terraform cloud.
- Private registry modules have source strings of the following form:
  - `<HOSTNAME>/<NAMESPACE>/<NAME>/<PROVIDER>`
- This is the same format for the public registry, but with an added hostname prefix.
- While fetching a module, having a version is required.

# Important pointers for the exam - 3
## Terraform Functions
- The terraform language includes a number of built-in functions that you can use to transform and combine values.
- The terraform language doesn't support user-defined functions, and so only the functions built into the language are available for use.
- Be aware of basic functions like element, lookup.
## Count and Count Index
- The count parameter on resources can simplify configurations and let you scale resources by simply incrementing a number.
- In resource blocks where the count is set, an additional count object (count.index) is available in expressions, so that you can modify the configuration of each instance.
```hcl
resource "aws_iam_user" "lb"{
    name = "LoadBalancer.${count.index}"
    count = 5
    path = "/system/"
}
```

## Find the issue - Use-case
- You can expect use-case with terraform code, and you have to find what should be removed as part of Terraform best practice.
```hcl
terraform {
    backend "s3"{
        bucket = "mybucket"
        key = "path/to/my/key"
        region = "eu-north-1"
        access_key = 1234 #NOT
        secret_key = 1223284 #NOT
    }
}
```

## Terraform Lock
- If supported by your backend, terraform will lock your state for all operations that could write state.
- Terraform has a force-unlock command to manually unlock the state if unlocking failed.

## Use Case - Resources Deleted out of terraform
- You've created an EC2 instance. Someone has modified the EC2 instance manually. What will happen if you do terraform plan yet again?
  - 1. Someone has changed EC2 instance type from `t2.micro` to `t2.large`?
  - 2. Someone has terminated the EC2 instance.
- Answer 1: Terraform's current state will have t2.large, and the desired state is t2.micro. It will try to change back instance type to t2.micro.
- Answer 2: Terraform will create a new EC2 instance.

## Resource Block
- Each resource block describes one or more infrastructure objects, such as virtual networks, compute instances, or higher-level components such as DNS records.
- A resource block declares a resource of a given type "aws_instance" with a given local name "web"
## Sentinel
- Sentinel is an embedded policy-as-code framework integrated with the hashicorp enterprise products.
- Can be used for various use-cases like:
  - Verify if EC2 instance has tags
  - Verify if the S3 bucket has encryption enabled.

## Sensitive Data in State file
- If you manage any sensitive data with terraform (like database passwords, user passwords, or private keys), treat the state itself as sensitive data.
- Approaches in such a scenario:
  - Terraform cloud always encrypts the state at rest and protects it with TLS in transit. Terraform cloud also knows the identity of the user requesting state and maintains a history of state changes.
  - The `S3` backend supports encryption at rest when the encrypt option is enabled.
## Dealing with credentials in Config
- Hard coding credentials into any terraform configuration are not recommended and risks the secret leakage should this file ever be committed to a public version control system.
- You can store the credentials outside of terraform configuration.
- Storing credentials as part of environment variables is also a much better approach than hard coding it in the system.

## Remote backend for terraform cloud
- The remote backend stores the terraform state and may be used to run operations in terraform cloud.
- When using full remote operations like terraform plan or apply can be executed in terraform cloud's run environment, with log output streaming to the local terminal.
## Imp Pointers
- Terraform doesn't require go as a prerequisite.
- It works well in windows, linux and mac.
- Windows server is not mandatory.

# Important pointers for the exam - 4
## Terraform graph
- The `terraform graph` command is used to generate a visual representation of either a configuration or execution plan.
- The output of terraform graph is in the `DOT` format, which can easily be converted to an image
<img src="../4-tf-config/graph/graph.svg">

## Splat Expressions
- Splat expression allows us to get a list of all attributes.
```hcl
resource "aws_iam_user" "lb"{
    name = "iamuser.${count.index}"
    count = 3
}
output "arns"{
    value = aws_iam_user.lb[*].arn
}
```

## Terraform terminologies
```hcl
resource "aws_instance" "example"{
    ami = "abdsu"
}
```
- `aws_instance` : resource type
- `example` : local name for resource
- `ami` : argument name
- `abdsu` : argument value


## Provider Configuration
- Provider configuration block is not mandatory for all terraform configuration.
- like in local provider no need for config provider.

## Terraform output
- The `terraform output` command is used to extract the value of an output variable from the state file.
  - `terraform output iam_names`
  
## Terraform Unlock
- If supported by your backend, terraform will lock your state for all operations that could write state.
- Not all backends supports locking functionality.
- Terraform has a force-unlock command to manually unlock the state if unlocking failed.
- `terraform force-unlock LOCK_ID[DIR]`

## Other Imp Pointers
- 3 Benefits of IAC tools:
  - Automation
  - Versioning
  - Reusability
- Various IAC tools available in the market
  - Terraform
  - Cloud Formation
  - Azure Resource Manager
  - Google Cloud Deployment Manager
  - Pulumi

- Sentinel is a proactive service.
- Terraform refresh doesn't modify the infrastructure but it modifies the state file.
- Slice function is not part of the string function. Others like join, split, chomp are part of it.
- It is not mandatory to include the module version argument while pulling the code from terraform registry.
- Overuse of dynamic blocks can make configuration hard to read and maintain.
- Terraform apply can change, destroy and provision resources but can't import any resource.

# Important Pointers for exam - 5
## Terraform Enterprise & Terraform Cloud
- Terraform enterprise provides several added advantages compared to terraform cloud
  - Single Sign on
  - Auditing
  - Private Data center networking
  - Clustering
- Team and Governance features are not available for terraform cloud Free(Paid).

## Vairables with undefined values
- If you have variables with undefined values, it will not directly result in an error.
- Terraform will ask you to supply the value associated with them.
- Example Code:
  - `variable custom_var{}`

## Environment Variables
- Envrionment variables can be used to set variables.
- The environment variables must be in format `TF_VAR_name`

## Structural Data Types
- A structural type allows multiple values of several distint types to be grouped together as a single value.
- List contains multiple values of same type while object can contain multiple values of different type.

## Backend Configuration
- Backends are configured directly in terraform files in terraform section.
- After configuring a backend, it has to be initialized.
### Backend Configuration type 1
- First Time Configuration
- When configuring a backend for the first time(moving from no defined backend to explicitly configuring one), Terraform will give us the option to migrate our state to new backend.
- This lets us adopt backend without losing any existing state.
### Backend configuration type 2
- Partial Time Configuration
- You don't need to specify every required argument in the backend configuration.
- Omitting certain arguments may be desirable to avoid storing secrets, such as access keys, within the main configuration.
- With a partial Configuration, the remaining configuration arguments must be provided as part of the initialization process.
- `terraform init -backend-config="address=demo.consul.io" -backend-config="path="sample/tfstate"`

## Terraform Taint
- The `terraform taint` command manually marks a terraform managed resource as tainted, forcing it to  be destroyed and recreated on the next apply.
- Once a resource is marked as tainted, the next plan will show that the resource will be destroyed and recreated and the next apply will implement this change.
## Local Provisioner
- The `local-exec` provisioner invokes a local executable after a resource is created. This invokes a process on the machine running terraform, not on the resource.
## Remote Provisioner
- The `remote-exec` provisioner invokes a script on a remote resource after it's created.
- The `remote-exec` provisioner supports both ssh and winrm type connections.

## Provisioner Failure behaviour
- By default, provisioners that fail will also cause the terraform apply itself to fail.
- The `on_failure` setting can be used to change this. The allowed values are:
  - `continue`
  - `fail`

## Provisioner types: Destroy time/Creation time

### Creation Time Provisioner
- Creation time provisioners are only run during creation not during updating or any other lifecycle.
- If a creation-time provisioner fails, the resource is marked as tained.

### Destroy Time provisioners
- Destroy provisioners are run before the resource is destroyed.
- If "when=destroy" is specified, the provisioner will run when the resource it is defined within is destroyed.
  ```hcl
  resource "aws_instance" "web"{
    provisioner "local-exec"{
        when = destroy
        command = "echo 'Destroy time provisioner'"
    }
  }
  ```

## Variable definition precedence
- Terraform loads variables in the following order, with later sources taking precedence over earlier ones:
  - Environment variables
  - `terraform.tfvars` file, if present
  - `terraform.tfvars.json` file , if present
  - Any `*.auto.tfvars` or `*.auto.tfvars.json` files, processed in lexical order of their filenames.
  - Any `-var` and `-var-file` options on the command line, in the order they are provided.

## Versioning arguments
| Version Number Arguments | Description                       |
|--------------------------|-----------------------------------|
| >=1.0                    | Greater than equal to the version |
| <=1.0                    | Less than equal to the version    |
| ~>2.0                    | Any Version in the 2.X range      |
| >=2.10,<=2.30            | Any Version between 2.10 and 2.30 |

## Terraform plan destroy
- The behaviour of any terraform destroy command can be previewed at any time with an equivalent `terraform plan -destroy` command.

