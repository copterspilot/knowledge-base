# User

Для учебного курса System Design компании OTUS нужно подготовить занятие на 90 минут на тему "Кэширование и хранение данных"
Требуется:
- определить актуальные на 2025 концепции кэширования для различных систем
- определить наиболее актуальные на 2025 год цели занятия, предложить несколько вариантов исходя из обсуждаемости в интернете
- список компетенций которые слушатели получат в результате занятия
- составить план занятия с ключевыми тезисами

---
# ChatGPT

Отлично, давай соберём 90-минутное занятие «Кэширование и хранение данных» с прицелом на практику и то, что реально актуально в 2025 году.

# Концепции кэширования (актуальные на 2025)

**Системный уровень**

- **Кэширование в БД**:
    
    - _InnoDB buffer pool_ (MySQL) как главный RAM-кэш страниц таблиц/индексов; тюнинг размера и многопуловость для многопоточности. ([MySQL](https://dev.mysql.com/doc/refman/8.4/en/innodb-buffer-pool.html?utm_source=chatgpt.com "MySQL 8.4 Reference Manual :: 17.5.1 Buffer Pool"))
        
    - _PostgreSQL shared_buffers_ + анализ через `pg_buffercache`/`pg_prewarm` для прогрева. ([PostgreSQL](https://www.postgresql.org/docs/current/pgbuffercache.html?utm_source=chatgpt.com "17: F.25. pg_buffercache — inspect PostgreSQL buffer cache state"))
        
- **ОС/дисковый кэш**: взаимодействие page cache ОС с буферами СУБД (коротко, без углубления).
    

**Прикладной уровень**

- **Шаблоны кэширования**: cache-aside, read-through, write-through, write-behind, refresh-ahead.
    
- **Политики устаревания**: TTL/TTI, _stale-while-revalidate_ и _stale-if-error_ (HTTP/RFC 9111 + расширения; диагностика через `Cache-Status`). ([IETF Datatracker](https://datatracker.ietf.org/doc/rfc9111/?utm_source=chatgpt.com "RFC 9111 - HTTP Caching - IETF Datatracker"))
    
- **Избежание «cache stampede»**: коалесcация запросов (Go `singleflight`), джиттер/пробабилистический рефреш, mutex-lock/семафоры, «догрэв» (refresh-ahead). ([Medium](https://medium.com/pickme-engineering-blog/singleflight-in-go-a-clean-solution-to-cache-stampede-02acaf5818e3?utm_source=chatgpt.com "Singleflight in Go : A Clean Solution to Cache Stampede - Medium"), [newsletter.adaptiveengineer.com](https://newsletter.adaptiveengineer.com/p/cache-stampede-a-problem-the-industry?utm_source=chatgpt.com "Cache Stampede: A Problem The Industry Fights Every Day"))
    
- **Политики вытеснения/допуска**: LRU/LFU, _W-TinyLFU_ (Caffeine) как best-of-breed для high-QPS кэшей. ([arXiv](https://arxiv.org/pdf/1512.00727?utm_source=chatgpt.com "[PDF] TinyLFU: A Highly Efficient Cache Admission Policy - arXiv"), [GitHub](https://github.com/ben-manes/caffeine/wiki/Efficiency?utm_source=chatgpt.com "Efficiency · ben-manes/caffeine Wiki - GitHub"))
    
- **Негативное кэширование** (404/пустые ответы), **версионирование ключей** (tagging), **warm-up**/**prewarm**.
    
- **Клиентское/браузерное кэширование**: `Cache-Control`, `ETag`, различия private vs shared caches; interplay `max-age/s-maxage` со SWR. ([IETF Datatracker](https://datatracker.ietf.org/doc/rfc9111/?utm_source=chatgpt.com "RFC 9111 - HTTP Caching - IETF Datatracker"), [Stack Overflow](https://stackoverflow.com/questions/72642866/how-does-stale-while-revalidate-interact-with-s-maxage-in-cache-control-header?utm_source=chatgpt.com "How does stale-while-revalidate interact with s-maxage in Cache ..."))
    

**Сетевой/edge-уровень**

- **CDN и edge-кэширование**: Fastly/Akamai/Cloudflare (SWR/SIE, tiered, purge API, Cache Reserve, инциденты как фактор SRE-практик). ([Fastly](https://www.fastly.com/documentation/guides/concepts/edge-state/cache/?utm_source=chatgpt.com "Caching content with Fastly | Fastly Documentation"), [Akamai Tech Docs](https://techdocs.akamai.com/property-mgr/docs/know-caching?utm_source=chatgpt.com "Learn about Akamai's caching"), [Cloudflare Docs](https://developers.cloudflare.com/cache/advanced-configuration/cache-reserve/?utm_source=chatgpt.com "Cache Reserve · Cloudflare Cache (CDN) docs"), [The Cloudflare Blog](https://blog.cloudflare.com/cloudflare-incident-march-21-2025/?utm_source=chatgpt.com "Cloudflare incident on March 21, 2025"))
    

**Новые тренды**

- **Redis-экосистема после изменения лицензии**: появление _Valkey_ (Linux Foundation) как BSD-форка Redis 7.2; в 2025 Redis 8 снова открыт (AGPL). Это влияет на выбор движка/совместимость. ([Linux Foundation](https://www.linuxfoundation.org/press/linux-foundation-launches-open-source-valkey-community?utm_source=chatgpt.com "Linux Foundation Launches Open Source Valkey Community"), [The Stack](https://www.thestack.technology/redis-reverts-to-open-source/?utm_source=chatgpt.com "Redis reverts to open-source - The Stack"))
    
- **Альтернативы Redis**: _Dragonfly_ (multi-threaded, drop-in Redis/Memcached API) — сильный кандидат для high-throughput кэшей. ([GitHub](https://github.com/dragonflydb/dragonfly?utm_source=chatgpt.com "dragonflydb/dragonfly: A modern replacement for Redis ... - GitHub"))
    
- **Семантический кэш для LLM/RAG**: кэширование по близости эмбеддингов (vector similarity). Новые фичи Redis 2025: _Vector Sets_ и управляемый _LangCache_; интеграции на уровне гейтвеев. ([The New Stack](https://thenewstack.io/redis-launches-vector-sets-and-a-new-tool-for-semantic-caching-of-llm-responses/?utm_source=chatgpt.com "Redis Launches Vector Sets and a New Tool for Semantic Caching ..."), [Redis](https://redis.io/blog/spring-release-2025/?utm_source=chatgpt.com "Introducing LangCache and vector sets, simple solutions for high ..."))
    

---

# Варианты целей занятия (под разные акценты на 2025)

1. **Практик-инженерия кэшей для высоконагруженных API**  
    Научиться проектировать многоуровневый кэш (app + CDN + DB) с SWR/SIE, коалесcацией запросов и W-TinyLFU, с измеримым снижением p95-латентности и нагрузки на origin. ([IETF Datatracker](https://datatracker.ietf.org/doc/rfc9111/?utm_source=chatgpt.com "RFC 9111 - HTTP Caching - IETF Datatracker"), [Medium](https://medium.com/pickme-engineering-blog/singleflight-in-go-a-clean-solution-to-cache-stampede-02acaf5818e3?utm_source=chatgpt.com "Singleflight in Go : A Clean Solution to Cache Stampede - Medium"), [arXiv](https://arxiv.org/pdf/1512.00727?utm_source=chatgpt.com "[PDF] TinyLFU: A Highly Efficient Cache Admission Policy - arXiv"))
    
2. **Выбор движка в новой реальности Redis/Valkey/Dragonfly**  
    Понять компромиссы лицензий, совместимости и производительности; собрать критерии миграции с Redis OSS → Valkey/Dragonfly. ([Linux Foundation](https://www.linuxfoundation.org/press/linux-foundation-launches-open-source-valkey-community?utm_source=chatgpt.com "Linux Foundation Launches Open Source Valkey Community"), [GitHub](https://github.com/dragonflydb/dragonfly?utm_source=chatgpt.com "dragonflydb/dragonfly: A modern replacement for Redis ... - GitHub"))
    
3. **Edge-кэширование и «устойчивые» веб-сервисы**  
    Сконфигурировать CDN-слой (purge, tiered, Cache Reserve, SWR) и отработать инцидент-плейбуки на примере реальных событий 2025. ([Cloudflare Docs](https://developers.cloudflare.com/cache/advanced-configuration/cache-reserve/?utm_source=chatgpt.com "Cache Reserve · Cloudflare Cache (CDN) docs"), [The Cloudflare Blog](https://blog.cloudflare.com/cloudflare-incident-march-21-2025/?utm_source=chatgpt.com "Cloudflare incident on March 21, 2025"))
    
4. **Кэширование для GenAI/LLM**  
    Реализовать семантический кэш (embeddings + порог сходства) и измерить экономию токенов/латентности; понять edge-cases инвалидации. ([The New Stack](https://thenewstack.io/redis-launches-vector-sets-and-a-new-tool-for-semantic-caching-of-llm-responses/?utm_source=chatgpt.com "Redis Launches Vector Sets and a New Tool for Semantic Caching ..."), [Redis](https://redis.io/docs/latest/develop/ai/redisvl/api/cache/?utm_source=chatgpt.com "LLM Cache | Docs - Redis"))
    

> Выбери одну как основную, две — как «расширение» (в зависимости от уровня группы и домашнего задания).

---

# Компетенции слушателей после занятия

- Проектирование многоуровневой стратегии кэширования (клиент → CDN/edge → приложение → БД).
    
- Применение SWR/SIE и диагностики `Cache-Status` по RFC 9111/9211. ([IETF Datatracker](https://datatracker.ietf.org/doc/rfc9111/?utm_source=chatgpt.com "RFC 9111 - HTTP Caching - IETF Datatracker"))
    
- Предотвращение cache stampede (locking, request coalescing, jitter). ([Medium](https://medium.com/pickme-engineering-blog/singleflight-in-go-a-clean-solution-to-cache-stampede-02acaf5818e3?utm_source=chatgpt.com "Singleflight in Go : A Clean Solution to Cache Stampede - Medium"))
    
- Выбор и тюнинг политик вытеснения (в т.ч. W-TinyLFU/Caffeine). ([GitHub](https://github.com/ben-manes/caffeine/wiki/Efficiency?utm_source=chatgpt.com "Efficiency · ben-manes/caffeine Wiki - GitHub"))
    
- Тюнинг кэшей на уровне СУБД (InnoDB buffer pool, Pg shared_buffers, prewarm). ([MySQL](https://dev.mysql.com/doc/refman/8.4/en/innodb-buffer-pool.html?utm_source=chatgpt.com "MySQL 8.4 Reference Manual :: 17.5.1 Buffer Pool"), [PostgreSQL](https://www.postgresql.org/docs/current/pgbuffercache.html?utm_source=chatgpt.com "17: F.25. pg_buffercache — inspect PostgreSQL buffer cache state"))
    
- Осознанный выбор кэширующего движка в 2025 (Redis/Valkey/Dragonfly) и сценарии миграции. ([Linux Foundation](https://www.linuxfoundation.org/press/linux-foundation-launches-open-source-valkey-community?utm_source=chatgpt.com "Linux Foundation Launches Open Source Valkey Community"), [GitHub](https://github.com/dragonflydb/dragonfly?utm_source=chatgpt.com "dragonflydb/dragonfly: A modern replacement for Redis ... - GitHub"))
    
- Основы семантического кэширования для LLM/RAG. ([The New Stack](https://thenewstack.io/redis-launches-vector-sets-and-a-new-tool-for-semantic-caching-of-llm-responses/?utm_source=chatgpt.com "Redis Launches Vector Sets and a New Tool for Semantic Caching ..."))
    

---

# План 90-минутного занятия (аудитория: инженеры/архитекторы; формат — мини-лекция + live-разбор)

**0–5 мин — Разогрев**  
Кейс: «падение» API из-за истечения TTL одной горячей записи → что пошло не так?

**5–20 мин — Базовые строительные блоки**

- Уровни кэширования и «источники правды».
    
- Шаблоны (cache-aside/read-through/write-through/write-behind/refresh-ahead).
    
- Настройка TTL/TTI, негативный кэш, версии ключей.
    

**20–35 мин — HTTP/Edge слой (что реально работает на проде в 2025)**

- Семантика RFC 9111: `Cache-Control`, revalidation, `ETag`, `Vary`.
    
- _stale-while-revalidate_ и _stale-if-error_ + `Cache-Status` (диагностика CDN/прокси). Практики Fastly/Akamai; что умеет Cloudflare (Cache Reserve, изменения purge в 2025). ([IETF Datatracker](https://datatracker.ietf.org/doc/rfc9111/?utm_source=chatgpt.com "RFC 9111 - HTTP Caching - IETF Datatracker"), [Fastly](https://www.fastly.com/documentation/guides/concepts/edge-state/cache/?utm_source=chatgpt.com "Caching content with Fastly | Fastly Documentation"), [Akamai Tech Docs](https://techdocs.akamai.com/property-mgr/docs/know-caching?utm_source=chatgpt.com "Learn about Akamai's caching"), [Cloudflare Docs](https://developers.cloudflare.com/cache/advanced-configuration/cache-reserve/?utm_source=chatgpt.com "Cache Reserve · Cloudflare Cache (CDN) docs"))
    
- Ключи кэша и нормализация URL/заголовков.
    

**35–50 мин — Борьба с cache stampede**

- Проблема и её проявления в метриках.
    
- Техники: request coalescing (`singleflight`), распределённые локи, jitter, probabilistic early refresh. Демка псевдокода. ([Medium](https://medium.com/pickme-engineering-blog/singleflight-in-go-a-clean-solution-to-cache-stampede-02acaf5818e3?utm_source=chatgpt.com "Singleflight in Go : A Clean Solution to Cache Stampede - Medium"))
    

**50–62 мин — Политики вытеснения и эффективность**

- Почему одного LRU мало; hit-rate vs память.
    
- _W-TinyLFU_ (идея «допуска» + оконная область), где взять готовое (Caffeine, аналоги). Короткий пример интеграции в Java/Go. ([arXiv](https://arxiv.org/pdf/1512.00727?utm_source=chatgpt.com "[PDF] TinyLFU: A Highly Efficient Cache Admission Policy - arXiv"), [GitHub](https://github.com/ben-manes/caffeine/wiki/Efficiency?utm_source=chatgpt.com "Efficiency · ben-manes/caffeine Wiki - GitHub"))
    

**62–72 мин — Кэши в БД: что тюнить, что не трогать**

- MySQL: размер/инстансы buffer pool; Postgres: `shared_buffers`, `pg_buffercache`, `pg_prewarm`. Когда лучше отдать RAM ОС кэшу. ([MySQL](https://dev.mysql.com/doc/refman/8.4/en/innodb-buffer-pool.html?utm_source=chatgpt.com "MySQL 8.4 Reference Manual :: 17.5.1 Buffer Pool"), [PostgreSQL](https://www.postgresql.org/docs/current/pgbuffercache.html?utm_source=chatgpt.com "17: F.25. pg_buffercache — inspect PostgreSQL buffer cache state"))
    

**72–82 мин — Что выбрать в 2025: Redis, Valkey, Dragonfly**

- Лицензии и совместимость; когда выгодна многопоточность Dragonfly; риски и миграция. ([Linux Foundation](https://www.linuxfoundation.org/press/linux-foundation-launches-open-source-valkey-community?utm_source=chatgpt.com "Linux Foundation Launches Open Source Valkey Community"), [GitHub](https://github.com/dragonflydb/dragonfly?utm_source=chatgpt.com "dragonflydb/dragonfly: A modern replacement for Redis ... - GitHub"))
    

**82–90 мин — Бонус-блок: семантический кэш для LLM/RAG**

- Идея: эмбеддинги, порог сходства, экономия токенов и p95.
    
- Новинки Redis 2025: _Vector Sets_ + _LangCache_; варианты интеграции. Домашнее задание-мини: прототип на любом стеке. ([The New Stack](https://thenewstack.io/redis-launches-vector-sets-and-a-new-tool-for-semantic-caching-of-llm-responses/?utm_source=chatgpt.com "Redis Launches Vector Sets and a New Tool for Semantic Caching ..."), [Redis](https://redis.io/blog/spring-release-2025/?utm_source=chatgpt.com "Introducing LangCache and vector sets, simple solutions for high ..."))
    

---

## Материалы/ссылки для слайдов и подготовки демо

- RFC 9111 (HTTP caching) и RFC 9211 (`Cache-Status`). ([IETF Datatracker](https://datatracker.ietf.org/doc/rfc9111/?utm_source=chatgpt.com "RFC 9111 - HTTP Caching - IETF Datatracker"))
    
- InnoDB buffer pool / Pg `pg_buffercache`/`pg_prewarm`. ([MySQL](https://dev.mysql.com/doc/refman/8.4/en/innodb-buffer-pool.html?utm_source=chatgpt.com "MySQL 8.4 Reference Manual :: 17.5.1 Buffer Pool"), [PostgreSQL](https://www.postgresql.org/docs/current/pgbuffercache.html?utm_source=chatgpt.com "17: F.25. pg_buffercache — inspect PostgreSQL buffer cache state"))
    
- W-TinyLFU (оригинальная работа + заметки Caffeine). ([arXiv](https://arxiv.org/pdf/1512.00727?utm_source=chatgpt.com "[PDF] TinyLFU: A Highly Efficient Cache Admission Policy - arXiv"), [GitHub](https://github.com/ben-manes/caffeine/wiki/Efficiency?utm_source=chatgpt.com "Efficiency · ben-manes/caffeine Wiki - GitHub"))
    
- Redis/Valkey/Dragonfly — статус и сравнения. ([Linux Foundation](https://www.linuxfoundation.org/press/linux-foundation-launches-open-source-valkey-community?utm_source=chatgpt.com "Linux Foundation Launches Open Source Valkey Community"), [GitHub](https://github.com/dragonflydb/dragonfly?utm_source=chatgpt.com "dragonflydb/dragonfly: A modern replacement for Redis ... - GitHub"))
    
- Redis LangCache/Vector Sets и семантическое кэширование. ([The New Stack](https://thenewstack.io/redis-launches-vector-sets-and-a-new-tool-for-semantic-caching-of-llm-responses/?utm_source=chatgpt.com "Redis Launches Vector Sets and a New Tool for Semantic Caching ..."), [Redis](https://redis.io/blog/what-is-semantic-caching/?utm_source=chatgpt.com "Semantic caching for faster, smarter LLM apps - Redis"))
    

---

## Что подготовить заранее (чтобы 90 минут «зашли»):

- Два сервисных эндпойнта: «медленный» и «быстрый» (с кэшем).
    
- Конфиг CDN (например, Fastly/Akamai sandbox) с SWR/SIE и `Cache-Status`. ([Fastly](https://www.fastly.com/documentation/guides/concepts/edge-state/cache/?utm_source=chatgpt.com "Caching content with Fastly | Fastly Documentation"))
    
- Мини-демо _singleflight_ или аналог (любой язык). ([Medium](https://medium.com/pickme-engineering-blog/singleflight-in-go-a-clean-solution-to-cache-stampede-02acaf5818e3?utm_source=chatgpt.com "Singleflight in Go : A Clean Solution to Cache Stampede - Medium"))
    
- Небольшой Java-пример с Caffeine (W-TinyLFU). ([GitHub](https://github.com/ben-manes/caffeine/wiki/Efficiency?utm_source=chatgpt.com "Efficiency · ben-manes/caffeine Wiki - GitHub"))
    
- Скрипт для `pg_prewarm` и дашборд hit-rate (Pg/MySQL). ([PostgreSQL](https://www.postgresql.org/docs/current/pgprewarm.html?utm_source=chatgpt.com "17: F.28. pg_prewarm — preload relation data into buffer caches"), [MySQL](https://dev.mysql.com/doc/refman/8.4/en/innodb-buffer-pool.html?utm_source=chatgpt.com "MySQL 8.4 Reference Manual :: 17.5.1 Buffer Pool"))
    

Если хочешь, упакую это в слайды/методичку и добавлю говорящий «скрипт спикера» и задания для ДЗ под выбранную цель (например, «Практик-инженерия» или «GenAI-кэш»).

---

Сравни свои предложения со следующими:
1. Цели занятия:
- проанализировать принципы работы кэширующих систем, таких как Redis и Memcached, и их применимость в системах с высокой нагрузкой;  
- разобраться с стратегиями кеширования и вытеснения данных из кеша;  
- обсудить методы проектирования систем с использованием кэша для ускорения обработки данных;  
- разобраться в подходах к проектированию распределённых систем с использованием Content Delivery Networks (CDNs);  
- научиться интегрировать кэширование и CDNs для улучшения производительности и доступности приложений;  
- разобраться с применением S3 в качестве хранения данных
2. Суть:
- стратегии кеширования, вытеснения данных из кеша;
- использование кэширующих систем: Redis, Memcached;
- проектирование систем с использованием CDN и S3.
3. Компетенции:
- Применять эффективные решения по работе с обработкой и хранением данных
    - выбирать оптимальные решения по обработке данных
    - оценивать перспективу выбора БД для текущей и будущей работы
Какие совпадают, какие не актуальны, какие не учены

---

Давай разложу сравнение по пунктам.

---

## 1. **Цели занятия**

**Совпадения с моими предложениями**:

- _Проанализировать принципы работы кэширующих систем (Redis/Memcached)_ — да, у меня Redis (и его актуальные форки Valkey/Dragonfly) присутствует, Memcached я не выделял отдельно, но можно упомянуть в сравнении.
    
- _Разобраться с стратегиями кэширования и вытеснения_ — у меня это целый блок: cache-aside, read-through, write-through, write-behind, refresh-ahead, LRU/LFU/W-TinyLFU.
    
- _Обсудить методы проектирования систем с использованием кэша_ — это у меня вынесено в «многоуровневое кэширование» (клиент → CDN → приложение → БД) и «предотвращение cache stampede».
    
- _Разобраться в подходах к проектированию распределённых систем с CDN_ — у меня целый блок про edge-кэширование, SWR/SIE, tiered caching, purge API, Cache Reserve.
    
- _Научиться интегрировать кэширование и CDNs_ — тоже есть, как часть многоуровневого дизайна.
    

**Различия / то, что не учтено у вас, но есть у меня**:

- Лицензионные и технологические изменения в экосистеме Redis (Valkey, Dragonfly).
    
- Семантическое кэширование для LLM/RAG — новая тема 2025.
    
- Кэширование в СУБД (InnoDB buffer pool, Pg shared_buffers, prewarm) — полезно, но у вас не указано.
    
- Подробный разбор stale-while-revalidate / stale-if-error (SWR/SIE) и `Cache-Status`.
    
- Методы борьбы с cache stampede (request coalescing, jitter, locks) — у вас нет отдельным пунктом.
    

**Неактуальное или узкое в ваших целях**:

- _Применение S3 в качестве хранения данных_ — это больше про объектное хранилище, не про кэширование. S3 можно упомянуть как cold storage или origin для CDN, но как отдельная цель в теме «Кэширование» — вторично.
    

---

## 2. **Суть занятия**

**Совпадения**:

- Стратегии кэширования и вытеснения — полностью перекрываются моим блоком «Политики и шаблоны».
    
- Redis, Memcached — у меня Redis + актуальные альтернативы, Memcached можно добавить в сравнительную таблицу.
    
- CDN — у меня расширенный разбор с современными фичами (SWR/SIE, tiered, purge, Cache Reserve).
    

**Отличия**:

- S3 как отдельная тема — у меня нет в плане, но могу встроить в блок «источники правды и многоуровневое хранение» как cold storage с edge-кэшированием.
    
- У меня добавлены кэши в БД, политики анти-штормов (cache stampede), семантический кэш — у вас этого нет.
    

---

## 3. **Компетенции**

**Совпадения**:

- «Выбирать оптимальные решения по обработке данных» — это по сути у меня в компетенциях «осознанный выбор движка и стратегии кэширования».
    
- «Применять эффективные решения по хранению» — у меня тоже есть, включая многоуровневое хранение (CDN + app + DB).
    

**Что у вас нет, но у меня есть**:

- Диагностика кэш-хитов/промахов (через Cache-Status, hit-rate метрики).
    
- Борьба с cache stampede как отдельная инженерная компетенция.
    
- Понимание особенностей новых движков (Valkey/Dragonfly) и лицензионных ограничений Redis.
    
- Использование W-TinyLFU и других современных eviction-алгоритмов.
    
- Семантическое кэширование (векторные поиски) — важно для AI-приложений.
    

---

Если коротко:

|Пункт|Совпадает|Нет в вашем плане|У вас есть, у меня нет|Мнение по актуальности|
|---|---|---|---|---|
|Redis, Memcached|✅|Я добавил Valkey/Dragonfly|—|Актуально, но Memcached как основа — нишевая тема в 2025|
|Стратегии кэширования|✅|Добавлены SWR/SIE, anti-stampede|—|Очень актуально|
|CDN|✅|Подробные фичи CDN 2025|—|Актуально|
|S3|⚠️|—|✅ (у вас есть, у меня нет)|Вторично для темы «Кэширование»|
|DB caches|—|✅|—|Полезно добавить|
|Anti-stampede|—|✅|—|Важная практика в high-load|
|Семантический кэш|—|✅|—|Новый тренд 2025|

---

Хочешь, я сделаю **объединённую версию целей/компетенций/плана**, где будет и твой базовый список, и мои расширенные темы под 2025? Так получится «золотая середина» — без потери актуальных фич и с учётом классики.