# ỨNG DỤNG ĐẶT MÓN ĂN - TÀI LIỆU MÔ TẢ TÍNH NĂNG

**Phiên bản:** 1.0.0  
**Ngày tạo:** 01/02/2026  
**Dành cho:** Khách hàng, Nhà quản lý, Người không chuyên về kỹ thuật

---

## 📖 TỔNG QUAN DỰ ÁN

Ứng dụng di động đặt món ăn trực tuyến giúp khách hàng dễ dàng xem thực đơn, đặt món và thanh toán ngay trên điện thoại. Quản trị viên có thể quản lý món ăn, đơn hàng và theo dõi doanh thu một cách thuận tiện.

**Điểm nổi bật:** Hệ thống gợi ý món ăn thông minh (AI) giúp tăng doanh thu bằng cách đề xuất các món kèm phù hợp với sở thích khách hàng.

---

## 👥 VAI TRÒ NGƯỜI DÙNG

### **1. Người dùng (Khách hàng)**
- Xem thực đơn và đặt món ăn
- Quản lý giỏ hàng
- Theo dõi đơn hàng
- Gửi đánh giá và phản hồi

### **2. Quản trị viên (Admin)**
- Quản lý món ăn (thêm, sửa, xóa)
- Quản lý đơn hàng của khách
- Xem phản hồi từ khách hàng
- Theo dõi doanh thu và thống kê

---

## 🎯 DANH SÁCH TÍNH NĂNG (25 NHÓM)

## **NHÓM 1: QUẢN LÝ TÀI KHOẢN**

### ✅ Đăng nhập
- Khách hàng và Admin đăng nhập bằng email và mật khẩu
- Có tùy chọn "Ghi nhớ đăng nhập" để không phải đăng nhập lại
- Hệ thống tự động phân quyền Admin hoặc User

### ✅ Đăng ký tài khoản mới
- Người dùng mới có thể tạo tài khoản
- Nhập: Họ tên, Email, Số điện thoại, Mật khẩu
- Xác nhận đồng ý điều khoản sử dụng

### ✅ Quên mật khẩu
- Khôi phục mật khẩu qua email
- Hệ thống gửi link reset mật khẩu vào email đã đăng ký

### ✅ Đổi mật khẩu
- Người dùng đã đăng nhập có thể đổi mật khẩu
- Yêu cầu nhập mật khẩu cũ để xác thực

### ✅ Hồ sơ cá nhân (Profile)
- Xem và chỉnh sửa thông tin: Tên, Số điện thoại, Địa chỉ
- Thay đổi ảnh đại diện
- Email không thể thay đổi

### ✅ Đăng xuất
- Thoát khỏi tài khoản hiện tại

---

## **NHÓM 2: QUẢN LÝ MÓN ĂN (ADMIN)**

### ✅ Xem danh sách món ăn
- Admin xem tất cả món ăn trong hệ thống
- Hiển thị: Hình ảnh, Tên món, Giá, Trạng thái (Còn/Hết)

### ✅ Thêm món ăn mới
- Admin thêm món ăn với thông tin:
  - Hình ảnh món
  - Tên món ăn
  - Giá tiền
  - Danh mục (Món chính, Đồ uống, Tráng miệng, Khai vị)
  - Mô tả chi tiết
  - Đánh dấu "Món phổ biến" hoặc "Món gợi ý"

### ✅ Chỉnh sửa món ăn
- Admin cập nhật thông tin món ăn đã có

### ✅ Xóa món ăn
- Admin xóa món không còn kinh doanh
- Có xác nhận trước khi xóa

### ✅ Tìm kiếm món ăn theo tên
- Admin tìm nhanh món ăn trong danh sách

---

## **NHÓM 3: QUẢN LÝ PHẢN HỒI (ADMIN)**

### ✅ Xem danh sách phản hồi
- Admin xem tất cả phản hồi từ khách hàng
- Hiển thị: Tên khách, Nội dung, Đánh giá sao, Thời gian
- Phân loại: Mới/Đã đọc

### ✅ Xem chi tiết phản hồi
- Admin đọc đầy đủ nội dung phản hồi
- Tự động đánh dấu "Đã đọc"

### ✅ Xóa phản hồi
- Xóa các phản hồi không phù hợp

---

## **NHÓM 4: QUẢN LÝ ĐƠN HÀNG (ADMIN)**

### ✅ Xem tất cả đơn hàng
- Admin xem danh sách đơn hàng của toàn bộ khách
- Hiển thị: Mã đơn, Tên khách, Tổng tiền, Trạng thái
- Lọc theo: Trạng thái, Ngày
- Tìm kiếm theo mã đơn hoặc tên khách

### ✅ Xem chi tiết đơn hàng
- Thông tin khách hàng (Tên, SĐT, Địa chỉ)
- Danh sách món đã đặt
- Ghi chú của khách
- Tổng tiền

### ✅ Cập nhật trạng thái đơn hàng
- Admin thay đổi trạng thái:
  - Chờ xác nhận
  - Đang xử lý
  - Hoàn thành
  - Đã hủy
- Gửi thông báo cho khách khi thay đổi trạng thái

### ✅ Thống kê đơn hàng
- Tổng số đơn hàng
- Số đơn theo từng trạng thái
- Biểu đồ theo thời gian

---

## **NHÓM 5: THEO DÕI DOANH THU (ADMIN)**

### ✅ Doanh thu theo ngày
- Admin chọn ngày cụ thể để xem doanh thu
- Hiển thị: Tổng tiền, Số đơn hoàn thành, Món bán chạy nhất

### ✅ Doanh thu theo tháng
- Biểu đồ doanh thu từng ngày trong tháng
- So sánh với tháng trước

### ✅ Doanh thu theo khoảng thời gian
- Chọn ngày bắt đầu và kết thúc
- Xuất báo cáo PDF

### ✅ Top món bán chạy
- Xem 10 món ăn bán chạy nhất
- Số lượng đã bán và doanh thu

---

## **NHÓM 6: HIỂN THỊ MÓN PHỔ BIẾN**

### ✅ Slideshow món phổ biến
- Trang chủ hiển thị các món ăn phổ biến dạng slide tự động chuyển
- Hình ảnh đẹp, chuyển đổi mượt mà
- Click vào ảnh để xem chi tiết món

---

## **NHÓM 7: GỢI Ý MÓN ĂN**

### ✅ Danh sách món gợi ý
- Hiển thị các món được gợi ý ở trang chủ
- Cập nhật tự động từ hệ thống

---

## **NHÓM 8: TÌM KIẾM MÓN ĂN**

### ✅ Tìm kiếm theo tên
- Ô tìm kiếm ở trang chủ
- Kết quả hiển thị ngay khi gõ (tìm kiếm nhanh)
- Lưu lịch sử tìm kiếm

### ✅ Lọc theo danh mục
- Lọc món theo: Tất cả, Món chính, Đồ uống, Tráng miệng, Khai vị

### ✅ Sắp xếp món ăn
- Sắp xếp theo:
  - Giá: Thấp đến Cao
  - Giá: Cao đến Thấp
  - Đánh giá cao nhất
  - Mới nhất

---

## **NHÓM 9: CHI TIẾT MÓN ĂN**

### ✅ Xem thông tin chi tiết
- Hình ảnh lớn (vuốt để xem nhiều ảnh)
- Tên món, Giá
- Đánh giá sao và số lượt đánh giá
- Mô tả chi tiết
- Nút "Thêm vào giỏ hàng"

### ✅ Thư viện ảnh món ăn
- Xem nhiều ảnh món ăn từ góc độ khác nhau
- Phóng to ảnh

### ✅ Đánh giá & Nhận xét
- Xem đánh giá từ khách hàng khác
- Người dùng có thể thêm đánh giá của mình

---

## **NHÓM 10: GIỎ HÀNG**

### ✅ Thêm món vào giỏ
- Chọn số lượng khi thêm
- Hiệu ứng khi thêm thành công
- Hiển thị số lượng món trong giỏ

### ✅ Xem giỏ hàng
- Danh sách tất cả món đã thêm
- Mỗi món hiển thị: Ảnh, Tên, Giá, Số lượng
- Tổng tiền tất cả món

### ✅ Thay đổi số lượng
- Tăng/giảm số lượng món trong giỏ
- Tổng tiền tự động cập nhật

### ✅ Xóa món khỏi giỏ
- Xóa từng món không muốn mua
- Có thể vuốt để xóa
- Có nút "Hoàn tác" nếu xóa nhầm

### ✅ Xóa toàn bộ giỏ hàng
- Xóa tất cả món trong giỏ
- Có xác nhận trước khi xóa

---

## **NHÓM 11: ĐẶT HÀNG**

### ✅ Tạo đơn hàng
- Xác nhận món trong giỏ hàng
- Nhập thông tin giao hàng:
  - Tên người nhận
  - Số điện thoại
  - Địa chỉ giao hàng
- Chọn phương thức thanh toán:
  - Tiền mặt khi nhận hàng (COD)
  - Chuyển khoản
  - Ví điện tử (Momo, ZaloPay)
- Thêm ghi chú cho đơn hàng (tùy chọn)

### ✅ Xác nhận đặt hàng thành công
- Hiển thị mã đơn hàng
- Thời gian dự kiến giao hàng
- Nút xem đơn hàng hoặc về trang chủ

### ✅ Lưu địa chỉ giao hàng
- Lưu nhiều địa chỉ khác nhau
- Đặt địa chỉ mặc định
- Chọn nhanh khi đặt hàng

---

## **NHÓM 12: LỊCH SỬ ĐƠN HÀNG**

### ✅ Xem danh sách đơn đã đặt
- Hiển thị tất cả đơn hàng đã đặt
- **Màu xám**: Đơn đã hoàn thành
- **Màu trắng**: Đơn chưa hoàn thành
- **Màu đỏ**: Đơn đã hủy
- Lọc theo trạng thái

### ✅ Xem chi tiết đơn hàng
- Mã đơn, Trạng thái
- Danh sách món đã đặt
- Thông tin giao hàng
- Tổng tiền
- Timeline trạng thái đơn hàng

### ✅ Hủy đơn hàng
- Hủy đơn khi còn ở trạng thái "Chờ xác nhận"
- Chọn lý do hủy

### ✅ Đặt lại đơn hàng
- Đặt lại món từ đơn hàng cũ
- Có thể chỉnh sửa trước khi đặt

---

## **NHÓM 13: PHẢN HỒI & ĐÁNH GIÁ**

### ✅ Gửi phản hồi
- Đánh giá sao (1-5 sao)
- Viết nhận xét về dịch vụ
- Đính kèm hình ảnh (tùy chọn)

### ✅ Xem phản hồi đã gửi
- Xem lại các phản hồi đã gửi
- Chỉnh sửa hoặc xóa phản hồi

---

## **NHÓM 14: THÔNG TIN LIÊN HỆ**

### ✅ Liên hệ với quán
- **Facebook**: Mở trang Facebook quán
- **Skype**: Chat qua Skype
- **Gọi điện**: Gọi trực tiếp số điện thoại quán
- **YouTube**: Xem video giới thiệu
- **Zalo**: Chat qua Zalo
- **Email**: Gửi email

### ✅ Xem vị trí trên bản đồ
- Hiển thị vị trí quán trên Google Maps
- Nút "Chỉ đường" mở Google Maps để dẫn đường

---

## **NHÓM 15: TÍNH NĂNG AI - GỢI Ý THÔNG MINH ⭐**

### ✅ Gợi ý món kèm thông minh
**Mô tả:** Khi khách chọn mua món, ứng dụng sẽ tự động gợi ý các món kèm phù hợp.

**Ví dụ:**
- Khách chọn "Gà rán" → Gợi ý "Khách mua gà thường uống kèm Pepsi"
- Đang giờ trưa → "Bạn có muốn thêm canh súp không?"
- Khách mua món chiên → Gợi ý thêm salad

**Lợi ích:**
- Khách hàng: Được gợi ý món ngon, phù hợp
- Quán: Tăng doanh thu mỗi đơn hàng

### ✅ Hiển thị popup gợi ý
- Khi thêm món vào giỏ, hiển thị popup gợi ý
- Khách có thể chọn thêm hoặc bỏ qua
- Giao diện đẹp, dễ sử dụng

### ✅ Thống kê hiệu quả AI (Admin)
- Admin xem số lần khách chấp nhận gợi ý
- Doanh thu tăng thêm từ tính năng này
- Top món được mua kèm nhiều nhất

---

## **NHÓM 16: THÔNG BÁO**

### ✅ Thông báo đẩy (Push Notification)
- Thông báo khi đơn hàng được xác nhận
- Thông báo khi đơn hàng đang xử lý
- Thông báo khi đơn hàng hoàn thành
- Thông báo khuyến mãi mới
- Thông báo món yêu thích giảm giá

### ✅ Thông báo trong ứng dụng
- Xem lại tất cả thông báo
- Đánh dấu đã đọc/chưa đọc
- Xóa thông báo

---

## **NHÓM 17: YÊU THÍCH**

### ✅ Lưu món yêu thích
- Click icon "trái tim" để lưu món yêu thích
- Lưu trữ trên tài khoản

### ✅ Xem danh sách yêu thích
- Xem tất cả món đã lưu
- Thêm nhanh vào giỏ hàng

---

## **NHÓM 18: KHUYẾN MÃI & MÃ GIẢM GIÁ**

### ✅ Xem khuyến mãi
- Banner khuyến mãi ở trang chủ
- Danh sách chương trình khuyến mãi
- Chi tiết: Điều kiện, Thời gian, Giá trị giảm

### ✅ Áp dụng mã giảm giá
- Nhập mã giảm giá khi đặt hàng
- Kiểm tra mã hợp lệ
- Hiển thị số tiền được giảm

### ✅ Quản lý mã giảm giá (Admin)
- Tạo mã giảm giá mới
- Thiết lập: Phần trăm/Số tiền giảm, Điều kiện, Thời gian
- Xem số lượt sử dụng
- Vô hiệu hóa mã

---

## **NHÓM 19: BÁO CÁO & THỐNG KÊ (ADMIN)**

### ✅ Dashboard tổng quan
- Doanh thu hôm nay
- Số đơn hàng hôm nay
- Số khách hàng mới
- Biểu đồ doanh thu 7 ngày
- Top 5 món bán chạy

### ✅ Báo cáo doanh thu chi tiết
- Xuất báo cáo PDF hoặc Excel
- Gửi báo cáo qua email

### ✅ Thống kê khách hàng
- Tổng số khách hàng
- Khách hàng mới trong tháng
- Khách mua nhiều nhất
- Tần suất đặt hàng

---


## **NHÓM 20: THANH TOÁN**

### ✅ Thanh toán Momo
- Quét mã QR hoặc liên kết ví Momo
- Thanh toán nhanh chóng

### ✅ Thanh toán ZaloPay
- Thanh toán qua ví ZaloPay

### ✅ Thanh toán COD
- Trả tiền mặt khi nhận hàng

---

## 🎨 GẬY DIỆN ỨNG DỤNG

### **Màn hình chính (Trang chủ)**
- Slideshow món phổ biến
- Danh sách món gợi ý
- Ô tìm kiếm
- Danh mục món ăn
- Menu điều hướng

### **Màu sắc chủ đạo**
- Màu chính: Đỏ/Cam (kích thích thèm ăn)
- Màu phụ: Trắng, Xám nhạt
- Màu nổi bật: Xanh lá (nút hành động)

### **Biểu tượng (Icons)**
- Rõ ràng, dễ hiểu
- Phong cách hiện đại, tối giản

---

## 📊 LỢI ÍCH DỰ ÁN

### **Dành cho Khách hàng:**
✅ Đặt món dễ dàng, tiện lợi  
✅ Xem thực đơn đầy đủ với hình ảnh đẹp  
✅ Nhận gợi ý món kèm phù hợp  
✅ Theo dõi đơn hàng realtime  
✅ Nhiều phương thức thanh toán  

### **Dành cho Quán ăn (Admin):**
✅ Quản lý món ăn dễ dàng  
✅ Theo dõi đơn hàng tập trung  
✅ Thống kê doanh thu chi tiết  
✅ Tăng doanh thu nhờ AI gợi ý  
✅ Nhận phản hồi từ khách hàng  

---

## 📱 THIẾT BỊ HỖ TRỢ

- **Hệ điều hành:** Android 6.0 trở lên
- **Kết nối:** Cần kết nối Internet (3G/4G/5G/Wifi)
- **Dung lượng:** Khoảng 20-30 MB

---

## 🚀 KẾ HOẠCH TRIỂN KHAI

### **Giai đoạn 1: Cơ bản (2 tháng)**
- Đăng nhập/Đăng ký
- Xem món ăn, Tìm kiếm
- Giỏ hàng, Đặt hàng
- Quản lý món ăn (Admin)

### **Giai đoạn 2: Nâng cao (1.5 tháng)**
- Quản lý đơn hàng (Admin)
- Lịch sử đơn hàng
- Phản hồi
- Thanh toán

### **Giai đoạn 3: Tính năng AI (1.5 tháng)**
- AI gợi ý thông minh
- Thông báo đẩy
- Theo dõi doanh thu
- Thống kê

### **Giai đoạn 4: Hoàn thiện (1 tháng)**
- Chat hỗ trợ
- Tích điểm thưởng
- Giới thiệu bạn bè
- Tối ưu hóa

**Tổng thời gian:** Khoảng 6 tháng

---

## 💰 CHI PHÍ DỰ KIẾN

### **Miễn phí (Sử dụng Firebase)**
- Database: Miễn phí đến 1GB
- Storage: Miễn phí đến 5GB
- Authentication: Miễn phí không giới hạn
- Phù hợp: Quán nhỏ, vừa (< 1000 đơn/tháng)

### **Trả phí (Nếu mở rộng)**
- Khi có nhiều khách hàng (> 1000 đơn/tháng)
- Chi phí: Khoảng $25-100/tháng tùy lượng sử dụng

---

## 📞 LIÊN HỆ HỖ TRỢ

**Khi cần hỗ trợ kỹ thuật hoặc có thắc mắc, vui lòng liên hệ:**
- Email: support@foodorderapp.com
- Hotline: 0123-456-789
- Thời gian: 8:00 - 22:00 (tất cả các ngày)

---

**Ngày cập nhật:** 01/02/2026  
**Tài liệu dành cho:** Người dùng không kỹ thuật  
**Phiên bản kỹ thuật:** Xem file "Food_Order_App_Specification.md"
