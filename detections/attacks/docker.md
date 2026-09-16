# Kết quả Phát hiện Docker Privilege Escalation

## 1. Tổng quan chuỗi sự kiện 

Kịch bản tấn công Docker (simulate_docker_escape.sh) được thực thi ngầm đã tạo ra một chuỗi các sự kiện liên hoàn trên hệ thống. 
Mặc dù kẻ tấn công đã cố tình sử dụng tham số -d để tàng hình và tự động xóa container, nhưng hệ thống Wazuh vẫn thu thập được bức tranh toàn cảnh thông qua sự phối hợp của 3 module giám sát: nhật ký xác thực (auth.log), hệ thống kiểm toán lõi (auditd), và module lắng nghe Docker (docker-listener).

## 2. Chi tiết sự kiện 

Sự kiện 1: Dấu hiệu thực thi kịch bản

Rule ID:5402

Rule Level:3

Description: Successful sudo to ROOT executed.

data.srcuser: khangg

data.command: ./simulate_docker_escape.sh

Đây được xem là bước đầu của cuộc tấn công. Kẻ tấn công đã sử dụng quyền sudo để chạy kịch bản mô phỏng. Bằng chứng rõ ràng nhất là auth.log đã ghi lại chính là tên file kịch bản độc hại ./simulate_docker_escape.sh.

<img width="1427" height="777" alt="image" src="https://github.com/user-attachments/assets/151a484f-bf28-4d46-863d-a35ef156c8d0" />

<img width="1053" height="857" alt="image" src="https://github.com/user-attachments/assets/5c75327d-d14f-48bf-8edf-6cf4af4e27c7" />



Sự kiện 2: Khởi tạo Container

Rule ID:87928

Rule Level:3 

Description: Docker: Network bridge connected

data.docker.Actor.Attributes.container:746d9a982019e7cc10d95144f3a6ede300f7c762cb6b41f044e315fdca2f4524

Bối cảnh: Ngay khi script được chạy, Docker tạo ra một container mới và gắn nó vào mạng bridge. Module docker-listener lập tức bắt được sự kiện này. Dù chỉ ở Level 3, nhưng Container ID được sinh ra ở bước này chính là chìa khóa để xâu chuỗi các hành vi tiếp theo.

<img width="1178" height="782" alt="image" src="https://github.com/user-attachments/assets/1b791ba1-acb0-482d-9d8b-e9c9007f249e" />

<img width="1193" height="577" alt="image" src="https://github.com/user-attachments/assets/7b85bc8f-4792-4225-8c96-b531aaca91cf" />



Sự kiện 3: Bằng chứng từ Kernel

Rule ID: 80710

Rule Level:10

Description: Auditd: Device enables promiscuous mode.

data.audit.dev: vethf1b0c94

full_log: ... comm="dockerd" exe="/usr/bin/dockerd" ...

Khi Docker khởi chạy container, nó buộc phải tạo ra một card mạng ảo để giao tiếp. Hành động này kích hoạt chế độ promiscuous mode ở tầng Kernel. Trình kiểm toán lõi auditd của Ubuntu đã phát ra một cảnh báo Level 10. Đây là cảnh báo cốt lõi báo hiệu có một tiến trình Docker đang can thiệp sâu vào hệ thống mạng vật lý.

<img width="1442" height="672" alt="image" src="https://github.com/user-attachments/assets/23b7e1e6-7331-44ee-8078-65d41dd58112" />

<img width="1415" height="790" alt="image" src="https://github.com/user-attachments/assets/5685bbec-a10c-4806-967f-404dc21b9e01" />

<img width="1173" height="403" alt="image" src="https://github.com/user-attachments/assets/9f9595d5-5a8d-4ec9-b1bf-19882b57c9c7" />

Sự kiện 4: Xóa dấu vết

Rule ID:87929

Rule Level:4

Description: Docker: Network bridge disconnected

data.docker.Actor.Attributes.container: 746d9a982019e7cc10d95144f3a6ede300f7c762cb6b41f044e315fdca2f4524 khớp hoàn toàn với Container ID ở Sự kiện 2.

Bối cảnh: Sự kiện này chốt lại toàn bộ kịch bản. Container độc hại bị ép ngắt kết nối mạng và tiêu hủy. Điểm đáng chú ý nhất là khoảng cách thời gian khi sự kiện Connect xảy ra lúc 20:40:04, và Disconnect lúc 20:40:14. Khoảng thời gian tồn tại chính xác 10 giây khớp hoàn toàn với tham số sleep 10 mà kẻ tấn công đã cấu hình để ngụy trang.

<img width="1167" height="800" alt="image" src="https://github.com/user-attachments/assets/6293f895-be2e-4130-a773-aa8f640d47cd" />

<img width="1027" height="455" alt="image" src="https://github.com/user-attachments/assets/4f8a2bce-4ae5-4ef5-9f70-e85aadc3326e" />





