# LAB 6 - pfSense + Active Directory + Access Policies
Họ tên : Lê Trung Kiên
Lớp: 11_ĐH_CNPM1
MSSV: 1150080060
LINK VIDEO: https://www.youtube.com/watch?v=fMcvfcVzk4o
## 1. Mục tiêu
Bài LAB xây dựng mô hình pfSense riêng với 4 interface gồm WAN, MGMT, LAN và DMZ; tích hợp Active Directory qua LDAP; sử dụng Captive Portal; tạo Alias, Schedule và các chính sách Firewall để kiểm soát truy cập.

## 2. Mô hình mạng đã cấu hình

### pfSense-LAB6
- WAN: `192.168.250.3/24` (DHCP từ LAB6-WAN)
- LAN: `10.6.0.1/24`
- DMZ: `172.16.6.1/24`
- MGMT: `192.168.56.254/24`

### DC01
- IP: `10.6.0.10/24`
- Gateway: `10.6.0.1`
- DNS: `10.6.0.10`
- Domain: `lab6.test`

### Client-A
- IP dự kiến: `10.6.0.21/24`
- Gateway: `10.6.0.1`
- DNS: `10.6.0.10`

### Client-B
- IP dự kiến: `10.6.0.22/24`
- Gateway: `10.6.0.1`
- DNS: `10.6.0.10`

### DMZ-WEB
- IP chính dự kiến: `172.16.6.10/24`
- IP phụ dùng mô phỏng:
  - `172.16.6.20` - JOB_SITE
  - `172.16.6.30` - SOCIAL_SITE
  - `172.16.6.40` - RESTRICTED_SITE
- Gateway: `172.16.6.1`

## 3. Kết quả thực tế đã xác nhận

Các kết quả dưới đây được ghi nhận từ hình ảnh kiểm thử thực tế:

- pfSense đã cấu hình đủ 4 interface WAN, LAN, DMZ và MGMT.
- Máy quản trị kết nối được tới pfSense MGMT tại `192.168.56.254`.
- DC01 đã được cấu hình địa chỉ `10.6.0.10`.
- Domain `lab6.test` đã được tạo thành công.
- Có thể đăng nhập bằng tài khoản `LAB6\Administrator`.
- Active Directory đã có OU `LAB6-Users`.
- Đã tạo các tài khoản phục vụ bài LAB, bao gồm `sv_a`, `sv_b` và tài khoản service LDAP.
- DNS của Active Directory hoạt động.
- Truy vấn SRV:
  `_ldap._tcp.dc._msdcs.lab6.test`
  trả về:
  - Server: `dc01.lab6.test`
  - Port: `389`
  - IP: `10.6.0.10`
- Ping `DC01.lab6.test` thành công.
- Ping gateway `10.6.0.1` thành công.

## 4. Các thành phần đang dùng

- Oracle VirtualBox
- pfSense CE
- Windows Server 2022
- Active Directory Domain Services
- DNS Server
- LDAP
- Windows Client
- Ubuntu Server / Nginx cho DMZ-WEB

## 5. Cấu trúc Active Directory

```text
lab6.test
|
|-- LAB6-Users
|   |-- sv_a
|   `-- sv_b
|
`-- Users
    `-- svc_pfsense_ldap
```

## 6. Thông số LDAP dùng cho pfSense

- Authentication Server: `LAB6-AD`
- Host: `10.6.0.10`
- Port: `389`
- Protocol Version: `3`
- Search Scope: `Entire Subtree`
- Base DN: `DC=lab6,DC=test`
- Authentication Container: `OU=LAB6-Users,DC=lab6,DC=test`
- Bind DN: `CN=svc_pfsense_ldap,CN=Users,DC=lab6,DC=test`
