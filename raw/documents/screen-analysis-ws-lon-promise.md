---
title: วิเคราะห์หน้าจอ ws_lon_promise (ทำสัญญา)
created: 2026-09-14
updated: 2026-09-14
type: raw
tags: [raw-source, mhd-loan, screen-analysis]
sources: [source-code]
---

# วิเคราะห์หน้าจอ ws_lon_promise (ทำสัญญา)

## ⚙️ ตั้งค่าก่อนใช้งาน

```
ชื่อระบบ                                = ระบบสินเชื่อ
ชื่อหน้าจอ                               = ทำสัญญา
Path ของหน้าจอนี้                        = \mhd\GCOOP\Saving\Applications\loan\ws_lon_promise_ctrl\
Path PB Process (Extend)              = ไม่มี
Path PB Process (Core)                 = \CORE\GCOOP\PBProcess\
Path PB Service                        = \CORE\GCOOP\PBService125\
ไฟล์ตัวอย่าง (calibration example)        = ไม่มี
โฟลเดอร์เก็บผลลัพธ์                       = \CORE\.output\
```

## 1. ภาพรวมหน้าจอ

### 1.1 ชื่อหน้าจอและวัตถุประสงค์
**หน้าจอ:** ws_lon_promise (ทำสัญญา)
**วัตถุประสงค์:** หน้าจอสำหรับการจัดทำสัญญาสินเชื่อหลังจากได้รับการอนุมัติ รองรับการตรวจสอบข้อมูลสมาชิก การจัดการค้ำประกัน การคำนวณดอกเบี้ย และการเซ็นสัญญา

### 1.2 ไฟล์หลักและขนาด
- **ws_lon_promise.aspx** (42,904 bytes) - หน้า UI หลัก
- **ws_lon_promise.aspx.cs** (49,518 bytes) - Business Logic
- **DataSet1.Designer.cs** (372,450 bytes) - Dataset definitions (ขนาดใหญ่)
- **DataSet1.cs** (366 bytes) - Dataset extension
- **3 User Controls** - DsMain, DsColl, DsCollOld

### 1.3 User Controls และหน้าจอย่อย
1. **DsMain.ascx** - ข้อมูลหลักสัญญา
2. **DsColl.ascx** - ข้อมูลค้ำประกันใหม่
3. **DsCollOld.ascx** - ข้อมูลค้ำประกันเดิม

**หน้าจอย่อย:** ไม่มี subdirectories (ไม่มี dialogs แยก)

## 2. โครงสร้างข้อมูล (Data Structure)

### 2.1 DataSource หลัก
จากการวิเคราะห์โค้ด พบ DataSourceTool สำหรับ:
- **DsMain** - ข้อมูลหลักสัญญา (เลขที่สัญญา, อัตราดอกเบี้ย, เงื่อนไข)
- **DsColl** - รายการค้ำประกันใหม่
- **DsCollOld** - รายการค้ำประกันเดิมที่โอนมา

### 2.2 ฟิลด์สำคัญ
จากโค้ดพบคำสำคัญ:
- **CONTRACT/contract** - ข้อมูลสัญญา
- **CONT_NO** - เลขที่สัญญา
- **promise** - การทำสัญญา
- **sign** - การเซ็นสัญญา
- การจัดการค้ำประกัน (collateral) หลายประเภท

## 3. กระบวนการทำงาน (Business Flow)

### 3.1 ขั้นตอนหลัก
1. **โหลดข้อมูล** - WebSheetLoadBegin() โหลดข้อมูลสัญญาที่อนุมัติแล้ว
2. **ตรวจสอบสมาชิก** - IsValidMember() ตรวจสอบสิทธิ์สมาชิก
3. **จัดการค้ำประกัน** - GetCollPermiss(), GetCollPermissSec(), GetCollPermissDep()
4. **คำนวณค้ำประกัน** - of_settcollbalance(), setinitCollPermiss()
5. **ทำสัญญา** - SaveWebSheet() บันทึกข้อมูลสัญญา
6. **เสร็จสิ้น** - WebSheetLoadEnd()

### 3.2 การจัดการค้ำประกัน (Collateral Management)
**ประเภทค้ำประกันที่รองรับ:**
- **GetCollPermiss()** - ค้ำประกันทั่วไป
- **GetCollPermissSec()** - ค้ำประกันหลักทรัพย์
- **GetCollPermissDep()** - ค้ำประกันเงินฝาก

**การตรวจสอบ:**
- of_settcollbalance() - ตั้งค่ายอดค้ำประกันคงเหลือ
- setinitCollPermiss() - เริ่มต้นสิทธิ์ค้ำประกัน

### 3.3 การคำนวณและตรวจสอบ
- **GetIntrate()** - คำนวณอัตราดอกเบี้ย
- **checkadjust()** - ตรวจสอบการปรับปรุง
- **JsPostLoanContractNo()** - จัดการเลขที่สัญญา
- **PostOpenAdjustdocno()** - เปิดเอกสารปรับปรุง

## 4. การเชื่อมต่อระบบภายนอก (External Integration)

### 4.1 WCF Service Calls
พบการเรียก WCF Services ทั้งหมด 4 services (เฉพาะ NLoan):

**NLoan Service:**
- **of_checkcollmancount()** - ตรวจสอบจำนวนผู้ค้ำ
- **of_getcollpermiss()** - ดึงข้อมูลสิทธิ์ค้ำประกัน
- **of_getcollusecontamt()** - ยอดค้ำประกันที่ใช้ในสัญญา
- **of_getcollusereqamt()** - ยอดค้ำประกันที่ขอใช้

### 4.2 PowerBuilder Process Calls
**ไม่พบ** การเรียก runProcessing หรือ runProcessingExtend

### 4.3 JavaScript Integration
- การจัดการเลขที่สัญญาผ่าน JavaScript
- การตรวจสอบข้อมูลด้านหน้า (client-side validation)

## 5. การจัดการข้อผิดพลาด (Error Handling)

### 5.1 Business Validation
- **IsValidMember()** - ตรวจสอบสิทธิ์สมาชิก
- **checkadjust()** - ตรวจสอบความจำเป็นในการปรับปรุง
- การตรวจสอบค้ำประกันเพียงพอ

### 5.2 Collateral Validation
- การตรวจสอบจำนวนผู้ค้ำผ่าน WCF
- การตรวจสอบยอดค้ำประกันคงเหลือ
- การตรวจสอบสิทธิ์ค้ำประกันแต่ละประเภท

## 6. ความปลอดภัย (Security)

### 6.1 Contract Integrity
- การตรวจสอบเลขที่สัญญาไม่ซ้ำ
- การบันทึกข้อมูลสัญญาแบบ transactional
- การ audit trail สำหรับการเซ็นสัญญา

### 6.2 Collateral Security
- การตรวจสอบสิทธิ์ค้ำประกันแต่ละประเภท
- การป้องกันการใช้ค้ำประกันเกินสิทธิ์
- การจัดการค้ำประกันเดิมและใหม่แยกกัน

## 7. ประสิทธิภาพ (Performance)

### 7.1 การโหลดข้อมูล
- WebSheetLoadBegin()/End() lifecycle management
- การโหลดข้อมูลค้ำประกันแยกตามประเภท
- DataSet ขนาดใหญ่ (372KB) สำหรับข้อมูลครอบคลุม

### 7.2 การคำนวณ
- การคำนวณดอกเบี้ยแบบ real-time
- การตรวจสอบค้ำประกันแบบ batch processing
- WCF calls สำหรับ complex business logic

## 8. การบำรุงรักษา (Maintainability)

### 8.1 Code Structure
- แยก User Controls ตามหน้าที่ (Main, Coll, CollOld)
- Business Logic แยกจาก UI ชัดเจน
- WCF Services สำหรับ collateral business rules

### 8.2 Data Management
- DataSet ขนาดใหญ่บ่งบอกถึงข้อมูลครอบคลุม
- การจัดการค้ำประกันแยกเป็น components
- Configuration management ผ่าน framework

## 9. การทดสอบ (Testing Considerations)

### 9.1 Unit Testing Areas
- การคำนวณดอกเบี้ย (GetIntrate)
- การตรวจสอบสิทธิ์สมาชิก (IsValidMember)
- การจัดการค้ำประกันแต่ละประเภท
- การตรวจสอบการปรับปรุง (checkadjust)

### 9.2 Integration Testing
- WCF service integration (4 collateral services)
- JavaScript และ C# integration
- Database transaction handling
- Cross-control data synchronization

### 9.3 User Acceptance Testing
- Contract creation workflow
- Collateral management accuracy
- Interest calculation correctness
- Document generation and signing

## 10. ข้อเสนอแนะการพัฒนา (Development Recommendations)

### 10.1 Code Quality
- Optimize DataSet1.Designer.cs ขนาด 372KB
- เพิ่ม unit tests สำหรับ collateral logic
- ปรับปรุง error handling และ logging

### 10.2 Performance Optimization
- Lazy loading สำหรับ collateral data
- Async WCF calls สำหรับ non-critical operations
- Caching สำหรับ interest rate lookup
- Pagination สำหรับ large collateral lists

### 10.3 Security Enhancement
- Digital signature integration
- Enhanced audit trail
- Role-based contract approval
- Collateral security validation

### 10.4 User Experience
- Progress indicators สำหรับ contract generation
- Auto-save functionality
- Enhanced collateral search and selection
- Mobile-friendly contract signing
- Real-time collateral availability checking

### 10.5 Business Logic Enhancement
- Workflow engine สำหรับ complex contract rules
- Automated collateral valuation
- Integration with external credit bureaus
- Contract template management system
- Collateral portfolio optimization

---
**หมายเหตุ:** การวิเคราะห์นี้อ้างอิงจากโค้ดจริงในระบบ ณ วันที่ 2026-09-14 โดยตรวจสอบไฟล์ทั้งหมด 11 ไฟล์ในโฟลเดอร์ ws_lon_promise_ctrl รวมขนาดไฟล์ประมาณ 480KB