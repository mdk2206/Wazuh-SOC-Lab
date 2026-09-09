# Cấu hình phát hiện các tệp nhị phân đáng ngờ
## Mục tiêu
* Cấu hình module **Rootcheck** trên Wazuh Agent để tự động rà quét và đối chiếu các tệp nhị phân hệ thống với cơ sở dữ liệu chữ ký (signatures) của các loại mã độc, rootkit và trojan.
* Tối ưu hóa tần suất quét (giảm xuống 120 giây) để phục vụ cho việc kiểm thử tính năng phát hiện tệp nhị phân bị giả mạo hoặc thay thế trái phép (sẽ thực hiện ở phần mô phỏng).
## Cấu hình trên Ubuntu Agent
Ta sẽ kích hoạt Rootcheck và rà soát Trojan

<img width="667" height="313" alt="image" src="https://github.com/user-attachments/assets/aa8e8520-de9e-4101-a7c4-b1a3a0846685" />


