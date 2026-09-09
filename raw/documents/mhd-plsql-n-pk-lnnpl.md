---
source_url: internal/mhd/GCOOP/PLSQL/PACKAGES/n_pk_lnnpl.pls
ingested: 2026-09-07
---

# n_pk_lnnpl — Oracle Package (ระบบเงินกู้)

## Package Spec
```sql
create or replace package n_pk_lnnpl as
  function of_getcollmast (av_coopid varchar2, av_contno varchar2) return varchar2;
  function of_getpaystmt(av_coopid varchar2, av_contno varchar2, an_return number, an_mm number, an_yyyy number) return number;
  function of_diff_year_month(ld_date1 date, ld_date2 date) return number;
  function of_diff_year_month_thai(ld_date1 date, ld_date2 date) return varchar2;
end n_pk_lnnpl;
```

## Functions
- `of_getcollmast` — ดึงข้อมูล collateral master ด้วย coop_id และ contno
- `of_getpaystmt` — ดึงข้อมูลการชำระเงิน (payment statement)
- `of_diff_year_month` — คำนวณผลต่างปี/เดือนระหว่าง 2 วันที่
- `of_diff_year_month_thai` — คำนวณผลต่างปี/เดือน format ไทย

## Oracle Packages อื่นที่เกี่ยวข้อง
- `w_sheet_ln_collredeem` — worksheet สำหรับ collateral redemption
- `n_pk_doccontrol` — document control
- `fpb` — general purpose package
