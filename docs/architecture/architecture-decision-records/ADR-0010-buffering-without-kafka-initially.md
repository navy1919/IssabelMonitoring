# ADR-0010: بافرینگ بدون Kafka در ابتدا (Buffering Without Kafka Initially)

| ID | Title | Status | Confidence | Date | Deciders |
|---|---|---|---|---|---|
| ADR-0010 | buffering-without-kafka-initially | Proposed | **Provisional** (BM-07، Q-06، Q-09) | 2026-10-11 (۱۴۰۵/۰۷/۱۹)؛ بازنگری 2026-10-12 | Architecture (pending product owner approval) |

Version: `0.2` — تاریخچه تغییر در انتهای سند.

Related documents: [technology-evaluation.md §۱۰](../technology-evaluation.md)، [capacity-planning.md §۳ و §۹](../capacity-planning.md)، [ADR-0004](ADR-0004-collector-and-connector-architecture.md)، [ADR-0008](ADR-0008-telemetry-store-postgresql-first-clickhouse-on-trigger.md)، [ADR-0021](ADR-0021-build-buy-reuse-strategy.md)، [product-benchmark.md](../../benchmark/product-benchmark.md)

## Context

- الزام: at-least-once + idempotent (NFR-REL-001)، بدون از دست رفتن خاموش داده (NFR-REL-002)، بافر لبه ≥ ۴ ساعت در Medium (NFR-REL-003).
- برآورد ingest: Medium میانگین ~۳۳٫۷۵k رکورد/ثانیه، اوج ~۱۳۵k؛ High میانگین ~۲۰۷k، اوج ~۱٫۰۳M ([capacity-planning.md](../capacity-planning.md)).
- Kafka یک سیستم توزیع‌شده stateful اضافی با هزینه عملیاتی بالا است؛ تیم ۴ تا ۸ نفره ([فرض A-03]).
- تحلیل در micro-batch هر ۳۰ تا ۶۰ ثانیه است، نه streaming زیرثانیه‌ای.
- **پس از Product Benchmark (0.2):** با راهبرد Reuse-first ([ADR-0021](ADR-0021-build-buy-reuse-strategy.md)) پلتفرم متریک خام را wholesale ingest نمی‌کند ([ADR-0008](ADR-0008-telemetry-store-postgresql-first-clickhouse-on-trigger.md)). ورودی `ingest` در سناریوی Reuse حدود ۵۸ رکورد/ثانیه در Low و حدود ۹۲۰ رکورد/ثانیه در Medium برآورد می‌شود ([capacity-planning.md](../capacity-planning.md)) — دو تا سه مرتبه بزرگی زیر آستانه Kafka.
- شواهد Benchmark ([product-benchmark.md](../../benchmark/product-benchmark.md)، B2.6):
  - Kafka 4.0 (مارس 2025) نخستین نسخه فقط-KRaft است؛ broker به Java 17 نیاز دارد (https://kafka.apache.org/blog/2025/03/18/apache-kafka-4.0.0-release-announcement/، High) — یعنی حذف ZooKeeper پیچیدگی را کم کرده ولی یک خوشه stateful سه‌گرهی همچنان لازم است.
  - صف پایدار `sending_queue` با افزونه `file_storage` در OpenTelemetry Collector موارد صف‌شده را پس از crash دوباره ارسال می‌کند (https://github.com/open-telemetry/opentelemetry-collector/blob/main/exporter/exporterhelper/README.md، High).
  - Kafka فقط وقتی توجیه دارد که چند مصرف‌کننده مستقل، replay، بافر فراتر از `file_storage` یا fan-out لاگ پرحجم لازم باشد؛ در حجم کم تا متوسط با یک مصرف‌کننده، صف پایدار کافی است (research-oss #49، ارزیابی با اطمینان Medium).

## Decision

1. **Kafka در حال حاضر رد می‌شود** (نه فقط «به تعویق می‌افتد»): در فازهای ۱ تا ۵ **event bus وجود ندارد** و Kafka در دامنه هیچ فازی نیست مگر Triggerهای زیر با اندازه‌گیری فعال شوند (Phase 6 فقط در صورت Trigger). دوام با این اجزا تأمین می‌شود:
   - صف دیسکی پایدار در `connector-runtime` (صف محلی دیسکی) و — در صورت استقرار اختیاری — `edge-collector` (`file_storage`) با ظرفیت ≥ ۴ ساعت حجم Zone؛
   - batching و backpressure در نقش `ingest` (پاسخ OTLP retryable هنگام اشباع، تا لبه نگه دارد)؛
   - درج idempotent در Store تله‌متری (PostgreSQL نخست؛ ClickHouse در صورت Trigger [ADR-0008](ADR-0008-telemetry-store-postgresql-first-clickhouse-on-trigger.md)) با کلید dedup؛
   - **outbox** در PostgreSQL برای رویدادهای دامنه پایدار بین ماژول‌ها.
2. **Triggerهای معرفی Kafka (KRaft)** به‌عنوان `event-bus`:
   - پایدار بیش از حدود **۵۰ هزار رکورد/ثانیه** در ورودی `ingest`؛ **تعریف واحد:** کل رکوردهای ورودی به نقش `ingest` (نمونه متریک + رویداد + ردیف تراکنش)، **میانگین متحرک ۱۵ دقیقه‌ای**؛
   - نیاز به **replay** یا **چند مصرف‌کننده مستقل** روی جریان خام؛
   - **fan-in چندسایتی** (سناریوی 2 active + DR)؛
   - نیاز به تحمل قطعی نقش `ingest`/Store بیش از ظرفیت بافر لبه (≥ ۴ ساعت).
3. طراحی آماده: کلید idempotency در همه رکوردها، قرارداد CloudEvents/OTLP مستقل از انتقال، و جداسازی «دریافت» از «نوشتن» در ماژول `ingestion` تا Kafka بعداً بین آن دو قرار گیرد.

## Alternatives considered

| گزینه | W-now | W-scale | دلیل |
|---|---|---|---|
| بدون Bus (انتخاب فعلی) | 4.10 | 3.05 | کمترین پیچیدگی؛ کافی برای Low/Medium؛ در سناریوی Reuse با فاصله زیاد کافی |
| Kafka KRaft | 3.75 | 4.50 | **رد برای اکنون** (شواهد Benchmark B2.6)؛ اکوسیستم، replay، Apache 2.0 — گزینه آینده فقط با Trigger |
| Redpanda | 3.50 | 4.20 | BSL (Q-21) |
| NATS JetStream | 4.05 | 4.05 | ساده‌تر، اکوسیستم کمتر برای ClickHouse |
| RabbitMQ Streams | 3.45 | 3.60 | throughput و replay کمتر |

## Rationale

در Low/Medium هیچ‌یک از triggerها فعال نیست و Kafka هزینه عملیاتی (حداقل ۳ broker + ۳ controller، مدیریت دیسک، ارتقا، امنیت) بدون مزیت اندازه‌گیری‌شده اضافه می‌کند. با طراحی idempotent، افزودن Kafka بعداً بازطراحی نمی‌خواهد (NFR-SCL-003).

## Consequences

- مثبت: دو Store stateful به‌جای سه؛ نصب آفلاین ساده‌تر.
- منفی: replay فقط از لبه و محدود به بافر؛ هر مصرف‌کننده جدید جریان خام باید در `ingest` اضافه شود؛ در قطعی طولانی‌تر از بافر، داده از دست می‌رود (اما گپ نمایان است — NFR-REL-002).
- هزینه عملیاتی: پایش درصد پر بودن بافر لبه و lag (NFR-OBS-002)؛ دیسک بافر در هر گره لبه (Medium ۵۰ تا ۱۰۰ GB).

## Evidence that would change this decision

- هر یک از triggerهای بالا؛ سناریوی High با ingest خام (own-ingestion) به احتمال زیاد trigger اول را فعال می‌کند؛ در سناریوی Reuse حتی High بعید است به آستانه برسد ([capacity-planning.md](../capacity-planning.md)).
- شکست راهبرد Reuse (بازگشت به گزینه B در [ADR-0021](ADR-0021-build-buy-reuse-strategy.md)) و ingest متریک خام توسط پلتفرم → بازبینی همزمان این ADR و ADR-0008.
- BM-07 نشان دهد backpressure از ingest به لبه در اوج پایدار نیست.
- **Q-09:** اگر بانک خوشه Kafka تحت بهره‌برداری با تیم مسئول داشته باشد، تحلیل حساسیت Kafka را حتی اکنون برنده می‌کند (4.55 در برابر 4.10) — مشروط به ارزیابی اشتراک failure domain (NFR-AVL-002).

## نکته برای بازبینی

تعریف واحد Trigger اول («کل رکوردهای ورودی به `ingest`، میانگین متحرک ۱۵ دقیقه‌ای») اکنون در متن تصمیم آمده است. با سناریوی own-ingestion، Medium (~۳۴k) زیر و High (~۲۰۷k) بالای آستانه است؛ با سناریوی Reuse هر سه سناریو زیر آستانه‌اند.

## Related requirements

FR-ING-007، FR-ING-008، FR-ING-009، NFR-REL-001، NFR-REL-002، NFR-REL-003، NFR-SCL-002، NFR-SCL-003، NFR-OBS-002

## Related questions

Q-06، Q-09، Q-05، BM-07

## تاریخچه تغییر

| Version | Date | تغییر |
|---|---|---|
| 0.1 | 2026-10-11 | نسخه اولیه. |
| 0.2 | 2026-10-12 | تغییر پس از Product Benchmark: Kafka «رد برای اکنون» با شواهد Benchmark؛ تعریف واحد Trigger در متن تصمیم؛ edge-collector اختیاری؛ Store تله‌متری مطابق ADR-0008 جدید. |
