# Lộ trình & hướng dẫn học mạng cho người mới - chi tiết dễ hiểu.
— Mình sẽ đưa bạn đi từ **khái niệm cơ bản → thực hành lệnh → lab chân tay → mẹo debug.** Mỗi phần kèm ví dụ trực quan, câu lệnh thực tế, bài tập nhỏ để làm ngay.

# 1. Tóm tắt ngắn gọn (về mặt tư duy)
- **Mạng = hai (hoặc nhiều) máy tính trao đổi dữ liệu**.
- **IP là địa chỉ nhà, MAC = địa chỉ cửa, Port = của phòng (ứng dụng)**.
- Dữ liệu khi gửi đi **bị "đóng gói"** nhiều lớp (Application → Transport → Network → DataLink → Physical).
- **Switch** làm việc ở tầng 2 (MAC), **Router** ở  tầng 3 (IP).
- **TCP**: đáng tin cậy (ACK/retransmit). **UDP**: nhanh không đảm bảo.

# 2. Lộ trình dành cho người mới
## Tuần 1 - Cơ bản và tư duy
- Hiểu OSI & TCP (mỗi tầng làm gì).
- Khái niệm IP, MAC, Port, DNS, Gateway, Subnet.
- Bài tập: đọc `ipconfig`/ `ifconfig` và `ping` với gateway.
## Tuần 2 - Địa chỉ và định tuyến
- Subnetting (CIDR) cơ bản: /24, /16, /8, cách chia mạng.
- Router và Switch vs Hub; ARP (làm sao IP → MAC).
- Bài tập: đặt IP tĩnh cho 2 máy ảo, ping, kiểm tra `arp -a`.
## Tuần 3 - Transport và dịch vụ
- TCP vs UDP, cách dùng `netstat`/ `ss` đẻ xem port.
- HTTP, HTTPS, DNS, SSH - kiểm thử với `curl`, `telnet`.
- Bài tập: chạy máy chủ web đơn giản (`python -m http.server`) và truy cập từ máy khác.
## Tuần 4 - Ghi bắt & debug
- Dùng `traceroute`/ `tracert`, `tcpdump`, Wireshark cơ bản.
- Checklist debug mạng (cable → link → IP → route → DNS → app).
- Mini project: dựng 2 mạng VM + 1 route ảo, capture traffic, đọc packet.

# 3. Lý giải dễ hiểu bằng ví dụ và phép ẩn dụ
**Ẩn dụ bưu điện:**
Bạn gửi một thư (dữ liệu):
- Envelope (Transport header) ghi người nhận phòng (port) và dấu nhận (sequence).
- Gửi tới bưu cục (router) theo địa chỉ nhà (IP).
- Trong khu trung cư (LAN), gác cổng (switch) đọc thẻ cư dân (MAC) để phân phối thư vào đúng căn hộ.
**Encapsulation** (Đóng gói): 
Ứng dụng → TCP header → IP header → Ethernet header → đây.
Bên nhận mở ngược lại theo thứ tự.

# 4. Các lệnh cơ bản (Windows/ Linux)
## 1. Xem cấu hình mạng
- Window:
```bash
ipconfig /all
```
- Linux:
```bash
ip addr  show
# hoặc (cũ) ifconfig
```
## 2. Kiểm tra kết nối ICMP (ping)
- Window
```bash
ping 8.8.8.8
```
- Linux:
```bash
ping -c 4 8.8.8.8
```
**Ý nghĩa kết quả**: thời gian (latency), TLL (số hop còn lại) - nếu timeout: không kết nối tới IP đó.

## 3. Truy vết đường đi (traceroute)
- Windows:
```bash
tracert google.com
```
- Linux:
```bash
traceroute google.com
```
**Ý nghĩ**: nhìn thấy từng router trung gian (mỗi hop là 1 router).

## 4. Kiểm tra bảng ARP (IP → MAC)
- Windows:
```bash
arp -a
```
- Linux:
```bash
ip neigh
# hoặc arp -n
```
## 5. Xem kết nối & Port (listening)
- Windows:
```bash
netstat -an
```
- Linux:
```bash
ss -tuln
# hoặc netstat -tuln (nếu có)
```
**Hiểu**: cột PORT cho biết service đang lắng nghe (LISTEN) = ứng dụng sẵn sàng kết nối.

## 6. Kiểm tra DNS
- Windows:
```bash
nslookup example.com
```
- Linux:
```bash
dig example.com
# nếu không có dig: sudo apt install dnsutils
```

## 7. Lấy HTTP header (kiểm app)
```bash
curl -I http://example.com
# hoặc
curl -v http://exapmle.com
```
## 8. Bắt gói tin (capture)
- Dùng tcpdump (Linux):
```bash
sudo tcpdump -i eth0 -n -c 50 icmp
```
- Wireshark: giao diện GUI, filter ví dụ: `tcp`, `http`, ip.addr==192.168.1.5

# 5. Lab thực hành từng bước (làm tại nhà với 2 máy ảo hoặc 2 máy thật trong mạng LAN)
**Lab A — Kiểm tra liên lạc cơ bản**
1. Máy A: 192.168.56.101/24, Máy B: 192.168.56.102/24 (cùng mạng).
2. ping 192.168.56.102 từ A → nếu ok, thành công.
3. arp -a trên A → thấy MAC của B chưa? (hiện khi ping)
4. Dùng tcpdump trên B: sudo tcpdump -i eth0 icmp → thấy echo requests.

**Lab B — HTTP server nhỏ**
1. Trên Máy B chạy:
```bash
python3 -m http.server 8000
```
2. Từ A: curl http://192.168.56.102:8000 → kiểm tra nhận nội dung.
3. Dùng Wireshark lọc tcp.port==8000 để xem handshake TCP + HTTP GET.

**Lab C — Traceroute & route**
1. Từ A: traceroute 8.8.8.8 → phân tích số hop, nếu * * * ở một hop thì router chặn ICMP.
2. Xem routing table: ip route show (Linux) hoặc route print (Windows).

# 6. Checklist debug khi mạng "bị lỗi" (theo thứ tự)
1. **Vật lý**: cáp, đèn port, Wi-Fi bật?
2. **Link layer**: `ip addr` có IP? `ip link` trạng thái UP?
3. **Ping local**: ping localhost, ping gateway.
4. **ARP**: `arp -a` xem mapping IP → MAC.
5. **Route**: `ip route show` có default gateway không?
6. **DNS**: `nslookup example.com` trả về IP không?
7. **Port/App**: `ss -tuln` để xem dịch vụ đã lắng nghe?
8. **Package capture**: tcpdump/Wireshark để thấy gói có tới/ không.
9. **Logs**: server/ app logs (Nginx, systemd, firewall).
10. **Firewall/NAT**: kiểm tra iptables/ufw/windows firewall.

# 7. Cheat-sheet (ngắn gọn) - những thứ phải nhớ
- **Common ports**: HTTP 80, HTTPS 443, SSH 22, DNS 53, SMTP 25, MySQL 3026.
- **Commands**: `ping`, `traceroute/tracert`, `ip addr`, `ip route`, `arp -a`, `ss/netstat`, `tcpdump`, `wireshark`, `curl`, `dig/nslookup`.
- **Mnemonics OSI**: Please Do Not Throw Sausage Pizza Away (Physical → Data link → Network → Transport → Session → Presentation → Application).