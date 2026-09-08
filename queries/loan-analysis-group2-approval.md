---
title: ระบบสินเชื่อ — วิเคราะห์ End-to-End (กลุ่ม 2: อนุมัติ)
created: 2026-09-07
updated: 2026-09-07
type: query
tags: [loan, aspnet, oracle, wcf]
sources: []
confidence: high
---

# วิเคราะห์ระบบสินเชื่อ — กลุ่ม 2: อนุมัติ

**วันที่วิเคราะห์:** 2026-09-07

---

## 2. ดัชนีหน้าจอ — กลุ่มอนุมัติ

| ลำดับ | ชื่อหน้าจอ | วัตถุประสงค์ | child_dialog | PB Service ที่เรียก | ตารางหลัก | Confidence | Needs Review |
|---|---|---|---|---|---|---|---|
| 5 | ws_lon_apvloan | อนุมัติคำขอกู้ (ผู้มีอำนาจอนุมัติ) | - | wcf.NLoan.of_saveapv_lnreq, of_gennewcontractno, of_computeinterest; wcf.NCommon.of_gennewdocnocustom | lnreqloan, lncontmaster | Medium | true |
| 6 | ws_lon_approve_loan | อนุมัติคำขอกู้ (ระบบ batch) | - | wcf.NLoan.of_saveapv_lnreq, of_gennewcontractno | lnreqloan | Medium | true |
| 7 | ws_lon_cfloantype | ตั้งค่าประเภทเงินกู้ | - | - (Direct SQL ทั้งหมด) | lnloantype, lnloantypecolluse, lnloantyperightcoll, lnloantypecustom, lncfgrplnperm, lncfloanintrate, lncfintpromotion, lncfsalarybalance, lnucfloanobjective, lnucfcollmasttype, lnucfloancolltype | High | false |

---

## 3. WCF: of_saveapv_lnreq

`wcf.NLoan.of_saveapv_lnreq` — **ไม่พบ** ใน `.sru` ทุกไฟล์ของ `lncoopsrv.pbl`
→ อาจถูก implement ใน PBService ของ SWU/MHD extension (ไม่ได้อยู่ใน core/) หรือถูก define ใน lncoopsrv.pblx

> **Needs Review** — Confidence = Medium: trace ถึง WCF call ได้ แต่หา implementation ใน PB ไม่เจอ

`wcf.NLoan.of_gennewcontractno` — พบใน `n_cst_lncoopsrv_lnoperate.sru` (signature เท่านั้น, body ไม่เปิดเผย SQL ตรงๆ)

---

## 4. กระบวนการอนุมัติ

```
ws_lon_apvloan / ws_lon_approve_loan
  → แสดงรายการ lnreqloan (status รอดอนุมัติ)
  → ผู้ใช้เลือก/อนุมัติ
  → wcf.NLoan.of_saveapv_lnreq(wspass, docnos, username, workdate)
  → wcf.NLoan.of_gennewcontractno → สร้างเลขสัญญา lncontmaster
  → บันทึก loanrequest_status = อนุมัติ
```

```
ws_lon_cfloantype (Direct SQL)
  → อ่าน/แก้ไข lnloantype (config ทุกด้าน)
  → exe.Execute() → UPDATE lnloantype + related tables
  → DELETE + INSERT lnloantypecustom (line 380-425)
  → INSERT lnloantypecolluse (line 479-493)
```

---

## 5. จุดที่ต้องให้ Business User ยืนยัน

| หน้าจอ | ประเด็น |
|---|---|
| ws_lon_apvloan | line 266: `if (lncont_status == 1)` — เงื่อนไขนี้หมายถึงอะไร? และส่งผลต่อ flow อนุมัติอย่างไร? |
| ws_lon_approve_loan | line 419: สร้าง contract_no ใหม่ด้วย `wcf.NLoan.of_gennewcontractno` — สร้างใน flow ปกติหรือเฉพาะกรณีพิเศษ? |
| ws_lon_cfloantype | line 380: `DELETE LNLOANTYPECUSTOM` โดยไม่มี transaction ครอบที่เห็น — ถ้า INSERT ล้มเหลวหลัง DELETE จะ rollback ได้ไหม? |

---

## 6. ความไม่สอดคล้อง

- `wcf.NLoan.of_saveapv_lnreq` ถูกเรียกใน 2 หน้าจอ (apvloan + approve_loan) แต่ implement ไม่พบใน core lncoopsrv.pbl — อาจอยู่ใน SWU extension PBService ที่ไม่ได้ checkout
- ws_lon_cfloantype ไม่ใช้ WCF เลย ต่างจาก ws_lon_apvloan ที่ WCF-heavy — pattern ไม่สม่ำเสมอในกลุ่มอนุมัติ

---

## Related
- [[loan-analysis-group1-request]] — กลุ่ม 1 คำร้องขอกู้
- [[gcoop-loan-system]] — ภาพรวมระบบเงินกู้
- [[gcoop-mhd]] — ระบบ MHD
