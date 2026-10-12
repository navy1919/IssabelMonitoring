# ADR-0018: نگهداری داده، حریم خصوصی و کنترل ظرفیت (Data Retention, Privacy and Capacity Controls)

| ID | Title | Status | Confidence | Date | Deciders |
|---|---|---|---|---|---|
| ADR-0018 | data-retention-privacy-capacity-controls | Proposed | **Firm** (قواعد حریم خصوصی) / **Provisional** (اعداد retention — Q-16، Q-10) | 2026-10-11 (۱۴۰۵/۰۷/۱۹) | Architecture (pending product owner approval) |

Version: `0.1`

Related documents: [security-and-threat-model.md](../security-and-threat-model.md)، [data-architecture.md](../data-architecture.md)، [capacity-planning.md](../capacity-planning.md)

## Context

- داده بانکی شامل اطلاعات کارت و هویتی است؛ نشت از طریق لاگ/query (R-10) ریسک اصلی است.
- الزامات قانونی (بانک مرکزی، PCI DSS، ISO 27001) نامعلوم‌اند ([باز Q-10]).
- retention مستقیماً دیسک را تعیین می‌کند: rollup یک‌دقیقه‌ای ۱۳ ماهه دو سوم تا سه چهارم حجم ([capacity-planning.md](../capacity-planning.md)).

## Decision

### حریم خصوصی (Firm)
1. **هرگز ذخیره نمی‌شود:** PAN، CVV، PIN، PIN block، track data، رمز عبور، OTP، token/secret، payload کامل.
2. **مجاز:** BIN (۶ یا ۸ رقم اول) فقط با توجیه؛ **توکن HMAC-SHA256** از PAN و کلیدهای همبستگی با کلید جداگانه به ازای Environment در secret store و چرخش تعریف‌شده (NFR-PRV-003)؛ **دسته مبلغ** (نه مبلغ دقیق) به‌طور پیش‌فرض.
3. **Redaction در لبه و در ingest** (دفاع در عمق) با آشکارسازهای PAN (Luhn + الگو)، کد ملی، شبا (IR + ۲۴ رقم)، شماره موبایل، شماره حساب (الگوی قابل پیکربندی).
4. نتایج evidence query فدرال از همان redaction عبور می‌کنند (NFR-PRV-005).
5. ممیزی redaction: نمونه‌برداری دوره‌ای از داده ذخیره‌شده برای کشف نشت (معیار VS-1: صفر داده حساس).

### Retention (Provisional)
| کلاس داده | مقدار پیشنهادی |
|---|---|
| نمونه خام متریک | 30 روز |
| rollup یک‌دقیقه‌ای | 13 ماه |
| rollup یک‌ساعته | 3 سال |
| رویداد نرمال‌شده | 13 ماه |
| خلاصه تراکنش | 13 ماه |
| رویداد tokenized تراکنش | 30 روز |
| Audit | ≥ 5 سال (یا الزام قانونی) |

اجرای retention با TTL/partition drop خودکار (NFR-PRV-004)؛ retention در سطح **کلاس سری** نیز قابل پیکربندی است (سیاست P2 در [capacity-planning.md](../capacity-planning.md)).

### کنترل ظرفیت
- سقف cardinality به ازای منبع/connector و رد برچسب‌های نامحدود (ADR-0006)؛ سهمیه ingest به ازای connector؛ هشدار رشد غیرعادی حجم؛ گزارش مصرف دیسک به ازای کلاس داده.

## Alternatives considered

| گزینه | معایب | نتیجه |
|---|---|---|
| redaction فقط در ingest | داده حساس از Zone خارج می‌شود | رد |
| رمزنگاری برگشت‌پذیر PAN | نیاز به مدیریت کلید در سطح PCI، ریسک بیشتر | رد |
| hash ساده بدون کلید (SHA-256) | قابل حمله brute-force روی فضای کوچک PAN | رد |
| retention یکسان برای همه | هدررفت دیسک | رد |

## Rationale

کمینه‌سازی داده ساده‌ترین کنترل امنیتی است؛ HMAC با کلید مخفی برای شمارش و همبستگی کافی است و برگشت‌پذیر نیست.

## Consequences

- مثبت: کاهش دامنه انطباق (احتمالاً خارج کردن پلتفرم از دامنه داده کارت — نیازمند تأیید Q-10).
- منفی: عدم امکان بررسی تراکنش منفرد با PAN در پلتفرم (باید در سامانه منبع انجام شود)؛ هزینه CPU برای redaction.
- هزینه عملیاتی: چرخش کلید HMAC (توکن‌های قبل و بعد از چرخش قابل join نیستند مگر دوره هم‌پوشانی تعریف شود)، نگهداری الگوهای آشکارساز.

## Evidence that would change this decision

- Q-10: الزام قانونی retention متفاوت یا الزام عدم نگهداری BIN.
- Q-16: retention تأییدشده کسب‌وکار.
- BM-07: هزینه CPU redaction بیش از برآورد → بهینه‌سازی، نه حذف.

## Related requirements

FR-TXN-010، FR-INT-007، NFR-PRV-001..NFR-PRV-005، NFR-SEC-002، NFR-SEC-004، NFR-CMP-001

## Related questions

Q-10، Q-16، Q-09 (HSM/vault)، R-10
