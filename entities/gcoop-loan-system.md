---
title: ระบบเงินกู้ (Loan System)
created: 2026-09-07
updated: 2026-09-07
type: entity
tags: [loan, oracle, aspnet, powerbuilder, wcf]
sources:
  - raw/documents/mhd-plsql-n-pk-lnnpl.md
  - raw/documents/core-loanservice-cs.md
  - raw/documents/core-loancollservice-cs.md
confidence: high
---

# ระบบเงินกู้ (Loan System) — GCOOP

ระบบจัดการสินเชื่อของสหกรณ์ ครอบคลุมตั้งแต่การยื่นคำขอกู้ อนุมัติ ค้ำประกัน ปิดบัญชี และ NCB

## สถาปัตยกรรม

```
[ASP.NET Web (Saving)]
       ↓
[WCF Service Layer]
       ↓
[C# Business Logic]          [PowerBuilder Service]
 GcoopServiceCs/              PBService125/lncoopsrv.pbl
  - LoanService.cs
  - LoanCollService.cs
  - StructLoanRequest.cs
       ↓
[Oracle Database]
  - PL/SQL Packages (n_pk_lnnpl, w_sheet_ln_collredeem)
  - Functions (ft_getintrate, ft_roundmoney)
  - Tables (lnloantype, lnmast, lnstm, cmroundmoney)
```

## Components หลัก

### 1. C# Services (core/GCOOP/GcoopServiceCs/)
| Class | หน้าที่ |
|---|---|
| `LoanService` | Business logic หลัก, คำนวณดอกเบี้ย, ปัดเศษ |
| `LoanCollService` | จัดการค้ำประกัน, ตรวจสอบวงเงิน |
| `StructLoanRequest` | โครงสร้างข้อมูลคำขอกู้ (xmlMain, xmlGuarantee, xmlClear) |

### 2. Oracle PL/SQL (mhd/GCOOP/PLSQL/)
| Package/Function | หน้าที่ |
|---|---|
| `n_pk_lnnpl` | ดึงข้อมูล collateral, payment statement, คำนวณผลต่างวันที่ |
| `w_sheet_ln_collredeem` | Worksheet การไถ่ถอนค้ำประกัน |
| `ft_getintrate` | ดึงอัตราดอกเบี้ย |
| `ft_roundmoney` | ปัดเศษเงิน |

### 3. PowerBuilder Service (core/GCOOP/PBService125/lncoopsrv.pbl/)
| DataWindow | หน้าที่ |
|---|---|
| `d_lcsrv_loanreq` | ข้อมูลคำขอกู้ |
| `d_lcsrv_loanreq_coll` | ข้อมูลค้ำประกัน |
| `d_lcsrv_loanapv` | ข้อมูลการอนุมัติ |
| `d_lcsrv_loanreq_clr` | ข้อมูลการปิดบัญชี |
| `d_lcsrv_clsmonth_loandata` | ข้อมูลปิดรายเดือน |
| `d_lcsrv_clsyear_loanoption` | ข้อมูลปิดรายปี |

### 4. Reports (PBReport125/)
- `reportloan.pbl` — รายงานเงินกู้ (core)
- `reporloan_mhd.pbl` — รายงานเงินกู้ MHD
- `reportloanassist.pbl` — รายงานเงินกู้ผู้ช่วย
- `prcomloan.pbl` — รายงานเงินกู้รวม

## ตารางหลัก Oracle
| ตาราง | คำอธิบาย |
|---|---|
| `lnloantype` | ประเภทเงินกู้ (config อัตราดอกเบี้ย, เงื่อนไข) |
| `lnmast` | master ข้อมูลเงินกู้ |
| `lnstm` | รายการชำระเงินกู้ |
| `cmroundmoney` | config การปัดเศษ (ใช้ร่วมกับทุกระบบ) |

## การตั้งค่าการปัดเศษ (cmroundmoney)
config ที่ `applgroup_code='LON'`:
- `satang_type` — ประเภทการปัดสตางค์ (00=ไม่ปัด, 10=ปัดขึ้น 5สต.)
- `truncate_pos_amt` — ตำแหน่งทศนิยมที่ตัด
- `round_type` — วิธีปัด (1=ปัดแต่ละขั้น, 2=รวมแล้วปัด)
- `round_pos_amt` — ตำแหน่งที่ปัด

## การค้ำประกัน (Collateral)
- ตรวจสอบจาก `lnloantype`: `usemangrt_status`, `collchkmarrige_status`, `maxcoll_age`
- ตรวจจำนวนค้ำสูงสุด: `maxcountcont_amt` (จำนวนสัญญา), `maxcount_mem` (จำนวนสมาชิก)
- คำนวณวงเงินผ่าน `LoanCollService.Getcollbalance()`

## กระบวนการทำงาน

```
ยื่นคำขอ → ตรวจสอบค้ำประกัน → อนุมัติ → ทำสัญญา → ชำระรายเดือน → ปิดบัญชี
```

## Related
- [[gcoop-loan-tables]] — โครงสร้างตาราง Oracle ระบบเงินกู้
- [[gcoop-loan-collateral]] — ระบบค้ำประกันโดยละเอียด
- [[gcoop-mhd]] — ระบบ MHD หลัก
- [[gcoop-overview]] — ภาพรวมระบบ
