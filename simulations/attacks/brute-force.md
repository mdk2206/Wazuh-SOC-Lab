# Mô phỏng Tấn công SSH Brute-Force
## 1. Tổng quan Kịch bản

Tấn công Brute-force là một trong những vector tấn công phổ biến nhất nhằm vào các dịch vụ quản trị từ xa (như SSH, RDP). Trong kịch bản này, mình sẽ xây dựng kẻ tấn công sẽ sử dụng phương pháp Dictionary Attack để thử hàng loạt mật khẩu khác nhau trong thời gian ngắn nhằm chiếm quyền điều khiển hệ thống.
## 2. Môi trường thực hiện 

Attacker: Máy Kali Linux (192.168.50.40)

Target:Máy Ubuntu Agent (192.168.50.30)

Tài khoản mục tiêu: khangg.

Công cụ sử dụng:hydra.

## 3. Các bước thực thi
Bước 1: Khởi tạo Wordlist mật khẩu để tấn công

Nhằm để kịch bản diễn ra nhanh chóng và kiểm soát được lượng log sinh ra,mình tạo một tệp từ điển giả lập chứa các mật khẩu sai và cả mật khẩu đúng.

<img width="408" height="261" alt="image" src="https://github.com/user-attachments/assets/3374f328-1fe5-4b9a-adc2-2f7f3ec7a60a" />

Bước 2: Khởi chạy tấn công bằng Hydra

Mình sẽ sử dụng công cụ hydra trên Kali Linux để thực hiện hàng loạt truy vấn đăng nhập song song vào cổng 22 (SSH) của Ubuntu Agent:

Ở đây mình sử dụng thêm tham số -t 4: Mở 4 luồng xử lý song song để tăng cường tốc độ dò quét, gây ra lượng lớn log Failed password trong thời gian ngắn.

<img width="1666" height="186" alt="image" src="https://github.com/user-attachments/assets/d48969c9-fc55-4a1b-b5d0-2afdfbbae5c9" />

Chúng ta thấy rằng, sau vài giây dò quét, hydra sẽ in ra màn hình dòng chữ xanh báo hiệu thành công: 

22][ssh] host: 192.168.50.30   login: khangg   password: Khangg2202

1 of 1 target successfully completed, 1 valid password found

Lúc này, trên máy Ubuntu Agent, tệp nhật ký hệ thống /var/log/auth.log đã ghi nhận hàng loạt hành vi đăng nhập thất bại liên tiếp trước khi có một lần đăng nhập thành công. Đây chính là dấu hiệu để Wazuh đối chiếu với tập rules phát hiện chúng.






