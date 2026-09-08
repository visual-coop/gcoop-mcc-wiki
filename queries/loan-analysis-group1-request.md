---
title: ระบบสินเชื่อ — วิเคราะห์ End-to-End (กลุ่ม 1: คำร้องขอกู้)
created: 2026-09-07
updated: 2026-09-07
type: query
tags: [loan, aspnet, oracle, powerbuilder, wcf]
sources:
  - raw/documents/core-loanservice-cs.md
  - raw/documents/core-loancollservice-cs.md
  - raw/documents/mhd-plsql-n-pk-lnnpl.md
confidence: high
---

# วิเคราะห์ระบบสินเชื่อ — กลุ่ม 1: คำร้องขอกู้

**วันที่วิเคราะห์:** 2026-09-07
**Source:** `/mhd/GCOOP/Saving/Applications/loan/`

---

## 1. ภาพรวมกลุ่ม

กลุ่มนี้ครอบคลุมการ**ยื่นคำขอกู้**ทุกรูปแบบ รวมถึงการทำสัญญาเบื้องต้น

รูปแบบสถาปัตยกรรมที่พบ: **WCF-heavy** — ทุกการคำนวณสำคัญ (ดอกเบี้ย, วงเงิน, ค้ำประกัน) ผ่าน `wcf.NLoan.*` และ `wcf.NCommon.*` ไม่มี `runProcessingExtend`/`runProcessing` ในกลุ่มนี้เลย

---

## 2. ดัชนีหน้าจอ — กลุ่มคำร้องขอกู้

| ลำดับ | ชื่อหน้าจอ | วัตถุประสงค์ | child_dialog | PB Service ที่เรียก | ตารางหลัก | Confidence | Needs Review |
|---|---|---|---|---|---|---|---|
| 1 | ws_lon_reqloan | ยื่นคำขอกู้ปกติ | wd_lon_rightcoll, wd_lon_mthoth_minus, wd_lon_mthoth_plus, wd_lon_openloanreq (dlg/) | wcf.NLoan.of_calperiodpay, of_isvalidcoll, of_isvalidcoll2, of_getcollpermiss, of_getcollusecontamt, of_getcollusereqamt, of_getgrplonpermiss, of_computeinterest, of_computeinterest2, of_checkcollmancount; wcf.NCommon.of_getnewdocno, of_getpostingdate, of_getpostingdate2, of_isworkingdate; wcf.Shrlon.of_roundmoney | lnreqloan, lnreqloancoll, lnreqloanclr, lnreqloanclrother, lnloantype, lnloanconstant, lnloantypeperiod, lnloantypecustom, mbmembmaster | High | true |
| 2 | ws_lon_reqloanext | ยื่นคำขอกู้พิเศษ (External) | - | wcf.NLoan.of_calperiodpay, of_calinstallment, of_isvalidcoll, of_getcollpermiss, of_getcollusecontamt, of_getcollusereqamt, of_getgrplonpermiss, of_computeinterest, of_computeinterest2, of_checkcollmancount; wcf.NCommon.of_getnewdocno, of_getpostingdate | lnreqloan (lcreqloan), lnreqloancoll, lnreqloanclr, lnreqloanclrother, lnloantype, lnloantypeperiod, lnloantypecustom | High | false |
| 3 | ws_lon_reqloan_adjust_onlne | ปรับปรุงคำขอกู้ Online | - | wcf.NLoan.of_calperiodpay, of_computeinterest, of_getgrplonpermiss; wcf.NCommon.of_getnewdocno, of_getpostingdate, of_getpostingdate2, of_isworkingdate; wcf.Shrlon.of_roundmoney | lnreqloancolladjust, lnreqloanclrotheradjust, lnreqloanclr, lnloantype, lnloantypeperiod, lnloantypecustom | High | false |
| 4 | ws_lon_promise | ทำสัญญา/แก้ไขสัญญา | - | wcf.NLoan.of_getcollpermiss, of_getcollusecontamt, of_getcollusereqamt, of_checkcollmancount | lncontmaster, lncontcoll, lnloantype, lnucfadjustcause | High | true |

---

## 3. WCF Service Mapping (wcf.NLoan → lncoopsrv.pbl)

| wcf method | .sru ที่ implement | DataWindow (.srd) | ตาราง (lc* = view ของ ln*) |
|---|---|---|---|
| of_calperiodpay | n_cst_lncoopsrv_calperiod.sru | - | - |
| of_computeinterest, of_computeinterest2 | n_cst_lncoopsrv_interest.sru | - | - |
| of_isvalidcoll, of_isvalidcoll2 | n_cst_lncoopsrv_allrequest.sru | - | mbmembmaster |
| of_getcollpermiss, of_getcollusecontamt, of_getcollusereqamt | n_cst_lncoopsrv_allrequest.sru | - | - |
| of_getgrplonpermiss | n_cst_lncoopsrv_allrequest.sru | - | - |
| of_initlnrcv, of_saveslip_lnrcv | n_cst_lncoopsrv_lnoperate.sru | d_lcsrv_loanreq.srd → lcreqloan | lcreqloan |
| of_initpayin, of_saveslip_payin | n_cst_lncoopsrv_lnoperate.sru | d_lcsrv_slippayin.srd → lcslippayin | lcslippayin |

> **หมายเหตุ:** prefix `lc` ใน DataWindow = view/alias ของตาราง `ln` จริงในระบบ (lnreqloan → lcreqloan เป็นต้น)
> SQL body ใน .sru ไม่พบ direct SQL (ใช้ DataWindow Object update แทน raw SQL) — Confidence: Medium สำหรับ SQL ระดับ PB

---

## 4. กระบวนการทำงาน

```
สมาชิก → ws_lon_reqloan → ตรวจสอบ lnloantype (config วงเงิน/ดอกเบี้ย)
                        → wcf.NLoan.of_isvalidcoll (ตรวจค้ำประกัน)
                        → wcf.NLoan.of_calperiodpay (คำนวณงวด)
                        → wcf.NLoan.of_computeinterest (คำนวณดอกเบี้ย)
                        → บันทึก lnreqloan + lnreqloancoll + lnreqloanclr
                        ↓
                       wcf.NCommon.of_getnewdocno → เลขที่ใบขอกู้
                        ↓
                    ws_lon_promise → ดึง lncontmaster → บันทึก lncontcoll
```

---

## 5. จุดที่ต้องให้ Business User ยืนยัน

| หน้าจอ | ประเด็น |
|---|---|
| ws_lon_reqloan | line 470: hardcode ข้อความ "ผู้ค้ำ...อายุสมาชิกของผู้ค้ำต้องมีอายุไม่น้อยกว่า 72เดือน (6ปี)" — ตัวเลขนี้ hardcode หรือดึงจาก config? |
| ws_lon_reqloan | line 217: hardcode เงื่อนไข "เงินกู้ประเภทสามัญอาวุโส ยอดขอกู้ ต้องหลักพัน หรือ 500" — มาจาก lnloantype config หรือ hardcode? |
| ws_lon_reqloan | line 807/924: `exe.AddFormView(dsDetail, ExecuteType.Insert/Update)` — มี transaction ครอบหรือไม่? ถ้า insert lnreqloan สำเร็จแต่ insert lnreqloancoll ล้มเหลว จะ rollback ได้ไหม? |
| ws_lon_reqloan | line 2021: มี comment `//this.SetOnLoadedScript("Gcoop.OpenIFrame2(...)")` — comment ออกแล้วใช้ `OpenIFrame2Extend` แทน ต้องการยืนยันว่า OpenIFrame2 เป็น dead code จริง |
| ws_lon_promise | line 769: `delete lncontcoll where coop_id = {0} and loancontract_no = {1}` — delete-then-insert โดยไม่มี transaction ครอบที่เห็นชัด (Needs Review) |

---

## 6. Needs Review

- **ws_lon_reqloan** — `wd_lon_rightcoll.aspx` ถูก reference ที่ line 2023 แต่ไม่พบโฟลเดอร์นี้ใน `dlg/` — อาจอยู่ใต้ path อื่น ต้องตรวจสอบ
- **ws_lon_promise → lncontcoll** — delete-then-insert pattern ที่ line 769-776 ไม่มี explicit transaction ครอบที่พบในโค้ด — risk data integrity
- **wcf.NLoan SQL layer** — `.sru` ใช้ DataWindow update (ไม่มี raw SQL) ทำให้ trace ตาราง target จาก PB layer ได้เพียง Confidence=Medium สำหรับตาราง write path

---

## Related
- [[gcoop-loan-system]] — ภาพรวมระบบเงินกู้
- [[gcoop-loan-collateral]] — ระบบค้ำประกัน
- [[gcoop-mhd]] — ระบบ MHD
