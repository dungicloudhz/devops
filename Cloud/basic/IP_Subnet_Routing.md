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

# 3. Private IP vs Public IO