# I. Giới thiệu về Terminal Linux

## Terminal là gì?
Terminal (hay còn gọi là **CLI - Command Line Interface**) là một công cụ mạnh mẽ trên Linux, cho phép người dùng tương tác trực tiếp với hệ điều hành thông qua các dòng lệnh.  
Không giống như giao diện đồ họa (GUI), Terminal chỉ sử dụng văn bản để nhập liệu và hiển thị kết quả, từ đó mang lại sự kiểm soát chi tiết và linh hoạt hơn đối với hệ thống.

## Vì sao Terminal quan trọng?
1. **Tính linh hoạt**: Terminal cho phép bạn truy cập và cấu hình tất cả các thành phần của hệ thống, điều mà GUI không thể làm.  
2. **Hiệu quả cao**: Với Terminal, các tác vụ phức tạp có thể thực hiện nhanh chóng chỉ với một vài lệnh.  
3. **Tự động hóa**: Bạn có thể viết script để tự động hóa các công việc lặp đi lặp lại.  
4. **Khả năng tương thích**: Hầu hết các máy chủ Linux đều không có giao diện đồ họa, vì vậy việc sử dụng Terminal là cần thiết.  

---

## Các lệnh cơ bản trong Terminal
Dưới đây là một số lệnh cơ bản giúp bạn làm quen với Terminal.

### 1. `history` : Xem lịch sử các lệnh đã chạy
Lệnh `history` hiển thị danh sách các lệnh mà bạn đã nhập vào Terminal trước đó.  
Điều này giúp bạn dễ dàng theo dõi và sử dụng lại các lệnh đã chạy.  

**Cách sử dụng**:
```bash
$ history
```
Kết quả:
```bash
15  ssh user@192.168.1.1
```
Xóa lịch sử lệnh:
```bash
$ history -c
```
### 2. `clear` : Xóa màn hình terminal
Lệnh `clear` giúp xóa toàn bộ nội dung trên màn hình Terminal.
**Cách sử dụng**:
```bash
$ clear
```
Kết quả: Màn hình Terminal sẽ sạch và chỉ còn lại ký hiệu `$`.

### 3. `exit` : Thoát khỏi Terminal

Lệnh `exit` dùng để thoát khỏi phiên làm việc hiện tại hoặc đăng xuất khỏi Terminal.
**Cách sử dụng**:
```bash
$ exit
```
**Kết quả**: Bạn sẽ thoát khỏi phiên làm việc hiện tại. Nếu đang sử dụng SSH, lệnh này cũng sẽ kết thúc kết nối SSH.