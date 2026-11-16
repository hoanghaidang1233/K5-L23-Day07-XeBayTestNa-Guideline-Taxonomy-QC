# Guideline gán nhãn — v1.1 (nhóm XeBayTestNa, sau calibration)

- Phiên bản: **v1.1** · Ngày: 16/11/2026 · Dựa trên v1.0 + kết quả IAA (`submission/qc/iaa_summary.md`)
- Người biên tập: 102 · Người chốt: 101 · Phụ trách calibration: 104
- Loại thay đổi: **semantic** (đổi cách chấm thuộc tính, cách ôm box, cách dùng `ignore_region`) → phải sửa lại **cả lô 40 ảnh**
- Taxonomy: **giữ nguyên v1.0** (8 lớp, 3 thuộc tính) — không đổi lớp/thuộc tính, chỉ làm rõ cách chọn

> Phần **in đậm có nhãn "v1.1"** là chỗ thay đổi so với v1.0. Mọi chỗ khác giữ nguyên.

---

## 1. Mục tiêu

Nhãn dùng để dạy mô hình **phát hiện người và phương tiện** trên ảnh đường phố 2D.

- **Lỗi nặng nhất:** bỏ sót người hoặc xe thật, nhất là người đi xe máy và vật ở gần.
- **Lỗi nặng thứ hai:** gán vật giả (tượng, hình in, phản chiếu, đồ chơi) thành người hoặc xe.
- Khi phân vân, ưu tiên: **đúng lớp → đúng vật → đủ vật**.

---

## 2. Lớp và thuộc tính

### 2.1. Lớp (mỗi box đúng một lớp)

| Lớp | Là gì | Không phải / dễ nhầm |
|---|---|---|
| `car` | Ô tô chở người, **nóc thấp**, tối đa 9 chỗ: sedan, hatchback, SUV, taxi, minivan gia đình | `van` (thân hộp, nóc cao); xe đồ chơi, xe trong gương |
| `van` | Xe **thân hộp liền khối, nóc cao**, khoang sau liền cabin: van chở hàng, xe 10–16 chỗ, xe cứu thương | `car`; `truck` |
| `truck` | Xe chở hàng có **cabin tách riêng thùng/sàn hàng**: xe tải, đầu kéo, **xe bán tải (pickup)** | `van` |
| `bus` | Xe chở khách **cỡ lớn**, nhiều hàng ghế, nhiều cửa sổ dọc thân | `van`; `truck` |
| `motorcycle` | Xe máy, mô tô 2 bánh có động cơ, kể cả sidecar | Xe đạp, xích lô (không gán); `tuk_tuk` |
| `tuk_tuk` | Xe 3 bánh có động cơ, có khoang/mái che liền khối | Sidecar (`motorcycle`) |
| `person` | **Người thật**: đi bộ, lái xe, ngồi trên xe, đạp xe | Tượng, ma-nơ-canh, linh vật, hình in, phản chiếu |
| `ignore_region` | **Vùng** chứa người/xe thật không gán được từng vật (R-08) | Vật đơn lẻ còn tách được → gán lớp cụ thể |

**v1.1 — Phép thử ranh giới lớp (R-13):** xem mục 3.

### 2.2. Thuộc tính (mọi lớp trừ `ignore_region`) — **v1.1: bắt buộc chọn, cách đo cụ thể (R-12)**

| Thuộc tính | Giá trị | Cách chọn |
|---|---|---|
| `occluded_level` | `0` | Bị che 0–10% (gần như thấy trọn vật) |
| | `1` | Bị che 11–40% (che **dưới một nửa**: tối đa khoảng 1–1,5 phần trên 4) |
| | `2` | Bị che 41–70% (che **khoảng một nửa đến 3/4**) |
| | `3` | Bị che > 70% nhưng vẫn nhận ra chắc chắn lớp |
| `truncated` | `yes` | Box **chạm mép ảnh** và vật rõ ràng tiếp tục ra ngoài khung |
| | `no` | Vật nằm trọn trong ảnh |
| `can_review` | tick | Đã vẽ nhưng không chắc lớp, ranh giới hoặc có nên gán không |

**v1.1 — Cách ước lượng % che (thống nhất cho cả nhóm):**
1. Hình dung **toàn bộ vật** (cả phần bị che), chia thành **4 phần bằng nhau** (người: đầu-vai / thân / đùi / cẳng chân; xe: chia dọc thân xe).
2. Đếm số phần **bị vật khác che** (không tính phần bị mép ảnh cắt — phần đó là `truncated`).
3. 0 phần → `0` · tới 1,5 phần → `1` · tới gần 3 phần → `2` · hơn 3 phần → `3`.
4. **Người ngồi trên xe máy:** phần chân bị thân xe che → tính là bị che. **Xe máy có người ngồi:** phần thân xe bị người che → tính là bị che.

> **v1.1:** Không được để giá trị mặc định cho cả ảnh. Mỗi box phải được nhìn và chọn `occluded_level`, `truncated`. IAA v1.0 cho thấy có người để toàn bộ box ở `0`/`no` → coi là lỗi.

---

## 3. Quy tắc vẽ box

| Mã | Quy tắc |
|---|---|
| **R-01** | Box ôm sát **phần nhìn thấy**, không đoán phần bị che. **v1.1 — box gồm / không gồm:** *Gồm* gương chiếu hậu, tay lái, bánh xe, tay/chân/đầu người nhìn thấy, mũ bảo hiểm, ba lô đeo trên người. *Không gồm* hàng chở nhô ra, thanh dài, bóng đổ, vệt phản chiếu dưới đường. Mép box chạm đúng pixel ngoài cùng của vật, không chừa viền. |
| **R-02** | **Mỗi vật một box**, kể cả khi đứng sát hoặc chồng nhau. |
| **R-03** | Vật bị che nặng: không còn nhận ra lớp → **không vẽ**; vẫn nhận ra → vẽ và chọn `occluded_level`. |
| **R-04** | Vật bị mép ảnh cắt: box dừng đúng mép ảnh, `truncated = yes`. |
| **R-05** | **Người trên xe → box riêng cho từng người và một box cho xe.** Box người chỉ bao người; box xe bao toàn bộ xe nhìn thấy. **v1.1:** hai box được phép chồng nhau nhiều; không thu nhỏ box xe để tránh box người. |
| **R-06** | Xe chở xe / chở hàng: box xe chở ôm thân xe (cabin + thùng), **không bao hàng nhô ra**. Xe thật được chở → box riêng. Hàng không thuộc lớp nào → không gán. |
| **R-07** | **Vật không thật → không gán, không vẽ `ignore_region`:** tượng, ma-nơ-canh, linh vật, xe đồ chơi/mô hình, hình in/biển quảng cáo, **ảnh phản chiếu trong gương/kính/nước** (v1.1: gộp R-12 cũ vào đây). |
| **R-08** | **v1.1 — `ignore_region`** chỉ dùng cho vật **thật** khi: (a) vật cao **< 20 px**, hoặc (b) **cụm dày đặc**: từ **3 vật trở lên** chồng lên nhau mà **không xác định được mép** của từng vật. Vùng ignore **ôm sát cụm đó**, **không phủ lên** vật đã tách được (vật cao ≥ 20 px, thấy rõ mép → gán riêng dù nằm cạnh cụm). Không dùng ignore chỉ vì nhiều vật hoặc tốn thời gian; không vẽ một vùng ignore phủ cả nửa ảnh. |
| **R-09** | Xe chủ (mui, tay lái, gương của xe đang chở camera) → không gán. |
| **R-10** | Ảnh mờ / tối: còn nhận ra lớp và ranh giới → gán; không nhận ra → không gán; phân vân → gán + `can_review`. **v1.1:** vật ở xa cao **≥ 20 px** và nhận ra lớp **phải gán** (lỗi bỏ sót nhiều nhất ở v1.0 là `person`, `motorcycle` ở xa). |
| **R-11** | Ca chưa có quy tắc: đã vẽ → `can_review`; chưa vẽ được → ghi `image_issues.csv`. Không đổi guideline giữa lúc gán. |
| **R-12** | **v1.1 (mới) — Thuộc tính:** chọn `occluded_level` theo cách đếm 4 phần ở mục 2.2; `truncated = yes` chỉ khi box chạm mép ảnh. Bắt buộc chọn cho từng box. |
| **R-13** | **v1.1 (mới) — Phép thử ranh giới lớp** (dùng đặc điểm nhìn thấy, không đoán số chỗ): **car ↔ van:** nóc xe cao hơn hẳn đỉnh kính trước *và* đuôi xe gần thẳng đứng → `van`, ngược lại `car`. **van ↔ truck:** có khe/khoảng tách giữa cabin và thùng → `truck`. **pickup** luôn là `truck`. **truck ↔ bus:** có hàng cửa sổ hành khách dọc thân → `bus`. Vẫn không chắc → chọn theo phép thử và tick `can_review`. |
| **R-14** | **v1.1 (mới) — Box người và xe máy chồng nhau:** gán lớp theo **vật ở tâm box**: box bao đầu → thân người → `person`; box bao bánh trước → bánh sau → `motorcycle`. Không dùng một box cho cả người và xe. |

---

## 4. Ca khó

Giữ nguyên bảng 4.1 của v1.0 (img_001 → img_038). Bổ sung từ IAA v1.0:

| Ảnh | Vấn đề IAA | Quyết định v1.1 | Rule |
|---|---|---|---|
| img_028 | car ↔ truck (xe bán tải) | `truck` — quy tắc đã có ở v1.0 | 2.1, R-13 |
| img_009 | bus ↔ truck | Theo phép thử cửa sổ hành khách | R-13 |
| img_015, img_016 | car ↔ van ↔ truck | Theo phép thử nóc/đuôi và khe cabin–thùng | R-13 |
| img_010, img_034 | person ↔ motorcycle (box chồng nhau) | Mỗi người một `person`, mỗi xe một `motorcycle`, lớp theo tâm box | R-05, R-14 |
| img_003, img_021, img_030, img_039 | Bất đồng `ignore_region` và bỏ sót nhiều | Tách từng vật ≥ 20 px rõ mép; chỉ cụm ≥ 3 vật không thấy mép mới là ignore | R-08, R-10 |
| img_007, img_008 | Mức che (ca mở ở v1.0) | Đếm 4 phần theo mục 2.2 | R-12 |
| img_005, img_014 | car/van; ảnh nhòe (ca mở ở v1.0) | Theo R-13 và R-10; nhóm chốt khi sửa ảnh, ghi vào `phieu_sua` | R-13, R-10 |

---

## 5. QC và phiên bản

- Ngưỡng của lab (không đổi): IoU từng box ≥ 0.85 (box ≥ 50 px) / ≥ 0.7 (20–50 px); IAA: IoU TB ≥ 0.85, cùng lớp ≥ 95%; ảnh lỗi ≤ 2% mới Release.
- Luôn ghi số liệu dạng **tử số / mẫu số**.
- Người chốt cuối: 101 sau khi nhóm thảo luận. Mỗi ảnh sửa xong phải được **một người khác** kiểm lại.

### Thay đổi v1.0 → v1.1 và bằng chứng IAA

| Thay đổi | Bằng chứng (IAA v1.0, 518 cặp box ghép) | Rule |
|---|---|---|
| Cách đếm 4 phần cho `occluded_level`; bắt buộc chọn thuộc tính | Cùng `occluded_level` chỉ 53.1%; 276 ca `sai_thuoc_tinh`; một người để `0`/`no` cho toàn bộ box | 2.2, R-12 |
| Định nghĩa cụm dày đặc, ignore không phủ vật tách được | 222 ca `bat_dong_ignore`; số vùng ignore mỗi người 7–27 | R-08 |
| Liệt kê phần thuộc / không thuộc box | IoU TB 0.815 < 0.85; 265 ca `iou_thap` rải đều 5 người | R-01, R-05 |
| Vật xa ≥ 20 px phải gán | 216 ca `bo_sot_thua`, chủ yếu `person` (107), `motorcycle` (71) | R-10 |
| Phép thử ranh giới lớp xe; box người/xe chồng nhau | 14 ca `sai_lop` (car/van/truck/bus, person/motorcycle) | R-13, R-14 |
| Gộp "phản chiếu qua gương" vào R-07 | R-12 cũ trùng R-07 | R-07 |

### Lịch sử phiên bản

| Phiên bản | Thay đổi | Lý do (ca nào) |
|---|---|---|
| v0 | Bản khởi đầu của giảng viên | — |
| v1.0 | Thêm `bus`, `tuk_tuk`; định nghĩa car/van/truck; ngưỡng che 10/40/70; R-01 → R-11 | Tổng hợp 5 bản cá nhân |
| v1.1 | Cách đo `occluded_level` + bắt buộc thuộc tính (R-12); R-08 cụm dày đặc; R-01 phạm vi box; R-10 vật xa; R-13 ranh giới lớp; R-14 box người/xe | IAA v1.0: 1003 ca trên 35/40 ảnh (`calibration_decisions.csv`) |
