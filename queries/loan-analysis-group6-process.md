---
title: ระบบสินเชื่อ — วิเคราะห์ End-to-End (กลุ่ม 6: กระบวนการ/อื่นๆ)
created: 2026-09-07
updated: 2026-09-07
type: query
tags: [loan, aspnet, oracle, wcf, powerbuilder]
sources: []
confidence: high
---

# วิเคราะห์ระบบสินเชื่อ — กลุ่ม 6: กระบวนการ/อื่นๆ

**วันที่วิเคราะห์:** 2026-09-07

---

## 2. ดัชนีหน้าจอ — กลุ่มกระบวนการ/อื่นๆ

| ลำดับ | ชื่อหน้าจอ | วัตถุประสงค์ | child_dialog | PB Process/Service | ตารางหลัก | Confidence | Needs Review |
|---|---|---|---|---|---|---|---|
| 23 | ws_lon_proc_moneyreturn | Process คืนเงินดอกเบี้ย | - | runProcessingExtend("PROCSETINTRETURNMHD") → n_cst_proc_? .of_proc_setmnyreturn_mhd (pcloan.pbl) | - | Medium | false |
| 24 | ws_lon_proc_paymoneyreturn | Process Post ใบสำคัญจ่ายคืนดอกเบี้ย | - | runProcessingExtend("POSTINTRETPAYOUT_POSTSTM_SUMPAY") → of_post_toslippayout_sumpay (pcloan.pbl) | - | Medium | true |
| 25 | ws_lon_post_paymoneyreturn | Post รายการคืนดอกเบี้ย | - | runProcessingExtend("POSTINTRETPAYOUT_POSTSTM") → of_post_toslippayout (pcloan.pbl) | - | Medium | false |
| 26 | ws_lon_prcshrlonbal | Process ปรับยอดหุ้น-เงินกู้ | - | runProcessingExtend("SHRLONBAL") → n_cst_proc_slshrlonbal.of_proc_shrlonbal (pcloan.pbl) | - | Medium | false |
| 27 | ws_lon_cls_process_setmb_npl | กำหนดสมาชิก NPL | - | runProcessing("PROCSETARRMTHNPL") → n_cst_sl_mth_arrear_process.of_procnpl (pcloan.pbl); wcf.NCommon.of_lastdayofmonth | - | Medium | true |
| 28 | ws_lon_caltobank | เครื่องคำนวณเงินกู้ (Calculator) | - | LoanMasterClass.CalPeriodPayment() (C# helper) | - (ไม่ write DB) | High | false |
| 29 | ws_lon_reprnmortgage | พิมพ์เอกสารจำนอง | - | iReportBuider → webreportdetail config | webreportdetail | High | false |
| 30 | ws_lon_reprntpayout | พิมพ์ใบสำคัญจ่าย | - | iReportBuider | - | High | false |
| 31 | ws_lon_docscan_datatransfer | โอนข้อมูล Document Scan | - | - (ไม่พบ call ใดๆ ใน .cs) | - | Low | true |
| 32 | ws_lon_certificateinterest | หนังสือรับรองดอกเบี้ย | - | runProcessingCappExtend("runPostProcess"), Direct SQL | lnsumintcert, lncontstatement | High | true |
| 33 | ws_imp_post_ctrl | Import + Post transaction | - | runProcessing("LNPOSTTRNPAYIN") → n_cst_proc_sltrnpayin (pcloan.pbl) | - | Medium | false |

---

## 3. PB Process Dispatcher — กลุ่ม 6 (จาก pcloan.pbl binary extract)

| Process Name | Object | Method |
|---|---|---|
| `PROCSETINTRETURNMHD` | ? (ไม่พบชื่อ object ชัด) | `of_proc_setmnyreturn_mhd` |
| `POSTINTRETPAYOUT_POSTSTM_SUMPAY` | ? | `of_post_toslippayout_sumpay` |
| `POSTINTRETPAYOUT_POSTSTM` | ? | `of_post_toslippayout` |
| `SHRLONBAL` | `n_cst_proc_slshrlonbal` | `of_proc_shrlonbal` |
| `PROCSETARRMTHNPL` | `n_cst_sl_mth_arrear_process` | `of_procnpl` |
| `LNPOSTTRNPAYIN` | `n_cst_proc_sltrnpayin` | `of_proc_trnpayin` |

---

## 4. จุดสำคัญ

### ws_lon_caltobank — Calculator (ไม่ write DB)
- hardcode `interest_rate = 5.25` (line: `dsMain.DATA[0].interest_rate = 5.25m;`) — อัตราดอกเบี้ย default hardcode
- `LoanMasterClass.CalPeriodPayment(2, 2, 1, amt, rate, period)` — C# helper ไม่ผ่าน WCF
- `SaveWebSheet()` ว่างเปล่า — ไม่ save ข้อมูลใดๆ

### ws_lon_certificateinterest — DELETE + INSERT (High Risk)
```sql
DELETE FROM lnsumintcert WHERE sum_year = '{YEAR}'  -- ลบก่อน
-- แล้ว INSERT INTO lnsumintcert ทีละรายการ
-- จาก SELECT lncontstatement
```
> ⚠️ DELETE-then-INSERT โดยไม่มี transaction ครอบชัดเจน

### ws_lon_docscan_datatransfer — Low Confidence
- .cs มี 114 บรรทัด แต่ไม่พบ runProcessing/wcf/INSERT/UPDATE เลย
- น่าจะทำงานผ่าน base class หรือ JavaScript call ที่ไม่อยู่ใน .cs

### ws_lon_cls_process_setmb_npl
- `//outputProcess = WebUtil.runProcessing(state, "LNCLOSEMONTH", ...)` — commented out!
- ใช้แค่ `PROCSETARRMTHNPL` จริง

---

## 5. จุดที่ต้องให้ Business User ยืนยัน

| หน้าจอ | ประเด็น |
|---|---|
| ws_lon_caltobank | hardcode `interest_rate = 5.25` — ควรดึงจาก config ไหม หรือ calculator ใช้ค่าสมมติ? |
| ws_lon_certificateinterest | DELETE lnsumintcert ทั้ง year แล้ว INSERT ใหม่ — ถ้า INSERT บางรายการล้มเหลว จะเกิดข้อมูลสูญหาย |
| ws_lon_cls_process_setmb_npl | `LNCLOSEMONTH` ถูก comment — ปิดปรับปรุงชั่วคราวหรือถาวร? |
| ws_lon_proc_paymoneyreturn | comment line 59-60: 2 lines commented (POSTINTRETPAYOUTEGAT) — dead code? |
| ws_lon_docscan_datatransfer | ไม่พบ business logic ใดๆ ใน .cs — หน้าจอนี้ทำงานจริงหรือเปล่า? |

---

## Related
- [[loan-analysis-group5-collateral]] — กลุ่ม 5 ค้ำประกัน
- [[loan-analysis-group4-payment]] — กลุ่ม 4 ชำระคืน
- [[gcoop-loan-system]] — ภาพรวมระบบเงินกู้
- [[gcoop-mhd]] — ระบบ MHD
