# 03 — พื้นฐาน Python และแนวคิดการเงินที่ต้องรู้

## 1. Python ที่ใช้บ่อยในโปรเจกต์

| คำสั่ง | ความหมาย | ตัวอย่างใน QUANT |
|---|---|---|
| `import` | นำเข้า module | `import math` |
| `from ... import ...` | นำเข้าฟังก์ชัน/ชื่อเฉพาะ | `from simulation import run_monte_carlo` |
| `def` | ประกาศฟังก์ชัน | `def calculate_risk(...):` |
| `return` | ส่งค่ากลับผู้เรียก | `return daily_returns` |
| `if/elif/else` | เลือกทำงานตามเงื่อนไข | ตรวจเมนู |
| `for` | วนสมาชิกในข้อมูล | วนผลตอบแทนรายวัน |
| `while` | วนซ้ำตามเงื่อนไข | รับ input จนถูกต้อง |
| `try/except` | จัดการ error | แปลง input เป็น float |
| `list` | เก็บข้อมูลเรียงลำดับ | ราคาหุ้น |
| `dict` | เก็บข้อมูลแบบ key-value | ผลลัพธ์ volatility |
| `set` | เก็บสมาชิกไม่ซ้ำ | วันที่ที่อ่านแล้ว |
| `**some_dict` | กระจาย key-value เข้า dict ใหม่ | รวม volatility และ risk |
| `with open(...)` | เปิดไฟล์พร้อมปิดเมื่อจบ | อ่าน/เขียน CSV/TXT |
| `enumerate(..., start=2)` | วนพร้อมเลขลำดับ | เลขแถว CSV |
| `lambda` | ฟังก์ชันสั้น | ใช้เป็น key ใน sort |
| `os.path.join` | ต่อ path ให้เหมาะกับระบบ | path ของ CSV |
| `os.makedirs(..., exist_ok=True)` | สร้างโฟลเดอร์ถ้ายังไม่มี | cache/reports |

## 2. Function: input → process → return

ตัวอย่าง:

```python
def double(number):
    result = number * 2
    return result

answer = double(5)
```

- `number` คือ parameter (ตัวแปรรับค่า)
- `5` คือ argument (ค่าที่ส่งเข้าไป)
- `result` เป็นตัวแปรภายในฟังก์ชัน
- `return` ส่ง 10 กลับไปยังจุดที่เรียกใช้
- ถ้าไม่มี return ฟังก์ชันจะคืน `None` โดยปริยาย

## 3. List กับ Dictionary

```python
prices = [100.0, 101.0, 99.5]
```

เข้าถึงสมาชิกด้วย index เช่น `prices[0]`

```python
record = {"date": "2026-01-01", "ticker": "AAPL", "close": 100.0}
```

เข้าถึงค่าด้วย key เช่น `record["close"]`

ใน QUANT, `stock_data` คือ list ที่สมาชิกแต่ละตัวเป็น dictionary

## 4. การอ่าน list comprehension

```python
prices = [record["close"] for record in stock_data]
```

อ่านว่า: สร้าง list ใหม่ โดยหยิบค่า close จาก record ทุกตัวใน stock_data

เทียบกับแบบเขียนยาว:

```python
prices = []
for record in stock_data:
    prices.append(record["close"])
```

## 5. การรวม Dictionary ด้วย **

```python
analysis = {
    "ticker": ticker,
    **volatility_results,
    **risk_results,
}
```

หมายถึงสร้าง dictionary ใหม่ ใส่ ticker และนำ key-value ทั้งหมดจากอีกสอง dictionary มาใส่ด้วย

## 6. ความผันผวน (Volatility)

ความผันผวนวัดการกระจายของผลตอบแทนรอบค่าเฉลี่ย ไม่ได้บอกทิศทางว่าหุ้นจะขึ้นหรือลง

- Variance: ค่าเฉลี่ยของ squared deviations (ในที่นี้เป็น sample variance)
- Standard deviation: รากที่สองของ variance
- Annualized volatility: แปลงความผันผวนรายวันเป็นรายปีโดยใช้สมมติฐาน 252 วันทำการ

## 7. Monte Carlo และ Bootstrap

Monte Carlo คือการใช้การสุ่มเพื่อสร้างสถานการณ์จำลองจำนวนมาก

Historical Bootstrap ในโปรเจกต์นี้:
1. มีผลตอบแทนรายวันในอดีต เช่น [0.01, -0.02, 0.005]
2. สุ่มหนึ่งค่าจากชุดนี้ในแต่ละวัน
3. นำผลตอบแทนไปคูณทบกับมูลค่าพอร์ต
4. ทำซ้ำหลายวันเป็นหนึ่งเส้นทาง
5. ทำซ้ำหลายเส้นทาง

คำว่า with replacement หมายถึงสุ่มแล้วค่าที่เคยถูกเลือกยังมีโอกาสถูกเลือกอีก

## 8. Risk metrics

- Probability of Loss: สัดส่วนเส้นทางที่มูลค่าปลายทางต่ำกว่าเงินเริ่มต้น
- VaR: percentile ของการกระจายผลขาดทุนตาม confidence level
- Maximum Drawdown: การลดลงสูงสุดจาก peak ก่อนหน้า
- Average Final Value: ค่าเฉลี่ยมูลค่าพอร์ตตอนจบทุก simulation

## 9. จุดที่อาจถูกถามเชิงวิจารณ์

- 252 คือสมมติฐานจำนวนวันทำการซื้อขายต่อปี ไม่ใช่ 365 วันปฏิทิน
- Historical Bootstrap ใช้ข้อมูลอดีตเป็นตัวแทนการเปลี่ยนแปลงในอนาคต จึงไม่รับประกันว่าจะครอบคลุมเหตุการณ์ใหม่
- seed ช่วยให้ผลสุ่มทำซ้ำได้ แต่ไม่ได้ทำให้แบบจำลองแม่นยำขึ้น
- ผลลัพธ์ขึ้นกับจำนวนข้อมูล จำนวน simulations และช่วงเวลาที่นำมาคำนวณ
