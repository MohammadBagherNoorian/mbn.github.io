# مجموعه ADR برای آمادگی آزمون معماری سیستم

این فایل شامل مجموعه‌ای از ADRهای پرتکرار در حوزه Quality Attributes و Non-Functional Requirements است.  
ساختار هر ADR:

- کانتکست
- تصمیم
- راه‌حل جایگزین
- پیامد مثبت
- پیامد منفی

---

# 1. Security — احراز هویت کاربران

## کانتکست
سیستم دارای کاربران مختلف است و باید هویت هر کاربر پیش از دسترسی به منابع سیستم تأیید شود. دسترسی بدون احراز هویت می‌تواند باعث سوءاستفاده، افشای اطلاعات یا تغییر غیرمجاز داده‌ها شود.

## تصمیم
استفاده از یک مکانیزم احراز هویت متمرکز مانند OAuth 2.0 / OpenID Connect و صدور Token برای کاربران. اعتبار Token در سمت سرور بررسی می‌شود.

## راه‌حل جایگزین
استفاده از Session سنتی و نگهداری Session روی سرور.

## پیامد مثبت
- جداسازی Authentication از Business Logic
- مناسب برای معماری توزیع‌شده
- امکان Single Sign-On
- مدیریت متمرکز هویت کاربران

## پیامد منفی
- پیچیدگی بیشتر نسبت به Login ساده
- نیاز به مدیریت Token و Expiration
- وابستگی به Identity Provider

---

# 2. Security — کنترل دسترسی مبتنی بر نقش

## کانتکست
کاربران و تیم‌های مختلف نباید به همه منابع و عملیات سیستم دسترسی داشته باشند.

## تصمیم
استفاده از RBAC و تعریف Role و Permission. بررسی مجوز در سمت Backend انجام شود.

## راه‌حل جایگزین
استفاده از ABAC بر اساس Attributeهای کاربر، منبع و شرایط درخواست.

## پیامد مثبت
- کنترل دقیق دسترسی
- مدیریت ساده‌تر مجوزها
- امکان Audit
- کاهش دسترسی غیرمجاز

## پیامد منفی
- نیاز به مدیریت Roleها
- افزایش پیچیدگی در سیستم‌های بزرگ
- امکان ایجاد Role Explosion

---

# 3. Security — رمزنگاری داده در حال انتقال

## کانتکست
ارتباط بین Clientها و سرویس‌ها ممکن است از شبکه‌های ناامن عبور کند.

## تصمیم
استفاده از TLS برای تمام ارتباطات خارجی و ارتباطات حساس بین سرویس‌ها.

## راه‌حل جایگزین
ارسال داده بدون رمزنگاری در شبکه داخلی.

## پیامد مثبت
- جلوگیری از شنود
- جلوگیری از دستکاری داده
- افزایش محرمانگی

## پیامد منفی
- سربار پردازشی
- نیاز به مدیریت Certificate
- پیچیدگی بیشتر در Renewal و Rotation

---

# 4. Security — رمزنگاری داده در حالت ذخیره

## کانتکست
در صورت دسترسی غیرمجاز به Storage یا Database، اطلاعات حساس نباید قابل خواندن باشند.

## تصمیم
استفاده از Encryption at Rest در سطح Database، Disk یا Object Storage.

## راه‌حل جایگزین
رمزنگاری داده در Application قبل از ذخیره.

## پیامد مثبت
- حفاظت از داده در صورت سرقت Storage
- کاهش ریسک افشای اطلاعات
- تطبیق بهتر با الزامات امنیتی

## پیامد منفی
- پیچیدگی مدیریت کلید
- سربار پردازشی
- دشوارتر شدن برخی عملیات روی داده رمزنگاری‌شده

---

# 5. Security — مدیریت کلیدهای رمزنگاری

## کانتکست
کلیدهای رمزنگاری نباید داخل Source Code یا فایل‌های عادی Configuration نگهداری شوند.

## تصمیم
استفاده از KMS یا Secret Management برای ایجاد، نگهداری و Rotation کلیدها.

## راه‌حل جایگزین
نگهداری کلید در Environment Variable ساده یا فایل محلی.

## پیامد مثبت
- کاهش احتمال افشای کلید
- امکان Rotation
- Audit بهتر
- مدیریت متمرکز

## پیامد منفی
- وابستگی به سرویس مدیریت کلید
- افزایش پیچیدگی عملیاتی
- نیاز به طراحی فرآیند Recovery برای کلیدها

---

# 6. Security — مدیریت Secretها

## کانتکست
سیستم شامل Password، API Key و Tokenهای حساس است.

## تصمیم
Secretها در Secret Manager نگهداری شوند و در زمان اجرا به سرویس تزریق شوند.

## راه‌حل جایگزین
قرار دادن Secretها در فایل Configuration یا Repository.

## پیامد مثبت
- کاهش احتمال نشت Secret
- امکان Rotation
- کنترل دسترسی بهتر

## پیامد منفی
- وابستگی به Secret Manager
- پیچیدگی Deployment
- نیاز به مدیریت Permission دقیق

---

# 7. Recoverability — Disaster Recovery با RTO و RPO

## کانتکست
در صورت وقوع Disaster، سیستم باید حداکثر ظرف 15 دقیقه عملیاتی شود و حداکثر 5 دقیقه داده از دست برود.

## تصمیم
طراحی DR با Replication، Backup، Point-in-Time Recovery و Automated Failover.

## راه‌حل جایگزین
بازیابی دستی صرفاً از Backup دوره‌ای.

## پیامد مثبت
- کاهش Downtime
- کاهش Data Loss
- رعایت RTO و RPO
- کاهش Business Impact

## پیامد منفی
- هزینه زیرساخت بیشتر
- پیچیدگی بالاتر
- نیاز به تست منظم DR

---

# 8. Recoverability — Backup Strategy

## کانتکست
خرابی نرم‌افزاری، خطای انسانی یا Corruption ممکن است داده‌ها را از بین ببرد.

## تصمیم
استفاده از Full Backup دوره‌ای به همراه Incremental Backup یا WAL/PITR.

## راه‌حل جایگزین
فقط Full Backup روزانه.

## پیامد مثبت
- Recovery دقیق‌تر
- کاهش Data Loss
- انعطاف بیشتر در بازگشت به نقطه زمانی مشخص

## پیامد منفی
- پیچیدگی مدیریت Backup
- نیاز به Storage بیشتر
- نیاز به تست Restore

---

# 9. Recoverability — Multi-Region Recovery

## کانتکست
ممکن است یک Region کامل از دسترس خارج شود.

## تصمیم
نگهداری Replica یا Standby در Region دیگر و تعریف فرآیند Failover.

## راه‌حل جایگزین
استفاده از یک Region و Backup خارج از Region.

## پیامد مثبت
- تحمل خرابی Region
- Recovery سریع‌تر
- کاهش ریسک Single Region Failure

## پیامد منفی
- هزینه بیشتر
- پیچیدگی Consistency
- Latency احتمالی Replication

---

# 10. Availability — حذف Single Point of Failure

## کانتکست
خرابی یک سرور نباید کل سیستم را از دسترس خارج کند.

## تصمیم
استفاده از چند Instance برای اجزای حیاتی و Load Balancer برای توزیع ترافیک.

## راه‌حل جایگزین
استفاده از یک Instance قوی‌تر.

## پیامد مثبت
- افزایش Availability
- کاهش اثر خرابی Node
- امکان Maintenance بدون توقف کامل

## پیامد منفی
- هزینه بیشتر
- پیچیدگی زیرساخت
- نیاز به مدیریت State

---

# 11. Availability — Health Check و Failover

## کانتکست
Instanceهای خراب نباید ترافیک دریافت کنند.

## تصمیم
تعریف Liveness و Readiness Check و حذف خودکار Instance ناسالم از Load Balancer.

## راه‌حل جایگزین
تشخیص خرابی به صورت دستی.

## پیامد مثبت
- Recovery سریع
- کاهش خطای کاربر
- Automation بهتر

## پیامد منفی
- Health Check اشتباه می‌تواند سرویس سالم را حذف کند
- نیاز به تنظیم Threshold مناسب

---

# 12. Robustness — Circuit Breaker

## کانتکست
خرابی یک Dependency ممکن است باعث ایجاد Cascade Failure شود.

## تصمیم
استفاده از Circuit Breaker در تماس‌های Remote.

## راه‌حل جایگزین
Retry نامحدود یا ادامه ارسال درخواست.

## پیامد مثبت
- جلوگیری از انتشار خرابی
- کاهش فشار روی سرویس خراب
- بهبود Recovery

## پیامد منفی
- پیچیدگی Configuration
- نیاز به تنظیم Timeout و Threshold
- احتمال Reject شدن درخواست سالم در زمان Open بودن Circuit

---

# 13. Robustness — Retry با Backoff

## کانتکست
برخی خطاهای شبکه موقتی هستند.

## تصمیم
برای خطاهای موقت از Retry محدود همراه با Exponential Backoff و Jitter استفاده شود.

## راه‌حل جایگزین
عدم Retry یا Retry فوری و نامحدود.

## پیامد مثبت
- افزایش موفقیت درخواست‌های موقتاً شکست‌خورده
- کاهش فشار ناگهانی
- تحمل بهتر خطاهای transient

## پیامد منفی
- افزایش Latency
- امکان Retry Storm در طراحی ضعیف
- نیاز به Idempotency

---

# 14. Scalability — Horizontal Scaling

## کانتکست
تعداد کاربران و درخواست‌ها ممکن است افزایش پیدا کند.

## تصمیم
سرویس‌ها Stateless طراحی شوند و با اضافه کردن Instance جدید به صورت افقی Scale شوند.

## راه‌حل جایگزین
Vertical Scaling.

## پیامد مثبت
- مقیاس‌پذیری بهتر
- کاهش محدودیت سخت‌افزاری
- افزایش Availability

## پیامد منفی
- پیچیدگی Distributed System
- مدیریت State سخت‌تر
- هزینه Monitoring بیشتر

---

# 15. Scalability — Auto Scaling

## کانتکست
Load سیستم در طول زمان ثابت نیست.

## تصمیم
تعداد Instanceها بر اساس CPU، Memory، Queue Length یا Request Rate به‌صورت خودکار تنظیم شود.

## راه‌حل جایگزین
تعداد ثابت Instanceها.

## پیامد مثبت
- استفاده بهینه از منابع
- پاسخ مناسب به Spike
- کاهش هزینه در زمان کم‌باری

## پیامد منفی
- نیاز به Metric مناسب
- تأخیر در Scale Up
- احتمال Flapping

---

# 16. Scalability — استفاده از Cache

## کانتکست
خواندن مکرر داده از Database باعث افزایش Load و Latency می‌شود.

## تصمیم
استفاده از Cache برای داده‌های پرتکرار و کم‌تغییر.

## راه‌حل جایگزین
تمام درخواست‌ها مستقیماً به Database ارسال شوند.

## پیامد مثبت
- کاهش Latency
- کاهش Load دیتابیس
- افزایش Throughput

## پیامد منفی
- Cache Invalidation پیچیده است
- احتمال داده قدیمی
- نیاز به مدیریت Eviction

---

# 17. Scalability — Queue برای پردازش Async

## کانتکست
برخی عملیات زمان‌بر هستند و نباید Request اصلی را Block کنند.

## تصمیم
ارسال Task به Message Queue و پردازش Async توسط Workerها.

## راه‌حل جایگزین
پردازش Synchronous داخل Request.

## پیامد مثبت
- افزایش Throughput
- کنترل بهتر Spike
- امکان Scale مستقل Worker

## پیامد منفی
- Eventual Consistency
- پیچیدگی Debug
- نیاز به Idempotency

---

# 18. Performance — Cache برای کاهش Latency

## کانتکست
کاربران انتظار پاسخ سریع دارند و دسترسی مکرر به داده ثابت باعث کندی سیستم می‌شود.

## تصمیم
استفاده از Cache در مسیرهای پرتکرار.

## راه‌حل جایگزین
بهینه‌سازی صرف Database Query.

## پیامد مثبت
- پاسخ سریع‌تر
- Load کمتر

## پیامد منفی
- پیچیدگی Consistency
- استفاده از Memory اضافی

---

# 19. Performance — Database Indexing

## کانتکست
Queryهای پرتکرار روی جدول‌های بزرگ کند شده‌اند.

## تصمیم
ایجاد Index روی ستون‌هایی که در Filter، Join و Sort پرتکرار استفاده می‌شوند.

## راه‌حل جایگزین
افزایش منابع Database بدون تغییر Schema.

## پیامد مثبت
- بهبود سرعت Query
- کاهش زمان پاسخ

## پیامد منفی
- افزایش Storage
- کندتر شدن Write
- نیاز به نگهداری Index

---

# 20. Performance — CDN برای محتوای Static

## کانتکست
کاربران از نقاط جغرافیایی مختلف فایل‌های ثابت دریافت می‌کنند.

## تصمیم
استفاده از CDN برای Cache و توزیع Assetهای Static.

## راه‌حل جایگزین
ارسال تمام Assetها از Origin اصلی.

## پیامد مثبت
- Latency کمتر
- Load کمتر روی Origin
- مقیاس‌پذیری بهتر

## پیامد منفی
- هزینه CDN
- پیچیدگی Cache Invalidation

---

# 21. Modifiability — معماری Modular

## کانتکست
سیستم باید در آینده قابلیت‌های جدید دریافت کند.

## تصمیم
سیستم به ماژول‌های با مسئولیت مشخص و Coupling پایین تقسیم شود.

## راه‌حل جایگزین
ساختار شدیداً Coupled.

## پیامد مثبت
- توسعه آسان‌تر
- تست بهتر
- تغییرات محدودتر

## پیامد منفی
- طراحی اولیه بیشتر
- افزایش تعداد Abstractionها

---

# 22. Modifiability — استفاده از Interface و Contract

## کانتکست
تغییر Implementation یک بخش نباید وابسته‌ها را بشکند.

## تصمیم
وابستگی بین ماژول‌ها از طریق Interface و Contract پایدار انجام شود.

## راه‌حل جایگزین
وابستگی مستقیم به Implementation.

## پیامد مثبت
- Loose Coupling
- Testability بهتر
- امکان تعویض Implementation

## پیامد منفی
- پیچیدگی بیشتر
- Overengineering در پروژه کوچک

---

# 23. Extensibility — Plugin Architecture

## کانتکست
سیستم باید قابلیت اضافه شدن Featureهای مستقل را با کمترین تغییر در Core داشته باشد.

## تصمیم
تعریف Extension Point و Plugin Contract.

## راه‌حل جایگزین
اضافه کردن تمام Featureها مستقیم به Core.

## پیامد مثبت
- توسعه مستقل
- کاهش تغییرات Core
- امکان فعال/غیرفعال کردن Featureها

## پیامد منفی
- طراحی پیچیده‌تر
- Version Compatibility دشوارتر

---

# 24. Upgradability — Rolling Deployment

## کانتکست
سرویس باید بدون توقف کامل ارتقا پیدا کند.

## تصمیم
نسخه جدید به تدریج روی Instanceها Deploy شود.

## راه‌حل جایگزین
توقف کل سرویس و Deploy همزمان.

## پیامد مثبت
- Downtime بسیار کم
- استفاده از زیرساخت موجود
- انتشار تدریجی

## پیامد منفی
- همزمانی چند Version
- نیاز به Backward Compatibility

---

# 25. Upgradability — Blue-Green Deployment

## کانتکست
ریسک Deployment باید کم باشد و Rollback سریع نیاز است.

## تصمیم
دو محیط Blue و Green نگهداری شود و Traffic بین آن‌ها Switch شود.

## راه‌حل جایگزین
Rolling Deployment.

## پیامد مثبت
- Rollback بسیار سریع
- Downtime کم
- تست نسخه جدید قبل از Switch

## پیامد منفی
- هزینه دو محیط
- مدیریت Database Migration سخت‌تر

---

# 26. Upgradability — Canary Deployment

## کانتکست
نسخه جدید باید قبل از انتشار کامل روی بخش کوچکی از کاربران آزمایش شود.

## تصمیم
درصد کمی از Traffic به نسخه جدید هدایت شود و Metricها بررسی شوند.

## راه‌حل جایگزین
انتشار کامل نسخه برای همه کاربران.

## پیامد مثبت
- کاهش ریسک
- کشف خطا قبل از انتشار کامل
- امکان Rollback سریع

## پیامد منفی
- Routing پیچیده‌تر
- نیاز به Monitoring دقیق
- همزمانی Versionها

---

# 27. Upgradability — Backward Compatible API

## کانتکست
سرویس‌ها ممکن است مستقل از هم Deploy شوند.

## تصمیم
تغییرات API تا حد امکان Backward Compatible باشند و حذف Field یا Endpoint ناگهانی انجام نشود.

## راه‌حل جایگزین
Deploy همزمان تمام Consumer و Providerها.

## پیامد مثبت
- Deployment مستقل
- کاهش ریسک شکست سرویس‌ها
- Migration تدریجی

## پیامد منفی
- نگهداری نسخه‌های قدیمی
- پیچیدگی بیشتر Contract

---

# 28. Portability — Containerization

## کانتکست
سیستم باید روی Environmentهای مختلف قابل اجرا باشد.

## تصمیم
Application و Dependencyها در Container بسته‌بندی شوند.

## راه‌حل جایگزین
نصب مستقیم روی Host.

## پیامد مثبت
- رفتار یکسان در محیط‌ها
- نصب ساده‌تر
- کاهش مشکل Dependency

## پیامد منفی
- نیاز به Container Platform
- پیچیدگی Image Management

---

# 29. Portability — جداسازی وابستگی‌های Platform-Specific

## کانتکست
سیستم باید روی چند Platform اجرا شود.

## تصمیم
وابستگی‌های Platform-Specific پشت Adapter یا Abstraction قرار بگیرند.

## راه‌حل جایگزین
استفاده مستقیم از APIهای سیستم‌عامل.

## پیامد مثبت
- Portability بیشتر
- تست ساده‌تر
- تغییر Platform آسان‌تر

## پیامد منفی
- Abstraction اضافی
- توسعه بیشتر

---

# 30. Installability — نصب خودکار

## کانتکست
نصب دستی سیستم روی محیط‌های متعدد مستعد خطاست.

## تصمیم
فرآیند نصب با Script، Package Manager یا Container Automation شود.

## راه‌حل جایگزین
راهنمای نصب دستی.

## پیامد مثبت
- نصب تکرارپذیر
- کاهش خطای انسانی
- زمان نصب کمتر

## پیامد منفی
- نیاز به نگهداری Script
- اختلاف Environmentها می‌تواند Automation را پیچیده کند

---

# 31. Configurability — Externalized Configuration

## کانتکست
تنظیمات در Dev، Stage و Prod متفاوت هستند.

## تصمیم
Configuration از Source Code جدا شود و از Environment Variable یا Config Service استفاده شود.

## راه‌حل جایگزین
Hard-code کردن Configuration.

## پیامد مثبت
- Build یکسان برای Environmentهای مختلف
- تغییر سریع تنظیمات
- امنیت بهتر

## پیامد منفی
- Configuration Drift
- نیاز به Versioning تنظیمات

---

# 32. Configurability — Feature Flag

## کانتکست
برخی Featureها باید بدون Deploy مجدد فعال یا غیرفعال شوند.

## تصمیم
استفاده از Feature Flag برای کنترل Runtime Featureها.

## راه‌حل جایگزین
فعال‌سازی Feature فقط با Deploy نسخه جدید.

## پیامد مثبت
- Rollout تدریجی
- Rollback سریع
- A/B Testing

## پیامد منفی
- بدهی Flagهای قدیمی
- پیچیدگی Logic
- نیاز به Governance

---

# 33. Data Lifecycle — Retention Policy

## کانتکست
همه داده‌ها نباید برای همیشه نگهداری شوند.

## تصمیم
برای هر نوع داده مدت Retention مشخص شود و پس از آن Archive یا Delete شود.

## راه‌حل جایگزین
نگهداری دائمی همه داده‌ها.

## پیامد مثبت
- کاهش هزینه Storage
- کنترل بهتر حجم Database
- مدیریت بهتر داده

## پیامد منفی
- احتمال حذف اشتباه
- نیاز به فرآیند بازیابی
- پیچیدگی Policy

---

# 34. Data Lifecycle — Archive کردن داده سرد

## کانتکست
داده‌های قدیمی به ندرت استفاده می‌شوند ولی باید نگهداری شوند.

## تصمیم
انتقال داده‌های قدیمی به Cold Storage یا Archive Storage.

## راه‌حل جایگزین
نگهداری همه داده‌ها در Database اصلی.

## پیامد مثبت
- کاهش هزینه
- کاهش حجم Database اصلی
- Performance بهتر داده فعال

## پیامد منفی
- دسترسی کندتر
- Recovery پیچیده‌تر

---

# 35. Data Lifecycle — حذف امن داده

## کانتکست
برخی داده‌ها پس از پایان Retention باید واقعاً حذف شوند.

## تصمیم
فرآیند Secure Deletion همراه با ثبت Audit ایجاد شود.

## راه‌حل جایگزین
صرفاً علامت‌گذاری رکورد به عنوان Deleted.

## پیامد مثبت
- کاهش ریسک نگهداری غیرضروری داده
- رعایت بهتر الزامات Privacy
- Auditability

## پیامد منفی
- بازیابی دشوار یا غیرممکن
- پیچیدگی حذف از Replica و Backup

---

# 36. Maintainability — Logging ساختاریافته

## کانتکست
عیب‌یابی سیستم توزیع‌شده با Logهای نامنظم دشوار است.

## تصمیم
استفاده از Structured Logging همراه با Correlation ID.

## راه‌حل جایگزین
Log متنی آزاد.

## پیامد مثبت
- جستجوی بهتر
- Debug سریع‌تر
- تحلیل خودکار آسان‌تر

## پیامد منفی
- حجم Log بیشتر
- هزینه Storage و پردازش

---

# 37. Observability — Metrics

## کانتکست
تیم عملیاتی باید وضعیت سلامت و Performance سیستم را تشخیص دهد.

## تصمیم
جمع‌آوری Metricهای فنی و Business مانند Latency، Error Rate، Throughput و Resource Usage.

## راه‌حل جایگزین
اتکا فقط به Log.

## پیامد مثبت
- تشخیص سریع مشکل
- Alerting دقیق‌تر
- Capacity Planning بهتر

## پیامد منفی
- هزینه Monitoring
- نیاز به تعیین Metric مناسب

---

# 38. Observability — Distributed Tracing

## کانتکست
یک Request ممکن است از چند سرویس عبور کند و پیدا کردن منبع Latency دشوار باشد.

## تصمیم
استفاده از Distributed Tracing و Trace ID مشترک بین سرویس‌ها.

## راه‌حل جایگزین
بررسی دستی Log هر سرویس.

## پیامد مثبت
- مشاهده End-to-End Request
- کشف Bottleneck
- Debug سریع‌تر

## پیامد منفی
- سربار
- هزینه ذخیره Trace
- پیچیدگی Instrumentation

---

# 39. Reliability — Idempotency

## کانتکست
در شبکه ممکن است یک Request چند بار ارسال شود.

## تصمیم
برای عملیات حساس از Idempotency Key استفاده شود.

## راه‌حل جایگزین
پردازش هر Request بدون تشخیص تکرار.

## پیامد مثبت
- جلوگیری از عملیات تکراری
- مناسب برای Payment و Order
- Retry امن‌تر

## پیامد منفی
- نیاز به Storage برای Keyها
- پیچیدگی State Management

---

# 40. Reliability — Transactional Outbox

## کانتکست
سیستم باید هم Database را تغییر دهد و هم Event منتشر کند و نباید یکی موفق و دیگری شکست بخورد.

## تصمیم
استفاده از Transactional Outbox و انتشار Async Event از Outbox.

## راه‌حل جایگزین
نوشتن Database و ارسال Message به صورت دو عملیات مستقل.

## پیامد مثبت
- جلوگیری از Lost Event
- Consistency بهتر
- Reliability بالاتر

## پیامد منفی
- Eventual Consistency
- نیاز به Worker یا CDC
- پیچیدگی بیشتر

---

# 41. Reliability — Database Replication

## کانتکست
خرابی Database اصلی نباید باعث از دست رفتن کامل سرویس شود.

## تصمیم
استفاده از Replica و مکانیزم Failover.

## راه‌حل جایگزین
یک Database Instance با Backup.

## پیامد مثبت
- Availability بالاتر
- Recovery سریع‌تر
- امکان Read Scaling

## پیامد منفی
- Replication Lag
- هزینه بیشتر
- Failover Complexity

---

# 42. Interoperability — API استاندارد

## کانتکست
سیستم باید با سرویس‌های خارجی و داخلی مختلف Integrate شود.

## تصمیم
استفاده از APIهای استاندارد مانند REST/HTTP یا gRPC با Contract مشخص.

## راه‌حل جایگزین
Integration مستقیم و اختصاصی برای هر سیستم.

## پیامد مثبت
- Integration ساده‌تر
- Documentation بهتر
- کاهش Coupling

## پیامد منفی
- نیاز به Versioning
- مدیریت Contract
- محدودیت‌های Protocol انتخاب‌شده

---

# 43. Interoperability — Adapter برای سیستم‌های خارجی

## کانتکست
Providerهای خارجی APIهای متفاوتی دارند.

## تصمیم
برای هر Provider یک Adapter پیاده‌سازی شود و Core سیستم به Interface مشترک وابسته باشد.

## راه‌حل جایگزین
قرار دادن Logic هر Provider داخل Core.

## پیامد مثبت
- جداسازی Integration
- تعویض Provider آسان‌تر
- Testability بهتر

## پیامد منفی
- تعداد Component بیشتر
- نگهداری Adapterها

---

# 44. Testability — Dependency Injection

## کانتکست
تست واحد زمانی دشوار می‌شود که Componentها Dependencyهای واقعی را مستقیماً ایجاد کنند.

## تصمیم
Dependencyها از بیرون Inject شوند.

## راه‌حل جایگزین
ساخت Dependency داخل کلاس.

## پیامد مثبت
- Unit Test ساده‌تر
- Mock کردن Dependency
- Loose Coupling

## پیامد منفی
- Configuration بیشتر
- پیچیدگی اولیه برای پروژه کوچک

---

# 45. Auditability — ثبت Audit Log

## کانتکست
برای عملیات حساس باید مشخص باشد چه کسی چه تغییری در چه زمانی انجام داده است.

## تصمیم
Audit Log غیرقابل‌تغییر برای عملیات مهم ثبت شود.

## راه‌حل جایگزین
استفاده فقط از Application Log.

## پیامد مثبت
- Traceability
- بررسی Incident
- امنیت و Compliance بهتر

## پیامد منفی
- افزایش Storage
- نیاز به حفاظت از Audit Log
- مدیریت Retention

---

# 46. Consistency — Strong Consistency برای عملیات مالی

## کانتکست
در عملیات مالی، مشاهده داده قدیمی یا ثبت دوباره تراکنش قابل قبول نیست.

## تصمیم
برای Core Transaction از Transaction دیتابیس و Consistency قوی استفاده شود.

## راه‌حل جایگزین
Eventual Consistency برای تمام عملیات.

## پیامد مثبت
- صحت بیشتر
- جلوگیری از وضعیت‌های ناسازگار
- مناسب برای Balance و Payment

## پیامد منفی
- Scalability محدودتر
- Latency بیشتر
- Coupling بیشتر به Database Transaction

---

# 47. Consistency — Eventual Consistency برای فرآیندهای غیرحساس

## کانتکست
برخی داده‌ها لازم نیست بلافاصله در همه بخش‌ها یکسان شوند.

## تصمیم
استفاده از Event-Driven Architecture و Eventual Consistency.

## راه‌حل جایگزین
Distributed Transaction.

## پیامد مثبت
- Scalability بیشتر
- Coupling کمتر
- Availability بهتر

## پیامد منفی
- داده موقتاً ناسازگار
- Debug پیچیده‌تر
- نیاز به Compensation

---

# 48. Availability — Graceful Degradation

## کانتکست
خرابی یک قابلیت فرعی نباید Core سیستم را از دسترس خارج کند.

## تصمیم
در صورت خرابی سرویس غیرحیاتی، سیستم با قابلیت محدود ادامه کار دهد.

## راه‌حل جایگزین
Fail کردن کل Request.

## پیامد مثبت
- تجربه کاربری بهتر
- Availability بیشتر
- کاهش Cascade Failure

## پیامد منفی
- Logic پیچیده‌تر
- نیاز به تعریف Fallback
- احتمال ارائه داده ناقص

---

# 49. Deployability — Database Migration بدون Downtime

## کانتکست
تغییر Schema نباید باعث توقف Application شود.

## تصمیم
Migrationها با الگوی Expand and Contract انجام شوند.

## راه‌حل جایگزین
تغییر Breaking Schema و Deploy همزمان.

## پیامد مثبت
- Zero/Low Downtime
- سازگاری چند Version
- Rollback ساده‌تر

## پیامد منفی
- Migration چندمرحله‌ای
- نیاز به نگهداری موقت ستون یا Schema قدیمی

---

# 50. Scalability — Partitioning / Sharding

## کانتکست
حجم داده از ظرفیت یک Database Instance بیشتر می‌شود.

## تصمیم
تقسیم داده بر اساس Shard Key مناسب.

## راه‌حل جایگزین
Vertical Scaling Database.

## پیامد مثبت
- افزایش ظرفیت
- توزیع Load
- امکان Scale افقی داده

## پیامد منفی
- Queryهای Cross-Shard سخت‌تر
- Rebalancing پیچیده
- Transaction توزیع‌شده دشوار

---

# الگوی سریع پاسخ در آزمون

برای هر سؤال این ترتیب را رعایت کن:

1. **کانتکست:** مشکل و محدودیت Business/Technical را توضیح بده.
2. **تصمیم:** تصمیم معماری مشخص و مستقیم بده.
3. **راه‌حل جایگزین:** یک گزینه منطقی دیگر ذکر کن.
4. **پیامد مثبت:** مزایای مرتبط با Quality Attribute را بگو.
5. **پیامد منفی:** Trade-off واقعی تصمیم را بگو.

## الگوی ذهنی

Requirement → Quality Attribute → Architectural Tactic → Alternative → Trade-off

مثال:

«سیستم باید در 15 دقیقه برگردد و حداکثر 5 دقیقه داده از دست برود»

→ Recoverability  
→ RTO = 15 min / RPO = 5 min  
→ Replication + Backup + PITR + Failover  
→ Alternative: Restore دستی از Backup  
→ Trade-off: هزینه و پیچیدگی بیشتر
