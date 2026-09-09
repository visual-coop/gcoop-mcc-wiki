---
title: ระบบเงินกู้ GCOOP MHD
created: 2026-09-07
updated: 2026-09-09
type: entity
tags: [loan, aspnet, oracle, powerbuilder, wcf, workflow]
sources:
  - queries/loan-analysis-group1-request.md
  - queries/loan-analysis-group2-approval.md
  - queries/loan-analysis-group3-disbursement.md
  - queries/loan-analysis-group4-payment.md
  - queries/loan-analysis-group5-collateral.md
  - queries/loan-analysis-group6-process.md
  - queries/ระบบสินเชื่อ_Analysis_20260908.md
confidence: high
---

# ระบบเงินกู้ GCOOP MHD

ระบบสินเชื่อ (Loan System) ของ GCOOP MHD ครอบคลุมกระบวนการตั้งแต่ยื่นคำขอกู้จนถึงปิดบัญชีและคืนดอกเบี้ย

**วันที่วิเคราะห์:** 2026-09-08
**หน้าจอหลัก:** 33 หน้าจอ (ws_*) + 13 child_dialog (wd_*)
**Path UI:** `/mhd/GCOOP/Saving/Applications/loan/`

---

## สถาปัตยกรรมระบบ

| Pattern | จำนวนหน้าจอ | สัดส่วน |
|---|---|---|
| WCF-heavy (wcf.NLoan.* + wcf.NCommon.*) | 14 | 42% |
| runProcessingExtend (MHD PBProcess) | 5 | 15% |
| runProcessing (Core PBProcess) | 5 | 15% |
| Direct SQL (WebUtil.QuerySdt / ExecuteDataSource) | 6 | 18% |
| Mixed (WCF + Direct SQL หรือ WCF + runProcessing) | 3 | 9% |

**ข้อสังเกต:** Pattern ไม่สม่ำเสมอ — หน้าจอในกลุ่มเดียวกันบางตัวใช้ WCF บางตัวใช้ Direct SQL โดยไม่มีเหตุผลชัดเจน

---

## กลุ่มหน้าจอ (6 กลุ่ม)

### A: คำร้องขอกู้ (4 หน้าจอ)
→ ดูรายละเอียด [[loan-analysis-group1-request]]

| หน้าจอ | วัตถุประสงค์ | Pattern | Confidence |
|---|---|---|---|
| ws_lon_reqloan | ยื่นคำขอกู้ปกติ | WCF-heavy | High |
| ws_lon_reqloanext | ยื่นคำขอกู้พิเศษ (External) | WCF-heavy | High |
| ws_lon_reqloan_adjust_onlne | ปรับปรุงคำขอกู้ Online | WCF-heavy | High |
| ws_lon_promise | ทำสัญญา/แก้ไขสัญญา | WCF | High |

### B: อนุมัติ (3 หน้าจอ)
→ ดูรายละเอียด [[loan-analysis-group2-approval]]

| หน้าจอ | วัตถุประสงค์ | Pattern | Confidence |
|---|---|---|---|
| ws_lon_apvloan | อนุมัติคำขอกู้ | WCF | Medium |
| ws_lon_approve_loan | อนุมัติคำขอกู้ (batch) | WCF | Medium |
| ws_lon_cfloantype | ตั้งค่าประเภทเงินกู้ | Direct SQL | High |

### C: เบิกจ่าย/รับเงิน (4 หน้าจอ)
→ ดูรายละเอียด [[loan-analysis-group3-disbursement]]

| หน้าจอ | วัตถุประสงค์ | Pattern | Confidence |
|---|---|---|---|
| ws_lon_rcvloan | บันทึกรับเงินกู้ | WCF + iReport | Medium |
| ws_lon_post_loan_receive | Post รายการรับเงิน (บัญชี) | WCF | Medium |
| ws_lon_post_loan_pxpmp | Post รับเงินกู้ PX/PMP | Direct SQL | High |
| ws_lon_paintloan | พิมพ์เอกสารเงินกู้ | runProcessingReport | Medium |

### D: ชำระคืน (6 หน้าจอ)
→ ดูรายละเอียด [[loan-analysis-group4-payment]]

| หน้าจอ | วัตถุประสงค์ | Pattern | Confidence |
|---|---|---|---|
| ws_lon_payment | บันทึกชำระเงินกู้ | WCF + runProcessingFin | Medium |
| ws_lon_cclpayin | ยกเลิกใบเสร็จชำระ | WCF (commented!) | Medium |
| ws_lon_cclpayin_apv | อนุมัติยกเลิกใบเสร็จ | WCF | Medium |
| ws_lon_cclloanrcv_apv | อนุมัติยกเลิกรับเงินกู้ | WCF | Medium |
| ws_lon_prctrnpayin | Process โอน transaction ชำระ | runProcessing(LNPOSTTRNPAYIN) | Medium |
| ws_lon_prc_preparepay | เตรียมข้อมูลชำระ | runProcessing(LNPAYPREPARE) | Medium |

### E: ค้ำประกัน/หลักทรัพย์ (5 หน้าจอ)
→ ดูรายละเอียด [[loan-analysis-group5-collateral]]

| หน้าจอ | วัตถุประสงค์ | Pattern | Confidence |
|---|---|---|---|
| ws_lon_collateral_master | บันทึกหลักทรัพย์ค้ำประกัน | Direct SQL + wcf.NCommon | High |
| ws_lon_collateral_mastdet | ดูรายละเอียดหลักทรัพย์ | Direct SQL + wcf.NCommon | High |
| ws_lon_collateral_car | บันทึกหลักทรัพย์รถยนต์ | Direct SQL + wcf.NCommon | High |
| ws_lon_collredeem | ขอไถ่ถอนหลักทรัพย์ | Direct SQL | High |
| ws_lon_trnsloancoll | โอนสัญญาค้ำประกัน | WCF (of_savetrn_lntrnres) | Medium |

### F: กระบวนการ/อื่นๆ (11 หน้าจอ)
→ ดูรายละเอียด [[loan-analysis-group6-process]]

| หน้าจอ | วัตถุประสงค์ | Pattern | Confidence |
|---|---|---|---|
| ws_lon_proc_moneyreturn | Process คืนเงินดอกเบี้ย | runProcessingExtend(PROCSETINTRETURNMHD) | Medium |
| ws_lon_proc_paymoneyreturn | Post ใบสำคัญจ่ายคืนดอกเบี้ย | runProcessingExtend(POSTINTRETPAYOUT_POSTSTM_SUMPAY) | Medium |
| ws_lon_post_paymoneyreturn | Post รายการคืนดอกเบี้ย | runProcessingExtend(POSTINTRETPAYOUT_POSTSTM) | Medium |
| ws_lon_prcshrlonbal | Process ปรับยอดหุ้น-เงินกู้ | runProcessingExtend(SHRLONBAL) | Medium |
| ws_lon_cls_process_setmb_npl | กำหนดสมาชิก NPL | runProcessing(PROCSETARRMTHNPL) | Medium |
| ws_lon_caltobank | เครื่องคำนวณเงินกู้ | C# helper (ไม่ write DB) | High |
| ws_lon_reprnmortgage | พิมพ์เอกสารจำนอง | iReportBuilder | High |
| ws_lon_reprntpayout | พิมพ์ใบสำคัญจ่าย | iReportBuilder | High |
| ws_lon_docscan_datatransfer | โอนข้อมูล Document Scan | ไม่พบ logic ใดๆ | Low |
| ws_lon_certificateinterest | หนังสือรับรองดอกเบี้ย | runProcessingCappExtend + Direct SQL | High |
| ws_imp_post_ctrl | Import + Post transaction | runProcessing(LNPOSTTRNPAYIN) | Medium |

---

## Data Flow หลัก

```
[A] ยื่นคำร้อง → lnreqloan (WCF-heavy: NLoan, NCommon)
        ↓
[B] อนุมัติ → lncontmaster เกิดขึ้น (wcf.NLoan.of_saveapv_lnreq)
        ↓
[E] ค้ำประกัน → lncollmaster, lncontcoll (Direct SQL + wcf.NCommon)
        ↓
[C] เบิกจ่าย → sltranspayin, slslippayout (WCF + Direct SQL)
        ↓
[D] ชำระคืน → slslippayindet, lnstm (WCF + runProcessing)
        ↓
[F] กระบวนการปิด → lnsumintcert (runProcessingExtend + Direct SQL)
```

---

## ตารางข้อมูลหลัก

| ตาราง | ใช้ในกลุ่ม | บทบาท |
|---|---|---|
| lnreqloan | A, B | คำขอกู้ |
| lncontmaster | B, C, D, E, F | สัญญาเงินกู้หลัก |
| lncontcoll | A, E | ค้ำประกันต่อสัญญา |
| lnreqloancoll | A | ค้ำประกันในคำขอ |
| lnreqloanclr | A | ปิดกู้เดิมในคำขอ |
| lncollmaster / lncolldetail | E | หลักทรัพย์ค้ำประกัน |
| lncollcarmaster | E | หลักทรัพย์รถยนต์ |
| lnreqcollmastredeem | E | คำขอไถ่ถอนหลักทรัพย์ |
| lnloantype + ~13 child tables | A, B | config ประเภทเงินกู้ |
| lnloanconstant | A, C, D | config ทั่วไปของระบบ |
| slslippayindet / slslippayin | D | รายละเอียดใบเสร็จชำระ |
| sltranspayin | C, D | transaction การรับเงิน |
| lnsumintcert / lncontstatement | F | หนังสือรับรองดอกเบี้ย |
| mbmembmaster | A | ข้อมูลสมาชิก |
| webreportdetail | E, F | config รายงาน |

> **หมายเหตุ:** prefix `lc*` ใน DataWindow = view/alias ของตาราง `ln*` จริง (เช่น lcreqloan = lnreqloan)

---

## WCF Services ที่ใช้

| wcf Service | .pbl | .sru หลัก |
|---|---|---|
| wcf.NLoan | lncoopsrv.pbl | n_cst_lncoopsrv_lnoperate.sru, n_cst_lncoopsrv_interest.sru, n_cst_lncoopsrv_allrequest.sru, n_cst_lncoopsrv_calperiod.sru |
| wcf.NCommon | commonsrv.pbl | of_getnewdocno, of_getpostingdate, of_isworkingdate, of_lastdayofmonth |
| wcf.NDeposit | depositsrv.pbl | of_withdraw_deposit_trans, of_withdraw_loan |
| wcf.Shrlon | lncoopsrv.pbl | of_roundmoney |

**⚠️ wcf.NLoan.of_saveapv_lnreq และ of_savetrn_lntrnres** — ไม่พบใน core lncoopsrv.pbl — น่าจะอยู่ใน MHD-specific extension PBService ที่ยังไม่ได้ checkout

---

## MHD-Specific PBProcess

| Process | Object | Method | Core? |
|---|---|---|---|
| PROCSETINTRETURNMHD | ? | of_proc_setmnyreturn_mhd | MHD เท่านั้น |
| PROCSETARRMTHNPL | n_cst_sl_mth_arrear_process | of_procnpl | MHD เท่านั้น (Core ใช้ TARRMTHNPL) |
| LNPOSTTRNPAYIN | n_cst_proc_sltrnpayin | of_proc_trnpayin | MHD + Core |
| LNPAYPREPARE | n_cst_proc_lnprepare | of_proc_lnprepare | MHD + Core |
| SHRLONBAL | n_cst_proc_slshrlonbal | of_proc_shrlonbal | MHD + Core |
| POSTINTRETPAYOUT_POSTSTM_SUMPAY | ? | of_post_toslippayout_sumpay | MHD + Core |
| POSTINTRETPAYOUT_POSTSTM | ? | of_post_toslippayout | MHD + Core |

---

## Dead Code ที่พบ

| ไฟล์ | Dead Code |
|---|---|
| ws_lon_reqloan.aspx.cs line 2021 | `//Gcoop.OpenIFrame2(...)` — ใช้ OpenIFrame2Extend แทน |
| ws_lon_cclpayin.aspx.cs line 110 | `// int result = loanService.of_saveccl_payin(...)` — save call หลักถูก comment |
| ws_lon_cls_process_setmb_npl.aspx.cs line 78 | `//runProcessing("LNCLOSEMONTH", ...)` |
| ws_lon_proc_paymoneyreturn.aspx.cs lines 59-60 | `//POSTINTRETPAYOUTEGAT` สองบรรทัด |

---

## Related
- [[gcoop-loan-collateral]] — Logic การตรวจสอบค้ำประกันโดยละเอียด
- [[gcoop-mhd]] — ระบบ MHD ภาพรวม
- [[loan-analysis-group1-request]]
- [[loan-analysis-group2-approval]]
- [[loan-analysis-group3-disbursement]]
- [[loan-analysis-group4-payment]]
- [[loan-analysis-group5-collateral]]
- [[loan-analysis-group6-process]]
