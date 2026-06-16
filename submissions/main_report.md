# Software Testing & Quality Assurance (STQA)

**System Under Test (SUT):** https://stqa.rbc.vn
**Reference Document:** SRS v1.0
**Report Date:** 20/05/2026
**Browser:** Chrome (Latest Version)
**OS:** Windows 11

---

## 1. Project Team Members (STQA Group 22)

| # | Student ID | Full Name | Role |
|---|------------|-----------|------|
| 1 | 23BA14158 | Nguyên Chí Kiên | Team Leader |
| 2 | 23BA14148 | Phạm Vũ Khánh | Member |
| 3 | 23BA14059 | Nguyên Minh Đức | Member |
| 4 | 2410761 | Nguyên Tú Oanh | Member |
| 5 | 23BA14107 | Đào Trung Hiêu | Member |

---

## 2. Input Domain Modeling (IDM)

Before designing the test cases, the input domain was analyzed using Equivalence Partitioning (EP) for discrete partitions and Boundary Value Analysis (BVA) for numerical and date-based boundaries.

### IDM — Login (REQ-01)

| Characteristic | Block / Partition | Representative Value | Expected Result |
|---|---|---|---|
| Does email exist in DB? | Yes | librarian@library.com | Login successful |
| | No | nobody@test.com | Message: "Member not found" |
| Is password correct? | Correct | admin123 | Login successful |
| | Incorrect | wrongpassword | Message: "Incorrect password" |
| Are fields empty? | Not empty | (any value) | Process normally |
| | Empty | "" | Message: "Please enter email and password" |

### IDM — View Book List (REQ-02)

| Characteristic | Block / Partition | Representative Value | Expected Result |
|---|---|---|---|
| User role? | Librarian | librarian@library.com | Can view full book list |
| | Member | ba.nguyen@email.com | Can view full book list |
| Book status displayed? | Available | BOOK001 | Shows "Available" |
| | Borrowed | BOOK003 | Shows "Borrowed" |
| Updated after borrow? | Changed | Borrow BOOK001 | Status changes to "Borrowed" immediately |

### IDM — Search Books (REQ-03)

| Characteristic | Block / Partition | Representative Value | Expected Result |
|---|---|---|---|
| Does keyword exist in DB? | Yes (book title) | "Flutter" | Shows books containing "Flutter" |
| | Yes (author name) | "Nguyen" | Shows books by author Nguyen |
| | No | "XYZ123" | Message: "Book not found" |
| Case-sensitive? | Lowercase | "flutter" | Same result as "Flutter" |
| | Uppercase | "FLUTTER" | Same result as "Flutter" |
| Filter by genre? | Books in genre | "Technology" | Shows only Technology books |
| | No books in genre | "Non-existent genre" | Empty list or no results message |

### IDM — Borrow Book (REQ-04)

| Characteristic | Block / Partition | Representative Value | Expected Result |
|---|---|---|---|
| Who performs the action? | Member | ba.nguyen@email.com | Can borrow (Books tab, + button) |
| | Librarian | librarian@library.com | Cannot borrow on behalf of member |
| Book status? | Available | BOOK001 | Borrowing allowed |
| | Borrowed | BOOK003 | Not allowed |
| | Lost | BOOK007 | Not allowed |
| Member status? | Active | MEM002 | Borrowing allowed |
| | Suspended | MEM004 | Rejected, suspended message |
| | Expired | MEM005 | Rejected, expired message |
| Number of books currently borrowed? | < 3 (BVA: 0, 1, 2) | MEM006 (1 book) | Borrowing allowed |
| | = 3 (BVA: limit) | Member with 3 books | Rejected, limit exceeded message |

### IDM — Return Book (REQ-05)

| Characteristic | Block / Partition | Representative Value | Expected Result |
|---|---|---|---|
| Who performs the action? | Librarian | librarian@library.com | Can return (Borrow/Return tab) |
| | Member | ba.nguyen@email.com | Can return own borrowing records |
| Is the book currently borrowed? | Yes | BOOK013 (MEM006) | Return allowed, book returns to "Available" |
| | No | BOOK001 (not borrowed) | Return rejected |
| Is return overdue? | Overdue | BR001 (MEM002) | Return successful + overdue warning displayed |
| | On time | BR003 (MEM006) | Return successful, no overdue warning |

### IDM — Overdue Processing (REQ-06)

| Characteristic | Block / Partition | Representative Value | Expected Result |
|---|---|---|---|
| Who performs the action? | Librarian | Click "Check Overdue" | Updates overdue record statuses |
| dueDate vs today? | ≤ today | BR001 (15/09/2024) | Marked "Overdue" |
| | > today | BR003 (15/10/2024) | Remains "Borrowed" |
| Member views record? | Own record is overdue | MEM002 after check | Sees "Overdue" status |

### IDM — Member Management (REQ-07)

| Characteristic | Block / Partition | Representative Value | Expected Result |
|---|---|---|---|
| Who performs the action? | Librarian | librarian@library.com | Allowed to add members |
| | Member | ba.nguyen@email.com | No tab/permission to add |
| Is email valid? | Valid | new.member@email.com | Created successfully |
| | Invalid (missing .) | user@domain | Rejected, error message |
| Is email a duplicate? | Duplicate | ba.nguyen@email.com | Rejected, duplicate email message |

### IDM — Borrowing Record Lookup (REQ-08)

| Characteristic | Block / Partition | Representative Value | Expected Result |
|---|---|---|---|
| Who looks up? | Librarian | Look up MEM003 | Can see all records for MEM003 |
| | Member | MEM002 views records | Only sees own (MEM002) records |
| Record info? | Valid record | BR001 | Shows code, book, borrow date, due date, status |
| Member views another's records? | Member views others | MEM002 looks up MEM003 | Not allowed / rejected |

---

## 3. Detailed Test Cases & Execution Results

### REQ-01 — Login

- **TC-01 (Pass):** Librarian login success. AppBar shows name "Nguyen Thu Thu" and Librarian role. (Technique: EP)
- **TC-02 (Pass):** Member login success. AppBar shows name "Nguyen Hoc Ba" and Member role. (Technique: EP)
- **TC-03 (Pass):** Non-existent email (noone@email.com) → rejected with "Member not found" message. (Technique: EP)
- **TC-04 (Pass):** Wrong password → rejected with "Incorrect password" message. (Technique: EP)
- **TC-05 (Pass):** Empty email and password → displays "Please enter email and password". (Technique: EP, BVA)
- **TC-06 (Pass):** Suspended member (MEM004) logs in successfully. AppBar shows "Le Can Cu (Member)". (Technique: EP)
- **TC-07 (Pass):** Expired member (MEM005) logs in successfully. AppBar shows "Pham Trung Binh (Member)". (Technique: EP)
- **TC-31 (Pass):** Only email field empty → rejected with "Please enter email and password" message. (Technique: EP, BVA)

### REQ-02 — View Book List

- **TC-08 (Pass):** View full book list (20 books) with all standard info fields present. (Technique: EP)
- **TC-09 (Pass):** Real-time status update: book changes to "Borrowed" immediately after borrowing. (Technique: EP)

### REQ-03 — Search & Filter Books

- **TC-10 (Pass):** Search books by title "Flutter" → successfully returns BOOK005, BOOK013, BOOK019. (Technique: EP)
- **TC-11 (Pass):** Search books by author name "Nguyen" → returns books containing author "Nguyen". (Technique: EP)
- **TC-12 (Pass):** Case-insensitive keyword search (flutter vs FLUTTER return identical results). (Technique: EP)
- **TC-13 (Pass):** Search with non-existent keyword → displays "No books found". (Technique: EP)
- **TC-14 (Fail):** Genre filter is case-sensitive. Selecting Technology works, but lowercase/uppercase options show no results. (Technique: EP) → **[BUG-02]**
- **TC-32 (Pass):** Clear search box keyword → full list of 20 books is completely restored. (Technique: EP)
- **TC-37 (Fail):** Combined search + filter ignores genre filter constraint when a keyword exists outside that category. (Technique: EP) → **[BUG-06]**

### REQ-04 — Borrow Book

- **TC-15 (Pass):** Borrow book successfully (Happy Path). Record status becomes "Borrowed" and due date is set to +14 days. (Technique: EP, Decision Table)
- **TC-16 (Pass):** Reject borrowing an already borrowed book. (Technique: EP, Decision Table)
- **TC-17 (Pass):** Reject borrow attempt for Suspended member (MEM004) with an explicit suspension message. (Technique: EP, Decision Table)
- **TC-18 (Fail):** Reject borrow for Expired member (MEM005) displays a "suspended" instead of an "expired" message. (Technique: EP, Decision Table) → **[BUG-03]**
- **TC-19 (Fail):** 3-book limit validation (BVA). The system mistakenly allows borrowing a 4th book concurrently. (Technique: BVA, Decision Table) → **[BUG-01]**
- **TC-20 (Pass):** Reject borrowing a book with "Lost" status. (Technique: EP, Decision Table)
- **TC-33 (Pass):** Borrow succeeds at BVA boundary (member successfully borrows a 2nd active book). (Technique: BVA)

### REQ-05 — Return Book

- **TC-21 (Pass):** Return book successfully. Record changes to "Returned" and book becomes "Available". (Technique: EP)
- **TC-22 (Pass):** Attempting to return a book that was not borrowed is rejected. (Technique: EP)
- **TC-38 (Pass):** Returning an overdue book displays an overdue warning to the librarian. (Technique: EP)

### REQ-06 — Overdue Processing

- **TC-23 (Fail):** "Check Overdue" routine fails to flag records due exactly today. Second execution reports 0 records. (Technique: EP, BVA) → **[BUG-04]**
- **TC-24 (Pass):** Member can successfully see their own overdue record after verification. (Technique: EP)
- **TC-35 (Pass):** Librarian sees all overdue records globally across all library members. (Technique: EP)

### REQ-07 — Member Management

- **TC-25 (Fail):** Adding a new member with fully valid details is incorrectly rejected with an "Invalid email" error. (Technique: EP) → **[BUG-05]**
- **TC-26 (Fail):** An invalid email format missing a dot (tay.tran@emailcom) is accepted during registration. (Technique: EP, BVA) → **[BUG-05]**
- **TC-27 (Fail):** Registering a duplicate email returns a generic "Invalid email" instead of a duplicate message. (Technique: EP) → **[BUG-05]**
- **TC-28 (Pass):** Member accounts have no visual visibility or permission to access the Member Management tab. (Technique: EP)

### REQ-08 — Borrow Record Lookup

- **TC-29 (Pass):** Librarian can look up and see all detailed fields of borrow records for any member. (Technique: EP)
- **TC-30 (Pass):** Regular members are strictly restricted to seeing only their own borrowing records. (Technique: EP)
- **TC-36 (Pass):** Returned records display the "Returned" status correctly along with all required standard fields. (Technique: EP)
- **TC-39 (Fail):** Cross-authorization breach. A Member can search for another member's ID to view and return books on their behalf. (Technique: EP) → **[BUG-07]**

---

## 4. Defect Reports (Bug Reports)

### BUG-01: Member can borrow a 4th book simultaneously, bypassing the max limit of 3

- **Bug ID:** BUG-01 | **Related TC:** TC-19 | **Requirement:** REQ-04, BR-01
- **Severity:** High — Violates a core business logic constraint, creating risk of library book inventory loss.
- **Detected By:** Nguyên Tú Oanh
- **Reproduction Steps:**
  1. Log in as a Member or a Librarian with appropriate borrow permissions.
  2. Consecutively borrow 3 books for the same active member account.
  3. Attempt to borrow a 4th book that is currently marked "Available".
  4. Review the member's profile and count active records with "Borrowed" status.
- **Expected Result:** The 4th attempt must be blocked with an error message indicating the max limit of 3 books has been reached.
- **Actual Result:** The system allows the 4th book to be borrowed successfully, resulting in 4 concurrent active loans.
- **Suggested Fix:** Update the condition check before processing a borrow transaction to `borrowed_count >= 3`.

### BUG-02: Book genre filter is case-sensitive, violating SRS search rules

- **Bug ID:** BUG-02 | **Related TC:** TC-14 | **Requirement:** REQ-03
- **Severity:** Medium — Functional inconsistency with search rules; typing a valid category with varied casing shows no books.
- **Detected By:** Nguyên Minh Đức
- **Reproduction Steps:**
  1. Navigate to the Books tab.
  2. Filter by genre using the exact casing `Technology`.
  3. Re-filter the list using lowercase `technology` or uppercase `TECHNOLOGY`.
- **Expected Result:** Per REQ-03, search and filtering are case-insensitive. All three variations should return the exact same matching list.
- **Actual Result:** Only `Technology` works. Inputting lowercase or uppercase variations throws a "No books found" error.
- **Suggested Fix:** Apply `.toLowerCase()` to string data before checking conditions to handle case insensitivity.

### BUG-03: Borrow rejection displays incorrect "suspended" error message for Expired members

- **Bug ID:** BUG-03 | **Related TC:** TC-18 | **Requirement:** REQ-04
- **Severity:** Medium — Confusing feedback; heavily impacts user support and customer service operations.
- **Detected By:** Phạm Vũ Khánh
- **Reproduction Steps:**
  1. Log in as a Librarian.
  2. Attempt to borrow an available book for member MEM005 (whose status is explicitly set to Expired).
- **Expected Result:** Transaction is blocked with a message indicating the membership is expired.
- **Actual Result:** Transaction is blocked but displays a "Member is suspended..." warning message.
- **Suggested Fix:** Route status checks accurately so that expired statuses display expiration templates rather than suspension errors.

### BUG-04: "Check Overdue" misses records due exactly today; second run yields 0 records

- **Bug ID:** BUG-04 | **Related TC:** TC-23 | **Requirement:** REQ-06
- **Severity:** High — Off-by-one boundary date logic failure that distorts fines and return alerts.
- **Detected By:** Nguyên Chí Kiên
- **Reproduction Steps:**
  1. Insert a mock borrow record whose dueDate matches exactly today's date.
  2. Click Check Overdue (Execution 1) and note the confirmation toast message.
  3. Without resetting data, click Check Overdue a second time (Execution 2).
- **Expected Result:** All records with `dueDate <= today` are flagged as "Overdue". Subsequent manual triggers should remain idempotent and accurate.
- **Actual Result:** Records due today are not flagged as overdue (uses `>` instead of `>=`). Execution 2 outputs a broken "Updated: 0 overdue records" message.
- **Suggested Fix:** Fix date operators to `today >= dueDate`. Make sure the asynchronous routine is idempotent.

### BUG-05: Member form mishandles email validation (blocks valid, accepts invalid, misreports duplicates)

- **Bug ID:** BUG-05 | **Related TC:** TC-25, TC-26, TC-27 | **Requirement:** REQ-07, BR-08
- **Severity:** High — Compromises member data collection integrity by rejecting valid inputs and accepting malformed data.
- **Detected By:** Đào Trung Hiêu
- **Reproduction Steps:**
  - Scenario A: Attempt to add a member with a valid email format (tay.tran@email.com).
  - Scenario B: Attempt to add a member with an invalid email format lacking a dot (tay.tran@emailcom).
  - Scenario C: Attempt to add a member with a pre-existing email (ba.nguyen@email.com).
- **Expected Result:** Scenario A succeeds. Scenario B is blocked via layout formatting check. Scenario C is blocked with a unique duplicate error message.
- **Actual Result:** Scenario A is blocked with "Invalid email". Scenario B is successfully added. Scenario C throws a generic "Invalid email" error instead of a duplicate warning.
- **Suggested Fix:** Bind input text against the explicit SRS email regex pattern and divide exception blocks into `DUPLICATE_EMAIL` and `INVALID_EMAIL_FORMAT`.

### BUG-06: Combined search + filter ignores active genre selection when keyword is found elsewhere

- **Bug ID:** BUG-06 | **Related TC:** TC-37 | **Requirement:** REQ-03
- **Severity:** Medium — Violates compound filter logic (AND operator), feeding misleading query results to users.
- **Detected By:** Nguyên Minh Đức
- **Reproduction Steps:**
  1. Select the "Technology" genre filter from the dropdown.
  2. Type the author name "Nguyen Van An" (who has no books categorized under Technology) into the search bar and press Enter.
- **Expected Result:** Since conditions must be combined using AND logic, the screen should read "No books found".
- **Actual Result:** The genre filter is dropped entirely, displaying all books written by the author across all categories.
- **Suggested Fix:** Ensure backend queries enforce all active parameters simultaneously: `WHERE genre = ? AND (title LIKE ? OR author LIKE ?)`.

### BUG-07: [CRITICAL] Member can view and perform return actions on other members' borrow history

- **Bug ID:** BUG-07 | **Related TC:** TC-39 | **Requirement:** REQ-08
- **Severity:** Critical — Broken Object Level Authorization (BOLA); major data confidentiality and data privacy vulnerability.
- **Detected By:** Đào Trung Hiêu
- **Reproduction Steps:**
  1. Log in using a standard Member account (MEM002).
  2. Navigate directly to the Borrow/Return tab.
  3. Inside the search bar, look up the target ID of another member (MEM006).
  4. Check data visibility and try to interact with the action buttons on the retrieved records.
- **Expected Result:** Members must be completely sandboxed to their own profile entries. Searching other account handles must yield empty results or throw access errors.
- **Actual Result:** MEM002 can view MEM006's entire private borrow history and click Return to check in books on their behalf.
- **Suggested Fix:** Enforce rigorous server-side authorization mapping by constraining query tokens to the session user ID: `WHERE member_id = session.user_id`.

---

## 5. Test Summary Metrics

### 5.1. Overall Execution Summary

- **Total Test Cases:** 38
- **Passed:** 29
- **Failed:** 9
- **Blocked / Not Run:** 0
- **Pass Rate:** 76.3%
- **Total Bugs Documented:** 7

### 5.2. Metrics broken down by Requirement Group

| Feature Group | Requirement ID | Total TCs | Pass | Fail | Pass Rate | Related Bugs |
|---|---|---|---|---|---|---|
| Login | REQ-01 | 8 | 8 | 0 | 100% | — |
| View Book List | REQ-02 | 2 | 2 | 0 | 100% | — |
| Search & Filter | REQ-03 | 7 | 5 | 2 | 71.4% | BUG-02, BUG-06 |
| Borrow Book | REQ-04 | 7 | 5 | 2 | 71.4% | BUG-01, BUG-03 |
| Return Book | REQ-05 | 3 | 3 | 0 | 100% | — |
| Overdue Processing | REQ-06 | 3 | 2 | 1 | 66.7% | BUG-04 |
| Member Management | REQ-07 | 4 | 1 | 3 | 25.0% | BUG-05 |
| Borrow Record Lookup | REQ-08 | 4 | 3 | 1 | 75.0% | BUG-07 |
| **Total** | | **38** | **29** | **9** | **76.3%** | |

### 5.3. Bug Distribution by Severity

| Severity Level | Count | Defect Identifiers |
|---|---|---|
| Critical | 1 | BUG-07 |
| High | 3 | BUG-01, BUG-04, BUG-05 |
| Medium | 3 | BUG-02, BUG-03, BUG-06 |
| Low | 0 | — |

---

## 6. Product Quality Assessment & Recommendations

### 6.1. Quality Assessment

**Stable Functionality:** Core authentication states hold successfully (REQ-01). Standard tabular rendering for books works as expected (REQ-02). Clean return pathways execute cleanly under normal operational parameters (REQ-05).

**Critical Risks:** The system has an authorization vulnerability (BUG-07) where cross-tenant member records are exposed. Key business rules are un-enforced, allowing members to exceed book limits (BUG-01) and accepting broken input patterns during sign-up (BUG-05).

### 6.2. Recommended Fix Priority

**1. P1 (Immediate Fix Required):**
- **BUG-07:** Critical authorization bypass — exposes member loan histories globally.
- **BUG-01:** Breaks inventory capacity constraints by allowing a 4th book loan.
- **BUG-05:** Risks database integrity with bad email strings and bad exception filtering.
- **BUG-04:** Directly breaks core business logic, notifications, and fine tracking schedules.

**2. P2 (Normal Track):**
- **BUG-03:** Misrepresents account status context to users.
- **BUG-06:** Compound filter failure generates misleading query outputs.

**3. P3 (Low Priority / Cosmetic):**
- **BUG-02:** Adjust filter inputs to ignore character case patterns.

---

## 7. Release Conclusion

The product **SHOULD NOT BE RELEASED** in its current build. Although the net testing statistics yield a 76.3% pass rate, the presence of a Critical authorization bug (BUG-07) presents an immediate compliance risk. High-severity functional defects are concentrated in central business areas (REQ-04, REQ-06, REQ-07), further undermining overall operational stability.

Once all identified P1 issues are resolved, a mandatory regression testing cycle must be initiated across the following boundary scenarios:

- **REQ-03:** TC-37 (Compound query matching)
- **REQ-04:** TC-19 (Account capacity constraints)
- **REQ-06:** TC-23 (Daily scheduler validation)
- **REQ-07:** TC-25 through TC-27 (Input validation)
- **REQ-08:** TC-39 (Cross-object permission verification)

---

## 8. Lessons Learned

- **Pre-testing SRS analysis and IDM design are essential.** Mapping out requirements systematically helped identify specific testing partitions, ensuring critical business limits and formatting rules were covered rather than just standard paths.
- **Boundary Value Analysis (BVA) uncovers flaws missed by Equivalence Partitioning.** Off-by-one errors in logic constraints and date tracking configurations are classic boundary bugs that would have slipped past standard validation.
- **Decision Tables uncover logical edge cases.** Analyzing intersecting business rules helped identify where the system failed to differentiate between distinct error states, such as account suspensions versus expirations.
- **Isolate distinct test assertions.** Keeping validation vectors separate across test setups prevents compound logic issues from masking separate root defects, making debugging cleaner.
- **Classify issue severity by business impact rather than visibility.** High-frequency UI bugs are minor if they do not corrupt database records, whereas rare edge cases that break structural data constraints deserve immediate engineering attention.
- **Provide actionable technical feedback to speed up resolution.** Outlining specific operator fixes or database query adjustments saves engineering triage time when working across asynchronous team pipelines.
