# MCC Session 4: Integration & Summary Analysis
# บทสรุปการวิเคราะห์ครบถ้วน — ระบบสหกรณ์ GCOOP MCC

**วันที่วิเคราะห์:** 2026-09-14  
**ขอบเขต:** สรุปรวม Sessions 1-3 พร้อม Integration Architecture  
**Sources:**
- Session 1: `mcc-session1-web-analysis.md` — 24 โมดูล, 436 ASPX
- Session 2: `mcc-session2-powerbuilder-analysis.md` — 11 PBL, iSavBOfc, 9 banks
- Session 3: `mcc-session3-infrastructure-analysis.md` — 676 jrxml, 39 PLSQL, deployment scripts

---

## 1. สถาปัตยกรรม Integration ภาพรวม (Web ↔ PowerBuilder ↔ Oracle ↔ iReport)

### 1.1 แผนภาพสถาปัตยกรรมหลัก

```
┌─────────────────────────────────────────────────────────────────────────┐
│                    GCOOP MCC — System Architecture                       │
├─────────────────────────────────────────────────────────────────────────┤
│                                                                          │
│  ┌──────────────────────────────────────────────────────────────┐       │
│  │               Web Layer (ASP.NET 4.0)                        │       │
│  │   Frame.Master → PageWebSheet → DataSets → ExecuteDataSource │       │
│  │   24 โมดูล, 436 ASPX, AjaxPro, JsPostBack pattern          │       │
│  │   Modules: hr(81), rdc(57), loan(38), mbshr(35), walfare(33) │       │
│  └──────────────────────┬────────────────────┬─────────────────┘       │
│                          │                    │                          │
│              ┌───────────▼──────────┐  ┌─────▼─────────────────────┐  │
│              │  WCF Services Layer  │  │  Oracle Direct (x86)       │  │
│              │  WcfService125/*.svc │  │  System.Data.OracleClient  │  │
│              │  n_loan, n_common,   │  │  10 connection strings     │  │
│              │  n_deposit, n_keeping│  │  TH8TISASCII charset       │  │
│              │  n_finance, n_mbshr  │  └────────────────────────────┘  │
│              │  n_account, n_divavg │                                   │
│              │  n_investment        │                                   │
│              │  ATMcoreWeb.svc      │                                   │
│              └───────────┬──────────┘                                   │
│                          │                                               │
│  ┌───────────────────────▼──────────────────────────────────────┐       │
│  │          PowerBuilder Batch Layer (PBProcess)                 │       │
│  │   pbprocess.pbl (entry) → pccommon.pbl → domain libs         │       │
│  │   pckeeping(14.4MB), pcdeposit(11.9MB), pcloan(9MB),        │       │
│  │   pcfinance(8.5MB), pcdivavg(3.9MB), pcinsurance(1.7MB)     │       │
│  └───────────────────────┬──────────────────────────────────────┘       │
│                          │                                               │
│  ┌───────────────────────▼──────────────────────────────────────┐       │
│  │              Oracle Database                                   │       │
│  │   192.168.10.200/gcoop (Main MCC) | 43.229.79.117 (Cloud)   │       │
│  │   PLSQL: 17 Functions + 7 Packages + 8 Types                 │       │
│  │   Java stored proc: WCFCALLING (HTTP POST from Oracle)        │       │
│  └───────────────────────┬──────────────────────────────────────┘       │
│                          │                                               │
│  ┌───────────────────────▼──────────────────────────────────────┐       │
│  │              iReport Engine (Java)                             │       │
│  │   676 .jrxml templates | 117 CriteriaIReport schemas         │       │
│  │   Queue: Web → %TEMP%/*.bat → Task Scheduler (1 min)         │       │
│  │   Output: C:\GCOOP\Saving\Report\PDF\                        │       │
│  └──────────────────────────────────────────────────────────────┘       │
│                                                                          │
└─────────────────────────────────────────────────────────────────────────┘
```

### 1.2 สาม Integration Patterns หลัก

**Pattern A: Web → WCF → PowerBuilder → Oracle**
```
ASP.NET Page (ws_lon_apvloan.aspx.cs)
  └─ wcf.NLoan.of_gennewcontractno(state.SsWsPass, req_coopid, loantype_code)
     └─ n_loan.svc (WcfService125)
        └─ PowerBuilder NVO: n_cst_process_service.of_pbprocess(as_procid)
           └─ Oracle INSERT/UPDATE (pcloan.pbl transaction)
```

**Pattern B: Web → Oracle Direct**
```
ASP.NET Page (ws_hr_payroll_process.aspx.cs)
  └─ WebUtil.QuerySdt("SELECT count(post_status) FROM hrpayroll WHERE ...")
     └─ Oracle (System.Data.OracleClient x86)
        └─ Return Sdt (Simple DataTable)
```

**Pattern C: Report Queue (Task Scheduler)**
```
Web App → สร้าง .bat ใน %TEMP%
  └─ Windows Task Scheduler (RunReport.xml) ทำงานทุก 1 นาที
     └─ runreport.bat: execute .bat files ทั้งหมด
        └─ pbreport.exe → reportdepositmcc.pbl / reportfinancemcc.pbl / ...
           └─ iReport Engine → .jrxml → PDF → C:\GCOOP\Saving\Report\PDF\
              └─ runreportdel.bat: ล้าง .bat ที่ execute แล้ว
```

### 1.3 Desktop Integration (iSavBOfc)

```
iSavBOfc (PowerBuilder 12.5 + PFC 10.5)
  ├─ ibanktrn.pbl (Transaction engine, 2.6 MB)
  ├─ iimportdept.pbl (Import department, 4.4 MB)
  ├─ cmsrv_fin.pbl (Finance service, 2.1 MB)
  ├─ cmsrv_loan.pbl (Loan service, 1.1 MB)
  └─ Oracle (192.198.1.171/gcoop — TH8TISASCII)
     └─ iBank.ini → 9 ธนาคาร Direct Credit
```

---

## 2. End-to-End Business Flows

### 2.1 กระบวนการสมัครสมาชิก (Member Join)

```
[1] ยื่นใบสมัคร
    └─ ws_mem_apvappl.aspx (mbshr module)
       └─ บันทึกข้อมูลสมาชิกผ่าน ExecuteDataSource → Oracle

[2] อนุมัติสมาชิก
    └─ ws_mem_apvappl.aspx → อนุมัติโดยเจ้าหน้าที่

[3] กำหนดกลุ่มสมาชิก
    └─ ws_mem_ucfmembgroup.aspx → ตั้งค่ากลุ่ม (ใช้ใน Payroll deduction)

[4] ออกบัตรสมาชิก (ถ้ามี)
    └─ winLKE (C# + Msre.ocx) → เขียน magnetic card
    └─ ws_ixp_mb_register_cardPerson.aspx (IXP module) → ลงทะเบียนบัตร

[5] ข้อมูลสมาชิกครบถ้วน
    └─ ws_mem_memberdetail.aspx
       └─ 34+ DataSets: dsMain, dsDetail, dsLoan, dsShare, dsKeep, dsRdc,
                         dsLinkATM, dsWalfare, dsInsurance, dsFamily ฯลฯ
```

### 2.2 กระบวนการฝากเงิน (Deposit)

```
[1] เปิดบัญชีฝาก
    └─ dw_slipopen_name.jrxml (iReport) → ใบเปิดบัญชี

[2] รับฝากเงิน (ประจำวัน)
    └─ iSavBOfc: ibanktrn.pbl → บันทึก transaction

[3] ประมวลผลดอกเบี้ย (รายวัน/รายเดือน)
    └─ PBProcess: pcdeposit.pbl (11.9 MB)
       └─ n_cst_keeping_process.of_rcvprocess() → คำนวณดอกเบี้ย

[4] ประมวลผลเงินฝากประจำ (ws_kep_process_keep)
    └─ proc_type 2-5 แยกตาม: MemberGroup / MemberNo / Category / DebtAgent
    └─ PBProcess: pckeeping.pbl (14.4 MB) → บัญชีรับจ่าย

[5] อัพเดทสมุดบัญชี
    └─ winUPBOOK (VB.NET) → GetRule_BookPage() ← DPDEPTCONSTANT
       └─ PEM.ocx → Passbook Printer Hardware
```

### 2.3 กระบวนการเงินกู้ (Loan)

```
[1] ยื่นคำขอกู้
    └─ ws_lon_reqloan.aspx
       └─ DataSets: dsMain, dsDetail, dsLoanClr, dsLoanColl, dsLoanCreditScoll, dsLoanIns, dsLoanExpense ฯลฯ
       └─ JsPostBack: ReCalSalary, CalPayMonth, ReCalPeriodPay, InsertRowColl, ChgInsPlan
       └─ LOANREQUEST_DOCNO = "AUTO" → Oracle auto-generate

[2] Credit Scoring
    └─ ws_lon_ucfloancreditscoring.aspx → กำหนดค่า credit score
    └─ ws_elc_score_committee.aspx → คะแนนคณะกรรมการ

[3] อนุมัติสินเชื่อ
    └─ ws_lon_apvloan.aspx
       └─ wcf.NLoan.of_gennewcontractno(state.SsWsPass, req_coopid, loantype_code)
          └─ WCF → PBProcess → Oracle: ออกเลขสัญญา
       └─ CoopId logic: state.SsCoopControl == "003001" || "074001" → order_type = "3"

[4] บันทึกสัญญา/รับเงินกู้
    └─ ws_lon_apvpromise.aspx → บันทึกสัญญา
    └─ ws_lon_rcvloan.aspx → รับเงิน
    └─ ws_lon_post_loan_receive.aspx → Post รับสินเชื่อ

[5] ชำระงวด (รายเดือน)
    └─ ws_lon_payment.aspx
       └─ WebUtil.UseMembUptoToken() → ล็อคสมาชิก (Token-based locking)
       └─ SLIPTYPE_CODE = "PX" (slip ประเภทชำระ)
       └─ DataSets: dsMain, dsDetailShare, dsDetailLoan, dsDetailEtc, dsDetailMoneytype
    └─ PBProcess: pcloan.pbl
       └─ n_cst_keeping_process.of_processloan()
       └─ n_cst_keeping_process.of_processloanocalint() ← คำนวณดอกเบี้ย
       └─ n_cst_proc_slmnyret.of_postslip_payinloan()
       └─ n_cst_proc_slmnyret.of_postpayinslippx_mcc()  ← MCC-specific

[6] ตัดเงินเดือน (Payroll Deduction)
    └─ iSavBOfc: ibanktrn.pbl + cmsrv_fin.pbl + pccomloan.pbl
       └─ iBank.ini: เลือกธนาคาร (KTB default, รองรับ 9 ธนาคาร)
       └─ Export Direct Credit file → ธนาคาร

[7] ติดตามหนี้/NPL
    └─ Oracle Package: n_pk_lnnpl → ติดตาม NPL
    └─ ws_lon_paintloan.aspx → ดูยอดเงินกู้
```

### 2.4 กระบวนการชำระหนี้ (Repayment)

```
[1] ชำระปกติ
    └─ ws_lon_payment.aspx → รับชำระ / ตัดเงินเดือน

[2] ขอพักชำระหนี้
    └─ ws_lon_reqpauseloan.aspx → ยื่นขอ
    └─ ws_lon_apvreqpauseloan.aspx → อนุมัติ

[3] ชำระคืนเงิน (ประมวลผล)
    └─ ws_lon_proc_moneyreturn.aspx → ประมวลผลคืนเงิน
    └─ ws_lon_proc_paymoneyreturn.aspx → จ่ายคืนเงิน
    └─ PBProcess: n_cst_proc_slmnyret.of_poststm_contract() ← post loan statement
    └─ PBProcess: n_cst_proc_slmnyret.of_setlistcontnewlrtmcc() ← MCC-specific

[4] สิ้นสุดสัญญา
    └─ Oracle: Oracle Package n_pk_doccontrol → ควบคุมเอกสาร
    └─ RDC: ws_rdc_documentsafe.aspx → เก็บเอกสารสัญญาในตู้นิรภัย
```

### 2.5 กระบวนการปันผล/เฉลี่ยคืน (Dividend)

```
[1] ประมวลผลเฉลี่ยคืน (รายงวด)
    └─ PBProcess: pcdivavg.pbl (3.9 MB) → คำนวณเงินปันผล/เฉลี่ยคืน

[2] iReport รายงาน
    └─ ir_didurt_* (20 templates) — รายงานเงินปันผล
    └─ ir_diinvt_* (18 templates) — รายงานเงินปันผลการลงทุน

[3] จ่ายเงิน
    └─ iSavBOfc: ibanktrn.pbl → จ่ายเงินปันผล
    └─ iBank.ini → Direct Credit ผ่านธนาคาร
```

### 2.6 กระบวนการ Payroll (พนักงาน)

```
[1] ประมวลผลเงินเดือน
    └─ ws_hr_payroll_process.aspx
       └─ SELECT count(post_status) FROM hrpayroll WHERE coop_id = '...' AND payroll_period = '...'
       └─ SELECT firstworkdate, lastworkdate FROM amworkcalendar WHERE year = '...' AND month = '...'
       └─ คำนวณปี พ.ศ.: Convert.ToDecimal(DateTime.Now.Year) + 543

[2] โอนข้อมูลไปการเงิน
    └─ ws_hr_trantofin.aspx → โอนข้อมูล HR → การเงิน

[3] จ่ายเงินเดือน
    └─ iSavBOfc: iimportdept.pbl → import department payroll
    └─ iBank.ini (9 ธนาคาร) → Direct Credit / Internet Payment

[4] หักสหกรณ์
    └─ cmsrv_loan.pbl + pccomloan.pbl → หักชำระสินเชื่อจากเงินเดือน
```

---

## 3. Integration Points กับระบบภายนอก

### 3.1 ATM Integration

```
ช่องทาง ATM:
├─ WCF Service: ATMcoreWeb.svc (localhost/ATM/CoreCoop/ATMcoopServiceWeb/)
├─ Web: dsLinkATM ใน ws_mem_memberdetail.aspx — แสดงข้อมูล ATM สมาชิก
├─ BackOffice: amscwins_amtksb.txt → w_sheet_atm_bay_post
│   └─ Process ID: 031001 — เชื่อมต่อ Bay Bank/Krungsri ATM
└─ ACCID ATM: dsAccidAtm DataSet ใน ws_mem_memberdetail
```

### 3.2 ธนาคาร (9 ธนาคาร Direct Credit)

```
iBank.ini — การโอนเงินเดือน/สวัสดิการผ่าน Direct Credit:
├─ [002-DC] BBL (Bangkok Bank) Direct Credit
├─ [004-DC] K-Bank Direct Credit
├─ [006-DC] KTB (กรุงไทย) Direct Credit — format: FIXED-POST, .trf
├─ [006-IP] KTB Internet Payment — format: FIXED-POST, .DAT (default company_bank=006)
├─ [014-DC] SCB Direct Credit — company_code=tpds408
├─ [017-MD] Citibank Media Clearing
├─ [022-DC] PEA (การไฟฟ้า) Direct Credit
├─ [025-SM] BAAC Same-day
└─ [034-DC] TKS (BAAC) Direct Credit — save_path=A:\

Export files:
├─ ws_fin_exptobank.aspx (IXP module) — Export การเงินไปธนาคาร
├─ ws_lon_exptobank.aspx — Export สินเชื่อไปธนาคาร
└─ ws_fin_exptobank_slipno.aspx — Export ไปธนาคาร (slip-based)
```

### 3.3 Smart Card

```
Smart Card Integration:
└─ ws_mem_memberdetail.aspx → PostSmartCard event
   └─ อ่านบัตรอัจฉริยะสมาชิก (ข้อมูลสมาชิกจากบัตร)
```

### 3.4 Passbook Printer (เครื่องพิมพ์สมุดบัญชี)

```
print.formsets.xml — Passbook Printer Stations:
├─ FULLPRINT: 10.20.241.35 (SERVER)
├─ Passbook15-17: 192.168.99.15-17 (Stations)
├─ Passbook20: 192.168.99.20
├─ Passbook25: 192.168.99.25
└─ printloan: 192.168.99.11 (เครื่องพิมพ์กู้)

winUPBOOK (VB.NET):
├─ PEM.ocx → ควบคุม PEMS Passbook Printer Hardware
├─ GetRule_BookPage() ← DPDEPTCONSTANT (Oracle):
│   Pagetotal_Pb, Lineperpage_Pb, Linenomidpage_Pb, Nlinenextmid_Pb
└─ ClickOnce Deploy: http://[server]/pbUpbook/ (versions 1.0.0.5 → 1.0.0.9)
```

### 3.5 Magnetic Card Reader

```
winLKE (C#):
├─ Msre.ocx → MSRE Magnetic Stripe Reader Encoder
├─ winLKE.exe ที่ C:\GCOOP_ALL\AERO\winLKE\Drivers\winLKEAppDevices\
├─ Win32 P/Invoke: ShowWindow, MoveWindow, SetWindowPos, FindWindow
└─ ClickOnce Deploy: version 1.0.0.12 (.NET 4.6.1)
```

### 3.6 ระบบ Aero (สหกรณ์ภายนอก)

```
Aero Cooperative Integration:
├─ Oracle Connection: 203.154.158.107:1521/gcoop (iscoaero, iscoaerodep)
├─ ws_wc_approve_newmemb_aero.aspx — สมาชิกใหม่ Aero
├─ ws_wc_walfare_req_edit_aero.aspx — คำขอสวัสดิการ Aero
└─ ws_kep_exportfile_aero.aspx — Export ไฟล์ Aero
```

### 3.7 SSH/SFTP (ระบบภายนอก)

```
Renci.SshNet (316 .cs files embedded):
├─ SftpClient — โอนไฟล์ backup ผ่าน SFTP
├─ SshClient — execute commands บน remote server
├─ ForwardedPort — port forwarding
└─ PrivateKeyAuthenticationMethod — SSH key authentication
```

---

## 4. Deployment Architecture

### 4.1 IIS 32-bit Application Pools

```
Deploy-IIS-Extend.v4.bat สร้าง:

App Pool       Runtime  32-bit  Identity          หน้าที่
─────────────────────────────────────────────────────────
MCC.saving     v4.0     ✅      SpecificUser       ASP.NET Web App
MCC.wcf        v2.0     ✅      default            WCF Services
MCC.report     v2.0     ✅      NetworkService     Report Engine (Classic Pipeline)

ทุก Pool: enable32BitAppOnWin64=true, Idle Timeout=30 min

Virtual Applications:
├─ /MCC                        → %ROOT_DIR%\GCOOP_ALL\MCC
├─ /MCC/GCOOP/Saving           → MCC.saving (ASP.NET 4.0)
├─ /MCC/GCOOP/WcfService       → MCC.wcf (WCF 2.0)
└─ /MCC/GCOOP/WebServiceReport → MCC.report (Classic Pipeline)

MIME Types เพิ่ม:
├─ .pbd → application/pbd-stream (PowerBuilder compiled library)
└─ .trf → text/plain (KTB Direct Credit transfer file)
```

### 4.2 Build Process (Build.bat)

```
Build.bat ลำดับการ build:

1. wmic process where (name="Javaw.exe") delete    ← Kill iReport Java
2. PATH = %WINDIR%\Microsoft.NET\Framework\v4.0.30319
3. NET STOP W3SVC                                   ← Stop IIS
4. msbuild.exe GCOOP_MCC.sln /t:Clean              ← Clean solution
5. msbuild.exe Extend_Saving.csproj /t:rebuild     ← Build Web App
6. NET START W3SVC                                  ← Start IIS

Active Project: Extend_Saving.csproj
(Other projects commented out: DataLibrary, GcoopServiceCs, WcfService, 
 WebServiceReport, CoreSavingLibrary, SingleSignOn, Saving, 
 Extend_WcfService, Extend_WebServiceReport)
```

### 4.3 ClickOnce Deployment (Client Tools)

```
winUPBOOK (Passbook Printer Tool):
├─ URL: http://[server]/pbUpbook/
├─ Versions: 1.0.0.5 → 1.0.0.9 (5 versions)
├─ Target: .NET 4.0, x86
└─ Auto-update: ClickOnce ตรวจสอบ version ทุก launch

winLKE (Magnetic Card Reader):
├─ Version: 1.0.0.12
├─ Target: .NET 4.6.1
└─ Deployment: ClickOnce ผ่าน IIS
```

### 4.4 Windows Task Scheduler (Report Queue)

```
RunReport.xml (Task Scheduler):
├─ Author: COOPWEB\Administrator (2015-12-16)
├─ Trigger: CalendarTrigger — ทุก 1 นาที (PT1M), ทุกวัน
├─ Execution Limit: P3D (3 วัน)
├─ MultipleInstances: IgnoreNew
└─ Actions:
   1. runreport.bat: for %%i in (%SystemRoot%\Temp\*.bat) do %%i
   2. runreportdel.bat: del %SystemRoot%\Temp\*.bat
```

### 4.5 Directory Structure บน Server

```
C:\GCOOP_ALL\
├─ MCC\
│   └─ GCOOP\
│       ├─ Saving\            (ASP.NET 4.0 Web App)
│       │   ├─ Applications\  (24 modules)
│       │   ├─ CriteriaIReport\ (117 schemas)
│       │   ├─ DataWindow\    (27 folders)
│       │   ├─ IREPORTFILE\
│       │   ├─ SlipApp/, SlipPB\
│       │   └─ Web.config
│       ├─ WcfService125\     (WCF Services)
│       │   └─ n_*.svc        (10 services)
│       ├─ PBProcess\         (Batch Engine)
│       │   └─ pbprocess.exe + 11 .pbl libs
│       ├─ PBReport125\       (Report Engine)
│       │   └─ pbreport.exe + RunReport.xml
│       ├─ iReport\
│       │   └─ Reports\       (676 .jrxml)
│       ├─ XMLConfig\         (Configuration)
│       │   └─ *.xml          (connection_string, wcf_detail, print.*)
│       └─ PLSQL\             (Database Scripts)
│           └─ PLImporter.exe + 39 .pls
├─ CORE\GCOOP\PBProcess\      (Core/Shared PBProcess)
├─ AERO\
│   └─ winUPBOOK\             (Passbook Printer)
│   └─ winLKE\                (Magnetic Card)
└─ PBSLIP_MCC\                (SlipPB local cache)

C:\GCOOP\Saving\Report\PDF\   (PDF output)
```

### 4.6 Database Environments

```
Oracle Connection Profiles:

Production (Web.config):
├─ ConnectionString  : 192.168.10.200/gcoop — iscorfd (Main MCC)
├─ ConnectionString3 : 172.17.30.45/gcoop  — iscobtg (BTG สาขา)
├─ ConnectionString5 : 43.229.79.117/gcoop — iscomrc (MRC)
└─ ConnectionStringAERO: 203.154.158.107:1521/gcoop — iscoaero

XMLConfig Profiles (server.connection_string.xml):
├─ Default@Cloud: 43.229.79.117/gcoop — iscomrc
├─ MCC@Siam: web.siamcoop.com/gcoop — iscomrc
├─ MCC@Dept: 43.229.79.117/gcoop — iscotestmrc
└─ เทสเรียกเก็บ10/68: 43.229.79.117/gcoop — iscomrckep

iSavBOfc:
├─ Production: 192.198.1.171/gcoop — iscodoaeuat (Branch 057001)
├─ Dev/Test: localhost/gcoop — iscoaero (Branch 077001)
└─ Legacy: 192.198.1.201/saving — dbo (rfscold)

ทุก connection: charset=TH8TISASCII, AutoCommit=false
```

---

## 5. เปรียบเทียบ MCC vs MHD

### 5.1 ขนาดและไฟล์

| มิติ | MCC | MHD | ต่าง |
|---|---|---|---|
| **Working copy** | 1.7 GB | ~1.5 GB | MCC +13% |
| **GCOOP dir** | 343 MB | ~300 MB | MCC ใหญ่กว่า |
| **Web files** | 6,721 (196 MB) | ~5,000 | MCC +26% |
| **ASPX files** | 436 | ~350 | MCC +24% |
| **Report templates (iReport)** | 676 jrxml (MCC dir) | ไม่ระบุ | MCC มีมากกว่า |
| **CriteriaIReport** | 117 schemas | ไม่ระบุ | — |
| **PB Core Libs (PBProcess)** | 11 libs (~54 MB) | 11 libs | **เหมือนกัน 100%** |
| **PLSQL** | 39 .pls files | ~35 | MCC มากกว่า |

### 5.2 Business Focus

| ด้าน | MCC | MHD |
|---|---|---|
| **โมดูลใหญ่สุด** | HR — 81 ASPX | Loan — 33+ ASPX (วิเคราะห์ครบ) |
| **จุดเน้น** | HR + RDC + Welfare + PM | Loan + Saving + Member |
| **Document Management** | RDC (57 ASPX) — ครบวงจร | ไม่มี RDC module ระดับนี้ |
| **Welfare** | walfare (33 ASPX) — holistic | พื้นฐาน |
| **Portfolio Mgmt** | pm (21 ASPX) | ไม่มี/น้อยกว่า |
| **Loan** | loan (38 ASPX) | loan เป็นหัวใจ |
| **กลุ่มลูกค้า** | สหกรณ์ขนาดใหญ่ มีพนักงาน | สหกรณ์ออมทรัพย์มาตรฐาน |

### 5.3 Technology Stack (เหมือนกัน)

| Layer | MCC | MHD |
|---|---|---|
| Web Framework | ASP.NET WebForms 4.0 | เหมือนกัน |
| Pattern | Frame.Master + PageWebSheet + JsPostBack | เหมือนกัน |
| WCF Services | WcfService125/n_*.svc (BasicHttpBinding) | เหมือนกัน |
| PowerBuilder | PBProcess 11 libs, iSavBOfc (PFC 10.5) | **เหมือนกัน** |
| Database | Oracle, TH8TISASCII, System.Data.OracleClient | เหมือนกัน |
| Report | iReport 5.0.4, .jrxml, Task Scheduler | เหมือนกัน |
| Desktop Tools | winUPBOOK + winLKE (ClickOnce) | เหมือนกัน |

### 5.4 ความแตกต่างหลัก

**MCC-Specific features (ไม่มีใน MHD):**
- `ws_mem_adjfamily_mcc.aspx` — จัดการครอบครัว MCC
- `ws_kep_fileimport_mcc.aspx` — Import Keeping พร้อม dialog พิเศษ
- `ws_acc_budget_setamt_mcc.aspx` — ตั้งงบประมาณ MCC
- `u_cri_coopid_memno_loancont_refcollno_mcc` — iReport criteria MCC
- CoopId logic: `003001` และ `074001` มี order_type = "3"
- `n_cst_proc_slmnyret.of_postpayinslippx_mcc()` — MCC-specific batch function
- `n_cst_proc_slmnyret.of_setlistcontnewlrtmcc()` — MCC-specific batch

**Shared Core (ใช้ร่วม 85%+):**
- WCF endpoints pattern (n_*.svc)
- PBProcess LibList เหมือนกัน
- Oracle PLSQL functions/packages
- iReport engine (Java, Task Scheduler)
- Deploy scripts (IIS 32-bit, ClickOnce)

---

## 6. Security Architecture

### 6.1 Single Sign-On (SSO)

```
Web.config SSO Configuration:
<authentication mode="Forms">
  <forms loginUrl="http://sav.icoopthai.co/CEN/GCOOP/SingleSignOn/Login.aspx"
         defaultUrl="http://sso.icoopthai.co/CEN/GCOOP/SingleSignOn/Default.aspx"
         domain="icoopthai.co"/>
</authentication>

- Domain: icoopthai.co (shared cookie across subdomains)
- Entry: sav.icoopthai.co — Login page
- Default: sso.icoopthai.co — After login
- sitePrefix: "CEN" (Mahidol University Saving and Credit Co-Operative)
```

### 6.2 Session และ Token Management

```
Frame.Master.cs Session Pattern:

WebStateFactory state:
├─ state.SsTokenId    — Token session สำหรับ lock
├─ state.SsUsername   — Username
├─ state.SsWorkDate   — Working date
├─ state.SsCoopControl — CoopId ปัจจุบัน
└─ state.SsCoopId     — สหกรณ์ที่ login

FrameContext = "t={tokenId}&d={encryptedConnectionString}"
└─ EncryptDecryptEngine.Encrypt(connectionString) — ส่ง URL parameter ทุกหน้า

Token-based Member Locking:
└─ WebUtil.UseMembUptoToken(state.SsTokenId, state.SsUsername, "")
   └─ ล็อค member record ขณะเจ้าหน้าที่กำลังแก้ไข (ป้องกัน concurrent edit)
```

### 6.3 WCF Authentication

```
WCF Service Call:
└─ wcf.NLoan.of_gennewcontractno(state.SsWsPass, req_coopid, loantype_code)
   └─ state.SsWsPass — WCF password (ส่งทุก call)
   └─ BasicHttpBinding — HTTP basic authentication

WCF Endpoint: localhost/CORE/GCOOP/WcfService125/
└─ ทำงานบน IIS Pool: MCC.wcf (v2.0, 32-bit)
```

### 6.4 Oracle Security

```
Oracle Profiles ตาม Environment:
├─ iscorfd  — Main production read/write user
├─ iscomrc  — MRC user
├─ iscoaero — Aero cooperative user
├─ iscobtg  — BTG branch user
└─ iscocmt  — CMT user

Kill Sessions Script (Alter_Oracle_Kill_Sessions_user_w3w.sql):
└─ ตรวจหา INACTIVE session > 15 นาที ที่ใช้ w3wp.exe
   └─ ALTER SYSTEM KILL SESSION ... IMMEDIATE
   └─ รองรับ Amazon RDS: rdsadmin.rdsadmin_util.kill()

SHA3 Encryption:
├─ ENC.java — Java SHA3 implementation
└─ FT_ENC.sql — Oracle PL/SQL function ใช้ encrypt ข้อมูลใน DB

Java WcfCalling (Security concern):
└─ WCFCALLING Oracle stored function → HTTP POST จาก Oracle
   └─ ใช้สำหรับ Oracle trigger เรียก WCF Web Service
```

### 6.5 Connection String Encryption

```
URL parameter: d={encryptedConnectionString}
└─ EncryptDecryptEngine.Encrypt() — encrypt connection string ก่อนส่ง URL
└─ แต่ละหน้ารับ FrameContext และ decrypt เพื่อเชื่อมต่อ Oracle
```

### 6.6 Network Security

```
Internal Network:
├─ WCF Services: localhost (internal IIS)
├─ XMLConfig WCF: 192.168.253.181 (internal IP)
├─ Passbook Stations: 192.168.99.15-28 (internal LAN)
└─ Oracle: 192.168.10.200 (internal), 43.229.79.117 (cloud)

SSH/SFTP (Renci.SshNet):
└─ 316 .cs source files embedded — Private key & password authentication
   └─ ใช้ transfer backup files (expdp .DMP) ไป backup server
```

---

## 7. Maintenance และ Operations Guide

### 7.1 Daily Operations

```
รายวัน (Automated):
├─ [Batch] PBProcess: pccommon → pcdeposit → pcloan → pckeeping → pcfinance
│   └─ n_cst_keeping_process.of_processloan() — process loan daily
│   └─ n_cst_keeping_process.of_processloanocalint() — คำนวณดอกเบี้ย
│   └─ n_cst_keeping_process.of_rcvprocess() — batch receive
├─ [Report] Task Scheduler ทุก 1 นาที: runreport.bat → pbreport.exe
│   └─ ล้าง: runreportdel.bat
└─ [DB] ws_fin_closeday.aspx — ปิดวันบัญชีการเงิน

รายวัน (Manual):
├─ ตรวจสอบ Oracle session: Alter_Oracle_Kill_Sessions_user_w3w.sql
│   └─ Kill INACTIVE session > 15 นาที (w3wp.exe)
└─ ตรวจสอบ IIS Application Pool status
```

### 7.2 Monthly Operations

```
รายเดือน (Automated):
├─ [Batch] ws_kep_process_keep.aspx + PBProcess:
│   └─ proc_type 2-5: MemberGroup / MemberNo / Category / DebtAgent
│   └─ RECEIVE_YEAR / RECEIVE_MONTH — ระบุงวด
├─ [PBProcess] pcloan.pbl:
│   └─ n_cst_proc_slmnyret.of_poststm_contract() — post loan statement
│   └─ n_cst_proc_slmnyret.of_setlistcontnewlrtmcc() — MCC contract list
├─ [HR] ws_hr_payroll_process.aspx:
│   └─ ตรวจ post_status ใน hrpayroll + amworkcalendar
└─ [Dividend] pcdivavg.pbl — คำนวณเฉลี่ยคืน/เงินปันผล
```

### 7.3 Oracle Database Maintenance

```
Backup (OracleSync2BackupServer.master.bat):
1. Map network drive ไป backup server
2. Clean tmp directory
3. expdp export schema ISCOMCC → 43.229.79.117 (Cloud)
4. MD5 checksum verification
5. Optional: 7-zip compression (OracleSync2BackupServer.script.cloud_backup_7z.bat)

PLImporter (Deploy Oracle PLSQL):
1. PLImporter.exe reads PLImporter.config (connection string, PLSQL_Path)
2. Drop all: Type → Package → Function → Procedure → Trigger
3. Import from PLSQL/ directory (FUNCTIONS/17 + PACKAGES/14 + TYPES/8)
4. Verify ด้วย Debug_mode = true

Session Management:
└─ Alter_Oracle_Kill_Sessions_user.xml (Task Scheduler) + .bat + .sql
   └─ Schedule: kill inactive sessions อัตโนมัติ
```

### 7.4 Web Application Deployment

```
การ Build และ Deploy ใหม่:

Step 1: Build (Build.bat):
  wmic process where (name="Javaw.exe") delete  ← ปิด iReport ก่อน
  NET STOP W3SVC                                 ← หยุด IIS
  msbuild.exe GCOOP_MCC.sln /t:Clean
  msbuild.exe Extend_Saving.csproj /t:rebuild
  NET START W3SVC                                ← Start IIS

Step 2: IIS Config (Deploy-IIS-Extend.v4.bat — ครั้งแรกหรือ reset):
  - สร้าง App Pools: MCC.saving, MCC.wcf, MCC.report
  - สร้าง Virtual Applications
  - เพิ่ม MIME Types (.pbd, .trf)
  - สร้าง PBSLIP_MCC directory

Step 3: ClickOnce Update (winUPBOOK/winLKE):
  - Publish ไปที่ pbUpbook/ folder ใน IIS
  - เพิ่ม version ใน Application Files/
  - Client จะ auto-update ครั้งต่อไปที่เปิด
```

### 7.5 Configuration Management (XMLConfig)

```
XMLConfig.exe (PowerBuilder admin tool):
├─ server.connection_string.xml — เปลี่ยน DB profile
├─ server.wcf_detail.xml       — เปลี่ยน WCF endpoint
├─ print.formsets.xml          — กำหนด/เพิ่ม Passbook Printer stations
├─ print.report.xml            — กำหนด PDF output path
├─ d_ln_loan_type_*.xml (13 files) — กำหนด loan type attributes
└─ xmlconf.constmap.xml        — ตั้งค่า PBSlip, Central Log

ทุกไฟล์: UTF-16LE encoding
```

### 7.6 Data Migration (CONVERT_MCC)

```
เมื่อต้องการ migrate ข้อมูลจากระบบเดิม:

1. SQL preparation (CONVERT_MCC/SQL Scripts):
   └─ Delete Data.txt → ลบข้อมูลเดิมก่อน migrate
   └─ Delete Table.sql → ลบตาราง (ถ้าจำเป็น)

2. PowerBuilder Pipeline (mcc_pipeline/):
   └─ mcc_pipe_member.pbl  → migrate สมาชิก
   └─ mcc_pipe_dep.pbl     → migrate บัญชีเงินฝาก
   └─ mcc_pipe_shrlon.pbl  → migrate หุ้น/เงินกู้
   └─ mcc_pipe_keeping.pbl → migrate ออมทรัพย์พิเศษ
   └─ mcc_pipe_insurance.pbl → migrate ประกัน
   └─ mcc_pipe_divavg.pbl  → migrate เงินปันผล

3. Post-migration SQL (SQL Update หลัง CNV.txt):
   └─ Update lastcalint_date ใน dpdeptprncfixed
   └─ Update ประเภทบัญชี 005, 015, 019, 002, 006, 028, 032-034 ฯลฯ

4. Address conversion (cnv_addr/):
   └─ cnvaddr.pbl + fmcomsv.pbl + fmsheet.pbl → convert ที่อยู่
```

### 7.7 iReport Template Management

```
iReport Templates (676 .jrxml):
Path: C:\GCOOP_ALL\MCC\GCOOP\iReport\Reports\

การเพิ่ม/แก้ไข template:
├─ ใช้ iReport 5.0.4 (ห้ามใช้ version ใหม่กว่า)
├─ DTD: iReport 5.0.4 standard เท่านั้น
│   ✅ <pen lineWidth="0.5" lineStyle="Dashed"/>
│   ❌ stroke="Dashed" (JasperReports 6.x+ syntax)
├─ UUID: 36-character GUID format เสมอ
└─ หลัง deploy: Task Scheduler จะ execute รายงานทันที (รอ ≤1 นาที)

CriteriaIReport (117 schemas):
Path: /GCOOP/Saving/CriteriaIReport/
├─ .xsd — XML Schema Definition
├─ .xsc — XmlSerializer cache
└─ .xss — XmlSerializer schema

MCC-specific criteria (3 ไฟล์):
├─ u_cri_coopid_memno_loancont_refcollno_mcc
├─ u_cri_coopid_memno_loancont_refcollno_mcc_resign
└─ u_cri_coopid_period_memno_loancont_refcollno_mcc
```

### 7.8 ข้อควรระวัง (Known Issues)

```
1. 32-bit IIS constraint:
   └─ System.Data.OracleClient เป็น 32-bit เท่านั้น
   └─ library ทั้งหมดต้องเป็น x86-compatible
   └─ ห้ามเปลี่ยน App Pool เป็น 64-bit

2. Encoding:
   └─ .cs/.aspx files: UTF-8 with BOM (UTF-8-sig)
   └─ PowerBuilder Classic (.pbl/.pbt): Windows-874 (TH8TIS)
   └─ XMLConfig XML files: UTF-16LE
   └─ Oracle charset: TH8TISASCII (ไม่ใช่ UTF-8)

3. Concurrent edit protection:
   └─ UseMembUptoToken() ต้องปล่อย lock เมื่อ session หมด
   └─ ถ้า lock ค้าง: kill Oracle session ที่เกี่ยวข้อง

4. iReport Java process:
   └─ Build.bat ต้อง kill Javaw.exe ก่อน build เสมอ
   └─ Frame.Master.cs: WebUtil.StartIreportBuilder(true) เมื่อ r=1

5. WCF HttpRuntime timeout:
   └─ executionTimeout = 43200 วินาที (12 ชั่วโมง)
   └─ ตั้งสูงสำหรับ batch processing — ปกติไม่ควร timeout

6. Oracle session kill:
   └─ สำหรับ Amazon RDS ใช้ rdsadmin.rdsadmin_util.kill() แทน ALTER SYSTEM
```

---

## สรุปภาพรวมขั้นสุดท้าย

ระบบ GCOOP MCC เป็นระบบสหกรณ์ออมทรัพย์ระดับองค์กรขนาดใหญ่ที่มีสถาปัตยกรรมหลายชั้น:

| ชั้น | เทคโนโลยี | ขนาด | บทบาท |
|---|---|---|---|
| **Web** | ASP.NET 4.0, WebForms, AjaxPro | 436 ASPX, 24 modules | User interface, workflow |
| **WCF** | BasicHttpBinding, .svc | 10 services | Business logic bridge |
| **PowerBuilder** | PBProcess + iSavBOfc + PBReport | 11+26 libs, ~80 MB | Core business engine |
| **Oracle** | System.Data.OracleClient, TH8TISASCII | 39 PLSQL, 10+ connections | Data persistence |
| **iReport** | iReport 5.0.4, Task Scheduler | 676 jrxml | Report generation |
| **Client Tools** | VB.NET + C# + ClickOnce | winUPBOOK + winLKE | Peripheral devices |

ความแตกต่างสำคัญของ MCC คือ **ความสมบูรณ์ด้าน HR (81 หน้าจอ), RDC Document Management (57 หน้าจอ) และ Welfare (33 หน้าจอ)** ซึ่งทำให้ MCC เหมาะกับสหกรณ์ขนาดใหญ่ที่ต้องการบริหารองค์กรครบวงจร

---

*เอกสารนี้สร้างจากการสังเคราะห์ข้อมูลจาก Session 1-3 เท่านั้น ข้อมูลทั้งหมดตรวจสอบจาก source code และไฟล์ configuration จริงในระบบ*  
*วันที่สร้าง: 2026-09-14*
