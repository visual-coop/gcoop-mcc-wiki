---
title: วิเคราะห์หน้าจอ ws_lon_payment (ชำระหนี้)
created: 2026-09-14
updated: 2026-09-14
type: raw
tags: [raw-source, mhd-loan, screen-analysis]
sources: [source-code]
---

# วิเคราะห์หน้าจอ ws_lon_payment (ชำระหนี้)

## ⚙️ ตั้งค่าก่อนใช้งาน

```
ชื่อระบบ                                = ระบบสินเชื่อ
ชื่อหน้าจอ                               = ชำระหนี้
Path ของหน้าจอนี้                        = \mhd\GCOOP\Saving\Applications\loan\ws_lon_payment_ctrl\
Path PB Process (Extend)              = ไม่มี
Path PB Process (Core)                 = \CORE\GCOOP\PBProcess\
Path PB Service                        = \CORE\GCOOP\PBService125\
ไฟล์ตัวอย่าง (calibration example)        = ไม่มี
โฟลเดอร์เก็บผลลัพธ์                       = \CORE\.output\
```

## 1. ภาพรวมหน้าจอ

### 1.1 ชื่อหน้าจอและวัตถุประสงค์
**หน้าจอ:** ws_lon_payment (ชำระหนี้)
**วัตถุประสงค์:** หน้าจอสำหรับการรับชำระหนี้สินเชื่อของสมาชิก รองรับการคำนวณดอกเบี้ย การชำระแบบต่างๆ การหักเงินฝาก และการออกใบเสร็จ

### 1.2 ไฟล์หลักและขนาด
- **ws_lon_payment.aspx** (33,102 bytes) - หน้า UI หลัก
- **ws_lon_payment.aspx.cs** (103,626 bytes) - Business Logic (ไฟล์ใหญ่ที่สุด)
- **DataSet1.Designer.cs** (597,393 bytes) - Dataset definitions (ขนาดใหญ่มาก)
- **5 User Controls** - DsMain, DsDetailLoan, DsDetailShare, DsDetailMoneytype, DsDetailEtc

### 1.3 User Controls และหน้าจอย่อย
1. **DsMain.ascx** - ข้อมูลหลักการชำระ
2. **DsDetailLoan.ascx** - รายละเอียดสินเชื่อที่ชำระ
3. **DsDetailShare.ascx** - รายละเอียดการชำระหุ้น
4. **DsDetailMoneytype.ascx** - ประเภทเงินที่รับชำระ
5. **DsDetailEtc.ascx** - รายการอื่นๆ

**หน้าจอย่อย:** ไม่มี subdirectories - เป็น single complex page

## 2. โครงสร้างข้อมูล (Data Structure)

### 2.1 DataSource หลัก
จากการวิเคราะห์ 5 User Controls:
- **DsMain** - ข้อมูลหลักการชำระ (สมาชิก, วันที่, ยอดรวม)
- **DsDetailLoan** - รายละเอียดสินเชื่อแต่ละสัญญา
- **DsDetailShare** - การชำระเงินค่าหุ้น
- **DsDetailMoneytype** - ประเภทการรับเงิน (เงินสด, โอน, หักเงินฝาก)
- **DsDetailEtc** - ค่าธรรมเนียมและรายการอื่นๆ

### 2.2 ฟิลด์สำคัญ
จากโค้ด Business Logic (24 methods):
- **Payment/PAY/pay** - การชำระหนี้
- การคำนวณดอกเบี้ย: ReCalint(), of_calfeeloanpayment()
- การจัดการเงินสด: of_Cashdetail()
- การหักเงินฝาก: withdraw_deposit_trans, withdraw_loan

## 3. กระบวนการทำงาน (Business Flow)

### 3.1 ขั้นตอนหลัก (24 Methods)
1. **เริ่มต้น** - WebSheetLoadBegin(), InitJsPostBack()
2. **ตั้งค่าเริ่มต้น** - of_setloanfilepayment(), SetDefaultTofromaccid()
3. **คำนวณ** - ReCalint(), calItemPay(), of_calfeeloanpayment()
4. **จัดการเงินสด** - of_Cashdetail(), UpMoneyreturn()
5. **บันทึก** - SaveWebSheet()
6. **รีเฟรช** - refresh(), of_activeworkdate()

### 3.2 การคำนวณดอกเบี้ยและค่าธรรมเนียม
- **ReCalint()** - คำนวณดอกเบี้ยใหม่
- **of_calfeeloanpayment()** - คำนวณค่าธรรมเนียมการชำระ
- **calItemPay()** - คำนวณรายการที่ชำระ
- การเรียก WCF: wcf.NLoan.of_calfeeloanpayment()

### 3.3 การจัดการประเภทการรับเงิน
- **OfSetDefaultTofromaccidTRN()** - ตั้งค่าการโอนเงิน
- **of_Cashdetail()** - จัดการเงินสดรายละเอียด
- **UpMoneyreturn()** - จัดการเงินทอน
- **of_is_teller()** - ตรวจสอบ teller

### 3.4 การจัดการเงินฝากและสินเชื่อ
- **InitLnRcv()** - เริ่มต้นการรับชำระสินเชื่อ
- **InitLnRcvKeepArrear()** - จัดการค้างชำระ
- **PostUpdateKparr()** - อัพเดทค้างชำระ

## 4. การเชื่อมต่อระบบภายนอก (External Integration)

### 4.1 WCF Service Calls (8 Services)
**NCommon Service:**
- **of_getnewdocno()** - สร้างเลขที่เอกสารใหม่
- **of_getnextworkday()** - หาวันทำการถัดไป

**NDeposit Service:**
- **of_withdraw_deposit_trans()** - หักเงินฝากเพื่อชำระ
- **of_withdraw_loan()** - หักเงินกู้

**NLoan Service:**
- **of_calfeeloanpayment()** - คำนวณค่าธรรมเนียมการชำระ
- **of_initslippayin()** - เริ่มต้นใบเสร็จรับเงิน
- **of_initslippayin_recalint()** - เริ่มต้นใบเสร็จพร้อมคำนวณดอกเบี้ย
- **of_saveslip_payin()** - บันทึกใบเสร็จรับเงิน

### 4.2 PowerBuilder Process Calls
**ไม่พบ** การเรียก runProcessing หรือ runProcessingExtend

### 4.3 Internal Integration
- การเชื่อมต่อกับระบบเงินฝาก (NDeposit)
- การเชื่อมต่อกับระบบสินเชื่อ (NLoan)
- การจัดการเอกสารและใบเสร็จ

## 5. การจัดการข้อผิดพลาด (Error Handling)

### 5.1 Business Validation
- **GetCheckKptem()** - ตรวจสอบเงื่อนไข
- การตรวจสอบยอดเงินและดอกเบี้ย
- การตรวจสอบสิทธิ์การหักเงินฝาก

### 5.2 Transaction Validation
- การตรวจสอบความเพียงพอของเงินฝาก
- การตรวจสอบการคำนวณดอกเบี้ย
- การตรวจสอบการออกใบเสร็จ

### 5.3 Data Consistency
- การตรวจสอบข้อมูลก่อนบันทึก
- การจัดการ transaction rollback
- การตรวจสอบยอดคงเหลือ

## 6. ความปลอดภัย (Security)

### 6.1 Payment Security
- การตรวจสอบสิทธิ์การรับชำระ
- การ audit trail สำหรับการรับเงิน
- การป้องกันการรับชำระซ้ำ

### 6.2 Financial Security
- การตรวจสอบยอดเงินทุกขั้นตอน
- การจัดการเงินสดอย่างปลอดภัย
- การตรวจสอบการหักเงินฝาก

### 6.3 Document Security
- การสร้างใบเสร็จที่ไม่ซ้ำ
- การป้องกันการแก้ไขใบเสร็จ
- การเก็บ audit log

## 7. ประสิทธิภาพ (Performance)

### 7.1 การโหลดข้อมูล
- DataSet ขนาดใหญ่ (597KB) สำหรับข้อมูลครอบคลุม
- การแยก User Controls ตาม functionality
- การ lazy loading สำหรับข้อมูลใหญ่

### 7.2 การคำนวณ
- WCF services สำหรับ complex calculations
- Real-time interest calculation
- Efficient money type handling

### 7.3 Memory Management
- การจัดการ DataSet ขนาดใหญ่
- การ refresh data เมื่อจำเป็น
- การจัดการ state ของ 5 User Controls

## 8. การบำรุงรักษา (Maintainability)

### 8.1 Code Structure
- แยก User Controls ตาม business domain
- Business Logic 103KB แยกจาก UI ชัดเจน
- WCF Services สำหรับ reusable operations

### 8.2 Configuration Management
- การจัดการประเภทเงินและค่าธรรมเนียม
- การตั้งค่าการคำนวณดอกเบี้ย
- การจัดการเอกสารและรายงาน

## 9. การทดสอบ (Testing Considerations)

### 9.1 Unit Testing Areas
- การคำนวณดอกเบี้ย (ReCalint, calItemPay)
- การคำนวณค่าธรรมเนียม (of_calfeeloanpayment)
- การจัดการเงินสด (of_Cashdetail, UpMoneyreturn)
- การหักเงินฝาก (withdraw functions)

### 9.2 Integration Testing
- WCF service integration (8 services)
- Cross-control data synchronization (5 controls)
- Payment workflow completeness
- Receipt generation accuracy

### 9.3 User Acceptance Testing
- Complete payment scenarios
- Interest calculation accuracy
- Multi-payment type handling
- Receipt printing and validation

## 10. ข้อเสนอแนะการพัฒนา (Development Recommendations)

### 10.1 Code Quality
- Optimize DataSet1.Designer.cs ขนาด 597KB
- Refactor ws_lon_payment.aspx.cs ขนาด 103KB
- เพิ่ม unit tests สำหรับ critical calculations
- ปรับปรุง error handling และ logging

### 10.2 Performance Optimization
- Implement async WCF calls สำหรับ non-blocking operations
- Add caching สำหรับ interest rate calculations
- Optimize large dataset loading
- Implement progressive loading สำหรับ large payment histories

### 10.3 Security Enhancement
- Enhanced payment authentication
- Digital receipt signatures
- Real-time fraud detection
- Comprehensive audit trail with tamper protection

### 10.4 User Experience
- Single page application design
- Real-time balance updates
- Auto-calculation สำหรับ payment amounts
- Mobile-friendly payment interface
- Progress indicators สำหรับ complex calculations

### 10.5 Business Logic Enhancement
- Payment scheduling and automation
- Integration with external banking systems
- Advanced interest calculation models
- Comprehensive payment reporting
- Multi-currency support

### 10.6 Technical Architecture
- Microservices architecture สำหรับ payment processing
- Event-driven architecture สำหรับ real-time updates
- Implement proper transaction management
- Add comprehensive monitoring and alerting

### 10.7 Financial Features
- Payment plan optimization
- Early payment discount calculations
- Late payment penalty automation
- Payment history analytics
- Predictive payment modeling

---
**หมายเหตุ:** การวิเคราะห์นี้อ้างอิงจากโค้ดจริงในระบบ ณ วันที่ 2026-09-14 โดยตรวจสอบไฟล์ทั้งหมด 14 ไฟล์ในโฟลเดอร์ ws_lon_payment_ctrl รวมขนาดประมาณ 780KB ซึ่งเป็นหน้าจอที่ซับซ้อนและมีขนาดใหญ่ที่สุดในระบบสินเชื่อ