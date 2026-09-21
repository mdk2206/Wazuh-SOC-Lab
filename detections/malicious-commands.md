# Kết quả phát hiện thực thi lệnh nguy hiểm (Malicious Commands)

## Tổng quan chuỗi sự kiện

Kịch bản tấn công theo hình thức lạm dụng công cụ (Living off the Land - LotL) và sử dụng các phần mềm mạng (Nmap, Netcat) được kẻ tấn công thực thi trực tiếp trên hệ thống để trinh sát hoặc tạo luồng kết nối ngược.

Thông qua việc giám sát ở tầng nhân hệ điều hành (auditd trên Linux với Syscall execve) và nhật ký bảo mật của Windows (Event ID 4688 - Process Creation), Wazuh Agent liên tục thu thập mọi hành vi khởi tạo tiến trình kèm theo tham số dòng lệnh và đẩy dữ liệu về Wazuh Server để phân tích, đối chiếu với danh sách đen (CDB List) và các luật tùy chỉnh.

## Phân tích chi tiết luồng cảnh báo

Trên Wazuh Dashboard, quá trình phát hiện các lệnh nguy hiểm được thể hiện qua các sự kiện bám sát theo hành vi trinh sát trên cả hai hệ điều hành:

Sự kiện 1: Thực thi công cụ trinh sát mạng trên Linux (Nmap)

Rule ID: 100210

Rule Level: 12

Description: Audit: Suspicious Command executed: nmap

MITRE ATT&CK: T1046 (Network Service Discovery)

Command: nmap -sT 127.0.0.1

Kết quả trả về: Syscall 59 (execve) thực thi thành công (exit=0).

Bối cảnh: Khi người dùng chạy lệnh nmap trong Terminal, hệ thống auditd lập tức tóm gọn tiến trình. Rule 100210 lập tức bùng nổ cảnh báo mức độ 12 để báo hiệu hệ thống đang bị quét cổng mạng.

<img width="1126" height="841" alt="image" src="https://github.com/user-attachments/assets/05734274-8a8d-4b06-a02e-d87f7f285092" />

<img width="1435" height="647" alt="image" src="https://github.com/user-attachments/assets/659b436e-0691-43bc-bed2-c22dd4dfed57" />

<img width="1462" height="777" alt="image" src="https://github.com/user-attachments/assets/dffc7762-0874-46eb-8cb1-b584d6ccaffc" />

Sự kiện 2: Thực thi lệnh thu thập thông tin đặc quyền trên Windows (Whoami)

Rule ID: 100211

Rule Level: 12

Description: Windows: Suspicious reconnaissance command detected: "C:\Windows\system32\whoami.exe" /all

MITRE ATT&CK: T1033 (System Owner/User Discovery)

Command: whoami /all

Kết quả trả về: Event ID 4688 (Process Creation - AUDIT_SUCCESS).

Bối cảnh: Kẻ tấn công hoặc người dùng nội bộ cố gắng liệt kê toàn bộ đặc quyền bảo mật và SID của tài khoản thông qua CMD hoặc PowerShell. Dù whoami.exe là file chuẩn của Windows, nhưng nhờ cấu hình Audit Process Creation, Wazuh đã trích xuất được toàn bộ tham số trong trường data.win.eventdata.commandLine. Rule 100211 quét trúng chuỗi tham số /all nguy hiểm đi kèm nên đã đẩy mức cảnh báo lên tối đa, bóc trần hành vi trinh sát đặc quyền.

<img width="1177" height="843" alt="image" src="https://github.com/user-attachments/assets/39cb6f70-ae10-4732-9d58-df10ba75cec0" />

<img width="1447" height="848" alt="image" src="https://github.com/user-attachments/assets/2a65f0dd-26c1-42a9-81a6-0b7b70dc61f3" />

<img width="1463" height="568" alt="image" src="https://github.com/user-attachments/assets/d1d7cccc-d552-419d-bf4b-53dfda01f6c8" />






