# پرسش‌های باز، فرض‌ها و آزمایش‌ها — BSOIP

| فیلد | مقدار |
|---|---|
| Document ID | `ARC-OQA-001` |
| Version | `0.2` |
| Status | `Draft – Phase 0 – awaiting approval` |
| Date | `2026-10-12` (۱۴۰۵/۰۷/۲۰) |
| Owner | `Architecture` |
| Related documents | [master-plan.md](../roadmap/master-plan.md)، [implementation-backlog.md](../roadmap/implementation-backlog.md)، [ADR index](architecture-decision-records/README.md)، [capacity-planning.md](capacity-planning.md)، [technology-evaluation.md](technology-evaluation.md)، [non-functional-requirements.md](../requirements/non-functional-requirements.md)، [quality-and-validation-strategy.md](../testing/quality-and-validation-strategy.md)، [product-benchmark.md](../benchmark/product-benchmark.md)، [competitive-gap-analysis.md](../benchmark/competitive-gap-analysis.md)، [build-buy-reuse-analysis.md](../benchmark/build-buy-reuse-analysis.md) |
| Change log | `0.1` (2026-10-11) پیش‌نویس اولیه؛ `0.2` (2026-10-12) **تغییر پس از Product Benchmark**: Q-23..Q-28، زیرپرسش‌های Q-03 و Q-16، فرض‌های ظرفیت CAP-01..CAP-17 به‌صورت ارجاع، فرضیه‌ها و الزامات PoC، پرسش‌های فروشنده، شواهد ناموجود، استثناهای NFR-IMP-002، ریسک‌های R-16..R-18، وضعیت ADR-0021..0024 و تغییر ADR-0008، بازچینی دسته‌های بازبینی |

## ۱. هدف

این سند مرجع واحد همه عدم‌قطعیت‌های فاز ۰ است: واقعیت‌های تأییدشده (F)، فرض‌ها (A و CAP)، فرضیه‌ها، پرسش‌های باز (Q)، آزمایش‌ها (EXP) و آزمایش‌های PoC (PoC-1..PoC-5)، پرسش‌های فروشنده، شواهد ناموجود و وضعیت تصمیم هر ADR. اسناد دیگر با برچسب‌های **[فرض A-xx]**، **[فرضیه]** و **[باز Q-xx]** به اینجا ارجاع می‌دهند.

پس از Product Benchmark ([product-benchmark.md](../benchmark/product-benchmark.md))، راهبرد پیشنهادی گزینهٔ **E — «Reuse-first thin banking intelligence layer»** است ([ADR-0021](architecture-decision-records/ADR-0021-build-buy-reuse-strategy.md)): Zabbix/SolarWinds/Splunk/Elasticsearch سیستم مرجع تله‌متری خام، هشدار و سلامت زیرساخت می‌مانند و لایهٔ سفارشی کوچک فقط معناشناسی بانکی را می‌افزاید. بسیاری از پرسش‌های جدید این نسخه (Q-23..Q-28) تعیین می‌کنند این راهبرد در عمل قابل اجراست یا خیر، و بخشی از آن‌ها در **فاز 0.5 Evidence PoC** پاسخ می‌گیرند.

## ۲. واقعیت‌های تأییدشده

| ID | واقعیت | منبع |
|---|---|---|
| F-01 | مخزن `navy1919/IssabelMonitoring` در آغاز فاز ۰ خالی بود (بدون commit و فایل). بررسی‌شده در 2026-10-11. | بررسی مستقیم |
| F-02 | محدودیت‌های اعلام‌شده مالک محصول (Navy19): فقط on-premises در مرکز داده بانک، بدون Internet مستقیم؛ شروع با SolarWinds، Zabbix، Splunk و Elasticsearch موجود؛ ابزارگذاری مستقیم برنامه‌ها به فازهای بعد و بر اساس نیاز واقعی؛ معماری enterprise با تحویل تدریجی و بدون پیچیدگی غیرضروری؛ تمرکز بر سرویس‌ها و تراکنش‌های بانکی نه پایش زیرساخت؛ مستندات فارسی با شناسه/نام فایل/اصطلاحات انگلیسی؛ عدم پیاده‌سازی تا تأیید صریح. | مالک محصول |
| F-03 | متن منشور محصول (prompt فاز ۰): فهرست کامل قابلیت‌ها، دامنه‌ها و تحویل‌دادنی‌ها. | مالک محصول |

**هر چیز دیگر** فرض، فرضیه، قابلیت مستند فروشنده که «نیازمند تأیید در نسخه نصب‌شده بانک» است، یا توصیه است. یافته‌های Product Benchmark از مستندات عمومی و متون مقررات گردآوری شده‌اند و **واقعیت دربارهٔ محیط بانک نیستند**؛ سطح اطمینان هر ادعا در [product-benchmark.md](../benchmark/product-benchmark.md) آمده است.

## ۳. پرسش‌های باز

### ۳.۱ دسته‌بندی برای جلسات بازبینی

پیشنهاد می‌شود پرسش‌ها در **یک جلسه بازبینی اصلی** با حضور مالک محصول، معماری، امنیت، حقوقی/انطباق، عملیات/NOC، مدیران منابع (Zabbix/SolarWinds/Splunk/ES/MQ) و زیرساخت مرور شوند؛ ترتیب دستورجلسه بر اساس انسداد. گیت‌ها مطابق [master-plan.md](../roadmap/master-plan.md) §۹.

| دسته | گیت | زمان | پرسش‌ها | چرا این ترتیب |
|---|---|---|---|---|
| **A0 — مسدودکننده شروع PoC** | G0 (آغاز فاز 0.5) | جلسه اول، ضروری | Q-13، Q-07 (پاسخ اولیه)، Q-26 (شامل تأمین نمونهٔ غیرتولیدی Zabbix)، Q-28، Q-27 (فقط: آیا آمار صف از قبل فعال است؟)، Q-22 (budget موقت PoC)، Q-17 (اختیاری) | PoC به سرویس VS-1، دسترسی read-only به Zabbix/Splunk/ES/MQ، نمونهٔ غیرتولیدی Zabbix و budget پرس‌وجو نیاز دارد |
| **A — مسدودکننده شروع ساخت** | G-PoC (آغاز فاز ۱، T-01-01) | جلسه اول؛ پاسخ نهایی تا پایان PoC | Q-01، Q-02، Q-04، Q-09، Q-23، Q-24، Q-25 | ADR-0003/0015/0016/0022/0024 و ادامهٔ مسیر reuse به آن‌ها وابسته‌اند |
| **B — پیش از فاز ۲** | G1 | پیگیری کتبی در فاز ۱ | Q-03 (به‌همراه فهرست DBMS مجاز)، Q-08، Q-22 (نهایی)، Q-21، Q-12، Q-11، Q-27 (نهایی)، Q-26 (مجوز نوشتن برای FR-CAL-008) | کانکتورها، استقرار، لایسنس، نام، منبع دادهٔ MQ |
| **C — پیش از فاز ۳** | G2 | پیگیری کتبی در فاز ۱–۲ | Q-06، Q-15، Q-16 (به‌همراه نگهداشت به تفکیک کلاس سری)، Q-17 | ظرفیت، تقویم، retention، زمان |
| **D — پیش از فاز ۴/۶** | G3/G4 | پیگیری در فاز ۲–۳ | Q-05، Q-10، Q-14، Q-18، Q-19، Q-20 | HA/DR، انطباق، اعلان، عملیات |

### ۳.۲ جدول پرسش‌ها

پرسش‌های بانک در [product-benchmark.md](../benchmark/product-benchmark.md) با شناسه‌های محلی BQ-01..BQ-08 به همین Qها نگاشت شده‌اند (BQ-01→Q-25، BQ-02→Q-26، BQ-03→Q-27، BQ-04→Q-28/Q-22، BQ-05→Q-23، BQ-06→Q-07، BQ-07→Q-08، BQ-08→Q-24/Q-21)؛ مرجع حقیقت Qها این جدول است.

| ID | پرسش | چرا مهم است | تصمیم‌های مسدود | پاسخ‌دهنده (نقش) | پیش‌فرض در صورت بی‌پاسخ | لازم تا فاز |
|---|---|---|---|---|---|---|
| Q-01 | اندازه و مهارت‌های تیم (Java/.NET/Go/Python/TS)؟ | زبان backend باید با تیم نگهدارنده سازگار باشد (R-04) | ADR-0003؛ T-01-01 | مدیر مهندسی / مدیر IT | Java 21 + Spring Boot 3.x (ADR-0003)، TypeScript برای UI | 1 (G-PoC) |
| Q-02 | آیا Kubernetes/OpenShift داخلی برای میزبانی این پلتفرم وجود دارد و مجاز است؟ | مدل بسته‌بندی و عملیات | ADR-0016 | تیم زیرساخت/پلتفرم | containerهای OCI روی VM با Podman/Compose؛ Helm بعداً | 1 (G-PoC) |
| Q-03 | استاندارد OS و فرایند تأمین VM؟ **زیرپرسش (0.2):** فهرست DBMSهای مجاز بانک چیست؟ آیا PostgreSQL (و در صورت trigger، ClickHouse) در آن هست یا افزودنش چه فرایندی دارد؟ | hardening، runtime container، زمان نصب؛ پس از benchmark، PostgreSQL تنها ذخیره‌گاه Low است ([ADR-0008](architecture-decision-records/ADR-0008-telemetry-store-postgresql-first-clickhouse-on-trigger.md)) | ADR-0016؛ ADR-0007؛ ADR-0008؛ NFR-DEP-002 | تیم زیرساخت + تیم پایگاه‌داده | RHEL-compatible (A-02) با Podman؛ PostgreSQL مجاز یا قابل افزودن | 2 (G1) |
| Q-04 | IdP بانک: فقط AD، ADFS، یا ارائه‌دهنده OIDC/SAML؟ MFA؟ | مدل احراز هویت و SSO | ADR-0015؛ EP-02 | امنیت اطلاعات / تیم هویت | Keycloak با AD/LDAP federation؛ MFA در صورت پشتیبانی | 1 (G-PoC) |
| Q-05 | RPO/RTO، توپولوژی سایت DR، هدف دسترس‌پذیری؟ | معماری HA/DR و هزینه سخت‌افزار | ADR-0017؛ EP-18 | مدیریت IT / BCP | 99.9% UI/API، RPO ≤ 15 min metadata، RTO ≤ 4 h، DR active-passive | 6 (G4) |
| Q-06 | اعداد بار: سرویس‌ها، موجودیت‌ها، سری‌های متریک، **تعداد سری‌های مرتبط با KPI**، حجم لاگ/رویداد/تراکنش، **تعداد TransactionType**، کاربران، retention، SLO query، سایت‌ها، نسبت peak | اندازه‌گذاری؛ تعیین اینکه trigger ClickHouse فعال می‌شود یا نه؛ بودجهٔ پرس‌وجوی سمت منبع | ADR-0008 (trigger)، ADR-0010، NFR-SCL-001 | عملیات + مالکان منابع | سناریوی Medium (A-11) در حالت reuse | 3 (G2)؛ نهایی تا 6 |
| Q-07 | برای سرویس VS-1: لاگ‌ها کجا هستند (Splunk یا ES)، آیا کد outcome/پاسخ، latency و کلیدهای همبستگی دارند؟ | تعیین سطح قابل دستیابی L0/L2 و معنای «موفقیت کسب‌وکاری» (R-01)؛ تعیین «پلتفرم لاگ VS-1» در فاز ۲ | دامنه VS-1؛ PoC-2؛ EP-11 | مالک سرویس + تیم برنامه + مدیر Splunk/ES | فقط L0 و شاخص‌های فنی؛ موفقیت کسب‌وکاری «ناموجود» | 0.5 (G0) پاسخ اولیه؛ دقیق با EXP-01/PoC-2 |
| Q-08 | نسخه‌ها و API در دسترس: Zabbix، SolarWinds، Splunk، ES/OpenSearch، IBM MQ، RabbitMQ، F5، HAProxy، NGINX، WAF؛ **و templateهای Zabbix نصب‌شده برای F5/HAProxy/RabbitMQ/MSSQL؛ و اینکه NGINX، WAF، API gateway، ESB و JVMها اکنون در کدام ابزار موجود پایش می‌شوند** (در benchmark تأییدنشده؛ با EP-22 بسته می‌شود؛ BQ-07) | انتخاب API، fixtureها، capabilityها (R-12)؛ تعیین اینکه کانکتور مستقیم LB/RabbitMQ لازم است یا داده از Zabbix کافی است | EP-08، EP-09 | مالکان منابع | کانکتورها بر اساس مستندات عمومی با برچسب «نیازمند تأیید» | 2 (G1) |
| Q-09 | وجود Kafka، vault/HSM برای secret، registry داخلی (Nexus/Harbor/Artifactory)؟ | ساخت آفلاین، SecretProvider، توزیع image | T-01-01، T-01-04، ADR-0016، ADR-0010 | زیرساخت + امنیت | mirror موقت تحت مدیریت تیم؛ فایل رمز‌شده برای secret؛ بدون Kafka | 1 (G-PoC) |
| Q-10 | الزامات مقرراتی: بانک مرکزی، کاربرد PCI DSS، محل داده، مدت نگهداری audit؟ | retention، کنترل‌ها، ممیزی (R-13) | ADR-0018 (اعداد)، NFR-CMP-001 | انطباق / امنیت | audit ≥ 5 سال؛ کنترل‌های PCI-like برای داده کارت | 6 (G4)؛ اثر بر retention از 2 |
| Q-11 | محصول برای یک بانک است یا چند بانک؟ | multi-tenancy | FR-ADM-005 | مالک محصول | تک‌بانک، تک‌مستأجر (A-01) | 2 (G1) |
| Q-12 | نام نهایی محصول و مخزن؟ نام `IssabelMonitoring` تداعی‌کننده Issabel PBX و نامرتبط است؛ آیا تغییر نام انجام شود؟ | شفافیت، جلوگیری از سردرگمی | نام artifactها و package‌ها | مالک محصول | نام کاری BSOIP؛ مخزن فعلی تا تصمیم | 2 (G1)، ترجیحاً پیش از T-01-01 |
| Q-13 | تأیید سرویس VS-1 (پیش‌فرض: کارت به کارت از Mobile Banking)؟ | تمرکز PoC، کشف داده و دمو | دامنه PoC و VS-1، EP-22، EP-23 | مالک محصول + عملیات | کارت به کارت از Mobile Banking | 0.5 (G0) |
| Q-14 | کانال‌های اعلان موجود (Zabbix actions، email، درگاه SMS) و ابزار ITSM؟ | پلتفرم از کانال‌های موجود استفاده می‌کند و کانال جدید نمی‌سازد (FR-NTF-001) | EP-17، مسیر on-call | عملیات / ITSM | فقط UI + email؛ یا Zabbix actions اگر Q-26 اجازه دهد | 4 (G3) |
| Q-15 | منبع تقویم تعطیلات و برنامه‌های ساتنا/پایا/پل بانک؟ | expected-inactive و SLO (R-14)؛ داده‌ای که در FR-CAL-008 به Zabbix همگام می‌شود | EP-04 داده، EP-12 | عملیات / واحد پرداخت | import دستی سالانه؛ پنجره‌ها توسط مالک سرویس | 3 (G2)؛ ساختار در 1 |
| Q-16 | retention به‌ازای کلاس داده؟ **زیرپرسش (0.2):** نگهداشت به تفکیک **کلاس سری** — آیا rollup یک‌دقیقه‌ای ۱۳ ماهه فقط برای سری‌های مرتبط با KPI لازم است و برای بقیه rollup ساعتی کافی است؟ آیا خلاصه‌های دقیقه‌ای تراکنش پس از ۹۰ روز به ساعتی تبدیل شوند؟ | rollup یک‌دقیقه‌ای ۱۳ ماهه بزرگ‌ترین مصرف دیسک است و تعیین می‌کند trigger ClickHouse در Medium فعال شود یا نه ([capacity-planning.md](capacity-planning.md)) | ADR-0018؛ ADR-0008 | انطباق + عملیات | spine §6 (raw 30 d، rollup 1m 13 ماه، 1h 3 سال، رویداد 13 ماه، tokenized 30 d، audit ≥ 5 سال) با محدودیت پیشنهادی: 1m ۱۳ ماهه فقط برای سری‌های KPI؛ در Low، raw 30 d و rollup 1m یکی نگه داشته شود | 3 (G2) |
| Q-17 | وضعیت NTP/همگامی زمان در منابع؟ | همبستگی (R-11) | تحمل پنجره همبستگی | زیرساخت | skew چندثانیه‌ای ممکن (A-08)؛ اندازه‌گیری با EXP-05 | 3 (G2) |
| Q-18 | بودجه سخت‌افزار پلتفرم؟ | اندازه‌گذاری محیط‌ها | ADR-0017، مدل استقرار | مدیریت IT | Low برای UAT/VS-1؛ Medium برای PROD | 6 (G4)؛ UAT در 1 |
| Q-19 | چه تیمی پلتفرم را اداره می‌کند؟ ۲۴×۷؟ | runbookها، on-call، handover | مدل عملیات | مدیریت IT / NOC | NOC سطح اول، تیم تحویل سطح دوم تا M6 | 6 (G4) |
| Q-20 | حساب‌های آزمون synthetic و فرایند تأیید؟ | ایمنی synthetic (FR-DX-003) | EP-15 | عملیات + امنیت + مالک سرویس | synthetic فقط endpointهای غیرمالی و health | 4 (G3) |
| Q-21 | سیاست لایسنس متن‌باز (AGPL و ...)؟ | Zabbix 7.0+ و Grafana OSS تحت AGPLv3 هستند؛ اثر بر Grafana اختیاری (ADR-0024) و هر تغییر/توزیع Zabbix (R-18) | ADR-0014، ADR-0024، ابزارهای CI | حقوقی / امنیت | پرهیز از تغییر یا توزیع اجزای AGPL؛ Grafana فقط اختیاری و بدون تغییر | 2 (G1) |
| Q-22 | سهمیه و ظرفیت Splunk/ES برای بار query اضافی؟ | بار و لایسنس (R-03)؛ در سناریوی reuse هزینهٔ اصلی پلتفرم | query budget کانکتورها، BM-04، PoC-2 | مدیران Splunk/ES | budget محافظه‌کارانه: query تجمیعی دقیقه‌ای، هم‌زمانی ۱، بدون backfill طولانی | 0.5 (budget موقت PoC)؛ 2 (G1) |
| Q-23 | آیا UI فارسی RTL با تقویم جلالی برای اپراتورها **الزامی** است، یا UI انگلیسی (مثلاً Grafana یا ابزارهای موجود) برای اپراتورها پذیرفتنی است؟ | Product Benchmark شاهدی از پشتیبانی RTL/جلالی در Grafana، Kibana یا OpenSearch Dashboards نیافت (اطمینان کم تا متوسط: نبود شاهد)؛ پاسخ، مرز UI سفارشی و استفادهٔ مجدد از Grafana را تعیین می‌کند | [ADR-0024](architecture-decision-records/ADR-0024-visualization-split-custom-operator-ui-optional-grafana.md)، ADR-0014، FR-UI-001، NFR-USA-001 | مالک محصول + مدیر NOC | الزامی برای صفحات اپراتوری (سلامت سرویس، Incident/شواهد، کاتالوگ، تقویم)؛ نماهای مهندسی انگلیسی (Grafana اختیاری یا deep link) | 1 (G-PoC) |
| Q-24 | نظر حقوقی/انطباق بانک دربارهٔ تهیه، تمدید، پشتیبانی یا به‌روزرسانی نرم‌افزار تجاری خارجی (آمریکایی و غیرآمریکایی) در شرایط تحریم چیست؟ سیاست بانک؟ **همچنین:** وضعیت حقوقی استفاده و به‌روزرسانی نرم‌افزار متن‌باز منتشرشده توسط شرکت‌های آمریکایی (مانند IBM `mq-metric-samples`/`mq_prometheus`، Grafana، ClickHouse، OpenTelemetry) چیست؟ (BQ-08) | ITSR (31 CFR 560.204)، EAR (15 CFR 746.7) و تحریم بخش مالی (E.O. 13902) محصولات آمریکایی را «مسدود مگر با مجوز حقوقی» می‌کنند؛ فروشندگان غیرآمریکایی ریسک تحریم ثانویه دارند؛ این مشاورهٔ حقوقی نیست | [ADR-0022](architecture-decision-records/ADR-0022-commercial-platforms-not-adopted.md)، ADR-0021، NFR-CMP-001؛ حتی ارسال پرسش به فروشندگان (§۸) | حقوقی / انطباق / تدارکات | هیچ محصول تجاری خارجی جدیدی تهیه نمی‌شود؛ گزینهٔ C فقط با مجوز حقوقی و PoC برتر بازگشایی می‌شود | 1 (G-PoC) |
| Q-25 | Splunk، SolarWinds، **IBM MQ** (و اشتراک Elasticsearch در صورت وجود) موجود چگونه لایسنس، تمدید و به‌روزرسانی می‌شوند (دائمی/دوره‌ای، مستقیم/واسطه، معتبر/منقضی)؟ ریسک تداوم چیست؟ (BQ-01) | مسیر reuse به تداوم این ابزارها وابسته است؛ گزارش‌های انجمن (اطمینان کم-متوسط) می‌گویند با انقضای لایسنس دوره‌ای Splunk، جست‌وجو مسدود می‌شود؛ SolarWinds نیز شرکت آمریکایی است | ADR-0021 (انتخاب منبع لاگ VS-1)، ADR-0009، FR-MQ-002 (کلاینت/exporter MQ)، R-16 | مدیریت IT + مدیران Splunk/SolarWinds/ES + تدارکات | تداوم به‌عنوان ریسک R-16 ثبت؛ اگر ES و Splunk هر دو لاگ VS-1 را دارند، ES ترجیح داده شود؛ پلتفرم با قطع یک منبع تخریب تدریجی دارد (NFR-REL-004) | 1 (G-PoC) |
| Q-26 | نسخهٔ Zabbix در حال استفاده چیست؟ آیا قابلیت‌های services (business service tree) و SLA از قبل استفاده می‌شوند؟ مالک پیکربندی Zabbix کیست و آیا اجازهٔ نوشتن maintenance/SLA schedule (FR-CAL-008) می‌دهد؟ | services/SLA در Zabbix 6.0+ مستند است (اطمینان متوسط)؛ تعیین می‌کند سلامت/SLA زیرساختی reuse شود ([ADR-0023](architecture-decision-records/ADR-0023-reuse-zabbix-services-and-sla.md)) و همگام‌سازی تقویم ممکن باشد؛ نقشهٔ راه Zabbix 8.0 اعلام شده ولی منتشر نشده (R-17) | ADR-0023، PoC-1، FR-INT-008، FR-CAL-008 | مالک/مدیر Zabbix | نسخه ≥ 6.0 فرض؛ فقط خواندن روی تولید؛ **PoC-1 روی یک نمونهٔ غیرتولیدی Zabbix** (هم‌نسخه با تولید، با کپی پیکربندی/دادهٔ لازم) تا صفر نوشتن در تولید تضمین شود؛ FR-CAL-008 به‌صورت فایل خروجی برای اعمال توسط مالک Zabbix (BQ-02) | 0.5 (G0)؛ مجوز نوشتن تا 2 (G1) |
| Q-27 | آیا فعال‌سازی آمار IBM MQ (MQI statistics / queue monitoring، مثلاً برای نرخ‌ها و oldest message age) روی queue managerهای تولیدی پذیرفتنی است؟ آیا از قبل فعال است؟ | exporter `mq_prometheus` عمق و سن قدیمی‌ترین پیام را می‌دهد ولی نرخ‌ها/زمان‌بندی‌ها به فعال بودن آمار نیاز دارند؛ این تغییر پیکربندی است و با NFR-IMP-002 در تعارض است مگر تأیید شود | FR-MQ-002 (منبع داده)، FR-MQ-004، PoC-3، NFR-IMP-002 (استثنا E-01) | تیم middleware + کمیتهٔ تغییر | پذیرفتنی نیست؛ فقط داده‌های بدون تغییر پیکربندی (depth و هر آنچه از قبل فعال است)؛ FR-MQ-004 با depth + نرخ مشتق | 0.5 (G0، وضعیت فعلی)؛ 2 (G1، تصمیم) |
| Q-28 | آیا مدیران Splunk/ES حاضرند saved search/aggregation زمان‌بندی‌شده را برای پلتفرم میزبانی کنند (یا اجرای ad-hoc REST را ترجیح می‌دهند)؟ سهمیهٔ جست‌وجو چیست؟ | در گزینهٔ E محاسبهٔ KPI تراکنش داخل Splunk/ES انجام و فقط تجمیع دقیقه‌ای pull می‌شود؛ بار و مالکیت این جست‌وجوها باید توافق شود | PoC-2، FR-TXN-003، FR-INT-002/003، NFR-IMP-001 | مدیران Splunk/ES | پلتفرم **تعریف** saved search/aggregation را تولید می‌کند و مدیران منبع آن را نصب و مالکیت می‌کنند؛ حساب پلتفرم فقط‌خواندنی می‌ماند و فقط نتایج را می‌خواند؛ اگر نپذیرند، جست‌وجوی REST توسط `connector-runtime` با budget Q-22 (BQ-04) | 0.5 (G0) |

## ۴. فرض‌ها

### ۴.۱ فرض‌های عمومی (A)

| ID | فرض | اثر در صورت نادرستی | روش اعتبارسنجی |
|---|---|---|---|
| A-01 | یک بانک، تک‌مستأجر؛ چند محیط (PROD، DR، UAT) و دو سایت (DC اصلی، DR) | نیاز به multi-tenancy یا چندسایتی فعال؛ تغییر مدل داده و IAM | Q-11، Q-05 |
| A-02 | VMهای لینوکس سازگار با RHEL؛ Kubernetes برای پلتفرم فرض نمی‌شود | تغییر مدل بسته‌بندی (Helm زودتر یا runtime دیگر) | Q-02، Q-03 |
| A-03 | تیم تحویل ۴–۸ مهندس | برآوردهای master plan و دامنه فازها نامعتبر | Q-01 |
| A-04 | دانه‌بندی دقیقه‌ای برای KPI کسب‌وکاری و ۱۰–۶۰ ثانیه برای صف/LB کافی است | نیاز به جریان زیرثانیه/streaming و Kafka زودتر | تأیید عملیات؛ PoC-4؛ مرور VS-1 |
| A-05 | لاگ برنامه‌های کلیدی بانکی به Splunk و/یا Elasticsearch می‌رسد | VS-1 بدون شاخص تراکنش؛ نیاز به جمع‌آوری لاگ با collector | Q-07، EXP-01، PoC-2 |
| A-06 | Zabbix و/یا SolarWinds میزبان‌ها، تجهیزات شبکه و بخشی از middleware را پوشش می‌دهند (پس از benchmark: احتمالاً با templateهای رسمی F5/HAProxy/RabbitMQ/MSSQL؛ IBM MQ template رسمی ندارد) | نیاز به collector مستقیم (Prometheus/SNMP) زودتر | Q-08، Q-26، EXP-02، PoC-1 |
| A-07 | Active Directory موجود است؛ وجود IdP OIDC/SAML نامعلوم | تغییر طراحی IAM | Q-04 |
| A-08 | NTP وجود دارد ولی skew چندثانیه‌ای بین منابع ممکن است | همبستگی نادرست یا پنجره‌های بزرگ‌تر و confidence کمتر | Q-17، EXP-05 |
| A-09 | حساب‌های API read-only/حداقل دسترسی پس از بازبینی امنیتی قابل اعطاست | انسداد PoC و فاز ۲ (R-02) یا نیاز به webhook/import فایل | EP-22، EP-23، EXP-02، EXP-04 |
| A-10 | منطقه زمانی Asia/Tehran؛ تقویم جلالی برای UI و تقویم کسب‌وکار | — (پشتیبانی میلادی وجود دارد) | تأیید مالک محصول؛ Q-23 |
| A-11 | سناریوهای Low/Medium/High واقعیت را در بر می‌گیرند | اندازه‌گذاری نادرست؛ تغییر triggerهای ClickHouse/Kafka/sharding | Q-06، PoC-5، BM-01..03 (در صورت trigger) |
| A-12 | بدون cloud عمومی؛ بدون Internet از هیچ نود پلتفرم؛ mirror داخلی قابل تأمین | فرایند ساخت و به‌روزرسانی امنیتی کندتر (R-09) | Q-09 |
| A-13 | تأخیر تشخیص در حد دقیقه (≤ 2–5 min) برای منابع pull در فازهای اولیه قابل‌قبول است | نیاز به push/webhook یا polling پرتکرار (بار بیشتر روی منابع) | تأیید عملیات/NOC؛ PoC-4 (≤ ۲ بازهٔ polling) |

### ۴.۲ فرض‌های ظرفیت (CAP) — فهرست مرکزی به‌صورت ارجاع

مقادیر و فرمول‌ها در [capacity-planning.md](capacity-planning.md) نگهداری می‌شوند (مالک مقدار آن سند است)؛ این جدول فقط شناسه، موضوع و راه اعتبارسنجی را متمرکز می‌کند تا از تکرار عدد جلوگیری شود. پس از benchmark، CAPهای ویژهٔ ClickHouse فقط در صورت فعال شدن trigger [ADR-0008](architecture-decision-records/ADR-0008-telemetry-store-postgresql-first-clickhouse-on-trigger.md) کاربرد دارند.

| ID | موضوع | کاربرد پس از benchmark | اعتبارسنجی |
|---|---|---|---|
| CAP-01 | اندازه فشرده نمونه متریک خام در ClickHouse | فقط سناریوی own-ingestion / پس از trigger | BM-01 |
| CAP-02 | اندازه ردیف rollup یک‌دقیقه‌ای | ClickHouse پس از trigger؛ معادل PostgreSQL در سناریوی reuse | BM-01؛ PoC-5 (معادل PG) |
| CAP-03 | اندازه ردیف rollup یک‌ساعته | همان | BM-01؛ PoC-5 |
| CAP-04 | اندازه ردیف رویداد نرمال‌شده | هر دو سناریو | PoC-5؛ BM-03 |
| CAP-05 | اندازه ردیف خلاصه تراکنش | هر دو سناریو؛ عامل اصلی trigger در Medium | PoC-5؛ BM-03 |
| CAP-06 | اندازه رویداد tokenized تراکنش (L2) | فقط در صورت نیاز به L2 > ~۱۰۰ رویداد/ثانیه (trigger) | BM-03 |
| CAP-07 | ردیف تاریخچه سلامت/ناهنجاری | هر دو سناریو | PoC-5 |
| CAP-08 | حجم سیم OTLP به ازای نقطه متریک | فقط با `edge-collector`/OTLP (FR-ING-002، P4) | BM-07 |
| CAP-09 | حجم سیم رویداد/خلاصه/رویداد تراکنش | batch API `connector-runtime` و OTLP | BM-07 |
| CAP-10 | حجم صف دیسکی collector به ازای نقطه | فقط در صورت استقرار `edge-collector` | BM-07 |
| CAP-11 | سهم بزرگ‌ترین Zone از کل حجم | هر دو سناریو | Q-06 |
| CAP-12 | سقف استفاده از دیسک (فضای merge/رشد) | ClickHouse؛ برای PostgreSQL مقدار معادل در capacity-planning | BM-01؛ PoC-5 |
| CAP-13 | قراردادهای طول دوره (ماه، ۱۳ ماه، ۳ و ۵ سال) | همه محاسبات | — |
| CAP-14 | اندازهٔ ردیف PostgreSQL برای نمونه/rollup/خلاصهٔ تراکنش/سلامت شامل index (0.2) | سناریوی reuse (PostgreSQL) | PoC-5 |
| CAP-15 | اندازهٔ ردیف رویداد نرمال‌شده در PostgreSQL شامل index (0.2) | سناریوی reuse (PostgreSQL) | PoC-5 |
| CAP-16 | تاریخچهٔ سلامت در reuse: تغییر حالت + snapshot دوره‌ای (0.2) | سناریوی reuse | PoC-5 |
| CAP-17 | تعداد TransactionType به ازای سرویس و در هر saved search (0.2) | بودجهٔ پرس‌وجوی سمت منبع | PoC-2؛ BM-04؛ Q-06 |

**سناریوی reuse (spine-benchmark B6، [فرضیه]):** Low = ۲k سری مرتبط با KPI @60 s (≈ ۳۳ نمونه/ثانیه) + ۲۰ ردیف خلاصهٔ تراکنش/ثانیه + ۵ رویداد/ثانیه؛ Medium = ۲۰k سری @30 s (≈ ۶۷۰ نمونه/ثانیه) + ۲۰۰ ردیف/ثانیه + ۵۰ رویداد/ثانیه؛ اندازهٔ ردیف PostgreSQL ≈ ۱۰۰–۲۰۰ بایت شامل index. جدول کامل در [capacity-planning.md](capacity-planning.md)؛ اعتبارسنجی در PoC-5.

## ۵. فرضیه‌ها

شناسه‌های H-xx فقط محلی همین سند‌اند.

| ID | فرضیه [فرضیه] | آزمون | ADR/تصمیم متأثر |
|---|---|---|---|
| H-01 | (در صورت trigger) ClickHouse یک‌نود/دو replica سناریوی Medium را با سخت‌افزار متعارف ingest و ذخیره می‌کند | BM-01، BM-02 در EXP-03 — **فقط اگر trigger ADR-0008 فعال شود** | ADR-0008 |
| H-02 | شمارش‌های دقیقه‌ای (L0) از لاگ‌های موجود برای تشخیص hop خراب در VS-1 کافی است | EXP-01 + PoC-2 + PoC-4 + replay رخدادها | ADR-0019، معیار VS-1 |
| H-03 | استخراج تجمیعی از Splunk/ES در بودجه قابل‌قبول مدیران منبع ممکن است | PoC-2؛ BM-04 | ADR-0009، ADR-0020، ADR-0021 |
| H-04 | polling read-only IBM MQ (یا scrape `mq_prometheus`) با دوره 10–60 s اثر قابل اغماض دارد | PoC-3؛ BM-06 در EXP-04 | EP-09 |
| H-05 | بدون Kafka، batching در `connector-runtime` (و صف دیسکی collector در صورت استقرار) برای Low/Medium کافی است | BM-07 | ADR-0010 |
| H-06 | موتور همبستگی قطعی روی PostgreSQL در ≤ 60 s برای Medium اجرا می‌شود | PoC-4 (کارکرد)؛ BM-08 (کارایی) | ADR-0012 |
| H-07 | baselineهای time-of-week/DayType هشدار کاذب را نسبت به آستانه ایستا کاهش می‌دهد | harness ارزیابی فاز ۵ | ADR-0013 |
| H-08 | skew ساعت منابع در اکثر موارد کمتر از پنجره ۳۰–۶۰ ثانیه همبستگی است | EXP-05 | پارامترهای `correlation` |
| H-09 | PostgreSQL (جداول partitioned و time-bucketed) KPIهای مشتق را در Low و Medium (با نگهداشت محدود Q-16) در اهداف NFR-PRF-003 ذخیره و query می‌کند | PoC-5 | ADR-0008 (آستانه‌های trigger) |
| H-10 | business service tree و SLA در Zabbix می‌توانند برش VS-1 و پنجره‌های expected-inactive را برای سلامت زیرساختی نمایش دهند | PoC-1 | ADR-0023، FR-INT-008، FR-CAL-008 |
| H-11 | جست‌وجوی زمان‌بندی‌شده در Splunk/ES شمارش دقیقه‌ای outcome (فنی در برابر کسب‌وکاری) و صدک‌های latency را تولید می‌کند | PoC-2 (توسعهٔ EXP-01) | دامنهٔ FR-TXN، ارزش هستهٔ سفارشی |
| H-12 | معیارهای لازم MQ برای FR-MQ-004 بدون تغییر پیکربندی و با حداقل مجوز به‌دست می‌آیند | PoC-3 | FR-MQ-002/004، Q-27 |
| H-13 | رتبه‌بندی شواهد قطعی و توپولوژی‌آگاه، hop خراب واقعی را در top-3 برای ≥ ۲ از ۳ حادثه قرار می‌دهد و برای اپراتور مفید است | PoC-4 | ADR-0012، FR-COR-004 |

## ۶. آزمایش‌ها (فقط پس از تأیید)

### ۶.۱ آزمایش‌های کشف داده (EXP)

| ID | هدف | روش | پیش‌نیازها (شامل تأییدها) | خروجی | تصمیم متأثر |
|---|---|---|---|---|---|
| EXP-01 | شناخت فیلدهای لاگ سرویس VS-1 (کد outcome، کلیدها، latency، قالب زمان) | بانک نمونه لاگ را ناشناس‌سازی می‌کند؛ بررسی در محیط تأییدشده بانک؛ فهرست‌برداری فیلد و فراوانی کدها؛ هیچ داده خام از محیط خارج نمی‌شود | M0؛ Q-13؛ تأیید امنیت اطلاعات و مالک سرویس؛ فرایند ناشناس‌سازی | گزارش فیلدها، OutcomeMapping پیش‌نویس، سطح L0/L2 ممکن | دامنه VS-1، EP-11، ADR-0019؛ ورودی PoC-2 |
| EXP-02 | آزمون استخراج read-only از Zabbix/ES/Splunk | حساب read-only در sandbox یا UAT منبع؛ اجرای query‌های نمونه؛ اندازه‌گیری زمان پاسخ و بار؛ ضبط fixtureهای redact‌شده | M0؛ تأیید امنیت و مالکان منابع؛ قاعده فایروال آزمون | fixtureها، capability per version، داده BM-04/05 اولیه | EP-08، ADR-0020، Q-08؛ هم‌پوشان با PoC-1/PoC-2 |
| EXP-03 | benchmark ClickHouse (BM-01..BM-03) — **مشروط** | فقط اگر PoC-5 یا Q-06 نشان دهد trigger ADR-0008 فعال است | trigger فعال؛ سخت‌افزار نماینده (Q-18)؛ images آفلاین | گزارش BM-01..03 | ADR-0008، اندازه‌گذاری |
| EXP-04 | آزمون حداقل مجوز read-only IBM MQ | تعریف کاربر با مجوزهای inquire/display حداقل در qmgr آزمون؛ خواندن depth، rates و (در صورت در دسترس بودن) oldest msg age، channel status؛ آزمون `$SYS` topics/رویدادهای آمار در صورت فعال بودن؛ `RESET QSTATS` ممنوع؛ اندازه‌گیری اثر (BM-06) | تأیید تیم middleware و امنیت؛ qmgr غیرتولیدی | فهرست مجوزهای حداقل، اثر polling | EP-09، NFR-SEC-003؛ بخشی از PoC-3 |
| EXP-05 | نظرسنجی clock skew بین منابع | مقایسه timestamp رویدادهای هم‌زمان شناخته‌شده یا متریک‌های NTP منابع؛ بدون تغییر در منابع | تأیید زیرساخت؛ دسترسی خواندنی | توزیع skew per source | پنجره همبستگی، Q-17، R-11 |

### ۶.۲ الزامات Evidence PoC (فاز 0.5، EP-23)

برنامهٔ زمانی، گیت G-PoC و Storyها در [master-plan.md](../roadmap/master-plan.md) §۵.۰.۵ و [implementation-backlog.md](../roadmap/implementation-backlog.md) (EP-23)؛ راهبرد اعتبارسنجی در [quality-and-validation-strategy.md](../testing/quality-and-validation-strategy.md). الزامات اجباری PoC:

| # | الزام |
|---|---|
| PR-01 | **فقط‌خواندنی:** صفر نوشتن در سیستم‌های منبع تولیدی (Zabbix، SolarWinds، Splunk، ES، IBM MQ، LB)؛ هیچ تغییر پیکربندی منبع؛ saved searchها فقط توسط مدیران منبع و با تأیید Q-28 نصب می‌شوند؛ **PoC-1 (ایجاد سرویس/SLA/tag آزمایشی) فقط روی نمونهٔ غیرتولیدی Zabbix**. |
| PR-02 | **محدود در زمان:** time-box توافق‌شده در M0 (پیشنهاد: ۴–۶ هفته [فرض]) با بازبینی میانی؛ پایان time-box بدون نتیجه = گزارش «ناموفق/نامعلوم»، نه تمدید ضمنی. |
| PR-03 | **کد دورریختنی:** کد PoC در مخزن/شاخهٔ جدا، با برچسب `poc-throwaway`؛ هیچ بخشی از آن بدون بازنویسی در `platform-core` استفاده نمی‌شود (T-01-01 از صفر). |
| PR-04 | **آفلاین:** اجرای کامل بدون Internet روی VM یا ایستگاه کار تأییدشده در شبکهٔ بانک. |
| PR-05 | **داده:** ابتدا دادهٔ موجود Zabbix/Splunk/ES؛ در غیر این صورت دادهٔ مصنوعی با برچسب صریح «synthetic» و contract fixtureها؛ هیچ دادهٔ حساس در ذخیره‌گاه PoC (ممیزی redaction با نرمال‌سازی ارقام فارسی/عربی-هندی، NFR-PRV-002). |
| PR-06 | **بار منبع:** همه پرس‌وجوها در budget توافق‌شدهٔ موقت (Q-22، Q-28) و با اندازه‌گیری مصرف. |
| PR-07 | **برچسب‌گذاری خروجی:** هر خروجی PoC «واقعیت مشاهده‌شده» را از «علت استنباطی» جدا و confidence عددی دارد. |
| PR-08 | **حذف پس از PoC:** حساب‌های موقت غیرفعال، دادهٔ PoC حذف و گواهی حذف ثبت می‌شود. |

| PoC | پرسش | تصمیمی که حل می‌کند | پیش‌نیاز | فرضیه |
|---|---|---|---|---|
| PoC-1 | Zabbix services/SLA (tagها، درخت سرویس، زمان‌بندی SLA، excluded downtime) می‌تواند برش VS-1 و پنجره‌های expected-inactive را نمایش دهد؟ | ADR-0023: reuse در برابر سلامت/SLA سفارشی؛ دامنهٔ FR-INT-008 و FR-CAL-008 | Q-26، **نمونهٔ غیرتولیدی Zabbix** (هم‌نسخه)، حساب read-only Zabbix تولید برای مقایسه | H-10 |
| PoC-2 | saved search/aggregation در Splunk/ES شمارش دقیقه‌ای outcome (فنی/کسب‌وکاری) و صدک latency را می‌دهد؟ | ارزش هستهٔ سفارشی و دامنهٔ FR-TXN (توسعهٔ EXP-01)؛ انتخاب پلتفرم لاگ VS-1 | Q-07، Q-22، Q-28، EXP-01 | H-02، H-03، H-11 |
| PoC-3 | معیارهای MQ read-only (`mq_prometheus` یا itemهای موجود Zabbix) با حداقل مجوز؛ آیا فعال‌سازی آمار MQI تأیید می‌شود؟ | منبع دادهٔ FR-MQ-002/004؛ استثنای E-01 | Q-27، EXP-04 | H-04، H-12 |
| PoC-4 | رتبه‌بندی شواهد روی ۳ حادثهٔ تاریخی (یا مصنوعی): افت throughput/افزایش latency همراه با رشد صف یا خرابی downstream؛ شناسایی سرویس متأثر | ADR-0012 و مفید بودن برای اپراتور | PoC-1..3 (داده)، فهرست حوادث با عملیات | H-06، H-13 |
| PoC-5 | ذخیره و query KPIهای مشتق در PostgreSQL در حجم مصنوعی Low/Medium | آستانه‌های trigger ClickHouse در ADR-0008 | VM آزمون؛ Q-16 (پیش‌فرض) | H-09 |

**معیار پذیرش PoC (G-PoC):** (۱) افت throughput یا افزایش latency تزریقی/واقعی در ≤ ۲ بازهٔ polling تشخیص داده شود؛ (۲) با رشد صف/خرابی downstream همبسته و سرویس کسب‌وکاری متأثر نام برده شود؛ (۳) برای ≥ ۲ از ۳ حادثه، hop خراب واقعی در top-3 فرضیه‌ها باشد؛ (۴) هر خروجی واقعیت را از استنباط با confidence جدا کند؛ (۵) اجرای کاملاً آفلاین؛ (۶) صفر نوشتن در منابع؛ (۷) بار منبع در budget توافق‌شده؛ (۸) صفر دادهٔ حساس در ذخیره‌گاه PoC (ممیزی redaction).

### ۶.۳ فهرست استثناهای NFR-IMP-002

فرایند استثنا در [non-functional-requirements.md](../requirements/non-functional-requirements.md) (NFR-IMP-002) تعریف شده است. هیچ استثنایی تاکنون تأیید نشده است.

| ID | تغییر پیشنهادی در سیستم موجود | دلیل | پرسش | وضعیت |
|---|---|---|---|---|
| E-01 | فعال‌سازی آمار/monitoring صف IBM MQ (نرخ‌ها، oldest message age) | داده‌های تکمیلی FR-MQ-002؛ FR-MQ-004 بدون آن هم کار می‌کند | Q-27، PoC-3 | پیشنهادی — تأییدنشده |
| E-02 | نوشتن maintenance/زمان‌بندی SLA در Zabbix توسط پلتفرم | FR-CAL-008 (جلوگیری از suppression تکراری)؛ جایگزین: فایل خروجی | Q-26، PoC-1 | پیشنهادی — تأییدنشده |
| E-03 | نصب saved search زمان‌بندی‌شده در Splunk/ES **توسط مدیران منبع** از روی تعریف تولیدشده توسط پلتفرم (حساب پلتفرم فقط‌خواندنی) | محاسبهٔ KPI در منبع (گزینهٔ E)؛ جایگزین: جست‌وجوی REST توسط `connector-runtime` | Q-28، PoC-2 | پیشنهادی — تأییدنشده |

## ۷. ریسک‌های افزوده پس از benchmark

ثبت کامل (احتمال، اثر، کاهش، مالک) در [master-plan.md](../roadmap/master-plan.md) §۸.

| ID | ریسک | پرسش‌های مرتبط |
|---|---|---|
| R-16 | تداوم لایسنس/تحریم ابزارهای تجاری موجود (Splunk، SolarWinds) — مسیر reuse به آن‌ها وابسته است | Q-24، Q-25 |
| R-17 | وابستگی به قابلیت‌ها و نقشهٔ راه Zabbix (تغییرات 8.0، API services/SLA) | Q-26، PoC-1 |
| R-18 | تعهدات AGPL در صورت تغییر یا توزیع Zabbix/Grafana توسط پلتفرم | Q-21 |

همچنین: R-09 (زنجیرهٔ تأمین آفلاین) اکنون شامل اثر تحریم بر مخازن بسته و لایسنس‌ها نیز هست (معیار ADR-0016).

## ۸. پرسش‌های فروشنده

> **رابطه با benchmark:** پرسش‌های خلاصهٔ فروشنده با شناسه‌های VQ-01..VQ-07 در [product-benchmark.md](../benchmark/product-benchmark.md) مرجع‌اند؛ این بخش فقط متن دقیق قابل ارسال را به تفکیک فروشنده می‌دهد و هر گروه به VQ مربوط ارجاع دارد (V-00 ⊂ VQ-01، V-D ⊂ VQ-02، V-S ⊂ VQ-03، V-I ⊂ VQ-04، V-A ⊂ VQ-05، V-N/V-R ⊂ VQ-06؛ پرسش Zabbix: VQ-07).
>
> **هشدار:** ارسال هر پرسشی به فروشندگان آمریکایی (Dynatrace، Cisco/Splunk، IBM) یا حتی غیرآمریکایی، خود تعامل تجاری است و باید **پس از** نظر حقوقی Q-24 انجام شود. طبق [ADR-0022](architecture-decision-records/ADR-0022-commercial-platforms-not-adopted.md) این محصولات پذیرفته نشده‌اند؛ پرسش‌های زیر فقط برای حالتی است که مجوز حقوقی صادر شود و بانک بخواهد گزینهٔ C را دوباره بسنجد. همهٔ ادعاهای قابلیت در [product-benchmark.md](../benchmark/product-benchmark.md) تا پاسخ مکتوب فروشنده یا PoC «تأییدنشده — نیازمند تأیید فروشنده یا PoC» هستند.

**مشترک برای همهٔ فروشندگان (VQ-01)**

- V-00a آیا با بانکی ایرانی که مشمول E.O. 13902 است قرارداد، تحویل لایسنس، پشتیبانی و به‌روزرسانی ارائه می‌کنید؟ مبنای حقوقی (مجوز OFAC یا معادل) چیست؟
- V-00b در حالت کاملاً آفلاین، فعال‌سازی لایسنس، تمدید و دریافت به‌روزرسانی/patch چگونه است و آیا هر کدام نیاز به ارتباط با فروشنده یا واسطه دارد؟
- V-00c پس از انقضای لایسنس یا قطع پشتیبانی، کدام قابلیت‌ها متوقف می‌شوند (جست‌وجو، ingest، UI، API)؟

**Dynatrace (Managed، offline) — VQ-02**

- V-D1 بند صادرات و کشورهای تحریمی در Master Subscription Agreement فعلی (متن در دسترس نبود) چیست؟
- V-D2 آیا در Managed offline، extensionها (مانند IBM MQ extension) بدون دسترسی به Extensions Hub قابل import هستند و پشتیبانی می‌شوند؟
- V-D3 آیا Business Events یا معادلی برای KPI کسب‌وکاری (موفقیت فنی در برابر کسب‌وکاری) روی Managed، بدون Grail، وجود دارد یا برنامه‌ریزی شده است؟
- V-D4 آیا Davis root-cause برای هر علت محتمل confidence عددی ارائه می‌کند؟
- V-D5 آیا تقویم کسب‌وکار (تعطیلات غیرمیلادی، cut-offهای پرداخت) جز از طریق maintenance window با API پشتیبانی می‌شود؟
- V-D6 آیا اتصال بومی به Zabbix، SolarWinds یا Splunk وجود دارد؟

**Splunk / Cisco (ITSI روی Splunk Enterprise موجود) — VQ-03**

- V-S1 وضعیت لایسنس فعلی Splunk بانک (نوع، انقضا، کانال تأمین) از نظر شما چیست و آیا افزودن ITSI ممکن است؟ (پرسش داخلی هم‌ارز: Q-25)
- V-S2 رفتار رسمی Splunk Enterprise پس از انقضای لایسنس دوره‌ای (گزارش انجمن: ingest ادامه، جست‌وجو مسدود) چیست؟
- V-S3 آیا ITSI تقویم تعطیلات/روز کاری غیرمیلادی یا time policy مبتنی بر lookup تعطیلات را بدون توسعهٔ سفارشی پشتیبانی می‌کند؟
- V-S4 آیا Episode Review/aggregation policies یا Predictive Analytics علت محتمل با confidence عددی ارائه می‌کنند؟ پیش‌نیازهای Predictive Analytics (مثلاً MLTK) چیست؟
- V-S5 آیا add-on پشتیبانی‌شده‌ای برای IBM MQ (depth، oldest message age) و SolarWinds (add-on فعلی archived است) وجود دارد؟

**IBM (Instana self-hosted، IBM MQ) — VQ-04**

- V-I1 آیا نصب air-gapped Standard Edition بدون هیچ ارتباط فروشنده (لایسنس و image bundle) ممکن است؟
- V-I2 آیا Probable Root Cause (preview) روی self-hosted در دسترس است و آستانهٔ confidence آن چگونه پیکربندی می‌شود؟
- V-I3 فهرست رسمی متریک‌های IBM MQ sensor (به‌ویژه `oldestMessage`) و پیش‌نیاز آن (فعال بودن monitoring صف) چیست؟
- V-I4 آیا مجوز استفاده و به‌روزرسانی exporter متن‌باز `mq_prometheus` و کتابخانه‌های کلاینت MQ برای بانک (با توجه به تحریم) مسئلهٔ حقوقی ایجاد می‌کند؟ (ارجاع به Q-24)
- V-I5 آیا Instana تقویم کسب‌وکار یا پنجرهٔ سرویس غیر از maintenance دارد؟

**Cisco AppDynamics (on-prem / Virtual Appliance) — VQ-05**

- V-A1 بیانیهٔ صادرات Cisco می‌گوید محصولات در ایران عرضه نمی‌شوند؛ آیا هیچ مسیر مجاز وجود دارد؟ (انتظار: خیر)
- V-A2 آیا Virtual Appliance در حالت air-gapped پشتیبانی می‌شود؟
- V-A3 آیا Anomaly Detection & RCA روی on-prem/VA در دسترس است و confidence عددی دارد؟ هزینهٔ add-on؟
- V-A4 متریک‌های depth و oldest message age IBM MQ از کدام extension و با چه پشتیبانی‌ای ارائه می‌شوند؟

**INETCO (Insight) — VQ-06**

- V-N1 با توجه به مقررات تحریمی کانادا و ریسک تحریم ثانویهٔ آمریکا، آیا به بانک ایرانی می‌فروشید و پشتیبانی می‌کنید؟ آیا محصول محتوای کنترل‌شدهٔ آمریکایی (EAR) دارد؟
- V-N2 آیا decoderهای ISO 8583 شما گونه‌های شتاب/ساتنا/پایا (و قالب‌های اختصاصی) را decode می‌کنند؟ آیا PoC روی ترافیک ضبط‌شدهٔ ناشناس ممکن است؟
- V-N3 مدل استقرار (appliance، نرم‌افزار روی VM)، نیاز به TAP/SPAN، و نصب کاملاً آفلاین؟
- V-N4 آیا خروجی (API/export) برای ارسال شاخص‌های تراکنش به پلتفرم دیگر وجود دارد؟

**IR (Transact / Prognosis) — VQ-06**

- V-R1 با توجه به مقررات تحریمی استرالیا و ریسک تحریم ثانویه، آیا به بانک ایرانی می‌فروشید و پشتیبانی می‌کنید؟
- V-R2 فهرست رسمی switchها و قالب‌های پیام پشتیبانی‌شده (Base24، Postilion و ...) و امکان decode قالب‌های شتاب/ساتنا/پایا؟
- V-R3 وضعیت «Iris AI Assistant» (beta) و آیا علت محتمل با confidence ارائه می‌کند؟
- V-R4 نصب کاملاً آفلاین، فعال‌سازی لایسنس و به‌روزرسانی؟

**Zabbix SIA — VQ-07:** متن در product-benchmark؛ پشتیبانی تجاری Zabbix نیز مشمول Q-24 است.

## ۹. شواهد ناموجود

شکاف‌های قابلیتی در [competitive-gap-analysis.md](../benchmark/competitive-gap-analysis.md) با شناسه‌های G-01..G-15 آمده‌اند و اینجا تکرار نمی‌شوند. مواردی که Product Benchmark نتوانست با شاهد کافی تأیید کند و نباید به‌عنوان واقعیت استفاده شوند (جزئیات و منابع در [product-benchmark.md](../benchmark/product-benchmark.md)):

| # | شاهد ناموجود | اطمینان فعلی | راه به‌دست آوردن |
|---|---|---|---|
| ME-01 | وضعیت لایسنس و مسیر به‌روزرسانی Splunk، SolarWinds و Elasticsearch موجود بانک (و نوع لایسنس ES: AGPL/SSPL/ELv2 یا اشتراک) | ناموجود | Q-25 |
| ME-02 | متن رسمی رفتار Splunk پس از انقضای لایسنس | کم–متوسط (انجمن) | Q-25؛ V-S2 |
| ME-03 | نظر حقوقی دربارهٔ ادامهٔ استفاده/patch نرم‌افزار آمریکایی موجود؛ مقررات کانادا/استرالیا برای INETCO/IR | ناموجود (مقررات کانادا/استرالیا بررسی نشد) | Q-24 |
| ME-04 | محتوای کامل صفحات SLA/services در مستندات Zabbix (فقط نام متدها از ناوبری تأیید شد) | متوسط | PoC-1؛ Q-26 |
| ME-05 | وضعیت انتشار Zabbix 8.0 (OTLP، correlation، پیش‌بینی) | نقشهٔ راه، منتشرنشده | بازبینی پیش از G1؛ R-17 |
| ME-06 | پشتیبانی RTL/جلالی در Grafana، Kibana، OpenSearch Dashboards | کم–متوسط (نبود شاهد) | Q-23؛ بررسی عملی در صورت نیاز |
| ME-07 | decode گونه‌های ISO 8583 شتاب در INETCO/IR؛ مدل استقرار INETCO | کم (ادعای فروشنده) | V-N2، V-R2 (پس از Q-24) |
| ME-08 | در دسترس بودن Probable RCA در Instana self-hosted؛ confidence عددی در Dynatrace Davis | متوسط/نبود شاهد | V-I2، V-D4 (پس از Q-24) |
| ME-09 | اینکه آمار IBM MQ در queue managerهای بانک از قبل فعال است | ناموجود | Q-27؛ PoC-3 |
| ME-10 | کیفیت لاگ VS-1 (کد outcome، latency، کلید) | ناموجود | Q-07؛ EXP-01؛ PoC-2 |
| ME-11 | اندازه‌گیری واقعی بار پرس‌وجوی Splunk/ES و Zabbix API در محیط بانک | ناموجود | PoC-2؛ BM-04؛ BM-05 |
| ME-12 | ساخت آفلاین OCB (نیاز به ماژول‌های Go) — فقط در صورت استقرار `edge-collector` | متوسط (مستندات راهنمای air-gap ندارد) | آزمون ساخت در صورت نیاز |
| ME-13 | پوشش فعلی NGINX، WAF، API gateway، ESB و JVM در ابزارهای موجود | تأییدنشده | EP-22 (US-22-07)؛ Q-08 |

## ۱۰. وضعیت تصمیم ADRها

Status طبق ADR-0001: فقط ADR-0001 `Accepted`؛ بقیه `Proposed`. Confidence: Firm | Provisional | Blocked.

| ADR | عنوان | Confidence | مسدود/مشروط به |
|---|---|---|---|
| ADR-0001 | record-architecture-decisions | Accepted | — |
| ADR-0002 | modular-monolith-with-edge-collectors | Firm | — (`edge-collector` اختیاری شد؛ ADR-0004) |
| ADR-0003 | backend-language-java-spring-boot | **Blocked** | Q-01 |
| ADR-0004 | collector-and-connector-architecture | Provisional | BM-07، EXP-02، PoC-3؛ `edge-collector` فقط در صورت نیاز |
| ADR-0005 | api-and-event-contracts | Firm | — |
| ADR-0006 | canonical-schemas-otel-plus-bank-namespace | Firm | — |
| ADR-0007 | metadata-store-postgresql | Firm | Q-03 (فهرست DBMS مجاز) |
| ADR-0008 | [telemetry-store-postgresql-first-clickhouse-on-trigger](architecture-decision-records/ADR-0008-telemetry-store-postgresql-first-clickhouse-on-trigger.md) (**تغییر 0.2:** پیش‌تر `telemetry-store-clickhouse`) | Provisional | PoC-5، Q-06، Q-16؛ BM-02، BM-03؛ triggerها: L2 > ~۱۰۰ رویداد/ثانیه، KPI مشتق > ~۵۰ ردیف/ثانیه پایدار یا > ~۱ TB برای ۱۳ ماه، یا شکست اهداف p95 |
| ADR-0009 | no-own-raw-log-store-federate-existing | Firm (فاز ۱–۵) | بازبینی در صورت شکست BM-04/PoC-2 یا retention منابع؛ Q-25 |
| ADR-0010 | buffering-without-kafka-initially | Provisional (تقویت‌شده: Kafka فعلاً رد) | BM-07، Q-06، Q-09 |
| ADR-0011 | topology-model-and-dependency-discovery | Provisional | EXP-01، EXP-02، Q-08 (کشف در فاز ۴) |
| ADR-0012 | correlation-engine-design | Firm | PoC-4 (کارکرد)، BM-08 (کارایی) |
| ADR-0013 | analytics-staging-rules-statistics-ml | Firm | — |
| ADR-0014 | frontend-react-echarts-cytoscape | Provisional | Q-01، Q-21، Q-23، BM-10؛ دامنه محدود به ADR-0024 |
| ADR-0015 | authentication-authorization-audit | **Blocked** | Q-04 |
| ADR-0016 | offline-deployment-and-artifacts | **Blocked** | Q-02، Q-03؛ معیار تحریم/دسترسی بسته: Q-24 (0.2) |
| ADR-0017 | ha-dr-architecture | **Blocked** | Q-05 |
| ADR-0018 | data-retention-privacy-capacity-controls | Firm (حریم خصوصی) / Provisional (retention) | Q-16، Q-10 |
| ADR-0019 | transaction-correlation-tiers | Firm | — |
| ADR-0020 | existing-platform-integration-read-only-pull | Firm | استثناهای E-01..E-03 فقط با تأیید |
| ADR-0021 | [build-buy-reuse-strategy](architecture-decision-records/ADR-0021-build-buy-reuse-strategy.md) (گزینهٔ E، امتیاز ۷۸ در برابر D ۷۵، B ۷۰، A ۶۶، C ۵۶ مردود؛ انتخاب کم‌پشیمانی) | Provisional (PoC-gated) | G-PoC (PoC-1..5)، Q-25 |
| ADR-0022 | [commercial-platforms-not-adopted](architecture-decision-records/ADR-0022-commercial-platforms-not-adopted.md) | Firm مگر با مجوز حقوقی | Q-24 (شرط بازبینی) |
| ADR-0023 | [reuse-zabbix-services-and-sla](architecture-decision-records/ADR-0023-reuse-zabbix-services-and-sla.md) | **Blocked** | PoC-1، Q-26 |
| ADR-0024 | [visualization-split-custom-operator-ui-optional-grafana](architecture-decision-records/ADR-0024-visualization-split-custom-operator-ui-optional-grafana.md) | Provisional | Q-23، Q-21 |

**خلاصه:** Accepted: 1 · Firm: 10 (به‌علاوه بخش حریم خصوصی ADR-0018) · Provisional: 7 (به‌علاوه بخش retention ADR-0018) · Blocked: 5.

## نکته برای بازبینی

- Q-09 در spine جزو پرسش‌های مسدودکننده نیست، اما در دسته A آمده، زیرا T-01-01 «ساخت آفلاین با mirror داخلی» را لازم دارد. اگر بانک Kafka موجود داشته باشد (Q-09)، کفهٔ ترازو در ADR-0010 به نفع Kafka سنگین‌تر می‌شود، ولی اشتراک دامنهٔ خرابی با سیستم‌های پایش‌شده (NFR-AVL-002) باید سنجیده شود.
- پیوندهای قدیمی به فایل ناموجود `open-questions.md` در پوشهٔ requirements (در برخی اسناد معماری، مثلاً system-context) باید به همین فایل (`docs/architecture/open-questions-and-assumptions.md`) اصلاح شوند؛ اسناد زیر `docs/architecture/` در مالکیت نویسندهٔ دیگری‌اند.
- شناسه‌های H-xx، PR-xx، E-xx، V-xx و ME-xx فقط محلی همین سند هستند (G-xx، VQ-xx و BQ-xx محلی اسناد benchmark‌اند) و در spine تعریف نشده‌اند.
- دسته‌بندی گیت‌ها تغییر کرد: G0 اکنون «شروع PoC» است و پرسش‌های پیشین G0 (Q-01، Q-02، Q-04، Q-09) به G-PoC (شروع T-01-01) منتقل شدند. Q-07 و Q-13 زودتر (G0) لازم‌اند چون PoC به سرویس VS-1 نیاز دارد.
- مقادیر Confidence جدول ۱۰ بر اساس spine و spine-benchmark است؛ اگر نویسندهٔ ADRها وضعیتی را تغییر دهد (مثلاً ADR-0002/0004 به دلیل اختیاری شدن `edge-collector`)، این جدول باید هم‌راستا شود.
- time-box پیشنهادی PoC (۴–۶ هفته) فرض این سند است و در spine عددی ندارد.
