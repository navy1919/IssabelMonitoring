# ADR-0015: احراز هویت، مجوزدهی و ممیزی (Authentication, Authorization and Audit)

| ID | Title | Status | Confidence | Date | Deciders |
|---|---|---|---|---|---|
| ADR-0015 | authentication-authorization-audit | Proposed | **Blocked (Q-04)** | 2026-10-11 (۱۴۰۵/۰۷/۱۹) | Architecture (pending product owner approval) |

Version: `0.1`

Related documents: [security-and-threat-model.md](../security-and-threat-model.md)، [ADR-0007](ADR-0007-metadata-store-postgresql.md)

## Context

- [فرض A-07] Active Directory موجود است؛ وجود IdP با OIDC/SAML و MFA نامعلوم ([باز Q-04]).
- پلتفرم داده عملیاتی حساس و credentialهای منابع را نگه می‌دارد (R-06)؛ کاربران متنوع (NOC، SRE، مالک سرویس، SOC، مدیریت، ادمین).
- ممیزی غیرقابل تغییر لازم است (NFR-SEC-006)؛ مدت نگهداری audit ≥ ۵ سال یا الزام قانونی ([باز Q-10]).

## Decision

1. **احراز هویت:** OIDC از طریق IdP بانک اگر موجود باشد؛ در غیر این صورت **Keycloak** (`identity-provider`) با federation به AD/LDAP. MFA از طریق IdP (NFR-SEC-009). SAML فقط اگر IdP بانک OIDC نداشته باشد (از طریق Keycloak به‌عنوان broker).
2. **مجوزدهی:** RBAC با deny-by-default (NFR-SEC-005)؛ نقش‌ها (پیشنهادی: `viewer`، `operator`، `service-owner`، `connector-admin`، `platform-admin`، `auditor`) و **scope** به ازای Environment و گروه سرویس؛ نگاشت گروه‌های AD به نقش در ماژول `iam`. اجرای مجوز در لایه API و در کوئری‌ها (فیلتر داده بر اساس scope).
3. **ارتباط داخلی:** mTLS بین اجزا؛ هویت سرویس با گواهی.
4. **Audit:** ماژول `audit`؛ جدول append-only در PostgreSQL (نقش برنامه فقط INSERT؛ زنجیره hash برای تشخیص دست‌کاری — [توصیه])؛ رویدادهای ورود، تغییر پیکربندی، دسترسی به evidence query، تغییر Topology/SLO، export؛ ارسال هم‌زمان به SIEM/Splunk بانک. partition ماهانه و بایگانی طبق [capacity-planning.md §۴.۵](../capacity-planning.md).
5. Break-glass: حساب محلی اضطراری با MFA و audit ویژه، فقط اگر IdP در دسترس نباشد.

## Alternatives considered

| گزینه | مزایا | معایب | نتیجه |
|---|---|---|---|
| IdP بانک (OIDC) | یکپارچگی، MFA مرکزی | وجود نامعلوم | ترجیح اول |
| Keycloak + AD federation | OIDC استاندارد، Apache 2.0 | یک جزء stateful دیگر | جایگزین |
| LDAP مستقیم در برنامه | ساده | بدون SSO/MFA، مدیریت رمز در برنامه | رد |
| ABAC کامل (مثلاً OPA) | انعطاف | پیچیدگی؛ RBAC+scope کافی است | آینده در صورت نیاز |
| Audit فقط در SIEM | متمرکز | وابستگی به سیستم خارجی؛ نیاز به نمایش در UI | ترکیبی |

## Rationale

OIDC استاندارد امکان تعویض IdP را بدون تغییر برنامه می‌دهد؛ scope به ازای محیط/گروه سرویس با ساختار سازمانی بانک (تیم‌های مالک سرویس) سازگار است.

## Consequences

- مثبت: SSO، MFA، ممیزی کامل.
- منفی: در صورت Keycloak، نیاز به HA و Backup آن؛ وابستگی ورود به IdP (کاهش با break-glass).
- هزینه عملیاتی: بازبینی دوره‌ای دسترسی‌ها؛ چرخش گواهی‌های mTLS.

## Evidence that would change this decision

- پاسخ Q-04 (نوع IdP، MFA).
- Q-10: الزام قانونی برای نگهداری audit در سامانه مشخص یا WORM.
- Q-11: چندبانکی بودن → tenant در مدل مجوز.

## Related requirements

FR-ADM-001، FR-ADM-002، FR-ADM-003، FR-ADM-004، NFR-SEC-001، NFR-SEC-005، NFR-SEC-006، NFR-SEC-009، NFR-CMP-001

## Related questions

Q-04 (مسدودکننده)، Q-09، Q-10، Q-11، R-06
