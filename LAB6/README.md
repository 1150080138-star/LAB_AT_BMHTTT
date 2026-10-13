# LAB6 – Cấu hình Firewall pfSense

## 1. Giới thiệu

Bài thực hành LAB6 xây dựng hệ thống mạng sử dụng **pfSense CE 2.7.2** với bốn vùng mạng **WAN, MGMT, LAN và DMZ**. Nội dung thực hành gồm kết nối xác thực **LDAP/Active Directory**, thiết lập **Captive Portal**, tạo **Aliases**, cấu hình chính sách **Firewall** và kiểm tra nhật ký truy cập.
YT:https://youtu.be/TEq691Avsg4

> **Trạng thái:** Đang thực hiện. Chỉ đánh dấu hoàn thành các mục sau khi đã cấu hình và kiểm thử thành công.

## 2. Mục tiêu

- Thiết lập máy ảo pfSense với 4 interface mạng.
- Cấu hình địa chỉ IP và kết nối giữa các vùng mạng.
- Kết nối pfSense với Active Directory thông qua LDAP và kiểm tra xác thực.
- Cấu hình Captive Portal cho mạng LAN.
- Sử dụng Aliases để quản lý IP máy khách và nhóm địa chỉ đích.
- Tạo và kiểm thử các quy tắc Firewall theo IP, cổng dịch vụ và lịch thời gian.
- Quan sát Firewall Logs, kiểm tra kết quả trước/sau khi áp dụng chính sách.

## 3. Môi trường thực hành

| Thành phần | Mô tả |
|---|---|
| Phần mềm ảo hóa | VMware Workstation 17.5 |
| Firewall | pfSense CE 2.7.2 |
| Máy chủ xác thực | Windows Server / Active Directory (cấu hình trong quá trình thực hành) |
| Máy khách | Các máy thử nghiệm trong mạng LAN |
| Máy chủ web | Máy thử nghiệm trong DMZ |

### Sơ đồ mạng dự kiến

| Interface | VMware Network | Dải mạng | Địa chỉ pfSense |
|---|---|---|---|
| WAN | VMnet8 (NAT) | 192.168.40.0/24 | DHCP |
| MGMT (OPT1) | VMnet3 (Host-only) | 192.168.56.0/24 | 192.168.56.254/24 |
| LAN | VMnet4 (Host-only) | 10.6.0.0/24 | 10.6.0.1/24 |
| DMZ (OPT2) | VMnet5 (Host-only) | 172.16.6.0/24 | 172.16.6.1/24 |

**Lưu ý:** Bảng thể hiện cấu hình mục tiêu của LAB6; cần đối chiếu lại địa chỉ thực tế sau khi cấu hình xong. Không thay đổi các VMnet đang sử dụng cho LAB5.

## 4. Nội dung thực hiện

### 4.1. Cấu hình mạng pfSense

- Tạo hoặc sử dụng máy ảo pfSense dành riêng cho LAB6.
- Gắn 4 card mạng tương ứng WAN, MGMT, LAN và DMZ.
- Gán interface và cấu hình địa chỉ IP.
- Kiểm tra khả năng truy cập giao diện quản trị và kết nối mạng.

### 4.2. LDAP / Active Directory

- Chuẩn bị Domain Controller và tài khoản kiểm thử.
- Cấu hình Authentication Server loại LDAP trên pfSense.
- Kiểm tra xác thực tại **Diagnostics > Authentication**.

### 4.3. Captive Portal

- Tạo Captive Portal zone cho LAN.
- Cấu hình phương thức xác thực thông qua LDAP/AD.
- Kiểm tra đăng nhập từ máy khách.

### 4.4. Aliases và Firewall Rules

- Tạo Alias cho IP máy khách và nhóm địa chỉ đích.
- Thiết lập các chính sách truy cập theo yêu cầu bài thực hành.
- Kiểm tra giới hạn theo địa chỉ IP, dịch vụ/cổng và thời gian.
- Lưu ý: Firewall rule cơ bản của pfSense không trực tiếp lọc theo tên người dùng LDAP; LDAP được sử dụng trong các cơ chế xác thực như Captive Portal.

### 4.5. Kiểm thử và ghi log

- Kiểm tra truy cập trước khi áp dụng rule.
- Kiểm tra lại sau khi áp dụng rule.
- Kiểm tra **Status > System Logs > Firewall**.
- Reset states khi cần để kiểm thử thay đổi chính sách.

## 5. Kết quả thực hiện

*Cập nhật kết quả thực tế sau khi hoàn thành LAB6.*

| Hạng mục | Kết quả | Minh chứng |
|---|---|---|
| 4 interface pfSense | Chưa cập nhật | H01, H02 |
| LDAP xác thực AD | Chưa kiểm thử | H03, H04 |
| Captive Portal | Chưa kiểm thử | H05, H06 |
| Aliases / Firewall Rules | Chưa kiểm thử | H07, H08 |
| Kiểm thử chính sách | Chưa kiểm thử | H09 |
| Firewall Logs | Chưa kiểm thử | H10 |

## 6. Minh chứng



## 7. Báo cáo

Báo cáo Word được lưu trong thư mục [.

## 8. Lưu ý khi nộp

- Không đưa mật khẩu, tài khoản quản trị có thông tin nhạy cảm, khóa bí mật lên GitHub.
- Không cần tải lên các tệp máy ảo `.vmdk`, `.vmem`, `.vmsn` hoặc toàn bộ thư mục VMware.
- Đối chiếu yêu cầu nộp bài của giảng viên trước khi công khai repository.
