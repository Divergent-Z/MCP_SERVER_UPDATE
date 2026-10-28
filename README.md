# MCP Server Progress Updates

พื้นที่เก็บบันทึกความคืบหน้าของโครงการ MCP โดยส่งรายงานไปยัง Discord webhook และบันทึกประวัติใน GitHub

## ปลายทางรายงาน

- Discord: ส่งข้อความสถานะสั้น ๆ ไปยัง webhook ที่กำหนดใน `DISCORD_WEBHOOK_URL`
- GitHub: เพิ่มรายการลง `reports/progress.md` บน branch `main`
- ตารางรายวัน: 03:00 น. เวลา `Asia/Bangkok` (UTC+7) โดยสรุปสถานะ checklist จาก `PLAN.md`

## การส่งรายงาน

จาก workspace `/home/devg/MCP` ใช้คำสั่งต่อไปนี้เพื่อส่งความคืบหน้าของขั้นตอนสำคัญ:

```bash
python3 /home/devg/MCP/report_progress.py --message "สรุปความคืบหน้าที่ต้องการรายงาน"
```

ส่งสรุป checklist รายวันด้วยคำสั่ง:

```bash
python3 /home/devg/MCP/report_progress.py --daily
```

สคริปต์อ่านค่าจาก `.env` ใน workspace โดยใช้ชื่อตัวแปร `DISCORD_WEBHOOK_URL`, `GITHUB_REPO` และ `GITHUB_TOKEN` เท่านั้น ห้ามใส่ค่า secret ใน README, commit, log หรือข้อความรายงาน ตัวแปร GitHub ต้องมีสิทธิ์อ่านและเขียน Contents ของ repository บน branch `main`.

ผลการส่งของ Discord และ GitHub แสดงแยกกัน หากปลายทางใดล้มเหลว คำสั่งจะคืนสถานะผิดพลาดเพื่อให้ตรวจสอบและส่งซ้ำได้

## ตารางเวลารายวัน

มี systemd user timer กำหนดเวลา 03:00 น. เวลาไทยไว้ใน workspace แต่ยังไม่ได้ติดตั้งหรือเปิดใช้งาน การเปิด timer ต้องตรวจการตั้งค่า `.env` และสิทธิ์ GitHub ก่อน ดูขั้นตอนใน [คู่มือรายงาน](../REPORTING.md)

## ข้อควรระวัง

- รายงานต้องเป็นสถานะโครงการที่จำเป็นต่อการติดตามงาน ห้ามใส่ credentials, ข้อมูลส่วนบุคคล หรือลูกค้า
- ทดสอบโดย mock HTTP เมื่อไม่ต้องการส่งข้อความหรือเขียน GitHub จริง
- อย่าอ้างว่ารายงานถูกส่งครบทั้งสองที่ หากผลลัพธ์ระบุว่าปลายทางหนึ่งล้มเหลว

---

## การสื่อสารระหว่าง AI (AI Secretary ↔ AI SERVER)

> ส่วนนี้กำหนดโดย **เลขาส่วนตัวของพี่ฆัง** (`secretary / workspace AI`) เพื่อให้ AI ฝั่ง SERVER (`MCP_SERVER_UPDATE`) อ่านคำสั่ง นโยบาย และรูปแบบรายงานจากเลขาได้ถูกต้อง

### ช่องทางการสื่อสาร (2-way)

| ทิศทาง | ช่องทาง | รูปแบบ | ข้อมูลที่ต้องส่ง |
|---|---|---|---|
| SERVER → SECRETARY | `reports/progress.md` (Git commit) | `timestamp + bullet + status` (`§3.2` ใน `AGENTS.md`) | Prototype status / test results / blockers / config changes / security check |
| SECRETARY → SERVER | `AGENTS.md` (repo file) + workspace clone (`~/Documents/secretary/server-respon/MCP_SERVER_UPDATE/`) | Markdown + frontmatter (`§4.1` ใน `AGENTS.md`) | คำสั่งทำงาน / นโยบายความปลอดภัย / รูปแบบการขออนุมัติ |

### รูปแบบข้อความ (Standard Format — ใช้ใน `reports/progress.md` และ commit message)

```
## YYYY-MM-DD HH:MM:SS +07

<สรุปสถานะสั้น 1-3 บรรทัด>

- [หัวข้อ]: <สถานะ> (+ รายละเอียดสั้น)
- ...

Blockers / ข้อจำกัด: <รายการ>
ขั้นตอนถัดไป (รออนุมัติจากพี่ฆัง): <รายการ>
Secret Confirm: ไม่บันทึก `.env` / `GITHUB_TOKEN` / `DISCORD_WEBHOOK_URL` ใน commit/log/README
```

### ข้อบังคับเพิ่มเติม (จาก `AGENTS.md`)

- **อ่าน `AGENTS.md`** ใน workspace (`/home/devg/MCP`) ทุกครั้งก่อนเริ่มงาน
- **ไม่บันทึก secret** ใน `README`, `AGENTS.md`, `progress.md`, commit message, log (`S1` ใน `AGENTS.md` §2.2)
- **ไม่อ้างว่ารายงานถูกส่งครบ** — ตรวจผลลัพธ์ของ `report_progress.py` ก่อนสรุป (`S2`)
- **ไม่ใช้ privileged workaround** (`S5`) — รอ `administrator-approved installation` สำหรับ `Traefik` / TLS
- **รายงาน blockers และขั้นตอนรออนุมัติทุกครั้ง** (`§4.1` ใน `AGENTS.md`)

> ดูรายละเอียดบทบาท นโยบาย รูปแบบการขออนุมัติ และ checklist การบำรุงรักษาในไฟล์ `AGENTS.md` ของ repo นี้
