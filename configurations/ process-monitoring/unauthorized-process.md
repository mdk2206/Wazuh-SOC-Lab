# Cấu hình phát hiện tiến trình trái phép
## Mục tiêu: Thiết lập Wazuh Agent thu thập danh sách các tiến trình đang chạy định kỳ trên cả Ubuntu và Windows Agent. Sau đó, cấu hình Wazuh Server phân tích dữ liệu này nhằm phát hiện và cảnh báo khi có một tiến trình trái phép (như công cụ Netcat mở cổng lắng nghe kết nối backdoor) xuất hiện trên hệ thống.
## Phần 1: Cấu hình thu thập tiến trình trên Ubuntu Server
Bước 1: Cấu hình thu thập danh sách tiến trình

Thêm đoạn cấu hình sau để yêu cầu Agent định kỳ chạy lệnh ps để thu thập danh sách tiến trình

<img width="450" height="92" alt="image" src="https://github.com/user-attachments/assets/8875d135-786d-4a05-9153-3cdb254ab30c" />

## Phần 2: Cấu hình thu thập tiến trình trên Windows Agent
Bước 1: Bổ sung cấu hình thu thập bằng tasklist, sử dụng <alias> để định danh là process list nhằm tái sử dụng bộ quy tắc trên Wazuh Server

<img width="432" height="127" alt="image" src="https://github.com/user-attachments/assets/ed88e8cf-6928-48b4-bd48-4ca466728d32" />

## Phần 3: Cấu hình trên Wazuh Server

Thêm các bộ quy tắc sau để giám sát tiến trình

<img width="991" height="456" alt="image" src="https://github.com/user-attachments/assets/d10098c2-4b3d-4636-ae62-fe9a4317a50a" />







