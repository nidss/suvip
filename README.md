# Souped Up Grand Prix — VIP Village

Landing page แบบ single-file สำหรับซื้อบัตร **Souped Up Grand Prix VIP Village**
พร้อมระบบชำระเงิน (เดโม) และหน้า Dashboard สำหรับแอดมิน
โทนสี/ดีไซน์เดียวกับโปรเจกต์ [nidss/su](https://github.com/nidss/su) รองรับ 2 ภาษา (ไทย/อังกฤษ)

A single-file landing page to buy **Souped Up Grand Prix VIP Village** tickets,
with a demo payment flow and an admin dashboard. Same dark racing theme as
[nidss/su](https://github.com/nidss/su), bilingual (TH/EN).

🔗 **Live:** https://nidss.github.io/suvip/

---

## หน้า Landing / Registration

Tabs ด้านบนสุด: **Early Bird** / **Normal Price** — ราคาทุกส่วนปรับตาม tab อัตโนมัติ

1. **Banner** — `asset/banner.png`
2. **เนื้อหา + ราคา** — Early Bird มีเงื่อนไข (จอง-ชำระในกำหนด, จำกัด 20 ที่นั่ง/วัน), Normal Price ไม่มี
3. **สถานที่จัดงาน** — ซ้าย: Bangkok Drag Avenue + ลิงก์ Google Maps / ขวา: รูปแผนที่ (`asset/map.png`) คลิกเปิด Google Maps
4. **ฟอร์มผู้สมัคร** — ชื่อ, นามสกุล, เบอร์, อีเมล, ช่องทางติดต่อ (Line / WeChat / WhatsApp — ติ๊กแล้วมีช่องกรอก)
5. **แพ็กเกจ VIP Village** — radio 1/2/3 วัน → เลือกวัน 5–7 ธ.ค. 2026 (แพ็ก 1 วัน = radio เลือกวันเดียว, 2/3 วัน = checkbox จำกัดจำนวนวันอัตโนมัติ)
6. **ไซส์เสื้อ** — L / XL
7. **สำรองที่จอดรถ** — สร้างช่องกรอกทะเบียนรถตามวันที่เลือกในข้อ 5
8. **อาหารบุฟเฟต์** — รูป (`asset/buffet.png`) + ช่องอาหารที่ไม่ทาน / อาหารที่แพ้
9. **ยินยอมข้อมูลส่วนบุคคล** + ปุ่ม Submit
10. **Footer** — Contact Center

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

## Dashboard แอดมิน / Admin Dashboard

กดคีย์ลัด **`Ctrl` + `Shift` + `Z`**

- **หน้า Login แอดมิน** ขึ้นก่อน — เดโม: **ใส่อะไรก็ได้** เพื่อเข้าสู่ระบบ (จำสถานะไว้ใน `sessionStorage`, มีปุ่ม Log out)
- **KPI cards** — จำนวนผู้สมัคร, จำนวนเงินรวม
- **ตารางผู้สมัคร** — แสดงทุก field, แบ่งหน้า **30 แถว/หน้า** (pagination)
- **แก้ไข** (ไอคอน square-pen) — เปิด modal แก้ไขได้ทุก field รวมถึงแพ็กเกจ/วันที่มา/ทะเบียนรถ (ยอดคำนวณใหม่อัตโนมัติ)
- **Export CSV** — มุมขวาบนของตาราง (UTF-8 + BOM เปิดใน Excel ภาษาไทยไม่เพี้ยน)
- มี **mock data 68 รายการ** seed อัตโนมัติเมื่อยังไม่มีข้อมูล

> ข้อมูลผู้สมัครเก็บใน **localStorage** ของเบราว์เซอร์ (front-end demo — ยังไม่เชื่อม backend)

## การรัน / Run

เปิด `index.html` ในเบราว์เซอร์ได้ทันที ไม่มี dependency ที่ต้องติดตั้ง
(ควรเปิดผ่าน HTTP server เพื่อให้ localStorage ทำงานเต็มที่ เช่น `python3 -m http.server`)

## รูปภาพ / Assets

วางไฟล์ในโฟลเดอร์ `asset/`:

| ไฟล์ | ใช้ที่ |
|------|--------|
| `banner.png` | แบนเนอร์หัวหน้า |
| `map.png` | รูปแผนที่สถานที่จัดงาน |
| `buffet.png` | รูปอาหารบุฟเฟต์ |

## Deploy (GitHub Pages)

Settings → Pages → Source: **Deploy from a branch** → `main` / root
เว็บจะขึ้นที่ https://nidss.github.io/suvip/ (มี `.nojekyll` ให้แล้ว)
