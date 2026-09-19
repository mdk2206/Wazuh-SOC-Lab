# Kết quả phát hiện tệp nhị phân đáng ngờ

## 1. Tổng quan Chuỗi Sự kiện

Kịch bản tấn công leo thang đặc quyền bắt đầu bằng việc kẻ tấn công tải một tệp nhị phân đáng ngờ (linpeas.sh) vào máy chủ và cấp quyền thực thi cho nó. Nhờ tính năng Syscheck kết hợp Whodata (auditd) giám sát liên tục ở chế độ realtime, Wazuh Agent không chỉ bắt quả tang hành vi tải file và đổi quyền, mà còn tóm gọn toàn bộ chuỗi hành vi rà quét hệ thống sâu, thao tác với tệp tin tạm  và thăm dò lõi bảo mật của LinPEAS trong giai đoạn hậu khai thác.

2. Phân tích chi tiết luồng cảnh báo

Sự kiện 1: Tệp tin lạ được tải xuống hệ thống

Rule ID: 100400

Rule Level: 7

Description: File added to /document directory. Preparing for VirusTotal/Yara scan.

MITRE ATT&CK: T1105 (Ingress Tool Transfer)

Tiến trình thực thi: /usr/bin/wget

Bối cảnh: Ngay khi lệnh tải file hoàn tất, tính năng Syscheck lập tức phát hiện tệp tin linpeas.sh xuất hiện trong thư mục nhạy cảm. Hệ thống kích hoạt luật tùy chỉnh 100400 để chuẩn bị mồi quét mã độc.

<img width="1220" height="722" alt="image" src="https://github.com/user-attachments/assets/e471b54c-3e8b-4d1b-b9bc-34b09ed341d2" />

<img width="987" height="637" alt="image" src="https://github.com/user-attachments/assets/0667f18b-2012-4997-8aaf-47bef87375c3" />

<img width="1207" height="817" alt="image" src="https://github.com/user-attachments/assets/2c6f1dc1-85d5-4b57-91c2-e57bb6207d39" />

Sự kiện 2: Kiểm tra tình báo mối đe dọa (VirusTotal Integration)

Rule ID: 87104

Rule Level: 3

Description: VirusTotal: Alert - /document/suspicious-binary/linpeas.sh - No positives found

Bối cảnh: Ngay khi Rule 100400 nổ ra, Wazuh Manager tự động trích xuất mã băm gửi cho API VirusTotal. Tuy nhiên, kết quả trả về là found: 1 nhưng positives: 0 (0/59 engine phát hiện mã độc)

<img width="1417" height="741" alt="image" src="https://github.com/user-attachments/assets/89cff000-eea1-4f53-b69a-ef02ca3ce702" />

<img width="1345" height="532" alt="image" src="https://github.com/user-attachments/assets/1d088d98-1753-4695-9006-f79c0bac53d2" />

Sự kiện 3: Chỉnh sửa đặc quyền thực thi

Rule ID: 550

Rule Level: 7

Description: Integrity checksum changed.

MITRE ATT&CK: T1222 (File and Directory Permissions Modification)

Tiến trình thực thi: /usr/bin/chmod

Bối cảnh: Kẻ tấn công dùng lệnh chmod +x để cấp quyền thực thi. Cảnh báo vi phạm toàn vẹn lập tức nổ. Trên Dashboard, trường syscheck.perm_after hiển thị cờ x  đã được bật. Đặc biệt, Whodata chỉ đích danh lệnh /usr/bin/chmod.

<img width="1300" height="827" alt="image" src="https://github.com/user-attachments/assets/e3d050b1-8b76-49b1-8417-711e4a5ffbb6" />

<img width="1210" height="782" alt="image" src="https://github.com/user-attachments/assets/28a1acd5-a6d2-40dd-86c2-f43fd9f21f92" />

<img width="1195" height="668" alt="image" src="https://github.com/user-attachments/assets/f66990e2-b07f-4a1a-afeb-10fb8f2ed333" />

Sự kiện 4: Các hành vi rà quét và tạo tệp tin tạm 

Rule ID: 554 (File added) & 553 (File deleted)

Rule Level: 5 và 7

MITRE ATT&CK: T1082 (System Information Discovery), T1070.004 (File Deletion)

Thư mục ghi nhận: /tmp/

Các tệp tin tiêu biểu: /tmp/linpeas_host_checker_*.err, /tmp/cf31-probe-*.py, /tmp/syscheck-squashfs-*/canary.txt,...

Bối cảnh: Ngay khi chạy, LinPEAS tự động sinh ra hàng loạt tệp tin kiểm tra trong thư mục /tmp nhằm dò tìm lỗ hổng môi trường (Cloud, Kernel, Squashfs Mount). Sau khi test xong, nó lập tức xóa bỏ để phi tang dấu vết. Nhờ cấu hình giám sát realtime, Wazuh đã bắt trọn vẹn vòng lặp tạo/xóa này dù file chỉ tồn tại trên ổ cứng chưa tới 1 giây.

<img width="1230" height="827" alt="image" src="https://github.com/user-attachments/assets/5ea85396-ec03-42a5-aaef-aa7d2f6d826e" />

<img width="1185" height="802" alt="image" src="https://github.com/user-attachments/assets/9a1f7e5e-271f-419b-899e-342a36f23c1b" />

<img width="1192" height="848" alt="image" src="https://github.com/user-attachments/assets/043d2b00-bd7e-4abf-9bd1-e3902c80a135" />

<img width="1226" height="855" alt="image" src="https://github.com/user-attachments/assets/1e7d9c20-dad9-45b8-8444-6968b5469be9" />

Sự kiện 5: Rà quét Module bảo mật và gây tràn nhật ký

Rule ID: 80730 (Auditd: SELinux/AppArmor) & 591 (Log file rotated)

Rule Level: 3

MITRE ATT&CK: T1562.001 (Impair Defenses)

Tiến trình thực thi: /usr/sbin/apparmor_parser

Bối cảnh: LinPEAS tiến hành thăm dò sâu vào lõi bảo mật của Ubuntu. Các log Auditd liên tục nổ khi công cụ này cố gắng đọc cấu hình của AppArmor, Snap và hệ thống ảo hóa LXD nhằm tìm đường thoát khỏi hộp cát Sandbox Escape. Khối lượng rà quét này tạo ra hàng ngàn sự kiện auditd, làm file /var/log/audit/audit.log đầy tràn nhanh chóng và hệ thống phải lập tức xoay vòng nhật ký.

<img width="1388" height="675" alt="image" src="https://github.com/user-attachments/assets/50c68515-798f-46b1-941e-73ab4cecac10" />

<img width="1500" height="792" alt="image" src="https://github.com/user-attachments/assets/b0b4af20-6480-44a7-8e85-eaa93ca4339f" />

<img width="933" height="217" alt="image" src="https://github.com/user-attachments/assets/dd027c65-b25f-40d0-882f-1ab93498d661" />

Sự kiện 6: Phát hiện bất thường cấp độ hệ thống 

Rule ID: 510

Rule Level: 7

Description: Host-based anomaly detection event (rootcheck).

MITRE ATT&CK: T1514 (Elevated Execution with Rootkit)

Module: rootcheck

Bối cảnh: Trong quá trình LinPEAS thực thi các bài test khai thác lỗ hổng và can thiệp vào môi trường hệ thống, module Rootcheck của Wazuh chuyên quét mã độc ẩn mình - Rootkit đã bị kích hoạt.

<img width="1447" height="757" alt="image" src="https://github.com/user-attachments/assets/db00184b-5fa4-42c2-9653-0e4f8290d358" />

<img width="981" height="407" alt="image" src="https://github.com/user-attachments/assets/e813ffac-912b-4b6a-a6a9-17acb84dc738" />
























 
