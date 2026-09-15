# Kết quả phát hiện SQL Injection (SQLi)

## 1. Tổng quan Chuỗi Sự kiện

Kịch bản tấn công SQL Injection thông qua các phương pháp thủ công curl và sqlmap đã tạo ra hàng loạt truy vấn chứa các ký tự đặc biệt nhắm vào Web Server. Thông qua bộ giải mã web-accesslog, Wazuh Agent liên tục đọc nhật ký truy cập (/var/log/nginx/access.log) và đẩy dữ liệu thô về Wazuh Server.

## 2. Phân tích chi tiết luồng Cảnh báo

Trên Wazuh Dashboard, quá trình phát hiện SQLi được thể hiện qua các nhóm sự kiện bám sát theo hành vi tấn công:


Sự kiện 1: Tấn công Web thành công (A web attack returned code 200)

Rule ID:31106

Rule Level:6 (Medium)

Description:A web attack returned code 200 (success).

MITRE ATT&CK:T1190 (Exploit Public-Facing Application - Initial Access)

Payload:/products.php?id=1%20UNION%20SELECT%20username,password%20FROM%20users--

Kết quả trả về:HTTP 200 OK.

Bối cảnh:Khi payload UNION-based vào đúng đường dẫn tồn tại (/products.php), Nginx trả về mã 200 OK cùng dữ liệu trích xuất 171 bytes. Wazuh đánh giá mức độ thông qua trạng thái 200 OK, khẳng định Payload đã lọt qua và Server đã phản hồi dữ liệu hợp lệ dẫn đến nguy cơ Data Exfiltration.

<img width="1448" height="817" alt="image" src="https://github.com/user-attachments/assets/7e87dd8b-f814-44b4-92ec-abd5298d7656" />

<img width="1016" height="635" alt="image" src="https://github.com/user-attachments/assets/ac429815-59ee-4b5a-bc12-7b2686609088" />


Sự kiện 2: Nỗ lực tiêm mã SQL (SQL Injection attempt)

Rule ID:31103

Rule Level: 7 (Medium)

Description :SQL injection attempt.

MITRE ATT&CK:T1190 (Exploit Public-Facing Application - Initial Access)

Payload:/view?id=1%20AND%20(SELECT%207777%20FROM%20(SELECT(SLEEP(5)))KhAng).

Kết quả trả về:HTTP 404 (Không tìm thấy trang).

Bối cảnh:Khi kẻ tấn công thử tiêm payload Time-Based Blind (SLEEP), Server trả về lỗi 404 do sai đường dẫn. Dù tấn công thất bại, Wazuh vẫn bắt được từ khóa độc hại trong trường data.url và cảnh báo đây là nỗ lực tiêm mã.

<img width="1432" height="760" alt="image" src="https://github.com/user-attachments/assets/f3a0ad42-254b-4be7-a81d-8e21b80b4693" />

<img width="1018" height="493" alt="image" src="https://github.com/user-attachments/assets/2ae1324c-a693-4b31-9d30-3d696ac7de26" />

## 3. Giới hạn của SIEM

Trong quá trình thử nghiệm thực tế, mình cũng đã phát hiện ra những điểm mù của hệ thống SIEM thuần dựa trên chữ ký (Signature-based):

3.1. Bỏ lọt Authentication Bypass (OR 1=1)
Hiện tượng: Khi thực thi curl với payload login.php?user=admin'%20OR%20'1'='1, Nginx ghi nhận log đầy đủ với mã HTTP 200 OK, nhưng Wazuh hoàn toàn không có alert.
Nguyên nhân: Tập luật mặc định của Wazuh được tối ưu hóa để tránh báo động giả, chủ yếu quét các từ khóa truy vấn mạnh (SELECT, UNION, SLEEP). Payload chứa OR 1=1 không kích hoạt các từ khóa này nên bị Regex bỏ qua.

3.2. SQLMap quét ầm ầm nhưng cũng không tạo ra alert
Hiện tượng: Mặc dù sqlmap đã gửi 76 request và gây ra 57 lỗi HTTP 500, Dashboard không xuất hiện lượng cảnh báo dồn dập như kỳ vọng.
Nguyên nhân: 
    1. Có thể SQLMap sử dụng các payload Boolean-based blind đơn giản không chứa từ khóa nhạy cảm nặng.
    2. Tốc độ gửi request quá nhanh có thể kích hoạt cơ chế chống nghẽn (Anti-flooding) của Wazuh Agent.

Và để khắc phục những điểm mù này, mình đã viết thêm những rules nhằm để nâng cấp wazuh để đưa ra các cảnh báo sát hơn với thực tế ở phần custom-rules






