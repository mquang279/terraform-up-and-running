# Deploy a Cluster of Webserver
Để tạo **Auto Scaling Group** thì đầu tiên cần tạo **launch configuration** - định nghĩa config của các EC2 Instance chạy trong **Auto Scaling Group**.
Resource `aws_launch_template` sử dụng tương tự như `aws_instance`. Tuy nhiên không có tags và `user_data_replace_on_change` parameter.
```tf
resource "aws_launch_template" "example" {
  image_id               = "ami-0fb653ca2d3203ac1"
  instance_type          = "t2.micro"
  vpc_security_group_ids = [aws_security_group.instance.id]
  user_data = base64encode(<<-EOF
                            #!/bin/bash
                            echo "Hello, World" > index.html
                            nohup busybox httpd -f -p ${var.server_port} &
                            EOF
  )
}
```
Tiếp theo, tạo **Auto Scaling Group** sử dụng `aws_autoscaling_group` resource
```tf
resource "aws_autoscaling_group" "example" {
 launch_template {
    id      = aws_launch_template.example.id
    version = "$Latest"
 }
 min_size = 2
 max_size = 10
 tag {
 key = "Name"
 value = "terraform-asg-example"
 propagate_at_launch = true
 }
}
```
Vì `aws_launch_template` là immutable, có nghĩa là nếu ta chỉnh sửa thì Terraform sẽ xoá resource sau đó tạo lại. Mà `aws_autoscaling_group` lại tham chiếu đến `aws_launch_template`. Khi đó, nếu thay đổi `aws_launch_template` thì ASG vẫn đang dùng launch config cũ, Terraform cố xoá launch config sẽ bị AWS từ chối vì resource đó vẫn đang được sử dụng.
Giải pháp cho việc này là sử dụng `lifecycle` block. Tất cả các Terraform resource đều hỗ trợ `lifecycle` block, dùng để config cách resource được tạo, update và delete. Trong trường hợp này, nên sử dụng `create_before_destroy`, khi đó terraform sẽ:
- Tạo resource mới trước và update tất cả các references đến resource này.
- Sau đó xoá resource cũ.

```tf
resource "aws_launch_template" "example" {
  image_id               = "ami-0fb653ca2d3203ac1"
  instance_type          = "t2.micro"
  vpc_security_group_ids = [aws_security_group.instance.id]
  user_data = base64encode(<<-EOF
                            #!/bin/bash
                            echo "Hello, World" > index.html
                            nohup busybox httpd -f -p ${var.server_port} &
                            EOF
  )
  lifecycle {
    create_before_destroy = true
  }
}
```
Tiếp theo cần chỉ định subnet để chạy EC2 Instance cho **ASG**. Để lấy danh sách subnet từ AWS, sử dụng `data sources`. **Data sources** là những dữ liệu được lấy từ cloud provider.
```tf
data "<PROVIDER>_<TYPE>" "<NAME>" {
 [CONFIG ...]
}
```
- `PROVIDER`: tên của cloud provider (`aws`, `google`,...)
- `TYPE`: loại data source muốn lấy (`vpc`, `subnet`,...)
- `NAME`: là tên của datasource, dùng trong Terraform code.

Để lấy data từ data source, sử dụng:
```tf
data.<PROVIDER>_<TYPE>.<NAME>.<ATTRIBUTE>
data.aws_vpc.default.id
```
