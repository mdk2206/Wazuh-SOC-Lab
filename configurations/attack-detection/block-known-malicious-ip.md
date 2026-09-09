# Cấu hình chặn các IP độc hại đã biết (Malicious IP Blocking)
## Mục tiêu: Tích hợp danh sách các địa chỉ IP độc hại (Blacklist) từ AlienVault vào Wazuh Server. Khi phát hiện các kết nối từ các IP này đến máy chủ Web trên máy Agent, Wazuh sẽ tự động kích hoạt tính năng Active Response để chặn đứng kết nối từ IP đó trong vòng 60 giây.
### Phần 1. Cấu hình trên Ubuntu Agent (Sử dụng Nginx)
Bước 1: Cài đặt và khởi động Nginx

<img width="992" height="297" alt="image" src="https://github.com/user-attachments/assets/acb99dc9-dfc6-400f-a7f7-4440a99e615b" />

Bước 2: Cấu hình Wazuh giám sát log truy cập Nginx

<img width="455" height="95" alt="image" src="https://github.com/user-attachments/assets/8a921fa2-0cbf-4994-a962-f7dbd3a69413" />

### Phần 2. Cấu hình trên Windows Agent (Sử dụng Apache)
Bước 1: Cài đặt Visual C++ và Apache

Bước 2: Khởi chạy Apache trên Windows

Bước 3: Cấu hình Wazuh giám sát log truy cập Apache


<img width="455" height="92" alt="image" src="https://github.com/user-attachments/assets/5e79afb4-adc4-424f-b8fd-841efaa82d66" />

### Phần 3: Tích hợp Danh sách đen (Blacklist) trên Wazuh Server
Bước 1: Tải cơ sở dữ liệu AlienVault IP Reputation

<img width="848" height="205" alt="image" src="https://github.com/user-attachments/assets/ca316b7e-1fe2-48ce-84af-35c50be8e7b7" />

Bước 2: Chuyển đổi định dạng Blacklist sang chuẩn CDB

<img width="847" height="265" alt="image" src="https://github.com/user-attachments/assets/50a57da9-ac5f-451e-84d3-22a1c4c5ea45" />

Bước 3: Khai báo danh sách vào cấu hình chung

<img width="395" height="27" alt="image" src="https://github.com/user-attachments/assets/67cb321c-47dd-4235-b67d-957b3aaf884c" />

Bước 4: Tạo Rules cảnh báo

<img width="780" height="156" alt="image" src="https://github.com/user-attachments/assets/71b07606-08b4-4562-b88f-68a29b334ff7" />

Bước 5: Cấu hình Active Response ngăn chặn tấn công, chặn IP trên Agent Ubuntu (firewall-drop) và chặn IP trên Agent Windows (netsh)


<img width="357" height="217" alt="image" src="https://github.com/user-attachments/assets/e0f08a41-70d1-49ae-9467-cf90e06defaa" />











