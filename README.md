# แอปลิงก์สั้น (บทที่ 2)

แอปทำงานเหมือนบทที่ 1 ทุกอย่าง แต่จัดโครงใหม่ให้พร้อมสำหรับการ deploy หลาย environment (dev/staging/prod)

## โครงไฟล์

```
app/
  main.py            จุดเริ่ม: อ่าน config → ตั้ง log → ประกอบ repository + service + api
  config.py          อ่านค่าตั้งค่าจาก env var
  logging_setup.py   log แบบ JSON บรรทัดละเหตุการณ์ ออก stdout
  api/               ชั้น api: รับ/ตอบ HTTP (links.py = REST API, web.py = หน้าเว็บและ redirect)
  service/           ชั้น service: กฎทางธุรกิจ (ตรวจ URL, สุ่มรหัส, นับคลิก)
  repository/        ชั้น repository: base.py = interface, sqlite.py = เก็บใน SQLite
```

แต่ละชั้นคุยกับชั้นที่อยู่ติดกันเท่านั้น: api → service → repository
ถ้าวันหนึ่งเปลี่ยนที่เก็บข้อมูลเป็น Redis (บทที่ 4) ก็แค่เพิ่ม repository ใหม่ ไม่ต้องแตะ api หรือ service

## ค่าตั้งค่า (env var)

| ตัวแปร | ค่าเริ่มต้น | ความหมาย |
|---|---|---|
| `BASE_URL` | `http://localhost:8000` | ส่วนหน้าของลิงก์สั้นที่ตอบกลับ |
| `DATABASE_PATH` | `links.db` | ที่อยู่ไฟล์ SQLite |
| `LOG_LEVEL` | `INFO` | ระดับ log ขั้นต่ำ (`DEBUG`, `INFO`, `WARNING`, `ERROR`) |

`PORT` (ค่าเริ่มต้น 8000) ใช้โดย `make run` เท่านั้น ตัวแอปไม่ได้อ่านค่านี้

## รันบนเครื่อง (ใน WSL)

ต้องมีคำสั่ง `make` (ถ้ายังไม่มี: `sudo apt update && sudo apt install -y make`)

```bash
cp .env.example .env     # แก้ค่าในไฟล์ .env ได้ตามต้องการ
make install             # สร้าง .venv และติดตั้ง dependency ที่ pin ไว้
make run                 # รันแอป log เป็น JSON ออกหน้าจอ
```

ลองรันด้วยค่าอื่นโดยไม่แก้โค้ด:

```bash
make run BASE_URL=https://sho.rt PORT=8001 LOG_LEVEL=WARNING   # ค่าบน command line ชนะค่าใน .env
```

ตรวจสุขภาพแอป (ตรวจว่าเปิดฐานข้อมูลได้ด้วย ถ้าไม่ได้จะตอบ 503):

```bash
curl -s localhost:8000/healthz
```

คำสั่งอื่น: `make clean` ลบ .venv, cache และไฟล์ .db · `make test` และ `make lint` ยังเป็นที่ว่างไว้ จะใส่ของจริงในบทที่ 3
