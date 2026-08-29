# Souped Up Grand Prix — VIP Village

Landing page แบบ single-file สำหรับซื้อบัตร **Souped Up Grand Prix VIP Village**
โทนสี/ดีไซน์เดียวกับโปรเจกต์ [nidss/su](https://github.com/nidss/su) รองรับ 2 ภาษา (ไทย/อังกฤษ)

A single-file landing page to buy **Souped Up Grand Prix VIP Village** tickets.
Same dark racing theme as [nidss/su](https://github.com/nidss/su), bilingual (TH/EN).

## โครงสร้างหน้า / Sections
1. Tabs ด้านบน: **Early Bird** / **Normal Price** (ราคาปรับตาม tab อัตโนมัติ)
2. Banner
3. เนื้อหา + ราคา (Early Bird มีเงื่อนไข, Normal ไม่มี)
4. สถานที่จัดงาน — ซ้าย: Bangkok Drag Avenue + ลิงก์ Google Maps / ขวา: แผนที่ Leaflet
5. ฟอร์มผู้สมัคร (ชื่อ, นามสกุล, เบอร์, อีเมล, ช่องทางติดต่อ Line/WeChat/WhatsApp แบบ checkbox + input)
6. แพ็กเกจ VIP Village (radio 1/2/3 วัน) → เลือกวัน 5–7 ธ.ค. 2026 (จำกัดตามจำนวนวันของแพ็กเกจ)
7. ไซส์เสื้อ (L / XL)
8. สำรองที่จอดรถ — สร้างช่องกรอกทะเบียนรถตามวันที่เลือกในข้อ 6
9. อาหารบุฟเฟต์ (รูป + ช่องอาหารที่ไม่ทาน / อาหารที่แพ้)
10. ยินยอมข้อมูลส่วนบุคคล + ปุ่ม Submit
11. Footer — Contact Center

## การรัน / Run
เปิด `index.html` ในเบราว์เซอร์ได้ทันที (front-end demo — ยังไม่เชื่อม backend; payload พิมพ์ลง console)

## เปลี่ยนรูปจริง / Swap real artwork
- Banner: วางไฟล์ที่ `asset/banner.png` แล้วแก้ `<div class="banner">` ตามคอมเมนต์ในไฟล์
- Buffet: วางไฟล์ที่ `asset/buffet.jpg` แล้วแทน `.buffet-ph` ตามคอมเมนต์ในไฟล์
