# Internship / CWIE System Mockup

Repository นี้เก็บ mockup และเอกสารโครงสร้างระบบสำหรับระบบฝึกงาน / CWIE ภาควิชาไอที

## ไฟล์หลัก

- [Mockup HTML](outputs/internship-system_feature_mockup.html)  
  ไฟล์ mockup แบบ single-page HTML เปิดดูได้ใน browser

- [เอกสารโครงสร้างระบบ](outputs/system-architecture.md)  
  อธิบายภาพรวมระบบ บทบาทผู้ใช้ โมดูลหลัก data model permission matrix และ workflow สำคัญ

## ฟีเจอร์ที่ครอบคลุมใน mockup

- Dashboard เจ้าหน้าที่
- รายชื่อนักศึกษาและหน้า Student 360
- สถานประกอบการและข้อมูลพี่เลี้ยง
- Company Portal สำหรับจัดการพี่เลี้ยง, logbook, Job plan และงานของบริษัท
- Student Portal พร้อม daily logbook และ weekly summary
- Advisor Portal สำหรับอาจารย์นิเทศ
- วงจรเอกสาร CWIE และ timeline รายคน
- Matching logic พร้อม capacity guard และ skill synonym
- ตั้งค่าช่วงเวลาประเมินโดยเจ้าหน้าที่
- Audit log และ notification

## หมายเหตุด้านบทบาท

- สถานประกอบการเป็นผู้เพิ่ม/แก้ไข/ผูกพี่เลี้ยงกับนักศึกษา
- เจ้าหน้าที่มีหน้าที่ติดตามสถานะพี่เลี้ยงและขอให้บริษัทอัปเดต ไม่แก้ไขแทนบริษัท
- นักศึกษาและอาจารย์นิเทศประเมินสถานประกอบการได้เฉพาะช่วงเวลาที่เจ้าหน้าที่กำหนด

