# Mô phỏng Tấn công SQL Injection (SQLi)
## 1. Tổng quan Kịch bản
SQL Injection là một trong những lỗ hổng bảo mật Web nghiêm trọng nhất. Kẻ tấn công lợi dụng các lỗ hổng trong việc kiểm duyệt đầu vào để tiêm các đoạn mã SQL độc hại vào câu truy vấn của cơ sở dữ liệu.

Trong kịch bản này, mình sẽ mô phỏng khi kẻ tấn công gửi các HTTP GET Request chứa Payload SQLi (như OR 1=1, UNION SELECT) vào Web Server. Thông qua việc giám sát Access Log, Wazuh Agent sẽ bóc tách các URL này và phát ra cảnh báo.

## 2. Môi trường thực hiện 
Attacker: Máy Kali Linux (192.168.50.40)

Target: Máy Ubuntu Agent (192.168.50.30) chạy dịch vụ Nginx

Công cụ sử dụng:curl và sqlmap.

## 3. Các bước thực hiện

Bước 1: Kiểm tra dịch vụ Nginx trên máy target có chạy tốt không.

<img width="1082" height="312" alt="image" src="https://github.com/user-attachments/assets/952d336a-4a3c-4e8f-9903-2cb3a157c7b6" />


Bước 2: Tấn công thủ công bằng curl

2.1 Payload Authentication Bypass (Vượt qua xác thực): admin'%20OR%20'1'='1

Mục đích: Kẻ tấn công đóng nháy đơn  của biến user sớm, sau đó thêm điều kiện OR '1'='1'. Vì 1=1 luôn luôn đúng , toàn bộ câu truy vấn xác thực SQL sẽ trả về TRUE, giúp hacker đăng nhập thẳng vào tài khoản admin mà không cần biết mật khẩu.

<img width="796" height="220" alt="image" src="https://github.com/user-attachments/assets/64723cd4-400f-461e-a80a-7a56bfa48daf" />

Sau khi chạy lệnh, thì màn hình báo lênhj Đăng nhập thành công! Xin chào admin&#039; OR &#039;1&#039;=&#039;1 cho thấy hacker đã xâm nhập được vào tài khoản admin

2.2 Payload UNION-Based (Trích xuất dữ liệu): UNION%20SELECT%20username,password...

Ở payload này kẻ tấn công sử dụng toán tử UNION để gộp kết quả của câu truy vấn gốc và tìm sản phẩm có id=1 với một câu truy vấn hoàn toàn mới (lấy username và password từ bảng users). Ký tự `--` ở cuối dùng để vô hiệu hóa toàn bộ phần mã SQL thừa phía sau để tránh bị lỗi cú pháp.

<img width="1218" height="228" alt="image" src="https://github.com/user-attachments/assets/1ba37b6e-5a5f-4603-98a5-5e04ad0f34a0" />

Ta cũng có thể thấy sau khi chạy lệnh UNION-Based SQLi, Web Server trả về mã HTTP 200 OK kèm theo dữ liệu nhạy cảm bị rò rỉ

2.3 Payload 3 (Time-Based Blind): SELECT(SLEEP(5))

Ở payload này hacker đã ép cơ sở dữ liệu phản hồi chậm 5 giây để kiểm chứng sự có tồn tại của lỗ hổng SQLi ngầm hay không.

Lần này khi chạy lệnh Time-Based Blind vào đường dẫn /view, kết quả trả về không phải là dữ liệu mà là trang lỗi mặc định của Nginx: HTTP/1.1 404 Not Found. Lí do là vì máy chủ không tồn tại đường dẫn /view, Nginx đã từ chối yêu cầu ngay từ vòng ngoài và trả về mã 404.

<img width="1002" height="330" alt="image" src="https://github.com/user-attachments/assets/e3bd8539-4128-4137-b819-e9879cadb8a3" />

Bước 3: Tấn công tự động bằng SQLMap

Bên cạnh việc tấn công thủ công, chúng ta sử dụng sqlmap để mô phỏng một cuộc rà quét lỗ hổng tự động. Mục tiêu ở đây là tạo ra một lượng lớn request dị thường  nhằm kiểm thử khả năng phát hiện và gom nhóm sự kiện (Correlation) của Wazuh.

Tổng quan về lệnh sử dụng 

--batch: Tự động hóa hoàn toàn quá trình dò quét.

--random-agent: Liên tục thay đổi User-Agent để ngụy trang luồng truy cập, né tránh các cơ chế lọc cơ bản.

--level=2 --risk=2: Gia tăng số lượng và độ phức tạp của payload. Việc này sẽ ép Web Server liên tục gặp lỗi cú pháp SQL và trả về các mã lỗi (HTTP 500). Đây chính là "mồi lửa" hoàn hảo để kích hoạt các Rule cảnh báo mức độ cao trên SIEM.

<img width="1672" height="726" alt="image" src="https://github.com/user-attachments/assets/6d76ff2c-63d1-4c68-9fc1-567c765e1747" />

<img width="1680" height="776" alt="image" src="https://github.com/user-attachments/assets/8cb3e368-ed34-4317-b519-3935a1301292" />

Chỉ trong vài giây chạy lệnh, sqlmap đã quét thành công và cho ra toàn bộ kiến trúc của hệ thống đích:

Nhận diện mục tiêu: Phát hiện chính xác hệ điều hành (Ubuntu), Web Server (Nginx) và nền tảng CSDL (MySQL/MariaDB).

Xác nhận lỗ hổng: Khẳng định tham số ?id= dính 3 phương thức tấn công nghiêm trọng: Boolean-based blind, Time-based blind, và UNION query.














