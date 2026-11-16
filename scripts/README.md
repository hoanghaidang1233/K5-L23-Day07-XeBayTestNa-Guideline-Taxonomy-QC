# Lệnh và bằng chứng Day 07

Chạy từ thư mục gốc sau khi cài `requirements.txt`. [Hướng dẫn](../GUIDE.md) giải thích thời điểm chạy từng lệnh.

| Lệnh | Đầu ra chính |
|---|---|
| `python scripts/split_assign.py --members 101,102 --copy-images` | `assignment.csv`, danh sách từng mã và ảnh đã copy. Thêm `--all-images` để phân công cả bộ. |
| `python scripts/make_cvat_labels.py` | Kiểm JSON Schema và sinh cấu hình CVAT. `--final` đọc taxonomy dùng cho bản cuối. |
| `python scripts/validate_labels.py submission/labels/101.json` | Đối chiếu COCO, hình học, lớp, thuộc tính và đúng tập ảnh của thành viên. |
| `python scripts/qc_compare.py` | Độ phủ lượt gán, IAA, ca cần xem, ảnh bị cờ và nháp đồng thuận. Dừng nếu một lượt được giao vắng mặt. |
| `python scripts/viz_disagreement.py --images flagged` | Ảnh chồng nhãn của từng người trong `work/viz/`. |
| `python scripts/build_final.py` | Mẫu quyết định lần đầu nếu cần, sau đó tạo COCO/CSV nhãn cuối và manifest khi đủ dữ liệu. |
| `python scripts/prepare_calibration.py` | Ảnh đúng phạm vi cần sửa, COCO nháp khi schema tương thích và đường dẫn export có mã phiên bản. |
| `python scripts/qc_sample.py --round 1` | `qc_sample.csv`, phiếu, ảnh overlay và bản chụp nhãn/manifest của vòng. Thêm `--n` chỉ để demo. |
| `python scripts/qc_report.py --round 1` | Số đo, `qc_result.json` và gợi ý quyết định dựa trên phiếu của vòng. |
| `python scripts/check_submission.py --round 1` | Kiểm các trường bắt buộc của báo cáo và đối chiếu quyết định với bản nhãn và phiếu hiện hành. |

## Phiếu QC

- Ảnh sạch: một dòng với `image, error_type=khong, reviewer`.
- Lỗi trên box: một dòng mỗi loại lỗi, ghi `object_id` được in trên ảnh QC, `rule_id` và `note`. Dùng `thua`, `sai_lop`, `sai_vi_tri_kich_thuoc` hoặc `sai_thuoc_tinh`.
- Vật bỏ sót: dùng `bo_sot`, để trống `object_id`, ghi `region` đủ cụ thể để tìm vật, kèm `rule_id` và `note`. Với nhiều vật ở gần nhau, ghi một mô tả vùng phân biệt được cho mỗi vật.
- Ghi mã ba chữ số của người review trên mỗi dòng. Nếu cần sửa nhãn, cập nhật quyết định, kiểm và lưu COCO theo phiên bản do `prepare_calibration.py` in ra, rồi gộp COCO và CSV bằng `build_final.py`, rồi mở vòng mới. Phiếu vòng trước vẫn đi cùng bản chụp nhãn cũ.
