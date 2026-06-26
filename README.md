# Internship / CWIE System Mockup

## GitHub Pages

Live mockup URL after enabling GitHub Pages:

https://wivachr.github.io/ui-mockup-backend-auth-sso-mock/

GitHub setup:

- Go to `Settings` > `Pages`
- Source: `Deploy from a branch`
- Branch: `main`
- Folder: `/ (root)`
- Click `Save`

After GitHub finishes publishing, the root `index.html` redirects to `outputs/internship-system_feature_mockup.html`.

Repository นี้เก็บ mockup และเอกสารโครงสร้างระบบสำหรับระบบฝึกงาน / CWIE ภาควิชาไอที

## ไฟล์หลัก

- [Mockup HTML](outputs/internship-system_feature_mockup.html)  
  ไฟล์ mockup แบบ single-page HTML เปิดดูได้ใน browser

- [เอกสารโครงสร้างระบบ](outputs/system-architecture.md)  
  อธิบายภาพรวมระบบ บทบาทผู้ใช้ โมดูลหลัก data model permission matrix และ workflow สำคัญ

- [แผน Tech Stack และเฟสการพัฒนา](outputs/tech-stack-and-roadmap.md)  
  เสนอ stack ที่ควรใช้และ roadmap การพัฒนาระบบจาก mockup ไปสู่ระบบใช้งานจริง

## ฟีเจอร์ที่ครอบคลุมใน mockup

- Dashboard เจ้าหน้าที่
- รายชื่อนักศึกษาและหน้า Student 360
- สถานประกอบการและข้อมูลพี่เลี้ยง
- Company Portal สำหรับจัดการพี่เลี้ยง, logbook, Job plan และงานของบริษัท
- Student Portal พร้อม daily logbook และ weekly summary
- Advisor Portal สำหรับอาจารย์นิเทศ พร้อมแก้ไขข้อมูลส่วนตัว/ข้อมูลติดต่อของอาจารย์เอง
- วงจรเอกสาร CWIE และ timeline รายคน
- Matching logic พร้อม capacity guard และ skill synonym
- ตั้งค่าช่วงเวลาประเมินโดยเจ้าหน้าที่
- Audit log และ notification

## หมายเหตุด้านบทบาท

- สถานประกอบการเป็นผู้เพิ่ม/แก้ไข/ผูกพี่เลี้ยงกับนักศึกษา
- เจ้าหน้าที่มีหน้าที่ติดตามสถานะพี่เลี้ยงและขอให้บริษัทอัปเดต ไม่แก้ไขแทนบริษัท
- อาจารย์นิเทศเป็นผู้เพิ่ม/แก้ไขข้อมูลติดต่อ ตารางเวลาพบ และความเชี่ยวชาญของตนเอง
- นักศึกษาและอาจารย์นิเทศประเมินสถานประกอบการได้เฉพาะช่วงเวลาที่เจ้าหน้าที่กำหนด
