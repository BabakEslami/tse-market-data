<div dir="rtl">

# Awesome TSETMC API 📊

مرجع غیررسمی و آزمایش‌شده‌ی endpoint های API بازار سرمایه ایران (TSETMC) — برای توسعه‌دهنده‌هایی که می‌خوان بدون ساعت‌ها probe کردن، مستقیم سراغ ساخت ابزار برن.

> ⚠️ **این پروژه هیچ ارتباط رسمی با شرکت مدیریت فناوری بورس تهران (TSETMC) ندارد.** این یک مستندسازی جامعه‌محور از یک API غیررسمی و مستندنشده است که صرفاً از طریق مشاهده و تست عملی به دست آمده. هیچ SLA یا تعهد پایداری‌ای پشت این endpoint ها نیست و ممکن است در هر لحظه بدون اطلاع قبلی تغییر کنند یا از کار بیفتند.

## این ریپو چیه؟

لیستی دسته‌بندی‌شده از ~۵۶ endpoint فعال روی `cdn.tsetmc.com` که به‌صورت عملی تست شده‌اند — همراه با توضیح کارکرد هر کدام، حجم/ساختار رکوردهای برگشتی، و نکات رفتاری مهمی که فقط از طریق تست واقعی (نه مستندات رسمی، چون مستنداتی وجود ندارد) کشف می‌شن.

هدف: کسی که می‌خواد روی داده‌های بازار سرمایه ایران ابزار بسازه (پایپ‌لاین قیمت، بک‌تست، دیده‌بان، ربات تلگرام و...) بتونه از این نقطه شروع کنه، نه از صفر.

## فهرست مطالب

- [شروع سریع](#شروع-سریع)
- [Endpoint ها بر اساس دسته](#endpoint-ها-بر-اساس-دسته)
- [تشخیص نوع ابزار مالی و صنعت](#تشخیص-نوع-ابزار-مالی-و-صنعت)
- [تشخیص وضعیت توقف/تعلیق نماد](#تشخیص-وضعیت-توقفتعلیق-نماد)
- [نکات و هشدارهای عملیاتی](#نکات-و-هشدارهای-عملیاتی)
- [منابع مکمل](#منابع-مکمل)
- [مشارکت](#مشارکت)
- [مجوز](#مجوز)

## شروع سریع

همه‌ی endpoint ها زیر این base URL هستن:

```
https://cdn.tsetmc.com/api/...
```

نمونه‌ی یک درخواست ساده (تاریخچه‌ی قیمت پایانی یک نماد):

```bash
curl "https://cdn.tsetmc.com/api/ClosingPrice/GetClosingPriceDailyList/{InsCode}/0"
```

`InsCode` شناسه‌ی داخلی هر نماد در سیستم TSETMC است (نه نماد فارسی خودش). برای پیدا کردن InsCode یک نماد:

```bash
curl "https://cdn.tsetmc.com/api/Instrument/GetInstrumentSearch/{Query}"
```

جدول علامت‌گذاری:
- بدون علامت = در تست ما داده‌ی واقعی برگردانده
- ⚠️ = مسیر معتبر است اما در تست با پارامتر نمونه خالی برگشت (احتمالاً نیاز به پارامتر واقعی یا session دارد)

## Endpoint ها بر اساس دسته

### قیمت پایانی و تاریخچه — `ClosingPrice`

| نام | متد | آدرس | کارکرد |
|---|---|---|---|
| ChartData daily | GET | `/ClosingPrice/GetChartData/{InsCode}/D` | OHLCVT روزانه |
| ClosingPrice daily | GET | `/ClosingPrice/GetClosingPriceDaily/{InsCode}/{DEven}` | خلاصه قیمت یک روز |
| ClosingPrice daily list | GET | `/ClosingPrice/GetClosingPriceDailyList/{InsCode}/{Top}` | تاریخچه قیمت؛ Top=0 یعنی همه |
| ClosingPrice daily list CSV | GET | `/ClosingPrice/GetClosingPriceDailyListCSV/{InsCode}/0` | همان بالا، خروجی شبه‌CSV |
| ClosingPrice history | GET | `/ClosingPrice/GetClosingPriceHistory/{InsCode}/{DEven}` | اسنپ‌شات‌های تاریخی/درون‌روزی |
| ClosingPrice info | GET | `/ClosingPrice/GetClosingPriceInfo/{InsCode}` | خلاصه قیمت امروز + وضعیت نماد |
| Instruments history in day | GET | `/ClosingPrice/GetInstrmentsHistoryInDay/{DEven}` | قیمت همه نمادها در یک تاریخ |
| Instrument calendar | GET | `/ClosingPrice/GetInstrumentCalendar/{InsCode}` | تقویم معاملاتی نماد با قیمت و حجم |
| Market watch ⚠️ | GET | `/ClosingPrice/GetMarketWatch` | دیده‌بان بازار (در عمل خالی — [جایگزین](#منابع-مکمل) را ببینید) |
| Price adjust by flow ⚠️ | GET | `/ClosingPrice/GetPriceAdjustByFlow/{Flow}/{Top}` | تعدیل قیمت بر اساس نوع بازار |
| Price adjust list ⚠️ | GET | `/ClosingPrice/GetPriceAdjustList/{InsCode}` | تعدیل قیمت برای یک نماد |
| Related company | GET | `/ClosingPrice/GetRelatedCompany/{CSecVal}` | نمادهای هم‌صنعت + تاریخچه ۳۰ روزه |

### صف خرید/فروش — `BestLimits`

| نام | متد | آدرس | کارکرد |
|---|---|---|---|
| BestLimits now | GET | `/BestLimits/{InsCode}` | صف ۵ سطحی فعلی |
| BestLimits historical | GET | `/BestLimits/{InsCode}/{DEven}` | صف‌های تاریخی یک روز معاملاتی |

> ⚠️ این یک **delta feed** است، نه اسنپ‌شات کامل. برای بازسازی دقیق order book باید carry-forward stateful پیاده‌سازی کنید.

### معاملات — `Trade`

| نام | متد | آدرس | کارکرد |
|---|---|---|---|
| Trade now | GET | `/Trade/GetTrade/{InsCode}` | معاملات آخرین روز |
| Trade history | GET | `/Trade/GetTradeHistory/{InsCode}/{DEven}/false` | معاملات درون‌روزی تاریخی |
| Trade intraday | GET | `/Trade/GetTradeIntraDay/{InsCode}` | معاملات تجمیع‌شده آخرین روز |
| Trade volume | GET | `/Trade/GetTradeVolume/{InsCode}` | توزیع حجم بر اساس قیمت/زمان |

> ⚠️ **شکاف پوشش شناخته‌شده:** `GetTradeHistory` نسبت به آمار رسمی بورس حدود ۳۶-۳۸٪ کسری دارد. برای ارقام تجمعی (حجم/ارزش/تعداد کل)، از `ClosingPriceHistory` به‌عنوان منبع معتبر استفاده کنید.

### حقیقی/حقوقی — `ClientType`

| نام | متد | آدرس | کارکرد |
|---|---|---|---|
| ClientType now | GET | `/ClientType/GetClientType/{InsCode}/1/0` | داده فعلی حقیقی/حقوقی |
| ClientType all | GET | `/ClientType/GetClientTypeAll` | داده حقیقی/حقوقی همه نمادها (آخرین روز) |
| ClientType history | GET | `/ClientType/GetClientTypeHistory/{InsCode}/{DEven}` | تاریخی برای نماد/روز خاص |

### اطلاعات و جستجوی نماد — `Instrument`

| نام | متد | آدرس | کارکرد |
|---|---|---|---|
| Instrument history | GET | `/Instrument/GetInstrumentHistory/{InsCode}/{DEven}` | متادیتای ساده تاریخی |
| Instrument identity | GET | `/Instrument/GetInstrumentIdentity/{InsCode}` | هویت، صنعت، زیرصنعت، نام‌ها |
| Instrument info | GET | `/Instrument/GetInstrumentInfo/{InsCode}` | اطلاعات کامل، EPS، صنعت، آستانه‌های ایستا |
| Instrument search | GET | `/Instrument/GetInstrumentSearch/{Query}` | جستجوی نماد بر اساس کلیدواژه |
| Share change ⚠️ | GET | `/Instrument/GetInstrumentShareChange/{InsCode}` | تغییرات تعداد سهام |
| Share change by flow ⚠️ | GET | `/Instrument/GetInstrumentShareChangeByFlow/{Flow}/{Top}` | تغییرات سهام بر اساس نوع بازار |

### داده‌های کلی بازار — `MarketData`

| نام | متد | آدرس | کارکرد |
|---|---|---|---|
| Instrument state | GET | `/MarketData/GetInstrumentState/{InsCode}/{DEven}` | وضعیت نماد در یک روز |
| Instrument state top | GET | `/MarketData/GetInstrumentStateTop/{Top}` | آخرین تغییرات وضعیت |
| Instrument statistic | GET | `/MarketData/GetInstrumentStatistic/{InsCode}` | میانگین ارزش/حجم/تعداد معاملات |
| Inst value all params | GET | `/MarketData/GetInstValueAllInstAllParam` | همه پارامترها برای همه نمادها (حجیم، ~۲۴MB) |
| Market overview | GET | `/MarketData/GetMarketOverview/{Flow}` | نمای کلی بازار + شاخص‌ها |
| Sectors summary | GET | `/MarketData/GetSectorsSummary` | خلاصه صنایع |
| Static threshold | GET | `/MarketData/GetStaticThreshold/{InsCode}/{DEven}` | آستانه‌های ایستای قیمت |

### صندوق‌ها/ETF — `Fund`

| نام | متد | آدرس | کارکرد |
|---|---|---|---|
| ETF by inscode | GET | `/Fund/GetETFByInsCode/{InsCode}` | داده صندوق شامل NAV صدور/ابطال |
| Fund detail 0/1 | GET | `/Fund/GetFundInDetail/{0|1}` | جزئیات و آمار NAV صندوق |

### سهامداران — `Shareholder`

| نام | متد | آدرس | کارکرد |
|---|---|---|---|
| Shareholder changes old | GET | `/Shareholder/{InsCode}/{DEven}` | تغییرات سهامداران در روز |
| Shareholder last ⚠️ | GET | `/Shareholder/GetInstrumentShareHolderLast/{InsCode}` | آخرین سهامداران |
| Shareholder changes all ⚠️ | GET | `/Shareholder/GetShareHolderChanges/false` | تغییرات اخیر همه نمادها |
| Shareholder company list ⚠️ | GET | `/Shareholder/GetShareHolderCompanyList/{ShareHolderShareID}` | سایر مالکیت‌های یک سهامدار |
| Shareholder history ⚠️ | GET | `/Shareholder/GetShareHolderHistory/{InsCode}/{DEven}/{Top}` | تغییرات برای نماد/روز خاص |

### اطلاعیه‌های کدال — `Codal`

| نام | متد | آدرس | کارکرد |
|---|---|---|---|
| Prepared data top | GET | `/Codal/GetPreparedData/{Top}` | آخرین اطلاعیه‌های کدال |
| Prepared data by inscode | GET | `/Codal/GetPreparedDataByInsCode/{Top}/{InsCode}` | اطلاعیه‌های یک نماد |
| Codal publisher by symbol | GET | `/Codal/GetCodalPublisherBySymbol/{Symbol}` | یافتن ناشر کدال با نماد |
| File attachment by row id | GET | `/Codal/GetFileAttachmentByMainTableRowId/{MainTableRowId}` | پیوست کدال |
| File attachment by tracing ⚠️ | GET | `/Codal/GetFileAttachmentByTracingNo/{TracingNo}` | پیوست بر اساس شماره پیگیری |

### پیام ناظر — `Msg`

| نام | متد | آدرس | کارکرد |
|---|---|---|---|
| Messages by flow | GET | `/Msg/GetMsgByFlow/{Flow}/{Top}` | پیام‌های ناظر بر اساس نوع بازار |
| Messages by inscode | GET | `/Msg/GetMsgByInsCode/{InsCode}` | پیام‌های ناظر یک نماد |

### بورس انرژی — `Energy`

| نام | متد | آدرس | کارکرد |
|---|---|---|---|
| Energy auction by id | GET | `/Energy/GetAuctionById/{AuctionId}` | مزایده بورس انرژی |
| Energy auction trade by id ⚠️ | GET | `/Energy/GetAuctionTradeById/{AuctionId}` | معاملات مزایده |

### داده ثابت/زمان — `StaticData`

| نام | متد | آدرس | کارکرد |
|---|---|---|---|
| Static data | GET | `/StaticData/GetStaticData` | توضیحات ثابت (نوع اوراق، صنایع، YVal) |
| Time | GET | `/StaticData/GetTime` | تاریخ و ساعت سرور |

### آموزش — `Learning`

| نام | متد | آدرس | کارکرد |
|---|---|---|---|
| Learning topics | GET | `/Learning/GetLearningTopics` | موضوعات آموزشی سایت TSETMC |

## تشخیص نوع ابزار مالی و صنعت

تست‌شده روی ۴ نوع واقعی: سهم عادی، ETF اهرمی، صندوق کالایی، آپشن.

**صنعت/بخش** از `GetInstrumentIdentity` (فیلدهای `sector.lSecVal` و `subSector.lSoSecVal`) قابل استخراج است؛ همین فیلدها در `GetInstrumentInfo` هم تکرار می‌شوند.

**نوع دقیق ابزار** از پیشوند `cIsin` در `GetInstrumentHistory`:

| پیشوند | نوع ابزار |
|---|---|
| `IRO1...` | سهم عادی — بورس اصلی یا بازار دوم |
| `IRO5...` | سهم عادی — بازار نوآفرین/رشد فرابورس |
| `IRT1...` | واحد صندوق/ETF |
| `IRO9...` | آپشن |
| `IRB3TR...` | اوراق/اسناد خزانه اسلامی (اخزا) |

> ⚠️ پیشوند ISIN فقط نوع کلی ابزار را نشان می‌دهد، نه تابلوی دقیق بازار. برای آن حتماً فیلد `cgrValCotTitle` را هم چک کنید — مثلاً دو نماد می‌توانند هر دو `IRO1` داشته باشند اما `cgrValCot` متفاوت (بورس اصلی در برابر بازار دوم).

**تفاوت رفتار endpoint بر اساس نوع ابزار:**

| Endpoint | سهم | ETF/صندوق | آپشن |
|---|---|---|---|
| `GetETFByInsCode` | ❌ HTTP 500 | ✅ | ❌ HTTP 500 |
| `GetPriceAdjustList` | ✅ | خالی (ساختاری) | خالی |
| `GetInstrumentShareChange` | ✅ | خالی (ساختاری) | خالی |
| `GetShareholderChangesOld` | ✅ | ✅ | ❌ خالی |
| `GetPreparedDataByInsCode` | ✅ | ✅ | ❌ خالی |
| `GetMsgByInsCode` | ✅ | ✅ | ❌ خالی |

## تشخیص وضعیت توقف/تعلیق نماد

فیلد کلیدی `cEtaval` / `cEtavalTitle` در شیء `instrumentState` (هم در `GetInstrumentState` و هم در `GetClosingPriceInfo`).

```json
"cEtaval": "A ", "cEtavalTitle": "مجاز", "underSupervision": 0
```
```json
"cEtaval": "IS", "cEtavalTitle": "ممنوع-متوقف", "underSupervision": 3
```

قبل از تفسیر خالی‌بودن یا خطای ۵۰۰ در endpoint های تاریخی برای یک نماد/تاریخ خاص، ابتدا `cEtavalTitle` را چک کنید — نماد متوقف/ممنوع، خالی‌بودن داده طبیعی است، نه نقص endpoint.

## نکات و هشدارهای عملیاتی

1. **API غیررسمی و مستندنشده است** — هیچ SLA وجود ندارد؛ رفتار endpoint ها می‌تواند بدون اطلاع تغییر کند.
2. **رفتار هر endpoint به سه عامل بستگی دارد**: نوع ابزار مالی، وضعیت توقف/تعلیق نماد، و باز/بسته بودن روز معاملاتی (تاریخ «امروز» می‌تواند برای فیلدهای تاریخی خطای ۵۰۰ بدهد حتی برای نماد سالم).
3. **گاهی یک endpoint موقتاً و بدون دلیل ساختاری خالی برمی‌گردد** — قبل از نتیجه‌گیری قطعی، درخواست را دوباره امتحان کنید.
4. **برخی endpoint حجم بسیار بالایی دارند** (`GetInstValueAllInstAllParam` ~۲۴ مگابایت) — برای استفاده مکرر حتماً cache کنید.
5. **پارامترهای ساختگی نتیجه‌ی خالی می‌دهند، نه خطا** — برای تست واقعی مقادیری مثل `TracingNo` یا `AuctionId` باید از endpoint دیگری استخراج شوند.

## منابع مکمل

### دیده‌بان بازار کامل — جایگزین `GetMarketWatch`

مسیر رسمی `GetMarketWatch` در عمل همیشه خالی برمی‌گردد. جایگزین legacy تأییدشده:

```
https://old.tsetmc.com/tsev2/excel/MarketWatchPlus.aspx?d=0
```

خروجی Excel با کل بازار (سهم، صندوق، آپشن، اوراق) در یک شیت — نماد، قیمت، حجم، صف خرید/فروش سطح ۱ و... اما **هیچ `InsCode` یا `cIsin` ندارد**؛ فقط بر اساس نماد قابل جستجوست. برای اتصال به سایر endpoint ها باید نماد را با `GetInstrumentSearch/{نماد}` به InsCode تبدیل کنید.

## مشارکت

اگر endpoint جدیدی پیدا کردید یا رفتار متفاوتی از آنچه اینجا مستند شده مشاهده کردید، خوشحال می‌شویم PR بدهید. لطفاً:
- برای هر ادعای رفتاری، نمونه‌ی واقعی تست (نه فرضی) ضمیمه کنید.
- اگر endpoint جدیدی اضافه می‌کنید، آن را در دسته‌ی مناسب قرار دهید و در صورت امکان به `test_active_tsetmc_endpoints.py` هم اضافه کنید (اگر اسکریپت تست را هم منتشر کردید).

## مجوز

MIT — استفاده، تغییر و توزیع آزاد، بدون هیچ تضمینی از صحت یا پایداری داده‌ها.

</div>
