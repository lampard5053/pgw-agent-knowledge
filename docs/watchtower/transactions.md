# Transactions

Breadcrumb: "{Instance} > Transactions" — มุมขวาบน: ปุ่ม download, toggle **Auto** + interval (เช่น "30s") + นับถอยหลังเป็นวินาที + ปุ่มรีเฟรชมือ

!!! danger "ยืนยันชัดเจนที่สุด — ข้อมูลชุดเดียวกับ OpsDash Transaction"
    รูปแบบ REF (เช่น `ktbbiz-17073915592953`) และ ID (MongoDB ObjectId) ตรงกับโครงสร้างที่เห็นใน [OpsDash → Transaction](../opsdash/transactions.md) ทุกประการ

## Filter ทั้งหมด

State, Type, Transaction ID, Bank Account ID, From Bank, To Bank, From Account No, To Account No, From Date, To Date + ปุ่ม Add Filter / Search

### State dropdown — enum ที่ 3 ในระบบทั้งหมด (ไม่ตรงกับ Admin Console หรือ OpsDash เป๊ะ)

**All states, Pending, Processing, Confirming, Success, Manual Success, Failed, Manual Failed**

!!! danger "เทียบ State enum ทั้ง 3 ระบบ"
    | ระบบ | State ที่มี |
    |---|---|
    | Admin Console | Created, Processing, Unmatched, Refunding, Refunded, Success, Failed, Canceled, **Manual Success**, **Manual Failed**, Expired |
    | OpsDash | Pending, Processing, Success, Failed, Cancelled, Unmatched, **Confirming**, Conflict |
    | **Watchtower** | Pending, Processing, **Confirming**, Success, **Manual Success**, Failed, **Manual Failed** |

    Watchtower ผสมทั้ง "Confirming" (จาก OpsDash) และ "Manual Success/Manual Failed" (จาก Admin Console) แต่ไม่มี Cancelled/Unmatched/Conflict/Refunding — **คาดว่า Watchtower ทำ mapping/normalize state ของตัวเองจากทั้ง 2 ระบบ** ไม่ใช่ passthrough ตรงๆ จากระบบใดระบบหนึ่ง

    ที่มาของ "Manual Success/Manual Failed" **คาดว่า**มาจากปุ่ม "Force Success"/"Cancel" ที่เห็นในหน้า [Jobs](jobs.md) — ops กดบังคับที่ระดับ Job แล้วส่งผลมาที่ state ของ Transaction ที่ผูกกัน

### Type dropdown
All types, Inbound, Outbound

### Bank dropdown (From Bank / To Bank) — รายชื่อธนาคารเต็มรูปแบบ

| รหัส | ชื่อเต็ม |
|---|---|
| KBNK | Kasikornbank |
| BBL | Bangkok Bank |
| KTB | Krung Thai Bank |
| TTB | TMBThanachart |
| SCB | Siam Commercial Bank |
| BAY | Bank of Ayudhya |
| GSB | Government Savings Bank |
| UOBT | UOB Thailand |
| GHB | Government Housing Bank |

!!! question "ค้างไว้"
    List ถูกตัดตรง GHB ในภาพที่สำรวจ — น่าจะมีธนาคารอื่นต่ออีก (เช่น BAAC, CIMB, KKP, LHBANK, TISCO ที่มักอยู่ในรายชื่อธนาคารไทยมาตรฐาน) ยังไม่ยืนยัน

## ตาราง Transactions

| คอลัมน์ | รายละเอียด |
|---|---|
| TYPE | ▼IN (เขียว) / ▲OUT (แดง) |
| REF | 2 บรรทัด: ID แบบ MongoDB ObjectId + reference string (เช่น `ktbbiz-17073915592953`) |
| FROM / TO | เลขบัญชี + รหัสธนาคาร |
| AMOUNT | จำนวนเงิน |
| STATE | ดู enum ด้านบน |
| DATE | วันเวลาที่เกิดธุรกรรม |
| ACTIONS | เมนู "..." |
