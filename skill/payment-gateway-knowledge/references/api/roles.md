# Roles & Hierarchy

ผู้เกี่ยวข้องใน PGW ecosystem ทั้งหมด และโครงสร้างค่าธรรมเนียม

## บทบาททั้ง 4 ระดับ

| Role | คำอธิบาย |
|---|---|
| **PGW Provider** | เจ้าของระบบ PGW — ดูแล Core, Bank Adapter, infrastructure เป็นคนที่ Partner/Merchant จ่าย platform fee ให้ (ตัวอย่าง: JoinPay, PGWay) |
| **Partner (Agent)** | ตัวแทนที่ PGW Provider แต่งตั้ง — หา Merchant มาใช้ระบบ ได้รับ revenue share จาก transaction ของ Merchant ในสังกัด ดูแล support เบื้องต้นให้ Merchant |
| **Merchant** | เจ้าของเว็บไซต์/แอปที่ integrate PGW API ใช้ API สร้าง deposit/withdraw order ได้รับ webhook เมื่อ transaction เสร็จ แล้วบวก/หักเครดิต End User เอง (ตัวอย่าง: เว็บเกม, เว็บ e-commerce, แอปลงทุน) |
| **End User** | ลูกค้าของ Merchant — เป็นคนโอนเงิน/รับเงินจริง ไม่ได้ interact กับ PGW โดยตรง ทำผ่าน UI ของ Merchant เท่านั้น PGW รู้จัก End User ผ่าน `customerUuid` ที่ Merchant ลงทะเบียนไว้ |

!!! tip "Partner ในเอกสารนี้ตรงกับ Partner ที่เห็นจริงใน Watchtower"
    คำว่า "Partner" ที่นี่คือแนวคิดเดียวกับ 11 พาร์ทเนอร์ที่เห็นจริงในหน้า [Watchtower — ภาพรวม](../watchtower/index.md) — แต่ละพาร์ทเนอร์ทำทุกอย่างได้เหมือน JoinPay (มี Admin Console/OpsDash ของตัวเอง) ไม่ใช่แค่ "แบรนด์ที่ยืมเทคใช้" ยืนยันจากผู้ใช้โดยตรง

    ส่วน **Merchant** ในเอกสาร API นี้ก็คือ Merchant ตัวเดียวกับที่เห็นในหน้า [Admin Console → Merchants](../console/merchant/merchants.md) — ผูกอยู่ใต้ Seller อีกที (โครงสร้าง 1 Seller : หลาย Merchant)

## Fee Structure — ค่าธรรมเนียมที่ pgw_service.html อธิบายไว้

`pgw_service.html` (เอกสารต้นฉบับที่ย้ายเนื้อหามาหน้านี้) อธิบายว่าทุก transaction มี fee 3 ชั้นซ้อนกัน:

| ชั้น | ใครได้รับ (ตามเอกสารเดิม) | ตัวอย่างอัตรา |
|---|---|---|
| **Platform Fee** | PGW Provider | 1.00% |
| **Partner Fee** | Partner (revenue share) | 0.50% |
| **Seller Fee** | Merchant ชั้นใน (ถ้า Merchant มี sub-seller) | 0.10% |

Fee ทั้งหมดหักจาก **Merchant wallet** ไม่ใช่หักจาก End User โดยตรง — Merchant เลือกเองว่าจะส่งต่อค่าธรรมเนียมไปยัง End User หรือรับภาระเองในรูปแบบอื่น

!!! danger "ขัดแย้งกับสิ่งที่ยืนยันไว้แล้วจากฝั่ง Console — 'Partner Fee' ยังไม่เคยเห็นตัวจริง"
    คำถามค้างเดิม "Partner Fee มีจริงไหม?" ถูกตอบไปแล้วจากการสำรวจ Admin Console จริง (ดู [คำถามที่ยังไม่มีคำตอบ → ตารางคำถามที่ตอบได้แล้ว](../open-questions.md)) ว่า **ไม่มี Partner Fee** — ระบบที่เห็นจริงใช้แค่ **Platform Fee / Seller Fee / Payment Fee** เท่านั้น

    พูดง่ายๆ คือ 3 ชั้นด้านบนเป็นสิ่งที่ `pgw_service.html` อธิบายไว้ในเชิง business concept เท่านั้น — **ยังไม่มีหลักฐานจาก UI จริงยืนยันว่า "Partner Fee" มีอยู่จริงในระบบ** อาจเป็นได้ทั้ง (ก) ยังไม่ได้ implement ตามที่ออกแบบไว้ หรือ (ข) มีอยู่จริงแต่เรียกชื่ออื่นในเอกสาร/ทีม Support ให้ตรวจสอบกับทีมพัฒนาอีกที

!!! tip "Seller Fee ตรงกับ Fee Dashboard/MDR Templates ที่เห็นใน Console"
    โครงสร้าง Seller Fee นี้คือกลไกเดียวกับที่เห็นเป็นรูปธรรมในฝั่ง Admin Console — ดู [Seller → Fee Dashboard](../console/seller/fee-dashboard.md) และ [Seller MDR Templates](../console/seller/mdr-templates.md) สำหรับหน้าตาจริงของการตั้งค่าอัตรา fee ระดับ Seller

## Merchant Onboarding — สิ่งที่ต้องเตรียม

| รายการ | รายละเอียด | ใครเตรียม |
|---|---|---|
| ชื่อบริษัท / เว็บไซต์ | ข้อมูลธุรกิจ Merchant | Merchant |
| บัญชีธนาคารรับ settlement | PGW โอน net amount ให้หลัง reconcile | Merchant |
| Callback URL | PGW ยิง webhook มาที่ URL นี้เมื่อ transaction เปลี่ยนสถานะ | Merchant (ต้อง implement endpoint ก่อน) |
| IP Whitelist (optional) | IP ที่ Merchant จะยิง API มาจาก | Merchant |
| Fee tier | % ตกลงกับ Partner / PGW | Partner + PGW |
| API Key + Secret Key | PGW ออกให้หลัง onboard | PGW Provider |
| Top-up เริ่มต้น | Merchant โอนเงินสำรองเข้า wallet เพื่อรองรับ withdraw แรก | Merchant |

ขั้นตอน onboarding แบบเป็นลำดับดูที่ [Integration Guide](integration-guide.md)
