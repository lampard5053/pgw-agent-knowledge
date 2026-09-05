# Agent Devices

Header: "Agent Devices"

## Card: Enroll a device

| ฟิลด์ | รายละเอียด |
|---|---|
| Device udid / code | placeholder ตัวอย่าง "e.g. bbl-020406133452" |
| Bank (app) | dropdown: **— leave as-is —** (default), **BBL, GSB, KBANK, KBIZ** |
| ปุ่ม Generate QR | สร้าง QR code สำหรับ enroll |

## Registered devices

"No devices registered yet." (ตอนสำรวจ) + ปุ่ม Refresh

!!! tip "กลไกที่คาดว่าเป็นไปได้ — enroll ผ่านสแกน QR ด้วยแอพ agent บนมือถือ"
    **คาดว่า**flow คือ: ops กรอกรหัสอุปกรณ์ + เลือกธนาคาร → กด Generate QR → **สแกน QR ด้วยแอพ "agent" ที่ติดตั้งบนมือถือเครื่องนั้น** เพื่อ self-register เข้าระบบอัตโนมัติ — ต่างจากหน้า [Devices](devices.md) ที่ ops ต้อง config เองทีละขั้น (Pair via Wi-Fi, Install WDA, Mount Dev Image ฯลฯ) นี่อาจเป็นวิธี provision ที่เร็วกว่าสำหรับอุปกรณ์บางประเภท

!!! question "ค้างไว้"
    - "No devices registered yet" — หน้านี้ยังไม่เคยถูกใช้งานจริงเลย หรือเป็นแค่ list ที่แสดงเฉพาะที่ enroll ผ่านช่องทางนี้ (แยกจาก 89 เครื่องที่เห็นในหน้า Devices)?
    - Bank (app) มีแค่ 4 ตัวเลือก (BBL, GSB, KBANK, KBIZ) — ทำไมไม่มี SCB/KTBBIZ/SCBBIZ/KPLUS ที่เจอในระบบอื่น? ระบบนี้รองรับแค่ 4 ธนาคารนี้ หรือ list ยังไม่ครบ?
