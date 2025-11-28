1. Destroy the previous deployment
2. Create 2 EC2 instances in Ohio and N.Virginia respectively
3. Rename Ohio’s instance to ‘hello-ohio’ and Virginia’s instance to
‘hello-virginia’

Step 1: terraform destroy --auto-approve

Step 2: Creating new provider.tf files "You cannot put two aliases and two regions inside ONE provider block."
* provider means like aws,azure,gcp
* alias is like
   * One SIM → one region
   * Want two regions? → You need two SIMs
   * Terraform calls each SIM a provider, and the label you give it is the alias
* region means we mention like which location to create 

```
provider "aws" {
  alias  = "ohio"
  region = "us-east-2"
}

provider "aws" {
  alias  = "virginia"
  region = "us-east-1"
}
```
Step 3: Createing new EC2.tf file 
```
resource "aws_instance" "ohio_ec2" {
  provider      = aws.ohio
  ami           = "ami-0e83be366243f524a"
  instance_type = "t2.micro"

  tags = {
    Name = "hello-ohio"
  }
}

resource "aws_instance" "virginia_ec2" {
  provider      = aws.virginia
  ami           = "ami-053a45fff0a704a47"
  instance_type = "t2.micro"

  tags = {
    Name = "hello-virginia"
  }
}
```
Step 4: terraform validate 
Step 5: terraform apply --auto-approve
