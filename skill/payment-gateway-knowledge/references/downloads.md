# ดาวน์โหลด PDF

คู่มือฉบับ PDF แยกเป็นไฟล์ตาม chapter — เพราะรวมเป็นไฟล์เดียวแล้วรูป/ตารางเยอะเกินกว่าตัว renderer จะสร้างเอกสารให้ครบในรวดเดียว (รายละเอียดทางเทคนิคดูที่ `pdf_event_hook.py` ในซอร์สโค้ด) แนะนำดาวน์โหลดเฉพาะ chapter ที่ต้องใช้

| Chapter | ไฟล์ |
|---|---|
| ภาพรวมระบบ (หน้าแรก + คำถามที่ยังไม่มีคำตอบ) | [00-overview.pdf](pdf/00-overview.pdf) |
| PGW API (Merchant-facing) | [01-pgw-api.pdf](pdf/01-pgw-api.pdf) |
| Admin Console (1/2) — ภาพรวม, Dashboard, Company, Seller, Merchant, Slip Verification | [02-admin-console-1.pdf](pdf/02-admin-console-1.pdf) |
| Admin Console (2/2) — Payment, Deep-dive Match Type | [02-admin-console-2.pdf](pdf/02-admin-console-2.pdf) |
| OpsDash | [03-opsdash.pdf](pdf/03-opsdash.pdf) |
| Watchtower | [04-watchtower.pdf](pdf/04-watchtower.pdf) |
| PGWay (Device Monitoring) | [05-pgway.pdf](pdf/05-pgway.pdf) |
| ทีม Support | [06-support.pdf](pdf/06-support.pdf) |

!!! note "ทำไมแยกไฟล์"
    เดิมทำเป็น PDF เดียวรวมทั้งไซต์ แต่เจอบั๊กของ WeasyPrint (ตัว renderer) ที่หยุด render กลางเอกสารแบบไม่มี error เมื่อเอกสารใหญ่/ซับซ้อนเกินระดับหนึ่ง (รูปกว่า 200 รูป + ตารางกว่า 100 ตาราง) — ทดสอบแล้วว่าแต่ละ chapter แยกกันไม่ชนขีดจำกัดนี้ จึงแยกไฟล์แทนการฝืนรวมเป็นไฟล์เดียว
