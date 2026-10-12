# ADR-0014: Frontend با React، ECharts و Cytoscape.js (Frontend React / ECharts / Cytoscape)

| ID | Title | Status | Confidence | Date | Deciders |
|---|---|---|---|---|---|
| ADR-0014 | frontend-react-echarts-cytoscape | Proposed | **Provisional** (BM-10، Q-01، Q-21) | 2026-10-11 (۱۴۰۵/۰۷/۱۹) | Architecture (pending product owner approval) |

Related documents: [technology-evaluation.md §۵](../technology-evaluation.md)، [capacity-planning.md (BM-10)](../capacity-planning.md#benchmark-plan)

## Context

- [واقعیت F-02] UI فارسی، RTL، تقویم جلالی؛ شناسه‌ها انگلیسی.
- داشبورد سری زمانی سنگین، نقشه Topology تا ۵۰۰ گره تعاملی ≤ ۲ ثانیه (NFR-PRF-005) و در High تا ۲۰۰۰ گره.
- ساخت آفلاین با mirror npm.

## Decision

1. SPA با **React + TypeScript** (`web-ui`).
2. نمودار با **Apache ECharts**؛ محور زمانی داخلی UTC با formatter جلالی و منطقه زمانی Asia/Tehran از tzdata.
3. گراف Topology با **Cytoscape.js** پشت یک لایه انتزاعی نازک (`TopologyRenderer`) تا تعویض با G6 در صورت شکست BM-10 محلی باشد.
4. منطق تقویم/قالب‌بندی تاریخ و اعداد فارسی در یک ماژول مشترک؛ هیچ offset ثابت.
5. کتابخانه کامپوننت با پشتیبانی RTL مستند (MUI یا Ant Design — در فاز ۱ با spike و بررسی مجوز انتخاب).
6. Grafana برای داشبوردهای محصول استفاده **نمی‌شود**؛ Grafana OSS فقط به‌صورت اختیاری برای self-monitoring پلتفرم، مشروط به بررسی AGPL ([باز Q-21]).

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
| Grafana embedded | — | مدل سرویس‌محور، RBAC، توضیح‌پذیری و RTL را پوشش نمی‌دهد؛ AGPL |

## Rationale

ترکیب انتخابی بیشترین امتیاز را در معیارهای RTL/جلالی و کارایی دارد و همه مجوزها سهل‌گیرند.

## Consequences

- مثبت: کنترل کامل تجربه کاربری فارسی و توضیح‌پذیری.
- منفی: ساخت داشبوردها به‌جای استفاده از ابزار آماده؛ نگهداری وابستگی‌های npm (زنجیره تأمین — R-09).
- هزینه عملیاتی: mirror npm، اسکن وابستگی‌ها، SBOM.

## Evidence that would change this decision

- BM-10: Cytoscape.js در ۵۰۰ گره به ≤ ۲ ثانیه نرسد یا bidi فارسی روی Canvas مشکل‌دار باشد → G6 یا رندر برچسب در لایه HTML.
- Q-01: تیم Angular → Angular (تحلیل حساسیت: 4.35 در برابر 4.25).
- Q-21: ممنوعیت یا محدودیت یک مجوز.

## Related requirements

FR-UI-001..FR-UI-004، FR-CAT-008، NFR-USA-001، NFR-USA-002، NFR-USA-003، NFR-PRF-005

## Related questions

Q-01، Q-21، BM-10
