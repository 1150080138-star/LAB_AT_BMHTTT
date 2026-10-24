LAB 7 – Xây dựng chính sách Web Protection trên pfSense + Web Proxy
Môn học: An toàn hệ thống thông tin
Sinh viên: Trần Thị Ngọc Huyền
MSSV: 1150080138
Lớp: 11CNPM2
link youtub: https://youtu.be/iVJmVOCzBIM
1. Mục tiêu
Triển khai mô hình bảo vệ truy cập web bằng pfSense, Squid, E2Guardian và ClamAV trong môi trường VMware. Bài thực hành tập trung vào việc kiểm soát truy cập HTTP qua proxy, ngăn truy cập trực tiếp bỏ qua proxy, xây dựng chính sách lọc website, lịch truy cập và kiểm tra nhật ký hệ thống.
2. Mô hình mạng
Thành phần	Địa chỉ IP	Vai trò
pfSense LAN	10.6.0.1	Gateway, firewall
DC01	10.6.0.10	DNS nội bộ (theo mô hình đề bài)
Client-A	10.6.0.21	Client kiểm thử (theo mô hình đề bài)
Client-B	10.6.0.22	Client kiểm thử (theo mô hình đề bài)
WEB-PROXY	10.6.0.30	Squid, E2Guardian, ClamAV
DMZ-WEB	172.16.6.10	Web server mô phỏng


Luồng truy cập dự kiến: Client → E2Guardian (10.6.0.30:8080) → Squid (3128) → pfSense → DMZ-WEB / Internet.
3. Website kiểm thử
Website	Mục đích
normal.lab7.test	Truy cập web bình thường qua proxy
jobs.lab7.test	Hard Block
streaming.lab7.test	Warning / Temporary Bypass
education.lab7.test	Whitelist
download.lab7.test	Kiểm thử tải file và EICAR


4. Nội dung thực hành và minh chứng
- H1–H3: Môi trường WEB-PROXY, kết nối mạng và dịch vụ Squid/E2Guardian/ClamAV.
- H4–H5: Website mô phỏng và truy cập qua proxy.
- H6–H7: pfSense Firewall Rules và kiểm thử chặn truy cập trực tiếp không qua proxy.
- H8: Chính sách Hard Block cho jobs.lab7.test.
- H9: Warning và Temporary Bypass cho streaming.lab7.test.
- H10: Whitelist cho education.lab7.test.
- H11: Schedule LAB7_ENT_60M trên pfSense.
- H12: Kiểm thử ClamAV và EICAR.
- H13: Nhật ký E2Guardian, Squid, pfSense và ClamAV.
Lưu ý về kết quả: Báo cáo và ảnh minh chứng phản ánh quá trình thực hành thực tế. Các chức năng Hard Block, Warning/Bypass, Whitelist, kiểm thử EICAR qua proxy và giới hạn dung lượng file không được coi là đã đạt nếu chưa có kết quả kiểm thử xác nhận. Việc tạo Schedule cũng chưa đủ để kết luận chính sách theo danh mục website đã hoạt động.

5. Tài liệu và hình ảnh
- Báo cáo: 11CNPM2-Lab7_1150080138-TranThiNgocHuyen_.docx
- Ảnh minh chứng: thư mục Evidence/ (hoặc các ảnh PNG trích từ báo cáo Word).
Cấu trúc repository gợi ý:
LAB7/
├── README.md
├── 11CNPM2-Lab7_1150080138-TranThiNgocHuyen_.docx
└── Evidence/
    ├── H01.png
    ├── H02.png
    └── ...
6. Nhật ký cần kiểm tra
sudo tail -n 30 /var/log/e2guardian/access.log
sudo tail -n 30 /var/log/squid/access.log
sudo journalctl -u clamav-daemon -n 30 --no-pager
Trên pfSense: Status → System Logs → Firewall.