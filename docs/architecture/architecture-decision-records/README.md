# نمایه تصمیم‌های معماری (Architecture Decision Records Index)

| فیلد | مقدار |
|---|---|
| Document ID | `ARC-ADR-INDEX` |
| Version | `0.1` |
| Status | `Draft – Phase 0 – awaiting approval` |
| Date | `2026-10-11` (۱۴۰۵/۰۷/۱۹) |
| Owner | `Architecture` |
| Related documents | [technology-evaluation.md](../technology-evaluation.md)، [capacity-planning.md](../capacity-planning.md)، [container-architecture.md](../container-architecture.md)، [open-questions-and-assumptions.md](../open-questions-and-assumptions.md) |

قالب، چرخه عمر و معنای Status/Confidence در [ADR-0001](ADR-0001-record-architecture-decisions.md) تعریف شده است. خلاصه:
- **Status:** `Proposed` → `Accepted` | `Rejected`؛ `Accepted` → `Superseded` | `Deprecated`. در Phase 0 فقط ADR-0001 پذیرفته شده و بقیه `Proposed` هستند تا جلسه بازبینی تصمیم پس از M0.
- **Confidence:** `Firm` (مستقل از پرسش باز/بنچمارک)، `Provisional` (وابسته به BM/EXP)، `Blocked` (وابسته به پاسخ Q-xx).

## فهرست

| ID | عنوان | Status | Confidence | وابستگی | خلاصه تصمیم |
|---|---|---|---|---|---|
| [ADR-0001](ADR-0001-record-architecture-decisions.md) | record-architecture-decisions | **Accepted** | Firm | — | ثبت تصمیم‌ها به‌صورت ADR در مخزن؛ قالب و چرخه عمر |
| [ADR-0002](ADR-0002-modular-monolith-with-edge-collectors.md) | modular-monolith-with-edge-collectors | Proposed | Firm | — | `platform-core` monolith ماژولار با نقش‌های `api`/`ingest`/`analytics` + اجزای لبه به ازای Zone |
| [ADR-0003](ADR-0003-backend-language-java-spring-boot.md) | backend-language-java-spring-boot | Proposed | **Blocked** | Q-01 | Java 21 LTS + Spring Boot 3.x |
| [ADR-0004](ADR-0004-collector-and-connector-architecture.md) | collector-and-connector-architecture | Proposed | Provisional | BM-07، EXP-02 | OTel Collector برای پروتکل‌های استاندارد + Java `connector-runtime` برای APIهای فروشنده؛ فقط خروجی |
| [ADR-0005](ADR-0005-api-and-event-contracts.md) | api-and-event-contracts | Proposed | Firm | — | OpenAPI 3.1، OTLP، CloudEvents + JSON Schema، semver، contract test |
| [ADR-0006](ADR-0006-canonical-schemas-otel-plus-bank-namespace.md) | canonical-schemas-otel-plus-bank-namespace | Proposed | Firm | — | OTel semantic conventions + فضای نام `bank.*` |
| [ADR-0007](ADR-0007-metadata-store-postgresql.md) | metadata-store-postgresql | Proposed | Firm | — | PostgreSQL؛ Topology با adjacency + CTE بازگشتی؛ بدون Graph DB |
| [ADR-0008](ADR-0008-telemetry-store-clickhouse.md) | telemetry-store-clickhouse | Proposed | Provisional | BM-01..BM-03 | ClickHouse به‌عنوان Store واحد تله‌متری |
| [ADR-0009](ADR-0009-no-own-raw-log-store-federate-existing.md) | no-own-raw-log-store-federate-existing | Proposed | Firm (P1–P5) | Q-22، BM-04 | بدون Store لاگ خام؛ query فدرال روی Splunk/ES |
| [ADR-0010](ADR-0010-buffering-without-kafka-initially.md) | buffering-without-kafka-initially | Proposed | Provisional | Q-06، Q-09، BM-07 | صف دیسکی لبه + batching + outbox؛ Kafka با trigger |
| [ADR-0011](ADR-0011-topology-model-and-dependency-discovery.md) | topology-model-and-dependency-discovery | Proposed | Provisional | EXP-01، EXP-02 | Topology دستی سپس کشف با provenance/confidence/approval |
| [ADR-0012](ADR-0012-correlation-engine-design.md) | correlation-engine-design | Proposed | Firm | — | همبستگی قطعی، توضیح‌پذیر و توپولوژی‌آگاه؛ بدون ML در شروع |
| [ADR-0013](ADR-0013-analytics-staging-rules-statistics-ml.md) | analytics-staging-rules-statistics-ml | Proposed | Firm | — | آستانه → baseline فصلی → آمار مقاوم → چندمتغیره → پیش‌بینی → ML با دروازه ارزیابی |
| [ADR-0014](ADR-0014-frontend-react-echarts-cytoscape.md) | frontend-react-echarts-cytoscape | Proposed | Provisional | BM-10، Q-01، Q-21 | React + TypeScript، ECharts، Cytoscape.js؛ RTL/جلالی |
| [ADR-0015](ADR-0015-authentication-authorization-audit.md) | authentication-authorization-audit | Proposed | **Blocked** | Q-04 | OIDC (IdP بانک یا Keycloak + AD)، RBAC با scope، audit append-only |
| [ADR-0016](ADR-0016-offline-deployment-and-artifacts.md) | offline-deployment-and-artifacts | Proposed | **Blocked** | Q-02، Q-03 | OCI روی VM با Podman/Compose؛ بسته آفلاین امضاشده با SBOM |
| [ADR-0017](ADR-0017-ha-dr-architecture.md) | ha-dr-architecture | Proposed | **Blocked** | Q-05 | active-active درون سایت، active-passive DR، Patroni، ClickHouse RF=2 + Keeper |
| [ADR-0018](ADR-0018-data-retention-privacy-capacity-controls.md) | data-retention-privacy-capacity-controls | Proposed | Firm (حریم خصوصی) / Provisional (retention) | Q-16، Q-10 | ممنوعیت داده حساس، HMAC، redaction دولایه، retention به ازای کلاس |
| [ADR-0019](ADR-0019-transaction-correlation-tiers.md) | transaction-correlation-tiers | Proposed | Firm | — | سطوح L0..L3 با ثبت tier روی هر متریک |
| [ADR-0020](ADR-0020-existing-platform-integration-read-only-pull.md) | existing-platform-integration-read-only-pull | Proposed | Firm | — | API فقط‌خواندنی و pull با Query budget؛ webhook اختیاری؛ بدون تغییر پیکربندی منابع |

## جمع‌بندی وضعیت

| Confidence | تعداد | ADRها |
|---|---|---|
| Firm | 10 (+ بخش حریم خصوصی ADR-0018) | 0001، 0002، 0005، 0006، 0007، 0009، 0012، 0013، 0019، 0020 و حریم خصوصی 0018 |
| Provisional | 5 (+ بخش retention ADR-0018) | 0004، 0008، 0010، 0011، 0014 |
| Blocked | 4 | 0003 (Q-01)، 0015 (Q-04)، 0016 (Q-02/Q-03)، 0017 (Q-05) |

**پیش‌نیاز اولین کار پیاده‌سازی (T-01-01):** رفع انسداد ADR-0003 (Q-01). ADRهای Blocked دیگر در جلسه بازبینی تصمیم پس از M0 بررسی می‌شوند.

## نمودار وابستگی تصمیم‌ها

```mermaid
flowchart TD
  A2[ADR-0002 modular monolith] --> A3[ADR-0003 Java]
  A2 --> A4[ADR-0004 collectors]
  A4 --> A20[ADR-0020 read-only pull]
  A4 --> A10[ADR-0010 no Kafka initially]
  A20 --> A9[ADR-0009 federate logs]
  A9 --> A8[ADR-0008 ClickHouse]
  A10 --> A8
  A2 --> A7[ADR-0007 PostgreSQL]
  A7 --> A11[ADR-0011 topology]
  A11 --> A12[ADR-0012 correlation]
  A12 --> A13[ADR-0013 analytics staging]
  A6[ADR-0006 schemas] --> A19[ADR-0019 txn tiers]
  A18[ADR-0018 privacy] --> A19
  A5[ADR-0005 contracts] --> A6
  A15[ADR-0015 IAM] --> A14[ADR-0014 frontend]
  A16[ADR-0016 offline deploy] --> A17[ADR-0017 HA/DR]
  A8 --> A17
  A7 --> A17
```

توضیح: پیکان یعنی «تصمیم مقصد بر فرض تصمیم مبدأ بنا شده است»؛ تغییر یک ADR باید ADRهای پایین‌دست آن را بازبینی کند.
