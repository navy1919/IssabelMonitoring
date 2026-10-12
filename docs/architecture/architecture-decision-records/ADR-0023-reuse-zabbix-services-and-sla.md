# ADR-0023: بازاستفاده از سرویس‌ها و SLA در Zabbix (Reuse Zabbix Services and SLA)

| ID | Title | Status | Confidence | Date | Deciders |
|---|---|---|---|---|---|
| ADR-0023 | reuse-zabbix-services-and-sla | Proposed | **Blocked** (PoC-1، Q-26) | 2026-10-12 (۱۴۰۵/۰۷/۲۰) | Architecture + Zabbix owners (pending product owner approval) |

Version: `0.2` — نخستین نسخه این ADR در بازنگری پس از Product Benchmark ایجاد شد.

Related documents: [product-benchmark.md](../../benchmark/product-benchmark.md)، [build-buy-reuse-analysis.md](../../benchmark/build-buy-reuse-analysis.md)، [ingestion-and-connectors.md](../ingestion-and-connectors.md)، [correlation-and-intelligence.md](../correlation-and-intelligence.md)، [ADR-0020](ADR-0020-existing-platform-integration-read-only-pull.md)، [ADR-0021](ADR-0021-build-buy-reuse-strategy.md)

## Context

- [فرض A-06] Zabbix از قبل میزبان‌ها و بخشی از میانه‌افزار را پوشش می‌دهد؛ نسخه و استفاده فعلی از سرویس/SLA نامعلوم است ([باز Q-26]).
- قابلیت‌های مستند Zabbix (research-oss #22–#28؛ اطمینان Medium برای صفحات مستندی که متن آن‌ها خوانده نشد):
  - **درخت سرویس کسب‌وکار (6.0+):** نگاشت سرویس به problem با **tag**، الگوریتم وضعیت، قواعد propagation (`as_is`/`increase`/`decrease`/`ignore`/`fixed`)، قواعد اضافی با N، N% یا **وزن** فرزندان (https://docs.ansible.com/ansible/8/collections/community/zabbix/zabbix_service_module.html، High برای پارامترها).
  - **اشیای SLA:** SLO، دوره گزارش، **schedule** هفتگی شبیه ساعات کاری، **excluded downtime**، انتخاب سرویس با tag؛ `sla.getsli` مقدار SLI به ازای دوره را برمی‌گرداند (https://www.zabbix.com/documentation/current/en/manual/it_services/sla، Medium).
  - نمای Services مسئله‌های **root cause** مؤثر بر وضعیت سرویس را نشان می‌دهد — درخت قطعی، نه RCA احتمالاتی (Medium).
  - API: `service.get`، `sla.getsli`، `problem.get`، `maintenance.*` (Medium-High).
  - Zabbix 7.0+ تحت **AGPLv3** است (https://blog.zabbix.com/striking-the-right-balance-zabbix-7-0-to-be-released-under-agplv3-license/، High). Zabbix 8.0 (OTLP، همبستگی، پیش‌بینی) اعلام شده ولی منتشر نشده است — باید دوباره بررسی شود (R-17).
- شکاف‌ها: تقویم جلالی و تعطیلات ایران به‌صورت بومی وجود ندارد (باید به schedule/excluded downtime/maintenance تبدیل شود)؛ KPI تراکنش و موفقیت کسب‌وکار در Zabbix نیست؛ اطمینان عددی و شواهد نقض وجود ندارد.

## Decision

[توصیه، مشروط به PoC-1]

1. **وضعیت سلامت سطح زیرساخت/سرویس فنی** و **SLI در دسترس‌پذیری فنی** هرجا درخت سرویس و SLA در Zabbix کافی باشد، از Zabbix **خوانده** می‌شود (`service.get`، `sla.getsli`، فقط‌خواندنی) و به‌عنوان **شاهد** وارد همبستگی می‌شود (FR-INT-008)، نه به‌عنوان جایگزین `HealthState`. پلتفرم منطق propagation زیرساختی را دوباره نمی‌سازد.
2. سلامت **سرویس بانکی** (`HealthState` با توضیح، expected-inactive، موفقیت کسب‌وکار، KPI تراکنش) در لایه سفارشی باقی می‌ماند؛ وضعیت سرویس Zabbix یکی از ورودی‌های آن با `derivation = DIRECT` است.
3. **منبع حقیقت تقویم** پلتفرم است (`calendar`). برای جلوگیری از منطق سرکوب تکراری، پنجره‌های سرویس و maintenance **می‌توانند** به maintenance/SLA schedule/excluded downtime در Zabbix همگام شوند (FR-CAL-008). این نوشتن در Zabbix است و **به‌صورت پیش‌فرض خاموش** است؛ فقط با تأیید صریح بانک، حساب جداگانه با حداقل دسترسی نوشتن روی اشیای مشخص و audit کامل فعال می‌شود (تعارض با [ADR-0020](ADR-0020-existing-platform-integration-read-only-pull.md) و NFR-IMP-002 آگاهانه و به‌صورت استثنای اختیاری ثبت می‌شود). در حالت پیش‌فرض فقط‌خواندنی، پلتفرم پنجره‌های Zabbix را می‌خواند و ناسازگاری با تقویم خود را به‌عنوان هشدار پیکربندی نشان می‌دهد.
4. **PoC-1 بدون هیچ نوشتن در Zabbix تولید** انجام می‌شود: هر ایجاد آزمایشی سرویس، tag یا SLA فقط روی نمونه **غیرتولیدی** Zabbix (UAT/آزمایشگاه با کپی پیکربندی یا داده مصنوعی) است؛ از Zabbix تولید فقط خواندن. اگر نمونه غیرتولیدی وجود نداشته باشد، PoC-1 فقط روی سرویس/SLAهای موجود (در صورت وجود) و به‌صورت فقط‌خواندنی اجرا می‌شود.
5. اگر PoC-1 منفی باشد (tag/درخت سرویس نتواند برش VS-1 و پنجره‌های expected-inactive را نمایش دهد)، سلامت و SLO سرویس فنی نیز در لایه سفارشی ساخته می‌شود (مسیر 0.1)، بدون تغییر سایر بخش‌های [ADR-0021](ADR-0021-build-buy-reuse-strategy.md).

## Alternatives considered

| گزینه | مزایا | معایب | نتیجه |
|---|---|---|---|
| خواندن سرویس/SLA از Zabbix به‌عنوان شاهد + سلامت بانکی سفارشی (انتخاب، مشروط) | بدون بازسازی درخت سرویس فنی؛ یک منبع حقیقت برای وضعیت زیرساخت | وابستگی به کیفیت tagها و مالکیت پیکربندی Zabbix (Q-26) | **پیشنهاد** |
| سلامت و SLO کاملاً سفارشی (0.1) | مستقل از Zabbix | تکرار قابلیت موجود؛ دو منبع حقیقت برای وضعیت فنی | جایگزین در صورت شکست PoC-1 |
| همه سلامت/SLA در Zabbix (بدون لایه سفارشی) | بدون کد | بدون تقویم جلالی، KPI کسب‌وکار، expected-inactive و شواهد با اطمینان | رد |
| همگام‌سازی نوشتنی تقویم به Zabbix به‌صورت پیش‌فرض | سرکوب یکسان در Zabbix و پلتفرم | نقض اصل فقط‌خواندنی؛ حساب با دسترسی نوشتن = هدف با ارزش (R-06) | رد به‌عنوان پیش‌فرض؛ قابلیت اختیاری |
| Zabbix 8.0 همبستگی/پیش‌بینی | قابلیت بومی | منتشر نشده؛ بدون اطمینان عددی تأییدشده | بازبینی پس از GA |

## Rationale

Benchmark نشان داد درخت سرویس و SLA فنی در Zabbix موجود است و ساخت دوباره آن با F-02 (بدون پیچیدگی غیرضروری) ناسازگار است؛ اما شواهد برای کفایت آن در سطح Medium است و پیکربندی واقعی بانک نامعلوم — بنابراین تصمیم Blocked بر PoC-1 است.

## Consequences

- مثبت: کاهش دامنه ماژول‌های `health` و `slo` در Phase 2؛ هم‌راستایی وضعیت پلتفرم با دید تیم زیرساخت.
- منفی: وابستگی به نسخه و API Zabbix (R-12، R-17)؛ اگر Zabbix در Incident در دسترس نباشد، شاهد مربوط `UNKNOWN` می‌شود (R-15).
- هزینه: همکاری با مالک پیکربندی Zabbix برای tagگذاری؛ در صورت فعال‌سازی همگام‌سازی نوشتنی، فرایند تأیید و audit.
- AGPL: خواندن API Zabbix تعهد AGPL ایجاد نمی‌کند؛ تغییر یا توزیع Zabbix نیازمند بررسی حقوقی است (R-18، Q-21).

## Evidence that would change this decision

- PoC-1: نمایش برش VS-1 و پنجره‌های expected-inactive با tag/درخت/SLA schedule ممکن نباشد یا `sla.getsli` در نسخه بانک موجود نباشد → سلامت/SLO فنی سفارشی.
- Q-26: نسخه Zabbix کمتر از 6.0 → سرویس‌های جدید در دسترس نیست.
- Q-26: مالک Zabbix اجازه tagگذاری/ایجاد سرویس را ندهد → فقط خواندن problemها (FR-INT-001).
- انتشار GA Zabbix 8.0 با همبستگی و پیش‌بینی → بازنگری مرز با ماژول `correlation`.

## Related requirements

FR-INT-001، FR-INT-008، FR-CAL-003، FR-CAL-008، FR-HLT-001، FR-HLT-002، FR-CAL-005، NFR-IMP-002، NFR-SEC-003، NFR-SEC-006

## Related questions

Q-08، Q-21، Q-26، PoC-1، R-06، R-12، R-15، R-17، R-18
