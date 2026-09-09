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
| N/A | Google Apps Script (Internal) | Đọc/Ghi dữ liệu Sheet và Google Drive nội bộ | ⬜ Chưa xác nhận đầy đủ |

> ⚠️ Tool chỉ được DX Team approve khi **toàn bộ** hàng trong bảng có trạng thái ✅ Đã xác nhận.

> ⛔ **Chưa thể xác nhận:** File `main` có gọi `HtmlService.createTemplateFromFile('Index')` (dùng cho dialog tải file ZIP ở hàm `getData` và `createBulkFiles`), nhưng file `Index` (client-side HTML/JS) **chưa từng được export/commit** vào repo. Không thể xác minh phần code này có gọi CDN/domain ngoài nào không → tuyên bố "Không gọi external API" ở trên hiện chưa có cơ sở đầy đủ.

## 4. Quyền truy cập cần thiết
- Database: [Không]
- File system: [Google Drive & Google Sheets của tài khoản Workspace nội bộ công ty]
- Network: [Không mở port]

## 5. Rule đã áp dụng (theo file RULES_VN.md)
- [x] Không hardcode secret (Mục 1.1)
- [x] Không sử dụng lệnh OS như `eval`, `exec` (Mục 1.5)
- [x] Không sử dụng GCP SDK hoặc file `credentials.json` (Mục 6.1)
- [ ] Cấu trúc Standalone Script tách biệt với dữ liệu (Mục 6.3.2) — **Đang chờ xác nhận từ tác giả**, xem chi tiết Mục 6.2
- [ ] Ghi Log nội bộ đúng chuẩn (Mục 4.2 & 4.3) — **CHƯA đạt**: file không có bất kỳ `Logger.log` nào, nhiều khối `catch` rỗng nuốt lỗi im lặng. Xem Mục 6.
- [ ] Export đầy đủ toàn bộ code Apps Script để DX Team review (Mục 6.3.4) — **CHƯA đạt**: thiếu file `Index.html`. Xem Mục 6.
- [ ] Versioning & Changelog (Mục 5.5) — **CHƯA đạt**: thiếu header Version/Last Updated/Author, thiếu `CHANGELOG.md`.

## 6. Điểm DX Team cần chú ý

1. **[Cần xử lý trước khi duyệt] Thiếu file `Index.html`**: Code gọi `HtmlService.createTemplateFromFile('Index')` ở 2 nơi (hàm `getData`, `createBulkFiles`) để hiển thị dialog tải file ZIP, nhưng file `Index` (client-side HTML/JS) chưa từng được export/commit vào repo (chỉ có file `main`). DX Team không thể review phần code này — có thể chứa script tải từ CDN ngoài, xử lý dữ liệu không an toàn (XSS khi render tên file), v.v. Đề nghị bổ sung export file này trước khi duyệt.

2. **[⛔ CHẶN — cần tác giả xác nhận trước khi approve] Nguy cơ vi phạm Mục 6.3.2 (container-bound script)**: Cả 5/5 hàm chính (`getData`, `deleteData`, `createBulkFiles`, `checkData`, `generateParams`) đều có comment "Gán hàm này cho nút [X]" — xác nhận đây là chủ ý thiết kế dùng nút vẽ (Drawing) trên Sheet để chạy, không phải comment sót lại từ bản cũ. File không có `onOpen()`, không `createMenu`, không có lời gọi `Library.xxx()` nào.
   - **Trạng thái:** Xác nhận flow vận hành.

3. **README chưa khớp với code**: README liệt kê hàm `duplicateCRImages` ("Dò tìm và đổi tên bản sao của Media") nhưng hàm này **không tồn tại** trong file `main`. README cũng hướng dẫn "Thay thế giá trị `TARGET_SHEET_ID` ở đầu file main.gs" nhưng biến này **không tồn tại** — thay vào đó có 1 Google Sheet ID của file ngoài bị hardcode sâu trong hàm `getData()`, không dễ cấu hình như README mô tả. Member làm theo hướng dẫn hiện tại sẽ không tìm thấy các mục này.

4. **Logging & Error Handling (Mục 4.2/4.3)**: Toàn bộ file không có một lệnh `Logger.log`/`console.log` nào — không có log nội bộ khi có lỗi. Nhiều khối `catch` rỗng nuốt lỗi im lặng (không ghi nhận nguyên nhân). Một số nơi trả thẳng `error.message` ra dialog cho end-user mà không qua log nội bộ trước, không đúng nguyên tắc "ghi log rõ ràng trước khi trả thông báo chung chung cho người dùng".

5. **Versioning (Mục 5.5)**: File `main` không có header comment (Tool/Version/Last Updated/Author/Description) ở đầu file. Repo cũng không có `CHANGELOG.md`.

6. **Thao tác xóa không có bước xác nhận**: Hàm `deleteData()` (gán cho nút "DELETE") xóa sheet và dữ liệu vĩnh viễn ngay khi gọi, không có `ui.alert` hỏi xác nhận trước — nên cân nhắc thêm bước confirm cho thao tác không thể hoàn tác này.

7. **Quy cách đặt tên file export (Mục 6.3.4)**: Nên đổi tên file export từ `main` sang dạng `main.gs.txt` theo đúng khuyến nghị đặt tên rõ ràng khi export code Apps Script ra `.txt`.

8. **Lưu ý nhẹ (không phải lỗi)**: Một số hàm (`getData`, `checkData`, `createBulkFiles`) khá dài. Đây là chủ ý của người viết code nên không yêu cầu tách nhỏ theo Mục 4.1 — chỉ ghi nhận để người maintain sau này lưu ý khi đọc/sửa code.

- Dự án ban đầu dùng giao diện Popup qua `HtmlService` gắn trực tiếp trên Sheet, nhưng đã được chuyển sang cấu trúc **Standalone Script** theo yêu cầu Mục 6.3.2 — kiến trúc này đạt yêu cầu tách access list khỏi Sheet. File tải xuống được tự động xử lý và lưu vào Google Drive của người chạy lệnh.
