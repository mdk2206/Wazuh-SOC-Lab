# Mô phỏng tấn công: Tính toàn vẹn tệp tin

## 1. Tổng quan kịch bản
Mã độc tống tiền (Ransomware) là một trong những mối đe dọa nguy hiểm nhất đối với máy chủ lưu trữ. Khác với các mã độc thông thường, Ransomware nhắm trực tiếp vào dữ liệu bằng cách mã hóa chúng, khóa quyền truy cập và xóa bản gốc để đòi tiền chuộc.

Trong kịch bản này, mình sẽ sử dụng một kịch bản Python để mô phỏng vòng đời phá hoại tĩnh lặng của Ransomware. Bằng việc cấu hình tính năng Syscheck kết hợp với cơ chế kiểm toán hạt nhân Whodata (auditd) trên thư mục /document, Wazuh Agent sẽ không chỉ phát hiện dữ liệu bị thay đổi, mà còn bóc tách và chỉ đích danh tiến trình nào đang thực hiện hành vi đó.

## 2. Môi trường thực hiện

Target:Máy Ubuntu Agent (192.168.50.30).

Thư mục giám sát:/document.

## 3. Các bước thực hiện

Bước 1: Khởi tạo dữ liệu mồi.

Trước khi mã độc tấn công, quản trị viên khởi tạo một tệp tin dữ liệu quan trọng chứa thông tin khách hàng.

Mục đích: Wazuh Syscheck lập tức ghi nhận sự xuất hiện của tệp tin này, tính toán mã hash gốc và lưu vào cơ sở dữ liệu để làm baseline.

<img width="1025" height="70" alt="image" src="https://github.com/user-attachments/assets/01318907-574e-4353-836a-ed0e57d0f4b8" />

Bước 2: Phát triển và thực thi kịch bản mô phỏng Ransomware

Để mô phỏng chân thực, mình tạo một file kịch bản ransom_simulator.py. Kịch bản này sẽ tự động: Ghi đè nội dung tống tiền -> Tước quyền -> Xóa tệp tin gốc.

<img width="1012" height="622" alt="image" src="https://github.com/user-attachments/assets/5d5c5917-09cf-4e6d-bee3-084e66db1774" />

Bước 3: Tiến hành chạy file với quyền root

<img width="505" height="57" alt="image" src="https://github.com/user-attachments/assets/adfa7ef3-2389-47d5-a4c2-89cd9f4f6aee" />








