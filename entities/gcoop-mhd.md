---
title: GCOOP MHD — ระบบหลัก
created: 2026-09-07
updated: 2026-09-07
type: entity
tags: [architecture, aspnet, oracle, powerbuilder, saving, loan]
sources: []
confidence: high
---

# GCOOP MHD

ระบบหลักของ GCOOP พัฒนาด้วย ASP.NET (C#) และ Oracle Database
SVN: `http://svn.coopsiam.com:8080/svnGCOOPCORE/mhd/`

## โครงสร้าง Directory

```
mhd/
├── GCOOP/
│   ├── GCOOP_MHD.sln       — Visual Studio Solution
│   ├── Saving/             — ระบบเงินฝาก (ASP.NET Web)
│   ├── PLSQL/              — Oracle PL/SQL (Functions, Procedures, Packages)
│   ├── PBProcess/          — PowerBuilder batch processes
│   ├── PBReport125/        — PowerBuilder reports
│   ├── Renci.SshNet/       — SSH library (C#)
│   └── XMLConfig/          — XML configuration files
├── iSavBOfc/               — PowerBuilder iBank application
├── iconvISCwinTOweb/       — Data conversion tools
├── Isc_syncOnline/         — Online sync utility
├── Convert_MU/             — Data migration scripts
├── dbConvert/              — DB conversion tool
└── 0.DB/                   — Oracle DB scripts (import/export)
```

## ระบบเงินฝาก (Saving)
- ASP.NET Web Application
- มี DataWindow (PowerBuilder) สำหรับ reports
- เชื่อมกับ Oracle ผ่าน WCF/SOAP services

## Oracle PL/SQL
- **Functions:** `ft_roundmoney`, `ftreadtbaht`, `ft_getintrate`, `ft_memname` ฯลฯ
- **Packages:** `pk_srv_mis_rpt`, `pk_srv_mem_audit`, `n_pk_lnnpl`, `fpb` ฯลฯ
- **Types:** Custom Oracle types สำหรับ data structures

## Related
- [[gcoop-overview]] — ภาพรวมระบบทั้งหมด
- [[gcoop-core]] — Core infrastructure
