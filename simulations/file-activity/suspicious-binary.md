# Mô phỏng Tấn công: Phát hiện tệp nhị phân đáng ngờ


## 1. Tổng quan kịch bản

Trong giai đoạn hậu khai thác, sau khi xâm nhập thành công vào máy chủ Web với quyền user hạn chế, mục tiêu tiếp theo của kẻ tấn công luôn là leo thang đặc quyền để chiếm quyền quản trị cao nhất. 

Để làm được điều này, hacker thường tải lên hệ thống các tệp nhị phân đáng ngờ hoặc các tập lệnh tự động để rà quét lỗ hổng. LinPEAS là một trong những công cụ phổ biến nhất. 

Trong kịch bản này, mình sẽ đóng vai kẻ tấn công, âm thầm tải LinPEAS vào máy chủ và cấp quyền thực thi cho nó. Wazuh thông qua FIM sẽ làm nhiệm vụ phát hiện sự xuất hiện của tệp tin lạ này cũng như hành vi thay đổi quyền trái phép để phát ra cảnh báo.

## 2. Môi trường thực hiện

Target: Máy Ubuntu Agent (192.168.50.30).

Công cụ sử dụng: wget, chmod và LinPEAS.

Thư mục giám sát:/document/suspicious-binary.

## 3. Các bước thực hiện

Bước 1: Thả tệp tin đáng ngờ vào hệ thống

Hacker thường tạo các thư mục ẩn hoặc lợi dụng thư mục tải lên của Web Server để chứa công cụ. Ở đây, mình tiến hành tạo thư mục đích và dùng wget để tải trực tiếp file linpeas.sh từ internet về máy.

<img width="1671" height="478" alt="image" src="https://github.com/user-attachments/assets/743a399d-7695-4ff2-8556-4fdea7b07330" />

Khi tệp tin linpeas.sh rơi xuống ổ cứng, tính năng FIM của Wazuh sẽ ngay lập tức bắt được sự kiện File Added.

Bước 2: Cấp quyền thực thi cho tệp tin

Mặc định, các tệp tin tải về từ internet bằng wget sẽ chỉ có quyền Đọc/Ghi (rw-r--r--) để đảm bảo an toàn. Kẻ tấn công bắt buộc phải dùng lệnh chmod để biến file này thành một tệp nhị phân có khả năng tự khởi chạy.

<img width="623" height="66" alt="image" src="https://github.com/user-attachments/assets/a5434942-dcec-4f39-844c-82aa501fa04d" />

Bước 3: Thực thi công cụ rà quét

Hacker tiến hành chạy LinPEAS để bắt đầu quá trình thu thập thông tin và dò tìm điểm yếu của hạt nhân Linux.

Ta thấy ngay khi khởi chạy, LinPEAS sẽ in ra màn hình hàng loạt các thông tin cấu hình hệ thống với giao diện đặc trưng. 

<img width="1002" height="785" alt="image" src="https://github.com/user-attachments/assets/5e1d5541-fb6c-4299-9f38-c1ca0cd8f77e" />

<img width="1401" height="830" alt="image" src="https://github.com/user-attachments/assets/683ac909-0930-48a2-ba07-80cce7f9f647" />


