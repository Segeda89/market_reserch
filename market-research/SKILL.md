---
name: market-research
description: "Универсальный ресёрч рынка: методология 4 потока + last30days + Tavily + web_search + AI-фильтрация шума."
version: 6.0.0
author: Alexander + Hermes
license: MIT
metadata:
  hermes:
    tags: [Research, Market Analysis, Deep Research, Competition, Noise Filtering]
    category: research
    related_skills: [last30days, grounded-citations]
---

> **Требование к окружению (16.09.2026):** движок `last30days` требует **Python 3.12+**. Там, где `python3` = 3.11 (например, сервер Hermes с python3.14), вызовы ниже надо делать через `python3.14` — иначе движок падает с «last30days v3 requires Python 3.12+».
> **YouTube** работает при установленном `yt-dlp`; TikTok/Instagram требуют `SCRAPECREATORS_API_KEY`, X — отдельной авторизации.
> **LAW 7 движка:** на темах-именах собственных (продукты, компании, люди) обязателен `--plan` — агент генерирует JSON-план сам.

# Installation

## Method 1: Via AI Agent (Recommended)

If you have a Hermes Agent running, ask it to install this skill:

```
Copy the market-research skill folder to ~/.hermes/skills/market-research/
```

The agent will handle the file placement automatically.

## Method 2: Via Terminal

```bash
# Clone the repository
git clone https://github.com/Segeda89/market_reserch.git /tmp/market_reserch

# Create the skills directory if it doesn't exist
mkdir -p ~/.hermes/skills

# Copy the skill folder
cp -r /tmp/market_reserch/market-research ~/.hermes/skills/

# Clean up
rm -rf /tmp/market_reserch
```

Verify installation:

```bash
ls ~/.hermes/skills/market-research/SKILL.md
```

---

# Tavily Setup

## What is Tavily?

[Tavily](https://tavily.com/) is a deep web search API designed specifically for AI agents and LLM applications. Unlike standard web search, Tavily returns structured, clean content from web pages — making it ideal for research tasks that need depth beyond keyword search results.

## How to Get an API Key

1. Go to [https://app.tavily.com](https://app.tavily.com)
2. Sign up for a free account
3. Navigate to your dashboard
4. Copy your API key

## Documentation

Full API documentation: [https://docs.tavily.com/](https://docs.tavily.com/)

## Adding the Key to Hermes

Add your Tavily API key to the Hermes environment file:

```bash
echo 'TAVILY_API_KEY=tvly-YOUR_API_KEY_HERE' >> ~/.hermes/.env
```

Replace `tvly-YOUR_API_KEY_HERE` with your actual key from the dashboard.

## Is Tavily Required?

**No.** Tavily is optional but recommended for best results.

- **With Tavily:** Deep web search, page extraction, advanced research queries
- **Without Tavily:** The `web_search` tool (DuckDuckGo) works as a free fallback for basic searches

The skill auto-detects Tavily availability and falls back gracefully.

---

# Market Research: Метод «Четыре потока»

Универсальный скилл для ЛЮБОГО ресёрча рынка. Методология «4 потока» + инструменты сбора данных + AI-фильтрация шума.

## Когда вызывать

- Любой запрос на ресёрч рынка, конкурентов, аудитории
- Анализ ниши перед запуском продукта/фичи
- Поиск «бриллиантов» (разрывов между спросом и предложением)
- Подготовка контент-плана на основе данных
- Любой вопрос «что люди думают/ищут/боятся в нише X»

---

## Движки (инструменты)

| Движок | Зачем | Когда использовать |
|--------|-------|-------------------|
| **Методология 4 потоков** | Структура и алгоритм | Всегда — это карта действий |
| **last30days** | Глубокий ресёрч по источникам за 30 дней | Обзор рынка, тренды, что обсуждают |
| **Tavily** | Глубокий веб-поиск + извлечение страниц | Когда нужна глубина (tavily_search, tavily_extract, tavily_research) |
| **web_search** | Быстрый поиск | Fallback если Tavily недоступен |

**Приоритет:** Tavily → last30days → web_search. Выбирай по задаче, можно комбинировать.

**Почему web_search нужен рядом с Tavily:** Tavily — платный API. Если ключа нет, лимит исчерпан или сервис недоступен — web_search (DuckDuckGo) работает как бесплатный fallback.

---

## AI-фильтрация шума (включена по умолчанию)

### Зачем
Собранные данные содержат много мусора: репосты, спам, оффтоп, повторы, реклама. AI-фильтрация превращает 200 постов в 30 релевантных сигналов.

### 4-Stage Pipeline

Используй многоступенчатую конвейерную фильтрацию — **не** один LLM-вызов на всё. Дешёвые операции первыми, LLM только на уже сокращённых данных.

| Stage | Название | Что делает | Стоимость |
|-------|----------|-----------|-----------|
| **1** | Rule-based | Дедупликация (хеш), фильтр языка, фильтр длины, блеклист-домены, удаление ссылок-без-текста | Zero LLM |
| **2** | Heuristic | N-gram повторы (спам/копипаста), удаление boilerplate/HTML, плотность эмодзи, аномалии частоты постинга | Zero LLM |
| **3** | LLM Classification | Релевантность 1-10, категория, конкретика, семантическая дедупликация, уверенность | Core filtering |
| **4** | Quote Extraction + Grouping | Группировка по темам, дословные цитаты, выделение меньшинственных мнений — **только на отфильтрованных данных** | On filtered data |

### Classification Taxonomy (7 категорий)

| Категория | Примеры |
|-----------|---------|
| **COMPLAINT** | «X keeps crashing on Android, I'm fed up», «Support hasn't replied in 2 weeks» |
| **QUESTION** | «Has anyone tried Y for Z?», «Is it worth the money?» |
| **FACT** | «I switched to Y and saved $200/month», «X has 10M users as of 2026» |
| **RECOMMENDATION** | «If you're dealing with Z, try W — it actually works», «Don't buy Y, get Z instead» |
| **COMPARISON** | «X is better than Y for small teams, but Z wins for enterprise» |
| **DEMAND** | «They need to add offline mode, it's 2026», «Should support webhooks by now» |
| **NOISE** | «BUY NOW 50% OFF!!!», «lol», «this», «👍», ссылки без комментариев |

**Secondary attributes** (присваиваются к любой категории):
- **Relevance**: 1-10 (1=полностью оффтоп, 10=ядро ниши)
- **Specificity**: HIGH (имена, цифры, детали) / LOW (расплывчато)
- **Emotion**: fear / anger / confusion / distrust / hope / positive / neutral
- **Actionability**: HIGH (можно действовать) / LOW (только инфо)

### Prompt Template: Initial Noise Classification

```
You are a market research data analyst. Classify the following social media posts/comments.

CONTEXT: We are researching [YOUR NICHE/TOPIC]. Only posts relevant to this niche should pass through.

For each post, determine:
1. RELEVANCE to [niche]: 1-10 (1=completely off-topic, 10=core niche discussion)
2. CATEGORY: COMPLAINT / QUESTION / FACT / RECOMMENDATION / COMPARISON / DEMAND / NOISE / OTHER
3. SPECIFICITY: HIGH (contains names, numbers, concrete details) / LOW (vague, generic)
4. EMOTION: fear / anger / confusion / distrust / hope / positive / neutral
5. ACTIONABLE: true (contains useful insight) / false (informational only)

CATEGORY EXAMPLES:
- COMPLAINT: "X keeps crashing on Android, I'm fed up", "Support hasn't replied in 2 weeks", "Pricing is a ripoff"
- QUESTION: "Has anyone tried Y for Z?", "What's the best alternative to Y?", "Is Z worth the money?"
- FACT: "I switched to Y and it took 2 days to set up", "X has 10M users as of 2026"
- RECOMMENDATION: "You should try X, it solved all my problems", "Don't buy Y, get Z instead"
- COMPARISON: "X is better than Y for small teams, but Z wins for enterprise"
- DEMAND: "They need to add offline mode, it's 2026", "Should support webhooks by now"
- NOISE: "BUY NOW", "lol", "this", "👍", link-only posts with no commentary
- OTHER: Does not clearly fit any of the above. Use only when genuinely uncertain.

IMPORTANT RULES:
- Posts that CONTRADICT the majority opinion should be RETAINED even if relevance is moderate
- Do NOT paraphrase or clean up language — note the EXACT sentiment
- If a post is ambiguous, classify by its PRIMARY intent
- Use OTHER only when genuinely uncertain — do not force into wrong category
- Preserve unique/unusual use cases or problems even if mentioned by few people

Posts to classify:
{posts_json}

Respond as a JSON array with one object per post:
[{"id": "post_id", "category": "COMPLAINT", "relevance": 7, "specificity": "HIGH", "emotion": "anger", "actionable": true, "confidence": 0.91}]
```

### Minority Opinion Preservation

Агрессивная фильтрация непропорционально удаляет инакомыслие. Правила сохранения:

1. **Порог релевантности: 3-4** (не 5). Пост с relevance=4, который противоречит 90% — Ценнее, чем relevance=7, который повторяет большинство.
2. **Явное правило в промпте**: «Posts that CONTRADICT the majority opinion should be RETAINED even if relevance is moderate»
3. **Мониторинг распределения**: если 100% отфильтрованных данных согласны — что-то не так, снизь порог.
4. **Tier-система**:

| Tier | Relevance | Действие |
|------|-----------|----------|
| Tier 1 | 7-10 | Включить в основной анализ |
| Tier 2 | 5-6 | Включить, пометить как «borderline» |
| Tier 3 | 3-4 | Включить только если от эксперта ИЛИ содержит необычное мнение |
| Tier 4 | 1-2 | Исключить (явный шум) |

### Quote Extraction Rules

Извлечение цитат — **дословно**, без перефразирования:

1. Копируй ТОЧНЫЙ текст оригинала — **НЕ** перефразируй, **НЕ** «почищай» грамматику
2. Сохраняй опечатки, сленг, сокращения как есть
3. Каждая цитата должна быть самодостаточной (читаема без контекста)
4. Включай атрибуцию источника (платформа / имя пользователя)
5. Используй кавычки `"..."` вокруг каждой цитаты
6. Если цитата длинная — используй `...`, но сохраняй точные слова

**Плохо:** «Пользователь выразил недовольство ценовой моделью» → это ПЕРЕФРАЗ
**Хорошо:** «charged me $47 for the month i never even used lol never again» → это ДОСЛОВНО

### Quality Metrics

| Метрика | Цель | Зачем |
|---------|------|-------|
| **Recall** (сигнал) | >90% | Потерять жалобу хуже, чем пропустить мусор |
| **Precision** (шум) | >95% | Не фильтровать лишний контент |
| **F1 Score** | >0.90 | Сбалансированная мера |
| **Quote Verbatim Rate** | >95% | Цитаты дословные |

**Автоматические проверки после каждого батча:**
1. Количество постов ≠ 0 (не отфильтровали всё)
2. Доля NOISE в ожидаемом диапазоне (обычно 40-70% от сырых данных)
3. Ни одна категория не превышает 80% (иначе — bias промпта)
4. Распределение confidence адекватное (не все 1.0 и не все 0.3)
5. Цитаты — реальные цитаты, не саммари

### Когда отключать (`--no-filter`)

- Если нужен полный сырой набор данных (для ручного анализа)
- Если ниша очень узкая и каждый элемент ценен
- Если данных мало (< 20 элементов)

---

## Параллельное выполнение (субагенты)

**Правило:** Если задача включает 3+ независимых направления — запускай их параллельно через субагентов, а не последовательно. Финальный синтез делаешь сам после получения результатов всех субагентов.

**Типовое распределение:**

| Поток | Субагент | Инструменты |
|-------|----------|-------------|
| Поток 1 (Голос рынка) | Субагент A | last30days (Reddit, X, YouTube) |
| Поток 2 (Спрос) | Субагент B | Tavily + web_search |
| Поток 3 (Деньги) | Субагент C | Tavily + web_search |
| Поток 4 (Предложение) | Субагент D | last30days + Tavily |
| AI-фильтрация | Субагент E (или основной) | LLM-анализ собранных данных |
| Синтез + бриллианты | **Ты** | Результаты всех субагентов |

**Питфол:** Каждый субагент получает изолированный контекст — передавай ему всю необходимую информацию о нише в `context`, не рассчитывай что он знает что-то из основного диалога.

---

## Пошаговый алгоритм

### Шаг 0: Опиши нишу

1. **Что за ниша?** [одна фраза]
2. **Для кого?** [целевая аудитория]
3. **Какой продукт?** [название и суть]
4. **Кто конкуренты?** [топ-3-5]

---

### Шаг 1: Голос рынка (Поток 1)

**Цель:** Что люди РЕАЛЬНО говорят, когда думают, что их никто не слышит.

**Инструменты:**
```bash
# last30days — глубокий обзор
python3.14 ~/.hermes/skills/research/last30days/scripts/last30days.py "[ниша] problems complaints frustrated" --search=reddit,x,youtube --days=30

# Tavily — глубокий поиск
tavily_search(query="[ниша] problems complaints frustrated", search_depth="advanced")

# web_search — fallback
web_search("[ниша] problems complaints frustrated site:reddit.com")
```

**Что собирать:**
- Жалобы на конкурентов
- Страхи и опасения
- Повторяющиеся формулировки (язык аудитории)
- Конкретные цитаты с источниками
- Эмоции: страх, гнев, растерянность, недоверие

---

### Шаг 2: Спрос (Поток 2)

**Цель:** Чего боятся ещё до входа в нишу.

**Инструменты:**
```bash
# last30days
python3.14 ~/.hermes/skills/research/last30days/scripts/last30days.py "[ниша] how to start beginner guide safe" --search=reddit,youtube --days=30

# Tavily
tavily_search(query="[ниша] как начать безопасно для новичков", search_depth="advanced")

# web_search
web_search("[ниша] как начать безопасно для новичков")
```

**Важно:** Смотреть на long-tail запросы — они показывают реальные страхи.

---

### Шаг 3: Деньги (Поток 3)

**Цель:** Реальный размер рынка, а не «взять 1% от абстрактного рынка».

**Инструменты:**
```bash
# last30days
python3.14 ~/.hermes/skills/research/last30days/scripts/last30days.py "[ниша] market size revenue funding" --search=reddit,x,hackernews --days=30

# Tavily
tavily_search(query="[ниша] market size 2025 2026 revenue TAM", search_depth="advanced")

# web_search
web_search("[ниша] market size 2025 2026 revenue")
```

**Дополнительно:**
- Посчитай TAM/SAM/SOM
- Изучи тарифы конкурентов
- Проверь: готовы ли люди платить? Какой средний чек?

---

### Шаг 4: Предложение (Поток 4)

**Цель:** Что конкуренты предлагают — и чего НЕ предлагают.

**Инструменты:**
```bash
# last30days
python3.14 ~/.hermes/skills/research/last30days/scripts/last30days.py "[конкурент1] vs [конкурент2] comparison review problems" --search=reddit,x,youtube --days=30

# Tavily
tavily_search(query="[конкурент] review problems complaints", search_depth="advanced")
tavily_extract(urls=["https://site-конкурента.com/pricing"])

# web_search
web_search("[конкурент] review problems complaints")
```

**Важно:** Смотреть на ПЕРВЫЕ ЭКРАНЫ, офферы, какие боли они закрывают.

---

### Шаг 5: AI-фильтрация шума

**Цель:** Отделить сигнал от шума. Превратить 200 постов в 30 релевантных.

**Алгоритм (4-Stage Pipeline):**
1. **Stage 1 — Rule-based:** Дедупликация (хеш), фильтр языка, фильтр длины, блеклист-домены
2. **Stage 2 — Heuristic:** N-gram повторы, удаление boilerplate, плотность эмодзи
3. **Stage 3 — LLM Classification:** Релевантность 1-10, категория (COMPLAINT/QUESTION/FACT/RECOMMENDATION/COMPARISON/DEMAND/NOISE), конкретика, уверенность
4. **Stage 4 — Quote Extraction + Grouping:** Группировка по темам, дословные цитаты, выделение меньшинственных мнений

**На выходе:** структурированный набор данных готовый к анализу.

---

### Шаг 6: Поиск бриллиантов

**Цель:** Разрывы между спросом и предложением — там лежат бриллианты.

**Алгоритм:**
1. Собери данные из 4 потоков (после AI-фильтрации)
2. Найди пересечения:
   - Жалоба + отсутствие решения у конкурентов = Gap #1
   - Вопрос + нет понятного ответа = Gap #2
   - Готовность платить + нет продукта = Gap #3
3. Проверь каждый Gap:
   - Реален ли? (есть доказательства)
   - Размер достаточен? (есть деньги)
   - Мы можем решить? (есть компетенция)
   - Конкуренты не скопируют? (есть moat)
4. Приоритизируй: размер аудитории × скорость реализации × уникальность

**На выходе:** топ-5-7 бриллиантов. Для каждого:
- Проблема → почему конкуренты не решают → как решить → как проверить

---

## Ключевые фреймворки

### JTBD (Jobs to Be Done)
Понять, «какую работу» клиент нанимает продукт.
- Спрашивай: «Что ты делал до этого? Какое решение использовал? Почему выбрал именно его?»

### Модель Кано
- **Must-be:** без чего нежизнеспособен
- **Performance:** пропорционально удовлетворению
- **Attractive:** создаёт восторг
- **Indifferent:** большинству всё равно

### TAM/SAM/SOM
- **TAM:** весь рынок в теории
- **SAM:** часть, которую можешь достичь
- **SOM:** что захватишь за 1-2 года

### Pre-Mortem
Представь: прошёл год, проект провалился. Какие причины?

---

## Протокол верификации

1. Минимум 2 независимых источника на факт
2. Сохранять лог запросов
3. Проверять, не отравлен ли источник
4. Не верить AI-ассистенту на слово — проверять каждый вывод

---

## Флаги запуска

| Флаг | Описание |
|------|----------|
| `--no-filter` | Отключить AI-фильтрацию шума (сырые данные) |
| `--min-relevance 4` | Минимальная оценка релевантности для включения (по умолчанию 4) |
| `--keep-contrarian true` | Сохранять посты, противоречащие большинству (по умолчанию true) |
| `--streams 1-4` | Запустить только указанные потоки |
| `--parallel` | Запускать потоки параллельно через субагентов |
| `--output markdown` | Формат отчёта (markdown / json / plain) |
| `--save-to <path>` | Сохранить отчёт в файл |

---

## Чек-лист

### До начала
- [ ] Определена конкретная ниша
- [ ] Определена целевая аудитория
- [ ] Есть доступ к площадкам обсуждений

### Сбор данных
- [ ] Собраны жалобы конкурентов (минимум 30-50 постов)
- [ ] Собраны вопросы пользователей (минимум 20-30)
- [ ] Посчитан TAM/SAM/SOM
- [ ] Изучены топ-3-5 конкурентов

### AI-фильтрация
- [ ] Сырые данные отфильтрованы через 4-stage pipeline
- [ ] Мусор удалён (репосты, спам, оффтоп)
- [ ] Данные сгруппированы по темам
- [ ] Цитаты извлечены дословно
- [ ] Меньшинственные мнения сохранены (relevance ≥ 3-4)

### Анализ
- [ ] Найдены 3-7 gaps (разрывов)
- [ ] Каждый gap проверен по JTBD
- [ ] Каждый gap оценён по размеру рынка
- [ ] Есть гипотезы решений

### Валидация
- [ ] Проверены гипотезы через протокол верификации
- [ ] Есть 2+ независимых источника на каждый факт

---

## Ловушки

- «Все источники врут» — не верь ни одному без проверки
- «Конкуренты глупые» — они не глупые, у них другие приоритеты
- «Рынок огромный» — посчитай реальный размер через TAM/SAM/SOM
- «Пользователи хотят X» — проверь через JTBD, а не опрос мнений
- «Мы первые» — скорее всего, нет. Проверь архивы
- «AI всё знает» — AI помогает анализировать, но не заменяет проверку
- «Шума мало» — даже 10 релевантных постов лучше 200 мусорных
