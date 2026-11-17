# G-STUDY — Nền tảng học tiếng Anh lớp 10

Ứng dụng web học tiếng Anh lớp 10, gồm đăng ký/đăng nhập học sinh, lộ trình 10 Unit, lưu kết quả học tập, thống kê, trang cá nhân và khu vực quản trị.

## Triển khai lên Vercel

1. Đưa toàn bộ file trong thư mục này vào **thư mục gốc** của repository GitHub. `app.py`, `index.html`, `admin.html`, `requirements.txt` và `vercel.json` phải nằm ngay tại root.
2. Trên Vercel, Import repository đó. Đặt Root Directory là `./`; Framework Preset chọn `Other` nếu chưa tự nhận Flask; để Build Command và Output Directory mặc định/trống.
3. Kết nối PostgreSQL với project và đặt biến `DATABASE_URL` trong Project Settings → Environment Variables. Dùng connection string PostgreSQL do nhà cung cấp cấp.
4. Trong Environment Variables, đặt `ADMIN_EMAIL` và `ADMIN_PASSWORD`. Có thể giữ email quản trị ban đầu của dự án. Với `ADMIN_PASSWORD`, nhập mật khẩu quản trị đang dùng từ dự án gốc trực tiếp trong ô Value của Vercel. Không ghi mật khẩu vào file `.env`, HTML, JavaScript, README hoặc GitHub.
5. Áp dụng biến cho Production rồi Redeploy.

## Cổng quản trị nội bộ

Trang đăng nhập quản trị được phục vụ tại `/admin`. Khi chưa xác thực, trang này chỉ hiển thị nhãn nội bộ, một ô mật khẩu và nút truy cập. Trang không hiển thị email quản trị, tên học sinh, số liệu, trạng thái hệ thống hay thông tin cấu hình.

Đăng nhập thành công tạo cookie phiên `HttpOnly`, `Secure` trên HTTPS và chuyển tới khu vực quản trị. Trang tổng quan và mọi API quản trị đều yêu cầu phiên hợp lệ có quyền `admin` ở phía máy chủ. Đường dẫn `/admin/dashboard` tự chuyển về trang đăng nhập nếu chưa có quyền.

Cổng quản trị có giới hạn thử sai theo địa chỉ IP: tối đa 5 lần trong cửa sổ 15 phút, sau đó tạm khóa 15 phút. Lỗi đăng nhập luôn trả thông báo chung, không tiết lộ email/tài khoản có tồn tại hay không.

## Biến môi trường

| Biến | Mục đích |
|---|---|
| `DATABASE_URL` | Kết nối PostgreSQL trên Vercel |
| `ADMIN_EMAIL` | Email định danh tài khoản admin; không hiển thị trên trang đăng nhập |
| `ADMIN_PASSWORD` | Mật khẩu admin; chỉ lưu trong Vercel Environment Variables |

## Chạy thử cục bộ

```bash
pip install -r requirements.txt
```

Thiết lập `ADMIN_EMAIL`, `ADMIN_PASSWORD` và `DATABASE_URL` trong môi trường rồi chạy:

```bash
python app.py
```

Mở `http://127.0.0.1:8090` để vào ứng dụng học sinh hoặc `http://127.0.0.1:8090/admin` để vào cổng quản trị.

## Bảo mật

- Không upload file `.db`, `.sqlite`, `.env` hoặc connection string lên GitHub.
- Không nhúng mật khẩu quản trị vào mã nguồn. Người có quyền xem repository không thể đọc mật khẩu từ HTML/JavaScript.
- Để có dữ liệu bền vững trên Vercel, phải kết nối PostgreSQL; file SQLite cục bộ chỉ dùng cho thử nghiệm.
- Đăng nhập Google và gửi email đặt lại mật khẩu cần cấu hình OAuth/dịch vụ email riêng trước khi dùng.
