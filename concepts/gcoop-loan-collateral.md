---
title: ระบบค้ำประกันเงินกู้ (Loan Collateral)
created: 2026-09-07
updated: 2026-09-07
type: concept
tags: [loan, oracle, aspnet]
sources:
  - raw/documents/core-loancollservice-cs.md
confidence: high
---

# ระบบค้ำประกันเงินกู้ (Loan Collateral)

## Logic การตรวจสอบ (LoanCollService.Getcollbalance)

```
1. is_validcoll()
   → ตรวจ lnloantype: usemangrt_status, maxcoll_age
   → ตรวจสถานะสมาชิกผู้ค้ำ, สถานะสมรส

2. of_getconstantcollpermiss()
   → ดึง config วงเงิน: maxcount_mem, maxcountcont_amt, maxcollcredit_amt

3. getcollloancredit()
   → คำนวณวงเงินที่ค้ำได้ตาม lncollpermgrp, หุ้น, เงินเดือน

4. getcolluse_count_all()
   → นับจำนวนและวงเงินที่ค้ำไปแล้ว

5. ตรวจ limit → คืนยอดคงเหลือ
```

## Validation
- `maxcountcont_amt` — สัญญาที่ค้ำได้สูงสุด
- `maxcount_mem` — สมาชิกที่ค้ำได้สูงสุด
- `maxcoll_age` — อายุสูงสุดของผู้ค้ำ
- `usemangrt_status` — การใช้ผู้ค้ำ
- `collchkmarrige_status` — ตรวจสถานะสมรส

## Related
- [[gcoop-loan-system]] — ระบบเงินกู้ทั้งหมด
- [[gcoop-mhd]] — ระบบ MHD หลัก
