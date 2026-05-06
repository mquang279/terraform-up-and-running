# Deploy a Single Webserver
Đầu tiên cần config cloud provider muốn sử dụng
```tf
provider "aws" {
  region = "us-east-2"
}
```
EC2 Instance sẽ chạy script trong `user_data` argument vào lần đầu boot.
```tf
resource "aws_instance" "example" {
  ami                    = "ami-0fb653ca2d3203ac1"
  instance_type          = "t2.micro"

  user_data = <<-EOF
            #!/bin/bash
            echo "Hello, World" > index.html
            nohup busybox httpd -f -p 8080 &
            EOF

  user_data_replace_on_change = true
  tags = {
    Name = "terraform-example-2"
  }
}
```
Vì shell script trong `user_data` chỉ chạy trong lần đầu EC2 boot, nên nếu muốn chỉnh sửa `user_data` thì cần thêm argument `user_data_replace_on_change = true`. Khi đó nếu `user_data` thay đổi thì Terraform sẽ terminate EC2 Instance hiện tại vào tạo Instance mới.
Vì AWS không cho traffic đi vào EC2 Instance, nên cần config thêm security group resource.
```tf
resource "aws_security_group" "instance" {
  name = "terraform-example-instance"

  ingress {
    from_port   = 8080
    to_port     = 8080
    protocol    = "tcp"
    cidr_blocks = ["0.0.0.0/0"]
  }
}
```
Sau đó, cần gán security group đã tạo cho EC2 Instance
```tf
resource "aws_instance" "example" {
  ami                    = "ami-0fb653ca2d3203ac1"
  instance_type          = "t2.micro"
  vpc_security_group_ids = [aws_security_group.instance.id]

  user_data = <<-EOF
            #!/bin/bash
            echo "Hello, World" > index.html
            nohup busybox httpd -f -p 8080 &
            EOF
            
  user_data_replace_on_change = true
  tags = {
    Name = "terraform-example-2"
  }
}
```