---
title: GCOOP PowerBuilder Process Systems
created: 2026-09-10
updated: 2026-09-10
type: entity
tags: [powerbuilder, batch, workflow, oracle, finance, department]
sources: [raw/documents/gcoop-pbprocess-systems.md]
confidence: high
---

# GCOOP PowerBuilder Process Systems

## ภาพรวม
ระบบ PowerBuilder Process (PBProcess) เป็น backbone สำหรับการประมวลผล batch jobs ในระบบ GCOOP ทำงานใน background เพื่อจัดการงานประมวลผลข้อมูลขนาดใหญ่

## โครงสร้างระบบ

### 1. Main PBProcess Engine
- **Executable:** `pbprocess.exe`
- **Location:** `/mhd/GCOOP/PBProcess/`
- **Function:** Core engine สำหรับรัน batch processes

#### PowerBuilder Modules
- **pcaccount.pbl** — โมดูลบัญชี
- **pccommon.pbl** — Common utilities และ shared functions
- **pcdeposit.pbl** — ประมวลผลเงินฝาก
- **pcdivavg.pbl** — คำนวณเงินปันผลและค่าเฉลี่ย
- **pcfinance.pbl** — ระบบการเงินและบัญชี
- **pcinsurance.pbl** — ประมวลผลประกัน
- **pcinvertment.pbl** — การลงทุน
- **pckeeping.pbl** — การดูแลรักษาข้อมูล
- **pcloan.pbl** — ประมวลผลสินเชื่อ
- **pcmbshr.pbl** — สมาชิกและหุ้น

### 2. Department Process Worker
- **Location:** `/core/DEPT_ProcessWID/`
- **Function:** ประมวลผลข้อมูลแผนกและงานปิดวัน
- **Key Processes:** GENPRNCBALEDAY, ปิดวัน, สภาพคล่อง

### 3. Finance Process Worker  
- **Location:** `/core/POSTTOFIN_ProcessWID/`
- **Function:** โพสต์ข้อมูลไปยังระบบการเงิน
- **Key Process:** POSTTOFIN

## Process Types และการทำงาน

### Finance Processes
1. **PROCESS_DEP** — ประมวลผลเงินฝาก
2. **PROCESS_SHR** — ประมวลผลหุ้น  
3. **PROCESS_LON** — ประมวลผลสินเชื่อ
4. **PROCESS_DIV** — ประมวลผลเงินปันผล
5. **POSTTOFIN** — โพสต์รายการไประบบการเงิน

### Department Processes
1. **GENPRNCBALEDAY** — สร้างยอดคงเหลือ NCB รายวัน
2. **CLOSEDAY** — ปิดวันทำการ
3. **GENLIQUIDITY** — สร้างรายงานสภาพคล่อง
4. **INTREMAIN** — คำนวณดอกเบี้ยคงเหลือ

## การจัดการ Process

### Process Control Table
**Table:** `cmprocessing` (Oracle)
- ควบคุมสถานะการทำงานของทุก process
- เก็บ parameters เป็น XML format
- Track runtime status และ error messages

### Command Pattern
```bash
PBProcess.exe process_id=<ID> dbconnect=<CONNECTION> [PARAMS]
```

### Batch Automation
- **Manual:** รัน .bat files โดยตรง
- **Scheduled:** Windows Task Scheduler
- **Monitoring:** Batch file monitoring system (loop ทุก 1 นาที)

## เชื่อมต่อกับระบบอื่น
- **[[gcoop-mhd]]** — Web application หลัก (trigger processes)
- **[[gcoop-database-tools]]** — Oracle database backend
- **[[gcoop-interest-calculation]]** — คำนวณดอกเบี้ยใน batch mode
- **[[gcoop-loan-system]]** — ประมวลผลสินเชื่อ batch
- **Oracle Database** — เก็บข้อมูลและ configuration

## การ Monitoring และ Maintenance

### Status Monitoring
1. ตรวจสอบ `cmprocessing.runtime_status`
2. อ่าน `runtime_message` สำหรับ error details
3. Monitor database locks และ performance
4. ตรวจสอบ log files ใน process directories

### Troubleshooting
- ตรวจสอบ Oracle connection strings
- ดู process parameters ใน XML format
- Monitor disk space และ memory usage
- ตรวจสอบ file permissions และ access rights

## Technical Architecture
- **Platform:** Windows (32-bit x86 compatible)
- **Database:** Oracle with Unicode support
- **Connection:** OLE DB/ODBC connection strings
- **Deployment:** Compiled PowerBuilder (.pbd) files
- **Automation:** Batch scripts + Windows scheduling

## Performance Considerations
- Large data processing ใน off-peak hours
- Database connection pooling
- Parallel processing capabilities
- Resource monitoring และ throttling
- Transaction management สำหรับ data consistency