# Mô phỏng Tấn công Shellshock

# 1. Tổng quan Kịch bản
Shellshock là một lỗ hổng thực thi mã từ xa (RCE) kinh điển và cực kỳ nghiêm trọng liên quan đến trình thông dịch lệnh Bash trên Linux. Theo đó, kẻ tấn công không nhắm vào URL như SQL Injection, mà lợi dụng các trường HTTP Headers (ví dụ: User-Agent, Referer, Cookie) để chèn mã độc. Khi Web Server đẩy các header này vào biến môi trường để xử lý, Bash sẽ vô tình thực thi luôn đoạn mã độc đó. Dấu hiệu nhận biết đặc trưng nhất của Shellshock là chuỗi ký tự hàm trống: () { :; };.

Trong kịch bản này, mình sẽ dùng curl để chỉnh sửa HTTP Header, đưa payload Shellshock vào Web Server. Wazuh Agent sẽ đọc Access Log của Nginx, bóc tách trường User-Agent/Referer và phát ra cảnh báo khi thấy chuỗi ký tự này.

## 2. Thông tin Môi trường
Attacker:Kali Linux (192.168.50.40).

Target: Máy Ubuntu Agent (192.168.50.30) chạy dịch vụ Nginx .

Công cụ sử dụng:curl.

## 3. Các bước thực hiện

Bước 1: Đảm bảo Web Server đã chạy ổn

Bước 2: Tấn công thủ công bằng curl

Kịch bản 1: Đọc file nhạy cảm qua trường User-Agent

Mục đích: Kẻ tấn công giả mạo User-Agent bằng chuỗi mã độc () { :; };. Nếu máy chủ mắc lỗi, nó sẽ bị ép chạy lệnh /bin/bash -c cat /etc/passwd và trả về toàn bộ danh sách người dùng của hệ điều hành.

<img width="1347" height="737" alt="image" src="https://github.com/user-attachments/assets/511f6891-2e5f-4b29-85d7-173ea16e6b12" />

Kịch bản 2: Kiểm tra kết nối ngược (Ping back) qua trường Referer

Mục đích: Kẻ tấn công tiêm mã độc vào trường Referer. Lệnh này ép máy chủ Ubuntu phải tự động ping ngược về địa chỉ IP của kẻ tấn công. Đây là kỹ thuật dò đường  để xác nhận xem mục tiêu có thực sự dính lỗi hay không trước khi cài cắm Backdoor.

<img width="1206" height="748" alt="image" src="https://github.com/user-attachments/assets/512fd506-791f-42ea-84bd-c3d1a712176b" />

Chúng ta có thể thấy, sau khi chạy các lệnh curl trên, thay vì đọc được file /etc/passwd, Terminal của máy Kali sẽ trả về một đoạn mã HTML dài hiển thị trang web mặc định. Do máy chủ Ubuntu là một Web Server đời mới, chỉ phục vụ file tĩnh (index.html) chứ không chạy các mã kịch bản CGI cũ, đồng thời nhân Bash cũng đã được vá lỗi Shellshock từ lâu nên output cho ra là hoàn toàn có thể hiểu được.

Bước 3: Tấn công rà quét tự động

Ở bước này, để gia tăng quy mô của kịch bản mình giả lập một công cụ rà quét Shellshock tự động tìm các file .cgi trên server.

Kịch bản này sử dụng vòng lặp để tự động bắn payload Shellshock vào các đường dẫn thực thi phổ biến (.cgi, .sh, .pl) thường có trên Web Server. Mục tiêu là tạo ra một đợt bão log nhằm kiểm tra khả năng gom nhóm cảnh báo của hệ thống Wazuh.

<img width="1085" height="215" alt="image" src="https://github.com/user-attachments/assets/efdc3267-601e-4b62-ad9e-a01c8d0832c9" />


