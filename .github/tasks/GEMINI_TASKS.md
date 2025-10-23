# 🤖 Google Gemini - Завдання | Tasks

**AI Agent:** Google Gemini  
**Середовище:** Google AI Studio / Gemini API  
**Спеціалізація:** Testing, QA, Documentation  
**Координатор:** Cursor AI

---

## 🎯 Ваша Зона Відповідальності

### Директорії де ви працюєте:
```
**/src/test/kotlin/          # Unit tests
**/src/androidTest/kotlin/   # Integration tests
docs/examples/               # Code examples
docs/                        # Documentation updates
```

### Ваша місія:
- 🧪 **Quality Assurance** - Всебічне тестування
- 📝 **Test Generation** - Автоматична генерація тестів
- 📚 **Documentation** - Оновлення та розширення docs
- 🎯 **Examples Creation** - Приклади використання

---

## 📋 Поточні Завдання (Priority Order)

### 🟢 LOW PRIORITY (Чекає на готовий код)

#### Task 1: Create Unit Tests for Domain Layer
**Estimated Time:** 4-6 hours  
**Dependencies:** Copilot має закінчити Domain layer

**Files to Create:**
- `core/domain/src/test/kotlin/com/quantumforce_code/core/domain/usecase/GetDtcCodesUseCaseTest.kt`
- `core/domain/src/test/kotlin/com/quantumforce_code/core/domain/usecase/ClearDtcCodesUseCaseTest.kt`
- `core/domain/src/test/kotlin/com/quantumforce_code/core/domain/usecase/GetVehicleUseCaseTest.kt`
- `core/domain/src/test/kotlin/com/quantumforce_code/core/domain/usecase/SaveVehicleUseCaseTest.kt`
- `core/domain/src/test/kotlin/com/quantumforce_code/core/domain/model/DtcCodeTest.kt`
- `core/domain/src/test/kotlin/com/quantumforce_code/core/domain/model/VehicleTest.kt`

**Documentation:**
- Read: `docs/testing-guidelines.md`
- Read: Code створений Copilot

**Requirements:**
- JUnit 5 (JUnit Jupiter)
- MockK для mocking
- Kotlinx-coroutines-test для suspend функцій
- AssertJ або Kotest для assertions
- Test Coverage > 80%

**Test Structure:**
```kotlin
class GetDtcCodesUseCaseTest {
    
    private lateinit var dtcRepository: DtcRepository
    private lateinit var useCase: GetDtcCodesUseCase
    
    @BeforeEach
    fun setup() {
        dtcRepository = mockk()
        useCase = GetDtcCodesUseCase(dtcRepository)
    }
    
    @Test
    fun `invoke returns success when repository returns DTCs`() = runTest {
        // Given
        val vehicleId = "VIN123"
        val expectedDtcs = listOf(
            DtcCode(code = "P0420", description = "Catalyst System Efficiency", ...)
        )
        coEvery { dtcRepository.getDtcCodes(vehicleId, any()) } returns expectedDtcs
        
        // When
        val result = useCase(GetDtcCodesUseCase.Params(vehicleId))
        
        // Then
        assertThat(result.isSuccess).isTrue()
        assertThat(result.getOrNull()).isEqualTo(expectedDtcs)
    }
    
    @Test
    fun `invoke returns failure when repository throws exception`() = runTest {
        // Given
        val vehicleId = "VIN123"
        coEvery { dtcRepository.getDtcCodes(any(), any()) } throws IOException("Network error")
        
        // When
        val result = useCase(GetDtcCodesUseCase.Params(vehicleId))
        
        // Then
        assertThat(result.isFailure).isTrue()
    }
}
```

**Acceptance Criteria:**
- [ ] Tests for all UseCases (happy path)
- [ ] Tests for all error scenarios
- [ ] Tests for edge cases (empty lists, null values)
- [ ] Tests for domain model methods
- [ ] All tests pass
- [ ] Coverage > 80% для Domain layer
- [ ] Clear test names (describe behavior)
- [ ] Proper use of Given/When/Then

**Промпт для себе:**
```
Створи comprehensive unit tests для Domain Layer:

Код для тестування: [attach Domain layer code]

Створи тести для:
1. GetDtcCodesUseCase:
   - Success scenario
   - Repository failure
   - Empty results
   - Invalid parameters

2. ClearDtcCodesUseCase:
   - Success scenario
   - Failure scenario
   - Permission denied

3. Domain models:
   - DtcCode.requiresImmediateAttention()
   - Vehicle.supportsAdvancedDiagnostics()
   - DiagnosticSession.complete()

Використай:
- JUnit 5
- MockK для mocking
- runTest для coroutines
- AssertJ для assertions

Структура: Given/When/Then
Naming: backtick style з описом behavior
```

---

#### Task 2: Create Integration Tests for Data Layer
**Estimated Time:** 3-5 hours  
**Dependencies:** Claude закінчив Data layer

**Files to Create:**
- `core/data/src/androidTest/kotlin/com/quantumforce_code/core/data/repository/DtcRepositoryImplTest.kt`
- `core/data/src/androidTest/kotlin/com/quantumforce_code/core/data/repository/VehicleRepositoryImplTest.kt`
- `core/data/src/androidTest/kotlin/com/quantumforce_code/core/data/db/dao/DtcDaoTest.kt`

**Requirements:**
- In-memory Room database
- Real DAO testing (не моки)
- Data integrity tests
- Transaction tests

**Example:**
```kotlin
@RunWith(AndroidJUnit4::class)
class DtcRepositoryImplTest {
    
    private lateinit var database: AppDatabase
    private lateinit var repository: DtcRepositoryImpl
    
    @Before
    fun setup() {
        val context = ApplicationProvider.getApplicationContext<Context>()
        database = Room.inMemoryDatabaseBuilder(context, AppDatabase::class.java)
            .allowMainThreadQueries()
            .build()
        repository = DtcRepositoryImpl(database.dtcDao(), DtcMapper())
    }
    
    @After
    fun tearDown() {
        database.close()
    }
    
    @Test
    fun saveDtcCode_and_retrieve_successfully() = runBlocking {
        // Given
        val dtc = DtcCode(code = "P0420", description = "...", ...)
        
        // When
        repository.saveDtcCode(dtc)
        val retrieved = repository.getDtcByCode("P0420")
        
        // Then
        assertThat(retrieved).isEqualTo(dtc)
    }
}
```

**Acceptance Criteria:**
- [ ] Tests з реальною Room database (in-memory)
- [ ] CRUD operations tested
- [ ] Data mappers tested
- [ ] Transaction behavior tested
- [ ] All tests pass на emulator
- [ ] Coverage > 70%

---

#### Task 3: Create UI Tests (Compose)
**Estimated Time:** 3-4 hours  
**Dependencies:** Claude створив UI screens

**Files to Create:**
- `app/src/androidTest/kotlin/com/quantumforce_code/app/ui/DtcScreenTest.kt`
- `app/src/androidTest/kotlin/com/quantumforce_code/app/ui/DashboardScreenTest.kt`

**Requirements:**
- Compose Testing framework
- UI behavior tests
- User interaction tests
- State change tests

**Example:**
```kotlin
class DtcScreenTest {
    
    @get:Rule
    val composeTestRule = createComposeRule()
    
    @Test
    fun dtcScreen_loading_state_shows_progress() {
        // Given
        val uiState = DtcUiState(isLoading = true)
        
        // When
        composeTestRule.setContent {
            DtcScreen(uiState = uiState, onEvent = {})
        }
        
        // Then
        composeTestRule.onNodeWithTag("loading_indicator").assertIsDisplayed()
    }
    
    @Test
    fun dtcScreen_displays_dtc_list() {
        // Given
        val dtcs = listOf(
            DtcCode(code = "P0420", ...),
            DtcCode(code = "P0301", ...)
        )
        val uiState = DtcUiState(dtcList = dtcs)
        
        // When
        composeTestRule.setContent {
            DtcScreen(uiState = uiState, onEvent = {})
        }
        
        // Then
        composeTestRule.onNodeWithText("P0420").assertIsDisplayed()
        composeTestRule.onNodeWithText("P0301").assertIsDisplayed()
    }
}
```

---

#### Task 4: Create Test Data Generators
**Estimated Time:** 2-3 hours

**Files to Create:**
- `core/domain/src/test/kotlin/com/quantumforce_code/core/domain/TestDataFactory.kt`
- `core/data/src/test/kotlin/com/quantumforce_code/core/data/TestDatabasePopulator.kt`

**Purpose:**
- Генерація тестових DTC кодів
- Генерація тестових Vehicle об'єктів
- Populate database з realistic даними
- Faker patterns для різних scenarios

**Example:**
```kotlin
object TestDataFactory {
    
    fun createDtcCode(
        code: String = "P0420",
        description: String = "Test DTC",
        severity: DtcCode.Severity = DtcCode.Severity.MEDIUM
    ) = DtcCode(
        code = code,
        description = description,
        severity = severity,
        causes = listOf("Cause 1", "Cause 2"),
        solutions = listOf("Solution 1"),
        affectedSystems = listOf("Engine"),
        timestamp = System.currentTimeMillis()
    )
    
    fun createVehicle(
        id: String = UUID.randomUUID().toString(),
        make: String = "Volkswagen",
        model: String = "Golf"
    ) = Vehicle(
        id = id,
        make = make,
        model = model,
        year = 2015,
        vin = "WVWZZZ1JZXW123456",
        protocol = Vehicle.Protocol.OBD2_ISO15765
    )
    
    fun createDtcList(count: Int = 5) = List(count) {
        createDtcCode(code = "P0${100 + it}")
    }
}
```

---

### 📚 DOCUMENTATION TASKS

#### Task 5: Create Code Examples
**Estimated Time:** 2-3 hours

**Files to Create:**
- `docs/examples/basic-usage.md`
- `docs/examples/dtc-scanning.md`
- `docs/examples/live-data-monitoring.md`

**Content:**
```markdown
# Basic Usage Examples

## Scanning for DTCs

```kotlin
// 1. Get DTC Use Case
val getDtcUseCase = GetDtcCodesUseCase(dtcRepository)

// 2. Execute
val result = getDtcUseCase(
    GetDtcCodesUseCase.Params(vehicleId = "VIN123")
)

// 3. Handle result
result.onSuccess { dtcList ->
    println("Found ${dtcList.size} DTCs")
    dtcList.forEach { dtc ->
        println("${dtc.code}: ${dtc.description}")
    }
}.onFailure { error ->
    println("Error: ${error.message}")
}
```

## Expected Output
```
Found 2 DTCs
P0420: Catalyst System Efficiency Below Threshold
P0301: Cylinder 1 Misfire Detected
```
```

**Acceptance Criteria:**
- [ ] Examples для всіх основних features
- [ ] Step-by-step інструкції
- [ ] Expected outputs показані
- [ ] Error handling examples
- [ ] Code snippets компілюються

---

#### Task 6: Update Documentation
**Estimated Time:** 2-3 hours

**Files to Update:**
- `README.md` - Add "Running Tests" section
- `docs/testing-guidelines.md` - Add test examples
- `docs/IMPLEMENTATION_EXAMPLES.md` - Add test examples

**Content to Add:**
```markdown
## Running Tests

### Unit Tests
```bash
./gradlew test
```

### Integration Tests
```bash
./gradlew connectedAndroidTest
```

### Test Coverage Report
```bash
./gradlew jacocoTestReport
open build/reports/jacoco/test/html/index.html
```

### Test Specific Module
```bash
./gradlew :core:domain:test
./gradlew :core:data:connectedAndroidTest
```
```

---

## 🛠️ Як виконувати завдання

### 1. Отримайте код для тестування:
```bash
git clone https://github.com/MixaJuba/QuantumForce_Code.git
cd QuantumForce_Code

# Pull latest code
git pull origin main
```

### 2. Аналізуйте код:
- Прочитайте implementation files
- Зрозумійте business logic
- Визначте edge cases
- Знайдіть можливі точки failure

### 3. Використовуйте Gemini для генерації:
```
Промпт для Gemini:

Проаналізуй цей код та створи comprehensive unit tests:

[paste code]

Вимоги:
- JUnit 5
- MockK для mocking
- Test coverage > 80%
- Happy path + error scenarios + edge cases
- Given/When/Then structure
- Clear test names

Generate complete test file.
```

### 4. Запустіть тести:
```bash
# Local
./gradlew test

# Ask Claude to run in Codespace if you can't
```

### 5. Створіть PR:
```bash
git checkout -b test/domain-layer-tests-gemini
git add .
git commit -m "test(domain): Add comprehensive unit tests"
git push origin test/domain-layer-tests-gemini
```

---

## ✅ Чек-лист перед PR

**Test Quality:**
- [ ] All tests pass locally (or verified by Claude)
- [ ] Coverage > 70% мінімум (80% ціль)
- [ ] No flaky tests (100% reproducible)
- [ ] Fast execution (< 1s per test)

**Test Coverage:**
- [ ] Happy path scenarios
- [ ] Error scenarios
- [ ] Edge cases (null, empty, invalid)
- [ ] Boundary conditions

**Code Quality:**
- [ ] Clear test names (describe behavior)
- [ ] Given/When/Then structure
- [ ] No code duplication (use @BeforeEach)
- [ ] Proper use of assertions

**Documentation:**
- [ ] Complex tests explained in comments
- [ ] Test data clearly defined
- [ ] Examples updated in docs

---

## 📚 Ресурси для вас

**Must Read:**
1. `docs/testing-guidelines.md`
2. Code створений іншими AI
3. JUnit 5 User Guide
4. MockK Documentation

**Testing Frameworks:**
- JUnit 5 (Jupiter)
- MockK - mocking library
- AssertJ - fluent assertions
- Kotlinx-coroutines-test - testing coroutines
- Compose Testing - UI tests

**Best Practices:**
- Test naming: `methodName_stateUnderTest_expectedBehavior`
- One assertion per test (або пов'язані assertions)
- Arrange/Act/Assert (Given/When/Then)
- Test isolation (кожен тест незалежний)

---

## 💬 Комунікація

**Питання по коду:**
- Tag `@copilot` для Domain code
- Tag `@claude` для Data/UI code
- Tag `@codex` для Protocol code

**Питання по тестах:**
- Tag `@cursor` для стратегії тестування
- Create Issue з тегом `testing-question`

**Bug reports:**
- Якщо знайшли баг в чужому коді - створіть Issue
- Tag відповідного AI автора
- Опишіть як reproduced
- Запропонуйте fix якщо можливо

---

## 📊 Ваш Прогрес

**Поточний статус:** 🟢 WAITING FOR CODE  
**Призначені завдання:** 6 tasks  
**Estimated Total Time:** 16-24 hours  
**Blocked by:** Всі інші AI (потрібен код для тестування)

**Можна почати:**
- ✅ Налаштувати test frameworks
- ✅ Створити TestDataFactory
- ✅ Підготувати test templates
- ✅ Вивчити testing best practices

**Чекаємо:**
- ⏳ Domain code (Copilot)
- ⏳ Data code (Claude)
- ⏳ Protocol code (Codex)
- ⏳ UI code (Claude)

---

## 🎯 Очікування від вас

Як **QA та Documentation Specialist**:

✅ **Робіть:**
- Всебічне тестування (unit, integration, UI)
- Знаходьте bugs перед production
- Генеруйте realistic test data
- Документуйте приклади використання
- Підтримуйте high test coverage
- Будьте "якісний контроль" команди

❌ **Не робіть:**
- Не змінюйте production код (тільки тести)
- Не створюйте flaky tests
- Не ігноруйте failing tests
- Не пишіть tests які тестують implementation details

---

## 🎓 Testing Best Practices

### Test Naming:
```kotlin
// ✅ Good
fun `getDtcCodes returns empty list when no DTCs stored`()
fun `saveDtcCode throws exception when database is locked`()

// ❌ Bad
fun test1()
fun testGetDtcCodes()
```

### Test Structure:
```kotlin
// ✅ Good - Clear Given/When/Then
@Test
fun `clearing DTCs updates database correctly`() = runTest {
    // Given
    val vehicleId = "VIN123"
    repository.saveDtcCode(createDtcCode())
    
    // When
    val result = repository.clearDtcCodes(vehicleId)
    
    // Then
    assertThat(result.isSuccess).isTrue()
    assertThat(repository.getDtcCodes(vehicleId)).isEmpty()
}
```

### Coverage Goals:
- Domain Layer: **> 80%** (критично)
- Data Layer: **> 70%**
- UI Layer: **> 50%** (Compose tests)
- Protocol Layer: **> 60%**

---

## 🚀 Наступні кроки

**Зараз:**
1. Вивчіть testing frameworks
2. Підготуйте test utilities
3. Створіть TestDataFactory

**Коли код готовий:**
1. Візьміть Task 1 (Domain tests)
2. Створіть Draft PR для раннього feedback
3. Iterate на основі code review

---

**Координатор:** @cursor  
**Code Authors:** @copilot @claude @codex  
**Testing Questions:** Create Issue з тегом `@cursor @gemini`

Ви - захисник якості! 🛡️ No bugs shall pass! 🚫🐛
