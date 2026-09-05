# Phone Status

Header: "Phone Status" | มุมขวา: "Snapshot: {timestamp} (ICT)" + ปุ่ม Refresh
Subtitle: *"Daily transfer totals reset at midnight (Asia/Bangkok) and are computed from successful transfers. Static snapshot — refresh for latest."*

!!! tip "นโยบายรีเซ็ตวงเงินรายวัน"
    ยอดโอนรายวันรีเซ็ตเที่ยงคืน **เวลาไทย (Asia/Bangkok)** และคำนวณจาก**ธุรกรรมที่สำเร็จเท่านั้น** — หน้านี้เป็น **static snapshot ต้องกด Refresh เองถึงจะอัปเดต** ไม่ใช่ real-time

## Filter

**Sort by:** Name, Balance (high→low), Daily limit (high→low), Transferred today (high→low), Remaining (low→high)
**Status:** All, Active, Offline, Disabled
**Type:** All, iOS, Android
Checkbox: "Only limit reached"

## ตาราง Phone Status

คอลัมน์: DEVICE, TYPE (platform · Kind), **ACTIVE** (Active เขียว/Offline แดง/Disabled แดง), BALANCE, **DAILY LIMIT**, **TRANSFERRED TODAY**, **REMAINING**

!!! danger "เจอวงเงินโอนรายวันจริงตามประเภทบัญชี — ยืนยันตรงกับความเป็นจริงของธนาคารไทย"
    | Kind | Daily Limit ที่พบ |
    |---|---|
    | **KBIZ** (Kasikorn Business) | **1,000,000.00** บาท |
    | GSB (ส่วนบุคคล) | 100,000-200,000 บาท |
    | KBANK (ส่วนบุคคล) | 50,000 บาท |

    วงเงิน KBIZ สูงกว่าบัญชีส่วนบุคคลมาก — ตรงกับความจริงที่บัญชี **Business Banking มีวงเงินโอนต่อวันสูงกว่าบัญชีบุคคลธรรมดา**จริงในระบบธนาคารไทย ยืนยันว่าระบบเคารพ/ผูกกับ policy วงเงินจริงของแต่ละประเภทบัญชี

    พบ device prefix เฉพาะสำหรับ KBIZ ที่ไม่เจอที่อื่น: **"PRC..."**, **"RAC..."** (เช่น `PRC02-KBIZ-1963590675`, `RAC01-KBIZ-2222710327`) แยกจาก PR/RA/TP/KR ทั่วไป

!!! tip "Offline ≠ ไม่ได้ใช้งานวันนี้"
    หลายแถวที่ Status = **Offline** ก็ยังมี Transferred Today สูง (เกือบเต็ม limit) — แปลว่า "Offline" หมายถึงแค่**ตอนนี้ไม่ได้เชื่อมต่ออยู่** ไม่ได้แปลว่าไม่ถูกใช้งานมาทั้งวัน (เครื่องอาจโอนจนใกล้เต็ม limit แล้วเพิ่งหลุดการเชื่อมต่อภายหลัง) เช่นเดียวกับแถวที่ Status = **Disabled** ก็ยังมี Transferred Today > 0 (ถูกปิดใช้งาน**หลังจาก**มีการโอนไปแล้วบางส่วน)
