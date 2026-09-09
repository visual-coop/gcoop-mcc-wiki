---
title: ระบบ GCOOP MHD
created: 2026-09-07
updated: 2026-09-09
type: entity
tags: [architecture, aspnet, oracle, powerbuilder, wcf]
sources: []
confidence: high
---

# ระบบ GCOOP MHD

ระบบหลักของสหกรณ์ออมทรัพย์ MHD ที่รันบน ASP.NET WebForms + Oracle + PowerBuilder

## Path Structure

| Path | เนื้อหา |
|---|---|
| `mhd/GCOOP/Saving/Applications/loan/` | หน้าจอระบบสินเชื่อ (ws_lon_*, wd_lon_*, dlg/) |
| `mhd/GCOOP/Saving/Applications/` | หน้าจอระบบอื่นๆ (saving, member ฯลฯ) |
| `mhd/GCOOP/PBProcess/` | MHD-specific PB Process (pcloan.pbl เป็นต้น) |
| `mhd/GCOOP/PLSQL/` | Oracle Packages/Procedures เฉพาะ MHD |

## Shared Library

**CoreSavingLibrary** — .NET library ที่ import ใน .cs ทุกไฟล์
- namespace: `CoreSavingLibrary.WcfNLoan`, `CoreSavingLibrary.WcfNCommon`, `CoreSavingLibrary.WcfNDeposit`
- ใช้เชื่อมต่อกับ WCF Services ใน core/GCOOP/PBService125/

## การเรียก WCF Service

Pattern ใน .aspx.cs:
```csharp
NLoanClient loanService = new NLoanClient();
loanService.of_calperiodpay(wspass, ...);
```

Pattern runProcessing (Core PBProcess):
```csharp
WebUtil.runProcessing("LNPOSTTRNPAYIN", gid, ...);
```

Pattern runProcessingExtend (MHD PBProcess):
```csharp
WebUtil.runProcessingExtend("PROCSETINTRETURNMHD", gid, ...);
```

## โมดูลที่วิเคราะห์แล้ว

- **ระบบสินเชื่อ** → [[gcoop-loan-system]] — ครบ 33 หน้าจอ (วิเคราะห์ 2026-09-08)

## Related
- [[gcoop-overview]] — ภาพรวม GCOOP
- [[gcoop-core]] — Core infrastructure
- [[gcoop-loan-system]] — ระบบเงินกู้
