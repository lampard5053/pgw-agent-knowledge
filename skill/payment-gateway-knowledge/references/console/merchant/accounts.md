# Merchant Accounts

![รายการ Merchant Accounts](../../assets/screen-short/merchant-accounts-list.jpg "รายการ Merchant Accounts — สังเกต merchant 'aixStore3' มีหลายบัญชีธนาคาร/หลายชื่อบุคคล/นิติบุคคลผูกอยู่")

!!! question "คำถามค้าง — ความสัมพันธ์กับ Payment Pool"
    merchant เดียว (aixStore3) มีหลายบัญชี (Teerapong Saensuk/KBNK, Jaruwan/BBL, FSMP Garage Company Limited/KTB, Ratchada Entertainment group/KBNK) — ชื่อบัญชีแต่ละอันเป็นคนละนิติบุคคลกัน

    **ยังไม่ชัดเจนว่า:** นี่คือ pool บัญชีรับเงินฝากที่ merchant ใช้หมุนเวียนเอง หรือเป็นบัญชีของ sub-vendor ในเครือ merchant หรือเป็นคนละ layer จาก [Payment Channel/Pool](../payment/pools.md) โดยสิ้นเชิง — ควรถามผู้รู้ระบบยืนยัน เพราะกระทบความเข้าใจเรื่อง deposit flow โดยตรง

## ฟอร์ม Create Merchant Account

![Bank Code dropdown](../../assets/screen-short/merchant-accounts-bank-code-dropdown.jpg "Bank Code dropdown — ยืนยัน order ธนาคารตรงกับ enum ของ customer bankCode")

![ฟอร์มเต็ม](../../assets/screen-short/merchant-accounts-full-form.jpg "ฟอร์มเต็ม: Bank Code, Account Number, Account Name, Merchant")
