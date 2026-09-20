---
coop: mcc
svn_rev: 2085
analyzed_at: 2026-09-20
title: MCC — ปรับสัญญาเงินกู้และค้ำประกัน (SVN 2085)
created: 2026-09-20
updated: 2026-09-20
type: entity
tags: [loan, member, aspnet, powerbuilder, ireport, oracle, workflow]
sources: [raw/articles/mcc-svn-2085-2026-09-20.md]
confidence: high
---

# MCC — ปรับสัญญาเงินกู้และค้ำประกัน (SVN 2085)

หน้านี้บันทึกสถานะ **ณ rev 2085** (วิเคราะห์ 2026-09-20) ของชุดการเปลี่ยนแปลง
ที่ SVN ส่งมาในช่วง 2063 → 2085 ทั้ง 22 ไฟล์กระจุกตัวอยู่ที่
**การอนุมัติสินเชื่อ และการปรับสัญญา/ค้ำประกัน** — ดู changelog ที่
`raw/articles/mcc-svn-2085-2026-09-20.md`

## 1. หน้าอนุมัติปรับสัญญา — `ws_lon_apvpromise`

ตำแหน่ง: `GCOOP/Saving/Applications/loan/ws_lon_apvpromise_ctrl/`

### รายการที่แสดง

`CheckJsPostBack` ดึงรายการปรับสัญญาจาก `dsList` แล้วไฮไลต์ฟิลด์ที่แก้ได้

```csharp
if (eventArg == "PostChangeReq") {
    int row = dsList.GetRowFocus();
    dsList.FindTextBox(row, "contadjust_docno").BackColor = Color.Yellow;
    dsList.FindTextBox(row, "loancontract_no").BackColor = Color.Yellow;
    dsList.FindTextBox(row, "member_no").BackColor = Color.Yellow;
    dsList.FindTextBox(row, "fullname").BackColor = Color.Yellow;
    dsList.FindTextBox(row, "contadjust_date").BackColor = Color.Yellow;
    dsList.FindDropDownList(row, "adjust_status").BackColor = Color.Yellow;
    dsDetailNew.Retrieve(dsList.DATA[row].contadjust_docno);
    dsDetailOld.Retrieve(dsList.DATA[row].contadjust_docno);
}
```

- `dsDetailOld` / `dsDetailNew` = เทียบ **ค้ำประกันก่อน vs หลัง** ของเอกสารใบนั้น
- `adjust_status` เป็นตัวตัดสินว่าจะอนุมัติ (1) หรือไม่อนุมัติ (-1)

### อนุมัติ — เขียนค้ำประกันใหม่ทั้งชุด

เมื่อ `adjust_status == 1` ระบบ **ลบค้ำประกันเดิมของสัญญาทิ้ง แล้วสร้างใหม่**
จากเอกสารคำขอปรับสัญญา (ไม่ใช่แก้ทีละรายการ)

```csharp
String sql1 = @"delete from lncontcoll where coop_id = {0} and loancontract_no = {1} ";
...
string sqlInsertMain = @"insert into lncontcoll(coop_id
                                        ,loancontract_no
                                        ,seq_no
                                        ,loancolltype_code
                                        ,refcoop_id
                                        ,ref_collno
                                        ,description
                                        ,collbase_amt
                                        ,collbase_percent
                                        ,collused_amt
                                        ,collbalance_amt
                                        ,collmax_amt
                                        ,collactive_amt
                                        ,collactive_percent
                                        )  
select LNREQCONTADJUST.coop_id
                                        ,LNREQCONTADJUST.loancontract_no
                                        ,lnreqcontadjustcoll.seq_no
                                        ,trim(lnreqcontadjustcoll.loancolltype_code)
                                        ,LNREQCONTADJUST.coop_id
                                        ,lnreqcontadjustcoll.ref_collno
                                        ,lnreqcontadjustcoll.description
                                        ,lnreqcontadjustcoll.collbase_amt
                                        ,lnreqcontadjustcoll.collbase_percent
                                        ,lnreqcontadjustcoll.collused_amt
                                        ,lnreqcontadjustcoll.collbalance_amt
                                        ,lnreqcontadjustcoll.collmax_amt
                                        ,lnreqcontadjustcoll.collactive_amt
                                        ,lnreqcontadjustcoll.collactive_percent
from LNREQCONTADJUST 
left join  lnreqcontadjustcoll  on lnreqcontadjustcoll.contadjust_docno = LNREQCONTADJUST.contadjust_docno  
where LNREQCONTADJUST.contadjust_docno = {0} 
and lnreqcontadjustcoll.contadjust_type = 'NEW' ";
...
string sqlUpdate = "update LNREQCONTADJUST set contadjust_status = 1 where contadjust_docno = {0}";
```

สังเกต: `refcoop_id` ถูกเซ็ตเป็น `LNREQCONTADJUST.coop_id` (ไม่ใช่ `refcoop_id` ของรายการค้ำประกัน)
และคัดเฉพาะแถวที่ `contadjust_type = 'NEW'`

### ไม่อนุมัติ

```csharp
else if (dsList.DATA[i].adjust_status == -1)
{
    String sqlupdate = @"update LNREQCONTADJUST set contadjust_status = -1 
                         where coop_id = {0} and loancontract_no = {1} and contadjust_docno = {2}";
```

ทั้งสองทางเลือกอยู่ในลูปเดียวที่ผูกกับ `check_flag == 1` ของแถวนั้น

## 2. หน้าอนุมัติสินเชื่อ — `ws_lon_apvloan`

ตำแหน่ง: `GCOOP/Saving/Applications/loan/ws_lon_apvloan_ctrl/`

ค้นคำขอจาก `lnreqloan` ด้วย `entry_id`, `member_no`, `loantype_code`,
`loanrequest_date`, `loanrcvfix_date` แล้วบันทึกผ่าน WCF

```csharp
int result = wcf.NLoan.of_saveapv_lnreq(state.SsWsPass, str, state.SsUsername, state.SsWorkDate);
```

ออกเลขสัญญาผ่าน `wcf.NLoan.of_gennewcontractno` และกรณีพิเศษผ่าน
`wcf.NCommon.of_gennewdocnocustom` โดยมี `DsLastDoc.ascx` ประกอบหน้าจอ

## 3. Criteria สำหรับ iReport

`GCOOP/Saving/CriteriaIReport/u_cri_coopid_memno_loancont_refcollno/`

หน้าจอเลือกเงื่อนไข 3 ชั้น ให้ผู้ใช้กรองรายงานตามสัญญาและหลักประกัน

```csharp
public void DdCoopId()
{
    String sql = @"select coop_id, coop_name from cmcoopmaster ";
...
public void DdLoancont(string member_no)
{
    String sql = @"select loancontract_no,1 as sorter from lncontmaster where coop_id = {0} and member_no = {1} and principal_balance > 0
                    union select '',0 from dual order by sorter,loancontract_no";
...
public void DdRefcollno(string cont_no)
{
    String sql = @"select loancolltype_code, ref_collno, description from lncontcoll where loancontract_no = {0} ";
```

## 4. ตาราง Oracle ที่เกี่ยวข้อง

- `LNREQCONTADJUST` — เอกสารคำขอปรับสัญญา (`contadjust_docno`, `contadjust_status`)
- `lnreqcontadjustcoll` — รายการค้ำประกันของคำขอ (`contadjust_type` = `NEW`)
- `lncontcoll` — ค้ำประกันจริงของสัญญา (ถูกเขียนใหม่ตอนอนุมัติ)
- `lncontmaster` — สัญญาเงินกู้ (ใช้ `principal_balance > 0`)
- `lnreqloan` — คำขอกู้
- `cmcoopmaster` — ข้อมูลสหกรณ์

## 5. PowerBuilder

`GCOOP/PBProcess/pcmbshr.pbl` แก้ในรอบนี้ — แกะได้ 14 process:

```
CONFIRMBAL, DIVESTIMATEBFCLS, DIVPROCESSING, DIVPROCESSMEM,
LNCLOSEDAY, LNCLOSEYEAR, LNPAYPREPARE, LNPOSTTRNPAYIN, LNSHORTLONG,
PROCINTRETURN, RETIRE, SHRLONBAL, SHRPAYMENTADJ, YRDIVMETHODPAY
```

function ที่อ่านได้ชัด: `of_saveapv_trnmb` — ความเห็นในซอร์สระบุ
`อนุมัติโอนย้ายกลุ่มสมาชิก สามัญ <-> สมทบ`

> ข้อจำกัด: การแกะ `.pbl` เป็นการอ่านชื่อจากไฟล์ไบนารี บางชื่ออาจมีอักขระแปลกปน
> จากโครงสร้างไฟล์ — ใช้เป็นเบาะแส ไม่ใช่รายการที่ยืนยันครบถ้วน

`pcloan.pbd` แก้ด้วยแต่เป็นไบนารี จึงยังไม่แกะเป็นซอร์ส

## 6. รายงาน iReport

11 ไฟล์ในรอบนี้ แบ่งเป็น 3 กลุ่มตามชื่อ:

- `ir_loan_book_chg_collno_mcc` (+ `_new_resign`) — ทะเบียนเงินกู้ **เปลี่ยนเลขที่หลักประกัน**
  (`ir_loan_book_chg_collno_mcc.jasper` เป็นไฟล์เพิ่มใหม่ในรอบนี้)
- `ir_loan_book_payment_memlon_mcc_new` (+ `_subcoll`) — ทะเบียนเงินกู้รับชำระ
  (เวอร์ชันที่มีหลักประกันย่อย)
- `ir_kep_paper_debtoverdue_paycoll_mcc` / `_subcoll_mcc` — เอกสารค้างชำระ (กลุ่มค้ำประกัน)

## ความเชื่อมโยง

ชุดการเปลี่ยนแปลงนี้สอดคล้องกันทั้งวง: หน้าจอปรับสัญญา → เขียนค้ำประกันใหม่ →
criteria เลือกสัญญา/หลักประกัน → รายงานที่พิมพ์ตามเลขที่หลักประกัน
ดูภาพรวมระบบที่ [[gcoop-mcc]] และหน้าจอรับชำระที่
[[gcoop-mcc-ws-lc-npl-follow]]