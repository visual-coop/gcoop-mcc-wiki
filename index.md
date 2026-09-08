# Wiki Index — GCOOP Hermes

> Content catalog. ทุก wiki page listed พร้อม one-line summary
> อ่านไฟล์นี้ก่อนเสมอเพื่อหา pages ที่เกี่ยวข้อง
> Last updated: 2026-09-07 | Total pages: 9

## Entities (ระบบ/โมดูล)
- [[gcoop-overview]] — ภาพรวมระบบ GCOOP ทั้งหมด
- [[gcoop-mhd]] — ระบบ MHD (ระบบหลัก ASP.NET + Oracle)
- [[gcoop-core]] — ระบบ Core infrastructure (IIS, deploy, tools)
- [[gcoop-loan-system]] — ระบบเงินกู้ทั้งหมด (C#, PB, Oracle)

## Concepts
- [[gcoop-loan-collateral]] — Logic การตรวจสอบค้ำประกันเงินกู้โดยละเอียด

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
- [[mhd-plsql-n-pk-lnnpl]] — Oracle Package n_pk_lnnpl (collateral, payment statement)
