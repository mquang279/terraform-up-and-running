# 1. Infrastructure as Code (IaC) là gì?
> **Infrastructure as Code (IaC)** là việc viết và thực thi code để định nghĩa, deploy, update và destroy hạ tầng thay vì phải cấu hình thủ công. Mục đích là quản lý mọi thứ bằng code bao gồm servers, databases, networks, application configurations,...

5 nhóm công cụ **Infrastructure as Code (IaC)** bao gồm:
- Ad Hoc Scripts
- Configuration Management Tools
- Server Templating Tools
- Orchestration Tools
- Provisioning Tools

# 2. Ad Hoc Scripts
**Ad Hoc Scripts** là script được viết để tự động hoá một task cụ thể thay vì thao tác thủ công.
Trước đây cần làm thủ công: SSH vào server -> Cài package -> Sửa config -> Restart service. Với Ad Hoc Scripts ta cần thực hiện:
- Chia nhỏ task thành từng bước
- Viết code cho từng bước
- Chạy script đó trên server

Ad hoc Scripts có thể viết bằng bất cứ ngôn ngữ gì như Bash, Ruby, Python,..

```bash
# Update the apt-get cache
sudo apt-get update

# Install PHP and Apache
sudo apt-get install -y php apache2

# Copy the code from the repository
sudo git clone https://github.com/brikis98/php-app.git
/var/www/html/app

# Start Apache
sudo service apache2 start
```

# 3. Configuration Management Tools
**Configuration Management Tools** là các công cụ dùng để Tự động cấu hình và duy trì trạng thái mong muốn của hệ thống trên các server đã tồn tại. Một số CM Tools phổ biến là **Ansible, Chef, Puppet,...**
```ansible
- name: Update the apt-get cache
  apt:
    update_cache: yes
- name: Install PHP
  apt:
    name: php
- name: Install Apache
  apt:
    name: apache2
- name: Copy the code from the repository
  git: repo=https://github.com/brikis98/php-app.git
dest=/var/www/html/app
- name: Start Apache
  service: name=apache2 state=started enabled=yes
```
Đoạn code trên là code **Ansible** dùng để config Apache Web Server tương tự đoạn bash scripts phía trước.
Sử dụng các **Configuration Management Tools** có nhiều ưu điểm hơn so với việc sử dụng **Ad Hoc Scripts**:
- **Coding Convention**: Với Ad hoc scripts, mỗi dev đặt tên khác nhau, cấu trúc khác nhau, ngôn ngữ khác nhau. Các CM Tools áp dụng convention chuẩn giúp dễ đọc, dễ maintain.
- **Idempotence**: Đối với CM Tools, Code chạy bao nhiêu lần cũng cho cùng 1 kết quả. Với ad hoc scripts phải tự xử lý, kiểm tra điều kiện.
- **Distribution**: Ad hoc scripts thiết kế cho 1 máy và local execution. Các tool như Ansible quản lý rất nhiều server và chạy từ 1 controller node -> nhiều target.
# 4. Server Templating Tools
**Server Templating Tools** là các công cụ dùng để tạo ra *machine image* đã được cấu hình và cài đặt sẵn, dùng để tạo server mới nhanh và nhất quán.
**Machine Image** là snapshot của hệ thống bao gồm:
- OS
- Installed Packages
- Config files
- Application code

Một số **Server Templating Tools** phổ biến là Packet, Docker,...

# 5. Provisioning Tools
**Provisioning Tools** là các công cụ dùng để Tạo và quản lý hạ tầng bằng code bao gồm server, network, storage, load balancer,...
Một số **Provisioning Tools** phổ biến là Terraform, CloudFormation, OpenStack Heat,...

# 6. Orchestration Tools
**Orchestration Tools** là các công cụ dùng để quản lý, điều phối và vận haàn hệ thống gồm nhiều VM/Container ở quy mô lớn một cách tự động. Chịu trách nhiệm vận hành hệ thống phân tán, đảm bảo ứng dụng luôn chạy đúng số lượng, đúng cách, và tự động thích nghi với lỗi và tải.
Sau khi đã Tạo Server (Terraform), Build Image (Packer/Docker), Cấu hình (Ansible). Orchestration sẽ chạy hệ thống đó.

Nhưng vấn đề mà **Orchestration Tools** giải quyết:
- **Deploy hiệu quả**: Deploy Container/VM sao cho tối ưu tài nguyên.
- **Update hệ thống**: Sử dụng các chiến lược quan trọng như Rolling Deployment, Blue-Green Deployment, Canary Deployment.
- **Auto Healing**: Nếu container chết thì tự động tạo lại.
- **Auto Scaling**: Tăng giảm số  lượng instance theo load thực tế.
- **Load Balancing**: Phân phối request giữa các instance, tránh overload 1 node.
- **Service Discovery**: Các service tự tìm thấy nhau, không cần hard code ip.
