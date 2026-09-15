# Lab 1: Bắt gói tin Telnet - SSH

* **Họ và tên sinh viên:** Lê Trung Kiên
* **Mã số sinh viên:** = 1150080060
* **Lớp:** 11_DH_CNPM1
* **Link Video Demo (YouTube):https://www.youtube.com/watch?v=QFGllMGqzVc

---

## 1. Nội dung đã thực hiện
* Thiết lập mô hình mạng ảo trên VMware gồm 3 máy[cite: 1, 2]:
  * **Server (`10.0.0.1`):** Cung cấp dịch vụ quản trị từ xa (Telnet và SSH)[cite: 1, 2].
  * **Client (`10.0.0.2`):** Sử dụng PuTTY để kết nối điều khiển từ xa[cite: 1, 2].
  * **Attacker (`10.0.0.3`):** Sử dụng Wireshark để bắt và phân tích gói tin[cite: 1, 2].
* Thực hành bắt gói tin không mã hóa qua giao thức **Telnet (Cổng 23)**[cite: 2].
* Thực hành bắt gói tin mã hóa qua giao thức **SSH (Cổng 22)**[cite: 2].
* Phân tích sự khác biệt về mức độ bảo mật giữa hai giao thức và trả lời các câu hỏi báo cáo[cite: 2].

## 2. Kết quả thực hiện
* **Telnet:** Toàn bộ thông tin xác thực (username, password) và các câu lệnh thực thi (`dir`, `mkdir`) đều bị lộ dưới dạng văn bản rõ (*plaintext*) khi phân tích trên Wireshark[cite: 2].
* **SSH:** Dữ liệu trao đổi được mã hóa hoàn toàn thành các gói tin `Encrypted packet`, đảm bảo tính bảo mật cho kênh truyền[cite: 2].
