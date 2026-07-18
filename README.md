# Cấp số & Quản lý Tài liệu nội bộ — BVQY175 (bản Google Sheets)

**Kiến trúc đã đơn giản hóa** — không còn trang "quản lý" tự viết code nữa. Giờ chỉ còn:

- **Google Sheet "Quản lý cấp số"** — bạn nhập/sửa/xóa dữ liệu **trực tiếp trên Google
  Sheets** (web hoặc app điện thoại), như một bảng tính bình thường. Không cần đăng nhập gì
  thêm ngoài tài khoản Google sẵn có của bạn.
- **`tracuu.html`** — trang tra cứu tĩnh, link riêng, ai có link cũng xem được, **không cần
  đăng nhập, không sửa được**. Đọc dữ liệu chỉ-đọc từ Google Sheet "Tra cứu" (tự đồng bộ từ
  Sheet quản lý qua `IMPORTRANGE`).

Vì không còn trang quản lý riêng, cũng **không cần thiết lập Google Cloud / OAuth Client ID**
nữa — phần đó đã được bỏ hẳn. Đổi lại, các tính năng "tự động" trước đây (gợi ý MSTL, Stt tự
tăng, tự chuyển tài liệu hết hiệu lực) giờ làm bằng **công thức Google Sheets** hoặc **thao
tác tay** — xem hướng dẫn ở Phần A2 bên dưới.

---

## PHẦN A — Thiết lập Google Sheet "Quản lý cấp số"

### A1. Tạo cấu trúc tab
Tạo một Google Sheet mới, đặt tên ví dụ **"BVQY175 - Quản lý cấp số"**, tạo đúng **8 tab**
với tên **chính xác từng chữ**:

```
QTVH   QTCS   QTKT   QTTH   Quy định   Hướng dẫn   Phác đồ   Hết hiệu lực
```

Mỗi tab, dòng 1 gõ đúng 9 tiêu đề cột, theo đúng thứ tự:

```
Stt | Loại tài liệu | Đơn vị biên soạn | MSTL | Tên tài liệu | Lần BH | Ngày hiệu lực | QĐ ban hành | Trạng thái
```

Riêng tab **"Hết hiệu lực"** có thêm cột thứ 10: `Loại tài liệu gốc`.

Tạo thêm 1 tab tên **"Nguyên tắc đặt mã số"** — copy nguyên bảng quy tắc mã số + bảng Tên
đơn vị/Mã đơn vị từ file Excel gốc sang (cột A-B là bảng mẫu mã, cột E-F là bảng đơn vị).

Tạo thêm 1 tab tên **"Dữ liệu chung"** để gộp cả 8 tab lại một chỗ (dùng cho trang tra cứu ở
Phần B). Dòng 1 gõ 9 tiêu đề cột như trên, ô **A2** dán công thức:

```
={QTVH!A2:I10000;QTCS!A2:I10000;QTKT!A2:I10000;QTTH!A2:I10000;
  'Quy định'!A2:I10000;'Hướng dẫn'!A2:I10000;'Phác đồ'!A2:I10000;
  'Hết hiệu lực'!A2:I10000}
```
(gõ liền 1 dòng; tên tab có dấu cách/dấu tiếng Việt cần để trong nháy đơn `' '`).

### A2. Tái tạo phần "tự động" bằng công thức Google Sheets (khuyến nghị)

**a) Đơn vị biên soạn — chọn từ danh sách, tránh gõ sai mã**
Chọn cột C (Đơn vị biên soạn) → menu **Dữ liệu (Data) → Xác thực dữ liệu (Data validation)**
→ Tiêu chí: **Danh sách từ một dải ô (List from a range)** → chọn dải mã đơn vị ở tab
"Nguyên tắc đặt mã số" (cột Mã đơn vị). Từ đó ô này sẽ hiện menu chọn thay vì gõ tay.

**b) Trạng thái — chọn "Còn hiệu lực" / "Hết hiệu lực"**
Chọn cột I (Trạng thái) → **Dữ liệu → Xác thực dữ liệu** → **Danh sách các mục**: nhập
`Còn hiệu lực, Hết hiệu lực`.

**c) Stt tự đánh số tăng dần (chỉ tính dòng có Tên tài liệu)**
Ở cột A (Stt), từ dòng 2 trở xuống, dùng công thức:
```
=IF($E2="","",COUNTA($E$2:$E2))
```
Kéo công thức xuống hết các dòng dự kiến dùng (ví dụ đến dòng 500). Dòng nào chưa có Tên
tài liệu sẽ để trống, dòng nào có tên sẽ tự đánh số tăng dần đúng thứ tự.

**d) Loại tài liệu tự điền theo tên tab**
Ở cột B, dòng 2:
```
=IF($E2="","","QTVH")
```
(thay `"QTVH"` bằng đúng tên loại tài liệu ứng với từng tab — QTCS, QTKT, QTTH, "Quy định",
"Hướng dẫn", "Phác đồ").

**e) MSTL tự gợi ý theo đúng quy tắc**
Bảng mẫu mã (nhắc lại từ tab "Nguyên tắc đặt mã số"):

| Tab | Công thức mẫu | Prefix | Chữ giữa |
|---|---|---|---|
| QTVH | QT-xx-Pyy | `QT-` | `P` |
| QTCS | QT-xx-Nyy | `QT-` | `N` |
| QTKT | QT-xx-Myy | `QT-` | `M` |
| QTTH | QT-xx-Syy | `QT-` | `S` |
| Quy định | QĐ-xx-yy | `QĐ-` | *(không có)* |
| Hướng dẫn | HD-xx-yy | `HD-` | *(không có)* |
| Phác đồ | PD-xx-yy | `PD-` | *(không có)* |

Ở cột D (MSTL), dòng 2, ví dụ cho tab **QTKT**:
```
=IF($E2="","","QT-"&$C2&"-M"&TEXT(COUNTIF($C$2:$C2,$C2),"00"))
```
Với tab **Quy định** (không có chữ giữa):
```
=IF($E2="","","QĐ-"&$C2&"-"&TEXT(COUNTIF($C$2:$C2,$C2),"00"))
```
Đổi `"M"`/`"QT-"` theo đúng bảng ở trên cho từng tab.

**⚠️ Lưu ý quan trọng khi dùng công thức MSTL/Stt:**
Công thức này đếm lại từ trên xuống mỗi khi trang tính thay đổi — nếu bạn **xóa hoặc chèn
dòng ở giữa** danh sách, các mã số/Stt phía dưới sẽ tự đổi theo, kể cả với những tài liệu đã
chính thức ban hành trước đó (không nên để một mã số đã cấp bị đổi). Để tránh rủi ro này:
- Chỉ **thêm dòng mới ở cuối** danh sách, không chèn xen giữa.
- Không xóa dòng — nếu tài liệu hết hiệu lực, chuyển "Trạng thái" thành "Hết hiệu lực" và
  cắt dòng đó sang tab "Hết hiệu lực" theo cách ở mục (f) dưới đây.
- Sau khi một dòng đã được **chính thức ban hành** (có số QĐ thật), nên "khóa" giá trị: chọn
  ô Stt và MSTL của dòng đó → Copy (Ctrl+C) → Paste Special → **Chỉ dán giá trị (Paste
  values only)** — để công thức không còn tính lại ô đó nữa.

**f) Chuyển tài liệu "Hết hiệu lực" sang tab riêng (thao tác tay)**
Khi một dòng đổi Trạng thái thành "Hết hiệu lực": chọn nguyên dòng đó → Cắt (Ctrl+X) → dán
vào dòng trống tiếp theo ở tab "Hết hiệu lực" → điền thêm cột "Loại tài liệu gốc" (tên tab
vừa cắt ra) → xóa dòng trống còn lại ở tab gốc (chuột phải → Delete row) để tránh lệch công
thức Stt phía dưới.

---

## PHẦN B — Sheet "Tra cứu" + trang `tracuu.html`

### B1. Tạo Sheet "Tra cứu"
Tạo một Google Sheet **khác file**, đặt tên ví dụ **"BVQY175 - Tra cứu"**, chỉ cần 1 tab
(đặt tên tùy ý, ví dụ "Tổng hợp"). Dòng 1 gõ đúng 9 tiêu đề cột như Phần A1. Ô **A2** dán:

```
=IMPORTRANGE("LINK_SHEET_QUAN_LY","Dữ liệu chung!A2:I10000")
```

(thay `LINK_SHEET_QUAN_LY` bằng link Sheet "Quản lý cấp số" ở Phần A1). Bấm Enter → bấm
**"Cho phép truy cập"** khi Google hỏi (chỉ 1 lần).

### B2. Publish tab "Tổng hợp" lên web
1. Mở Sheet "Tra cứu" → **File → Share → Publish to web**.
2. Đổi "Entire Document" sang đúng tên tab "Tổng hợp".
3. Định dạng: **Comma-separated values (.csv)** → **Publish** → xác nhận.
4. Copy link được tạo ra.

Dán link đó vào `tracuu.html` (mục CẤU HÌNH ở đầu phần `<script>`):
```js
const CSV_URL = 'https://docs.google.com/....output=csv';
```

---

## PHẦN C — Đưa `tracuu.html` lên GitHub → nối Cloudflare Pages

1. Tạo repository mới trên GitHub (Public) — nếu chị đã có repo `quanlytailieunoibo` từ
   trước, **xóa file `index.html`** khỏi repo đó (không cần dùng nữa, tránh nhầm lẫn), chỉ
   giữ lại `tracuu.html` và `README.md`.
2. **Add file → Upload files** → tải `tracuu.html` và `README.md` lên → **Commit changes**.
3. **dash.cloudflare.com → Workers & Pages → Create → Pages → Connect to Git** → chọn repo →
   Build settings: Framework **None**, Build command **để trống**, Output directory **/** →
   **Save and Deploy**.

Địa chỉ tra cứu: `https://<tên-dự-án>.pages.dev/tracuu.html` — gửi link này cho các đơn vị
khác. Mỗi khi bạn sửa `tracuu.html` trên GitHub, Cloudflare tự deploy lại; dữ liệu bên trong
thì tự đồng bộ liên tục qua `IMPORTRANGE`, không cần deploy lại khi chỉ đổi số liệu.

---

## Tổng kết quy trình làm việc hằng ngày

1. Mở Google Sheet "Quản lý cấp số" (web hoặc app điện thoại) như một bảng tính bình thường.
2. Thêm dòng mới ở cuối tab tương ứng → chọn Đơn vị (menu thả xuống) → Stt/Loại tài
   liệu/MSTL tự điền qua công thức → điền Tên tài liệu, Lần BH, Ngày hiệu lực, QĐ ban hành.
3. Tài liệu hết hiệu lực: đổi Trạng thái → cắt dòng sang tab "Hết hiệu lực" (Phần A2 mục f).
4. Sheet "Tra cứu" và trang `tracuu.html` tự cập nhật theo, không cần thao tác gì thêm.

## Giới hạn cần biết

- Không còn giao diện thêm/sửa "đẹp" như bản web app — thao tác trực tiếp trên Google Sheets
  giống thao tác Excel thông thường.
- Công thức MSTL/Stt nhạy với việc chèn/xóa dòng giữa danh sách — đọc kỹ lưu ý ⚠️ ở Phần A2.
- Trang tra cứu công khai theo link, không có mật khẩu thật sự (đúng cơ chế Publish to web
  của Google).
