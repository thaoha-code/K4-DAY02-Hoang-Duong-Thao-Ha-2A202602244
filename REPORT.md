# Báo cáo — Ngày 2: phát hiện vật thể

**Họ và tên:** HOÀNG DƯƠNG THẢO HÀ
**MSSV:** 2A202602244
**Hình thức:** Cá nhân
**Mã cặp:** `SOLO`

## 1. Bài độc lập và nguồn dữ liệu

- Mã SHA-256 của ZIP ảnh được cấp: `f7d99888f21440fb0374d84962b93213bd8c14e665d093cc8d37f4c61b71ed33`
- Bốn mã ảnh: `drive_008`, `drive_022`, `drive_033`, `drive_038`
- Số vật thể thực tế: 62 (drive_008: 14, drive_022: 5, drive_033: 21, drive_038: 22)
- Mã SHA-256 của gói YOLO của bạn: `3826a8ea8096e4f1c53403731d99221132691f34980e160cf09651aa8b51d986`
- Mã SHA-256 của gói CVAT gốc của bạn: `ab67b33fbc73518f607d8f22c7f4b36dc9fc35ebeccc784dcdc67746524168f1`
- Nguồn đối chiếu: Bộ nhãn đối chiếu do Lab Coach cung cấp.
- Mã SHA-256 của gói đối chiếu: `c8bbc767d8bb9a29f4ca5abf0c3516e5c2af94c58143a980b0148cfe0b500d2b`
- Nếu làm cá nhân, ghi mã lần phát và thời điểm nhận bộ tham chiếu: Nhận bộ tham chiếu sau khi đã hoàn thành file export.

Giải thích vì sao bài của bạn vẫn độc lập trước khi đối chiếu: Việc gán nhãn 4 ảnh trên CVAT, tự kiểm tra và xuất 2 gói dữ liệu (`Ultralytics YOLO Detection` và `CVAT for images 1.1`) được hoàn thành độc lập và lưu trước khi nạp bộ nhãn tham chiếu vào.

## 2. Quyết định phân lớp

| Ảnh/vật thể                                             | Lớp    | Dấu hiệu nhìn thấy                                                                   | Quy tắc áp dụng                                                    |
| ---------------------------------------------------------- | ------- | ---------------------------------------------------------------------------------------- | --------------------------------------------------------------------- |
| `drive_038` / Vật thể [490.94, 107.00, 581.31, 208.30] | `van` | Thân hộp nhỏ, không có sự phân tách rõ ràng giữa khoang lái và khoang hàng | Thân hộp nhỏ kín, dùng chở người hoặc hàng thì gán`van` |
| `drive_008` / Vật thể [285.62, 250.18, 350.85, 358.17] | `van` | Xe có dáng thân hộp kín, thành xe phẳng và vuông vức                           | Xe van thân hộp kín một khối gán lớp`van`                    |

Nêu một ví dụ cho thấy lớp và thuộc tính là hai loại thông tin khác nhau:
Lớp (`car`, `truck`, `bus`, `van`) phân loại bản chất phương tiện. Thuộc tính (`visibility`, `boundary`) mô tả trạng thái vật lý của chiếc xe đó trong khung hình, ví dụ như bị che khuất (`occluded`) hay bị cắt khung (`truncated`).

## 3. Tự kiểm tra và sửa nhãn

| Trước khi sửa                                                                                                  | Loại lỗi                              | Cách phát hiện                                                | Sau khi sửa và quy tắc                                                                                                |
| ----------------------------------------------------------------------------------------------------------------- | --------------------------------------- | ---------------------------------------------------------------- | ------------------------------------------------------------------------------------------------------------------------ |
| Gán`confident`cho vật thể ô tô con sát mép phải ảnh `drive_033` (Box 24 / xtl="631.25" ytl="159.47") | Thuộc tính / Không đủ bằng chứng | Phóng to viền ảnh lúc rà soát lỗi tổng thể.             | Chuyển thành`needs_review` do chỉ lộ một dải mui xe hẹp ở mép phải, thiếu cứ liệu phân lớp chính xác. |
| Vẽ viền bao luôn cả bóng đổ xuống lòng đường                                                          | Hình học hộp                         | Bật/tắt hộp nhãn liên tục để đối chiếu ranh giới xe. | Thu hẹp viền hộp sát vào mép khung kim loại của xe, theo quy tắc "vẽ sát phần vật thể nhìn thấy".        |

- Số hộp `needs_review` trước và sau khi kiểm: Trước khi kiểm tra có 3 hộp `needs_review` (1 hộp trong `drive_008`, 1 hộp trong `drive_033`, 1 hộp trong `drive_038`).
- Một quyết định chưa đủ bằng chứng và cách bạn xin hỗ trợ: Quyết định với chiếc xe (Box 24, `xtl="631.25" ybr="189.95"`) ở sát mép phải ảnh `drive_033.jpg`. Đặt câu hỏi cho Lab Coach để chốt xem với phần hiển thị bị mép ảnh cắt (`truncated`) mất phần lớn diện tích có nên giữ nhãn hay xóa hẳn.

## 4. Một dòng nhãn YOLO

- Dòng `class x_center y_center width height`: `1 0.939477 0.459641 0.087109 0.111125`.
- Tên lớp và tọa độ điểm ảnh `xyxy`:
  * Lớp: `1` tương ứng với `truck`.
  * Tọa độ `xyxy` pixel trên ảnh kích thước 640x640: Mã vật thể (hộp xe tải bên góc phải) ở tọa độ `[573.39, 258.61, 629.14, 329.73]`.
- Vì sao dòng đúng định dạng vẫn có thể sai lớp, phạm vi hoặc hình học?
  Dòng nhãn YOLO chỉ lưu trữ các con số tọa độ và ID lớp. Script kiểm tra tự động xác nhận tọa độ nằm trong giới hạn [0, 1], nhưng không thể biết người gán có vẽ sát viền thực tế hay không hoặc gán nhầm ID lớp.

## 5. Huấn luyện và dự đoán thử

- Ba mã ảnh huấn luyện: `drive_022`, `drive_033`, `drive_038`.
- Mã ảnh thẩm định: `drive_008`.
- Mô tả một dự đoán trong `detect_result.jpg`: Mô hình khoanh nhầm một vùng bóng râm tối dưới lòng đường thành xe con (`car`) với độ tự tin thấp, hoặc không phát hiện ra các ô tô con bị che lấp quá sâu.
- Dự đoán đó gợi ý cần kiểm lại quy tắc hoặc dữ liệu nào? Gợi ý cần phải kiểm tra lại tính nhất quán trong quy tắc vẽ nhãn đối với các vật thể bị che khuất. Đồng thời, mô hình cần lượng dữ liệu huấn luyện lớn hơn nhiều để học được đặc trưng phân biệt hình học xe với bóng râm.
- Minh chứng nào có thể bác bỏ nhận định của bạn? Tệp `annotations.xml` của ảnh `drive_008.jpg` hoàn toàn không có nhãn nào được khoanh vào vùng bóng râm đó, chứng tỏ nguyên nhân do khả năng dự đoán của mô hình chưa đủ khái quát hóa, không phải do gán sai nhãn gốc.
- Vì sao kết quả trên bốn ảnh không phải phép đánh giá mô hình dùng thực tế? Mức độ chính xác trung bình (mAP) trên 4 ảnh là tín hiệu chẩn đoán lỗi dữ liệu, không phải điểm đạt và không chứng minh nhãn đúng hay khả năng đánh giá mô hình dùng trong thực tế\.

## 6. Đối chiếu nhãn

- Số hộp ghép được: 44
- IoU trung bình và trung vị: Trung bình 0.885035, Trung vị 0.890076
- Mức đồng thuận lớp: 0.704545 (tương đương 70.45%)
- Số hộp phía bạn không ghép được: 18
- Số hộp phía đối chiếu không ghép được: 6
- Một điểm khác biệt cụ thể: Gói nhãn độc lập có 18 hộp không ghép được với bộ tham chiếu, cho thấy việc gán nhãn của em có thể đã bao gồm nhiều vật thể nhỏ hoặc bị che khuất sâu ở hậu cảnh mà bộ tham chiếu bỏ qua.
- Quy tắc hoặc hành động sửa phát sinh: Rà soát lại quy tắc không gán vật thể quá nhỏ hoặc mờ đến mức không phân lớp được có căn cứ. Tiến hành vào CVAT xóa bớt các hộp bị nhiễu hoặc ở quá xa này.
- Vì sao mức đồng thuận cao không chứng minh mọi nhãn đều đúng? Mức đồng thuận giữa nhãn đối chiếu và bài làm đo khả năng tái lập quy tắc, không chứng minh cả hai đều đúng tuyệt đối (vì cả hai hoàn toàn có thể cùng áp dụng sai một quy tắc).

## 7. Kiểm tra kho GitHub cá nhân

- [X] Có phiếu quy tắc với ba tình huống mơ hồ.
- [X] Có kết quả kiểm hai gói xuất.
- [X] Có thông tin lần huấn luyện và ảnh dự đoán.
- [X] Có tóm tắt, bảng và ảnh phủ của bước đối chiếu.
- [X] Không có gói xuất thô, bộ nhãn tham chiếu hoặc trọng số mô hình.
- [X] Không có dữ liệu VinFast/khách hàng/ảnh cá nhân/mật khẩu/mã truy cập.

Minh chứng mạnh nhất trong bài và câu hỏi còn lại cho Lab Coach:
Minh chứng mạnh nhất là ảnh phủ `comparison_overlay.png` và biểu đồ IoU với độ đồng thuận cao cho thấy việc vạch hộp hình học của bài làm sát với bộ tham chiếu.
Câu hỏi: Đối với các trường hợp xe bị che khuất hơn 80%, nếu dùng thuộc tính `occluded` và trạng thái `needs_review`, hệ thống mô hình sẽ quyết định xử lý loại bỏ hay đưa vào huấn luyện các hộp nhãn này như thế nào?
