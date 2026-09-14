# Phiếu quy tắc gán nhãn — Ngày 2

**Họ và tên:** HOÀNG DƯƠNG THẢO HÀ
**MSSV:** 2A202502244
**Hình thức:** Cá nhân
**Mã cặp:** `SOLO`

## 1. Phạm vi

- Chỉ gán phương tiện thuộc bốn lớp bên dưới.
- Mỗi phương tiện là một hộp; không gộp nhiều xe.
- Không gán người, xe máy, xe đạp, biển báo hoặc phần phản chiếu.
- Vật thể quá nhỏ hoặc mờ đến mức không thể phân lớp có căn cứ: không đoán; ghi lý do vào nhật ký quyết định.

## 2. Bốn lớp cố định

| Mã | Lớp                 | Gán khi nhìn thấy                                         | Không gán vào lớp này                               |
| --: | -------------------- | ------------------------------------------------------------ | -------------------------------------------------------- |
|   0 | `car` (ô tô con) | sedan, hatchback, SUV, taxi, xe bán tải dùng như xe con  | xe có thùng/ben rõ; thân xe buýt; xe van thân hộp |
|   1 | `truck` (xe tải)  | thùng, ben, sàn hàng hoặc thiết bị công vụ rõ ràng | ô tô con; thân xe buýt; xe van kín một khối       |
|   2 | `bus` (xe buýt)   | thân xe khách dài, nhiều cửa sổ hoặc hàng ghế       | xe van nhỏ; xe tải; ô tô con                         |
|   3 | `van` (xe van)     | thân hộp nhỏ, kín, dùng chở người hoặc hàng        | thân xe buýt; khoang hàng tách biệt như xe tải    |

Thứ tự lớp là cố định: `0 car, 1 truck, 2 bus, 3 van`.

## 3. Hộp giới hạn

- Vẽ sát phần vật thể nhìn thấy.
- Không ước lượng phần bị xe khác che.
- Vật thể chạm mép ảnh vẫn được gán nếu đủ bằng chứng phân lớp.
- Không để hộp chứa nhiều nền hoặc nhiều phương tiện.

## 4. Ba thuộc tính

| Thuộc tính                             | Giá trị                                                         | Ý nghĩa                                   |
| ---------------------------------------- | ----------------------------------------------------------------- | ------------------------------------------- |
| `visibility` (mức nhìn thấy)        | `clear` (rõ), `occluded` (bị che), `unclear` (không rõ) | mức bằng chứng nhìn thấy               |
| `boundary` (quan hệ mép ảnh)        | `inside` (trong ảnh), `truncated` (bị cắt)                 | vật thể có bị mép ảnh cắt hay không |
| `review_state` (trạng thái xem lại) | `confident` (tự tin), `needs_review` (cần xem lại)         | đánh dấu quyết định cần quay lại    |

YOLO không lưu ba thuộc tính này. Vì vậy phải xuất thêm `CVAT for images 1.1` từ cùng công việc.

## 5. Ba tình huống mơ hồ

Hoàn thành trước khi xem bài của người khác hoặc bộ nhãn tham chiếu.

### Tình huống A — xe buýt hay xe van?

- Ảnh và mã vật thể: `drive_038.jpg`, vật thể box ID 47.
- Dấu hiệu nhìn thấy: Xe có nhiều cửa sổ liền kề nhưng thân nhỏ, hộp kín.
- Quy tắc áp dụng: Bus có thân xe khách dài, nhiều cửa sổ. Van cần thân hộp nhỏ, kín.
- Quyết định: Xe van
- Nếu vẫn thiếu bằng chứng, bạn sẽ làm gì? Phóng to 100%, kiểm tra lại hình dáng, cửa số và sử dụng quy tắc áp dụng.

### Tình huống B — xe tải hay xe van/ô tô con?

- Ảnh và mã vật thể: `drive_038.jpg`, vật thể box ID 45.
- Dấu hiệu nhìn thấy: Cabin giống van nhưng có cần cẩu/thiết bị lộ rõ trên sàn hở phía sau.
- Quy tắc áp dụng: Truck có thiết bị lộ rõ phía sau, không kín hết như xe van
- Quyết định: Xe truck
- Nếu vẫn thiếu bằng chứng, bạn sẽ làm gì? Kiểm tra phía sau cabin có hở hay chứa thiết bị lộ rõ hay không.

### Tình huống C — bị che, bị mép ảnh cắt hay không đủ bằng chứng?

- Ảnh và mã vật thể: `drive_033.jpg`, vật thể box ID 24.
- Dấu hiệu nhìn thấy khi phóng 100%: Lộ diện một phần thân xe ở sát mép phải, thân xe đã đi ra ngoài và bị cắt khỏi khung hình.
- Giá trị `visibility`: `clear`
- Giá trị `boundary`: `truncated`
- Trạng thái `review_state`: `confident`
- Lý do: Phương tiện bị cắt khỏi khung hình nhưng vẫn thấy được hình dáng của phương tiện và phần bị cắt không quá nhiều để ảnh hưởng đến quyết định.

## 6. Xác nhận tự kiểm tra

- [X] Đã rà đủ bốn ảnh.
- [X] Đã kiểm vật thể thiếu và trùng.
- [X] Đã kiểm lớp và hình học từng hộp.
- [X] Mỗi hộp có đủ ba thuộc tính.
- [X] Đã xử lý mọi hộp `needs_review`.
- [X] Đã hoàn thành ba tình huống trước khi xem nguồn đối chiếu.
- [X] Nếu làm theo cặp, hai người đã xuất bài độc lập trước khi trao đổi.
- [X] Nếu làm cá nhân, bài riêng đã được kiểm trước khi nhận bộ tham chiếu.
- [X] Số vật thể thực tế: 40–60 là mục tiêu khối lượng, không phải điểm cắt.
