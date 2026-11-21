# QUANT Learning Thai 🇹🇭

คลังเรียนรู้โปรเจกต์ **QUANT – Stock Investment Risk Simulator** สำหรับเตรียมสอบปากเปล่าและ Live Coding

> Repository นี้เป็นสำเนาเพื่อการศึกษา แยกจาก Repository โปรเจกต์ทีมต้นฉบับ การเพิ่มคำอธิบายหรือแบบฝึกหัดในที่นี้จะไม่แก้ไขโค้ดของทีม

## เริ่มต้นตรงนี้

1. อ่าน [ภาพรวมระบบและเส้นทางข้อมูล](docs/01-system-overview.md)
2. อ่าน [คู่มือทำความเข้าใจไฟล์ทั้ง 4](docs/02-file-by-file.md)
3. อ่าน [พื้นฐาน Python และแนวคิดสำคัญ](docs/03-python-and-finance-basics.md)
4. เปิด [แผนที่ตำแหน่งโค้ดและฟังก์ชัน](docs/05-code-location-map.md) เพื่อดูว่าโค้ดแต่ละส่วนอยู่ไฟล์ไหนและบรรทัดใด
5. ฝึกตอบ [แนวข้อสอบปากเปล่าและ Live Coding](docs/04-oral-exam-practice.md)

## โค้ดฉบับอธิบายภาษาไทย (อ่านควบคู่กับต้นฉบับ)

ไฟล์ในโฟลเดอร์ [annotated_code](annotated_code/) เป็นสำเนาเพื่อการศึกษา โดยเพิ่มคอมเมนต์ภาษาไทยก่อนบรรทัดคำสั่งและอธิบายจุดสำคัญของการทำงาน โดยไม่แก้ไขไฟล์ต้นฉบับของทีม

- [main_thai.py](annotated_code/main_thai.py)
- [data_analysis_thai.py](annotated_code/data_analysis_thai.py)
- [simulation_thai.py](annotated_code/simulation_thai.py)
- [risk_report_thai.py](annotated_code/risk_report_thai.py)

> หมายเหตุ: ไฟล์เหล่านี้ทำไว้เพื่ออ่านและเรียนรู้เป็นหลัก หากต้องการรันโปรแกรมจริง ให้ใช้ไฟล์ต้นฉบับในโฟลเดอร์ด้านล่าง เพราะไฟล์ฉบับศึกษาอาจมีคำอธิบายแทรกอยู่ใน expression หลายบรรทัด และควรทดสอบก่อนนำไปใช้งาน

## โค้ดต้นฉบับที่อัปโหลด

ไฟล์ต้นฉบับอยู่ในโฟลเดอร์ [Stock-Investment-Risk-Simulator-PE104-main-2](Stock-Investment-Risk-Simulator-PE104-main-2/)

- [main.py](Stock-Investment-Risk-Simulator-PE104-main-2/main.py) — เมนูหลักและเชื่อมทุกโมดูล
- [data_analysis.py](Stock-Investment-Risk-Simulator-PE104-main-2/data_analysis.py) — โหลดข้อมูลหุ้นและคำนวณผลตอบแทนรายวัน
- [simulation.py](Stock-Investment-Risk-Simulator-PE104-main-2/simulation.py) — คำนวณความผันผวนและจำลอง Monte Carlo
- [risk_report.py](Stock-Investment-Risk-Simulator-PE104-main-2/risk_report.py) — ประเมินความเสี่ยงและสร้างรายงาน
- [README ต้นฉบับ](Stock-Investment-Risk-Simulator-PE104-main-2/README.md)
- [แผนทดสอบต้นฉบับ](Stock-Investment-Risk-Simulator-PE104-main-2/docs/test_plan.md)

## ลำดับการทำงานของโปรแกรม

`ผู้ใช้ → main.py → data_analysis.py → simulation.py → risk_report.py → รายงาน TXT`

1. รับ ticker, เงินลงทุน, จำนวนวัน และจำนวนรอบจำลอง
2. โหลดราคาหุ้นจาก Yahoo Finance หรือ CSV ตัวอย่าง
3. แปลงราคาปิดเป็นผลตอบแทนรายวัน
4. คำนวณความผันผวน
5. จำลองเส้นทางมูลค่าพอร์ตด้วย Historical Bootstrap Monte Carlo
6. คำนวณ Probability of Loss, VaR และ Maximum Drawdown
7. รวมผลเป็น dictionary และสร้างรายงาน

## ข้อตกลงในการเรียน

- ไม่แก้ไข Repository ต้นฉบับของทีม
- เริ่มจากเข้าใจข้อมูลเข้า (input) → กระบวนการ (process) → ข้อมูลออก (output)
- อ่านฟังก์ชันพร้อมตามดูว่าใครเรียกใช้ และใครรับค่าที่ return
- คำอธิบายทางการเงินเป็นแบบจำลองเพื่อการศึกษา ไม่ใช่คำแนะนำการลงทุน
