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
