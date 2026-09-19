---
coop: mcc
title: GCOOP MCC PowerBuilder System
created: 2026-09-14
updated: 2026-09-14
type: entity
tags: [powerbuilder, saving, loan, member, workflow]
sources: [raw/documents/mcc-session2-powerbuilder-analysis.md]
confidence: high
---

# GCOOP MCC PowerBuilder System

ระบบ PowerBuilder Classic (PB 12.5) ของ MCC ประกอบด้วย 3 ส่วนหลัก: **PBProcess** (batch engine), **iSavBOfc** (back office savings app), และ **PBReport125** (reporting engine) — เชื่อมต่อ Oracle ผ่าน charset TH8TISASCII

---

## PBProcess — Batch Processing Engine (`GCOOP/PBProcess/`)

### LibList (จาก pbprocess.pbt จริง)

```
pbprocess.pbl;pccommon.pbl;pcaccount.pbl;pcdeposit.pbl;pcdivavg.pbl;
pcfinance.pbl;pcinsurance.pbl;pcinvertment.pbl;pckeeping.pbl;pcloan.pbl;pcmbshr.pbl
```

### Library ทั้งหมดพร้อมขนาดจริง

| Library | .pbl | .pbd | บทบาท |
|---|---|---|---|
| `pckeeping.pbl` | **14.4 MB** | 4.5 MB | Bookkeeping — บัญชีรับจ่าย, แยกประเภท (ใหญ่สุด) |
| `pcdeposit.pbl` | **11.9 MB** | 3.4 MB | Deposit — เงินฝากสหกรณ์ทุกประเภท |
| `pcloan.pbl` | **9.1 MB** | 2.8 MB | Loan — สินเชื่อสมาชิก |
| `pcfinance.pbl` | **8.5 MB** | 2.2 MB | Finance — การเงิน, งบการเงิน |
| `pcdivavg.pbl` | **3.9 MB** | 1.3 MB | Dividend averaging — คำนวณเฉลี่ยคืน/ปันผล |
| `pcinsurance.pbl` | 1.7 MB | 463 KB | Insurance — ประกันสมาชิก |
| `pcinvertment.pbl` | 1.3 MB | 407 KB | Investment — การลงทุน |
| `pccommon.pbl` | 2.1 MB | 311 KB | Common utilities — ใช้ร่วมทุก module |
| `pcmbshr.pbl` | 693 KB | 223 KB | Member shares — หุ้นสมาชิก |
| `pcaccount.pbl` | 536 KB | 180 KB | Account — บัญชีสมาชิก |
| `pbprocess.pbl` | 82 KB | 37 KB | Main entry point + process service |

### Function Signatures ที่พบจริงใน pbprocess.usr.opt

| Class | Method | Signature |
|---|---|---|
| `n_cst_process_service` | `of_pbprocess` | `(string as_procid) returns integer` |
| `n_cst_pbprocess_loan` | `ue_pbprocess` | event |
| `n_cst_proc_slmnyret` | `of_post_toslippayout_lrt_pxloan_mcc` | `() returns integer` |
| `n_cst_proc_slmnyret` | `of_postpayinslippx_mcc` | `(string as_memnno, datetime adtm_trndate, string as_payoutslipno) returns integer` |
| `n_cst_proc_slmnyret` | `of_postslip_payinloan` | `(n_ds ads_payinslip, n_ds ads_payinslipdet, n_ds ads_payinslipexp) returns integer` |
| `n_cst_proc_slmnyret` | `of_poststm_contract` | `(str_poststmloan astr_lnstatement) returns integer` |
| `n_cst_proc_slmnyret` | `of_setlistcontnewlrtmcc` | `() returns integer` |
| `n_cst_keeping_process` | `of_processloan` | `() returns integer` |
| `n_cst_keeping_process` | `of_processloanocalint` | `(ref n_ds ads_loandata, datetime adtm_calintto) returns integer` |
| `n_cst_keeping_process` | `of_rcvprocess` | `() returns integer` |
| `n_cst_pbprocess_keeping` | `ue_pbprocess` | event |

### Workspace Targets (pbprocess.pbw)

| Target | Path | วัตถุประสงค์ |
|---|---|---|
| Production | `pbprocess.pbt` | MCC local production build |
| Migration | `..\..\CONVERT_MCC\mcc_pipeline\mcc_pipe.pbt` | Data migration pipeline |
| Core | `..\..\..\CORE\GCOOP\PBProcess\pbprocess.pbt` | Core/shared build |

---

## iSavBOfc — Back Office Savings Application (`iSavBOfc/`)

### โครงสร้าง Directory และ Libraries

#### `iBank/` — Core Application

| Library | ขนาด | บทบาท |
|---|---|---|
| `isavbofc.pbl` | 468 KB | Main app entry point |
| `ibanktrn.pbl` | **2,574 KB** | Transaction engine — ใหญ่สุดใน core |
| `chack_stmbal.pbl` | 224 KB | ตรวจสอบ statement balance |
| `iimportdept.pbl` | **4,447 KB** | Import ข้อมูลแผนก/payroll — ใหญ่มาก |
| `isavbofc.exe` | 46 KB | Executable |

#### `sBaseCom/` — Base Infrastructure Services

| Library | ขนาด | บทบาท |
|---|---|---|
| `cmappcom.pbl` | 576 KB | Application common |
| `cmcoinit.pbl` | 1,461 KB | Cooperative initialization |
| `cmconfig.pbl` | 1,545 KB | Configuration management |
| `cmcomsrv.pbl` | 264 KB | Common service |
| `cmtdtsrv.pbl` | 409 KB | Transaction date service |
| `cmrepsrv.pbl` | 374 KB | Report service |
| `cmsqlsrv.pbl` | 231 KB | SQL service |
| `cmoutsrv.pbl` | 196 KB | Output/export service |
| `cmprnsrv.pbl` | 162 KB | Print service |

#### `sBussCom/` — Business Services

| Library | ขนาด | บทบาท |
|---|---|---|
| `cmsrv_fin.pbl` | **2,140 KB** | Finance service — ใหญ่สุดใน sBussCom |
| `pccomloan.pbl` | 1,254 KB | Common loan library |
| `cmsrv_loan.pbl` | 1,135 KB | Loan service |
| `cmsrv_dept.pbl` | 901 KB | Department service |
| `cmsrv_mbshr.pbl` | 200 KB | Member share service |

#### `PFC105/` — PowerBuilder Foundation Class 10.5

| Library | ขนาด | บทบาท |
|---|---|---|
| `pfcapsrv.pbl` | **6,428 KB** | Application service — ใหญ่สุดใน PFC |
| `pfcmain.pbl` | 5,497 KB | Main PFC framework |
| `pfcdwsrv.pbl` | 4,758 KB | DataWindow service |
| `pfcutil.pbl` | 2,198 KB | Utilities |
| `pfcwnsrv.pbl` | 1,371 KB | Window service |
| `SERVICE.PBL` | 555 KB | Service base |
| `pfe*.pbl` (5 files) | 211–348 KB | Extended PFC layer |

#### `pbpro/` — PBProcess Shared Libs (copy for iSavBOfc)

| Library | ขนาด | หมายเหตุ |
|---|---|---|
| `pcdeposit.pbl` | **15,948 KB** | ใหญ่กว่า PBProcess version (11.9 MB)! |
| `pccommon.pbl` | 2,105 KB | เท่ากัน |
| `rpt_report_dept.pbl` | 1,597 KB | Department report |
| `pbprocess.pbl` | 38 KB | Smaller version |

### iSavBOfc LibList (26 libraries จาก isavbofc.pbt)

```
isavbofc.pbl; ibanktrn.pbl; chack_stmbal.pbl; iimportdept.pbl;
..\pbpro\pbprocess.pbl; ..\pbpro\pccommon.pbl;
..\sBaseCom\cmappcom.pbl; cmcoinit.pbl; cmcomsrv.pbl;
cmoutsrv.pbl; cmprnsrv.pbl; cmrepsrv.pbl; cmtdtsrv.pbl;
..\sBussCom\pccomloan.pbl;
..\PFC105\pfcapsrv.pbl; pfcdwsrv.pbl; pfcmain.pbl;
pfcutil.pbl; pfcwnsrv.pbl; pfeapsrv.pbl; pfedwsrv.pbl;
pfemain.pbl; pfeutil.pbl; pfewnsrv.pbl; SERVICE.PBL
```

### Bank Payment Configuration (iBank.ini จริง)

iSavBOfc รองรับการโอนเงิน Direct Credit **9 ธนาคาร**:

| Code | ธนาคาร | Format | หมายเหตุ |
|---|---|---|---|
| 006 (default) | KTB กรุงไทย | FIXED-POST (.trf / .DAT) | Main bank |
| 002-DC | BBL กรุงเทพ | FIXED-UPAYDAT (.dat) | fee 5 บาท |
| 004-DC | K-Bank กสิกรไทย | DCT (.txt) | fee 5 บาท |
| 014-DC | SCB ไทยพาณิชย์ | PAY (.txt), code=tpds408 | fee 5 บาท |
| 017-MD | Citibank | PTA (.txt) | Media Clearing |
| 022-DC | PEA การไฟฟ้า | FIXED-PEA (.text) | fee 0 บาท |
| 025-SM | BAAC ธ.ก.ส. Same-day | FIXED-CAPIT070 | acc=0470048533 |
| 034-DC | TKS (BAAC) | FIXED-BAAC (floppy A:\) | fee 10 บาท |
| 006-IP | KTB Internet Payment | FIXED-POST (.DAT) | acc=1096058065 |

---

## PBReport125 — Reporting Engine (`GCOOP/PBReport125/`)

### Libraries ที่มีจริงในโฟลเดอร์

| Library | ขนาด .pbl | บทบาท |
|---|---|---|
| `reportdepositmcc.pbl` | **4,460 KB** | รายงานเงินฝาก MCC — ใหญ่สุด |
| `reportfinancemcc.pbl` | 2,842 KB | รายงานการเงิน MCC |
| `reportmbshr_mcc.pbl` | 2,200 KB | รายงานหุ้นสมาชิก MCC |
| `prcommon.pbl` | 2,105 KB | Common report utilities |
| `reportshrlon.pbl` | 1,073 KB | รายงานสินเชื่อหุ้น |
| `reportmbshare.pbl` | 209 KB | รายงานหุ้น (base) |
| `pbreport.pbl` | 118 KB | Main engine entry |
| `pbreport.exe` | 32 KB | Executable |

### Report Library Categories (จาก pbreport.pbt LibList)

รวม ~30 .pbd ที่ reference จาก CORE path:

| กลุ่ม | Libraries |
|---|---|
| บัญชีสมาชิก | reportaccount, accountservice |
| เงินฝาก | reportdeposit, reportdeposit_ole, reportdepositmcc |
| การเงิน | reportfinance, reportfinance_ole, reportfinancemcc |
| ปันผล/เฉลี่ยคืน | reportdiv, reportdiv_ole |
| บัญชี/Keeping | reportkeeping, reportkeeping_ole |
| สินเชื่อ | prcomloan, reportloanassist |
| หุ้นสมาชิก | reportmbshr, reportmbshr_ole, reportmbshr_mcc, rptstdmbshr |
| สินเชื่อหุ้น | reportshrlon, reportshrlon_ole, rptstdshrlon |
| ประกัน | reportinsure |
| การลงทุน | reportinvestments |
| HR/สวัสดิการ | reporthr, reportwalfare |
| Admin/Legal | reportadmin, reportassist, reportlawsys |
| Cloud/Others | reportcloud, reportcmd, dw2xls12.x |

### Report Scheduling (RunReport.xml — Windows Task Scheduler)

```
Author: COOPWEB\Administrator (2015-12-16)
Trigger: ทุก 1 นาที (PT1M interval), ทุกวัน
ExecutionTimeLimit: P3D (3 วัน max)
MultipleInstances: IgnoreNew

Action 1: runreport.bat
  → for %%i in (%SystemRoot%\Temp\*.bat) do %%i
  → Execute .bat files queued in %TEMP%

Action 2: runreportdel.bat
  → del %SystemRoot%\Temp\*.bat
  → Cleanup after execution
```

**Pattern:** Web system สร้าง .bat ใน %TEMP% → Scheduler รัน ทุก 1 นาที → Report executed → Cleanup

---

## Oracle Database Integration

### Connection Matrix (ทั้งระบบ จากไฟล์ .ini จริง)

| Profile | Server | Schema | ระบบ | วัตถุประสงค์ |
|---|---|---|---|---|
| Main Production | `192.198.1.171/gcoop` | `iscodoaeuat` | PBProcess + iSavBOfc (prod) | Primary DB |
| Legacy Storage | `192.198.1.201/saving` | `dbo` | iSavBOfc rfscold | Cold/historical data |
| DBO Schema | `192.198.1.171/dbo` | `dbo` | iSavBOfc rfscold1/2 | Additional schema |
| Dev Local | `localhost/gcoop` | `iscoaero` | iSavBOfc (dev) | Development |
| Oracle XE | `localhost/XE` | `iscoaero` | iSavBOfc (dev) | Oracle Express Edition |

**Thai charset:** `NLS_Charset='TH8TISASCII'` ทุก connection  
**AutoCommit:** `false` — manual transaction control ทั้งหมด

### Branch Control

| Code | สภาพแวดล้อม | ระบบ |
|---|---|---|
| `057001` | Production | iSavBOfc root |
| `077001` | Development | iSavBOfc iBank |

---

## Batch Processing Workflow

### Daily Batch

```
1. pccommon          → Initialize parameters, validate date
2. pcdeposit         → Daily deposit interest accrual
3. pcloan            → n_cst_keeping_process.of_processloan()
                     → n_cst_keeping_process.of_processloanocalint(loandata, date)
4. pckeeping         → n_cst_keeping_process.of_rcvprocess()
5. pcfinance         → Daily financial reconciliation
6. n_cst_process_service.of_pbprocess(as_procid) → Dispatch by process ID
```

### Month-End Batch

```
1. pcdeposit         → Monthly deposit summary
2. pcloan            → n_cst_proc_slmnyret.of_poststm_contract(lnstatement)
                     → n_cst_proc_slmnyret.of_setlistcontnewlrtmcc()
3. pcdivavg          → คำนวณเฉลี่ยคืน/ปันผล
4. pcinsurance       → Premium processing
5. pcfinance         → Monthly financial statements
6. pcmbshr           → Member share balances
```

### Salary Payment (iSavBOfc)

```
1. iimportdept       → Import payroll data from department
2. ibanktrn          → Query member salary/welfare
3. cmsrv_fin         → Calculate deductions
4. pccomloan         → Loan repayment deduction
5. n_cst_proc_slmnyret.of_postpayinslippx_mcc(memnno, trndate, slipno)
6. iBank.ini         → Select bank format (KTB/BBL/KBank/SCB ...)
7. Output: Direct Credit file → ส่งธนาคาร
```

---

## Related

- [[gcoop-mcc]] — ภาพรวม MCC system
- [[gcoop-mcc-web-system]] — Web layer (ASP.NET) ที่ trigger PB batch
- [[gcoop-mcc-infrastructure]] — PLSQL + deployment environment
- [[gcoop-mcc-integration-architecture]] — Integration patterns ระหว่าง Web↔PB↔Oracle
