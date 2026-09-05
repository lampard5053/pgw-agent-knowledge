# Dashboard

หน้าแรกที่เห็นเมื่อ login เข้า Watchtower — มุมมองภาพรวมข้ามทุก instance (ถ้าเลือก "All instances" ที่ sidebar)

## แถบสถานะบนสุด

| ฟิลด์ | รายละเอียด |
|---|---|
| Connected: 9/9 | จำนวน instance ที่เชื่อมต่อสำเร็จ / ทั้งหมด |
| "2 inactive instances hidden" | จำนวน instance ที่ถูกปิดใช้งาน (ไม่นับรวมใน 9/9) |
| Toggle **Active / All** | สลับว่าจะนับเฉพาะบัญชีที่ Active หรือรวมทุกสถานะ |

!!! danger "⚠ 'N accounts need attention' — banner เตือนหลัก"
    ตัวอย่างจริงที่พบ: "33 accounts need attention" (ระดับ All instances) ดูรายละเอียดกลไกเต็มที่ [Accounts](accounts.md#health) — เป็นการนับบัญชีที่ Health = "Needs Attention"

## การ์ด Balance

| ฟิลด์ | รายละเอียด |
|---|---|
| THB Balance | ยอดรวมสกุล THB ทั้งหมด + จำนวนบัญชี + เวลาข้อมูลล่าสุด (เช่น "2m ago") |
| LAK Balance | ยอดรวมสกุล **LAK (Lao Kip)** — ปรากฏเฉพาะโหมด "All" เท่านั้น (โหมด Active ไม่โชว์ถ้าไม่มีบัญชี Active สกุลนี้) |

!!! danger "เจอสกุลเงินใหม่ — ระบบให้บริการในลาวด้วย"
    **LAK** เป็นรหัสสกุลเงินของประเทศลาว — พบว่า instance **GGPAY** เป็นรายเดียวที่มียอดทั้ง THB และ LAK พร้อมกัน บ่งชี้ว่า GGPAY ให้บริการในลาวควบคู่กับไทย ต่อยอดจากที่เคยเจอ "IDR BANK" ใน [Payment Channels](../console/payment/channels.md) ฝั่ง Admin Console — ระบบนี้ครอบคลุมอย่างน้อย 3 ประเทศ (ไทย, อินโดนีเซีย, ลาว)

## Active vs All — ตัวเลขต่างกันมาก

!!! tip "สมมติฐาน — น่าจะกรองตาม Account Status"
    ตัวอย่างจริงระดับ All instances:

    | โหมด | THB Balance | จำนวนบัญชี |
    |---|---|---|
    | Active | ฿14,373,108.28 | 197 |
    | All | ฿41,603,309.95 | 482 |

    ต่างกันเกือบ 3 เท่า — **คาดว่า** "Active" กรองเฉพาะบัญชีที่ Status = active (ดู [Accounts](accounts.md)) ส่วน "All" รวมบัญชี inactive/suspend ด้วย

## Balance by Instance

ตารางรายชื่อทุก instance พร้อมยอด balance ของตัวเอง (คลิกดูได้ทีละแบรนด์) — ตัวอย่างจริง (โหมด All):

| Instance | THB Balance |
|---|---|
| MkcPay | 359,032.47 |
| EazyPay | 10,328.00 |
| TPay | 142,135.442 |
| JoinPay | 6,514,001.77 |
| NextPay | 5,097,871.22 |
| GGPAY | 3,150,700.123 (+ LAK 62.00) |
| ManeePay | 1,589,272.01 |
| PayOk | 4,194,696.83 |
| PaymentPlus | 20,545,272.09 |

## กราฟ Trend

**"LAK Balance Trend (7 days)"** และ **"THB Balance Trend (7 days)"** — กราฟเส้น/พื้นที่แสดงยอดรวมรายวันย้อนหลัง 7 วัน
