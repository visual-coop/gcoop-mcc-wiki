---
title: เปรียบเทียบ MCC vs MHD — สหกรณ์ GCOOP (ครบถ้วน)
created: 2026-09-14
updated: 2026-09-14
type: comparison
tags: [architecture, aspnet, powerbuilder, oracle, comparison, hr, rdc, welfare, loan, deployment]
sources: [raw/documents/mcc-session1-web-analysis.md, raw/documents/mcc-session2-powerbuilder-analysis.md, raw/documents/mcc-session3-infrastructure-analysis.md, raw/documents/mcc-session4-integration-summary.md]
confidence: high
---

# เปรียบเทียบ MCC vs MHD — สหกรณ์ GCOOP (ครบถ้วน)

MCC (Member Credit) vs MHD — สร้างบน GCOOP platform เดียวกัน แต่ business focus, ขนาด, และ infrastructure แตกต่างกัน

## 1. สรุปขนาด

| มิติ | MCC | MHD | ต่าง |
|---|---|---|---|
| **Working copy** | 1.7 GB | ~1.5 GB | MCC +13% |
| **GCOOP dir** | 343 MB | ~300 MB | MCC ใหญ่กว่า |
| **Web files** | 6,721 (196 MB) | ~5,000 | MCC +26% |
| **ASPX files** | 436 | ~350 | MCC +24% |
| **iReport .jrxml** | 676 (MCC dir) | ไม่ระบุ | MCC มากกว่า |
| **CriteriaIReport schemas** | 117 | ไม่ระบุ | MCC มากกว่า |
| **PB Core Libs (PBProcess)** | 11 libs (~54 MB) | 11 libs | **เหมือนกัน 100%** |
| **PLSQL** | 39 .pls files | ~35 | MCC มากกว่า |
| **Oracle connections (Web.config)** | 10 connection strings | ไม่ระบุ | MCC มีมากกว่า |

## 2. Business Focus

| ด้าน | MCC | MHD |
|---|---|---|
| **โมดูลใหญ่สุด** | HR — 81 ASPX | Loan — เน้นหลัก |
| **จุดเน้น** | HR + RDC + Welfare + PM | Loan + Saving + Member |
| **Document Management** | RDC (57 ASPX) — ครบวงจร (รับ-ส่ง-เก็บ-ทำลาย, EMS, ตู้นิรภัย) | ไม่มี RDC module ระดับนี้ |
| **Welfare** | walfare (33 ASPX) — holistic (เสียชีวิต, ฟื้นฟู, Aero integration) | พื้นฐาน |
| **Portfolio Management** | pm (21 ASPX) | น้อยกว่าหรือไม่มี |
| **Loan** | loan (38 ASPX) — Credit Scoring, Collateral | loan เป็นหัวใจหลัก |
| **HR Payroll** | ws_hr_payroll_process → hrpayroll + amworkcalendar | ไม่มีหรือน้อยกว่า |
| **กลุ่มลูกค้า** | สหกรณ์ขนาดใหญ่ มีพนักงาน องค์กรสมัยใหม่ | สหกรณ์ออมทรัพย์มาตรฐาน |

**MCC** = Employee Experience + Digital Transformation + Member Care  
**MHD** = สินเชื่อและเงินฝากเชิงลึก

## 3. Technology Stack (เหมือนกันทั้งหมด)

| Layer | MCC | MHD |
|---|---|---|
| **Web Framework** | ASP.NET WebForms 4.0 | เหมือนกัน |
| **Pattern** | Frame.Master + PageWebSheet + JsPostBack | เหมือนกัน |
| **Session** | WebStateFactory (SsTokenId, SsUsername, SsWorkDate, SsCoopControl) | เหมือนกัน |
| **WCF Services** | WcfService125/n_*.svc (BasicHttpBinding) | เหมือนกัน |
| **PowerBuilder** | PBProcess 11 libs + iSavBOfc (PFC 10.5) | **เหมือนกัน** |
| **Database** | Oracle, TH8TISASCII, System.Data.OracleClient x86 | เหมือนกัน |
| **Report** | iReport 5.0.4, .jrxml, Task Scheduler ทุก 1 นาที | เหมือนกัน |
| **Desktop Tools** | winUPBOOK (VB.NET) + winLKE (C#) — ClickOnce | เหมือนกัน |
| **IIS** | 3 App Pools (saving/wcf/report), 32-bit | เหมือนกัน (คนละ pools) |
| **SSO** | icoopthai.co domain | domain เดียวกัน (คนละ prefix) |

## 4. ความแตกต่าง Infrastructure

| เรื่อง | MCC | MHD |
|---|---|---|
| **XMLConfig DB profiles** | 4 profiles: Default@Cloud, MCC@Siam, MCC@Dept, เทส | คล้ายกัน (คนละ host/schema) |
| **Oracle servers** | 192.168.10.200, 43.229.79.117, 203.154.158.107 | คนละ server |
| **Oracle schemas** | iscorfd, iscomrc, iscoaero, iscobtg, iscocmt | คนละ schema |
| **Domain** | mcc.siamcoop.com | คนละ domain |
| **PBSlip** | C:\PBSLIP_MCC\ | คนละ directory |
| **Conversion tool** | CONVERT_MCC/mcc_pipeline (6 domain libraries) | มี convert แยก |
| **sitePrefix** | CEN (Mahidol University) | คนละ prefix |

## 5. PowerBuilder Lib Comparison (เหมือนกัน 100%)

ทั้ง MCC และ MHD ใช้ LibList เดียวกัน:
```
pbprocess.pbl → pccommon.pbl → pckeeping.pbl → pcdeposit.pbl
→ pcloan.pbl → pcfinance.pbl → pcdivavg.pbl → pcinsurance.pbl
→ pcinvertment.pbl → pcmbshr.pbl → pcaccount.pbl
```

ขนาด .pbl อาจต่างกันเล็กน้อย (MCC version มี pcdeposit ใหญ่กว่าใน pbpro: 15.9 MB vs 11.9 MB ใน iSavBOfc)

## 6. MCC-Specific Screens (ไม่มีใน MHD)

| Screen | โมดูล | คำอธิบาย |
|---|---|---|
| `ws_mem_adjfamily_mcc.aspx` | mbshr | จัดการข้อมูลครอบครัว MCC |
| `ws_kep_fileimport_mcc.aspx` | keeping | Import Keeping + dialog `wd_kep_adjustkeprealitem_memb` |
| `ws_acc_budget_setamt_mcc.aspx` | account | ตั้งงบประมาณ MCC |

iReport MCC-specific:
- `chq_006_krungthai_mcc.jrxml` — เช็ค KTB format MCC
- `chq_011_tmb_mcc.jrxml` — เช็ค TMB format MCC
- CriteriaIReport: `u_cri_*_mcc` (3 criteria files)

PBProcess MCC-specific functions:
- `n_cst_proc_slmnyret.of_postpayinslippx_mcc()` — post salary payment slip MCC
- `n_cst_proc_slmnyret.of_setlistcontnewlrtmcc()` — set new contract list MCC

CoopId-specific logic (Web code):
```csharp
if (state.SsCoopControl == "003001" || state.SsCoopControl == "074001")
{ dsMain.DATA[0].order_type = "3"; }
```

## 7. Shared Core (ใช้ร่วมได้ 85%+)

- WCF Services pattern (n_*.svc, BasicHttpBinding, SsWsPass auth)
- PBProcess core LibList (pccommon → domain libs)
- Oracle PLSQL functions (ft_roundmoney, ftreadtbaht, ft_getintrate, ...)
- Oracle PLSQL packages (n_pk_doccontrol, n_pk_string, pk_srv_mem_audit, ...)
- iReport engine (Java, Task Scheduler, runreport.bat pattern)
- Deploy scripts (IIS 32-bit, ClickOnce pattern)
- winUPBOOK / winLKE hardware integration
- Encoding standards (UTF-8 BOM for C#, Windows-874 for PB Classic, UTF-16LE for XML)

## 8. ข้อสรุปการเลือกใช้

**เลือก MCC** เมื่อ:
- ต้องการระบบ HR ครบวงจร (payroll, leave, OT, welfare, pension)
- ต้องการ RDC Document Management ดิจิทัล (รับ-ส่ง-เก็บ-ทำลาย, EMS)
- ต้องการ Welfare Management ครบถ้วน (เสียชีวิต, ฟื้นฟู, Aero integration)
- สหกรณ์มีพนักงานจำนวนมาก บริหารแบบองค์กร
- ต้องการ Portfolio Management + Investment tracking

**เลือก MHD** เมื่อ:
- เน้นสินเชื่อเชิงลึก (loan workflow ครบ)
- สหกรณ์ออมทรัพย์มาตรฐาน ไม่มีพนักงานจำนวนมาก
- ต้องการระบบที่เล็กกว่า บำรุงรักษาง่ายกว่า

**แชร์ Core ได้:**
- WCF services, PBProcess libs, Oracle patterns ใช้ร่วมกันได้ 85%
- การ customize ส่วนใหญ่อยู่ที่ Web layer และ CoopId-specific logic
- Report templates บางส่วนใช้ร่วมกันได้ (ir_coopid_* prefix)

## Related

- [[gcoop-mcc]] — ภาพรวม MCC
- [[gcoop-mcc-hr-rdc-welfare]] — จุดเด่น MCC (HR/RDC/Welfare)
- [[gcoop-mcc-web-system]] — Web layer MCC
- [[gcoop-mcc-powerbuilder]] — PB layer (เหมือน MHD)
- [[gcoop-mcc-infrastructure]] — Infrastructure MCC
- [[gcoop-mcc-integration-architecture]] — Integration Architecture ครบถ้วน
