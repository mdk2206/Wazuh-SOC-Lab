# Cấu hình Phát hiện Tấn công SQL Injection
## Mục tiêu: Cấu hình Wazuh Agent (Ubuntu) giám sát Nginx access log, cho phép Wazuh Server tự động phân tích và phát hiện payload tấn công SQL Injection dựa trên bộ quy tắc mặc định.
## Lưu ý triển khai
Do tệp nhật ký truy cập của Nginx (/var/log/nginx/access.log) và dịch vụ Wazuh Agent trên Ubuntu đã được cấu hình thu thập từ phần block-known-malicious-ip. Do đó, không cần cấu hình lại phần thu thập log ở bài này. Hệ thống sẽ sử dụng chung nguồn log sẵn có kết hợp với bộ quy tắc (web_rules.xml) mặc định của Wazuh để tự động bóc tách và phát hiện các dấu hiệu tấn công SQL Injection.
