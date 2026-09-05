# Job

Subtitle: "Manage and monitor job execution" — Card: "Job List" / "View all jobs and their current status"

## Filter และ Auto Reload

Job Reference (search), **State** dropdown, ปุ่ม Search/Reset, "Filters: No filters applied", Add Filter, Auto Reload (▷5s ▷15s ▷30s ▷60s)

### State dropdown — enum
**All States, Processing, Success, Failed**

## ตาราง Job List

| คอลัมน์ | รายละเอียด |
|---|---|
| Job Reference | รูปแบบ **UUIDv7** (สังเกต segment "7000" ตรงกลาง = UUID version 7 มาตรฐาน มี timestamp ฝังอยู่ในตัว) |
| Bank Reference | รูปแบบ `BULK{YYMMDD}{เลขลำดับ}` เช่น `BULK260728409232` — ตัวเลขวันที่ตรงกับวันที่ job รันจริง |
| Client Code | ทุกแถวที่พบเป็น `core` |
| State | ดู enum ด้านบน |
| Kind | ทุกตัวอย่างที่พบเป็น **KBIZ** |
| Started At / Completed At | เวลาเริ่ม-จบของ job |
| Actions | 👁 ดูรายละเอียด / 🔧 จัดการ |

!!! tip "Bank Reference เป็น 'BULK job' ไม่ใช่ job เดี่ยว"
    Prefix "BULK" บ่งชี้ว่านี่คือ job แบบชุด/batch (bulk operation) ไม่ใช่การรันครั้งละ 1 ธุรกรรม

## Job Details modal — เจอ raw log ของ Appium bot ตรงๆ

Modal title: "Job Details" — "View details for job {MongoDB ObjectId}" (คนละ id กับ Job Reference ที่เป็น UUID)

**Basic Information:** Client Code, Job Reference, Bank Reference, State, Kind, **Bot ID** (ObjectId เชื่อมกลับไปที่ entity [Bot](bots.md))
**Timing Information:** Started At, Completed At
**Output:** log แบบ raw text เต็ม พร้อมปุ่ม **Copy Output**

### ตัวอย่างจริง — Job ที่ Failed

```
Running kbiz bot for job ref: 019fa4d7-4ab1-7000-a1d0-acbe9cba2de0 on device RFCR20RQQAE
[1] Account error: Failed to find the transaction ID.
[2] Account error: Failed to find the search group transaction field.
[3] device 'RFCR20RQQAE' not found
```

### ตัวอย่างจริง — Job ที่ Success

```
Running kbiz bot for job ref: 019f8bd0-abc7-7000-9671-3a120edfa119 on device 192.168.1.106:5577
[1] Bot run completed successfully.
```

!!! danger "การค้นพบสำคัญที่สุด — ยืนยัน mechanism ของ KBIZ bot ด้วย log จริง"

    **1) `RFCR20RQQAE` คือ Android device serial จริง** (รูปแบบ ADB serial ของมือถือ Samsung) — ยืนยัน physical phone farm 100% ไม่ใช่ mock/simulator

    **2) เจอ 2 รูปแบบ device identifier ต่างกัน ตรงกับ Connection type ที่เห็นในหน้า [Bot](bots.md) พอดี:**

    - Job ที่ fail ใช้ device แบบ **serial number** (`RFCR20RQQAE`) = การเชื่อมต่อแบบ **USB**
    - Job ที่ success ใช้ device แบบ **IP:port** (`192.168.1.106:5577`) = การเชื่อมต่อแบบ **Tunnel/network** (ADB over network มาตรฐาน)

    **3) "kbiz bot" ทำงานโดยการอ่านหน้าจอแอพ ไม่ใช่เรียก REST API** — ข้อความ error "Failed to find the transaction ID" / "Failed to find the search group transaction field" อ่านได้ว่าบอทกำลัง**เปิดแอพ K-BIZ แล้วค้นหารายการ transaction บนหน้าจอเพื่อ verify** ทีละขั้น พอหาไม่เจอทั้งสองครั้งถึงจะเช็คว่า device ยังต่ออยู่ไหม (`device not found`) — เรียงลำดับ error แบบ retry/fallback เห็นชัด

!!! danger "จุดที่ต้องแก้ไขความเข้าใจจาก [Deep-dive: Match Type](../console/match-type.md) เดิม"
    เอกสารเดิมเคยตั้งสมมติฐานว่า "*BIZ ทุกตัว (KBIZ/KTBBIZ/SCBBIZ) น่าจะมี business banking API ทางการที่แม่นยำกว่า personal banking" — จากหลักฐาน log ชุดนี้ **KBIZ (Kasikorn Business) ก็ยังใช้ Bot/Appium อ่านหน้าจอเหมือนบัญชี personal** ไม่ได้มี API ทางการเสมอไป

    ในทางกลับกัน ดูหลักฐานที่ [Transaction](transactions.md) พบว่า **KTBBIZ (Krungthai Business) มี REST API ทางการจริง** (`business.krungthai.com/ktb/rest/biznext-channel`) และธุรกรรมกลุ่มนั้น**ไม่มี Job ID เลย** (ไม่ต้องพึ่งบอท) — สรุปคือ **ต้องแยกพิจารณาเป็นรายธนาคาร ไม่ใช่เหมารวมทั้งกลุ่ม "*BIZ"**: บางเจ้ามี API ทางการ บางเจ้าไม่มีและต้องพึ่ง Bot อ่านหน้าจอแทน

    ผลกระทบต่อ Match Type: ความแม่นยำทศนิยมที่ต่างกันระหว่างธนาคารอาจไม่ได้มาจาก "มี API vs ไม่มี API" เพียงอย่างเดียว แต่มาจากคุณภาพการแสดงผลของแอพ/API แต่ละธนาคารเองด้วย — ยังต้องการหลักฐานเพิ่มเพื่อสรุปแน่ชัด
