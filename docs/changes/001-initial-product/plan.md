# Implementation Plan: ระบบจองตั๋วสำหรับงานแบบระบุที่นั่งและแบบจำนวนตั๋ว

Change: 001-initial-product
Status: Draft
Accepted by: Pending
Accepted on: Pending
Acceptance evidence: Pending
Intent: [intent.md](intent.md) — Accepted, 2026-11-23
Specification: [spec.md](spec.md) — Accepted, 2026-11-24

## เป้าหมาย

สร้าง MVP แนวตั้งที่ UI ของ Next.js ใช้งานกับ API ของ NestJS และ PostgreSQL ได้จริง ครอบคลุม Owner/Customer, Event สองโหมด, inventory, reservation 15 นาที, mock payment ผ่าน QR, Dashboard, การยกเลิกและสถานะคืนเงินแบบ Manual, MinIO และการทดสอบตาม Specification

Plan นี้เป็นข้อเสนอทางเทคนิคสำหรับรีวิวก่อน Build การยอมรับ Specification ไม่ได้หมายถึงการยอมรับทางเลือกสถาปัตยกรรมในเอกสารนี้

## โครงสร้าง repository ที่เสนอ

```text
apps/
  web/                 Next.js UI สำหรับ Owner และ Customer
  api/                 NestJS modular monolith และ REST API
packages/
  contracts/           TypeScript API schemas/types ที่จำเป็นต่อการเชื่อม UI/API
docs/
  changes/001-initial-product/
```

ใช้ pnpm workspace ขั้นต้นเพื่อจัดการ web/api ใน monorepo; ยังไม่เพิ่ม Turborepo หรือระบบ build orchestration จนกว่าจะมีเหตุผลด้าน build time. เริ่มด้วย API contract แบบ REST และ validation schema ที่แชร์เท่าที่จำเป็น; หลีกเลี่ยง package กลางที่เป็น abstraction กว้างเกิน MVP. โครงสร้าง NestJS ภายใน `apps/api` ใช้ feature modules ได้แก่ Identity (แยก Owner/Customer), Events, VenueLayouts, Inventory/Reservations, MockPayments, RefundStatus, Dashboard และ Files

## ข้อเสนอทางเทคนิคและ trade-offs

| เรื่อง | ข้อเสนอ | เหตุผลและ trade-off |
| --- | --- | --- |
| CSS component | shadcn/ui เฉพาะ component ที่ใช้จริง ร่วมกับ Tailwind และ lucide-react | เข้ากับ Tailwind และเพิ่ม component ลงใน repository ให้ปรับได้; ต้องดูแล source component เองและอาจดึง dependencies เพิ่มตาม component ที่เลือก |
| Workspace | pnpm workspaces แบบพื้นฐาน | แยก web/api และจัดการ dependencies ใน monorepo; ต้องใช้ pnpm ในการพัฒนา/CI |
| Backend | NestJS modular monolith | ขอบเขตธุรกิจแยก module ได้แต่ deploy/process เดียว เข้าใจง่ายและเหมาะกับ R&D |
| Database access | เปรียบเทียบ Prisma กับ SQL-first (เช่น Kysely/node-postgres) ก่อนเลือก; เอนเอียงไปทาง SQL-first สำหรับ transaction/inventory ที่ต้องเห็น SQL และ lock behavior ชัด | SQL-first ทำให้เรียนรู้ transaction และ PostgreSQL โดยตรง แต่ต้องเขียน mapping/migrations เพิ่ม; Prisma ลดงาน CRUD แต่เงื่อนไข locking/SQL เฉพาะอาจต้องใช้ raw SQL |
| Auth realms | แยก Owner และ Customer เป็นขอบเขต identity/session ชัดเจนและตรวจ realm ในทุก API | เพิ่มงาน auth/config และ Google callback สองเส้นทาง แต่สอดคล้องกับข้อกำหนดห้ามข้ามบทบาท; รายละเอียดการแยก storage ต้อง review ก่อน Build |
| Inventory | ใช้ PostgreSQL เป็น source of truth; transaction และ constraint/conditional update เป็นตัวคุม capacity/seat uniqueness | ลด oversell โดยไม่เพิ่ม Redis; contention อาจทำให้บางคำขอรอหรือถูกปฏิเสธ ซึ่งยอมรับได้สำหรับ MVP |
| Expiration | เก็บ `expires_at` และตรวจหมดอายุใน transaction ของ reservation/payment; เพิ่มวิธี sweep หมดอายุแบบเรียบง่ายหากจำเป็นต่อ Dashboard | ไม่พึ่ง timer ใน process เดียวเป็นหลัก; การ sweep ช่วยปรับสถานะรายงาน แต่ correctness ต้องมาจากการตรวจเวลาใน transaction |
| QR mock payment | สร้าง QR จาก URL ของหน้า transaction บน web; server อ่านยอดเงินจริงจาก reservation และเทียบ input ก่อนยืนยัน | เป็น mock payment เพื่อเรียนรู้ ไม่มีเงินจริง; bypass ต้องจำกัดใน automated test/config และไม่รับยอด/สถานะจาก client เป็นข้อเท็จจริง |
| File storage | S3-compatible API ผ่าน MinIO ทุก environment; UI/API validate extension, size และ MIME | MinIO จำลอง API ใกล้ S3 แต่เพิ่ม service ใน local/test/deploy; ต้องทดสอบสิทธิ์การอ่านไฟล์และการอัปโหลดใหญ่ |

แหล่งอ้างอิงการตั้งค่าและ compatibility (ตรวจ ณ 2026-11-24): [Next.js installation](https://nextjs.org/docs/app/getting-started/installation), [Next.js Tailwind CSS](https://nextjs.org/docs/pages/getting-started/css), [NestJS first steps](https://docs.nestjs.com/first-steps), [NestJS workspace mode](https://docs.nestjs.com/cli/monorepo), [shadcn/ui Next.js](https://ui.shadcn.com/docs/installation/next), [pnpm workspaces](https://pnpm.io/workspaces). เอกสาร Next.js และ shadcn/ui แสดงตัวอย่าง pnpm/Tailwind; การเลือก shadcn/ui เป็นข้อเสนอ ไม่ใช่ข้อกำหนดที่ผู้พัฒนาอนุมัติแล้ว

## ลำดับดำเนินงาน

### 1. ยืนยันการตัดสินใจและขอบเขต

- ปิดประเด็นค้างใน Specification: reservation ที่ยังไม่ชำระเมื่อยกเลิก Event, Google account ที่ใช้อีเมลเดียวกันในคนละ realm, audit ข้อมูลการเปลี่ยน refund status และข้อมูลขั้นต่ำก่อน Publish
- เลือก data access/migration approach และยืนยันว่าผู้สร้าง Event เป็นผู้เปลี่ยน refund status เท่านั้น
- ยืนยันว่า shadcn/ui และ pnpm workspace เหมาะสม

### 2. วางฐาน monorepo และ local environment

- สร้าง pnpm workspace ที่มี `apps/web` และ `apps/api`; เพิ่มเฉพาะ scripts/config ที่จำเป็นต่อ dev, lint, typecheck, build และ tests
- ตั้งค่า PostgreSQL และ MinIO สำหรับ local development/test โดยไม่ใส่ credentials จริงใน repository
- ตั้งค่า environment variable templates, `.gitignore`, migrations และ health/readiness endpoints สำหรับ dependencies ที่จำเป็น
- เชื่อม web กับ API ผ่าน configurable base URL; ระบุ CORS/session/cookie policy ในการออกแบบ auth ก่อนเปิดสองแอป

### 3. สร้าง identity และ authorization

- ทำ signup/login/logout สำหรับ Owner และ Customer แยกกัน รวม Google Authentication callback แยก realm
- ทำ server-side authorization checks และ ownership guard; เพิ่มกรณีทดสอบห้ามข้าม role และห้าม Owner ที่ไม่ใช่ผู้สร้างจัดการ Event
- หลีกเลี่ยงการรวม account อัตโนมัติระหว่าง realm แม้อีเมลตรงกัน จนกว่าจะมีการตัดสินใจชัดเจน

### 4. ทำ Event draft, layouts และไฟล์

- เพิ่มข้อมูลตั้งต้นของผังสนามกีฬา/เวที หอประชุม และโรงภาพยนตร์ พร้อม seat identifiers; ทำเป็น data ที่ seed/reuse ได้
- ทำ UI แก้ Draft: เลือกผัง/โหมด, เปิดปิดที่นั่ง/พื้นที่, เพิ่มประเภทตั๋ว, กำหนดราคาและ capacity, กรอกรายละเอียด, อัปโหลดภาพ/PDF
- บังคับ capacity ของแต่ละประเภทและผลรวม; validate ชนิด/ขนาดไฟล์ฝั่ง API และเก็บ object ใน MinIO
- ทำ Publish validation และ server-side lock หลัง Publish; อนุญาตอัปเดตเฉพาะคำอธิบายและรูปภาพ

### 5. ทำ inventory และ reservation ให้ถูกต้องก่อน UI เต็ม

- นิยาม state transition และ inventory invariant สำหรับ seat-based และ capacity-based reservation
- ใช้ PostgreSQL transaction/constraints เพื่อให้คำขอชนกันไม่สร้าง seat reservation ซ้ำหรือ oversell; ทบทวนระดับ isolation และรูปแบบ lock จาก SQL จริงก่อนสรุป
- เก็บเวลาเริ่มและ `expires_at`; การจอง/ชำระต้องตรวจสถานะและเวลาใน transaction เดียวกับการแก้ inventory
- เพิ่มการหมดอายุแบบควบคุมเวลาในการทดสอบและวิธีปรับสถานะให้ Dashboard ทันสมัย

### 6. ทำ mock payment และ refund workflow

- ออก QR URL ที่ชี้ transaction เฉพาะ booking; หน้า mobile transaction แสดงยอดที่ server คำนวณ
- ยืนยันยอดตรง, expiry, repeated submit และ payment/expiry race แบบ atomic/idempotent
- จำกัด bypass ให้ automated tests; ห้าม endpoint สาธารณะรับคำสั่งที่ทำให้ booking ชำระแล้วโดยไม่ผ่านเงื่อนไข
- ทำ Event cancellation โดย creator และสถานะคืนเงิน Manual ตามลำดับ; แสดง booking ที่ชำระแล้วทุกใบที่ต้องดำเนินการ

### 7. ทำ Customer/Owner UI และ Dashboard

- ทำรายการ Event ที่ Publish, รายละเอียด Event, เลือกที่นั่ง/ประเภท/จำนวน, สร้าง reservation, QR และ transaction form
- ทำ Owner dashboard ด้วยตัวเลข capacity/reserved, available, paid, pending, expired และ refund states จากข้อมูล backend
- ทำ empty/loading/error states และ responsive interaction ขั้นพื้นฐาน; ใช้ lucide-react icons

### 8. Integration, verification และ review

- เชื่อมเส้นทาง UI/API จริงและตรวจ flow ทั้งสองโหมดจาก browser/API
- รัน unit tests ของ domain/state transitions, API integration tests กับ PostgreSQL จริง, MinIO file tests, auth/authorization tests และ UI flow tests ตาม acceptance criteria
- รันทดสอบ concurrency 100 requests ต่อ inventory 20 แยกสองโหมด พร้อม payment/expiry race และยืนยันยอดคงเหลือหลังจบ
- รัน lint, typecheck, build และชุดทดสอบที่มี; บันทึกคำสั่งและผลจริง
- Review diff เทียบ Specification; ระบุข้อเบี่ยงเบน ความเสี่ยง และความสามารถที่เลื่อนไป change ถัดไป

## verification strategy ตาม invariant

- **No oversell:** จำนวน reservation ที่ยังถือสิทธิ์และ paid bookings รวมกันต้องไม่เกินจำนวนที่เปิดขาย; seat หนึ่งตัวมีผู้ถือสิทธิ์ได้สูงสุดหนึ่งรายการ
- **Reservation expiry:** ก่อนหมดเวลา inventory ถูกถือไว้; หลังหมดเวลาและยังไม่ชำระ inventory กลับมาใช้ได้; การหมดอายุซ้ำไม่ทำให้คืน inventory ซ้ำ
- **Payment:** ยอดไม่ตรง/หมดอายุ/ยกเลิกแล้วปฏิเสธ; payment ซ้ำไม่สร้างผลซ้ำ; payment กับ expiration ที่แข่งกันได้ผลลัพธ์เดียวตามลำดับที่ commit
- **Authorization:** role, realm และ creator ownership ถูกตรวจใน API โดยตรง
- **Published Event immutability:** API ปฏิเสธแก้ราคา/capacity/layout/ticket types หลัง Publish; description/images ยังแก้ได้
- **Refund:** cancel โดย creator เปลี่ยน paid booking ทุกใบเข้าสู่ “รอคืนเงิน”; Manual transitions เดินไปข้างหน้าตามลำดับและบันทึกผลตามแนวทางที่อนุมัติ
- **Storage:** MIME/extension/size checks, สิทธิ์เข้าถึง และการอัปโหลด/อ่าน object ผ่าน MinIO ทำงานครบ

## ความเสี่ยงและ trade-offs

- การจองพร้อมกันทำให้ row/table contention; PostgreSQL เพียงพอสำหรับเป้าหมาย correctness MVP แต่ baseline 100/20 ไม่ได้ทำนาย production throughput
- QR mock payment ไม่ใช่หลักฐานการรับเงินจริง; UI ต้องแสดงชัดว่าเป็นการชำระจำลอง
- Google OAuth ต้องกำหนด callback และ secrets แยกตาม realm; การตั้งค่า provider credentials เป็นงาน environment setup ไม่ commit ลง Git
- การล็อก Event หลัง Publish จำกัดการแก้ไขและต้องนิยามให้ตรงกันทั้ง UI/API; ข้อความ/ภาพเป็นข้อยกเว้นที่อนุญาต
- ไฟล์ PDF สูงสุด 50 MB มีผลต่อ timeout และ memory; ใช้ streaming upload/download เท่าที่ stack รองรับและไม่อ่านทั้งไฟล์เข้า memory โดยไม่จำเป็น
- สถานะคืนเงิน Manual ไม่มีการยืนยันธุรกรรมเงินจริง; dashboard ต้องสื่อว่าเป็นการติดตามงานคืนเงิน
- ระยะ 2–3 สัปดาห์เสี่ยงสำหรับ scope รวม Google Auth สอง realm, layouts, uploads, QR flow และ concurrency; หากเวลาไม่พอ ให้ลด polish/analytics ก่อน และเสนอเลื่อน feature โดยไม่ลด invariants หลัก

## ประเด็นที่ต้องอนุมัติก่อน Build

1. แนวทาง data access และ migration; ข้อเสนอ SQL-first
2. shadcn/ui และ pnpm workspace
3. การแยก identity/session สำหรับ Google sign-in ระหว่าง Owner และ Customer รวมกรณีอีเมลเดียวกัน
4. การยกเลิก Event ทำให้ reservation ที่ยังไม่ชำระถูกยกเลิกและคืน inventory ทันทีหรือไม่
5. ขอบเขตข้อมูล Event ขั้นต่ำก่อน Publish และ audit fields สำหรับ refund status

เมื่อประเด็นเหล่านี้ได้รับการตัดสินและ Plan นี้ได้รับการยอมรับ จึงเริ่ม Build โดยไม่เปลี่ยน business requirements ที่ยอมรับแล้ว
