# HỆ THỐNG QUẢN LÝ KHO VỚI PHÂN QUYỀN NGƯỜI DÙNG

## TỔNG QUAN

Hệ thống quản lý kho được xây dựng trên Google Apps Script với tính năng phân quyền người dùng theo vai trò và kho được phân công. Hệ thống sử dụng **đăng nhập bằng tài khoản và mật khẩu** thay vì Google OAuth.

## TÍNH NĂNG PHÂN QUYỀN

### 1. Các Vai Trò Người Dùng

#### **Admin**
- ✅ Toàn quyền trên hệ thống
- ✅ Quản lý tài khoản người dùng
- ✅ Thay đổi cài đặt hệ thống
- ✅ Xem tất cả dữ liệu

#### **Kế Toán**
- ✅ **Toàn quyền của thủ kho** + **quyền điền giá**
- ✅ Tạo, sửa, xóa sản phẩm và chứng từ
- ✅ **Điền/sửa đơn giá** (quyền đặc biệt)
- ✅ Truy cập tất cả kho
- ✅ Xem báo cáo tồn kho

#### **Thủ Kho**
- ✅ **Quyền hạn chế** theo kho được phân công
- ✅ Tạo, sửa, xóa **CHỈ KHI** chứng từ chưa có giá
- ✅ **KHÔNG ĐƯỢC** điền/sửa đơn giá
- ✅ **KHÔNG ĐƯỢC** sửa/xóa chứng từ đã có giá
- ✅ Chỉ xem dữ liệu kho được phân công

### 2. Cách Thức Hoạt Động

- **Đăng nhập**: Form đăng nhập với USER/PASS
- **Session**: Duy trì trạng thái đăng nhập
- **Phân quyền**: Kiểm tra quyền trước khi thực hiện thao tác
- **Lọc dữ liệu**: Chỉ hiển thị dữ liệu theo kho được phân công
- **Giao diện**: Ẩn/hiện các chức năng theo quyền

## HƯỚNG DẪN THIẾT LẬP

### Bước 1: Tạo Google Sheet

1. Tạo Google Sheet mới
2. Thay đổi ID trong file `code.txt`:
   ```javascript
   const SPREADSHEET_ID = 'YOUR_SHEET_ID_HERE';
   ```

### Bước 2: Tạo Sheet USERS

Tạo sheet `USERS` với cấu trúc sau:

| Cột | Mô tả | Ví dụ |
|-----|-------|-------|
| USER | Tên đăng nhập | `admin`, `ketoan1`, `thukho1` |
| PASS | Mật khẩu đăng nhập | `123456`, `ketoan@2024` |
| ROLE | Phân quyền | `admin`, `accountant`, `warehouse_keeper` |
| STATUS | Trạng thái | `active`, `inactive` |
| KHO | Kho được phân công | `KHO1,KHO2` hoặc `*` (tất cả kho) |

**Dữ liệu mẫu:**
```
USER        | PASS      | ROLE              | STATUS  | KHO
admin       | admin123  | admin             | active  | *
ketoan1     | ketoan123 | accountant        | active  | *
thukho1     | thukho123 | warehouse_keeper  | active  | KHO1,KHO2
thukho2     | thukho456 | warehouse_keeper  | active  | KHO3
```

### Bước 3: Thiết Lập Google Apps Script

1. Mở [Google Apps Script](https://script.google.com/)
2. Tạo project mới
3. Copy nội dung file `code.txt` vào `Code.gs`
4. Copy nội dung file `index.txt` vào `Index.html`
5. Lưu project

### Bước 4: Triển Khai Web App

1. Click "Deploy" > "New deployment"
2. Chọn "Web app"
3. Thiết lập:
   - **Execute as**: Me
   - **Who has access**: Anyone
4. Click "Deploy"

### Bước 5: Sử Dụng Hệ Thống

1. Truy cập Web App
2. Đăng nhập bằng tài khoản đã tạo trong sheet USERS
3. Hệ thống sẽ hiển thị giao diện theo quyền của người dùng

## SỬ DỤNG HỆ THỐNG

### Đăng Nhập

1. Nhập **Tên đăng nhập** (USER)
2. Nhập **Mật khẩu** (PASS)
3. Click **Đăng nhập**

### Quản Lý Người Dùng (Admin)

1. Vào tab **Cài đặt**
2. Chọn **Quản lý người dùng** để xem danh sách
3. Chọn **Phân quyền** để cập nhật vai trò

### Cập Nhật Vai Trò

1. Chọn người dùng từ dropdown
2. Chọn vai trò mới
3. Nếu chọn "Thủ kho", nhập kho được phân công
4. Click "Cập nhật vai trò"

### Phân Quyền Tự Động

Hệ thống sẽ tự động cập nhật phân quyền khi thay đổi vai trò:

- **Admin**: Tất cả quyền trên tất cả kho
- **Kế toán**: Quyền tạo/sửa và điền giá trên tất cả kho
- **Thủ kho**: Quyền hạn chế chỉ trên kho được phân công

## LUẬT NGHIỆP VỤ

### Quyền Sửa/Xóa Chứng Từ:

- **Thủ kho**: Chỉ được sửa/xóa khi `DON_GIA = 0` hoặc `DON_GIA = null`
- **Kế toán**: Có thể sửa/xóa bất kỳ lúc nào
- **Admin**: Toàn quyền

### Quyền Truy Cập Kho:

- **Admin & Kế toán**: Tất cả kho
- **Thủ kho**: Chỉ kho được ghi trong cột `KHO`

## BẢO MẬT

- **Session-based authentication**: Duy trì trạng thái đăng nhập
- **Kiểm tra quyền**: Tất cả thao tác đều được kiểm tra quyền ở backend
- **Lọc dữ liệu**: Người dùng chỉ có thể truy cập dữ liệu theo quyền được cấp
- **Không thể bypass**: Không thể vượt qua phân quyền từ frontend

## KHẮC PHỤC SỰ CỐ

### Sử Dụng Tính Năng Debug

Hệ thống có sẵn các tính năng debug để giúp khắc phục sự cố:

1. **Kiểm tra trạng thái hệ thống**: Xem tổng quan về người dùng và phát hiện vấn đề
2. **Kiểm tra cấu trúc sheet**: Xem cấu trúc sheet USERS có đúng không
3. **Xóa cache**: Xóa cache để đảm bảo dữ liệu mới nhất
4. **Logs chi tiết**: Tất cả thao tác đều được log trong Google Apps Script

### Cách Sử Dụng Debug

1. Mở form đăng nhập
2. Click vào "🔧 Debug & Kiểm tra hệ thống"
3. Sử dụng các nút kiểm tra để xem thông tin chi tiết:
   - **Kiểm tra trạng thái hệ thống**: Xem tổng quan người dùng
   - **Kiểm tra cấu trúc sheet**: Xem cấu trúc sheet USERS
   - **Xóa cache**: Xóa cache để đảm bảo dữ liệu mới nhất

### Lỗi Thường Gặp

1. **"Tên đăng nhập hoặc mật khẩu không đúng"**
   - Kiểm tra USER và PASS trong sheet USERS
   - Đảm bảo STATUS = 'active'
   - Sử dụng "Kiểm tra cấu trúc sheet" để xem cấu trúc

2. **"Bạn không có quyền truy cập chức năng này"**
   - Kiểm tra ROLE trong sheet USERS
   - Liên hệ Admin để được cấp quyền

3. **Không thấy tab Cài đặt**
   - Chỉ Admin mới thấy tab này
   - Kiểm tra ROLE trong sheet USERS

4. **Không thể sửa/xóa chứng từ**
   - Thủ kho chỉ được sửa/xóa khi chưa có giá
   - Kiểm tra DON_GIA trong chứng từ

5. **"Sheet USERS có vấn đề"**
   - Kiểm tra tên sheet có đúng là "USERS" không
   - Kiểm tra các cột: USER, PASS, ROLE, STATUS, KHO
   - Đảm bảo có ít nhất 1 dòng dữ liệu

### Hỗ Trợ

Nếu gặp vấn đề, hãy kiểm tra:
1. Console của trình duyệt
2. Logs trong Google Apps Script
3. Quyền truy cập Google Sheet
4. Cấu trúc sheet USERS

## CẬP NHẬT VÀ BẢO TRÌ

- **Quản lý người dùng**: Admin có thể dễ dàng thay đổi quyền trong sheet USERS
- **Phân quyền tự động**: Hệ thống tự động cập nhật giao diện theo quyền
- **Cache dữ liệu**: Cache dữ liệu trong 15 phút để tối ưu hiệu suất
- **Session management**: Tự động đăng xuất khi có lỗi hoặc hết hạn

## LƯU Ý QUAN TRỌNG

1. **Bảo mật mật khẩu**: Không chia sẻ mật khẩu với người khác
2. **Đăng xuất**: Luôn đăng xuất khi không sử dụng
3. **Phân quyền**: Chỉ cấp quyền cần thiết cho từng người dùng
4. **Sao lưu**: Thường xuyên sao lưu dữ liệu quan trọng