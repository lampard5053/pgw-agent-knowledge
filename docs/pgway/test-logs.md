# Test Logs

Header: "Test Logs" — log กลางของทุก test ข้ามทุกอุปกรณ์ (ต่างจาก "Recent Tests" ของหน้า [Dashboard](dashboard.md) ที่โชว์แค่ล่าสุด)

## Filter

Search testId, **State**, **Type**, **Device**

### Status dropdown
**All statuses, Passed, Failed, Error, Running, Cancelled**

!!! question "ข้อสังเกต"
    Dropdown ไม่มี "Queued" เป็นตัวเลือก ทั้งที่ตารางจริงมีแถว STATUS = queued อยู่หลายแถว — อาจเป็นแค่ UI ไม่ได้ list ให้ครบ หรือ "queued" ถูกนับรวมอยู่ในหมวดอื่นตอน filter

### Type dropdown

**All types, transfer, balance, verify-slip, line-verify**

!!! danger "ยืนยัน 'line-verify' คือ test type จริง"
    ตรงกับปุ่ม "Verify LINE" ที่เห็นในทุก device card ของหน้า [Devices](devices.md) — เป็น test ประเภทที่ 4 นอกจาก transfer/balance/verify-slip ที่ตรงกับ 3 ปุ่มในฟอร์ม New Test ของ [Dashboard](dashboard.md) — ยืนยันว่า LINE integration เป็นส่วนสำคัญของระบบจริง

### Device dropdown
รายชื่อยาวของอุปกรณ์ — พบบางเครื่องชื่อ **"iPhone 11 Test"** และ **"iPhone"** เฉยๆ (ซ้ำกันหลายครั้ง) โดยไม่มีรหัสตามรูปแบบมาตรฐาน — คาดว่าเป็นเครื่องทดสอบ/ยังไม่ได้ provision เต็มรูปแบบ

## ตาราง Test Logs

คอลัมน์: TEST ID (ลิงก์), TYPE, STATUS, STEPS (จุดไล่ระดับ), DEVICE (serial), DETAILS (ธนาคาร+จำนวนเงิน+เลขบัญชี), IP (ตรงกับ "Requested by" ของหน้ารายละเอียด)

## Test Detail {: #test-detail }

คลิก Test ID เพื่อเปิดหน้ารายละเอียด — Badge สถานะ: **QUEUED** (เหลือง) / **RUNNING** (ฟ้า) / **PASSED** (เขียว) + badge ประเภท (`transfer`) | ปุ่ม Cancel (แดง, เฉพาะ queued/running)

**ฟิลด์พื้นฐาน:** Test ID, Device (ชื่อ + serial สีเทา), **App** (badge เช่น `kbank` + ชื่อแอพจริง), Bank, Account, Amount, **Requested by** (IP address ผู้เรียก test), Started, Finished, Exit Code

!!! danger "เจอชื่อแอพธนาคารจริงยืนยันครบ — K PLUS (KBank) และ MyMo (GSB)"
    Field "App" โชว์ **"K PLUS · single transfer"** สำหรับอุปกรณ์ kbank — K PLUS คือชื่อจริงของแอพมือถือส่วนบุคคลของกสิกรไทย ส่วน "MyMo single transfer" สำหรับอุปกรณ์ GSB ก็คือชื่อจริงของแอพ GSB เช่นกัน — ทั้งสองชื่อถูกต้องตามจริง ไม่ใช่การตั้งชื่อผิด

### Steps table — เผยขั้นตอนเต็มของ automation script (Transfer)

| # | Step |
|---|---|
| 0 | login |
| 1 | tap transfer |
| 2 | select bank |
| 3 | enter account number |
| 4 | enter amount |
| 5 | review |
| 6 | confirm send |
| 7 | extract result |
| 8 | read balance |

Progress bar แสดงเป็น "N/12 steps passed" — **แต่ในตัวอย่างที่เห็นรันจบสมบูรณ์ทุกครั้งอยู่ที่ step 8 พร้อม log "PASSED — all steps"** คาดว่า "12" เป็นค่า max step ที่ใช้ร่วมกันในระบบ (บาง bank script อาจมี step มากกว่านี้) แต่ script `kbank-transfer` ใช้แค่ 9 ขั้น (0-8) ก็ถือว่าผ่านสมบูรณ์แล้ว

### Result section (เฉพาะ test ที่ PASSED) — ใบเสร็จธุรกรรมจริง

**Transaction:** Amount, Reference, Date (รูปแบบไทย), **Balance after**
**Sender (from):** Account name (มีการ mask นามสกุลบางส่วน), Account number, Bank
**Destination (to):** Account name, Account number, Bank
มี collapsible **"▶ Raw result JSON"** ให้ดูข้อมูลดิบเพิ่ม

### Output — raw console log ของ automation script

ตัวอย่างจริง (transfer ธนาคารกรุงเทพ 120 บาท):

```
[kbank-transfer] serial=192.168.0.120:5555 acct=0724888458 amount=120 bank=ธนาคารกรุงเทพ->กรุงเทพ STOP_AT_STEP=99

[step 0] ▶ login
[kbank-transfer] logged in
[step 0] ✓ login (49148ms)

[step 1] ▶ tap transfer
[step 1] ✓ tap transfer (12907ms)

[step 2] ▶ select bank
[step 2] ✓ select bank (6390ms)

[step 3] ▶ enter account number
[step 3] ✓ enter account number (47104ms)

[step 4] ▶ enter amount
[step 4] ✓ enter amount (21188ms)

[step 5] ▶ review
[kbank-transfer] REVIEW: to "MR PAING THU OO" ธ.กรุงเทพ 072-4-88845-8 amount=120
[step 5] ✓ review (12867ms)

[step 6] ▶ confirm send
[kbank-transfer] STOP_AT_STEP=99 — tapping ยืนยัน (REAL transfer)
[step 6] ✓ confirm send (13071ms)

[step 7] ▶ extract result
[kbank-transfer] SENT 120 to "MR PAING THU OO" ref=016210173301BOR00703
[step 7] ✓ extract result (2820ms)

[step 8] ▶ read balance
[kbank-transfer] updated balance after transfer = 373
[step 8] ✓ read balance (9985ms)

[kbank-transfer] PASSED — all steps
```

!!! danger "การค้นพบที่สำคัญที่สุด — Test คือการโอนเงินจริง ไม่ใช่ simulation"
    ข้อความ **"(REAL transfer)"** ที่ step 6 ยืนยันตรงๆ ว่า **ทุกครั้งที่ทดสอบผ่าน PGWay คือการโอนเงินจริง ระหว่างบัญชีจริง** พารามิเตอร์ **`STOP_AT_STEP`** จึงมีความหมายชัดเจน:

    - ตั้งค่าต่ำ (หยุดก่อน step 6 "confirm send") = ทดสอบแค่ login/นำทาง/กรอกข้อมูล **โดยไม่โอนเงินจริง**
    - ตั้งเป็น **99 = รันจนจบ รวมกดยืนยันโอนเงินจริง**

!!! danger "เจอหลักฐานทางเทคนิคระดับลึก"
    - **`serial=192.168.0.120:5555`** — รูปแบบ IP:port ยืนยันว่าอุปกรณ์นี้เชื่อมผ่าน **ADB over network** (ตรงกับ concept "Tunnel" ของ [OpsDash → Bot](../opsdash/bots.md))
    - แต่ละ step log ตามรูปแบบ: `[step N] ▶ {name}` (เริ่ม) ตามด้วย `[step N] ✓ {name} ({ms}ms)` (จบ)
    - Reference number รูปแบบ `0162101733xx{3-letter code}xxxxx` ต่างกันตามธนาคารปลายทาง (พบ `BOR` สำหรับ Bangkok Bank, `COR` สำหรับ GSB) — ยังไม่ยืนยันความหมายตัวอักษร 3 หลักแน่ชัด
