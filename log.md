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

## [2026-09-14] analyze | MCC Session 2: PowerBuilder Analysis
- Sources inspected: PBProcess/ (ls -la, pbprocess.pbt, pbprocess.pbw, pbprocess.usr.opt), iSavBOfc/ (isavbofc.ini x2, iBank.ini, isavbofc.pbt, isavbofc.pbw, iscprog.reg, amscwins_amtksb.txt), PBReport125/ (pbreport.pbt, RunReport.xml, runreport.bat, runreportdel.bat)
- Findings:
  - PBProcess: 11 core libs (82 KB → 14.4 MB), LibList confirmed from .pbt, function signatures from .usr.opt binary
  - iSavBOfc: 26 libs in 5 dirs (iBank/sBaseCom/sBussCom/PFC105/pbpro), PFC 10.5 framework
  - PBReport125: 7 local libs + ~30 CORE .pbd refs, Windows Task Scheduler every 1 min
  - Oracle: 5 connection profiles (prod=192.168.1.171, dev=localhost), TH8TISASCII, AutoCommit=false
  - Multi-bank Direct Credit: 9 banks (KTB/BBL/KBank/SCB/Citibank/PEA/BAAC/TKS/KTB-IP)
  - Branch codes: 057001 (prod) / 077001 (dev)
- Updated files:
  - raw/documents/mcc-session2-powerbuilder-analysis.md (17.6 KB) — NEW (overwrite)
  - entities/gcoop-mcc-powerbuilder.md (12.6 KB) — OVERWRITE
- Confidence: high (all findings from actual file inspection, no fabrication)

## [2026-09-14] analyze | MCC Session 3: Infrastructure Analysis
- Sources inspected: winUPBOOK/ (vbproj, .vb files, App.config, ODP.NET, pbUpbook/), winLKE/ (csproj, Form1.cs, App.config, Drivers/), CONVERT_MCC/ (17 SQL scripts, mcc_pipeline/*.pbl, cnv_addr/*.pbl), 0.DB/ (Backup/*.bat, ISCOMCC/*.sql, Java.WCF/WcfCalling.java, Oracle.SHA3/, WcfCalling.sql), GCOOP/iReport/ (count .jrxml), GCOOP/XMLConfig/ (all XML files), GCOOP/PLSQL/ (all .pls files + PLImporter.config.dist), Build.bat, Deploy-IIS-Extend.v4.bat, GCOOP/Renci.SshNet/ (structure + key .cs files)
- Findings:
  - winUPBOOK: VB.NET, .NET 4.0 x86, PEM.ocx passbook printer, ClickOnce versions 1.0.0.5-1.0.0.9, queries DPDEPTCONSTANT
  - winLKE: C# .NET 4.6.1, Msre.ocx magnetic reader, ClickOnce version 1.0.0.12, Win32 P/Invoke
  - CONVERT_MCC: 17 SQL migration scripts + 2 PowerBuilder programs (cnv_addr, mcc_pipeline with 6 domain libraries)
  - 0.DB: Oracle kill-session SQL (W3WP/RDS), Oracle backup (expdp→43.229.79.117, MD5), Java WcfCalling (loadjava→Oracle stored function), SHA3 encryption
  - iReport: 676 .jrxml total (560 ir_, 102 r_, 3 chq_, 6 ireport_, 3 sub_, 1 dw_), top domains: mis(104), kep(45), loan(33)
  - XMLConfig: UTF-16LE XML files, 4 DB profiles, WCF at 192.168.253.181, Passbook stations 192.168.99.15-28
  - PLSQL: 39 .pls files (17 FUNCTIONS, 14 PACKAGES, 8 TYPES, 0 PROCEDURES), PLImporter.exe drop-all strategy
  - Build.bat: Stop IIS → msbuild clean → msbuild Extend_Saving.csproj rebuild → Start IIS
  - Deploy-IIS-Extend.v4.bat: 3 App Pools (MCC.saving v4.0, MCC.wcf v2.0, MCC.report v2.0 Classic), all 32-bit
  - Renci.SshNet: 316 .cs files embedded source (SSH/SFTP/SCP/ForwardedPort/NetConf)
- Updated files:
  - raw/documents/mcc-session3-infrastructure-analysis.md (34 KB) — NEW
  - entities/gcoop-mcc-infrastructure.md (10.5 KB) — OVERWRITE
- Confidence: high (all findings from actual file inspection, no fabrication)

## [2026-09-14] re-ingest | MCC Session 1 — Web Interface Deep Analysis (ปรับปรุงใหม่)
- Source: raw/documents/mcc-session1-web-analysis.md (OVERWRITE, 45 KB)
- ข้อมูลจริงจาก code inspection: 24 โมดูล, 436 ASPX, 16 active modules
- Updated: entities/gcoop-mcc-web-system.md (OVERWRITE — ข้อมูลครบถ้วนจาก source code จริง)
- Updated: index.md (ปรับตัวเลขโมดูลและ ASPX ให้ถูกต้อง)
- Findings สำคัญ:
  - hr: 81 aspx (top module), rdc: 57, loan: 38, mbshr: 35, walfare: 33
  - WCF: 10 services (n_common, n_loan, n_deposit, n_keeping, n_finance, n_mbshr, n_account, n_divavg, n_investment, ATMcoreWeb)
  - Oracle: 10 connection strings (iscorfd, iscolap, iscobtg, iscomrc, iscoaero, iscocmt)
  - MCC-specific: _mcc screens (3 found), CoopId 003001/074001 logic, Token locking, SmartCard, Dual-DB mode
  - ws_mem_memberdetail: 34+ DataSets — หน้าที่ซับซ้อนที่สุด
  - payroll: hrpayroll + amworkcalendar tables, Buddhist year calculation
  - iReport: 117 CriteriaIReport schemas (incl. 3 MCC-specific)
  - Frame.Master: XmlConfigService + WebStateFactory + WcfCalling pattern
  - SSO: icoopthai.co domain, login at sav.icoopthai.co/CEN/GCOOP/SingleSignOn/
