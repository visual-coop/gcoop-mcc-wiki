---
title: GCOOP Core — Infrastructure
created: 2026-09-07
updated: 2026-09-07
type: entity
tags: [architecture, deployment, iis, oracle, api]
sources: []
confidence: high
---

# GCOOP Core

ระบบ Infrastructure และเครื่องมือสนับสนุนของ GCOOP
SVN: `http://svn.coopsiam.com:8080/svnGCOOPCORE/core/`

## โครงสร้าง Directory

```
core/
├── GCOOP/              — Web application หลัก
├── API_PRINT/          — API สำหรับพิมพ์เอกสาร
├── GEXT/               — Extension modules
├── PHP/                — PHP components
├── DEPT_ProcessWID/    — Department process worker
├── POSTTOFIN_ProcessWID/ — Post to finance process worker
├── OracleDataPumpStudio/ — GUI tool สำหรับ Oracle backup/restore
├── SmartCardReader.SCR-N3300/ — Smart Card Reader driver
├── SmartCardSimulator/ — Simulator สำหรับทดสอบ Smart Card
└── *.bat               — Deploy/management scripts
```

## เครื่องมือสำคัญ

### OracleDataPumpStudio
- GUI tool สำหรับ Oracle Data Pump (expdp/impdp)
- รองรับทั้ง GUI mode และ headless backup
- พัฒนาด้วย .NET

### Smart Card System
- รองรับ SCR-N3300 Smart Card Reader
- มี Simulator สำหรับทดสอบโดยไม่ใช้ hardware จริง
- ใช้สำหรับ authentication สมาชิก

## Deploy Scripts
- `Deploy-IIS-Core.v4.bat` — Deploy IIS Core application
- `1.IIS.CORE.start/stop.ApplicationPool.bat` — จัดการ Application Pool
- `batchfile_monitoring.bat` — Monitoring script

## Related
- [[gcoop-overview]] — ภาพรวมระบบทั้งหมด
- [[gcoop-mhd]] — ระบบ MHD หลัก
