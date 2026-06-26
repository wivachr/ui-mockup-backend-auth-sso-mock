# แผน Tech Stack และเฟสการพัฒนา

เอกสารนี้เสนอ tech stack และ roadmap สำหรับพัฒนาระบบฝึกงาน / CWIE จาก mockup ปัจจุบันให้เป็นระบบใช้งานจริง

## 1. หลักคิดในการเลือก Stack

ระบบนี้มีลักษณะเป็น workflow-heavy application มากกว่าเว็บประชาสัมพันธ์ทั่วไป จุดสำคัญคือ permission, audit trail, document workflow, notification, file storage, และข้อมูลเชิงสัมพันธ์จำนวนมาก เช่น นักศึกษา บริษัท พี่เลี้ยง เอกสาร logbook และการประเมิน

ดังนั้น stack ควรเน้น:

- Type safety ระหว่าง frontend / backend / database
- Role-based access control ชัดเจน
- Database ที่เหมาะกับ relational workflow
- File storage สำหรับเอกสาร CWIE และไฟล์แนบ logbook
- Audit log ที่ query ได้
- Deploy ง่ายและดูแลง่ายสำหรับทีมเล็ก

## 2. Recommended Tech Stack

### 2.1 Frontend / Full-stack Framework

**แนะนำ: Next.js + TypeScript**

เหตุผล:

- เหมาะกับ full-stack web application
- รองรับ routing, server rendering, server-side data fetching และ backend endpoint ในโปรเจกต์เดียว
- ทำ role-based dashboard หลาย portal ได้ดี เช่น staff, student, advisor, company
- ใช้ TypeScript ได้เต็มระบบ ลด bug จาก payload และ schema ที่ไม่ตรงกัน

โครงสร้าง route ที่แนะนำ:

```txt
app/
  (auth)/
    login/
  staff/
    dashboard/
    students/
    students/[id]/
    companies/
    mentors/
    matching/
    cwie/
    evaluation-settings/
    reports/
    audit-log/
  student/
    dashboard/
    logbook/
    evaluation/
  advisor/
    dashboard/
    evaluations/
  company/
    dashboard/
    mentors/
    students/
    documents/
    logbooks/
    evaluations/
```

### 2.2 UI Layer

**แนะนำ: Tailwind CSS + shadcn/ui + Radix UI**

เหตุผล:

- ทำ dashboard, form, table, tabs, dialog, badge, toast ได้เร็ว
- เหมาะกับ mockup ที่ตอนนี้เป็น operational UI
- ปรับ design system ได้โดยไม่ติด vendor มาก

Component ที่ควรทำเป็น shared component:

- StatusBadge
- OwnerChip
- DataTable
- Timeline
- DocumentFlow
- TaskQueue
- EvaluationWindowCard
- LogbookDailyForm
- AuditLogTable

### 2.3 Backend / API

**แนะนำ: Next.js server-side handlers / server actions สำหรับเฟสแรก**

เฟสแรกควรอยู่ใน monolith เดียวก่อน เพราะ domain ยังเปลี่ยนเร็วและทีมจะ iterate ได้ไว

ใช้ backend layer แยก service ตาม domain:

```txt
server/
  services/
    auth.service.ts
    students.service.ts
    companies.service.ts
    matching.service.ts
    cwie.service.ts
    logbook.service.ts
    evaluations.service.ts
    notifications.service.ts
    audit.service.ts
  policies/
    permissions.ts
    ownership.ts
```

หากระบบโตขึ้นค่อยแยก background worker หรือ service เฉพาะ notification/report ภายหลัง

### 2.4 Database

**แนะนำ: PostgreSQL**

เหตุผล:

- ข้อมูลเป็น relational ชัดเจน
- ต้อง query cross-module เช่น student + company + mentor + documents + logbook
- เหมาะกับ audit log, workflow status, report
- รองรับ JSONB สำหรับคะแนนประเมินหรือ payload บางส่วนได้

### 2.5 ORM / Database Access

**แนะนำ: Prisma**

เหตุผล:

- Type-safe query ใน TypeScript
- มี schema + migration workflow ชัดเจน
- เหมาะกับทีมที่ต้องพัฒนา data model ต่อเนื่องจาก mockup
- ใช้ Prisma Studio ตรวจข้อมูลระหว่าง dev ได้สะดวก

หมายเหตุ:

- ถ้าใช้ Supabase Auth + RLS หนักมาก อาจเลือกใช้ Supabase client / SQL function บางส่วนร่วมกับ Prisma
- ถ้าใช้ Prisma เป็นหลัก ควรออกแบบ permission ใน application service layer ให้เข้ม

### 2.6 Backend Platform

**ตัวเลือกแนะนำ: Supabase**

ใช้สำหรับ:

- Managed Postgres
- Authentication
- Storage สำหรับเอกสาร CWIE และไฟล์แนบ logbook
- Realtime notification เฉพาะบางจุด ถ้าจำเป็น
- Edge Functions สำหรับงาน webhook หรือ background task บางส่วน

แนวทางใช้ Supabase:

- Auth: เก็บ user identity และ session
- Database: ใช้ Postgres เป็น source of truth
- Storage: แยก bucket เช่น `cwie-documents`, `logbook-attachments`, `evaluation-files`
- RLS: ใช้กับ table ที่ expose ผ่าน Supabase client โดยตรง
- Server-side Next.js: ใช้ service role เฉพาะใน server เท่านั้น ห้ามส่งไป client

### 2.7 Authentication / Authorization

แนะนำใช้ Supabase Auth หรือ Auth.js ร่วมกับ PostgreSQL ก็ได้ แต่ถ้าเลือก Supabase อยู่แล้ว ให้ใช้ Supabase Auth เพื่อลดงาน backend

Role หลัก:

- `staff`
- `student`
- `advisor`
- `company_admin`
- `company_mentor`

หลักการ permission:

- Staff จัดการข้อมูลระบบและตั้งค่าช่วงประเมิน
- Student เห็นเฉพาะข้อมูลของตน
- Advisor เห็นเฉพาะงานนิเทศที่ได้รับมอบหมาย
- Advisor เพิ่ม/แก้ไขข้อมูล profile ของตนเองได้ เช่น ข้อมูลติดต่อ เวลาพบ ความเชี่ยวชาญ และจำนวนรับนิเทศสูงสุด
- Company เห็นเฉพาะนักศึกษา/พี่เลี้ยง/เอกสารของบริษัทตน
- Company เป็นเจ้าของข้อมูลพี่เลี้ยง
- Staff ติดตามข้อมูลพี่เลี้ยงได้ แต่ไม่แก้ไขแทนบริษัท

### 2.8 File Storage

**แนะนำ: Supabase Storage**

Bucket:

- `cwie-documents`
- `job-plans`
- `logbook-attachments`
- `evaluation-attachments`

ควรเก็บ metadata ใน database:

- uploader_id
- owner_type
- owner_id
- file_path
- mime_type
- size
- checksum
- uploaded_at

### 2.9 Notification

เฟสแรก:

- In-app notification table
- Email notification ผ่าน provider เช่น Resend / SendGrid / SMTP มหาวิทยาลัย

เฟสถัดไป:

- Scheduled reminders ก่อนครบกำหนด 7 วัน / 3 วัน / 1 วัน
- Digest รายวันให้เจ้าหน้าที่
- Notification preference ต่อ role

### 2.10 Background Jobs

เฟสแรกใช้ scheduled job ง่าย ๆ:

- Vercel Cron หรือ Supabase Scheduled Functions

งานที่เหมาะกับ background job:

- แจ้งเตือนเอกสารใกล้ครบกำหนด
- แจ้งเตือน logbook รอตรวจ
- ปิด evaluation window เมื่อหมดเวลา
- สรุปรายงานประจำรอบ

### 2.11 Audit Log

ควรทำเป็น cross-cutting service ตั้งแต่แรก

ทุก action สำคัญต้องเขียน audit log:

- login
- create/update/delete student
- company update mentor
- mentor assignment
- matching assignment
- capacity guard triggered
- document status change
- logbook submitted/reviewed
- evaluation window changed
- evaluation submitted

## 3. Data Model กลุ่มแรกที่ควรสร้าง

### Core

- users
- user_roles
- internship_rounds
- students
- companies
- company_users
- company_mentors
- mentor_assignments
- advisor_profiles
- advisor_assignments

### Workflow

- internship_assignments
- cwie_documents
- document_files
- daily_logbook_entries
- weekly_logbook_summaries
- logbook_review_comments
- evaluation_windows
- evaluations
- notifications
- audit_logs

### Reference / Config

- skill_synonyms
- company_positions
- evaluation_forms
- evaluation_criteria
- system_settings

## 4. เฟสการพัฒนา

### Phase 0: Project Setup

เป้าหมาย: ตั้งฐานโปรเจกต์ให้พร้อมพัฒนาจริง

งาน:

- สร้าง Next.js + TypeScript project
- ตั้ง Tailwind/shadcn/ui
- ตั้ง Supabase project หรือ local Supabase
- ตั้ง Prisma schema และ migration
- ตั้ง lint, format, typecheck
- ตั้ง env แยก local/staging/production
- สร้าง layout ตาม role

ผลลัพธ์:

- เข้าหน้า login/mock auth ได้
- มี shell ของ staff/student/advisor/company
- มี database migration แรก

### Phase 1: Auth + Role + Core Master Data

เป้าหมาย: ใช้ระบบแบบมีผู้ใช้และสิทธิ์จริง

งาน:

- Login/logout
- Role-based route guard
- Staff จัดการนักศึกษา
- Staff จัดการสถานประกอบการ
- Company จัดการข้อมูลบริษัทตนเอง
- Company จัดการพี่เลี้ยง
- Advisor จัดการ profile ของตนเอง
- Staff ดูสถานะพี่เลี้ยงแบบ read-only

ผลลัพธ์:

- ข้อมูลนักศึกษา/บริษัท/พี่เลี้ยงอยู่ใน database จริง
- แยกสิทธิ์ staff/company ได้ชัดเจน

### Phase 2: Matching Engine

เป้าหมาย: จับคู่นักศึกษากับสถานประกอบการตามกฎธุรกิจ

งาน:

- สร้าง company positions และ capacity
- Matching score
- Skill synonym matching
- Capacity guard
- Flag บริษัทเฝ้าระวัง
- Assign company to student
- สร้าง audit log ทุกครั้งที่จับคู่

ผลลัพธ์:

- Staff จับคู่ได้
- ระบบห้ามจับคู่เกินความจุ
- บริษัทใหม่ไม่มีคะแนนปลอม

### Phase 3: Student 360 + CWIE Workflow

เป้าหมาย: มีหน้ารายคนที่ใช้ติดตามงานจริง

งาน:

- Student 360
- CWIE document timeline
- Owner per document
- Due date
- Upload document
- Staff issue/send document
- Company submit Job plan
- Status/action required

ผลลัพธ์:

- เปิดนักศึกษารายคนแล้วเห็น workflow ทั้งหมด
- เห็นว่างานค้างที่ใคร

### Phase 4: Daily Logbook + Mentor Review

เป้าหมาย: นักศึกษาบันทึกการปฏิบัติงานรายวันและพี่เลี้ยงตรวจได้

งาน:

- Student daily log form
- Draft/submit daily log
- Weekly summary generator
- Submit weekly summary to mentor
- Company mentor review
- Approve / request changes
- Attach evidence
- Audit log

ผลลัพธ์:

- นักศึกษาบันทึกรายวันได้
- พี่เลี้ยงตรวจเป็นรายวันหรือรายสัปดาห์ได้
- อาจารย์/เจ้าหน้าที่ดูประกอบการติดตามได้

### Phase 5: Evaluation Window + Evaluations

เป้าหมาย: แบบประเมินเปิดตามช่วงเวลาที่เจ้าหน้าที่กำหนด

งาน:

- Staff ตั้งช่วงเวลาประเมิน
- Student company evaluation
- Advisor company evaluation
- Company student evaluation
- Lock/unlock form by evaluation window
- Reminder before close
- Store scores and comments

ผลลัพธ์:

- นักศึกษาและอาจารย์ประเมินได้เฉพาะช่วงที่เปิด
- Staff เห็น preview ว่า portal ไหนเปิด/ล็อก
- ข้อมูลประเมินพร้อมใช้ทำรายงาน

### Phase 6: Notification + Task Queue

เป้าหมาย: ทุก role เห็นงานที่ต้องทำต่อทันที

งาน:

- In-app notifications
- Staff notification topbar
- Company task queue
- Student action required
- Advisor task list
- Email reminder
- Scheduled reminders

ผลลัพธ์:

- ลดงานไล่ตามด้วยมือ
- เห็นงานค้างตาม owner ชัดเจน

### Phase 7: Reports + Quality Intelligence

เป้าหมาย: ใช้ข้อมูลประเมินและ logbook เพื่อช่วยตัดสินใจรอบถัดไป

งาน:

- Company quality report
- Skill demand report
- Student progress report
- CWIE completion report
- Export CSV/PDF
- Audit log export

ผลลัพธ์:

- เห็นบริษัทแนะนำ/เฝ้าระวัง
- เห็นงานตรงสายจริงหรือไม่
- ใช้ประกอบประกันคุณภาพได้

### Phase 8: Hardening / Production

เป้าหมาย: ทำให้ระบบพร้อมใช้งานจริง

งาน:

- Security review
- RLS / permission tests
- Backup/restore plan
- Error monitoring
- Performance tuning
- Accessibility pass
- UAT กับทุก role
- Training material

ผลลัพธ์:

- พร้อม pilot กับรอบฝึกงานจริง

## 5. MVP Scope ที่แนะนำ

ถ้าต้องทำให้ใช้งานได้เร็ว ควรเลือก MVP ดังนี้:

1. Auth + roles
2. Staff จัดการนักศึกษา/บริษัท
3. Company จัดการพี่เลี้ยง
4. Matching + capacity guard
5. Student 360
6. CWIE timeline เบื้องต้น
7. Daily logbook + weekly submit
8. Evaluation window + student/advisor evaluation
9. Audit log ขั้นพื้นฐาน

ยังไม่ต้องทำใน MVP:

- Realtime complex notification
- PDF generation เต็มรูปแบบ
- Advanced analytics
- Mobile app แยก
- Workflow designer แบบ configurable

## 6. Risk และข้อควรระวัง

### Permission ซับซ้อน

ควรเขียน permission test ตั้งแต่ Phase 1 เพราะข้อมูลหลายส่วนเป็น cross-role เช่น staff เห็นได้ แต่แก้ไม่ได้

### Evaluation Window

ต้องใช้ server-side validation เสมอ ห้ามเช็กแค่ frontend เพราะผู้ใช้สามารถ bypass UI ได้

### Logbook ย้อนหลัง

ควรกำหนด policy ชัดเจน เช่น ย้อนหลังได้ไม่เกิน 7 วัน หรือถ้าย้อนหลังต้องมีเหตุผลและ audit log

### Company Mentor Ownership

ต้องล็อกให้บริษัทแก้ได้เฉพาะ mentor ของบริษัทตัวเอง และ staff ไม่ควรแก้แทนใน normal flow

### File Upload

ต้องจำกัดขนาดไฟล์ ตรวจ MIME type และผูกไฟล์กับ owner/entity ชัดเจน

## 7. Recommendation สรุป

Stack ที่แนะนำ:

- Frontend/full-stack: Next.js + TypeScript
- UI: Tailwind CSS + shadcn/ui
- Database: PostgreSQL
- ORM: Prisma
- Backend platform: Supabase
- Auth: Supabase Auth
- Storage: Supabase Storage
- Notification: In-app table + email provider
- Background jobs: Vercel Cron หรือ Supabase Scheduled Functions
- Deploy: Vercel สำหรับ web app, Supabase สำหรับ backend services

แนวทางพัฒนา:

เริ่มจาก monolith full-stack ก่อน อย่ารีบแยก service เพราะ domain ยังปรับอยู่มาก สิ่งสำคัญที่สุดคือ data model, permission, audit log และ workflow ownership ให้ถูกตั้งแต่แรก
