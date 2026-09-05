# PGWay (Device Monitoring) — ภาพรวม

**"PGWay"** (ชื่อที่ปรากฏจริงในแอป) คือระบบ **QA/monitoring สำหรับ phone farm** ที่ควบคุมมือถือจริง (ทั้ง iOS และ Android) ซึ่งรันแอพธนาคารเพื่อทำ automation — เป็นชั้นที่**ลึกที่สุด**ในเครือข่ายที่สำรวจมา ลึกกว่า [OpsDash](../opsdash/index.md) อีกที เพราะจัดการอุปกรณ์จริงระดับ device/driver โดยตรง

!!! danger "หมายเหตุความแม่นยำของเอกสารส่วนนี้"
    เช่นเดียวกับส่วนอื่น เอกสารนี้เขียนจากการสังเกตหน้าจอ UI จริงเท่านั้น จุดที่เป็นการวิเคราะห์/อนุมานจะมีกล่อง **❓ คำถามค้าง** กำกับไว้เสมอ ดูสรุปคำถามค้างทั้งหมดที่ [คำถามที่ยังไม่มีคำตอบ](../open-questions.md)

    **ไม่เคยเห็น URL/โดเมนของระบบนี้โดยตรง** ระหว่างสำรวจ (ต่างจาก OpsDash/Watchtower ที่เห็น URL ชัดเจน) — คาดว่าน่าจะอยู่ใต้ตระกูล `pgway.work` เช่นกัน แต่ไม่ยืนยัน

## PGWay คืออะไร (ทวนสั้นๆ)

ถ้า [OpsDash](../opsdash/index.md) คือชั้นบันทึก/ประมวลผลธุรกรรมที่ Bot ตรวจพบ — **PGWay คือเครื่องมือที่ทีม QA/ops ใช้ทดสอบและมอนิเตอร์ "สุขภาพของอุปกรณ์" ที่รัน Bot เหล่านั้นโดยตรง** ครอบคลุมตั้งแต่การ enroll อุปกรณ์ใหม่, ตั้งค่า WebDriverAgent (iOS)/ADB (Android), สั่งทดสอบทำธุรกรรมจริง, ไปจนถึงติดตามวงเงินโอนรายวันต่อเครื่อง

## ยืนยันความเชื่อมโยงกับ OpsDash

- **App ที่ทดสอบ** (`kbank`, `gsb`) ตรงกับ **Kind** enum ที่เจอใน [OpsDash → Bank Account](../opsdash/bank-accounts.md)
- **Script ชื่อ `kbank-transfer`** ที่เห็นใน raw output ตรงกับแนวคิด "kbiz bot" ที่เห็น log ใน [OpsDash → Job](../opsdash/jobs.md)
- Device serial ทั้งแบบ USB (เช่น `R5GL13C31XZ`) และแบบ IP:port network (เช่น `192.168.0.250:5555`) ตรงกับ 2 ประเภท Connection (USB/Tunnel) ที่เคยเห็นใน [OpsDash → Bot](../opsdash/bots.md)
- **Phone Status** ยืนยันว่า KBIZ ก็รันบนอุปกรณ์ Android ธรรมดา (ไม่ใช่ REST API ล้วน) เสริมหลักฐานเดิมว่า KBIZ ต้องพึ่ง Bot/automation จริง

!!! danger "ระวังสับสน — KBANK (PGWay) กับ KBIZ ไม่ใช่ตัวเดียวกัน"
    App `kbank` ที่เห็นในเอกสารชุดนี้ (แอพ **K PLUS**) คือ Kasikornbank ฝั่ง**บุคคลธรรมดา** — ตรงกับ Kind ชื่อ `KPLUS` ที่เห็นใน [OpsDash → Bank Account](../opsdash/bank-accounts.md) และ bank code `KBNK` ที่เห็นในคอลัมน์ From/To ของทุก Transaction (เป็น**ตัวเดียวกันทั้ง 3 ชื่อ** แค่คนละระบบเรียกไม่ตรงกัน) — **คนละตัวกับ `KBIZ`** ซึ่งคือ Kasikornbank ฝั่ง **Business** (แอพ/กลไกอัตโนมัติคนละชุดกันโดยสิ้นเชิง แม้จะปรากฏในตาราง [Phone Status](phone-status.md) เดียวกันก็ตาม) ดูตารางสรุปการตั้งชื่อทั้งหมดที่ [หน้าแรก](../index.md#การตั้งชื่อธนาคารข้ามระบบ)

## โครงสร้างเมนูทั้งหมด

| เมนู | สรุปสั้น |
|---|---|
| [Dashboard](dashboard.md) | ภาพรวม + ฟอร์มสั่งทดสอบใหม่ (Transfer/Balance/Verify Slip) + ตารางสรุปต่อเครื่อง |
| [Devices](devices.md) | จัดการอุปกรณ์ที่เชื่อมต่อ (WDA Signing Teams, health alert, background watchdog jobs) |
| [Test Logs](test-logs.md) | log กลางของทุก test ข้ามทุกอุปกรณ์ + หน้ารายละเอียด test พร้อม raw script log |
| [Balance Checks](balance-checks.md) | ผลการเช็คยอดคงเหลือล่าสุดต่อเครื่อง |
| [Phone Status](phone-status.md) | ติดตามวงเงินโอนรายวันต่อเครื่อง |
| [Agent Devices](agent-devices.md) | enroll อุปกรณ์ใหม่ผ่าน QR code |
| [Admin](admin.md) | จัดการ token/user ของระบบเอง |

!!! danger "การค้นพบสำคัญที่สุด — Test ใน PGWay คือการโอนเงินจริง ไม่ใช่ simulation"
    Raw output log ของ test ที่ผ่าน ("PASSED") มีข้อความ **"tapping ยืนยัน (REAL transfer)"** ตรงๆ — ทุกครั้งที่ QA สั่งทดสอบ Transfer และปล่อยให้รันจนจบ (`STOP_AT_STEP=99`) คือมีเงินจริงถูกโอนระหว่างบัญชีทดสอบจริง ดูรายละเอียดที่ [Test Logs](test-logs.md)
