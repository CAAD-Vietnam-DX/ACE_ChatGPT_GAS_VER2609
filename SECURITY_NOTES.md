# Security Notes — Ads Management & Parameter Generator

## 1. Phân loại Tool
- [ ] Tool chạy local (máy cá nhân)
- [x] Tool chạy shared (nhiều người dùng / server / cloud script)

## 2. Dữ liệu xử lý
- Loại data input: Tên Campaign, Ad Group, ID sự kiện chuyển đổi, Link Google Drive nội bộ.
- Có xử lý PII không? [Không]
- Output lưu ở đâu: Ghi trực tiếp kết quả vào file Google Sheet đích, xuất thư mục CSV/Media lên Google Drive của người dùng.
- Phương thức lưu credentials: [Không lưu credentials. Xác thực thông qua session hệ thống của Google Apps Script].

## 3. Kết nối Mạng
- Có gọi external API không? [Không]
- Có gọi LLM API không? [Không]
- Danh sách kết nối (điền đầy đủ để DX Team xác nhận):

| URL | Phương thức truy cập | Mục đích sử dụng | Xác nhận ToS/Legal |
|---|---|---|---|
| N/A | Google Apps Script (Internal) | Đọc/Ghi dữ liệu Sheet và Google Drive nội bộ | ✅ Đã xác nhận |

> ⚠️ Tool chỉ được DX Team approve khi **toàn bộ** hàng trong bảng có trạng thái ✅ Đã xác nhận.

## 4. Quyền truy cập cần thiết
- Database: [Không]
- File system: [Google Drive & Google Sheets của tài khoản Workspace nội bộ công ty]
- Network: [Không mở port]

## 5. Rule đã áp dụng (theo file RULES_VN.md)
- [x] Không hardcode secret (Mục 1.1)
- [x] Không sử dụng lệnh OS như `eval`, `exec` (Mục 1.5)
- [x] Không sử dụng GCP SDK hoặc file `credentials.json` (Mục 6.1)
- [x] Cấu trúc Standalone Script tách biệt với dữ liệu (Mục 6.3.2)
- [x] Ghi Log nội bộ đúng chuẩn, không dùng HTML Service UI/Dialog để đảm bảo an toàn cho mã nguồn (Mục 4.2 & 6.3)

## 6. Điểm DX Team cần chú ý
- Dự án ban đầu dùng giao diện Popup qua `HtmlService` gắn trực tiếp trên Sheet, nhưng đã được chuyển hoàn toàn sang cấu trúc **Standalone Script** chạy qua IDE theo yêu cầu Mục 6.3.2. Toàn bộ thông báo lỗi và tiến trình đã được xử lý thông qua hệ thống Logging của Apps Script. File tải xuống được tự động xử lý và lưu an toàn vào Google Drive của người chạy lệnh.
