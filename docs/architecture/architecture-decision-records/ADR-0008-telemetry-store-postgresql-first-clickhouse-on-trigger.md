# ADR-0008: Store تله‌متری؛ ابتدا PostgreSQL، ClickHouse با Trigger (Telemetry Store PostgreSQL First, ClickHouse on Trigger)

| ID | Title | Status | Confidence | Date | Deciders |
|---|---|---|---|---|---|
| ADR-0008 | telemetry-store-postgresql-first-clickhouse-on-trigger | Proposed | **Provisional** (PoC-5، BM-02، BM-03) | 2026-10-12 (۱۴۰۵/۰۷/۲۰) | Architecture (pending product owner approval) |

Version: `0.2` — تاریخچه تغییر در انتهای سند.

Related documents: [technology-evaluation.md](../technology-evaluation.md)، [capacity-planning.md](../capacity-planning.md)، [data-architecture.md](../data-architecture.md)، [ADR-0007](ADR-0007-metadata-store-postgresql.md)، [ADR-0018](ADR-0018-data-retention-privacy-capacity-controls.md)، [ADR-0021](ADR-0021-build-buy-reuse-strategy.md)، [product-benchmark.md](../../benchmark/product-benchmark.md)، [build-buy-reuse-analysis.md](../../benchmark/build-buy-reuse-analysis.md)

## Context

- نسخه 0.1 این ADR «ClickHouse به‌عنوان Store واحد همه داده‌های زمانی از ابتدا» را پیشنهاد می‌کرد، با این فرض که پلتفرم متریک خام (۱۵۰k تا ۵M سری) را خودش ingest می‌کند.
- Product Benchmark این فرض را تغییر داد ([ADR-0021](ADR-0021-build-buy-reuse-strategy.md)، گزینه E): Zabbix، SolarWinds، Splunk و Elasticsearch **سامانه مرجع (system of record)** داده خام، هشدار و سلامت زیرساخت می‌مانند. پلتفرم فقط این‌ها را نگه می‌دارد:
  - KPIهای مشتق دقیقه‌ای تراکنش (که با saved search/aggregation **درون** Splunk/ES محاسبه و فقط نتیجه تجمیعی کشیده می‌شود)؛
  - **سری‌های مرتبط با KPI** (KPI-linked series): زیرمجموعه انتخاب‌شده‌ای از itemهای Zabbix و متریک‌های MQ/LB که در قواعد سلامت/همبستگی استفاده می‌شوند؛
  - رویدادهای نرمال‌شده، وضعیت سلامت، ناهنجاری و Incident.
- [توصیه] با این دامنه، حجم Low حدود ۳۳ نمونه/ثانیه + ۲۰ ردیف خلاصه/ثانیه + ۵ رویداد/ثانیه است (به‌جای ۲٫۵k نمونه/ثانیه) — محاسبه در [capacity-planning.md](../capacity-planning.md) (سناریوی Reuse).
- شواهد Benchmark ([product-benchmark.md](../../benchmark/product-benchmark.md)، B2.6):
  - توجیه اصلی ClickHouse داده **رویداد به ازای تراکنش با cardinality بالا** و صدک‌ها در Medium به بالا است؛ PostgreSQL برای حجم کم (تقریباً کمتر از ۱۰ تا ۵۰ میلیون ردیف در روز) کافی و از نظر عملیاتی ساده‌تر است — ارزیابی با اطمینان Medium (research-oss #52).
  - فشرده‌سازی، continuous aggregate و retention policy در TimescaleDB تحت مجوز **TSL** است، نه Apache-2.0 (https://www.tigerdata.com/docs/get-started/choose-your-path/timescaledb-editions، High).
  - downsampling و multi-retention در VictoriaMetrics فقط در نسخه Enterprise است (https://docs.victoriametrics.com/victoriametrics/enterprise/، High).
  - TTL و rollup بومی ClickHouse (https://clickhouse.com/docs/engines/table-engines/mergetree-family/mergetree، High) همچنان بهترین گزینه برای مقیاس بالا است.
- PostgreSQL از قبل برای `metadata-db` انتخاب شده است ([ADR-0007](ADR-0007-metadata-store-postgresql.md))؛ افزودن Store دوم در Low هزینه عملیاتی بدون مزیت اندازه‌گیری‌شده است.

## Decision

1. **PostgreSQL نخست:** سری‌های KPI مشتق، خلاصه دقیقه‌ای تراکنش (`txn_summary_1m`)، سری‌های مرتبط با KPI، رویدادهای نرمال‌شده، `health_states` و `anomalies` در **همان خوشه PostgreSQL** (schema جدا، مثلاً `telemetry`) و در **جدول‌های partition‌شده بر اساس زمان** (partition روزانه یا هفتگی) با bucket زمانی ثابت (۱ دقیقه، ۱ ساعت) نگهداری می‌شوند. حذف داده با drop کردن partition انجام می‌شود (retention ارزان). از TimescaleDB TSL استفاده **نمی‌شود** مگر پس از بررسی مجوز (Q-21).
2. **داده خام wholesale ingest نمی‌شود:** فقط سری‌های مرتبط با KPI (فهرست صریح، نسخه‌دار، به ازای سرویس) کپی می‌شوند. drill-down شواهد برای سایر itemها با کوئری درخواستی به history/trend در Zabbix (یا SolarWinds/Splunk/ES) از طریق `connector-runtime` انجام می‌شود ([ADR-0009](ADR-0009-no-own-raw-log-store-federate-existing.md)، [ADR-0020](ADR-0020-existing-platform-integration-read-only-pull.md)).
3. **ClickHouse فقط با Trigger** به‌عنوان `telemetry-store` (کانتینر deferred/conditional) معرفی می‌شود. Triggerها (هر کدام کافی است):
   - T1: نیاز به **رویداد tokenized تراکنش (L2)** با نرخ پایدار بیش از حدود **۱۰۰ رویداد/ثانیه**؛
   - T2: ردیف‌های KPI مشتق بیش از حدود **۵۰ ردیف/ثانیه پایدار** یا حجم ذخیره KPI برای ۱۳ ماه بیش از حدود **۱ TB**؛
   - T3: ناکامی در اهداف p95 کوئری (NFR-PRF-003) در BM-02.
   - (مقادیر T1..T3 عیناً مطابق spine-benchmark B5 و [build-buy-reuse-analysis.md](../../benchmark/build-buy-reuse-analysis.md) هستند؛ PoC-5 آن‌ها را روی حجم مصنوعی Low/Medium می‌سنجد.)
4. **طراحی آماده مهاجرت:** دسترسی به داده زمانی فقط از لایه repository در `platform-core` (بدون SQL پراکنده در ماژول‌ها)؛ کلید idempotency در هر ردیف (FR-ING-007)؛ مدل منطقی جدول‌ها ([data-architecture.md](../data-architecture.md)) مستقل از Store. نگاشت ClickHouse (MergeTree، TTL، `ReplicatedMergeTree` + Keeper سه‌گرهی در Medium+) به‌عنوان **هدف Trigger** در data-architecture حفظ می‌شود.
5. rollupها با recompute زمان‌بندی‌شده و شماره revision (نه materialized view) تا رفتار در هر دو Store یکسان باشد.
6. دسترسی فقط از `platform-core` با حساب‌های جدا برای نقش‌های نوشتن (`ingest`) و خواندن (`api`/`analytics`)؛ TLS؛ رمزنگاری دیسک.

## Alternatives considered

| گزینه | مزایا | معایب | نتیجه |
|---|---|---|---|
| PostgreSQL نخست، ClickHouse با Trigger (انتخاب) | یک Store stateful در Low؛ مهارت موجود؛ partition برای retention؛ مسیر مهاجرت روشن | سقف حجم پایین‌تر؛ صدک روی داده خام کندتر؛ نیاز به پایش Trigger | **پیشنهاد** |
| ClickHouse از ابتدا (تصمیم 0.1) | فشرده‌سازی و throughput بالا، TTL بومی | Store stateful دوم و مهارت DBA جدید ([باز Q-19]) برای حجمی که در سناریوی Reuse لازم نیست | رد برای اکنون؛ هدف Trigger |
| TimescaleDB | SQL روی PostgreSQL، continuous aggregate | قابلیت‌های کلیدی تحت TSL، نه Apache-2.0 (Q-21) | رد مگر Q-21 TSL را مجاز کند — آنگاه جایگزین اول partitioning دستی |
| VictoriaMetrics (community) | عالی برای متریک pre-aggregated | downsampling/multi-retention فقط Enterprise؛ رویداد/تراکنش Store دوم می‌خواهد | رد |
| Prometheus + Thanos/Mimir | استاندارد متریک | اجزای متعدد، object storage، فقط متریک، Mimir AGPL | رد |
| InfluxDB | سری زمانی | خوشه‌بندی تجاری، تغییرات نسخه/مجوز | رد |
| Elasticsearch/OpenSearch بانک | از قبل موجود | اشتراک failure domain با سامانه پایش‌شده (NFR-AVL-002، R-15)؛ ناکارآمد برای تجمیع متریک | رد ([ADR-0009](ADR-0009-no-own-raw-log-store-federate-existing.md)) |
| فقط کوئری زنده از Zabbix/Splunk/ES بدون ذخیره مشتق | بدون Store | baseline فصلی و SLO ۱۳ ماهه ممکن نیست؛ بار منبع زیاد؛ در Incident منبع ممکن است در دسترس نباشد | رد |

## Rationale

پس از Benchmark، تمایز محصول در لایه معنایی بانکی است، نه در ذخیره متریک خام ([ADR-0021](ADR-0021-build-buy-reuse-strategy.md)). نرخ رکورد ورودی در سناریوی Reuse حدود ۱/۴۰ برآورد 0.1 است و حجم ذخیره در Low حدود ۰٫۲۶–۰٫۵۱ TB (در برابر ۰٫۶۳–۱٫۲۹ TB در own-ingestion فشرده؛ [capacity-planning.md](../capacity-planning.md) §۴.۷) که در توان PostgreSQL قرار می‌گیرد. ClickHouse همچنان پاسخ درست برای رویداد tokenized و KPI پرحجم است؛ بنابراین به‌جای حذف، به Trigger اندازه‌پذیر متصل می‌شود. محاسبه [capacity-planning.md](../capacity-planning.md) نشان می‌دهد Medium با retention ۱۳ ماهه خلاصه دقیقه‌ای احتمالاً T2 را فعال می‌کند، مگر سیاست P2 (retention بر اساس کلاس سری) اعمال شود.

## Consequences

- مثبت: Low = `platform-core` + PostgreSQL + `connector-runtime` (+ exporter MQ)؛ نصب آفلاین و Backup ساده‌تر؛ یک زبان کوئری (SQL) و یک ابزار Backup.
- منفی: کوئری صدک روی بازه طولانی کندتر از ClickHouse است → صدک‌ها به‌صورت از پیش محاسبه‌شده در Splunk/ES یا histogram bucket ذخیره می‌شوند؛ رشد جدول‌ها نیاز به پایش autovacuum و اندازه partition دارد؛ مهاجرت در صورت Trigger کار برنامه‌ریزی‌شده (یک فاز کوتاه در Phase 6) است.
- هزینه عملیاتی: پایش شاخص‌های Trigger (ردیف/ثانیه، اندازه schema `telemetry`، p95 کوئری) در self-monitoring (NFR-OBS-001)؛ اگر Trigger فعال شود، هزینه‌های 0.1 (Keeper، Backup ClickHouse، مهارت DBA) برمی‌گردد.
- اثر روی [ADR-0017](ADR-0017-ha-dr-architecture.md): در Low/Medium بدون ClickHouse، HA داده زمانی همان Patroni/streaming replication PostgreSQL است.

## Evidence that would change this decision

- هر یک از Triggerهای T1..T3 (اندازه‌گیری در PoC-5، BM-02، BM-03 یا در بهره‌برداری).
- Q-06: حجم واقعی بانک بالاتر از Medium یا نیاز قطعی به L2 از ابتدا → ClickHouse از Phase 2.
- Q-16: retention ۱۳ ماهه برای همه خلاصه‌های دقیقه‌ای اجباری باشد → T2 در Medium تقریباً قطعی است.
- Q-21: مجاز شدن TSL → TimescaleDB به‌جای partitioning دستی بررسی شود.
- Q-03: فهرست DBMSهای مجاز بانک PostgreSQL را شامل نشود → بازنگری کامل همراه ADR-0007.

## Related requirements

FR-TXN-003، FR-TXN-004، FR-TXN-006، FR-HLT-001، FR-ANO-002، FR-UI-002، NFR-PRF-001، NFR-PRF-003، NFR-SCL-001، NFR-SCL-003، NFR-PRV-004، NFR-IMP-001

## Related questions

Q-03، Q-06، Q-16، Q-18، Q-19، Q-21، Q-22، Q-28، PoC-5، BM-01، BM-02، BM-03، EXP-03

## تاریخچه تغییر

| Version | Date | تغییر |
|---|---|---|
| 0.1 | 2026-10-11 | نسخه اولیه: «telemetry-store-clickhouse» — ClickHouse به‌عنوان Store واحد از ابتدا. |
| 0.2 | 2026-10-12 | تغییر پس از Product Benchmark: تغییر نام فایل و عنوان؛ PostgreSQL نخست برای سری‌های KPI مشتق؛ ClickHouse با Trigger؛ عدم ingest wholesale متریک خام. |
