# فهرست مستندات فاز صفر

| فیلد | مقدار |
|---|---|
| Document ID | `DOC-INDEX` |
| Version | `0.2` |
| Status | `Draft – Phase 0 – awaiting approval` |
| Date | `2026-10-12` (۱۴۰۵/۰۷/۲۰) |
| Owner | `Architecture` |
| Related documents | [master-plan.md](roadmap/master-plan.md)، [ADR index](architecture/architecture-decision-records/README.md)، [open-questions-and-assumptions.md](architecture/open-questions-and-assumptions.md) |

نام‌های درخواستی در افزونهٔ Benchmark به اسناد موجود نگاشت شده‌اند تا سند تکراری ساخته نشود: `system-architecture.md` → [container-architecture.md](architecture/container-architecture.md)، `implementation-roadmap.md` → [master-plan.md](roadmap/master-plan.md)، `open-questions.md` → [open-questions-and-assumptions.md](architecture/open-questions-and-assumptions.md)، `technology-decision-records.md` → ADRهای جداگانه (ADR-0021 تا ADR-0024 و بازنگری ADR-0008/0010).

| شناسه | سند | محتوا |
|---|---|---|
| — | [phase-0-completion-report.md](phase-0-completion-report.md) | گزارش اجرایی تکمیل فاز صفر و تصمیم‌های نیازمند تأیید |
| 1 | [vision/product-charter.md](vision/product-charter.md) | چشم‌انداز، کاربران هدف، تمایزها، محدوده، Non-goals، معیارهای موفقیت |
| 2 | [vision/domain-model.md](vision/domain-model.md) | موجودیت‌های دامنهٔ بانکی، اصطلاحات، چرخهٔ عمر تراکنش، قواعد توسعه‌پذیری |
| 3 | [requirements/functional-requirements.md](requirements/functional-requirements.md) | نیازمندی‌های وظیفه‌مندی با شناسه، اولویت، وابستگی و معیار پذیرش |
| 4 | [requirements/non-functional-requirements.md](requirements/non-functional-requirements.md) | نیازمندی‌های غیروظیفه‌مندی |
| 5 | [architecture/system-context.md](architecture/system-context.md) | Context سیستم، بازیگران، سامانه‌های بیرونی، مرزهای اعتماد |
| 6 | [architecture/container-architecture.md](architecture/container-architecture.md) | اجزای منطقی، مسئولیت‌ها، پروتکل‌ها، مرزهای استقرار |
| 7 | [architecture/data-architecture.md](architecture/data-architecture.md) | Schemaهای Canonical و نگاشت ذخیره‌سازی |
| 8 | [architecture/ingestion-and-connectors.md](architecture/ingestion-and-connectors.md) | الگوهای جمع‌آوری، قرارداد Connector، نرمال‌سازی، Backpressure |
| 9 | [architecture/correlation-and-intelligence.md](architecture/correlation-and-intelligence.md) | مدل سلامت، همبستگی، کمک به تشخیص علت، تشخیص ناهنجاری |
| 10 | [architecture/security-and-threat-model.md](architecture/security-and-threat-model.md) | مدل تهدید، کنترل دسترسی، دادهٔ حساس |
| 11 | [architecture/technology-evaluation.md](architecture/technology-evaluation.md) | ارزیابی وزن‌دار فناوری‌ها |
| 12 | [architecture/architecture-decision-records/](architecture/architecture-decision-records/README.md) | ADRها |
| 13 | [architecture/capacity-planning.md](architecture/capacity-planning.md) | سناریوهای بار، مدل ظرفیت، برنامهٔ Benchmark |
| 14 | [roadmap/master-plan.md](roadmap/master-plan.md) | نقشهٔ راه فازبندی‌شده، Milestoneها، معیارهای خروج |
| 15 | [roadmap/implementation-backlog.md](roadmap/implementation-backlog.md) | Epicها، User Storyها، Taskها |
| 16 | [testing/quality-and-validation-strategy.md](testing/quality-and-validation-strategy.md) | راهبرد آزمون و اعتبارسنجی |
| 17 | [operations/deployment-and-runbooks.md](operations/deployment-and-runbooks.md) | استقرار، نصب آفلاین، پشتیبان‌گیری، ارتقا، Runbookها |
| 18 | [architecture/open-questions-and-assumptions.md](architecture/open-questions-and-assumptions.md) | پرسش‌های باز، فرضیات، آزمایش‌های پیشنهادی، پرسش‌های فروشندگان |
| B1 | [benchmark/product-benchmark.md](benchmark/product-benchmark.md) | Product Benchmark: محصولات بر اساس نقش، شواهد با منبع، محدودیت‌ها، وضعیت تحریم و مجوز |
| B2 | [benchmark/competitive-gap-analysis.md](benchmark/competitive-gap-analysis.md) | نگاشت نیازمندی‌های بانکی به محصولات و فهرست شکاف‌ها |
| B3 | [benchmark/build-buy-reuse-analysis.md](benchmark/build-buy-reuse-analysis.md) | گزینه‌های راهبردی A تا E، ماتریس وزن‌دار، تقسیم مسئولیت‌ها، پیشنهاد PoC |

## قراردادهای نگارشی

- متن توضیحی به فارسی؛ نام فایل‌ها، شناسه‌ها، نام ماژول‌ها/کلاس‌ها/APIها و اصطلاحات استاندارد فنی به انگلیسی.
- برچسب‌ها: **[واقعیت]** واقعیت تأییدشده، **[فرض A-xx]** فرض موقت، **[فرضیه]** فرضیهٔ نیازمند آزمایش، **[توصیه]** پیشنهاد معماری، **[باز Q-xx]** وابسته به پرسش باز.
- الگوی شناسه‌ها: `FR-<AREA>-NNN`، `NFR-<AREA>-NNN`، `ADR-00NN`، `EP-NN`، `US-NN-NN`، `T-NN-NN`، `Q-NN`، `A-NN`، `R-NN`، `BM-NN`، `EXP-NN`، `PoC-N`، `M0..M6` (به‌علاوهٔ `M0.5`)، گیت‌ها `G0..G4` و `G-PoC`؛ فاز Evidence PoC همه‌جا «Phase 0.5» / «فاز 0.5» نامیده می‌شود.
- برچسب‌های داخل نمودارهای Mermaid به انگلیسی هستند، چون نمایش راست‌به‌چپ در Mermaid قابل اتکا نیست.
