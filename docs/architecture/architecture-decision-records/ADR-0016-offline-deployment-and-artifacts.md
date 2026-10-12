# ADR-0016: استقرار آفلاین و Artifactها (Offline Deployment and Artifacts)

| ID | Title | Status | Confidence | Date | Deciders |
|---|---|---|---|---|---|
| ADR-0016 | offline-deployment-and-artifacts | Proposed | **Blocked (Q-02، Q-03)** | 2026-10-11 (۱۴۰۵/۰۷/۱۹) | Architecture (pending product owner approval) |

Related documents: [deployment-and-runbooks.md](../../operations/deployment-and-runbooks.md)، [security-and-threat-model.md](../security-and-threat-model.md)

## Context

- [واقعیت F-02] on-premises، بدون اینترنت مستقیم.
- [فرض A-02] VM لینوکسی سازگار با RHEL؛ وجود Kubernetes/OpenShift داخلی نامعلوم ([باز Q-02])؛ استاندارد سیستم‌عامل و روش تأمین VM نامعلوم ([باز Q-03]).
- [فرض A-12] mirror داخلی artifact قابل تأمین است؛ وجود Nexus/Harbor/Artifactory نامعلوم ([باز Q-09]).
- نصب ≤ ۱ روز روی VM آماده (NFR-DEP-002)، rollback در پنجره نگهداری (NFR-DEP-003)، SBOM و امضا (NFR-SEC-007).

## Decision

1. همه اجزا به‌صورت **OCI image** بسته‌بندی می‌شوند.
2. استقرار اول روی **VM لینوکسی با Podman (ترجیحی در خانواده RHEL) یا Docker Compose**؛ اجزای stateful (PostgreSQL، ClickHouse، Keeper) می‌توانند در صورت سیاست بانک به‌صورت بسته RPM بومی نصب شوند.
3. **Helm chart** فقط در صورت وجود Kubernetes/OpenShift داخلی بالغ (Q-02)، در فاز بعدی.
4. **بسته آفلاین امضاشده**: imageها (`oci-archive`)، chart/compose، اسکریپت نصب idempotent، **SBOM CycloneDX** به ازای image، checksum (SHA-256)، امضا (مثلاً cosign با کلید بانک یا GPG — در فاز ۱ تعیین)، یادداشت انتشار و دستور rollback.
5. **رجیستری داخلی** (mirror) برای image، Maven، npm و بسته‌های سیستم‌عامل؛ ساخت CI به‌صورت کاملاً آفلاین از mirror.
6. پیکربندی به‌صورت code (NFR-DEP-004)؛ secretها از طریق SecretProvider، نه در بسته.

## Alternatives considered

| گزینه | مزایا | معایب | نتیجه |
|---|---|---|---|
| OCI روی VM با Podman/Compose (انتخاب اولیه) | بدون پیش‌نیاز K8s، ساده | ارکستراسیون دستی‌تر، HA با پیکربندی | **پذیرفته (مشروط)** |
| Kubernetes/OpenShift | خودترمیمی، rolling update | پیش‌نیاز پلتفرم و مهارت؛ اگر نباشد، ساختن آن خارج از دامنه است | در صورت Q-02 |
| بسته‌های بومی RPM برای همه | آشنا برای تیم زیرساخت | ساخت/نگهداری بسته برای هر جزء | فقط برای stateful در صورت سیاست |
| Ansible بدون container | خودکارسازی | وابستگی به وضعیت سیستم‌عامل | به‌عنوان ابزار نصب، مکمل |

## Rationale

OCI image قابل‌حمل‌ترین واحد تحویل است و هر دو مسیر VM و K8s را پوشش می‌دهد.

## Consequences

- مثبت: نصب تکرارپذیر، تحلیل آسیب‌پذیری بر اساس SBOM.
- منفی: اندازه بسته آفلاین (چند GB)؛ نیاز به فرایند انتقال امن بسته به شبکه بانک.
- هزینه عملیاتی: نگهداری mirror؛ به‌روزرسانی دوره‌ای image پایه برای وصله‌های امنیتی.
- ریسک دسترسی به مخازن عمومی از ایران (R-09) — تأمین وابستگی‌ها ممکن است نیازمند مسیر جایگزین باشد (نیازمند بررسی).

## Evidence that would change this decision

- Q-02: OpenShift/K8s داخلی با تیم پشتیبانی → Helm/Operator اولویت.
- Q-03: سیاست ممنوعیت container runtime روی VMهای تولید → بسته بومی.
- Q-09: رجیستری موجود → استفاده از آن به‌جای ایجاد mirror جدید.

## Related requirements

NFR-DEP-001..NFR-DEP-004، NFR-SEC-007، NFR-SEC-008

## Related questions

Q-02، Q-03 (مسدودکننده)، Q-09، Q-21، R-09
