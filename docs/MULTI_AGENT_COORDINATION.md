# 🎭 Система Мультиагентної Координації | Multi-Agent Coordination System

**Реалізація адаптивного діалогу між конструктивним і скептичним агентами для досягнення узгоджених рішень**

*Implementation of adaptive dialogue between constructive and skeptical agents to achieve coherent decisions*

---

## 🎯 Призначення | Purpose

Цей документ описує **механізм координації** між AI-агентами для прийняття рішень через:
- 🗣️ Діалог між конструктивним агентом (пропонує) і скептичним (критикує)
- 🔄 Ітеративне покращення через цикли пропозиція → критика → уточнення
- 📊 Адаптивну зупинку при досягненні достатньої упевненості
- 🎯 Чергування ролей для зменшення упередження
- 🔍 Red team аналіз для пошуку контрприкладів

---

## 🏗️ Архітектура системи | System Architecture

```
┌─────────────────────────────────────────────────────────┐
│  ІНІЦІАЛІЗАЦІЯ                                          │
│  ├─ Визначення ролі координатора                       │
│  ├─ Читання всіх джерел (docs/, аналіз тенденцій)     │
│  ├─ Ініціалізація Evidence Ledger                      │
│  └─ Ініціалізація Assumption Register                  │
├─────────────────────────────────────────────────────────┤
│  ІТЕРАТИВНИЙ ЦИКЛ (max 4 раунди)                       │
│  ┌───────────────────────────────────────────────┐     │
│  │ Раунд 1-2: Agent_A (конструктивний)          │     │
│  │          + Agent_B (скептичний)              │     │
│  │                                               │     │
│  │ Раунд 3-4: ролі змінюються                   │     │
│  │   (зменшення confirmation bias)              │     │
│  └───────────────────────────────────────────────┘     │
│                                                         │
│  Кожен раунд:                                          │
│  1. Agent_A → пропозиція                               │
│  2. Оцінка впевненості (confidence)                    │
│  3. Agent_B → критика                                  │
│  4. Agent_A → уточнена пропозиція                      │
│  5. Оцінка нової впевненості                           │
│  6. Прийняття кращого варіанту                         │
│                                                         │
│  Умови виходу:                                         │
│  ├─ Confidence >= 75%                                  │
│  ├─ Немає суперечностей                                │
│  └─ АБО досягнуто max_rounds (4)                       │
├─────────────────────────────────────────────────────────┤
│  RED TEAM АНАЛІЗ                                       │
│  └─ Пошук контрприкладів та крайових випадків         │
├─────────────────────────────────────────────────────────┤
│  ФІНАЛІЗАЦІЯ                                           │
│  ├─ Людський наратив (для новачків)                   │
│  ├─ Технічний паспорт (для інженерів)                 │
│  ├─ Ланцюг сумнівів + план зняття                     │
│  └─ Межі рішень + тригери перегляду                   │
└─────────────────────────────────────────────────────────┘
```

---

## 👥 Ролі агентів | Agent Roles

### Agent_A: Конструктивний агент | Constructive Agent

**Відповідальність:**
- ✅ Пропонує дизайн, рішення, інструкції
- ✅ Базується на Evidence Ledger та документації
- ✅ Включає явні якорі на джерела
- ✅ Документує припущення в Assumption Register
- ✅ Рефайнить пропозиції на основі критики

**Критерії якості пропозиції:**
1. Чіткість формулювання (однозначне розуміння)
2. Наявність якорів на джерела
3. Явні припущення з методами верифікації
4. Оцінка впливу на архітектуру
5. Альтернативні підходи розглянуті

**Приклад пропозиції:**
```markdown
## Пропозиція: Додати підтримку WiFi OBD адаптерів

### Обґрунтування:
- Evidence: 25% користувачів Launch X431 використовують WiFi [джерело: ринковий аналіз]
- Припущення: WiFi швидше за Bluetooth для великих обсягів даних
  - Верифікація: провести benchmark test
  - Тригер перегляду: якщо різниця < 20%

### Архітектурний вплив:
- Додати новий Transport: `WiFiTransport` implements `Transport`
- Не порушує Clean Architecture ✅
- Backward compatible з існуючим `BluetoothTransport` ✅

### Альтернативи розглянуті:
1. USB-only підхід — відкинуто (не всі планшети мають USB-C)
2. Bluetooth-only — поточне рішення, достатньо для MVP

### Якорі:
- [INTERFACE_CONTRACTS.md#port](INTERFACE_CONTRACTS.md#port)
- [ASSUMPTION_REGISTER.md#S5.002](ASSUMPTION_REGISTER.md#S5.002)
```

---

### Agent_B: Скептичний агент | Skeptical Agent

**Відповідальність:**
- ✅ Критичний аналіз пропозицій
- ✅ Перевірка логіки та безпеки
- ✅ Оцінка відтворюваності
- ✅ Пошук альтернатив
- ✅ Генерація контрприкладів

**Критерії якості критики:**
1. Конкретні питання (не загальні "а що якщо...")
2. Посилання на конфлікти з документацією
3. Пропозиції щодо покращення
4. Оцінка ризиків
5. Альтернативні підходи

**Приклад критики:**
```markdown
## Критика пропозиції: WiFi OBD адаптери

### Логічні питання:
1. ❓ Яка частка Android планшетів підтримує WiFi concurrent connections?
   - Можливість: планшет на WiFi інтернеті + WiFi до OBD може не працювати
   - Рекомендація: перевірити Android WiFi API documentation

2. ❓ Security implications: WiFi адаптери часто мають default паролі
   - Ризик: Man-in-the-middle attack
   - Рекомендація: додати warning про зміну паролів

### Конфлікти з документацією:
- ⚠️ [ASSUMPTION_REGISTER.md#S5.002](ASSUMPTION_REGISTER.md#S5.002) каже "Bluetooth достатньо для MVP"
- ❓ Чому WiFi зараз, якщо MVP ще не завершено?

### Альтернативні підходи:
1. Зробити WiFi post-MVP feature (відкласти до v1.1)
2. Спочатку провести user research: скільки реально потрібен WiFi?

### Тест на контрприклад:
- Сценарій: Користувач у гаражі без WiFi роутера
- Питання: Як WiFi OBD працює без WiFi точки доступу?
- Очікування: OBD створює власну WiFi AP (Access Point)
- Верифікація: перевірити що це documented

### Оцінка confidence:
- Пропозиція: 60% (технічно обґрунтована, але timing questionable)
- Рекомендація: відкласти до post-MVP або зібрати user data
```

---

## 🔄 Процес ітеративного покращення | Iterative Improvement Process

### Раунд 1: Початкова пропозиція

**Agent_A (Конструктивний):**
```
State: Початковий
Завдання: Створити пропозицію інтеграції галузевої тенденції X
Вихід: Proposal_v1 з якорями та припущеннями
```

**Оцінка впевненості:**
```python
confidence = estimate_confidence(
    proposal=Proposal_v1,
    evidence_ledger=EvidenceLedger,
    verification_mode="text"
)
# Приклад: confidence = 0.55 (55%)
```

**Agent_B (Скептичний):**
```
State: Proposal_v1
Завдання: Знайти логічні проблеми, ризики, альтернативи
Вихід: Critique_v1 з конкретними питаннями
```

**Agent_A (Рефайнінг):**
```
State: Proposal_v1 + Critique_v1
Завдання: Адресувати критику, уточнити припущення
Вихід: Proposal_v2 (revised)
```

**Оцінка нової впевненості:**
```python
confidence_v2 = estimate_confidence(
    proposal=Proposal_v2,
    evidence_ledger=EvidenceLedger,
    verification_mode="text"
)
# Приклад: confidence_v2 = 0.68 (68%)
```

**Вибір кращого:**
```python
if confidence_v2 > confidence_v1:
    state = adopt(Proposal_v2)
    confidence = confidence_v2
else:
    state = adopt(best_of(Proposal_v1, Proposal_v2))
```

---

### Раунд 2: Продовження з тією ж роллю

Процес повторюється, але тепер:
- Agent_A працює з Proposal_v2
- Agent_B може знайти нові проблеми
- Confidence має зростати

**Умова ранньої зупинки:**
```python
if contradictions(state) == false and confidence >= 0.75:
    break  # Достатня упевненість досягнута
```

---

### Раунд 3-4: Зміна ролей

**Чому міняємо ролі?**
- Зменшення confirmation bias
- Агент що пропонував тепер критикує свої ідеї
- Свіжий погляд на проблему

```python
if round == 2:
    swap_roles(Agent_A, Agent_B)
    # Agent_A тепер скептичний
    # Agent_B тепер конструктивний
```

---

## 🔍 Red Team аналіз | Red Team Analysis

**Після основних раундів:**

```python
red_team = probe_for_counterexample(
    state=final_state,
    mode="text",
    evidence_ledger=EvidenceLedger
)
```

**Що робить Red Team:**
1. Шукає edge cases (крайові випадки)
2. Тестує припущення на міцність
3. Намагається зламати логіку рішення
4. Шукає неочевидні конфлікти з документацією

**Приклад Red Team тесту:**
```markdown
## Red Team Test: WiFi OBD підтримка

### Edge Case 1: Dual WiFi requirement
- Сценарій: Планшет підключений до домашнього WiFi (інтернет)
- Питання: Чи може Android одночасно підключитись до WiFi OBD?
- Перевірка: Android >= 5.0 підтримує WiFi Direct + infrastructure mode
- Результат: ✅ Технічно можливо, але потрібен API Level check

### Edge Case 2: Security
- Сценарій: OBD має default пароль "12345678"
- Атака: MITM через fake WiFi AP з тією ж SSID
- Результат: ⚠️ Потребує user education + warning в UI

### Contradiction Check:
- Перевірка: чи не конфліктує з [SECURITY_POLICY.md](SECURITY_POLICY.md)
- Результат: ✅ Немає прямого конфлікту, але додати security checklist
```

**Якщо Red Team знайшов проблему:**
```python
if red_team.found:
    state = Agent_A(address(red_team.issue, state, EvidenceLedger, AssumptionRegister))
    # Ще один раунд для адресації проблеми
```

---

## 📊 Оцінка впевненості | Confidence Estimation

### Фактори що впливають на confidence:

**Для MODE="text" (документація):**
```python
confidence = weighted_average([
    cited_anchors_present * 0.3,      # Є цитовані якорі?
    definitions_consistent * 0.25,     # Визначення узгоджені?
    no_contradictions * 0.25,          # Немає суперечностей?
    assumptions_documented * 0.1,      # Припущення задокументовані?
    verification_path_exists * 0.1     # Є шлях верифікації?
])
```

**Для MODE="code" (реалізація):**
```python
confidence = weighted_average([
    codespaces_runnable * 0.3,        # Можна запустити в Codespaces?
    build_passes * 0.25,               # Збірка проходить?
    tests_pass * 0.25,                 # Тести проходять?
    examples_work * 0.1,               # Приклади виконуються?
    success_criteria_met * 0.1         # Критерії успіху досягнуті?
])
```

**Для MODE="math" (аналітика):**
```python
confidence = weighted_average([
    solution_verified * 0.4,           # Рішення верифіковано підстановкою?
    alternative_proof * 0.3,           # Є альтернативне доведення?
    edge_cases_checked * 0.2,          # Крайові випадки перевірені?
    assumptions_stated * 0.1           # Припущення явно вказані?
])
```

---

## 🎨 Фіналізація результатів | Finalization

### Людський наратив (для аудиторії)

**Для новачків:**
```markdown
## Що було зроблено

Ми вирішили додати підтримку WiFi OBD адаптерів після детального аналізу.

### Навіщо це потрібно?
- 25% користувачів професійних сканерів використовують WiFi
- WiFi може бути швидшим для передачі великих логів

### Що змінюється в репозиторії?
- Новий файл: `hardware/transport/wifi/WiFiTransport.kt`
- Оновлено: `docs/INTERFACE_CONTRACTS.md` (додано WiFi приклад)
- Додано: тести в `hardware/transport/wifi/WiFiTransportTest.kt`

### Як відтворити середовище в Codespaces?
1. Відкрийте Codespaces (як в [VERIFICATION_PATHS.md](VERIFICATION_PATHS.md))
2. Запустіть `./gradlew :hardware:transport:wifi:build`
3. Перевірте тести: `./gradlew :hardware:transport:wifi:test`

### Як побачити роботу агента?
- GitHub Copilot згенерував 80% коду WiFiTransport
- Reviewer (Claude) перевірив security aspects
- Gemini створив тести

### Де прочитати рішення?
- ADR: [adr/adr-018-wifi-transport.md](adr/adr-018-wifi-transport.md)
- Наслідки: швидша передача даних, але потреба в security warnings
```

---

### Технічний паспорт (для інженерів)

```markdown
## Artifact Passport: WiFi Transport Implementation

### Що створено/оновлено:
- ✅ `WiFiTransport.kt` — 250 LOC, implements `Transport` interface
- ✅ `WiFiTransportTest.kt` — 15 unit tests, 95% coverage
- ✅ `INTERFACE_CONTRACTS.md` — додано WiFi приклад
- ✅ `SECURITY_POLICY.md` — додано WiFi security checklist

### Контракти для агентів:

**Copilot Coding Agent:**
- MUST NOT змінювати `Transport` interface без ADR
- MUST додавати KDoc до всіх public API
- MUST писати тести одночасно з кодом

**External Agents:**
- WiFi адаптери MUST бути на whitelist (security)
- Default паролі MUST trigger warning
- Concurrent WiFi MUST be checked (API level >= 21)

### Безпека:
- ✅ WiFi connection requires user confirmation
- ✅ SSID whitelist для відомих OBD виробників
- ⚠️ Warning якщо default password detected
- 🔒 Traffic encryption recommendation (future)

### Масштабованість:
- ✅ Transport interface дозволяє додати Ethernet, USB без змін архітектури
- ✅ DI structure (Hilt) підтримує multiple transports
- ✅ Strategy pattern дозволяє runtime вибір транспорту

### Верифікація:
- Mode: code
- Build: ✅ Passed
- Tests: ✅ 15/15 passed
- Codespaces: ✅ Reproducible
- Manual test: ⏳ Pending (потрібен WiFi OBD адаптер)
```

---

### Ланцюг сумнівів і план зняття

```markdown
## Сумніви та план їх зняття

### Сумнів 1: Чи дійсно WiFi швидший за Bluetooth?
**Припущення:** WiFi має вищу пропускну здатність  
**План верифікації:**
1. Benchmark test з реальним OBD адаптером (обидва типи)
2. Виміряти час зчитування 1000 PIDs
3. Порівняти з Bluetooth

**Тригер перегляду:** Якщо різниця < 20%, відкласти WiFi до v2.0

---

### Сумнів 2: Чи користувачі готові міняти паролі на OBD?
**Припущення:** Security-savvy користувачі змінять default паролі  
**План верифікації:**
1. User testing з 10 користувачами
2. Виміряти скільки % змінили пароль після warning

**Тригер перегляду:** Якщо < 30%, розробити auto-password change feature

---

### Сумнів 3: Чи Android Dual WiFi працює стабільно?
**Припущення:** Android 5.0+ підтримує concurrent WiFi  
**План верифікації:**
1. Тестування на 5 різних планшетах (різні виробники)
2. Перевірка в документації Android

**Тригер перегляду:** Якщо не працює на 2+ пристроях, fallback до single WiFi mode
```

---

### Межі рішень і тригери перегляду

```markdown
## Тимчасові рішення та обмеження

### Обмеження 1: WiFi тільки для Android 5.0+
**Причина:** API для concurrent WiFi з'явився в Lollipop  
**Межа:** Користувачі з Android 4.x не матимуть WiFi підтримки  
**Тригер перегляду:**
- Якщо > 10% користувачів на Android 4.x
- Тоді: розробити fallback UI "Upgrade your Android"

---

### Обмеження 2: Security warnings in MVP
**Причина:** Немає часу на auto-password change в MVP  
**Межа:** Користувачі мають manually змінювати паролі  
**Тригер перегляду:**
- Post-MVP (v1.1)
- Або: якщо security incident reported

---

### Обмеження 3: WiFi whitelist based on SSID patterns
**Причина:** Неможливо перевірити всі WiFi OBD моделі  
**Межа:** Деякі OBD адаптери можуть не розпізнатись автоматично  
**Тригер перегляду:**
- Після 100 user reports
- Тоді: розширити whitelist або додати manual mode
```

---

## 📈 Метрики процесу | Process Metrics

**Відстеження ефективності координації:**

```python
metrics = {
    "average_rounds_to_consensus": 2.5,        # Середня кількість раундів
    "confidence_improvement_rate": 0.15,       # Приріст confidence за раунд
    "early_exit_rate": 0.65,                   # % рішень до 4 раундів
    "red_team_catch_rate": 0.12,               # % проблем знайдених Red Team
    "role_swap_effectiveness": 0.08,           # Покращення після зміни ролей
}
```

**Цільові показники:**
- ✅ average_rounds_to_consensus: 2-3 (не занадто швидко, не занадто повільно)
- ✅ confidence_improvement_rate: > 0.10 (процес дає результат)
- ✅ early_exit_rate: 60-80% (більшість рішень якісні до 4 раундів)
- ✅ red_team_catch_rate: 10-20% (є що ловити, але не критично багато)

---

## 🔗 Інтеграція з іншими системами | Integration

### Evidence Ledger
- Використовується для верифікації якорів
- Оновлюється після прийняття рішення (додаються нові факти)

### Assumption Register
- Використовується для документування припущень з пропозицій
- Оновлюється з планами верифікації та тригерами

### Architecture Decision Records
- Кожне прийняте рішення створює ADR
- ADR містить результати діалогу Agent_A ↔ Agent_B

### Verification Paths
- Використовується для створення human-readable інструкцій
- "Як відтворити" секція базується на verification paths

---

## 🎓 Навчання агентів | Agent Training

### Для нових AI-агентів:

**Крок 1: Розуміння ролей**
- Прочитати [AI_AGENT_ROLE.md](AI_AGENT_ROLE.md)
- Визначити чи ви конструктивний чи скептичний у цьому завданні

**Крок 2: Вивчення процесу**
- Прочитати цей документ повністю
- Переглянути приклади діалогів

**Крок 3: Практика**
- Взяти simple issue
- Пройти 1-2 раунди з іншим агентом
- Отримати feedback від координатора

---

**Автор:** Copilot Coding Agent  
**Версія:** 1.0.0  
**Дата створення:** 2024-10-23  
**Базовано на:** Problem statement PromptCode  
**Ліцензія:** Відповідно до основного репозиторію
