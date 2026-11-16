# CVAT và COCO cho Day 07

Dùng instance CVAT do lớp chỉ định. Coach kiểm quyền tạo Task và xuất/nhập COCO trước buổi. Khi chia sẻ artifact, dùng mã thành viên.

## Tạo task gán độc lập

1. Chạy `python scripts/make_cvat_labels.py`. Trong **Projects → Create new project → Labels → Raw**, dán `cvat/labels_generated.json` và lưu.
2. Tạo Task riêng, chọn Project và tải ảnh trong `work/<mã>/images/`. Số frame phải khớp danh sách phân công.
3. Chọn **Rectangle**, lớp và thuộc tính. Phím `N` bắt đầu vẽ, `F`/`D` đi qua ảnh, `Ctrl+S` lưu (biến thể tùy máy). Gán `occluded_level` của nhóm. Cờ occluded sẵn trong CVAT là trường khác.
4. Với vùng bỏ qua, dùng rectangle lớp `ignore_region`. Với box mơ hồ, tick `can_review`. Với vùng chưa có box, ghi vấn đề cấp ảnh. Ảnh không có đối tượng vẫn nằm trong Task.

## Xuất COCO 1.0

Trong **Actions → Export task dataset**, chọn **COCO 1.0**, bỏ chọn tải ảnh, rồi tải file trong **Requests**. Giải nén và lấy JSON ở `annotations/instances_<subset>.json` (thường là `instances_default.json`). Lưu theo mã như `submission/labels/101.json`. Chạy `python scripts/validate_labels.py submission/labels/101.json`; kiểm đặc biệt ảnh trống và các trường trong `attributes`.

## Sửa và review

1. Sau khi điền quyết định, chạy `python scripts/prepare_calibration.py`. Tạo Task từ `work/calib/images/`. Nếu taxonomy đổi, tạo Project từ `cvat/labels_final.json` do `make_cvat_labels.py --final` sinh ra.
2. Nếu script tạo `work/calib/consensus_flagged.json`, dùng **Actions → Upload annotations → COCO 1.0**. Nếu schema không còn tương thích, áp dụng mapping hoặc gán lại theo quyết định rồi xuất COCO.
3. Nếu instance có Review mode, mở issue trên box hoặc vùng ảnh. Ghi mã ca, mã quy tắc, cách sửa và kết quả reviewer kiểm lại. Dùng ảnh chồng box và CSV nếu thiếu quyền Review.
4. Xuất COCO 1.0 và lưu theo đường dẫn có mã phiên bản mà `prepare_calibration.py` in ra. Giữ cùng tên ảnh. Nếu xuất thiếu thuộc tính, kiểm cấu hình Project và lưu lại box trước khi xuất.

[Tài liệu COCO của CVAT](https://docs.cvat.ai/docs/manual/advanced/formats/format-coco/) · [Manual QA](https://docs.cvat.ai/docs/qa-analytics/manual-qa/)
