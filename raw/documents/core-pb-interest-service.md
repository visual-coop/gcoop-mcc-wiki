---
source_url: /root/gcoop_hermes/core/GCOOP/PBService125/lncoopsrv.pbl/n_cst_lncoopsrv_interest.sru
ingested: 2026-09-10
sha256: [computed during write]
---

# PowerBuilder Interest Calculation Service (n_cst_lncoopsrv_interest.sru)

## ภาพรวม
- **ไฟล์:** n_cst_lncoopsrv_interest.sru
- **ภาษา:** PowerBuilder Classic (UTF-16LE encoding)
- **ฟังก์ชัน:** บริการคำนวณดอกเบี้ยสินเชื่อ

## ฟังก์ชันหลัก

### 1. การเริ่มต้นระบบ
- `of_initservice()` - เริ่มต้นบริการ เชื่อมต่อฐานข้อมูล
- `of_loadconfigdefault()` - โหลดค่า config จาก lccfloanconstant table

### 2. การคำนวณดอกเบี้ย
- `of_calculateinterest()` - คำนวณดอกเบี้ยตามตารางอัตรา
- `of_computeinterest()` - คำนวณดอกเบี้ยแบบต่างๆ
- `of_testcalnew()` - ฟังก์ชันทดสอบการคำนวณใหม่

### 3. การจัดการอัตราดอกเบี้ย
- `of_getinteresttable()` - ดึงตารางอัตราดอกเบี้ย
- `of_getloanintrate()` - ดึงอัตราดอกเบี้ยตาม loan type
- `of_getcontractintrate()` - ดึงอัตราดอกเบี้ยตามสัญญา

### 4. การปัดเศษ
- `of_roundinterest()` - ปัดเศษดอกเบี้ย
- `of_roundmoney()` - ปัดเศษเงิน
- `of_daysinyear()` - คำนวณจำนวนวันในปี

## ตัวแปรสำคัญ
- `ii_daysinyear` - จำนวนวันในปี (365/366)
- `ii_rdsatangtype` - รูปแบบการปัดเศษสตางค์
- `ii_rdint2digittype` - รูปแบบการปัดเศษทศนิยม 2 หลัก
- `ii_rdintdectype` - รูปแบบทศนิยม
- `ii_rdintdecdigit` - จำนวนหลักทศนิยม

## DataObjects
- `d_lcsrv_info_cfintrate` - ตารางอัตราดอกเบี้ย
- `d_lcsrv_info_contint` - ข้อมูลดอกเบี้ยสัญญา
- `d_lcsrv_info_contintspc` - ข้อมูลดอกเบี้ยพิเศษ

## Exception Handling
ใช้ `Exception` class สำหรับจัดการ error ในการคำนวณ