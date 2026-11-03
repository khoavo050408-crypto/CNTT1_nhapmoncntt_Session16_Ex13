- Nếu tiếp tục dùng chung 1 Profile Chrome mặc định cho cả GitHub cá nhân và GitHub công ty, hệ thống sẽ xảy ra hiện tượng Xung đột phiên đăng nhập hay Ghi đè Cookie xác thực. Trình duyệt lưu trữ Cookie đăng nhập của miền github.com trong cùng một cơ sở dữ liệu Cookies thuộc thư mục User Data Directory duy nhất. Khi đăng nhập tài khoản GitHub công ty, Session Cookie mới sẽ ghi đè lên Session Cookie của tài khoản cá nhân, khiến bạn bị đăng xuất tài khoản cá nhân ngay lập tức, đồng thời gây lẫn lộn lịch sử duyệt web, SSH/OAuth keys và thông tin tự động điền giữa hai môi trường.
- Mô hình 3 Profile chuẩn Developer:
+ Profile 1: Personal - Dành riêng cho các hoạt động cá nhân (mạng xã hội, mua sắm, giải trí, email cá nhân và tài khoản GitHub cá nhân).
+ Profile 2: Company_Work - Dành riêng cho công việc tại công ty: Email công ty, Jira/Confluence, GitHub Enterprise công ty, Figma dự án.
+ Profile 3: Sandbox/Test - Dành riêng cho việc kiểm thử, chạy thử nghiệm ứng dụng nội bộ (test.internal-tool.com), chứa các tài khoản test Admin/User.
- Cấu trúc Bookmark chuẩn (3 Thư mục):
+ Thư mục 1: Work_Tools (Lưu trữ các công cụ quản lý dự án & giao tiếp: Jira / Confluence Công ty, GitHub Enterprise Công ty)
+ Thư mục 2: Tech_Docs (Lưu trữ tài liệu kỹ thuật & tra cứu: MDN Web Docs, Internal Wiki / API Documentation)
+ Thư mục 3: Testing_Tools (Lưu trữ môi trường kiểm thử & công cụ dev: Internal Tool Dashboard test.internal-tool.com, JSON Formatter / Postman Web Client)
- Giải pháp khi đồng nghiệp mượn máy 5 phút: Sử dụng Guest Profile. Người mượn máy sẽ không thể xem hay chạm vào bất kỳ dấu trang, lịch sử lướt web, mật khẩu đã lưu hay các phiên đăng nhập tài khoản của bạn. Ngay sau khi họ dùng xong và đóng cửa sổ lại, toàn bộ dữ liệu duyệt web tạm thời đó cũng sẽ tự động biến mất.
PHẦN B – CHỌN ĐÚNG EXTENSION HỖ TRỢ
- Nhóm Extension chọn: Productivity Suite. 
+ Tên tiện ích: JSON Formatter (hoặc JSON Viewer). 
+ Lý do 2 nhóm còn lại không phù hợp: Nhóm Dev Suite chủ yếu dùng để kiểm tra cấu trúc thành phần hoặc hiệu năng ứng dụng chứ không có tính năng định dạng chuỗi dữ liệu thô. Trong khi đó, nhóm Security Suite chỉ tập trung vào việc bảo vệ quyền riêng tư, chặn quảng cáo hoặc quản lý mật khẩu chứ hoàn toàn không hỗ trợ đọc dữ liệu này.
- Cấu hình quyền Site Access: Chọn On click. 
+ Giải thích theo quy tắc quản trị Extension: Tuân thủ nguyên tắc bảo mật tối thiểu, không cấp quyền On all sites để tránh nguy cơ extension tự động đọc/ghi dữ liệu nhạy cảm (mật khẩu, API token, dữ liệu nội bộ) trên tất cả các trang web. 
+ Cấu hình On click đảm bảo extension chỉ hoạt động khi bạn chủ động kích hoạt trên trang chứa chuỗi JSON thô.
PHẦN C – PHÂN TÍCH LUỒNG HTTP REQUEST
- 4 giai đoạn xử lý trong luồng HTTP Request Flow:
+ Giai đoạn 1: DNS Lookup (Phân giải tên miền từ URL thành địa chỉ IP).
+ Giai đoạn 2: TCP Handshake (Thiết lập kết nối an toàn giữa trình duyệt và Server).
+ Giai đoạn 3: HTTP Request & Response (Gửi yêu cầu HTTP Request và nhận về HTTP Response chứa mã trạng thái và nội dung).
+ Giai đoạn 4: Rendering (Phân tích cú pháp HTML/CSS/JS, dựng DOM và hiển thị giao diện lên màn hình).
- So sánh 2 mã trạng thái HTTP:
+ 404 Not Found: Không tìm thấy tài nguyên yêu cầu trên máy chủ. Lỗi ở Client-side hoặc cấu hình Route trên Server. Hướng debug: Kiểm tra lại cú pháp URL/Endpoint API xem có bị gõ sai không, kiểm tra cấu hình Routing trên Server.
+ 500 Internal Server Error: Lỗi nội bộ máy chủ (Server gặp sự cố khi xử lý request). Lỗi ở Server-side (Backend). Hướng debug: Kiểm tra hệ thống Server Logs / Backend Console để tìm đoạn mã bị crash/exception, kiểm tra kết nối Database.
- Tab DevTools xem mã trạng thái HTTP: Mở Network Tab. 
+ Giải thích: Các tab khác (Elements, Console, Application) phục vụ mục đích khác nhau
+Chỉ duy nhất Network Tab ghi lại nhật ký toàn bộ luồng gửi/nhận yêu cầu HTTP mạng kèm cột Status Code tương ứng.
PHẦN D – CHẨN ĐOÁN CACHE VÀ COOKIES
- Lỗi Dev sửa CSS nhưng F5 vẫn hiện giao diện cũ: Nguyên nhân do trình duyệt đang tải tệp CSS cũ từ bộ nhớ đệm cục bộ. 
- Thao tác khắc phục: Thực hiện Hard Reload bằng tổ hợp phím Ctrl + F5 (hoặc Cmd + Shift + R trên Mac), hoặc mở DevTools, giữ/chuột phải nút Reload và chọn "Empty Cache and Hard Reload".
- Rủi ro khi Cookie session_id không có cờ HttpOnly: 
+ Loại tấn công: XSS Attack (Cross-Site Scripting). 
+ Rủi ro cụ thể: Kẻ tấn công có thể chạy mã độc JavaScript trên trang để đọc giá trị session_id thông qua câu lệnh document.cookie, dẫn đến việc lấy cắp token và giả mạo phiên đăng nhập 
- Thao tác chỉ xóa phiên đăng nhập nhưng giữ lại Cache ảnh/CSS: 
+ Mở DevTools (F12)  Chọn tab Application  Mục Storage  chọn Cookies  chọn đúng tên miền  tìm cookie session_id, nhấp chuột phải chọn Delete.
PHẦN E – DEBUG BẰNG INSPECT ELEMENT VÀ NETWORK TAB
- Thao tác thử sửa màu nút "Đăng nhập" trực tiếp trên trình duyệt: Nhấp chuột phải vào nút chọn Inspect để mở tab Elements  Tìm thuộc tính background-color trong bảng Styles bên phải  Nhấp vào ô màu hoặc gõ mã màu mới để thử nghiệm thay đổi trực quan trên giao diện.
- Quy trình 4 Bước Debugging cho Request API báo đỏ trên Network Tab:
+ Bước 1: Reproduce: Mở Network Tab, thực hiện lại thao tác để ghi nhận request API lỗi.
+Bước 2: Isolate: Nhấp vào request lỗi, kiểm tra tab Headers/Payload và Response/Preview để đọc thông điệp lỗi từ Server.
+Bước 3: Fix: Điều chỉnh tham số truyền đi hoặc báo bộ phận Backend xử lý nếu lỗi 500.
+Bước 4: Verify: Thực hiện lại thao tác call API, kiểm tra trên Network Tab xem request chuyển sang màu xanh (Status 200 OK) và giao diện phản hồi chính xác chưa.
- 2 Thao tác cần bật trên Network Tab:
+Thao tác 1: Chọn bộ lọc Fetch/XHR để chỉ hiển thị các lời gọi API, ẩn ảnh/CSS/JS không liên quan.
+Thao tác 2: Tích chọn ô Disable cache trên thanh menu của tab Network để đảm bảo không lấy dữ liệu từ bộ nhớ đệm.
