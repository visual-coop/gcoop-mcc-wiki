## [2026-09-19] ingest | MCC SVN update
- Sources: raw/articles/mcc-svn-2063-2026-09-19.md
- Updated: index.md
- Updates made to raw, entities, concepts, and log records.
- ใช้ในการดึงข้อมูล SVN ล่าสุดและอัปเดต wiki

## [2026-09-19] schema | ย้าย schema ไปใช้ชุดกลาง + เพิ่ม frontmatter
- สร้าง `coops/_shared/` — SCHEMA.md, TAXONOMY.md, NAMING.md, COVERAGE.md (schema กลางใช้ร่วมทุกสหกรณ์)
- `SCHEMA.md` ของ vault นี้เปลี่ยนเป็นไฟล์ชี้กลับ schema กลาง + ส่วนต่างของ MCC
- เพิ่ม `coop: mcc` ใน frontmatter ของทุกหน้าที่มี frontmatter
- เพิ่ม `svn_rev` เฉพาะหน้าที่มีหลักฐาน: gcoop-mcc-pbprocess-pbl-2051 (2051), gcoop-mcc-svn-2051-ireports (2051), raw/articles/mcc-svn-2063-2026-09-19 (2063), raw/documents/mcc-svn-2051-2026-09-18 (2051)
- normalize tag: `walfare` คงไว้ตามชื่อจริงในระบบ (โมดูล source คือ GCOOP/Saving/Applications/walfare) — ไม่แก้เป็น welfare
- หมายเหตุ: ตรวจพบว่าการแก้ spelling `walfare`->`welfare` เป็นการแก้ที่ผิด จึง revert กลับตาม source
