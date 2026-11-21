# 02 — คู่มือทำความเข้าใจไฟล์ทั้ง 4

## A. main.py — ศูนย์ควบคุมโปรแกรม

**หน้าที่:** แสดงเมนู รับข้อมูลจากผู้ใช้ เรียกฟังก์ชันจากไฟล์อื่น แล้วส่งผลลัพธ์ต่อไปยังขั้นตอนถัดไป

### ฟังก์ชันหลัก

- `get_ticker()`: รับรหัสหุ้น ตัดช่องว่างด้วย `.strip()` แปลงเป็นตัวพิมพ์ใหญ่ด้วย `.upper()` และตรวจรูปแบบตัวอักษร
- `get_positive_float(prompt)`: รับเลขทศนิยม เช่น เงินลงทุน ใช้ `try/except ValueError` เพื่อให้ผู้ใช้กรอกใหม่เมื่อไม่ใช่ตัวเลข และใช้ `math.isfinite()` ป้องกัน inf/nan
- `get_positive_integer(prompt)`: รับจำนวนเต็มบวก เช่น วันและจำนวนรอบ
- `load_sample_data(sample_path, ticker)`: อ่าน CSV ตัวอย่าง ตรวจ header, ticker, ราคา, วันที่ซ้ำ และเรียงวันที่
- `run_analysis()`: ขั้นตอนหลักของการวิเคราะห์ตั้งแต่โหลดข้อมูลถึงสร้างรายงาน
- `view_previous_report()`: อ่านรายงานที่เคยบันทึกไว้
- `main()`: วนแสดงเมนูจนผู้ใช้เลือก 0

### จุดสำคัญใน run_analysis()

1. รับค่าจากผู้ใช้
2. กำหนด confidence level = 0.95
3. เลือกแหล่งข้อมูล: sample CSV หรือ Yahoo Finance/cache
4. สร้าง `prices` จาก `record["close"]`
5. คำนวณ `daily_returns`
6. ตรวจว่ามีผลตอบแทนอย่างน้อย 2 ค่า
7. เรียกคำนวณ volatility
8. เรียก Monte Carlo
9. เรียกประเมิน risk
10. รวมผลใน `analysis`
11. เรียก `generate_report()`

### ทำไมใช้ `if __name__ == "__main__":`

ส่วนนี้ทำให้ `main()` ทำงานเมื่อเปิดไฟล์ main.py โดยตรง แต่ไม่ทำงานเพียงเพราะไฟล์อื่น import main.py

---

## B. data_analysis.py — ข้อมูลหุ้นและผลตอบแทน

**หน้าที่:** ดึงข้อมูลราคาปิดย้อนหลังจาก Yahoo Finance จัดเก็บ/อ่าน CSV cache ตรวจสอบข้อมูล และคำนวณผลตอบแทนรายวัน

### ฟังก์ชันหลัก

- `normalize_ticker(ticker)`: ทำ ticker ให้เป็นรูปแบบมาตรฐาน เช่น ตัด suffix `.BK` ออกจากชื่อที่ใช้เก็บ
- `load_stock_data(file_path, ticker)`: ดาวน์โหลดข้อมูล 5 ปี รายวัน ด้วย yfinance; ถ้าดาวน์โหลดไม่ได้จะพยายามอ่าน CSV cache
- `calculate_daily_returns(prices)`: คำนวณผลตอบแทนระหว่างราคาปิดที่อยู่ติดกัน

### สูตร Daily Return

[
r_t = \frac{P_t}{P_{t-1}} - 1
]

- (P_t) = ราคาปิดวันนี้
- (P_{t-1}) = ราคาปิดวันก่อน
- ค่า 0.01 หมายถึง +1%; ค่า -0.02 หมายถึง -2%

### คำศัพท์ Python ที่เจอในไฟล์นี้

- `csv.DictReader`: อ่าน CSV โดยอ้างอิงชื่อคอลัมน์
- `csv.DictWriter`: เขียนข้อมูล CSV จาก dictionary
- `set()`: เก็บวันที่ที่เคยพบเพื่อกันข้อมูลซ้ำ
- `try/except/finally`: จัดการข้อผิดพลาดและคืนค่า logger
- `tempfile.NamedTemporaryFile`: สร้างไฟล์ชั่วคราว
- `os.replace()`: แทนที่ไฟล์ cache ด้วยไฟล์ใหม่
- `yf.Ticker(symbol).history()`: ขอประวัติราคาหุ้นจาก Yahoo Finance

**ข้อควรเข้าใจ:** Yahoo Finance อาจไม่มีข้อมูลหรือเชื่อมต่อไม่ได้ โปรแกรมจึงมีแนวทางใช้ข้อมูล cache เดิมแทน

---

## C. simulation.py — ความผันผวนและ Monte Carlo

**หน้าที่:** คำนวณสถิติความผันผวนจากผลตอบแทนในอดีต และสร้างเส้นทางจำลองมูลค่าพอร์ตหลายรอบ

### calculate_volatility(returns)

1. ตรวจชนิดข้อมูลและจำนวนตัวอย่าง
2. คำนวณค่าเฉลี่ยผลตอบแทนรายวัน
3. หาค่าคลาดเคลื่อนจากค่าเฉลี่ยและยกกำลังสอง
4. คำนวณ sample variance โดยหารด้วย (n-1)
5. ถอดรากเป็น daily standard deviation
6. คูณด้วย (sqrt{252}) เพื่อ annualize

[
s^2 = \frac{\sum_{i=1}^{n}(r_i-\bar r)^2}{n-1}
]

[
s = \sqrt{s^2}, \qquad
\sigma_{annual}=s\sqrt{252}
]

### run_monte_carlo(...)

- `random.Random(seed)`: สร้างตัวสุ่มเฉพาะฟังก์ชันนี้
- `rng.choice(returns)`: สุ่มผลตอบแทนหนึ่งค่าจากอดีต โดยเลือกซ้ำได้ (with replacement)
- `current_value * (1 + daily_return)`: ทบต้นผลตอบแทนรายวัน
- `portfolio_values`: เก็บมูลค่าพอร์ตตั้งแต่วันเริ่มต้นถึงวันสุดท้าย
- `all_simulations`: เก็บทุกเส้นทางจำลอง

**Historical Bootstrap:** สุ่มจากผลตอบแทนจริงในอดีต ไม่ได้สุ่มจากการแจกแจงปกติโดยตรง

**seed:** ทำให้การรันซ้ำด้วย input และ seed เดิมได้ลำดับสุ่มเดิม ช่วยให้ทดสอบและสาธิตได้

---

## D. risk_report.py — ประเมินความเสี่ยงและสร้างรายงาน

**หน้าที่:** วิเคราะห์ผลจำลองและแปลงผลเป็นรายงาน TXT

### ฟังก์ชันหลัก

- `calculate_maximum_drawdown(path)`: หาการลดลงจากจุดสูงสุดสะสมของพอร์ต
- `calculate_percentile(sorted_values, confidence_level)`: หา percentile ด้วย interpolation ระหว่างข้อมูลที่เรียงแล้ว
- `calculate_risk(simulation_paths, initial_investment, confidence_level)`: คำนวณผลลัพธ์ความเสี่ยงจากทุกเส้นทาง
- `generate_report(analysis, output_path)`: สร้างข้อความรายงานและเขียนลงไฟล์

### Maximum Drawdown (MDD)

[
DD_t = 1 - \frac{V_t}{\max_{s\leq t}V_s}
]

MDD คือค่า drawdown ที่มากที่สุดในเส้นทางหนึ่ง ๆ เช่น พอร์ตเคยขึ้นไป 120 แล้วลดเหลือ 90 จะลดลงจาก peak 25%

### Probability of Loss

[
P(loss)=\frac{\text{จำนวนรอบที่มูลค่าสุดท้ายต่ำกว่าเงินเริ่มต้น}}{\text{จำนวนรอบทั้งหมด}}
]

### Value at Risk (VaR) ในโค้ดนี้

สร้าง `losses = initial_investment - final_value` แล้วเรียงจากน้อยไปมาก จากนั้นใช้ percentile ตาม confidence level

**ข้อควรระวังในการอธิบาย:** VaR เป็น percentile ของผลขาดทุนจาก simulation ไม่ใช่การรับประกันว่าจะขาดทุนไม่เกินค่านี้ และผลลัพธ์ขึ้นกับข้อมูลย้อนหลัง/สมมติฐานของแบบจำลอง

### generate_report()

รับ `analysis` ซึ่ง main.py รวมข้อมูลไว้แล้ว จากนั้นประกอบ `report_lines`, รวมด้วย `"\n".join(...)`, สร้างโฟลเดอร์ด้วย `os.makedirs(..., exist_ok=True)`, เขียนไฟล์ UTF-8 และ return ข้อความรายงาน
