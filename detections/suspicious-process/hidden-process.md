# Kết quả phát hiện lây nhiễm Rootkit và kỹ thuật tàng hình

## 1. Tổng quan Chuỗi Sự kiện
Ở phần trước mình đã xây dựng kịch bản tấn công nhắm vào việc cấy mô-đun hạt nhân độc hại (Rootkit Caraxes) vào máy chủ Ubuntu Agent nhằm tàng hình các tiến trình ngầm. Thông qua các bộ giải mã auditd và kernel, Wazuh Agent liên tục thu thập hành vi thực thi lệnh đặc quyền và nhật ký thay đổi ở tầng nhân hệ điều hành (syslog/journald), sau đó bóc tách và đẩy dữ liệu về Wazuh Server.

## 2. Phân tích chi tiết luồng cảnh báo

Sự kiện diễn ra: Nạp mô-đun hạt nhân trái phép 

Rule ID: 5132

Rule Level: 11

Description: Unsigned kernel module was loaded.

MITRE ATT&CK: T1547.006.

Log gốc: kernel: caraxes: module verification failed: signature and/or required key missing - tainting kernel.

Bối cảnh: Ngay khi mã độc caraxes.ko được nạp vào Ring 0 thông qua lệnh insmod, bộ giải mã kernel của Wazuh phát hiện lõi hệ điều hành bị can thiệp bởi một mô-đun không có chữ ký số hợp lệ. Đây là sự kiện chốt hạ xác nhận Rootkit đã chính thức cắm rễ vào hệ thống.

## 3. Giới hạn của SIEM
Trong quá trình thử nghiệm thực tế với Rootkit Caraxes, mình đã nhận thấy điểm mù của module rà quét thụ động (Rootcheck) trước các kỹ thuật tàng hình thế hệ mới:

3.1. Bỏ lọt tiến trình bị ẩn (Hidden Process Bypass)

Hiện tượng: Sau khi Caraxes che giấu thành công tiến trình sleep , dù đã khởi động lại Wazuh Agent và ép chu kỳ quét rootcheck xuống mức cao nhất (120s), Wazuh Dashboard hoàn toàn không xuất hiện cảnh báo Rule 521 (Process hidden from ...).

Nguyên nhân: Việc Wazuh không bắt được tiến trình bị ẩn có lẽ là do Rootkit Caraxes quá tinh vi. Không giống như các Rootkit thế hệ cũ (mình đã thử kịch bản với diamorphine thì có nhận được alert 521), Caraxes có khả năng tàng hình triệt để bằng cách can thiệp sâu vào các hàm kiểm tra của hệ điều hành. Điều này khiến module Rootcheck của Wazuh bị đánh lừa hoàn toàn, lầm tưởng rằng không có bất kỳ tiến trình ngầm nào đang tồn tại nên không kích hoạt cảnh báo.
