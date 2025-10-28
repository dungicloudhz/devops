Hiểu OSI vs TCP/IP (mỗi tầng làm gì) và các khái niệm cơ bản: **IP, MAC, Port, DNS, Gateway, Subnet**.

# 1. OSI vs TCP/IP - nhìn tổng quan dễ nhớ.
## OSI 7 tầng - ý tưởng
- **Tầng 1 - Physical (Vật lý)**: truyền bit qua dây, cáp, sóng.(Ví dụ Ethernet, sóng Wifi)
- **Tầng 2 - Data link (Liên kết dữ liệu)**: đóng khung (frame), quản lý MAC, phát hiện lỗi khung.(Ví dụ: Ethernet, ARP).
- **Tầng 3 - Network (Mạng)**: định tuyến package giữa các mạng, địa chỉ IP. (Ví dụ: IP, ICMP)
- **Tầng 4 - Transport (Giao vận)**: đảm bảo/ tách luồng cho ứng dụng - TCP (reliable) hoặc UDP (fast).
- **Tầng 5 - Session (Phiên)**: quản lý phiên làm việc giữa ứng dụng (ít dùng trực tiếp).
- **Tầng 6 - Presentation (Trình diễn)**: mã hóa, nén, chuyển đổi dữ liệu (Ví dụ: TLS, mã hóa).
- **Tầng 7 - Application (Ứng dụng)**: giao tiếp với ứng dụng người dùng (HTTP, DNS, SMTP)

## TCP/IP (4 tầng) - mô hình thực tế
- **Application** = tầng 5-7 của OSI (HTTP, DNS, SSH...)
- **Transport** = tầng 4 (TCP/IP)
- **Internet** = tầng 3 (IP)
- **Network Interface** = tầng 1-2 (Ethernet, Wifi)

**Tư duy**: dùng OSI để hiểu nhiệm vụ từng lớp, dùng TCP/IP để hiểu cách internet thật sự hoạt động.

# 2. Mỗi tầng làm gì - minh họa bằng "gửi thư"
Khi bạn gửi dữ liệu từ trình duyệt (Application):
- Ứng dụng (HTTP) tạo nội dung thư.
- Transport (TCP) chia thành các đoạn (segments), đánh số (sequence), yêu cầu ACK.
- Internet (IP) bọc mỗi segment vào packet có địa chỉ nguồn/đích IP.
- Data Link (Ethernet) thêm header MAC, thành frame.
- Physical chuyển các bit đi trên cáp/sóng.

Ở đầu nhận, quy trình **mở gói (decapsulation)** theo thứ tự ngược lại.

# 3. IP - địa chỉ mạng (chi tiết dễ hiểu)
- **IP (IPv4)** là địa chỉ 32-bit, viết thành 4 octet thập phân (ví dụ `192.168.1.10`)
- IP định tuyến packet qua các mạng. Router sử dụng IP để quyết định "bước tiếp theo" (next hop).

Ví dụ thực tế: `192.168.1.10` là "địa chỉ nhà".
Lệnh kiểm tra:
- Windows: `ipconfig /all`
- Linux: `ip addr show`

# 4. MAC - địa chỉ vật lý trên LAN
- **MAC (Media Access Control)** là địa chỉ 48-bit gán cho NIC, dạng hexa như `00:1A:2B:3C:4D:SE`.
- Switch chuyển frame dựa trên **MAC** (chỉ trong cùng LAN).
- Khi gửi từ IP đến IP trong cùng LAN, máy gửi tìm MAC bằng **ARP** (Address Resolution Protocol).

**Lệnh kiểm tra ARP table:**
- Windows: `arp -a`
- Linux: `ip neigh` hoặc `arp -n`

# 5. Port - "cửa" của ứng dụng
- **Port** là số 16-bit (0-65535) để phân biệt ứn dụng trên cùng IP.
    - Well-known: 0-1023 (HTTP 80, HTTPS 443, SSH 22, DNS 53)
    - Ephemeral: thường > 1024 (client side)
- Transport layer dùng port để giao tiếp endpoint: **(IP, Port)** là địa chỉ hoàn chỉnh cho socket.

**Lệnh xem port đang nghe:**
- Windows: `netstat -an`
- Linux: `ss -tuln`

# 6. DNS - "danh bạ" của Internet
- **DNS** chuyển tên miền (ví dụ `google.com`) → địa chỉ IP.
- Khi bạn nhập teen trang, máy hỏi DNS resolver (ÍP hoặc publich DNS).
- DNS là dịch vụ tầng Application.
