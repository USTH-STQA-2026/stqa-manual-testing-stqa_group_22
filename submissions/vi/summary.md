# Báo cáo tóm tắt kiểm thử

> **Cơ sở:** test_case.docx, test_execution.docx, bug_report.docx (20/05/2026) — hệ thống dưới kiểm thử: **https://stqa.rbc.vn**

---

## 1. Thông tin chung

| **Hạng mục** | **Chi tiết** |
|---|---|
| **Nhóm** | STQA Group 22 |
| **Ngày báo cáo** | 20/05/2026 |
| **Môi trường** | Chrome (phiên bản mới nhất), Windows 11 |
| **Tài liệu tham chiếu** | SRS v1.0 |

---

## 2. Kết quả thực thi tổng quan

| **Chỉ số** | **Giá trị** |
|---|---|
| Tổng số TC | **39** |
| Đạt | **29** |
| Không đạt | **10** |
| Bị chặn | **0** |
| Chưa chạy | **0** |
| **Tỷ lệ đạt** | **75.0%** |
| **Số lỗi đã ghi nhận** | **8** |

### 2.1. Kết quả theo từng yêu cầu

| **Nhóm chức năng** | **Tổng TC** | **Đạt** | **Không đạt** | **Tỷ lệ đạt** | **Lỗi liên quan** |
|---|---|---|---|---|---|
| REQ-01 Đăng nhập | 8 | 8 | 0 | 100% | — |
| REQ-02 Xem danh sách sách | 2 | 2 | 0 | 100% | — |
| REQ-03 Tìm kiếm & lọc | 7 | 5 | 2 | 71.4% | BUG-03, BUG-06 |
| REQ-04 Mượn sách | 7 | 4 | 3 | 57.1% | BUG-02, BUG-04 |
| REQ-05 Trả sách | 3 | 2 | 1 | 66.7% | BUG-07 |
| REQ-06 Xử lý quá hạn | 4 | 3 | 1 | 75% | BUG-06 (TC-23) |
| REQ-07 Quản lý thành viên | 4 | 1 | 3 | 25% | BUG-07 (email) |
| REQ-08 Tra cứu bản ghi mượn | 4 | 3 | 1 | 75% | BUG-08 |

### 2.2. Ánh xạ TC không đạt → lỗi

| **TC không đạt** | **Mô tả hành vi thực tế (tóm tắt)** | **Lỗi** |
|---|---|---|
| TC-14 | Bộ lọc thể loại phân biệt hoa thường (nhập chữ thường/chữ in hoa thì không có kết quả) | BUG-03 |
| TC-17 | Thành viên **Suspended** nhận thông báo "expired" thay vì "suspended" | BUG-04 |
| TC-18 | Thành viên **Expired** dùng chung mẫu thông báo với trường hợp suspended | BUG-04 |
| TC-19 | Hệ thống cho phép mượn **cuốn thứ 4** (vượt giới hạn 3 sách) | BUG-02 |
| TC-23 | Kiểm tra quá hạn không ổn định + lỗi logic ở mốc ngày biên | BUG-06 (quá hạn) |
| TC-26 | Email không hợp lệ (tay.tran@emailcom) vẫn được chấp nhận khi tạo thành viên mới | BUG-07 (email) |
| TC-27 | Email trùng chỉ hiển thị thông báo "Invalid email" | BUG-07 (email) |
| TC-38 | Tìm kiếm + lọc kết hợp bỏ qua bộ lọc thể loại; từ khóa khớp với sách ngoài danh mục đã chọn | BUG-06 |
| TC-39 | Trả sách quá hạn hoàn tất lặng lẽ — không hiển thị cảnh báo quá hạn | BUG-07 |
| TC-40 | Member MEM002 có thể xem và trả bản ghi mượn của MEM006 — truy cập trái phép | BUG-08 |

> **Lưu ý:** **BUG-07** cũng ghi nhận hành vi bất thường ở luồng thêm thành viên hợp lệ (TC-25) theo đúng báo cáo lỗi.

### 2.3. Phân bố lỗi theo mức độ nghiêm trọng

| **Mức độ** | **Số lượng** | **Mã lỗi** |
|---|---|---|
| Nghiêm trọng | 1 | BUG-08 |
| Cao | 4 | BUG-02, BUG-06 (quá hạn), BUG-07 (email), BUG-07 (trả sách) |
| Trung bình | 2 | BUG-03, BUG-04, BUG-06 (tìm kiếm + lọc) |
| Thấp | 0 | — |

---

## 3. Kỹ thuật thiết kế kiểm thử đã dùng

| **Kỹ thuật** | **Áp dụng cho** | **Mô tả ngắn** |
|---|---|---|
| **EP** (Phân hoạch tương đương) | REQ-01 → REQ-08 | Chia lớp hợp lệ/không hợp lệ cho email, trạng thái sách, quyền vai trò, dữ liệu trùng lặp |
| **BVA** (Phân tích giá trị biên) | REQ-01, REQ-04, REQ-06, REQ-07 | Ngưỡng 3 sách, so sánh ngày quá hạn, email thiếu dấu `.` |
| **Bảng quyết định** | REQ-04 | Tổ hợp trạng thái sách × trạng thái thành viên × giới hạn mượn |

---

## 4. Đánh giá chất lượng sản phẩm

### 4.1. Khu vực ổn định

- Luồng đăng nhập hoạt động ổn định; hiển thị đúng vai trò (REQ-01).
- Chức năng xem danh sách sách và tìm kiếm theo từ khóa/tác giả đáp ứng kỳ vọng (REQ-02, phần lớn REQ-03).
- Chức năng trả sách hoạt động đúng ở cả trường hợp hợp lệ và từ chối (REQ-05).
- Tra cứu bản ghi mượn tuân thủ kiểm soát truy cập (REQ-08).

### 4.2. Rủi ro chính

- **Rò rỉ kiểm soát truy cập (Critical):** Thành viên có thể xem và trả bản ghi mượn của thành viên khác, vi phạm quyền riêng tư và tính toàn vẹn dữ liệu (REQ-08 — BUG-08).
- **Luồng mượn có lỗi nghiêm trọng:** vượt giới hạn 3 sách; thông báo trạng thái thành viên không chính xác (REQ-04).
- **Xử lý quá hạn không đáng tin cậy:** lỗi biên ngày + kết quả cập nhật không nhất quán giữa các lần chạy; không có cảnh báo khi trả sách quá hạn (REQ-06, REQ-05).
- **Kiểm tra dữ liệu thành viên không nhất quán:** chấp nhận email sai định dạng và thông báo lỗi không phản ánh đúng nguyên nhân (REQ-07).
- **Kết hợp tìm kiếm + lọc bị lỗi:** bộ lọc thể loại bị bỏ qua âm thầm khi từ khóa khớp với sách ngoài danh mục đã chọn (REQ-03).

---

## 5. Ưu tiên sửa lỗi đề xuất

| **Ưu tiên** | **Lỗi** | **Lý do** |
|---|---|---|
| P1 | BUG-08 | Lỗi nghiêm trọng về truy cập trái phép — thành viên xem và tác động vào dữ liệu mượn riêng tư của người khác |
| P1 | BUG-02 | Vi phạm ràng buộc nghiệp vụ cốt lõi (tối đa 3 sách mỗi thành viên) |
| P1 | BUG-07 (email) | Nguy cơ làm sai lệch dữ liệu thành viên + chặn dữ liệu hợp lệ / gây khó khăn cho đối soát trùng lặp |
| P1 | BUG-06 (quá hạn) | Ảnh hưởng trực tiếp đến logic quá hạn, nhắc hạn và tiền phạt |
| P2 | BUG-07 (trả sách) | Trả sách quá hạn nhưng không có cảnh báo — thủ thư không nhận biết được lượt trả trễ |
| P2 | BUG-04 | Gây hiểu nhầm về lý do bị từ chối, ảnh hưởng hỗ trợ vận hành |
| P2 | BUG-06 (tìm kiếm + lọc) | Bộ lọc bị bỏ qua một cách âm thầm; kết quả tìm kiếm gây hiểu lầm cho người dùng |
| P3 | BUG-03 | Trải nghiệm không nhất quán giữa bộ lọc và tìm kiếm (nhập đúng nhưng sai hoa/thường thì không có kết quả) |

---

## 6. Kết luận phát hành

Hệ thống **không nên phát hành** trong trạng thái hiện tại. Mặc dù tỷ lệ đạt chung là **75.0%**, nhưng một lỗi **Nghiêm trọng** về kiểm soát truy cập (BUG-08) cho phép bất kỳ thành viên đã xác thực nào cũng có thể xem và trả sách của thành viên khác — đây là lỗi bảo mật nền tảng. Ngoài ra, các lỗi mức cao tập trung ở những chức năng nghiệp vụ quan trọng (REQ-04, REQ-05, REQ-06, REQ-07), làm giảm đáng kể độ tin cậy vận hành.

Sau khi sửa các lỗi P1, cần chạy lại tối thiểu các TC sau:

- REQ-03: TC-38 (tìm kiếm + lọc kết hợp)
- REQ-04: TC-15 → TC-20
- REQ-05: TC-39 (cảnh báo trả sách quá hạn)
- REQ-06: TC-23, TC-24
- REQ-07: TC-25 → TC-28
- REQ-08: TC-40 (kiểm soát truy cập chéo giữa các thành viên)

---

## 7. Bài học rút ra (tùy chọn)

- **IDM và đọc kỹ SRS trước khi viết TC là bắt buộc, không phải tùy chọn.** Bảng IDM cho REQ-04 đã giúp nhóm tách riêng Suspended / Expired thành các phân hoạch khác nhau, từ đó tạo ra TC-17, TC-18 và TC-19 — cả ba đều Không đạt và phát hiện lỗi thật. Nếu không đọc kỹ SRS, nhóm có thể đã viết TC-19 với kết quả mong đợi sai (không biết đến giới hạn 3 sách từ BR-01), và BUG-07 có thể bị bỏ sót hoàn toàn (không biết email phải có dấu `.` trong phần domain theo BR-08).

- **BVA bắt được lỗi mà EP có thể bỏ sót.** BUG-02 (lỗi lệch biên: dùng `>` thay vì `>=` khi so sánh số sách đang mượn) và BUG-06 (bản ghi đến hạn đúng hôm nay không được đánh dấu Quá hạn) là các lỗi biên điển hình — EP chỉ kiểm tra giá trị nằm trong từng phân hoạch nên sẽ bỏ lỡ cả hai.

- **Bảng quyết định buộc phải suy nghĩ theo tổ hợp, không theo trình tự.** Khi xây dựng bảng cho REQ-04 (trạng thái sách × trạng thái thành viên × giới hạn mượn), nhóm nhận ra cần phân biệt *lý do* bị từ chối chứ không chỉ là *có bị từ chối hay không* — từ đó tạo ra TC-17 và TC-18 riêng biệt, qua đó phát hiện BUG-04: hệ thống dùng chung một thông báo "expired" cho hai trạng thái nghiệp vụ hoàn toàn khác nhau.

- **Mỗi TC nên kiểm thử đúng một điều kiện; mỗi lỗi nên mô tả rõ từng triệu chứng.** BUG-07 ảnh hưởng đến TC-25/26/27 theo ba hướng mâu thuẫn nhau (email hợp lệ bị từ chối, email sai được chấp nhận, email trùng bị báo nhầm là lỗi định dạng). Gộp chúng vào một TC hoặc một lỗi sẽ che khuất bản chất thực sự và khiến việc sửa đúng chỗ khó hơn.

- **Mức độ nghiêm trọng phải phản ánh tác động nghiệp vụ, không phải chỉ mức độ dễ thấy hay tần suất xuất hiện.** BUG-04 (thông báo sai) xuất hiện trong mọi lần chạy nhưng chỉ ở mức Trung bình vì không làm hỏng dữ liệu. BUG-02 (bỏ qua giới hạn mượn) cần một cấu hình nhất định để tái hiện nhưng ở mức Cao vì phá vỡ ràng buộc nghiệp vụ cốt lõi và có thể gây thất thoát tài nguyên thư viện.

- **Đề xuất cách sửa cụ thể tới mức toán tử giúp lập trình viên xử lý nhanh hơn.** Ghi rõ "đổi `>` thành `>=`" (BUG-02, BUG-06) hoặc "tách `DUPLICATE_EMAIL` và `INVALID_EMAIL_FORMAT`" (BUG-07) sẽ hữu ích hơn nhiều so với chỉ mô tả triệu chứng — đặc biệt khi nhóm làm việc không đồng bộ và khó trao đổi lại ngay với người kiểm thử.

---

## 8. Tuyên bố sử dụng AI

| **Công cụ** | **Phạm vi sử dụng** | **Phương pháp xác minh** |
|---|---|---|
| | | |

---

*Báo cáo tóm tắt kiểm thử — 20/05/2026*
