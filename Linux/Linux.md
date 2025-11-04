# Hướng Dẫn Sử Dụng Các Lệnh Cơ Bản Trong Linux
Linux là một hệ điều hành mã nguồn mở mạnh mẽ, được sử dụng rộng rãi trong các hệ thống máy chủ, lập trình, và nhiều lĩnh vực khác. Việc nắm vững các lệnh cơ bản của Linux không chỉ giúp bạn thao tác nhanh chóng mà còn tăng hiệu quả làm việc. Trong bài viết này, chúng ta sẽ cùng khám phá các lệnh cơ bản đến nâng cao, từ cách quản lý file, thư mục, biến môi trường, đến giám sát hệ thống và xử lý logs.

# I. Giới Thiệu Về Terminal Linux
**Terminal Là Gì?**
Terminal (hay còn gọi là CLI - Command Line Interface) là một công cụ mạnh mẽ trên Linux, cho phép người dùng tương tác trực tiếp với hệ điều hành thông qua các dòng lệnh. Không giống như giao diện đồ họa (GUI), Terminal chỉ sử dụng văn bản để nhập liệu và hiển thị kết quả, từ đó mang lại sự kiểm soát chi tiết và linh hoạt hơn đối với hệ thống.

**Vì Sao Terminal Quan Trọng?**
1. **Tính Linh Hoạt**: Terminal cho phép bạn truy cập và cấu hình tất cả các thành phần của hệ thống, điều mà GUI không thể làm.
2. **Hiệu Quả Cao**: Với Terminal, các tác vụ phức tạp có thể được thực hiện nhanh chóng chỉ với một vài lệnh.
3. **Tự Động Hóa**: Bạn có thể viết script để tự động hóa các công việc lặp đi lặp lại.
4. **Khả Năng Tương Thích**: Hầu hết các máy chủ Linux đều không có giao diện đồ họa, vì vậy việc sử dụng Terminal là cần thiết.

## Các Lệnh Cơ Bản Trong Terminal
Dưới đây là một số lệnh cơ bản giúp bạn làm quen với Terminal.

### 1. `history`: Xem Lịch Sử Các Lệnh Đã Chạy
Lệnh `history` hiển thị danh sách các lệnh mà bạn đã nhập vào Terminal trước đó. Điều này giúp bạn dễ dàng theo dõi và sử dụng lại các lệnh đã chạy.
Cách Sử Dụng:
```bash
$ history
```
Kết Quả:
```bash
1  ls
2  cd /home
3  mkdir my_folder
4  history
```
Bạn cũng có thể lọc kết quả bằng grep:
```bash
$ history | grep ssh
```
Kết Quả:
```bash
15  ssh user@192.168.1.1
```
Xóa lịch sử lệnh:
```bash
$ history -c
```
Lệnh này sẽ xóa toàn bộ lịch sử lệnh trong phiên làm việc hiện tại.
___
### 2. `clear`: Xóa Màn Hình Terminal
Lệnh `clear` giúp xóa toàn bộ nội dung trên màn hình Terminal, làm cho giao diện trở nên sạch sẽ và dễ nhìn.
**Cách Sử Dụng:**
```bash
$ clear
```
**Kết Quả**: Màn hình Terminal sẽ sạch và chỉ còn lại dấu nhắc lệnh ($).
### 3. `exit`: Thoát Khỏi Terminal
Lệnh `exit` dùng để thoát khỏi phiên làm việc hiện tại hoặc đăng xuất khỏi Terminal.
**Cách Sử Dụng:**
```bash
$ exit
```
**Kết Quả**: Bạn sẽ thoát khỏi phiên làm việc hiện tại. Nếu bạn đang sử dụng SSH, lệnh này cũng sẽ kết thúc kết nối SSH.
### 4. `shutdown`: Tắt Máy
Lệnh `shutdown` cho phép bạn tắt máy hoặc khởi động lại hệ thống một cách an toàn.
**Cách Sử Dụng**:
- Tắt máy ngay lập tức:
```bash
$ sudo shutdown -h now
```
- Khởi động lại hệ thống:
```bash
$ sudo shutdown -r now
```
___
## Các Phím Tắt Hữu Ích Trong Terminal
Ngoài các lệnh cơ bản, bạn cũng nên biết một số phím tắt phổ biến để thao tác trong Terminal:

1. Ctrl + C: Dừng lệnh đang chạy.
2. Ctrl + D: Thoát khỏi Terminal (tương tự lệnh `exit`).
3. Ctrl + R: Tìm kiếm lệnh trong lịch sử.
4. Tab: Tự động hoàn thành lệnh hoặc tên file/thư mục.
___
## Ứng Dụng Thực Tế Của Terminal
**1. Quản Trị Hệ Thống**
- Kiểm tra trạng thái hệ thống bằng các lệnh như `top`, `htop`, `uptime`.
- Tạo và xóa người dùng với `adduser` và `deluser`.
**2. Phát Triển Phần Mềm**
- Biên dịch mã nguồn với `gcc`, `make`.
- Quản lý phiên bản mã nguồn bằng `git`.
**3. Quản Lý Máy Chủ**
- Kết nối đến máy chủ từ xa bằng `ssh`.
- Chuyển file giữa các máy bằng `scp` hoặc `rsync`.
## Mẹo Sử Dụng Terminal Hiệu Quả
**1. Sử Dụng Alias**: Tạo alias để rút ngắn các lệnh dài.
```bash
$ alias ll='ls -al'
```
**2. Sử Dụng Script**: Viết các script shell để tự động hóa công việc.
**3. Tìm Hiểu Các Tùy Chọn**: Dùng lệnh man để xem chi tiết về các tùy chọn của lệnh.
```bash
$ man history
```
# II. Thao Tác Với File, Folder Và Đường Dẫn
Việc quản lý file và thư mục là một trong những kỹ năng cơ bản mà bất kỳ người dùng Linux nào cũng cần nắm vững. Từ việc tạo, chỉnh sửa, xóa file đến tổ chức cấu trúc thư mục, các lệnh dưới đây sẽ giúp bạn làm chủ hệ thống Linux một cách hiệu quả.

## 1. Quản Lý Thư Mục
### 1.1. Tạo Thư Mục Với mkdir
Lệnh mkdir được sử dụng để tạo thư mục mới. Bạn có thể tạo một hoặc nhiều thư mục cùng lúc, hoặc tạo cấu trúc thư mục lồng nhau.
Cách sử dụng:
```bash
$ mkdir my_folder
$ mkdir folder1 folder2 folder3
$ mkdir -p parent_folder/child_folder/grandchild_folder
```
Kết quả:
```bash
$ ls
my_folder folder1 folder2 folder3 parent_folder
$ tree parent_folder
parent_folder
└── child_folder
    └── grandchild_folder
```

### 1.2. Di Chuyển Giữa Các Thư Mục Với cd
Lệnh cd giúp bạn thay đổi thư mục hiện tại. Đây là một trong những lệnh được sử dụng thường xuyên nhất.
Cách sử dụng:
```bash
$ cd my_folder
$ cd ..              # Quay lại thư mục cha
$ cd /home/user      # Di chuyển đến đường dẫn cụ thể
$ cd ~               # Quay về thư mục gốc của user
```
Kết quả:
```bash
$ pwd
/home/user/my_folder
$ cd ..
$ pwd
/home/user
```

### 1.3. Hiển Thị Đường Dẫn Hiện Tại Với pwd
Lệnh pwd (print working directory) hiển thị đường dẫn đầy đủ của thư mục hiện tại.
Cách sử dụng:
```bash
$ pwd
```
Kết quả:
```bash
/home/user
```

## 2. Quản Lý File
### 2.1. Tạo File Trống Với touch
Lệnh touch được sử dụng để tạo file trống hoặc cập nhật thời gian truy cập file.
Cách sử dụng:
```bash
$ touch file1.txt
$ touch file2.txt file3.txt
```
Kết quả:
```
$ ls
file1.txt file2.txt file3.txt
```

### 2.2. Liệt Kê File Và Thư Mục Với ls
Lệnh ls hiển thị danh sách các file và thư mục trong thư mục hiện tại. Nó có nhiều tùy chọn để hiển thị thông tin chi tiết.
Cách sử dụng:
```bash
$ ls
$ ls -l           # Hiển thị chi tiết
$ ls -a           # Hiển thị cả file ẩn
$ ls -lh          # Hiển thị kích thước file dạng dễ đọc
```
Kết quả:
```bash
$ ls -lh
-rw-r--r--  1 user group 0 May 13 19:00 file1.txt
-rw-r--r--  1 user group 0 May 13 19:01 file2.txt
```
### 2.3. Chỉnh Sửa File Với nano, vi, vim
Các trình soạn thảo dòng lệnh như nano, vi, và vim rất hữu ích để chỉnh sửa file trực tiếp từ Terminal.
**Nano:**
```bash
$ nano file.txt

Ctrl + O: Lưu file
Ctrl + X: Thoát
```
**Vi/Vim:**
```bash
$ vi file.txt

i: Chuyển sang chế độ chỉnh sửa
ESC: Quay về chế độ lệnh
:w: Lưu file
:q: Thoát
:wq: Lưu và thoát
```
### 2.4. Xóa File Và Thư Mục Với rm
Lệnh rm được sử dụng để xóa file hoặc thư mục.
Cách sử dụng:
```bash
$ rm file1.txt             # Xóa file
$ rm -r folder             # Xóa thư mục và nội dung bên trong
$ rm -f file.txt           # Xóa file mà không cần xác nhận
```
Kết quả:
```bash
$ ls
file2.txt file3.txt
```
### 2.5. Sao Chép File/Thư Mục Với cp
Lệnh cp dùng để sao chép file hoặc thư mục.
Cách sử dụng:
```bash
$ cp source.txt dest.txt   # Sao chép file
$ cp -r source_folder/ dest_folder/  # Sao chép thư mục
```
Kết quả:
```
$ ls dest_folder/
source.txt
```
### 2.6. Di Chuyển hoặc Đổi Tên File Với mv
Lệnh mv được sử dụng để di chuyển hoặc đổi tên file/thư mục.
Cách sử dụng:
```bash
$ mv old_name.txt new_name.txt   # Đổi tên file
$ mv file.txt /path/to/dest/     # Di chuyển file
```
Kết quả:
```bash
$ ls /path/to/dest/
file.txt
```
## 3. Các Ứng Dụng Thực Tế
### 3.1. Tổ Chức Dữ Liệu
Với các lệnh như mkdir, ls, cp, bạn có thể dễ dàng tổ chức dữ liệu theo cấu trúc thư mục logic. Ví dụ:
```bash
$ mkdir -p projects/{project1,project2}/docs
$ tree projects
projects
├── project1
│   └── docs
└── project2
    └── docs
```
### 3.2. Dọn Dẹp File Không Cần Thiết
Sử dụng rm để xóa những file và thư mục không còn sử dụng:
```bash
$ rm -r temp_folder/
```
### 3.3. Di Chuyển Dữ Liệu Giữa Các Thư Mục
Với mv và cp, bạn có thể di chuyển hoặc sao chép dữ liệu dễ dàng:
```bash
$ mv old_folder/ new_folder/
$ cp -r backup/ new_backup/
```
# III. Thao Tác Với Biến Môi Trường
Biến môi trường trong Linux là những giá trị mà hệ thống hoặc các ứng dụng sử dụng để lưu trữ thông tin cấu hình. Chúng đóng vai trò quan trọng trong việc xác định hành vi của hệ thống hoặc ứng dụng đang chạy. Các thao tác với biến môi trường giúp bạn quản lý và cấu hình hệ thống hiệu quả hơn.

## 1. Quản Lý Biến Môi Trường Cục Bộ
### 1.1. Hiển Thị Giá Trị Biến Môi Trường Với echo
Lệnh echo được sử dụng để hiển thị giá trị của các biến môi trường.
Cách sử dụng:
```bash
$ echo $HOME
```
Kết quả:
```bash
/home/user
```
Ví dụ khác:
```bash
$ echo $PATH
/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin
```
### 1.2. Tạo Biến Môi Trường Cục Bộ
Bạn có thể tạo biến môi trường cục bộ bằng cách gán giá trị cho một biến. Lưu ý rằng biến này chỉ tồn tại trong phiên làm việc hiện tại.
Cách sử dụng:
```bash
$ MYVAR=1234
$ echo $MYVAR
1234
```
Nếu mở một shell mới, biến này sẽ không còn tồn tại:
```bash
$ bash
$ echo $MYVAR
```
Kết quả:
```bash

```
## 2. Quản Lý Biến Môi Trường Toàn Cục
### 2.1. Tạo Biến Toàn Cục Với export
Lệnh export giúp bạn biến một biến môi trường cục bộ thành toàn cục, nghĩa là nó sẽ tồn tại trong bất kỳ shell nào được mở sau đó.
Cách sử dụng:
```bash
$ export MYVAR=1234
$ echo $MYVAR
1234
```
Kiểm tra trong một shell mới:
```bash
$ bash
$ echo $MYVAR
1234
```
### 2.2. Xóa Biến Môi Trường Với unset
Lệnh unset được sử dụng để xóa các biến môi trường đã được thiết lập.
Cách sử dụng:
```bash
$ unset MYVAR
$ echo $MYVAR
```
Kết quả:
```bash


```
## 3. Cấu Hình Biến Môi Trường Mặc Định
Biến môi trường có thể được lưu trong các file cấu hình để tự động được thiết lập mỗi khi bạn đăng nhập hoặc mở một shell mới. Các file này bao gồm:
### 3.1. File .bashrc
File .bashrc nằm trong thư mục home của người dùng. Tất cả các biến trong file này sẽ được áp dụng mỗi khi bạn mở một terminal mới.
Ví dụ:
```bash
$ nano ~/.bashrc
```
Thêm dòng:
```bash
export MYVAR=5678
```
Lưu và áp dụng:
```bash
$ source ~/.bashrc
$ echo $MYVAR
5678
```
### 3.2. File .bash_profile
File .bash_profile được sử dụng khi bạn đăng nhập vào hệ thống qua SSH hoặc các phiên làm việc login shell.
Ví dụ:
```bash
$ nano ~/.bash_profile
```
Thêm dòng:
```basg
export MYVAR=91011
```
Lưu và áp dụng:
```bash
$ source ~/.bash_profile
$ echo $MYVAR
91011
```
### 3.3. File /etc/environment
File /etc/environment được sử dụng để thiết lập biến môi trường ở mức hệ thống, áp dụng cho tất cả người dùng.
Ví dụ:
```bash
$ sudo nano /etc/environment
```
Thêm dòng:
```bash
MYVAR=121314
```
Lưu và kiểm tra:
```bash
$ echo $MYVAR
121314
```
## 4. Các Lệnh Hữu Ích Khác
### 4.1. env: Hiển Thị Tất Cả Biến Môi Trường
Lệnh env hiển thị danh sách tất cả các biến môi trường hiện có trong phiên làm việc.
Cách sử dụng:
```bash
$ env
```
Kết quả (một phần):
```bash
SHELL=/bin/bash
USER=user
PATH=/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin
PWD=/home/user
```
### 4.2. printenv: Hiển Thị Giá Trị Biến Môi Trường Cụ Thể
Để hiển thị giá trị của một biến môi trường cụ thể, bạn có thể sử dụng lệnh printenv.
Cách sử dụng:
```bash
$ printenv HOME
```
Kết quả:
```bash
/home/user
```
### 4.3. source: Áp Dụng Ngay Lập Tức Thay Đổi Trong File Cấu Hình
Khi bạn chỉnh sửa file .bashrc hoặc .bash_profile, thay vì đăng xuất và đăng nhập lại, bạn có thể sử dụng lệnh source để áp dụng ngay lập tức.
Cách sử dụng:
```bash
$ source ~/.bashrc
```
## 5. Các Ứng Dụng Thực Tế
### 5.1. Cấu Hình Biến PATH
Biến PATH chứa danh sách các thư mục mà hệ thống tìm kiếm lệnh thực thi. Bạn có thể thêm thư mục vào PATH để chạy các chương trình tùy chỉnh.
Ví dụ:
```bash
$ export PATH=$PATH:/custom/bin
```
Kiểm tra:
```bash
$ echo $PATH
/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin:/custom/bin
```
### 5.2. Thiết Lập Biến Môi Trường Cho Script
Các biến môi trường có thể được sử dụng để truyền tham số cho script shell.
Ví dụ:
```bash
$ export DB_USER=root
$ export DB_PASS=1234
$ ./myscript.sh
```
Trong file myscript.sh:
```bash
#!/bin/bash
echo "User: $DB_USER"
echo "Password: $DB_PASS"
```
Kết quả:
```bash
User: root
Password: 1234
```
# IV. Một Số Lệnh Giúp Kết Nối Và Quản Lý Server
Trong Linux, việc kết nối và quản lý server từ xa là một kỹ năng quan trọng, đặc biệt khi làm việc với các hệ thống phân tán hoặc quản trị máy chủ. Dưới đây là các lệnh cơ bản và ứng dụng thực tế giúp bạn làm chủ việc kết nối và quản lý server.

## 1. Kết Nối Tới Server Từ Xa
### 1.1. Kết Nối Bằng ssh
Lệnh ssh (Secure Shell) cho phép bạn đăng nhập vào một server từ xa một cách an toàn.
Cách sử dụng:
```bash
$ ssh user@server_address
```
Ví dụ:
```bash
$ ssh root@192.168.1.1
```
Kết quả:
```bash
root@192.168.1.1's password:
Welcome to Ubuntu 20.04 LTS (GNU/Linux 5.4.0-42-generic x86_64)
Last login: Tue May 13 19:30:00 2025 from 192.168.1.100
```
Để sử dụng một file khóa riêng tư cụ thể:
```bash
$ ssh -i /path/to/private_key.pem user@server_address
```
### 1.2. Tạo Khóa SSH Với ssh-keygen
Lệnh ssh-keygen được sử dụng để tạo cặp khóa SSH nhằm đăng nhập vào server mà không cần mật khẩu.
Cách sử dụng:
```bash
$ ssh-keygen -t rsa -b 4096 -C "your_email@example.com"
```
Kết quả:
```bash
Generating public/private rsa key pair.
Enter file in which to save the key (/home/user/.ssh/id_rsa):
```
Sau khi tạo khóa, bạn cần sao chép khóa công khai (id_rsa.pub) lên server:
```bash
$ ssh-copy-id user@server_address
```
### 1.3. Sao Chép File Tới Server Với scp
Lệnh scp (Secure Copy Protocol) cho phép bạn sao chép file hoặc thư mục giữa máy cục bộ và server từ xa.
Cách sử dụng:
```bash
$ scp file.txt user@server_address:/remote/path/
```
Ví dụ:
```bash
$ scp my_file.txt root@192.168.1.1:/home/root/
```
Kết quả:
```bash
my_file.txt                                      100%   12KB   12.0KB/s   00:01
```
Sao chép thư mục:
```bash
$ scp -r my_folder/ user@server_address:/remote/path/
```
## 2. Quản Lý Quyền Truy Cập File
### 2.1. Thay Đổi Quyền Truy Cập Với chmod
Lệnh chmod thay đổi quyền truy cập của file hoặc thư mục.
Cách sử dụng:
```bash
$ chmod 755 file.txt
```
Kết quả:
```bash
$ ls -l file.txt
-rwxr-xr-x 1 user group 0 May 13 19:00 file.txt
```
Ý nghĩa:
**7**: Quyền đọc, ghi, và thực thi (rwx) cho chủ sở hữu.
**5**: Quyền đọc và thực thi (r-x) cho nhóm.
**5** Quyền đọc và thực thi (r-x) cho người khác.
### 2.2. Thay Đổi Quyền Sở Hữu Với chown
Lệnh chown thay đổi quyền sở hữu file hoặc thư mục.
Cách sử dụng:
```bash
$ chown username:groupname file.txt
```
Ví dụ:
```bash
$ sudo chown root:admin file.txt
```
Kết quả:
```bash
$ ls -l file.txt
-rwxr-xr-x 1 root admin 0 May 13 19:00 file.txt
```
## 3. Nén Và Giải Nén File
### 3.1. Nén File/Thư Mục Với tar
Lệnh tar nén file hoặc thư mục thành một file .tar.
Cách sử dụng:
```bash
$ tar -cvf archive.tar folder/
```
Ví dụ:
```bash
$ tar -czvf archive.tar.gz folder/
```
Kết quả:
```bash
folder/
folder/file1.txt
folder/file2.txt
```
### 3.2. Giải Nén File tar
Để giải nén file .tar:
```bash
$ tar -xvf archive.tar
```
Để giải nén file .tar.gz:
```bash
$ tar -xzvf archive.tar.gz
```
## 4. Tải File Từ Internet
### 4.1. Tải File Với wget
Lệnh wget tải file từ Internet.
Cách sử dụng:
```bash
$ wget http://example.com/file.txt
```
Kết quả:
```bash
--2025-05-13 20:00:00--  http://example.com/file.txt
Resolving example.com (example.com)... 93.184.216.34
Connecting to example.com (example.com)|93.184.216.34|:80... connected.
HTTP request sent, awaiting response... 200 OK
Length: 12345 (12K) [text/plain]
Saving to: ‘file.txt’

file.txt                                      100%   12KB  --.-KB/s   00:01
```
### 4.2. Gửi Yêu Cầu HTTP Với curl
Lệnh curl gửi yêu cầu HTTP và nhận phản hồi.
Cách sử dụng:
```bash
$ curl http://example.com
```
Ví dụ:
```bash
$ curl -I http://example.com
```
Kết quả:
```bash
HTTP/1.1 200 OK
Date: Tue, 13 May 2025 20:00:00 GMT
Content-Type: text/html; charset=UTF-8
Content-Length: 1234
Connection: keep-alive
```
## 5. Các Ứng Dụng Thực Tế
### 5.1. Tự Động Hóa Sao Lưu
Sử dụng scp để tự động sao lưu file từ server về máy cục bộ:
```bash
$ scp user@server:/path/to/backup.tar.gz /local/path/
```
### 5.2. Quản Lý Người Dùng Trên Server
Sử dụng ssh để quản lý người dùng trên server:
```bash
$ ssh root@192.168.1.1
$ adduser newuser
$ passwd newuser
```
### 5.3. Tải Dữ Liệu Tự Động
Sử dụng wget hoặc curl trong script để tự động tải dữ liệu:
```bash
#!/bin/bash
wget http://example.com/daily_report.txt -O /local/path/report.txt
```
# V. Kiểm Tra Dung Lượng Disk Và RAM
Quản lý tài nguyên hệ thống như ổ đĩa và RAM là một phần quan trọng để đảm bảo hệ thống Linux hoạt động hiệu quả. Các lệnh dưới đây sẽ giúp bạn kiểm tra và quản lý dung lượng ổ đĩa và RAM một cách chi tiết.

## 1. Kiểm Tra Dung Lượng Ổ Đĩa
### 1.1. Hiển Thị Thông Tin Về Dung Lượng Ổ Đĩa Với df
Lệnh df (disk filesystem) hiển thị thông tin về dung lượng ổ đĩa đã sử dụng và còn trống trên các file hệ thống.
Cách sử dụng:
```bash
$ df
```
Kết quả:
```bash
Filesystem     1K-blocks    Used Available Use% Mounted on
/dev/sda1       488281250 1024000  487257250   1% /
tmpfs             2000000       0    2000000   0% /dev/shm
```
Hiển thị kết quả ở định dạng dễ đọc:
```bash
$ df -h
```
Kết quả:
```bash
Filesystem      Size  Used Avail Use% Mounted on
/dev/sda1        50G  1.0G   49G   2% /
tmpfs           2.0G     0  2.0G   0% /dev/shm
```
Hiển thị thông tin cho một thư mục cụ thể:
```bash
$ df -h /home
```
### 1.2. Kiểm Tra Dung Lượng File/Thư Mục Với du
Lệnh du (disk usage) hiển thị dung lượng của các file hoặc thư mục. Đây là công cụ hữu ích để xác định thư mục hoặc file nào đang chiếm nhiều không gian.
Cách sử dụng:
```bash
$ du -h file.txt
```
Kết quả:
```bash
4.0K    file.txt
```
Kiểm tra dung lượng của tất cả các thư mục con:
```bash
$ du -sh ./*
```
Kết quả:
```bash
1.5G    documents
500M    downloads
10M     pictures
```
Tìm tổng dung lượng của các file .txt trong thư mục:
```bash
$ find . -name "*.txt" -exec du -ch {} + | grep total
```
## 2. Kiểm Tra RAM
### 2.1. Hiển Thị Thông Tin RAM Với free
Lệnh free hiển thị thông tin về dung lượng RAM đã sử dụng, còn trống, và khả dụng.
Cách sử dụng:
```bash
$ free
```
Kết quả:
```bash
              total        used        free      shared  buff/cache   available
Mem:       16384000     4194304     8388608     1048576     3686400    12124160
Swap:       8388608           0     8388608
```
Hiển thị thông tin ở định dạng dễ đọc:
```bash
$ free -h
```
Kết quả:
```bash
total        used        free      shared  buff/cache   available
Mem: 16G 4G 8G 1G 3.5G 12G Swap: 8G 0B 8G
```
---
### 2.2. Giám Sát RAM Và CPU Với `top`

Lệnh `top` hiển thị thông tin thời gian thực về việc sử dụng tài nguyên của hệ thống, bao gồm RAM và CPU.

Cách sử dụng:
```bash
$ top
```
Kết quả:
```bash
top - 20:00:00 up  1:00,  2 users,  load average: 0.01, 0.05, 0.00
Tasks:  98 total,   1 running,  97 sleeping,   0 stopped,   0 zombie
%Cpu(s):  0.1 us,  0.3 sy,  0.0 ni, 99.5 id,  0.0 wa,  0.0 hi,  0.1 si,  0.0 st
MiB Mem :   16000.0 total,   4000.0 used,   8000.0 free,   1000.0 shared,   3500.0 buff/cache
MiB Swap:    8000.0 total,      0.0 used,   8000.0 free
```
Để thoát khỏi top, nhấn **`q`**.
### 2.3. Sử Dụng htop
Lệnh htop là một phiên bản nâng cao của top, hiển thị thông tin chi tiết hơn và giao diện thân thiện hơn.
Cách sử dụng:
```bash
$ htop
```
Sử dụng các phím mũi tên để điều hướng.
Nhấn **`F9`** để dừng một tiến trình.
## 3. Các Ứng Dụng Thực Tế
### 3.1. Dọn Dẹp Dữ Liệu Không Cần Thiết
Sử dụng du để xác định các thư mục hoặc file chiếm nhiều dung lượng, sau đó xóa chúng bằng rm:
```bash
$ du -sh /var/*
```
Xóa các file log cũ:
```bash
$ find /var/log -name "*.log" -type f -mtime +30 -exec rm -f {} +
```
### 3.2. Giám Sát Hệ Thống
Sử dụng top hoặc htop để giám sát tài nguyên hệ thống và tìm các tiến trình tiêu tốn nhiều RAM hoặc CPU.
Ví dụ:
```bash
$ top
```
Dừng một tiến trình tiêu tốn nhiều tài nguyên:
```bash
$ kill -9 <PID>
```
### 3.3. Tự Động Kiểm Tra Dung Lượng Đĩa
Sử dụng df và du trong script để tự động kiểm tra dung lượng ổ đĩa:
```bash
#!/bin/bash
df -h > disk_usage.txt
du -sh /home/* >> disk_usage.txt
```
# VI. Quản Lý Service Đang Chạy, Ping, Quản Lý, Và Chặn IP, Port
Trong quá trình quản trị hệ thống Linux, việc quản lý các service, kiểm tra kết nối mạng và xử lý các vấn đề liên quan đến IP hoặc port là rất quan trọng. Các lệnh dưới đây sẽ giúp bạn thực hiện các nhiệm vụ này một cách hiệu quả.

## 1. Quản Lý Service Và Tiến Trình
### 1.1. Hiển Thị Các Service Đang Chạy Với ps
Lệnh ps hiển thị danh sách các tiến trình (process) đang chạy trên hệ thống.
Cách sử dụng:
```bash
$ ps aux
```
Kết quả:
```bash
USER       PID %CPU %MEM    VSZ   RSS TTY      STAT START   TIME COMMAND
root         1  0.0  0.1 169068  1232 ?        Ss   May13   0:00 /sbin/init
root       123  0.0  0.0  29568   556 ?        Ss   May13   0:00 /usr/sbin/cron
user      1010  0.0  0.2  63132  2348 ?        S    May13   0:00 /usr/bin/python3 script.py
```
Lọc tiến trình cụ thể:
```bash
$ ps aux | grep nginx
```
### 1.2. Dừng Tiến Trình Bằng kill
Lệnh kill được sử dụng để dừng một tiến trình dựa trên PID (Process ID).
Cách sử dụng:
```bash
$ kill -9 <PID>
```
Ví dụ:
```bash
$ kill -9 1010
```
### 1.3. Liệt Kê Các File Đang Mở Với lsof
Lệnh lsof hiển thị danh sách các file đang được mở bởi các tiến trình.
Cách sử dụng:
```bash
$ lsof
```
Hiển thị các cổng đang mở:
```bash
$ lsof -i
```
Kiểm tra cổng cụ thể:
```bash
$ lsof -i :8080
```
Kết quả:
```bash
COMMAND   PID USER   FD   TYPE DEVICE SIZE/OFF NODE NAME
python   1010 user   40u  IPv4  12345      0t0  TCP *:8080 (LISTEN)
```
## 2. Kiểm Tra Kết Nối Mạng
### 2.1. Kiểm Tra Kết Nối Mạng Với ping
Lệnh ping được sử dụng để kiểm tra kết nối mạng giữa máy cục bộ và một host từ xa.
Cách sử dụng:
```bash
$ ping google.com
```
Kết quả:
```bash
PING google.com (142.250.72.206): 56 data bytes
64 bytes from 142.250.72.206: icmp_seq=0 ttl=118 time=10.102 ms
```
Dừng lệnh ping bằng cách nhấn **`Ctrl + C`**.
## 2.2. Kiểm Tra Cổng Với nc (Netcat)
Lệnh nc kiểm tra xem một cổng cụ thể trên máy từ xa có mở không.
Cách sử dụng:
```bash
$ nc -vz 192.168.1.1 80
```
Kết quả:
```bash
Connection to 192.168.1.1 80 port [tcp/http] succeeded!
```
## 3. Quản Lý IP Và Port
### 3.1. Kiểm Tra Địa Chỉ IP Với ifconfig
Lệnh ifconfig hiển thị thông tin về các giao diện mạng đang hoạt động.
Cách sử dụng:
```bash
$ ifconfig
```
Kết quả:
```bash
eth0: flags=4163<UP,BROADCAST,RUNNING,MULTICAST>  mtu 1500
        inet 192.168.1.100  netmask 255.255.255.0  broadcast 192.168.1.255
        inet6 fe80::1e4a:34ff:fe8d:5e9b  prefixlen 64  scopeid 0x20<link>
        RX packets 123456  bytes 123456789 (123.4 MB)
        TX packets 654321  bytes 987654321 (987.6 MB)
```
## 3.2. Quản Lý Tường Lửa Với iptables
Lệnh iptables được sử dụng để quản lý tường lửa và kiểm soát lưu lượng mạng.
Chặn một IP cụ thể:
```bash
$ sudo iptables -A INPUT -s 192.168.1.100 -j DROP
```
Cho phép một IP cụ thể:
```bash
$ sudo iptables -A INPUT -s 192.168.1.100 -j ACCEPT
```
Kiểm tra quy tắc tường lửa:
```bash
$ sudo iptables -L
```
## 4. Các Ứng Dụng Thực Tế
### 4.1. Dừng Dịch Vụ Chiếm Cổng
Khi một cổng bị chiếm bởi một tiến trình không mong muốn, bạn có thể sử dụng lsof và kill để dừng tiến trình đó.
Ví dụ:
```bash
$ sudo lsof -i :8080
COMMAND   PID USER   FD   TYPE DEVICE SIZE/OFF NODE NAME
python   1010 user   40u  IPv4  12345      0t0  TCP *:8080 (LISTEN)

$ sudo kill -9 1010
```
### 4.2. Kiểm Tra Kết Nối Mạng Tới Server
Sử dụng ping để kiểm tra xem server có hoạt động hay không:
```bash
$ ping 192.168.1.1
```

### 4.3. Chặn Truy Cập Không Mong Muốn
Sử dụng iptables để chặn một địa chỉ IP gây hại:
```bash
$ sudo iptables -A INPUT -s 203.0.113.1 -j DROP
```

# VII. Phục Vụ Monitoring Logs
Logs là một phần quan trọng trong việc giám sát và quản lý hệ thống Linux. Bằng cách theo dõi logs, bạn có thể phát hiện và xử lý các lỗi, giám sát hoạt động và đảm bảo hệ thống hoạt động ổn định. Dưới đây là các lệnh phổ biến để làm việc với logs.

## 1. Tìm Kiếm Logs
### 1.1. Tìm File Logs Với find
Lệnh find cho phép bạn tìm kiếm các file logs dựa trên tên hoặc thuộc tính của chúng.
Cách sử dụng:
```bash
$ find /var/log/ -name "*.log"
```
Kết quả:
```bash
/var/log/syslog.log
/var/log/auth.log
/var/log/dpkg.log
```
Tìm file logs lớn hơn 10MB:
```bash
$ find /var/log/ -name "*.log" -size +10M
```
### 1.2. Tìm Dữ Liệu Trong Logs Với grep
Lệnh grep giúp bạn tìm kiếm các dòng chứa từ khóa cụ thể trong file logs.
Cách sử dụng:
```bash
$ grep "error" /var/log/syslog
```
Kết quả:
```bash
May 13 19:00:00 server systemd[1]: Failed to start MyService.service: Unit not found.
May 13 19:01:00 server kernel: [12345.678910] error: Unable to allocate memory.
```
Tìm kiếm không phân biệt hoa thường:
```bash
$ grep -i "error" /var/log/syslog
```
Tìm kiếm từ khóa trong tất cả các file logs:
```bash
$ grep -r "error" /var/log/
```
## 2. Xem Logs
### 2.1. Hiển Thị Logs Với cat
Lệnh cat được sử dụng để hiển thị toàn bộ nội dung file logs.
Cách sử dụng:
```bash
$ cat /var/log/syslog
```
### 2.2. Xem Logs Theo Trang Với less
Lệnh less giúp bạn xem nội dung logs theo từng trang, đặc biệt hữu ích khi file logs quá dài.
Cách sử dụng:
```bash
$ less /var/log/syslog
```
Phím tắt khi sử dụng less:
- Nhấn `Space`: Chuyển sang trang tiếp theo.
- Nhấn `q`: Thoát khỏi less.
### 2.3. Xem Logs Cuối File Với tail
Lệnh tail hiển thị các dòng cuối của file logs.
Cách sử dụng:
```bash
$ tail /var/log/syslog
```
Kết quả:
```bash
May 13 19:00:00 server systemd[1]: Starting Update UTMP about System Runlevel Changes...
May 13 19:00:00 server systemd[1]: Stopped target Graphical Interface.
May 13 19:00:00 server systemd[1]: Stopping Update UTMP about System Runlevel Changes...
```
Hiển thị 20 dòng cuối:
```bash
$ tail -n 20 /var/log/syslog
```
Theo dõi logs trong thời gian thực:
```bash
$ tail -f /var/log/syslog
```
## 3. Thao Tác Với Nội Dung Logs
### 3.1. Chỉnh Sửa Logs Với sed
Lệnh sed cho phép bạn chỉnh sửa hoặc thao tác nội dung logs trực tiếp.
Cách sử dụng:
Loại bỏ dòng chứa từ "debug":
```bash
$ sed '/debug/d' /var/log/syslog
```
Thay thế từ "error" bằng "ERROR":
```bash
$ sed 's/error/ERROR/g' /var/log/syslog
```
### 3.2. Kết Hợp Logs Với awk
Lệnh awk giúp bạn trích xuất và định dạng dữ liệu từ file logs.
Cách sử dụng:
Trích xuất cột đầu tiên (thời gian) và cột cuối cùng (nội dung):
```bash
$ awk '{print $1, $NF}' /var/log/syslog
```
Kết quả:
```bash
May ERROR
May Changes...
May Interface.
```
## 4. Các Ứng Dụng Thực Tế
### 4.1. Theo Dõi Logs Thời Gian Thực
Sử dụng tail -f để theo dõi logs khi chúng được ghi thêm:
```bash
$ tail -f /var/log/syslog
```
Điều này rất hữu ích khi bạn muốn kiểm tra hoạt động của các service hoặc ứng dụng.
### 4.2. Lọc Logs Theo Từ Khóa
Sử dụng grep để chỉ lấy những dòng logs có chứa từ khóa quan trọng:
```bash
$ grep "failed" /var/log/auth.log
```
### 4.3. Xóa Các Logs Cũ
Để dọn dẹp logs cũ, bạn có thể sử dụng find kết hợp với rm:
```bash
$ find /var/log/ -name "*.log" -mtime +30 -exec rm -f {} +
```
# VIII. Xem Thông Tin Và Trợ Giúp Về Lệnh
Khi làm việc với Linux, việc hiểu cách sử dụng các lệnh và tùy chọn của chúng là điều rất quan trọng. May mắn thay, hệ thống Linux cung cấp các công cụ mạnh mẽ giúp bạn tra cứu và học hỏi ngay trên terminal. Dưới đây là các lệnh phổ biến để xem thông tin và trợ giúp.
## 1. Xem Hướng Dẫn Sử Dụng Bằng man
### 1.1. man Là Gì?
Lệnh man (manual) hiển thị tài liệu hướng dẫn chi tiết về một lệnh cụ thể. Đây là công cụ quan trọng để tìm hiểu về chức năng, cú pháp, và các tùy chọn của lệnh.
Cách sử dụng:
```bash
$ man <command>
```
Ví dụ:
```bash
$ man ls
```
Kết quả:
```bash
LS(1)                     User Commands                    LS(1)

NAME
       ls - list directory contents

SYNOPSIS
       ls [OPTION]... [FILE]...

DESCRIPTION
       List  information  about the FILEs (the current directory by default).
       Sort entries alphabetically if none of -cftuvSUX nor --sort is specified.
```
Di chuyển trong man:
- Nhấn `Space`: Chuyển sang trang tiếp theo.
- Nhấn `q`: Thoát khỏi man.
### 1.2. Tìm Kiếm Với man -k
Bạn có thể tìm kiếm các lệnh liên quan đến một từ khóa bằng cách sử dụng tùy chọn `-k`.
Cách sử dụng:
```bash
$ man -k keyword
```
Ví dụ:
```bash
$ man -k copy
```
Kết quả:
```bash
cp (1)               - copy files and directories
scp (1)              - secure copy (remote file copy program)
```
## 2. Xem Mô Tả Ngắn Gọn Bằng whatis
### 2.1. whatis Là Gì?
Lệnh whatis hiển thị mô tả ngắn gọn về một lệnh cụ thể. Đây là cách nhanh chóng để nắm bắt ý nghĩa của một lệnh.
Cách sử dụng:
```bash
$ whatis <command>
```
Ví dụ:
```bash
$ whatis ls
```
Kết quả:
```bash
ls (1)               - list directory contents
```
## 3. Xác Định Vị Trí File Thực Thi Bằng which
### 3.1. which Là Gì?
Lệnh which cho biết vị trí của file thực thi tương ứng với một lệnh. Điều này hữu ích để kiểm tra phiên bản lệnh nào đang được sử dụng.
Cách sử dụng:
```bash
$ which <command>
```
Ví dụ:
```bash
$ which python
```
Kết quả:
```bash
/usr/bin/python
```
## 4. Xem Tài Liệu Chi Tiết Bằng info
### 4.1. info Là Gì?
Lệnh info cung cấp tài liệu chi tiết hơn so với man, bao gồm các ví dụ và giải thích chi tiết về cách sử dụng lệnh.
Cách sử dụng:
```bash
$ info <command>
```
Ví dụ:
```bash
$ info ls
```
Kết quả:
```bash
File: coreutils.info,  Node: ls invocation,  Next: dir invocation,  Up: Directory listing
6.1 `ls': List directory contents
=================================

     The `ls' program lists information about files (of any type,
     including directories).  Options and file arguments can be mixed
     arbitrarily, as usual.
```
Di chuyển trong info:
- Nhấn `n`: Chuyển sang phần tiếp theo.
- Nhấn `p`: Quay lại phần trước đó.
- Nhấn `q`: Thoát khỏi info.
## 5. Các Lệnh Hữu Ích Khác
### 5.1. Hiển Thị Lệnh Đã Sử Dụng Trước Đó Bằng history
Lệnh history hiển thị danh sách các lệnh đã nhập vào terminal trước đó.
Cách sử dụng:
```bash
$ history
```
Ví dụ:
```bash
1  ls
2  cd /home
3  mkdir test_folder
4  history
```
Tìm kiếm lịch sử lệnh:
```bash
$ history | grep ls
```
### 5.2. Tìm Nhanh Một Lệnh Bằng Ctrl + R
Bạn có thể tìm kiếm một lệnh trong lịch sử bằng cách nhấn Ctrl + R và nhập từ khóa.
Ví dụ:
```bash
(reverse-i-search)`ls': ls -l
```
## 6. Các Ứng Dụng Thực Tế
### 6.1. Tra Cứu Tài Liệu Nhanh
Khi bạn không nhớ cách sử dụng một lệnh, hãy sử dụng man để tra cứu:
```bash
$ man grep
```
### 6.2. Kiểm Tra Vị Trí Lệnh
Khi bạn có nhiều phiên bản của một chương trình, sử dụng which để kiểm tra vị trí của lệnh đang chạy:
```bash
$ which python3
```

### 6.3. Xem Mô Tả Lệnh Đơn Giản
Khi cần mô tả ngắn gọn, sử dụng whatis:
```bash
$ whatis chmod
```
# IX. Quản Lý Gói Cài Đặt Trên Linux
Quản lý gói cài đặt là một phần quan trọng trong việc duy trì và vận hành hệ thống Linux. Các công cụ quản lý gói như apt, yum, dnf, và snap giúp bạn dễ dàng cài đặt, cập nhật, gỡ bỏ, và tìm kiếm các phần mềm một cách hiệu quả.

## 1. Quản Lý Gói Trên Hệ Thống Dựa Trên Debian/Ubuntu Với apt
### 1.1. Cập Nhật Danh Sách Gói
Lệnh apt update được sử dụng để làm mới danh sách các gói cài đặt từ các kho lưu trữ.
Cách sử dụng:
```bash
$ sudo apt update
```
Kết quả:
```bash
Hit:1 http://archive.ubuntu.com/ubuntu focal InRelease
Hit:2 http://security.ubuntu.com/ubuntu focal-security InRelease
Reading package lists... Done
```
### 1.2. Nâng Cấp Các Gói Đã Cài Đặt
Sử dụng apt upgrade để nâng cấp tất cả các gói hiện có trên hệ thống lên phiên bản mới nhất.
Cách sử dụng:
```bash
$ sudo apt upgrade
```
Kết quả:
```bash
The following packages will be upgraded:
  libssl1.1 openssl
2 upgraded, 0 newly installed, 0 to remove and 0 not upgraded.
Do you want to continue? [Y/n]
```
### 1.3. Cài Đặt Một Gói Mới
Lệnh apt install được sử dụng để cài đặt một gói mới từ kho lưu trữ.
Cách sử dụng:
```bash
$ sudo apt install <package_name>
```
Ví dụ:
```bash
$ sudo apt install curl
```
### 1.4. Xóa Gói Cài Đặt
Sử dụng apt remove để gỡ bỏ một gói cài đặt khỏi hệ thống, nhưng vẫn giữ lại file cấu hình.
Cách sử dụng:
```bash
$ sudo apt remove <package_name>
```
Ví dụ:
```bash
$ sudo apt remove curl
```
### 1.5. Xóa Hoàn Toàn Một Gói
Lệnh apt purge xóa gói cài đặt cùng với tất cả các file cấu hình của nó.
Cách sử dụng:
```bash
$ sudo apt purge <package_name>
```
Ví dụ:
```bash
$ sudo apt purge curl
```
## 2. Quản Lý Gói Trên Hệ Thống Dựa Trên Red Hat/CentOS Với yum hoặc dnf
### 2.1. Cập Nhật Danh Sách Gói Với yum/dnf
Cách sử dụng:
```bash
$ sudo yum update
# Hoặc
$ sudo dnf update
```
### 2.2. Cài Đặt Gói Mới Với yum/dnf
Cách sử dụng:
```bash
$ sudo yum install <package_name>
# Hoặc
$ sudo dnf install <package_name>
```
Ví dụ:
```bash
$ sudo dnf install git
```
### 2.3. Xóa Gói Cài Đặt Với yum/dnf
Cách sử dụng:
```bash
$ sudo yum remove <package_name>
# Hoặc
$ sudo dnf remove <package_name>
```
## 3. Quản Lý Gói Snap Với snap
### 3.1. Cài Đặt Gói Từ Snap
Cách sử dụng:
```bash
$ sudo snap install <package_name>
```
Ví dụ:
```bash
$ sudo snap install vscode --classic
```
### 3.2. Xóa Gói Snap
Cách sử dụng:
```bash
$ sudo snap remove <package_name>
```
Ví dụ:
```bash
$ sudo snap remove vscode
```
## 4. Tìm Kiếm Gói
### 4.1. Tìm Gói Với apt
Cách sử dụng:
```bash
$ apt search <package_name>
```
Ví dụ:
```bash
$ apt search nginx
```
### 4.2. Tìm Gói Với yum/dnf
Cách sử dụng:
```bash
$ yum search <package_name>
# Hoặc
$ dnf search <package_name>
```
Ví dụ:
```bash
$ dnf search httpd
```
## 5. Các Ứng Dụng Thực Tế
### 5.1. Cài Đặt Web Server
Cài đặt Nginx trên hệ thống dựa trên Ubuntu:
```bash
$ sudo apt update
$ sudo apt install nginx
```
Khởi động Nginx:
```bash
$ sudo systemctl start nginx
$ sudo systemctl enable nginx
```
### 5.2. Cập Nhật Toàn Bộ Hệ Thống
Sử dụng apt hoặc yum/dnf để cập nhật toàn bộ hệ thống:
```bash
$ sudo apt update && sudo apt upgrade -y
# Hoặc
$ sudo dnf update -y
```
# Kết Luận
Việc nắm vững các lệnh cơ bản và nâng cao trong Linux không chỉ giúp bạn làm việc hiệu quả hơn mà còn tăng khả năng xử lý sự cố. Hãy thực hành thường xuyên để thành thạo các lệnh này.