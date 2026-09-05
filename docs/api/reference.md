# API Reference

อ้างอิงจาก OpenAPI spec `api-1.json` version **v1.5.13** — ทุก response ใช้ wrapper รูปแบบเดียวกัน: `{"ok": true, "data": {...}}`

=== "Customer"

    ### `PUT /api/v1/customer`

    Upsert End User — ถ้า `accountNo`+`bankCode` ซ้ำจะ update ทับ ไม่สร้างซ้ำ

    **Request**

    ```json
    {
      "accountName": "Somchai Jaidee",
      "accountNo": "1234567890",
      "bankCode": "KBNK"
    }
    ```

    **Response 200**

    ```json
    {
      "ok": true,
      "data": {
        "uuid": "c8f1a2e0-...",
        "status": "active",
        "bankCode": "KBNK",
        "accountNo": "1234567890",
        "accountName": "Somchai Jaidee",
        "merchantUuid": "m-9911-...",
        "createdAt": "2026-07-29T10:00:00Z"
      }
    }
    ```

    Error: `422` validation error, `423` customer banned

    `status` มี 3 ค่า: `active` / `inactive` / `banned`

    `data.uuid` = customerUuid ใช้ใน deposit/withdraw ทุกครั้ง — เก็บ map ไว้กับ user_id ของ Merchant

=== "Balance & Limit"

    ### `GET /api/v1/profile/balance`

    เช็ค wallet balance ปัจจุบัน — ต้อง query ทุกครั้ง ห้าม hardcode

    ```json
    {
      "ok": true,
      "data": { "balance": 125430.75 }
    }
    ```

    ### `GET /api/v1/transaction/deposit/limit` และ `GET /api/v1/transaction/withdraw/limit`

    ```json
    {
      "ok": true,
      "data": {
        "minimumDeposit": 1,
        "maximumDeposit": 500000,
        "depositRanges": [
          { "min": 0, "max": 10000000 }
        ]
      }
    }
    ```

    `withdraw/limit` ตอบ field ชุดเดียวกันแต่เปลี่ยนชื่อเป็น `minimumWithdraw`/`maximumWithdraw`/`withdrawRanges`

    !!! note "มี p2p-limit เพิ่มเติมใน spec ที่ยังไม่เคยเห็นตัวอย่าง response จริง"
        Spec มี endpoint คู่ขนาน `GET /transaction/deposit/p2p-limit` และ `GET /transaction/withdraw/p2p-limit` สำหรับ rail แบบ P2P โดยเฉพาะ — endpoint นี้ไม่ได้อยู่ใน `pgw_service.html` ต้นฉบับ และยังไม่เคยเห็นตัวอย่างการใช้งานจริง ถือเป็นคำถามค้าง (ดู [คำถามที่ยังไม่มีคำตอบ](../open-questions.md))

=== "Deposit"

    ### `POST /api/v1/transaction/deposit/request`

    สร้าง deposit order — response **201**, `amount` ใน request ต้องเป็น **integer** เท่านั้น (1–500,000 ห้ามมีทศนิยม)

    **Request**

    ```json
    {
      "merchantOrderId": "ORDER-001",
      "customerUuid": "c8f1a2e0-...",
      "amount": 1000
    }
    ```

    **Response 201**

    ```json
    {
      "ok": true,
      "data": {
        "uuid": "t-77af-...",
        "qrCode": "00020101...",
        "amount": "1000.47",
        "accountBankCode": "PGWM",
        "accountNo": "0115935562",
        "balance": 125430.75,
        "currency": "thb",
        "expiredAt": "2026-07-29T10:10:00Z",
        "paymentChannelType": "thb_bank"
      }
    }
    ```

    ช่อง p2p จะได้ `checkoutUrl` แทน `qrCode`

    Error: `404`, `409`, `412`, `422`, `423`

    !!! tip "amount ทศนิยม = unique matching key"
        End User ต้องโอน **1000.47 บาทพอดี** (ตาม unique amount ที่ระบบ gen) — ถ้าโอนไม่ตรง ระบบ match ไม่ได้ กลายเป็น `unknown_deposit` (รายละเอียดเต็มดูที่ [ทีม Support → Transaction Status Reference](../support/status-reference.md))

=== "Withdraw"

    ### `POST /api/v1/transaction/withdraw/request`

    โอนเงินออกจาก Merchant wallet ไปบัญชี End User — response **201**, เป็น async

    **Request**

    ```json
    {
      "merchantOrderId": "WD-001",
      "customerUuid": "c8f1a2e0-...",
      "amount": 500.50
    }
    ```

    `amount` รับทศนิยม ≤2 ตำแหน่ง (1–500,000)

    **Response 201**

    ```json
    {
      "ok": true,
      "data": {
        "uuid": "t-88c2-...",
        "status": "created",
        "balance": 124929.50,
        "paymentChannelType": "thb_bank"
      }
    }
    ```

    Error: `404`, `412` (balance ไม่พอ), `422`, `423`, `500` (`p2p_request_in_process` → `GET` เช็ค status ก่อน retry)

=== "Verify Slip"

    ### `POST /api/v1/transaction/verify-slip`

    ตรวจสลิป QR (TLV) กรณี match อัตโนมัติไม่สำเร็จ — ทำได้หลังสร้าง order ≥300 วินาทีเท่านั้น

    **Request**

    ```json
    {
      "uuid": "t-77af-...",
      "qrcode": "0038000600000101..."
    }
    ```

    `qrcode` = raw TLV จาก QR บนสลิปธนาคาร (ไม่ใช่รูปภาพสลิป)

    **Response 200**

    ```json
    {
      "ok": true,
      "data": {
        "uuid": "t-77af-...",
        "amount": "1000.47",
        "senderAccountBankCode": "KBNK",
        "senderAccountNo": "1234567890",
        "settleAt": "2026-07-29T10:04:33Z"
      }
    }
    ```

    Error: `404`, `409` (`slip_already_used`), `412` (`slip_verify_too_early` — ยังไม่ถึง 300s), `422`, `423`, `503` (`slip_verifier_error`)

=== "Transaction"

    ### `GET /api/v1/transaction/{uuid}`

    ดึงสถานะ transaction เต็มรูปแบบ

    ```json
    {
      "ok": true,
      "data": {
        "uuid": "t-77af-...",
        "merchantOrderId": "ORDER-001",
        "customerUuid": "c8f1a2e0-...",
        "type": "deposit",
        "status": "success",
        "amount": 1000,
        "settleAmount": 1000.47,
        "fee": 13.5,
        "senderAccountBankCode": "KBNK",
        "senderAccountNo": "1234567890",
        "receiverAccountBankCode": "PGWM",
        "receiverAccountNo": "0115935562",
        "currency": "thb",
        "createdAt": "2026-07-29T10:00:00Z"
      }
    }
    ```

    `type` enum: `deposit` / `withdraw` / `unknown_deposit`

    `status` enum: `created` / `processing` / `success` / `failed` / `canceled` / `unhold`

    ### `POST /api/v1/transaction/{uuid}/cancel`

    ยกเลิก deposit ที่ยัง `created`/pending — POST body ว่าง

    ```json
    {
      "ok": true,
      "data": {
        "uuid": "t-77af-...",
        "status": "canceled",
        "type": "deposit"
      }
    }
    ```

    `412` = state ที่ cancel ไม่ได้แล้ว

=== "Webhook"

    ### Callback ไปยัง `callback_url` ของ Merchant

    ยิงทุกครั้งที่มี ledger change สำเร็จ (settle) — Merchant ต้องตอบ `200` เสมอ ไม่งั้น PGW จะ retry

    **Headers**

    | Header | ความหมาย |
    |---|---|
    | `Content-Type` | `application/json` |
    | `x-signature` | `HMAC-SHA256(secretKey, "transactionUuid\|merchantOrderId\|customerUuid\|type\|status\|amount")` — ตรวจด้วย `amount` แบบ string ห้าม parse เป็นตัวเลขก่อน |
    | `x-api-key` | API key ของ Merchant คนเดียวกับที่ใช้ยิง request เข้า PGW — ส่งกลับมาด้วยเพื่อให้ Merchant ยืนยันว่า callback นี้เป็นของบัญชีตัวเอง |

    **Body**

    | Field | ความหมาย |
    |---|---|
    | `transactionUuid` | reference ของ PGW ฝั่ง merchant-ledger สำหรับ entry ที่เพิ่ง settle — เพิ่งถูกสร้างตอน settle นี้เอง Merchant จะไม่เคยเห็นค่านี้มาก่อน callback นี้ |
    | `uuid` | **advisory**, ไม่รวมใน signature — คือ uuid ที่ตอบกลับตอนสร้าง request (มุมมองของ Merchant) ใช้ correlate callback นี้กับ request ต้นทาง |
    | `merchantOrderId` | order id ของ Merchant เอง — อาจว่างถ้าไม่มีการสร้าง order ฝั่ง Merchant มาก่อน (เช่นกรณี `unknown_deposit`) |
    | `customerUuid` | customer ที่ลงทะเบียนไว้ — ใช้ระบุเจ้าของ event เมื่อ `merchantOrderId` ว่าง |
    | `type` | `deposit` / `withdraw` / `unhold` / `unknown_deposit` |
    | `status` | **`success` หรือ `failed` เท่านั้น** (ไม่มี `created`/`processing` ใน callback) |
    | `amount` | ส่งเป็น **string** เสมอ — ตรวจ signature ด้วย string ดิบ ห้าม parseFloat ก่อน |
    | `balance` | ยอด balance ของ Merchant หลังการเปลี่ยนแปลงนี้ |
    | `settleAt` | เวลาที่ธนาคารทำรายการจริง |
    | `fee` | **advisory**, ไม่รวมใน signature — ค่าธรรมเนียมที่ PGW หักไว้สำหรับ event นี้ |

    **ความหมายของ `amount`/`fee` แยกตาม `type`**

    | type | `amount` คือ | ผลต่อ `balance` |
    |---|---|---|
    | `deposit` / `unknown_deposit` | ยอด gross ที่ฝากเข้ามา | หัก fee ออกจากยอดฝาก → เครดิตสุทธิ = `amount - fee` |
    | `withdraw` | เงินต้นที่ถอน | หัก `amount + fee` ออกจาก balance (หักตั้งแต่ตอนรับ request) |
    | `unhold` | **ยอดที่ reserve ไว้ทั้งหมดคืนกลับ** = `withdraw` เดิม + fee เดิม | คืนเข้า balance เต็มจำนวน (`fee` บน entry นี้ = `"0"` เสมอ) |

    !!! danger "unhold ต้อง refund เต็มจำนวน ห้ามหัก fee ซ้ำ"
        `amount` บน `unhold` callback คือยอด reserve ทั้งหมด (เงินต้น withdraw + fee ที่เคยหักไปพร้อมกัน) — ตอน refund คืน End User ต้องคืนเต็มจำนวนนี้ ห้ามหัก fee ซ้ำอีกครั้ง เพราะ fee ถูก reverse กลับมาพร้อมกันแล้ว

    !!! note "P2P rail มี semantic เพิ่มเติมที่ยังไม่ได้ลงรายละเอียดในเอกสารชุดนี้"
        Spec มีส่วน P2P rail (`paymentChannelType: "p2p"`) ที่ callback มีพฤติกรรมต่างออกไป — `amount` อาจน้อยกว่าที่ขอไว้จริง (ใช้ค่านี้เป็นหลัก), ให้ correlate ด้วย `uuid` แทน, และ callback หลังอาจ supersede callback ก่อนหน้า (เอาอันล่าสุดเป็นหลัก) — เนื้อหาส่วนนี้ยังไม่มีใน `pgw_service.html` ต้นฉบับและยังไม่เคยเห็นตัวอย่างการใช้งานจริง เพิ่มเป็นคำถามค้างไว้ที่ [คำถามที่ยังไม่มีคำตอบ](../open-questions.md)

## Bank Codes

มี 2 กลุ่ม: กลุ่มที่ใช้ลงทะเบียน customer ได้ (`bankCode` enum ของ `PUT /customer`) กับกลุ่มที่ปรากฏเฉพาะใน field `senderAccountBankCode`/`receiverAccountBankCode` ของ transaction (ไม่ใช้ลงทะเบียน customer)

=== "✅ ลงทะเบียน Customer ได้ (20 รายการ)"

    `KBNK` Kasikorn Bank ・ `BBL` Bangkok Bank ・ `KTB` Krungthai Bank ・ `TTB` TMB Thanachart Bank ・ `SCB` Siam Commercial Bank ・ `BAY` Bank of Ayudhya (Krungsri) ・ `GSB` Government Savings Bank ・ `UOBT` United Overseas Bank ・ `GHB` Government Housing Bank ・ `BAAC` Bank for Agriculture and Agricultural Cooperatives ・ `TBNK` (ไม่มีชื่อเต็มระบุใน spec) ・ `ISBT` Islamic Bank of Thailand ・ `TSCO` Tesco Lotus ・ `KKB` Kiatnakin Phatra Bank ・ `TCRB` Thai Credit Retail Bank ・ `LHBA` Land and Houses Bank ・ `BOA` Bank of America ・ `SME` SME Development Bank ・ `CIMB` CIMB Bank ・ `PGWM` Payment Gateway Mock Bank (sandbox)

=== "ℹ️ Transaction-only (เพิ่มอีก 4 รายการ)"

    ปรากฏเฉพาะตอนแสดงผล sender/receiver ของ transaction ที่เกิดขึ้นจริง (เช่น เงินโอนเข้ามาจากธนาคารที่ไม่อยู่ใน 20 รายการข้างต้น) — **ห้าม**ใช้เป็นค่าตอนลงทะเบียน customer

    `UNK` Unknown Bank — ธนาคารต้นทาง/ปลายทางที่ระบบไม่รู้จัก ・ `BNPP` BNP Paribas ・ `EXIM` Export-Import Bank of Thailand ・ `ICBCT` ICBC (Thai)

!!! danger "ยืนยันจาก schema จริง — เอกสาร prose ใน spec เองขัดกับ schema enum ของตัวมันเอง"
    ตาราง "Supported Banks" ที่เขียนเป็น prose อยู่ใน `info.description` ของ `api-1.json` ระบุ 20 รายการโดย**ไม่มี** `TBNK` แต่**มี** `UNK` รวมอยู่ด้วย — แต่พอตรวจ schema จริงของ `bankCode` enum บน `PUT /customer` (แหล่งความจริงที่ authoritative กว่า เพราะเป็นตัว validate จริง) กลับพบว่า **มี `TBNK` และ `UNK` ไม่ได้อยู่ในกลุ่มลงทะเบียนได้** — ตรงกับที่ `pgw_service.html` เขียนไว้เป๊ะ ระบบเลยยึดตามการแบ่งกลุ่มด้านบน (ตาม schema จริง) ไม่ใช่ตาม prose

    พูดง่ายๆ คือ **prose ในตัว spec เองก็มีจุดที่ไม่ sync กับ schema ของตัวเอง** — ต้องอ้างอิง schema enum เป็นหลักเวลามีข้อขัดแย้งแบบนี้เกิดขึ้นอีก
