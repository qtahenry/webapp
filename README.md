# HỆ THỐNG QUẢN LÝ KHO VỚI PHÂN QUYỀN NGƯỜI DÙNG

## TỔNG QUAN

Hệ thống quản lý kho được xây dựng trên Google Apps Script với tính năng phân quyền người dùng theo vai trò và kho được phân công.

## TÍNH NĂNG PHÂN QUYỀN

### 1. Các Vai Trò Người Dùng

#### **Admin**
- ✅ Toàn quyền trên hệ thống
- ✅ Quản lý người dùng và phân quyền
- ✅ Thay đổi cài đặt hệ thống
- ✅ Xem tất cả dữ liệu

#### **Kế Toán**
- ✅ Tạo mã hàng mới
- ✅ Điền giá nhập và giá bán
- ✅ Tạo phiếu nhập/xuất
- ✅ Xem báo cáo tồn kho
- ❌ Không được xóa dữ liệu quan trọng

#### **Thủ Kho**
- ✅ Tạo mã hàng (chỉ cho kho được phân công)
- ✅ Tạo phiếu nhập/xuất (chỉ cho kho được phân công)
- ✅ Điền số lượng phát sinh
- ✅ Xem báo cáo tồn kho (chỉ kho được phân công)
- ❌ Không được điền giá
- ❌ Không được xóa dữ liệu

### 2. Cách Thức Hoạt Động

- **Xác thực**: Sử dụng Google OAuth để xác thực người dùng
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

### Bước 2: Tạo Các Sheet Cần Thiết

#### Sheet `USERS`
| Cột | Mô tả |
|-----|-------|
| EMAIL | Email người dùng |
| FULL_NAME | Họ tên đầy đủ |
| ROLE | Vai trò (admin/accountant/warehouse_keeper) |
| STATUS | Trạng thái (active/inactive) |
| CREATED_DATE | Ngày tạo |
| ASSIGNED_WAREHOUSES | Kho được phân công (cách nhau bởi dấu phẩy) |

#### Sheet `USER_PERMISSIONS`
| Cột | Mô tả |
|-----|-------|
| USER_EMAIL | Email người dùng |
| PERMISSION | Tên quyền |
| WAREHOUSE_CODE | Mã kho (dấu * = tất cả kho) |
| GRANTED | Có được cấp quyền hay không |

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

### Bước 5: Thiết Lập Người Dùng Đầu Tiên

1. Truy cập Web App
2. Đăng nhập bằng tài khoản Google
3. Hệ thống sẽ tự động tạo người dùng với role `warehouse_keeper`
4. Để trở thành Admin, bạn cần:
   - Mở Google Sheet
   - Vào sheet `USERS`
   - Thay đổi `ROLE` thành `admin` cho email của bạn

## SỬ DỤNG HỆ THỐNG

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

## BẢO MẬT

- Tất cả thao tác đều được kiểm tra quyền ở backend
- Người dùng chỉ có thể truy cập dữ liệu theo quyền được cấp
- Không thể bypass phân quyền từ frontend

## KHẮC PHỤC SỰ CỐ

### Lỗi Thường Gặp

1. **"Không thể xác định người dùng hiện tại"**
   - Kiểm tra quyền truy cập Google Sheet
   - Đảm bảo đã đăng nhập Google

2. **"Bạn không có quyền truy cập chức năng này"**
   - Kiểm tra vai trò trong sheet `USERS`
   - Liên hệ Admin để được cấp quyền

3. **Không thấy tab Cài đặt**
   - Chỉ Admin mới thấy tab này
   - Kiểm tra role trong sheet `USERS`

### Hỗ Trợ

Nếu gặp vấn đề, hãy kiểm tra:
1. Console của trình duyệt
2. Logs trong Google Apps Script
3. Quyền truy cập Google Sheet

## CẬP NHẬT VÀ BẢO TRÌ

- Hệ thống tự động tạo người dùng mới khi đăng nhập lần đầu
- Phân quyền được cập nhật tự động khi thay đổi vai trò
- Cache dữ liệu trong 15 phút để tối ưu hiệu suất