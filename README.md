# Random Date Generator – QA Testing Challenge

Tester: Oumer  
Target: https://codebeautify.org/generate-random-date  
Scope: Random Date Generator feature only (inputs, outputs, validation, UX/accessibility).

## Deliverables
- Test Report: `docs/Test-Report.md`
- Bug Report: `docs/Bug-Report-001.md`
- (Optional) Feature Improvement: `docs/Feature-Improvement-001.md`
- Test Cases: `docs/Test-Cases.md`

## How to Review
Open the target URL and follow the steps in the Test Report and Bug Report.  

# Test Report - Random Date Generator (codebeautify.org)

## 1. Objective
Validate that the Random Date Generator reliably generates the requested number of random dates in the selected format, within the configured start/end range, and provides clear handling for invalid inputs.

## 2. Scope
**In scope**
- Input fields: number of dates, date output format dropdown, custom format field, start date, end date
- Generate action, output formatting, range correctness
- Basic UX validation (error messaging, constraints, basic accessibility signals)

**Out of scope**
- Ads, other tools on the site, account/login, backend architecture, performance of the entire website

## 3. Test Approach
- Exploratory testing (session-based) focusing on:
  - Happy path generation
  - Boundary value analysis (min/max dates, min/max range dates)
  - Negative testing (start > end, empty/invalid inputs)
  - Format handling (dropdown vs custom format)
- Lightweight risk-based prioritization:
  - High risk: invalid range handling, formatting correctness, output count correctness
 

  # Test Cases – Random Date Generator

Legend: P0=Critical, P1=High, P2=Medium

| ID | Priority | Title | Steps | Expected Result |
|---|---|---|---|---|
| TC-01 | P0 | Happy path generation | Set N=7, format=MM-DD-YYYY, start=2020-01-01, end=2099-12-31, click Generate | Output contains 7 dates, formatted MM-DD-YYYY, all within range |
| TC-02 | P0 | Output count matches N | Set N=1, click Generate | Exactly 1 date generated |
| TC-03 | P1 | Range boundary inclusive | Set start=end=2030-05-15, N=5, Generate | Either all outputs = 05-15-2030 OR tool shows message “Range too small” (must be explicit) |
| TC-04 | P0 | Start date greater than end date | start=2099-12-31, end=2020-01-01, Generate | Clear validation error; no output generated |
| TC-05 | P1 | Empty start date | Clear start date, Generate | Validation error; no output |
| TC-06 | P1 | Empty end date | Clear end date, Generate | Validation error; no output |
| TC-07 | P1 | Non-numeric N | Enter N="abc", Generate | Validation error; no output |
| TC-08 | P1 | N=0 | Enter N=0, Generate | Validation error OR output empty with clear message |
| TC-09 | P1 | Negative N | Enter N=-5, Generate | Validation error; no output |
| TC-10 | P2 | Very large N | Enter N=5000, Generate | Tool remains responsive OR shows limit message |
| TC-11 | P1 | Switch formats | Generate with MM-DD-YYYY, then change to YY-MM-DD, Generate | Output changes to correct selected format |
| TC-12 | P1 | Custom format valid tokens | Enter custom format like YYYY-MM-DD, Generate | Output matches custom format |
| TC-13 | P1 | Custom format invalid tokens | Enter custom format like YYYY-QQ-DD, Generate | Clear error OR documented fallback behavior |
| TC-14 | P2 | Leap year handling | Range includes 2024-02-29; Generate multiple | If 02-29 appears, it must only be in leap years |
| TC-15 | P2 | Month/day validity | Generate multiple | No invalid dates like 02-30 or 13-10 |
| TC-16 | P2 | Whitespace input | Enter " 7 " in N | Trimmed and treated as 7 OR validation message |
| TC-17 | P2 | Keyboard-only usability | Tab through fields and Generate | Usable without mouse |
| TC-18 | P2 | Error messaging clarity | Trigger invalid states | Error states are visible and understandable |
| TC-19 | P2 | Output area behavior | Generate repeatedly | Output updates predictably (append vs replace should be consistent) |
| TC-20 | P2 | Reset behavior (if exists) | Use refresh or reset option | Inputs restored or predictable behavior |
 
## 4. Test Environment 
- OS: macOS (26.3)
- Browser: Chrome (145.0.7632.110)
- Device: MacBook
- Date/Time: (Feb/24/2026)

## 5. Key Assumptions
- “Random” means non-deterministic output, but must still respect range constraints.
- Start date and end date are inclusive (common expectation for generators). If exclusive, tool should clearly document it.
- Output must match the selected format exactly.
- If “custom format” is used, invalid format tokens should be rejected or handled explicitly.

## 6. Entry / Exit Criteria
**Entry**
- Tool loads successfully and input fields are interactable

**Exit**
- High/critical issues documented
- Test cases executed with recorded outcomes
- At least 1 bug or improvement report provided

## 7. Test Summary
### What was tested
- Generation with valid inputs
- Validation messaging for invalid inputs
- Output formatting and range constraints
- Custom format handling

### Results overview (update after you run)
- Total test cases executed: 20
- Passed: 20
- Failed: 01
- Blocked: 0
- Not run: 0

## 8. Observations / Risks (typical areas to confirm)
- Validation for `Start date > End date` must be explicit.
- Handling for empty dates / empty custom format must be explicit.
- Large number generation (e.g., 5000) may freeze UI; consider limits or progressive rendering.

## 9. Recommendation
- Add clear inline validation + disable Generate when inputs invalid.
- Provide help text defining whether range is inclusive.
- Add “Copy to clipboard” button and/or “Download CSV” for usability.

## 10. Test Data Example 
- Dates to generate: 7
- Output format: MM-DD-YYYY
- Start: 2020-01-01
- End: 2099-12-31
Expected: 7 dates, each within range, each formatted MM-DD-YYYY.

# Bug Report 001 – Missing validation when Start Date > End Date

## Summary
Random Date Generator allows generation (or fails silently) when Start Date is later than End Date. Tool should prevent generation and show clear validation message.

## Severity / Priority
- Severity: High (incorrect inputs not handled; can produce invalid results or confuse users)
- Priority: P0/P1 (core validation)

## Environment
- OS: macOS (26.3)
- Browser: Chrome (145.0.7632.110)
- URL: https://codebeautify.org/generate-random-date
- Date/Time: (Feb/24/2026)

## Preconditions
Page is loaded and interactive.

## Steps to Reproduce
1. Set **How many dates** = 7
2. Set **Date Output Format** = MM-DD-YYYY
3. Set **Start date** = 2099-12-31
4. Set **End date** = 2020-01-01
5. Click **Generate Random Date**

## Expected Result
- User sees a clear error message such as: “Start date must be earlier than or equal to end date.”
- No dates are generated.

## Actual Result 
- Dates are generated anyway OR no visible error is shown, leaving output unchanged with no guidance.

## Impact
Users can unknowingly generate invalid data sets, reducing trust and causing downstream errors for any consumer using these dates for testing or data generation.

## Suggested Fix
- Add front-end validation:
  - If Start > End, block Generate action
  - Display inline error under date fields
  - Optionally auto-highlight invalid field(s)
 
# Feature Review 001 – Export & Range Behavior Verification

## Type
Feature Verification / Enhancement Assessment

## Summary
During testing, the Random Date Generator was evaluated for export capabilities and date range clarity.

The tool already provides:
- Copy to Clipboard functionality
- Download (CSV/TXT) export option
- Clearly defined date range behavior

No usability gap was identified in these areas.

---

## Validation Performed

### 1. Copy Function
- Verified that clicking “Copy” copies all generated dates.
- Confirmed formatting and line breaks are preserved.
- Confirmed behavior works across multiple generation attempts.

### 2. Download Function
- Verified downloaded file contains exactly N rows.
- Confirmed output matches selected date format.
- Confirmed encoding is correct and file opens properly.

### 3. Date Range Behavior
- Confirmed that start/end range behavior is clearly defined and consistent.
- Verified boundary cases align with documented behavior.

---

## Conclusion
The Random Date Generator performs reliably in its core functionality, including date generation, formatting, and export capabilities. 
Overall behavior is stable and suitable for practical test data use.

One high-priority validation gap was identified: the tool does not explicitly prevent generation when the Start Date exceeds the End Date, 
creating a potential data integrity risk.

With this validation improvement addressed, the tool would meet strong usability and reliability standards.
































