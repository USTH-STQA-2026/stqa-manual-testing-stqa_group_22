# Kết quả thực thi kiểm thử

> **Hướng dẫn:** Chạy từng TC trên hệ thống tại https://stqa.rbc.vn và ghi lại kết quả thực tế.
>
> **Định nghĩa đánh giá:**
> - **Đạt** — kết quả thực tế khớp với kết quả mong đợi
> - **Không đạt** — kết quả thực tế sai → tạo báo cáo lỗi
> - **Bị chặn** — không thể thực thi do có lỗi chặn ở nơi khác
> - **Chưa chạy** — chưa được thực thi

| **Thông tin** | |
|---|---|
| **Nhóm** | STQA Group 22 |
| **Ngày thực thi** | 20/05/2026 |
| **Trình duyệt** | Chrome (phiên bản mới nhất) |
| **Hệ điều hành** | Windows 11 |

---

## Kết quả chi tiết

| **TC ID** | **Nhóm chức năng** | **Kết quả mong đợi (tóm tắt)** | **Kết quả thực tế** | **Đánh giá** | **Bằng chứng** | **Lỗi** |
|---|---|---|---|---|---|---|
| TC-01 | Đăng nhập | Librarian đăng nhập thành công, hiển thị Nguyen Thu Thu | Đăng nhập thành công, AppBar hiển thị đúng tên/vai trò của Librarian | **Đạt** | | |
| TC-02 | Đăng nhập | Member đăng nhập thành công, hiển thị Nguyen Hoc Ba | Đăng nhập thành công, AppBar hiển thị Nguyen Hoc Ba (Member) | **Đạt** | | |
| TC-03 | Đăng nhập | Email không tồn tại → bị từ chối | Đăng nhập thất bại (email và mật khẩu không được hệ thống nhận diện) | **Đạt** | | |
| TC-04 | Đăng nhập | Sai mật khẩu → thông báo "Incorrect password" | Hiển thị "Incorrect password", trang không thay đổi | **Đạt** | | |
| TC-05 | Đăng nhập | Email/mật khẩu trống → thông báo trường trống | Hiển thị "Please enter email and password" (theo SRS; khi kiểm thử thủ công app hiển thị *account*) | **Đạt** | | |
| TC-06 | Đăng nhập | MEM004 có trạng thái Suspended đăng nhập | Đăng nhập thành công — Le Can Cu (Member) | **Đạt** | | |
| TC-07 | Đăng nhập | MEM005 có trạng thái Expired đăng nhập | Đăng nhập thành công — Pham Trung Binh (Member) | **Đạt** | | |
| TC-08 | Xem sách | Danh sách đầy đủ với mọi trường, BOOK001/003 đúng trạng thái | Có 20 sách, đầy đủ các trường: Name/Author/Genre/Year/Status | **Đạt** | | |
| TC-09 | Xem sách | Sau khi mượn → trạng thái cập nhật ngay | BOOK001 đổi sang Borrowed ngay sau khi mượn | **Đạt** | | |
| TC-10 | Tìm kiếm | Tìm "Flutter" → BOOK005, BOOK013, BOOK019 | Hiển thị đúng các sách có chứa Flutter | **Đạt** | | |
| TC-11 | Tìm kiếm | Tìm "Nguyen" → sách theo tác giả Nguyen | Kết quả đúng theo tác giả | **Đạt** | | |
| TC-12 | Tìm kiếm | flutter = FLUTTER (không phân biệt hoa thường) | Cả hai lần tìm đều trả về cùng kết quả: BOOK005, BOOK013, BOOK019 | **Đạt** | | |
| TC-13 | Tìm kiếm | Không có kết quả → hiển thị thông báo | Hiển thị "No books found" | **Đạt** | | |
| TC-14 | Bộ lọc | Bộ lọc không phân biệt hoa thường | `Technology` hiển thị đúng; `technology`/chữ in hoa → "No books found" | **Không đạt** | | **BUG-02** |
| TC-15 | Mượn sách | Mượn BOOK002 cho MEM002 — thành công | Mượn thành công, bản ghi ở trạng thái "Borrowed", ngày hẹn trả +14 ngày | **Đạt** | | |
| TC-16 | Mượn sách | BOOK003 đã được mượn → bị từ chối | Không thể mượn một cuốn sách đã được mượn | **Đạt** | | |
| TC-17 | Mượn sách | MEM004 Suspended → thông báo suspended | Hiển thị "Member has expired…" (sai lý do) | **Không đạt** | | **BUG-03** |
| TC-18 | Mượn sách | MEM005 Expired → thông báo expired | Cùng một thông báo "expired", không phân biệt với suspended | **Không đạt** | | **BUG-03** |
| TC-19 | Mượn sách | Mượn cuốn thứ 4 → bị từ chối (giới hạn 3 sách) | Cho phép mượn cuốn thứ 4; thông báo giới hạn chỉ xuất hiện khi thành viên đã có đúng 3 sách | **Không đạt** | | **BUG-01** |
| TC-20 | Mượn sách | BOOK007 Lost → bị từ chối | Không thể mượn sách bị mất | **Đạt** | | |
| TC-21 | Trả sách | Trả BOOK013 → "Returned", sách Available | Trả thành công, trạng thái được cập nhật đúng | **Đạt** | | |
| TC-22 | Trả sách | Trả sách chưa được mượn → bị từ chối | Hệ thống từ chối, BOOK008 vẫn ở trạng thái Available | **Đạt** | | |
| TC-23 | Xử lý quá hạn | Check Overdue → BR001 được đánh dấu Overdue | Lần 1: 1 bản ghi; lần 2: 0 bản ghi; bản ghi có hạn đúng hôm nay không được đánh dấu | **Không đạt** | | **BUG-04** |
| TC-24 | Xử lý quá hạn | MEM002 xem bản ghi quá hạn của mình | BR001 hiển thị Overdue trong mục bản ghi của tôi | **Đạt** | | |
| TC-25 | Quản lý thành viên | Thêm thành viên hợp lệ → thành công | Thành viên mới không được thêm; hiển thị "Invalid email" | **Không đạt** | | **BUG-05** |
| TC-26 | Quản lý thành viên | Email tay.tran@emailcom → bị từ chối | Tạo thành viên thành công (email không hợp lệ vẫn được chấp nhận) | **Không đạt** | | **BUG-05** |
| TC-27 | Quản lý thành viên | Email trùng → thông báo trùng | Chỉ hiển thị "Invalid email" | **Không đạt** | | **BUG-05** |
| TC-28 | Quản lý thành viên | Thành viên không có quyền thêm thành viên mới | Không hiển thị tab Members | **Đạt** | | |
| TC-29 | Tra cứu bản ghi | Librarian xem bản ghi của MEM003 | Hiển thị đúng mã bản ghi, sách, ngày mượn, ngày hẹn trả và trạng thái | **Đạt** | | |
| TC-30 | Tra cứu bản ghi | Member chỉ xem bản ghi của chính mình | Chỉ hiển thị bản ghi của MEM002, không có bản ghi của thành viên khác | **Đạt** | | |
| TC-31 | Đăng nhập | Chỉ trống email → bị từ chối kèm thông báo | Hiển thị "Please enter email and password". Trang không thay đổi. | **Đạt** | | |
| TC-32 | Tìm kiếm | Xóa tìm kiếm → khôi phục lại toàn bộ 20 sách | Toàn bộ 20 sách hiển thị lại sau khi xóa ô tìm kiếm | **Đạt** | | |
| TC-33 | Mượn sách | Mượn tại ngưỡng BVA (lần mượn thứ 2) | Mượn thành công; thành viên hiện có 2 lượt mượn đang hoạt động, vẫn trong giới hạn 3 sách | **Đạt** | | |
| TC-34 | Xử lý quá hạn | Bản ghi có dueDate = ngày mai vẫn là "Borrowed" | Bản ghi có hạn trong tương lai vẫn ở trạng thái "Borrowed" sau khi Check Overdue | **Đạt** | | |
| TC-35 | Xử lý quá hạn | Librarian xem TẤT CẢ bản ghi quá hạn của mọi thành viên | Librarian xem được toàn bộ bản ghi quá hạn của mọi thành viên sau khi Check Overdue | **Đạt** | | |
| TC-36 | Tra cứu bản ghi | Bản ghi đã trả hiển thị đúng trạng thái + đầy đủ trường | Bản ghi đã trả hiển thị "Returned" với Record ID, Book, Borrow Date, Due Date | **Đạt** | | |
| TC-37 | Tìm kiếm | Tìm kiếm + bộ lọc: từ khóa không nằm trong danh mục → không có kết quả | Hệ thống bỏ qua bộ lọc thể loại và hiển thị kết quả từ mọi thể loại có chứa từ khóa | **Không đạt** | | **BUG-06** |
| TC-38 | Trả sách | Trả sách quá hạn → hiển thị cảnh báo quá hạn | Trả sách hoàn tất lặng lẽ, không có cảnh báo hay thông báo quá hạn | **Không đạt** | | **BUG-07** |
| TC-39 | Tra cứu bản ghi | Member không thể xem/trả bản ghi của thành viên khác | MEM002 có thể tìm mã của MEM006 và thấy bản ghi của MEM006; nút Return vẫn truy cập được | **Không đạt** | | **BUG-08** |

---

## Kết quả tổng hợp

| **Chỉ số** | **Giá trị** |
|---|---|
| **Tổng số TC** | 39 |
| **Đạt** | 29 |
| **Không đạt** | 10 |
| **Bị chặn** | 0 |
| **Chưa chạy** | 0 |
| **Tỷ lệ đạt** | 75.0% |
| **Số lỗi phát hiện** | 8 |

---

## Kết quả theo nhóm chức năng

| **Nhóm** | **Tổng TC** | **Đạt** | **Không đạt** | **Tỷ lệ đạt** |
|---|---|---|---|---|
| Đăng nhập | 8 | 8 | 0 | 100% |
| Xem sách | 2 | 2 | 0 | 100% |
| Tìm kiếm & lọc | 7 | 5 | 2 | 71.4% |
| Mượn sách | 7 | 4 | 3 | 57.1% |
| Trả sách | 3 | 2 | 1 | 66.7% |
| Xử lý quá hạn | 4 | 3 | 1 | 75% |
| Quản lý thành viên | 4 | 1 | 3 | 25% |
| Tra cứu bản ghi mượn | 4 | 3 | 1 | 75% |

---

## Bằng chứng chất lượng sản phẩm

### 4.1. Khu vực ổn định

- Luồng đăng nhập hoạt động ổn định; hiển thị đúng vai trò (REQ-01).
- Chế độ xem danh sách sách và tìm kiếm theo từ khóa/tác giả đáp ứng kỳ vọng (REQ-02, phần lớn REQ-03).
- Chức năng trả sách hoạt động đúng cho cả trường hợp hợp lệ và bị từ chối (REQ-05).
- Tra cứu bản ghi mượn tuân thủ kiểm soát truy cập (REQ-08).

### 4.2. Rủi ro chính

- **Rò rỉ kiểm soát truy cập (Critical):** Thành viên có thể xem và thực hiện trả sách cho bản ghi của thành viên khác, vi phạm quyền riêng tư và tính toàn vẹn dữ liệu (REQ-08 — BUG-08).
- **Luồng mượn có lỗi nghiêm trọng:** vượt giới hạn 3 sách; thông báo trạng thái thành viên không chính xác (REQ-04).
- **Xử lý quá hạn không ổn định:** lỗi biên ngày + kết quả cập nhật không nhất quán giữa các lần chạy; không có cảnh báo khi trả sách quá hạn (REQ-06, REQ-05).
- **Kiểm tra dữ liệu thành viên không nhất quán:** chấp nhận email không hợp lệ và thông báo lỗi không phản ánh đúng nguyên nhân (REQ-07).
- **Kết hợp tìm kiếm + lọc bị lỗi:** bộ lọc thể loại bị bỏ qua một cách âm thầm khi từ khóa khớp với sách ngoài danh mục đã chọn (REQ-03).

---

## Ưu tiên sửa lỗi đề xuất

| **Ưu tiên** | **Lỗi** | **Lý do** |
|---|---|---|
| P1 | BUG-08 | Lỗi nghiêm trọng về truy cập trái phép — thành viên xem và tác động lên dữ liệu mượn riêng tư của người khác |
| P1 | BUG-01 | Vi phạm ràng buộc nghiệp vụ cốt lõi (tối đa 3 sách mỗi thành viên) |
| P1 | BUG-05 | Nguy cơ làm sai lệch dữ liệu thành viên + chặn dữ liệu hợp lệ / gây khó khăn cho đối soát trùng lặp |
| P1 | BUG-06 | Ảnh hưởng trực tiếp tới logic quá hạn, nhắc hạn và tiền phạt |
| P2 | BUG-07 | Trả sách quá hạn nhưng không có cảnh báo — thủ thư không nhận diện được lượt trả trễ |
| P2 | BUG-03 | UX không nhất quán giữa bộ lọc và tìm kiếm (nhập đúng nhưng sai hoa thường thì không có kết quả) |
| P2 | BUG-04 | Gây hiểu nhầm về lý do bị từ chối, ảnh hưởng hỗ trợ vận hành |
| P3 | BUG-02 | Bộ lọc không phân biệt hoa thường (đúng input nhưng sai chữ hoa/thường thì không có kết quả) |

---

## Kết luận phát hành

Hệ thống **không nên phát hành** ở trạng thái hiện tại. Mặc dù tỷ lệ đạt chung là **75.0%**, nhưng một lỗi **Critical** về kiểm soát truy cập (BUG-08) cho phép bất kỳ thành viên đã xác thực nào cũng có thể xem và trả sách của thành viên khác — đây là lỗi bảo mật nền tảng. Bên cạnh đó, các lỗi mức cao tập trung ở những chức năng nghiệp vụ quan trọng (REQ-04, REQ-05, REQ-06, REQ-07), làm giảm đáng kể độ tin cậy vận hành.

Sau khi sửa các lỗi P1, cần chạy lại tối thiểu các TC sau:

- REQ-03: TC-37
- REQ-04: TC-15 → TC-20
- REQ-05: TC-38
- REQ-06: TC-23, TC-24
- REQ-07: TC-25 → TC-28
- REQ-08: TC-39

---

## Bài học rút ra (tùy chọn)

- **IDM và đọc kỹ SRS trước khi viết TC là bắt buộc, không phải tùy chọn.** Bảng IDM cho REQ-04 đã giúp nhóm tách riêng Suspended / Expired thành các phân hoạch khác nhau, từ đó tạo ra TC-17, TC-18 và TC-19 — cả ba đều Không đạt và phát hiện lỗi thật. Nếu không đọc kỹ SRS, nhóm có thể đã viết TC-19 với kết quả mong đợi sai (không biết đến giới hạn 3 sách từ BR-01), và BUG-05 có thể bị bỏ sót hoàn toàn (không biết email phải có dấu `.` trong phần domain theo BR-08).

- **BVA bắt được lỗi mà EP không thấy.** BUG-01 (lỗi lệch biên: dùng `>` thay vì `>=` khi so sánh số sách đang mượn) và BUG-04 (bản ghi đến hạn đúng hôm nay không được đánh dấu Quá hạn) là các lỗi biên điển hình — EP chỉ kiểm tra giá trị nằm trong từng phân hoạch nên sẽ bỏ lỡ cả hai.

- **Decision Table buộc phải suy nghĩ theo tổ hợp, không theo trình tự.** Khi xây dựng bảng cho REQ-04 (trạng thái sách × trạng thái thành viên × giới hạn mượn), nhóm nhận ra cần phân biệt *lý do* bị từ chối chứ không chỉ là *có bị từ chối hay không* — từ đó tạo ra TC-17 và TC-18 riêng biệt, qua đó phát hiện BUG-03: hệ thống dùng chung một thông báo "expired" cho hai trạng thái nghiệp vụ hoàn toàn khác nhau.

- **Mỗi TC nên kiểm thử đúng một điều kiện; mỗi lỗi nên mô tả rõ từng triệu chứng.** BUG-05 ảnh hưởng đến TC-25/26/27 theo ba hướng mâu thuẫn nhau (email hợp lệ bị từ chối, email sai được chấp nhận, email trùng bị báo nhầm là lỗi định dạng). Gộp chúng vào một TC hoặc một lỗi sẽ che khuất bản chất thực sự và khiến việc sửa sai mục tiêu khó hơn.

- **Mức độ nghiêm trọng phải phản ánh tác động nghiệp vụ, không phải chỉ mức độ dễ thấy hay tần suất xuất hiện.** BUG-04 (thông báo lỗi sai) xuất hiện trong mọi lần chạy nhưng chỉ ở mức Medium vì không làm hỏng dữ liệu. BUG-01 (bỏ qua giới hạn mượn) cần một cấu hình nhất định để tái hiện nhưng ở mức High vì phá vỡ ràng buộc nghiệp vụ cốt lõi và có thể gây thất thoát tài nguyên thư viện.

- **Đề xuất cách sửa cụ thể tới mức toán tử giúp lập trình viên xử lý nhanh hơn.** Ghi rõ "đổi `>` thành `>=`" (BUG-01, BUG-04) hoặc "tách `DUPLICATE_EMAIL` và `INVALID_EMAIL_FORMAT`" (BUG-05) sẽ hữu ích hơn nhiều so với chỉ mô tả triệu chứng — đặc biệt khi nhóm làm việc không đồng bộ và khó trao đổi lại ngay với người kiểm thử.

---

## 8. Tuyên bố sử dụng AI

| **Công cụ** | **Phạm vi sử dụng** | **Phương pháp xác minh** |
|---|---|---|
| | | |

---

*Báo cáo thực thi kiểm thử — 20/05/2026*
