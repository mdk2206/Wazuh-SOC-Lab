# Cấu hình giám sát tính toàn vẹn của tệp (FIM)
## Mục tiêu
* Sử dụng module **Syscheck (FIM)** của Wazuh để giám sát các hành vi tạo mới, chỉnh sửa, hoặc xóa tệp tin trong các thư mục quan trọng.
* Kích hoạt tính năng **Whodata** để ghi nhận chính xác người dùng (user) nào đã thực hiện thay đổi và tiến trình (process) nào được sử dụng.
* Kích hoạt tính năng **Report Changes** để lưu lại và hiển thị chi tiết nội dung văn bản trước và sau khi bị chỉnh sửa (diff).
## Phần 1: Cấu hình trên Ubuntu Agent
Bước 1: Tạo thư mục và tệp tin mục tiêu

<img width="1033" height="190" alt="image" src="https://github.com/user-attachments/assets/fdd0b8dd-bd26-4dbf-955f-de5a2190730d" />

Bước 2: Cài đặt công cụ Auditd (Bước này mình đã làm ở những phần trước rồi)

Bước 3: Cấu hình giám sát thư mục bằng Whodata và Report Changes

<img width="726" height="27" alt="image" src="https://github.com/user-attachments/assets/e5eb6b8d-fe14-4214-8835-2e654b7e39a3" />

## Phần 2: Cấu hình trên Windows Agent

Để tính năng whodata hoạt động trên Windows, hệ điều hành cần được bật tính năng Audit Object Access (Giám sát truy cập đối tượng) và cấu hình SACL cho thư mục

Bước 1: Bật Audit Object Access trên Windows

<img width="642" height="58" alt="image" src="https://github.com/user-attachments/assets/73691dce-55d1-4844-aded-c3d7663450d6" />

Bước 2: Tạo tệp danhsachkhachhang.txt

<img width="572" height="195" alt="image" src="https://github.com/user-attachments/assets/f5048af8-d02c-4767-a6b3-d4c93ceb7266" />

Bước 3: Thiết lập SACL để Windows ghi log khi có thay đổi

<img width="942" height="600" alt="image" src="https://github.com/user-attachments/assets/2b1ed75c-cba8-4cb4-b64a-20796b96b79b" />

Bước 4: Cấu hình Wazuh Agent bằng Whodata và Report Changes

<img width="787" height="25" alt="image" src="https://github.com/user-attachments/assets/8b294b1e-d92b-4405-85e3-03cb2aa7e685" />





