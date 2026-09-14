# Phát hiện tấn công Brute-Force
## 1. Tổng quan Chuỗi Sự kiện

Kịch bản tấn công SSH Brute-force bằng công cụ Hydra đã tạo ra một lượng lớn truy vấn xác thực sai trong thời gian ngắn. Thông qua bộ giải mã Decoder sshd được tích hợp sẵn, Wazuh Agent liên tục đọc tệp /var/log/auth.log và đẩy dữ liệu về Server. 

Wazuh Server không chỉ ghi nhận từng lần đăng nhập sai lẻ tẻ, mà còn sử dụng cơ chế tương quan sự kiện (Log Correlation) để gom nhóm các hành vi này lại và phát ra một cảnh báo mức độ cao về hành vi Brute-force tổng thể.

## 2. Chi tiết Luồng Cảnh báo

Mình ghi nhận thấy chuỗi 3 loại sự kiện sau diễn ra theo trình tự thời gian trên Dashboard :

Sự kiện 1: Các nỗ lực đăng nhập thất bại (Failed Logins)

Rule ID:5503

Rule Level:5

Description:	PAM: User login failed

MITRE ATT&CK: T1110.001 (Password Guessing)

Bối cảnh: Khi Hydra bắt đầu thử các mật khẩu sai, module PAM (Pluggable Authentication Modules) của Ubuntu từ chối truy cập và ghi nhận log pam_unix(sshd:auth): authentication failure. Cảnh báo này cung cấp thông tin chi tiết về IP kẻ tấn công (192.168.50.40) và tài khoản mục tiêu (khangg).

<img width="1498" height="778" alt="image" src="https://github.com/user-attachments/assets/319d8ed9-ab02-4b25-a0bb-60fb3e60747d" />

<img width="1005" height="842" alt="image" src="https://github.com/user-attachments/assets/058ebc0e-e395-42f2-9161-dbc4b46d2829" />


Sự kiện 2: Cảnh báo Tấn công Brute-Force 

Rule ID:5763

Rule Level:10 (High)

Description:sshd: brute force trying to get access to the system. Authentication failed.

MITRE ATT&CK:T1110 (Brute Force)

Điều kiện kích hoạt (Trigger): Wazuh phân tích luồng log và phát hiện sự lặp lại bất thường (tần suất rule.frequency: 8). Đáng chú ý là trường previous_output trong log đã ghi nhận rõ 8 luồng kết nối liên tiếp cùng kết nối cổng 22 từ các Source Port khác nhau (52966, 52976, 52978, 52980) - minh chứng cho tác dụng tham số -t 4  của Hydra.

Bối cảnh: Khi máy agent đang bị rà quét mật khẩu bằng công cụ tự động với cường độ cao, không phải do người dùng gõ sai thông thường.

<img width="1291" height="752" alt="image" src="https://github.com/user-attachments/assets/e71f9a13-40b5-4c68-83a0-fab6338d52cf" />

<img width="1407" height="852" alt="image" src="https://github.com/user-attachments/assets/7533325e-1a88-4e3f-933c-022cf6c68549" />


Sự kiện 3: Xâm nhập thành công và Khởi tạo phiên kết nối (Compromised Endpoint)

Rule ID:40112 (Level 12 - Critical) và 5501 (Level 3 - Session opened)

Mô tả:Multiple authentication failures followed by a success và PAM: Login session opened.

MITRE ATT&CK: T1078 (Valid Accounts), T1110 (Brute Force)

Bối cảnh:Thay vì chỉ ghi nhận việc mở phiên đăng nhập bình thường , Wazuh đã liên kết thành công chuỗi truy cập thất bại ở sự kiện 2 với lần đăng nhập hợp lệ này để thăng cấp lên cảnh báo nghiêm trọng (Rule 40112 - Level 12). Sự xuất hiện song song của hai log này khẳng định rằng cuộc tấn công Brute-force đã thành công, mật khẩu đã bị lộ và kẻ thù hiện đã khởi tạo được một phiên shell chiếm quyền kiểm soát hệ thống.

<img width="1457" height="746" alt="image" src="https://github.com/user-attachments/assets/8a6d4733-648e-4349-bcf5-c920c59d98af" />

<img width="1228" height="755" alt="image" src="https://github.com/user-attachments/assets/df5c0a33-f43f-4a49-ba7e-388756bf94c4" />


<img width="1426" height="826" alt="image" src="https://github.com/user-attachments/assets/49905de5-bb6b-4d28-af48-3952eb104c2f" />

<img width="1003" height="667" alt="image" src="https://github.com/user-attachments/assets/5e3fde80-73a0-4af1-b911-73098523e784" />







