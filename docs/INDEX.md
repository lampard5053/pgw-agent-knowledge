# INDEX — สารบัญค้นหาไว สำหรับ payment-gateway-knowledge

> ใช้ไฟล์นี้เป็นจุดเริ่มค้นก่อนเสมอ: หาหัวข้อที่ใกล้เคียงคำถาม แล้วเปิดไฟล์ที่ชี้ไปเท่านั้น ไม่ต้องเปิดทุกไฟล์

## เอกสารระดับบนสุด

- `index.md` — PGW Documentation
- `open-questions.md` — Open Questions — ต้องยืนยันกับทีมพัฒนา
- `pricing-partners.md` — เงื่อนไขการขายและ Partner
- `downloads.md` — ดาวน์โหลด PDF
- `case-log.md` — **เคสจริงจากบทสนทนา (สะสมต่อเนื่อง)** — เช็คไฟล์นี้เสมอเมื่อคำถามเกี่ยวกับปัญหา/บั๊ก/ฟิลด์ที่ระบบจริงมีแต่เอกสารสำรวจยังไม่ทัน
- `contradictions-log.md` — **จุดที่ข้อมูลขัดแย้งกันเอง (สะสมต่อเนื่อง)** — เช็คไฟล์นี้ก่อนฟันธงคำตอบเสมอ ถ้าเจอสองแหล่งพูดไม่ตรงกัน อย่าเลือกเชื่อฝั่งเดียวเงียบๆ

## opsdash/ — OpsDash (Bank Adapter) — จัดการบัญชีธนาคาร, บอท, job, transaction ดิบ

- `opsdash/bank-accounts.md` — Bank Account
- `opsdash/bank-onboarding-sop.md` — SOP การผูกบัญชีธนาคารใหม่
- `opsdash/bots.md` — Bot
- `opsdash/clients.md` — Client
- `opsdash/company.md` — Company
- `opsdash/dashboard.md` — Dashboard
- `opsdash/index.md` — OpsDash — ภาพรวม
- `opsdash/jobs.md` — Job
- `opsdash/onboard.md` — Onboard
- `opsdash/transactions.md` — Transaction

## watchtower/ — Watchtower — มอนิเตอร์สุขภาพบัญชี, transaction ย้อนหลัง, sync, groups, reports

- `watchtower/accounts.md` — Accounts
- `watchtower/dashboard.md` — Dashboard
- `watchtower/direct-sync.md` — Direct Sync
- `watchtower/flow.md` — Flow
- `watchtower/groups.md` — Groups (Account Groups)
- `watchtower/index.md` — Watchtower — ภาพรวม
- `watchtower/jobs.md` — Jobs
- `watchtower/notifications.md` — Notifications (Notification Channels)
- `watchtower/reports.md` — Reports
- `watchtower/transactions.md` — Transactions

## console/ — Console (ต่อแบรนด์) — Company/Seller/Merchant/Payment/Match Type/Slip Verification

- `console/dashboard.md` — Dashboard
- `console/index.md` — Admin Console — ภาพรวม
- `console/match-type.md` — Deep-dive: Match Type
- `console/slip-verification.md` — Slip Verification
- `console/seller/fee-dashboard.md` — Fee Dashboard (ระดับ Seller)
- `console/seller/index.md` — Seller — ภาพรวม
- `console/seller/mdr-templates.md` — Seller MDR Templates
- `console/seller/sellers.md` — Sellers
- `console/payment/callback-logs.md` — Callback Logs
- `console/payment/channels.md` — Payment Channels
- `console/payment/config.md` — Payment Config (Company Configuration)
- `console/payment/index.md` — Payment — ภาพรวม
- `console/payment/pools.md` — Payment Pools
- `console/payment/transactions.md` — Transactions
- `console/company/ban-accounts.md` — Ban Accounts
- `console/company/fee-accounts.md` — Fee Accounts
- `console/company/index.md` — Company — ภาพรวม
- `console/company/internal-accounts.md` — Internal Accounts
- `console/company/roles.md` — Company Roles
- `console/company/users.md` — Company Users
- `console/merchant/accounts.md` — Merchant Accounts
- `console/merchant/customers.md` — Merchant Customers
- `console/merchant/index.md` — Merchant — ภาพรวม
- `console/merchant/merchants.md` — Merchants
- `console/merchant/roles.md` — Merchant Roles
- `console/merchant/users.md` — Merchant Users

## pgway/ — PGWay — ระบบจัดการอุปกรณ์/มือถือจริงที่รันบอท (device farm)

- `pgway/admin.md` — Admin (Token Management)
- `pgway/agent-devices.md` — Agent Devices
- `pgway/balance-checks.md` — Balance Checks
- `pgway/dashboard.md` — Dashboard
- `pgway/devices.md` — Devices (Connected Devices)
- `pgway/index.md` — PGWay (Device Monitoring) — ภาพรวม
- `pgway/phone-status.md` — Phone Status
- `pgway/test-logs.md` — Test Logs

## support/ — Support — playbook เคสจริง, common cases, status reference

- `support/case-playbooks.md` — Playbook แก้ปัญหาแบบ step-by-step
- `support/common-cases.md` — เคสที่พบบ่อย
- `support/team-operations.md` — **Operations & Support — The Human Side of Running PGW** (คนละแหล่งข้อมูลจากไฟล์อื่น — org chart, shift, JD, Manual Credit Adjustment, Discrepancy Tracking, KB Pipeline — เช็คไฟล์นี้เมื่อคำถามเกี่ยวกับ "คน/ทีม/process" ไม่ใช่ตัวระบบ)
- `support/index.md` — ทีม Support — วิธีการทำงาน
- `support/related-links.md` — ลิงก์ที่เกี่ยวข้อง
- `support/status-reference.md` — Transaction Status Reference

## api/ — API — reference, integration guide, roles, transaction flows, FAQ

- `api/faq.md` — FAQ
- `api/index.md` — PGW API — ภาพรวม (Merchant-facing)
- `api/integration-guide.md` — Integration Guide
- `api/reference.md` — API Reference
- `api/roles.md` — Roles & Hierarchy
- `api/transaction-flows.md` — Transaction Flows
