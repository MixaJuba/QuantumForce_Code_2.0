# 🤖 GitHub Copilot - Завдання | Tasks

**AI Agent:** GitHub Copilot  
**Середовище:** GitHub Codespaces (інтегрований)  
**Спеціалізація:** Domain Layer & Business Logic  
**Координатор:** Cursor AI

---

## 🎯 Ваша Зона Відповідальності

### Директорії де ви працюєте:
```
core/domain/
├── src/main/kotlin/com/quantumforce_code/core/domain/
│   ├── model/          # Domain entities
│   ├── repository/     # Repository interfaces
│   └── usecase/        # Use cases (business logic)
└── src/test/kotlin/    # Unit tests
```

---

## 📋 Поточні Завдання (Priority Order)

### ✅ COMPLETED
- [x] Review documentation
- [x] Understand project architecture

### 🔴 HIGH PRIORITY - DO FIRST

#### Task 1: Create Domain Entities
**Estimated Time:** 2-3 hours  
**Files to Create:**
- `core/domain/src/main/kotlin/com/quantumforce_code/core/domain/model/DtcCode.kt`
- `core/domain/src/main/kotlin/com/quantumforce_code/core/domain/model/Vehicle.kt`
- `core/domain/src/main/kotlin/com/quantumforce_code/core/domain/model/DiagnosticSession.kt`

**Documentation:**
- Read: `docs/MODULAR_ARCHITECTURE_GUIDE.md` (section "Domain Layer")
- Example: `docs/IMPLEMENTATION_EXAMPLES.md` (section 1)

**Requirements:**
- Immutable data classes
- Business logic methods in classes
- KDoc comments in Ukrainian
- No Android dependencies (pure Kotlin)

**Acceptance Criteria:**
- [ ] DtcCode with Severity enum, FreezeFrame
- [ ] Vehicle with Protocol enum, supportsAdvancedDiagnostics()
- [ ] DiagnosticSession with Status enum, complete()/cancel()
- [ ] All classes documented with KDoc
- [ ] Compiles successfully

---

#### Task 2: Create Repository Interfaces
**Estimated Time:** 1-2 hours  
**Files to Create:**
- `core/domain/src/main/kotlin/com/quantumforce_code/core/domain/repository/DtcRepository.kt`
- `core/domain/src/main/kotlin/com/quantumforce_code/core/domain/repository/VehicleRepository.kt`
- `core/domain/src/main/kotlin/com/quantumforce_code/core/domain/repository/DiagnosticSessionRepository.kt`

**Documentation:**
- Read: `docs/INTERFACE_CONTRACTS.md` (section "Domain Layer Interfaces")

**Requirements:**
- All methods suspend functions
- Return Result<T> for operations that can fail
- Comprehensive KDoc for each method

**Acceptance Criteria:**
- [ ] DtcRepository with 9 methods (see INTERFACE_CONTRACTS.md)
- [ ] VehicleRepository with 8 methods
- [ ] DiagnosticSessionRepository with 11 methods
- [ ] All methods properly documented
- [ ] Compiles without errors

---

#### Task 3: Create Use Cases
**Estimated Time:** 3-4 hours  
**Files to Create:**
- `core/domain/src/main/kotlin/com/quantumforce_code/core/domain/usecase/UseCase.kt` (base class)
- `core/domain/src/main/kotlin/com/quantumforce_code/core/domain/usecase/GetDtcCodesUseCase.kt`
- `core/domain/src/main/kotlin/com/quantumforce_code/core/domain/usecase/ClearDtcCodesUseCase.kt`
- `core/domain/src/main/kotlin/com/quantumforce_code/core/domain/usecase/GetVehicleUseCase.kt`
- `core/domain/src/main/kotlin/com/quantumforce_code/core/domain/usecase/SaveVehicleUseCase.kt`

**Documentation:**
- Read: `docs/IMPLEMENTATION_EXAMPLES.md` (section 1 - full example)
- Read: `docs/AI_AGENT_IMPLEMENTATION_GUIDE.md` (Domain Layer section)

**Requirements:**
- Extend UseCase<Params, Result> base class
- Inner data class Params
- Single responsibility per UseCase
- Proper error handling

**Acceptance Criteria:**
- [ ] UseCase base class implemented
- [ ] All 4 use cases created
- [ ] Each has Params data class
- [ ] Business logic in execute() method
- [ ] KDoc documentation
- [ ] Compiles successfully

---

### 🟡 MEDIUM PRIORITY

#### Task 4: Additional Use Cases
**Files to Create:**
- `GetDiagnosticSessionUseCase.kt`
- `StartDiagnosticSessionUseCase.kt`
- `CompleteDiagnosticSessionUseCase.kt`

---

#### Task 5: Write Unit Tests
**Estimated Time:** 2-3 hours  
**Files to Create:**
- `core/domain/src/test/kotlin/com/quantumforce_code/core/domain/usecase/GetDtcCodesUseCaseTest.kt`
- `core/domain/src/test/kotlin/com/quantumforce_code/core/domain/usecase/ClearDtcCodesUseCaseTest.kt`
- (tests for other use cases)

**Documentation:**
- Read: `docs/testing-guidelines.md`

**Requirements:**
- Use JUnit 5
- Mock repositories with MockK
- Test success and failure scenarios
- Achieve > 80% coverage

**Acceptance Criteria:**
- [ ] Tests for all use cases
- [ ] Happy path tested
- [ ] Error cases tested
- [ ] All tests pass
- [ ] Coverage > 80%

---

## 🛠️ Як виконувати завдання

### 1. Візьміть завдання в роботу:
```bash
# В GitHub Issue додайте коментар:
"Starting work on this task. ETA: [time]"
```

### 2. Створіть гілку:
```bash
git checkout -b feature/domain-entities-copilot
```

### 3. Використовуйте Copilot:
```kotlin
// Напишіть коментар що потрібно створити:
// Create DtcCode data class with code, description, severity
// Add Severity enum: LOW, MEDIUM, HIGH, CRITICAL
// Add FreezeFrame nested data class

// Copilot запропонує код, перевірте його
```

### 4. Створіть PR:
```bash
git add .
git commit -m "feat(domain): Add DtcCode, Vehicle, DiagnosticSession entities"
git push origin feature/domain-entities-copilot
```

Then create PR on GitHub with:
- Title: `[Domain] Add core domain entities`
- Description: Reference to Issue #
- Tag: `@cursor` for review

---

## 📚 Ресурси для вас

**Must Read:**
1. `docs/MODULAR_ARCHITECTURE_GUIDE.md` - Ваш головний посібник
2. `docs/INTERFACE_CONTRACTS.md` - Що саме реалізувати
3. `docs/IMPLEMENTATION_EXAMPLES.md` - Приклади готового коду

**Корисні файли:**
- `docs/AI_AGENT_IMPLEMENTATION_GUIDE.md` - Покрокові інструкції
- `docs/CONTRIBUTING.md` - Стандарти коду

---

## ✅ Чек-лист перед PR

Перед створенням Pull Request перевірте:

**Код:**
- [ ] Всі класи мають KDoc коментарі
- [ ] Data classes є immutable
- [ ] Використовуються suspend функції де потрібно
- [ ] Result<T> для операцій що можуть fail
- [ ] Немає Android dependencies (тільки Kotlin stdlib)

**Документація:**
- [ ] KDoc для всіх public класів та методів
- [ ] Приклади використання в коментарях
- [ ] README оновлено якщо потрібно

**Тести:**
- [ ] Unit tests написані
- [ ] Тести проходять локально
- [ ] Coverage > 70% мінімум

**Git:**
- [ ] Commit message follows convention
- [ ] Branch назва описова
- [ ] PR description complete

---

## 🚫 Що НЕ робити

❌ **НЕ змінюйте:**
- Файли в інших модулях (data, ui, hardware, protocols)
- Архітектурні рішення без узгодження з Cursor
- INTERFACE_CONTRACTS.md без Issue

❌ **НЕ додавайте:**
- Android dependencies в Domain
- Зовнішні бібліотеки без узгодження
- Складну логіку в entities (має бути в UseCases)

---

## 💬 Якщо потрібна допомога

**Питання по архітектурі:**
- Створіть Issue з тегом `question`
- Tag `@cursor` для відповіді

**Питання по реалізації:**
- Перевірте `docs/IMPLEMENTATION_EXAMPLES.md`
- Якщо не зрозуміло - Issue з тегом `help-wanted`

**Технічні проблеми:**
- Tag `@claude` якщо проблеми з компіляцією
- Tag `@cursor` для інших питань

---

## 📊 Ваш Прогрес

**Поточний статус:** 🟢 READY TO START  
**Призначені завдання:** 5 tasks (3 HIGH, 2 MEDIUM)  
**Estimated Total Time:** 8-12 hours  
**Target Completion:** Протягом тижня

**Чек-лист старту:**
- [ ] Прочитав AGENTS_WORKFLOW.md
- [ ] Прочитав MODULAR_ARCHITECTURE_GUIDE.md
- [ ] Зрозумів свою зону відповідальності
- [ ] Готовий взяти Task 1

---

## 🎯 Наступні кроки

1. **Зараз:** Візьміть Task 1 (Domain Entities)
2. **Потім:** Task 2 (Repository Interfaces)
3. **Далі:** Task 3 (Use Cases)
4. **Після:** Task 5 (Unit Tests)

Після виконання всіх HIGH priority tasks - повідомте `@cursor` для нових завдань.

---

**Координатор:** @cursor  
**Питання:** Create Issue з тегом `@cursor @copilot`  
**Документація:** `docs/` directory

Успішної роботи! 🚀
