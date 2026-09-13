---
title: วิเคราะห์หน้าจอ ws_lon_apvloan (อนุมัติสินเชื่อ)
created: 2026-09-14
updated: 2026-09-14
type: raw
tags: [raw-source, mhd-loan, screen-analysis]
sources: [source-code]
---

# วิเคราะห์หน้าจอ ws_lon_apvloan (อนุมัติสินเชื่อ)

## ⚙️ ตั้งค่าก่อนใช้งาน

```
ชื่อระบบ                                = ระบบสินเชื่อ
ชื่อหน้าจอ                               = อนุมัติสินเชื่อ
Path ของหน้าจอนี้                        = \mhd\GCOOP\Saving\Applications\loan\ws_lon_apvloan_ctrl\
Path PB Process (Extend)              = ไม่มี
Path PB Process (Core)                 = \CORE\GCOOP\PBProcess\
Path PB Service                        = \CORE\GCOOP\PBService125\
ไฟล์ตัวอย่าง (calibration example)        = ไม่มี
โฟลเดอร์เก็บผลลัพธ์                       = \CORE\.output\
```

## 1. ภาพรวมหน้าจอ

### 1.1 ชื่อหน้าจอและวัตถุประสงค์
**หน้าจอ:** ws_lon_apvloan (อนุมัติสินเชื่อ)
**วัตถุประสงค์:** หน้าจอสำหรับการอนุมัติคำขอสินเชื่อของสมาชิก รองรับการตรวจสอบข้อมูล การอนุมัติ/ปฏิเสธ การสร้างเลขที่สัญญา และการจัดทำเอกสารประกอบ

### 1.2 ไฟล์หลักและขนาด
- **ws_lon_apvloan.aspx** (11,532 bytes) - หน้า UI หลัก
- **ws_lon_apvloan.aspx.cs** (29,597 bytes) - Business Logic
- **DataSet1.Designer.cs** (177,636 bytes) - Dataset definitions
- **3 User Controls** - DsMain, DsList, DsLastDoc

### 1.3 User Controls และหน้าจอย่อย
1. **DsMain.ascx** - ข้อมูลหลักการอนุมัติ
2. **DsList.ascx** - รายการคำขอที่รออนุมัติ
3. **DsLastDoc.ascx** - เอกสารล่าสุด

**หน้าจอย่อย:** ไม่มี subdirectories (ไม่มี dialogs แยก)

## 2. โครงสร้างข้อมูล (Data Structure)

### 2.1 DataSource หลัก
จากการวิเคราะห์โค้ด พบ DataSourceTool สำหรับ:
- **DsMain** - ข้อมูลการอนุมัติหลัก (สถานะ, วันที่, ผู้อนุมัติ)
- **DsList** - รายการคำขอสินเชื่อที่รออนุมัติ
- **DsLastDoc** - เอกสารและรายการล่าสุด

### 2.2 ฟิลด์สำคัญ
จากโค้ดพบคำสำคัญ:
- **appv_status** - สถานะการอนุมัติ
- **approve** - การอนุมัติ
- สร้างเลขที่สัญญาผ่าน GenContNo()
- การคำนวณดอกเบี้ยใหม่ผ่าน of_computeinterest()

## 3. กระบวนการทำงาน (Business Flow)

### 3.1 ขั้นตอนหลัก
1. **โหลดรายการ** - WebSheetLoadBegin() โหลดคำขอที่รออนุมัติ
2. **ตรวจสอบ** - แสดงข้อมูลคำขอผ่าน DsMain และ DsList
3. **การอนุมัติ** - SaveWebSheet() บันทึกผลการอนุมัติ
4. **สร้างสัญญา** - GenContNo() สร้างเลขที่สัญญา
5. **จัดทำเอกสาร** - PostSaveDocument() สำหรับเอกสารประกอบ

### 3.2 การตรวจสอบ (Validation)
- Jschklastdocnonew() - ตรวจสอบเลขที่เอกสารใหม่
- of_getrowdoccode() - ตรวจสอบรหัสเอกสาร
- retime() - จัดการเวลาในการอนุมัติ

### 3.3 การสร้างเอกสาร
- PostGenReqInsDOH() - สร้างเอกสารประกันภัย DOH
- PostupClrintreq() - อัพเดทข้อมูลการหักเคลียร์

## 4. การเชื่อมต่อระบบภายนอก (External Integration)

### 4.1 WCF Service Calls
พบการเรียก WCF Services ทั้งหมด 4 services:

**NCommon Service:**
- **of_gennewdocnocustom()** - สร้างเลขที่เอกสารแบบกำหนดเอง

**NLoan Service:**
- **of_computeinterest()** - คำนวณดอกเบี้ยใหม่สำหรับการอนุมัติ
- **of_gennewcontractno()** - สร้างเลขที่สัญญาใหม่
- **of_saveapv_lnreq()** - บันทึกข้อมูลการอนุมัติคำขอสินเชื่อ

### 4.2 PowerBuilder Process Calls
**ไม่พบ** การเรียก runProcessing หรือ runProcessingExtend

### 4.3 Dialog Integration
**ไม่มี** dialog windows แยก - หน้าจอทำงานแบบ single page

## 5. การจัดการข้อผิดพลาด (Error Handling)

### 5.1 Server-side Validation
จากโค้ด SaveWebSheet() และ PostSaveDocument():
- ตรวจสอบการสร้างเลขที่สัญญา
- ตรวจสอบการบันทึกข้อมูลอนุมัติ
- การจัดการ error ผ่าน framework มาตรฐาน

### 5.2 Document Validation
- การตรวจสอบเอกสารประกอบ
- การตรวจสอบความถูกต้องของเลขที่อ้างอิง

## 6. ความปลอดภัย (Security)

### 6.1 Authentication & Authorization
- การอนุมัติต้องผ่านระบบ authentication
- การบันทึก approval จะมีการ audit trail

### 6.2 Data Integrity
- การสร้างเลขที่สัญญาผ่าน WCF service เพื่อป้องกันการซ้ำ
- การบันทึกสถานะการอนุมัติแบบ transactional

## 7. ประสิทธิภาพ (Performance)

### 7.1 การโหลดข้อมูล
- WebSheetLoadBegin() และ WebSheetLoadEnd() สำหรับ lifecycle management
- การโหลดเฉพาะรายการที่รออนุมัติ

### 7.2 การประมวลผล
- การเรียก WCF services แบบ synchronous สำหรับ critical operations
- การคำนวณดอกเบี้ยทันทีเมื่ออนุมัติ

## 8. การบำรุงรักษา (Maintainability)

### 8.1 Code Structure
- แยก UI และ Business Logic ชัดเจน
- User Controls สำหรับแต่ละ section
- WCF Services สำหรับ business operations

### 8.2 Configuration Management
- ใช้ DataSourceTool framework
- การจัดการ document types และ status codes

## 9. การทดสอบ (Testing Considerations)

### 9.1 Unit Testing Areas
- การสร้างเลขที่สัญญา (GenContNo)
- การคำนวณดอกเบี้ยใหม่
- การบันทึกสถานะอนุมัติ
- การสร้างเอกสารประกอบ

### 9.2 Integration Testing
- WCF service integration (4 services)
- Database transaction handling
- Document generation workflow

### 9.3 User Acceptance Testing
- Approval workflow completeness
- Document accuracy
- Contract number uniqueness
- Error handling scenarios

## 10. ข้อเสนอแนะการพัฒนา (Development Recommendations)

### 10.1 Code Quality
- เพิ่ม error logging สำหรับ WCF service failures
- ปรับปรุง exception handling
- เพิ่ม validation สำหรับ business rules

### 10.2 Performance Optimization
- พิจารณา async WCF calls สำหรับ non-critical operations
- เพิ่ม caching สำหรับ lookup data
- Optimize large DataSet1.Designer.cs (177KB)

### 10.3 Security Enhancement
- เพิ่ม audit logging สำหรับการอนุมัติ
- Digital signature สำหรับ approval process
- Role-based authorization

### 10.4 User Experience
- Progress indicators สำหรับ document generation
- Batch approval functionality
- Enhanced search and filter capabilities
- Approval history and tracking

### 10.5 Business Logic Enhancement
- Workflow engine สำหรับ complex approval rules
- Automated notification system
- Integration with external credit scoring
- Mobile approval capability

---
**หมายเหตุ:** การวิเคราะห์นี้อ้างอิงจากโค้ดจริงในระบบ ณ วันที่ 2026-09-14 โดยตรวจสอบไฟล์ทั้งหมด 10 ไฟล์ในโฟลเดอร์ ws_lon_apvloan_ctrl