# MCC Session Prompts

**Updated:** 2026-09-14  
**Project:** MCC (Member Credit Cooperative)  
**System Path:** `/root/gcoop_hermes/mcc/`

## Session Overview

เนื่องจากการวิเคราะห์ครั้งแรกใช้ MHD template ผิด จึงได้แก้ไข session prompts ทั้งหมดให้เป็น MCC-specific:

### ✅ MCC Session Prompts (Updated)

1. **session-1-mcc-web-analysis.md** - MCC Web Interface Analysis
2. **session-2-mcc-powerbuilder-analysis.md** - MCC PowerBuilder Modules  
3. **session-3-mcc-infrastructure-analysis.md** - MCC Infrastructure Tools
4. **session-4-mcc-integration-summary.md** - MCC Integration & Comparison

### ❌ Removed (MHD Templates)

- ~~session-1-mhd-system-prompt.md~~ (ลบแล้ว)
- ~~session-2-pbprocess-prompt.md~~ (ลบแล้ว)  
- ~~session-3-core-infrastructure-prompt.md~~ (ลบแล้ว)
- ~~session-4-integration-analysis-prompt.md~~ (ลบแล้ว)

## Key Changes

### Critical Path Corrections
- **Old:** `/root/gcoop_hermes/mhd/` ❌
- **New:** `/root/gcoop_hermes/mcc/` ✅

### Focus Corrections
- **Old:** MHD Cooperative ❌  
- **New:** MCC (Member Credit Cooperative) ✅

### Analysis Scope
- **MCC-specific features:** HR, RDC, Welfare systems
- **MCC project structure:** iSavBOfc, winLKE, CONVERT_MCC
- **MCC business rules:** Member Credit Cooperative focus

## Usage

All future MCC analysis sessions should use these corrected prompts to ensure:
1. Correct path references (`/mcc/` not `/mhd/`)
2. MCC-specific system analysis  
3. Proper comparison with MHD when relevant
4. Focus on MCC's distinctive features (HR, Document Management, Welfare)

---
**Status:** Ready for MCC Sessions 2-4 with correct templates