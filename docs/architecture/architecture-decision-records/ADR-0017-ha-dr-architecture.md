# ADR-0017: معماری HA و DR (HA/DR Architecture)

| ID | Title | Status | Confidence | Date | Deciders |
|---|---|---|---|---|---|
| ADR-0017 | ha-dr-architecture | Proposed | **Blocked (Q-05)** | 2026-10-11 (۱۴۰۵/۰۷/۱۹) | Architecture (pending product owner approval) |

Related documents: [capacity-planning.md §۴.۶](../capacity-planning.md)، [deployment-and-runbooks.md](../../operations/deployment-and-runbooks.md)، [ADR-0007](ADR-0007-metadata-store-postgresql.md)، [ADR-0008](ADR-0008-telemetry-store-clickhouse.md)

## Context

- [فرض A-01] دو سایت (DC اصلی و DR).
- اهداف موقت: دسترس‌پذیری UI/API ۹۹٫۹٪ (NFR-AVL-001)، RPO متادیتا ≤ ۱۵ دقیقه، RTO ≤ ۴ ساعت، گپ تله‌متری قابل تحمل و نمایان (NFR-AVL-003) — همه به [باز Q-05] وابسته.
- پلتفرم پایش باید **هنگام incident** کار کند؛ پس نباید در failure domain سیستم‌های پایش‌شده باشد (NFR-AVL-002، R-15).

## Decision

1. **درون سایت:** گره‌های stateless `platform-core` به‌صورت active-active پشت Load Balancer (حداقل ۲ به ازای نقش در Medium)؛ لبه‌ها ۲ گره به ازای Zone در Medium+.
2. **PostgreSQL:** streaming replication با standby هم‌زمان در سایت و async در DR؛ failover خودکار با **Patroni** در فاز ۶.
3. **ClickHouse:** Low تک‌گره + Backup؛ Medium+ `ReplicatedMergeTree` با RF=2 و **Keeper سه‌گرهی**.
4. **DR:** active-passive؛ PostgreSQL async replica + ClickHouse یا replica در DR یا بازیابی از Backup (انتخاب بر اساس Q-05 و هزینه دیسک — [capacity-planning.md](../capacity-planning.md)). لبه‌ها پیکربندی دو مقصد (اصلی/DR) دارند.
5. **استقلال failure domain:** پلتفرم برای **کارکرد خود** به Elasticsearch، Splunk، Zabbix یا Kafka بانک وابسته نیست (آن‌ها فقط منبع داده‌اند)؛ زیرساخت مشترک (DNS، NTP، IdP، ذخیره‌ساز) شناسایی و برای هر کدام حالت تخریب تدریجی تعریف می‌شود.
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
- منفی: دیسک ClickHouse ×۲ تا ×۳ (RF و DR)؛ اجزای Patroni/DCS و Keeper.
- هزینه عملیاتی: تمرین دوره‌ای failover و DR (BM-09)، runbook.

## Evidence that would change this decision

- پاسخ Q-05 (RPO/RTO، توپولوژی DR، هدف دسترس‌پذیری).
- BM-09 ناموفق.
- سیاست بانک برای DR (مثلاً الزام active-active برای سامانه‌های پایش حیاتی).

## Related requirements

NFR-AVL-001، NFR-AVL-002، NFR-AVL-003، NFR-REL-002، NFR-REL-004، NFR-OBS-001، FR-ADM-006

## Related questions

Q-05 (مسدودکننده)، Q-18، Q-19، BM-09، R-15
