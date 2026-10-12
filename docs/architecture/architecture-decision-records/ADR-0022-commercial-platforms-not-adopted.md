# ADR-0022: عدم اتخاذ پلتفرم‌های تجاری APM/AIOps (Commercial Platforms Not Adopted)

| ID | Title | Status | Confidence | Date | Deciders |
|---|---|---|---|---|---|
| ADR-0022 | commercial-platforms-not-adopted | Proposed | **Firm** (مگر با مجوز حقوقی — Q-24) | 2026-10-12 (۱۴۰۵/۰۷/۲۰) | Architecture (pending product owner approval; legal/compliance review required) |

Version: `0.2` — نخستین نسخه این ADR در بازنگری پس از Product Benchmark ایجاد شد.

Related documents: [product-benchmark.md](../../benchmark/product-benchmark.md)، [competitive-gap-analysis.md](../../benchmark/competitive-gap-analysis.md)، [build-buy-reuse-analysis.md](../../benchmark/build-buy-reuse-analysis.md)، [ADR-0021](ADR-0021-build-buy-reuse-strategy.md)، [ADR-0016](ADR-0016-offline-deployment-and-artifacts.md)، [security-and-threat-model.md](../security-and-threat-model.md)

> این سند مشاوره حقوقی نیست. وضعیت «مسدود مگر با مجوز حقوقی» جمع‌بندی معماری از منابع عمومی است و نظر حقوقی/انطباق بانک ([باز Q-24]) آن را جایگزین می‌کند.

## Context

- نامزدهای تجاری بررسی‌شده در Benchmark: Dynatrace Managed، Splunk ITSI، IBM Instana (self-hosted)، Cisco AppDynamics (on-prem)؛ و محصولات ویژه پرداخت/بانک INETCO Insight، IR Transact و meshIQ/Nastel (فقط ادعای فروشنده).
- **تحریم و کنترل صادرات** (research-commercial §0):
  - ITSR 31 CFR 560.204 صادرات، فروش یا تأمین مستقیم/غیرمستقیم کالا، فناوری یا خدمات از آمریکا یا توسط شخص آمریکایی به ایران را ممنوع می‌کند (https://www.ecfr.gov/current/title-31/subtitle-B/chapter-V/part-560/subpart-B/section-560.204، High).
  - EAR 15 CFR 746.7: برای ایران استثنای مجوز وجود ندارد و مجوزها عموماً رد می‌شوند (https://www.law.cornell.edu/cfr/text/15/746.7، High).
  - در اکتبر 2020 بخش مالی ایران تحت E.O. 13902 شناسایی و ۱۸ بانک بزرگ ایرانی تحریم شدند؛ اشخاص غیرآمریکایی در معرض **تحریم ثانویه** هستند (https://www.hsfkramer.com/notes/sanctions/2020-10/ofac-significantly-expands-sanctions-on-the-iranian-financial-sector، High).
  - Cisco (مالک Splunk و AppDynamics) صریحاً اعلام می‌کند محصولات خود را در ایران ارائه نمی‌کند (https://cisco.com/c/en/us/about/legal/global-export-trade/general-export-compliance.html، High).
  - پرونده SAP (2021): ارائه **به‌روزرسانی و وصله** از طریق واسطه‌های کشور ثالث خود تخلف است (https://www.justice.gov/usao-ma/pr/sap-admits-thousands-illegal-exports-its-software-products-iran-and-enters-non، High).
  - فروشندگان غیرآمریکایی (INETCO کانادا، IR استرالیا) همچنان در معرض رژیم ملی خود، تحریم ثانویه و قواعد re-export EAR هستند؛ تمایل به فروش «تأییدنشده — نیازمند تأیید فروشنده یا PoC».
- **شکاف قابلیت** (مستقل از تحریم): Business Events در Dynatrace به Grail نیاز دارد که فقط SaaS است (https://www.dynatrace.com/hub/detail/business-events، High)؛ پنجره نگهداری Dynatrace و ITSI مفهوم تقویم کسب‌وکار (SATNA/PAYA، تعطیلات ایران) ندارد (High/Medium)؛ هیچ نامزدی «علت محتمل + اطمینان عددی + شواهد نقض» را به‌صورت مستند ارائه نمی‌کند.

## Decision

1. هیچ پلتفرم تجاری APM/AIOps/پایش تراکنش (Dynatrace، Splunk ITSI، IBM Instana، Cisco AppDynamics، meshIQ، INETCO، IR Transact) در این برنامه **اتخاذ نمی‌شود**. وضعیت تأمین محصولات آمریکایی: **«مسدود مگر با مجوز حقوقی»** (Blocked unless legally cleared)؛ محصولات غیرآمریکایی: «تأییدنشده — ریسک تحریم ثانویه».
2. این شامل **افزودن ماژول/لایسنس جدید به ابزارهای تجاری موجود** (مثلاً ITSI روی Splunk موجود) نیز می‌شود، چون معامله جدید با فروشنده آمریکایی است.
3. ابزارهای تجاری **موجود** بانک (Splunk، SolarWinds) فقط به‌عنوان منبع داده خوانده می‌شوند ([ADR-0020](ADR-0020-existing-platform-integration-read-only-pull.md))؛ معماری باید در صورت از دست رفتن هر یک کار کند (منبع `UNKNOWN`، نه شکست پلتفرم) — ریسک R-16، [باز Q-25].
4. معماری پلتفرم هیچ وابستگی به کلید فعال‌سازی، به‌روزرسانی آنلاین یا SDK اختصاصی فروشنده تجاری ندارد (معیار ADR-0016 بند ۷).

## Alternatives considered

| گزینه | مزایا | معایب | نتیجه |
|---|---|---|---|
| Dynatrace Managed (offline) | Davis RCA توپولوژی‌محور، baseline فصلی، افزونه IBM MQ | تحریم؛ Business Events فقط SaaS؛ بدون تقویم کسب‌وکار؛ اطمینان عددی RCA مستند نیست | رد |
| Splunk ITSI روی Splunk موجود | نزدیک‌ترین مدل سرویس/KPI، adaptive threshold، Episode Review | تحریم (Cisco)؛ لایسنس جدید؛ بدون تقویم کسب‌وکار؛ RCA با اطمینان مستند نیست | رد (نزدیک‌ترین جایگزین در صورت مجوز حقوقی) |
| IBM Instana self-hosted | air-gap Standard Edition، حسگر MQ و ACE | تحریم (IBM آمریکایی)؛ Probable Root Cause در preview؛ بدون مدل تراکنش بانکی | رد |
| Cisco AppDynamics on-prem | Business Transaction، backend MQ | Cisco صریحاً در ایران ارائه نمی‌کند؛ عمق/سن صف MQ نیاز به افزونه | رد |
| INETCO Insight / IR Transact | رمزگشایی ISO 8583 و پرداخت (ادعای فروشنده) | ریسک تحریم ثانویه؛ تمایل به فروش و پشتیبانی Shetab تأییدنشده؛ نیاز به TAP/SPAN | رد اکنون؛ بازنگری با تأیید فروشنده + مجوز حقوقی |
| خرید از واسطه/reseller | دسترسی سریع | خود تخلف تلقی می‌شود (پرونده SAP)؛ قطع به‌روزرسانی/پشتیبانی | رد قاطع |

## Rationale

حتی با فرض تأمین قانونی، هیچ محصولی تمایزهای اصلی (معنای بانکی، تقویم جلالی، شواهد با اطمینان) را پوشش نمی‌دهد و لایه سفارشی همچنان لازم بود؛ بنابراین هزینه حقوقی، lock-in و ریسک قطع به‌روزرسانی بدون حذف کار سفارشی پذیرفته می‌شد. گزینه E ([ADR-0021](ADR-0021-build-buy-reuse-strategy.md)) همان ارزش را با ابزارهای موجود و OSS ارائه می‌کند.

## Consequences

- مثبت: بدون ریسک حقوقی جدید؛ بدون lock-in؛ کنترل کامل معنای بانکی.
- منفی: قابلیت‌های آماده (APM خودکار، RUM، tracing خودکار MQ) در دسترس نیست؛ Phase 7 در صورت نیاز با OSS (OTel SDK) انجام می‌شود.
- هزینه: نگهداری لایه سفارشی توسط تیم بانک.

## Evidence that would change this decision

- نظر کتبی حقوقی/انطباق بانک (Q-24) که تأمین و **به‌روزرسانی** قانونی یک محصول را مجاز بداند **و** PoC همان محصول در برابر E روی VS-1 نتیجه بهتر بدهد.
- تأیید رسمی فروشنده غیرآمریکایی برای فروش و پشتیبانی به بانک ایرانی + نظر حقوقی درباره تحریم ثانویه.
- تغییر رژیم تحریم (بازبینی سالانه پیشنهاد می‌شود).
- **نکات مرتبط که در Q-24/Q-25 پاسخ می‌گیرند و ممکن است دامنه این ADR را گسترش دهند:** (الف) وضعیت حقوقی استفاده و به‌روزرسانی **نرم‌افزار متن‌باز با منشأ شرکت‌های آمریکایی** (مثلاً Grafana، Elastic، exporter IBM، توزیع‌های OpenJDK) — Q-24؛ (ب) نحوه مجوز و به‌روزرسانی **IBM MQ** موجود بانک (وابستگی exporter `mq_prometheus` و کلاینت MQ به آن) — Q-25. پاسخ منفی هر یک معیار ADR-0016 بند ۷ را برای آن جزء فعال می‌کند.

## Related requirements

NFR-DEP-001، NFR-SEC-007، NFR-AVL-002، NFR-CMP-001

## Related questions

Q-10، Q-24، Q-25، R-09، R-13، R-16
