---
coop: mcc
svn_rev: 2051
title: MCC PBProcess .pbl dispatchers (SVN 2051)
created: 2026-09-18
updated: 2026-09-18
type: entity
tags: [powerbuilder, loan, dividend, finance, workflow]
sources: [raw/documents/mcc-svn-2051-2026-09-18.md]
confidence: medium
---

# MCC PBProcess — dispatcher จาก binary `.pbl` (SVN 2051)

ไฟล์เหล่านี้เป็น **compiled binary** (`HDR* PowerB`) ไม่ใช่โฟลเดอร์ `.sru`  
แกะด้วย UTF-16LE ตาม skill `powerbuilder-reverse-engineering` — ได้ชื่อ process ที่ฝังใน dispatcher ไม่ใช่ซอร์สเต็ม

Path: `/root/gcoop_hermes/mcc/GCOOP/PBProcess/`  
SVN เช้า 18 ก.ย. อัปเดต: `pcloan.pbl`, `pcfinance.pbl`, `pcmbshr.pbl`, `pcdivavg.pbl` (+ `.pbd` / `pbprocess.exe` ไม่แกะ)

## pcloan.pbl (9.0 MB)

process: `CONFIRMBAL`, `DPTRANDEPT`, `LNCLOSEDAY`, `LNPAYPREPARE`, `LNPOSTTRNPAYIN`, `LNSHORTLONG`, `PROCINTRETURN`, `PROCSETARRMTHNPL`, `SHRLONBAL`, `SHRLONBAL_OLE`

function ที่อ่านได้บางส่วน: `of_proc_lnprepare`, `of_proc_lnprepare_cal`, `of_proc_lnprepare_clr`, `of_setsrvlninterest`, `of_gen_lntype`

## pcfinance.pbl (8.6 MB)

process: `POSTTOFIN`, `LNPOSTTRNPAYIN`

function ตัวอย่าง: `of_getmembername`, `of_defaultaccid`, `of_getattribconstant`, `of_importstring_xml`

## pcmbshr.pbl (0.7 MB)

process: `CONFIRMBAL`, `DIVESTIMATEBFCLS`, `DIVPROCESSING`, `DIVPROCESSMEM`, `LNCLOSEDAY`, `LNCLOSEYEAR`, `LNPAYPREPARE`, `LNPOSTTRNPAYIN`, `LNSHORTLONG`, `PROCINTRETURN`, `RETIRE`, `SHRLONBAL`, `SHRPAYMENTADJ`, `YRDIVMETHODPAY`

## pcdivavg.pbl (3.9 MB)

process: `DIVESTIMATE`, `DIVPREPAREPAY`, `DIVPROCDIVPAY`, `DIVPROCESSING`, `DIVPROCESSMEM`, `LNPAYPREPARE`, `LNPOSTTRNPAYIN`, `YRDIVMETHODPAY`

function ตัวอย่าง: `of_prc_methpay_opt`, `of_prc_methpay_chk`, `of_prc_methpay_clr`, `of_post_methpay`

หมายเหตุจากสกิล: ชื่อ process เดียวกันอาจอยู่ในหลาย `.pbl` — `LNPAYPREPARE` / `LNPOSTTRNPAYIN` โผล่ทั้ง pcloan, pcmbshr, pcdivavg

## ลิงก์

- [[gcoop-mcc-powerbuilder]]
- [[gcoop-mcc-svn-2051-ireports]]
- [[gcoop-mcc]]
