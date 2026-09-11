# 🎯 GCOOP System-by-System Ingest: Complete Session Guide

## 📋 Overview
This guide contains 4 comprehensive session prompts for analyzing the GCOOP cooperative system using a system-by-system approach. Each session focuses on a specific technology stack and creates specialized documentation.

---

## 📅 **Session Sequence Summary**

| Session | System | Technology | Model | Time | Output |
|---------|--------|------------|-------|------|---------|
| **Session 1** | MHD Main System | ASP.NET Web Forms | `fable51` | 2-3h | 10-15 screen pages |
| **Session 2** | PBProcess Modules | PowerBuilder Batch | `smart` | 1-2h | 11 module pages |
| **Session 3** | Core Infrastructure | C# Tools & Utilities | `claude` | 1h | 5-8 component pages |
| **Session 4** | Integration Analysis | Cross-system Workflows | `opus5` | 1h | 3-5 workflow pages |

**Total: 5-7 hours, 29-42 high-quality wiki pages**

---

## 🎯 **Session 1: MHD Main System**
### **Quick Start:**
```bash
hermes --model fable51 "ingest ระบบ MHD หลัก (ASP.NET Web Applications) โดยใช้ template-driven analysis

🎯 OBJECTIVE: วิเคราะห์หน้าจอ ASP.NET ในระบบ MHD GCOOP แบบทีละหน้าจอตาม template

📂 SCOPE: /root/gcoop_hermes/mhd/GCOOP/ - ASP.NET Web Forms และ Code-behind

📋 REQUIREMENTS: Screen-by-screen analysis, 10-section structure ตาม template, Business logic focus, Integration points, Thai language

เริ่มการ ingest เลย ทำทีละหน้าจอตาม template วิเคราะห์ทีละหน้าจอ_mhd_loan.md"
```

### **Focus Areas:**
- หน้าจอ Loan Management (สินเชื่อ)
- User Interface Components และ Controls
- Data Validation Rules และ Business Rules
- Database Interactions และ WCF Service calls
- User Experience Flow และ Navigation

---

## 🔄 **Session 2: PBProcess Modules**
### **Quick Start:**
```bash
hermes --model smart "ingest ระบบ PBProcess (PowerBuilder Batch Processing) 

🎯 OBJECTIVE: วิเคราะห์ PowerBuilder modules ในระบบ PBProcess แบบทีละ module

📂 SCOPE: /root/gcoop_hermes/mhd/PBProcess/ - PowerBuilder .pbl files และ batch processing

📋 REQUIREMENTS: สร้าง template ใหม่, Module-by-module analysis, Batch processing focus, Performance analysis, Integration points, Thai language

เริ่มด้วยการสร้าง template แล้วทำ analysis ทีละ module"
```

### **Focus Areas:**
- Batch Job Definitions และ Scheduling
- Data Processing Logic และ Algorithms
- Database Operations (Oracle PL/SQL integration)
- Error Handling และ Recovery Mechanisms
- Performance Optimization และ Resource Management

---

## 🏗️ **Session 3: Core Infrastructure**
### **Quick Start:**
```bash
hermes --model claude "ingest ระบบ Core Infrastructure (C# Tools & Utilities)

🎯 OBJECTIVE: วิเคราะห์ C# infrastructure components ในระบบ GCOOP Core

📂 SCOPE: /root/gcoop_hermes/core/ - C# utilities, libraries, และ infrastructure

📋 REQUIREMENTS: สร้าง template ใหม่, Component-by-component analysis, Infrastructure focus, Architecture analysis, Integration points, Thai language

เริ่มด้วยการสร้าง template แล้วทำ analysis ทีละ component"
```

### **Focus Areas:**
- System Utilities และ Helper Libraries
- Database Access Layers และ Data Access Objects
- Configuration Management และ Settings
- Logging และ Monitoring Tools
- Security Components และ Authentication

---

## 🔗 **Session 4: Integration Analysis**
### **Quick Start:**
```bash
hermes --model opus5 "ingest การวิเคราะห์ Integration และ Cross-system Workflows

🎯 OBJECTIVE: วิเคราะห์การเชื่อมต่อระหว่าง MHD, PBProcess, และ Core Infrastructure

📂 SCOPE: Cross-reference จาก Sessions 1-3, Integration workflows, Data flows, System dependencies

📋 REQUIREMENTS: สร้าง template ใหม่, End-to-end workflows, Data flow analysis, Integration points, System dependencies, Thai language

เริ่มด้วยการสร้าง template แล้วทำ comprehensive integration analysis"
```

### **Focus Areas:**
- Loan Processing Complete Workflow (ขั้นตอนสินเชื่อครบวงจร)
- Data Synchronization Processes (การซิงค์ข้อมูล)
- User Interface to Batch Job Triggers (การเรียก batch จาก UI)
- Database Transaction Coordination (การประสานงาน database transactions)
- Error Handling and Recovery Across Systems (การจัดการข้อผิดพลาดข้ามระบบ)

---

## 🎯 **Model Selection Strategy**

### **Why Different Models for Each Session:**

**Session 1 - `fable51` (Advanced):**
- Screen analysis requires detailed reasoning
- Template-driven approach needs precision
- Business logic complexity demands advanced capabilities

**Session 2 - `smart` (Advanced):**
- Technical PowerBuilder analysis
- Performance considerations need deep understanding
- Batch processing complexity requires advanced reasoning

**Session 3 - `claude` (Balanced):**
- Infrastructure components are well-documented
- Balanced approach suitable for utilities analysis
- Good cost/performance ratio for documentation

**Session 4 - `opus5` (Highest):**
- Cross-system integration is most complex
- Requires highest reasoning for workflow analysis
- Critical for complete system understanding

---

## 📊 **Expected Complete Output Structure**

```
mhd/wiki/
├── entities/
│   ├── gcoop-screen-ws-lon-*.md (10-15 MHD screens)
│   ├── gcoop-pbprocess-*.md (11 PB modules)
│   └── gcoop-core-*.md (5-8 core components)
├── concepts/
│   ├── gcoop-mhd-*.md (MHD concepts)
│   ├── gcoop-batch-*.md (Batch concepts)
│   ├── gcoop-infrastructure-*.md (Infrastructure concepts)
│   └── gcoop-integration-*.md (Integration concepts)
├── queries/
│   └── gcoop-workflow-*.md (3-5 workflows)
├── templates/
│   ├── powerbuilder-module-analysis.md
│   ├── infrastructure-component-analysis.md
│   └── integration-analysis.md
└── concepts/
    └── gcoop-system-architecture.md (Complete overview)
```

---

## ⚡ **Quick Session Execution**

### **For Immediate Start:**
Copy and paste the "Quick Start" command for any session. Each prompt is self-contained and includes all necessary instructions.

### **For Guided Approach:**
1. Read the detailed prompt file for the session
2. Review the preparation steps
3. Execute the main prompt
4. Follow the post-session review checklist

---

## 🎯 **Success Metrics**

### **Quantitative Goals:**
- **29-42 total wiki pages** created
- **4 new templates** for different system types
- **100% coverage** of major system components
- **Complete integration mapping** across all systems

### **Qualitative Goals:**
- **Business logic in Thai** for stakeholder understanding
- **Technical accuracy** for developer maintenance
- **Cross-references** for knowledge navigation
- **Template-driven consistency** across all documentation

---

## 💡 **Pro Tips**

### **Before Starting:**
- Ensure template files are accessible
- Check directory structures exist
- Verify model availability
- Set aside dedicated time blocks

### **During Sessions:**
- Follow the recommended model for each session
- Don't skip template creation steps
- Focus on integration points throughout
- Use Thai language for business explanations

### **After Each Session:**
- Run the post-session review commands
- Verify wiki growth and quality
- Test cross-references
- Document any issues for next session

---

## 🏁 **Final Verification Command**

After completing all 4 sessions:

```bash
hermes "สร้างสรุปโครงการ GCOOP System-by-System Ingest ทั้ง 4 Sessions:

1. แสดง wiki statistics (จำนวนหน้า, templates, concepts)
2. ทดสอบ cross-system queries
3. แสดงรายการ integration workflows ที่สมบูรณ์
4. ประเมินคุณภาพและความครอบคลุมของ documentation
5. ให้ข้อเสนะแนะสำหรับการใช้งานและบำรุงรักษา wiki"
```

**พร้อมเริ่มต้น Session 1 แล้วครับ!** 🚀