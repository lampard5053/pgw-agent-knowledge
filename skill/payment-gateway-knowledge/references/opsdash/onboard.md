# Onboard

Subtitle: "Prepare staged bank-account data before activation." — พื้นที่ **staging** สำหรับข้อมูลบัญชีธนาคารใหม่ ก่อนจะกลายเป็นบัญชีจริงใน [Bank Account](bank-accounts.md)

!!! danger "SOP จริงของทีมปฏิบัติการไม่ผ่านหน้านี้เสมอไป"
    ดู [SOP: ผูกบัญชีธนาคารใหม่](bank-onboarding-sop.md) — ขั้นตอนที่ทีมใช้จริงสำหรับ KTBBIZ/KBIZ/SCBBIZ **สร้างบัญชีตรงในฟอร์ม "Add New Bank Account" ทันที ไม่ผ่านหน้า Onboard (staging) นี้เลย** ต่างจากที่เคยตั้งสมมติฐานไว้ด้านล่างว่า credential ดิบต้องผ่าน staging ก่อนเสมอ — คาดว่าหน้านี้อาจสงวนไว้สำหรับ flow อื่น (เช่น client `onboarder` ที่ไม่มีสิทธิ์สร้างบัญชีจริงตรงๆ) ยังไม่ยืนยัน 100%

Card "Staged Bank Accounts" มี badge **"Onboarder"** กำกับ — ตรงกับ **Tier "Onboarder"** ของ client `onboarder` ที่เจอในหน้า [Client](clients.md) ยืนยันว่า tier นี้มีไว้เพื่อฟังก์ชัน staging บัญชีธนาคารโดยเฉพาะ

มี 2 แท็บ: **Draft** และ **History**

## แท็บ Draft

Subtitle: "Create draft bank-account data. Incomplete drafts stay editable before owner move."

| คอลัมน์ | รายละเอียด |
|---|---|
| Name | ชื่ออ้างอิง |
| Kind | ยี่ห้อ/ธนาคาร |
| Account Number | เลขบัญชี |
| Account Name | ชื่อบัญชี |
| Client Code | client ที่ submit draft นี้ |
| **Completeness** | badge บอกว่าข้อมูลกรอกครบหรือยังขาดอะไร |
| Updated | เวลาแก้ไขล่าสุด |
| Action | จัดการ draft นั้น |

มุมขวาบน: ปุ่ม **+ Add**, ปุ่มสลับ **Draft / History**, ↺ refresh
ท้ายตาราง: "Showing N draft records", Rows per page, Previous/Next

### ฟอร์ม Add Staged Bank Account

Subtitle: "Save draft data now. The completeness badge will show what is still missing."

| ฟิลด์ | รายละเอียด |
|---|---|
| Name | ชื่ออ้างอิง |
| Client Code | ผูกกับ client ไหน |
| Tenant | default = "default" |
| Kind | dropdown เลือกยี่ห้อธนาคาร |
| Account Number | เลขบัญชี |
| Account Name | ชื่อบัญชี |
| Phone Number | เบอร์มือถือที่ผูกบัญชี |
| **Username** | บัญชีผู้ใช้ (ล็อกอิน) ของแอพ/พอร์ทัลธนาคารจริง |
| **Password** | รหัสผ่านของแอพ/พอร์ทัลธนาคารจริง |

ปุ่ม Cancel / Save Draft

!!! danger "จุดสำคัญ — ฟอร์มนี้เก็บ credential ธนาคารจริง คนละชุดกับฟอร์ม Edit บัญชีจริง"
    ต่างจากฟอร์ม Edit บัญชีตัวจริงใน [Bank Account](bank-accounts.md) (ซึ่งมี Device ID + PIN + Appium Bot URL) — ฟอร์ม **staging** นี้เก็บแค่ **Username/Password ดิบของบัญชีธนาคาร** ไม่มี device/bot config เลย

    **คาดว่า flow คือ:** คนละคน/ระบบกรอก credential ธนาคารดิบเข้ามาก่อน (ผ่าน role "Onboarder") → แล้วอีกทีมค่อยมาผูก device + Appium + PIN ทีหลังตอน provision บัญชีจริง — แบ่งแยกหน้าที่กันด้าน security (คนรู้ password ธนาคาร ≠ คนดูแล bot infra)

## แท็บ History

Subtitle: "Review moved staged bank-account records that are no longer editable."

| คอลัมน์ | รายละเอียด |
|---|---|
| Name | ชื่ออ้างอิง |
| Kind | ยี่ห้อธนาคาร |
| Account Number | เลขบัญชี |
| Account Name | ชื่อบัญชี |
| Client Code | client ที่ submit ตอน staging |
| **State** | สถานะ — พบเฉพาะ **"Moved"** ในตัวอย่างที่สำรวจ |
| Moved | เวลาที่ถูกย้ายไปเป็นบัญชีจริง |
| **Bank Account** | ลิงก์ (🔗) ไปยัง record บัญชีจริงที่ถูกสร้างขึ้น (MongoDB ObjectId) |

### ตัวอย่างข้อมูลจริงที่พบ

| Name | Kind | Client Code (ตอน staging) | State |
|---|---|---|---|
| S04-SCB-0722968893-JP | SCB | onboarder | Moved |
| S01-SCB-1564341871-JP | SCB | onboarder | Moved |
| S03-SCB-1564526756-JP | SCB | onboarder | Moved |
| S02-SCB-0722968657-JP | SCB | onboarder | Moved |
| joinpay-ktbbiz-novomen-0170754480 | KTBBIZ | **core** | Moved |

!!! tip "ยืนยัน flow Onboard → Bank Account ด้วยหลักฐานจริง"
    4 แถวแรก (S01-S04-SCB) คือ**บัญชีเดียวกันเป๊ะ**กับที่ปรากฏในหน้า [Bank Account](bank-accounts.md) จริง — พิสูจน์ว่า path คือ **Onboard (staging, Client Code=`onboarder`) → กด Move → กลายเป็น record ใน Bank Account list จริง (Client Code เปลี่ยนเป็น `core`)**

    แถวสุดท้าย (`joinpay-ktbbiz-novomen`) มี Client Code = `core` ตั้งแต่ตอน staging เลย — แปลว่าบางครั้ง client "core" เองก็ submit staging ตรงได้ ไม่ต้องผ่าน "onboarder" role เสมอไป (อาจเป็น manual add โดย admin เอง)
