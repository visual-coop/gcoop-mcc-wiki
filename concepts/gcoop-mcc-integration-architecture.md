---
title: สถาปัตยกรรม Integration MCC
created: 2026-09-14
updated: 2026-09-14
type: concept
tags: [architecture, aspnet, powerbuilder, oracle, wcf, plsql, api, deployment, workflow]
sources: [raw/documents/mcc-session4-integration-summary.md, raw/documents/mcc-session1-web-analysis.md, raw/documents/mcc-session2-powerbuilder-analysis.md, raw/documents/mcc-session3-infrastructure-analysis.md]
confidence: high
---

# สถาปัตยกรรม Integration MCC

Multi-layer integration ของ MCC — Web → WCF → PowerBuilder → Oracle + ClickOnce/iReport

## 1. Integration Map

```
MCC Cooperative System
├── Web Layer (ASP.NET 23 modules: HR 1152 + RDC 748 + Welfare 441 + Loan 688 + ...)
├── Business Logic (PB: PCKeeping 14.3 MB + PCDeposit 11.9 MB + PCLoan 9 MB + PCFinance 8.5 MB)
├── Infrastructure (Oracle multi-profile + iReport 2000+ + XMLConfig + winUPBOOK/winLKE)
└── Services (WCF + PLSQL Packages + ClickOnce)
```

## 2. Flow Patterns

**Pattern 1: Web → PowerBuilder**
```
ASP.NET (HR/RDC/Welfare) → WCF Services → PBProcess (PBProcess.pbl→pccommon→domain) → Oracle
```

**Pattern 2: Desktop**
```
winUPBOOK/winLKE → Oracle Direct (ODP.NET) → ClickOnce Updates (http://mcc.siamcoop.com/...)
```

**Pattern 3: Report**
```
Web Apps → iReport Engine (ReportBuilderCORE.jar) → JRXML 2000+ → PDF/Print
```

## 3. Component Dependencies

- **Web:** `Frame.Master` + User Controls (`DsMain.ascx`, `DsList.ascx`) + XMLConfig
- **PB LibList:** `pbprocess → pccommon → [pckeeping|pcdeposit|pcloan|pcfinance|pcmbshr|...]`
- **DB:** Oracle multi-profile — `MCC@Siam` (web.siamcoop.com/gcoop), `Default@Cloud` (43.229.79.117/gcoop), `MCC@Dept` (iscotestmrc), charset `TH8TISASCII`
- **MCC Screens:** `ws_mem_adjfamily_mcc`, `ws_acc_budget_setamt_mcc`, `ws_kep_fileimport_mcc` (pattern `*_mcc.aspx`)

## 4. Deployment & Network

```
C:\GCOOP_ALL\
├── AERO\winUPBOOK\ | CORE\GCOOP\ | MCC\GCOOP\Saving|PBProcess|iReport|XMLConfig | PBSLIP_MCC\
Domain: mcc.siamcoop.com | DB: 10.20.240.x / 43.229.79.117 | Auto-update: ClickOnce http://mcc.siamcoop.com/MCC/GCOOP/Saving/SlipPB/SlipPB/ → C:\PBSLIP_MCC\
```

Thai support: DB `TH8TISASCII` + .NET UTF-8 BOM + PB Windows-874 + Oracle Unicode เต็มรูปแบบทุกระดับ

## 5. Maintenance

- **Daily:** txn log backup, index maintenance (PBLog audit)
- **Weekly:** full backup, archive cleanup
- **Monthly:** health check, capacity planning
- **RTO 4 ชม. / RPO 1 ชม.**, multi-site backup, monthly DR test

## Related
- [[gcoop-mcc]] — ภาพรวม MCC
- [[gcoop-mcc-web-system]] — Web layer
- [[gcoop-mcc-powerbuilder]] — PB layer
- [[gcoop-mcc-infrastructure]] — Deployment & tools
- [[gcoop-mcc-vs-mhd]] — เทียบ MHD
