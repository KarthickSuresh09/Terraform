
Create an EC2 service in the default subnet in the Ohio region

Step 1: we need to create one folder to store terraform script

Step 2: we need create file provider.tf and ec2.tf

provider.tf 
provider "aws" {
  region  = "ap-south-2"
}

