---
source_url: internal/core/GCOOP/GcoopServiceCs/LoanService.cs
ingested: 2026-09-07
---

# LoanService.cs — Core Loan Business Logic (C#)

## Namespace: GcoopServiceCs

### Class: LoanService
Business logic หลักของระบบเงินกู้ ใช้ DataLibrary (Sta, Sdt) เชื่อมต่อ Oracle

### Properties
- `years`, `months`, `days` — ช่วงเวลา
- `truncpos` — ตำแหน่งหลักทศนิยม
- `roundpos` — ตำแหน่งปัดเศษ
- `roundtype` — วิธีปัดเศษ (1=ปัดแต่ละขั้น, 2=รวมแล้วปัด)
- `satangtype` — ประเภทการปัดสตางค์
- `coopId`

### Methods หลัก
- `OfSetConfigRoundMoney(coopId, SystemCode, FunctionCode)` — ดึง config การปัดเศษจากตาราง `cmroundmoney`
- `OfRoundMoneyByConfig(coopId, amount)` — ปัดเศษเงินกู้ตาม config

### ตารางที่เกี่ยวข้อง
- `cmroundmoney` — config การปัดเศษ (satang_type, truncate_pos_amt, round_type, round_pos_amt)
- query ด้วย `coop_id`, `applgroup_code='LON'`, `function_code`

### satang_type values
- `00` — ไม่ปัด
- `10` — ปัดขึ้นเต็ม 5 สตางค์
