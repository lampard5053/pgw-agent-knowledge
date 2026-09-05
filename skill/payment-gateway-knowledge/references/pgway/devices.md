# Devices (Connected Devices)

Header: "Connected Devices"

## WDA Signing Teams

Subtitle: *"Apple Developer accounts available for signing per-device WDA IPAs"*

| Team ID | Name | Bundle ID | Devices | Notes |
|---|---|---|---|---|
| 5K5G94LNRJ | Default | `com.station.pgway.WebDriverAgentRunner` | 14 | "Original team, used before multi-team support" |
| 9RZ7H893R3 | Peter G | (เดียวกัน) | 1 | "Temporary use before new account coming" |

ฟอร์ม "Add a team": Team ID (10 chars), Name, Bundle ID, Notes (optional) + ปุ่ม Add

!!! danger "ข้อจำกัดจริงของการทำ iOS bot farm ขนาดใหญ่"
    WebDriverAgent (WDA) ต้อง **code-sign ด้วย Apple Developer account** ก่อนถึงจะรันบน iPhone จริงได้ — และแต่ละบัญชี Apple Developer มี**ข้อจำกัดจำนวนอุปกรณ์ที่ลงทะเบียนได้** ระบบนี้เลยต้องใช้**หลายบัญชี Apple Developer สลับกัน** ("Default" คุม 14 เครื่อง, "Peter G" เป็นบัญชีชั่วคราวคุม 1 เครื่องรอบัญชีใหม่มา) — ยืนยันว่าการสเกล iOS bot farm มีข้อจำกัดจริงจาก Apple เอง ไม่ใช่แค่เรื่อง infra ฝั่งตัวเอง

## Alert: อุปกรณ์ที่มีปัญหา — แยก 2 ประเภทตาม platform

**"N devices need attention"** (แดง): iOS → **"WDA is not running on this device"** (ปุ่ม Launch WDA) / Android → **"Device not connected"** (ปุ่ม Jump to card)

**"N Android devices need attention"** (เหลือง): แสดง IP:port (พอร์ต 5555 มาตรฐานของ ADB) + สถานะ **"not in adb"** + ปุ่ม Reconnect

## Background Jobs

### Balance Poller
*"Auto-refreshes one device's balance per tick (round-robin). Skips locked devices. Errors surface here as the disconnect signal — no test_runs rows."*
สถานะที่พบ: paused · Cadence: 300s (ทุก 5 นาที) · **Bank throttle: max 100 concurrent**

### WDA Watchdog
*"Probes every iOS device's :8100/status on a timer. On consecutive failures it re-checks tunneld, re-mounts the dev image, and re-launches WDA. New iPhones are picked up automatically."*
ตัวอย่างสถานะ: Monitoring 12 iOS · check ทุก 60s · restart หลัง fail 2 ครั้งติด → 7 healthy, 3 gave up, 2 unknown

!!! danger "กลไก self-healing ระดับลึกของ iOS automation"
    Watchdog เช็ค WDA process ผ่าน port **8100** (พอร์ตมาตรฐานของ WebDriverAgent) → ถ้า fail ติดกัน 2 รอบ จะไล่แก้ทีละขั้น: เช็ค **tunneld** (iOS device tunnel daemon) → **mount dev image ใหม่** (iOS ต้องมี Developer Disk Image ติดตั้งถึงจะเปิด dev feature ได้) → **relaunch WDA**

## รายการอุปกรณ์ทั้งหมด (89 เครื่อง)

Filter: Search name/code/udid, All platforms, All (สถานะ), ตัวนับ (เช่น 89/89), Expand/Collapse all

จัดกลุ่มตามแอพ/ธนาคาร เช่น **▼ GSB · MyMo (54)**, **▼ KBank · K+ (28)**

ปุ่ม **"Scan"** (ส้ม) ใน section "New Devices" — *"detect connected devices not yet in the database (live scan). Click Scan to look for connected but unconfigured devices. This runs a one-time hardware scan (no polling)."*

### โครงสร้าง Device Card

| ส่วน | รายละเอียด |
|---|---|
| หัวการ์ด | ชื่อ · platform badge (iOS/Android) · `app:` · `mode: prod` · settings link · status badge (Disabled/Ready/Unavailable/No check yet) · health (Unknown) |
| แท็บ | Info / Status / Setup |
| Info (iOS) | UDID, iOS Version, WiFi IP, PIN (saved), Balance + timestamp |
| Info (Android) | **Serial** (แทน UDID), Android Version, WiFi IP (pinned: ...), PIN, Balance |
| **SETUP** (เฉพาะ iOS) | Pair via Wi-Fi, Install WDA, Mount Dev Image, Rebuild IPA |
| **RUN** (iOS) | Launch WDA, Check Status, Connect Tunnel, Refresh Balance, Verify LINE |
| **RUN** (Android) | Mirror (scrcpy), Connect WiFi, Check Status, Refresh Balance, Verify LINE |

!!! question "ค้างไว้ — 'Verify LINE' คืออะไรกันแน่"
    ปรากฏในทุก device card ทั้ง iOS/Android และเป็น test type แยก (`line-verify`) ที่เจอใน [Test Logs](test-logs.md) ด้วย — **LINE** เป็นแอพแชทยอดนิยมในไทย คาดว่าเกี่ยวกับการยืนยัน OTP/แจ้งเตือนที่บางธนาคารส่งผ่าน LINE Notify หรือเช็คว่า LINE app ยังล็อกอินอยู่บนเครื่อง ยังไม่ยืนยันกลไกที่แท้จริง

!!! tip "Mirror (scrcpy)"
    **scrcpy** เป็นเครื่องมือ mirror หน้าจอ Android แบบ open-source ที่มีชื่อเสียง ให้ ops ดูหน้าจอมือถือจริงแบบ real-time ผ่านคอมได้โดยไม่ต้องเปิดมือถือเอง
