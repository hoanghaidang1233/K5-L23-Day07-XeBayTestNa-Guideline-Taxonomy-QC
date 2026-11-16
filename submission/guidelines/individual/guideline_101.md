# Guideline gán nhãn — v1.0 — 101

Bản cá nhân Pha 1 · 16/11/2026 · Bounding box trên CVAT, xuất COCO 1.0

- Phiên bản: v1.0
- Ngày: 16/11/2026
- Người viết / duyệt: 101
## 1. Mục tiêu

Dạy mô hình **tìm người và xe** trên ảnh đường phố.

- Lỗi nặng nhất: **bỏ sót** người/xe thật, hoặc gán **vật giả** (tượng, hình in) thành người/xe.
- Khi gán, ưu tiên: **đúng lớp → đúng vật → đủ vật**.

## 2. Lớp và thuộc tính

### Lớp

| Lớp | Là gì | Đừng nhầm với |
|---|---|---|
| `car` | Ô tô con (sedan, SUV, taxi) | `van` |
| `van` | Xe dạng hộp, thùng kín, cao hơn ô tô con | `truck` |
| `truck` | Xe có **cabin tách riêng thùng hàng** | `van` |
| `motorcycle` | Xe máy 2 bánh | Xe đạp, xe 3 bánh |
| `person` | **Người thật** (đi bộ, lái xe, ngồi trên xe) | Tượng, ma-nơ-canh, người trong hình in |
| `ignore_region` | **Vùng** quá dày hoặc quá nhỏ, không gán từng vật | — |

Vật không thuộc lớp nào: **không vẽ**, ghi lại để hỏi nhóm.

*Đề xuất (cần họp):* thêm `rider` (người trên xe) và `tuk_tuk` (xe 3 bánh chở khách).

### Thuộc tính

| Thuộc tính | Cách chọn |
|---|---|
| `occluded_level` (bị che) | `0`: < 10% · `1`: 10–35% · `2`: 35–60% · `3`: 60–80% · **> 80%: không vẽ** |
| `truncated` (bị mép ảnh cắt) | `yes` nếu mất ≥ 15% vật, còn lại `no` |
| `can_review` | Tick khi **không chắc** |

## 3. Quy tắc vẽ box

- **R-01** Box ôm sát **phần nhìn thấy**, không đoán phần bị che.
- **R-02** Mỗi vật một box, kể cả khi chồng nhau.
- **R-03** Bị che > 80% → không vẽ.
- **R-04** Bị mép ảnh cắt → box dừng ở mép, đặt `truncated`.
- **R-05** Người trên xe → **2 box**: một người, một xe.
- **R-06** Xe chở xe → box xe chở (bao cả hàng) + box từng xe được chở.
- **R-07** Vật cao < 20 px hoặc ≥ 5 vật dính chùm → một `ignore_region`.
- **R-08** Tượng, ma-nơ-canh, hình in, phản chiếu → **không gán**.
- **R-09** Xe đang quay (mui, tay lái) → không gán.
- **R-10** Vật ở xa nhưng cao ≥ 20 px → vẫn phải gán.

## 4. Ca khó

| Ảnh | Tình huống | Quyết định | Rule |
|---|---|---|---|
| img_001 | 2 người chồng nhau | 2 box riêng; ai bị che > 80% thì bỏ | R-02, R-03 |
| img_002 | Xe tải chở xe; van ở xa | Box xe tải + box từng xe trên thùng; gán cả van xa | R-06, R-10 |
| img_003 | Xe máy và người rất dày | Gán vật rõ; vùng quá dày dùng `ignore_region` | R-05, R-07 |
| img_004 | Tuk-tuk có người ngồi | Box xe + box người; lớp của tuk-tuk **cần họp** | R-05 |
| img_005 | Van và ô tô cạnh nhau | Phân lớp theo bảng ở mục 2 | — |
| img_006 | Nhiều vật nhỏ, bị khuất | Chỉ vẽ phần thấy; < 20 px dùng `ignore_region` | R-01, R-07 |
| img_007 | Xe tải, khó đoán mức che | Gán `truck`, tick `can_review` | R-03 |
| img_008 | Xe máy + người, khó đoán mức che | Gán `motorcycle` + `person`, tick `can_review` | R-05 |

*Còn bổ sung img_009 → img_040.*

**Mang ra họp:**
1. Có thêm `rider`, `tuk_tuk` không?
2. Người lái và người ngồi sau: cùng lớp?
3. Vật giả: không gán hay vẽ `ignore_region`?

## 5. QC và phiên bản

- Đạt khi: IoU ≥ 0.85 (box ≥ 50 px) hoặc ≥ 0.7 (box 20–50 px), cùng lớp ≥ 95%, ảnh lỗi ≤ 2%.
- Ca không chắc: tick `can_review` → nhóm chốt khi calibration.
- Đổi quy tắc → tăng phiên bản.

| Phiên bản | Thay đổi | Từ ca |
|---|---|---|
| v0 | Bản gốc | — |
| v0.1-101 | Định nghĩa lớp, ngưỡng che/cắt, R-01 → R-10, ca khó | img_001 → img_008 |
