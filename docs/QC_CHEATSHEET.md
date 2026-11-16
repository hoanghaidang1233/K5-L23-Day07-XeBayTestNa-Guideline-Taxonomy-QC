# Đọc số đo và ra quyết định QC

| Chỉ số | Đơn vị và mẫu số |
|---|---|
| Độ phủ | Lượt gán hợp lệ trên tổng lượt trong `assignment.csv`. Ảnh không có đối tượng vẫn tính vào độ phủ. |
| IAA | IoU trung bình và tỷ lệ cùng lớp chỉ tính trên cặp box ghép 1–1 không phân biệt lớp, với IoU ghép từ 0.5. Với ba người, một ảnh đóng góp ba cặp người. Không có cặp thì ghi N/A. |
| Ngưỡng IAA của lab | IoU TB ≥ 0.85, cùng lớp ≥ 95%. Dùng để tìm điểm cần calibration. Giữ kết quả trước calibration trong hồ sơ. |
| Box bất đồng | Đếm riêng box chưa ghép và box nằm trong vùng ignore của cả hai người. Nếu chỉ một người đặt vùng phủ box thì đưa ca vào calibration. Rà khác biệt vùng ignore và kiểm ca cả nhóm cùng sai bằng ảnh tham chiếu. |
| Ngưỡng IoU theo box của lab | Cặp có chiều cao box trung bình từ 50 px dùng 0.85, nhỏ hơn dùng 0.7. Vật cao dưới 20 px cần đối chiếu quy tắc vật nhỏ và ignore. |
| Lỗi theo ảnh | Ảnh có ít nhất một lỗi trên số ảnh đã duyệt. Release của lab dùng ngưỡng ≤ 2% và yêu cầu duyệt toàn bộ. Lô tối đa 40 ảnh nên một ảnh lỗi đã vượt ngưỡng. |
| Lỗi annotation đối tượng | Số ID annotation đối tượng hiện có bị lỗi trên số annotation đối tượng trong ảnh đã duyệt. Đếm lỗi bỏ sót riêng theo vùng cho mỗi vật, vì chưa có annotation. |

Ghi số đo dạng `tử số/mẫu số`. Với vòng demo lấy mẫu, so sánh nhóm ca bị cờ và nhóm ngẫu nhiên riêng. Kết quả mẫu áp dụng cho ảnh đã duyệt. Xem [NIST về lấy mẫu theo lô](https://itl.nist.gov/div898/handbook/pmc/section2/pmc21.htm) hoặc [ISO 2859-1:2026](https://www.iso.org/standard/85464.html) để hiểu cách xây dựng kế hoạch nghiệm thu bằng mẫu.

## Gợi ý quyết định

1. **Hold** nếu thiếu bằng chứng, dữ liệu/guideline còn vấn đề, review lệch phiên bản hoặc mới chỉ lấy mẫu.
2. **Rework** nếu tỷ lệ ảnh lỗi vượt ngưỡng hoặc có lỗi lặp theo quy tắc của lab. Ghi nguyên nhân, người sửa và kết quả kiểm lại.
3. **Release** khi reviewer xác nhận bằng chứng đạt yêu cầu bàn giao lô thực hành.

Một ảnh có thể có nhiều lỗi. Đọc quy định từng dòng phiếu ở [README của script](../scripts/README.md#phiếu-qc).
