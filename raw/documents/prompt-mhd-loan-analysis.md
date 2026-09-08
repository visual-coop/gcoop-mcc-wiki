# Prompt: วิเคราะห์ระบบ Loan แบบ End-to-End (สำหรับมอบหมายให้ Agent)

## ⚙️ ตั้งค่าก่อนใช้งาน — แก้ไขเฉพาะบล็อกนี้เท่านั้น ส่วนอื่นของ prompt ไม่ต้องแตะ

```

PATH_UI =\mhd\GCOOP\Saving\Applications\loan\ <-- แก้ path นี้

PATH_PBPROCESS_EXTEND = \mhd\GCOOP\PBProcess\ <-- แก้ path นี้

PATH_PBPROCESS_CORE = \CORE\GCOOP\PBProcess\ <-- แก้ path นี้

PATH_PBSERVICE = \CORE\GCOOP\PBService125\ <-- แก้ path นี้

PATH_OUTPUT = \CORE\.output\ <-- แก้ path นี้

SHARED_LIBRARY_NAMESPACE = CoreSavingLibrary <-- แก้ชื่อนี้

SYSTEM_NAME = ระบบสินเชื่อ <-- แก้ชื่อนี้

```

เนื้อหาด้านล่างทั้งหมดอ้างอิงค่าจากบล็อกนี้ด้วยชื่อตัวแปร (`PATH_UI`, `PATH_PBPROCESS_EXTEND` ฯลฯ)

ไม่มีการใส่ path ซ้ำที่อื่นอีก — แก้ที่นี่จุดเดียวพอ

---

## บทบาท

คุณคือ Senior Solution Analyst ที่ทำ Reverse-Engineering ระบบจาก source code จริง

(ASP.NET WebForms C# + PowerBuilder + SQL Server) เพื่อส่งมอบเอกสารวิเคราะห์ระบบ

ให้ทีม Business/Dev ใช้อ้างอิง ห้ามสรุปหรือเดาสิ่งที่ไม่พบในโค้ดจริง — ทุกข้อสรุปต้องอ้างอิงไฟล์/บรรทัด/ฟังก์ชันจริงที่เปิดอ่านแล้วเท่านั้น

**ห้าม spawn agent ย่อยเพื่อทำงานนี้แทนคุณ** — อ่านไฟล์และวิเคราะห์เองโดยตรงด้วย Read/Grep/Glob/Bash เท่านั้น

ถ้างานมีขนาดใหญ่เกินจะทำในรอบเดียว ให้แจ้งกลับมาขอแบ่งเป็นชุดแทนการไปเรียก Agent tool เอง

## ขอบเขต Source Code

- UI Layer: `PATH_UI`

- **ค้นหาทุกโฟลเดอร์แบบ recursive ที่ชื่อขึ้นต้นด้วย `ws_`** (ไม่ใช่แค่ pattern เดียว — ในระบบจริงอาจมีหลาย prefix ปนกัน เช่น `ws_lon_*`, `ws_loan_*` ที่หมายถึงโมดูลเดียวกัน ให้ list โฟลเดอร์ทั้งหมดก่อนแล้วดูรูปแบบจริงที่พบ อย่าเดา pattern จาก path ที่ผู้ใช้พิมพ์มาเพียงอย่างเดียว)

- โฟลเดอร์ `wd_*` ใต้ `dlg/` หรือซ้อนในโฟลเดอร์หน้าจอหลัก → จัดเป็น `child_dialog` (ดูนิยามด้านล่าง) ไม่ใช่หน้าจอหลัก

- ถ้าพบโฟลเดอร์หน้าจอที่มีไฟล์ `.aspx` มากกว่า 1 ไฟล์ (คนละ class กัน) ให้ตรวจสอบว่าเป็นหน้าจอที่ทำงานอิสระจริงกี่หน้าจอ แล้วบันทึกแยกเป็นหลายแถวในดัชนี

- ไฟล์ที่ต้องอ่านต่อหน้าจอ: `.aspx`, `.aspx.cs`, `.aspx.designer.cs`, `.ascx`, `.ascx.cs`, `.xsd` (DataSet), `.js` ที่อยู่ในโฟลเดอร์เดียวกันหรือถูก reference จาก `.aspx`

- PB Process (Customer Extension, เรียกผ่าน `WebUtil.runProcessingExtend`): `PATH_PBPROCESS_EXTEND`

- PB Process (Core, เรียกผ่าน `WebUtil.runProcessing`): `PATH_PBPROCESS_CORE`

- PB WCF Service (เรียกผ่าน `wcf.<Service>.<of_method>` — นี่คือรูปแบบ syntax ที่จะเจอในโค้ด ไม่ใช่ตัวแปรให้แทนที่): `PATH_PBSERVICE`

- ไฟล์ PB: `.pbl` (library), `.srw` (window), `.sru` (user object), `.srf` (function), `.srd` (datawindow)

- Shared bridge ฝั่ง C#: `SHARED_LIBRARY_NAMESPACE`, `WebUtil.cs`

## เป้าหมาย

ไล่ trace ทุกหน้าจอแบบ end-to-end ตาม chain:

UI Control (ASPX/ASCX/DataSet) → Client JavaScript → PostBack/AJAX Event (server-side handler ใน .cs)

→ C# Code-Behind → Shared Library / WebUtil.cs

→ PB Process Call (`runProcessingExtend`/`runProcessing`) หรือ WCF PB Service (`wcf.<Service>.<of_method>`)

→ PB Script/DataWindow (.srd/.sru/.srw/.srf ใน .pbl) → SQL Query/Stored Procedure → Database Table

ทุกขั้นต้องระบุ "ชื่อไฟล์จริง" และ "ชื่อฟังก์ชัน/event/process/service จริง" ที่พบในโค้ด

การ trace นี้ใช้เพื่อตัดสิน Confidence และเขียนหัวข้อ 4/6 — ผลลัพธ์ระดับไฟล์/ฟังก์ชันไม่ต้องบันทึกละเอียดทุกจุด

แต่ให้สรุปชื่อ process/service หลักและตารางหลักที่พบ ใส่ในหัวข้อ 2 (ดู "โครงสร้าง Output")

## กติกาจับคู่ระหว่างเลเยอร์ (Cross-Reference Rules)

1. C# → PB Process: อ่านพารามิเตอร์ชื่อ process ที่ส่งเข้า `WebUtil.runProcessingExtend("<Name>", ...)`

หรือ `WebUtil.runProcessing("<Name>", ...)` แล้วค้นหาไฟล์ที่ตรงชื่อในพาธ PBProcess ที่สอดคล้องกัน

(Extend → PATH_PBPROCESS_EXTEND, Core → PATH_PBPROCESS_CORE) โดยเปิด dispatcher object — สังเกตว่าไฟล์

dispatcher มักตั้งชื่อตามชื่อโมดูล (เช่นโมดูล loan มักมีไฟล์ `n_cst_pbprocess_loan.sru`) หาบรรทัด

`case "<Name>"` เพื่อดูว่า instantiate object ไหนและเรียก method อะไรต่อ

2. C# → WCF PB Service: จับ pattern `wcf.<ServiceName>.<of_method>(...)` แล้วค้นหา

Service/Function Object ชื่อ `<ServiceName>` ที่มี method `<of_method>` ในพาธ PATH_PBSERVICE

3. PB → SQL: เปิด DataWindow (.srd) หรือ embedded SQL ใน .srw/.sru/.srf เพื่อดึง SQL/SP/Table จริง

4. **⚠ กติกาสำคัญเรื่องไฟล์ `.pbl`**: ห้ามสรุปว่า `.pbl` "เป็น binary อ่านไม่ได้" จากผลคำสั่ง `file` เพียงอย่างเดียว

— PowerBuilder library ที่ compile แล้วมักยังฝัง **source code ต้นฉบับเป็น UTF-16LE text ไว้ภายในไฟล์ไบนารี**

(เหมือนกับที่ไฟล์ `.sru`/`.srw` ในระบบนี้เก็บเป็น UTF-16LE text) ก่อนจะสรุป Confidence = Low/Medium

เพราะ "มีแต่ .pbl" ให้ลอง extract ข้อความอ่านได้จากไฟล์ก่อนเสมอ ด้วยวิธีใดวิธีหนึ่ง:

- เขียนสคริปต์ (Python) อ่านไฟล์แบบ binary แล้วหา pattern ตัวอักษร ASCII/Thai สลับกับ byte `0x00`

(regex ประมาณ `(?:[\x20-\x7e]\x00){6,}` แล้ว decode ด้วย `utf-16le`) เพื่อดึงบล็อกข้อความที่อ่านได้ออกมา

- หรือ `grep -a -P` ด้วย pattern ตัวอักษรคั่น null byte (เช่น `o\x00f\x00_\x00...`) เพื่อค้นชื่อฟังก์ชัน/SQL ที่ต้องการ

- ค้นหาชื่อฟังก์ชัน/SQL keyword (`update `, `select `, `insert into`, ชื่อ object ที่สงสัย) ในข้อความที่ extract ได้

ถ้าลองแล้วจริงๆ ไม่พบ (ลองทั้ง generic dispatcher name และ suffix เฉพาะ) จึงค่อยสรุป Low/Medium confidence

พร้อมระบุว่า **พยายามด้วยวิธี UTF-16LE extraction แล้วไม่พบ** ไม่ใช่แค่ "เป็น binary"

5. ถ้าจับคู่ไม่ได้จริงๆ (ไม่พบไฟล์ปลายทาง / ชื่อไม่ตรง / มีหลายตัวเลือกกำกวม) ให้บันทึกเป็น "Needs Review" พร้อมเหตุผลที่ชัดเจน (ดูเกณฑ์ Needs Review ด้านล่าง)

## นิยามที่ต้องใช้ตรงกันทั้งเอกสาร

- **links_to**: หน้าจอ A เรียก/เปิดหน้าจอ B เมื่อพบ Response.Redirect, Server.Transfer,

cross-page PostBack, window.open, ASPxPopupControl/Callback ที่โหลด ascx/aspx อื่นที่เป็นหน้าจอหลัก (ขึ้นต้น `ws_`)

- **links_from**: ทิศตรงข้ามของ links_to (ใครเรียกหน้าจอนี้บ้าง)

- **child_dialog**: popup/modal (โฟลเดอร์ `wd_*` ไม่ว่าจะอยู่ใน `dlg/` ที่ใช้ร่วมกัน หรือซ้อนอยู่ในโฟลเดอร์หน้าจอหลักเอง)

ที่เปิดจากหน้าจอหลักเพื่อทำรายการย่อย ไม่นับเป็น links_to

- **Confidence**:

- High = trace ได้ครบทุกเลเยอร์ถึง SQL/Table จริง (รวมกรณี extract จาก .pbl สำเร็จ)

- Medium = trace ถึง PB Process/WCF ได้ แต่หา SQL ต้นทางไม่ได้แม้ลอง extract .pbl แล้ว

- Low = หาปลายทาง PB ไม่เจอเลย แม้ลองทั้ง dispatcher name และ .pbl extraction แล้ว

- **Needs Review = true** เมื่อ: มีความกำกวม, มี process/service/ไฟล์ dialog ชื่อคล้ายกันหลายตัว,

path อ้างอิง dialog ที่ไม่มีไฟล์จริง, มี TODO/dead code, auto-approve หรือ business logic

ที่ดูเหมือนใช้งานได้แต่จริงๆ เป็น dead code (เงื่อนไข hardcode เป็นเท็จเสมอ), หรือ logic ในโค้ดขัดแย้งกัน

## โครงสร้าง Output (เอกสารเดียวระดับระบบ วิเคราะห์ครั้งเดียวทั้งหมด)

### 1. ภาพรวมและขอบเขตของระบบ

สรุปวัตถุประสงค์รวมของหน้าจอ และรูปแบบสถาปัตยกรรมที่พบ (เช่น สัดส่วนหน้าจอที่เรียก PB Process/WCF

เทียบกับหน้าจอที่เขียน SQL ตรงจาก C# — "Direct-SQL" — ถ้าพบว่ามีหลาย pattern ปนกัน)

### 2. ดัชนีหน้าจอทั้งหมด

ตาราง: | ลำดับ | ชื่อหน้าจอ | วัตถุประสงค์สั้น | หน้าจอย่อยที่ใช้ทำรายการ (child_dialog) |

PB Process/Service ที่เรียก | ตารางหลักที่ใช้ | Confidence | Needs Review | ไฟล์อ้างอิง |

(ใส่เฉพาะชื่อไฟล์/ชื่อจริง ถ้าไม่มีใส่ "-" ห้ามเติมข้อความสถานะการอ่านโค้ด)

ถ้าหน้าจอมีจำนวนมาก (>15) ให้จัดกลุ่มเป็นหมวดตามกลุ่มธุรกรรม (เช่น คำร้อง/อนุมัติ/เบิกจ่าย/ชำระคืน/ตั้งค่า/รายงาน)

แล้วแบ่งเป็นตารางย่อยใต้หัวข้อย่อย h3 ของแต่ละกลุ่ม เพื่อให้อ่านง่าย

### 3. แผนภาพการทำงาน

```mermaid

flowchart TD

%% สร้างจาก links_to/links_from ของทุกหน้าจอ ตามนิยามด้านบน

%% ถ้าตรวจสอบแล้วไม่พบ screen-to-screen link ในโค้ดเลย (เป็นไปได้สูงถ้าหน้าจอเป็น standalone entry

%% point จากเมนู) ให้ระบุหมายเหตุนี้ไว้ชัดเจน แล้วสร้าง diagram แสดงความสัมพันธ์เชิง data-flow

%% (หน้าจอกลุ่มไหนเขียนตารางที่กลุ่มไหนอ่านต่อ) แทน โดยจัดกลุ่มเป็น subgraph ตามหมวดธุรกรรม

%% ถ้าจำนวน node > 30 ให้แบ่งเป็นหลาย subgraph ตามกลุ่มธุรกรรมเสมอ

```

### 4. กระบวนการทำงานหลักของระบบ

บรรยาย end-to-end flow ของระบบ อ้างอิง diagram หัวข้อ 3 และผลสรุปจากหัวข้อ 2 ประกอบ

(กล่าวถึงชื่อ process/service สำคัญที่พบระหว่าง trace, เส้นทางลัด/auto-approve ถ้ามี,

และสัดส่วนรูปแบบสถาปัตยกรรมที่ไม่สม่ำเสมอถ้าพบ)

### 5. ตารางข้อมูลที่ใช้

ตาราง: | ชื่อตาราง | ใช้ในหน้าจอใดบ้าง |

เน้นตารางหลัก/ที่มีนัยสำคัญเชิงธุรกิจ ถ้ามีตารางประกอบ/lookup จำนวนมาก ให้สรุปรวมเป็นกลุ่ม

(เช่น "LNLOANTYPE + ตารางลูก ~13 ตาราง") แทนการแจกแจงทุกตัว

### 6. จุดที่ต้องให้ Business User ยืนยัน (รวมทุกหน้าจอ)

ตาราง: | ชื่อหน้าจอต้นทาง | ประเด็นที่ต้องยืนยัน |

ให้ครอบคลุมอย่างน้อย: hardcode ที่ไม่ทราบที่มา (เลขวงเงิน/รหัส coop_id เฉพาะ/สถานะ),

auto-approve หรือ flow ข้ามขั้นตอนอนุมัติ, ความเสี่ยง data integrity (เช่น delete-then-insert

ไม่มี transaction ครอบ, ขาด commit/execute), บั๊กที่พบจริงในโค้ด (SQL syntax ผิด, path ผิด)

### 7. ข้อสังเกตความไม่สอดคล้องระหว่างหน้าจอ

รายการ links_to/links_from ที่จับคู่กันไม่ครบ หรือจุดอื่นที่ควรตรวจทานเพิ่ม ให้ครอบคลุม:

- รูปแบบที่เกิดซ้ำหลายจุด (ไม่ใช่แค่รายงานทีละจุด) เช่น "path อ้างอิง dialog ผิดพบ N จุด"

- หน้าจอที่ทำหน้าที่คล้ายกันแต่ implement ต่างกัน (บาง instance เรียก PB, บาง instance SQL ตรง)

- โค้ดตาย/ไฟล์ซ้ำซ้อนที่น่าสงสัยว่าเป็น legacy

- ถ้าระหว่างวิเคราะห์พบว่าข้อสรุปก่อนหน้า (เช่น ".pbl อ่านไม่ได้") อาจผิดพลาดเชิงวิธีการ

ให้บันทึกเป็นข้อสังเกตแยกต่างหากพร้อมวิธีแก้ที่ใช้ได้จริง เพื่อให้ผู้ตรวจทานคนถัดไปไม่ทำผิดซ้ำ

## รูปแบบไฟล์ Output

- 2 ไฟล์คู่กัน วางไว้ที่ `PATH_OUTPUT`: `SYSTEM_NAME_Analysis_YYYYMMDD.md` และ `.html`

- แปลง .md → .html ด้วยสคริปต์ที่ทดสอบแล้วว่า**closing code fence (```` ``` ````) ถูกตรวจจับถูกต้อง**

แยกจาก opening fence (บั๊กที่พบบ่อย: ใช้ regex เดียวกันเช็คทั้งเปิด/ปิด ทำให้เนื้อหาหลัง fence แรกหายทั้งหมด)

— ก่อนส่งมอบไฟล์ ให้ตรวจนับ heading ระดับ h2 ทั้งสองไฟล์ (`grep -c` markdown `^## ` เทียบกับ HTML `<h2`)

ต้องเท่ากับจำนวนหัวข้อจริง (7) เสมอ ถ้าไม่เท่ากันแปลว่าแปลงไม่สมบูรณ์ ห้ามส่งมอบ

- ภาษาไทยเป็นหลัก ชื่อไฟล์/ฟังก์ชัน/ตัวแปรคงภาษาอังกฤษตามต้นฉบับ

- ห้ามใส่ connection string, password, API key จริงที่พบในโค้ด — mask ด้วย `***`

## แผนการทำงาน (สำหรับผู้ปฏิบัติ/agent)

1. Discovery: list ทุกโฟลเดอร์ที่ขึ้นต้นด้วย `ws_` แบบ recursive ใต้ PATH_UI ตามกติกาในหัวข้อ "ขอบเขต Source Code"

แยกโฟลเดอร์ `wd_*` (child_dialog) ออกจากรายชื่อหน้าจอหลัก

2. ประเมินขนาด: ถ้าจำนวนหน้าจอมาก ให้เสนอแบ่งเป็นกลุ่มตามธุรกรรมก่อนเริ่มไล่โค้ดจริง (อย่าเริ่มอ่านไฟล์

ก่อนรู้ scope ทั้งหมด)

3. ต่อหน้าจอ: อ่าน .aspx/.ascx/.cs/.js ทั้งหมดในโฟลเดอร์ (รวม child_dialog ที่ซ้อนอยู่) →

ระบุ PostBack/AJAX event หลักที่ trigger process

4. ไล่ WebUtil call แต่ละจุด → หา PB process/service ที่ตรงกันตามกติกาจับคู่ข้างต้น (รวมการลอง

extract .pbl แบบ UTF-16LE ก่อนสรุป Low confidence)

5. เปิด PB object ที่พบ → หา SQL/SP/Table จริง → สรุปเป็น "PB Process/Service" และ

"ตารางหลักที่ใช้" ใส่ในหัวข้อ 2

6. รวบรวม links_to/links_from ทุกหน้าจอ → สร้างหัวข้อ 3 และ 7 (ถ้าไม่พบ link ในโค้ดเลย

ให้ระบุชัดเจนและใช้ data-flow แทนตามที่ระบุในหัวข้อ 3)

7. เขียนหัวข้อ 4 (narrative) โดยอ้างอิงผลสรุปจากหัวข้อ 2

8. จุดใดจับคู่ไม่ได้ ให้ทำเครื่องหมาย Needs Review พร้อมเหตุผล ห้ามเดาแทนการตรวจจริง

9. แปลง .md เป็น .html แล้วตรวจสอบความครบถ้วนตามเกณฑ์ในหัวข้อ "รูปแบบไฟล์ Output" ก่อนส่งมอบ