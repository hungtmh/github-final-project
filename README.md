FOOD ORDER APP - TECHNICAL SPECIFICATION DOCUMENT
📋 THÔNG TIN DỰ ÁN
Tên dự án: Food Order Application
Ngôn ngữ lập trình: Java (Android)
Ngày tạo: 01/02/2026
Phiên bản: 1.0.0

🎯 TỔNG QUAN DỰ ÁN
Ứng dụng đặt món ăn trực tuyến cho phép người dùng duyệt menu, đặt hàng và thanh toán. Ứng dụng hỗ trợ 2 vai trò chính: Admin (quản trị viên) và User (người dùng). Điểm nổi bật là tính năng AI Smart Upsell giúp tăng doanh thu thông qua gợi ý món ăn thông minh.

👥 VAI TRÒ NGƯỜI DÙNG
1. Admin (Quản trị viên)
Quản lý toàn bộ hệ thống
Quản lý món ăn, đơn hàng, feedback
Theo dõi doanh thu và thống kê
2. User (Người dùng)
Đặt món ăn
Quản lý giỏ hàng
Xem lịch sử đơn hàng
Gửi feedback
🔥 DANH SÁCH CÁC NHÓM TÍNH NĂNG
NHÓM 1: QUẢN LÝ TÀI KHOẢN & XÁC THỰC (Authentication & Account Management)
1.1. Đăng nhập (Login)
Mô tả: Cho phép người dùng đăng nhập vào hệ thống với email/số điện thoại và mật khẩu.

Chức năng chi tiết:

Nhập email/số điện thoại
Nhập mật khẩu (có nút hiện/ẩn mật khẩu)
Checkbox "Ghi nhớ đăng nhập"
Nút "Đăng nhập"
Link "Quên mật khẩu?"
Link "Đăng ký tài khoản mới"
Phân quyền tự động (Admin/User) sau khi đăng nhập thành công
Firebase Implementation:

// Firebase Authentication - Đăng nhập
FirebaseAuth mAuth = FirebaseAuth.getInstance();

mAuth.signInWithEmailAndPassword(email, password)
    .addOnCompleteListener(task -> {
        if (task.isSuccessful()) {
            FirebaseUser firebaseUser = mAuth.getCurrentUser();
            
            // Lấy thông tin user từ Firestore
            FirebaseFirestore.getInstance()
                .collection("users")
                .document(firebaseUser.getUid())
                .get()
                .addOnSuccessListener(document -> {
                    String name = document.getString("name");
                    String role = document.getString("role"); // "ADMIN" or "USER"
                    String avatar = document.getString("avatar");
                    
                    // Lưu thông tin local
                    SharedPrefManager.getInstance(context).saveUser(firebaseUser.getUid(), name, email, role);
                    
                    // Chuyển màn hình
                    if (role.equals("ADMIN")) {
                        startActivity(new Intent(context, AdminDashboardActivity.class));
                    } else {
                        startActivity(new Intent(context, HomeActivity.class));
                    }
                });
        } else {
            Toast.makeText(context, "Đăng nhập thất bại: " + task.getException().getMessage(), 
                Toast.LENGTH_SHORT).show();
        }
    });
Firestore Database Structure:

users/
  ├── {userId}/
  │     ├── name: "Nguyễn Văn A"
  │     ├── email: "user@example.com"
  │     ├── phone: "0123456789"
  │     ├── role: "USER" hoặc "ADMIN"
  │     ├── avatar: "url"
  │     ├── address: "123 ABC Street"
  │     └── createdAt: timestamp
Validation:

Email phải đúng định dạng
Mật khẩu không được để trống
Hiển thị thông báo lỗi nếu sai thông tin
1.2. Đăng ký (Register)
Mô tả: Cho phép người dùng mới tạo tài khoản.

Chức năng chi tiết:

Nhập họ tên
Nhập email
Nhập số điện thoại
Nhập mật khẩu
Nhập lại mật khẩu (confirm password)
Checkbox "Tôi đồng ý với điều khoản sử dụng"
Nút "Đăng ký"
Firebase Implementation:

// Firebase Authentication - Đăng ký
FirebaseAuth mAuth = FirebaseAuth.getInstance();
FirebaseFirestore db = FirebaseFirestore.getInstance();

// 1. Tạo tài khoản Authentication
mAuth.createUserWithEmailAndPassword(email, password)
    .addOnCompleteListener(task -> {
        if (task.isSuccessful()) {
            FirebaseUser firebaseUser = mAuth.getCurrentUser();
            String userId = firebaseUser.getUid();
            
            // 2. Lưu thông tin user vào Firestore
            Map<String, Object> user = new HashMap<>();
            user.put("name", name);
            user.put("email", email);
            user.put("phone", phone);
            user.put("role", "USER"); // Mặc định là USER
            user.put("avatar", "");
            user.put("address", "");
            user.put("createdAt", FieldValue.serverTimestamp());
            
            db.collection("users")
                .document(userId)
                .set(user)
                .addOnSuccessListener(aVoid -> {
                    Toast.makeText(context, "Đăng ký thành công!", Toast.LENGTH_SHORT).show();
                    // Chuyển về màn hình đăng nhập
                    finish();
                })
                .addOnFailureListener(e -> {
                    Toast.makeText(context, "Lỗi: " + e.getMessage(), Toast.LENGTH_SHORT).show();
                });
        } else {
            Toast.makeText(context, "Đăng ký thất bại: " + task.getException().getMessage(),
                Toast.LENGTH_SHORT).show();
        }
    });
Validation:

Email chưa tồn tại trong hệ thống
Số điện thoại đúng định dạng (10-11 số)
Mật khẩu tối thiểu 6 ký tự
Mật khẩu và xác nhận mật khẩu phải trùng khớp
1.3. Quên mật khẩu (Forgot Password)
Mô tả: Cho phép người dùng khôi phục mật khẩu qua email.

Chức năng chi tiết:

Nhập email đã đăng ký
Gửi mã OTP về email
Nhập mã OTP để xác thực
Nhập mật khẩu mới
Nhập lại mật khẩu mới
Firebase Implementation:

// Firebase Authentication - Gửi email reset mật khẩu
FirebaseAuth mAuth = FirebaseAuth.getInstance();

mAuth.sendPasswordResetEmail(email)
    .addOnCompleteListener(task -> {
        if (task.isSuccessful()) {
            Toast.makeText(context, 
                "Email khôi phục mật khẩu đã được gửi. Vui lòng kiểm tra email!",
                Toast.LENGTH_LONG).show();
            finish(); // Quay về màn hình đăng nhập
        } else {
            Toast.makeText(context, 
                "Lỗi: " + task.getException().getMessage(),
                Toast.LENGTH_SHORT).show();
        }
    });

// Lưu ý: Firebase tự động gửi email với link reset password
// User click vào link → nhập password mới → Firebase tự động cập nhật
1.4. Đổi mật khẩu (Change Password)
Mô tả: Cho phép người dùng đã đăng nhập đổi mật khẩu.

Chức năng chi tiết:

Nhập mật khẩu hiện tại
Nhập mật khẩu mới
Nhập lại mật khẩu mới
Nút "Cập nhật"
Firebase Implementation:

// Firebase Authentication - Đổi mật khẩu
FirebaseUser user = FirebaseAuth.getInstance().getCurrentUser();

// 1. Xác thực lại với mật khẩu hiện tại (bắt buộc)
AuthCredential credential = EmailAuthProvider.getCredential(user.getEmail(), currentPassword);

user.reauthenticate(credential)
    .addOnCompleteListener(task -> {
        if (task.isSuccessful()) {
            // 2. Cập nhật mật khẩu mới
            user.updatePassword(newPassword)
                .addOnCompleteListener(updateTask -> {
                    if (updateTask.isSuccessful()) {
                        Toast.makeText(context, "Đổi mật khẩu thành công!", Toast.LENGTH_SHORT).show();
                        finish();
                    } else {
                        Toast.makeText(context, "Lỗi: " + updateTask.getException().getMessage(),
                            Toast.LENGTH_SHORT).show();
                    }
                });
        } else {
            Toast.makeText(context, "Mật khẩu hiện tại không đúng!", Toast.LENGTH_SHORT).show();
        }
    });
1.5. Hiển thị User Profile
Mô tả: Hiển thị và chỉnh sửa thông tin cá nhân.

Chức năng chi tiết:

Hiển thị avatar (có thể chụp ảnh hoặc chọn từ thư viện)
Hiển thị/Chỉnh sửa họ tên
Hiển thị email (không cho phép sửa)
Hiển thị/Chỉnh sửa số điện thoại
Hiển thị/Chỉnh sửa địa chỉ
Nút "Lưu thay đổi"
Firebase Implementation:

// Lấy thông tin profile
FirebaseUser currentUser = FirebaseAuth.getInstance().getCurrentUser();
FirebaseFirestore db = FirebaseFirestore.getInstance();

db.collection("users")
    .document(currentUser.getUid())
    .get()
    .addOnSuccessListener(document -> {
        String name = document.getString("name");
        String phone = document.getString("phone");
        String address = document.getString("address");
        String avatar = document.getString("avatar");
        
        // Hiển thị lên UI
        tvName.setText(name);
        tvEmail.setText(currentUser.getEmail());
        tvPhone.setText(phone);
        tvAddress.setText(address);
        Glide.with(context).load(avatar).into(imgAvatar);
    });

// Cập nhật profile
Map<String, Object> updates = new HashMap<>();
updates.put("name", newName);
updates.put("phone", newPhone);
updates.put("address", newAddress);

// Upload avatar nếu có thay đổi
if (avatarUri != null) {
    StorageReference storageRef = FirebaseStorage.getInstance()
        .getReference("avatars/" + currentUser.getUid() + ".jpg");
    
    storageRef.putFile(avatarUri)
        .addOnSuccessListener(taskSnapshot -> {
            storageRef.getDownloadUrl().addOnSuccessListener(uri -> {
                updates.put("avatar", uri.toString());
                
                // Cập nhật Firestore
                db.collection("users").document(currentUser.getUid())
                    .update(updates)
                    .addOnSuccessListener(aVoid -> {
                        Toast.makeText(context, "Cập nhật thành công!", Toast.LENGTH_SHORT).show();
                    });
            });
        });
} else {
    // Cập nhật Firestore (không có avatar mới)
    db.collection("users").document(currentUser.getUid())
        .update(updates)
        .addOnSuccessListener(aVoid -> {
            Toast.makeText(context, "Cập nhật thành công!", Toast.LENGTH_SHORT).show();
        });
}
1.6. Đăng xuất (Logout)
Mô tả: Thoát khỏi tài khoản hiện tại.

Chức năng chi tiết:

Xóa token đã lưu
Xóa thông tin người dùng trong SharedPreferences
Chuyển về màn hình đăng nhập
Firebase Implementation:

// Đăng xuất
FirebaseAuth.getInstance().signOut();

// Xóa thông tin local
SharedPrefManager.getInstance(context).clear();

// Chuyển về màn hình đăng nhập
Intent intent = new Intent(context, LoginActivity.class);
intent.setFlags(Intent.FLAG_ACTIVITY_NEW_TASK | Intent.FLAG_ACTIVITY_CLEAR_TASK);
startActivity(intent);
finish();
NHÓM 2: QUẢN LÝ MÓN ĂN (Food Management) - ADMIN
2.1. Hiển thị danh sách món ăn (Admin)
Mô tả: Admin xem toàn bộ món ăn trong hệ thống.

Chức năng chi tiết:

Hiển thị danh sách dạng RecyclerView
Mỗi item hiển thị: hình ảnh, tên món, giá, trạng thái (available/unavailable)
Nút "Thêm món mới" (FAB)
Nút "Sửa" và "Xóa" trên mỗi item
Firebase Implementation:

// Lấy danh sách tất cả món ăn - REALTIME
FirebaseFirestore db = FirebaseFirestore.getInstance();

db.collection("foods")
    .orderBy("createdAt", Query.Direction.DESCENDING)
    .addSnapshotListener((value, error) -> {
        if (error != null) {
            Toast.makeText(context, "Lỗi: " + error.getMessage(), Toast.LENGTH_SHORT).show();
            return;
        }
        
        List<Food> foodList = new ArrayList<>();
        for (QueryDocumentSnapshot doc : value) {
            Food food = doc.toObject(Food.class);
            food.setId(doc.getId());
            foodList.add(food);
        }
        
        // Cập nhật RecyclerView
        adapter.setFoodList(foodList);
        adapter.notifyDataSetChanged();
    });

// Food Model
public class Food {
    private String id;
    private String name;
    private double price;
    private String image;
    private String category;
    private boolean isAvailable;
    private String description;
    private List<String> images;
    private boolean isPopular;
    private boolean isSuggested;
    private Timestamp createdAt;
    
    // Getters & Setters
}
Firestore Database Structure:

foods/
  ├── {foodId}/
  │     ├── name: "Gà rán"
  │     ├── price: 45000
  │     ├── image: "url_image_main"
  │     ├── category: "MAIN_DISH" | "DRINK" | "DESSERT" | "APPETIZER"
  │     ├── description: "Gà rán giòn tan..."
  │     ├── images: ["url1", "url2", "url3"]
  │     ├── isAvailable: true
  │     ├── isPopular: true
  │     ├── isSuggested: false
  │     ├── rating: 4.5
  │     ├── totalReviews: 120
  │     └── createdAt: timestamp
2.2. Thêm món ăn mới (Admin)
Mô tả: Admin thêm món ăn mới vào hệ thống.

Chức năng chi tiết:

Upload hình ảnh món ăn (từ camera hoặc gallery)
Nhập tên món
Nhập giá
Chọn danh mục (Category): Main Dish, Drink, Dessert, Appetizer
Nhập mô tả
Thêm nhiều hình ảnh khác (gallery)
Checkbox "Món phổ biến"
Checkbox "Món gợi ý"
Nút "Thêm món"
Firebase Implementation:

// 1. Upload hình ảnh lên Firebase Storage
StorageReference storageRef = FirebaseStorage.getInstance()
    .getReference("food_images/" + System.currentTimeMillis() + ".jpg");

storageRef.putFile(imageUri)
    .addOnSuccessListener(taskSnapshot -> {
        storageRef.getDownloadUrl().addOnSuccessListener(uri -> {
            String imageUrl = uri.toString();
            
            // 2. Lưu thông tin món ăn vào Firestore
            Map<String, Object> food = new HashMap<>();
            food.put("name", foodName);
            food.put("price", price);
            food.put("image", imageUrl);
            food.put("category", category);
            food.put("description", description);
            food.put("images", imageUrlsList); // List các ảnh phụ
            food.put("isAvailable", true);
            food.put("isPopular", isPopular);
            food.put("isSuggested", isSuggested);
            food.put("rating", 0.0);
            food.put("totalReviews", 0);
            food.put("createdAt", FieldValue.serverTimestamp());
            
            FirebaseFirestore.getInstance()
                .collection("foods")
                .add(food)
                .addOnSuccessListener(documentReference -> {
                    Toast.makeText(context, "Thêm món thành công!", Toast.LENGTH_SHORT).show();
                    finish();
                })
                .addOnFailureListener(e -> {
                    Toast.makeText(context, "Lỗi: " + e.getMessage(), Toast.LENGTH_SHORT).show();
                });
        });
    })
    .addOnProgressListener(snapshot -> {
        // Hiển thị progress khi upload
        double progress = (100.0 * snapshot.getBytesTransferred()) / snapshot.getTotalByteCount();
        progressBar.setProgress((int) progress);
    });
2.3. Chỉnh sửa món ăn (Admin)
Mô tả: Admin cập nhật thông tin món ăn.

Chức năng chi tiết:

Load thông tin món ăn hiện tại
Cho phép sửa tất cả các trường thông tin
Nút "Cập nhật"
API Endpoint:

PUT /api/admin/foods/{foodId}
Request Body: {
    // Same as add food
}
2.4. Xóa món ăn (Admin)
Mô tả: Admin xóa món ăn khỏi hệ thống.

Chức năng chi tiết:

Hiển thị dialog xác nhận "Bạn có chắc chắn muốn xóa món này?"
Nút "Hủy" và "Xóa"
Firebase Implementation:

// Xóa món ăn
FirebaseFirestore.getInstance()
    .collection("foods")
    .document(foodId)
    .delete()
    .addOnSuccessListener(aVoid -> {
        Toast.makeText(context, "Đã xóa món ăn!", Toast.LENGTH_SHORT).show();
        
        // Xóa ảnh trong Storage (optional)
        if (food.getImage() != null && !food.getImage().isEmpty()) {
            StorageReference photoRef = FirebaseStorage.getInstance()
                .getReferenceFromUrl(food.getImage());
            photoRef.delete();
        }
    })
    .addOnFailureListener(e -> {
        Toast.makeText(context, "Lỗi: " + e.getMessage(), Toast.LENGTH_SHORT).show();
    });
2.5. Tìm kiếm món ăn theo tên (Admin)
Mô tả: Admin tìm kiếm món ăn trong hệ thống.

Chức năng chi tiết:

SearchView ở toolbar
Tìm kiếm realtime khi gõ
Hiển thị kết quả dạng danh sách
API Endpoint:

GET /api/admin/foods/search?keyword={keyword}
NHÓM 3: QUẢN LÝ FEEDBACK (Feedback Management) - ADMIN
3.1. Hiển thị danh sách feedback
Mô tả: Admin xem tất cả feedback từ người dùng.

Chức năng chi tiết:

Hiển thị danh sách feedback dạng RecyclerView
Mỗi item hiển thị:
Tên người gửi
Nội dung feedback
Đánh giá sao (1-5 sao)
Thời gian gửi
Trạng thái: Mới/Đã đọc
Filter theo trạng thái
Nút "Xóa" trên mỗi item
API Endpoint:

GET /api/admin/feedbacks
Response: {
    "feedbacks": [
        {
            "id": "string",
            "userId": "string",
            "userName": "string",
            "userAvatar": "string",
            "content": "string",
            "rating": "number",
            "createdAt": "timestamp",
            "status": "NEW|READ"
        }
    ]
}
3.2. Xem chi tiết feedback
Mô tả: Admin xem chi tiết feedback và đánh dấu đã đọc.

Chức năng chi tiết:

Hiển thị đầy đủ thông tin feedback
Tự động cập nhật trạng thái thành "Đã đọc"
API Endpoint:

GET /api/admin/feedbacks/{feedbackId}
PUT /api/admin/feedbacks/{feedbackId}/mark-read
3.3. Xóa feedback
Mô tả: Admin xóa feedback không phù hợp.

Chức năng chi tiết:

Dialog xác nhận
Xóa khỏi hệ thống
API Endpoint:

DELETE /api/admin/feedbacks/{feedbackId}
NHÓM 4: QUẢN LÝ ĐƠN HÀNG (Order Management) - ADMIN
4.1. Hiển thị danh sách tất cả đơn hàng
Mô tả: Admin xem tất cả đơn hàng trong hệ thống.

Chức năng chi tiết:

Hiển thị danh sách đơn hàng
Mỗi item hiển thị:
Mã đơn hàng
Tên khách hàng
Số lượng món
Tổng tiền
Trạng thái: Chờ xác nhận/Đang xử lý/Hoàn thành/Đã hủy
Thời gian đặt
Filter theo trạng thái
Filter theo ngày
Search theo mã đơn hàng hoặc tên khách
API Endpoint:

GET /api/admin/orders?status={status}&date={date}&keyword={keyword}
Response: {
    "orders": [
        {
            "id": "string",
            "orderCode": "string",
            "userId": "string",
            "userName": "string",
            "totalItems": "number",
            "totalPrice": "number",
            "status": "PENDING|PROCESSING|COMPLETED|CANCELLED",
            "createdAt": "timestamp"
        }
    ]
}
4.2. Xem chi tiết đơn hàng
Mô tả: Admin xem chi tiết từng đơn hàng.

Chức năng chi tiết:

Thông tin khách hàng (tên, SĐT, địa chỉ)
Danh sách món ăn (tên, số lượng, giá)
Tổng tiền
Thời gian đặt
Ghi chú của khách hàng
Trạng thái hiện tại
API Endpoint:

GET /api/admin/orders/{orderId}
Response: {
    "id": "string",
    "orderCode": "string",
    "customer": {
        "name": "string",
        "phone": "string",
        "address": "string"
    },
    "items": [
        {
            "foodId": "string",
            "foodName": "string",
            "quantity": "number",
            "price": "number",
            "image": "string"
        }
    ],
    "totalPrice": "number",
    "note": "string",
    "status": "string",
    "createdAt": "timestamp"
}
4.3. Cập nhật trạng thái đơn hàng
Mô tả: Admin cập nhật trạng thái xử lý đơn hàng.

Chức năng chi tiết:

Dropdown chọn trạng thái:
Chờ xác nhận → Đang xử lý → Hoàn thành
Hoặc Hủy đơn hàng
Nhập lý do (nếu hủy)
Nút "Cập nhật"
Gửi thông báo cho khách hàng khi thay đổi trạng thái
API Endpoint:

PUT /api/admin/orders/{orderId}/status
Request Body: {
    "status": "PENDING|PROCESSING|COMPLETED|CANCELLED",
    "reason": "string"
}
4.4. Thống kê đơn hàng
Mô tả: Admin xem thống kê tổng quan đơn hàng.

Chức năng chi tiết:

Tổng số đơn hàng
Số đơn chờ xác nhận
Số đơn đang xử lý
Số đơn hoàn thành
Số đơn bị hủy
Biểu đồ theo thời gian
API Endpoint:

GET /api/admin/orders/statistics?startDate={date}&endDate={date}
NHÓM 5: THEO DÕI DOANH THU (Revenue Tracking) - ADMIN
5.1. Xem doanh thu theo ngày
Mô tả: Admin theo dõi doanh thu theo từng ngày cụ thể.

Chức năng chi tiết:

Chọn ngày cần xem (DatePicker)
Hiển thị:
Tổng doanh thu trong ngày
Số đơn hàng hoàn thành
Số đơn hàng bị hủy
Món ăn bán chạy nhất
Chi tiết từng đơn hàng trong ngày
API Endpoint:

GET /api/admin/revenue/daily?date={date}
Response: {
    "date": "string",
    "totalRevenue": "number",
    "completedOrders": "number",
    "cancelledOrders": "number",
    "topSellingFood": {
        "name": "string",
        "quantity": "number"
    },
    "orders": []
}
5.2. Xem doanh thu theo tháng
Mô tả: Admin theo dõi doanh thu theo tháng.

Chức năng chi tiết:

Chọn tháng/năm
Biểu đồ cột theo từng ngày trong tháng
Tổng doanh thu tháng
So sánh với tháng trước
API Endpoint:

GET /api/admin/revenue/monthly?month={month}&year={year}
5.3. Xem doanh thu theo khoảng thời gian
Mô tả: Admin tùy chọn khoảng thời gian để xem doanh thu.

Chức năng chi tiết:

Chọn ngày bắt đầu
Chọn ngày kết thúc
Hiển thị biểu đồ đường
Export báo cáo PDF
API Endpoint:

GET /api/admin/revenue/range?startDate={date}&endDate={date}
5.4. Top món ăn bán chạy
Mô tả: Admin xem danh sách món ăn bán chạy nhất.

Chức năng chi tiết:

Hiển thị top 10 món ăn
Số lượng đã bán
Doanh thu từng món
Filter theo thời gian
API Endpoint:

GET /api/admin/revenue/top-selling?startDate={date}&endDate={date}&limit=10
NHÓM 6: HIỂN THỊ MÓN ĂN PHỔ BIẾN (Popular Foods Display) - USER
6.1. Slide hình ảnh món ăn phổ biến
Mô tả: Hiển thị slider món ăn phổ biến ở màn hình Home với auto-run.

Chức năng chi tiết:

ViewPager2 hoặc RecyclerView với SnapHelper
Auto scroll mỗi 3 giây
Hiển thị indicator (dots)
Khi click vào ảnh → mở chi tiết món ăn
Load realtime từ server
API Endpoint:

GET /api/foods/popular
Response: {
    "foods": [
        {
            "id": "string",
            "name": "string",
            "image": "string",
            "price": "number",
            "rating": "number"
        }
    ]
}
Implementation:

// ViewPager2 with auto scroll
ViewPager2 viewPager = findViewById(R.id.viewPagerPopular);
Handler handler = new Handler();
Runnable runnable = new Runnable() {
    @Override
    public void run() {
        int currentItem = viewPager.getCurrentItem();
        int totalItems = adapter.getItemCount();
        viewPager.setCurrentItem((currentItem + 1) % totalItems);
        handler.postDelayed(this, 3000); // 3 seconds
    }
};
handler.postDelayed(runnable, 3000);
NHÓM 7: GỢI Ý MÓN ĂN (Suggested Foods) - USER
7.1. Hiển thị danh sách món ăn gợi ý
Mô tả: Hiển thị món ăn được gợi ý cho người dùng ở màn hình Home.

Chức năng chi tiết:

Hiển thị dạng Grid (2 cột) hoặc List
Load realtime từ server
Hiển thị: hình ảnh, tên món, giá, rating
Icon "Thêm vào giỏ hàng"
Click vào món → xem chi tiết
API Endpoint:

GET /api/foods/suggested
Response: {
    "foods": [
        {
            "id": "string",
            "name": "string",
            "image": "string",
            "price": "number",
            "rating": "number",
            "description": "string"
        }
    ]
}
NHÓM 8: TÌM KIẾM MÓN ĂN (Food Search) - USER
8.1. Tìm kiếm món ăn theo tên
Mô tả: Người dùng tìm kiếm món ăn ở màn hình Home.

Chức năng chi tiết:

SearchView trong Toolbar hoặc riêng một màn hình
Tìm kiếm realtime khi gõ (debounce 500ms)
Hiển thị kết quả dạng danh sách
Highlight từ khóa tìm kiếm
Lưu lịch sử tìm kiếm (local)
Gợi ý từ khóa phổ biến
API Endpoint:

GET /api/foods/search?keyword={keyword}
Response: {
    "foods": [],
    "suggestions": ["string"]
}
Implementation:

searchView.setOnQueryTextListener(new SearchView.OnQueryTextListener() {
    private Handler handler = new Handler();
    private Runnable searchRunnable;
    
    @Override
    public boolean onQueryTextChange(String newText) {
        if (searchRunnable != null) {
            handler.removeCallbacks(searchRunnable);
        }
        searchRunnable = () -> performSearch(newText);
        handler.postDelayed(searchRunnable, 500); // Debounce 500ms
        return true;
    }
});
8.2. Lọc món ăn theo danh mục
Mô tả: Người dùng lọc món ăn theo category.

Chức năng chi tiết:

Chip Group hiển thị các danh mục: Tất cả, Main Dish, Drink, Dessert, Appetizer
Click vào chip → filter danh sách món ăn
Kết hợp được với tìm kiếm
API Endpoint:

GET /api/foods?category={category}
8.3. Sắp xếp món ăn
Mô tả: Người dùng sắp xếp món ăn theo tiêu chí.

Chức năng chi tiết:

Sắp xếp theo:
Giá: Thấp → Cao
Giá: Cao → Thấp
Đánh giá cao nhất
Mới nhất
Dropdown hoặc Bottom Sheet chọn tiêu chí
API Endpoint:

GET /api/foods?sortBy={price_asc|price_desc|rating_desc|newest}
NHÓM 9: CHI TIẾT MÓN ĂN (Food Details) - USER
9.1. Hiển thị thông tin chi tiết món ăn
Mô tả: Hiển thị đầy đủ thông tin món ăn khi người dùng click vào.

Chức năng chi tiết:

Hình ảnh lớn (swipe để xem nhiều ảnh)
Tên món ăn
Giá
Rating (sao) và số lượt đánh giá
Mô tả chi tiết
Danh mục
Nút "Thêm vào giỏ hàng"
Số lượng selector (+/-)
API Endpoint:

GET /api/foods/{foodId}
Response: {
    "id": "string",
    "name": "string",
    "price": "number",
    "images": ["string"],
    "rating": "number",
    "totalReviews": "number",
    "description": "string",
    "category": "string",
    "ingredients": ["string"],
    "isAvailable": "boolean"
}
9.2. Gallery hình ảnh món ăn
Mô tả: Xem nhiều hình ảnh món ăn.

Chức năng chi tiết:

ViewPager2 để swipe qua lại
Indicator hiển thị vị trí ảnh
Pinch to zoom
Click vào ảnh → fullscreen
9.3. Đánh giá & Reviews món ăn
Mô tả: Hiển thị đánh giá từ người dùng khác.

Chức năng chi tiết:

Tổng rating trung bình
Số lượng đánh giá
Danh sách review (tên, avatar, số sao, nội dung, thời gian)
Người dùng có thể thêm review của mình
API Endpoint:

GET /api/foods/{foodId}/reviews
POST /api/foods/{foodId}/reviews
Request Body: {
    "rating": "number",
    "comment": "string"
}
NHÓM 10: GIỎ HÀNG (Shopping Cart) - USER
10.1. Thêm món ăn vào giỏ hàng
Mô tả: Người dùng thêm món ăn vào giỏ hàng với số lượng tùy chọn.

Chức năng chi tiết:

Nút "Thêm vào giỏ hàng" ở chi tiết món ăn
Chọn số lượng (+/- buttons hoặc NumberPicker)
Animation khi thêm vào giỏ
Cập nhật badge số lượng món trong giỏ
Lưu giỏ hàng local (SQLite hoặc Room Database)
Database Schema:

@Entity(tableName = "cart_items")
public class CartItem {
    @PrimaryKey
    private String foodId;
    private String foodName;
    private String foodImage;
    private double price;
    private int quantity;
    private long addedAt;
}
DAO:

@Dao
public interface CartDao {
    @Insert(onConflict = OnConflictStrategy.REPLACE)
    void addToCart(CartItem item);
    
    @Query("SELECT * FROM cart_items")
    LiveData<List<CartItem>> getAllCartItems();
    
    @Update
    void updateCartItem(CartItem item);
    
    @Delete
    void removeFromCart(CartItem item);
    
    @Query("DELETE FROM cart_items")
    void clearCart();
    
    @Query("SELECT SUM(quantity) FROM cart_items")
    LiveData<Integer> getTotalItems();
}
10.2. Hiển thị danh sách món trong giỏ hàng
Mô tả: Hiển thị tất cả món ăn đã thêm vào giỏ hàng.

Chức năng chi tiết:

RecyclerView hiển thị danh sách
Mỗi item hiển thị:
Hình ảnh món
Tên món
Giá
Số lượng (có thể tăng/giảm)
Tổng giá = giá × số lượng
Nút "Xóa"
Hiển thị tổng tiền tất cả món
Nút "Đặt hàng"
Nút "Xóa tất cả"
Layout:

<!-- RecyclerView Item -->
<CardView>
    <ImageView id="foodImage"/>
    <TextView id="foodName"/>
    <TextView id="foodPrice"/>
    <LinearLayout> <!-- Quantity Selector -->
        <Button id="btnMinus" text="-"/>
        <TextView id="tvQuantity"/>
        <Button id="btnPlus" text="+"/>
    </LinearLayout>
    <TextView id="tvTotalPrice"/>
    <ImageButton id="btnDelete"/>
</CardView>
10.3. Cập nhật số lượng món trong giỏ
Mô tả: Người dùng thay đổi số lượng món ăn.

Chức năng chi tiết:

Nút "+" để tăng số lượng
Nút "-" để giảm số lượng
Nếu số lượng = 0 → xóa món khỏi giỏ
Cập nhật realtime tổng tiền
Implementation:

btnPlus.setOnClickListener(v -> {
    int currentQty = cartItem.getQuantity();
    cartItem.setQuantity(currentQty + 1);
    cartViewModel.updateCartItem(cartItem);
});

btnMinus.setOnClickListener(v -> {
    int currentQty = cartItem.getQuantity();
    if (currentQty > 1) {
        cartItem.setQuantity(currentQty - 1);
        cartViewModel.updateCartItem(cartItem);
    } else {
        // Show confirmation dialog before removing
        showDeleteConfirmation(cartItem);
    }
});
10.4. Xóa món khỏi giỏ hàng
Mô tả: Người dùng xóa món không muốn mua.

Chức năng chi tiết:

Icon "Xóa" trên mỗi item
Swipe to delete (ItemTouchHelper)
Dialog xác nhận "Bạn muốn xóa món này?"
Animation khi xóa
Implementation:

ItemTouchHelper.SimpleCallback simpleCallback = new ItemTouchHelper.SimpleCallback(0, ItemTouchHelper.LEFT) {
    @Override
    public void onSwiped(@NonNull RecyclerView.ViewHolder viewHolder, int direction) {
        int position = viewHolder.getAdapterPosition();
        CartItem item = cartItems.get(position);
        
        // Show snackbar with undo option
        Snackbar.make(recyclerView, "Đã xóa " + item.getFoodName(), Snackbar.LENGTH_LONG)
            .setAction("HOÀN TÁC", v -> cartViewModel.addToCart(item))
            .show();
        
        cartViewModel.removeFromCart(item);
    }
};
10.5. Xóa tất cả giỏ hàng
Mô tả: Xóa toàn bộ món trong giỏ hàng.

Chức năng chi tiết:

Nút "Xóa tất cả"
Dialog xác nhận
Clear database
NHÓM 11: ĐẶT HÀNG (Order Placement) - USER
11.1. Tạo đơn hàng mới
Mô tả: Người dùng đặt hàng từ giỏ hàng.

Chức năng chi tiết:

Xác nhận danh sách món (từ giỏ hàng)
Nhập thông tin giao hàng:
Tên người nhận
Số điện thoại
Địa chỉ giao hàng (có thể chọn từ địa chỉ đã lưu)
Chọn phương thức thanh toán:
Tiền mặt (COD)
Chuyển khoản
Ví điện tử (Momo, ZaloPay)
Nhập ghi chú cho đơn hàng
Hiển thị tổng tiền
Nút "Xác nhận đặt hàng"
API Endpoint:

POST /api/orders
Headers: {
    "Authorization": "Bearer {token}"
}
Request Body: {
    "items": [
        {
            "foodId": "string",
            "quantity": "number",
            "price": "number"
        }
    ],
    "deliveryInfo": {
        "name": "string",
        "phone": "string",
        "address": "string"
    },
    "paymentMethod": "COD|BANK_TRANSFER|MOMO|ZALOPAY",
    "note": "string",
    "totalPrice": "number"
}
Response: {
    "success": "boolean",
    "orderId": "string",
    "orderCode": "string",
    "message": "string"
}
11.2. Xác nhận đơn hàng
Mô tả: Hiển thị màn hình xác nhận sau khi đặt hàng thành công.

Chức năng chi tiết:

Icon thành công (checkmark animation)
Mã đơn hàng
Thời gian dự kiến giao hàng
Nút "Xem đơn hàng"
Nút "Về trang chủ"
Xóa giỏ hàng sau khi đặt thành công
11.3. Lưu địa chỉ giao hàng
Mô tả: Người dùng lưu nhiều địa chỉ giao hàng.

Chức năng chi tiết:

Thêm địa chỉ mới
Đặt địa chỉ mặc định
Sửa/Xóa địa chỉ
Chọn địa chỉ khi đặt hàng
API Endpoint:

GET /api/user/addresses
POST /api/user/addresses
PUT /api/user/addresses/{addressId}
DELETE /api/user/addresses/{addressId}
NHÓM 12: LỊCH SỬ ĐƠN HÀNG (Order History) - USER
12.1. Hiển thị danh sách lịch sử đơn hàng
Mô tả: Người dùng xem tất cả đơn hàng đã đặt.

Chức năng chi tiết:

Hiển thị danh sách đơn hàng theo thời gian (mới nhất trước)
Mỗi item hiển thị:
Mã đơn hàng
Ngày đặt
Số lượng món
Tổng tiền
Trạng thái (màu sắc khác nhau):
Màu xám: Đã hoàn thành
Màu trắng: Chưa hoàn thành (Chờ xác nhận, Đang xử lý)
Màu đỏ: Đã hủy
Tab filter: Tất cả, Chờ xác nhận, Đang xử lý, Hoàn thành, Đã hủy
API Endpoint:

GET /api/user/orders?status={status}
Headers: {
    "Authorization": "Bearer {token}"
}
Response: {
    "orders": [
        {
            "id": "string",
            "orderCode": "string",
            "totalItems": "number",
            "totalPrice": "number",
            "status": "PENDING|PROCESSING|COMPLETED|CANCELLED",
            "createdAt": "timestamp"
        }
    ]
}
UI Implementation:

// Color coding for status
switch (order.getStatus()) {
    case "COMPLETED":
        cardView.setCardBackgroundColor(Color.GRAY);
        break;
    case "PENDING":
    case "PROCESSING":
        cardView.setCardBackgroundColor(Color.WHITE);
        break;
    case "CANCELLED":
        cardView.setCardBackgroundColor(Color.RED);
        break;
}
12.2. Xem chi tiết đơn hàng
Mô tả: Người dùng xem chi tiết từng đơn hàng.

Chức năng chi tiết:

Mã đơn hàng
Trạng thái hiện tại
Timeline trạng thái (nếu có):
Đã đặt hàng
Đã xác nhận
Đang xử lý
Hoàn thành
Danh sách món đã đặt
Thông tin giao hàng
Tổng tiền
Ghi chú
Nút "Đặt lại" (order again)
Nút "Hủy đơn" (nếu status = Pending)
API Endpoint:

GET /api/user/orders/{orderId}
12.3. Hủy đơn hàng
Mô tả: Người dùng hủy đơn hàng (chỉ khi trạng thái = Pending).

Chức năng chi tiết:

Dialog chọn lý do hủy:
Đổi ý
Đặt nhầm
Thay đổi địa chỉ
Khác (nhập lý do)
Nút "Xác nhận hủy"
API Endpoint:

PUT /api/user/orders/{orderId}/cancel
Request Body: {
    "reason": "string"
}
12.4. Đặt lại đơn hàng
Mô tả: Người dùng đặt lại đơn hàng đã từng đặt.

Chức năng chi tiết:

Copy danh sách món từ đơn cũ vào giỏ hàng
Chuyển đến màn hình giỏ hàng
Người dùng có thể chỉnh sửa trước khi đặt
NHÓM 13: FEEDBACK & ĐÁNH GIÁ (Feedback & Review) - USER
13.1. Gửi feedback cho quán
Mô tả: Người dùng gửi phản hồi, đánh giá về dịch vụ.

Chức năng chi tiết:

Rating sao (1-5 sao) - RatingBar
Nhập nội dung feedback (EditText multiline)
Upload hình ảnh (tùy chọn)
Nút "Gửi feedback"
Thông báo thành công
API Endpoint:

POST /api/feedbacks
Headers: {
    "Authorization": "Bearer {token}"
}
Request Body: {
    "rating": "number",
    "content": "string",
    "images": ["base64_string"]
}
Layout:

<ScrollView>
    <LinearLayout>
        <TextView text="Đánh giá của bạn"/>
        <RatingBar id="ratingBar" numStars="5"/>
        
        <TextView text="Nội dung phản hồi"/>
        <EditText 
            id="edtContent"
            inputType="textMultiLine"
            lines="5"
            hint="Chia sẻ trải nghiệm của bạn..."/>
        
        <TextView text="Hình ảnh (tùy chọn)"/>
        <RecyclerView id="rvImages"/> <!-- Selected images -->
        <Button id="btnAddImage" text="Thêm ảnh"/>
        
        <Button id="btnSubmit" text="Gửi feedback"/>
    </LinearLayout>
</ScrollView>
13.2. Xem feedback đã gửi
Mô tả: Người dùng xem lại các feedback đã gửi.

Chức năng chi tiết:

Danh sách feedback của user
Hiển thị: rating, nội dung, thời gian, trạng thái
Có thể chỉnh sửa hoặc xóa feedback
API Endpoint:

GET /api/user/feedbacks
NHÓM 14: THÔNG TIN LIÊN HỆ (Contact Information) - USER
14.1. Hiển thị thông tin liên hệ quán
Mô tả: Hiển thị các phương thức liên lạc với quán ăn.

Chức năng chi tiết:

Icon + Label cho từng phương thức:
Facebook: Click → mở Facebook Page
Skype: Click → mở Skype chat
Call Phone: Click → mở dialer với số điện thoại
YouTube: Click → mở YouTube Channel
Zalo: Click → mở Zalo chat
Gmail: Click → mở email client
Layout:

<LinearLayout orientation="vertical">
    <TextView text="Liên hệ với chúng tôi" style="bold"/>
    
    <LinearLayout id="btnFacebook">
        <ImageView src="@drawable/ic_facebook"/>
        <TextView text="Facebook"/>
    </LinearLayout>
    
    <LinearLayout id="btnSkype">
        <ImageView src="@drawable/ic_skype"/>
        <TextView text="Skype"/>
    </LinearLayout>
    
    <LinearLayout id="btnPhone">
        <ImageView src="@drawable/ic_phone"/>
        <TextView text="Gọi điện: 0123456789"/>
    </LinearLayout>
    
    <LinearLayout id="btnYoutube">
        <ImageView src="@drawable/ic_youtube"/>
        <TextView text="YouTube"/>
    </LinearLayout>
    
    <LinearLayout id="btnZalo">
        <ImageView src="@drawable/ic_zalo"/>
        <TextView text="Zalo"/>
    </LinearLayout>
    
    <LinearLayout id="btnGmail">
        <ImageView src="@drawable/ic_gmail"/>
        <TextView text="Email: restaurant@gmail.com"/>
    </LinearLayout>
</LinearLayout>
Implementation:

// Facebook
btnFacebook.setOnClickListener(v -> {
    String facebookUrl = "https://www.facebook.com/restaurantpage";
    Intent intent = new Intent(Intent.ACTION_VIEW, Uri.parse(facebookUrl));
    startActivity(intent);
});

// Phone
btnPhone.setOnClickListener(v -> {
    Intent intent = new Intent(Intent.ACTION_DIAL);
    intent.setData(Uri.parse("tel:0123456789"));
    startActivity(intent);
});

// Email
btnGmail.setOnClickListener(v -> {
    Intent intent = new Intent(Intent.ACTION_SENDTO);
    intent.setData(Uri.parse("mailto:restaurant@gmail.com"));
    intent.putExtra(Intent.EXTRA_SUBJECT, "Liên hệ từ ứng dụng");
    startActivity(intent);
});

// Zalo
btnZalo.setOnClickListener(v -> {
    String zaloUrl = "https://zalo.me/zalo_phone_number";
    Intent intent = new Intent(Intent.ACTION_VIEW, Uri.parse(zaloUrl));
    startActivity(intent);
});
14.2. Xem bản đồ vị trí quán
Mô tả: Hiển thị vị trí quán trên Google Maps.

Chức năng chi tiết:

Tích hợp Google Maps
Hiển thị marker vị trí quán
Nút "Chỉ đường" → mở Google Maps navigation
Implementation:

// Google Maps Fragment
SupportMapFragment mapFragment = (SupportMapFragment) getSupportFragmentManager()
    .findFragmentById(R.id.map);
mapFragment.getMapAsync(googleMap -> {
    LatLng restaurantLocation = new LatLng(10.762622, 106.660172); // Example
    googleMap.addMarker(new MarkerOptions()
        .position(restaurantLocation)
        .title("Tên quán ăn"));
    googleMap.moveCamera(CameraUpdateFactory.newLatLngZoom(restaurantLocation, 15));
});

// Direction button
btnDirection.setOnClickListener(v -> {
    String uri = "google.navigation:q=10.762622,106.660172";
    Intent intent = new Intent(Intent.ACTION_VIEW, Uri.parse(uri));
    intent.setPackage("com.google.android.apps.maps");
    startActivity(intent);
});
NHÓM 15: TÍNH NĂNG AI - SMART UPSELL (AI-Powered Smart Upsell)
15.1. Gợi ý món ăn kèm thông minh
Mô tả: Khi khách chọn mua món, App sẽ gợi ý món kèm dựa trên phân tích dữ liệu hoặc rule-based.

Ví dụ:

Khách chọn "Gà rán" → Gợi ý "Khách mua gà thường uống kèm Pepsi"
Đang giờ trưa → "Bạn có muốn thêm canh súp không?"
Cách thực hiện:

Phương án 1: Rule-Based (Đơn giản)
Logic:

public class UpsellService {
    
    public List<Food> getUpsellSuggestions(Food selectedFood, String currentTime) {
        List<Food> suggestions = new ArrayList<>();
        
        // Rule 1: Main Dish → Suggest Drink
        if (selectedFood.getCategory().equals("MAIN_DISH")) {
            suggestions.add(getFoodByName("Pepsi"));
            suggestions.add(getFoodByName("Coca Cola"));
        }
        
        // Rule 2: Lunch time → Suggest Soup
        int hour = Integer.parseInt(currentTime.split(":")[0]);
        if (hour >= 11 && hour <= 13) {
            suggestions.add(getFoodByName("Canh súp"));
        }
        
        // Rule 3: Fried food → Suggest Salad
        if (selectedFood.getName().contains("rán")) {
            suggestions.add(getFoodByName("Salad rau củ"));
        }
        
        // Rule 4: Price-based upsell
        if (selectedFood.getPrice() < 50000) {
            suggestions.add(getFoodByName("Khoai tây chiên")); // Low price combo
        }
        
        return suggestions;
    }
}
Phương án 2: AI-Based (Association Rule Learning)
PHƯƠNG ÁN 1: Logic trong App (Java) - ĐƠN GIẢN NHẤT

public class UpsellService {
    
    public List<Food> getUpsellSuggestions(Food selectedFood, String currentTime) {
        List<Food> suggestions = new ArrayList<>();
        
        // Rule 1: Main Dish → Suggest Drink
        if (selectedFood.getCategory().equals("MAIN_DISH")) {
            // Lấy món drink từ Firestore
            FirebaseFirestore.getInstance()
                .collection("foods")
                .whereEqualTo("category", "DRINK")
                .whereEqualTo("isSuggested", true)
                .limit(2)
                .get()
                .addOnSuccessListener(querySnapshot -> {
                    for (DocumentSnapshot doc : querySnapshot.getDocuments()) {
                        suggestions.add(doc.toObject(Food.class));
                    }
                    showUpsellDialog(suggestions);
                });
        }
        
        // Rule 2: Lunch time → Suggest Soup
        int hour = Calendar.getInstance().get(Calendar.HOUR_OF_DAY);
        if (hour >= 11 && hour <= 13) {
            FirebaseFirestore.getInstance()
                .collection("foods")
                .whereEqualTo("category", "APPETIZER")
                .limit(1)
                .get()
                .addOnSuccessListener(querySnapshot -> {
                    for (DocumentSnapshot doc : querySnapshot.getDocuments()) {
                        suggestions.add(doc.toObject(Food.class));
                    }
                });
        }
        
        return suggestions;
    }
}
PHƯƠNG ÁN 2: Phân tích dữ liệu từ Firestore (Nâng cao)

public class AiUpsellService {
    
    // Phân tích tất cả đơn hàng đã hoàn thành
    public void analyzeOrdersAndGetSuggestions(String selectedFoodId, UpsellCallback callback) {
        FirebaseFirestore db = FirebaseFirestore.getInstance();
        
        // Lấy tất cả đơn hàng đã hoàn thành
        db.collection("orders")
            .whereEqualTo("status", "COMPLETED")
            .get()
            .addOnSuccessListener(querySnapshot -> {
                // Map để lưu số lần món A và món B xuất hiện cùng nhau
                Map<String, Map<String, Integer>> associations = new HashMap<>();
                
                for (DocumentSnapshot orderDoc : querySnapshot.getDocuments()) {
                    List<Map<String, Object>> items = (List<Map<String, Object>>) orderDoc.get("items");
                    
                    if (items != null) {
                        // Duyệt qua từng món trong đơn hàng
                        for (int i = 0; i < items.size(); i++) {
                            String foodId1 = (String) items.get(i).get("foodId");
                            
                            if (!associations.containsKey(foodId1)) {
                                associations.put(foodId1, new HashMap<>());
                            }
                            
                            // Tìm món khác trong cùng đơn hàng
                            for (int j = 0; j < items.size(); j++) {
                                if (i != j) {
                                    String foodId2 = (String) items.get(j).get("foodId");
                                    Map<String, Integer> foodAssoc = associations.get(foodId1);
                                    foodAssoc.put(foodId2, foodAssoc.getOrDefault(foodId2, 0) + 1);
                                }
                            }
                        }
                    }
                }
                
                // Lấy top 3 món thường mua kèm với selectedFoodId
                if (associations.containsKey(selectedFoodId)) {
                    Map<String, Integer> relatedFoods = associations.get(selectedFoodId);
                    
                    // Sắp xếp theo số lần xuất hiện
                    List<Map.Entry<String, Integer>> sortedList = new ArrayList<>(relatedFoods.entrySet());
                    sortedList.sort((a, b) -> b.getValue().compareTo(a.getValue()));
                    
                    // Lấy top 3
                    List<String> topFoodIds = new ArrayList<>();
                    for (int i = 0; i < Math.min(3, sortedList.size()); i++) {
                        topFoodIds.add(sortedList.get(i).getKey());
                    }
                    
                    // Lấy thông tin món ăn từ Firestore
                    List<Food> suggestions = new ArrayList<>();
                    for (String foodId : topFoodIds) {
                        db.collection("foods").document(foodId)
                            .get()
                            .addOnSuccessListener(doc -> {
                                Food food = doc.toObject(Food.class);
                                food.setId(doc.getId());
                                suggestions.add(food);
                                
                                if (suggestions.size() == topFoodIds.size()) {
                                    callback.onSuccess(suggestions);
                                }
                            });
                    }
                } else {
                    // Nếu chưa có dữ liệu, dùng rule-based
                    callback.onSuccess(new ArrayList<>());
                }
            });
    }
    
    public interface UpsellCallback {
        void onSuccess(List<Food> suggestions);
    }
}
PHƯƠNG ÁN 3: Firebase Functions (JavaScript trên server Google)

// Chạy trên server của Google (không phải trên điện thoại)
// File: functions/index.js

const functions = require('firebase-functions');
const admin = require('firebase-admin');
admin.initializeApp();

exports.getUpsellSuggestions = functions.https.onCall(async (data, context) => {
    const foodId = data.foodId;
    
    // Lấy tất cả đơn hàng
    const ordersSnapshot = await admin.firestore()
        .collection('orders')
        .where('status', '==', 'COMPLETED')
        .get();
    
    const associations = {};
    
    ordersSnapshot.forEach(doc => {
        const items = doc.data().items;
        items.forEach((item, index) => {
            if (!associations[item.foodId]) {
                associations[item.foodId] = {};
            }
            items.forEach((otherItem, otherIndex) => {
                if (index !== otherIndex) {
                    if (!associations[item.foodId][otherItem.foodId]) {
                        associations[item.foodId][otherItem.foodId] = 0;
                    }
                    associations[item.foodId][otherItem.foodId]++;
                }
            });
        });
    });
    
    // Lấy top suggestions cho foodId
    const suggestions = [];
    if (associations[foodId]) {
        const sorted = Object.entries(associations[foodId])
            .sort((a, b) => b[1] - a[1])
            .slice(0, 3);
        
        for (const [suggestedFoodId, count] of sorted) {
            const foodDoc = await admin.firestore()
                .collection('foods')
                .doc(suggestedFoodId)
                .get();
            suggestions.push({ id: suggestedFoodId, ...foodDoc.data() });
        }
    }
    
    return { suggestions };
});
Gọi Firebase Functions từ Android:

// Gọi Cloud Function
FirebaseFunctions functions = FirebaseFunctions.getInstance();

Map<String, Object> data = new HashMap<>();
data.put("foodId", selectedFoodId);

functions.getHttpsCallable("getUpsellSuggestions")
    .call(data)
    .addOnSuccessListener(result -> {
        Map<String, Object> response = (Map<String, Object>) result.getData();
        List<Map<String, Object>> suggestions = (List<Map<String, Object>>) response.get("suggestions");
        
        // Hiển thị dialog gợi ý
        showUpsellDialog(suggestions);
    });
Client-side (Java Android) - Gọi API và hiển thị:

public class SmartUpsellManager {
    
    public void showUpsellDialog(Activity activity, Food selectedFood, CartDao cartDao) {
        // Call API to get AI recommendations
        ApiService.getUpsellSuggestions(selectedFood.getId(), new Callback<UpsellResponse>() {
            @Override
            public void onSuccess(UpsellResponse response) {
                if (response.getSuggestions().isEmpty()) {
                    // Fallback to rule-based
                    showRuleBasedUpsell(activity, selectedFood, cartDao);
                } else {
                    showAiUpsellDialog(activity, selectedFood, response.getSuggestions(), cartDao);
                }
            }
            
            @Override
            public void onError(Throwable error) {
                // Fallback to rule-based
                showRuleBasedUpsell(activity, selectedFood, cartDao);
            }
        });
    }
    
    private void showAiUpsellDialog(Activity activity, Food mainFood, 
                                     List<Food> suggestions, CartDao cartDao) {
        // Create BottomSheetDialog or DialogFragment
        BottomSheetDialog dialog = new BottomSheetDialog(activity);
        View view = LayoutInflater.from(activity).inflate(R.layout.dialog_upsell, null);
        
        TextView tvMessage = view.findViewById(R.id.tvMessage);
        RecyclerView rvSuggestions = view.findViewById(R.id.rvSuggestions);
        Button btnSkip = view.findViewById(R.id.btnSkip);
        
        // Set message
        String message = "Khách mua " + mainFood.getName() + " thường kèm theo:";
        tvMessage.setText(message);
        
        // Setup RecyclerView
        UpsellAdapter adapter = new UpsellAdapter(suggestions, food -> {
            // Add to cart
            cartDao.addToCart(new CartItem(food.getId(), food.getName(), 
                food.getImage(), food.getPrice(), 1, System.currentTimeMillis()));
            Toast.makeText(activity, "Đã thêm " + food.getName(), Toast.LENGTH_SHORT).show();
            dialog.dismiss();
        });
        rvSuggestions.setAdapter(adapter);
        
        btnSkip.setOnClickListener(v -> dialog.dismiss());
        
        dialog.setContentView(view);
        dialog.show();
    }
}
Firebase Implementation:

// CÁCH 1: Dùng rule-based đơn giản (Khuyên dùng cho bắt đầu)
UpsellService upsellService = new UpsellService();
List<Food> suggestions = upsellService.getUpsellSuggestions(selectedFood, currentTime);

// CÁCH 2: Dùng AI phân tích đơn hàng (Nâng cao)
AiUpsellService aiService = new AiUpsellService();
aiService.analyzeOrdersAndGetSuggestions(selectedFood.getId(), suggestions -> {
    if (suggestions.isEmpty()) {
        // Fallback về rule-based nếu chưa có dữ liệu
        showRuleBasedUpsell();
    } else {
        showAiUpsellDialog(suggestions);
    }
});

// CÁCH 3: Dùng Firebase Functions (Phức tạp nhất, cần deploy)
// Xem code ở trên
Lưu ý: Với Firebase, bạn có 3 lựa chọn:

Rule-based: Code trong app, đơn giản nhất ✅
AI local: Phân tích trong app, tốn tài nguyên điện thoại
Firebase Functions: Phân tích trên server Google, cần setup thêm
15.2. Hiển thị Upsell Popup
Mô tả: Khi người dùng bấm "Thêm vào giỏ hàng", hiển thị popup gợi ý.

Chức năng chi tiết:

DialogFragment hoặc BottomSheetDialog
Hiển thị 2-3 món gợi ý
Mỗi món hiển thị: ảnh, tên, giá, nút "Thêm (+10k)"
Nút "Bỏ qua"
Animation smooth
Layout:

<!-- dialog_upsell.xml -->
<LinearLayout>
    <TextView 
        id="tvTitle"
        text="Thêm món kèm để được ưu đãi!"
        style="bold"/>
    
    <TextView 
        id="tvMessage"
        text="Khách mua gà thường uống kèm:"/>
    
    <RecyclerView 
        id="rvSuggestions"
        orientation="horizontal"/>
    
    <!-- Each item in RecyclerView -->
    <CardView>
        <ImageView id="foodImage"/>
        <TextView id="foodName"/>
        <TextView id="foodPrice" text="+10.000đ"/>
        <Button id="btnAdd" text="Thêm"/>
    </CardView>
    
    <Button 
        id="btnSkip"
        text="Bỏ qua"
        style="text"/>
</LinearLayout>
15.3. Theo dõi hiệu quả Upsell
Mô tả: Admin xem thống kê hiệu quả của tính năng AI Upsell.

Chức năng chi tiết:

Số lần hiển thị gợi ý
Số lần khách chấp nhận (conversion rate)
Doanh thu tăng thêm từ upsell
Top món được upsell thành công nhất
API Endpoint:

GET /api/admin/ai/upsell-stats?startDate={date}&endDate={date}
Response: {
    "totalShown": "number",
    "totalAccepted": "number",
    "conversionRate": "number",
    "additionalRevenue": "number",
    "topUpsellFoods": [
        {
            "foodName": "string",
            "acceptCount": "number"
        }
    ]
}
NHÓM 16: THÔNG BÁO (Notifications)
16.1. Thông báo đẩy (Push Notifications)
Mô tả: Gửi thông báo cho người dùng về trạng thái đơn hàng, khuyến mãi.

Chức năng chi tiết:

Sử dụng Firebase Cloud Messaging (FCM)
Thông báo khi:
Đơn hàng được xác nhận
Đơn hàng đang xử lý
Đơn hàng hoàn thành
Có khuyến mãi mới
Món ăn yêu thích giảm giá
Implementation:

// FirebaseMessagingService
public class MyFirebaseMessagingService extends FirebaseMessagingService {
    @Override
    public void onMessageReceived(RemoteMessage remoteMessage) {
        String title = remoteMessage.getNotification().getTitle();
        String body = remoteMessage.getNotification().getBody();
        
        showNotification(title, body);
    }
    
    private void showNotification(String title, String message) {
        NotificationCompat.Builder builder = new NotificationCompat.Builder(this, CHANNEL_ID)
            .setSmallIcon(R.drawable.ic_notification)
            .setContentTitle(title)
            .setContentText(message)
            .setPriority(NotificationCompat.PRIORITY_HIGH)
            .setAutoCancel(true);
        
        NotificationManager notificationManager = 
            (NotificationManager) getSystemService(Context.NOTIFICATION_SERVICE);
        notificationManager.notify(0, builder.build());
    }
}
16.2. Thông báo trong app (In-app Notifications)
Mô tả: Hiển thị danh sách thông báo trong ứng dụng.

Chức năng chi tiết:

Màn hình danh sách thông báo
Badge hiển thị số thông báo chưa đọc
Đánh dấu đã đọc/chưa đọc
Xóa thông báo
API Endpoint:

GET /api/user/notifications
PUT /api/user/notifications/{notificationId}/mark-read
DELETE /api/user/notifications/{notificationId}
NHÓM 17: YÊU THÍCH (Favorites)
17.1. Thêm món vào danh sách yêu thích
Mô tả: Người dùng lưu món ăn yêu thích.

Chức năng chi tiết:

Icon "trái tim" ở chi tiết món ăn
Tap to toggle (thêm/bỏ yêu thích)
Animation khi thêm
Lưu local và sync với server
API Endpoint:

POST /api/user/favorites/{foodId}
DELETE /api/user/favorites/{foodId}
GET /api/user/favorites
17.2. Xem danh sách món yêu thích
Mô tả: Hiển thị tất cả món đã lưu.

Chức năng chi tiết:

Màn hình riêng hoặc tab trong Profile
Grid/List hiển thị món yêu thích
Nút "Thêm vào giỏ hàng" nhanh
NHÓM 18: KHUYẾN MÃI & MÃ GIẢM GIÁ (Promotions & Coupons)
18.1. Hiển thị danh sách khuyến mãi
Mô tả: Người dùng xem các chương trình khuyến mãi.

Chức năng chi tiết:

Banner khuyến mãi ở Home
Màn hình danh sách khuyến mãi
Chi tiết: điều kiện, thời gian, giảm giá
API Endpoint:

GET /api/promotions
Response: {
    "promotions": [
        {
            "id": "string",
            "title": "string",
            "description": "string",
            "image": "string",
            "discountType": "PERCENTAGE|FIXED_AMOUNT",
            "discountValue": "number",
            "minOrderValue": "number",
            "startDate": "timestamp",
            "endDate": "timestamp",
            "code": "string"
        }
    ]
}
18.2. Áp dụng mã giảm giá
Mô tả: Người dùng nhập mã giảm giá khi đặt hàng.

Chức năng chi tiết:

Ô nhập mã giảm giá ở màn hình checkout
Nút "Áp dụng"
Kiểm tra mã hợp lệ
Hiển thị số tiền được giảm
API Endpoint:

POST /api/coupons/validate
Request Body: {
    "code": "string",
    "totalPrice": "number"
}
Response: {
    "valid": "boolean",
    "discountAmount": "number",
    "message": "string"
}
18.3. Quản lý mã giảm giá (Admin)
Mô tả: Admin tạo và quản lý mã giảm giá.

Chức năng chi tiết:

Thêm mã mới
Thiết lập: % giảm hoặc số tiền, điều kiện, thời gian
Xem số lượt sử dụng
Xóa/Vô hiệu hóa mã
NHÓM 19: BÁO CÁO & THỐNG KÊ (Reports & Analytics) - ADMIN
19.1. Dashboard tổng quan
Mô tả: Admin xem thống kê tổng quan.

Chức năng chi tiết:

Doanh thu hôm nay
Số đơn hàng hôm nay
Số khách hàng mới
Biểu đồ doanh thu 7 ngày qua
Top 5 món bán chạy
API Endpoint:

GET /api/admin/dashboard
Response: {
    "todayRevenue": "number",
    "todayOrders": "number",
    "newCustomers": "number",
    "last7DaysRevenue": [
        {
            "date": "string",
            "revenue": "number"
        }
    ],
    "topSellingFoods": []
}
19.2. Báo cáo doanh thu chi tiết
Mô tả: Admin xuất báo cáo doanh thu.

Chức năng chi tiết:

Chọn khoảng thời gian
Xuất file PDF hoặc Excel
Gửi email báo cáo
19.3. Thống kê khách hàng
Mô tả: Admin xem thống kê về khách hàng.

Chức năng chi tiết:

Tổng số khách hàng
Khách hàng mới trong tháng
Khách hàng mua nhiều nhất
Tần suất đặt hàng
NHÓM 20: CÀI ĐẶT ỨNG DỤNG (App Settings)
20.1. Cài đặt tài khoản
Mô tả: Người dùng tùy chỉnh cài đặt tài khoản.

Chức năng chi tiết:

Bật/tắt thông báo đẩy
Bật/tắt thông báo email
Ngôn ngữ (Tiếng Việt/English)
Chế độ tối/sáng (Dark/Light mode)
20.2. Điều khoản & Chính sách
Mô tả: Hiển thị điều khoản sử dụng và chính sách bảo mật.

Chức năng chi tiết:

Điều khoản sử dụng
Chính sách bảo mật
Chính sách hoàn tiền
20.3. Giới thiệu ứng dụng
Mô tả: Thông tin về ứng dụng.

Chức năng chi tiết:

Phiên bản app
Logo
Thông tin developer
Liên hệ hỗ trợ
20.4. Đăng xuất
Mô tả: Thoát khỏi tài khoản.

NHÓM 21: BẢO MẬT & XÁC THỰC (Security & Authentication)
21.1. Xác thực 2 yếu tố (2FA)
Mô tả: Bảo mật tài khoản bằng OTP qua SMS/Email.

Chức năng chi tiết:

Bật/tắt 2FA trong cài đặt
Gửi OTP khi đăng nhập
Xác thực OTP
21.2. Quản lý phiên đăng nhập
Mô tả: Xem và quản lý các thiết bị đang đăng nhập.

Chức năng chi tiết:

Danh sách thiết bị
Thời gian đăng nhập
Đăng xuất từ xa
NHÓM 22: THANH TOÁN (Payment Integration)
22.1. Tích hợp thanh toán Momo
Mô tả: Thanh toán qua ví Momo.

Implementation:

// Momo SDK Integration
MoMoPayment.getInstance().requestPayment(
    amount,
    orderId,
    orderInfo,
    new MoMoCallback() {
        @Override
        public void onSuccess(MoMoResponse response) {
            // Update order status
        }
        
        @Override
        public void onError(MoMoError error) {
            // Show error
        }
    }
);
22.2. Tích hợp thanh toán ZaloPay
Mô tả: Thanh toán qua ZaloPay.

22.3. Thanh toán COD
Mô tả: Thanh toán khi nhận hàng.

NHÓM 23: CHAT HỖ TRỢ (Customer Support Chat)
23.1. Chat với admin
Mô tả: Người dùng chat trực tiếp với admin.

Chức năng chi tiết:

Realtime chat (Firebase Realtime Database)
Gửi tin nhắn văn bản
Gửi hình ảnh
Hiển thị trạng thái đã xem
Implementation:

// Firebase Realtime Database
DatabaseReference chatRef = FirebaseDatabase.getInstance()
    .getReference("chats")
    .child(userId);

// Send message
chatRef.push().setValue(new Message(
    userId,
    "admin",
    messageText,
    System.currentTimeMillis()
));

// Listen for messages
chatRef.addChildEventListener(new ChildEventListener() {
    @Override
    public void onChildAdded(DataSnapshot snapshot, String previousChildName) {
        Message message = snapshot.getValue(Message.class);
        messageList.add(message);
        adapter.notifyDataSetChanged();
    }
});
23.2. Admin quản lý chat
Mô tả: Admin xem và trả lời tất cả tin nhắn.

Chức năng chi tiết:

Danh sách cuộc hội thoại
Badge tin nhắn chưa đọc
Gửi tin nhắn nhanh
NHÓM 24: TÍCH HỢP BÊN THỨ BA (Third-party Integrations)
24.1. Google Maps
Mô tả: Hiển thị vị trí quán, chỉ đường.

24.2. Facebook Login
Mô tả: Đăng nhập bằng tài khoản Facebook.

Implementation:

// Facebook SDK
LoginButton loginButton = findViewById(R.id.login_button);
loginButton.setReadPermissions("email", "public_profile");
loginButton.registerCallback(callbackManager, new FacebookCallback<LoginResult>() {
    @Override
    public void onSuccess(LoginResult loginResult) {
        // Get user info and login
    }
});
24.3. Google Login
Mô tả: Đăng nhập bằng tài khoản Google.

NHÓM 25: TÍNH NĂNG BỔ SUNG (Additional Features)
25.1. Đánh giá món ăn sau khi hoàn thành đơn
Mô tả: Sau khi đơn hàng hoàn thành, yêu cầu đánh giá.

Chức năng chi tiết:

Dialog đánh giá món ăn
Rating sao cho từng món
Nhập nhận xét
25.2. Tìm kiếm bằng giọng nói
Mô tả: Tìm kiếm món ăn bằng voice search.

Implementation:

// Speech Recognition
Intent intent = new Intent(RecognizerIntent.ACTION_RECOGNIZE_SPEECH);
intent.putExtra(RecognizerIntent.EXTRA_LANGUAGE_MODEL, 
    RecognizerIntent.LANGUAGE_MODEL_FREE_FORM);
intent.putExtra(RecognizerIntent.EXTRA_LANGUAGE, "vi-VN");
startActivityForResult(intent, SPEECH_REQUEST_CODE);
25.3. Chia sẻ món ăn lên mạng xã hội
Mô tả: Chia sẻ món ăn yêu thích lên Facebook, Instagram.

Implementation:

Intent shareIntent = new Intent(Intent.ACTION_SEND);
shareIntent.setType("text/plain");
shareIntent.putExtra(Intent.EXTRA_TEXT, 
    "Món " + foodName + " ngon quá! Tải app để đặt món: [link]");
startActivity(Intent.createChooser(shareIntent, "Chia sẻ qua"));
25.4. Tích điểm thưởng (Loyalty Points)
Mô tả: Tích điểm khi mua hàng, đổi quà.

Chức năng chi tiết:

Mỗi đơn hàng nhận 1% giá trị đơn thành điểm
Xem số điểm hiện tại
Đổi điểm lấy voucher
25.5. Chương trình giới thiệu bạn bè
Mô tả: Nhận thưởng khi giới thiệu bạn bè.

Chức năng chi tiết:

Mã giới thiệu cá nhân
Chia sẻ mã
Nhận thưởng khi bạn bè đăng ký và đặt hàng
� SETUP FIREBASE
Bước 1: Tạo project Firebase
Truy cập: https://console.firebase.google.com/
Click "Add project" → Nhập tên project → Create
Trong project, click icon Android để thêm app
Nhập package name (vd: com.example.foodorder)
Download file google-services.json
Copy file vào thư mục app/ trong Android Studio
Bước 2: Cài đặt Firebase SDK trong Android Studio
File: build.gradle (Project level)

buildscript {
    dependencies {
        classpath 'com.google.gms:google-services:4.4.0'
    }
}
File: build.gradle (App level)

plugins {
    id 'com.android.application'
    id 'com.google.gms.google-services'
}

dependencies {
    // Firebase BOM (Bill of Materials)
    implementation platform('com.google.firebase:firebase-bom:32.7.0')
    
    // Firebase services
    implementation 'com.google.firebase:firebase-auth'
    implementation 'com.google.firebase:firebase-firestore'
    implementation 'com.google.firebase:firebase-storage'
    implementation 'com.google.firebase:firebase-messaging'
    implementation 'com.google.firebase:firebase-functions'
    
    // Other libraries
    implementation 'com.github.bumptech.glide:glide:4.16.0'
}
Bước 3: Enable Firebase services
Authentication: Console → Build → Authentication → Get Started → Enable Email/Password
Firestore: Console → Build → Firestore Database → Create Database → Start in test mode
Storage: Console → Build → Storage → Get Started → Start in test mode
Cloud Messaging: Console → Build → Cloud Messaging (tự động enable)
Bước 4: Tạo Admin user đầu tiên
Vào Authentication → Users → Add user
Tạo email: admin@foodorder.com + password
Vào Firestore → users collection → Add document:
Document ID: {uid của admin}
Fields:
role: "ADMIN"
name: "Admin"
email: "admin@foodorder.com"
�🛠️ CÔNG NGHỆ SỬ DỤNG
Frontend (Android)
Ngôn ngữ: Java
Architecture: MVVM (Model-View-ViewModel)
Libraries:
Retrofit 2: HTTP client
Glide/Picasso: Load ảnh
Room Database: Local database
LiveData & ViewModel: Reactive UI
RecyclerView: Danh sách
ViewPager2: Slider
Material Design Components
Firebase Cloud Messaging: Push notifications
Firebase Authentication: Xác thực
Google Maps SDK
Momo/ZaloPay SDK: Thanh toán
Backend (Server)
Platform: Firebase (Google)
Database: Firebase Firestore (NoSQL realtime database)
Authentication: Firebase Authentication (Email/Password, Google, Facebook)
File Storage: Firebase Storage
Push Notifications: Firebase Cloud Messaging (FCM)
AI/ML: Logic trong app (Java) hoặc Firebase Functions (JavaScript) cho phân tích đơn hàng
Tools & Services
Version Control: Git + GitHub
API Testing: Postman
Design: Figma
Analytics: Google Analytics
Crash Reporting: Firebase Crashlytics
📱 CẤU TRÚC ỨNG DỤNG ANDROID
FoodOrderApp/
├── app/
│   ├── src/
│   │   ├── main/
│   │   │   ├── java/com/example/foodorder/
│   │   │   │   ├── model/           # Data models
│   │   │   │   │   ├── User.java
│   │   │   │   │   ├── Food.java
│   │   │   │   │   ├── Order.java
│   │   │   │   │   ├── CartItem.java
│   │   │   │   │   ├── Feedback.java
│   │   │   │   ├── view/            # Activities & Fragments
│   │   │   │   │   ├── auth/
│   │   │   │   │   │   ├── LoginActivity.java
│   │   │   │   │   │   ├── RegisterActivity.java
│   │   │   │   │   ├── home/
│   │   │   │   │   │   ├── HomeActivity.java
│   │   │   │   │   │   ├── FoodDetailActivity.java
│   │   │   │   │   ├── cart/
│   │   │   │   │   │   ├── CartActivity.java
│   │   │   │   │   │   ├── CheckoutActivity.java
│   │   │   │   │   ├── order/
│   │   │   │   │   │   ├── OrderHistoryActivity.java
│   │   │   │   │   ├── admin/
│   │   │   │   │   │   ├── AdminDashboardActivity.java
│   │   │   │   │   │   ├── ManageFoodsActivity.java
│   │   │   │   ├── viewmodel/       # ViewModels
│   │   │   │   │   ├── AuthViewModel.java
│   │   │   │   │   ├── FoodViewModel.java
│   │   │   │   │   ├── CartViewModel.java
│   │   │   │   │   ├── OrderViewModel.java
│   │   │   │   ├── repository/      # Data repositories
│   │   │   │   │   ├── UserRepository.java
│   │   │   │   │   ├── FoodRepository.java
│   │   │   │   ├── database/        # Room Database
│   │   │   │   │   ├── AppDatabase.java
│   │   │   │   │   ├── dao/
│   │   │   │   │   │   ├── CartDao.java
│   │   │   │   │   │   ├── FavoriteDao.java
│   │   │   │   ├── network/         # API Service
│   │   │   │   │   ├── ApiClient.java
│   │   │   │   │   ├── ApiService.java
│   │   │   │   ├── adapter/         # RecyclerView Adapters
│   │   │   │   │   ├── FoodAdapter.java
│   │   │   │   │   ├── CartAdapter.java
│   │   │   │   │   ├── OrderAdapter.java
│   │   │   │   ├── utils/           # Utilities
│   │   │   │   │   ├── Constants.java
│   │   │   │   │   ├── SharedPrefManager.java
│   │   │   │   │   ├── ValidationUtils.java
│   │   │   │   ├── ai/              # AI Smart Upsell
│   │   │   │   │   ├── UpsellService.java
│   │   │   │   │   ├── SmartUpsellManager.java
│   │   │   ├── res/
│   │   │   │   ├── layout/          # XML Layouts
│   │   │   │   ├── drawable/        # Images & Icons
│   │   │   │   ├── values/          # Strings, Colors, Styles
│   │   │   ├── AndroidManifest.xml
🔐 BẢO MẬT
Mã hóa mật khẩu: BCrypt
HTTPS cho tất cả API calls
JWT token có thời gian hết hạn
Validation đầu vào
SQL Injection prevention
XSS protection
🚀 ROADMAP PHÁT TRIỂN
Phase 1: MVP (Minimum Viable Product) - 2 tháng
Authentication & Account Management
Food Display & Search
Shopping Cart
Basic Order Placement
Phase 2: Core Features - 1.5 tháng
Admin Panel (Manage Foods, Orders)
Order History
Feedback System
Payment Integration
Phase 3: Advanced Features - 1.5 tháng
AI Smart Upsell
Push Notifications
Revenue Tracking
Reports & Analytics
Phase 4: Enhancement - 1 tháng
Chat Support
Loyalty Points
Referral Program
Social Sharing
📊 KPI & METRICS
Conversion Rate: % người dùng hoàn thành đơn hàng
Average Order Value (AOV): Giá trị đơn hàng trung bình
Customer Retention Rate: % khách hàng quay lại
AI Upsell Conversion: % chấp nhận gợi ý upsell
App Rating: Đánh giá trên Google Play Store
📝 GHI CHÚ
File này có thể được cập nhật theo yêu cầu thực tế
Mỗi tính năng cần có test case riêng
UI/UX sẽ được thiết kế chi tiết trên Figma
Cần tài liệu API riêng cho backend team
Ngày cập nhật: 01/02/2026
Người tạo: GitHub Copilot
Trạng thái: Draft - Chờ duyệt
