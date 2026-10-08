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

## [2026-09-20] update | ทำให้ wiki เป็นปัจจุบัน ณ rev 2085
- registry: current_rev 2063 -> 2085, last_analyzed_rev 2085
- เพิ่ม raw changelog: `raw/articles/mcc-svn-2085-2026-09-20.md` (22 ไฟล์, 2063->2085)
- เพิ่ม entity ใหม่: `entities/gcoop-mcc-loan-contadjust.md` — ปรับสัญญาเงินกู้และค้ำประกัน
  วิเคราะห์จากไฟล์จริง: `ws_lon_apvpromise` (อนุมัติแล้ว delete lncontcoll แล้ว insert ใหม่ทั้งชุด
  จาก LNREQCONTADJUST + lnreqcontadjustcoll โดยคัด contadjust_type='NEW'),
  `ws_lon_apvloan`, criteria `u_cri_coopid_memno_loancont_refcollno`,
  pcmbshr.pbl (14 process), iReport 11 ไฟล์
- **แยก "หน้าสถานะปัจจุบัน" กับ "snapshot ของช่วง rev"** ตาม SCHEMA.md ที่แก้ใหม่:
  - snapshot: `entities/gcoop-mcc-svn-2051-ireports.md`, `entities/gcoop-mcc-pbprocess-pbl-2051.md`,
    `raw/documents/mcc-svn-2051-2026-09-18.md`, `raw/articles/mcc-svn-2063-*.md`,
    `raw/articles/mcc-svn-2085-*.md` -> `svn_rev_range` + `historical: true`
  - `coverage-report.py` ตัดหน้า historical ออกจากรายงานความล้าสมัยแล้ว
- ผล: MCC ไม่มีหน้าล้าสมัยเหลือ (เดิม 3)

## [2026-09-20] fix | verification pass (credentials / frontmatter / index count)
- `raw/documents/mcc-session2-powerbuilder-analysis.md` — redact credential value (1 hit) → `***`
- `entities/gcoop-mcc-infrastructure.md` — เพิ่ม frontmatter ที่หายไป (หน้าก่อนยุค schema)
- `index.md` — แก้ Total pages จาก 20 เป็น 17 (นับจาก entry จริง)

## [2026-09-22] update | rev 2100 — งานหลักประกันต่อเนื่อง
- registry: mcc current_rev 2085 -> 2100, last_analyzed_rev 2100
- อัปเดต `entities/gcoop-mcc-loan-contadjust.md` เป็น rev 2100 (จาก 2085)
- 33 ไฟล์ใน `Applications/loan` เปลี่ยน กระจุก 3 หน้าจอ:
  - **ใหม่** `ws_lon_collateral_master_ctrl/ws_lon_editcollateral_master_ctrl/`
    หน้าจอย่อยแก้ไขหลักประกันตาม mode — ตาราง `lncollmaster`,
    `lnucfcollmasttype`, `lnucfcollmasttypegrp` (23 ไฟล์ในหน้าหลักรวม DsLand/DsBding)
  - **ใหม่** `ws_lon_reqloanext_ctrl/DsLoanCollSubcommit.ascx` — ค้ำประกันย่อย
    ของคำขอกู้ต่อ ตารางใหม่ `LNREQLOANCOLLSUBCOMMIT` (DataSourceRepeater + ปุ่มลบ)
  - `ws_lon_proc_paymoneyreturn_ctrl` เพิ่ม DataSet12 (1 ไฟล์)
- ทำ `raw/documents/*session*-analysis.md` ของ aero/swu เป็น `historical: true`
  (เป็น snapshot ของ session วิเคราะห์ ไม่ใช่หน้าสถานะปัจจุบัน)
## [2026-09-29] add | gcoop-mcc-insurance-system
- เพิ่ม `entities/gcoop-mcc-insurance-system.md` (rev 2100) — ระบบประกันชีวิต MCC (8 จอ, ~7,500 บรรทัด)
- โมดูลสั้นกว่า core: มีเฉพาะ ทำประกัน (reqinsure + reqinsure_aero, apvinsure), เวนคืน (88→8 + อนุมัติทันที),
  รายละเอียด 8 tabs (statement/insreqchgplan/insreqtrnmemb/รูป/insreqinsuregain), และ batch 2 ตัว
- **เฉพาะ MCC:** `ws_ins_proc_apply_insloan` (ติดตั้งประกันเงินกู้: เบี้ยดึงจาก lnreqloanclrother 'INS',
  auto-approve, ประวัติ INSPROCINSUREINSTALL NEW/CHG, ส่งออกธนาคารกรุงไทย/SCBHashApp.exe)
  และ `ws_ins_proc_close_insloan` (ปิดประกันเมื่อ lncontmaster.contract_status=-1)
- ไม่มีจอ รับเบี้ย/จ่ายเวนคืน/สินไหม/เปลี่ยนแผน manual/ปิดเดือน — งานกลุ่มนี้อยู่โมดูลอื่น (ยังไม่ยืนยัน)
- iReport MCC: ir_ins_001-003, ir_ins_reqinsure_mcc, ir_coopid_rdate_insurecomp_fee_mcc ฯลฯ
- อัปเดต index.md + COVERAGE.md (hub) insurance MCC → mid

## [2026-10-08] update | ws_lc_memo (บันทึกข้อความสหกรณ์อื่นกู้) ณ rev 2383
- อัปเดต `entities/gcoop-mcc-ws-lc-memo.md` จาก rev 2051 -> 2383
- เพิ่มที่พบใหม่รอบนี้:
  - `GeneratePDF` — พิมพ์รายงานตาม `LCUCFDOCMEMOTYPE.JRXML_NAME` ของแต่ละประเภท
    ส่งพารามิเตอร์ `as_coopid` + `as_docno` + ฟิลด์ไดนามิก (postback `PostPrintPDF`)
  - `GenerateTestPDF` (postback `PostTestPDF`) สำหรับทดสอบ
  - **การประเมิน DOC_STATUS ใหม่**: hasReject -> -9, !hasPending && hasApproved -> 1, else 8
  - `AddTempPageLog` หนาแน่นทุกขั้น — ปลายทางเป็น **hidden field บนหน้าจอ**
    (`hdTempPageLog`, cap 50000 chars) ไม่ใช่ไฟล์ log + dump Request.Form ทุก key
    ที่มี `dyn_`/`appv_`/`memotype` (คอมเมนต์ในโค้ดว่า "HEAVY LOGS")
  - ยืนยัน dropdown สถานะผู้อนุมัติ 3 ค่า: 8 / 1 / -9
  - ยืนยันสีสถานะใน `DsMain.UpdateStatusColor` (เขียว/ชมพู/เหลือง/ฟ้า)
- ยืนยันว่าหน้านี้เป็นฝั่ง `lc*` (investment = สหกรณ์อื่นกู้) ไม่ใช่ `ln*` (เงินกู้สมาชิก)
