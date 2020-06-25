# Get Started - AWS
[Official doc](https://learn.hashicorp.com/terraform/getting-started/intro)

## Build intra

Before executing, configured the local aws profile `personal` as 

```
ap-mel-mac0261:learn-terraform-aws-instance kaiqi.yang$ aws configure --profile personal
AWS Access Key ID [None]: A******************
AWS Secret Access Key [None]: LJ**************************
Default region name [None]: ap-southeast-2
Default output format [None]: 
```

- To init the dir 
  - `terraform init`
- To format the doc
  - `terraform fmt`
- To validate the doc
  - `terraform validate`
- To run
  - `terraform apply`
- To check the state
  - `terraform show`


## Resource Dependencies

### Implicit dependencies
`instance = aws_instance.example.id` refs to `resource "aws_instance" "example" {...}`
- it also indicates the order of creation.

### Explicit dependencies
- `depends_on`
```
# New resource for the S3 bucket our application will use.
resource "aws_s3_bucket" "example" {
  # NOTE: S3 bucket names must be unique across _all_ AWS accounts, so
  # this name must be changed before applying this example to avoid naming
  # conflicts.
  bucket = "terraform-getting-started-guide"
  acl    = "private"
}

# Change the aws_instance we declared earlier to now include "depends_on"
resource "aws_instance" "example" {
  ami           = "ami-2757f631"
  instance_type = "t2.micro"

  # Tells Terraform that this EC2 instance must be created only after the
  # S3 bucket has been created.
  depends_on = [aws_s3_bucket.example]
}
```

### Provisioner

- To create a new key
  - `ssh-keygen -t rsa -f ~/.ssh/terraform`
- Give it the right permission
  - `chmod 400 ~/.ssh/terraform`
- remote-exec
  - Once that connection is successful, the `remote-exec `provisioner will run on the remote host to install, update, and start nginx in this example.
  ```
    provider "aws" {
        profile = "default"
        region  = "us-west-2"
    }

    resource "aws_key_pair" "example" {
        key_name   = "examplekey"
        public_key = file("~/.ssh/terraform.pub")
    }

    resource "aws_instance" "example" {
        key_name      = aws_key_pair.example.key_name
        ami           = "ami-04590e7389a6e577c"
        instance_type = "t2.micro"

        connection {
            type        = "ssh"
            user        = "ec2-user"
            private_key = file("~/.ssh/terraform")
            host        = self.public_ip
        }

        provisioner "remote-exec" {
            inline = [
            "sudo amazon-linux-extras enable nginx1.12",
            "sudo yum -y install nginx",
            "sudo systemctl start nginx"
            ]
        }
    }

  ```
  - Failed Provisioners and Tainted Resources
    - If a resource successfully creates but fails during provisioning, Terraform will error and mark the resource as "tainted". 
    - When you generate your next execution plan, Terraform will not attempt to restart provisioning on the same resource because it isn't guaranteed to be safe. Instead, Terraform will remove any tainted resources and create new resources, attempting to provision them again after creation.

  - Manually Tainting Resources
    - `terraform taint resource.id`
      - The correct resource and ID to taint this resource would be `terraform taint aws_instance.example`.
  - Destroy provisioners
    - If you need to execute something before the instance is shut down.

### Variables
- Using Variables in a Configuration
  ```
  provider "aws" {
    region = var.region
  }
  ```
  - Variable file can be named with anything, since Terraform loads all files in the directory ending in `.tf`.
- ### Assigning variables
  - Command-line flags
    - `terraform apply -var 'region=us-east-1'`
  - From a file
    - With name `terraform.tfvars` or `*.auto.tfvars`
    - Or specify with `-var-file`
      - `$ terraform apply -var-file="secret.tfvars" -var-file="production.tfvars"`
  - From environment variables
    - in the form of `TF_VAR_<name>`
  - Variable defaults
    - If no value is assigned to a variable via any of these methods and the variable has a `default` key in its declaration, that value will be used for the variable.
- ### More types
  - Lists
    ```
    # Declare implicitly by using brackets []
    variable "cidrs" { default = [] }
    ```
  - Maps
    ```
    variable "amis" {
    type = "map"
        default = {
            "us-east-1" = "ami-b374d5a5"
            "us-west-2" = "ami-4b32be2b"
        }
    }
    ```
    - To use it
      ```
      resource "aws_instance" "example" {
        ami           = var.amis[var.region]
        instance_type = "t2.micro"
      }
      ```

### Output variable
This data is outputted when `apply` is called, and can be queried using the `terraform output` command.
```
$ terraform apply
...

Apply complete! Resources: 0 added, 0 changed, 0 destroyed.

Outputs:

  ip = 50.17.232.209
```
`terraform output`
```
$ terraform output ip
50.17.232.209
```
