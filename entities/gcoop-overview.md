---
title: GCOOP System Overview
created: 2026-09-07
updated: 2026-09-07
type: entity
tags: [architecture, oracle, aspnet, powerbuilder]
sources: []
confidence: high
---

# GCOOP — ระบบสารสนเทศสหกรณ์

ระบบสารสนเทศสำหรับการบริหารจัดการสหกรณ์ พัฒนาโดยใช้ ASP.NET, Oracle Database, และ PowerBuilder

## โครงสร้างหลัก

### SVN Repository
- `svnGCOOPCORE/mhd/` — ระบบหลัก MHD
- `svnGCOOPCORE/core/` — ระบบ Core infrastructure

### Technology Stack
- **Web:** ASP.NET (C#), IIS
- **Database:** Oracle (PL/SQL, Stored Procedures, Packages)
- **Legacy:** PowerBuilder 12.5
- **Services:** WCF, REST API
- **Tools:** Smart Card Reader, Oracle Data Pump

## โมดูลหลัก
- **เงินฝาก (Saving)** — ระบบเงินฝากสมาชิก
- **เงินกู้ (Loan)** — ระบบสินเชื่อ
- **สมาชิก (Member)** — ระบบทะเบียนสมาชิก
- **ATM** — ระบบ ATM สหกรณ์
- **เงินปันผล (Dividend)** — ระบบคำนวณและจ่ายเงินปันผล
- **ประกัน (Insurance)** — ระบบประกันภัยสมาชิก
- **NCB** — ระบบรายงานข้อมูลสินเชื่อ
- **การเงิน (Finance)** — ระบบบัญชีและการเงิน

## Related
- [[gcoop-mhd]] — รายละเอียดระบบ MHD
- [[gcoop-core]] — รายละเอียดระบบ Core
