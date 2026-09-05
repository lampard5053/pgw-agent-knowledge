# Groups (Account Groups)

Breadcrumb: "{Instance}" — Header: "Account Groups" — ปุ่ม **Create Group** (มุมขวาบน)

!!! danger "Groups คือกลไกควบคุมสิทธิ์การมองเห็นบัญชี ไม่ใช่แค่ป้ายจัดหมวดหมู่"
    Banner เตือนที่พบจริง: **"49 accounts not assigned to any group"** — *"These accounts are not visible to any scoped users. Assign them to a group or they will be inaccessible to operators and viewers."*

    ยืนยันชัดเจนว่า **Group ควบคุมว่า user ระดับ Operator/Viewer มองเห็นบัญชีไหนได้บ้าง** — บัญชีที่ไม่ได้ผูก group ใดเลยจะ**มองไม่เห็นเลยสำหรับ user ที่ไม่ใช่ admin**

## ตาราง Account Groups

คอลัมน์: NAME, DESCRIPTION, ACCOUNTS (จำนวน), MEMBERS (จำนวน), ACTIONS

ตัวอย่างจริงที่พบ (instance: JoinPay):

| Name | Description | Accounts | Members |
|---|---|---|---|
| AccountJoinPay_Team | "บัญชีบุคคล" | 4 | 2 |
| **NextPayXJoinPay** | "เป็นบัญชีที่ทาง NextPay นำมาเปิด Partner ในฝั่งของ JoinPay" | 9 | 4 |
| pool-aix | - | 1 | 0 |
| **T126** | - | 6 | 0 |
| X2 Pool | - | 4 | 2 |

!!! danger "ไขคำถามค้าง 2 ข้อพร้อมกัน"

    **1) ยืนยัน "NextPayxJoinPay" ที่เห็นใน [Direct Sync](direct-sync.md)** — คำอธิบายบอกตรงๆ ว่า "เป็นบัญชีที่ทาง **NextPay นำมาเปิด Partner** ในฝั่งของ JoinPay" — นี่คือความสัมพันธ์ทางธุรกิจจริง NextPay กับ JoinPay ร่วมมือกันแชร์บัญชีธนาคารจริง (ไม่ใช่ 2 บริษัทที่ไม่เกี่ยวข้องกัน)

    **2) ไขปริศนา suffix "-T126"** ที่เจอซ้ำๆ ในชื่อบัญชีทั้งใน [OpsDash → Bank Account](../opsdash/bank-accounts.md) และ [Direct Sync](direct-sync.md) — **"T126" คือชื่อ Group นี้เอง** (มี 6 accounts ผูกอยู่) แปลว่า suffix ที่เห็นในชื่อบัญชีคือการ tag ว่าบัญชีนั้นเป็นสมาชิกของ group T126

!!! question "ค้างไว้"
    "pool-aix" อาจเกี่ยวกับ merchant "aixStore3" ที่เคยเห็นใน [Merchant Accounts](../console/merchant/accounts.md) ฝั่ง Admin Console (มีหลายบัญชีธนาคาร/นิติบุคคลผูกอยู่) หรือไม่ — ยังไม่ยืนยัน

## ฟอร์ม Edit Group

Title: "Edit Group" — "Manage details, accounts, and members"

| ฟิลด์ | รายละเอียด |
|---|---|
| Name | ชื่อ group |
| Description | คำอธิบาย |
| แท็บ **Accounts (n)** | search box + checkbox list เลือกบัญชีที่อยู่ใน group |
| แท็บ **Members (n)** | search box + checkbox list เลือก user ที่มีสิทธิ์เข้าถึง group นี้ |

ปุ่ม Cancel / Save Changes

### ตัวอย่างจริง — แท็บ Members ของ "NextPayXJoinPay" (4 members)

| User | Email | Role |
|---|---|---|
| Sky_NextPay ✅ | superadminsky@nextpay.app | Operator |
| X2 ☐ | x2@ocn.gg | Operator |
| 100MNextPay ✅ | next@nextpay.biz | Operator |
| Ace ☐ | ace@joinpay.app | **Manager** |
| ronnieNextPay ✅ | ronnie@nextpay.biz | Operator |
| SkyNextPay ✅ | sky@nextpay.app | Operator |
| shinchan ☐ | shinchan@ace.joinpay.app | Operator |

!!! danger "เจอ RBAC role ของ Watchtower เอง + หลักฐานคนจริงข้ามบริษัท"
    พบ role **Operator** และ **Manager** — เป็น role system ของ Watchtower เอง (คนละชุดกับ Company Roles/Merchant Roles ของ Admin Console หรือ Client Tier ของ OpsDash)

    Email ยืนยันคนจริงจากทีม **NextPay** (`@nextpay.app`, `@nextpay.biz`) มีสิทธิ์ Operator เข้าถึง group ที่อยู่ใน**instance JoinPay**ได้ — พิสูจน์ทางเทคนิคว่าความร่วมมือข้ามแบรนด์เป็นเรื่องจริง ไม่ใช่แค่ตั้งชื่อให้ดูเหมือนกัน
