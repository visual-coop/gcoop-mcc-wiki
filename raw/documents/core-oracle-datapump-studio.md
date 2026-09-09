---
source_url: /root/gcoop_hermes/core/OracleDataPumpStudio/
ingested: 2026-09-10
sha256: [computed during write]
---

# Oracle Data Pump Studio - Backup/Restore Tool

## ภาพรวม
- **ภาษา:** C# .NET 8.0 WinForms
- **ฟังก์ชัน:** GUI tool สำหรับจัดการ Oracle Data Pump (expdp/impdp)
- **เป้าหมาย:** ทำ backup/restore ฐานข้อมูล Oracle ของระบบ GCOOP

## Services Architecture

### 1. BackupArchiverService
- **ฟังก์ชัน:** บีบอัดไฟล์ .dmp และ .log เป็น .zip
- **การจัดเก็บ:** จัดหมวดหมู่ตามปีเดือน (yyyyMM)
- **Async Processing:** ทำงานแบบ asynchronous

### 2. OracleClientDetector
- ตรวจหา Oracle Client ที่ติดตั้งในเครื่อง
- ค้นหา expdp/impdp executable paths

### 3. TaskSchedulerService
- จัดการ scheduled backup jobs
- รองรับ Windows Task Scheduler integration

### 4. CommandBuilderService
- สร้าง command line สำหรับ expdp/impdp
- จัดการ parameters และ options

### 5. ProcessRunnerService
- เรียกใช้ Oracle Data Pump commands
- จัดการ output และ error logging

### 6. RetentionCleanerService
- ลบไฟล์ backup เก่าตาม retention policy
- ประหยัด disk space

## Models
- `ExportJobConfig` - การตั้งค่า export job
- `ImportJobConfig` - การตั้งค่า import job
- `ScheduleJobConfig` - การตั้งค่า scheduled job
- `SiteConfig` - การตั้งค่าทั่วไป
- `RemapItem` - การ remap schema/tablespace

## การใช้งาน
1. Configure Oracle connection
2. Setup export/import parameters
3. Schedule automatic backups
4. Monitor job status และ logs
5. Manage backup file retention

## ความเชื่อมโยงกับ GCOOP
- Backup ฐานข้อมูล Oracle หลักของระบบ GCOOP
- ใช้ใน production environment สำหรับ disaster recovery
- รองรับการ export แบบ selective (specific schemas/tables)