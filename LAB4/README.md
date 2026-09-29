# LAB 4 – Khảo sát và đánh giá bề mặt mạng bằng Nmap
Link video: https://www.youtube.com/watch?v=VjQZrglVmo4
| Thông tin | Nội dung |
|---|---|
| Họ tên | Lê Trung Kiên |
| MSSV | 1150080060 |
| Lớp | 11_ĐH_CNPM1 |
| Môn học | An toàn hệ thống thông tin |
| Tên lab | Lab 4 – Khảo sát và đánh giá bề mặt mạng bằng Nmap |
| Ngày thực hiện | 29/09/2026 |

> **Phạm vi:** toàn bộ thao tác chỉ thực hiện trên máy ảo do chính sinh viên dựng, trong mạng Host-Only, phục vụ học tập. Không quét hệ thống bên ngoài.

---

## 1. Phiên bản môi trường

| Thành phần | Chi tiết | Nguồn xác định |
|---|---|---|
| Công cụ quét | Nmap **7.99** | Output `Starting Nmap 7.99` |
| Máy quét | Kali Linux (VM `VM_KALI`), user `kienle`, hostname `KienLe` | Prompt terminal |
| Máy đích | Metasploitable 2 (VM `Metasploitable2-Linux`), hostname `metasploitable` | smb-os-discovery |
| Nền tảng ảo hóa | VMware (MAC `00:0C:29:…` và `00:50:56:…` là OUI của VMware) | Output Nmap |
| Mạng | Host-Only, dải `192.168.56.0/24` | Lệnh `nmap … 192.168.56.0/24` |
| Máy thật (host OS) | *(chưa ghi nhận – điền thêm nếu cần)* | – |
| Phiên bản Kali / VMware | *(chưa ghi nhận – điền thêm nếu cần)* | – |

### Bảng địa chỉ

| Thiết bị | IP | MAC | Vai trò |
|---|---|---|---|
| Kali VM | 192.168.56.128 | (không hiển thị – chính máy quét) | Máy quét |
| Metasploitable 2 | 192.168.56.129 | 00:0C:29:5D:F0:26 (VMware) | Máy đích |
| Dịch vụ mạng ảo | 192.168.56.254 | 00:50:56:EA:68:AC (VMware) | Nhiều khả năng là DHCP/mạng ảo của VMware, không phải VM của sinh viên |

---

## 2. Cách dựng môi trường

1. **Cài Nmap trên Kali** (nếu chưa có), sau đó kiểm tra phiên bản:
   ```bash
   sudo apt update
   sudo apt install nmap
   nmap --version
   ```
2. **Tạo mạng Host-Only** cho hai VM (Kali và Metasploitable 2) cùng nằm trong dải `192.168.56.0/24`. Metasploitable 2 chỉ dùng Host-Only, **không** Bridged, để không lộ máy có lỗ hổng ra mạng thật. Kali chỉ bật NAT tạm thời khi cần cập nhật gói, phải ngắt trước khi quét.
3. **Lấy IP từng máy:**
   ```bash
   ip -br addr        # trên Kali
   ifconfig           # trên Metasploitable 2 (user msfadmin)
   ```
4. **Kiểm tra kết nối trước khi quét:**
   ```bash
   ping -c 4 192.168.56.129
   ```
5. **Snapshot** cả hai VM trước khi thực hành (khuyến nghị đặt tên `Before-LAB4`).
6. Chạy các lệnh quét theo mục 3. Các lệnh quét gói thô (`-sS`, `-sF`, `-sX`, `-sN`, `-sU`, `-O`, `-A`) cần `sudo`.

---

## 3. Các tình huống đã thực hiện và kết quả PASS/FAIL

Quy ước: **PASS** = lệnh chạy đúng, có bằng chứng (ảnh) và kết quả đọc hiểu được. **FAIL** = lệnh lỗi hoặc kết quả không đạt. **CHƯA LÀM** = chưa có bằng chứng thực hiện trong bài.

Mục tiêu quét: `192.168.56.129` (Metasploitable 2).

| # | Tình huống | Lệnh | Kết quả quan sát | Kết quả |
|---|---|---|---|---|
| 1 | Host discovery / quét cổng 445 toàn dải | `sudo nmap -p 445 192.168.56.0/24 -oG smb.txt` | 256 IP, **3 host up** (.128, .129, .254) trong 15,30 s | **PASS** |
| 2 | TCP Connect scan | `nmap -sT 192.168.56.129` | 23 open, 977 closed (conn-refused), 0 filtered; 4,72 s | **PASS** |
| 3 | SYN scan | `sudo nmap -sS 192.168.56.129` | 23 open, 977 closed (reset), 0 filtered; 4,83 s | **PASS** |
| 4 | FIN scan | `sudo nmap -sF 192.168.56.129` | 23 open\|filtered; 6,04 s | **PASS** |
| 5 | Xmas scan | `sudo nmap -sX 192.168.56.129` | 23 open\|filtered; 6,07 s | **PASS** |
| 6 | NULL scan | `sudo nmap -sN 192.168.56.129` | 23 open\|filtered; 6,08 s | **PASS** |
| 7 | ACK scan | `sudo nmap -sA 192.168.56.129` | Chưa có ảnh/kết quả | **CHƯA LÀM** |
| 8 | UDP scan top 20 cổng | `sudo nmap -sU --top-ports 20 192.168.56.129` | 2 open (53, 137), 3 open\|filtered (68, 69, 138), 15 closed; 22,30 s | **PASS** |
| 9 | OS detection | `sudo nmap -O 192.168.56.129` | Linux 2.6.9 – 2.6.33; 1 hop; 6,13 s | **PASS** |
| 10 | Version detection | `sudo nmap -sV -O 192.168.56.129 -oN/-oX …` | Nhận diện phiên bản 23 dịch vụ (vsftpd 2.3.4, OpenSSH 4.7p1, Apache 2.2.8, Samba 3.0.20, MySQL 5.0.51a, UnrealIRCd…); ≈ 58,5 s | **PASS** |
| 11 | Aggressive scan | `sudo nmap -A 192.168.56.129` | Có version, OS, traceroute, default script; 157,67 s | **PASS** |
| 12 | NSE `smb-os-discovery` | `sudo nmap -p 445 --script smb-os-discovery 192.168.56.129` | Unix (Samba 3.0.20-Debian), computer name `metasploitable`, domain `localdomain` | **PASS** |
| 13 | NSE `smb-vuln-ms17-010` | `sudo nmap -p 445 --script smb-vuln-ms17-010 192.168.56.129` | Cổng 445 open, script **không báo VULNERABLE** và không in kết quả | **PASS** (chạy đúng; kết luận: không có dấu hiệu, *không* suy ra là đã vá) |
| 14 | Xuất normal text | `… -oN ket_qua.txt` | Tạo được `ket_qua.txt`, mở được bằng Mousepad | **PASS** |
| 15 | Xuất XML | `… -oX ket_qua.xml` | Tạo được `ket_qua.xml`, mở được bằng Firefox | **PASS** |
| 16 | Xuất grepable + lọc | `-oG smb.txt` rồi `grep "445/open" smb.txt` | Chỉ ra đúng 192.168.56.129 | **PASS** |
| 17 | Chuyển XML → HTML (lần 1) | `xsltproc ket_qua.xml -O bao_cao.html` | Lỗi `Unknown option -O` | **FAIL** |
| 18 | Chuyển XML → HTML (sau sửa) | `xsltproc ket_qua.xml -o bao_cao.html` | Tạo được `bao_cao.html`, mở được bằng Firefox | **PASS** |
| 19 | Before/after hardening (Windows VM) | `-sV` trước và sau khi thay đổi | Chưa có dữ liệu | **CHƯA LÀM** |
| 20 | NSE trên Windows VM (nếu có) | – | Chưa có dữ liệu | **CHƯA LÀM** |
| 21 | Bài tập bổ sung (mục 14: quét 65535 cổng, `-D`, `-oA`, bản đồ dịch vụ…) | – | Chưa có dữ liệu | **CHƯA LÀM** |

**Tổng kết:** 16 PASS, 1 FAIL (đã khắc phục ở #18), 4 CHƯA LÀM.

### Phát hiện chính trên Metasploitable 2

- 23 cổng TCP đang mở; nhiều dịch vụ phiên bản cũ và không mã hóa (Telnet, rexec/rlogin/rsh, FTP, VNC, X11).
- Ba dịch vụ rủi ro nhất: **1524/tcp bindshell** (root shell không xác thực), **21/tcp vsftpd 2.3.4** (bản có backdoor, cho phép anonymous FTP), **6667/tcp UnrealIRCd 3.2.8.1** (bản có backdoor).
- Samba 3.0.20 (445/tcp) và SMB message signing đang tắt.

---

## 4. Lỗi gặp phải và cách khắc phục

| # | Lỗi / hiện tượng | Nguyên nhân | Cách khắc phục |
|---|---|---|---|
| 1 |
| 2 | Lệnh `grep "445/open" smb.txt` xuất hiện hai lần trong ảnh | Chạy lặp lệnh khi chụp ảnh, không ảnh hưởng kết quả | Không cần khắc phục; kết quả cả hai lần giống nhau |
| 3 | Bị hỏi `[sudo] password` khi chạy `-sS`, `-sF`, … | Quét bằng gói thô cần quyền root | Dùng `sudo` và nhập mật khẩu; `-sT` thì không cần `sudo` |
| 4 | Số host up (3) nhiều hơn số VM đang bật (2) | Có thêm 192.168.56.254, là dịch vụ DHCP/mạng ảo của VMware | Đối chiếu MAC (`00:50:56:…`) để xác định đây không phải VM của sinh viên |
| 5 | FIN/Xmas/NULL chỉ ra `open\|filtered`, không xác định được cổng mở | Cổng mở và cổng bị lọc đều im lặng với gói bất thường | Không ghi `open\|filtered = open`; đối chiếu với `-sT`/`-sS` để xác nhận 23 cổng open |
| 6 | `smb-vuln-ms17-010` không in ra kết quả | Máy đích chạy Samba trên Linux, lỗ hổng SMBv1 của Windows không áp dụng; script không báo VULNERABLE | Chỉ kết luận "không có dấu hiệu", không suy ra "đã vá"; đề xuất nâng cấp Samba, tắt SMBv1, bật SMB signing |
| 7 | `-A` chạy rất lâu (157,67 s) | Nmap chạy version detection, OS detection, traceroute và nhiều NSE script | Chấp nhận; dùng `-A` để tổng hợp, còn phân tích chi tiết dùng `-sV`/`-O` riêng lẻ |
| 8 | UDP scan chậm (22,30 s cho 20 cổng) và có `open\|filtered` | UDP không có bắt tay; Linux giới hạn tốc độ ICMP unreachable | Chỉ quét nhóm cổng phổ biến bằng `--top-ports 20` |

---

## 5. Cấu trúc file nộp

```
Lab4_11_ĐH_CNPM1_1150080060_LeTrungKien.docx   # Báo cáo kèm ảnh minh chứng
README.md                                       # File này
ket_qua.txt / ket_qua.xml / bao_cao.html / smb.txt   # Kết quả xuất từ Nmap (nếu nộp kèm)
```

## 6. Việc còn thiếu

- Chụp và bổ sung `sudo nmap -sA 192.168.56.129` (ACK scan).
- Thực hiện before/after hardening trên Windows VM hoặc dịch vụ test, điền bảng so sánh.
- Bổ sung ảnh `ip -br addr` (Kali), `ifconfig` (Metasploitable 2) và `nmap -sn` nếu giảng viên yêu cầu đúng danh mục 8 ảnh.
- Làm các bài tập bổ sung mục 14 nếu được yêu cầu.
- Điền phiên bản Kali, VMware và hệ điều hành máy thật vào mục 1.