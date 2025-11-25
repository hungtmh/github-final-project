# 📚 Hướng Dẫn Chi Tiết Code - Shipping Order Management System

Tài liệu này giải thích chi tiết từng dòng code trong dự án, giúp người mới hiểu cách hoạt động của ứng dụng.

---

## 📋 Mục Lục

- [Cấu Trúc Tổng Quan](#cấu-trúc-tổng-quan)
- [File index.html](#file-indexhtml)
- [File main.jsx](#file-mainjsx)
- [File index.css](#file-indexcss)
- [File ApiContext.js](#file-apicontextjs)
- [File App.jsx](#file-appjsx)
- [File ShippingForm.jsx](#file-shippingformjsx)
- [File ListOrders.jsx](#file-listordersjsx)
- [File vite.config.js](#file-viteconfigjs)
- [Khái Niệm Quan Trọng](#khái-niệm-quan-trọng)

---

## Cấu Trúc Tổng Quan

```
Flow của ứng dụng:
index.html → main.jsx → App.jsx → (ListOrders.jsx | ShippingForm.jsx)
                           ↓
                     ApiContext.js
```

---

## File: `index.html`

**Mục đích:** File HTML gốc, là điểm khởi đầu của ứng dụng

```html
<!doctype html>
<html lang="en">
  <head>
    <meta charset="UTF-8" />
    <link rel="icon" type="image/svg+xml" href="/vite.svg" />
    <meta name="viewport" content="width=device-width, initial-scale=1.0" />
    <title>shipping</title>
  </head>
  <body>
    <div id="root"></div>
    <script type="module" src="/src/main.jsx"></script>
  </body>
</html>
```

**Giải thích từng dòng:**

```html
<!doctype html>
```
- Khai báo đây là HTML5 document

```html
<html lang="en">
```
- Thẻ html root, `lang="en"` chỉ định ngôn ngữ là tiếng Anh

```html
<meta charset="UTF-8" />
```
- Thiết lập encoding UTF-8 để hỗ trợ tiếng Việt và các ký tự đặc biệt

```html
<link rel="icon" type="image/svg+xml" href="/vite.svg" />
```
- Thiết lập favicon (icon hiển thị trên tab trình duyệt)

```html
<meta name="viewport" content="width=device-width, initial-scale=1.0" />
```
- Thiết lập responsive: width = chiều rộng thiết bị, zoom = 1.0

```html
<div id="root"></div>
```
- **QUAN TRỌNG:** Div này là nơi React sẽ render toàn bộ ứng dụng
- React sẽ "mount" vào element có id="root"

```html
<script type="module" src="/src/main.jsx"></script>
```
- Import file JavaScript chính (main.jsx)
- `type="module"` cho phép sử dụng ES6 modules (import/export)

---

## File: `main.jsx`

**Mục đích:** Entry point của React, khởi tạo ứng dụng

```jsx
import { StrictMode } from 'react'
import { createRoot } from 'react-dom/client'
import './index.css'
import App from './App.jsx'

createRoot(document.getElementById('root')).render(
  <StrictMode>
    <App />
  </StrictMode>,
)
```

**Giải thích từng dòng:**

```jsx
import { StrictMode } from 'react'
```
- Import `StrictMode` - mode kiểm tra lỗi nghiêm ngặt của React
- Giúp phát hiện lỗi tiềm ẩn trong quá trình development

```jsx
import { createRoot } from 'react-dom/client'
```
- Import `createRoot` - API mới của React 18 để render app
- Thay thế cho `ReactDOM.render()` cũ

```jsx
import './index.css'
```
- Import file CSS global (bao gồm Tailwind CSS)

```jsx
import App from './App.jsx'
```
- Import component App (component root của ứng dụng)

```jsx
createRoot(document.getElementById('root'))
```
- Tạo root React tại element có id="root" trong HTML
- `document.getElementById('root')` lấy div#root từ index.html

```jsx
.render(
  <StrictMode>
    <App />
  </StrictMode>,
)
```
- Render component App vào root
- Bọc trong `<StrictMode>` để bật mode kiểm tra lỗi

---

## File: `index.css`

**Mục đích:** Global CSS và cấu hình Tailwind

```css
@import "tailwindcss";

@custom-variant dark (&:where(.dark, .dark *));
```

**Giải thích:**

```css
@import "tailwindcss";
```
- Import toàn bộ Tailwind CSS v4
- Cung cấp tất cả utility classes như `bg-blue-500`, `text-white`, v.v.

```css
@custom-variant dark (&:where(.dark, .dark *));
```
- **Cấu hình Dark Mode cho Tailwind v4**
- Định nghĩa variant `dark:` hoạt động khi:
  - Element có class `dark`
  - Hoặc element nằm trong parent có class `dark`
- VD: `dark:bg-gray-800` sẽ áp dụng khi `<html class="dark">`

**Cách hoạt động:**
```jsx
// Khi dark mode OFF:
<html>
  <div class="bg-white dark:bg-gray-800">
    // bg-white được áp dụng
  </div>
</html>

// Khi dark mode ON:
<html class="dark">
  <div class="bg-white dark:bg-gray-800">
    // dark:bg-gray-800 được áp dụng (ghi đè bg-white)
  </div>
</html>
```

---

## File: `ApiContext.js`

**Mục đích:** Tạo Context để chia sẻ API config cho toàn bộ app

```jsx
import { createContext } from 'react';

export default createContext({});
```

**Giải thích:**

```jsx
import { createContext } from 'react';
```
- Import `createContext` từ React
- `createContext` tạo một Context object

```jsx
export default createContext({});
```
- Tạo và export một Context với giá trị mặc định là object rỗng `{}`
- Context này sẽ chứa `{ url, key }` khi được cung cấp từ App.jsx

**Cách sử dụng Context:**

```jsx
// Trong App.jsx (Provider - cung cấp dữ liệu):
<ApiContext.Provider value={{ url: apiUrl, key: apiKey }}>
  <ListOrders />
</ApiContext.Provider>

// Trong ListOrders.jsx (Consumer - sử dụng dữ liệu):
const api = useContext(ApiContext);
// api.url => 'https://...'
// api.key => 'eyJhbGci...'
```

**Tại sao dùng Context?**
- Tránh prop drilling (truyền props qua nhiều cấp)
- Dữ liệu API config được dùng ở nhiều component
- Dễ dàng thay đổi config ở một chỗ

---

## File: `App.jsx`

**Mục đích:** Component root, quản lý routing và dark mode

### Phần 1: Import và Setup

```jsx
import './App.css'
import { useState, useEffect } from 'react'
import ShippingForm from './ShippingForm'
import ListOrders from './ListOrders';
import ApiContext from './ApiContext';
```

**Giải thích:**
- Import CSS cho App
- Import hooks: `useState` (quản lý state), `useEffect` (side effects)
- Import các component con: ShippingForm, ListOrders
- Import ApiContext để cung cấp API config

### Phần 2: Component Function và State

```jsx
function App() {
  const apiUrl = 'https://bwwtoionbsosagwqllro.supabase.co/rest/v1';
  const apiKey = 'eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9...';
  const [currentPage, setCurrentPage] = useState('Home');
  const [darkMode, setDarkMode] = useState(false);
```

**Giải thích:**

```jsx
const apiUrl = 'https://...'
const apiKey = 'eyJhbGci...'
```
- **Supabase API Configuration**
- `apiUrl`: Base URL của Supabase REST API
- `apiKey`: Anonymous key để authenticate requests

```jsx
const [currentPage, setCurrentPage] = useState('Home');
```
- **State quản lý routing**
- `currentPage`: State lưu trang hiện tại ('Home' hoặc 'ShippingForm')
- `setCurrentPage`: Function để thay đổi trang
- `useState('Home')`: Giá trị khởi tạo là 'Home'

```jsx
const [darkMode, setDarkMode] = useState(false);
```
- **State quản lý dark mode**
- `darkMode`: true = dark mode ON, false = light mode ON
- Khởi tạo là `false` (light mode)

### Phần 3: Load Dark Mode từ LocalStorage

```jsx
useEffect(() => {
  const savedDarkMode = localStorage.getItem('darkMode') === 'true';
  setDarkMode(savedDarkMode);
  if (savedDarkMode) {
    document.documentElement.classList.add('dark');
  }
}, []);
```

**Giải thích từng dòng:**

```jsx
useEffect(() => { ... }, []);
```
- **useEffect hook**: Chạy side effects
- `[]` (dependency array rỗng): Chỉ chạy 1 lần khi component mount
- Tương đương `componentDidMount` trong class component

```jsx
const savedDarkMode = localStorage.getItem('darkMode') === 'true';
```
- Đọc giá trị 'darkMode' từ localStorage (lưu trữ trình duyệt)
- `localStorage.getItem()` trả về string hoặc null
- So sánh với 'true' để convert sang boolean
- VD: localStorage có 'darkMode': 'true' → savedDarkMode = true

```jsx
setDarkMode(savedDarkMode);
```
- Cập nhật state darkMode với giá trị đã lưu

```jsx
if (savedDarkMode) {
  document.documentElement.classList.add('dark');
}
```
- Nếu dark mode đã bật (savedDarkMode = true)
- Thêm class 'dark' vào thẻ `<html>` (`document.documentElement`)
- Class này kích hoạt các style `dark:` của Tailwind

**Flow:**
1. User bật dark mode → Lưu vào localStorage
2. Reload trang → useEffect chạy
3. Đọc localStorage → Thấy darkMode = 'true'
4. Set state và thêm class 'dark' → Dark mode tự động bật lại

### Phần 4: Toggle Dark Mode Function

```jsx
const toggleDarkMode = () => {
  const newDarkMode = !darkMode;
  setDarkMode(newDarkMode);
  localStorage.setItem('darkMode', newDarkMode);
  console.log('Dark mode:', newDarkMode);
  if (newDarkMode) {
    document.documentElement.classList.add('dark');
    console.log('Added dark class');
  } else {
    document.documentElement.classList.remove('dark');
    console.log('Removed dark class');
  }
};
```

**Giải thích:**

```jsx
const newDarkMode = !darkMode;
```
- Toggle boolean: false → true, true → false
- `!` là NOT operator

```jsx
setDarkMode(newDarkMode);
```
- Cập nhật state với giá trị mới

```jsx
localStorage.setItem('darkMode', newDarkMode);
```
- Lưu giá trị vào localStorage
- `newDarkMode` (boolean) tự động convert thành string
- VD: true → 'true', false → 'false'

```jsx
if (newDarkMode) {
  document.documentElement.classList.add('dark');
} else {
  document.documentElement.classList.remove('dark');
}
```
- Thêm/xóa class 'dark' từ thẻ `<html>`
- Kích hoạt/tắt dark mode styles

### Phần 5: Render Function

```jsx
const renderPage = () => {
  switch (currentPage) {
    case 'Home':
      return <ListOrders setCurrentPage={setCurrentPage} />;
    case 'ShippingForm':
      return <ShippingForm setCurrentPage={setCurrentPage} />;
  }
};
```

**Giải thích:**

```jsx
switch (currentPage) { ... }
```
- Kiểm tra giá trị của `currentPage`
- Tương tự if-else nhưng dễ đọc hơn cho nhiều cases

```jsx
case 'Home':
  return <ListOrders setCurrentPage={setCurrentPage} />;
```
- Nếu `currentPage === 'Home'`: render ListOrders
- Truyền `setCurrentPage` xuống child để child có thể đổi trang

```jsx
case 'ShippingForm':
  return <ShippingForm setCurrentPage={setCurrentPage} />;
```
- Nếu `currentPage === 'ShippingForm'`: render ShippingForm

**Cách hoạt động routing:**
1. User click "Create Order" trong ListOrders
2. ListOrders gọi `setCurrentPage('ShippingForm')`
3. State `currentPage` thay đổi → Component re-render
4. `renderPage()` thấy `currentPage === 'ShippingForm'` → Render ShippingForm

### Phần 6: Return JSX

```jsx
return (
  <div className="min-h-screen bg-white dark:bg-gray-900 transition-colors duration-200">
    <ApiContext.Provider value={{ url: apiUrl, key: apiKey }}>
      {/* Dark Mode Toggle Button */}
      <div className="fixed top-4 right-4 z-50">
        <button
          onClick={toggleDarkMode}
          className="p-3 rounded-full bg-gray-200 dark:bg-gray-700 hover:bg-gray-300 dark:hover:bg-gray-600 transition-colors duration-200 shadow-lg"
          title={darkMode ? 'Switch to Light Mode' : 'Switch to Dark Mode'}
        >
          {darkMode ? (
            <svg className="w-6 h-6 text-yellow-400" fill="currentColor" viewBox="0 0 20 20">
              {/* Sun icon */}
            </svg>
          ) : (
            <svg className="w-6 h-6 text-gray-700" fill="currentColor" viewBox="0 0 20 20">
              {/* Moon icon */}
            </svg>
          )}
        </button>
      </div>
      
      {renderPage()}
    </ApiContext.Provider>
  </div>
)
```

**Giải thích chi tiết:**

```jsx
<div className="min-h-screen bg-white dark:bg-gray-900 transition-colors duration-200">
```
- `min-h-screen`: Chiều cao tối thiểu = 100vh (full viewport)
- `bg-white`: Background trắng (light mode)
- `dark:bg-gray-900`: Background xám đậm (dark mode)
- `transition-colors duration-200`: Hiệu ứng chuyển màu mượt trong 200ms

```jsx
<ApiContext.Provider value={{ url: apiUrl, key: apiKey }}>
```
- **Context Provider**: Cung cấp dữ liệu cho toàn bộ component tree
- `value`: Object chứa url và key
- Mọi component con có thể dùng `useContext(ApiContext)` để lấy data

```jsx
<div className="fixed top-4 right-4 z-50">
```
- `fixed`: Position fixed (không cuộn theo trang)
- `top-4 right-4`: Cách top 1rem (16px), right 1rem
- `z-50`: Z-index cao để button luôn ở trên

```jsx
<button onClick={toggleDarkMode} ...>
```
- `onClick={toggleDarkMode}`: Gọi function khi click
- Không có `()` sau toggleDarkMode vì chỉ truyền reference

```jsx
{darkMode ? (
  <svg>...</svg>  // Sun icon
) : (
  <svg>...</svg>  // Moon icon
)}
```
- **Conditional rendering**
- Nếu `darkMode === true`: Hiển thị icon mặt trời (☀️)
- Nếu `darkMode === false`: Hiển thị icon mặt trăng (🌙)

```jsx
{renderPage()}
```
- Gọi function `renderPage()` để render component tương ứng

---

## File: `ShippingForm.jsx`

**Mục đích:** Form tạo đơn hàng mới với validation

### Phần 1: Imports

```jsx
import { useForm } from 'react-hook-form'
import { z } from 'zod'
import { zodResolver } from '@hookform/resolvers/zod'
import { useContext, useEffect, useState } from 'react'
import ApiContext from './ApiContext'
```

**Giải thích:**

```jsx
import { useForm } from 'react-hook-form'
```
- **React Hook Form**: Thư viện quản lý form hiệu quả
- `useForm`: Hook chính để tạo và quản lý form

```jsx
import { z } from 'zod'
```
- **Zod**: Thư viện validation schema
- Định nghĩa rules validation một cách type-safe

```jsx
import { zodResolver } from '@hookform/resolvers/zod'
```
- Adapter kết nối Zod với React Hook Form

### Phần 2: Component và State

```jsx
export default function ShippingForm({ setCurrentPage }) {
  const api = useContext(ApiContext);
  
  const [provinces, setProvinces] = useState([]);
  const [wards, setWards] = useState([]);
```

**Giải thích:**

```jsx
export default function ShippingForm({ setCurrentPage }) {
```
- Export component ShippingForm
- Nhận prop `setCurrentPage` từ App.jsx (để đổi trang)

```jsx
const api = useContext(ApiContext);
```
- **Consume Context**: Lấy API config từ ApiContext
- `api.url` và `api.key` sẽ có sẵn để dùng

```jsx
const [provinces, setProvinces] = useState([]);
const [wards, setWards] = useState([]);
```
- State lưu danh sách provinces (tỉnh/thành)
- State lưu danh sách wards (phường/xã)
- Khởi tạo là array rỗng `[]`

### Phần 3: Zod Schema Validation

```jsx
const schema = z.object({
  recipient: z.string().min(3, 'Recipent is required'),
  houseNumber: z.string().min(1, 'House number is required'),
  street: z.string().min(1, 'Street is required'),
  province: z.preprocess(
    val => (typeof val === "string" ? Number(val) : val), 
    z.number()
  ),
  ward: z.preprocess(
    val => (typeof val === "string" ? Number(val) : val), 
    z.number()
  ),
});
```

**Giải thích chi tiết:**

```jsx
const schema = z.object({ ... })
```
- Tạo validation schema dạng object
- Mỗi field trong form sẽ có rules riêng

```jsx
recipient: z.string().min(3, 'Recipent is required')
```
- `z.string()`: Kiểm tra kiểu string
- `.min(3, 'message')`: Tối thiểu 3 ký tự, nếu không hiện message lỗi

```jsx
province: z.preprocess(
  val => (typeof val === "string" ? Number(val) : val), 
  z.number()
)
```
- **Preprocess**: Xử lý giá trị trước khi validate
- `val => ...`: Arrow function nhận value
- `typeof val === "string" ? Number(val) : val`: 
  - Nếu là string → Convert sang number
  - Nếu không → Giữ nguyên
- `z.number()`: Sau khi preprocess, validate là number

**Tại sao cần preprocess?**
- `<select>` trong HTML trả về string
- VD: `<option value="1">` → value là "1" (string)
- Ta cần number để gửi lên API → Preprocess convert "1" → 1

### Phần 4: Load Provinces (useEffect)

```jsx
useEffect(() => {
  fetch(`${api.url}/provinces`, {
    headers: {
      apikey: api.key,
    },
  }).then(async (result) => {
    if (result.status === 200) {
      setProvinces(await result.json());
    } else {
      console.error('Cannot load province data:', result);
    }
  });
}, []);
```

**Giải thích từng dòng:**

```jsx
useEffect(() => { ... }, []);
```
- Chạy 1 lần khi component mount (dependency array rỗng)

```jsx
fetch(`${api.url}/provinces`, { ... })
```
- **Fetch API**: Gọi HTTP request đến Supabase
- URL: `https://.../rest/v1/provinces`
- Method mặc định: GET

```jsx
headers: {
  apikey: api.key,
}
```
- Header bắt buộc cho Supabase API
- `apikey`: Authentication key

```jsx
.then(async (result) => { ... })
```
- `.then()`: Xử lý khi request thành công
- `async`: Cho phép dùng `await` bên trong

```jsx
if (result.status === 200) {
  setProvinces(await result.json());
}
```
- Status 200 = OK (thành công)
- `result.json()`: Parse response body thành JSON
- `await`: Đợi parsing xong
- `setProvinces(...)`: Cập nhật state với data

**Flow:**
1. Component mount → useEffect chạy
2. Fetch provinces từ Supabase
3. Parse JSON response
4. Update state → Component re-render với data

### Phần 5: React Hook Form Setup

```jsx
const {
  register,
  handleSubmit,
  watch,
  setValue,
  formState: { errors },
} = useForm({
  resolver: zodResolver(schema),
});

const selectedProvince = watch('province');
```

**Giải thích:**

```jsx
const { register, handleSubmit, watch, setValue, formState: { errors } } = useForm({ ... })
```
- **Destructuring**: Lấy các methods từ useForm hook

**Các method:**

1. **register**: 
   ```jsx
   <input {...register('name')} />
   ```
   - Đăng ký input với form
   - Tự động bind value, onChange, onBlur

2. **handleSubmit**:
   ```jsx
   <form onSubmit={handleSubmit(onSubmit)}>
   ```
   - Wrapper cho submit handler
   - Tự động prevent default, validate, gọi callback

3. **watch**:
   ```jsx
   const value = watch('fieldName')
   ```
   - Theo dõi giá trị của field
   - Re-render khi field thay đổi

4. **setValue**:
   ```jsx
   setValue('fieldName', value)
   ```
   - Set giá trị cho field programmatically

5. **formState.errors**:
   ```jsx
   {errors.name && <p>{errors.name.message}</p>}
   ```
   - Object chứa lỗi validation
   - Mỗi field có thể có error message

```jsx
resolver: zodResolver(schema)
```
- Kết nối Zod schema với React Hook Form
- Mọi input sẽ được validate theo schema

```jsx
const selectedProvince = watch('province');
```
- Theo dõi giá trị của field 'province'
- Khi user chọn province → `selectedProvince` thay đổi → Re-render

### Phần 6: Load Wards khi Province Thay Đổi

```jsx
useEffect(() => {
  setValue('ward', '');
  setWards([]);
  if (selectedProvince && selectedProvince.length > 0) {
    fetch(`${api.url}/wards?province_id=eq.${selectedProvince}&order=name_with_type.asc`, {
      headers: {
        apikey: api.key,
      },
    }).then(async (result) => {
      if (result.status === 200) {
        setWards(await result.json());
      } else {
        console.error('Cannot load ward data:', result);
      }
    });
  }
}, [selectedProvince, setValue]);
```

**Giải thích:**

```jsx
useEffect(() => { ... }, [selectedProvince, setValue]);
```
- **Dependency array**: `[selectedProvince, setValue]`
- Chạy lại MỖI KHI `selectedProvince` thay đổi

```jsx
setValue('ward', '');
```
- Reset field 'ward' về rỗng
- Vì đổi province → ward cũ không còn hợp lệ

```jsx
setWards([]);
```
- Reset danh sách wards về array rỗng

```jsx
if (selectedProvince && selectedProvince.length > 0) {
```
- Kiểm tra đã chọn province chưa
- `selectedProvince.length > 0`: Đảm bảo không phải string rỗng

```jsx
fetch(`${api.url}/wards?province_id=eq.${selectedProvince}&order=name_with_type.asc`, ...)
```
- **Supabase Query Syntax**:
  - `?province_id=eq.${selectedProvince}`: Filter wards có province_id = selectedProvince
  - `&order=name_with_type.asc`: Sắp xếp tăng dần theo tên
  - `eq` = equals (toán tử so sánh)
  - `asc` = ascending (tăng dần)

**Flow khi user chọn province:**
1. User select province → `selectedProvince` thay đổi
2. useEffect trigger
3. Reset ward field và wards list
4. Fetch wards cho province mới
5. Update state → Dropdown ward hiển thị options mới

### Phần 7: Submit Handler

```jsx
const onSubmit = (data) => {
  console.log(data);
  fetch(`${api.url}/orders`, {
    method: 'POST',
    headers: {
      apikey: api.key,
      'Content-Type': 'application/json',
    },
    body: JSON.stringify({
      recipient: data.recipient,
      house_number: data.houseNumber,
      street: data.street,
      province_id: data.province,
      ward_id: data.ward,
    }),
  }).then((result) => {
    if (result.status === 201) {
      alert('Create new shipping order successfully!');
    } else {
      alert('Something went wrong! Check the console!');
      console.error(result);
    }
  });
};
```

**Giải thích:**

```jsx
const onSubmit = (data) => {
```
- Callback được gọi khi form valid
- `data`: Object chứa values của tất cả fields
- VD: `{ recipient: 'John', houseNumber: '123', ... }`

```jsx
console.log(data);
```
- Log để debug, xem data có đúng không

```jsx
method: 'POST',
```
- HTTP method POST để tạo mới resource

```jsx
'Content-Type': 'application/json',
```
- Header báo server rằng body là JSON

```jsx
body: JSON.stringify({ ... }),
```
- `JSON.stringify()`: Convert object JavaScript → JSON string
- Supabase yêu cầu body phải là JSON string

```jsx
recipient: data.recipient,
house_number: data.houseNumber,
```
- **Mapping field names**:
  - Form dùng camelCase: `houseNumber`
  - Database dùng snake_case: `house_number`
  - Phải convert khi gửi API

```jsx
if (result.status === 201) {
```
- Status 201 = Created (tạo thành công)
- 200 = OK, 400 = Bad Request, 500 = Server Error

**Flow submit:**
1. User click Submit → `handleSubmit` validate
2. Nếu valid → Gọi `onSubmit(data)`
3. POST data lên Supabase
4. Nếu 201 → Alert thành công
5. Nếu lỗi → Alert + console.error

### Phần 8: JSX Form Structure

```jsx
return (
  <form
    onSubmit={handleSubmit(onSubmit)}
    className="max-w-md mx-auto p-4 bg-white dark:bg-gray-800 shadow-md rounded-md space-y-6"
  >
```

**Giải thích:**

```jsx
onSubmit={handleSubmit(onSubmit)}
```
- `handleSubmit`: Wrapper của React Hook Form
- `onSubmit`: Callback function của chúng ta
- Flow: User submit → handleSubmit validate → Nếu OK → Gọi onSubmit

```jsx
className="max-w-md mx-auto p-4 bg-white dark:bg-gray-800 ..."
```
- `max-w-md`: Max width = 28rem (448px)
- `mx-auto`: Margin horizontal auto (center)
- `p-4`: Padding 1rem (16px)
- `bg-white dark:bg-gray-800`: BG trắng/xám đậm tùy mode
- `shadow-md`: Box shadow vừa
- `rounded-md`: Bo góc vừa
- `space-y-6`: Khoảng cách vertical 1.5rem giữa children

### Phần 9: Input Field Pattern

```jsx
<div>
  <label className="block mb-1 font-semibold text-gray-900 dark:text-gray-100" htmlFor="recipient">
    Recipient
  </label>
  <input
    id="recipient"
    type="text"
    {...register('recipient')}
    className={`w-full px-3 py-2 border rounded bg-white dark:bg-gray-700 text-gray-900 dark:text-white ${
      errors.recipient ? 'border-red-500' : 'border-gray-300 dark:border-gray-600'
    }`}
  />
  {errors.recipient && (
    <p className="text-red-500 dark:text-red-400 text-sm mt-1">{errors.recipient.message}</p>
  )}
</div>
```

**Giải thích từng phần:**

```jsx
<label htmlFor="recipient">Recipient</label>
```
- `htmlFor`: Liên kết label với input (click label = focus input)
- Phải match với `id` của input

```jsx
{...register('recipient')}
```
- **Spread operator**: Trải object thành props
- `register('recipient')` trả về:
  ```js
  {
    name: 'recipient',
    onChange: handleChange,
    onBlur: handleBlur,
    ref: inputRef
  }
  ```
- `{...}` spread tất cả props này vào input

```jsx
className={`... ${errors.recipient ? 'border-red-500' : 'border-gray-300 ...'}`}
```
- **Template literal với conditional**
- Nếu có lỗi: border đỏ
- Nếu không: border xám

```jsx
{errors.recipient && (
  <p>{errors.recipient.message}</p>
)}
```
- **Conditional rendering**
- Chỉ hiện error message nếu có lỗi
- `&&`: Short-circuit operator (nếu left false → không render right)

### Phần 10: Select Dropdown Pattern

```jsx
<select
  id="province"
  {...register('province')}
  className={`w-full px-3 py-2 border rounded bg-white dark:bg-gray-700 text-gray-900 dark:text-white ${
    errors.province ? 'border-red-500' : 'border-gray-300 dark:border-gray-600'
  }`}
>
  <option value="">Select province</option>
  {provinces.map(({ id, name_with_type }) => (
    <option key={id} value={id}>
      {name_with_type}
    </option>
  ))}
</select>
```

**Giải thích:**

```jsx
<option value="">Select province</option>
```
- Option mặc định (placeholder)
- `value=""`: Giá trị rỗng → Không hợp lệ theo schema

```jsx
{provinces.map(({ id, name_with_type }) => ( ... ))}
```
- **Array.map()**: Loop qua mảng provinces
- **Destructuring**: `{ id, name_with_type }` lấy properties từ object
- Return một `<option>` cho mỗi province

```jsx
<option key={id} value={id}>
  {name_with_type}
</option>
```
- `key={id}`: Key unique cho React (required trong list)
- `value={id}`: Giá trị được submit (number)
- `{name_with_type}`: Text hiển thị (VD: "Thành phố Hồ Chí Minh")

**Tại sao cần key?**
- React dùng key để track changes trong list
- Không có key → Warning và performance issues
- Key phải unique và stable

---

## File: `ListOrders.jsx`

**Mục đích:** Hiển thị danh sách đơn hàng với CRUD operations

### Phần 1: State Management

```jsx
const [orders, setOrders] = useState([]);
const [loading, setLoading] = useState(true);
const [editingId, setEditingId] = useState(null);
const [editForm, setEditForm] = useState({});
const [provinces, setProvinces] = useState([]);
const [wards, setWards] = useState([]);
const [searchTerm, setSearchTerm] = useState('');
```

**Giải thích các state:**

1. **orders**: Array chứa tất cả đơn hàng
   ```js
   [
     { id: 1, recipient: 'John', house_number: '123', ... },
     { id: 2, recipient: 'Jane', house_number: '456', ... }
   ]
   ```

2. **loading**: Boolean cho loading spinner
   - `true`: Đang fetch data → Hiện spinner
   - `false`: Đã có data → Hiện list

3. **editingId**: ID của order đang edit
   - `null`: Không edit order nào
   - `5`: Đang edit order có id = 5

4. **editForm**: Object chứa data đang edit
   ```js
   {
     recipient: 'John Doe',
     house_number: '123',
     street: 'Main St',
     province_id: 79,
     ward_id: 1234
   }
   ```

5. **provinces**: Array provinces cho dropdown edit

6. **wards**: Array wards cho dropdown edit (filtered by province)

7. **searchTerm**: String từ khóa tìm kiếm
   - VD: "john", "hcm", "123"

### Phần 2: Fetch Orders Function

```jsx
const fetchOrders = () => {
  fetch(`${api.url}/orders?select=*,ward:wards(*),province:provinces(*)&order=id.desc`, {
    headers: {
      apikey: api.key,
    },
  }).then(async (result) => {
    if (result.status === 200) {
      setLoading(false);
      setOrders(await result.json());
    } else {
      console.error('Cannot load order data:', result);
    }
  });
};
```

**Giải thích Supabase Query:**

```
/orders?select=*,ward:wards(*),province:provinces(*)&order=id.desc
```

**Breakdown:**
- `select=*`: Lấy tất cả columns của orders
- `,ward:wards(*)`: **JOIN** với table wards, đặt alias là 'ward'
- `,province:provinces(*)`: **JOIN** với table provinces, alias 'province'
- `&order=id.desc`: Sắp xếp giảm dần theo id (mới nhất trước)

**Response structure:**
```json
[
  {
    "id": 1,
    "recipient": "John",
    "house_number": "123",
    "street": "Main St",
    "province_id": 79,
    "ward_id": 1234,
    "ward": {
      "id": 1234,
      "name_with_type": "Phường Bến Nghé"
    },
    "province": {
      "id": 79,
      "name_with_type": "Thành phố Hồ Chí Minh"
    }
  }
]
```

**Tại sao cần join?**
- Table orders chỉ lưu `province_id` và `ward_id` (foreign keys)
- Để hiển thị tên, cần join với tables provinces và wards
- Supabase cho phép join ngay trong 1 request (thay vì 3 requests riêng)

### Phần 3: Delete Handler

```jsx
const handleDelete = (orderId) => {
  if (confirm('Are you sure you want to delete this order?')) {
    fetch(`${api.url}/orders?id=eq.${orderId}`, {
      method: 'DELETE',
      headers: {
        apikey: api.key,
      },
    }).then((result) => {
      if (result.status === 204) {
        alert('Order deleted successfully!');
        fetchOrders();
      } else {
        alert('Failed to delete order!');
        console.error(result);
      }
    });
  }
};
```

**Giải thích:**

```jsx
if (confirm('Are you sure...')) {
```
- `confirm()`: Native browser dialog với OK/Cancel
- Return `true` nếu user click OK, `false` nếu Cancel
- Ngăn xóa nhầm

```jsx
method: 'DELETE',
```
- HTTP method DELETE

```jsx
?id=eq.${orderId}
```
- Filter: Xóa record có id = orderId
- `eq` = equals operator

```jsx
if (result.status === 204) {
```
- Status 204 = No Content (xóa thành công)
- 204 không có response body

```jsx
fetchOrders();
```
- Refresh list sau khi xóa
- Lấy lại data mới từ server

### Phần 4: Toggle Complete Handler

```jsx
const handleToggleComplete = (orderId, currentStatus) => {
  fetch(`${api.url}/orders?id=eq.${orderId}`, {
    method: 'PATCH',
    headers: {
      apikey: api.key,
      'Content-Type': 'application/json',
      'Prefer': 'return=minimal',
    },
    body: JSON.stringify({
      completed: !currentStatus,
    }),
  }).then(async (result) => {
    if (result.status === 204) {
      fetchOrders();
    } else {
      const errorText = await result.text();
      alert('Failed to update order status!');
      console.error('Error response:', result.status, errorText);
    }
  });
};
```

**Giải thích:**

```jsx
method: 'PATCH',
```
- PATCH: Update một phần của resource (chỉ field `completed`)
- PUT: Update toàn bộ resource

```jsx
'Prefer': 'return=minimal',
```
- **Header đặc biệt của Supabase**
- `return=minimal`: Không trả về data, chỉ status
- Giảm bandwidth, tăng tốc độ

```jsx
body: JSON.stringify({
  completed: !currentStatus,
}),
```
- `!currentStatus`: Toggle boolean
  - `true` → `false`
  - `false` → `true`

**Flow:**
1. User click toggle button
2. Gọi API PATCH với `completed: !currentStatus`
3. Nếu 204 → Refresh orders list
4. List re-render với status mới

### Phần 5: Edit Handlers

```jsx
const handleEdit = (order) => {
  setEditingId(order.id);
  setEditForm({
    recipient: order.recipient,
    house_number: order.house_number,
    street: order.street,
    province_id: order.province_id,
    ward_id: order.ward_id,
  });
  // Load wards for selected province
  fetch(`${api.url}/wards?province_id=eq.${order.province_id}&order=name_with_type.asc`, {
    headers: {
      apikey: api.key,
    },
  }).then(async (result) => {
    if (result.status === 200) {
      setWards(await result.json());
    }
  });
};
```

**Giải thích:**

```jsx
setEditingId(order.id);
```
- Set state = id của order đang edit
- Component re-render → Hiển thị form edit cho order này

```jsx
setEditForm({ ... });
```
- Populate form với data hiện tại của order
- User sẽ thấy values sẵn trong inputs

```jsx
fetch(`${api.url}/wards?province_id=eq.${order.province_id}...`)
```
- Load wards cho province hiện tại
- Để dropdown ward có options đúng

### Phần 6: Save Edit Handler

```jsx
const handleSaveEdit = (orderId) => {
  fetch(`${api.url}/orders?id=eq.${orderId}`, {
    method: 'PATCH',
    headers: {
      apikey: api.key,
      'Content-Type': 'application/json',
      'Prefer': 'return=minimal',
    },
    body: JSON.stringify(editForm),
  }).then((result) => {
    if (result.status === 204) {
      alert('Order updated successfully!');
      setEditingId(null);
      setEditForm({});
      setWards([]);
      fetchOrders();
    } else {
      alert('Failed to update order!');
      console.error(result);
    }
  });
};
```

**Giải thích:**

```jsx
body: JSON.stringify(editForm),
```
- Gửi toàn bộ editForm object lên server
- Supabase sẽ update tất cả fields trong object

```jsx
setEditingId(null);
setEditForm({});
setWards([]);
```
- Reset states về default
- Exit edit mode
- Clear form data

```jsx
fetchOrders();
```
- Refresh list với data mới

**Flow edit:**
1. User click Edit → `handleEdit` → Show form
2. User modify fields → Update `editForm` state
3. User click Save → `handleSaveEdit` → PATCH API
4. Success → Reset states → Fetch new data → Show updated list

### Phần 7: Search Filter Logic

```jsx
const filteredOrders = orders.filter((order) => {
  if (!searchTerm) return true;
  
  const search = searchTerm.toLowerCase();
  return (
    order.id.toString().includes(search) ||
    order.recipient.toLowerCase().includes(search) ||
    order.house_number.toLowerCase().includes(search) ||
    order.street.toLowerCase().includes(search) ||
    order.ward.name_with_type.toLowerCase().includes(search) ||
    order.province.name_with_type.toLowerCase().includes(search)
  );
});
```

**Giải thích:**

```jsx
const filteredOrders = orders.filter((order) => { ... });
```
- `Array.filter()`: Tạo array mới chỉ chứa items thỏa điều kiện
- Return `true` = giữ lại, `false` = loại bỏ

```jsx
if (!searchTerm) return true;
```
- Nếu không có search term → Giữ tất cả orders
- `!searchTerm`: `""` (empty string) = falsy

```jsx
const search = searchTerm.toLowerCase();
```
- Convert search term sang lowercase
- Để so sánh case-insensitive (không phân biệt hoa thường)

```jsx
order.id.toString().includes(search)
```
- Convert id (number) sang string
- Check xem có chứa search term không
- VD: id = 123, search = "12" → `true`

```jsx
order.recipient.toLowerCase().includes(search)
```
- Convert recipient sang lowercase
- VD: recipient = "John Doe", search = "john" → `true`

```jsx
||
```
- OR operator: Chỉ cần 1 điều kiện `true` là return `true`
- Search match bất kỳ field nào → Hiển thị order đó

**Flow search:**
1. User type "john" vào search box
2. `searchTerm` state update → Component re-render
3. `filteredOrders` re-calculate
4. Chỉ orders có "john" trong bất kỳ field nào được giữ lại
5. Map `filteredOrders` thay vì `orders` → Chỉ hiện kết quả match

### Phần 8: Conditional Rendering (Edit Mode vs View Mode)

```jsx
{filteredOrders.map((o) => (
  <li key={o.id} ...>
    {editingId === o.id ? (
      // EDIT MODE
      <div className="space-y-3">
        <input ... />
        <button onClick={() => handleSaveEdit(o.id)}>Save</button>
        <button onClick={handleCancelEdit}>Cancel</button>
      </div>
    ) : (
      // VIEW MODE
      <div>
        <p>Order ID: #{o.id}</p>
        <p>Recipient: {o.recipient}</p>
        <button onClick={() => handleEdit(o)}>Edit</button>
        <button onClick={() => handleDelete(o.id)}>Delete</button>
      </div>
    )}
  </li>
))}
```

**Giải thích:**

```jsx
{editingId === o.id ? ... : ...}
```
- **Ternary operator**: `condition ? true_case : false_case`
- Nếu `editingId === o.id`: Đang edit order này → Show form
- Nếu không: Show view mode

**Edit Mode:**
```jsx
<input
  value={editForm.recipient}
  onChange={(e) => setEditForm({ ...editForm, recipient: e.target.value })}
/>
```
- Controlled input: Value từ state
- `onChange`: Update state khi user type
- `{ ...editForm, recipient: ... }`: Spread existing fields, override recipient

**View Mode:**
```jsx
<p>Recipient: {o.recipient}</p>
```
- Chỉ hiển thị text, không có input

**Buttons:**
```jsx
<button onClick={() => handleEdit(o)}>Edit</button>
```
- Arrow function: `() => handleEdit(o)`
- Truyền toàn bộ order object vào handler

```jsx
<button onClick={() => handleDelete(o.id)}>Delete</button>
```
- Chỉ truyền id (không cần toàn bộ object)

### Phần 9: Search UI Component

```jsx
<div className="mb-4">
  <div className="relative">
    <input
      type="text"
      placeholder="🔍 Tìm kiếm..."
      value={searchTerm}
      onChange={(e) => setSearchTerm(e.target.value)}
      className="w-full px-4 py-3 pl-10 border ..."
    />
    <svg className="absolute left-3 top-3.5 h-5 w-5">...</svg>
    {searchTerm && (
      <button onClick={() => setSearchTerm('')}>
        <svg>X icon</svg>
      </button>
    )}
  </div>
  <p className="mt-2 text-sm">
    Tìm thấy {filteredOrders.length} đơn hàng
  </p>
</div>
```

**Giải thích:**

```jsx
<div className="relative">
```
- Position relative: Parent của absolute positioned children

```jsx
<input ... className="... pl-10 ..." />
```
- `pl-10`: Padding left 2.5rem
- Để nhường chỗ cho icon search bên trái

```jsx
<svg className="absolute left-3 top-3.5 ...">
```
- Position absolute: Nằm trên input
- `left-3 top-3.5`: Vị trí icon

```jsx
{searchTerm && (
  <button onClick={() => setSearchTerm('')}>
```
- Chỉ hiện nút X khi có searchTerm
- Click X → Clear search

```jsx
Tìm thấy {filteredOrders.length} đơn hàng
```
- Dynamic counter
- Update realtime khi filter thay đổi

---

## File: `vite.config.js`

**Mục đích:** Cấu hình Vite build tool

```jsx
import { defineConfig } from 'vite'
import react from '@vitejs/plugin-react'
import tailwindcss from '@tailwindcss/vite'

export default defineConfig({
  plugins: [react(), tailwindcss()],
  server: {
    proxy: {
      '/rest': 'https://bwwtoionbsosagwqllro.supabase.co',
    },
  }
})
```

**Giải thích:**

```jsx
import { defineConfig } from 'vite'
```
- Helper function để define config với TypeScript intellisense

```jsx
plugins: [react(), tailwindcss()],
```
- **react()**: Plugin cho React (Fast Refresh, JSX transform)
- **tailwindcss()**: Plugin Tailwind v4 cho Vite

```jsx
server: {
  proxy: {
    '/rest': 'https://bwwtoionbsosagwqllro.supabase.co',
  },
}
```
- **Development proxy**
- Request đến `/rest/*` sẽ được forward đến Supabase
- Tránh CORS issues trong development

**VD:**
```js
// Code gọi:
fetch('/rest/v1/orders')

// Vite proxy thành:
fetch('https://bwwtoionbsosagwqllro.supabase.co/rest/v1/orders')
```

---

## Khái Niệm Quan Trọng

### 1. **React Hooks**

**useState:**
```jsx
const [value, setValue] = useState(initialValue);
```
- State management trong functional component
- `value`: Giá trị hiện tại
- `setValue`: Function để update
- Component re-render khi state thay đổi

**useEffect:**
```jsx
useEffect(() => {
  // Side effect code
  return () => {
    // Cleanup (optional)
  };
}, [dependencies]);
```
- Chạy side effects (API calls, subscriptions, etc.)
- `[]`: Chạy 1 lần khi mount
- `[dep]`: Chạy lại khi dep thay đổi
- No array: Chạy sau mỗi render

**useContext:**
```jsx
const value = useContext(MyContext);
```
- Consume context value
- Tránh prop drilling

### 2. **Component Communication**

**Parent → Child (Props):**
```jsx
// Parent
<Child name="John" age={25} />

// Child
function Child({ name, age }) {
  return <p>{name} is {age}</p>;
}
```

**Child → Parent (Callback Props):**
```jsx
// Parent
<Child onUpdate={(data) => console.log(data)} />

// Child
<button onClick={() => props.onUpdate('new data')}>
```

**Sibling Communication (Lift State Up):**
```jsx
function Parent() {
  const [data, setData] = useState('');
  return (
    <>
      <ChildA data={data} setData={setData} />
      <ChildB data={data} />
    </>
  );
}
```

### 3. **Controlled vs Uncontrolled Components**

**Controlled (Recommended):**
```jsx
const [value, setValue] = useState('');
<input 
  value={value} 
  onChange={(e) => setValue(e.target.value)} 
/>
```
- React state là "single source of truth"
- Value luôn sync với state

**Uncontrolled:**
```jsx
const inputRef = useRef();
<input ref={inputRef} />
// Lấy value: inputRef.current.value
```
- DOM là source of truth
- Dùng ref để access value

### 4. **Array Methods**

**map()** - Transform array:
```jsx
[1, 2, 3].map(x => x * 2)  // [2, 4, 6]
```

**filter()** - Filter array:
```jsx
[1, 2, 3, 4].filter(x => x > 2)  // [3, 4]
```

**find()** - Find first match:
```jsx
[1, 2, 3].find(x => x > 1)  // 2
```

### 5. **Async/Await vs Promises**

**Promise chain:**
```jsx
fetch(url)
  .then(response => response.json())
  .then(data => console.log(data))
  .catch(error => console.error(error));
```

**Async/await (cleaner):**
```jsx
try {
  const response = await fetch(url);
  const data = await response.json();
  console.log(data);
} catch (error) {
  console.error(error);
}
```

### 6. **Destructuring**

**Object:**
```jsx
const user = { name: 'John', age: 25 };
const { name, age } = user;
// name = 'John', age = 25
```

**Array:**
```jsx
const [first, second] = [1, 2, 3];
// first = 1, second = 2
```

**Props:**
```jsx
function Component({ name, age }) {
  // Thay vì: props.name, props.age
}
```

### 7. **Spread Operator**

**Copy array:**
```jsx
const arr1 = [1, 2];
const arr2 = [...arr1, 3];  // [1, 2, 3]
```

**Copy object:**
```jsx
const obj1 = { a: 1 };
const obj2 = { ...obj1, b: 2 };  // { a: 1, b: 2 }
```

**Update object:**
```jsx
setUser({ ...user, name: 'Jane' });
// Giữ nguyên other fields, chỉ update name
```

### 8. **Conditional Rendering**

**If/else:**
```jsx
{condition ? <ComponentA /> : <ComponentB />}
```

**And operator:**
```jsx
{isLoggedIn && <WelcomeMessage />}
```

**Nullish:**
```jsx
{data || <Loading />}
```

### 9. **Event Handlers**

**Inline:**
```jsx
<button onClick={() => console.log('clicked')}>
```

**Function reference:**
```jsx
<button onClick={handleClick}>
```

**With parameters:**
```jsx
<button onClick={() => handleClick(id)}>
```

### 10. **CSS Classes với Tailwind**

**Static:**
```jsx
<div className="bg-blue-500 text-white">
```

**Conditional:**
```jsx
<div className={`base-class ${isActive ? 'active' : 'inactive'}`}>
```

**Dark mode:**
```jsx
<div className="bg-white dark:bg-gray-900">
```

---

## Best Practices

### ✅ DO:

1. **Use functional components và hooks**
2. **Destructure props và state**
3. **Keep components small và focused**
4. **Use meaningful variable names**
5. **Add comments cho logic phức tạp**
6. **Validate user input**
7. **Handle errors properly**
8. **Use keys trong lists**

### ❌ DON'T:

1. **Mutate state directly** (`state.push()` ❌ → `setState([...state, item])` ✅)
2. **Forget dependency array** trong useEffect
3. **Use index as key** trong dynamic lists
4. **Put side effects** trực tiếp trong render
5. **Ignore errors** (luôn có try-catch hoặc .catch())

---

## Debugging Tips

### Console Logging:
```jsx
console.log('State:', state);
console.log('Props:', props);
console.error('Error:', error);
```

### React DevTools:
- Install extension
- Inspect component tree
- View props và state
- Profile performance

### Network Tab:
- Check API requests
- Verify request/response
- Check headers và body

### Breakpoints:
- Thêm `debugger;` trong code
- Use browser DevTools

---

**Chúc bạn code vui vẻ! 🚀**

*Tài liệu này giải thích chi tiết code cho người mới bắt đầu học React*
