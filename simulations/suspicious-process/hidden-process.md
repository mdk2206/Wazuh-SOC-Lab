# Mô phỏng tấn công: Tiến trình ẩn (hidden process)

## Tổng quan kịch bản

Để thực hiện mô phỏng này, mình tiến hành tải, biên dịch và nạp mã nguồn rootkit Caraxes trên máy Ubuntu Agent.

Caraxes là một Linux Kernel Module Rootkit hoạt động nhân hệ điều hành. Khác với các Rootkit truyền thống sử dụng tín hiệu kill để điều khiển, Caraxes tự động che giấu tiến trình dựa trên User ID (UID) và Group ID (GID) được cấu hình sẵn trong mã nguồn.

Cụ thể, mọi tiến trình thuộc về người dùng có UID 1001 hoặc GID 21 sẽ tự động bị tàng hình. Khi Rootkit được nạp, nó sẽ can thiệp vào các System Call của hệ điều hành, khiến các công cụ giám sát mức người dùng như lệnh ps, top hay cấu trúc thư mục /proc bị mù hoàn toàn trước các tiến trình mục tiêu.

## Môi trường thực hiện 

Target: Ubuntu Agent (192.168.50.30)

Công cụ: Rootkit Caraxes

## Các bước thực hiện 

Bước 1: Cài đặt các thư viện lõi để biên dịch Kernel Module

Cập nhật hệ thống và cài đặt trình biên dịch build-essential, git cùng với Linux headers tương ứng với phiên bản nhân hiện tại của Ubuntu.

<img width="878" height="632" alt="image" src="https://github.com/user-attachments/assets/0ae62d51-103a-4787-b81a-1dd8f7729e81" />

Bước 2: Tải và biên dịch mã nguồn Caraxes

<img width="710" height="206" alt="image" src="https://github.com/user-attachments/assets/6f4962b9-eb84-4092-b277-499f3d9ae924" />

Sau khi lệnh make hoàn tất, hệ thống build thành công file module hạt nhân caraxes.ko

Bước 3: Tạo tài khoản và chạy tiến trình mục tiêu trước khi nạp Rootkit

Để kích hoạt tính năng tàng hình, ta tiến hành tạo một User thỏa mãn điều kiện của Caraxes (UID 1001, GID 21) và cho chạy một tiến trình ngầm sleep 10000.

<img width="816" height="143" alt="image" src="https://github.com/user-attachments/assets/495a3bde-fb48-483e-93f7-00353c33dd7f" />

Ta có thể thấy, lệnh ps hiển thị rõ ràng tiến trình sleep 10000 đang chạy dưới quyền của hiddenuser do nhân Linux lúc này chưa bị can thiệp.

Bước 4: Nạp Rootkit và kiểm chứng sự tàng hình

Tiến hành nạp module Caraxes vào nhân hệ điều hành. Ngay lập tức, mã độc thao túng toàn bộ dữ liệu trả về đối với các tiến trình thuộc UID 1001/GID 21.

<img width="807" height="75" alt="image" src="https://github.com/user-attachments/assets/cc7d9620-af3c-4c9a-85f6-aee205ae494e" />

Ta có thể thấy, tiến trình sleep 10000 đã hoàn toàn "bốc hơi" khỏi kết quả kiểm tra.




