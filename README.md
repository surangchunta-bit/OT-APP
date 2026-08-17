# Request OT (RM) — HRM Process

แอปขอทำงานล่วงเวลา (OT) ของ Hot Rolling Mill · static site ไฟล์เดียว ไม่ต้อง build

## โครงไฟล์

```
index.html                     ตัวแอปทั้งหมด (HTML + CSS + JS ในไฟล์เดียว)
manifest.webmanifest           ข้อมูลสำหรับติดตั้งเป็นแอปบนมือถือ
sw.js                          service worker — เปิดใช้ออฟไลน์ได้หลังเข้าครั้งแรก
vercel.json                    ตั้งค่า cache header
favicon.ico
icons/
  icon-192.png                 ไอคอนแอป + โลโก้บนหัวแอป
  icon-512.png
  icon-512-maskable.png        สำหรับ Android (ไอคอนถูกครอปเป็นวงกลม/สี่เหลี่ยมมน)
  apple-touch-icon.png         สำหรับ iOS (Add to Home Screen)
  favicon-32.png / favicon-16.png
```

## Deploy ขึ้น Vercel

### วิธีที่ 1 — ลากวางบนเว็บ (ง่ายสุด ไม่ต้องลงอะไร)

1. เข้า https://vercel.com/new
2. เลือก **Deploy** แบบ third-party / drag & drop แล้วลากทั้งโฟลเดอร์นี้ลงไป
3. Framework Preset เลือก **Other** · Build Command เว้นว่าง · Output Directory เว้นว่าง
4. กด Deploy รอประมาณ 20 วินาที

### วิธีที่ 2 — Vercel CLI

```bash
npm i -g vercel
cd ot-vercel
vercel          # deploy preview
vercel --prod   # deploy production
```

### วิธีที่ 3 — ผ่าน GitHub (แนะนำถ้าจะแก้บ่อย)

```bash
cd ot-vercel
git init && git add -A && git commit -m "Request OT (RM)"
git remote add origin <URL repo ของคุณ>
git push -u origin main
```
แล้วที่ Vercel กด **Import Project** เลือก repo นี้ · Framework = Other · ไม่ต้องใส่ build command
หลังจากนั้นทุกครั้งที่ push จะ deploy ให้อัตโนมัติ

## ติดตั้งลงมือถือ

- **Android / Chrome** — เปิดลิงก์ที่ได้ → เมนู ⋮ → "ติดตั้งแอป" / "เพิ่มไปที่หน้าจอหลัก"
- **iPhone / Safari** — เปิดลิงก์ → ปุ่มแชร์ → "เพิ่มไปยังหน้าจอโหลม" (Add to Home Screen)

ไอคอนที่ขึ้นบนหน้าจอหลักคือรูป HRM Process ที่ให้มา

## เวลาแก้ไฟล์แล้ว deploy ใหม่

เปลี่ยนเลข `CACHE` ใน `sw.js` (เช่น `ot-request-v5` → `ot-request-v6`) ทุกครั้งที่แก้ `index.html`
ไม่งั้นเครื่องที่ติดตั้งไว้แล้วจะยังเห็นเวอร์ชันเก่าจาก cache

## หมายเหตุ

- แอปต้องเปิดผ่าน https (Vercel ให้มาอยู่แล้ว) เพราะปุ่มแชร์เข้า LINE ใช้ `navigator.share` ซึ่งทำงานเฉพาะบน https
- เพลงพื้นหลังสังเคราะห์เสียงเองด้วย Web Audio ไม่ต้องโหลดไฟล์เสียงจากที่ไหน
- html2canvas และฟอนต์โหลดจาก CDN — เข้าครั้งแรกต้องมีเน็ต หลังจากนั้น service worker cache ไว้ให้แล้ว
