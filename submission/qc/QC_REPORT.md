# Báo cáo QC — XeBayTestNa

## 1. Mục đích, dữ liệu và phiên bản

- Mục đích và giới hạn sử dụng: lô 40 ảnh đường phố gán bounding box 2D (8 lớp người/xe + `ignore_region`) để đặc tả guideline và chạy quy trình QC. Lô nhỏ, ảnh chọn từ Wikimedia Commons, thiên về cảnh đường phố Đông Nam Á, nên **không đại diện** cho phân bố thực tế (thời tiết, ban đêm, góc camera xe); chỉ dùng cho học tập / thử nghiệm, không dùng để đánh giá mô hình sản phẩm.
- Nguồn và phép sử dụng dữ liệu: ảnh lấy từ Wikimedia Commons, ghi công từng ảnh (tên gốc, tác giả, giấy phép CC BY / CC BY-SA / CC0 / public domain, link) trong `data/ATTRIBUTION.csv`. Dùng cho mục đích học tập; khi chia sẻ lại phải giữ ghi công và điều kiện giấy phép (ảnh CC BY-SA: bản phái sinh phải cùng giấy phép).
- Manifest và checksum nhãn cuối: <<batch_manifest.json và SHA-256>>
- Guideline và taxonomy khi gán / khi bàn giao: gán theo guideline v1.0 và taxonomy v1.0 (8 lớp); bản bàn giao <<v1.1 sau calibration>>
- Sửa script (theo RULES.md): sửa `scripts/make_cvat_labels.py` để CVAT bản mới nhận cấu hình nhãn: (1) checkbox `can_review` xuất `values: []` gây lỗi "attribute values must be a non-empty array" → đổi thành `["false"]`; (2) thiếu trường `type` gây lỗi "unknown label type undefined" → thêm `"type": "rectangle"`. Taxonomy không đổi; unit test 19/20 (test còn lại lỗi do quyền tạo symlink trên Windows).

## 2. Phân công và đồng thuận trước calibration

- Lượt hoàn thành / được giao: **120/120** (5 người × 24 ảnh, K=3, 40/40 ảnh có ≥ 2 người gán).
- Số cặp box ghép, IoU TB, đồng thuận lớp và mẫu số: mẫu số **518 cặp box ghép** (IoU ≥ 0.5, ghép 1-1 không phân biệt lớp, cộng trên mọi cặp người gán × ảnh chung). IoU TB **0.815** (ngưỡng 0.85 → **CHƯA ĐẠT**); cùng lớp **97.3%** (ngưỡng 95% → đạt); cùng `occluded_level` 53.1%; cùng `truncated` 87.5%. Chỉ 2/10 cặp đạt (103|104: 0.856, 103|105: 0.860); thấp nhất 101|105: 0.767.
- Box chưa ghép, loại do ignore, bất đồng vùng ignore: chưa ghép **194/142** (A/B) — không nằm trong mẫu số IoU/lớp; **26** box loại khỏi so sánh vì nằm trong `ignore_region`; **222** ca bất đồng vùng ignore. Tổng **1003 ca bất thường trên 35/40 ảnh** (`calibration_candidates.csv`). Ảnh nặng nhất: img_003 (92 ca), img_021 (72), img_034 (71), img_039 (69), img_008 (60).
- Ca hai người có thể cùng sai sau khi đối chiếu tham chiếu: <<ảnh, điều khoản, cách kiểm — nhóm điền sau khi đối chiếu ca tham chiếu của coach>>

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

- Ca đã xử lý, rule ID, bản sửa và người xác nhận: 1003/1003 ca có dòng quyết định trong `calibration_decisions.csv` (người chốt 101, guideline v1.1). Theo rule: R-12 thuộc tính 276 · R-01 ôm box 265 · R-08 ignore_region 222 · R-10 bỏ sót/thừa 216 · R-11 can_review 10 · R-13 ranh giới lớp 9 · R-14 box người/xe 5. Bản sửa: CVAT task calib trên `work/calib/images/` (nạp nháp `work/calib/consensus_flagged.json`), xuất `submission/qc/labels_calibrated_<rev>.json`. Kiểm lại: 105 trên ảnh chồng nhãn <<kết quả>>.
- Tác động v1.1 lên nhãn và ảnh: thay đổi **semantic** (`guideline_change.json`), taxonomy giữ v1.0 nên không cần mapping lớp. 763 ca `pham_vi=lo` (R-01, R-08, R-12 đổi cách làm cho mọi ảnh) → phạm vi sửa **40/40 ảnh**.
- Tham chiếu bằng chứng khi Rework: <<vòng cũ, vùng sửa, vòng mới>>

## 4. Kết quả duyệt

- Vòng QC: <<số nguyên, ví dụ 1>>
- Người review và ngày: <<mã, ngày>>
- Đã duyệt / cả lô: <<>>
- Ảnh lỗi / ảnh đã duyệt: <<>>
- Annotation đối tượng hiện có bị lỗi / annotation đối tượng đã duyệt: <<>>
- Vật bỏ sót, ảnh và vùng: <<>>
- Ca khó / phần còn lại, lỗi lặp và ví dụ: <<dẫn phiếu của vòng>>
- Một lỗi máy chặn được và một lỗi cần người đọc ảnh: <<>>

## 5. Quyết định bàn giao

- Quyết định: <<Release hoặc Hold hoặc Rework>>
- Lý do, người nhận và phạm vi sử dụng: <<>>
- Nếu còn việc: <<việc, người xử lý, bằng chứng cần kiểm lại>>
- Kiểm ngược từ báo cáo tới ảnh, rule và nhãn: <<mã ca, đường dẫn, kết quả>>
