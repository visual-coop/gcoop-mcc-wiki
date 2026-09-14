# MCC Session 3: Infrastructure Analysis
# การวิเคราะห์โครงสร้างพื้นฐานและระบบสนับสนุน GCOOP MCC

**วันที่วิเคราะห์:** 2026-09-14  
**ขอบเขต:** /root/gcoop_hermes/mcc/ — เครื่องมือสนับสนุนและโครงสร้างพื้นฐาน  
**ผู้วิเคราะห์:** Hermes Agent (MCC Session 3)

---

## 1. winUPBOOK — เครื่องมืออัพเดทสมุดบัญชี (Passbook Update Tool)

### ภาพรวม
`winUPBOOK` เป็น Windows Application พัฒนาด้วย **Visual Basic .NET (VB.NET)** เพื่ออัพเดทสมุดบัญชีเงินฝากผ่านเครื่องพิมพ์สมุดบัญชี (Passbook Printer) โดยเชื่อมต่อกับฐานข้อมูล Oracle โดยตรง

### โครงสร้าง Solution
```
winUPBOOK/
├── winUPBOOK.sln              # Visual Studio Solution
├── conn.dat                    # ไฟล์เก็บข้อมูล Connection (binary)
├── PEM.ocx / AxInterop.PEMLib.dll / Interop.PEMLib.dll  # OCX สำหรับ Passbook Printer
├── Oracle.DataAccess.dll       # ODP.NET Oracle Data Provider
├── ODP.NET/                    # Oracle Data Provider packages (2.x, 4)
├── ODP/                        # Oracle Data Provider เก่า
├── OraProvCfg.exe              # Oracle Provider Config utility
└── winUPBOOK/                  # Source code project
    ├── App.config              # .NET Framework 4.0 Client Profile
    ├── Base.vb                 # Base class สำหรับ connection/data
    ├── Connection.vb           # จัดการ Oracle connection
    ├── Data.vb                 # Data access layer
    ├── Form1.vb / Form2.vb     # หน้าจอหลัก
    ├── UpdateBook.vb           # Logic อัพเดทสมุดบัญชี (หัวใจหลัก)
    ├── ProgressDialog.vb       # แสดง progress ระหว่างอัพเดท
    ├── SplashScreen1.vb        # หน้าจอ loading
    ├── Timer.vb                # ควบคุม timing
    ├── frmThread1.vb / frmThread2.vb  # Multi-threading form
    ├── test.vb                 # Form สำหรับทดสอบ
    └── images/                 # รูปภาพ UI รวมถึง Passbook-AERO-2023-11-27 154249.png
```

### ไฟล์สำคัญ: UpdateBook.vb
- สืบทอดจาก `Base` class
- ฟังก์ชัน `GetRule_BookPage()` ดึงกฎการพิมพ์สมุดจากตาราง `DPDEPTCONSTANT`:
  - `Pagetotal_Pb` — จำนวนหน้าทั้งหมด
  - `Lineperpage_Pb` — จำนวนบรรทัดต่อหน้า
  - `Linenomidpage_Pb` — บรรทัดกลางหน้า
  - `Nlinenextmid_Pb` — บรรทัดถัดไปจากกลาง
- ใช้ `Oracle.DataAccess.Client` (ODP.NET) เชื่อมต่อ Oracle

### การ Deploy (ClickOnce)
ระบบใช้ **ClickOnce deployment** ผ่าน IIS:
- Publish folder: `pbUpbook/` และ `pbUpbook/Application Files/`
- รองรับ version history: `winUPBOOK_1_0_0_5` → `winUPBOOK_1_0_0_9` (5 versions)
- ล่าสุด: version 1.0.0.9 (มีรูป Passbook-AERO-2023-11-27)
- Target Platform: **x86 (32-bit)**

### Library พิเศษ: PEM.ocx
- `PEM.ocx` — ActiveX OCX สำหรับควบคุม Passbook Printer (PEMS hardware)
- `AxInterop.PEMLib.dll` — .NET Interop wrapper สำหรับ PEM.ocx
- `Interop.PEMLib.dll` — COM Interop library

---

## 2. winLKE — เครื่องอ่านบัตรแถบแม่เหล็ก (Magnetic Card Reader)

### ภาพรวม
`winLKE` เป็น Windows Application พัฒนาด้วย **C# .NET** เพื่ออ่านข้อมูลจากบัตรแถบแม่เหล็ก (Magnetic Stripe Card) สำหรับระบบสมุดสมาชิก

### โครงสร้าง
```
winLKE/
├── Driver LKE.rar             # Driver ต้นฉบับ (compressed)
├── Drivers/
│   ├── MSRE.zip               # Driver สำหรับ MSRE device
│   └── winLKEAppDevices.zip   # Application device driver
└── winMagneticBook/
    ├── winMagneticBook.sln    # Visual Studio Solution
    ├── conn.dat               # Connection data
    ├── Msre.ocx               # OCX สำหรับ Magnetic Reader (MSRE device)
    ├── Oracle.DataAccess.dll  # ODP.NET
    └── winMagneticBook/
        ├── App.config         # .NET Framework 4.6.1
        ├── Form1.cs           # หน้าจอหลัก (C# code)
        ├── Program.cs         # Entry point
        ├── Msre.ocx           # MSRE OCX copy
        └── winLKE.exe         # Executable ที่ build แล้ว
```

### ฟีเจอร์ Form1.cs
- ใช้ Win32 API ผ่าน P/Invoke: `ShowWindow`, `MoveWindow`, `SetWindowPos`, `FindWindow`, `SetForegroundWindow`
- บริหารจัดการ Process ของ `winLKE.exe` ที่อยู่ที่ `C:\GCOOP_ALL\AERO\winLKE\Drivers\winLKEAppDevices\winLKE.exe`
- Logic: หาก process กำลังทำงาน → ดึงมาที่หน้า, ไม่ทำงาน → start process ใหม่
- ใช้ `Oracle.DataAccess.Client` เชื่อมต่อ Oracle

### Hardware รองรับ
- **MSRE device** — Magnetic Stripe Reader Encoder
- DriverLKE: สำหรับ LKE hardware (Magnetic card reader ที่สหกรณ์ใช้)

### การ Deploy (ClickOnce)
- Version ที่ publish: `winMagneticBook_1_0_0_12` (version 1.0.0.12)
- Target: .NET Framework 4.6.1

---

## 3. CONVERT_MCC — เครื่องมือ Data Migration

### ภาพรวม
`CONVERT_MCC` เก็บ script และ tool สำหรับย้ายข้อมูลเข้าสู่ระบบ MCC จากระบบเดิม ประกอบด้วย SQL scripts และ PowerBuilder pipeline programs

### โครงสร้างไฟล์
```
CONVERT_MCC/
├── SQL Scripts (การ migrate ข้อมูล):
│   ├── Check shrlon bal.sql              # ตรวจสอบยอดคงเหลือ หุ้น/กู้
│   ├── Delete Data.txt                   # คำสั่งลบข้อมูลก่อน migrate
│   ├── Delete Table.sql                  # ลบตาราง
│   ├── SQL Update หลัง CNV MEM.txt      # Update ข้อมูลสมาชิกหลัง convert
│   ├── SQL Update หลัง CNV.txt          # Update ทั่วไปหลัง convert
│   ├── SQL Update หลัง CNV_INS.txt      # Update ประกันหลัง convert
│   ├── SQL update lncontmaster.txt       # Update ตาราง lncontmaster (สัญญากู้)
│   ├── SQL_FIN.txt                       # SQL Final step
│   ├── SQL_update_dept.txt               # Update ตารางเงินฝาก
│   ├── SQL_update_dif_keep.txt           # Update ตาราง keeping (ออมทรัพย์พิเศษ)
│   ├── alter new_code.sql                # Alter สำหรับ new code
│   ├── elcmapelection.txt                # Map ข้อมูลการเลือกตั้ง
│   ├── fomimagemaster.txt                # Map ข้อมูล Image
│   ├── lnloantype.txt                    # Map ประเภทสินเชื่อ
│   ├── mapkepitem.sql                    # Map รายการ keeping
│   ├── maplonitem.sql                    # Map รายการเงินกู้
│   ├── mapshritem.sql                    # Map รายการหุ้น
│   └── sql_update_loan_reqlaon.sql       # Update คำขอกู้
│
├── cnv_addr/                             # PowerBuilder: Convert ที่อยู่
│   ├── cnvaddr.pbt / cnvaddr.pbl         # PB target/library
│   ├── cnvaddr.pbw                       # PB workspace
│   ├── aCOMSRV/                          # COM Server libraries
│   │   ├── fmcomsv.pbl                   # Common service library
│   │   └── fmsheet.pbl                   # Sheet library
│   └── cnvaddr_mig.log                   # Log การ migrate
│
└── mcc_pipeline/                         # PowerBuilder: Pipeline migration
    ├── mcc_pipe.pbt                      # PB target
    ├── mcc_workspace.pbw                 # PB workspace
    ├── mcc_pipe_app.pbl                  # Application library
    ├── mcc_pipe_dep.pbl                  # Deposit (เงินฝาก) pipeline
    ├── mcc_pipe_divavg.pbl               # Dividend average pipeline
    ├── mcc_pipe_insurance.pbl            # Insurance (ประกัน) pipeline
    ├── mcc_pipe_keeping.pbl              # Keeping (ออมทรัพย์) pipeline
    ├── mcc_pipe_member.pbl               # Member (สมาชิก) pipeline
    └── mcc_pipe_shrlon.pbl               # Share/Loan (หุ้น/กู้) pipeline
```

### โมดูล Migration (mcc_pipeline)
| Library | โดเมนข้อมูล |
|---------|-------------|
| mcc_pipe_member.pbl | ข้อมูลสมาชิก |
| mcc_pipe_dep.pbl | บัญชีเงินฝาก |
| mcc_pipe_shrlon.pbl | หุ้น/เงินกู้ |
| mcc_pipe_keeping.pbl | เงินออมทรัพย์พิเศษ |
| mcc_pipe_insurance.pbl | ประกัน |
| mcc_pipe_divavg.pbl | เงินปันผล/เฉลี่ยคืน |

### SQL Update หลัง Convert (ตัวอย่างจากไฟล์จริง)
SQL scripts ใน `SQL Update หลัง CNV.txt` ครอบคลุม:
- Update `lastcalint_date` ของ `dpdeptprncfixed` (วันที่คำนวณดอกเบี้ยล่าสุด)
- Update ประเภทบัญชี 005, 015, 019, 002, 006, 028, 032-034, 039-043, 054-055, 068-070, 084
- Update `dpdeptmasdue` สำหรับบัญชีประเภท 005

---

## 4. 0.DB — Database Scripts และเครื่องมือฐานข้อมูล

### ภาพรวม
`0.DB` เก็บ script สำหรับดูแลรักษาฐานข้อมูล Oracle ของระบบ MCC

### โครงสร้าง
```
0.DB/
├── Alter_Oracle_Kill_Sessions_user.bat    # Script kill inactive sessions (batch)
├── Alter_Oracle_Kill_Sessions_user.xml    # XML config ของ batch
├── Alter_Oracle_Kill_Sessions_user_w3w.sql # SQL: Kill W3WP/inactive sessions
├── ENC.java                               # Java: SHA3 encryption (standalone)
├── FT_ENC.sql                             # SQL function: FT_ENC (encryption)
├── WcfCalling.bat                         # Batch run Java WCF caller
├── WcfCalling.class                       # Compiled Java class
├── WcfCalling.java                        # Java: HTTP POST caller สำหรับ Oracle stored function
├── WcfCalling.sql                         # SQL: CREATE FUNCTION WCFCALLING (Java stored proc)
├── script_insert_shstm_bf.sql             # SQL: Insert share statement (before)
│
├── Backup/
│   ├── OracleSync2BackupServer.master.bat          # Master script: สั่ง run backup + log
│   ├── OracleSync2BackupServer.script.cloud_backup.bat     # Export Oracle → backup server
│   └── OracleSync2BackupServer.script.cloud_backup_7z.bat  # Export + compress 7z
│
├── ISCOMCC/
│   ├── Alter_recreate_iscomcc_user.sql    # สร้าง/recreate user ISCOMCC
│   ├── ExpOracleDB-127.0.0.1-iscomcc.bat # Export schema ISCOMCC (expdp)
│   ├── ImpOracleDB-127.0.0.1-iscomcc.bat # Import schema ISCOMCC (impdp)
│   └── ImpOracleDB.bat                   # Import script ทั่วไป
│
├── Java.WCF/javawcf/                      # Java project: WcfCalling
│   ├── src/WcfCalling.java               # Source code
│   ├── dist/javawcf.jar                  # Compiled JAR
│   └── build/classes/WcfCalling.class    # Compiled class
│
└── Oracle.SHA3/
    ├── ENC.java                           # Java SHA3 encryption implementation
    └── FT_ENC.sql                         # PL/SQL function: encryption
```

### กลไกสำคัญ

#### Kill Oracle Sessions (Alter_Oracle_Kill_Sessions_user_w3w.sql)
Script ตรวจหา session ที่ INACTIVE นานกว่า 15 นาที จาก `gv$session` ที่ใช้โปรแกรม `w3wp.exe` (IIS Worker Process) แล้ว `ALTER SYSTEM KILL SESSION ... IMMEDIATE`  
รองรับทั้ง Oracle ปกติ และ **Amazon Oracle RDS** (ใช้ `rdsadmin.rdsadmin_util.kill`)

#### Oracle Backup Script (cloud_backup.bat)
- Oracle schema: `ISCOMCC`
- Server IP: `43.229.79.117` (cloud server)
- Port: 1521, SID: `gcoop`
- ขั้นตอน: Map network drive → Clean tmp → `expdp` export → สร้าง MD5 checksum
- รองรับ 7-zip compression (script แยก)

#### Java WcfCalling (WcfCalling.java + WcfCalling.sql)
- Java class ที่ deploy เข้า Oracle ด้วย `loadjava`
- สร้าง Oracle stored function `WCFCALLING` ที่เรียก HTTP POST จากภายใน Oracle
- ใช้สำหรับให้ Oracle stored procedure เรียก WCF Web Service ของ GCOOP

#### SHA3 Encryption (Oracle.SHA3/)
- Java implementation ของ SHA3 hash
- SQL function `FT_ENC` สำหรับ encrypt ข้อมูลภายใน Oracle

---

## 5. iReport — Report Templates

### ภาพรวม
`iReport` เก็บ template รายงานทั้งหมดในรูปแบบ **iReport 5.0.4** (JasperReports .jrxml format)

### สถิติ
| ประเภท | จำนวน |
|--------|--------|
| **รวมทั้งหมด** | **676 ไฟล์** |
| ir_ prefix (รายงานหลัก) | 560 ไฟล์ |
| r_ prefix | 102 ไฟล์ |
| ireport_ prefix | 6 ไฟล์ |
| sub_ prefix | 3 ไฟล์ |
| chq_ prefix | 3 ไฟล์ |
| dw_ prefix | 1 ไฟล์ |
| อื่นๆ | 1 ไฟล์ |

### โครงสร้างโฟลเดอร์
```
iReport/
├── Reports/         # ไฟล์ .jrxml ทั้งหมด 676 ไฟล์ (flat directory)
└── Picture/         # รูปภาพสำหรับ report (11 ไฟล์)
```

### การแบ่งประเภทรายงาน (ir_ prefix ตาม domain ที่ 2)
| Domain | จำนวน | หมวดหมู่ |
|--------|--------|---------|
| mis | 104 | MIS / รายงานภาพรวม |
| kep | 45 | ออมทรัพย์พิเศษ (Keeping) |
| coopid | 41 | ตามรหัสสหกรณ์ |
| loan | 33 | สินเชื่อ |
| report | 30 | รายงานทั่วไป |
| pm | 22 | PM (Payment/ชำระ) |
| didurt | 20 | เงินปันผล/เฉลี่ยคืน urgent |
| ass | 19 | สวัสดิการ/ผู้ช่วย (Assistance) |
| diinvt | 18 | เงินปันผล (Dividend investment) |
| inv | 16 | การลงทุน |
| hr | 16 | ทรัพยากรบุคคล |
| brw | 15 | กู้ยืม (Borrow) |
| yrconfirm | 13 | ยืนยันรายปี |
| printfin | 13 | พิมพ์เอกสารการเงิน |
| changecollno | 12 | เปลี่ยนงวดชำระ |
| reqloan | 11 | คำขอกู้ |
| อื่นๆ | ~101 | หลายหมวด |

### ตัวอย่างไฟล์
- `chq_006_krungthai_mcc.jrxml` — เช็ค KTB (กรุงไทย) format MCC
- `chq_011_tmb_a4.jrxml` / `chq_011_tmb_mcc.jrxml` — เช็ค TMB (ทหารไทย)
- `ir_accept_book.jrxml` — ใบรับสมุดบัญชี
- `ir_ass_procbudget_edu*.jrxml` — งบประมาณสวัสดิการการศึกษา
- `dw_slipopen_name.jrxml` — ใบเปิดบัญชีเงินฝาก

---

## 6. XMLConfig — ระบบการตั้งค่าผ่าน XML

### ภาพรวม
`XMLConfig` เป็น PowerBuilder application (`xmlconfig.exe`) สำหรับแก้ไขไฟล์ XML configuration ของระบบ ทำหน้าที่เป็น admin tool สำหรับผู้ดูแลระบบ

### ไฟล์ Configuration ทั้งหมด
```
XMLConfig/
├── xmlconfig.exe / xmlconfig.pbl / xmlconfig.pbd  # PowerBuilder app
├── xmlconfig.pbt / xmlconfig.pbw                  # PB target/workspace
├── xmldataobject.pbl / xmldataobject.pbd           # PB data object library
├── xmlconf.profiles.xml    # Profile sets (กลุ่มไฟล์ config)
├── xmlconf.files.xml       # รายการไฟล์ XML ทั้งหมด
├── xmlconf.constmap.xml    # Constant map (HTTP profile)
├── xmlconf.constmap.http.xml   # Constant map (HTTP)
├── xmlconf.constmap.https.xml  # Constant map (HTTPS)
├── xmlconf.codemap.xml     # Code → filename mapping
├── server.connection_string.xml     # Connection strings (สำคัญ!)
├── server.connection_string.xml.dist # Template
├── server.wcf_detail.xml   # WCF service endpoints
├── print.report.xml        # Report PDF path config
├── print.formsets.xml      # Printer/Passbook station config
├── print.formcode.xml      # Form code config
├── print.formmap.xml       # Form mapping
├── print.usermap.xml       # User print mapping
└── d_ln_loan_type_*.xml    # Loan type configuration (13 ไฟล์)
    ├── d_ln_loan_type_attrib_xmlconfig.xml
    ├── d_ln_loan_type_budycoll_xmlconfig.xml
    ├── d_ln_loan_type_intclear_xmlconfig.xml
    ├── d_ln_loan_type_intreturn_xmlconfig.xml
    ├── d_ln_loan_type_maxnetrecv_xmlconfig.xml
    ├── d_ln_loan_type_minnetrecv_xmlconfig.xml
    ├── d_ln_loan_type_minsalary_xmlconfig.xml
    ├── d_ln_loan_type_minsalcoll_xmlconfig.xml
    ├── d_ln_loan_type_pausecoll_xmlconfig.xml
    ├── d_ln_loan_type_reverse_xmlconfig.xml
    ├── d_ln_loan_type_reversecoll_xmlconfig.xml
    ├── d_ln_loan_type_subshr_xmlconfig.xml
    └── d_ln_loan_type_usecost_xmlconfig.xml
```

### server.connection_string.xml — Connection Profiles
ไฟล์ encoding: **UTF-16LE** มี 4 profiles:
| ID | Profile | Data Source | User |
|----|---------|-------------|------|
| 0 | Defalut@Cloud | 43.229.79.117/gcoop | iscomrc |
| 1 | MCC@Siam | web.siamcoop.com/gcoop | iscomrc |
| 2 | MCC@Dept | 43.229.79.117/gcoop | iscotestmrc |
| 3 | เทสเรียกเก็บ10/68 | 43.229.79.117/gcoop | iscomrckep |

### server.wcf_detail.xml — WCF Service Endpoints
WCF services ทั้งหมดชี้ไปที่ `192.168.253.181` (internal IP):
- Account, Admin, Agency, Budget, Busscom, Common
- Deposit, Finance, Investment และอื่นๆ
- Domain pattern: `{service}.wcf.gcoop`
- Method: `domain` (ใช้ domain name resolution)

### xmlconf.constmap.xml — Constant Configuration (ตัวอย่าง)
- `applet.pbslip_auto_update` = 1 (auto update)
- `applet.pbslip_client_path` = `C:\PBSLIP_MCC\`
- `applet.pbslip_server_path` = `http://mcc.siamcoop.com/MCC/GCOOP/Saving/SlipPB/SlipPB/`
- `centlog.using` = 1 (เปิด Central Log)
- Version PBSlip: 0.0.1

### print.formsets.xml — Passbook Printer Stations
กำหนด IP address ของเครื่องพิมพ์สมุดบัญชีแต่ละสถานี:
| formset_code | IP | หมายเหตุ |
|---|---|---|
| FULLPRINT | 10.20.241.35 | SERVER |
| Passbook15 | 192.168.99.15 | Station 15 |
| Passbook16 | 192.168.99.16 | Station 16 |
| Passbook17 | 192.168.99.17 | Station 17 |
| Passbook20 | 192.168.99.20 | Station 20 |
| Passbook25 | 192.168.99.25 | Station 25 |
| printloan | 192.168.99.11 | เครื่องพิมพ์กู้ |
| printloan-25/27/28 | 192.168.99.25-28 | เครื่องพิมพ์กู้เพิ่มเติม |

### print.report.xml
กำหนด PDF output path: `C:\GCOOP\Saving\Report\PDF\`

### xmlconf.profiles.xml — Profile Sets
| xmlset_no | ชื่อ |
|-----------|------|
| 1 | XML Configuration Program (this program) |
| 2 | ตั้งค่าส่วนกลางต่างๆ |
| 3 | iUnitTest Connections |
| 4 | loanright_xml |
| 5 | Connection String |

---

## 7. PLSQL — Oracle Stored Procedures และ Functions

### ภาพรวม
`PLSQL` เก็บ PL/SQL source code สำหรับ Oracle database รวมทั้ง PLImporter tool สำหรับ deploy ทั้งหมด 39 ไฟล์

### โครงสร้าง
```
PLSQL/
├── PLImporter.exe              # Tool import PL/SQL เข้า Oracle
├── PLImporter.config.dist      # Config template สำหรับ PLImporter
├── DataLibrary.dll             # .NET library
├── FUNCTIONS/   (17 ไฟล์)
├── PACKAGES/    (14 ไฟล์)
├── TYPES/       (8 ไฟล์)
└── PROCEDURES/  (0 ไฟล์ — โฟลเดอร์ว่าง)
```

### FUNCTIONS (17 ไฟล์)
| Function | หน้าที่ |
|----------|--------|
| ft_department.pls | ดึงข้อมูลแผนก |
| ft_getcontintrate.pls | ดึงอัตราดอกเบี้ยสัญญา |
| ft_getintrate.pls | ดึงอัตราดอกเบี้ย |
| ft_getmemname.pls | ดึงชื่อสมาชิก |
| ft_memgrp.pls | กลุ่มสมาชิก |
| ft_memgrpctrl.pls | ควบคุมกลุ่มสมาชิก |
| ft_memname.pls | ชื่อสมาชิก |
| ft_retrydate.pls | คำนวณวันที่ retry |
| ft_roundmoney.pls | ปัดเศษเงิน (ตาม cmroundmoney) |
| ftcalagemth.pls | คำนวณอายุ (เดือน) |
| ftcm_calagemth.pls | คำนวณอายุ (common) |
| ftcm_chgthdate.pls | แปลงวันที่ไทย |
| ftcm_roundmoney.pls | ปัดเศษเงิน (common) |
| ftcnvtdate.pls | แปลงวันที่ |
| ftreadtbaht.pls | อ่านจำนวนเงินเป็นตัวอักษร |
| ftreadtnum.pls | อ่านตัวเลขเป็นตัวอักษร |
| roundmoney.pls | ปัดเศษเงิน (พื้นฐาน) |

### ตัวอย่าง ft_roundmoney.pls
- Parameters: `adc_money`, `as_coopid`, `as_applgroup`, `as_function`
- ดึงกฎการปัดเศษจากตาราง `cmroundmoney` ตาม coop_id, applgroup_code, function_code
- รองรับหลายระดับ: function-specific → group-level → 'ALL' (fallback)

### PACKAGES (14 ไฟล์ = 7 package × header+body)
| Package | หน้าที่ |
|---------|--------|
| fpb | Finance package body |
| n_pk_doccontrol | ควบคุมเอกสาร |
| n_pk_lnnpl | NPL (หนี้ที่ไม่ก่อให้เกิดรายได้) |
| n_pk_string | String utility functions |
| pk_srv_mem_audit | Audit trail สมาชิก |
| pk_srv_mis_rpt | MIS Report service |
| w_sheet_ln_collredeem | Loan collateral redemption worksheet |

### TYPES (8 ไฟล์)
| Type | หน้าที่ |
|------|--------|
| datasourcetool.pls / .body.pls | Data source tool object |
| ntb_datacolumn.pls | Table type: data column |
| ntb_diminsion2.pls | Table type: 2-dimension |
| ntb_vc30.pls | Table type: VARCHAR2(30) |
| ntb_vc999.pls | Table type: VARCHAR2(999) |
| secure.pls | Security type |
| str_datacolumn.pls | Record type: data column |

### PLImporter.config.dist — การ Deploy
```xml
ConnectionString: Data Source=192.168.10.93/iorcl;...;User ID=iscotks
PLSQL_Path: C:\GCOOP_ALL\CEN\GCOOP\PLSQL
Drop_all_Type_before_import: true
Drop_all_Package_before_import: true
Drop_all_Function_before_import: true
Drop_all_Procedure_before_import: true
Drop_all_Trigger_before_import: true
Debug_mode: true
Runtime: .NET Framework 2.0 (v2.0.50727)
```
ก่อน import PLImporter จะ **drop ทั้งหมด** ก่อนแล้ว recreate

---

## 8. Deployment Scripts

### Build.bat — Build Script หลัก
**Path:** `/root/gcoop_hermes/mcc/Build.bat`

#### ขั้นตอนการ Build
1. Kill Java processes: `wmic process where (name="Javaw.exe") delete`
2. ตั้ง PATH: `%WINDIR%\Microsoft.NET\Framework\v4.0.30319`
3. **Stop IIS:** `NET STOP W3SVC`
4. Clean solution: `msbuild.exe GCOOP_MCC.sln /t:Clean`
5. Build: `msbuild.exe ... Extend_Saving.csproj /t:rebuild`
6. **Start IIS:** `NET START W3SVC`
7. รองรับ parameter SKIP=y เพื่อไม่ต้อง confirm

#### หมายเหตุ
- Projects อื่นๆ (commented out): DataLibrary, GcoopServiceCs, WcfService, WebServiceReport, CoreSavingLibrary, SingleSignOn, Saving, Extend_WcfService, Extend_WebServiceReport
- Project ที่ active build: `Extend_Saving.csproj`
- Drive: อ้างอิงจาก script drive `%~d0`

### Deploy-IIS-Extend.v4.bat — Deploy IIS Application Pools
**Path:** `/root/gcoop_hermes/mcc/Deploy-IIS-Extend.v4.bat`

#### Application Pools ที่สร้าง
| App Pool | Runtime | 32-bit | Identity |
|----------|---------|--------|---------|
| MCC.saving | v4.0 | enable32BitAppOnWin64=true | SpecificUser (admin) |
| MCC.wcf | v2.0 | enable32BitAppOnWin64=true | default |
| MCC.report | v2.0 | enable32BitAppOnWin64=true, Classic | NetworkService |

#### Virtual Applications ที่ deploy
| Path | Physical Path | App Pool |
|------|--------------|---------|
| /MCC | %ROOT_DIR%\GCOOP_ALL\MCC | (none) |
| /MCC/GCOOP/Saving | ...GCOOP\Saving | MCC.saving |
| /MCC/GCOOP/WcfService | ...GCOOP\WcfService | MCC.wcf |
| /MCC/GCOOP/WebServiceReport | ...GCOOP\WebServiceReport | MCC.report |

#### MIME Types เพิ่มเติม
- `.pbd` → `application/pbd-stream`
- `.trf` → `text/plain`

#### การตั้งค่าเพิ่มเติม
- Anonymous Authentication: username ว่าง
- Directory Browse: enabled
- สร้าง directory: `%ROOT_DIR%\PBSLIP_GCOOP` พร้อม full permission (Everyone:F)
- Idle Timeout: 30 นาที สำหรับทุก App Pool

---

## 9. Renci.SshNet — SSH/SFTP Capabilities

### ภาพรวม
`Renci.SshNet` เป็น **SSH.NET library** ที่เก็บ source code ทั้งหมด (316 ไฟล์ .cs) embedded ไว้ใน project GCOOP แทนที่จะใช้เป็น NuGet package เหมือนปกติ ซึ่งแสดงว่า GCOOP มีความสามารถ SSH/SFTP เต็มรูปแบบ

### โครงสร้าง (316 .cs files)
```
Renci.SshNet/
├── (Root: ~65 files)              # Core SSH client classes
│   ├── SshClient.cs               # SSH client หลัก
│   ├── SftpClient.cs / .NET.cs / .NET40.cs  # SFTP client
│   ├── ScpClient.cs / .NET.cs     # SCP client
│   ├── Session.cs / .NET.cs / .NET40.cs     # SSH session
│   ├── Shell.cs / ShellStream.cs  # Interactive shell
│   ├── NetConfClient.cs           # NetConf client
│   ├── ForwardedPortLocal/Remote/Dynamic  # Port forwarding
│   ├── PasswordAuthenticationMethod.cs    # Password auth
│   ├── PrivateKeyAuthenticationMethod.cs  # Private key auth
│   ├── KeyboardInteractiveAuthenticationMethod.cs  # KB-interactive
│   ├── ConnectionInfo.cs / IConnectionInfo.cs
│   └── BaseClient.cs
├── Channels/                      # SSH protocol channels
│   ├── Channel.cs / ChannelSession.cs
│   ├── ChannelDirectTcpip.cs      # Direct TCP tunnel
│   ├── ChannelForwardedTcpip.cs   # Forwarded TCP tunnel
│   └── IChannel*.cs interfaces
├── Common/                        # Utilities
│   ├── BigInteger.cs, Extensions.cs
│   ├── Authentication*EventArgs.cs
│   └── HostKeyEventArgs.cs
├── Compression/                   # SSH compression
├── Security/                      # Cryptography
├── Sftp/                          # SFTP protocol
├── Messages/                      # SSH protocol messages
├── Netconf/                       # NetConf protocol
├── Properties/                    # Assembly info
└── Renci.SshNet.csproj           # C# project file
```

### ความสามารถ SSH ที่มี
| ความสามารถ | Class | รองรับ |
|---|---|---|
| SSH Command Execution | SshClient, SshCommand | ✅ |
| SFTP File Transfer | SftpClient | ✅ |
| SCP File Transfer | ScpClient | ✅ |
| Interactive Shell | Shell, ShellStream | ✅ |
| Local Port Forwarding | ForwardedPortLocal | ✅ |
| Remote Port Forwarding | ForwardedPortRemote | ✅ |
| Dynamic Port Forwarding | ForwardedPortDynamic | ✅ |
| Direct TCP Tunnel | ChannelDirectTcpip | ✅ |
| NetConf | NetConfClient | ✅ |
| Password Auth | PasswordAuthenticationMethod | ✅ |
| Private Key Auth | PrivateKeyAuthenticationMethod | ✅ |
| Keyboard Interactive Auth | KeyboardInteractiveAuthenticationMethod | ✅ |

### การใช้งานในระบบ
Library นี้ embed เป็น source code ไว้ใน project GCOOP ซึ่งน่าจะใช้สำหรับ:
- การ transfer ไฟล์ backup ผ่าน SFTP ไปยัง backup server
- การเชื่อมต่อกับระบบภายนอกผ่าน SSH tunnel
- อาจใช้ใน Backup script (ดูหัวข้อ 4 ด้านบน) สำหรับ transfer .DMP files

---

## 10. สรุปภาพรวม Infrastructure

### แผนภาพ Infrastructure
```
┌─────────────────────────────────────────────────────────────┐
│                    MCC Infrastructure                        │
├──────────────────┬──────────────────┬───────────────────────┤
│   Client Tools   │  Config System   │   Database Layer      │
│                  │                  │                       │
│ winUPBOOK        │ XMLConfig (PB)   │ Oracle DB             │
│ (VB.NET, OCX)    │ ├─ connection    │ ├─ PLSQL/FUNCTIONS    │
│ Passbook Printer │ ├─ wcf_detail    │ ├─ PLSQL/PACKAGES     │
│                  │ ├─ constmap      │ ├─ PLSQL/TYPES        │
│ winLKE           │ ├─ loan_type     │ └─ PLImporter.exe     │
│ (C#, Msre.ocx)  │ └─ print config  │                       │
│ Mag Card Reader  │                  │ 0.DB Scripts          │
│                  │ iReport (676)    │ ├─ Kill Sessions       │
│                  │ ├─ 560 ir_       │ ├─ Backup (expdp)     │
│                  │ ├─ 102 r_        │ ├─ Java WcfCalling    │
│                  │ ├─ 3 chq_        │ └─ SHA3 encryption    │
│                  │ └─ ...อื่นๆ      │                       │
├──────────────────┴──────────────────┴───────────────────────┤
│                   Support Tools                              │
│  CONVERT_MCC: Data Migration (PB pipelines + SQL scripts)   │
│  Renci.SshNet: SSH/SFTP library (316 .cs files embedded)    │
│  Build.bat: MSBuild + IIS restart                           │
│  Deploy-IIS-Extend.v4.bat: IIS App Pool setup               │
└─────────────────────────────────────────────────────────────┘
```

### ข้อสังเกตสำคัญ
1. **ClickOnce Deployment** — ทั้ง winUPBOOK และ winLKE ใช้ ClickOnce ผ่าน IIS สำหรับ auto-update client
2. **32-bit (x86)** — ทุก IIS Application Pool ถูก configure เป็น 32-bit (`enable32BitAppOnWin64=true`)
3. **Multi-profile DB** — มี 4 database profiles ต่าง user สำหรับ environments ต่างๆ (cloud, siam, dept, test)
4. **Java in Oracle** — ใช้ `loadjava` deploy Java class เข้า Oracle เพื่อเรียก HTTP จาก stored function
5. **SSH embedded** — Renci.SshNet embed source code ทั้งหมด ไม่ได้ใช้ NuGet
6. **Encoding** — XML config files ทั้งหมดใช้ **UTF-16LE** encoding
7. **Backup strategy** — มี Oracle Data Pump (`expdp`) + Network Drive mapping + MD5 checksum
