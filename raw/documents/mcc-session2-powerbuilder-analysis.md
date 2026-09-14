# MCC Session 2: PowerBuilder Analysis
**วันที่วิเคราะห์:** 2026-09-14  
**ขอบเขต:** `/root/gcoop_hermes/mcc/GCOOP/PBProcess/`, `/root/gcoop_hermes/mcc/iSavBOfc/`, `/root/gcoop_hermes/mcc/GCOOP/PBReport125/`  
**เครื่องมือ:** `ls -la`, `cat` (readable text files), binary inspection via terminal  
**หมายเหตุ:** .pbl/.pbd คือ binary PowerBuilder Classic; อ่านได้เฉพาะ .pbt/.pbw/.ini/.bat/.reg/.xml/.txt — size ใช้วัดความสำคัญของโมดูล  

---

## 1. PBProcess — Batch Processing Engine

### 1.1 ไฟล์และ LibList (จาก pbprocess.pbt)

จากการอ่าน `/root/gcoop_hermes/mcc/GCOOP/PBProcess/pbprocess.pbt`:

```
appname "pbprocess";
applib "pbprocess.pbl";
LibList "pbprocess.pbl;pccommon.pbl;pcaccount.pbl;pcdeposit.pbl;pcdivavg.pbl;
         pcfinance.pbl;pcinsurance.pbl;pcinvertment.pbl;pckeeping.pbl;pcloan.pbl;pcmbshr.pbl";
type "pb";
```

### 1.2 รายการ Library พร้อมขนาด

| Library (.pbl) | ขนาด .pbl | ขนาด .pbd | อันดับ | บทบาทที่อนุมานจากชื่อ |
|---|---|---|---|---|
| `pckeeping.pbl` | **14,386 KB (14.4 MB)** | 4,514 KB | 1 | Bookkeeping — บัญชีรับจ่าย, บัญชีแยกประเภท |
| `pcdeposit.pbl` | **11,943 KB (11.9 MB)** | 3,446 KB | 2 | Deposit — เงินฝากสหกรณ์ทุกประเภท |
| `pcloan.pbl` | **9,054 KB (9.1 MB)** | 2,787 KB | 3 | Loan — สินเชื่อสมาชิก |
| `pcfinance.pbl` | **8,551 KB (8.5 MB)** | 2,186 KB | 4 | Finance — การเงิน, งบการเงิน |
| `pcdivavg.pbl` | **3,924 KB (3.9 MB)** | 1,316 KB | 5 | Dividend averaging — คำนวณเงินปันผล/เฉลี่ยคืน |
| `pcinsurance.pbl` | **1,725 KB (1.7 MB)** | 463 KB | 6 | Insurance — ประกันสมาชิก |
| `pcinvertment.pbl` | **1,270 KB (1.3 MB)** | 407 KB | 7 | Investment — การลงทุน |
| `pccommon.pbl` | **2,105 KB (2.1 MB)** | 311 KB | 8 | Common utilities — ใช้ร่วมทุก module |
| `pcaccount.pbl` | **536 KB** | 180 KB | 9 | Account — บัญชีสมาชิก |
| `pcmbshr.pbl` | **693 KB** | 223 KB | 10 | Member shares — หุ้นสมาชิก |
| `pbprocess.pbl` | **82 KB** | 37 KB | 11 | Main entry point + process service |
| `pbprocess.exe` | 30 KB | — | — | Executable |

**รวม .pbl ทั้งหมด:** ~54 MB  
**รวม .pbd ทั้งหมด:** ~15 MB

### 1.3 Function ที่พบใน pbprocess.usr.opt (binary readable strings)

จากการอ่าน `pbprocess.usr.opt` (5,504 bytes) พบ function signature ที่อ่านได้:

| Class | Method | พารามิเตอร์ที่ระบุ |
|---|---|---|
| `n_cst_process_service` | `of_pbprocess` | `string as_procid` → returns integer |
| `n_cst_pbprocess_loan` | `ue_pbprocess` | (event) |
| `n_cst_proc_slmnyret` | `of_post_toslippayout_lrt_pxloan_mcc` | () → returns integer |
| `n_cst_proc_slmnyret` | `of_postpayinslippx_mcc` | `string as_memnno, datetime adtm_trndate, string as_payoutslipno` → returns integer |
| `n_cst_proc_slmnyret` | `of_postslip_payinloan` | `n_ds ads_payinslip, n_ds ads_payinslipdet, n_ds ads_payinslipexp` → returns integer |
| `n_cst_proc_slmnyret` | `of_poststm_contract` | `str_poststmloan astr_lnstatement` → returns integer |
| `n_cst_proc_slmnyret` | `of_setlistcontnewlrtmcc` | () → returns integer |
| `n_cst_keeping_process` | `of_processloan` | () → returns integer |
| `n_cst_keeping_process` | `of_processloanocalint` | `ref n_ds ads_loandata, datetime adtm_calintto` → returns integer |
| `n_cst_keeping_process` | `of_rcvprocess` | () → returns integer |
| `n_cst_pbprocess_keeping` | `ue_pbprocess` | (event) |

> **หมายเหตุ:** ข้อมูล function เหล่านี้อ่านจากไฟล์ binary .usr.opt (PowerBuilder IDE user options) ซึ่งเก็บ breakpoint/watch items — เป็น class name จริงในระบบ

### 1.4 Workspace Targets (จาก pbprocess.pbw)

```
DefaultTarget "pbprocess.pbt"
Targets:
  0: pbprocess.pbt          -- Production build (MCC local)
  1: ..\..\CONVERT_MCC\mcc_pipeline\mcc_pipe.pbt  -- Data migration pipeline
  2: ..\..\..\CORE\GCOOP\PBProcess\pbprocess.pbt   -- Core/shared build
```

ยืนยันว่ามี **3 environments**: Production MCC, Pipeline/Migration, และ Core shared

---

## 2. iSavBOfc — Back Office Savings Application

### 2.1 โครงสร้างไดเรกทอรี

```
iSavBOfc/
├── iBank/                   ← Main application directory
│   ├── isavbofc.pbl (468 KB)  ← App entry point
│   ├── ibanktrn.pbl (2,574 KB) ← Transaction engine (ใหญ่สุดใน core)
│   ├── chack_stmbal.pbl (224 KB) ← Check/validate statement balance
│   ├── iimportdept.pbl (4,447 KB) ← Import department data (ใหญ่มาก)
│   ├── isavbofc.exe (46 KB)
│   ├── isavbofc.pbt            ← Build target
│   ├── isavbofc.ini            ← DB config (branch 077001/localhost)
│   ├── iBank.ini               ← Bank payment config
│   └── isavbofc_mig.log (0 bytes) ← Migration log (empty)
├── sBaseCom/                ← Base/infrastructure services
│   ├── cmappcom.pbl (576 KB)   ← Application common
│   ├── cmcoinit.pbl (1,461 KB) ← Cooperative initialization
│   ├── cmcomsrv.pbl (264 KB)   ← Common service
│   ├── cmconfig.pbl (1,545 KB) ← Configuration (ไม่มี .pbd)
│   ├── cmoutsrv.pbl (196 KB)   ← Output/export service
│   ├── cmprnsrv.pbl (162 KB)   ← Print service
│   ├── cmrepsrv.pbl (374 KB)   ← Report service
│   ├── cmsqlsrv.pbl (231 KB)   ← SQL service (ไม่มี .pbd)
│   └── cmtdtsrv.pbl (409 KB)   ← Trade/transaction date service
├── sBussCom/                ← Business services
│   ├── cmsrv_dept.pbl (901 KB) ← Department service
│   ├── cmsrv_fin.pbl (2,140 KB) ← Finance service (ใหญ่สุดใน sBussCom)
│   ├── cmsrv_loan.pbl (1,135 KB) ← Loan service
│   ├── cmsrv_mbshr.pbl (200 KB) ← Member share service
│   └── pccomloan.pbl (1,254 KB) ← Common loan library
├── PFC105/                  ← PowerBuilder Foundation Class 10.5
│   ├── pfcapsrv.pbl (6,428 KB) ← Application service (ใหญ่สุด)
│   ├── pfcdwsrv.pbl (4,758 KB) ← DataWindow service
│   ├── pfcmain.pbl (5,497 KB)  ← Main PFC framework
│   ├── pfcutil.pbl (2,198 KB)  ← Utilities
│   ├── pfcwnsrv.pbl (1,371 KB) ← Window service
│   ├── pfeapsrv.pbl (330 KB)   ← Extended app service
│   ├── pfedwsrv.pbl (211 KB)   ← Extended DW service
│   ├── pfemain.pbl (348 KB)    ← Extended main
│   ├── pfeutil.pbl (318 KB)    ← Extended utilities
│   ├── pfewnsrv.pbl (278 KB)   ← Extended window service
│   └── SERVICE.PBL (555 KB)
├── pbpro/                   ← Shared PBProcess libs (copy)
│   ├── pbprocess.pbl (38 KB)
│   ├── pccommon.pbl (2,105 KB)
│   ├── pcdeposit.pbl (15,948 KB) ← ใหญ่กว่า PBProcess version!
│   └── rpt_report_dept.pbl (1,597 KB)
├── Image/                   ← UI resources (BMP/GIF/JPG icons)
├── loan_keep.pbl (483 KB)   ← ไฟล์ที่ root (loan+keeping combined?)
├── isavbofc.ini             ← Main DB config (branch 057001/192.198.1.171)
├── isavbofc.pbw             ← Workspace
├── amscwins_amtksb.txt      ← ATM/Bank back office config (UTF-16)
└── [PB Runtime DLLs]        ← PBVM125.DLL, PBSHR125.DLL, etc.
```

### 2.2 iSavBOfc LibList (จาก iBank/isavbofc.pbt)

```
isavbofc.pbl;ibanktrn.pbl;chack_stmbal.pbl;iimportdept.pbl;
..\pbpro\pbprocess.pbl;..\pbpro\pccommon.pbl;
..\sBaseCom\cmappcom.pbl;..\sBaseCom\cmcoinit.pbl;
..\sBaseCom\cmcomsrv.pbl;..\sBaseCom\cmoutsrv.pbl;
..\sBaseCom\cmprnsrv.pbl;..\sBaseCom\cmrepsrv.pbl;
..\sBaseCom\cmtdtsrv.pbl;
..\sBussCom\pccomloan.pbl;
..\PFC105\pfcapsrv.pbl;..\PFC105\pfcdwsrv.pbl;
..\PFC105\pfcmain.pbl;..\PFC105\pfcutil.pbl;..\PFC105\pfcwnsrv.pbl;
..\PFC105\pfeapsrv.pbl;..\PFC105\pfedwsrv.pbl;
..\PFC105\pfemain.pbl;..\PFC105\pfeutil.pbl;..\PFC105\pfewnsrv.pbl;
..\PFC105\SERVICE.PBL
```

**รวม 26 libraries** ที่ iSavBOfc ใช้งาน

### 2.3 Oracle Database Connection (จาก isavbofc.ini — root)

```ini
[Database]
DBMS=ORA Oracle
LogId=iscodoaeuat
LogPassword=iscodoaeuat
ServerName=192.198.1.171/gcoop
DbParm="NLS_Charset='TH8TISASCII'"
AutoCommit=false

[rfscold]      ; Legacy/cold storage
LogId=dbo
ServerName=192.198.1.201/saving

[rfscold1]
LogId=dbo
ServerName=192.198.1.171/dbo
```

**Branch:** `057001` (production), `077001` (iBank dev/test)

### 2.4 Oracle Connection (iBank/isavbofc.ini — dev)

```ini
[Database]    ; Development
LogId=iscoaero
ServerName=localhost/gcoop

[Database1]   ; Oracle XE
LogId=iscoaero  
ServerName=localhost/XE

[rfscold2]
LogId=dbo
ServerName=192.198.1.171/dbo
```

### 2.5 iBank.ini — Bank Payment Configuration

ไฟล์ `/root/gcoop_hermes/mcc/iSavBOfc/iBank/iBank.ini` (Windows-874 encoded):

```ini
[Default]
company_bank=006    ; KTB เป็น default bank
format_type=IP

; รองรับธนาคารหลายแห่ง:
; [002-DC] BBL (Bangkok Bank) Direct Credit
; [004-DC] K-Bank Direct Credit  
; [006-DC] KTB Direct Credit (format: FIXED-POST, .trf)
; [006-IP] KTB Internet Payment (format: FIXED-POST, .DAT)
; [014-DC] SCB Direct Credit (company_code=tpds408)
; [017-MD] Citibank Media Clearing
; [022-DC] PEA (การไฟฟ้า) Direct Credit
; [025-SM] BAAC Same-day
; [034-DC] TKS (BAAC) Direct Credit (save_path=A:\)
```

ยืนยัน: iSavBOfc รองรับ **9 ธนาคาร** สำหรับการโอนเงินเดือน/สวัสดิการผ่าน Direct Credit

### 2.6 amscwins_amtksb.txt (ATM Back Office)

ไฟล์ UTF-16 ที่ root iSavBOfc — มีข้อมูล:
- `backoffice` → `BO000000005`
- `w_sheet_atm_bay_post` — งานฝ่าย ATM ธนาคาร (เชื่อมต่อระบบ Bay Bank/Krungsri ATM)
- Process ID: `031001`

---

## 3. PBReport125 — PowerBuilder Reporting Engine

### 3.1 รายการ Library

| Library | ขนาด .pbl | บทบาท |
|---|---|---|
| `pbreport.pbl` | 118 KB | Main report application entry |
| `prcommon.pbl` | 2,105 KB | Common report utilities |
| `reportdepositmcc.pbl` | **4,460 KB** | รายงานเงินฝาก MCC (ใหญ่สุด) |
| `reportfinancemcc.pbl` | **2,842 KB** | รายงานการเงิน MCC |
| `reportmbshr_mcc.pbl` | **2,200 KB** | รายงานหุ้นสมาชิก MCC |
| `reportshrlon.pbl` | 1,073 KB | รายงานสินเชื่อหุ้น |
| `reportmbshare.pbl` | 209 KB | รายงานหุ้น (base) |

**รวม .pbd ที่ reference จาก CORE path (จาก pbreport.pbt):**

| Category | Libraries (.pbd) |
|---|---|
| Account | `reportaccount`, `accountservice` |
| Deposit | `reportdeposit`, `reportdeposit_ole`, `reportdepositmcc` |
| Finance | `reportfinance`, `reportfinance_ole`, `reportfinancemcc` |
| Dividend | `reportdiv`, `reportdiv_ole` |
| Keeping | `reportkeeping`, `reportkeeping_ole` |
| Loan | `prcomloan`, `reportloanassist` |
| Member/Share | `reportmbshr`, `reportmbshr_ole`, `reportmbshr_mcc`, `reportshrlon`, `reportshrlon_ole`, `rptstdmbshr`, `rptstdshrlon` |
| Insurance | `reportinsure` |
| Investment | `reportinvestments` |
| Admin/HR | `reportadmin`, `reportassist`, `reporthr` |
| Legal | `reportlawsys` |
| Welfare | `reportwalfare` |
| Others | `reportcloud`, `reportcmd`, `dw2xls12.x` |

**รวมทั้งสิ้น: ~30 report libraries** ใน CORE path

### 3.2 RunReport.xml — Scheduled Task

จาก `/root/gcoop_hermes/mcc/GCOOP/PBReport125/RunReport.xml` (UTF-16):

```xml
Task version="1.2" (Windows Task Scheduler)
Author: COOPWEB\Administrator (2015-12-16)
Trigger: CalendarTrigger — ทุก 1 นาที (PT1M), ทุกวัน (DaysInterval=1)
ExecutionTimeLimit: P3D (3 วัน)
MultipleInstances: IgnoreNew
Actions:
  1. runreport.bat  (working dir: C:\GCOOP_ALL\CORE\GCOOP\PBReport125\)
  2. runreportdel.bat
```

**runreport.bat:** `for %%i in (%SystemRoot%\Temp\*.bat) do %%i`  
**runreportdel.bat:** `del %SystemRoot%\Temp\*.bat`

Pattern: รายงานถูก queue เป็น .bat ใน `%TEMP%` → RunReport.xml (Task Scheduler) run ทุก 1 นาที → execute แล้ว cleanup

---

## 4. Oracle Integration

### 4.1 Connection Profiles (ทั้งระบบ)

| Profile | Server | Schema/LogId | ใช้ใน | หมายเหตุ |
|---|---|---|---|---|
| Main | `192.198.1.171/gcoop` | `iscodoaeuat` | PBProcess, iSavBOfc (prod) | Production DB |
| Main Dev | `localhost/gcoop` | `iscoaero` | iSavBOfc (dev) | Local dev |
| Oracle XE | `localhost/XE` | `iscoaero` | iSavBOfc (dev) | Oracle Express |
| Legacy | `192.198.1.201/saving` | `dbo` | iSavBOfc rfscold | เก่า/cold storage |
| DBO | `192.198.1.171/dbo` | `dbo` | iSavBOfc rfscold1/2 | อีก schema |

**Charset:** `TH8TISASCII` (Thai 8-bit TIS-620) ทุก connection  
**AutoCommit:** `false` ทุก connection — ใช้ manual transaction control

### 4.2 PB Runtime Environment (iSavBOfc Runtime DLLs)

iSavBOfc มี PowerBuilder 12.5 runtime ครบชุด ได้แก่:
- `PBVM125.DLL` (4.9 MB) — PB Virtual Machine
- `PBSHR125.DLL` (3.0 MB) — PB Shared library
- `PBDWE125.DLL` / `PBDWM125.DLL` (4.0 MB each) — DataWindow engine
- `PBORC125.DLL` — Oracle connector
- `pbora125.dll` + `pbo10125.dll` + `pbo90125.dll` — Oracle 10g/9i drivers
- `PBWPS125.DLL` / `PBWZP125.DLL` — Web/WPS services

---

## 5. Batch Processing Workflow (สรุปจาก module names + function signatures)

### 5.1 งาน Daily

```
1. pccommon.pbl: Validate/initialize daily parameters
2. pcdeposit.pbl: คำนวณดอกเบี้ยเงินฝากประจำวัน
3. pcloan.pbl: 
   - n_cst_keeping_process.of_processloan()     ← process loan transactions
   - n_cst_keeping_process.of_processloanocalint() ← calculate loan interest
   - n_cst_proc_slmnyret.of_postslip_payinloan() ← post pay-in slip
4. pckeeping.pbl: บัญชีรับจ่ายประจำวัน
   - n_cst_keeping_process.of_rcvprocess()       ← receive/batch process
5. pcfinance.pbl: Financial summary/reconciliation
```

### 5.2 งาน Month-End

```
1. pcdeposit.pbl: สรุปเงินฝากสิ้นเดือน
2. pcloan.pbl: 
   - n_cst_proc_slmnyret.of_poststm_contract()  ← post loan statement
   - n_cst_proc_slmnyret.of_setlistcontnewlrtmcc() ← set new contract list
3. pcdivavg.pbl: คำนวณเฉลี่ยคืน/เงินปันผลระหว่างกาล
4. pcinsurance.pbl: Insurance premium processing
5. pcfinance.pbl: งบการเงินสิ้นเดือน
6. pcmbshr.pbl: ยอดหุ้นสมาชิกสิ้นเดือน
```

### 5.3 งาน Salary Payment (iSavBOfc)

```
1. ibanktrn.pbl: ดึงข้อมูลเงินเดือน/สวัสดิการสมาชิก
2. cmsrv_fin.pbl: คำนวณยอดหักสหกรณ์
3. pccomloan.pbl: หักชำระสินเชื่อ
4. iBank.ini: เลือกธนาคาร (KTB/BBL/KBank/SCB ฯลฯ)
5. n_cst_proc_slmnyret.of_postpayinslippx_mcc() ← post salary payment slip
6. iimportdept.pbl: Import department/payroll data
```

### 5.4 งาน Reporting (PBReport125)

```
1. Web system วาง .bat ไว้ใน %TEMP%
2. RunReport.xml (Task Scheduler) → runreport.bat ทุก 1 นาที
3. pbreport.exe execute รายงาน
4. reportdepositmcc / reportfinancemcc / reportmbshr_mcc
5. runreportdel.bat ล้าง .bat ที่ execute แล้ว
```

---

## 6. Key Findings

1. **pckeeping.pbl (14.4 MB)** คือ library ใหญ่สุด — ระบบบัญชีสหกรณ์ซับซ้อนมาก
2. **pcdeposit.pbl (11.9 MB / pbpro version 15.9 MB)** — เงินฝากเป็นหัวใจหลัก
3. **iSavBOfc** ใช้ PFC 10.5 framework ครบ (pfcapsrv 6.4 MB) — enterprise-grade architecture
4. **Multi-bank support** — 9 ธนาคารในระบบ Direct Credit ผ่าน iBank.ini
5. **Thai charset TH8TISASCII** ทุก Oracle connection — ไม่ใช่ UTF-8
6. **Report scheduling** ผ่าน Windows Task Scheduler (ทุก 1 นาที) — report-on-demand pattern
7. **3 Oracle environments**: Production (192.198.1.171), Legacy (192.198.1.201), Dev (localhost)
8. **Branch control code**: 057001 (prod) / 077001 (dev iBank)
9. **CONVERT_MCC/mcc_pipeline** — มี pipeline สำหรับ data migration ของ MCC โดยเฉพาะ
