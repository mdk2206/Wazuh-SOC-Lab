# Cấu hình phát hiện các cuộc tấn công Brute-Force
## Mục tiêu: Cấu hình Wazuh Agent trên Ubuntu Server và Windows để giám sát sự kiện xác thực, giúp Wazuh Server tự động phát hiện các nỗ lực tấn công Brute-Force (SSH trên Linux và RDP/Đăng nhập trên Windows).
## Phần 1: Cấu hình giám sát trên Ubuntu Agent 
Do Ubuntu Agent đã tự động theo dõi các tệp nhật ký hệ thống xác thực như /var/log/auth.log . Do đó, không cần cấu hình thêm, hệ thống sẽ tự động sử dụng các Rule tích hợp sẵn như Rule 5710, 5503, và Rule cảnh báo ngưỡng 5551 để phát hiện đăng nhập SSH thất bại liên tục.
## Phần 2: Cấu hình giám sát trên Windows Agent
Ta sẽ kiểm tra cấu hình EventChannel trên Windows và đảm bảo rằng khối thu thập nhật ký cho Security đã được bật để hệ thống bắt các mã sự kiện đăng nhập thất bại (chẳng hạn như Event ID 4625)

<img width="387" height="67" alt="image" src="https://github.com/user-attachments/assets/da16bb06-9cd8-4b88-b976-86f546a5fcfb" />


