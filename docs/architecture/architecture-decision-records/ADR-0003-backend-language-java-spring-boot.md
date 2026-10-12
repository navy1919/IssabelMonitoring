# ADR-0003: زبان بک‌اند Java و Spring Boot (Backend Language Java / Spring Boot)

| ID | Title | Status | Confidence | Date | Deciders |
|---|---|---|---|---|---|
| ADR-0003 | backend-language-java-spring-boot | Proposed | **Blocked (Q-01)** | 2026-10-11 (۱۴۰۵/۰۷/۱۹) | Architecture (pending product owner approval) |

Related documents: [technology-evaluation.md §۳](../technology-evaluation.md)، [ADR-0002](ADR-0002-modular-monolith-with-edge-collectors.md)، [ADR-0004](ADR-0004-collector-and-connector-architecture.md)

## Context

- `platform-core` و `connector-runtime` باید به IBM MQ (PCF/JMS)، JMX، LDAP/AD، PostgreSQL، ClickHouse و در آینده Kafka متصل شوند.
- [فرض] مهارت Java در بانک‌های ایرانی رایج است؛ اما مهارت واقعی تیم نامعلوم است ([باز Q-01]، R-04).
- ساخت آفلاین با mirror داخلی وابستگی‌ها و SBOM الزامی است (NFR-DEP-001، NFR-SEC-007).

## Decision

[توصیه] **Java 21 LTS** (توزیع OpenJDK مانند Eclipse Temurin یا Red Hat build of OpenJDK — نه Oracle JDK تجاری) با **Spring Boot 3.x** برای `platform-core` و `connector-runtime`. ابزار ساخت Gradle یا Maven (در T-01-01 انتخاب شود) با mirror داخلی. Python فقط برای آزمایش‌های تحلیلی جداشده در فاز ۵ در صورت توجیه، و نه در مسیر runtime اصلی بدون ADR جدید.

این تصمیم تا پاسخ Q-01 **Blocked** است.

## Alternatives considered

| گزینه | امتیاز وزنی | مزایا | معایب |
|---|---|---|---|
| Java 21 + Spring Boot 3.x | 4.50 | کلاینت IBM MQ و JMX بومی، Driverهای بالغ، LTS، ArchUnit | مصرف حافظه بیشتر، زمان راه‌اندازی |
| Kotlin + Spring | 4.00 | زبان مدرن روی همان JVM | مهارت کمتر در بازار (فرض) |
| .NET 8 (در عمل .NET 10 LTS) | 3.90 | ابزار قوی، کلاینت MQ .NET | بدون JMX؛ پایان پشتیبانی .NET 8 در 2026-11 (تأیید شود) |
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

- پاسخ Q-01: تیم عمدتاً .NET و نبود امکان جذب نیروی Java → تحلیل حساسیت نشان می‌دهد .NET 10 برنده می‌شود (Java 4.10 در برابر .NET 4.20).
- عدم امکان استفاده از کلاینت IBM MQ Java در نسخه MQ بانک (Q-08) — بعید.
- سیاست بانک درباره زبان‌های مجاز توسعه.

## Related requirements

FR-MQ-002، FR-RUN-001، FR-ING-001، NFR-MNT-001، NFR-MNT-002، NFR-SEC-007، NFR-DEP-001

## Related questions

Q-01 (مسدودکننده)، Q-08، Q-09، R-04، R-09
