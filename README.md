# Creditors 43B(h) Compliance & Aging Report Tool

## Overview
This Python script processes **raw Excel data exported from Tally** to:
- Generate **creditors' aging** as of a cutoff date using FIFO logic.
- Identify **advances to suppliers** separately from regular payables.
- Perform **Section 43B(h) disallowance** calculations for payments made after the cutoff date.
- Summarize the results in a **summary sheet** for easy review.

The script is designed to handle **cutoff-based FIFO adjustments** and ensures that:
- Payments after the cutoff date are applied to outstanding debits in FIFO order.
- Paid amounts never exceed the actual outstanding debit values.
- Only after-cutoff-date payments are considered for **43B(h) disallowance**.

---

## Data Source
- **Source**: Tally ERP/Prime Ledger Report
- **Export Period**: 14-02-202X to 15-05-202X
- **Type**: Detailed ledger export for creditors
- **Format**: `.xlsx` (Excel) file

---

## Output Files
1. **43B_h_Report.xlsx**
   - Detailed sheet showing transaction-level adjustments.
   - Summary sheet with:
     - Party Name
     - Opening Payables (as of cutoff date)
     - Debits after cutoff
     - Payments after cutoff
     - Disallowed Amount (43B(h))
     - Closing Balance

2. **Logs.txt**
   - Tracks execution details, errors, and skipped entries.

---

## Features
- ✅ FIFO-based adjustment logic
- ✅ Handles advance payments
- ✅ Enforces paid amount ≤ debit amount
- ✅ Automatic summary generation
- ✅ Works with large datasets

---

## How It Works
1. **Cutoff Date**  
   - User inputs a cutoff date (e.g., `31-03-2025`).
   - Transactions after this date are treated as post-cutoff.

2. **FIFO Matching**  
   - Pre-cutoff debits are matched with pre-cutoff credits first.
   - Any remaining payables as of cutoff are matched with post-cutoff credits for disallowance calculation.

3. **Advance Handling**  
   - If a payment exceeds purchases before cutoff, the excess is flagged as an **advance**.

---

## Requirements
- Python 3.9+
- Required Libraries:
  ```bash
  pip install pandas openpyxl
flowchart TD
  A[Raw Ledger Excel (Tally export)<br/>14-02-202X → 15-05-202X] --> B[Parse ledger rows<br/>(detect "Ledger:" header)]
  B --> C[Normalize transactions<br/>(Party, Date, Debit, Credit)]
  C --> D{Split by Cutoff Date}
  D --> D1[Pre-cutoff transactions (<= cutoff)]
  D --> D2[Post-cutoff transactions (> cutoff)]

  D1 --> E1[Pre-cutoff FIFO matching]
  E1 --> F1[Outstanding invoices as on cutoff]
  E1 --> ADV[Detect Advances (excess payments before cutoff)]

  D2 --> E2[Collect after-cutoff payments (debits)]
  F1 --> G[Allocate after-cutoff payments to outstanding invoices<br/>(FIFO order, cap allocations)]
  E2 --> G

  G --> H[43B(h) check]
  H -->|Compare paid-date to invoice_date + 45 days| I{Within 45 days?}
  I -->|Yes| J[Allowed - No disallowance]
  I -->|No| K[Disallowed amount u/s 43B(h)]

  %% Outputs
  F1 --> OUT1[Aging Summary (buckets)]
  E1 --> OUT2[FIFO Log (details)]
  H --> OUT3[43B(h) Detailed]
  K --> OUT4[43B(h) Summary per Party]

  style A fill:#f9f,stroke:#333,stroke-width:1px
  style H fill:#fffae6,stroke:#333
  style K fill:#ffe6e6,stroke:#333
