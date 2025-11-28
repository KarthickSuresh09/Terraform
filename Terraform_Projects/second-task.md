1. Destroy the previous deployment
2. Create a new EC2 instance with an Elastic IP

Step 1: terraform destroy means it will delete what we created so i delete previouly deployment

step 2: i am using previous provider.tf and creating a new EC2 file
ec2-with-eip.tf
```
resource "aws_instance" "web" {
  ami           = "ami-0e83be366243f524a" # use a valid AMI for us-east-2 or your region
  instance_type = "t2.micro"

  # Let AWS place it in default subnet of default VPC
  # (no subnet_id needed if default VPC exists)

  associate_public_ip_address = false

  tags = {
    Name = "ec2-with-eip"
  }
}

# Allocate and associate Elastic IP to the EC2 instance
resource "aws_eip" "web_eip" {
  instance = aws_instance.web.id
  domain   = "vpc"
}
```
Step 3: terraform apply
