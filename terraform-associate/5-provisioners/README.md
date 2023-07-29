- [Understanding provisioners](#understanding-provisioners)
  - [Using a provisioner remote exec on ec2 instance](#using-a-provisioner-remote-exec-on-ec2-instance)
- [Types of provisioners](#types-of-provisioners)
  - [Local Exec Provisioners](#local-exec-provisioners)
  - [Remote exec provisioner](#remote-exec-provisioner)
- [Provisioner Types(Creation time provisioner, destroy time provisioner)](#provisioner-typescreation-time-provisioner-destroy-time-provisioner)
  - [Creation Time Provisioner](#creation-time-provisioner)
  - [Destroy Time provisioners](#destroy-time-provisioners)
- [Failure behaviour of Provisioners](#failure-behaviour-of-provisioners)
- [Null Resource](#null-resource)
  - [Example 1](#example-1)
  - [Example 2](#example-2)
  - [Null resource schema](#null-resource-schema)

# Understanding provisioners
- Till now we have been working only on creation and destruction of infrastructure scenarios.
- For example
  - We created a web-server EC2 instance with terraform
  - Problem: It is only EC2 instance, it doesn't have any software installed.
  - How to solve this problem, if we want to install the softwares also on the instance after it's creation with terraform only.
- Provisioners are used to execute on a local or remote machine as part of resource creation or destruction.
- For example:
  - On creation of web server, execute a script which installs Nginx web-server.

```mermaid
flowchart LR;
A[Create EC2]-->B[Install nginx]
```

## Using a provisioner remote exec on ec2 instance
```hcl
resource "aws_instance" "myec2" {
    ami = "ami-0989fb15ce71ba39e"
    instance_type = "t3.micro"
    key_name = aws_key_pair.deployer.key_name
    provisioner "remote-exec" {
        inline = [ 
            "sudo apt install -y nginx",
            "sudo systemctl start nginx"
         ]
      
    }
    connection {
      type = "ssh"
      host = self.public_ip
      user = "ubuntu"
      private_key = file("terraform")
    }
}
```

# Types of provisioners
## Local Exec Provisioners
- `local-exec` provisioners allow us to invoke local executable after resource is created.
- One of the most used approach of `local-exec` is to run ansible playbooks on the creater server after the resource is created.
- For example:
  ```hcl
    resource "aws_instance" "myec2" {
      # ....
      provisioner "local-exec" {
          command = "echo ${aws_instance.web.private_ip} >> private_ips.txt"
        
      }
  }
  ```

- Many times, if we want ansible playbook should automatically run so we can use local provisioners.

## Remote exec provisioner
- `remote-exec` provisioners allow to invoke scripts directly on the remote server.
  ```hcl
    resource "aws_instance" "myec2" {
      # ....
      provisioner "remote-exec" {
        #.......
        
      }
  }
  ```

# Provisioner Types(Creation time provisioner, destroy time provisioner)
## Creation Time Provisioner
- Creation time provisioners are only run during creation not during updating or any other lifecycle.
- If a creation-time provisioner fails, the resource is marked as tained.

## Destroy Time provisioners
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

# Failure behaviour of Provisioners
- By default , provisioners that fail will also cause the terraform apply itself to fail.
- The `on_failure` setting can be used to change this. This allowed values are:
  - continue
    - Ignore the error and continue with creation and destruction
  - fail
    - Raise an error and stop applying (the default behaviour). If this is a creation provisioner, taint the resource.
```hcl
provisioner "remote-exec" {
        inline = [ 
            "sudo apt install -y nginx",
         ]
      on_failure = continue
    }
```

# Null Resource
- The `null_resource` implements the standard resource lifecycle but takes no further action.
```mermaid
flowchart LR;
A[FTP Server]--fetch credentials-->B[EC2]
B--Upload Contents-->S[S3 Bucket]
```
- Here we've 2 dependencies:
  - FTP Server
  - S3 Bucket
- Create EC2 only if both dependencies working.
## Example 1
```hcl
provider "aws" {
  region = "eu-north-1"

}
resource "aws_eip" "lb" {
  vpc        = true
  depends_on = [null_resource.health_check]
}
resource "null_resource" "health_check" {
  provisioner "local-exec" {
    command = "curl https://google.com"
  }
}
```
## Example 2
```hcl
provider "aws" {
  region = "eu-north-1"

}
resource "aws_eip" "lb" {
  vpc        = true
  count = 1
}
resource "null_resource" "ip_check" {
    triggers = {
        latest_ips = join(",",aws_eip.lb[*].public_ip)
    }
  provisioner "local-exec" {
    command = "echo Latest IPs are ${null_resource.ip_check.triggers.latest_ips} > sample.txt"
  }
}
```

## Null resource schema
- `triggers`
  - Map of string
  - A map of arbitary string that, when changed, will force null resource to be replaced, re-running any associated provisioners.
- `id`
  - String
  - This is a set to a random value at create time.