# 01 — ภาพรวมระบบและเส้นทางข้อมูล

## 1. โปรแกรมนี้ทำอะไร

QUANT เป็นโปรแกรมแบบ Terminal ที่นำราคาหุ้นย้อนหลังมาคำนวณผลตอบแทน วัดความผันผวน จำลองผลลัพธ์ของพอร์ตหลายสถานการณ์ แล้วสรุปความเสี่ยงออกมาเป็นรายงานข้อความ

## 2. แผนภาพการไหลของข้อมูล

```text
ผู้ใช้
  |
  v
main.py
  |-- รับ ticker / เงินลงทุน / จำนวนวัน / จำนวน simulations
  |
  |-- โหลดข้อมูล
  |     |-- โหมดออนไลน์: data_analysis.load_stock_data()
  |     |-- โหมดตัวอย่าง: main.load_sample_data()
  |
  v
stock_data = list ของ dictionary
  |
  v
prices = list ของราคาปิด (float)
  |
  v
data_analysis.calculate_daily_returns(prices)
  |
  v
daily_returns = list ของผลตอบแทนรายวัน
  |
  +------------------------------+
  |                              |
  v                              v
simulation.calculate_volatility  simulation.run_monte_carlo
  |                              |
  v                              v
volatility_results               simulation_paths
                                 |
                                 v
                         risk_report.calculate_risk
                                 |
                                 v
                           risk_results
                                 |
                main.py รวม dictionary ด้วย **
                                 |
                                 v
                     risk_report.generate_report
                                 |
                                 v
                    reports/risk_report.txt
```

## 3. รูปแบบข้อมูลสำคัญ

| ชื่อตัวแปร | ชนิดข้อมูล | ตัวอย่าง/ความหมาย |
|---|---|---|
| `stock_data` | list[dict] | ข้อมูลแต่ละวัน เช่น date, ticker, close |
| `prices` | list[float] | ราคาปิดหุ้นตามลำดับวัน |
| `daily_returns` | list[float] | ผลตอบแทนระหว่างวัน เช่น 0.02 = +2% |
| `volatility_results` | dict | ค่าเฉลี่ย variance และ volatility |
| `simulation_paths` | list[list[float]] | หลายเส้นทางจำลอง แต่ละเส้นมีมูลค่าพอร์ตทุกวัน |
| `risk_results` | dict | ค่าเฉลี่ยปลายทาง โอกาสขาดทุน VaR และ MDD |
| `analysis` | dict | รวมข้อมูลผู้ใช้ ผล volatility และผล risk |
| `report_text` | str | ข้อความรายงานที่แสดงและบันทึกลงไฟล์ |

## 4. ตัวอย่างการแปลงข้อมูลทีละขั้น

สมมติราคาปิด 3 วันคือ

```python
prices = [100, 110, 99]
```

ผลตอบแทนวันที่ 2:

```python
(110 / 100) - 1  # 0.10 หรือ +10%
```

ผลตอบแทนวันที่ 3:

```python
(99 / 110) - 1  # -0.10 หรือ -10%
```

ดังนั้น

```python
daily_returns = [0.10, -0.10]
```

สังเกตว่า ราคา 3 ค่าให้ผลตอบแทน 2 ค่าเสมอ เพราะผลตอบแทนต้องเทียบราคาวันนี้กับวันก่อนหน้า

## 5. ใครเรียกใคร

| ผู้เรียก | ฟังก์ชันที่ถูกเรียก | สิ่งที่ส่งให้ |
|---|---|---|
| `main.py` | `load_stock_data()` | path ของ CSV และ ticker |
| `main.py` | `load_sample_data()` | path ของ sample CSV และ ticker |
| `main.py` | `calculate_daily_returns()` | list ของราคาปิด |
| `main.py` | `calculate_volatility()` | list ของ daily returns |
| `main.py` | `run_monte_carlo()` | returns, เงินเริ่มต้น, วัน, จำนวนรอบ |
| `main.py` | `calculate_risk()` | เส้นทางจำลอง, เงินเริ่มต้น, confidence |
| `main.py` | `generate_report()` | dictionary ชื่อ analysis และ path รายงาน |

## 6. จุดที่ต้องจำให้แม่น

- `main.py` เป็นตัวประสานงาน (orchestrator) ไม่ได้คำนวณทุกอย่างเอง
- `data_analysis.py` คืนข้อมูลราคาหุ้นเป็น list ของ dictionaries
- `calculate_daily_returns()` คืน list ของตัวเลข ไม่ใช่ dictionary
- `run_monte_carlo()` คืน list ซ้อน list เพราะมีหลายรอบและหลายวัน
- `calculate_risk()` รับ simulation paths และคืน dictionary
- `analysis` ถูกสร้างใน `main.py` โดยรวม dictionary ด้วย `**`
- `generate_report()` รับ dictionary ที่รวมแล้ว ไม่ได้ไปเรียกคำนวณข้อมูลหุ้นใหม่
