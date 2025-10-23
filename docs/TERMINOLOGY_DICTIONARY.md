# 📖 Унікальний Словник Термінів | Terminology Dictionary

**Єдине джерело правди для термінології проєкту QuantumForce_Code**

*Single source of truth for QuantumForce_Code project terminology*

---

## 🎯 Призначення | Purpose

Цей документ забезпечує **узгодженість термінології** у всій документації, коді та комунікації. Кожен термін має:
- ✅ **Канонічну назву** (українською та англійською)
- ✅ **Визначення** з поясненням
- ✅ **Контекст використання** в проєкті
- ✅ **Синоніми та похідні форми**
- ✅ **Посилання на документацію** де термін використовується

---

## 📋 Правила використання | Usage Rules

### Для авторів документації:
1. **Завжди** використовуйте канонічну форму терміна при першій згадці
2. **Додавайте посилання** на цей словник при першій згадці складного терміна
3. **Оновлюйте словник** при введенні нових термінів
4. **Уникайте синонімів** - один концепт = один термін

### Для розробників:
1. **Називайте класи, методи, змінні** відповідно до канонічних назв
2. **Використовуйте англійську канонічну форму** в коді
3. **Додавайте KDoc/Javadoc** з посиланням на термін у словнику

### Для AI-агентів:
1. **Перевіряйте словник** перед використанням нових термінів
2. **Звіряйтеся з канонічними формами** при генерації коду
3. **Пропонуйте нові терміни** через Pull Request до цього документа

---

## 🔤 Формат запису | Entry Format

```markdown
### [Термін UA] | [Term EN]
**Канонічна форма:** [Форма для використання в документації]  
**Код:** `[Форма для використання в коді]`  
**Категорія:** [Архітектура/Домен/Протокол/UI/...]  
**Визначення:** [Чітке пояснення]  
**Контекст проєкту:** [Як використовується в нашому проєкті]  
**Синоніми:** [Альтернативні назви - НЕ використовувати]  
**Посилання:** [Документи де зустрічається]  
**Приклад:** [Приклад використання]
```

---

## 📚 Словник | Dictionary

### A. Архітектура та Дизайн | Architecture & Design

---

#### Чиста Архітектура | Clean Architecture
**Канонічна форма:** Clean Architecture  
**Код:** N/A (архітектурний патерн)  
**Категорія:** Архітектура  
**Визначення:** Архітектурний підхід, що розділяє систему на концентричні шари з чіткою ієрархією залежностей (від зовнішніх до внутрішніх)  
**Контекст проєкту:** Базова архітектурна парадигма QuantumForce_Code з 4 шарами: Presentation, Domain, Data, Infrastructure  
**Синоніми:** Onion Architecture, Hexagonal Architecture (НЕ використовувати - є тонкі відмінності)  
**Посилання:**
- [MODULAR_ARCHITECTURE_GUIDE.md#принципи-clean-architecture](MODULAR_ARCHITECTURE_GUIDE.md#принципи-clean-architecture)
- [architecture.md#архітектурні-шари](architecture.md#архітектурні-шари)
- [EVIDENCE_LEDGER.md#A001](EVIDENCE_LEDGER.md#A001)

**Приклад:**
```kotlin
// Clean Architecture: Domain не залежить від Data
// ✅ Правильно - Domain визначає інтерфейс
interface DtcRepository { 
    suspend fun getDtcs(vin: String): Result<List<Dtc>> 
}

// Data імплементує інтерфейс Domain
class DtcRepositoryImpl(
    private val dao: DtcDao
) : DtcRepository { ... }
```

---

#### Шар Презентації | Presentation Layer
**Канонічна форма:** Presentation Layer  
**Код:** `presentation`, `features/*` (package names)  
**Категорія:** Архітектура  
**Визначення:** Зовнішній шар Clean Architecture, відповідальний за UI та взаємодію з користувачем  
**Контекст проєкту:** Містить Jetpack Compose UI, ViewModels, Navigation. Залежить від Domain Layer  
**Синоніми:** UI Layer, View Layer (обмежено використовувати)  
**Посилання:**
- [MODULAR_ARCHITECTURE_GUIDE.md#архітектурні-шари](MODULAR_ARCHITECTURE_GUIDE.md#архітектурні-шари)
- [architecture.md#presentation-layer](architecture.md#presentation-layer)

**Приклад:**
```kotlin
// Presentation Layer: ViewModel
class DtcViewModel(
    private val getDtcsUseCase: GetDtcsUseCase
) : ViewModel() {
    val dtcState: StateFlow<DtcUiState> = ...
}
```

---

#### Шар Домену | Domain Layer
**Канонічна форма:** Domain Layer  
**Код:** `core.domain` (module/package)  
**Категорія:** Архітектура  
**Визначення:** Центральний шар Clean Architecture, що містить бізнес-логіку, сутності та інтерфейси  
**Контекст проєкту:** Pure Kotlin модуль без Android залежностей. Визначає UseCase, Entity, Repository interfaces  
**Синоніми:** Business Logic Layer (обмежено), Core Layer (НЕ використовувати - неоднозначно)  
**Посилання:**
- [MODULAR_ARCHITECTURE_GUIDE.md#core-domain-module](MODULAR_ARCHITECTURE_GUIDE.md#core-domain-module)
- [INTERFACE_CONTRACTS.md#domain-layer-interfaces](INTERFACE_CONTRACTS.md#domain-layer-interfaces)

**Приклад:**
```kotlin
// Domain Layer: UseCase
class GetDtcsUseCase(
    private val repository: DtcRepository // Залежить від інтерфейсу
) : UseCase<String, List<Dtc>>() {
    override suspend fun execute(vin: String) = repository.getDtcs(vin)
}
```

---

#### Шар Даних | Data Layer
**Канонічна форма:** Data Layer  
**Код:** `core.data` (module/package)  
**Категорія:** Архітектура  
**Визначення:** Шар Clean Architecture, відповідальний за доступ до даних (БД, мережа, файлова система)  
**Контекст проєкту:** Містить Repository implementations, Room DAOs, Data mappers. Залежить від Domain Layer  
**Синоніми:** Persistence Layer (занадто вузько), Infrastructure Data (НЕ використовувати)  
**Посилання:**
- [MODULAR_ARCHITECTURE_GUIDE.md#data-layer](MODULAR_ARCHITECTURE_GUIDE.md#data-layer)
- [architecture.md#data-layer](architecture.md#data-layer)

**Приклад:**
```kotlin
// Data Layer: Repository Implementation
class DtcRepositoryImpl(
    private val dtcDao: DtcDao,
    private val mapper: DtcMapper
) : DtcRepository {
    override suspend fun getDtcs(vin: String): Result<List<Dtc>> {
        return dtcDao.getDtcsByVin(vin).map { mapper.toDomain(it) }
    }
}
```

---

#### UseCase (Випадок Використання)
**Канонічна форма:** UseCase  
**Код:** `UseCase<P, R>` (base class), `*UseCase` (implementations)  
**Категорія:** Архітектура / Domain  
**Визначення:** Клас або інтерфейс, що інкапсулює одну конкретну бізнес-операцію  
**Контекст проєкту:** Базовий абстрактний клас у Domain Layer. Всі бізнес-операції реалізуються як UseCase  
**Синоніми:** Interactor (не використовувати), Command (різні патерни)  
**Посилання:**
- [MODULAR_ARCHITECTURE_GUIDE.md#usecase](MODULAR_ARCHITECTURE_GUIDE.md#usecase)
- [INTERFACE_CONTRACTS.md#usecase-p-r](INTERFACE_CONTRACTS.md#usecase-p-r)

**Приклад:**
```kotlin
// UseCase pattern
class ClearDtcCodesUseCase(
    private val repository: DtcRepository
) : UseCase<String, Boolean>() {
    override suspend fun execute(vehicleId: String): Result<Boolean> {
        return repository.clearDtcCodes(vehicleId)
    }
}
```

---

### B. Доменна Модель | Domain Model

---

#### DTC (Diagnostic Trouble Code) - Код Діагностичної Помилки
**Канонічна форма:** DTC (Diagnostic Trouble Code)  
**Код:** `DtcCode`, `Dtc` (domain entity)  
**Категорія:** Домен  
**Визначення:** Стандартизований код помилки, що генерується бортовим комп'ютером автомобіля при виявленні несправності  
**Контекст проєкту:** Основна доменна сутність. База даних містить 50,000+ DTC з описами та рекомендаціями  
**Синоніми:** Error Code, Fault Code, Trouble Code (використовувати обмежено)  
**Посилання:**
- [glossary.md#dtc](glossary.md#dtc)
- [EVIDENCE_LEDGER.md#C002](EVIDENCE_LEDGER.md#C002)

**Формат:** `[P/B/C/U][0-3][0-9A-F]{3}`  
- P = Powertrain (двигун/трансмісія)
- B = Body (кузов)
- C = Chassis (підвіска/гальма)
- U = Network (мережа)

**Приклад:**
```kotlin
data class DtcCode(
    val code: String,              // "P0420"
    val description: String,        // "Catalyst System Efficiency Below Threshold"
    val severity: DtcSeverity,     // CRITICAL, HIGH, MEDIUM, LOW
    val category: DtcCategory,     // POWERTRAIN, BODY, CHASSIS, NETWORK
    val timestamp: Instant,
    val status: DtcStatus          // ACTIVE, PENDING, CLEARED
)
```

---

#### Транспортне Засіб | Vehicle
**Канонічна форма:** Vehicle  
**Код:** `Vehicle` (domain entity)  
**Категорія:** Домен  
**Визначення:** Автомобіль, для якого проводиться діагностика  
**Контекст проєкту:** Доменна сутність що зберігає інформацію про авто (VIN, марка, модель, рік)  
**Синоніми:** Car, Automobile (НЕ використовувати в коді)  
**Посилання:**
- [INTERFACE_CONTRACTS.md#vehiclerepository](INTERFACE_CONTRACTS.md#vehiclerepository)

**Приклад:**
```kotlin
data class Vehicle(
    val id: String,
    val vin: String,               // Vehicle Identification Number (17 символів)
    val make: String,              // "Toyota"
    val model: String,             // "Camry"
    val year: Int,                 // 2020
    val engineType: EngineType,    // GASOLINE, DIESEL, ELECTRIC, HYBRID
    val lastDiagnostic: Instant?
)
```

---

#### Сесія Діагностики | Diagnostic Session
**Канонічна форма:** Diagnostic Session  
**Код:** `DiagnosticSession` (domain entity)  
**Категорія:** Домен  
**Визначення:** Окрема сесія підключення до автомобіля для проведення діагностики  
**Контекст проєкту:** Зберігає історію діагностик, зчитані DTC, параметри сесії  
**Синоніми:** Scan Session, Diagnostic Scan (обмежено)  
**Посилання:**
- [INTERFACE_CONTRACTS.md#diagnosticsessionrepository](INTERFACE_CONTRACTS.md#diagnosticsessionrepository)

**Приклад:**
```kotlin
data class DiagnosticSession(
    val id: String,
    val vehicleId: String,
    val startTime: Instant,
    val endTime: Instant?,
    val status: SessionStatus,      // ACTIVE, COMPLETED, CANCELLED, FAILED
    val dtcCodes: List<DtcCode>,
    val liveDataSnapshots: List<LiveDataSnapshot>
)
```

---

### C. Протоколи та Обладнання | Protocols & Hardware

---

#### OBD-II (On-Board Diagnostics II)
**Канонічна форма:** OBD-II  
**Код:** `Obd2`, `OBD2` (у константах)  
**Категорія:** Протокол  
**Визначення:** Стандарт автомобільної діагностики, обов'язковий для всіх авто після 1996 (США) / 2001 (Європа)  
**Контекст проєкту:** Базовий протокол для комунікації з автомобілем через діагностичний порт  
**Синоніми:** OBD2, OBDII (допустимі варіанти написання)  
**Посилання:**
- [glossary.md#obd-ii](glossary.md#obd-ii)
- [EVIDENCE_LEDGER.md#E001](EVIDENCE_LEDGER.md#E001)

**Стандарти:**
- ISO 9141 (K-Line)
- ISO 14230 (KWP2000)
- ISO 15765 (CAN)
- SAE J1850 (PWM, VPW)

**Приклад:**
```kotlin
interface Obd2Protocol {
    suspend fun readDtcs(): Result<List<DtcCode>>
    suspend fun clearDtcs(): Result<Boolean>
    suspend fun readPid(pid: Pid): Result<PidValue>
}
```

---

#### PID (Parameter ID) - Ідентифікатор Параметра
**Канонічна форма:** PID (Parameter ID)  
**Код:** `Pid` (domain entity)  
**Категорія:** Протокол  
**Визначення:** Унікальний ідентифікатор параметра, який можна зчитати з автомобіля в реальному часі  
**Контекст проєкту:** Використовується для отримання live data (RPM, швидкість, температура, тощо)  
**Синоніми:** Parameter Identifier (повна форма)  
**Посилання:**
- [glossary.md#pid](glossary.md#pid)

**Приклади PIDs:**
- 0x0C = Engine RPM
- 0x0D = Vehicle Speed
- 0x05 = Engine Coolant Temperature

**Приклад:**
```kotlin
data class Pid(
    val id: Int,                   // 0x0C
    val name: String,              // "Engine RPM"
    val unit: String,              // "rpm"
    val formula: String,           // "((A*256)+B)/4"
    val minValue: Double,
    val maxValue: Double
)
```

---

#### ELM327
**Канонічна форма:** ELM327  
**Код:** `Elm327Adapter`, `ELM327` (у константах)  
**Категорія:** Обладнання  
**Визначення:** Найпоширеніший мікроконтролер для OBD-II адаптерів, що перетворює OBD протоколи в послідовний інтерфейс  
**Контекст проєкту:** Основний тип адаптера для MVP. Підтримує команди AT та OBD  
**Синоніми:** ELM chip (обмежено)  
**Посилання:**
- [README.md#технології](../README.md#технології)
- [ASSUMPTION_REGISTER.md#S5.001](ASSUMPTION_REGISTER.md#S5.001)

**AT Commands:**
- `ATZ` - Reset
- `ATE0` - Echo off
- `ATH1` - Headers on
- `ATSP0` - Auto protocol

**Приклад:**
```kotlin
class Elm327Adapter(
    private val transport: Transport
) : Obd2Adapter {
    suspend fun initialize(): Result<Unit> {
        sendCommand("ATZ")   // Reset
        sendCommand("ATE0")  // Echo off
        sendCommand("ATSP0") // Auto protocol
    }
}
```

---

#### UDS (Unified Diagnostic Services)
**Канонічна форма:** UDS (Unified Diagnostic Services)  
**Код:** `Uds`, `UDS` (у константах)  
**Категорія:** Протокол  
**Визначення:** Стандарт ISO 14229 для розширеної автомобільної діагностики, що використовується виробниками  
**Контекст проєкту:** Додатковий протокол для глибокої діагностики (після MVP)  
**Синоніми:** ISO 14229 (номер стандарту)  
**Посилання:**
- [EVIDENCE_LEDGER.md#E002](EVIDENCE_LEDGER.md#E002)

**Services:**
- 0x10 - Diagnostic Session Control
- 0x19 - Read DTC Information
- 0x22 - Read Data By Identifier
- 0x27 - Security Access

---

### D. UI та Користувацький Досвід | UI & UX

---

#### Jetpack Compose
**Канонічна форма:** Jetpack Compose  
**Код:** `@Composable` (annotation)  
**Категорія:** UI Framework  
**Визначення:** Сучасний декларативний UI toolkit для Android від Google  
**Контекст проєкту:** Основний інструмент для створення UI в Presentation Layer  
**Синоніми:** Compose (короткий варіант - допустимо в контексті)  
**Посилання:**
- [README.md#технології](../README.md#технології)
- [EVIDENCE_LEDGER.md#B002](EVIDENCE_LEDGER.md#B002)

**Приклад:**
```kotlin
@Composable
fun DtcListScreen(
    viewModel: DtcViewModel,
    onDtcClick: (DtcCode) -> Unit
) {
    val state by viewModel.dtcState.collectAsState()
    
    LazyColumn {
        items(state.dtcCodes) { dtc ->
            DtcCard(dtc = dtc, onClick = { onDtcClick(dtc) })
        }
    }
}
```

---

#### ViewModel
**Канонічна форма:** ViewModel  
**Код:** `*ViewModel` (class suffix), extends `ViewModel`  
**Категорія:** Архітектура / UI  
**Визначення:** Компонент Architecture Components, що зберігає UI state та виживає при configuration changes  
**Контекст проєкту:** Міст між Presentation та Domain layers. Викликає UseCases, конвертує domain models у UI state  
**Синоніми:** Presenter (різні патерни - НЕ використовувати)  
**Посилання:**
- [MODULAR_ARCHITECTURE_GUIDE.md#presentation-layer](MODULAR_ARCHITECTURE_GUIDE.md#presentation-layer)

**Приклад:**
```kotlin
class DtcViewModel(
    private val getDtcsUseCase: GetDtcsUseCase,
    private val clearDtcsUseCase: ClearDtcsUseCase
) : ViewModel() {
    private val _state = MutableStateFlow<DtcUiState>(DtcUiState.Loading)
    val state: StateFlow<DtcUiState> = _state.asStateFlow()
}
```

---

### E. Dependency Injection та Конфігурація | DI & Configuration

---

#### Hilt
**Канонічна форма:** Hilt  
**Код:** `@HiltAndroidApp`, `@AndroidEntryPoint`, `@Inject`  
**Категорія:** Dependency Injection  
**Визначення:** DI framework для Android, побудований поверх Dagger  
**Контекст проєкту:** Основний механізм DI для всіх шарів архітектури  
**Синоніми:** Dagger-Hilt (повна назва - НЕ використовувати)  
**Посилання:**
- [README.md#технології](../README.md#технології)
- [EVIDENCE_LEDGER.md#B004](EVIDENCE_LEDGER.md#B004)

**Приклад:**
```kotlin
@Module
@InstallIn(SingletonComponent::class)
object DataModule {
    @Provides
    @Singleton
    fun provideDtcRepository(
        dao: DtcDao
    ): DtcRepository = DtcRepositoryImpl(dao)
}
```

---

### F. AI та Розробка | AI & Development

---

#### AI-агент | AI Agent
**Канонічна форма:** AI Agent  
**Код:** N/A (концепція розробки)  
**Категорія:** Development Process  
**Визначення:** Автономна AI система (Copilot, Claude, Codex, Gemini), що виконує роль розробника в проєкті  
**Контекст проєкту:** 5 спеціалізованих агентів розробляють різні шари архітектури  
**Синоніми:** AI Developer, Code Assistant (НЕ використовувати - різні концепції)  
**Посилання:**
- [AI_AGENT_ROLE.md](AI_AGENT_ROLE.md)
- [README.md#ai-driven-development](../README.md#ai-driven-development)
- [EVIDENCE_LEDGER.md#D001](EVIDENCE_LEDGER.md#D001)

**Ролі:**
- Cursor AI - Project Manager
- GitHub Copilot - Domain Developer
- Claude - Full-Stack Developer (Data/UI)
- OpenAI Codex - Infrastructure Developer
- Google Gemini - QA Specialist

---

#### GitHub Codespaces
**Канонічна форма:** GitHub Codespaces  
**Код:** N/A (environment)  
**Категорія:** Development Environment  
**Визначення:** Хмарне середовище розробки від GitHub, що надає повноцінний VSCode/IDE у браузері  
**Контекст проєкту:** Рекомендоване середовище для AI-агентів та контрибуторів  
**Синоніми:** Codespaces (короткий варіант - допустимо)  
**Посилання:**
- [ASSUMPTION_REGISTER.md#S6.001](ASSUMPTION_REGISTER.md#S6.001)

**Переваги:**
- Не потрібна локальна установка Android Studio
- Уніфіковане середовище для всіх
- Інтеграція з GitHub Copilot

---

### G. Тестування | Testing

---

#### Unit Test - Модульний Тест
**Канонічна форма:** Unit Test  
**Код:** `*Test.kt` (test files), `@Test` annotation  
**Категорія:** Testing  
**Визначення:** Тест, що перевіряє ізольовану одиницю коду (функцію, метод, клас)  
**Контекст проєкту:** Основний тип тестів для Domain та Data layers  
**Синоніми:** Unitary Test (НЕ використовувати)  
**Посилання:**
- [testing-guidelines.md](testing-guidelines.md)

**Приклад:**
```kotlin
class GetDtcsUseCaseTest {
    @Test
    fun `execute returns success when repository succeeds`() = runTest {
        // Given
        val repository = mockk<DtcRepository>()
        coEvery { repository.getDtcs(any()) } returns Result.success(emptyList())
        
        // When
        val useCase = GetDtcsUseCase(repository)
        val result = useCase.execute("VIN123")
        
        // Then
        assertTrue(result.isSuccess)
    }
}
```

---

## 📊 Статистика словника | Dictionary Statistics

**Всього термінів:** 24  
**Категорій:** 7
- A. Архітектура: 5 термінів
- B. Доменна Модель: 3 терміни
- C. Протоколи та Обладнання: 5 термінів
- D. UI та UX: 2 терміни
- E. Dependency Injection: 1 термін
- F. AI та Розробка: 2 терміни
- G. Тестування: 1 термін

**Остання оновлення:** 2024-10-23

---

## 🔍 Швидкий пошук | Quick Reference

### За категоріями:
- **Архітектура:** Clean Architecture, Presentation Layer, Domain Layer, Data Layer, UseCase
- **Домен:** DTC, Vehicle, Diagnostic Session
- **Протоколи:** OBD-II, PID, ELM327, UDS
- **UI:** Jetpack Compose, ViewModel
- **DI:** Hilt
- **AI:** AI Agent, GitHub Codespaces
- **Testing:** Unit Test

### За шарами архітектури:
- **Presentation:** Jetpack Compose, ViewModel
- **Domain:** UseCase, DTC, Vehicle, Diagnostic Session
- **Data:** Repository, Room (у B002)
- **Infrastructure:** OBD-II, ELM327, UDS, PID

---

## ✏️ Як додати новий термін | How to Add New Term

1. **Визначте категорію** (A-G або створіть нову)
2. **Перевірте** чи термін не є синонімом існуючого
3. **Заповніть шаблон:**

```markdown
#### [Термін UA] | [Term EN]
**Канонічна форма:** [Як писати в документації]  
**Код:** `[Як називати в коді]`  
**Категорія:** [Категорія]  
**Визначення:** [Чітке пояснення]  
**Контекст проєкту:** [Як використовується у нас]  
**Синоніми:** [Що НЕ використовувати]  
**Посилання:** [Документи]  
**Приклад:** [Код або текст]
```

4. **Оновіть статистику** та швидкий пошук
5. **Створіть PR** з описом чому термін потрібен

---

## 🔗 Зв'язки з іншими документами | Related Documents

- **[Evidence Ledger](EVIDENCE_LEDGER.md)** - перевірка тверджень про терміни
- **[Glossary](glossary.md)** - пояснення термінів для новачків
- **[INTERFACE_CONTRACTS.md](INTERFACE_CONTRACTS.md)** - технічні контракти
- **[Verification Paths](VERIFICATION_PATHS.md)** - як перевірити використання термінів

---

## ⚠️ Важливі примітки | Important Notes

### Узгодженість назв у коді:
- **Класи та інтерфейси:** PascalCase (`DtcCode`, `VehicleRepository`)
- **Методи та властивості:** camelCase (`getDtcs`, `vehicleId`)
- **Пакети:** lowercase (`core.domain`, `hardware.transport`)
- **Константи:** UPPER_SNAKE_CASE (`MAX_RETRY_COUNT`)

### Узгодженість у документації:
- Перша згадка: повна форма + абревіатура (`OBD-II (On-Board Diagnostics II)`)
- Подальші згадки: абревіатура (`OBD-II`)
- Посилання на словник при першій згадці складного терміна

---

**Автор:** Copilot Coding Agent  
**Версія:** 1.0.0  
**Дата створення:** 2024-10-23  
**Формат:** Markdown  
**Ліцензія:** Відповідно до основного репозиторію
