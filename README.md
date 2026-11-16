# Day 07 Lab — Guideline, Taxonomy & QC

**Nhóm 2–5 người · 4 giờ · 40 ảnh đường phố và ca đối chứng · gán box bằng CVAT**

Bạn nhận một guideline sơ sài và bộ ảnh có tuk-tuk, xe chở xe, ảnh phản chiếu, tượng và ma-nơ-canh. Nhóm sẽ viết lại quy tắc, gán độc lập trên cùng một lô pilot, giải quyết chỗ bất đồng và bàn giao nhãn COCO 1.0 kèm bằng chứng để người khác kiểm lại.

![Mẫu ảnh trong bộ dữ liệu](docs/assets/sample_grid.jpg)

| Chặng | Bằng chứng nhóm tạo ra |
|---|---|
| Đặc tả | Guideline cá nhân, guideline v1.0 của nhóm, taxonomy |
| Gán độc lập | Danh sách phân công và COCO riêng của từng người |
| Calibration | Báo cáo đồng thuận, quyết định theo từng ca, guideline v1.1 và nhãn đã sửa |
| Bàn giao | QC toàn bộ lô pilot, bản nhãn cuối, manifest và quyết định Release / Hold / Rework |

## Bắt đầu

1. Clone [repo lab](https://github.com/VinUni-AI20k/K5-L23-Day07-Guideline-Taxonomy-QC) vào máy. Nhóm dùng repo riêng tư của lớp hoặc chuẩn bị gói nộp qua LMS theo hướng dẫn của coach.
2. Cài Python 3.9 trở lên và chạy `python -m pip install -r requirements.txt`. Dùng tài khoản CVAT của lớp.
3. Mỗi người nhận một mã như `101`. Đọc [hướng dẫn từng pha](GUIDE.md) và [quy tắc làm bài](RULES.md) trước khi xem ảnh.

Mở [checkpoints](CHECKPOINTS.md) để theo dõi 4 giờ, [rubric](RUBRIC.md) để biết bằng chứng chấm và [hướng dẫn nộp](SUBMISSION.md) để rà gói bàn giao. Coach dùng [hướng dẫn chuẩn bị lớp](docs/COACH_GUIDE.md) và [ma trận kiểm thử](docs/TEST_MATRIX.md).

- [CVAT và COCO](docs/CVAT_SETUP.md)
- [Công thức và quyết định QC](docs/QC_CHEATSHEET.md)
- [Nguồn ảnh và giấy phép](data/README.md)
- [Lệnh và file đầu ra](scripts/README.md)
