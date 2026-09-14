# MCC PowerBuilder System Analysis

## Document Overview
Analysis Date: September 14, 2026  
Scope: MCC PowerBuilder modules, applications, and business logic  
Location: `/root/gcoop_hermes/mcc/`  

## Executive Summary

The MCC PowerBuilder system consists of multiple interconnected applications and libraries designed for cooperative financial management. The system includes:

- **MCC PBProcess modules** (11 core business logic libraries)
- **iSavBOfc application** (main banking interface with 8 component directories)
- **MCC Pipeline conversion system** (7 specialized conversion modules)
- **MCC Report system** (specialized reporting for MCC cooperative)
- **65 PowerBuilder libraries (.pbl)** and **51 compiled libraries (.pbd)**

## 1. MCC PBProcess Modules Analysis

### Location
`/root/gcoop_hermes/mcc/GCOOP/PBProcess/`

### Core Architecture
- **Primary Application**: `pbprocess.exe` (30,720 bytes)
- **Main Workspace**: `pbprocess.pbw` with target `pbprocess.pbt`
- **Application Library**: `pbprocess.pbl` (82,432 bytes)

### Business Logic Libraries

#### 1.1 Core Processing Modules
```
pbprocess.pbl      82,432 bytes   - Main processing engine
pccommon.pbl    2,105,856 bytes   - Common utilities and functions
```

#### 1.2 Financial Modules
```
pcdeposit.pbl  11,943,936 bytes   - Deposit account management (largest module)
pckeeping.pbl  14,386,176 bytes   - Bookkeeping system (largest module)
pcfinance.pbl   8,551,424 bytes   - Financial operations
pcloan.pbl      9,054,720 bytes   - Loan management
```

#### 1.3 Cooperative-Specific Modules
```
pcmbshr.pbl       693,760 bytes   - Member share management
pcdivavg.pbl    3,924,992 bytes   - Dividend averaging
pcinsurance.pbl 1,725,440 bytes   - Insurance management
pcinvertment.pbl 1,270,784 bytes  - Investment management
pcaccount.pbl     536,064 bytes   - Account management
```

### Library Dependencies (LibList)
```
pbprocess.pbl;pccommon.pbl;pcaccount.pbl;pcdeposit.pbl;
pcdivavg.pbl;pcfinance.pbl;pcinsurance.pbl;pcinvertment.pbl;
pckeeping.pbl;pcloan.pbl;pcmbshr.pbl
```

## 2. MCC iSavBOfc Application Analysis

### Location  
`/root/gcoop_hermes/mcc/iSavBOfc/`

### Application Structure
- **Main Workspace**: `isavbofc.pbw`
- **Target Application**: `iBank/isavbofc.pbt`
- **Application Name**: `isavbofc`

### Component Directories

#### 2.1 Core Application (`/iBank/`)
```
isavbofc.pbl     - Main application library
ibanktrn.pbl     - Banking transaction module
chack_stmbal.pbl - Statement balance checking
iimportdept.pbl  - Department import functionality
```

#### 2.2 PowerBuilder Foundation Classes (`/PFC105/`)
```
pfcapsrv.pbl     - Application server services
pfcdwsrv.pbl     - DataWindow services
pfcmain.pbl      - Main PFC framework
pfcutil.pbl      - Utility functions
pfcwnsrv.pbl     - Window services
pfeapsrv.pbl     - Extended application services
pfedwsrv.pbl     - Extended DataWindow services
pfemain.pbl      - Extended main framework
pfeutil.pbl      - Extended utilities
pfewnsrv.pbl     - Extended window services
```

#### 2.3 Base Communication Services (`/sBaseCom/`)
```
cmappcom.pbl     - Application communication
cmcoinit.pbl     - Communication initialization
cmcomsrv.pbl     - Communication server
cmoutsrv.pbl     - Output services
cmprnsrv.pbl     - Print services
cmrepsrv.pbl     - Report services
cmtdtsrv.pbl     - Date/time services
```

#### 2.4 Business Communication (`/sBussCom/`)
```
pccomloan.pbl    - Loan communication
cmsrv_mbshr.pbl  - Member share server
cmsrv_loan.pbl   - Loan server
cmsrv_fin.pbl    - Finance server
cmsrv_dept.pbl   - Department server
```

#### 2.5 Process Integration (`/pbpro/`)
```
pbprocess.pbl      - Process integration
pccommon.pbl       - Common process functions
pcdeposit.pbl      - Deposit process integration
rpt_report_dept.pbl - Department reporting
```

## 3. MCC Database Integration

### Connection Configuration

#### Primary Database (Oracle)
```ini
[Database]
DBMS=ORA Oracle
LogId=iscodoaeuat / iscoaero
ServerName=192.198.1.171/gcoop (Production)
ServerName=localhost/gcoop (Local)
DbParm="NLS_Charset='TH8TISASCII'"
AutoCommit=false
```

#### Secondary Databases
```ini
[rfscold] - Legacy system integration
ServerName=192.198.1.201/saving
LogId=dbo

[rfscold1] / [rfscold2] - Additional data sources
ServerName=192.198.1.171/dbo
```

### Branch Configuration
- **Production Branch**: `branchcontrol="057001"`, `branchcurrent="057001"`
- **Development Branch**: `branchcontrol="077001"`, `branchcurrent="077001"`

## 4. MCC Conversion Pipeline System

### Location
`/root/gcoop_hermes/mcc/CONVERT_MCC/mcc_pipeline/`

### Pipeline Architecture
- **Main Application**: `aeropipe` (mcc_pipe.pbt)
- **Primary Library**: `mcc_pipe_app.pbl`

### Conversion Modules
```
mcc_pipe_app.pbl       - Application pipeline core
mcc_pipe_dep.pbl       - Deposit conversion
mcc_pipe_divavg.pbl    - Dividend averaging conversion
mcc_pipe_insurance.pbl - Insurance conversion
mcc_pipe_keeping.pbl   - Bookkeeping conversion
mcc_pipe_member.pbl    - Member conversion
mcc_pipe_shrlon.pbl    - Share loan conversion
```

### Data Mapping Tables

#### Share Item Mapping (`mapshritem.sql`)
```sql
-- Legacy to new share item code mapping
'B ' -> 'STR' (หุ้นรับโอน - Transfer shares)
'BF' -> 'B/F' (ยอดยกมา - Balance forward)
'MS' -> 'SPM' (หุ้รายเดือน - Monthly shares)
'PM' -> 'SPM' (หุ้นรายเดือน - Monthly shares)
'PX' -> 'SPX' (ซื้อหุ้นพิเศษ - Special share purchase)
```

#### Keeping Item Mapping (`mapkepitem.sql`)
```sql
-- Legacy to new keeping item code mapping
'CM   ' -> 'CRM' (Commission/การกำกับดูแล)
'FEEF ' -> 'FFE' (Fee/ค่าธรรมเนียม)
'MD01 ' -> 'D00' (Member deposit type 1)
'MD02 ' -> 'D02' (Member deposit type 2)
'MD14 ' -> 'D01' (Member deposit type 14->1)
'ML01 ' -> 'L01' (Member loan type 1)
'ML02 ' -> 'L02' (Member loan type 2)
'ML03 ' -> 'L03' (Member loan type 3)
'MR   ' -> 'MRT' (Member receipt)
```

## 5. MCC Business Logic Analysis

### 5.1 Core Business Modules

#### PCKeeping (Bookkeeping) - 14.3MB
- **Primary Function**: Complete bookkeeping system for MCC cooperative
- **Key Features**: 
  - Transaction recording and validation
  - Account balance management
  - Financial statement preparation
  - Audit trail maintenance

#### PCDeposit (Deposit Management) - 11.9MB  
- **Primary Function**: Member deposit account management
- **Key Features**:
  - Deposit account creation and maintenance
  - Interest calculation and posting
  - Withdrawal processing
  - Statement generation

#### PCLoan (Loan Management) - 9.0MB
- **Primary Function**: Cooperative loan system
- **Key Features**:
  - Loan application processing
  - Interest and payment scheduling
  - Collateral management
  - Loan portfolio reporting

#### PCFinance (Financial Operations) - 8.5MB
- **Primary Function**: Financial operations and reporting
- **Key Features**:
  - Financial statement preparation
  - Budget management
  - Cash flow analysis
  - Regulatory reporting

### 5.2 Cooperative-Specific Features

#### PCMbshr (Member Shares) - 693KB
- **Primary Function**: Member share capital management
- **Key Features**:
  - Share subscription and redemption
  - Share value calculations
  - Dividend distribution
  - Member equity tracking

#### PCDivavg (Dividend Averaging) - 3.9MB
- **Primary Function**: Dividend calculation and distribution
- **Key Features**:
  - Annual dividend calculation
  - Member entitlement determination
  - Distribution processing
  - Tax withholding management

#### PCInsurance (Insurance) - 1.7MB
- **Primary Function**: Member insurance management
- **Key Features**:
  - Insurance policy management
  - Premium collection
  - Claims processing
  - Beneficiary management

#### PCInvertment (Investment) - 1.3MB
- **Primary Function**: Cooperative investment management
- **Key Features**:
  - Investment portfolio management
  - Return calculations
  - Risk assessment
  - Investment reporting

## 6. MCC Batch Processing Workflows

### 6.1 End-of-Day Processing
The MCC system implements automated batch processing through the PBProcess modules:

1. **Transaction Validation** (PCCommon)
   - Data integrity checks
   - Balance validation
   - Transaction matching

2. **Interest Calculation** (PCDeposit, PCLoan)
   - Daily interest accrual
   - Compound interest calculations
   - Rate application

3. **Statement Generation** (PCKeeping)
   - Account statements
   - Transaction summaries
   - Balance confirmations

4. **Regulatory Reports** (PCFinance)
   - Compliance reporting
   - Statistical returns
   - Audit preparations

### 6.2 Month-End Processing
1. **Dividend Calculations** (PCDivavg)
2. **Financial Statement Preparation** (PCFinance)
3. **Member Statement Distribution** (PCMbshr)
4. **Insurance Premium Processing** (PCInsurance)

### 6.3 Year-End Processing
1. **Annual Dividend Distribution** (PCDivavg)
2. **Tax Reporting** (PCFinance)
3. **Member Equity Adjustments** (PCMbshr)
4. **System Maintenance** (PCCommon)

## 7. MCC Report System

### Location
`/root/gcoop_hermes/mcc/GCOOP/PBReport125/`

### MCC-Specific Report Modules
```
reportfinancemcc.pbl   - MCC financial reports
reportdepositmcc.pbl   - MCC deposit reports  
reportmbshr_mcc.pbl    - MCC member share reports
reportshrlon.pbl       - Share loan reports
```

### Report Dependencies
The MCC reporting system extends the core GCOOP reporting framework with MCC-specific business logic and regulatory requirements.

## 8. Windows Integration Components

### Location
`/root/gcoop_hermes/mcc/winLKE/`

### Components
- **winMagneticBook** - C# .NET application for magnetic book management
- **Oracle.DataAccess.dll** - Oracle database connectivity
- **Device Drivers** - Hardware integration for card readers and devices

## 9. Technical Architecture Summary

### PowerBuilder Version
- **Framework**: PowerBuilder Classic (based on .pbw/.pbt format)
- **Database**: Oracle with Thai character support (TH8TISASCII)
- **Libraries**: 65 .pbl source libraries, 51 .pbd compiled libraries

### Integration Points
1. **Database Integration**: Oracle with multiple connection profiles
2. **Report Integration**: Specialized MCC reporting modules
3. **Conversion Pipeline**: Legacy system migration tools
4. **Windows Integration**: .NET components for extended functionality

### Security and Compliance
- Thai language support through TH8TISASCII charset
- Multi-branch support with branch control validation
- Audit trail maintenance through PCKeeping module
- Regulatory compliance through specialized reporting modules

## 10. Development and Maintenance Considerations

### Key Strengths
1. **Modular Design**: Clear separation of business logic by functional area
2. **Thai Language Support**: Proper charset configuration for Thai cooperative requirements
3. **Comprehensive Coverage**: Complete cooperative financial management system
4. **Integration Ready**: Multiple database and system integration points

### Areas for Attention
1. **Legacy System Integration**: Conversion pipeline indicates ongoing legacy migration
2. **Complex Dependencies**: Extensive library interdependencies require careful change management
3. **Multi-Environment Configuration**: Multiple database connections require environment-specific deployment
4. **PowerBuilder Framework**: Classic PowerBuilder requires specialized maintenance skills

---

## Conclusion

The MCC PowerBuilder system represents a comprehensive cooperative financial management platform specifically tailored for Thai cooperative requirements. With over 116 library files totaling significant codebase size, the system provides complete coverage of cooperative operations from member management through complex financial processing, reporting, and regulatory compliance.

The system's strength lies in its modular architecture, comprehensive business logic coverage, and proper Thai language support, making it well-suited for MCC's operational requirements as a Thai cooperative organization.