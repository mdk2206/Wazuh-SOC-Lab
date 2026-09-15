# Khắc phục điểm mù SIEM

## 1. Đặt vấn đề
Trong quá trình phân tích cảnh báo tại phần detections (cụ thể là ở phần 3.1), mình đã phát hiện một điểm mù cực kỳ nguy hiểm của Wazuh với cấu hình mặc định khi kẻ tấn công gửi Payload Authentication Bypass, Nginx ghi nhận tấn công thành công (HTTP 200 OK), nhưng Wazuh không sinh ra bất kỳ cảnh báo nào.

## 2. Giải pháp: Xây dựng Custom Rule với PCRE2

Giải pháp tối ưu là viết một luật tùy chỉnh (Custom Rule) mình sẽ sử dụng engine PCRE2 của Wazuh để bắt theo hành vi của Tautology.

<img width="1057" height="212" alt="image" src="https://github.com/user-attachments/assets/ff29a3b0-6fac-4b22-9ef4-a39e5a1d4032" />

Phân tích chi tiết rule:

(?i): Chấp nhận mọi kiểu viết hoa/thường (OR, or, Or).

(?:%20|\+| ): Chấp nhận mọi hình thức của khoảng trắng (Mã hóa %20, dấu +, hoặc dấu cách chuẩn).

['"]?[a-zA-Z0-9_]+['"]?: Chấp nhận cả số và chữ, có bọc hoặc không bọc trong dấu nháy đơn/nháy kép (ví dụ: 1, '1', "admin").

=: Bắt buộc phải có phép toán gán/so sánh làm lõi của biểu thức.

### 3. Kết quả

Sau khi áp dụng Custom Rule, hệ thống đã lập tức phát hiện thành công biến thể Payload mà trước đó bị bỏ sót.

<img width="1447" height="792" alt="image" src="https://github.com/user-attachments/assets/28b245d4-2c4a-415c-b684-b87954de1153" />

<img width="1036" height="456" alt="image" src="https://github.com/user-attachments/assets/b45c2aab-55c1-4e78-93a8-9f31ba873aa0" />

Ta thấy hệ thống đã nhận diện được hành vi từ IP 192.168.50.40 và lập tức đẩy cảnh báo lên Level 10. Trường data.id trả về 200. Điều này cho biết payload không bị văng lỗi mà đã lọt qua và được Nginx xử lý thành công.







