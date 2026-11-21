# 05 — Code Location Map: ฟังก์ชันและโค้ดอยู่ไฟล์ไหน?

เอกสารนี้เป็นแผนที่สำหรับเปิดโค้ดให้เจอเร็วระหว่างเรียนและสอบปากเปล่า อ้างอิงไฟล์ฉบับอธิบายภาษาไทยในโฟลเดอร์ annotated_code/ ณ วันที่จัดทำเอกสาร

> หมายเหตุ: เลขบรรทัดอาจเปลี่ยนได้ถ้ามีการเพิ่ม/ลบคอมเมนต์หรือแก้โค้ด ใช้ Cmd+F ค้นหาชื่อฟังก์ชันได้เสมอ

## 1. เปิดไฟล์ฉบับอธิบายภาษาไทย

| ไฟล์ | หน้าที่หลัก | เปิดไฟล์ |
|---|---|---|
| main_thai.py | เมนู รับค่าจากผู้ใช้ และควบคุมลำดับการทำงาน | [เปิดไฟล์](../annotated_code/main_thai.py) |
| data_analysis_thai.py | ดาวน์โหลด/อ่านข้อมูลหุ้น และคำนวณผลตอบแทนรายวัน | [เปิดไฟล์](../annotated_code/data_analysis_thai.py) |
| simulation_thai.py | คำนวณความผันผวนและจำลอง Monte Carlo | [เปิดไฟล์](../annotated_code/simulation_thai.py) |
| risk_report_thai.py | คำนวณความเสี่ยงและสร้างรายงาน | [เปิดไฟล์](../annotated_code/risk_report_thai.py) |

## 2. ฟังก์ชันทั้งหมดอยู่ตรงไหน?

### A. main_thai.py — จุดควบคุมหลักของโปรแกรม

| ฟังก์ชัน | บรรทัดเริ่มต้น | ทำหน้าที่ |
|---|---:|---|
| get_ticker() | 23 | รับรหัสหุ้น ตรวจรูปแบบ และแปลงเป็นตัวพิมพ์ใหญ่ |
| get_positive_float(prompt) | 48 | รับตัวเลขทศนิยมที่มากกว่าศูนย์ เช่น เงินลงทุน |
| get_positive_integer(prompt) | 69 | รับจำนวนเต็มบวก เช่น จำนวนวันและจำนวนรอบจำลอง |
| load_sample_data(sample_path, ticker="AAPL") | 90 | อ่านข้อมูลหุ้นตัวอย่างจาก CSV และตรวจสอบข้อมูล |
| run_analysis(ticker_override=None, use_sample=False) | 159 | เรียกขั้นตอนวิเคราะห์ทั้งหมด ตั้งแต่โหลดข้อมูลจนสร้างรายงาน |
| view_previous_report() | 278 | ตรวจสอบและแสดงรายงานที่เคยบันทึกไว้ |
| main() | 321 | แสดงเมนู รับตัวเลือก และเรียกฟังก์ชันที่เหมาะสม |

**ถ้าอาจารย์ถาม:** เมนูหลักอยู่ main_thai.py บรรทัด 321; รับเงินลงทุนผ่าน get_positive_float() บรรทัด 48; จุดประสานงานโมดูลอยู่ run_analysis() บรรทัด 159; โหลด CSV ตัวอย่างอยู่ load_sample_data() บรรทัด 90

### B. data_analysis_thai.py — จัดการข้อมูลหุ้น

| ฟังก์ชัน | บรรทัดเริ่มต้น | ทำหน้าที่ |
|---|---:|---|
| normalize_ticker(ticker) | 23 | ทำความสะอาดรหัสหุ้น เช่น ตัดช่องว่างและแปลงเป็นตัวพิมพ์ใหญ่ |
| load_stock_data(file_path, ticker) | 37 | ดาวน์โหลดข้อมูลจาก Yahoo Finance หรืออ่านข้อมูลจาก CSV/cache |
| calculate_daily_returns(prices) | 499 | คำนวณผลตอบแทนรายวันจากราคาปิด |

**ถ้าอาจารย์ถาม:** ดาวน์โหลดราคาหุ้นอยู่ data_analysis_thai.py ฟังก์ชัน load_stock_data() บรรทัด 37; คำนวณ Daily Return อยู่ calculate_daily_returns() บรรทัด 499; จัดรูปแบบ Ticker อยู่ normalize_ticker() บรรทัด 23

### C. simulation_thai.py — ความผันผวนและ Monte Carlo

| ฟังก์ชัน | บรรทัดเริ่มต้น | ทำหน้าที่ |
|---|---:|---|
| calculate_volatility(returns) | 14 | คำนวณค่าเฉลี่ย ความแปรปรวน ส่วนเบี่ยงเบนมาตรฐาน และ Annualized Volatility |
| run_monte_carlo(...) | 105 | สุ่มผลตอบแทนในอดีตแบบ Historical Bootstrap เพื่อจำลองเส้นทางมูลค่าพอร์ตหลายรอบ |

**ถ้าอาจารย์ถาม:** คำนวณ Volatility อยู่ simulation_thai.py ฟังก์ชัน calculate_volatility() บรรทัด 14; จำลอง Monte Carlo อยู่ run_monte_carlo() บรรทัด 105; การกำหนด seed อยู่ภายใน run_monte_carlo()

### D. risk_report_thai.py — ประเมินความเสี่ยงและสร้างรายงาน

| ฟังก์ชัน | บรรทัดเริ่มต้น | ทำหน้าที่ |
|---|---:|---|
| calculate_maximum_drawdown(path) | 25 | คำนวณการลดลงสูงสุดจากจุดสูงสุดก่อนหน้า |
| calculate_percentile(sorted_values, confidence_level) | 101 | หาค่าเปอร์เซ็นไทล์จากข้อมูลที่เรียงลำดับแล้ว |
| calculate_risk(...) | 214 | คำนวณค่าเฉลี่ย กำไร/ขาดทุน Probability of Loss, VaR และ Drawdown |
| generate_report(analysis, output_path) | 410 | จัดรูปแบบผลวิเคราะห์และบันทึกรายงานลงไฟล์ |

**ถ้าอาจารย์ถาม:** Maximum Drawdown อยู่ risk_report_thai.py บรรทัด 25; Percentile อยู่บรรทัด 101; VaR และ Probability of Loss อยู่ calculate_risk() บรรทัด 214; สร้างรายงานอยู่ generate_report() บรรทัด 410

## 3. ลำดับการทำงานข้ามไฟล์ (Data Flow)

ผู้ใช้
→ main_thai.py: main()
→ main_thai.py: run_analysis()
→ data_analysis_thai.py: load_stock_data()
→ data_analysis_thai.py: calculate_daily_returns()
→ simulation_thai.py: calculate_volatility()
→ simulation_thai.py: run_monte_carlo()
→ risk_report_thai.py: calculate_risk()
   → calculate_percentile()
   → calculate_maximum_drawdown()
→ risk_report_thai.py: generate_report()
→ แสดงผลและบันทึกรายงาน

ลำดับโดยสรุป:
1. main() รับคำสั่งจากผู้ใช้
2. run_analysis() เป็นผู้ประสานงานหลัก
3. load_stock_data() โหลดราคาหุ้น
4. calculate_daily_returns() แปลงราคาปิดเป็นผลตอบแทนรายวัน
5. calculate_volatility() สรุปความผันผวน
6. run_monte_carlo() สร้างเส้นทางจำลองหลายรอบ
7. calculate_risk() นำผลจำลองไปคำนวณความเสี่ยง โดยเรียก helper functions
8. generate_report() สร้างและบันทึกรายงาน

## 4. ถ้าต้องแก้โปรแกรม ต้องไปไฟล์ไหน?

| ต้องการเปลี่ยนอะไร | จุดที่ควรเริ่มดู |
|---|---|
| เพิ่ม/แก้ตัวเลือกเมนู | main_thai.py → main() |
| เปลี่ยนวิธีรับหรือเช็ก Input | main_thai.py → ฟังก์ชันรับ Input |
| เปลี่ยนแหล่งหรือวิธีโหลดข้อมูลหุ้น | data_analysis_thai.py → load_stock_data() |
| เปลี่ยนสูตร Daily Return | data_analysis_thai.py → calculate_daily_returns() |
| เปลี่ยนสูตร Volatility | simulation_thai.py → calculate_volatility() |
| เปลี่ยนวิธีสุ่มผลตอบแทน/จำลอง | simulation_thai.py → run_monte_carlo() |
| เปลี่ยนสูตร Maximum Drawdown | risk_report_thai.py → calculate_maximum_drawdown() |
| เปลี่ยนวิธีหา Percentile/VaR | risk_report_thai.py → calculate_percentile() และ calculate_risk() |
| เพิ่ม/แก้ข้อความในรายงาน | risk_report_thai.py → generate_report() |

**ระวัง:** การเปลี่ยนชื่อฟังก์ชัน พารามิเตอร์ หรือชื่อ key ใน Dictionary อาจกระทบไฟล์ที่เรียกใช้หรืออ่านค่านั้นด้วย ก่อนแก้ให้ค้นหาชื่อดังกล่าวทั้ง Repository

## 5. วิธีค้นหาฟังก์ชันอย่างรวดเร็วใน VS Code (Mac)

1. เปิดไฟล์ที่ต้องการ
2. กด Cmd + F
3. พิมพ์ชื่อ เช่น def run_monte_carlo
4. กด Enter เพื่อไปยังตำแหน่งที่พบ

ถ้าต้องการค้นหาทั้งโปรเจกต์ ให้กด Cmd + Shift + F แล้วพิมพ์ชื่อ เช่น calculate_risk

## 6. วิธีตอบอาจารย์ให้ครบ

อย่าตอบแค่ชื่อไฟล์ ให้ตอบ 4 ส่วน:
1. **ชื่อไฟล์** — โค้ดอยู่ที่ไหน
2. **ชื่อฟังก์ชัน** — ส่วนที่ทำงานคืออะไร
3. **หน้าที่** — รับอะไร แล้วทำอะไร
4. **ผลที่ส่งต่อ** — คืนค่าอะไร และใครนำไปใช้ต่อ

ตัวอย่างคำตอบ:

“การจำลอง Monte Carlo อยู่ในไฟล์ simulation.py ฟังก์ชัน run_monte_carlo() รับผลตอบแทนในอดีต เงินลงทุนเริ่มต้น จำนวนวัน และจำนวนรอบจำลอง จากนั้นสุ่มผลตอบแทนมาคำนวณมูลค่าพอร์ตในแต่ละวัน แล้วคืนค่าเป็น list ของเส้นทางการจำลอง เพื่อนำไปให้ calculate_risk() ในไฟล์ risk_report.py วิเคราะห์ความเสี่ยงต่อ”
