# ADR-0020: یکپارچگی فقط‌خواندنی و pull با سکوهای موجود (Existing Platform Integration – Read-only Pull)

| ID | Title | Status | Confidence | Date | Deciders |
|---|---|---|---|---|---|
| ADR-0020 | existing-platform-integration-read-only-pull | Proposed | Firm | 2026-10-11 (۱۴۰۵/۰۷/۱۹) | Architecture (pending product owner approval) |

Related documents: [ingestion-and-connectors.md](../ingestion-and-connectors.md)، [capacity-planning.md §۸](../capacity-planning.md)، [ADR-0004](ADR-0004-collector-and-connector-architecture.md)، [ADR-0009](ADR-0009-no-own-raw-log-store-federate-existing.md)

## Context

- [واقعیت F-02] شروع با SolarWinds، Zabbix، Splunk و Elasticsearch موجود؛ عدم جایگزینی آن‌ها.
- [فرض A-09] حساب‌های فقط‌خواندنی با حداقل دسترسی پس از بازبینی امنیتی قابل دریافت‌اند (R-02).
- تغییر پیکربندی دستگاه‌ها/ابزارهای موجود هزینه تأیید بالایی دارد (NFR-IMP-002).
- بار اضافی روی منابع باید محدود و توافقی باشد (NFR-IMP-001، R-03).

## Decision

1. یکپارچگی با سکوهای موجود از طریق **API فقط‌خواندنی و pull** از `connector-runtime`:
   - **Zabbix:** JSON-RPC API (`host.get`، `item.get`، `history.get`/`trend.get`، `problem.get`/`event.get`)؛
   - **SolarWinds:** SWIS با کوئری SWQL؛
   - **Splunk:** REST search API؛
   - **Elasticsearch/OpenSearch:** `_search` (با aggregation)؛
   (همه نیازمند تأیید در نسخه نصب‌شده بانک — Q-08.)
2. هر منبع **حساب اختصاصی فقط‌خواندنی** با حداقل دسترسی (محدود به host group، index، role) دارد؛ credentialها در SecretProvider.
3. هر منبع **Query budget** توافقی دارد که در connector به‌صورت سخت اعمال می‌شود (فراخوانی/دقیقه، هم‌روندی، بازه زمانی، اندازه پاسخ)؛ نزدیک شدن به سقف → کاهش فرکانس و علامت stale، نه افزایش بار.
4. **Webhook ورودی** (مثلاً media type webhook در Zabbix یا alert action در Splunk) فقط **اختیاری** و در صورتی که بانک پیکربندی آن را انتخاب کند (FR-INT-005).
5. **هیچ تغییر پیکربندی** در دستگاه‌ها و ابزارهای موجود لازم نیست؛ هر قابلیتی که به تغییر نیاز دارد (مثلاً فعال‌سازی queue monitoring در MQ) به‌عنوان «افزایش اختیاری» علامت می‌خورد.
6. Deep link به شیء مرتبط در هر منبع (FR-INT-006).

## Alternatives considered

| گزینه | مزایا | معایب | نتیجه |
|---|---|---|---|
| pull فقط‌خواندنی با budget (انتخاب) | بدون تغییر در منابع، کنترل بار | تأخیر poll (A-13) | **پذیرفته** |
| push اجباری (webhook/forwarder) از منابع | تأخیر کمتر | تغییر پیکربندی در همه منابع، تأیید امنیتی | فقط اختیاری |
| دسترسی مستقیم به پایگاه داده Zabbix/SolarWinds | کارایی | شکننده بین نسخه‌ها، خطر بار، دسترسی حساس | رد (مگر read replica با تأیید بانک) |
| forwarder دوم Splunk/ES به سمت ما | داده کامل | نقض ADR-0009، تغییر پیکربندی | رد |

## Rationale

کمترین مقاومت سازمانی و کمترین ریسک برای سامانه‌های تولیدی؛ هم‌راستا با «کار با صفر instrumentation جدید» (تمایز ۵).

## Consequences

- مثبت: شروع سریع پس از دریافت دسترسی؛ بدون اثر بر پیکربندی موجود.
- منفی: تأخیر تشخیص برابر فاصله poll + پردازش (NFR-PRF-002)؛ وابستگی به تغییرات API بین نسخه‌ها (R-12).
- هزینه عملیاتی: نگهداری تست‌های سازگاری connector به ازای نسخه منبع؛ هماهنگی دوره‌ای budget با ادمین‌ها.

## Evidence that would change this decision

- BM-04/BM-05/BM-06: budget قابل قبول برای پوشش لازم کافی نباشد → push اختیاری یا export بومی منبع با تأیید بانک.
- عدم صدور حساب فقط‌خواندنی (R-02) → import فایل آفلاین (FR-ING-005) به‌عنوان راه موقت.

## Related requirements

FR-INT-001..FR-INT-007، FR-ING-005، FR-MQ-002، FR-LB-002، FR-RUN-004، NFR-SEC-003، NFR-IMP-001، NFR-IMP-002، NFR-PRF-002

## Related questions

Q-08، Q-22، BM-04، BM-05، BM-06، EXP-02، EXP-04، R-02، R-03، R-12
