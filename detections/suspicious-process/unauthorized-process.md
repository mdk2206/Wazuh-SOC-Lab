# Kết quả phát hiện tiến trình trái phép (Unauthorized Process)

## 1. Tổng quan chuỗi sự kiện

Kịch bản sử dụng ncat để tạo một backdoor ngầm trên bộ nhớ RAM (port 8000). Thông qua cơ chế Command Monitoring định kỳ chạy lệnh ps, Wazuh Agent đã chụp lại danh sách tiến trình và đẩy về Manager. Dựa trên dữ liệu log thực tế, bộ luật Custom Ruleset đã chứng minh hiệu quả của phương pháp giám sát Process-Centric: đối chiếu trực tiếp chuỗi lệnh trên RAM với đường cơ sở Baseline để tóm gọn hành vi vi phạm trước khi tiến trình tự hủy.

## 2. Phân tích chi tiết luồng cảnh báo

Sự kiện : Phát hiện công cụ mạng mở cổng trái phép

Rule ID: 100051

Rule Level: 7

Description: Unauthorized process detected on Linux: netcat/ncat listening for incoming connections.

MITRE: T1059, T1095

Dựa vào trường full_log ,ta có thể bóc tách chính xác toàn bộ ngữ cảnh thực thi tại thời điểm quét. Hệ thống đã chụp lại được toàn cảnh từ tiến trình gốc do người dùng khởi chạy (PID 21786: /bin/bash ./simulate_unauthorized_proc.sh), tiến trình chờ (PID 21787: timeout 30s), và quan trọng nhất là tiến trình con đang trực tiếp vi phạm chính sách (PID 21790: ncat -l 127.0.0.1 8000).

<img width="1106" height="846" alt="image" src="https://github.com/user-attachments/assets/e8309760-9a0b-415b-a313-bc42123f1862" />

<img width="1202" height="857" alt="image" src="https://github.com/user-attachments/assets/7406967c-b4d6-4933-8fea-e41d39cca31a" />

<img width="1090" height="863" alt="image" src="https://github.com/user-attachments/assets/8415055e-2d58-4e02-8a32-b5990efbfc9f" />

<img width="1072" height="853" alt="image" src="https://github.com/user-attachments/assets/e8a21519-f33b-4d59-b765-bd58c473e3d4" />

<img width="1358" height="857" alt="image" src="https://github.com/user-attachments/assets/e5991183-9310-482e-8dfd-75833d7b865d" />

<img width="1322" height="857" alt="image" src="https://github.com/user-attachments/assets/585f3470-bf1b-4b7c-982c-dbeebd848bf9" />

<img width="1482" height="840" alt="image" src="https://github.com/user-attachments/assets/23979020-05fa-498f-be59-a56e4bc7d999" />







