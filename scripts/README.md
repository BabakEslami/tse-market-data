<div dir="rtl">

# اسکریپت‌های پایش

## `test_active_tsetmc_endpoints.py`

این اسکریپت همان ۵۶ endpoint مستندشده در [`../ENDPOINTS.md`](../ENDPOINTS.md) را دوباره تست می‌کند تا مشخص شود کدام‌ها هنوز زنده‌اند یا رفتارشان تغییر کرده — چون این API غیررسمی هیچ SLA یا تعهد پایداری ندارد.

### نصب

```bash
pip install requests openpyxl
```

`openpyxl` اختیاری است — فقط برای شمارش ردیف‌های خروجی Excel (endpoint دیده‌بان بازار legacy) لازم است؛ بدون آن هم بقیه‌ی endpoint ها به‌درستی تست می‌شوند.

### اجرا

```bash
python test_active_tsetmc_endpoints.py
```

با پارامترهای دلخواه (InsCode و تاریخ دیگر):

```bash
python test_active_tsetmc_endpoints.py --inscode 17914401175772326 --date 20260701
```

### خروجی

- گزارش زنده در ترمینال، خط‌به‌خط برای هر endpoint (وضعیت، کد HTTP، زمان پاسخ)
- خلاصه‌ی نهایی: چند endpoint هنوز فعال است و کدام‌ها رگرس کرده‌اند
- دو فایل خروجی:
  - `tsetmc_active_endpoints_test.csv`
  - `tsetmc_active_endpoints_test.json`

### پارامترهای بیشتر

| پارامتر | پیش‌فرض | توضیح |
|---|---|---|
| `--inscode` | `17914401175772326` (اهرم) | InsCode برای endpoint هایی که به آن نیاز دارند |
| `--date` | `20260701` | تاریخ به فرمت `YYYYMMDD` |
| `--timeout` | `20.0` | timeout هر درخواست به ثانیه |
| `--delay` | `0.1` | فاصله بین درخواست‌ها به ثانیه |
| `--max-retries` | `3` | تعداد تلاش مجدد برای خطاهای موقت (429/502/503/504) |
| `--csv` | `tsetmc_active_endpoints_test.csv` | مسیر فایل خروجی CSV |
| `--json` | `tsetmc_active_endpoints_test.json` | مسیر فایل خروجی JSON |

</div>
