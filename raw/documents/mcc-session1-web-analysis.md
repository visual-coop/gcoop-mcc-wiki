# MCC Session 1: Web Interface Analysis

## Overview
การวิเคราะห์ Web Interface ของระบบ MCC (Member Credit Cooperative) ใน GCOOP project ที่ทำหน้าที่เป็นระบบจัดการสินเชื่อสมาชิกสหกรณ์

## Main Web Structure

### 1. Entry Point และ Application Selection
- **ApplicationSelectionPage.aspx** - หน้าหลักเลือกระบบ
  - ใช้ Master Page Framework 
  - มีการจัดการ Application Icons แบบ Grid Layout
  - รองรับการเลือกระบบย่อยต่างๆ (Saving, Loan, Member Management)

### 2. Master Pages Framework
- **Frame.Master** - Layout หลักของระบบ
  - TopBar Control (ส่วนหัวระบบ)
  - MenuBar Control (เมนูหลัก)
  - MenuSub Control (เมนูย่อย)
  - DatePicker Control (การเลือกวันที่)
  - รองรับ JavaScript Framework สำหรับ UI Interactions

### 3. Core Application Modules

#### 3.1 Member & Share Management (mbshr)
**MCC-Specific Screens:**
- `ws_mem_adjfamily_mcc.aspx` - ปรับปรุงข้อมูลครอบครัวสมาชิก MCC
  - Form controls สำหรับข้อมูลส่วนตัวและครอบครัว
  - Validation และ Search functionality
  - Address และ Contact information management

**Main Member Screens:**
- `ws_mem_memberdetail.aspx` - รายละเอียดสมาชิก
- `ws_mem_reqappl_gain.aspx` - คำขอรับสมาชิก
- `ws_mem_apvappl.aspx` - อนุมัติสมาชิกใหม่
- `ws_mem_reqresign.aspx` - คำขอลาออก
- `ws_shr_withdraw.aspx` - เบิกหุ้น

#### 3.2 Loan Management (loan)
**Core Loan Processing Screens:**
- `ws_lon_reqloan.aspx` - คำขอสินเชื่อ
- `ws_lon_apvloan.aspx` - อนุมัติสินเชื่อ  
- `ws_lon_rcvloan.aspx` - รับเงินกู้
- `ws_lon_payment.aspx` - ชำระหนี้สินเชื่อ
- `ws_lon_estpayment.aspx` - ประมาณการชำระหนี้

**Loan Administration:**
- `ws_lon_cfloantype.aspx` - กำหนดประเภทสินเชื่อ
- `ws_lon_cfintrate.aspx` - กำหนดอัตราดอกเบิ้ย
- `ws_lon_collateral_master.aspx` - จัดการหลักประกัน
- `ws_lon_collateral_car.aspx` - หลักประกันรถยนต์

#### 3.3 Borrowing Management (brw) 
**Payment Management:**
- `ws_pm_loan_payment.aspx` - การชำระเงินกู้
- `ws_pm_loan_agreement.aspx` - สัญญาเงินกู้
- `ws_pm_requestloan.aspx` - คำขอเงินกู้

#### 3.4 Account Management (account)
**MCC-Specific:**
- `ws_acc_budget_setamt_mcc.aspx` - กำหนดจำนวนงบประมาณ MCC

### 4. Report Criteria Screens
**MCC-Specific Report Criteria:**
- `u_cri_coopid_memno_loancont_refcollno_mcc.aspx` - เกณฑ์รายงานสัญญาเงินกู้ MCC
- `uc_coopid_rdate_hp_disb_mcc.aspx` - เกณฑ์รายงานเบิกจ่าย MCC
- `u_cri_coopid_rdate_rmembgroup_status_mcc.aspx` - เกณฑ์รายงานสถานะกลุ่มสมาชิก MCC
- `uc_coopid_rdate_loandisbursement_bank_mcc.aspx` - เกณฑ์รายงานเบิกจ่ายผ่านธนาคาร MCC
- `uc_lc_rdate_rloantype_mcc.aspx` - เกณฑ์รายงานประเภทสินเชื่อ MCC

### 5. Supporting Modules

#### 5.1 Document Management (rdc)
- `ws_rdc_documentreceive.aspx` - รับเอกสาร
- `ws_rdc_documentsend.aspx` - ส่งเอกสาร
- `ws_rdc_documentsafe.aspx` - เก็บรักษาเอกสาร

#### 5.2 HR Management (hr)
- `ws_hr_master.aspx` - ข้อมูลพนักงาน
- `ws_hr_overtime_new.aspx` - การทำงานล่วงเวลา
- `ws_hr_bonus_board.aspx` - โบนัสกรรมการ

#### 5.3 Duration Investment (durinvt)
- `ws_dur_durtmaster.aspx` - ข้อมูลหลักการลงทุนระยะยาว
- `ws_invt_invtmaster.aspx` - ข้อมูลหลักการลงทุน

## Technical Architecture

### Frontend Technology Stack
- **ASP.NET Web Forms** with Master Pages
- **JavaScript/jQuery** สำหรับ client-side interactions
- **CSS** สำหรับ styling และ layout
- **Custom Controls** (.ascx) สำหรับ reusable components

### Control Patterns
1. **DataSource Controls** - การจัดการข้อมูล
2. **FormView Controls** - การแสดงฟอร์มข้อมูล  
3. **Repeater Controls** - การแสดงรายการข้อมูล
4. **Custom User Controls** - components ที่ใช้ร่วมกัน

### JavaScript Framework
- **DataSourceTool.js** - การจัดการข้อมูล client-side
- **DatePicker.js** - การเลือกวันที่
- **JsScriptFrame.js** - ฟังก์ชันพื้นฐานของระบบ
- **CallPlsql.js** - การเรียกใช้ stored procedures

## MCC-Specific Features

### 1. Member Credit Functions
- การจัดการข้อมูลสมาชิก MCC แยกต่างหากจากระบบทั่วไป
- ระบบรายงานเฉพาะสำหรับ MCC
- การจัดการงบประมาณเฉพาะ MCC

### 2. Specialized Reports  
- รายงานเบิกจ่ายแยกตาม MCC
- รายงานสถานะสมาชิกกลุ่ม MCC
- รายงานการจัดการสินเชื่อเฉพาะ MCC

### 3. Integration Points
- เชื่อมต่อกับระบบธนาคารสำหรับการเบิกจ่าย
- ระบบการจัดการเอกสารสำหรับ MCC
- การส่งออกข้อมูลผ่าน File Import/Export

## Navigation และ User Experience

### Menu Structure
1. **Top Level**: Application Selection
2. **Module Level**: ตามหน้าที่งาน (Member, Loan, Account, etc.)
3. **Function Level**: งานเฉพาะใน module นั้นๆ

### Common UI Patterns
- **Search และ Selection**: ปุ่ม "..." สำหรับค้นหา
- **Data Entry Forms**: Layout แบบ table-based
- **Validation**: JavaScript client-side validation
- **Navigation**: Master-Detail relationships

## Comparison กับ MHD System
เนื่องจากระบบ MCC ใช้โครงสร้างคล้ายคลึงกับ MHD แต่เน้นไปที่:

1. **Member Credit Management** แทน Medical/Health Data
2. **Financial Transactions** แทน Medical Records  
3. **Cooperative Banking** แทน Healthcare Operations
4. **Loan Processing** เป็น core function หลัก

## สรุป
ระบบ MCC Web Interface เป็นระบบจัดการสินเชื่อสมาชิกสหกรณ์ที่ครอบคลุม โดยมี web screens หลักในการจัดการสมาชิก การให้สินเชื่อ การชำระหนี้ และการรายงาน พร้อมด้วยระบบรายงานเฉพาะสำหรับ MCC และการเชื่อมต่อกับระบบภายนอก