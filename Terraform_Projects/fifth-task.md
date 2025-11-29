---------------------------This is incomplete state-----------------------

1. Destroy the previous deployments
2. Create a script to install Apache2
3. Run this script on a newly created EC2 instance
4. Print the IP address of the instance in a file on the local once deployed

Step 1: terraform apply --auto-approve

Step 2: Create a file apache.sh to install Apache2
```
#!/bin/bash
sudo apt update -y
sudo apt install apache2 -y
sudo systemctl enable apache2
sudo systemctl start apache2
```

Step 3: use alrady have provider.tf files and create main.tf files inside tf file will create EC2 + VPC + SG
```
# Get default VPC
data "aws_vpc" "default" {
  default = true
}

# Get latest Ubuntu AMI in us-east-2
data "aws_ami" "ubuntu" {
  most_recent = true

  owners = ["099720109477"] # Canonical

  filter {
    name   = "name"
    values = ["ubuntu/images/hvm-ssd/ubuntu-jammy-22.04-amd64-server-*"]
  }

  filter {
    name   = "virtualization-type"
    values = ["hvm"]
  }
}

# Security Group
resource "aws_security_group" "apache_sg" {
  name        = "apache-sg"
  description = "Allow HTTP and SSH"
  vpc_id      = data.aws_vpc.default.id

  ingress {
    from_port   = 22
    to_port     = 22
    protocol    = "tcp"
    cidr_blocks = ["0.0.0.0/0"]
  }

  ingress {
    from_port   = 80
    to_port     = 80
    protocol    = "tcp"
    cidr_blocks = ["0.0.0.0/0"]
  }

  egress {
    from_port   = 0
    to_port     = 0
    protocol    = "-1"
    cidr_blocks = ["0.0.0.0/0"]
  }
}

# EC2 instance
resource "aws_instance" "apache" {
  ami           = data.aws_ami.ubuntu.id
  instance_type = "t2.micro"

  vpc_security_group_ids = [aws_security_group.apache_sg.id]

  user_data = file("${path.module}/apache.sh")

  tags = {
    Name = "Apache-Server"
  }
}

# Local file with IP
resource "local_file" "apache_ip" {
  filename = "${path.module}/ec2-ip.txt"
  content  = aws_instance.apache.public_ip
}
```

Step 4: Now run terraform script

This is folder Structure 
.
├── apache.sh
├── ec2-ip.txt (ec2-ip.txt file will created after terraform script executed sucessfully)
├── main.tf
├── provider.tf
└── terraform.tfstate

Step 5: terraform destroy is command to delete what to you created

to check logs this command
sudo cat /var/log/cloud-init-output.log

