---
title: "ระบบ Web MCC — ASP.NET Application"
created: 2026-09-14
updated: 2026-09-18
type: entity
tags: [aspnet, saving, loan, member, workflow, wcf, oracle, ireport, hr, rdc, walfare]
sources: [raw/documents/mcc-session1-web-analysis.md]
confidence: high
---

# ระบบ Web MCC — ASP.NET Web Application

> ระบบ Web ของสหกรณ์ MCC (Member Credit Cooperative) พัฒนาด้วย ASP.NET 4.0  
> มี **24 โมดูล**, **436 ไฟล์ .aspx** (16 โมดูลที่ active)  
> สถาปัตยกรรม 3-tier: Web → WCF (PowerBuilder) → Oracle

## ข้อมูลพื้นฐาน

| รายการ | ค่า |
|---|---|
| **Path** | `/root/gcoop_hermes/mcc/GCOOP/Saving/` |
| **Framework** | ASP.NET 4.0, C# |
| **Pattern** | WebSheet (PageWebSheet + JsPostBack attributes) |
| **Database** | Oracle (System.Data.OracleClient, x86) |
| **WCF** | localhost/CORE/GCOOP/WcfService125/ (10 services) |
| **Report** | iReport 5.0.4 (Java Builder, 117 criteria, 2000+ reports) |
| **Auth** | Forms Auth + SSO (icoopthai.co domain) |
| **AJAX** | AjaxPro 2 |
| **Site** | Mahidol University Saving and Credit Co-Operative (CEN) |

## โมดูลทั้งหมด (24 โมดูล)

### โมดูล Active (มีหน้า .aspx)

| โมดูล | ASPX | คำอธิบาย |
|---|---|---|
| **hr** | 81 | Human Resources — เงินเดือน, ลา, OT, สวัสดิการพนักงาน |
| **rdc** | 57 | Document Management — ลงทะเบียน/ส่ง/รับ/ทำลายเอกสาร |
| **loan** | 38 | สินเชื่อและเงินกู้ — ขอกู้, อนุมัติ, ชำระ, สัญญา |
| **mbshr** | 35 | สมาชิกและหุ้น — ข้อมูลสมาชิก, สมัคร, ลาออก, ถอนหุ้น |
| **walfare** | 33 | สวัสดิการ — จ่ายกรณีเสียชีวิต, เบี้ยรายปี, สมาชิก |
| **finance** | 23 | การเงิน — ยืม-คืนเงิน, ปิดวัน, export ธนาคาร |
| **pm** | 21 | Portfolio Management — ลงทุน, ไถ่ถอน, ดอกเบี้ย |
| **brw** | 19 | กู้ยืมระหว่างสหกรณ์ — Memo, สัญญา, ชำระ |
| **durinvt** | 18 | ทรัพย์สินระยะยาว — รับ/จ่าย/ปิดปี |
| **ixp** | 12 | Interface — Export/Import ไฟล์, ATM, เงินเดือน |
| **assist** | 11 | เงินช่วยเหลือสมาชิก — ขอ, อนุมัติ, จ่าย |
| **insurance** | 9 | ประกันภัย — ขอ/อนุมัติ/ประกันสินเชื่อ |
| **keeping** | 7 | เงินฝากประจำ — ประมวลผล, ตัดเงิน, export |
| **investment** | 7 | การลงทุน — รวม `ws_lc_memo` ใหม่ SVN 2051 ([[gcoop-mcc-ws-lc-memo]]) |
| **fom** | 3 | Form/Workflow Management |
| **account** | 3 | งบประมาณ — ตั้งงบ, over budget |

### โมดูล Skeleton (มีโครงสร้างแต่ไม่มี .aspx)
`admin`, `agency`, `arc`, `budget`, `divavg`, `mis`, `trading`

## Architecture Pattern

```
Browser (JS + AjaxPro)
    ↓ HTTP POST [JsPostBack]
Frame.Master
  ├── WebStateFactory (SsTokenId, SsUsername, SsWorkDate, SsCoopControl)
  ├── XmlConfigService (XMLConfig)
  ├── WcfCalling (wcf.*) factory
  └── StartIreportBuilder()
    ↓
PageWebSheet (base class)
  ├── InitJsPostBack() — register DataSets
  ├── WebSheetLoadBegin() — initial load
  ├── CheckJsPostBack(eventArg) — handle JS events
  └── SaveWebSheet() — save to DB
    ↓
ExecuteDataSource      wcf.NLoan / wcf.NCommon / ...
    ↓                      ↓
Oracle Database     WcfService125/*.svc → PowerBuilder NVO → Oracle
```

## WCF Services (10 services)

| Service | Endpoint |
|---|---|
| n_common | WcfService125/n_common.svc |
| n_account | WcfService125/n_account.svc |
| n_deposit | WcfService125/n_deposit.svc |
| n_divavg | WcfService125/n_divavg.svc |
| n_finance | WcfService125/n_finance.svc |
| n_investment | WcfService125/n_investment.svc |
| n_keeping | WcfService125/n_keeping.svc |
| n_loan | WcfService125/n_loan.svc |
| n_mbshr | WcfService125/n_mbshr.svc |
| ATMcoreWeb | ATM/CoreCoop/ATMcoopServiceWeb/ATMcoreWeb.svc |

## Oracle Database Connections (10 connections)

- **หลัก:** 192.168.10.200/gcoop (iscorfd) — MCC CEN
- **Aero:** 203.154.158.107:1521/gcoop (iscoaero) — สหกรณ์ Aero
- **MRC:** 43.229.79.117/gcoop (iscomrc)
- **BTG:** 172.17.30.45/gcoop (iscobtg)
- **CMT:** 127.0.0.1/gcoop (iscocmt) — local
- และอื่นๆ อีก 5 connections

## MCC-Specific Features

1. **CoopId-based Logic:** `state.SsCoopControl == "003001" || "074001"` → เปลี่ยน order ใน loan approval
2. **ws_mem_adjfamily_mcc:** Screen แก้ไขครอบครัวสมาชิกเฉพาะ MCC
3. **ws_kep_fileimport_mcc:** Import keeping ที่มี adjustment dialog เพิ่ม
4. **ws_acc_budget_setamt_mcc:** ตั้งงบประมาณเฉพาะ MCC
5. **CriteriaIReport _mcc:** 3 schema files เฉพาะ MCC (loan collateral + resign)
6. **Token-based Member Locking:** `WebUtil.UseMembUptoToken()` ป้องกัน concurrent edit
7. **Smart Card:** `PostSmartCard` event ใน ws_mem_memberdetail
8. **Dual-DB Mode:** `Sta.IS_OLEDB_MODE` รองรับ Oracle และ SQL Server
9. **34+ DataSets ในหน้าเดียว:** ws_mem_memberdetail มีข้อมูลครบทุกมิติ
10. **6 ประเภทเงินช่วยเหลือพนักงาน:** ละเอียดกว่าระบบสหกรณ์ทั่วไป

## หน้าสำคัญสุดท้าย (Top Screens)

### Loan
- `ws_lon_reqloan` — ยื่นขอกู้ (10+ DataSets, JsPostBack 20+ events)
- `ws_lon_apvloan` — อนุมัติ + `wcf.NLoan.of_gennewcontractno()`
- `ws_lon_payment` — ชำระเงิน (SLIPTYPE_CODE=PX, token lock)

### Member
- `ws_mem_memberdetail` — ข้อมูลสมาชิกครบถ้วน (34+ DataSets, Smart Card)
- `ws_mem_apvappl` — อนุมัติสมาชิกใหม่

### HR
- `ws_hr_master` — ประวัติพนักงาน (30+ JsPostBack, ครอบครัว/การศึกษา/ประสบการณ์)
- `ws_hr_payroll_process` — payroll (amworkcalendar, hrpayroll, Thai Buddhist year)

### Keeping
- `ws_kep_process_keep` — ประมวลผลเงินฝาก (proc_type 1-5)

## ไฟล์/โฟลเดอร์สนับสนุน

| โฟลเดอร์ | รายละเอียด |
|---|---|
| `CriteriaIReport/` | 117 XSD criteria สำหรับ iReport |
| `DataWindow/` | 27 โฟลเดอร์ DataWindow definitions |
| `IREPORTFILE/` | ไฟล์ .jrxml รายงาน |
| `SlipApp/`, `SlipPB/` | Slip printing (App vs PowerBuilder) |
| `CustomControl/` | Custom ASP.NET controls |
| `Documents/`, `Downloads/` | เอกสาร/ดาวน์โหลด |
| `ImageMember/` | รูปภาพสมาชิก |

## Links

- → [[gcoop-mcc]] — ภาพรวม MCC
- → [[gcoop-mcc-powerbuilder]] — PowerBuilder backend (WcfService125)
- → [[gcoop-mcc-infrastructure]] — iReport, XMLConfig, ClickOnce
- → [[gcoop-mcc-integration-architecture]] — Web→WCF→PB→Oracle flow
- → [[gcoop-mcc-hr-rdc-welfare]] — จุดเด่น HR/RDC/Walfare
