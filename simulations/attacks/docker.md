# Mô phỏng Tấn công: Khai thác Docker
## 1. Tổng quan kịch bản

Ở phần này mình sẽ xây dựng kịch bản giả định kẻ tấn công đã lọt được vào bên trong máy chủ Ubuntu Web thông qua một lỗ hổng Web (ví dụ: upload webshell) và phát hiện hệ thống có cài đặt Docker. Sau đó kẻ tấn công sẽ lợi dụng cơ chế của Docker để:

1. Mount toàn bộ hệ thống file gốc (`/`) của máy chủ Ubuntu vào bên trong một Container.
2. Từ trong Container, truy cập và đánh cắp file chứa mật khẩu /etc/shadow của máy chủ .

Mục tiêu của kịch bản là kiểm tra xem tính năng giám sát docker-listener của Wazuh Agent có phát hiện được hành vi khởi tạo container với các tham số nguy hiểm này hay không.

## 2. Môi trường thực hiện

Máy Ubuntu Agent (192.168.50.30) đã được cài đặt Docker.

Chúng ta sẽ chạy script này trực tiếp trên máy Ubuntu Web Server (đóng vai trò là kẻ tấn công đang đứng trên máy nạn nhân).

Bước 1: Tạo một file kịch bản tên là simulate_docker_escape.sh:

Đây là kịch bản tự động hóa một đòn tấn công leo thang đặc quyền thông qua Docker. Nó giả lập tình huống kẻ tấn công lạm dụng quyền Docker để tạo một container độc hại, trích xuất file mật khẩu nhạy cảm của máy chủ thật (/etc/shadow), và tự động xóa sổ mọi dấu vết ngay sau đó để tàng hình.

<img width="1342" height="406" alt="image" src="https://github.com/user-attachments/assets/11be9c81-c17f-4d21-9858-18a5de661fb4" />


docker run -d --name ...: Khởi tạo và chạy container ở chế độ ngầm để ẩn khỏi màn hình terminal của quản trị viên.

-v /:/mnt/host_root: giúp mount toàn bộ hệ thống thư mục gốc (/) của máy chủ vật lý vào đường dẫn /mnt/host_root bên trong container.

bash -c "cat /mnt/host_root/etc/shadow; sleep 10": Lệnh này dùng để trộm file chứa mật khẩu (/etc/shadow) của toàn bộ hệ thống Host. Trạng thái sleep 10 giúp giữ container sống 10 giây để Wazuh Agent trên máy chủ kịp thời thu thập log và gửi về Manager.

docker stop ... và docker rm ...: Cơ chế xóa dấu vết. Ngay sau khi lấy được dữ liệu, kịch bản tự động dừng và xóa sạch container độc hại. 

Bước 2: Cấp quyền và thực thi kịch bản.

<img width="787" height="333" alt="image" src="https://github.com/user-attachments/assets/065808e9-a12f-411f-820a-2884f4aff894" />








