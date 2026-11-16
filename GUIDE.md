# Hướng dẫn làm lab

Bài nhóm 2–5 người, 4 giờ. Chạy lệnh từ thư mục gốc đã clone. Đầu ra là một lô nhãn COCO 1.0 mà reviewer có thể mở và kiểm lại quyết định. Xem [checkpoints](CHECKPOINTS.md) và [rubric](RUBRIC.md).

## Pha 0 — Chuẩn bị (15 phút)

Cài thư viện bằng `python -m pip install -r requirements.txt`. Kiểm tra bạn đăng nhập CVAT và mở được ảnh trong `data/images/`. Nhóm điền mã các thành viên vào `TEAM.md`, chẳng hạn `101`, `102`. Coach hướng dẫn kênh thu bài riêng. Dùng mã thành viên trong tên file và nhật ký.

**Tự kiểm:** mỗi người mở được ảnh và biết nơi nộp riêng bản làm độc lập.

## Pha 1 — Tìm chỗ guideline còn mơ hồ (35 phút)

Mở [guideline v0](guideline/guideline_v0_starter.md) và khảo sát cả 40 ảnh. Với ảnh có người đi xe máy, hãy tự quyết định lớp, số box và phần được bao trong từng box. Kiểm tra quy tắc ấy trên một ảnh khác. Với xe chở xe, người bị che, vật quá nhỏ, tượng hoặc phản chiếu, ghi ảnh, quyết định và lý do. Phân biệt trường hợp loại theo quy tắc với trường hợp cần hỏi reviewer.

Copy [khung guideline](guideline/TEMPLATE_guideline.md) thành `submission/guidelines/individual/guideline_101.md` (thay mã của bạn). Gửi riêng theo hướng dẫn của coach. Khi cả nhóm đã nộp, mở các bản để chuẩn bị họp. Giữ bản cá nhân đã nộp để so với quyết định của nhóm.

**Tự kiểm:** người đọc có thể tìm lại ảnh cho từng ví dụ và biết quyết định ban đầu của bạn.

## Pha 2 — Chốt đặc tả của nhóm (30 phút)

1. So sánh các bản cá nhân và chốt quy tắc theo mục đích mô hình phát hiện vật trên ảnh 2D. Định nghĩa từng lớp, thuộc tính, phần nhìn thấy được, cách xử lý che khuất, cắt mép và ca ngoài phạm vi. Gắn mã như `R-01` cho quy tắc thường được dẫn lại.
2. Lưu `submission/guidelines/guideline_v1.0_agreed.md`. Cập nhật `submission/taxonomy.json` để lớp và thuộc tính khớp, đặt version `v1.0` và giữ quy tắc số của lab.
3. Chạy `python scripts/make_cvat_labels.py`. Nếu schema báo sai, sửa đúng trường mà lỗi chỉ ra. Sau khi sinh được cấu hình, khóa guideline và taxonomy v1.0 cho lượt gán độc lập.

**Tự kiểm:** cùng một ảnh người đi xe máy, mọi người có thể giải thích cùng quy tắc và dự đoán số box sẽ vẽ.

## Pha 3 — Gán độc lập (60 phút sau nghỉ)

Một người chạy:

```bash
python scripts/split_assign.py --members 101,102 --copy-images
```

Mặc định lô [pilot](data/pilot_images.txt) có 12 ảnh từ bộ 40. Mỗi ảnh được gán độc lập bởi ít nhất hai người, hoặc ba nếu nhóm có năm người. Coach có thể chọn `--all-images` khi có thời gian. Cả nhóm dùng chung `assignment.csv`.

Mỗi người tạo Task riêng trong [CVAT](docs/CVAT_SETUP.md), tải đúng ảnh trong `work/<mã>/images/` và gán theo v1.0. Nếu đã vẽ một box nhưng chưa chắc, tick `can_review`. Nếu chưa có box, ghi riêng ảnh, vùng và lý do để nhóm thu vào `submission/qc/image_issues.csv` với các cột `image,annotator,region,note` sau khi khóa nhãn. Xuất COCO 1.0, lưu thành `submission/labels/101.json` theo mã, tự kiểm rồi gửi riêng:

```bash
python scripts/validate_labels.py submission/labels/101.json
```

**Tự kiểm:** số ảnh khớp phân công. Ảnh không có vật đủ điều kiện vẫn phải có mặt trong COCO. Chỉ tập hợp nhãn của cả nhóm sau khi tất cả đã nộp bản độc lập.

## Pha 4 — Đọc kết quả đồng thuận (20 phút)

```bash
python scripts/validate_labels.py --all
python scripts/qc_compare.py
python scripts/viz_disagreement.py --images flagged
```

Đọc `submission/qc/iaa_summary.md`: kiểm đủ lượt gán, sau đó đọc IoU và đồng thuận lớp trên **các cặp box ghép được**. Mở ảnh chồng nhãn, đọc box chưa ghép và bất đồng vùng ignore. Nếu không có cặp box, các số đo trên cặp là N/A.

Sau khi khóa bản độc lập, đối chiếu ca tham chiếu coach cung cấp với guideline nhóm. Tìm một tình huống hai người có thể làm giống nhau nhưng vẫn sai quy tắc. Ghi ảnh và điều khoản vào báo cáo.

**Tự kiểm:** bạn giải thích được mẫu số và chỉ ra một điều mà IAA chưa xác nhận.

## Pha 5 — Chốt ca và tạo bản nhãn cuối (40 phút)

1. Đối chiếu từng dòng `calibration_candidates.csv` với ảnh, quy tắc và nguyên nhân. Chạy `python scripts/build_final.py` để tạo mẫu `calibration_decisions.csv` nếu chưa có. Điền quyết định, mã quy tắc, phiên bản, người chốt, chỗ sửa và kết quả kiểm lại. Giữ bảng chỉ có header khi không có ca. Nếu phải chạy lại IAA với đầu vào khác, lưu bảng cũ riêng rồi để script tạo lại bảng theo các ca mới.
2. Lưu `guideline_v1.1_calibrated.md` và tạo `submission/guideline_change.json` có `kind` và `summary`. Dùng `editorial` cho đính chính câu chữ hoặc giữ nguyên quy tắc. Dùng `semantic` khi đổi cách chọn vật, lớp, box hay thuộc tính. Quy tắc đổi nghĩa cần ít nhất một quyết định `pham_vi=lo` và kiểm lại cả lô. Nếu đổi taxonomy, lưu thêm `submission/taxonomy_v1.1.json`, tăng version, ghi mapping hoặc cách rà lại. Sinh cấu hình bằng `python scripts/make_cvat_labels.py --final`.
3. Chạy `python scripts/prepare_calibration.py`, sửa đúng tập ảnh được chuẩn bị trong CVAT, rồi lưu COCO đã kiểm theo đúng đường dẫn `labels_calibrated_<phiên-bản>.json` mà lệnh in ra. Nếu quyết định hoặc guideline thay đổi, chạy lại bước chuẩn bị, kiểm theo phiên bản mới rồi lưu vào đường dẫn mới, kể cả khi nhãn được xác nhận giữ nguyên. Nếu không có ảnh cần sửa, dùng luôn bản đồng thuận.
4. Chạy lại `python scripts/build_final.py`. Script ghép nhãn đã sửa với phần còn lại và sinh `labels_calibrated.json`, `labels_calibrated.csv` cùng `batch_manifest.json`.

**Tự kiểm:** mỗi ca có người chốt, có dấu vết bản sửa và có kết quả kiểm lại. Mỗi ảnh trong lô xuất hiện đúng một lần ở bản cuối.

## Pha 6 — Duyệt và bàn giao (30 phút)

```bash
python scripts/qc_sample.py --round 1
python scripts/qc_report.py --round 1
```

Giữa hai lệnh, mở ảnh trong `work/review/round-01/` và điền `submission/qc/reviews/round-01/qc_review.csv`. Mặc định duyệt **toàn bộ lô**. Chọn người kiểm khác người sửa nếu có thể. Với nhóm hai người, đổi vai và ghi nhận khi reviewer từng gán ảnh đó. Dùng [hướng dẫn phiếu QC](scripts/README.md#phiếu-qc) để ghi cả lỗi có box và ca bỏ sót.

- **Hold:** ghi bằng chứng hoặc quyết định còn thiếu. Nếu guideline chưa chốt hoặc dữ liệu có vấn đề, chạy thêm `--guideline-gap N` hoặc `--data-problem N`.
- **Rework:** thêm ca mới vào nhật ký với ID như `Q01`, kể cả nếu IAA không cờ ảnh. Làm lại Pha 5 cho phạm vi cần sửa. Nếu đã có ảnh sửa trước đó, COCO sửa phải bao gồm cả phạm vi cũ và ảnh mới. Sau đó mở `--round 2` để duyệt bản mới, giữ vòng 1.
- **Release:** người kiểm xác nhận lô thực hành cho mục đích đã mô tả sau khi đối chiếu phiếu, nhãn và manifest.

Điền [mẫu báo cáo](docs/QC_REPORT_TEMPLATE.md) và [ghi chú cá nhân](docs/INDIVIDUAL_NOTE_TEMPLATE.md). Chạy `python scripts/check_submission.py --round 1` hoặc chỉ định vòng cuối của nhóm. Nhờ một người ngoài nhóm tìm lại một ca từ báo cáo tới ảnh, quy tắc và bản nhãn. Nộp theo [SUBMISSION.md](SUBMISSION.md).

**Mở rộng:** chạy `python scripts/qc_sample.py --round 99 --n 5 --seed 42` để minh họa chọn ngẫu nhiên từ phần còn lại sau khi lấy các ca khó. Vòng demo luôn giữ trạng thái Hold cho bước bàn giao.
