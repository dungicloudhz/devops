# 1. IP là gì?
**IP (Internet Protocol Address)** là **địa chỉ của một thiết bị trong mạng** - giống như số nhà trên một con đường.
Máy tính dùng IP để **gửi và nhận dữ liệu** qua mạng.
- Có 2 loại IP phổ biến:
    - **IPv4**: 32-bit → dạng 4 số, ví dụ: `192.168.1.10`
    - **IPv6**: 128-bit → ví dụ: `fe80::f8b5:52ff:fe2d:1a2c` (ít dùng trong cơ bản)
Khi gõ ping `8.8.8.8`, bạn đang gửi gói tin đến địa chỉ IP đó.

# 2. Subnet Mask & CIDR Notation
Subnet mask giúp **xác định phần nào của IP là "mạng" (network)** và phần nào là "máy" (host).
Ví dụ:
```bash
IP: 192.168.1.10
Subnet mask: 255.255.255.0
```
Có nghĩa là:
- 3 phần đầu `192.168.1` là **mạng**
- Phần cuối `.10` là **thiết bị trong mạng đó**

**CIDR Notation**
Thay vì viết "255.255.255.0", người ta viết **/24**
- `/24` nghĩa là: có **24 bit đầu dành cho network**, còn **8 bit cuối** dành cho host.

| CIDR | Subnet Mask     | Số IP khả dụng               |
| ---- | --------------- | ---------------------------- |
| /8   | 255.0.0.0       | ~16 triệu                    |
| /16  | 255.255.0.0     | ~65 nghìn                    |
| /24  | 255.255.255.0   | ~254                         |
| /30  | 255.255.255.252 | chỉ 2 host (dùng cho router) |

💡 Ví dụ dễ hiểu:
- `/24` → 192.168.1.0 → có thể chứa 254 máy (từ 192.169.1.1 đến 192.168.1.254)

# 3. Private IP vs Public IP
Không phải IP nào cũng ra Internet được!

| Loại IP        | Dải địa chỉ                                                                               | Dùng ở đâu                                             |
| -------------- | ----------------------------------------------------------------------------------------- | ------------------------------------------------------ |
| **Private IP** | 10.0.0.0 – 10.255.255.255<br>172.16.0.0 – 172.31.255.255<br>192.168.0.0 – 192.168.255.255 | Dùng **nội bộ** trong LAN, không ra Internet trực tiếp |
| **Public IP**  | Còn lại                                                                                   | Dùng **trên Internet**, ai cũng truy cập được          |

Ví dụ:
- Laptop ở nahf: `192.168.1.15` → Private IP
- Router của bạn: có 1 **Public IP** (do nhà mạng cấp)

# 4. Default Gateway
Gateway là **"của ra của mạng nội bộ"**.
- Nếu bạn gửi gói tin đến một IP **ngoài mạng của mình**, nó sẽ đi **qua gateway**.
- Gateway thường là **router hoặc modem**.
💡Ví dụ:
```bash
IP: 192.168.1.10
Subnet: 255.255.255.0 (/24)
Gateway: 192.168.1.1
```
→ Khi bạn gửi đến `8.8.8.8`, máy tính biết "IP này không nằm trong mạng 192.168.1.0", nên nó sẽ gửi qua **gateway 192.168.1.1**
 # 5. Routing Table (Bảng định tuyến)
Routing Table giống như **bản đồ giao thông** mà hệ điều hành dùng để biết:
> "Nếu muốn đi đến mạng X, thì nên đi đường nào."
Xem bảng này bằng lệnh:
- Windows: `route print`
- Linux/Mac: `ip route show`
Ví dụ trên Linux:
```bash
default via 192.168.1.1 dev eth0
192.168.1.0/24 dev eth0 proto kernel scope link src 192.168.1.10
```
Giải thích:
- `default via 192.168.1.1`: mọi gói tin không biết đi đâu thì đi qua **gateway 192.168.1.1**
- `192.168.1.0/24`: mạng nội bộ, gửi trực tiếp không qua gateway

# 6. NAT (Network Address Translation)
NAT giúp **nhiều máy tính trong mạng nội bộ (private IP)**, truy cập Internet **thông qua 1 địa chỉ public IP duy nhất.**
Ví dụ:
Bạn có 3 máy trong LAN:
```bash
192.168.1.10
192.168.1.11
192.168.1.12
```
Tất cả đều đi ra Internet qua modern có **Public IP: 203.0.113.45.**
Router/modern sẽ:
- Ghi nhớ gói tin nào từ máy nào.
- Khi gói phản hồi quay lại, router dịch ngược lại về đúng máy.
👉 Đây là lý do vì sao bạn và người thân cùng Wi-fi có thể truy cập web cùng một lúc, nhưng bên ngoài chỉ thấy **1 IP duy nhất**.
___
🧠 Tuy duy cần nắm
**1. IP = định danh máy trong mạng**
**2. Subnet = phạm vi của mạng nột bộ**
**3. Gateway = đường ra ngoài mạng**
**4. Routing = quyết định đường đi của gói tin**
**5. NAT = cho phép private IP đi ra internet**
___
🧪 Bài tập thực hành gợi ý
**1. Xem IP máy mình**
- Windows: `ipconfig`
- Linux: `ifconfig` hoặc `ip addr`
**2. Kiểm tra mạng gateway**
- Windows: `ifconfig | findstr Gateway`
**3. Ping ra ngoài mạng**
- `ping 8.8.8.8` (kiểm tra Internet)
**4. Xem bảng định tuyến**
- `route print` hoặc `ip route show`
**5. Thực hành chia subnet**
- Chia `192.168.1.0/24` thành 4 subnet nhỏ → /26
- Mooxi subnet có 62 máy.