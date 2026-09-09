# Cấu hình Wazuh phát hiện các tiến trình ẩn
## Mục tiêu
Cấu hình và kích hoạt module **Rootcheck** trên Wazuh Agent để định kỳ rà quét sâu vào hệ thống mức kernel.
* Tối ưu hóa tần suất quét (giảm xuống 120 giây) nhằm phục vụ cho việc kiểm thử tính năng đối chiếu API hệ thống, chuẩn bị sẵn sàng cho kịch bản mô phỏng phát hiện mã độc Rootkit (sẽ thực hiện ở phần Simulations).
## Cấu hình trên Ubuntu Agent
Ta sẽ cấu hình tần suất quét của module Rootcheck, theo mặc định, module Rootcheck của Wazuh được thiết lập quét hệ thống 12 giờ một lần (43200 giây) để tối ưu hiệu năng. Tuy nhiên, để phục vụ việc kiểm thử trong môi trường Lab, chúng ta sẽ giảm thời gian này xuống.

<img width="623" height="222" alt="image" src="https://github.com/user-attachments/assets/6aed4e1a-642e-458f-8eae-e387f67ee311" />


