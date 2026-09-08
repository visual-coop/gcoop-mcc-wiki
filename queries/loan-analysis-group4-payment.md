---
title: ระบบสินเชื่อ — วิเคราะห์ End-to-End (กลุ่ม 4: ชำระคืน)
created: 2026-09-07
updated: 2026-09-07
type: query
tags: [loan, aspnet, oracle, wcf, powerbuilder]
sources: []
confidence: high
---

# วิเคราะห์ระบบสินเชื่อ — กลุ่ม 4: ชำระคืน

**วันที่วิเคราะห์:** 2026-09-07

---

## 2. ดัชนีหน้าจอ — กลุ่มชำระคืน

| ลำดับ | ชื่อหน้าจอ | วัตถุประสงค์ | child_dialog | PB Service/Process | ตารางหลัก | Confidence | Needs Review |
|---|---|---|---|---|---|---|---|
| 12 | ws_lon_payment | บันทึกชำระเงินกู้ (รายเดือน) | - | wcf.NLoan.of_initslippayin, of_initslippayin_recalint, of_saveslip_payin, of_calfeeloanpayment; wcf.NCommon.of_getnewdocno, of_getnextworkday; wcf.NDeposit.of_withdraw_deposit_trans, of_withdraw_loan; WebUtil.runProcessingFin | lncontmaster, lncffeedet, lncffee, lnloanconstant, slslippayindet (ผ่าน WCF) | Medium | false |
| 13 | ws_lon_cclpayin | ยกเลิกใบเสร็จชำระ (draft) | - | wcf.NLoan (of_saveccl_payin — commented out!) | slslippayindet, slucfslipitemtype | Medium | true |
| 14 | ws_lon_cclpayin_apv | อนุมัติยกเลิกใบเสร็จชำระ | - | wcf.NLoan.of_saveccl_payin | slslippayindet | Medium | false |
| 15 | ws_lon_cclloanrcv_apv | อนุมัติยกเลิกการรับเงินกู้ | - | wcf.NLoan.of_saveccl_lnrcv | - | Medium | false |
| 16 | ws_lon_prctrnpayin | Process โอน transaction ชำระ | - | WebUtil.runProcessing("LNPOSTTRNPAYIN") → n_cst_proc_sltrnpayin.of_proc_trnpayin (pcloan.pbl) | lnstm, lncont, lnpayin (พบใน pcloan.pbl binary) | Medium | false |
| 17 | ws_lon_prc_preparepay | เตรียมข้อมูลชำระ | - | WebUtil.runProcessing("LNPAYPREPARE") → n_cst_proc_lnprepare.of_proc_lnprepare (pcloan.pbl) | - | Medium | false |

---

## 3. PB Process Dispatcher Mapping (จาก pcloan.pbl binary extract)

| Process Name | Object | Method |
|---|---|---|
| `LNPOSTTRNPAYIN` | `n_cst_proc_sltrnpayin` | `of_proc_trnpayin` |
| `LNPAYPREPARE` | `n_cst_proc_lnprepare` | `of_proc_lnprepare` |

> pcloan.pbl เป็น binary (.pbl) แต่ extract text สำเร็จ — พบ dispatcher table และ object mapping
> SQL body ใน n_cst_proc_sltrnpayin ยืนยันว่า touch tables: `lnstm`, `lncont`, `lnpayin` — Confidence: Medium

---

## 4. กระบวนการชำระคืน

```
ws_lon_payment (บันทึกชำระ)
  → wcf.NLoan.of_initslippayin → โหลดข้อมูลสัญญา
  → [คำนวณ] wcf.NLoan.of_calfeeloanpayment
  → wcf.NLoan.of_saveslip_payin(wspass, ref strslip) → บันทึก slippayin
  → wcf.NDeposit.of_withdraw_loan (ถ้าชำระจากเงินฝาก)
  → WebUtil.runProcessingFin → FinService process

ws_lon_cclpayin / ws_lon_cclpayin_apv (ยกเลิกใบเสร็จ)
  → ดึง slslippayindet (รายละเอียดใบเสร็จ)
  → wcf.NLoan.of_saveccl_payin(wspass, slipcancel) → ยกเลิก

ws_lon_prctrnpayin
  → WebUtil.runProcessing("LNPOSTTRNPAYIN", xml)
  → pcloan.pbl dispatcher → n_cst_proc_sltrnpayin.of_proc_trnpayin
  → UPDATE lnstm + lncont

ws_lon_prc_preparepay
  → WebUtil.runProcessing("LNPAYPREPARE", xml_option, xml_option_lntype)
  → pcloan.pbl dispatcher → n_cst_proc_lnprepare.of_proc_lnprepare
```

---

## 5. จุดที่ต้องให้ Business User ยืนยัน

| หน้าจอ | ประเด็น |
|---|---|
| ws_lon_cclpayin | line 110: `// int result = loanService.of_saveccl_payin(...)` — commented out! การยกเลิกใบเสร็จทำงานอย่างไรถ้า call หลักถูก comment? (Needs Review) |
| ws_lon_payment | wcf.NDeposit.of_withdraw_loan และ of_withdraw_deposit_trans — เรียกเมื่อไหร่? ขึ้นกับเงื่อนไขอะไร? |
| ws_lon_payment | WebUtil.runProcessingFin — เรียก FinService อะไร และ trigger เงื่อนไขใด? |

---

## 6. ความไม่สอดคล้อง

- ws_lon_cclpayin: `of_saveccl_payin` ถูก **comment out** (line 110) แต่ ws_lon_cclpayin_apv มีการเรียกจริง (line 115) — อาจหมายความว่า cclpayin เป็นแค่หน้าจอ preview/list ส่วน apv ทำการบันทึกจริง
- ws_lon_payment เรียกทั้ง WCF (NLoan, NDeposit) และ runProcessingFin — mixed pattern

---

## Related
- [[loan-analysis-group3-disbursement]] — กลุ่ม 3 เบิกจ่าย
- [[gcoop-loan-system]] — ภาพรวมระบบเงินกู้
- [[gcoop-mhd]] — ระบบ MHD
