# Kết quả phát hiện: Tấn công bằng mã độc tống tiền để giám sát sự toàn vẹn của tệp tin.

## 1. Tổng quan Chuỗi Sự kiện
Kịch bản mô phỏng tấn công Ransomware thông qua kịch bản Python đã tạo ra một chuỗi hành vi phá hoại tĩnh lặng nhắm vào dữ liệu hợp lệ trên máy chủ. Thông qua tính năng Syscheck kết hợp với cơ chế Whodata - auditd, Wazuh Agent liên tục giám sát thư mục /document và đẩy chi tiết các sự kiện thay đổi (mã băm, quyền, tiến trình) về Wazuh Server.

## 2. Phân tích chi tiết luồng cảnh báo

Trên Wazuh Dashboard, quá trình phát hiện mã độc tống tiền được thể hiện qua các nhóm sự kiện bám sát theo vòng đời của tệp tin:


Sự kiện 1: Khởi tạo dữ liệu mồi

Rule ID: 100400

Rule Level: 7

Description: File added to /document directory. Preparing for VirusTotal/Yara scan.


Bối cảnh: Khi quản trị viên tạo file dữ liệu customer_database.csv, Wazuh phát hiện có tệp tin mới trong thư mục giám sát. 

<img width="1227" height="817" alt="image" src="https://github.com/user-attachments/assets/0f15b755-8855-4d58-904d-ee1cf599928b" />

<img width="1140" height="826" alt="image" src="https://github.com/user-attachments/assets/23df208c-4160-493b-aa0a-082806e0951b" />

<img width="1143" height="405" alt="image" src="https://github.com/user-attachments/assets/3ac2c851-8959-40f7-a643-08d7778e7aeb" />

Sự kiện 2: Mã hóa nội dung tệp tin

Rule ID: 100401

Rule Level: 7

Description: File modified in /document directory. Preparing for VirusTotal/Yara scan.

Tiến trình thực thi: /usr/bin/python3.12

Kết quả trả về: Kích thước tệp tin (Size) đổi từ 19 sang 47 bytes. Mã băm SHA256 thay đổi hoàn toàn.

Bối cảnh: Kịch bản mã độc chạy và tiến hành ghi đè nội dung. Wazuh ghi nhận chi tiết sự thay đổi nội dung chuyển từ < Du lieu quan trong sang > YOUR_FILES_ARE_ENCRYPTED_SEND_0.5_BTC_TO_UNLOCK. Hệ thống kích hoạt rule 100401 để chuẩn bị mồi kích hoạt các module kiểm tra mã độc tự động.

<img width="1432" height="787" alt="image" src="https://github.com/user-attachments/assets/114b4686-2169-4fa8-bf61-f38b9aa79625" />

<img width="1130" height="797" alt="image" src="https://github.com/user-attachments/assets/cb0e22a4-57a0-450c-85b6-559d7d8defe2" />

<img width="1305" height="827" alt="image" src="https://github.com/user-attachments/assets/9b085a20-76d2-402a-bd16-d00e8de68530" />

<img width="1237" height="403" alt="image" src="https://github.com/user-attachments/assets/c0cf79a3-94af-4449-8ca4-49a84c98a264" />


Sự kiện 3: Tích hợp tình báo mối đe dọa

Rule ID: 87103

Rule Level: 3

Description: VirusTotal: Alert - No records in VirusTotal database

Module: Integration (VirusTotal API)

Bối cảnh: Ngay khi Rule 100401 nổ ra, Wazuh Manager lập tức trích xuất mã băm mới và tự động gửi API call lên hệ thống tình báo của VirusTotal. Do tệp tin tống tiền giả lập này là tự tạo và chưa từng tồn tại trên không gian mạng, VirusTotal trả về kết quả found: 0 (No records).

<img width="1127" height="833" alt="image" src="https://github.com/user-attachments/assets/fb168ae9-c63e-4af1-8879-0c8e27e128c2" />

<img width="1091" height="356" alt="image" src="https://github.com/user-attachments/assets/59f671fe-9471-4ef5-a9cc-6632754b35ef" />



Sự kiện 4: Khóa quyền truy cập tệp tin 

Rule ID: 100401

Rule Level: 7 

Description: File modified in /document directory. Preparing for VirusTotal/Yara scan.

Tiến trình thực thi: /usr/bin/python3.12


Bối cảnh: Ngay sau khi mã hóa dữ liệu, mã độc tiếp tục tước đoạt toàn bộ quyền truy cập tệp tin để ngăn cản quản trị viên can thiệp. Cảnh báo tiếp tục nổ khi hệ thống nhận thấy thuộc tính phân quyền bị thay đổi đột ngột (syscheck.perm_before: rw-r--r-- chuyển thành syscheck.perm_after: ---------). 

<img width="1287" height="803" alt="image" src="https://github.com/user-attachments/assets/6ff6822f-d2f4-414a-800f-3947926a82d0" />

<img width="1222" height="742" alt="image" src="https://github.com/user-attachments/assets/a78d1106-c078-4c3f-a7ca-a0984aa28b1c" />

<img width="1281" height="591" alt="image" src="https://github.com/user-attachments/assets/79f53017-89d1-4347-8132-36d2bd99a999" />

Sự kiện 5: Tệp tin bị tiêu hủy

Rule ID: 553

Rule Level: 7

Description: File deleted.

MITRE ATT&CK: T1070.004 (File Deletion), T1485 (Data Destruction)

Tiến trình thực thi: /usr/bin/python3.12

Bối cảnh: Sự kiện này diễn ra ở bước cuối cùng của kịch bản khi mã độc xóa sổ vĩnh viễn dữ liệu gốc để phi tang dấu vết. Hệ thống giữ lại được bằng chứng số quan trọng nhất: Tiến trình Python đã ra lệnh xóa tệp tin, cung cấp đầy đủ thông tin về thời gian thực và hành vi của mã độc.

<img width="1095" height="722" alt="image" src="https://github.com/user-attachments/assets/c540ea63-9e6b-45b6-8cdd-30abdfcd0aa1" />

<img width="1210" height="820" alt="image" src="https://github.com/user-attachments/assets/017d98d7-3084-4677-bba2-5d37caf1d661" />

<img width="1248" height="820" alt="image" src="https://github.com/user-attachments/assets/79d41580-9d9b-4402-aed0-c2e56c28d46c" />


















