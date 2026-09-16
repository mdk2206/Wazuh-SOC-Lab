# Khắc phục điểm mù SIEM

##  1. Điểm mù 3.1
Trong quá trình phân tích cảnh báo tại phần detections (cụ thể là ở phần 3.1), mình đã phát hiện một điểm mù cực kỳ nguy hiểm của Wazuh với cấu hình mặc định khi kẻ tấn công gửi Payload Authentication Bypass, Nginx ghi nhận tấn công thành công (HTTP 200 OK), nhưng Wazuh không sinh ra bất kỳ cảnh báo nào.

##  Giải pháp cho điểm mù 3.1: Xây dựng Custom Rule với PCRE2

Giải pháp tối ưu là viết một luật tùy chỉnh (Custom Rule) mình sẽ sử dụng engine PCRE2 của Wazuh để bắt theo hành vi của Tautology.

<img width="1057" height="212" alt="image" src="https://github.com/user-attachments/assets/ff29a3b0-6fac-4b22-9ef4-a39e5a1d4032" />

Phân tích chi tiết rule:

(?i): Chấp nhận mọi kiểu viết hoa/thường (OR, or, Or).

(?:%20|\+| ): Chấp nhận mọi hình thức của khoảng trắng (Mã hóa %20, dấu +, hoặc dấu cách chuẩn).

['"]?[a-zA-Z0-9_]+['"]?: Chấp nhận cả số và chữ, có bọc hoặc không bọc trong dấu nháy đơn/nháy kép (ví dụ: 1, '1', "admin").

=: Bắt buộc phải có phép toán gán/so sánh làm lõi của biểu thức.

## Kết quả

Sau khi áp dụng Custom Rule, hệ thống đã lập tức phát hiện thành công biến thể Payload mà trước đó bị bỏ sót.

<img width="1447" height="792" alt="image" src="https://github.com/user-attachments/assets/28b245d4-2c4a-415c-b684-b87954de1153" />

<img width="1036" height="456" alt="image" src="https://github.com/user-attachments/assets/b45c2aab-55c1-4e78-93a8-9f31ba873aa0" />

Ta thấy hệ thống đã nhận diện được hành vi từ IP 192.168.50.40 và lập tức đẩy cảnh báo lên Level 10. Trường data.id trả về 200. Điều này cho biết payload không bị văng lỗi mà đã lọt qua và được Nginx xử lý thành công.

## Điểm mù 3.2

Khi SQLMap rà quét, nó bắn hàng trăm payload mã 200 liên tục. Dù rủi ro cao, nhưng Rule 31106 mặc định chỉ xếp ở Level 6. Với Level này, hệ thống sẽ không tự động kích hoạt tính năng chặn IP, đồng thời tạo ra một cảnh báo level thực sự cao trên Dashboard.

##  Giải pháp cho điểm mù 3.2 

Để khắc phục điểm mù ấy, ở phần này em sẽ dùng kỹ thuật Correlation (Gom nhóm sự kiện) để tạo ra 1 rule hiểu rằng: Khiều cảnh báo nhỏ liên tiếp từ cùng một nguồn chính sẽ có nguy cơ là một cuộc tấn công lớn. Mình sẽ thiết kế một Rule tổng quát, rule này sẽ lắng nghe tất cả các dấu hiệu thuộc nhóm attack (bao gồm SQLi, XSS, Path Traversal, và cả Shellshock sau này).

<img width="748" height="185" alt="image" src="https://github.com/user-attachments/assets/fc83dd44-3f0a-4386-964f-7a77b5f84eec" />

Giải thích logic cấu hình:

<if_matched_group>attack</if_matched_group>: Bắt kỳ Alert nào do Wazuh phát hiện có chứa tag attack.

<same_source_ip>: Các truy cập độc hại phải xuất phát từ cùng một địa chỉ IP .

frequency="5" timeframe="60": Nếu nổ 5 lần trong vòng 60 giây, hệ thống sẽ gom tất cả lại và xuất ra duy nhất một Alert Level 12.

## Kết quả 

Sau khi cấu hình rule, Wazuh đã hiển thị một Alert Rule 100510 (Level 12) . Trường previous_output lưu giữ toàn bộ dấu vết của các hit đánh chặn trước đó.

<img width="1486" height="760" alt="image" src="https://github.com/user-attachments/assets/0258ed6b-6095-4e04-8fbf-6085b55e22d9" />

<img width="1485" height="792" alt="image" src="https://github.com/user-attachments/assets/ddd65e8c-13d2-4577-9511-55f0288177de" />



















