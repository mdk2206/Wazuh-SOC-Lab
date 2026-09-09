# Cấu hình Phát hiện Tấn công Shellshock
## Mục tiêu: Cấu hình Wazuh Agent (Ubuntu) giám sát Nginx access log, cho phép Wazuh Server tự động phát hiện các nỗ lực khai thác lỗ hổng Shellshock dựa trên bộ quy tắc mặc định.
## Lưu ý triển khai: 
Tệp nhật ký truy cập của Nginx (/var/log/nginx/access.log) và dịch vụ Wazuh Agent trên Ubuntu đã được thiết lập thu thập từ các bài cấu hình trước. Do đó, không cần cấu hình lại. Wazuh sẽ sử dụng luồng log sẵn có kết hợp với bộ quy tắc phát hiện tấn công web (web_rules.xml chứa Rule ID 31168) để tự động phân tích tiêu đề HTTP (User-Agent) và bắt các chuỗi payload Shellshock (() { :; }; ...).
