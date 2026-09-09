---
source_url: /root/.hermes/profiles/gcoop/cache/documents/doc_525a82e8387e_วิเคราะห์ทีละหน้าจอ_mhd_loan.md
ingested: 2026-09-10
sha256: [computed during write]
---

# GCOOP Screen Analysis Template - MHD Loan System

## ภาพรวม
เอกสารเทมเพลตสำหรับการวิเคราะห์หน้าจอระบบ MHD Loan ของ GCOOP โดยเฉพาะหน้าจอ "ขอกู้" ในระบบสินเชื่อ

## โครงสร้างเทมเพลต

### การตั้งค่าเริ่มต้น
- **ชื่อระบบ:** ระบบสินเชื่อ
- **ชื่อหน้าจอ:** ขอกู้
- **Path ของหน้าจอ:** `\mhd\GCOOP\Saving\Applications\loan\ws_lon_reqloan_ctrl\`
- **PB Process Core:** `\CORE\GCOOP\PBProcess\`
- **PB Service:** `\CORE\GCOOP\PBService125\`

### เทคโนโลยีที่ใช้
- **Frontend:** ASP.NET Web Forms
- **Framework:** DataSourceTool framework
- **Database:** Oracle (ADO.NET/ODP.NET)
- **Business Logic:** PowerBuilder Service (.NET) + PowerBuilder Process (Classic)

## วิธีการวิเคราะห์

### Scope การวิเคราะห์
1. **หน้าจอหลัก:** ไฟล์ entry point (ws_*.aspx)
2. **หน้าจอย่อย:** ไฟล์ .aspx อื่นใต้ path เดียวกัน (wd_*.aspx)
3. **ไฟล์ที่เกี่ยวข้อง:** .aspx.cs, .aspx.designer.cs, .cs (BLL/DAL), DataSourceTool config, User Controls

### การอ่าน PowerBuilder Integration
- **PB Service:** ผ่าน `wcf.<Service>.<of_method>(...)`
- **PB Process Extend:** ผ่าน `WebUtil.runProcessingExtend(...)`  
- **PB Process Core:** ผ่าน `WebUtil.runProcessing(...)`

### ระบบเลขรหัส
- **ฟังก์ชัน:** หน้าจอหลัก (1,2,3...) → หน้าจอย่อย (D1-1, D1-2...) → PB Service (S1-1...) → PB Process (P1-1...)
- **Business Rules:** BR-01, BR-02... → BR-D1-01... → BR-S1-01... → BR-P1-01...

## Output Format Structure

### 1. ภาพรวมของหน้าจอ
- วัตถุประสงค์และไฟล์ต้นทาง
- หน้าจอย่อย (Dialog) ที่ใช้ทำรายการ

### 2. ลำดับขั้นตอนการทำงาน
- Numbered steps จากเปิดหน้าจอจนงานเสร็จ
- รวมขั้นตอนในหน้าจอย่อย

### 3. แผนภาพขั้นตอน (Mermaid)
- Flowchart แสดงการทำงาน
- กำกับรหัส D#, S#, P# ที่จุดเกี่ยวข้อง

### 4. คุณสมบัติและฟังก์ชัน
- รายการฟังก์ชันพร้อม source code แบบ collapsible
- เรียงตามระบบเลขรหัส

### 5. กฎทางธุรกิจและการตรวจสอบ
- Business rules พร้อม source code
- สูตรคำนวณแบบข้อความธรรมดา (ไม่ใช่ LaTeX)

### 6. สิทธิ์การใช้งาน
- Role/Permission และเงื่อนไข

### 7. จุดเชื่อมโยงกับหน้าจอ/ระบบอื่น
- แผนที่การเชื่อมต่อทั้งหมด

### 8. รายงาน/เอกสารที่สร้างได้
- รายงานที่หน้าจอสามารถสร้าง

### 9. ตารางข้อมูลที่เกี่ยวข้อง
- Database tables ที่ใช้ในแต่ละฟังก์ชัน

### 10. ข้อสังเกต / Confidence Notes
- จุดที่ไม่แน่ใจและต้องให้ business user ยืนยัน

## กฎสำคัญ

### Source Code Management
- **ห้ามแต่งโค้ดขึ้นเอง** - ต้อง copy จากไฟล์จริงเท่านั้น
- **PowerBuilder .pbl files** - ลอง extract UTF-16LE text ก่อนสรุปว่าอ่านไม่ได้
- **Quality Check** - ตรวจ signature ของฟังก์ชันให้ตรงกับที่ trace

### Documentation Rules
- ใช้โครงสร้างตายตัวตาม template
- ห้ามเปลี่ยนชื่อหัวข้อแม้จะไม่มีเนื้อหา
- ใช้ตารางจริงในหัวข้อที่กำหนด (ไม่ใช่ bullet list)
- ห้ามแสดงกระบวนการผลิตเอกสารในเนื้อหา

### PowerBuilder Integration
- อ่านเฉพาะเมื่อพบหลักฐานการเรียกจริงในโค้ด C#
- ตรวจคุณภาพเนื้อหาที่แกะได้ก่อนใช้
- ตั้ง needs_human_review: true เสมอสำหรับเนื้อหาจาก PB

## การใช้งาน Template

### เตรียมการ
1. กำหนด path ต่างๆ ในส่วนการตั้งค่า
2. ระบุไฟล์ตัวอย่าง (calibration) ถ้ามี
3. กำหนดโฟลเดอร์เก็บผลลัพธ์

### ขั้นตอนการวิเคราะห์
1. อ่านไฟล์ตาม scope ที่กำหนด
2. ติดตาม PowerBuilder integration points
3. สกัดฟังก์ชันและ business rules
4. จัดทำเอกสารตามโครงสร้าง OUTPUT FORMAT
5. ตรวจสอบตาม Self-Check Checklist

### ผลลัพธ์
- ไฟล์ .md และ .html พร้อม frontmatter
- เนื้อหาครบถ้วนตามโครงสร้าง 10 หัวข้อ
- Source code แนบแบบ collapsible
- แผนภาพ Mermaid workflow

เทมเพลตนี้ใช้สำหรับวิเคราะห์หน้าจอระบบ GCOOP อย่างเป็นระบบและสม่ำเสมอ เพื่อให้ได้เอกสาร knowledge base ที่มีคุณภาพสำหรับทีมพัฒนา