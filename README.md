# terraform-aws-infrastructure
<img width="1536" height="1024" alt="lab3" src="https://github.com/user-attachments/assets/e83279c2-59cf-43d5-bc1e-476534624d80" />

## Project Goal

### Terraform will create:

- ✅ Custom VPC
- ✅ Public Subnet
- ✅ Internet Gateway
- ✅ Route Table
- ✅ Security Group
- ✅ EC2 Instance (Ubuntu)

Exactly like the architecture diagram we created.


---

Before Starting

### Install Terraform

Open PowerShell:

`terraform -version`

If you get:

`terraform is not recognized`

install Terraform first.

Download:

`[Terraform Download](https://developer.hashicorp.com/terraform/downloads?utm_source=chatgpt.com)`

Install and restart PowerShell.

Verify:

`terraform -version`


---

### Step 1 — Create Project Folder on your Desktop

Create:

`terraform-aws-infra`

Inside create:

terraform-aws-infra
│
├── main.tf
├── variables.tf
├── outputs.tf
└── provider.tf

all these files via notepad or any other editor

---

### Step 2 — Create IAM User for Terraform

Go to:

IAM
→ Users
→ Create User

Name:

`terraform-user`

Attach policy:

`AdministratorAccess`

(For learning lab only)

Create user.


---

### Step 3 — Create Access Key

Open:

terraform-user
→ Security Credentials
→ Create Access Key

Select:

Command Line Interface (CLI)

Copy:

`Access Key ID
Secret Access Key`

Save them.


---

### Step 4 — Configure AWS CLI

Install AWS CLI:

`[AWS CLI Download](https://aws.amazon.com/cli/?utm_source=chatgpt.com)`

Verify:

`aws --version`

Now configure:

`aws configure`

Enter:

`Access Key ID
Secret Access Key
Region
json`

Example:

`ap-south-1`


---

### Step 5 — Create provider.tf

Paste:
```
provider "aws" {
  region = "ap-south-1"
}
```
Replace region if needed according to your region.


---

### Step 6 — Create main.tf

Paste:
```
resource "aws_vpc" "lab_vpc" {

  cidr_block = "10.0.0.0/16"

  tags = {
    Name = "Lab3-VPC"
  }
}
```

---

### Step 7 — Create Public Subnet

Add below VPC in main.tf:
```
resource "aws_subnet" "public_subnet" {

  vpc_id = aws_vpc.lab_vpc.id

  cidr_block = "10.0.1.0/24"

  map_public_ip_on_launch = true

  tags = {
    Name = "Lab3-Public-Subnet"
  }
}
```

---

### Step 8 — Create Internet Gateway

Add in main.tf:
```
resource "aws_internet_gateway" "igw" {

  vpc_id = aws_vpc.lab_vpc.id

  tags = {
    Name = "Lab3-IGW"
  }
}
```

---

### Step 9 — Create Route Table

Add in main.tf:
```
resource "aws_route_table" "public_rt" {

  vpc_id = aws_vpc.lab_vpc.id

  route {

    cidr_block = "0.0.0.0/0"

    gateway_id = aws_internet_gateway.igw.id
  }

  tags = {
    Name = "Lab3-Route-Table"
  }
}
```

---

### Step 10 — Associate Subnet

Add in main.tf:
```
resource "aws_route_table_association" "rta" {

  subnet_id = aws_subnet.public_subnet.id

  route_table_id = aws_route_table.public_rt.id
}
```

---

### Step 11 — Create Security Group

Add in main.tf:
```
resource "aws_security_group" "web_sg" {

  vpc_id = aws_vpc.lab_vpc.id

  ingress {

    from_port = 22

    to_port = 22

    protocol = "tcp"

    cidr_blocks = ["0.0.0.0/0"]
  }

  ingress {

    from_port = 80

    to_port = 80

    protocol = "tcp"

    cidr_blocks = ["0.0.0.0/0"]
  }

  egress {

    from_port = 0

    to_port = 0

    protocol = "-1"

    cidr_blocks = ["0.0.0.0/0"]
  }

  tags = {
    Name = "Lab3-SG"
  }
}
```

---

### Step 12 — Create Key Pair

AWS Console:

EC2
→ Key Pairs
→ Create Key Pair

Name:

terraform-key

Download:

terraform-key.pem

Keep safe in terraform-aws-infra folder.


---

### Step 13 — Get Ubuntu AMI

Open:

`EC2
→ Launch Instance
→ Ubuntu 24.04`

Copy AMI ID.

Example:

`ami-xxxxxxxxxxxx`

Cancel instance launch.


---

### Step 14 — Create EC2 Resource

Add to main.tf:
```
resource "aws_instance" "web_server" {

  ami = "YOUR_AMI_ID"

  instance_type = "t2.micro"

  subnet_id = aws_subnet.public_subnet.id

  vpc_security_group_ids = [
    aws_security_group.web_sg.id
  ]

  key_name = "terraform-key"

  tags = {
    Name = "Terraform-Web-Server"
  }
}
```
Replace:

`YOUR_AMI_ID`

with your actual AMI.


---
### Step 15 — Initialize Terraform on your command prompt

Inside project folder:

`terraform init`

Expected:

`Terraform has been successfully initialized`


---

### Step 16 — Validate

`terraform validate`

Expected:

`Success`


---

### Step 17 — Plan

`terraform plan`

Terraform will show:

+ create VPC
+ create Subnet
+ create IGW
+ create Route Table
+ create SG
+ create EC2


---

### Step 18 — Deploy

`terraform apply`

Type:

`yes`

Terraform starts building AWS resources automatically 🚀


---

### Step 19 — Verify

AWS Console:

`VPC
Subnet
IGW
Route Table
Security Group
EC2`

Everything should exist.


---

### Step 20 — SSH on cmd prompt

`ssh -i terraform-key.pem ubuntu@PUBLIC_IP`

Success 🎉


---

### Step 21 — Destroy Resources

Very important to avoid charges:

`terraform destroy`

Type:

`yes`

Terraform removes everything automatically.


---

Portfolio Skills After Lab 3

- ✅ Terraform
- ✅ Infrastructure as Code
- ✅ VPC
- ✅ Subnetting
- ✅ Internet Gateway
- ✅ Route Tables
- ✅ Security Groups
- ✅ EC2 Provisioning
- ✅ AWS CLI
- ✅ Resource Lifecycle Management


---
