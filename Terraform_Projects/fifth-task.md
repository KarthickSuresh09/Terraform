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

Step 3: use alrady have provider.tf files and create ec2.tf files inside tf file will create EC2 + User Data + SG
```
resource "aws_security_group" "web_sg" {
  name        = "apache-sg"
  description = "Allow HTTP and SSH"

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

resource "aws_instance" "web" {
  ami           = "ami-0f5fcdfbd140e4ab7"  # Amazon Linux 2023 for us-east-1
  instance_type = "t2.micro"

  vpc_security_group_ids = [aws_security_group.web_sg.id]

  user_data = file("apache.sh")

  associate_public_ip_address = true

  tags = {
    Name = "apache-ec2"
  }
}
```

Step 4:
```
```
This is folder Structure 
apache-ec2-project/
│
├── provider.tf
├── ec2.tf
├── outputs.tf
├── local_file.tf
│
├── apache.sh
│
└── terraform.tfstate        (auto-created after apply)

to check logs this command
sudo cat /var/log/cloud-init-output.log

