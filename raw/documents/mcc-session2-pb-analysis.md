# MCC Session 2: PowerBuilder Modules Analysis

## Overview
This document provides a comprehensive analysis of PowerBuilder components in the MCC (Member Credit Cooperative) project, focusing on .pbl/.pbd files and batch processing modules for member credit operations.

## PowerBuilder File Statistics

### Total PowerBuilder Files
- **Total .pbl/.pbd files**: 116
- **PowerBuilder Library (.pbl) files**: 65  
- **PowerBuilder Dynamic (.pbd) files**: 51

## Main PowerBuilder Application Structure

### 1. Core Applications

#### iSavBOfc (Savings Bank Office Application)
**Location**: `/root/gcoop_hermes/mcc/iSavBOfc/`
**Workspace**: `isavbofc.pbw` → `iBank/isavbofc.pbt`

**Key Components**:
- **Main Application**: `isavbofc.pbl`, `isavbofc.exe`
- **Banking Transaction Module**: `ibanktrn.pbl/.pbd`
- **Balance Check Module**: `chack_stmbal.pbl/.pbd` 
- **Import Department Module**: `iimportdept.pbl/.pbd`

#### PBProcess (Batch Processing Application)
**Location**: `/root/gcoop_hermes/mcc/GCOOP/PBProcess/`
**Workspace**: `pbprocess.pbw` → `pbprocess.pbt`
**Executable**: `pbprocess.exe`

**Core Processing Modules**:
- `pbprocess.pbl/.pbd` - Main batch processing engine
- `pccommon.pbl/.pbd` - Common utilities (2,105,856 bytes source)
- `pcaccount.pbl/.pbd` - Account processing
- `pcdeposit.pbl/.pbd` - Deposit processing (11,943,936 bytes source)
- `pcdivavg.pbl/.pbd` - Dividend average processing
- `pcfinance.pbl/.pbd` - Finance processing (8,551,424 bytes source)
- `pcinsurance.pbl/.pbd` - Insurance processing
- `pcinvertment.pbl/.pbd` - Investment processing
- `pckeeping.pbl/.pbd` - Keeping/savings processing (14,386,176 bytes source)
- `pcloan.pbl/.pbd` - Loan processing (9,054,720 bytes source)
- **`pcmbshr.pbl/.pbd`** - **Member share processing (693,760 bytes source)**

### 2. Member Credit Operations Modules

#### Business Component Services (sBussCom)
**Location**: `/root/gcoop_hermes/mcc/iSavBOfc/sBussCom/`

**Member Credit Related Files**:
- **`cmsrv_mbshr.pbl`** (200,704 bytes) - Member share services
- **`cmsrv_loan.pbl`** (1,135,616 bytes) - Loan services  
- `cmsrv_fin.pbl` (2,140,672 bytes) - Financial services
- `cmsrv_dept.pbl` (901,632 bytes) - Department services
- **`pccomloan.pbl/.pbd`** (1,254,912 bytes source, 353,792 bytes compiled) - Common loan operations

#### Base Component Services (sBaseCom)  
**Location**: `/root/gcoop_hermes/mcc/iSavBOfc/sBaseCom/`

**Core Services**:
- `cmappcom.pbl/.pbd` - Application common services
- `cmcoinit.pbl/.pbd` - Cooperative initialization
- `cmcomsrv.pbl/.pbd` - Communication services
- `cmoutsrv.pbl/.pbd` - Output services
- `cmprnsrv.pbl/.pbd` - Print services
- `cmrepsrv.pbl/.pbd` - Report services
- `cmtdtsrv.pbl/.pbd` - Transaction data services
- `cmconfig.pbl` - Configuration management
- `cmsqlsrv.pbl` - SQL services

### 3. MCC-Specific Pipeline Processing

#### MCC Pipeline Application
**Location**: `/root/gcoop_hermes/mcc/CONVERT_MCC/mcc_pipeline/`
**Workspace**: `mcc_workspace.pbw` → `mcc_pipe.pbt`
**Application Name**: `aeropipe`

**MCC Pipeline Modules**:
- **`mcc_pipe_member.pbl`** (210,944 bytes) - **Member data pipeline processing**
- `mcc_pipe_dep.pbl` (152,576 bytes) - Deposit pipeline
- `mcc_pipe_divavg.pbl` (94,208 bytes) - Dividend average pipeline  
- `mcc_pipe_insurance.pbl` (30,720 bytes) - Insurance pipeline
- `mcc_pipe_keeping.pbl` (155,136 bytes) - Keeping/savings pipeline
- `mcc_pipe_shrlon.pbl` (165,376 bytes) - Share loan pipeline
- `mcc_pipe_app.pbl` (819,712 bytes) - Main pipeline application

### 4. PowerBuilder Framework (PFC 10.5)

**Location**: `/root/gcoop_hermes/mcc/iSavBOfc/PFC105/`

**Framework Components**:
- `pfcmain.pbl/.pbd` - Main framework
- `pfcutil.pbl/.pbd` - Utilities
- `pfcapsrv.pbl/.pbd` - Application services
- `pfcdwsrv.pbl/.pbd` - DataWindow services
- `pfcwnsrv.pbl/.pbd` - Window services
- `pfemain.pbl/.pbd` - Extended main
- `pfeutil.pbl/.pbd` - Extended utilities
- `pfeapsrv.pbl/.pbd` - Extended application services
- `pfedwsrv.pbl/.pbd` - Extended DataWindow services
- `pfewnsrv.pbl/.pbd` - Extended window services
- `service.pbd` - Service components

## Member Credit Operations Batch Processing

### 1. Primary Batch Processing Modules

#### Member Share Processing (`pcmbshr.pbl`)
**Purpose**: Processes member share transactions, calculations, and updates
**Size**: 693,760 bytes (source), 223,232 bytes (compiled)
**Integration**: Part of main PBProcess application

#### Loan Processing (`pcloan.pbl`) 
**Purpose**: Handles loan calculations, interest computations, payment processing
**Size**: 9,054,720 bytes (source), 2,787,840 bytes (compiled)
**Integration**: Core component of batch processing system

#### Member Pipeline Processing (`mcc_pipe_member.pbl`)
**Purpose**: Specialized MCC member data conversion and migration
**Size**: 210,944 bytes
**Context**: Part of MCC-specific data pipeline for member operations

### 2. Supporting Batch Components

#### Deposit Processing (`pcdeposit.pbl`)
**Purpose**: Member deposit calculations, interest accrual
**Size**: 11,943,936 bytes (largest processing module)
**Member Integration**: Processes member savings and fixed deposits

#### Keeping/Savings Processing (`pckeeping.pbl`) 
**Purpose**: Member keeping account processing, monthly calculations
**Size**: 14,386,176 bytes (largest module overall)
**Member Integration**: Core for member savings operations

#### Financial Processing (`pcfinance.pbl`)
**Purpose**: Financial reporting, balance calculations for member accounts
**Size**: 8,551,424 bytes
**Member Integration**: Generates member financial statements

### 3. Business Services for Member Credit

#### Member Services (`cmsrv_mbshr.pbl`)
**Purpose**: Core member share business logic and data access
**Size**: 200,704 bytes
**Architecture**: Business service layer for member operations

#### Loan Services (`cmsrv_loan.pbl`)
**Purpose**: Loan business rules, validation, and processing logic  
**Size**: 1,135,616 bytes
**Architecture**: Business service layer for credit operations

#### Common Loan Operations (`pccomloan.pbl`)
**Purpose**: Shared loan processing functions and utilities
**Size**: 1,254,912 bytes (source)
**Usage**: Referenced by multiple loan processing components

## Web Application Integration

### Member Web Applications
**Location**: `/root/gcoop_hermes/mcc/GCOOP/Saving/Applications/mbshr/`

**Key Web Components**:
- `ws_mem_memberdetail_ctrl` - Member detail management
- `ws_mem_adjmember_detail_ctrl` - Member adjustment controls
- Supporting controls for:
  - Share statements (`wd_stm_share_ctrl`)
  - Loan statements (`wd_stm_loan_ctrl`) 
  - Deposit statements (`wd_stm_dept_ctrl`)
  - Keeping statements (`wd_stm_keep_ctrl`)
  - Member status management (`wd_mem_status_ctrl`)

## Database Integration

### Oracle Database Support
**Database**: ISCOMCC (Oracle 12.2.0)
**Encoding**: TH8TISASCII (Thai language support)
**Import Script**: `ImpOracleDB.bat`

**Database Operations**:
- Data pump import/export for member data migration
- Tablespace management for MCC member data
- Schema remapping for cooperative data structures

### DataWindow Components
**Location**: `/root/gcoop_hermes/mcc/GCOOP/Saving/DataWindow/`

**Member-Related DataWindows**:
- Share loan processing (`Shrlon/sl_shlnproc.pbl`)
- Member browse and search functionality
- Account processing windows
- Dividend average calculations

## Reporting System

### PowerBuilder Reports
**Location**: `/root/gcoop_hermes/mcc/GCOOP/PBReport125/`

**MCC-Specific Reports**:
- `reportmbshr_mcc.pbl/.pbd` - Member share reports for MCC
- `reportfinancemcc.pbl/.pbd` - Financial reports for MCC  
- `reportdepositmcc.pbl/.pbd` - Deposit reports for MCC
- `reportshrlon.pbl/.pbd` - Share loan reports
- `reportmbshare.pbl` - Member share reporting

## Architecture Summary

### Batch Processing Flow
1. **PBProcess** (`pbprocess.exe`) serves as the main batch processing engine
2. **Member Processing** handled by `pcmbshr.pbl` for share calculations
3. **Credit Processing** managed by `pcloan.pbl` for loan operations
4. **Business Services** (`cmsrv_mbshr.pbl`, `cmsrv_loan.pbl`) provide data access
5. **Pipeline Processing** (`mcc_pipe_member.pbl`) handles data conversion/migration

### Integration Points
1. **Database Layer**: Oracle ISCOMCC database with Thai language support
2. **Business Logic Layer**: PowerBuilder business services (cmsrv_*)
3. **Processing Layer**: Batch processing modules (pc*)
4. **Presentation Layer**: Web applications and DataWindows
5. **Reporting Layer**: PowerBuilder reports and iReport integration

### Member Credit Operation Workflow
1. **Data Input**: Web applications capture member transactions
2. **Validation**: Business services validate member credit operations
3. **Batch Processing**: Overnight/scheduled processing via PBProcess
4. **Interest Calculation**: Automated calculations for deposits, loans, shares
5. **Statement Generation**: Member account statements and reports
6. **Integration**: Pipeline processing for data migration and conversion

## Technical Notes

### PowerBuilder Version
- Framework: PFC 10.5 (PowerBuilder Foundation Class 10.5)
- Save Format: v3.0 (19990112) - Indicates PowerBuilder 7.0+ compatibility
- Architecture: Client/server with Oracle database backend

### File Size Analysis
- **Largest Processing Module**: `pckeeping.pbl` (14.4 MB) - Member savings processing
- **Largest Business Module**: `pcdeposit.pbl` (11.9 MB) - Deposit processing  
- **Member-Specific**: `pcmbshr.pbl` (694 KB) - Focused member share processing
- **Credit-Specific**: `pcloan.pbl` (9.1 MB) - Comprehensive loan processing

### Development Environment
- **IDE**: PowerBuilder IDE (version determined by PFC 10.5 compatibility)
- **Database**: Oracle 12.2.0 with Thai character set support
- **Framework**: PowerBuilder Foundation Classes for standardized development
- **Architecture**: 3-tier with presentation, business logic, and data layers

---

**Analysis Date**: September 14, 2026  
**Analyst**: Hermes Agent - GCOOP Project Analysis  
**Session**: MCC PowerBuilder Components Analysis (Session 2)