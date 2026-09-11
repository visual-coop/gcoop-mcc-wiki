# 🎯 Session 1: MHD Main System Ingest Prompt

## 📋 Session Overview
- **Focus:** ASP.NET Web Applications - Screen-by-Screen Analysis
- **Scope:** /root/gcoop_hermes/mhd/GCOOP/ 
- **Template:** วิเคราะห์ทีละหน้าจอ_mhd_loan.md (existing)
- **Model:** `hermes --model fable51` (detailed screen analysis)
- **Expected Output:** 10-15 screen analysis pages
- **Time Estimate:** 2-3 hours

---

## 🎯 **MAIN PROMPT FOR SESSION 1**

```
ingest ระบบ MHD หลัก (ASP.NET Web Applications) โดยใช้ template-driven analysis

🎯 **OBJECTIVE:**
วิเคราะห์หน้าจอ ASP.NET ในระบบ MHD GCOOP แบบทีละหน้าจอตาม template ที่มีอยู่แล้ว เพื่อสร้าง comprehensive screen documentation

📂 **SCOPE:**
- Directory: /root/gcoop_hermes/mhd/GCOOP/
- Focus: ASP.NET Web Forms, Controls, และ Code-behind files
- Template: ใช้ template จาก raw/documents/วิเคราะห์ทีละหน้าจอ_mhd_loan.md

📋 **ANALYSIS REQUIREMENTS:**
1. **Screen-by-Screen Analysis:** แยกวิเคราะห์แต่ละหน้าจออย่างละเอียด
2. **10-Section Structure:** ตาม template ที่กำหนด
3. **Business Logic Focus:** เน้น business rules และ user workflows
4. **Integration Points:** ระบุจุดเชื่อมต่อกับระบบอื่น
5. **Thai Language:** ใช้ภาษาไทยในการอธิบาย business logic

🎭 **SPECIFIC FOCUS AREAS:**
- หน้าจอ Loan Management (สินเชื่อ)
- User Interface Components และ Controls
- Data Validation Rules และ Business Rules  
- Database Interactions และ WCF Service calls
- User Experience Flow และ Navigation

⚙️ **TECHNICAL REQUIREMENTS:**
- Code analysis ของ .aspx, .aspx.cs files
- Control binding และ event handling
- Session management และ security
- Error handling และ validation
- Performance considerations

📊 **OUTPUT FORMAT:**
- Entity pages สำหรับแต่ละหน้าจอ (entities/gcoop-screen-*.md)
- Concept pages สำหรับ shared functionality (concepts/gcoop-mhd-*.md)
- Cross-references ระหว่างหน้าจอ และระบบอื่น
- Update index.md และ log.md

🎯 **SUCCESS CRITERIA:**
- ครอบคลุมหน้าจอหลักทั้งหมดใน MHD system
- Business logic ชัดเจนและใช้งานได้จริง
- Technical documentation เพียงพอสำหรับ maintenance
- Cross-references ถูกต้องและครบถ้วน

เริ่มการ ingest เลย ทำทีละหน้าจอตาม template ที่มีอยู่
```

---

## 🔧 **Pre-Session Preparation**

### **1. Check Template Availability**
```bash
# Verify template exists
ls -la /root/gcoop_hermes/mhd/wiki/raw/documents/วิเคราะห์ทีละหน้าจอ*

# Read template to understand structure  
head -50 /root/gcoop_hermes/mhd/wiki/raw/documents/วิเคราะห์ทีละหน้าจอ_mhd_loan.md
```

### **2. Survey Target Directory**
```bash
# Get overview of MHD structure
find /root/gcoop_hermes/mhd/GCOOP/ -name "*.aspx" | head -20

# Check for key loan-related screens
find /root/gcoop_hermes/mhd/GCOOP/ -name "*loan*" -o -name "*สินเชื่อ*"
```

### **3. Set Optimal Model**
```bash
# Use advanced model for detailed analysis
hermes --model fable51 [your prompt]
```

---

## 📊 **Expected Outputs**

### **Entity Pages (10-15 pages):**
- `entities/gcoop-screen-ws-lon-reqloan.md` - หน้าจอขอสินเชื่อ
- `entities/gcoop-screen-ws-lon-apvloan.md` - หน้าจออนุมัติสินเชื่อ
- `entities/gcoop-screen-ws-lon-promise.md` - หน้าจอทำสัญญา
- `entities/gcoop-screen-ws-lon-rcvloan.md` - หน้าจอเบิกจ่ายเงินกู้
- `entities/gcoop-screen-ws-lon-payment.md` - หน้าจอชำระเงินกู้
- `entities/gcoop-screen-ws-lon-search.md` - หน้าจอค้นหาข้อมูล
- ... (อื่นๆ ตามที่พบในระบบ)

### **Concept Pages (3-5 pages):**
- `concepts/gcoop-mhd-architecture.md` - โครงสร้าง MHD system
- `concepts/gcoop-mhd-security.md` - ระบบรักษาความปลอดภัย
- `concepts/gcoop-mhd-validation.md` - ระบบตรวจสอบข้อมูล
- `concepts/gcoop-mhd-navigation.md` - ระบบนำทาง

---

## ⚡ **Quick Start Commands**

### **Option 1: Full Automatic Ingest**
```bash
hermes --model fable51 "ingest ระบบ MHD หลัก (ASP.NET Web Applications) โดยใช้ template-driven analysis

🎯 OBJECTIVE: วิเคราะห์หน้าจอ ASP.NET ในระบบ MHD GCOOP แบบทีละหน้าจอตาม template

📂 SCOPE: /root/gcoop_hermes/mhd/GCOOP/ - ASP.NET Web Forms และ Code-behind

📋 REQUIREMENTS: Screen-by-screen analysis, 10-section structure ตาม template, Business logic focus, Integration points, Thai language

เริ่มการ ingest เลย ทำทีละหน้าจอตาม template วิเคราะห์ทีละหน้าจอ_mhd_loan.md"
```

### **Option 2: Step-by-Step Guided**
```bash
hermes --model fable51 "เริ่มต้น Session 1: MHD System Analysis

ขั้นตอนที่ 1: ตรวจสอบโครงสร้างไดเรกทอรี่ /root/gcoop_hermes/mhd/GCOOP/ และหาหน้าจอ ASP.NET ทั้งหมด

จากนั้นเริ่มวิเคราะห์ทีละหน้าจอตาม template ที่มีอยู่"
```

---

## 🎯 **Success Indicators**

### **Quality Checkpoints:**
- [ ] ใช้ template structure ครบทั้ง 10 sections
- [ ] Business logic อธิบายเป็นภาษาไทยที่เข้าใจง่าย  
- [ ] Technical details เพียงพอสำหรับ developer
- [ ] Cross-references ชัดเจนและถูกต้อง
- [ ] Integration points ระบุครบถ้วน

### **Coverage Checkpoints:**
- [ ] หน้าจอหลักทุกหน้าของ loan system
- [ ] User authentication และ authorization screens
- [ ] Search และ reporting screens  
- [ ] Administrative functions
- [ ] Error handling และ validation screens

---

## 📝 **Post-Session Review**

### **After Completion, Run:**
```bash
# Check wiki growth
hermes "แสดงรายการ entity pages ใหม่ที่สร้างใน Session 1"

# Verify cross-references
hermes "ตรวจสอบ cross-references ระหว่าง MHD screens"

# Test query functionality  
hermes "ขอดูข้อมูลหน้าจอขอสินเชื่อ ws_lon_reqloan"
```

### **Quality Assurance:**
```bash
# Check for proper Thai language usage
hermes "ตรวจสอบการใช้ภาษาไทยใน MHD screen documentation"

# Verify template compliance
hermes "เปรียบเทียบ structure ของ screen pages กับ template"
```