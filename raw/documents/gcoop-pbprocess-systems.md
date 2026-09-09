---
source_url: /root/gcoop_hermes/mhd/GCOOP/PBProcess/ และ /root/gcoop_hermes/core/DEPT_ProcessWID/
ingested: 2026-09-10
sha256: [computed during write]
---

# GCOOP PowerBuilder Process Systems (PBProcess)

## ภาพรวม
ระบบ PowerBuilder Process ใน GCOOP ประกอบด้วยระบบ batch processing หลายตัว ที่ทำงานใน background สำหรับงานประมวลผลข้อมูลขนาดใหญ่

## 1. Main PBProcess System

### ที่อยู่
- **หลัก:** `/mhd/GCOOP/PBProcess/`
- **สำรอง:** `/core/GCOOP/PBProcess/`

### โครงสร้างไฟล์
```
PBProcess/
├── pbprocess.exe          — Executable หลัก
├── pbprocess.pbl          — PowerBuilder main library
├── pbprocess.pbd          — Compiled/Deployed version
├── pbprocess.pbw          — PowerBuilder workspace
└── Modules:
    ├── pcaccount.pbl      — โมดูลบัญชี
    ├── pccommon.pbl       — Common utilities
    ├── pcdeposit.pbl      — โมดูลเงินฝาก
    ├── pcdivavg.pbl       — เงินปันผล/เฉลี่ย
    ├── pcfinance.pbl      — การเงิน
    ├── pcinsurance.pbl    — ประกัน
    ├── pcinvertment.pbl   — การลงทุน
    ├── pckeeping.pbl      — การดูแล/รักษา
    ├── pcloan.pbl         — สินเชื่อ
    └── pcmbshr.pbl        — สมาชิก/หุ้น
```

### Command Line Usage
```bash
PBProcess.exe process_id=<PROCESS_ID> dbconnect=<CONNECTION_STRING> [ADDITIONAL_PARAMS]
```

## 2. DEPT_ProcessWID - Department Process Worker

### ที่อยู่
`/core/DEPT_ProcessWID/`

### ฟังก์ชัน
- ประมวลผลข้อมูลแผนก (Department)
- คำนวณดอกเบี้ยคงเหลือ
- สร้างรายงานสภาพคล่อง
- ปิดวันทำการ

### SQL Scripts
- `preprocess.sql` — การเตรียมข้อมูลก่อนประมวลผล
- `preprocesscloseday.sql` — เตรียมข้อมูลปิดวัน
- `preprocessgenliquidity.sql` — สร้างข้อมูลสภาพคล่อง
- `preprocessintremain.sql` — คำนวณดอกเบี้ยคงเหลือ

### Batch Scripts
- `runprocess.bat` — รันโปรเซสหลัก
- `runprocesscloseday.bat` — รันโปรเซสปิดวัน
- `runprocessgenliquidity.bat` — รันโปรเซสสภาพคล่อง
- `runprocessintremain.bat` — รันโปรเซสดอกเบี้ย

## 3. POSTTOFIN_ProcessWID - Post to Finance Worker

### ที่อยู่
`/core/POSTTOFIN_ProcessWID/`

### ฟังก์ชัน
- โพสต์ข้อมูลไปยังระบบการเงิน
- ประมวลผลรายการทางบัญชี
- อัปเดตสถานะทางการเงิน

### ไฟล์หลัก
- `POSTTOFIN.sql` — อัปเดต cmprocessing table
- `POSTTOFIN.bat` — รันโปรเซส
- `POSTTOFIN.xml` — Config XML

### SQL Logic
```sql
UPDATE cmprocessing SET 
  process_id='POSTTOFIN',
  coop_control=(SELECT coop_id FROM cmcoopmaster WHERE rownum=1),
  entry_date=sysdate-1,
  start_process=0,
  end_process=0,
  runtime_status=0,
  cmd='C:\GCOOP_ALL\CORE\GCOOP\PBProcess\PBProcess.exe process_id=POSTTOFIN ...'
WHERE process_id='POSTTOFIN';
```

## 4. Batch File Monitoring System

### ไฟล์
`/mhd/batchfile_monitoring.bat`

### ฟังก์ชัน
- ตรวจสอบไฟล์ .bat ใน `%SystemRoot%\temp\`
- รัน batch files อัตโนมัติ
- ลบไฟล์หลังใช้งานเสร็จ
- Loop ทำงานต่อเนื่องทุก 1 นาที

### Logic
```batch
:LOOP    
IF NOT EXIST %BATCH_PATH%*.bat GOTO SKIP01
  FOR /f "delims=" %%i in ('dir /b "%BATCH_PATH%*.bat"') do (
    CALL %BATCH_PATH%%%i
    DEL %BATCH_PATH%%%i /s /f /q
  )
:SKIP01
PING 1.1.1.1 -n 10 -w 1000 >NUL
GOTO LOOP
```

## Database Connection Pattern

### Connection String Format
```
Data Source={HOST}/{SID};
Persist Security Info=True;
User ID={USERNAME};
Password={PASSWORD};
Unicode=True;
Dbtype=1;
```

### ตัวอย่าง
```
Data Source=192.168.1.183/gcoop;
User ID=iscocooppea;
Password=iscocooppea;
```

## Process Management

### cmprocessing Table
ตาราง Oracle ที่เก็บข้อมูล process status:
- `process_id` — รหัส process
- `coop_control`, `coop_id` — รหัสสหกรณ์
- `start_process`, `end_process` — สถานะการทำงาน
- `runtime_status` — สถานะ runtime
- `runtime_message` — ข้อความสถานะ
- `criteria_xml`, `criteria_xml_1`, `criteria_xml_2` — Parameters XML

## Process Types

### Department Processes
- **GENPRNCBALEDAY** — สร้างยอดคงเหลือ NCB รายวัน
- **DEPT_PROCESS** — ประมวลผลข้อมูลแผนก
- **CLOSEDAY** — ปิดวันทำการ
- **GENLIQUIDITY** — สร้างรายงานสภาพคล่อง
- **INTREMAIN** — คำนวณดอกเบี้ยคงเหลือ

### Finance Processes
- **POSTTOFIN** — โพสต์ข้อมูลระบบการเงิน
- **PROCESS_DEP** — ประมวลผลเงินฝาก
- **PROCESS_SHR** — ประมวลผลหุ้น
- **PROCESS_LON** — ประมวลผลสินเชื่อ
- **PROCESS_DIV** — ประมวลผลเงินปันผล

## การใช้งาน

### Manual Execution
```bash
# รัน DEPT process
C:\GCOOP_ALL\CORE\DEPT_ProcessWID\runprocess.bat

# รัน Finance process  
C:\GCOOP_ALL\CORE\POSTTOFIN_ProcessWID\POSTTOFIN.bat

# รัน PBProcess โดยตรง
PBProcess.exe process_id=GENPRNCBALEDAY dbconnect="..." GENPRNCBALEDAY
```

### Automated Execution
- ใช้ Windows Task Scheduler
- Batch file monitoring system
- Web-based process management (ผ่าน GCOOP web)

## การตรวจสอบและ Monitoring
1. ตรวจสอบ `cmprocessing` table สำหรับสถานะ
2. ดู log files ใน process directories
3. ตรวจสอบ `runtime_message` และ `runtime_status`
4. Monitor database locks และ performance