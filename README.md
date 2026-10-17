# eTicket — Nền tảng đặt vé sự kiện trực tuyến

Hệ thống backend hỗ trợ tìm kiếm sự kiện, chọn suất tổ chức, giữ chỗ, đặt vé, thanh toán và quản lý vé điện tử, được xây dựng bằng Java và Spring Boot.

## 1. Tổng quan dự án

eTicket là nền tảng đặt vé sự kiện trực tuyến, kết nối khách hàng với các đơn vị tổ chức sự kiện.

Khách hàng có thể tìm kiếm sự kiện, lựa chọn suất tổ chức, chọn ghế hoặc loại vé, giữ chỗ, thanh toán và nhận vé điện tử. Đơn vị tổ chức có thể quản lý sự kiện, lịch diễn, sơ đồ ghế, số lượng vé, đơn đặt vé và hoạt động check-in. Quản trị viên chịu trách nhiệm quản lý người dùng, phê duyệt sự kiện và giám sát hoạt động toàn hệ thống.

Dự án tập trung vào các vấn đề quan trọng trong phát triển backend như tính nhất quán dữ liệu, xử lý giao dịch, kiểm soát truy cập đồng thời, tính idempotent trong thanh toán và đảm bảo phát hành vé chính xác.

## 2. Các chức năng chính

### 2.1. Khách hàng (CUSTOMER)

- Đăng ký, đăng nhập, đăng xuất và làm mới token xác thực.
- Xem và cập nhật thông tin cá nhân.
- Tìm kiếm, lọc và xem chi tiết sự kiện.
- Lựa chọn suất tổ chức, loại vé, ghế ngồi hoặc số lượng vé.
- Giữ chỗ trong vòng 10 phút.
- Tạo đơn đặt vé và thực hiện thanh toán.
- Xem lịch sử đặt vé và danh sách vé đã phát hành.
- Sử dụng mã QR để check-in tại sự kiện.

### 2.2. Đơn vị tổ chức (ORGANIZER)

- Tạo và quản lý sự kiện, các suất tổ chức.
- Cấu hình loại vé, giá vé, số lượng vé và giới hạn mua.
- Thiết lập địa điểm, khu vực và sơ đồ ghế cho sự kiện có chỗ ngồi.
- Gửi sự kiện để quản trị viên phê duyệt.
- Gửi yêu cầu thay đổi các thông tin quan trọng của sự kiện đã phát hành.
- Theo dõi đơn đặt vé, số vé đã bán, số vé còn lại và doanh thu.
- Theo dõi và thực hiện check-in vé tại sự kiện.

### 2.3. Quản trị viên (ADMIN)

- Cấp và quản lý tài khoản đơn vị tổ chức.
- Quản lý trạng thái tài khoản khách hàng và đơn vị tổ chức.
- Phê duyệt hoặc từ chối sự kiện, kèm lý do cụ thể.
- Phê duyệt yêu cầu thay đổi thông tin sự kiện đã phát hành.
- Theo dõi sự kiện, đơn đặt vé, thanh toán và vé trên toàn hệ thống.
- Xem thống kê và báo cáo hoạt động toàn hệ thống.

## 3. Các khái niệm nghiệp vụ cốt lõi

| Khái niệm | Mô tả |
|---|---|
| Event | Sự kiện tổng thể, có thể bao gồm nhiều suất tổ chức. |
| Show | Một lần tổ chức cụ thể của sự kiện, có thời gian và địa điểm xác định. |
| Ticket Type | Loại vé với mức giá, số lượng và giới hạn mua riêng. |
| Seat | Ghế vật lý thuộc một khu vực trong địa điểm tổ chức. |
| Show Seat | Đại diện cho một ghế trong một suất tổ chức cụ thể, có trạng thái vé riêng. |
| Booking | Đơn đặt vé của khách hàng, có thể chứa nhiều vé. |
| Payment | Một lần thực hiện thanh toán cho đơn đặt vé. |
| Ticket | Vé điện tử được phát hành sau khi đơn đặt vé được xác nhận. |
| Check-in | Quá trình xác thực và ghi nhận vé khi khách tham dự sự kiện. |

## 4. Mô hình bán vé

Hệ thống hỗ trợ hai mô hình bán vé:

- **SEATED — Vé có chỗ ngồi:** Khách hàng lựa chọn ghế cụ thể. Trạng thái khả dụng của ghế được quản lý riêng cho từng suất tổ chức.
- **GENERAL_ADMISSION — Vé không định danh ghế:** Khách hàng chọn loại vé và số lượng cần mua mà không phải chọn ghế cụ thể.

Hệ thống được thiết kế để hạn chế tình trạng nhiều khách hàng đặt trùng một ghế hoặc số lượng vé được đặt vượt quá số vé cho phép.

## 5. Các luồng nghiệp vụ chính

### 5.1. Quản lý và phát hành sự kiện

1. Đơn vị tổ chức tạo sự kiện và các suất tổ chức.
2. Cấu hình loại vé, giá vé, số lượng và sơ đồ ghế nếu cần.
3. Gửi sự kiện để quản trị viên phê duyệt.
4. Sau khi được duyệt, sự kiện được cấu hình và mở bán theo kế hoạch.
5. Khách hàng có thể tìm kiếm và đặt vé khi sự kiện đủ điều kiện mở bán.

### 5.2. Tìm kiếm và đặt vé

1. Khách hàng tìm kiếm sự kiện và chọn suất tổ chức.
2. Lựa chọn loại vé, ghế ngồi hoặc số lượng vé.
3. Hệ thống kiểm tra tình trạng vé và giới hạn mua.
4. Nếu hợp lệ, hệ thống giữ chỗ trong 10 phút.
5. Khách hàng thực hiện thanh toán trong thời gian giữ chỗ.

### 5.3. Booking và thanh toán

- Booking chỉ được xác nhận sau khi hệ thống xác minh thanh toán thành công.
- Nếu giữ chỗ hết hạn mà chưa xác nhận được thanh toán, hệ thống xử lý trạng thái Booking và giải phóng tài nguyên theo quy tắc nghiệp vụ.
- Các yêu cầu hoặc callback thanh toán trùng lặp phải được xử lý theo cơ chế idempotent.
- Các trường hợp thanh toán chưa rõ kết quả cần có cơ chế thử lại hoặc đối soát trước khi kết luận thất bại.

### 5.4. Phát hành vé và check-in

- Vé điện tử chỉ được phát hành sau khi Booking được xác nhận.
- Mỗi vé có mã định danh và mã QR riêng.
- Khi check-in thành công, vé chuyển sang trạng thái đã sử dụng.
- Hệ thống từ chối check-in lần tiếp theo đối với vé đã sử dụng.
- Các lần quét vé được ghi nhận để phục vụ kiểm tra và thống kê.

## 6. Công nghệ sử dụng

| Công nghệ | Mục đích |
|---|---|
| Java | Ngôn ngữ lập trình chính |
| Spring Boot | Xây dựng ứng dụng backend |
| Spring Data JPA / Hibernate | Ánh xạ đối tượng và truy cập cơ sở dữ liệu |
| PostgreSQL | Hệ quản trị cơ sở dữ liệu quan hệ |
| Spring Security + JWT | Xác thực và phân quyền |
| Redis | Hỗ trợ dữ liệu tạm thời, cache và cơ chế giữ chỗ khi phù hợp |
| Apache Kafka | Xử lý thông điệp bất đồng bộ và kiến trúc hướng sự kiện |
| Docker | Đóng gói và triển khai các thành phần trong môi trường container |
| JUnit + Mockito | Kiểm thử đơn vị và các thành phần ứng dụng |
| Testcontainers | Kiểm thử tích hợp với các dịch vụ thực tế |
| Swagger / OpenAPI | Mô tả và tài liệu hóa API |
| GitHub Actions | Tự động hóa quy trình kiểm thử và CI |

## 7. Các vấn đề kỹ thuật trọng tâm

Dự án hướng đến giải quyết các bài toán backend quan trọng:

- **Kiểm soát truy cập đồng thời:** Ngăn nhiều khách hàng giữ cùng một ghế hoặc đặt vượt số lượng vé còn lại.
- **Hết hạn giữ chỗ:** Tự động giải phóng vé hoặc ghế khi thời gian giữ chỗ kết thúc mà chưa xác nhận thanh toán.
- **Idempotency trong thanh toán:** Đảm bảo callback hoặc yêu cầu thanh toán trùng lặp không làm ghi nhận giao dịch nhiều lần.
- **Đối soát thanh toán:** Xử lý trường hợp nhà cung cấp thanh toán đã ghi nhận thành công nhưng hệ thống chưa nhận được kết quả xác nhận.
- **Check-in nguyên tử:** Đảm bảo một vé chỉ được check-in thành công một lần, kể cả khi nhiều thiết bị gửi yêu cầu đồng thời.
- **Phân quyền theo vai trò:** Giới hạn chức năng theo quyền của CUSTOMER, ORGANIZER và ADMIN.
- **Lưu vết nghiệp vụ:** Lưu lịch sử check-in và các yêu cầu thay đổi quan trọng của sự kiện.

## 8. Thiết kế cơ sở dữ liệu

Cơ sở dữ liệu được tổ chức thành bốn nhóm nghiệp vụ chính:

- **Identity & Access:** `users`, `roles`, `user_roles`, `organizers`.
- **Event Management:** `events`, `event_change_requests`, `shows`, `venues`, `sections`, `seats`, `ticket_types`, `show_seats`.
- **Booking & Payment:** `bookings`, `booking_items`, `payments`.
- **Ticket & Check-in:** `tickets`, `check_ins`.

Một số định hướng thiết kế quan trọng:

- Phân biệt ghế vật lý với trạng thái ghế trong từng suất tổ chức.
- Cho phép một Booking có nhiều lần thử thanh toán.
- Lưu giá vé tại thời điểm đặt để đảm bảo tính chính xác của giao dịch.
- Mỗi vé điện tử có mã riêng để phục vụ xác thực và check-in.
- Lưu lịch sử check-in nhằm hỗ trợ kiểm tra và đối soát.

## 9. Tài liệu dự án

- [Phân tích nghiệp vụ](docs/business-analysis.md): Các tác nhân, khái niệm, luồng nghiệp vụ, vòng đời trạng thái và yêu cầu chức năng.
- [Thiết kế cơ sở dữ liệu](docs/database-design.md): Danh sách bảng, các trường dữ liệu, quan hệ giữa các bảng và những lưu ý thiết kế.

*Lưu ý: Cập nhật đường dẫn trong mục này nếu tên file hoặc cấu trúc thư mục thực tế của dự án khác với ví dụ trên.*

## 10. Trạng thái dự án

**Giai đoạn hiện tại: Đã hoàn thành phân tích nghiệp vụ, thiết kế cơ sở dữ liệu và khởi tạo bộ khung Spring Boot.**

- [x] Phân tích nghiệp vụ.
- [x] Thiết kế cơ sở dữ liệu ban đầu.
- [x] Khởi tạo bộ khung dự án Spring Boot.
- [ ] Xây dựng migration và triển khai schema cơ sở dữ liệu.
- [ ] Xây dựng chức năng xác thực và phân quyền.
- [ ] Phát triển API quản lý sự kiện.
- [ ] Xây dựng quy trình giữ chỗ và đặt vé.
- [ ] Tích hợp thanh toán và đối soát giao dịch.
- [ ] Phát hành vé điện tử và xây dựng chức năng check-in.
- [ ] Hoàn thiện kiểm thử tự động và quy trình CI.
- [ ] Hoàn thiện môi trường triển khai bằng Docker.

## 11. Hướng dẫn chạy dự án

Hướng dẫn cài đặt và chạy ứng dụng sẽ được bổ sung khi hoàn thiện cấu hình ứng dụng, cơ sở dữ liệu và môi trường phát triển.

## 12. Định hướng phát triển

Các chức năng có thể được mở rộng trong giai đoạn tiếp theo:

- Hủy đặt vé và hoàn tiền theo chính sách.
- Tích hợp nhà cung cấp thanh toán thực tế.
- Gửi thông báo qua email hoặc các kênh phù hợp.
- Bổ sung báo cáo doanh thu và phân tích hoạt động bán vé.

## 13. Giấy phép

Giấy phép sử dụng dự án sẽ được xác định sau.
