# Wiki Schema — GCOOP Hermes

## Domain
ระบบสารสนเทศสหกรณ์ GCOOP (Cooperative Management System)
ครอบคลุม: สถาปัตยกรรมระบบ, โมดูลต่างๆ, ฐานข้อมูล Oracle, API, การ deploy, และการพัฒนาระบบ

## โครงสร้าง Project
- **mhd/** — ระบบหลัก GCOOP MHD (ASP.NET + Oracle + PowerBuilder)
  - `GCOOP/Saving/` — ระบบเงินฝาก (Web)
  - `GCOOP/PLSQL/` — Stored Procedures, Functions, Packages (Oracle)
  - `GCOOP/PBProcess/` — PowerBuilder batch process
- **core/** — ระบบ Core infrastructure
  - IIS, deploy scripts, Oracle tools, Smart Card, API

## Conventions
- File names: lowercase, hyphens, ไม่มี spaces (เช่น `gcoop-saving-module.md`)
- ทุก wiki page ต้องมี YAML frontmatter
- ใช้ `[[wikilinks]]` เชื่อมระหว่าง pages (อย่างน้อย 2 outbound links ต่อ page)
- เมื่ออัปเดต page ให้ bump `updated` date เสมอ
- ทุก page ใหม่ต้องเพิ่มใน `index.md`
- ทุก action ต้องบันทึกใน `log.md`

## Frontmatter
```yaml
---
title: Page Title
created: YYYY-MM-DD
updated: YYYY-MM-DD
type: entity | concept | comparison | query | summary
tags: [from taxonomy below]
sources: [raw/documents/source-name.md]
confidence: high | medium | low
---
```

## Tag Taxonomy
### ระบบ/โมดูล
- `saving` — ระบบเงินฝาก
- `loan` — ระบบเงินกู้
- `member` — ระบบสมาชิก
- `atm` — ระบบ ATM
- `dividend` — ระบบเงินปันผล
- `insurance` — ระบบประกัน
- `ncb` — ระบบ NCB
- `finance` — ระบบการเงิน

### เทคโนโลยี
- `oracle` — Oracle Database
- `aspnet` — ASP.NET Web
- `powerbuilder` — PowerBuilder
- `iis` — IIS Web Server
- `wcf` — WCF Service
- `api` — REST/SOAP API
- `plsql` — PL/SQL

### ประเภท
- `architecture` — สถาปัตยกรรมระบบ
- `database` — ฐานข้อมูล
- `deployment` — การ deploy
- `workflow` — กระบวนการทำงาน
- `bug` — ปัญหา/bug ที่พบ
- `todo` — งานที่ต้องทำ

## Page Thresholds
- **สร้าง page** เมื่อ entity/concept ปรากฏใน 2+ sources หรือสำคัญมากใน 1 source
- **เพิ่มใน page เดิม** เมื่อ source กล่าวถึงสิ่งที่มีอยู่แล้ว
- **แบ่ง page** เมื่อยาวเกิน 200 บรรทัด

## Update Policy
เมื่อข้อมูลใหม่ขัดแย้งกับเดิม:
1. ตรวจสอบวันที่ — ข้อมูลใหม่กว่ามักถูกต้องกว่า
2. หากขัดแย้งจริง ให้บันทึกทั้งสองฝ่ายพร้อมวันที่และ source
3. mark `contested: true` ใน frontmatter
