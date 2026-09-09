---
title: ระบบ Core Infrastructure GCOOP
created: 2026-09-07
updated: 2026-09-09
type: entity
tags: [architecture, wcf, powerbuilder, deployment]
sources: []
confidence: high
---

# ระบบ Core Infrastructure GCOOP

Core ประกอบด้วย WCF Services, PB Process, และ infrastructure tools ที่ share ระหว่างสหกรณ์ต่างๆ

## Path Structure

| Path | เนื้อหา |
|---|---|
| `core/GCOOP/PBService125/` | WCF PB Services (lncoopsrv.pbl, commonsrv.pbl, depositsrv.pbl ฯลฯ) |
| `core/GCOOP/PBProcess/` | Core PB Process (ไม่มี MHD-specific process) |
| `core/.output/` | Analysis output (MD + HTML) |

## PBService125 — WCF Services

| .pbl | Service Name | .sru หลัก |
|---|---|---|
| lncoopsrv.pbl | wcf.NLoan | n_cst_lncoopsrv_lnoperate.sru, n_cst_lncoopsrv_interest.sru, n_cst_lncoopsrv_allrequest.sru, n_cst_lncoopsrv_calperiod.sru, n_cst_lncoopsrv_mboperate.sru |
| commonsrv.pbl | wcf.NCommon | of_getnewdocno, of_getpostingdate, of_isworkingdate, of_lastdayofmonth, of_gennewdocnocustom |
| depositsrv.pbl | wcf.NDeposit | of_withdraw_deposit_trans, of_withdraw_loan |

## ข้อสำคัญ — File Encoding

- **PB Classic (.sru/.srd):** UTF-16LE with BOM — decode ด้วย `data.decode('utf-16le', errors='replace')`
- ทุกไฟล์ขึ้นต้นด้วย `$PBExportHeader$`
- prefix `lc*` ใน DataWindow = view/alias ของตาราง `ln*` จริง

## PBProcess — Core vs MHD

| Process | Core | MHD |
|---|---|---|
| LNPOSTTRNPAYIN | ✅ | ✅ |
| LNPAYPREPARE | ✅ | ✅ |
| SHRLONBAL | ✅ | ✅ |
| DPTRANDEPT | ✅ | ❌ |
| TARRMTHNPL | ✅ | ❌ (MHD ใช้ PROCSETARRMTHNPL แทน) |
| PROCSETINTRETURNMHD | ❌ | ✅ |
| PROCSETARRMTHNPL | ❌ | ✅ |

## Related
- [[gcoop-overview]] — ภาพรวม GCOOP
- [[gcoop-mhd]] — ระบบ MHD
- [[gcoop-loan-system]] — ระบบเงินกู้ (ใช้ WCF services เหล่านี้)
