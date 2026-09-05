# Transaction Status Reference

สรุปความเข้าใจปัจจุบันเกี่ยวกับ Transaction Status/Type ทั้งหมด ที่ได้จากการนั่งไล่วิเคราะห์ร่วมกับทีม Support โดยตั้งกรอบเป้าหมายทางธุรกิจของแต่ละ Type ไว้ก่อนเป็นหลักในการตรวจสอบ — ดูเคสตัวอย่างจริงประกอบที่ [เคสที่พบบ่อย](common-cases.md)

## กรอบเป้าหมายทางธุรกิจ (ground truth) ของแต่ละ Type

| Type | ปลายทางที่ควรเกิดขึ้นจริง |
|---|---|
| Deposit | คนโอนเงินมาจริง → ได้เครดิตเข้า wallet |
| Withdraw | คนขอถอน → ได้เงินจริง + เครดิตถูกหักออกจาก wallet |
| Topup | เจ้าของร้านค้า → เงินเข้าบัญชีธนาคารจริงของ Pool/Channel |
| Settlement | เจ้าของร้านค้า → เงินออกจากระบบไปเข้าบัญชีธนาคารจริงของร้าน |

## สถานะความรู้ปัจจุบันของ Status ทั้ง 11 ตัว

| Status | ความหมาย | in-place หรือสร้าง TX ใหม่? | หลักฐาน |
|---|---|---|---|
| Created | เพิ่งสร้าง pre-transaction/transaction ยังไม่มีเงินขยับ / **หรือไม่มี Job ใดไปแตะเลย (orphan)** | in-place (ไม่เปลี่ยนแปลงเลยถ้าไม่มีอะไรมาแตะ) | ดู [เคสที่ 3](common-cases.md#เคสที่-3) |
| Processing | กำลังดำเนินการ | — | ยังไม่มีตัวอย่างจริง |
| Unmatched | เงินเข้ามาแต่จับคู่กับ pre-transaction ไหนไม่ได้ (= unknown_deposit) | ต้นทาง (ค้างจนกว่าจะมีคน Match/Manual Success) | ยืนยันแล้วจากหลายเคส ดูหัวข้อ [เจาะลึก Unmatched](#เจาะลึก-unmatched) |
| Refunding | กำลังคืนเงิน (settlement/withdraw ล้มเหลว) | **สร้าง TX ใหม่** (มี "Refund TX ID" ผูก) | ยืนยันจาก Payment Receipt "Settlement ล้มเหลว" ใน [Admin Console → Transactions](../console/payment/transactions.md) |
| Refunded | คืนเงินสำเร็จแล้ว | (terminal ของ TX ที่สร้างใหม่นั้น) | อนุมานจาก Refunding |
| Success | สำเร็จปกติ | in-place | ยืนยันเยอะมาก |
| Failed | มี Job พยายามทำแล้วจริง แต่เจอ error ระหว่างทาง | in-place | ยืนยันจาก [เคสที่ 3](common-cases.md#เคสที่-3) (Bot ติด CAPTCHA) |
| Canceled | ถูกยกเลิก (โดยลูกค้าเองหรือแอดมิน) | in-place | ยืนยันแล้วมีปุ่ม Cancel + เจอตัวอย่างจริงใน [เคสที่ 4](common-cases.md#เคสที่-4) |
| Manual Success | แอดมินบังคับสำเร็จด้วยมือ (ไม่มี pre-transaction ให้จับคู่แบบ formal) | **in-place** (Type ไม่เปลี่ยน ยังเป็น Unknown Deposit) | ยืนยันละเอียดจาก [เคสที่ 2](common-cases.md#เคสที่-2) |
| Manual Failed | แอดมินบังคับล้มเหลวด้วยมือ | — | **ยังไม่เคยเห็นตัวอย่างเลย** |
| Expired | pre-transaction/deposit request หมดเวลารอ ไม่มีเงินตรงตามที่คาดเข้ามา | (เป็น TX/PreTransaction คนละตัวจาก Unknown Deposit ที่เกิดจากเงินจริง) | ยืนยันจาก [เคสที่ 2](common-cases.md#เคสที่-2) และ [เคสที่ 4](common-cases.md#เคสที่-4) |

## ไขปริศนา Match vs Manual Success {: #match-vs-manual-success }

กรอง Transactions ด้วย **Status = Manual Success** พบ 6 ตัวอย่างจริง ทุกแถวเป็น **Type: UNKNOWN DEPOSIT** ทั้งหมด (ต่างจากเคส Match ที่ Type เปลี่ยนเป็น "Deposit" ปกติ) และหลายแถวมี **To Account เดียวกันหมด** (`0170739155` KTB) แต่ **Merchant ต่างกันคนละร้าน** (`br-5588`, `marang777`, `popsloth`, `br-x89`)

!!! danger "ไขคำถามค้างแล้ว — Manual Success ใช้เมื่อไม่มี pre-transaction ให้จับคู่แบบ formal"
    สาเหตุคือ **บัญชีธนาคารเดียวกันถูกใช้ร่วมกันระหว่างหลายร้านค้าพร้อมกัน** (ตรงกับ concept "shared" Payment Pool ที่เคยเจอใน [Admin Console → Payment Pools](../console/payment/pools.md)) — เมื่อมีเงินโอนเข้าบัญชีที่ใช้ร่วมกันแบบนี้ ระบบไม่มีทางรู้อัตโนมัติว่าเป็นของร้านไหน (ไม่มี unique amount/QR reference ผูกกับ pre-transaction เฉพาะเจาะจง) ธุรกรรมจึงค้างเป็น **Unknown Deposit ถาวร**

    เจ้าหน้าที่ต้องอาศัยบริบทอื่น (เช่น ติดต่อร้านค้าถามยอดที่รอ, เทียบกับคำขอที่ pending อยู่) เพื่อสรุปเองว่าเงินก้อนนี้เป็นของร้านไหน แล้วกด **Manual Success** เพื่อ**บังคับเครดิตให้ merchant นั้นโดยตรง** — ต่างจาก Match ตรงที่ **Type ไม่เปลี่ยนเป็น "Deposit" เลย ยังคงเป็น "Unknown Deposit" ตลอดไป** (เพราะไม่เคยมี pre-transaction ที่แท้จริงให้จับคู่ด้วยตั้งแต่แรก) มีแค่ Status ที่เปลี่ยนเป็น `Manual_success` เพื่อบอกว่า "แก้ไขให้แล้วด้วยมือ"

    **สรุปเทียบ:**

    | | มี pre-transaction ให้จับคู่ | Type หลังแก้ | Status หลังแก้ |
    |---|---|---|
    | **Match** | มี — จับคู่กับคำขอที่มีอยู่จริงในระบบ | Deposit (ปกติ) | Success |
    | **Manual Success** | ไม่มี — เจ้าหน้าที่ใช้ดุลยพินิจ/บริบทนอกระบบตัดสินเอง | Unknown Deposit (ค้างเดิม) | Manual_success |

ดูตัวอย่างจริงเต็มรูปแบบที่ [เคสที่ 1](common-cases.md#เคสที่-1) (Match) และ [เคสที่ 2](common-cases.md#เคสที่-2) (Manual Success)

### Payment Receipt modal — ฟิลด์เพิ่มเติม

เปิดจากปุ่ม 👁 ในตาราง Transactions — พบฟิลด์ที่ไม่เคยบันทึกไว้มาก่อน:

| ฟิลด์ | ตัวอย่างค่าจริง |
|---|---|
| Payment Channel | `EasyPay-ktbbiz-Minoren-0170754324-B` (ชื่อ Payment Channel จริงที่ธุรกรรมนี้วิ่งผ่าน) |
| More Details (collapsed) | `฿127.01` — ตัวเลขต่างจาก Amount (฿130.00) |

!!! danger "ยืนยันแล้ว — 'More Details' คือ Settle Amount"
    ยืนยันจาก Transaction Troubleshooting dump ของ transaction ใน [เคสที่ 2](common-cases.md#เคสที่-2) ที่มี field ชัดเจนว่า `Settle Amount: ฿1,465.50` (Amount ฿1,500.00 − Total Fee ฿34.50 = ฿1,465.50 ตรงเป๊ะ) — **"More Details" ในหน้า Payment Receipt modal ก็คือ Settle Amount** ตัวเดียวกันนี้เอง แค่แสดงแบบย่อ/collapsed

## เจาะลึก "Unmatched" — backlog ใหญ่ที่สุด {: #เจาะลึก-unmatched }

Filter Transactions ด้วย Status = Unmatched พบมากกว่า 100 รายการ ทุกแถว Merchant = N/A

!!! danger "ยืนยันว่า Match/Manual Success อัปเดต transaction เดิมในตัว ไม่สร้างซ้ำทิ้งไว้"
    จากหลักฐาน 2 เคสที่ยืนยันแล้ว (Match ใน [เคสที่ 1](common-cases.md#เคสที่-1), Manual Success ใน [เคสที่ 2](common-cases.md#เคสที่-2)) — **transaction ID เดิมถูกอัปเดต Status/Type ในตัวเองโดยตรง ไม่ได้สร้าง TX ใหม่แยกทิ้งของเก่าค้างไว้** ดังนั้นรายการที่ยัง Unmatched ค้างอยู่ตอนนี้ **น่าจะเป็นเงินที่ยังไม่เคยถูกแก้เลยจริงๆ** ไม่ใช่ของที่แก้แล้วแต่มีตัวซ้ำหลงเหลือ (ยังไม่ยืนยัน 100% ว่าไม่มีข้อยกเว้นแบบ Refund ที่สร้าง TX ใหม่)

!!! danger "เจอ Type ใหม่ที่ไม่เคยมีตัวอย่างจริงมาก่อน — 'Unknown Withdraw'"
    เป็นปัญหาเดียวกับ Unknown Deposit แต่กลับทิศทาง — บัญชี shared channel เดียวกัน (`0170739155`) จ่ายเงินออกไปหาหลายคนที่ไม่เกี่ยวข้องกัน ระบบไม่รู้ว่าเป็นการถอนของ merchant ไหน เหมือนกับที่ไม่รู้ว่าเงินฝากเข้าเป็นของใคร — ยืนยันว่าปัญหารากมาจาก**บัญชีที่ใช้ร่วมกันหลาย merchant**เหมือนกันทั้ง 2 ทิศทาง

!!! question "คำถามค้าง — Unknown Deposit มีวันหมดอายุ/เก็บกวาดอัตโนมัติไหม"
    PreTransaction/Deposit request มี "Expired" เมื่อรอเงินไม่ทัน แต่ **"Unknown Deposit" ที่เป็นเงินจริงที่เข้ามาแล้วจับคู่ไม่ได้ ดูเหมือนไม่มีกลไกหมดอายุ/เก็บกวาดอัตโนมัติเลย** ปล่อยค้างเป็น Unmatched ตลอดไปจนกว่าจะมีคนมาแก้ด้วยมือ — ถ้าจริง backlog นี้จะพอกพูนขึ้นเรื่อยๆ ไม่มีวันลดลงเอง

ดูตัวอย่างพฤติกรรมลูกค้าที่ทำให้เกิด backlog นี้ที่ [เคสที่ 4](common-cases.md#เคสที่-4)

### backlog นี้เคลียร์ได้ยังไง — และมีอะไรบ้างที่ไม่ถูกนับรวม

!!! tip "ยืนยันจาก 2 เคส — Unmatched เคลียร์ได้ก็ต่อเมื่อมีคนแจ้งเข้ามาจากภายนอกระบบ"
    ทั้ง [เคสที่ 1](common-cases.md#เคสที่-1) (ลูกค้าส่งสลิปตรงเข้าห้อง Support) และ [เคสที่ 2](common-cases.md#เคสที่-2) (แอดมินร้าน `br-5588` ส่งรูปยืนยันเข้าห้อง Telegram) ถูกเคลียร์เพราะ **มีคนส่งหลักฐาน/แจ้งเข้ามาก่อน** ไม่ใช่แอดมินไล่เดาเองหรือระบบตรวจจับอัตโนมัติ — **ยังไม่เคยเห็นเคสที่แอดมินแก้เชิงรุกโดยไม่มีใครแจ้งมาก่อนเลย** จึงยังฟันธง 100% ไม่ได้ว่าไม่มีทางอื่น แต่หลักฐานที่มีตอนนี้สนับสนุนว่า **backlog นี้แทบทั้งหมดรอ "คนมาเคลม" เป็นหลัก**

!!! danger "ของที่ 'สร้าง pretransaction มั่วๆ แล้วไม่โอนจริง' ไม่ถูกนับรวมใน Unmatched — เป็นคนละ backlog กัน"
    ต้องแยกให้ชัดว่า Unmatched/Unknown Deposit คือ**เงินจริงที่เข้าบัญชีแล้ว**เท่านั้น ไม่ปนกับคำขอที่ไม่เคยมีเงินเข้าจริง:

    | กรณี | เงินจริงเข้าไหม | สถานะที่ค้าง | นับใน Unmatched filter ไหม |
    |---|---|---|---|
    | ลูกค้าสร้าง pretransaction (กดขอฝาก) แต่ไม่โอนจริง/เปลี่ยนใจ (เช่น [เคสที่ 4](common-cases.md#เคสที่-4)) | ❌ ไม่มีเงินเข้า | Created (ไม่มี Job แตะ) → สุดท้าย Expired | **ไม่นับ** — คนละ Type/Status |
    | มีเงินจริงโอนเข้าบัญชี shared channel แต่จับคู่กับ pretransaction ไหนไม่ได้ | ✅ มีเงินเข้าจริง | Unknown Deposit / Unmatched | **นับ** — นี่คือก้อนที่เห็นเป็น 100+ แถวในหน้า Transactions |

    พูดง่ายๆ คือตัวเลข Unmatched ที่เห็นในหน้า Transactions เป็น **เงินจริงที่นอนรอคนมาเคลมล้วนๆ** ไม่ปนกับคำขอฝากที่ไม่เคยสำเร็จ
