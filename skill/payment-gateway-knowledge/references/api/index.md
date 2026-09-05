# PGW API — ภาพรวม (Merchant-facing)

หน้านี้อธิบายว่า Payment Gateway (PGW) คืออะไร ทำหน้าที่อะไร และไม่ยุ่งกับอะไร — สำหรับ Merchant ที่จะ integrate ผ่าน REST API

!!! note "แหล่งที่มาของเนื้อหาในหมวด PGW API ทั้งหมด"
    เนื้อหาในหมวดนี้ (`docs/api/`) แปลงมาจากไฟล์ต้นฉบับ `pgw_service.html` และตรวจทานไขว้กับ OpenAPI spec จริง (`api-1.json`, version `v1.5.13`) แล้ว — ข้อมูลที่นำมาลงถือว่ายืนยันแล้วจากทั้งสองแหล่ง เว้นแต่จะระบุเป็นอย่างอื่น

## PGW คืออะไร

Payment Gateway (PGW) คือ **B2B SaaS service** ที่ให้ Merchant (เจ้าของเว็บไซต์/แอป) เรียกใช้ผ่าน API เพื่อจัดการการเงินระหว่าง End User กับ Merchant

PGW **ไม่ยุ่งกับ business logic ของ Merchant** เช่น เครดิตในเกม, การเดิมพัน, สินค้า — หน้าที่ของ PGW คือรับเงินเข้า/โอนเงินออกในนามของ Merchant อย่างปลอดภัยเท่านั้น

## สิ่งที่ PGW จัดการให้

| ประเภทธุรกรรม | คำอธิบาย |
|---|---|
| **Deposit** (รับเงินเข้า) | End User โอนเงินเข้าบัญชีธนาคารของ PGW → PGW ยืนยันเงินเข้าและแจ้ง Merchant ผ่าน webhook → Merchant บวกเครดิตให้ End User ในระบบของตัวเอง |
| **Withdraw** (โอนเงินออก) | Merchant สั่งโอนเงินออกจาก wallet ไปยังบัญชีธนาคารของ End User → PGW โอนจริงผ่านธนาคาร → แจ้ง Merchant เมื่อสำเร็จหรือล้มเหลว |
| **Top-up** (เติมทุน) | Merchant โอนเงินเข้า PGW wallet เพื่อสำรอง liquidity สำหรับรองรับ withdraw ของ End User |
| **Settlement** (จ่ายเงินคืน) | PGW รวบยอดสุทธิในรอบบัญชี (หักค่าธรรมเนียม) แล้วโอนให้ Merchant ตามรอบ (รายวัน/รายสัปดาห์) |

รายละเอียด flow แต่ละประเภทดูที่ [Transaction Flows](transaction-flows.md)

## ทำไม Merchant ต้องใช้ PGW

การจัดการธนาคารตรงโดย Merchant เองทำได้ยาก — ต้องผูก API ธนาคาร, monitor ยอดเข้า, จัดการ reconcile, handle edge case ต่างๆ เอง

PGW สรุปทั้งหมดนี้เหลือแค่ **3 API calls หลัก**: สร้าง customer → สร้าง deposit order → รับ webhook — Merchant ไม่ต้องแตะระบบธนาคารโดยตรงเลย

## ขอบเขตของ PGW (Scope)

| ✅ PGW รับผิดชอบ | ❌ PGW ไม่ยุ่งกับ |
|---|---|
| รับ deposit request จาก Merchant | Business logic ของ Merchant (เกม, สินค้า, บริการ) |
| สร้าง QR / checkout URL ให้ End User | การบวก/หักเครดิต End User ในระบบ Merchant |
| Monitor บัญชีธนาคาร (Bank Adapter) | ราคา, promotion, bonus ของ Merchant |
| Match เงินโอนกับ order | KYC / identity verification ของ End User |
| ส่ง webhook แจ้ง Merchant | Customer service ของ Merchant กับ End User |
| โอนเงินออก (withdraw) ผ่านธนาคาร | การตัดสินใจว่าจะ approve หรือ reject End User |
| Top-up wallet, Settlement รอบบัญชี | |
| Verify slip (ป้องกันสลิปปลอม) | |

!!! tip "ตรงกับสถาปัตยกรรมที่เห็นจากฝั่ง Console/OpsDash"
    "Bank Adapter" ที่กล่าวถึงในหน้านี้คือเลเยอร์เดียวกับที่เอกสารฝั่ง Admin Console เรียกว่า **OpsDash** — ดู [OpsDash — ภาพรวม](../opsdash/index.md) สำหรับรายละเอียดว่า Bank Adapter ทำงานยังไงจริงๆ (Bot + REST API hybrid ต่อบัญชีธนาคาร)

## หน้าในหมวดนี้

| หน้า | เนื้อหา |
|---|---|
| ภาพรวม (หน้านี้) | PGW คืออะไร, ขอบเขต |
| [Roles & Hierarchy](roles.md) | บทบาทใน ecosystem, Fee Structure, Merchant Onboarding |
| [Integration Guide](integration-guide.md) | ขั้นตอน onboarding, setup, code sample ทีละ endpoint |
| [API Reference](reference.md) | รายละเอียด endpoint ทั้งหมด + Bank Codes |
| [Transaction Flows](transaction-flows.md) | State diagram, Deposit/Withdraw/Top-up/Settlement แบบละเอียด + edge cases |
| [FAQ](faq.md) | คำถามที่พบบ่อยพร้อมคำตอบยืนยันจาก spec |
