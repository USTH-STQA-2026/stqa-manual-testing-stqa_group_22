# Báo cáo lỗi

> **Hướng dẫn:** Tạo một mục lỗi cho mỗi TC có kết quả **Không đạt**.
> Mỗi lỗi cần có: tiêu đề mô tả hành vi sai, các bước tái hiện, kết quả mong đợi so với thực tế, mức độ nghiêm trọng + giải thích.

| **Thông tin** | |
|---|---|
| **Nhóm** | STQA Group 22 |
| **Ngày báo cáo** | 20/05/2026 |
| **Nguồn** | Thực thi kiểm thử (20/05/2026) — https://stqa.rbc.vn |
| **Ngày phát hiện** | 20/05/2026 |
| **Trạng thái** | Đang mở |
| **Môi trường** | Chrome (phiên bản mới nhất), Windows 11, giao diện tiếng Việt — https://stqa.rbc.vn |

---

## BUG-01: Thành viên có thể mượn đồng thời 4 cuốn sách thay vì tối đa 3 cuốn theo SRS

| **Thuộc tính** | **Chi tiết** |
|---|---|
| **Mã lỗi** | BUG-01 |
| **TC liên quan** | TC-19 |
| **REQ liên quan** | REQ-04, BR-01 |
| **Mức độ nghiêm trọng** | **Cao** — Cho phép mượn vượt quá giới hạn nghiệp vụ (3 cuốn), dễ gây thất thoát / quản lý sai tài sản thư viện |
| **Phát hiện bởi** | Nguyễn Tú Oanh |

**Tiền điều kiện:**
- Đăng nhập bằng tài khoản Member hoặc Librarian có quyền mượn sách.
- Thành viên đang ở trạng thái **Active** và hiện mượn **ít hơn 3** cuốn (hoặc đặt lại dữ liệu trước khi kiểm thử).
- Có đủ sách ở trạng thái **"Available"** để mượn liên tiếp.

**Các bước tái hiện:**
1. Trong tab **Books** hoặc **Borrow/Return**, mượn lần lượt sách thứ 1, 2 và 3 cho cùng một thành viên — tất cả đều thành công.
2. Mượn **cuốn thứ 4** (vẫn ở trạng thái "Available") cho cùng thành viên đó.
3. Tra cứu các bản ghi mượn của thành viên và đếm các bản ghi có trạng thái **"Borrowed"**.

**Kết quả mong đợi:** Theo **REQ-04 / BR-01**: tối đa **3 cuốn sách mỗi thành viên** tại bất kỳ thời điểm nào. Lượt mượn thứ 4 phải bị **từ chối** với thông báo như *"Maximum borrow limit reached (3 books)"* (hoặc tương đương). Không được tạo thêm bản ghi mượn thứ 4.

**Kết quả thực tế:** Hệ thống **cho phép mượn cuốn thứ 4** thành công. Thành viên kết thúc với **4 bản ghi mượn đang hoạt động**. Thông báo giới hạn 3 sách chỉ xuất hiện trong một số tình huống khác (ví dụ: thành viên đang hoạt động đã có đúng 3 cuốn), và không chặn đúng lượt mượn thứ 4 như mô tả trong TC-19.

**Tác động:** Vi phạm quy tắc giới hạn mượn cốt lõi. Nhiều khả năng là lỗi toán tử biên (`>` thay vì `>=`) khi so sánh số lượng sách đang mượn, dẫn đến cấp phát quá mức tài nguyên thư viện.

**Bằng chứng:** *(đính kèm ảnh chụp màn hình)*

**Đề xuất sửa:** Đổi điều kiện kiểm tra giới hạn thành `borrowed_count >= 3` trước khi từ chối mượn thêm; đảm bảo mọi luồng mượn (tab Books, tab Borrow/Return) dùng chung một logic.

---

## BUG-02: Bộ lọc thể loại sách phân biệt hoa thường, trái với quy tắc tìm kiếm không phân biệt hoa thường

| **Thuộc tính** | **Chi tiết** |
|---|---|
| **Mã lỗi** | BUG-02 |
| **TC liên quan** | TC-14 |
| **REQ liên quan** | REQ-03 |
| **Mức độ nghiêm trọng** | **Trung bình** — Bộ lọc thể loại không nhất quán với tìm kiếm theo từ khóa; người dùng nhập đúng tên thể loại nhưng sai hoa/thường sẽ không thấy kết quả |
| **Phát hiện bởi** | Nguyễn Minh Đức |

**Tiền điều kiện:**
- Đã đăng nhập (Librarian hoặc Member).
- Tab **Books** đang mở; dữ liệu seed có sách thuộc thể loại **"Technology"**.

**Các bước tái hiện:**
1. Trong tab **Books**, lọc theo thể loại với giá trị `Technology` → quan sát kết quả.
2. Lọc lại bằng `technology` (viết thường toàn bộ) → quan sát kết quả.
3. Lọc lại bằng chữ in hoa hoàn toàn (ví dụ: `TECHNOLOGY`) → quan sát kết quả.
4. (Để đối chiếu) Gõ `flutter` / `FLUTTER` trong **ô tìm kiếm** — các sách giống nhau vẫn hiển thị (TC-12 đạt).

**Kết quả mong đợi:** Theo **REQ-03**: tìm kiếm/lọc phải **không phân biệt hoa thường**. Cả ba đầu vào trên phải trả về **cùng một tập** sách thể loại Technology. Nếu không có sách nào phù hợp → hiển thị **"No books found"**.

**Kết quả thực tế:**
- `Technology`: **thành công**, hiển thị sách Technology.
- `technology` hoặc chữ in hoa toàn bộ: **"No books found"** / danh sách trống dù thể loại đó tồn tại trong DB.
- Tìm kiếm theo tên/tác giả vẫn **không phân biệt hoa thường** (đạt).

**Tác động:** Trải nghiệm người dùng không nhất quán; người dùng có thể hiểu nhầm rằng thư viện không có sách thuộc thể loại đó.

**Bằng chứng:** *(đính kèm ảnh chụp màn hình)*

**Đề xuất sửa:** Chuẩn hóa phép so sánh thể loại bằng `toLowerCase()` / `localeCompare` không phân biệt hoa thường, đồng bộ với logic tìm kiếm theo tên/tác giả.

---

## BUG-03: Từ chối mượn hiển thị cùng một thông báo "expired" cho cả thành viên Suspended và Expired

| **Thuộc tính** | **Chi tiết** |
|---|---|
| **Mã lỗi** | BUG-03 |
| **TC liên quan** | TC-17, TC-18 |
| **REQ liên quan** | REQ-04 |
| **Mức độ nghiêm trọng** | **Trung bình** — Thông báo lỗi sai cho trạng thái thành viên; ảnh hưởng đến vận hành và hỗ trợ người dùng |
| **Phát hiện bởi** | Phạm Vũ Khánh |

**Tiền điều kiện:**
- Đăng nhập bằng Librarian: librarian@library.com / admin123.
- MEM004 (cu.le@email.com) — trạng thái **Suspended**.
- MEM005 (binh.pham@email.com) — trạng thái **Expired**.
- Có ít nhất một cuốn sách ở trạng thái **"Available"**.

**Các bước tái hiện:**
1. Vào tab **Borrow/Return**.
2. Thử mượn một cuốn sách "Available" cho **MEM004** (Suspended). Ghi nhận thông báo lỗi.
3. Thử mượn một cuốn sách "Available" khác cho **MEM005** (Expired). Ghi nhận thông báo lỗi.

**Kết quả mong đợi:** Theo **REQ-04**:
- MEM004 (Suspended): bị từ chối với thông báo mô tả **tạm khóa** (ví dụ: *"Member is currently suspended. Cannot borrow books."*).
- MEM005 (Expired): bị từ chối với thông báo mô tả **hết hạn** (ví dụ: *"Member membership has expired. Cannot borrow books."*).
- Hai thông báo phải **khác nhau**, phản ánh chính xác `member.status`.

**Kết quả thực tế:**
- MEM004 (Suspended) nhận thông báo kiểu **"Member has expired…"** — **sai lý do** (đáng lẽ phải nói suspended).
- MEM005 (Expired) nhận **cùng một thông báo "expired"**, không phân biệt với trường hợp suspended.
- Không thông báo nào phản ánh đúng hai trạng thái nghiệp vụ khác nhau như SRS quy định.

**Tác động:** Librarian và thành viên hiểu sai lý do bị từ chối; khó xử lý khiếu nại đúng cách (gia hạn hay gỡ tạm khóa).

**Bằng chứng:**
- TC-17: *(đính kèm ảnh chụp màn hình)*
- TC-18: *(đính kèm ảnh chụp màn hình)*

**Đề xuất sửa:** Tách riêng các nhánh `if (status == suspended)` và `if (status == expired)` với thông báo khác nhau; không dùng chung một mẫu "expired".

---

## BUG-04: Check Overdue không đánh dấu các bản ghi có dueDate bằng hôm nay; lần chạy thứ hai báo 0 bản ghi

| **Thuộc tính** | **Chi tiết** |
|---|---|
| **Mã lỗi** | BUG-04 |
| **TC liên quan** | TC-23 |
| **REQ liên quan** | REQ-06 |
| **Mức độ nghiêm trọng** | **Cao** — Lỗi logic ngày biên khiến các bản ghi đến hạn **đúng hôm nay** không được đánh dấu quá hạn; chạy kiểm tra lần hai báo 0 dù vẫn còn bản ghi cần xử lý |
| **Phát hiện bởi** | Nguyễn Chí Kiên |

**Tiền điều kiện:**
- Đăng nhập bằng Librarian.
- Dữ liệu seed có một bản ghi quá hạn (ví dụ BR001, due date ≤ hôm nay).
- Dữ liệu **không được reset** giữa hai lần bấm **Check Overdue**.

**Các bước tái hiện:**
1. Trong tab **Borrow/Return**, tìm một bản ghi có due date **≤ hôm nay** và trạng thái **"Borrowed"**.
2. Bấm **Check Overdue** — **lần 1**. Ghi nhận thông báo (ví dụ: *"Updated: 1 overdue borrow record"*).
3. **Không reset** dữ liệu. Bấm **Check Overdue** — **lần 2**. Ghi nhận thông báo.
4. Kiểm tra xem bản ghi có dueDate **bằng đúng ngày hôm nay** đã được gán trạng thái **"Overdue"** hay chưa.

**Kết quả mong đợi:** Theo **REQ-06**: tất cả bản ghi có dueDate ≤ hôm nay (bao gồm **đúng ngày đến hạn**) phải được chuyển sang **"Overdue"**. BR001 và các bản ghi tương tự phải được cập nhật. Các lần chạy sau phải **nhất quán** với trạng thái đã được gán trước đó (không báo sai số lượng nếu không còn bản ghi mới nào cần xử lý).

**Kết quả thực tế:**
- Lần 1: cập nhật **1** bản ghi quá hạn.
- Lần 2: báo **"Updated: 0 overdue borrow records"** — khó khớp nếu vẫn còn bản ghi cần xử lý hoặc logic ngày biên bị sai.
- Các bản ghi có due date **bằng đúng hôm nay** không được gán **"Overdue"** (khả năng off-by-one: dùng `>` thay vì `>=`).
- TC-24 (thành viên xem bản ghi quá hạn của mình) vẫn **đạt** sau lần chạy 1.

**Tác động:** Bỏ sót bản ghi quá hạn đúng ngày đến hạn; báo cáo quá hạn không đáng tin cậy; ảnh hưởng đến tiền phạt và nhắc nhở trả sách.

**Bằng chứng:** *(đính kèm ảnh chụp màn hình)*

**Đề xuất sửa:** Đổi phép so sánh thành `today >= dueDate` (hoặc `!dueDate.isAfter(today)`). Đảm bảo tác vụ là idempotent: lần chạy 2 không xóa trạng thái quá hạn đã có và báo đúng số bản ghi còn lại cần xử lý.

---

## BUG-05: Form thêm thành viên xử lý email sai — từ chối email hợp lệ, chấp nhận email thiếu dấu chấm, báo trùng thành "Invalid email"

| **Thuộc tính** | **Chi tiết** |
|---|---|
| **Mã lỗi** | BUG-05 |
| **TC liên quan** | TC-25, TC-26, TC-27 |
| **REQ liên quan** | REQ-07, BR-08 |
| **Mức độ nghiêm trọng** | **Cao** — Kiểm tra email không nhất quán: từ chối dữ liệu hợp lệ, chấp nhận email sai định dạng và dùng thông báo sai cho trường hợp trùng |
| **Phát hiện bởi** | Đào Trung Hiếu |

**Tiền điều kiện:**
- Đăng nhập bằng Librarian: librarian@library.com / admin123.
- Tab **Members** — chỉ Librarian mới thấy **Add Member** (TC-28 đạt: thành viên không có quyền).

**Các bước tái hiện:**

*Phần A — TC-25: Thêm thành viên hợp lệ*
1. Vào tab **Members** → bấm **Add Member**.
2. Nhập: Name: Tran Thi Tay, Email: tay.tran@email.com, Phone: 0363636361 (theo TC-25).
3. Bấm **Save**.

*Phần B — TC-26: Định dạng email không hợp lệ*
4. Bấm **Add Member** một lần nữa.
5. Nhập email thiếu dấu `.` ở domain: `tay.tran@emailcom`.
6. Bấm **Save**.

*Phần C — TC-27: Email trùng*
7. Bấm **Add Member** một lần nữa.
8. Nhập email đã tồn tại: `ba.nguyen@email.com`.
9. Bấm **Save**.

**Kết quả mong đợi:** Theo **REQ-07 / BR-08**:
- **TC-25**: Tạo thành công; thành viên mới xuất hiện với trạng thái **Active**.
- **TC-26**: Bị từ chối; thông báo **email không hợp lệ**; không tạo bản ghi.
- **TC-27**: Bị từ chối; thông báo **email đã tồn tại / trùng** (không bị nhầm với lỗi định dạng).

**Kết quả thực tế:**
- **TC-25**: Thành viên **không được tạo**; hiển thị **"Invalid email"** dù `tay.tran@email.com` là định dạng hợp lệ theo SRS.
- **TC-26**: Thành viên **được tạo thành công** với `tay.tran@emailcom` (thiếu `.` sau domain) — vi phạm quy tắc kiểm tra.
- **TC-27**: Chỉ hiển thị **"Invalid email"**, không cho biết email là **trùng**.

**Tác động:** Việc nhập dữ liệu thành viên không đáng tin cậy: dữ liệu hợp lệ bị chặn, dữ liệu sai lọt vào hệ thống, và Librarian không phân biệt được lỗi định dạng với lỗi trùng email.

**Bằng chứng:**
- TC-25: *(đính kèm ảnh chụp màn hình)*
- TC-26: *(đính kèm ảnh chụp màn hình)*
- TC-27: *(đính kèm ảnh chụp màn hình)*

**Đề xuất sửa:**
- Kiểm tra email theo regex của SRS (`local@domain.tld`, bắt buộc có `.` ở phần domain).
- Tách mã và thông báo lỗi: `DUPLICATE_EMAIL` vs `INVALID_EMAIL_FORMAT`.
- Đảm bảo TC-25 / TC-26 / TC-27 đều đạt sau khi sửa (luồng thành công + chặn dữ liệu sai + chặn email trùng).

---

## BUG-06: Tìm kiếm kết hợp bộ lọc bỏ qua thể loại khi từ khóa khớp với sách ngoài danh mục đã chọn

| **Thuộc tính** | **Chi tiết** |
|---|---|
| **Mã lỗi** | BUG-06 |
| **TC liên quan** | TC-37 |
| **REQ liên quan** | REQ-03 |
| **Mức độ nghiêm trọng** | **Trung bình** — Bộ lọc thể loại bị bỏ qua âm thầm khi từ khóa khớp với sách ngoài danh mục đã chọn; kết quả gây hiểu nhầm |
| **Phát hiện bởi** | Nguyễn Minh Đức |

**Tiền điều kiện:**
- Đã đăng nhập (Librarian hoặc Member).
- Tab **Books** đang mở.
- Bộ lọc thể loại đặt là **"Technology"**; từ khóa tìm kiếm là **"Nguyen Van An"** (tác giả có sách trong Literature, không có trong Technology).

**Các bước tái hiện:**
1. Trong tab **Books**, chọn bộ lọc thể loại **"Technology"**.
2. Xác nhận chỉ hiển thị sách Technology.
3. Nhập từ khóa **"Nguyen Van An"** (tác giả không có sách thuộc Technology) vào ô tìm kiếm và nhấn tìm kiếm.
4. Quan sát kết quả.

**Kết quả mong đợi:** Theo **REQ-03**: khi cả từ khóa và bộ lọc thể loại đều đang bật, kết quả phải thỏa **đồng thời** cả hai điều kiện (logic AND). Vì "Nguyen Van An" không có sách nào thuộc Technology, hệ thống phải hiển thị **"No books found"** hoặc danh sách trống.

**Kết quả thực tế:** Hệ thống **bỏ qua bộ lọc thể loại** và hiển thị toàn bộ sách của "Nguyen Van An" bất kể thể loại. Bộ lọc bị bỏ âm thầm, đưa ra kết quả không khớp với danh mục đang chọn.

**Tác động:** Người dùng tìm kiếm trong một thể loại cụ thể sẽ nhận kết quả ngoài danh mục mà không có cảnh báo nào — dẫn đến nhầm lẫn và khám phá sách sai.

**Bằng chứng:** *(đính kèm ảnh chụp màn hình)*

**Đề xuất sửa:** Đảm bảo truy vấn tìm kiếm áp dụng đồng thời cả bộ lọc từ khóa và bộ lọc thể loại (SQL: `WHERE genre = ? AND (title LIKE ? OR author LIKE ?)` ). Nếu không có kết quả thỏa cả hai, trả về "No books found" thay vì nới lỏng ràng buộc thể loại.

---

## BUG-07: Trả sách quá hạn hoàn tất im lặng — không hiển thị cảnh báo hoặc thông báo quá hạn

| **Thuộc tính** | **Chi tiết** |
|---|---|
| **Mã lỗi** | BUG-07 |
| **TC liên quan** | TC-38 |
| **REQ liên quan** | REQ-05 |
| **Mức độ nghiêm trọng** | **Cao** — Sách quá hạn được trả mà không có cảnh báo; Librarian và thành viên không có khả năng nhìn thấy lượt trả trễ, làm gián đoạn quy trình tiền phạt và nhắc nhở |
| **Phát hiện bởi** | Phạm Vũ Khánh |

**Tiền điều kiện:**
- Đăng nhập bằng Librarian: librarian@library.com / admin123.
- "Check Overdue" đã được chạy — BR001 (MEM002, BOOK003) có trạng thái **"Overdue"**.

**Các bước tái hiện:**
1. Trong tab **Borrow/Return**, tìm bản ghi BR001 (trạng thái "Overdue").
2. Bấm **Return** trên BR001.
3. Xác nhận thao tác trả.
4. Quan sát phản hồi của hệ thống — tìm bất kỳ cảnh báo, hộp thoại hay thông báo nào về trạng thái quá hạn.

**Kết quả mong đợi:** Theo **REQ-05**: khi thành viên trả một cuốn sách quá hạn, hệ thống phải hiển thị cảnh báo quá hạn (ví dụ: *"This book was returned overdue — please apply the applicable late fee."*). Trạng thái bản ghi đổi thành "Returned" **và** cảnh báo phải được hiển thị.

**Kết quả thực tế:** Thao tác trả hoàn tất thành công và bản ghi đổi sang "Returned", nhưng **không có cảnh báo quá hạn nào được hiển thị**. Điều kiện quá hạn bị bỏ qua một cách im lặng. Không có dấu hiệu nào cho Librarian hoặc thành viên biết rằng lượt trả này là trễ hạn.

**Tác động:** Nhân viên thư viện không có tín hiệu trong hệ thống cho các lượt trả trễ. Không thể kích hoạt tiền phạt hoặc hành động nhắc nhở từ luồng này, dẫn đến các lượt trả muộn không được theo dõi và có thể thất thoát doanh thu.

**Bằng chứng:** *(đính kèm ảnh chụp màn hình)*

**Đề xuất sửa:** Sau khi trả thành công, kiểm tra `dueDate < returnDate`; nếu đúng, hiển thị cảnh báo quá hạn trước khi đóng hộp thoại trả sách. Có thể ghi thêm sự kiện quá hạn để phục vụ báo cáo.

---

## BUG-08: Thành viên có thể tìm và xem bản ghi mượn của thành viên khác, đồng thời trả sách thay họ — truy cập trái phép

| **Thuộc tính** | **Chi tiết** |
|---|---|
| **Mã lỗi** | BUG-08 |
| **TC liên quan** | TC-39 |
| **REQ liên quan** | REQ-08 |
| **Mức độ nghiêm trọng** | **Nghiêm trọng** — Một Member có thể xem bản ghi mượn của bất kỳ thành viên nào khác và thực hiện thao tác trả thay họ — vi phạm hoàn toàn kiểm soát truy cập |
| **Phát hiện bởi** | Đào Trung Hiếu |

**Tiền điều kiện:**
- Đăng nhập bằng Member MEM002 (ba.nguyen@email.com / password123).
- MEM006 (biet.hoang@email.com) có ít nhất một bản ghi mượn đang hoạt động (ví dụ: BR003 — BOOK013).
 
**Các bước tái hiện:**
1. Đăng nhập bằng **MEM002** (ba.nguyen@email.com).
2. Mở tab **Borrow/Return**.
3. Trong ô tìm kiếm/tra cứu thành viên, nhập **MEM006** (hoặc biet.hoang@email.com).
4. Quan sát xem bản ghi mượn của MEM006 có hiển thị hay không.
5. Nếu bản ghi xuất hiện, thử bấm **Return** trên một trong các bản ghi của MEM006.

**Kết quả mong đợi:** Theo **REQ-08**: Member chỉ được xem và thao tác trên **bản ghi mượn của chính mình**. Tìm kiếm thành viên khác phải trả về **kết quả trống** hoặc hiển thị thông báo từ chối truy cập. Nút Return không được xuất hiện trên bản ghi của thành viên khác.

**Kết quả thực tế:** MEM002 **có thể tìm mã của MEM006** và hệ thống hiển thị toàn bộ lịch sử mượn của MEM006. Hơn nữa, MEM002 có thể **bấm Return** trên bản ghi đang hoạt động của MEM006 (BR003), trả sách thành công thay cho thành viên khác — mà không có bất kỳ kiểm tra phân quyền nào.

**Tác động:** Bất kỳ member nào đã xác thực cũng có thể xem lịch sử mượn của các thành viên khác (rò rỉ quyền riêng tư) và thực hiện thao tác trả thay họ (vi phạm tính toàn vẹn dữ liệu). Đây là lỗi nghiêm trọng nhất trong hệ thống — nó vô hiệu hóa hoàn toàn kiểm soát truy cập đối với quản lý bản ghi mượn.

**Bằng chứng:** *(đính kèm ảnh chụp màn hình)*

**Đề xuất sửa:** Trên mọi truy vấn bản ghi mượn từ phiên Member, áp dụng bộ lọc phía server: `WHERE member_id = session.user_id`. Không bao giờ hiển thị hoặc cho phép thao tác trên bản ghi thuộc về một member khác, bất kể dữ liệu tìm kiếm là gì. Bản sửa phải được thực thi ở server — lọc phía client là không đủ.
