
Create an EC2 service in the default subnet in the Ohio region

Step 1: we need to create one folder to store terraform script

Step 2: we need create file provider.tf and ec2.tf

provider.tf 
```
provider "aws" {
  region  = "ap-south-2"
}
```
ec2.tf
```
resource "aws_instance" "simple_ec2" {
  ami           = "ami-0f5fcdfbd140e4ab7" # <- use a valid AMI for your region
  instance_type = "t2.micro"

  # No subnet_id -> AWS will use default subnet in default VPC

  associate_public_ip_address = true

  tags = {
    Name = "simple-ec2"
  }
}
```
