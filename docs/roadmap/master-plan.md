# برنامه جامع تحویل (Master Plan) — BSOIP

| فیلد | مقدار |
|---|---|
| Document ID | `RM-001` |
| Version | `0.1` |
| Status | `Draft – Phase 0 – awaiting approval` |
| Date | `2026-10-11` (۱۴۰۵/۰۷/۱۹) |
| Owner | `Architecture` |
| Related documents | [implementation-backlog.md](implementation-backlog.md)، [quality-and-validation-strategy.md](../testing/quality-and-validation-strategy.md)، [deployment-and-runbooks.md](../operations/deployment-and-runbooks.md)، [open-questions-and-assumptions.md](../architecture/open-questions-and-assumptions.md)، [ADR index](../architecture/architecture-decision-records/README.md)، [functional-requirements.md](../requirements/functional-requirements.md)، [non-functional-requirements.md](../requirements/non-functional-requirements.md)، [product-charter.md](../vision/product-charter.md) |

## ۱. هدف این سند

این سند توالی فازهای ۰ تا ۷ پلتفرم **Banking Service Observability & Intelligence Platform (BSOIP)**، وابستگی‌ها، نقاط عطف (M0..M6)، گیت‌های تصمیم و ریسک‌ها را تعریف می‌کند. جزئیات Epic و User Story در [implementation-backlog.md](implementation-backlog.md) و راهبرد آزمون در [quality-and-validation-strategy.md](../testing/quality-and-validation-strategy.md) آمده است.

قرارداد برچسب‌گذاری: **[واقعیت]** فقط F-01..F-03؛ **[فرض A-xx]**؛ **[فرضیه]**؛ **[توصیه]**؛ **[باز Q-xx]**. فهرست کامل در [open-questions-and-assumptions.md](../architecture/open-questions-and-assumptions.md).

## ۲. چرا توالی فازها بازنگری شد

منشور اولیه محصول (F-03) قابلیت‌ها را به ترتیب دامنه (کاتالوگ، تراکنش، LB، MQ، ...، یکپارچگی با پلتفرم‌های موجود) فهرست کرده بود. توالی تحویل به سه دلیل بازچینی شد:

1. **پلتفرم‌های موجود نخستین منبع داده‌اند.** [واقعیت] مالک محصول تصریح کرده که کار با SolarWinds، Zabbix، Splunk و Elasticsearch موجود آغاز شود و ابزارگذاری مستقیم برنامه‌ها به فازهای بعد و بر اساس نیاز واقعی موکول شود (F-02). بنابراین بدون یکپارچگی با این پلتفرم‌ها هیچ داده‌ای برای سلامت سرویس، تراکنش یا همبستگی وجود ندارد؛ یکپارچگی با پلتفرم‌های موجود از انتهای فهرست به **فاز ۲** منتقل شد و پیش‌نیاز فازهای ۳ تا ۵ است.
2. **همبستگی در یک فاز متمرکز شد (فاز ۴).** رتبه‌بندی علت محتمل به سه چیز وابسته است: مدل رخداد مشترک (فاز ۲)، توپولوژی با provenance (فاز ۱–۲) و شاخص‌های تراکنش در سطح hop (فاز ۳). اجرای پراکنده همبستگی در چند فاز به بازنویسی منجر می‌شد. فاز ۳ فقط نسخه سبک (`COR-003` lite) لازم برای VS-1 را دارد.
3. **فاز ۷ اختیاری است.** ابزارگذاری مستقیم (OTel SDK/agent)، همبستگی سطح L3 و RUM فقط در صورت اثبات نیاز (مثلاً ناکافی بودن L0/L2 در یک سرویس مشخص) و پس از استقرار تولیدی اجرا می‌شود.

[توصیه] سخت‌سازی امنیتی، آزمون خودکار و self-observability در **همه** فازها پیوسته است؛ فاز ۶ اعتبارسنجی در مقیاس است، نه نخستین جایی که امنیت دیده می‌شود.

## ۳. نمای کلی فازها و وابستگی‌ها

```mermaid
flowchart LR
  P0["Phase 0<br/>Discovery & Architecture"] -->|M0| P1["Phase 1<br/>Foundation & Catalog"]
  P1 -->|M1| P2["Phase 2<br/>Ingestion & Existing-Platform Connectors"]
  P1 -.parallel track.-> D["EP-22 Data Discovery<br/>EXP-01..EXP-05"]
  D --> P2
  D --> P3
  P2 -->|M2| P3["Phase 3<br/>Transactions & SLO<br/>VS-1"]
  P3 -->|M3 VS-1| P4["Phase 4<br/>Correlation & Extended Integrations"]
  P4 -->|M4| P5["Phase 5<br/>Anomaly & Prediction"]
  P4 --> P6
  P5 -->|M5| P6["Phase 6<br/>Enterprise Hardening"]
  P6 -->|M6 go-live| P7["Phase 7 (optional)<br/>Instrumentation, L3, RUM"]
```

*توضیح:* فاز ۶ از نظر فنی به خروجی فاز ۴ وابسته است و بخشی از کارهای آن (HA پایگاه‌داده، آزمون کارایی) می‌تواند هم‌زمان با فاز ۵ آغاز شود [توصیه]. فاز ۷ پس از M6 و مشروط است.

### ۳.۱ توالی نسبی (بدون تاریخ تقویمی)

مدت‌ها نامعلوم‌اند؛ نمودار زیر فقط ترتیب و هم‌پوشانی را نشان می‌دهد. واحدها «اسپرینت نسبی» هستند و تقویمی نیستند (محور افقی صرفاً شماره واحد نسبی است؛ طول نوارها بیانگر بازه تلاش جدول ۳.۲ نیست و فقط ترتیب و هم‌پوشانی را نشان می‌دهد).

```mermaid
gantt
  title Relative sequencing (no calendar dates)
  dateFormat X
  axisFormat %s
  section Phase 0
  Architecture package & review :p0, 0, 1
  section Phase 1
  Foundation, IAM, Catalog, Calendar, UI shell :p1, 1, 5
  Data discovery EXP-01..05 (parallel) :d1, 1, 6
  section Phase 2
  Ingestion, SDK, connectors, health v1 :p2, 5, 11
  section Phase 3
  Transactions, SLO, VS-1 :p3, 11, 16
  section Phase 4
  Correlation & extended integrations :p4, 16, 22
  section Phase 5
  Anomaly & forecasting :p5, 22, 27
  section Phase 6
  HA/DR, perf, pentest, handover :p6, 22, 30
  section Phase 7
  Optional instrumentation/RUM :p7, 30, 34
```

### ۳.۲ برآورد تلاش (فقط فرض)

[فرض A-03] با تیم ۴ تا ۸ مهندس و [فرض A-12] زیرساخت آفلاین آماده، بازه‌های تقریبی زیر صرفاً برای برنامه‌ریزی ظرفیت ارائه می‌شود و تعهد نیست. ابعاد واقعی پس از بستن Q-01، Q-06، Q-07 و Q-08 بازبرآورد می‌شود.

| فاز | بازه تلاش (نفر-ماه، فرض) | عامل اصلی عدم قطعیت |
|---|---|---|
| 1 | 8–16 | آماده بودن mirror داخلی (Q-09)، IdP (Q-04) |
| 2 | 14–28 | نسخه و دسترسی API منابع (Q-08)، تأییدهای امنیتی (R-02) |
| 3 | 10–20 | کیفیت لاگ‌ها (Q-07, R-01) |
| 4 | 14–26 | تعداد یکپارچگی‌های تکمیلی |
| 5 | 8–16 | وجود تاریخچه برچسب‌خورده رخدادها |
| 6 | 12–24 | معماری DR (Q-05)، دامنه pentest |
| 7 | نامعین | به نیاز اثبات‌شده بستگی دارد |

## ۴. نقاط عطف (Milestones)

| ID | تعریف | شاهد پذیرش | پایان فاز |
|---|---|---|---|
| M0 | بسته معماری فاز ۰ تأیید شد | صورت‌جلسه بازبینی؛ بسته شدن یا پذیرش پیش‌فرض Q-01, Q-02, Q-04, Q-07, Q-13 | 0 |
| M1 | پایه پلتفرم + کاتالوگ در UAT | نصب آفلاین bundle v1 در UAT؛ ورود با AD/OIDC؛ کاتالوگ VS-1 ثبت شده | 1 |
| M2 | منابع موجود ingest شده + health v1 | داده Zabbix و ES/Splunk برای سرویس VS-1 در داشبورد؛ freshness و UNKNOWN درست | 2 |
| M3 | دموی VS-1 پذیرفته شد | معیارهای بخش ۷ برقرار | 3 |
| M4 | همبستگی پذیرفته شد | ارزیابی رتبه‌بندی علت محتمل روی replay رخدادها، بازخورد اپراتور فعال | 4 |
| M5 | تشخیص ناهنجاری ارزیابی شد | گزارش precision/recall روی تاریخچه برچسب‌خورده؛ alert budget رعایت شد | 5 |
| M6 | تصمیم استقرار تولیدی | گزارش BM-09، pentest، drillهای upgrade/rollback، تحویل عملیات | 6 |

## ۵. شرح فازها

> هر فاز ساختار یکسان دارد: هدف کسب‌وکاری، دامنه و استثناها، قابلیت‌ها و اجزای فنی، تحویل‌دادنی‌ها، پیش‌نیازها، Epicها، معیار پذیرش، الزامات آزمون خودکار، ملاحظات امنیتی، ریسک‌های عملیاتی، DoD، معیار خروج.

### ۵.۰ فاز ۰ — Discovery & Architecture

- **هدف کسب‌وکاری:** توافق بر هویت محصول (سرویس‌محور و تراکنش‌محور، نه پایش زیرساخت)، معماری، توالی و پرسش‌های باز پیش از هرگونه پیاده‌سازی. [واقعیت] پیاده‌سازی تا تأیید صریح ممنوع است (F-02).
- **دامنه:** بسته مستندات (vision، requirements، architecture، ADR-0001..0020، roadmap، testing، operations).
- **استثناها:** هیچ کد، migration، فایل زیرساخت یا نصب نرم‌افزار؛ هیچ اتصال به منابع بانک.
- **تحویل‌دادنی‌ها:** همین بسته؛ فهرست Q-01..Q-22 گروه‌بندی‌شده برای جلسه بازبینی.
- **پیش‌نیازها:** منشور محصول (F-03).
- **Epicها:** — (فقط مستندات)
- **معیار پذیرش:** تأیید مالک محصول و معماری؛ هیچ تناقض بین اسناد در شناسه‌ها؛ پاسخ یا پذیرش پیش‌فرض برای پرسش‌های مسدودکننده دسته A (بخش ۹).
- **آزمون خودکار:** — (بررسی پیوندهای Markdown و lint مستندات در فاز ۱ خودکار می‌شود).
- **امنیت:** هیچ داده واقعی بانک در مستندات.
- **ریسک‌ها:** R-07 (گسترش دامنه)، R-13.
- **DoD:** همه اسناد با جدول metadata و برچسب‌گذاری؛ ADRها با وضعیت و Confidence.
- **معیار خروج:** M0؛ تصمیم ADR-0003 (Q-01) گرفته شده تا T-01-01 آغاز شود.

### ۵.۱ فاز ۱ — Platform Foundation & Service Catalog

- **هدف کسب‌وکاری:** ثبت سرویس‌های بانکی، مالکیت و وابستگی‌ها در یک منبع واحد، و ایجاد پایه امن و قابل نصب آفلاین که فازهای بعد روی آن ساخته شوند.
- **دامنه:**
  - مخزن، ساخت و CI آفلاین، استانداردها، ArchUnit (EP-01).
  - IAM با AD/OIDC، RBAC با scope، audit تغییرناپذیر (EP-02).
  - کاتالوگ: BusinessService، TechnicalService، Component، ServiceType قابل گسترش، Environment/Site، وابستگی دستی، import/export YAML/CSV، تاریخچه تغییر (EP-03).
  - تقویم کسب‌وکار جلالی، بارگذاری تعطیلات به‌صورت داده، ServiceWindow، MaintenanceWindow (EP-04).
  - پوسته UI فارسی RTL با تاریخ جلالی (`FR-UI-001`).
  - Offline install bundle v1 (EP-05).
  - مسیر موازی: کشف داده EXP-01..EXP-04 (EP-22).
- **استثناها:** هیچ ingestion تله‌متری؛ نمایش گراف توپولوژی تعاملی (`FR-CAT-008`) در فاز ۲؛ هیچ HA؛ ClickHouse فقط در صورت آماده‌سازی برای فاز ۲ نصب می‌شود.
- **قابلیت‌ها و اجزا:** `platform-core` (ماژول‌های `catalog`, `topology`, `calendar`, `iam`, `audit`, `query-api`)، `metadata-db` (PostgreSQL)، `web-ui`، `identity-provider`.
- **FR مرتبط:** FR-CAT-001..006, 011, 012؛ FR-CAL-001..003؛ FR-UI-001؛ FR-ADM-001..003.
- **تحویل‌دادنی‌ها:** artifact امضاشده `platform-core` و `web-ui`، SBOM، bundle v1، OpenAPI 3.1 کاتالوگ، مستند نصب.
- **پیش‌نیازها:** M0؛ تصمیم ADR-0003 [باز Q-01]؛ ADR-0015 [باز Q-04] (پیش‌فرض: Keycloak + AD federation)؛ mirror داخلی artifact [باز Q-09]؛ VM برای UAT [فرض A-02].
- **Epicها:** EP-01، EP-02، EP-03 (بخش دستی)، EP-04، EP-05 (v1)، EP-22.
- **معیار پذیرش:**
  - کاربر با حساب AD وارد می‌شود و فقط سرویس‌های scope خود را می‌بیند.
  - سرویس VS-1 با زنجیره کامل (بخش ۷) از طریق YAML import ثبت و در UI به فارسی نمایش داده می‌شود.
  - هر تغییر کاتالوگ در audit با کاربر، زمان (UTC ذخیره، نمایش Asia/Tehran) و diff ثبت می‌شود.
  - تقویم سال جاری با فایل تعطیلات import می‌شود؛ هیچ تعطیلی قمری در کد محاسبه نمی‌شود.
  - نصب bundle روی VMهای آماده در UAT بدون Internet انجام می‌شود.
- **آزمون خودکار:** unit (domain ≥ 80% خط، NFR-MNT-002)، ArchUnit مرزهای ماژول، integration با PostgreSQL (Testcontainers، image آفلاین)، contract OpenAPI، آزمون UI RTL/جلالی، SAST و اسکن وابستگی در CI.
- **امنیت:** deny-by-default (NFR-SEC-005)، TLS (NFR-SEC-001)، عدم نگهداری secret در پیکربندی (NFR-SEC-004)، امضا و SBOM (NFR-SEC-007)، audit (NFR-SEC-006).
- **ریسک‌ها:** R-04، R-09، R-02 (برای EP-22)، R-14، R-08.
- **DoD:** کد بازبینی‌شده، آزمون‌ها سبز در CI آفلاین، SBOM بدون آسیب‌پذیری Critical حل‌نشده، مستند به‌روز، ADRهای تحت تأثیر به‌روز.
- **معیار خروج و آمادگی برای فاز ۲:** M1؛ نتایج EXP-01 و EXP-02 حداقل برای یک منبع لاگ و Zabbix؛ حساب‌های read-only برای Zabbix و یک منبع لاگ در مسیر تأیید [فرض A-09]؛ Q-08 برای منابع VS-1 پاسخ داده شده.

### ۵.۲ فاز ۲ — Ingestion Core & Existing-Platform Connectors

- **هدف کسب‌وکاری:** نمایش سلامت سرویس‌های ثبت‌شده با استفاده از داده‌ای که بانک *همین حالا* در Zabbix، SolarWinds، Splunk و Elasticsearch دارد، بدون هیچ ابزارگذاری جدید.
- **دامنه:**
  - Connector SDK و `connector-runtime` (per zone، فقط اتصال outbound) (EP-07).
  - `edge-collector` (توزیع OTel Collector ساخته‌شده با OCB به‌صورت آفلاین، صف دیسکی، redaction processor) و ماژول `ingestion`: normalize، UTC، redact، dedup، late data، backpressure (EP-06).
  - کانکتورهای Zabbix، Elasticsearch/OpenSearch، Splunk (Must) و SolarWinds (Should) (EP-08).
  - IBM MQ، RabbitMQ و LB (F5/HAProxy/NGINX) — از مسیر Zabbix/SolarWinds یا API مستقیم read-only، هر کدام در دسترس باشد (EP-09).
  - مدل سلامت v1 با `HealthState` و توضیح، UNKNOWN برای داده کهنه، propagation به‌عنوان impact (EP-10).
  - قواعد آستانه ایستا (`FR-ANO-001`)، مدل رخداد مشترک (`FR-COR-001`، EP-13 بخش مدل).
  - داشبورد سرویس و نقشه توپولوژی؛ ExternalRef؛ deep link به منابع.
  - self-observability پایه (EP-21) و نخستین benchmarkها BM-01, BM-05, BM-07 (EP-19).
- **استثناها:** همبستگی و رتبه‌بندی علت؛ ذخیره لاگ خام (ADR-0009)؛ Kafka (ADR-0010)؛ SNMP/syslog (فاز ۴)؛ تغییر پیکربندی دستگاه‌های موجود (NFR-IMP-002).
- **اجزا:** `connector-runtime`، `edge-collector`، `telemetry-store` (ClickHouse)، ماژول‌های `ingestion`, `connectors-control`, `health`, `events`.
- **FR مرتبط:** FR-ING-001..003, 005..011؛ FR-INT-001..004, 006؛ FR-LB-001, 002, 004؛ FR-MQ-001..003, 005؛ FR-RUN-004؛ FR-HLT-001..003؛ FR-CAT-007 (آغاز)، 008، 009؛ FR-TXN-010؛ FR-COR-001؛ FR-ANO-001؛ FR-UI-002, 003؛ FR-ADM-004, 006.
- **تحویل‌دادنی‌ها:** Connector SDK v1 با مستند و harness شبیه‌سازی؛ کانکتورهای بالا با capability declaration؛ bundle v2 شامل collector و ClickHouse؛ گزارش BM-01/05/07 (اولیه).
- **پیش‌نیازها:** M1؛ حساب‌های read-only و قوانین فایروال zone → monitoring-zone (R-02)؛ query budget توافق‌شده با مدیران Splunk/ES [باز Q-22]؛ نسخه‌ها [باز Q-08]؛ ADR-0008 تا پایان فاز با BM-01 تأیید یا بازنگری شود.
- **Epicها:** EP-03 (توپولوژی و ExternalRef)، EP-05، EP-06، EP-07، EP-08، EP-09، EP-10، EP-13 (مدل)، EP-19، EP-21، EP-22.
- **معیار پذیرش:**
  - داده سرویس VS-1 از حداقل Zabbix و یکی از ES/Splunk در داشبورد دیده می‌شود؛ تأخیر pull ≤ poll interval + 60 s در P95 (NFR-PRF-002).
  - قطع یک منبع ← اجزای وابسته `UNKNOWN` با دلیل «داده کهنه/منبع در دسترس نیست» و نه `CRITICAL` (NFR-REL-004).
  - قطع ۱ ساعته ارتباط collector با core بدون از دست رفتن داده؛ پس از اتصال مجدد، داده با `observed_at` درست ثبت و تکراری حذف می‌شود.
  - redaction روی corpus مصنوعی PAN/کد ملی/شبا: صفر نشت.
  - هر کانکتور بار خود را در قالب rate limit پیکربندی‌شده نگه می‌دارد و مصرف واقعی را گزارش می‌کند (NFR-IMP-001).
- **آزمون خودکار:** contract tests کانکتور با fixtureهای ضبط‌شده برای هر نسخه API؛ شبیه‌ساز منابع؛ integration با ClickHouse؛ failure injection (source down، slow source، disk full collector)؛ load test BM-07.
- **امنیت:** نگهداری credentialهای منابع فقط از طریق SecretProvider (NFR-SEC-004, FR-ADM-004)؛ mTLS بین collector/connector و core؛ هیچ اتصال از core به zoneها (NFR-SEC-010)؛ redaction دولایه (NFR-PRV-002).
- **ریسک‌ها:** R-02، R-03، R-06، R-10، R-12، R-15، R-11.
- **DoD:** مانند فاز ۱ + هر کانکتور: fixture ضبط‌شده، صفحه diagnostics، سند capability «نیازمند تأیید در نسخه نصب‌شده بانک»، runbook خرابی.
- **معیار خروج:** M2؛ نتیجه EXP-01 برای سرویس VS-1 مشخص کرده که کدام سطح L0/L2 قابل دستیابی است؛ OutcomeMapping اولیه با تیم عملیات بازبینی شده.

### ۵.۳ فاز ۳ — Transaction Observability & SLOs (VS-1)

- **هدف کسب‌وکاری:** پاسخ به پرسش «آیا تراکنش‌های این سرویس از نظر کسب‌وکاری موفق‌اند؟» و تفکیک موفقیت فنی از موفقیت کسب‌وکاری، با آگاهی از تقویم بانکی.
- **دامنه:** TransactionType، OutcomeMapping (داده، نه کد)، خلاصه‌های دقیقه‌ای از لاگ (L0) و L2 در جایی که کلید وجود دارد، throughput/success/P50/P95/P99 در صورت وجود latency، مقایسه upstream/downstream، طبقه‌بندی backlog صف، SLO تقویم‌محور و error budget، تشخیص expected-inactive، پنل evidence ساده، سرکوب اعلان در maintenance (`FR-NTF-002`)، grouping سبک توپولوژی (`FR-COR-003` lite)، baseline پایه time-of-week.
- **استثناها:** رتبه‌بندی علت کامل؛ ML؛ L3 trace؛ کانال‌های اعلان (فاز ۴).
- **اجزا:** ماژول‌های `transactions`, `slo`, `calendar` (ارزیابی), `health`, `correlation` (lite)، Splunk/ES saved-search-style extraction در کانکتورها.
- **FR مرتبط:** FR-TXN-001..008, 011؛ FR-MQ-004؛ FR-CAL-004..007؛ FR-CAT-010؛ FR-RUN-006؛ FR-DX-004؛ FR-ANO-002 (basic)، FR-ANO-004 (یک قاعده)؛ FR-COR-003 (lite)؛ FR-NTF-002؛ FR-UI-004.
- **تحویل‌دادنی‌ها:** VS-1 کامل (بخش ۷)؛ گزارش BM-03 و BM-04؛ کتابچه OutcomeMapping سرویس VS-1.
- **پیش‌نیازها:** M2؛ [باز Q-07] و [باز Q-13] بسته؛ [باز Q-15] برای داده تقویم؛ ≥ ۳ رخداد تاریخی توافق‌شده با عملیات و دسترسی به داده تاریخی آن بازه در منابع.
- **Epicها:** EP-11، EP-12، EP-10 (توسعه)، EP-13 (lite)، EP-19، EP-21.
- **معیار پذیرش:** معیارهای VS-1 (بخش ۷.۲).
- **آزمون خودکار:** تست سناریوی end-to-end با replay رخداد؛ آزمون‌های property-based برای OutcomeMapping و تقویم (مرز روز، DST-free بودن Asia/Tehran از طریق tzdata، سال کبیسه جلالی)؛ load BM-03.
- **امنیت:** HMAC tokenization کلیدها (NFR-PRV-003)؛ ممیزی redaction روی خروجی واقعی VS-1 در UAT؛ عدم ذخیره مبلغ دقیق.
- **ریسک‌ها:** R-01 (اصلی)، R-05، R-11، R-14، R-03.
- **DoD:** هر شاخص تراکنش tier خود را ثبت و UI آن را نشان می‌دهد؛ هر وضعیت محاسبه‌شده توضیح دارد (NFR-USA-002).
- **معیار خروج:** M3 (پذیرش دموی VS-1)؛ تصمیم صریح go/no-go برای فاز ۴ بر اساس ارزش اثبات‌شده.

### ۵.۴ فاز ۴ — Correlation & Extended Integrations

- **هدف کسب‌وکاری:** کاهش زمان تشخیص هنگام حادثه با گروه‌بندی سیگنال‌ها و رتبه‌بندی قابل توضیح علت‌های محتمل؛ گسترش پوشش به runtime، DB، K8s، WAF و آزمون‌های synthetic.
- **دامنه:** مدل رخداد کامل، dedup و پنجره زمانی، grouping توپولوژیک، رتبه‌بندی CauseHypothesis با evidence/confidence/counter-evidence و disclaimer «همبستگی ≠ علیت»، Change awareness، OperatorFeedback، timeline حادثه، federated evidence query با redaction، webhook ورودی، اعلان و ITSM، JMX، SQL Server، K8s، WAF، synthetic API checks با guardrail، کشف وابستگی (`FR-CAT-007`) با تأیید، SNMP/syslog، قواعد failure-domain LB.
- **استثناها:** ML؛ remediation خودکار؛ SIEM.
- **FR مرتبط:** FR-COR-002..007؛ FR-INT-005, 007؛ FR-NTF-001؛ FR-RUN-001, 003, 005؛ FR-LB-003, 005..007؛ FR-DX-001..003؛ FR-ING-004؛ FR-CAT-007.
- **Epicها:** EP-13، EP-14، EP-15، EP-17، EP-03 (کشف)، EP-19 (BM-08, BM-10)، EP-21.
- **پیش‌نیازها:** M3؛ [باز Q-14] کانال اعلان و ITSM؛ [باز Q-20] حساب‌های آزمون synthetic؛ [باز Q-17] وضعیت NTP (برای پنجره‌های همبستگی).
- **معیار پذیرش:** روی مجموعه replay (≥ ۱۰ رخداد برچسب‌خورده، [توصیه]) علت واقعی در top-3 در ≥ 70% موارد [فرضیه، هدف پیشنهادی برای توافق با عملیات]؛ چرخه ارزیابی ≤ 60 s (NFR-PRF-004)؛ هر hypothesis توضیح و counter-evidence دارد؛ synthetic هرگز تراکنش مالی ایجاد نمی‌کند.
- **آزمون خودکار:** golden tests همبستگی با سناریوهای ثابت؛ clock-skew injection؛ BM-08؛ آزمون guardrail synthetic؛ contract webhook.
- **امنیت:** نتایج federated query پیش از UI redact می‌شوند (NFR-PRV-005)؛ webhook با احراز هویت و محدودیت نرخ؛ حساب synthetic با کمترین دسترسی.
- **ریسک‌ها:** R-05، R-11، R-15، R-12، R-08، R-03.
- **DoD:** هر قاعده همبستگی golden test و مستند توضیح دارد.
- **معیار خروج:** M4.

### ۵.۵ فاز ۵ — Anomaly Detection & Prediction

- **هدف کسب‌وکاری:** تشخیص زودتر انحراف‌ها با baselineهای فصلی و پیش‌بینی (time-to-full صف، SLO burn) با نرخ هشدار کاذب قابل‌قبول.
- **دامنه:** طبق ADR-0013: baseline فصلی → robust z/MAD، EWMA، STL residual → الگوهای چندمتغیره وابستگی‌محور → پیش‌بینی → ML فقط با گیت ارزیابی. harness ارزیابی، drift، توضیح برای هر ناهنجاری.
- **استثناها:** مدل‌های black-box بدون توضیح؛ هر detector بدون اندازه‌گیری precision/recall.
- **FR مرتبط:** FR-ANO-002..008؛ FR-CAL-007؛ FR-TXN-011 (پیشرفته)؛ FR-MQ-006.
- **Epicها:** EP-16، EP-19.
- **پیش‌نیازها:** M4؛ تاریخچه ≥ چند هفته داده در ClickHouse شامل حداقل یک چرخه ماهانه [فرض]؛ برچسب‌گذاری رخدادها از OperatorFeedback فاز ۴.
- **معیار پذیرش:** precision/recall اندازه‌گیری‌شده روی تاریخچه برچسب‌خورده و توافق‌شده با عملیات؛ alert budget به‌ازای هر سرویس رعایت شود؛ هیچ detector بدون گزارش ارزیابی فعال نشود.
- **آزمون خودکار:** harness ارزیابی در CI روی dataset برچسب‌خورده مصنوعی + تاریخی؛ آزمون regression برای جلوگیری از کاهش precision.
- **امنیت:** داده آموزشی فقط از داده مشتق‌شده و tokenized.
- **ریسک‌ها:** R-05 (اصلی)، R-01.
- **DoD:** هر detector: کارت مدل (ورودی، فرض، معیار، محدودیت)، توضیح در UI.
- **معیار خروج:** M5.

### ۵.۶ فاز ۶ — Enterprise Hardening & Production Readiness

- **هدف کسب‌وکاری:** آمادگی استقرار تولیدی مطابق الزامات دسترس‌پذیری، امنیت و عملیات بانک.
- **دامنه:** HA درون سایت و DR (ADR-0017)، Patroni برای PostgreSQL، ClickHouse ReplicatedMergeTree + Keeper (۳ گره) برای Medium+، اعتبارسنجی کارایی در سناریوی توافق‌شده (NFR-SCL-001)، pentest و ارزیابی امنیتی، drill upgrade/rollback، drill DR (BM-09)، runbookها، تحویل عملیات.
- **استثناها:** multi-tenancy (`FR-ADM-005` فقط در صورت پاسخ مثبت Q-11).
- **Epicها:** EP-05 (نهایی)، EP-18، EP-19، EP-20، EP-21.
- **پیش‌نیازها:** [باز Q-05] RPO/RTO و توپولوژی DR؛ [باز Q-18] بودجه سخت‌افزار؛ [باز Q-19] اپراتور پلتفرم؛ [باز Q-10] الزامات انطباق.
- **معیار پذیرش:** RPO ≤ 15 min برای metadata و RTO ≤ 4 h در drill (NFR-AVL-003، موقت)؛ سناریوی Medium روی سخت‌افزار مرجع (NFR-SCL-001)؛ صفر یافته Critical/High باز در pentest؛ rollback در پنجره نگهداری (NFR-DEP-003)؛ نصب روی VMهای آماده ≤ 1 روز (NFR-DEP-002).
- **آزمون خودکار:** load و soak، chaos/failure injection (DB failover)، DAST، drill خودکار backup/restore.
- **ریسک‌ها:** R-06، R-09، R-13، R-15.
- **معیار خروج:** M6 — تصمیم go-live توسط کمیته تغییر بانک.

### ۵.۷ فاز ۷ (اختیاری) — Direct Instrumentation, L3, RUM

- **هدف:** فقط برای سرویس‌هایی که در فازهای ۳–۴ نشان داده شده L0/L2 برای تشخیص کافی نیست.
- **دامنه:** OTel SDK/agent برای برنامه‌های منتخب، دریافت trace (`FR-TXN-009`)، همبستگی L3، `.NET` runtime (`FR-RUN-002`)، RUM برای وب/PWA/Android (`FR-DX-005`).
- **پیش‌نیازها:** M6؛ تحلیل هزینه/فایده به‌ازای هر برنامه؛ تأیید تیم‌های توسعه برنامه؛ ارزیابی ریسک داده در RUM.
- **معیار ورود (گیت):** مورد کسب‌وکاری مستند که نشان دهد کدام پرسش عملیاتی با داده موجود پاسخ‌پذیر نیست.

## ۶. ساختار قابلیت در فازها (خلاصه)

| حوزه | P1 | P2 | P3 | P4 | P5 | P6 | P7 |
|---|---|---|---|---|---|---|---|
| Catalog/Topology | دستی، import | ExternalRef، نقشه | change records | کشف با تأیید | | | |
| Ingestion | | SDK، collector، کانکتورها | استخراج تراکنش | SNMP/syslog، webhook | | Kafka اگر trigger | OTLP traces |
| Health/SLO | | Health v1 | SLO، error budget | | | | |
| Correlation | | مدل رخداد | lite | کامل | | | L3 |
| Analytics | | آستانه ایستا | baseline پایه، ۱ قاعده | | baseline، forecast | | |
| Security/Ops | IAM، audit، bundle v1 | secrets، mTLS، self-obs | | | | HA/DR، pentest | |

## ۷. Vertical Slice VS-1

### ۷.۱ تعریف

- **سرویس پیش‌فرض:** کارت به کارت از طریق Mobile Banking [باز Q-13].
- **زنجیره مدل‌شده:** Mobile app → (WAF) → Load Balancer (F5 یا HAProxy) → API gateway/mobile backend → card-transfer service → ESB → IBM MQ → card switch / اتصال شتاب (ExternalParty)، به‌علاوه core DB.

```mermaid
flowchart LR
  APP["Mobile App"] --> WAF["WAF (optional)"] --> LB["Load Balancer<br/>F5 / HAProxy"] --> GW["API Gateway /<br/>Mobile Backend"] --> CT["Card-Transfer Service"] --> ESB["ESB"] --> MQ["IBM MQ"] --> SW["Card Switch /<br/>Shetab (ExternalParty)"]
  CT --> DB[("Core DB")]
  subgraph Sources["Evidence sources (no new instrumentation)"]
    ZBX["Zabbix"]
    LOGS["Elasticsearch / Splunk"]
    MQRO["IBM MQ read-only"]
    LBS["LB stats"]
  end
```

- **داده بدون ابزارگذاری جدید:** Zabbix (میزبان‌ها و itemهای LB/MQ در صورت وجود)؛ ES یا Splunk (شمارش دقیقه‌ای بر اساس outcome و latency در صورت لاگ شدن) [فرض A-05]؛ IBM MQ read-only یا از طریق Zabbix (depth، نرخ‌ها، سن قدیمی‌ترین پیام، وضعیت channel)؛ آمار LB از Zabbix/SolarWinds/HAProxy stats/F5 iControl (هر کدام موجود باشد) — همه «نیازمند تأیید در نسخه نصب‌شده بانک» [باز Q-08].

### ۷.۲ آنچه VS-1 نشان می‌دهد

1. کاتالوگ و توپولوژی با provenance.
2. سلامت سرویس با توضیح.
3. نرخ موفقیت فنی در برابر موفقیت کسب‌وکاری (`BUSINESS_DECLINE` جدا از `TECHNICAL_FAILURE`).
4. قاعده «افت نرخ موفقیت در حالی که ترافیک ورودی پایدار است» با baseline time-of-week.
5. طبقه‌بندی backlog صف MQ (گذرا/پایدار).
6. مدیریت `EXPECTED_INACTIVE` با تقویم.
7. پنل evidence با سیگنال‌های همبسته در hopها با confidence و disclaimer «همبستگی ≠ علیت».
8. deep link به Zabbix/Splunk/ES.

### ۷.۳ معیارهای موفقیت (پیش‌نویس)

| # | معیار | روش سنجش |
|---|---|---|
| S1 | replay حداقل ۳ رخداد تاریخی توافق‌شده با عملیات؛ سرویس متأثر نشان داده شود و hop خراب واقعی در top-3 evidence برای حداقل ۲ از ۳ رخداد باشد | آزمون سناریو E2E (quality strategy §۱۱) |
| S2 | وضعیت سلامت با قضاوت اپراتور در ≥ 90% پنجره‌های ۵ دقیقه‌ای نمونه‌گیری‌شده مطابق باشد | نمونه‌گیری کور توسط اپراتور |
| S3 | صفر داده حساس در ممیزی redaction | اسکن ClickHouse/PostgreSQL/لاگ‌های پلتفرم با detectorها |
| S4 | اثر روی منابع در محدوده query budget توافق‌شده | متریک‌های مصرف کانکتور + تأیید مدیر منبع |

اگر EXP-01 نشان دهد لاگ‌ها کد outcome ندارند (R-01)، S1 و S2 با L0 و شاخص‌های فنی سنجیده می‌شوند و نمایش موفقیت کسب‌وکاری به‌عنوان «ناموجود – داده کافی نیست» علامت می‌خورد؛ این تصمیم در جلسه گیت فاز ۳ ثبت می‌شود.

## ۸. خلاصه ثبت ریسک

احتمال/اثر: L=کم، M=متوسط، H=زیاد (ارزیابی اولیه، [فرض]).

| ID | ریسک | احتمال | اثر | کاهش | نقش مالک |
|---|---|---|---|---|---|
| R-01 | لاگ‌ها فاقد کد outcome/latency/کلید | M | H | EXP-01 زودهنگام؛ طراحی L0 کامل؛ OutcomeMapping داده‌محور | Product Owner + Data Engineer |
| R-02 | تأخیر تأیید حساب‌های read-only | H | H | آغاز درخواست در فاز ۱ (EP-22)؛ بسته درخواست استاندارد با مجوزهای حداقلی | Delivery Lead |
| R-03 | بار/لایسنس اضافی روی Splunk/ES | M | H | query budget، rate limit، تجمیع دقیقه‌ای، BM-04 با مدیران | Integration Lead |
| R-04 | عدم تطابق مهارت تیم با stack | M | H | Q-01 پیش از T-01-01؛ ADR-0003 Blocked | Engineering Manager |
| R-05 | هشدار کاذب و از بین رفتن اعتماد | H | H | آستانه محافظه‌کارانه، alert budget، feedback، گیت ارزیابی | Analytics Lead |
| R-06 | پلتفرم مخزن credentialهای متعدد | M | H | SecretProvider، read-only، تفکیک per-zone، audit، pentest | Security Architect |
| R-07 | گسترش دامنه به همه حوزه‌های بانکی | H | M | VS-1 محور، گیت فاز، non-goals | Product Owner |
| R-08 | کهنه شدن توپولوژی دستی | H | M | lastSeen/confidence، کشف با تأیید (فاز ۴)، گزارش drift | Service Owner role |
| R-09 | مدیریت وابستگی آفلاین و زنجیره تأمین | M | H | mirror داخلی، SBOM، امضا، checksum | Platform Engineer |
| R-10 | نشت داده حساس از لاگ/query | M | H | redaction دولایه، corpus آزمون، ممیزی دوره‌ای | Security Architect |
| R-11 | clock skew و شکست همبستگی | M | M | EXP-05، تخمین skew، پنجره تحمل | Platform Engineer |
| R-12 | تفاوت نسخه/API فروشندگان | H | M | capability per installation، fixture per version | Integration Lead |
| R-13 | الزامات مقرراتی نامعلوم | M | H | Q-10؛ placeholder انطباق NFR-CMP-001 | Compliance liaison |
| R-14 | بار نگهداری داده تقویم | M | M | import سالانه، مالک داده تعیین‌شده، هشدار پیش از پایان سال | Operations |
| R-15 | وابستگی به در دسترس بودن پلتفرم‌های موجود هنگام حادثه | M | H | UNKNOWN صریح، داده مشتق ذخیره‌شده محلی، دامنه خرابی مستقل (NFR-AVL-002) | Architect |

## ۹. گیت‌های تصمیم

| گیت | پیش از | پرسش‌هایی که باید بسته شوند (یا پیش‌فرض پذیرفته شود) |
|---|---|---|
| G0 | آغاز فاز ۱ (T-01-01) | Q-01 (ADR-0003)، Q-02، Q-04 (ADR-0015)، Q-07، Q-13، Q-09 |
| G1 | آغاز فاز ۲ | Q-08 (منابع VS-1)، Q-22، Q-03 (ADR-0016)، Q-21، تأیید EXP-02 |
| G2 | آغاز فاز ۳ | Q-15، Q-16، Q-17 (نتیجه EXP-05)، Q-06 (حداقل برآورد Medium/High) |
| G3 | آغاز فاز ۴ | Q-14، Q-20، پذیرش M3 |
| G4 | آغاز فاز ۶ | Q-05 (ADR-0017)، Q-10، Q-18، Q-19، Q-11 |
| G5 | آغاز فاز ۷ | مورد کسب‌وکاری مستند به‌ازای هر برنامه |

جزئیات هر پرسش، پیش‌فرض و پاسخ‌دهنده در [open-questions-and-assumptions.md](../architecture/open-questions-and-assumptions.md).

## ۱۰. نخستین کار پس از تأیید

جلسه بازبینی تصمیم برای بستن Q-01، Q-02، Q-04، Q-07، Q-13؛ سپس **T-01-01**: ایجاد اسکلت مخزن `platform-core` (modular monolith، Gradle multi-module یا Maven، مرزهای ماژول + آزمون ArchUnit، ساخت آفلاین با mirror داخلی، CI شامل lint/test/SBOM) **بدون هیچ قابلیت کسب‌وکاری** — فقط پس از رفع انسداد ADR-0003. به‌طور موازی EP-22 و EXP-01.

## نکته برای بازبینی

- هدف «top-3 در ≥ 70%» برای M4 در spine تعریف نشده و صرفاً پیشنهاد این سند است؛ باید با عملیات توافق شود.
- گیت G0 علاوه بر پرسش‌های مسدودکننده spine (§18)، Q-09 را هم شامل کرده است، زیرا ساخت آفلاین T-01-01 بدون mirror داخلی ممکن نیست؛ در صورت نبود mirror، پیش‌فرض «ساخت mirror موقت داخل تیم» پذیرفته می‌شود.
