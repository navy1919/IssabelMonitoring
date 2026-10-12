# تحلیل شکاف رقابتی (Competitive Gap Analysis) — BSOIP

| فیلد | مقدار |
|---|---|
| Document ID | `DOC-BMK-002` |
| Version | `0.1` |
| Status | `Draft – Phase 0 – awaiting approval` |
| Date | `2026-10-11` (۱۴۰۵/۰۷/۱۹ – `1405/07/19`) |
| Owner | `Architecture` |
| Related documents | [product-benchmark.md](product-benchmark.md)، [build-buy-reuse-analysis.md](build-buy-reuse-analysis.md)، [functional-requirements.md](../requirements/functional-requirements.md)، [non-functional-requirements.md](../requirements/non-functional-requirements.md)، [ADR-0021](../architecture/architecture-decision-records/ADR-0021-build-buy-reuse-strategy.md)، [ADR-0022](../architecture/architecture-decision-records/ADR-0022-commercial-platforms-not-adopted.md)، [ADR-0023](../architecture/architecture-decision-records/ADR-0023-reuse-zabbix-services-and-sla.md)، [ADR-0024](../architecture/architecture-decision-records/ADR-0024-visualization-split-custom-operator-ui-optional-grafana.md)، [open-questions-and-assumptions.md](../architecture/open-questions-and-assumptions.md)، [master-plan.md](../roadmap/master-plan.md) |

> **قرارداد برچسب‌گذاری:** **[واقعیت]** فقط F-01..F-03؛ **[فرض A-xx]**؛ **[فرضیه]**؛ **[توصیه]**؛ **[باز Q-xx]** — مطابق [منشور محصول](../vision/product-charter.md).
> هر خانهٔ ماتریس یک **طبقه‌بندی قابلیت بر اساس مستندات عمومی** است، با شناسهٔ شواهد (`EV-*`) که منبع و URL آن در [product-benchmark.md](product-benchmark.md) آمده است. **هیچ خانه‌ای برای محیط بانک تأیید نشده است** مگر خلاف آن ذکر شود (در عمل: هیچ‌کدام).
> **این تحلیل مشاوره حقوقی نیست.** وضعیت تدارک محصولات آمریکایی «Blocked unless legally cleared» است و به **[باز Q-24]** ارجاع می‌شود.

---

## ۱. هدف و روش

این سند نیازهای بانکی فهرست‌شده توسط مالک محصول **[واقعیت F-03]** را در برابر نامزدهای واقع‌بینانه می‌گذارد و برای هر شکاف مهم توضیح می‌دهد **چرا پیکربندی یا یکپارچه‌سازی ابزارهای موجود کافی نیست — یا اینکه کافی است**. خروجی این سند ورودی ماتریس وزن‌دار و جدول تخصیص مسئولیت در [build-buy-reuse-analysis.md](build-buy-reuse-analysis.md) است.

**نامزدها:**
| کد | نامزد | توضیح |
|---|---|---|
| DT | Dynatrace Managed (آفلاین) | APM/AIOps تجاری |
| ITSI | Splunk ITSI روی Splunk Enterprise | لایهٔ سرویس/KPI تجاری |
| INS | IBM Instana self-hosted (Standard Edition) | APM تجاری |
| APD | Cisco AppDynamics on-prem / Virtual Appliance | APM تجاری |
| EX | ابزارهای موجود **بدون تغییر**: Zabbix + SolarWinds + Splunk + Elasticsearch | وضع موجود بانک **[فرض A-05، A-06]** |
| OSS | پشتهٔ متن‌باز: OTel Collector + `mq_prometheus` + Grafana OSS + PostgreSQL/ClickHouse (+ Keep/SkyWalking در صورت نیاز) | بدون لایهٔ بانکی سفارشی |
| PAY | محصولات ویژهٔ پرداخت: INETCO Insight / IR Transact | فقط ادعای فروشنده |

**کلید طبقه‌بندی:** Native / Configurable / Integration-dependent / Add-on license / Custom instrumentation / Custom development / Not supported / **Unverified** (= تأییدنشده — نیازمند تأیید فروشنده یا PoC). Confidence در پرانتز: H / M / L.

---

## ۲. ماتریس الف — نامزدهای تجاری و ویژهٔ پرداخت (فقط قابلیت؛ تدارک: Blocked/Unverified)

| # | نیاز بانکی | FR مرتبط | DT | ITSI | INS | APD | PAY |
|---|---|---|---|---|---|---|---|
| R01 | صدور/پذیرش کارت و سوئیچ | FR-TXN-001..005، FR-CAT-001/002 | Custom instrumentation (request attributes روی برنامهٔ سوئیچ اگر JVM/.NET باشد؛ ISO 8583 روی TCP decode بومی نمی‌شود) — Unverified (EV-D8) (M) | Configurable اگر لاگ سوئیچ index شده باشد (EV-T11) (M) | Custom instrumentation (SDK/OTel) — Unverified (EV-I9) (M) | Custom instrumentation (POJO BT rules) (EV-A6) (M) | Native — ادعای فروشنده، decode ISO 8583 — Unverified (EV-P1، EV-P2) (L–M) |
| R02 | یکپارچگی‌های شتاب | FR-CAT-006، FR-TXN-006 | Custom development (M) | Configurable/Custom development (مبتنی بر لاگ) (M) | Custom development (M) | Custom development (M) | Unverified (گویش ISO 8583 شتاب) (L) |
| R03 | کارت‌به‌کارت / A2A | FR-TXN-001..005، FR-TXN-008 | Custom instrumentation (M) | Configurable (لاگ) (M) | Custom instrumentation (M) | Configurable (BT rules) (M) | Unverified (L) |
| R04 | همراه‌بانک/اینترنت‌بانک، Android/PWA | FR-DX-005 (P7)، FR-DX-001/002، FR-TXN-003 | Native: RUM وب + plugin Android از طریق Cluster ActiveGate (EV-D13) (M) | Not supported بومی (منبع RUM لازم) (M) | Native: Android/iOS/RN/Flutter + EUM وب (EV-I8) (H) | Add-on license (RUM Peak، EUM Server) (EV-A4) (H) | Not supported (فقط سمت شبکه) (M) |
| R05 | ناهنجاری ورود/احراز هویت/نشست و خطاهای API | FR-DX-004، FR-TXN-004، FR-ANO-002/003 | Configurable (request attributes + metric events) (EV-D8، EV-D10) (M) | Configurable (KPI با SPL + adaptive threshold) (EV-T4) (M) | Configurable (Smart Alerts روی endpoint) (EV-I6) (M) | Configurable (BT + health rules) (M) | Unverified (L) |
| R06 | افت throughput، نسبت موفقیت، percentile تأخیر، timeout، الگوی غیرعادی | FR-TXN-002/004/011، FR-ANO-004 | Native (سرویس‌ها) / Configurable (calculated metrics)؛ **Business Events: Not supported روی Managed** (EV-D6) (H) | Configurable (EV-T3، EV-T4) (H) | Native (calls، error rate، p25–p99؛ throughput غیرعادی) (EV-I6) (H) | Native (BT) / Add-on (Transaction Analytics) (EV-A4) (H) | Native — ادعای فروشنده (L) |
| R06b | موفقیت فنی در برابر کسب‌وکار | FR-TXN-005 | Configurable (request attributes) (M) | Configurable (SPL) (M) | Custom instrumentation (M) | Configurable (data collectors) / Add-on (Analytics) (M) | Configurable (قواعد فیلد روی کد پاسخ) — ادعای فروشنده (L) |
| R07 | LB/WAF/NGINX/HAProxy/API gateway/ESB/runtime | FR-LB-001..007، FR-RUN-001/006 | Native/Integration-dependent (OneAgent NGINX؛ extension برای بقیه) — Unverified آفلاین (EV-D4) (L–M) | Configurable (لاگ) (M) | Native (sensor ACE/IIB) (EV-I5) (H)؛ NGINX/HAProxy جداگانه تأیید نشد (L) | Integration-dependent (machine-agent extension) (M) | Native (decode HTTP روی سیم) — ادعای فروشنده (L) |
| R08 | IBM MQ / RabbitMQ | FR-MQ-001..006 | Integration-dependent (extension MQ با oldest message؛ DDU) (EV-D12) (H/M)؛ RabbitMQ: Unverified (L) | Integration-dependent (add-on جامعه؛ depth/age تأیید نشد) (EV-T10) (M)؛ RabbitMQ: Configurable (API polling) (M) | Native (sensor MQ با PCF؛ oldestMessage طبق صفحهٔ محصول) (EV-I3) (H/M)؛ tracing MQ (EV-I4) | Integration-dependent (depth/age با machine-agent، تأیید نشد)؛ backend detection جاوا (EV-A5) (M) | MQ: Not supported برای depth؛ decode ترافیک (M) |
| R09 | گلوگاه DB/برنامه، disk I/O، خرابی وابستگی | FR-RUN-003/004، FR-HLT-002 | Native (ردیابی فراخوانی DB) (M) | Configurable (DB Connect/لاگ) (M) | Native (M) | Native / Add-on (DB Visibility) (M) | Native SQL decode — ادعای فروشنده (L) |
| R10 | پنجره‌ها، cut-off و تقویم ساتنا/پایا/پل | FR-CAL-001..007، FR-CAL-008 | Configurable (maintenance window از API) + Custom development برای baseline آگاه از cut-off (EV-D11) (H/M) | Configurable (maintenance window تکرارشونده، time policy) + Custom development (lookup تعطیلات) (EV-T4، EV-T7) (H) | Unverified (قابلیت تقویم یافت نشد) (L) | Unverified (L) | Unverified (L) |
| R11 | Open Banking و وابستگی‌های داخلی/خارجی | FR-CAT-006/007، FR-TXN-006، FR-LB-007 | Native (Smartscape برای بخش‌های instrument‌شده)؛ طرف خارجی: Custom (M) | Configurable (درخت سرویس دستی) (M) | Native (نقشهٔ وابستگی) (M) | Native (flow map) (M) | Native (زمان‌بندی hop شبکه) — ادعا (L) |
| R12 | توپولوژی و tracing | FR-CAT-007/008، FR-UI-003، FR-TXN-009 | Native (Smartscape، PurePath) — نیازمند OneAgent (H) | Configurable (درخت سرویس دستی؛ بدون tracing) (H) | Native (نقشهٔ وابستگی، tracing MQ/ACE) (H) | Native (flow map) (H) | Native — ادعا (L) |
| R13 | همبستگی چندسیگنالی با زمینهٔ کسب‌وکار | FR-COR-001..003، FR-INT-007 | Native (problemهای Davis)؛ business events روی Managed نیست (EV-D6، EV-D9) (H) | Native (episode، health سرویس) (EV-T6) (H) | Native (incident) (M) | Add-on license (AD&RCA) (EV-A2) (H) | Unverified (L) |
| R14 | خط زمانی incident، علت محتمل، شواهد، **اطمینان** | FR-COR-004/006/007 | Native RCA؛ **اطمینان عددی مستند نشده** (EV-D9) (H) | Native episode؛ RCA با اطمینان مستند نشده (EV-T6) (H) | Native incident؛ Probable RCA پیش‌نمایش، مشروط به اطمینان، self-hosted تأیید نشد (EV-I7) (M) | Add-on license (EV-A2) (H) | Not supported (INETCO)؛ Unverified (Iris بتا، IR) (L) |
| R15 | تشخیص ناهنجاری و پیش‌بینی | FR-ANO-001..008 | Native (auto-adaptive، seasonal)؛ forecast روی Managed: Unverified (EV-D10) (H/L) | Native (adaptive؛ پیش‌بینی ۳۰ دقیقه‌ای health score) (EV-T4، EV-T5) (H) | Native (adaptive بتا)؛ forecast: Unverified (EV-I6) (H/L) | Add-on license (EV-A2) (H) | Unverified (L) |
| R16 | یکپارچگی با SolarWinds/Zabbix/Splunk/ELK | FR-INT-001..008 | Custom development (API) (EV-D14) (L) | Integration-dependent (add-on SolarWinds آرشیوشده؛ Zabbix تأیید نشد) (EV-T9) (H/L) | Custom development / Unverified (L) | Custom development (L) | Unverified (L) |
| R17 | آفلاین درون‌سازمانی | NFR-DEP-001، NFR-SEC-010 | Native (مجوز آفلاین) — ولی فعال‌سازی/به‌روزرسانی از فروشنده (EV-D1، EV-D2) (H) | Native (Splunk Enterprise on-prem) (EV-T1) (H) | Native (Standard Edition air-gapped — ادعا) (EV-I1) (H) | Native (controller/VA)؛ air-gap: Unverified (EV-A2) (M) | Unverified (L) |
| — | **قابلیت تدارک قانونی برای بانک ایرانی** | — | **Blocked unless legally cleared** | **Blocked unless legally cleared** | **Blocked unless legally cleared** | **Blocked (Cisco: not offered in Iran)** | Unverified (غیرآمریکایی؛ ریسک تحریم ثانویه) |

---

## ۳. ماتریس ب — ابزارهای موجود، پشتهٔ متن‌باز و کار سفارشی باقی‌مانده

| # | نیاز بانکی | EX: ابزارهای موجود بدون تغییر | OSS: پشتهٔ متن‌باز بدون لایهٔ بانکی | کار باقی‌مانده در گزینهٔ E (مالک طبق B4) |
|---|---|---|---|---|
| R01 | کارت و سوئیچ | Configurable: جست‌وجو در Splunk/ES روی لاگ سوئیچ **اگر** index شده و کد پاسخ دارد — Unverified (Q-07، R-01) (M) | Custom development (filelog + OTTL برای تجزیه؛ طبقه‌بندی custom) (EV-O11) (M) | تعریف `TransactionType` و `OutcomeMapping` (کد پاسخ ISO 8583 به‌عنوان **داده**) → saved search در Splunk/ES؛ پلتفرم فقط تجمیع دقیقه‌ای را می‌کشد |
| R02 | شتاب | Configurable/Custom development مبتنی بر لاگ — Unverified (L) | Custom development (L) | `ExternalParty` + `TrustBoundary=interbank` در کاتالوگ؛ مقایسهٔ upstream/downstream (L0) |
| R03 | کارت‌به‌کارت / A2A | Configurable (جست‌وجوی لاگ) — Unverified؛ هدف PoC-2 (M) | Custom development / Configurable (M) | همان R01؛ سرویس پیش‌فرض VS-1 |
| R04 | همراه‌بانک/Android/PWA | سمت سرور: Configurable (لاگ API gateway/backend)؛ RUM: Not supported (M) | RUM: Custom instrumentation — Unverified (در پژوهش بررسی نشد) (L) | Phase 3: KPI سمت سرور از لاگ؛ RUM فقط Phase 7 با نیاز اثبات‌شده (FR-DX-005) |
| R05 | ورود/احراز هویت و خطای API | Configurable: جست‌وجو و هشدار در Splunk/ES؛ ML در Elastic: Add-on license (EV-O42) (M/H) | Configurable (تجزیهٔ لاگ) + Custom development (M) | saved search برای نرخ موفقیت/شکست ورود و latency (FR-DX-004)؛ baseline آگاه از تقویم در Phase 5 |
| R06 | throughput، موفقیت، percentile، timeout، الگوی غیرعادی | Configurable (جست‌وجو/aggregation) — Unverified برای نسخه‌های بانک؛ PoC-2 (M) | Configurable (کوئری ClickHouse) + تعریف KPI سفارشی (EV-O52) (M) | تفکیک `TxnOutcome` (SUCCESS / BUSINESS_DECLINE / TECHNICAL_FAILURE / TIMEOUT …)؛ قاعدهٔ «افت موفقیت با ورودی پایدار» (FR-ANO-004) |
| R07 | LB/WAF/NGINX/HAProxy/API GW/ESB/runtime | HAProxy و F5: Native (templateهای رسمی Zabbix — نصب بودن نامعلوم) (EV-O19، EV-O20) (H)؛ NGINX/WAF/API GW/ESB/JVM: Unverified (L) | HAProxy: Native beta (EV-O2)؛ NGINX: Native محدود به stub_status (EV-O3) (H)؛ بقیه Unverified | فقط نگاشت `ExternalRef` به itemهای Zabbix و طبقه‌بندی failure-domain (FR-LB-007)؛ API مستقیم فقط اگر شکاف اثبات شود |
| R08 | IBM MQ / RabbitMQ | RabbitMQ: Native (template رسمی) (EV-O18) (H)؛ IBM MQ: **Not supported رسمی** (فقط template جامعه ۲۰۱۸) (EV-O17) (H) | IBM MQ: Integration-dependent (`mq_prometheus`، Apache-2.0؛ نرخ‌ها نیازمند MQI statistics) (EV-O6..8) (H/M)؛ RabbitMQ: Native beta (EV-O1) (H) | استقرار `mq_prometheus` (استاندارد، نه custom) + طبقه‌بندی backlog گذرا/پایدار و time-to-full (FR-MQ-004) |
| R09 | DB/برنامه، disk I/O، وابستگی | میزبان و disk: Native (Zabbix/SolarWinds **[فرض A-06]**)؛ MSSQL: Native (EV-O21) (H)؛ خرابی وابستگی: Configurable (M) | SQL Server: Native beta (EV-O4) (H) | فقط ارجاع به عنوان شاهد؛ propagation اثر روی توپولوژی (FR-HLT-002) |
| R10 | تقویم ساتنا/پایا/پل | Configurable **جزئی**: SLA schedule هفتگی و excluded downtime و maintenance در Zabbix (EV-O23) (M)؛ بدون جلالی/تعطیلات/cut-off | Configurable جزئی: mute timing در Grafana (EV-O37) (M)؛ بدون جلالی | **Custom development (کوچک):** `BusinessCalendar` جلالی به‌عنوان منبع حقیقت + همگام‌سازی به maintenance/SLA در Zabbix (FR-CAL-008) |
| R11 | Open Banking و وابستگی داخلی/خارجی | Configurable: لاگ‌ها؛ درخت سرویس Zabbix فقط برای اشیای Zabbix (EV-O22) (M) | Custom development (M) | کاتالوگ با `TrustBoundary` و `Dependency` با provenance/confidence (FR-CAT-006/007) |
| R12 | توپولوژی و tracing | توپولوژی: Configurable (درخت سرویس فنی Zabbix) (EV-O22) (H)؛ tracing: Not supported؛ Zabbix 8.0 نقشهٔ راه (EV-O28) (M) | نمایش: Configurable (node graph Grafana، ~۲۰۰ گره) (EV-O36) (H)؛ tracing: Custom instrumentation (SkyWalking/SigNoz) (EV-O55، EV-O57) | مدل توپولوژی سرویس بانکی با provenance (هستهٔ تمایز)؛ tracing در Phase 7 |
| R13 | همبستگی چندسیگنالی با زمینهٔ کسب‌وکار | **Not supported بین ابزارها** (هر ابزار جزیره است)؛ درون Zabbix: Configurable قاعده‌محور (EV-O25) (M) | Custom development؛ Keep: گروه‌بندی قاعده/توپولوژی بدون اطمینان (EV-O53) (H) | موتور شواهد قطعی و توضیح‌پذیر (ADR-0012) روی KPI مشتق + رویدادهای Zabbix/SolarWinds + پرس‌وجوی federated |
| R14 | خط زمانی، علت محتمل، شواهد، اطمینان | Zabbix: root cause درختی قطعی (EV-O24) و cause/symptom (EV-O26) (M)؛ **اطمینان: Not supported** | Custom development؛ AI RCA در Coroot پولی (EV-O54) (H) | `CauseHypothesis` با rank، score، confidence، counter-evidence و سلب مسئولیت «همبستگی ≠ علیت» |
| R15 | ناهنجاری و پیش‌بینی | Elastic ML: Add-on license (EV-O42) (H)؛ Zabbix: Unverified برای نسخهٔ بانک؛ پیش‌بینی AI در 8.0 نقشهٔ راه (EV-O28) (M) | OpenSearch AD/forecast رایگان ولی خوشهٔ اضافه (EV-O43، EV-O44)؛ vmanomaly: Add-on (EV-O34) (H) | آمار سبک روی KPI مشتق در Phase 5 (FR-ANO-002..008) با چارچوب ارزیابی |
| R16 | یکپارچگی با SolarWinds/Zabbix/Splunk/ELK | Native (خود ابزارها هستند)؛ بین آن‌ها: Integration-dependent (add-on SolarWinds برای Splunk آرشیو شده — EV-T9؛ plugin Zabbix برای Grafana — EV-O29) (H) | Integration-dependent (plugin Grafana) + Custom development (connector) (M) | `connector-runtime` فقط‌خواندنی: Zabbix JSON-RPC، SWIS/SWQL، Splunk REST، ES `_search` (ADR-0020)؛ deep link (FR-INT-006) |
| R17 | آفلاین درون‌سازمانی | Native (از قبل درون‌سازمانی) — **ولی** تداوم مجوز/به‌روزرسانی Splunk/SolarWinds نامعلوم (EV-S10، Q-25، R-16) (M) | Configurable (OCB نیازمند mirror ماژول Go — EV-O13؛ plugin Grafana با ZIP — EV-O38) (H) | بستهٔ نصب آفلاین با اجزای کمتر (core + PostgreSQL + connector-runtime + exporter MQ) |

---

## ۴. فهرست شکاف‌ها: آیا پیکربندی/یکپارچه‌سازی ابزارهای موجود کافی است؟

ستون «کفایت پیکربندی/یکپارچه‌سازی»: **بله** = نیازی به توسعهٔ سفارشی نیست؛ **جزئی** = ابزار موجود محاسبه یا ذخیره را انجام می‌دهد ولی معنای بانکی باید افزوده شود؛ **خیر** = هیچ نامزد قابل‌تدارکی آن را پوشش نمی‌دهد.

| ID | شکاف | FR | کفایت پیکربندی/یکپارچه‌سازی | چرا | کوچک‌ترین راه بستن | شواهد | Conf. | اعتبارسنجی |
|---|---|---|---|---|---|---|---|---|
| G-01 | معنای نتیجهٔ تراکنش (موفقیت فنی ≠ کسب‌وکار؛ `TxnOutcome`) | FR-TXN-002/005 | **جزئی** | Splunk/ES می‌توانند شمارش و percentile را محاسبه کنند (اگر لاگ کد نتیجه داشته باشد)، ولی هیچ ابزاری نگاشت کد پاسخ → نتیجهٔ کانونی به‌عنوان **دادهٔ قابل‌نسخه‌بندی**، یکسان بین چند منبع و همراه با ثبت سطح همبستگی (L0–L3) ندارد. ITSI نزدیک‌ترین است اما Blocked. | تعریف `OutcomeMapping` در لایهٔ custom؛ **محاسبه درون Splunk/ES** با saved search؛ کشیدن فقط تجمیع دقیقه‌ای | EV-T11، EV-O11، EV-D6 | M | PoC-2 |
| G-02 | تقویم کاری بانکی (جلالی، تعطیلات، ساتنا/پایا/پل، `EXPECTED_INACTIVE`) | FR-CAL-001..007، FR-CAL-008 | **خیر** (فقط جزئی برای اجرا) | Zabbix SLA schedule هفتگی و excluded downtime دارد و Grafana mute timing؛ هیچ‌کدام تقویم جلالی، تعطیلات قمری که سالانه به‌صورت داده بارگذاری می‌شوند، روز نیمه‌تعطیل، cut-off چرخه‌ها یا تمایز «غیرفعال مورد انتظار» از «قطعی» را ندارند. Dynatrace و ITSI نیز فقط maintenance window دارند. | `BusinessCalendar` custom کوچک به‌عنوان منبع حقیقت + همگام‌سازی به Zabbix (تا منطق suppression دوبار پیاده نشود) | EV-O23، EV-O37، EV-D11، EV-T7، EV-O46 | H (نبودِ قابلیت در منابع)/M (Zabbix) | PoC-1 |
| G-03 | کاتالوگ سرویس بانکی و توپولوژی با provenance/confidence/approval بین منابع | FR-CAT-001..009 | **خیر** | درخت سرویس Zabbix فقط اشیای Zabbix را با tag نگاشت می‌کند و نمی‌تواند به entityهای Splunk/ES/SolarWinds/MQ ارجاع دهد؛ مفهوم provenance، confidence و گردش تأیید ندارد. SolarWinds و Splunk هم مدل مشترکی ندارند. | ماژول‌های `catalog` و `topology` روی PostgreSQL با `ExternalRef` | EV-O22، EV-O24 | H | PoC-1 (مرز با Zabbix) |
| G-04 | همبستگی شواهد بین منابع و رتبه‌بندی علت محتمل با اطمینان و شواهد نقض | FR-COR-001..007 | **خیر** | همبستگی Zabbix قاعده‌محور و درون Zabbix است؛ Keep بدون امتیاز اطمینان؛ RCA در Dynatrace اطمینان عددی مستند ندارد و Blocked است؛ ITSI و Instana هم Blocked. هیچ ابزار موجودی رویدادهای Zabbix، KPIهای مشتق از لاگ و وضعیت MQ را با هم روی توپولوژی بانکی نمی‌سنجد. | موتور قطعی کوچک در نقش `analytics` (ADR-0012) | EV-O25، EV-O26، EV-O53، EV-D9، EV-I7 | M–H | PoC-4 |
| G-05 | متریک‌های IBM MQ (depth، oldest age، backlog) | FR-MQ-002/004 | **بله، با یکپارچه‌سازی استاندارد** | Zabbix template رسمی ندارد، ولی `mq_prometheus` (Apache-2.0) این متریک‌ها را می‌دهد و connector-runtime یا Zabbix می‌تواند آن را بخواند. فقط طبقه‌بندی backlog (گذرا/پایدار، time-to-full) معنای افزوده است. نرخ‌ها به MQI statistics نیاز دارند [باز Q-27]. | استقرار exporter (بدون کد custom) + قاعدهٔ طبقه‌بندی | EV-O6..9، EV-O17 | H/M | PoC-3 |
| G-06 | LB (F5/HAProxy)، RabbitMQ، MSSQL | FR-LB-001/002، FR-MQ-003، FR-RUN-003 | **بله** (اگر templateها نصب باشند) | templateهای رسمی Zabbix وجود دارد؛ پلتفرم فقط itemها را از طریق API می‌خواند. | نگاشت `ExternalRef`؛ API مستقیم فقط در صورت شکاف | EV-O18..21 | H (وجود template)/L (نصب در بانک) | Q-08، Q-26 |
| G-07 | سلامت سرویس سطح زیرساخت و SLA | FR-HLT-001/002، FR-CAL-005/006 | **احتمالاً بله** | درخت سرویس Zabbix با وزن و propagation و SLA با schedule و `sla.getsli`؛ ساخت دوبارهٔ آن در پیش‌نویس اولیه تکراری بود. سلامت **کسب‌وکارآگاه** (G-01، G-02) همچنان custom است. | بازاستفاده و import وضعیت سرویس/SLA به‌عنوان شاهد (FR-INT-008) | EV-O22، EV-O23، EV-O27 | M | PoC-1 → [ADR-0023](../architecture/architecture-decision-records/ADR-0023-reuse-zabbix-services-and-sla.md) |
| G-08 | هشدار و اعلان | FR-NTF-001/002 | **بله** | کانال‌های موجود (actionهای Zabbix، ITSM، ایمیل) کار می‌کنند. پلتفرم حداکثر یک اعلان سطح incident به ازای هر incident سرویس می‌فرستد تا هشدار تکراری ایجاد نشود. | استفاده از کانال موجود [باز Q-14] | — | M | Phase 4 |
| G-09 | ناهنجاری روی KPI کسب‌وکار با baseline آگاه از تقویم | FR-ANO-002..008، FR-CAL-007 | **خیر** (در حال حاضر) | Elastic ML پولی است؛ ITSI Blocked؛ OpenSearch AD یک خوشهٔ اضافه است و تقویم جلالی ندارد؛ پیش‌بینی AI در Zabbix 8.0 هنوز نقشهٔ راه است. | آمار سبک (MAD، EWMA، فصلی) روی KPI مشتق در Phase 5 | EV-O42، EV-O43، EV-O34، EV-O28 | M | Phase 5؛ بازنگری پس از GA نسخهٔ 8.0 |
| G-10 | UI اپراتور فارسی RTL جلالی | FR-UI-001، NFR-USA-001 | **خیر، اگر الزامی باشد** | شواهدی از fa/RTL/جلالی در Grafana، Kibana یا OSD یافت نشد. اگر انگلیسی پذیرفتنی باشد، Grafana سهم بیشتری می‌گیرد. | UI سفارشی **فقط برای صفحات اپراتور** (سلامت، incident، شواهد، کاتالوگ، تقویم) | EV-O39، EV-O46 | L–M (نبودِ شواهد) | [باز Q-23] → [ADR-0024](../architecture/architecture-decision-records/ADR-0024-visualization-split-custom-operator-ui-optional-grafana.md) |
| G-11 | tracing انتها‌به‌انتها (L3) و RUM | FR-TXN-009، FR-DX-005 | **فعلاً شکاف عمدی** | ابزارهای موجود trace ندارند؛ نامزدهای تجاری Blocked؛ instrumentation مستقیم به تصمیم مالک محصول به تعویق افتاده است **[واقعیت F-02]**. | Phase 7 در صورت نیاز اثبات‌شده (SkyWalking/SigNoz/OTel SDK) | EV-O55، EV-O57، EV-I4 | H | — |
| G-12 | نمایش توپولوژی | FR-CAT-008، FR-UI-003 | **جزئی** | node graph در Grafana اگر API گره/یال بدهد قابل استفاده است (کندی بالای ۵۰۰ گره)؛ تعامل اپراتوری (approval، provenance) و فارسی custom است. | نمای اپراتور custom؛ Grafana اختیاری برای مهندسی | EV-O36 | H | Q-23 |
| G-13 | ذخیرهٔ telemetry خام و streaming | — (ADR-0009، ADR-0010) | **شکاف نیست** | Splunk/ES/Zabbix از قبل داده خام و retention را نگه می‌دارند؛ با ingestion محدود به KPI مشتق، شرایط Kafka برقرار نیست. | بدون store خام؛ PostgreSQL ابتدا؛ ClickHouse با trigger | EV-O49، EV-O52 | M | PoC-5 |
| G-14 | ناهنجاری ورود/احراز هویت و خطای API | FR-DX-004 | **جزئی** | تشخیص آستانه‌ای با جست‌وجو/هشدار موجود قابل پیکربندی است؛ baseline آگاه از تقویم و پیوند به سرویس بانکی custom است. | saved search + قاعدهٔ سلامت | EV-T4 (مقایسه)، EV-O42 | M | PoC-2 |
| G-15 | دید تراکنش شتاب/ساتنا/پایا وقتی لاگ کافی نیست | FR-TXN-003، FR-CAT-006 | **خیر — شکاف داده است نه ابزار** | اگر لاگ کد نتیجه/کلید ندارد، فقط ضبط شبکه (INETCO-مانند، Unverified و ریسک تحریم ثانویه) یا instrumentation (Phase 7) کمک می‌کند. | ابتدا EXP-01/PoC-2؛ در صورت شکست، تصمیم جداگانه | EV-P1، EV-S11 | L | PoC-2، R-01 |

### ۴.۱ جمع‌بندی شکاف‌ها
- **با پیکربندی/یکپارچه‌سازی کافی است** (نباید ساخته شود): متریک زیرساخت و دستگاه، LB/RabbitMQ/MSSQL از طریق templateهای Zabbix، متریک IBM MQ از طریق `mq_prometheus`، ذخیره و جست‌وجوی لاگ خام، محاسبهٔ KPI درون موتورهای جست‌وجو، هشدار و اعلان، و احتمالاً سلامت/SLA سطح زیرساخت (منوط به PoC-1).
- **شکاف واقعی که custom لازم دارد** (و تمایز محصول همین‌جاست): G-01 (بخش معنایی)، G-02، G-03، G-04، G-09 (سبک)، G-10 (مشروط به Q-23).
- **شکاف عمدی/به‌تعویق‌افتاده:** G-11، G-15.

---

## ۵. یافته‌ها، برآوردها و پرسش‌های حل‌نشده

### ۵.۱ یافته‌های مستند (از منابع؛ تأییدنشده در محیط بانک)
- هیچ نامزدی — تجاری یا متن‌باز — معنای نتیجهٔ تراکنش بانکی، تقویم جلالی/پنجره‌های ساتنا/پایا/پل، یا علت محتمل با اطمینان عددی و شواهد نقض را به‌صورت Native مدل نمی‌کند (EV-D6، EV-D9، EV-D11، EV-T7، EV-O53، EV-O54).
- Splunk ITSI نزدیک‌ترین نامزد در مدل‌سازی سرویس/KPI است ولی تقویم کسب‌وکار ندارد و Blocked است (EV-T3، EV-T7، EV-T12).
- Zabbix templateهای رسمی LB/RabbitMQ/MSSQL و درخت سرویس/SLA دارد؛ template رسمی IBM MQ ندارد (EV-O17..23).

### ۵.۲ برآوردها
- **[فرضیه]** بیشتر نیازهای R07–R09 و R16 با بازاستفاده بسته می‌شوند؛ وزن کار custom در R01–R06 و R10–R14 است.
- **[فرضیه]** کیفیت لاگ (R-01) بیش از انتخاب ابزار، کیفیت R01–R06 را تعیین می‌کند.

### ۵.۳ پرسش‌های حل‌نشده
Q-07، Q-08، Q-14، Q-21، Q-22، Q-23، Q-24، Q-25، Q-26، Q-27، Q-28 — جزئیات در [open-questions-and-assumptions.md](../architecture/open-questions-and-assumptions.md) و پرسش‌های VQ/BQ در [product-benchmark.md](product-benchmark.md).

---

## نکته برای بازبینی
1. ستون «EX» برای NGINX، WAF، API gateway، ESB و JVM در Zabbix/SolarWinds «Unverified» است چون پژوهش آن‌ها را پوشش نداده؛ کشف داده (EP-22) باید آن را روشن کند.
2. FR-CAL-008 و FR-INT-008 طبق افزودهٔ B8 جدیدند؛ نویسندهٔ سند FR باید آن‌ها را اضافه کند.
3. شناسه‌های شکاف `G-01..G-15` فقط در اسناد بنچمارک استفاده می‌شوند و با شناسه‌های دیگر spine تداخل ندارند.
