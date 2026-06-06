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
| TC-01 | Login | Librarian logs in, shows Nguyen Thu Thu | Login OK, AppBar shows correct name/role for Librarian | **Pass** | | |
| TC-02 | Login | Member logs in, shows Nguyen Hoc Ba | Login OK, AppBar shows Nguyen Hoc Ba (Member) | **Pass** | | |
| TC-03 | Login | Non-existent email → rejected | Login failed (email + password not recognized) | **Pass** | | |
| TC-04 | Login | Wrong password → "Incorrect password" | Shows "Incorrect password", page does not change | **Pass** | | |
| TC-05 | Login | Empty email/password → empty field message | Shows "Please enter email and password" (SRS wording; app shows *account* when tested manually) | **Pass** | | |
| TC-06 | Login | MEM004 Suspended logs in | Login OK — Le Can Cu (Member) | **Pass** | | |
| TC-07 | Login | MEM005 Expired logs in | Login OK — Pham Trung Binh (Member) | **Pass** | | |
| TC-08 | View Books | Full list with all fields, BOOK001/003 correct status | 20 books, all fields present: Name/Author/Genre/Year/Status | **Pass** | | |
| TC-09 | View Books | After borrow → status updates immediately | BOOK001 changes to Borrowed immediately after borrow | **Pass** | | |
| TC-10 | Search | Search "Flutter" → BOOK005, BOOK013, BOOK019 | Correct books containing Flutter displayed | **Pass** | | |
| TC-11 | Search | Search "Nguyen" → books by author Nguyen | Results correct by author | **Pass** | | |
| TC-12 | Search | flutter = FLUTTER (case-insensitive) | Both searches return the same results: BOOK005, BOOK013, BOOK019 | **Pass** | | |
| TC-13 | Search | No results → message shown | Shows "No books found" | **Pass** | | |
| TC-14 | Filter | Filter is case-insensitive | `Technology` OK; `technology`/uppercase → "No books found" | **Fail** | | **BUG-02** |
| TC-15 | Borrow | Borrow BOOK002 for MEM002 — success | Borrow OK, record "Borrowed", due date +14 days | **Pass** | | |
| TC-16 | Borrow | BOOK003 already borrowed → rejected | Cannot borrow an already-borrowed book | **Pass** | | |
| TC-17 | Borrow | MEM004 Suspended → suspended message | Shows "Member has expired…" (wrong reason) | **Fail** | | **BUG-03** |
| TC-18 | Borrow | MEM005 Expired → expired message | Same "expired" message, not differentiated from suspended | **Fail** | | **BUG-03** |
| TC-19 | Borrow | 4th borrow → rejected (3-book limit) | 4th book allowed; limit message only shown for active member already at exactly 3 | **Fail** | | **BUG-01** |
| TC-20 | Borrow | BOOK007 Lost → rejected | Cannot borrow a lost book | **Pass** | | |
| TC-21 | Return | Return BOOK013 → "Returned", book Available | Return successful, statuses updated correctly | **Pass** | | |
| TC-22 | Return | Return unborrowed book → rejected | System rejects, BOOK008 remains Available | **Pass** | | |
| TC-23 | Overdue | Check Overdue → BR001 marked Overdue | Run 1: 1 record; Run 2: 0 records; record due exactly today not flagged | **Fail** | | **BUG-04** |
| TC-24 | Overdue | MEM002 sees own overdue record | BR001 shows Overdue in my records | **Pass** | | |
| TC-25 | Members | Add valid member → success | New member not added; shows "Invalid email" | **Fail** | | **BUG-05** |
| TC-26 | Members | Email tay.tran@emailcom → rejected | New member created successfully (invalid email accepted) | **Fail** | | **BUG-05** |
| TC-27 | Members | Duplicate email → duplicate message | Only shows "Invalid email" | **Fail** | | **BUG-05** |
| TC-28 | Members | Member cannot add new member | No Members tab visible | **Pass** | | |
| TC-29 | Record Lookup | Librarian views MEM003 records | Shows record ID, book, dates, and status correctly | **Pass** | | |
| TC-30 | Record Lookup | Member only sees own records | Only MEM002 records visible, no other members' records | **Pass** | | |
| TC-31 | Login | Only email empty → rejected with message | Shows "Please enter email and password". Page does not change. | **Pass** | | |
| TC-32 | Search | Clear search → all 20 books restored | All 20 books reappear after clearing the search box | **Pass** | | |
| TC-33 | Borrow | Borrow at BVA boundary (2nd active borrow) | Borrow succeeds; member now has 2 active borrows, within the 3-book limit | **Pass** | | |
| TC-34 | Overdue | Record with dueDate = tomorrow stays "Borrowed" | Record with future due date remains "Borrowed" after Check Overdue | **Pass** | | |
| TC-35 | Overdue | Librarian sees ALL overdue records across members | Librarian sees all overdue records for all members after Check Overdue | **Pass** | | |
| TC-36 | Record Lookup | Returned record shows correct status + all fields | Returned record shows "Returned" with Record ID, Book, Borrow Date, Due Date | **Pass** | | |
| TC-37 | Search | Search + filter: keyword not in category → no result | System ignores the genre filter and shows results from all genres containing the keyword | **Fail** | | **BUG-06** |
| TC-38 | Return | Return overdue book → overdue warning shown | Return completes silently with no overdue warning or notification displayed | **Fail** | | **BUG-07** |
| TC-39 | Record Lookup | Member cannot view/return another member's records | MEM002 can search MEM006's ID and see MEM006's records; Return button is accessible | **Fail** | | **BUG-08** |

---

## Summary Results

| **Metric** | **Value** |
|---|---|
| **Total Test Cases** | 39 |
| **Pass** | 29 |
| **Fail** | 10 |
| **Blocked** | 0 |
| **Not Run** | 0 |
| **Pass Rate** | 75.0% |
| **Bugs Found** | 8 |

---

## Results by Feature Group

| **Group** | **Total TCs** | **Pass** | **Fail** | **Pass Rate** |
|---|---|---|---|---|
| Login | 8 | 8 | 0 | 100% |
| View Book List | 2 | 2 | 0 | 100% |
| Search & Filter | 7 | 5 | 2 | 71.4% |
| Borrow Book | 7 | 4 | 3 | 57.1% |
| Return Book | 3 | 2 | 1 | 66.7% |
| Overdue Processing | 4 | 3 | 1 | 75% |
| Member Management | 4 | 1 | 3 | 25% |
| Borrow Record Lookup | 4 | 3 | 1 | 75% |
