# GCOOP MCC Infrastructure — Entity Documentation

**Entity Type:** Infrastructure & Supporting Systems  
**วันที่อัพเดท:** 2026-09-14  
**Session:** MCC Session 3 — Infrastructure Analysis  
**อ้างอิง:** [mcc-session3-infrastructure-analysis.md](../raw/documents/mcc-session3-infrastructure-analysis.md)

---

## ภาพรวม

GCOOP MCC Infrastructure ประกอบด้วยเครื่องมือสนับสนุน 4 กลุ่มหลัก:
1. **Client Tools** — winUPBOOK (สมุดบัญชี), winLKE (บัตรแถบแม่เหล็ก)
2. **Configuration System** — XMLConfig + ไฟล์ XML ทั้งหมด
3. **Database Layer** — PLSQL, 0.DB scripts, Oracle Backup
4. **Development Support** — CONVERT_MCC (migration), iReport (676 reports), Renci.SshNet (SSH)

---

## winUPBOOK — Passbook Update Tool

| คุณสมบัติ | รายละเอียด |
|-----------|-----------|
| ภาษา | Visual Basic .NET (VB.NET) |
| .NET Version | Framework 4.0 Client Profile |
| Platform | x86 (32-bit) |
| DB Access | ODP.NET (Oracle.DataAccess.dll) |
| Printer Driver | PEM.ocx (PEMS Passbook Printer ActiveX) |
| Deployment | ClickOnce via IIS |
| Version History | 1.0.0.5 → 1.0.0.9 (5 versions, latest: 1.0.0.9) |

### Source Files หลัก
- `UpdateBook.vb` — Logic อัพเดทสมุด, query `DPDEPTCONSTANT` สำหรับ page rules
- `Connection.vb` — Oracle connection management
- `Base.vb` — Base class
- `Form1.vb`, `Form2.vb` — UI หน้าจอหลัก
- `frmThread1.vb`, `frmThread2.vb` — Multi-thread processing
- `ProgressDialog.vb` — แสดง progress

### DB Table ที่ใช้
- `DPDEPTCONSTANT` — กฎการพิมพ์สมุด: Pagetotal_Pb, Lineperpage_Pb, Linenomidpage_Pb, Nlinenextmid_Pb

---

## winLKE — Magnetic Card Reader Tool

| คุณสมบัติ | รายละเอียด |
|-----------|-----------|
| ภาษา | C# .NET |
| .NET Version | Framework 4.6.1 |
| DB Access | ODP.NET (Oracle.DataAccess.dll) |
| Device Driver | Msre.ocx (MSRE Magnetic Stripe Reader) |
| Deployment | ClickOnce, version 1.0.0.12 |
| External Exe | winLKE.exe (C:\GCOOP_ALL\AERO\winLKE\Drivers\winLKEAppDevices\) |

### Source Files หลัก
- `Form1.cs` — Main form, Win32 P/Invoke (ShowWindow, FindWindow, MoveWindow)
- `Program.cs` — Entry point

### Driver Files
- `Driver LKE.rar` — Original driver package
- `Drivers/MSRE.zip` — MSRE device driver
- `Drivers/winLKEAppDevices.zip` — Application device driver

---

## CONVERT_MCC — Data Migration Tools

### SQL Scripts (17 ไฟล์)
| ไฟล์ | หน้าที่ |
|------|--------|
| Check shrlon bal.sql | ตรวจสอบยอด หุ้น/กู้ |
| Delete Data.txt | ลบข้อมูลก่อน migrate |
| SQL Update หลัง CNV.txt | Update deposit หลัง convert |
| SQL Update หลัง CNV MEM.txt | Update สมาชิกหลัง convert |
| SQL Update หลัง CNV_INS.txt | Update ประกันหลัง convert |
| SQL update lncontmaster.txt | Update ตารางสัญญากู้ |
| mapkepitem.sql | Map keeping items |
| maplonitem.sql | Map loan items |
| mapshritem.sql | Map share items |

### PowerBuilder Pipeline Programs (2 programs)
#### cnv_addr (Convert ที่อยู่)
- `cnvaddr.pbl` — Main library
- `aCOMSRV/fmcomsv.pbl`, `fmsheet.pbl` — COM service libraries

#### mcc_pipeline (Data Pipeline)
| Library | Domain |
|---------|--------|
| mcc_pipe_member.pbl | สมาชิก |
| mcc_pipe_dep.pbl | เงินฝาก |
| mcc_pipe_shrlon.pbl | หุ้น/เงินกู้ |
| mcc_pipe_keeping.pbl | ออมทรัพย์พิเศษ |
| mcc_pipe_insurance.pbl | ประกัน |
| mcc_pipe_divavg.pbl | เงินปันผล/เฉลี่ยคืน |

---

## 0.DB — Database Scripts

### Oracle Session Management
- `Alter_Oracle_Kill_Sessions_user_w3w.sql` — Kill INACTIVE sessions (W3WP + idle > 15 min) ทั้ง Oracle ปกติและ Amazon RDS

### Oracle Backup
| Script | หน้าที่ |
|--------|--------|
| OracleSync2BackupServer.master.bat | Master script + logging |
| OracleSync2BackupServer.script.cloud_backup.bat | expdp → map drive → MD5 |
| OracleSync2BackupServer.script.cloud_backup_7z.bat | expdp + 7z compress |

- Schema: `ISCOMCC`, Server: `43.229.79.117:1521/gcoop`

### User Management (ISCOMCC/)
- `Alter_recreate_iscomcc_user.sql` — recreate ISCOMCC user, grant permissions, kill running jobs
- `ExpOracleDB-*.bat` / `ImpOracleDB-*.bat` — expdp/impdp scripts

### Java Integration
- `WcfCalling.java` → compile → `loadjava` → Oracle stored function `WCFCALLING`
- ทำให้ Oracle stored procedure เรียก HTTP POST ไปยัง WCF service ได้
- Example: เรียก `svc.php` endpoints ของ GCOOP

### SHA3 Encryption (Oracle.SHA3/)
- Java SHA3 implementation deploy เข้า Oracle
- PL/SQL function `FT_ENC` สำหรับ encrypt ภายใน DB

---

## iReport — Report Templates

| สถิติ | ค่า |
|-------|-----|
| จำนวนทั้งหมด | **676 .jrxml files** |
| Format | iReport 5.0.4 (JasperReports) |
| Location | GCOOP/iReport/Reports/ (flat) |
| รูปภาพ | 11 ไฟล์ใน Picture/ |

### การแบ่งประเภท
| Prefix | จำนวน | Domain |
|--------|--------|--------|
| ir_ | 560 | รายงานหลัก |
| r_ | 102 | รายงานรอง |
| ireport_ | 6 | iReport template |
| sub_ | 3 | Sub-reports |
| chq_ | 3 | เช็ค (KTB, TMB) |
| dw_ | 1 | DataWindow slip |

### Domain ย่อย (ir_ reports)
| Domain | จำนวน |
|--------|--------|
| mis | 104 |
| kep (keeping) | 45 |
| coopid | 41 |
| loan | 33 |
| report | 30 |
| pm | 22 |
| didurt | 20 |
| ass (assistance) | 19 |
| diinvt (dividend) | 18 |
| inv | 16 |
| hr | 16 |
| brw | 15 |
| อื่นๆ | ~91 |

---

## XMLConfig — Configuration System

### Technology
- PowerBuilder application (`xmlconfig.exe`)
- ไฟล์ XML ทั้งหมดใช้ encoding **UTF-16LE**

### ไฟล์ Configuration หลัก
| ไฟล์ | หน้าที่ |
|------|--------|
| server.connection_string.xml | Oracle connection profiles (4 profiles) |
| server.wcf_detail.xml | WCF service endpoints (IP: 192.168.253.181) |
| xmlconf.constmap.xml | System constants (pbslip, centlog, etc.) |
| print.formsets.xml | Passbook printer stations (IP mapping) |
| print.report.xml | PDF output path |
| d_ln_loan_type_*.xml | Loan type configuration (13 ไฟล์) |

### Database Profiles
| Profile | Server | User |
|---------|--------|------|
| Defalut@Cloud | 43.229.79.117/gcoop | iscomrc |
| MCC@Siam | web.siamcoop.com/gcoop | iscomrc |
| MCC@Dept | 43.229.79.117/gcoop | iscotestmrc |
| เทสเรียกเก็บ10/68 | 43.229.79.117/gcoop | iscomrckep |

### Passbook Printer Stations
- SERVER (FULLPRINT): 10.20.241.35
- Passbook15-25: 192.168.99.15, 16, 17, 20, 25
- printloan, printloan-25/27/28: 192.168.99.11, 25, 27, 28

---

## PLSQL — Oracle Stored Procedures

### สถิติ
| ประเภท | จำนวนไฟล์ |
|--------|----------|
| FUNCTIONS | 17 .pls |
| PACKAGES | 14 .pls (7 packages × header+body) |
| TYPES | 8 .pls |
| PROCEDURES | 0 (โฟลเดอร์ว่าง) |
| **รวม** | **39 ไฟล์** |

### Functions สำคัญ
- `ft_roundmoney` — ปัดเศษเงินตามกฎ `cmroundmoney`
- `ftreadtbaht` — อ่านจำนวนเงินเป็นคำอ่านภาษาไทย
- `ft_getintrate` — ดึงอัตราดอกเบี้ย
- `ft_memname` / `ft_getmemname` — ดึงชื่อสมาชิก
- `ftcm_chgthdate` — แปลงวันที่เป็น Thai date format

### Packages สำคัญ
- `pk_srv_mis_rpt` — MIS Report service
- `pk_srv_mem_audit` — Audit trail สมาชิก
- `n_pk_lnnpl` — NPL management
- `n_pk_doccontrol` — Document control

### PLImporter
- Tool: `PLImporter.exe` (.NET 2.0)
- Drop all objects before import (Types, Packages, Functions, Procedures, Triggers)

---

## Deployment

### Build.bat (ขั้นตอน Build)
1. Kill Java processes (ReportBuilder)
2. Stop IIS (`NET STOP W3SVC`)
3. Clean: `msbuild.exe GCOOP_MCC.sln /t:Clean`
4. Build: `msbuild.exe Extend_Saving.csproj /t:rebuild`
5. Start IIS (`NET START W3SVC`)

### Deploy-IIS-Extend.v4.bat (IIS Setup)
| App Pool | Runtime | 32-bit | Identity |
|----------|---------|--------|---------|
| MCC.saving | v4.0 | ✅ | Specific User |
| MCC.wcf | v2.0 | ✅ | Default |
| MCC.report | v2.0 + Classic | ✅ | NetworkService |

Virtual Paths: `/MCC/GCOOP/Saving`, `/MCC/GCOOP/WcfService`, `/MCC/GCOOP/WebServiceReport`

---

## Renci.SshNet — SSH Library

| คุณสมบัติ | รายละเอียด |
|-----------|-----------|
| ไฟล์ | 316 .cs files (embedded source) |
| Project | Renci.SshNet.csproj |
| Capabilities | SSH, SFTP, SCP, Shell, Port Forwarding, NetConf |
| Auth Methods | Password, Private Key, Keyboard Interactive |
| Multi-target | .NET และ .NET40 variants |

### ความสามารถหลัก
- `SshClient` — execute remote commands
- `SftpClient` — SFTP file transfer  
- `ScpClient` — SCP file transfer
- `ForwardedPortLocal/Remote/Dynamic` — port forwarding
- `ChannelDirectTcpip` — TCP tunneling

---

## Dependencies สำคัญ

| Component | Technology | Version |
|-----------|-----------|---------|
| Database | Oracle | 11g/12c (SID: gcoop) |
| .NET | Framework | 2.0, 4.0, 4.6.1 |
| IIS | IIS (32-bit) | v4.0, v2.0 app pools |
| Reports | iReport | 5.0.4 (JasperReports) |
| Passbook Printer | PEM.ocx | - |
| Magnetic Reader | Msre.ocx | - |
| SSH Library | Renci.SshNet | (embedded source) |
| DB Access | ODP.NET | Oracle.DataAccess.dll |
| Build | MSBuild | .NET Framework v4.0.30319 |

---

## Related Entities
- [gcoop-mcc-saving.md](gcoop-mcc-saving.md) — Saving module (ใช้ IIS App Pool จาก Deploy script)
- [gcoop-mcc-wcf.md](gcoop-mcc-wcf.md) — WCF services (config ใน server.wcf_detail.xml)
- [gcoop-mcc-database.md](gcoop-mcc-database.md) — Oracle DB schema
