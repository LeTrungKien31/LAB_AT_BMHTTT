# LAB 5 – THIẾT LẬP MÔ HÌNH TƯỜNG LỬA pfSense
**Link Video: https://www.youtube.com/watch?v=_Y6xGrjA4pw

## Thông tin sinh viên

- **Họ và tên:** Lê Trung Kiên
- **MSSV:** 1150080060
- **Lớp:** 11_ĐH_CNPM1
- **Môn học:** An toàn bảo mật hệ thống thông tin
- **Bài thực hành:** Thiết lập mô hình tường lửa pfSense

---

## 1. Mục tiêu

Bài lab xây dựng mô hình mạng sử dụng **pfSense CE** làm tường lửa để bảo vệ mạng LAN và vùng DMZ.

Các nội dung chính:

- Cài đặt pfSense trên Oracle VirtualBox.
- Cấu hình các interface WAN, LAN và DMZ.
- Cấu hình NAT và Firewall Rules.
- Sử dụng Windows Server làm Domain Controller trong mạng LAN.
- Tạo máy chủ Web trong vùng DMZ và cài IIS.
- Kiểm thử các tình huống lọc lưu lượng bằng pfSense.
- Theo dõi Firewall Log để xác định lưu lượng bị chặn.

---

## 2. Mô hình mạng

```text
                         INTERNET
                             |
                            WAN
                      DHCP / Bridged
                             |
                        +-----------+
                        |  pfSense  |
                        +-----------+
                         |         |
                    LAN  |         |  DMZ
                10.0.0.1/8     172.16.0.1/16
                         |         |
              ------------         ------------
              |                                |
      Windows Server DC                  DMZ-Web Server
         10.0.0.2/8                      172.16.0.2/16
       GW: 10.0.0.1                    GW: 172.16.0.1
       DNS: 10.0.0.2                    DNS: 8.8.8.8
```

---

## 3. Bảng địa chỉ IP

| Thiết bị | Interface | IP | Subnet Mask | Gateway | DNS |
|---|---|---|---|---|---|
| pfSense | WAN | DHCP | Tự động | DHCP | Upstream |
| pfSense | LAN | 10.0.0.1 | 255.0.0.0 | - | - |
| pfSense | DMZ | 172.16.0.1 | 255.255.0.0 | - | - |
| Domain Controller | LAN | 10.0.0.2 | 255.0.0.0 | 10.0.0.1 | 10.0.0.2 |
| Máy thật quản trị | Host-only | 10.0.0.100 | 255.0.0.0 | - | - |
| DMZ-Web | DMZ | 172.16.0.2 | 255.255.0.0 | 172.16.0.1 | 8.8.8.8 |

---

## 4. Cấu hình máy ảo pfSense

### Phần cứng

- RAM: khoảng 2 GB
- CPU: 2 vCPU
- Disk: khoảng 16–20 GB
- Hệ điều hành: BSD / FreeBSD 64-bit
- pfSense CE 2.7.2-RELEASE

### Card mạng

- **Adapter 1:** Bridged Adapter → WAN
- **Adapter 2:** Host-only Adapter → LAN
- **Adapter 3:** Internal Network `dmz-net` → DMZ

> Không sử dụng VirtualBox NAT mặc định `10.0.2.0/24` cho WAN vì dải này nằm trong mạng LAN `10.0.0.0/8` của bài lab.

---

## 5. Cấu hình pfSense

### LAN

```text
IP: 10.0.0.1
Prefix: /8
DHCP Server: Disable
```

### DMZ

```text
Interface: OPT1
Description: DMZ
IPv4 Configuration Type: Static IPv4
IP: 172.16.0.1/16
```

### Outbound NAT

Sử dụng một trong hai chế độ:

- Automatic Outbound NAT
- Hybrid Outbound NAT

pfSense tự sinh NAT cho các mạng nội bộ. Không cần tạo manual Outbound NAT cho LAN hoặc DMZ trong cấu hình cơ bản.

---

## 6. Domain Controller

Máy Windows Server trong LAN được cấu hình:

```text
IP address: 10.0.0.2
Subnet mask: 255.0.0.0
Default gateway: 10.0.0.1
Preferred DNS: 10.0.0.2
```

Các bước:

1. Cài **Active Directory Domain Services**.
2. Promote server thành Domain Controller.
3. Tạo forest mới.
4. Cấu hình DNS Forwarder đến DNS upstream, ví dụ `8.8.8.8`.

---

## 7. DMZ-Web Server

Máy Windows Server trong DMZ:

```text
IP address: 172.16.0.2
Subnet mask: 255.255.0.0
Default gateway: 172.16.0.1
DNS: 8.8.8.8
```

Cài IIS bằng PowerShell:

```powershell
Install-WindowsFeature Web-Server -IncludeManagementTools
```

Kiểm tra IIS:

```powershell
curl.exe http://localhost
```

---

## 8. Firewall Rule nền tảng

Trên tab **Firewall → Rules → LAN**:

- Disable các rule `Default allow LAN to any`.
- Giữ `Anti-Lockout Rule`.
- Tạo rule:

```text
Action: Pass
Protocol: Any
Source: LAN net
Destination: Any
```

Sau mỗi lần thay đổi rule cần:

```text
Apply Changes
→ Diagnostics
→ States
→ Reset States
```

pfSense là stateful firewall nên state cũ có thể làm sai kết quả kiểm thử nếu không được xóa.

---

## 9. Một số lệnh kiểm tra

### Windows

```cmd
ipconfig
ping 10.0.0.1
ping 8.8.8.8
nslookup example.com
ipconfig /flushdns
```

### PowerShell

```powershell
Resolve-DnsName example.com
curl.exe -4 https://example.com
curl.exe http://localhost
```

### Cho phép ICMP Echo tạm thời trên Domain Controller

```cmd
netsh advfirewall firewall add rule name="LAB-Allow-ICMPv4-Echo" protocol=icmpv4:8,any dir=in action=allow
```

Xóa rule sau khi kiểm thử:

```cmd
netsh advfirewall firewall delete rule name="LAB-Allow-ICMPv4-Echo"
```

---

## 11. Lưu ý

- Không tắt **Anti-Lockout Rule** trên pfSense.
- Không đặt Gateway/DNS trên card Host-only của máy thật.
- Không dùng `10.0.2.0/24` làm WAN trong mô hình này.
- Rule trên pfSense được xét theo thứ tự từ trên xuống.
- Rule cụ thể phải được đặt phía trên rule tổng quát.
- Sau khi thay đổi firewall rule nên `Apply Changes` và `Reset States`.
- DMZ-Web phải chạy IIS thành công trước khi kiểm thử Port Forward.
- Các ảnh minh chứng trong báo cáo phải là ảnh chụp từ môi trường thực hành của sinh viên.

---



File báo cáo:

```text
Lab5_11_ĐH_CNPM1_1150080060_LeTrungKien_HOAN_CHINH.docx
```

Nội dung báo cáo gồm:

- Cấu hình máy ảo và các card mạng.
- Cài đặt pfSense.
- Cấu hình LAN, WAN và DMZ.
- Cấu hình Domain Controller.
- Cấu hình IIS trên DMZ-Web.
- NAT và firewall rules.
- Các tình huống kiểm thử.
- Firewall Logging.
- Câu hỏi 
