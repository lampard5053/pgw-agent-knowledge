# OpsDash — ภาพรวม

**"Bank Adapter OpsDash"** คือระบบปฏิบัติการ (ops tool) แยกโปรดักส์ต่างหากจาก [Admin Console](../console/index.md) — เป็นชั้นที่ทำหน้าที่ **เชื่อมต่อกับบัญชีธนาคารจริง** (ผ่าน REST API ทางการของธนาคารบางเจ้า และผ่าน Bot ควบคุมมือถือจริงสำหรับธนาคารเจ้าอื่น) แล้วส่งข้อมูลธุรกรรมที่ตรวจพบเข้าไปให้ระบบ PGW Core ประมวลผลต่อ

!!! danger "หมายเหตุความแม่นยำของเอกสารส่วนนี้"
    เช่นเดียวกับส่วน Admin Console เอกสารนี้เขียนจากการสังเกตหน้าจอ UI จริงเท่านั้น ไม่มี spec/source code ยืนยันเบื้องหลังทุกจุด จุดที่เป็นการวิเคราะห์/อนุมานจะมีกล่อง **❓ คำถามค้าง** กำกับไว้เสมอ ดูสรุปคำถามค้างทั้งหมดที่ [คำถามที่ยังไม่มีคำตอบ](../open-questions.md)

## OpsDash คืออะไร (ทวนสั้นๆ)

ชื่อโปรดักส์เต็ม: **"Bank Adapter OpsDash"** — เข้าถึงผ่านเว็บแอปแยกโดเมนต่างหาก (พบจริงที่ `opsdash.pgway.work`) ไม่ได้ฝังอยู่ใน Admin Console เลย เข้าใจง่ายๆ คือ:

- **Admin Console** = หน้าจอที่ทีม Operation ของ PGW Provider ใช้บริหาร Merchant/Seller/Payment Channel/Transaction ระดับธุรกิจ
- **OpsDash** = หน้าจอที่ทีมเทคนิค/infra ใช้บริหาร **การเชื่อมต่อกับธนาคารจริง** ระดับล่างสุด — บัญชีธนาคารจริง, บอทที่ควบคุมมือถือ, งาน (job) ที่บอทรัน, และธุรกรรมดิบที่ตรวจพบจากฝั่งธนาคารก่อนจะถูกส่งต่อไปให้ PGW Core

## Multi-instance / Multi-partner — จุดที่ต่างจาก Admin Console ชัดเจนที่สุด

OpsDash ไม่ได้ผูกกับพาร์ทเนอร์ "JoinPay" รายเดียว — ล็อกอินด้วย **URL Endpoint + Token** (ไม่ใช่ username/password ปกติ) และมีฟีเจอร์ **"Switch Profile"** ที่มุมขวาบนของทุกหน้า ให้สลับไปมาระหว่างหลาย deployment ได้เลยโดยไม่ต้อง login ใหม่:

| Profile | API Endpoint |
|---|---|
| JoinPay (Active ในตัวอย่างที่สำรวจ) | `https://bank-adapter-api.joinpay.app` |
| HW | `https://bank-adapter-api.joinpay.app` (endpoint เดียวกับ JoinPay — **ยืนยันแล้วว่าเป็น profile/entity เดียวกัน** ไม่ใช่คนละบัญชี) |
| NextPay | `https://bank-adapter-api.nextpay.biz` (**คนละ domain จริง**) |

!!! danger "นัยสำคัญ — OpsDash คือ core technology ที่ให้บริการพาร์ทเนอร์ PGW ทั้งหมดพร้อมกัน"
    JoinPay เป็นแค่ **1 ใน deployment** ของซอฟต์แวร์ "Bank Adapter OpsDash" ไม่ใช่เจ้าของแต่ผู้เดียว — NextPay มี domain (`nextpay.biz`) แยกขาดจาก joinpay.app โดยสิ้นเชิง ยืนยันแล้วว่าเป็น **Partner ที่ร่วมมือกับ JoinPay จริง** (ดู [Watchtower → Groups](../watchtower/groups.md)) ไม่ใช่แค่ลูกค้าอีกรายที่ใช้ software เดียวกันเฉยๆ

    คุณสมบัติ "Manage Profiles" ให้ดู Name / URL / Last Used ของแต่ละ profile ที่เคย save credential ไว้ พร้อมปุ่มแก้ไข/ลบ

## โครงสร้างเมนูทั้งหมด

Nav bar บนสุดมี 8 เมนู:

| เมนู | สรุปสั้น |
|---|---|
| [Dashboard](dashboard.md) | ภาพรวมมูลค่า/จำนวนธุรกรรม inbound-outbound และ transfer event ทั้งระบบ |
| [Client](clients.md) | จัดการระบบภายนอกที่ลงทะเบียนเชื่อมต่อ API เข้ากับ OpsDash เอง (คนละแนวคิดกับ Merchant) |
| [Bank Account](bank-accounts.md) | บัญชีธนาคารจริงที่ OpsDash ดูแล พร้อม config เฉพาะของแต่ละธนาคาร |
| [Onboard](onboard.md) | พื้นที่ staging ข้อมูลบัญชีธนาคารใหม่ก่อนจะ "Move" เป็นบัญชีจริง |
| [Bot](bots.md) | รายการบอทที่ควบคุมมือถือ/อุปกรณ์จริงแต่ละเครื่อง |
| [Job](jobs.md) | ประวัติงานที่บอทรัน พร้อม raw log |
| [Transaction](transactions.md) | ธุรกรรมดิบระดับธนาคาร ก่อนถูกส่งต่อไปเป็น Transaction ฝั่ง Admin Console |
| [Company](company.md) | ตั้งค่า proxy ที่ระบบใช้ route traffic ออกไปคุยกับธนาคาร |

## ภาพรวม Flow ทั้งระบบ (สรุปจากหลักฐานที่เจอ)

```
มือถือจริง (ล็อกอินแอพธนาคารค้างไว้) ── USB/Tunnel ──▶ Bot (Appium)
                                                          │
บาง Kind (เช่น KTBBIZ) มี REST API ทางการ ──────────────┤
(business.krungthai.com/ktb/rest/biznext-channel)        │
                                                          ▼
                                                  OpsKash บันทึกเป็น
                                                  Transaction (raw)
                                                          │
                                       ยิง callback ไปหา client "core"
                                       (https://bank-adapter.joinpay.app/api/v1/receiver/callback)
                                                          │
                                                          ▼
                                          PGW Core ประมวลผล/match
                                                          │
                                                          ▼
                                    โผล่เป็น Transaction ใน Admin Console
                                    (พร้อม webhook แจ้ง Merchant ต่อ)
```

!!! tip "หลักฐานที่ยืนยัน flow นี้"
    - Client "core" (Tier: Partner) มี Endpoint = `https://bank-adapter.joinpay.app/api/v1/receiver/callback` — path `/receiver/callback` บ่งชี้ปลายทางรับ webhook จาก OpsDash เอง
    - Transaction ใน OpsDash มีปุ่ม "**Resend Webhook**" ยืนยันว่า OpsDash ยิง webhook ออกจริง
    - Bank Account ทุกตัวที่เจอใน Bank Account list มี `Client Code = core` — ยืนยันว่าบัญชีธนาคารที่ใช้งานจริงทั้งหมดถูกจัดกลุ่มไว้ใต้ client "core"

!!! danger "จุดที่ต้องแก้ไขความเข้าใจจากเอกสารเดิม — ไม่ใช่ทุกธนาคารใช้กลไกเดียวกัน"
    จากหลักฐานในหน้า [Job](jobs.md) และ [Transaction](transactions.md): **KTBBIZ (Krungthai Business) มี REST API ทางการจริง** (เห็น raw data ที่มี field ตรงกับ schema ของ `biznext-channel` API) ธุรกรรมกลุ่มนี้ **ไม่มี Job ID เลย** (ไม่ต้องพึ่งบอท) ในขณะที่ **KBIZ (Kasikorn Business) ใช้ Bot/Appium ควบคุมมือถือจริง** ต้องรัน Job แล้วอาจ fail ด้วย error ระดับอุปกรณ์ (เช่น "device not found") — สอดคล้องกับสมมติฐานดั้งเดิมใน [Deep-dive: Match Type](../console/match-type.md) ที่ว่าคุณภาพข้อมูลจากแต่ละธนาคารไม่เท่ากัน แต่ตอนนี้มีหลักฐานระดับ infrastructure ยืนยันเหตุผลเบื้องหลังแล้ว

## Entity หลักที่เจอ (สรุป)

- **Client** — ระบบ/บัญชีที่มีสิทธิ์เรียก API ของ OpsDash เอง มี Tier: Onboarder / Partner / Owner
- **Bank Account** — บัญชีธนาคารจริง 1 บัญชี ผูกกับ Client Code, มี Kind (ยี่ห้อ/adapter ธนาคาร) และ config เฉพาะของแต่ละ Kind
- **Bot** — ตัวแทนมือถือ/อุปกรณ์จริง 1 เครื่องที่ผูกกับ Bank Account 1 บัญชี เชื่อมต่อผ่าน USB หรือ Tunnel
- **Job** — งานที่ Bot รันจริง (พบเป็น batch "BULK...") พร้อม raw output log
- **Transaction** — ธุรกรรมดิบระดับธนาคาร (Inbound/Outbound) ที่ตรวจพบ ก่อนส่งต่อเป็น Transaction ฝั่ง Admin Console
