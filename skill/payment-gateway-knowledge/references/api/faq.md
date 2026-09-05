# FAQ

คำถามที่พบบ่อยพร้อมคำตอบยืนยันจาก spec

??? question "Withdraw bank reject → เงินไปไหน?"
    เงินไม่หาย — bounce กลับ T+1 ถึง T+2 วัน → Bank Adapter แจ้ง Core → Core ส่ง **unhold webhook** คืน Merchant wallet อัตโนมัติ: `{ type: "unhold", amount: "513.50", fee: "0" }`

    Merchant refund End User เต็ม `amount` — ห้ามหัก fee ซ้ำ

    ✅ ยืนยันจาก spec v1.5.13

??? question "หมด expiredAt แต่เงินโอนเข้าช้า — หายไหม?"
    เงินไม่หาย แต่ match ไม่ได้ → กลายเป็น `type: unknown_deposit`

    แก้ไข: ส่ง uuid + สลิปให้ PGW Support → manual reconcile

    ✅ ยืนยันจาก transaction type enum ใน spec

??? question "HMAC Signature ป้องกัน replay attack ได้ไหม?"
    HMAC ป้องกัน **tampering** (แก้ไขข้อมูลระหว่างทาง) แต่**ไม่ป้องกัน replay** (ส่งซ้ำ payload เดิม)

    Merchant ต้อง implement **idempotency check** บน `transactionUuid` เอง:

    ```js
    if (db.exists(transactionUuid)) return 200;
    ```

    ✅ ยืนยันจาก spec

??? question "deposit ส่ง amount เป็น integer แต่ response ได้ทศนิยม — ทำไม?"
    Request ส่ง `amount: 1000` (integer) → ระบบ gen unique suffix → response `"amount": "1000.47"`

    ทศนิยมคือ **unique amount** ที่ระบบสร้างอัตโนมัติ ใช้ match กับยอดโอนจริงจากธนาคาร — End User ต้องโอน 1000.47 พอดี ไม่ใช่ 1000

    ✅ ยืนยันจาก spec v1.5.13

??? question "unhold คืออะไร และ fee ของ unhold ต้องจัดการยังไง?"
    `unhold` เกิดเมื่อ withdraw failed/canceled → ระบบ refund (เงินต้น + fee) คืน Merchant wallet

    webhook: `{ type: "unhold", amount: "513.50", fee: "0" }`

    **fee บน unhold = `"0"` เสมอ** เพราะ fee ถูก reverse พร้อมกันไปแล้ว — Merchant refund End User เต็ม `amount` ห้ามหัก fee ซ้ำ

    ✅ ยืนยันจาก spec + webhook semantic ที่ตรวจไขว้ใน `api-1.json`

!!! question "คำถามที่ยังไม่มีคำตอบจากส่วนนี้"
    รวมอยู่ที่ [คำถามที่ยังไม่มีคำตอบ](../open-questions.md) แล้ว — ดูหัวข้อ P2P rail และ p2p-limit endpoints ที่ยังไม่เคยเห็นตัวอย่างการใช้งานจริง
