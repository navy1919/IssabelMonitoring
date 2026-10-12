# ADR-0013: مرحله‌بندی تحلیل: قاعده، آمار، یادگیری ماشینی (Analytics Staging: Rules → Statistics → ML)

| ID | Title | Status | Confidence | Date | Deciders |
|---|---|---|---|---|---|
| ADR-0013 | analytics-staging-rules-statistics-ml | Proposed | Firm | 2026-10-11 (۱۴۰۵/۰۷/۱۹) | Architecture (pending product owner approval) |

Version: `0.1`

Related documents: [correlation-and-intelligence.md](../correlation-and-intelligence.md)، [quality-and-validation-strategy.md](../../testing/quality-and-validation-strategy.md)، [ADR-0012](ADR-0012-correlation-engine-design.md)

## Context

- منشور محصول تشخیص ناهنجاری و پیش‌بینی را می‌خواهد؛ هشدار کاذب اعتماد اپراتور را از بین می‌برد (R-05).
- الگوی ترافیک بانکی فصلی و تقویم‌محور است (روز کاری/تعطیل، چرخه‌های ساتنا/پایا، اول ماه) — تمایز ۳.
- داده تاریخی برچسب‌دار در شروع وجود ندارد.

## Decision

قابلیت‌های تحلیلی به‌ترتیب و فقط با عبور از دروازه ارزیابی وارد می‌شوند:

| مرحله | قابلیت | فاز | دروازه ورود |
|---|---|---|---|
| S1 | آستانه ایستا (FR-ANO-001) | P2 | — |
| S2 | baseline فصلی ساده time-of-week و DayType (FR-CAL-007، FR-ANO-002 پایه) و قاعده چندمتغیره VS-1 «افت موفقیت با ورودی پایدار» | P3 | تأیید عملیات روی داده بازپخش |
| S3 | آمار مقاوم: robust z/MAD، EWMA، باقیمانده STL (FR-ANO-003) | P5 | precision/recall روی تاریخچه برچسب‌دار ≥ هدف توافقی و بودجه هشدار |
| S4 | الگوهای چندمتغیره و وابستگی‌آگاه (FR-ANO-004) | P5 | همان |
| S5 | پیش‌بینی: time-to-full صف، burn rate SLO، اشباع (FR-ANO-005) | P5 | خطای پیش‌بینی اندازه‌گیری‌شده |
| S6 | ML (فقط با دروازه) | پس از P5 | برتری اندازه‌گیری‌شده نسبت به S3/S4 در harness (FR-ANO-006)، توضیح‌پذیری (FR-ANO-008)، پایش drift (FR-ANO-007) |

قواعد: هر ناهنجاری توضیح دارد (چه، نسبت به چه baseline، چقدر)؛ تقویم و پنجره نگهداری قبل از هشدار اعمال می‌شود (EXPECTED_INACTIVE/MAINTENANCE)؛ harness ارزیابی (FR-ANO-006) پیش از S3 ساخته می‌شود.

## Alternatives considered

| گزینه | معایب | نتیجه |
|---|---|---|
| مرحله‌بندی با دروازه (انتخاب) | کندتر به «AI» می‌رسد | **پذیرفته** |
| ML/AIOps از روز اول | بدون داده، غیرقابل توضیح، هشدار کاذب | رد |
| فقط آستانه ایستا | ناکافی برای الگوی فصلی بانکی | رد به‌عنوان مقصد نهایی |
| ابزار تجاری AIOps | lock-in، آفلاین، مجوز | رد |

## Rationale

هر مرحله ارزش مستقل دارد و پایه مرحله بعد را (داده برچسب‌دار، harness، اعتماد اپراتور) فراهم می‌کند.

## Consequences

- مثبت: هشدارهای قابل دفاع، معیار M5 قابل سنجش.
- منفی: نیاز به برچسب‌زنی incidentها توسط عملیات (هزینه انسانی).
- هزینه عملیاتی: نگهداری مدل‌ها/پارامترها نسخه‌دار؛ اگر Python برای S6 لازم شود، runtime جدید با ADR جداگانه.

## Evidence that would change this decision

- در دسترس بودن تاریخچه برچسب‌دار قابل اتکا از ابزارهای موجود (مثلاً تیکت‌های ITSM مرتبط با alertها) → تسریع S3.
- شکست S2 در تأیید عملیات → بازنگری مدل baseline پیش از S3.

## Related requirements

FR-ANO-001..FR-ANO-008، FR-CAL-004، FR-CAL-007، FR-NTF-002، NFR-USA-002

## Related questions

Q-14، Q-15، R-05، R-14
