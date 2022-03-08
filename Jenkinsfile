# Configure the AWS Provider
provider "aws" {
  region  = var.aws_region
}

resource "aws_security_group" "allow_http" {
  name = "allow_http"
  description = "allow HTTP and SSH traffic"
  vpc_id = aws_vpc.kellyvpc.id

  dynamic "ingress" {
    for_each = var.ingress_rules
    content { 
      from_port = ingress.value.from_port
      to_port = ingress.value.to_port
      protocol = ingress.value.protocol
      cidr_blocks = ingress.value.cidr_blocks
    }
}

  egress {
    from_port = 0
    to_port = 0
    protocol = "-1"
    cidr_blocks = ["0.0.0.0/0"]
  }
}

resource "aws_vpc" "kellyvpc" {
  cidr_block = var.vpc_cidr_block
}

resource "aws_subnet" "mainpub" {
  vpc_id     = aws_vpc.kellyvpc.id
  cidr_block = var.public_subnet_cidr_block
  map_public_ip_on_launch = true

  tags = {
    Name = "MainPub"
  }
}


resource "aws_subnet" "mainpriv" {
  vpc_id     = aws_vpc.kellyvpc.id
  cidr_block = var.private_subnet_cidr_block

  tags = {
    Name = "MainPriv"
  }
}

resource "aws_internet_gateway" "kellygw" {
  vpc_id = aws_vpc.kellyvpc.id

  tags = {
    Name = "kellygw"
  }
}

resource "aws_route_table" "kelly_rt" {
  vpc_id  = aws_vpc.kellyvpc.id

  route {
    cidr_block  = "0.0.0.0/0"
    gateway_id  = aws_internet_gateway.kellygw.id
  }

  tags = {
    Name = "Demo-Route-Table"
  }
}


resource "aws_route_table_association" "rt" {
  subnet_id   = aws_subnet.mainpub.id
  route_table_id  = aws_route_table.kelly_rt.id
}

resource "tls_private_key" "this" {
  algorithm = "RSA"
}

module "key_pair" {
  source = "terraform-aws-modules/key-pair/aws"

  key_name   = "deployer-one"
  public_key = tls_private_key.this.public_key_openssh
}

resource "aws_instance" "terraform_ec2" {
  ami = "ami-033b95fb8079dc481"
  instance_type = var.ec2_type
  monitoring = false
  vpc_security_group_ids = [aws_security_group.allow_http.id]
  subnet_id = aws_subnet.mainpub.id
  associate_public_ip_address = true
  depends_on  = [aws_internet_gateway.kellygw]

  tags = {
    Name = "Terraform_Ec2"
  }
}

output "instance_ips" {
  value = aws_instance.terraform_ec2.public_ip
    
