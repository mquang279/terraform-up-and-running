# Deploy a Configurable Webserver
**DRY (Don't Repeat Yourself) Principle** phát biểu rằng không nên lặp lại logic, dữ liệu hoặc cấu hình trong nhiều nơi.
Khi thực hiện deploy single webserver, việc chỉ định port 8080 xảy ra ở nhiều nơi. Dẫn tới vi phạm DRY Principle.
## 1. Input Variables
Để tuân thủ DRY Principle, có thể định nghĩa các input variables.
```tf
variable "NAME" {
 [CONFIG ...]
}
```
Phần `CONFIG` có thể có các parameter sau:
- `description`: Mô tả cách mà variable được sử dụng.
- `default`: Giá trị mặc định của variable. Giá trị của variable có thể được set bằng cách sử dụng `-var` khi chạy terraform apply hoặc sử dụng environment variable. Nếu không set thì variable có giá trị default.
- `type`: Kiểu dữ liệu của variable bao gồm `string, number, bool, list, map, set, object, tuple, any`. Nếu không chỉ định type thì type mặc định là `any`.
- `validation`: Định nghĩa quy tắc cho variable.
- `sensitive`: Nếu đặt là `true` thì Terraform sẽ không in ra khi chạy `plan` và `apply`. Nên sử dụng cho các secret variable như password, API key,...
```tf
variable "number_example" {
 description = "An example of a number variable in Terraform"
 type = number
 default = 42
}
```
## 2. Output Variables
```tf
output "<NAME>" {
    value = <VALUE>
    [CONFIG...]
}
```
Phần config có thể có các parameter sau:
- `sensitive`: Nếu đặt là `true` thì terraform sẽ không log variable này khi chạy `plan` hoặc `apply`.
- `description`: Mô tả về output variables.

Ví dụ để hiển thị EC2 Instance public IP mà không cần truy cập console:
```tf
output "public_ip" {
 value = aws_instance.example.public_ip
 description = "The public IP address of the web server"
}
```