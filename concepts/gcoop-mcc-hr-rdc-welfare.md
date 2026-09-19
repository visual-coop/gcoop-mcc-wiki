---
coop: mcc
title: HR, RDC และ Welfare — จุดเด่นธุรกิจ MCC
created: 2026-09-14
updated: 2026-09-14
type: concept
tags: [member, architecture, workflow, saving, loan]
sources: [raw/documents/mcc-session1-web-analysis.md, raw/documents/mcc-session4-integration-summary.md]
confidence: high
---

# HR, RDC และ Welfare — จุดเด่นธุรกิจ MCC

3 โมดูลที่ทำให้ MCC ต่างจากสหกรณ์ทั่วไป (ที่เน้น loan/deposit) — รวม 2,341 ไฟล์ (35% ของระบบ)

## 1. HR Management ⭐⭐⭐ — 1,152 ไฟล์ (81 ASPX + 648 C#)

ใหญ่สุดใน MCC — ครบวงจร recruitment → retirement

| Capability | รายละเอียด |
|---|---|
| Core | Employee management, payroll, performance 360°, training & development |
| Compliance | HR audit, ระเบียบสหกรณ์ |
| Integration | เชื่อม loan/member/finance ทั้งระบบ |

เทียบ MHD: MHD ไม่มี HR ขนาดนี้ — loan เป็นหลัก (MHD loan 33 หน้าจอ)

## 2. RDC Document Management ⭐⭐⭐ — 748 ไฟล์ (57 ASPX + 392 C#)

ระบบเอกสารดิจิทัล — Paperless office ของ MCC

| Capability | รายละเอียด |
|---|---|
| Workflow | Approval & routing อัตโนมัติ, versioning, audit trail |
| Signature | Digital signature ถูกกฎหมาย |
| Tech | OCR + classification, mobile access, cloud sync, encryption & access control |

เป็น digital transformation leader — ไม่พบใน MHD ในสเกลนี้

## 3. Welfare System ⭐⭐ — 441 ไฟล์ (33 ASPX + 241 C#)

สวัสดิการสมาชิกแบบ holistic — ดูแลทั้งครอบครัว

| Capability | รายละเอียด |
|---|---|
| Health | Insurance management, ตรวจสุขภาพ |
| Education | ทุนการศึกษา, โปรแกรมพัฒนา |
| Emergency | ช่วยเหลือฉุกเฉิน 24 ชม. |
| Retirement | วางแผนเกษียณ |
| Integration | เชื่อม insurance + healthcare + member |

## Business Process Integration

```
สมาชิกใหม่ → HR Onboarding → RDC Document Setup → Welfare Registration
     ↓              ↓                  ↓                     ↓
Account Setup → Loan Eligibility → Insurance Setup → Service Access
```

```
Document Creation (RDC) → Approval Workflow → Digital Signature → Archive
         ↑                      ↓                ↓             ↓
  HR/Finance Input → Automated Routing → Audit Trail → Report (iReport)
```

## ทำไม MCC ถึงลงทุน 3 โมดูลนี้

MCC เป็นสหกรณ์ขนาดใหญ่ที่บริหารแบบองค์กรสมัยใหม่ — ไม่ใช่แค่การเงิน แต่คือ **Employee Experience + Digital Transformation + Member Care** เทียบเท่าองค์กรการเงินชั้นนำ

## Related
- [[gcoop-mcc]] — ภาพรวม MCC
- [[gcoop-mcc-web-system]] — Web modules ทั้ง 23 ตัว
- [[gcoop-mcc-integration-architecture]] — Workflow integration
- [[gcoop-mcc-vs-mhd]] — เทียบ MHD ที่ไม่มี 3 โมดูลนี้
