---
title: วิเคราะห์ระบบ PowerBuilder PBProcess Overview
created: 2026-09-14
updated: 2026-09-14
type: raw
tags: [raw-source, pbprocess, powerbuilder]
sources: [source-code]
---

# วิเคราะห์ระบบ PowerBuilder PBProcess Overview

## 1. ข้อมูลทั่วไปของระบบ

**ชื่อระบบ:** PowerBuilder Process System (PBProcess)
**เทคโนโลยี:** PowerBuilder Classic
**ตำแหน่ง:** `/root/gcoop_hermes/mhd/GCOOP/PBProcess/`
**ขนาดรวม:** 51.8MB (Source Libraries) + 15.5MB (Runtime Libraries)

## 2. โครงสร้างไฟล์และ Components

### 2.1 Source Libraries (.pbl) - 11 libraries

| Library | Size | Purpose | Priority |
|---------|------|---------|----------|
| **pckeeping.pbl** | 13.9MB | บัญชีโบรกเกอร์/ทะเบียน | 🔴 Critical |
| **pcdeposit.pbl** | 11.4MB | เงินฝาก/ออมทรัพย์ | 🔴 Critical |
| **pcloan.pbl** | 8.5MB | สินเชื่อ/เงินกู้ | 🔴 Critical |
| **pcfinance.pbl** | 8.1MB | การเงิน/บัญชี | 🔴 Critical |
| **pcdivavg.pbl** | 3.7MB | เฉลี่ยปันผล | 🟡 Medium |
| **pccommon.pbl** | 2.0MB | Common utilities | 🟠 High |
| **pcinsurance.pbl** | 1.6MB | ประกันภัย | 🟡 Medium |
| **pcinvertment.pbl** | 1.2MB | ลงทุน/การเงิน | 🟡 Medium |
| **pcmbshr.pbl** | 0.7MB | สมาชิก/หุ้น | 🟠 High |
| **pcaccount.pbl** | 0.5MB | บัญชีทั่วไป | 🟠 High |
| **pbprocess.pbl** | 0.1MB | Main process | 🔴 Critical |

### 2.2 Runtime Libraries (.pbd) - Deployment files

- **pckeeping.pbd:** 4,414KB (บัญชีโบรกเกอร์)
- **pcdeposit.pbd:** 3,364KB (เงินฝาก)
- **pcloan.pbd:** 2,674KB (สินเชื่อ)
- **pcfinance.pbd:** 2,133KB (การเงิน)
- **pcdivavg.pbd:** 1,285KB (เฉลี่ยปันผล)
- **pcinsurance.pbd:** 453KB (ประกันภัย)
- **pcinvertment.pbd:** 398KB (ลงทุน)
- **pccommon.pbd:** 304KB (Common)
- **pcmbshr.pbd:** 218KB (สมาชิก)
- **pcaccount.pbd:** 176KB (บัญชี)
- **pbprocess.pbd:** 36KB (Main)

### 2.3 Executables และ Config Files

- **pbprocess.exe:** 30KB (Main executable)
- **pbprocess.pbt:** 269B (Target configuration)
- **pbprocess.usr.opt:** 1,614B (User options)
- **pbprocess.pbw:** 167B (Workspace file)

## 3. Library Dependencies Chain

ตาม pbprocess.pbt configuration:

```
pbprocess.pbl (Main)
├── pccommon.pbl (Utilities)
├── pcaccount.pbl (Account base)
├── pcdeposit.pbl (Deposits)
├── pcfinance.pbl (Finance)
├── pcinvertment.pbl (Investment)
├── pckeeping.pbl (Bookkeeping)
└── pcloan.pbl (Loans)
```

**Missing from target:** pcdivavg.pbl, pcinsurance.pbl, pcmbshr.pbl (อาจเป็น optional libraries)

## 4. ระบบงานหลัก (Business Modules)

### 4.1 Core Financial Systems (4 modules)
1. **pckeeping.pbl (13.9MB)** - บัญชีโบรกเกอร์/ทะเบียน
2. **pcdeposit.pbl (11.4MB)** - เงินฝาก/ออมทรัพย์  
3. **pcloan.pbl (8.5MB)** - สินเชื่อ/เงินกู้
4. **pcfinance.pbl (8.1MB)** - การเงิน/บัญชี

### 4.2 Secondary Systems (4 modules)
5. **pcdivavg.pbl (3.7MB)** - เฉลี่ยปันผล
6. **pcinsurance.pbl (1.6MB)** - ประกันภัย
7. **pcinvertment.pbl (1.2MB)** - ลงทุน/การเงิน
8. **pcmbshr.pbl (0.7MB)** - สมาชิก/หุ้น

### 4.3 Infrastructure (3 modules)
9. **pccommon.pbl (2.0MB)** - Common utilities/shared functions
10. **pcaccount.pbl (0.5MB)** - บัญชีทั่วไป/base account
11. **pbprocess.pbl (0.1MB)** - Main process/entry point

## 5. การทำงานของระบบ

### 5.1 Architecture Pattern
- **Modular Design:** แยก business logic ตาม domain (loan, deposit, finance)
- **Shared Services:** pccommon.pbl ให้ utilities ร่วม
- **Deployment Ready:** มี .pbd files พร้อม production deployment

### 5.2 Size Analysis
- **Large Modules (>5MB):** pckeeping (13.9), pcdeposit (11.4), pcloan (8.5), pcfinance (8.1) = 41.9MB (81% ของระบบ)
- **Core Business:** 4 modules ใหญ่คือหัวใจของระบบ
- **Supporting:** 7 modules เล็ก เป็น utilities และ secondary functions

## 6. จุดสำคัญและข้อสังเกต

### 6.1 Critical Findings
- **Legacy PowerBuilder:** Classic PB (ไม่ใช่ .NET) ดูจาก .pbl/.pbd files
- **Complete Deployment:** มีทั้ง source (.pbl) และ runtime (.pbd)
- **Modular Architecture:** แยก domain ชัด เช่น loan, deposit, finance
- **Production Ready:** มี executable และ config files ครบ

### 6.2 Business Process Coverage
- **Loan System Integration:** pcloan.pbl (8.5MB) ใหญ่ = ระบบสินเชื่อครอบคลุม
- **Deposit Management:** pcdeposit.pbl (11.4MB) ใหญ่ที่สุดรอง = เงินฝากเป็นธุรกิจหลัก  
- **Bookkeeping:** pckeeping.pbl (13.9MB) ใหญ่ที่สุด = บัญชีซับซ้อน
- **Complete Financial Suite:** ครอบคลุม loan, deposit, insurance, investment

## 7. การเชื่อมโยงกับระบบอื่น

### 7.1 Integration Points
- **MHD Web System:** เชื่อมผ่าน WCF services (พบใน Session 1)
- **Oracle Database:** ใช้ database เดียวกับ MHD system
- **Batch Processing:** PBProcess ทำงานแบบ batch สำหรับ bulk operations

### 7.2 Expected Workflows
- **Daily Batch:** คำนวณดอกเบี้ย, ปันผล, รายงานประจำวัน
- **Month-end:** ปิดบัญชี, สรุปยอด, รายงานเดือน
- **Data Migration:** import/export ข้อมูลระหว่างระบบ

## 8. Next Steps สำหรับ Deep Analysis

1. **pckeeping.pbl** - วิเคราะห์ระบบบัญชีโบรกเกอร์ (ใหญ่ที่สุด)
2. **pcdeposit.pbl** - ระบบเงินฝาก/ออมทรัพย์ (ธุรกิจหลัก)
3. **pcloan.pbl** - ระบบสินเชื่อ (เชื่อมกับ MHD)
4. **pcfinance.pbl** - ระบบการเงิน/บัญชี (critical)
5. **pccommon.pbl** - Common services (foundation)

---

**หมายเหตุ:** PowerBuilder libraries (.pbl) เป็น binary format ต้องใช้ PowerBuilder IDE เพื่อ export source code หรือใช้ reverse engineering tools เพื่อวิเคราะห์เชิงลึก