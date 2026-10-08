---
coop: mcc
svn_rev: 2383
analyzed_at: 2026-10-08
title: ws_lc_memo — บันทึกข้อความสหกรณ์อื่นกู้ (MCC investment)
created: 2026-09-18
updated: 2026-10-08
type: entity
tags: [loan, aspnet, oracle, workflow, ireport, screen-analysis]
sources:
  - /root/gcoop_hermes/mcc/GCOOP/Saving/Applications/investment/ws_lc_memo_ctrl/
  - raw/documents/mcc-svn-2051-2026-09-18.md
confidence: high
---

# `ws_lc_memo` — หน้าบันทึกข้อความ (Memo) ฝั่งสหกรณ์อื่นกู้

Path: `GCOOP/Saving/Applications/investment/ws_lc_memo_ctrl/`
วิเคราะห์ ณ **rev 2383** (2026-10-08) — `ws_lc_memo.aspx.cs` 942 บรรทัด
class `ws_lc_memo : PageWebSheet, WebSheet`
namespace `Saving.Applications.investment.ws_lc_memo_ctrl` · Master `~/Frame.Master`

เป็นหน้าจอ **เอกสารข้อความ** ของโมดูล investment (ฝั่ง `lc*` — สหกรณ์อื่นกู้)
คนละชุดกับระบบเงินกู้สมาชิก (`ln*`)

> หน้านี้เกิดที่ rev 2051 และถูกแก้เพิ่มใน rev 2383 — ส่วนที่เพิ่มรอบหลังคือ
> **`GeneratePDF` และ logging หนาแน่น** (ระบุไว้ในหัวข้อ 2.5 และ 5)

## 1. หลักการ: ฟิลด์ไดนามิกตามประเภทเอกสาร

หน้าจอไม่มีฟิลด์ตายตัว — **โครงฟิลด์มาจากฐานข้อมูล** ตาม `memotype_code`
เปลี่ยนประเภทเอกสารแล้วระบบสร้าง control ใหม่ทั้งชุด

```
LCUCFDOCMEMOTYPE   → ประเภทเอกสาร + ชื่อรายงาน (JRXML_NAME)
LCUCFDOCMEMODETAIL → โครงฟิลด์ของแต่ละประเภท (FIELD_NAME/LABEL/TYPE/SEQ/DEFAULT_VALUE)
```

## 2. ขั้นตอนการทำงาน

### 2.1 เปิดหน้าจอ — `WebSheetLoadBegin`

```csharp
dsMain.DATA[0].doc_date      = state.SsWorkDate;
dsMain.DATA[0].entry_id      = state.SsUsername;
dsMain.DATA[0].memotype_code = "00";        // 00 = ยังไม่เลือกประเภท
```

`InitJsPostBack()` ผูก `dsMain`, `dsApprove` และ provider ฟิลด์ไดนามิก
ถ้ามีประเภทแล้วจะเรียก `EnsureThreeApproverRows()` + `RenderDynamicFields()`

**บน PostBack ทุกครั้ง** โค้ดบังคับ re-render เพื่อให้ ASP.NET เก็บ state
(ถ้าค่า `memotype_code` ยังไม่ถูก map จาก framework จะดึงจาก `Request.Form`
โดยหาคีย์ที่ลงท้าย `memotype_code` หรือมี `memotype_code_0`)

### 2.2 เปลี่ยนประเภทเอกสาร — `PostMemoTypeChanged`

```csharp
if (eventArg == "PostMemoTypeChanged")
{
    RenderDynamicFields();      // สร้าง control ตาม LCUCFDOCMEMODETAIL
    EnsureThreeApproverRows();  // เตรียมแถวอนุมัติ 3 แถว
}
```

### 2.3 ค้นหาเอกสารเดิม — `PostSearchDoc`

เปิด iframe `wd_memmo_search.aspx` → ส่ง `doc_id` กลับ (`GetDocNoFromDlg`)

```csharp
dsMain.ResetRow();
dsMain.DATA.Rows[0]["doc_date"]      = state.SsWorkDate;
dsMain.DATA.Rows[0]["entry_id"]      = state.SsUsername;
dsMain.DATA.Rows[0]["memotype_code"] = "00";
dsApprove.DATA.Rows.Clear();
EnsureThreeApproverRows();
PanelDynamicFields.Controls.Clear();     // ล้าง control เก่าก่อนเสมอ
dsMain.DATA.Rows[0]["doc_id"] = doc_id;  // คืนค่า แล้วดึงใหม่
RetrieveMainAndApprove();
RenderDynamicFields();
```

> คอมเมนต์ `[!REMARK]` ในโค้ดระบุว่าแก้เคส "กดค้นหาแล้วกดอีก" — ต้อง clear ก่อน

### 2.4 บันทึก — `SaveWebSheet` (แยก 2 กรณี)

ทั้งหมดอยู่ใน transaction เดียว (`Sta ta = new Sta(state.SsConnectionString)`,
`ta.Transection()` → `ta.Commit()` / `ta.RollBack()`)

**กรณีที่ 1 — ผู้อนุมัติเข้ามาใส่ความเห็น** (`doc_id > 0 && entry_id != state.SsUsername`)

```csharp
string sqlUpdRemark = "UPDATE LCDOCMEMOAPPROVE SET APPV_REMARK = {0}, APPV_STATUS = {1} WHERE DOC_ID = {2} AND APPV_USER_ID = {3}";
```

แล้ว **ประเมินสถานะ master ใหม่** จากสถานะผู้อนุมัติทุกคน:

```csharp
if (status == -9 || status == -1)    hasReject   = true;
else if (status == 8)                hasPending  = true;
else if (status == 1)                hasApproved = true;

decimal finalDocStatus = 8;
if (hasReject)                        finalDocStatus = -9;  // ต้องแก้ไข
else if (!hasPending && hasApproved)  finalDocStatus = 1;   // อนุมัติครบทุกคน
else                                  finalDocStatus = 8;   // รออนุมัติ/ยังไม่ครบ
```

**กรณีที่ 2 — ผู้สร้างเอกสาร**

ตรวจก่อนว่าถ้ากรอกผิดให้ throw:
```csharp
if (string.IsNullOrEmpty(memotype_code) || memotype_code == "00")
    throw new Exception("กรุณาระบุประเภทเอกสาร");
```

เอกสารใหม่:
```sql
doc_id = SEQ_LCDOCMEMOMASTER_DOCID.NEXTVAL
doc_no = "MEMO" + doc_id.ToString("000000")        -- ถ้าว่าง

INSERT INTO LCDOCMEMOMASTER (DOC_ID, DOC_NO, DOC_DATE, MEMOTYPE_CODE, DOC_STATUS, ENTRY_ID, CURRENT_APPV_SEQ)
VALUES ({0}, {1}, {2}, {3}, 8, {4}, 1)             -- DOC_STATUS = 8, APPV_SEQ = 1
```

เอกสารเดิม → **ลบของเก่าแล้วใส่ใหม่ทั้งชุด**:
```sql
UPDATE LCDOCMEMOMASTER SET DOC_NO=?, DOC_DATE=?, MEMOTYPE_CODE=?, DOC_STATUS=8 WHERE DOC_ID=?
DELETE FROM LCDOCMEMODETAIL  WHERE DOC_ID = ?
DELETE FROM LCDOCMEMOAPPROVE WHERE DOC_ID = ?
```

insert ฟิลด์ไดนามิกทุกตัว:
```csharp
INSERT INTO LCDOCMEMODETAIL (DOC_ID, FIELD_NAME, FIELD_VALUE) VALUES ({0}, {1}, {2})
```
- ค่าอ่านจาก `Request.Form` โดยหาคีย์ที่ลงท้าย `dyn_<FIELD_NAME>`
- ถ้า `FIELD_TYPE == "NUMBER"` → ตัด comma แล้ว parse เป็น decimal
  ถ้า parse ไม่ได้ → `throw new Exception("ข้อมูลฟิลด์ " + fieldLabel + " ต้องเป็นตัวเลขเท่านั้น")`
- ถ้า checkbox `chkdef_<FIELD_NAME> == "1"` → บันทึกเป็น default ของประเภทนั้น
  ```sql
  UPDATE LCUCFDOCMEMODETAIL SET DEFAULT_VALUE = ? WHERE MEMOTYPE_CODE = ? AND FIELD_NAME = ?
  ```
  (ล้มเหลวแค่ log WARN ไม่ล้ม transaction)

insert สายอนุมัติ:
```csharp
INSERT INTO LCDOCMEMOAPPROVE (DOC_ID, APPV_SEQ, APPV_USER_ID, APPV_STATUS, APPV_REMARK) VALUES (...)
```
`APPV_SEQ` ใช้ตัวนับ `actual_seq` ที่เดินเฉพาะแถวที่มี `appv_user_id` (ไม่ใช้ค่าจาก form)

หลัง commit สำเร็จ → **ล้างหน้าจอ** และแจ้ง `บันทึกข้อมูลสำเร็จ (Doc No: MEMOxxxxxx)`

### 2.5 พิมพ์ PDF — `GeneratePDF` (ใหม่ในรอบนี้)

```csharp
if (string.IsNullOrEmpty(doc_no)) { แจ้ง "กรุณาบันทึกเอกสารก่อนพิมพ์"; return; }

SELECT MEMOTYPE_DESC, JRXML_NAME FROM LCUCFDOCMEMOTYPE WHERE MEMOTYPE_CODE = ?

iReportArgument args = new iReportArgument();
args.Add("as_coopid", iReportArgumentType.String, state.SsCoopControl);
args.Add("as_docno",  iReportArgumentType.String, doc_no);
```

**รายงานผูกกับประเภทเอกสาร** — แต่ละ `MEMOTYPE_CODE` มี `JRXML_NAME` ของตัวเอง
ตัวแปรที่ส่งเข้า report: `as_coopid`, `as_docno` + ฟิลด์ไดนามิกของประเภทนั้น
ถ้า `JRXML_NAME` ว่าง → แจ้ง "กรุณาลองใหม่อีกครั้งภายหลัง"

เรียกผ่าน postback `PostPrintPDF` (มี `PostTestPDF` → `GenerateTestPDF` สำหรับทดสอบ)

## 3. สถานะ

**ผู้อนุมัติแต่ละคน — `LCDOCMEMOAPPROVE.APPV_STATUS`** (dropdown `ddlAppvStatus`):
```
8   รออนุมัติ
1   อนุมัติ
-9  ไม่อนุมัติ
```

**เอกสาร — `LCDOCMEMOMASTER.DOC_STATUS`**:
```
8   รออนุมัติ / อนุมัติยังไม่ครบ
1   อนุมัติครบทุกคนแล้ว
-9  ต้องแก้ไข (มีผู้อนุมัติไม่อนุมัติ)
```

แสดงบนหน้าจอพร้อมสี (`DsMain.UpdateStatusColor`):

| ข้อความ | สี |
|---|---|
| อนุมัติครบทุกคนแล้ว | LightGreen |
| ต้องแก้ไข | LightPink |
| อนุมัติยังไม่ครบ | LightYellow |
| รออนุมัติ | LightSkyBlue |

## 4. สายอนุมัติ — บังคับ 3 แถว

`EnsureThreeApproverRows()` เตรียม 3 แถวเสมอ และ `SaveWebSheet` วน
`for (int i = 0; i < 3; i++)` อ่านจาก form ด้วย key:
```
Repeater1$ctl{00,01,02}$appv_seq
Repeater1$ctl{00,01,02}$appv_user_id
Repeater1$ctl{00,01,02}$appv_remark
Repeater1$ctl{00,01,02}$appv_status
```

> คอมเมนต์ในโค้ดระบุชัด: **ผู้สร้างอัปเดตเอกสาร บังคับให้สถานะผู้อนุมัติกลับเป็น 8
> (รออนุมัติ) ทั้งหมด** — ตรงกับ `DELETE ... APPROVE` + insert ใหม่ข้างบน

## 5. Logging — เขียนลง hidden field ไม่ใช่ไฟล์

รอบนี้เพิ่ม log หนาแน่น (`AddTempPageLog` เรียกในทุกขั้น) แต่ **ปลายทางคือ
hidden field บนหน้าจอ**:

```csharp
public void AddTempPageLog(string level, string message)
{
    string currentLog = hdTempPageLog.Value ?? string.Empty;
    string line = string.Format("[{0:HH:mm:ss.fff}] [{1}] {2}{3}",
                                DateTime.Now, level, message, Environment.NewLine);
    string nextLog = currentLog + line;
    if (nextLog.Length > TempPageLogMaxLength)          // 50000
        nextLog = nextLog.Substring(nextLog.Length - TempPageLogMaxLength);
    hdTempPageLog.Value = nextLog;
}
```

- `TempPageLogMaxLength = 50000` — เกินแล้วตัดจากด้านหน้า (เก็บของใหม่)
- `hdTempPageLog` เป็น `asp:HiddenField` และมี JS ฝั่งหน้าจออ่านออกมาแสดง
- `AddTempPageException(ex)` = log ระดับ ERROR
- `SaveWebSheet` dump `Request.Form` ทุก key ที่มี `dyn_`, `appv_`, `memotype`
  ลง log ด้วย (คอมเมนต์ในโค้ดเขียนว่า "HEAVY LOGS")

**ข้อสังเกต:** เป็น logging สำหรับ debug ที่ยังอยู่ในโปรดักชัน และเขียนลง
response ของผู้ใช้ทุกครั้ง — ถ้าต้องการ log ถาวรต้องเปลี่ยนปลายทาง

## 6. ตาราง Oracle ทั้งหมด

| ตาราง | บทบาท |
|---|---|
| `LCUCFDOCMEMOTYPE` | ประเภทเอกสาร (`MEMOTYPE_CODE`, `MEMOTYPE_DESC`) + ชื่อรายงาน `JRXML_NAME` |
| `LCUCFDOCMEMODETAIL` | โครงฟิลด์ (`FIELD_NAME`, `FIELD_LABEL`, `FIELD_TYPE`, `FIELD_SEQ`, `DEFAULT_VALUE`) ตาม `MEMOTYPE_CODE` |
| `LCDOCMEMOMASTER` | header: `DOC_ID`, `DOC_NO`, `DOC_DATE`, `MEMOTYPE_CODE`, `DOC_STATUS`, `ENTRY_ID`, `CURRENT_APPV_SEQ` |
| `LCDOCMEMODETAIL` | key–value `FIELD_NAME` / `FIELD_VALUE` |
| `LCDOCMEMOAPPROVE` | สายอนุมัติ `APPV_SEQ`, `APPV_USER_ID`, `APPV_STATUS`, `APPV_REMARK` |
| sequence | `SEQ_LCDOCMEMOMASTER_DOCID` |

## 7. ไฟล์ในโฟลเดอร์

```
ws_lc_memo.aspx / .aspx.cs / .designer.cs     หน้าจอหลัก (942 บรรทัด)
DsMain.ascx / .ascx.cs                        header + สถานะ + สี
DsApprove.ascx / .ascx.cs                     สายอนุมัติ 3 แถว + dropdown สถานะ
jrxml_ai_guide.md                             คู่มือเขียน jrxml สำหรับ memo
```

`DsMain.InitDsMain` สร้าง DataTable เอง (ไม่ผูก table) ด้วยคอลัมน์:
`doc_id`, `doc_no`, `doc_date`, `memotype_code`, `doc_status_desc`, `entry_id`

## 8. JRXML (จาก `jrxml_ai_guide.md` ในโฟลเดอร์เดียวกัน)

Query ตัวอย่างในไกด์ใช้ `LCDOCMEMOMASTER` LEFT JOIN `LCDOCMEMODETAIL` แล้ว PIVOT ด้วย
`MAX(CASE WHEN d.FIELD_NAME = ...)` — parameter `$P{as_docno}`
ไกด์ระบุ JRXML เป็น UTF-8 ไม่มี BOM

## ความเชื่อมโยง

- ระบบสหกรณ์อื่นกู้ (ภาพรวม `lc*`): [gcoop-lc-loan-coopother](https://github.com/visual-coop/gcoop-kb-shared/blob/main/core/gcoop-lc-loan-coopother.md)
- หน้าจอ investment อื่น: `ws_lon_reqlncoopother_ctrl`, `ws_lon_lcprocesspayment_ctrl`,
  `ws_lc_collmast_ctrl`, `ws_lc_npl_follow_ctrl`, `ws_lon_auditloan_ctrl`
- [[gcoop-mcc-web-system]] · [[gcoop-mcc-svn-2051-ireports]] · [[gcoop-mcc-loan-contadjust]]
  · [[gcoop-mcc]]