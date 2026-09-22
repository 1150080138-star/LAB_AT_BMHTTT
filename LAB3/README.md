# LAB3 - NHẬN DIỆN VÀ ỨNG PHÓ CÁC MỐI ĐE DỌA ĐẾN AN TOÀN THÔNG TIN

## 1. Thông tin sinh viên

- Họ và tên: Trần Thị Ngọc Huyền
- MSSV: 1150080138
- Lớp: 11CNPM2
- Tên lab: LAB3 - Nhận diện và ứng phó các mối đe dọa đến an toàn thông tin

## 2. Môi trường thực hành

- Máy ảo: VMware Workstation 17.6.4
- Hệ điều hành: Windows 11 64-bit
- Cấu hình mạng: Host-only
- CPU máy ảo: 2 vCPU
- RAM máy ảo: 6 GB
- Ổ đĩa máy ảo: 64 GB
- Microsoft Defender Antivirus: Real-time Protection và Tamper Protection được bật
- Windows Defender Firewall: Enabled
- Python: Python 3.x 64-bit
- Wireshark: Wireshark 4.6.8 + Npcap
- Sysmon: Microsoft Sysinternals Sysmon
- Autoruns: Microsoft Sysinternals Autoruns
- Process Explorer: Microsoft Sysinternals Process Explorer
- Thư mục thực hành: C:\LAB3
- Gói dữ liệu: LAB3_Threats_Assets
- Local test target: 127.0.0.1:8080

## 3. Cách dựng môi trường

1. Tạo máy ảo Windows 11 trên VMware Workstation.
2. Cấu hình VM với 2 vCPU, 6 GB RAM và 64 GB ổ đĩa.
3. Cấu hình Network Adapter ở chế độ Host-only.
4. Tạo thư mục C:\LAB3 để lưu dữ liệu và evidence.
5. Giải nén và kiểm tra gói LAB3_Threats_Assets.
6. Cài đặt/kiểm tra Python, Wireshark + Npcap.
7. Chuẩn bị Sysmon, Autoruns và Process Explorer.
8. Kiểm tra Microsoft Defender và Windows Defender Firewall.
9. Thu thập baseline trước khi thực hiện các tình huống kiểm thử.

## 4. Các tình huống đã thực hiện

### TH1 - Asset, Vulnerability, Threat, Risk
Phân biệt và xác định Asset, Vulnerability, Threat và Risk trong môi trường thực hành.

Kết quả: PASS

### TH2 - EICAR và Microsoft Defender
Sử dụng EICAR Test File để kiểm tra khả năng phát hiện của Microsoft Defender. Defender phát hiện và quarantine tệp kiểm thử.

Kết quả: PASS

### TH3 - Password và Windows Event Log
Tạo tài khoản thử nghiệm lab3user, thực hiện các tình huống đăng nhập và kiểm tra Windows Security Event Log, bao gồm Event ID 4625.

Kết quả: PASS

### TH4 - Persistence và Listener cục bộ
Cài đặt Sysmon và quan sát Process Create Event ID 1. Sử dụng Autoruns để quan sát LAB3_Run_Demo và Process Explorer để kiểm tra tiến trình liên quan. Listener của bài thực hành được giới hạn tại 127.0.0.1:8080.

Kết quả: PASS

### TH5 - HTTP và HTTPS
Sử dụng Wireshark để capture traffic của chính VM. Quan sát HTTP plaintext với dữ liệu TRAINING_ONLY và so sánh với traffic HTTPS/TLS trên TCP port 443.

Kết quả: PASS

### TH6 - DoS/DDoS/Mail Bombing
Chạy local_load_test.py trong môi trường local với target 127.0.0.1:8080 và phân tích dữ liệu mẫu offline. Không thực hiện DoS/DDoS hoặc mail bombing trên hệ thống bên ngoài.

Kết quả: PASS

### TH7 - Social Engineering/Phishing
Phân tích phishing_email.txt và các tình huống social engineering trong dữ liệu mẫu. Không sử dụng email hoặc dữ liệu thật.

Kết quả: PASS

## 5. Cleanup - Recover - Verify

Sau khi hoàn thành bài thực hành:

- Xóa LAB3_Run_Demo.
- Xóa LAB3_Persistence_Demo/Scheduled Task của bài thực hành.
- Dừng listener tại port 8080.
- Xóa tài khoản thử nghiệm lab3user.
- Kiểm tra Microsoft Defender vẫn được bật.
- Kiểm tra lại các artifact sau quá trình cleanup.
- Tính SHA-256 cho evidence cần nộp.

Kết quả: PASS

## 6. Lỗi gặp phải và cách khắc phục

### Lỗi đường dẫn LAB3_Threats_Assets

Ban đầu sử dụng sai đường dẫn đến thư mục lab3_assets nên PowerShell báo PathNotFound.

Cách khắc phục:
- Kiểm tra lại cấu trúc thư mục trong Downloads.
- Xác định đúng đường dẫn của LAB3_Threats_Assets.
- Copy thư mục lab3_assets vào C:\LAB3.
- Kiểm tra lại bằng Get-ChildItem.

Kết quả: Đã khắc phục.

### Lỗi Python

Ban đầu Windows 11 VM chưa nhận lệnh Python/local_load_test.py.

Cách khắc phục:
- Kiểm tra/cài đặt Python cho Windows.
- Kiểm tra Python PATH/App Execution Alias.
- Chạy lại script local_load_test.py.
- Script chạy thành công với target 127.0.0.1:8080.

Kết quả: Đã khắc phục.

## 7. Evidence

Các bằng chứng chính được thu thập trong quá trình thực hành gồm:

- H1 - VM và Windows Version
- H2 - Tool Versions
- H3 - Defender + Firewall Baseline
- H4 - Protection History EICAR
- H5 - Event ID 4625
- H6 - Sysmon Event ID 1
- H7 - Autoruns LAB3_Run_Demo
- H8 - Process Explorer Python
- H9 - HTTP Plaintext
- H10 - TLS/443
- H11 - Cleanup/Recovery Verification

Các file output/log trước khi đưa lên repository được kiểm tra và loại bỏ thông tin nhạy cảm.

Hash SHA-256 của evidence được lưu trong:

evidence_sha256.csv

## 8. An toàn thực hành

Toàn bộ hoạt động được thực hiện trong môi trường lab.

local_load_test.py chỉ được sử dụng với:

127.0.0.1:8080

Không thực hiện mail bombing, DDoS, spoofing, MITM chủ động hoặc các hoạt động tấn công lên hệ thống bên ngoài môi trường lab.

Không đưa mật khẩu, token/API key, cookie/session, email thật, dữ liệu cá nhân, installer, executable hoặc file bị Defender quarantine lên repository.

## 9. Kết luận

Qua LAB3, em đã thực hành quy trình Baseline → Observe → Detect → Contain → Recover → Verify và sử dụng các công cụ Microsoft Defender, Windows Event Log, Sysmon, Autoruns, Process Explorer và Wireshark để thu thập, phân tích các dấu vết liên quan đến an toàn thông tin.

Các tình huống thực hành được giới hạn trong môi trường VM/local và môi trường được kiểm tra, phục hồi sau khi hoàn thành bài.

Kết quả tổng thể: PASS