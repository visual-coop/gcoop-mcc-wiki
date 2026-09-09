---
title: GCOOP Database Management Tools
created: 2026-09-10
updated: 2026-09-10
type: entity
tags: [oracle, database, backup, deployment, tools]
sources: [raw/documents/core-oracle-datapump-studio.md]
confidence: high
---

# GCOOP Database Management Tools

## ภาพรวม
ชุดเครื่องมือจัดการฐานข้อมูล Oracle สำหรับระบบ GCOOP รวมถึงการ backup, restore, และการจัดการข้อมูล

## เครื่องมือหลัก

### 1. Oracle Data Pump Studio
- **ภาษา:** C# .NET 8.0 WinForms
- **ฟังก์ชัน:** GUI wrapper สำหรับ Oracle Data Pump (expdp/impdp)
- **เป้าหมาย:** จัดการ backup/restore แบบ enterprise

#### Services Architecture
1. **BackupArchiverService**
   - บีบอัด .dmp/.log เป็น .zip
   - จัดเก็บตามปีเดือน (yyyyMM structure)
   - Asynchronous processing

2. **OracleClientDetector**
   - ตรวจหา Oracle Client installation
   - ค้นหา expdp/impdp executable paths

3. **TaskSchedulerService**
   - Scheduled backup jobs
   - Windows Task Scheduler integration

4. **RetentionCleanerService**
   - ลบไฟล์ backup เก่าตาม retention policy
   - จัดการ disk space

### 2. SQL Scripts Collection
- **ตำแหน่ง:** `/core/GCOOP/ZModifyDB/St-Procedures-Functions-CII/`
- **เนื้อหา:** Oracle Functions, Procedures, Packages
- **ไฟล์สำคัญ:**
  - `ft_getlncontintrate.sql` - ดึงอัตราดอกเบี้ยสัญญา
  - `ft_getgrpaddr.sql` - ดึงที่อยู่กลุ่ม
  - `ft_calretrydtm.sql` - คำนวณวันที่ retry

## การใช้งานใน Production

### Backup Strategy
1. **Daily Backup:** Schema-level export
2. **Weekly Backup:** Full database export
3. **Monthly Archive:** Compressed long-term storage
4. **Retention:** 3 months daily, 12 months weekly, 5 years monthly

### Disaster Recovery
- Full database restore capability
- Point-in-time recovery options
- Cross-platform compatibility (Windows/Linux Oracle)

## เชื่อมต่อกับระบบอื่น
- **[[gcoop-core]]** - Core infrastructure
- **[[gcoop-mhd]]** - ฐานข้อมูลหลัก
- **[[gcoop-loan-system]]** - ข้อมูลสินเชื่อ
- **Oracle Database** - ฐานข้อมูลหลักของระบบ

## การตั้งค่าและการใช้งาน
1. Configure Oracle connection parameters
2. Setup export/import job configurations
3. Schedule automatic backup jobs
4. Monitor job status และ error logs
5. Manage backup file retention policies

## Technical Requirements
- Oracle Client 11g+ installed
- .NET 8.0 Runtime
- Windows OS (for Task Scheduler integration)
- Sufficient disk space for backup files