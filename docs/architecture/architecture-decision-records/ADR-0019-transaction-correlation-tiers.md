# ADR-0019: سطوح همبستگی تراکنش (Transaction Correlation Tiers)

| ID | Title | Status | Confidence | Date | Deciders |
|---|---|---|---|---|---|
| ADR-0019 | transaction-correlation-tiers | Proposed | Firm | 2026-10-11 (۱۴۰۵/۰۷/۱۹) | Architecture (pending product owner approval) |

Related documents: [correlation-and-intelligence.md](../correlation-and-intelligence.md)، [data-architecture.md](../data-architecture.md)، [ADR-0006](ADR-0006-canonical-schemas-otel-plus-bank-namespace.md)، [ADR-0018](ADR-0018-data-retention-privacy-capacity-controls.md)

## Context

- محصول تراکنش‌محور است ([واقعیت F-02])، اما در شروع trace وجود ندارد و وجود کلید همبستگی در لاگ‌ها نامعلوم است ([باز Q-07]، R-01).
- ادعای دقت بیش از داده، اعتماد را از بین می‌برد.

## Decision

چهار سطح همبستگی تعریف می‌شود و **هر متریک تراکنشی سطحی را که از آن مشتق شده ثبت می‌کند** (`bank.txn.correlation_tier`)؛ UI سطح را نمایش می‌دهد:

| Tier | روش | دقت | پیش‌نیاز |
|---|---|---|---|
| L3 Trace | W3C `traceparent` / OTel trace id سرتاسری یا قطعه‌ای | دقیق | instrumentation (فاز ۷) |
| L2 Business key | join قطعی بر کلیدهای کسب‌وکار پیکربندی‌شده (مثلاً ISO 8583 STAN + RRN + terminal + date؛ شماره پیگیری ساتنا/پایا؛ request id داخلی) — کلیدها پیش از ذخیره با HMAC-SHA256 توکن می‌شوند | بالا | وجود کلید در لاگ هر hop |
| L1 Heuristic | کلید ناقص + پنجره زمانی + دسته مبلغ و … → پیوند احتمالی با امتیاز confidence | متوسط/متغیر | بخشی از کلیدها |
| L0 Aggregate | بدون پیوند منفرد؛ مقایسه شمارش/outcome دقیقه‌ای به ازای hop و یال (بالادست در برابر پایین‌دست) | تجمیعی | فقط شمارش‌های لاگ |

**L0 پیش‌فرض است و باید به‌تنهایی کاملاً مفید باشد** (VS-1 بر L0 و در صورت وجود کلید L2 تکیه دارد). نگاشت outcome (مثلاً جدول کدهای پاسخ ISO 8583 → `TxnOutcome`) **داده** است، نه کد.

## Alternatives considered

| گزینه | معایب | نتیجه |
|---|---|---|
| فقط trace (L3) | تا فاز ۷ هیچ داده‌ای | رد |
| فقط تجمیعی (L0) | از کلیدهای موجود استفاده نمی‌کند | رد به‌عنوان سقف |
| سطوح چندگانه با ثبت tier (انتخاب) | پیچیدگی مدل | **پذیرفته** |
| ذخیره کلید خام برای join | نقض ADR-0018 | رد |

## Rationale

ثبت صریح tier همان اصل صداقت در عدم قطعیت است و ارتقای تدریجی (L0 → L2 → L3) را بدون تغییر مدل داده ممکن می‌کند.

## Consequences

- مثبت: ارزش از روز اول با داده موجود؛ مسیر روشن برای بهبود.
- منفی: L1 نیازمند کالیبراسیون confidence؛ L2 حجم رویداد tokenized را بالا می‌برد ([capacity-planning.md](../capacity-planning.md)).
- هزینه عملیاتی: نگهداری پیکربندی کلیدها و جداول outcome به ازای نوع تراکنش؛ مدیریت کلید HMAC.

## Evidence that would change this decision

- EXP-01 نشان دهد هیچ کلیدی در لاگ‌ها نیست → تمرکز بر L0 و تعویق L2.
- مقاومت‌پذیری L1 در برابر clock skew (EXP-05) ناکافی → غیرفعال‌سازی L1 به‌طور پیش‌فرض.

## Related requirements

FR-TXN-001..FR-TXN-011، FR-LB-006، FR-ANO-004، NFR-PRV-003

## Related questions

Q-07، Q-13، Q-17، EXP-01، EXP-05، R-01، R-11
