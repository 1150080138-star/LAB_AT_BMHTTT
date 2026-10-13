# LAB 5 – THIẾT LẬP TƯỜNG LỬA pfSense

**Môn học:** Thực hành An toàn hệ thống thông tin  
**Sinh viên:** Trần Thị Ngọc Huyền  
**MSSV:** 1150080138  
**Lớp:** 11CNPM2  
**Môi trường:** pfSense CE 2.7.2-RELEASE (amd64), VMware Workstation 17.5  
**Video minh họa:** [Xem trên YouTube](https://youtu.be/SijcQoZyLGk)

## 1. Mục tiêu

- Xây dựng mô hình tường lửa pfSense bảo vệ hai vùng mạng LAN và DMZ.
- Thiết lập WAN, LAN, DMZ, Outbound NAT và các quy tắc Firewall.
- Sử dụng Windows Server và Ubuntu Server làm các máy kiểm thử.
- Thực hành các tình huống kiểm soát lưu lượng, cô lập mạng và chuyển tiếp cổng.

## 2. Môi trường và mô hình mạng

| Thiết bị / Interface | Vai trò | Địa chỉ IP | Gateway | DNS |
|---|---|---|---|---|
| pfSense WAN | Kết nối mạng ngoài | DHCP qua Bridged | Theo WAN | Theo cấu hình WAN |
| pfSense LAN | Firewall cho LAN | `10.0.0.1/8` | — | — |
| pfSense DMZ | Firewall cho DMZ | `172.16.0.1/16` | — | — |
| Domain Controller | Máy chủ trong LAN | `10.0.0.2/8` | `10.0.0.1` | `10.0.0.2` |
| LAN-Test | Máy kiểm thử trong LAN | `10.0.0.3/8` | `10.0.0.1` | DNS upstream phù hợp |
| DMZ-Web | Máy chủ Web trong DMZ | `172.16.0.2/16` | `172.16.0.1` | Ban đầu để trống; `8.8.8.8` sau khi có rule cho phép |

**Ánh xạ mạng trong VMware:**

- **WAN:** Network Adapter 1 – Bridged.
- **LAN:** Network Adapter 2 – Custom `VMnet1` (Host-only, `10.0.0.0/8`, tắt VMware DHCP).
- **DMZ:** Network Adapter 3 – Custom `VMnet2` (mạng riêng, `172.16.0.0/16`, tắt VMware DHCP).

Không để các subnet WAN, LAN và DMZ chồng lấn nhau.

## 3. Nội dung thực hành

### 3.1. Cài đặt pfSense

- Sử dụng pfSense CE `2.7.2-RELEASE` (amd64).
- Kiểm tra SHA-256 của file `.iso.gz` trước khi giải nén:

  ```text
  883fb7bc64fe548442ed007911341dd34e178449f8156ad65f7381a02b7cd9e4
  ```

- Tạo VM pfSense với 2 GB RAM, 2 vCPU và ổ đĩa 16–20 GB.
- Gán ba card mạng WAN/LAN/DMZ; đặt LAN `10.0.0.1/8` và DMZ `172.16.0.1/16`.

### 3.2. Cấu hình máy chủ và DMZ

- **Domain Controller:** Windows Server 2019/2022, địa chỉ `10.0.0.2/8`, cài AD DS và DNS, cấu hình DNS Forwarder phù hợp.
- **DMZ-Web:** Windows Server trong VMnet2, địa chỉ `172.16.0.2/16`, cài IIS bằng PowerShell:

  ```powershell
  Install-WindowsFeature Web-Server -IncludeManagementTools
  curl.exe http://localhost
  ```

### 3.3. Outbound NAT và LAN Rules

- Vào **Firewall → NAT → Outbound** để kiểm tra chế độ Automatic Outbound NAT.
- Vào **Firewall → Rules → LAN**, vô hiệu hóa hai rule Default allow LAN IPv4/IPv6, giữ nguyên Anti-Lockout Rule.
- Tạo rule LAN do sinh viên quản lý: **Pass – IPv4 – LAN net → Any**.
- Sau khi thay đổi rule, thực hiện **Apply Changes** và **Reset States** trước khi kiểm thử lại.

## 4. Các tình huống Firewall

| Tình huống | Cấu hình / phép thử | Kết quả mong đợi |
|---|---|---|
| 1. Chặn ICMP, cho phép Web/DNS | Block ICMP; Pass DNS (53); Pass HTTP/HTTPS (80/443) | Ping thất bại, DNS và Web hoạt động |
| 2. Chỉ một máy LAN ra Internet | Pass `10.0.0.2 → Any` đặt trên Block `LAN net → Any` | DC `10.0.0.2` được phép; LAN-Test `10.0.0.3` bị chặn |
| 3. Cô lập DMZ khỏi LAN | Kiểm thử DMZ → LAN trước khi Block; sau đó Block `DMZ net → LAN net` | Truy cập LAN thành công ở baseline, thất bại sau khi Block |
| 4. Port Forward đến DMZ-Web | WAN TCP `8080` → `172.16.0.2:80` | Truy cập được IIS qua địa chỉ WAN và cổng 8080 |
| 5. Firewall Logging | Bật log trên rule Block, tạo lưu lượng bị chặn | Log ghi nhận lưu lượng và rule tương ứng |

**Lưu ý:** Bảng trên mô tả kịch bản và **kết quả mong đợi**, không khẳng định tất cả kịch bản đã kiểm thử thành công. Đối chiếu ảnh chụp và kết quả thực tế trong báo cáo Word trước khi kết luận.

Các lệnh kiểm thử minh họa trên Windows Server:

```powershell
ping 8.8.8.8
Resolve-DnsName example.com -Server 8.8.8.8
curl.exe -4 https://example.com
```

## 5. Lỗi thường gặp và cách xử lý

- **Rule thay đổi nhưng kết quả chưa đổi:** Apply Changes và Reset States, sau đó tạo kết nối kiểm thử mới.
- **DMZ không ra Internet:** Kiểm tra gateway trên DMZ, Automatic NAT và rule Pass DMZ → Internet.
- **DMZ ping DC thất bại ngay ở baseline:** Kiểm tra Windows Firewall của DC và quyền nhận ICMP.
- **Port Forward không hoạt động:** Kiểm tra IIS trên máy DMZ-Web trước, sau đó kiểm tra NAT rule, associated filter rule và IP WAN.

## 6. Báo cáo và minh chứng

Báo cáo Word: [`LAB5_CNPM2_1150080138_TranThiNgocHuyen.docx`](./LAB5_CNPM2_1150080138_TranThiNgocHuyen.docx)

Báo cáo trình bày cấu hình môi trường, mô hình mạng, ảnh pfSense/VMware và phần thực hành các tình huống Firewall. Các ảnh kiểm thử cần phản ánh đúng kết quả thực tế, đặc biệt với tình huống cô lập DMZ phải có minh chứng **trước và sau khi áp dụng Block**.

## 7. Lưu ý sử dụng

pfSense CE 2.7.2 là phiên bản được dùng theo tài liệu LAB5 trong môi trường thực hành. Không sử dụng cấu hình lab này trực tiếp cho hệ thống production hoặc mạng công cộng.
