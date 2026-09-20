# Wiki Index — GCOOP MCC

> Content catalog. ทุก wiki page listed พร้อม one-line summary
> อ่านไฟล์นี้ก่อนเสมอเพื่อหา pages ที่เกี่ยวข้อง
> Last updated: 2026-09-18 | Total pages: 16

## Entities (ระบบ/โมดูล)
- [[gcoop-mcc]] — ระบบสหกรณ์ MCC ภาพรวม (1.7 GB, 4-layer architecture, HR/RDC/Welfare เด่น, 24 modules 436 ASPX, vs MHD)
- [[gcoop-mcc-web-system]] — ระบบ Web MCC 24 โมดูล 436 ASPX (ASP.NET 4.0, 10 WCF, Oracle x86, MCC-specific screens)
- [[gcoop-mcc-powerbuilder]] — ระบบ PowerBuilder MCC: PBProcess 11 libs + iSavBOfc (PFC 10.5) + PBReport + Pipeline
- [[gcoop-mcc-infrastructure]] — Infrastructure MCC (winUPBOOK/winLKE ClickOnce, PLSQL 39 files, iReport 688 jrxml ณ 2026-09-18, XMLConfig)
- [[gcoop-mcc-ws-lc-memo]] — หน้า Dynamic Memo ใหม่ (investment) SVN 2051 — LCDOCMEMO*
- [[gcoop-mcc-ws-lc-npl-follow]] — หน้าติดตาม NPL (investment) อัปเดต SVN 2051
- [[gcoop-mcc-svn-2051-ireports]] — iReport ชุดใหม่/แก้ใน SVN 2035→2051
- [[gcoop-mcc-pbprocess-pbl-2051]] — dispatcher จาก binary pcloan/pcfinance/pcmbshr/pcdivavg (SVN 2051)

## Concepts
- [[gcoop-mcc-hr-rdc-welfare]] — HR/RDC/Welfare จุดเด่นธุรกิจ MCC (81+57+33 ASPX)
- [[gcoop-mcc-integration-architecture]] — สถาปัตยกรรม Integration ครบถ้วน (3 patterns, business flows, external systems, security, deployment)

## Comparisons
- [gcoop-mcc-vs-mhd](https://github.com/visual-coop/gcoop-kb-shared/blob/main/comparisons/gcoop-mcc-vs-mhd.md) — เปรียบเทียบ MCC vs MHD (ขนาด, business focus, tech stack เหมือนกัน 100%, MCC-specific features)

## Raw Sources
- [[mcc-session1-web-analysis]] — Session 1: Web Interface Analysis (436 ASPX, 24 modules, WCF+Oracle+iReport, MCC-specific, SSO)
- [[mcc-session2-powerbuilder-analysis]] — Session 2: PowerBuilder Analysis (11 core libs, iSavBOfc PFC10.5, 9 banks, 3 environments)
- [[mcc-session3-infrastructure-analysis]] — Session 3: Infrastructure (winUPBOOK/winLKE ClickOnce, PLSQL, iReport 676, XMLConfig, Build/Deploy)
- [[mcc-session4-integration-summary]] — Session 4: Integration Summary — สังเคราะห์ครบถ้วน (architecture, flows, external, deployment, security, maintenance)
- [[mcc-svn-2051-2026-09-18]] — raw changelog SVN MCC 2035→2051 (2026-09-18)
