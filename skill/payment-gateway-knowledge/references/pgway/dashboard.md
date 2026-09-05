# Dashboard

Nav bar: Dashboard, Devices, Test Logs, Balance Checks, Phone Status, Agent Devices, Admin — มุมขวา: user (เช่น "operator_max (admin)"), toggle ภาษาไทย, Logout

## การ์ดสรุป 5 ใบ

| การ์ด | ตัวอย่างค่าจริง |
|---|---|
| Connected Devices | 75 |
| Running Tests | 5 |
| Total Tests | 104,786 |
| Passed (เขียว) | 84,998 |
| Failed (แดง) | 15,319 |

## New Test — ฟอร์มสั่งทดสอบใหม่

ปุ่มเลือกประเภท: **Transfer, Balance, Verify Slip**

!!! danger "ตรงกับ core action ของ Merchant API เป๊ะ"
    3 ประเภทการทดสอบตรงกับ 3 action หลักของ Merchant API (deposit/withdraw ผ่าน transfer, balance check, verify-slip) — PGWay คือเครื่องมือให้ QA/ops สั่งทดสอบยิงรายการจริงผ่านอุปกรณ์จริงแต่ละเครื่อง เพื่อยืนยันว่า automation ของแต่ละ device/bank ยังทำงานถูกต้องอยู่

### ฟอร์ม Transfer

| ฟิลด์ | รายละเอียด |
|---|---|
| Device | dropdown แสดง `{ชื่อเครื่อง} ({serial}) · app: {kind} · {สถานะ}` เช่น `PR16-GSB-020474174982 (2241001E) · app: gsb · WDA Stopped` |
| Bank | dropdown เช่น "KBNK - ธนาคารกสิกรไทย" |
| Account Number | เลขบัญชีผู้รับทดสอบ |
| Amount | จำนวนเงินทดสอบ |
| PIN (optional, uses device PIN) | ถ้าไม่กรอกใช้ PIN ที่ตั้งไว้ในตัวเครื่อง |
| Transfer PIN (set via `scripts/generate-transfer-pin.ts`) | PIN ยืนยันโอนที่สร้างผ่านสคริปต์เฉพาะ |

ปุ่ม Start Test (น้ำเงิน)

!!! tip "เจอสถานะ 'WDA Stopped' ในตัวเลือก Device — เชื่อมกับ Devices"
    ยืนยันว่ามีอุปกรณ์ **iOS** ในฟาร์ม (ใช้ WebDriverAgent/WDA) ปนกับ Android — ดูรายละเอียดเต็มที่ [Devices](devices.md)

## Tests by Device

Filter: All / Today / Week / Month

| คอลัมน์ | รายละเอียด |
|---|---|
| DEVICE | ชื่อตัวหนา + serial สีเทา |
| TOTAL | จำนวน test ทั้งหมดของเครื่องนั้น |
| PASSED | จำนวน + % |
| FAILED / ERROR / CANCELLED | จำนวนแยกประเภท |
| TOTAL AMOUNT | มูลค่ารวมที่ทดสอบผ่านเครื่องนี้ |
| LAST TEST | เวลาทดสอบล่าสุด |

พบ device code prefix หลายแบบ: **TP, PR, KR, RA, BL** (รวมถึง prefix เฉพาะ KBIZ อย่าง **PRC, RAC** ที่เจอในหน้า [Phone Status](phone-status.md)) และ device ชื่อ **"iPhone 11 Test"** ตรงๆ (pass rate ต่ำมาก ~1%)

!!! tip "อุปกรณ์จริง 1 เครื่องใช้ทดสอบได้หลาย config"
    แถว "PR43-SCB-4182251220-TEMP" ใช้ serial เดียวกับ "iPhone 11 Test" (`36DA802E`) — ยืนยันว่าอุปกรณ์ physical 1 เครื่องผูกกับ test-config/บัญชีได้มากกว่า 1 รายการ

## Recent Tests

| คอลัมน์ | รายละเอียด |
|---|---|
| TEST ID | ลิงก์ไปหน้ารายละเอียด (ดู [Test Logs](test-logs.md#test-detail)) |
| TYPE | badge เช่น `transfer` |
| STATUS | queued (เหลือง) / running (ฟ้า) / passed (เขียว) |
| STEPS | จุดไล่ระดับความคืบหน้า |
| DETAIL | ธนาคาร + จำนวนเงิน |
| STARTED | เวลาเริ่ม |
| ACTIONS | ปุ่ม Cancel (เฉพาะ queued/running) |

พบตัวอย่าง **"retry of {test id เดิม}"** — ยืนยันว่า test ที่ fail กด retry ใหม่ได้ พร้อมลิงก์อ้างอิงกลับไป test ต้นฉบับ
