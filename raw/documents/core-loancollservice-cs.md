---
source_url: internal/core/GCOOP/GcoopServiceCs/LoanCollService.cs
ingested: 2026-09-07
---

# LoanCollService.cs — Collateral (ค้ำประกัน) Service (C#)

## Class: LoanCollService (GcoopServiceCs namespace)

### Method หลัก: Getcollbalance()
คำนวณยอดคงเหลือวงเงินค้ำประกัน

**Parameters:**
- `coop_id`, `ref_collno`, `colltype_code`, `loantype_code`
- `loan_memno` — เลขสมาชิกผู้กู้
- `loanreq_clr` — เลขสัญญาที่ต้องการ clear
- `req_date` — วันที่ขอกู้
- `remark` (ref) — ข้อความแจ้งเตือน
- `collperm_amt` (ref) — วงเงินที่ได้รับอนุมัติ
- `collused_amt` (ref) — วงเงินที่ใช้ไปแล้ว
- `collbase_percent` (ref) — เปอร์เซ็นต์ฐาน

### Logic การตรวจสอบค้ำประกัน
1. `is_validcoll()` — ตรวจสอบการงดค้ำประกัน
2. `of_getconstantcollpermiss()` — ดึง config วงเงินค้ำประกัน
3. `getcollloancredit()` — คำนวณวงเงินค้ำประกัน
4. `getcolluse_count_all()` — นับจำนวนที่ค้ำไปแล้ว

### Validation Rules
- `maxcountcont_amt` — จำนวนสัญญาสูงสุดที่ค้ำได้
- `maxcount_mem` — จำนวนสมาชิกสูงสุดที่ค้ำได้

### ตารางที่เกี่ยวข้อง
- `lnloantype` — ประเภทเงินกู้ (usemangrt_status, collchkmarrige_status, maxcoll_age)
