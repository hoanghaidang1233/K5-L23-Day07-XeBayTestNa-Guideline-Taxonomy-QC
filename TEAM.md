# Thông tin nhóm

- Tên nhóm: XeBayTestNa
- Mã thành viên: 101, 102, 103, 104, 105 (5 người → mỗi ảnh 3 người gán độc lập, K = 3)
- Người điều phối: 101
- Người review bản cuối: 105
- Commit chốt bài (điền ở kênh nộp sau khi commit):

## Thành viên

| Mã | Vai trò |
|---|---|
| 101 | Điều phối / Git / nộp bài |
| 102 | Biên tập guideline |
| 103 | Taxonomy & CVAT |
| 104 | IAA & Calibration |
| 105 | Reviewer QC |

Repo để public theo yêu cầu của coach; bảng đối chiếu mã ↔ họ tên gửi riêng cho coach.

## Phân công

| Mã | Vai trò | Phụ trách thêm (ngoài guideline cá nhân, nhãn và ghi chú cá nhân) |
|---|---|---|
| 101 | Điều phối / Git / nộp bài | `TEAM.md`, quản lý repo và mốc thời gian, chạy `check_submission.py`, gửi bài và commit chốt |
| 102 | Biên tập guideline | Tổng hợp bất đồng Pha 2, viết `guideline_v1.0_agreed.md`, `guideline_v1.1_calibrated.md`, `guideline_change.json` |
| 103 | Taxonomy & CVAT | `taxonomy.json` (và `taxonomy_v1.1.json` nếu đổi), `make_cvat_labels.py`, chạy `split_assign.py`, hỗ trợ CVAT |
| 104 | IAA & Calibration | `image_issues.csv`, `qc_compare.py`, `viz_disagreement.py`, `calibration_decisions.csv`, `prepare_calibration.py`, `build_final.py` |
| 105 | Reviewer QC | `qc_sample.py`, phiếu `reviews/round-XX/qc_review.csv`, `qc_report.py`, `QC_REPORT.md` |

## Kết quả tóm tắt (điền cuối bài)

- Lượt gán hoàn tất / được giao: 120/120
- IAA trước calibration (kèm mẫu số): IoU TB 0.815 / 518 cặp box ghép; cùng lớp 97.3%; CHƯA ĐẠT (ngưỡng IoU 0.85)
- Số ảnh phải calibration: 35 / 40
- Vòng QC và số ảnh đã duyệt / cả lô: vòng 1, 40/40
- Tỉ lệ lỗi: 72.5% theo ảnh (29/40), 12.8% theo đối tượng (39/305)
- **Quyết định** (Release / Hold / Rework) và lý do một dòng: **HOLD** — còn 6 box `can_review` chưa chốt; gỡ xong vẫn Rework vì 72.5% ảnh lỗi > 2% (lỗi lặp: thiếu người lái/xe, truncated, occluded).

## Ghi chú phiên bản guideline

- Guideline v1.0 (`guideline_v1.0_agreed.md`) được cả nhóm 101–105 xác nhận và khóa ở buổi họp Pha 2, dùng nguyên cho Pha 3. Dòng "BẢN NHÁP" trong file là chữ còn sót lại từ lúc soạn; không sửa vì file đã được băm làm đầu vào IAA (sửa sẽ làm lệch bằng chứng).
- Guideline v1.1 (`guideline_v1.1_calibrated.md`) được cả nhóm đọc và đồng ý ngày 16/11/2026 trước khi sửa nhãn ở Pha 5.
