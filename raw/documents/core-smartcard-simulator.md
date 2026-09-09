---
source_url: /root/gcoop_hermes/core/SmartCardSimulator/GcoopSmartCardSimulator.cs
ingested: 2026-09-10
sha256: [computed during write]
---

# GCOOP Smart Card Reader Simulator - C# WinForms Application

## ภาพรวม
- **ภาษา:** C# WinForms Application
- **ฟังก์ชัน:** จำลองเครื่องอ่านบัตรประชาชน/บัตรสมาชิกสหกรณ์
- **HTTP Server:** รันบน localhost สำหรับให้ระบบ GCOOP เรียกใช้

## คุณสมบัติหลัก
1. **HTTP Listener Service**
   - รับ request จากระบบ GCOOP web application
   - ส่งข้อมูลบัตรประชาชนกลับเป็น JSON/XML

2. **ข้อมูลส่วนบุคคล**
   - เลขบัตรประชาชน (Card ID)
   - ข้อมูลชื่อ-นามสกุล (ไทย/อังกฤษ)
   - วันเกิด, วันออกบัตร, วันหมดอายุ
   - เพศ

3. **ข้อมูลที่อยู่**
   - บ้านเลขที่, หมู่, ซอย, ถนน
   - ตำบล, อำเภอ, จังหวัด

4. **รูปภาพ**
   - รองรับการแนบรูปภาพจำลอง
   - แปลงเป็น Base64 เพื่อส่งผ่าน HTTP

## การใช้งาน
ระบบนี้ใช้สำหรับ development/testing เพื่อจำลองการอ่านบัตรประชาชนโดยไม่ต้องมีเครื่องอ่านบัตรจริง

## Technical Details
- **Framework:** .NET WinForms
- **HTTP Port:** configurable (default localhost)
- **Data Format:** JSON response with Thai language support
- **Encoding:** UTF-8 with BOM (ตาม GCOOP rules)