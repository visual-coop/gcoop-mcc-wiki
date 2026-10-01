---
title: ระบบประกันชีวิต MCC — insurance module
coop: mcc
created: 2026-09-29
updated: 2026-09-29
type: entity
tags: [insurance, aspnet, oracle, workflow, ireport]
confidence: high
svn_rev: 2100
analyzed_at: 2026-09-29
---

# ระบบประกันชีวิต MCC (insurance module)

ที่มา: `/root/gcoop_hermes/mcc/GCOOP/Saving/Applications/insurance/` — 8 หน้าจอ (~7,500 บรรทัด C#)
ใช้โครงสร้างตารางตระกูล `ins*` ชุดเดียวกับ base (ดู [gcoop-insurance-system](https://github.com/visual-coop/gcoop-kb-shared/blob/main/core/gcoop-insurance-system.md) ของ hub) แต่ MCC
**คัดเฉพาะวงจรที่ใช้งานจริง** = ทำประกัน (เน้นประกันเงินกู้แบบ batch), เวนคืน, ดูรายละเอียด

**หน้าจอที่มี:** `ws_ins_reqinsure` + `ws_ins_reqinsure_aero` (2 แบบ), `ws_ins_apvinsure`,
`ws_ins_reqsurrender`, `ws_ins_apvsurrender`, `ws_ins_insuredetail` (8 tabs + dlg รูป),
`ws_ins_proc_apply_insloan`, `ws_ins_proc_close_insloan`

**ที่ MCC ไม่มี (ต่างจาก core):** จอรับเบี้ย (`recvpay`/`payment_customer`), จ่ายเงินเวนคืน/สินไหม,
เปลี่ยนแผนแบบ manual, ยกเลิกกรมธรรม์ (`cclinsure`), ปิดเดือน, ประมวลรายปี — งานกลุ่มนี้น่าจะอยู่
ฝั่งการเงิน/PB หรือโมดูลอื่น (ยังไม่ยืนยันตำแหน่ง)

## ส่วนที่เหมือน base (สอบ code แล้ว)

- **ขอทำประกัน** (`ws_ins_reqinsure` / `_aero`): ค้นสมาชิก `mbmembmaster`/ครอบครัว `mbmembfamily`,
  เลือกประเภท `insinsuretype` (active), ผู้เอาประกัน `insucfinsured` (code ใช้ `'10'`/`'20'`/`'30'` — 30 = ครอบครัว ใช้ 8 จุด),
  บริษัท `insucfcompany`, แผน `insinsuretypeplan` (กรอง startloan_amt/endmember_time/use_flag/spc_flag);
  คำนวณเบี้ย 2 แบบ: **ทวีคูณ** (`coverage/plan_cover × plan_premium`) หรือ **ตามอัตรา**
  (`insinsuretyperate` ตามเพศ+อายุ → `coverage × ins_rate/1000`); ประกันเงินกู้เลือกสัญญา
  `lncontmaster` ตาม `insinsuretypeloan`; บันทึก `insreqinsure` status 8
- **อนุมัติ** (`ws_ins_apvinsure`): ดึง status 8 → อนุมัติ gen `INSINSURENO` (056001+03/06 ใช้ `INSINSURENOFINE`),
  `insurance_id` = เลขสมาชิก (insured 10/20 หรือ 057001) / หาเก่าจาก `card_person` / gen `INSDOCNO`;
  insert `insinsuremaster` (status 1) + update `insreqinsure` + ผูก `FOMIMAGEMASTER`

## จุดต่าง MCC (จาก code โดยตรง)

### 1. เวนคืน — ใช้สถานะ 88 → 8 (ไม่ใช่ 1 เหมือน core)
- `ws_ins_reqsurrender`: INSERT `insreqsurrender` **status = 88** (รออนุมัติ) + `surrender_cause_code`;
  มี **checkbox "อนุมัติทันที"** (`update_value`) → 88→8 + `insinsuremaster.insurance_status = 8`
  + เก็บ `surrender_amt` (ทำเหมือนหน้าอนุมัติทันทีในใบขอ)
- `ws_ins_apvsurrender`: เลือก docno → อนุมัติ `surrender_status` 88→8 + master → 8 + `surrender_amt`
- หมายเหตุ: MCC ไม่มีจอ "จ่ายเงินเวนคืน" ในโมดูล — การจ่ายจริง (`slslippayout` ฯลฯ) ไม่ได้อยู่ที่นี่

### 2. `ws_ins_proc_apply_insloan` — ติดตั้งประกันเงินกู้ batch **⭐ หน้าเฉพาะ MCC**
โปรเซสอัตโนมัติ: สร้าง/แก้ประกันให้กับเงินกู้ที่ออกสัญญาใหม่ (remark = "ติดตั้งประกัน")
- **กรองรายการ:** `lncontmaster` ที่ `startcont_date` ในช่วงที่เลือก, `contract_status = 1`,
  `principal_balance > 0`, `loantype_code` (default `10`) + `insuretype_code` (default `20`, ห้าม `01`),
  และยังไม่มี `insinsuremaster` สถานะ 1 ของประเภทนั้น (join `lnreqloan`/`lnreqloanclr`/`lnreqloanclrother`)
- **เบี้ย = ดึงจากคำขอกู้** — `lnreqloanclrother.clrother_amt` (clrothertype_code = `'INS'`):
  ในโค้ดมี comment "เปลี่ยนจากคำนวณเองเป็นดึงยอดจากคำขอกู้" (มีสูตรเก่าคำนวณ 0.00445 × เดือนค้าง เฉพาะใน comment)
- **กรณีสมาชิกยังไม่มีประกัน (NEW):** สร้าง `insreqinsure` **(สถานะ 1 — อนุมัติทันที ไม่ผ่านจออนุมัติ)**
  + `insinsuremaster` (สถานะ 1, `premium_payamt` = 0, `loancontract_no`, expense_code `SAL`,
  insured_code `'10'`, `insrequest_type` 2, `inspayment_type` 2 รายปี) + บันทึกประวัติ
  **`INSPROCINSUREINSTALL`** (proctype_code `NEW`, proc_type `02`)
- **กรณีมีประกันแล้ว (CHG):** สร้าง `insreqchgplan` (สถานะ 1 อนุมัติทันที, `objchgplan_type = 'LON'`,
  แผนใหม่ `01`, วงเงินใหม่ = ยอดเงินต้นคงเหลือ, บันทึก old/new contract) + `update insinsuremaster`
  (insstart_date/insureplan_code/coverage_amt/premium_amt/loancontract_no) + บันทึก `INSPROCINSUREINSTALL` (CHG)
- **ส่งออกจ่ายผ่านธนาคาร:** อ้างอิง `finexptobankhis` (batch_no/operate_date/from_system),
  ตัวเลือก Direct 02/03-Express 01/02-Standard 03/04 (ธนาคารกรุงไทย, หมายเหตุ "สอ.กฟผ."),
  มี `SCBHashApp.exe` (hash ไฟล์ส่งธนาคาร) · รายงาน async ผ่าน `cmreportprocessing`
- `INSPROCINSUREINSTALL` = ตาราง MCC-specific: `COOP_ID, REF_REQNO, OPERATE_DATE, PROCTYPE_CODE,
  MEMBER_NO, INSURANCE_NO, OLD/NEW_PLAN, OLD/NEW_COVERAGE, OLD/NEW_PREMIUM, OLD/NEW_CONTRACT, REMARK, ENTRY_*, NEW_ARREAR, PROC_TYPE`

### 3. `ws_ins_proc_close_insloan` — ปิดประกันเงินกู้ batch
- รายการจาก `insinsuremaster` (status 1) **join `lncontmaster` ที่ `contract_status = -1`** (สัญญาปิดแล้ว)
  + ชื่อ/กลุ่ม/แผน/`lastpayment_date` — ติ๊ก (all-select ได้) → save:
  `update insinsuremaster set insurance_status = -1, cancel_date, cancel_id` (insend_date อ้าง `cp_lastpay`)

### 4. `ws_ins_insuredetail` — รายละเอียดกรมธรรม์ 8 tabs (รวยกว่า core)
| Tab | DataSource (จริงจาก code) |
|---|---|
| DsMain | `insinsuremaster` + `insinsuretype` + `mbmembmaster`/`mbucfprename`/`mbucffamily`/`fomimagemaster`/`fomucfworkgroup` |
| DsPlan | `insinsurestatement` (statement กรมธรรม์) |
| DsHisplan | `insreqchgplan` (ประวัติเปลี่ยนแผน) |
| DsTrn | `insreqtrnmemb` (ประวัติโอนย้าย) |
| DsHistory | `insinsuremaster` (ประวัติสถานะ) |
| DsPicture | `insinsuremaster` + dlg `wd_ins_picturedetail` (รูป/เอกสาร) |
| DsType | ประเภทประกัน |
| DsGain | `insreqinsuregain` + `MBUCFGAINCONCERN` (เงินคืน/กำไร — ดูด้านล่าง) |

### 5. Tab เงินคืน/กำไรในใบคำขอ — `DsGain` (`ws_ins_reqinsure_ctrl`)
- อ่าน `insreqinsuregain` ตาม `insrequest_docno` + lookup `MBUCFGAINCONCERN` (`concern_code`, `gain_concern`)
- **ไม่พบ INSERT/UPDATE ของ `insreqinsuregain` ใน .cs ของ mcc/core** — น่าจะเขียนจาก process ฝั่งอื่น (PB?) — ยังไม่ยืนยัน

### 6. มีหน้าจอขอทำประกัน 2 แบบ
- `ws_ins_reqinsure_ctrl` กับ `ws_ins_reqinsure_aero_ctrl` — ใช้ตารางชุดเดียวกัน
  (diff aspx.cs ~135 บรรทัด: aero ใช้ `insinsuretypeplan` lookup แบบ active, reqinsure มี block
  "ประกันอัคคีภัย" ที่ comment ไว้) — สันนิษฐานว่าเป็นหน้าจอเก่า/ใหม่คู่กัน

## สถานะ (MCC เทียบ core)

- `insreqsurrender.surrender_status`: **88** รออนุมัติ → **8** อนุมัติแล้ว (core: จ่ายแล้ว = 1)
- `insinsuremaster.insurance_status`: 1 มีผล · **8 ขอ/อนุมัติเวนคืน** · -1 ปิด/ยกเลิก (proc_close)
- `insreqinsure.insrequest_status`: 8 รออนุมัติ (manual) · 1 อนุมัติอัตโนมัติ (proc_apply_insloan)
- `insreqchgplan.chgplan_status`: สร้าง = 1 เสมอ (auto-approve ผ่านโปรเซส)

## iReport MCC (โฟลเดอร์ `mcc/GCOOP/iReport/Reports/`)

- `ir_ins_001/002/003` — ทะเบียน/ใบคำขอ (query `ir_ins_001` ตรวจแล้ว: `insinsuremaster`
  join `insinsuretype`/`insinsuretypeplan`-desc/`mbucfprename`, แสดง `loancontract_no`/`premium_amt`)
- `ir_ins_reqinsure_mcc`, `ir_ins_insenddate_period` (ครบกำหนดสัญญา `insenddate`)
- `ir_coopid_rdate_insurecomp_fee_mcc` — ค่าธรรมเนียมบริษัทประกัน (`_2` variant)
- `ir_estfund_insure_mcc`, `ir_insinsure_conflagration_mcc`, `ir_insinsure_destroy_mcc`
  (ชื่อบ่งว่าเป็น กองทุนสำรอง / เพลิงไหม้ / เสียหาย — ยังไม่ได้อ่าน query)

## หมายเหตุ

- ค่ารายการ lookup (ประเภท/แผน/บริษัท/อัตรา/`MBUCFGAINCONCERN`) เป็น data ใน Oracle ต่อสหกรณ์
- rev ที่วิเคราะห์: 2100 (`registry.yaml` current_rev MCC)
- ยังไม่ได้เจาะ: `insurance.cs`, `DataSetIns*.Designer.cs`, หน้าจอ `ws_ins_reqinsure_aero` แบบเต็ม
- อ้างอิงข้อกำหนดโฟลเดอร์/แท็ก: [[SCHEMA]], [TAXONOMY](https://github.com/visual-coop/gcoop-kb-shared/blob/main/TAXONOMY.md) · ภาพรวมระบบ: [[gcoop-mcc]] · โมดูลอื่น: [[gcoop-mcc-web-system]]