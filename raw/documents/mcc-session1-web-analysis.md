# MCC Session 1: Web Interface Analysis

**วันที่วิเคราะห์:** 2026-09-14  
**โปรเจค:** MCC (Member Credit Cooperative) System  
**Path:** `/root/gcoop_hermes/mcc/`  
**Focus:** ระบบสหกรณ์ออมทรัพย์ MCC เฉพาะ

---

## Executive Summary

การวิเคราะห์ Web Interface ของระบบ MCC (Member Credit Cooperative) ซึ่งเป็นระบบสหกรณ์ออมทรัพย์ที่มีขนาดใหญ่และครอบคลุม โดยมีความโดดเด่นด้าน HR, RDC (Document Management), และ Welfare Systems ที่แตกต่างจากสหกรณ์อื่น

## 1. MCC Project Structure Overview

### 1.1 โครงสร้างหลัก
**Base Path:** `/root/gcoop_hermes/mcc/`

| Directory | Files | Subdirs | Size | Purpose |
|-----------|-------|---------|------|---------|
| **GCOOP** | 3 | 7 | 343M | Web Application Framework |
| **iSavBOfc** | 223 | 7 | 192M | PowerBuilder Banking Core |
| **winLKE** | 1 | 2 | 67M | Magnetic Card Reader |
| **winUPBOOK** | 8 | 5 | 96M | Passbook Update Tool |
| **CONVERT_MCC** | 18 | 2 | 4.7M | Data Migration Tools |

### 1.2 GCOOP Web Application Components
**Path:** `/root/gcoop_hermes/mcc/GCOOP/`

| Component | Files | Size | Description |
|-----------|-------|------|-------------|
| **Saving** | 6,721 | 196M | Main Web Application (ASP.NET) |
| **PBProcess** | 26 | 68M | PowerBuilder Batch Processing |
| **iReport** | 1,345 | 59M | Report Generation System |
| **PBReport125** | 20 | 16M | iReport 5.0.4 Reporting |
| **XMLConfig** | 40 | 3.7M | System Configuration Files |
| **PLSQL** | 42 | 312K | Oracle Database Scripts |
| **Renci.SshNet** | 316 | 2.7M | SSH.NET Library |

## 2. MCC Web Applications Analysis

### 2.1 Application Modules Statistics
**Path:** `/root/gcoop_hermes/mcc/GCOOP/Saving/Applications/`

| Module | ASPX Files | C# Files | Total Files | Primary Function |
|--------|------------|----------|-------------|------------------|
| **hr** | 81 | 648 | 1,152 | Human Resources Management |
| **rdc** | 57 | 392 | 748 | Document Management System |
| **loan** | 38 | 404 | 688 | สินเชื่อและเงินกู้ |
| **mbshr** | 35 | 356 | 616 | สมาชิกและหุ้น |
| **walfare** | 33 | 241 | 441 | ระบบสวัสดิการ |
| **finance** | 23 | 153 | 284 | การเงินและบัญชี |
| **pm** | 21 | 165 | 256 | Project Management |
| **brw** | 19 | 118 | 185 | Borrowing System |
| **durinvt** | 18 | 133 | 221 | Duration Investment |
| **ixp** | 12 | 57 | 108 | Import/Export Processing |
| **assist** | 11 | 120 | 207 | Member Assistance |
| **insurance** | 9 | 66 | 118 | ประกันภัย |
| **keeping** | 7 | 52 | 91 | เงินฝากออมทรัพย์ |
| **investment** | 6 | 68 | 117 | การลงทุน |

### 2.2 MCC-Specific Key Features

#### 2.2.1 MCC-Specific Screens
ระบบมีหน้าจอเฉพาะสำหรับ MCC ที่แตกต่างจากสหกรณ์อื่น:

1. **ws_mem_adjfamily_mcc.aspx** - ปรับปรุงข้อมูลครอบครัวสมาชิก MCC
   - Path: `mbshr/ws_mem_adjfamily_mcc_ctrl/`
   - Features: Family member management with MCC-specific validation
   - JavaScript validation for relation codes

2. **ws_acc_budget_setamt_mcc.aspx** - การตั้งงบประมาณ MCC
   - Path: `account/ws_acc_budget_setamt_mcc_ctrl/`  
   - Features: MCC budget management

3. **ws_kep_fileimport_mcc.aspx** - นำเข้าไฟล์ข้อมูล MCC
   - Path: `ixp/ws_kep_fileimport_mcc_ctrl/`
   - Features: MCC-specific data import functionality

#### 2.2.2 MCC Distinctive Modules

**HR Module (ขนาดใหญ่ที่สุด):**
- 81 ASPX files, 648 C# files
- ระบบบริหารทรัพยากรบุคคลครบถ้วน
- แสดงให้เห็นว่า MCC มีฟังก์ชัน HR ที่ซับซ้อน

**RDC Module (Document Management):**
- 57 ASPX files, 392 C# files  
- ระบบจัดการเอกสารแบบครบวงจร
- เป็นจุดเด่นของ MCC ที่แตกต่างจากสหกรณ์ทั่วไป

**Welfare Module:**
- 33 ASPX files, 241 C# files
- ระบบสวัสดิการสมาชิกขยาย
- แสดงการมุ่งเน้นสวัสดิการสมาชิก

## 3. Technology Architecture

### 3.1 Web Framework
- **ASP.NET Web Forms** with Master Pages (`Frame.Master`)
- **C# CodeBehind** สำหรับ Business Logic  
- **User Controls** (.ascx) สำหรับ Reusable Components
- **DataSet/DataSource** Architecture

### 3.2 Key Technical Components
```
MCC Web Application Structure:
├── Frame.Master (Master Page Framework)
├── Applications/ (Business Modules)
│   ├── mbshr/ (Member & Share Management)
│   ├── loan/ (Loan Processing)  
│   ├── hr/ (Human Resources - MCC Strength)
│   ├── rdc/ (Document Management - MCC Unique)
│   └── welfare/ (Welfare System - MCC Focus)
├── Controls/ (Shared UI Components)
└── Configuration/ (XML Config Files)
```

### 3.3 Database Integration
- **Oracle Database** backend
- **PLSQL** stored procedures (42 files)
- **ADO.NET DataSet** patterns
- **XML Configuration** for connection strings

## 4. MCC Business Characteristics

### 4.1 MCC ความโดดเด่น
1. **HR-Focused Cooperative** - มี HR module ขนาดใหญ่ที่สุด
2. **Document Management Excellence** - RDC system ครบถ้วน  
3. **Comprehensive Welfare** - ระบบสวัสดิการขยาย
4. **Project Management** - มี PM module สำหรับบริหารโครงการ

### 4.2 ขนาดและความซับซ้อน
- **Total Web Files:** 6,721 files (196MB)
- **Application Modules:** 23 active modules
- **Database Scripts:** 42 PLSQL files
- **Reports:** 1,345 report templates

### 4.3 เปรียบเทียบกับสหกรณ์ทั่วไป
MCC มีความครบถ้วนสูงกว่าสหกรณ์ธรรมดา:
- **HR Management** เป็นจุดแข็งหลัก
- **Document Management** ระบบจัดการเอกสารที่ทันสมัย  
- **Welfare Systems** ครอบคลุมสวัสดิการสมาชิก
- **Project Management** สำหรับบริหารโครงการขนาดใหญ่

## 5. Technical Observations

### 5.1 Code Structure
- มีไฟล์เฉพาะ MCC (`*_mcc.aspx`) แยกจาก templates มาตรฐาน
- ใช้ User Control pattern อย่างเป็นระบบ (DsMain.ascx, DsList.ascx)  
- JavaScript validation ใน client-side
- ADO.NET DataSet architecture

### 5.2 Integration Points
- **PowerBuilder Integration** ผ่าน PBProcess modules
- **Report Integration** ผ่าน iReport 5.0.4
- **Database Integration** ผ่าน Oracle PLSQL packages
- **File Management** ผ่าน import/export systems

## 6. Conclusion

MCC (Member Credit Cooperative) เป็นระบบสหกรณ์ออมทรัพย์ที่มีความครอบคลุมสูง โดยเฉพาะด้าน HR Management, Document Management, และ Welfare Systems ซึ่งแตกต่างจากสหกรณ์ทั่วไปที่มุ่งเน้นเฉพาะสินเชื่อและเงินฝาก

**จุดแข็งของ MCC:**
- ระบบ HR ครบถ้วนและซับซ้อน (81 หน้าจอ)
- Document Management ที่ทันสมัย (RDC)  
- Welfare System ที่ครอบคลุม
- Project Management capabilities

ระบบนี้เหมาะสำหรับสหกรณ์ขนาดใหญ่ที่ต้องการการบริหารจัดการแบบองค์กรสมัยใหม่ ไม่เพียงแค่การเงินเบื้องต้น

---
**Analysis Date:** 2026-09-14  
**Total Files Analyzed:** 6,721+ web files  
**System Type:** Comprehensive Cooperative Management  
**Technical Focus:** ASP.NET + Oracle + PowerBuilder Integration