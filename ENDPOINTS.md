<div dir="rtl">

# مرجع کامل Endpoint ها

Base URL: `https://cdn.tsetmc.com/api/...`

جدول علامت‌گذاری:
- بدون علامت = در تست ما داده‌ی واقعی برگردانده
- ⚠️ = مسیر معتبر است اما در تست با پارامتر نمونه خالی برگشت (احتمالاً نیاز به پارامتر واقعی یا session دارد)

## فهرست مطالب

- [قیمت پایانی و تاریخچه](#قیمت-پایانی-و-تاریخچه--closingprice)
- [صف خرید/فروش](#صف-خریدفروش--bestlimits)
- [معاملات](#معاملات--trade)
- [حقیقی/حقوقی](#حقیقیحقوقی--clienttype)
- [اطلاعات و جستجوی نماد](#اطلاعات-و-جستجوی-نماد--instrument)
- [داده‌های کلی بازار](#داده‌های-کلی-بازار--marketdata)
- [صندوق‌ها/ETF](#صندوق‌هاetf--fund)
- [سهامداران](#سهامداران--shareholder)
- [اطلاعیه‌های کدال](#اطلاعیه‌های-کدال--codal)
- [پیام ناظر](#پیام-ناظر--msg)
- [بورس انرژی](#بورس-انرژی--energy)
- [داده ثابت/زمان](#داده-ثابتزمان--staticdata)
- [آموزش](#آموزش--learning)
- [تشخیص نوع ابزار مالی و صنعت](#تشخیص-نوع-ابزار-مالی-و-صنعت)
- [تشخیص وضعیت توقف/تعلیق نماد](#تشخیص-وضعیت-توقفتعلیق-نماد)
- [نکات و هشدارهای عملیاتی](#نکات-و-هشدارهای-عملیاتی)
- [منابع مکمل](#منابع-مکمل)

## قیمت پایانی و تاریخچه — `ClosingPrice`

| نام | متد | آدرس | کارکرد | رکورد/کلید |
|---|---|---|---|---|
| ChartData daily | GET | `/ClosingPrice/GetChartData/{InsCode}/D` | OHLCVT chart data, daily resolution | `closingPriceChartData: 1016` |
| ClosingPrice daily | GET | `/ClosingPrice/GetClosingPriceDaily/{InsCode}/{DEven}` | Single day closing price summary | `closingPriceDaily: object` |
| ClosingPrice daily list top | GET | `/ClosingPrice/GetClosingPriceDailyList/{InsCode}/{Top}` | Historical closing prices; Top=0 returns all | `closingPriceDaily: 10` |
| ClosingPrice daily list all | GET | `/ClosingPrice/GetClosingPriceDailyList/{InsCode}/0` | All historical closing prices | `closingPriceDaily: 1084` |
| ClosingPrice daily list CSV | GET | `/ClosingPrice/GetClosingPriceDailyListCSV/{InsCode}/0` | All historical closing prices as CSV-like output | `` |
| ClosingPrice history | GET | `/ClosingPrice/GetClosingPriceHistory/{InsCode}/{DEven}` | Old ClosingPriceData intraday/history snapshots | `closingPriceHistory: 5021` |
| ClosingPrice info today | GET | `/ClosingPrice/GetClosingPriceInfo/{InsCode}` | Today price summary and instrument state | `closingPriceInfo: object` |
| Instruments history in day | GET | `/ClosingPrice/GetInstrmentsHistoryInDay/{DEven}` | Price info of all instruments on a specific date | `closingPriceDailyHistoryWithInstDetails: 1923` |
| Instrument calendar | GET | `/ClosingPrice/GetInstrumentCalendar/{InsCode}` | Instrument trading calendar with closing prices and volumes | `instrumentCalendar: 1084` |
| Market watch ⚠️ | GET | `/ClosingPrice/GetMarketWatch` | Market watch data; may return empty from CDN without params/session | `marketwatch: 0` |
| Market watch Excel (legacy) | GET | `https://old.tsetmc.com/tsev2/excel/MarketWatchPlus.aspx?d=0` | Legacy full market watch export; جایگزین واقعی ردیف بالا (جزئیات کامل در بخش «منابع مکمل») | `rows: 3136 (Excel)` |
| Price adjust by flow ⚠️ | GET | `/ClosingPrice/GetPriceAdjustByFlow/{Flow}/{Top}` | Price adjustment events by market flow | `priceAdjust: 0` |
| Price adjust list ⚠️ | GET | `/ClosingPrice/GetPriceAdjustList/{InsCode}` | Price adjustment events for instrument | `priceAdjust: 0` |
| Related company | GET | `/ClosingPrice/GetRelatedCompany/{CSecVal}` | Instruments and 30-day history in industry sector | `relatedCompany: 65` |

## صف خرید/فروش — `BestLimits`

| نام | متد | آدرس | کارکرد | رکورد/کلید |
|---|---|---|---|---|
| BestLimits now | GET | `/BestLimits/{InsCode}` | Current 5-level order book / best limits | `bestLimits: 5` |
| BestLimits historical | GET | `/BestLimits/{InsCode}/{DEven}` | Historical order book snapshots for one trading day | `bestLimitsHistory: 24660` |

> ⚠️ **BestLimits یک delta feed است، نه یک اسنپ‌شات کامل.** برای بازسازی دقیق عمق order book به‌ازای هر لحظه، باید یک stateful carry-forward reconstruction پیاده‌سازی کنید. بعد از این بازسازی، پوشش داده عملاً کامل است.

## معاملات — `Trade`

| نام | متد | آدرس | کارکرد | رکورد/کلید |
|---|---|---|---|---|
| Trade now | GET | `/Trade/GetTrade/{InsCode}` | Trades for last trading day | `trade: 14818` |
| Trade history | GET | `/Trade/GetTradeHistory/{InsCode}/{DEven}/false` | Historical intraday trades | `tradeHistory: 14818` |
| Trade intraday | GET | `/Trade/GetTradeIntraDay/{InsCode}` | Intraday aggregated trades for last trading day | `tradeIntraDay: 105` |
| Trade volume | GET | `/Trade/GetTradeVolume/{InsCode}` | Trade volume distribution by price/time | `tradeVolume: 798` |

> ⚠️ **شکاف پوشش شناخته‌شده:** `GetTradeHistory` نسبت به آمار رسمی بورس حدود ۳۶-۳۸٪ کسری دارد. برای ارقام تجمعی (حجم/ارزش/تعداد کل معاملات)، `ClosingPriceHistory` منبع معتبر است.

## حقیقی/حقوقی — `ClientType`

| نام | متد | آدرس | کارکرد | رکورد/کلید |
|---|---|---|---|---|
| ClientType now | GET | `/ClientType/GetClientType/{InsCode}/1/0` | Current real/legal buyer-seller data | `clientType: object` |
| ClientType all | GET | `/ClientType/GetClientTypeAll` | Client type data for all instruments from last trading day | `clientTypeAllDto: 1878` |
| ClientType history | GET | `/ClientType/GetClientTypeHistory/{InsCode}/{DEven}` | Historical real/legal buyer-seller data for instrument/day | `clientType: object` |

## اطلاعات و جستجوی نماد — `Instrument`

| نام | متد | آدرس | کارکرد | رکورد/کلید |
|---|---|---|---|---|
| Instrument history | GET | `/Instrument/GetInstrumentHistory/{InsCode}/{DEven}` | Historical simple instrument metadata for a day | `instrumentHistory: object` |
| Instrument identity | GET | `/Instrument/GetInstrumentIdentity/{InsCode}` | Instrument identity, sector, sub-sector, names | `instrumentIdentity: object` |
| Instrument info | GET | `/Instrument/GetInstrumentInfo/{InsCode}` | Instrument full information, EPS, sector, static thresholds | `instrumentInfo: object` |
| Instrument search | GET | `/Instrument/GetInstrumentSearch/{Query}` | Instrument search by keyword | `instrumentSearch: 41` |
| Share change ⚠️ | GET | `/Instrument/GetInstrumentShareChange/{InsCode}` | Share changes for instrument | `instrumentShareChange: 0` |
| Share change by flow ⚠️ | GET | `/Instrument/GetInstrumentShareChangeByFlow/{Flow}/{Top}` | Share changes by market flow | `instrumentShareChange: 0` |

## داده‌های کلی بازار — `MarketData`

| نام | متد | آدرس | کارکرد | رکورد/کلید |
|---|---|---|---|---|
| Instrument state | GET | `/MarketData/GetInstrumentState/{InsCode}/{DEven}` | Instrument state for day | `instrumentState: 1` |
| Instrument state top | GET | `/MarketData/GetInstrumentStateTop/{Top}` | Recently changed instrument states | `instrumentState: 10` |
| Instrument statistic | GET | `/MarketData/GetInstrumentStatistic/{InsCode}` | Instrument statistics such as average value/volume/trades | `instrumentStatistic: 88` |
| Inst value all params | GET | `/MarketData/GetInstValueAllInstAllParam` | All instrument value parameters for all instruments; very large | `instValueAllInstAllParam: 299174` |
| Market overview | GET | `/MarketData/GetMarketOverview/{Flow}` | Market overview by flow with index values and activity | `marketOverview: object` |
| Sectors summary | GET | `/MarketData/GetSectorsSummary` | Sectors summary | `sectorSummeries: 49` |
| Static threshold | GET | `/MarketData/GetStaticThreshold/{InsCode}/{DEven}` | Static price thresholds for day | `staticThreshold: 2` |

## صندوق‌ها/ETF — `Fund`

| نام | متد | آدرس | کارکرد | رکورد/کلید |
|---|---|---|---|---|
| ETF by inscode | GET | `/Fund/GetETFByInsCode/{InsCode}` | ETF/fund data including redemption/subscription NAV-like values | `etf: object` |
| Fund detail 0 | GET | `/Fund/GetFundInDetail/0` | Fund details and NAV stats | `fund: object` |
| Fund detail 1 | GET | `/Fund/GetFundInDetail/1` | Fund details variant | `fund: object` |

## سهامداران — `Shareholder`

| نام | متد | آدرس | کارکرد | رکورد/کلید |
|---|---|---|---|---|
| Shareholder changes old | GET | `/Shareholder/{InsCode}/{DEven}` | Shareholder changes at day | `shareShareholder: 4` |
| Shareholder last ⚠️ | GET | `/Shareholder/GetInstrumentShareHolderLast/{InsCode}` | Latest shareholders for instrument | `shareHolder: 0` |
| Shareholder changes all ⚠️ | GET | `/Shareholder/GetShareHolderChanges/false` | All shareholder changes in recent days | `shareHoldersChanges: 0` |
| Shareholder company list ⚠️ | GET | `/Shareholder/GetShareHolderCompanyList/{ShareHolderShareID}` | Other holdings of shareholder | `shareHolderShare: 0` |
| Shareholder history ⚠️ | GET | `/Shareholder/GetShareHolderHistory/{InsCode}/{DEven}/{Top}` | Shareholder changes for instrument/day | `shareHolder: 0` |

## اطلاعیه‌های کدال — `Codal`

| نام | متد | آدرس | کارکرد | رکورد/کلید |
|---|---|---|---|---|
| Prepared data top | GET | `/Codal/GetPreparedData/{Top}` | Recent codal notifications | `preparedData: 10` |
| Prepared data by inscode | GET | `/Codal/GetPreparedDataByInsCode/{Top}/{InsCode}` | Codal notifications for instrument | `preparedData: 10` |
| Codal publisher by symbol | GET | `/Codal/GetCodalPublisherBySymbol/{Symbol}` | Codal publisher lookup by symbol | `codalPublisher: object` |
| File attachment by row id | GET | `/Codal/GetFileAttachmentByMainTableRowId/{MainTableRowId}` | Codal attachment by main table row id | `fileAttachment: 1` |
| File attachment by tracing ⚠️ | GET | `/Codal/GetFileAttachmentByTracingNo/{TracingNo}` | Codal attachment by tracing no | `fileAttachment: 0` |

## پیام ناظر — `Msg`

| نام | متد | آدرس | کارکرد | رکورد/کلید |
|---|---|---|---|---|
| Messages by flow | GET | `/Msg/GetMsgByFlow/{Flow}/{Top}` | Supervisor messages by flow | `msg: 10` |
| Messages by inscode | GET | `/Msg/GetMsgByInsCode/{InsCode}` | Supervisor messages for instrument | `msg: 354` |

## بورس انرژی — `Energy`

| نام | متد | آدرس | کارکرد | رکورد/کلید |
|---|---|---|---|---|
| Energy auction by id | GET | `/Energy/GetAuctionById/{AuctionId}` | Energy exchange auction by id | `auction: object` |
| Energy auction trade by id ⚠️ | GET | `/Energy/GetAuctionTradeById/{AuctionId}` | Energy auction trades by id | `auctionTrade: 0` |

## داده ثابت/زمان — `StaticData`

| نام | متد | آدرس | کارکرد | رکورد/کلید |
|---|---|---|---|---|
| Static data | GET | `/StaticData/GetStaticData` | Static descriptions/names such as paper type, sectors, YVal | `staticData: 75` |
| Time | GET | `/StaticData/GetTime` | Server date and time | `` |

## آموزش — `Learning`

| نام | متد | آدرس | کارکرد | رکورد/کلید |
|---|---|---|---|---|
| Learning topics | GET | `/Learning/GetLearningTopics` | Learning/help topics from TSETMC site | `learningTopics: 78` |

---

## تشخیص نوع ابزار مالی و صنعت

تست‌شده روی ۴ نوع واقعی: سهم عادی (فولاد)، ETF اهرمی (اهرم)، صندوق کالایی (عیار)، آپشن (ضهرم۴۰۲۳).

### صنعت/بخش — از `GetInstrumentIdentity`
فیلدهای `sector.lSecVal` و `subSector.lSoSecVal`:
```json
"sector": {"cSecVal": "68 ", "lSecVal": "صندوق سرمايه گذاري قابل معامله"},
"subSector": {"cSoSecVal": 6821, "lSoSecVal": "صندوق هاي سرمايه گذاري اهرمي"}
```
همین فیلدها عیناً در `GetInstrumentInfo` هم تکرار شده‌اند.

### نوع دقیق ابزار — از `GetInstrumentHistory`
| منبع | مقدار | نوع ابزار |
|---|---|---|
| پیشوند `cIsin` | `IRO1...` | سهم عادی — بورس اصلی یا بازار دوم (تأییدشده با فولاد و وسکهبو) |
| پیشوند `cIsin` | `IRO5...` | سهم عادی در بازار نوآفرین/رشد فرابورس (تأییدشده با سحرخیز) |
| پیشوند `cIsin` | `IRT1...` | واحد صندوق/ETF (تأییدشده با اهرم) |
| پیشوند `cIsin` | `IRO9...` | آپشن (تأییدشده با ضهرم۴۰۲۳) |
| پیشوند `cIsin` | `IRB3TR...` | اوراق/اسناد خزانه اسلامی-اخزا (تأییدشده با اخزا۲۰۱/۲۰۲) |
| `cgrValCotTitle` | نام فارسی بازار فرعی | شفاف‌ترین فیلد — می‌گوید دقیقاً کدام تابلو (بورس اصلی/بازار دوم/نوآفرین/ابزار نوین مالی/مشتقه) |
| `flow` + `flowTitle` | کد بازار کلی | درشت‌تر از `cgrValCotTitle`؛ فقط بورس/فرابورس/مشتقه/انرژی را جدا می‌کند |

**هشدار مهم:** پیشوند سوم/چهارم ISIN (`IRO1` در برابر `IRO5`) فقط نوع کلی ابزار (سهم عادی) را نشان می‌دهد، نه تابلوی دقیق بازار؛ برای آن حتماً `cgrValCotTitle` را هم چک کنید — مثلاً هم فولاد (بورس اصلی) و هم وسکهبو (بازار دوم) هر دو `IRO1` دارند اما `cgrValCot` متفاوت (`N1` در برابر `N2`).

**آتی هنوز تأیید نشده:** با جست‌وجوی کلیدواژه‌ای («سکه»، «زعف») در `GetInstrumentSearch`، هیچ قرارداد آتی واقعی پیدا نشد — هر دو نتیجه به‌طور تصادفی به نام شرکت‌های عادی (نه قرارداد مشتقه) خورد. به‌احتمال زیاد قراردادهای آتی کالایی (سکه/زعفران) روی بورس کالای ایران (IME) معامله می‌شوند و ممکن است اصلاً در پایگاه‌داده‌ی `cdn.tsetmc.com` نباشند.

### تفاوت رفتار endpoint بر اساس نوع ابزار
| Endpoint | سهم | ETF/صندوق | آپشن |
|---|---|---|---|
| `GetETFByInsCode` | ❌ HTTP 500 | ✅ کار می‌کند | ❌ HTTP 500 |
| `GetPriceAdjustList` | ✅ داده واقعی | خالی (ساختاری) | خالی |
| `GetInstrumentShareChange` | ✅ داده واقعی | خالی (ساختاری) | خالی |
| `GetShareholderChangesOld` | ✅ داده واقعی | ✅ داده واقعی | ❌ خالی |
| `GetPreparedDataByInsCode` (کدال) | ✅ | ✅ | ❌ خالی (آپشن ناشر کدال ندارد) |
| `GetMsgByInsCode` (پیام ناظر) | ✅ | ✅ | ❌ خالی |

---

## تشخیص وضعیت توقف/تعلیق نماد

فیلد کلیدی: `cEtaval` و `cEtavalTitle` داخل شیء `instrumentState`، که هم در `GetInstrumentState/{InsCode}/{DEven}` و هم داخل `GetClosingPriceInfo` دیده می‌شود.

نمونه‌ی واقعی — نماد فعال (اهرم):
```json
"cEtaval": "A ", "cEtavalTitle": "مجاز", "underSupervision": 0
```

نمونه‌ی واقعی — نماد متوقف (فولاد، تعلیق‌شده به دلیل بررسی افشای اطلاعات):
```json
"cEtaval": "IS", "cEtavalTitle": "ممنوع-متوقف", "underSupervision": 3
```

**نتیجه‌ی عملی:** قبل از تفسیر خالی‌بودن یا خطای ۵۰۰ در endpointهای تاریخی (`Trade history`, `BestLimits historical`, `ClientType history`) برای یک نماد و تاریخ خاص، ابتدا `cEtavalTitle` را چک کن — اگر «متوقف» یا «ممنوع» بود، خالی‌بودن داده طبیعی و مورد انتظار است، نه یک نقص در endpoint.

---

## نکات و هشدارهای عملیاتی

1. **API غیررسمی و مستندنشده است.** هیچ SLA یا تعهد پایداری از TSETMC وجود ندارد؛ همیشه با یک اسکریپت پایش دوره‌ای تست شود.
2. **رفتار endpoint به سه عامل مستقل بستگی دارد**، نه فقط به این‌که «کار می‌کند یا نه»:
   - نوع ابزار مالی (سهم/ETF/صندوق/آپشن) — بخش بالا
   - وضعیت توقف/تعلیق نماد در تاریخ درخواستی — بخش بالا
   - باز یا بسته بودن روز معاملاتی (تاریخ «امروز» می‌تواند داده‌ی ناقص یا خطای ۵۰۰ برای فیلدهای «تاریخچه‌ای» بدهد، حتی برای نماد کاملاً سالم)
3. **گاهی یک endpoint برای یک تاریخ خاص به‌طور موقت و بدون دلیل ساختاری خالی برمی‌گردد.** قبل از نتیجه‌گیری قطعی از یک نتیجه‌ی خالی غیرمنتظره، درخواست را دوباره امتحان کن.
4. **حجم برخی endpoint خیلی بالاست** (`GetInstValueAllInstAllParam` ~۲۴ مگابایت). برای استفاده‌ی مکرر حتماً cache شود.
5. **پارامترهای ساختگی (fake id) نتیجه‌ی خالی می‌دهند، نه خطا** — برای تست واقعی `TracingNo`, `AuctionId`, `ShareHolderShareID`، باید مقدار واقعی از یک endpoint دیگر (مثلاً `GetPreparedData` برای TracingNo) استخراج شود.

---

## منابع مکمل

### دیده‌بان بازار کامل — جایگزین واقعی `GetMarketWatch` خراب
مسیر API رسمی `GetMarketWatch` همیشه در تست ما خالی برگشت (`marketwatch: 0`). اما یک مسیر legacy جایگزین که واقعاً کار می‌کند و کل بازار را در یک درخواست می‌دهد پیدا و تأیید شد:

```
https://old.tsetmc.com/tsev2/excel/MarketWatchPlus.aspx?d=0          (اعداد به‌صورت int واقعی — پیشنهادی)
https://old.tsetmc.com/tsev2/excel/MarketWatchPlus.aspx?d=0&format=0 (اعداد به‌صورت متن)
```

- خروجی: فایل Excel با ۳۱۳۳ نماد (۳۱۳۶ ردیف کل شیت شامل ۳ ردیف عنوان/هدر) — کل بازار: سهم، صندوق، آپشن، اوراق در یک شیت.
- ستون‌ها: نماد، نام، تعداد، حجم، ارزش، دیروز، اولین، آخرین معامله (مقدار/تغییر/درصد)، قیمت پایانی (مقدار/تغییر/درصد)، کمترین، بیشترین، EPS، P/E، خرید (تعداد/حجم/قیمت)، فروش (قیمت/حجم/تعداد).
- **محدودیت مهم:** هیچ `InsCode`، `cIsin`، یا صنعت/زیرصنعتی ندارد — فقط بر اساس «نماد» (نام کوتاه) قابل جست‌وجوست. برای اتصال به سایر endpointها (که همه InsCode می‌خواهند)، باید نماد را از طریق `GetInstrumentSearch/{نماد}` به InsCode تبدیل کرد.
- کاربرد مناسب: دید کلی/لحظه‌ای سریع از کل بازار (قیمت، حجم، صف خرید/فروش سطح ۱) بدون هزاران فراخوانی API.

</div>
