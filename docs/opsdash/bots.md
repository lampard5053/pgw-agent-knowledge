# Bot

Subtitle: "Manage and monitor bot execution" — Card: "Bot List" / "View all bots and their current status"

มุมขวาบน: ↺ refresh, **Auto Reload** (▷5s ▷15s ▷30s ▷60s — เหมือน pattern ที่เจอซ้ำในหน้า [Job](jobs.md) และ [Transaction](transactions.md)), ปุ่ม ⊕ add

## ตาราง Bot List

| คอลัมน์ | รายละเอียด |
|---|---|
| Name | ชื่ออ้างอิงบอท — พบรูปแบบ เช่น `JoinPay-kbiz-ReadyToEatFoods-1382916766-K`, `neo-2233562788`, และแม้แต่ค่าทดสอบ `asdf` |
| Client Code | client ที่บอทนี้ผูกอยู่ — พบทั้ง `core` และ `orga` ปนกัน |
| Bank Account | เลขบัญชีธนาคารที่บอทนี้ดูแล |
| Kind | ยี่ห้อ/adapter ธนาคาร — ตัวอย่างที่พบส่วนใหญ่เป็น `kbiz`, พบ `gsb` 1 แถว |
| Connection | ดูรายละเอียดด้านล่าง |
| Healthcheck State | badge คู่: **Device** / **Account** (เขียว/แดงอิสระกัน) |
| Last Request Job | เวลาล่าสุดที่มีการเรียกใช้ + สถานะ ("Inactive" / "No requests") |
| Action | 🗑️ ลบ |

!!! tip "อ่าน Healthcheck State"
    **Device = เขียว** น่าจะหมายถึง USB/hardware connection ยังเสียบอยู่ปกติ ส่วน **Account = แดง** (พบเกือบทุกแถว แม้ Device จะเขียว) น่าจะหมายถึง session ล็อกอินแอพธนาคารหมดอายุ/ต้อง re-login — สอดคล้องกับธรรมชาติแอพธนาคารที่ auto logout เป็นประจำ

    แถวที่ **Device และ Account แดงทั้งคู่** จะโชว์ Last Request Job = "**Never / No requests**" (ยังไม่เคยถูกใช้จริงเลย) ต่างจากแถวที่เคยใช้งานมาก่อนซึ่งจะโชว์ "Inactive" พร้อมเวลาล่าสุด

## Connection — พบ 2 ประเภท

**USB** และ **Tunnel**

!!! danger "นัยสำคัญ — phone farm อาจกระจายอยู่คนละที่กันได้"
    **USB** = มือถือเสียบสายจริงกับเครื่อง "station" ในสถานที่เดียวกัน (ทุกแถวในตารางตัวอย่างที่สำรวจเป็น `usb`) ส่วน **Tunnel** = เชื่อมต่อมือถือ/device จากระยะไกลผ่าน network tunnel (คาดว่าเป็น ADB over network หรือ VPN) — ยืนยันด้วยหลักฐานจาก [Job](jobs.md) ที่พบ device identifier 2 รูปแบบต่างกัน: **serial number จริง** (เช่น `RFCR20RQQAE` — รูปแบบ ADB serial ของมือถือ Samsung) สำหรับการเชื่อมต่อ USB และ **IP:port** (เช่น `192.168.1.106:5577`) สำหรับ Tunnel/network

    แปลว่า infra การรันบอทไม่จำเป็นต้องรวมอยู่ที่เดียวทั้งหมด อาจมี "station" หลายจุด หรือแม้แต่มือถือที่ไม่ได้อยู่ในสถานที่เดียวกับ server เลยก็เชื่อมได้

## ฟอร์ม Add Bot

| ฟิลด์ | รายละเอียด |
|---|---|
| Name | ชื่ออ้างอิงบอท |
| Bank Account ID | ใส่ internal ID (MongoDB ObjectId) ของ Bank Account ที่จะผูกบอทนี้เข้าไป |
| Connection | dropdown USB / Tunnel |
| Bot Token | token สำหรับให้บอทยืนยันตัวตนกับ OpsDash backend พร้อมปุ่ม copy |

ปุ่ม Cancel / Add Bot

!!! danger "ไขความสัมพันธ์กับ Bank Account fields แล้ว — ผ่านหลักฐานจาก PGWay"
    Entity "Bot" นี้ กับ field "Device ID / Appium Bot URL / PIN" ที่เห็นใน [Bank Account](bank-accounts.md) Edit modal (ส่วน "{Kind} Specific Fields") **ไม่ใช่การจัดการอุปกรณ์เต็มรูปแบบ** — ทั้งสองเป็นแค่ข้อมูลอ้างอิงชุดเล็กที่ OpsDash เก็บไว้เพื่อรู้ว่าจะสั่ง Job ไปที่อุปกรณ์ไหน

    ระบบที่บริหารอุปกรณ์จริงแบบเต็มรูปแบบ (WDA Signing Teams, Setup/Run actions, health watchdog, ฯลฯ) คือ **[PGWay](../pgway/index.md)** ต่างหาก — "Bot" ของ OpsDash กับ "Bot Token" ในฟอร์มนี้จึงเป็นแค่ pointer/credential ชี้ไปที่อุปกรณ์ตัวเดียวกับที่ PGWay ดูแลอยู่ ไม่ได้ซ้ำซ้อนกัน แค่คนละชั้นความรับผิดชอบ
