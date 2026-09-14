---
title: เปรียบเทียบ MCC vs MHD — สหกรณ์ GCOOP
created: 2026-09-14
updated: 2026-09-14
type: comparison
tags: [architecture, aspnet, powerbuilder, oracle, comparison]
sources: [raw/documents/mcc-session1-web-analysis.md, raw/documents/mcc-session4-integration-summary.md, raw/documents/mcc-session2-powerbuilder-analysis.md]
confidence: high
---

# เปรียบเทียบ MCC vs MHD — สหกรณ์ GCOOP

MCC (Member Credit) vs MHD — สร้างบน GCOOP เดียวกัน แต่ business focus และขนาดต่างกัน

## สรุปขนาด

| มิติ | MCC | MHD | ต่าง |
|---|---|---|---|
| **Working copy** | 1.7 GB | ~1.5 GB | +13% |
| **GCOOP** | 343 MB | ~300 MB | ใหญ่กว่า |
| **Web files** | 6,721 (196 MB) | ~5,000 | +26% |
| **Report templates** | 1,345 (59 MB) | ~1,000 | มากกว่า |
| **PB Libs** | 11 core libs (65 .pbl) | 11 libs | **เหมือนกัน 100%** |
| **PLSQL** | 42 files | ~35 | มากกว่า |

**สรุป:** Web layer ต่างกันมาก, PB core เหมือนกัน, Infrastructure MCC ครบกว่า

## Business Focus

| ด้าน | MCC | MHD |
|---|---|---|
| **จุดเด่น** | HR (1,152) + RDC (748) + Welfare (441) + PM (256) | Loan (33 หน้าจอ วิเคราะห์ครบ) + Saving/Member |
| **โมดูลใหญ่สุด** | HR — 81 ASPX | Loan — ws_lon_* |
| **Document** | RDC digital workflow, digital signature, OCR | ไม่มี RDC สเกลนี้ |
| **Welfare** | Holistic (health/education/emergency/retirement) | พื้นฐาน |
| **ลูกค้า** | สหกรณ์ขนาดใหญ่ บริหารแบบองค์กร | สหกรณ์ออมทรัพย์มาตรฐาน |

MCC = **Employee Experience + Digital Transformation + Member Care** | MHD = **สินเชื่อและเงินฝาก** เชิงลึก

## Technology Stack (เหมือนกัน)

| Layer | MCC | MHD |
|---|---|---|
| Web | ASP.NET WebForms + Frame.Master + XMLConfig | เหมือนกัน |
| Business | PowerBuilder Classic, TH8TISASCII | เหมือนกัน |
| DB | Oracle, ODP.NET, 42 PLSQL | เหมือนกัน |
| Report | iReport 5.0.4, 2000+ JRXML | เหมือนกัน |
| Desktop | winUPBOOK (VB.NET ClickOnce) + winLKE (C#) | มี winUPBOOK/winLKE เช่นกัน |
| Deploy | ClickOnce `mcc.siamcoop.com` vs MHD domain | คนละ domain/profile |

**PB LibList เหมือนกัน:** `pbprocess → pccommon → pckeeping/pcdeposit/pcloan/pcfinance ...`

## MCC-Specific Screens (ไม่มีใน MHD)

- `ws_mem_adjfamily_mcc.aspx` — ครอบครัว MCC
- `ws_acc_budget_setamt_mcc.aspx` — งบ MCC
- `ws_kep_fileimport_mcc.aspx` — import MCC

MHD มี `ws_lon_*` 33 หน้าจอที่วิเคราะห์ละเอียดแล้ว — MCC loan 38 ASPX ยังไม่ได้วิเคราะห์รายหน้าจอ

## Infrastructure

| เรื่อง | MCC | MHD |
|---|---|---|
| XMLConfig profiles | 3 profiles (Siam/Cloud/Dept) + loan type configs 577 KB | คล้ายกัน แต่ MCC มี loan reverse 577 KB ใหญ่ |
| DB connections | `web.siamcoop.com/gcoop`, `43.229.79.117/gcoop` | คนละ host/schema |
| Conversion | `CONVERT_MCC/mcc_pipeline` (aeropipe 7 modules) | มี convert แยก |

## ข้อสรุป

- **เลือก MCC** เมื่อต้องการองค์กรสมัยใหม่ครบวงจร HR+RDC+Welfare
- **เลือก MHD** เมื่อเน้นสินเชื่อเชิงลึก (วิเคราะห์ 33 หน้าจอพร้อม)
- **แชร์ Core ได้:** WCF services (`lncoopsrv`, `commonsrv`, `depositsrv`), PBProcess core, Oracle patterns ใช้ร่วมกันได้ 85%

## Related
- [[gcoop-mcc]] — ภาพรวม MCC
- [[gcoop-mcc-hr-rdc-welfare]] — จุดเด่น MCC
- [[gcoop-mcc-web-system]] — Web MCC
- [[gcoop-mcc-powerbuilder]] — PB (เหมือน MHD)
- [[gcoop-mcc-integration-architecture]] — Integration MCC
