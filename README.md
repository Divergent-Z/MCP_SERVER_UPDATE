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
