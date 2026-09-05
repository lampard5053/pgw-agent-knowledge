# Reports

Breadcrumb: "{Instance} > Reports" — มุมขวาบน: ปุ่ม download + dropdown ช่วงเวลา (เช่น "Last 7 days")

## การ์ดสรุป 3 ใบ

Total Transfers, Total Amount, Success Rate — ตัวอย่างจริง: 5 transfers / 216,825.00 / 60.0%

## Transfer Summary

ตารางแยกตาม Status พร้อม Count และ Total Amount — ตัวอย่างจริง:

| STATUS | COUNT | TOTAL AMOUNT |
|---|---|---|
| **Submitted** | 1 | 108,350.00 |
| Failed | 1 | 108,355.00 |
| Success | 3 | 120.00 |

(1+1+3 = 5 = Total Transfers, 3/5 = 60% = Success Rate — ตัวเลขสอดคล้องกัน)

!!! danger "เจอ Status ตัวใหม่ที่ไม่เคยเจอในทั้ง 3 ระบบมาก่อน — 'Submitted'"
    ไล่เทียบ enum ที่เจอมาทั้งหมด: Admin Console, [OpsDash → Transaction](../opsdash/transactions.md), [Watchtower → Transactions](transactions.md) — **ไม่มีคำว่า "Submitted" ในที่ไหนเลย** คาดว่าอาจเป็น**สถานะเริ่มต้นสุดก่อนแม้แต่ Pending** หรืออาจเป็นคำเรียกอื่นของ Pending/Processing เฉพาะในบริบทของหน้า Reports เท่านั้น ยังไม่ยืนยันแน่ชัด

## Daily Cash Position

ตาราง snapshot รายวันของ instance นี้: DATE, ACCOUNTS (จำนวนบัญชี), TOTAL BALANCE

!!! tip "ประวัติย้อนหลังระดับวัน"
    ตัวอย่างจริงแสดงจำนวนบัญชีเพิ่มขึ้นต่อเนื่องในสัปดาห์ที่สำรวจ (67 → 68 → 72 บัญชี) — แสดงว่ามีการเพิ่มบัญชีธนาคารใหม่เข้าระบบต่อเนื่อง เหมาะสำหรับ audit trail ย้อนหลังดูว่ายอดคงเหลือรวมของ instance เปลี่ยนแปลงยังไงแต่ละวัน
