---
title: ระบบสหกรณ์ MCC (Member Credit Cooperative)
created: 2026-09-14
updated: 2026-09-14
type: entity
tags: [architecture, aspnet, powerbuilder, oracle, member, loan, saving]
sources: [raw/documents/mcc-session1-web-analysis.md, raw/documents/mcc-session4-integration-summary.md]
confidence: high
---

# ระบบสหกรณ์ MCC (Member Credit Cooperative)

ระบบสหกรณ์ออมทรัพย์ MCC แบบองค์กรสมัยใหม่ — จุดเด่น HR, RDC (Document Management), Welfare ครอบคลุมกว่าสหกรณ์ทั่วไป

## ภาพรวม

| รายการ | ค่า |
|---|---|
| **Project Path** | `/root/gcoop_hermes/mcc/` |
| **Wiki Location** | `/root/gcoop_hermes/gcoop-mcc-wiki/` |
| **SVN** | `svn.coopsiam.com:8080/svnGCOOPCORE/mcc` rev 1997 |
| **Size** | 1.7 GB, 343 MB (GCOOP), 192 MB (iSavBOfc) |
| **Stack** | ASP.NET WebForms + WCF + PowerBuilder Classic + Oracle |
| **Report** | iReport 5.0.4 — 1,345 templates (59 MB) |
| **vs MHD** | ใหญ่กว่า 13%, ไฟล์มากกว่า 26% (8,522 vs 6,775) |

## โครงสร้างหลัก

| Directory | Files | Size | บทบาท |
|---|---|---|---|
| `GCOOP/Saving/` | 6,721 | 196 MB | Web Application หลัก (ASP.NET) |
| `GCOOP/PBProcess/` | 26 | 68 MB | Batch Processing (PowerBuilder) |
| `GCOOP/iReport/` | 1,345 | 59 MB | Report Generation |
| `GCOOP/PBReport125/` | 20 | 16 MB | iReport 5.0.4 Runtime |
| `GCOOP/PLSQL/` | 42 | 312 KB | Oracle Packages/Procedures |
| `GCOOP/XMLConfig/` | 40 | 3.7 MB | Configuration |
| `iSavBOfc/` | 223 | 192 MB | PowerBuilder Banking Core |
| `winUPBOOK/` | 8 | 96 MB | Passbook Update (VB.NET) |
| `winLKE/` | 1 | 67 MB | Magnetic Card Reader (C#) |
| `CONVERT_MCC/` | 18 | 4.7 MB | Data Migration |

## จุดเด่น MCC vs สหกรณ์ทั่วไป

- **HR-Focused** — โมดูลใหญ่สุด 1,152 ไฟล์ (81 ASPX) เทียบ MHD ที่ loan เป็นหลัก
- **RDC Document Management** — 748 ไฟล์ ระบบเอกสารดิจิทัลครบวงจร
- **Welfare ครอบคลุม** — 441 ไฟล์ สวัสดิการสมาชิกขยาย
- **Project Management** — 256 ไฟล์ บริหารโครงการ

ดูรายละเอียด [[gcoop-mcc-hr-rdc-welfare]] และ [[gcoop-mcc-web-system]]

## สถาปัตยกรรม 3 ชั้น

```
ASP.NET (23 modules) → WCF Services → PowerBuilder PBProcess (11 libs) → Oracle
        ↕                      ↕                     ↕
   XMLConfig / iReport    PLSQL Packages      winUPBOOK / winLKE
```

รายละเอียด integration: [[gcoop-mcc-integration-architecture]]

## เปรียบเทียบ MHD

MCC ใหญ่กว่า, ครอบคลุมกว่า, โครงสร้าง PowerBuilder เหมือนกัน 100% แต่ web layer ต่างกันมาก — ดู [[gcoop-mcc-vs-mhd]]

## Related
- [[gcoop-mcc-web-system]] — ระบบ Web 23 โมดูล
- [[gcoop-mcc-powerbuilder]] — PowerBuilder 116 libs
- [[gcoop-mcc-infrastructure]] — Infrastructure & Deployment
- [[gcoop-mcc-integration-architecture]] — Integration Patterns
- [[gcoop-mcc-hr-rdc-welfare]] — จุดเด่นธุรกิจ MCC
- [[gcoop-mcc-vs-mhd]] — เปรียบเทียบ MCC vs MHD
