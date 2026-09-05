# Integration Guide

ขั้นตอนการ integrate PGW ตั้งแต่ต้นจนใช้งานได้จริง

## Onboarding — ขั้นตอนสมัครใช้งานอย่างเป็นทางการ

| ขั้นตอน | รายละเอียด |
|---|---|
| 1. Contact Sales Team | ติดต่อ authorized sales representative ของ PGW Provider เพื่อเริ่มกระบวนการขอ integration |
| 2. Submit Merchant Information | ส่งข้อมูลเพื่อเปิดบัญชี Merchant: ชื่อ Merchant + merchant code, Bank settlement info (Bank Code, Account Number, Account Name), Callback API endpoint URL |
| 3. Credential Provisioning | หลังอนุมัติและตั้งค่าบัญชีสำเร็จ จะได้รับ: API Endpoints (Sandbox + Production), API Key + API Secret, Console Web Portal URL + access credentials |

!!! danger "ข้อกำหนดสำคัญ"
    ต้องสร้าง Customer account (`PUT /customer`) ก่อนเสมอ — deposit/withdraw request จะ fail ถ้า customer ยังไม่มีในระบบ

## Setup เริ่มต้น

สิ่งที่ต้องมีก่อน integrate:

1. **API Key** — ใส่ใน header ทุก request: `Authorization: Bearer <api_key>`
2. **Secret Key** — ใช้คำนวณ HMAC signature ตรวจ webhook (ห้ามส่งออก network เด็ดขาด)
3. **Callback URL** — endpoint ของ Merchant ที่รับ webhook จาก PGW
4. **Base URL** — sandbox: `https://pgway.work` | production: ตามที่ provider กำหนด

### Checklist ก่อน go-live

- [ ] ลงทะเบียน customer ทดสอบ (`PUT /customer`)
- [ ] สร้าง deposit order ใน sandbox และทดสอบรับ webhook
- [ ] ทดสอบ withdraw สำเร็จ และ failed (unhold)
- [ ] ตรวจ HMAC signature ถูกต้อง
- [ ] Implement idempotency check บน `transactionUuid`
- [ ] Top-up wallet เริ่มต้นก่อน go-live
- [ ] ตั้ง alert เมื่อ balance ต่ำกว่า threshold

## ทีละ Endpoint

=== "1. Customer"

    ### ลงทะเบียน Customer (End User)

    ก่อนสร้าง deposit/withdraw ทุกครั้ง Merchant ต้อง **upsert customer** ก่อน เพื่อให้ PGW รู้จัก End User — ถ้า `accountNo`+`bankCode` ซ้ำกับที่มีอยู่แล้วจะ update ทับ ไม่สร้างซ้ำ

    **Request** — `PUT /api/v1/customer`

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
        "accountName": "Somchai Jaidee"
      }
    }
    ```

    !!! tip "เก็บ uuid ไว้ให้ดี"
        เก็บ `data.uuid` (customerUuid) map กับ `user_id` ของ Merchant — ต้องใช้ในทุก deposit/withdraw ถัดไป | `423` = customer ถูก banned ทำ transaction ไม่ได้

=== "2. Deposit"

    ### สร้าง Deposit Order

    Merchant สร้าง order → ได้ QR กลับมา → แสดงให้ End User สแกนโอนเงิน → รอ webhook

    **Request** — `POST /api/v1/transaction/deposit/request`

    ```json
    {
      "merchantOrderId": "ORDER-001",
      "customerUuid": "c8f1a2e0-...",
      "amount": 1000
    }
    ```

    `amount` เป็น **integer เท่านั้น** (1–500,000)

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
        "expiredAt": "2026-07-29T10:10:00Z",
        "paymentChannelType": "thb_bank"
      }
    }
    ```

    !!! tip "amount ที่ตอบกลับต่างจาก request"
        Request ส่ง `amount: 1000` (integer) → Response ได้ `"amount": "1000.47"` (string + ทศนิยม) ทศนิยมคือ **unique suffix** ที่ระบบสร้างอัตโนมัติเพื่อ match กับเงินโอนจริง — End User ต้องโอนเงิน 1000.47 บาทพอดี

=== "3. Withdraw"

    ### สร้าง Withdraw Order

    Merchant สั่งโอนเงินออกจาก wallet ไปบัญชี End User — เป็น async ต้องรอ webhook

    **Request** — `POST /api/v1/transaction/withdraw/request`

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

    `412` = balance ไม่พอ | `500 p2p_request_in_process` = อย่า retry ทันที ให้ `GET` เช็ค status ก่อน

    !!! tip "หัก wallet ทันทีตอน create"
        ระบบ debit `amount + fee` จาก wallet ทันทีตอน create — ถ้า failed ระบบจะ refund คืนผ่าน `unhold` webhook อัตโนมัติ

=== "4. Webhook"

    ### รับ Webhook (Callback)

    PGW POST มาที่ `callback_url` ของ Merchant ทุกครั้งที่ transaction เปลี่ยนสถานะ

    **Payload**

    ```
    // Headers
    x-api-key: <MerchantAPIKey>
    x-signature: <HMAC>

    // Body
    {
      "transactionUuid": "t-77af-...",
      "merchantOrderId": "ORDER-001",
      "customerUuid": "c8f1a2e0-...",
      "type": "deposit",          // deposit | withdraw | unhold | unknown_deposit
      "status": "success",        // success | failed เท่านั้น
      "amount": "1000.47",
      "balance": 126431.22,
      "settleAt": "2026-07-29T10:04:33Z",
      "uuid": "t-77af-...",       // advisory — ไม่รวมใน signature
      "fee": "13.50"              // advisory — ไม่รวมใน signature
    }
    ```

    **ตรวจ Signature + Idempotency**

    ```js
    // 1. ตรวจ HMAC (6 field เรียงตามนี้)
    const raw = [
      body.transactionUuid,
      body.merchantOrderId,
      body.customerUuid,
      body.type,
      body.status,
      body.amount   // string! ห้าม parseFloat ก่อนตรวจ
    ].join("|");
    // uuid, fee ห้ามใส่ในสูตร

    // 2. Idempotency check
    if (db.exists(body.transactionUuid)) {
      return res.status(200).send(); // ignore duplicate
    }

    // 3. ตอบ 200 เสมอ (ถ้าไม่ตอบ PGW จะ retry)
    // 4. unhold → refund เต็ม amount ห้ามหัก fee ซ้ำ
    ```

    !!! danger "3 จุดที่ผิดบ่อย"
        1. ใส่ `uuid`/`fee` เข้าไปในสูตร signature → hash ไม่ตรง
        2. แปลง `amount` เป็น number ก่อนตรวจ → signature เพี้ยน (ต้องตรวจด้วย string ดิบ)
        3. `unhold` หัก fee ซ้ำ → End User ได้เงินคืนไม่ครบ (ดูรายละเอียด fee ของ unhold ที่ [API Reference → Webhook](reference.md))

    ดูรายละเอียด field ทั้งหมดและความหมายแต่ละ field ที่ [API Reference → Webhook](reference.md)

=== "5. Security"

    ### Security Best Practices

    **Webhook Security**

    - ตรวจ HMAC signature ทุก request ก่อน process
    - Implement idempotency check บน `transactionUuid` (เก็บใน DB)
    - ตอบ 200 ทันที แล้วค่อย process แบบ async เพื่อไม่ให้ timeout

    **API Key Management**

    - เก็บ API Key และ Secret Key ใน environment variable ห้าม commit ลง repo
    - Secret Key ใช้เฉพาะฝั่ง server ห้าม expose ให้ client
    - ถ้า key leak → แจ้ง PGW Support เพื่อ rotate ทันที

    !!! danger "Fake Slip Attack"
        **ห้ามเติมเครดิต End User จากสลิปภาพถ่ายเพียงอย่างเดียว** — ถ้า match อัตโนมัติไม่สำเร็จและ End User ส่งสลิปมา ให้ใช้ `POST /verify-slip` แทน สลิปจริงมี QR TLV ที่ปลอมแปลงไม่ได้ ระบบจะ cross-check กับ Bank Adapter ให้ | `409 slip_already_used` ป้องกัน replay สลิปเดิม

        ดูตัวอย่างเคสจริงของฝั่ง Support ที่เจอปัญหาแนวนี้ (สลิปกับยอดเงินไม่ตรงกัน) ที่ [ทีม Support — เคสที่พบบ่อย](../support/common-cases.md)
