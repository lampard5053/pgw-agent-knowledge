# Accounts

หน้าดูสุขภาพบัญชีธนาคารระดับ **instance เดียว** (ต้องเลือก instance ก่อน เช่น "JoinPay") — Breadcrumb: "{Instance} > Accounts"

มุมซ้ายบนของ sidebar แสดง "{Instance} / {Role}" เช่น "JoinPay / Supporter" — role ของ user ปัจจุบันต่อ instance นั้น

## แถบสรุปด้านบน

| ฟิลด์ | รายละเอียด |
|---|---|
| THB Balance | ยอดรวมของ instance นี้ + เวลาข้อมูลล่าสุด |
| **ACTIVE / SUSPENDED / INACTIVE / TOTAL** | ตัวเลขสรุปสถานะบัญชี (สีเขียว/แดง/เทา) |
| แถบ breakdown ตาม Kind | เช่น "10/21 KTB BIZ", "1/11 KBIZ", "0/23 GSB", "4/13 SCB Biz", "4/4 SCB" |

!!! danger "ยืนยัน Kind enum เดียวกับ OpsDash — พิสูจน์ว่าเป็นข้อมูลชุดเดียวกัน"
    ตัวเลข breakdown รวมกันตรงกับตัวเลข ACTIVE/TOTAL เป๊ะ (เช่น 10+1+0+4+4 = 19 = ACTIVE) — Kind ที่เจอ (KTB BIZ, KBIZ, GSB, SCB Biz, SCB) ตรงกับ enum ของ [OpsDash → Bank Account](../opsdash/bank-accounts.md) ทุกตัวอักษร (ยกเว้น KPLUS/BBL/PGWM ที่ instance นี้ไม่ได้ใช้)

## Filter

Search (Name or number), **Bank** (dropdown), **Status** (dropdown), **Health** (dropdown)

### Bank dropdown (ตัวอย่างที่พบสำหรับ JoinPay)
All Banks, GSB, KBIZ, KTB BIZ, SCB, SCB BIZ

### Status dropdown
All Statuses, active, inactive, suspend

### Health dropdown {: #health }
All Health, **Needs Attention**, Captcha, Normal, Polling Limit

!!! danger "Status กับ Health เป็นคนละมิติกัน"
    **Status** = สถานะการทำงาน (active/inactive/suspend) ส่วน **Health** = สภาพ/ปัญหาที่เจอ (Normal/Needs Attention/Captcha/Polling Limit) — banner "N accounts need attention" ที่เห็นทั้งใน [Dashboard](dashboard.md) และหน้านี้ คือการนับบัญชีที่ **Health = "Needs Attention"** โดยเฉพาะ ไม่ใช่นับจาก Status

    - **"Captcha"** — บอทที่ควบคุมมือถือ (Appium) เจอหน้าจอ CAPTCHA ของแอพธนาคารแล้วติดขัด ต้องมีคนแก้ด้วยมือ (เชื่อมกับกลไก Bot ที่เจอใน [OpsDash → Job](../opsdash/jobs.md))
    - **"Polling Limit"** — คาดว่าบัญชีถูกเรียก/poll บ่อยเกินขีดจำกัดที่ธนาคารกำหนด เลยถูกระงับชั่วคราว

## ตาราง Accounts

| คอลัมน์ | รายละเอียด |
|---|---|
| BANK | badge สี ระบุ Kind (เช่น GSB, KBIZ) |
| ACCOUNT | ชื่อบัญชี + เลขบัญชีสีเทาด้านล่าง (เช่น `joinpay-020475598197`, `gsb-020474458138`, `orga-020475595284`) |
| Balance | ยอดคงเหลือ (sort ได้) |
| STATUS | badge Suspended (แดง) / Inactive (เทา) / Active (เขียว) + badge เสริม (Captcha/Polling Limit) ถ้ามี |

!!! danger "หลักฐานยืนยันตรงๆ ว่าเป็นบัญชีเดียวกับ OpsDash"
    เลขบัญชี **`020475875405`** (`gsb-020475875405`) ที่เห็นในตารางนี้ **ตรงเป๊ะ**กับบัญชีที่เห็นใน [OpsDash → Bot](../opsdash/bots.md) มาก่อน (Client Code: orga, Kind: gsb) — พิสูจน์ 100% ว่า Watchtower มอนิเตอร์ชุดข้อมูลเดียวกับ OpsDash

    Prefix ชื่อบัญชี (`joinpay-`, `gsb-`, `orga-`) **คาดว่า**ตรงกับ Client Code (`core`/`orga`) ที่เคยเห็นใน OpsDash เช่นกัน
