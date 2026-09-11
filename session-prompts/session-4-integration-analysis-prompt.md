# 🔗 Session 4: Integration Analysis Ingest Prompt

## 📋 Session Overview
- **Focus:** Cross-system Workflows & Integration Points
- **Scope:** Cross-reference existing pages from Sessions 1-3
- **Template:** Integration analysis template
- **Model:** `hermes --model opus5` (complex reasoning)
- **Expected Output:** 3-5 workflow/integration pages
- **Time Estimate:** 1 hour

---

## 🎯 **MAIN PROMPT FOR SESSION 4**

```
ingest การวิเคราะห์ Integration และ Cross-system Workflows โดยใช้ข้อมูลจาก Sessions 1-3 เพื่อสร้าง comprehensive integration documentation

🎯 **OBJECTIVE:**
วิเคราะห์การเชื่อมต่อและการไหลของข้อมูลระหว่าง MHD System, PBProcess Modules, และ Core Infrastructure เพื่อสร้าง complete system integration map

📂 **SCOPE:**
- Cross-reference: MHD screens (Session 1) + PBProcess modules (Session 2) + Core components (Session 3)
- Focus: Integration workflows, data flows, และ system dependencies
- Template: สร้าง template ใหม่สำหรับ integration analysis

📋 **ANALYSIS REQUIREMENTS:**
1. **End-to-End Workflows:** แมป complete business processes ข้าม 3 systems
2. **Data Flow Analysis:** ติดตามการไหลของข้อมูลจาก UI → Batch → Database
3. **Integration Points:** ระบุและอธิบาย integration interfaces ทั้งหมด
4. **System Dependencies:** วิเคราะห์ dependencies และ coupling ระหว่างระบบ
5. **Thai Language:** ใช้ภาษาไทยในการอธิบาย business workflows

🎭 **SPECIFIC FOCUS AREAS:**
- Loan Processing Complete Workflow (ขั้นตอนสินเชื่อครบวงจร)
- Data Synchronization Processes (การซิงค์ข้อมูล)
- User Interface to Batch Job Triggers (การเรียก batch จาก UI)
- Database Transaction Coordination (การประสานงาน database transactions)
- Error Handling and Recovery Across Systems (การจัดการข้อผิดพลาดข้ามระบบ)

⚙️ **TECHNICAL REQUIREMENTS:**
- System interaction patterns และ protocols
- WCF service integration mapping
- Database transaction boundaries
- Asynchronous processing workflows
- Security context propagation

📊 **OUTPUT FORMAT:**
- Workflow pages (queries/gcoop-workflow-*.md)
- Integration concept pages (concepts/gcoop-integration-*.md)
- Template creation: templates/integration-analysis.md
- System architecture overview (concepts/gcoop-system-architecture.md)
- Cross-system dependency matrix

🎯 **SUCCESS CRITERIA:**
- End-to-end workflows ชัดเจนและครบถ้วน (3-5 major workflows)
- Integration points ระบุครบทุกจุดเชื่อมต่อ
- System dependencies วิเคราะห์ถูกต้องและละเอียด
- Business process mapping สมบูรณ์และใช้งานได้จริง

เริ่มการ ingest เลย สร้าง template ก่อนแล้วทำ integration analysis แบบ comprehensive
```

---

## 🔧 **Pre-Session Preparation**

### **1. Review Previous Session Outputs**
```bash
# Review MHD screens from Session 1
hermes "แสดงรายการ MHD screen entity pages และ integration points ที่ระบุไว้"

# Review PBProcess modules from Session 2  
hermes "แสดงรายการ PBProcess module pages และ database integration ที่พบ"

# Review Core components from Session 3
hermes "แสดงรายการ Core Infrastructure pages และ cross-system interfaces"
```

### **2. Create Integration Template First**
```bash
hermes --model opus5 "สร้าง template สำหรับ Integration Analysis โดยเน้น:
1. Workflow Overview (ภาพรวมกระบวนการ)
2. System Participants (ระบบที่เกี่ยวข้อง)
3. Data Flow Sequence (ลำดับการไหลของข้อมูล)
4. Integration Points (จุดเชื่อมต่อระบบ)
5. Technical Implementation (การใช้งานเทคนิค)
6. Error Handling (การจัดการข้อผิดพลาด)
7. Performance Considerations (ข้อพิจารณาด้านประสิทธิภาพ)
8. Security Aspects (ด้านความปลอดภัย)
9. Dependencies (ความพึ่งพิง)
10. Recommendations (ข้อเสนอแนะ)

บันทึกเป็น templates/integration-analysis.md"
```

### **3. Set Advanced Reasoning Model**
```bash
# Use opus5 model for complex system reasoning
hermes --model opus5 [your prompt]
```

---

## 📊 **Expected Outputs**

### **Template Creation:**
- `templates/integration-analysis.md` - Integration analysis template

### **Workflow Pages (3-5 major workflows):**
- `queries/gcoop-workflow-loan-complete.md` - สินเชื่อครบวงจร (UI→Batch→DB)
- `queries/gcoop-workflow-data-sync.md` - ซิงค์ข้อมูลระหว่างระบบ
- `queries/gcoop-workflow-batch-scheduling.md` - การจัดการ batch jobs
- `queries/gcoop-workflow-reporting.md` - การสร้างรายงาน end-to-end
- `queries/gcoop-workflow-error-recovery.md` - การจัดการข้อผิดพลาดข้ามระบบ

### **Integration Concept Pages (3-4 pages):**
- `concepts/gcoop-integration-architecture.md` - สถาปัตยกรรมการเชื่อมต่อ
- `concepts/gcoop-system-dependencies.md` - ความพึ่งพิงระหว่างระบบ
- `concepts/gcoop-data-flow-patterns.md` - รูปแบบการไหลของข้อมูล
- `concepts/gcoop-cross-system-security.md` - ความปลอดภัยข้ามระบบ

### **System Architecture Overview:**
- `concepts/gcoop-system-architecture.md` - ภาพรวมสถาปัตยกรรมทั้งระบบ

---

## ⚡ **Quick Start Commands**

### **Option 1: Template Creation First**
```bash
hermes --model opus5 "สร้าง Integration Analysis Template

📋 TEMPLATE REQUIREMENTS:
- Workflow Overview (ภาพรวมและวัตถุประสงค์)
- System Participants (ระบบที่เข้าร่วมในกระบวนการ)
- Data Flow Sequence (ขั้นตอนการไหลของข้อมูล)
- Integration Points (จุดเชื่อมต่อและ interfaces)
- Technical Implementation (รายละเอียดการใช้งาน)
- Error Handling (กลไกจัดการข้อผิดพลาด)
- Performance (ข้อพิจารณาประสิทธิภาพ)
- Security (ความปลอดภัยข้ามระบบ)
- Dependencies (แผนผังความพึ่งพิง)
- Recommendations (ข้อเสนะแนะปรับปรุง)

บันทึกเป็น templates/integration-analysis.md"
```

### **Option 2: Full Comprehensive Analysis**
```bash
hermes --model opus5 "ingest การวิเคราะห์ Integration และ Cross-system Workflows

🎯 OBJECTIVE: วิเคราะห์การเชื่อมต่อระหว่าง MHD, PBProcess, และ Core Infrastructure

📂 SCOPE: Cross-reference จาก Sessions 1-3, Integration workflows, Data flows, System dependencies

📋 REQUIREMENTS: สร้าง template ใหม่, End-to-end workflows, Data flow analysis, Integration points, System dependencies, Thai language

เริ่มด้วยการสร้าง template แล้วทำ comprehensive integration analysis"
```

---

## 🎯 **Integration-Specific Analysis Points**

### **1. End-to-End Loan Processing Workflow**
- หน้าจอขอสินเชื่อ (MHD) → Validation → Database Update
- Batch processing triggers → Interest calculation → Payment schedules
- Core utilities involvement → Logging → Monitoring
- Integration với Oracle PL/SQL packages

### **2. Data Synchronization Patterns**
- Real-time vs batch synchronization
- Conflict resolution strategies
- Data consistency mechanisms
- Transaction coordination across systems

### **3. System Integration Interfaces**
- WCF service boundaries
- Database stored procedure calls
- File-based data exchanges
- Message queue patterns (if any)

### **4. Cross-System Error Handling**
- Error propagation mechanisms
- Rollback strategies across systems
- Logging และ audit trails
- User notification patterns

### **5. Performance and Scalability**
- Bottleneck identification
- Load balancing strategies
- Caching mechanisms
- Resource utilization patterns

---

## 🔄 **Integration Workflow Examples**

### **Loan Application Complete Flow:**
```
1. User submits loan application (MHD Screen)
2. Real-time validation (Core utilities)
3. Database update (Oracle via WCF)
4. Batch job trigger (PBProcess scheduling)
5. Interest calculation (PowerBuilder batch)
6. Report generation (Integration of all systems)
7. User notification (Back to MHD)
```

### **Daily Batch Processing Flow:**
```
1. Scheduled trigger (PBProcess scheduler)
2. Data extraction (Core database layer)
3. Business logic processing (PowerBuilder modules)
4. Results update (Database transactions)
5. Report generation (Cross-system coordination)
6. Status updates (MHD dashboard)
```

---

## 📝 **Post-Session Review**

### **After Completion, Run:**
```bash
# Check integration template
hermes "แสดงเนื้อหา Integration Analysis template"

# Review workflow documentation
hermes "แสดงรายการ workflow pages ที่สร้างใน Session 4"

# Test complete system understanding
hermes "อธิบายกระบวนการสินเชื่อครบวงจรจาก UI ถึง batch processing"
```

### **Quality Assurance:**
```bash
# Verify workflow completeness
hermes "ตรวจสอบความครบถ้วนของ end-to-end workflows"

# Check integration accuracy
hermes "แสดง system dependency matrix และ integration points"
```

---

## 🎯 **Final System Understanding**

### **Complete GCOOP Architecture Map:**
- **Layer 1:** MHD Web Interface (User interaction)
- **Layer 2:** Core Infrastructure (Shared services)
- **Layer 3:** PBProcess Batch (Background processing)
- **Layer 4:** Oracle Database (Data persistence)

### **Key Integration Patterns:**
- **Synchronous:** MHD → Core → Database
- **Asynchronous:** MHD → Batch triggers → PBProcess
- **Event-driven:** Database changes → Notifications → UI updates

---

## 🏁 **Session 4 Completion Checklist**

### **Deliverables:**
- [ ] Integration analysis template created
- [ ] 3-5 major workflow pages documented
- [ ] System architecture overview completed
- [ ] Cross-system dependency matrix established
- [ ] Integration points fully mapped

### **Quality Gates:**
- [ ] End-to-end workflows are complete and actionable
- [ ] Technical implementation details are accurate
- [ ] Business processes are clearly explained in Thai
- [ ] All systems from Sessions 1-3 are properly integrated
- [ ] Performance and security considerations are addressed

### **Final Verification:**
```bash
hermes "สร้างสรุปโครงการ GCOOP System-by-System Ingest ทั้ง 4 Sessions พร้อม wiki statistics และ key achievements"
```