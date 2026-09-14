---
title: ระบบ Web Application MCC (ASP.NET)
created: 2026-09-14
updated: 2026-09-14
type: entity
tags: [aspnet, architecture, saving, loan, member, insurance, workflow]
sources: [raw/documents/mcc-session1-web-analysis.md]
confidence: high
---

# ระบบ Web Application MCC (ASP.NET)

Web Application หลักของ MCC — 6,721 ไฟล์, 23 โมดูล, ASP.NET WebForms + Master Page

## สถิติโมดูล (Path: `GCOOP/Saving/Applications/`)

| Module | ASPX | C# | รวม | บทบาท |
|---|---|---|---|---|
| **hr** | 81 | 648 | 1,152 | Human Resources — ใหญ่สุดในระบบ |
| **rdc** | 57 | 392 | 748 | Document Management — จุดเด่น MCC |
| **loan** | 38 | 404 | 688 | สินเชื่อและเงินกู้ |
| **mbshr** | 35 | 356 | 616 | สมาชิกและหุ้น |
| **walfare** | 33 | 241 | 441 | สวัสดิการสมาชิก |
| **finance** | 23 | 153 | 284 | การเงินและบัญชี |
| **pm** | 21 | 165 | 256 | Project Management |
| **assist** | 11 | 120 | 207 | Member Assistance |
| **brw** | 19 | 118 | 185 | Borrowing |
| **durinvt** | 18 | 133 | 221 | Duration Investment |
| **insurance** | 9 | 66 | 118 | ประกันภัย |
| **keeping** | 7 | 52 | 91 | เงินฝากออมทรัพย์ |
| **investment** | 6 | 68 | 117 | การลงทุน |
| **ixp** | 12 | 57 | 108 | Import/Export |

## MCC-Specific Screens (ต่างจากสหกรณ์อื่น)

| หน้าจอ | Path | ฟีเจอร์ |
|---|---|---|
| `ws_mem_adjfamily_mcc.aspx` | `mbshr/ws_mem_adjfamily_mcc_ctrl/` | จัดการข้อมูลครอบครัว MCC + validation relation code |
| `ws_acc_budget_setamt_mcc.aspx` | `account/ws_acc_budget_setamt_mcc_ctrl/` | ตั้งงบประมาณ MCC |
| `ws_kep_fileimport_mcc.aspx` | `ixp/ws_kep_fileimport_mcc_ctrl/` | นำเข้าไฟล์ข้อมูล MCC |

Pattern: `*_mcc.aspx` แยกจาก template มาตรฐาน

## เทคโนโลยี

- **Framework:** ASP.NET WebForms + `Frame.Master`, CodeBehind C#, User Controls (.ascx: `DsMain.ascx`, `DsList.ascx`)
- **Data:** ADO.NET DataSet/DataSource, Oracle + PLSQL 42 ไฟล์
- **Validation:** JavaScript client-side + server-side
- **Config:** XMLConfig (`server.connection_string.xml`, `xmlconf.constmap.xml`)

```
Frame.Master
├── Applications/hr, rdc, loan, mbshr, welfare ... (23 modules)
├── Controls/ (DsMain, DsList)
└── XMLConfig/ (connection, constmap, wcf_detail)
```

## ความโดดเด่น

MCC ครอบคลุมกว่าสหกรณ์ทั่วไปที่เน้น loan/deposit — HR+RDC+Welfare รวมกัน 2,341 ไฟล์ (35% ของระบบ) ดู [[gcoop-mcc-hr-rdc-welfare]]

## Related
- [[gcoop-mcc]] — ภาพรวม MCC
- [[gcoop-mcc-hr-rdc-welfare]] — HR/RDC/Welfare เชิงลึก
- [[gcoop-mcc-integration-architecture]] — Web → WCF → PB → Oracle
- [[gcoop-mcc-vs-mhd]] — เทียบ MHD (MHD เน้น loan)
