# Bài 4: Khai Báo GitHub Secrets và Cấu Hình Đăng Nhập Container Registry (GHCR)

## 1. Mục tiêu
- Tạo Repository Secret lưu trữ Personal Access Token (PAT) trên GitHub.
- Cấu hình xác thực đăng nhập tự động vào GitHub Container Registry (`ghcr.io`) thông qua GitHub Actions workflow.

---

## 2. Hướng dẫn thực hiện từng bước

### Bước 1: Tạo Personal Access Token (classic) trên GitHub
1. Truy cập: [GitHub Settings > Developer settings > Personal access tokens > Tokens (classic)](https://github.com/settings/tokens).
2. Nhấn **Generate new token** -> chọn **Generate new token (classic)**.
3. Điền thông tin:
   - **Note**: `GHCR_REGISTRY_TOKEN`
   - **Expiration**: Chọn thời hạn phù hợp (ví dụ: `30 days` hoặc `90 days`).
   - **Scopes**: Tích chọn quyền **`write:packages`** (quyền `read:packages` sẽ tự động được chọn).
4. Cuộn xuống và nhấn **Generate token**.
5. **Sao chép và lưu token** ngay lập tức (token dạng `ghp_...`).

---

### Bước 2: Thêm Secret vào GitHub Repository
1. Mở trang Repository của bạn trên GitHub.
2. Điều hướng tới: **Settings** -> **Secrets and variables** -> **Actions**.
3. Tại mục **Repository secrets**, nhấn nút **New repository secret**.
4. Nhập:
   - **Name**: `REGISTRY_TOKEN` (bắt buộc đúng tên theo đề bài).
   - **Secret**: Dán chuỗi token đã sao chép ở Bước 1.
5. Nhấn **Add secret**.
> 📸 **Lưu ý nộp bài**: Chụp ảnh màn hình trang này hiển thị biến `REGISTRY_TOKEN` trong danh sách Repository secrets.

---

### Bước 3: Cấu hình kịch bản GitHub Actions (`ci.yml`)
Tệp workflow đã được tạo tại:
- [`.github/workflows/ci.yml`](../../.github/workflows/ci.yml)
- [`homework/session_11/ex4/ci.yml`](ci.yml)

Nội dung:
```yaml
name: CI Workflow

on:
  push:
    branches: [ "main", "master" ]
  pull_request:
    branches: [ "main", "master" ]
  workflow_dispatch:

jobs:
  build:
    name: Build and Login to Registry
    runs-on: ubuntu-latest

    steps:
      - name: Checkout mã nguồn
        uses: actions/checkout@v4

      - name: Đăng nhập vào GitHub Container Registry
        uses: docker/login-action@v3
        with:
          registry: ghcr.io
          username: ${{ github.actor }}
          password: ${{ secrets.REGISTRY_TOKEN }}
```

---

### Bước 4: Đẩy code và kiểm tra kết quả
1. Khởi tạo / Commit và Push code lên GitHub:
   ```bash
   git add .
   git commit -m "feat: configure ghcr login with docker/login-action@v3"
   git push origin main
   ```
2. Mở tab **Actions** trên Repository GitHub:
   - Chọn lần chạy workflow mới nhất.
   - Nhấp vào job **Build and Login to Registry**.
   - Mở rộng bước **Đăng nhập vào GitHub Container Registry**.
   - Xác nhận có dòng log:
     ```text
     Logging into ghcr.io...
     Login Succeeded
     ```

---

## 3. Sản phẩm nộp bài
1. Tệp kịch bản: `.github/workflows/ci.yml` (hoặc bản sao tại `homework/session_11/ex4/ci.yml`).
2. Ảnh chụp màn hình: Mục **Settings -> Secrets and variables -> Actions** hiển thị biến `REGISTRY_TOKEN`.
