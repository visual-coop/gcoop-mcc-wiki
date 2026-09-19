---
coop: mcc
title: ws_lc_memo — Dynamic Memo (MCC investment)
created: 2026-09-18
updated: 2026-09-18
type: entity
tags: [aspnet, loan, workflow, oracle, ireport]
sources: [raw/documents/mcc-svn-2051-2026-09-18.md]
confidence: high
---

# ws_lc_memo — ระบบ Dynamic Memo (MCC)

หน้าใหม่ในโมดูล **investment** ที่มาพร้อม SVN MCC revision 2051  
Path: `/root/gcoop_hermes/mcc/GCOOP/Saving/Applications/investment/ws_lc_memo_ctrl/`

ไฟล์ที่ตรวจจริง: `ws_lc_memo.aspx`, `ws_lc_memo.aspx.cs`, `DsMain.ascx.cs`, `DsApprove.ascx.cs`, `jrxml_ai_guide.md`

## หน้าที่

สร้าง/ค้นหาเอกสาร memo แบบฟิลด์ไดนามิกตามประเภทเอกสาร แล้วส่งสายอนุมัติสูงสุด 3 คน

- class `ws_lc_memo` : `PageWebSheet, WebSheet` namespace `Saving.Applications.investment.ws_lc_memo_ctrl`
- Master: `~/Frame.Master`
- JsPostBack: `PostMemoTypeChanged`, `PostSearchDoc`
- เปลี่ยนประเภทเอกสาร (`memotype_code`) → `RenderDynamicFields()`
- ปุ่มค้นหาเปิด iframe `wd_memmo_search.aspx` แล้ว `GetDocNoFromDlg` ใส่ `doc_id` + `PostSearchDoc`

## ตาราง Oracle ที่โค้ดเรียกจริง

| ตาราง | ใช้ทำ |
|---|---|
| `LCUCFDOCMEMOTYPE` | dropdown ประเภทเอกสาร (`DdMemoType`) |
| `LCUCFDOCMEMODETAIL` | โครงฟิลด์ (`FIELD_NAME`, `FIELD_LABEL`, `FIELD_TYPE`, `FIELD_SEQ`) ตาม `MEMOTYPE_CODE` |
| `LCDOCMEMOMASTER` | header: `DOC_ID`, `DOC_NO`, `DOC_DATE`, `MEMOTYPE_CODE`, `DOC_STATUS`, `ENTRY_ID`, `CURRENT_APPV_SEQ` |
| `LCDOCMEMODETAIL` | key-value `FIELD_NAME` / `FIELD_VALUE` |
| `LCDOCMEMOAPPROVE` | สายอนุมัติ `APPV_SEQ`, `APPV_USER_ID`, `APPV_REMARK` |
| sequence | `SEQ_LCDOCMEMOMASTER_DOCID.NEXTVAL` |

เลขที่เอกสารถ้าว่าง: `"MEMO" + doc_id` รูปแบบ 6 หลัก

## สถานะเอกสาร (`DOC_STATUS`) จาก `RetrieveMainAndApprove`

- `8` = รออนุมัติ (insert ใหม่ใช้ค่านี้)
- `1` = อนุมัติครบถ้วน
- `-9` = ไม่อนุมัติ

## การบันทึก (`SaveWebSheet`)

- ถ้ามี `doc_id` และ `entry_id != state.SsUsername` → อัปเดตเฉพาะ `APPV_REMARK` ของผู้อนุมัติคนนั้น แล้วจบ
- เอกสารใหม่: insert master สถานะ 8, `CURRENT_APPV_SEQ = 1`
- เอกสารเดิมของเจ้าของเรื่อง: update master แล้วลบ detail+approve แล้วใส่ใหม่
- ฟิลด์ไดนามิกอ่านจาก `Request.Form` คีย์ลงท้าย `dyn_{FIELD_NAME}`
- `FIELD_TYPE == NUMBER` ต้อง parse เป็นตัวเลขได้
- สายอนุมัติ 3 แถวจาก Repeater (`Repeater1$ctl00$appv_user_id` …)

## JRXML (จาก `jrxml_ai_guide.md` ในโฟลเดอร์เดียวกัน)

Query ตัวอย่างในไกด์ใช้ `LCDOCMEMOMASTER` LEFT JOIN `LCDOCMEMODETAIL` แล้ว PIVOT ด้วย `MAX(CASE WHEN d.FIELD_NAME = ...)`  
parameter `$P{as_docno}`  
ไกด์ระบุ JRXML เป็น UTF-8 ไม่มี BOM

## ลิงก์

- [[gcoop-mcc-web-system]]
- [[gcoop-mcc-svn-2051-ireports]]
- [[gcoop-mcc]]
