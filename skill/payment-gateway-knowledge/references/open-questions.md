# Open Questions — ต้องยืนยันกับทีมพัฒนา

รวบรวมทุกจุดที่เอกสารนี้ยังไม่สามารถยืนยันได้ 100% จากการสำรวจ UI เพียงอย่างเดียว

## 🔴 กระทบโครงสร้างเอกสารโดยตรง

1. **Relay Deposit / Relay Withdraw คืออะไร?**
   เจอใน Transaction Type filter ([Payment → Transactions](console/payment/transactions.md)) แต่ไม่เคยเห็นตัวอย่างจริง สมมติฐาน: อาจเป็นการโยกเงินภายในระหว่าง Payment Channel ในพูล

2. **Callback Log Action "transfer" และ "payment" คืออะไร?**
   เจอใน filter ของ [Callback Logs](console/payment/callback-logs.md) — ต่างจาก Transaction Type ที่เจอในหน้า Transactions อีกชุดหนึ่ง ใช้ต่างบริบทกันยังไง?

## 🟡 เสริมความสมบูรณ์

3. **Match Type (Digits vs Balance) — ยืนยันกลไกที่วิเคราะห์ไว้ถูกไหม?**
   ดูการวิเคราะห์เต็มใน [Deep-dive: Match Type](console/match-type.md) — มีหลักฐานสดใหม่ที่ตรงกันดีมากจาก [OpsDash → Transaction](opsdash/transactions.md) (raw data `deposit: 4000` จำนวนเต็ม vs `ledgerBalance: 286100.26` มีทศนิยม) แต่กลไก polling/คำนวณส่วนต่างระดับ logic จริงยังไม่ยืนยัน

4. **Merchant Accounts สัมพันธ์กับ Payment Pool/Channel ยังไง?**
   ดู [Merchant Accounts](console/merchant/accounts.md) — อาจเกี่ยวกับ Account Group "pool-aix" ที่เจอใน [Watchtower → Groups](watchtower/groups.md) (ดูข้อ 23) แต่ยังไม่มีหลักฐานเชื่อมโยงตรง

5. **Master Account structure (KTB Biz `accountLevel: "MA"`) เกี่ยวกับ Pool/Channel ไหม?**
   พบใน raw data ของ Bank Adapter OpsDash — ยืนยันซ้ำอีกครั้งจาก [OpsDash → Transaction](opsdash/transactions.md) ว่าฟิลด์ `isMasterAccount`/`masterAccountNo`/`accountLevel: "MA"` มีอยู่จริงในระดับ REST API ของ KTB Biznext Channel แต่ยังไม่ยืนยันว่ากระทบการรวม Payment Pool ฝั่ง Admin Console โดยตรงหรือไม่

6. **เลขบัญชีผู้โอนที่ mask ในสลิป กระทบ fallback matching / Ban Accounts ไหม?**

7. **Internal Accounts ใช้เก็บอะไรกันแน่?**
   ดู [Internal Accounts](console/company/internal-accounts.md) — ยังไม่มีตัวอย่างข้อมูลจริง

8. **Slip Verifier Provider มีตัวเลือกอะไรบ้าง?**
   ดู [Slip Verification](console/slip-verification.md)

9. **Selection Strategy ของ Payment Pool ทำงานยังไงจริงๆ ในระดับ logic?**
   ดู [Payment Pools](console/payment/pools.md)

## 🔵 จาก OpsDash (ระบบใหม่)

18. **"KBIZ ใช้ Bot, KTBBIZ ใช้ REST API" generalize ไปถึง Kind อื่น (SCBBIZ, SCB, BBL, PGWM) ได้ไหม?**
    ยืนยันแล้ว 3 กลุ่มจากหลักฐานข้าม 3 ระบบ: **KTBBIZ = REST API จริง** (ไม่มี Job ID, raw data ตรง schema `biznext-channel` — ดู [OpsDash → Transaction](opsdash/transactions.md)), **KBIZ และ KBANK/KPLUS = Bot/Appium ผ่านอุปกรณ์จริง** (มี Job ID + raw log — ดู [OpsDash → Job](opsdash/jobs.md) และ [PGWay → Phone Status](pgway/phone-status.md)) — แต่ **SCBBIZ, SCB (ส่วนบุคคล), BBL, PGWM ยังไม่มีหลักฐานยืนยันฝั่งไหนเลย** ทั้งไม่เคยเห็น raw data แบบ REST API และไม่เคยเห็นใน [PGWay → Agent Devices](pgway/agent-devices.md) ด้วย (รองรับแค่ BBL, GSB, KBANK, KBIZ — แต่ยังไม่เคยเห็นตัวอย่าง BBL จริงใช้งาน) — ยังเป็นคำถามค้างสำหรับ 4 Kind นี้

    พบหลักฐานชวนสงสัยเพิ่มที่ [Watchtower → Direct Sync](watchtower/direct-sync.md): KBIZ ก็อยู่ในรายชื่อบัญชีที่ "pull จาก bank's API" ได้ด้วย ทั้งที่ควรใช้ Bot ล้วน — อาจมี API บางส่วนสำหรับ KBIZ ที่ยังไม่เคยเห็นมาก่อน

19. **Transfer Events (86,499) ใน OpsDash Dashboard ต่างจาก Transaction Count (338,610) มาก — คืออะไรกันแน่?**
    ดู [OpsDash → Dashboard](opsdash/dashboard.md)

20. **Client tier "Onboarder" หายไปจาก Tier dropdown ตอน Edit (โชว์ placeholder ว่าง) ทั้งที่ list แสดง Tier ถูกต้อง — UI bug หรือ tier คนละชุด?**
    ดู [OpsDash → Client](opsdash/clients.md)

21. **Proxy (Webshare.io, TH-rotate) ครอบคลุม traffic ทั้งระบบหรือเฉพาะบางส่วน และมี fallback ยังไงถ้า proxy ที่ active ใช้ไม่ได้?**
    ดู [OpsDash → Company](opsdash/company.md) — เพิ่มสมมติฐานที่แคบลงหลังเห็น [PGWay → Devices](pgway/devices.md): อุปกรณ์ bot เชื่อมต่อผ่าน IP เครือข่ายภายใน (`192.168.x.x`) คือมือถือจริงที่ต่อ WiFi/network ในสถานที่จริง ไม่จำเป็นต้องพรางผ่าน proxy เลย (เพราะเป็น traffic จากมือถือจริงอยู่แล้ว) — **คาดว่า** proxy Webshare.io น่าจะใช้เฉพาะฝั่ง REST API ที่เซิร์ฟเวอร์เรียกตรง (เช่น KTB Biznext Channel) ไม่ใช่ทุก component แต่ยังไม่ยืนยัน 100%

## 🟣 จาก Watchtower (ระบบใหม่)

22. **Status "Submitted" ที่เจอในหน้า Reports คืออะไร — ไม่ตรงกับ State enum ของทั้ง Admin Console, OpsDash, หรือ Watchtower → Transactions เลย**
    ดู [Watchtower → Reports](watchtower/reports.md)

23. **Group "pool-aix" เกี่ยวกับ merchant "aixStore3" ที่เคยเห็นใน Merchant Accounts ฝั่ง Admin Console ไหม?**
    ดู [Watchtower → Groups](watchtower/groups.md) เทียบกับ [Merchant Accounts](console/merchant/accounts.md)

24. **รายชื่อธนาคารเต็มใน Watchtower → Transactions ถูกตัดตรง "GHB" — มีธนาคารอื่นต่ออีกไหม?**
    ดู [Watchtower → Transactions](watchtower/transactions.md)

25. **เมนู "Analytics" ของ Watchtower ยังไม่ได้สำรวจเลย — มีอะไรอยู่ในนั้นบ้าง?**
    ดู [Watchtower — ภาพรวม](watchtower/index.md)

26. **Watchtower เชื่อมกับข้อมูล OpsDash ผ่านกลไก sync แบบไหนกันแน่ในระดับ infra?**
    ยืนยันแล้วว่าเป็นข้อมูลชุดเดียวกัน (เลขบัญชี/Job Ref ตรงกันเป๊ะ) แต่ไม่รู้ว่า sync ผ่าน [Direct Sync](watchtower/direct-sync.md) เพียงอย่างเดียว หรือมีกลไก background sync อื่นอีก — **สมมติฐานใหม่หลังเห็น [PGWay → Devices](pgway/devices.md):** background job ชื่อ **"Balance Poller"** ใน PGWay (round-robin รีเฟรช balance ทีละเครื่องทุก 300 วินาที) น่าจะเป็นกลไกจริงที่ทำให้ตัวเลข balance สดใหม่ที่เห็นทั้งใน OpsDash และ Watchtower ตรงกัน (timestamp "Xm ago" ที่เห็นซ้ำๆ ทุกที่) — **คาดว่า** OpsDash/Watchtower/PGWay อ่านจากฐานข้อมูลกลางเดียวกัน โดย PGWay เป็นตัวที่ทำหน้าที่ poll ข้อมูลสดจากอุปกรณ์จริงเข้าฐานข้อมูลนั้น ยังไม่ยืนยัน 100%

## 🟠 จาก PGWay / Device Monitoring (ระบบใหม่)

27. **"Verify LINE" / test type "line-verify" มีกลไกที่แท้จริงยังไง?**
    ดู [PGWay → Devices](pgway/devices.md) และ [PGWay → Test Logs](pgway/test-logs.md) — คาดว่าเกี่ยวกับ OTP/แจ้งเตือนผ่าน LINE แต่ยังไม่ยืนยัน

28. **ทำไม Test ID ของ Balance Checks เป็น hash สั้น 8 ตัวอักษร ต่างจาก UUID ยาวของ Transfer test?**
    ดู [PGWay → Balance Checks](pgway/balance-checks.md)

29. **"Agent Devices" (enroll ผ่าน QR) กับหน้า "Devices" หลัก เป็นคนละระบบลงทะเบียนกันหรือช่องทางเสริม?**
    ตอนสำรวจพบ "No devices registered yet" ทั้งที่หน้า Devices มี 89 เครื่องแล้ว ดู [PGWay → Agent Devices](pgway/agent-devices.md)

30. **Username "orga" ที่เจอใน PGWay Admin คือบัญชีเดียวกับ client "orga"/"Orga 888" ที่เจอใน OpsDash และ Watchtower ไหม?**
    ดู [PGWay → Admin](pgway/admin.md) — ชื่อ "orga" ปรากฏสอดคล้องกันถึง 3 ระบบอิสระ (OpsDash client Tier=Owner, Watchtower instance, PGWay user) ทำให้ **มั่นใจมากขึ้นว่าเป็น identity เดียวกัน** ที่ใช้เป็นชื่ออ้างอิงกลางข้ามเครื่องมือ internal ทั้งหมด — แต่ยังเป็นการอนุมานจากชื่อที่ตรงกันเท่านั้น ไม่มี ID ที่เชื่อมกันโดยตรงให้ยืนยัน 100%

51. **"pinkman" ที่เจอใน SOP ผูกบัญชี คือทีม/role เดียวกับ "PM"/"TS" ที่เจอในฝั่ง Support (Telegram) ไหม?**
    ดู [OpsDash → SOP: ผูกบัญชีธนาคารใหม่](opsdash/bank-onboarding-sop.md) — เป็นคนให้ IP อุปกรณ์และดูแลการ setup อุปกรณ์จริง

52. **SOP ผูกบัญชีธนาคารไม่ผ่านหน้า Onboard (staging) เลย — flow staging สงวนไว้สำหรับกรณีไหนกันแน่?**
    ดู [OpsDash → Onboard](opsdash/onboard.md) และ [SOP: ผูกบัญชีธนาคารใหม่](opsdash/bank-onboarding-sop.md) — คาดว่าอาจสงวนไว้สำหรับ client `onboarder` ที่ไม่มีสิทธิ์สร้างบัญชีจริงตรงๆ ยังไม่ยืนยัน 100%

## 🟤 จากทีม Support (ระบบใหม่)

32. **รูปแบบชื่อห้อง `[JoinPay][Support] {รหัสร้านค้า}` ใช้ชื่อ "JoinPay" คงที่เสมอ หรือเปลี่ยนตามพาร์ทเนอร์อื่น (เช่น NextPay)?**
    ดู [ทีม Support](support/index.md)

33. **`/md` เป็นคำสั่งเดียวที่ทีม Support ใช้ หรือมีคำสั่งอื่นสำหรับเคสประเภทอื่น?**
    ดู [ทีม Support](support/index.md)

34. **field "เจ้าหน้าที่ร้านค้า: null" ในสรุป ticket เป็นค่าว่างเสมอ หรือบางครั้งมีค่า — ใช้ระบุอะไร?**
    ดู [ทีม Support](support/index.md)

35. **`[PM]` และ `[TS]` ใน Telegram handle เจ้าหน้าที่ (เช่น `@[PM][TS]-Lady Sherry-`) ย่อมาจากอะไร มี role อื่นอีกไหม?**
    ดู [ทีม Support](support/index.md)

38. **"Deposit TX" ใน Transaction Troubleshooting dump ชี้ไปที่ ID ที่ค้นหาในหน้า Transactions ไม่เจอเลย — เป็น PreTransaction ID (คนละคอลเลกชัน) ใช่ไหม?**
    ค้นหาด้วย UUID เต็มแล้ว "No transactions match" — คาดว่าเป็น PreTransaction ไม่ใช่ Transaction แต่ยังไม่เจอหน้าที่ list PreTransaction โดยตรงมายืนยัน ดู [เคสที่ 2](support/common-cases.md#เคสที่-2)

39. **"Sale Fee" ที่เห็นใน Transaction Troubleshooting dump คือตัวเดียวกับ "Seller Fee" ที่เคยเจอใน Admin Console ไหม?**
    Total Fee = Platform Fee + Sale Fee ในตัวอย่างที่เห็น ดู [เคสที่ 2](support/common-cases.md#เคสที่-2)

40. **field "Callback" เป็น epoch zero (`1970-01-01...`) แปลว่า merchant ไม่เคยได้รับ webhook แจ้งเตือนสำหรับเคส Manual Success เลยใช่ไหม?**
    ต่างจาก field "Payment Callback" ที่มีเวลาแล้ว — ถ้าจริง หมายความว่า merchant อาจไม่รู้ว่าได้เงินจนกว่าจะเข้ามาเช็ค balance เอง ดู [เคสที่ 2](support/common-cases.md#เคสที่-2)

42. **order ID เดียวกัน (`D58X50435TBGUZOOJ5C3`) ปรากฏทั้งใน transaction "Deposit/Expired" และ "Unknown Deposit/Manual_success" — เกี่ยวข้องกันจริงไหม (ลูกค้าโอนผิดยอด/ผิดบัญชีจากคำขอเดิม)?**
    เสริมหลักฐานแล้วว่า UUID ใน `/md` ชี้ไปที่ transaction "Deposit/Expired" ตัวเดิม ไม่ใช่ Unknown Deposit โดยตรง — สนับสนุนสมมติฐานนี้มาก แต่ยังไม่ยืนยัน mechanism 100% ดู [เคสที่ 2](support/common-cases.md#เคสที่-2)

43. **ฟอร์ม "Match Transaction" ให้เลือกแค่ Merchant ไม่มีช่องเลือก pre-transaction เฉพาะเจาะจง — ระบบจับคู่กับ pre-transaction ยังไงเบื้องหลัง?**
    ดู [Admin Console → Transactions](console/payment/transactions.md#match-transaction)

44. **"Manual Failed" เกิดขึ้นตอนไหน — ยังไม่เคยเห็นตัวอย่างจริงเลยแม้แต่ครั้งเดียว**
    ดู [Transaction Status Reference](support/status-reference.md)

45. **เลขบัญชีปลายทางที่ยาว/ซ้ำผิดปกติ (`13881353831388...`) ทำให้ Job ของ Withdraw ไม่เคยถูก dispatch เลยจริงไหม?**
    ยังไม่ได้เปิด Payment Receipt ดูเลขเต็มมายืนยัน ดู [เคสที่ 3](support/common-cases.md#เคสที่-3)

46. **"Unknown Deposit"/"Unknown Withdraw" มีกลไกหมดอายุ/เก็บกวาดอัตโนมัติไหม หรือค้างตลอดไปจนกว่าจะมีคนแก้ด้วยมือ?**
    ดู [Transaction Status Reference](support/status-reference.md#เจาะลึก-unmatched)

47. **"Unknown Withdraw" เกิดจากอะไรกันแน่ — เป็นการถอนจริงที่รอจับคู่ merchant หรือเป็นการโยกเงินภายในของ ops เอง?**
    ดู [Transaction Status Reference](support/status-reference.md#เจาะลึก-unmatched)

53. **"PGWay" ในเอกสาร "Support Guideline for PGWay" หมายถึงอะไรกันแน่ — ชื่อ partner/instance หรือชื่อเรียกฝั่ง Bank Adapter?**
    หน้าจอที่แนบมาในเอกสารเป็นหน้าตา Bank Adapter/OpsDash ไม่ใช่ระบบ [PGWay (Device Monitoring)](pgway/index.md) ที่เคยสำรวจไว้ — ดู [Playbook แก้ปัญหา](support/case-playbooks.md)

54. **เคส "ยอดถอนไม่ออก ดูจาก Job ขึ้น" (status Created ค้าง) ควรมีขั้นตอนแก้ยังไง?**
    เอกสารต้นฉบับตัดจบตรงหัวข้อพอดี ไม่มีขั้นตอนตามมา ดู [Playbook แก้ปัญหา](support/case-playbooks.md)

55. **"WayPay" คือแบรนด์เดียวกับ "PGWay"/"payways.io" ที่เจอในฝั่งเทคนิคจริงไหม?**
    ดู [เงื่อนไขการขายและ Partner](pricing-partners.md)

56. **ค่าธรรมเนียม % ในหน้า Pricing สัมพันธ์กับ Platform Fee/Seller Fee/Payment Fee ที่เคยยืนยันไว้ยังไง?**
    ดู [เงื่อนไขการขายและ Partner](pricing-partners.md)

## 🟢 จากเอกสาร OpenAPI ต้นฉบับ (ค้างมาแต่แรก)

10. P2P checkout URL หมดอายุเมื่อไหร่?
11. พฤติกรรม unknown_deposit เมื่อ customer ถูก ban ณ เวลา match เป็นยังไง?
12. Settlement cut-off time และ timezone ที่ใช้คืออะไร?
13. Dispute/refund process policy สำหรับ chargeback เป็นยังไง?
14. ค่า fee tolerance ที่ธนาคารปลายทาง accept (±สตางค์) คือเท่าไหร่?
48. **`GET /transaction/deposit/p2p-limit` และ `GET /transaction/withdraw/p2p-limit` ใช้งานจริงยังไง — ยังไม่เคยเห็นตัวอย่าง response**
    มีอยู่จริงใน `api-1.json` แต่ไม่มีใน `pgw_service.html` เดิมเลย ดู [API Reference → Balance & Limit](api/reference.md)
49. **"Partner Fee" ที่ `pgw_service.html` อธิบายไว้ (3-tier: Platform/Partner/Seller) มีอยู่จริงในระบบไหม?**
    ขัดแย้งกับที่เคยยืนยันแล้วจากฝั่ง Console ว่าใช้แค่ Platform Fee/Seller Fee/Payment Fee — ดู [Roles & Hierarchy](api/roles.md)
50. **P2P rail (`paymentChannelType: "p2p"`) มีพฤติกรรม callback ต่างจากปกติยังไงในทางปฏิบัติจริง?**
    Spec อธิบายไว้ละเอียด (amount อาจน้อยกว่าที่ขอ, correlate ด้วย uuid แทน, callback ล่าสุด supersede ของเก่า) แต่ยังไม่เคยเห็นตัวอย่างการใช้งานจริงจากทุกระบบที่สำรวจมา ดู [API Reference → Webhook](api/reference.md)

## ✅ คำถามที่ตอบได้แล้วระหว่างทาง (สรุปไว้เพื่ออ้างอิง)

| คำถามเดิม | คำตอบที่ได้ |
|---|---|
| Balance ใน webhook เป็น merchant wallet หลัง transaction หรือ real-time? | เป็น balance **หลัง** transaction นั้นเป๊ะ |
| Retry logic ของ webhook กี่ครั้ง? interval เท่าไหร่? | retry ได้เรื่อยๆ ไม่มี limit ตายตัว จนกว่าจะสำเร็จ |
| Partner Fee มีจริงไหม? | ไม่มี — ระบบใช้ Platform Fee / Seller Fee / Payment Fee เท่านั้น |
| unknown_deposit มี webhook แจ้ง merchant ไหม? | มี — ยิง callback ทันทีพร้อม `type: unknown_deposit` |
| Seller ↔ Merchant เป็นความสัมพันธ์แบบไหน? | 1 Seller : หลาย Merchant (ยืนยันจากตัวอย่างจริง 1 seller มี 43 merchant) |
| Payment Pool ↔ Merchant เป็นความสัมพันธ์แบบไหน? | 1 Pool : หลาย Merchant (ยืนยันจากตัวอย่างจริง pool "shared" มี 43 merchant) |
| "PGWM" ในเอกสาร OpenAPI เดิม คือบัญชีรับเงินบัญชีเดียวจริงไหม? | ไม่ใช่ — PGWM เป็นแค่ 1 ใน 8 "Kind" ของบัญชีธนาคารใน [OpsDash → Bank Account](opsdash/bank-accounts.md) (ร่วมกับ KBIZ/KTBBIZ/SCBBIZ/GSB/KPLUS/SCB/BBL) ไม่ใช่บัญชีเดียวที่ยืนหนึ่งอย่างที่เอกสารเดิมเข้าใจ |
| KBIZ กับ KTBBIZ ใช้กลไกเดียวกันไหม (business API vs bot)? | ไม่เหมือนกัน — KTBBIZ มี REST API ทางการจริง (`biznext-channel`, ไม่มี Job ID), ส่วน KBIZ ใช้ Bot/Appium ควบคุมมือถือจริง (มี Job ID + raw log) ดู [OpsDash → Job](opsdash/jobs.md) |
| NextPay คือลูกค้าอีกรายของ PGW Provider เจ้าเดียวกับ JoinPay หรือคนละบริษัทที่ไม่เกี่ยวข้องกัน? | เป็น **Partner กันจริง** — [Watchtower → Groups](watchtower/groups.md) มี group "NextPayXJoinPay" ระบุตรงๆ ว่า "เป็นบัญชีที่ทาง NextPay นำมาเปิด Partner ในฝั่งของ JoinPay" พร้อมพนักงาน NextPay จริง (อีเมล `@nextpay.app`/`@nextpay.biz`) มีสิทธิ์ Operator เข้าถึงฝั่ง JoinPay ได้ |
| กรณีลูกค้าคนเดียวลงทะเบียนกับ 2 merchant ด้วยบัญชีธนาคารเดียวกัน แอดมินรู้ได้ยังไงว่าเงินตั้งใจเติมให้ merchant ไหน? | **รู้จากห้อง Telegram ที่ลูกค้าเลือกส่งสลิปเข้ามาโดยตรง** (เช่น `[JoinPay][Support] br-5588`) — ไม่ใช่ระบบเดา/คำนวณเอง แต่เป็นบริบทจากช่องทางที่ลูกค้าติดต่อเข้ามาเอง ยืนยันจาก screenshot จริงของเคส "จรวยพร เวียงสมุทร" ดู [เคสที่ 2](support/common-cases.md#เคสที่-2) |
| "More Details" ใน Payment Receipt modal คือ Settle Amount หลังหักค่าธรรมเนียมใช่ไหม? | **ใช่ ยืนยันแล้ว** — Transaction Troubleshooting dump ของอีก transaction แสดง `Settle Amount: ฿1,465.50` ตรงกับสูตร Amount − Total Fee เป๊ะ ("More Details" คือ Settle Amount ตัวเดียวกัน แค่ย่อ) ดู [Transaction Status Reference](support/status-reference.md) |
| "Manual Success" เกิดขึ้นตอนไหนกันแน่ — ต่างจาก "Match" ยังไง? | **Manual Success ใช้เมื่อไม่มี pre-transaction ให้จับคู่แบบ formal** (มักเกิดกับบัญชีที่ใช้ร่วมกันหลายร้านค้า เจ้าหน้าที่ตัดสินใจเองว่าเงินเป็นของร้านไหนจากบริบทนอกระบบ) — **Type ยังคงเป็น "Unknown Deposit" ตลอดไป** มีแค่ Status เปลี่ยนเป็น Manual_success ต่างจาก Match ที่ Type เปลี่ยนเป็น "Deposit" ปกติ ดู [Transaction Status Reference](support/status-reference.md#match-vs-manual-success) |
| Entity "Bot" (OpsDash) กับ field "Device ID/Appium Bot URL/PIN" (Bank Account) ซ้ำซ้อนกันไหม? | ไม่ซ้ำซ้อน — เป็นคนละ layer จริง **[PGWay](pgway/index.md) คือระบบเต็มที่บริหารอุปกรณ์จริง** (WDA Signing Teams, Setup/Run actions, health monitoring) ส่วน "Bot" ใน OpsDash และ field ใน Bank Account เป็นแค่**ข้อมูลอ้างอิงชุดเล็กที่ OpsDash เก็บไว้เพื่อรู้ว่าจะสั่ง Job ไปที่อุปกรณ์ไหน** ไม่ได้บริหารอุปกรณ์เต็มรูปแบบเอง |
| "KBANK"/"KPLUS"/"KBNK" ที่เจอกระจายอยู่หลายที่ คือตัวเดียวกันไหม? | **ใช่ — ทั้งหมดคือ Kasikornbank บัญชีส่วนบุคคล ผ่านแอพ K PLUS** ตัวเดียวกัน แค่คนละระบบเรียกชื่อไม่ตรงกัน: `KBNK` = bank code (ใช้ในคอลัมน์ From/To ของ Transaction), `KPLUS` = ชื่อ Kind ใน [OpsDash → Bank Account](opsdash/bank-accounts.md), `KBANK` = ชื่อ Kind ที่ใช้ใน [PGWay](pgway/index.md) (device app: kbank, แอพ K PLUS) — **คนละชื่อกับ `KBIZ`** ซึ่งคือ Kasikornbank ฝั่ง **Business** (แอพ/กลไกอัตโนมัติคนละชุดกันโดยสิ้นเชิง) |
| HW กับ JoinPay profile ใน OpsDash ชี้ endpoint เดียวกัน (`bank-adapter-api.joinpay.app`) — สัมพันธ์กันยังไง? | **เป็น profile เดียวกัน/entity เดียวกัน** — ยืนยันโดยผู้ใช้ระบบโดยตรง ไม่ใช่คนละบัญชี/คนละสิทธิ์อย่างที่เคยสงสัยไว้ (สอดคล้องกับที่ [Watchtower](watchtower/index.md) ไม่มี "HW" แยกอยู่ในรายชื่อ 11 instance เลย เพราะเป็นแค่อีกชื่อของ JoinPay) |
| PGWay อยู่ใต้โดเมนอะไร? | **`controller.pgway.work`** — Portainer CE instance ที่จัดการ container ของอุปกรณ์บอททั้งหมด ยืนยันจาก SOP ผูกบัญชีจริง ดู [OpsDash → SOP: ผูกบัญชีธนาคารใหม่](opsdash/bank-onboarding-sop.md) |
| KTBBIZ กับ KBIZ ต่างกันตรงไหนในแง่ขั้นตอนตั้งค่าจริง? | **ยืนยันชัดเจนจาก SOP จริง** — KTBBIZ จบด้วยฟอร์มเดียว ("Register Device") ไม่มีขั้นตอนสร้าง Bot เลย ส่วน KBIZ ต้องสร้าง Bot object แยก แล้ว deploy container ผ่าน Portainer เต็มรูปแบบ (volume, env vars, adb connect) ตรงกับที่เคยสังเกตว่า KTBBIZ ไม่มี Job ID ส่วน KBIZ มี ดู [SOP: ผูกบัญชีธนาคารใหม่](opsdash/bank-onboarding-sop.md) |

## ฝั่งปฏิบัติการ (Operations / Support Team) — จาก team-operations.md

> คนละแหล่งข้อมูลจากหัวข้อด้านบน (มาจากบทสนทนาออกแบบระบบ task management ให้ทีม ไม่ใช่จากการสำรวจ mkdocs) ดูรายละเอียดเต็มที่ [Team Operations](support/team-operations.md)

51. **ความหมายจริงของ "Tier1" ใน JD ของ Support (Tier 2)** — เอกสารพูดถึง Tier1 เป็นทั้งที่ Support ส่ง escalate ขึ้นไป และเป็นที่ Tier 2 รับช่วงงานแทนเอง — สองประโยคขัดกันเรื่องทิศทาง ต้องยืนยันว่า role ที่มี JD 20 ข้อนี้คือ Tier 2 จริงไหม
52. **ตำแหน่ง Regal Support อยู่ตรงไหนใน org chart 8 ทีม (Payment Internal Team)**
53. **JD ของ Tier 3 (Engineering Specialist) และ Level 4 (Executive Approval)** — รายละเอียดยังไม่ได้รับ
54. **Tier 3 กับ 8 ทีมย่อยของ Payment Internal Team** — เป็นกลุ่มเดียวกันหรือคนละสาย
55. **สถานะ Manual Intervention Log ใน Notion** — เคยสร้างไว้แต่หายไปจาก workspace ล่าสุด ไม่แน่ใจว่าถูกลบหรือย้ายรวมกับตารางอื่น
56. **ความสัมพันธ์ Account Team (org chart 3 ทีมย่อย) กับ Account Team (Account Support + Regal Support)** — อาจเป็นเอกสารคนละชุดที่อธิบายทีมเดียวกัน ยังไม่ merge ให้ชัด
