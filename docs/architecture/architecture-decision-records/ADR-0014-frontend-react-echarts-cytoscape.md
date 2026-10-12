# ADR-0014: Frontend با React، ECharts و Cytoscape.js (Frontend React / ECharts / Cytoscape)

| ID | Title | Status | Confidence | Date | Deciders |
|---|---|---|---|---|---|
| ADR-0014 | frontend-react-echarts-cytoscape | Proposed | **Provisional** (BM-10، Q-01، Q-21، Q-23) | 2026-10-11 (۱۴۰۵/۰۷/۱۹)؛ بازنگری 2026-10-12 | Architecture (pending product owner approval) |

Version: `0.2` — تاریخچه تغییر در انتهای سند.

Related documents: [technology-evaluation.md §۵](../technology-evaluation.md)، [capacity-planning.md (BM-10)](../capacity-planning.md#benchmark-plan)، [ADR-0024](ADR-0024-visualization-split-custom-operator-ui-optional-grafana.md)

## Context

- [واقعیت F-02] UI فارسی، RTL، تقویم جلالی؛ شناسه‌ها انگلیسی.
- داشبورد سری زمانی سنگین، نقشه Topology تا ۵۰۰ گره تعاملی ≤ ۲ ثانیه (NFR-PRF-005) و در High تا ۲۰۰۰ گره.
- ساخت آفلاین با mirror npm.
- **پس از Product Benchmark (0.2):** تقسیم کار نمایش در [ADR-0024](ADR-0024-visualization-split-custom-operator-ui-optional-grafana.md) تعیین شد: UI سفارشی فقط برای صفحات اپراتور؛ نمای مهندسی (داشبوردهای فنی متریک) با Grafana OSS اختیاری یا deep link به ابزارهای موجود. این ADR فقط فناوری همان UI سفارشی را تعیین می‌کند.

## Decision

1. SPA با **React + TypeScript** (`web-ui`). **دامنه (0.2):** فقط صفحات اپراتور — سلامت سرویس‌ها، Incident و پنل شواهد، کاتالوگ و توپولوژی، تقویم و پنجره‌ها، SLO، مدیریت connector و مدیریت دسترسی. داشبوردهای فنی عمومی (CPU، حافظه، نمودارهای خام item) در `web-ui` ساخته **نمی‌شوند**؛ deep link به Zabbix/SolarWinds/Splunk/ES یا Grafana اختیاری (FR-INT-006).
2. نمودار با **Apache ECharts**؛ محور زمانی داخلی UTC با formatter جلالی و منطقه زمانی Asia/Tehran از tzdata.
3. گراف Topology با **Cytoscape.js** پشت یک لایه انتزاعی نازک (`TopologyRenderer`) تا تعویض با G6 در صورت شکست BM-10 محلی باشد.
4. منطق تقویم/قالب‌بندی تاریخ و اعداد فارسی در یک ماژول مشترک؛ هیچ offset ثابت.
5. کتابخانه کامپوننت با پشتیبانی RTL مستند (MUI یا Ant Design — در فاز ۱ با spike و بررسی مجوز انتخاب).
6. Grafana برای **صفحات اپراتور** استفاده نمی‌شود؛ نقش Grafana OSS (نمای مهندسی اختیاری و self-monitoring) در [ADR-0024](ADR-0024-visualization-split-custom-operator-ui-optional-grafana.md) تعیین شده است، مشروط به بررسی AGPL ([باز Q-21]).

## Alternatives considered

| گزینه | امتیاز | دلیل |
|---|---|---|
| React (انتخاب) | 4.70 | اکوسیستم RTL/جلالی، بازار نیرو |
| Angular | 4.05 | ساختار قوی؛ با تیم Angular برنده می‌شود |
| Vue | 3.90 | — |
| ECharts (انتخاب) | 4.80 | کارایی Canvas، انواع نمودار، Apache 2.0 |
| Highcharts | 4.20 | مجوز تجاری |
| Cytoscape.js (انتخاب) | 4.45 | layoutها، compound node، MIT |
| G6 (AntV) | 4.35 | WebGL، کارایی بالاتر — جایگزین اول |
| D3 | 4.15 | هزینه توسعه بالا |
| Grafana embedded برای صفحات اپراتور | — | مدل سرویس‌محور، RBAC، توضیح‌پذیری و RTL را پوشش نمی‌دهد؛ شواهدی از UI فارسی/RTL/جلالی در Grafana یافت نشد (Low–Medium)؛ AGPL — جزئیات در ADR-0024 |

## Rationale

ترکیب انتخابی بیشترین امتیاز را در معیارهای RTL/جلالی و کارایی دارد و همه مجوزها سهل‌گیرند.

## Consequences

- مثبت: کنترل کامل تجربه کاربری فارسی و توضیح‌پذیری.
- منفی: ساخت صفحات اپراتور به‌جای استفاده از ابزار آماده (دامنه با ADR-0024 محدود شد)؛ نگهداری وابستگی‌های npm (زنجیره تأمین — R-09).
- هزینه عملیاتی: mirror npm، اسکن وابستگی‌ها، SBOM.

## Evidence that would change this decision

- BM-10: Cytoscape.js در ۵۰۰ گره به ≤ ۲ ثانیه نرسد یا bidi فارسی روی Canvas مشکل‌دار باشد → G6 یا رندر برچسب در لایه HTML.
- Q-01: تیم Angular → Angular (تحلیل حساسیت: 4.35 در برابر 4.25).
- Q-21: ممنوعیت یا محدودیت یک مجوز.
- Q-23: اگر UI فارسی RTL/جلالی برای اپراتور الزامی نباشد، بخشی از صفحات (به‌ویژه نمودارهای KPI) می‌تواند به Grafana منتقل شود و دامنه این ADR کوچک‌تر شود (ADR-0024).

## Related requirements

FR-UI-001..FR-UI-004، FR-CAT-008، NFR-USA-001، NFR-USA-002، NFR-USA-003، NFR-PRF-005

## Related questions

Q-01، Q-21، Q-23، BM-10

## تاریخچه تغییر

| Version | Date | تغییر |
|---|---|---|
| 0.1 | 2026-10-11 | نسخه اولیه. |
| 0.2 | 2026-10-12 | تغییر پس از Product Benchmark: ارجاع به ADR-0024؛ دامنه UI سفارشی محدود به صفحات اپراتور؛ وابستگی به Q-23. |
