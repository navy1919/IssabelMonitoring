# نیازمندی‌های کارکردی (Functional Requirements)

| فیلد | مقدار |
|---|---|
| Document ID | `DOC-REQ-001` |
| Version | `0.1` |
| Status | `Draft – Phase 0 – awaiting approval` |
| Date | `2026-10-11` (۱۴۰۵/۰۷/۱۹ – `1405/07/19`) |
| Owner | `Architecture` |
| Related documents | [product-charter.md](../vision/product-charter.md)، [domain-model.md](../vision/domain-model.md)، [non-functional-requirements.md](non-functional-requirements.md)، ADRها در [architecture-decision-records](../architecture/architecture-decision-records/) |

> **قرارداد برچسب‌گذاری:** **[واقعیت]**، **[فرض A-xx]**، **[فرضیه]**، **[توصیه]**، **[باز Q-xx]** — مطابق [منشور محصول](../vision/product-charter.md). همهٔ نیازمندی‌های این سند در وضعیت پیش‌نویس‌اند و خود از جنس **[توصیه]** هستند تا زمان تأیید M0.

---

## ۱. راهنمای خواندن

- **شناسه:** `FR-<AREA>-NNN`؛ ثابت و غیرقابل‌استفادهٔ مجدد.
- **اولویت (MoSCoW):** Must / Should / Could.
- **فاز:** شمارهٔ فاز مطابق [منشور](../vision/product-charter.md) (P1..P7). نماد `P2→P4` یعنی شروع در P2 و تکمیل در P4.
- **VS-1:** `VS-1 (core)` = بخشی از قابلیت‌هایی که دموی VS-1 نشان می‌دهد؛ `VS-1 (prereq)` = پیش‌نیاز دموی VS-1؛ `—` = غیرمرتبط.
- **معیار پذیرش:** آزمون‌پذیر؛ به شکل Given/When/Then یا گزاره‌های قابل‌سنجش.
- **وابستگی‌ها:** شناسه‌های FR/NFR که باید پیش از این نیازمندی یا هم‌زمان با آن تحقق یابند.
- مفاهیم و enumها (`TxnOutcome`، `HealthState`، `Dependency` و …) در [domain-model.md](../vision/domain-model.md) تعریف شده‌اند.

## ۲. جدول خلاصه

| ID | عنوان | اولویت | فاز | VS-1 |
|---|---|---|---|---|
| FR-CAT-001 | Business services | Must | P1 | prereq |
| FR-CAT-002 | Technical services & components linked to business services | Must | P1 | prereq |
| FR-CAT-003 | Custom service types, bank terminology, custom attributes | Must | P1 | — |
| FR-CAT-004 | Ownership, criticality, environment, status | Must | P1 | prereq |
| FR-CAT-005 | Environments & sites incl. DR mapping | Must | P1 | — |
| FR-CAT-006 | Manual dependencies with direction/protocol/trust boundary | Must | P1 | prereq |
| FR-CAT-007 | Discovered relationships with provenance/confidence/approval | Should | P2→P4 | — |
| FR-CAT-008 | Dependency graph visualization & impact traversal | Must | P2 | core |
| FR-CAT-009 | ExternalRef mapping to source identities | Must | P2 | prereq |
| FR-CAT-010 | Version & deployment change records | Should | P3 | — |
| FR-CAT-011 | Bulk import/export YAML/CSV | Must | P1 | prereq |
| FR-CAT-012 | Catalog change history | Must | P1 | — |
| FR-TXN-001 | Transaction type definitions | Must | P3 | prereq |
| FR-TXN-002 | Canonical outcome classification `TxnOutcome` | Must | P3 | prereq |
| FR-TXN-003 | Log/search-derived per-minute summaries | Must | P3 | **core** |
| FR-TXN-004 | Throughput, success/failure rates, P50/P95/P99 | Must | P3 | core |
| FR-TXN-005 | Technical vs business success | Must | P3 | **core** |
| FR-TXN-006 | Upstream vs downstream count comparison per edge | Should | P3 | core |
| FR-TXN-007 | Retry/duplicate detection where keys exist | Could | P3 | — |
| FR-TXN-008 | Correlation tier recording L0–L3 | Must | P3 | core |
| FR-TXN-009 | W3C trace context / OTLP traces ingestion | Could | P7 | — |
| FR-TXN-010 | Redaction & tokenization of correlation keys | Must | P2 | prereq |
| FR-TXN-011 | Volume vs expected baseline | Must | P3 basic, P5 advanced | core |
| FR-LB-001 | LB/proxy model virtual server/pool/member | Must | P2 | prereq |
| FR-LB-002 | LB metrics via available source with capability declaration | Must | P2 | core |
| FR-LB-003 | Pool capacity loss & uneven distribution | Should | P4 | — |
| FR-LB-004 | TLS certificate expiry | Should | P2 | — |
| FR-LB-005 | WAF block/challenge counts | Could | P4 | — |
| FR-LB-006 | Incoming vs processed discrepancy | Should | P4 | — |
| FR-LB-007 | Failure-domain classification rules | Should | P4 | — |
| FR-MQ-001 | Messaging model | Must | P2 | prereq |
| FR-MQ-002 | IBM MQ read-only metrics | Must | P2 | **core** |
| FR-MQ-003 | RabbitMQ via management API/Prometheus plugin | Should | P2 | — |
| FR-MQ-004 | Backlog classification transient vs sustained | Must | P3 | **core** |
| FR-MQ-005 | DLQ/failed message indicators | Should | P2 | — |
| FR-MQ-006 | Consumer throughput degradation | Should | P5 | — |
| FR-RUN-001 | JVM via JMX | Should | P4 | — |
| FR-RUN-002 | .NET runtime | Could | P7 | — |
| FR-RUN-003 | DB health, SQL Server first | Should | P4 | — |
| FR-RUN-004 | OS/host metrics via Zabbix/SolarWinds | Must | P2 | core |
| FR-RUN-005 | Container/K8s | Could | P4 | — |
| FR-RUN-006 | App-specific metrics & structured log mapping | Should | P3 | — |
| FR-DX-001 | Synthetic API checks (authorized, non-financial) | Should | P4 | — |
| FR-DX-002 | Synthetic multi-step journeys | Could | P4 | — |
| FR-DX-003 | Synthetic safety guardrails | Must | P4 | — |
| FR-DX-004 | Login success/failure & auth latency from logs | Should | P3 | — |
| FR-DX-005 | RUM web/PWA/Android | Could | P7 | — |
| FR-CAL-001 | Business calendars Jalali + holiday import | Must | P1 | prereq |
| FR-CAL-002 | Service/processing windows | Must | P1 | prereq |
| FR-CAL-003 | Maintenance windows & suppression semantics | Must | P1 | prereq |
| FR-CAL-004 | Expected-inactive vs abnormal-inactive | Must | P3 | **core** |
| FR-CAL-005 | Calendar-aware SLO definitions | Must | P3 | core |
| FR-CAL-006 | Error budget & burn rate | Should | P3 | — |
| FR-CAL-007 | Time-of-week/day-type volume baselines | Should | P3→P5 | core |
| FR-HLT-001 | Service health with explanation (`HealthState`) | Must | P2 | **core** |
| FR-HLT-002 | Health propagation as impact, not cause | Should | P2 | core |
| FR-HLT-003 | Data freshness/source health → UNKNOWN | Must | P2 | core |
| FR-ING-001 | Connector SDK contract | Must | P2 | prereq |
| FR-ING-002 | OTLP receive | Must | P2 | prereq |
| FR-ING-003 | Prometheus scrape via edge-collector | Should | P2 | — |
| FR-ING-004 | SNMP & syslog via edge-collector | Could | P4 | — |
| FR-ING-005 | File-based offline import | Should | P2 | — |
| FR-ING-006 | Normalization, UTC timestamps, source identity | Must | P2 | prereq |
| FR-ING-007 | Dedup & idempotent writes | Must | P2 | prereq |
| FR-ING-008 | Late-arriving data | Must | P2 | prereq |
| FR-ING-009 | Buffering & backpressure | Must | P2 | prereq |
| FR-ING-010 | Connector health/diagnostics UI | Must | P2 | prereq |
| FR-ING-011 | Connector simulation/test harness | Must | P2 | prereq |
| FR-INT-001 | Zabbix connector | Must | P2 | **core** |
| FR-INT-002 | Elasticsearch/OpenSearch connector | Must | P2 | **core** |
| FR-INT-003 | Splunk connector | Must | P2 | **core** |
| FR-INT-004 | SolarWinds SWIS connector | Should | P2 | — |
| FR-INT-005 | Inbound webhooks | Should | P4 | — |
| FR-INT-006 | Deep links back to sources | Must | P2 | core |
| FR-INT-007 | Federated evidence query with redaction | Should | P4 | — |
| FR-COR-001 | Common event model | Must | P2 | prereq |
| FR-COR-002 | Dedup & time-window grouping | Must | P4 | — |
| FR-COR-003 | Topology-aware incident grouping | Must | P4 (lite P3) | core (lite) |
| FR-COR-004 | Probable-cause ranking with evidence/confidence | Must | P4 | — (evidence panel in P3) |
| FR-COR-005 | Change awareness | Should | P4 | — |
| FR-COR-006 | Operator feedback | Must | P4 | — |
| FR-COR-007 | Incident timeline | Must | P4 | — |
| FR-ANO-001 | Static thresholds | Must | P2 | prereq |
| FR-ANO-002 | Seasonal baselines | Must | P5 (basic P3) | core (basic) |
| FR-ANO-003 | Statistical detection (robust z/MAD, EWMA, STL) | Should | P5 | — |
| FR-ANO-004 | Multivariate/dependency-aware patterns | Should | P5 (one rule P3) | **core (one rule)** |
| FR-ANO-005 | Forecasts (queue time-to-full, SLO burn, saturation) | Should | P5 | — |
| FR-ANO-006 | Evaluation harness | Must | P5 | — |
| FR-ANO-007 | Drift & retraining | Should | P5 | — |
| FR-ANO-008 | Explanation for every anomaly | Must | P5 | — |
| FR-NTF-001 | Channels: email/SMS gateway/ITSM webhook | Should | P4 | — |
| FR-NTF-002 | Suppression in maintenance & expected-inactive | Must | P3 | core |
| FR-UI-001 | Persian RTL, Jalali dates, English identifiers | Must | P1 | prereq |
| FR-UI-002 | Service overview/health dashboard | Must | P2 | core |
| FR-UI-003 | Topology map | Must | P2 | core |
| FR-UI-004 | Service detail: KPIs/transactions/dependencies/evidence | Must | P3 | **core** |
| FR-ADM-001 | RBAC with scopes | Must | P1 | prereq |
| FR-ADM-002 | AD/LDAP + SSO OIDC/SAML | Must | P1 | prereq |
| FR-ADM-003 | Audit log | Must | P1 | prereq |
| FR-ADM-004 | Connector secret management | Must | P2 | prereq |
| FR-ADM-005 | Multi-bank tenancy | Could | P6+ | — |
| FR-ADM-006 | Platform self-monitoring | Must | P2→P6 | prereq |

---

## ۳. کاتالوگ و توپولوژی (CAT) — Epic EP-03

#### FR-CAT-001 — Business services
- **شرح:** ثبت، ویرایش، بایگانی و جست‌وجوی `BusinessService` با شناسهٔ پایدار، نام فارسی/انگلیسی، نوع (`ServiceType`) و توضیح.
- **اولویت/فاز:** Must / P1 · **VS-1:** prereq
- **وابستگی‌ها:** FR-ADM-001، FR-ADM-003، FR-UI-001
- **معیار پذیرش:**
  - Given کاربری با نقش `catalog-editor`، When سرویس «کارت به کارت همراه‌بانک» را ثبت می‌کند، Then سرویس با شناسهٔ پایدار ایجاد و در audit ثبت می‌شود.
  - جست‌وجو با نام فارسی، نام انگلیسی و alias نتیجه می‌دهد.
  - بایگانی سرویس، تاریخچه و ارجاعات آن را حذف نمی‌کند.

#### FR-CAT-002 — Technical services/components linked to business services
- **شرح:** تعریف `TechnicalService` و `Component` (همهٔ زیرنوع‌های مدل دامنه) و پیوند ترکیبی آن‌ها به `BusinessService`.
- **اولویت/فاز:** Must / P1 · **VS-1:** prereq
- **وابستگی‌ها:** FR-CAT-001، FR-CAT-003
- **معیار پذیرش:**
  - هر زیرنوع Component در [domain-model.md](../vision/domain-model.md) §۳.۲ قابل‌ایجاد است.
  - یک Component می‌تواند به چند TechnicalService و یک TechnicalService به چند BusinessService متصل باشد.
  - سلسله‌مراتب ترکیب (مثلاً `LoadBalancer → VirtualServer → Pool → PoolMember`) جدا از Dependency ذخیره و نمایش داده می‌شود.

#### FR-CAT-003 — Custom service types, bank terminology, custom attributes
- **شرح:** رجیستری `ServiceType` قابل‌گسترش با انواع `custom.*`، schema ویژگی‌های سفارشی نسخه‌بندی‌شده، و `TermAlias` برای اصطلاحات خاص بانک (قواعد در [domain-model.md](../vision/domain-model.md) §۶).
- **اولویت/فاز:** Must / P1 · **VS-1:** —
- **وابستگی‌ها:** FR-CAT-012، FR-ADM-003
- **معیار پذیرش:**
  - Given تعریف نوع `custom.bill-payment-gateway` با ویژگی الزامی، When مؤلفه‌ای بدون آن ویژگی ذخیره شود، Then خطای اعتبارسنجی با پیام فارسی برگردانده می‌شود.
  - ویژگی با پرچم `sensitive` در export و API برای نقش‌های فاقد مجوز mask می‌شود.
  - enumهای canonical (`TxnOutcome`، `HealthState`، …) از طریق این مکانیزم قابل‌تغییر نیستند.
  - تغییر alias بدون تغییر شناسهٔ canonical در API انجام می‌شود.

#### FR-CAT-004 — Ownership, criticality, environment, status
- **شرح:** هر سرویس/مؤلفه دارای owner (فرد/تیم)، criticality (مقیاس قابل‌پیکربندی، پیش‌فرض ۴ سطح)، Environment و وضعیت چرخهٔ عمر (planned/active/deprecated/retired).
- **اولویت/فاز:** Must / P1 · **VS-1:** prereq
- **وابستگی‌ها:** FR-CAT-001، FR-ADM-002
- **معیار پذیرش:**
  - سرویس بدون owner و criticality در وضعیت `active` قابل‌ذخیره نیست.
  - فیلتر داشبوردها و RBAC scope بر اساس Environment کار می‌کند.

#### FR-CAT-005 — Environments & sites incl. DR mapping
- **شرح:** تعریف Environment (`PROD`، `DR`، `UAT`، `TEST` و موارد سفارشی)، Site، Zone و نگاشت صریح مؤلفهٔ PROD به معادل DR.
- **اولویت/فاز:** Must / P1 · **VS-1:** —
- **وابستگی‌ها:** FR-CAT-002
- **معیار پذیرش:**
  - برای هر مؤلفهٔ PROD می‌توان معادل DR را ثبت و نمایش داد.
  - گزارش «مؤلفه‌های PROD با criticality بالا بدون معادل DR» تولید می‌شود.

#### FR-CAT-006 — Manual dependencies with direction/protocol/trust boundary
- **شرح:** ثبت دستی `Dependency` با from/to، direction، protocol، criticality، TrustBoundary؛ provenance=`MANUAL`، confidence=1.0، وضعیت `APPROVED`.
- **اولویت/فاز:** Must / P1 · **VS-1:** prereq
- **وابستگی‌ها:** FR-CAT-002، FR-CAT-005
- **معیار پذیرش:**
  - زنجیرهٔ کامل VS-1 ([domain-model.md](../vision/domain-model.md) §۸.۱) قابل‌ثبت است.
  - ثبت حلقهٔ وابستگی (cycle) مجاز است ولی با هشدار نمایش داده می‌شود.
  - یال‌هایی که بین Zoneها عبور می‌کنند بدون TrustBoundary قابل‌ذخیره نیستند.

#### FR-CAT-007 — Discovered relationships with provenance/confidence/approval
- **شرح:** پیشنهاد خودکار یال‌ها از منابع (LB pool، تعاریف channel/queue در MQ، نقشه‌های Zabbix/SolarWinds، هم‌رخدادی در لاگ؛ trace در P7) با provenance=`DISCOVERED`، `source`، `confidence`، `firstSeen`/`lastSeen` و گردش کار `PROPOSED → APPROVED | REJECTED` ([ADR-0011](../architecture/architecture-decision-records/ADR-0011-topology-model-and-dependency-discovery.md)).
- **اولویت/فاز:** Should / P2→P4 · **VS-1:** —
- **وابستگی‌ها:** FR-CAT-006، FR-CAT-009، FR-INT-001، FR-MQ-001، FR-LB-001
- **معیار پذیرش:**
  - یال `PROPOSED` در UI متمایز (مثلاً خط‌چین) و با confidence نمایش داده می‌شود.
  - یال `REJECTED` در کشف‌های بعدی دوباره پیشنهاد نمی‌شود مگر شواهد جدید با confidence بالاتر از آستانهٔ پیکربندی‌شده.
  - یالی که `lastSeen` آن از آستانهٔ کهنگی گذشته باشد به‌عنوان «احتمالاً کهنه» علامت می‌خورد (R-08).

#### FR-CAT-008 — Dependency graph visualization & impact traversal
- **شرح:** نمایش گراف وابستگی و پیمایش اثر (upstream/downstream) با عمق قابل‌تنظیم.
- **اولویت/فاز:** Must / P2 · **VS-1:** core
- **وابستگی‌ها:** FR-CAT-006، FR-UI-003، NFR-PRF-005
- **معیار پذیرش:**
  - Given یک مؤلفه، When «سرویس‌های متأثر» درخواست شود، Then همهٔ BusinessServiceهای وابسته از طریق یال‌های `APPROVED` (و در صورت انتخاب، `PROPOSED`) فهرست می‌شوند.
  - پیمایش روی گراف ۵۰۰ گره‌ای در هدف NFR-PRF-005 رندر می‌شود.

#### FR-CAT-009 — ExternalRef mapping to source identities
- **شرح:** نگاشت موجودیت‌های کاتالوگ به هویت‌های منبع (Zabbix hostid/itemid، SolarWinds NodeID، Splunk host/source/sourcetype، ES index+فیلتر، MQ qmgr+queue، F5 virtual/pool)، دستی و با پیشنهاد خودکار تطبیق نام.
- **اولویت/فاز:** Must / P2 · **VS-1:** prereq
- **وابستگی‌ها:** FR-CAT-002، FR-ING-001
- **معیار پذیرش:**
  - سیگنال ورودی بدون ExternalRef منطبق در صف «نگاشت‌نشده» قابل‌مشاهده است و از دست نمی‌رود.
  - گزارش پوشش: درصد مؤلفه‌های criticality بالا که حداقل یک ExternalRef فعال دارند.

#### FR-CAT-010 — Version & deployment change records
- **شرح:** ثبت `Change` (Deployment، ConfigChange) با نسخه، زمان، مؤلفه؛ دستی، از طریق API، یا از webhook/منابع در P4.
- **اولویت/فاز:** Should / P3 · **VS-1:** —
- **وابستگی‌ها:** FR-CAT-002، FR-COR-005
- **معیار پذیرش:**
  - تغییرات در خط زمانی سرویس و حادثه نمایش داده می‌شوند.
  - API ثبت تغییر idempotent است (کلید تغییر تکراری رکورد دوم نمی‌سازد).

#### FR-CAT-011 — Bulk import/export YAML/CSV
- **شرح:** import/export انبوه کاتالوگ، وابستگی‌ها، ExternalRef، ServiceType، OutcomeMapping و تقویم‌ها با حالت dry-run و گزارش تفاوت.
- **اولویت/فاز:** Must / P1 · **VS-1:** prereq
- **وابستگی‌ها:** FR-CAT-001..006، NFR-DEP-004
- **معیار پذیرش:**
  - dry-run تغییرات را بدون اعمال نشان می‌دهد.
  - import اتمیک است: یک خطای اعتبارسنجی کل دسته را رد می‌کند و گزارش خط‌به‌خط می‌دهد.
  - export → import در محیط خالی، کاتالوگ معادل (round-trip) می‌سازد.

#### FR-CAT-012 — Catalog change history
- **شرح:** تاریخچهٔ نسخه‌دار همهٔ تغییرات کاتالوگ (چه کسی، چه زمانی، قبل/بعد) و امکان مشاهدهٔ وضعیت کاتالوگ در یک زمان گذشته.
- **اولویت/فاز:** Must / P1 · **VS-1:** —
- **وابستگی‌ها:** FR-ADM-003
- **معیار پذیرش:**
  - برای هر موجودیت diff قبل/بعد هر تغییر قابل‌مشاهده است.
  - تحلیل حادثهٔ گذشته از توپولوژی معتبر در زمان حادثه استفاده می‌کند.

---

## ۴. مشاهده‌پذیری تراکنش (TXN) — Epic EP-11

#### FR-TXN-001 — Transaction type definitions
- **شرح:** تعریف `TransactionType` به ازای سرویس، با `TransactionFlow` (hopهای مرتب)، منبع شمارش در هر hop (پرس‌وجوی ES/Splunk یا معیار)، و فیلدهای latency/کد/کلید.
- **اولویت/فاز:** Must / P3 · **VS-1:** prereq
- **وابستگی‌ها:** FR-CAT-006، FR-CAT-009، FR-INT-002/003
- **معیار پذیرش:**
  - نوع تراکنش `card_transfer` با حداقل ۳ hop قابل‌تعریف است.
  - پیش‌نمایش تعریف روی دادهٔ نمونه (یا بازهٔ زمانی محدود منبع) پیش از فعال‌سازی امکان‌پذیر است و بودجهٔ پرس‌وجو را رعایت می‌کند (NFR-IMP-001).

#### FR-TXN-002 — Canonical outcome classification `TxnOutcome`
- **شرح:** طبقه‌بندی هر رکورد/شمارش به یکی از مقادیر `TxnOutcome` با `OutcomeMapping` به‌عنوان داده.
- **اولویت/فاز:** Must / P3 · **VS-1:** prereq
- **وابستگی‌ها:** FR-TXN-001، FR-CAT-003
- **معیار پذیرش:**
  - کد منطبق‌نشده به `UNKNOWN` نگاشت می‌شود (هرگز `SUCCESS`) و درصد `UNKNOWN` به‌عنوان شاخص کیفیت داده نمایش داده می‌شود.
  - تغییر نگاشت نسخه‌دار و audit‌شده است و از زمان فعال‌سازی اعمال می‌شود (بازپردازش گذشته اختیاری و صریح).
  - آزمون نگاشت با مجموعه‌ای از کدهای نمونه پیش از فعال‌سازی ممکن است.

#### FR-TXN-003 — Log/search-derived per-minute summaries
- **شرح:** تولید `TransactionSummary` دقیقه‌ای (تعداد به تفکیک outcome، latency در صورت وجود) از Elasticsearch/Splunk با پرس‌وجوهای تجمیعی زمان‌بندی‌شده؛ سطح L0 پیش‌فرض.
- **اولویت/فاز:** Must / P3 · **VS-1:** core
- **وابستگی‌ها:** FR-TXN-001، FR-TXN-002، FR-INT-002، FR-INT-003، FR-ING-008، NFR-IMP-001، NFR-PRF-002
- **معیار پذیرش:**
  - Given لاگ‌های سرویس VS-1 در ES یا Splunk، When connector فعال است، Then خلاصهٔ هر دقیقه ظرف «interval + ۶۰ ثانیه» قابل‌مشاهده است (NFR-PRF-002).
  - مجموع شمارش‌ها با شمارش مستقیم در منبع برای ۲۰ دقیقهٔ نمونه برابر است (تلرانس: صفر برای دقیقه‌های بسته‌شده پس از گذشت پنجرهٔ دادهٔ دیررس).
  - دقیقه‌هایی که پرس‌وجویشان ناموفق بوده «gap» علامت می‌خورند، نه صفر (NFR-REL-002).
- **ریسک/وابستگی داده:** R-01، EXP-01، **[باز Q-07]**.

#### FR-TXN-004 — Throughput, success/failure rates, P50/P95/P99
- **شرح:** محاسبهٔ throughput، نرخ موفقیت/شکست به تفکیک outcome و صدک‌های latency **فقط در صورت وجود فیلد latency**.
- **اولویت/فاز:** Must / P3 · **VS-1:** core
- **وابستگی‌ها:** FR-TXN-003
- **معیار پذیرش:**
  - اگر latency در منبع نباشد، UI صریحاً «latency در دسترس نیست» نشان می‌دهد و مقدار صفر/جعلی نمایش نمی‌دهد.
  - روش تجمیع صدک‌ها (مثلاً از histogram یا صدک محاسبه‌شده در منبع) در توضیح معیار ذکر می‌شود؛ میانگین‌گیری از صدک‌ها انجام نمی‌شود.

#### FR-TXN-005 — Technical vs business success
- **شرح:** نمایش جداگانهٔ نرخ موفقیت فنی (`SUCCESS` + `BUSINESS_DECLINE` نسبت به کل طبقه‌بندی‌شده) و نرخ موفقیت کسب‌وکار (`SUCCESS` نسبت به کل)، با تعریف قابل‌پیکربندی مخرج.
- **اولویت/فاز:** Must / P3 · **VS-1:** core
- **وابستگی‌ها:** FR-TXN-002، FR-TXN-004
- **معیار پذیرش:**
  - Given افزایش `BUSINESS_DECLINE` بدون تغییر `TECHNICAL_FAILURE`، Then نرخ موفقیت فنی ثابت و نرخ موفقیت کسب‌وکار کاهش‌یافته نمایش داده می‌شود.
  - فرمول هر نرخ در tooltip/توضیح قابل‌مشاهده است (NFR-USA-002).

#### FR-TXN-006 — Upstream vs downstream count comparison per edge
- **شرح:** مقایسهٔ شمارش دقیقه‌ای بین hopهای متوالی `TransactionFlow` برای یافتن «گم‌شدن» یا تجمع تراکنش‌ها روی یک یال.
- **اولویت/فاز:** Should / P3 · **VS-1:** core
- **وابستگی‌ها:** FR-TXN-003، FR-CAT-006
- **معیار پذیرش:**
  - اختلاف نسبی هر یال با تلرانس پیکربندی‌شده و جبران تأخیر (lag) قابل‌تنظیم محاسبه می‌شود.
  - اختلاف بیش از آستانه یک `Event` تولید می‌کند که در پنل شواهد VS-1 ظاهر می‌شود.

#### FR-TXN-007 — Retry/duplicate detection where keys exist
- **شرح:** شناسایی تلاش مجدد/تکرار بر اساس کلیدهای L2 توکن‌شده.
- **اولویت/فاز:** Could / P3 · **VS-1:** —
- **وابستگی‌ها:** FR-TXN-008، FR-TXN-010
- **معیار پذیرش:**
  - فقط وقتی کلید L2 پیکربندی شده فعال است؛ در غیر این صورت UI «نیازمند کلید» نشان می‌دهد.
  - نرخ تکرار به تفکیک TransactionType و hop گزارش می‌شود.

#### FR-TXN-008 — Correlation tier recording L0–L3
- **شرح:** ثبت `bank.txn.correlation_tier` روی هر معیار/رویداد تراکنشی مطابق [ADR-0019](../architecture/architecture-decision-records/ADR-0019-transaction-correlation-tiers.md) و نمایش آن در UI.
- **اولویت/فاز:** Must / P3 · **VS-1:** core
- **وابستگی‌ها:** FR-TXN-003
- **معیار پذیرش:**
  - هیچ معیار تراکنشی بدون tier ذخیره نمی‌شود (اعتبارسنجی schema).
  - پیوندهای L1 دارای confidence هستند و در UI با نشان «احتمالاتی» نمایش داده می‌شوند.

#### FR-TXN-009 — W3C trace context / OTLP traces ingestion
- **شرح:** دریافت trace از طریق OTLP و استخراج `TraceSummary` و همبستگی L3.
- **اولویت/فاز:** Could / P7 · **VS-1:** —
- **وابستگی‌ها:** FR-ING-002، FR-TXN-008
- **معیار پذیرش:**
  - spanهای دارای `traceparent` به TransactionFlow نگاشت می‌شوند و معیارها tier=L3 می‌گیرند.
  - ویژگی‌های span از redaction عبور می‌کنند (NFR-PRV-002).

#### FR-TXN-010 — Redaction & tokenization of correlation keys
- **شرح:** توکن‌سازی کلیدهای همبستگی (و PAN در صورت نیاز به شمارش) با HMAC-SHA256 با کلید جدا برای هر محیط؛ redaction داده‌های ممنوع در edge و ingest ([ADR-0018](../architecture/architecture-decision-records/ADR-0018-data-retention-privacy-capacity-controls.md)).
- **اولویت/فاز:** Must / P2 · **VS-1:** prereq
- **وابستگی‌ها:** NFR-PRV-001..003، FR-ING-006
- **معیار پذیرش:**
  - Given رکورد آزمونی حاوی PAN معتبر Luhn، کد ملی، شبا و شمارهٔ موبایل، Then هیچ‌یک در `telemetry-store`، `metadata-db`، لاگ‌های پلتفرم و پاسخ API یافت نمی‌شود.
  - یک کلید ورودی در یک محیط همواره توکن یکسان می‌دهد؛ در دو محیط توکن‌ها متفاوت‌اند.
  - چرخش کلید طبق NFR-PRV-003 بدون از کار افتادن همبستگی در پنجرهٔ هم‌پوشانی انجام می‌شود.

#### FR-TXN-011 — Volume vs expected baseline
- **شرح:** مقایسهٔ حجم فعلی با baseline مورد انتظار. P3: baseline ساده بر اساس زمان هفته و DayType؛ P5: baseline فصلی پیشرفته.
- **اولویت/فاز:** Must / P3 basic, P5 advanced · **VS-1:** core
- **وابستگی‌ها:** FR-CAL-007، FR-ANO-002
- **معیار پذیرش:**
  - نمودار حجم، باند مورد انتظار را با توضیح روش محاسبه نمایش می‌دهد.
  - روزهای `HOLIDAY` با baseline روزهای کاری مقایسه نمی‌شوند.

---

## ۵. Load Balancer، Proxy و WAF (LB) — Epic EP-09 (P2)، EP-14 (P4)

#### FR-LB-001 — LB/proxy model
- **شرح:** مدل `LoadBalancer → VirtualServer → Pool → PoolMember` مستقل از فروشنده (F5، HAProxy، NGINX).
- **اولویت/فاز:** Must / P2 · **VS-1:** prereq
- **وابستگی‌ها:** FR-CAT-002، FR-CAT-009
- **معیار پذیرش:** هر سه فروشنده با همین مدل قابل‌نمایش‌اند؛ ویژگی‌های خاص فروشنده در custom attributes.

#### FR-LB-002 — LB metrics via available source with capability declaration
- **شرح:** جمع‌آوری درخواست‌ها، اتصالات، کدهای وضعیت و سلامت backend از هر منبع موجود (Zabbix، SolarWinds، HAProxy stats، F5 iControl REST، NGINX status — **نیازمند تأیید در نسخه نصب‌شده بانک**) با اعلام `Capability`.
- **اولویت/فاز:** Must / P2 · **VS-1:** core
- **وابستگی‌ها:** FR-LB-001، FR-ING-001، FR-INT-001، NFR-SEC-003، **[باز Q-08]**
- **معیار پذیرش:**
  - برای هر نمونه، Capabilityهای تأییدشده (مثلاً «status code breakdown: available/unavailable») در UI connector دیده می‌شود.
  - داشبورد فقط معیارهای دارای Capability تأییدشده را نمایش می‌دهد و بقیه را «در دسترس نیست» نشان می‌دهد.

#### FR-LB-003 — Pool capacity loss & uneven distribution
- **شرح:** تشخیص کاهش اعضای سالم pool (درصد ظرفیت از دست‌رفته) و توزیع نامتوازن بار بین اعضا.
- **اولویت/فاز:** Should / P4 · **VS-1:** —
- **وابستگی‌ها:** FR-LB-002
- **معیار پذیرش:** کاهش اعضای سالم زیر آستانهٔ پیکربندی‌شده رویداد با درصد ظرفیت باقی‌مانده تولید می‌کند؛ عدم توازن با معیار انحراف قابل‌پیکربندی گزارش می‌شود.

#### FR-LB-004 — TLS certificate expiry
- **شرح:** پایش تاریخ انقضای گواهی‌های TLS روی virtual serverها (از منبع یا بررسی handshake فقط‌خواندنی از zone).
- **اولویت/فاز:** Should / P2 · **VS-1:** —
- **وابستگی‌ها:** FR-LB-001
- **معیار پذیرش:** هشدار در آستانه‌های پیکربندی‌شده (پیش‌فرض پیشنهادی ۳۰/۱۴/۷ روز **[توصیه]**) با تاریخ جلالی.

#### FR-LB-005 — WAF block/challenge counts
- **شرح:** شمارش رویدادهای block/challenge WAF به ازای سرویس، در صورت وجود telemetry.
- **اولویت/فاز:** Could / P4 · **VS-1:** —
- **وابستگی‌ها:** FR-LB-001، FR-INT-002/003
- **معیار پذیرش:** شمارش‌ها به `REJECTED` در سطح سرویس قابل‌نگاشت‌اند؛ payload درخواست ذخیره نمی‌شود.

#### FR-LB-006 — Incoming vs processed discrepancy
- **شرح:** مقایسهٔ درخواست‌های ورودی LB با درخواست‌های پردازش‌شده در backend/برنامه.
- **اولویت/فاز:** Should / P4 · **VS-1:** —
- **وابستگی‌ها:** FR-LB-002، FR-TXN-006
- **معیار پذیرش:** اختلاف بیش از تلرانس، رویداد با هر دو مقدار و بازهٔ زمانی تولید می‌کند.

#### FR-LB-007 — Failure-domain classification rules
- **شرح:** قواعد طبقه‌بندی محل محتمل خرابی (app / network / LB / WAF / downstream) با الزامات شواهد صریح برای هر کلاس.
- **اولویت/فاز:** Should / P4 · **VS-1:** —
- **وابستگی‌ها:** FR-LB-002، FR-COR-004، FR-CAT-006
- **معیار پذیرش:** هر طبقه‌بندی فهرست شواهد پشتیبان و شواهد مخالف را نشان می‌دهد؛ اگر شواهد لازم موجود نباشد، طبقه «نامشخص» است.

---

## ۶. پیام‌رسانی (MQ) — Epic EP-09

#### FR-MQ-001 — Messaging model
- **شرح:** مدل `MessageBroker` (IBM MQ QueueManager، RabbitMQ node) → `Queue`/`Channel` با نقش صف (request/reply/DLQ/transmission).
- **اولویت/فاز:** Must / P2 · **VS-1:** prereq
- **وابستگی‌ها:** FR-CAT-002
- **معیار پذیرش:** صف‌های VS-1 با نقش و صاحب ثبت و به TransactionFlow متصل می‌شوند.

#### FR-MQ-002 — IBM MQ read-only metrics
- **شرح:** عمق فعلی، max depth، نرخ enqueue/dequeue، oldest message age و وضعیت channel از طریق دسترسی فقط‌خواندنی با حداقل امتیاز (PCF/REST — **نیازمند تأیید در نسخه نصب‌شده بانک**) یا از طریق Zabbix.
- **اولویت/فاز:** Must / P2 · **VS-1:** core
- **وابستگی‌ها:** FR-MQ-001، FR-ING-001، NFR-SEC-003، NFR-IMP-001، EXP-04، BM-06
- **معیار پذیرش:**
  - حساب پلتفرم قادر به put/get/browse محتوای پیام نیست (آزمون منفی EXP-04).
  - بازهٔ polling پیش‌فرض در بازهٔ ۱۰ تا ۶۰ ثانیه قابل‌تنظیم است **[فرض A-04]**.
  - معیار ناموجود (مثلاً oldest message age بدون فعال بودن monitoring صف) به‌عنوان Capability «unavailable» گزارش می‌شود.

#### FR-MQ-003 — RabbitMQ via management API/Prometheus plugin
- **شرح:** دریافت معیارهای صف/consumer/connection از RabbitMQ management API یا Prometheus plugin (**نیازمند تأیید در نسخه نصب‌شده بانک**).
- **اولویت/فاز:** Should / P2 · **VS-1:** —
- **وابستگی‌ها:** FR-MQ-001، FR-ING-001 یا FR-ING-003
- **معیار پذیرش:** همان معیارهای canonical FR-MQ-002 تا حدی که منبع پشتیبانی کند، با اعلام Capability.

#### FR-MQ-004 — Backlog classification transient vs sustained
- **شرح:** طبقه‌بندی backlog به گذرا یا پایدار با نرخ رشد، زمان تخلیهٔ تخمینی (drain time) و زمان تا پرشدن (time-to-full).
- **اولویت/فاز:** Must / P3 · **VS-1:** core
- **وابستگی‌ها:** FR-MQ-002، FR-CAL-004
- **معیار پذیرش:**
  - Given عمق رو به افزایش با dequeue ≈ 0 برای مدت پیکربندی‌شده، Then کلاس «sustained» با time-to-full تخمینی نمایش داده می‌شود.
  - Given افزایش عمق با dequeue فعال و drain time کمتر از آستانه، Then کلاس «transient» و بدون هشدار بحرانی.
  - توضیح طبقه‌بندی شامل مقادیر ورودی و آستانه‌هاست.

#### FR-MQ-005 — DLQ/failed message indicators
- **شرح:** پایش عمق و نرخ ورود به صف‌های DLQ/backout.
- **اولویت/فاز:** Should / P2 · **VS-1:** —
- **وابستگی‌ها:** FR-MQ-002/003
- **معیار پذیرش:** هر ورود جدید به DLQ سرویس‌های criticality بالا رویداد تولید می‌کند؛ محتوای پیام خوانده نمی‌شود.

#### FR-MQ-006 — Consumer throughput degradation
- **شرح:** تشخیص افت نرخ مصرف نسبت به baseline در حالی که ورودی پایدار است.
- **اولویت/فاز:** Should / P5 · **VS-1:** —
- **وابستگی‌ها:** FR-ANO-002، FR-MQ-002
- **معیار پذیرش:** ارزیابی‌شده در evaluation harness (FR-ANO-006).

---

## ۷. Runtime، پایگاه‌داده و زیرساخت (RUN) — Epic EP-08 (P2)، EP-14 (P4)

#### FR-RUN-001 — JVM via JMX
- **شرح:** heap، GC، threadها و pool اتصالات از طریق JMX فقط‌خواندنی.
- **اولویت/فاز:** Should / P4 · **VS-1:** —
- **وابستگی‌ها:** FR-ING-001، NFR-SEC-003
- **معیار پذیرش:** اتصال JMX با TLS و احراز هویت؛ هیچ عملیات MBean نوشتنی فراخوانی نمی‌شود.

#### FR-RUN-002 — .NET runtime
- **شرح:** معیارهای runtime دات‌نت (مثلاً از طریق OTel/EventCounters) پس از instrumentation.
- **اولویت/فاز:** Could / P7 · **VS-1:** —
- **وابستگی‌ها:** FR-ING-002
- **معیار پذیرش:** معیارها با نام‌گذاری OTel semantic conventions دریافت می‌شوند.

#### FR-RUN-003 — DB health, SQL Server first
- **شرح:** سلامت پایگاه‌داده (اتصالات، waitها، blocking، استفادهٔ فضای log) با حساب فقط‌خواندنی؛ SQL Server ابتدا.
- **اولویت/فاز:** Should / P4 · **VS-1:** —
- **وابستگی‌ها:** FR-ING-001، NFR-IMP-001
- **معیار پذیرش:** پرس‌وجوهای پایش در بودجهٔ توافق‌شده با DBA اجرا می‌شوند؛ هیچ دادهٔ کسب‌وکاری خوانده نمی‌شود.

#### FR-RUN-004 — OS/host metrics via Zabbix/SolarWinds
- **شرح:** CPU، حافظه، دیسک، شبکه و دسترسی‌پذیری میزبان از Zabbix/SolarWinds موجود.
- **اولویت/فاز:** Must / P2 · **VS-1:** core
- **وابستگی‌ها:** FR-INT-001، FR-INT-004، FR-CAT-009
- **معیار پذیرش:** معیارهای میزبان‌های زنجیرهٔ VS-1 در صفحهٔ سرویس نمایش داده می‌شوند بدون نصب agent جدید.

#### FR-RUN-005 — Container/K8s
- **شرح:** وضعیت Pod/Container/restart و منابع از Kubernetes (Prometheus scrape یا API فقط‌خواندنی).
- **اولویت/فاز:** Could / P4 · **VS-1:** —
- **وابستگی‌ها:** FR-ING-003
- **معیار پذیرش:** نگاشت به مؤلفه‌ها بر اساس label و نه نام Pod انجام می‌شود.

#### FR-RUN-006 — App-specific metrics & structured log mapping
- **شرح:** تعریف نگاشت فیلدهای لاگ ساخت‌یافته/معیارهای خاص برنامه به schema canonical.
- **اولویت/فاز:** Should / P3 · **VS-1:** —
- **وابستگی‌ها:** FR-TXN-001، FR-ING-006
- **معیار پذیرش:** نگاشت به‌صورت داده تعریف و با نمونه آزمون می‌شود.

---

## ۸. تجربهٔ دیجیتال و synthetic (DX) — Epic EP-15 (DX-004 در EP-11)

#### FR-DX-001 — Synthetic API checks (authorized, non-financial)
- **شرح:** فراخوانی دوره‌ای endpointهای غیرمالی مجاز (مثلاً health، استعلام عمومی) از zone مناسب.
- **اولویت/فاز:** Should / P4 · **VS-1:** —
- **وابستگی‌ها:** FR-DX-003، **[باز Q-20]**
- **معیار پذیرش:** نتیجه (موفق/ناموفق/latency) به‌عنوان Metric با tier جداگانه ثبت می‌شود.

#### FR-DX-002 — Synthetic multi-step journeys
- **شرح:** سناریوهای چندمرحله‌ای با گزارش گام شکست.
- **اولویت/فاز:** Could / P4 · **VS-1:** —
- **وابستگی‌ها:** FR-DX-001، FR-DX-003
- **معیار پذیرش:** گزارش گام ناموفق و latency هر گام.

#### FR-DX-003 — Synthetic safety guardrails
- **شرح:** محافظ‌ها: فقط عملیات غیرمالی، فقط حساب‌های آزمونی تأییدشده، سقف نرخ، allowlist endpointها، kill-switch، و ثبت audit.
- **اولویت/فاز:** Must / P4 · **VS-1:** —
- **وابستگی‌ها:** FR-ADM-003، FR-ADM-004
- **معیار پذیرش:**
  - تعریف check برای endpoint خارج از allowlist رد می‌شود.
  - kill-switch همهٔ checkها را ظرف یک چرخه متوقف می‌کند.
  - فعال‌سازی هر check نیازمند تأیید دونفره (four-eyes) است **[توصیه]**.

#### FR-DX-004 — Login success/failure & auth latency from logs
- **شرح:** نرخ ورود موفق/ناموفق و latency احراز هویت از لاگ‌های موجود.
- **اولویت/فاز:** Should / P3 · **VS-1:** —
- **وابستگی‌ها:** FR-TXN-003
- **معیار پذیرش:** نام کاربری، OTP و توکن ذخیره نمی‌شوند؛ فقط شمارش‌ها.

#### FR-DX-005 — RUM web/PWA/Android
- **شرح:** پایش کاربر واقعی برای وب/PWA/Android.
- **اولویت/فاز:** Could / P7 · **VS-1:** —
- **وابستگی‌ها:** FR-ING-002، NFR-PRV-001
- **معیار پذیرش:** فقط با نیاز اثبات‌شده و تأیید امنیت؛ معیارها پیش از ارسال redaction می‌شوند.

---

## ۹. تقویم و SLO (CAL) — Epic EP-04 (P1)، EP-12 (P3)

#### FR-CAL-001 — Business calendars Jalali + holiday import
- **شرح:** تقویم‌های کاری جلالی‌محور با DayType و import سالانهٔ تعطیلات (CSV/YAML)؛ تعطیلات قمری محاسبه نمی‌شوند.
- **اولویت/فاز:** Must / P1 · **VS-1:** prereq
- **وابستگی‌ها:** FR-UI-001، FR-CAT-011، **[باز Q-15]**
- **معیار پذیرش:**
  - import تعطیلات ۱۴۰۵ و ۱۴۰۶ با اعتبارسنجی تاریخ جلالی انجام می‌شود.
  - هشدار مدیریتی وقتی تقویم سال آینده تا آستانهٔ پیکربندی‌شده بارگذاری نشده است (R-14).
  - چند تقویم هم‌زمان (مثلاً تقویم شعب و تقویم ساتنا) پشتیبانی می‌شود.

#### FR-CAL-002 — Service/processing windows
- **شرح:** تعریف `ServiceWindow` به ازای سرویس و DayType (مثلاً چرخه‌های پایا/ساتنا) به‌صورت داده.
- **اولویت/فاز:** Must / P1 · **VS-1:** prereq
- **وابستگی‌ها:** FR-CAL-001
- **معیار پذیرش:** پنجره‌ها با ساعت محلی `Asia/Tehran` تعریف و به UTC محاسبه می‌شوند؛ هیچ زمانی در کد ثابت نیست (بررسی در code review).

#### FR-CAL-003 — Maintenance windows & suppression semantics
- **شرح:** پنجرهٔ نگهداری برنامه‌ریزی‌شده/اضطراری با دامنه (مؤلفه/سرویس) و معنای suppression صریح: رویدادها ثبت می‌شوند ولی اعلان نمی‌شوند؛ سلامت `MAINTENANCE`؛ SLO طبق سیاست مستثنا یا شمرده می‌شود.
- **اولویت/فاز:** Must / P1 · **VS-1:** prereq
- **وابستگی‌ها:** FR-CAT-002، FR-ADM-003
- **معیار پذیرش:**
  - رویدادهای درون پنجره حذف نمی‌شوند و با برچسب «suppressed» قابل‌مشاهده‌اند.
  - اثر روی سرویس‌های downstream فقط اگر در دامنه باشند suppress می‌شود.
  - ایجاد پنجرهٔ گذشته‌نگر (retroactive) فقط با نقش مجاز و audit.

#### FR-CAL-004 — Expected-inactive vs abnormal-inactive
- **شرح:** تشخیص اینکه سکوت یک سرویس خارج از پنجره/تعطیل است (`EXPECTED_INACTIVE`) یا غیرعادی.
- **اولویت/فاز:** Must / P3 · **VS-1:** core
- **وابستگی‌ها:** FR-CAL-001، FR-CAL-002، FR-HLT-001، FR-HLT-003
- **معیار پذیرش:**
  - Given صفر تراکنش ساتنا خارج از ServiceWindow، Then وضعیت `EXPECTED_INACTIVE` و بدون اعلان.
  - Given صفر تراکنش در پنجرهٔ فعال روز کاری به مدت آستانهٔ پیکربندی‌شده، Then رویداد «abnormal inactivity».
  - Given عدم دریافت داده از منبع، Then `UNKNOWN` (نه `EXPECTED_INACTIVE`).

#### FR-CAL-005 — Calendar-aware SLO definitions
- **شرح:** تعریف SLO (موفقیت، latency، دسترس‌پذیری) که فقط در پنجره‌های فعال سنجیده می‌شود، با سیاست صریح برای `UNKNOWN`/`INDETERMINATE`/maintenance.
- **اولویت/فاز:** Must / P3 · **VS-1:** core
- **وابستگی‌ها:** FR-CAL-002، FR-TXN-005
- **معیار پذیرش:** گزارش SLO دقیقه‌های مستثنا را به تفکیک دلیل نشان می‌دهد.

#### FR-CAL-006 — Error budget & burn rate
- **شرح:** بودجهٔ خطا و نرخ سوختن چندپنجره‌ای.
- **اولویت/فاز:** Should / P3 · **VS-1:** —
- **وابستگی‌ها:** FR-CAL-005
- **معیار پذیرش:** هشدار burn rate با پنجره‌های قابل‌پیکربندی؛ بودجه فقط بر اساس دقیقه‌های فعال.

#### FR-CAL-007 — Time-of-week/day-type volume baselines
- **شرح:** baseline حجم به ازای زمان هفته و DayType؛ P3 ساده، P5 فصلی پیشرفته.
- **اولویت/فاز:** Should / P3→P5 · **VS-1:** core
- **وابستگی‌ها:** FR-CAL-001، FR-TXN-003
- **معیار پذیرش:** baseline با حداقل تاریخچهٔ پیکربندی‌شده ساخته می‌شود؛ در کمبود تاریخچه، UI «baseline ناکافی» نشان می‌دهد.

---

## ۱۰. سلامت (HLT) — Epic EP-10

#### FR-HLT-001 — Service health with explanation
- **شرح:** محاسبهٔ `HealthState` برای هر سرویس/مؤلفه در چرخهٔ analytics (NFR-PRF-004) با توضیح قابل‌خواندن (کدام قاعده، کدام سیگنال، چه مقدار).
- **اولویت/فاز:** Must / P2 · **VS-1:** core
- **وابستگی‌ها:** FR-ANO-001، FR-CAT-006، FR-CAL-003، NFR-USA-002
- **معیار پذیرش:**
  - هر وضعیت غیر `OK` حداقل یک دلیل با پیوند به سیگنال دارد.
  - در VS-1، تطابق با قضاوت اپراتور ≥ ۹۰٪ پنجره‌های ۵ دقیقه‌ای نمونه (SC-05 در [منشور](../vision/product-charter.md)).

#### FR-HLT-002 — Health propagation as impact, not cause
- **شرح:** انتشار اثر در توپولوژی (مثلاً سرویس کسب‌وکار «متأثر از» ESB)، با تمایز بصری و معنایی از علت.
- **اولویت/فاز:** Should / P2 · **VS-1:** core
- **وابستگی‌ها:** FR-HLT-001، FR-CAT-008
- **معیار پذیرش:** وضعیت انتشاری با برچسب «impacted by X» نمایش داده می‌شود و هرگز به‌عنوان علت در CauseHypothesis وارد نمی‌شود مگر با شواهد مستقل.

#### FR-HLT-003 — Data freshness/source health → UNKNOWN
- **شرح:** اگر دادهٔ لازم کهنه یا منبع ناسالم باشد، وضعیت `UNKNOWN` با دلیل.
- **اولویت/فاز:** Must / P2 · **VS-1:** core
- **وابستگی‌ها:** FR-ING-010، NFR-OBS-002
- **معیار پذیرش:** Given توقف connector Zabbix، Then مؤلفه‌های وابسته پس از آستانهٔ کهنگی `UNKNOWN` (نه `OK`) می‌شوند و دلیل «source stale» دارند.

---

## ۱۱. ingestion و connectorها (ING) — Epics EP-06، EP-07

#### FR-ING-001 — Connector SDK contract
- **شرح:** قرارداد SDK شامل: اعلام Capability، پیکربندی schema‌دار، زمان‌بندی، بودجهٔ پرس‌وجو/نرخ، checkpoint، گزارش سلامت، خروجی canonical، و دریافت secret از `SecretProvider` ([ADR-0004](../architecture/architecture-decision-records/ADR-0004-collector-and-connector-architecture.md)).
- **اولویت/فاز:** Must / P2 · **VS-1:** prereq
- **وابستگی‌ها:** NFR-MNT-004، NFR-SEC-004، NFR-IMP-001
- **معیار پذیرش:** یک connector نمونه بدون تغییر در `platform-core` اضافه و اجرا می‌شود (NFR-MNT-004)؛ آزمون قرارداد (contract test) در CI.

#### FR-ING-002 — OTLP receive
- **شرح:** دریافت OTLP/gRPC و OTLP/HTTP از `edge-collector` و `connector-runtime` با mTLS.
- **اولویت/فاز:** Must / P2 · **VS-1:** prereq
- **وابستگی‌ها:** NFR-SEC-001
- **معیار پذیرش:** اتصال بدون گواهی کلاینت معتبر رد می‌شود؛ پاسخ‌های backpressure مطابق مشخصات OTLP.

#### FR-ING-003 — Prometheus scrape via edge-collector
- **شرح:** scrape اهداف Prometheus در zone و ارسال OTLP.
- **اولویت/فاز:** Should / P2 · **VS-1:** —
- **وابستگی‌ها:** FR-ING-002
- **معیار پذیرش:** محدودیت cardinality قابل‌پیکربندی؛ سری‌های اضافه drop و شمارش می‌شوند.

#### FR-ING-004 — SNMP & syslog via edge-collector
- **شرح:** دریافت syslog و SNMP (در صورت نیاز) در edge.
- **اولویت/فاز:** Could / P4 · **VS-1:** —
- **وابستگی‌ها:** FR-ING-002، NFR-PRV-002
- **معیار پذیرش:** redaction روی پیام‌های syslog پیش از ارسال.

#### FR-ING-005 — File-based offline import
- **شرح:** import فایل (CSV/JSON/OTLP-JSON) برای داده‌های تاریخی یا zoneهای بدون مسیر شبکه.
- **اولویت/فاز:** Should / P2 · **VS-1:** —
- **وابستگی‌ها:** FR-ING-006، FR-ING-007
- **معیار پذیرش:** import از همان مسیر redaction/dedup عبور می‌کند؛ import تکراری داده را دوبرابر نمی‌کند.

#### FR-ING-006 — Normalization, UTC timestamps, source identity
- **شرح:** نرمال‌سازی به schema canonical (OTel + `bank.*`)، تبدیل زمان به UTC با ms، ثبت `observed_at`/`ingested_at`/`source_clock_skew_estimate` و هویت منبع.
- **اولویت/فاز:** Must / P2 · **VS-1:** prereq
- **وابستگی‌ها:** [ADR-0006](../architecture/architecture-decision-records/ADR-0006-canonical-schemas-otel-plus-bank-namespace.md)
- **معیار پذیرش:** رکورد بدون منطقهٔ زمانی مشخص با قاعدهٔ پیکربندی‌شدهٔ منبع تفسیر و برچسب می‌خورد؛ هیچ offset ثابتی در کد نیست.

#### FR-ING-007 — Dedup & idempotent writes
- **شرح:** کلید idempotency برای هر دسته/رکورد؛ تکرار ناشی از retry اثری ندارد.
- **اولویت/فاز:** Must / P2 · **VS-1:** prereq
- **وابستگی‌ها:** NFR-REL-001
- **معیار پذیرش:** ارسال مجدد یک دسته ۳ بار، شمارش‌ها را تغییر نمی‌دهد.

#### FR-ING-008 — Late-arriving data
- **شرح:** پذیرش دادهٔ دیررس تا پنجرهٔ پیکربندی‌شده و بازمحاسبهٔ rollup/خلاصه‌های متأثر؛ پس از آن، رد با شمارش.
- **اولویت/فاز:** Must / P2 · **VS-1:** prereq
- **وابستگی‌ها:** FR-ING-007
- **معیار پذیرش:** دادهٔ دیررس درون پنجره در خلاصه‌ها منعکس می‌شود؛ رکوردهای ردشده در معیار self-monitoring شمرده می‌شوند.

#### FR-ING-009 — Buffering & backpressure
- **شرح:** صف دیسکی در collectorها، batching در ingest، و backpressure سرتاسری ([ADR-0010](../architecture/architecture-decision-records/ADR-0010-buffering-without-kafka-initially.md)).
- **اولویت/فاز:** Must / P2 · **VS-1:** prereq
- **وابستگی‌ها:** NFR-REL-003
- **معیار پذیرش:** با قطع ingest، collector داده را در دیسک نگه می‌دارد و پس از بازگشت بدون از دست رفتن ارسال می‌کند (BM-07).

#### FR-ING-010 — Connector health/diagnostics UI
- **شرح:** صفحهٔ وضعیت هر ConnectorInstance: آخرین اجرای موفق، lag، خطاها، مصرف بودجهٔ پرس‌وجو، Capabilityها.
- **اولویت/فاز:** Must / P2 · **VS-1:** prereq
- **وابستگی‌ها:** NFR-OBS-002
- **معیار پذیرش:** خطای احراز هویت منبع در کمتر از یک چرخه با پیام قابل‌فهم نمایش داده می‌شود؛ secretها هرگز نمایش داده نمی‌شوند.

#### FR-ING-011 — Connector simulation/test harness
- **شرح:** محیط آزمون با شبیه‌ساز/ضبط پاسخ‌های منبع (بدون داده حساس) برای توسعه و رگرسیون connectorها در محیط آفلاین.
- **اولویت/فاز:** Must / P2 · **VS-1:** prereq
- **وابستگی‌ها:** FR-ING-001
- **معیار پذیرش:** هر connector مجموعهٔ آزمون مبتنی بر پاسخ‌های ضبط‌شده دارد که در CI بدون دسترسی به منبع واقعی اجرا می‌شود.

---

## ۱۲. یکپارچه‌سازی با پلتفرم‌های موجود (INT) — Epic EP-08

همهٔ connectorها فقط‌خواندنی، pull، با حساب اختصاصی و بودجهٔ پرس‌وجو ([ADR-0020](../architecture/architecture-decision-records/ADR-0020-existing-platform-integration-read-only-pull.md))؛ جزئیات API **نیازمند تأیید در نسخه نصب‌شده بانک** **[باز Q-08]**.

#### FR-INT-001 — Zabbix connector
- **شرح:** دریافت hosts، items، history/trends منتخب و problems از Zabbix JSON-RPC API.
- **اولویت/فاز:** Must / P2 · **VS-1:** core
- **وابستگی‌ها:** FR-ING-001، FR-CAT-009، NFR-IMP-001، BM-05
- **معیار پذیرش:** همگام‌سازی problems به `Event` canonical؛ بار API در بودجهٔ توافق‌شده؛ عدم نیاز به تغییر پیکربندی Zabbix.

#### FR-INT-002 — Elasticsearch/OpenSearch connector
- **شرح:** پرس‌وجوهای تجمیعی زمان‌بندی‌شده `_search` (aggregations) برای استخراج خلاصه‌ها و رویدادها.
- **اولویت/فاز:** Must / P2 · **VS-1:** core
- **وابستگی‌ها:** FR-ING-001، NFR-IMP-001، BM-04، **[باز Q-22]**
- **معیار پذیرش:** فقط aggregation؛ اسناد خام فقط در پرس‌وجوی federated مجاز و redacted (FR-INT-007)؛ timeout و سقف هزینهٔ پرس‌وجو اعمال می‌شود.

#### FR-INT-003 — Splunk connector
- **شرح:** اجرای جست‌وجوهای زمان‌بندی‌شده از طریق Splunk REST search API با خروجی تجمیعی.
- **اولویت/فاز:** Must / P2 · **VS-1:** core
- **وابستگی‌ها:** FR-ING-001، NFR-IMP-001، BM-04، **[باز Q-22]**
- **معیار پذیرش:** رعایت سهمیهٔ جست‌وجوی هم‌زمان توافق‌شده؛ شکست جست‌وجو به gap منجر می‌شود نه صفر.

#### FR-INT-004 — SolarWinds SWIS connector
- **شرح:** دریافت nodes، interfaces، alerts از SWIS/SWQL.
- **اولویت/فاز:** Should / P2 · **VS-1:** —
- **وابستگی‌ها:** FR-ING-001
- **معیار پذیرش:** همانند FR-INT-001.

#### FR-INT-005 — Inbound webhooks
- **شرح:** endpoint اختیاری برای webhookهای ورودی (alert، deployment) در صورتی که بانک پیکربندی کند.
- **اولویت/فاز:** Should / P4 · **VS-1:** —
- **وابستگی‌ها:** FR-COR-001، NFR-SEC-001
- **معیار پذیرش:** احراز هویت (mTLS یا امضای HMAC)، اعتبارسنجی schema، idempotency.

#### FR-INT-006 — Deep links back to sources
- **شرح:** پیوند مستقیم از سیگنال/شاهد به صفحهٔ مربوط در Zabbix/SolarWinds/Splunk/Kibana.
- **اولویت/فاز:** Must / P2 · **VS-1:** core
- **وابستگی‌ها:** FR-CAT-009
- **معیار پذیرش:** پیوند بازهٔ زمانی و فیلتر صحیح را حمل می‌کند؛ دسترسی کاربر به منبع توسط خود منبع کنترل می‌شود.

#### FR-INT-007 — Federated evidence query with redaction
- **شرح:** پرس‌وجوی درخواستی (on-demand) به Splunk/ES از طریق `connector-runtime` (صف فرمان pull) با redaction نتایج.
- **اولویت/فاز:** Should / P4 · **VS-1:** —
- **وابستگی‌ها:** NFR-PRV-005، NFR-IMP-001، FR-ADM-001
- **معیار پذیرش:** نتایج پیش از رسیدن به UI redaction می‌شوند؛ سقف تعداد نتایج و زمان؛ هر پرس‌وجو audit می‌شود.

---

## ۱۳. رویداد و همبستگی (COR) — Epic EP-13

#### FR-COR-001 — Common event model
- **شرح:** schema رویداد canonical نسخه‌دار (پوشش CloudEvents-style، [ADR-0005](../architecture/architecture-decision-records/ADR-0005-api-and-event-contracts.md)) برای alertها، problemها، تغییرات و رویدادهای مشتق.
- **اولویت/فاز:** Must / P2 · **VS-1:** prereq
- **وابستگی‌ها:** FR-ING-006
- **معیار پذیرش:** رویدادهای Zabbix، SolarWinds و قواعد داخلی به همین schema نگاشت می‌شوند؛ آزمون قرارداد در CI.

#### FR-COR-002 — Dedup & time-window grouping
- **شرح:** حذف تکرار و گروه‌بندی زمانی رویدادها.
- **اولویت/فاز:** Must / P4 · **VS-1:** —
- **وابستگی‌ها:** FR-COR-001
- **معیار پذیرش:** رویدادهای تکراری با شمارنده تجمیع می‌شوند؛ نرخ کاهش نویز گزارش می‌شود.

#### FR-COR-003 — Topology-aware incident grouping
- **شرح:** گروه‌بندی رویدادها در یک Incident بر اساس مجاورت توپولوژیک و زمانی. نسخهٔ lite در P3 برای VS-1.
- **اولویت/فاز:** Must / P4 (lite P3) · **VS-1:** core (lite)
- **وابستگی‌ها:** FR-COR-001، FR-CAT-008
- **معیار پذیرش:** در بازپخش حوادث VS-1، رویدادهای hopهای زنجیره در یک گروه قرار می‌گیرند.

#### FR-COR-004 — Probable-cause ranking with evidence/confidence/uncertainty
- **شرح:** رتبه‌بندی `CauseHypothesis` قطعی و توضیح‌پذیر ([ADR-0012](../architecture/architecture-decision-records/ADR-0012-correlation-engine-design.md)) با score، confidence، شواهد موافق/مخالف و disclaimer «همبستگی ≠ علیت». در P3 فقط پنل شواهد ساده (لیست سیگنال‌های همبسته در hopها با confidence).
- **اولویت/فاز:** Must / P4 · **VS-1:** — (پنل شواهد P3 جزء VS-1)
- **وابستگی‌ها:** FR-COR-003، FR-HLT-002، FR-TXN-006
- **معیار پذیرش:**
  - در بازپخش ≥ ۳ حادثهٔ توافق‌شده، hop معیوب واقعی در top-3 برای ≥ ۲ از ۳ (SC-04).
  - هر فرضیه توضیح متنی فارسی و فهرست شواهد دارد؛ disclaimer همیشه نمایش داده می‌شود.

#### FR-COR-005 — Change awareness
- **شرح:** در نظر گرفتن `Change`های اخیر روی مؤلفه‌های مرتبط به‌عنوان شاهد.
- **اولویت/فاز:** Should / P4 · **VS-1:** —
- **وابستگی‌ها:** FR-CAT-010، FR-COR-004
- **معیار پذیرش:** تغییر در پنجرهٔ پیکربندی‌شده پیش از حادثه در شواهد ظاهر می‌شود.

#### FR-COR-006 — Operator feedback
- **شرح:** ثبت تأیید/رد/اصلاح فرضیه‌ها و علت واقعی توسط اپراتور.
- **اولویت/فاز:** Must / P4 · **VS-1:** —
- **وابستگی‌ها:** FR-COR-004، FR-ADM-003
- **معیار پذیرش:** بازخورد در ارزیابی (FR-ANO-006) و گزارش دقت رتبه‌بندی استفاده می‌شود.

#### FR-COR-007 — Incident timeline
- **شرح:** خط زمانی حادثه شامل رویدادها، تغییرات، تغییر وضعیت سلامت، اقدامات و بازخورد.
- **اولویت/فاز:** Must / P4 · **VS-1:** —
- **وابستگی‌ها:** FR-COR-003
- **معیار پذیرش:** زمان‌ها به جلالی/Asia/Tehran نمایش داده و skew منبع در صورت معنادار بودن علامت‌گذاری می‌شود.

---

## ۱۴. ناهنجاری و پیش‌بینی (ANO) — Epic EP-16 (ANO-001 در EP-10)

مرحله‌بندی طبق [ADR-0013](../architecture/architecture-decision-records/ADR-0013-analytics-staging-rules-statistics-ml.md).

#### FR-ANO-001 — Static thresholds
- **شرح:** قواعد آستانهٔ ایستا روی معیارها با hysteresis و مدت پایداری.
- **اولویت/فاز:** Must / P2 · **VS-1:** prereq
- **وابستگی‌ها:** FR-COR-001
- **معیار پذیرش:** قاعده با آستانه/مدت پیکربندی و پیش‌نمایش روی تاریخچه آزمون‌پذیر است.

#### FR-ANO-002 — Seasonal baselines
- **شرح:** baselineهای فصلی (روز/هفته/DayType). نسخهٔ پایه در P3.
- **اولویت/فاز:** Must / P5 (basic P3) · **VS-1:** core (basic)
- **وابستگی‌ها:** FR-CAL-007
- **معیار پذیرش:** باند مورد انتظار با روش و طول تاریخچه قابل‌نمایش است.

#### FR-ANO-003 — Statistical detection
- **شرح:** robust z-score/MAD، EWMA، باقی‌ماندهٔ STL.
- **اولویت/فاز:** Should / P5 · **VS-1:** —
- **وابستگی‌ها:** FR-ANO-002، FR-ANO-006
- **معیار پذیرش:** فعال‌سازی هر آشکارساز فقط پس از ارزیابی در harness.

#### FR-ANO-004 — Multivariate/dependency-aware patterns
- **شرح:** قواعد چندمتغیره وابستگی‌آگاه. **در VS-1 یک قاعده:** «افت نرخ موفقیت در حالی که ترافیک ورودی پایدار است» (نسبت به baseline زمان هفته).
- **اولویت/فاز:** Should / P5 (one rule P3) · **VS-1:** core (one rule)
- **وابستگی‌ها:** FR-TXN-005، FR-CAL-007، FR-LB-002
- **معیار پذیرش:** Given ورودی در باند baseline و نرخ موفقیت پایین‌تر از آستانه برای N دقیقه، Then رویداد با هر دو مقدار و توضیح.

#### FR-ANO-005 — Forecasts
- **شرح:** پیش‌بینی time-to-full صف، burn بودجهٔ SLO و اشباع.
- **اولویت/فاز:** Should / P5 · **VS-1:** —
- **وابستگی‌ها:** FR-MQ-004، FR-CAL-006
- **معیار پذیرش:** هر پیش‌بینی بازهٔ عدم‌قطعیت دارد.

#### FR-ANO-006 — Evaluation harness
- **شرح:** چارچوب ارزیابی precision/recall و بودجهٔ هشدار روی تاریخچهٔ برچسب‌دار.
- **اولویت/فاز:** Must / P5 · **VS-1:** —
- **وابستگی‌ها:** FR-COR-006
- **معیار پذیرش:** گزارش قابل‌بازتولید برای هر آشکارساز/نسخه؛ M5 فقط با معیارهای اندازه‌گیری‌شده.

#### FR-ANO-007 — Drift & retraining
- **شرح:** پایش drift و بازآموزی/بازسازی baselineها.
- **اولویت/فاز:** Should / P5 · **VS-1:** —
- **وابستگی‌ها:** FR-ANO-006
- **معیار پذیرش:** هشدار drift و نسخه‌بندی مدل/baseline با امکان بازگشت.

#### FR-ANO-008 — Explanation for every anomaly
- **شرح:** هر ناهنجاری دارای توضیح (معیار، مقدار، مقدار مورد انتظار، روش، اطمینان).
- **اولویت/فاز:** Must / P5 · **VS-1:** —
- **وابستگی‌ها:** NFR-USA-002
- **معیار پذیرش:** ناهنجاری بدون توضیح توسط اعتبارسنجی schema رد می‌شود.

---

## ۱۵. اعلان (NTF) — Epic EP-17

#### FR-NTF-001 — Channels: email/SMS gateway/ITSM webhook
- **شرح:** ارسال اعلان به email، درگاه SMS داخلی و ITSM از طریق webhook.
- **اولویت/فاز:** Should / P4 · **VS-1:** —
- **وابستگی‌ها:** FR-COR-003، **[باز Q-14]**
- **معیار پذیرش:** قواعد مسیریابی بر اساس سرویس/criticality؛ retry با idempotency؛ محتوای اعلان فاقد دادهٔ حساس.

#### FR-NTF-002 — Suppression in maintenance & expected-inactive
- **شرح:** عدم ارسال اعلان در `MAINTENANCE` و `EXPECTED_INACTIVE` (در P3 برای نمایش/هشدارهای داخلی؛ در P4 برای کانال‌ها).
- **اولویت/فاز:** Must / P3 · **VS-1:** core
- **وابستگی‌ها:** FR-CAL-003، FR-CAL-004
- **معیار پذیرش:** هشدارهای suppress‌شده با دلیل قابل‌مشاهده‌اند.

---

## ۱۶. رابط کاربری (UI) — Epics EP-03، EP-10

#### FR-UI-001 — Persian RTL, Jalali dates, English identifiers
- **شرح:** UI فارسی راست‌به‌چپ، تاریخ جلالی، شناسه‌های فنی انگلیسی LTR درون متن.
- **اولویت/فاز:** Must / P1 · **VS-1:** prereq
- **وابستگی‌ها:** NFR-USA-001
- **معیار پذیرش:** همهٔ صفحات در RTL بدون شکستگی چیدمان؛ شناسه‌ها و اعداد فنی با جهت صحیح؛ امکان نمایش تاریخ میلادی در کنار جلالی.

#### FR-UI-002 — Service overview/health dashboard
- **شرح:** نمای کلی سرویس‌ها با HealthState، روند و فیلتر (Environment، criticality، owner).
- **اولویت/فاز:** Must / P2 · **VS-1:** core
- **وابستگی‌ها:** FR-HLT-001، NFR-PRF-003
- **معیار پذیرش:** بارگذاری در هدف NFR-PRF-003؛ به‌روزرسانی زنده با SSE.

#### FR-UI-003 — Topology map
- **شرح:** نقشهٔ توپولوژی با رنگ وضعیت، provenance و confidence یال‌ها.
- **اولویت/فاز:** Must / P2 · **VS-1:** core
- **وابستگی‌ها:** FR-CAT-008، NFR-PRF-005
- **معیار پذیرش:** ۵۰۰ گره در هدف NFR-PRF-005 (BM-10)؛ تمایز بصری MANUAL/DISCOVERED/PROPOSED.

#### FR-UI-004 — Service detail
- **شرح:** صفحهٔ جزئیات سرویس: KPIها، تراکنش‌ها (فنی/کسب‌وکاری، tier)، وابستگی‌ها، پنل شواهد، پیوند به منابع.
- **اولویت/فاز:** Must / P3 · **VS-1:** core
- **وابستگی‌ها:** FR-TXN-004، FR-TXN-005، FR-COR-004 (پنل P3)، FR-INT-006
- **معیار پذیرش:** همهٔ عناصر دموی VS-1 ([منشور](../vision/product-charter.md)) در این صفحه قابل‌مشاهده‌اند.

---

## ۱۷. مدیریت و امنیت (ADM) — Epics EP-02، EP-07، EP-21

#### FR-ADM-001 — RBAC with scopes
- **شرح:** نقش‌ها با scope (Environment، گروه سرویس)، deny-by-default ([ADR-0015](../architecture/architecture-decision-records/ADR-0015-authentication-authorization-audit.md)).
- **اولویت/فاز:** Must / P1 · **VS-1:** prereq
- **وابستگی‌ها:** FR-ADM-002، NFR-SEC-005
- **معیار پذیرش:** کاربر بدون نقش به هیچ دادهٔ سرویس دسترسی ندارد؛ آزمون ماتریس دسترسی خودکار.

#### FR-ADM-002 — AD/LDAP + SSO OIDC/SAML
- **شرح:** احراز هویت از طریق IdP بانک (OIDC/SAML) یا Keycloak با فدراسیون AD/LDAP؛ نگاشت گروه‌های AD به نقش‌ها.
- **اولویت/فاز:** Must / P1 · **VS-1:** prereq
- **وابستگی‌ها:** NFR-SEC-009، **[باز Q-04]**، **[فرض A-07]**
- **معیار پذیرش:** ورود SSO؛ حذف کاربر از گروه AD دسترسی را حداکثر تا پایان اعتبار نشست/توکن لغو می‌کند.

#### FR-ADM-003 — Audit log
- **شرح:** لاگ audit append-only برای ورود، تغییرات پیکربندی/کاتالوگ، پرس‌وجوهای federated، export و تغییرات دسترسی؛ ارسال به SIEM/Splunk بانک.
- **اولویت/فاز:** Must / P1 · **VS-1:** prereq
- **وابستگی‌ها:** NFR-SEC-006
- **معیار پذیرش:** هیچ نقشی (حتی admin) نمی‌تواند رکورد audit را ویرایش/حذف کند؛ رکوردها شامل actor، زمان، عمل، هدف، نتیجه.

#### FR-ADM-004 — Connector secret management
- **شرح:** نگهداری اعتبارنامه‌های منابع از طریق `SecretProvider` (vault/HSM بانک یا ذخیرهٔ رمز‌شده) با چرخش.
- **اولویت/فاز:** Must / P2 · **VS-1:** prereq
- **وابستگی‌ها:** NFR-SEC-004، **[باز Q-09]**
- **معیار پذیرش:** secretها در فایل پیکربندی، لاگ یا پاسخ API ظاهر نمی‌شوند؛ چرخش بدون restart کامل.

#### FR-ADM-005 — Multi-bank tenancy
- **شرح:** پشتیبانی چند بانک (Organization) با جداسازی کامل.
- **اولویت/فاز:** Could / P6+ · **VS-1:** —
- **وابستگی‌ها:** **[باز Q-11]**
- **معیار پذیرش:** به تصمیم Q-11 موکول است؛ طراحی P1 شناسهٔ Organization را در مدل نگه می‌دارد تا مسیر آینده بسته نشود **[توصیه]**.

#### FR-ADM-006 — Platform self-monitoring
- **شرح:** telemetry خود پلتفرم (OTel) و صفحهٔ سلامت داخلی؛ از P2 تا P6 تکمیل می‌شود.
- **اولویت/فاز:** Must / P2→P6 · **VS-1:** prereq
- **وابستگی‌ها:** NFR-OBS-001، NFR-OBS-002، NFR-AVL-002
- **معیار پذیرش:** صفحهٔ سلامت پلتفرم حتی در صورت از دسترس خارج بودن منابع بانک کار می‌کند.

---

## ۱۸. ردیابی (Traceability)

| حوزه | Epic(ها) | ADRهای مرتبط |
|---|---|---|
| CAT | EP-03 (P1–P2)، EP-22 | ADR-0007، ADR-0011 |
| TXN | EP-11 | ADR-0006، ADR-0018، ADR-0019 |
| LB | EP-09، EP-14 | ADR-0004، ADR-0020 |
| MQ | EP-09 | ADR-0004، ADR-0020 |
| RUN | EP-08 (RUN-004)، EP-14 | ADR-0004 |
| DX | EP-15، EP-11 (DX-004) | ADR-0018 |
| CAL | EP-04، EP-12 | ADR-0007 |
| HLT | EP-10 | ADR-0012 |
| ING | EP-06، EP-07 | ADR-0004، ADR-0005، ADR-0006، ADR-0010 |
| INT | EP-08، EP-13 (INT-007) | ADR-0009، ADR-0020 |
| COR | EP-13 | ADR-0005، ADR-0012 |
| ANO | EP-10 (ANO-001)، EP-16 | ADR-0013 |
| NTF | EP-17 | ADR-0005 |
| UI | EP-03، EP-10 | ADR-0014 |
| ADM | EP-02، EP-07 (ADM-004)، EP-21 (ADM-006) | ADR-0015، ADR-0016، ADR-0018 |

نیازمندی‌های غیرکارکردی در [non-functional-requirements.md](non-functional-requirements.md) و مستندات ADR در پوشهٔ [architecture-decision-records](../architecture/architecture-decision-records/) قرار دارند. شناسهٔ storyها به شکل `US-<EP#>-NN` به این FRها ارجاع خواهد داد.

## ۱۹. نکات برای بازبینی

- «نکته برای بازبینی»: FR-COR-004 در spine «Must, P4» است و در VS-1 علامت VS ندارد، ولی معیار موفقیت VS-1 (رتبه‌بندی hop معیوب در top-3) عملاً به نسخهٔ ساده‌ای از رتبه‌بندی شواهد در P3 نیاز دارد. این سند آن را «پنل شواهد P3» نامیده است؛ پیشنهاد می‌شود دامنهٔ دقیق «evidence panel» در P3 (صرفاً فهرست مرتب‌شده با confidence، بدون CauseHypothesis کامل) در طرح VS-1 صریح شود.
- «نکته برای بازبینی»: FR-NTF-002 در P3 است ولی کانال‌های اعلان (FR-NTF-001) در P4؛ معنای P3 در این سند «suppression در وضعیت/هشدارهای داخلی UI» تعبیر شده است.
