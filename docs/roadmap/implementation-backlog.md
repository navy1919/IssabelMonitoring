# Backlog پیاده‌سازی — BSOIP

| فیلد | مقدار |
|---|---|
| Document ID | `RM-002` |
| Version | `0.2` |
| Status | `Draft – Phase 0 – awaiting approval` |
| Date | `2026-10-12` (۱۴۰۵/۰۷/۲۰) |
| Owner | `Architecture` |
| Related documents | [master-plan.md](master-plan.md)، [quality-and-validation-strategy.md](../testing/quality-and-validation-strategy.md)، [deployment-and-runbooks.md](../operations/deployment-and-runbooks.md)، [open-questions-and-assumptions.md](../architecture/open-questions-and-assumptions.md)، [ADR index](../architecture/architecture-decision-records/README.md)، [functional-requirements.md](../requirements/functional-requirements.md)، [non-functional-requirements.md](../requirements/non-functional-requirements.md)، [product-charter.md](../vision/product-charter.md)، [product-benchmark.md](../benchmark/product-benchmark.md)، [build-buy-reuse-analysis.md](../benchmark/build-buy-reuse-analysis.md) |
| Change log | `0.1` (2026-10-11) پیش‌نویس اولیه؛ `0.2` (2026-10-12) **تغییر پس از Product Benchmark**: EP-23 Evidence PoC (فاز 0.5) و EP-24 (فاز ۷)؛ حذف/کوتاه‌سازی Storyهای ClickHouse و `edge-collector` در P1–P2؛ Storyهای FR-CAL-008 و FR-INT-008؛ نخستین کار پس از تأیید = PoC (T-23-01)، نخستین Task ساخت پس از G-PoC = T-01-01 |

## ۱. راهنما

- Epicها EP-01..EP-24 مطابق [master-plan.md](master-plan.md) (EP-23 و EP-24 در نسخه 0.2 افزوده شدند). شناسه Story: `US-<EP#>-NN`؛ شناسه Task: `T-<EP#>-NN`.
- اولویت MoSCoW (Must/Should/Could) و فاز مطابق شناسه‌های FR.
- Epicهای فاز ۱ و ۲ با جزئیات کافی برای شروع کار نوشته شده‌اند؛ فازهای ۳ به بعد در سطح feature و چند Story کلیدی، و در ابتدای هر فاز ریزتر می‌شوند.
- **هیچ Task پیش از M0 آغاز نمی‌شود** [واقعیت] (F-02). **نخستین کار پس از تأیید Evidence PoC است (EP-23، با T-23-01)**؛ کد PoC دورریختنی است. **نخستین Task ساخت T-01-01** است و فقط پس از G-PoC و رفع انسداد ADR-0003 [باز Q-01].
- نقش‌ها در Storyها: اپراتور NOC، مهندس SRE/پشتیبانی برنامه، مالک سرویس، تیم middleware/integration، SOC، مدیریت IT، مدیر پلتفرم.

### ۱.۱ Definition of Ready (DoR) برای هر Story

معیار پذیرش قابل آزمون دارد؛ FR/NFR مرتبط مشخص است؛ وابستگی به Q باز ندارد یا پیش‌فرض آن پذیرفته شده؛ داده آزمون مصنوعی موجود یا قابل تولید است.

### ۱.۲ Definition of Done (DoD) مشترک

بازبینی کد؛ آزمون unit/integration/contract مرتبط سبز در CI آفلاین؛ ArchUnit سبز؛ SBOM و اسکن وابستگی بدون Critical حل‌نشده؛ برچسب‌های UI فارسی؛ OpenAPI/JSON Schema به‌روز؛ رویداد audit برای عملیات تغییر‌دهنده؛ مستند کاربر/عملیات به‌روز.

## ۲. نمای کلی Epicها

| Epic | عنوان | فاز | اولویت | وابستگی |
|---|---|---|---|---|
| EP-23 | Evidence PoC | P0.5 | Must | M0 (G0) |
| EP-01 | Engineering foundation | P1 | Must | G-PoC، ADR-0003 |
| EP-02 | Identity, access & audit | P1 | Must | EP-01، ADR-0015 |
| EP-03 | Service catalog & topology | P1–P2 | Must | EP-01، EP-02 |
| EP-04 | Business calendar & maintenance windows | P1 (+ CAL-008 در P2) | Must | EP-01 |
| EP-05 | Offline packaging & deployment | P1→P6 | Must | EP-01، ADR-0016 |
| EP-06 | Ingestion pipeline & canonical schemas | P2 (OTLP/collector در P4) | Must | EP-03، ADR-0006، ADR-0008 |
| EP-07 | Connector SDK & connector-runtime | P2 | Must | EP-06 |
| EP-08 | Existing-platform connectors | P2 | Must | EP-07، EP-22 |
| EP-09 | Middleware & LB connectors | P2 | Must/Should | EP-07، EP-22 |
| EP-10 | Health model & dashboards | P2 | Must | EP-03، EP-06 |
| EP-11 | Transaction observability | P3 | Must | EP-08، EXP-01 |
| EP-12 | SLO engine & error budgets | P3 | Must | EP-04، EP-11 |
| EP-13 | Event model & correlation engine | P2→P4 | Must | EP-06، EP-03 |
| EP-14 | Runtime/DB/K8s/WAF connectors (+ OTLP/`edge-collector` اختیاری) | P4 | Should | EP-07 |
| EP-15 | Synthetic monitoring | P4 | Should | EP-07، Q-20 |
| EP-16 | Anomaly detection & forecasting | P5 | Must/Should | EP-11، EP-13 |
| EP-17 | Notifications via existing channels & ITSM | P4 | Should | EP-13، Q-14 |
| EP-18 | HA/DR & resilience | P6 | Must | Q-05 |
| EP-19 | Performance & capacity validation | P2→P6 | Must | EP-06 |
| EP-20 | Security hardening & assessment | continuous→P6 | Must | — |
| EP-21 | Platform self-observability | P2→P6 | Must | EP-06 |
| EP-22 | Data discovery & source onboarding | P0.5→P2 | Must | M0 |
| EP-24 | Direct instrumentation, L3 & RUM (optional) | P7 | Could | M6، G5 |

---

## ۲.۵ Epic فاز 0.5 (جزئیات کامل)

### EP-23 — Evidence PoC

- **هدف:** آزمودن فرضیه‌های کلیدی راهبرد reuse-first ([ADR-0021](../architecture/architecture-decision-records/ADR-0021-build-buy-reuse-strategy.md)) با دادهٔ واقعی و فقط‌خواندنی پیش از هر ساخت؛ خروجی ماندگار فقط گزارش شواهد و تصمیم‌های ADR است.
- **فاز:** P0.5 · **اولویت:** Must
- **قیود:** فقط‌خواندنی؛ time-box (پیشنهاد ۴–۶ هفته [فرض])؛ کد دورریختنی (برچسب `poc-throwaway`، هیچ استفادهٔ مجدد بدون بازنویسی)؛ آفلاین؛ صفر نوشتن در تولید؛ PoC-1 روی نمونهٔ غیرتولیدی Zabbix؛ الزامات PR-01..PR-08 در [open-questions-and-assumptions.md](../architecture/open-questions-and-assumptions.md) §۶.۲.
- **FR/NFR/ADR:** فرضیه‌های FR-TXN-003/005، FR-MQ-002/004، FR-COR-004 (پنل شواهد)، FR-INT-008، FR-CAL-008؛ NFR-IMP-001/002، NFR-PRV-002؛ ADR-0008، ADR-0012، ADR-0021، ADR-0023.

**User Stories**

- **US-23-01** — به‌عنوان معمار می‌خواهم پروتکل PoC (دامنه، time-box، داده، دسترسی‌ها، budget منبع، معیارهای پذیرش) پیش از شروع تأیید شود تا PoC محدود و قابل ممیزی بماند.
  - AC: پروتکل امضاشده توسط مالک محصول، امنیت و مالکان منابع؛ حساب‌های موقت read-only با تاریخ انقضا؛ نمونهٔ غیرتولیدی Zabbix تأمین شده.
- **US-23-02 (PoC-1)** — به‌عنوان مهندس SRE می‌خواهم بدانم Zabbix services/SLA می‌تواند برش VS-1 و پنجره‌های expected-inactive را نمایش دهد تا درباره reuse یا ساخت سلامت/SLA زیرساختی تصمیم بگیریم (ADR-0023).
  - AC: روی Zabbix غیرتولیدی، درخت سرویس برش VS-1 با tagها و SLA با schedule/excluded downtime ساخته و `service.get`/`sla.getsli` با حساب فقط‌خواندنی خوانده می‌شود؛ گزارش آنچه قابل نمایش است و آنچه نیست (مثلاً تعطیلات جلالی، DayType).
- **US-23-03 (PoC-2)** — به‌عنوان مالک سرویس می‌خواهم ببینم جست‌وجوی زمان‌بندی‌شده در Splunk/ES شمارش دقیقه‌ای outcome (فنی/کسب‌وکاری) و صدک latency را می‌دهد (توسعهٔ EXP-01).
  - AC: تعریف جست‌وجو توسط تیم پلتفرم تولید و توسط مدیر منبع نصب یا اجرا می‌شود [باز Q-28]؛ نتیجه فقط تجمیعی؛ مصرف budget اندازه‌گیری و با Q-22 مقایسه می‌شود.
- **US-23-04 (PoC-3)** — به‌عنوان مهندس middleware می‌خواهم معیارهای MQ را با `mq_prometheus` یا itemهای موجود Zabbix و حداقل مجوز بخوانم و بدانم کدام معیار بدون تغییر پیکربندی در دسترس است.
  - AC: فهرست معیارهای در دسترس/ناموجود؛ مجوزهای حداقل؛ هیچ تغییر پیکربندی queue manager؛ تصمیم پیشنهادی برای Q-27.
- **US-23-05 (PoC-4)** — به‌عنوان اپراتور NOC می‌خواهم روی ۳ حادثهٔ تاریخی (یا مصنوعی برچسب‌دار) ببینم آیا رتبه‌بندی شواهد hop خراب را در top-3 قرار می‌دهد و سرویس متأثر را نام می‌برد.
  - AC: تشخیص در ≤ ۲ بازهٔ polling؛ top-3 برای ≥ ۲ از ۳؛ خروجی واقعیت/استنباط/confidence را جدا می‌کند؛ بازخورد اپراتور ثبت می‌شود.
- **US-23-06 (PoC-5)** — به‌عنوان معمار می‌خواهم ذخیره و query KPIهای مشتق در PostgreSQL را در حجم مصنوعی Low/Medium بسنجم تا آستانه‌های trigger ClickHouse تعیین شود.
  - AC: نرخ نوشتن، اندازهٔ ردیف/روز، p95 query داشبورد 24 h و 30 d در برابر NFR-PRF-003 گزارش می‌شود.
- **US-23-07** — به‌عنوان مسئول امنیت می‌خواهم پس از PoC ممیزی redaction (با نرمال‌سازی ارقام فارسی/عربی-هندی) و حذف داده و حساب‌ها انجام شود.

**Tasks کلیدی**

| ID | Task | خروجی |
|---|---|---|
| **T-23-01** | **نخستین کار پس از تأیید:** نگارش پروتکل PoC (دامنه، time-box، معیار پذیرش، داده، budget، فهرست حوادث) و درخواست دسترسی‌های موقت | پروتکل تأییدشده |
| T-23-02 | آماده‌سازی محیط PoC آفلاین (VM، PostgreSQL، ابزار اسکریپت) و تأمین نمونهٔ غیرتولیدی Zabbix | محیط PoC |
| T-23-03 | PoC-1: مدل‌سازی برش VS-1 در Zabbix غیرتولیدی و خواندن services/SLA | گزارش PoC-1 |
| T-23-04 | PoC-2: تعریف جست‌وجوهای outcome/latency و اجرای آن‌ها با مدیر منبع | گزارش PoC-2 + اندازه‌گیری بار |
| T-23-05 | PoC-3: خواندن MQ از `mq_prometheus` یا Zabbix؛ آزمون مجوز حداقل (با EXP-04) | گزارش PoC-3 |
| T-23-06 | PoC-4: اسکریپت دورریختنی رتبه‌بندی شواهد و replay حوادث | گزارش PoC-4 |
| T-23-07 | PoC-5: مولد بار مصنوعی و آزمون PostgreSQL | گزارش PoC-5 |
| T-23-08 | ممیزی redaction، حذف داده/حساب‌ها، گزارش نهایی و پیشنهاد تصمیم ADR-0008/0021/0023 برای G-PoC | گزارش PoC |

- **وابستگی:** M0 (G0)؛ تأییدهای امنیتی؛ Q-13، Q-07، Q-26، Q-27، Q-28، Q-22.

---

## ۳. Epicهای فاز ۱ (جزئیات کامل)

### EP-01 — Engineering foundation

- **هدف:** مخزن، ساخت و CI آفلاین با مرزهای ماژول اجباری، تا هر قابلیت بعدی روی پایه‌ای قابل اعتماد و قابل ممیزی ساخته شود.
- **فاز:** P1 · **اولویت:** Must
- **FR/NFR:** NFR-MNT-001..003، NFR-SEC-007، NFR-DEP-001.
- **Features:** اسکلت `platform-core`؛ قواعد ArchUnit؛ ساخت آفلاین با mirror داخلی؛ pipeline CI (lint، test، SBOM، scan، sign)؛ استانداردهای کدنویسی و ADR-as-code؛ اسکلت `web-ui`.

**User Stories**

- **US-01-01** — به‌عنوان مهندس توسعه می‌خواهم اسکلت ماژول‌های `platform-core` با مرزهای اجباری داشته باشم تا وابستگی نامجاز بین ماژول‌ها در CI شکست بخورد.
  - AC: ماژول‌های `catalog`, `topology`, `calendar`, `slo`, `ingestion`, `connectors-control`, `health`, `transactions`, `events`, `correlation`, `anomaly`, `incidents`, `notification`, `evidence-query`, `iam`, `audit`, `query-api` به‌صورت خالی (فقط API عمومی و package داخلی) وجود دارند.
  - AC: یک آزمون ArchUnit عمدتاً شکسته (دسترسی `catalog` به internal `audit`) در یک branch آزمایشی CI را قرمز می‌کند.
  - AC: artifact واحد با role انتخابی از پیکربندی (`api`, `ingest`, `analytics`) اجرا می‌شود و فقط bean‌های role را بالا می‌آورد.
- **US-01-02** — به‌عنوان مهندس پلتفرم می‌خواهم ساخت کامل بدون Internet انجام شود تا در شبکه بانک قابل تکرار باشد.
  - AC: با قطع کامل Internet و فقط mirror داخلی، `build` از صفر موفق است.
  - AC: نسخه همه وابستگی‌ها pin شده و lockfile/verification metadata در مخزن است.
- **US-01-03** — به‌عنوان مسئول امنیت می‌خواهم هر artifact دارای SBOM (CycloneDX) و امضا باشد تا منشأ آن قابل ممیزی باشد.
  - AC: CI برای هر build یک SBOM و checksum SHA-256 تولید می‌کند؛ اسکن آسیب‌پذیری با پایگاه داده آفلاین اجرا و Critical حل‌نشده build را متوقف می‌کند (با فرایند استثنای مستند).
- **US-01-04** — به‌عنوان توسعه‌دهنده frontend می‌خواهم اسکلت `web-ui` با RTL و locale فارسی داشته باشم تا همه صفحات از ابتدا راست‌به‌چپ باشند.
  - AC: صفحه خالی با `dir="rtl"`، فونت فارسی بسته‌بندی‌شده محلی (بدون CDN)، و نمایش یک تاریخ نمونه به جلالی با منطقه زمانی Asia/Tehran از tzdata.

**Tasks کلیدی**

| ID | Task | خروجی |
|---|---|---|
| **T-01-01** | **نخستین Task ساخت پس از G-PoC:** ایجاد اسکلت مخزن `platform-core` به‌صورت modular monolith (Gradle multi-module یا Maven)، مرزهای ماژول + آزمون ArchUnit، ساخت آفلاین با mirror داخلی، pipeline CI (lint/test/SBOM)، **بدون هیچ قابلیت کسب‌وکاری و بدون کد PoC** — فقط پس از رفع انسداد ADR-0003 | مخزن قابل ساخت، CI سبز |
| T-01-02 | قواعد ArchUnit: فقط package `api` هر ماژول قابل دسترسی؛ ممنوعیت چرخه | کتابخانه قواعد |
| T-01-03 | پیکربندی role-based startup (`api`/`ingest`/`analytics`) | پیکربندی + آزمون |
| T-01-04 | پیکربندی mirror داخلی (Maven/Gradle، npm، OCI) و مستند آن [باز Q-09] | مستند + تنظیمات |
| T-01-05 | مرحله CI: SBOM CycloneDX، checksum، امضا (ابزار امضا وابسته به ADR-0016) | artifact امضاشده |
| T-01-06 | اسکلت `web-ui` (React + TypeScript، ADR-0014)، lint، آزمون واحد | اسکلت UI |
| T-01-07 | قالب‌های ADR، PR template، استاندارد پیام commit، lint Markdown و بررسی پیوند مستندات | قالب‌ها |
| T-01-08 | image پایهٔ آفلاین PostgreSQL برای Testcontainers در registry داخلی (ClickHouse فقط پس از trigger ADR-0008) | images |

- **وابستگی:** G-PoC؛ ADR-0003 [باز Q-01]؛ mirror [باز Q-09].

### EP-02 — Identity, access & audit

- **هدف:** ورود امن کاربران بانک، دسترسی مبتنی بر نقش با scope محیط/گروه سرویس، و audit تغییرناپذیر.
- **فاز:** P1 · **اولویت:** Must
- **FR/NFR:** FR-ADM-001..003؛ NFR-SEC-005, 006, 009؛ ADR-0015 [باز Q-04].
- **Features:** OIDC login (IdP بانک یا Keycloak + AD federation)؛ نگاشت گروه‌های AD به نقش؛ RBAC deny-by-default با scope؛ audit append-only + forward به SIEM/Splunk؛ مدیریت session.

**User Stories**

- **US-02-01** — به‌عنوان اپراتور NOC می‌خواهم با حساب AD خود وارد شوم تا رمز جداگانه نداشته باشم.
  - AC: جریان OIDC Authorization Code + PKCE؛ token در UI در storage ماندگار ذخیره نمی‌شود؛ MFA در صورت پشتیبانی IdP اعمال می‌شود.
  - AC: با IdP در دسترس نبودن، ورود جدید ممکن نیست و پیام فارسی روشن نمایش داده می‌شود؛ sessionهای موجود تا انقضا معتبرند.
- **US-02-02** — به‌عنوان مدیر پلتفرم می‌خواهم گروه‌های AD را به نقش‌ها و scopeها نگاشت کنم تا دسترسی متمرکز مدیریت شود.
  - AC: نقش‌های پایه: `viewer`, `operator`, `service-owner`, `catalog-admin`, `platform-admin`, `auditor` [توصیه].
  - AC: scope با محیط (PROD/DR/UAT) و گروه سرویس؛ کاربر بدون نگاشت به هیچ داده‌ای دسترسی ندارد.
- **US-02-03** — به‌عنوان تحلیلگر SOC می‌خواهم همه عملیات تغییر‌دهنده و ورودها در audit تغییرناپذیر ثبت شوند تا قابل پیگیری باشند.
  - AC: هر رکورد: actor، action، target، before/after diff، `occurred_at` (UTC)، source IP، correlation id.
  - AC: جدول audit فقط INSERT؛ کاربر پایگاه داده برنامه مجوز UPDATE/DELETE ندارد؛ زنجیره hash برای تشخیص دست‌کاری [توصیه].
  - AC: forward به syslog/Splunk HEC قابل پیکربندی (اختیاری، [باز Q-10]).
- **US-02-04** — به‌عنوان ممیز می‌خواهم audit را با فیلتر و خروجی CSV ببینم تا گزارش ارائه دهم.

**Tasks کلیدی**

| ID | Task |
|---|---|
| T-02-01 | ماژول `iam`: Resource Server OIDC، اعتبارسنجی JWT، نگاشت claimها |
| T-02-02 | پیاده‌سازی Keycloak + AD federation به‌عنوان پیش‌فرض در صورت نبود IdP [باز Q-04] |
| T-02-03 | مدل RBAC/scope در `metadata-db` و policy enforcement point در `query-api` |
| T-02-04 | ماژول `audit`: جدول append-only، hash chain، API جستجو |
| T-02-05 | forwarder audit (syslog RFC 5424 / Splunk HEC) |
| T-02-06 | آزمون‌های امنیتی: دسترسی بدون نقش، IDOR بین scopeها، انقضای token |

- **وابستگی:** EP-01؛ ADR-0015.

### EP-03 — Service catalog & topology

- **هدف:** منبع واحد سرویس‌های بانکی، اجزای فنی، مالکیت و وابستگی‌ها با provenance.
- **فاز:** P1 (دستی) → P2 (نقشه، ExternalRef) → P4 (کشف) · **اولویت:** Must
- **FR/NFR:** FR-CAT-001..012؛ NFR-USA-002؛ ADR-0007، ADR-0011.
- **Features:** CRUD BusinessService/TechnicalService/Component؛ ServiceType قابل گسترش با JSON Schema برای ویژگی‌های سفارشی؛ Environment/Site/Zone/TrustBoundary؛ Dependency با provenance/confidence/approval؛ import/export YAML/CSV؛ تاریخچه تغییر؛ ExternalRef (P2)؛ نقشه توپولوژی و impact traversal (P2)؛ کشف (P4).

**User Stories (P1)**

- **US-03-01** — به‌عنوان مالک سرویس می‌خواهم سرویس کسب‌وکاری را با مالک، critical بودن، محیط و وضعیت ثبت کنم تا تیم عملیات بداند مسئول کیست.
  - AC: فیلدهای اجباری: نام فارسی، شناسه انگلیسی یکتا، مالک، criticality، محیط‌ها؛ اعتبارسنجی و پیام خطای فارسی.
- **US-03-02** — به‌عنوان مدیر کاتالوگ می‌خواهم نوع سرویس جدید با ویژگی‌های سفارشی (اصطلاحات بانک) تعریف کنم تا بدون تغییر کد قابل گسترش باشد.
  - AC: ServiceType با JSON Schema ویژگی‌ها؛ نمونه‌ها بر اساس schema اعتبارسنجی می‌شوند؛ تغییر schema نسخه‌دار است.
- **US-03-03** — به‌عنوان مهندس SRE می‌خواهم وابستگی دستی با جهت، پروتکل، criticality و TrustBoundary ثبت کنم تا زنجیره سرویس مدل شود.
  - AC: وابستگی دستی `provenance=MANUAL`، `confidence=1.0`، `approval=APPROVED`؛ چرخه مجاز ولی هشدار داده می‌شود.
- **US-03-04** — به‌عنوان مدیر کاتالوگ می‌خواهم کاتالوگ را با YAML/CSV وارد و صادر کنم تا ثبت انبوه و بازبینی در Git ممکن باشد.
  - AC: import idempotent (کلید طبیعی = شناسه انگلیسی)؛ حالت dry-run با گزارش diff؛ خطا در یک ردیف کل تراکنش را rollback می‌کند.
  - AC: export → import همان وضعیت را بازتولید می‌کند (round-trip test).
- **US-03-05** — به‌عنوان ممیز می‌خواهم تاریخچه تغییر هر موجودیت را ببینم تا بدانم چه کسی چه چیزی را تغییر داد.
- **US-03-06** — به‌عنوان مالک سرویس VS-1 می‌خواهم زنجیره کارت به کارت (بخش ۷ master plan) را ثبت کنم تا مبنای VS-1 فراهم شود.

**User Stories (P2)**

- **US-03-07** — به‌عنوان مدیر کاتالوگ می‌خواهم هر جزء را به شناسه‌های منبع (Zabbix hostid/itemid، SolarWinds NodeID، Splunk host/sourcetype، ES index+filter، MQ qmgr+queue، F5 virtual/pool) نگاشت کنم تا داده به جزء درست متصل شود (`FR-CAT-009`).
  - AC: پیشنهاد نگاشت خودکار بر اساس hostname/IP با `PROPOSED`، تأیید دستی لازم.
- **US-03-08** — به‌عنوان اپراتور NOC می‌خواهم نقشه توپولوژی سرویس را با رنگ سلامت و provenance ببینم تا مسیر تأثیر را درک کنم (`FR-CAT-008`, `FR-UI-003`).
  - AC: ۵۰۰ گره در ≤ 2 s رندر (NFR-PRF-005، تأیید با BM-10)؛ یال‌های DISCOVERED با خط‌چین و confidence.
- **US-03-09** — به‌عنوان SRE می‌خواهم impact traversal (upstream/downstream) یک جزء را ببینم.

**Tasks کلیدی**

| ID | Task |
|---|---|
| T-03-01 | schema PostgreSQL کاتالوگ و توپولوژی (adjacency + recursive CTE، ADR-0007) |
| T-03-02 | API REST کاتالوگ (OpenAPI 3.1) با pagination و فیلتر scope |
| T-03-03 | ServiceType registry با اعتبارسنجی JSON Schema |
| T-03-04 | import/export YAML/CSV با dry-run |
| T-03-05 | رویدادهای domain کاتالوگ از طریق outbox برای audit و ماژول‌های بعدی |
| T-03-06 | صفحات UI فهرست/جزئیات/ویرایش سرویس (RTL) |
| T-03-07 | (P2) جدول ExternalRef و پیشنهاد نگاشت |
| T-03-08 | (P2) نمای Cytoscape.js با layout قابل ذخیره |
| T-03-09 | (P2) API impact traversal با محدودیت عمق |

### EP-04 — Business calendar & maintenance windows

- **هدف:** تفکیک «غیرفعال مورد انتظار» از «قطعی» با تقویم بانکی جلالی‌محور و پنجره‌های سرویس/نگهداری.
- **فاز:** P1 · **اولویت:** Must
- **FR/NFR:** FR-CAL-001..003 (P1)، FR-CAL-008 (P2)، زیرساخت برای FR-CAL-004..007 (P3)؛ NFR-USA-001.
- **Features:** BusinessCalendar با DayType؛ import تعطیلات سالانه به‌صورت داده (تعطیلات قمری محاسبه نمی‌شود)؛ ServiceWindow تکرارشونده (مثلاً چرخه‌های ساتنا — پیکربندی، نه hardcode) [باز Q-15]؛ MaintenanceWindow برنامه‌ریزی‌شده/غیربرنامه‌ریزی‌شده با معنای suppression.

**User Stories**

- **US-04-01** — به‌عنوان مدیر پلتفرم می‌خواهم فایل تعطیلات سال آینده را import کنم تا تقویم به‌روز باشد.
  - AC: فرمت YAML/CSV با تاریخ جلالی و نوع روز؛ اعتبارسنجی تاریخ؛ گزارش تفاوت با سال قبل؛ هشدار اگر تقویم سال آینده تا ۳۰ روز مانده به پایان سال بارگذاری نشده باشد (R-14).
- **US-04-02** — به‌عنوان مالک سرویس می‌خواهم پنجره پردازش سرویس (مثلاً ساعات کاری روزهای کاری) تعریف کنم تا خارج از آن «قطعی» گزارش نشود.
  - AC: قاعده تکرار بر اساس DayType و بازه ساعت به وقت Asia/Tehran؛ ذخیره قاعده نه نمونه‌ها؛ آزمون مرز نیمه‌شب.
- **US-04-03** — به‌عنوان اپراتور NOC می‌خواهم پنجره نگهداری برای یک سرویس/جزء ثبت کنم تا هشدارهای آن دوره سرکوب و علامت‌گذاری شود.
  - AC: suppression هشدارها را حذف نمی‌کند بلکه علامت `MAINTENANCE` می‌زند؛ ثبت در audit.
- **US-04-04** — به‌عنوان کاربر می‌خواهم همه تاریخ‌ها در UI جلالی و قابل تغییر به میلادی باشند.
- **US-04-05 (P2، Should، `FR-CAL-008`)** — به‌عنوان مدیر Zabbix می‌خواهم تعطیلات، پنجره‌های سرویس و نگهداری پلتفرم به‌صورت maintenance/زمان‌بندی SLA در Zabbix منعکس شوند تا منطق suppression در دو جا تکرار نشود.
  - AC: پیش‌فرض: تولید فایل/بستهٔ dry-run برای اعمال توسط مالک Zabbix؛ نوشتن مستقیم فقط با حساب محدود و تأیید Q-26 (استثنای E-02).
  - AC: idempotent (اجرای مجدد تکرار نمی‌سازد)؛ گزارش drift؛ هیچ ورودی غیرپلتفرمی تغییر نمی‌کند؛ audit هر اجرا.

**Tasks کلیدی:** T-04-01 مدل داده تقویم و DayType؛ T-04-02 کتابخانه تبدیل جلالی (با آزمون سال کبیسه، مقایسه با جدول مرجع)؛ T-04-03 موتور ارزیابی ServiceWindow (`isExpectedActive(service, instant)`)؛ T-04-04 import تعطیلات؛ T-04-05 UI تقویم و پنجره نگهداری؛ T-04-06 (P2) مولد همگام‌سازی Zabbix (dry-run/export، نگاشت ExternalRef، تشخیص drift) و آزمون‌های sync با fixture.

- **وابستگی:** EP-01؛ داده تقویم [باز Q-15].

### EP-05 — Offline packaging & deployment

- **هدف:** نصب و ارتقای کاملاً آفلاین و قابل بازبینی.
- **فاز:** P1 (bundle v1: `platform-core` + PostgreSQL + `web-ui`) → P2 (v2 + `connector-runtime` + exporter `mq_prometheus`) → P6 (نهایی، Helm در صورت نیاز؛ ClickHouse/Grafana فقط به‌صورت ماژول اختیاری) · **اولویت:** Must
- **FR/NFR:** NFR-DEP-001..004، NFR-SEC-007؛ ADR-0016 [باز Q-02/Q-03].
- **Features:** bundle آفلاین (OCI images، SBOM، امضا، checksum، اسکریپت نصب)؛ Compose/Podman برای VM؛ پیکربندی به‌صورت کد؛ verification پیش از نصب. جزئیات در [deployment-and-runbooks.md](../operations/deployment-and-runbooks.md).

**User Stories**

- **US-05-01** — به‌عنوان مهندس عملیات بانک می‌خواهم bundle را روی VMهای آماده بدون Internet نصب کنم تا با سیاست شبکه سازگار باشد.
  - AC: نصب UAT ≤ 1 روز کاری (NFR-DEP-002)؛ اسکریپت پیش‌بررسی (OS، دیسک، پورت، NTP، گواهی‌ها).
- **US-05-02** — به‌عنوان مسئول امنیت می‌خواهم پیش از نصب امضا و checksum همه اجزا را بررسی کنم.
  - AC: نصب با امضای نامعتبر متوقف می‌شود و log فارسی/انگلیسی می‌دهد.
- **US-05-03** — به‌عنوان مهندس عملیات می‌خواهم پیکربندی هر محیط در یک مخزن نسخه‌دار باشد و secretها جدا تأمین شوند.

**Tasks کلیدی:** T-05-01 ساختار bundle و manifest؛ T-05-02 ساخت OCI images با base image تأییدشده؛ T-05-03 اسکریپت‌های install/verify/upgrade/rollback؛ T-05-04 قالب پیکربندی محیط؛ T-05-05 مستند نصب.

### EP-22 — Data discovery & source onboarding

- **هدف:** کاهش زودهنگام ریسک داده (R-01, R-02, R-12) با آزمایش‌های EXP-01..EXP-05 و آغاز فرایند تأیید دسترسی‌ها.
- **فاز:** P0.5→P2 (مسیر موازی؛ EXP-01 و EXP-04 ورودی EP-23) · **اولویت:** Must
- **FR/NFR:** پیش‌نیاز FR-INT-001..004، FR-TXN-003، FR-MQ-002؛ NFR-SEC-003، NFR-IMP-001.
- **Features:** inventory منابع و نسخه‌ها [باز Q-08]؛ بسته درخواست حساب read-only per source؛ تحلیل نمونه لاگ ناشناس‌شده؛ آزمون استخراج sandbox؛ آزمون مجوز MQ؛ نظرسنجی clock skew.

**User Stories**

- **US-22-01** — به‌عنوان معمار می‌خواهم inventory منابع (نسخه، API فعال، مالک، zone) داشته باشم تا نسخه‌های API و fixtureها را برنامه‌ریزی کنم.
- **US-22-02** — به‌عنوان مدیر تحویل می‌خواهم بسته استاندارد درخواست دسترسی read-only (دامنه مجوز، IPها، rate limit، مالک حساب) داشته باشم تا تأیید امنیتی سریع‌تر شود.
  - AC: برای Zabbix، SolarWinds، Splunk، ES، IBM MQ، RabbitMQ، F5/HAProxy یک برگه حداقل مجوز «نیازمند تأیید در نسخه نصب‌شده بانک».
- **US-22-03** — به‌عنوان مالک محصول می‌خواهم گزارش EXP-01 برای سرویس VS-1 داشته باشم تا بدانم کدام سطح L0/L2 ممکن است.
  - AC: فهرست فیلدهای outcome/response code، latency، کلیدهای همبستگی، فرمت timestamp؛ هیچ داده خام در گزارش؛ نمونه‌ها توسط بانک ناشناس‌سازی شده‌اند.
- **US-22-04** — به‌عنوان مهندس integration می‌خواهم نتیجه EXP-02 (استخراج read-only در sandbox) داشته باشم تا fixtureهای واقعی contract test ضبط شوند.
- **US-22-05** — به‌عنوان مهندس middleware می‌خواهم EXP-04 نشان دهد حداقل مجوزهای MQ برای خواندن depth/rates/channel status چیست.
- **US-22-06** — به‌عنوان معمار می‌خواهم EXP-05 اختلاف ساعت منابع را اندازه‌گیری کند تا پنجره تحمل همبستگی تعیین شود.
- **US-22-07** — به‌عنوان معمار می‌خواهم بدانم NGINX، WAF، API gateway، ESB و JVMهای زنجیرهٔ VS-1 اکنون در کدام ابزار موجود (Zabbix/SolarWinds/Splunk/ES) و با چه معیارهایی پایش می‌شوند، تا کانکتور مستقیم فقط برای شکاف اثبات‌شده ساخته شود (در benchmark تأییدنشده؛ [باز Q-08]).
  - AC: ماتریس جزء × ابزار × معیار با برچسب «موجود/ناموجود/نامعلوم» و templateهای Zabbix نصب‌شده.

**Tasks کلیدی:** T-22-08 ماتریس پوشش ابزار موجود (US-22-07)؛ T-22-01 قالب inventory؛ T-22-02 بسته درخواست دسترسی؛ T-22-03 پروتکل EXP-01 (با تأیید امنیت)؛ T-22-04 پروتکل EXP-02؛ T-22-05 پروتکل EXP-04؛ T-22-06 پروتکل EXP-05؛ T-22-07 ضبط و پاک‌سازی fixtureها (redaction پیش از ورود به مخزن).

- **وابستگی:** M0؛ تأییدهای امنیتی بانک.

---

## ۴. Epicهای فاز ۲ (جزئیات کامل)

### EP-06 — Ingestion pipeline & canonical schemas

- **هدف:** مسیر واحد و قابل اعتماد ورود داده‌های **مشتق** (KPI، سری‌های مرتبط با KPI، رویداد نرمال‌شده): normalize، UTC، redact، dedup، late data، backpressure، نوشتن idempotent در PostgreSQL ([ADR-0008](../architecture/architecture-decision-records/ADR-0008-telemetry-store-postgresql-first-clickhouse-on-trigger.md)).
- **فاز:** P2 (OTLP receive و `edge-collector` به EP-14/P4 منتقل شدند) · **اولویت:** Must
- **FR/NFR:** FR-ING-003, 005..009؛ FR-TXN-010؛ NFR-REL-001..003؛ NFR-PRV-001..003؛ NFR-PRF-001؛ ADR-0005، ADR-0006، ADR-0008، ADR-0010، ADR-0018.
- **Features:** batch API `/internal/v1/ingest/events` (و مسیر سری‌های متریک مشتق) از `connector-runtime`؛ schema رویداد با envelope شبیه CloudEvents و JSON Schema نسخه‌دار؛ attributeهای `bank.*`؛ redaction در ingestion (با نرمال‌سازی ارقام فارسی/عربی-هندی)؛ HMAC tokenization؛ dedup با کلید idempotency؛ ثبت `observed_at`, `ingested_at`, `source_clock_skew_estimate`؛ جداول partitioned و time-bucketed PostgreSQL و rollup با بازمحاسبهٔ زمان‌بندی‌شده؛ لایهٔ repository مستقل از ذخیره‌گاه تا ورود ClickHouse پس از trigger بدون تغییر قرارداد ممکن باشد.

**User Stories**

- **US-06-01** — به‌عنوان مهندس integration می‌خواهم داده را با batch API ارسال کنم و پاسخ قطعی (پذیرفته/رد/throttle) بگیرم تا retry درست انجام شود.
  - AC: HTTP 429 همراه Retry-After هنگام فشار؛ رد schema نامعتبر با خطای قابل خواندن؛ batch تکراری بدون رکورد تکراری.
- **US-06-02** — به‌عنوان مسئول امنیت می‌خواهم هر رکورد پیش از ذخیره redact شود تا PAN/کد ملی/شبا/موبایل هرگز ذخیره نشود.
  - AC: corpus مصنوعی (quality strategy §۷.۵) صفر نشت؛ شمارنده redaction per source به‌عنوان metric؛ کلیدهای همبستگی با HMAC-SHA256 و کلید per environment از SecretProvider.
- **US-06-03** — به‌عنوان SRE می‌خواهم داده دیررس با `observed_at` درست ثبت شود و rollupها اصلاح شوند تا گزارش‌ها دقیق بمانند.
  - AC: داده تا X ساعت دیرتر (پیکربندی، پیش‌فرض ۲۴ h [فرض]) پذیرفته و rollup مربوطه بازمحاسبه می‌شود؛ دیرتر از آن رد و شمارش می‌شود.
- **US-06-04** — به‌عنوان اپراتور می‌خواهم شکاف داده به‌صورت صریح دیده شود تا داده گم‌شده با «صفر» اشتباه نشود (NFR-REL-002).
- ~~US-06-05 (`edge-collector` با صف دیسکی ≥ 4 h)~~ — **منتقل به EP-14 (P4) به‌عنوان US-14-02، فقط در صورت استقرار `edge-collector`**. در P2، تحمل قطع با checkpoint/watermark در `connector-runtime` و بازخوانی از منبع تأمین می‌شود (US-07-06).

**Tasks کلیدی**

| ID | Task |
|---|---|
| T-06-01 | JSON Schema رویداد مشترک v1 و envelope؛ قواعد سازگاری semver |
| T-06-02 | ماژول `ingestion`: ورودی batch API (role `ingest`)؛ OTLP receiver به T-14-05 (P4) منتقل شد |
| T-06-03 | batch API داخلی با mTLS و احراز هویت client cert per connector instance |
| T-06-04 | کتابخانه redaction مشترک (Java) برای `connector-runtime` و `ingestion` با نرمال‌سازی ارقام فارسی/عربی-هندی (processor معادل collector فقط در P4)؛ detectorهای Luhn، کد ملی (الگوریتم checksum)، شبا (IR + 24 رقم، mod-97)، موبایل |
| T-06-05 | HMAC tokenizer با چرخش کلید (key id در رکورد) |
| T-06-06 | dedup/idempotency key و نوشتن batch در PostgreSQL (`ON CONFLICT` با کلید طبیعی) |
| T-06-07 | جداول PostgreSQL partitioned (به ازای زمان): سری‌های مرتبط با KPI، rollup 1m/1h، خلاصهٔ تراکنش، events؛ حذف partition مطابق ADR-0018 و Q-16 |
| T-06-08 | ~~ساخت آفلاین `edge-collector` با OCB~~ — منتقل به T-14-06 (P4، اختیاری) |
| T-06-09 | backpressure و metrics صف درون‌فرایندی |
| T-06-10 | پایش triggerهای ADR-0008 (نرخ ردیف، حجم، p95) و تکرار PoC-5 روی سخت‌افزار UAT (با EP-19)؛ BM-01/BM-07 فقط در صورت trigger |

- **وابستگی:** EP-03 (شناسه موجودیت)، ADR-0008 (Provisional؛ آستانه‌ها از PoC-5).

### EP-07 — Connector SDK & connector-runtime

- **هدف:** افزودن کانکتور بدون تغییر core (NFR-MNT-004) با قرارداد واحد برای capability، زمان‌بندی، budget، health و secret.
- **فاز:** P2 · **اولویت:** Must
- **FR/NFR:** FR-ING-001, 010, 011؛ FR-ADM-004؛ NFR-SEC-003, 004, 010؛ NFR-IMP-001؛ NFR-OBS-002؛ ADR-0004، ADR-0020.
- **Features:** SPI کانکتور (`describeCapabilities`, `verifyCapabilities`, `poll`, `executeEvidenceQuery`, `health`)؛ دریافت پیکربندی از core با mTLS؛ command queue با long-poll `/internal/v1/commands`؛ rate limiter و query budget per source؛ SecretProvider (فایل رمز‌شده/vault [باز Q-09])؛ checkpoint/watermark؛ شبیه‌ساز و harness.

**User Stories**

- **US-07-01** — به‌عنوان توسعه‌دهنده کانکتور می‌خواهم با پیاده‌سازی یک SPI و مستند capability، کانکتور جدید بسازم بدون تغییر `platform-core`.
  - AC: کانکتور نمونه (file-based import، `FR-ING-005`) فقط با SDK ساخته و در CI با harness آزموده می‌شود.
- **US-07-02** — به‌عنوان مدیر پلتفرم می‌خواهم وضعیت هر ConnectorInstance (آخرین poll موفق، lag، خطاها، مصرف budget، capability تأییدشده) را ببینم (`FR-ING-010`).
- **US-07-03** — به‌عنوان مدیر منبع (مثلاً Splunk) می‌خواهم سقف بار کانکتور (درخواست/دقیقه، هم‌زمانی، بازه زمانی query) قابل پیکربندی و رعایت‌شده باشد.
  - AC: تجاوز از budget منجر به تأخیر poll و هشدار می‌شود، نه افزایش بار.
- **US-07-04** — به‌عنوان مسئول امنیت می‌خواهم credentialها فقط در `connector-runtime` همان zone و از طریق SecretProvider باشند و در log ظاهر نشوند.
- **US-07-05** — به‌عنوان مهندس شبکه می‌خواهم همه اتصالات از `connector-runtime` به‌سمت بیرون باشد تا core هیچ اتصالی به zoneها باز نکند.
- **US-07-06** — به‌عنوان SRE می‌خواهم با قطع ۱ ساعتهٔ ارتباط `connector-runtime` و core، داده از دست نرود (checkpoint/watermark، بافر محدود محلی و بازخوانی از منبع) و شکاف صریح ثبت شود.
- **US-07-07** — به‌عنوان مهندس middleware می‌خواهم `connector-runtime` endpoint exporterهای Prometheus (مانند `mq_prometheus`) را scrape کند (`FR-ING-003`) تا برای MQ به `edge-collector` نیاز نباشد.
  - AC: محدودیت cardinality؛ فقط سری‌های دارای ExternalRef نگه داشته می‌شوند.

**Tasks کلیدی:** T-07-01 تعریف SPI و مدل Capability؛ T-07-02 runtime (scheduler، checkpoint، retry با jitter، circuit breaker)؛ T-07-03 کانال پیکربندی و command long-poll؛ T-07-04 SecretProvider با پیاده‌سازی فایل رمز‌شده؛ T-07-05 rate limiter/budget؛ T-07-06 harness شبیه‌سازی + replay fixture؛ T-07-07 UI diagnostics؛ T-07-08 کانکتور file import.

### EP-08 — Existing-platform connectors

- **هدف:** استفاده از داده موجود در Zabbix، Elasticsearch/OpenSearch، Splunk و SolarWinds به‌صورت read-only با بار محدود (ADR-0020).
- **فاز:** P2 · **اولویت:** Must (SolarWinds: Should)
- **FR/NFR:** FR-INT-001..004, 006, 008؛ FR-RUN-004؛ FR-LB-002 (از طریق این منابع)؛ NFR-IMP-001, 002؛ NFR-PRF-002.
- **ترتیب (پس از benchmark):** Zabbix و **پلتفرم لاگی که لاگ‌های VS-1 را دارد** (Splunk یا ES، بر اساس PoC-2) اول؛ پلتفرم لاگ دوم در صورت نیاز؛ SolarWinds (Should).
- **Features:** Zabbix JSON-RPC (hosts، items منتخب مرتبط با KPI، history/trends on-demand برای drill-down، problems، services/SLA)؛ ES `_search` با aggregation دقیقه‌ای؛ Splunk REST search (job با بازه محدود)؛ SolarWinds SWIS/SWQL؛ deep link؛ نگاشت به ExternalRef؛ capability per version. همه «نیازمند تأیید در نسخه نصب‌شده بانک» [باز Q-08].

**User Stories**

- **US-08-01** — به‌عنوان اپراتور NOC می‌خواهم متریک‌های میزبان‌ها و itemهای Zabbix مرتبط با سرویس VS-1 را در BSOIP ببینم تا وضعیت زیرساخت در زمینه سرویس دیده شود.
  - AC: فقط itemهای دارای ExternalRef poll می‌شوند؛ problems به رویداد مشترک تبدیل می‌شوند؛ deep link به صفحه Zabbix.
- **US-08-02** — به‌عنوان SRE می‌خواهم شمارش دقیقه‌ای لاگ‌های سرویس بر اساس فیلد outcome از ES دریافت شود تا نرخ خطا محاسبه شود.
  - AC: فقط query aggregation (نه سند خام)؛ بازه watermark-based بدون هم‌پوشانی مضاعف؛ مصرف budget گزارش می‌شود.
- **US-08-03** — همان برای Splunk (search job با `earliest/latest` محدود، `stats count by`).
- **US-08-04** — به‌عنوان اپراتور می‌خواهم node/interface status از SolarWinds دریافت شود (Should).
- **US-08-05** — به‌عنوان مدیر پلتفرم می‌خواهم capability نسخه نصب‌شده هر منبع در اتصال اول تأیید شود تا قابلیت‌های پشتیبانی‌نشده خاموش شوند (R-12).
- **US-08-06 (Should، `FR-INT-008`)** — به‌عنوان SRE می‌خواهم وضعیت business service و SLI/SLA زبیکس به‌عنوان شاهد در توضیح سلامت سرویس دیده شود تا درخت سلامت زیرساختی را دوباره نسازیم (ADR-0023).
  - AC: `service.get`/`sla.getsli` فقط‌خواندنی؛ نگاشت به ExternalRef؛ برچسب منبع `zabbix-service`؛ در نبود API، Capability «unavailable» و بقیهٔ کانکتور سالم.
- **US-08-07** — به‌عنوان مدیر Splunk/ES می‌خواهم تعریف جست‌وجوهای KPI (به ازای TransactionType) را از پلتفرم دریافت و خودم نصب کنم تا حساب پلتفرم فقط‌خواندنی بماند [باز Q-28].
  - AC: خروجی نسخه‌دار تعریف جست‌وجو؛ کانکتور فقط نتایج را می‌خواند؛ جایگزین: اجرای REST با budget Q-22.

**Tasks کلیدی:** T-08-01 کانکتور Zabbix + fixtureهای نسخه‌ها؛ T-08-02 کانکتور ES/OpenSearch؛ T-08-03 کانکتور Splunk (T-08-02 یا T-08-03 هر کدام پلتفرم لاگ VS-1 باشد اول)؛ T-08-04 کانکتور SolarWinds؛ T-08-05 ساخت‌گر deep link؛ T-08-06 BM-04 و BM-05 با مدیران منابع؛ T-08-07 خواندن services/SLA زبیکس (FR-INT-008)؛ T-08-08 مولد تعریف جست‌وجوی KPI برای Splunk/ES.

- **وابستگی:** EP-07، EP-22 (دسترسی و نسخه).

### EP-09 — Middleware & LB connectors

- **هدف:** متریک‌های صف و LB برای زنجیره VS-1 از هر مسیر در دسترس.
- **فاز:** P2 · **اولویت:** IBM MQ Must؛ RabbitMQ/F5/HAProxy/NGINX Should
- **FR/NFR:** FR-MQ-001..003, 005؛ FR-LB-001, 002, 004؛ NFR-SEC-003؛ NFR-IMP-001.
- **Features:** IBM MQ از exporter استاندارد `mq_prometheus` (scrape توسط `connector-runtime`) یا itemهای موجود Zabbix، بر اساس PoC-3؛ PCF مستقیم فقط به‌عنوان جایگزین (depth، max depth، enq/deq، channel status؛ oldest message age فقط بدون تغییر پیکربندی تأییدنشده [باز Q-27])؛ LB/RabbitMQ ترجیحاً از templateهای Zabbix؛ RabbitMQ management API/Prometheus plugin (از طریق collector)؛ F5 iControl REST read-only؛ HAProxy stats؛ NGINX stub_status/Plus API؛ انقضای گواهی TLS؛ DLQ indicators؛ مدل VirtualServer/Pool/PoolMember.

**User Stories**

- **US-09-01** — به‌عنوان مهندس middleware می‌خواهم depth و نرخ‌های صف‌های زنجیره VS-1 (و سن قدیمی‌ترین پیام در صورت در دسترس بودن) را هر ۱۰–۶۰ ثانیه ببینم [فرض A-04].
  - AC: polling/scrape با حداقل مجوز تأییدشده در EXP-04/PoC-3؛ اثر با BM-06 اندازه‌گیری شده؛ معیار ناموجود Capability «unavailable».
- **US-09-02** — به‌عنوان اپراتور می‌خواهم وضعیت pool memberهای LB و توزیع درخواست‌ها را ببینم.
- **US-09-03** — به‌عنوان SRE می‌خواهم هشدار انقضای گواهی TLS روی virtual serverها را X روز قبل ببینم (`FR-LB-004`).
- **US-09-04** — به‌عنوان مهندس middleware می‌خواهم شاخص DLQ/پیام‌های ناموفق را ببینم (`FR-MQ-005`).

**Tasks کلیدی:** T-09-01 بسته‌بندی آفلاین و پیکربندی exporter `mq_prometheus` + scrape؛ T-09-02 مسیر MQ از itemهای Zabbix؛ T-09-07 (جایگزین، فقط در صورت شکاف) کانکتور PCF؛ T-09-03 RabbitMQ؛ T-09-04 F5؛ T-09-05 HAProxy/NGINX از طریق collector یا connector؛ T-09-06 BM-06.

### EP-10 — Health model & dashboards

- **هدف:** وضعیت سلامت قابل توضیح هر سرویس و جزء، با UNKNOWN صریح برای داده ناکافی.
- **فاز:** P2 (v1) → P3 (توسعه) · **اولویت:** Must
- **FR/NFR:** FR-HLT-001..003؛ FR-UI-002, 003؛ FR-ANO-001؛ NFR-USA-002؛ NFR-PRF-003, 004.
- **Features:** قواعد سلامت اعلانی per ServiceType/Component؛ `HealthState` (OK, DEGRADED, CRITICAL, UNKNOWN, EXPECTED_INACTIVE, MAINTENANCE)؛ freshness؛ propagation به‌عنوان impact؛ داشبورد نمای کلی؛ صفحه جزئیات با توضیح.

**User Stories**

- **US-10-01** — به‌عنوان اپراتور NOC می‌خواهم فهرست سرویس‌ها با وضعیت و دلیل کوتاه ببینم تا در چند ثانیه بدانم کجا مشکل است.
  - AC: بارگذاری ≤ 3 s (NFR-PRF-003)؛ به‌روزرسانی زنده با SSE؛ هر وضعیت لینک «چرا؟».
- **US-10-02** — به‌عنوان SRE می‌خواهم وقتی منبع داده قطع است وضعیت `UNKNOWN` با دلیل نمایش داده شود، نه `OK` یا `CRITICAL`.
- **US-10-03** — به‌عنوان مالک سرویس می‌خواهم قاعده آستانه ایستا تعریف کنم (مثلاً depth > N به مدت M دقیقه).
- **US-10-04** — به‌عنوان اپراتور می‌خواهم سرویسی که به جزء خراب وابسته است «تحت تأثیر» نشان داده شود، با تفکیک از خرابی مستقیم.

**Tasks کلیدی:** T-10-01 DSL/مدل قاعده سلامت (اعلانی، نسخه‌دار)؛ T-10-02 ارزیاب micro-batch در role `analytics` (30–60 s)؛ T-10-03 freshness tracker per ExternalRef؛ T-10-04 propagation روی توپولوژی؛ T-10-05 داشبورد و SSE؛ T-10-06 ذخیره تاریخچه سلامت در PostgreSQL (partitioned)؛ T-10-07 به‌کارگیری شاهد services/SLA زبیکس (FR-INT-008) در توضیح سلامت.

### EP-13 (بخش فاز ۲) — Common event model

- **US-13-01** — به‌عنوان SRE می‌خواهم problems/alerts از Zabbix، SolarWinds و هشدارهای Splunk در یک مدل رخداد مشترک با شدت نرمال‌شده ذخیره شوند (`FR-COR-001`).
  - AC: نگاشت شدت هر منبع به‌صورت داده؛ شناسه منبع و deep link حفظ می‌شود؛ رویداد باز/بسته شدن.
- **Tasks:** T-13-01 schema رویداد؛ T-13-02 نگاشت per source؛ T-13-03 جدول events (partitioned) و وضعیت جاری در PostgreSQL.

### EP-19 (بخش فاز ۲) — Performance baselines

- **US-19-01** — به‌عنوان معمار می‌خواهم سنجش PoC-5 روی سخت‌افزار UAT با سناریوی reuse تکرار و triggerهای ADR-0008/ADR-0010 ارزیابی شوند؛ BM-01/BM-07 فقط در صورت فعال شدن trigger.
- **Tasks:** T-19-01 مولد بار مصنوعی (سری‌های KPI، رویداد، خلاصه تراکنش) بر اساس سناریوهای reuse Low/Medium؛ T-19-02 اجرای سنجش PostgreSQL (و BM-01/02/07 در صورت trigger) و گزارش؛ T-19-03 BM-10 (UI)؛ T-19-04 BM-04/05/06 با مدیران منابع.

### EP-21 (بخش فاز ۲) — Self-observability

- **US-21-01** — به‌عنوان مدیر پلتفرم می‌خواهم صفحه سلامت خود پلتفرم (ingest lag، lag کانکتورها و exporter MQ، خطاهای نوشتن PostgreSQL، فضای دیسک، شاخص‌های trigger ClickHouse) را ببینم (NFR-OBS-001, 002).
- **Tasks:** T-21-01 OTel SDK داخلی در `platform-core`؛ T-21-02 metrics `connector-runtime`؛ T-21-03 صفحه self-health؛ T-21-04 هشدار مستقل از ingest اصلی (مسیر جداگانه، ADR-0017 failure domain).

---

## ۵. Epicهای فاز ۳ (سطح Feature)

### EP-11 — Transaction observability (P3, Must)

- **FR:** FR-TXN-001..008, 011؛ FR-MQ-004؛ FR-DX-004؛ FR-RUN-006؛ ADR-0019.
- **Features:** تعریف TransactionType per service؛ OutcomeMapping داده‌محور (مثلاً جدول کدهای پاسخ ISO 8583 به‌عنوان داده)؛ استخراج خلاصه دقیقه‌ای (L0) از ES/Splunk؛ L2 با کلیدهای tokenized در صورت وجود؛ throughput/success/P50/P95/P99؛ technical vs business success؛ مقایسه upstream/downstream per edge؛ retry/duplicate (Could)؛ ثبت tier؛ طبقه‌بندی backlog (growth rate، drain time، time-to-full).
- **Story‌های کلیدی:**
  - **US-11-01** — به‌عنوان مالک سرویس می‌خواهم نرخ موفقیت کسب‌وکاری را جدا از فنی ببینم تا رد به‌دلیل «موجودی ناکافی» قطعی محسوب نشود.
  - **US-11-02** — به‌عنوان مهندس middleware می‌خواهم backlog صف را «گذرا» یا «پایدار» با زمان تخلیه تخمینی ببینم.
  - **US-11-03** — به‌عنوان SRE می‌خواهم tier همبستگی (L0..L3) هر شاخص را ببینم تا میزان اطمینان را بدانم.

### EP-12 — SLO engine & error budgets (P3, Must/Should)

- **FR:** FR-CAL-004..007؛ FR-NTF-002.
- **Features:** SLO تقویم‌محور (فقط در پنجره‌های فعال)؛ error budget و burn rate؛ تشخیص expected-inactive در برابر abnormal-inactive؛ baseline time-of-week/DayType پایه؛ سرکوب اعلان.
- **US-12-01** — به‌عنوان مدیر IT می‌خواهم گزارش ماهانه SLO سرویس را با کسر پنجره‌های نگهداری و خارج از سرویس ببینم.

### EP-13 (بخش فاز ۳) — Correlation lite

- **US-13-02** — به‌عنوان اپراتور می‌خواهم پنل evidence برای سرویس VS-1 سیگنال‌های هم‌زمان در hopهای زنجیره را با confidence و disclaimer نشان دهد (`FR-COR-003` lite).

## ۶. Epicهای فاز ۴ (سطح Feature)

### EP-13 — Event model & correlation engine (P4, Must)

- **FR:** FR-COR-002..007؛ FR-INT-007؛ ADR-0012.
- **Features:** dedup و grouping زمانی؛ grouping توپولوژیک؛ رتبه‌بندی CauseHypothesis (score، confidence، explanation، counter-evidence)؛ Change awareness؛ OperatorFeedback؛ timeline حادثه؛ federated evidence query از طریق command queue با redaction.
- **US-13-03** — به‌عنوان SRE می‌خواهم در حادثه، فهرست رتبه‌بندی‌شده hopهای محتمل با شواهد موافق و مخالف ببینم تا سریع‌تر تشخیص دهم.
- **US-13-04** — به‌عنوان اپراتور می‌خواهم فرضیه را تأیید یا رد کنم تا دقت سیستم سنجیده و بهبود یابد.

### EP-14 — Runtime/DB/K8s/WAF connectors (P4, Should/Could)

- **FR:** FR-RUN-001, 003, 005؛ FR-LB-005؛ FR-ING-002, 004.
- **Features:** JMX (ترجیحاً از ابزار موجود، وگرنه connector/collector)؛ SQL Server ترجیحاً از template زبیکس، وگرنه read-only DMV؛ K8s metrics/events؛ WAF counts؛ **دریافت OTLP و `edge-collector` اختیاری** (توزیع OCB آفلاین، صف دیسکی، redaction processor) فقط برای zoneهای غیرقابل‌دسترس برای `connector-runtime`، SNMP/syslog یا ابزارگذاری آینده.
- **US-14-01** — به‌عنوان مهندس integration می‌خواهم OTLP/gRPC و OTLP/HTTP با mTLS دریافت شود (`FR-ING-002`، Should).
- **US-14-02** — (منتقل از US-06-05) به‌عنوان مهندس پلتفرم می‌خواهم `edge-collector`، در صورت استقرار، صف دیسکی ≥ 4 h در حجم per-zone داشته باشد (NFR-REL-003).
- **Tasks:** T-14-05 OTLP receiver در ماژول `ingestion`؛ T-14-06 ساخت آفلاین `edge-collector` با OCB (نیاز به mirror ماژول‌های Go) و فهرست component تأییدشده.

### EP-15 — Synthetic monitoring (P4, Should)

- **FR:** FR-DX-001..003.
- **Features:** API check مجاز غیرمالی؛ journey چندمرحله‌ای (Could)؛ guardrail (allowlist endpoint، حساب آزمون، سقف نرخ، kill switch، ممنوعیت عملیات مالی) [باز Q-20].

### EP-17 — Notifications & ITSM (P4, Should)

- **FR:** FR-NTF-001؛ FR-INT-005.
- **Features:** اعلان **فقط در سطح Incident سرویس و از کانال‌های موجود** (Zabbix actions، ITSM، email، درگاه SMS موجود) [باز Q-14]؛ حداکثر یک اعلان به ازای Incident با به‌روزرسانی روی همان مورد؛ پلتفرم منبع هشدار جدید نیست؛ webhook ورودی.

## ۷. Epicهای فاز ۵ و ۶

### EP-16 — Anomaly detection & forecasting (P5)

- **FR:** FR-ANO-002..008؛ FR-CAL-007؛ FR-TXN-011؛ FR-MQ-006؛ ADR-0013.
- **Features:** baseline فصلی per DayType؛ robust z/MAD، EWMA، STL residual؛ قواعد چندمتغیره وابستگی‌محور؛ forecast time-to-full و SLO burn؛ harness ارزیابی (precision/recall/alert budget)؛ drift؛ توضیح هر ناهنجاری.
- **US-16-01** — به‌عنوان مسئول analytics می‌خواهم هیچ detector بدون گزارش ارزیابی روی داده برچسب‌خورده فعال نشود.

### EP-18 — HA/DR & resilience (P6, Must)

- **NFR:** NFR-AVL-001..003؛ ADR-0017 [باز Q-05].
- **Features:** چند نود stateless برای `platform-core`؛ Patroni؛ ClickHouse replication + Keeper **فقط در صورت trigger**؛ سایت DR active-passive؛ drill BM-09.

### EP-19 — Performance & capacity validation (P2→P6)

- **NFR:** NFR-PRF-*، NFR-SCL-*. **Features:** BM-02، BM-04..BM-06، BM-08..BM-10 (BM-01/03/07 فقط در صورت trigger)؛ آزمون soak؛ گزارش ظرفیت برای سناریوی توافق‌شده [باز Q-06].

### EP-20 — Security hardening & assessment (continuous→P6)

- **NFR:** NFR-SEC-*، NFR-PRV-*، NFR-CMP-001.
- **Features:** threat model per فاز؛ SAST/SCA/DAST در CI؛ hardening baseline؛ pentest مستقل؛ ممیزی redaction دوره‌ای؛ نگاشت انطباق [باز Q-10].

### EP-21 — Platform self-observability (P2→P6)

- **Features (P6):** داشبورد عملیات پلتفرم، هشدارهای پلتفرم به کانال on-call [باز Q-19]، SLO خود پلتفرم.

### EP-24 — Direct instrumentation, L3 & RUM (P7، اختیاری)

- **FR:** FR-TXN-009، FR-RUN-002، FR-DX-005.
- **گیت:** G5 — مورد کسب‌وکاری مستند به ازای هر برنامه که نشان دهد L0/L2 کافی نیست.
- **Features:** OTel SDK/agent برای برنامه‌های منتخب؛ دریافت trace (نیازمند FR-ING-002 و `edge-collector` از EP-14)؛ همبستگی L3؛ `.NET` runtime؛ RUM وب/PWA/Android با ارزیابی ریسک داده.
- **US-24-01** — به‌عنوان SRE می‌خواهم برای سرویسی که گیت G5 را گذرانده، traceهای W3C را ببینم تا hop خراب با دقت L3 تعیین شود.

## ۸. ماتریس ردیابی خلاصه (Epic ↔ FR)

| Epic | FR اصلی |
|---|---|
| EP-23 | (اعتبارسنجی) TXN-003/005، MQ-002/004، COR-004، INT-008، CAL-008 |
| EP-02 | ADM-001..003 |
| EP-03 | CAT-001..012، UI-003 |
| EP-04 | CAL-001..003، CAL-008 |
| EP-06 | ING-005..009، TXN-010 |
| EP-07 | ING-001, 003, 010, 011، ADM-004 |
| EP-08 | INT-001..004, 006, 008، RUN-004 |
| EP-09 | MQ-001..003, 005، LB-001, 002, 004 |
| EP-10 | HLT-001..003، UI-002، ANO-001 |
| EP-11 | TXN-001..008, 011، MQ-004، DX-004، RUN-006 |
| EP-12 | CAL-004..007، NTF-002 |
| EP-13 | COR-001..007، INT-007 |
| EP-14 | RUN-001, 003, 005، LB-003, 005..007، ING-002, 004 |
| EP-15 | DX-001..003 |
| EP-16 | ANO-002..008 |
| EP-17 | NTF-001، INT-005 |
| EP-21 | ADM-006 |
| EP-24 | TXN-009، RUN-002، DX-005 |

## نکته برای بازبینی

- Epic فاز ۷ که در نسخه 0.1 به‌عنوان «EP-23» پیشنهاد شده بود، اکنون **EP-24** است؛ EP-23 به Evidence PoC (فاز 0.5) اختصاص یافت.
- FR-ING-003 از EP-06 به EP-07 منتقل شد چون scrape exporterها در P2 توسط `connector-runtime` انجام می‌شود.
- Storyهای ClickHouse و `edge-collector` حذف نشدند بلکه به P4 (EP-14) یا حالت «فقط در صورت trigger» منتقل شدند تا ردیابی حفظ شود.
- `FR-CAT-010` (version & deployment change records) در spine برای P3 آمده ولی Epic صریح ندارد؛ در اینجا به EP-11/EP-13 (Change awareness) مرتبط شده است. FR-UI-004 به EP-11/EP-10 تعلق دارد.
- نام‌گذاری نقش‌های RBAC در US-02-02 پیشنهادی است و باید در NFR/ADR-0015 هماهنگ شود.
