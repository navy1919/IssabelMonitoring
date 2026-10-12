# پرسش‌های باز، فرض‌ها و آزمایش‌ها — BSOIP

| فیلد | مقدار |
|---|---|
| Document ID | `ARC-OQA-001` |
| Version | `0.1` |
| Status | `Draft – Phase 0 – awaiting approval` |
| Date | `2026-10-11` (۱۴۰۵/۰۷/۱۹) |
| Owner | `Architecture` |
| Related documents | [master-plan.md](../roadmap/master-plan.md)، [implementation-backlog.md](../roadmap/implementation-backlog.md)، [ADR index](architecture-decision-records/README.md)، [capacity-planning.md](capacity-planning.md)، [technology-evaluation.md](technology-evaluation.md)، [non-functional-requirements.md](../requirements/non-functional-requirements.md)، [quality-and-validation-strategy.md](../testing/quality-and-validation-strategy.md) |

## ۱. هدف

این سند مرجع واحد همه عدم‌قطعیت‌های فاز ۰ است: واقعیت‌های تأییدشده (F)، فرض‌ها (A)، فرضیه‌ها، پرسش‌های باز (Q)، آزمایش‌ها (EXP) و وضعیت تصمیم هر ADR. اسناد دیگر با برچسب‌های **[فرض A-xx]**، **[فرضیه]** و **[باز Q-xx]** به اینجا ارجاع می‌دهند.

## ۲. واقعیت‌های تأییدشده

| ID | واقعیت | منبع |
|---|---|---|
| F-01 | مخزن `navy1919/IssabelMonitoring` در آغاز فاز ۰ خالی بود (بدون commit و فایل). بررسی‌شده در 2026-10-11. | بررسی مستقیم |
| F-02 | محدودیت‌های اعلام‌شده مالک محصول (Navy19): فقط on-premises در مرکز داده بانک، بدون Internet مستقیم؛ شروع با SolarWinds، Zabbix، Splunk و Elasticsearch موجود؛ ابزارگذاری مستقیم برنامه‌ها به فازهای بعد و بر اساس نیاز واقعی؛ معماری enterprise با تحویل تدریجی و بدون پیچیدگی غیرضروری؛ تمرکز بر سرویس‌ها و تراکنش‌های بانکی نه پایش زیرساخت؛ مستندات فارسی با شناسه/نام فایل/اصطلاحات انگلیسی؛ عدم پیاده‌سازی تا تأیید صریح. | مالک محصول |
| F-03 | متن منشور محصول (prompt فاز ۰): فهرست کامل قابلیت‌ها، دامنه‌ها و تحویل‌دادنی‌ها. | مالک محصول |

**هر چیز دیگر** فرض، فرضیه، قابلیت مستند فروشنده که «نیازمند تأیید در نسخه نصب‌شده بانک» است، یا توصیه است.

## ۳. پرسش‌های باز

### ۳.۱ دسته‌بندی برای یک جلسه بازبینی

پیشنهاد می‌شود همه پرسش‌ها در **یک جلسه بازبینی** با حضور مالک محصول، معماری، امنیت، عملیات/NOC، مدیران منابع (Zabbix/SolarWinds/Splunk/ES/MQ) و زیرساخت مرور شوند؛ ترتیب دستورجلسه بر اساس انسداد:

| دسته | زمان | پرسش‌ها | چرا این ترتیب |
|---|---|---|---|
| **A — مسدودکننده شروع (گیت G0)** | جلسه اول، ضروری | Q-01، Q-02، Q-04، Q-07، Q-13، Q-09 | T-01-01 و ADR-0003/0015/0016 و دامنه VS-1 به آن‌ها وابسته‌اند |
| **B — پیش از فاز ۲ (G1)** | همان جلسه، در صورت زمان؛ وگرنه پیگیری کتبی | Q-03، Q-08، Q-22، Q-21، Q-12، Q-11 | کانکتورها، استقرار، لایسنس، نام |
| **C — پیش از فاز ۳ (G2)** | پیگیری کتبی در فاز ۱ | Q-06، Q-15، Q-16، Q-17 | ظرفیت، تقویم، retention، زمان |
| **D — پیش از فاز ۴/۶ (G3/G4)** | پیگیری در فاز ۲–۳ | Q-05، Q-10، Q-14، Q-18، Q-19، Q-20 | HA/DR، انطباق، اعلان، عملیات |

### ۳.۲ جدول پرسش‌ها

| ID | پرسش | چرا مهم است | تصمیم‌های مسدود | پاسخ‌دهنده (نقش) | پیش‌فرض در صورت بی‌پاسخ | لازم تا فاز |
|---|---|---|---|---|---|---|
| Q-01 | اندازه و مهارت‌های تیم (Java/.NET/Go/Python/TS)؟ | زبان backend باید با تیم نگهدارنده سازگار باشد (R-04) | ADR-0003؛ T-01-01 | مدیر مهندسی / مدیر IT | Java 21 + Spring Boot 3.x (ADR-0003)، TypeScript برای UI | 1 (G0) |
| Q-02 | آیا Kubernetes/OpenShift داخلی برای میزبانی این پلتفرم وجود دارد و مجاز است؟ | مدل بسته‌بندی و عملیات | ADR-0016 | تیم زیرساخت/پلتفرم | containerهای OCI روی VM با Podman/Compose؛ Helm بعداً | 1 (G0) |
| Q-03 | استاندارد OS و فرایند تأمین VM؟ | hardening، runtime container، زمان نصب | ADR-0016؛ NFR-DEP-002 | تیم زیرساخت | RHEL-compatible (A-02) با Podman | 2 (G1) |
| Q-04 | IdP بانک: فقط AD، ADFS، یا ارائه‌دهنده OIDC/SAML؟ MFA؟ | مدل احراز هویت و SSO | ADR-0015؛ EP-02 | امنیت اطلاعات / تیم هویت | Keycloak با AD/LDAP federation؛ MFA در صورت پشتیبانی | 1 (G0) |
| Q-05 | RPO/RTO، توپولوژی سایت DR، هدف دسترس‌پذیری؟ | معماری HA/DR و هزینه سخت‌افزار | ADR-0017؛ EP-18 | مدیریت IT / BCP | 99.9% UI/API، RPO ≤ 15 min metadata، RTO ≤ 4 h، DR active-passive | 6 (G4) |
| Q-06 | اعداد بار: سرویس‌ها، موجودیت‌ها، سری‌های متریک، حجم لاگ/رویداد/تراکنش، کاربران، retention، SLO query، سایت‌ها، نسبت peak | اندازه‌گذاری، انتخاب سناریو | ADR-0008 (تأیید)، ADR-0010، NFR-SCL-001 | عملیات + مالکان منابع | سناریوی Medium (A-11) | 3 (G2)؛ نهایی تا 6 |
| Q-07 | برای سرویس VS-1: لاگ‌ها کجا هستند، آیا کد outcome/پاسخ، latency و کلیدهای همبستگی دارند؟ | تعیین سطح قابل دستیابی L0/L2 و معنای «موفقیت کسب‌وکاری» (R-01) | دامنه VS-1؛ EP-11 | مالک سرویس + تیم برنامه + مدیر Splunk/ES | فقط L0 و شاخص‌های فنی؛ موفقیت کسب‌وکاری «ناموجود» | 1 (G0)؛ پاسخ دقیق با EXP-01 |
| Q-08 | نسخه‌ها و API در دسترس: Zabbix، SolarWinds، Splunk، ES/OpenSearch، IBM MQ، RabbitMQ، F5، HAProxy، NGINX، WAF | انتخاب API، fixtureها، capabilityها (R-12) | EP-08، EP-09 | مالکان منابع | کانکتورها بر اساس مستندات عمومی با برچسب «نیازمند تأیید» | 2 (G1) |
| Q-09 | وجود Kafka، vault/HSM برای secret، registry داخلی (Nexus/Harbor/Artifactory)؟ | ساخت آفلاین، SecretProvider، توزیع image | T-01-01، T-01-04، ADR-0016، ADR-0010 | زیرساخت + امنیت | mirror موقت تحت مدیریت تیم؛ فایل رمز‌شده برای secret؛ بدون Kafka | 1 (G0) |
| Q-10 | الزامات مقرراتی: بانک مرکزی، کاربرد PCI DSS، محل داده، مدت نگهداری audit؟ | retention، کنترل‌ها، ممیزی (R-13) | ADR-0018 (اعداد)، NFR-CMP-001 | انطباق / امنیت | audit ≥ 5 سال؛ کنترل‌های PCI-like برای داده کارت | 6 (G4)؛ اثر بر retention از 2 |
| Q-11 | محصول برای یک بانک است یا چند بانک؟ | multi-tenancy | FR-ADM-005 | مالک محصول | تک‌بانک، تک‌مستأجر (A-01) | 2 (G1) |
| Q-12 | نام نهایی محصول و مخزن؟ نام `IssabelMonitoring` تداعی‌کننده Issabel PBX و نامرتبط است؛ آیا تغییر نام انجام شود؟ | شفافیت، جلوگیری از سردرگمی | نام artifactها و package‌ها | مالک محصول | نام کاری BSOIP؛ مخزن فعلی تا تصمیم | 2 (G1)، ترجیحاً پیش از T-01-01 |
| Q-13 | تأیید سرویس VS-1 (پیش‌فرض: کارت به کارت از Mobile Banking)؟ | تمرکز کشف داده و دمو | دامنه VS-1، EP-22 | مالک محصول + عملیات | کارت به کارت از Mobile Banking | 1 (G0) |
| Q-14 | کانال‌های اعلان (email، درگاه SMS) و ابزار ITSM؟ | EP-17 | EP-17، مسیر on-call | عملیات / ITSM | فقط UI + email | 4 (G3) |
| Q-15 | منبع تقویم تعطیلات و برنامه‌های ساتنا/پایا/پل بانک؟ | expected-inactive و SLO (R-14) | EP-04 داده، EP-12 | عملیات / واحد پرداخت | import دستی سالانه؛ پنجره‌ها توسط مالک سرویس | 3 (G2)؛ ساختار در 1 |
| Q-16 | retention به‌ازای کلاس داده؟ | ظرفیت و انطباق | ADR-0018 | انطباق + عملیات | spine §6: raw 30 d، rollup 1m 13 ماه، 1h 3 سال، رویداد 13 ماه، tokenized 30 d، audit ≥ 5 سال | 3 (G2) |
| Q-17 | وضعیت NTP/همگامی زمان در منابع؟ | همبستگی (R-11) | تحمل پنجره همبستگی | زیرساخت | skew چندثانیه‌ای ممکن (A-08)؛ اندازه‌گیری با EXP-05 | 3 (G2) |
| Q-18 | بودجه سخت‌افزار پلتفرم؟ | اندازه‌گذاری محیط‌ها | ADR-0017، مدل استقرار | مدیریت IT | Low برای UAT/VS-1؛ Medium برای PROD | 6 (G4)؛ UAT در 1 |
| Q-19 | چه تیمی پلتفرم را اداره می‌کند؟ ۲۴×۷؟ | runbookها، on-call، handover | مدل عملیات | مدیریت IT / NOC | NOC سطح اول، تیم تحویل سطح دوم تا M6 | 6 (G4) |
| Q-20 | حساب‌های آزمون synthetic و فرایند تأیید؟ | ایمنی synthetic (FR-DX-003) | EP-15 | عملیات + امنیت + مالک سرویس | synthetic فقط endpointهای غیرمالی و health | 4 (G3) |
| Q-21 | سیاست لایسنس متن‌باز (AGPL و ...)؟ | انتخاب ابزارها (Grafana OSS، ابزارهای اسکن) | ADR-0014، ابزارهای CI | حقوقی / امنیت | پرهیز از AGPL در اجزای توزیع‌شده؛ Grafana فقط اختیاری | 2 (G1) |
| Q-22 | سهمیه و ظرفیت Splunk/ES برای بار query اضافی؟ | بار و لایسنس (R-03) | query budget کانکتورها، BM-04 | مدیران Splunk/ES | budget محافظه‌کارانه: query تجمیعی دقیقه‌ای، هم‌زمانی ۱، بدون backfill طولانی | 2 (G1) |

## ۴. فرض‌ها

| ID | فرض | اثر در صورت نادرستی | روش اعتبارسنجی |
|---|---|---|---|
| A-01 | یک بانک، تک‌مستأجر؛ چند محیط (PROD، DR، UAT) و دو سایت (DC اصلی، DR) | نیاز به multi-tenancy یا چندسایتی فعال؛ تغییر مدل داده و IAM | Q-11، Q-05 |
| A-02 | VMهای لینوکس سازگار با RHEL؛ Kubernetes برای پلتفرم فرض نمی‌شود | تغییر مدل بسته‌بندی (Helm زودتر یا runtime دیگر) | Q-02، Q-03 |
| A-03 | تیم تحویل ۴–۸ مهندس | برآوردهای master plan و دامنه فازها نامعتبر | Q-01 |
| A-04 | دانه‌بندی دقیقه‌ای برای KPI کسب‌وکاری و ۱۰–۶۰ ثانیه برای صف/LB کافی است | نیاز به جریان زیرثانیه/streaming و Kafka زودتر | تأیید عملیات؛ مرور VS-1 |
| A-05 | لاگ برنامه‌های کلیدی بانکی به Splunk و/یا Elasticsearch می‌رسد | VS-1 بدون شاخص تراکنش؛ نیاز به جمع‌آوری لاگ با collector | Q-07، EXP-01 |
| A-06 | Zabbix و/یا SolarWinds میزبان‌ها، تجهیزات شبکه و بخشی از middleware را پوشش می‌دهند | نیاز به collector مستقیم (Prometheus/SNMP) زودتر | Q-08، EXP-02 |
| A-07 | Active Directory موجود است؛ وجود IdP OIDC/SAML نامعلوم | تغییر طراحی IAM | Q-04 |
| A-08 | NTP وجود دارد ولی skew چندثانیه‌ای بین منابع ممکن است | همبستگی نادرست یا پنجره‌های بزرگ‌تر و confidence کمتر | Q-17، EXP-05 |
| A-09 | حساب‌های API read-only/حداقل دسترسی پس از بازبینی امنیتی قابل اعطاست | انسداد فاز ۲ (R-02) یا نیاز به webhook/import فایل | EP-22، EXP-02، EXP-04 |
| A-10 | منطقه زمانی Asia/Tehran؛ تقویم جلالی برای UI و تقویم کسب‌وکار | — (پشتیبانی میلادی وجود دارد) | تأیید مالک محصول |
| A-11 | سناریوهای Low/Medium/High واقعیت را در بر می‌گیرند | اندازه‌گذاری نادرست؛ تغییر triggerهای Kafka/sharding | Q-06، BM-01..03 |
| A-12 | بدون cloud عمومی؛ بدون Internet از هیچ نود پلتفرم؛ mirror داخلی قابل تأمین | فرایند ساخت و به‌روزرسانی امنیتی کندتر (R-09) | Q-09 |
| A-13 | تأخیر تشخیص در حد دقیقه (≤ 2–5 min) برای منابع pull در فازهای اولیه قابل‌قبول است | نیاز به push/webhook یا polling پرتکرار (بار بیشتر روی منابع) | تأیید عملیات/NOC |

## ۵. فرضیه‌ها

شناسه‌های H-xx فقط محلی همین سند‌اند.

| ID | فرضیه [فرضیه] | آزمون | ADR/تصمیم متأثر |
|---|---|---|---|
| H-01 | ClickHouse یک‌نود/دو replica سناریوی Medium را با سخت‌افزار متعارف ingest و ذخیره می‌کند (فرض فشرده‌سازی 1.5–3 بایت/نمونه) | BM-01، BM-02 در EXP-03 | ADR-0008 |
| H-02 | شمارش‌های دقیقه‌ای (L0) از لاگ‌های موجود برای تشخیص hop خراب در VS-1 کافی است | EXP-01 + replay رخدادها | ADR-0019، معیار VS-1 |
| H-03 | استخراج تجمیعی از Splunk/ES در بودجه قابل‌قبول مدیران منبع ممکن است | BM-04 | ADR-0009، ADR-0020 |
| H-04 | polling read-only IBM MQ با دوره 10–60 s اثر قابل اغماض دارد | BM-06 در EXP-04 | EP-09 |
| H-05 | بدون Kafka، صف دیسکی collector + batching برای Low/Medium کافی است | BM-07 | ADR-0010 |
| H-06 | موتور همبستگی قطعی روی PostgreSQL/ClickHouse در ≤ 60 s برای Medium اجرا می‌شود | BM-08 | ADR-0012 |
| H-07 | baselineهای time-of-week/DayType هشدار کاذب را نسبت به آستانه ایستا کاهش می‌دهد | harness ارزیابی فاز ۵ | ADR-0013 |
| H-08 | skew ساعت منابع در اکثر موارد کمتر از پنجره ۳۰–۶۰ ثانیه همبستگی است | EXP-05 | پارامترهای `correlation` |

## ۶. آزمایش‌ها (فقط پس از تأیید)

| ID | هدف | روش | پیش‌نیازها (شامل تأییدها) | خروجی | تصمیم متأثر |
|---|---|---|---|---|---|
| EXP-01 | شناخت فیلدهای لاگ سرویس VS-1 (کد outcome، کلیدها، latency، قالب زمان) | بانک نمونه لاگ را ناشناس‌سازی می‌کند؛ بررسی در محیط تأییدشده بانک؛ فهرست‌برداری فیلد و فراوانی کدها؛ هیچ داده خام از محیط خارج نمی‌شود | M0؛ Q-13؛ تأیید امنیت اطلاعات و مالک سرویس؛ فرایند ناشناس‌سازی | گزارش فیلدها، OutcomeMapping پیش‌نویس، سطح L0/L2 ممکن | دامنه VS-1، EP-11، ADR-0019 |
| EXP-02 | آزمون استخراج read-only از Zabbix/ES/Splunk | حساب read-only در sandbox یا UAT منبع؛ اجرای query‌های نمونه کانکتور؛ اندازه‌گیری زمان پاسخ و بار؛ ضبط fixtureهای redact‌شده | M0؛ تأیید امنیت و مالکان منابع؛ قاعده فایروال آزمون | fixtureها، capability per version، داده BM-04/05 اولیه | EP-08، ADR-0020، Q-08 |
| EXP-03 | benchmark ClickHouse (BM-01..BM-03) | داده مصنوعی مطابق سناریوها روی سخت‌افزار نماینده؛ اندازه‌گیری ingest، فشرده‌سازی، latency query | سخت‌افزار نماینده (Q-18)؛ images آفلاین | گزارش BM-01..03 | ADR-0008، اندازه‌گذاری |
| EXP-04 | آزمون حداقل مجوز read-only IBM MQ | تعریف کاربر با مجوزهای inquire/display حداقل در qmgr آزمون؛ خواندن depth، rates، oldest msg age، channel status؛ اندازه‌گیری اثر (BM-06) | تأیید تیم middleware و امنیت؛ qmgr غیرتولیدی | فهرست مجوزهای حداقل، اثر polling | EP-09، NFR-SEC-003 |
| EXP-05 | نظرسنجی clock skew بین منابع | مقایسه timestamp رویدادهای هم‌زمان شناخته‌شده یا متریک‌های NTP منابع؛ بدون تغییر در منابع | تأیید زیرساخت؛ دسترسی خواندنی | توزیع skew per source | پنجره همبستگی، Q-17، R-11 |

## ۷. وضعیت تصمیم ADRها

Status طبق ADR-0001: فقط ADR-0001 `Accepted`؛ بقیه `Proposed`. Confidence: Firm | Provisional | Blocked.

| ADR | عنوان | Confidence | مسدود/مشروط به |
|---|---|---|---|
| ADR-0001 | record-architecture-decisions | Accepted | — |
| ADR-0002 | modular-monolith-with-edge-collectors | Firm | — |
| ADR-0003 | backend-language-java-spring-boot | **Blocked** | Q-01 |
| ADR-0004 | collector-and-connector-architecture | Provisional | EXP-02، BM-07 |
| ADR-0005 | api-and-event-contracts | Firm | — |
| ADR-0006 | canonical-schemas-otel-plus-bank-namespace | Firm | — |
| ADR-0007 | metadata-store-postgresql | Firm | — |
| ADR-0008 | telemetry-store-clickhouse | Provisional | BM-01..03 (EXP-03)، Q-06 |
| ADR-0009 | no-own-raw-log-store-federate-existing | Firm (فاز ۱–۵) | بازبینی در صورت شکست BM-04 یا retention منابع |
| ADR-0010 | buffering-without-kafka-initially | Provisional | BM-07، Q-06، Q-09 |
| ADR-0011 | topology-model-and-dependency-discovery | Provisional | — (کشف در فاز ۴) |
| ADR-0012 | correlation-engine-design | Firm | BM-08 برای کارایی |
| ADR-0013 | analytics-staging-rules-statistics-ml | Firm | — |
| ADR-0014 | frontend-react-echarts-cytoscape | Provisional | Q-01، Q-21، BM-10 |
| ADR-0015 | authentication-authorization-audit | **Blocked** | Q-04 |
| ADR-0016 | offline-deployment-and-artifacts | **Blocked** | Q-02، Q-03 |
| ADR-0017 | ha-dr-architecture | **Blocked** | Q-05 |
| ADR-0018 | data-retention-privacy-capacity-controls | Firm (حریم خصوصی) / Provisional (retention) | Q-16، Q-10 |
| ADR-0019 | transaction-correlation-tiers | Firm | — |
| ADR-0020 | existing-platform-integration-read-only-pull | Firm | — |

**خلاصه:** Accepted: 1 · Firm: 9 (به‌علاوه بخش حریم خصوصی ADR-0018) · Provisional: 5 · Blocked: 4.

## نکته برای بازبینی

- Q-09 در spine جزو پرسش‌های مسدودکننده §18 نیست، اما در این سند در دسته A آمده، زیرا T-01-01 «ساخت آفلاین با mirror داخلی» را لازم دارد.
- سند [system-context.md](system-context.md) به `../requirements/open-questions.md` پیوند داده که وجود ندارد؛ مقصد صحیح همین فایل (`docs/architecture/open-questions-and-assumptions.md`) است.
- شناسه‌های H-01..H-08 در spine تعریف نشده‌اند و فقط محلی این سند هستند.
