# Deploy IQC: GitHub Pages + Firebase (đăng nhập Google, database Firestore)

## 1. Tạo project Firebase
1. Vào https://console.firebase.google.com → **Add project** (tắt Google Analytics cũng được).
2. **Project settings → Your apps → Web (`</>`)** → đặt tên app → copy khối `firebaseConfig`.
3. Mở `index.html`, tìm `DAN_API_KEY`, `DAN_PROJECT_ID`, `DAN_APP_ID` (đầu thẻ `<script type="module">`) và dán giá trị thật vào.

## 2. Bật đăng nhập Google
1. **Build → Authentication → Get started → Sign-in method → Google → Enable** (chọn email hỗ trợ → Save).
2. **Authentication → Settings → Authorized domains → Add domain**: `TEN-GITHUB.github.io`
   (chỉ nhập tên miền, không có `/ten-repo`). Thiếu bước này, đăng nhập sẽ báo `auth/unauthorized-domain`.

## 3. Tạo database và luật bảo mật
1. **Build → Firestore Database → Create database** → chọn **production mode**, vị trí gần Việt Nam (vd. `asia-southeast1`).
2. Tab **Rules** → xoá nội dung cũ, dán toàn bộ file `firestore.rules` → **Publish**.

## 4. Cấp quyền cho người dùng (bắt buộc)
Vì dùng Gmail cá nhân, ai cũng đăng nhập được, nên quyền truy cập được kiểm soát bằng danh sách này:
1. Firestore → **Start collection** → ID: `allowed`.
2. **Document ID = email viết thường** (vd. `nguyenvana@gmail.com`), thêm 1 field bất kỳ (vd. `ten` = "Nguyễn Văn A") → Save.
3. Thêm người mới = thêm document mới; thu hồi quyền = xoá document.

## 5. Đưa lên GitHub Pages
1. Tạo repo mới trên GitHub, upload `index.html` (đúng tên này). `firestore.rules` có thể upload kèm để lưu trữ.
2. **Settings → Pages → Build and deployment → Deploy from a branch → `main` / `(root)` → Save**.
3. Sau 1–2 phút truy cập `https://TEN-GITHUB.github.io/TEN-REPO/`.

## 6. Chuyển dữ liệu cũ
Dữ liệu cũ nằm trong trình duyệt/file bạn đang dùng, không tự sang web mới:
1. Mở bản cũ → **Lưu bản sao lưu** (file `.json`).
2. Mở web mới, đăng nhập → **Mở bản sao lưu** → xác nhận. Dữ liệu được ghi lên Firestore và mọi người thấy ngay.

## Lưu ý
- `apiKey` của Firebase không phải bí mật; an toàn nằm ở **Rules + danh sách `allowed`**. Đừng bỏ bước 3 và 4.
- Kế hoạch (tên máy, mã tài liệu) nằm trong `index.html` nên ai biết link đều xem được mã nguồn, dù chưa đăng nhập. Chỉ dữ liệu thực hiện/kết quả trên Firestore mới được bảo vệ. Nếu cần ẩn cả kế hoạch, dùng repo private (cần gói GitHub trả phí để Pages) hoặc Firebase Hosting.
- Mỗi lần ai đó sửa, dữ liệu đồng bộ realtime cho mọi người; mỗi dòng ghi kèm email người sửa (`by`) và thời gian (`at`) để truy vết.
- Gói Spark (miễn phí) của Firebase đủ cho quy mô này. Giao diện console có thể khác chút so với mô tả.
