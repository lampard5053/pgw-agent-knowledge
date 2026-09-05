# Flow

Breadcrumb: "{Instance} > Flow" — Header: "Flow Visualization" — มุมขวาบน: dropdown ช่วงเวลา (เช่น "Last 7 days") + จำนวนธุรกรรมในช่วงนั้น (เช่น "5 transactions")

มี 3 แท็บย่อย แสดงข้อมูล transaction flow ชุดเดียวกันคนละมุมมอง: **Timeline, Sankey, Network**

## แท็บ Timeline

*"Chronological scatter of transfers. Size = amount, color = direction (green=in, red=failed). Use the slider to zoom into time ranges."*

Scatter chart แกน Y = Amount, มี slider/brush ด้านล่างสำหรับ zoom ช่วงเวลา

!!! tip "สีบอกสถานะ ไม่ใช่ทิศทาง"
    "red" หมายถึง **"failed"** ไม่ใช่ "outbound" — สีบอกว่าธุรกรรมสำเร็จหรือไม่ (green = in สำเร็จ, red = failed) ไม่เกี่ยวกับทิศทาง Inbound/Outbound โดยตรง

## แท็บ Sankey

*"Money flow between accounts. Left = source, right = destination. Band width = total amount transferred."*

แสดงเส้นทางเงินระหว่างบัญชี ความหนาของแถบ = มูลค่ารวมที่โอน — ตัวอย่างจริงพบ flow หลัก `scbbiz:4242062383 → KBNK:2163330965` เป็นก้อนใหญ่ที่สุดในช่วงเวลานั้น

## แท็บ Network

*"Account relationship graph. Node size = transfer volume. Drag nodes to explore. Click to highlight connections."*

Node-link graph — ขนาดโหนด = ปริมาณเงินที่ไหลผ่านบัญชีนั้น ลากโหนดสำรวจได้ คลิกเพื่อ highlight เส้นเชื่อมที่เกี่ยวข้อง

!!! tip "ฟีเจอร์นี้น่าจะเป็นเครื่องมือสืบสวน/ตรวจสอบเส้นทางเงิน"
    ทั้ง 3 มุมมองใช้ dataset เดียวกันตามช่วงเวลาที่เลือก — เหมาะสำหรับ ops/fraud team ใช้ไล่ดูว่าเงินไหลจากบัญชีไหนไปบัญชีไหนบ้าง ในกรณีสงสัยธุรกรรมผิดปกติ
