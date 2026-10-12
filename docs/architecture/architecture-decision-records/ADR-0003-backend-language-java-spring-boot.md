# ADR-0003: زبان بک‌اند Java و Spring Boot (Backend Language Java / Spring Boot)

| ID | Title | Status | Confidence | Date | Deciders |
|---|---|---|---|---|---|
| ADR-0003 | backend-language-java-spring-boot | Proposed | **Blocked (Q-01)** | 2026-10-11 (۱۴۰۵/۰۷/۱۹)؛ بازنگری 2026-10-12 | Architecture (pending product owner approval) |

Version: `0.2` — تاریخچه تغییر در انتهای سند.

Related documents: [technology-evaluation.md §۳](../technology-evaluation.md)، [ADR-0002](ADR-0002-modular-monolith-with-edge-collectors.md)، [ADR-0004](ADR-0004-collector-and-connector-architecture.md)

## Context

- `platform-core` و `connector-runtime` باید به IBM MQ (PCF/JMS)، JMX، LDAP/AD، PostgreSQL، ClickHouse و در آینده Kafka متصل شوند.
- [فرض] مهارت Java در بانک‌های ایرانی رایج است؛ اما مهارت واقعی تیم نامعلوم است ([باز Q-01]، R-04).
- ساخت آفلاین با mirror داخلی وابستگی‌ها و SBOM الزامی است (NFR-DEP-001، NFR-SEC-007).
- **چرخه پشتیبانی .NET (0.2):** پشتیبانی .NET 8 LTS حدود نوامبر 2026 پایان می‌یابد (**نیازمند تأیید** در سیاست پشتیبانی Microsoft). چون Phase 1 پس از این تاریخ آغاز می‌شود، گزینه .NET در عمل یعنی **.NET 10 LTS**؛ هر جا در این بسته «.NET» آمده، منظور .NET 10 است.
- با راهبرد Reuse-first ([ADR-0021](ADR-0021-build-buy-reuse-strategy.md)) دامنه کد سفارشی کوچک‌تر شده است (connectorها + لایه معنایی بانکی)؛ این وزن «مهارت موجود تیم» را نسبت به «اکوسیستم» بالاتر می‌برد. همچنین MQ با exporter `mq_prometheus` خوانده می‌شود ([ADR-0004](ADR-0004-collector-and-connector-architecture.md))، پس مزیت کلاینت بومی IBM MQ Java کمتر از برآورد 0.1 است (JMX در Phase 4 همچنان مزیت Java است).

## Decision

[توصیه] **Java 21 LTS** (توزیع OpenJDK مانند Eclipse Temurin یا Red Hat build of OpenJDK — نه Oracle JDK تجاری) با **Spring Boot 3.x** برای `platform-core` و `connector-runtime`. ابزار ساخت Gradle یا Maven (در T-01-01 انتخاب شود) با mirror داخلی. Python فقط برای آزمایش‌های تحلیلی جداشده در فاز ۵ در صورت توجیه، و نه در مسیر runtime اصلی بدون ADR جدید.

این تصمیم تا پاسخ Q-01 **Blocked** است.

## Alternatives considered

| گزینه | امتیاز وزنی | مزایا | معایب |
|---|---|---|---|
| Java 21 + Spring Boot 3.x | 4.50 | کلاینت IBM MQ و JMX بومی، Driverهای بالغ، LTS، ArchUnit | مصرف حافظه بیشتر، زمان راه‌اندازی |
| Kotlin + Spring | 4.00 | زبان مدرن روی همان JVM | مهارت کمتر در بازار (فرض) |
| .NET 10 LTS (نه .NET 8) | 3.90 | ابزار قوی، کلاینت MQ .NET | بدون JMX؛ .NET 8 حدود 2026-11 از پشتیبانی خارج می‌شود (نیازمند تأیید) پس فقط .NET 10 قابل طرح است |
| Go | 3.20 | باینری سبک، هم‌روندی | بدون JMX، کلاینت MQ مبتنی بر C، نگهداشت monolith بزرگ ضعیف‌تر |
| Python | 2.80 | تحلیل داده | کارایی، تایپ، نگهداشت monolith |

جزئیات امتیاز در [technology-evaluation.md](../technology-evaluation.md).

## Rationale

اکوسیستم یکپارچه‌سازی (L1) و نگهداشت‌پذیری monolith ماژولار (L4) بیشترین وزن را دارند و Java در هر دو بالاترین امتیاز را دارد. Spring Boot امکانات امنیتی (OIDC resource server، mTLS)، observability (Micrometer/OTel) و پیکربندی نقش‌محور (profiles) را فراهم می‌کند.

## Consequences

- مثبت: یک زبان برای هسته و connectorها؛ اشتراک Connector SDK.
- منفی: مصرف حافظه JVM (برآورد گره‌ها در [capacity-planning.md](../capacity-planning.md)).
- هزینه عملیاتی: برنامه ارتقای منظم Spring Boot (بازه پشتیبانی OSS هر نسخه محدود است — تأیید شود) و JDK LTS.
- لازم: mirror داخلی Maven، اسکن آسیب‌پذیری وابستگی‌ها، SBOM CycloneDX.

## Evidence that would change this decision

- پاسخ Q-01: تیم عمدتاً .NET و نبود امکان جذب نیروی Java → تحلیل حساسیت نشان می‌دهد .NET 10 برنده می‌شود (Java 4.10 در برابر .NET 4.20). **قاعده بستن انسداد:** اگر Q-01 نشان دهد تیم عمدتاً .NET است، تصمیم به .NET 10 LTS تغییر می‌کند (کاهش دامنه کد در 0.2 این جابه‌جایی را محتمل‌تر می‌کند).
- عدم امکان استفاده از کلاینت IBM MQ Java در نسخه MQ بانک (Q-08) — بعید.
- سیاست بانک درباره زبان‌های مجاز توسعه.

## Related requirements

FR-MQ-002، FR-RUN-001، FR-ING-001، NFR-MNT-001، NFR-MNT-002، NFR-SEC-007، NFR-DEP-001

## Related questions

Q-01 (مسدودکننده)، Q-08، Q-09، R-04، R-09

## تاریخچه تغییر

| Version | Date | تغییر |
|---|---|---|
| 0.1 | 2026-10-11 | نسخه اولیه. |
| 0.2 | 2026-10-12 | تغییر پس از Product Benchmark: .NET 8 پایان پشتیبانی حدود نوامبر 2026 (نیازمند تأیید) → گزینه .NET یعنی .NET 10؛ اثر Reuse-first بر وزن مهارت تیم. |
