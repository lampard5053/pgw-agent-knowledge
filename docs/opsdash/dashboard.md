# Dashboard

หน้าแรกที่เห็นเมื่อ login เข้า OpsDash — subtitle: "Monitor transaction performance and analytics across all payment channels"

มุมขวาบน: ปุ่ม **Add Filter**

## การ์ดที่ 1 และ 2 — Transaction Analytics Dashboard (Amount / Count)

มี 2 การ์ดคู่กัน ใช้ UI component เดียวกันซ้ำ ต่างกันแค่หน่วยวัด (มูลค่าเงิน vs จำนวนรายการ):

| ฟิลด์ / ปุ่ม | รายละเอียด |
|---|---|
| Quick Ranges | ปุ่มลัดช่วงเวลา: 1hr, 1 Day, 2 Days, 1 Week, 2 Weeks, 1 Month, 2 Months |
| From / To | Date + Time picker กำหนดช่วงเวลาเองได้ |
| Granularity | dropdown ความละเอียดกราฟ (พบตัวอย่าง "Day") |
| ปุ่ม Update Chart (n) | รีเฟรชกราฟ — เลขในวงเล็บคือจำนวนจุดข้อมูล/วันที่จะแสดง |
| Total / Inbound / Outbound | ตัวเลขสรุปมุมขวา พร้อมไอคอนทิศทาง (↙ Inbound, ↗ Outbound) |
| กราฟแท่ง 2 สี | สีน้ำเงิน = Inbound, สีส้ม = Outbound แยกตามวัน |

**ตัวอย่างข้อมูลจริงที่พบ (การ์ด Amount):**

| Metric | ค่า |
|---|---|
| Total | ฿408,308,963.09 |
| Inbound | ฿205,942,117.14 |
| Outbound | ฿202,366,845.95 |

**ตัวอย่างข้อมูลจริงที่พบ (การ์ด Count):**

| Metric | ค่า |
|---|---|
| Total | 338,610 |
| Inbound | 254,268 |
| Outbound | 84,342 |

!!! tip "ตัวเลขบวกกันลงตัว"
    Inbound + Outbound ของการ์ด Count รวมกันได้ 338,610 พอดีเท่ากับ Total — ยืนยันว่าเป็นแค่ 2 กลุ่มย่อยของ Total เดียวกัน ไม่มีประเภทที่ 3 แอบซ่อนอยู่

## การ์ดที่ 3 — Transfer Events Dashboard

ใช้ filter ชุดเดียวกัน (Quick Ranges / From-To / Granularity / Update Chart) แต่ผลลัพธ์ต่างจาก 2 การ์ดแรก:

- แสดงแค่ **Total Events** เดียว (ไม่แยก Inbound/Outbound เหมือน 2 การ์ดบน)
- กราฟแท่งสีม่วง เดี่ยว (ไม่ใช่ 2 สีคู่แบบ Amount/Count)
- ตัวอย่างจริง: **Total Events 86,499**

!!! danger "คำถามค้างสำคัญ — Transfer Event ≠ Transaction"
    ตัวเลข Transfer Event (86,499) ต่างจาก Transaction Count (338,610) มาก ไม่ใช่ subset ที่ลงตัวหรือสัดส่วนที่อธิบายง่ายๆ **คาดว่า** "Transfer Event" อาจเป็นข้อมูลดิบระดับที่ Bot/ระบบตรวจจับได้ในทุกครั้งที่มีความเคลื่อนไหว (ก่อนสรุปเป็น Transaction ที่สมบูรณ์) แต่ยังไม่มีหลักฐานยืนยันชัดเจนว่ากลไกจริงคืออะไร ต้องดูหน้า detail ของ Transfer Event เพิ่มเติมถ้ามีโอกาส
