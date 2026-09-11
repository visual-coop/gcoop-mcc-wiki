# 🔄 Session 2: PBProcess Modules Ingest Prompt

## 📋 Session Overview
- **Focus:** PowerBuilder Batch Processing Systems - Module Analysis
- **Scope:** /root/gcoop_hermes/mhd/PBProcess/
- **Template:** Create new PowerBuilder module analysis template
- **Model:** `hermes --model smart` (technical analysis)
- **Expected Output:** 11 module analysis pages
- **Time Estimate:** 1-2 hours

---

## 🎯 **MAIN PROMPT FOR SESSION 2**

```
ingest ระบบ PBProcess (PowerBuilder Batch Processing) โดยสร้าง template ใหม่และทำ module-by-module analysis

🎯 **OBJECTIVE:**
วิเคราะห์ PowerBuilder modules ในระบบ PBProcess GCOOP แบบทีละ module เพื่อสร้าง comprehensive batch processing documentation

📂 **SCOPE:**
- Directory: /root/gcoop_hermes/mhd/PBProcess/
- Focus: PowerBuilder .pbl files, batch processing logic, และ scheduling
- Template: สร้าง template ใหม่สำหรับ PowerBuilder module analysis

📋 **ANALYSIS REQUIREMENTS:**
1. **Module-by-Module Analysis:** แยกวิเคราะห์แต่ละ PBL module อย่างละเอียด
2. **Batch Processing Focus:** เน้น batch job logic และ data processing
3. **Performance Analysis:** ประเมิน performance และ resource usage
4. **Integration Points:** ระบุการเชื่อมต่อกับ database และระบบอื่น
5. **Thai Language:** ใช้ภาษาไทยในการอธิบาย business logic

🎭 **SPECIFIC FOCUS AREAS:**
- Batch Job Definitions และ Scheduling
- Data Processing Logic และ Algorithms
- Database Operations (Oracle PL/SQL integration)
- Error Handling และ Recovery Mechanisms
- Performance Optimization และ Resource Management

⚙️ **TECHNICAL REQUIREMENTS:**
- PowerBuilder .pbl structure analysis
- DataWindow objects และ data access patterns
- Stored procedure calls และ database transactions
- Job dependencies และ execution sequences
- Logging และ monitoring capabilities

📊 **OUTPUT FORMAT:**
- Entity pages สำหรับแต่ละ module (entities/gcoop-pbprocess-*.md)
- Concept pages สำหรับ shared functionality (concepts/gcoop-batch-*.md)
- Template creation: templates/powerbuilder-module-analysis.md
- Cross-references กับ MHD system และ database components

🎯 **SUCCESS CRITERIA:**
- ครอบคลุม PBProcess modules ทั้งหมด (11 modules)
- Batch processing logic ชัดเจนและใช้งานได้จริง
- Performance considerations เพียงพอสำหรับ optimization
- Cross-references ถูกต้องและครบถ้วน

เริ่มการ ingest เลย สร้าง template ก่อนแล้วทำทีละ module
```

---

## 🔧 **Pre-Session Preparation**

### **1. Survey PBProcess Structure**
```bash
# Get overview of PBProcess modules
find /root/gcoop_hermes/mhd/PBProcess/ -name "*.pbl" | sort

# Check for documentation files
find /root/gcoop_hermes/mhd/PBProcess/ -name "*.txt" -o -name "*.doc" -o -name "*.pdf"

# Look for configuration files
find /root/gcoop_hermes/mhd/PBProcess/ -name "*.ini" -o -name "*.cfg" -o -name "*.xml"
```

### **2. Create PowerBuilder Template First**
```bash
hermes --model smart "สร้าง template สำหรับ PowerBuilder module analysis โดยเน้น:
1. Module Structure และ Components
2. Batch Processing Logic
3. Database Integration
4. Performance Characteristics
5. Error Handling
6. Dependencies และ Integration Points

สร้างเป็น templates/powerbuilder-module-analysis.md"
```

### **3. Set Technical Model**
```bash
# Use smart model for technical analysis
hermes --model smart [your prompt]
```

---

## 📊 **Expected Outputs**

### **Template Creation:**
- `templates/powerbuilder-module-analysis.md` - PowerBuilder module analysis template

### **Entity Pages (11 modules):**
- `entities/gcoop-pbprocess-loan-batch.md` - สินเชื่อ batch processing
- `entities/gcoop-pbprocess-saving-batch.md` - เงินฝาก batch processing  
- `entities/gcoop-pbprocess-interest-calc.md` - คำนวณดอกเบี้ย
- `entities/gcoop-pbprocess-report-gen.md` - สร้างรายงาน
- `entities/gcoop-pbprocess-data-sync.md` - ซิงค์ข้อมูล
- `entities/gcoop-pbprocess-backup.md` - สำรองข้อมูล
- `entities/gcoop-pbprocess-maintenance.md` - บำรุงรักษาระบบ
- ... (อื่นๆ ตามที่พบในระบบ)

### **Concept Pages (3-5 pages):**
- `concepts/gcoop-batch-processing-architecture.md` - โครงสร้าง batch processing
- `concepts/gcoop-powerbuilder-integration.md` - การเชื่อมต่อ PowerBuilder
- `concepts/gcoop-job-scheduling.md` - ระบบ scheduling งาน
- `concepts/gcoop-batch-performance.md` - การปรับปรุงประสิทธิภาพ

---

## ⚡ **Quick Start Commands**

### **Option 1: Template Creation First**
```bash
hermes --model smart "สร้าง PowerBuilder Module Analysis Template

📋 TEMPLATE REQUIREMENTS:
- Module Overview (ความเป็นมา วัตถุประสงค์)
- Technical Architecture (โครงสร้างทางเทคนิค)
- Batch Processing Logic (กระบวนการ batch)
- Database Integration (การเชื่อมต่อฐานข้อมูล)
- Performance Characteristics (ลักษณะการทำงาน)
- Error Handling (การจัดการข้อผิดพลาด)
- Dependencies (ความพึ่งพิงระบบอื่น)
- Integration Points (จุดเชื่อมต่อ)
- Maintenance Notes (หมายเหตุการบำรุงรักษา)
- Recommendations (ข้อเสนอแนะ)

บันทึกเป็น templates/powerbuilder-module-analysis.md"
```

### **Option 2: Full Automatic Ingest**
```bash
hermes --model smart "ingest ระบบ PBProcess (PowerBuilder Batch Processing) 

🎯 OBJECTIVE: วิเคราะห์ PowerBuilder modules ในระบบ PBProcess แบบทีละ module

📂 SCOPE: /root/gcoop_hermes/mhd/PBProcess/ - PowerBuilder .pbl files และ batch processing

📋 REQUIREMENTS: สร้าง template ใหม่, Module-by-module analysis, Batch processing focus, Performance analysis, Integration points, Thai language

เริ่มด้วยการสร้าง template แล้วทำ analysis ทีละ module"
```

---

## 🎯 **PowerBuilder-Specific Analysis Points**

### **1. Module Structure Analysis**
- PBL contents และ object inventory
- DataWindow objects และ data access patterns
- User objects และ custom functions
- Window objects และ user interfaces (if any)

### **2. Batch Processing Logic**
- Job definitions และ execution sequences
- Data processing algorithms
- Business rule implementations
- Calculation methods และ formulas

### **3. Database Integration**
- Oracle database connections
- Stored procedure calls
- Transaction management
- Data synchronization logic

### **4. Performance Considerations**
- Memory usage patterns
- Database query optimization
- Batch size configurations
- Processing time estimations

### **5. Error Handling & Recovery**
- Exception handling mechanisms
- Rollback procedures
- Logging implementations
- Alert systems

---

## 📝 **Post-Session Review**

### **After Completion, Run:**
```bash
# Check template creation
hermes "แสดงเนื้อหา PowerBuilder module analysis template"

# Review module pages created
hermes "แสดงรายการ PBProcess entity pages ที่สร้างใน Session 2"

# Test cross-references
hermes "ตรวจสอบ integration points ระหว่าง PBProcess กับ MHD system"
```

### **Quality Assurance:**
```bash
# Verify technical accuracy
hermes "ตรวจสอบความถูกต้องของ PowerBuilder technical details"

# Check batch processing coverage
hermes "แสดงภาพรวม batch processing workflows ในระบบ GCOOP"
```

---

## 🔗 **Integration Notes**

### **Connection to Session 1 (MHD):**
- Reference MHD screens ที่เรียกใช้ batch processes
- Link database operations ระหว่าง web UI และ batch jobs
- Document data flow จาก user interface ไป batch processing

### **Preparation for Session 3 (Core):**
- Note infrastructure dependencies
- Identify shared utilities และ libraries  
- Document database schema requirements