# ADR-0011: مدل Topology و کشف وابستگی‌ها (Topology Model and Dependency Discovery)

| ID | Title | Status | Confidence | Date | Deciders |
|---|---|---|---|---|---|
| ADR-0011 | topology-model-and-dependency-discovery | Proposed | **Provisional** (EXP-01، EXP-02، Q-08) | 2026-10-11 (۱۴۰۵/۰۷/۱۹) | Architecture (pending product owner approval) |

Version: `0.1`

Related documents: [domain-model.md](../../vision/domain-model.md)، [data-architecture.md](../data-architecture.md)، [ADR-0007](ADR-0007-metadata-store-postgresql.md)، [ADR-0012](ADR-0012-correlation-engine-design.md)

## Context

- Topology سرویس پایه همبستگی و تحلیل اثر است (تمایز ۱ محصول).
- در شروع هیچ trace یا service mesh وجود ندارد ([واقعیت F-02]: instrumentation موکول).
- Topology دستی کهنه می‌شود (R-08)؛ Topology کشف‌شده ممکن است نادرست باشد.

## Decision

1. مدل: موجودیت‌های `Component` (با زیرنوع‌ها) و یال `Dependency` با ویژگی‌های `from`، `to`، `direction`، `protocol`، `criticality`، `provenance` (MANUAL | DISCOVERED | IMPORTED)، `source`، `confidence` (۰ تا ۱)، `firstSeen`، `lastSeen`، و وضعیت تأیید (PROPOSED | APPROVED | REJECTED). همچنین `TrustBoundary` روی یال/Zone.
2. ترتیب منابع:
   - فاز ۱: **دستی** + import انبوه YAML/CSV (FR-CAT-011).
   - فاز ۲ به بعد: **کشف** از pool/memberهای LB، تعریف channel/queue در MQ (remote queue، transmission queue، channel)، نقشه‌های Zabbix/SolarWinds، و فاز ۴: هم‌وقوعی در لاگ‌ها (log co-occurrence)؛ فاز ۷: trace.
3. یال کشف‌شده با `PROPOSED` وارد می‌شود و تا تأیید انسانی (یا قاعده پذیرش خودکار پیکربندی‌شده برای منابع قطعی مانند عضویت pool LB) فقط با برچسب «پیشنهادی» در UI و با وزن کمتر در همبستگی استفاده می‌شود.
4. `lastSeen` و انقضا: یال کشف‌شده‌ای که در بازه پیکربندی‌شده دیده نشود «کهنه» علامت می‌خورد، حذف نمی‌شود.
5. همه تغییرات Topology در تاریخچه (FR-CAT-012) و audit ثبت می‌شوند.

## Alternatives considered

| گزینه | مزایا | معایب | نتیجه |
|---|---|---|---|
| دستی + کشف با provenance/confidence/approval (انتخاب) | کنترل، شفافیت، تدریجی | کار انسانی برای تأیید | **پذیرفته** |
| فقط دستی | ساده | کهنگی (R-08) | ناکافی |
| فقط کشف خودکار | بدون کار دستی | خطای کشف بی‌کنترل؛ اعتماد پایین اپراتور (R-05) | رد |
| import از CMDB بانک | منبع موجود | وجود و کیفیت CMDB نامعلوم | به‌عنوان provenance=IMPORTED پشتیبانی شود؛ پرسش جدید پیشنهاد می‌شود |
| APM/trace برای کشف | دقیق | نیاز به instrumentation (فاز ۷) | آینده |

## Rationale

provenance و confidence همان اصل explainability را به Topology می‌آورند: همبستگی همیشه می‌گوید هر یال از کجا آمده و چقدر قابل اتکا است.

## Consequences

- مثبت: Topology قابل اعتماد و قابل ممیزی؛ امکان سنجش پوشش Topology.
- منفی: صف تأیید یال‌ها نیازمند مالک (service owner)؛ UI مدیریت پیشنهادها.
- هزینه عملیاتی: بازبینی دوره‌ای یال‌های کهنه.

## Evidence that would change this decision

- EXP-02/Q-08: منابع اطلاعات کافی برای کشف نداشته باشند → تکیه بیشتر بر دستی/CMDB.
- وجود CMDB با کیفیت بالا → IMPORTED به‌عنوان منبع اصلی.
- دقت اندازه‌گیری‌شده کشف (درصد یال‌های پذیرفته) > ۹۵٪ برای یک منبع → پذیرش خودکار برای آن منبع.

## Related requirements

FR-CAT-006، FR-CAT-007، FR-CAT-008، FR-CAT-009، FR-CAT-011، FR-CAT-012، FR-HLT-002، FR-COR-003، FR-UI-003، NFR-USA-002

## Related questions

Q-08، Q-13، EXP-01، EXP-02، R-08، BM-08، BM-10
