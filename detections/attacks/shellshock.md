# Kết quả phát hiện Shellshock

## 1. Tổng quan Chuỗi Sự kiện

Kịch bản tấn công Shellshock bằng curl đã tạo ra các HTTP Request giả mạo trường User-Agent và Referer chứa mã độc RCE nhắm vào Web Server. Thông qua bộ giải mã web-accesslog, Wazuh Agent thu thập nhật ký từ /var/log/nginx/access.log và bóc tách luồng dữ liệu HTTP Header để đưa ra cảnh báo.

Dựa trên dữ liệu log thực tế, bộ luật (Ruleset) mặc định của Wazuh thể hiện sự thông minh vượt trội khi không chỉ nhận diện được chuỗi ký tự Shellshock () { :; };, mà còn phân loại mức độ nghiêm trọng dựa trên mã phản hồi của Web Server.

## 2. Phân tích chi tiết luồng cảnh báo

Sự kiện 1: Khai thác Shellshock thành công về mặt kết nối

Rule ID:31168

Rule Level:15

Description :Shellshock attack detected

MITRE: T1068, T1190

Payload:Khai thác qua User-Agent (cat /etc/passwd) và Referer (/bin/ping...).

Kết quả trả về: HTTP 200 OK.

Mặc dù máy chủ Ubuntu thực tế không dính lỗi, nhưng việc Web Server trả về mã 200 OK cho một gói tin chứa payload Shellshock được SIEM đánh giá là một mối nguy hiểm. Wazuh ngay lập tức kích hoạt mức Level 15 tối đa và tự động đánh cờ gửi email báo động.

<img width="1432" height="812" alt="image" src="https://github.com/user-attachments/assets/1fcb9704-f178-48c8-a5ed-cfa88872ce18" />

<img width="1097" height="615" alt="image" src="https://github.com/user-attachments/assets/f2ade047-7440-4d6d-ba69-9c13a8976437" />

<img width="1457" height="845" alt="image" src="https://github.com/user-attachments/assets/4453a335-b354-412f-b64d-3dc153ea3a90" />

<img width="1172" height="621" alt="image" src="https://github.com/user-attachments/assets/370e1342-e373-4529-bcf8-7c100e2938ff" />



Sự kiện 2: Nỗ lực rà quét Shellshock thất bại (Attempt)

Rule ID:31166

Rule Level:6 (Medium)

Description :Shellshock attack attempt

MITRE: T1068, T1190

Payload:Vòng lặp bắn tự động () { :; }; echo VULNERABLE vào các thư mục ảo như /cgi-bin/test.cgi.

Kết quả trả về:HTTP 404.

Kết quả trả về 404 chứng tỏ payload chưa chạm được vào tệp tin thực thi nào. Hệ thống hạ cảnh báo xuống Level 6.

<img width="1405" height="828" alt="image" src="https://github.com/user-attachments/assets/7eebbffb-93e2-4252-a56f-de12aed3cc95" />

<img width="1072" height="521" alt="image" src="https://github.com/user-attachments/assets/78bbcde6-e024-4c7d-9e4a-1a725d7f9242" />

Sự kiện 3: Cảnh báo Tương quan sự kiện (Custom Rule Correlation)

Khi áp dụng Custom Rule 100510 mà mình đã xây dựng để gom nhóm các đòn tấn công web từ cùng một nguồn IP trong khoảng thời gian ngắn, hệ thống đã ghi nhận log thực tế như sau:

Rule ID:100510

Rule Level:12

Description: Custom Rule: Multiple Web Attacks detected from same IP.

data.url: /cgi-bin/admin.sh

full_log: 192.168.50.40 - - [16/Sep/2026:07:51:33 +0000] "GET /cgi-bin/admin.sh HTTP/1.1" 404 162 "-" "() { :; }; echo VULNERABLE"

previous_output:Lưu giữ toàn bộ dấu vết lịch sử của các request rà quét trước đó vào các file index.sh và login.cgi.

<img width="1430" height="768" alt="image" src="https://github.com/user-attachments/assets/01e52db9-ee18-4ec7-848d-9ede68e5d9a6" />

<img width="1425" height="465" alt="image" src="https://github.com/user-attachments/assets/fcc1d865-679a-4d95-9502-3177903d5dcd" />




