# Schema — สหกรณ์ MCC

**Schema กลางใช้ร่วมทุกสหกรณ์:** [`../_shared/SCHEMA.md`](../_shared/SCHEMA.md)
**Taxonomy:** [`../_shared/TAXONOMY.md`](../_shared/TAXONOMY.md)
**Naming:** [`../_shared/NAMING.md`](../_shared/NAMING.md)
**Coverage:** [`../_shared/COVERAGE.md`](../_shared/COVERAGE.md)

อ่าน schema กลางก่อนเสมอ — ไฟล์นี้อธิบายเฉพาะส่วนที่ต่างของสหกรณ์นี้

## ส่วนที่ต่างของ MCC

```yaml
coop: mcc
```

- **Vault path:** `/root/gcoop_hermes/coops/mcc`
- **Git repo:** https://github.com/visual-coop/gcoop-mcc-wiki
- **Source (SVN working copy):** `/root/gcoop_hermes/mcc`
- **SVN URL:** http://svn.coopsiam.com:8080/svnGCOOPCORE/mcc
- **Push script:** `bash /root/.hermes/scripts/mcc-wiki-autopush.sh`

## ลักษณะเฉพาะของระบบ MCC

- 4-layer architecture, ~1.7 GB
- จุดเด่นธุรกิจ: HR / RDC / Welfare
- Web: 24 โมดูล 436 ASPX, WCF 10 ตัว, Oracle x86
- iReport: 688 jrxml (ณ 2026-09-18)
- PowerBuilder: PBProcess 11 libs + iSavBOfc (PFC 10.5) + PBReport + Pipeline

รายละเอียดดู `index.md` และ `entities/gcoop-mcc.md`

## ข้อควรระวังเฉพาะ MCC

- **ห้ามเขียน path ของ MHD หรือ vault อื่นลงในหน้านี้** (กฎเดิมของโครงการ)
- หน้า `comparisons/gcoop-mcc-vs-mhd.md` เป็นหน้าเปรียบเทียบข้ามสหกรณ์ —
  ตาม `NAMING.md` ที่ถูกต้องควรอยู่ hub กลาง ยังไม่ย้าย รอตัดสินใจ
- ยังใช้ prefix `gcoop-mcc-` ในชื่อไฟล์เดิม — **ห้าม rename ยกชุด**
  เพราะ `[[wikilinks]]` จะพัง ดูแนวทางใน `NAMING.md`