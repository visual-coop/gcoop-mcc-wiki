# GCOOP MCC System Wiki

## Overview

Knowledge base for **MCC (Member Credit Cooperative)** system - สหกรณ์ออมทรัพย์ MCC ที่ใช้ระบบ GCOOP

**Project Location:** `/root/gcoop_hermes/mcc/`  
**Wiki Location:** `/root/gcoop_hermes/gcoop-mcc-wiki/`  
**System Type:** สหกรณ์ออมทรัพย์และสินเชื่อสมาชิก

## System Architecture

- **SVN Repository:** http://svn.coopsiam.com:8080/svnGCOOPCORE/mcc
- **Current Revision:** 1997 (Latest checkout 2026-09-14)
- **Size:** 1.7GB working copy
- **Technologies:** C#.NET (ASP.NET + WCF), PowerBuilder, Oracle Database

## Analysis Complete ✅

### MCC Analysis Sessions (All Complete):
1. **✅ Session 1:** MCC Web Interface Analysis (7.9KB)
2. **✅ Session 2:** MCC PowerBuilder Modules (10.2KB)  
3. **✅ Session 3:** MCC Infrastructure Tools (15.3KB)
4. **✅ Session 4:** MCC Integration Analysis (23.9KB)

**Total Analysis:** 57.3KB comprehensive MCC documentation

## System Components

- **Web Application:** 177MB, 6,721 files - ASP.NET web interfaces
- **PowerBuilder:** 67MB, 26 files - Batch processing modules
- **Reports:** 56MB, 1,345 templates - iReport 5.0.4 reporting
- **Infrastructure:** Various tools (iSavBOfc, winUPBOOK, winLKE)

## Key Differences from MHD

- **13% larger** than MHD cooperative (491MB vs 436MB)
- **Comprehensive features:** Insurance, Welfare, Document Management  
- **Modern approach:** 10 Oracle configs vs MHD's legacy focus
- **PowerBuilder core identical** to MHD (100% match)

## Integration with GCOOP

- **Shared Infrastructure:** Core WCF services, Oracle database
- **Same Technology Stack:** ASP.NET + PowerBuilder + Oracle
- **Cross-compatible:** Can share services with MHD system
- **85% architectural similarity** with MHD cooperative

---

**Created:** 2026-09-14  
**Last Updated:** 2026-09-14  
**Status:** Complete analysis, separate wiki vault
**Comparison:** See cooperative-comparison-analysis.md in MHD wiki