# Tóm tắt IAA (taxonomy v1.0)

- Ảnh được so sánh (có >=2 người gán): **40**; số người gán: 5
- Lượt gán đủ **120/120**.
- IoU trung bình và % cùng lớp **chỉ tính trên các cặp box đã ghép** (IoU >= 0.5, ghép 1-1 không phân biệt lớp). Box chưa ghép được thống kê riêng ở cột bỏ sót/thừa.
- Mẫu số: 518 cặp box ghép (tổng mọi cặp người gán, ảnh chung).

| Cặp | Ảnh | Cặp box ghép | IoU TB | Cùng lớp | Cùng occluded_level | Cùng truncated | Chưa ghép (A/B) | Bỏ qua do ignore | Đạt IAA |
|---|---|---|---|---|---|---|---|---|---|
| **TỔNG** | 120 | 518 | 0.815 | 97.3% | 53.1% | 87.5% | 194/142 | 26 | CHƯA ĐẠT |
| 101|102 | 13 | 71 | 0.845 | 98.6% | 47.9% | 88.7% | 10/21 | 0 | CHƯA ĐẠT |
| 101|103 | 12 | 50 | 0.803 | 100.0% | 48.0% | 88.0% | 44/4 | 1 | CHƯA ĐẠT |
| 101|104 | 12 | 73 | 0.801 | 95.9% | 57.5% | 90.4% | 13/29 | 0 | CHƯA ĐẠT |
| 101|105 | 11 | 51 | 0.767 | 100.0% | 49.0% | 90.2% | 17/10 | 0 | CHƯA ĐẠT |
| 102|103 | 13 | 68 | 0.771 | 97.1% | 47.1% | 94.1% | 31/13 | 0 | CHƯA ĐẠT |
| 102|104 | 11 | 44 | 0.820 | 95.5% | 45.5% | 86.4% | 15/12 | 8 | CHƯA ĐẠT |
| 102|105 | 11 | 31 | 0.790 | 100.0% | 38.7% | 83.9% | 8/8 | 11 | CHƯA ĐẠT |
| 103|104 | 11 | 41 | 0.856 | 95.1% | 70.7% | 92.7% | 11/24 | 0 | ĐẠT |
| 103|105 | 12 | 36 | 0.860 | 97.2% | 69.4% | 80.6% | 12/16 | 5 | ĐẠT |
| 104|105 | 14 | 53 | 0.852 | 94.3% | 60.4% | 73.6% | 33/5 | 1 | CHƯA ĐẠT |

Ngưỡng IAA: IoU TB >= 0.85, đồng thuận lớp >= 95%.

Số ca bất thường: 1003 ({'bo_sot_thua': 216, 'co_can_review': 10, 'iou_thap': 265, 'sai_thuoc_tinh': 276, 'bat_dong_ignore': 222, 'sai_lop': 14}), trên 35 ảnh cần calibration.
