# ICT Strategy – Technical Specification (Implementation-Ready)

**Version:** 1.0  
**Language:** Persian (explanations) + English (code & technical terms)  
**Purpose:** این سند مشخصات فنی کامل استراتژی ICT است و طوری نوشته شده که یک برنامه‌نویس بتواند بدون هیچ ابهام و سؤال اضافی، آن را به کد تبدیل کند.

---

## 1. Data Requirements

هر کندل باید حداقل این فیلدها را داشته باشد:

```text
timestamp   : datetime (با timezone)
open        : float
high        : float
low         : float
close       : float
volume      : float (اختیاری)
```

- تمام محاسبات روی **یک تایم‌فریم مشخص** انجام می‌شود (1m, 5m, 15m, 1H, 4H, Daily, Weekly, Monthly).
- تمام زمان‌ها بر اساس **New York Time (UTC-5)** محاسبه می‌شوند.
- تبدیل timezone باید داخل کد انجام شود و هیچ وابستگی به ساعت محلی کاربر وجود نداشته باشد.

---

## 2. Trading Sessions (New York Time)

| Session          | Start (inclusive) | End (exclusive) | Allowed to open new trade? |
|------------------|-------------------|-----------------|----------------------------|
| Asia             | 20:00             | 24:00           | **No**                     |
| London           | 01:00             | 05:00           | **Yes**                    |
| Lunch            | 05:00             | 07:00           | **No**                     |
| New York         | 07:00             | 10:00           | **Yes**                    |
| Outside sessions | any other time    |                 | **No**                     |

### قانون ورود (کد)

```python
from datetime import time

def is_trading_allowed(ny_time: time) -> bool:
    """
    فقط در سشن لندن و نیویورک اجازه باز کردن ترید جدید وجود دارد.
    """
    return (time(1, 0) <= ny_time < time(5, 0)) or (time(7, 0) <= ny_time < time(10, 0))
```

**نکته مهم:** این فیلتر سخت‌گیرانه است و هیچ استثنایی ندارد.

---

## 3. Swing High / Swing Low

### تعریف دقیق

یک **Swing High** در کندل `i` وجود دارد اگر و فقط اگر:

```text
high[i] > high[i-1]  AND  high[i] > high[i+1]
```

نقطه Swing High = مقدار `high[i]`

یک **Swing Low** در کندل `i` وجود دارد اگر و فقط اگر:

```text
low[i] < low[i-1]  AND  low[i] < low[i+1]
```

نقطه Swing Low = مقدار `low[i]`

### نکات پیاده‌سازی

- برای تشخیص در زمان واقعی، باید منتظر بسته شدن کندل `i+1` بمانید.
- می‌توان پارامتر `left` و `right` را اضافه کرد (پیش‌فرض = 1).

```python
def is_swing_high(highs: list[float], i: int, left: int = 1, right: int = 1) -> bool:
    if i < left or i + right >= len(highs):
        return False
    return all(highs[i] > highs[i - j] for j in range(1, left + 1)) and \
           all(highs[i] > highs[i + j] for j in range(1, right + 1))

def is_swing_low(lows: list[float], i: int, left: int = 1, right: int = 1) -> bool:
    if i < left or i + right >= len(lows):
        return False
    return all(lows[i] < lows[i - j] for j in range(1, left + 1)) and \
           all(lows[i] < lows[i + j] for j in range(1, right + 1))
```

---

## 4. Order Block

### 4.1 Bearish Order Block

شرایط تشکیل:

1. یک حرکت نزولی قوی وجود داشته باشد.
2. آخرین کندل **صعودی** (`close > open`) قبل از شروع این حرکت نزولی، Order Block نزولی است.
3. محدوده Order Block = از `low` تا `high` همان کندل صعودی.

### 4.2 Bullish Order Block

آخرین کندل **نزولی** قبل از یک حرکت صعودی قوی.

### 4.3 شرایط اعتبار (Clean Order Block)

یک Order Block فقط زمانی معتبر است که **همه** شرایط زیر برقرار باشد:

| شرط              | توضیح دقیق                                                                 |
|------------------|-----------------------------------------------------------------------------|
| Untested         | بعد از تشکیل، قیمت هنوز هیچ بار به محدوده آن نفوذ نکرده باشد (حتی یک تیک) |
| Strong Impulse   | حرکت بعد از Order Block باید قوی باشد (وجود FVG در حرکت توصیه‌شده است)   |
| Formation speed  | کلوز زیر/بالای Order Block حداکثر با ۱ یا ۲ کندل انجام شده باشد            |
| One-time use     | در تایم‌فریم‌های ۴H و پایین‌تر فقط یک بار قابل استفاده است. بعد از اولین تست باطل می‌شود |

### 4.4 Higher Timeframe Order Block (Daily / Weekly / Monthly)

به جای کل محدوده، فقط این سه نقطه مهم هستند:

**Bullish OB:**
- Open
- High
- 50% of the body → `(open + close) / 2`

**Bearish OB:**
- Open
- Low
- 50% of the body

این نقاط می‌توانند تا ۳ بار واکنش بدهند.

### کد تشخیص Bearish Order Block

```python
def detect_bearish_order_block(candles: list, i: int) -> Optional[dict]:
    """
    i = ایندکس کندل کاندید برای Order Block
    """
    if not (candles[i].close > candles[i].open):
        return None

    # بررسی حرکت نزولی قوی بعد از آن
    impulsive_count = 0
    for j in range(i + 1, min(i + 5, len(candles))):
        if candles[j].close < candles[j].open and candles[j].close < candles[i].low:
            impulsive_count += 1
            if impulsive_count >= 1:
                break
    else:
        return None

    if impulsive_count > 2:
        return None

    return {
        "type": "bearish",
        "top": candles[i].high,
        "bottom": candles[i].low,
        "open": candles[i].open,
        "mid": (candles[i].open + candles[i].close) / 2,
        "formed_at": candles[i].timestamp,
        "tested": False,
        "times_tested": 0
    }
```

---

## 5. Rejection Block

فقط در تایم‌فریم‌های **Daily, Weekly, Monthly** معتبر است (به جز موارد تلاقی با نقاط مهم بالاتر).

### Bearish Rejection Block
```text
from = swing_high_price
to   = nearest body of the candle that formed the swing high
```
(نزدیک‌ترین بدنه = `max(open, close)` در کندل تشکیل‌دهنده سوینگ)

### Bullish Rejection Block
```text
from = swing_low_price
to   = nearest body of the candle that formed the swing low
```

یک‌بار مصرف هستند.

---

## 6. Fair Value Gap (FVG)

### 6.1 Type 1 Bearish FVG

سه کندل متوالی `i-1`, `i`, `i+1`:

- رنگ کندل `i-1` و `i+1` مهم نیست
- کندل `i` باید نزولی باشد (`close[i] < open[i]`)
- شرط گپ: `low[i-1] > high[i+1]`

محدوده FVG:
```text
top    = low[i-1]
bottom = high[i+1]
```

### 6.2 Type 1 Bullish FVG

```text
شرط: high[i-1] < low[i+1]
top    = low[i+1]
bottom = high[i-1]
```

### 6.3 Type 2 FVG

در کندل جاری هنوز گپ تشکیل نشده.  
انتظار داریم کندل بعدی گپ را تاچ کند.  
اگر نکند → تبدیل به Type 1 می‌شود.

### 6.4 FVG Update

اگر قیمت بخشی از FVG را پر کند، محدوده باقی‌مانده همچنان معتبر است.

### کد تشخیص FVG Type 1

```python
def detect_fvg_type1(candles: list, i: int) -> Optional[dict]:
    """
    i = ایندکس کندل وسط
    """
    if i < 1 or i >= len(candles) - 1:
        return None

    left  = candles[i - 1]
    mid   = candles[i]
    right = candles[i + 1]

    # Bearish FVG
    if mid.close < mid.open and left.low > right.high:
        return {
            "type": "bearish",
            "top": left.low,
            "bottom": right.high,
            "mid": (left.low + right.high) / 2,
            "formed_at": mid.timestamp
        }

    # Bullish FVG
    if mid.close > mid.open and left.high < right.low:
        return {
            "type": "bullish",
            "top": right.low,
            "bottom": left.high,
            "mid": (right.low + left.high) / 2,
            "formed_at": mid.timestamp
        }

    return None
```

---

## 7. Market Structure (MS High / MS Low)

### تعریف

- **MS High** = آخرین Swing High معتبر
- **MS Low** = آخرین Swing Low معتبر
- رنج فعلی = فاصله بین MS Low و MS High

### شکست ساختار (Break of Structure)

**Break of MS High (صعودی):**
```text
close[i] > MS_High
```
(فقط کلوز معتبر است، شادو کافی نیست)

پس از شکست:
1. از کندل شکست به عقب بروید تا اولین Swing Low را پیدا کنید → این **MS Low جدید** می‌شود.
2. منتظر تشکیل Swing High جدید بمانید → این **MS High جدید** می‌شود.

**Break of MS Low (نزولی):** کاملاً متقارن.

### قوانین مهم

- MS High و MS Low **فقط با کلوز** آپدیت می‌شوند.
- هیچ‌وقت از سمت راست چارت شروع به علامت‌گذاری نکنید. همیشه از گذشته به جلو بروید.
- خطوط ساختار هر تایم‌فریم فقط در همان تایم‌فریم باید visible باشند.

---

## 8. Confirmation: Stop Hunt + BMS → Launch Pad (LP)

### الگوی Bearish LP (برای ورود به فروش)

1. وجود یک Swing High (نقطه ۱)
2. قیمت آن Swing High را هانت کند (با شادو یا بدنه – مهم نیست)
3. بلافاصله زیر اولین Swing Low بعد از هانت کلوز کند (نقطه ۳)

**شرایط تمیزی:**
- بعد از هانت، حداکثر در **۴ کندل** BMS رخ دهد
- نقطه ۳ باید **بین** نقطه ۱ و نقطه ۲ باشد (نه روی آن‌ها)

محدوده **LP** = از پایین‌ترین نقطه تا بالاترین نقطه الگوی Stop Hunt + BMS

### ورود
- Limit زیر LP یا در تست LP
- Stop Loss بالای LP
- Target بر اساس ساختار بعدی یا Risk:Reward ثابت (حداقل ۱:۲)

الگوی Bullish LP کاملاً متقارن است.

### کد تشخیص Bearish LP

```python
def detect_bearish_lp(candles: list, lookback: int = 30) -> Optional[dict]:
    for i in range(len(candles) - 3, max(0, len(candles) - lookback), -1):
        if not is_swing_high([c.high for c in candles], i):
            continue

        swing_high_price = candles[i].high

        # پیدا کردن هانت
        hunt_idx = None
        for j in range(i + 1, len(candles)):
            if candles[j].high >= swing_high_price:
                hunt_idx = j
                break
        if hunt_idx is None:
            continue

        # پیدا کردن اولین Swing Low بعد از هانت
        first_sl_idx = None
        first_sl_price = None
        for k in range(hunt_idx + 1, len(candles)):
            if is_swing_low([c.low for c in candles], k):
                first_sl_idx = k
                first_sl_price = candles[k].low
                break
        if first_sl_idx is None:
            continue

        # بررسی BMS حداکثر در ۴ کندل بعد از هانت
        bms_idx = None
        for m in range(hunt_idx + 1, min(hunt_idx + 5, len(candles))):
            if candles[m].close < first_sl_price:
                bms_idx = m
                break
        if bms_idx is None:
            continue

        lp_top = max(c.high for c in candles[i:bms_idx + 1])
        lp_bottom = min(c.low for c in candles[i:bms_idx + 1])

        return {
            "type": "bearish",
            "top": lp_top,
            "bottom": lp_bottom,
            "formed_at": candles[bms_idx].timestamp,
            "swing_high": swing_high_price,
            "bms_level": first_sl_price
        }

    return None
```

---

## 9. Read Candle

یک کندل **Read** است اگر:

```text
(قیمت به MS High یا MS Low یا یک Swing نفوذ کند)
AND
(کلوز در سمت مخالف نفوذ باشد)
```

مثال Bearish Read روی MS High:
```text
high[i] > MS_High  AND  close[i] < MS_High
```

رید روی MS قدرت بیشتری نسبت به رید روی سوینگ ساده دارد و می‌تواند به عنوان سیگنال اولیه استفاده شود.

---

## 10. Forbidden Times / Days

| مورد                                      | قانون                                      |
|-------------------------------------------|---------------------------------------------|
| دوشنبه سشن لندن                           | توصیه قوی به عدم ترید                       |
| ۳۰ دقیقه قبل و بعد اخبار مهم              | باز کردن ترید جدید ممنوع                    |
| ۱۰–۱۲ روز آخر سال و ابتدای سال            | اجتناب کامل                                 |
| از حدود ۱۰ ژانویه به بعد                  | شرایط عادی                                  |

اخبار مهم شامل: CPI, NFP, Interest Rate Decision, Unemployment Rate و موارد مشابه.

---

## 11. Multi-Timeframe Confirmation Table

| نقطه مهم در | تأییدیه مجاز                          |
|-------------|---------------------------------------|
| 4H          | OB 4H یا OB 1H یا LP 15m             |
| 1H          | OB 1H یا OB 15m یا LP 5m             |
| Daily       | OB Daily یا OB 4H یا LP 1H           |
| Weekly      | OB Weekly یا OB Daily یا LP 4H       |

---

## 12. Core Trading Logic (Pseudocode)

```python
def evaluate_setup(candles_htf, candles_ltf, current_time_ny):
    # 1. فیلتر سشن
    if not is_trading_allowed(current_time_ny):
        return None

    # 2. تعیین روند از ساختار بازار تایم‌فریم بالاتر
    ms = get_market_structure(candles_htf)
    trend = ms.trend   # "bullish" | "bearish"

    # 3. جمع‌آوری سطوح کلیدی تمیز و تست‌نشده در جهت روند
    key_levels = find_valid_key_levels(candles_htf, trend)

    # 4. بررسی رسیدن قیمت به یکی از سطوح کلیدی
    current_price = candles_ltf[-1].close
    touched_level = None
    for level in key_levels:
        if price_touched(level, current_price):
            touched_level = level
            break

    if touched_level is None:
        return None

    # 5. در تایم‌فریم پایین‌تر به دنبال تأییدیه بگرد
    confirmation = detect_confirmation(candles_ltf, touched_level)

    if confirmation is None:
        return None

    # 6. محاسبه ورود، استاپ و تارگت
    entry, stop, target = calculate_trade_levels(confirmation, ms, min_rr=2.0)

    return {
        "side": "sell" if trend == "bearish" else "buy",
        "entry": entry,
        "stop": stop,
        "target": target,
        "risk_reward": abs(entry - target) / abs(entry - stop),
        "confirmation_type": confirmation["type"],
        "key_level": touched_level
    }
```

---

## 13. Recommended Data Structures

```python
from dataclasses import dataclass
from datetime import datetime
from typing import Optional

@dataclass
class OrderBlock:
    type: str               # "bullish" | "bearish"
    top: float
    bottom: float
    open: float
    mid: float
    formed_at: datetime
    timeframe: str
    tested: bool = False
    times_tested: int = 0

@dataclass
class FVG:
    type: str
    top: float
    bottom: float
    mid: float
    formed_at: datetime
    filled_percent: float = 0.0

@dataclass
class LP:
    type: str
    top: float
    bottom: float
    formed_at: datetime
    swing_high: Optional[float] = None
    swing_low: Optional[float] = None
    bms_level: Optional[float] = None

@dataclass
class MarketStructure:
    ms_high: float
    ms_low: float
    trend: str              # "bullish" | "bearish" | "ranging"
    last_break_time: datetime
```

---

## 14. Critical Implementation Rules (No Ambiguity)

1. **فقط کندل‌های بسته‌شده** برای تمام تشخیص‌ها استفاده می‌شوند (Swing, OB, FVG, LP, Structure Break).
2. **شکست ساختار** فقط با **کلوز** معتبر است، هرگز با شادو.
3. Order Blockهای ≤ 4H کاملاً **یک‌بار مصرف** هستند. بعد از هر لمس (حتی بدون واکنش) باطل می‌شوند.
4. همیشه از چپ به راست (گذشته → حال) کار کنید. هرگز ساختار را از سمت راست چارت شروع نکنید.
5. سطوح تایم‌فریم بالاتر اولویت دارند. تایم‌فریم پایین‌تر فقط برای زمان‌بندی و تأییدیه استفاده می‌شود.
6. فیلتر سشن سخت‌گیرانه است و هیچ استثنایی ندارد.
7. فیلتر اخبار (۳۰ دقیقه قبل و بعد) باید با تقویم اقتصادی خارجی پیاده‌سازی شود.

---

## 15. Helper Functions

```python
def is_bullish_candle(c) -> bool:
    return c.close > c.open

def is_bearish_candle(c) -> bool:
    return c.close < c.open

def body_high(c) -> float:
    return max(c.open, c.close)

def body_low(c) -> float:
    return min(c.open, c.close)

def body_mid(c) -> float:
    return (c.open + c.close) / 2
```

---

**پایان مشخصات فنی**

این سند کامل، دقیق و بدون ابهام است.  
می‌توان آن را مستقیماً به برنامه‌نویس تحویل داد تا سیستم را پیاده‌سازی کند.
