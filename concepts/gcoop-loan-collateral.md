---
title: Logic การตรวจสอบค้ำประกันเงินกู้ GCOOP
created: 2026-09-07
updated: 2026-09-09
type: concept
tags: [loan, oracle, wcf, powerbuilder]
sources:
  - queries/loan-analysis-group1-request.md
  - queries/loan-analysis-group5-collateral.md
  - queries/ระบบสินเชื่อ_Analysis_20260908.md
confidence: high
---

# Logic การตรวจสอบค้ำประกันเงินกู้

ระบบสินเชื่อ GCOOP MHD มีกระบวนการตรวจสอบค้ำประกัน 2 ระดับ: (1) ตอนยื่นคำขอกู้ และ (2) ตอนบริหารหลักทรัพย์จริง

---

## 1. การตรวจสอบค้ำประกันตอนยื่นคำขอ (ws_lon_reqloan)

### WCF Methods ที่เรียกใน ws_lon_reqloan

| WCF Method | .sru | วัตถุประสงค์ |
|---|---|---|
| wcf.NLoan.of_isvalidcoll | n_cst_lncoopsrv_allrequest.sru | ตรวจสอบความถูกต้องของผู้ค้ำ |
| wcf.NLoan.of_isvalidcoll2 | n_cst_lncoopsrv_allrequest.sru | ตรวจสอบผู้ค้ำ (กรณีที่ 2) |
| wcf.NLoan.of_getcollpermiss | n_cst_lncoopsrv_allrequest.sru | ดึงสิทธิ์ค้ำประกัน |
| wcf.NLoan.of_getcollusecontamt | n_cst_lncoopsrv_allrequest.sru | วงเงินค้ำที่ใช้ไปแล้ว (ต่อสัญญา) |
| wcf.NLoan.of_getcollusereqamt | n_cst_lncoopsrv_allrequest.sru | วงเงินค้ำที่ใช้ไปในคำขอ |
| wcf.NLoan.of_getgrplonpermiss | n_cst_lncoopsrv_allrequest.sru | ดึงสิทธิ์กลุ่มเงินกู้ |
| wcf.NLoan.of_checkcollmancount | n_cst_lncoopsrv_allrequest.sru | ตรวจสอบจำนวนผู้ค้ำ |

### ตารางที่เกี่ยวข้อง

- **mbmembmaster** — ข้อมูลสมาชิก (ตรวจสอบอายุสมาชิกผู้ค้ำ ≥ 72 เดือน)
- **lnreqloancoll** — บันทึกผู้ค้ำในคำขอกู้
- **lnloantype** — config ประเภทเงินกู้ (วงเงินสูงสุด, เงื่อนไขผู้ค้ำ)
- **lncfgrplnperm** — config สิทธิ์กลุ่มเงินกู้

### จุดที่ต้องยืนยัน (Business User)

- **Hardcode อายุสมาชิกผู้ค้ำ:** line 470 ws_lon_reqloan.aspx.cs — ข้อความ "ผู้ค้ำ...อายุสมาชิกของผู้ค้ำต้องมีอายุไม่น้อยกว่า 72เดือน (6ปี)" — hardcode หรือดึงจาก config?
- **Hardcode เงื่อนไขเงินกู้สามัญอาวุโส:** line 217 — "ยอดขอกู้ต้องหลักพันหรือ 500" — มาจาก lnloantype config หรือ hardcode?

---

## 2. การบริหารหลักทรัพย์จริง (กลุ่ม E)

### ประเภทหลักทรัพย์

| ประเภท | หน้าจอ | ตาราง |
|---|---|---|
| หลักทรัพย์ทั่วไป (ที่ดิน/อาคาร) | ws_lon_collateral_master | lncollmaster, lncolldetail, lncollmastprop, lncollmastbuilding |
| หลักทรัพย์บุคคล (ค้ำบุคคล) | ws_lon_collateral_master | lncollmastmemco |
| หลักทรัพย์รถยนต์ | ws_lon_collateral_car | lncollcarmaster |

### ตารางค้ำประกันในสัญญา

- **lncontcoll** — เชื่อมหลักทรัพย์กับสัญญา (สร้างใน ws_lon_promise, โอนใน ws_lon_trnsloancoll)
- **lncollmaster** — master record ของหลักทรัพย์ (เลขที่อ้างอิงจาก wcf.NCommon.of_getnewdocno)
- **lnreqcollmastredeem** — บันทึกคำขอไถ่ถอน (สร้างใน ws_lon_collredeem)

### Config ตาราง (lnucf*)

| ตาราง | ใช้ใน |
|---|---|
| lnucfcollmasttypegrp | ws_lon_collateral_master — grouping ประเภทหลักทรัพย์ |
| lnucflandtype | ws_lon_collateral_master — ประเภทที่ดิน |
| lnucfbuildingtype | ws_lon_collateral_master — ประเภทอาคาร |
| lnucfbuildingarea | ws_lon_collateral_master — พื้นที่อาคาร |
| lnucfcollredeemcause | ws_lon_collredeem — เหตุผลการไถ่ถอน |

---

## 3. กระบวนการ LifeCycle ของหลักทรัพย์

```
[ยื่นขอกู้] ws_lon_reqloan
  → wcf.NLoan.of_isvalidcoll (ตรวจสอบ)
  → บันทึก lnreqloancoll (ผู้ค้ำในคำขอ)
        ↓
[ทำสัญญา] ws_lon_promise
  → DELETE lncontcoll (เดิม) + INSERT ใหม่
  ⚠️ ไม่มี transaction ครอบ — risk data integrity
        ↓
[บันทึกหลักทรัพย์] ws_lon_collateral_master / _car
  → wcf.NCommon.of_getnewdocno → เลขที่หลักทรัพย์
  → INSERT lncollmaster + lncolldetail
        ↓
[โอนสัญญา] ws_lon_trnsloancoll (เมื่อต้องการโอน)
  → wcf.NLoan.of_savetrn_lntrnres → อัปเดต lncontcoll
        ↓
[ไถ่ถอน] ws_lon_collredeem
  → SELECT lncontcoll (ตรวจยอดคงค้าง)
  → INSERT lnreqcollmastredeem (บันทึกคำขอ)
  ⚠️ ไม่ชัดว่าต้องผ่านขั้นตอนอนุมัติหรือ execute ทันที
```

---

## 4. ความเสี่ยงที่พบ

| ประเด็น | หน้าจอ | ระดับความเสี่ยง |
|---|---|---|
| DELETE lncontcoll + INSERT ใหม่ ไม่มี explicit transaction | ws_lon_promise | สูง |
| of_savetrn_lntrnres ไม่พบใน core lncoopsrv.pbl | ws_lon_trnsloancoll | Medium (อาจอยู่ใน extension) |
| Hardcode อายุสมาชิกผู้ค้ำ 72 เดือน | ws_lon_reqloan | ต้องยืนยัน |
| ไถ่ถอนไม่มีขั้นตอนอนุมัติชัดเจน | ws_lon_collredeem | ต้องยืนยัน |

---

## Related
- [[gcoop-loan-system]] — ระบบเงินกู้ภาพรวม
- [[loan-analysis-group1-request]] — รายละเอียดกลุ่มคำร้องขอกู้
- [[loan-analysis-group5-collateral]] — รายละเอียดกลุ่มค้ำประกัน
