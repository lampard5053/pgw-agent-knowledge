# Jobs

Breadcrumb: "{Instance} > Jobs" — มุมขวาบน: toggle Auto + countdown + ปุ่มรีเฟรชมือ

!!! danger "ยืนยัน 100% — ข้อมูลชุดเดียวกับ OpsDash Job เป๊ะทุกแถว"
    ตัวอย่าง Job Ref `019fa4d7-4ab1-7000-a1d0-acbe9cba2de0` / Bank Ref `BULK260728409232` / State **Failed** — ตรงกับ Job ที่เห็น raw log ("device RFCR20RQQAE not found") ใน [OpsDash → Job](../opsdash/jobs.md) มาก่อนเป๊ะทุกตัวอักษร ยืนยันชัดเจนว่า Watchtower เป็นหน้าต่างมอนิเตอร์ทับข้อมูล OpsDash ตัวเดียวกัน

## การ์ดสรุปสุขภาพ 2 ใบ — ฟีเจอร์ที่ OpsDash ไม่มี

| การ์ด | ค่า (ตัวอย่างจริง) | ความหมาย |
|---|---|---|
| **STUCK > 5M** ⚠️ | 0 | "processing with no progress" — จำนวน job ที่ค้างสถานะ Processing เกิน 5 นาทีโดยไม่คืบหน้า |
| **PROCESSING** 🔄 | 0 | "live jobs in flight" — จำนวน job ที่กำลังรันสดๆ ตอนนี้ |

## Filter
Job Ref, Bank Ref, Bot ID, State (All states)

## ตาราง Jobs

| คอลัมน์ | รายละเอียด |
|---|---|
| STATE | Success (เขียว) / Failed (แดง) |
| JOB REF | UUIDv7 |
| BANK REF | รูปแบบ `BULK{YYMMDD}{เลขลำดับ}` |
| CLIENT | เช่น `core` |
| KIND | เช่น `KBIZ` |
| **BOT** | Bot ID — แสดงตรงในคอลัมน์ (OpsDash ต้องเปิด modal ถึงจะเห็น) |
| STARTED | วันเวลาเริ่ม |
| **DURATION** | เวลาที่ job ใช้ไป (เช่น 46s, 3m 2s) — คำนวณให้อัตโนมัติ (OpsDash ไม่มีคอลัมน์นี้ ต้องคำนวณเองจาก Started/Completed) |
| ACTIONS | เมนู "..." |

## Action menu — เจอที่มาของ Transaction State "Manual Success"

เปิดจากปุ่ม "..." สำหรับ job ที่ **Failed**: **Cancel, Force Success**

!!! danger "เชื่อมกับ Transaction State 'Manual Success/Manual Failed' ที่เห็นในหน้า Transactions"
    **"Force Success"** คือปุ่มที่ ops กดเพื่อ**บังคับให้ job ที่ bot ทำไม่สำเร็จ ถือว่าสำเร็จ** (น่าจะใช้ตอนตรวจสอบด้วยตาแล้วว่าธุรกรรมจริงผ่านแล้ว แค่ bot verify ไม่ได้เพราะ error ทางเทคนิค เช่น device หลุด) — **คาดว่า**นี่คือที่มาของ state "Manual Success" ที่เห็นใน [Transactions](transactions.md) — action นี้อยู่ที่ระดับ **Job** ไม่ใช่ที่ Transaction โดยตรง แต่ผลลัพธ์กระทบ Transaction state ที่ผูกกับ Job นั้น
