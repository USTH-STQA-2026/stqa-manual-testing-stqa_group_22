# Test Cases

> **Instructions:** Write at least **20 test cases** covering the main features (REQ-01 → REQ-08). Organize and group test cases in the most logical way.

| **Info** | |
|---|---|
| **Group** | STQA Group 22 |
| **Date Created** | 19/05/2026 |
| **System** | https://stqa.rbc.vn |
| **Reference** | SRS v1.0 |

---

## Step 1: Input Domain Modeling (IDM)

> **Textbook:** Chapter 6 — *Input Domain Modeling*, Paul Ammann & Jeff Offutt. Before writing test cases, the group **must** analyze the input domain using the IDM tables below. Each feature requires identifying: **Characteristic**, **Block/Partition**, and **Representative Value**.

### IDM — Login (REQ-01)

| **Characteristic** | **Block/Partition** | **Representative Value** | **Expected Result** |
|---|---|---|---|
| Does email exist in DB? | Yes | librarian@library.com | Login successful |
| | No | nobody@test.com | Message: "Member not found" |
| Is password correct? | Correct | admin123 | Login successful |
| | Incorrect | wrongpassword | Message: "Incorrect password" |
| Are fields empty? | Not empty | (any value) | Process normally |
| | Empty | "" | Message: "Please enter email and password" |

### IDM — View Book List (REQ-02)

| **Characteristic** | **Block/Partition** | **Representative Value** | **Expected Result** |
|---|---|---|---|
| User role? | Librarian | librarian@library.com | Can view full book list |
| | Member | ba.nguyen@email.com | Can view full book list |
| Book status displayed? | Available | BOOK001 | Shows "Available" |
| | Borrowed | BOOK003 | Shows "Borrowed" |
| Updated after borrow? | Changed | Borrow BOOK001 | Status changes to "Borrowed" immediately |

### IDM — Search Books (REQ-03)

| **Characteristic** | **Block/Partition** | **Representative Value** | **Expected Result** |
|---|---|---|---|
| Does keyword exist in DB? | Yes (book title) | "Flutter" | Shows books containing "Flutter" |
| | Yes (author name) | "Nguyen" | Shows books by author Nguyen |
| | No | "XYZ123" | Message: "Book not found" |
| Case-sensitive? | Lowercase | "flutter" | Same result as "Flutter" |
| | Uppercase | "FLUTTER" | Same result as "Flutter" |
| Filter by genre? | Books in genre | "Technology" | Shows only Technology books |
| | No books in genre | "Non-existent genre" | Empty list or no results message |

### IDM — Borrow Book (REQ-04)

| **Characteristic** | **Block/Partition** | **Representative Value** | **Expected Result** |
|---|---|---|---|
| Who performs the action? | Member | ba.nguyen@email.com | Can borrow (Books tab, + button) |
| | Librarian | librarian@library.com | **Cannot** borrow on behalf of member |
| Book status? | Available | BOOK001 | Borrowing allowed |
| | Borrowed | BOOK003 | Not allowed |
| | Lost | BOOK007 | Not allowed |
| Member status? | Active | MEM002 | Borrowing allowed |
| | Suspended | MEM004 | Rejected, suspended message |
| | Expired | MEM005 | Rejected, expired message |
| Number of books currently borrowed? | < 3 (BVA: 0, 1, 2) | MEM006 (1 book) | Borrowing allowed |
| | = 3 (BVA: limit) | Member with 3 books | Rejected, limit exceeded message |

### IDM — Return Book (REQ-05)

| **Characteristic** | **Block/Partition** | **Representative Value** | **Expected Result** |
|---|---|---|---|
| Who performs the action? | Librarian | librarian@library.com | Can return (Borrow/Return tab) |
| | Member | ba.nguyen@email.com | Can return **own** borrowing records |
| Is the book currently borrowed? | Yes | BOOK013 (MEM006) | Return allowed, book returns to "Available" |
| | No | BOOK001 (not borrowed) | Return rejected |
| Is return overdue? | Overdue | BR001 (MEM002) | Return successful + overdue warning displayed |
| | On time | BR003 (MEM006) | Return successful, no overdue warning |

### IDM — Overdue Processing (REQ-06)

| **Characteristic** | **Block/Partition** | **Representative Value** | **Expected Result** |
|---|---|---|---|
| Who performs the action? | Librarian | Click "Check Overdue" | Updates overdue record statuses |
| dueDate vs today? | ≤ today | BR001 (15/09/2024) | Marked "Overdue" |
| | > today | BR003 (15/10/2024) | Remains "Borrowed" |
| Member views record? | Own record is overdue | MEM002 after check | Sees "Overdue" status |

### IDM — Member Management (REQ-07)

| **Characteristic** | **Block/Partition** | **Representative Value** | **Expected Result** |
|---|---|---|---|
| Who performs the action? | Librarian | librarian@library.com | Allowed to add members |
| | Member | ba.nguyen@email.com | No tab/permission to add |
| Is email valid? | Valid | new.member@email.com | Created successfully |
| | Invalid (missing .) | user@domain | Rejected, error message |
| Is email a duplicate? | Duplicate | ba.nguyen@email.com | Rejected, duplicate email message |

### IDM — Borrowing Record Lookup (REQ-08)

| **Characteristic** | **Block/Partition** | **Representative Value** | **Expected Result** |
|---|---|---|---|
| Who looks up? | Librarian | Look up MEM003 | Can see all records for MEM003 |
| | Member | MEM002 views records | Only sees own (MEM002) records |
| Record info? | Valid record | BR001 | Shows code, book, borrow date, due date, status |
| Member views another's records? | Member views others | MEM002 looks up MEM003 | Not allowed / rejected |

> 💡 **Technique Tip:** Use **Equivalence Partitioning (EP)** for discrete partitions and **Boundary Value Analysis (BVA)** for numeric partitions (e.g., 3-book limit). See textbook §6.1–6.3.

---

## Step 2: Test Cases

### REQ-01 — Login

| **TC ID** | **Test Objective** | **Precondition** | **Steps** | **Input Data** | **Expected Result** | **Technique** |
|---|---|---|---|---|---|---|
| TC-01 | Librarian login success | Login page open, seed data loaded | 1. Enter librarian email. 2. Enter password. 3. Click **Login**. | Email: librarian@library.com, PW: admin123 | Navigate to home. AppBar shows name "Nguyen Thu Thu" and Librarian role. | EP |
| TC-02 | Member login success | Login page open, seed data loaded | 1. Enter member email. 2. Enter password. 3. Click **Login**. | Email: ba.nguyen@email.com, PW: password123 | Navigate to home. AppBar shows name "Nguyen Hoc Ba" and Member role. | EP |
| TC-03 | Login with non-existent email | Login page open | 1. Enter email not in system. 2. Enter any password. 3. Click **Login**. | Email: noone@email.com, PW: password123 | Error message: **"Member not found"**. Page does not change. | EP |
| TC-04 | Login with incorrect password | Login page open | 1. Enter valid email. 2. Enter wrong password. 3. Click **Login**. | Email: ba.nguyen@email.com, PW: wrongpass | Error message: **"Incorrect password"**. Page does not change. | EP |
| TC-05 | Login with empty email and password | Login page open | 1. Leave Email field empty. 2. Leave Password field empty. 3. Click **Login**. | Email: *(empty)*, PW: *(empty)* | Message: **"Please enter email and password"**. Page does not change. | EP, BVA |
| TC-06 | Login as Suspended member | Login page, MEM004 Suspended | 1. Enter MEM004 email. 2. Enter password. 3. Click **Login**. 4. Observe AppBar. | Email: cu.le@email.com, PW: password123 | Login successful. AppBar shows **"Le Can Cu (Member)"**. | EP |
| TC-07 | Login as Expired member | Login page, MEM005 Expired | 1. Enter MEM005 email. 2. Enter password. 3. Click **Login**. | Email: binh.pham@email.com, PW: password123 | Login successful. AppBar shows **"Pham Trung Binh (Member)"**. | EP |
| TC-31 | Login with only email empty (password filled) | Login page open | 1. Leave Email field empty. 2. Enter a valid password. 3. Click **Login**. | Email: *(empty)*, PW: password123 | Message: **"Please enter email and password"** (or equivalent). Page does not change. | EP, BVA |

### REQ-02 — View Book List

| **TC ID** | **Test Objective** | **Precondition** | **Steps** | **Input Data** | **Expected Result** | **Technique** |
|---|---|---|---|---|---|---|
| TC-08 | View full book list with complete info | Logged in as Member ba.nguyen@email.com, on **Books** tab | 1. Log in as member. 2. Open **Books** tab. 3. Observe book list. | Account: ba.nguyen@email.com / password123 | Shows 20 books. Each book has: Title, Author, Genre, Year, Status (Available, Borrowed, Lost), Book ID. | EP |
| TC-09 | Real-time book status update after borrowing | Logged in as Member MEM006, BOOK001 status "Available" | 1. In **Books** tab, confirm BOOK001 is "Available". 2. Borrow BOOK001 (+ button). 3. Return to **Books** tab. | Account: biet.hoang@email.com, Book: BOOK001 | BOOK001 changes to **"Borrowed"** immediately, no refresh needed. | EP |

### REQ-03 — Search & Filter Books

| **TC ID** | **Test Objective** | **Precondition** | **Steps** | **Input Data** | **Expected Result** | **Technique** |
|---|---|---|---|---|---|---|
| TC-10 | Search books by title | Logged in, on **Books** tab | 1. Enter keyword in search box. 2. Press search or Enter. 3. Observe results. | Keyword: Flutter | Shows BOOK005 "Artificial Intelligence Overview", BOOK013 "Modern HR Management", BOOK019 "Vietnamese Literature Overview". No unrelated books shown. | EP |
| TC-11 | Search books by author name | Logged in, on **Books** tab | 1. Enter author name in search box. 2. Press search. 3. Observe results. | Keyword: Nguyen | Shows all books with author containing "Nguyen". | EP |
| TC-12 | Case-insensitive search | Logged in, on **Books** tab | 1. Search with lowercase flutter. 2. Note results. 3. Clear, search with FLUTTER. | Keywords: flutter / Flutter | Both searches return the same results: BOOK005, BOOK013, BOOK019. | EP |
| TC-13 | Search with no results | Logged in, on **Books** tab | 1. Enter a non-existent keyword. 2. Press search. 3. Observe message. | Keyword: XYZ123 | Message: **"No books found."** List is empty. | EP |
| TC-14 | Filter books by genre (case-insensitive) | Logged in, on **Books** tab | 1. Filter **Technology**. 2. Filter **technology** (lowercase). 3. Filter **TECHNOLOGY** (uppercase). 4. Compare results. | Genre: Technology / technology / TECHNOLOGY | All three inputs return the **same** set of Technology books; non-matching genre shows **"No books found."** | EP |
| TC-32 | Clear search keyword → full book list restored | Logged in, **Books** tab, keyword "Flutter" already searched | 1. Clear the search box (delete all text). 2. Press search or Enter. 3. Observe results. | Keyword: *(empty after clear)* | All **20 books** are shown again. No filter remains applied. | EP |
| TC-37 | Combined search + filter: keyword absent in selected category → no result | Logged in, **Books** tab | 1. Filter by genre **"Technology"**. 2. Enter keyword **"Nguyen Van An"** (exists in Literature, not Technology) in search box. 3. Press search. 4. Observe results. | Genre: Technology, Keyword: Nguyen Van An | System shows **no results** (or "No books found") because keyword does not exist in the filtered category. Filter must NOT be silently ignored. | EP |

### REQ-04 — Borrow Book

| **TC ID** | **Test Objective** | **Precondition** | **Steps** | **Input Data** | **Expected Result** | **Technique** |
|---|---|---|---|---|---|---|
| TC-15 | Borrow book successfully (happy path) | Logged in as Member MEM002, BOOK002 "Available", currently borrowing 1 book (BOOK003) | 1. **Books** tab. 2. Borrow BOOK002 (+ button). 3. Confirm borrow. 4. Check **Borrow/Return** tab for record. | Account: ba.nguyen@email.com, Book: BOOK002 | Borrow successful. New record created with status **"Borrowed"**, due date = borrow date + 14 days. BOOK002 changes to **"Borrowed"**. | EP, Decision Table |
| TC-16 | Reject borrowing a book already on loan | Logged in as Member MEM003, BOOK003 borrowed by MEM002 | 1. **Books** tab. 2. Find BOOK003. 3. Try to borrow (+ button). 4. Observe response. | Account: dam.tran@email.com, Book: BOOK003 | System rejects. BOOK003 shows **"Borrowed"**, cannot be borrowed. No new record created. | EP, Decision Table |
| TC-17 | Reject borrow for Suspended member | Logged in as Member MEM004, status Suspended | 1. **Books** tab. 2. Try to borrow BOOK004 (+ button). 3. Observe message. | Account: cu.le@email.com, Book: BOOK004 | Borrow rejected. Message cites **suspension** (distinct from expiry). | EP, Decision Table |
| TC-18 | Reject borrow for Expired member | Logged in as Member MEM005, status Expired | 1. **Books** tab. 2. Try to borrow BOOK008 (+ button). 3. Observe message. | Account: binh.pham@email.com, Book: BOOK008 | Borrow rejected. Message cites **expiry** (distinct from suspension). | EP, Decision Table |
| TC-19 | Reject borrow when 3-book limit reached (BVA) | Logged in as Member MEM002, already borrowing 3 books | 1. **Books** tab. 2. Borrow BOOK005. 3. Observe response. | Account: ba.nguyen@email.com, Book: BOOK005 | Borrow attempt shows **limit of 3 books exceeded** message. No 4th borrow record created. | BVA, Decision Table |
| TC-20 | Reject borrowing a Lost book | Logged in as Member MEM006, BOOK007 status Lost | 1. **Books** tab. 2. Find BOOK007. 3. Try to borrow (+ button). 4. Observe response. | Account: biet.hoang@email.com, Book: BOOK007 | System rejects. BOOK007 shows **"Lost"**, cannot be borrowed. No record created. | EP, Decision Table |
| TC-33 | Borrow allowed at BVA boundary (2 active borrows) | Logged in as Member MEM002, currently borrowing 1 book (BOOK003) | 1. **Books** tab. 2. Find an Available book (e.g., BOOK001). 3. Borrow it (+ button). 4. Confirm in **Borrow/Return** tab. | Account: ba.nguyen@email.com, Book: BOOK001 | Borrow succeeds. Member now has **2 active borrows** — exactly at BVA point below the limit. New record created with status "Borrowed". | BVA |

### REQ-05 — Return Book

| **TC ID** | **Test Objective** | **Precondition** | **Steps** | **Input Data** | **Expected Result** | **Technique** |
|---|---|---|---|---|---|---|
| TC-21 | Return book successfully | Logged in as Librarian; MEM006 borrowing BOOK013 (record BR003) | 1. **Borrow/Return** tab. 2. Return borrow record for BOOK013 by MEM006. 3. Confirm return. 4. Check **Books** tab. | Librarian: librarian@library.com, Record: BR003, Book: BOOK013 | Return successful. Record changes to **"Returned"**. BOOK013 returns to **"Available"**. | EP |
| TC-22 | Return a book that was not borrowed | Logged in as Member MEM003, not currently borrowing BOOK008 | 1. **Borrow/Return** tab. 2. Try to return BOOK008. 3. Observe response. | Account: dam.tran@email.com, Book: BOOK008 | System rejects. No return record created. BOOK008 remains **"Available"**. | EP |
| TC-38 | Return overdue book — overdue warning must be displayed | Logged in as Librarian; BR001 has status "Overdue" (Check Overdue already run) | 1. **Borrow/Return** tab. 2. Return BR001 (BOOK003 for MEM002). 3. Confirm return. 4. Observe any warning or notification. | Account: librarian@library.com, Record: BR001 | Return succeeds **AND** system displays an overdue warning (e.g., *"This book was returned overdue"*). Record changes to "Returned". | EP |

### REQ-06 — Overdue Processing

| **TC ID** | **Test Objective** | **Precondition** | **Steps** | **Input Data** | **Expected Result** | **Technique** |
|---|---|---|---|---|---|---|
| TC-23 | Librarian checks and marks overdue records | Logged in as Librarian, BR001 has dueDate 15/09/2024 ≤ today | 1. **Borrow/Return** tab. 2. Click **Check Overdue**. 3. Look up record BR001. | Record: BR001 (MEM002, BOOK003) | BR001 changes status to **"Overdue"**. Librarian sees it in overdue list. | EP, BVA |
| TC-24 | Member views own overdue record | Librarian ran "Check Overdue", BR001 belongs to MEM002 | 1. Log in as ba.nguyen@email.com. 2. **Borrow/Return** tab → my records. 3. Observe BR001. | Account: MEM002 | MEM002 sees BR001 with status **"Overdue"**. Cannot see other members' records. | EP |
| TC-35 | Librarian sees all overdue records across all members | Logged in as Librarian, Check Overdue already run (BR001 is Overdue) | 1. **Borrow/Return** tab. 2. Observe overdue record list or search all members. 3. Confirm records from multiple members appear. | Account: librarian@library.com | Librarian sees overdue records for **all members** (e.g., MEM002's and any other member's overdue records). Not limited to one member. | EP |

### REQ-07 — Member Management

| **TC ID** | **Test Objective** | **Precondition** | **Steps** | **Input Data** | **Expected Result** | **Technique** |
|---|---|---|---|---|---|---|
| TC-25 | Add new member successfully | Logged in as Librarian, **Members** tab | 1. Click **Add Member**. 2. Enter complete valid information. 3. Save. 4. Find new member in list. | Name: Tran Thi Tay, Email: tay.tran@email.com, Phone: 0363636361 | Created successfully. New member appears in list with Active status. | EP |
| TC-26 | Reject adding member with invalid email | Logged in as Librarian, **Members** tab | 1. Click **Add Member**. 2. Enter email missing the . in domain. 3. Click Save. | Email: tay.tran@emailcom (invalid per SRS) | System rejects. Shows invalid email message. No new member created. | EP, BVA |
| TC-27 | Reject adding member with duplicate email | Logged in as Librarian, **Members** tab | 1. Click **Add Member**. 2. Enter existing email. 3. Click Save. | Email: ba.nguyen@email.com (already exists) | Rejected. Message: **email already exists / duplicate**. | EP |
| TC-28 | Member has no permission to manage members | Logged in as ba.nguyen@email.com | 1. Observe bottom navigation bar. 2. Look for **Members** tab / **Add Member** button. | Account: MEM002 | **No** Members tab or Add Member function is visible. | EP |

### REQ-08 — Borrow Record Lookup

| **TC ID** | **Test Objective** | **Precondition** | **Steps** | **Input Data** | **Expected Result** | **Technique** |
|---|---|---|---|---|---|---|
| TC-29 | Librarian looks up borrow records for any member | Logged in as Librarian, **Borrow/Return** tab | 1. Look up records by MEM003 code. 2. Observe record list. 3. Check record details. | Member ID: MEM003 | Shows all records for MEM003. Each record has: Record ID, Book, Borrow Date, Due Date, Status. | EP |
| TC-30 | Member only views own borrow records | Logged in as MEM002, **Borrow/Return** tab | 1. Open **Borrow/Return** tab. 2. Observe record list. 3. Confirm no records from MEM003/MEM006. | Account: ba.nguyen@email.com | Only sees MEM002 records (e.g. BR001). Cannot see other members' records. | EP |
| TC-36 | Returned record displays correct status and all fields | Logged in as MEM002; TC-21 return already completed | 1. Log in as ba.nguyen@email.com. 2. **Borrow/Return** tab. 3. Find a returned record. 4. Check all displayed fields. | Account: ba.nguyen@email.com | Record shows status **"Returned"** with all fields: Record ID, Book title, Borrow Date, Due Date, Status. No fields missing or blank. | EP |
| TC-39 | Member cannot view or return another member's records by searching | Logged in as Member MEM002, **Borrow/Return** tab | 1. In **Borrow/Return** tab, search for member ID **MEM006** or enter MEM006's code. 2. Observe whether MEM006's records appear. 3. If visible, try to click Return on one of MEM006's records. | Account: ba.nguyen@email.com, Search target: MEM006 | MEM006's records are **not visible** to MEM002. Any search attempt returns empty or is rejected. MEM002 cannot return a book on behalf of MEM006. | EP |

---

## Summary

| **Feature Group** | **# TCs** | **REQ Coverage** | **IDM Techniques Applied** |
|---|---|---|---|
| Login | 8 | REQ-01 | EP, BVA |
| View Book List | 2 | REQ-02 | EP |
| Search & Filter Books | 7 | REQ-03 | EP |
| Borrow Book | 7 | REQ-04 | EP, BVA, Decision Table |
| Return Book | 3 | REQ-05 | EP |
| Overdue Processing | 3 | REQ-06 | EP, BVA |
| Member Management | 4 | REQ-07 | EP, BVA |
| Borrow Record Lookup | 4 | REQ-08 | EP |
| **Total** | **38** | **REQ-01 → REQ-08** | **EP, BVA, Decision Table** |
 