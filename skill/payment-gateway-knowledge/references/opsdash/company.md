# Company

Subtitle: "Manage company proxy configuration" — Card: "Proxy Configuration" / "Configure the proxy list and active proxy URL"

!!! danger "ค่า credential ในหน้านี้ถูก mask ไว้โดยตั้งใจ"
    ตอนสำรวจหน้านี้พบ proxy URL ที่มี username/password ฝังอยู่ตรงๆ ในค่าเดียวกัน (รูปแบบ `socks5://user:pass@host:port`) เพื่อความปลอดภัย เอกสารนี้จะ**แสดงเฉพาะโครงสร้าง ไม่ใส่ค่า credential จริง** — ถ้าจำเป็นต้องดูค่าจริงให้เข้าไปดูในระบบ OpsDash โดยตรงเท่านั้น ห้าม commit ค่า credential จริงลงในเอกสารนี้หรือที่ใดก็ตามที่ push ขึ้น git

## Proxy Configuration

| ฟิลด์ | รายละเอียด |
|---|---|
| Current Proxy URL | dropdown เลือก proxy ที่กำลังใช้งานอยู่ — ตัวเลือกมี **"None (no proxy)"** เสมอ + proxy ทุกตัวที่อยู่ใน Proxy List พร้อม ✓ กำกับตัวที่ active |
| คำอธิบายใต้ dropdown | "The proxy URL currently in use by the system" |
| Proxy List | ตารางรายการ proxy ทั้งหมดที่เคยเพิ่มไว้ แต่ละแถวมี badge `active`/ไม่ active + ปุ่ม 🗑️ ลบ |
| ช่อง Add proxy ใหม่ | placeholder รูปแบบ `socks5://proxy-host:port` + ปุ่ม **+ Add** ("Press Enter or click Add to append a proxy URL") |
| ID / Updated | metadata ท้ายฟอร์ม (MongoDB ObjectId + timestamp แก้ไขล่าสุด) |
| ปุ่ม Save Changes | บันทึกการตั้งค่า |

### ตัวอย่างจริงที่พบ (มาสก์ credential แล้ว)

```
socks5://***:***@p.webshare.io:80    [active]
```

ผู้ให้บริการคือ **Webshare.io** (residential/rotating proxy provider เชิงพาณิชย์ที่มีชื่อเสียง) รูปแบบ username ที่พบมีส่วนระบุ **"TH"** (ประเทศไทย) และ **"rotate"** (โหมดหมุนเวียน IP)

!!! danger "นัยสำคัญของฟีเจอร์นี้ (คาดว่า) — หลบ fraud detection ของธนาคาร"
    **คาดว่า**ระบบ Bot/API ทั้งหมดที่คุยกับแอพ/API ธนาคาร**จงใจ route traffic ผ่าน IP ประเทศไทยที่หมุนเวียนตลอด** เพื่อให้ดูเหมือน traffic ปกติจากในประเทศ ไม่ใช่ traffic อัตโนมัติจาก data center หรือต่างประเทศที่ธนาคารอาจ flag ว่าน่าสงสัย — เป็นเทคนิคที่พบทั่วไปในงาน automation ที่ต้องคุยกับระบบธนาคาร/บริการที่มีการตรวจจับ bot เข้มงวด ยังไม่มีหลักฐานยืนยัน 100% ว่าใช้เพื่อจุดประสงค์นี้เท่านั้น

!!! danger "สมมติฐานที่แคบลง หลังเห็น PGWay — proxy น่าจะใช้เฉพาะฝั่ง REST API"
    [PGWay → Devices](../pgway/devices.md) เผยว่าอุปกรณ์ bot ทุกเครื่องเชื่อมต่อผ่าน **IP เครือข่ายภายใน** (เช่น `192.168.0.250:5555`) — เป็นมือถือจริงที่ต่อ WiFi ในสถานที่จริง ไม่ได้ผ่าน proxy Webshare.io เลย (ไม่จำเป็นต้องพราง เพราะเป็น traffic จากมือถือจริงที่ล็อกอินแอพธนาคารอยู่แล้ว เหมือน user ทั่วไป) — **คาดว่า** proxy Webshare.io (TH-rotate) ใช้เฉพาะฝั่งที่เซิร์ฟเวอร์ยิง REST API ตรง (เช่น เรียก KTB Biznext Channel) เพื่อให้ traffic ดูเหมือนมาจาก IP ไทยปกติ ไม่ใช่จาก data center ต่างประเทศ — ยังไม่ยืนยัน 100%

!!! question "คำถามค้าง"
    มี fallback ยังไงถ้า proxy ที่ active หลุด/ใช้ไม่ได้ — ระบบ auto-switch ไป proxy ตัวอื่นใน list หรือ "None (no proxy)" เอง หรือต้องคนมาเปลี่ยนเอง?
