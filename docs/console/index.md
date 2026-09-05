# Admin Console — ภาพรวม

คู่มือนี้สำรวจ **Admin Console** (เว็บฝั่งแอดมิน ตราแบรนด์ "JoinPay") ของระบบ PGW แบบ B2B ที่มีคู่มือเทคนิคฝั่ง Merchant อยู่แล้วที่ [PGW API](../api/index.md) — ส่วนนี้อธิบาย **ฝั่งตรงข้าม**: หน้าจอที่ทีม Operation/Admin ของ PGW Provider ใช้บริหารจัดการ Merchant, Seller, Payment Channel, Transaction และการตั้งค่าทั้งระบบ

## ระบบนี้คืออะไร (ทวนสั้นๆ)

ระบบเป็น Whitelabel SaaS Payment Gateway ที่ให้ **Merchant** (เจ้าของเว็บ/แอปต่างๆ) เรียกใช้ API เพื่อรับ-ส่งเงินให้ **End User** ของตัวเอง โดยมี **PGW Provider** เป็นเจ้าของระบบ Core + Bank Adapter ทั้งหมด และมี **Partner/Seller** เป็นชั้นตัวแทนที่หา Merchant เข้าระบบ

## โครงสร้างเมนูทั้งหมดของ Console

Console มี top nav 6 เมนูหลัก แต่ละเมนูมี dropdown ย่อยของตัวเอง:

| เมนูหลัก | เมนูย่อย | สรุปสั้น |
|---|---|---|
| **Dashboard** | — | ภาพรวมตัวเลขทั้งบริษัท กรองตาม Merchant/Seller ได้ |
| **Company** | Company Users | จัดการ admin user ภายใน PGW Provider เอง |
| | Company Roles | ตั้งค่า RBAC role/permission ระดับ Company |
| | Fee Accounts | บัญชีธนาคารจริงที่รับ Platform Fee |
| | Ban Accounts | บัญชีธนาคารที่ถูกขึ้นบัญชีดำทั้งระบบ |
| | Internal Accounts | บัญชีภายในอื่นๆ ของบริษัท |
| **Seller** | Sellers | จัดการบัญชี Seller (sub-partner) |
| | Fee Dashboard | ภาพรวมรายได้ค่าธรรมเนียมแยกตาม Seller |
| | Seller MDR Templates | เทมเพลตอัตราค่าธรรมเนียมที่ใช้ซ้ำได้กับหลาย Seller |
| **Merchant** | Merchants | จัดการบัญชี Merchant ทั้งหมด |
| | Merchant Users | user login ของฝั่ง Merchant |
| | Merchant Roles | RBAC role ระดับ Merchant user |
| | Merchant Customers | End User ของทุก Merchant รวมกัน |
| | Merchant Accounts | บัญชีธนาคารที่ผูกอยู่กับแต่ละ Merchant |
| **Slip Verification** | Slip Verifiers | ตั้งค่าผู้ให้บริการตรวจสอบสลิป QR ภายนอก |
| **Payment** | Payment Channels | บัญชีธนาคารจริงที่ใช้รับ/ส่งเงิน |
| | Payment Pools | กลุ่มของ Payment Channel + กลยุทธ์เลือกใช้บัญชี |
| | Transactions | รายการธุรกรรมทั้งหมดในระบบ |
| | Callback Logs | ประวัติการยิง webhook ไปหา Merchant |
| | Payment Config | ค่า default ระดับบริษัทสำหรับ fee/limit ทั้งหมด |

## แผนภาพความสัมพันธ์ระหว่าง Entity หลัก

- **Company (PGW Provider)** → มี Fee Account (รับ Platform Fee), Internal Account, Ban Account list (ใช้ร่วมกันทั้งบริษัท)
- **Seller** 1 ราย → ผูกกับ **Merchant** ได้หลายราย (พบตัวอย่างจริง 1 seller ผูก 43 merchant) → มี MDR Template เป็นชุด fee ที่ reuse ได้
- **Merchant** 1 ราย → มี Merchant User หลายคน, มี Merchant Customer (End User) จำนวนมาก, ผูกกับ Payment Channel Pool 1 พูล, มี Merchant Account หลายบัญชี
- **Payment Pool** 1 พูล → รวม Payment Channel (บัญชีธนาคารจริง) หลายบัญชี → แต่ละ action (Deposit/Withdraw/Topup/Settlement/Withdraw Fee) เลือก Selection Strategy ของตัวเอง
- ทุก action ของ End User → กลายเป็น **Transaction** record → ถ้า match สำเร็จก็ยิง **Callback** (webhook) กลับไปหา Merchant พร้อม log ทุกครั้ง

!!! danger "หมายเหตุสำคัญเกี่ยวกับความแม่นยำของเอกสาร"
    เอกสารนี้เขียนจากการสังเกตหน้าจอ UI จริงเท่านั้น ไม่มี spec/source code ยืนยันเบื้องหลัง — บางจุดที่ระบุว่า "น่าจะ" หรือ "คาดว่า" คือการอนุมานจากรูปแบบ UI ที่เห็น ไม่ใช่ข้อเท็จจริงที่ยืนยันแล้ว 100% กรุณาตรวจสอบกับทีมพัฒนาก่อนใช้อ้างอิงในการตัดสินใจสำคัญ ดูสรุปทั้งหมดได้ที่ [คำถามที่ยังไม่มีคำตอบ](../open-questions.md)
