# 🤖 Claude (Anthropic) - Завдання | Tasks

**AI Agent:** Claude  
**Середовище:** GitHub Codespaces + Claude Code Interpreter  
**Спеціалізація:** Full-Stack Development, Compilation, Integration  
**Координатор:** Cursor AI

---

## 🎯 Ваша Зона Відповідальності

### Директорії де ви працюєте:
```
core/data/              # Repository implementations
features/dtc/           # DTC feature module
features/live/          # Live data feature module
app/                    # Main application module
```

### Ваша унікальна роль:
- 🔨 **Компіляція проекту** - Ви єдиний хто має повний Codespace доступ
- 🔧 **Debugging** - Виправлення помилок компіляції
- 🏗️ **Integration** - З'єднуєте всі частини разом
- 🎨 **UI Development** - Jetpack Compose screens

---

## 📋 Поточні Завдання (Priority Order)

### 🔴 CRITICAL - DO FIRST

#### Task 0: Setup Build Environment
**Estimated Time:** 1-2 hours  
**Objective:** Створити базову структуру Android проекту

**Files to Create:**
- `settings.gradle.kts`
- `build.gradle.kts` (root)
- `app/build.gradle.kts`
- `core/domain/build.gradle.kts`
- `core/data/build.gradle.kts`
- `gradle/libs.versions.toml` (version catalog)

**Documentation:**
- Read: `docs/guides/quick-start-ua.md` (День 1)
- Read: `docs/guides/automotive-diagnostic-software-guide.md` (ФАЗА 1)

**Requirements:**
- Kotlin 1.9+
- Gradle 8.2+
- Android SDK 26-34
- Jetpack Compose
- Hilt DI
- Room Database

**Acceptance Criteria:**
- [ ] Gradle sync успішний
- [ ] Базовий app module компілюється
- [ ] Емулятор запускається
- [ ] Hello World екран показується

**Промпт для себе:**
```
Створи Android проект структуру для QuantumForce_Code.

Вимоги:
- Multi-module: app, core/domain, core/data
- Kotlin DSL для Gradle
- Version catalog в gradle/libs.versions.toml
- Jetpack Compose для UI
- Hilt для DI
- Room для database
- Мінімум SDK 26, Target SDK 34

Використай модульну структуру з docs/MODULAR_ARCHITECTURE_GUIDE.md
```

---

#### Task 1: Implement Data Layer - Room Database
**Estimated Time:** 3-4 hours  
**Priority:** HIGH  
**Dependencies:** Copilot має закінчити Domain entities

**Files to Create:**
- `core/data/src/main/kotlin/com/quantumforce_code/core/data/db/AppDatabase.kt`
- `core/data/src/main/kotlin/com/quantumforce_code/core/data/db/dao/DtcDao.kt`
- `core/data/src/main/kotlin/com/quantumforce_code/core/data/db/dao/VehicleDao.kt`
- `core/data/src/main/kotlin/com/quantumforce_code/core/data/db/entity/DtcEntity.kt`
- `core/data/src/main/kotlin/com/quantumforce_code/core/data/db/entity/VehicleEntity.kt`

**Documentation:**
- Read: `docs/IMPLEMENTATION_EXAMPLES.md` (section 2 - Data Layer)
- Read: `docs/INTERFACE_CONTRACTS.md`

**Requirements:**
- Room 2.6+
- DAOs with suspend functions
- Entities with proper @PrimaryKey, @ColumnInfo
- Type converters for complex types
- Database migrations strategy

**Acceptance Criteria:**
- [ ] Database створюється при старті
- [ ] DAOs мають CRUD методи
- [ ] Можна зберегти та прочитати DTC
- [ ] Можна зберегти та прочитати Vehicle
- [ ] Компілюється без помилок
- [ ] Database tests проходять

---

#### Task 2: Implement Repository Implementations
**Estimated Time:** 2-3 hours  
**Dependencies:** Task 1 completed

**Files to Create:**
- `core/data/src/main/kotlin/com/quantumforce_code/core/data/repository/DtcRepositoryImpl.kt`
- `core/data/src/main/kotlin/com/quantumforce_code/core/data/repository/VehicleRepositoryImpl.kt`
- `core/data/src/main/kotlin/com/quantumforce_code/core/data/mapper/DtcMapper.kt`
- `core/data/src/main/kotlin/com/quantumforce_code/core/data/mapper/VehicleMapper.kt`

**Documentation:**
- Read: `docs/IMPLEMENTATION_EXAMPLES.md` (section 2)

**Requirements:**
- Implement Repository interfaces from Domain
- Use Mappers to convert Entity ↔ Domain Model
- Proper error handling with Result<T>
- Inject DAOs through constructor

**Acceptance Criteria:**
- [ ] DtcRepositoryImpl реалізує DtcRepository
- [ ] VehicleRepositoryImpl реалізує VehicleRepository
- [ ] Mappers правильно конвертують дані
- [ ] Всі методи інтерфейсу реалізовані
- [ ] Integration tests проходять

---

### 🟡 HIGH PRIORITY

#### Task 3: Setup Hilt Dependency Injection
**Estimated Time:** 2 hours

**Files to Create:**
- `app/src/main/kotlin/com/quantumforce_code/app/App.kt`
- `app/src/main/kotlin/com/quantumforce_code/app/di/DatabaseModule.kt`
- `app/src/main/kotlin/com/quantumforce_code/app/di/RepositoryModule.kt`

**Documentation:**
- Read: `docs/IMPLEMENTATION_EXAMPLES.md` (section 7 - Integration)

**Requirements:**
- @HiltAndroidApp на Application class
- @Module @InstallIn для Database
- @Provides для Repositories
- @Singleton де потрібно

**Acceptance Criteria:**
- [ ] Hilt setup complete
- [ ] Database injected
- [ ] Repositories injectable
- [ ] App компілюється з Hilt

---

#### Task 4: Create DTC Feature - ViewModel
**Estimated Time:** 2-3 hours  
**Dependencies:** Task 2, Task 3 completed

**Files to Create:**
- `features/dtc/src/main/kotlin/com/quantumforce_code/features/dtc/DtcViewModel.kt`
- `features/dtc/src/main/kotlin/com/quantumforce_code/features/dtc/DtcUiState.kt`
- `features/dtc/src/main/kotlin/com/quantumforce_code/features/dtc/DtcEvent.kt`
- `features/dtc/src/main/kotlin/com/quantumforce_code/features/dtc/DtcEffect.kt`

**Documentation:**
- Read: `docs/IMPLEMENTATION_EXAMPLES.md` (section 5 - Features Layer)

**Requirements:**
- MVI pattern (State, Event, Effect)
- StateFlow for UI state
- SharedFlow for one-time effects
- @HiltViewModel annotation
- Inject GetDtcCodesUseCase, ClearDtcCodesUseCase

**Acceptance Criteria:**
- [ ] ViewModel реалізований з MVI
- [ ] UiState має loading, data, error
- [ ] Events обробляються в handleEvent()
- [ ] Effects для navigation/toasts
- [ ] Compiles успішно

---

#### Task 5: Create DTC Feature - UI Screen
**Estimated Time:** 3-4 hours  
**Dependencies:** Task 4 completed

**Files to Create:**
- `app/src/main/kotlin/com/quantumforce_code/app/ui/screens/DtcScreen.kt`
- `app/src/main/kotlin/com/quantumforce_code/app/ui/components/DtcCard.kt`

**Documentation:**
- Read: `docs/IMPLEMENTATION_EXAMPLES.md` (section 6 - UI Layer)

**Requirements:**
- Jetpack Compose
- Material 3 Design
- Dark theme підтримка
- Observe ViewModel state
- Collect effects in LaunchedEffect

**Acceptance Criteria:**
- [ ] DtcScreen composable created
- [ ] LazyColumn shows DTC list
- [ ] DtcCard shows code, description, severity
- [ ] FAB для scan action
- [ ] Loading/Error states shown
- [ ] Compiles and shows on emulator

---

### 🟢 MEDIUM PRIORITY

#### Task 6: Create Dashboard Screen
**Estimated Time:** 2-3 hours

**Files to Create:**
- `app/src/main/kotlin/com/quantumforce_code/app/ui/screens/DashboardScreen.kt`

**Requirements:**
- Show vehicle info card
- Show total DTCs count
- Show connection status
- Navigation to other screens

---

#### Task 7: Setup Navigation
**Estimated Time:** 1-2 hours

**Files to Create:**
- `app/src/main/kotlin/com/quantumforce_code/app/navigation/NavGraph.kt`
- `app/src/main/kotlin/com/quantumforce_code/app/navigation/Destinations.kt`

---

## 🔨 Як виконувати завдання

### 1. Відкрийте Codespace:
```bash
gh codespace create --repo MixaJuba/QuantumForce_Code
gh codespace code --codespace <name>
```

### 2. Візьміть завдання:
- Створіть коментар в Issue: "Taking this task. ETA: [time]"

### 3. Створіть гілку:
```bash
git checkout -b feature/data-layer-claude
```

### 4. Використовуйте Claude для кодування:
```
Промпт для Claude:

Створи Room Database для QuantumForce_Code:
- AppDatabase з Room
- DtcEntity та VehicleEntity
- DAOs з CRUD операціями
- Type converters

Використай структуру з docs/IMPLEMENTATION_EXAMPLES.md розділ 2.
Дотримуйся Clean Architecture принципів.
```

### 5. КОМПІЛЯЦІЯ (ваша критична роль!):
```bash
./gradlew clean build
./gradlew test
./gradlew assembleDebug
```

Якщо є помилки - ВИПРАВТЕ їх перед PR!

### 6. Тестування на емуляторі:
```bash
./gradlew installDebug
# Run emulator
```

### 7. Створіть PR:
```bash
git add .
git commit -m "feat(data): Implement Room database and repositories"
git push origin feature/data-layer-claude

# Create PR and tag @cursor for review
```

---

## 🚨 Ваша унікальна відповідальність

### КОМПІЛЯЦІЯ та DEBUGGING

Ви - **єдиний AI з повним Codespace доступом**. Це означає:

✅ **Ви маєте:**
- Запускати `./gradlew build`
- Виправляти compilation errors
- Тестувати на емуляторі
- Перевіряти що все працює разом

✅ **Коли інші AI створюють PR:**
1. Copilot створив Domain entities → Ви перевіряєте компіляцію
2. Codex створив Protocol → Ви інтегруєте та тестуєте
3. Будь-який код → Ви гарантуєте що проект BUILD успішний

✅ **Workflow перевірки:**
```bash
# Коли новий PR створений:
git fetch origin
git checkout pr-branch

# Компіляція
./gradlew clean build

# Якщо помилки:
# 1. Виправте їх
# 2. Commit fixes
# 3. Коментар в PR що саме виправили

# Якщо OK:
# Коментар: "✅ Build successful. Ready for merge."
```

---

## ✅ Чек-лист перед PR

**Build:**
- [ ] `./gradlew clean build` успішний
- [ ] Немає compilation errors
- [ ] Немає warnings (або пояснені)
- [ ] Lint checks пройдені

**Tests:**
- [ ] Unit tests проходять
- [ ] Integration tests проходять (якщо є)
- [ ] Coverage reasonable (>60%)

**Runtime:**
- [ ] Додаток запускається на емуляторі
- [ ] Основна функціональність працює
- [ ] Немає crashes

**Code Quality:**
- [ ] KDoc коментарі додані
- [ ] Kotlin code conventions дотримані
- [ ] Hilt правильно налаштований

**Documentation:**
- [ ] README оновлено якщо потрібно
- [ ] CHANGELOG записано

---

## 🛠️ Налаштування Codespace

### Рекомендовані розширення:
```json
{
  "recommendations": [
    "kotlin.kotlin",
    "vscjava.vscode-gradle",
    "GitHub.copilot",
    "ms-azuretools.vscode-docker"
  ]
}
```

### Налаштування JDK:
```bash
# Використовуйте JDK 17
export JAVA_HOME=/usr/lib/jvm/java-17-openjdk
```

---

## 📚 Ваші головні ресурси

**Must Read Before Starting:**
1. `docs/IMPLEMENTATION_EXAMPLES.md` - ВАШ ГОЛОВНИЙ ПОСІБНИК
2. `docs/MODULAR_ARCHITECTURE_GUIDE.md` - Архітектура
3. `docs/AI_AGENT_IMPLEMENTATION_GUIDE.md` - Покрокові інструкції

**Для кожного типу роботи:**
- Data Layer → `IMPLEMENTATION_EXAMPLES.md` section 2
- UI Layer → `IMPLEMENTATION_EXAMPLES.md` section 6
- DI → `IMPLEMENTATION_EXAMPLES.md` section 7
- ViewModels → `IMPLEMENTATION_EXAMPLES.md` section 5

---

## 💬 Комунікація

**Для координації:**
- Tag `@cursor` для архітектурних питань
- Tag `@copilot` якщо потрібні зміни в Domain
- Tag `@codex` якщо проблеми з Transport/Protocol

**Для build проблем:**
- Створіть Issue з тегом `build-failure`
- Опишіть помилку компіляції
- Що вже спробували
- Tag `@cursor` для допомоги

---

## 📊 Ваш Прогрес

**Поточний статус:** 🔴 CRITICAL TASKS WAITING  
**Призначені завдання:** 7 tasks  
**Estimated Total Time:** 15-20 hours  
**Target:** Завершити Task 0-2 цього тижня

**Пріоритет виконання:**
1. 🔴 Task 0 (Build Setup) - ЗАРАЗ
2. 🔴 Task 1 (Room Database) - ПОТІМ
3. 🔴 Task 2 (Repositories) - ДАЛІ
4. 🟡 Task 3 (Hilt DI)
5. 🟡 Task 4-5 (DTC Feature)

---

## 🎯 Очікування від вас

Як **Full-Stack Developer та Builder** вашої команди:

✅ **Робіть:**
- Створюйте повнофункціональні модулі
- Інтегруйте код від інших AI
- Компілюйте та тестуйте ВСЕ
- Будьте "tech lead" для білд процесу
- Повідомляйте про проблеми рано

❌ **Не робіть:**
- Не пишіть Domain entities (це Copilot)
- Не пишіть Transport/Protocol (це Codex)
- Не merge без успішної компіляції
- Не ігноруйте warnings

---

**Координатор:** @cursor  
**Tech Questions:** Create Issue з `@cursor`  
**Build Problems:** Issue з тегом `build-failure`

Ви - ключовий гравець команди! Успіхів! 🚀
