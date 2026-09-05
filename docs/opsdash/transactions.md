# Transaction

Subtitle: "View and manage bank transactions" — Card: "Transaction List" / "All transactions in the system"

Auto Reload: ▷5s ▷15s ▷30s ▷60s (pattern เดิมจากหน้า [Bot](bots.md)/[Job](jobs.md))

## Filter ทั้งหมด

**แถว 1:** Type (All Types), State (All States), Transaction ID (search), Bank Account ID (search)
**แถว 2:** From Account No (search), To Account No (search), Job ID (search), Client Transaction Ref (search)
**แถว 3:** Date-Time Range (From Date & Time / To Date & Time)

ปุ่ม Search / X (clear), "Filters: No filters applied", Add Filter

### State dropdown — enum เต็ม

**All States, Pending, Processing, Success, Failed, Cancelled, Unmatched, Confirming, Conflict**

!!! danger "เทียบกับ Transaction Status ฝั่ง Admin Console — คนละ state machine"
    ฝั่ง [Admin Console → Transactions](../console/payment/transactions.md) มี: Created, Processing, Unmatched, Refunding, Refunded, Success, Failed, Canceled, Manual Success, Manual Failed, Expired

    ฝั่ง OpsDash มี **Pending, Confirming, Conflict** ที่ไม่เจอฝั่ง Console เลย และไม่มี Refunding/Manual Success/Expired ฝั่งนี้ — ยืนยันว่า**นี่คือ state machine คนละชั้นกัน**: OpsDash = raw transaction ระดับธนาคาร (ก่อนแปลง), Admin Console = state ที่ผ่านการ map/ประมวลผลแล้วโดย PGW Core

## ตาราง Transaction List

| คอลัมน์ | รายละเอียด |
|---|---|
| Reference | รูปแบบ `{kind}-{ref}` เช่น `ktbbiz-019fad23-7...` |
| Client | client ที่ธุรกรรมนี้อยู่ใต้ — ทุกแถวที่พบเป็น `core` |
| **Job ID** | ลิงก์ไปยัง [Job](jobs.md) ถ้ามี — **เป็น "-" (ว่าง) ทุกแถวของ Kind = KTBBIZ ในตัวอย่างที่สำรวจ** เพราะไม่ต้องพึ่งบอท |
| Client Tx Ref | reference ของฝั่ง client (มักว่างเป็น "-") |
| Tenant | ทุกแถวที่พบเป็น `default` |
| Type | **Inbound** / **Outbound** |
| State | ดู enum ด้านบน |
| From / To | รูปแบบ `{BankCode}:{เลขบัญชี}` เช่น `KTB:0170754480`, `KBNK:2342770749` |
| Amount | จำนวนเงิน |
| Date | เวลาที่เกิดธุรกรรม |
| Actions | 👁 ดู / 🔧 จัดการ (เมนู action ดูด้านล่าง) |

!!! tip "หลักฐานยืนยันความต่าง KBIZ vs KTBBIZ (ต่อจาก Job.md)"
    ทุกแถวของธุรกรรม Kind KTBBIZ ที่พบมี **Job ID = "-" ว่างเปล่าหมด** — สนับสนุนข้อสรุปว่า KTBBIZ ใช้ REST API ทางการโดยตรง ไม่ต้องรันผ่านบอท/Job เหมือน KBIZ

## เมนู Action (ปุ่ม 🔧)

**Resend Webhook, Cancel, Force Cancel, Manual Success**

!!! danger "เปรียบเทียบกับเมนู Transaction Operations ฝั่ง Admin Console"
    ฝั่ง [Admin Console](../console/payment/transactions.md) มี: Match, Match Topup, Refund, Manual Success, Cancel — ฝั่ง OpsDash **ไม่มี "Match"** เลย เพราะระดับนี้ยังไม่ต้องรู้เรื่อง Merchant/matching — แค่ยืนยัน/ยกเลิกธุรกรรมฝั่งธนาคารเท่านั้น ส่วน "Match" ไปเกิดที่ชั้น PGW Core/Console ต่างหาก

    "**Resend Webhook**" ยืนยันว่า OpsDash เองก็ยิง webhook ออกไปหาระบบอื่น (คาดว่าคือ client "core" → ปลายทาง `bank-adapter.joinpay.app/api/v1/receiver/callback` ที่เห็นในหน้า [Client](clients.md)) — บาง action ในเมนูนี้อาจถูก disable (เทา) ขึ้นกับ state ปัจจุบันของธุรกรรม

## Transaction Details modal

Title: "Transaction Details" — "View details for transaction {MongoDB ObjectId}"

**Basic Information:** Transaction ID, Client Code, Tenant, Type (Inbound/Outbound), State, Amount, Transaction Date
**Sender Information:** Bank Code, Account Number, Account Name, Account Name (Thai)
**Recipient Information:** Bank Code, Account Number, Account Name, Account Name (Thai)
**Transaction References:** Reference Number, Transfer Order Ref, Client Transaction Ref, Job ID, **Bank Account ID** (ลิงก์สีฟ้า เชื่อมกลับไปที่ [Bank Account](bank-accounts.md))
**Additional Information:** Memo
**Raw Data:** JSON ดิบเต็มรูปแบบ พร้อมปุ่ม **Copy Raw Data**

### ตัวอย่างจริง — Raw Data ฝั่ง Outbound (Kind: KTBBIZ)

```json
{
  "financialInsAcctNo": "XXX-X-XX831-5",
  "financialInsName": "ไทยพาณิชย์",
  "transferItemId": "5ce73884-5d95-4f4b-8721-e842c8bd9953",
  "transferOrderId": "8bb1baba-448b-4c04-9753-67c0d70891c3",
  "instructionRefNo": "20260729005000054736",
  "status": "ส่งเรียบร้อย",
  "subService": { "value": "TRANSFER_OTHER_BANK", "label": "โอนต่างธนาคารมีผลทันที" },
  "beneType": "INTERBANK",
  "fromAccountNo": "XXX-X-XX432-4",
  "newPayeeAccountNo": "2322758315",
  "newPayeeNameTh": "บริษัท แคลิฟอร์เนีย 46 จำกัด",
  "newPayeeBankCode": "014",
  "amount": 12000,
  "transferFee": 5,
  "feeChargeTo": "ผู้ชำระเงิน",
  "feeChargeToEnum": "PAYER",
  "isWithholdingTaxEnabled": false,
  "inputPlatform": "MOBILE",
  "payerAvailableBalance": 273266.61,
  "payeeBankLogoUrl": "https://business.krungthai.com/ktb/rest/biznext-channel/static/img/bank/siam-commercial-bank-logo.png",
  "isMasterAccount": false,
  "masterAccountNo": "XXX-X-XX432-4",
  "accountLevel": "MA"
}
```

!!! danger "ยืนยันแล้ว — KTBBIZ มี REST API ทางการจริง + ตอบคำถาม Master Account ที่ค้างมานาน"
    URL `payeeBankLogoUrl` ชี้ไปที่ **`business.krungthai.com/ktb/rest/biznext-channel`** — คือ endpoint API ทางการของ **KTB Business (Biznext Channel)** จริง ฟิลด์ทั้งหมด (subService, beneType, feeChargeTo, withholding tax, invoices ฯลฯ) ตรงกับ schema ของ REST API แท้ๆ ไม่ใช่ผลจากการ scrape หน้าจอ

    `masterAccountNo` / `accountLevel: "MA"` / `isMasterAccount: false` — ยืนยันว่าโครงสร้าง sub-account ภายใต้ Master Account ของ KTB Biz **มีอยู่จริงในระดับ API ของธนาคาร** ตรงกับหลักฐานเดิมใน [Deep-dive: Match Type](../console/match-type.md) เป๊ะ — แต่ยังไม่ชัดว่ากระทบการรวม Payment Pool ฝั่ง Admin Console โดยตรงหรือไม่ (ยังเป็นคำถามค้าง)

    `payerAvailableBalance: 273266.61` — ยืนยัน field เดิมที่เคยเจอใน match-type.md ซ้ำอีกครั้ง (มี field นี้ให้ใช้ทุก call จริง)

### ตัวอย่างจริง — Raw Data ฝั่ง Inbound/Deposit (Kind: KTBBIZ)

```json
{
  "transactionIndex": "17065949625462",
  "transactionRefId": "17065949625462",
  "transactionDateTime": "2026-07-29T16:11:51+07:00",
  "transactionCode": "IORSDT",
  "descriptionTransactionInfo": "รายการ ORFT via KTB Online โดยทำรายการโอนเงินจาก Web ของต่างธนาคาร เข้าบัญชี Saving ของกรุงไทย",
  "descriptionName": "โอนเงินเข้า",
  "transactionComment": "014-9182911553",
  "withdraw": null,
  "deposit": 4000,
  "ledgerBalance": 286100.26,
  "currency": "THB",
  "transactionType": "Deposit",
  "paymentRef": null
}
```

!!! danger "พิสูจน์สมมติฐาน Match Type 'Balance' ด้วยหลักฐานสดใหม่ — ตรงกับ match-type.md ทุกจุด"
    - `deposit: 4000` เป็นจำนวนเต็ม **ไม่มีทศนิยม** — ตรงกับ pattern ที่เคยพบ (field `deposit` ของ KTB ไม่รายงานทศนิยม)
    - `ledgerBalance: 286100.26` มีทศนิยมครบ — ยืนยันกลไก "เอา ledgerBalance ก่อน-หลังมาลบกันเพื่อกู้ทศนิยมที่แท้จริง" ตามที่วิเคราะห์ไว้ใน [Deep-dive: Match Type](../console/match-type.md)
    - `transactionComment: "014-9182911553"` — รูปแบบ `[bank code]-[เลขอ้างอิง]` ไม่ใช่เลขบัญชีผู้โอนที่อ่านได้ตรงๆ ตรงกับที่เคยสรุปไว้ว่า "ไม่มี field ระบุบัญชีผู้โอนเลย มีแค่ transactionComment ที่ดูเหมือนโค้ดอ้างอิง"
    - `transactionCode: "IORSDT"` และ `descriptionTransactionInfo` เป็นข้อความอธิบายภาษาไทยจากธนาคารเอง — ฟิลด์ใหม่ที่ไม่เคยเห็นมาก่อน อาจมีประโยชน์สำหรับจำแนกประเภทธุรกรรมย่อยเพิ่มเติมในอนาคต
