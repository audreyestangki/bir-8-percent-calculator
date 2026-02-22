# MVP Product Requirements Document (PRD)
**Product Name:** VibeTax – 8% Tax Calculator for Filipino Freelancers (MVP)
**Version:** 2.0
**Last Updated:** 2026-02-22

---

## 1. What is the App?

### 1.1 Product Definition

VibeTax is an interactive, web-based tax calculator designed for Filipino freelancers and self-employed individuals who want a fast and clear way to estimate their taxes under the 8% income tax option.

The app allows users to:
- Enter their monthly gross income (January–December)
- Instantly determine eligibility for the 8% tax rate
- View quarterly and annual tax computations
- See tax filing deadlines relevant to their computed results

This is an MVP-focused app built for speed, clarity, and correctness — not for full tax filing or compliance automation.

---

## 2. Who is the App For?

### Primary Target Users
- Filipino freelancers (remote workers, creatives, consultants)
- Self-employed professionals who are non-VAT registered and opted into the 8% tax rate
- First-time taxpayers confused about quarterly vs annual tax

### User Characteristics
- Non-accountants
- Income varies monthly
- Wants "Am I eligible?" and "How much do I pay?" answers fast

---

## 3. How Do We Use the App?

### 3.1 Core User Flow

1. User opens the web app
2. User sees a **"What is the 8% option?"** more info link at the top (lightweight tooltip or modal — not a full education section)
3. User sees 12 monthly income input fields (Jan–Dec)
4. User enters gross income per month as **whole numbers only** (no decimals, no comma formatting)
5. User clicks **Compute**
6. The system:
   - Aggregates monthly income into quarterly and annual totals
   - Checks eligibility for the 8% tax option
   - Based on eligibility:
     - ❌ If not eligible → user sees a clear eligibility message
     - ✅ If eligible → user sees tax results
7. User reviews:
   - Total annual income
   - Quarterly tax computation
   - Annual tax computation
   - Relevant tax filing deadlines

---

## 4. Eligibility & Tax Logic (8% Only)

### 4.1 Eligibility Rules

A user is eligible for the 8% income tax option if:
- Total annual gross income ≤ ₱3,000,000

If annual income exceeds ₱3,000,000:
- The app flags the user as **not eligible**
- No tax computation is shown
- A clear explanation is displayed

### 4.2 Assumptions (Baked Into Logic)

- The ₱250,000 deduction is applied **once per year** and is **not shared with any compensation income**
- The app assumes the user is a **pure self-employment income earner** with no mixed income
- No prior quarterly tax payments are considered — all computations are based on inputs provided in the current session

### 4.3 Tax Computation Formula

**Annual Tax Formula**
```
Taxable Income = max(0, Annual Gross Income – ₱250,000)
Annual Tax Due  = Taxable Income × 8%
```

**Rules:**
- If annual income ≤ ₱250,000 → tax due is ₱0
- The deduction line is **hidden** from results when it does not apply (i.e., annual income ≤ ₱250,000)

### 4.4 Quarterly Tax Computation (Cumulative)

Quarterly income is auto-derived from monthly inputs:
- **Q1:** Jan–Mar
- **Q2:** Apr–Jun
- **Q3:** Jul–Sep
- **Q4:** Oct–Dec

For each quarter:
```
Cumulative Gross Income  = Sum of income from January to end of quarter
Taxable Amount           = max(0, Cumulative Gross – ₱250,000)
Cumulative Tax Due       = Taxable Amount × 8%
Quarterly Tax Payable    = Cumulative Tax Due – Taxes Paid in Previous Quarters
```

- Q4 must reconcile **exactly** with the annual tax due
- Quarterly tax payable will always be ≥ ₱0 due to the cumulative approach

---

## 5. What Does the App Show?

### 5.1 If User is NOT Eligible

- Total annual income
- Eligibility status: ❌ Not eligible for 8% tax rate
- Explanation in plain language
- Suggestion to consult an accountant

### 5.2 If User IS Eligible

**Summary Section**
- Total annual gross income
- Eligibility status: ✅ Eligible for 8% tax rate

**Quarterly Breakdown**

For each quarter (Q1–Q4):
| Field | Description |
|---|---|
| Quarterly Gross Income | Sum of monthly income for that quarter |
| Cumulative Gross Income | Sum of all income from January to end of that quarter |
| Quarterly Tax Payable | Tax due for that quarter only (cumulative tax minus previous quarters) |

**Annual Breakdown**
- Annual gross income
- ₱250,000 deduction *(hidden if annual income ≤ ₱250,000)*
- Taxable income *(hidden if annual income ≤ ₱250,000)*
- Annual tax due

**All monetary values** are displayed with the peso sign and comma separators:
> Format: ₱1,234,567.89 → rounded to 2 decimal places

### 5.3 Tax Filing Deadlines (Static — Informational Only)

Deadlines are displayed statically. They are **not** highlighted as past or upcoming based on today's date.

| Quarter | Deadline |
|---|---|
| Q1 | On or before May 15 |
| Q2 | On or before August 15 |
| Q3 | On or before November 15 |
| Annual (Q4 Reconciliation) | On or before April 15, [following year]* |

> *The annual deadline dynamically displays the **actual following year** based on the current calendar year (e.g., if the current year is 2026, it shows "April 15, 2027").

---

## 6. Interaction & UX Patterns

### 6.1 Design Patterns

- **Single-action compute** (no step-by-step wizard)
- **Progressive disclosure:**
  - Eligibility status shown first
  - Tax results shown second
- **Explain-then-show** pattern for trust
- **Read-only results** (no accidental edits)
- **Results auto-clear:** If the user edits any input field after computing, all results immediately hide and the user must click Compute again

### 6.2 "What is the 8% Option?" Entry Point

- A lightweight **more info link** (e.g., tooltip, modal, or accordion) is shown near the top of the app
- Provides a brief, plain-English explanation of the 8% flat tax option
- Does **not** replace professional tax advice
- Full tax education is out of scope for MVP

---

## 7. Making the App Most Useful (MVP Principles)

### 7.1 Clarity Over Completeness
- Only one tax scheme (8%)
- No forms, no jargon, no filing steps

### 7.2 Trust & Confidence
- Step-by-step breakdowns
- Plain English explanations
- No legal or tax advice claims

### 7.3 Fast Feedback
- Compute instantly
- No login required
- No backend dependency for MVP

---

## 8. Non-Functional Requirements

- Mobile-first responsive design
- **Whole number inputs only** (integers; no decimals accepted)
- **Raw number input only** (no comma-formatted input, e.g., "50,000" is not valid input)
- Round all monetary results to **2 decimal places**
- Display all monetary values with **₱ sign and comma separators**
- All computation happens **client-side**
- No backend, no login, no database

---

## 9. Edge Cases & Validation

| Scenario | Behavior |
|---|---|
| Negative monthly input | Invalid — show error on Compute |
| Decimal monthly input | Invalid — whole numbers only, show error on Compute |
| Missing (empty) monthly input | Disable Compute button |
| All zero inputs | Valid — tax due = ₱0 |
| Annual income > ₱3,000,000 | Show eligibility failure, no tax computation |
| Annual income ≤ ₱250,000 | Valid — tax due = ₱0, deduction line hidden |
| Input edited after Compute | Results immediately clear; user must recompute |

**Validation timing:** All input validation is triggered **only on Compute click**, not inline while typing.

---

## 10. Legal Disclaimer (Required — Visible at All Times)

> **Disclaimer:** This calculator is for estimation purposes only based on BIR computation methodology and does not replace professional tax advice.
>
> This tool is intended for **non-VAT registered, purely self-employed individuals** only. It is **not applicable to mixed income earners** (those with both compensation and self-employment income). Consult a licensed tax professional for advice specific to your situation.

---

## 11. MVP Success Criteria

- User can compute taxes in under 30 seconds
- Eligibility result is immediately understood
- Quarterly and annual numbers reconcile correctly
- Zero incorrect results in known test cases
- Disclaimer and VAT/mixed income notes are clearly visible

---

## 12. Out of Scope (MVP)

- VAT computation
- Graduated income tax comparison
- BIR form generation
- Account creation or saved history
- Inline (as-you-type) validation
- Deadline highlighting based on current date

---

## 13. Future / Stretch Goals (Post-MVP)

| Feature | Notes |
|---|---|
| **Export to CSV** | Allow users to export their quarterly and annual tax breakdown as a CSV file |
| Graduated tax comparison | Show side-by-side 8% vs graduated scheme |
| BIR form pre-fill | Map results to BIR form fields |
| Save / history | Let users save past computations |

---

*End of PRD v2.0*
