# ADR-0006: Schemaهای canonical بر پایه OTel به‌علاوه فضای نام بانکی (Canonical Schemas: OTel + Bank Namespace)

| ID | Title | Status | Confidence | Date | Deciders |
|---|---|---|---|---|---|
| ADR-0006 | canonical-schemas-otel-plus-bank-namespace | Proposed | Firm | 2026-10-11 (۱۴۰۵/۰۷/۱۹) | Architecture (pending product owner approval) |

Related documents: [data-architecture.md](../data-architecture.md)، [domain-model.md](../../vision/domain-model.md)، [ADR-0005](ADR-0005-api-and-event-contracts.md)، [ADR-0019](ADR-0019-transaction-correlation-tiers.md)

## Context

- داده از منابع ناهمگن (Zabbix، SolarWinds، Splunk، ES، MQ، LB) با نام‌گذاری متفاوت می‌آید؛ برای همبستگی باید به مدل واحد نگاشت شود.
- مفاهیم بانکی (سرویس کسب‌وکار، نوع تراکنش، outcome، tier همبستگی) در OTel semantic conventions وجود ندارند.

## Decision

1. تله‌متری از **OpenTelemetry semantic conventions** استفاده می‌کند (مثلاً `service.name`، `host.name`، `messaging.system`، `http.response.status_code`) — نسخه convention در schema URL ثبت می‌شود.
2. مفاهیم بانکی در فضای نام **`bank.*`** تعریف می‌شوند، از جمله: `bank.service.id`، `bank.technical_service.id`، `bank.component.id`، `bank.environment`، `bank.zone`، `bank.txn.type`، `bank.txn.outcome` (مقادیر `TxnOutcome`)، `bank.txn.correlation_tier` (`L0`..`L3`)، `bank.txn.key_token` (فقط توکن HMAC)، `bank.source.system`، `bank.source.ref`.
3. همه رکوردها دارای `observed_at` (زمان منبع)، `ingested_at`، و در صورت امکان `source_clock_skew_estimate`؛ زمان به UTC با دقت میلی‌ثانیه.
4. فهرست ویژگی‌ها و مقادیر مجاز در یک **registry نسخه‌دار** (فایل YAML در مخزن + JSON Schema) نگهداری و در ingest اعتبارسنجی می‌شود؛ ویژگی ناشناخته در `bank.*` رد یا قرنطینه می‌شود.
5. کنترل cardinality: ویژگی‌های با cardinality نامحدود (مثل شناسه درخواست) مجاز به عنوان برچسب متریک نیستند.

## Alternatives considered

| گزینه | مزایا | معایب | نتیجه |
|---|---|---|---|
| OTel + `bank.*` (انتخاب) | استاندارد، سازگار با instrumentation آینده (فاز ۷) | نیاز به نگاشت از منابع غیر OTel | **پذیرفته** |
| مدل کاملاً اختصاصی | آزادی کامل | ناسازگاری با OTel SDK آینده، بازاختراع | رد |
| Elastic Common Schema (ECS) | مناسب لاگ | هم‌گرایی ECS با OTel در جریان است؛ تمرکز بر لاگ | رد (نگاشت ECS→OTel در connector ES) |
| فقط OTel بدون namespace بانکی | ساده | مفاهیم بانکی بی‌جا | رد |

## Rationale

OTel مسیر instrumentation مستقیم آینده (فاز ۷) را بدون مهاجرت schema باز نگه می‌دارد؛ فضای نام اختصاصی از تداخل با convention‌های آینده OTel جلوگیری می‌کند.

## Consequences

- مثبت: همبستگی میان منابع، کوئری یکسان، آمادگی برای L3 trace.
- منفی: هر connector باید نگاشت صریح داشته باشد (تست‌شده با harness — FR-ING-011).
- هزینه: نگهداری registry و پیگیری تغییرات convention‌های OTel (برخی هنوز experimental — تأیید شود).

## Evidence that would change this decision

- اگر بانک schema سازمانی اجباری برای داده عملیاتی داشته باشد → نگاشت دوطرفه.
- تغییر شکننده در convention‌های OTel → قفل نسخه schema URL و مهاجرت برنامه‌ریزی‌شده.

## Related requirements

FR-ING-006، FR-TXN-002، FR-TXN-008، FR-COR-001، FR-CAT-009، NFR-PRV-003

## Related questions

Q-07، Q-17، R-11، R-12
