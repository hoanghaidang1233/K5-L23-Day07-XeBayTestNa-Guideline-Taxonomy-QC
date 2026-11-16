# Ghi chú cá nhân — <mã>

> Copy file này thành `submission/individual_notes/<mã>.md`. Mỗi thành viên một file, ngắn gọn (khoảng một trang), tự viết.

## 1. Pha 1: ba ca khó tôi tìm ra trong 40 ảnh
| Ảnh | v0 không trả lời được gì | Quyết định ban đầu của tôi |
|---|---|---|
| img_026.jpg | V0 chưa nói rõ hai người ngồi trên cùng một xe máy cần bao nhiêu box, box người có bao gồm xe không và xử lý phần xe bị cắt mép thế nào. | Tôi đề xuất vẽ hai box `person` và một box `motorcycle`, mỗi box ôm phần nhìn thấy của đúng đối tượng. Không gộp người với xe; đặt `truncated=yes` cho xe bị cắt ở mép dưới. |
| img_028.jpg | V0 chưa phân biệt rõ xe bán tải thuộc `car` hay `truck`, đồng thời chưa quy định box xe có bao gồm vật chở trên thùng hay không. | Tôi đề xuất xếp xe bán tải vào `truck`, cần nhóm thống nhất lại. Box chỉ bao phần xe, không mở rộng để bao vật hình ngựa trên thùng; vật hình ngựa không thuộc các lớp hiện có nên không gán nhãn riêng. |
| img_030.jpg | V0 chưa quy định khi nào phải gán từng xe trong bãi xe đông, khi nào được dùng `ignore_region` khi các xe che nhau và khó tách biệt. | Tôi đề xuất gán riêng `motorcycle` cho từng xe còn phân biệt được. Không gộp cả bãi thành một box; vùng không thể tách đáng tin cậy cần review và chỉ dùng `ignore_region` sau khi nhóm thống nhất tiêu chí. |

## 2. Pha 2: chỗ tôi bất đồng với nhóm

- Trong bản cá nhân, tôi đề xuất xếp xe bán tải ở `img_028.jpg` vào lớp `truck`. Box của xe không mở rộng để bao vật hình ngựa trên thùng.
- Ý kiến khác của thành viên trong nhóm: [Điền ý kiến thực tế; nếu không có bất đồng thì ghi rõ].
- Cuối cùng nhóm thống nhất: [Điền quyết định trong guideline v1.0].
- Tôi [đồng ý/chưa đồng ý] vì [nêu lý do cụ thể].

## 3. Pha 3: ảnh tôi đã gán

- Số ảnh được giao: 8 ảnh, từ `img_025.jpg` đến `img_032.jpg` [đối chiếu lại danh sách phân công].
- Số box đã vẽ: [Điền số thực tế].
- Số lần tôi tick `can_review`: [Điền số thực tế].
- Ca tôi thấy cần cân nhắc nhiều là `img_030.jpg`: nhiều xe máy đứng sát và che nhau, khó xác định phần nhìn thấy thuộc xe nào. Tôi cần phân biệt trường hợp còn gán riêng được với trường hợp cần review, tránh dùng `ignore_region` chỉ vì ảnh có nhiều đối tượng.

## 4. Pha 4–5: tôi lệch ở đâu

- Các ca liên quan đến tôi trong `calibration_candidates.csv`: [Điền case_id và tên ảnh].
- Nội dung bất đồng: [Sai lớp / lệch box / sai thuộc tính / bỏ sót / thừa].
- Nguyên nhân: [Guideline chưa rõ hoặc tôi áp dụng chưa đúng], vì [nêu quy tắc và điểm khác nhau cụ thể].
- Quyết định calibration tôi thấy quan trọng nhất: [Điền quyết định nhóm thực sự đã chốt].
- Quyết định này giúp [nêu tác dụng] và được ghi tại [mã quy tắc trong guideline v1.1].

> Nếu chưa chạy IAA hoặc họp calibration, ghi “Chưa thực hiện, sẽ bổ sung sau”; không kết luận chưa có lỗi.

## 5. Dùng AI

- Tôi sử dụng ChatGPT để giải thích quy trình lab. 
## 6. Bài học

- Lần sau, tôi sẽ viết rõ tiêu chí phân biệt lớp, phạm vi box và điều kiện dùng `ignore_region` ngay từ đầu, kèm ví dụ và phản ví dụ. Tôi sẽ thử cùng một quy tắc trên nhiều ảnh để phát hiện điểm mơ hồ trước khi bắt đầu gán nhãn.
