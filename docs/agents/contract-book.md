# 📋 Контракти Взаємодії AI-Агентів
**Contract Book for AI Agents Coordination**

**Версія:** 1.0.0
**Дата:** 2025-01-20
**Автор:** Cursor AI (AI Program Director)
**Статус:** 📋 ACTIVE CONTRACTS

---

## 🎯 Призначення Контрактів

Цей документ визначає **контракти взаємодії** між AI-агентами - правила, за якими вони працюють, що можуть читати, що можуть змінювати, як звітують про свою роботу.

**Contract (контракт)** - це угода між агентами про те, як вони взаємодіють, щоб уникнути конфліктів та забезпечити якість.

---

## 📚 Джерела Істини (Sources of Truth)

### 🏗️ **Архітектурні Джерела**
- **`docs/architecture.md`** - Загальна архітектура системи
- **`docs/adr/`** - Architecture Decision Records (архітектурні рішення)
- **`docs/MODULAR_ARCHITECTURE_GUIDE.md`** - Детальний гід по архітектурі

### 📋 **Контрактні Джерела**
- **`docs/INTERFACE_CONTRACTS.md`** - Всі інтерфейси та їх сигнатури
- **`docs/IMPLEMENTATION_EXAMPLES.md`** - Приклади реалізації
- **`.ops/agents-registry.yaml`** - Реєстр агентів та їх ролей

### 🔄 **Процесні Джерела**
- **`.github/AGENTS_WORKFLOW.md`** - Робочий процес агентів
- **`.github/tasks/`** - Завдання для кожного агента
- **`.github/CODE_REVIEW_CHECKLIST.md`** - Чек-лист для code review

### 🚀 **Технічні Джерела**
- **`docs/CI_CD_SYSTEM_GUIDE.md`** - CI/CD процеси
- **`docs/SECURITY_QUALITY_ASSURANCE.md`** - Безпека та якість
- **`docs/testing-guidelines.md`** - Правила тестування

---

## 🤝 Контракти Між Агентами

### 👔 **Cursor AI ↔ Всі Агенти**

#### **Що Cursor AI читає:**
- Всі Issues та PR
- Всі файли документації
- Всі звіти агентів
- Всі метрики продуктивності

#### **Що Cursor AI може змінювати:**
- Створювати та редагувати Issues
- Робити code review та мержити PR
- Оновлювати документацію
- Приймати архітектурні рішення

#### **Як Cursor AI звітує:**
- Щоденні звіти в Issues
- Щотижневі звіти в Project Board
- Архітектурні рішення в ADR
- Координаційні плани

#### **Сценарій "Делегування Задачі":**
1. **Аналіз:** Cursor AI аналізує вимоги
2. **Планування:** Створює Issue з чіткими критеріями
3. **Делегування:** Призначає відповідного агента
4. **Моніторинг:** Відстежує прогрес
5. **Review:** Перевіряє результат та мержить

### 💼 **GitHub Copilot ↔ Claude**

#### **Що Copilot читає:**
- `docs/INTERFACE_CONTRACTS.md` (Domain секція)
- `docs/IMPLEMENTATION_EXAMPLES.md` (Domain приклади)
- Issues з міткою `ai:copilot`

#### **Що Copilot може змінювати:**
- `core/domain/src/main/kotlin/**/*.kt`
- `core/domain/src/test/kotlin/**/*Test.kt`
- Domain документацію

#### **Що Claude читає:**
- `docs/INTERFACE_CONTRACTS.md` (Data секція)
- `docs/IMPLEMENTATION_EXAMPLES.md` (Data приклади)
- Issues з міткою `ai:claude`

#### **Що Claude може змінювати:**
- `core/data/src/main/kotlin/**/*.kt`
- `features/*/src/main/kotlin/**/*.kt`
- `app/src/main/kotlin/**/*.kt`

#### **Сценарій "Hand-off між Copilot та Claude":**
1. **Copilot завершує:** Створює Domain entities та interfaces
2. **Коміт:** Copilot комітить з повідомленням "Domain layer ready for Data implementation"
3. **Claude читає:** Перевіряє нові interfaces
4. **Реалізація:** Claude реалізує Repository implementations
5. **Тестування:** Claude створює integration tests

### 🔧 **OpenAI Codex ↔ Всі Агенти**

#### **Що Codex читає:**
- `docs/guides/automotive-diagnostic-software-guide.md`
- `docs/adr/adr-003-ev-support-architecture.md`
- `docs/adr/adr-004-adas-integration-architecture.md`
- Issues з міткою `ai:codex`

#### **Що Codex може змінювати:**
- `hardware/transport/src/main/kotlin/**/*.kt`
- `protocols/obd/src/main/kotlin/**/*.kt`
- `protocols/ev/src/main/kotlin/**/*.kt`
- `hardware/adas/src/main/kotlin/**/*.kt`

#### **Сценарій "Infrastructure Integration":**
1. **Codex реалізує:** Hardware protocols
2. **Тестування:** Codex створює protocol tests
3. **Документація:** Codex оновлює protocol docs
4. **Інтеграція:** Інші агенти використовують protocols
5. **Валідація:** Gemini тестує integration

### 🧪 **Google Gemini ↔ Всі Агенти**

#### **Що Gemini читає:**
- Весь код проекту
- `docs/testing-guidelines.md`
- Issues з міткою `ai:gemini`

#### **Що Gemini може змінювати:**
- `**/test/**/*Test.kt`
- Тестові дані
- Документацію тестів
- Приклади використання

#### **Сценарій "Testing Integration":**
1. **Агент завершує:** Створює новий код
2. **Gemini аналізує:** Перевіряє що потрібно протестувати
3. **Тести:** Gemini створює comprehensive tests
4. **Валідація:** Gemini перевіряє coverage
5. **Документація:** Gemini оновлює test docs

---

## 📊 Звітність та Комунікація

### 📝 **Щоденні Звіти**
Кожен агент щодня коментує в своїх Issues:
```
## Прогрес за сьогодні:
- ✅ Завершено: [список завдань]
- 🔄 В роботі: [поточне завдання]
- ⏳ Планується: [наступні кроки]
- 🚨 Проблеми: [якщо є]

ETA до завершення: [час]
```

### 📈 **Щотижневі Звіти**
Кожен агент оновлює Project Board:
- Переміщує Issues в відповідні колонки
- Оновлює прогрес у відсотках
- Додає коментарі про досягнення

### 🚨 **Ескалація Проблем**
При проблемах агент:
1. Додає мітку `escalation` до Issue
2. Описує проблему та запропоновані рішення
3. Tag Cursor AI: `@cursor`
4. Чекає на рішення

### 📋 **Завершення Завдань**
При завершенні завдання агент:
1. Створює PR з детальним описом
2. Додає посилання на тести
3. Оновлює документацію
4. Чекає на code review

---

## 🔒 Політики Безпеки

### 🛡️ **Безпека Коду**
- **Жодних секретів** у репозиторії
- **Мінімальні дозволи** для кожного агента
- **Code review** обов'язковий для всіх змін
- **Security scanning** автоматичний

### 🔐 **Управління Секретами**
- **GitHub Secrets** для API ключів
- **Environment variables** для конфігурації
- **Ніколи не комітити** паролі або ключі
- **Rotate secrets** регулярно

### 📊 **Моніторинг**
- **Всі дії** логуються
- **Метрики** збираються автоматично
- **Аномалії** виявляються автоматично
- **Звіти** генеруються щодня

---

## 🎯 Сценарії Взаємодії

### 🔄 **Сценарій 1: Нова Фіча**

1. **Cursor AI:** Аналізує вимоги, створює Issue
2. **Copilot:** Реалізує Domain layer
3. **Claude:** Реалізує Data та UI layers
4. **Codex:** Додає Infrastructure якщо потрібно
5. **Gemini:** Створює comprehensive tests
6. **Cursor AI:** Робить code review та мержить

### 🔧 **Сценарій 2: Bug Fix**

1. **Gemini:** Виявляє bug через тести
2. **Gemini:** Створює Issue з детальним описом
3. **Відповідний агент:** Виправляє bug
4. **Gemini:** Перевіряє fix тестами
5. **Cursor AI:** Робить code review та мержить

### 🚀 **Сценарій 3: Архітектурна Зміна**

1. **Cursor AI:** Аналізує потребу в зміні
2. **Cursor AI:** Створює ADR з рішенням
3. **Всі агенти:** Обговорюють ADR
4. **Cursor AI:** Затверджує ADR
5. **Всі агенти:** Реалізують зміни згідно з ADR

---

## 📋 Як Перевірити Контракти

### 👀 **Що Побачити:**
1. **Issues:** Всі мають правильні мітки агентів
2. **PR:** Всі мають детальні описи
3. **Code Review:** Всі зміни перевірені
4. **Tests:** Всі нові функції протестовані

### 🔍 **Як Зрозуміти:**
- **Зелені мітки:** Контракти дотримуються
- **Жовті мітки:** Є незначні порушення
- **Червоні мітки:** Серйозні порушення контрактів

### ✅ **Що Перевірити:**
1. Агенти працюють тільки в своїх сферах
2. Всі зміни проходять code review
3. Тести покривають новий код
4. Документація оновлена
5. Секрети не потрапили в код

---

**Контракти активні та діють!** 🤝
