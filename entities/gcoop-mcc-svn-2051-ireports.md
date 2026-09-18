---
title: MCC iReport ชุดใหม่ SVN 2051
created: 2026-09-18
updated: 2026-09-18
type: entity
tags: [ireport, dividend, hr, keeping, saving]
sources: [raw/documents/mcc-svn-2051-2026-09-18.md]
confidence: high
---

# MCC iReport — ไฟล์ที่มาใน SVN 2035→2051

ตำแหน่งจริง: `/root/gcoop_hermes/mcc/GCOOP/iReport/Reports/`  
นับ `.jrxml` ทั้งโฟลเดอร์ตอน ingest: **688 ไฟล์** (หน้า [[gcoop-mcc-infrastructure]] เดิมระบุ 676)

ข้อมูลด้านล่างมาจากอ่าน `.jrxml` โดยตรง ไม่แต่ง query

## รายงานใหม่ (A)

### `r_div_to_share_Nohave_mcc` / `r_div_to_share_have_mcc`

- report name ในไฟล์: `r_div_methpay_mcc` ทั้งคู่
- params: `as_coopid`, `as_period`, `REPORT_NAME`, `PRINT_USER`
- query อ่าน `yrdivmethpay` รวม `div_amt+avg_amt` ที่ `methpaytype_code IN ('FSC','CSC')` และ `moneytype_code = 'TRN'`
- ต่างกันที่ชื่อไฟล์ Nohave / have (โอนปันผลเข้าหุ้น กรณีไม่มี/มีบัญชี)

### `ir_dp_deptmonth_amt`

- ชื่อบนรายงาน: `รายงานเงินฝากส่งรายเดือน`
- params: `coop_id`
- จาก `dpdeptmaster` join `CMCOOPMASTER`
- เงื่อนไขที่อ่านได้: `depttype_code = '15'`, `deptclose_status = 0`, `deptmonth_status = 1`
- คอลัมน์: เลขสมาชิก, เลขที่บัญชี, ชื่อบัญชี, ยอดฝากรายเดือน

### `r_hr_pay_provf_mcc`

- ชื่อบนรายงาน: `รายงานข้อมูลเงินนำส่งสงเคราะห์ลูกจ้าง`
- params: `coop_id`, `period`
- จาก HR payroll (`hrpayrolldet` item `R01` เป็นค่าจ้าง) + ข้อมูลพนักงาน
- คอลัมน์ตัวอย่าง: คำนำหน้า, สัญชาติ, ชื่อ, สกุล, อายุ, เลขบัตร, ค่าจ้าง, วันที่เริ่มงาน, เงินสมทบ, นายจ้างสมทบ

### `ir_kp_member_deduct_fail_monthly_mcc`

- params: `coop_id`, `period`
- จาก keeping slip (`kp.*`) + `FT_GETMEMNAME` + กลุ่มสมาชิก
- map `KEEPITEMTYPE_CODE`: L01 ฉุกเฉิน, L02 สามัญ, L03 พิเศษ

## รายงานที่ถูกแก้ (U)

### `r_hr_Certificate_mcc`

- report name: `r_hr_Certificate`
- params: `as_sempno`, `as_eempno`, `emp_no`, `apv_type`, `as_coopid`
- หนังสือรับรองภาษาไทย: ตำแหน่ง, เงินตำแหน่ง `R03`, `ft_readtbaht`

### `r_hr_Certificate_eng_mcc`

- report name: `r_hr_Certificate_eng`
- static: `Letter of Certification`
- ที่อยู่ตัวอย่างในไฟล์: 2509 Village No.1, Kromluang Chumporn Camp, Sattahip … `thaimarinecoop@gmail.com`

## ลิงก์

- [[gcoop-mcc-infrastructure]]
- [[gcoop-mcc-ws-lc-memo]]
- [[gcoop-mcc-web-system]]
