# Transaction Flows

ทุกเคสที่เกิดขึ้นได้จริงในระดับ API — Happy Path และ Edge Cases สำหรับ Deposit, Withdraw, Top-up, Settlement

## State Machine — ภาพรวมทุก Flow

ทุก transaction (deposit/withdraw) เดินตาม state machine เดียวกัน เข้าใจ flow นี้แล้วจะเข้าใจทุก flow ในหน้านี้ง่ายขึ้น

```
Create Customer (ต้องทำก่อนเสมอ)
        │
   ┌────┴────┐
   ▼         ▼
Deposit    Withdraw
Request    Request
   └────┬────┘
        ▼
Transaction: created
        │
   ┌────┼────┐
   ▼    ▼    ▼
success failed canceled (Admin)
   │    └──┬──┘
   ▼       ▼
success  unhold
callback callback

──────────────────────────────
unmatched (independent state — ไม่ผูกกับ pre-transaction ไหนเลย)
```

| อ่าน diagram ยังไง | |
|---|---|
| `failed` และ `canceled` | ทั้งคู่จบที่ **unhold callback** — คืนเงิน (+fee) กลับ Merchant wallet เสมอ |
| `success` | จบที่ **success callback** เท่านั้น — ไม่มี unhold ตามมา |

!!! danger "unmatched คือคนละเรื่องกับ failed/canceled"
    `unmatched` (เอกสาร API เรียก `unknown_deposit`) เกิดเมื่อเงินโอนเข้าจริงแต่ match กับ pre-transaction ไม่ได้เลย (จำนวนผิด / หมด `expiredAt`) — **ไม่ได้อยู่ใน lifecycle ปกติของ transaction ใดๆ** ต้อง manual reconcile แยกต่างหาก ไม่ได้เกิดจากการที่ transaction เปลี่ยนสถานะไปมา

    ฝั่ง Admin Console เรียกสถานะนี้ว่า **Unmatched** และมีกลไก manual reconcile ทั้ง Match / Manual Success — ดูรายละเอียดกลไกจริงพร้อมเคสตัวอย่างที่ [ทีม Support → Transaction Status Reference](../support/status-reference.md)

## Deposit — 9 ขั้นตอน (Happy Path)

| # | ผู้ทำ | รายละเอียด |
|---|---|---|
| 1 | Merchant → Core API | ยิง `POST /transaction/deposit/request` พร้อม `customerUuid` + `amount` (integer) |
| 2 | Core — สร้าง Pre-transaction | บันทึก record ใน DB, gen unique suffix (เช่น `.47`), ตั้ง `status: created`, `expiredAt` +10 นาที — วิธี match: ① unique amount ② เลขบัญชีของ customer (ถ้าระบบรู้) |
| 3 | Core → Merchant (Response 201) | ส่ง QR code พร้อม unique amount กลับ — Merchant นำ QR ไปแสดง End User |
| 4 | End User → ธนาคาร | เปิด Mobile Banking → สแกน QR → โอน **1000.47 บาทพอดี** ไปบัญชี PGWM (เป็นการโอนจริงในระบบธนาคาร ไม่มี API call) |
| 5 | ธนาคาร → Bank Adapter | ธนาคาร push notification เข้า Bank Adapter ทันทีที่มีเงินเข้า: `amount: 1000.47`, `senderAccountNo: "1234567890"`, `senderBankCode: "KBNK"` |
| 6 | Bank Adapter → Core (Internal) | ส่ง event แจ้ง Core → Core ค้นหา pre-transaction ที่ `settleAmount = 1000.47` → **Match สำเร็จ** |
| 7 | Core — Update DB | `status → success`, บันทึก sender account, `settleAt`, `fee` แล้วบวก net amount เข้า Merchant wallet (หัก platform fee) |
| 8 | Core → Merchant (Webhook) | POST ไปที่ `callback_url` พร้อม HMAC signature: `type: "deposit"`, `status: "success"`, `amount: "1000.47"`, `fee: "13.50"` |
| 9 | Merchant — บวกเครดิต End User | ตรวจ HMAC → check idempotency → บวกเครดิตในระบบของ Merchant → ตอบ `200 OK` |

### Edge Cases

!!! danger "โอนผิดจำนวน (Under/Over-transfer)"
    **Under:** Pre-transaction 400.67 แต่โอนมา 300 → match ไม่ได้ → `unknown_deposit`

    **Over:** Pre-transaction 100.23 แต่โอนมา 200 → match ไม่ได้ → `unknown_deposit`

    **แก้ไข:** End User ส่งสลิปให้ Admin → Admin ส่ง uuid + สลิปให้ PGW Support → manual reconcile หรือ `verify-slip` API

!!! danger "หมด expiredAt แต่เงินเข้าช้า"
    Core cancel pre-transaction ไปแล้ว → เงินที่โอนเข้ามาทีหลังกลายเป็น `unknown_deposit` — แก้ไขโดยส่ง uuid + สลิปให้ Support ทำ manual reconcile

!!! danger "Fake Slip — โอนจริง 0.50 บาท แต่ส่งสลิปปลอม 500 บาท"
    ถ้า Admin เห็นแค่สลิปภาพถ่าย "500 บาท" แล้วเติมเครดิตให้ End User ตรงๆ — แต่เงินจริงเข้าแค่ 0.50 บาท ระบบจะขาดทุนทันที

    **ป้องกัน:** ใช้ `POST /verify-slip` ส่ง TLV จากสลิปจริง ระบบจะ cross-check กับ Bank Adapter ให้ — สลิปปลอม (TLV ไม่ตรง) จะถูก reject, สลิป replay จะได้ `409 slip_already_used`

## Withdraw — Happy Path + Edge Cases

| # | ผู้ทำ | รายละเอียด |
|---|---|---|
| 1 | Merchant — ตรวจก่อน Withdraw | `GET /profile/balance` ≥ amount + fee, `GET /withdraw/limit`, customer status = active |
| 2 | Merchant → Core API | `POST /transaction/withdraw/request` → Core debit (`amount + fee`) จาก wallet ทันที → `status: created` |
| 3 | Core → Bank Adapter → ธนาคาร | Core สั่ง Bank Adapter โอนเงินออก → ธนาคาร process |
| 4 | ธนาคาร → Bank Adapter → Core | ธนาคาร confirm → Core อัปเดต `status → success` → ยิง webhook |
| 5 | Merchant — รับ Webhook | `{ type: "withdraw", status: "success" }` → หักเครดิต End User ในระบบของ Merchant |

!!! danger "ธนาคารปลายทาง Reject → Unhold"
    บัญชี End User ปิด/ชื่อไม่ตรง → เงิน bounce กลับ T+1 ถึง T+2 วัน → Bank Adapter เห็น → Core refund ผ่าน **unhold event**: `{ type: "unhold", status: "success", amount: "513.50", fee: "0" }`

    Merchant ต้อง refund เต็ม `amount` — **ห้ามหัก fee ซ้ำ** เพราะ fee ถูก reverse กลับมาพร้อมกันแล้ว

!!! danger "P2P 500 Error (p2p_request_in_process)"
    ห้าม retry ทันที — ใช้ `GET /transaction/{uuid}` ตรวจ status ก่อน ถ้ายัง `processing` ให้รอ

## Top-up — เติมทุน Merchant Wallet

Merchant wallet ต้องมีเงินสำรองเพื่อรองรับ withdraw ของ End User — Top-up คือการที่ Merchant โอนเงินจากบัญชีบริษัทไปบัญชี PGW ที่กำหนดไว้ แล้ว PGW credit เข้า wallet

!!! tip "ต่างจาก Deposit ตรงไหน"
    Top-up เป็นการโอนระหว่าง **Merchant กับ PGW** — ไม่มี End User เกี่ยวข้องเลย ต่างจาก Deposit ที่เป็นการโอนจาก End User

**Flow Top-up:**

1. Finance ของ Merchant โอนเงินไปบัญชี top-up ของ PGW
2. Bank Adapter เห็นเงินเข้า → ตรวจ source account ว่าตรงกับ Merchant
3. Core credit เงินเข้า Merchant wallet
4. Merchant ได้รับแจ้ง (email/webhook ตาม config)

แนะนำตั้ง alert เมื่อ balance ต่ำกว่า threshold แล้วให้ Finance top-up ก่อนช่วง peak (เย็น/กลางคืน) — buffer ที่ควรมีคือ ≥ ยอดถอนสูงสุดต่อวัน × 1.5 เท่า

!!! danger "ถ้าไม่ top-up ทัน"
    Withdraw จะ fail `412` → End User เห็น error → ชื่อเสียงเสีย → PGW อาจ suspend account ชั่วคราว

## Settlement — รอบบัญชี

PGW คำนวณยอดสุทธิในรอบบัญชี (รายวัน/สัปดาห์/เดือน ตาม SLA) แล้วโอนเงินไปบัญชีธนาคารของ Merchant

**สูตร:** ยอดโอน = Deposit รวม − Platform fee − Partner fee − chargeback/refund

**ตัวอย่างคำนวณ:**

| รายการ | จำนวน |
|---|---|
| Deposit รวม | 1,000,000 บาท |
| Platform fee (1%) | −10,000 |
| Partner fee (0.5%) | −5,000 |
| **โอนให้ Merchant** | **985,000 บาท** |

⚠️ Top-up ที่ Merchant เติมเองไม่ถือเป็น revenue — ถูก hold ไว้สำหรับ payout เท่านั้น ไม่รวมในสูตร settlement ด้านบน

**Settlement Edge Cases:**

- **Chargeback หลัง settle** — ธนาคาร force คืนเงิน → PGW หักจาก settlement รอบถัดไป
- **Dispute ยอดไม่ตรง** — ต้องใช้ Bank Adapter log + Core log + bank statement มา reconcile ด้วยมือ
- **Pending transaction ข้ามรอบ** — settle ในรอบถัดไปเมื่อ transaction complete
