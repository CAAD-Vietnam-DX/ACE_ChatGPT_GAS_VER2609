# Hướng dẫn sử dụng: Ads Management & Parameter Generator

Công cụ tự động hóa quy trình quảng cáo: Dọn dẹp dữ liệu, trích xuất Media từ Google Drive, kiểm tra lỗi, đóng gói file ZIP CSV và tự động tạo Parameter cho Ads (Standalone Architecture).

### Yêu cầu cài đặt
- Chạy dưới dạng **Standalone Script** trên nền tảng Google Apps Script (tạo project tại `script.google.com`).
- Tài khoản sử dụng phải thuộc hệ thống nội bộ công ty (`@ca-adv...`).
- Lần đầu chạy sẽ yêu cầu cấp quyền truy cập vào Google Sheets và Google Drive.

### Hướng dẫn sử dụng
1. Mở code tại `script.google.com`.
2. Thay thế giá trị `TARGET_SHEET_ID` ở đầu file `main.gs` bằng ID thực tế của file Google Sheet làm việc.
3. Dán dữ liệu cần xử lý vào Sheet `データ` (bắt đầu từ dòng 12).
4. Chọn hàm tương ứng từ thanh công cụ của Apps Script và nhấn **Run**:
   - `getData`: Trích xuất dữ liệu, lưu file Media vào thư mục trên Google Drive.
   - `duplicateCRImages`: Dò tìm và đổi tên bản sao của Media.
   - `checkData`: Kiểm tra và đối chiếu dữ liệu giữa các Sheet, tự động tạo file backup.
   - `createBulkFiles`: Sinh file CSV và lưu vào thư mục `Export_Bulk_YYYYMMDD` trên Google Drive.
   - `generateParams`: Gửi dữ liệu đi để sinh Parameter và cập nhật vào Sheet AD.
   - `deleteData`: Làm sạch dữ liệu rác trong các Sheet.
5. Xem tiến độ và kết quả tại mục **Execution Log (Nhật ký thực thi)** bên dưới trình soạn thảo.

### Troubleshoot
- **Thiếu quyền truy cập Drive:** Hãy chắc chắn tài khoản chạy Script đã được Share quyền Viewer với tất cả thư mục Drive. Tool sẽ tự bỏ qua và báo lỗi link bị chặn trong Log.
- Nếu hệ thống gặp sự cố kẹt luồng, hãy kiểm tra lại Execution Log để biết chi tiết và liên hệ DX Team.
