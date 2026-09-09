# Wiki Index — GCOOP Hermes

> Content catalog. ทุก wiki page listed พร้อม one-line summary
> อ่านไฟล์นี้ก่อนเสมอเพื่อหา pages ที่เกี่ยวข้อง
> Last updated: 2026-09-09 | Total pages: 16

## Entities (ระบบ/โมดูล)
- [[gcoop-overview]] — ภาพรวมระบบ GCOOP ทั้งหมด (tech stack, repo structure, กฎ AGENTS.md)
- [[gcoop-mhd]] — ระบบ MHD (ASP.NET + Oracle + PB) paths, WCF pattern, โมดูลที่วิเคราะห์แล้ว
- [[gcoop-core]] — Core infrastructure (WCF Services, PBService125, PBProcess, encoding)
- [[gcoop-loan-system]] — ระบบเงินกู้ครบ 33 หน้าจอ (6 กลุ่ม, tables, WCF, dead code, data flow)

## Concepts
- [[gcoop-loan-collateral]] — Logic การตรวจสอบค้ำประกันเงินกู้โดยละเอียด (lifecycle, risk points)

## Comparisons

## Queries
- [[loan-analysis-group1-request]] — วิเคราะห์ End-to-End กลุ่มคำร้องขอกู้ (4 หน้าจอ)
- [[loan-analysis-group2-approval]] — วิเคราะห์ End-to-End กลุ่มอนุมัติ (3 หน้าจอ)
- [[loan-analysis-group3-disbursement]] — วิเคราะห์ End-to-End กลุ่มเบิกจ่าย/รับเงิน (4 หน้าจอ)
- [[loan-analysis-group4-payment]] — วิเคราะห์ End-to-End กลุ่มชำระคืน (6 หน้าจอ)
- [[loan-analysis-group5-collateral]] — วิเคราะห์ End-to-End กลุ่มค้ำประกัน/หลักทรัพย์ (5 หน้าจอ)
- [[loan-analysis-group6-process]] — วิเคราะห์ End-to-End กลุ่มกระบวนการ/อื่นๆ (11 หน้าจอ)
- [[ระบบสินเชื่อ_Analysis_20260908]] — Master analysis ระบบสินเชื่อครบ (33 หน้าจอ, 7 sections, mermaid data-flow)

## Raw Sources
- [[core-loanservice-cs]] — LoanService.cs — C# business logic คำนวณดอกเบี้ย/ปัดเศษ
- [[core-loancollservice-cs]] — LoanCollService.cs — C# ตรวจสอบค้ำประกัน
- [[mhd-plsql-n-pk-lnnpl]] — Oracle Package n_pk_lnnpl (collateral, payment statement)
