# Operations & Support — The Human Side of Running PGW

> **แหล่งข้อมูลต่างจากไฟล์อื่นในสกิลนี้:** ไฟล์นี้มาจากบทสนทนาก่อนหน้าเกี่ยวกับการออกแบบระบบ task management ให้ทีม Support/Account/PM ของ PGW (ไม่ได้มาจากการสำรวจ mkdocs docs project เหมือนไฟล์อื่นๆ ใน `references/`) — สะท้อนวิธีทำงานจริงของทีมปฏิบัติการ (คน/org/process) ไม่ใช่ spec ทางเทคนิคของตัวระบบ ถูกกู้คืนกลับมาหลังจากตอนแทนที่สกิลด้วยฉบับเต็มครั้งแรกไม่ได้ย้ายไฟล์นี้มาด้วย เพราะเนื้อหานี้ไม่ทับซ้อนกับ docs ฉบับเต็มเลย

## Gateway Brands ที่ทีมนี้ดูแล

ทีมปฏิบัติการเดียวกันดูแล**หลายแบรนด์ payment gateway พร้อมกัน** แต่ละแบรนด์มี Console/Ticket system ของตัวเอง:

- **Join / JoinPay** — แบรนด์ที่สำรวจ Admin Console ไว้ละเอียดที่สุด (ดู `admin-console.md`)
- **PayOK**
- **ManeePay**
- **NextPay**
- **MKCPay**
- **EazyPay**
- **101 SEN**

แต่ละแบรนด์แยก Console/Ticket แต่ share คำศัพท์ปฏิบัติการเดียวกัน (pre-transaction, manual-success, unknown_deposit, Pool, Bank Adapter) เพราะน่าจะเป็น instance ของระบบ backend ตระกูลเดียวกัน — **อย่าถือว่าพฤติกรรมที่ยืนยันแล้วบนแบรนด์หนึ่งเป็นจริงกับแบรนด์อื่นเสมอไป** จนกว่าจะเช็คซ้ำ

## เครื่องมือหลักที่ใช้ทำงานประจำวัน

| เครื่องมือ | ใช้ทำอะไร |
|---|---|
| **OpsDash (Bank Adapter OpsDash)** | Dashboard ของ Bank Adapter — ดู/ค้น transaction ดิบจากธนาคาร (raw payload), ลงทะเบียนบัญชีใหม่, เช็คสถานะ active/inactive/suspend ของบัญชี, สั่ง manual-success ได้บางกรณี (แต่**มี bug ที่บางครั้งกดไม่ได้** — ต้อง escalate ไป Dev) |
| **Watchtower** | ระบบค้นหา/วิเคราะห์ transaction ย้อนหลัง — ทีมบัญชีใช้ค้นยอดฝาก/ถอนตามช่วงเวลา+จำนวนเงิน (ฟีเจอร์ค้นหาแบบ range ยังจำกัด เคยถูก request เพิ่ม) |
| **Console (ต่อแบรนด์)** | เหมือนที่สำรวจไว้ใน `admin-console.md` — แต่ละแบรนด์มีของตัวเอง |
| **Ticket system (ต่อแบรนด์)** | ห้อง support แยกตามแบรนด์ (JoinPay/EazyPay/PayOk ที่ยืนยันมีจริง) |
| **Discord / Telegram** | ช่องทาง alert/notification (webhook, monitoring) และคุยกับ Admin/Partner ภายนอก |

## Support Tier Structure

- **Tier 1** — จุดที่ Support (Tier 2) ส่ง escalate เคสเกินขอบเขต (ยังไม่ชัดว่าคือทีม specialist 8 ทีมย่อย หรือ Support Agent เอง — ดู open-questions.md)
- **Tier 2 — Support (Shift Agent)** — ทีมหลักที่ monitor ระบบ, match รายการ, ตรวจ log, ประสานงาน Admin/Partner ภายนอกผ่าน Telegram
- **Tier 3 — Engineering Specialist** — รับเคสที่ Tier 2 ตั้งสมมติฐานแล้วส่งต่อ แก้ปัญหาระดับโค้ด/ระบบ
- **Level 4 — Executive Approval** — งานความเสี่ยงสูงเกี่ยวกับเงิน ต้องผู้บริหารตัดสินใจ

## Shift Structure

ทำงานเป็นกะ **3 กะ x 8 ชม.**: 18:00–02:00, 02:00–10:00, 10:00–18:00 — มี Ops Coordinator หมุนเวียนรับผิดชอบแต่ละกะ ต้อง **handover** สรุปงาน/ปัญหาให้กะถัดไปทุกครั้ง (จุดที่เคยมีปัญหา: เคสค้างข้ามกะโดยไม่มีใครรู้สถานะล่าสุด — แก้ด้วย Ongoing Issue Board)

## หน้าที่ Support (Tier 2) — สรุปย่อ (จาก JD 20 ข้อ)

- **Monitoring:** ดูแล deposit/withdraw ทั้งระบบ, เช็คยอด/สถานะทุกบัญชี-Pool, เช็คระบบ (JoinPay/EazyPay/OpsDash ฯลฯ)
- **บอท:** register บอทให้บัญชีใหม่, เช็คกรณีบอทไม่จับคู่รายการ
- **แก้ปัญหา:** ไล่เคสยอดไม่เข้า/ไม่อัปเดต/ค้าง/จับคู่ไม่ได้ ผ่าน Transaction + Log
- **Handover:** ตามเคสค้างจากกะก่อน, สรุปส่งกะถัดไป
- **Escalation:** แจ้งทีมเมื่อกระทบหลายบัญชี/ระบบ, ประสาน Admin/Partner ภายนอกและ Payment แต่ละเจ้า
- **Customer-facing:** จัดข้อความแจ้งลูกค้าเวลาระบบ/ธนาคารมีปัญหา (ทำทุกวันที่เข้างาน)

## Account Team

แยกเป็น 2 ส่วน: **Account Support** และ **Regal Support**

### Account Support — 2 งานหลัก

**1. การติดตั้งบัญชี (7 sub-step จริง แม้ status หลักมีแค่ 3 ระดับ)**
เตรียมข้อมูล+อุปกรณ์ → เชื่อมต่อผ่าน Anydesk → Refresh Balance → กรอกฟอร์มลงทะเบียนใน OpsDash → ติดต่อ Support ให้ online ในระบบหลังบ้าน → ทดสอบโอนเงินเข้า-ออก → Follow up ตาม transaction

**2. การดูแลบัญชี (monitoring)**
ติดตาม 3 ช่องทาง: Discord (notification/webhook/alert), Telegram Tagging, Support Team — **ไม่มีจุดรวมข้อมูลกลาง** เป็นช่องโหว่ที่เคยชี้ให้เห็น

### Regal Support — บัญชีที่ถูกอายัด (ความเสี่ยงสูงสุด)

Flow เมื่อบัญชีโดนอายัดมากกว่า 1 บัญชีในเวลาใกล้กัน: ไล่เส้นเงินบัญชีต้นทางเพื่อตรวจความเชื่อมโยง → แจ้งเตือนลูกค้า/เจ้าของบัญชี → เตรียมโยกเงินออกให้เหลือยอดต่ำสุด → **Monitor ใกล้ชิด 1-3 วัน** นับจากบัญชีแรกที่โดนอายัด — มี timeline กดดันชัดเจน ถ้าไม่มีระบบเตือนจะพลาดง่าย

## Issue Taxonomy (Category ที่ใช้ tag เคส)

Account Mismatch, Transaction Delayed, Transaction Not Synced, Account Status Change, Account Onboarding, Rate Limit, Webhook Issue, **Fraud/Security**, **Manual Credit Adjustment**, General Inquiry

## Pattern ปัญหาจริงที่เจอบ่อย (ตัวอย่างจาก brief เวรจริง)

- **บัญชีโดนระงับ (suspended)** — รายการค้างสะสมตั้งแต่เวลาหนึ่ง จนสรุปว่าบัญชีถูกระงับ
- **Queue-timeout** — รายการถอนค้างนาน ต้องเข้าไปตรวจสอบซ้ำบ่อยๆ
- **Rate limit** — บัญชีติด rate limit ธนาคาร → แก้ชั่วคราวด้วยการปรับ min withdrawal ให้สูงขึ้น (ลดจำนวน transaction ต่อช่วงเวลา)
- **Force Polling** — รายการฝากไม่เข้า Console เอง ต้องสั่ง force polling manual
- **Fraud pattern (สำคัญ):** มิจฉาชีพใช้ยอดเล็ก (เช่น 0.01 บาท) ทดสอบระบบก่อน แล้วยิงยอดใหญ่ (เช่น 2000 บาท) ตามมา — ยังไม่มี category "Fraud/Security" แยกในระบบเดิม (เพิ่งเสนอให้เพิ่ม)
- **Webhook resend ไม่ทำงานเสมอไป** — เคยพบเคสกด Resend ใน Watchtower แล้วยอดไม่เข้า Console ทั้งที่กดใน Bank Adapter ได้ปกติ (bug ที่ยังไม่แก้)
- **Pool-level action** — ปิด Pool กระทบหลาย merchant พร้อมกัน (คนละ scope จากปัญหารายบัญชีเดียว)

## Manual Credit Adjustment — จุดเสี่ยงที่สุด

เมื่อลูกค้าส่งสลิปให้ Admin ตรวจแล้วเติมเครดิตให้เอง (นอกระบบอัตโนมัติ) — เป็น action ที่ขยับเงินจริงด้วยมือ ความเสี่ยงสูงสุดในบรรดา action ทั้งหมด **ควรมี audit trail แยก** เสมอ (Amount, Approved By, Evidence/Slip ref) ไม่ใช่แค่คุยผ่านแชทเฉยๆ — ถ้าช่วยงานที่เกี่ยวข้องกับเรื่องนี้ ให้เน้นย้ำประเด็นนี้กับผู้ใช้เสมอ

## Discrepancy Tracking

มี Withdraw Discrepancy Tracker / Deposit Discrepancy Tracker แยกกัน — ใช้ track เคสที่ระบบขึ้นสถานะ Success แต่เงินไม่เข้าจริง (หรือกลับกัน) แหล่งข้อมูลตัวอย่างจริงมาจากรายงาน "Manual-Success" (Excel 2 แท็บ EazyPay/JoinPay รวม 90 รายการ แบ่งเป็น NOT_IN_STATEMENT, FOUND(ref), ACCOUNT_NOT_FETCHED)

## Customer Self-Service / KB Pipeline

แนวทางลดภาระ support ด้วยการทำ content ให้ลูกค้า self-service:

```
Recurring Issue → KB Candidate (เข้าเกณฑ์: ปลอดภัย+ทำเองได้+เกิดซ้ำบ่อย) → Drafting → Review (technical) → Published → วัด Deflection Rate
```

ช่องทาง publish: Console Help Center, FAQ Page, In-app tooltip/error message (ทรงพลังสุด), Chatbot/canned reply

**Deflection Rate** = (จำนวนเคสประเภทนั้นที่ลดลงหลัง publish) / (จำนวนเคสเดิมก่อน publish)
