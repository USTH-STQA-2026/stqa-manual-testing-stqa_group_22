# Bug Reports

> **Instructions:** Create one bug entry for each TC with a **Fail** result.
> Each bug needs: a title describing the faulty behavior, reproduction steps, expected vs actual results, severity + explanation.

| **Info** | |
|---|---|
| **Group** | STQA Group 22 |
| **Report Date** | 20/05/2026 |
| **Source** | Test execution (20/05/2026) — https://stqa.rbc.vn |

---

## BUG-01

| **Attribute** | **Details** |
|---|---|
| **Bug ID** | BUG-02 |
| **Related TC** | TC-19 |
| **Related REQ** | REQ-04, BR-01 |
| **Severity** | **High** — Allows borrowing beyond the business limit (3 books), easily causing inventory loss/mismanagement |
| **Detected By** | STQA Group 22 |
| **Detection Date** | 20/05/2026 |
| **Status** | Open |

**Title:** Member can borrow **4 books simultaneously** instead of the maximum of 3 per SRS

**Environment:**
- Browser: Chrome (latest version)
- OS: Windows 11
- UI Language: Vietnamese
- App: https://stqa.rbc.vn

**Preconditions:**
- Logged in as Member or Librarian with borrow permission.
- Member has **Active** status and is currently borrowing **fewer than 3** books (or reset data before testing).
- Sufficient books with **"Available"** status exist for consecutive borrowing.

**Reproduction Steps:**
1. In **Books** or **Borrow/Return** tab, borrow books 1, 2, and 3 consecutively for the same member — all succeed.
2. Borrow a **4th book** (still "Available") for the same member.
3. Look up the member's borrow records and count records with status **"Borrowed"**.

**Expected Result:** Per **REQ-04 / BR-01**: maximum **3 books per member** at any time. The 4th borrow attempt must be **rejected** with a message like *"Maximum borrow limit reached (3 books)"* (or equivalent). No 4th borrow record should be created.

**Actual Result:** The system **allows the 4th book** to be borrowed successfully. The member ends up with **4 active borrow records**. The 3-book limit message only appears in certain other scenarios (e.g., an active member who already has exactly 3 books), and does not correctly block the 4th borrow as described in TC-19.

**Impact:** Breaks the core borrow limit rule. Likely a boundary operator error (> instead of >= when comparing the number of currently borrowed books), leading to over-allocation of library resources.

**Evidence:** *(attach screenshots)*

**Suggested Fix:** Change the limit check condition to `borrowed_count >= 3` before rejecting additional borrows; ensure all borrow flows (Books tab, Borrow/Return tab) share the same logic.

---

## BUG-02

| **Attribute** | **Details** |
|---|---|
| **Bug ID** | BUG-03 |
| **Related TC** | TC-14 |
| **Related REQ** | REQ-03 |
| **Severity** | **Medium** — Genre filter is inconsistent with keyword search; users entering the correct genre name in the wrong case see no results |
| **Detected By** | STQA Group 22 |
| **Detection Date** | 20/05/2026 |
| **Status** | Open |

**Title:** Book genre filter is **case-sensitive**, contradicting the case-insensitive search rule

**Environment:**
- Browser: Chrome (latest version)
- OS: Windows 11
- UI Language: Vietnamese
- App: https://stqa.rbc.vn

**Preconditions:**
- Logged in (Librarian or Member).
- **Books** tab is open; seed data includes books in the **"Technology"** genre.

**Reproduction Steps:**
1. In **Books** tab, filter by genre using the value `Technology` → observe results.
2. Filter again using `technology` (all lowercase) → observe results.
3. Filter again using all-uppercase (e.g., `TECHNOLOGY`) → observe results.
4. (For reference) Type `flutter` / `FLUTTER` in the **search box** — the same books appear (TC-12 Passes).

**Expected Result:** Per **REQ-03**: search/filter must be **case-insensitive**. All three inputs above must return the **same set** of Technology books. If no books found → message **"No books found"**.

**Actual Result:**
- `Technology`: **success**, shows Technology books.
- `technology` or all-uppercase: **"No books found"** / empty list even though the genre exists in DB.
- The keyword search by name/author is still **case-insensitive** (Pass).

**Impact:** Inconsistent user experience; users may wrongly conclude the library has no books in that genre.

**Evidence:** *(attach screenshots)*

**Suggested Fix:** Normalize the genre comparison using `toLowerCase()` / case-insensitive `localeCompare`, in sync with the name/author search logic.

---

## BUG-03

| **Attribute** | **Details** |
|---|---|
| **Bug ID** | BUG-04 |
| **Related TC** | TC-17, TC-18 |
| **Related REQ** | REQ-04 |
| **Severity** | **Medium** — Incorrect and misleading error messages for member status; impacts operations and user support |
| **Detected By** | STQA Group 22 |
| **Detection Date** | 20/05/2026 |
| **Status** | Open |

**Title:** Borrow rejection displays the **same "expired" message** for both **Suspended** and **Expired** members

**Environment:**
- Browser: Chrome (latest version)
- OS: Windows 11
- UI Language: Vietnamese
- App: https://stqa.rbc.vn

**Preconditions:**
- Logged in as Librarian: librarian@library.com / admin123.
- MEM004 (cu.le@email.com) — status **Suspended**.
- MEM005 (binh.pham@email.com) — status **Expired**.
- At least one book with **"Available"** status exists.

**Reproduction Steps:**
1. Go to **Borrow/Return** tab.
2. Try to borrow an "Available" book for **MEM004** (Suspended). Note the error message.
3. Try to borrow a different "Available" book for **MEM005** (Expired). Note the error message.

**Expected Result:** Per **REQ-04**:
- MEM004 (Suspended): rejected with a message describing **suspension** (e.g., *"Member is currently suspended. Cannot borrow books."*)
- MEM005 (Expired): rejected with a message describing **expiry** (e.g., *"Member membership has expired. Cannot borrow books."*)
- The two messages must be **different**, accurately reflecting `member.status`.

**Actual Result:**
- MEM004 (Suspended) receives a message like **"Member has expired…"** — **wrong reason** (should say suspended).
- MEM005 (Expired) receives the **same "expired" message**, indistinguishable from the suspended case.
- Neither message accurately reflects the two distinct business statuses as specified in the SRS.

**Impact:** Librarians and members misunderstand the rejection reason; difficult to handle complaints correctly (renewal vs. lifting a suspension).

**Evidence:**
- TC-17: *(attach screenshot)*
- TC-18: *(attach screenshot)*

**Suggested Fix:** Separate the `if (status == suspended)` and `if (status == expired)` branches with distinct messages; do not share a single "expired" template.

---

## BUG-04

| **Attribute** | **Details** |
|---|---|
| **Bug ID** | BUG-06 |
| **Related TC** | TC-23 |
| **Related REQ** | REQ-06 |
| **Severity** | **High** — Boundary date logic error causes records due **exactly today** to not be marked overdue; running the check a second time reports 0 records despite overdue records still existing |
| **Detected By** | STQA Group 22 |
| **Detection Date** | 20/05/2026 |
| **Status** | Open |

**Title:** **Check Overdue** does not flag records with dueDate **equal to today**; second run reports **0 records** after first run updated **1 record**

**Environment:**
- Browser: Chrome (latest version)
- OS: Windows 11
- UI Language: Vietnamese
- App: https://stqa.rbc.vn

**Preconditions:**
- Logged in as Librarian.
- Seed data includes an overdue record (e.g., BR001, due date ≤ today).
- Data is **not reset** between two clicks of **Check Overdue**.

**Reproduction Steps:**
1. In **Borrow/Return** tab, locate a record with due date **≤ today** and status **"Borrowed"**.
2. Click **Check Overdue** — **run 1**. Note the message (e.g., *"Updated: 1 overdue borrow record"*).
3. **Do not reset** data. Click **Check Overdue** — **run 2**. Note the message.
4. Check whether a record with dueDate **exactly equal** to today's date was assigned **"Overdue"** status.

**Expected Result:** Per **REQ-06**: all records with dueDate ≤ today (including **exactly on the due date**) → **"Overdue"**. BR001 and similar records are updated. Subsequent runs are **consistent** with previously assigned statuses (no incorrect count if no new records remain).

**Actual Result:**
- Run 1: updates **1** overdue record.
- Run 2: reports **"Updated: 0 overdue borrow records"** — difficult to reconcile if records still need processing or the boundary date logic is wrong.
- Records with due date **exactly equal to today** are **not** marked **"Overdue"** (possible off-by-one: using `>` instead of `>=`).
- TC-24 (member views own overdue record) still **passes** after run 1.

**Impact:** Missed overdue records on the due date; overdue reports are unreliable; affects fines and return reminders.

**Evidence:** *(attach screenshots)*

**Suggested Fix:** Change comparison to `today >= dueDate` (or `!dueDate.isAfter(today)`). Ensure the job is idempotent: run 2 should not clear previously set overdue statuses and should accurately report remaining records to process.

---

## BUG-05

| **Attribute** | **Details** |
|---|---|
| **Bug ID** | BUG-07 |
| **Related TC** | TC-25, TC-26, TC-27 |
| **Related REQ** | REQ-07, BR-08 |
| **Severity** | **High** — Inconsistent email validation: rejects valid data, accepts malformed emails, and uses misleading error messages for duplicates |
| **Detected By** | STQA Group 22 |
| **Detection Date** | 20/05/2026 |
| **Status** | Open |

**Title:** **Add Member** form handles email incorrectly per SRS: rejects valid email, accepts email missing `.`, and reports duplicates as *"Invalid email"*

**Environment:**
- Browser: Chrome (latest version)
- OS: Windows 11
- UI Language: Vietnamese
- App: https://stqa.rbc.vn

**Preconditions:**
- Logged in as Librarian: librarian@library.com / admin123.
- **Members** tab — only the Librarian sees **Add Member** (TC-28 Pass: members have no permission).

**Reproduction Steps:**

*Part A — TC-25: Add valid member*
1. Go to **Members** tab → click **Add Member**.
2. Enter: Name: Tran Thi Tay, Email: tay.tran@email.com, Phone: 0363636361 (per TC-25).
3. Click **Save**.

*Part B — TC-26: Invalid email format*
4. Click **Add Member** again.
5. Enter email missing the `.` in domain: `tay.tran@emailcom`.
6. Click **Save**.

*Part C — TC-27: Duplicate email*
7. Click **Add Member** again.
8. Enter an already-existing email: `ba.nguyen@email.com`.
9. Click **Save**.

**Expected Result:** Per **REQ-07 / BR-08**:
- **TC-25**: Created successfully; new member appears with **Active** status.
- **TC-26**: Rejected; message shows **invalid email**; no record created.
- **TC-27**: Rejected; message shows **email already exists / duplicate** (not confused with format error).

**Actual Result:**
- **TC-25**: Member is **not created**; shows **"Invalid email"** even though `tay.tran@email.com` is correctly formatted per SRS.
- **TC-26**: Member is **created successfully** with `tay.tran@emailcom` (missing `.` after domain) — validation rule violated.
- **TC-27**: Only shows **"Invalid email"**, does **not** indicate that the email is a **duplicate**.

**Impact:** Data entry for new members is unreliable: valid data is blocked, invalid data enters the system, and Librarians cannot distinguish input format errors from duplicate email errors.

**Evidence:**
- TC-25: *(attach screenshot)*
- TC-26: *(attach screenshot)*
- TC-27: *(attach screenshot)*

**Suggested Fix:**
- Validate email against the SRS regex (`local@domain.tld`, must include `.` in the domain part).
- Separate error codes and messages: `DUPLICATE_EMAIL` vs `INVALID_EMAIL_FORMAT`.
- Ensure TC-25 / TC-26 / TC-27 all pass after the fix (happy path + reject invalid + reject duplicate).
