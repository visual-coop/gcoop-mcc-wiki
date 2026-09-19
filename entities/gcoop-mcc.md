---
title: ระบบสหกรณ์ MCC (Member Credit Cooperative)
created: 2026-09-14
updated: 2026-09-14
type: entity
tags: [architecture, aspnet, powerbuilder, oracle, member, loan, saving, hr, rdc, welfare, deployment]
sources: [raw/documents/mcc-session1-web-analysis.md, raw/documents/mcc-session2-powerbuilder-analysis.md, raw/documents/mcc-session3-infrastructure-analysis.md, raw/documents/mcc-session4-integration-summary.md]
confidence: high
---

# ระบบสหกรณ์ MCC (Member Credit Cooperative)

ระบบสหกรณ์ออมทรัพย์ MCC แบบองค์กรสมัยใหม่ครบวงจร — จุดเด่น HR (81 ASPX), RDC Document Management (57 ASPX), Welfare (33 ASPX), ครอบคลุมกว่าสหกรณ์ทั่วไปอย่างมาก

## ภาพรวม

| รายการ | ค่า |
|---|---|
| **Project Path** | `/root/gcoop_hermes/mcc/` |
| **Wiki Location** | `/root/gcoop_hermes/coops/mcc/` |
| **SVN** | `svn.coopsiam.com:8080/svnGCOOPCORE/mcc` |
| **Size** | 1.7 GB total (343 MB GCOOP + 192 MB iSavBOfc) |
| **Stack** | ASP.NET 4.0 WebForms + WCF + PowerBuilder Classic 12.5 + Oracle |
| **Report** | iReport 5.0.4 — 676 .jrxml templates (MCC dir) + 117 CriteriaIReport schemas |
| **vs MHD** | ใหญ่กว่า ~13%, ไฟล์มากกว่า ~26% (6,721 vs ~5,000 web files) |
| **Deployment** | IIS 32-bit (3 App Pools) + ClickOnce client tools |

## โครงสร้าง Directory หลัก

| Directory | บทบาท | ขนาดโดยประมาณ |
|---|---|---|
| `GCOOP/Saving/` | Web Application (ASP.NET 4.0) — 436 ASPX, 24 modules | 196 MB |
| `GCOOP/WcfService125/` | WCF Services — 10 services (n_loan, n_common, ...) | — |
| `GCOOP/PBProcess/` | Batch Processing (PowerBuilder) — 11 .pbl libs | 68 MB |
| `GCOOP/PBReport125/` | Report Engine (PowerBuilder Report Runner) | 16 MB |
| `GCOOP/iReport/` | Report Templates — 676 .jrxml | 59 MB |
| `GCOOP/PLSQL/` | Oracle Stored Procedures/Functions — 39 .pls files | 312 KB |
| `GCOOP/XMLConfig/` | Configuration Admin Tool + XML configs | 3.7 MB |
| `iSavBOfc/` | PowerBuilder Banking Back Office (PFC 10.5, 26 libs) | 192 MB |
| `winUPBOOK/` | Passbook Printer Tool (VB.NET, ClickOnce) | 96 MB |
| `winLKE/` | Magnetic Card Reader (C#, ClickOnce) | 67 MB |
| `CONVERT_MCC/` | Data Migration Tools (PB Pipeline + SQL scripts) | 4.7 MB |
| `0.DB/` | Database Maintenance Scripts (backup, kill sessions, Java WCF) | — |

## สถาปัตยกรรม 4 ชั้น

```
Browser (AjaxPro / JsPostBack)
    ↓ HTTP POST
ASP.NET Web Layer (Frame.Master → PageWebSheet → 24 modules, 436 ASPX)
    ↓ WCF Call / Oracle Direct
WCF Services (WcfService125 — n_loan, n_common, n_deposit, n_keeping, n_finance, n_mbshr...)
    ↓
PowerBuilder Batch (PBProcess: pckeeping 14.4MB, pcdeposit 11.9MB, pcloan 9MB, pcfinance 8.5MB)
    ↓
Oracle Database (TH8TISASCII — 192.168.10.200/gcoop + 43.229.79.117)
    ↕
iReport Engine (676 jrxml, Task Scheduler ทุก 1 นาที)

Desktop Tools: winUPBOOK (Passbook Printer/PEM.ocx) ←→ winLKE (Magnetic Card/Msre.ocx)
```

## โมดูล Web (24 โมดูล, 436 ASPX)

| โมดูล | ASPX | คำอธิบาย |
|---|---|---|
| **hr** | 81 | Human Resources — payroll, leave, OT, welfare |
| **rdc** | 57 | Record/Document Management |
| **loan** | 38 | สินเชื่อและเงินกู้ |
| **mbshr** | 35 | สมาชิกและหุ้น |
| **walfare** | 33 | สวัสดิการสหกรณ์ |
| **finance** | 23 | การเงิน |
| **pm** | 21 | Portfolio Management |
| **brw** | 19 | Borrowing ระหว่างสหกรณ์ |
| **durinvt** | 18 | ทรัพย์สิน |
| **ixp** | 12 | Interface/Export-Import |
| **assist** | 11 | เงินช่วยเหลือสมาชิก |
| **insurance** | 9 | ประกันภัย |
| **keeping** | 7 | เงินฝากประจำ |
| **investment** | 6 | การลงทุน |
| อื่นๆ (admin, agency, arc, budget, divavg, mis, trading, fom, account) | 0-3 | — |

## PowerBuilder Core Libraries (PBProcess)

| Library | ขนาด | บทบาท |
|---|---|---|
| `pckeeping.pbl` | 14.4 MB | Bookkeeping — บัญชีรับจ่าย |
| `pcdeposit.pbl` | 11.9 MB | Deposit — เงินฝากทุกประเภท |
| `pcloan.pbl` | 9.1 MB | Loan — สินเชื่อ |
| `pcfinance.pbl` | 8.5 MB | Finance — งบการเงิน |
| `pcdivavg.pbl` | 3.9 MB | Dividend averaging |
| `pcinsurance.pbl` | 1.7 MB | Insurance |
| `pccommon.pbl` | 2.1 MB | Common utilities |
| `pcinvertment.pbl` | 1.3 MB | Investment |
| `pcmbshr.pbl` | 0.7 MB | Member shares |
| `pcaccount.pbl` | 0.5 MB | Account |
| `pbprocess.pbl` | 0.08 MB | Main entry point |

## จุดเด่น MCC vs สหกรณ์ทั่วไป

- **HR-Focused** — 81 ASPX (โมดูลใหญ่สุด): ประวัติพนักงาน, payroll, การลา, OT, บำเหน็จ
- **RDC Document Management** — 57 ASPX: รับ-ส่ง-เก็บ-ทำลายเอกสาร, EMS, ตู้นิรภัย, รูปภาพ
- **Welfare ครอบคลุม** — 33 ASPX: สมาชิกสวัสดิการ, เสียชีวิต, ฟื้นฟู, Aero integration
- **Multi-bank Payment** — 9 ธนาคาร Direct Credit ผ่าน iSavBOfc + iBank.ini
- **Smart Card** — PostSmartCard event ใน ws_mem_memberdetail.aspx
- **Passbook Printer** — winUPBOOK + PEM.ocx + 6 printer stations (192.168.99.x)
- **ATM Integration** — ATMcoreWeb.svc + dsLinkATM DataSet
- **Token Locking** — WebUtil.UseMembUptoToken() ป้องกัน concurrent edit

## Security Architecture

- **SSO**: Forms authentication, domain `icoopthai.co`, login ที่ `sav.icoopthai.co/CEN/GCOOP/SingleSignOn/`
- **Session**: WebStateFactory (SsTokenId, SsUsername, SsWorkDate, SsCoopControl, SsCoopId)
- **URL Encryption**: EncryptDecryptEngine encrypt connection string ใน FrameContext URL
- **WCF Auth**: BasicHttpBinding + SsWsPass ทุก WCF call
- **Oracle**: Kill inactive sessions (w3wp.exe, >15 min), SHA3 encryption (FT_ENC)

## Deployment

- **IIS**: 3 App Pools (MCC.saving v4.0, MCC.wcf v2.0, MCC.report v2.0 Classic) — ทั้งหมด 32-bit (enable32BitAppOnWin64=true)
- **Build**: Build.bat → Stop IIS → msbuild Extend_Saving.csproj → Start IIS
- **ClickOnce**: winUPBOOK (v1.0.0.9) + winLKE (v1.0.0.12) auto-update ผ่าน IIS
- **Report Scheduler**: Windows Task Scheduler ทุก 1 นาที (RunReport.xml)
- **DB Backup**: Oracle expdp → 43.229.79.117 + MD5 checksum + optional 7-zip

## MCC-Specific Features (ไม่มีในสหกรณ์อื่น)

- `ws_mem_adjfamily_mcc.aspx` — จัดการครอบครัว MCC
- `ws_kep_fileimport_mcc.aspx` — Import Keeping พร้อม dialog พิเศษ
- `ws_acc_budget_setamt_mcc.aspx` — ตั้งงบประมาณ MCC
- CoopId logic: `003001` / `074001` → order_type = "3"
- `n_cst_proc_slmnyret.of_postpayinslippx_mcc()` — Batch function MCC
- `n_cst_proc_slmnyret.of_setlistcontnewlrtmcc()` — Contract list MCC
- iReport criteria: `*_mcc.jrxml` (เช่น `chq_006_krungthai_mcc.jrxml`)

## Related

- [[gcoop-mcc-web-system]] — ระบบ Web 24 โมดูล 436 ASPX
- [[gcoop-mcc-powerbuilder]] — PowerBuilder: PBProcess + iSavBOfc + PBReport
- [[gcoop-mcc-infrastructure]] — Infrastructure: winUPBOOK, winLKE, PLSQL, iReport, XMLConfig
- [[gcoop-mcc-integration-architecture]] — Integration Architecture ครบถ้วน
- [[gcoop-mcc-hr-rdc-welfare]] — จุดเด่น HR/RDC/Welfare
- [[gcoop-mcc-vs-mhd]] — เปรียบเทียบ MCC vs MHD
