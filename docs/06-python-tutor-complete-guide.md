# 06 — คู่มือใช้ Python Tutor ไล่การทำงานของ QUANT ทั้งโปรเจกต์

คู่มือนี้เป็นบทเรียนกลางสำหรับสมาชิกทุกคน ใช้โค้ดต้นฉบับในโฟลเดอร์ `Stock-Investment-Risk-Simulator-PE104-main-2/` เป็นหลัก และใช้ Python Tutor เพื่อมองเห็นการทำงานทีละบรรทัด

> **ข้อจำกัดที่ต้องเข้าใจก่อน:** Python Tutor รับโค้ด Python เป็นชุดเดียว ไม่ได้เปิดโปรเจกต์หลายไฟล์แล้วติดตามการ import ข้ามไฟล์ให้ครบเหมือน VS Code debugger ดังนั้นเราจะแบ่งเรียนเป็น 2 แบบ:
> 1. **ภาพรวมการไหลข้ามไฟล์:** ใช้โค้ดจำลองที่ย่อให้เหลือเฉพาะการเรียกฟังก์ชัน เพื่อเห็นภาพทั้งระบบ
> 2. **การทำงานจริงภายในฟังก์ชัน:** คัดลอกฟังก์ชันจากไฟล์ต้นฉบับมาทดลองทีละฟังก์ชัน พร้อมโค้ดเรียกฟังก์ชันและข้อมูลตัวอย่าง

อย่าเอาโค้ดทั้ง 4 ไฟล์มาต่อกันตรง ๆ แล้วคาดหวังว่าจะทำงานได้ทันที เพราะมี import, ไลบรารีภายนอก, การอ่านไฟล์ และเงื่อนไขเริ่มโปรแกรมที่ Python Tutor อาจไม่รองรับ

---

## A. เปิด Python Tutor

1. เปิด https://pythontutor.com/
2. เลือก **Python 3**
3. วางโค้ดจากบทเรียนแต่ละชุดในหัวข้อด้านล่าง
4. กด **Visualize Execution**
5. ใช้ **Forward** เพื่อเดินหน้า และ **Back** เพื่อย้อนกลับ
6. สังเกตบรรทัดที่กำลังทำงาน, Frames, Objects และ Output

ความหมาย:
- **Code:** บรรทัดที่กำลังทำงาน
- **Frames:** ฟังก์ชันที่กำลังทำงานและตัวแปรเฉพาะในฟังก์ชันนั้น
- **Objects:** list, dictionary และข้อมูลที่อยู่ในหน่วยความจำ
- **Output:** ข้อความที่โปรแกรมพิมพ์ออกมา

## B. แผนผังการทำงานจริงของ QUANT

เมื่อผู้ใช้เลือกเมนู 1 หรือ 3 เส้นทางหลักเป็นดังนี้:

```text
main.py
  main()
    └── run_analysis()
          ├── รับ ticker / เงินลงทุน / จำนวนวัน / จำนวนรอบ
          ├── data_analysis.py
          │     └── load_stock_data()  [โหมดออนไลน์]
          │        หรือ load_sample_data() [โหมดตัวอย่าง]
          ├── data_analysis.py
          │     └── calculate_daily_returns(prices)
          ├── simulation.py
          │     ├── calculate_volatility(daily_returns)
          │     └── run_monte_carlo(daily_returns, ...)
          ├── risk_report.py
          │     └── calculate_risk(simulation_paths, ...)
          │           ├── calculate_percentile(...)
          │           └── calculate_maximum_drawdown(...) หลายเส้นทาง
          └── risk_report.py
                └── generate_report(analysis, report_path)
```

เส้นทางเมนูอื่น:
- เมนู 2: `main() → view_previous_report() → อ่านไฟล์ risk_report.txt`
- เมนู 0: `main() → return 0` เพื่อออกจากโปรแกรม
- เมนู 3: `main() → run_analysis("AAPL", use_sample=True)` ใช้ข้อมูล CSV ตัวอย่าง ไม่ต้องดาวน์โหลดจาก Yahoo Finance

## C. เข้าใจข้อมูลที่ส่งต่อระหว่างไฟล์

| จุดส่งต่อ | ข้อมูลที่ส่ง | ผู้รับ |
|---|---|---|
| โหลดข้อมูล | list ของ dictionary เช่น `{"date": "...", "ticker": "AAPL", "close": 100.0}` | `run_analysis()` |
| สกัดราคา | list ของราคา เช่น `[100, 102, 101]` | `calculate_daily_returns()` |
| ผลตอบแทน | list เช่น `[0.02, -0.0098]` | volatility และ Monte Carlo |
| ความผันผวน | dictionary ที่มี mean, variance, daily_std_dev, annualized_volatility | `run_analysis()` |
| ผลจำลอง | list ซ้อน list: แต่ละรอบมีมูลค่าพอร์ตตั้งแต่วันเริ่มถึงวันสุดท้าย | `calculate_risk()` |
| ผลความเสี่ยง | dictionary เช่น average_final_value, var, average_mdd | `run_analysis()` |
| ข้อมูลรวม | dictionary ชื่อ `analysis` | `generate_report()` |
| รายงาน | string ของรายงาน และบันทึกลงไฟล์ TXT | `run_analysis()` แสดงผล |

**จุดสำคัญ:** Python Tutor แสดงการส่งค่า argument และ return ได้ดี แต่การเรียกข้ามไฟล์จริงจะต้องจำลองเป็นโค้ดชุดเดียว หรือแยกดูทีละฟังก์ชัน

---

## D. ชุดที่ 1 — ดูภาพรวมการเรียกข้ามทั้ง 4 โมดูล

คัดลอกชุดนี้ไป Python Tutor ก่อน เป็นโค้ดย่อเพื่อจำลองเส้นทางของ QUANT ไม่ใช่โค้ด production ทั้งหมด

```python
def load_stock_data(ticker):
    print("data_analysis: load_stock_data")
    return [{"close": 100}, {"close": 102}, {"close": 101}]

def calculate_daily_returns(prices):
    print("data_analysis: calculate_daily_returns")
    return [prices[i] / prices[i-1] - 1 for i in range(1, len(prices))]

def calculate_volatility(returns):
    print("simulation: calculate_volatility")
    return {"annualized_volatility": 0.25}

def run_monte_carlo(returns, investment):
    print("simulation: run_monte_carlo")
    return [[investment, investment * 1.05],
            [investment, investment * 0.90]]

def calculate_risk(paths, investment):
    print("risk_report: calculate_risk")
    final_values = [path[-1] for path in paths]
    return {"best": max(final_values), "worst": min(final_values)}

def generate_report(analysis):
    print("risk_report: generate_report")
    return "Report: " + str(analysis)

def run_analysis():
    ticker = "AAPL"
    investment = 10000
    stock_data = load_stock_data(ticker)
    prices = [row["close"] for row in stock_data]
    returns = calculate_daily_returns(prices)
    volatility = calculate_volatility(returns)
    paths = run_monte_carlo(returns, investment)
    risk = calculate_risk(paths, investment)
    analysis = {"ticker": ticker, **volatility, **risk}
    report = generate_report(analysis)
    print(report)

def main():
    run_analysis()

main()
```

### จุดที่ต้องหยุดดู
1. ที่ `main()`: ดูว่าเรียก `run_analysis()` อย่างไร
2. เข้า `load_stock_data()`: ดู argument `ticker` และค่าที่ return
3. กลับ `run_analysis()`: ดูว่า return กลายเป็น `stock_data`
4. เข้า `calculate_daily_returns()`: ดู list ราคาและ list ผลตอบแทน
5. เข้า `run_monte_carlo()`: ดูว่า returns และ investment ถูกส่งเข้าไป
6. เข้า `calculate_risk()`: ดู list ซ้อน list ที่รับเข้ามา
7. ก่อน `generate_report()`: ดู dictionary `analysis` ว่ารวมข้อมูลจากหลายขั้นตอนแล้ว
8. เมื่อจบ: ดูว่า report กลับมาที่ `run_analysis()` และถูก print

ตอบตัวเองให้ได้: **ฟังก์ชันไหนเป็นคนสร้างข้อมูล? ฟังก์ชันไหนเป็นคนรับข้อมูล? ฟังก์ชันไหน return ข้อมูลกลับ?**

---

## E. ชุดที่ 2 — ฝึกฟังก์ชันจากโค้ดจริงทีละตัว

หลักการทุกครั้ง:
1. เปิดไฟล์ที่ระบุใน GitHub หรือ VS Code
2. คัดลอกเฉพาะฟังก์ชันที่ต้องการศึกษา
3. วางฟังก์ชันนั้นใน Python Tutor
4. เพิ่มโค้ดทดลองเรียกฟังก์ชันไว้ด้านล่าง
5. ถ้าฟังก์ชันใช้ `math`, `random`, `statistics`, `os` หรือ `datetime` ให้คัดลอก import ที่จำเป็นมาด้วย
6. อย่าคัดลอกส่วน `if __name__ == "__main__":` มาทดลอง เว้นแต่บทเรียนระบุให้ทำ

### E1 — main.py: ตรวจ ticker
- ตำแหน่ง: `get_ticker()` ประมาณบรรทัด 12
- คัดลอกฟังก์ชัน แล้วเพิ่ม `print(get_ticker())`
- Python Tutor อาจไม่เหมาะกับการทดสอบ `input()` แบบโต้ตอบ ให้เปลี่ยน input ชั่วคราวเป็นค่าคงที่ในสำเนาทดลอง หรือข้ามการ visualize ฟังก์ชันรับ input นี้
- คำถาม: ทำไมใช้ `.strip()` และ `.upper()`? ทำไมมี `while True`?

### E2 — main.py: ตรวจตัวเลข
- ตำแหน่ง: `get_positive_float(prompt)` บรรทัดประมาณ 26 และ `get_positive_integer(prompt)` บรรทัดประมาณ 38
- ทั้งคู่รับค่าจาก `input()` จึงให้ฝึกอ่านโค้ดและทดสอบใน terminal จริงเป็นหลัก
- คำถาม: `float()` ต่างจาก `int()` อย่างไร? `ValueError` เกิดเมื่อใด? ทำไมต้องตรวจค่ามากกว่า 0?

### E3 — main.py: อ่านข้อมูลตัวอย่าง
- ตำแหน่ง: `load_sample_data(sample_path, ticker="AAPL")` บรรทัดประมาณ 50
- ฟังก์ชันนี้อ่านไฟล์ CSV จริง จึงควรทดสอบใน VS Code/Terminal ด้วยไฟล์ `sample_data/stock_prices.csv` มากกว่า Python Tutor
- ใน Python Tutor ให้ใช้ตัวอย่าง list ของ records เพื่อฝึกการกรอง ticker, ตรวจข้อมูล และเรียงวันที่
- คำถาม: ทำไมต้องมี `seen_dates`? ทำไมข้ามแถวที่ข้อมูลผิด?

### E4 — data_analysis.py: ปรับ ticker
- ตำแหน่ง: `normalize_ticker(ticker)` บรรทัดประมาณ 11
- โค้ดทดลอง:
  ```python
  def normalize_ticker(ticker):
      return ticker.strip().upper().removesuffix(".BK")

  print(normalize_ticker(" ptt.bk "))
  print(normalize_ticker("aapl"))
  ```
- คาดว่าจะได้ `PTT` และ `AAPL`
- คำถาม: เหตุใด `PTT.BK` จึงถูกเก็บเป็น `PTT` ในระบบ?

### E5 — data_analysis.py: คำนวณผลตอบแทนรายวัน
- ตำแหน่ง: `calculate_daily_returns(prices)` บรรทัดประมาณ 304
- คัดลอกฟังก์ชันเต็ม พร้อม `import math`
- โค้ดทดลอง:
  ```python
  prices = [100, 110, 99]
  print(calculate_daily_returns(prices))
  ```
- คาดหวัง: `[0.1, -0.1]`
- กด Forward ดู `previous_price`, `current_price`, `daily_return` และ list `daily_returns`
- คำถาม: ทำไมราคามี 3 ค่า แต่ผลตอบแทนมี 2 ค่า? ทำไมเริ่ม loop ที่ index 1?

### E6 — simulation.py: คำนวณความผันผวน
- ตำแหน่ง: `calculate_volatility(returns)` บรรทัดประมาณ 7
- คัดลอกฟังก์ชันเต็ม พร้อม `import math`
- โค้ดทดลอง:
  ```python
  returns = [0.01, -0.02, 0.015]
  result = calculate_volatility(returns)
  print(result)
  ```
- สังเกต `mean_return`, `squared_deviations`, `variance`, `daily_std_dev`, `annualized_volatility`
- คำถาม: ทำไม variance หารด้วย `n - 1`? ทำไม annualized volatility คูณ `sqrt(252)`?

### E7 — simulation.py: Monte Carlo
- ตำแหน่ง: `run_monte_carlo(...)` บรรทัดประมาณ 66
- คัดลอกฟังก์ชันเต็ม พร้อม `import random` และ `import math`
- โค้ดทดลอง:
  ```python
  returns = [0.01, -0.02, 0.015]
  paths = run_monte_carlo(returns, 10000, 3, 2, seed=42)
  print(paths)
  ```
- เริ่มจากใช้ 2 simulations และ 3 trading days เพื่อให้เห็น list ซ้อน list ชัดเจน
- ดู `rng`, `all_simulations`, `portfolio_values`, `current_value`, `daily_return`
- คำถาม: ทำไมแต่ละ path มี 4 ค่า ทั้งที่จำลอง 3 วัน? seed มีไว้ทำอะไร?

### E8 — risk_report.py: Maximum Drawdown
- ตำแหน่ง: `calculate_maximum_drawdown(path)` บรรทัดประมาณ 16
- คัดลอกฟังก์ชันเต็ม พร้อม `import math`
- โค้ดทดลอง:
  ```python
  path = [100, 120, 90, 110, 80, 130]
  print(calculate_maximum_drawdown(path))
  ```
- ดู `running_peak`, `current_value`, `drawdown`, `maximum_drawdown`
- คำถาม: จุดสูงสุดเดิมคืออะไร? ถ้าพอร์ตทำจุดสูงสุดใหม่ running_peak เปลี่ยนเมื่อใด?

### E9 — risk_report.py: Percentile
- ตำแหน่ง: `calculate_percentile(sorted_values, confidence_level)` บรรทัดประมาณ 69
- คัดลอกฟังก์ชันเต็ม พร้อม `import math`
- โค้ดทดลอง:
  ```python
  values = [10, 20, 30, 40, 50]
  print(calculate_percentile(values, 0.95))
  ```
- ดู `position`, `lower_index`, `upper_index`, `fraction`
- คำถาม: ทำไมต้องเรียงข้อมูลก่อน? ทำไมบางครั้งต้อง interpolate หรือคำนวณค่าระหว่างสองตำแหน่ง?

### E10 — risk_report.py: รวมการประเมินความเสี่ยง
- ตำแหน่ง: `calculate_risk(...)` บรรทัดประมาณ 146
- คัดลอกฟังก์ชัน `calculate_risk`, `calculate_percentile` และ `calculate_maximum_drawdown` มาด้วย พร้อม imports `statistics` และ `math`
- โค้ดทดลอง:
  ```python
  paths = [
      [10000, 10500, 11000],
      [10000, 9500, 9000],
      [10000, 10200, 9800]
  ]
  result = calculate_risk(paths, 10000, 0.95)
  print(result)
  ```
- หยุดดู `final_values`, `loss_count`, `probability_of_loss`, `losses`, `var_value`, `maximum_drawdowns`, `risk_results`
- คำถาม: ทำไม `calculate_risk` ต้องเรียก helper functions อีกสองตัว?

### E11 — risk_report.py: สร้างรายงาน
- ตำแหน่ง: `generate_report(analysis, output_path)` บรรทัดประมาณ 268
- ฟังก์ชันนี้เขียนไฟล์ลงเครื่อง จึงควรทดสอบจริงใน VS Code/Terminal ด้วย path ชั่วคราวที่ปลอดภัย
- ใน Python Tutor ให้ศึกษาเฉพาะการสร้าง `report_lines`, `report_text` และ return โดยตัดส่วนเขียนไฟล์ออกในสำเนาฝึก
- คำถาม: ทำไมรับ dictionary แทนการรับ argument จำนวนมาก? ทำไมต้องตรวจ `required_keys`?

### E12 — main.py: ตัวควบคุมหลัก
- ตำแหน่ง: `run_analysis(...)` บรรทัดประมาณ 87
- นี่คือจุดเชื่อมสำคัญที่สุด เพราะเรียกใช้ทุกโมดูล
- อย่าคัดลอกฟังก์ชันนี้ทั้งก้อนลง Python Tutor โดยไม่เตรียม dependency: มันใช้ imports จาก 3 ไฟล์, file system, CSV และอาจเชื่อม Yahoo Finance
- ให้เปิดฟังก์ชันจริงใน VS Code แล้วใช้แผนผัง B และชุดจำลอง D ไล่ทีละคำสั่ง
- คำถาม: `prices` ถูกสร้างจาก `stock_data` อย่างไร? `analysis` รวม dictionary สองชุดอย่างไร? ถ้าไม่มี stock_data โปรแกรมทำอะไร?

### E13 — main.py: เมนู
- ตำแหน่ง: `main()` บรรทัดประมาณ 175 และ entry point บรรทัดท้ายไฟล์
- `main()` ใช้ `input()` และ loop เมนู จึงเหมาะกับการทดสอบใน Terminal มากกว่า Python Tutor
- จำให้ได้ว่า `if __name__ == "__main__": main()` ทำให้ main เริ่มทำงานเมื่อเปิดไฟล์นี้โดยตรง ไม่ใช่เมื่อไฟล์ถูก import จากอีกไฟล์
- คำถาม: เลือก 1, 2, 3 และ 0 แล้วเส้นทางต่างกันอย่างไร?

---

## F. ตารางลำดับเรียนที่แนะนำ

| ลำดับ | บทเรียน | เป้าหมาย |
|---:|---|---|
| 1 | ชุด D ภาพรวมระบบ | เห็นลำดับเรียกฟังก์ชันครบ |
| 2 | E4 normalize_ticker | เข้าใจ string และ return |
| 3 | E5 calculate_daily_returns | เข้าใจ list, loop, index |
| 4 | E6 calculate_volatility | เข้าใจตัวแปรคำนวณและ dictionary |
| 5 | E7 run_monte_carlo | เข้าใจ nested loops และ list ซ้อน list |
| 6 | E8 Maximum Drawdown | เข้าใจการติดตามค่าสูงสุด |
| 7 | E9 Percentile | เข้าใจ index และ interpolation |
| 8 | E10 calculate_risk | เข้าใจการเรียก helper และรวมผล |
| 9 | E11 generate_report | เข้าใจ dictionary และ string |
| 10 | E3 load_sample_data | เข้าใจ CSV และ file handling |
| 11 | E1–E2 input validation | เข้าใจ input, while, try/except |
| 12 | E12 run_analysis | เชื่อมทุกโมดูล |
| 13 | E13 main | เข้าใจเมนูและจุดเริ่มต้นจริง |

---

## G. วิธีพิสูจน์ว่าเข้าใจจริง

หลังดูแต่ละฟังก์ชัน สมาชิกทุกคนต้องตอบ 5 ข้อนี้โดยไม่อ่านคำตอบ:

1. ฟังก์ชันนี้เริ่มทำงานเมื่อใครเรียก?
2. รับ input อะไร และเป็นชนิดข้อมูลอะไร?
3. ทำงานทีละขั้นอย่างไร?
4. return อะไร และใครเป็นผู้รับค่ากลับ?
5. ถ้าแก้ตัวแปรหรือ return นี้ จะกระทบฟังก์ชันใดต่อ?

ตัวอย่างคำตอบที่ดี:
`run_monte_carlo()` รับ list ของ daily returns, เงินลงทุนเริ่มต้น, จำนวนวัน และจำนวนรอบ จากนั้นสุ่มผลตอบแทนมาคำนวณมูลค่าพอร์ตในแต่ละวัน สร้าง list ของแต่ละรอบ แล้วคืน list ซ้อน list ให้ `run_analysis()` ส่งต่อไป `calculate_risk()`

## H. แหล่งโค้ดต้นฉบับ

ใช้ไฟล์ต้นฉบับเหล่านี้เป็นแหล่งอ้างอิง อย่าแก้โค้ดจริงเพียงเพื่อให้ Python Tutor รันได้ ให้สร้างสำเนาสำหรับฝึกแยกต่างหาก

- [main.py](../Stock-Investment-Risk-Simulator-PE104-main-2/main.py)
- [data_analysis.py](../Stock-Investment-Risk-Simulator-PE104-main-2/data_analysis.py)
- [simulation.py](../Stock-Investment-Risk-Simulator-PE104-main-2/simulation.py)
- [risk_report.py](../Stock-Investment-Risk-Simulator-PE104-main-2/risk_report.py)

**หมายเหตุเรื่องเลขบรรทัด:** เลขบรรทัดเป็นตำแหน่ง ณ เวลาจัดทำ หากไฟล์เปลี่ยน ให้ค้นหาชื่อฟังก์ชันด้วย Cmd+F แทนการยึดเลขบรรทัดอย่างเดียว
