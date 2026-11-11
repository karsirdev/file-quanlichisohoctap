| File | Loại | Chức năng |
|---|---|---|
| `session.js` | ![core](https://img.shields.io/badge/core-all_pages-d6336c) | Phiên đăng nhập (PB-01, PB-02, PB-03) |
| `auth_common.js` | ![auth](https://img.shields.io/badge/auth-login_/_register-7048e8) | Tiện ích trang Đăng nhập / Đăng ký |
| `app_shell.js` | ![layout](https://img.shields.io/badge/layout-after_login-1c7ed6) | Khung trang sau đăng nhập |
| `storageService.js` | ![mock](https://img.shields.io/badge/mock-localStorage-f59f00) | Dữ liệu tạm, thay bằng API khi BE xong |
| `gradeCalculator.js` | ![logic](https://img.shields.io/badge/logic-GPA-2f9e44) | Tính điểm, quy đổi, GPA |
| `app_common.js` | ![unused](https://img.shields.io/badge/status-unused-868e96) | Chưa trang nào nạp |

## 13. Cấu trúc Frontend

```
frontend/
├── index.html              # Trang Đăng nhập (mở đầu tiên)
├── pages/                  # Các trang còn lại
└── assets/
    ├── css/
    └── js/
```

### 13.1. Trang HTML

| Trang | Chức năng | JS riêng |
|---|---|---|
| `index.html` | Đăng nhập | `login.js` |
| `pages/register.html` | Đăng ký | `register.js` |
| `pages/forgot-password.html` | Quên mật khẩu | — |
| `pages/dashboard.html` | Trang chủ: thẻ chỉ số, biểu đồ, hoạt động, lịch học | `dashboard.js` |
| `pages/statistics.html` | Thống kê GPA, phân bố điểm, tiến độ tín chỉ, xuất CSV | `statistics.js` |
| `pages/semesters.html` | Quản lý học kỳ | `semesters.js` |
| `pages/courses.html` | Quản lý môn học và điểm | `courses.js` |
| `pages/goals.html` | Mục tiêu GPA và mô phỏng What-if | `goals.js` |
| `pages/import.html` | Nhập điểm từ CSV | `import.js` |
| `pages/transcript.html` | Bảng điểm, in A4 | `transcript.js` |
| `pages/profile.html` | Hồ sơ cá nhân, đổi mật khẩu | `profile.js` |
| `pages/schedule.html` | Thời khóa biểu | — |

### 13.2. JS dùng chung

| File | Chức năng | Hàm / thành phần chính |
|---|---|---|
| `session.js` | Phiên đăng nhập (PB-01, PB-02, PB-03). **Mọi trang đều nạp.** | `requireAuth`, `redirectIfLoggedIn`, `apiFetch`, `authApi`, `logout`, `mockServer` |
| `auth_common.js` | Tiện ích cho trang Đăng nhập / Đăng ký | `isValidEmail`, `showError`, `clearAllErrors`, `showModal`, `setSubmitting` |
| `app_shell.js` | Khung các trang sau đăng nhập: header, sidebar, menu tài khoản | `initAppShell`, `renderUser`, `showConfirm`, `showToast`, `escapeHtml` |
| `app_common.js` | Tiện ích điều hướng cũ, **hiện chưa trang nào nạp** | `initAppUser`, `highlightActiveNav` |
| `storageService.js` | Lưu dữ liệu học kỳ, môn, điểm vào localStorage (thay bằng API khi BE xong) | `StorageService` |
| `gradeCalculator.js` | Tính điểm môn, quy đổi thang điểm, GPA học kỳ và tích lũy | `GradeCalculator` |
| `csvHelper.js` | Đọc, kiểm tra và xuất CSV (chống CSV injection) | `CSVHelper` |

**`session.js` chi tiết:**

| Tính năng | Mô tả |
|---|---|
| Lưu token | Tick "Nhớ tài khoản" → `localStorage` (7 ngày); không tick → `sessionStorage` |
| Chặn trang | `requireAuth()` trong `<head>`: chưa đăng nhập → về trang Đăng nhập |
| Tự refresh | `apiFetch()` gặp 401 → refresh token rồi gửi lại; refresh hỏng → đăng xuất |
| Tài khoản bị khóa | 403 → xóa phiên, báo tài khoản bị khóa |
| Đường dẫn | `inPagesDir`, `loginPath`, `dashboardPath` tự chọn đường dẫn theo vị trí trang |
| Giả lập BE | `USE_MOCK = true` dùng `mockServer`; đổi `false` khi BE có API |

### 13.3. JS riêng từng trang

| File | Chức năng |
|---|---|
| `login.js` | Validate form, đăng nhập, xử lý 401 / 403 / 429 (khóa nút và đếm ngược), báo hết phiên |
| `register.js` | Validate (mật khẩu ≥ 8 ký tự), đăng ký, báo email đã tồn tại |
| `dashboard.js` | Thẻ chỉ số, biểu đồ SVG có tab, hoạt động gần đây, lịch học (đang dùng `MOCK_DATA`) |
| `statistics.js` | GPA tích lũy, xếp loại, biểu đồ, phân bố điểm, tiến độ tín chỉ, xuất CSV (đang dùng `MOCK_STATS`) |
| `semesters.js` | Thêm, sửa, xóa học kỳ; kiểm tra ngày; hiển thị GPA học kỳ |
| `courses.js` | Thêm, sửa, xóa, tìm kiếm môn; cấu hình thành phần điểm (tổng 100%); nhập điểm |
| `goals.js` | Lưu mục tiêu, tính điểm cần đạt (BR-13, BR-14), mô phỏng What-if |
| `import.js` | Kéo thả file CSV, xem trước kèm lỗi theo dòng, lưu hàng loạt |
| `transcript.js` | Hiển thị bảng điểm tổng hợp để in |
| `profile.js` | Sửa hồ sơ, ảnh đại diện, đổi mật khẩu |

### 13.4. CSS

| File | Chức năng | Dùng ở |
|---|---|---|
| `variables.css` | Màu sắc, font, biến dùng chung | Mọi trang |
| `common.css` | Reset và style cơ bản | Hầu hết các trang |
| `model.css` | Hộp thông báo (modal) | Mọi trang |
| `auth.css` | Thẻ Đăng nhập / Đăng ký / Quên mật khẩu | Trang xác thực |
| `app_layout.css` | Khung sau đăng nhập: sidebar, header, card, tabs, responsive | Trang sau đăng nhập |
| `app.css` | Học kỳ, môn học, mục tiêu, import, bảng điểm, hồ sơ | Các trang quản lý |
| `dashboard.css` | Phần riêng của Trang chủ | `dashboard.html` |
| `statistics.css` | Phần riêng của Thống kê | `statistics.html` |

### 13.5. Thứ tự nạp script

| Loại trang | Thứ tự |
|---|---|
| Đăng nhập / Đăng ký | `auth_common.js` → `session.js` → JS của trang |
| Sau đăng nhập | `storageService.js` → `gradeCalculator.js` → `session.js` → `app_shell.js` → JS của trang |

> File nạp sau dùng được hàm của file nạp trước, không có chiều ngược lại.
