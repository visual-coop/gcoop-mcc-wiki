# MCC Infrastructure Tools Analysis - Session 3

## Executive Summary

This document provides a comprehensive analysis of MCC (Savings Cooperative) Infrastructure Tools discovered during source code investigation. The analysis focuses on the technical infrastructure, deployment architecture, and supporting tools that enable MCC's cooperative operations.

## 1. MCC winUPBOOK (Passbook Update Tool)

### 1.1 Technical Architecture
- **Framework**: VB.NET (.NET Framework 4.0 Client Profile)
- **IDE**: Visual Studio 2010
- **Platform**: x86 (32-bit) architecture
- **Deployment**: ClickOnce deployment with versioning system (1.0.0.x)

### 1.2 Core Components
- **Main Solution**: `winUPBOOK.sln` with single VB.NET project
- **Database Access**: Oracle.DataAccess.Client (ODP.NET) for Oracle database connectivity
- **COM Integration**: PEMLib COM component for passbook printer hardware interface
- **Key Modules**:
  - `Form1.vb` - Main user interface and printing logic
  - `UpdateBook.vb` - Core passbook update functionality 
  - `Connection.vb` - Database connection management
  - `Base.vb` - Base class with common functionality

### 1.3 Database Integration
- **Connection Method**: Oracle ODP.NET with connection string configuration
- **Connection File**: External configuration via `C:\GCOOP_ALL\AERO\winUPBOOK\conn.dat`
- **Sample Connection**: Oracle database on `10.20.240.79:1521/GCOOP` using `iscoegat` schema
- **Logging**: PBLog table for audit trail tracking

### 1.4 Deployment Infrastructure
- **ClickOnce**: Auto-update mechanism with application manifests
- **Publishing Path**: `C:\GCOOP_ALL\AERO\winUPBOOK\pbUpbook\`
- **Update URL**: `http://localhost/winUPBOOK/`
- **Version Management**: Multiple version deployments (1.0.0.5 through 1.0.0.9)

## 2. MCC winLKE (Magnetic Card Reader)

### 2.1 Technical Architecture
- **Framework**: C# .NET Framework 4.6.1
- **IDE**: Visual Studio 2010
- **Platform**: Any CPU architecture
- **Hardware Integration**: Magnetic stripe reader support via Msre.ocx

### 2.2 Core Components
- **Main Solution**: `winMagneticBook.sln`
- **Main Application**: `winMagneticBook.csproj` (C# WinForms)
- **Hardware Interface**: `Msre.ocx` ActiveX control for magnetic card reading
- **Driver Package**: `Driver LKE.rar` (31MB driver package)

### 2.3 System Integration
- **Oracle Database**: Oracle.DataAccess.Client for data operations
- **WIN32 API**: User32.dll imports for window management and positioning
- **COM Interop**: ActiveX integration for magnetic stripe reader hardware

### 2.4 Deployment Structure
- **ClickOnce**: Similar deployment pattern to winUPBOOK
- **Binary Output**: `winLKE.exe` executable
- **Driver Support**: Dedicated drivers directory for hardware compatibility

## 3. MCC PLSQL Database Architecture

### 3.1 PL/SQL Structure Organization
- **Base Path**: `/mcc/GCOOP/PLSQL/`
- **Importer Tool**: `PLImporter.exe` with configuration-driven deployment
- **Structure Categories**:
  - `TYPES/` - Custom Oracle types and object definitions
  - `PACKAGES/` - Packaged procedures and functions
  - `PROCEDURES/` - Standalone stored procedures
  - `FUNCTIONS/` - Standalone functions

### 3.2 Key Database Components

#### 3.2.1 Custom Types (TYPES/)
- `str_datacolumn.pls` - Data column structure definitions
- `ntb_*.pls` - Nested table types (vc999, vc30, datacolumn)
- `secure.pls` - Security-related type definitions
- `datasourcetool.pls/.body.pls` - Data source management objects

#### 3.2.2 Package Library (PACKAGES/)
- `fpb.pls/.body.pls` - Financial passbook operations
- `n_pk_doccontrol.pls/.body.pls` - Document control management
- `n_pk_lnnpl.pls/.body.pls` - Loan NPL (Non-Performing Loan) operations
- `n_pk_string.body.pls` - String manipulation utilities

### 3.3 Deployment Configuration
- **Configuration**: `PLImporter.config.dist`
- **Target Database**: Oracle 192.168.10.93/iorcl with `iscotks` schema
- **Deployment Strategy**: 
  - Drop all existing objects before import (configurable)
  - Supports Types, Packages, Functions, Procedures, and Triggers
  - Debug mode available for troubleshooting

## 4. MCC iReport System

### 4.1 Report Infrastructure
- **Base Path**: `/mcc/GCOOP/iReport/`
- **Report Count**: 2000+ JRXML report templates
- **Builder Tool**: ReportBuilderCORE.jar integration
- **Launcher**: `run_ireport_builder.bat` automation script

### 4.2 Report Categories
- **Check Templates**: Bank-specific check printing (Krungthai, TMB)
- **Slip Reports**: Transaction slip generation (`dw_slipopen_name`)
- **Acceptance Reports**: Book acceptance processing (`ir_accept_book`)
- **Budget Reports**: Educational budget processing series
- **Financial Reports**: Loan, savings, and cooperative operations

### 4.3 Report Management System
- **Report Builder**: Java-based CORE report builder
- **Process Management**: PowerShell-based instance control
- **Auto-termination**: Previous instance cleanup before new startup
- **Parameters**: `MCC 30 "DRIVE:\GCOOP_ALL" CORE GCOOP`

### 4.4 Resource Management
- **Pictures Directory**: Shared image assets for reports
- **Template Storage**: Organized JRXML and compiled Jasper files
- **Version Control**: Source JRXML with compiled Jasper counterparts

## 5. MCC XMLConfig Configuration Management

### 5.1 Configuration Architecture
- **Base Path**: `/mcc/GCOOP/XMLConfig/`
- **PowerBuilder Integration**: xmlconfig.pbl library (1.4MB)
- **Executable Tools**: xmlconfig.exe for configuration management

### 5.2 Core Configuration Files

#### 5.2.1 Database Connections (`server.connection_string.xml`)
- **Multi-Profile Support**: Default@Cloud, MCC@Siam, MCC@Dept profiles
- **Connection Targets**:
  - Cloud: `43.229.79.117/gcoop` with `iscomrc` schema
  - Siam: `web.siamcoop.com/gcoop` with `iscomrc` schema
  - Test Environment: `iscotestmrc` schema for testing

#### 5.2.2 System Constants (`xmlconf.constmap.xml`)
- **Applet Configuration**: PBSlip auto-update settings
- **Client Paths**: `C:\PBSLIP_MCC\` local installation
- **Server Paths**: `http://mcc.siamcoop.com/MCC/GCOOP/Saving/SlipPB/SlipPB/`
- **Logging Control**: Central logging system configuration
- **Screen Capture**: Audit trail and debugging support

#### 5.2.3 Printing Configuration
- **Form Codes**: `print.formcode.xml` - Print form definitions
- **Form Mapping**: `print.formmap.xml` - Form-to-template mapping
- **User Mapping**: `print.usermap.xml` - User-specific printing preferences
- **Report Integration**: `print.report.xml` - Report system integration

#### 5.2.4 WCF Service Configuration (`server.wcf_detail.xml`)
- **Service Architecture**: Windows Communication Foundation integration
- **Service Endpoints**: Defined service communication points
- **Security Configuration**: Service-level security settings

### 5.3 Loan Management Configuration
Multiple XML configuration files for loan type management:
- **Attribute Management**: `d_ln_loan_type_attrib_xmlconfig.xml`
- **Collection Rules**: `d_ln_loan_type_budycoll_xmlconfig.xml`
- **Interest Processing**: `d_ln_loan_type_intclear_xmlconfig.xml`
- **Salary Requirements**: `d_ln_loan_type_minsalary_xmlconfig.xml` (52KB config)
- **Reverse Operations**: `d_ln_loan_type_reverse_xmlconfig.xml` (577KB config)

## 6. MCC Database Architecture

### 6.1 Database Platform
- **Primary DBMS**: Oracle Database
- **Connection Architecture**: ODP.NET (Oracle Data Provider for .NET)
- **Schema Design**: Multi-tenant with schema-based isolation

### 6.2 Database Environments
- **Production**: `web.siamcoop.com/gcoop` (MCC@Siam)
- **Cloud**: `43.229.79.117/gcoop` (Default@Cloud, MCC@Dept)
- **Development**: Local instances with dedicated schemas

### 6.3 Key Database Objects
- **Audit Tables**: PBLog for passbook operation tracking
- **Configuration Tables**: DPDEPTCONSTANT for system constants
- **Loan Management**: Extensive loan processing table structure
- **Document Control**: Centralized document management system

## 7. MCC Deployment Infrastructure

### 7.1 Application Deployment
- **ClickOnce Technology**: Primary deployment mechanism for desktop applications
- **Auto-Update**: Automatic version management and updates
- **Central Distribution**: HTTP-based application distribution

### 7.2 File System Architecture
- **Root Path**: `C:\GCOOP_ALL\`
- **Application Structure**:
  - `AERO\` - Desktop applications (winUPBOOK)
  - `CORE\GCOOP\` - Core libraries and services
  - `MCC\GCOOP\` - MCC-specific extensions

### 7.3 Network Architecture
- **Production Domain**: `mcc.siamcoop.com`
- **Internal Networks**: 10.20.240.x subnet for database servers
- **Cloud Infrastructure**: `43.229.79.117` for cloud services

### 7.4 Hardware Integration
- **Passbook Printers**: COM-based PEMLib integration
- **Magnetic Card Readers**: ActiveX-based hardware interface
- **Network Printing**: Centralized print server support

## 8. MCC CONVERT_MCC (Data Migration Tools) - Status

### 8.1 Investigation Results
**No dedicated CONVERT_MCC tools were found** in the current MCC infrastructure analysis. The data migration capabilities appear to be integrated into the existing systems rather than maintained as separate tools.

### 8.2 Alternative Migration Mechanisms
- **PLImporter.exe**: Database schema migration and deployment
- **XMLConfig System**: Configuration data migration and synchronization
- **ClickOnce Updates**: Application binary migration and updates

## 9. Security and Compliance

### 9.1 Database Security
- **Schema Isolation**: Separate schemas for different environments
- **Connection Security**: Encrypted connection strings with credential management
- **Audit Logging**: Comprehensive operation logging with IP tracking

### 9.2 Application Security
- **Code Signing**: ClickOnce applications with certificate-based signing
- **Hardware Integration**: Secure COM/ActiveX interfaces for hardware access
- **Configuration Protection**: External configuration file security

## 10. Maintenance and Operations

### 10.1 System Monitoring
- **Central Logging**: Configurable logging system with screen capture
- **Process Management**: Automated process lifecycle management
- **Version Control**: Comprehensive version tracking and rollback capabilities

### 10.2 Operational Procedures
- **Database Deployment**: Automated PL/SQL deployment with rollback support
- **Application Updates**: Seamless ClickOnce-based application updates
- **Configuration Management**: Centralized XML-based configuration deployment

## 11. Technical Dependencies

### 11.1 Platform Requirements
- **Operating System**: Windows (32-bit application support required)
- **Framework**: .NET Framework 4.0+ (Client Profile supported)
- **Database**: Oracle Database with ODP.NET support
- **Java Runtime**: Required for iReport ReportBuilderCORE.jar

### 11.2 Hardware Requirements
- **Passbook Printers**: PEMLib COM-compatible printers
- **Magnetic Card Readers**: ActiveX-compatible stripe readers
- **Network Infrastructure**: Stable connectivity for cloud/remote database access

## Conclusion

The MCC infrastructure represents a comprehensive cooperative management system with robust deployment, configuration, and operational capabilities. The architecture demonstrates mature enterprise patterns with proper separation of concerns, automated deployment mechanisms, and extensive configuration management. The lack of dedicated CONVERT_MCC migration tools suggests that data migration is handled through the integrated PLImporter and configuration management systems rather than standalone utilities.

The system architecture supports the operational needs of a savings cooperative with integrated passbook printing, magnetic card reading, comprehensive reporting, and centralized configuration management. The deployment infrastructure ensures reliable application distribution and updates across the cooperative's operational environment.

---
*Analysis completed: Infrastructure Tools Review*  
*Source: MCC /root/gcoop_hermes/mcc/ directory structure*  
*Focus: Infrastructure components, deployment architecture, and system integration*