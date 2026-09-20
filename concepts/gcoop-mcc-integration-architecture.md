---
coop: mcc
title: สถาปัตยกรรม Integration MCC — ครบถ้วน
created: 2026-09-14
updated: 2026-09-14
type: concept
tags: [architecture, aspnet, powerbuilder, oracle, wcf, plsql, api, deployment, workflow, security, ireport]
sources: [raw/documents/mcc-session1-web-analysis.md, raw/documents/mcc-session2-powerbuilder-analysis.md, raw/documents/mcc-session3-infrastructure-analysis.md, raw/documents/mcc-session4-integration-summary.md]
confidence: high
---

# สถาปัตยกรรม Integration MCC — ครบถ้วน

Integration architecture ครบถ้วนของ MCC — Web ↔ WCF ↔ PowerBuilder ↔ Oracle ↔ iReport พร้อม flow ธุรกิจ, external systems, deployment, และ security

## 1. Integration Map ภาพรวม

```
┌─────────────────────────────────────────────────────────────────────────┐
│                    GCOOP MCC — Integration Architecture                  │
├─────────────────────────────────────────────────────────────────────────┤
│                                                                          │
│  Browser ──AjaxPro/JsPostBack──▶ ASP.NET Web (Frame.Master)            │
│                                    └─ 24 modules, 436 ASPX              │
│                                    └─ PageWebSheet + DataSets            │
│                                        │                                 │
│              ┌─────────────────────────┼───────────────────┐            │
│              ▼                         ▼                   ▼            │
│  WCF Services (WcfService125)  Oracle Direct       iReport Engine       │
│  n_loan, n_common, n_deposit   (System.Data.        676 .jrxml          │
│  n_keeping, n_finance          OracleClient x86)   Task Scheduler       │
│  n_mbshr, n_account            ExecuteDataSource    1 min queue         │
│  n_divavg, n_investment         WebUtil.QuerySdt()                      │
│  ATMcoreWeb.svc                        │                                 │
│              │                         │                                 │
│              ▼                         ▼                                 │
│  PowerBuilder PBProcess         Oracle Database                          │
│  pbprocess.pbl (entry)          192.168.10.200/gcoop                    │
│  → pccommon.pbl                 43.229.79.117/gcoop                     │
│  → pckeeping(14.4MB)            TH8TISASCII charset                     │
│  → pcdeposit(11.9MB)            PLSQL: 17 Functions                     │
│  → pcloan(9MB)                         14 Packages                      │
│  → pcfinance(8.5MB)                     8 Types                         │
│  → pcdivavg(3.9MB)             Java: WCFCALLING stored proc             │
│              │                                                           │
│  iSavBOfc (PFC 10.5, 26 libs)                                          │
│  ibanktrn + cmsrv_fin + cmsrv_loan                                      │
│  → iBank.ini (9 ธนาคาร Direct Credit)                                  │
└─────────────────────────────────────────────────────────────────────────┘
```

## 2. สาม Integration Patterns

### Pattern A: Web → WCF → PowerBuilder → Oracle
```
ASP.NET (ws_lon_apvloan.aspx.cs)
  └─ WcfCalling wcf = new WcfCalling()
     └─ wcf.NLoan.of_gennewcontractno(state.SsWsPass, req_coopid, loantype_code)
        └─ n_loan.svc (WcfService125, BasicHttpBinding)
           └─ PB NVO: n_cst_process_service.of_pbprocess(as_procid)
              └─ pcloan.pbl → Oracle INSERT/UPDATE (AutoCommit=false)
```

### Pattern B: Web → Oracle Direct
```
ASP.NET (ws_hr_payroll_process.aspx.cs)
  └─ WebUtil.QuerySdt("SELECT count(post_status) FROM hrpayroll WHERE ...")
     └─ System.Data.OracleClient (x86, 32-bit)
        └─ Return Sdt (Simple DataTable)

ASP.NET (ทั่วไป)
  └─ ExecuteDataSource exe
     └─ INSERT/UPDATE/DELETE ผ่าน DataSource pattern
```

### Pattern C: Report Queue
```
ASP.NET (WebUtil.StartIreportBuilder / CriteriaIReport)
  └─ สร้าง .bat ใน %SystemRoot%\Temp\
     └─ Windows Task Scheduler (RunReport.xml) ทุก 1 นาที
        └─ runreport.bat: execute .bat files
           └─ pbreport.exe → .jrxml → PDF
              └─ Output: C:\GCOOP\Saving\Report\PDF\
                 └─ runreportdel.bat: ล้าง .bat

iReport Engine:
└─ Frame.Master.cs: WebUtil.StartIreportBuilder(false) ทุก request
   └─ restart ถ้า r=1 parameter
   └─ gstore_ip = 127.0.0.1 (GStore server)
```

## 3. Business Flow ครบวงจร

### สมัครสมาชิก → ฝากเงิน → กู้ → ชำระ → ปันผล

```
[1] สมัครสมาชิก
    ws_mem_apvappl.aspx → Oracle (mbshr module)
    ws_ixp_mb_register_cardPerson.aspx → winLKE (magnetic card)

[2] เปิดบัญชีเงินฝาก
    iSavBOfc/ibanktrn.pbl → Oracle
    ws_kep_process_keep.aspx → pckeeping.pbl (proc_type 2-5)

[3] อัพเดทสมุดบัญชี
    winUPBOOK → PEM.ocx → Passbook Printer Hardware
    GetRule_BookPage() ← DPDEPTCONSTANT Oracle table

[4] ยื่นกู้ → อนุมัติ → รับเงิน
    ws_lon_reqloan.aspx → Oracle (dsMain + 9 DataSets)
    ws_lon_apvloan.aspx → wcf.NLoan.of_gennewcontractno() → WCF → PB → Oracle
    ws_lon_rcvloan.aspx + ws_lon_post_loan_receive.aspx

[5] ชำระงวด (รายเดือน)
    ws_lon_payment.aspx → UseMembUptoToken() → Oracle (dsDetailLoan)
    PBProcess: pcloan.pbl
    → n_cst_keeping_process.of_processloan()
    → n_cst_keeping_process.of_processloanocalint()
    → n_cst_proc_slmnyret.of_postslip_payinloan()
    → n_cst_proc_slmnyret.of_postpayinslippx_mcc() ← MCC-specific

[6] ตัดเงินเดือน + โอนธนาคาร
    ws_hr_payroll_process.aspx → hrpayroll + amworkcalendar
    iSavBOfc: ibanktrn.pbl + pccomloan.pbl → iBank.ini (9 banks)
    ws_fin_exptobank.aspx / ws_lon_exptobank.aspx → Direct Credit files

[7] คำนวณเฉลี่ยคืน/ปันผล
    PBProcess: pcdivavg.pbl → Oracle
    iReport: ir_didurt_* (20 templates) + ir_diinvt_* (18 templates)
```

## 4. External System Integration Points

### ATM
- WCF: `ATMcoreWeb.svc` (localhost/ATM/CoreCoop/ATMcoopServiceWeb/)
- DataSet: `dsLinkATM`, `dsAccidAtm` ใน ws_mem_memberdetail.aspx
- Back Office: `amscwins_amtksb.txt` → `w_sheet_atm_bay_post` (Bay/Krungsri ATM, Process 031001)

### ธนาคาร (9 แห่ง — iBank.ini)
- BBL, K-Bank, KTB (DC+IP), SCB, Citibank, PEA, BAAC, TKS
- Format: FIXED-POST (.trf / .DAT)
- Default: KTB (company_bank=006)

### Smart Card
- `PostSmartCard` event ใน ws_mem_memberdetail.aspx
- อ่านข้อมูลสมาชิกจากบัตรอัจฉริยะ

### Passbook Printer
- winUPBOOK (VB.NET) + PEM.ocx (ActiveX)
- 6 printer stations: 192.168.99.15-28 + SERVER 10.20.241.35
- Rules จาก Oracle: DPDEPTCONSTANT (Pagetotal_Pb, Lineperpage_Pb)

### Magnetic Card Reader
- winLKE (C#) + Msre.ocx
- MSRE hardware device
- Win32 P/Invoke: ShowWindow, MoveWindow, FindWindow

### Aero Cooperative
- Oracle: 203.154.158.107:1521/gcoop (iscoaero, iscoaerodep)
- Screens: ws_wc_approve_newmemb_aero, ws_wc_walfare_req_edit_aero
- Export: ws_kep_exportfile_aero.aspx

### SSH/SFTP (External Transfer)
- Renci.SshNet (316 .cs embedded): SftpClient, SshClient, ScpClient
- ใช้ transfer backup .DMP files ไป backup server
- Auth: PrivateKeyAuthenticationMethod + PasswordAuthenticationMethod

## 5. Security Architecture

### Single Sign-On (SSO)
```
domain: icoopthai.co (shared cookie)
loginUrl: http://sav.icoopthai.co/CEN/GCOOP/SingleSignOn/Login.aspx
defaultUrl: http://sso.icoopthai.co/CEN/GCOOP/SingleSignOn/Default.aspx
sitePrefix: "CEN" (Mahidol University Saving and Credit Co-Operative)
```

### Session & Token
- WebStateFactory: `SsTokenId`, `SsUsername`, `SsWorkDate`, `SsCoopControl`, `SsCoopId`
- FrameContext URL: `t={tokenId}&d={EncryptDecryptEngine.Encrypt(connectionString)}`
- Token locking: `WebUtil.UseMembUptoToken()` ป้องกัน concurrent edit

### WCF Authentication
- BasicHttpBinding + `state.SsWsPass` ส่งทุก WCF call
- WCF pool: MCC.wcf (IIS v2.0, 32-bit, default identity)

### Oracle Security
- Multiple users: iscorfd, iscomrc, iscoaero, iscobtg, iscocmt (แยกตาม environment)
- Kill inactive sessions: `gv$session` WHERE osuser=w3wp.exe, INACTIVE > 15 min
- Amazon RDS: `rdsadmin.rdsadmin_util.kill()` แทน ALTER SYSTEM
- SHA3: `FT_ENC` PL/SQL function (Java SHA3 implementation via loadjava)
- Java WcfCalling: Oracle stored function เรียก HTTP POST (ใช้ Oracle trigger เรียก WCF)

## 6. Deployment Architecture

### IIS 32-bit App Pools
| Pool | Runtime | 32-bit | Identity | หน้าที่ |
|---|---|---|---|---|
| MCC.saving | v4.0 | ✅ | SpecificUser | ASP.NET Web |
| MCC.wcf | v2.0 | ✅ | default | WCF Services |
| MCC.report | v2.0 | ✅ | NetworkService | Report (Classic) |

Virtual Apps:
- `/MCC/GCOOP/Saving` → MCC.saving
- `/MCC/GCOOP/WcfService` → MCC.wcf
- `/MCC/GCOOP/WebServiceReport` → MCC.report

### Build Process
```
1. Kill Javaw.exe (iReport)
2. NET STOP W3SVC
3. msbuild Extend_Saving.csproj /t:rebuild
4. NET START W3SVC
```

### ClickOnce (Client Tools)
- winUPBOOK: `http://[server]/pbUpbook/` — versions 1.0.0.5→1.0.0.9, .NET 4.0 x86
- winLKE: version 1.0.0.12, .NET 4.6.1

### Task Scheduler
- RunReport.xml: ทุก 1 นาที, Execution limit P3D, MultipleInstances=IgnoreNew

## 7. Database Profiles

| Profile | Server | User | ใช้ใน |
|---|---|---|---|
| Main MCC | 192.168.10.200/gcoop | iscorfd | Production Web |
| BTG Branch | 172.17.30.45/gcoop | iscobtg | สาขา BTG |
| MRC | 43.229.79.117/gcoop | iscomrc | MRC + Cloud |
| Aero | 203.154.158.107:1521/gcoop | iscoaero | Aero Cooperative |
| iSavBOfc Prod | 192.198.1.171/gcoop | iscodoaeuat | Banking back office |
| iSavBOfc Dev | localhost/gcoop | iscoaero | Development |

charset: `TH8TISASCII` | AutoCommit: `false` ทุก connection

## 8. Encoding Standards

| Layer | Encoding |
|---|---|
| .cs / .aspx (C#) | UTF-8 with BOM (UTF-8-sig) |
| PowerBuilder Classic (.pbl/.pbt) | Windows-874 (TH8TIS) |
| XMLConfig XML files | UTF-16LE |
| Oracle Database | TH8TISASCII |
| iReport .jrxml | UTF-8 |

## 9. Known Constraints

- IIS 32-bit (`enable32BitAppOnWin64=true`) — ทุก library ต้องเป็น x86
- `System.Data.OracleClient` 32-bit เท่านั้น — ห้ามใช้ ODP.NET 64-bit กับ Web
- iReport 5.0.4 DTD เท่านั้น — ห้ามใช้ JasperReports 6.x+ syntax
- executionTimeout = 43200 วินาที (12 ชม.) — สำหรับ batch processing

## Related

- [[gcoop-mcc]] — ภาพรวม MCC
- [[gcoop-mcc-web-system]] — Web layer (436 ASPX, 24 modules)
- [[gcoop-mcc-powerbuilder]] — PB layer (PBProcess + iSavBOfc)
- [[gcoop-mcc-infrastructure]] — Deployment, tools, iReport, PLSQL
- [[gcoop-mcc-hr-rdc-welfare]] — Business domain highlights
- [gcoop-mcc-vs-mhd](https://github.com/visual-coop/gcoop-kb-shared/blob/main/comparisons/gcoop-mcc-vs-mhd.md) — เทียบ MHD
