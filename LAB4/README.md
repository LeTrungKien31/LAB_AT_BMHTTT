# BÁO CÁO THỰC HÀNH LAB 3
LINK VIDEO: https://www.youtube.com/watch?v=Gjqdf3-w_Vk
**Học phần:** Thực hành An toàn và Bảo mật Hệ thống Thông tin[cite: 3]  
**Bài thực hành:** Lab 3 – Nhận diện và ứng phó các mối đe dọa đến an toàn thông tin[cite: 3]  
**Họ và tên:** LÊ TRUNG KIÊN  
**Mã số sinh viên (MSSV):** 1150080060  
**Lớp:** 11_ĐH_CNPM1  
**Tên tệp báo cáo:** `[11_ĐH_CNPM1]-LAB3_1150080060-LeTrungKien.docx`[cite: 3]
---

## PHẦN 1. MÔI TRƯỜNG THỰC HÀNH VÀ CÁC THÔNG SỐ HỆ THỐNG
* **Hệ điều hành máy ảo (Guest OS):** Windows 10 Pro 64-bit, Version 22H2 (OS Build 19045.3803)[cite: 11].
* **Phần mềm ảo hóa:** VMware Workstation (RAM: 2 GB, CPU: 2 Cores, Network: Host-only)[cite: 11].
* **Thư mục làm việc:** `C:\LAB3` | **Thư mục minh chứng:** `C:\LAB3\Evidence`[cite: 3, 11].
* **Phiên bản các công cụ:**
  * Python: `3.14.7`[cite: 3, 11]
  * Wireshark / TShark: `4.6.8`[cite: 3, 11]
  * Sysmon: `15.22`[cite: 3, 11]
  * Autoruns: `14.3`[cite: 3, 11]
  * Process Explorer: `17.14`[cite: 3, 11]
* **Mốc thời gian bắt đầu (Start Time):** `2026-09-22 00:14:43 -07:00`[cite: 11].

---

## PHẦN 2. KẾT QUẢ THỰC HIỆN CÁC TÌNH HUỐNG (TH0 – TH8)

| Tình huống | Nội dung thực hiện | Bằng chứng / File log | Kết quả |
| :--- | :--- | :--- | :---: |
| **TH0 & TH1** | Ghi nhận Baseline hệ thống (OS, Defender, Firewall, Network, Process) và lập Risk Register phân loại 5 nguồn đe dọa.[cite: 3, 11] | `baseline_os.txt`, `baseline_defender.txt`, `baseline_firewall.txt`, `baseline_network.txt`, `baseline_processes.txt`[cite: 3, 11] | **PASS** |
| **TH2** | Tạo chuỗi EICAR thử nghiệm, kiểm chứng tính năng Real-time Protection và Quarantine của Microsoft Defender.[cite: 3, 11] | `defender_eicar.txt`[cite: 3, 11] | **PASS** |
| **TH3** | Bật auditpol sự kiện đăng nhập, tạo tài khoản `lab3user`. Ghi nhận Event ID 4624, 4625 và 4648 khi dùng `runas`. Đổi mật khẩu thành công.[cite: 3, 11] | `auth_events_before_rotation.txt`[cite: 3, 11] | **PASS** |
| **TH4** | Cài đặt Sysmon 15.22 (Event 1). Tạo persistence Registry Run `LAB3_Run_Demo` và task `LAB3_Persistence_Demo`. Mở listener 8080 với Python (PID 4116).[cite: 3, 11] | `autoruns_before.csv`, `task_ran.txt`, `sysmon_persistence.txt`[cite: 3, 11] | **PASS** |
| **TH5** | Dùng Wireshark bắt gói tin: HTTP truyền bản rõ lộ tham số `TRAINING_ONLY`; HTTPS mã hóa TLS 1.2 bảo mật toàn bộ dữ liệu.[cite: 3, 11] | Ảnh chụp màn hình Wireshark (HTTP & HTTPS)[cite: 3, 11] | **PASS** |
| **TH6** | Kiểm thử tải cục bộ cổng 8080, phân tích tập dữ liệu DDoS TEST-NET và log `mailbomb_sample.csv` (phát hiện `bulk-sender` gửi 60 thư).[cite: 2, 3, 11] | `local_load_test.txt`, `dos_connections.txt`, `mail_sender_counts.txt`, `mail_volume.txt`[cite: 1, 2, 3] | **PASS** |
| **TH7** | Phân tích ngoại tuyến mẫu `phishing_email.txt` (5 chỉ dấu nhận diện) và phân loại 6 kịch bản Social Engineering trong `social_engineering_cases.csv`.[cite: 3, 11] | `phishing_email.txt`, kết quả phân loại case[cite: 3, 11] | **PASS** |
| **TH8** | Dọn dẹp persistence, dừng server 8080, xóa user `lab3user`, kiểm tra an toàn hệ thống và xuất bảng băm SHA-256 toàn bộ thư mục Evidence.[cite: 3, 11] | `autoruns_diff.txt`, `evidence_sha256.csv`[cite: 3, 11] | **PASS** |

---

## PHẦN 3. TRẢ LỜI CÂU HỎI THỰC HÀNH (10 CÂU NGẮN GỌN – 3 DÒNG/CÂU)

**Câu 1: Phân biệt Asset, Vulnerability, Threat, Risk và Attack**
* Tài sản (Asset) là dữ liệu báo cáo trong `C:\LAB3\Evidence`, còn lỗ hổng (Vulnerability) là dịch vụ web HTTP chạy bản rõ không mã hóa[cite: 3, 11].
* Mối đe dọa (Threat) là đối tượng nghe lén; rủi ro (Risk) là nguy cơ rò rỉ dữ liệu nhạy cảm làm mất tính bảo mật[cite: 3].
* Tấn công (Attack) là hành vi dùng Wireshark bắt gói tin để trích xuất tham số `TRAINING_ONLY` từ luồng truyền mạng[cite: 3, 11].

**Câu 2: Phân loại năm tình huống ở TH1**
* Tình huống 1 là *Hành động vô ý* do người dùng bất cẩn; tình huống 2 là *Hành động cố ý* mang mục đích xâm nhập phá hoại[cite: 3].
* Tình huống 3 thuộc nhóm *Thảm họa tự nhiên / sự cố môi trường* do mất điện diện rộng từ hạ tầng bên ngoài[cite: 3].
* Tình huống 4 là *Lỗi kỹ thuật* do hỏng hóc phần cứng/phần mềm; tình huống 5 là *Lỗi quản lý* do thiếu giám sát chính sách[cite: 3].

**Câu 3: Ý nghĩa kiểm chứng của chuỗi EICAR với Microsoft Defender**
* EICAR chứng minh tính năng Real-time Protection của Defender đang bật và cơ chế nhận diện chữ ký hoạt động tốt[cite: 3].
* Quá trình chặn ghi tệp và cách ly chứng minh tính năng bảo vệ tự động của hệ điều hành được kích hoạt chuẩn xác[cite: 3, 11].
* EICAR không chứng minh được máy trạm có khả năng chống lại mọi dòng mã độc thực tế, zero-day hay fileless malware[cite: 3].

**Câu 6: Trạng thái xác thực của lab3user qua Event ID 4624, 4625 và 4648**
* Event 4648 ghi nhận khi dùng `runas`, còn Event 4624 xuất hiện khi đăng nhập thành công bằng mật khẩu ban đầu[cite: 3].
* Event 4625 được ghi lại khi cố ý nhập sai mật khẩu để kiểm thử cơ chế phát hiện brute-force của hệ thống[cite: 3, 11].
* Sau khi đổi mật khẩu mới, mật khẩu cũ bị từ chối (tạo Event 4625) và chỉ mật khẩu mới mới tạo được Event 4624[cite: 3].

**Câu 8: Cổng 8080 đang Listen có đủ kết luận backdoor không và các bằng chứng cần đối chiếu**
* Chưa đủ kết luận vì nhiều tiến trình hệ thống và phần mềm hợp pháp cũng mở cổng lắng nghe cục bộ[cite: 3].
* Cần kiểm tra: đường dẫn tệp thực thi (Path) và chữ ký số (Publisher) của tiến trình qua Process Explorer[cite: 3, 11].
* Cần đối chiếu thêm: dòng lệnh khởi chạy (Command line) và phạm vi bind chỉ ở `127.0.0.1` hay mở ra toàn mạng[cite: 3, 11].

**Câu 10: So sánh DoS và DDoS qua thực tế bài lab**
* Đoạn script tải cục bộ mô phỏng DoS từ một nguồn duy nhất trên localhost (`127.0.0.1:8080`) gây nghẽn dịch vụ[cite: 3, 11].
* Tệp `ddos_sample.csv` thể hiện tấn công DDoS với lưu lượng phân tán đến từ hàng loạt dải IP TEST-NET khác nhau[cite: 3].
* Không thể chặn DDoS chỉ bằng một rule IP vì kẻ tấn công liên tục đổi địa chỉ IP và tận dụng mạng botnet phân tán[cite: 3].

**Câu 11: Ảnh hưởng của Mail Bombing và hai chỉ số phát hiện bất thường**
* Mail bombing làm suy giảm tính sẵn sàng (Availability) do gây quá tải hàng đợi xử lý và cạn kiệt dung lượng hộp thư[cite: 3].
* Chỉ số 1: Tần suất gửi thư theo nguồn (Sender Count), nhận diện kẻ gửi thư rác dồn dập (như `bulk-sender` gửi 60 thư)[cite: 2, 3].
* Chỉ số 2: Tổng dung lượng (Sum) và dung lượng trung bình (Average) qua trường `SizeBytes` tăng đột biến trong log[cite: 1, 3].

**Câu 12: Sự khác nhau giữa HTTP và HTTPS khi quan sát bằng Wireshark**
* Lưu lượng HTTP gửi tới cổng 8080 lộ rõ toàn bộ Request URI, phương thức GET và chuỗi tham số bản rõ (Plaintext)[cite: 3, 11].
* Lưu lượng HTTPS tới cổng 443 được mã hóa an toàn qua TLS v1.2, bảo vệ tuyệt đối nội dung dữ liệu ứng dụng[cite: 3, 11].
* Bắt gói tin HTTPS chỉ xem được thông tin metadata (IP nguồn/đích, cổng, kích thước gói) chứ không đọc được nội dung web[cite: 3, 11].

**Câu 17: Năm chỉ dấu nhận diện trong tệp phishing_email.txt**
* Tạo cảm giác thúc ép, khẩn cấp khi đe dọa khóa tài khoản của người dùng chỉ trong vòng 15 phút[cite: 3, 11].
* Dùng tên hiển thị giả mạo uy tín (`IT Support - Training`) và địa chỉ `Reply-To` chuyển hướng khác với địa chỉ `From`[cite: 3, 11].
* Thúc ép nạn nhân nhấp vào liên kết ngoài để nhập thông tin đăng nhập cùng mã xác minh tài khoản[cite: 3, 11].

**Câu 19: Ý nghĩa của mã băm SHA-256 đối với thư mục Evidence**
* SHA-256 chứng minh tính toàn vẹn (Integrity), đảm bảo các tệp bằng chứng không bị thay đổi hay sửa đổi sau khi xuất[cite: 3, 11].
* Thay đổi dù chỉ 1 ký tự trong tệp log sẽ làm giá trị hash thay đổi hoàn toàn, giúp phát hiện việc làm giả dữ liệu[cite: 3].
* Mã băm không chứng minh được tính đúng đắn hay nguồn gốc ban đầu của dữ liệu nếu dữ liệu được ghi bị sai lệch từ đầu[cite: 3].

---

## PHẦN 4. NỘI DUNG TỆP README.MD CHO REPOSITORY GITHUB

```markdown
# LAB 3: NHẬN DIỆN VÀ ỨNG PHÓ CÁC MỐI ĐE DỌA ĐẾN AN TOÀN THÔNG TIN

- **Sinh viên thực hiện:** LÊ TRUNG KIÊN
- **MSSV:** 1150080060
- **Lớp:** 11_ĐH_CNPM1
- **Môn:** Thực hành An toàn và Bảo mật Hệ thống Thông tin

## 1. Thông số môi trường
- Hệ điều hành máy ảo: Windows 10 Pro 64-bit (Build 19045.3803)
- Phần mềm ảo hóa: VMware Workstation Pro (Host-only Network)
- Các công cụ sử dụng: Python 3.14.7, Wireshark 4.6.8, Sysmon 15.22, Autoruns 14.3, Process Explorer 17.14

## 2. Các tình huống thực hiện
- TH0 & TH1: Baseline hệ thống và lập bảng Risk Register phân loại nguy cơ.
- TH2: Thử nghiệm mã độc EICAR và cơ chế cách ly của Defender.
- TH3: Kiểm toán sự kiện đăng nhập Event ID 4624/4625 và đổi mật khẩu an toàn.
- TH4: Thiết lập persistence qua Registry Run, Scheduled Task và kiểm tra tiến trình cổng 8080.
- TH5: Phân tích bắt gói tin Wireshark so sánh HTTP bản rõ và HTTPS mã hóa.
- TH6: Mô phỏng DoS localhost, phân tích dữ liệu DDoS và log mail bombing.
- TH7: Phân tích mẫu email Phishing và phân loại 6 trường hợp Social Engineering.
- TH8: Cleanup môi trường, xác minh phục hồi và băm mã SHA-256 toàn bộ bằng chứng.

## 3. Lỗi gặp phải và cách xử lý
- **Lệch đường dẫn giải nén file mẫu:** Xử lý bằng cách chuẩn hóa giải nén vào thư mục `C:\LAB3\lab3_assets`.
- **Lỗi đường dẫn Autoruns:** Gọi thực thi thông qua tìm kiếm động hoặc đường dẫn trực tiếp của tệp `autorunsc64.exe`.
- **Xung đột tiến trình khi hash SHA-256:** Áp dụng điều kiện lọc bỏ qua chính file `evidence_sha256.csv` khi đọc thư mục.

## 4. Cấu trúc thư mục nộp bài
- `[11_ĐH_CNPM1]-LAB3_1150080060-LeTrungKien.docx`: Báo cáo Word hoàn chỉnh kèm hình ảnh minh chứng.
- `evidence_sha256.csv`: Bảng mã băm SHA-256 toàn vẹn cho dữ liệu.
- `README.md`: Hướng dẫn và thông tin thực hiện bài lab.