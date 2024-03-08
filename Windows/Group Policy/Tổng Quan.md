## Group Policy hoạt động như thế nào ?
Với Group Policy bạn có thể quản lý các thiết lập cùng lúc cho hàng ngàn users và máy tính thay vì phải đưa ra thiết lập cho từng user hoặc từng máy tính mà không cần phải đến tận nơi làm việc của các users đó. Để làm được điều này, chúng ta sẻ sử dụng một vài công cụ quản trị để thay đổi những thiết lập cho những giá trị đã có sẵn. Với Group Policy bạn có thể dễ dàng “enable” hoặc “disable” các policy để tối ưu hóa giá trị của registry hoặc những thiết lập khác, và thay đổi này sẻ tự động “apply” đến các máy tính trong OU (Organizational Unit) đó, và nếu bạn không muốn thực thi kết quả đó, bạn có thể khôi phục lại bằng cách thay đổi những thiết lập policy để trở về các thiết lập nguyên thủy của nó một cách dễ dàng và nhanh chóng
Trong Group Policy có 02 nhánh chính :
 - Computer Configuration : Những thiết lập trong nhánh này sẻ ảnh hưởng đến toàn bộ máy tính (computer). Khi máy tính khởi động và thiết lập mạng được khởi tạo, những thiết lập computer policy và những thiết lập dựa vào registry sẻ được thực thi. Các thiết lập này sẻ được lưu tại đường dẫn : %AllUsserProfile%\Ntuser.pol
 - Và các thành phần con như:

 - Software Settings : Chính sách triển khai cài đặt phần mềm xuống Client tự động
 - Windows Settings : Tại đây chúng ta có thể tinh chỉnh , áp dụng các chính sách về vấn đề sử dụng tài khoản , quản lý khởi động và đăng nhập trên máy client .
 - Script (Logon / Logoff) : Chỉ đingj cho Windows chạy 1 đoạn mã nào đó . Ví dụ chạy đoạn mã vbs.bat khi máy tính khởi động hoặc tắt máy hiển thị nội dung như “Xin chào gia nhập domain ITFORVN.COM” hoặc “Hệ thống đang chuẩn bị được bảo trị xin vui lòng lưu tài liệu và dừng mọi công việc sau 30 phút nữa”
 - Name Resolution Policy: Các chính sách phân giải tên Security Settings: Các thiết lập bảo mật cho hệ thống, thiết lập này áp dụng cho toàn bộ hệ thống chứ không riêng người nào.
Administrative Template: Các chính sách về hệ thống Account Policies: Các chính sách áp dụng cho tài khoản người dùng.
 - Local Policy: Kiểm định chính sách, những tùy chọn quyền lợi và chính sách an toàn cho người dùng cục bộ. Public Key Policies. Các chính sách khóa dùng chung.

 - User Configuration : Những thiết lập trong nhánh này sẻ ảnh hưởng đến các users ngồi trên máy tính đó. Khi user logon vào máy tính, những thiết lập user policy và những thiết lập dựa vào registry sẻ được thực thi. Các thiết lập này sẻ được lưu tại đường dẫn %AllUsserProfile%\Ntuser.pol
 
Khi apply, những thiết lập của group policy tự động giữ lại những thiết lập hiện có và thêm vào hoặc thay đổi ứng với các thiết lập mới do người quản trị đưa ra. Mặc định, Group Policy trên domain controller tự động refresh mỗi 5 phút một lần, còn đối với các workstation và các loại dịch vụ trên server sẻ tự động được refresh 90 – 120 phút một lần

Các Group Policy Objects được lưu trữ trong cơ sở dữ liệu của Active Directory. Chương trình để tạo ra và chỉnh sửa GPO có tên là Group Policy Object Editor (đây là 1 dạng console tên là gpedit.msc, console của Active Directory Users and Computers là dsa.msc)
