# Guideline gán nhãn — v1.0 (nhóm XeBayTestNa)

- Phiên bản: **v1.0** · Ngày: 16/11/2026
- Tổng hợp từ 5 bản cá nhân: 101, 102, 103, 104, 105
- Người biên tập: 102 · Người duyệt: 101
- Trạng thái: **BẢN NHÁP — chờ cả nhóm xác nhận ở buổi họp Pha 2.** Sau khi chốt, khóa bản này cho toàn bộ Pha 3.

---

## 1. Mục tiêu

Nhãn dùng để dạy mô hình **phát hiện người và phương tiện** trên ảnh đường phố 2D (camera giao thông, xe tự lái).

- **Lỗi nặng nhất:** bỏ sót người hoặc xe thật, nhất là người đi xe máy và vật ở gần.
- **Lỗi nặng thứ hai:** gán vật giả (tượng, hình in, phản chiếu, đồ chơi) thành người hoặc xe.
- Khi phân vân, ưu tiên: **đúng lớp → đúng vật → đủ vật**.

---

## 2. Lớp và thuộc tính

### 2.1. Lớp (mỗi box đúng một lớp)

| Lớp | Là gì | Ví dụ | Không phải / dễ nhầm |
|---|---|---|---|
| `car` | Ô tô chở người, **nóc thấp**, tối đa 9 chỗ: sedan, hatchback, SUV, taxi, minivan gia đình | img_023 (sedan đen), img_018 (bãi ô tô), img_019 (xe diễu hành) | `van` (thân hộp, nóc cao); xe đồ chơi img_024, img_038; xe trong gương img_020 |
| `van` | Xe **thân hộp liền khối, nóc cao** rõ hơn ô tô con, khoang sau liền cabin: van chở hàng, xe 10–16 chỗ, xe cứu thương | img_002 (van ở xa), img_005, img_015 | `car` (nóc thấp); `truck` (cabin tách thùng) |
| `truck` | Xe chở hàng có **cabin tách riêng thùng/sàn hàng**: xe tải, xe đầu kéo, **xe bán tải (pickup)** | img_001, img_002 (xe chở ô tô), img_007, img_028 (pickup) | `van` (thân liền khối) |
| `bus` | Xe chở khách **cỡ lớn**, nhiều hàng ghế, nhiều cửa sổ dọc thân | img_031 | `van` 16 chỗ trở xuống; `truck` |
| `motorcycle` | Xe máy, mô tô 2 bánh có động cơ, **kể cả xe mô tô gắn thùng bên (sidecar)** | img_003, img_008, img_021, img_022 (sidecar) | Xe đạp, xích lô (không gán xe); `tuk_tuk` |
| `tuk_tuk` | Xe **3 bánh có động cơ**, có khoang/mái che chở khách hoặc hàng liền khối | img_004 | Mô tô sidecar (`motorcycle`) |
| `person` | **Người thật**: đi bộ, lái xe, ngồi trên xe, đạp xe | img_001, img_023 (người đạp xích lô), img_026 | Tượng img_017; mặt người trên biển quảng cáo img_018; linh vật thú bông img_019; ma-nơ-canh |
| `ignore_region` | **Vùng** chứa người/xe thật nhưng không thể gán từng vật (xem R-08) | img_019 (khán giả ở xa), img_021 (xe ken đặc giữa ngã tư) | Một xe đơn lẻ bị che nhưng còn nhận ra → gán lớp cụ thể |

**Vật không thuộc lớp nào** (xe đạp, xích lô, xe ngựa, vật chở trên xe…): **không vẽ box cho vật đó.** Người trên vật đó vẫn gán `person`.

### 2.2. Thuộc tính (cho mọi lớp trừ `ignore_region`)

| Thuộc tính | Giá trị | Cách chọn |
|---|---|---|
| `occluded_level` | `0` | Bị che 0–10% |
| | `1` | Bị che 11–40% |
| | `2` | Bị che 41–70% |
| | `3` | Bị che > 70% **nhưng vẫn nhận ra chắc chắn là lớp gì** |
| `truncated` | `yes` | Vật bị **mép ảnh cắt mất bất kỳ phần nào** |
| | `no` | Vật nằm trọn trong ảnh |
| `can_review` | tick | Đã vẽ box nhưng **không chắc** lớp, ranh giới hoặc có nên gán không |

> Ước lượng % che theo **diện tích vật**, không theo diện tích box. Không dùng cờ `occluded` có sẵn của CVAT (phím `Q`).

---

## 3. Quy tắc vẽ box

| Mã | Quy tắc |
|---|---|
| **R-01** | Box ôm sát **phần nhìn thấy**. Không đoán phần bị che, không kéo box theo vật đang mang (thanh dài, bao tải, đồ chở). |
| **R-02** | **Mỗi vật một box**, kể cả khi đứng sát hoặc chồng nhau. Không gộp nhiều người/xe vào một box. |
| **R-03** | Vật bị che nặng: nếu **không còn nhận ra lớp** thì **không vẽ**. Nếu vẫn nhận ra thì vẽ và chọn `occluded_level`. |
| **R-04** | Vật bị mép ảnh cắt: box dừng đúng mép ảnh, đặt `truncated = yes`. |
| **R-05** | **Người trên xe → box riêng cho từng người và một box cho xe.** 1 xe máy chở 2 người = 2 `person` + 1 `motorcycle`. Box người chỉ bao người; box xe bao toàn bộ xe nhìn thấy. |
| **R-06** | **Xe chở xe / chở hàng:** box xe chở ôm thân xe (cabin + thùng/sàn), **không bao hàng nhô ra**. Xe thật được chở (ô tô trên xe chở ô tô) → gán **box riêng** đúng lớp. Hàng không thuộc lớp nào (ngựa, bao tải) → không gán. |
| **R-07** | **Vật không thật → không gán, không vẽ `ignore_region`:** tượng, ma-nơ-canh, linh vật, xe đồ chơi/mô hình, người hoặc xe trên biển quảng cáo/hình in, ảnh phản chiếu trong gương/kính/nước. |
| **R-08** | **`ignore_region`** chỉ dùng cho vật **thật** khi: (a) vật cao **< 20 px**, hoặc (b) một cụm dày đặc **không tách được ranh giới từng vật**. Vật nào trong ảnh còn tách được và cao ≥ 20 px thì vẫn gán riêng. Không dùng `ignore_region` chỉ vì tốn thời gian. |
| **R-09** | **Xe chủ** (mui, tay lái, gương, thân xe đang chở camera) → không gán. |
| **R-10** | **Ảnh mờ / tối:** còn nhận ra lớp và ranh giới → gán bình thường; không nhận ra lớp → không gán; phân vân → gán và tick `can_review`. |
| **R-11** | **Ca chưa có quy tắc:** đã vẽ box → tick `can_review`; chưa vẽ được box → ghi `ảnh, mã người gán, vùng, lý do` để đưa vào `image_issues.csv`. **Không tự đoán, không đổi guideline giữa Pha 3.** |

---

## 4. Ca khó

### 4.1. Ca áp dụng quy tắc

| Ảnh | Tình huống | Quyết định | Rule | Nguồn |
|---|---|---|---|---|
| img_001 | 2 người chồng nhau | 2 box `person` riêng; người không còn nhận ra thì bỏ | R-02, R-03 | 101 |
| img_002 | Xe tải chở ô tô 2 tầng; van ở xa | 1 `truck` + 1 `car` cho **từng** ô tô trên sàn; gán cả van ở xa | R-06, R-02 | 101 |
| img_003 | Xe máy và người rất dày | Gán từng người/xe tách được; phần ken đặc không tách được → `ignore_region` | R-05, R-08 | 101 |
| img_004 | Tuk-tuk có người ngồi trong | 1 `tuk_tuk` + `person` cho từng người nhìn thấy | R-05 | 101 |
| img_010 | Xe đạp chở rau | Không gán xe đạp; người đạp → `person` | 2.1, R-05 | 102 |
| img_014 | Người đi xe máy bị nhòe | Theo R-10; **xem lại trên ảnh khi họp** (102 đề xuất không gán) | R-10 | 102 |
| img_015 | Xe bị che một phần phía sau | `van`, chọn mức che theo bảng 2.2 | R-03 | 102 |
| img_017 | Tượng kỵ sĩ bằng đồng | Không gán | R-07 | 103 |
| img_018 | Mặt người trên biển quảng cáo; ô tô sau hàng rào | Biển quảng cáo: không gán. Mỗi ô tô một `car`, chọn `occluded_level` theo % che | R-07, R-02 | 103 |
| img_019 | Xe diễu hành có tài xế và linh vật; khán giả ở xa | `car` + `person` (tài xế); linh vật không gán; khán giả không tách được → `ignore_region` | R-07, R-08 | 103 |
| img_020 | Xe trong gương chiếu hậu; thân xe chủ | Không gán cả hai | R-07, R-09 | 103 |
| img_021 | Ngã tư đông xe máy | Gần: tách `person` + `motorcycle`; xa ken đặc → `ignore_region` | R-05, R-08 | 103 |
| img_022 | Mô tô có thùng bên (sidecar) | 1 `motorcycle` bao cả xe và thùng + `person` người lái | 2.1, R-05 | 103 |
| img_023 | Xích lô chở người; ô tô ngược chiều | Không gán xích lô; `person` cho người đạp và người ngồi; `car` cho ô tô | 2.1, R-05 | 103 |
| img_024 | Xe mô hình trên nóc ô tô thật | `car` cho ô tô thật, box **không** bao xe mô hình | R-06, R-07 | 103 |
| img_025 | Người rất nhỏ ở xa | Đo chiều cao: < 20 px → `ignore_region`; ≥ 20 px và nhận ra → `person` | R-08 | 104 |
| img_026 | 2 người trên 1 xe máy, xe bị mép dưới cắt | 2 `person` + 1 `motorcycle`; xe `truncated = yes` | R-04, R-05 | 104 |
| img_027 | Tượng hình xe bán tải trên bệ | Không gán | R-07 | 104 |
| img_028 | Pickup chở vật hình ngựa | `truck`, box chỉ ôm xe, không bao vật hình ngựa | 2.1, R-06 | 104 |
| img_029 | Xe máy và xe đạp chở thanh dài | `person` + `motorcycle`; xe đạp không gán; box không kéo theo thanh dài | R-01, R-05 | 104 |
| img_030 | Bãi xe máy đông, chồng lấn | Gán riêng từng xe tách được; phần không tách được → `ignore_region` | R-02, R-08 | 104 |
| img_031 | Cảnh đêm, đường ướt, có xe buýt | Xe buýt → `bus`; vệt sáng phản chiếu không gán; vật quá tối theo R-10 | R-07, R-10 | 104 |
| img_032 | Hàng người đứng sát nhau ở trạm chờ | Mỗi người tách được một `person`; không gộp cả hàng | R-02, R-08 | 104 |
| img_038 | Xe đồ chơi | Không gán | R-07 | 105 |

### 4.2. Ca cần xem lại trong buổi họp (chưa chốt)

| Ảnh | Câu hỏi |
|---|---|
| img_005, img_015 | Ranh giới `car` / `van`: xe đó có "nóc cao, thân hộp" không? Cùng xem trên ảnh để thống nhất. |
| img_007, img_008 | 101 chưa chắc mức che → cả nhóm cùng ước lượng % che để làm ví dụ mẫu cho bảng 2.2. |
| img_014 | Ảnh nhòe: còn nhận ra người và xe máy không? (102 đề xuất không gán) |

---

## 5. QC và phiên bản

- **Ngưỡng của lab** (không đổi): IoU từng box ≥ 0.85 nếu box cao ≥ 50 px, ≥ 0.7 nếu cao 20–50 px; IAA: IoU trung bình ≥ 0.85, cùng lớp ≥ 95%; ảnh lỗi ≤ 2% mới Release.
- Luôn ghi số liệu dạng **tử số / mẫu số**.
- Ca mơ hồ trong Pha 3: tick `can_review` hoặc ghi `image_issues.csv` → chốt ở calibration (Pha 5). **Người chốt cuối:** 101 (điều phối), sau khi nhóm thảo luận.
- Mọi thay đổi làm đổi nhãn → tăng phiên bản (v1.1…) và ghi vào bảng dưới.

### Các bất đồng giữa 5 bản cá nhân và cách chốt

| # | Vấn đề | Ý kiến các bản | Chốt v1.0 | Lý do |
|---|---|---|---|---|
| 1 | Thêm lớp mới? | 101: `rider`, `tuk_tuk` · 102: `bus`, `bicycle`, `tuktuk` · 103: không thêm, bus ngoài phạm vi · 104: không ép bus/xe đạp vào lớp gần | Thêm **`bus`, `tuk_tuk`**. Không thêm `rider`, `bicycle` | Bus và tuk-tuk là phương tiện cơ giới lớn, mô hình cần nhận ra; ép vào `truck`/`motorcycle` làm sai hình dạng lớp. `rider` trùng thông tin với `person` + box xe; xe đạp/xích lô hiếm và người trên đó đã được gán `person` |
| 2 | Pickup | 105: `car` · 102, 103, 104: `truck` | **`truck`** | Đa số; pickup có thùng hàng tách cabin, khớp định nghĩa `truck` |
| 3 | Ranh giới `car` / `van` | 101: van cao hơn ô tô · 102: minivan → van · 103: van 9–16 chỗ, SUV 7 chỗ → car | Van = **thân hộp, nóc cao**; minivan gia đình nóc thấp → `car` | Dùng đặc điểm nhìn được trên ảnh thay vì số chỗ (không đếm được ghế) |
| 4 | Ngưỡng `occluded_level` | 101: 10/35/60/80 · 103: 0/35/75 · 105: 0/50/80 · 102, 104: chưa có ngưỡng | **10 / 40 / 70**; > 70% vẫn vẽ nếu nhận ra lớp | Mốc tròn dễ ước lượng; quyết định "có vẽ không" dựa vào nhận ra lớp thay vì một con số % khó đo ở mức che rất nặng |
| 5 | `truncated` | 101: mất ≥ 15% · 103, 105: bị cắt bất kỳ | **Bị cắt bất kỳ → `yes`** | Đa số; dễ kiểm (box chạm mép ảnh), không cần ước lượng % |
| 6 | Box xe chở có bao hàng? | 101: bao cả hàng · 103, 104: không bao vật chở | **Không bao hàng nhô ra**; xe thật được chở gán box riêng | Hàng chở không phải phương tiện; box to theo hàng làm sai kích thước xe |
| 7 | Vật không thật | 101: chưa chốt có vẽ `ignore_region` không · 102, 103, 104, 105: không gán | **Không gán, không vẽ `ignore_region`** | `ignore_region` dành cho vật thật không gán được; vật giả không phải mục tiêu |
| 8 | Khi nào dùng `ignore_region` | 101: < 20 px hoặc ≥ 5 vật dính chùm · 103: < 20 px hoặc không tách được · 104: chỉ khi thật sự không tách được | **< 20 px hoặc không tách được ranh giới** | Bỏ con số "5 vật" vì không phản ánh việc có tách được hay không |
| 9 | Ảnh mờ / tối | 102: không gán · 104: review | **Theo khả năng nhận ra lớp** (R-10) | Một quy tắc chung cho mọi ảnh, phân vân thì `can_review` |
| 10 | Sidecar | 103: `motorcycle` · 101: xe 3 bánh dễ nhầm | **Sidecar → `motorcycle`**, xe 3 bánh có khoang → `tuk_tuk` | Sidecar là xe máy 2 bánh gắn thêm thùng |

### Lịch sử phiên bản

| Phiên bản | Thay đổi | Lý do (ca nào) |
|---|---|---|
| v0 | Bản khởi đầu của giảng viên | — |
| v1.0 | Thêm lớp `bus`, `tuk_tuk`; định nghĩa lại `car` / `van` / `truck` (pickup → truck); ngưỡng `occluded_level` 10/40/70; `truncated` = bị cắt bất kỳ; quy tắc R-01 → R-11 | Tổng hợp 5 bản cá nhân; ca img_001–008, 010, 014–015, 017–032, 038 |
