# ADR-0005: قراردادهای API و رویداد (API and Event Contracts)

| ID | Title | Status | Confidence | Date | Deciders |
|---|---|---|---|---|---|
| ADR-0005 | api-and-event-contracts | Proposed | Firm | 2026-10-11 (۱۴۰۵/۰۷/۱۹) | Architecture (pending product owner approval) |

Related documents: [container-architecture.md](../container-architecture.md)، [data-architecture.md](../data-architecture.md)، [ADR-0006](ADR-0006-canonical-schemas-otel-plus-bank-namespace.md)

## Context

- چند مرز ارتباطی وجود دارد: `web-ui` ↔ `api`، لبه ↔ `ingest`، `connector-runtime` ↔ `api` (پیکربندی و فرمان)، و در آینده webhook و ITSM.
- connectorها و هسته با سرعت متفاوت منتشر می‌شوند؛ ناسازگاری نسخه در محیط آفلاین (که ارتقای هم‌زمان همه Zoneها دشوار است) خطرناک است.

## Decision

1. **REST** با مشخصات **OpenAPI 3.1**، طراحی contract-first؛ نسخه در مسیر (`/api/v1/...`، داخلی `/internal/v1/...`). احراز هویت UI با OIDC bearer token؛ داخلی با mTLS.
2. **به‌روزرسانی زنده UI** با Server-Sent Events.
3. **تله‌متری** با **OTLP** (gRPC یا HTTP/protobuf).
4. **رویدادهای canonical** (رویداد نرمال‌شده، تغییر، incident، رویداد دامنه در outbox) با پاکت به سبک **CloudEvents** (`id`، `source`، `type`، `time`، `specversion`، `dataschema`، `datacontenttype`) و بدنه با **JSON Schema** نسخه‌دار.
5. **نسخه‌گذاری معنایی (semver)** با قواعد سازگاری: افزودن فیلد اختیاری = minor؛ حذف/تغییر معنا = major با دوره هم‌زیستی حداقل یک نسخه major قبلی؛ مصرف‌کننده‌ها فیلدهای ناشناخته را نادیده می‌گیرند (tolerant reader).
6. **Contract test** در CI: اعتبارسنجی OpenAPI/JSON Schema، تست مصرف‌کننده-محور برای Connector SDK، و بررسی تغییرات شکننده (breaking change diff).
7. یکسان‌سازی خطا با `application/problem+json` (RFC 9457).

## Alternatives considered

| گزینه | مزایا | معایب | نتیجه |
|---|---|---|---|
| OpenAPI REST + OTLP + CloudEvents/JSON Schema (انتخاب) | استاندارد، ابزار فراوان، قابل خواندن | JSON حجیم‌تر از باینری | **پذیرفته** |
| gRPC برای همه | کارایی، قرارداد قوی | UI مرورگری نیازمند gRPC-Web/پراکسی؛ اشکال‌زدایی سخت‌تر | فقط برای OTLP |
| GraphQL برای UI | انعطاف query | کنترل هزینه query و مجوزدهی فیلدی پیچیده | رد (قابل بازنگری) |
| Avro + Schema Registry | تکامل schema قوی | نیاز به registry؛ مناسب وقتی Kafka بیاید | بازنگری با ADR-0010 |
| WebSocket برای زنده | دوطرفه | نیاز واقعی یک‌طرفه است؛ SSE ساده‌تر و سازگار با پراکسی | رد |

## Rationale

قراردادهای استاندارد باز هزینه یکپارچه‌سازی با ابزارهای بانک (ITSM، SIEM) را کم می‌کنند و lock-in ندارند. CloudEvents پاکتی سبک و شناخته‌شده است که بعداً بدون تغییر به Kafka منتقل می‌شود.

## Consequences

- مثبت: ارتقای مستقل connectorها در Zoneها با سازگاری عقب‌رو.
- منفی: نگهداری schemaها و تست‌های قرارداد هزینه دارد.
- هزینه عملیاتی: انتشار مستندات API در بسته آفلاین؛ نگهداری حداقل دو نسخه major API داخلی در دوره گذار.

## Evidence that would change this decision

- معرفی Kafka (ADR-0010) → ارزیابی Avro/Protobuf با registry برای topicها.
- نیاز اثبات‌شده به query انعطاف‌پذیر از سوی مصرف‌کنندگان خارجی → GraphQL فقط‌خواندنی.

## Related requirements

FR-ING-001، FR-ING-002، FR-COR-001، FR-INT-005، FR-NTF-001، NFR-MNT-004، NFR-SEC-001، NFR-REL-001

## Related questions

Q-14 (ابزار ITSM)، Q-09
