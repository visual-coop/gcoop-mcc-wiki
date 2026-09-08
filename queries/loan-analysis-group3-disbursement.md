---
title: ระบบสินเชื่อ — วิเคราะห์ End-to-End (กลุ่ม 3: เบิกจ่าย/รับเงิน)
created: 2026-09-07
updated: 2026-09-07
type: query
tags: [loan, aspnet, oracle, wcf]
sources: []
confidence: high
---

# วิเคราะห์ระบบสินเชื่อ — กลุ่ม 3: เบิกจ่าย/รับเงิน

**วันที่วิเคราะห์:** 2026-09-07

---

## 2. ดัชนีหน้าจอ — กลุ่มเบิกจ่าย/รับเงิน

| ลำดับ | ชื่อหน้าจอ | วัตถุประสงค์ | child_dialog | PB Service ที่เรียก | ตารางหลัก | Confidence | Needs Review |
|---|---|---|---|---|---|---|---|
| 8 | ws_lon_rcvloan | บันทึกการเบิกรับเงินกู้ (ผู้กู้รับเงิน) | wd_lon_rcvdetail_ctrl, wd_lon_rcvlon_ctrl, w_dlg_loan_search_receive_ctrl | CoreSavingLibrary.WcfNLoan (method ไม่ปรากฏ direct ใน .cs), iReportBuider | lncontmaster, lnreqloan, lnloanconstant | Medium | true |
| 9 | ws_lon_post_loan_receive | Post รายการรับเงินกู้ (บัญชี) | - | wcf.NLoan.of_saveslip_postlnrcv | lnpostlist_lnrcv (ผ่าน d_lcsrv_postlist_lnrcv.srd), lnloanconstant | Medium | true |
| 10 | ws_lon_post_loan_pxpmp | Post รับเงินกู้ประเภท PX/PMP | - | - (Direct SQL) | sltranspayin | High | true |
| 11 | ws_lon_paintloan | พิมพ์เอกสารเงินกู้ (report) | - | WebUtil.runProcessingReport (gid=LOAN_DAILY, rid=LNDY0001) | lncontmaster, lnreqloan | Medium | false |

---

## 3. รายละเอียด WCF/Service

### ws_lon_rcvloan
- ใช้ `CoreSavingLibrary.WcfNLoan` (namespace import) แต่ไม่พบ direct `wcf.NLoan.of_xxx` calls ใน .cs
- พิมพ์ใบเสร็จผ่าน `iReportBuider` → report name hardcode: `"ir_receipt_a4_lon2p_mhd_new"`
- ข้อมูลที่แสดง: UNION SELECT จาก `lncontmaster` และ `lnreqloan`

### ws_lon_post_loan_receive
- `wcf.NLoan.of_saveslip_postlnrcv(wspass, coopid, payoutslip_no, coopcontrol)` — line 332
- ค้นหาใน lncoopsrv.pbl: **พบชื่อ** ใน n_cst_lncoopsrv_lnoperate.sru (ค้นด้วย contains) แต่ไม่พบ body ชัดเจน
- DataWindow `d_lcsrv_postlist_lnrcv.srd` → table: `lcmembmaster` (view ของ mbmembmaster)

### ws_lon_post_loan_pxpmp — Direct SQL (High Confidence)
```sql
INSERT INTO sltranspayin (
  coop_id, member_no, trans_date, seq_no, transitem_code,
  realpay_date, shrtype_code, loancontract_no, CONTCOOP_ID,
  transpay_type, trans_amt, principal_trnamt, interest_trnamt,
  sliptype_code, trnsource_code, trnsource_refslipno,
  trnsource_status, moneytype_code, post_status,
  tofrom_accid, lastcalint_date, interest_period,
  transreal_amt, trnsource_accno, period
) values (...)
```
hardcode: `sliptype_code='PX'`, `trnsource_code='DFP'`, `trnsource_status=8`, `moneytype_code='TRD'`

### ws_lon_paintloan
- `WebUtil.runProcessingReport(state, "loan", "LOAN_DAILY", "LNDY0001", criteriaXML, pdfFileName, printer)`
- อ่าน `lncontmaster` และ `lnreqloan` จาก DsList

---

## 4. กระบวนการทำงาน

```
ws_lon_rcvloan
  → แสดง lncontmaster UNION lnreqloan (ที่มีวงเงินคงเหลือ)
  → สมาชิกรับเงิน (Post ผ่าน WcfNLoan)
  → พิมพ์ใบเสร็จ ir_receipt_a4_lon2p_mhd_new

ws_lon_post_loan_receive
  → รายการที่รอ post
  → wcf.NLoan.of_saveslip_postlnrcv → บันทึก accounting

ws_lon_post_loan_pxpmp
  → INSERT INTO sltranspayin (direct SQL)
  → exe.Execute() (ไม่มี transaction ครอบชัดเจน)

ws_lon_paintloan
  → WebUtil.runProcessingReport → PBProcess LOAN_DAILY/LNDY0001
  → พิมพ์เอกสาร
```

---

## 5. จุดที่ต้องให้ Business User ยืนยัน

| หน้าจอ | ประเด็น |
|---|---|
| ws_lon_rcvloan | report hardcode `"ir_receipt_a4_lon2p_mhd_new"` — ใบเสร็จรับเงินใช้ template เดียวทุกกรณีหรือไม่? (line 199) |
| ws_lon_post_loan_pxpmp | INSERT INTO sltranspayin ต่อ loop แต่ละ row โดย exe.Execute() ครั้งเดียว — ถ้า row ที่ N ล้มเหลว rows ก่อนหน้า rollback ไหม? |
| ws_lon_post_loan_pxpmp | hardcode `trnsource_code='DFP'`, `sliptype_code='PX'` — ค่าเหล่านี้มีนัยสำคัญทางบัญชีอย่างไร? |
| ws_lon_post_loan_receive | wcf.NLoan.of_saveslip_postlnrcv — ยืนยัน implementation อยู่ที่ไหน (ไม่พบชัดเจนใน core) |

---

## 6. ความไม่สอดคล้อง

- ws_lon_rcvloan ใช้ `CoreSavingLibrary.WcfNLoan` namespace แต่ไม่มี direct `wcf.NLoan.of_xxx` calls ในโค้ด — น่าจะเรียกผ่าน base class/helper ที่ไม่อยู่ใน source นี้
- ws_lon_post_loan_pxpmp เขียน Direct SQL ตรงๆ ต่างจาก ws_lon_post_loan_receive ที่ใช้ WCF — pattern ไม่สม่ำเสมอ

---

## Related
- [[loan-analysis-group2-approval]] — กลุ่ม 2 อนุมัติ
- [[gcoop-loan-system]] — ภาพรวมระบบเงินกู้
- [[gcoop-mhd]] — ระบบ MHD
