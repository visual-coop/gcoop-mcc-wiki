# Wiki Index — GCOOP Hermes

> Content catalog. ทุก wiki page listed พร้อม one-line summary
> อ่านไฟล์นี้ก่อนเสมอเพื่อหา pages ที่เกี่ยวข้อง
> Last updated: 2026-09-10 | Total pages: 18

## Entities (ระบบ/โมดูล)
- [[gcoop-core]] — ระบบ Core infrastructure (IIS, deploy, tools)
- [[gcoop-database-tools]] — เครื่องมือจัดการฐานข้อมูล Oracle (Data Pump Studio)
- [[gcoop-loan-system]] — ระบบเงินกู้ทั้งหมด (C#, PB, Oracle)
- [[gcoop-mhd]] — ระบบ MHD (ระบบหลัก ASP.NET + Oracle)
- [[gcoop-overview]] — ภาพรวมระบบ GCOOP ทั้งหมด
- [[gcoop-pbprocess-systems]] — ระบบ PowerBuilder Process (Batch Jobs)
- [[gcoop-smartcard-system]] — ระบบบัตรสมาชิกอัจฉริยะ และ simulator

## Concepts
- [[gcoop-interest-calculation]] — ระบบคำนวณดอกเบี้ยสินเชื่อ (PowerBuilder + Oracle)
- [[gcoop-loan-collateral]] — Logic การตรวจสอบค้ำประกันเงินกู้โดยละเอียด
- [[gcoop-screen-analysis-methodology]] — วิธีการวิเคราะห์หน้าจอระบบ GCOOP

## Comparisons

## Queries
- [[loan-analysis-group1-request]] — วิเคราะห์ End-to-End กลุ่มคำร้องขอกู้ (4 หน้าจอ)
- [[loan-analysis-group2-approval]] — วิเคราะห์ End-to-End กลุ่มอนุมัติ (3 หน้าจอ)

- [[loan-analysis-group3-disbursement]] — วิเคราะห์ End-to-End กลุ่มเบิกจ่าย/รับเงิน (4 หน้าจอ)

- [[loan-analysis-group4-payment]] — วิเคราะห์ End-to-End กลุ่มชำระคืน (6 หน้าจอ)

- [[loan-analysis-group5-collateral]] — วิเคราะห์ End-to-End กลุ่มค้ำประกัน/หลักทรัพย์ (5 หน้าจอ)

- [[loan-analysis-group6-process]] — วิเคราะห์ End-to-End กลุ่มกระบวนการ/อื่นๆ (11 หน้าจอ)

## Raw Sources
- [[core-loanservice-cs]] — LoanService.cs — C# business logic คำนวณดอกเบี้ย/ปัดเศษ
- [[core-loancollservice-cs]] — LoanCollService.cs — C# ตรวจสอบค้ำประกัน
- [[core-oracle-datapump-studio]] — Oracle Data Pump Studio - Backup/Restore Tool
- [[core-pb-interest-service]] — PowerBuilder Interest Calculation Service
- [[core-smartcard-simulator]] — GCOOP Smart Card Reader Simulator
- [[gcoop-pbprocess-systems]] — PowerBuilder Process Systems (Batch Jobs)
- [[mhd-plsql-n-pk-lnnpl]] — Oracle Package n_pk_lnnpl (collateral, payment statement)
- [[mhd-screen-analysis-template]] — GCOOP Screen Analysis Template (MHD Loan)
