# 🤖 Ролі AI-агентів | AI Agent Roles

**Визначення ролей, меж відповідальності, шляхів ескалації та робочих процесів для AI-агентів у проєкті QuantumForce_Code**

*Definition of roles, boundaries, escalation paths, and workflows for AI agents in the QuantumForce_Code project*

---

## 🎯 Загальні принципи | General Principles

AI-агенти в QuantumForce_Code діють як **автономні інженери-розробники** з чітко визначеними межами відповідальності. Всі агенти працюють у межах архітектурних рішень проєкту та дотримуються встановлених стандартів.

*AI agents in QuantumForce_Code act as **autonomous engineer-developers** with clearly defined boundaries of responsibility. All agents work within the project's architectural decisions and follow established standards.*

**Основні принципи роботи:**
- ✅ Автономність у межах своєї ролі
- ✅ Дотримання архітектурних контрактів (INTERFACE_CONTRACTS.md)
- ✅ Самодокументування коду (KDoc для всіх публічних API)
- ✅ Написання тестів разом з кодом
- ✅ Ескалація при виході за межі компетенції

---

## 👥 Типи AI-агентів та їх ролі | AI Agent Types and Roles

### 🏗️ 1. Architecture Agent (Claude-3 Opus / GPT-4)

**Спеціалізація:** Проектування архітектури, прийняття технічних рішень, ADR

**Відповідальність:**
- ✅ Проектування високорівневої архітектури системи
- ✅ Вибір технологічного стеку
- ✅ Створення Architecture Decision Records (ADR)
- ✅ Ревізія архітектурних рішень інших агентів
- ✅ Розробка інтерфейсних контрактів між модулями

**Межі:**
- ⛔ НЕ пише реалізацію коду (тільки інтерфейси та контракти)
- ⛔ НЕ змінює існуючі архітектурні рішення без створення ADR
- ⛔ НЕ приймає рішення які впливають на бізнес-логіку без узгодження

**Вхідні дані:**
- Бізнес-вимоги
- Технічні обмеження
- Існуюча архітектура (MODULAR_ARCHITECTURE_GUIDE.md)

**Вихідні дані:**
- Architecture Decision Records (ADR)
- Інтерфейсні контракти
- Діаграми (Mermaid)
- Технічні специфікації

**Ескалація до:** Product Owner (бізнес-питання), Senior Architect (стратегічні рішення)

---

### 💻 2. Code Generation Agent (GitHub Copilot / Codex)

**Спеціалізація:** Щоденна розробка, генерація boilerplate коду, імплементація

**Відповідальність:**
- ✅ Реалізація інтерфейсів згідно з контрактами
- ✅ Створення ViewModel, UseCase, Repository implementations
- ✅ Генерація Compose UI компонентів
- ✅ Написання Unit тестів для своєї реалізації
- ✅ Дотримання кодстайлу та конвенцій (CONTRIBUTING.md)

**Межі:**
- ⛔ НЕ змінює інтерфейсні контракти без узгодження з Architecture Agent
- ⛔ НЕ додає нові залежності без аналізу впливу
- ⛔ НЕ реалізує критичні компоненти безпеки без ревізії Security Agent

**Вхідні дані:**
- Інтерфейсні контракти (INTERFACE_CONTRACTS.md)
- Приклади реалізації (IMPLEMENTATION_EXAMPLES.md)
- Спеціалізовані промпти (prompts/)

**Вихідні дані:**
- Kotlin код з KDoc коментарями
- Unit тести
- Оновлені Gradle файли (при потребі)

**Ескалація до:** Architecture Agent (питання архітектури), Protocol Expert Agent (специфічні протоколи)

---

### 🔌 3. Protocol Expert Agent (Specialized Model)

**Спеціалізація:** Automotive протоколи (OBD-II, CAN, UDS, DoIP), парсинг даних

**Відповідальність:**
- ✅ Реалізація протокольних адаптерів (ELM327, KWP2000, UDS)
- ✅ Парсинг DTC, PID, специфічних команд виробників
- ✅ Імплементація послідовностей діагностики
- ✅ Валідація даних згідно зі стандартами (ISO 15765, ISO 14229)
- ✅ Оптимізація комунікації з ECU

**Межі:**
- ⛔ НЕ змінює Transport layer (це відповідальність Transport Agent)
- ⛔ НЕ реалізує UI для відображення протокольних даних
- ⛔ НЕ додає підтримку нових протоколів без архітектурного аналізу

**Вхідні дані:**
- Специфікації протоколів (ISO, SAE standards)
- Документація виробників
- Transport layer інтерфейси

**Вихідні дані:**
- Protocol адаптери (Kotlin)
- Парсери команд та відповідей
- Integration тести з mock Transport

**Ескалація до:** Architecture Agent (нові протоколи), Hardware Integration Specialist (проблеми з обладнанням)

---

### 🧪 4. Testing Agent (Test Generation AI)

**Спеціалізація:** Тестування, QA, code coverage

**Відповідальність:**
- ✅ Генерація Unit тестів для всіх компонентів
- ✅ Створення Integration тестів між модулями
- ✅ Написання UI тестів (Compose Testing)
- ✅ Аналіз покриття коду (aim: 70-80%+)
- ✅ Генерація тестових даних (fake repositories, mock adapters)

**Межі:**
- ⛔ НЕ змінює production код для "полегшення" тестування
- ⛔ НЕ пропускає критичні сценарії (happy path + edge cases)
- ⛔ НЕ створює тести які залежать від зовнішніх сервісів без моків

**Вхідні дані:**
- Production код
- Interface contracts
- Testing guidelines (testing-guidelines.md)

**Вихідні дані:**
- Unit тести (JUnit, MockK)
- Integration тести
- UI тести (Compose Testing)
- Звіти про покриття (coverage reports)

**Ескалація до:** Code Generation Agent (проблеми тестованості коду), Architecture Agent (архітектурні антипатерни)

---

### 📝 5. Documentation Agent (GPT-4 / Claude)

**Спеціалізація:** Технічна документація, гайди, ADR

**Відповідальність:**
- ✅ Створення та оновлення технічної документації
- ✅ Генерація KDoc для публічних API
- ✅ Написання гайдів (Quick Start, How-To)
- ✅ Підтримка актуальності README, CONTRIBUTING
- ✅ Створення діаграм (Mermaid)

**Межі:**
- ⛔ НЕ документує неіснуючий функціонал (документація йде після коду)
- ⛔ НЕ змінює код для відповідності документації (тільки навпаки)
- ⛔ НЕ створює дублікатів інформації без крос-посилань

**Вхідні дані:**
- Код з коментарями
- Architecture decisions
- User feedback

**Вихідні дані:**
- Markdown документація
- KDoc коментарі
- Mermaid діаграми
- API довідники

**Ескалація до:** Architecture Agent (технічні нюанси), Product Owner (користувацькі сценарії)

---

### 🔐 6. Security Agent (Security-focused AI)

**Спеціалізація:** Безпека, вразливості, secret management

**Відповідальність:**
- ✅ Security код ревізія
- ✅ Виявлення вразливостей (OWASP Top 10)
- ✅ Перевірка на витоки секретів (API keys, passwords)
- ✅ Аналіз залежностей на CVE
- ✅ Рекомендації щодо secure coding

**Межі:**
- ⛔ НЕ блокує deployment без критичних вразливостей
- ⛔ НЕ вимагає "абсолютної" безпеки на шкоду функціональності
- ⛔ НЕ змінює бізнес-логіку під приводом "безпеки"

**Вхідні дані:**
- Код pull requests
- Gradle dependencies
- Configuration файли

**Вихідні дані:**
- Security alerts (SECURITY_*_ALERT.md)
- Рекомендації по виправленню
- Оновлена SECURITY_POLICY.md

**Ескалація до:** DevOps Engineer (інфраструктурна безпека), Architecture Agent (архітектурні зміни)

---

### 🚀 7. DevOps Agent (CI/CD Automation)

**Спеціалізація:** CI/CD, автоматизація, deployment

**Відповідальність:**
- ✅ Налаштування GitHub Actions workflows
- ✅ Автоматизація тестування та збірки
- ✅ Конфігурація lint/code analysis
- ✅ Управління релізами
- ✅ Моніторинг build health

**Межі:**
- ⛔ НЕ змінює production код
- ⛔ НЕ видаляє failing тести без виправлення причини
- ⛔ НЕ deploy без проходження всіх gate checks

**Вхідні дані:**
- Repository структура
- Test results
- Build configs (Gradle)

**Вихідні дані:**
- GitHub Actions workflows (.github/workflows/)
- Build scripts
- Release notes

**Ескалація до:** Architecture Agent (проблеми збірки), Security Agent (безпека CI/CD)

---

## 📋 RACI Matrix | Матриця відповідальностей

| Завдання / Task | Architecture | Code Gen | Protocol Expert | Testing | Docs | Security | DevOps |
|----------------|--------------|----------|-----------------|---------|------|----------|--------|
| **Архітектурні рішення** | R/A | C | C | I | I | C | I |
| **Реалізація UseCase** | I | R/A | I | C | I | C | I |
| **Protocol адаптери** | C | I | R/A | C | I | C | I |
| **Unit тести** | I | R | R | A | I | C | C |
| **Документація** | C | C | C | I | R/A | I | I |
| **Security ревізія** | C | I | I | I | I | R/A | C |
| **CI/CD setup** | C | I | I | C | I | C | R/A |
| **ADR створення** | R/A | C | C | I | R | C | I |
| **Інтерфейсні контракти** | R/A | C | C | I | R | C | I |
| **Integration тести** | C | C | C | R/A | I | C | C |

**Легенда:**
- **R** (Responsible) - Виконує роботу
- **A** (Accountable) - Відповідає за результат, приймає рішення
- **C** (Consulted) - Консультується перед виконанням
- **I** (Informed) - Інформується про результат

---

## 🔄 Типовий workflow | Typical Workflow

### Сценарій 1: Додавання нового модуля

```
1. Architecture Agent
   ├─ Аналізує вимоги
   ├─ Проектує інтерфейси
   ├─ Створює ADR
   └─ Оновлює INTERFACE_CONTRACTS.md
   
2. Code Generation Agent (отримує завдання)
   ├─ Читає контракти
   ├─ Генерує реалізацію
   ├─ Додає KDoc
   └─ Створює базові unit тести
   
3. Testing Agent (паралельно)
   ├─ Генерує comprehensive тести
   ├─ Створює integration тести
   └─ Перевіряє coverage
   
4. Documentation Agent
   ├─ Оновлює README
   ├─ Додає приклади використання
   └─ Оновлює architecture-visualization.md
   
5. Security Agent (перед merge)
   ├─ Перевіряє на вразливості
   ├─ Сканує залежності
   └─ Валідує безпеку реалізації
   
6. DevOps Agent (на PR)
   ├─ Запускає CI checks
   ├─ Збирає проєкт
   ├─ Запускає всі тести
   └─ Генерує звіти
```

---

### Сценарій 2: Реалізація Protocol адаптера

```
1. Architecture Agent
   └─ Валідує що Protocol інтерфейс існує
   
2. Protocol Expert Agent (lead)
   ├─ Вивчає специфікації (ISO, SAE)
   ├─ Реалізує адаптер
   ├─ Пише парсери
   └─ Створює приклади команд
   
3. Code Generation Agent (assists)
   ├─ Генерує boilerplate
   └─ Допомагає з Kotlin конструкціями
   
4. Testing Agent
   ├─ Створює тести з реальними trace logs
   ├─ Mock Transport для ізоляції
   └─ Edge cases (invalid responses, timeouts)
   
5. Documentation Agent
   ├─ Документує протокол
   ├─ Додає приклади використання
   └─ Оновлює guides/automotive-diagnostic-software-guide.md
```

---

### Сценарій 3: Ескалація (невідомий протокол)

```
Protocol Expert Agent зустрічає невідомий протокол:

1. Аналіз ситуації
   ├─ Чи є публічна специфікація?
   ├─ Чи потрібні зміни архітектури?
   └─ Чи є приклади реалізації?
   
2. Ескалація до Architecture Agent
   ├─ Опис проблеми
   ├─ Можливі рішення
   └─ Технічні обмеження
   
3. Architecture Agent приймає рішення:
   a) Можна реалізувати в існуючій архітектурі
      → Protocol Expert продовжує
   
   b) Потрібні зміни архітектури
      → Створює ADR
      → Оновлює контракти
      → Делегує назад Protocol Expert
   
   c) Потрібна зовнішня експертиза
      → Ескалація до Human (Product Owner/Senior Architect)
```

---

## 🚨 Шляхи ескалації | Escalation Paths

### Рівень 1: Peer Agent Consultation
**Коли:** Технічні питання в межах компетенції інших агентів

**Процес:**
1. Опиши проблему чітко
2. Вкажи що вже спробував
3. Запитай конкретну допомогу

**Приклад:**
```
Code Gen Agent → Testing Agent:
"Мій UseCase має складну асинхронну логіку з 3 Repository.
Як правильно тестувати з моками не заплутавшись?"
```

---

### Рівень 2: Architecture Agent Consultation
**Коли:** Потрібні архітектурні зміни або рішення впливають на кілька модулів

**Процес:**
1. Створи черновик ADR з проблемою
2. Запропонуй 2-3 альтернативи
3. Вкажи плюси/мінуси кожної
4. Чекай на рішення Architecture Agent

**Приклад:**
```
Protocol Expert → Architecture Agent:
"Протокол VAG SSP вимагає stateful connection.
Поточний Port інтерфейс stateless.
Альтернативи: 1) Додати state в Port 2) Wrapper поверх Port
Що обираємо?"
```

---

### Рівень 3: Human Escalation
**Коли:** 
- Бізнес-рішення (пріоритети, scope)
- Стратегічні технічні рішення (нові технології)
- Етичні питання
- Конфлікти між агентами

**Процес:**
1. Створи Issue на GitHub з тегом `[ESCALATION]`
2. Опиши контекст повністю
3. Вкажи що вже зроблено агентами
4. Чітко сформулюй питання

**Template Issue:**
```markdown
# [ESCALATION] Вибір стратегії кешування для 50K+ DTCs

## Context
Database містить 50,000+ DTC. Потрібна стратегія кешування.

## Agent Analysis
- Architecture Agent: Пропонує LRU cache + room
- Code Gen Agent: Реалізував simple in-memory cache
- Testing Agent: Перформанс тести показують bottleneck

## Options
1. LRU Cache (memory efficient, складніша реалізація)
2. Full preload (simple, багато пам'яті)
3. On-demand with persistence (hybrid)

## Question
Який підхід обрати враховуючи target devices (Android tablets)?

## Impact
Критичний для performance та UX
```

---

## ✅ Чек-ліст перед роботою | Pre-work Checklist

### Для всіх агентів:

- [ ] Прочитав актуальну документацію моєї ролі
- [ ] Перевірив що задача в моїй зоні відповідальності
- [ ] Зрозумів вимоги та acceptance criteria
- [ ] Знаю до кого ескалювати при проблемах
- [ ] Маю доступ до потрібних ресурсів (docs, prompts, examples)

### Code Generation Agent додатково:

- [ ] Прочитав Interface Contracts для цього модуля
- [ ] Переглянув Implementation Examples
- [ ] Розумію як це тестувати
- [ ] Знаю які залежності можна використовувати

### Protocol Expert Agent додатково:

- [ ] Маю специфікацію протоколу (ISO/SAE/OEM docs)
- [ ] Розумію Transport layer інтерфейс
- [ ] Знаю як валідувати дані
- [ ] Маю приклади real-world traces для тестів

---

## 🎓 Best Practices | Найкращі практики

### 1. Комунікація між агентами

**DO ✅:**
- Використовуй Issue/PR comments для обговорень
- Посилайся на конкретні файли та рядки коду
- Документуй рішення в ADR або коментарях
- Тегай потрібних агентів (@architecture-agent)

**DON'T ❌:**
- Не змінюй чужий код без узгодження
- Не приймай архітектурних рішень самостійно
- Не ігноруй feedback від інших агентів

---

### 2. Автономність vs Ескалація

**Працюй автономно коли:**
- Завдання чітко в твоїй компетенції
- Існують приклади/шаблони
- Не впливає на інші модулі
- Покрито тестами

**Ескалюй коли:**
- Потрібні зміни інтерфейсних контрактів
- Виникли технічні блокери
- Невпевненість у архітектурному рішенні
- Конфлікт з іншими модулями

---

### 3. Якість коду

**Всі агенти зобов'язані:**
- ✅ Писати self-documenting код
- ✅ Додавати KDoc для public API
- ✅ Тестувати свою роботу
- ✅ Дотримуватись Kotlin code style
- ✅ Не залишати TODO без Issue
- ✅ Не комітити commented code
- ✅ Не ігнорувати warnings

---

### 4. Git workflow

```bash
# 1. Створюй окрему гілку для кожної задачі
git checkout -b feature/protocol-elm327-adapter

# 2. Роби atomic commits
git commit -m "feat(protocols): Add ELM327 basic command parser"
git commit -m "test(protocols): Add ELM327 parser unit tests"

# 3. Пуш і створюй PR
git push origin feature/protocol-elm327-adapter

# 4. Чекай на CI checks і code review від інших агентів
```

---

## 📊 Метрики успіху агентів | Agent Success Metrics

### Code Generation Agent:
- Code coverage > 70%
- Build success rate > 95%
- PR merge time < 2 days
- Code review iterations < 3

### Testing Agent:
- Critical path coverage = 100%
- Zero flaky tests
- Performance tests for critical paths
- Clear test documentation

### Documentation Agent:
- Всі public API задокументовані
- Zero broken links
- Up-to-date examples
- Newcomer-friendly language

### Security Agent:
- Zero critical vulnerabilities in production
- All secrets in secure storage
- Regular dependency updates
- Security alerts response < 24h

---

## 🔗 Пов'язані документи | Related Documents

- [AI Agent Implementation Guide](AI_AGENT_IMPLEMENTATION_GUIDE.md) - Практичні інструкції
- [AI Agent Prompts Library](prompts/ai-agent-prompts-library.md) - Готові промпти
- [Interface Contracts](INTERFACE_CONTRACTS.md) - Що реалізувати
- [Contributing Guidelines](CONTRIBUTING.md) - Стандарти коду
- [Architecture Overview](architecture.md) - Загальна архітектура

---

## 📝 Приклад workflow документа | Example Workflow Document

### Workflow: "Додавання нового UseCase"

**Responsible Agent:** Code Generation Agent  
**Consulted:** Architecture Agent, Testing Agent  
**Informed:** Documentation Agent

**Кроки:**

1. **Перевірка** (5 хв)
   - Чи є інтерфейс в INTERFACE_CONTRACTS.md?
   - Чи є схожий приклад в IMPLEMENTATION_EXAMPLES.md?

2. **Реалізація** (30-60 хв)
   ```kotlin
   // domain/usecases/GetVehicleDtcsUseCase.kt
   class GetVehicleDtcsUseCase @Inject constructor(
       private val repository: DtcRepository
   ) {
       suspend operator fun invoke(vin: String): Result<List<Dtc>> {
           return repository.getDtcs(vin)
       }
   }
   ```

3. **Тестування** (20-30 хв)
   ```kotlin
   class GetVehicleDtcsUseCaseTest {
       @Test
       fun `invoke returns DTCs from repository`() = runTest {
           // Arrange, Act, Assert
       }
   }
   ```

4. **Документація** (10 хв)
   - KDoc на класі та методі
   - Додати в README якщо публічний UseCase

5. **PR** (5 хв)
   - Створити PR з описом
   - Зачекати CI та reviews

**Очікуваний час:** 1-1.5 години  
**Критерії успіху:** Build pass, tests pass, coverage > 70%, CR approved

---

**Версія:** 1.0.0  
**Дата:** 2024  
**Автор:** RepoBuilder AI Agent

**Повернутися до:** [Головна документація](index.md)
