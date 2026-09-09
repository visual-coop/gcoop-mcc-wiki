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

## [2026-09-10] ingest | GCOOP Project Analysis - Extended Components
- Created: raw/documents/core-smartcard-simulator.md (Smart Card Reader Simulator)
- Created: raw/documents/core-pb-interest-service.md (PowerBuilder Interest Service)
- Created: raw/documents/core-oracle-datapump-studio.md (Oracle Data Pump Studio)
- Created: entities/gcoop-smartcard-system.md (Smart Card System Entity)
- Created: entities/gcoop-database-tools.md (Database Management Tools)
- Created: concepts/gcoop-interest-calculation.md (Interest Calculation System)
- Updated: index.md (14 total pages, reorganized alphabetically)
- Analysis scope: C# tools, PowerBuilder services, Oracle utilities

## [2026-09-10] ingest | PBProcess Analysis - PowerBuilder Batch Systems
- Created: raw/documents/gcoop-pbprocess-systems.md (PowerBuilder Process Systems)
- Created: entities/gcoop-pbprocess-systems.md (PBProcess Entity)
- Updated: index.md (16 total pages)
- Analysis scope: PBProcess modules, DEPT_ProcessWID, POSTTOFIN_ProcessWID, batch monitoring
- Found: 11 PB modules (pcaccount, pccommon, pcdeposit, etc.), 150+ .pbl files total
- Key discoveries: cmprocessing table, XML config patterns, batch automation systems

## [2026-09-10] ingest | MHD Screen Analysis Template and Methodology
- Created: raw/documents/mhd-screen-analysis-template.md (Screen Analysis Template)
- Created: concepts/gcoop-screen-analysis-methodology.md (Analysis Methodology)
- Updated: index.md (18 total pages)
- Analysis scope: ASP.NET + PowerBuilder integration analysis framework
- Key features: 10-section template, PB integration handling, quality assurance rules
- Target: Systematic screen analysis for GCOOP MHD loan system and others
