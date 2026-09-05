# Admin (Token Management)

Header: "Token Management"

## Card: Create New Token

| ฟิลด์ | รายละเอียด |
|---|---|
| Username | placeholder "e.g. operator1" |
| Role | dropdown: **User, Admin** |
| ปุ่ม Generate Token | สร้าง token ใหม่ |

!!! tip "ยืนยัน auth แบบ token เหมือน OpsDash"
    ระบบนี้ใช้ **token-based auth** เหมือนที่เคยเห็นในหน้า login ของ [OpsDash](../opsdash/index.md) (URL Endpoint + Token) ไม่ใช่ username/password ทั่วไป — ยืนยันว่าเป็น pattern มาตรฐานของกลุ่มเครื่องมือ internal ops ทั้งหมดในเครือ pgway

## ตาราง Users

คอลัมน์: USERNAME, ROLE (`admin`/`user` badge สี), CREATED, LAST USED, ACTIONS (Delete / "You" สำหรับ user ปัจจุบัน)

ตัวอย่างจริงที่พบ (9 users): admin, operator1 (role: user), developer, **orga** (role: user), operatorsite, operator_max (You), operator_jon, operator_ggacc, operator_Sherry — ส่วนใหญ่เป็น role "admin"

!!! tip "Role system เรียบง่ายกว่าระบบอื่นมาก"
    มีแค่ **2 role: User, Admin** — เรียบง่ายกว่า [Watchtower](../watchtower/groups.md) (Operator/Manager) หรือ Admin Console (RBAC ละเอียดเป็น module) มาก

!!! question "ข้อสังเกต"
    Username **"orga"** ปรากฏในระบบนี้ด้วย — ชื่อตรงกับ client "orga"/"Orga 888" ที่เจอทั้งใน [OpsDash](../opsdash/clients.md) และ [Watchtower](../watchtower/index.md) ก่อนหน้า อาจเป็นบัญชีเดียวกันที่ใช้ชื่อซ้ำข้ามระบบ หรือบังเอิญตั้งชื่อคล้ายกัน ยังไม่ยืนยัน
