# Test Execution — Test Results

> **Instructions:** Run each TC against the system at https://stqa.rbc.vn and record the actual results.
>
> **Verdict definitions:**
> - **Pass** — actual result matches expected
> - **Fail** — actual result is incorrect → create bug report
> - **Blocked** — cannot execute due to a blocking defect elsewhere
> - **Not Run** — not yet executed

| **Info** | |
|---|---|
| **Group** | STQA Group 22 |
| **Execution Date** | 20/05/2026 |
| **Browser** | Chrome (latest version) |
| **OS** | Windows 11 |

---

## Detailed Results

| **TC ID** | **Feature Group** | **Expected Result (summary)** | **Actual Result** | **Verdict** | **Evidence** | **Bug** |
|---|---|---|---|---|---|---|
| TC-01 | REQ-01 Login | Librarian logs in, shows Nguyen Thu Thu | Login OK, AppBar shows correct name/role for Librarian | **Pass** | | |
| TC-02 | REQ-01 Login | Member logs in, shows Nguyen Hoc Ba | Login OK, AppBar shows Nguyen Hoc Ba (Member) | **Pass** | | |
| TC-03 | REQ-01 Login | Non-existent email → rejected | Login failed — email + password not recognized | **Pass** | | |
| TC-04 | REQ-01 Login | Wrong password → "Incorrect password" | Shows "Incorrect password", page does not change | **Pass** | | |
| TC-05 | REQ-01 Login | Empty email/password → empty field message | Shows "Please enter email and password" | **Pass** | | |
| TC-06 | REQ-01 Login | MEM004 Suspended logs in successfully | Login OK — Le Can Cu (Member) | **Pass** | | |
| TC-07 | REQ-01 Login | MEM005 Expired logs in successfully | Login OK — Pham Trung Binh (Member) | **Pass** | | |
| TC-31 | REQ-01 Login | Only email empty → rejected with message | Shows "Please enter email and password". Page does not change. | **Pass** | | |
| TC-08 | REQ-02 View Books | Full list with all fields, BOOK001/003 correct status | 20 books, all fields present: Name/Author/Genre/Year/Status | **Pass** | | |
| TC-09 | REQ-02 View Books | After borrow → status updates immediately | BOOK001 changes to Borrowed immediately after borrow | **Pass** | | |
| TC-10 | REQ-03 Search | Search "Flutter" → BOOK005, BOOK013, BOOK019 | Correct books containing Flutter displayed | **Pass** | | |
| TC-11 | REQ-03 Search | Search "Nguyen" → books by author Nguyen | Results correct by author | **Pass** | | |
| TC-12 | REQ-03 Search | flutter = FLUTTER (case-insensitive) | Both searches return the same results: BOOK005, BOOK013, BOOK019 | **Pass** | | |
| TC-13 | REQ-03 Search | No results → message shown | Shows "No books found" | **Pass** | | |
| TC-32 | REQ-03 Search | Clear search → all 20 books restored | All 20 books reappear after clearing the search box | **Pass** | | |
| TC-14 | REQ-03 Filter | Filter is case-insensitive | `Technology` OK; `technology`/uppercase → "No books found" | **Fail** | | **BUG-02** |
| TC-37 | REQ-03 Filter | Search + filter: keyword not in category → no result | System ignores genre filter; shows results from all genres | **Fail** | | **BUG-06** |
| TC-15 | REQ-04 Borrow | Borrow BOOK002 for MEM002 — success | Borrow OK, record "Borrowed", due date +14 days | **Pass** | | |
| TC-16 | REQ-04 Borrow | BOOK003 already borrowed → rejected | Cannot borrow an already-borrowed book | **Pass** | | |
| TC-17 | REQ-04 Borrow | MEM004 Suspended → rejected with suspension message | Borrow rejected. Message cites suspension. | **Pass** | | |
| TC-18 | REQ-04 Borrow | MEM005 Expired → rejected with expiry message | Same "expired" message, not differentiated from suspended | **Fail** | | **BUG-03** |
| TC-19 | REQ-04 Borrow | 4th borrow → rejected (3-book limit) | 4th book allowed; limit not enforced correctly | **Fail** | | **BUG-01** |
| TC-20 | REQ-04 Borrow | BOOK007 Lost → rejected | Cannot borrow a lost book | **Pass** | | |
| TC-33 | REQ-04 Borrow | Borrow at BVA boundary (2nd active borrow) | Borrow succeeds; member now has 2 active borrows | **Pass** | | |
| TC-21 | REQ-05 Return | Return BOOK013 → "Returned", book Available | Return successful, statuses updated correctly | **Pass** | | |
| TC-22 | REQ-05 Return | Return unborrowed book → rejected | System rejects, BOOK008 remains Available | **Pass** | | |
| TC-38 | REQ-05 Return | Return overdue book → overdue warning shown | Return successful, overdue warning displayed. | **Pass** | | |
| TC-23 | REQ-06 Overdue | Check Overdue → BR001 marked Overdue | Run 1: 1 record updated; Run 2: 0 records; record due today not flagged | **Fail** | | **BUG-04** |
| TC-24 | REQ-06 Overdue | MEM002 sees own overdue record | BR001 shows Overdue in my records | **Pass** | | |
| TC-35 | REQ-06 Overdue | Librarian sees ALL overdue records across members | Librarian sees all overdue records for all members after Check Overdue | **Pass** | | |
| TC-25 | REQ-07 Members | Add valid member → success | New member not added; shows "Invalid email" | **Fail** | | **BUG-05** |
| TC-26 | REQ-07 Members | Email tay.tran@emailcom → rejected | New member created successfully (invalid email accepted) | **Fail** | | **BUG-05** |
| TC-27 | REQ-07 Members | Duplicate email → duplicate message | Only shows "Invalid email" | **Fail** | | **BUG-05** |
| TC-28 | REQ-07 Members | Member cannot add new member | No Members tab visible | **Pass** | | |
| TC-29 | REQ-08 Record Lookup | Librarian views MEM003 records | Shows record ID, book, dates, and status correctly | **Pass** | | |
| TC-30 | REQ-08 Record Lookup | Member only sees own records | Only MEM002 records visible, no other members' records | **Pass** | | |
| TC-36 | REQ-08 Record Lookup | Returned record shows correct status + all fields | Returned record shows "Returned" with Record ID, Book, Borrow Date, Due Date | **Pass** | | |
| TC-39 | REQ-08 Record Lookup | Member cannot view/return another member's records | MEM002 can search MEM006's ID and see records; Return button accessible | **Fail** | | **BUG-07** |

---

## Summary Results

| **Metric** | **Value** |
|---|---|
| **Total Test Cases** | 38 |
| **Pass** | 29 |
| **Fail** | 9 |
| **Blocked** | 0 |
| **Not Run** | 0 |
| **Pass Rate** | 76.3% |
| **Bugs Found** | 7 |

---

## Results by Feature Group

| **Group** | **REQ** | **Total TCs** | **Pass** | **Fail** | **Pass Rate** |
|---|---|---|---|---|---|
| Login | REQ-01 | 8 | 8 | 0 | 100% |
| View Book List | REQ-02 | 2 | 2 | 0 | 100% |
| Search & Filter | REQ-03 | 7 | 5 | 2 | 71.4% |
| Borrow Book | REQ-04 | 7 | 5 | 2 | 71.4% |
| Return Book | REQ-05 | 3 | 3 | 0 | 100% |
| Overdue Processing | REQ-06 | 3 | 2 | 1 | 66.7% |
| Member Management | REQ-07 | 4 | 1 | 3 | 25.0% |
| Borrow Record Lookup | REQ-08 | 4 | 3 | 1 | 75.0% |
| **Total** | | **38** | **29** | **9** | **76.3%** |
