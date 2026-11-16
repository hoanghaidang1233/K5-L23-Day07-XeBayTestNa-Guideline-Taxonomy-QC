# Nộp bài Day 07

Một nhóm nộp **một link repo riêng tư có quyền cho coach hoặc một gói nén qua LMS** theo kênh được công bố. Đặt tên theo mẫu `K5-L23-DAY07-<TenNhom>-GuidelineQC`. Coach giữ danh sách đối chiếu mã thành viên ở kênh lớp có giới hạn quyền. Dùng mã tự đặt cho lab (ví dụ `101`), tránh dùng họ tên, MSHV, email cá nhân hay tài khoản để đặt tên file, Task hoặc title README.

## Gói bàn giao

| Thành phần | Đường dẫn hoặc nội dung |
|---|---|
| Nhóm và nguồn | `TEAM.md`, bản gốc `data/ATTRIBUTION.csv`, tham chiếu phiên bản bộ ảnh trong manifest |
| Đặc tả | `submission/guidelines/individual/guideline_<mã>.md`, guideline nhóm v1.0 và v1.1, `submission/guideline_change.json`, `submission/taxonomy.json` và bản v1.1 nếu đổi schema |
| Gán độc lập | `submission/labels/<mã>.json`, `submission/qc/assignment.csv`, `image_issues.csv` nếu có ca chưa có box |
| Calibration | `iaa_summary.json/.md`, `calibration_candidates.csv`, `flagged_images.txt`, `consensus_draft.json`, `calibration_decisions.csv`, `calibration_context.json` và COCO phạm vi đã sửa nếu có |
| Nhãn cuối | `submission/qc/labels_calibrated.json`, `labels_calibrated.csv`, `batch_manifest.json` |
| QC và học cá nhân | `submission/qc/reviews/round-*/` giữ cả vòng cũ, `submission/qc/QC_REPORT.md`, `submission/individual_notes/<mã>.md` |

Giữ đường dẫn tương đối để reviewer chạy lại lệnh. Nếu nộp gói nén, kèm starter repo và dữ liệu theo hướng dẫn của lớp, hoặc cung cấp đúng commit và cách lấy lại dữ liệu đã dùng. Giữ nguyên ghi công và điều kiện của ảnh. Thư mục `work/` chỉ phục vụ thao tác cục bộ.

Chạy `python scripts/check_submission.py --round <vòng-cuối>`. Với bài còn Hold vì thiếu đầu vào, ghi rõ phần còn thiếu, người xử lý và nộp bằng chứng đã có qua kênh lớp để coach xem. Thời hạn do coach công bố.
