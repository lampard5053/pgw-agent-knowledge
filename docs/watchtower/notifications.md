# Notifications (Notification Channels)

Breadcrumb: "{Instance}" — Header: "Notification Channels" — ปุ่ม **+ Add Channel** (มุมขวาบน)

!!! danger "ค่า credential ในหน้านี้ถูก mask ไว้โดยตั้งใจ"
    ตอนสำรวจหน้านี้พบ Discord Webhook URL จริง (`https://discord.com/api/webhooks/...`) ซึ่งเป็น credential ที่ให้สิทธิ์โพสต์เข้า Discord channel นั้นได้ เพื่อความปลอดภัย เอกสารนี้แสดงเฉพาะโครงสร้าง ไม่ใส่ค่า URL เต็มจริง

## รายการ Channel

| Name | Type | Last success | Actions |
|---|---|---|---|
| Test Discord | `discord` | (timestamp, สีเขียวถ้าสำเร็จ) | toggle เปิด/ปิด, ▷ ทดสอบส่ง, ✏️ แก้ไข, 🗑️ ลบ |

## ฟอร์ม Edit Channel
Title: "Edit Channel" — "Update this notification channel's configuration."

| ฟิลด์ | รายละเอียด |
|---|---|
| Name | ชื่ออ้างอิง channel |
| Type | เช่น "Discord" (readonly ตอน edit) |
| Discord Webhook URL | URL ปลายทาง (masked ในเอกสารนี้) |
| Enabled | toggle เปิด/ปิดการใช้งาน |

## ฟอร์ม Create Channel
Title: "Create Channel" — **"Configure a notification channel for balance alerts."**

| ฟิลด์ | รายละเอียด |
|---|---|
| Name | placeholder ตัวอย่าง "e.g. Ops Discord" |
| Type | dropdown — พบอย่างน้อย 2 ค่า: **Discord**, **HTTP Webhook** (generic) |
| Webhook URL | placeholder ตัวอย่าง "https://example.com/webhook" |
| Enabled | toggle |

!!! tip "นัยสำคัญ — ระบบแจ้งเตือนสำหรับ balance alert โดยเฉพาะ"
    Subtitle ของ Create Channel บอกชัดว่าใช้สำหรับ **"balance alerts"** — เชื่อมกับ banner "N accounts need attention" ที่เห็นใน [Dashboard](dashboard.md)/[Accounts](accounts.md) และสถานะ Suspended/Captcha/Polling Limit — **คาดว่า**เมื่อบัญชีไหนเข้าเงื่อนไข (เช่น Health เปลี่ยนเป็น Needs Attention หรือ balance ต่ำกว่าเกณฑ์) ระบบจะยิงแจ้งเตือนออกไปยัง channel เหล่านี้อัตโนมัติ ยังไม่ยืนยันเงื่อนไข trigger ที่แท้จริง
