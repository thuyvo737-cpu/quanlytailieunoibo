# Cấp số & Quản lý Tài liệu nội bộ — BVQY175

Công cụ web tĩnh (chạy hoàn toàn trong trình duyệt, không có máy chủ, không cơ sở dữ liệu)
để theo dõi, cấp số tài liệu nội bộ (MSTL) và xem thống kê tổng hợp — thao tác trực tiếp
trên trang, tự động lưu lại vào file Excel trên máy bạn. Giao diện tông xanh lá, font chữ
Manrope/Inter.

## Nguyên tắc thiết kế

- Trang web (`index.html`) chỉ chứa mã nguồn — **không chứa dữ liệu tài liệu của bệnh viện.**
- Dữ liệu luôn nằm trên máy bạn. Bạn liên kết một lần với file `.xlsx` đang có sẵn; từ đó,
  mỗi lần thêm/sửa/xóa dòng, trang **tự ghi thẳng vào đúng file đó** (Chrome/Edge) — không
  cần bấm tải xuống thủ công.
- Trên trình duyệt không hỗ trợ (Firefox, Safari), trang tự động tải một bản sao cập nhật
  về thư mục Downloads sau mỗi thay đổi.

## Hai màn hình chính

1. **📋 Danh mục tài liệu** — xem/tìm/thêm/sửa/xóa theo từng loại tài liệu (tab), gợi ý mã
   MSTL tiếp theo tự động.
2. **📊 Thống kê tổng hợp** — tự tính lại từ dữ liệu hiện có, gồm: tổng số tài liệu, số đơn
   vị, số tài liệu ban hành trong năm hiện tại, số tài liệu chưa có số QĐ chính thức; bảng
   ma trận Đơn vị biên soạn × Loại tài liệu; biểu đồ số tài liệu ban hành theo năm; và ô
   đếm số tài liệu ban hành trong một khoảng thời gian tùy chọn.

## Quy trình triển khai: tạo repo trên GitHub → nối sang Cloudflare Pages

### Bước 1 — Tạo repository trên GitHub và tải mã nguồn lên
1. Đăng nhập **github.com** → bấm **+** góc trên phải → **New repository**.
2. Đặt tên, ví dụ `quanlytailieunoibo` → chọn **Public** → **Create repository**.
3. Trong repo vừa tạo, bấm **Add file → Upload files**.
4. Kéo thả 2 file `index.html` và `README.md` vào → **Commit changes**.

Đến đây repo GitHub đã có đầy đủ mã nguồn — mọi lần cần sửa code sau này, chỉ cần thay file
trong repo này.

### Bước 2 — Nối repository đó sang Cloudflare Pages (Connect to Git)
1. Vào **dash.cloudflare.com** → đăng nhập/đăng ký (miễn phí).
2. Menu trái: **Workers & Pages → Create → Pages → Connect to Git**.
3. Bấm **Connect GitHub**, cho phép Cloudflare truy cập tài khoản GitHub (có thể chọn "Only
   select repositories" và tick đúng repo `quanlytailieunoibo` cho an toàn).
4. Chọn repository `quanlytailieunoibo` → **Begin setup**.
5. Ở phần **Build settings**:
   - Framework preset: **None**
   - Build command: **để trống**
   - Build output directory: **/** (dấu gạch chéo, nghĩa là thư mục gốc)
6. Bấm **Save and Deploy**.

Sau khoảng 30–60 giây, Cloudflare cấp địa chỉ dạng `https://quanlytailieunoibo.pages.dev`.
Từ giờ, mỗi khi bạn cập nhật file trong repo GitHub, Cloudflare sẽ **tự động build và deploy
lại** — không cần thao tác thủ công ở Cloudflare nữa.

## Cách dùng

1. Mở link `.pages.dev` → bấm "Chọn file Excel trên máy" → chọn file
   `Theo_dõi_cấp_số_tài_liệu_nội_bộ.xlsx` → cho phép quyền đọc/ghi khi được hỏi.
2. Tab **📋 Danh mục tài liệu**: chọn loại tài liệu → "+ Thêm tài liệu mới" → điền thông
   tin → "Gợi ý MSTL" → kiểm tra lại → "Lưu". Có thể "Sửa"/"Xóa" trực tiếp dòng có sẵn.
3. Tab **📊 Thống kê tổng hợp**: xem ngay các số liệu tính từ toàn bộ dữ liệu đã nạp.
4. Mỗi lần Lưu/Xóa, trang tự ghi ngay vào file gốc trên máy — chấm tròn xanh và dòng trạng
   thái ở góc trên bên phải cho biết đã lưu lúc nào.

## Giới hạn cần biết

- Tự động ghi trực tiếp vào file chỉ hoạt động trên **Chrome hoặc Edge** bản mới.
- Định dạng gốc (màu nền, độ rộng cột) của các sheet được chỉnh sửa không được giữ lại khi
  ghi — các sheet không đụng tới (Tổng hợp, Biểu mẫu, Dữ liệu chung, Tra cứu…) giữ nguyên.
- Số liệu thống kê được tính lại từ đầu mỗi lần mở tab Thống kê dựa trên các cột Đơn vị biên
  soạn / Năm / Ngày hiệu lực / QĐ ban hành hiện có — nếu file gốc của bạn có công thức thống
  kê khác (ví dụ chỉ đếm tài liệu còn hiệu lực, loại trừ bản đã hết hiệu lực…), hãy cho biết
  cụ thể để chỉnh khớp chính xác hơn.
- Gợi ý MSTL dựa trên số lớn nhất hiện có của cùng đơn vị biên soạn trong cùng sheet — luôn
  kiểm tra lại trước khi lưu.
