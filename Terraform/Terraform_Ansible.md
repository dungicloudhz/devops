# 1. Giới thiệu về khóa học Infrastructure as code với Terraform và Ansible
# 3. Chú ý quan trọng về điều kiện để tham gia khóa học
# 6. Setup AWS CLI và Ansible
Tùy thuộc vào hệ điều hành (OS), sử dụng lệnh yum, apt-get, dnf để setup python pip (Python Package Installer).

- Cài đặt Ansible à download Ansible configure file (Link trong phần tai nguyên).
- Cài đặt AWS CLI, va cấu hình AWS CLI (aws configure)

```bash
yum update -y # update hệ điều hành
```
Trong một số trường hợp các lệnh không tương thích với hệ điều hành SELinux ta cần disable tính năng này đi
```bash
vim /etc/selinux/config
# sửa dòng cấu hình và coment dòng sau
SELINUX=disabled
# SELINUXTYPE=targeted // Vô hiệu hóa dòng này
:wq # quit và lưu lại nội dung của file config
```
Cài đặt python 3.11 pip
```bash
python3.11 --version
# Python 3.11.7
dnf install python3.11-pip -y
```
Lưu tất cả các file terraform và ansible template vào cùng một thư mục
```bash
mkdir tf_ansible_deploy
cd tf_ansible_deploy
# Download file cấu hình của ansible
wget https://raw.gethubusercontent.com/phuongluuho/TerraformAnsible/main/ansible.cfg
ls
# ansible.cfg

pip3.11 install --upgrade pip # update pip  
pip3.11 install --upgrade pip --user # update pip with user root 

# Cài đặt ansible
yum install epel-release # -> Y (yes)

yum install ansible # -> Y (yes)

rpm -qa|grep ansible # kiểm tra ansible đã cài đặt thành công chưa

# Setup aws CLI
curl "https://awscli.amazonaws.com/awscli-exe-linux-x86_64.zip" -o "awscliv2.zip"

yum install unzip # down cli giải nén file

unzip awscliv2.zip giải nén file

sudo /aws/install # cài đặt aws CLI

# Kiểm tra aws và ansible có được cài đặt thành công hay không
ansible --version # kiểm tra version của ansible
aws --version # kiểm tra version của aws CLI

# Cấu hình thông tin xác thực cho AWS CLI từ phần user trên console.
aws configure
# ... Nhập Access key
# ... Nhập Secret Access key
# ... Region name ...
# ... Output format: json

# Test xem AWS CLI có thể làm việc và tương tác với các dịch vụ và tài nguyên trong tài khoản AWS của bạn hay ko?sử dụng lệnh
aws ec2 describe-instances # liệt kê các ec2 (kiểm tra đã có truyền sử dụng AWS CLI)
```
Chúng ta sử dụng lệnh aws configure để cấu hình thông tin xác thực cho AWS CLI, để xác thực và tương tác với các dịch vụ AWS từ môi trường dòng lệnh để xác thực và tương tác với các dịch vụ AWS.