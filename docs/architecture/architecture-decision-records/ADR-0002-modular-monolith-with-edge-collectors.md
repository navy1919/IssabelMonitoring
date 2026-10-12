# ADR-0002: Monolith ماژولار با جمع‌آورنده‌های لبه (Modular Monolith with Edge Collectors)

| ID | Title | Status | Confidence | Date | Deciders |
|---|---|---|---|---|---|
| ADR-0002 | modular-monolith-with-edge-collectors | Proposed | Firm | 2026-10-11 (۱۴۰۵/۰۷/۱۹) | Architecture (pending product owner approval) |

Version: `0.2` — تاریخچه تغییر در انتهای سند.

Related documents: [container-architecture.md](../container-architecture.md)، [ADR-0004](ADR-0004-collector-and-connector-architecture.md)، [ADR-0010](ADR-0010-buffering-without-kafka-initially.md)، [capacity-planning.md](../capacity-planning.md)

## Context

- [واقعیت F-02] معماری سازمانی با تحویل تدریجی و **بدون پیچیدگی غیرضروری**؛ on-premises، بدون اینترنت.
- [فرض A-03] تیم ۴ تا ۸ مهندس؛ [فرض A-02] ماشین مجازی Linux، بدون Kubernetes تضمین‌شده.
- دامنه‌های محصول (کاتالوگ، Topology، تقویم، SLO، تراکنش، همبستگی، …) به‌شدت به هم وابسته‌اند (مثلاً همبستگی به Topology، تقویم و SLO نیاز دارد) و مرزهای آن‌ها در Phase 0 هنوز پایدار نیست.
- جمع‌آوری باید در هر Zone شبکه، با اتصال فقط خروجی به monitoring-zone انجام شود (NFR-SEC-010).

## Decision

1. هسته محصول یک **monolith ماژولار** به نام `platform-core` است: یک artifact واحد که با پیکربندی در نقش‌های `api`، `ingest` و `analytics` (هر ترکیبی) اجرا می‌شود.
2. ماژول‌ها (`catalog`، `topology`، `calendar`، `slo`، `ingestion`، `connectors-control`، `health`، `transactions`، `events`، `correlation`، `anomaly`، `incidents`، `notification`، `evidence-query`، `iam`، `audit`، `query-api`) فقط از طریق interfaceهای عمومی و رویدادهای دامنه درون‌پردازه‌ای با هم ارتباط دارند؛ رویدادهای پایدار بین ماژولی از طریق جدول outbox در PostgreSQL. مرزها با تست‌های ArchUnit در CI اعمال می‌شوند (NFR-MNT-001).
3. اجزای لبه جداگانه مستقر می‌شوند: `connector-runtime` (Java، pull از APIهای فروشنده، با Prometheus scrape برای exporter `mq_prometheus`) به ازای هر Zone؛ `edge-collector` (OTel Collector) پس از Product Benchmark **اختیاری** است و فقط جایی مستقر می‌شود که endpointهای Prometheus/OTLP در Zoneی باید scrape شوند که `connector-runtime` به آن دسترسی ندارد، یا برای instrumentation آینده ([ADR-0004](ADR-0004-collector-and-connector-architecture.md)).
4. هر ماژول مالک جداول خود است (schema جداگانه در PostgreSQL)؛ دسترسی مستقیم به جداول ماژول دیگر ممنوع است.

## Alternatives considered

| گزینه | مزایا | معایب | نتیجه |
|---|---|---|---|
| Monolith ماژولار + لبه (انتخاب) | یک استقرار، تراکنش محلی، refactor ارزان مرزها، عملیات ساده | نیاز به انضباط مرز ماژول؛ مقیاس به ازای نقش نه به ازای ماژول | **پذیرفته** |
| Microservices از ابتدا | مقیاس و استقرار مستقل | ده‌ها سرویس، service discovery، tracing توزیع‌شده، ارکستراسیون — برای تیم ۴ تا ۸ و محیط آفلاین بیش از حد | رد |
| Monolith بدون مرز | سریع‌ترین شروع | فرسایش معماری، استخراج بعدی دشوار | رد |
| جمع‌آوری مرکزی بدون لبه | اجزای کمتر | نیاز به اتصال ورودی به Zoneها یا باز کردن firewall از monitoring-zone به همه منابع — مغایر NFR-SEC-010 | رد |
| Serverless/FaaS | — | در on-prem بدون پلتفرم آماده موضوعیت ندارد | رد |

## Rationale

مقیاس‌پذیری لازم (حتی سناریوی High، [capacity-planning.md](../capacity-planning.md)) با مقیاس افقی نقش‌ها (`ingest`، `analytics`) و sharding در `telemetry-store` (فقط پس از فعال شدن trigger [ADR-0008](ADR-0008-telemetry-store-postgresql-first-clickhouse-on-trigger.md)؛ تا آن زمان PostgreSQL) تأمین می‌شود و نیازی به تفکیک فرایندی به ازای ماژول نیست. نقش‌ها مزیت اصلی microservices (مقیاس و ایزولاسیون بار ingest از API) را بدون هزینه آن می‌دهند. ماژول‌های با مرز اعمال‌شده در صورت نیاز آینده قابل استخراج‌اند.

## Consequences

- مثبت: یک pipeline ساخت، یک بسته نصب آفلاین برای هسته، اشکال‌زدایی ساده، تراکنش ACID درون ماژول.
- منفی: نسخه‌های همه ماژول‌ها با هم منتشر می‌شوند؛ خطای حافظه در یک ماژول نقش را متأثر می‌کند (کاهش با جداسازی نقش‌ها روی گره‌های جدا در Medium+).
- هزینه عملیاتی: در Low دو گره همه‌نقشه؛ در Medium/High گره‌های جدا برای هر نقش. اجزای لبه به ازای هر Zone حداقل ۱ و ترجیحاً ۲ گره.
- لازم: T-01-01 (اسکلت مخزن با ArchUnit) پس از رفع انسداد ADR-0003.

## Evidence that would change this decision

- نیاز به چرخه انتشار مستقل یک ماژول توسط تیم جداگانه (مثلاً `anomaly` توسط تیم داده) → استخراج همان ماژول.
- BM-07/BM-08 نشان دهد یک ماژول منابع نقش را اشباع می‌کند و جداسازی نقش کافی نیست.
- وجود پلتفرم Kubernetes/OpenShift بالغ داخلی ([باز Q-02]) هزینه استقرار چندسرویسی را کاهش می‌دهد ولی به‌تنهایی دلیل تغییر نیست.

## Related requirements

NFR-MNT-001، NFR-MNT-004، NFR-SCL-002، NFR-SCL-003، NFR-SEC-010، NFR-DEP-002

## Related questions

Q-01، Q-02، Q-03، R-04، R-07

## تاریخچه تغییر

| Version | Date | تغییر |
|---|---|---|
| 0.1 | 2026-10-11 | نسخه اولیه. |
| 0.2 | 2026-10-12 | هم‌راستاسازی پس از Product Benchmark: `edge-collector` اختیاری (ADR-0004)؛ sharding `telemetry-store` فقط پس از trigger ADR-0008. نام فایل برای حفظ ارجاع‌ها تغییر نکرد. |
