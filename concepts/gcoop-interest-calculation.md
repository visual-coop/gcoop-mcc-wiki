---
title: GCOOP Interest Calculation System
created: 2026-09-10
updated: 2026-09-10
type: concept
tags: [loan, powerbuilder, oracle, plsql, algorithm]
sources: [raw/documents/core-pb-interest-service.md, raw/documents/mhd-plsql-n-pk-lnnpl.md]
confidence: high
---

# GCOOP Interest Calculation System

## ภาพรวม
ระบบคำนวณดอกเบี้ยสินเชื่อของ GCOOP ประกอบด้วยส่วนประกอบหลายชั้น ตั้งแต่ PowerBuilder Service จนถึง Oracle PL/SQL Functions

## สถาปัตยกรรมการคำนวณ

### 1. PowerBuilder Service Layer
- **ไฟล์:** `n_cst_lncoopsrv_interest.sru`
- **ภาษา:** PowerBuilder Classic (UTF-16LE)
- **หน้าที่:** Business logic หลักสำหรับการคำนวณดอกเบี้ย

#### ฟังก์ชันหลัก
```powerbuilder
// การเริ่มต้น
of_initservice()
of_loadconfigdefault()

// การคำนวณ
of_calculateinterest()
of_computeinterest()

// การจัดการอัตรา
of_getinteresttable()
of_getloanintrate()
of_getcontractintrate()

// การปัดเศษ
of_roundinterest()
of_roundmoney()
```

### 2. Oracle PL/SQL Layer
- **Package:** `n_pk_lnnpl`
- **Functions:** `ft_getintrate`, `ft_roundmoney`
- **Tables:** `lccfloanconstant`, `cfintrate`

## การทำงานของระบบ

### 1. การโหลด Configuration
```sql
SELECT dayinyear, rdintsatang_type, rdint2digit_type, 
       rdintdec_type, rdintdec_digit
FROM lccfloanconstant
WHERE coop_id = :coop_id
```

#### ตัวแปร Config สำคัญ
- `ii_daysinyear` - จำนวนวันในปี (365/366)
- `ii_rdsatangtype` - รูปแบบการปัดเศษสตางค์ (0=ไม่ปัด)
- `ii_rdint2digittype` - การปัดทศนิยม 2 หลัก (1=ปัด)
- `ii_rdintdectype` - รูปแบบทศนิยม
- `ii_rdintdecdigit` - จำนวนหลักทศนิยม (default=2)

### 2. การคำนวณดอกเบี้ย

#### Formula พื้นฐาน
```
ดอกเบี้ย = (เงินต้น × อัตราดอกเบี้ย × จำนวนวัน) / จำนวนวันในปี
```

#### การปรับอัตราดอกเบี้ย
- รองรับการเพิ่ม/ลดอัตรา (`idc_intincrease`)
- ตรวจสอบอัตราตามวันที่และจำนวนเงิน
- อ้างอิง `cfintrate` table สำหรับอัตราแบบขั้นบันได

### 3. DataObjects ที่ใช้งาน
- `d_lcsrv_info_cfintrate` - ตารางอัตราดอกเบี้ย
- `d_lcsrv_info_contint` - ข้อมูลดอกเบี้ยสัญญา
- `d_lcsrv_info_contintspc` - ดอกเบี้ยพิเศษ/ผิดนัด

## การปัดเศษ (Rounding Logic)

### 1. การปัดเศษสตางค์ (ii_rdsatangtype)
- `0` = ไม่ปัดเศษ
- `>0` = ปัดรูปแบบต่างๆ ตามที่กำหนด

### 2. การปัดเศษทศนิยม
- `ii_rdint2digittype = 1` = บังคับปัดเป็น 2 หลัก
- `ii_rdintdecdigit` = จำนวนหลักทศนิยมที่ต้องการ

### 3. Integration กับ Oracle
เรียกใช้ `ft_roundmoney()` function ใน Oracle สำหรับการปัดเศษที่สอดคล้องกันทั้งระบบ

## Exception Handling
- ใช้ PowerBuilder `Exception` class
- จัดการ error ในการคำนวณ
- Propagate ข้อผิดพลาดขึ้นไปยัง calling layer

## การเชื่อมต่อกับระบบอื่น
- **[[gcoop-loan-system]]** - ระบบสินเชื่อหลัก
- **[[gcoop-mhd]]** - Web application layer
- **Oracle Database** - ฐานข้อมูล config และ master data

## ตัวอย่างการใช้งาน

### 1. การคำนวณดอกเบี้ยรายเดือน
```powerbuilder
// โหลดตารางอัตราดอกเบี้ย
of_getinteresttable(ls_inttabcode, ldt_startdate, ldt_enddate, lds_inttable)

// คำนวณดอกเบี้ย
ldc_interest = of_calculateinterest(lds_inttable, ldt_calintfrom, ldt_calintto, ldc_principal)

// ปัดเศษ
ldc_interest_rounded = of_roundinterest(ldc_interest, ii_rdintdectype, ii_rdintdecdigit)
```

### 2. การคำนวณดอกเบี้ยตามสัญญา
```powerbuilder
ldc_interest = of_computeinterest(ls_coopid, ls_contno, ldt_calintto)
```

## Performance Considerations
- Cache ตารางอัตราดอกเบี้ยใน memory
- Reuse DataStore objects
- Optimize Oracle function calls
- ลด round-trip ระหว่าง PowerBuilder และ Oracle