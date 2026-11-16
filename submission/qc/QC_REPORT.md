# Báo cáo QC — XeBayTestNa

## 1. Mục đích, dữ liệu và phiên bản

- Mục đích và giới hạn sử dụng: lô 40 ảnh đường phố gán bounding box 2D (8 lớp người/xe + `ignore_region`) để đặc tả guideline và chạy quy trình QC. Lô nhỏ, ảnh chọn từ Wikimedia Commons, thiên về cảnh đường phố Đông Nam Á, nên **không đại diện** cho phân bố thực tế (thời tiết, ban đêm, góc camera xe); chỉ dùng cho học tập / thử nghiệm, không dùng để đánh giá mô hình sản phẩm.
- Nguồn và phép sử dụng dữ liệu: ảnh lấy từ Wikimedia Commons, ghi công từng ảnh (tên gốc, tác giả, giấy phép CC BY / CC BY-SA / CC0 / public domain, link) trong `data/ATTRIBUTION.csv`. Dùng cho mục đích học tập; khi chia sẻ lại phải giữ ghi công và điều kiện giấy phép (ảnh CC BY-SA: bản phái sinh phải cùng giấy phép).
- Manifest và checksum nhãn cuối: `submission/qc/batch_manifest.json`; nhãn cuối `labels_calibrated.json` SHA-256 `48e02870f8843a7be0e51bd5b3275be013ce824423d06c5259753d5e7aa83776` (40 ảnh, 318 annotation).
- Guideline và taxonomy khi gán / khi bàn giao: gán theo guideline v1.0 và taxonomy v1.0 (8 lớp); bàn giao theo guideline v1.1 (`guideline_v1.1_calibrated.md`, thay đổi semantic), taxonomy giữ v1.0.
- Sửa script (theo RULES.md): sửa `scripts/make_cvat_labels.py` để CVAT bản mới nhận cấu hình nhãn: (1) checkbox `can_review` xuất `values: []` gây lỗi "attribute values must be a non-empty array" → đổi thành `["false"]`; (2) thiếu trường `type` gây lỗi "unknown label type undefined" → thêm `"type": "rectangle"`. Taxonomy không đổi; unit test 19/20 (test còn lại lỗi do quyền tạo symlink trên Windows).

## 2. Phân công và đồng thuận trước calibration

- Lượt hoàn thành / được giao: **120/120** (5 người × 24 ảnh, K=3, 40/40 ảnh có ≥ 2 người gán).
- Số cặp box ghép, IoU TB, đồng thuận lớp và mẫu số: mẫu số **518 cặp box ghép** (IoU ≥ 0.5, ghép 1-1 không phân biệt lớp, cộng trên mọi cặp người gán × ảnh chung). IoU TB **0.815** (ngưỡng 0.85 → **CHƯA ĐẠT**); cùng lớp **97.3%** (ngưỡng 95% → đạt); cùng `occluded_level` 53.1%; cùng `truncated` 87.5%. Chỉ 2/10 cặp đạt (103|104: 0.856, 103|105: 0.860); thấp nhất 101|105: 0.767.
- Box chưa ghép, loại do ignore, bất đồng vùng ignore: chưa ghép **194/142** (A/B) — không nằm trong mẫu số IoU/lớp; **26** box loại khỏi so sánh vì nằm trong `ignore_region`; **222** ca bất đồng vùng ignore. Tổng **1003 ca bất thường trên 35/40 ảnh** (`calibration_candidates.csv`). Ảnh nặng nhất: img_003 (92 ca), img_021 (72), img_034 (71), img_039 (69), img_008 (60).
- Ca hai người có thể cùng sai sau khi đối chiếu tham chiếu: **img_031** (gán bởi 101, 102, 103) và **img_035** (101, 102, 103) — cả 3 người cùng **không vẽ `person` cho người lái** trên xe máy (img_031 #240, #246, #235) và trên tuk-tuk (img_035 #276), trái với R-05. Vì không ai vẽ nên không có cặp box để so → IAA không báo lỗi. Cách kiểm: QC bằng mắt vòng 1 (105) ghi `bo_sot` R-05; đối chiếu lại `labels/101–103.json`: số box `person` chồng lên các xe này = 0 ở cả 3 người. Cùng kiểu với img_005, 016, 021 (thiếu xe hoặc người lái). Nếu coach đưa ca tham chiếu khác, nhóm bổ sung vào dòng này.

### Phân tích: sửa guideline hay sửa người gán

| Loại ca | Số ca | Dấu hiệu | Kết luận |
|---|---|---|---|
| `sai_thuoc_tinh` — `occluded_level` | phần lớn 276 | 101, 102 hay chấm 1–3 (101 có 47 box mức 2, 24 box mức 3); 103, 104 nghiêng về 0–1; **105 để 0 cho toàn bộ 106 box** | Hai vấn đề: (a) nhiều người ước lượng % che khác nhau → **guideline mơ hồ**, cần cách đo cụ thể + ví dụ; (b) 105 không đổi giá trị nào (cả `truncated` cũng toàn `no`) → **một người lệch**, nhiều khả năng để mặc định CVAT → sửa người gán |
| `iou_thap` | 265 | Cả 5 người đều có, IoU TB ca thấp 0.71–0.74, phân bố đều | Không phải lỗi một người → **guideline R-01 chưa đủ rõ** về cách ôm box (gương, tay chân, phần hàng hoá, người ngồi trên xe) |
| `bat_dong_ignore` | 222 | 102 ca "vùng ignore không ghép được"; 105 dùng 27 `ignore_region` (người khác 7–14) và vùng của 105/103 phủ box mà 104/101 đã vẽ từng vật | **Guideline R-08 mơ hồ**: chưa định nghĩa "cụm dày đặc" và kích thước vùng; 105 dùng ignore rộng hơn hẳn |
| `bo_sot_thua` | 216 | Chủ yếu `person` (107) và `motorcycle` (71). Người hay bị thiếu box nhất: 103 (66), 101 (55); 103 có ít box nhất (126 so với 163–173 của 101/102/104) | Gắn với R-08 (vật nhỏ, cụm đông) → guideline; riêng 103 cần rà lại vật ở xa ≥ 20 px |
| `sai_lop` | 14 | img_028 (car↔truck: xe bán tải — v1.0 đã quy định pickup = `truck`); img_016, img_015 (car/van/truck); img_009 (bus↔truck); img_010, img_034 (person↔motorcycle, IoU cao → box người ngồi xe và box xe trùng nhau) | img_028: **người lệch** (đã có quy tắc). car/van, bus/truck: **guideline mơ hồ** (ca mở mục 4.2). person↔motorcycle: cần mở ảnh xem có phải ghép nhầm cặp không |
| `co_can_review` | 10 | 102 (img_003), 103 (img_001, 003, 016, 022, 029) | Đưa thẳng vào họp calibration |

**Hướng cho Pha 5 / guideline v1.1:** (1) ngưỡng `occluded_level` có cách ước lượng + ảnh ví dụ; (2) R-08 định nghĩa cụm dày đặc và cách vẽ vùng; (3) R-01 bổ sung phần nào thuộc box; (4) chốt ranh giới car/van/truck/bus; (5) nhắc người gán phải chọn thuộc tính, không để mặc định.

**Điều IAA chưa xác nhận:** IoU/lớp chỉ đo trên cặp đã ghép — box cả 3 người cùng bỏ sót hoặc cùng gán sai lớp không làm IAA giảm. Phải đối chiếu ca tham chiếu và QC bằng mắt ở Pha 6.

## 3. Calibration và kiểm lại

- Ca đã xử lý, rule ID, bản sửa và người xác nhận: 1003/1003 ca có dòng quyết định trong `calibration_decisions.csv` (người chốt 101, guideline v1.1). Theo rule: R-12 thuộc tính 276 · R-01 ôm box 265 · R-08 ignore_region 222 · R-10 bỏ sót/thừa 216 · R-11 can_review 10 · R-13 ranh giới lớp 9 · R-14 box người/xe 5. Bản sửa: CVAT task calib trên `work/calib/images/` (nạp nháp `work/calib/consensus_flagged.json`), xuất `submission/qc/labels_calibrated_<rev>.json`. Kiểm lại: QC vòng 1 (105) trên nhãn cuối — còn 29/40 ảnh có lỗi (mục 4) → cần Rework.
- Tác động v1.1 lên nhãn và ảnh: thay đổi **semantic** (`guideline_change.json`), taxonomy giữ v1.0 nên không cần mapping lớp. 763 ca `pham_vi=lo` (R-01, R-08, R-12 đổi cách làm cho mọi ảnh) → phạm vi sửa **40/40 ảnh**.
- Tham chiếu bằng chứng khi Rework: vòng cũ `reviews/round-01/` (giữ nguyên, kèm `labels_snapshot.json`); vùng sửa = các dòng lỗi trong `round-01/qc_review.csv`; vòng mới sẽ là `reviews/round-02/` sau khi sửa nhãn và chạy lại `build_final.py`.

## 4. Kết quả duyệt

- Vòng QC: 1
- Người review và ngày: 105, 16/11/2026
- Đã duyệt / cả lô: 40/40 ảnh (duyệt toàn bộ; nhóm `ca_kho` 35/35, `con_lai` 5/5).
- Ảnh lỗi / ảnh đã duyệt: 29/40 = 72.5% (ngưỡng ≤ 2%). `ca_kho` 29/35 = 82.9%; `con_lai` 0/5.
- Annotation đối tượng hiện có bị lỗi / annotation đối tượng đã duyệt: 38/305 = 12.5% (không tính `ignore_region`).
- Vật bỏ sót, ảnh và vùng: 28 vật, ghi vùng từng vật trong `qc_review.csv`. Chủ yếu là người lái / xe của người lái chưa có box (img_001, 005, 008, 009, 016, 021, 031, 035) và vật ở xa ≥ 20 px (img_006, 012, 015, 022, 024, 025, 032, 039).
- Ca khó / phần còn lại, lỗi lặp và ví dụ: theo `round-01/qc_report_auto.md`: `bo_sot` 28, `sai_thuoc_tinh` 24, `sai_vi_tri_kich_thuoc` 10, `thua` 6, `sai_lop` 4. Lỗi lặp: (1) thiếu box người lái hoặc xe của người lái (R-05), ví dụ img_031 thiếu người lái của #240, #246, #235; (2) box chạm mép nhưng `truncated=no` (R-04) ở 14 ảnh, ví dụ img_030 #230–#232; (3) `occluded_level=0` dù bị che (R-12), ví dụ img_018 #153, #154 sau hàng rào. 6 dòng reviewer từng chưa chắc (img_011 #111, img_012, img_013 #128, img_022, img_025) đã được 105 xác nhận là lỗi.
- Một lỗi máy chặn được và một lỗi cần người đọc ảnh: *máy chặn được*: box chạm mép ảnh mà `truncated=no` (so tọa độ box với kích thước ảnh), và box còn tick `can_review` (qc_report đã báo). *Cần người đọc ảnh*: người lái chưa có box trên xe máy (img_031), xe máy điêu khắc bị gán như xe thật (img_011 #119) — script không biết vật có thật hay không.

## 5. Quyết định bàn giao

- Quyết định: Hold
- Lý do, người nhận và phạm vi sử dụng: Hold (khớp `round-01/qc_result.json`) vì nhãn cuối còn 6 box tick `can_review` (img_001 #6, img_003 #21, #38, img_016 #145, img_022 #184, img_029 #221) — bằng chứng chưa đủ để bàn giao. Kể cả khi gỡ, tỉ lệ ảnh lỗi 72.5% > 2% và lỗi lặp lại → Rework. Chưa bàn giao cho người nhận; lô chỉ dùng nội bộ để học quy trình.
- Nếu còn việc: (1) 104 cập nhật `calibration_decisions.csv` thêm ca Q01… cho các lỗi vòng 1; (2) cả nhóm sửa nhãn trong CVAT theo v1.1 (ưu tiên bỏ sót R-05, `truncated` R-04, `occluded_level` R-12), bỏ tick `can_review`; (3) chạy `prepare_calibration.py` → lưu COCO theo tên mới → `build_final.py`; (4) 105 mở `qc_sample.py --round 2`, duyệt lại toàn lô, chạy `qc_report.py --round 2`. Vòng 1 giữ nguyên làm bằng chứng.
- Kiểm ngược từ báo cáo tới ảnh, rule và nhãn: ví dụ img_016 #142 — `round-01/qc_review.csv` ghi `sai_lop`, R-13 (xe bán tải gán `car`); trong `labels_calibrated.json` annotation 142 là `car`; guideline v1.1 R-13 quy định pickup là `truck`; ảnh `work/review/round-01/img_016.jpg.png`. Khớp.
