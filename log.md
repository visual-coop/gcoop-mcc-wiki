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

## [2026-09-19] move | ย้ายหน้าเปรียบเทียบข้ามสหกรณ์ออกจาก vault
- `comparisons/gcoop-mcc-vs-mhd.md` -> `coops/_shared/comparisons/` (repo `gcoop-kb-shared`)
  เหตุผล: หน้าเปรียบเทียบข้ามสหกรณ์ไม่ควรอยู่ใน vault ของฝ่ายใดฝ่ายหนึ่ง
- แปลง `gcoop-mcc-vs-mhd` เป็น markdown link ไป repo กลาง ใน 4 หน้า:
  index.md, entities/gcoop-mcc.md, concepts/gcoop-mcc-hr-rdc-welfare.md, concepts/gcoop-mcc-integration-architecture.md
- รับ raw ที่เป็นเนื้อหา MCC กลับจาก MHD vault: `raw/documents/critical-mcc-chat-summary-20260909.md`, `raw/documents/critical-md-gcoop-งานเร่งด่วน.md`
- `comparisons/` ของ vault นี้ว่างแล้ว
