# AUTOMATE INFRASTTUCTURE WITH IAC USING TERRAFORM PART 1

* Created a terraform IAM user with programmatic access to my AWS account and granted it AdministratorAccess permission
* Saved the Secret access key and access key ID in my notepad as it will be unrecoverable and needed for AWS CLI configuration
* Configured programmatic access to my account using the terraform IAM user through AWS CLI with the `aws configure` command
* Created an S3 bucket to store my terraform state file. Named it kazeem-dev-terraform-bucket as it has to be a unique bucket name
* 

## Creating VPC | SUBNETS | SECURITY GROUPS
* Created a folder called PBL and created a main.tf file in the folder
* Added AWS as a provider, and a resource to create a VPC in the main.tf file as shown below
* To be noted that I used us-east-1 as my region
    ```
    provider "aws" {
      region = "us-east-1"
    }

    # Create VPC
    resource "aws_vpc" "main" {
      cidr_block                     = "172.16.0.0/16"
      enable_dns_support             = "true"
      enable_dns_hostnames           = "true"
      enable_classiclink             = "false"
      enable_classiclink_dns_support = "false"
    }
    ```
* Ran `terraform init` command to download necessary plugins by AWS provider for Terraform to work.
* Ran the `terraform plan` command to see the resources to be created. Followed by `terraform apply` command for the resources to be created.
* Went to my VPC console and found two VPCs created already. A default VPC and the one created by Terraform. Match the CIDR block in the picture below with that in to code block above.


* I will also be creating 2 public subnets with the code block below
```
# Create public subnet1
    resource "aws_subnet" "public1" {
    vpc_id                     = aws_vpc.main.id
    cidr_block                 = "172.16.0.0/24"
    map_public_ip_on_launch    = true
    availability_zone          = "us-east-1a"
}

# Create public subnet2
    resource "aws_subnet" "public2" {
    vpc_id                     = aws_vpc.main.id
    cidr_block                 = "172.16.1.0/24"
    map_public_ip_on_launch    = true
    availability_zone          = "us-east-1b"
}
```
* Ran `terraform plan` followed by `terraform apply` to connfirm resources to be created as well as create the resources
* Apply ran succesfully and VPC subnets were created
* After comfirmation, I ran `terraform destroy` command to delete all created resources


## Our code blocks have hardcoded values and repeated codeblocks and we will be refactoring them soon
* Starting with the provider block, I declared a variable named region, gave it a default value, and updated the provider section by referring to the declared region variable.
* I did the same to other required arguments as shown below


* Fetched Availability zones from AWS, and replace the hard coded value in the subnet’s availability_zone section with the code block below
* Introduce a count argument in the subnet block in order to make use of this new data resource. The count tells us that we need 2 subnets. Therefore, Terraform will invoke a loop to create 2 subnets.

```
# Get list of availability zones
  data "aws_availability_zones" "available" {
  state = "available"
}

# Create public subnet1
resource "aws_subnet" "public" { 
  count                   = 2
  vpc_id                  = aws_vpc.main.id
  cidr_block              = "172.16.1.0/24"
  map_public_ip_on_launch = true
  availability_zone       = data.aws_availability_zones.available.names[count.index]
}
```

* To be noted that if we run Terraform with this configuration, it may succeed for the first time, but by the time it goes into the second loop, it will fail because we still have cidr_block hard coded. The same cidr_block cannot be created twice within the same VPC. We will be fixing this soon by making the cidr_block dynamic with the cidrsubnet() function as shown below

```
# Create public subnets
resource "aws_subnet" "public" { 
    count                   = 2
    vpc_id                  = aws_vpc.main.id
    cidr_block              = cidrsubnet(var.vpc_cidr, 4 , count.index)
    map_public_ip_on_launch = true
    availability_zone       = data.aws_availability_zones.available.names[count.index]
}
```

* We still have count to be hardcoded. To fix this, we will be using the length() function and the data.aws_availability_zones block as shown below
```
# Create public subnet1
resource "aws_subnet" "public" { 
    count                   = length(data.aws_availability_zones.available.names)
    vpc_id                  = aws_vpc.main.id
    cidr_block              = cidrsubnet(var.vpc_cidr, 4 , count.index)
    map_public_ip_on_launch = true
    availability_zone       = data.aws_availability_zones.available.names[count.index]
}
```
* An error with this approach is that length() will return 3 (as tested on terraform console), hence making terraform create 3 subnet. and we do not want that. TO circumvent this, we will create a 'preferred_number_of_public_subnets' variable as shown below. and we will use it to update the count argument in a ternary operator fashion to act as backup as illustrated in the codeblock below

```
variable "preferred_number_of_public_subnets" {
  default = 2
}

# Create public subnets
resource "aws_subnet" "public" {
  count  = var.preferred_number_of_public_subnets == null ? length(data.aws_availability_zones.available.names) : var.preferred_number_of_public_subnets   
  vpc_id = aws_vpc.main.id
  cidr_block              = cidrsubnet(var.vpc_cidr, 4 , count.index)
  map_public_ip_on_launch = true
  availability_zone       = data.aws_availability_zones.available.names[count.index]
}
```


## Introducing variables.tf & terraform.tfvars

#### To improve readability and better code structure, we will be moving out some parts of the configuration content to other files as enumerated below
* Created a new file and named it variables.tf
* Copied all the variable declarations into the variables.tf file.
* Created another terraform.tfvars file and set values for each of the variables.
* Ran terraform apply to create resources in AWS as shown in below
