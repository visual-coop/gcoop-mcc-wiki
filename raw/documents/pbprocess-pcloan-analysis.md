# PowerBuilder Library Analysis: pcloan.pbl

## ข้อมูลพื้นฐาน (Basic Information)

**ไฟล์:** `/root/gcoop_hermes/mhd/GCOOP/PBProcess/pcloan.pbl`  
**ขนาด:** 8.5 MB (8,906,752 bytes)  
**ระบบ:** ระบบสินเชื่อ/เงินกู้ (Loan System)  
**การเชื่อมต่อ:** เชื่อมกับระบบ MHD หลัก  
**วันที่วิเคราะห์:** 14/09/2026 17:18

## สรุปผลการวิเคราะห์ (Analysis Summary)

จากการวิเคราะห์ PowerBuilder Library pcloan.pbl พบว่าเป็นไลบรารี่หลักของระบบสินเชื่อที่มีความซับซ้อนสูง ประกอบด้วย:

- **Text Blocks ที่พบ:** 15,136 blocks
- **เนื้อหาที่เกี่ยวข้องกับสินเชื่อ:** 1,141 blocks  
- **Function Signatures:** 452 รายการ
- **ตารางฐานข้อมูล:** 31 ตาราง
- **กฎทางธุรกิจ:** 165 กฎ
- **การคำนวณดอกเบี้ย:** 264 รายการ
- **การประมวลผลการชำระเงิน:** 299 รายการ

## โครงสร้างระบบ (System Architecture)

### ตารางฐานข้อมูลหลัก (Main Database Tables)

- **kparrearreceive** - Keeping/Processing (การเก็บรักษา/ประมวลผล)
- **kpmastreceive** - Keeping/Processing (การเก็บรักษา/ประมวลผล)
- **kpreceiveexpense** - Keeping/Processing (การเก็บรักษา/ประมวลผล)
- **kptempreceive** - Keeping/Processing (การเก็บรักษา/ประมวลผล)
- **kptempreceivedet** - Keeping/Processing (การเก็บรักษา/ประมวลผล)
- **lccontmaster** - Loan Contract (สัญญาเงินกู้)
- **lccontmaster_loanapprove_amt** - Loan Contract (สัญญาเงินกู้)
- **lccontmaster_period_payment** - Loan Contract (สัญญาเงินกู้)
- **lcloantype_inttabrate_code** - Loan Contract (สัญญาเงินกู้)
- **lncfloanintrate** - Loan (สินเชื่อ)
- **lncfloanintrated** - Loan (สินเชื่อ)
- **lncfloanintratedet** - Loan (สินเชื่อ)
- **lncfloanintratedet_upper_amt_t** - Loan (สินเชื่อ)
- **lncontintcaltest** - Loan (สินเชื่อ)
- **lncontintspc** - Loan (สินเชื่อ)
- **lncontmaster** - Loan (สินเชื่อ)
- **lncontstateme** - Loan (สินเชื่อ)
- **lncontstatement** - Loan (สินเชื่อ)
- **lndoubtacccondition** - Loan (สินเชื่อ)
- **lnestloanshortlong** - Loan (สินเชื่อ)
- **lnloanconstant** - Loan (สินเชื่อ)
- **lnloantype_continttable_code_t** - Loan (สินเชื่อ)
- **lnpreparepay** - Loan (สินเชื่อ)
- **lnreqcontadjust** - Loan (สินเชื่อ)
- **lnucfloanitemtype** - Loan (สินเชื่อ)
- **slclsmthbalance** - Other
- **slcurbalance** - Other
- **slmemberdata** - Other
- **slmemreceivelong** - Other
- **slmemreceiveshort** - Other
- **slratesavingdep** - Other

### หมวดหมู่ตารางฐานข้อมูล (Database Table Categories)

1. **LN Series (สินเชื่อหลัก)** - ตารางหลักของระบบสินเชื่อ
   - `lncontmaster` - ข้อมูลสัญญาเงินกู้หลัก
   - `lncontstatement` - รายการเคลื่อนไหวสินเชื่อ  
   - `lncfloanintrate` - อัตราดอกเบี้ยสินเชื่อ

2. **LC Series (สัญญาเงินกู้)** - ข้อมูลสัญญาและการอนุมัติ
   - `lccontmaster` - ข้อมูลสัญญาหลัก
   - `lcloantype_inttabrate_code` - รหัสตารางอัตราดอกเบี้ยตามประเภทเงินกู้

3. **KP Series (การประมวลผล)** - การเก็บรักษาและประมวลผลธุรกรรม  
   - `kpmastreceive` - การรับเงินหลัก
   - `kptempreceive` - การรับเงินชั่วคราว
   - `kparrearreceive` - การรับเงินค้างชำระ

## ฟังก์ชันหลัก (Main Functions)

### การคำนวณดอกเบี้ย (Interest Calculation)

**1.** `column=(type=number updatewhereclause=yes name=interest_return dbname="interest_return" )...`
**2.** `ds_intrate.getitemdecimal( ll_find, "interest_rate" )...`
**3.** `ccess_date =TO_DATE('1-09-2026 16:59:47','DD-MM-YYYY HH24:MI:SS') , lastcalint_date =TO_DATE('1-09-2...`
**4.** `column(band=detail id=19 alignment="1" tabsequence=190 border="0" color="33554432" x="8599" y="4" he...`
**5.** `r ln.principal_return  > 0  ) and ln.lastcalint_date >= :ldtm_retdate and ln.lastprocess_date >= :ld...`

### การประมวลผลการชำระเงิน (Payment Processing)

**1.** `al(2) update=yes updatewhereclause=yes name=principal_payment dbname="kptempreceivedet.principal_pay...`
**2.** `column=(name=period_payment_max dbname="lncontmaster.period_payment_max" type=decimal(2) updatewhere...`
**3.** `column=(type=decimal(2) update=yes updatewhereclause=yes name=interest_payment dbname="kptempreceive...`
**4.** `format="[general]" html.valueishtml="0"  name=payment_status visible="1" edit.limit=0 edit.case=any ...`
**5.** `, prnarr_amt , prnarr_bal , intarr_amt , intarr_bal , ref_mthslipno , bfprincipal_balance , calint_f...`

### การอนุมัติสินเชื่อ (Loan Approval Logic)

**1.** `system_code , ref_slipdate , ref_slipno , ref_docno , description , shrlontype_code , loancontract_n...`
**2.** `format="[general]" html.valueishtml="0"  name=payment_status visible="1" edit.limit=0 edit.case=any ...`
**3.** `, prnarr_amt , prnarr_bal , intarr_amt , intarr_bal , ref_mthslipno , bfprincipal_balance , calint_f...`
**4.** `( coop_id , member_no , div_year , seq_no , operate_date , slip_date , divitemtype_code , div_paymen...`
**5.** `column(name=loanapprove_amt band=detail x="1074" y="4" height="76" width="334" id=5 border="0" align...`

## การดำเนินงานฐานข้อมูล (Database Operations)

ระบบมีการดำเนินงานฐานข้อมูลที่สำคัญดังนี้:

**1.** `update lncontstatement set interest_return =interest_payment where principal_balance < 0 and operate_date =TO_DATE('1-09-2026 16:59:47','DD-MM-YYYY HH...`
**2.** `update lnpreparepay set prn_payment =0 , int_payment =0 , item_payment =0 where coop_id =' ' and loan_id =' ' and expense_code =' ' and prepayuse_cod...`
**3.** `c function long of_updatearr (string as_coopid, string as_memberid, string as_loancontno, decimal adc_loanarr, decimal adc_intarr, date adt_calcdate...`
**4.** `cancel_id =' ' , a.cancel_date =TO_DATE('1-09-2026 16:59:47','DD-MM-YYYY HH24:MI:SS') where ( a.coop_id =' ' ) and ( a.loancontract_no =' ' ) and (...`
**5.** `3 function string of_sql_procupload (string as_coopid, string as_proctype, string as_fileupload, datetime adt_uploaddate, string as_uploaduserid) st...`
**6.** `update lncontmaster set cancel_id =' ' , cancel_date =TO_DATE('1-09-2026 16:59:47','DD-MM-YYYY HH24:MI:SS') where coop_id =' ' and loancontract_no =...`
**7.** `c function long of_inserttemprcv (string as_coopid, string as_memberid, string as_loancontno, decimal adc_principal, decimal adc_interest, decimal ad...`
**8.** `update kptempreceivedet set process_date =TO_DATE('1-09-2026 16:59:47','DD-MM-YYYY HH24:MI:SS') , process_id =' ' where coop_id =' ' and receive_no ...`

## ความสัมพันธ์กับระบบ MHD (MHD System Integration)

ระบบ pcloan.pbl เชื่อมต่อกับระบบ MHD หลักผ่าน:

1. **PowerBuilder Services** - การเรียกใช้ WCF Services
2. **Database Sharing** - การใช้ตารางฐานข้อมูลร่วมกัน
3. **Business Logic Integration** - การประมวลผลธุรกิจแบบบูรณาการ
4. **Report Generation** - การสร้างรายงานร่วมกัน

## คุณสมบัติเด่น (Key Features)

### 1. ระบบการคำนวณดอกเบี้ยที่ซับซ้อน
- รддержการคำนวณดอกเบี้ยแบบต่างๆ มากมาย
- การจัดการอัตราดอกเบี้ยตามประเภทสินเชื่อ
- การคำนวณดอกเบี้ยค้างชำระ

### 2. ระบบการจัดการการชำระเงิน  
- การรับชำระเงินต้น ดอกเบี้ย และค่าธรรมเนียม
- การจัดการการชำระล่วงหน้า
- การติดตามยอดค้างชำระ

### 3. การควบคุมสถานะสินเชื่อ
- การติดตามสถานะของสัญญาเงินกู้
- การจัดการการอนุมัติและยกเลิก
- การควบคุมเงื่อนไขต่างๆ

### 4. การรายงานและวิเคราะห์
- การสร้างรายงานทางการเงิน
- การวิเคราะห์ผลประกอบการ
- การติดตามความเสี่ยง

## ข้อเสนอแนะสำหรับการพัฒนาต่อ (Development Recommendations)

1. **การปรับปรุงประสิทธิภาพ**
   - ทบทวนการ query ฐานข้อมูลเพื่อเพิ่มความเร็ว
   - ปรับปรุงการคำนวณดอกเบี้ยให้มีประสิทธิภาพมากขึ้น

2. **การเพิ่มความปลอดภัย**  
   - เพิ่มการตรวจสอบสิทธิ์การเข้าถึงข้อมูล
   - ปรับปรุงการ audit trail

3. **การปรับปรุง User Interface**
   - พัฒนา web interface สำหรับการใช้งานที่สะดวกขึ้น
   - เพิ่ม mobile compatibility

4. **การบูรณาการ**
   - พัฒนา API สำหรับการเชื่อมต่อกับระบบภายนอก
   - ปรับปรุงการเชื่อมต่อกับระบบธนาคาร

## ข้อควรระวัง (Considerations)

1. **ความซับซ้อนของระบบ** - ระบบมีความซับซ้อนสูง ต้องมีความเข้าใจเชิงลึกก่อนแก้ไข
2. **การพึ่งพาฐานข้อมูล** - มีการเชื่อมโยงกับตารางหลายตารางมาก ต้องระวังผลกระทบ
3. **Business Logic** - มีกฎทางธุรกิจที่ซับซ้อน ต้องทดสอบอย่างละเอียด
4. **Performance** - การประมวลผลจำนวนมากอาจส่งผลต่อประสิทธิภาพระบบ

---

**หมายเหตุ:** การวิเคราะห์นี้ใช้เทคนิค PowerBuilder Library Analysis โดยการแยกสตริงจากไฟล์ .pbl และวิเคราะห์รูปแบบการใช้งาน ผลลัพธ์อาจต้องได้รับการตรวจสอบเพิ่มเติมจากการทดสอบระบบจริง