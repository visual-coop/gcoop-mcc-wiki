# MCC Session 1: Web Interface Analysis (ปรับปรุงใหม่)

**วันที่วิเคราะห์:** 2026-09-14  
**โปรเจค:** MCC (Member Credit Cooperative) System  
**Path:** `/root/gcoop_hermes/mcc/GCOOP/Saving/`  
**Focus:** การวิเคราะห์ ASP.NET Web Application ระบบสหกรณ์ออมทรัพย์ MCC

---

## Executive Summary

ระบบ Web MCC เป็น ASP.NET Web Application (.NET 4.0) ที่ประกอบด้วย **24 โมดูล** ใน `/Applications/` โดยมี 16 โมดูลที่มีหน้า .aspx จริง รวม **436 ไฟล์ .aspx** สถาปัตยกรรมใช้รูปแบบ WebSheet (PageWebSheet base class + JsPostBack attributes) เรียก WCF Services ผ่าน `wcf.NLoan`, `wcf.NCommon` ฯลฯ และเชื่อมต่อ Oracle Database ผ่าน System.Data.OracleClient โมดูลเด่นที่สุดคือ **hr (81 aspx)**, **rdc (57 aspx)**, **loan (38 aspx)** และ **walfare (33 aspx)**

---

## 1. โครงสร้าง Web Application

### 1.1 Root Directory — `/root/gcoop_hermes/mcc/GCOOP/Saving/`

| ไฟล์/โฟลเดอร์ | ประเภท | คำอธิบาย |
|---|---|---|
| `Applications/` | โฟลเดอร์ | โมดูลทั้งหมด (24 โมดูล) |
| `Frame.Master` | Master Page | กรอบหลักแอปพลิเคชัน |
| `FrameDialog.Master` | Master Page | กรอบสำหรับ popup/dialog |
| `Report.Master` | Master Page | กรอบสำหรับรายงาน |
| `Default.aspx` | หน้าแรก | Login/entry point |
| `ChangePass.aspx` | หน้าเปลี่ยนรหัสผ่าน | |
| `ApplicationSelectionPage.aspx` | เลือกแอปพลิเคชัน | |
| `Logout.aspx` | ออกจากระบบ | |
| `dbms.aspx` | หน้า DB Management | |
| `Web.config` | การตั้งค่า | Oracle connections + WCF endpoints |
| `WcfCallingExtend.cs` | WCF helper | Factory สำหรับสร้าง WCF clients |
| `CriteriaIReport/` | iReport schemas | 117 ไฟล์ criteria (XSD/XSS/XSC) |
| `DataWindow/` | DataWindow definitions | 27 โฟลเดอร์ย่อยตามโมดูล |
| `Settings.cs` | App settings | |
| `CustomControl/` | Custom controls | |
| `CmPage/` | Common pages | |
| `IREPORTFILE/` | ไฟล์ iReport | |
| `SlipApp/`, `SlipPB/` | Slip printing | |
| `ajaxpro/` | AjaxPro library | |
| `Css/`, `JsCss/`, `js/` | Front-end assets | |
| `Image/`, `ImageMember/` | รูปภาพสมาชิก | |

### 1.2 Master Pages

**Frame.Master.cs** — กรอบหลัก
```
Namespace: Saving
Class: Frame : System.Web.UI.MasterPage
```
- จัดการ `XmlConfigService` สำหรับ XMLConfig system
- เรียก `WebUtil.StartIreportBuilder()` ทุก request (restart iReport ถ้า `r=1`)
- ติดตาม session ผ่าน `WebStateFactory` (state.SsTokenId, state.SsUsername, state.SsWorkDate, state.SsCoopControl, state.SsCoopId)
- `WcfCalling wcf` — factory สำหรับเรียก WCF services
- ส่ง `FrameContext` = `t=<tokenId>&d=<encryptedConnectionString>` ไปยังทุกหน้า
- ใช้ `EncryptDecryptEngine` เพื่อ encrypt connection string ใน URL
- มี `selectedPrinter`, `downloadPatch`, `isConfirmOnNew` properties

**FrameDialog.Master** — กรอบ popup dialog (ใช้กับหน้า `wd_*` prefix)

---

## 2. โมดูลทั้งหมดใน Applications/

### 2.1 ตาราง Module Overview

| โมดูล | .aspx Files | คำอธิบาย |
|---|---|---|
| **hr** | 81 | Human Resources Management |
| **rdc** | 57 | Records/Document Management |
| **loan** | 38 | สินเชื่อและเงินกู้ |
| **mbshr** | 35 | สมาชิกและหุ้น |
| **walfare** | 33 | สวัสดิการ (welfare) |
| **finance** | 23 | การเงินและการยืม-คืนเงิน |
| **pm** | 21 | Portfolio Management / ลงทุน |
| **brw** | 19 | Borrowing / กู้ยืมระหว่างสหกรณ์ |
| **durinvt** | 18 | ดุริยางค์/ทรัพย์สินระยะยาว |
| **ixp** | 12 | Interface/Export-Import |
| **assist** | 11 | เงินช่วยเหลือ (สมาชิก) |
| **insurance** | 9 | ประกันภัย |
| **investment** | 6 | การลงทุน |
| **keeping** | 7 | เงินฝากประจำ (keeping/สะสมทรัพย์) |
| **fom** | 3 | Form/workflow management |
| **account** | 3 | บัญชีงบประมาณ |
| **admin** | 0 | Admin (ไม่มี .aspx — มีไฟล์อื่น) |
| **agency** | 0 | หน่วยงาน |
| **arc** | 0 | Archive |
| **budget** | 0 | งบประมาณ |
| **divavg** | 0 | เฉลี่ยคืน (Dividend) |
| **mis** | 0 | Management Information |
| **trading** | 0 | การค้า |
| **rdc** | 57 | Document Management |

**รวม ASPX ทั้งหมด:** 436 ไฟล์ (นับจากโมดูลที่มีไฟล์จริง)

### 2.2 DataWindow Subdirectories (27 โฟลเดอร์)
`Cmd`, `Common`, `Criteria`, `Shrlon`, `account`, `admin`, `agency`, `ap_deposit`, `app_assist`, `app_finance`, `arc`, `budget`, `divavg`, `etp_icc`, `hr`, `insurance`, `investment`, `keeping`, `keepingmonth`, `lawsys`, `loanassist`, `loantracking`, `mbshr`, `mis`, `pm`, `shrlonclsproc`, `trading`

---

## 3. โมดูล Loan — สินเชื่อและเงินกู้ (38 หน้า)

### 3.1 รายการหน้าสำคัญ

| ชื่อ Screen | ประเภท | คำอธิบาย |
|---|---|---|
| `ws_lon_reqloan` | WebSheet | **ยื่นคำขอกู้** — หน้าหลักการขอสินเชื่อ |
| `ws_lon_apvloan` | WebSheet | **อนุมัติสินเชื่อ** — รายการรออนุมัติ + ออกเลขสัญญา |
| `ws_lon_payment` | WebSheet | **ชำระเงินกู้** — รับชำระ/ตัดเงินเดือน |
| `ws_lon_rcvloan` | WebSheet | รับเงินกู้ |
| `ws_lon_estpayment` | WebSheet | ประมาณการชำระ |
| `ws_lon_cfintrate` | WebSheet | กำหนดอัตราดอกเบี้ย |
| `ws_lon_cfloantype` | WebSheet | กำหนดประเภทเงินกู้ |
| `ws_lon_collateral_master` | WebSheet | จัดการหลักประกัน |
| `ws_lon_collateral_car` | WebSheet | หลักประกันยานพาหนะ |
| `ws_lon_ucfloancreditscoring` | WebSheet | Credit Scoring กำหนดค่า |
| `ws_elc_score_committee` | WebSheet | คะแนนคณะกรรมการ |
| `ws_lon_apvpromise` | WebSheet | อนุมัติ/บันทึกสัญญา |
| `ws_lon_apvreqpauseloan` | WebSheet | อนุมัติขอพักชำระหนี้ |
| `ws_lon_reqpauseloan` | WebSheet | ยื่นขอพักชำระหนี้ |
| `ws_lon_paintloan` | WebSheet | ดูยอดเงินกู้ |
| `ws_lon_proc_moneyreturn` | WebSheet | ประมวลผลคืนเงิน |
| `ws_lon_proc_paymoneyreturn` | WebSheet | จ่ายคืนเงิน |
| `ws_lon_reqchg_loancoll` | WebSheet | ขอเปลี่ยนหลักประกัน |
| `ws_lon_reqloanext` | WebSheet | ขอกู้เพิ่มเติม |
| `ws_lon_contestpayment` | WebSheet | แข่งขันการชำระ |
| `ws_lon_post_loan_receive` | WebSheet | Post รับสินเชื่อ |
| `ws_lon_add_commitee` | WebSheet | เพิ่มคณะกรรมการ |

#### Dialog หน้า loan:
`wd_lon_openloanreqpre`, `wd_lon_rcvlon`, `wd_lon_rightcoll`, `wd_lon_search_conadjust`, `wd_lon_search_payetc`, `wd_lon_search_promise`, `wd_lon_securities`, `wd_mem_search`, `wd_stm_loan`

### 3.2 Business Logic Highlights — Loan

#### ws_lon_reqloan.aspx.cs
- **Base class:** `PageWebSheet, WebSheet` — pattern WebSheet ทุกหน้า
- **Using:** `CoreSavingLibrary.WcfNLoan` — WCF client สำหรับ loan service
- **Data sets:** `dsMain`, `dsDetail`, `dsLoanClr`, `dsLoanColl`, `dsLoanClrOth`, `dsLoanIns`, `dsKeepData`, `dsLoanCreditScoll`, `dsLoanExpense`, `dsLoanChkDoc` — ครอบคลุมข้อมูลสินเชื่อทั้งหมด
- **JsPostBack events:** ReCalSalary, CalPayMonth, ReCalPeriodPay, ReCalPaymentType, RefCollno, InsertRowColl, ChgInsPlan, Expense, ReCalLoanReAmtKpshr, CalOthMemb, PrintReq, PrintScoll, InsertRowLonExpense, CalMaxPeriodKBKH, ChgRcvfixdate, calloanpermiss, Collpermissman, CalpremiumIns
- **PostBegin():** ตั้งค่า LOANREQUEST_DOCNO = "AUTO", set working date, DdLoantype dropdown, reset DataSets
- **SaveWebSheet():** `ExecuteDataSource exe` — บันทึกข้อมูลผ่าน Oracle

#### ws_lon_apvloan.aspx.cs
- **WCF Call:** `loanService = wcf.NLoan` (n_loanClient)
- **GenContNo():** เรียก `wcf.NLoan.of_gennewcontractno(state.SsWsPass, req_coopid, loantype_code)` — ออกเลขสัญญากู้ผ่าน WCF
- **SQL Mode Detection:** `Sta.IS_OLEDB_MODE` — รองรับทั้ง Oracle (`to_date()`) และ SQL Server (`convert(datetime)`)
- **SQL Filter:** filter by entry_id, member_no, loantype_code, date range (loanrequest_date, loanrcvfix_date)
- **CoopId-specific logic:** `state.SsCoopControl == "003001" || state.SsCoopControl == "074001"` → order_type = "3" (MCC มีหลายสาขา)
- **PostGenContNo:** ออกเลขสัญญาสำหรับรายการที่อนุมัติแล้ว (status=1, loancontract_no ว่าง)

#### ws_lon_payment.aspx.cs
- **Using:** `CoreSavingLibrary.WcfNLoan`, `CoreSavingLibrary.WcfNCommon`
- **SLIPTYPE_CODE default:** `"PX"` — slip ประเภทชำระ
- **CultureInfo:** th-TH (ไทย)
- **WebUtil.UseMembUptoToken()** — ล็อคสมาชิกขณะใช้งาน (token-based locking)
- **Data sets:** dsMain, dsDetailShare, dsDetailLoan, dsDetailEtc, dsDetailMoneytype
- **Events:** PostMemberNo, PostOperateFlag (L/E), PostSlipItem, PostInsertRow, PostDeleteRow, PostSliptypecode, PostPayspecMethod, PostChkLoanPayment, JsPostDataPMX, Jsreftranbank, JsPostPayin

---

## 4. โมดูล Keeping — เงินฝากประจำ (7 หน้า)

### 4.1 รายการหน้า

| ชื่อ Screen | คำอธิบาย |
|---|---|
| `ws_kep_process_keep` | **ประมวลผลเงินฝากประจำ** รายเดือน |
| `ws_kep_process_keepcut` | ตัดเงินฝากประจำ |
| `ws_kep_adjust_kptemp` | ปรับปรุง keeping ชั่วคราว |
| `ws_kep_adjustitem_kptp` | ปรับ item keeping temp |
| `ws_kep_exportfile_aero` | Export ไฟล์ Aero |
| `ws_kep_reprint_receipt` | พิมพ์ใบเสร็จซ้ำ |

### 4.2 Business Logic Highlights — Keeping

#### ws_kep_process_keep.aspx.cs
- **Using:** `CoreSavingLibrary.WcfNCommon`
- **proc_type:** แยกการประมวลผล 5 ประเภท:
  - `"2"` → ประมวลผลตาม Member Group (`ReportUtil.GetMinMaxMembgroup()`)
  - `"3"` → ประมวลผลตาม Member No (`ReportUtil.GetMinMaxMembno()`)
  - `"4"` → ประมวลผลตาม Member Category (`ReportUtil.GetMinMaxMbucfcategory()`)
  - `"5"` → ประมวลผลตาม Debt Agent (`dsMain.GetMinMaxdebtagent()`)
- **RECEIVE_YEAR / RECEIVE_MONTH:** ระบุงวดเดือนที่ประมวลผล
- **OfFindConfigProcDate():** ค้นหาวันประมวลผลที่กำหนดไว้
- **Tasks:** `System.Threading.Tasks` — async processing
- **ExecuteDataSource exe** — execute SQL ผ่าน DataSource

---

## 5. โมดูล MbShr — สมาชิกและหุ้น (35 หน้า)

### 5.1 รายการหน้าสำคัญ

| ชื่อ Screen | คำอธิบาย |
|---|---|
| `ws_mem_memberdetail` | **ข้อมูลสมาชิก** — แสดงข้อมูลครบถ้วน |
| `ws_mem_adjmember_detail` | แก้ไขข้อมูลสมาชิก |
| `ws_mem_adjfamily_mcc` | แก้ไขข้อมูลครอบครัว (MCC-specific) |
| `ws_mem_adjmemb_mobile` | แก้ไขเบอร์โทร |
| `ws_mem_adjappl_date` | แก้ไขวันที่สมัคร |
| `ws_mem_apvappl` | อนุมัติการสมัครสมาชิก |
| `ws_mem_apvresign` | อนุมัติลาออก |
| `ws_mem_apvreqgain` | อนุมัติขอรับเงินคืน |
| `ws_mem_apvtrnmb_settable` | อนุมัติโอนย้ายสมาชิก |
| `ws_mem_reqappl_gain` | ขอรับผลประโยชน์ |
| `ws_mem_reqresign` | ยื่นลาออก |
| `ws_mem_reqtran` | ขอโอนย้าย |
| `ws_mem_reqgain` | ขอรับเงินคืน |
| `ws_mem_adjperiod_share_std` | ปรับงวดหุ้นมาตรฐาน |
| `ws_mem_change_shareover` | เปลี่ยนแปลงหุ้นส่วนเกิน |
| `ws_mem_ucfmembgroup` | กำหนดกลุ่มสมาชิก |
| `ws_shr_withdraw` | ถอนหุ้น |
| `ws_shr_cclwithdraw` | ยกเลิกถอนหุ้น |

### 5.2 Business Logic Highlights — ws_mem_memberdetail

#### DataSets ที่ใช้ใน ws_mem_memberdetail.aspx.cs (ครอบคลุมที่สุด)
```
dsMain, dsDetail, dsGain, dsMoneytr, dsLoan, dsLoanCheck,
dsShare, dsColl, dsCollWho, dsKeep, dsDept, dsFamily,
dsKeepMonth, dsLoanStop, dsKeepArrear, dsKeepArrearPeriod,
dsFund, dsMonthOtherIn, dsMonthOtherOut, dsInsurance, dsRdc,
dsLinkATM, dsKeepMonthExpense, dsReward, dsAccidAtm,
dsMoneyreturn, dsMoneytrMain, dsMembStatus, dsReqChggroup,
dsReqApply, dsReqNameLog, dsGainreq, dsGainDetOld,
dsGainDetNew, dsWalfare
```
- **34+ DataSets** ในหน้าเดียว — แสดงข้อมูลสมาชิกครอบคลุมทุกมิติ
- รวมถึง `dsRdc` (เอกสาร), `dsLinkATM` (ATM link), `dsWalfare` (สวัสดิการ)
- **PostSmartCard:** รองรับการอ่าน Smart Card
- **PostGainReq:** ขอรับผลประโยชน์จากหน้าสมาชิก
- **PostKeepArrearPeriod:** ตรวจสอบเงินฝากค้างชำระ
- **RunReportDetail:** พิมพ์รายงานสมาชิก

---

## 6. โมดูล HR — Human Resources (81 หน้า)

### 6.1 รายการหน้าสำคัญ (แบ่งกลุ่ม)

#### กลุ่ม Master Data
| ชื่อ Screen | คำอธิบาย |
|---|---|
| `ws_hr_master` | **ประวัติพนักงาน** — ข้อมูลครบถ้วน |
| `ws_board_master` | ข้อมูลกรรมการ |
| `ws_hr_ucf_employee_level` | กำหนดระดับพนักงาน |
| `ws_hr_ucf_position_mas` | กำหนดตำแหน่ง |
| `ws_hr_ucf_position_board` | กำหนดตำแหน่งกรรมการ |
| `ws_hr_ucf_worktime` | กำหนดเวลาทำงาน |
| `ws_hr_ucf_positionlimit` | จำกัดตำแหน่ง |
| `ws_hr_ucf_meeting` | กำหนดประชุม |

#### กลุ่มการลา/OT
| ชื่อ Screen | คำอธิบาย |
|---|---|
| `ws_hr_leave` | **บันทึกการลา** |
| `ws_hr_apv_leave` | อนุมัติการลา |
| `ws_hr_apv_cancel_leave_new` | อนุมัติยกเลิกการลา |
| `ws_hr_cancel_leave_new` | ยกเลิกการลา |
| `ws_hr_reprint_leave` | พิมพ์ใบลาซ้ำ |
| `ws_hr_overtime_new` | **บันทึก OT** |
| `ws_hr_apv_ot` | อนุมัติ OT |
| `ws_hr_reprint_ot` | พิมพ์ OT ซ้ำ |
| `ws_hr_ot_trantofin` | โอน OT ไปการเงิน |
| `ws_hr_worktime_new` | บันทึกเวลาทำงาน |
| `ws_hr_worktime_imp` | Import เวลาทำงาน |
| `ws_hr_work_outside` | ทำงานนอกสถานที่ |
| `ws_hr_board_meeting_expenses` | ค่าประชุมกรรมการ |

#### กลุ่มเงินเดือน/Payroll
| ชื่อ Screen | คำอธิบาย |
|---|---|
| `ws_hr_payroll_process` | **ประมวลผลเงินเดือน** |
| `ws_hr_payroll_inc_exc` | รายได้/รายจ่ายพิเศษ |
| `ws_hr_salaryall` | เงินเดือนทั้งหมด |
| `ws_hr_bonus1` | โบนัส |
| `ws_hr_bonus_board` | โบนัสกรรมการ |
| `ws_hr_bonus_ratio_new` | อัตราโบนัส |
| `ws_hr_paybonus_year` | จ่ายโบนัสประจำปี |
| `ws_hr_diligence_money` | เงินขยันงาน |
| `ws_hr_diligence_pay` | จ่ายเงินขยัน |
| `ws_hr_process_special` | ประมวลผลพิเศษ |
| `ws_hr_trantofin` | โอนข้อมูลไปการเงิน |
| `ws_hr_trantofin_assist` | โอนเงินช่วยเหลือไปการเงิน |
| `ws_hr_clsyear` | ปิดปี HR |

#### กลุ่มสวัสดิการพนักงาน
| ชื่อ Screen | คำอธิบาย |
|---|---|
| `ws_hr_assist_employee` | เงินช่วยเหลือพนักงาน |
| `ws_hr_assist_employee_apv_new` | อนุมัติเงินช่วยเหลือ |
| `ws_hr_assist_child` | ทุนบุตร |
| `ws_hr_assist_dead` | เงินช่วยเหลืองานศพ |
| `ws_hr_assist_dead_family` | งานศพครอบครัว |
| `ws_hr_assist_inpatient` | ค่ารักษาพยาบาลผู้ป่วยใน |
| `ws_hr_assist_other_new` | เงินช่วยเหลืออื่น (ใหม่) |
| `ws_hr_assist_view_all` | ดูเงินช่วยเหลือทั้งหมด |
| `ws_hr_assist_work` | เงินช่วยเหลือระหว่างทำงาน |
| `ws_hr_reqassist_etc` | ขอเงินช่วยเหลืออื่นๆ |
| `ws_hr_cancel_apv_assist` | ยกเลิกอนุมัติเงินช่วยเหลือ |
| `ws_hr_cure_newfamily` | รักษาครอบครัวใหม่ |
| `ws_hr_cure_newfamily_apv_new` | อนุมัติรักษาครอบครัวใหม่ |
| `ws_hr_ucf_assistetc_new` | กำหนดเงินช่วยเหลืออื่น |
| `ws_hr_ucf_assistmedical` | กำหนดค่ารักษาพยาบาล |
| `ws_hr_ucf_assistwork` | กำหนดเงินช่วยเหลืองาน |

#### กลุ่มพิเศษ
| ชื่อ Screen | คำอธิบาย |
|---|---|
| `ws_hr_pension_emp` | บำเหน็จ/เกษียณ |
| `ws_hr_paylayoff` | จ่ายเงินเลิกจ้าง |
| `ws_hr_atone_layoff` | ชดเชยเลิกจ้าง |
| `ws_hr_changework` | โอนย้ายงาน |
| `ws_hr_ucf_costlive` | ค่าครองชีพ |
| `ws_hr_ucf_divageemp` | เฉลี่ยคืนพนักงาน |
| `ws_hr_ucf_investment` | ลงทุนพนักงาน |
| `ws_hr_ucf_bonuscal` | คำนวณโบนัส |

### 6.2 Business Logic Highlights — HR

#### ws_hr_master.aspx.cs
- **JsPostBack events 30+ รายการ:** PostEmpNo, PostInsertRowFamily, PostInsertRowGain, PostInsertRowEdu, PostInsertRowExperience, PostInsertRowTraining, PostDeleteFamilyRow, PostDeleteGainRow, PostAdnProvince, PostAdrProvince, PostAdnAmphur, PostAdrAmphur, PostExpBank, Postsalary, postidcard, postretrydate, postidcardfam, Postposition, Postdeptgrp_code, pos_code, PostPrint, PostEmername, PostInsertRowDisoffense, PostDeleteDisoffenseRow, PostInsertRowDsdisease, PostDeleteDsCrimeRow
- **ข้อมูลที่จัดการ:** ประวัติการศึกษา, ประสบการณ์, การฝึกอบรม, ข้อมูลครอบครัว, ที่อยู่ทั้ง 2 แห่ง (ปัจจุบัน/ตามทะเบียนบ้าน), ค่าตอบแทน, บัญชีธนาคาร, ประวัติโรค, ประวัติการกระทำผิด

#### ws_hr_payroll_process.aspx.cs
- **ตาราง Oracle ที่ใช้:**
  - `hrpayroll` — เก็บข้อมูล Payroll (post_status, payroll_period, coop_id)
  - `amworkcalendar` — ปฏิทินทำงาน (firstworkdate, lastworkdate, year, month)
- **คำนวณงวดเดือน:** ปี พ.ศ. + เดือน (`Convert.ToDecimal(DateTime.Now.Year) + 543`)
- **ตรวจสอบ post_status:** ถ้า post_status = 1 = ประมวลผลแล้ว → ป้องกันการทำซ้ำ
- **DdEmptype():** dropdown ประเภทพนักงาน
- **CultureInfo th-TH:** แสดงผลภาษาไทย

---

## 7. โมดูล RDC — Document Management (57 หน้า)

### 7.1 รายการหน้าสำคัญ

| ชื่อ Screen | คำอธิบาย |
|---|---|
| `ws_rdc_documentregis` | **ลงทะเบียนเอกสาร** |
| `ws_rdc_documentedit` | แก้ไขเอกสาร |
| `ws_rdc_documentsearch` | ค้นหาเอกสาร |
| `ws_rdc_documentstatus` | สถานะเอกสาร |
| `ws_rdc_documentcheck` | ตรวจสอบเอกสาร |
| `ws_rdc_documentchecksend` | ตรวจสอบ/ส่งเอกสาร |
| `ws_rdc_documentsend` | ส่งเอกสาร |
| `ws_rdc_documentreceive` | รับเอกสาร |
| `ws_rdc_documentreturn` | คืนเอกสาร |
| `ws_rdc_documentpick` | เบิกเอกสาร |
| `ws_rdc_documentdeposit` | ฝากเอกสาร |
| `ws_rdc_documentsafe` | เก็บเอกสารในตู้นิรภัย |
| `ws_rdc_documentdestroy` | ทำลายเอกสาร |
| `ws_rdc_documentcancel` | ยกเลิกเอกสาร |
| `ws_rdc_insidedocument` | เอกสารภายใน |
| `ws_rdc_outsidedocument` | เอกสารภายนอก |
| `ws_rdc_docmasterlist` | รายการเอกสารหลัก |
| `ws_rdc_exportdocument` | Export เอกสาร |
| `ws_rdc_sendems` | ส่งทาง EMS |
| `ws_rdc_findsend` | ค้นหาและส่งเอกสาร |
| `ws_rdc_opr_0023` | ปฏิบัติการ 0023 (พร้อมรูปภาพ) |
| `ws_rdc_opr_0024` | ปฏิบัติการ 0024 |
| `ws_rdc_opr_0052` | ปฏิบัติการ 0052 |
| `ws_rdc_adt_0033` | Audit 0033 |
| `ws_rdc_ccl_0032` | Cancel 0032 |
| `ws_rdc_ccl_0034` | Cancel 0034 |
| `ws_rdc_zcf_0011/0012` | กำหนดค่า ZCF |
| `ws_rdc_ucfrdcapprovenm` | กำหนดผู้อนุมัติ |
| `ws_rdc_ucfrdcdocsafetype` | ประเภทตู้เก็บ |
| `ws_rdc_ucfrdcmembertype` | ประเภทสมาชิก RDC |
| `ws_rdc_ucfrdcreceivetype` | ประเภทการรับ |
| `ws_rdc_ucfrdcstatusdestoy` | สถานะการทำลาย |
| `ws_rdc_ucfstatustype` | ประเภทสถานะ |

**ฟีเจอร์พิเศษ:** `ws_rdc_documentedit_img`, `ws_rdc_opr_img` — รองรับรูปภาพในเอกสาร (Image attachment)

---

## 8. โมดูล Walfare — สวัสดิการสหกรณ์ (33 หน้า)

> **หมายเหตุ:** ชื่อโฟลเดอร์จริงคือ `walfare` (ไม่ใช่ `welfare`) ตามที่พบในระบบ

### 8.1 รายการหน้าสำคัญ

| ชื่อ Screen | คำอธิบาย |
|---|---|
| `ws_wc_master_import` | Import ข้อมูลสวัสดิการหลัก |
| `ws_wc_adjmemb` | ปรับปรุงข้อมูลสมาชิกสวัสดิการ |
| `ws_wc_approve_newmemb_aero` | อนุมัติสมาชิกใหม่ (Aero) |
| `ws_wc_approve_resign` | อนุมัติลาออกจากสวัสดิการ |
| `ws_wc_paydie` | จ่ายเงินกรณีเสียชีวิต |
| `ws_wc_paydie_inform` | แจ้งการจ่ายกรณีเสียชีวิต |
| `ws_wc_revive` | ฟื้นฟูสมาชิก |
| `ws_wc_regis_paid` | ลงทะเบียนจ่ายเบี้ย |
| `ws_wc_paidyear_imp_wc` | Import การจ่ายรายปี |
| `ws_wc_year_paid` | จ่ายรายปี |
| `ws_wc_walfare_process` | ประมวลผลสวัสดิการ |
| `ws_wc_walfare_deptedit` | แก้ไขหักบัญชีสวัสดิการ |
| `ws_wc_edit_wccodeposit_admin` | แก้ไขรหัสบัญชี Admin |
| `ws_wc_walfare_req_edit_aero` | แก้ไขคำขอสวัสดิการ (Aero) |
| `ws_wc_setting` | ตั้งค่าสวัสดิการ |
| `ws_wc_inform_aero` | แจ้งข้อมูล Aero |
| `ws_wc_wccontcoop` | เชื่อมต่อสหกรณ์สวัสดิการ |
| `ws_wc_ucfrecievefixedyear` | กำหนดรับคงที่รายปี |
| `ws_wc_ucfroundregisfixed` | กำหนดรอบลงทะเบียนคงที่ |
| `ws_wf_walfare_group_paid` | จ่ายกลุ่มสวัสดิการ |

---

## 9. โมดูล Finance — การเงิน (23 หน้า)

| ชื่อ Screen | คำอธิบาย |
|---|---|
| `ws_fin_reqborrow` | ขอยืมเงิน |
| `ws_fin_borrow` | บันทึกการยืมเงิน |
| `ws_fin_approveborrow` | อนุมัติการยืม |
| `ws_fin_payborrow` | จ่ายเงินที่ยืม |
| `ws_fin_returnborrow` | คืนเงินยืม |
| `ws_fin_clearborrow` | ล้างหนี้ยืม |
| `ws_fin_cancelborrow` | ยกเลิกการยืม |
| `ws_fin_cancelreqborrow` | ยกเลิกคำขอยืม |
| `ws_fin_cancelclearborrow` | ยกเลิกการล้างหนี้ |
| `ws_fin_cancelreturnborrow` | ยกเลิกการคืนเงิน |
| `ws_fin_reprintborrow` | พิมพ์ใบยืมซ้ำ |
| `ws_fin_reprintborrow_pay` | พิมพ์ใบจ่ายซ้ำ |
| `ws_fin_closeday` | ปิดวัน |
| `ws_fin_exptobank_slipno` | Export ไปธนาคาร |
| `ws_fin_finucfborrow` | กำหนดประเภทยืม |
| `ws_fin_ucf_account_map` | Map บัญชี |
| `ws_fin_ucf_itemtype` | ประเภทรายการ |

---

## 10. โมดูล PM — Portfolio Management (21 หน้า)

| ชื่อ Screen | คำอธิบาย |
|---|---|
| `ws_pm_investment_account` | บัญชีลงทุน |
| `ws_pm_investment_account_edit` | แก้ไขบัญชีลงทุน |
| `ws_pm_investment_detail` | รายละเอียดการลงทุน |
| `ws_pm_requestmemo` | ขอ Memo |
| `ws_pm_approvememo` | อนุมัติ Memo |
| `ws_pm_redemption_investment` | ไถ่ถอนการลงทุน |
| `ws_pm_postinterest` | Post ดอกเบี้ย |
| `ws_pm_postpayment` | Post การชำระ |
| `ws_pm_posttofin_cancle` | ยกเลิก Post ไปการเงิน |
| `ws_pm_import_cleanprice` | Import ราคา Clean |
| `ws_pm_clean_yield` | คำนวณ Clean Yield |
| `ws_pm_intreceivedate_mass` | วันรับดอกเบี้ยจำนวนมาก |
| `ws_pm_checklist_finance` | Checklist การเงิน |
| `ws_pm_ucflistposition` | กำหนด Position list |
| `ws_pm_ucfposition` | กำหนด Position |

---

## 11. โมดูล BRW — Borrowing ระหว่างสหกรณ์ (19 หน้า)

| ชื่อ Screen | คำอธิบาย |
|---|---|
| `ws_brw_memoloanrequest` | Memo ขอกู้ |
| `ws_brw_memoapprove` | อนุมัติ Memo |
| `ws_brw_memostatus` | สถานะ Memo |
| `ws_brw_loan_detail` | รายละเอียดกู้ |
| `ws_brw_loan_agreement_detail` | รายละเอียดสัญญา |
| `ws_pm_requestloan` | ขอกู้ (PM) |
| `ws_pm_loan_agreement` | สัญญากู้ |
| `ws_pm_loan_payment` | ชำระเงินกู้ |
| `ws_pm_loan_postpayment` | Post การชำระ |
| `ws_pm_ucfborrowtype` | ประเภทการกู้ |
| `ws_pm_ucfcompany` | กำหนดบริษัท |
| `ws_pm_ucfinttype` | ประเภทดอกเบี้ย |

---

## 12. โมดูล DurInvt — ดุริยางค์/ทรัพย์สิน (18 หน้า)

| ชื่อ Screen | คำอธิบาย |
|---|---|
| `ws_dur_durtmaster` | ทรัพย์สินหลัก |
| `ws_dur_gendurtregno` | ออกเลขทะเบียน |
| `ws_dur_cal_statement` | คำนวณ statement |
| `ws_dur_close_year` | ปิดปี |
| `ws_dur_process_period_acc` | ประมวลผลงวด |
| `ws_durt_durtslipin_addmaster` | เพิ่มรายการรับ |
| `ws_durt_durtslipout` | รายการจ่าย |
| `ws_invt_invtmaster` | ทรัพย์สินระยะยาว |
| `ws_invt_invtreturn` | คืนทรัพย์สิน |
| `ws_invt_invtslipin_addmaster` | รับทรัพย์สิน |
| `ws_invt_invtslipout_adj` | จ่ายทรัพย์สิน (ปรับ) |
| `ws_invt_process_period_balacc` | ประมวลผลงวดยอดคงเหลือ |
| `ws_dur_ucf_diaccyear` | กำหนดปีบัญชี |
| `ws_dur_ucfname_report` | กำหนดชื่อรายงาน |
| `ws_dur_ucfposition_report` | กำหนดตำแหน่งรายงาน |
| `ws_dur_ucfprename_report` | กำหนดคำนำหน้ารายงาน |

---

## 13. โมดูล IXP — Interface/Export-Import (12 หน้า)

| ชื่อ Screen | คำอธิบาย |
|---|---|
| `ws_fin_exptobank` | Export การเงินไปธนาคาร |
| `ws_ixp_billing_cut` | ตัด Billing |
| `ws_ixp_loan_disbursement` | เบิกจ่ายสินเชื่อ |
| `ws_ixp_mb_register_cardPerson` | ลงทะเบียน Card |
| `ws_ixp_mb_salary` | เงินเดือนสมาชิก |
| `ws_ixp_mb_salaryid` | รหัสเงินเดือน |
| `ws_kep_fileexport` | Export ไฟล์ Keeping |
| `ws_kep_fileimport` | Import ไฟล์ Keeping |
| `ws_kep_fileimport_mcc` | Import ไฟล์ Keeping (MCC-specific) |
| `ws_lon_exptobank` | Export สินเชื่อไปธนาคาร |

**MCC-Specific:** `ws_kep_fileimport_mcc` — screen ที่มีเฉพาะ MCC ไม่มีในสหกรณ์อื่น

---

## 14. โมดูลอื่นๆ

### Assist (11 หน้า) — เงินช่วยเหลือสมาชิก
`ws_as_request`, `ws_as_genrequest`, `ws_as_assistpay`, `ws_as_assistpaygroup_list`, `ws_as_assstatement`, `ws_as_approve_rfsc`, `ws_as_update_money_request`, `ws_as_update_status_request_edu`, `ws_as_ucfassisttype`

### Insurance (9 หน้า) — ประกันภัย
`ws_ins_reqinsure`, `ws_ins_reqinsure_aero`, `ws_ins_insuredetail`, `ws_ins_apvinsure`, `ws_ins_reqsurrender`, `ws_ins_apvsurrender`, `ws_ins_proc_apply_insloan`, `ws_ins_proc_close_insloan`

### Investment (6 หน้า) — ลงทุน
`ws_inv_*` (6 ไฟล์)

### Account (3 หน้า) — งบประมาณ
`ws_acc_budget_over_budget`, `ws_acc_budget_set_remark`, `ws_acc_budget_setamt_mcc`

### FOM (3 หน้า) — Form Management
`wd_fom_addworkgroup`, `wd_fom_upload_image`, `wd_fom_user_list` (ส่วนใหญ่เป็น Dialog)

---

## 15. Web.config — การตั้งค่าระบบ

### 15.1 App Settings สำคัญ
```xml
<add key="sitePrefix" value="CEN"/>
<add key="sitePrefixDB" value="CEN"/>
<add key="siteEName" value="Mahidol University Saving and Credit Co-Operative , Limited"/>
<add key="siteLinkName" value="http://www.postcatsavings.com/index.html"/>
<add key="gstore_ip" value="127.0.0.1"/>
<add key="bookVersion" value="new"/>
<add key="bindingMaxReceivedMessageSize" value="2147483647"/>
```
- **sitePrefix CEN** = สหกรณ์ Mahidol University (MCC หลัก)
- **gstore_ip** = 127.0.0.1 (GStore server สำหรับ iReport)
- **bindingMaxReceivedMessageSize** = 2GB (WCF รับข้อมูลขนาดใหญ่)

### 15.2 Oracle Database Connections
| Name | Data Source | User | หมายเหตุ |
|---|---|---|---|
| ConnectionString | 192.168.10.200/gcoop | iscorfd | หลัก (MCC) |
| ConnectionString1 | 192.168.1.229/gcoop | iscolap | |
| ConnectionString2 | 10.1.1.1/gcoop | iscolap | |
| ConnectionString3 | 172.17.30.45/gcoop | iscobtg | BTG สาขา |
| ConnectionString4 | Web.siamcoop.com/gcoop | iscorfscuat | RFSC UAT |
| ConnectionString5 | 43.229.79.117/gcoop | iscomrc | MRC |
| ConnectionStringAERO | 203.154.158.107:1521/gcoop | iscoaero | Aero สหกรณ์ |
| isconcmt | 203.154.158.107:1521/gcoop | iscoaero | CMT |
| ConnectionString6 | 203.154.158.107/gcoop | iscoaerodep | Aero Deposit |
| ConnectionString7 | 43.229.79.117/gcoop | iscomrc | MRC |
| ConnectionString8 | 127.0.0.1/gcoop | iscocmt | CMT Local |

**ทุก connection ใช้** `providerName="System.Data.OracleClient"` (32-bit, x86)

### 15.3 Authentication
```xml
<authentication mode="Forms">
  <forms loginUrl="http://sav.icoopthai.co/CEN/GCOOP/SingleSignOn/Login.aspx"
         defaultUrl="http://sso.icoopthai.co/CEN/GCOOP/SingleSignOn/Default.aspx"
         domain="icoopthai.co"/>
</authentication>
```
- **Single Sign-On** ผ่าน icoopthai.co domain
- Cookie shared across domain

### 15.4 WCF Service Endpoints (n_*.svc)
| Service | Endpoint URL | Contract |
|---|---|---|
| n_common | localhost/CORE/GCOOP/WcfService125/n_common.svc | BasicHttpBinding |
| n_account | localhost/CORE/GCOOP/WcfService125/n_account.svc | BasicHttpBinding |
| n_deposit | localhost/CORE/GCOOP/WcfService125/n_deposit.svc | BasicHttpBinding |
| n_divavg | localhost/CORE/GCOOP/WcfService125/n_divavg.svc | BasicHttpBinding |
| n_finance | localhost/CORE/GCOOP/WcfService125/n_finance.svc | BasicHttpBinding |
| n_investment | localhost/CORE/GCOOP/WcfService125/n_investment.svc | BasicHttpBinding |
| n_keeping | localhost/CORE/GCOOP/WcfService125/n_keeping.svc | BasicHttpBinding |
| n_loan | localhost/CORE/GCOOP/WcfService125/n_loan.svc | BasicHttpBinding |
| n_mbshr | localhost/CORE/GCOOP/WcfService125/n_mbshr.svc | BasicHttpBinding |
| ATMcoreWeb | localhost/ATM/CoreCoop/ATMcoopServiceWeb/ATMcoreWeb.svc | BasicHttpBinding |

**Path:** `localhost/CORE/GCOOP/WcfService125/` — WCF Services ที่ PowerBuilder expose ให้ ASP.NET

### 15.5 Runtime Settings
```xml
<httpRuntime executionTimeout="43200" maxRequestLength="104856"/>
<compilation debug="true" targetFramework="4.0"/>
```
- **executionTimeout = 43200 วินาที** (12 ชั่วโมง) — สำหรับ batch processing ขนาดใหญ่
- **maxRequestLength = 104856 KB** (~100 MB) — upload ไฟล์ขนาดใหญ่ได้
- **AjaxPro:** `POST,GET` path `ajaxpro/*.ashx`

---

## 16. CriteriaIReport — Schema สำหรับ iReport

**Path:** `/root/gcoop_hermes/mcc/GCOOP/Saving/CriteriaIReport/`  
**จำนวน:** 117 criteria files

| ไฟล์ระบบ | คำอธิบาย |
|---|---|
| `DatasetCriIR.xsd/xsc/xss` | Dataset schema หลักสำหรับ iReport |
| `u_ass_coopid_rdate_rmembno_dead` | ช่วยเหลือกรณีเสียชีวิต |
| `u_ass_coopid_rdate_rmembno_prename` | ช่วยเหลือตามชื่อ |
| `u_ass_coopid_retire_date` | เกษียณอายุ |
| `u_coopid_date_daily_receipt` | ใบเสร็จรายวัน |
| `u_cri_coopid_hr_empno_payroll` | Payroll HR |
| `u_cri_coopid_hr_rdate_in_empno` | HR ตามช่วงวันที่ |
| `u_cri_coopid_memno_loancont_refcollno_mcc` | **MCC-specific** — เลขสัญญา+หลักประกัน |
| `u_cri_coopid_memno_loancont_refcollno_mcc_resign` | MCC resign criteria |
| `u_cri_coopid_period_memno_loancont_refcollno_mcc` | MCC งวด+สัญญา+หลักประกัน |
| `u_cri_coopid_mb_rgroupp` | กลุ่มสมาชิก |
| `u_cri_coopid_period_rmembgroup_rmembagent` | งวด+กลุ่ม+agent |

**MCC-Specific Criteria:** ไฟล์ที่มี `_mcc` ต่อท้าย — พบ 3 ไฟล์ criteria ที่สร้างเฉพาะ MCC

---

## 17. DataWindow Directory

**Path:** `/root/gcoop_hermes/mcc/GCOOP/Saving/DataWindow/`  
**27 โฟลเดอร์:**

| โฟลเดอร์ | โมดูลที่เกี่ยวข้อง |
|---|---|
| `Common` | DataWindows ส่วนกลาง |
| `Cmd` | Command DataWindows |
| `Criteria` | Criteria definitions |
| `Shrlon` | Share+Loan |
| `account`, `admin`, `agency`, `arc` | โมดูลย่อย |
| `ap_deposit` | เงินฝาก |
| `app_assist`, `app_finance` | เงินช่วยเหลือ, การเงิน |
| `budget`, `divavg` | งบประมาณ, เฉลี่ยคืน |
| `etp_icc` | ETP ICC integration |
| `hr` | HR DataWindows |
| `insurance`, `investment` | ประกัน, ลงทุน |
| `keeping`, `keepingmonth` | เงินฝากประจำ รายเดือน |
| `lawsys` | ระบบกฎหมาย |
| `loanassist`, `loantracking` | สินเชื่อช่วยเหลือ, ติดตาม |
| `mbshr` | สมาชิก+หุ้น |
| `mis` | MIS reports |
| `pm` | Portfolio |
| `shrlonclsproc` | ปิดหุ้น+เงินกู้ |
| `trading` | การค้า |

---

## 18. Integration Points

### 18.1 WCF Integration (Web → PowerBuilder)
```
ASP.NET Page → WcfCalling (wcf.*) → WcfService125/*.svc → PowerBuilder NVO
```
- `wcf.NLoan` → `n_loan.svc` → PB NVO n_loan
- `wcf.NCommon` → `n_common.svc` → PB NVO n_common
- **ตัวอย่างการเรียก WCF จริง:**
  ```csharp
  loanService = wcf.NLoan;  // (ws_lon_apvloan.aspx.cs)
  String newReqDocNo = wcf.NLoan.of_gennewcontractno(state.SsWsPass, req_coopid, loantype_code);
  ```

### 18.2 Oracle Direct Query
- ส่วนของ PayrollProcess เรียก Oracle ตรง (ไม่ผ่าน WCF):
  ```sql
  SELECT count(post_status) FROM hrpayroll WHERE coop_id = '...' AND payroll_period = '...'
  SELECT firstworkdate, lastworkdate FROM amworkcalendar WHERE coop_id = '...' AND year = '...' AND month = '...'
  ```
- ใช้ `WebUtil.QuerySdt()` สำหรับ query Oracle และ return `Sdt` (Simple DataTable)
- ใช้ `ExecuteDataSource exe` สำหรับ Insert/Update/Delete

### 18.3 iReport Integration
- **Frame.Master.cs:** `WebUtil.StartIreportBuilder(false/true)` — start/restart Java iReport Builder
- **CriteriaIReport/:** 117 XSD schemas ส่ง parameter ไปยัง iReport
- **gstore_ip=127.0.0.1** — iReport server บน localhost
- **IREPORTFILE/:** ไฟล์ .jrxml ที่ generate รายงาน

### 18.4 ATM Integration
- **n_loanClient** จาก `CoreSavingLibrary.WcfNLoan`
- **ATMcoreWeb.svc** — ATM Core Web Service (localhost/ATM/CoreCoop/)
- `dsLinkATM` ใน ws_mem_memberdetail — แสดงข้อมูล ATM ของสมาชิก

### 18.5 AjaxPro
```xml
<add verb="POST,GET" path="ajaxpro/*.ashx" type="AjaxPro.AjaxHandlerFactory, AjaxPro.2"/>
```
- AjaxPro 2 สำหรับ JavaScript-to-Server method calls
- ใช้ร่วมกับ `[JsPostBack]` attribute pattern

---

## 19. MCC-Specific Features

คุณลักษณะพิเศษที่พบเฉพาะใน MCC (จากการตรวจสอบ code จริง):

### 19.1 CoopId-specific Logic ใน ws_lon_apvloan
```csharp
if (state.SsCoopControl == "003001" || state.SsCoopControl == "074001")
{
    dsMain.DATA[0].order_type = "3";
    dsList.RetrieveList("", "3");
}
```
- MCC มีหลาย CoopId (003001, 074001) — logic พิเศษสำหรับบางสาขา

### 19.2 ws_mem_adjfamily_mcc — แก้ไขครอบครัว MCC
- Screen ที่มีชื่อ `_mcc` ต่อท้ายโดยตรง — สร้างเฉพาะ MCC

### 19.3 ws_kep_fileimport_mcc — Import Keeping MCC
- `ws_kep_fileimport_mcc` vs `ws_kep_fileimport` — version MCC มี dialog `wd_kep_adjustkeprealitem_memb` เพิ่มเติม

### 19.4 CriteriaIReport MCC-Specific
- `u_cri_coopid_memno_loancont_refcollno_mcc` — criteria หลักประกันสินเชื่อเฉพาะ MCC
- `u_cri_coopid_memno_loancont_refcollno_mcc_resign` — เพิ่ม flag ลาออก

### 19.5 ws_acc_budget_setamt_mcc — งบประมาณ MCC
- Screen ตั้งงบประมาณเฉพาะ MCC (3 screens ใน account: over_budget, set_remark, setamt_mcc)

### 19.6 Token-based Member Locking
```csharp
WebUtil.UseMembUptoToken(state.SsTokenId, state.SsUsername, "");
```
- ล็อคสมาชิกขณะมีเจ้าหน้าที่กำลังใช้งาน — ป้องกัน concurrent edit

### 19.7 Dual-DB Mode Support
```csharp
if (Sta.IS_OLEDB_MODE)
{
    sqlwhere += " ... convert(datetime, '...', 103) ...";
}
else
{
    sqlwhere += " ... to_date('...', 'dd/MM/yyyy') ...";
}
```
- รองรับทั้ง Oracle (`to_date`) และ SQL Server (`convert`) — ยืดหยุ่นสำหรับ multi-client deployment

### 19.8 Smart Card Integration
- `PostSmartCard` event ใน ws_mem_memberdetail — อ่านบัตร Smart Card สมาชิก

### 19.9 Multiple Connection Strings
- 10 connection strings ในไฟล์เดียว — deploy ให้หลายสหกรณ์พร้อมกัน (CEN, BTG, MRC, Aero, CMT)

### 19.10 เงินช่วยเหลือแบ่งประเภทละเอียด (HR Module)
- `ws_hr_assist_child` (ทุนบุตร), `ws_hr_assist_dead` (งานศพ), `ws_hr_assist_dead_family` (งานศพครอบครัว), `ws_hr_assist_inpatient` (ผู้ป่วยใน), `ws_hr_assist_other_new` (อื่นๆ), `ws_hr_assist_work` (ระหว่างงาน)
- 6 ประเภทเงินช่วยเหลือพนักงาน — ละเอียดกว่าระบบอื่น

---

## 20. สรุป Architecture Pattern

```
Browser (JavaScript/AjaxPro)
    ↓ HTTP POST (JsPostBack)
Frame.Master (session, WcfCalling factory, XmlConfigService)
    ↓
PageWebSheet (CheckJsPostBack / SaveWebSheet / WebSheetLoadBegin)
    ↓ [ผ่าน ExecuteDataSource]        ↓ [ผ่าน wcf.*]
Oracle Database (System.Data.OracleClient) ↔ WcfService125 (PowerBuilder NVO)
                                               ↕
                                          Oracle Database
```

**Pattern WebSheet:**
1. `InitJsPostBack()` — ลงทะเบียน DataSets
2. `WebSheetLoadBegin()` — initial load (ถ้าไม่ PostBack)
3. `CheckJsPostBack(eventArg)` — handle events จาก JavaScript
4. `SaveWebSheet()` — บันทึกข้อมูล

---

*เอกสารนี้สร้างจากการตรวจสอบ source code จริงใน `/root/gcoop_hermes/mcc/GCOOP/Saving/` วันที่ 2026-09-14*
