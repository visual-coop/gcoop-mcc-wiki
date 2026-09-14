---
title: ระบบ PowerBuilder MCC (PBProcess + iSavBOfc)
created: 2026-09-14
updated: 2026-09-14
type: entity
tags: [powerbuilder, oracle, plsql, saving, loan, dividend, insurance, workflow]
sources: [raw/documents/mcc-session2-powerbuilder-analysis.md]
confidence: high
---

# ระบบ PowerBuilder MCC (PBProcess + iSavBOfc)

PowerBuilder Classic — 65 .pbl + 51 .pbd (116 libs), 11 core business libs, iSavBOfc + Pipeline

## PBProcess Core (`GCOOP/PBProcess/`)

| Library | Size | บทบาท |
|---|---|---|
| `pbprocess.pbl` | 82 KB | Main engine (`pbprocess.exe`) |
| `pccommon.pbl` | 2.1 MB | Common utilities |
| `pckeeping.pbl` | 14.3 MB | Bookkeeping — ใหญ่สุด |
| `pcdeposit.pbl` | 11.9 MB | Deposit management |
| `pcloan.pbl` | 9.0 MB | Loan management |
| `pcfinance.pbl` | 8.5 MB | Financial operations |
| `pcdivavg.pbl` | 3.9 MB | Dividend averaging |
| `pcinsurance.pbl` | 1.7 MB | Insurance |
| `pcinvertment.pbl` | 1.2 MB | Investment |
| `pcmbshr.pbl` | 693 KB | Member shares |
| `pcaccount.pbl` | 536 KB | Account |

**LibList:** `pbprocess.pbl;pccommon.pbl;pcaccount.pbl;pcdeposit.pbl;pcdivavg.pbl;pcfinance.pbl;pcinsurance.pbl;pcinvertment.pbl;pckeeping.pbl;pcloan.pbl;pcmbshr.pbl`

## iSavBOfc Application (`iSavBOfc/`)

| Component | Path | Libs |
|---|---|---|
| Core App | `iBank/` | `isavbofc.pbl`, `ibanktrn.pbl`, `chack_stmbal.pbl` |
| PFC Framework | `PFC105/` | `pfcmain.pbl`, `pfcdwsrv.pbl`, `pfcwnsrv.pbl` + extended (pfe*) |
| Base Comm | `sBaseCom/` | `cmappcom.pbl`, `cmcomsrv.pbl`, `cmrepsrv.pbl` |
| Buss Comm | `sBussCom/` | `pccomloan.pbl`, `cmsrv_mbshr/loan/fin/dept.pbl` |
| Process Integ | `pbpro/` | `pbprocess.pbl`, `pccommon.pbl` |

PFC 10.5 framework — Thai charset `TH8TISASCII`, branch control `057001` (prod) / `077001` (dev)

## Conversion Pipeline (`CONVERT_MCC/mcc_pipeline/` — aeropipe)

| Module | บทบาท |
|---|---|
| `mcc_pipe_app.pbl` | Pipeline core |
| `mcc_pipe_dep/divavg/insurance/keeping/member/shrlon.pbl` | แปลงข้อมูลแต่ละโดเมน |
| `mapshritem.sql`, `mapkepitem.sql` | Mapping table (legacy → new codes) |

ตัวอย่าง mapping: `'B '→'STR'`, `'MS'→'SPM'`, `'MD01 '→'D00'`, `'ML01 '→'L01'`

## Batch Workflows

- **Daily:** Transaction validation (PCCommon) → Interest accrual (PCDeposit/PCLoan) → Statement (PCKeeping) → Compliance (PCFinance)
- **Month-End:** Dividend (PCDivavg) → Financial stmt (PCFinance) → Member stmt (PCMbshr) → Premium (PCInsurance)
- **Year-End:** Annual dividend → Tax reporting → Equity adjustments

## DB Integration

```ini
[Database] DBMS=ORA, LogId=iscodoaeuat/iscoaero, ServerName=192.198.1.171/gcoop, DbParm=NLS_Charset='TH8TISASCII'
[rfscold] ServerName=192.198.1.201/saving (legacy)
```

## Related
- [[gcoop-mcc]] — ภาพรวม MCC
- [[gcoop-mcc-web-system]] — Web layer ที่เรียก PB ผ่าน WCF
- [[gcoop-mcc-infrastructure]] — PLSQL + Deployment ที่ PB ใช้
- [[gcoop-mcc-integration-architecture]] — Integration patterns
