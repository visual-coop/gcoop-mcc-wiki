---
title: วิเคราะห์หน้าจอ ws_lon_reqloan (ขอกู้)
created: 2026-09-14
updated: 2026-09-14
type: raw
tags: [raw-source, mhd-loan, screen-analysis]
sources: [source-code]
---

# วิเคราะห์หน้าจอ ws_lon_reqloan (ขอกู้)

## ⚙️ ตั้งค่าก่อนใช้งาน

```
ชื่อระบบ                                = ระบบสินเชื่อ
ชื่อหน้าจอ                               = ขอกู้  
Path ของหน้าจอนี้                        = \mhd\GCOOP\Saving\Applications\loan\ws_lon_reqloan_ctrl\
Path PB Process (Extend)              = ไม่มี
Path PB Process (Core)                 = \CORE\GCOOP\PBProcess\
Path PB Service                        = \CORE\GCOOP\PBService125\
ไฟล์ตัวอย่าง (calibration example)        = ไม่มี
โฟลเดอร์เก็บผลลัพธ์                       = \CORE\.output\
```

## 1. ภาพรวมหน้าจอ

### 1.1 ชื่อหน้าจอและวัตถุประสงค์
**หน้าจอ:** ws_lon_reqloan (ขอกู้)
**วัตถุประสงค์:** หน้าจอสำหรับยื่นคำขอสินเชื่อของสมาชิกสหกรณ์ รองรับการกรอกข้อมูลคำขอ การคำนวณดอกเบี้ย การระบุค้ำประกัน และการจัดการเอกสารประกอบ

### 1.2 ไฟล์หลักและขนาด
- **ws_lon_reqloan.aspx** (62,225 bytes) - หน้า UI หลัก
- **ws_lon_reqloan.aspx.cs** (307,383 bytes) - Business Logic
- **DataSet2.Designer.cs** (2,056,156 bytes) - Dataset definitions
- **8 User Controls** - DsMain, DsDetail, DsLoanClr, DsLoanColl, DsLoanClrOth, DsLoanIns, DsKeepData, DsLoanObj
- **2 Dialog Subdirectories** - wd_lon_mthoth_minus_ctrl, wd_lon_mthoth_plus_ctrl

### 1.3 User Controls และหน้าจอย่อย
1. **DsMain.ascx** - ข้อมูลหลักสมาชิก
2. **DsDetail.ascx** - รายละเอียดคำขอสินเชื่อ  
3. **DsLoanClr.ascx** - หักค่าใช้จ่าย
4. **DsLoanColl.ascx** - ค้ำประกัน
5. **DsLoanClrOth.ascx** - หักอื่นๆ
6. **DsLoanIns.ascx** - ประกันภัย
7. **DsKeepData.ascx** - เก็บข้อมูล
8. **DsLoanObj.ascx** - วัตถุประสงค์การกู้

**หน้าจอย่อย (Dialogs):**
- wd_lon_mthoth_minus_ctrl - จัดการรายการหัก
- wd_lon_mthoth_plus_ctrl - จัดการรายการเพิ่ม

## 2. โครงสร้างข้อมูล (Data Structure)

### 2.1 DataSource หลัก
จากการวิเคราะห์ JavaScript พบ DataSourceTool ทั้งหมด 8 ตัว:
- **dsMain** - ข้อมูลสมาชิกและประเภทสินเชื่อ
- **dsDetail** - รายละเอียดยอดกู้และเงื่อนไข
- **dsLoanClr** - รายการหักค่าใช้จ่าย
- **dsLoanColl** - รายการค้ำประกัน  
- **dsLoanClrOth** - รายการหักอื่นๆ
- **dsLoanIns** - ข้อมูลประกันภัย
- **dsKeepData** - ข้อมูลเก็บรักษา
- **dsLoanObj** - วัตถุประสงค์การกู้

### 2.2 ฟิลด์สำคัญ
จากโค้ด JavaScript Validation:
- **loanrequest_amt** - ยอดเงินขอกู้
- **loantypeCode** - รหัสประเภทสินเชื่อ  
- **expense_code** - รหัสการจ่ายเงิน (TRN=โอน)
- **expense_accid** - เลขบัญชีรับเงิน
- **ref_collno** - เลขที่หลักประกัน
- **collactive_amt** - ยอดค้ำประกันใช้งานได้
- **period_payamt** - ยอดชำระต่องวด
- **period_payment** - จำนวนงวดชำระ
- **recvestimate_amt** - ยอดรับสุทธิ

## 3. กระบวนการทำงาน (Business Flow)

### 3.1 ขั้นตอนหลัก
1. **เริ่มต้น** - WebSheetLoadBegin() โหลดข้อมูลเริ่มต้น
2. **กรอกข้อมูล** - ผ่าน 8 User Controls ตาม workflow
3. **คำนวณ** - of_calperiodpayment() คำนวณยอดชำระ
4. **ตรวจสอบ** - Validate() เช็คความถูกต้อง
5. **บันทึก** - SaveWebSheet() บันทึกข้อมูล

### 3.2 การตรวจสอบ (Validation)
จาก Validate() function ในโค้ด:
```javascript
// เช็คการค้ำประกัน
if (dsLoanColl.GetItem(i, "ref_collno") == null) {
    alert("เนื่องจากกรอกการค้ำประกันไม่ถูกต้อง กรุณาตรวจสอบ !!!");
}

// เช็คยอดค้ำประกัน vs ยอดขอกู้
if (collactive_amt < loanrequest_amt && loantypeCode != "38" && loantypeCode != "28") {
    alert("เนื่องจากยอดค้ำประกันไม่ถูกต้อง กรุณาตรวจสอบ/ หรือทำการกด Re ค้ำประกันใหม่ !!!");
}

// เช็คบัญชีโอน
if (expense_code == "TRN" && expense_accid.length < 5) {
    alert("เนืองจากจ่ายเป็นโอนเข้าบัญชีสหกหรณ์ แต่เลขบัญชีไม่มี กรูณาระบุเลขบัญชีเงินฝากด้วย !!!");
}
```

### 3.3 การคำนวณ
จาก SaveWebSheet() มีการตรวจสอบ:
```csharp
// เช็คการชำระแบบคงต้น
if(dsDetail.DATA[0].LOANPAYMENT_TYPE == 1 && dsMain.DATA[0].LOANTYPE_CODE != "30" && dsMain.DATA[0].LOANTYPE_CODE != "2A")
{
    decimal diff = dsDetail.DATA[0].PERIOD_PAYAMT * dsDetail.DATA[0].PERIOD_PAYMENT - dsDetail.DATA[0].LOANREQUEST_AMT;
    if(Math.Abs(diff) > 1) {
        // แจ้งเตือนยอดชำระไม่ถูกต้อง
    }
}
```

## 4. การเชื่อมต่อระบบภายนอก (External Integration)

### 4.1 WCF Service Calls
พบการเรียก WCF Services ทั้งหมด 35 calls ในหมวดหมู่:

**NCommon Service:**
- of_getnewdocno() - สร้างเลขที่เอกสารใหม่
- of_getpostingdate(), of_getpostingdate2() - ดึงวันที่ทำรายการ
- of_isworkingdate() - ตรวจสอบวันทำการ

**NLoan Service:**
- of_calperiodpay() - คำนวณยอดชำระรายงวด
- of_computeinterest(), of_computeinterest2() - คำนวณดอกเบี้ย
- of_getcollpermiss() - ตรวจสอบสิทธิ์ค้ำประกัน
- of_getcollusecontamt(), of_getcollusereqamt() - ยอดค้ำประกันคงเหลือ/ใช้ได้
- of_checkcollmancount() - ตรวจนับผู้ค้ำ
- of_getgrplonpermiss() - สิทธิ์กู้กลุ่ม
- of_isvalidcoll(), of_isvalidcoll2() - ตรวจสอบค้ำประกันถูกต้อง

**Shrlon Service:**
- of_roundmoney() - ปัดเศษเงิน

### 4.2 PowerBuilder Process Calls
**ไม่พบ** การเรียก runProcessing หรือ runProcessingExtend ในโค้ดหน้าจอนี้

### 4.3 Dialog Integration
หน้าจอเรียก dialogs ต่างๆ ผ่าน JavaScript:
- **wd_lon_openloanreq.aspx** - เปิดคำขอเดิม
- **wd_mem_search.aspx** - ค้นหาสมาชิก  
- **wd_mem_deptaccount.aspx** - ค้นหาบัญชีแผนก
- **wd_lon_securities.aspx** - ค้นหาหลักทรัพย์
- **wd_ins_insure_search.aspx** - ค้นหาประกันภัย
- **wd_lon_reqcolldetail.aspx** - รายละเอียดค้ำประกัน
- **wd_lon_mthoth_minus.aspx** - รายการหัก
- **wd_lon_mthoth_plus.aspx** - รายการเพิ่ม

## 5. การจัดการข้อผิดพลาด (Error Handling)

### 5.1 JavaScript Validation
- ตรวจสอบค้ำประกัน: ref_collno ต้องไม่ว่าง
- ตรวจสอบยอดค้ำประกัน: collactive_amt >= loanrequest_amt (ยกเว้นประเภท 38, 28)
- ตรวจสอบบัญชีโอน: expense_code="TRN" ต้องมี expense_accid ครบ 5 หลัก

### 5.2 Server-side Validation
จาก SaveWebSheet():
- ตรวจสอบยอดชำระ×งวด = ยอดกู้ (สำหรับแบบคงต้น)
- ตรวจสอบยอดรับสุทธิไม่ติดลบ: RECVESTIMATE_AMT >= -1
- แสดง error ผ่าน LtServerMessage.Text + WebUtil.ErrorMessage()

### 5.3 การแจ้งเตือน
ใช้ JavaScript alert() และ C# error message display ผ่าน WebUtil framework

## 6. ความปลอดภัย (Security)

### 6.1 Authentication & Authorization
ไม่พบข้อมูลชัดเจนในโค้ดที่วิเคราะห์ แต่อ้างอิงผ่าน Master Page Framework

### 6.2 Input Validation
- JavaScript validation ด้านหน้า (client-side)
- Server-side validation ใน SaveWebSheet()
- DataSourceTool framework handling

### 6.3 Data Access Control
ผ่าน WCF Services layer - ไม่มีการเรียก Database โดยตรง

## 7. ประสิทธิภาพ (Performance)

### 7.1 การโหลดข้อมูล
- ใช้ DataSourceTool framework สำหรับ lazy loading
- WebSheetLoadBegin() สำหรับ initial load
- User Controls แยกการโหลดตาม section

### 7.2 การคำนวณ
- เรียก WCF services สำหรับ business calculation
- JavaScript calculation สำหรับ UI immediate feedback
- Server-side final validation

### 7.3 Memory Management
- DataSet2.Designer.cs ขนาด 2MB+ บ่งบอกถึง large dataset schema
- User Controls แยก state management

## 8. การบำรุงรักษา (Maintainability)

### 8.1 Code Structure
- แยก UI (ASPX) และ Logic (CS) ชัดเจน
- User Controls สำหรับ reusable components
- WCF Services สำหรับ business layer separation

### 8.2 Configuration Management
- ใช้ DataSourceTool configuration
- Master Page framework สำหรับ common elements
- JavaScript functions แยกตาม responsibility

### 8.3 Error Logging
- Server error messages ผ่าน WebUtil.ErrorMessage()
- Client alerts สำหรับ immediate feedback

## 9. การทดสอบ (Testing Considerations)

### 9.1 Unit Testing Areas
- WCF Service integration points (35 service calls)
- JavaScript validation logic
- Server-side calculation accuracy
- User Control data binding

### 9.2 Integration Testing
- Dialog window interactions
- DataSourceTool framework integration  
- Cross-control data synchronization
- Error handling workflows

### 9.3 User Acceptance Testing
- Complete loan request workflow
- Validation message accuracy
- Calculation correctness
- Print functionality

## 10. ข้อเสนอแนะการพัฒนา (Development Recommendations)

### 10.1 Code Quality
- ปรับปรุง error message ให้เป็น resource file สำหรับ localization
- เพิ่ม unit tests สำหรับ critical calculation methods
- พิจารณา refactor ขนาดใหญ่ของ DataSet2.Designer.cs

### 10.2 Performance Optimization
- Lazy loading สำหรับ User Controls
- Async WCF calls เพื่อปรับปรุง UI responsiveness
- Caching สำหรับข้อมูลอ้างอิง (lookup data)

### 10.3 Security Enhancement
- Input sanitization สำหรับ SQL injection prevention
- CSRF protection
- Session management review

### 10.4 User Experience  
- Progress indicators สำหรับ long-running operations
- Auto-save functionality
- Enhanced error message clarity
- Mobile-responsive design considerations

---
**หมายเหตุ:** การวิเคราะห์นี้อ้างอิงจากโค้ดจริงในระบบ ณ วันที่ 2026-09-14 โดยตรวจสอบไฟล์ทั้งหมด 24 ไฟล์ในโฟลเดอร์ ws_lon_reqloan_ctrl และ dialog subdirectories