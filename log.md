# Wiki Log — GCOOP MCC

> Chronological record of all wiki actions. Append-only.
> Format: `## [YYYY-MM-DD] action | subject`

## [2026-09-14] create | Wiki initialized
- Domain: ระบบสหกรณ์ออมทรัพย์ MCC (Member Credit Cooperative)
- Structure: SCHEMA.md, index.md, log.md, raw/documents (4 sessions), session-prompts (4)
- Vault: gcoop-mcc-wiki แยกจาก gcoop-mhd-wiki

## [2026-09-14] ingest | MCC Sessions 1-4 — Web + PowerBuilder + Infrastructure + Integration
- Sources: raw/documents/mcc-session1-web-analysis.md (8.7 KB), mcc-session2-powerbuilder-analysis.md (12.1 KB), mcc-session3-infrastructure-analysis.md (12 KB), mcc-session4-integration-summary.md (21 KB)
- Created entities (4):
  - entities/gcoop-mcc.md — ภาพรวม MCC (1.7 GB, 13% ใหญ่กว่า MHD, 3-layer architecture)
  - entities/gcoop-mcc-web-system.md — Web 23 โมดูล 6,721 ไฟล์ + 3 MCC-specific screens
  - entities/gcoop-mcc-powerbuilder.md — PB 116 libs (pckeeping 14.3 MB, pcdeposit 11.9 MB) + iSavBOfc + Pipeline
  - entities/gcoop-mcc-infrastructure.md — winUPBOOK/winLKE/PLSQL/iReport 2000+/XMLConfig/ClickOnce
- Created concepts (2):
  - concepts/gcoop-mcc-hr-rdc-welfare.md — HR 1152 + RDC 748 + Welfare 441 จุดเด่น MCC
  - concepts/gcoop-mcc-integration-architecture.md — 3 flow patterns + deployment + Thai support
- Created comparisons (1):
  - comparisons/gcoop-mcc-vs-mhd.md — ขนาด/ business focus / tech stack / MCC-specific screens
- Updated: index.md (11 pages total), log.md
- Tags: architecture, aspnet, powerbuilder, oracle, deployment, workflow, saving, loan, member
- Wikilinks: ทุก page ≥2 outbound links ✅, sources ครบ ✅, frontmatter ตาม SCHEMA ✅

## [2026-09-14] auto-sync | Server auto-push + SVN update
- Cron: SVN Update MCC and MHD Daily (every day 07:30), MCC Wiki Auto-Push Monitor (every 5m)
- GitHub: visual-coop/gcoop-mcc-wiki (main)
