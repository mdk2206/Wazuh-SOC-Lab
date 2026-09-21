# Mô phỏng phát hiện thực thi lệnh nguy hiểm (Malicious Commands)

## Tổng quan Kịch bản

Thực thi lệnh nguy hiểm và lạm dụng các công cụ có sẵn là kỹ thuật phổ biến được kẻ tấn công sử dụng để trinh sát hệ thống, leo thang đặc quyền hoặc duy trì truy cập mà không cần tải thêm mã độc.

Trong kịch bản này, mình sẽ mô phỏng việc kẻ tấn công thực thi các công cụ dò quét mạng và quản trị hệ thống. Thông qua auditd trên Linux và chính sách Audit Process Creation trên Windows, Wazuh Agent sẽ bóc tách các dòng lệnh này, đối chiếu với danh sách đen (CDB List) và các luật tùy chỉnh để phát ra cảnh báo mức độ cao.

## Môi trường thực hiện

Target 1: Máy Ubuntu Agent (192.168.50.30) - Giám sát bằng auditd kết hợp CDB List.

Target 2: Máy Windows Agent (Win10Lab - 192.168.50.20) - Giám sát bằng Event 4688 (Process Creation).

Công cụ sử dụng: Các công cụ mạng (nmap, nc, tcpdump) và lệnh hệ thống Windows (whoami, net, powershell).

## Các bước thực hiện

Bước 1: Giám sát lệnh trinh sát trên Ubuntu Agent

Mình sẽ thực hiện dò quét mạng nội bộ bằng Nmap: nmap -sT 127.0.0.1

Mục đích: Kẻ tấn công sử dụng công cụ nmap để dò tìm các cổng dịch vụ đang mở trên máy chủ. Trong hệ thống Wazuh, nmap đã được phân loại là phần mềm đáng ngờ (mức độ orange/red) trong tệp CDB List suspicious-programs.

<img width="657" height="245" alt="image" src="https://github.com/user-attachments/assets/7708552f-d814-4dcc-85a9-318acc7bdbe2" />

Bước 2: Giám sát kỹ thuật LotL trên Windows Agent

Mình sẽ sử dụng trinh sát đặc quyền bằng lệnh hệ thống: whoami /all

Mục đích: Kẻ tấn công mở Command Prompt hoặc PowerShell để kiểm tra chi tiết quyền hạn, nhóm người dùng và các SID của tài khoản hiện tại nhằm tìm hướng leo thang đặc quyền. Lệnh này lợi dụng công cụ whoami.exe hợp lệ của Windows nên các trình diệt virus truyền thống thường bỏ qua.

<img width="1255" height="812" alt="image" src="https://github.com/user-attachments/assets/dff3303b-4ebc-420a-8af7-14a9d26be5c9" />





