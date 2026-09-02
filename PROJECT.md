# PROJECT.md — Souped Up Grand Prix VIP Village

เอกสารสถาปัตยกรรมสำหรับนักพัฒนา (developer-facing architecture notes)
ส่วนการใช้งาน/ฟีเจอร์ดูที่ [`README.md`](README.md) และแนวทางสำหรับ
Claude Code ดูที่ [`CLAUDE.md`](CLAUDE.md)

---

## ภาพรวม / Overview

หน้า landing + ซื้อบัตรแบบ **single-file** สองภาษา (ไทย/อังกฤษ) สำหรับงาน
"Souped Up Grand Prix VIP Village" ทั้งหน้า — HTML, CSS และ JavaScript —
อยู่ใน `index.html` ไฟล์เดียว **ไม่มี build system, package manager หรือ
dependency ภายนอก** เจตนาให้ flow ซื้อ/ชำระเงินเหมือนโปรเจกต์พี่น้อง
[nidss/su](https://github.com/nidss/su) เมื่อแก้ checkout/payment ต้องรักษา
ความเข้ากันนี้ไว้

## โครงสร้างไฟล์ / File layout

| ไฟล์ / โฟลเดอร์ | หน้าที่ |
|---|---|
| `index.html` | ทั้งแอป (markup + CSS + JS inline) |
| `asset/` | รูปที่หน้าอ้างถึง — `logovip.png`, `banner.png`, `map.png`, `buffet.png` |
| `.nojekyll` | กัน GitHub Pages ประมวลผลไฟล์ผิด |
| `README.md` | คู่มือฟีเจอร์/การใช้งาน (TH/EN) |
| `CLAUDE.md` | แนวทางสำหรับ Claude Code |
| `PROJECT.md` | เอกสารสถาปัตยกรรม (ไฟล์นี้) |

## สถาปัตยกรรม / Architecture

### Views & routing

หน้าเป็นชุดการ์ด `.card` ซ้อนกันใน `#regView` พร้อมกับอีก 2 view พี่น้อง
`#payView` และ `#doneView` — ฟังก์ชัน `showView('reg'|'pay'|'done')` สลับ
attribute `hidden` เป็น "router" เดียวของแอป

```
#regView  → banner · content+price · venue · [form: contact · tickets · consent · submit]
#payView  → สรุปยอด + วิธีชำระเงิน + ใบกำกับภาษี
#doneView → หน้าสำเร็จ + ใบเสร็จ + เลขอ้างอิง
```

### State-driven (ไม่มี framework)

`state` (global object เดียว) เป็น source of truth — mutate `state` แล้วเรียก
`render*()` ที่ตรงกัน คือ pattern การอัปเดตทั้งหมด ไม่มี reactive binding

| ฟิลด์ใน `state` | ความหมาย |
|---|---|
| `lang` | ภาษาปัจจุบัน `'th'` / `'en'` |
| `tab` | ราคา `'early'` (Early Bird) / `'normal'` (Normal) |
| `tickets[]` | บัตรแต่ละใบ สร้างจาก `newTicket()` (แต่ละใบมีแพ็กเกจ/วัน/รถ/เสื้อ/อาหารแยกกัน) |
| `payMethod` | `'card'` / QR |
| `tax` | ขอใบกำกับภาษีหรือไม่ |
| `result` | ผลการชำระเงินสำหรับหน้า done |
| `dashPage` | หน้า pagination ของประวัติการซื้อ |

ฟังก์ชัน render หลัก: `renderContent()` (การ์ดราคา), `renderTickets()`
(หนึ่งการ์ดยุบ/ขยายได้ต่อหนึ่งบัตร), `renderPay()` / `renderPayDetail()`,
`renderDone()` — event handler จะถูกผูกใหม่ **ภายใน** ฟังก์ชันเหล่านี้ทุกครั้งที่
rebuild DOM ผ่าน attribute `data-*` (เช่น `data-ticket-day`, `data-ticket-plate`,
`data-chan`)

### ราคา/ที่นั่ง เป็น data-driven

ค่าคงที่บนสุดของ `<script>` เป็นแหล่งข้อมูลเดียว — แก้วันจัดงาน ราคา หรือ
จำนวนที่นั่งที่นี่ ไม่ใช่ใน markup

| ค่าคงที่ | ใช้ทำอะไร |
|---|---|
| `PRICES` | ราคาต่อแพ็กเกจ แยกตาม tab |
| `PKG_DAYS` | จำนวนวันของแต่ละแพ็กเกจ (`p1`=1, `p2`=2, `p3`=3) |
| `DAYS` | วันจัดงาน (4–6 ธ.ค. 2026) + label สองภาษา |
| `DAY_CAP` / `DAY_BOOKED` / `NEAR_LEFT` | ความจุ/ยอดจอง (mock) เพื่อคำนวณสถานะเต็ม/ใกล้เต็ม |

`dayStatus`, `availableDayIds`, `pkgAvailable` คำนวณจากค่าเหล่านี้ เช่น
แพ็กเกจที่ครอบวันซึ่งเต็มแล้วจะเลือกไม่ได้

### Validation & persistence

Submit handler ของ `#regForm` ตรวจตามลำดับ: ข้อมูลติดต่อ → ช่องทางติดต่อเพิ่มเติม
(อย่างน้อย 1) → แต่ละบัตร (เลือกแพ็กเกจ, จำนวนวันถูกต้อง, ทะเบียนรถถ้ามีรถ) →
ยินยอม เมื่อไม่ผ่านจะ mark `.field.invalid` (กรอบแดง + `.err-msg`) และเรียก
`toast()` เมื่อผ่านจะเขียนหนึ่ง record ต่อหนึ่งบัตรลง `localStorage`
(`suvip_regs`) ผ่าน `addReg()` แล้วไป view `pay`

Persistence ใช้ `localStorage` key เดียว `suvip_regs` (array) ผ่าน
`loadRegs` / `saveRegs` / `addReg` — เป็น mock "ประวัติการซื้อ"

## i18n

ทุกข้อความที่ผู้ใช้เห็นเป็น key ใน dictionary `L` (`L.th` / `L.en`) — `t(key)`
แปลตาม `state.lang` และ `applyLang()` เดินทั้ง DOM แล้วเติมค่า:

| attribute | เติมลงใน |
|---|---|
| `data-i18n` | `textContent` |
| `data-i18n-html` | `innerHTML` |
| `data-i18n-ph` | `placeholder` |

**เพิ่มข้อความใหม่ต้องใส่ทั้ง `th` และ `en` แล้วอ้างผ่าน attribute เหล่านี้**
ห้าม hardcode — และ label ที่มี child element (เช่น `*` บังคับกรอก) ต้องหุ้ม
ข้อความที่แปลได้ไว้ใน `<span data-i18n=...>` ชั้นใน เพราะ `data-i18n` เขียนทับ
`textContent` ของ node ตัวเอง

## การเชื่อมกับ host SU ผ่าน postMessage

หน้านี้รันแบบ standalone **หรือ** ฝังใน iframe โดย host `nidss/su` ก็ได้ จะคุยกับ
`window.parent` เฉพาะตอนถูกฝัง (`window.parent !== window`) เท่านั้น

- **ขาเข้า:** `su:member` → `applyHostMember()` เติมข้อมูลติดต่อ
  (`name`/`surname`/`phone`/`email`) จากโปรไฟล์สมาชิกที่แชร์มา และแสดงปุ่มสมาชิก
- **ขาออก:** `suvip:complete` (เมื่อชำระเงินเสร็จ), `suvip:profile`,
  `suvip:back`, `suvip:scroll-top`, `suvip:resize` (`ResizeObserver` รายงานความสูง
  ให้ host ปรับขนาด iframe)

ฟีเจอร์ standalone ใหม่ต้อง guard ให้ทำงานได้ปกติเมื่อไม่มี parent frame

## รัน & Deploy

- **รันในเครื่อง:** เปิด `index.html` ตรง ๆ หรือ (แนะนำ เพื่อให้ `localStorage`
  ทำงาน) `python3 -m http.server` แล้วเปิดผ่าน server
- **ไม่มีคำสั่ง lint/test/build** — หลังแก้ `<script>` ให้ตรวจ syntax โดยดึงบล็อก
  script ไปรันผ่าน `node` (เช่น `new Function(scriptSource)`) เพราะ script ที่พังจะ
  เงียบในหน้า static
- **Deploy:** GitHub Pages, branch `main` / root — push `main` แล้วเผยแพร่ที่
  https://nidss.github.io/suvip/

## Conventions

- คอมเมนต์ในโค้ดเขียนเป็น **ไทยหรืออังกฤษเท่านั้น** — ห้ามใช้อักษรญี่ปุ่น
- รักษาสไตล์ single-file ที่กระชับ (handler สั้น, helper `el(id)`, `esc()` สำหรับ
  ข้อความผู้ใช้ที่ interpolate)
- ไอคอนใช้ inline SVG จาก [Lucide](https://lucide.dev) (เช่น chevron-up /
  chevron-down ของปุ่มยุบ/ขยายบัตร) — `stroke="currentColor"` เพื่อรับสีจาก CSS
