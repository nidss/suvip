# Souped Up Grand Prix - VIP Village

Landing page แบบ single-file สำหรับซื้อบัตร **Souped Up Grand Prix VIP Village**
พร้อมระบบชำระเงินเดโม ใช้หน้าและ flow เวอร์ชันเดียวกับโปรเจกต์
[nidss/su](https://github.com/nidss/su) และรองรับ 2 ภาษา (ไทย/อังกฤษ)

A single-file landing page for **Souped Up Grand Prix VIP Village** tickets,
using the current purchase flow from [nidss/su](https://github.com/nidss/su),
with demo payment and bilingual TH/EN support.

🔗 **Live:** https://nidss.github.io/suvip/

---

## หน้า Landing / Registration

Tabs ด้านบนสุด: **Early Bird** / **Normal Price** - ราคาทุกส่วนปรับตาม tab อัตโนมัติ

1. **Banner** - `asset/banner.png`
2. **เนื้อหา + ราคา** - Early Bird มีเงื่อนไข (จอง-ชำระในกำหนด, จำกัด 20 ที่นั่ง/วัน), Normal Price ไม่มี
3. **สถานที่จัดงาน** - ซ้าย: Bangkok Drag Avenue + ลิงก์ Google Maps / ขวา: รูปแผนที่ (`asset/map.png`) คลิกเปิด Google Maps
4. **ข้อมูลติดต่อ** - ชื่อ / นามสกุล / เบอร์โทร / อีเมล (บังคับทั้งหมด) และช่องทางการติดต่อเพิ่มเติมแบบ checkbox (Line / WeChat / Whatsapp) ที่เผยช่องกรอกเมื่อเลือก และต้องเลือกอย่างน้อย 1 ช่องทาง — ค่าเริ่มต้นดึงจากโปรไฟล์สมาชิก SU เมื่อฝังใน iframe
5. **บัตรเข้างาน** - เลือกแพ็กเกจ 1/2/3 วัน และวันที่ 4–6 ธ.ค. 2026
6. **สำรองที่จอดรถ** - เลือกมีรถ/ไม่มีรถ และบังคับกรอกทะเบียนเมื่อเลือกมีรถ
7. **ไซส์เสื้อ** - L / XL
8. **อาหารบุฟเฟต์** - รูป (`asset/buffet.png`) + ช่องอาหารที่ไม่ทาน / อาหารที่แพ้
9. **เพิ่มบัตรเข้างาน** - ซื้อหลายใบได้ โดยแต่ละใบมีแพ็กเกจ รถ เสื้อ และอาหารแยกกัน
10. **ยินยอมข้อมูลส่วนบุคคล** + ปุ่ม Submit
11. **หน้าชำระเงินและใบเสร็จ**

แพ็กเกจ 3 วันจะเลือกไม่ได้เมื่อมีวันที่เต็ม และกล่องบัตรจะยุบ/ขยายได้เมื่อมีมากกว่าหนึ่งใบ
(ไอคอนยุบ/ขยายใช้ Lucide chevron-up / chevron-down)

### ราคา / Pricing

| แพ็กเกจ | Early Bird | Normal Price |
|---------|-----------:|-------------:|
| 1 วัน | 3,700 | 3,900 |
| 2 วัน | 7,400 | 8,400 |
| 3 วัน | 7,900 | 8,900 |

## หน้าชำระเงิน / Payment

กด Submit (ผ่าน validation) → เข้าหน้าชำระเงิน (ดีไซน์เดียวกับ nidss/su):
สรุปยอด, เลือกบัตรเครดิต / QR โอนเงิน, ใบกำกับภาษี, เงื่อนไขการชำระเงิน →
ยืนยัน → หน้าสำเร็จพร้อมใบเสร็จ + เลขอ้างอิง

## การรัน / Run

เปิด `index.html` ในเบราว์เซอร์ได้ทันที ไม่มี dependency ที่ต้องติดตั้ง
(ควรเปิดผ่าน HTTP server เพื่อให้ localStorage ทำงานเต็มที่ เช่น `python3 -m http.server`)

## รูปภาพ / Assets

วางไฟล์ในโฟลเดอร์ `asset/`:

| ไฟล์ | ใช้ที่ |
|------|--------|
| `logovip.png` | โลโก้มุมซ้ายบน (Nav bar, สูง 40px, คลิกกลับหน้าแรก) |
| `banner.png` | แบนเนอร์หัวหน้า |
| `map.png` | รูปแผนที่สถานที่จัดงาน |
| `buffet.png` | รูปอาหารบุฟเฟต์ |

## Deploy (GitHub Pages)

Settings → Pages → Source: **Deploy from a branch** → `main` / root
เว็บจะขึ้นที่ https://nidss.github.io/suvip/ (มี `.nojekyll` ให้แล้ว)
