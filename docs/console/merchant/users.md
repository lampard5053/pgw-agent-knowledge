# Merchant Users

![รายการ Merchant Users](../../assets/screen-short/merchant-users-list.jpg "รายการ Merchant Users ทั้งบริษัท — เห็น audit trail 'by bot_support' จำนวนมาก")

!!! tip "ข้อสังเกต"
    Merchant User หลายรายถูกสร้างโดย `bot_support` (ระบบอัตโนมัติ) ไม่ใช่ admin พิมพ์เอง — สะท้อนว่ามีกระบวนการ automated merchant onboarding อยู่เบื้องหลัง

![Create Merchant User](../../assets/screen-short/merchant-users-create-form.jpg "ฟอร์ม Create Merchant User")

| ฟิลด์ | รายละเอียด |
|---|---|
| Username / Password (8+ ตัวอักษร) | สำหรับ login เข้า merchant portal |
| Authentication Type | เห็นแค่ "Password" — hint ว่าอนาคตรองรับ auth แบบอื่นได้ |
| Role | ผูกกับ Merchant Role ที่สร้างไว้ |
| Merchant | ผูก user นี้กับ merchant ไหน |
| Status | Active / Inactive / Locked / Suspended |

![Edit Merchant User](../../assets/screen-short/merchant-users-edit-form.jpg "ฟอร์ม Edit Merchant User — สังเกตว่า field 'Merchant' ถูก disable ไว้")

!!! question "ข้อสังเกต"
    ตอน Edit field "Merchant" มักจะ disabled (สีเทา) — คาดว่า merchant assignment แก้ไม่ได้หลังสร้าง ต้องลบแล้วสร้างใหม่แทน (ยังไม่ยืนยัน)
