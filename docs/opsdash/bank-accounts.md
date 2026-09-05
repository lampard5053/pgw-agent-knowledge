# Bank Account

Subtitle: "Manage and monitor bank account settings" — Card: "Bank Account List" / "View all bank accounts and their current status"

!!! tip "น่าจะเป็นบัญชีเดียวกับ Payment Channel ฝั่ง Admin Console"
    รูปแบบชื่อบัญชีที่พบ (มี prefix แบรนด์ปนกัน เช่น `EasyPay-ktbbiz-...`, `JoinPay-ktbbiz-...`) และการผสม Personal/Juristic ในชุดเดียวกัน ตรงกับลักษณะที่เคยเห็นใน [Payment Channels](../console/payment/channels.md) ฝั่ง Admin Console มาก **คาดว่า**นี่คือบัญชีธนาคารจริงชุดเดียวกัน มองจากคนละมุม (OpsDash = มุมเชื่อมต่อ/บอท, Admin Console = มุม routing/fee) ยังไม่มีหลักฐานยืนยัน 100%

## Filter ทั้งหมด

**แถวบน:** ID (search), Account Number (search), Bank Account Name (search), Client Code (search)
**แถวล่าง:** Kind (dropdown), Status (dropdown), Lock Flag (dropdown), ปุ่ม Search, ปุ่ม X (clear)

### Kind dropdown — enum เต็ม

**All Kinds, KBIZ, KTBBIZ, SCBBIZ, GSB, KPLUS, SCB, BBL, PGWM**

!!! danger "ไขปริศนา 'PGWM' จากเอกสาร OpenAPI เดิม"
    เอกสาร API เดิม (ฝั่ง Merchant-facing) เข้าใจว่ามีบัญชีรับเงินบัญชีเดียวชื่อ "PGWM" ที่ทุกอย่างไหลผ่าน — จากหลักฐานนี้ **PGWM เป็นแค่ 1 ใน 8 "Kind" ของบัญชี** เท่านั้น (คาดว่าเป็น kind เริ่มต้น/ทดสอบ หรือ virtual account ภายใน ไม่ใช่บัญชีจริงเดียวที่ยืนหนึ่ง) — ตรงกับที่ [Payment Channels](../console/payment/channels.md) ยืนยันไว้แล้วว่ามีบัญชีจริงหลายสิบบัญชี ไม่ใช่บัญชีเดียว

    **อัปเดตหลังสำรวจ [PGWay](../pgway/index.md) — แบ่งกลุ่มได้ชัดขึ้นมาก:**

    - กลุ่มที่มี **REST API ทางการของธนาคาร ไม่ต้องพึ่งอุปกรณ์จริงเลย**: **KTBBIZ** (ยืนยันแล้ว — raw data ตรง schema ของ `biznext-channel`, ไม่มี Job ID ผูกเลย)
    - กลุ่มที่ใช้ **Bot/Appium ควบคุมมือถือจริง** (มี Job ID + raw log): **KBIZ**, **KPLUS** (ยืนยันแล้วจาก [PGWay → Phone Status](../pgway/phone-status.md) — PGWay เรียก Kind นี้ว่า "KBANK", แอพที่ใช้คือ **K PLUS** ตรงกับ bank code `KBNK` ที่เห็นในคอลัมน์ From/To ทุกที่ — เป็นชื่อเดียวกัน 3 ระบบเรียกต่างกัน), **GSB** (แอพ **MyMo**, ยืนยันจาก PGWay เช่นกัน)
    - ที่ยังไม่มีหลักฐานยืนยันฝั่งไหนเลย: **SCBBIZ, SCB (ส่วนบุคคล), BBL, PGWM** — ไม่เคยเห็นทั้ง raw data แบบ REST API และไม่เคยเห็นใน [PGWay → Agent Devices](../pgway/agent-devices.md) ที่ใช้งานจริง (แม้ตัวเลือก enroll จะมี BBL ให้เลือกก็ตาม)

### Status dropdown
All Status, Active, Inactive

### Lock Flag dropdown
**All Lock Flags, Normal (0), Lock Inbound (1), Lock Outbound (2), Lock Both (3)**

!!! tip "Lock Flag ละเอียดกว่า Status ธรรมดา"
    ล็อกแยกทิศทางเงินเข้า/ออกได้อิสระ (bitmask-like: 0=ปกติ, 1=ล็อกขาเข้า, 2=ล็อกขาออก, 3=ล็อกทั้งคู่) — คนละกลไกกับ Status Active/Inactive ธรรมดา และคนละมิติกับ Channel Status (Active/Hide/Limit/Inactive) ที่เคยเห็นใน Admin Console

## ตาราง Bank Account List

| คอลัมน์ | รายละเอียด |
|---|---|
| Name | ชื่ออ้างอิงบัญชี — พบรูปแบบหลากหลาย เช่น `S01-SCB-1564341871-JP`, `601-KTBbiz-NattharatTransport-0170762149`, `JoinPay-ktbbiz-Chanrawee-4883201120-T126` |
| Account Number | เลขบัญชีธนาคาร |
| Bank Account Name | ชื่อเจ้าของบัญชี (พบทั้งชื่อบุคคลและนิติบุคคล ปนกัน) |
| Kind | ดู enum ด้านบน |
| Status | Active / Inactive |
| Flag | ค่า Lock Flag ปัจจุบัน (พบ "Normal" ทุกแถวในตัวอย่าง) |
| Client Code | ทุกแถวที่พบเป็น **`core`** |
| Tenant | ทุกแถวที่พบเป็น **`default`** |
| Action | ปุ่ม **Transfer**, ไอคอนดูรายละเอียด, ⚙️ แก้ไข |

## ฟอร์ม Add New Bank Account

| ฟิลด์ | รายละเอียด |
|---|---|
| Name | ชื่ออ้างอิง |
| Client Code | ผูกกับ client ไหน |
| Tenant | default = "default" |
| Status | default = **Inactive** (ต้องเปิดเองภายหลัง เป็นค่าตั้งต้นเชิง safety) |
| Kind | dropdown (ดู enum ด้านบน) |
| Account Number | เลขบัญชี |
| Account Name | ชื่อบัญชี |
| **Phone Number** | เบอร์โทรของมือถือที่ผูกกับบัญชีนี้ |

!!! danger "field 'Phone Number' คือหลักฐานยืนยันกลไก 'มือถือเชื่อมแอพธนาคาร'"
    ทุกบัญชีธนาคารผูกกับ**เบอร์โทรศัพท์ 1 เบอร์** ตรงกับที่อธิบายไว้ว่าระบบรับข้อมูลจากมือถือจริงที่ล็อกอินแอพธนาคารค้างไว้

## ฟอร์ม Edit Bank Account

### ส่วนพื้นฐาน (ทุก Kind เหมือนกัน)
Name, Client Code, Tenant, Status, **Lock Flag**, Kind, Account Number, Account Name, Phone Number
ปุ่มท้ายฟอร์ม: **Set Limit** (แยกจาก Save — น่าจะเปิด modal ตั้งวงเงินแยกต่างหาก) และ **Save**

### ส่วน "{Kind} Specific Fields" — ค่า config เฉพาะของแต่ละธนาคาร

!!! danger "การค้นพบสำคัญที่สุดของทั้งหมด — ยืนยันกลไก Bot ด้วย Appium"
    ตัวอย่างที่พบจริงสำหรับบัญชี Kind = **SCB** แสดงหัวข้อ **"SCB Specific Fields"**:

    | ฟิลด์ | ตัวอย่างค่าจริง | ความหมาย |
    |---|---|---|
    | Appium Bot URL | `https://station-two.pgway.work` | URL ของ Appium server ("station") ที่ควบคุมมือถือเครื่องนี้ |
    | GSB Server Mobile Token (Optional) | (blank) | token สำหรับ GSB โดยเฉพาะ — ปรากฏแม้อยู่ใน SCB section (อาจเป็น field ที่ share ข้ามกลุ่ม kind บางส่วน) |
    | Device ID (Optional) | `scb-0722968657` | รหัสอ้างอิงอุปกรณ์/มือถือเครื่องที่รันบัญชีนี้ |
    | PIN (Optional) | (blank) | รหัส PIN ปลดล็อกมือถือ/แอพ สำหรับให้ bot ใช้ |

    **"Appium"** คือ framework automation มือถือที่มีชื่อเสียงมาก (ควบคุม UI แอพ Android/iOS จากภายนอกเหมือนคนกดจริง) — ฟิลด์นี้ยืนยัน 100% ว่า **แต่ละบัญชีธนาคาร (อย่างน้อยกลุ่มที่ไม่มี business API) ผูกกับมือถือจริง 1 เครื่อง ที่ล็อกอินแอพธนาคารทิ้งไว้ แล้วมี Bot ควบคุมผ่าน Appium server (เรียกว่า "station") เพื่ออ่านยอด/ทำรายการแทนคน**

    มี "station" มากกว่า 1 ตัว (พบ `station-two` ในตัวอย่าง ⇒ น่าจะมี station-one/station-three ฯลฯ กระจาย load การควบคุมบอท) — ส่วนฟิลด์เฉพาะของ Kind อื่น (KBIZ, GSB, KPLUS, BBL) ยังไม่เคยเห็นตัวอย่างจริง คาดว่ามีชุด field ต่างกันไปตาม UI ของแอพแต่ละธนาคาร

    ดูกลไกการทำงานจริงของบอทต่อได้ที่ [Bot](bots.md) และ [Job](jobs.md)
