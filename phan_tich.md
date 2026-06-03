Dưới đây là bài làm hoàn chỉnh cho bài **“Đánh giá chiến lược: Xác thực trong hệ thống Blockchain Explorer”**.

## Phần 1: Phân tích logic

Trong hệ thống **Blockchain Explorer**, người dùng có thể truy cập từ nhiều nền tảng như web, mobile và các dịch vụ backend phân tán. Vì vậy, cơ chế xác thực cần đáp ứng tốt các yêu cầu: **khả năng mở rộng cao**, **hoạt động hiệu quả qua API**, và **phù hợp với microservices**.

### 1. Session-based Authentication

**Session-based Authentication** là cơ chế xác thực truyền thống. Khi người dùng đăng nhập thành công, server tạo một session và lưu thông tin đăng nhập ở phía server. Client chỉ giữ một `sessionId`, thường được lưu trong cookie.

**Ưu điểm:**

Thứ nhất, session-based dễ triển khai và quen thuộc với nhiều hệ thống web truyền thống. Server có toàn quyền quản lý phiên đăng nhập của người dùng, nên việc đăng xuất hoặc hủy session có thể thực hiện khá đơn giản.

Thứ hai, dữ liệu xác thực được lưu ở phía server nên token phía client không chứa nhiều thông tin nhạy cảm. Điều này giúp giảm rủi ro nếu client bị lộ cookie, miễn là hệ thống có cấu hình bảo mật cookie tốt.

**Nhược điểm:**

Thứ nhất, session-based không phù hợp lắm với hệ thống cần mở rộng lớn. Vì session được lưu ở server, khi có hàng triệu người dùng đồng thời, hệ thống phải lưu trữ và đồng bộ rất nhiều session. Nếu chạy nhiều server, cần thêm cơ chế chia sẻ session như Redis, database hoặc sticky session.

Thứ hai, session-based kém linh hoạt trong môi trường API và microservices. Các dịch vụ khác nhau cần truy cập thông tin session, dẫn đến phụ thuộc vào session store tập trung. Điều này làm hệ thống phức tạp hơn và có thể tạo điểm nghẽn hiệu năng.

---

### 2. JSON Web Token - JWT

JWT là cơ chế xác thực dạng token. Sau khi người dùng đăng nhập thành công, server cấp một token có chứa thông tin định danh như username, userId, roles và thời gian hết hạn. Client gửi token này trong header:

```text
Authorization: Bearer <token>
```

**Ưu điểm:**

Thứ nhất, JWT phù hợp với hệ thống có khả năng mở rộng cao. Server không cần lưu session cho từng người dùng, vì thông tin xác thực đã nằm trong token và có thể được kiểm tra bằng chữ ký số. Điều này giúp giảm tải cho server khi hệ thống có hàng triệu người dùng.

Thứ hai, JWT hoạt động tốt với API, mobile app và microservices. Mỗi service có thể tự xác minh token mà không cần gọi về session store trung tâm. Điều này giúp hệ thống phân tán hoạt động linh hoạt và hiệu quả hơn.

**Nhược điểm:**

Thứ nhất, JWT khó thu hồi ngay lập tức nếu token còn hạn. Ví dụ, nếu người dùng đăng xuất hoặc token bị lộ, hệ thống cần thêm cơ chế blacklist, refresh token hoặc thời gian hết hạn ngắn để giảm rủi ro.

Thứ hai, JWT có thể phức tạp hơn khi triển khai bảo mật. Cần quản lý secret key/private key cẩn thận, cấu hình thời gian hết hạn hợp lý, tránh lưu quá nhiều dữ liệu trong token và đảm bảo token được truyền qua HTTPS.

---

## Phần 2: Báo cáo ngắn 300-500 từ

Trong bối cảnh xây dựng một hệ thống Blockchain Explorer phục vụ hàng triệu người dùng toàn cầu, việc lựa chọn cơ chế xác thực ảnh hưởng trực tiếp đến hiệu năng, khả năng mở rộng và độ ổn định của hệ thống. Hai phương pháp phổ biến là Session-based Authentication và JSON Web Token.

Session-based Authentication có ưu điểm là quen thuộc, dễ triển khai và dễ quản lý phiên đăng nhập. Khi người dùng đăng nhập, server tạo session và lưu thông tin ở phía server. Nhờ đó, việc hủy phiên đăng nhập hoặc logout có thể thực hiện nhanh chóng bằng cách xóa session. Ngoài ra, client chỉ giữ sessionId nên không phải lưu nhiều thông tin người dùng ở phía trình duyệt. Tuy nhiên, phương pháp này có nhược điểm lớn về khả năng mở rộng. Khi hệ thống có hàng triệu người dùng, server phải lưu trữ số lượng session rất lớn. Nếu triển khai nhiều server, hệ thống cần đồng bộ session thông qua Redis, database hoặc sticky session. Điều này làm tăng độ phức tạp và có thể tạo điểm nghẽn trong kiến trúc phân tán. Session-based cũng không thật sự tối ưu cho mobile app và microservices vì nhiều dịch vụ cần truy cập chung vào session store.

Ngược lại, JWT phù hợp hơn với hệ thống hiện đại dựa trên API. Sau khi đăng nhập, server cấp cho người dùng một token chứa thông tin định danh và quyền hạn. Client gửi token trong header của mỗi request. Ưu điểm lớn nhất của JWT là tính stateless, nghĩa là server không cần lưu session cho từng người dùng. Điều này giúp hệ thống mở rộng dễ dàng hơn khi có nhiều server hoặc nhiều microservices. JWT cũng hoạt động tốt trên web, mobile và các dịch vụ phân tán vì mỗi service có thể tự xác minh token bằng chữ ký. Tuy nhiên, JWT cũng có nhược điểm. Token khó bị thu hồi ngay lập tức nếu chưa hết hạn, do đó cần thêm cơ chế refresh token, blacklist hoặc thời gian sống ngắn. Ngoài ra, việc quản lý khóa ký token, bảo vệ token và thiết kế payload cần được thực hiện cẩn thận.

Với Blockchain Explorer, tôi đề xuất sử dụng **JWT kết hợp Access Token và Refresh Token**. Access Token nên có thời gian sống ngắn để giảm rủi ro, còn Refresh Token dùng để cấp lại token mới khi cần. Giải pháp này phù hợp với hệ thống lớn, nhiều nền tảng và kiến trúc microservices, đồng thời vẫn đảm bảo hiệu năng và trải nghiệm người dùng.
