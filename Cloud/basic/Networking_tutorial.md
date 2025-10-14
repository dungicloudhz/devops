Hiểu OSI vs TCP/IP (mỗi tầng làm gì) và các khái niệm cơ bản: **IP, MAC, Port, DNS, Gateway, Subnet**.

# 1. OSI vs TCP/IP - nhìn tổng quan dễ nhớ.
OSI 7 tầng - ý tưởng
- **Tầng 1 - Physical (Vật lý)**: truyền bit qua dây, cáp, sóng.(Ví dụ Ethernet, sóng Wifi)
- **Tầng 2 - Data link (Liên kết dữ liệu)**: đóng khung (frame), quản lý MAC, phát hiện lỗi khung.(Ví dụ: Ethernet, ARP).
- **Tầng 3 - Network (Mạng)**: định tuyến package giữa các mạng, địa chỉ IP. (Ví dụ: IP, ICMP)
- **Tầng 4 - Transport (Giao vận)**: đảm bảo/ tách luồng cho ứng dụng - TCP (reliable) hoặc UDP (fast).
- **Tầng 5 - Session (Phiên)**: quản lý phiên làm việc giữa ứng dụng (ít dùng trực tiếp).
- **Tầng 6 - Presentation (Trình diễn)**: mã hóa, nén, chuyển đổi dữ liệu (Ví dụ: TLS, mã hóa).
- **Tầng 7 - Application (Ứng dụng)**: giao tiếp với ứng dụng người dùng (HTTP, DNS, SMTP).

