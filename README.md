# Tally Export → FIFO Aging & 43B(h) Compliance Report

## 📌 Overview
This project processes raw ledger data exported from **Tally** and generates a **creditors’ aging report** with a special focus on **Section 43B(h)** compliance.  
It identifies and categorizes **advances to suppliers**, calculates **aging as per FIFO logic**, and produces a **summary sheet** for quick compliance review.

**Date range of raw export:** 14-02-202X to 15-05-202X

---

## 📂 Input Data
- **Source:** Tally export (Excel)
- **Data Type:** Detailed ledger report (creditors)
- **Required Columns:** Date, Particulars, Voucher Type, Voucher No., Debit, Credit

---

## ⚙️ Processing Logic
1. **Load Excel export from Tally**
2. **FIFO Matching**
   - Payments (debits) are adjusted against purchases (credits) in chronological order
   - Excess payments before purchases are flagged as **advances**
3. **43B(h) Compliance**
   - Identifies outstanding dues beyond the 45-day limit for MSMEs
4. **Remarks Tagging**
   - Flags `Advance to Supplier`
5. **Summary Sheet**
   - Party-wise total outstanding, advance amounts, and 43B(h) exposure

---

## 📊 Output
- **Detailed Aging Report** → FIFO-based outstanding balance
- **43B(h) Compliance Report** → List of overdue suppliers beyond MSME limits
- **Summary Sheet** → At-a-glance totals by supplier

---

## 🚀 How to Use
1. Export the **Ledger Report** from Tally:
   - Go to **Gateway of Tally → Display → Account Books → Ledger**
   - Select the **Creditors Ledger Group**
   - Press `Alt+E` → Export → Excel (detailed format)
   - Ensure **columns: Date, Particulars, Voucher Type, Voucher No., Debit, Credit**
2. Save the file in the `input` folder
3. Run the Python script
4. Output files will be in the `output` folder

---

## 📈 FIFO + 43B(h) Workflow Diagram

```mermaid
flowchart TD
    A[📤 Export from Tally] --> B[📄 Read Excel into Pandas]
    B --> C[🔄 FIFO Matching: Payments vs Purchases]
    C --> D[🏷 Tag Advances to Supplier]
    D --> E[📅 Check 43B_h 45-Day Rule]
    E --> F[📝 Generate Detailed Aging Report]
    E --> G[📊 Create Summary Sheet]
    F --> H[💾 Save Outputs]
    G --> H
    H --> I[✅ Final Compliance Reports]

