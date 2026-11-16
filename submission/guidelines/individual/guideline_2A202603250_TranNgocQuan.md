# Guideline gán nhãn — <phiên bản> — <mã hoặc nhóm>

> Copy file này (hoặc `guideline_v0_starter.md`) rồi viết. Một guideline đủ gồm **5 phần**. Câu nào của v0
> làm hai người đọc ra hai cách khác nhau thì phải viết lại cho một cách hiểu duy nhất.

- Phiên bản: v0
- Ngày: 16/11/2026
- Người viết / duyệt: Trần Ngọc Quân

## 1. Mục tiêu
Mô hình nào dùng nhãn này, để làm gì, sai ở đâu thì nguy hiểm.
-Mô hình & Mục đích: Dữ liệu dùng để huấn luyện mô hình phát hiện vật thể 2D (2D Object Detection) phục vụ hệ thống giao thông thông minh / tự hành (nhận diện người và phương tiện trên đường).

-Lỗi nguy hiểm nhất (False Negative - Bỏ sót): Bỏ sót người đi bộ, người đi xe máy hoặc xe đang di chuyển gần đầu xe (dễ gây tai nạn va chạm).

-Lỗi thứ yếu (False Positive / Lệch box): Nhận nhầm hình in quảng cáo, bóng phản chiếu là vật thật, hoặc vẽ box quá rộng/lệch làm sai lệch ước lượng khoảng cách.

## 2. Lớp và thuộc tính
### Lớp
| Lớp | Định nghĩa (một câu) | Ví dụ | Phản ví dụ / dễ nhầm với |
|---|---|---|---|
| car | Phương tiện bốn bánh chở người cỡ nhỏ đến trung bình. | Sedan, SUV, hatchback, taxi, xe bán tải. | Xe tải thùng lớn, xe buýt, xe van. |

Vật **không thuộc lớp nào** thì làm gì (không gán? vùng ignore? lớp mới?).
-Không gán nhãn

### Thuộc tính
Mỗi thuộc tính: tên, **các mức cố định**, nghĩa từng mức, **ngưỡng cụ thể** (ví dụ che bao nhiêu % thì mức nào).
-truncated (Bị cắt ở mép ảnh):
+false: Toàn bộ vật thể nằm gọn trong khung hình.
+true: Vật thể bị cắt mất một phần do nằm sát rìa bức ảnh.
-occluded (Mức độ che khuất):
+none (0%): Nhìn thấy toàn bộ vật thể, không bị vật khác che.
+partial (1% – 50%): Bị che một phần bởi xe khác, cột điện, người... nhưng vẫn nhận diện rõ hình dáng.
+heavy (> 50% – 80%): Bị che quá nửa, chỉ thấy một phần đặc trưng (như đầu xe hoặc bánh xe). (Nếu che > 80% không đoán được hình dạng -> bỏ qua, không vẽ).

## 3. Quy tắc vẽ box
Box ôm sát phần nhìn thấy; vật bị che; vật bị cắt mép ảnh; phản chiếu / hình in / tượng / đồ chơi; xe chủ (ego); vật quá nhỏ; đám đông; một vật có nhiều phần (người và xe)…

## 4. Ca khó
| Ảnh | Tình huống | Rule ID | Quyết định | Lý do |
|---|---|---|---|---|
| img_038 | ảnh chụp phương tiện nhưng lại là xe đồ chơi(car) | 01 | không gán | hông phải phương tiện(kích thước, hình dạng) trên đường và cũng không tham gia giao thông trên đường |

Nếu chưa chốt, đánh dấu `can_review` trên box hoặc ghi vấn đề cấp ảnh. Dùng mã quy tắc trong nhật ký QC.

## 5. QC và phiên bản
Ngưỡng QC áp dụng, ai quyết ca mơ hồ, lịch sử thay đổi:

| Phiên bản | Thay đổi | Lý do (ca nào) |
|---|---|---|
| | | |
