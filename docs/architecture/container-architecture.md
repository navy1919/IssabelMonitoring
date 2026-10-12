# معماری کانتینرها (Container Architecture – C4 Level 2)

| فیلد | مقدار |
|---|---|
| Document ID | `ARC-CNT-001` |
| Version | `0.1` |
| Status | `Draft – Phase 0 – awaiting approval` |
| Date | `2026-10-11` (۱۴۰۵/۰۷/۱۹) |
| Owner | `Architecture` |
| Related documents | [system-context.md](system-context.md)، [data-architecture.md](data-architecture.md)، [ADR-0002](architecture-decision-records/ADR-0002-modular-monolith-with-edge-collectors.md)، [ADR-0003](architecture-decision-records/ADR-0003-backend-language-java-spring-boot.md)، [ADR-0004](architecture-decision-records/ADR-0004-collector-and-connector-architecture.md)، [ADR-0005](architecture-decision-records/ADR-0005-api-and-event-contracts.md)، [ADR-0007](architecture-decision-records/ADR-0007-metadata-store-postgresql.md)، [ADR-0008](architecture-decision-records/ADR-0008-telemetry-store-clickhouse.md)، [ADR-0010](architecture-decision-records/ADR-0010-buffering-without-kafka-initially.md)، [ADR-0012](architecture-decision-records/ADR-0012-correlation-engine-design.md)، [ADR-0016](architecture-decision-records/ADR-0016-offline-deployment-and-artifacts.md)، [ADR-0017](architecture-decision-records/ADR-0017-ha-dr-architecture.md) |

> **قرارداد برچسب‌گذاری:** [واقعیت]، [فرض A-xx]، [فرضیه]، [توصیه]، [باز Q-xx] — مطابق spine §1.

## ۱. خلاصه

[توصیه] سبک معماری، **modular monolith** به نام `platform-core` به‌همراه اجزای جمع‌آوری لبه که جداگانه و به‌ازای هر zone مستقر می‌شوند ([ADR-0002](architecture-decision-records/ADR-0002-modular-monolith-with-edge-collectors.md)، Confidence: Firm). هسته یک artifact واحد است که با پیکربندی در سه نقش `api`، `ingest` و `analytics` اجرا می‌شود. هیچ message broker در شروع وجود ندارد؛ `event-bus` (Kafka) فقط با تحقق triggerهای [ADR-0010](architecture-decision-records/ADR-0010-buffering-without-kafka-initially.md) اضافه می‌شود.

[باز Q-01] زبان و چارچوب (Java 21 LTS + Spring Boot 3.x) تا روشن شدن مهارت تیم Blocked است ([ADR-0003](architecture-decision-records/ADR-0003-backend-language-java-spring-boot.md)). ساختار ماژولی این سند مستقل از زبان معتبر است.

## ۲. نمودار کانتینرها

```mermaid
flowchart LR
    User["Operator browser"]

    subgraph MON["monitoring-zone"]
        UI["web-ui<br/>React + TS, ECharts, Cytoscape.js<br/>(static SPA)"]
        subgraph CORE["platform-core (single artifact)"]
            API["role: api"]
            ING["role: ingest"]
            ANA["role: analytics"]
        end
        PG[("metadata-db<br/>PostgreSQL 16+")]
        CH[("telemetry-store<br/>ClickHouse")]
        BUS[["event-bus (deferred)<br/>Kafka KRaft"]]
    end

    IDP["identity-provider<br/>Bank IdP or Keycloak + AD"]

    subgraph ZONE["source zone (per zone)"]
        CR["connector-runtime<br/>Java 21, Connector SDK"]
        EC["edge-collector<br/>OTel Collector (OCB build)<br/>disk-backed queue, redaction"]
        SRC["Source systems<br/>Zabbix, SolarWinds, Splunk, ES,<br/>IBM MQ, RabbitMQ, F5, HAProxy, NGINX"]
        APP["Apps / devices<br/>OTLP, Prometheus, syslog"]
    end

    User -- "HTTPS" --> UI
    User -- "HTTPS REST (OpenAPI 3.1) + SSE, OIDC bearer" --> API
    User -- "OIDC login" --> IDP
    API -- "OIDC token validation / JWKS" --> IDP

    CR -- "read-only pull" --> SRC
    EC -- "scrape / receive" --> APP
    APP -- "push OTLP / syslog" --> EC

    CR -- "mTLS REST: GET config" --> API
    CR -- "mTLS long-poll /internal/v1/commands" --> API
    CR -- "mTLS OTLP/HTTP + /internal/v1/ingest/events" --> ING
    EC -- "mTLS OTLP gRPC/HTTP" --> ING

    API -- "JDBC/TLS" --> PG
    API -- "HTTP/native TLS (read)" --> CH
    ING -- "JDBC/TLS (outbox, lookups)" --> PG
    ING -- "batched insert" --> CH
    ANA -- "JDBC/TLS" --> PG
    ANA -- "read/insert" --> CH
    ING -. "only if ADR-0010 triggers fire" .-> BUS
    BUS -. " " .-> ANA
```

*توضیح:* همهٔ اتصال‌ها از zone منبع به سمت `monitoring-zone` آغاز می‌شوند. `platform-core` هیچ اتصالی به داخل zoneها باز نمی‌کند؛ فرمان‌ها (مثلاً evidence query) را `connector-runtime` با long-poll دریافت می‌کند.

## ۳. کانتینرها و مسئولیت‌ها

| Container | مسئولیت | فناوری (موقت) | حالت | استقرار |
|---|---|---|---|---|
| `web-ui` | SPA فارسی RTL با تقویم جلالی؛ داشبورد سلامت، نقشهٔ توپولوژی، جزئیات سرویس، پنل شواهد، مدیریت کاتالوگ و connectorها | React + TypeScript، Apache ECharts، Cytoscape.js ([ADR-0014](architecture-decision-records/ADR-0014-frontend-react-echarts-cytoscape.md)) | بدون حالت (فایل ایستا) | سرو شده از reverse proxy داخلی یا خود `platform-core(api)` در `monitoring-zone` |
| `platform-core` | منطق دامنه، API، دریافت و نرمال‌سازی داده، تحلیل، Incident | Java 21 LTS + Spring Boot 3.x (Blocked Q-01) | بدون حالت (حالت در DBها) | `monitoring-zone`؛ ≥ ۲ نمونه در هر نقش از فاز ۶ |
| `connector-runtime` | میزبان connectorهای pull: Zabbix، SolarWinds، Splunk، Elasticsearch/OpenSearch، IBM MQ، RabbitMQ، F5، HAProxy، NGINX، DB فقط‌خواندنی؛ زمان‌بندی poll، اعمال query budget، redaction لبه، بافر محلی، اجرای evidence query | Java 21، Connector SDK | checkpoint محلی + بافر دیسکی کوچک [توصیه] | یک نمونه (یا دو برای HA) در هر zone منبع |
| `edge-collector` | دریافت پروتکل‌های استاندارد: OTLP، Prometheus scrape، syslog، filelog، SNMP (بعداً)؛ صف پایدار دیسکی؛ processor برای redaction | OpenTelemetry Collector (contrib، توزیع سفارشی با OCB، ساخت آفلاین) | صف دیسکی | در هر zone که منبع push یا scrape دارد |
| `metadata-db` | کاتالوگ، توپولوژی، تقویم، SLO، پیکربندی connector، Incident، نگاشت IAM، audit، outbox | PostgreSQL 16+ ([ADR-0007](architecture-decision-records/ADR-0007-metadata-store-postgresql.md)) | حالت‌دار | `monitoring-zone`؛ Patroni + streaming replication در فاز ۶ |
| `telemetry-store` | متریک، rollupها، رویدادهای نرمال‌شده، خلاصهٔ تراکنش، رویداد تراکنش توکنی، تاریخچهٔ سلامت/ناهنجاری | ClickHouse LTS؛ ClickHouse Keeper در حالت replicated ([ADR-0008](architecture-decision-records/ADR-0008-telemetry-store-clickhouse.md)) | حالت‌دار | `monitoring-zone`؛ تک‌نود در Low، ReplicatedMergeTree + Keeper سه‌نودی در Medium+ |
| `identity-provider` | احراز هویت OIDC/SAML؛ federation به AD/LDAP | IdP بانک یا Keycloak ([ADR-0015](architecture-decision-records/ADR-0015-authentication-authorization-audit.md)، Blocked Q-04) | خارجی یا حالت‌دار (Keycloak با PostgreSQL جدا) | `shared-services-zone` یا `monitoring-zone` |
| `event-bus` (deferred) | بافر و replay چندمصرف‌کننده | Kafka (KRaft) | حالت‌دار | فقط پس از تحقق trigger |

## ۴. نقش‌های زمان‌اجرای `platform-core`

یک artifact واحد؛ نقش با پیکربندی (مثلاً `bsoip.roles=api,ingest`) انتخاب می‌شود. در Low هر سه نقش می‌توانند در یک فرایند اجرا شوند.

| نقش | ماژول‌های فعال (غالب) | بار کاری | مقیاس‌پذیری |
|---|---|---|---|
| `api` | `query-api`، `catalog`، `topology`، `calendar`، `slo`، `incidents` (خواندن/ویرایش)، `connectors-control`، `evidence-query` (ایجاد فرمان و دریافت نتیجه)، `iam`، `audit` | درخواست‌های REST، SSE، endpointهای داخلی پیکربندی و فرمان برای `connector-runtime` | افقی پشت Load Balancer؛ بدون حالت |
| `ingest` | `ingestion`، `connectors-control` (دریافت heartbeat و lag) | دریافت OTLP و batch API، نرمال‌سازی، redaction دوم، dedup، batching و نوشتن در ClickHouse؛ نوشتن outbox برای رویدادهای مهم | افقی؛ هر نمونه مستقل batch می‌نویسد |
| `analytics` | `health`، `transactions`، `events`، `correlation`، `anomaly`، `slo` (محاسبهٔ budget)، `incidents` (ایجاد/به‌روزرسانی)، `notification` | micro-batch هر ۳۰–۶۰ ثانیه (موقت) روی ClickHouse/PostgreSQL؛ مصرف outbox | افقی با تقسیم کار بر اساس partition سرویس‌ها و قفل مشورتی (advisory lock) در PostgreSQL [توصیه] |

[توصیه] ماژول `audit` در همهٔ نقش‌ها فعال است چون هر عملیات حساس باید در همان تراکنش audit شود. [فرضیه] چرخهٔ ≤ ۶۰ ثانیه (NFR-PRF-004) در اندازهٔ Medium برقرار است؛ با BM-08 آزموده می‌شود.

## ۵. نقشهٔ ماژول‌ها و وابستگی‌های مجاز

قواعد:

1. هر ماژول API عمومی (`api` package) و پیاده‌سازی داخلی (`internal`) دارد؛ ماژول‌های دیگر فقط به `api` دسترسی دارند.
2. وابستگی‌ها یک‌طرفه و بدون چرخه‌اند؛ برای جهت معکوس از domain event درون‌فرایندی استفاده می‌شود.
3. هیچ ماژولی به جداول schema ماژول دیگر در PostgreSQL مستقیم دسترسی ندارد (هر ماژول مالک schema خود است؛ نگاه کنید به [data-architecture.md](data-architecture.md)).
4. این قواعد با ArchUnit در CI اعمال می‌شوند (NFR-MNT-001).

```mermaid
flowchart TB
    subgraph L4["Edge-facing / presentation"]
        QAPI["query-api"]
        EVQ["evidence-query"]
        NTF["notification"]
    end
    subgraph L3["Analysis"]
        COR["correlation"]
        ANO["anomaly"]
        INC["incidents"]
        HLT["health"]
        SLO["slo"]
        TXN["transactions"]
    end
    subgraph L2["Data intake"]
        ING["ingestion"]
        EVT["events"]
        CCTL["connectors-control"]
    end
    subgraph L1["Reference / foundation"]
        CAT["catalog"]
        TOP["topology"]
        CAL["calendar"]
        IAM["iam"]
        AUD["audit"]
    end

    QAPI --> INC & HLT & SLO & TXN & CAT & TOP & CAL & CCTL & EVQ
    EVQ --> CCTL & CAT & AUD
    NTF --> INC & CAL
    COR --> EVT & TOP & HLT & CAT
    INC --> COR & CAT
    ANO --> TXN & CAL & CAT
    HLT --> TOP & CAL & CAT
    SLO --> TXN & CAL & CAT
    TXN --> CAT & CAL
    ING --> CAT & CCTL
    EVT --> CAT
    CCTL --> CAT
    TOP --> CAT
```

*توضیح:* پیکان یعنی «وابسته است به (فراخوانی API همگام)». `iam` و `audit` به‌عنوان cross-cutting توسط همهٔ ماژول‌ها قابل استفاده‌اند و برای خوانایی رسم نشده‌اند؛ خود `iam` و `audit` به هیچ ماژول دامنه‌ای وابسته نیستند. جهت معکوس (مثلاً `ingestion` → `health` برای «داده رسید») فقط از طریق رویداد است.

| ماژول | مسئولیت | مالک schema در PostgreSQL | جداول ClickHouse که می‌نویسد |
|---|---|---|---|
| `catalog` | BusinessService، TechnicalService، Component و زیرنوع‌ها، ServiceType، ExternalRef، Environment، Site، Zone | `catalog` | — |
| `topology` | Dependency، TransactionFlow، پیمایش اثر (recursive CTE)، پیشنهادهای کشف‌شده با تأیید | `topology` | — |
| `calendar` | BusinessCalendar، DayType، ServiceWindow، MaintenanceWindow | `calendar` | — |
| `slo` | تعریف SLO، ErrorBudget، burn rate | `slo` | — (نتایج در `health_states` یا جدول rollup) |
| `ingestion` | دریافت OTLP و batch، نرمال‌سازی، redaction، dedup، نوشتن | — | `metric_samples`، `events`، `txn_summary_1m`، `txn_events` |
| `connectors-control` | ConnectorInstance، Capability، زمان‌بندی، health و lag connectorها، صدور گواهی/ثبت نود [توصیه] | `connectors` | — |
| `events` | مدل رویداد مشترک، dedup و grouping اولیه | (از `outbox` استفاده می‌کند) | `events` (به‌روزرسانی وضعیت با نسخهٔ جدید) |
| `health` | محاسبهٔ `HealthState` با توضیح، propagation به‌عنوان اثر | — | `health_states` |
| `transactions` | TransactionType، OutcomeMapping، محاسبهٔ KPIها، مقایسهٔ upstream/downstream | `catalog` (تعاریف؛ نگاه کنید به نکتهٔ زیر جدول) | خواندن `txn_summary_1m` |
| `correlation` | امتیازدهی شواهد توپولوژی‌محور، رتبه‌بندی CauseHypothesis | — | — |
| `anomaly` | آستانه، baseline فصلی، آشکارسازهای آماری (فاز ۵) | — | `anomalies` |
| `incidents` | Incident، Evidence، CauseHypothesis، OperatorFeedback، timeline | `incidents` | — |
| `notification` | ارسال اعلان، سرکوب در maintenance و expected-inactive | `incidents` (جدول ارسال‌ها) | — |
| `evidence-query` | هماهنگی federated query به Splunk/ES از طریق `connector-runtime`، اعمال redaction و budget | `connectors` (جدول فرمان‌ها) | — |
| `iam` | نگاشت گروه AD/IdP به نقش و scope؛ ارزیابی RBAC | `iam` | — |
| `audit` | ثبت append-only و ارسال به SIEM | `audit` | — |
| `query-api` | OpenAPI عمومی `/api/v1/...`، SSE | — | خواندن |

«نکته برای بازبینی»: spine schema جداگانه‌ای برای `transactions` تعریف نکرده است؛ پیشنهاد این سند نگهداری TransactionType و OutcomeMapping در schema `catalog` با مالکیت ماژول `transactions` است، یا افزودن schema `transactions`. باید در [data-architecture.md](data-architecture.md) و ADR-0007 هم‌راستا شود.

## ۶. پروتکل‌های ارتباطی

| از | به | پروتکل | الگو | امنیت | یادداشت |
|---|---|---|---|---|---|
| مرورگر | `web-ui` | HTTPS | دریافت فایل ایستا | TLS 1.2+ | — |
| مرورگر / `web-ui` | `platform-core(api)` | HTTPS REST (OpenAPI 3.1)، SSE | request/response؛ push زنده با SSE | OIDC bearer token، RBAC | `/api/v1/...` |
| `edge-collector` | `platform-core(ingest)` | OTLP/gRPC یا OTLP/HTTP | push | mTLS | فقط zone → `monitoring-zone` |
| `connector-runtime` | `platform-core(api)` | HTTPS REST | pull پیکربندی | mTLS | `/internal/v1/connectors/{id}/config` |
| `connector-runtime` | `platform-core(api)` | HTTPS long-poll | pull فرمان | mTLS | `/internal/v1/commands` |
| `connector-runtime` | `platform-core(ingest)` | OTLP/HTTP + HTTPS batch | push داده | mTLS | `/internal/v1/ingest/events` |
| `connector-runtime` | سامانهٔ منبع | API بومی هر منبع | pull فقط‌خواندنی | TLS، حساب حداقلی | [ADR-0020](architecture-decision-records/ADR-0020-existing-platform-integration-read-only-pull.md) |
| `platform-core` | `metadata-db` | JDBC | — | TLS، حساب جدا به‌ازای نقش [توصیه] | — |
| `platform-core` | `telemetry-store` | ClickHouse HTTP یا native | insert دسته‌ای، query | TLS | — |
| `platform-core` | `identity-provider` | OIDC (JWKS، token introspection در صورت نیاز) | — | TLS | — |
| ماژول ↔ ماژول | (درون‌فرایند) | فراخوانی Java interface، domain event | همگام / ناهمگام درون‌فرایند | — | outbox برای رویدادهای پایدار |

## ۷. APIهای داخلی و عمومی

### ۷.۱ API عمومی `/api/v1/...`

[توصیه] قرارداد OpenAPI 3.1، نسخه‌دهی semver، تست قرارداد ([ADR-0005](architecture-decision-records/ADR-0005-api-and-event-contracts.md)). گروه‌های اصلی (نمونه، نهایی نیست):

| مسیر | کاربرد |
|---|---|
| `/api/v1/catalog/business-services`، `/technical-services`، `/components`، `/service-types` | CRUD کاتالوگ + import/export (FR-CAT-001..012) |
| `/api/v1/topology/dependencies`، `/topology/graph?root=...&depth=...` | توپولوژی و پیمایش اثر |
| `/api/v1/calendars`، `/maintenance-windows` | تقویم و پنجره‌ها |
| `/api/v1/health/services/{id}` | وضعیت سلامت و توضیح |
| `/api/v1/transactions/{serviceId}/summary?from=&to=` | KPI تراکنش با tier همبستگی |
| `/api/v1/slos`، `/slos/{id}/budget` | SLO و error budget |
| `/api/v1/incidents`، `/incidents/{id}/evidence`، `/incidents/{id}/feedback` | Incident و شواهد |
| `/api/v1/evidence-queries` | ایجاد federated query و دریافت نتیجهٔ redact‌شده (P4) |
| `/api/v1/connectors`، `/connectors/{id}/health` | مدیریت و تشخیص connector |
| `/api/v1/stream` | SSE برای به‌روزرسانی زنده |

### ۷.۲ API داخلی `/internal/v1/...`

فقط از طریق mTLS و با گواهی صادرشده برای نودهای `connector-runtime`/`edge-collector` قابل دسترسی است؛ روی listener جداگانه از API عمومی [توصیه].

| مسیر | فراخوان | کاربرد |
|---|---|---|
| `GET /internal/v1/connectors/{instanceId}/config` | `connector-runtime` | دریافت پیکربندی نسخه‌دار (بدون secret؛ ارجاع به secret در SecretProvider محلی) |
| `POST /internal/v1/connectors/{instanceId}/heartbeat` | `connector-runtime` | گزارش سلامت، lag، capability تأییدشده |
| `GET /internal/v1/commands?runtime={id}&wait=30s` | `connector-runtime` | long-poll فرمان‌ها (evidence query، اجرای آزمایشی اتصال، backfill) |
| `POST /internal/v1/commands/{commandId}/result` | `connector-runtime` | بازگرداندن نتیجهٔ redact‌شده |
| `POST /internal/v1/ingest/events` | `connector-runtime` | ارسال دسته‌ای Event نرمال‌شده، TransactionSummary، TransactionEvent با کلید idempotency |
| OTLP `/v1/metrics`، `/v1/logs` (و gRPC) | `edge-collector`، `connector-runtime` | متریک و لاگ مشتق‌شده |

«نکته برای بازبینی»: spine فقط `/internal/v1/ingest/events` و `/internal/v1/commands` را نام برده است؛ مسیرهای config/heartbeat/result بالا پیشنهاد این سند هستند.

## ۸. رویدادهای درون‌فرایندی و outbox

- [توصیه] رویدادهای غیرحیاتی (مثلاً `CatalogEntityChanged` برای invalidation کش) از event publisher درون‌فرایندی و پس از commit تراکنش منتشر می‌شوند.
- رویدادهایی که از دست رفتنشان حالت نادرست ایجاد می‌کند (مثلاً `MaintenanceWindowStarted`، `IncidentOpened`، `DependencyApproved`، `NotificationRequested`) در جدول `outbox.events` در همان تراکنش PostgreSQL نوشته می‌شوند و توسط نقش `analytics` (یا هر مصرف‌کننده) با `SELECT ... FOR UPDATE SKIP LOCKED` مصرف می‌شوند؛ تحویل at-least-once و مصرف‌کننده idempotent است (NFR-REL-001).
- چون نقش‌ها ممکن است در فرایندهای جدا باشند، outbox راه ارتباط ناهمگام بین‌نقشی است؛ ingest → analytics عمدتاً از طریق خواندن ClickHouse در micro-batch است، نه رویداد.
- در صورت معرفی `event-bus`، outbox به Kafka relay می‌شود و مصرف‌کننده‌ها بدون تغییر قرارداد رویداد مهاجرت می‌کنند.

## ۹. حالت‌های خرابی و تنزل تدریجی

| جزء از کار افتاده | اثر | رفتار تنزل‌یافته [توصیه] | نشانهٔ قابل‌مشاهده |
|---|---|---|---|
| یک سامانهٔ منبع (مثلاً Splunk) | توقف دادهٔ مشتق از آن منبع | connector با backoff تلاش می‌کند؛ داده‌های وابسته پس از آستانهٔ تازگی `UNKNOWN` می‌شوند، نه `OK` یا `CRITICAL` (FR-HLT-003)؛ سایر منابع ادامه دارند | نشان «منبع در دسترس نیست» و lag در صفحهٔ connector |
| `connector-runtime` در یک zone | توقف pull آن zone | پس از بازگشت، backfill از checkpoint تا حد مجاز منبع؛ شکاف داده علامت‌گذاری می‌شود (NFR-REL-002) | heartbeat گم‌شده → هشدار self-monitoring |
| `edge-collector` | توقف دریافت push | فرستنده‌ها بسته به قابلیت خود بافر می‌کنند؛ داده از دست‌رفته به‌صورت gap نمایش داده می‌شود | — |
| `platform-core(ingest)` | نرسیدن داده به ClickHouse | `edge-collector` در صف دیسکی (هدف ≥ ۴ ساعت در Medium، NFR-REL-003) و `connector-runtime` در بافر محلی نگه می‌دارند؛ پس از بازگشت با کلید idempotency بازنویسی | رشد صف در متریک self-telemetry |
| `platform-core(analytics)` | توقف محاسبهٔ سلامت/Incident | UI آخرین وضعیت را با برچسب «قدیمی از زمان X» نشان می‌دهد؛ dashboardهای خام همچنان کار می‌کنند | سن آخرین چرخه در صفحهٔ سلامت پلتفرم |
| `platform-core(api)` | عدم دسترسی UI | ingest و analytics مستقل ادامه می‌دهند؛ اعلان‌ها ارسال می‌شوند | — |
| `telemetry-store` | ingest نمی‌تواند بنویسد؛ کوئری‌ها شکست می‌خورند | ingest backpressure اعمال می‌کند (پاسخ 429/`RESOURCE_EXHAUSTED` OTLP) تا بافرهای لبه نگه دارند؛ کاتالوگ و Incidentها از PostgreSQL قابل مشاهده‌اند | — |
| `metadata-db` | از دست رفتن کاتالوگ/پیکربندی/RBAC | [توصیه] چون RBAC و audit به PostgreSQL وابسته‌اند، API درخواست‌های نوشتنی را رد می‌کند (عملیات بدون audit مجاز نیست) و ingest با کش نگاشت ExternalRef تا مدت محدود ادامه می‌دهد؛ تا HA در فاز ۶ این نقطهٔ تک‌خرابی است | — |
| `identity-provider` | ورود جدید ممکن نیست | نشست‌های جاری تا انقضای token ادامه دارند؛ حساب اضطراری محلی (break-glass) با audit ویژه [توصیه، باز Q-04] | — |
| Elasticsearch/Splunk بانک در حین Incident (R-15) | evidence query و دادهٔ لاگ‌محور ناقص | پلتفرم مستقل از آن‌ها کار می‌کند (NFR-AVL-002)؛ شواهد از منابع دیگر رتبه‌بندی می‌شود و عدم‌قطعیت افزایش می‌یابد | — |

## ۱۰. مقایسهٔ خلاصه: modular monolith در برابر microservices

جزئیات در [ADR-0002](architecture-decision-records/ADR-0002-modular-monolith-with-edge-collectors.md).

| معیار | Modular monolith + edge collectors | Microservices |
|---|---|---|
| هزینهٔ عملیاتی on-prem و آفلاین | پایین: یک artifact هسته + دو نوع جزء لبه | بالا: ده‌ها سرویس، service discovery، tracing داخلی، احتمالاً K8s |
| تناسب با تیم ۴–۸ نفره [فرض A-03] | مناسب | سربار هماهنگی زیاد |
| تراکنش‌پذیری بین ماژول‌ها | تراکنش محلی PostgreSQL + outbox | saga و eventual consistency |
| مقیاس مستقل | با نقش‌های `api`/`ingest`/`analytics` | ریزدانه |
| جداسازی خرابی | در سطح نقش و جزء لبه | ریزدانه |
| خطر فرسایش مرزها | با ArchUnit کنترل می‌شود | ذاتاً جدا |
| مسیر آینده | استخراج ماژول هنگام تحقق trigger | — |

## ۱۱. مرزهای استقرار به‌ازای zone

| Zone | اجزا | ورودی مجاز (فایروال) | خروجی مجاز |
|---|---|---|---|
| `monitoring-zone` | `web-ui`، `platform-core` (همهٔ نقش‌ها)، `metadata-db`، `telemetry-store`، (Keycloak در صورت نیاز) | از user-zone به Load Balancer داخلی پلتفرم (HTTPS)؛ از zoneهای منبع به listener داخلی (mTLS) | به IdP، SMTP/ITSM، SIEM، NTP |
| هر zone منبع (`dmz`، `card-zone`، `core-banking-zone`، `middleware-zone`، ...) | `connector-runtime`، `edge-collector` | از برنامه‌های همان zone به `edge-collector` (OTLP/syslog) | به سامانه‌های منبع همان zone؛ به `monitoring-zone` روی پورت‌های ingest/internal |
| `shared-services-zone` | (خارج از پلتفرم) IdP، SMTP، SIEM، NTP | از `monitoring-zone` | — |

[فرض A-02] استقرار روی Linux VM (RHEL-compatible) با Podman/Docker Compose ([ADR-0016](architecture-decision-records/ADR-0016-offline-deployment-and-artifacts.md)، Blocked Q-02/Q-03).

## ۱۲. مسیر مقیاس Low → Medium → High

اعداد سناریوها همه فرض‌اند ([فرض A-11]، spine §10) و با BM-01..BM-10 جایگزین می‌شوند.

| سناریو | توپولوژی پیشنهادی [توصیه] |
|---|---|
| Low (≈ 2.5k sample/s، 5 event/s) | یک VM برای `platform-core` با هر سه نقش؛ PostgreSQL تک‌نود؛ ClickHouse تک‌نود؛ یک `connector-runtime` + `edge-collector` در هر zone |
| Medium (≈ 33k sample/s، peak 4× ≈ 132k/s) | `api` ×2، `ingest` ×2، `analytics` ×2 (یا ×1 + standby)؛ PostgreSQL primary + replica با Patroni؛ ClickHouse دو replica با Keeper سه‌نودی؛ connector-runtime دوگانه در zoneهای پرحجم |
| High (≈ 200k sample/s، peak 5× ≈ 1M/s، 5k txn event/s) | `ingest` ×N افقی؛ ClickHouse sharded + replicated؛ ارزیابی Kafka بر اساس triggerهای ADR-0010 (مثلاً > ~50k event/s پایدار یا fan-in چندسایتی)؛ احتمالاً جداسازی `ingestion` |

[فرضیه] نرخ ۱۳۲k sample/s در peak Medium با دو نمونه `ingest` و دو replica ClickHouse قابل جذب است؛ BM-01 و BM-07 آن را می‌آزمایند.

## ۱۳. محرک‌های جداسازی یک ماژول به سرویس مستقل

یک ماژول فقط زمانی از `platform-core` جدا می‌شود که حداقل یکی از شرایط زیر با شواهد اندازه‌گیری‌شده برقرار باشد:

1. **نیاز مقیاس ناهمگون**: نقش موجود نمی‌تواند بار آن ماژول را بدون مقیاس‌دادن بی‌مورد سایر ماژول‌ها جذب کند (مثلاً `ingestion` در High).
2. **چرخهٔ انتشار متفاوت**: ماژول باید با آهنگی بسیار متفاوت منتشر شود (مثلاً مدل‌های `anomaly` در فاز ۵).
3. **runtime یا زبان متفاوت**: مثلاً تحلیل آزمایشی با Python در فاز ۵ که فقط در صورت توجیه مجاز است ([ADR-0003](architecture-decision-records/ADR-0003-backend-language-java-spring-boot.md)).
4. **جداسازی امنیتی**: مثلاً `evidence-query` اگر ارزیابی امنیتی نیاز به مرز فرایندی برای دسترسی به نتایج لاگ را نشان دهد.
5. **دامنهٔ خرابی**: اگر خرابی یک ماژول به‌طور مکرر نقش میزبان را مختل کند.
6. **تحقق triggerهای Kafka** در ADR-0010 که استخراج مصرف‌کننده‌ها را آسان می‌کند.

پیش‌شرط استخراج: ماژول از قبل فقط از طریق API عمومی و رویداد outbox تعامل داشته باشد (که ArchUnit تضمین می‌کند)، و schema خود را در PostgreSQL مالک باشد.

## ۱۴. نکته برای بازبینی

- مسیرهای داخلی پیشنهادی (`config`، `heartbeat`، `commands/{id}/result`) و listener جداگانه برای `/internal/v1` باید در ADR-0005 تثبیت شوند.
- مالکیت schema برای TransactionType/OutcomeMapping (بخش ۵).
- رفتار `api` هنگام از دست رفتن `metadata-db` (رد نوشتن، کش محدود) به تصمیم HA در [ADR-0017](architecture-decision-records/ADR-0017-ha-dr-architecture.md) (Blocked Q-05) وابسته است.
