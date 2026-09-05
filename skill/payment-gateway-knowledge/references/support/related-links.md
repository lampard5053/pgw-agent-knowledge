# ลิงก์ที่เกี่ยวข้อง

สรุปว่าเจอปัญหาแบบไหน ควรไปเช็คที่เครื่องมือ/หน้าไหนก่อน — รวบรวมจากรูปแบบปัญหาที่เจอจริงใน [เคสที่พบบ่อย](common-cases.md)

## เจอปัญหาแบบนี้ → ไปเช็คที่นี่

| เจอปัญหา | ไปเช็คที่ | เพราะอะไร |
|---|---|---|
| ลูกค้าแจ้งฝากเงินแล้วเครดิตไม่เข้า (unknown_deposit) | [Admin Console → Transactions](../console/payment/transactions.md) ค้นด้วย pre-transaction UUID จากคำสั่ง `/md` | หา transaction จริงเทียบกับสลิป แล้วกด Match/Manual Success |
| สงสัยว่าลูกค้าคนนี้ผูกกับหลาย merchant ด้วยบัญชีเดียวกันไหม | [Admin Console → Merchant Customers](../console/merchant/customers.md) ค้นด้วยเลขบัญชีผู้โอน | เห็น customer record ทั้งหมดที่ใช้เลขบัญชีนั้นข้าม merchant (ดู [เคสที่ 2](common-cases.md#เคสที่-2)) |
| Withdraw ค้างสถานะ Created/Failed นาน | [OpsDash → Bank Account](../opsdash/bank-accounts.md) ค้นด้วยเลขบัญชีต้นทาง | เช็ค Status/Flag ของ Payment Channel — ถ้า Suspend/Captcha แปลว่า Bot ติดปัญหา (ดู [เคสที่ 3](common-cases.md#เคสที่-3)) |
| อยากรู้ว่า Job ของธุรกรรมนั้นเคยถูกสร้าง/รันจริงไหม | [OpsDash → Job](../opsdash/jobs.md) หรือ [Watchtower → Jobs](../watchtower/jobs.md) | ดู raw log ของ Bot ว่าพยายามทำถึงไหน ติด error อะไร |
| เช็ควงเงินโอนรายวันของบัญชี/อุปกรณ์ | [PGWay → Phone Status](../pgway/phone-status.md) | ดู Daily Limit / Transferred Today / Remaining ของแต่ละอุปกรณ์ |
| บอทค้าง ต้องแก้ CAPTCHA/WDA ด้วยมือ | [PGWay → Devices](../pgway/devices.md) | ดู alert "N devices need attention" + ปุ่ม Launch WDA/Reconnect |
| อยากดูภาพรวมข้ามหลายพาร์ทเนอร์พร้อมกัน | [Watchtower → Dashboard](../watchtower/dashboard.md) / [Accounts](../watchtower/accounts.md) | มองข้าม 11 พาร์ทเนอร์ในเครือได้ในหน้าเดียว |
| อยากรู้ Seller/Merchant สังกัดใคร | [Admin Console → Sellers](../console/seller/sellers.md) / [Merchants](../console/merchant/merchants.md) | ดูโครงสร้าง 1 Seller : หลาย Merchant |

## โครงสร้าง Telegram (สรุปสั้น)

ดูรายละเอียดเต็มที่ [ภาพรวม](index.md) — สรุปสั้น: ห้อง Support ต่อร้านค้า (`[JoinPay][Support] {รหัสร้านค้า}`) → คำสั่ง `/md {UUID}` → forward เข้าห้องกลาง `[Ticket] JoinPay`

## หน้าอ้างอิงหลักในเอกสารชุดนี้

| หมวด | หน้า |
|---|---|
| ภาพรวมทั้งระบบ + ตารางตั้งชื่อธนาคารข้ามระบบ | [หน้าแรก](../index.md) |
| Admin Console (Transactions, Merchant, Seller) | [Admin Console — ภาพรวม](../console/index.md) |
| OpsDash (Bank Account, Bot, Job) | [OpsDash — ภาพรวม](../opsdash/index.md) |
| Watchtower (มองข้ามพาร์ทเนอร์) | [Watchtower — ภาพรวม](../watchtower/index.md) |
| PGWay (อุปกรณ์/บอท/วงเงินรายวัน) | [PGWay — ภาพรวม](../pgway/index.md) |
| คำถามค้างทั้งหมด | [คำถามที่ยังไม่มีคำตอบ](../open-questions.md) |
