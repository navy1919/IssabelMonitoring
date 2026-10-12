# منشور محصول (Product Charter)

| فیلد | مقدار |
|---|---|
| Document ID | `DOC-VIS-001` |
| Version | `0.2` |
| Status | `Draft – Phase 0 – awaiting approval` |
| Date | `2026-10-11` (۱۴۰۵/۰۷/۱۹ – `1405/07/19`) |
| Owner | `Architecture` |
| Related documents | [domain-model.md](domain-model.md)، [functional-requirements.md](../requirements/functional-requirements.md)، [non-functional-requirements.md](../requirements/non-functional-requirements.md)، [ADR-0001](../architecture/architecture-decision-records/ADR-0001-record-architecture-decisions.md)، [ADR-0002](../architecture/architecture-decision-records/ADR-0002-modular-monolith-with-edge-collectors.md)، [ADR-0009](../architecture/architecture-decision-records/ADR-0009-no-own-raw-log-store-federate-existing.md)، [ADR-0020](../architecture/architecture-decision-records/ADR-0020-existing-platform-integration-read-only-pull.md)، [ADR-0021](../architecture/architecture-decision-records/ADR-0021-build-buy-reuse-strategy.md)، [ADR-0022](../architecture/architecture-decision-records/ADR-0022-commercial-platforms-not-adopted.md)، [product-benchmark.md](../benchmark/product-benchmark.md)، [build-buy-reuse-analysis.md](../benchmark/build-buy-reuse-analysis.md)، [open-questions-and-assumptions.md](../architecture/open-questions-and-assumptions.md) |
| Change log | `0.1` (2026-10-11) پیش‌نویس اولیه؛ `0.2` (2026-10-12) **تغییر پس از Product Benchmark**: هویت reuse-first، دامنهٔ فازها (فاز 0.5 Evidence PoC؛ بدون ClickHouse/`edge-collector` در فاز ۲)، غیرهدف‌های جدید (ذخیره‌گاه تله‌متری خام، منبع هشدار اضافه، APM تجاری)؛ معیارهای موفقیت حفظ شد |

> **قرارداد برچسب‌گذاری:** **[واقعیت]** فقط برای F-01..F-03 و نتایج مستقیم آن‌ها؛ **[فرض A-xx]** فرض موقت؛ **[فرضیه]** گزاره‌ای که با آزمایش (EXP-xx) یا بنچمارک (BM-xx) باید سنجیده شود؛ **[توصیه]** پیشنهاد معماری؛ **[باز Q-xx]** وابسته به پرسش باز.

---

## ۱. نام و هویت محصول

- نام کاری: **Banking Service Observability & Intelligence Platform (BSOIP)**. نام نهایی محصول و نام مخزن هنوز باز است **[باز Q-12]**. نام فعلی مخزن (`IssabelMonitoring`) تداعی‌کنندهٔ محصول Issabel PBX است و به دامنهٔ این محصول ارتباطی ندارد.
- **[واقعیت F-01]** مخزن `navy1919/IssabelMonitoring` در شروع فاز ۰ خالی بوده است (بدون commit و فایل؛ بازبینی ۲۰۲۶-۱۰-۱۱).
- هویت محصول: هوش عملیاتی **سرویس‌محور** و **تراکنش‌محور** برای خدمات بانکی. ابزارهای موجود (SolarWinds، Zabbix، Splunk، Elasticsearch) **منابع شواهد** هستند، نه خودِ محصول.
- **راهبرد (پس از Product Benchmark، [ADR-0021](../architecture/architecture-decision-records/ADR-0021-build-buy-reuse-strategy.md)) [توصیه]:** «Reuse-first thin banking intelligence layer» — ابزارهای موجود سیستم مرجع تله‌متری خام، جست‌وجوی لاگ، هشدار و سلامت زیرساخت می‌مانند؛ BSOIP **لایهٔ نازک هوش بانکی** است که فقط آنچه هیچ ابزار موجود یا نامزدی ندارد را می‌سازد: معنای نتیجهٔ تراکنش بانکی، تقویم کاری جلالی و پنجره‌های سرویس، کاتالوگ و توپولوژی بانکی با provenance/confidence، و رتبه‌بندی شواهد با اطمینان عددی و شواهد نقض ([competitive-gap-analysis.md](../benchmark/competitive-gap-analysis.md)). این راهبرد کم‌پشیمانی و **مشروط به Evidence PoC** (فاز 0.5) است.

## ۲. چشم‌انداز (Vision)

> «هر اپراتور، پشتیبان و مالک سرویس در بانک بتواند در چند دقیقه بفهمد کدام سرویس بانکی و کدام نوع تراکنش برای مشتری دچار مشکل شده است، اثر آن بر نتیجهٔ کسب‌وکار چیست، و کدام حلقه از زنجیرهٔ وابستگی‌ها با چه شواهد و چه میزان اطمینانی محتمل‌ترین محل مشکل است — بدون اینکه ابتدا لازم باشد ابزار جدیدی روی برنامه‌ها نصب شود.»

این چشم‌انداز با محدودیت‌های اعلام‌شده توسط مالک محصول **[واقعیت F-02]** سازگار است: استقرار کاملاً درون‌سازمانی، شروع از ابزارهای موجود و تعویق instrumentation مستقیم.

## ۳. بیان مسئله (Problem Statement)

بانک امروز ابزارهای پایش متعددی دارد که هر کدام بخشی از تصویر را نشان می‌دهند **[فرض A-05، A-06]**:

1. **پایش زیرساخت‌محور است نه سرویس‌محور.** Zabbix و SolarWinds وضعیت میزبان، شبکه و برخی middleware را نشان می‌دهند؛ اما پاسخ نمی‌دهند که «کارت به کارت از طریق همراه‌بانک الان سالم است یا نه».
2. **موفقیت فنی با موفقیت کسب‌وکار یکی نیست.** پاسخ HTTP 200 یا پیام ISO 8583 با کد پاسخ «موجودی ناکافی» از دید فنی موفق ولی از دید کسب‌وکار ناموفق است؛ و افزایش ناگهانی خطاهای فنی در میان انبوه ردهای کسب‌وکاری ممکن است گم شود.
3. **سکوت همیشه به معنای خرابی نیست.** نبود تراکنش ساتنا پس از پایان چرخه‌های پردازشی یا در روز تعطیل، «غیرفعال مورد انتظار» است؛ در حالی که همان سکوت در ساعت اوج روز کاری یک حادثه است. ابزارهای فعلی تقویم کاری بانکی را نمی‌شناسند.
4. **همبستگی بین لایه‌ها دستی است.** در یک حادثه، اپراتور باید بین کنسول Zabbix، جست‌وجوی Splunk/Elasticsearch، وضعیت صف‌های IBM MQ و آمار Load Balancer جابه‌جا شود و رابطهٔ علّی را حدس بزند.
5. **توپولوژی وابستگی‌ها مستند و قابل‌اعتماد نیست** **[فرضیه]**: نقشهٔ وابستگی سرویس‌ها (کانال → API Gateway → ESB → MQ → سوئیچ/شتاب) اغلب در ذهن افراد یا اسناد پراکنده است.
6. **محدودیت‌های محیطی سخت‌اند** **[واقعیت F-02]**: بدون اینترنت، درون مرکز داده، و بدون امکان تغییر گستردهٔ برنامه‌ها در کوتاه‌مدت.

## ۴. کاربران هدف و پرسوناها

| پرسونا | نقش | نیاز اصلی | استفادهٔ کلیدی از محصول |
|---|---|---|---|
| **P1 – اپراتور NOC/Operations** | پایش ۲۴×۷، اولین پاسخ‌دهنده | تشخیص سریع «کدام سرویس، چقدر جدی، از کجا» | داشبورد سلامت سرویس، رویدادهای گروه‌بندی‌شده، پنل شواهد، پیوند به ابزار منبع |
| **P2 – SRE / پشتیبانی برنامه** | عیب‌یابی عمیق | شواهد چندلایه با زمان‌بندی دقیق و اطمینان | جزئیات سرویس، خط زمانی حادثه، رتبه‌بندی علت محتمل، پرس‌وجوی federated |
| **P3 – مالک سرویس (Service Owner)** | پاسخ‌گوی کیفیت سرویس کسب‌وکار | نرخ موفقیت کسب‌وکار، SLO، بودجهٔ خطا | گزارش SLO تقویم‌آگاه، روند حجم در برابر baseline |
| **P4 – تیم middleware/یکپارچه‌سازی** | ESB، IBM MQ، RabbitMQ، API Gateway | تشخیص backlog گذرا از پایدار، وضعیت کانال‌ها | طبقه‌بندی backlog، زمان تا پرشدن صف، وضعیت کانال MQ |
| **P5 – امنیت / SOC** | مصرف‌کنندهٔ audit | ردگیری دسترسی‌ها و تغییرات | لاگ audit تغییرناپذیر و ارسال به SIEM بانک |
| **P6 – مدیریت فناوری اطلاعات** | تصمیم‌گیری | گزارش سطح سرویس و روندها | گزارش‌های دوره‌ای سطح سرویس |
| **P7 – مدیر پلتفرم (Platform Admin)** | راهبری خود محصول | پیکربندی connectorها، کاتالوگ، تقویم، دسترسی‌ها | صفحات مدیریت، سلامت connectorها، self-monitoring |

## ۵. تمایزدهنده‌ها (Differentiators)

1. **کاتالوگ سرویس بانکی + توپولوژی وابستگی با منشأ (provenance) و اطمینان (confidence)**: هر یال وابستگی می‌گوید از کجا آمده (`MANUAL` | `DISCOVERED` | `IMPORTED`) و چقدر قابل‌اعتماد است.
2. **آگاهی از نتیجهٔ کسب‌وکار**: تفکیک `SUCCESS`، `BUSINESS_DECLINE`، `TECHNICAL_FAILURE`، `TIMEOUT` و غیره (enum `TxnOutcome`)؛ موفقیت فنی ≠ موفقیت کسب‌وکار.
3. **آگاهی از تقویم کاری بانکی**: تمایز «غیرفعال مورد انتظار» از «قطعی»؛ پنجره‌های ساتنا/پایا/پل **قابل پیکربندی‌اند و هرگز در کد ثابت نمی‌شوند**.
4. **رتبه‌بندی علت محتمل، توضیح‌پذیر و توپولوژی‌آگاه**: با اطمینان و عدم‌قطعیت صریح؛ **هرگز ادعای علیت نمی‌کند** («همبستگی ≠ علیت»).
5. **کار با صفر instrumentation جدید در شروع و بدون دوباره‌سازی ابزارهای موجود**: از طریق federate کردن و استفادهٔ مجدد از ابزارهای موجود، از جمله services/SLA زبیکس در صورت کفایت ([ADR-0009](../architecture/architecture-decision-records/ADR-0009-no-own-raw-log-store-federate-existing.md)، [ADR-0020](../architecture/architecture-decision-records/ADR-0020-existing-platform-integration-read-only-pull.md)).
6. **آفلاین، درون‌سازمانی، با امنیت در سطح بانکی**.
7. **رابط کاربری فارسی RTL با تقویم جلالی** برای صفحات اپراتوری **[باز Q-23]**.

## ۶. دامنه (Scope)

### ۶.۱ دامنه‌های بانکی پوشش‌داده‌شده (از منشور مالک محصول، F-03)

نگاشت هر دامنه به مدل دامنه در [domain-model.md](domain-model.md) (بخش ۸) آمده است.

- صدور و پذیرش کارت (Card issuing/acquiring)
- سوئیچ پرداخت و ارتباط با شتاب (Payment switch / Shetab)
- کارت به کارت و انتقال حساب به حساب (A2A)
- همراه‌بانک و اینترنت‌بانک؛ کلاینت‌های Android و PWA
- Open Banking و مدیریت API
- ساتنا (SATNA)، پایا (PAYA)، پل (POL) با تقویم‌ها و چرخه‌های پردازشی
- ESB و مرزهای اعتماد (Trust Boundaries)
- IBM MQ و RabbitMQ
- Load Balancer، Reverse Proxy و WAF
- Docker / Kubernetes
- Application Serverها، پایگاه‌داده، cache و سرویس‌های احراز هویت

### ۶.۲ دامنه به تفکیک فاز

| فاز | داخل دامنه | خارج از دامنه در این فاز |
|---|---|---|
| **Phase 0** – Discovery & architecture | این بستهٔ مستندات، ADRها، Product Benchmark، پرسش‌های باز، طرح آزمایش‌ها | هرگونه پیاده‌سازی **[واقعیت F-02]** |
| **Phase 0.5** – Evidence PoC | آزمایش‌های کوچک، فقط‌خواندنی، محدود در زمان و دورریختنی PoC-1..PoC-5 روی برش VS-1 (services/SLA زبیکس، KPI لاگ‌محور در Splunk/ES، معیارهای MQ، رتبه‌بندی شواهد، ذخیره‌سازی PostgreSQL) — گیت G-PoC | ساخت محصول؛ هر نوشتن در سیستم‌های تولیدی |
| **Phase 1** – Platform foundation & service catalog | repo/build/CI آفلاین، IAM (AD/OIDC)، audit، کاتالوگ، توپولوژی دستی، تقویم کاری و پنجره‌های نگهداری، پوستهٔ UI فارسی (صفحات اپراتوری، [باز Q-23])، بستهٔ نصب آفلاین v1؛ مسیر موازی کشف داده (EXP-01..EXP-04) | دریافت telemetry، داشبورد سلامت، ClickHouse، `edge-collector` |
| **Phase 2** – Ingestion core & existing-platform connectors | Connector SDK، `connector-runtime`، خط لولهٔ ingestion دادهٔ مشتق روی PostgreSQL، connector Zabbix (شامل services/SLA به‌عنوان شاهد و همگام‌سازی تقویم) + پلتفرم لاگی که لاگ VS-1 را دارد (Splunk یا ES) + exporter IBM MQ (+SolarWinds Should)، معیارهای اولیهٔ RabbitMQ/LB از Zabbix، مدل سلامت v1، قواعد ایستا، داشبورد و نقشهٔ توپولوژی | تحلیل تراکنش، همبستگی پیشرفته، ClickHouse (مگر با trigger)، `edge-collector`/OTLP (فاز ۴، اختیاری) |
| **Phase 3** – Transaction observability & SLOs | انواع تراکنش، OutcomeMapping، خلاصه‌های دقیقه‌ای (L0) و L2 در صورت وجود کلید، موفقیت فنی/کسب‌وکار، طبقه‌بندی backlog، SLO تقویم‌آگاه، تشخیص غیرفعال مورد انتظار، پنل شواهد ساده؛ **دموی VS-1** | رتبه‌بندی کامل علت محتمل، ML |
| **Phase 4** – Correlation & extended integrations | مدل رویداد کامل، dedup/گروه‌بندی، رتبه‌بندی علت محتمل توپولوژی‌آگاه، آگاهی از تغییر، بازخورد اپراتور، پرس‌وجوی federated، webhook، اعلان سطح Incident از طریق کانال‌های موجود/ITSM، OTLP و `edge-collector` اختیاری، JVM/JMX، SQL Server، K8s، WAF، synthetic API checks | تشخیص ناهنجاری آماری پیشرفته |
| **Phase 5** – Anomaly detection & prediction | baselineهای فصلی، آشکارسازهای آماری، قواعد چندمتغیره، پیش‌بینی، چارچوب ارزیابی، drift، توضیح‌پذیری | ML بدون عبور از دروازهٔ ارزیابی |
| **Phase 6** – Enterprise hardening & production readiness | HA/DR، اعتبارسنجی کارایی، ارزیابی امنیتی/pentest، تمرین ارتقا/بازگشت، runbook، تحویل عملیات؛ ClickHouse/Kafka فقط اگر triggerها فعال شده باشند | – |
| **Phase 7** (اختیاری) | instrumentation مستقیم (OTel SDK/agent) برای برنامه‌های منتخب، همبستگی L3، RUM برای وب/PWA/Android — **فقط با نیاز اثبات‌شده** | – |

> سخت‌سازی (امنیت، تست، self-observability) در همهٔ فازها پیوسته است؛ Phase 6 اعتبارسنجی در مقیاس است.

## ۷. غیرهدف‌ها (Non-goals) – در ابتدای کار

- جایگزینی Zabbix، SolarWinds، Splunk یا Elasticsearch.
- ذخیرهٔ لاگ خام توسط خود محصول ([ADR-0009](../architecture/architecture-decision-records/ADR-0009-no-own-raw-log-store-federate-existing.md)).
- **ذخیره‌گاه تله‌متری خام اختصاصی** (کپی wholesale متریک‌های Zabbix/SolarWinds): فقط KPIهای مشتق و سری‌های مرتبط با KPI نگه داشته می‌شوند؛ ClickHouse فقط با trigger ([ADR-0008](../architecture/architecture-decision-records/ADR-0008-telemetry-store-postgresql-first-clickhouse-on-trigger.md)).
- **منبع هشدار دیگری در کنار Zabbix/SolarWinds بودن**: اعلان فقط در سطح Incident سرویس و از کانال‌های موجود بانک ([FR-NTF-001](../requirements/functional-requirements.md)).
- **بازسازی درخت سلامت/SLA زیرساختی** در صورتی که services/SLA زبیکس کافی باشد (PoC-1، [ADR-0023](../architecture/architecture-decision-records/ADR-0023-reuse-zabbix-services-and-sla.md)).
- **داشبوردهای مهندسی عمومی** در UI سفارشی؛ UI سفارشی فقط صفحات اپراتوری است و نماهای مهندسی Grafana OSS اختیاری یا deep link به ابزارهای موجودند ([ADR-0024](../architecture/architecture-decision-records/ADR-0024-visualization-split-custom-operator-ui-optional-grafana.md)).
- **خرید یا استقرار APM/AIOps تجاری** (Dynatrace، Splunk ITSI، Instana، AppDynamics و مشابه): به دلیل تحریم و شکاف‌های قابلیتی پذیرفته نشد؛ فقط با مجوز حقوقی [باز Q-24] و PoC برتر بازبینی می‌شود ([ADR-0022](../architecture/architecture-decision-records/ADR-0022-commercial-platforms-not-adopted.md)).
- Kafka تا فعال شدن triggerهای [ADR-0010](../architecture/architecture-decision-records/ADR-0010-buffering-without-kafka-initially.md).
- مدیریت پیکربندی تجهیزات شبکه.
- اصلاح خودکار (automated remediation).
- SIEM بودن.
- توسعهٔ APM agent در فازهای اولیه.
- RUM پیش از Phase 7.
- SaaS چندمستأجری (multi-bank tenancy فقط Could در Phase 6+، [FR-ADM-005](../requirements/functional-requirements.md)).

## ۸. معیارهای موفقیت (قابل‌اندازه‌گیری)

| شناسه | معیار | هدف | روش سنجش | فاز/Milestone |
|---|---|---|---|---|
| SC-01 | تأیید بستهٔ معماری | تأیید رسمی Phase 0 و بسته‌شدن پرسش‌های گیت G0 (Q-07، Q-13، Q-22، Q-26، Q-27، Q-28) و سپس G-PoC (Q-01، Q-02، Q-04، Q-09، Q-23، Q-24، Q-25) با برقراری معیارهای پذیرش Evidence PoC | جلسهٔ بازبینی تصمیم؛ گزارش PoC | M0، M0.5 |
| SC-02 | کاتالوگ در UAT | سرویس VS-1 و زنجیرهٔ وابستگی آن کامل در کاتالوگ با owner و criticality | بازبینی با مالک سرویس | M1 |
| SC-03 | دریافت از منابع موجود | Zabbix + حداقل یکی از Elasticsearch/Splunk با سلامت connector قابل‌مشاهده، بدون تغییر پیکربندی در منابع ([NFR-IMP-002](../requirements/non-functional-requirements.md)) | آزمون پذیرش M2 | M2 |
| SC-04 | **VS-1: رتبه‌بندی حلقهٔ معیوب** | در بازپخش ≥ ۳ حادثهٔ تاریخی مورد توافق با عملیات، حلقهٔ معیوب واقعی در ۳ شاهد برتر برای ≥ ۲ از ۳ حادثه | بازپخش حوادث | M3 |
| SC-05 | **VS-1: تطابق سلامت** | وضعیت سلامت با قضاوت اپراتور در ≥ ۹۰٪ پنجره‌های ۵ دقیقه‌ای نمونه‌گیری‌شده تطابق دارد | نمونه‌گیری و برچسب‌زنی اپراتور | M3 |
| SC-06 | **VS-1: حریم خصوصی** | صفر دادهٔ حساس در ممیزی redaction | ممیزی redaction روی ذخیره‌سازها و پاسخ‌های API | M3 |
| SC-07 | **VS-1: اثر بر منبع** | بار پرس‌وجو روی هر منبع در بودجهٔ توافق‌شده ([NFR-IMP-001](../requirements/non-functional-requirements.md)) | سنجش با مدیران منبع (BM-04/BM-05/BM-06) | M3 |
| SC-08 | کیفیت تشخیص ناهنجاری | precision/recall اندازه‌گیری‌شده روی تاریخچهٔ برچسب‌دار؛ آستانه با عملیات توافق می‌شود | evaluation harness ([FR-ANO-006](../requirements/functional-requirements.md)) | M5 |
| SC-09 | آمادگی تولید | سناریوی Medium روی سخت‌افزار مرجع ([NFR-SCL-001](../requirements/non-functional-requirements.md))، تمرین DR موفق (BM-09)، pentest بدون یافتهٔ بحرانی باز | گزارش Phase 6 | M6 |
| SC-10 | زمان تشخیص و بومی‌سازی حادثه (MTTD/MTTI) | **[توصیه]** baseline فعلی در Phase 1–3 از سوابق حوادث اندازه‌گیری شود و هدف کاهش پس از M3 با عملیات توافق شود؛ عدد هدف از پیش تعیین نمی‌شود | مقایسهٔ قبل/بعد روی حوادث واقعی | M4–M6 |
| SC-11 | پذیرش اپراتور | آموزش اپراتور ≤ ۱ روز ([NFR-USA-004](../requirements/non-functional-requirements.md))؛ نرخ بازخورد «مفید» روی فرضیه‌های علت (FR-COR-006) پایش شود | نظرسنجی و آمار feedback | M4+ |

## ۹. ذی‌نفعان

| ذی‌نفع | علاقه/نقش | مشارکت |
|---|---|---|
| مالک محصول (Navy19) | تعیین چشم‌انداز و محدودیت‌ها، تأیید فازها | تأییدکنندهٔ هر Milestone |
| واحد عملیات/NOC | کاربر اصلی؛ تأمین سوابق حوادث برای VS-1 | تعریف معیار پذیرش VS-1 |
| تیم‌های پشتیبانی برنامه و مالکان سرویس | تعریف TransactionType، OutcomeMapping، SLO | داده و دانش دامنه |
| تیم middleware/یکپارچه‌سازی | ESB، IBM MQ، RabbitMQ | دسترسی فقط‌خواندنی و تعریف توپولوژی |
| مدیران Zabbix/SolarWinds/Splunk/Elasticsearch | منابع شواهد و سیستم‌های مرجع | تخصیص حساب فقط‌خواندنی، بودجهٔ پرس‌وجو، نصب saved searchها و نمونهٔ غیرتولیدی Zabbix برای PoC (Q-08، Q-22، Q-26، Q-28) |
| امنیت اطلاعات / SOC | تأیید دسترسی‌ها، ممیزی، pentest | تأیید معماری امنیتی، دریافت audit |
| تیم زیرساخت و شبکه | VM، firewall، zoneها | تأمین محیط (Q-03، Q-18) |
| انطباق و حقوقی | الزامات بانک مرکزی، PCI DSS، نگهداشت؛ تحریم و لایسنس | پاسخ Q-10، Q-21، Q-24، Q-25 |
| مدیریت IT | بودجه و اولویت | تأمین منابع |
| تیم تحویل (۴ تا ۸ مهندس، **[فرض A-03]**) | ساخت و نگهداری | – |

## ۱۰. اصول راهنما

1. **سرویس و تراکنش در مرکز**؛ زیرساخت فقط به‌عنوان شاهد.
2. **ابتدا از آنچه هست استفاده کن** **[واقعیت F-02]**: federate کردن و استفادهٔ مجدد از ابزارهای موجود، فقط‌خواندنی و با بودجهٔ پرس‌وجو؛ ابزار متن‌باز استاندارد فقط برای شکاف اثبات‌شده؛ کد سفارشی فقط برای معنای بانکی؛ instrumentation مستقیم فقط با نیاز اثبات‌شده.
3. **صداقت دربارهٔ عدم‌قطعیت**: هر وضعیت محاسبه‌شده توضیح دارد؛ هر پیوند تراکنشی سطح همبستگی (L0..L3) را نشان می‌دهد؛ رتبه‌بندی علت‌ها «محتمل» است نه قطعی؛ دادهٔ کهنه یا ناکافی → `UNKNOWN`، نه `OK`.
4. **پیکربندی به‌جای کد**: انواع سرویس، اصطلاحات بانک، نگاشت کدهای پاسخ، تقویم‌ها و پنجره‌های سرویس **داده‌اند**.
5. **حریم خصوصی در طراحی**: PAN، CVV، PIN و … هرگز ذخیره نمی‌شوند؛ redaction دولایه ([ADR-0018](../architecture/architecture-decision-records/ADR-0018-data-retention-privacy-capacity-controls.md)).
6. **سادگی و تحویل تدریجی** **[واقعیت F-02]**: modular monolith؛ Kafka فقط با فعال‌شدن triggerهای [ADR-0010](../architecture/architecture-decision-records/ADR-0010-buffering-without-kafka-initially.md)؛ ML فقط پس از دروازهٔ ارزیابی ([ADR-0013](../architecture/architecture-decision-records/ADR-0013-analytics-staging-rules-statistics-ml.md)).
7. **حوزهٔ خرابی مستقل**: پلتفرم نباید به سیستم‌هایی که پایش می‌کند وابسته باشد ([NFR-AVL-002](../requirements/non-functional-requirements.md)).
8. **کمترین اثر بر منبع**: هیچ تغییر پیکربندی اجباری روی تجهیزات موجود ([NFR-IMP-002](../requirements/non-functional-requirements.md)).
9. **هر پیاده‌سازی پس از تأیید صریح** **[واقعیت F-02]**.

## ۱۱. محدودیت‌ها (Constraints)

### ۱۱.۱ محدودیت‌های تأییدشده — **[واقعیت F-02]**

| کد | محدودیت |
|---|---|
| C-01 | فقط درون‌سازمانی (on-premises)، در مرکز دادهٔ بانک |
| C-02 | بدون دسترسی مستقیم به Internet |
| C-03 | شروع با استفاده از SolarWinds، Zabbix، Splunk و Elasticsearch موجود |
| C-04 | instrumentation مستقیم برنامه‌ها به فازهای بعدی و بر اساس نیاز واقعی موکول می‌شود |
| C-05 | معماری enterprise با تحویل تدریجی و بدون پیچیدگی غیرضروری |
| C-06 | محصول متمرکز بر خدمات و تراکنش‌های بانکی است، نه پایش زیرساخت |
| C-07 | مستندات فارسی؛ شناسه‌ها، نام فایل‌ها و اصطلاحات استاندارد انگلیسی |
| C-08 | هیچ پیاده‌سازی پیش از تأیید صریح |

### ۱۱.۲ فرض‌های محدودکننده (موقت)

- **[فرض A-02]** VMهای لینوکسی RHEL-compatible در دسترس‌اند؛ Kubernetes برای خود پلتفرم فرض نمی‌شود **[باز Q-02]**.
- **[فرض A-12]** بدون ابر عمومی؛ mirror داخلی artifact قابل‌تأمین یا ساخت است **[باز Q-09]**.
- **[فرض A-09]** حساب‌های API فقط‌خواندنی پس از بازبینی امنیتی قابل‌دریافت‌اند (ریسک R-02).
- **[فرض A-13]** تأخیر تشخیص چنددقیقه‌ای (≤ ۲–۵ دقیقه) برای منابع pull در فازهای اولیه پذیرفتنی است.
- **[باز Q-10]** الزامات مقرراتی (بانک مرکزی، PCI DSS، نگهداشت audit) هنوز مشخص نیست.

## ۱۲. ریسک‌های سطح منشور

مهم‌ترین ریسک‌ها برای منشور: R-16 (تداوم لایسنس/تحریم ابزارهای تجاری موجود که راهبرد reuse به آن‌ها تکیه دارد)، R-17 (وابستگی به Zabbix)، R-01 (نبود کد نتیجه/latency/کلید در لاگ‌ها)، R-02 (تأخیر در مجوزها)، R-03 (بار اضافه روی Splunk/ES)، R-05 (هشدار کاذب)، R-07 (گسترش دامنه به همهٔ حوزه‌های بانکی). پاسخ منشور به R-07: **VS-1 روی یک سرویس (کارت به کارت از طریق همراه‌بانک، [باز Q-13]) و گسترش دامنه‌ها فقط پس از M3**.

## ۱۳. نکات برای بازبینی

- «نکته برای بازبینی»: تمایزدهندهٔ ۷ (UI فارسی RTL جلالی) به پاسخ Q-23 وابسته شد؛ معیارهای موفقیت SC-02..SC-11 بدون تغییر حفظ شدند و SC-01 فقط برای گیت G-PoC گسترش یافت.

- «نکته برای بازبینی»: معیار SC-10 (MTTD/MTTI) عمداً بدون عدد هدف آمده است، چون baseline فعلی بانک ناشناخته است؛ پیشنهاد می‌شود جمع‌آوری baseline از سوابق ITSM در EP-22 گنجانده شود (وابسته به Q-14).
