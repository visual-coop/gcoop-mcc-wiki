---
title: ระบบสินเชื่อ — วิเคราะห์ End-to-End (กลุ่ม 5: ค้ำประกัน/หลักทรัพย์)
created: 2026-09-07
updated: 2026-09-07
type: query
tags: [loan, aspnet, oracle, wcf]
sources: []
confidence: high
---

# วิเคราะห์ระบบสินเชื่อ — กลุ่ม 5: ค้ำประกัน/หลักทรัพย์

**วันที่วิเคราะห์:** 2026-09-07

---

## 2. ดัชนีหน้าจอ — กลุ่มค้ำประกัน/หลักทรัพย์

| ลำดับ | ชื่อหน้าจอ | วัตถุประสงค์ | child_dialog | PB Service | ตารางหลัก | Confidence | Needs Review |
|---|---|---|---|---|---|---|---|
| 18 | ws_lon_collateral_master | บันทึก/แก้ไขหลักทรัพย์ค้ำประกัน | - | wcf.NCommon.of_getnewdocno | lncollmaster, lncolldetail, lncollmastprop, lncollmastbuilding, lncollmastmemco, lnucfcollmasttypegrp, lnucflandtype, lnucfbuildingtype, lnucfbuildingarea | High | false |
| 19 | ws_lon_collateral_mastdet | ดูรายละเอียดหลักทรัพย์ (read-heavy) | - | wcf.NCommon.of_getnewdocno | lncollmaster, lncollmastprop, lncollmastbuilding, lncollmastmemco | High | false |
| 20 | ws_lon_collateral_car | บันทึกหลักทรัพย์ประเภทรถยนต์ | - | wcf.NCommon.of_getnewdocno | lncollcarmaster | High | false |
| 21 | ws_lon_collredeem | ขอไถ่ถอนหลักทรัพย์ | - | - (Direct SQL) | lncontcoll, lncollmaster, lncolldetail, lncollmastbuilding, lnucfcollredeemcause, lnreqcollmastredeem | High | true |
| 22 | ws_lon_trnsloancoll | โอนสัญญาค้ำประกัน | - | wcf.NLoan.of_savetrn_lntrnres (comment: //pb125) | lncontmaster, lncontcoll, lnloantype | Medium | true |

---

## 3. รายละเอียดสำคัญ

### ws_lon_collateral_master (Direct SQL — High Confidence)
- `exe.AddFormView(dsMain, ExecuteType.Insert)` → INSERT `lncollmaster`
- ตามด้วย INSERT `lncolldetail` (SQL เพิ่มเติม)
- exe.Execute() ครอบการ save ทั้งหมด

### ws_lon_collateral_car (Direct SQL — High Confidence)
- INSERT INTO `lncollcarmaster` ทุก field (ยืนยันจาก DataSet Designer)
- Fields: COLLMAST_NO, CAR_CODE, VERSION, SERIAL_NUMBER, CHASSIS_NUMBER, REGIST_NUMBER, CARPRICE_AMT, INSURANCE_AMT ฯลฯ

### ws_lon_collredeem (Direct SQL — High Confidence)
- SELECT `lncontcoll` เพื่อตรวจยอด `principal_balance`
- INSERT INTO `lnreqcollmastredeem` (บันทึกคำขอไถ่ถอน)
- exed.Execute() — line 189

### ws_lon_trnsloancoll (WCF — Medium Confidence)
- `wcf.NLoan.of_savetrn_lntrnres(wspass, ref str)` — comment ในโค้ดระบุ `//pb125`
- str มี: xml_trnmast (dsMain), xml_trndetail (dsList)
- ไม่พบ implementation ใน core lncoopsrv.pbl — น่าจะอยู่ใน SWU extension

---

## 4. กระบวนการทำงาน

```
ws_lon_collateral_master/mastdet/car
  → wcf.NCommon.of_getnewdocno → เลขที่หลักทรัพย์
  → exe.Execute() → INSERT/UPDATE lncollmaster + detail tables

ws_lon_collredeem
  → SELECT lncontcoll (ยอดคงค้าง)
  → INSERT INTO lnreqcollmastredeem (คำขอไถ่ถอน)

ws_lon_trnsloancoll
  → อ่าน lncontmaster + lncontcoll
  → wcf.NLoan.of_savetrn_lntrnres → โอนสัญญา
```

---

## 5. จุดที่ต้องให้ Business User ยืนยัน

| หน้าจอ | ประเด็น |
|---|---|
| ws_lon_collredeem | INSERT lnreqcollmastredeem หลัง exe.Execute() — การไถ่ถอนต้องผ่านขั้นตอนอนุมัติหรือ execute ทันที? |
| ws_lon_trnsloancoll | `wcf.NLoan.of_savetrn_lntrnres` — comment "pb125" หมายความว่าใช้ PB 12.5 เท่านั้น — version compatibility? |

---

## Related
- [[loan-analysis-group4-payment]] — กลุ่ม 4 ชำระคืน
- [[gcoop-loan-collateral]] — ระบบค้ำประกันโดยละเอียด
- [[gcoop-loan-system]] — ภาพรวมระบบเงินกู้
