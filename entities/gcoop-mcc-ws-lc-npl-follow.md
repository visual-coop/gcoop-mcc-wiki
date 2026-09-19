---
coop: mcc
title: ws_lc_npl_follow — ติดตาม NPL (MCC)
created: 2026-09-18
updated: 2026-09-18
type: entity
tags: [aspnet, loan, workflow, oracle]
sources: [raw/documents/mcc-svn-2051-2026-09-18.md]
confidence: high
---

# ws_lc_npl_follow — ติดตามลูกหนี้ NPL (MCC)

Path: `/root/gcoop_hermes/mcc/GCOOP/Saving/Applications/investment/ws_lc_npl_follow_ctrl/`  
SVN 2051 อัปเดต `ws_lc_npl_follow.aspx/.cs` และ `DsMain.ascx/.cs`

ตรวจจาก `ws_lc_npl_follow.aspx.cs` และ `DsMain.ascx.cs` เท่านั้น

## พฤติกรรมหน้าจอ

class `ws_lc_npl_follow` : `PageWebSheet, WebSheet`

JsPostBack: `PostMemberNo`, `PostLoanContract`, `PostFollowSeq`, `PostNewFollowSeq`, `PostAddRow`, `PostDelRow`

ชุดข้อมูล: `dsMain`, `dsNplMaster`, `dsNplDetail`

- กรอกเลขสมาชิก → `WebUtil.MemberNoFormat` → `RetrieveMain` จาก `lcmembmaster` → dropdown สัญญา `lccontmaster`
- เลือกสัญญา → `RetrieveContractData`: ยอดคงเหลือจาก `lcclsmthbalance` (งวดล่าสุด) ถ้ารไม่มีใช้ `cm.principal_balance` / `interest_arrear`
- สถานะกฎหมาย `lcucfcontlaw`, ประเภทสงสัยจะสูญ `lcucfdoubtacctype`
- ถ้ามี follow seq เดิม → `BindFollowSeqBoxes` และปุ่มสร้าง seq ใหม่

## ลิงก์

- [[gcoop-mcc-ws-lc-memo]]
- [[gcoop-mcc-web-system]]
- [[gcoop-mcc]]
