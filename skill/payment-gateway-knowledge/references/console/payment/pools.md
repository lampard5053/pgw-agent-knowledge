# Payment Pools

!!! tip "Payment Pool คืออะไร"
    Pool คือกลุ่มของ [Payment Channel](channels.md) (บัญชีธนาคารจริงหลายบัญชี) ที่ใช้ **Selection Strategy** ร่วมกัน — **Merchant ไม่ได้มีบัญชีของตัวเองตรงๆ แต่ถูก assign เข้า Pool** ที่มีหลายบัญชีให้ใช้ร่วมกัน แล้วระบบเลือกว่าจะ route เงินเข้า/ออกบัญชีไหนตามกลยุทธ์ที่ตั้งไว้

![รายการ Payment Pools](../../assets/screen-short/payment-pools-list.jpg "รายการ Payment Pools — คอลัมน์ Strategies แสดงกลยุทธ์ของแต่ละ action")

## ฟอร์ม Create Payment Pool + Selection Strategy

![Create Pool](../../assets/screen-short/payment-pools-create-form.jpg "ฟอร์ม Create Payment Pool — 5 Strategy dropdown")

=== "Deposit Strategy"
    ![Deposit Strategy](../../assets/screen-short/payment-pools-deposit-strategy-dropdown.jpg "Default, Round Robin Frequency, Round Robin Frequency In/Out, Most Available, Balance")
=== "Withdraw Strategy"
    ![Withdraw Strategy](../../assets/screen-short/payment-pools-withdraw-strategy-dropdown.jpg "dropdown เดียวกันใช้ได้ทุก action")

| Strategy | ความหมาย (คาดการณ์จากชื่อ) |
|---|---|
| `Default` | ใช้ลำดับ/กติกาพื้นฐานของระบบ |
| `Round Robin Frequency` | วนใช้บัญชีตามจำนวนครั้งที่เคยถูกใช้ |
| `Round Robin Frequency In/Out` | วนแยกฝั่ง deposit-in กับ withdraw-out |
| `Most Available` | เลือกบัญชีที่เหลือ limit/วงเงินเยอะที่สุด |
| `Balance` | เลือกบัญชีตามยอดคงเหลือ |

!!! question "คำถามค้าง"
    รายละเอียด logic ที่แท้จริงของแต่ละ strategy ยังไม่มีการยืนยัน เป็นการตีความจากชื่อเท่านั้น

## Payment Pool Details

![Balance Information](../../assets/screen-short/payment-pools-balance-information.jpg "Payment Pool Details — Balance Information แยก Usable/Available ต่อประเภท")

![Transaction Limits](../../assets/screen-short/payment-pools-transaction-limits.jpg "Transaction Limits ระดับ Pool — มี Envelope Maximum + Accepted Ranges ต่อประเภท")

| ฟิลด์ | รายละเอียด |
|---|---|
| Envelope Maximum | เพดานรวมสูงสุดที่ pool นี้ยอมรับต่อประเภท action |
| Accepted ranges | ช่วง min-max ของยอดเงินต่อ transaction ที่ pool ยอมรับ |
| Withdraw Fee (Currently unavailable) | พบสถานะนี้ในตัวอย่าง — อาจหมายถึงยังไม่ถูก config |

## Merchant ที่ผูกอยู่ใน Pool

![Merchants in pool](../../assets/screen-short/payment-pools-merchants-in-pool.jpg "Merchants: shared — pool 'shared' ผูกกับ 43 merchants")

!!! tip "ยืนยันความสัมพันธ์ Pool ↔ Merchant"
    Pool ชื่อ "shared" ผูกกับ **43 merchants** พร้อมกัน — ยืนยันชัดว่า **1 Pool : หลาย Merchant** ได้ มีปุ่ม "Assign Merchant" ให้เพิ่ม merchant เข้า pool ได้จากหน้านี้โดยตรง

## Payment Channel ที่อยู่ใน Pool (พร้อม Capabilities)

![Channels in pool](../../assets/screen-short/payment-pools-channels-in-pool.jpg "Payments: shared — รายการ Channel ในพูล พร้อม Capabilities (DEP/WDR/SET/FEE)")

| ฟิลด์ | รายละเอียด |
|---|---|
| Fees (0% + 0) | fee ของ channel นั้นในบริบท pool นี้ |
| Balance / D / W | ยอดคงเหลือ + วงเงิน deposit/withdraw ที่เหลือวันนี้ |
| Match | Match Type ของ channel นั้น (เช่น account_balance) |
| Capabilities: DEP / WDR / SET / FEE | checkbox เปิด-ปิดว่า channel นี้ใช้ทำอะไรได้บ้างในพูลนี้ |

!!! tip "Match Type ที่ 2 — 'account_balance'"
    พบว่า Match Type ของ channel ในพูลนี้เป็น `account_balance` ไม่ใช่ `Digits` — ยืนยันว่ามีวิธี match มากกว่า 1 แบบใช้งานจริงพร้อมกัน (ดู [Deep-dive: Match Type](../match-type.md))

## ฟอร์ม Edit Payment Pool

![Edit Pool](../../assets/screen-short/payment-pools-edit-modal.jpg "Edit Payment Pool — เปลี่ยน strategy ทีหลังได้ทุกอัน")
