# Balance Checks

Header: "Balance Checks"
Subtitle: *"Only the latest successful check per device is kept; failed checks are retained for debugging."*

!!! tip "นโยบายการเก็บข้อมูล"
    เก็บแค่**การเช็ค balance ที่สำเร็จล่าสุด 1 รายการต่อเครื่อง** (รายการเก่าที่สำเร็จจะถูกทับ/ทิ้งไป) แต่**การเช็คที่ fail จะถูกเก็บไว้ทั้งหมดเพื่อ debug** — อธิบายได้ว่าทำไมตารางนี้มีแค่ 1 แถวต่อเครื่อง (ไม่ใช่ log เต็มประวัติ)

## Status dropdown
**All statuses, Passed, Failed, Error** (3 ค่า — น้อยกว่า Test Logs ที่มี 5 ค่า ไม่มี Running/Cancelled สำหรับ balance check)

## ตาราง Balance Checks

คอลัมน์: DEVICE (serial), BALANCE (฿), STATUS, CAUSE, DURATION, STARTED, ERROR, TEST ID

!!! tip "ยอดคงเหลือส่วนใหญ่กระจุกตัวที่ ~20,000 บาท"
    ตัวอย่างจริงส่วนใหญ่มียอด**ใกล้เคียงกันมากที่ 20,000-20,800 บาท** — คาดว่าบัญชีทดสอบเหล่านี้ถูกเติมเงินให้อยู่ที่เป้าหมายราวๆ 20,000 บาทเป็นมาตรฐาน (สำหรับใช้หมุนโอนทดสอบไปเรื่อยๆ)

!!! question "ข้อสังเกต"
    คอลัมน์ TEST ID ในหน้านี้เป็น **hash สั้น 8 ตัวอักษร** (เช่น `c9e69581`) ต่างจาก Test ID แบบ UUID ยาวที่เห็นใน [Test Logs](test-logs.md) โดยสิ้นเชิง — คาดว่า Balance Check test ใช้ ID scheme ของตัวเองแยกต่างหาก ยังไม่ยืนยันแน่ชัด
