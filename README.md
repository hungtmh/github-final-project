# 📦 Shipping Order Management System

Hệ thống quản lý đơn hàng giao hàng được xây dựng với React, Vite, Tailwind CSS v4 và Supabase. Dự án này cho phép người dùng tạo, chỉnh sửa, xóa và quản lý các đơn hàng giao hàng với giao diện hiện đại và hỗ trợ chế độ Dark Mode.

---

## 📋 Mục Lục

- [Tính Năng](#-tính-năng)
- [Công Nghệ Sử Dụng](#-công-nghệ-sử-dụng)
- [Yêu Cầu Hệ Thống](#-yêu-cầu-hệ-thống)
- [Cài Đặt](#-cài-đặt)
- [Cấu Trúc Dự Án](#-cấu-trúc-dự-án)
- [Hướng Dẫn Sử Dụng](#-hướng-dẫn-sử-dụng)
- [API & Database](#-api--database)
- [Các Lệnh Hữu Ích](#-các-lệnh-hữu-ích)
- [Troubleshooting](#-troubleshooting)

---

## ✨ Tính Năng

### 🎯 Quản Lý Đơn Hàng
- ✅ **Tạo đơn hàng mới** với thông tin chi tiết (người nhận, địa chỉ, tỉnh/thành, phường/xã)
- ✅ **Xem danh sách đơn hàng** với thông tin đầy đủ
- ✅ **Chỉnh sửa đơn hàng** ngay trên danh sách (inline editing)
- ✅ **Xóa đơn hàng** với xác nhận trước khi xóa
- ✅ **Đánh dấu hoàn thành/chưa hoàn thành** bằng toggle button

### 🎨 Giao Diện
- ✅ **Dark Mode/Light Mode** - Chuyển đổi dễ dàng với nút toggle
- ✅ **Responsive Design** - Tối ưu cho mọi kích thước màn hình
- ✅ **Modern UI** - Giao diện đẹp mắt với Tailwind CSS v4
- ✅ **Smooth Transitions** - Hiệu ứng chuyển đổi mượt mà

### 🗺️ Quản Lý Địa Chỉ
- ✅ **Tỉnh/Thành phố** - Danh sách được tải từ Supabase
- ✅ **Phường/Xã** - Tự động lọc theo tỉnh/thành phố đã chọn
- ✅ **Sắp xếp tăng dần** - Dữ liệu được sắp xếp theo alphabet

### 🔍 Validation
- ✅ **Form Validation** với React Hook Form và Zod
- ✅ **Error Messages** - Hiển thị lỗi rõ ràng cho từng trường

---

## 🛠 Công Nghệ Sử Dụng

| Công Nghệ | Phiên Bản | Mục Đích |
|-----------|-----------|----------|
| **React** | 19.2.0 | Thư viện UI chính |
| **Vite** | 7.2.4 | Build tool & dev server |
| **Tailwind CSS** | 4.1.17 | Framework CSS utility-first |
| **React Hook Form** | 7.66.1 | Quản lý form |
| **Zod** | 4.1.13 | Schema validation |
| **Supabase** | - | Backend & Database |
| **React Spinners** | 0.17.0 | Loading indicators |

---

## 💻 Yêu Cầu Hệ Thống

Trước khi bắt đầu, hãy đảm bảo bạn đã cài đặt:

- **Node.js** (phiên bản 18.x trở lên) - [Tải tại đây](https://nodejs.org/)
- **npm** hoặc **yarn** (đi kèm với Node.js)
- **Git** (tùy chọn) - [Tải tại đây](https://git-scm.com/)
- Trình duyệt web hiện đại (Chrome, Firefox, Edge, Safari)

### Kiểm tra cài đặt:
```bash
node --version   # Nên hiển thị v18.x.x trở lên
npm --version    # Nên hiển thị 9.x.x trở lên
```

---

## 🚀 Cài Đặt

### Bước 1: Clone hoặc tải dự án

**Dùng Git:**
```bash
git clone https://github.com/nguyenkha/csc13008-23ktpm1.git
cd csc13008-23ktpm1/shipping
```

**Hoặc tải ZIP:**
1. Tải file ZIP từ GitHub
2. Giải nén vào thư mục bất kỳ
3. Mở Terminal/CMD tại thư mục `shipping`

### Bước 2: Cài đặt dependencies

```bash
npm install
```

⏳ Quá trình này sẽ mất 1-2 phút để tải về tất cả các packages cần thiết.

### Bước 3: Chạy ứng dụng

```bash
npm run dev
```

✅ Nếu thành công, bạn sẽ thấy:
```
VITE v7.2.4  ready in 808 ms

➜  Local:   http://localhost:5173/
➜  Network: use --host to expose
```

### Bước 4: Mở trình duyệt

Truy cập: **http://localhost:5173/**

🎉 **Hoàn thành!** Bạn đã chạy thành công ứng dụng!

---

## 📁 Cấu Trúc Dự Án

```
shipping/
├── public/                 # Static files
├── src/
│   ├── assets/            # Images, icons
│   ├── ApiContext.js      # Context API cho Supabase
│   ├── App.jsx            # Component chính (routing, dark mode)
│   ├── App.css            # Styles cho App
│   ├── ListOrders.jsx     # Trang danh sách đơn hàng
│   ├── ShippingForm.jsx   # Form tạo đơn hàng mới
│   ├── main.jsx           # Entry point
│   └── index.css          # Global styles + Tailwind config
├── index.html             # HTML template
├── package.json           # Dependencies & scripts
├── vite.config.js         # Vite configuration
├── eslint.config.js       # ESLint configuration
└── README.md              # Tài liệu này
```

### 📄 Chi Tiết Các File Quan Trọng

#### `src/App.jsx`
- Component root của ứng dụng
- Quản lý routing giữa ListOrders và ShippingForm
- Xử lý logic dark mode
- Cung cấp ApiContext cho toàn bộ app

#### `src/ListOrders.jsx`
- Hiển thị danh sách đơn hàng
- Chức năng: xem, sửa, xóa, toggle complete
- Fetch data từ Supabase với join (provinces, wards)
- Inline editing mode

#### `src/ShippingForm.jsx`
- Form tạo đơn hàng mới
- Validation với Zod schema
- Dynamic loading: wards theo province
- Submit data lên Supabase

#### `src/ApiContext.js`
- React Context để chia sẻ API config
- Chứa URL và API Key của Supabase

#### `src/index.css`
- Import Tailwind CSS
- Custom variant cho dark mode
- `@custom-variant dark (&:where(.dark, .dark *));`

---

## 📖 Hướng Dẫn Sử Dụng

### 1️⃣ Tạo Đơn Hàng Mới

1. Click nút **"Create Order"** ở trang danh sách
2. Điền thông tin:
   - **Recipient**: Tên người nhận (tối thiểu 3 ký tự)
   - **House Number**: Số nhà
   - **Street**: Tên đường
   - **Province**: Chọn tỉnh/thành phố
   - **Ward**: Chọn phường/xã (sau khi chọn Province)
3. Click **"Submit"**
4. Thông báo thành công → Quay về danh sách

### 2️⃣ Xem Danh Sách Đơn Hàng

- Trang chủ hiển thị tất cả đơn hàng
- Thông tin hiển thị:
  - Order ID
  - Recipient (người nhận)
  - Address (địa chỉ đầy đủ)
  - Status (Completed/Pending)

### 3️⃣ Chỉnh Sửa Đơn Hàng

1. Click nút **✎ (Edit)** trên đơn hàng
2. Form chỉnh sửa hiện ra inline
3. Thay đổi thông tin cần thiết
4. Click **"Save"** hoặc **"Cancel"**

### 4️⃣ Đánh Dấu Hoàn Thành

- Click nút **✓ (màu xanh)** để đánh dấu hoàn thành
- Click nút **↺ (màu vàng)** để chuyển về pending
- Status badge tự động cập nhật màu

### 5️⃣ Xóa Đơn Hàng

1. Click nút **🗑️ (Delete)** màu đỏ
2. Xác nhận trong popup
3. Đơn hàng bị xóa khỏi database

### 6️⃣ Chuyển Đổi Dark Mode

- Click nút **🌙/☀️** ở góc trên bên phải
- Theme tự động lưu vào localStorage
- Reload trang vẫn giữ nguyên theme đã chọn

---

## 🗄️ API & Database

### Supabase Configuration

**API URL:**
```
https://bwwtoionbsosagwqllro.supabase.co/rest/v1
```

**Tables:**
- `orders` - Lưu thông tin đơn hàng
- `provinces` - Danh sách tỉnh/thành phố
- `wards` - Danh sách phường/xã

### Database Schema

#### Table: `orders`
```sql
- id (integer, primary key, auto-increment)
- recipient (text)
- house_number (text)
- street (text)
- province_id (integer, foreign key → provinces.id)
- ward_id (integer, foreign key → wards.id)
- completed (boolean, default: false)
- created_at (timestamp)
```

#### Table: `provinces`
```sql
- id (integer, primary key)
- name_with_type (text) - VD: "Thành phố Hồ Chí Minh"
```

#### Table: `wards`
```sql
- id (integer, primary key)
- name_with_type (text) - VD: "Phường Bến Nghé"
- province_id (integer, foreign key → provinces.id)
```

### API Endpoints Sử Dụng

**Lấy danh sách đơn hàng (với join):**
```
GET /orders?select=*,ward:wards(*),province:provinces(*)&order=id.desc
```

**Tạo đơn hàng mới:**
```
POST /orders
Body: { recipient, house_number, street, province_id, ward_id }
```

**Cập nhật đơn hàng:**
```
PATCH /orders?id=eq.{id}
Header: Prefer: return=minimal
Body: { completed: true/false } hoặc toàn bộ fields
```

**Xóa đơn hàng:**
```
DELETE /orders?id=eq.{id}
```

**Lấy danh sách provinces:**
```
GET /provinces?order=name_with_type.asc
```

**Lấy wards theo province:**
```
GET /wards?province_id=eq.{province_id}&order=name_with_type.asc
```

---

## 🔧 Các Lệnh Hữu Ích

| Lệnh | Mô Tả |
|------|-------|
| `npm install` | Cài đặt dependencies |
| `npm run dev` | Chạy dev server (http://localhost:5173) |
| `npm run build` | Build production (tạo folder `dist/`) |
| `npm run preview` | Preview bản build production |
| `npm run lint` | Kiểm tra lỗi code với ESLint |

### Build Production

```bash
npm run build
```

Kết quả được tạo trong folder `dist/`. Deploy folder này lên hosting (Vercel, Netlify, etc.)

---

## 🐛 Troubleshooting

### ❌ Lỗi: "npm: command not found"
**Nguyên nhân:** Chưa cài Node.js  
**Giải pháp:** Tải và cài Node.js từ https://nodejs.org/

### ❌ Lỗi: "Failed to update order status! (400 Bad Request)"
**Nguyên nhân:** Thiếu header `Prefer: return=minimal`  
**Giải pháp:** Đã fix trong code, đảm bảo dùng phiên bản mới nhất

### ❌ Lỗi: "Cannot GET /api/..."
**Nguyên nhân:** API endpoint sai hoặc Supabase không hoạt động  
**Giải pháp:** Kiểm tra API URL và API Key trong `src/ApiContext.js`

### ❌ Dark mode không hoạt động
**Nguyên nhân:** Tailwind v4 cần config đặc biệt  
**Giải pháp:** Đảm bảo `src/index.css` có dòng:
```css
@custom-variant dark (&:where(.dark, .dark *));
```

### ❌ Port 5173 đang được sử dụng
**Giải pháp:**
```bash
# Dừng process đang chạy hoặc dùng port khác
npm run dev -- --port 3000
```

### ❌ Lỗi khi cài đặt dependencies
**Giải pháp:**
```bash
# Xóa node_modules và package-lock.json
rm -rf node_modules package-lock.json
# Cài lại
npm install
```

---

## 🎓 Kiến Thức Cần Thiết

### Dành cho người mới:

1. **JavaScript ES6+**: Arrow functions, destructuring, async/await
2. **React Basics**: Components, props, state, hooks (useState, useEffect, useContext)
3. **HTML/CSS**: Cơ bản về form, styling
4. **REST API**: HTTP methods (GET, POST, PATCH, DELETE)

### Học thêm:

- [React Official Docs](https://react.dev/)
- [Tailwind CSS v4 Docs](https://tailwindcss.com/)
- [React Hook Form](https://react-hook-form.com/)
- [Zod Documentation](https://zod.dev/)
- [Supabase Docs](https://supabase.com/docs)

---

## 👨‍💻 Development Tips

### VS Code Extensions Khuyến Nghị:
- **ES7+ React/Redux/React-Native snippets**
- **Tailwind CSS IntelliSense**
- **ESLint**
- **Prettier - Code formatter**

### Code Style:
- Sử dụng functional components với hooks
- Arrow functions cho các handlers
- Destructuring props và state
- Comments cho logic phức tạp

---

## 📝 License

Dự án này được tạo cho mục đích học tập (CSC13008 - 23KTPM1).

---

## 🤝 Đóng Góp

Nếu bạn tìm thấy bug hoặc muốn thêm tính năng:
1. Fork repository
2. Tạo branch mới (`git checkout -b feature/AmazingFeature`)
3. Commit changes (`git commit -m 'Add some AmazingFeature'`)
4. Push to branch (`git push origin feature/AmazingFeature`)
5. Tạo Pull Request

---

## 📞 Liên Hệ & Hỗ Trợ

Nếu gặp vấn đề, hãy:
1. Kiểm tra phần [Troubleshooting](#-troubleshooting)
2. Search issues trên GitHub repository
3. Tạo issue mới với mô tả chi tiết

---

**Happy Coding! 🚀**

*Tài liệu này được tạo cho sinh viên CSC13008-23KTPM1*
