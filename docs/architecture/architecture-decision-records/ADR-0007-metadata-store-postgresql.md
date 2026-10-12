# ADR-0007: PostgreSQL به‌عنوان Store متادیتا (Metadata Store PostgreSQL)

| ID | Title | Status | Confidence | Date | Deciders |
|---|---|---|---|---|---|
| ADR-0007 | metadata-store-postgresql | Proposed | Firm | 2026-10-11 (۱۴۰۵/۰۷/۱۹) | Architecture (pending product owner approval) |

Related documents: [technology-evaluation.md §۶](../technology-evaluation.md)، [data-architecture.md](../data-architecture.md)، [capacity-planning.md §۴.۵](../capacity-planning.md)، [ADR-0011](ADR-0011-topology-model-and-dependency-discovery.md)

## Context

داده‌های متادیتا (کاتالوگ، Topology، تقویم، SLO، پیکربندی connector، incident، نگاشت IAM، audit، outbox) رابطه‌ای، تراکنشی، کم‌حجم (برآورد Medium حدود ۴۵ تا ۱۲۰ GB شامل audit پنج‌ساله) و نیازمند یکپارچگی ارجاعی هستند. ویژگی‌های سفارشی ServiceType (FR-CAT-003) ساختار انعطاف‌پذیر می‌خواهد. Topology گرافی با ده‌ها هزار گره در High است.

## Decision

1. **PostgreSQL 16+** (community) به‌عنوان `metadata-db`.
2. Topology به‌صورت **جداول adjacency** (`component`، `dependency` با provenance/confidence/approval) و پیمایش با **CTE بازگشتی**؛ **بدون Graph DB**.
3. ویژگی‌های سفارشی در **JSONB** با اعتبارسنجی JSON Schema به ازای ServiceType و ایندکس GIN در صورت نیاز.
4. هر ماژول schema خود را دارد؛ مهاجرت‌ها با ابزار نسخه‌دار (Flyway یا Liquibase — در فاز ۱).
5. Audit به‌صورت append-only (فقط INSERT برای نقش برنامه)، partition ماهانه (ADR-0015).
6. رمزنگاری at-rest از طریق رمزنگاری دیسک/ذخیره‌ساز (TDE در community نیست — تأیید شود)؛ TLS برای همه اتصالات.

## Alternatives considered

| گزینه | امتیاز | دلیل |
|---|---|---|
| PostgreSQL (انتخاب) | 4.40 | CTE بازگشتی، JSONB، partitioning، مجوز باز، ابزار Backup بالغ |
| MariaDB/MySQL | 3.80 | امکانات JSON و نوع‌های غنی کمتر |
| SQL Server | 3.80 | HA عالی، ولی مجوز تجاری و lock-in |
| Oracle | 3.60 | قوی ولی مجوز، هزینه، lock-in |
| Graph DB (مثلاً Neo4j) برای Topology | — | Store سوم؛ اندازه گراف (≤ 30k گره) با CTE قابل مدیریت است؛ مجوز نسخه Enterprise (Q-21) |

## Rationale

اندازه و الگوی پیمایش Topology (عمق محدود، impact traversal) با CTE بازگشتی و ایندکس مناسب پاسخ می‌دهد [فرضیه، در BM-08 بررسی شود]. یک DBMS رابطه‌ای کافی است و افزودن Graph DB اصل «کمترین Store» را نقض می‌کند.

## Consequences

- مثبت: یکپارچگی تراکنشی برای کاتالوگ + outbox؛ ابزار شناخته‌شده.
- منفی: HA خودکار نیازمند Patroni و DCS (etcd یا مشابه) است — جزء عملیاتی اضافه در فاز ۶ (ADR-0017).
- هزینه عملیاتی: Backup با pgBackRest یا معادل، WAL archiving، تست بازیابی دوره‌ای، ارتقای major با `pg_upgrade`.

## Evidence that would change this decision

- سیاست بانک فقط DBMSهای تجاری تأییدشده را مجاز بداند (پیشنهاد افزودن به Q-03) → SQL Server/Oracle با لایه دسترسی داده قابل حمل.
- BM-08 نشان دهد پیمایش impact در Topology بزرگ از ۱ ثانیه بیشتر است → cache درون‌پردازه‌ای گراف (نه Graph DB) و سپس بازنگری.

## Related requirements

FR-CAT-001..FR-CAT-012، FR-CAL-001..FR-CAL-003، FR-ADM-001، FR-ADM-003، NFR-SEC-002، NFR-SEC-006، NFR-AVL-003

## Related questions

Q-03، Q-05، Q-10، Q-21
