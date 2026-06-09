# Test Summary Report

> **Basis:** test_case.docx, test_execution.docx, bug_report.docx (20/05/2026) — system under test: **https://stqa.rbc.vn**

---

## 1. General Information

| **Item** | **Details** |
|---|---|
| **Group** | STQA Group 22 |
| **Report Date** | 20/05/2026 |
| **Environment** | Chrome (latest version), Windows 11 |
| **Reference** | SRS v1.0 |

---

## 2. Overall Execution Results

| **Metric** | **Value** |
|---|---|
| Total test cases | **38** |
| Pass | **29** |
| Fail | **9** |
| Blocked | **0** |
| Not Run | **0** |
| **Pass Rate** | **76.3%** |
| **Bugs Recorded** | **7** |

### 2.1. Results by Requirement

| **Feature Group** | **Total TCs** | **Pass** | **Fail** | **Pass Rate** | **Related Bugs** |
|---|---|---|---|---|---|
| REQ-01 Login | 8 | 8 | 0 | 100% | — |
| REQ-02 View Book List | 2 | 2 | 0 | 100% | — |
| REQ-03 Search & Filter | 7 | 5 | 2 | 71.4% | BUG-02, BUG-06 |
| REQ-04 Borrow Book | 7 | 5 | 2 | 71.4% | BUG-01, BUG-03 |
| REQ-05 Return Book | 3 | 3 | 0 | 100% | — |
| REQ-06 Overdue Processing | 3 | 2 | 1 | 66.7% | BUG-04 (TC-23) |
| REQ-07 Member Management | 4 | 1 | 3 | 25% | BUG-05 (email) |
| REQ-08 Borrow Record Lookup | 4 | 3 | 1 | 75% | BUG-07 |

### 2.2. Failed TC → Bug Mapping

| **Failed TC** | **Actual Behavior (summary)** | **Bug** |
|---|---|---|
| TC-14 | Genre filter is case-sensitive (lowercase/uppercase inputs return no results) | BUG-02 |
| TC-18 | **Expired** member receives incorrect "suspended" error message | BUG-03 |
| TC-19 | System allows borrowing a **4th book** (exceeds 3-book limit) | BUG-01 |
| TC-23 | Overdue check is unstable + boundary date error | BUG-04 |
| TC-25 | Valid email is incorrectly rejected as "Invalid email" | BUG-05 |
| TC-26 | Invalid email (tay.tran@emailcom) is still accepted for new member creation | BUG-05 |
| TC-27 | Duplicate email only shows generic "Invalid email" message | BUG-05 |
| TC-37 | Combined search + filter ignores genre filter when keyword matches outside the genre | BUG-06 |
| TC-39 | Member MEM002 can view and return MEM006's borrow records — unauthorized access | BUG-07 |

### 2.3. Bug Distribution by Severity

| **Severity** | **Count** | **Bug IDs** |
|---|---|---|
| Critical | 1 | BUG-07 |
| High | 3 | BUG-01, BUG-04, BUG-05 |
| Medium | 3 | BUG-02, BUG-03, BUG-06 |
| Low | 0 | — |

---

## 3. Test Design Techniques Used

| **Technique** | **Applied To** | **Brief Description** |
|---|---|---|
| **EP** (Equivalence Partitioning) | REQ-01 → REQ-08 | Divides valid/invalid classes for email, book status, role permissions, duplicate data |
| **BVA** (Boundary Value Analysis) | REQ-01, REQ-04, REQ-06, REQ-07 | 3-book boundary, overdue date comparison, email missing the . |
| **Decision Table** | REQ-04 | Combination of book status × member status × borrow limit |

---

## 4. Product Quality Assessment

| **Category** | **Details** |
|---|---|
| **Stable Areas** | - Login flow works reliably; role display is correct (REQ-01).<br>- Book list view and keyword/author search meet expectations (REQ-02, most of REQ-03).<br>- Book return works correctly for both valid and rejected cases (REQ-05). |
| **Key Risks** | - **Access control breach (Critical):** A Member can view and return borrow records of other members, violating data privacy and integrity (REQ-08 — BUG-07).<br>- **Borrow flow has critical defects:** exceeds the 3-book limit (BUG-01); expired member error message is incorrect (BUG-03).<br>- **Overdue processing is unreliable:** boundary date error + inconsistent update results across runs (REQ-06 — BUG-04).<br>- **Member management validation is inconsistent:** accepts invalid email formats and error messages do not reflect the actual cause (REQ-07 — BUG-05).<br>- **Search + filter combination is broken:** genre filter is silently ignored when keyword matches books outside the selected category (REQ-03 — BUG-06). |

---

## 5. Recommended Fix Priority

| **Priority** | **Bug** | **Reason** |
|---|---|---|
| P1 | BUG-07 | Critical access control breach — member can view and act on other members' private data |
| P1 | BUG-01 | Violates core business constraint (max 3 books per member) |
| P1 | BUG-05 | Risk of corrupt member data + blocks valid data entry / makes deduplication difficult |
| P1 | BUG-04 | Directly impacts overdue business logic, reminders, and fines |
| P2 | BUG-03 | Causes misunderstanding of rejection reason, impacts operational support |
| P2 | BUG-06 | Filter silently ignored; misleading search results for users |
| P3 | BUG-02 | Inconsistent UX between filter and search (correct input but wrong case yields no results) |

---

## 6. Release Conclusion

The system **should not be released** in its current state. Although the overall pass rate is **76.3%**, a **Critical** access control breach (BUG-07) allows any authenticated member to view and return other members' borrow records — a fundamental security failure. Additionally, high-severity bugs are concentrated in critical business functions (REQ-04, REQ-06, REQ-07), further reducing operational reliability.

After fixing P1 bugs, a minimum regression run is required for:

- REQ-03: TC-37 (search + filter combo)
- REQ-04: TC-19 (borrow limit)
- REQ-06: TC-23 (check overdue)
- REQ-07: TC-25 → TC-27 (member validation)
- REQ-08: TC-39 (cross-member access control)

---

## 7. Lessons Learned (Optional)

- **IDM and thorough SRS reading before writing TCs are mandatory, not optional.** The IDM table for REQ-04 was what led the group to separate Suspended / Expired into distinct partitions, producing TC-17, TC-18, and TC-19. Without reading the SRS carefully, TC-19 could have been written with the wrong expected result (unaware of the 3-book limit from BR-01), and BUG-05 would have gone undetected entirely (unaware that email must contain a `.` in the domain, per BR-08).

- **BVA catches bugs that EP cannot.** BUG-01 (off-by-one: `>` instead of `>=` when comparing borrowed book count) and BUG-04 (a record due exactly today is not marked Overdue) are classic boundary errors — EP only tests values inside a partition and would have missed both entirely.

- **Decision Table forces combinatorial thinking, not sequential.** When building the REQ-04 table (book status × member status × borrow limit), the group recognized the need to distinguish *why* a borrow is rejected, not just *whether* it is — leading to separate TC-17 and TC-18, which exposed BUG-03: the system uses an incorrect message for Expired members, confusing it with suspension.

- **Each TC should test exactly one condition; each bug should document each symptom separately.** BUG-05 affects TC-25/26/27 in three contradictory directions (valid email rejected, malformed email accepted, duplicate email misreported as invalid format). Merging them into one TC or one bug would hide the true complexity and make it harder for the developer to fix the right root cause.

- **Severity should reflect business impact, not how visibly or frequently a bug appears.** BUG-03 (wrong error message) shows up on every test run but is only Medium because no data is corrupted. BUG-01 (borrow limit bypass) requires a specific setup to reproduce but is High because it breaks a core business constraint and can cause inventory loss.

- **Suggesting a specific fix down to the operator level helps developers resolve bugs faster.** Stating "change `>` to `>=`" (BUG-01, BUG-04) or "split into `DUPLICATE_EMAIL` vs `INVALID_EMAIL_FORMAT`" (BUG-05) is far more actionable than describing symptoms alone — especially when the team works asynchronously and cannot easily follow up with the tester in real time.

---

## 8. AI Assistance Declaration

| **Tool** | **Scope of Use** | **Verification Method** |
|---|---|---|
| | | |

---

*Test Summary Report — 20/05/2026*
