# Các trường hợp kiểm thử

> **Hướng dẫn:** Viết ít nhất **20 trường hợp kiểm thử** bao phủ các tính năng chính (REQ-01 → REQ-08). Sắp xếp và nhóm các trường hợp kiểm thử theo cách hợp lý nhất.

| **Thông tin** | |
|---|---|
| **Nhóm** | STQA Group 22 |
| **Ngày tạo** | 19/05/2026 |
| **Hệ thống** | https://stqa.rbc.vn |
| **Tài liệu tham chiếu** | SRS v1.0 |

---

## Bước 1: Mô hình hóa miền đầu vào (IDM)

> **Giáo trình:** Chương 6 — *Mô hình hóa miền đầu vào*, Paul Ammann & Jeff Offutt. Trước khi viết các trường hợp kiểm thử, nhóm **phải** phân tích miền đầu vào bằng các bảng IDM dưới đây. Mỗi tính năng cần xác định: **Đặc tính**, **Phân hoạch**, và **Giá trị đại diện**.

### IDM — Đăng nhập (REQ-01)

| **Đặc tính** | **Phân hoạch** | **Giá trị đại diện** | **Kết quả mong đợi** |
|---|---|---|---|
| Email có tồn tại trong DB không? | Có | librarian@library.com | Đăng nhập thành công |
| | Không | nobody@test.com | Thông báo: "Member not found" |
| Mật khẩu có đúng không? | Đúng | admin123 | Đăng nhập thành công |
| | Sai | wrongpassword | Thông báo: "Incorrect password" |
| Các trường có trống không? | Không trống | (bất kỳ giá trị nào) | Xử lý bình thường |
| | Trống | "" | Thông báo: "Please enter email and password" |

### IDM — Xem danh sách sách (REQ-02)

| **Đặc tính** | **Phân hoạch** | **Giá trị đại diện** | **Kết quả mong đợi** |
|---|---|---|---|
| Vai trò người dùng? | Librarian | librarian@library.com | Có thể xem đầy đủ danh sách sách |
| | Member | ba.nguyen@email.com | Có thể xem đầy đủ danh sách sách |
| Trạng thái sách hiển thị? | Available | BOOK001 | Hiển thị "Available" |
| | Borrowed | BOOK003 | Hiển thị "Borrowed" |
| Cập nhật sau khi mượn? | Đã thay đổi | Mượn BOOK001 | Trạng thái đổi sang "Borrowed" ngay lập tức |

### IDM — Tìm kiếm sách (REQ-03)

| **Đặc tính** | **Phân hoạch** | **Giá trị đại diện** | **Kết quả mong đợi** |
|---|---|---|---|
| Từ khóa có tồn tại trong DB không? | Có (tựa sách) | "Flutter" | Hiển thị sách có chứa "Flutter" |
| | Có (tên tác giả) | "Nguyen" | Hiển thị sách của tác giả Nguyen |
| | Không | "XYZ123" | Thông báo: "Book not found" |
| Có phân biệt hoa thường không? | Chữ thường | "flutter" | Kết quả giống "Flutter" |
| | Chữ hoa | "FLUTTER" | Kết quả giống "Flutter" |
| Lọc theo thể loại? | Sách thuộc thể loại đó | "Technology" | Chỉ hiển thị sách thuộc Technology |
| | Không có sách trong thể loại đó | "Non-existent genre" | Danh sách trống hoặc thông báo không có kết quả |

### IDM — Mượn sách (REQ-04)

| **Đặc tính** | **Phân hoạch** | **Giá trị đại diện** | **Kết quả mong đợi** |
|---|---|---|---|
| Ai thực hiện thao tác? | Member | ba.nguyen@email.com | Có thể mượn (tab Books, nút +) |
| | Librarian | librarian@library.com | **Không thể** mượn thay cho member |
| Trạng thái sách? | Available | BOOK001 | Cho phép mượn |
| | Borrowed | BOOK003 | Không cho phép |
| | Lost | BOOK007 | Không cho phép |
| Trạng thái thành viên? | Active | MEM002 | Cho phép mượn |
| | Suspended | MEM004 | Từ chối, hiển thị thông báo suspended |
| | Expired | MEM005 | Từ chối, hiển thị thông báo expired |
| Số sách đang mượn hiện tại? | < 3 (BVA: 0, 1, 2) | MEM006 (1 cuốn) | Cho phép mượn |
| | = 3 (BVA: ngưỡng) | Thành viên đang mượn 3 cuốn | Từ chối, hiển thị thông báo vượt giới hạn |

### IDM — Trả sách (REQ-05)

| **Đặc tính** | **Phân hoạch** | **Giá trị đại diện** | **Kết quả mong đợi** |
|---|---|---|---|
| Ai thực hiện thao tác? | Librarian | librarian@library.com | Có thể trả (tab Borrow/Return) |
| | Member | ba.nguyen@email.com | Có thể trả các bản ghi mượn **của chính mình** |
| Sách hiện đang được mượn không? | Có | BOOK013 (MEM006) | Cho phép trả, sách trở về "Available" |
| | Không | BOOK001 (chưa được mượn) | Từ chối trả |
| Việc trả có quá hạn không? | Quá hạn | BR001 (MEM002) | Trả thành công + hiển thị cảnh báo quá hạn |
| | Đúng hạn | BR003 (MEM006) | Trả thành công, không có cảnh báo quá hạn |

### IDM — Xử lý quá hạn (REQ-06)

| **Đặc tính** | **Phân hoạch** | **Giá trị đại diện** | **Kết quả mong đợi** |
|---|---|---|---|
| Ai thực hiện thao tác? | Librarian | Bấm "Check Overdue" | Cập nhật trạng thái các bản ghi quá hạn |
| dueDate so với hôm nay? | ≤ hôm nay | BR001 (15/09/2024) | Được đánh dấu "Overdue" |
| | > hôm nay | BR003 (15/10/2024) | Giữ nguyên "Borrowed" |
| Thành viên xem bản ghi? | Bản ghi của chính mình bị quá hạn | MEM002 sau khi kiểm tra | Thấy trạng thái "Overdue" |

### IDM — Quản lý thành viên (REQ-07)

| **Đặc tính** | **Phân hoạch** | **Giá trị đại diện** | **Kết quả mong đợi** |
|---|---|---|---|
| Ai thực hiện thao tác? | Librarian | librarian@library.com | Được phép thêm thành viên |
| | Member | ba.nguyen@email.com | Không có tab/quyền thêm thành viên |
| Email có hợp lệ không? | Hợp lệ | new.member@email.com | Tạo thành công |
| | Không hợp lệ (thiếu .) | user@domain | Bị từ chối, có thông báo lỗi |
| Email có trùng không? | Trùng | ba.nguyen@email.com | Bị từ chối, thông báo email trùng |

### IDM — Tra cứu bản ghi mượn (REQ-08)

| **Đặc tính** | **Phân hoạch** | **Giá trị đại diện** | **Kết quả mong đợi** |
|---|---|---|---|
| Ai tra cứu? | Librarian | Tra cứu MEM003 | Có thể xem tất cả bản ghi của MEM003 |
| | Member | MEM002 xem bản ghi | Chỉ thấy bản ghi của chính mình (MEM002) |
| Thông tin bản ghi? | Bản ghi hợp lệ | BR001 | Hiển thị mã, sách, ngày mượn, ngày hẹn trả, trạng thái |
| Thành viên xem bản ghi của người khác? | Member xem của người khác | MEM002 tra cứu MEM003 | Không được phép / bị từ chối |

> 💡 **Gợi ý kỹ thuật:** Dùng **Phân hoạch tương đương (EP)** cho các phân hoạch rời rạc và **Phân tích giá trị biên (BVA)** cho các phân hoạch số (ví dụ: giới hạn 3 cuốn). Xem giáo trình §6.1–6.3.

---

## Bước 2: Các trường hợp kiểm thử

### REQ-01 — Đăng nhập

| **Mã TC** | **Mục tiêu kiểm thử** | **Tiền điều kiện** | **Các bước** | **Dữ liệu đầu vào** | **Kết quả mong đợi** | **Kỹ thuật** |
|---|---|---|---|---|---|---|
| TC-01 | Librarian đăng nhập thành công | Trang đăng nhập đã mở, dữ liệu seed đã nạp | 1. Nhập email Librarian. 2. Nhập mật khẩu. 3. Bấm **Login**. | Email: librarian@library.com, PW: admin123 | Điều hướng đến trang chủ. AppBar hiển thị tên "Nguyen Thu Thu" và vai trò Librarian. | EP |
| TC-02 | Member đăng nhập thành công | Trang đăng nhập đã mở, dữ liệu seed đã nạp | 1. Nhập email Member. 2. Nhập mật khẩu. 3. Bấm **Login**. | Email: ba.nguyen@email.com, PW: password123 | Điều hướng đến trang chủ. AppBar hiển thị tên "Nguyen Hoc Ba" và vai trò Member. | EP |
| TC-03 | Đăng nhập bằng email không tồn tại | Trang đăng nhập đã mở | 1. Nhập email không có trong hệ thống. 2. Nhập bất kỳ mật khẩu nào. 3. Bấm **Login**. | Email: noone@email.com, PW: password123 | Hiển thị lỗi: **"Member not found"**. Trang không thay đổi. | EP |
| TC-04 | Đăng nhập với mật khẩu sai | Trang đăng nhập đã mở | 1. Nhập email hợp lệ. 2. Nhập mật khẩu sai. 3. Bấm **Login**. | Email: ba.nguyen@email.com, PW: wrongpass | Hiển thị lỗi: **"Incorrect password"**. Trang không thay đổi. | EP |
| TC-05 | Đăng nhập với email và mật khẩu trống | Trang đăng nhập đã mở | 1. Để trống trường Email. 2. Để trống trường Password. 3. Bấm **Login**. | Email: *(trống)*, PW: *(trống)* | Hiển thị: **"Please enter email and password"**. Trang không thay đổi. | EP, BVA |
| TC-06 | Đăng nhập với thành viên bị Suspended | Trang đăng nhập, MEM004 có trạng thái Suspended | 1. Nhập email của MEM004. 2. Nhập mật khẩu. 3. Bấm **Login**. 4. Quan sát AppBar. | Email: cu.le@email.com, PW: password123 | Đăng nhập thành công. AppBar hiển thị **"Le Can Cu (Member)"**. | EP |
| TC-07 | Đăng nhập với thành viên Expired | Trang đăng nhập, MEM005 có trạng thái Expired | 1. Nhập email của MEM005. 2. Nhập mật khẩu. 3. Bấm **Login**. | Email: binh.pham@email.com, PW: password123 | Đăng nhập thành công. AppBar hiển thị **"Pham Trung Binh (Member)"**. | EP |
| TC-31 | Đăng nhập khi chỉ trống email | Trang đăng nhập đã mở | 1. Để trống trường Email. 2. Nhập một mật khẩu hợp lệ. 3. Bấm **Login**. | Email: *(trống)*, PW: password123 | Hiển thị: **"Please enter email and password"** (hoặc tương đương). Trang không thay đổi. | EP, BVA |

### REQ-02 — Xem danh sách sách

| **Mã TC** | **Mục tiêu kiểm thử** | **Tiền điều kiện** | **Các bước** | **Dữ liệu đầu vào** | **Kết quả mong đợi** | **Kỹ thuật** |
|---|---|---|---|---|---|---|
| TC-08 | Xem đầy đủ danh sách sách với mọi thông tin | Đã đăng nhập bằng Member ba.nguyen@email.com, đang ở tab **Books** | 1. Đăng nhập bằng member. 2. Mở tab **Books**. 3. Quan sát danh sách sách. | Tài khoản: ba.nguyen@email.com / password123 | Hiển thị 20 cuốn sách. Mỗi sách có: Tiêu đề, Tác giả, Thể loại, Năm, Trạng thái (Available, Borrowed, Lost), Mã sách. | EP |
| TC-09 | Cập nhật trạng thái sách theo thời gian thực sau khi mượn | Đã đăng nhập bằng Member MEM006, trạng thái BOOK001 là "Available" | 1. Trong tab **Books**, xác nhận BOOK001 là "Available". 2. Mượn BOOK001 (nút +). 3. Quay lại tab **Books**. | Tài khoản: biet.hoang@email.com, Sách: BOOK001 | BOOK001 đổi sang **"Borrowed"** ngay lập tức, không cần refresh. | EP |

### REQ-03 — Tìm kiếm & lọc sách

| **Mã TC** | **Mục tiêu kiểm thử** | **Tiền điều kiện** | **Các bước** | **Dữ liệu đầu vào** | **Kết quả mong đợi** | **Kỹ thuật** |
|---|---|---|---|---|---|---|
| TC-10 | Tìm kiếm sách theo tên | Đã đăng nhập, đang ở tab **Books** | 1. Nhập từ khóa vào ô tìm kiếm. 2. Nhấn search hoặc Enter. 3. Quan sát kết quả. | Từ khóa: Flutter | Hiển thị BOOK005 "Artificial Intelligence Overview", BOOK013 "Modern HR Management", BOOK019 "Vietnamese Literature Overview". Không hiển thị sách không liên quan. | EP |
| TC-11 | Tìm kiếm sách theo tên tác giả | Đã đăng nhập, đang ở tab **Books** | 1. Nhập tên tác giả vào ô tìm kiếm. 2. Nhấn search. 3. Quan sát kết quả. | Từ khóa: Nguyen | Hiển thị tất cả sách có tác giả chứa "Nguyen". | EP |
| TC-12 | Tìm kiếm không phân biệt hoa thường | Đã đăng nhập, đang ở tab **Books** | 1. Tìm với chữ thường flutter. 2. Ghi nhận kết quả. 3. Xóa, tìm với FLUTTER. | Từ khóa: flutter / Flutter | Cả hai lần tìm trả về cùng kết quả: BOOK005, BOOK013, BOOK019. | EP |
| TC-13 | Tìm kiếm không có kết quả | Đã đăng nhập, đang ở tab **Books** | 1. Nhập một từ khóa không tồn tại. 2. Nhấn search. 3. Quan sát thông báo. | Từ khóa: XYZ123 | Hiển thị thông báo: **"No books found."** Danh sách trống. | EP |
| TC-14 | Lọc sách theo thể loại (không phân biệt hoa thường) | Đã đăng nhập, đang ở tab **Books** | 1. Chọn bộ lọc **Technology**. 2. Chọn **technology** (chữ thường). 3. Chọn **TECHNOLOGY** (chữ in hoa). 4. So sánh kết quả. | Thể loại: Technology / technology / TECHNOLOGY | Cả ba đầu vào trả về **cùng** một tập sách thuộc Technology; thể loại không khớp hiển thị **"No books found."** | EP |
| TC-32 | Xóa từ khóa tìm kiếm → khôi phục danh sách đầy đủ | Đã đăng nhập, tab **Books**, đã tìm từ khóa "Flutter" | 1. Xóa toàn bộ nội dung trong ô tìm kiếm. 2. Nhấn search hoặc Enter. 3. Quan sát kết quả. | Từ khóa: *(trống sau khi xóa)* | Hiển thị lại toàn bộ **20 cuốn sách**. Không còn bộ lọc nào được áp dụng. | EP |
| TC-37 | Kết hợp tìm kiếm + lọc: từ khóa không có trong danh mục đã chọn → không có kết quả | Đã đăng nhập, tab **Books** | 1. Lọc theo thể loại **"Technology"**. 2. Nhập từ khóa **"Nguyen Van An"** (có trong Literature, không có trong Technology) vào ô tìm kiếm. 3. Nhấn search. 4. Quan sát kết quả. | Thể loại: Technology, Từ khóa: Nguyen Van An | Hệ thống hiển thị **không có kết quả** (hoặc "No books found") vì từ khóa không tồn tại trong danh mục đã lọc. Bộ lọc **không được** bị bỏ qua âm thầm. | EP |

### REQ-04 — Mượn sách

| **Mã TC** | **Mục tiêu kiểm thử** | **Tiền điều kiện** | **Các bước** | **Dữ liệu đầu vào** | **Kết quả mong đợi** | **Kỹ thuật** |
|---|---|---|---|---|---|---|
| TC-15 | Mượn sách thành công (luồng chuẩn) | Đã đăng nhập bằng Member MEM002, BOOK002 "Available", hiện đang mượn 1 cuốn (BOOK003) | 1. Vào tab **Books**. 2. Mượn BOOK002 (nút +). 3. Xác nhận mượn. 4. Kiểm tra tab **Borrow/Return** để xem bản ghi. | Tài khoản: ba.nguyen@email.com, Sách: BOOK002 | Mượn thành công. Tạo bản ghi mới trạng thái **"Borrowed"**, ngày hẹn trả = ngày mượn + 14 ngày. BOOK002 đổi sang **"Borrowed"**. | EP, Bảng quyết định |
| TC-16 | Từ chối mượn sách đã đang cho mượn | Đã đăng nhập bằng Member MEM003, BOOK003 đang được MEM002 mượn | 1. Vào tab **Books**. 2. Tìm BOOK003. 3. Thử mượn (nút +). 4. Quan sát phản hồi. | Tài khoản: dam.tran@email.com, Sách: BOOK003 | Hệ thống từ chối. BOOK003 hiển thị **"Borrowed"**, không thể mượn. Không tạo bản ghi mới. | EP, Bảng quyết định |
| TC-17 | Từ chối mượn với Member Suspended | Đã đăng nhập bằng Member MEM004, trạng thái Suspended | 1. Vào tab **Books**. 2. Thử mượn BOOK004 (nút +). 3. Quan sát thông báo. | Tài khoản: cu.le@email.com, Sách: BOOK004 | Từ chối mượn. Thông báo nêu rõ **tạm khóa** (khác với hết hạn). | EP, Bảng quyết định |
| TC-18 | Từ chối mượn với Member Expired | Đã đăng nhập bằng Member MEM005, trạng thái Expired | 1. Vào tab **Books**. 2. Thử mượn BOOK008 (nút +). 3. Quan sát thông báo. | Tài khoản: binh.pham@email.com, Sách: BOOK008 | Từ chối mượn. Thông báo nêu rõ **hết hạn** (khác với tạm khóa). | EP, Bảng quyết định |
| TC-19 | Từ chối mượn khi đã chạm giới hạn 3 sách (BVA) | Đã đăng nhập bằng Member MEM002, hiện đang mượn 3 cuốn | 1. Vào tab **Books**. 2. Mượn BOOK005. 3. Quan sát phản hồi. | Tài khoản: ba.nguyen@email.com, Sách: BOOK005 | Lượt mượn hiển thị thông báo **đã vượt giới hạn 3 sách**. Không tạo bản ghi mượn thứ 4. | BVA, Bảng quyết định |
| TC-20 | Từ chối mượn sách bị mất | Đã đăng nhập bằng Member MEM006, BOOK007 có trạng thái Lost | 1. Vào tab **Books**. 2. Tìm BOOK007. 3. Thử mượn (nút +). 4. Quan sát phản hồi. | Tài khoản: biet.hoang@email.com, Sách: BOOK007 | Hệ thống từ chối. BOOK007 hiển thị **"Lost"**, không thể mượn. Không tạo bản ghi nào. | EP, Bảng quyết định |
| TC-33 | Cho phép mượn ở ngưỡng BVA (đang có 2 lượt mượn) | Đã đăng nhập bằng Member MEM002, hiện đang mượn 1 cuốn (BOOK003) | 1. Vào tab **Books**. 2. Tìm một cuốn Available (ví dụ BOOK001). 3. Mượn nó (nút +). 4. Xác nhận trong tab **Borrow/Return**. | Tài khoản: ba.nguyen@email.com, Sách: BOOK001 | Mượn thành công. Thành viên hiện có **2 lượt mượn đang hoạt động** — đúng tại điểm BVA dưới giới hạn. Tạo bản ghi mới với trạng thái "Borrowed". | BVA |

### REQ-05 — Trả sách

| **Mã TC** | **Mục tiêu kiểm thử** | **Tiền điều kiện** | **Các bước** | **Dữ liệu đầu vào** | **Kết quả mong đợi** | **Kỹ thuật** |
|---|---|---|---|---|---|---|
| TC-21 | Trả sách thành công | Đã đăng nhập bằng Librarian; MEM006 đang mượn BOOK013 (bản ghi BR003) | 1. Vào tab **Borrow/Return**. 2. Trả bản ghi mượn của BOOK013 thuộc MEM006. 3. Xác nhận trả. 4. Kiểm tra tab **Books**. | Librarian: librarian@library.com, Bản ghi: BR003, Sách: BOOK013 | Trả thành công. Bản ghi đổi sang **"Returned"**. BOOK013 trở về trạng thái **"Available"**. | EP |
| TC-22 | Trả một cuốn sách chưa từng được mượn | Đã đăng nhập bằng Member MEM003, hiện không mượn BOOK008 | 1. Vào tab **Borrow/Return**. 2. Thử trả BOOK008. 3. Quan sát phản hồi. | Tài khoản: dam.tran@email.com, Sách: BOOK008 | Hệ thống từ chối. Không tạo bản ghi trả sách. BOOK008 vẫn ở trạng thái **"Available"**. | EP |
| TC-38 | Trả sách quá hạn — phải hiển thị cảnh báo quá hạn | Đã đăng nhập bằng Librarian; BR001 có trạng thái "Overdue" (đã chạy Check Overdue) | 1. Vào tab **Borrow/Return**. 2. Trả BR001 (BOOK003 của MEM002). 3. Xác nhận trả. 4. Quan sát có cảnh báo hay thông báo nào không. | Tài khoản: librarian@library.com, Bản ghi: BR001 | Trả thành công **và** hệ thống hiển thị cảnh báo quá hạn (ví dụ: *"This book was returned overdue"*). Bản ghi đổi sang "Returned". | EP |

### REQ-06 — Xử lý quá hạn

| **Mã TC** | **Mục tiêu kiểm thử** | **Tiền điều kiện** | **Các bước** | **Dữ liệu đầu vào** | **Kết quả mong đợi** | **Kỹ thuật** |
|---|---|---|---|---|---|---|
| TC-23 | Librarian kiểm tra và đánh dấu các bản ghi quá hạn | Đã đăng nhập bằng Librarian, BR001 có dueDate 15/09/2024 ≤ hôm nay | 1. Vào tab **Borrow/Return**. 2. Bấm **Check Overdue**. 3. Tra cứu bản ghi BR001. | Bản ghi: BR001 (MEM002, BOOK003) | BR001 đổi trạng thái sang **"Overdue"**. Librarian thấy bản ghi trong danh sách quá hạn. | EP, BVA |
| TC-24 | Member xem bản ghi quá hạn của chính mình | Librarian đã chạy "Check Overdue", BR001 thuộc MEM002 | 1. Đăng nhập bằng ba.nguyen@email.com. 2. Vào tab **Borrow/Return** → bản ghi của tôi. 3. Quan sát BR001. | Tài khoản: MEM002 | MEM002 thấy BR001 với trạng thái **"Overdue"**. Không thể xem bản ghi của thành viên khác. | EP |
-- removed TC-34 --
| TC-35 | Librarian xem tất cả bản ghi quá hạn của mọi thành viên | Đã đăng nhập bằng Librarian, Check Overdue đã chạy (BR001 là Overdue) | 1. Vào tab **Borrow/Return**. 2. Quan sát danh sách bản ghi quá hạn hoặc tìm kiếm tất cả thành viên. 3. Xác nhận bản ghi của nhiều thành viên xuất hiện. | Tài khoản: librarian@library.com | Librarian xem được bản ghi quá hạn của **tất cả thành viên** (ví dụ: của MEM002 và các thành viên khác). Không bị giới hạn trong một member. | EP |

### REQ-07 — Quản lý thành viên

| **Mã TC** | **Mục tiêu kiểm thử** | **Tiền điều kiện** | **Các bước** | **Dữ liệu đầu vào** | **Kết quả mong đợi** | **Kỹ thuật** |
|---|---|---|---|---|---|---|
| TC-25 | Thêm thành viên mới thành công | Đã đăng nhập bằng Librarian, tab **Members** | 1. Bấm **Add Member**. 2. Nhập đầy đủ thông tin hợp lệ. 3. Lưu. 4. Tìm thành viên mới trong danh sách. | Name: Tran Thi Tay, Email: tay.tran@email.com, Phone: 0363636361 | Tạo thành công. Thành viên mới xuất hiện trong danh sách với trạng thái Active. | EP |
| TC-26 | Từ chối thêm thành viên có email không hợp lệ | Đã đăng nhập bằng Librarian, tab **Members** | 1. Bấm **Add Member**. 2. Nhập email thiếu dấu . trong domain. 3. Bấm Save. | Email: tay.tran@emailcom (không hợp lệ theo SRS) | Hệ thống từ chối. Hiển thị thông báo email không hợp lệ. Không tạo thành viên mới. | EP, BVA |
| TC-27 | Từ chối thêm thành viên có email trùng | Đã đăng nhập bằng Librarian, tab **Members** | 1. Bấm **Add Member**. 2. Nhập email đã tồn tại. 3. Bấm Save. | Email: ba.nguyen@email.com (đã tồn tại) | Bị từ chối. Thông báo: **email đã tồn tại / trùng**. | EP |
| TC-28 | Member không có quyền quản lý thành viên | Đã đăng nhập bằng ba.nguyen@email.com | 1. Quan sát thanh điều hướng dưới cùng. 2. Tìm tab **Members** / nút **Add Member**. | Tài khoản: MEM002 | **Không** hiển thị tab Members hoặc chức năng Add Member. | EP |

### REQ-08 — Tra cứu bản ghi mượn

| **Mã TC** | **Mục tiêu kiểm thử** | **Tiền điều kiện** | **Các bước** | **Dữ liệu đầu vào** | **Kết quả mong đợi** | **Kỹ thuật** |
|---|---|---|---|---|---|---|
| TC-29 | Librarian tra cứu bản ghi mượn của bất kỳ thành viên nào | Đã đăng nhập bằng Librarian, tab **Borrow/Return** | 1. Tra cứu theo mã MEM003. 2. Quan sát danh sách bản ghi. 3. Kiểm tra chi tiết bản ghi. | Member ID: MEM003 | Hiển thị tất cả bản ghi của MEM003. Mỗi bản ghi có: Record ID, Book, Borrow Date, Due Date, Status. | EP |
| TC-30 | Member chỉ xem bản ghi mượn của chính mình | Đã đăng nhập bằng MEM002, tab **Borrow/Return** | 1. Mở tab **Borrow/Return**. 2. Quan sát danh sách bản ghi. 3. Xác nhận không có bản ghi của MEM003/MEM006. | Tài khoản: ba.nguyen@email.com | Chỉ thấy bản ghi của MEM002 (ví dụ BR001). Không thể xem bản ghi của thành viên khác. | EP |
| TC-36 | Bản ghi đã trả hiển thị đúng trạng thái và đầy đủ trường | Đã đăng nhập bằng MEM002; TC-21 đã trả xong | 1. Đăng nhập bằng ba.nguyen@email.com. 2. Vào tab **Borrow/Return**. 3. Tìm bản ghi đã trả. 4. Kiểm tra tất cả trường hiển thị. | Tài khoản: ba.nguyen@email.com | Bản ghi hiển thị trạng thái **"Returned"** cùng đầy đủ các trường: Record ID, Book title, Borrow Date, Due Date, Status. Không có trường nào bị thiếu hoặc trống. | EP |
| TC-39 | Member không thể xem hoặc trả bản ghi của thành viên khác bằng cách tìm kiếm | Đã đăng nhập bằng Member MEM002, tab **Borrow/Return** | 1. Trong tab **Borrow/Return**, tìm mã thành viên **MEM006** hoặc nhập mã của MEM006. 2. Quan sát xem bản ghi của MEM006 có xuất hiện không. 3. Nếu thấy, thử bấm Return trên một bản ghi của MEM006. | Tài khoản: ba.nguyen@email.com, Mã tìm kiếm: MEM006 | Bản ghi của MEM006 **không hiển thị** cho MEM002. Bất kỳ lần tìm nào cũng trả về rỗng hoặc bị từ chối. MEM002 không thể trả sách thay cho MEM006. | EP |

---

## Tóm tắt

| **Nhóm tính năng** | **# TC** | **Phạm vi REQ** | **Kỹ thuật IDM đã áp dụng** |
|---|---|---|---|
| Đăng nhập | 8 | REQ-01 | EP, BVA |
| Xem danh sách sách | 2 | REQ-02 | EP |
| Tìm kiếm & lọc sách | 7 | REQ-03 | EP |
| Mượn sách | 7 | REQ-04 | EP, BVA, Bảng quyết định |
| Trả sách | 3 | REQ-05 | EP |
| Xử lý quá hạn | 3 | REQ-06 | EP, BVA |
| Quản lý thành viên | 4 | REQ-07 | EP, BVA |
| Tra cứu bản ghi mượn | 4 | REQ-08 | EP |
| **Tổng** | **38** | **REQ-01 → REQ-08** | **EP, BVA, Bảng quyết định** |
