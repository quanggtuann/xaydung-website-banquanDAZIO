# SD-53-NEW - Hệ Thống Bán Quần ÂU

Hệ thống e-commerce bán quần áo với kiến trúc phân tách Backend-Frontend, hỗ trợ bán hàng online và bán hàng tại quầy (POS).

## Kiến Trúc Hệ Thống

```
┌─────────────────┐     ┌─────────────────┐     ┌─────────────────┐
│   QuanView      │────▶│    QuanApi      │────▶│   SQL Server    │
│  (MVC Frontend) │     │  (.NET 8 API)   │     │   Database      │
└─────────────────┘     └─────────────────┘     └─────────────────┘
                               │
                               ▼
                        ┌─────────────────┐
                        │  Third-party    │
                        │  Integrations   │
                        │  - VNPay        │
                        │  - GHTK         │
                        │  - Google OAuth │
                        └─────────────────┘
```

## Cấu Trúc Dự Án

| Project | Loại | Mô Tả |
|---------|------|-------|
| **QuanApi** | .NET 8 Web API | Backend RESTful API, xử lý business logic, database access |
| **QuanView** | ASP.NET Core MVC | Frontend web application, giao diện khách hàng và admin |
| **QuanApi.Tests** | xUnit | Unit tests cho API controllers |

## Tính Năng Chính

### 🛒 Bán Hàng Online (Customer Portal)

- **Authentication**: Đăng ký/đăng nhập (JWT + Google OAuth2)
- **Sản phẩm**: Danh sách, chi tiết, tìm kiếm/lọc, gallery ảnh
- **Giỏ hàng**: Thêm/xóa/sửa số lượng, tính tổng tiền
- **Địa chỉ**: Quản lý sổ địa chỉ, tích hợp API 63 tỉnh thành Việt Nam
- **Đơn hàng**: Tạo đơn, theo dõi trạng thái, lịch sử mua hàng
- **Thanh toán**: VNPay integration (sandbox)
- **Vận chuyển**: Tính phí ship động qua Giao Hàng Tiết Kiệm API
- **Khuyến mãi**: Áp dụng mã giảm giá, đợt giảm giá

### 🖥️ Bán Hàng Tại Quầy (POS System)

- Quét barcode, thêm sản phẩm vào giỏ
- Thanh toán tiền mặt/chuyển khoản
- In hóa đơn
- Quản lý đơn hàng offline

### 📊 Quản Trị (Admin)

- Quản lý sản phẩm, biến thể (size, màu), tồn kho
- Quản lý đơn hàng, cập nhật trạng thái
- Quản lý khách hàng, nhân viên, phân quyền
- Báo cáo thống kê (Charts)
- Quản lý khuyến mãi

## Công Nghệ Sử Dụng

### Backend (QuanApi)

- **.NET 8** - Framework chính
- **Entity Framework Core 8** - ORM, database access
- **SQL Server** - Database
- **AutoMapper** - Object mapping
- **JWT Bearer Authentication** - Token-based auth
- **BCrypt** - Password hashing
- **EPPlus** - Excel export
- **MailKit** - Email service
- **Swagger/OpenAPI** - API documentation

### Frontend (QuanView)

- **ASP.NET Core MVC**
- **Bootstrap 5** - CSS framework
- **JavaScript** - Interactivity
- **HttpClient** - API communication

### Third-party Integrations

- **VNPay** - Thanh toán online
- **Giao Hàng Tiết Kiệm (GHTK)** - Tính phí vận chuyển
- **Google OAuth2** - Social login
- **Provinces Open API** - Dữ liệu địa chỉ Việt Nam

## Cài Đặt & Chạy Dự Án

### Yêu Cầu

- .NET 8 SDK
- SQL Server (LocalDB hoặc SQL Server Express)
- Visual Studio 2022 hoặc VS Code

### Bước 1: Clone Repository

```bash
git clone [repository-url]
cd SD-53-NEW
```

### Bước 2: Cấu Hình Database

1. Mở file `QuanApi/appsettings.json`
2. Cập nhật connection string:

```json
"ConnectionStrings": {
  "DefaultConnection": "Server=YOUR_SERVER;Database=BanQuanAuDb;Trusted_Connection=True;TrustServerCertificate=True;"
}
```

3. Chạy migration để tạo database:

```bash
cd QuanApi
dotnet ef database update
```

### Bước 3: Cấu Hình VNPay (Tùy chọn)

Trong `QuanApi/appsettings.json` hoặc `QuanView/appsettings.json`:

```json
"VnPay": {
  "TmnCode": "YOUR_TMN_CODE",
  "HashSecret": "YOUR_HASH_SECRET",
  "BaseUrl": "https://sandbox.vnpayment.vn/paymentv2/vpcpay.html",
  "ReturnUrl": "https://localhost:7182/Checkout/PaymentCallback"
}
```

> **Lưu ý**: Sử dụng sandbox credentials cho testing.

### Bước 4: Chạy Ứng Dụng

**Chạy Backend (QuanApi):**

```bash
cd QuanApi
dotnet run
```

API sẽ chạy tại: `https://localhost:7001` hoặc `http://localhost:5001`

Swagger UI: `https://localhost:7001/swagger`

**Chạy Frontend (QuanView):**

```bash
cd QuanView
dotnet run
```

Website sẽ chạy tại: `https://localhost:7182` hoặc `http://localhost:5172`

### Bước 5: Chạy Tests

```bash
cd QuanApi.Tests
dotnet test
```

## API Endpoints Chính

### Authentication

| Method | Endpoint | Mô tả |
|--------|----------|-------|
| POST | `/api/KhachHang/register` | Đăng ký khách hàng |
| POST | `/api/KhachHang/login` | Đăng nhập (JWT) |
| POST | `/api/KhachHang/google-login` | Đăng nhập Google |

### Sản Phẩm

| Method | Endpoint | Mô tả |
|--------|----------|-------|
| GET | `/api/SanPhams` | Danh sách sản phẩm |
| GET | `/api/SanPhams/{id}` | Chi tiết sản phẩm |
| GET | `/api/SanPhams/search?keyword=` | Tìm kiếm sản phẩm |

### Giỏ Hàng

| Method | Endpoint | Mô tả |
|--------|----------|-------|
| GET | `/api/GioHangs` | Xem giỏ hàng |
| POST | `/api/GioHangs/add` | Thêm vào giỏ |
| PUT | `/api/GioHangs/update` | Cập nhật số lượng |
| DELETE | `/api/GioHangs/remove/{id}` | Xóa khỏi giỏ |

### Đơn Hàng

| Method | Endpoint | Mô tả |
|--------|----------|-------|
| POST | `/api/HoaDons` | Tạo đơn hàng |
| GET | `/api/HoaDons/{id}` | Chi tiết đơn hàng |
| GET | `/api/HoaDons/my-orders` | Lịch sử đơn hàng |

### Thanh Toán

| Method | Endpoint | Mô tả |
|--------|----------|-------|
| POST | `/api/Checkout/create-payment` | Tạo URL thanh toán VNPay |
| GET | `/Checkout/PaymentCallback` | Xử lý callback từ VNPay |

## Cấu Trúc Database

```
DanhMuc          SanPham         HoaDon           KhachHang
├─ ThuongHieu    ├─ AnhSanPham   ├─ ChiTietHoaDon ├─ DiaChi
├─ ChatLieu      ├─ SanPhamChiTiet                ├─ GioHang
├─ MauSac        ├─ LoaiOng                       └─ ChiTietGioHang
├─ KichCo        ├─ KieuDang
├─ HoaTiet       └─ LungQuan
└─ DotGiamGia

NhanVien ── VaiTro
PhongTroChuyen ── TinNhan
Banner
PhieuGiamGia
```

## Tính Năng Bảo Mật

- **JWT Authentication** - Token-based auth với expiration
- **BCrypt Hashing** - Mã hóa mật khẩu an toàn
- **HMAC SHA512** - Xác thực callback VNPay
- **Input Validation** - Validate dữ liệu đầu vào
- **SQL Injection Prevention** - Sử dụng EF Core parameterized queries
- **CORS** - Cross-origin resource sharing configuration

## Xử Lý Concurrent Requests

- **Transaction** - Đảm bảo toàn vẹn dữ liệu khi tạo đơn hàng
- **Optimistic Locking** - RowVersion để tránh overselling sản phẩm

## Đóng Góp

1. Fork repository
2. Tạo branch mới (`git checkout -b feature/AmazingFeature`)
3. Commit thay đổi (`git commit -m 'Add some AmazingFeature'`)
4. Push lên branch (`git push origin feature/AmazingFeature`)
5. Mở Pull Request

## License

Dự án này được phát triển cho mục đích học tập.

## Liên Hệ

Nếu có câu hỏi hoặc góp ý, vui lòng tạo issue trong repository.

---

**Lưu ý**: Đây là dự án học tập. Một số tính năng (như VNPay, GHTK) sử dụng sandbox/development environment.
