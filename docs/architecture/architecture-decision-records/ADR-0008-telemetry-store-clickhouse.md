# ADR-0008: ClickHouse به‌عنوان Store تله‌متری (Telemetry Store ClickHouse)

| ID | Title | Status | Confidence | Date | Deciders |
|---|---|---|---|---|---|
| ADR-0008 | telemetry-store-clickhouse | Proposed | **Provisional** (BM-01، BM-02، BM-03) | 2026-10-11 (۱۴۰۵/۰۷/۱۹) | Architecture (pending product owner approval) |

Related documents: [technology-evaluation.md §۸ و §۹](../technology-evaluation.md)، [capacity-planning.md](../capacity-planning.md)، [data-architecture.md](../data-architecture.md)، [ADR-0018](ADR-0018-data-retention-privacy-capacity-controls.md)

## Context

- پلتفرم باید متریک، rollup، رویداد نرمال‌شده، خلاصه تراکنش، رویداد tokenized تراکنش و تاریخچه سلامت/ناهنجاری را نگه دارد و کوئری‌هایی ترکیبی (group-by با cardinality بالا، join با ابعاد کاتالوگ، مقایسه upstream/downstream) اجرا کند.
- برآورد حجم: Medium حدود ۴٫۵ تا ۹٫۴ TB به ازای replica با retention مبنا؛ High حدود ۲۴ تا ۵۱ TB ([capacity-planning.md](../capacity-planning.md)) — همه پیش از بنچمارک.
- اصل «یک Store برای همه داده‌های زمانی» برای جلوگیری از Storeهای هم‌پوشان.

## Decision

1. **ClickHouse** (فقط شاخه LTS) به‌عنوان `telemetry-store` واحد برای همه کلاس‌های داده زمانی.
2. Low: یک گره (RF=1) با Backup روزانه. Medium+: `ReplicatedMergeTree` با RF=2 و **ClickHouse Keeper** سه‌گرهی.
3. rollupها با Materialized View؛ retention با `TTL` به ازای جدول/کلاس داده؛ partition زمانی برای حذف ارزان.
4. طراحی آماده sharding از ابتدا: کلید sharding منطقی (`bank.service.id` یا hash موجودیت)، دسترسی از طریق جدول Distributed/لایه repository.
5. درج دسته‌ای (batch) از نقش `ingest` با کلید dedup برای idempotency (FR-ING-007).
6. دسترسی فقط از `platform-core` (کاربران پایگاه با حداقل دسترسی: نقش نوشتن ingest، نقش خواندن api/analytics)؛ TLS؛ رمزنگاری دیسک.

## Alternatives considered

| گزینه | امتیاز | دلیل اصلی |
|---|---|---|
| ClickHouse (انتخاب) | 4.36 | SQL ستونی، فشرده‌سازی، TTL، یک Store برای همه کلاس‌ها |
| VictoriaMetrics | 3.76 | عالی برای متریک ولی رویداد/تراکنش نیازمند Store دوم؛ برخی قابلیت‌ها Enterprise |
| Prometheus + Thanos/Mimir | 3.06 | اجزای متعدد، object storage، فقط متریک، Mimir AGPL |
| TimescaleDB | 3.62 | SQL روی PostgreSQL؛ قابلیت‌های کلیدی تحت TSL (Q-21)؛ مقیاس افقی محدود |
| InfluxDB | 2.91 | خوشه‌بندی تجاری، تغییرات نسخه/مجوز |
| Elasticsearch/OpenSearch | — | برای متریک و تجمیع پرحجم ناکارآمدتر و پرهزینه‌تر در دیسک/RAM؛ رد در ADR-0009 |

## Rationale

ClickHouse تنها گزینه‌ای است که همه کلاس‌های داده را با SQL پوشش می‌دهد و در فشرده‌سازی و throughput رتبه بالا دارد. تحلیل حساسیت نشان می‌دهد نتیجه فقط وقتی برمی‌گردد که هم بنچمارک ClickHouse ضعیف باشد و هم تصمیم به تفکیک رویداد/تراکنش به Store دیگر گرفته شود.

## Consequences

- مثبت: یک Store، یک زبان کوئری (SQL) برای `analytics` و `query-api`.
- منفی: مدیریت merge/parts، حساسیت به insertهای کوچک (نیاز به batch)، چرخه انتشار سریع (فقط LTS).
- هزینه عملیاتی: Keeper سه‌گرهی در Medium+؛ Backup (فرمان `BACKUP` بومی یا ابزار clickhouse-backup — تأیید شود) روی ذخیره‌ساز مستقل؛ پایش parts/merge/replication lag؛ مهارت DBA جدید برای تیم بانک ([باز Q-19]).

## Evidence that would change this decision

- BM-01: ingest پایدار کمتر از ۲× اوج Medium روی سخت‌افزار مرجع یا بایت/نمونه بسیار بیشتر از فرض (مثلاً > ۶ بایت).
- BM-02: p95 کوئری داشبورد بالاتر از NFR-PRF-003 حتی با rollup.
- BM-03: ناتوانی در تجمیع رویداد تراکنش در ≤ ۶۰ ثانیه.
- سیاست مجوز یا ممنوعیت محصول (Q-21).
- در صورت شکست: VictoriaMetrics برای متریک + ClickHouse یا PostgreSQL برای رویداد/تراکنش (پذیرش دو Store).

## Related requirements

FR-TXN-003، FR-TXN-004، FR-TXN-006، FR-HLT-001، FR-ANO-002، FR-UI-002، NFR-PRF-001، NFR-PRF-003، NFR-SCL-001، NFR-SCL-003، NFR-PRV-004

## Related questions

Q-06، Q-16، Q-18، Q-19، Q-21، BM-01، BM-02، BM-03، EXP-03
