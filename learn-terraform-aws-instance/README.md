# Build intra

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


# Resource Dependencies

## Implicit dependencies
`instance = aws_instance.example.id` refs to `resource "aws_instance" "example" {...}`
- it also indicates the order of creation.

## Explicit dependencies
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