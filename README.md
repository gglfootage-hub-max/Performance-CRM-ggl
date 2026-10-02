# สนามยอดขาย — เว็บวัดผลรายสัปดาห์ของทีมเซลส์

เว็บนี้วางบน **GitHub Pages** (ฟรี) และเก็บข้อมูลที่ซุปกรอกไว้ใน **Google Sheet** ของบริษัท
ซุปเปิดลิงก์แล้วกรอกได้เลย ไม่ต้องมีบัญชี Claude หรือ GitHub แค่ใส่รหัสทีมครั้งแรกครั้งเดียว

```
index.html            หน้าเว็บ
config.js             ตั้งค่า: ลิงก์ API, สีธีม, เดือน, เป้าแต่ละทีม
apps-script/Code.gs   หลังบ้าน (วางใน Google Apps Script ไม่ต้องอัปขึ้น GitHub)
```

---

## ขั้นที่ 1 — ทำหลังบ้านด้วย Google Sheet (ประมาณ 5 นาที)

1. สร้าง Google Sheet ใหม่ ตั้งชื่อเช่น `สนามยอดขาย ต.ค. 69`
2. เมนู **Extensions (ส่วนขยาย) → Apps Script**
3. ลบโค้ดเดิมทิ้ง แล้ววางโค้ดทั้งหมดจากไฟล์ `apps-script/Code.gs`
4. แก้บรรทัด `const PASSCODE = 'เปลี่ยนรหัสตรงนี้';` เป็นรหัสทีมที่ต้องการ แล้วกด 💾 Save
5. ที่แถบด้านบน เลือกฟังก์ชัน **setup** แล้วกด **Run**
   - Google จะขอสิทธิ์ → กด Review permissions → เลือกบัญชี → Advanced → Go to (unsafe) → Allow
   - กลับไปดูที่ Sheet จะมีชีต `Entries` และ `Teams` ขึ้นมา
6. กด **Deploy → New deployment**
   - กดรูปเฟือง ⚙ เลือก **Web app**
   - Execute as: **Me**
   - Who has access: **Anyone**
   - กด **Deploy** แล้ว **คัดลอก Web app URL** (ลงท้ายด้วย `/exec`)

## ขั้นที่ 2 — ใส่ลิงก์ใน config.js

เปิด `config.js` แล้ววาง URL ที่คัดลอกมาลงใน `apiUrl`

```js
apiUrl: "https://script.google.com/macros/s/xxxxxxxx/exec",
```

ถ้ามีรหัสสีม่วงของบริษัท ใส่ที่ `brand` ได้เลย เช่น `brand: "#6A1B9A"`

## ขั้นที่ 3 — ขึ้นเว็บบน GitHub Pages

1. เข้า github.com → ปุ่ม **New** (สร้าง repository)
2. ตั้งชื่อเช่น `sales-race` เลือก **Public** แล้วกด **Create repository**
3. กดลิงก์ **uploading an existing file** แล้วลากไฟล์ `index.html`, `config.js`, `README.md` ไปวาง → กด **Commit changes**
4. ไปที่ **Settings → Pages**
   - Source: **Deploy from a branch**
   - Branch: **main** / **(root)** → **Save**
5. รอ 1–2 นาที รีเฟรชหน้า Settings → Pages จะมีลิงก์เว็บ เช่น
   `https://ชื่อผู้ใช้.github.io/sales-race/`
6. ส่งลิงก์นี้พร้อมรหัสทีมให้ซุปทุกคน 🎉

---

## ใช้งานประจำ

- **ข้อมูลทั้งหมดอยู่ใน Google Sheet** (ชีต Entries) ดู กรอง หรือดาวน์โหลดได้ตามปกติ
- เว็บดึงข้อมูลใหม่อัตโนมัติทุก 30 วินาที
- **เปลี่ยนรหัสทีม:** แก้ `PASSCODE` ใน Apps Script → Save → Deploy → Manage deployments → ✏️ → Version: **New version** → Deploy
  (ต้องทำแบบนี้ทุกครั้งที่แก้โค้ดใน Apps Script ไม่อย่างนั้นเว็บจะยังใช้โค้ดเก่า)

## เริ่มเดือนใหม่

1. สร้าง Google Sheet ใหม่ แล้วทำขั้นที่ 1 อีกรอบ (ได้ URL ใหม่) หรือจะใช้ Sheet เดิมแต่ลบแถวข้อมูลในชีต Entries ก็ได้
2. แก้ `config.js` บน GitHub (กดไฟล์ → ไอคอนดินสอ ✏️)
   - `apiUrl` (ถ้าใช้ Sheet ใหม่), `month`, `storageKey`, `totalDays`
   - `weeks` ช่วงวันที่และวันทำงานสะสมของแต่ละสัปดาห์
   - `target` เป้าทั้งเดือนของแต่ละทีม
3. กด **Commit changes** รอ 1 นาที เว็บจะอัปเดตเอง

## วิธีคิดตัวเลข

| ตัวชี้วัด | สูตร |
|---|---|
| เป้าสะสม | เป้าทั้งเดือน × วันทำงานสะสม ÷ วันทำงานทั้งเดือน |
| %ติดต่อได้ | ได้คุย ÷ โทรออก |
| %คุยจบ | คุยจบ ÷ ได้คุย |
| %Con | ปิดการขาย ÷ คุยจบ |
| Talktime / คน / วัน | Talktime รวม ÷ (จำนวนเซลส์ × วันทำงานของสัปดาห์) |
| ต้องทำวันละ | (เป้าทั้งเดือน − ยอดสะสม) ÷ วันทำงานที่เหลือ |

## แก้ปัญหา

- **ขึ้น "ยังไม่ได้เชื่อมกับ Google Sheet"** → ยังไม่ได้ใส่ `apiUrl` ใน config.js
- **ขึ้น "เชื่อมต่อข้อมูลกลางไม่ได้"** → ตอน Deploy ต้องเลือก Who has access = **Anyone** และ URL ต้องลงท้าย `/exec`
- **ใส่รหัสแล้วขึ้นว่ารหัสไม่ถูกต้อง** → เช็ก `PASSCODE` ใน Apps Script และอย่าลืม Deploy เป็น New version หลังแก้
- **แก้ config.js แล้วเว็บไม่เปลี่ยน** → รอ 1–2 นาทีแล้วกด Ctrl+Shift+R (หรือ Cmd+Shift+R บน Mac)
