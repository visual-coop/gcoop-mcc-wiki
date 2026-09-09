---
title: GCOOP Smart Card System
created: 2026-09-10
updated: 2026-09-10
type: entity
tags: [aspnet, smartcard, api, member]
sources: [raw/documents/core-smartcard-simulator.md]
confidence: high
---

# GCOOP Smart Card System

## ภาพรวม
ระบบบัตรสมาชิกอัจฉริยะของ GCOOP ประกอบด้วยทั้งเครื่องอ่านบัตรจริงและ Smart Card Simulator สำหรับการพัฒนาระบบ

## ส่วนประกอบหลัก

### 1. Smart Card Reader Simulator
- **ฟังก์ชัน:** จำลองเครื่องอ่านบัตรประชาชน/สมาชิกสหกรณ์
- **เทคโนโลยี:** C# WinForms Application
- **HTTP API:** รองรับการเรียกใช้จาก [[gcoop-mhd]] web application

### 2. ข้อมูลที่รองรับ
- **ข้อมูลส่วนบุคคล:** เลขบัตร, ชื่อ-นามสกุล (ไทย/อังกฤษ), เพศ, วันเกิด
- **ข้อมูลที่อยู่:** ที่อยู่เต็มรูปแบบตามบัตรประชาชน
- **รูปภาพ:** รูปหน้าจากบัตร (Base64 encoding)
- **วันที่:** วันออกบัตร, วันหมดอายุ

## การทำงาน

### Development Environment
1. เปิด Smart Card Simulator
2. ใส่ข้อมูลทดสอบ
3. ระบบ [[gcoop-mhd]] เรียก HTTP API
4. ได้รับข้อมูลจำลองกลับ

### Production Environment
- ใช้เครื่องอ่านบัตรจริงแทน Simulator
- API interface เหมือนกัน
- รองรับ Thai National ID Card standard

## เชื่อมต่อกับระบบอื่น
- **[[gcoop-member]]** - ระบบสมาชิก
- **[[gcoop-mhd]]** - Web application หลัก
- **[[gcoop-loan-system]]** - ระบบสินเชื่อ (ใช้ตรวจสอบตัวตนผู้กู้)

## Technical Details
- **HTTP Port:** Configurable (localhost)
- **Data Format:** JSON/XML response
- **Encoding:** UTF-8 with BOM
- **Framework:** .NET WinForms
- **Security:** Local network access only