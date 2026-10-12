# ADR-0021: راهبرد ساخت/خرید/بازاستفاده (Build-Buy-Reuse Strategy)

| ID | Title | Status | Confidence | Date | Deciders |
|---|---|---|---|---|---|
| ADR-0021 | build-buy-reuse-strategy | Proposed | **Provisional** (PoC-1..PoC-5، G-PoC) | 2026-10-12 (۱۴۰۵/۰۷/۲۰) | Architecture (pending product owner approval) |

Version: `0.2` — نخستین نسخه این ADR در بازنگری پس از Product Benchmark ایجاد شد.

Related documents: [product-benchmark.md](../../benchmark/product-benchmark.md)، [competitive-gap-analysis.md](../../benchmark/competitive-gap-analysis.md)، [build-buy-reuse-analysis.md](../../benchmark/build-buy-reuse-analysis.md)، [container-architecture.md](../container-architecture.md)، [ADR-0008](ADR-0008-telemetry-store-postgresql-first-clickhouse-on-trigger.md)، [ADR-0020](ADR-0020-existing-platform-integration-read-only-pull.md)، [ADR-0022](ADR-0022-commercial-platforms-not-adopted.md)، [ADR-0023](ADR-0023-reuse-zabbix-services-and-sla.md)، [ADR-0024](ADR-0024-visualization-split-custom-operator-ui-optional-grafana.md)

## Context

- [واقعیت F-02] شروع با SolarWinds، Zabbix، Splunk و Elasticsearch موجود؛ بدون پیچیدگی غیرضروری؛ محصول سرویس‌محور و تراکنش‌محور، نه پایش زیرساخت.
- Product Benchmark ([product-benchmark.md](../../benchmark/product-benchmark.md)) سه یافته تعیین‌کننده دارد:
  1. **هیچ نامزدی** (تجاری یا OSS) معنای نتیجه تراکنش بانکی (موفقیت فنی ≠ موفقیت کسب‌وکار)، تقویم کسب‌وکار جلالی و پنجره‌های SATNA/PAYA/POL، یا «علت محتمل با شواهد + اطمینان عددی + شواهد نقض» را به‌صورت بومی مدل نمی‌کند (B2.2). Splunk ITSI نزدیک‌ترین است ولی تقویم کسب‌وکار ندارد و از نظر حقوقی مسدود است.
  2. محصولات تجاری آمریکایی برای بانک ایرانی «مسدود مگر با مجوز حقوقی» هستند ([ADR-0022](ADR-0022-commercial-platforms-not-adopted.md)).
  3. Zabbix (که بانک دارد) درخت سرویس کسب‌وکار، قواعد propagation و وزن، نمای root-cause و اشیای SLA با schedule ساعات کاری و downtime مستثنا را از قبل دارد (B2.3، اطمینان Medium در بخشی که صفحات مستند خوانده نشد).
- گزینه‌های راهبردی بررسی‌شده در [build-buy-reuse-analysis.md](../../benchmark/build-buy-reuse-analysis.md): A ساخت کامل سفارشی؛ B پشته OSS + لایه بانکی سفارشی؛ C پلتفرم تجاری + یکپارچه‌سازی بانکی؛ **D ترکیبی = شکل پیش‌نویس اولیه Phase 0 (نسخه 0.1)**: ابزارهای موجود منبع اصلی داده، ولی با `telemetry-store` ClickHouse خودی، `edge-collector` اجباری، pipeline کامل ingestion، موتور سلامت/SLA و اعلان خودی و آمادگی Kafka؛ E پالایش D.
- امتیاز وزنی (از ۱۰۰): **E = 78، D = 75**، B = 70، A = 66، C = 56 و مردود (Blocked unless legally cleared). اختلاف E و D کم است و به اطمینان پایین K14 (میزبانی saved search، Q-28) حساس است: اگر PoC-2 منفی باشد E و D هم‌تراز می‌شوند ([build-buy-reuse-analysis.md §۴–۵](../../benchmark/build-buy-reuse-analysis.md)).

## Decision

[توصیه] **گزینه E: «لایه نازک هوشمندی بانکی با اولویت بازاستفاده» (Reuse-first thin banking intelligence layer):**

1. Zabbix، SolarWinds، Splunk و Elasticsearch **سامانه مرجع** داده خام تله‌متری، هشدار زیرساختی و سلامت زیرساخت باقی می‌مانند. پلتفرم آن‌ها را جایگزین یا کپی نمی‌کند.
2. سرویس‌ها/SLA در Zabbix هرجا کافی باشد بازاستفاده می‌شوند — تصمیم با PoC-1 ([ADR-0023](ADR-0023-reuse-zabbix-services-and-sla.md)).
3. لایه سفارشی کوچک فقط این‌ها را اضافه می‌کند: کاتالوگ سرویس بانکی و توپولوژی با provenance/confidence؛ تقویم کسب‌وکار جلالی؛ سلامت آگاه از کسب‌وکار و expected-inactive؛ تمایز موفقیت فنی و کسب‌وکار؛ همبستگی شواهد بین‌منبعی و رتبه‌بندی علت محتمل با اطمینان؛ آشکارسازی آماری سبک روی KPIهای مشتق (Phase 5)؛ UI اپراتور.
4. OSS استاندارد فقط جایی که شکاف اثبات شود: exporter `mq_prometheus`، OpenTelemetry Collector (اختیاری)، PostgreSQL، Grafana OSS اختیاری برای نمای مهندسی.
5. **تخصیص مسئولیت‌ها** مطابق جدول زیر (مرجع واحد؛ سایر اسناد به آن ارجاع می‌دهند — نسخه کامل با توضیح در [container-architecture.md](../container-architecture.md)):

| مسئولیت | مالک |
|---|---|
| متریک میزبان/شبکه/دستگاه، دسترس‌پذیری، هشدار زیرساخت | Reuse: Zabbix، SolarWinds |
| متریک LB (F5/HAProxy)، RabbitMQ، MSSQL | Reuse: قالب‌های Zabbix (نصب تأیید شود)؛ API مستقیم فقط‌خواندنی فقط در صورت شکاف |
| متریک IBM MQ | OSS استاندارد: exporter `mq_prometheus` (scrape توسط `connector-runtime` یا `edge-collector`)، یا itemهای موجود Zabbix |
| لاگ خام، جستجو، نگهداری لاگ | Reuse: Splunk، Elasticsearch (بدون کپی) |
| محاسبه KPI تراکنش | Reuse موتور جستجو: saved search/aggregation زمان‌بندی‌شده **درون** Splunk/ES؛ لایه سفارشی **تعریف آن را تولید می‌کند** (از TransactionType، OutcomeMapping)، **مدیر Splunk/ES آن را نصب و مالکیت می‌کند** (Q-28؛ حساب پلتفرم فقط‌خواندنی می‌ماند) و پلتفرم فقط تجمیع دقیقه‌ای را می‌کشد |
| درخت سرویس و SLA سطح زیرساخت | نامزد Reuse: سرویس/SLA در Zabbix (PoC تصمیم می‌گیرد) |
| کاتالوگ سرویس بانکی، توپولوژی با provenance/confidence | Custom (تمایز اصلی) |
| تقویم کسب‌وکار (جلالی، تعطیلات، پنجره‌های سرویس) | Custom؛ همگام‌سازی به maintenance/SLA schedule در Zabbix (بدون منطق سرکوب تکراری) |
| سلامت آگاه از کسب‌وکار، expected-inactive، موفقیت فنی در برابر کسب‌وکار | Custom |
| همبستگی شواهد بین‌منبعی و رتبه‌بندی علت محتمل با اطمینان | Custom (کوچک، قطعی) |
| آشکارسازی ناهنجاری روی KPIهای مشتق | Custom سبک آماری (Phase 5) روی PostgreSQL/داده مشتق |
| تحویل هشدار/اعلان | Reuse کانال‌های موجود (Zabbix actions/ITSM/email)؛ پلتفرم حداکثر یک اعلان در سطح Incident به ازای هر Incident سرویس |
| داشبوردهای مهندسی | Grafana OSS اختیاری (بررسی AGPL، Q-21) یا deep link به ابزارهای موجود |
| UI اپراتور (سلامت سرویس، Incident، شواهد، کاتالوگ، تقویم) | Custom نازک؛ فارسی RTL/جلالی اگر Q-23 الزامی بودن را تأیید کند |
| ذخیره فراداده + KPI مشتق | PostgreSQL (Store واحد) در Low؛ ClickHouse فقط با Trigger |
| Streaming | ندارد (Kafka فعلاً رد — [ADR-0010](ADR-0010-buffering-without-kafka-initially.md)) |
| APM/AIOps تجاری | ندارد (مسدود — [ADR-0022](ADR-0022-commercial-platforms-not-adopted.md)) |

6. **دروازه G-PoC:** پیش از ساخت Phase 1، Phase 0.5 (Evidence PoC) با آزمایش‌های فقط‌خواندنی و زمان‌بندی‌شده PoC-1..PoC-5 اجرا می‌شود؛ معیارهای پذیرش در [build-buy-reuse-analysis.md](../../benchmark/build-buy-reuse-analysis.md) و نقشه راه. این ADR پس از G-PoC به Firm یا به گزینه جایگزین تغییر می‌کند.

## Alternatives considered

| گزینه | مزایا | معایب | نتیجه |
|---|---|---|---|
| A ساخت کامل سفارشی | کنترل کامل | بازسازی آنچه Zabbix/Splunk/ES انجام می‌دهند؛ هزینه و زمان؛ ingest و ذخیره خام پرحجم | رد |
| B پشته OSS + لایه بانکی سفارشی | مستقل از مجوز ابزارهای تجاری موجود | ingest و Store خام دوباره، دو منبع حقیقت برای هشدار | **جایگزین (fallback)** اگر ابزارهای موجود غیرقابل استفاده شوند (تداوم مجوز Q-25، محدودیت API) |
| C پلتفرم تجاری + یکپارچه‌سازی بانکی | قابلیت آماده APM/AIOps | مسدود از نظر تحریم؛ شکاف تقویم کسب‌وکار؛ lock-in | رد اکنون ([ADR-0022](ADR-0022-commercial-platforms-not-adopted.md)) |
| D ترکیبی (پیش‌نویس 0.1؛ امتیاز 75) | استقلال بیشتر از در دسترس بودن ابزارهای موجود در Incident؛ KPI از داده خام خودی | ClickHouse، `edge-collector` اجباری، pipeline و موتور سلامت/SLA/اعلان خودی = پیچیدگی عملیاتی بیشتر و هشدار تکراری | **جایگزین نزدیک**؛ مقصد حرکت اگر PoC-2 منفی باشد |
| **E Reuse-first thin layer (انتخاب؛ امتیاز 78)** | کمترین کد و Store؛ تمرکز روی تمایز بانکی؛ سازگار با F-02؛ low-regret: هر جزء D بعداً با Trigger قابل افزودن است | وابستگی به در دسترس بودن، مجوز و API ابزارهای موجود (R-15، R-16، R-17)؛ بار جستجو روی Splunk/ES؛ وابستگی به همکاری مدیران منبع (Q-28) | **پیشنهاد (PoC-gated)** |

## Rationale

E برتری قاطع بر D ندارد (78 در برابر 75)؛ دلیل انتخاب آن **کم‌پشیمانی (low-regret)** است: E با کمترین سرمایه‌گذاری آغاز می‌کند و هر جزء D (ClickHouse، `edge-collector`، ingest لاگ مشتق) با Trigger اندازه‌پذیر و بدون بازطراحی قابل افزودن است، در حالی که شروع با D هزینه عملیاتی را پیش از اثبات نیاز می‌پردازد. به همین دلیل تصمیم **مشروط به G-PoC** است.

تمایز محصول (معنای بانکی، تقویم، شواهد با اطمینان) در هیچ محصولی موجود نیست و باید ساخته شود؛ سایر قابلیت‌ها (جمع‌آوری، هشدار زیرساخت، جستجوی لاگ، درخت سرویس فنی و SLA) در ابزارهای فعلی بانک وجود دارد. ساخت دوباره آن‌ها با قید «بدون پیچیدگی غیرضروری» (F-02) و تیم ۴ تا ۸ نفره ([فرض A-03]) سازگار نیست. نتیجه فنی: حذف ClickHouse و `edge-collector` از Phase 1، Store واحد PostgreSQL ([ADR-0008](ADR-0008-telemetry-store-postgresql-first-clickhouse-on-trigger.md))، Kafka رد ([ADR-0010](ADR-0010-buffering-without-kafka-initially.md)).

## Consequences

- مثبت: Low = `platform-core` + PostgreSQL + `connector-runtime` (+ exporter MQ)؛ نرخ رکورد ورودی حدود ۱/۴۰ و حجم ذخیره حدود نصف سناریوی own-ingestion ([capacity-planning.md](../capacity-planning.md) §۳.۱ و §۴.۷)؛ بدون هشدار تکراری.
- منفی: هزینه اصلی به **بودجه کوئری سمت منبع** منتقل می‌شود (saved search هر دقیقه به ازای TransactionType در Splunk/ES — Q-22، Q-28)؛ همکاری مدیران Splunk/ES و Zabbix لازم است (Q-26، Q-28).
- ایجاد saved search/aggregation در منبع با حساب فقط‌خواندنی پلتفرم ([ADR-0020](ADR-0020-existing-platform-integration-read-only-pull.md)) در تعارض است؛ حل: پلتفرم تعریف نسخه‌دار را تولید و به‌صورت بسته تحویل می‌دهد و **مدیر منبع** آن را نصب می‌کند؛ پلتفرم فقط نتیجه را می‌خواند. اگر مدیران نپذیرند (Q-28)، fallback: کوئری aggregation درخواستی از connector در بودجه کوئری توافقی.
- ریسک: تداوم مجوز/به‌روزرسانی Splunk و SolarWinds (R-16، Q-25)؛ وابستگی به نقشه راه Zabbix (R-17 — Zabbix 8.0 اعلام شده ولی منتشر نشده)؛ تعهدات AGPL در صورت تغییر/توزیع Zabbix یا Grafana (R-18).
- عدم استقلال کامل failure domain: در Incidentی که خود Splunk/ES را درگیر کند، KPI تراکنش ناقص می‌شود (R-15) — باید به‌صورت `UNKNOWN` و شواهد ناقص نمایش داده شود (FR-HLT-003)، نه `OK`.

## Evidence that would change this decision

- G-PoC: PoC-2 نشان دهد saved search در Splunk/ES نمی‌تواند تجمیع دقیقه‌ای با بودجه کوئری توافقی بدهد → ingest لاگ مشتق از طریق OTel filelog/transform (نزدیک‌تر به B).
- Q-25: مجوز Splunk/SolarWinds قابل تمدید یا به‌روزرسانی قانونی نباشد → B برای آن منبع.
- Q-24: مجوز حقوقی برای محصول تجاری صادر شود **و** PoC آن از E بهتر باشد → بازنگری C ([ADR-0022](ADR-0022-commercial-platforms-not-adopted.md)).
- PoC-1 منفی → سلامت/SLA سفارشی ([ADR-0023](ADR-0023-reuse-zabbix-services-and-sla.md))، بدون تغییر بقیه E.

## Related requirements

FR-INT-001..FR-INT-004، FR-INT-006، FR-INT-008، FR-CAL-008، FR-TXN-003، FR-HLT-001، FR-COR-004، FR-NTF-001، NFR-IMP-001، NFR-IMP-002، NFR-AVL-002

## Related questions

Q-21، Q-22، Q-23، Q-24، Q-25، Q-26، Q-27، Q-28، PoC-1..PoC-5، R-15، R-16، R-17، R-18
