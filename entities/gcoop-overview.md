---
title: ภาพรวมระบบ GCOOP
created: 2026-09-07
updated: 2026-09-09
type: entity
tags: [architecture, aspnet, oracle, powerbuilder]
sources: []
confidence: high
---

# ภาพรวมระบบ GCOOP

ระบบสารสนเทศสหกรณ์ GCOOP (Cooperative Management System) พัฒนาสำหรับสหกรณ์ออมทรัพย์ในประเทศไทย

## Tech Stack

| Layer | Technology |
|---|---|
| Web UI | ASP.NET WebForms (C#) — IIS 32-bit (x86) |
| Business Logic | PowerBuilder 12.5 Classic (.sru/.srd) via WCF |
| Database | Oracle (ตารางหลัก prefix: ln, mb, sl, sh, cm, ac) |
| Batch Process | PowerBuilder Process (.pbl) ผ่าน runProcessing / runProcessingExtend |
| Reporting | iReport 5.0.4 (.jrxml) |

## โครงสร้าง Repository (SVN)

```
svn.coopsiam.com:8080/svnGCOOPCORE/
├── mhd/          # ระบบ MHD (สหกรณ์ออมทรัพย์ MHD)
│   ├── GCOOP/Saving/Applications/   # ASP.NET Web Screens
│   ├── GCOOP/PBProcess/             # MHD-specific PB Process
│   └── GCOOP/PLSQL/                 # Oracle Stored Procedures
└── core/         # Core infrastructure
    ├── GCOOP/PBService125/           # WCF Services (.pbl)
    ├── GCOOP/PBProcess/              # Core PB Process
    └── .output/                      # Analysis output
```

**Local checkout:** `/root/gcoop_hermes/mhd/` และ `/root/gcoop_hermes/core/`

## โมดูลหลัก

- **ระบบเงินกู้** — [[gcoop-loan-system]] (33 หน้าจอ วิเคราะห์แล้ว)
- **ระบบเงินฝาก** — saving module (ยังไม่ได้วิเคราะห์)
- **ระบบสมาชิก** — member module (ยังไม่ได้วิเคราะห์)

## กฎสำคัญ (AGENTS.md)

1. C# (.cs/.aspx): UTF-8 with BOM เสมอ
2. PB Classic (.sru/.srd): Windows-874 — ห้าม convert เป็น UTF-8
3. ห้าม Auto-build — list ไฟล์ที่แก้ไขแทน
4. IIS 32-bit (x86) — dependencies ต้องเป็น x86
5. ห้าม SVN network commands นอกจากจะขอ
6. iReport 5.0.4 DTD เท่านั้น

## Related
- [[gcoop-mhd]] — ระบบ MHD รายละเอียด
- [[gcoop-core]] — Core infrastructure รายละเอียด
- [[gcoop-loan-system]] — ระบบเงินกู้
