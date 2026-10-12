# Banking Service Observability & Intelligence Platform (BSOIP)

> نام کاری محصول. نام نهایی محصول و نام این Repository هنوز باز است (Q-12).

این Repository محل طراحی و توسعهٔ یک پلتفرم تخصصی **مانیتورینگ، Observability و هوش عملیاتی سرویس‌های بانکی** است؛ پلتفرمی که سلامت سرویس‌های بانکی، مسیر تراکنش‌ها، وابستگی اپلیکیشن‌ها، Load Balancerها، میان‌افزارها، صف‌های پیام و دیتابیس‌ها را تحلیل می‌کند و در تشخیص علت‌های احتمالی اختلال و پیش‌بینی ناهنجاری‌ها به اپراتور کمک می‌کند.

این محصول یک ابزار عمومی مانیتورینگ زیرساخت نیست. سامانه‌های موجود بانک (SolarWinds، Zabbix، Splunk و Elasticsearch) منبع شواهد آن هستند، نه هویت آن.

## وضعیت فعلی

**فاز صفر (Architecture Discovery and Master Planning)**. در این فاز فقط مستندات معماری و برنامه‌ریزی تولید شده است. هیچ کد اجرایی، Migration دیتابیس یا زیرساخت Production در این Repository وجود ندارد و تا تأیید صریح مالک محصول ایجاد نخواهد شد.

## محدودیت‌های قطعی

- استقرار صرفاً On-Premises در دیتاسنتر بانک و بدون اینترنت مستقیم.
- استفاده از SolarWinds، Zabbix، Splunk و Elasticsearch موجود در مراحل اولیه.
- Instrumentation مستقیم اپلیکیشن‌ها فقط در مراحل بعدی و بر اساس نیاز واقعی.
- معماری Enterprise با توسعهٔ مرحله‌ای و پرهیز از پیچیدگی غیرضروری.
- مستندات به فارسی؛ شناسه‌ها، نام فایل‌ها و اصطلاحات استاندارد فنی به انگلیسی.

## مستندات

فهرست کامل مستندات فاز صفر در [docs/README.md](docs/README.md) آمده است. برای شروع:

1. [گزارش تکمیل فاز صفر](docs/phase-0-completion-report.md)
2. [Product Charter](docs/vision/product-charter.md)
3. [Master Plan](docs/roadmap/master-plan.md)
4. [پرسش‌های باز و فرضیات](docs/architecture/open-questions-and-assumptions.md)
