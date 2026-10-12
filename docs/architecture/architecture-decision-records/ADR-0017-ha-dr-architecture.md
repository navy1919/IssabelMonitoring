# ADR-0017: معماری HA و DR (HA/DR Architecture)

| ID | Title | Status | Confidence | Date | Deciders |
|---|---|---|---|---|---|
| ADR-0017 | ha-dr-architecture | Proposed | **Blocked (Q-05)** | 2026-10-11 (۱۴۰۵/۰۷/۱۹) | Architecture (pending product owner approval) |

Version: `0.2` — تاریخچه تغییر در انتهای سند.

Related documents: [capacity-planning.md §۴.۶](../capacity-planning.md)، [deployment-and-runbooks.md](../../operations/deployment-and-runbooks.md)، [ADR-0007](ADR-0007-metadata-store-postgresql.md)، [ADR-0008](ADR-0008-telemetry-store-postgresql-first-clickhouse-on-trigger.md)

## Context

- [فرض A-01] دو سایت (DC اصلی و DR).
- اهداف موقت: دسترس‌پذیری UI/API ۹۹٫۹٪ (NFR-AVL-001)، RPO متادیتا ≤ ۱۵ دقیقه، RTO ≤ ۴ ساعت، گپ تله‌متری قابل تحمل و نمایان (NFR-AVL-003) — همه به [باز Q-05] وابسته.
- پلتفرم پایش باید **هنگام incident** کار کند؛ پس نباید در failure domain سیستم‌های پایش‌شده باشد (NFR-AVL-002، R-15).

## Decision

1. **درون سایت:** گره‌های stateless `platform-core` به‌صورت active-active پشت Load Balancer (حداقل ۲ به ازای نقش در Medium)؛ لبه‌ها ۲ گره به ازای Zone در Medium+.
2. **PostgreSQL:** streaming replication با standby هم‌زمان در سایت و async در DR؛ failover خودکار با **Patroni** در فاز ۶. پس از Product Benchmark، PostgreSQL علاوه بر metadata، سری‌های KPI مشتق‌شده را هم نگه می‌دارد ([ADR-0008](ADR-0008-telemetry-store-postgresql-first-clickhouse-on-trigger.md))، پس تنها مخزن stateful پلتفرم است و HA آن از Medium به بعد الزامی است.
3. **ClickHouse (فقط اگر triggerهای ADR-0008 فعال شوند):** در آن صورت `ReplicatedMergeTree` با RF=2 و **Keeper سه‌گرهی**؛ تا آن زمان هیچ جزء HA مربوط به ClickHouse مستقر نمی‌شود.
4. **DR:** active-passive؛ PostgreSQL async replica (و در صورت وجود ClickHouse، replica در DR یا بازیابی از Backup) (انتخاب بر اساس Q-05 و هزینه دیسک — [capacity-planning.md](../capacity-planning.md)). `connector-runtime` و لبه‌های اختیاری پیکربندی دو مقصد (اصلی/DR) دارند.
5. **استقلال failure domain:** پلتفرم برای **کارکرد خود** به Elasticsearch، Splunk، Zabbix یا Kafka بانک وابسته نیست (آن‌ها فقط منبع داده‌اند)؛ اما در گزینهٔ E ([ADR-0021](ADR-0021-build-buy-reuse-strategy.md)) **ارزش تحلیلی** پلتفرم به دسترس‌پذیری همین منابع وابسته است: اگر منبعی در خود حادثه از دست برود، سیگنال‌های آن `UNKNOWN` نمایش داده می‌شوند و خودِ قطع منبع به‌عنوان شاهد ثبت می‌شود (R-15)؛ زیرساخت مشترک (DNS، NTP، IdP، ذخیره‌ساز) شناسایی و برای هر کدام حالت تخریب تدریجی تعریف می‌شود.
6. self-monitoring و صفحه سلامت مستقل (NFR-OBS-001).

## Alternatives considered

| گزینه | مزایا | معایب | نتیجه |
|---|---|---|---|
| active-active درون سایت + active-passive DR (انتخاب) | تعادل هزینه/پیچیدگی | RTO دقیقه‌ها تا ساعت | **پذیرفته (مشروط Q-05)** |
| active-active بین سایت‌ها | RTO نزدیک صفر | تکثیر هم‌زمان بین‌سایتی، پیچیدگی و تأخیر | فقط سناریوی High با «2 active» |
| فقط Backup/Restore | ارزان | RTO طولانی | فقط Low |
| اشتراک خوشه‌های بانک (ES/Kafka) برای ذخیره | صرفه‌جویی | نقض استقلال failure domain | رد |

## Rationale

هدف ۹۹٫۹٪ و RTO ≤ ۴ ساعت با این الگو قابل دستیابی است [فرضیه — BM-09] و پیچیدگی را تا فاز ۶ به تعویق می‌اندازد.

## Consequences

- مثبت: پلتفرم در زمان قطعی منابع کار می‌کند و قطعی را نشان می‌دهد.
- منفی: اجزای Patroni/DCS؛ و تنها در صورت فعال شدن trigger، دیسک ClickHouse ×۲ تا ×۳ (RF و DR) و Keeper.
- هزینه عملیاتی: تمرین دوره‌ای failover و DR (BM-09)، runbook.

## Evidence that would change this decision

- پاسخ Q-05 (RPO/RTO، توپولوژی DR، هدف دسترس‌پذیری).
- BM-09 ناموفق.
- سیاست بانک برای DR (مثلاً الزام active-active برای سامانه‌های پایش حیاتی).

## Related requirements

NFR-AVL-001، NFR-AVL-002، NFR-AVL-003، NFR-REL-002، NFR-REL-004، NFR-OBS-001، FR-ADM-006

## تاریخچه تغییر

- 0.2 — تغییر پس از Product Benchmark: هم‌راستا با ADR-0008 (PostgreSQL اول، ClickHouse مشروط) و ADR-0021.

## Related questions

Q-05 (مسدودکننده)، Q-18، Q-19، BM-09، R-15
