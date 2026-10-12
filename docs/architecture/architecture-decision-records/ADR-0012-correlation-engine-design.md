# ADR-0012: طراحی موتور همبستگی (Correlation Engine Design)

| ID | Title | Status | Confidence | Date | Deciders |
|---|---|---|---|---|---|
| ADR-0012 | correlation-engine-design | Proposed | Firm | 2026-10-11 (۱۴۰۵/۰۷/۱۹) | Architecture (pending product owner approval) |

Related documents: [correlation-and-intelligence.md](../correlation-and-intelligence.md)، [ADR-0011](ADR-0011-topology-model-and-dependency-discovery.md)، [ADR-0013](ADR-0013-analytics-staging-rules-statistics-ml.md)، [ADR-0019](ADR-0019-transaction-correlation-tiers.md)

## Context

- تمایز ۴ محصول: رتبه‌بندی علت محتمل، توپولوژی‌آگاه و قابل توضیح، با confidence و عدم قطعیت؛ **هرگز ادعای علیت نمی‌کند**.
- داده برچسب‌خورده تاریخی برای یادگیری ماشینی در شروع وجود ندارد؛ اعتماد اپراتور شکننده است (R-05).
- تأخیر قابل قبول: چرخه ارزیابی ≤ ۶۰ ثانیه (NFR-PRF-004)؛ [فرض A-13] تأخیر ۲ تا ۵ دقیقه برای منابع pull.

## Decision

1. موتور همبستگی **قطعی (deterministic) و قابل توضیح** در ماژول `correlation`، اجرا در نقش `analytics` با micro-batch هر ۳۰ تا ۶۰ ثانیه.
2. مراحل: (الف) نرمال‌سازی و dedup رویدادها (FR-COR-002)؛ (ب) گروه‌بندی پنجره زمانی با تحمل clock skew (`source_clock_skew_estimate`)؛ (ج) گروه‌بندی توپولوژی‌آگاه (FR-COR-003)؛ (د) **امتیازدهی شواهد** به ازای هر گره کاندید با مؤلفه‌های صریح: زمان شروع نسبی (زودتر بودن)، موقعیت در Topology (پایین‌دست‌ترین گره ناسالم)، شدت انحراف، اطمینان یال (confidence)، تغییر اخیر (FR-COR-005)، کیفیت/تازگی داده، و شواهد نقض (counter-evidence)؛ (ه) خروجی `CauseHypothesis` با rank، score، confidence، توضیح متنی و شواهد نقض.
3. **بدون ML** در شروع؛ وزن‌ها پیکربندی‌شده، نسخه‌دار و قابل ممیزی. بازخورد اپراتور (FR-COR-006) ذخیره و برای تنظیم وزن‌ها با ارزیابی آفلاین استفاده می‌شود.
4. UI همیشه disclaimer «همبستگی ≠ علیت» و tier همبستگی تراکنش (ADR-0019) را نشان می‌دهد.
5. داده ناکافی یا کهنه → confidence پایین و اعلام صریح عدم قطعیت، نه حدس.

## Alternatives considered

| گزینه | مزایا | معایب | نتیجه |
|---|---|---|---|
| قواعد/امتیازدهی قطعی توپولوژی‌آگاه (انتخاب) | توضیح‌پذیر، بدون داده آموزشی، قابل آزمون | نیاز به تنظیم وزن | **پذیرفته** |
| ML/AIOps از ابتدا | الگوهای پیچیده | نیاز به داده برچسب‌دار، جعبه سیاه، خطر بی‌اعتمادی | رد (ADR-0013) |
| Complex Event Processing (مثلاً Esper/Flink CEP) | قدرت بیان زمانی | زیرساخت جدید، تأخیر زیرثانیه لازم نیست | رد |
| Graph DB برای استدلال | پیمایش بومی | Store اضافه (ADR-0007) | رد |
| Causal inference آماری | نزدیک‌تر به علیت | داده و فرض‌های قوی لازم دارد | پژوهش آینده، نه محصول اولیه |

## Rationale

قابلیت توضیح و سنجش‌پذیری (معیار VS-1: علت واقعی در top-3 برای ≥ ۲ از ۳ incident بازپخش‌شده) با قواعد قطعی قابل دستیابی و قابل دفاع است.

## Consequences

- مثبت: هر رتبه قابل بازتولید است؛ تست با بازپخش incidentهای تاریخی.
- منفی: کیفیت وابسته به کیفیت Topology و داده؛ وزن‌ها نیاز به نگهداری.
- هزینه عملیاتی: مجموعه بازپخش (replay set) incidentها و اجرای دوره‌ای آن؛ BM-08 برای کارایی.

## Evidence that would change this decision

- نرخ top-3 کمتر از معیار پس از تنظیم در M4 → بازنگری مدل امتیازدهی.
- انباشت بازخورد برچسب‌دار کافی (مثلاً چند صد incident) و ارزیابی آفلاین نشان دهد مدل یادگیرنده با حفظ توضیح‌پذیری بهتر است → ADR جدید طبق دروازه ارزیابی ADR-0013.
- BM-08: مدت چرخه > ۶۰ ثانیه در Medium.

## Related requirements

FR-COR-001..FR-COR-007، FR-HLT-002، FR-ANO-004، NFR-PRF-004، NFR-USA-002

## Related questions

Q-17، EXP-05، BM-08، R-05، R-11، R-15
