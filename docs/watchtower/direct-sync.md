# Direct Sync

Breadcrumb: "{Instance} > Direct Sync" — icon 🔄 + subtitle: **"Manually pull transactions from a bank's API and record them to the ledger."**

!!! danger "กลไก 'manual catch-up' ที่เติมเต็มระบบ auto-poll"
    ข้อความเสริมที่เห็นบางส่วน (ถูกบัง UI บางส่วนตอนสำรวจ) สื่อว่า: เลือกบัญชี → กด sync เพื่อ**ดึงข้อมูล transaction ของบัญชีนั้นจาก bank API ตรงๆ ทันที** ใช้สำหรับ**อุดรอยรั่วที่ auto-poll (Job/Bot อัตโนมัติ) พลาดไป** — เชื่อมกับทุกอย่างที่เห็นใน [Accounts](accounts.md) เรื่อง Suspended/Captcha/Polling Limit: พอ bot ติด CAPTCHA หรือโดน Polling Limit จน auto-poll พลาดธุรกรรมไป ops ใช้ Direct Sync ดึงย้อนหลังเองได้

## ฟิลด์ ACCOUNT

Dropdown "Pick an account..." — เลือกได้เฉพาะบัญชีบางกลุ่ม (พบเฉพาะ **KBIZ, KTB BIZ, SCB BIZ** ในตัวอย่างที่สำรวจ ไม่เจอ GSB/SCB)

ตัวอย่างบัญชีที่พบในรายการ:

| Kind | ชื่อบัญชี |
|---|---|
| KBIZ | joinpay-2248177368 |
| KTB BIZ | t126-ktbbiz-kittiyos-0170659496 |
| SCB BIZ | t126-scbbiz-origin-1624367092 |
| KTB BIZ | joinpay-ktbbiz-NSP-0170739155 |
| SCB BIZ | 601-scbbiz-tanglakdee-4142366382 |
| KTB BIZ | K-ktbbiz-Boonraksa-0170740447 |
| SCB BIZ | **NextPayxJoinPay**-scbbiz-OnePK-5492574657 |
| KTB BIZ | JoinPay-ktbbiz-Boonkampaeng-0170749711-**T126** |

!!! danger "บัญชีธนาคารถูกใช้ร่วมกันข้ามแบรนด์ได้ — ยืนยันแล้วที่ Groups"
    ชื่อ **"NextPayxJoinPay-scbbiz-OnePK-..."** ยืนยันแล้วจาก [Groups](groups.md) ว่าคือบัญชีที่ NextPay นำมาเปิด Partner ในฝั่ง JoinPay จริง ไม่ใช่แค่ตั้งชื่อคล้ายกันเฉยๆ

    Suffix **"-T126"** ก็ยืนยันแล้วจาก [Groups](groups.md) ว่าคือชื่อ Account Group ที่บัญชีนี้ผูกอยู่

!!! question "ค้างไว้ — ความเป็นไปได้ที่ KBIZ อาจมี API บางส่วน"
    Subtitle บอกว่าดึงจาก "bank's API" แต่ **KBIZ** ก็อยู่ในรายชื่อที่ Direct Sync รองรับด้วย ทั้งที่ [OpsDash → Job](../opsdash/jobs.md) เคยยืนยันว่า KBIZ ใช้ Bot/Appium อ่านหน้าจอ ไม่ใช่ REST API ทางการ — **อาจแปลว่า KBIZ มี API บางส่วนที่ใช้สำหรับ sync ย้อนหลังได้** (แค่ไม่ reliable พอสำหรับ real-time เลยต้องมี Bot สำรอง) ยังสรุปแน่ชัดไม่ได้ ไม่พบ GSB/SCB (personal banking ล้วน) ในรายชื่อ Direct Sync เลย ซึ่งสอดคล้องกับสมมติฐานว่าบัญชี personal banking ไม่มี API ให้ sync ตรงได้เลย
