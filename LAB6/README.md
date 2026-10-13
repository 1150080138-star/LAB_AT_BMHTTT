LAB6 – pfSense: Phân vùng mạng, Aliases và Firewall Rules
Môn học: Thực hành An toàn hệ thống thông tin
Sinh viên: Trần Thị Ngọc Huyền
MSSV: 1150080138
Lớp: 11CNPM2
Môi trường: VMware Workstation, pfSense CE 2.7.2, Ubuntu Server (Nginx), Windows 11.
1. Mục tiêu
Xây dựng mô hình mạng gồm bốn vùng WAN, MGMT, LAN và DMZ trên pfSense; triển khai web server Nginx trong DMZ; tạo Aliases và Firewall Rules để kiểm soát truy cập từ máy khách LAN tới các website mô phỏng.
2. Mô hình mạng
Thành phần	Địa chỉ	Vai trò
WAN	DHCP qua NAT	Kết nối Internet
MGMT	192.168.56.254/24	Quản trị pfSense
LAN	10.6.0.1/24	Mạng máy khách
DMZ	172.16.6.1/24	Mạng máy chủ
Ubuntu Server	172.16.6.10	Nginx web server
Client-A	10.6.0.21	Máy kiểm thử


Lưu ý: Trong cấu hình pfSense hiện tại, interface có nhãn DMZ (OPT1) mang IP MGMT 192.168.56.254, còn OPT2 mang IP DMZ 172.16.6.1. Xác định interface theo IP và vai trò khi tạo rule.
3. Nội dung thực hành
3.1. Thiết lập pfSense
- Cấu hình bốn card mạng WAN, MGMT, LAN, DMZ.
- Đăng nhập WebGUI và kiểm tra địa chỉ IP các interface.
3.2. Cài đặt Nginx trên Ubuntu Server
- Cấu hình máy Ubuntu trong DMZ.
- Cho phép truy cập Internet tạm thời để tải gói phần mềm.
- Kiểm tra kết nối và cài Nginx:
ping -c 3 8.8.8.8
sudo apt update
sudo apt install nginx
sudo systemctl status nginx
curl http://172.16.6.10
Kết quả cần kiểm tra: trang mặc định Welcome to nginx! được trả về qua HTTP.
3.3. Chuẩn bị Windows 11 Client
- Kết nối máy khách vào mạng LAN 10.6.0.0/24.
- Thiết lập IP tĩnh và kiểm tra kết nối tới pfSense và máy chủ DMZ.
3.4. Cấu hình Aliases
Vào Firewall → Aliases → IP, tạo:
Alias	Địa chỉ	Mô tả
CLIENT_A	10.6.0.21	Máy khách cần áp dụng chính sách
GENERAL_SITE	172.16.6.10	Website được phép truy cập
JOB_SITE	172.16.6.20	Website cần chặn


3.5. Cấu hình Firewall Rules
Tại Firewall → Rules → LAN:
1. Cho phép ICMP từ LAN tới địa chỉ LAN của pfSense để kiểm tra kết nối.
2. Block CLIENT_A → JOB_SITE, TCP port 80.
3. Pass CLIENT_A → GENERAL_SITE, TCP port 80.
4. Đặt rule chặn cụ thể phía trên các rule cho phép rộng, sau đó Save → Apply Changes.
3.6. Kiểm thử truy cập
Trên Windows 11, chạy:
curl.exe --max-time 5 http://172.16.6.10
curl.exe --max-time 5 http://172.16.6.20
Kết quả kỳ vọng: truy cập GENERAL_SITE nhận HTML từ Nginx; truy cập JOB_SITE bị chặn hoặc timeout. Chỉ xác nhận kết quả thực tế sau khi đã chạy kiểm thử trên máy.
4. Nội dung mở rộng của LAB6
- LDAP/Active Directory: pfSense có thể sử dụng LDAP để xác thực người dùng từ AD.
- Captive Portal: kiểm soát truy cập người dùng trên LAN qua trang đăng nhập.
- Schedule: giới hạn thời gian áp dụng firewall rule.
- Firewall Log và State Table: hỗ trợ kiểm tra lưu lượng và trạng thái kết nối.
Các mục mở rộng trên được trình bày theo mục tiêu bài lab, không mặc định là đã kiểm thử thành công.
5. Nội dung báo cáo
Báo cáo Word trình bày phần thiết lập môi trường, cấu hình Nginx, Aliases, Firewall Rules, kết quả kiểm thử và 12 câu hỏi lý thuyết của LAB6.
Minh chứng: Hình ảnh trong tài liệu cần được đối chiếu với kết quả chạy thực tế. Không sử dụng hình của người khác như bằng chứng mình đã trực tiếp thực hiện.
6. Lưu ý khi nộp
- Không đăng mật khẩu pfSense, tài khoản AD hoặc dữ liệu nhạy cảm.
- Không đưa toàn bộ máy ảo VMware lên GitHub.
- Kiểm tra lại IP, firewall rule và kết quả thực tế trước khi nộp.