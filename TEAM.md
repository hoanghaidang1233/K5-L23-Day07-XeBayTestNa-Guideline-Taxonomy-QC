# Thông tin nhóm

- Tên nhóm: XeBayTestNa
- Mã thành viên: 101, 102, 103, 104, 105 (5 người → mỗi ảnh 3 người gán độc lập, K = 3)
- Người điều phối: 101
- Người review bản cuối: 105
- Commit chốt bài (điền ở kênh nộp sau khi commit):

## Thành viên

| Mã | Thành viên | Vai trò |
|---|---|---|
| 101 | Hoàng Hải Đăng | Điều phối / Git / nộp bài |
| 102 | Trịnh Vinh Khánh | Biên tập guideline |
| 103 | Nguyễn Thiên Phúc | Taxonomy & CVAT |
| 104 | Nguyễn Khánh Trình | IAA & Calibration |
| 105 | Trần Ngọc Quân | Reviewer QC |

## Phân công

| Mã | Vai trò | Phụ trách thêm (ngoài guideline cá nhân, nhãn và ghi chú cá nhân) |
|---|---|---|
| 101 | Điều phối / Git / nộp bài | `TEAM.md`, quản lý repo và mốc thời gian, chạy `check_submission.py`, gửi bài và commit chốt |
| 102 | Biên tập guideline | Tổng hợp bất đồng Pha 2, viết `guideline_v1.0_agreed.md`, `guideline_v1.1_calibrated.md`, `guideline_change.json` |
| 103 | Taxonomy & CVAT | `taxonomy.json` (và `taxonomy_v1.1.json` nếu đổi), `make_cvat_labels.py`, chạy `split_assign.py`, hỗ trợ CVAT |
| 104 | IAA & Calibration | `image_issues.csv`, `qc_compare.py`, `viz_disagreement.py`, `calibration_decisions.csv`, `prepare_calibration.py`, `build_final.py` |
| 105 | Reviewer QC | `qc_sample.py`, phiếu `reviews/round-XX/qc_review.csv`, `qc_report.py`, `QC_REPORT.md` |

## Kết quả tóm tắt (điền cuối bài)

- Lượt gán hoàn tất / được giao:
- IAA trước calibration (kèm mẫu số):
- Số ảnh phải calibration: … / 40
- Vòng QC và số ảnh đã duyệt / cả lô:
- Tỉ lệ lỗi: … % theo ảnh (…/…), … % theo đối tượng (…/…)
- **Quyết định** (Release / Hold / Rework) và lý do một dòng:
