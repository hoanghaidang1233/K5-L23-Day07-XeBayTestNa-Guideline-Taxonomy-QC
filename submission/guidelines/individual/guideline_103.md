# Guideline gán nhãn — bản cá nhân — 103 | Nhóm: XeBayTestNa

> Copy file này (hoặc `guideline_v0_starter.md`) rồi viết. Một guideline đủ gồm **5 phần**. Câu nào của v0
> làm hai người đọc ra hai cách khác nhau thì phải viết lại cho một cách hiểu duy nhất.

- Phiên bản: v0
- Ngày: 16/11/2026
- Người viết / duyệt: 103

## 1. Mục tiêu
- Nhãn bounding box 2D được sử dụng để huấn luyện mô hình phát hiện đối tượng phục vụ hệ thống camera giám sát giao thông đô thị thông minh và hệ thống hỗ trợ lái xe tự động

## 2. Lớp và thuộc tính
### Lớp
| Lớp | Định nghĩa (một câu) | Ví dụ | Phản ví dụ / Dễ nhầm lẫn |
| --- | --- | --- | --- |
| `car` | Xe ô tô con từ 4 đến 9 chỗ ngồi (sedan, hatchback, SUV, coupe, crossover). | Xe sedan đen chạy ngược chiều trong `img_023`; các xe ô tô đỗ trong bãi `img_018`; xe AMC Pacer màu đen `img_024`. | Xe mô hình đồ chơi gắn trên nóc trong `img_024` (bỏ qua); xe phản chiếu trong gương chiếu hậu `img_020` (bỏ qua). |
| `truck` | Xe tải chở hàng có khoang/thùng chở hàng riêng biệt, xe đầu kéo, xe bán tải (pickup). | Xe tải chở hàng thùng hở hoặc thùng kín lưu thông trên đường. | Xe van thùng kín liền khối (`van`); xe ô tô con chở đồ trên giá nóc (`car`). |
| `van` | Xe ô tô thân liền kín cỡ trung bình đến lớn (khoảng 9–16 chỗ chở khách hoặc xe tải van chở hàng liền khối). | Xe cứu thương, xe chở học sinh dạng van 16 chỗ liền khối. | Xe buýt công cộng lớn (out-of-scope); xe SUV 7 chỗ gia đình (`car`). |
| `motorcycle` | Phương tiện giao thông cơ giới có 2 hoặc 3 bánh gắn động cơ (xe máy số, tay ga, mô tô phân khối lớn, xe mô tô 3 bánh có thùng sidecar). | Dàn xe máy đang dừng/chạy ở ngã tư trong `img_021`; xe mô tô 3 bánh có thùng sidecar bên cạnh trong `img_022`. | Xe xích lô đạp chân trong `img_023` (không gán xe); xe đạp thông thường (không gán xe). |
| `person` | Con người còn sống thuộc mọi tư thế (đang đi bộ, đứng, ngồi, hoặc đang điều khiển phương tiện). | Người đạp xích lô trong `img_023`; người lái xe máy trong `img_021`; tài xế lái xe diễu hành trong `img_019`. | Tượng danh nhân kỵ sĩ bằng đồng trong `img_017` (bỏ qua); mặt người trên biển quảng cáo trong `img_018` (bỏ qua); mô hình linh vật chuột Danger Mouse trong `img_019` (bỏ qua). |
| `ignore_region` | Vùng không gian trên ảnh chứa cụm người/phương tiện có mật độ quá đông, bị che khuất cực nặng hoặc ở quá xa làm mờ nhạt không thể tách box cá nhân đạt chuẩn IoU. | Cụm biển xe máy và người ken đặc ở xa giữa ngã tư trong `img_021`; đám đông khán giả đứng ken đặc sau rào chắn trong `img_019`. | Một chiếc xe đơn lẻ bị che một phần nhưng vẫn thấy rõ đặc trưng (phải gán nhãn lớp cụ thể và gán thuộc tính `occluded_level`). |

Vật **không thuộc lớp nào** thì làm gì (không gán? vùng ignore? lớp mới?).
- Xe đạp, xe xích lô đạp chân: không có lớp bicycle hay cyclo, không vẽ box cho phương tiện này. Tuy nhiên, nếu có người điều khiển hoặc hành khách nhìn thấy rõ, phải vẽ box person cho từng người.
- Xe chế tác hình chuột: nếu vẫn là xe cơ giới 4 bánh tự hành lưu thông trên đường, gán car, tài xế gán person, linh vật chuột nộm không gán.

### Thuộc tính
Mỗi thuộc tính: tên, **các mức cố định**, nghĩa từng mức, **ngưỡng cụ thể** (ví dụ che bao nhiêu % thì mức nào).
| Thuộc tính | Kiểu / Giá trị | Ngưỡng định lượng cụ thể & Ý nghĩa |
| --- | --- | --- |
| `occluded_level` | `0`, `1`, `2`, `3` | Mức độ bị che khuất bề mặt nhìn thấy:<br>- `0`: Thấy toàn bộ, diện tích bị che = 0%.<br>- `1` (Che nhẹ): Bị che khuất từ 1% đến 35% diện tích (ví dụ xe ô tô đỗ sau chấn song sắt thưa trong `img_018`).<br>- `2` (Che vừa đến nặng): Bị che khuất từ 36% đến 75% diện tích (ví dụ người ngồi trên xe máy bị đầu xe hoặc người đi bên cạnh che mất nửa thân dưới trong `img_021`).<br>- `3` (Che cực nặng): Bị che khuất trên 75% diện tích, chỉ còn thấy một phần nhỏ (ví dụ chỉ thấy một phần đầu hoặc cánh tay) hoặc vật quá mờ nhạt. |
| `truncated` | `no`, `yes` | Mức độ bị cắt bởi mép ảnh:<br>- `no`: Toàn bộ đối tượng nằm hoàn toàn bên trong 4 mép ảnh.<br>- `yes`: Đối tượng bị cắt ngang thân bởi ít nhất một trong bốn mép ảnh (ví dụ xe ô tô chủ chỉ lộ một phần cạnh sườn ở góc gương trong `img_020`). |
| `can_review` | `true`, `false` | Đánh dấu cần thẩm định: Tick `true` (checkbox) khi gặp tình huống ca khó, ca biên mơ hồ mà quy tắc chưa quy định dứt khoát hoặc phân vân giữa 2 lớp (ví dụ xe 3 bánh sidecar `img_022`) để reviewer chốt lại trong buổi họp nhóm. |

## 3. Quy tắc vẽ box
1. Nguyên tắc bám sát phần nhìn thấy (Visible Bounding Box):
- Box phải vẽ ôm sát thực tế nhìn thấy của đối tượng. Tuyệt đối không vẽ phần không thấy hay tưởng tượng phần thân xe/người bị khuất sau vật cản khác để vẽ box to ra.

2. Quy tắc tách Người và Phương tiện gắn liền:
- Khi có người điều khiển hoặc ngồi trên xe máy: Bắt buộc tách thành các box độc lập. Vẽ 1 box person cho người (từ đỉnh mũ bảo hiểm đến hết phần chân/thân thấy được) và 1 box motorcycle cho xe máy (bao quanh toàn bộ phần khung xe, bánh xe nhìn thấy).
- Nếu 1 xe máy chở 2 người: Vẽ 2 box person riêng biệt và 1 box motorcycle.

3. Quy tắc xe có bộ phận phụ gắn liền:
- Với xe mô tô 3 bánh có thùng bên (sidecar), vẽ 1 box motorcycle duy nhất bao trùm toàn bộ cả xe 2 bánh lẫn khoang thuyền phụ gắn liền, vì đây là một khối phương tiện giao thông duy nhất khi lưu thông.

4. Quy tắc loại trừ vật thể phi thực thể giao thông:
- Tượng nghệ thuật / tượng danh nhân: Bỏ qua hoàn toàn, không gán person.
- Ảnh in trên pano, áp phích, biển quảng cáo: Bỏ qua hoàn toàn, không gán person cho hình ảnh 2D trên biển hiệu.
- Hình ảnh phản chiếu trong gương / bề mặt kính: Bỏ qua hoàn toàn xe nhìn thấy qua gương chiếu hậu.
- Xe đồ chơi, mô hình trang trí: Bỏ qua chiếc xe mô hình màu vàng gắn trên nóc xe, chỉ gán box cho chiếc ô tô thật bên dưới.

5. Quy tắc đám đông và vùng bỏ qua (ignore_region):
- Khi mật độ người và phương tiện quá dày đặc ở khoảng cách xa, nếu chiều cao các đối tượng < 20px hoặc xen kẽ đến mức không thể phân tách chính xác ranh giới từng người, không cố vẽ từng box nhỏ mà vẽ một box lớn bao trùm toàn bộ cụm đó và gán nhãn ignore_region.

6. Ngưỡng kích thước tối thiểu:
Mọi đối tượng có chiều cao nhỏ hơn 20 pixel (theo cấu hình min_box_height_px: 20 trong taxonomy.json) thì không gán nhãn, trừ khi nằm trong cụm được khoanh bởi ignore_region.

## 4. Ca khó
| Ảnh | Tình huống cụ thể | Rule ID | Quyết định | Lý do lập luận |
| --- | --- | --- | --- | --- |
| `img_017.jpg` | Tượng kỵ sĩ bằng đồng đội nón chóp giao thông đứng trước tòa nhà | `R-01` | KHÔNG gán nhãn cho người cưỡi ngựa | Tượng nghệ thuật điêu khắc, không phải người thật tham gia giao thông. Tránh lỗi False Positive nguy hiểm cho xe tự hành. |
| `img_018.jpg` | Ảnh chân dung người in trên biển quảng cáo lớn; bãi ô tô sau hàng rào sắt | `R-02` | - Ảnh quảng cáo: KHÔNG gán `person`.<br>- Dàn ô tô: Gán từng box `car`, đặt `occluded_level = 1`. | Hình ảnh 2D trên biển hiệu không phải người thật. Các ô tô phía dưới bị hàng rào song sắt che khuất nhẹ nhưng vẫn nhận diện rõ ràng toàn bộ hình dáng. |
| `img_019.jpg` | Xe diễu hành có tài xế người thật và linh vật chuột Danger Mouse; đám đông khán giả sau rào | `R-03` | - Gán `car` cho xe diễu hành, `person` cho tài xế.<br>- Linh vật chuột: KHÔNG gán.<br>- Đám đông khán giả ở xa: Khoanh 1 box `ignore_region`. | Linh vật là mô hình/trang phục thú bông, không phải người. Đám đông khán giả quá đông và ở xa không thể tách từng box đạt chuẩn IoU. |
| `img_020.jpg` | Xe ô tô phản chiếu qua gương chiếu hậu dính mưa; thân xe chủ lộ một phần cạnh viền | `R-04` | - Xe trong gương: KHÔNG gán.<br>- Thân xe chủ cạnh gương: KHÔNG gán (hoặc nếu gán thì `truncated = yes`, `can_review = true`). | Hình ảnh trong gương là ảnh ảo phản chiếu phía sau, không phải vật thể thật phía trước. Phần thân xe chủ chỉ lộ mép cắt không mang giá trị nhận diện giao thông. |
| `img_021.jpg` | Ngã tư đường phố Việt Nam lúc chập tối với hàng trăm xe máy chen chúc | `R-05` | - Xe máy & người ở cự ly gần: Tách riêng từng box `person` và `motorcycle`, đặt `occluded_level = 1` hoặc `2`.<br>- Vùng xe cộ ken đặc ở giữa ngã tư xa: Khoanh `ignore_region`. | Cự ly gần còn đủ độ phân giải và đặc trưng để tách box. Vùng xa mật độ quá đặc quánh, kích thước < 20px, nếu gán từng box sẽ gây nhiễu và sai lệch IoU giữa các annotator. |
| `img_022.jpg` | Xe mô tô 3 bánh có thùng bên (sidecar) chạy trên đường rừng đen trắng | `R-06` | - Gán 1 box `motorcycle` trùm toàn bộ xe và thùng phụ.<br>- Gán 1 box `person` cho người lái xe. | Thùng phụ gắn cố định vào xe mô tô tạo thành một khối phương tiện duy nhất. Người lái tách box riêng theo quy tắc chung. |
| `img_023.jpg` | Xe xích lô đạp chân chở người quay lưng; ô tô con đen chạy ngược chiều | `R-07` | - Gán 1 box `car` cho ô tô đen (`occluded_level = 0`).<br>- Xe xích lô: KHÔNG gán nhãn xe.<br>- Người đạp xích lô: Gán 1 box `person` (`occluded_level = 0`, `truncated = no`). | Taxonomy v0 không có lớp xe đạp hay xe xích lô. Tuy nhiên người điều khiển vẫn là thực thể tham gia giao thông cần bảo vệ nên phải gán `person`. |
| `img_024.jpg` | Xe mô hình đồ chơi màu vàng đặt trên giá nóc ô tô thật trong bảo tàng xe | `R-08` | - Chiếc ô tô AMC đen thật: Gán 1 box `car`.<br>- Chiếc xe mô hình vàng trên nóc: KHÔNG gán box riêng, và box của xe thật không bao gồm xe mô hình này. | Chiếc xe trên nóc chỉ là mô hình đồ chơi/hàng trang trí, không phải phương tiện giao thông. Box xe thật chỉ ôm sát thân xe thật. |

Câu hỏi nào chưa chốt: gắn `can_review` và ghi lên trưởng nhóm. Không tự đoán.

## 5. QC và phiên bản
Ngưỡng QC áp dụng, ai quyết ca mơ hồ, lịch sử thay đổi:

| Phiên bản | Thay đổi | Lý do (ca nào) |
|---|---|---|
| | | |
