# ADR-0024: تقسیم نمایش؛ UI سفارشی اپراتور و Grafana اختیاری (Visualization Split: Custom Operator UI, Optional Grafana)

| ID | Title | Status | Confidence | Date | Deciders |
|---|---|---|---|---|---|
| ADR-0024 | visualization-split-custom-operator-ui-optional-grafana | Proposed | **Provisional** (Q-23، Q-21) | 2026-10-12 (۱۴۰۵/۰۷/۲۰) | Architecture (pending product owner approval) |

Version: `0.2` — نخستین نسخه این ADR در بازنگری پس از Product Benchmark ایجاد شد.

Related documents: [product-benchmark.md](../../benchmark/product-benchmark.md)، [competitive-gap-analysis.md](../../benchmark/competitive-gap-analysis.md)، [ADR-0014](ADR-0014-frontend-react-echarts-cytoscape.md)، [ADR-0021](ADR-0021-build-buy-reuse-strategy.md)، [container-architecture.md](../container-architecture.md)

## Context

- [واقعیت F-02] مستندات فارسی؛ charter محصول UI فارسی RTL با تقویم جلالی را جزو تمایزها آورده است؛ اینکه این برای اپراتور **الزامی** است یا UI انگلیسی پذیرفتنی است، باز است ([باز Q-23]).
- یافته‌های Benchmark (research-oss #35–#39):
  - Grafana OSS از 2021 تحت **AGPLv3** است؛ اجرای بدون تغییر داخلی تعهد افشا ندارد ولی تغییر و ارائه به کاربران دارد (https://grafana.com/licensing/، High).
  - پنل **node graph** می‌تواند توپولوژی را از API گره/یال رسم کند؛ پیش‌فرض ۲۰۰ گره و کند شدن layout بالای ۵۰۰ گره (https://grafana.com/docs/grafana/latest/panels-visualizations/visualizations/node-graph/، High).
  - Alerting در OSS با mute timing — تقریب پنجره سرویس، **نه** تقویم جلالی/تعطیلات (Medium-High).
  - نصب آفلاین plugin با ZIP ممکن است (High).
  - **شواهدی از UI فارسی، RTL یا انتخابگر زمان جلالی** در Grafana، Kibana یا OpenSearch Dashboards یافت نشد (Low–Medium؛ نبود شاهد).
  - plugin Zabbix برای Grafana وجود دارد (https://grafana.com/grafana/plugins/alexanderzobnin-zabbix-app/، High).
- کاربران مهندسی (SRE، تیم میانه‌افزار) از قبل با Zabbix/Splunk/Kibana کار می‌کنند؛ اپراتور NOC به نمای سرویس‌محور با توضیح و شواهد نیاز دارد.

## Decision

1. **UI سفارشی (`web-ui`) فقط برای صفحات اپراتور:** سلامت سرویس‌های بانکی با توضیح، Incident و پنل شواهد (واقعیت در برابر استنتاج، اطمینان)، کاتالوگ و توپولوژی با provenance، تقویم و پنجره‌ها، SLO/error budget، مدیریت connector و دسترسی. فناوری مطابق [ADR-0014](ADR-0014-frontend-react-echarts-cytoscape.md).
2. **نمای مهندسی** (نمودارهای فنی متریک، مقایسه آزاد سری‌ها) در `web-ui` ساخته نمی‌شود؛ گزینه‌ها به ترتیب ترجیح: (الف) **deep link** به Zabbix/SolarWinds/Splunk/ES با بازه زمانی و شناسه موجودیت (FR-INT-006)؛ (ب) **Grafana OSS اختیاری** بدون تغییر کد، با datasource PostgreSQL (فقط‌خواندنی روی جدول‌های KPI مشتق) و plugin Zabbix، مشروط به بررسی AGPL (Q-21).
3. Grafana در صورت استقرار: **بدون** Alerting فعال برای سرویس‌های بانکی (جلوگیری از هشدار تکراری — اعلان فقط از پلتفرم یا کانال‌های موجود)؛ check_for_updates و reporting خاموش؛ احراز هویت از IdP بانک؛ نسخه pin‌شده در بسته آفلاین.
4. **اگر Q-23 نشان دهد UI فارسی RTL/جلالی برای اپراتور الزامی نیست:** نمودارهای KPI صفحات اپراتور می‌توانند به‌صورت داشبورد Grafana embed/link شوند و `web-ui` به صفحات تعاملی (Incident، شواهد، کاتالوگ، تقویم) محدود شود. اگر الزامی باشد، همه صفحات اپراتور در `web-ui` می‌مانند.
5. self-monitoring پلتفرم (NFR-OBS-001) می‌تواند در همان Grafana اختیاری یا در صفحه سلامت `web-ui` نمایش داده شود.

## Alternatives considered

| گزینه | مزایا | معایب | نتیجه |
|---|---|---|---|
| تقسیم: UI سفارشی اپراتور + Grafana اختیاری/deep link (انتخاب) | کد UI کمتر؛ بازاستفاده ابزار آشنای مهندسان | دو تجربه کاربری؛ AGPL برای Grafana | **پیشنهاد** |
| همه چیز در UI سفارشی (0.1) | تجربه یکپارچه فارسی | ساخت داشبوردهای فنی که در ابزارهای موجود هست | رد |
| همه چیز در Grafana (با app plugin سفارشی) | کمترین کد UI پایه | بدون شاهد RTL/جلالی؛ مدل سرویس، RBAC و توضیح‌پذیری ضعیف؛ AGPL در صورت تغییر | رد مگر Q-23 «غیرالزامی» و PoC کفایت node graph را نشان دهد |
| Kibana / OpenSearch Dashboards | موجود در کنار ES | بدون جلالی بومی؛ وابستگی به failure domain ES (NFR-AVL-002) | رد |
| فقط deep link، بدون Grafana | بدون جزء جدید | مقایسه بین‌منبعی در یک نما ممکن نیست | پیش‌فرض Phase 1–2؛ Grafana در صورت درخواست مهندسان |

## Rationale

ارزش متمایز UI در نمای سرویس‌محور و توضیح‌پذیر است، نه در نمودار متریک عمومی که Zabbix/Grafana/Kibana فراهم می‌کنند. محدود کردن دامنه `web-ui` هزینه Phase 2–4 را کاهش می‌دهد و تصمیم نهایی درباره اندازه UI را به پاسخ Q-23 گره می‌زند.

## Consequences

- مثبت: کاهش کار frontend؛ عدم تکرار داشبوردهای موجود.
- منفی: کاربر مهندس بین چند ابزار جابه‌جا می‌شود؛ deep link به پایداری URL ابزارهای منبع وابسته است (R-12).
- هزینه: در صورت Grafana، یک جزء stateful کوچک (پایگاه داخلی Grafana یا PostgreSQL) و به‌روزرسانی امنیتی آفلاین؛ بررسی AGPL (R-18).

## Evidence that would change this decision

- Q-23 (الزامی بودن فارسی RTL/جلالی) → بند ۴.
- Q-21: AGPL مجاز نباشد → فقط deep link.
- بازخورد اپراتورها در VS-1 که نمای مهندسی را در `web-ui` لازم بداند → افزودن صفحه محدود.
- انتشار پشتیبانی رسمی RTL/جلالی در Grafana → بازنگری گزینه «همه در Grafana».

## Related requirements

FR-UI-001..FR-UI-004، FR-INT-006، FR-CAT-008، FR-NTF-001، NFR-USA-001، NFR-USA-002، NFR-OBS-001، NFR-PRF-005

## Related questions

Q-01، Q-21، Q-23، BM-10، R-18
