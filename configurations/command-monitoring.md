# Cấu hình giám sát lệnh thực thi (Command Monitoring)
## Mục tiêu
Thiết lập khả năng giám sát các câu lệnh được thực thi trên hai hệ điều hành:
1. Ubuntu Server: Sử dụng auditd để theo dõi các lệnh hệ thống (đặc biệt là đặc quyền root/sudo) và đối chiếu với danh sách lệnh đáng ngờ trên Wazuh Server.
2. Windows Client: Kích hoạt chính sách Audit Process Creation để ghi nhận chi tiết (command line) mọi tiến trình do người dùng chạy (cmd, powershell).
## Phần 1: Cấu hình trên máy Ubuntu Agent
Bước 1: Cài đặt và khởi chạy Auditd

<img width="950" height="385" alt="image" src="https://github.com/user-attachments/assets/3970cbeb-32c1-45d2-bfec-8142fbffef99" />


Bước 2: Thiết lập luật giám sát lệnh cho Auditd

<img width="892" height="61" alt="image" src="https://github.com/user-attachments/assets/1ffe0766-5f54-47da-a985-796348f96255" />

Bước 3: Cấu hình Wazuh Agent thu thập log của Auditd

<img width="432" height="78" alt="image" src="https://github.com/user-attachments/assets/46a26235-8ed0-46f6-96ad-f5acbf0bd118" />

## Phần 2: Cấu hình trên máy chủ Wazuh Server
Bước 1: Tạo danh sách các chương trình đáng ngờ (CDB List)

<<img width="956" height="221" alt="image" src="https://github.com/user-attachments/assets/c9816421-d59f-439b-8cd1-334386c8daa9" />


Bước 2: Khai báo danh sách vào cấu hình Wazuh Server

<img width="435" height="25" alt="image" src="https://github.com/user-attachments/assets/4aef2dd5-3c65-4ffc-8e4f-e6432ec7bd7f" />

Bước 3: Tạo Rule cảnh báo

<img width="913" height="181" alt="image" src="https://github.com/user-attachments/assets/6688851e-c7cb-40c4-aecc-2fc1d89a3217" />

## Phần 3: Cấu hình trên máy Windows Agent
Bước 1: Bật chính sách Audit Process Creation

<img width="860" height="221" alt="image" src="https://github.com/user-attachments/assets/a65d73b0-dc51-4821-9edb-fd0618223ad5" />

Bước 2: Xác nhận thu thập log bảo mật trên Wazuh Agent

<img width="507" height="65" alt="image" src="https://github.com/user-attachments/assets/5c89dee1-3b38-405d-8f30-6273510a34c9" />












