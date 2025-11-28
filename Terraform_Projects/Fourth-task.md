1. Destroy the previous deployments
2. Create a VPC with the required components using Terraform
3. Deploy an EC2 instance inside the VPC

<img width="521" height="311" alt="image" src="https://github.com/user-attachments/assets/a9c5a7be-8b6a-4e10-ba49-9d84546a798a" />

<img width="1317" height="745" alt="image" src="https://github.com/user-attachments/assets/08df8326-3410-450d-b6af-4bbd07ef4001" />

* 1 VPC
* 1 Public Subnet
* 1 Internet Gateway
* 1 Route Table + association
* 1 Security Group (SSH)
* 1 EC2 instance inside the VPC

Step 1: terraform destroy --auto-approve

Step 2: I am creating a provider.tf files 
```
provider "aws" {
  region  = "ap-south-2"
}
```

Step 3: Now we are creating seperate vpc.tf file inside the file we creating (Simple VPC + Subnet + IGW + Route Table)
```
resource "aws_vpc" "main" {
  cidr_block = "10.0.0.0/16"

  tags = {
    Name = "simple-vpc"
  }
}

resource "aws_internet_gateway" "igw" {
  vpc_id = aws_vpc.main.id

  tags = {
    Name = "simple-igw"
  }
}

resource "aws_subnet" "public" {
  vpc_id                  = aws_vpc.main.id
  cidr_block              = "10.0.1.0/24"
  map_public_ip_on_launch = true

  tags = {
    Name = "simple-public-subnet"
  }
}

resource "aws_route_table" "public_rt" {
  vpc_id = aws_vpc.main.id

  route {
    cidr_block = "0.0.0.0/0"
    gateway_id = aws_internet_gateway.igw.id
  }

  tags = {
    Name = "simple-public-rt"
  }
}

resource "aws_route_table_association" "public_assoc" {
  subnet_id      = aws_subnet.public.id
  route_table_id = aws_route_table.public_rt.id
}
```
Step 4: Creating the new EC2.tf files inside file we creating (EC2 + SG only)
```
resource "aws_security_group" "ec2_sg" {
  name   = "simple-ec2-sg"
  vpc_id = aws_vpc.main.id

  ingress {
    from_port   = 22
    to_port     = 22
    protocol    = "tcp"
    cidr_blocks = ["0.0.0.0/0"]
  }

  egress {
    from_port   = 0
    to_port     = 0
    protocol    = "-1"
    cidr_blocks = ["0.0.0.0/0"]
  }

  tags = {
    Name = "simple-sg"
  }
}

resource "aws_instance" "my_ec2" {
  ami           = "ami-025ca978d4c1d9825" # Ubuntu Linux 2023 (us-east-1)
  instance_type = "t2.micro"

  subnet_id              = aws_subnet.public.id
  vpc_security_group_ids = [aws_security_group.ec2_sg.id]

  associate_public_ip_address = true

  tags = {
    Name = "simple-ec2"
  }
}

```

Step 5: terraform apply --auto-approve
