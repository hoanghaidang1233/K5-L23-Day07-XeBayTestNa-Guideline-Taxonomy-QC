# Guideline gán nhãn — bản nháp cá nhân — 104

- Phiên bản: bản nháp cá nhân trước họp nhóm; chưa phải guideline v1.0 thống nhất.
- Ngày soạn: 2026-11-16.
- Người hoàn thiện và chịu trách nhiệm quyết định: 104.
- Tám ảnh được cung cấp để ghi ca: img_025.jpg đến img_032.jpg.
- Trạng thái khảo sát đủ 40 ảnh: [TỰ ĐIỀN: đã/chưa; nếu coach cho phạm vi khác, ghi rõ].

## 1. Mục tiêu

Nhãn dùng để huấn luyện mô hình phát hiện người và phương tiện trên ảnh đường phố 2D, phục vụ các bài toán như camera giao thông hoặc xe tự lái. Mỗi đối tượng thuộc phạm vi gán nhãn cần được mô tả bằng bounding box, lớp và thuộc tính theo cùng một quy tắc.

Bỏ sót người hoặc phương tiện có thể khiến mô hình học thiếu các tình huống quan trọng. Sai lớp làm giảm khả năng phân biệt đối tượng; box sai làm sai vị trí và kích thước mục tiêu. Gán vật không thuộc phạm vi có thể tạo nhãn dương tính không phù hợp. Mục tiêu của guideline là hạn chế cách hiểu khác nhau giữa những người gán.

## 2. Lớp và thuộc tính

### 2.1. Lớp

Bảng sau giữ danh sách và định nghĩa sơ bộ của v0. Các ô chưa điền là phần cần tự làm rõ, không được coi định nghĩa v0 là đã đầy đủ.

| Lớp | Định nghĩa |
|---|---|
| `car` | Ô tô con 
| `truck` | Xe tải |
| `van` | Xe thùng kín cỡ nhỏ |
| `motorcycle` | Xe máy, mô tô |
| `person` | Người | [TỰ ĐIỀN] |
| `ignore_region` | Vùng không cần gán từng vật |

### 2.2. Thuộc tính

| Thuộc tính | Giá trị | Ý nghĩa |
|---|---|---|
| `occluded_level` | `0`, `1`, `2`, `3` | Mức bị che: 0 là thấy rõ, 1 là bị che ít, 2 là bị che nhiều, 3 là không rõ. |
| `truncated` | `no`, `yes` | Vật bị cắt ở mép ảnh. |
| `can_review` | tick / không | Tick khi bạn không chắc, để người khác xem lại. |

## 3. Quy tắc vẽ box

Các dòng có [TỰ ĐIỀN] là câu hỏi cần chuyển thành quy tắc của chính học viên. Các ngưỡng ghi sẵn được lấy từ tài liệu lab.

| Rule ID | Chủ đề | Nội dung |
|---|---|---|
| R-01 | Phạm vi box | Theo khung đề: box ôm sát phần nhìn thấy. [TỰ ĐIỀN: cách bao các phần nhìn thấy bị tách rời do che; không suy đoán phần khuất] |
| R-02 | Che khuất | [TỰ ĐIỀN: phần nào được dùng xác định box; chọn mức occluded_level theo ngưỡng đã đề xuất] |
| R-03 | Cắt mép | Box không vượt phạm vi ảnh. [TỰ ĐIỀN: khi nào truncated=yes/no] |
| R-04 | Người và phương tiện | [TỰ ĐIỀN: số box, lớp, phần bao của từng box, cách xử lý nhiều người; thử trên ảnh thứ hai] |
| R-05 | Vật không thật | [TỰ ĐIỀN: quy tắc đối với tượng, ma-nơ-canh, hình in, phản chiếu, đồ chơi; lý do] |
| R-06 | Vật nhỏ | Theo QC_CHEATSHEET: vật cao dưới 20 px không gán từng vật, dùng ignore_region. [TỰ ĐIỀN: cách xác định chiều cao, phạm vi vùng] |
| R-07 | Đám đông / vật sát nhau | [TỰ ĐIỀN: điều kiện còn tách từng vật được và điều kiện dùng ignore_region; không dùng chỉ vì mất thời gian] |
| R-08 | Vật ngoài taxonomy | [TỰ ĐIỀN: cách xử lý thống nhất và trường hợp cần hỏi reviewer] |
| R-09 | Xe chở vật / xe khác | [TỰ ĐIỀN: tiêu chí tách đối tượng và giới hạn box của phương tiện chở] |
| R-10 | Xe chủ (ego) | [TỰ ĐIỀN: phạm vi xử lý phần xe mang camera xuất hiện trong ảnh] |
| R-11 | Tối / mờ | [TỰ ĐIỀN: điều kiện đủ cơ sở nhận dạng, điều kiện cần review hoặc loại theo quy tắc] |
| R-12 | Ca chưa chốt | Đánh dấu can_review trên box phù hợp hoặc ghi vấn đề cấp ảnh; nêu rõ câu hỏi. Không thay đổi guideline giữa Pha 3; đưa ca vào calibration. |


## 4. Ca khó

| Ảnh | Tình huống do tôi quan sát | Rule ID | Quyết định đề xuất để đối chiếu | Lý do |
|---|---|---|---|---|
| img_025.jpg | Phía xa dọc lối đi có nhiều người nhỏ, khó xác định ranh giới cơ thể. V0 chưa quy định rõ kích thước tối thiểu để gán. | R-06, R-11 | Đo chiều cao đối tượng trên ảnh gốc. Người cao dưới 20 px xử lý bằng `ignore_region` theo tài liệu lab; người đạt ngưỡng và nhận dạng được thì gán `person`. Ca không đủ rõ ghi để review. | Không kết luận kích thước chỉ bằng nhìn ảnh thu nhỏ. Cần ngưỡng đo được để mọi người xử lý nhất quán. |
| img_026.jpg | Hai người cùng ngồi trên một xe máy ở nửa dưới ảnh, che nhau và che một phần xe; xe bị cắt ở mép dưới. | R-02, R-03, R-04 | Với cụm này, đề xuất **hai box `person` và một box `motorcycle`**, mỗi box bao phần nhìn thấy của đúng đối tượng. Không gộp người và xe. Đặt `truncated=yes` cho xe do bị cắt mép; mức che của từng đối tượng cần đối chiếu ngưỡng đã chốt. | Người và xe là các đối tượng riêng. Che khuất không phải lý do để gộp; cắt mép và bị che cần đánh giá riêng. |
| img_027.jpg | Vật hình xe bán tải màu đen ở giữa ảnh nằm trên bệ trong nghĩa trang, có dấu hiệu là tượng/trang trí dạng xe. | R-05, R-12 | Đề xuất không gán `car` hoặc `truck` nếu xác nhận đây là tượng hình xe. Ghi trường hợp này vào quy tắc vật không thật; nếu chưa xác nhận được thì đưa ra review. | Hình dạng giống phương tiện chưa đủ để coi là phương tiện thật thuộc mục tiêu gán nhãn. Cần xét cả ngữ cảnh. |
| img_028.jpg | Xe bán tải màu nâu ở tiền cảnh chở vật hình ngựa. V0 chưa rõ xe bán tải thuộc `car` hay `truck`, và box xe có bao cả hàng chở không. | R-01, R-08, R-09 | Đề xuất một box cho xe, ôm phần xe nhìn thấy, **không mở rộng lên để bao vật hình ngựa**. Đề xuất xếp pickup vào `truck` nhưng cần nhóm chốt ranh giới lớp. Không gán vật hình ngựa vào các lớp hiện có. | Cần phân biệt phương tiện với hàng chở. Taxonomy hiện không có lớp ngựa; cách phân loại pickup phải được viết rõ để tránh mỗi người chọn khác nhau. |
| img_029.jpg | Ở giữa và bên phải ảnh có người đi xe máy và người đi xe đạp, cùng mang các thanh dài. Xe đạp không có trong taxonomy v0. | R-04, R-08, R-09 | Tách box người khỏi phương tiện; gán người vào `person`, xe máy vào `motorcycle`. Không kéo box người hoặc xe dài theo các thanh đang mang. Với xe đạp, ghi là vật ngoài taxonomy và đề xuất nhóm chốt cách bỏ qua hoặc bổ sung lớp trước khi gán chính thức. | Các thanh mang theo không phải bộ phận cơ thể hay phương tiện. Không ép xe đạp vào `motorcycle` chỉ vì cùng có hai bánh. |
| img_030.jpg | Bãi xe máy đông, nhiều xe chồng lấn, bị cắt mép và khó phân biệt bộ phận thuộc xe nào. | R-02, R-03, R-07 | Gán riêng `motorcycle` cho những xe còn xác định được từng đối tượng và phần nhìn thấy. Không gộp cả bãi thành một box xe máy. Với vùng không thể tách đáng tin cậy, đề xuất review và chỉ dùng `ignore_region` khi đáp ứng tiêu chí nhóm đã chốt. | Đông hoặc che nhiều không tự động đồng nghĩa phải bỏ qua. Tuy nhiên, suy đoán bộ phận của xe có thể tạo box sai hoặc trùng nhãn. |
| img_031.jpg | Cảnh đêm có ánh sáng phản chiếu trên đường ướt, một số đối tượng tối; xe buýt ở giữa bên phải không thuộc danh sách lớp v0. | R-05, R-08, R-11 | Gán các đối tượng thuộc taxonomy khi còn nhận dạng và xác định được phạm vi. Không gán vệt sáng phản chiếu thành phương tiện. Không ép xe buýt thành `truck` hoặc `van`; ghi vấn đề ngoài taxonomy để nhóm chốt. Ca quá tối, chưa đủ cơ sở thì review. | Ánh phản chiếu không phải đối tượng độc lập. Thiếu lớp trong taxonomy cần được giải quyết bằng quy tắc chung, không bằng chọn lớp gần giống. |
| img_032.jpg | Hàng người ở trạm chờ đứng sát nhau, một số bị người khác hoặc cột che. V0 chưa rõ khi nào tách từng người và khi nào dùng vùng bỏ qua. | R-02, R-07 | Đề xuất gán riêng `person` cho từng người còn phân biệt được, box bao phần nhìn thấy của người đó. Không dùng một box `person` cho cả hàng. Chỉ đề xuất `ignore_region` ở phần thực sự không tách được, theo tiêu chí nhóm thống nhất. | Đứng sát nhau không biến nhiều người thành một đối tượng. Gán riêng giúp giữ đúng số lượng và vị trí, còn vùng không tách được cần xử lý nhất quán. |

## 5. QC và phiên bản

### Ngưỡng theo tài liệu lab
- IAA: IoU trung bình trên các cặp box ghép được ≥ 0,85; đồng thuận lớp ≥ 95%. Luôn ghi số cặp làm mẫu số và số box không ghép được.
- QC_CHEATSHEET nêu ngưỡng IoU từng box: cao ≥ 50 px là ≥ 0,85; nhóm 20–50 px là ≥ 0,7. Tại đúng 50 px, áp dụng nhánh ≥ 50 px; đối chiếu rules/script nếu cần. Không nhầm ngưỡng từng box với ngưỡng IAA trung bình.
- Mẫu QC trong lab: các ảnh bị cờ và 20 ảnh ngẫu nhiên theo script; ghi số ảnh thực tế, không tự suy ra kích thước mẫu.
- Báo cáo cả tỷ lệ đối tượng lỗi/tổng đối tượng và ảnh lỗi/tổng ảnh.
- Hold nếu guideline chưa rõ hoặc dữ liệu hỏng. Nếu không Hold, Rework khi > 2% ảnh lỗi, có lỗi lặp lại cùng kiểu, hoặc IoU/IAA dưới ngưỡng. Chỉ Release khi các điều kiện cho phép đều đạt.
- Validator không thay thế kiểm tra bằng mắt: không kết luận được box ôm sát, bỏ sót hay cả nhóm cùng hiểu sai.
- Người chốt ca mơ hồ: 

### Lịch sử phiên bản
| Phiên bản | Thay đổi | Lý do |
|---|---|---|
| Bản nháp cá nhân 2026-11-16 | Bổ sung khung lớp, mã quy tắc, bảng tám ca và ngưỡng QC; quyết định cá nhân còn để trống | Chuẩn bị hoàn thiện Pha 1 từ mẫu và tài liệu lab |