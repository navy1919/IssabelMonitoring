# ADR-0009: عدم نگهداری لاگ خام و استفاده فدرال از منابع موجود (No Own Raw Log Store – Federate Existing)

| ID | Title | Status | Confidence | Date | Deciders |
|---|---|---|---|---|---|
| ADR-0009 | no-own-raw-log-store-federate-existing | Proposed | Firm (فازهای ۱ تا ۵) | 2026-10-11 (۱۴۰۵/۰۷/۱۹) | Architecture (pending product owner approval) |

Related documents: [technology-evaluation.md §۷](../technology-evaluation.md)، [ingestion-and-connectors.md](../ingestion-and-connectors.md)، [ADR-0020](ADR-0020-existing-platform-integration-read-only-pull.md)، [capacity-planning.md §۸](../capacity-planning.md)

## Context

- [واقعیت F-02] شروع با Splunk و Elasticsearch موجود؛ جایگزینی آن‌ها هدف نیست (non-goal).
- [فرض A-05] لاگ برنامه‌های کلیدی بانکی از قبل به Splunk و/یا Elasticsearch می‌رسد.
- کپی لاگ خام: هزینه دیسک چندبرابر، افزایش سطح افشای داده حساس (R-10)، و دوباره‌کاری عملیاتی.
- ریسک R-15: منابع موجود ممکن است دقیقاً هنگام incident در دسترس نباشند.

## Decision

1. پلتفرم **لاگ خام ذخیره نمی‌کند**. لاگ در Splunk/ES بانک می‌ماند.
2. connectorها با query‌های زمان‌بندی‌شده و محدود به budget، **داده مشتق** استخراج می‌کنند: متریک مشتق از لاگ (شمارش به ازای outcome، تأخیر در صورت وجود)، رویداد نرمال‌شده، خلاصه تراکنش دقیقه‌ای، و در صورت وجود کلید، رویداد tokenized تراکنش (L2).
3. **Evidence query فدرال** (FR-INT-007): هنگام بررسی incident، کوئری موردی از طریق `connector-runtime` (فرمان long-poll) اجرا و نتیجه پس از redaction به UI داده می‌شود؛ نتیجه فقط در cache کوتاه‌مدت جلسه نگه داشته می‌شود و در Store پایدار ذخیره نمی‌شود (مگر خلاصه/شمارش).
4. Deep link به Splunk/ES برای مشاهده لاگ اصلی (FR-INT-006).
5. دسترس‌ناپذیری منبع → وضعیت `UNKNOWN`/stale صریح؛ داده مشتق قبلی در Store ما باقی است.

## Alternatives considered

| گزینه | امتیاز | دلیل |
|---|---|---|
| Federate (انتخاب) | 4.10 | بدون تکرار داده، عملیات ساده، کمترین افشا |
| OpenSearch اختصاصی برای لاگ خام | 3.05 | سخت‌افزار و عملیات سنگین، تکرار داده |
| ClickHouse برای لاگ خام | 3.35 | full-text ضعیف‌تر، تکرار داده |

## Rationale

مزیت متمایز محصول در مدل سرویس/تراکنش و همبستگی است، نه نگهداری لاگ. ضعف اشتراک failure domain با ذخیره داده مشتق در Store مستقل و نمایش صریح «نبود داده» جبران می‌شود.

## Consequences

- مثبت: صرفه‌جویی ده‌ها TB؛ رعایت non-goal؛ مالکیت لاگ نزد تیم‌های موجود.
- منفی: وابستگی به کیفیت و فیلدهای لاگ موجود (R-01)؛ بار اضافی روی Splunk/ES (R-03)؛ evidence query هنگام قطعی منبع در دسترس نیست.
- هزینه عملیاتی: هماهنگی Query budget با ادمین‌ها، نگهداری قالب‌های query به ازای نسخه منبع.

## Evidence that would change this decision

- BM-04/Q-22: Splunk/ES نتوانند Query budget لازم را تأمین کنند (تحلیل حساسیت: ClickHouse برای لاگ‌های منتخب هم‌تراز می‌شود).
- retention منابع کوتاه‌تر از نیاز تحلیل (مثلاً کمتر از ۳۰ روز برای بازپخش incident).
- R-15 در عمل پرتکرار شود (منبع در اکثر incidentها در دسترس نباشد).
- در این صورت: ingest **فقط لاگ‌های منتخب، redact‌شده** سرویس‌های کلیدی با retention کوتاه — با ADR جدید.

## Related requirements

FR-TXN-003، FR-INT-002، FR-INT-003، FR-INT-006، FR-INT-007، FR-HLT-003، NFR-IMP-001، NFR-PRV-005، NFR-AVL-002، NFR-REL-004

## Related questions

Q-07، Q-08، Q-22، BM-04، EXP-01، EXP-02، R-01، R-03، R-15
