---
title: ระบบ Infrastructure MCC (Deployment + Tools)
created: 2026-09-14
updated: 2026-09-14
type: entity
tags: [deployment, oracle, plsql, architecture, iis, api]
sources: [raw/documents/mcc-session3-infrastructure-analysis.md]
confidence: high
---

# ระบบ Infrastructure MCC (Deployment + Tools)

Infrastructure ครบวงจร — ClickOnce + Oracle PLSQL + iReport 2000+ + XMLConfig + winUPBOOK/winLKE

## 1. winUPBOOK — Passbook Update Tool

| รายการ | ค่า |
|---|---|
| Framework | VB.NET 4.0 Client Profile, VS2010, x86 ClickOnce |
| DB | Oracle ODP.NET → `10.20.240.79:1521/GCOOP` (iscoegat), config `C:\GCOOP_ALL\AERO\winUPBOOK\conn.dat` |
| COM | PEMLib (passbook printer) |
| Modules | `Form1.vb`, `UpdateBook.vb`, `Connection.vb`, `Base.vb` |
| Deploy | `C:\GCOOP_ALL\AERO\winUPBOOK\pbUpbook\` → `http://localhost/winUPBOOK/` ver 1.0.0.5–1.0.0.9 |
| Audit | PBLog table |

## 2. winLKE — Magnetic Card Reader

| รายการ | ค่า |
|---|---|
| Framework | C# .NET 4.6.1, VS2010, Any CPU, WinForms |
| Hardware | `Msre.ocx` ActiveX + `Driver LKE.rar` 31 MB |
| Integration | Oracle.DataAccess.Client + User32.dll (WIN32 API) |
| Output | `winLKE.exe` ClickOnce |

## 3. Oracle PLSQL (`GCOOP/PLSQL/`)

- **Tool:** `PLImporter.exe` + `PLImporter.config.dist` (target `192.168.10.93/iorcl` iscotks)
- **Types:** `str_datacolumn.pls`, `ntb_*.pls` (vc999/vc30/datacolumn), `secure.pls`, `datasourcetool.pls`
- **Packages:** `fpb`, `n_pk_doccontrol`, `n_pk_lnnpl` (NPL), `n_pk_string`
- **Deploy:** drop existing → import Types→Packages→Functions→Procedures→Triggers (debug mode ได้)

## 4. iReport System (`GCOOP/iReport/`)

- **Scale:** 2000+ JRXML, `ReportBuilderCORE.jar` + `run_ireport_builder.bat`
- **Categories:** Check (Krungthai/TMB), Slip (`dw_slipopen_name`), Accept (`ir_accept_book`), Budget, Financial
- **Mgmt:** Java CORE builder + PowerShell process control, `MCC 30 "DRIVE:\GCOOP_ALL" CORE GCOOP`
- **Assets:** `Pictures/` shared images, JRXML + compiled Jasper

## 5. XMLConfig (`GCOOP/XMLConfig/` — xmlconfig.pbl 1.4 MB)

| File | บทบาท |
|---|---|
| `server.connection_string.xml` | Multi-profile: Default@Cloud (43.229.79.117/gcoop iscomrc), MCC@Siam (web.siamcoop.com/gcoop), Test (iscotestmrc) |
| `xmlconf.constmap.xml` | PBSlip auto-update `C:\PBSLIP_MCC\` ↔ `http://mcc.siamcoop.com/...` |
| `print.formcode/formmap/usermap/report.xml` | Printing & report mapping |
| `server.wcf_detail.xml` | WCF endpoints + security |
| `d_ln_loan_type_*.xml` | Loan type configs (minsalary 52 KB, reverse 577 KB) |

## 6. Deployment Architecture

```
C:\GCOOP_ALL\
├── AERO\winUPBOOK\ (desktop tools)
├── CORE\GCOOP\ (shared libs)
├── MCC\GCOOP\Saving|PBProcess|iReport|XMLConfig
└── PBSLIP_MCC\ (PBSlip client)

Domain: mcc.siamcoop.com | DB: 43.229.79.117/gcoop, 10.20.240.x | Cloud: 43.229.79.117
```

ClickOnce + HTTP auto-update, `PLImporter` สำหรับ schema migration, XMLConfig สำหรับ config migration

## Related
- [[gcoop-mcc]] — ภาพรวม MCC
- [[gcoop-mcc-powerbuilder]] — PB ที่ใช้ PLSQL/XMLConfig
- [[gcoop-mcc-integration-architecture]] — Deployment + WCF integration
