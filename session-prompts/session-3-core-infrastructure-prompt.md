# 🏗️ Session 3: Core Infrastructure Ingest Prompt

## 📋 Session Overview
- **Focus:** C# Tools & Utilities - Infrastructure Components
- **Scope:** /root/gcoop_hermes/core/
- **Template:** Create new infrastructure component template
- **Model:** `hermes --model claude` (balanced approach)
- **Expected Output:** 5-8 component pages
- **Time Estimate:** 1 hour

---

## 🎯 **MAIN PROMPT FOR SESSION 3**

```
ingest ระบบ Core Infrastructure (C# Tools & Utilities) โดยสร้าง template ใหม่และทำ component-by-component analysis

🎯 **OBJECTIVE:**
วิเคราะห์ C# infrastructure components ในระบบ GCOOP Core เพื่อสร้าง technical infrastructure documentation

📂 **SCOPE:**
- Directory: /root/gcoop_hermes/core/
- Focus: C# utilities, tools, libraries, และ infrastructure components
- Template: สร้าง template ใหม่สำหรับ infrastructure component analysis

📋 **ANALYSIS REQUIREMENTS:**
1. **Component-by-Component Analysis:** แยกวิเคราะห์แต่ละ component อย่างละเอียด
2. **Infrastructure Focus:** เน้น system utilities และ support tools
3. **Architecture Analysis:** ประเมิน technical architecture และ design patterns
4. **Integration Points:** ระบุการเชื่อมต่อกับ MHD และ PBProcess
5. **Thai Language:** ใช้ภาษาไทยในการอธิบาย technical concepts

🎭 **SPECIFIC FOCUS AREAS:**
- System Utilities และ Helper Libraries
- Database Access Layers และ Data Access Objects
- Configuration Management และ Settings
- Logging และ Monitoring Tools
- Security Components และ Authentication

⚙️ **TECHNICAL REQUIREMENTS:**
- C# class library analysis
- .NET Framework compatibility
- Assembly dependencies และ references
- Configuration patterns และ best practices
- Performance และ scalability considerations

📊 **OUTPUT FORMAT:**
- Entity pages สำหรับแต่ละ component (entities/gcoop-core-*.md)
- Concept pages สำหรับ shared patterns (concepts/gcoop-infrastructure-*.md)
- Template creation: templates/infrastructure-component-analysis.md
- Cross-references กับ MHD และ PBProcess systems

🎯 **SUCCESS CRITERIA:**
- ครอบคลุม Core components ทั้งหมด (5-8 components)
- Technical architecture ชัดเจนและใช้งานได้จริง
- Infrastructure patterns เพียงพอสำหรับ maintenance
- Cross-references ถูกต้องและครบถ้วน

เริ่มการ ingest เลย สร้าง template ก่อนแล้วทำทีละ component
```

---

## 🔧 **Pre-Session Preparation**

### **1. Survey Core Infrastructure Structure**
```bash
# Get overview of Core components
find /root/gcoop_hermes/core/ -name "*.csproj" -o -name "*.sln" | sort

# Check for C# source files
find /root/gcoop_hermes/core/ -name "*.cs" | head -20

# Look for configuration files
find /root/gcoop_hermes/core/ -name "*.config" -o -name "*.xml" -o -name "*.json"

# Check for documentation
find /root/gcoop_hermes/core/ -name "*.txt" -o -name "*.md" -o -name "*.doc"
```

### **2. Create Infrastructure Template First**
```bash
hermes --model claude "สร้าง template สำหรับ Infrastructure Component analysis โดยเน้น:
1. Component Overview และ Purpose
2. Technical Architecture
3. API และ Interface Design
4. Dependencies และ References
5. Configuration Management
6. Performance Characteristics
7. Security Considerations
8. Integration Points
9. Maintenance Guidelines
10. Usage Examples

สร้างเป็น templates/infrastructure-component-analysis.md"
```

### **3. Set Balanced Model**
```bash
# Use claude model for balanced technical analysis
hermes --model claude [your prompt]
```

---

## 📊 **Expected Outputs**

### **Template Creation:**
- `templates/infrastructure-component-analysis.md` - Infrastructure component analysis template

### **Entity Pages (5-8 components):**
- `entities/gcoop-core-database-layer.md` - Database access layer
- `entities/gcoop-core-logging-system.md` - Logging และ monitoring
- `entities/gcoop-core-config-manager.md` - Configuration management
- `entities/gcoop-core-security-lib.md` - Security libraries
- `entities/gcoop-core-utilities.md` - Common utilities
- `entities/gcoop-core-data-export.md` - Data export tools
- `entities/gcoop-core-smartcard-sim.md` - Smart card simulator
- `entities/gcoop-core-oracle-tools.md` - Oracle management tools

### **Concept Pages (3-4 pages):**
- `concepts/gcoop-infrastructure-architecture.md` - โครงสร้าง infrastructure
- `concepts/gcoop-dotnet-patterns.md` - .NET design patterns
- `concepts/gcoop-configuration-strategy.md` - กลยุทธ์การจัดการ config
- `concepts/gcoop-cross-system-integration.md` - การเชื่อมต่อข้ามระบบ

---

## ⚡ **Quick Start Commands**

### **Option 1: Template Creation First**
```bash
hermes --model claude "สร้าง Infrastructure Component Analysis Template

📋 TEMPLATE REQUIREMENTS:
- Component Overview (ภาพรวมและวัตถุประสงค์)
- Technical Architecture (สถาปัตยกรรมทางเทคนิค)
- API Design (การออกแบบ interface)
- Dependencies (ความพึ่งพิงระบบอื่น)
- Configuration (การจัดการ configuration)
- Performance (ลักษณะการทำงาน)
- Security (ความปลอดภัย)
- Integration Points (จุดเชื่อมต่อ)
- Maintenance (การบำรุงรักษา)
- Usage Guidelines (แนวทางการใช้งาน)

บันทึกเป็น templates/infrastructure-component-analysis.md"
```

### **Option 2: Full Automatic Ingest**
```bash
hermes --model claude "ingest ระบบ Core Infrastructure (C# Tools & Utilities)

🎯 OBJECTIVE: วิเคราะห์ C# infrastructure components ในระบบ GCOOP Core

📂 SCOPE: /root/gcoop_hermes/core/ - C# utilities, libraries, และ infrastructure

📋 REQUIREMENTS: สร้าง template ใหม่, Component-by-component analysis, Infrastructure focus, Architecture analysis, Integration points, Thai language

เริ่มด้วยการสร้าง template แล้วทำ analysis ทีละ component"
```

---

## 🎯 **Infrastructure-Specific Analysis Points**

### **1. Component Architecture Analysis**
- Class library structure และ namespaces
- Design patterns implementation (Factory, Repository, etc.)
- Interface definitions และ contracts
- Dependency injection patterns

### **2. Technical Implementation**
- .NET Framework version compatibility
- Assembly loading และ versioning
- Resource management และ disposal
- Thread safety considerations

### **3. Configuration Management**
- Configuration sources (app.config, database, files)
- Environment-specific settings
- Dynamic configuration updates
- Configuration validation

### **4. Integration Capabilities**
- API endpoints และ service contracts
- Database connection management
- External system interfaces
- Message passing mechanisms

### **5. Operational Aspects**
- Logging strategies และ levels
- Performance monitoring
- Error handling และ diagnostics
- Deployment considerations

---

## 🔗 **Integration Mapping**

### **Connection to MHD System:**
- Identify which utilities are used by MHD screens
- Map configuration dependencies
- Document shared libraries และ references
- Note WCF service integrations

### **Connection to PBProcess System:**
- Identify PowerBuilder interop components
- Map shared database access layers
- Document data exchange mechanisms
- Note batch processing utilities

---

## 📝 **Post-Session Review**

### **After Completion, Run:**
```bash
# Check template creation
hermes "แสดงเนื้อหา Infrastructure Component analysis template"

# Review component pages created
hermes "แสดงรายการ Core Infrastructure entity pages ที่สร้างใน Session 3"

# Test integration mapping
hermes "แสดงการเชื่อมต่อระหว่าง Core Infrastructure กับ MHD และ PBProcess"
```

### **Quality Assurance:**
```bash
# Verify technical accuracy
hermes "ตรวจสอบความถูกต้องของ C# infrastructure technical details"

# Check architecture documentation
hermes "แสดงภาพรวม infrastructure architecture patterns ในระบบ GCOOP"
```

---

## 🎯 **Special Focus Areas**

### **Smart Card Integration:**
- Smart card simulator analysis
- Card reader interface components
- Security protocols และ encryption

### **Oracle Integration:**
- Oracle Data Pump Studio analysis
- Database management utilities
- Connection pooling และ optimization

### **Cross-System Communication:**
- WCF service infrastructure
- Data transfer mechanisms
- Synchronization utilities

---

## 🚀 **Preparation for Session 4**

### **Document Integration Points:**
- List all cross-system interfaces
- Note shared components usage
- Identify workflow dependencies
- Prepare integration mapping for Session 4

### **Architecture Summary:**
- Create high-level component diagram
- Document major design patterns
- Note critical dependencies
- Identify potential improvement areas