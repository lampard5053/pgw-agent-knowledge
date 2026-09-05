# Watchtower — ภาพรวม

**"Watchtower"** คือระบบมอนิเตอร์ระดับสูงสุดที่รวมภาพรวมของ**พาร์ทเนอร์ (Partner) ทั้งหมดของ PGW พร้อมกัน** — เข้าถึงผ่านโดเมนแยกต่างหาก พบจริงที่ `watchtower.pgway.work` (โดเมนตระกูลเดียวกับ `opsdash.pgway.work`)

!!! tip "ยืนยันจากทีมงานโดยตรง"
    11 instance ที่เห็นในหน้านี้คือ**พาร์ทเนอร์ทั้งหมดของ PGW** — แต่ละพาร์ทเนอร์ทำได้ทุกอย่างเหมือน JoinPay ที่ใช้เป็นตัวอย่างหลักในเอกสารชุดนี้ (มี Console/OpsDash/instance เป็นของตัวเองครบเหมือนกันหมด ไม่ใช่แค่ลูกค้าที่ยืม tech ไปใช้เฉยๆ)

!!! danger "หมายเหตุความแม่นยำของเอกสารส่วนนี้"
    เช่นเดียวกับ [Admin Console](../console/index.md) และ [OpsDash](../opsdash/index.md) เอกสารนี้เขียนจากการสังเกตหน้าจอ UI จริงเท่านั้น จุดที่เป็นการวิเคราะห์/อนุมานจะมีกล่อง **❓ คำถามค้าง** กำกับไว้เสมอ ดูสรุปคำถามค้างทั้งหมดที่ [คำถามที่ยังไม่มีคำตอบ](../open-questions.md)

    **เมนู "Analytics" ยังไม่ได้สำรวจ** — ไม่มีข้อมูลใดๆ ให้บันทึกในเอกสารชุดนี้

## Watchtower คืออะไร (ทวนสั้นๆ)

ถ้า [OpsDash](../opsdash/index.md) คือชั้นที่เชื่อมต่อกับธนาคารจริง (ผ่าน Bot/API) ของ**แต่ละ instance เดียว** — **Watchtower คือชั้นที่มองภาพรวมข้าม instance ทั้งหมดพร้อมกัน** ตั้งแต่ balance รวม, สุขภาพบัญชี, ธุรกรรม, ไปจนถึงการแจ้งเตือน — เหมาะสำหรับทีม ops ที่ต้องดูแลหลายแบรนด์พร้อมกันในหน้าเดียว

## Multi-partner — พบ 11 instance ทั้งหมด

หน้า Dashboard (โหมด "All instances") และ dropdown "Select Instance" เผยรายชื่อพาร์ทเนอร์ PGW ที่ Watchtower ดูแลครบทั้งหมด:

| Instance | สถานะ |
|---|---|
| JoinPay | Active |
| PaymentPlus | Active |
| NextPay | Active |
| ManeePay | Active |
| PayOk | Active |
| GGPAY | Active |
| EazyPay | Active |
| MkcPay | Active |
| TPay | Active |
| Stark | **Inactive** |
| GodPay | **Inactive** |

!!! danger "เชื่อมกับ OpsDash — JoinPay/NextPay เป็นแค่ 2 ใน 11 พาร์ทเนอร์"
    OpsDash เคยเผยแค่ 3 profile (JoinPay, NextPay, HW) — Watchtower เผยว่ามีอย่างน้อย **11 พาร์ทเนอร์ PGW** ในเครือข่ายเดียวกัน ไม่ใช่แค่ JoinPay + NextPay + HW ตามที่เคยเข้าใจ ("HW" ไม่ปรากฏในรายชื่อ Watchtower เลย — ยืนยันแล้วว่าเป็น profile/entity เดียวกับ JoinPay ดู [OpsDash — ภาพรวม](../opsdash/index.md))

## ความสัมพันธ์ระหว่างพาร์ทเนอร์ — ไม่ได้แยกกันเด็ดขาด

หลักฐานจาก [Groups](groups.md) และ [Direct Sync](direct-sync.md) ยืนยันว่า**บัญชีธนาคารถูกใช้ร่วมกันข้ามพาร์ทเนอร์ได้จริง** เช่น กลุ่ม "NextPayXJoinPay" ที่มีคำอธิบายตรงตัวว่า "เป็นบัญชีที่ทาง NextPay นำมาเปิด Partner ในฝั่งของ JoinPay" พร้อมพนักงาน NextPay จริง (อีเมล `@nextpay.app`/`@nextpay.biz`) มีสิทธิ์ Operator เข้าถึงข้อมูลฝั่ง JoinPay ได้ — ยืนยันว่า NextPay กับ JoinPay ร่วมมือกันจริงในระดับปฏิบัติการ ไม่ใช่แค่คนละพาร์ทเนอร์ที่แยกขาดจากกันโดยสิ้นเชิง

## โครงสร้างเมนูทั้งหมด

Sidebar แบ่งเป็น 3 กลุ่ม:

| กลุ่ม | เมนู |
|---|---|
| **OVERVIEW** | [Dashboard](dashboard.md) |
| **INSTANCE** (ต้องเลือก instance เดียวก่อนถึงจะใช้ได้) | [Accounts](accounts.md), [Transactions](transactions.md), [Direct Sync](direct-sync.md), [Jobs](jobs.md), Analytics (ยังไม่สำรวจ), [Flow](flow.md), [Reports](reports.md) |
| **MANAGE** | [Groups](groups.md), [Notifications](notifications.md) |

!!! tip "ยืนยันจากการสังเกต UI"
    ตอนเลือก "All instances" เมนูกลุ่ม INSTANCE ทั้งหมดจะเป็นสีเทา/ใช้ไม่ได้ — ต้องเลือก instance เดียว (เช่น JoinPay) ก่อนถึงจะเข้าเมนูเหล่านี้ได้ ยกเว้นกลุ่ม MANAGE ที่ใช้ได้เสมอ

## ความสัมพันธ์กับ OpsDash — คนละมุมมอง ข้อมูลชุดเดียวกัน

หลักฐานหลายจุดยืนยันว่า Watchtower **ไม่ได้เก็บข้อมูลแยกต่างหาก** แต่มองเข้าไปในข้อมูลชุดเดียวกับ [OpsDash](../opsdash/index.md):

- เลขบัญชี `020475875405` ใน [Watchtower → Accounts](accounts.md) ตรงกับบัญชีที่เห็นใน OpsDash → Bot เป๊ะ
- Job Ref `019fa4d7-4ab1-7000-a1d0-acbe9cba2de0` ใน [Watchtower → Jobs](jobs.md) ตรงกับ Job ที่เห็น raw log ("device RFCR20RQQAE not found") ใน OpsDash → Job เป๊ะทุกตัวอักษร
- Kind enum ที่เจอ (KTBBIZ, KBIZ, GSB, SCBBIZ, SCB) ตรงกับ Kind ของ OpsDash → Bank Account

**คาดว่า**เชื่อมกันผ่านเมนู [Direct Sync](direct-sync.md) หรือกลไก sync เบื้องหลังอื่น — ยังไม่มีหลักฐานยืนยันกลไก sync ที่แท้จริงระดับ infra

!!! danger "สมมติฐานที่ชัดขึ้น หลังเห็น PGWay — Watchtower/OpsDash/PGWay น่าจะอ่านฐานข้อมูลกลางเดียวกัน"
    [PGWay → Devices](../pgway/devices.md) มี background job ชื่อ **"Balance Poller"** ที่ทำหน้าที่รีเฟรช balance ของแต่ละอุปกรณ์แบบ round-robin ทุก 300 วินาที — ตรงกับ timestamp "Xm ago" ที่เห็นซ้ำๆ กันทั้งใน [Dashboard](dashboard.md), [Accounts](accounts.md) และหน้า Bank Account ของ OpsDash **คาดว่า** ทั้ง 3 ระบบ (OpsDash, Watchtower, PGWay) อ่าน/เขียนฐานข้อมูลกลางเดียวกัน โดย PGWay ทำหน้าที่เป็นตัว poll ข้อมูลสดจากอุปกรณ์จริงเข้าไปเก็บ ส่วน OpsDash/Watchtower เป็นแค่ 2 มุมมอง (UI) ที่อ่านข้อมูลเดียวกันออกมาแสดงคนละแบบ — ยังไม่ยืนยัน 100%
