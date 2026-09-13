---
title: วิเคราะห์หน้าจอ ws_lon_rcvloan (รับเงินกู้/เบิกจ่าย)
created: 2026-09-14
updated: 2026-09-14
type: raw
tags: [raw-source, mhd-loan, screen-analysis]
sources: [source-code]
---

# วิเคราะห์หน้าจอ ws_lon_rcvloan (รับเงินกู้/เบิกจ่าย)

## ⚙️ ตั้งค่าก่อนใช้งาน

```
ชื่อระบบ                                = ระบบสินเชื่อ
ชื่อหน้าจอ                               = รับเงินกู้/เบิกจ่าย
Path ของหน้าจอนี้                        = \mhd\GCOOP\Saving\Applications\loan\ws_lon_rcvloan_ctrl\
Path PB Process (Extend)              = ไม่มี
Path PB Process (Core)                 = \CORE\GCOOP\PBProcess\
Path PB Service                        = \CORE\GCOOP\PBService125\
ไฟล์ตัวอย่าง (calibration example)        = ไม่มี
โฟลเดอร์เก็บผลลัพธ์                       = \CORE\.output\
```

## 1. ภาพรวมหน้าจอ

### 1.1 ชื่อหน้าจอและวัตถุประสงค์
**หน้าจอ:** ws_lon_rcvloan (รับเงินกู้/เบิกจ่าย)
**วัตถุประสงค์:** หน้าจอสำหรับการเบิกจ่ายเงินกู้ให้แก่สมาชิกที่มีสัญญาแล้ว รองรับการค้นหาสัญญา การตรวจสอบรายละเอียด การเบิกจ่าย และการพิมพ์ใบเสร็จ

### 1.2 ไฟล์หลักและขนาด
- **ws_lon_rcvloan.aspx** (7,208 bytes) - หน้า UI หลัก
- **ws_lon_rcvloan.aspx.cs** (14,525 bytes) - Business Logic
- **DataSet1.Designer.cs** (97,958 bytes) - Dataset definitions
- **2 User Controls** - DsMain, DsList

### 1.3 User Controls และหน้าจอย่อย
**User Controls หลัก:**
1. **DsMain.ascx** - ข้อมูลหลักการเบิกจ่าย
2. **DsList.ascx** - รายการสัญญาที่เบิกได้

**Dialog Subdirectories (3 ตัว):**
1. **w_dlg_loan_search_receive_ctrl** (6 ไฟล์, 120KB) - ค้นหาสัญญาสำหรับเบิกจ่าย
   - Keywords: search, receive, rcv
   - วัตถุประสงค์: ค้นหาและเลือกสัญญาที่พร้อมเบิกจ่าย

2. **wd_lon_rcvdetail_ctrl** (8 ไฟล์, 179KB) - รายละเอียดการเบิกจ่าย  
   - Keywords: detail, rcv
   - วัตถุประสงค์: แสดงรายละเอียดการเบิกจ่ายและค้ำประกัน

3. **wd_lon_rcvlon_ctrl** (14 ไฟล์, 800KB) - การเบิกจ่ายจริง
   - Keywords: rcv
   - วัตถุประสงค์: ดำเนินการเบิกจ่ายเงินกู้ครบวงจร
   - มี 5 User Controls: DsMain, DsDetail, DsAdd, DsPayto, DsPayoutdet

## 2. โครงสร้างข้อมูล (Data Structure)

### 2.1 DataSource หลัก (Main Page)
- **DsMain** - ข้อมูลหลักการเบิกจ่าย
- **DsList** - รายการสัญญาที่สามารถเบิกจ่ายได้

### 2.2 DataSource ใน Dialog wd_lon_rcvlon_ctrl
- **DsMain** - ข้อมูลสัญญาหลัก
- **DsDetail** - รายละเอียดการเบิกจ่าย  
- **DsAdd** - รายการเพิ่มเติม
- **DsPayto** - ข้อมูลผู้รับเงิน
- **DsPayoutdet** - รายละเอียดการจ่ายเงิน
- **DsColl** - ข้อมูลค้ำประกัน (ใน rcvdetail_ctrl)

### 2.3 ฟิลด์สำคัญ
จากโค้ดพบคำสำคัญ:
- **rcv/Rcv** - การรับเงิน/เบิกจ่าย
- **withdraw** - การถอนเงิน
- การจัดการประเภทสินเชื่อผ่าน of_setloantype()

## 3. กระบวนการทำงาน (Business Flow)

### 3.1 ขั้นตอนหลัก (Main Page)
1. **โหลดข้อมูล** - WebSheetLoadBegin() โหลดรายการสัญญา
2. **แสดงข้อมูล** - ShowData() แสดงรายการที่เบิกได้
3. **เลือกสัญญา** - ผ่าน DsList control
4. **เบิกจ่าย** - SaveWebSheet() บันทึกการเบิกจ่าย
5. **พิมพ์ใบเสร็จ** - PrintSlipAll() พิมพ์เอกสาร

### 3.2 JavaScript Functions
1. **Validate()** - ตรวจสอบข้อมูลก่อนเบิกจ่าย
2. **MenubarOpen()** - เปิดเมนูการทำงาน
3. **GetShowData()** - ดึงข้อมูลแสดงผล
4. **GetItemLoan()** - ดึงรายการสินเชื่อ
5. **PrintSlipAll()** - พิมพ์ใบเสร็จทั้งหมด

### 3.3 การจัดการ UI
- **setcolordefault()** - ตั้งค่าสีพื้นฐาน
- **setcolor_row()** - ตั้งค่าสีแถว
- **GetFormPA()** - จัดการ form parameters
- **of_setloantype()** - ตั้งค่าประเภทสินเชื่อ

## 4. การเชื่อมต่อระบบภายนอก (External Integration)

### 4.1 WCF Service Calls
**ไม่พบ** การเรียก WCF Services ในหน้าหลัก - การประมวลผลอยู่ใน dialogs

### 4.2 PowerBuilder Process Calls
**ไม่พบ** การเรียก runProcessing หรือ runProcessingExtend

### 4.3 Dialog Integration
การเปิด dialogs ผ่าน JavaScript:
- w_dlg_loan_search_receive.aspx - ค้นหาสัญญา
- wd_lon_rcvdetail.aspx - รายละเอียดการเบิก
- wd_lon_rcvlon.aspx - เบิกจ่ายจริง

**หมายเหตุ:** การประมวลผลหลักอยู่ในไฟล์ wd_lon_rcvlon.aspx.cs (57,325 bytes) ซึ่งเป็น dialog หลักสำหรับการเบิกจ่าย

## 5. การจัดการข้อผิดพลาด (Error Handling)

### 5.1 Client-side Validation
- **Validate()** function ใน JavaScript
- การตรวจสอบข้อมูลก่อนส่งไป dialog

### 5.2 Server-side Validation
- การตรวจสอบใน SaveWebSheet()
- การจัดการ error ผ่าน framework มาตรฐาน

### 5.3 Data Consistency
- การตรวจสอบสถานะสัญญาก่อนเบิกจ่าย
- การตรวจสอบยอดคงเหลือ

## 6. ความปลอดภัย (Security)

### 6.1 Transaction Security
- การตรวจสอบสิทธิ์การเบิกจ่าย
- การ audit trail สำหรับการเบิกเงิน
- การป้องกันการเบิกจ่ายซ้ำ

### 6.2 Data Protection
- การตรวจสอบความถูกต้องของเลขที่สัญญา
- การป้องกันการแก้ไขข้อมูลโดยไม่ได้รับอนุญาต

## 7. ประสิทธิภาพ (Performance)

### 7.1 การโหลดข้อมูล
- การโหลดเฉพาะรายการที่พร้อมเบิกจ่าย
- UI responsive design ด้วย color coding

### 7.2 การประมวลผล
- การแยกการทำงานเป็น dialogs เพื่อลด load
- การใช้ JavaScript สำหรับ immediate feedback

## 8. การบำรุงรักษา (Maintainability)

### 8.1 Code Structure
- แยกหน้า main เป็น controller
- ใช้ dialogs สำหรับ complex operations
- User Controls สำหรับ reusability

### 8.2 Modular Design
- การแยก functionality เป็น 3 dialogs ชัดเจน
- แต่ละ dialog มี responsibility เฉพาะ
- DataSet แยกตาม dialog

## 9. การทดสอบ (Testing Considerations)

### 9.1 Unit Testing Areas
- การค้นหาสัญญา (search dialog)
- การตรวจสอบรายละเอียด (detail dialog)
- การเบิกจ่ายจริง (rcvlon dialog)
- การพิมพ์ใบเสร็จ

### 9.2 Integration Testing
- Dialog communication และ data passing
- Main page กับ dialogs integration
- การพิมพ์เอกสารและรายงาน
- Database transaction handling

### 9.3 User Acceptance Testing
- Complete disbursement workflow
- Search functionality accuracy
- Receipt printing correctness
- Error handling scenarios

## 10. ข้อเสนอแนะการพัฒนา (Development Recommendations)

### 10.1 Code Quality
- รวม business logic จาก dialogs กลับมาที่ main page
- ปรับปรุง error handling และ logging
- เพิ่ม unit tests สำหรับแต่ละ dialog

### 10.2 Performance Optimization
- เพิ่ม WCF services สำหรับ business logic
- Async operations สำหรับ complex calculations
- Caching สำหรับ contract lookup
- Optimize large DataSet files (703KB ใน wd_lon_rcvlon_ctrl)

### 10.3 Security Enhancement
- Enhanced authentication สำหรับ disbursement
- Digital signature สำหรับ transaction approval
- Real-time fraud detection
- Comprehensive audit trail

### 10.4 User Experience
- Single page application แทนการใช้ multiple dialogs
- Real-time balance checking
- Batch disbursement capability
- Mobile-friendly interface
- Progress tracking สำหรับ disbursement process

### 10.5 Business Logic Enhancement
- Automated disbursement scheduling
- Integration with banking systems
- Real-time notification system
- Comprehensive reporting dashboard
- Risk management integration

### 10.6 Technical Architecture
- พิจารณารวม 3 dialogs เป็น single page application
- ใช้ AJAX สำหรับ seamless user experience
- Implement proper state management
- Add comprehensive error recovery mechanisms

---
**หมายเหตุ:** การวิเคราะห์นี้อ้างอิงจากโค้ดจริงในระบบ ณ วันที่ 2026-09-14 โดยตรวจสอบไฟล์ทั้งหมด 36 ไฟล์ในโฟลเดอร์ ws_lon_rcvloan_ctrl และ 3 dialog subdirectories รวมขนาดประมาณ 1.1MB