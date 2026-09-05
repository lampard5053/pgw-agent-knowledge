# Case Log — เคสจริงจากบทสนทนา (สะสมต่อเนื่อง)

> ไฟล์นี้ต่างจากไฟล์อื่นในสกิล: เนื้อหาในนี้ **ไม่ได้มาจากการสำรวจระบบแบบเป็นระบบ** แต่มาจากเคสจริง/บทสนทานจริงที่เกิดขึ้นระหว่างใช้งานสกิลนี้ — แต่ละเคสคือ "หลักฐานสด" ที่บางครั้งขัดแย้งหรือเติมเต็มช่องว่างที่ `open-questions.md` ทิ้งไว้
>
> **กฎการเก็บเคส:** ทุกครั้งที่เจอเคสใหม่ในบทสนทนา (ปัญหาจริง, ฟิลด์ที่ไม่เคยเจอมาก่อน, พฤติกรรมระบบที่ขัดกับที่เอกสารอื่นบอกไว้) ให้บันทึกเพิ่มต่อท้ายไฟล์นี้อัตโนมัติ ไม่ต้องรอผู้ใช้สั่ง — ใช้ template ด้านล่าง และแนบภาพประกอบ (ถ้ามี) ไว้ใน `../assets/case-log/`
>
> ถ้าเคสไหนขัดแย้งกับข้อสรุปในไฟล์ reference อื่น ให้ใส่ลิงก์ไขว้ (cross-reference) ทั้งสองทาง: ที่นี่ลิงก์ไปไฟล์นั้น และถ้าเป็นไปได้เพิ่มบันทึกสั้นๆ ในไฟล์นั้นด้วยว่า "ดูเคสจริงใน case-log.md"

## Template สำหรับเคสใหม่

```
## [YYYY-MM-DD] หัวข้อเคสสั้นๆ

**บริบท:** ใครถามอะไร / เกิดอะไรขึ้น
**หลักฐาน:** ภาพ/ข้อความที่ยืนยัน (แนบไฟล์ใน assets/case-log/ ถ้ามีภาพ)
**สรุปสิ่งที่ได้เรียนรู้:** ข้อเท็จจริงใหม่ หรือช่องว่างที่ยังไม่มีคำตอบ
**เกี่ยวข้องกับ:** ลิงก์ไปไฟล์ reference ที่เกี่ยวข้อง (ถ้ามี)
**สถานะ:** ยืนยันแล้ว / คาดว่า (ยังไม่ยืนยัน) / open question
```

---

## [2026-09-05] GSB — ฟิลด์ "Polling Type" ใน OpsDash ไม่เคยถูกบันทึกไว้ในเอกสารฉบับเต็ม + เจอบั๊ก Save ไม่ persist

**บริบท:** ทีม Account ติดตั้งบัญชี GSB (`101-mtsen-gsb-Laddawan-020370603340`) ใหม่ผ่าน OpsDash แบบ Polling Type = Normal แล้วมีปัญหา — escalate เข้ากลุ่ม Telegram "GG-ACC"

**หลักฐาน:**
- ภาพหน้าจอ OpsDash Edit Account modal จริง — `../assets/case-log/2026-09-05_gsb-polling-type-opsdash-edit-form.jpg`
- ภาพแชท Telegram รายงานปัญหา — `../assets/case-log/2026-09-05_gsb-polling-type-telegram-report.png`

**สรุปสิ่งที่ได้เรียนรู้:**

1. **ฟิลด์ใหม่ที่ไม่เคยมีในเอกสาร:** section "GSB Specific Fields" ของ Bank Account Edit modal มีฟิลด์ `Outbound Transfer Type` (ค่าที่เห็น: `manual-bot`) และ `Polling Type` (ค่าที่เห็น: `Normal`, ทีมเคยลองเปลี่ยนเป็น `Line`) เพิ่มเติมจากฟิลด์เดิมที่เอกสาร (`opsdash/bank-accounts.md`) เคยสำรวจไว้แค่ Appium Bot URL / GSB Server Mobile Token / Device ID / PIN — แปลว่า UI มีการอัปเดตฟิลด์เพิ่มหลังจากทำเอกสารฉบับนี้เสร็จ
2. **ความหมายของ "Polling Type: Normal" ยังไม่ยืนยัน 100%** — อนุมานจากบริบทกว้างๆ ของระบบ (Health="Polling Limit", Notification="Polling Failed", auto-poll ผ่าน Bot/Appium ที่เอกสารยืนยันไว้แล้ว) ว่าน่าจะหมายถึงโหมด poll ตามรอบเวลาอัตโนมัติปกติ ต่างจากโหมดอื่นที่ยังไม่รู้ชัด (เช่น "Line")
3. **บั๊กที่เจอจริง:** กด Save ในฟอร์มนี้ได้ ไม่ error, Network log ยืนยัน response 200 แต่ **ข้อมูลไม่ถูกเปลี่ยนจริงหลัง reload** (ลองเปลี่ยน Polling Type จาก Normal → Line แล้ว Save ก็ยังเห็นค่าเดิม) — ยังไม่สรุปว่าเป็นบั๊กฝั่ง backend (ไม่ persist ค่า) หรือ frontend (cache ค่าเก่า ไม่ re-fetch หลัง save) ต้อง hard refresh ตัดตัวแปรก่อน
4. ผลกระทบที่ตามมา: Balance ไม่ขึ้นฝั่ง Watch Tower และเจอ Transaction Fail

**เกี่ยวข้องกับ:**
- `opsdash/bank-accounts.md` — ควรเพิ่ม section "GSB Specific Fields" ฉบับสมบูรณ์เมื่อยืนยันได้
- `watchtower/accounts.md` — Health = "Polling Limit" / Notification "Polling Failed"
- `watchtower/direct-sync.md` — ทางเลือกสำรองเวลา auto-poll พลาด
- `open-questions.md` — ควรเพิ่มเป็นข้อใหม่: "Polling Type (Normal/Line/...) ของ GSB มีตัวเลือกอะไรบ้าง ต่างกันยังไง"

**สถานะ:** คาดว่า (ยังไม่ยืนยัน) — ทั้งความหมายของฟิลด์และสาเหตุบั๊ก Save ไม่ persist
