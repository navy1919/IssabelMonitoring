# Backlog پیاده‌سازی — BSOIP

| فیلد | مقدار |
|---|---|
| Document ID | `RM-002` |
| Version | `0.1` |
| Status | `Draft – Phase 0 – awaiting approval` |
| Date | `2026-10-11` (۱۴۰۵/۰۷/۱۹) |
| Owner | `Architecture` |
| Related documents | [master-plan.md](master-plan.md)، [quality-and-validation-strategy.md](../testing/quality-and-validation-strategy.md)، [deployment-and-runbooks.md](../operations/deployment-and-runbooks.md)، [open-questions-and-assumptions.md](../architecture/open-questions-and-assumptions.md)، [ADR index](../architecture/architecture-decision-records/README.md)، [functional-requirements.md](../requirements/functional-requirements.md)، [non-functional-requirements.md](../requirements/non-functional-requirements.md)، [product-charter.md](../vision/product-charter.md) |

## ۱. راهنما

- Epicها EP-01..EP-22 مطابق [master-plan.md](master-plan.md). شناسه Story: `US-<EP#>-NN`؛ شناسه Task: `T-<EP#>-NN`.
- اولویت MoSCoW (Must/Should/Could) و فاز مطابق شناسه‌های FR.
- Epicهای فاز ۱ و ۲ با جزئیات کافی برای شروع کار نوشته شده‌اند؛ فازهای ۳ به بعد در سطح feature و چند Story کلیدی، و در ابتدای هر فاز ریزتر می‌شوند.
- **هیچ Task پیش از M0 آغاز نمی‌شود** [واقعیت] (F-02). نخستین Task پیاده‌سازی **T-01-01** است و فقط پس از رفع انسداد ADR-0003 [باز Q-01].
- نقش‌ها در Storyها: اپراتور NOC، مهندس SRE/پشتیبانی برنامه، مالک سرویس، تیم middleware/integration، SOC، مدیریت IT، مدیر پلتفرم.

### ۱.۱ Definition of Ready (DoR) برای هر Story

معیار پذیرش قابل آزمون دارد؛ FR/NFR مرتبط مشخص است؛ وابستگی به Q باز ندارد یا پیش‌فرض آن پذیرفته شده؛ داده آزمون مصنوعی موجود یا قابل تولید است.

### ۱.۲ Definition of Done (DoD) مشترک

بازبینی کد؛ آزمون unit/integration/contract مرتبط سبز در CI آفلاین؛ ArchUnit سبز؛ SBOM و اسکن وابستگی بدون Critical حل‌نشده؛ برچسب‌های UI فارسی؛ OpenAPI/JSON Schema به‌روز؛ رویداد audit برای عملیات تغییر‌دهنده؛ مستند کاربر/عملیات به‌روز.

## ۲. نمای کلی Epicها

| Epic | عنوان | فاز | اولویت | وابستگی |
|---|---|---|---|---|
| EP-01 | Engineering foundation | P1 | Must | M0، ADR-0003 |
| EP-02 | Identity, access & audit | P1 | Must | EP-01، ADR-0015 |
| EP-03 | Service catalog & topology | P1–P2 | Must | EP-01، EP-02 |
| EP-04 | Business calendar & maintenance windows | P1 | Must | EP-01 |
| EP-05 | Offline packaging & deployment | P1→P6 | Must | EP-01، ADR-0016 |
| EP-06 | Ingestion pipeline & canonical schemas | P2 | Must | EP-03، ADR-0006، ADR-0008 |
| EP-07 | Connector SDK & connector-runtime | P2 | Must | EP-06 |
| EP-08 | Existing-platform connectors | P2 | Must | EP-07، EP-22 |
| EP-09 | Middleware & LB connectors | P2 | Must/Should | EP-07، EP-22 |
| EP-10 | Health model & dashboards | P2 | Must | EP-03، EP-06 |
| EP-11 | Transaction observability | P3 | Must | EP-08، EXP-01 |
| EP-12 | SLO engine & error budgets | P3 | Must | EP-04، EP-11 |
| EP-13 | Event model & correlation engine | P2→P4 | Must | EP-06، EP-03 |
| EP-14 | Runtime/DB/K8s/WAF connectors | P4 | Should | EP-07 |
| EP-15 | Synthetic monitoring | P4 | Should | EP-07، Q-20 |
| EP-16 | Anomaly detection & forecasting | P5 | Must/Should | EP-11، EP-13 |
| EP-17 | Notifications & ITSM | P4 | Should | EP-13، Q-14 |
| EP-18 | HA/DR & resilience | P6 | Must | Q-05 |
| EP-19 | Performance & capacity validation | P2→P6 | Must | EP-06 |
| EP-20 | Security hardening & assessment | continuous→P6 | Must | — |
| EP-21 | Platform self-observability | P2→P6 | Must | EP-06 |
| EP-22 | Data discovery & source onboarding | P1→P2 | Must | M0 |

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
| **T-01-01** | **ایجاد اسکلت مخزن `platform-core` به‌صورت modular monolith (Gradle multi-module یا Maven)، مرزهای ماژول + آزمون ArchUnit، ساخت آفلاین با mirror داخلی، pipeline CI (lint/test/SBOM)، بدون هیچ قابلیت کسب‌وکاری** — فقط پس از رفع انسداد ADR-0003 | مخزن قابل ساخت، CI سبز |
| T-01-02 | قواعد ArchUnit: فقط package `api` هر ماژول قابل دسترسی؛ ممنوعیت چرخه | کتابخانه قواعد |
| T-01-03 | پیکربندی role-based startup (`api`/`ingest`/`analytics`) | پیکربندی + آزمون |
| T-01-04 | پیکربندی mirror داخلی (Maven/Gradle، npm، OCI) و مستند آن [باز Q-09] | مستند + تنظیمات |
| T-01-05 | مرحله CI: SBOM CycloneDX، checksum، امضا (ابزار امضا وابسته به ADR-0016) | artifact امضاشده |
| T-01-06 | اسکلت `web-ui` (React + TypeScript، ADR-0014)، lint، آزمون واحد | اسکلت UI |
| T-01-07 | قالب‌های ADR، PR template، استاندارد پیام commit، lint Markdown و بررسی پیوند مستندات | قالب‌ها |
| T-01-08 | image پایه‌های آفلاین PostgreSQL/ClickHouse برای Testcontainers در registry داخلی | images |

- **وابستگی:** M0؛ ADR-0003 [باز Q-01]؛ mirror [باز Q-09].

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
- **FR/NFR:** FR-CAL-001..003 (P1)، زیرساخت برای FR-CAL-004..007 (P3)؛ NFR-USA-001.
- **Features:** BusinessCalendar با DayType؛ import تعطیلات سالانه به‌صورت داده (تعطیلات قمری محاسبه نمی‌شود)؛ ServiceWindow تکرارشونده (مثلاً چرخه‌های ساتنا — پیکربندی، نه hardcode) [باز Q-15]؛ MaintenanceWindow برنامه‌ریزی‌شده/غیربرنامه‌ریزی‌شده با معنای suppression.

**User Stories**

- **US-04-01** — به‌عنوان مدیر پلتفرم می‌خواهم فایل تعطیلات سال آینده را import کنم تا تقویم به‌روز باشد.
  - AC: فرمت YAML/CSV با تاریخ جلالی و نوع روز؛ اعتبارسنجی تاریخ؛ گزارش تفاوت با سال قبل؛ هشدار اگر تقویم سال آینده تا ۳۰ روز مانده به پایان سال بارگذاری نشده باشد (R-14).
- **US-04-02** — به‌عنوان مالک سرویس می‌خواهم پنجره پردازش سرویس (مثلاً ساعات کاری روزهای کاری) تعریف کنم تا خارج از آن «قطعی» گزارش نشود.
  - AC: قاعده تکرار بر اساس DayType و بازه ساعت به وقت Asia/Tehran؛ ذخیره قاعده نه نمونه‌ها؛ آزمون مرز نیمه‌شب.
- **US-04-03** — به‌عنوان اپراتور NOC می‌خواهم پنجره نگهداری برای یک سرویس/جزء ثبت کنم تا هشدارهای آن دوره سرکوب و علامت‌گذاری شود.
  - AC: suppression هشدارها را حذف نمی‌کند بلکه علامت `MAINTENANCE` می‌زند؛ ثبت در audit.
- **US-04-04** — به‌عنوان کاربر می‌خواهم همه تاریخ‌ها در UI جلالی و قابل تغییر به میلادی باشند.

**Tasks کلیدی:** T-04-01 مدل داده تقویم و DayType؛ T-04-02 کتابخانه تبدیل جلالی (با آزمون سال کبیسه، مقایسه با جدول مرجع)؛ T-04-03 موتور ارزیابی ServiceWindow (`isExpectedActive(service, instant)`)؛ T-04-04 import تعطیلات؛ T-04-05 UI تقویم و پنجره نگهداری.

- **وابستگی:** EP-01؛ داده تقویم [باز Q-15].

### EP-05 — Offline packaging & deployment

- **هدف:** نصب و ارتقای کاملاً آفلاین و قابل بازبینی.
- **فاز:** P1 (bundle v1) → P2 (v2 + collector/ClickHouse) → P6 (نهایی، Helm در صورت نیاز) · **اولویت:** Must
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
- **فاز:** P1→P2 (مسیر موازی) · **اولویت:** Must
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

**Tasks کلیدی:** T-22-01 قالب inventory؛ T-22-02 بسته درخواست دسترسی؛ T-22-03 پروتکل EXP-01 (با تأیید امنیت)؛ T-22-04 پروتکل EXP-02؛ T-22-05 پروتکل EXP-04؛ T-22-06 پروتکل EXP-05؛ T-22-07 ضبط و پاک‌سازی fixtureها (redaction پیش از ورود به مخزن).

- **وابستگی:** M0؛ تأییدهای امنیتی بانک.

---

## ۴. Epicهای فاز ۲ (جزئیات کامل)

### EP-06 — Ingestion pipeline & canonical schemas

- **هدف:** مسیر واحد و قابل اعتماد ورود داده: normalize، UTC، redact، dedup، late data، backpressure، نوشتن idempotent در ClickHouse.
- **فاز:** P2 · **اولویت:** Must
- **FR/NFR:** FR-ING-002, 003, 005..009؛ FR-TXN-010؛ NFR-REL-001..003؛ NFR-PRV-001..003؛ NFR-PRF-001؛ ADR-0005، ADR-0006، ADR-0008، ADR-0010، ADR-0018.
- **Features:** دریافت OTLP (gRPC/HTTP، mTLS)؛ batch API `/internal/v1/ingest/events`؛ schema رویداد با envelope شبیه CloudEvents و JSON Schema نسخه‌دار؛ attributeهای `bank.*`؛ redaction در ingestion؛ HMAC tokenization؛ dedup با کلید idempotency؛ ثبت `observed_at`, `ingested_at`, `source_clock_skew_estimate`؛ جداول ClickHouse و rollup؛ distribution `edge-collector` با OCB.

**User Stories**

- **US-06-01** — به‌عنوان مهندس integration می‌خواهم داده را با OTLP و batch API ارسال کنم و پاسخ قطعی (پذیرفته/رد/throttle) بگیرم تا retry درست انجام شود.
  - AC: HTTP 429/`RESOURCE_EXHAUSTED` همراه Retry-After هنگام فشار؛ رد schema نامعتبر با خطای قابل خواندن؛ batch تکراری بدون رکورد تکراری.
- **US-06-02** — به‌عنوان مسئول امنیت می‌خواهم هر رکورد پیش از ذخیره redact شود تا PAN/کد ملی/شبا/موبایل هرگز ذخیره نشود.
  - AC: corpus مصنوعی (quality strategy §۷.۵) صفر نشت؛ شمارنده redaction per source به‌عنوان metric؛ کلیدهای همبستگی با HMAC-SHA256 و کلید per environment از SecretProvider.
- **US-06-03** — به‌عنوان SRE می‌خواهم داده دیررس با `observed_at` درست ثبت شود و rollupها اصلاح شوند تا گزارش‌ها دقیق بمانند.
  - AC: داده تا X ساعت دیرتر (پیکربندی، پیش‌فرض ۲۴ h [فرض]) پذیرفته و rollup مربوطه بازمحاسبه می‌شود؛ دیرتر از آن رد و شمارش می‌شود.
- **US-06-04** — به‌عنوان اپراتور می‌خواهم شکاف داده به‌صورت صریح دیده شود تا داده گم‌شده با «صفر» اشتباه نشود (NFR-REL-002).
- **US-06-05** — به‌عنوان مهندس پلتفرم می‌خواهم `edge-collector` با صف دیسکی ≥ 4 h در حجم Medium per-zone کار کند (NFR-REL-003).

**Tasks کلیدی**

| ID | Task |
|---|---|
| T-06-01 | JSON Schema رویداد مشترک v1 و envelope؛ قواعد سازگاری semver |
| T-06-02 | ماژول `ingestion`: OTLP receiver (role `ingest`) |
| T-06-03 | batch API داخلی با mTLS و احراز هویت client cert per connector instance |
| T-06-04 | کتابخانه redaction مشترک (Java) و processor معادل در collector؛ detectorهای Luhn، کد ملی (الگوریتم checksum)، شبا (IR + 24 رقم، mod-97)، موبایل |
| T-06-05 | HMAC tokenizer با چرخش کلید (key id در رکورد) |
| T-06-06 | dedup/idempotency key و نوشتن batch در ClickHouse (ReplacingMergeTree یا insert deduplication؛ با BM-01 تعیین شود) |
| T-06-07 | جداول ClickHouse: samples، rollup 1m/1h، events؛ TTL مطابق ADR-0018 |
| T-06-08 | ساخت آفلاین `edge-collector` با OCB و فهرست component تأییدشده |
| T-06-09 | backpressure و metrics صف |
| T-06-10 | BM-01 و BM-07 (با EP-19) |

- **وابستگی:** EP-03 (شناسه موجودیت)، ADR-0008 (Provisional).

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

**Tasks کلیدی:** T-07-01 تعریف SPI و مدل Capability؛ T-07-02 runtime (scheduler، checkpoint، retry با jitter، circuit breaker)؛ T-07-03 کانال پیکربندی و command long-poll؛ T-07-04 SecretProvider با پیاده‌سازی فایل رمز‌شده؛ T-07-05 rate limiter/budget؛ T-07-06 harness شبیه‌سازی + replay fixture؛ T-07-07 UI diagnostics؛ T-07-08 کانکتور file import.

### EP-08 — Existing-platform connectors

- **هدف:** استفاده از داده موجود در Zabbix، Elasticsearch/OpenSearch، Splunk و SolarWinds به‌صورت read-only با بار محدود (ADR-0020).
- **فاز:** P2 · **اولویت:** Must (SolarWinds: Should)
- **FR/NFR:** FR-INT-001..004, 006؛ FR-RUN-004؛ FR-LB-002 (از طریق این منابع)؛ NFR-IMP-001, 002؛ NFR-PRF-002.
- **Features:** Zabbix JSON-RPC (hosts، items، history/trends، problems)؛ ES `_search` با aggregation دقیقه‌ای؛ Splunk REST search (job با بازه محدود)؛ SolarWinds SWIS/SWQL؛ deep link؛ نگاشت به ExternalRef؛ capability per version. همه «نیازمند تأیید در نسخه نصب‌شده بانک» [باز Q-08].

**User Stories**

- **US-08-01** — به‌عنوان اپراتور NOC می‌خواهم متریک‌های میزبان‌ها و itemهای Zabbix مرتبط با سرویس VS-1 را در BSOIP ببینم تا وضعیت زیرساخت در زمینه سرویس دیده شود.
  - AC: فقط itemهای دارای ExternalRef poll می‌شوند؛ problems به رویداد مشترک تبدیل می‌شوند؛ deep link به صفحه Zabbix.
- **US-08-02** — به‌عنوان SRE می‌خواهم شمارش دقیقه‌ای لاگ‌های سرویس بر اساس فیلد outcome از ES دریافت شود تا نرخ خطا محاسبه شود.
  - AC: فقط query aggregation (نه سند خام)؛ بازه watermark-based بدون هم‌پوشانی مضاعف؛ مصرف budget گزارش می‌شود.
- **US-08-03** — همان برای Splunk (search job با `earliest/latest` محدود، `stats count by`).
- **US-08-04** — به‌عنوان اپراتور می‌خواهم node/interface status از SolarWinds دریافت شود (Should).
- **US-08-05** — به‌عنوان مدیر پلتفرم می‌خواهم capability نسخه نصب‌شده هر منبع در اتصال اول تأیید شود تا قابلیت‌های پشتیبانی‌نشده خاموش شوند (R-12).

**Tasks کلیدی:** T-08-01 کانکتور Zabbix + fixtureهای نسخه‌ها؛ T-08-02 کانکتور ES/OpenSearch؛ T-08-03 کانکتور Splunk؛ T-08-04 کانکتور SolarWinds؛ T-08-05 ساخت‌گر deep link؛ T-08-06 BM-04 و BM-05 با مدیران منابع.

- **وابستگی:** EP-07، EP-22 (دسترسی و نسخه).

### EP-09 — Middleware & LB connectors

- **هدف:** متریک‌های صف و LB برای زنجیره VS-1 از هر مسیر در دسترس.
- **فاز:** P2 · **اولویت:** IBM MQ Must؛ RabbitMQ/F5/HAProxy/NGINX Should
- **FR/NFR:** FR-MQ-001..003, 005؛ FR-LB-001, 002, 004؛ NFR-SEC-003؛ NFR-IMP-001.
- **Features:** IBM MQ read-only (PCF: queue depth، max depth، enq/deq، oldest message age، channel status) یا از Zabbix؛ RabbitMQ management API/Prometheus plugin (از طریق collector)؛ F5 iControl REST read-only؛ HAProxy stats؛ NGINX stub_status/Plus API؛ انقضای گواهی TLS؛ DLQ indicators؛ مدل VirtualServer/Pool/PoolMember.

**User Stories**

- **US-09-01** — به‌عنوان مهندس middleware می‌خواهم depth و سن قدیمی‌ترین پیام صف‌های زنجیره VS-1 را هر ۱۰–۶۰ ثانیه ببینم [فرض A-04].
  - AC: polling با حداقل مجوز تأییدشده در EXP-04؛ اثر polling با BM-06 اندازه‌گیری شده.
- **US-09-02** — به‌عنوان اپراتور می‌خواهم وضعیت pool memberهای LB و توزیع درخواست‌ها را ببینم.
- **US-09-03** — به‌عنوان SRE می‌خواهم هشدار انقضای گواهی TLS روی virtual serverها را X روز قبل ببینم (`FR-LB-004`).
- **US-09-04** — به‌عنوان مهندس middleware می‌خواهم شاخص DLQ/پیام‌های ناموفق را ببینم (`FR-MQ-005`).

**Tasks کلیدی:** T-09-01 کانکتور IBM MQ (JMS/PCF، ADR-0003)؛ T-09-02 مسیر جایگزین MQ از Zabbix؛ T-09-03 RabbitMQ؛ T-09-04 F5؛ T-09-05 HAProxy/NGINX از طریق collector یا connector؛ T-09-06 BM-06.

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

**Tasks کلیدی:** T-10-01 DSL/مدل قاعده سلامت (اعلانی، نسخه‌دار)؛ T-10-02 ارزیاب micro-batch در role `analytics` (30–60 s)؛ T-10-03 freshness tracker per ExternalRef؛ T-10-04 propagation روی توپولوژی؛ T-10-05 داشبورد و SSE؛ T-10-06 ذخیره تاریخچه سلامت در ClickHouse.

### EP-13 (بخش فاز ۲) — Common event model

- **US-13-01** — به‌عنوان SRE می‌خواهم problems/alerts از Zabbix، SolarWinds و هشدارهای Splunk در یک مدل رخداد مشترک با شدت نرمال‌شده ذخیره شوند (`FR-COR-001`).
  - AC: نگاشت شدت هر منبع به‌صورت داده؛ شناسه منبع و deep link حفظ می‌شود؛ رویداد باز/بسته شدن.
- **Tasks:** T-13-01 schema رویداد؛ T-13-02 نگاشت per source؛ T-13-03 جدول events در ClickHouse و وضعیت جاری در PostgreSQL.

### EP-19 (بخش فاز ۲) — Performance baselines

- **US-19-01** — به‌عنوان معمار می‌خواهم BM-01 و BM-07 روی سخت‌افزار نماینده اجرا شود تا ADR-0008/ADR-0010 تأیید یا بازنگری شود.
- **Tasks:** T-19-01 مولد بار مصنوعی (متریک، رویداد، خلاصه تراکنش) بر اساس سناریوهای Low/Medium/High؛ T-19-02 اجرای BM-01/02/07 و گزارش؛ T-19-03 BM-10 (UI).

### EP-21 (بخش فاز ۲) — Self-observability

- **US-21-01** — به‌عنوان مدیر پلتفرم می‌خواهم صفحه سلامت خود پلتفرم (ingest lag، صف collector، lag کانکتورها، خطاهای نوشتن ClickHouse، فضای دیسک) را ببینم (NFR-OBS-001, 002).
- **Tasks:** T-21-01 OTel SDK داخلی در `platform-core`؛ T-21-02 metrics collector و connector-runtime؛ T-21-03 صفحه self-health؛ T-21-04 هشدار مستقل از ingest اصلی (مسیر جداگانه، ADR-0017 failure domain).

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

- **FR:** FR-RUN-001, 003, 005؛ FR-LB-005؛ FR-ING-004.
- **Features:** JMX (از طریق collector یا connector)؛ SQL Server read-only DMV؛ K8s metrics/events؛ WAF counts؛ SNMP/syslog از collector.

### EP-15 — Synthetic monitoring (P4, Should)

- **FR:** FR-DX-001..003.
- **Features:** API check مجاز غیرمالی؛ journey چندمرحله‌ای (Could)؛ guardrail (allowlist endpoint، حساب آزمون، سقف نرخ، kill switch، ممنوعیت عملیات مالی) [باز Q-20].

### EP-17 — Notifications & ITSM (P4, Should)

- **FR:** FR-NTF-001؛ FR-INT-005.
- **Features:** email، SMS gateway، webhook ITSM [باز Q-14]؛ قواعد مسیریابی و dedup اعلان؛ webhook ورودی.

## ۷. Epicهای فاز ۵ و ۶

### EP-16 — Anomaly detection & forecasting (P5)

- **FR:** FR-ANO-002..008؛ FR-CAL-007؛ FR-TXN-011؛ FR-MQ-006؛ ADR-0013.
- **Features:** baseline فصلی per DayType؛ robust z/MAD، EWMA، STL residual؛ قواعد چندمتغیره وابستگی‌محور؛ forecast time-to-full و SLO burn؛ harness ارزیابی (precision/recall/alert budget)؛ drift؛ توضیح هر ناهنجاری.
- **US-16-01** — به‌عنوان مسئول analytics می‌خواهم هیچ detector بدون گزارش ارزیابی روی داده برچسب‌خورده فعال نشود.

### EP-18 — HA/DR & resilience (P6, Must)

- **NFR:** NFR-AVL-001..003؛ ADR-0017 [باز Q-05].
- **Features:** چند نود stateless برای `platform-core`؛ Patroni؛ ClickHouse replication + Keeper؛ سایت DR active-passive؛ drill BM-09.

### EP-19 — Performance & capacity validation (P2→P6)

- **NFR:** NFR-PRF-*، NFR-SCL-*. **Features:** BM-01..BM-10؛ آزمون soak؛ گزارش ظرفیت برای سناریوی توافق‌شده [باز Q-06].

### EP-20 — Security hardening & assessment (continuous→P6)

- **NFR:** NFR-SEC-*، NFR-PRV-*، NFR-CMP-001.
- **Features:** threat model per فاز؛ SAST/SCA/DAST در CI؛ hardening baseline؛ pentest مستقل؛ ممیزی redaction دوره‌ای؛ نگاشت انطباق [باز Q-10].

### EP-21 — Platform self-observability (P2→P6)

- **Features (P6):** داشبورد عملیات پلتفرم، هشدارهای پلتفرم به کانال on-call [باز Q-19]، SLO خود پلتفرم.

## ۸. ماتریس ردیابی خلاصه (Epic ↔ FR)

| Epic | FR اصلی |
|---|---|
| EP-02 | ADM-001..003 |
| EP-03 | CAT-001..012، UI-003 |
| EP-04 | CAL-001..003 |
| EP-06 | ING-002, 003, 005..009، TXN-010 |
| EP-07 | ING-001, 010, 011، ADM-004 |
| EP-08 | INT-001..004, 006، RUN-004 |
| EP-09 | MQ-001..003, 005، LB-001, 002, 004 |
| EP-10 | HLT-001..003، UI-002، ANO-001 |
| EP-11 | TXN-001..008, 011، MQ-004، DX-004، RUN-006 |
| EP-12 | CAL-004..007، NTF-002 |
| EP-13 | COR-001..007، INT-007 |
| EP-14 | RUN-001, 003, 005، LB-003, 005..007، ING-004 |
| EP-15 | DX-001..003 |
| EP-16 | ANO-002..008 |
| EP-17 | NTF-001، INT-005 |
| EP-21 | ADM-006 |
| فاز ۷ (بدون Epic در spine) | TXN-009، RUN-002، DX-005 |

## نکته برای بازبینی

- spine برای قابلیت‌های فاز ۷ (FR-TXN-009، FR-RUN-002، FR-DX-005) Epic تعریف نکرده است؛ پیشنهاد می‌شود در صورت فعال شدن فاز ۷ یک Epic جدید (مثلاً EP-23) افزوده شود.
- `FR-CAT-010` (version & deployment change records) در spine برای P3 آمده ولی Epic صریح ندارد؛ در اینجا به EP-11/EP-13 (Change awareness) مرتبط شده است. FR-UI-004 به EP-11/EP-10 تعلق دارد.
- نام‌گذاری نقش‌های RBAC در US-02-02 پیشنهادی است و باید در NFR/ADR-0015 هماهنگ شود.
