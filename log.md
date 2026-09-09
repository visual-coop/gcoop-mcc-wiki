# Wiki Log — GCOOP Hermes

> Chronological record of all wiki actions. Append-only.
> Format: `## [YYYY-MM-DD] action | subject`
> Actions: ingest, update, query, lint, create, archive

## [2026-09-07] create | Wiki initialized
- Domain: ระบบสารสนเทศสหกรณ์ GCOOP
- Structure created: SCHEMA.md, index.md, log.md
- Directories: raw/, entities/, concepts/, comparisons/, queries/
- Initial pages created: gcoop-overview.md, gcoop-mhd.md, gcoop-core.md

## [2026-09-07] ingest | ระบบเงินกู้ (mhd + core)
- Created: entities/gcoop-loan-system.md
- Created: concepts/gcoop-loan-collateral.md
- Created: raw/documents/mhd-plsql-n-pk-lnnpl.md
- Created: raw/documents/core-loanservice-cs.md
- Created: raw/documents/core-loancollservice-cs.md

## [2026-09-07] query | วิเคราะห์ End-to-End กลุ่ม 1 คำร้องขอกู้
- Analyzed: ws_lon_reqloan, ws_lon_reqloanext, ws_lon_reqloan_adjust_onlne, ws_lon_promise
- Created: queries/loan-analysis-group1-request.md
- Method: อ่าน .aspx.cs, .ascx.cs, DataSet.xsd, .srd (UTF-16LE decode), .sru (UTF-16LE decode)
- WCF traced to: lncoopsrv.pbl (n_cst_lncoopsrv_lnoperate.sru, interest.sru, calperiod.sru, allrequest.sru)
- Tables confirmed: lnreqloan, lnreqloancoll, lnreqloanclr, lncontmaster, lncontcoll, lnloantype + related

## [2026-09-07] lint | กลุ่ม 1 — 0 broken links, 4 orphan raw docs (expected), index updated
- Fixed: index.md อัปเดตครบ 9 pages
- Outbound links: ทุก page มี ≥ 2 wikilinks ✅
- raw/ documents เป็น orphan โดยธรรมชาติ (immutable sources ไม่ต้องลิงก์)

## [2026-09-09] ingest | ระบบสินเชื่อ — สร้าง entity/concept pages ที่หายไป
- สร้าง: entities/gcoop-loan-system.md (ระบบเงินกู้ครบ 33 หน้าจอ + 6 กลุ่ม + tables + WCF + PBProcess + dead code)
- สร้าง: concepts/gcoop-loan-collateral.md (logic ค้ำประกัน lifecycle + risk points)
- สร้าง: entities/gcoop-overview.md (ภาพรวม GCOOP tech stack + repo structure + AGENTS.md rules)
- สร้าง: entities/gcoop-mhd.md (MHD paths + CoreSavingLibrary + WCF pattern)
- สร้าง: entities/gcoop-core.md (PBService125 mapping + PBProcess Core vs MHD + encoding)
- อัปเดต: index.md → 16 pages (เดิม 9 → บวก 5 entity/concept ใหม่ + master analysis query)
- Source: สังเคราะห์จาก queries/loan-analysis-group*.md + queries/ระบบสินเชื่อ_Analysis_20260908.md
