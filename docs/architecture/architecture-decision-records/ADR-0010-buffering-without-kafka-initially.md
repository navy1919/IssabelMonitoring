# ADR-0010: بافرینگ بدون Kafka در ابتدا (Buffering Without Kafka Initially)

| ID | Title | Status | Confidence | Date | Deciders |
|---|---|---|---|---|---|
| ADR-0010 | buffering-without-kafka-initially | Proposed | **Provisional** (BM-07، Q-06، Q-09) | 2026-10-11 (۱۴۰۵/۰۷/۱۹) | Architecture (pending product owner approval) |

Related documents: [technology-evaluation.md §۱۰](../technology-evaluation.md)، [capacity-planning.md §۳ و §۹](../capacity-planning.md)، [ADR-0004](ADR-0004-collector-and-connector-architecture.md)

## Context

- الزام: at-least-once + idempotent (NFR-REL-001)، بدون از دست رفتن خاموش داده (NFR-REL-002)، بافر لبه ≥ ۴ ساعت در Medium (NFR-REL-003).
- برآورد ingest: Medium میانگین ~۳۳٫۷۵k رکورد/ثانیه، اوج ~۱۳۵k؛ High میانگین ~۲۰۷k، اوج ~۱٫۰۳M ([capacity-planning.md](../capacity-planning.md)).
- Kafka یک سیستم توزیع‌شده stateful اضافی با هزینه عملیاتی بالا است؛ تیم ۴ تا ۸ نفره ([فرض A-03]).
- تحلیل در micro-batch هر ۳۰ تا ۶۰ ثانیه است، نه streaming زیرثانیه‌ای.

## Decision

1. در فازهای اولیه **event bus وجود ندارد**. دوام با این اجزا تأمین می‌شود:
   - صف دیسکی پایدار در `edge-collector` (`file_storage`) و `connector-runtime` (صف محلی دیسکی) با ظرفیت ≥ ۴ ساعت حجم Zone؛
   - batching و backpressure در نقش `ingest` (پاسخ OTLP retryable هنگام اشباع، تا لبه نگه دارد)؛
   - درج idempotent در ClickHouse با کلید dedup؛
   - **outbox** در PostgreSQL برای رویدادهای دامنه پایدار بین ماژول‌ها.
2. **Triggerهای معرفی Kafka (KRaft)** به‌عنوان `event-bus`:
   - پایدار بیش از حدود **۵۰ هزار رکورد/ثانیه** در ورودی `ingest` (تفسیر پیشنهادی: میانگین ۱۵ دقیقه‌ای کل رکوردها — «نکته برای بازبینی» زیر)؛
   - نیاز به **replay** یا **چند مصرف‌کننده مستقل** روی جریان خام؛
   - **fan-in چندسایتی** (سناریوی 2 active + DR)؛
   - نیاز به تحمل قطعی نقش `ingest`/Store بیش از ظرفیت بافر لبه (≥ ۴ ساعت).
3. طراحی آماده: کلید idempotency در همه رکوردها، قرارداد CloudEvents/OTLP مستقل از انتقال، و جداسازی «دریافت» از «نوشتن» در ماژول `ingestion` تا Kafka بعداً بین آن دو قرار گیرد.

## Alternatives considered

| گزینه | W-now | W-scale | دلیل |
|---|---|---|---|
| بدون Bus (انتخاب فعلی) | 4.10 | 3.05 | کمترین پیچیدگی؛ کافی برای Low/Medium |
| Kafka KRaft (انتخاب آینده) | 3.75 | 4.50 | اکوسیستم، replay، Apache 2.0 |
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

- هر یک از triggerهای بالا؛ سناریوی High به احتمال زیاد trigger اول را فعال می‌کند.
- BM-07 نشان دهد backpressure از ingest به لبه در اوج پایدار نیست.
- **Q-09:** اگر بانک خوشه Kafka تحت بهره‌برداری با تیم مسئول داشته باشد، تحلیل حساسیت Kafka را حتی اکنون برنده می‌کند (4.55 در برابر 4.10) — مشروط به ارزیابی اشتراک failure domain (NFR-AVL-002).

## نکته برای بازبینی

واحد «~50k events/s» در spine تعریف نشده است. پیشنهاد: «کل رکوردهای ورودی (نمونه متریک + رویداد + ردیف تراکنش) در نقش `ingest`، میانگین متحرک ۱۵ دقیقه‌ای». با این تعریف Medium (~۳۴k) زیر و High (~۲۰۷k) بالای آستانه است.

## Related requirements

FR-ING-007، FR-ING-008، FR-ING-009، NFR-REL-001، NFR-REL-002، NFR-REL-003، NFR-SCL-002، NFR-SCL-003، NFR-OBS-002

## Related questions

Q-06، Q-09، Q-05، BM-07
