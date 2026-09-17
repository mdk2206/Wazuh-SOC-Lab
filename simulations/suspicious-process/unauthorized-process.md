# Mô phỏng Tấn công: Tiến trình trái phép

## 1. Tổng quan kịch bản
Kịch bản này giả lập hành vi tấn công Living off the Land bằng cách lạm dụng công cụ mạng hợp lệ để lén lút mở cổng kết nối ngầm hoàn toàn trên bộ nhớ RAM. Mục đích chính là tạo ra một tiến trình vi phạm baseline của hệ thống. Thông qua cơ chế Command Monitoring định kỳ chụp ảnh danh sách tiến trình, Wazuh Agent sẽ đối chiếu với tập luật để bắt quả tang hành vi trái phép này và phát ra cảnh báo.

## 2. Môi trường thực hiện 

Target: Máy Ubuntu Agent (192.168.50.30).

Công cụ sử dụng: bash, ncat .

## 3. Các bước thực hiện

Bước 1: Kiểm tra công cụ trên máy target

Đảm bảo máy Ubuntu Agent đã được cài đặt sẵn bộ công cụ ncat

Bước 2: Tạo file kịch bản simulate_unauthorized_proc.sh

<img width="1005" height="582" alt="image" src="https://github.com/user-attachments/assets/278568b1-754d-49c2-882a-9faa26ff1732" />

Tổng quan về tác dụng của kịch bản này:

Mở Backdoor ngầm: Tự động gọi ncat kết hợp với Subshell ( ) và dấu & để mở port 8000 chạy ẩn hoàn toàn dưới nền.

Sử dụng lệnh timeout 30s và sleep 30 để ép tiến trình này phải sống trong 30 giây. 

Tự động dọn dẹp:Sau 30 giây, kịch bản tự động tiêu hủy tiến trình mạng, ngắt kết nối và không để lại bất kỳ file rác nào.

Bước 3: Cấp quyền và thực thi kịch bản

<img width="618" height="275" alt="image" src="https://github.com/user-attachments/assets/5ad06ae1-e10f-4ecb-98b3-8cc9a82f37eb" />

