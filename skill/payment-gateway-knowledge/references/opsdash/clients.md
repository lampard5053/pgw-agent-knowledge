# Client

Subtitle: "Manage and monitor client configurations" — Card: "Client List" / "View all clients and their current status"

!!! danger "Client (ในที่นี้) ≠ Merchant/Seller ฝั่ง Admin Console"
    เป็นคนละแนวคิดกันโดยสิ้นเชิง — **"Client" ในหน้านี้คือระบบ/บัญชีที่ลงทะเบียนเชื่อมต่อ API เข้ากับ OpsDash เอง** (การเชื่อมต่อระดับ system-to-system) **ไม่ใช่ผู้ประกอบการที่ End User ใช้จ่ายเงินด้วย** อย่าง Merchant/Seller ในฝั่ง Admin Console

## ตาราง Client List

| คอลัมน์ | รายละเอียด |
|---|---|
| Code | รหัสอ้างอิงสั้น มีปุ่ม copy ข้างชื่อ |
| Name | ชื่อเต็มของ client |
| Status | Active / Inactive (badge สี) |
| Tier | ระดับสิทธิ์ — พบ 3 ค่า: **Onboarder, Partner, Owner** |
| Endpoint | URL ปลายทางของ client นั้น (ใช้เป็น webhook/callback destination) |
| Action | ⚙️ แก้ไข / 🗑️ ลบ |

ด้านล่างตารางมี pagination: "Showing N clients", Rows per page (default 100), Previous/Next

## ตัวอย่างข้อมูลจริงที่พบ (ครบทั้ง 3 client ในระบบตอนสำรวจ)

| Code | Name | Tier | Endpoint |
|---|---|---|---|
| `onboarder` | Onboarder | Onboarder | `https://random.pgway.work` |
| `core` | core | **Partner** | `https://bank-adapter.joinpay.app/api/v1/receiver/callback` |
| `orga` | Orga 888 | **Owner** | `https://random.pgway.work` |

!!! danger "จุดสำคัญที่สุด — client 'core' คือปลายทางที่เชื่อม OpsDash เข้ากับ PGW Core"
    Endpoint ของ client **"core"** คือ `https://bank-adapter.joinpay.app/api/v1/receiver/callback` — path `/receiver/callback` บ่งชี้ชัดว่านี่คือจุดที่ **OpsDash ยิง webhook กลับไปหาระบบ PGW Core** ทุกครั้งที่ตรวจพบธุรกรรม ดูภาพรวม flow เต็มที่ [OpsDash — ภาพรวม](index.md)

    ส่วน `onboarder` และ `orga` มี endpoint เป็น `https://random.pgway.work` เหมือนกัน — **คาดว่า**เป็นค่า placeholder/ทดสอบที่ยังไม่ได้ตั้งค่าจริง ไม่ใช่ convention การตั้งชื่อโดเมนที่มีความหมาย

## ฟอร์ม Edit Client

เปิดจากปุ่ม ⚙️ ในตาราง — ชื่อ modal: "Edit {Name} ({Code})"

| ฟิลด์ | รายละเอียด |
|---|---|
| Code | readonly (แสดงเป็นสีเทา ไม่ให้แก้) |
| Name | ชื่อ client แก้ได้ |
| Active / Inactive | toggle สวิตช์ |
| Tier | dropdown เลือก Onboarder/Partner/Owner |
| Endpoint | URL text field |
| API Key (Optional) | ช่องกรอก + ปุ่ม copy/paste |
| Secret Key (Optional) | ช่องกรอก + ปุ่ม copy/paste |
| ปุ่ม Update | บันทึกการแก้ไข |

!!! question "ข้อสังเกตที่ต้องเช็คต่อ"
    ตอนเปิด Edit client "Onboarder" ช่อง Tier แสดง placeholder "Select a tier" ทั้งที่ตารางหลักระบุว่า Tier ของ client นี้คือ "Onboarder" อยู่แล้ว — อาจเป็น UI bug ที่ dropdown ไม่ preload ค่าเดิม หรือ Tier "Onboarder" ไม่ได้อยู่ในชุด option เดียวกับ Partner/Owner ที่โชว์ในฟอร์ม (มีชุด tier คนละกลุ่มกัน?) ยังสรุปไม่ได้ชัดเจน

## หมายเหตุด้าน implementation
กดปุ่ม copy ข้าง Code ในตาราง ได้ค่าเป็นสตริง 24 ตัวอักษร hex (เช่น `6a36a2b0c57ef153247378d9`) ซึ่งเป็นรูปแบบ **MongoDB ObjectId** มาตรฐาน — ยืนยันว่า backend เก็บข้อมูล client (และ entity อื่นๆ ของ OpsDash) ด้วย MongoDB โดยค่านี้คือ internal `_id` ของ record ไม่ใช่ secret/credential ใดๆ
