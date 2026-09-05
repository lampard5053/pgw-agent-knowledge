# PGW Knowledge & Agent Base

Repo นี้คือ **คลังความรู้ + ต้นทางของ Claude Skill** สำหรับผู้ช่วย AI (agent/skill) ของโปรเจค Payment Gateway (PGW)

**แยกต่างหากจาก repo หลัก** [`lampard5053/PaymentGateway`](https://github.com/lampard5053/PaymentGateway) ซึ่งเป็นเอกสารทางการของทีม (mkdocs docs + PDF export) — แต่เนื้อหาส่วนใหญ่ **sync มาจาก repo หลัก ณ วันที่สร้าง repo นี้** แล้วเพิ่มเนื้อหาเฉพาะสำหรับให้ agent ใช้งานเข้าไปด้วย

## ทำไมต้องแยก repo

- Repo หลัก = เอกสารทางการที่ทีมดูแล ใช้สร้างเว็บ/PDF ให้คนอ่าน
- Repo นี้ = ความรู้ที่ตัดต่อ/จัดรูปแบบให้ **agent (Claude) ใช้ค้น-ตอบ-เรียนรู้ต่อเนื่อง** ได้ ซึ่งมีเนื้อหาเพิ่มที่ไม่มีใน repo หลัก (case log, contradictions log, index สำหรับค้นไว ฯลฯ)
- ทำให้ทั้งสอง repo พัฒนาไปคนละจังหวะได้โดยไม่ชนกัน แต่ยังอ้างอิงเนื้อหาเดียวกันได้

## โครงสร้าง

```
.
├── docs/                          ← เนื้อหา mkdocs (เว็บอ่านได้จริง ผ่าน `mkdocs serve`/`mkdocs build`)
│   ├── INDEX.md                   ← สารบัญค้นไว สำหรับ agent (ไม่มีใน repo หลัก)
│   ├── case-log.md                ← เคสจริงจากบทสนทนา สะสมต่อเนื่อง (ไม่มีใน repo หลัก)
│   ├── contradictions-log.md      ← จุดที่ข้อมูลขัดแย้งกัน สะสมต่อเนื่อง (ไม่มีใน repo หลัก)
│   ├── open-questions.md          ← รวมทั้งฝั่งเทคนิคและฝั่งทีมปฏิบัติการ
│   ├── opsdash/, watchtower/, console/, pgway/, api/, support/   ← เนื้อหาเทคนิค (sync จาก repo หลัก)
│   │   └── support/team-operations.md   ← คน/องค์กร/กระบวนการทำงาน (ไม่มีใน repo หลัก)
│   └── assets/                    ← รูปภาพประกอบทั้งหมด (screenshots + เคสจริง)
├── mkdocs.yml                     ← config สำหรับ build เว็บจาก docs/
└── skill/
    └── payment-gateway-knowledge/ ← ตัว Claude Skill ที่ติดตั้งใช้งานได้จริง (SKILL.md + references + assets)
                                      เนื้อหาเดียวกับ docs/ แต่จัดโครงสร้างสำหรับ Claude โดยเฉพาะ
```

## วิธีอัปเดตเมื่อมีความรู้ใหม่

1. เพิ่ม/แก้ไฟล์ใน `docs/` (และ/หรือ `skill/payment-gateway-knowledge/references/` ให้ตรงกัน)
2. อัปเดต `docs/INDEX.md` ให้ชี้ไปไฟล์ใหม่เสมอ — ไม่งั้น agent จะหาไม่เจอ
3. ถ้าเจอข้อมูลขัดแย้งกับของเดิม → บันทึกใน `docs/contradictions-log.md` ก่อนเสมอ อย่าเขียนทับเงียบๆ
4. commit + push ตามปกติ

## เกี่ยวกับ Claude Skill

โฟลเดอร์ `skill/payment-gateway-knowledge/` คือสกิลที่ใช้งานจริงกับ Claude — สามารถแพ็กเป็นไฟล์ `.skill` เดียวเพื่อแจกจ่ายให้คนอื่นติดตั้งได้ ดูรายละเอียดกติกาการทำงานของ agent ทั้งหมดใน [`skill/payment-gateway-knowledge/SKILL.md`](skill/payment-gateway-knowledge/SKILL.md)
