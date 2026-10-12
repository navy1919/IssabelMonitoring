# بنچمارک محصولات و مؤلفه‌ها (Product Benchmark) — BSOIP

| فیلد | مقدار |
|---|---|
| Document ID | `DOC-BMK-001` |
| Version | `0.1` |
| Status | `Draft – Phase 0 – awaiting approval` |
| Date | `2026-10-11` (۱۴۰۵/۰۷/۱۹ – `1405/07/19`) |
| Owner | `Architecture` |
| Related documents | [competitive-gap-analysis.md](competitive-gap-analysis.md)، [build-buy-reuse-analysis.md](build-buy-reuse-analysis.md)، [ADR-0021](../architecture/architecture-decision-records/ADR-0021-build-buy-reuse-strategy.md)، [ADR-0022](../architecture/architecture-decision-records/ADR-0022-commercial-platforms-not-adopted.md)، [ADR-0023](../architecture/architecture-decision-records/ADR-0023-reuse-zabbix-services-and-sla.md)، [ADR-0024](../architecture/architecture-decision-records/ADR-0024-visualization-split-custom-operator-ui-optional-grafana.md)، [open-questions-and-assumptions.md](../architecture/open-questions-and-assumptions.md)، [master-plan.md](../roadmap/master-plan.md)، [technology-evaluation.md](../architecture/technology-evaluation.md)، [functional-requirements.md](../requirements/functional-requirements.md) |

> **قرارداد برچسب‌گذاری:** **[واقعیت]** فقط F-01..F-03؛ **[فرض A-xx]**؛ **[فرضیه]**؛ **[توصیه]**؛ **[باز Q-xx]** — مطابق [منشور محصول](../vision/product-charter.md).
> همهٔ ادعاهای قابلیتی این سند از **مستندات عمومی فروشنده/پروژه** گرفته شده‌اند و برای محیط بانک **تأیید نشده‌اند** («نیازمند تأیید در نسخهٔ نصب‌شدهٔ بانک»). هیچ قیمت، هیچ entitlement قراردادی، هیچ مرجع بانکی و هیچ نتیجهٔ بنچمارکی در این سند ادعا نشده است.
> **این تحلیل مشاوره حقوقی نیست.** هر گزاره دربارهٔ تحریم و کنترل صادرات فقط بازگویی متن منابع است و باید در **[باز Q-24]** توسط واحد حقوقی/انطباق بانک بررسی شود.

---

## ۱. هدف، دامنه و روش

### ۱.۱ هدف
این سند چشم‌انداز محصولات و مؤلفه‌هایی را که می‌توانند بخشی از نیازهای BSOIP را پوشش دهند، **بر اساس نقش** دسته‌بندی می‌کند و برای هر کدام نقاط قوت، محدودیت‌ها، ملاحظات استقرار آفلاین، وضعیت مجوز/تحریم و جدول شواهد را ثبت می‌کند. نتیجه‌گیری‌های مقایسه‌ای در [competitive-gap-analysis.md](competitive-gap-analysis.md) و تصمیم راهبردی در [build-buy-reuse-analysis.md](build-buy-reuse-analysis.md) آمده است.

### ۱.۲ روش
- تاریخ پژوهش: `2026-10-11`. روش: جست‌وجوی وب و خواندن مستندات رسمی فروشنده، README و فایل LICENSE پروژه‌ها، متن مقررات و خلاصه‌های مؤسسات حقوقی.
- هیچ محصولی نصب یا آزمایش نشده است. **هیچ ادعایی در محیط بانک تأیید نشده است.**
- برخی صفحات قابل خواندن نبودند (فهرست کامل در بخش ۷). به‌ویژه **متن بدنهٔ صفحات مستندات Zabbix** از طریق ابزار خواندن نمایش داده نشد؛ ادعاهای سطح مستند Zabbix در نتیجه «Medium» هستند مگر آنکه از منبع دیگری (صفحات integrations، مستند ماژول Ansible، بلاگ Zabbix) تأیید شده باشند.

### ۱.۳ کلیدهای طبقه‌بندی
| کلید | معنا |
|---|---|
| Native | بدون تنظیم خاص در محصول وجود دارد |
| Configurable | در محصول وجود دارد ولی مدل‌سازی/پیکربندی می‌خواهد |
| Integration-dependent | به محصول/exporter/extension دیگری نیاز دارد |
| Add-on license | مجوز افزوده یا tier بالاتر لازم است |
| Custom instrumentation | نیازمند instrumentation برنامه (agent/SDK/rule) |
| Custom development | بانک یا تیم پروژه باید آن را بنویسد |
| Not supported | در منابع، پشتیبانی نشده یا صراحتاً غیرموجود |
| Unverified | **تأییدنشده — نیازمند تأیید فروشنده یا PoC** |

### ۱.۴ سطوح اطمینان (Confidence)
- **High:** مستند رسمی یا متن مقررات خوانده شد.
- **Medium:** صفحهٔ رسمی فقط بخشی خوانده شد، یا اظهار کارمند فروشنده در انجمن، یا منبع ثانویه.
- **Low:** انجمن/بازاریابی، استنتاج یا «نبودِ شواهد».

ستون **«تأیید برای این محیط»** در همهٔ جدول‌ها تقریباً همیشه «خیر» است؛ تأیید فقط از راه PoC (بخش PoC در [build-buy-reuse-analysis.md](build-buy-reuse-analysis.md)) یا پاسخ کتبی فروشنده/بانک ممکن است.

### ۱.۵ شناسهٔ شواهد
برای ارجاع بین سه سند بنچمارک، هر ادعا یک شناسه دارد: `EV-S*` (تحریم)، `EV-D*` (Dynatrace)، `EV-T*` (Splunk ITSI)، `EV-I*` (Instana)، `EV-A*` (AppDynamics)، `EV-P*` (محصولات پرداخت/میان‌افزار)، `EV-O*` (متن‌باز و ابزارهای موجود). این شناسه‌ها در [competitive-gap-analysis.md](competitive-gap-analysis.md) و [build-buy-reuse-analysis.md](build-buy-reuse-analysis.md) استفاده شده‌اند.

---

## ۲. شرط دروازه‌ای: تحریم و کنترل صادرات (ابتدا بخوانید)

> **این تحلیل مشاوره حقوقی نیست.** آنچه در ادامه می‌آید فقط گزارش متن منابع است. نتیجه‌گیری حقوقی، تصمیم خرید یا ادامهٔ استفاده/به‌روزرسانی هر نرم‌افزار خارجی (آمریکایی یا غیرآمریکایی) به **[باز Q-24]** ارجاع می‌شود. وضعیت مجوز و به‌روزرسانی Splunk و SolarWinds موجود بانک **نامعلوم** است **[باز Q-25]**.

| ID | آنچه منبع می‌گوید | منبع | Confidence |
|---|---|---|---|
| EV-S1 | ITSR بند 31 CFR 560.204 صادرات، صادرات مجدد، فروش یا تأمین مستقیم/غیرمستقیم هر کالا، فناوری یا خدمت از آمریکا یا توسط «US person» به ایران را جز با مجوز ممنوع می‌کند؛ شامل تأمین از کشور ثالث «با علم یا دلیلی برای علم» به مقصد ایران. | https://www.ecfr.gov/current/title-31/subtitle-B/chapter-V/part-560/subpart-B/section-560.204 | High |
| EV-S2 | EAR بند 15 CFR 746.7: برای ایران «هیچ استثنای مجوزی» قابل استفاده نیست؛ مجوزها جز موارد بشردوستانه/ایمنی هوانوردی «عموماً رد می‌شوند». مجوز OFAC به‌جای مجوز BIS پذیرفته می‌شود. | https://www.law.cornell.edu/cfr/text/15/746.7 | High |
| EV-S3 | OFAC General License D-2 (سپتامبر ۲۰۲۲) فقط «ارتباطات اینترنتی شخصی» را پوشش می‌دهد؛ نرم‌افزار سازمانی APM/ITOM/observability در دسته‌های آن نیست. | https://ofac.treasury.gov/media/928096/download | High (متن)؛ Medium (تفسیر خارج بودن observability) |
| EV-S4 | در ۸ اکتبر ۲۰۲۰ بخش مالی ایران ذیل E.O. 13902 شناسایی و ۱۸ بانک بزرگ ایرانی در فهرست SDN قرار گرفتند؛ اشخاص غیرآمریکایی برای تراکنش‌های «قابل‌توجه» در بخش مالی ایران در معرض **تحریم ثانویه** هستند. | https://www.hsfkramer.com/notes/sanctions/2020-10/ofac-significantly-expands-sanctions-on-the-iranian-financial-sector ؛ https://sanctionsnews.bakermckenzie.com/us-government-imposes-sanctions-on-the-iranian-financial-sector-under-executive-order-13902-and-designates-18-iranian-banks/ | High (خلاصهٔ مؤسسات حقوقی) |
| EV-S5 | **Cisco** (مالک Splunk و AppDynamics): «Cisco does not offer Cisco Products in … Iran …»؛ «No party may transfer Cisco Products to any of these locations». | https://cisco.com/c/en/us/about/legal/global-export-trade/general-export-compliance.html | High |
| EV-S6 | صفحهٔ کنترل صادرات **Splunk**: مشتری نباید محصول را برخلاف قوانین صادر/منتقل کند و تأیید می‌کند در کشور تحت تحریم نیست؛ به embargo.splunk.com پیوند می‌دهد (نام ایران در متن رندرشده نیامده). | https://splunk.com/en_us/legal/export-controls.html ؛ https://embargo.splunk.com/ | High |
| EV-S7 | **Dynatrace**: بند صادرات قرارداد MSA قابل دریافت نبود (robots). شرکت آمریکایی است، پس EV-S1/S2 به حکم قانون اعمال می‌شود؛ متن قرارداد تأیید نشده است. | https://www.dynatrace.com/company/trust-center/customers/subscription-agreement/ | Medium |
| EV-S8 | **IBM** (Instana و IBM MQ): بیانیهٔ اختصاصی ایران یافت نشد؛ IBM «US person» است. سابقه: مسدود کردن دسترسی SoftLayer از ایران در ۲۰۱۶ (خبر). | https://www.datacenterknowledge.com/archives/2016/02/11/ibm-softlayer-blocks-services-iran-us-lifts-sanctions | Medium |
| EV-S9 | سابقهٔ اجرایی: SAP (شرکت آلمانی) در ۲۹ آوریل ۲۰۲۱ بیش از ۸ میلیون دلار پرداخت کرد چون نرم‌افزار، **ارتقا و patch** آمریکایی‌منشأ بیش از ۲۰٬۰۰۰ بار به کاربران ایرانی، عمدتاً از طریق شرکای کشور ثالث، تحویل شده بود. یعنی **به‌روزرسانی از طریق reseller هم تخلف تلقی شده است.** | https://www.justice.gov/usao-ma/pr/sap-admits-thousands-illegal-exports-its-software-products-iran-and-enters-non | High |
| EV-S10 | مجوزهای term در Splunk Enterprise منقضی می‌شوند. گزارش انجمن: پس از انقضا indexing ادامه می‌یابد ولی **search مسدود می‌شود** تا مجوز معتبر اعمال شود. متن رسمی بازیابی نشد. | https://community.splunk.com/t5/Installation/License-Expired-How-long-will-indexing-Continue/td-p/616989 | Low–Medium |
| EV-S11 | فروشندگان غیرآمریکایی (INETCO کانادا؛ IR استرالیا) خارج از صلاحیت اولیهٔ OFAC‌اند ولی با (الف) رژیم‌های ملی خود، (ب) تحریم ثانویهٔ E.O. 13902 و (ج) قواعد re-export در EAR در صورت محتوای آمریکایی‌منشأ مواجه‌اند. متن مقررات کانادا خوانده نشد. | https://laws.justice.gc.ca/eng/regulations/SOR-2010-165/20230804/P1TT3xt3.html (رندر نشد) | Low |

**پیامدهای برگرفته (نه نتیجه‌گیری حقوقی):**
- خرید، تمدید، پشتیبانی یا دریافت به‌روزرسانی/extension برای Dynatrace، Splunk (شامل ITSI)، AppDynamics و Instana برای یک بانک ایرانی، بر اساس متن منابع، بدون مجوز اختصاصی OFAC ممنوع **به نظر می‌رسد**. وضعیت این محصولات در همهٔ سندهای بنچمارک: **«Blocked unless legally cleared»**.
- «نصب آفلاین» مسئله را حل نمی‌کند: فایل فعال‌سازی مجوز، بستهٔ به‌روزرسانی و extension خودشان تحویل از فروشنده‌اند (EV-S9، EV-D2).
- وضعیت Splunk و SolarWinds **موجود** بانک (perpetual/term، مستقیم/reseller، فعال/منقضی) نامعلوم است **[باز Q-25]** و هم بر ریسک حقوقی و هم بر **تداوم مسیر «بازاستفاده از Splunk»** اثر دارد (EV-S10؛ ریسک R-16).
- پژوهش، وضعیت حقوقی **نرم‌افزار متن‌باز منتشرشده توسط شرکت‌های آمریکایی** (مثلاً `mq-metric-samples` از IBM، Grafana از Grafana Labs) یا دریافت آن از مخازن میزبانی‌شده در آمریکا را بررسی **نکرده است**. این نیز باید در دامنهٔ **[باز Q-24]** قرار گیرد (نکته برای بازبینی ۱).

---

## ۳. چشم‌انداز محصولات بر اساس نقش (B1)

| نقش | محصول/مؤلفه | مجوز/مدل | وضعیت تدارک | جایگاه در گزینهٔ E |
|---|---|---|---|---|
| پلتفرم کامل APM/AIOps (تجاری) | Dynatrace Managed | تجاری، اشتراکی | Blocked unless legally cleared | استفاده نمی‌شود ([ADR-0022](../architecture/architecture-decision-records/ADR-0022-commercial-platforms-not-adopted.md)) |
| | Splunk ITSI (premium app روی Splunk Enterprise) | Add-on license | Blocked unless legally cleared | استفاده نمی‌شود |
| | IBM Instana (self-hosted) | تجاری | Blocked unless legally cleared | استفاده نمی‌شود |
| | Cisco AppDynamics (on-prem controller / Virtual Appliance) | تجاری | Blocked (Cisco: not offered in Iran) | استفاده نمی‌شود |
| پایش ویژهٔ بانک/پرداخت | INETCO Insight | تجاری (کانادا) | Unverified؛ ریسک تحریم ثانویه | فقط اگر لاگ کافی نباشد و حقوقی تأیید کند (بازنگری) |
| | IR Transact | تجاری (استرالیا) | Unverified؛ ریسک تحریم ثانویه | همان |
| | meshIQ (Nastel) — MQ | تجاری (به‌طور عمومی آمریکایی تلقی می‌شود؛ تأیید نشده) | به احتمال زیاد Blocked | استفاده نمی‌شود |
| ابزارهای موجود بانک (بازاستفاده) | Zabbix | AGPLv3 (7.0+) | در بانک موجود **[فرض A-06]** | منبع اصلی زیرساخت، هشدار، و نامزد سرویس/SLA ([ADR-0023](../architecture/architecture-decision-records/ADR-0023-reuse-zabbix-services-and-sla.md)) |
| | SolarWinds (Orion/SWIS) | تجاری (آمریکا) | موجود؛ وضعیت مجوز نامعلوم [باز Q-25] | منبع شبکه/دستگاه (Should) |
| | Splunk Enterprise | تجاری (آمریکا) | موجود؛ وضعیت مجوز نامعلوم [باز Q-25] | منبع لاگ و محاسبهٔ KPI با saved search |
| | Elasticsearch | SSPL/ELv2/AGPLv3 (free tier) یا اشتراک | موجود؛ نوع مجوز نامعلوم | منبع لاگ و محاسبهٔ KPI با aggregation |
| خط لولهٔ telemetry | OpenTelemetry Collector (contrib) | Apache-2.0 | آزاد | اختیاری (`edge-collector` فقط در صورت نیاز) |
| | IBM `mq_prometheus` (`mq-metric-samples`) | Apache-2.0 | آزاد (نکتهٔ Q-24) | منبع استاندارد متریک IBM MQ |
| موتورهای ذخیره‌سازی | PostgreSQL (+ TimescaleDB TSL) | PostgreSQL License / TSL | آزاد | PostgreSQL: ذخیرهٔ واحد metadata و KPI مشتق |
| | ClickHouse | Apache-2.0 | آزاد | فقط با trigger ([ADR-0008](../architecture/architecture-decision-records/ADR-0008-telemetry-store-postgresql-first-clickhouse-on-trigger.md)) |
| | VictoriaMetrics | Apache-2.0 (community) / Enterprise | آزاد/تجاری | استفاده نمی‌شود |
| | OpenSearch / Elasticsearch | Apache-2.0 / چندمجوزی | آزاد | فقط به‌عنوان منبع موجود؛ خوشهٔ جدید نه |
| Streaming | Apache Kafka | Apache-2.0 | آزاد | فعلاً رد ([ADR-0010](../architecture/architecture-decision-records/ADR-0010-buffering-without-kafka-initially.md)) |
| مصورسازی | Grafana OSS | AGPLv3 | آزاد (بازبینی AGPL: Q-21) | اختیاری برای نماهای مهندسی ([ADR-0024](../architecture/architecture-decision-records/ADR-0024-visualization-split-custom-operator-ui-optional-grafana.md)) |
| AIOps/همبستگی متن‌باز | Keep | MIT + `ee/` | آزاد؛ ریسک نقشهٔ راه (خرید توسط Elastic) | استفاده نمی‌شود (الگوی مرجع) |
| | Coroot | Apache-2.0 CE + tier پولی | RCA/RBAC/air-gap پولی | استفاده نمی‌شود |
| | SigNoz | MIT + `ee/` | آزاد | استفاده نمی‌شود |
| | Apache SkyWalking | Apache-2.0 | آزاد | گزینهٔ Phase 7 (نیازمند agent) |
| | OpenObserve | AGPLv3 | آزاد | استفاده نمی‌شود |
| | Netdata | GPLv3+ (agent) + **NCUL1** (UI) | UI غیر OSS | **رد** |

---

## ۴. محصولات تجاری کامل (APM/AIOps)

### ۴.۱ Dynatrace Managed (حالت آفلاین)

**نقاط قوت (مستند فروشنده):** حالت آفلاین رسمی با مجوز آفلاین؛ RCA مبتنی بر Smartscape و fault-tree؛ baseline تطبیقی و فصلی در metric events؛ extension برای IBM MQ با متریک oldest message؛ request attributes برای زمینهٔ کسب‌وکار روی Managed؛ RUM موبایل و وب.

**محدودیت‌ها:** Grail، و در نتیجه **Business Events / Business Observability، روی Managed در دسترس نیست**؛ AppEngine و AutomationEngine فقط SaaS (Medium)؛ در Managed آفلاین Extensions Hub در دسترس نیست و Application Security «کاملاً غایب» است (انجمن، Low–Medium)؛ امتیاز عددی اطمینان برای RCA در مستند دیده نشد؛ مفهوم تقویم کسب‌وکار (ساتنا/پایا/پل، تعطیلات ایران) وجود ندارد و باید با maintenance window از طریق API مدل شود (custom).

**ملاحظات استقرار/آفلاین:** فعال‌سازی مجوز آفلاین از طریق فرم `mcsvc.dynatrace.com` (از ماشین دیگر با اینترنت)؛ به‌روزرسانی‌ها با ایمیل اعلام و دستی اعمال؛ پنجرهٔ پشتیبانی نسخه ۳ ماه (+۱ با Enterprise Success). یعنی **هر به‌روزرسانی رابطهٔ فعال با فروشنده لازم دارد** که با EV-S9 در تعارض است.

**مجوز/تحریم:** Blocked unless legally cleared (EV-S1، EV-S2، EV-S7). قیمت: یافت نشد.

| ID | ادعا | طبقه‌بندی | وابستگی (edition/license/agent) | Confidence | تأیید برای این محیط | منبع |
|---|---|---|---|---|---|---|
| EV-D1 | Managed در حالت آفلاین با مجوز آفلاین که «همهٔ قابلیت‌های نیازمند اینترنت را غیرفعال می‌کند»؛ گزارش دستی مصرف مجوز. | Native | Managed + offline licence | High | خیر | https://docs.dynatrace.com/managed/managed-cluster/installation/dynatrace-offline |
| EV-D2 | فعال‌سازی از طریق `mcsvc.dynatrace.com`؛ به‌روزرسانی با بستهٔ اعلام‌شده در ایمیل؛ bundle agentها دستی آپلود می‌شوند. | Native (فرایند) | رابطهٔ فعال با فروشنده برای هر به‌روزرسانی | High | خیر؛ از نظر حقوقی Blocked | همان |
| EV-D3 | محدودیت‌های آفلاین: بدون Mission Control، بدون همگام‌سازی خودکار مجوز، گواهی self-signed پیش‌فرض، «Premium HA» پشتیبانی نمی‌شود. | — | — | High | خیر | همان |
| EV-D4 | در Managed کاملاً آفلاین Extensions Hub در دسترس نیست؛ synthetic فقط با private ActiveGate؛ Application Security غایب. | — | — | Low–Medium (انجمن) | خیر | https://community.dynatrace.com/t5/Dynatrace-Managed-Q-A/What-features-are-not-available-when-using-Offline-licenses-in/m-p/288780 |
| EV-D5 | Grail فقط SaaS؛ نقشهٔ راه Managed شامل Grail نیست. AppEngine/AutomationEngine/EdgeConnect نیز SaaS (انجمن). | Not supported (Managed) | — | High (Grail)؛ Medium (بقیه) | خیر | https://docs.dynatrace.com/managed/upgrade/unavailable-in-managed ؛ https://community.dynatrace.com/t5/Dynatrace-Managed-Q-A/EOL-Dynatrace-Managed/m-p/210769 |
| EV-D6 | Business Events به Grail نیاز دارد → **KPIهای business-event روی Managed/آفلاین در دسترس نیست**. | Not supported (Managed) | SaaS + Grail | High | — | https://www.dynatrace.com/hub/detail/business-events ؛ https://community.dynatrace.com/t5/Dynatrace-Managed-Q-A/Business-Analytics-SaaS-deplyment-only/m-p/208106 |
| EV-D7 | کارمند Dynatrace (۲۰۲۳): Managed متوقف نمی‌شود و به‌روزرسانی ماهانه دارد؛ نوآوری مبتنی بر Grail فقط SaaS. | — | — | Medium | — | https://community.dynatrace.com/t5/Dynatrace-Managed-Q-A/EOL-Dynatrace-Managed/m-p/210769 |
| EV-D8 | request attributes روی Managed: داده از درخواست وب و آرگومان متدهای Java/.NET/PHP و OneAgent SDK؛ برای فیلتر، تشخیص خطای منطق کسب‌وکار و calculated service metrics؛ سقف ۱۰۰ attribute در هر درخواست؛ masking محرمانه. | Configurable / Custom instrumentation | OneAgent full-stack | High | خیر | https://docs.dynatrace.com/managed/observe/application-observability/services/request-attributes |
| EV-D9 | RCA داویس روی Managed: Smartscape + fault-tree؛ «همبستگی زمانی به‌تنهایی کافی نیست»؛ یک problem واحد. **امتیاز عددی اطمینان ذکر نشده.** | Native | OneAgent topology | High | خیر | https://docs.dynatrace.com/managed/dynatrace-intelligence/root-cause-analysis/concepts |
| EV-D10 | metric events با آستانهٔ ایستا، auto-adaptive و seasonal baseline؛ سقف ۱۰٬۰۰۰ پیکربندی. | Native / Configurable | — | High | خیر | https://docs.dynatrace.com/managed/discover-dynatrace/platform/davis-ai/anomaly-detection/set-up-a-customized-anomaly-detector/how-to-set-up/metric-events |
| EV-D11 | maintenance window برنامه‌ریزی‌شده/نشده، خارج از baseline؛ سقف ۲٬۰۰۰. **مفهوم تقویم کسب‌وکار دیده نشد.** | Configurable / Custom development | — | High (قابلیت)؛ Medium (نبودِ تقویم) | خیر | https://docs.dynatrace.com/managed/analyze-explore-automate/notifications-and-alerting/maintenance-windows |
| EV-D12 | extension IBM MQ: depth، oldest message، channel status، DLQ، enqueue/dequeue؛ جمع‌آوری محلی (MQ 9.1+) یا از راه دور با ActiveGate (~300 MB RAM به ازای هر اتصال QM)؛ مصرف DDU. صفحه Managed را ذکر نمی‌کند. | Integration-dependent | Extension 2.0, ActiveGate, DDUs | High (قابلیت)؛ Medium (Managed) | خیر؛ import آفلاین extension (EV-D4) | https://docs.dynatrace.com/docs/ingest-from/extensions/supported-extensions/ibm-mq |
| EV-D13 | RUM موبایل روی Managed: plugin Gradle/React Native به Cluster ActiveGate؛ تزریق خودکار RUM JS وب. | Native | RUM licence (DEM units) | Medium | خیر | https://community.dynatrace.com/t5/Dynatrace-Managed-Q-A/Confirming-RUM-Data-Flow-for-Web-and-Mobile-Monitoring-Managed/m-p/282394 |
| EV-D14 | connector بومی برای Zabbix یا SolarWinds یافت نشد؛ یکپارچگی از طریق API ingest یا extension سفارشی. | Custom development | — | Low (نبودِ شواهد) | خیر | (یافتهٔ عدم وجود) |

### ۴.۲ Splunk IT Service Intelligence (ITSI)

**نقاط قوت:** مدل سرویس → KPI → entity؛ health score وزن‌دار با وابستگی سرویس‌ها؛ adaptive thresholding با time policy (روز کاری/تعطیل، ساعات خاص)؛ پیش‌بینی health score حدود ۳۰ دقیقه؛ Episode Review برای تجمیع رویدادها؛ maintenance window تکرارشونده؛ import سرویس/entity از CSV/جست‌وجو. نزدیک‌ترین «خرید» به لایهٔ سرویس/KPI که بانک نیاز دارد، چون **روی Splunk موجود بانک** سوار می‌شود.

**محدودیت‌ها:** مجوز جداگانه (Add-on license)؛ sizing سنگین (search head اختصاصی با 32 vCPU، indexer حداقل 16 core و 32 GB)؛ تقویم کسب‌وکار ایرانی/cut-off ندارد (custom با lookup)؛ RCA با امتیاز اطمینان در صفحهٔ aggregation توصیف نشده؛ add-on رسمی SolarWinds آرشیو شده (آخرین نسخه ۲۰۲۱)؛ add-on IBM MQ جامعه‌محور است و depth/oldest age را تأیید نمی‌کند. KPIهای تراکنشی در نهایت همان جست‌وجوهای SPL روی لاگ‌های موجودند، پس **امکان‌پذیری به کیفیت لاگ وابسته است نه به ITSI** (EV-T11).

**ملاحظات استقرار/آفلاین:** روی Splunk Enterprise درون‌سازمانی نصب‌پذیر؛ SSL اجباری؛ Java روی search headها.

**مجوز/تحریم:** Blocked unless legally cleared (EV-S5، EV-S6، EV-T12). افزودن ITSI یعنی تراکنش جدید با فروشندهٔ آمریکایی. قیمت: یافت نشد.

| ID | ادعا | طبقه‌بندی | وابستگی | Confidence | تأیید برای این محیط | منبع |
|---|---|---|---|---|---|---|
| EV-T1 | ITSI مجوز جداگانه علاوه بر Splunk Enterprise لازم دارد؛ روی on-prem نصب‌پذیر. | Add-on license | Splunk Enterprise + ITSI | High | خیر | https://help.splunk.com/en/splunk-it-service-intelligence/splunk-it-service-intelligence/install-and-upgrade/5.0/planning/plan-your-itsi-deployment |
| EV-T2 | sizing: search head 32 vCPU؛ indexer ≥16 core/32 GB؛ SSL اجباری؛ SHC بالای ~۲۰۰ KPI. | — | — | High | خیر | همان |
| EV-T3 | سرویس → KPI (saved search تکرارشونده) → entity؛ health score = میانگین وزن‌دار شدت KPIها و وابستگی‌ها؛ glass table. | Native | ITSI | High | خیر | https://help.splunk.com/en/splunk-it-service-intelligence/splunk-it-service-intelligence/visualize-and-assess-service-health/4.18/overview/overview-of-service-insights-in-itsi |
| EV-T4 | adaptive thresholding شبانه از ۷ روز تاریخچه (۱۴/۳۰/۶۰ قابل تنظیم)؛ time policy؛ فقط سطح KPI. | Native / Configurable | ITSI | High | خیر | https://docs.splunk.com/Documentation/ITSI/4.13.1/SI/AT |
| EV-T5 | Predictive Analytics: پیش‌بینی health score حدود ۳۰ دقیقه؛ پیش‌نیازها بررسی نشد. | Native | ITSI (وابستگی MLTK تأیید نشده) | High (قابلیت)/Low (پیش‌نیاز) | خیر | https://docs.splunk.com/Documentation/ITSI/4.13.3/SI/PA |
| EV-T6 | aggregation policy → episode؛ Smart Mode با ML؛ RCA با اطمینان توصیف نشده. | Native / Configurable | ITSI | High | خیر | https://help.splunk.com/en/splunk-it-service-intelligence/splunk-it-service-intelligence/detect-and-act-on-notable-events/4.20/event-aggregation/overview-of-aggregation-policies-in-itsi |
| EV-T7 | maintenance window یک‌باره یا تکرارشونده؛ موتور تقویم کسب‌وکار نیست. | Configurable / Custom development | ITSI | High | خیر | https://help.splunk.com/en/splunk-it-service-intelligence/splunk-it-service-intelligence/administer/5.0/maintenance-windows/schedule-maintenance-downtime-in-itsi |
| EV-T8 | import entity/سرویس از جست‌وجو و CSV. | Configurable | ITSI | Medium | خیر | https://docs.splunk.com/Documentation/ITSI/latest/Entity/DiscoverEntities |
| EV-T9 | SolarWinds Add-on for Splunk: هشدار، inventory، خروجی SWQL؛ **آرشیو شده**، نسخهٔ ۱.۲.۰ (مارس ۲۰۲۱). | Integration-dependent | Splunk | High | خیر | https://splunkbase.splunk.com/app/3584/ |
| EV-T10 | IBM MQ Modular Input (شخص ثالث): پیام صف و وضعیت کانال؛ depth/oldest age تأیید نشده. | Integration-dependent | community | Medium | خیر | https://splunkbase.splunk.com/app/3004 |
| EV-T11 | KPIهای تراکنشی در ITSI جست‌وجوی SPL روی لاگ‌های موجود است؛ امکان‌پذیری به محتوای لاگ وابسته است. | Configurable / Custom development | کیفیت لاگ | Medium (استنتاج از EV-T3) | خیر | استنتاج |
| EV-T12 | ITSI محصول Cisco/Splunk است و EV-S5/S6 بر آن اعمال می‌شود. | — | — | High | — | بخش ۲ |

### ۴.۳ IBM Instana (self-hosted)

**نقاط قوت:** Standard Edition با ادعای «full mirroring in air-gapped environments»؛ هم‌ارزی قابلیت با SaaS (طبق IBM)؛ sensor بومی IBM MQ (PCF) و ACE/IIB؛ tracing پیام‌های MQ با user exit و انتشار W3C `traceparent`؛ Smart Alerts با blueprint کاهش/افزایش غیرعادی throughput و percentileها؛ پایش موبایل Android/iOS/RN/Flutter با endpoint خودمیزبان.

**محدودیت‌ها:** Probable Root Cause در پیش‌نمایش عمومی (اکتبر ۲۰۲۴) و فقط برای incidentهای Smart Alerts؛ در دسترس بودن روی self-hosted تأیید نشد؛ adaptive threshold در بتا و نیازمند ۱۴ روز داده؛ ماژول صریح «business transaction / business KPI» در فهرست قابلیت‌ها نیامده؛ تقویم کسب‌وکار یافت نشد؛ فهرست نام متریک‌های MQ از صفحهٔ بازاریابی است.

**ملاحظات استقرار/آفلاین:** صفحهٔ نصب air-gapped وجود دارد ولی رندر نشد؛ Custom Edition روی Kubernetes/OpenShift و ادعای air-gap آن ذکر نشده؛ کلید مجوز و bundle تصاویر همچنان از IBM می‌آید.

**مجوز/تحریم:** Blocked unless legally cleared (EV-S1، EV-S8، EV-I10).

| ID | ادعا | طبقه‌بندی | وابستگی | Confidence | تأیید برای این محیط | منبع |
|---|---|---|---|---|---|---|
| EV-I1 | Standard Edition (stanctl) «allows full mirroring in air-gapped environments»؛ صفحهٔ air-gapped رندر نشد. | Native | Standard Edition | High (وجود ادعا) | خیر | https://www.ibm.com/docs/en/SSE1JP5/src/pages/self_hosted/installation_options.html ؛ https://www.ibm.com/docs/en/SSE1JP5/src/pages/self_hosted/stanctl/installing_standard_edition_offline.html |
| EV-I2 | SaaS، Standard و Custom Edition backend و قابلیت یکسان دارند. | — | — | High | خیر | https://www.ibm.com/docs/en/SSE1JP5_current/src/pages/setup_and_manage/deployment_option.html |
| EV-I3 | sensor IBM MQ با host agent و PCF؛ ادعای متریک ۱ ثانیه‌ای شامل `queueDepth`، `oldestMessage`، `channelStatus` (از صفحهٔ بازاریابی). | Native | Instana host agent | High (sensor)/Medium (نام متریک) | خیر | https://www.ibm.com/docs/en/SSE1JP5/src/pages/ecosystem/ibmmq/index.html ؛ https://ibm.com/products/instana/supported-technologies/ibm-mq-monitoring |
| EV-I4 | tracing MQ با `mqtracingexit` و انتشار W3C traceparent؛ امکان span OTel. | Native (پیکربندی exit) | agent روی میزبان QM | High | خیر | https://www.ibm.com/docs/SSE1JP5_current/src/pages/ecosystem/ibmmq/mq_tracing.html |
| EV-I5 | sensor ACE/IIB: زمان جریان پیام، شمارش ورودی، خطای MQGET، commit/backout؛ IIB 10 و ACE 11/12/13. | Native | host agent | High | خیر | https://www.ibm.com/docs/en/SSE1JP5/src/pages/ecosystem/ace/index.html |
| EV-I6 | Smart Alerts: slow calls (p25–p99)، erroneous calls، throughput غیرعادی؛ آستانهٔ ایستا، فصلی روزانه/هفتگی، **adaptive (بتا، ۱۴ روز داده)**. | Native | — | High | خیر | https://www.ibm.com/docs/en/SSE1JP5_current/src/pages/application_monitoring/smart_alerts.html |
| EV-I7 | Probable Root Cause (پیش‌نمایش)؛ «وقتی داده یا اطمینان کافی نیست root cause نمایش داده نمی‌شود»؛ self-hosted تأیید نشد. | Native (preview) | — | Medium | خیر | https://community.ibm.com/community/user/discussion/probable-root-cause |
| EV-I8 | پایش موبایل (Android، iOS، RN، Flutter) با endpoint EUM خودمیزبان. | Native | EUM | High | خیر | https://www.ibm.com/docs/en/SSE1JP5/src/pages/mobile_app_monitoring/index.html |
| EV-I9 | قابلیت‌ها: application perspectives، نقشهٔ زیرساخت، SLO/error budget، synthetic، incident؛ ماژول صریح business KPI ندارد. | Native | — | High | خیر | https://www.ibm.com/docs/en/SSE1JP5/src/pages/about/key-capabilities.html |
| EV-I10 | IBM «US person» است؛ نصب air-gapped هم کلید مجوز و bundle از IBM لازم دارد. | — | — | High | — | بخش ۲ |

### ۴.۴ Cisco / Splunk AppDynamics (On-Premises و Virtual Appliance)

**نقاط قوت:** Controller درون‌سازمانی همچنان منتشر و پشتیبانی می‌شود (26.1 در فوریهٔ ۲۰۲۶)؛ business transaction خودکار با match rule قابل سفارشی‌سازی؛ تشخیص backend برای JMS، IBM MQ و RabbitMQ در agent جاوا؛ Transaction Analytics در APM Enterprise.

**محدودیت‌ها:** Anomaly Detection & RCA در Virtual Appliance **add-on** است؛ depth/age صف MQ از APM پوشش داده نمی‌شود و extension machine-agent لازم است (تأیید نشده)؛ backend MQ فقط وقتی دیده می‌شود که درخواست والد BT ثبت شده باشد (انجمن)؛ air-gap در صفحهٔ VA ذکر نشده. **هیچ ادعای EoL نباید مطرح شود** (EV-A3).

**مجوز/تحریم:** Blocked — Cisco صراحتاً محصول را در ایران عرضه نمی‌کند (EV-S5، EV-A7).

| ID | ادعا | طبقه‌بندی | وابستگی | Confidence | تأیید برای این محیط | منبع |
|---|---|---|---|---|---|---|
| EV-A1 | Controller on-prem: 26.1 (فوریه ۲۰۲۶)، 25.10 (EoS ۴ فوریه ۲۰۲۷)، 25.7 (EoS ۳۱ اکتبر ۲۰۲۶). | — | — | High | — | https://help.splunk.com/en/appdynamics-on-premises/release-notes-and-references/version-support-compatibility-and-maintenance/maintenance-support-for-software-versions/controller-ga-end-of-maintenance-and-support-for-on-premises-deployments |
| EV-A2 | Virtual Appliance مبتنی بر Kubernetes؛ Anomaly Detection & RCA، Secure Application و Automated Transaction Diagnostics add-on هستند؛ بیانیهٔ air-gap یافت نشد. | Add-on license | VA licence | High | خیر | https://help.splunk.com/en/appdynamics-on-premises/virtual-appliance-self-hosted/26.7.0/overview |
| EV-A3 | اعلان EoL برای on-prem یافت نشد؛ ادعای EoL نباید مطرح شود. | — | — | Medium | — | EV-A1، EV-A2 |
| EV-A4 | مجوز IBL: APM Enterprise شامل Transaction Analytics (تا ۴۰۰٬۰۰۰ رویداد BT در ۲۴ ساعت به ازای هر واحد)؛ RUM Peak؛ Log Analytics On-Prem Pro ۵ GB/روز. | Add-on license (tier) | Events Service | High | خیر | https://help.splunk.com/en/appdynamics-on-premises/splunk-appdynamics-licensing/25.7.0/observe-on-premises-license-usage/on-premises-license-entitlements-and-restrictions |
| EV-A5 | تشخیص backend برای JMS، IBM MQ، RabbitMQ در agent جاوا؛ depth/age صف پوشش داده نمی‌شود. | Native (tracing) / Integration-dependent (متریک MQ) | Java/.NET agent | Medium | خیر | https://help.splunk.com/en/appdynamics-saas/application-performance-monitoring/26.2.0/configure-instrumentation/backend-detection-rules/java-backend-detection ؛ https://community.splunk.com/t5/Splunk-AppDynamics/IBM-MQ-monitoring-with-AppDynamics/m-p/733194 |
| EV-A6 | BT خودکار و سفارشی‌سازی با match rule. | Native / Configurable | agents | High | خیر | https://help.splunk.com/en/appdynamics-on-premises/application-performance-monitoring/25.6.0/configure-instrumentation/transaction-detection-rules/custom-match-rules/.net-business-transaction-detection |
| EV-A7 | Cisco صراحتاً محصول را در ایران عرضه نمی‌کند. | — | — | High | — | EV-S5 |

---

## ۵. محصولات ویژهٔ بانک/پرداخت و میان‌افزار

> همهٔ ادعاهای این بخش **ادعای فروشنده / بازاریابی** هستند (Low–Medium). هیچ مرجع بانکی، هیچ پشتیبانی از گویش ISO 8583 شبکهٔ شتاب و هیچ آمادگی فروش به بانک ایرانی تأیید نشده است.

### ۵.۱ INETCO Insight
- **قوت (ادعای فروشنده):** ضبط غیرفعال بسته‌ها از پورت TAP/SPAN و بازسازی تراکنش بدون قرارگیری درون مسیر؛ decoderهای ISO 8583، ISO 20022، VISA 2، NDC+/Diebold/Triton (ATM)، IBM MQ، AMQP، HTTP/JSON/XML/SOAP، SQL؛ قواعد هشدار سطح فیلد؛ زمان‌بندی انتها‌به‌انتها. این تنها رویکرد یافته‌شده است که **بدون لاگ برنامه** دید تراکنشی می‌دهد.
- **محدودیت:** مدل استقرار ذکر نشده؛ گویش ISO 8583 شتاب تأیید نشده؛ ادعاهای «۹۰٪ عیب‌یابی سریع‌تر» و «۷۵٪ RCA سریع‌تر» **بازاریابی** است؛ ضبط شبکه با ترافیک رمزنگاری‌شده (TLS) چالش دارد — این مورد در منابع بررسی نشد (Unverified).
- **تحریم:** فروشندهٔ کانادایی؛ EV-S11 (Low). تمایل به فروش تأیید نشده.

### ۵.۲ IR Transact (Integrated Research)
- **قوت (ادعای فروشنده):** «powered by the Prognosis platform»؛ کارت، پرداخت با ارزش بالا و بلادرنگ؛ نسخهٔ 13.3 یکپارچگی ACI MTS TSI و «Iris AI Assistant» (بتا) با «correlation logic to explain why».
- **محدودیت:** سوئیچ‌های پشتیبانی‌شده از صفحهٔ رسمی تأیید نشد؛ دستیار AI **بازاریابی/بتا** است.
- **تحریم:** فروشندهٔ استرالیایی؛ EV-S11.

### ۵.۳ meshIQ (Nastel سابق)
- **قوت (بازاریابی):** مدیریت، پایش، ردیابی و tracing IBM MQ؛ agent یا agentless؛ RabbitMQ/Kafka/ActiveMQ.
- **تحریم:** به‌طور عمومی شرکت آمریکایی تلقی می‌شود (در این پژوهش تأیید نشد) → احتمالاً EV-S1. ACI و Broadcom نیز آمریکایی‌اند و پژوهش قابلیتی برایشان انجام نشد.

| ID | ادعا | طبقه‌بندی | Confidence | تأیید برای این محیط | منبع |
|---|---|---|---|---|---|
| EV-P1 | INETCO: ضبط TAP/SPAN، decoder ISO 8583/ISO 20022/MQ/AMQP/HTTP/SQL، قواعد سطح فیلد. | Native (ادعای فروشنده) | Medium | خیر؛ Unverified | https://www.inetco.com/products/inetco-insight/ |
| EV-P2 | IR Transact 13.3: یکپارچگی ACI MTS TSI؛ Iris AI Assistant (بتا). | Native (ادعای فروشنده) | Medium | خیر؛ Unverified | https://www.ir.com/blog/payments/whats-new-in-transact-13.3 |
| EV-P3 | meshIQ: پایش/tracing IBM MQ، agent یا agentless. | Native (بازاریابی) | Low | خیر | https://www.meshiq.com/integrations/ibm-mq/ ؛ https://www.meshiq.com/transaction-tracking/ |

---

## ۶. ابزارهای موجود و مؤلفه‌های متن‌باز

### ۶.۱ Zabbix (موجود در بانک — [فرض A-06]؛ نسخه نامعلوم [باز Q-26])

**نقاط قوت:** درخت سرویس کسب‌وکار (6.0+) با نگاشت problem از طریق tag، قواعد محاسبهٔ وضعیت، propagation و **وزن**؛ اشیای SLA با SLO، **schedule** ساعت کاری و **excluded downtime**، قابل خواندن با `sla.getsli`؛ نمای root cause problems؛ همبستگی رویداد مبتنی بر قاعده؛ cause/symptom در 7.0+؛ API JSON-RPC کامل برای connector فقط‌خواندنی؛ templateهای رسمی RabbitMQ، HAProxy، F5 BIG-IP (SNMP) و MSSQL. این قابلیت‌ها **بخش قابل‌توجهی از موتور سلامت/SLA سطح زیرساخت** را که پیش‌نویس اولیهٔ Phase 0 قصد ساختش را داشت، از قبل فراهم می‌کنند (نامزد بازاستفاده، تصمیم با PoC-1 → [ADR-0023](../architecture/architecture-decision-records/ADR-0023-reuse-zabbix-services-and-sla.md)).

**محدودیت‌ها:** **template رسمی IBM MQ ندارد** (فقط پروژهٔ جامعه از ۲۰۱۸)؛ همبستگی فقط قاعده‌محور و بدون امتیاز اطمینان؛ root cause یک درخت قطعی است نه رتبه‌بندی احتمالاتی؛ تقویم جلالی و تعطیلات ایران را ندارد (باید با API به‌صورت schedule/excluded downtime/maintenance همگام شود)؛ قابلیت‌های 8.0 (OTLP، CEP با گروه‌بندی root-cause/symptom، پیش‌بینی روند، MCP server) **نقشهٔ راه/بازاریابی تا GA** هستند. وضعیت انتشار 8.0 در تاریخ این سند باید دوباره بررسی شود.

**مجوز:** 7.0+ تحت AGPLv3؛ پیش از آن GPLv2+. اجرای بدون تغییر درون‌سازمانی؛ اگر پلتفرم Zabbix را تغییر دهد یا توزیع کند، تعهدات AGPL مطرح است (ریسک R-18؛ [باز Q-21]).

| ID | ادعا | طبقه‌بندی | مجوز/نسخه | Confidence | تأیید برای این محیط | منبع |
|---|---|---|---|---|---|---|
| EV-O16 | Zabbix 7.0+ تحت AGPLv3. | — | AGPLv3 | High | خیر (نسخهٔ بانک نامعلوم) | https://blog.zabbix.com/striking-the-right-balance-zabbix-7-0-to-be-released-under-agplv3-license/ |
| EV-O17 | **template رسمی IBM MQ وجود ندارد**؛ فقط پروژهٔ جامعه (2018، «not verified by Zabbix»). | Not supported (رسمی) | — | High | خیر | https://www.zabbix.com/integrations/ibm_mq |
| EV-O18 | templateهای رسمی RabbitMQ (agent و HTTP)؛ متریک‌های صف: total/ready/unacked/consumers و نرخ publish/deliver/ack. | Native | AGPLv3 | High | خیر (نصب بودن نامعلوم) | https://www.zabbix.com/integrations/rabbitmq |
| EV-O19 | templateهای رسمی HAProxy (HTTP و agent): discovery frontend/backend/server، نرخ 1xx–5xx، queue time، response time. | Native | AGPLv3 | High | خیر | https://www.zabbix.com/integrations/haproxy |
| EV-O20 | template رسمی F5 BIG-IP by SNMP: virtual server، pool، node، failover/sync، انقضای گواهی. | Native | AGPLv3 | High | خیر | https://www.zabbix.com/integrations/f5 |
| EV-O21 | templateهای رسمی MSSQL (agent 2 plugin و ODBC). | Native | AGPLv3 | High | خیر | https://www.zabbix.com/integrations/mssql |
| EV-O22 | درخت سرویس (6.0+): نگاشت problem با tag؛ قواعد وضعیت `status_to_ok`/`most_crit_if_all_children`/`most_crit_of_child_serv`؛ propagation `as_is`/`increase`/`decrease`/`ignore`/`fixed`؛ قواعد وزن/N/N%. | Configurable | AGPLv3 | High (پارامترها) | خیر | https://docs.ansible.com/ansible/8/collections/community/zabbix/zabbix_service_module.html ؛ https://www.zabbix.com/documentation/current/en/manual/it_services/service_tree |
| EV-O23 | اشیای SLA: SLO، دورهٔ گزارش، schedule هفتگی، excluded downtime، service tags؛ `sla.getsli`. | Configurable | AGPLv3 | Medium (بدنهٔ صفحه رندر نشد) | خیر | https://www.zabbix.com/documentation/current/en/manual/it_services/sla |
| EV-O24 | نمای Services مشکلات root cause مؤثر بر وضعیت سرویس را نشان می‌دهد؛ درخت قطعی، نه RCA احتمالاتی. | Native | AGPLv3 | Medium | خیر | مستند سرویس‌های Zabbix 6.0+ |
| EV-O25 | همبستگی رویداد: trigger-based و global correlation rules؛ فقط قاعده‌محور، بدون امتیاز اطمینان. | Configurable | AGPLv3 | Medium | خیر | https://www.zabbix.com/documentation/current/en/manual/config/event_correlation |
| EV-O26 | cause/symptom در 7.0+ (UI و `event.update`)؛ پیوند دستی یا قاعده‌محور. | Configurable | AGPLv3 | Medium | خیر | منوی مستندات «Cause and symptom problems» |
| EV-O27 | API: `problem.get`، `event.get`، `history.get`، `trend.get`، `service.get`، `sla.getsli`، `maintenance.*`. | Native | AGPLv3 | Medium–High | خیر | https://www.zabbix.com/documentation/current/en/manual/api |
| EV-O28 | Zabbix 8.0 (devel؛ LTS برنامه‌ریزی‌شده ۲۰۲۶): OTLP، traces/logs، CEP با root-cause/symptom، پیش‌بینی روند با AI، MCP server — **نقشهٔ راه تا GA**. | Roadmap | AGPLv3 (انتظار) | Medium (برنامه) | خیر | https://www.zabbix.com/documentation/devel/en/manual/whatsnew ؛ https://assets.zabbix.com/files/events/2026/conference_poland_2026/Alexei_Vladishev_Zabbix8_0.pdf |
| EV-O29 | plugin Zabbix برای Grafana: متریک‌ها، توابع پردازشی، پنل Triggers/problems. | Integration-dependent | LICENSE مخزن | High | خیر | https://grafana.com/grafana/plugins/alexanderzobnin-zabbix-app/ |

### ۶.۲ SolarWinds (موجود؛ وضعیت مجوز نامعلوم [باز Q-25])

**قوت:** SWIS با SWQL (پرس‌وجوی شبه‌SQL فقط‌خواندنی روی `Orion.Nodes`، `Orion.AlertActive` و …)؛ محدودسازی نتایج با account limitation؛ endpoint REST/JSON و کلاینت‌های Python/Go/Java/PowerShell. connector فقط‌خواندنی **کوچک** و custom است.
**محدودیت/ریسک:** شرکت آمریکایی؛ صفحهٔ کنترل صادرات SolarWinds بازیابی نشد؛ تداوم به‌روزرسانی نامعلوم (R-16). verbهایی مانند Acknowledge وجود دارد → حساب connector باید فقط‌خواندنی و محدود باشد (NFR-SEC-003).

| ID | ادعا | طبقه‌بندی | Confidence | تأیید برای این محیط | منبع |
|---|---|---|---|---|---|
| EV-O30 | SWIS/SWQL فقط‌خواندنی روی درخت entity؛ CRUD با URI و verbها؛ محدودسازی با account limitation. | Native | High | خیر | https://solarwinds.github.io/OrionSDK/docs/about-swis/ |
| EV-O31 | endpoint REST/JSON روی پورت 17774 (17778 قدیمی)؛ کلاینت‌ها. | Native | Medium (پورت از دانش قبلی) | خیر | https://github.com/solarwinds/OrionSDK |

### ۶.۳ Splunk Enterprise و Elasticsearch موجود (به‌عنوان منبع لاگ و موتور محاسبهٔ KPI)

**قوت:** لاگ‌های برنامه‌های کلیدی احتمالاً از قبل در این دو هستند **[فرض A-05]**؛ ذخیره و retention لاگ خام را انجام می‌دهند (ADR-0009)؛ امکان اجرای جست‌وجوی زمان‌بندی‌شده/aggregation **درون همین موتورها** و کشیدن فقط نتایج دقیقه‌ای (توصیهٔ B4).
**محدودیت:** ML و APM service map و connectorهای webhook/ITSM در Elastic self-managed در tier Platinum/Enterprise هستند (EV-O42)؛ تقویم جلالی در Kibana/OpenSearch Dashboards بومی نیست (EV-O46)؛ مجوز Elasticsearch بانک (open-source یا اشتراک) بررسی نشد؛ بار اضافی جست‌وجو و سهمیه نامعلوم **[باز Q-22، Q-28]**؛ ریسک توقف search در Splunk پس از انقضای مجوز term (EV-S10).

| ID | ادعا | طبقه‌بندی | مجوز | Confidence | تأیید برای این محیط | منبع |
|---|---|---|---|---|---|---|
| EV-O41 | کد Elasticsearch/Kibana تحت SSPL/ELv2/AGPLv3 (AGPL از ۲۰۲۴ برای بخش‌های رایگان)؛ توزیع پیش‌فرض ELv2. | — | tri-license | High | خیر (مجوز بانک نامعلوم) | https://www.elastic.co/pricing/faq/licensing |
| EV-O42 | ML anomaly detection، APM service map و connectorهای webhook/ITSM در self-managed: Platinum/Enterprise؛ Platinum فقط برای مشتریان موجود. | Add-on license | paid | High | خیر | https://www.elastic.co/pricing/self-managed ؛ https://www.elastic.co/subscriptions |
| EV-O46 | OpenSearch Dashboards تقویم جلالی بومی ندارد؛ برای Kibana فقط افزونهٔ مرورگر شخص ثالث. | Custom development | — | Medium | خیر | https://forum.opensearch.org/t/changing-calendar-type-from-gregorian-to-jalali/14727 ؛ https://chromewebstore.google.com/detail/hpoegiodadagdnelficfklmidgkcfemp |
| EV-O47 | Splunk ITSI (اگر مجوز Splunk قابل گسترش باشد) نزدیک‌ترین معادل «خرید» برای لایهٔ KPI/سرویس/ناهنجاری است؛ premium app جداگانه. | Add-on license | Splunk premium | High (قابلیت)/Medium (مجوز) | خیر؛ Blocked | https://docs.splunk.com/Documentation/ITSI/4.18.0/SI/ML |

### ۶.۴ OpenTelemetry Collector (contrib)

**قوت:** receiverهای RabbitMQ، HAProxy، NGINX، SQL Server، SNMP؛ redaction processor با HMAC؛ transform processor (OTTL) برای استخراج ویژگی‌های کسب‌وکار؛ صف پایدار روی دیسک (`file_storage`) که بافرینگ را **بدون Kafka** پوشش می‌دهد؛ ساخت توزیع سفارشی با OCB.
**محدودیت:** **receiver برای IBM MQ ندارد**؛ NGINX receiver فقط `stub_status` (بدون status code و latency به ازای upstream)؛ بسیاری از مؤلفه‌ها beta/alpha (SNMP و redaction برای logs alpha) → نیاز به pin نسخه و آزمون رگرسیون؛ OCB به Go و دریافت ماژول نیاز دارد و راهنمای air-gap ندارد.
**جایگاه در E:** **اختیاری** — فقط در zoneهایی که `connector-runtime` نمی‌تواند endpoint Prometheus/OTLP را بخواند یا برای instrumentation آینده (Phase 7).

| ID | ادعا | طبقه‌بندی | Confidence | تأیید برای این محیط | منبع |
|---|---|---|---|---|---|
| EV-O1 | RabbitMQ receiver: beta؛ Management API. | Native | High | خیر | https://github.com/open-telemetry/opentelemetry-collector-contrib/blob/main/receiver/rabbitmqreceiver/README.md |
| EV-O2 | HAProxy receiver: beta؛ stats socket یا HTTP. | Native | High | خیر | https://github.com/open-telemetry/opentelemetry-collector-contrib/blob/main/receiver/haproxyreceiver/README.md |
| EV-O3 | NGINX receiver: beta؛ فقط stub_status؛ برای latency باید access log تجزیه شود. | Native (محدود) | High | خیر | https://github.com/open-telemetry/opentelemetry-collector-contrib/blob/main/receiver/nginxreceiver/README.md |
| EV-O4 | SQL Server receiver: metrics beta؛ نیازمند VIEW SERVER STATE برای اتصال مستقیم. | Native | High | خیر | https://github.com/open-telemetry/opentelemetry-collector-contrib/blob/main/receiver/sqlserverreceiver/README.md |
| EV-O5 | SNMP receiver: alpha؛ v1/v2c/v3. | Native (alpha) | High | خیر | https://github.com/open-telemetry/opentelemetry-collector-contrib/blob/main/receiver/snmpreceiver/README.md |
| EV-O6 | contrib receiver برای IBM MQ ندارد؛ همهٔ یکپارچگی‌ها از `mq_prometheus` استفاده می‌کنند. | Integration-dependent | High | خیر | https://www.elastic.co/docs/reference/integrations/ibmmq_otel ؛ https://docs.newrelic.com/docs/opentelemetry/integrations/ibm-mq/overview.md |
| EV-O10 | redaction processor: logs/metrics alpha، traces beta؛ allow-list و regex؛ hash HMAC. | Native | High | خیر | https://github.com/open-telemetry/opentelemetry-collector-contrib/blob/main/processor/redactionprocessor/README.md |
| EV-O11 | transform processor (OTTL): beta؛ استخراج ویژگی کسب‌وکار از لاگ. | Configurable | High | خیر | https://github.com/open-telemetry/opentelemetry-collector-contrib/blob/main/processor/transformprocessor/README.md |
| EV-O12 | صف پایدار با `file_storage` (beta)؛ ارسال مجدد پس از crash. | Configurable | High | خیر | https://github.com/open-telemetry/opentelemetry-collector/blob/main/exporter/exporterhelper/README.md ؛ https://github.com/open-telemetry/opentelemetry-collector-contrib/blob/main/extension/storage/filestorage/README.md |
| EV-O13 | OCB به Go و دریافت ماژول نیاز دارد؛ بدون راهنمای air-gap. | Configurable | High | خیر | https://opentelemetry.io/docs/collector/custom-collector/ |
| EV-O14 | بسیاری از receiverها beta/alpha؛ pin نسخه و آزمون رگرسیون لازم. | — | High | — | READMEهای بالا |
| EV-O15 | receiverهای jmx، prometheus، syslog، filelog، kafka موجود؛ jmx به JAR خارجی جاوا نیاز دارد؛ README jmx خوانده نشد. | Native | Medium | خیر | مخزن contrib |

### ۶.۵ IBM `mq-metric-samples` / `mq_prometheus`

**قوت:** Apache-2.0؛ depth، max depth، **oldest message age** و queue times؛ `amqsevtg` برای قالب‌بندی event messageهای queue manager.
**محدودیت:** نرخ و زمان‌بندی MQPUT/MQGET به **فعال‌سازی MQI statistics** روی queue manager نیاز دارد = تغییر پیکربندی → در تعارض با NFR-IMP-002 مگر با تأیید بانک **[باز Q-27]**؛ `useObjectStatus` (DIS QSTATUS) و RESET QSTATS اختیاری‌اند و RESET QSTATS فقط برای یک monitor به ازای هر QMgr قابل‌اتکاست (طبق تصمیم‌های قبلی، RESET QSTATS ممنوع است). یکپارچگی OTel «بومی» MQ 9.4 **فقط tracing** است و entitlement آن به برنامهٔ Instana گره خورده است.

| ID | ادعا | طبقه‌بندی | Confidence | تأیید برای این محیط | منبع |
|---|---|---|---|---|---|
| EV-O7 | `mq-metric-samples` Apache-2.0؛ agentهای Prometheus و `amqsevtg`؛ DIS QSTATUS و RESET QSTATS اختیاری. | Integration-dependent | High | خیر | https://github.com/ibm-messaging/mq-metric-samples |
| EV-O8 | depth، max depth، oldest message age، queue times؛ نرخ‌ها نیازمند MQI statistics. | Integration-dependent | Medium–High (مستند New Relic) | خیر | https://docs.newrelic.com/docs/opentelemetry/integrations/ibm-mq/overview.md |
| EV-O9 | OTel بومی MQ 9.4 فقط tracing (API exit)؛ entitlement در برنامهٔ Instana؛ متریک OTel بومی ندارد. | Integration-dependent | High | خیر | https://www.ibm.com/docs/en/SSFKSJ_latest/monitor/opentelemetry_tracing_multi.html |

### ۶.۶ موتورهای ذخیره‌سازی

| مؤلفه | قوت | محدودیت | جایگاه در E |
|---|---|---|---|
| PostgreSQL | از قبل برای metadata انتخاب شده (ADR-0007)؛ یک store کمتر برای عملیات | برای رویدادهای تراکنشی پرحجم با ابعاد پرکاردینال مناسب نیست (EV-O52، قضاوت) | **ذخیرهٔ واحد** metadata + سری KPI مشتق در Low |
| TimescaleDB | compression، continuous aggregate، retention | این قابلیت‌ها در **TSL** هستند نه Apache-2 (EV-O51) | گزینه؛ نیازمند بازبینی مجوز Q-21 |
| ClickHouse | TTL، tiering، rollup با `GROUP BY … SET`، codec ستونی (EV-O50)؛ مناسب‌ترین برای رویداد تراکنشی پرکاردینال و percentile (EV-O52) | یک سیستم توزیع‌شدهٔ دیگر برای عملیات؛ Keeper برای replication | **فقط با trigger** (ADR-0008) |
| VictoriaMetrics | Apache-2.0 community | downsampling، چند retention، vmanomaly، mTLS فقط Enterprise (EV-O33، EV-O34) | استفاده نمی‌شود |
| Prometheus | Apache-2.0 | TSDB تک‌گره، retention کوتاه (EV-O40) | استفاده نمی‌شود |
| OpenSearch | AD (RCF) با grade و confidence و forecasting رایگان (EV-O43، EV-O44) | **یک خوشهٔ اضافه** در حالی که بانک Elasticsearch دارد | استفاده نمی‌شود |

| ID | ادعا | Confidence | تأیید برای این محیط | منبع |
|---|---|---|---|---|
| EV-O32 | VictoriaMetrics community: Apache-2.0. | High | — | https://github.com/VictoriaMetrics/VictoriaMetrics/blob/master/LICENSE |
| EV-O33 | downsampling، چند retention، vmanomaly، mTLS و … فقط Enterprise. | High | — | https://docs.victoriametrics.com/victoriametrics/enterprise/ |
| EV-O34 | vmanomaly از v1.5.0 کلید Enterprise لازم دارد. | High | — | https://docs.victoriametrics.com/anomaly-detection/ |
| EV-O40 | Prometheus: Apache-2.0، تک‌گره، retention روز تا هفته. | High | — | https://github.com/prometheus/prometheus |
| EV-O43 | OpenSearch AD: RCF، anomaly grade + confidence، high-cardinality، Apache-2.0. | High | خیر | https://github.com/opensearch-project/documentation-website/blob/main/_observing-your-data/ad/index.md |
| EV-O44 | OpenSearch Forecasting (RCF) از حدود 3.1. | Medium (نسخه)/High (قابلیت) | خیر | https://docs.opensearch.org/latest/observing-your-data/forecast/index/ |
| EV-O45 | Trace Analytics service map در OpenSearch Dashboards به Data Prepper نیاز دارد. | High | خیر | https://github.com/opensearch-project/documentation-website/blob/main/_observing-your-data/trace/ta-dashboards.md |
| EV-O50 | ClickHouse MergeTree TTL: DELETE، TO DISK/VOLUME، RECOMPRESS، GROUP BY … SET. | High | خیر | https://clickhouse.com/docs/engines/table-engines/mergetree-family/mergetree |
| EV-O51 | TimescaleDB: compression، continuous aggregate و retention در TSL، نه Apache-2. | High | — | https://www.tigerdata.com/docs/get-started/choose-your-path/timescaledb-editions |
| EV-O52 | قضاوت: ClickHouse برای رویداد تراکنشی پرکاردینال و percentile بهترین؛ PostgreSQL(+TSL) در مقیاس کم (≈ <10–50M ردیف/روز) کافی. | Medium (قضاوت) | خیر (PoC-5) | ارزیابی |

### ۶.۷ Apache Kafka

| ID | ادعا | Confidence | منبع |
|---|---|---|---|
| EV-O48 | Kafka 4.0 (مارس ۲۰۲۵) فقط KRaft؛ broker با Java 17. | High | https://kafka.apache.org/blog/2025/03/18/apache-kafka-4.0.0-release-announcement/ |
| EV-O49 | قضاوت: Kafka وقتی موجه است که چند مصرف‌کنندهٔ مستقل، replay، بافر فراتر از `file_storage` یا fan-out لاگ پرحجم لازم باشد؛ در حجم کم/متوسط با یک مصرف‌کننده صف پایدار OTel کافی است. | Medium (قضاوت) | ارزیابی |

**نتیجه:** با حذف ingestion گستردهٔ telemetry خام در گزینهٔ E، هیچ‌یک از شرایط Kafka برقرار نیست → **فعلاً رد** ([ADR-0010](../architecture/architecture-decision-records/ADR-0010-buffering-without-kafka-initially.md)).

### ۶.۸ Grafana OSS

**قوت:** node graph برای توپولوژی (۲۰۰ گره پیش‌فرض؛ کندی بالای ۵۰۰)؛ Alerting در OSS با contact point، silence و mute timing؛ نصب plugin آفلاین با ZIP؛ plugin Zabbix.
**محدودیت:** **شواهدی از UI فارسی، چیدمان RTL یا انتخابگر تاریخ جلالی یافت نشد** (Low–Medium، نبودِ شواهد)؛ mute timing تقویم جلالی/تعطیلات را بدون provisioning سفارشی نمی‌شناسد؛ AGPLv3 — اجرای بدون تغییر درون‌سازمانی تعهد افشا ندارد، تغییر و ارائه به کاربران دارد (R-18، Q-21).
**جایگاه در E:** **اختیاری برای نماهای مهندسی**؛ UI اپراتور فارسی فقط اگر Q-23 آن را الزامی کند custom می‌ماند ([ADR-0024](../architecture/architecture-decision-records/ADR-0024-visualization-split-custom-operator-ui-optional-grafana.md)).

| ID | ادعا | طبقه‌بندی | Confidence | تأیید برای این محیط | منبع |
|---|---|---|---|---|---|
| EV-O35 | Grafana، Loki و Tempo در ۲۰۲۱-۰۴-۲۰ از Apache 2.0 به AGPLv3. | — | High | — | https://grafana.com/licensing/ |
| EV-O36 | node graph: frame یال و گره، ۲۰۰ گره پیش‌فرض، کندی بالای ۵۰۰، data link. | Configurable | High | خیر | https://grafana.com/docs/grafana/latest/panels-visualizations/visualizations/node-graph/ |
| EV-O37 | Alerting OSS: contact point، notification policy، silence، mute timing؛ نه تقویم جلالی. | Configurable | Medium–High | خیر | https://grafana.com/docs/grafana/latest/alerting/ |
| EV-O38 | نصب plugin آفلاین (ZIP یا grafana-cli)؛ غیرفعال‌سازی `check_for_updates` و `reporting_enabled`. | Configurable | High | خیر | https://grafana.com/docs/grafana/latest/administration/plugin-management/plugin-install/ ؛ https://grafana.com/docs/grafana/latest/setup-grafana/configure-grafana/ |
| EV-O39 | زبان UI با `default_language`؛ **شواهدی از fa/RTL/جلالی نیست**. | Custom development (در صورت الزام) | Low–Medium | خیر | https://grafana.com/docs/grafana/latest/setup-grafana/configure-grafana/ ؛ https://community.grafana.com/t/grafana-oss-can-we-add-new-languages-manually-or-must-all-new-languages-go-through-the-official-crowdin-project/162028 |

### ۶.۹ AIOps/APM متن‌باز

| ID | محصول | ادعا | مجوز | Confidence | ارزیابی | منبع |
|---|---|---|---|---|---|---|
| EV-O53 | Keep | dedup، قواعد همبستگی CEL، همبستگی مبتنی بر توپولوژی (پیش‌فرض خاموش، پنجرهٔ ۱۵ دقیقه)، دریافت از Zabbix؛ **Elastic موافقت خرید (۲۰۲۵-۰۵-۰۷)** — ریسک نقشهٔ راه. | MIT + `ee/` | High | گروه‌بندی بدون امتیاز اطمینان؛ مرجع طراحی، نه وابستگی | https://github.com/keephq/keep/blob/main/LICENSE ؛ https://docs.keephq.dev/overview/correlation-topology ؛ https://docs.keephq.dev/overview/correlation-rules ؛ https://www.elastic.co/blog/elastic-and-keep-join-forces |
| EV-O54 | Coroot | eBPF، service map، SLO، ClickHouse+Prometheus؛ **AI RCA، SSO، RBAC و نصب air-gapped در tier پولی**. | Apache-2.0 CE + paid | High | مبتنی بر agent eBPF؛ قابلیت‌های کلیدی پولی | https://github.com/coroot/coroot ؛ https://coroot.com/pricing/ ؛ https://docs.coroot.com/ |
| EV-O55 | SigNoz | APM بومی OTel روی ClickHouse. | MIT + `ee/` | High | نیازمند instrumentation؛ Phase 7 | https://github.com/SigNoz/signoz/blob/main/LICENSE |
| EV-O56 | OpenObserve | — | AGPL-3.0 | High | ذخیرهٔ تکراری لاگ؛ رد | https://github.com/openobserve/openobserve/blob/main/LICENSE |
| EV-O57 | SkyWalking | APM با توپولوژی سرویس/endpoint، agentها، دریافت OTel؛ storage: BanyanDB/ES/OpenSearch/MySQL/PostgreSQL. | Apache-2.0 | High | نامزد Phase 7 در صورت نیاز tracing | https://skywalking.apache.org/docs/main/latest/en/concepts-and-designs/overview/ |
| EV-O58 | Netdata | UI تحت **NCUL1** (غیر OSS). | GPLv3+ + NCUL1 | Medium–High | **رد** | https://github.com/netdata/netdata/blob/master/LICENSE ؛ https://isitreallyfoss.com/projects/netdata |
| EV-O59 | Flink CEP | تشخیص الگو روی stream؛ نیازمند Kafka + خوشهٔ Flink. | Apache-2.0 | Medium (صفحه خوانده نشد) | زیاده‌روی برای این مقیاس | https://nightlies.apache.org/flink/flink-docs-stable/docs/libs/cep/ |

**یافتهٔ مشترک (Medium–High):** هیچ‌یک از این محصولات تراکنش کسب‌وکار بانکی، تقویم کاری یا incident با «علت محتمل + شواهد + اطمینان عددی + شواهد نقض» را مدل نمی‌کند.

---

## ۷. یافته‌ها، برآوردها و پرسش‌های حل‌نشده

### ۷.۱ یافته‌های مستند (از منبع رسمی خوانده‌شده؛ برای محیط بانک تأیید نشده)
1. Business Events در Dynatrace به Grail وابسته است و Grail روی Managed نیست (EV-D5، EV-D6) — High.
2. ITSI مجوز جداگانه دارد (EV-T1) و maintenance window آن موتور تقویم کسب‌وکار نیست (EV-T7) — High.
3. Cisco صراحتاً محصولاتش را در ایران عرضه نمی‌کند (EV-S5) — High.
4. Zabbix 7.0+ تحت AGPLv3 است (EV-O16)؛ template رسمی IBM MQ ندارد (EV-O17)؛ templateهای رسمی RabbitMQ/HAProxy/F5/MSSQL دارد (EV-O18..21) — High.
5. پارامترهای درخت سرویس Zabbix (وزن، propagation) در API مستند شده‌اند (EV-O22) — High؛ SLA با schedule و excluded downtime — Medium (EV-O23).
6. OTel contrib receiver برای IBM MQ ندارد؛ `mq_prometheus` (Apache-2.0) مسیر استاندارد است (EV-O6، EV-O7) — High.
7. Elastic ML در tier پولی است (EV-O42) — High.
8. قابلیت‌های کلیدی TimescaleDB و VictoriaMetrics Enterprise خارج از مجوز Apache-2.0 هستند (EV-O51، EV-O33) — High.

### ۷.۲ برآوردها و قضاوت‌ها (نه یافته)
1. **[فرضیه]** درخت سرویس و SLA در Zabbix برای سلامت سطح زیرساخت سرویس‌ها و SLA با schedule کافی است → PoC-1.
2. **[فرضیه]** KPIهای تراکنش (موفقیت فنی/کسب‌وکار، percentile latency) را می‌توان با saved search/aggregation درون Splunk/ES محاسبه کرد، به شرط وجود کد نتیجه و latency در لاگ → PoC-2 (ریسک R-01).
3. **[فرضیه]** PostgreSQL برای سری KPI مشتق در سناریوی Low کافی است و ClickHouse در Medium با retention ۱۳ ماهه احتمالاً لازم می‌شود → PoC-5 و [capacity-planning.md](../architecture/capacity-planning.md).
4. قضاوت (Medium): Kafka در مقیاس فعلی لازم نیست (EV-O49).
5. قضاوت (Low–Medium): Grafana و Kibana/OSD نیاز UI فارسی RTL جلالی را پوشش نمی‌دهند (نبودِ شواهد، EV-O39، EV-O46).

### ۷.۳ پرسش‌های حل‌نشده (ارجاع به [open-questions-and-assumptions.md](../architecture/open-questions-and-assumptions.md))
Q-08 (نسخه‌ها و API)، Q-21 (سیاست AGPL)، Q-22 و Q-28 (سهمیهٔ جست‌وجو و میزبانی saved search)، Q-23 (الزام UI فارسی)، Q-24 (نظر حقوقی تحریم)، Q-25 (مجوز Splunk/SolarWinds/Elasticsearch)، Q-26 (نسخه و استفادهٔ فعلی Zabbix services/SLA)، Q-27 (فعال‌سازی MQI statistics).

---

## ۸. شکاف‌های پژوهش و پرسش‌های آینده

### ۸.۱ صفحاتی که خوانده نشدند یا ناقص خوانده شدند
| مورد | اثر | اقدام |
|---|---|---|
| بند صادرات MSA شرکت Dynatrace (robots) | متن قراردادی تحریم تأیید نشد | Q-24 (حقوقی) |
| صفحهٔ نصب air-gapped در Instana (رندر نشد) | جزئیات air-gap تأیید نشد | فقط در صورت رفع Q-24 |
| بدنهٔ صفحات مستندات Zabbix (service tree، SLA، event correlation، API) | ادعاهای Zabbix در Medium | PoC-1 روی نسخهٔ بانک |
| مقررات SEMA کانادا (SOR-2010-165) | وضعیت INETCO نامعلوم | Q-24 |
| صفحهٔ کنترل صادرات SolarWinds | وضعیت حقوقی ابزار موجود نامعلوم | Q-24، Q-25 |
| مستند رسمی رفتار انقضای مجوز Splunk | EV-S10 در Low–Medium | Q-25 |
| پیش‌نیازهای Predictive Analytics در ITSI | — | فقط در صورت رفع Q-24 |
| فهرست متریک‌های MQ در مستند Instana | از صفحهٔ بازاریابی | — |
| بیانیهٔ air-gap در AppDynamics VA | تأیید نشد | — |
| README receiver jmx در OTel | جزئیات JMX تأیید نشد | Phase 4 (FR-RUN-001) |
| صفحهٔ Flink CEP | Medium | — |
| پورت REST در OrionSDK | از دانش قبلی | Q-08 |
| وضعیت انتشار Zabbix 8.0 در تاریخ این سند | قابلیت‌های جدید ممکن است دامنهٔ custom را کم کند | بررسی مجدد پیش از Phase 1 |
| template NGINX در Zabbix، پایش WAF/API gateway/ESB در ابزارهای موجود | Unverified | Q-08 و کشف داده (EP-22) |

### ۸.۲ پرسش‌های دقیق برای فروشندگان (فقط اگر Q-24 مسیر را باز کند)
- **VQ-01 (همهٔ فروشندگان تجاری):** آیا به‌صورت کتبی تأیید می‌کنید که می‌توانید به یک بانک ایرانی مشمول E.O. 13902 مجوز، پشتیبانی و به‌روزرسانی ارائه دهید؟ بر اساس کدام مجوز یا مبنای حقوقی؟
- **VQ-02 (Dynatrace):** در Managed آفلاین کدام قابلیت‌ها (Extensions Hub، Application Security، Business Events) در دسترس نیستند؟ آیا RCA امتیاز عددی اطمینان دارد؟ آیا extension IBM MQ روی Managed پشتیبانی می‌شود؟
- **VQ-03 (Splunk ITSI):** آیا Predictive Analytics به MLTK وابسته است؟ آیا ITSI روی نسخهٔ Splunk Enterprise نصب‌شده در بانک قابل نصب است؟ روش مدل‌سازی تقویم کاری با تعطیلات متغیر سالانه؟
- **VQ-04 (Instana):** آیا Probable Root Cause روی Standard Edition خودمیزبان فعال است؟ فهرست کامل متریک‌های sensor IBM MQ و حداقل مجوزهای PCF؟
- **VQ-05 (AppDynamics):** بیانیهٔ air-gap برای Virtual Appliance؟ پایش depth/oldest age صف MQ با کدام extension؟
- **VQ-06 (INETCO/IR):** آیا گویش ISO 8583 شبکهٔ شتاب و پیام‌های ساتنا/پایا را decode می‌کنید؟ مدل استقرار (appliance/نرم‌افزار)، رفتار با ترافیک TLS، و نیاز به اینترنت برای مجوز/به‌روزرسانی؟ آیا مرجع **قابل‌راستی‌آزمایی** از بانک‌ها دارید؟ تمایل به قرارداد با بانک ایرانی؟
- **VQ-07 (Zabbix SIA):** وضعیت GA نسخهٔ 8.0 و قابلیت‌های CEP/OTLP؛ آیا API سرویس/SLA در 8.0 سازگار باقی می‌ماند؟ (پشتیبانی تجاری Zabbix نیز مشمول Q-24 است.)

### ۸.۳ پرسش‌های دقیق برای بانک
- **BQ-01 [Q-25]:** Splunk، SolarWinds و Elasticsearch چگونه مجوز گرفته و به‌روزرسانی می‌شوند (perpetual/term، تاریخ انقضا، کانال تأمین)؟ اگر مجوز Splunk منقضی شود، search متوقف می‌شود؟
- **BQ-02 [Q-26]:** نسخهٔ Zabbix؟ آیا services/SLA اکنون استفاده می‌شوند؟ مالک پیکربندی Zabbix کیست و آیا ایجاد tag، سرویس و maintenance توسط پلتفرم (یا توسط تیم Zabbix با درخواست پلتفرم) مجاز است؟
- **BQ-03 [Q-27]:** آیا فعال‌سازی MQI statistics یا queue monitoring (MONQ) روی queue managerهای تولید پذیرفتنی است؟
- **BQ-04 [Q-28، Q-22]:** آیا ادمین‌های Splunk/ES saved search/aggregation زمان‌بندی‌شده برای پلتفرم را میزبانی می‌کنند؟ سهمیهٔ جست‌وجو چقدر است؟
- **BQ-05 [Q-23]:** آیا UI اپراتور فارسی RTL با تقویم جلالی الزامی است؟
- **BQ-06 [Q-07]:** برای سرویس VS-1، لاگ‌ها کجا هستند و آیا کد پاسخ/نتیجه، latency و کلید همبستگی دارند؟
- **BQ-07 [Q-08]:** نسخه‌ها و APIهای F5/HAProxy/NGINX/WAF/API gateway/ESB و اینکه کدام‌ها از قبل در Zabbix/SolarWinds پایش می‌شوند.
- **BQ-08 [Q-24، Q-21]:** سیاست بانک دربارهٔ نرم‌افزار متن‌باز منتشرشده توسط شرکت‌های آمریکایی و مجوزهای AGPL.

---

## نکته برای بازبینی
1. وضعیت حقوقی دریافت و استفاده از **نرم‌افزار متن‌باز** منتشرشده توسط شرکت‌های آمریکایی (IBM `mq-metric-samples`، Grafana، ClickHouse، OTel) در پژوهش بررسی نشده؛ پیشنهاد می‌شود صریحاً در دامنهٔ Q-24 گنجانده شود.
2. خود IBM MQ نرم‌افزار IBM است؛ وضعیت مجوز آن در بانک (مانند Splunk/SolarWinds) نیز می‌تواند در Q-25 بررسی شود. پژوهش آن را پوشش نداده است.
3. نام فایل ADR-0008 فرض شده پس از تغییر نام `ADR-0008-telemetry-store-postgresql-first-clickhouse-on-trigger.md` است (طبق B5).
