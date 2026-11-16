# Guideline gán nhãn — v0 — 102 | Nhóm: XeBayTestNa

> Copy file này (hoặc `guideline_v0_starter.md`) rồi viết. Một guideline đủ gồm **5 phần**. Câu nào của v0
> làm hai người đọc ra hai cách khác nhau thì phải viết lại cho một cách hiểu duy nhất.

- Phiên bản: v0
- Ngày: 16/11/2026
- Người viết / duyệt: 102 / 101

## 1. Mục tiêu

Mô hình hướng đến việc nhận diện và phân loại người tham gia giao thông cũng như các phương tiện trong môi trường đường phố, dựa trên dữ liệu hình ảnh thực tế. Kết quả có thể được ứng dụng trong các hệ thống giám sát giao thông hoặc tích hợp vào hệ thống nhận thức môi trường của xe tự hành. Do đó, chất lượng nhãn đóng vai trò quan trọng đối với độ chính xác của mô hình. Việc gán sai đối tượng, phân loại nhầm hoặc bỏ sót các đối tượng tham gia giao thông có thể dẫn đến những dự đoán không chính xác, ảnh hưởng trực tiếp đến các quyết định xử lý tiếp theo và tiềm ẩn nguy cơ mất an toàn.

## 2. Lớp và thuộc tính
### Lớp
| Lớp | Định nghĩa (một câu) | Ví dụ | Phản ví dụ / dễ nhầm với |
|---|---|---|---|
| `car` | Ô tô con có thiết kế chủ yếu để chở người, thường có từ 4 bánh và tối đa khoảng 8 chỗ ngồi ngoài ghế lái. | Sedan, hatchback, SUV, coupe, ô tô gia đình. | Xe bán tải (`truck`), xe tải (`truck`), xe van (`van`), xe buýt (`bus`). |
| `truck` | Phương tiện cơ giới được thiết kế chủ yếu để vận chuyển hàng hóa, thường có thùng hoặc khoang chở hàng riêng biệt. | Xe tải thùng, xe tải ben, xe đầu kéo, xe bán tải (pickup truck). | Ô tô con (`car`), xe van chở hàng (`van`), xe buýt (`bus`). |
| `van` | Phương tiện cơ giới có thân xe dạng hộp, khoang hành khách hoặc khoang hàng kín, thường có kích thước nhỏ hơn xe tải lớn. | Minivan, xe van chở hàng, xe giao hàng cỡ nhỏ, xe chở khách dạng van. | Ô tô con dạng SUV (`car`), xe tải thùng lớn (`truck`), xe buýt cỡ nhỏ (`bus`). |
| `bus` | Phương tiện cơ giới được thiết kế chủ yếu để vận chuyển nhiều hành khách, có khoang chở khách với nhiều hàng ghế hoặc vị trí đứng. | Xe buýt nội thành, xe khách liên tỉnh, xe buýt trường học, xe buýt du lịch. | Xe van chở khách (`van`), minivan (`van`), xe tải (`truck`). |
| `motorcycle` | Phương tiện giao thông cơ giới hai bánh, có động cơ và được thiết kế để chở người. | Xe máy phổ thông, xe tay ga, mô tô phân khối lớn, xe máy điện hai bánh. | Xe đạp (`bicycle`), xe đạp điện không thuộc định nghĩa xe máy của taxonomy, xe ba bánh hoặc tuk-tuk (`tuktuk`). |
| `bicycle` | Phương tiện giao thông hai bánh di chuyển chủ yếu bằng sức người thông qua bàn đạp, không có động cơ dẫn động chính. | Xe đạp thể thao, xe đạp địa hình, xe đạp thành phố, xe đạp trẻ em. | Xe máy (`motorcycle`), xe máy điện, xe ba bánh chở hàng, tuk-tuk (`tuktuk`). |
| `tuktuk` | Phương tiện giao thông ba bánh có động cơ, thường có khoang chở khách hoặc hàng hóa và thiết kế đặc trưng của xe tuk-tuk. | Tuk-tuk chở khách, xe auto-rickshaw, xe tuk-tuk chở hàng. | Xe máy hai bánh (`motorcycle`), xe ba bánh tự chế không có đặc điểm nhận diện rõ ràng của tuk-tuk, xe van (`van`). |
| `person` | Con người thực tế xuất hiện trong ảnh, bao gồm người đi bộ, người lái xe và hành khách khi có thể quan sát được. | Người đi bộ, người đi xe máy, người đạp xe, tài xế, hành khách, người đứng bên đường. | Tượng người, hình người trên biển quảng cáo, hình in trên poster, ma-nơ-canh, hình nộm. |

Vật **không thuộc lớp nào** thì làm gì (không gán? vùng ignore? lớp mới?).
vật không thuộc lớp thì không gán.

### Thuộc tính

| Thuộc tính | Giá trị | Ý nghĩa |
|---|---|---|
| `occluded_level` | `0`, `1`, `2`, `3` | Mức bị che: 0 là thấy rõ, 1 là bị che ít, 2 là bị che nhiều, 3 là không rõ. |
| `truncated` | `no`, `yes` | Vật bị cắt ở mép ảnh. |
| `can_review` | tick / không | Tick khi không chắc, để người khác xem lại. |


## 3. Quy tắc vẽ box

1. Gán box cho mọi người và phương tiện trong ảnh.
2. Box ôm sát vật.
3. Chọn đúng lớp và thuộc tính.

## 4. Ca khó
| Ảnh | Tình huống | Quyết định | Lý do |
|---|---|---|---|
| img_010 | xe đạp chở rau | nhãn bicycle, occluded_level = 1 | tay lái xe đạp bị khuất nhưng vẫn có thể nhận diện bánh xe và hình dáng xe| 
| img_014 | ảnh người đi xe máy trên đường bị nhòe | không gán nhãn | không thể nhận diện được các đặc trưng của vật thể |
| img_015 | Ảnh xe bị che khuất một phần đằng sau. | gán nhãn van, occluded_level = 1 | có thể nhận diện được vật với các phần có thể nhìn thấy |


Câu hỏi nào chưa chốt: gắn `can_review` và ghi lên trưởng nhóm. Không tự đoán.

## 5. QC và phiên bản
Ngưỡng QC áp dụng, ai quyết ca mơ hồ, lịch sử thay đổi:

| Phiên bản | Thay đổi | Lý do (ca nào) |
|---|---|---|
| | | |
