# Deploy a single server
Đầu tiên, cần config provider muốn sử dụng. Ví dụ muốn sử dụng AWS với `us-east-2` region
```tf
provider "aws" {
    region = "us-east-2"
}
```
Đối với mỗi provider, sẽ có các loại `resources` khác nhau có thể tạo. Syntax để tạo resource trong Terraform là:
```tf
resource "<PROVIDER>_<TYPE>" "<NAME>" {
    [CONFIG ...]
}
```
- `PROVIDER`: Là tên của cloud provider (ví dụ `aws`, `google`,...)
- `TYPE`: Là loại resource cần tạo (ví dụ `instance`, `vpc`, `subnet`,...)
- `NAME`: Là tên đặt cho resource, có thể dùng để gọi đến nó trong Terraform code (Không phải tên của resource khi tạo trên cloud provider).

Các lệnh thực thi:
- `terraform init`: Chuẩn bị workspace để Terraform chạy. Khi chạy nó sẽ Tải provider, Tạo file lock (Ghi lại version provider), Khởi tạo backend (Nếu có). Cần chạy lại khi lần đầu clone repo, thêm provider mới, thay đổi backend.
- `terraform plan`: Xem Terraform sẽ làm gì trước khi thực thi.
- `terraform apply`: Áp dụng thay đổi vào infrastructure thật.