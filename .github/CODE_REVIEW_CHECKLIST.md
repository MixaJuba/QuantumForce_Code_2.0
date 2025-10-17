# ✅ Code Review Checklist | Чек-лист для Code Review

**Reviewer:** Cursor AI  
**Purpose:** Гарантувати якість коду перед merge

---

## 🎯 Загальні Принципи Review

### Що перевіряємо:
1. ✅ **Correctness** - Код робить те, що має робити
2. ✅ **Architecture** - Дотримання Clean Architecture
3. ✅ **Code Quality** - Читабельність, maintainability
4. ✅ **Tests** - Наявність та якість тестів
5. ✅ **Documentation** - KDoc, README updates
6. ✅ **Performance** - Немає очевидних performance issues

### Що НЕ перевіряємо (це робить CI/CD):
- Compilation (перевіряє Claude)
- Lint errors (автоматично)
- Test execution (автоматично)

---

## 📋 Чек-лист для Різних Типів PR

### 1️⃣ Domain Layer PR (Copilot)

**Architecture ✅**
- [ ] Entities є data classes з immutability
- [ ] Entities НЕ мають Android dependencies
- [ ] Repository interfaces в правильному package
- [ ] UseCase extends базовий UseCase<P, R> клас
- [ ] UseCase має inner data class Params
- [ ] Немає domain logic в entities (має бути в UseCases)

**Code Quality ✅**
- [ ] KDoc для всіх public класів
- [ ] KDoc для всіх public методів
- [ ] Method names описують behavior
- [ ] Data classes мають meaningful property names
- [ ] Enums використовуються де потрібно

**Business Logic ✅**
- [ ] UseCase має ОДНУ відповідальність
- [ ] Error handling через Result<T>
- [ ] Suspend функції де потрібно
- [ ] Немає hardcoded values
- [ ] Validation логіка присутня

**Testing ✅**
- [ ] Unit tests для UseCases (або Issue створений для Gemini)
- [ ] Tests покривають happy path
- [ ] Tests покривають error scenarios
- [ ] Domain model methods tested

**Example Issues:**
```kotlin
// ❌ Bad - Android dependency
import android.content.Context
data class Vehicle(val context: Context)

// ✅ Good - Pure Kotlin
data class Vehicle(val id: String, val make: String)

// ❌ Bad - Mutable
data class DtcCode(var code: String)

// ✅ Good - Immutable
data class DtcCode(val code: String)
```

---

### 2️⃣ Data Layer PR (Claude)

**Architecture ✅**
- [ ] Repository implementations в `core/data/repository/`
- [ ] Room entities в `core/data/db/entity/`
- [ ] DAOs в `core/data/db/dao/`
- [ ] Mappers в `core/data/mapper/`
- [ ] Repository implements Domain interface
- [ ] Немає Domain classes в Data layer (використовуй Mappers)

**Database ✅**
- [ ] @Entity annotations правильні
- [ ] @PrimaryKey визначений
- [ ] @ColumnInfo для non-obvious names
- [ ] Type converters для custom types
- [ ] Indices для часто queried columns
- [ ] Foreign keys налаштовані (якщо потрібно)

**Code Quality ✅**
- [ ] Mappers конвертують Entity ↔ Domain
- [ ] Repository методи suspend functions
- [ ] Error handling з Result<T> або try/catch
- [ ] DAO queries оптимізовані (немає N+1)
- [ ] Transaction handling де потрібно

**Testing ✅**
- [ ] Integration tests з in-memory database (або Issue для Gemini)
- [ ] Mapper tests (конвертація в обидва боки)
- [ ] DAO queries tested

**Example Issues:**
```kotlin
// ❌ Bad - Domain model in Data layer
@Entity
data class Vehicle(val id: String, val make: String)

// ✅ Good - Separate Entity
@Entity(tableName = "vehicles")
data class VehicleEntity(
    @PrimaryKey val id: String,
    @ColumnInfo(name = "make") val make: String
)

// ❌ Bad - Blocking call
fun getDtcs(): List<Dtc> = dao.getDtcs()

// ✅ Good - Suspend
suspend fun getDtcs(): List<Dtc> = withContext(Dispatchers.IO) {
    dao.getDtcs().map { mapper.toDomain(it) }
}
```

---

### 3️⃣ Transport Layer PR (Codex)

**Architecture ✅**
- [ ] Port interface в `hardware/transport/`
- [ ] Implementations в subdirectories (bluetooth/, usb/, tcp/)
- [ ] ConnectionManager interface та impl
- [ ] Немає OBD protocol логіки (це Protocol layer)

**Hardware Integration ✅**
- [ ] Timeout для всіх IO operations
- [ ] Proper resource cleanup (close() в finally)
- [ ] Error handling comprehensive
- [ ] Coroutines використовуються правильно (Dispatchers.IO)
- [ ] Bluetooth: SPP UUID правильний
- [ ] USB: Supported chips documented

**Code Quality ✅**
- [ ] ByteArray operations безпечні
- [ ] Buffer overflow protection
- [ ] Thread-safety (StateFlow, Mutex)
- [ ] Logging для debugging
- [ ] No memory leaks (weak references де потрібно)

**Testing ✅**
- [ ] Unit tests з mock Port
- [ ] Integration tests з simulator (documented як запустити)

**Example Issues:**
```kotlin
// ❌ Bad - No timeout
suspend fun read(): ByteArray = inputStream.read()

// ✅ Good - With timeout
suspend fun read(timeout: Long = 2000): Result<ByteArray> = 
    withTimeout(timeout) {
        try {
            Result.success(inputStream.readBytes())
        } catch (e: IOException) {
            Result.failure(e)
        }
    }

// ❌ Bad - Resource leak
fun open() {
    socket = device.createRfcommSocketToServiceRecord(SPP_UUID)
    socket.connect()
}

// ✅ Good - Proper cleanup
suspend fun open(): Result<Boolean> = withContext(Dispatchers.IO) {
    try {
        socket = device.createRfcommSocketToServiceRecord(SPP_UUID)
        socket?.connect()
        Result.success(true)
    } catch (e: IOException) {
        socket?.close()
        Result.failure(e)
    }
}
```

---

### 4️⃣ Protocol Layer PR (Codex)

**Architecture ✅**
- [ ] ObdInterface implementation
- [ ] Protocol-specific адаптери (Elm327, KWP2000, UDS)
- [ ] Parsers в окремих файлах
- [ ] Використовує Port interface (НЕ прямо Socket/Bluetooth)

**Protocol Correctness ✅**
- [ ] Commands відповідають специфікації (ELM327 datasheet)
- [ ] Hex parsing правильний
- [ ] Response validation
- [ ] Checksum verification (якщо потрібно)
- [ ] DTC codes формат правильний (P0XXX, etc)
- [ ] PID формули правильні

**Code Quality ✅**
- [ ] Hex operations читабельні та коментовані
- [ ] Magic numbers explained
- [ ] Command timeouts адекватні (2-5s для OBD)
- [ ] Retry logic для failed commands
- [ ] Proper sequence для initialization

**Testing ✅**
- [ ] Unit tests для парсерів (hex → data)
- [ ] Integration tests з mock responses
- [ ] Real trace logs used for testing

**Example Issues:**
```kotlin
// ❌ Bad - Magic numbers
val rpm = (bytes[0] * 256 + bytes[1]) / 4

// ✅ Good - Explained
// RPM formula per SAE J1979
// RPM = ((A * 256) + B) / 4
// where A = bytes[0], B = bytes[1]
val rpm = ((bytes[0].toInt() * 256) + bytes[1].toInt()) / 4

// ❌ Bad - No validation
fun parseDtc(hex: String): String = hex.substring(0, 4)

// ✅ Good - With validation
fun parseDtc(hex: String): Result<String> {
    if (hex.length < 4) {
        return Result.failure(IllegalArgumentException("Invalid DTC hex"))
    }
    val code = buildDtcCode(hex)
    return Result.success(code)
}
```

---

### 5️⃣ Features/UI Layer PR (Claude)

**Architecture ✅**
- [ ] ViewModel в features module
- [ ] UiState data class визначений
- [ ] Events sealed class
- [ ] Effects sealed class (для one-time events)
- [ ] ViewModel @HiltViewModel annotation

**State Management ✅**
- [ ] StateFlow для UI state
- [ ] SharedFlow для effects
- [ ] viewModelScope для coroutines
- [ ] Immutable UiState updates (copy())
- [ ] No mutable state exposed

**UI Code ✅**
- [ ] Jetpack Compose
- [ ] Material 3 components
- [ ] collectAsState() для StateFlow
- [ ] LaunchedEffect для effects
- [ ] Previews для всіх states (loading, success, error)

**Code Quality ✅**
- [ ] Composables є stateless (приймають UiState, onEvent)
- [ ] Reusable composables extracted
- [ ] Proper Modifier chains
- [ ] Accessibility (contentDescription)
- [ ] Dark theme support

**Testing ✅**
- [ ] ViewModel tests (state transitions)
- [ ] Compose UI tests (або Issue для Gemini)

**Example Issues:**
```kotlin
// ❌ Bad - Mutable state
val dtcList = mutableStateListOf<Dtc>()

// ✅ Good - Immutable
private val _uiState = MutableStateFlow(DtcUiState())
val uiState: StateFlow<DtcUiState> = _uiState.asStateFlow()

// ❌ Bad - Direct mutation
_uiState.value.dtcList = newList

// ✅ Good - Copy
_uiState.update { it.copy(dtcList = newList) }

// ❌ Bad - Stateful Composable
@Composable
fun DtcScreen() {
    var isLoading by remember { mutableStateOf(false) }
    // ...
}

// ✅ Good - Stateless
@Composable
fun DtcScreen(
    uiState: DtcUiState,
    onEvent: (DtcEvent) -> Unit
) {
    if (uiState.isLoading) {
        CircularProgressIndicator()
    }
    // ...
}
```

---

### 6️⃣ Testing PR (Gemini)

**Test Quality ✅**
- [ ] Test names описують behavior (backtick style)
- [ ] Given/When/Then structure
- [ ] One logical assertion per test
- [ ] No test interdependencies
- [ ] Deterministic (не flaky)

**Test Coverage ✅**
- [ ] Happy path tested
- [ ] Error scenarios tested
- [ ] Edge cases (null, empty, boundary)
- [ ] Coverage > 70% для модуля

**Code Quality ✅**
- [ ] Test data factories used
- [ ] MockK properly configured
- [ ] Coroutines tested з runTest
- [ ] No Thread.sleep() (use TestCoroutineDispatcher)

**Example Issues:**
```kotlin
// ❌ Bad - Unclear name
@Test
fun test1()

// ✅ Good - Descriptive
@Test
fun `getDtcCodes returns empty list when vehicle has no DTCs`()

// ❌ Bad - Multiple assertions unrelated
@Test
fun testRepository() {
    assertThat(repo.getDtcs()).isEmpty()
    assertThat(repo.getVehicles()).isEmpty()
    assertThat(repo.clearDtcs()).isTrue()
}

// ✅ Good - Focused
@Test
fun `getDtcs returns empty list when no DTCs stored`()

@Test
fun `clearDtcs succeeds when database is accessible`()
```

---

## 🚦 Review Decision Flow

```
1. Read PR description
   └─> Is it clear what changed? 
       ├─> NO → Request: "Please add clear description"
       └─> YES ↓

2. Check files changed
   └─> Are all changes necessary?
       ├─> NO → Request: "Please remove unrelated changes"
       └─> YES ↓

3. Review architecture
   └─> Follows Clean Architecture?
       ├─> NO → Request changes with explanation
       └─> YES ↓

4. Review code quality
   └─> Readable, maintainable, documented?
       ├─> NO → Request improvements
       └─> YES ↓

5. Check tests
   └─> Tests present and passing?
       ├─> NO → Request: "Add tests" or create Issue for Gemini
       └─> YES ↓

6. Check compilation (ask Claude)
   └─> Build успішний?
       ├─> NO → Request: Claude to fix
       └─> YES ↓

7. APPROVE ✅
   └─> Merge PR
```

---

## 📝 Коментарі в PR

### Template для Request Changes:

```markdown
### Architecture Issue

**File:** `path/to/file.kt`  
**Line:** 42

**Issue:** Domain entity має Android dependency

```kotlin
// Current (❌ Bad)
import android.content.Context
data class Vehicle(val context: Context)
```

**Suggested Fix:**
```kotlin
// Should be (✅ Good)
data class Vehicle(val id: String, val make: String)
// Context should be in Data layer, not Domain
```

**Reason:** Domain layer має бути platform-independent згідно з Clean Architecture.

**Reference:** docs/MODULAR_ARCHITECTURE_GUIDE.md section "Domain Layer"
```

### Template для Minor Issues:

```markdown
### 💡 Suggestion (non-blocking)

**File:** `path/to/file.kt`

Consider adding KDoc for this public method:
```kotlin
/**
 * Clears all DTCs for the specified vehicle.
 * 
 * @param vehicleId VIN of the vehicle
 * @return Result indicating success or failure
 */
suspend fun clearDtcs(vehicleId: String): Result<Unit>
```

This would improve documentation for other AI agents.
```

---

## ✅ Approval Template

```markdown
## ✅ APPROVED

**Review Summary:**
- ✅ Architecture: Follows Clean Architecture principles
- ✅ Code Quality: Well-written, documented, maintainable
- ✅ Tests: Comprehensive coverage (83%)
- ✅ Build: Successful (verified by @claude)
- ✅ Documentation: KDoc complete

**Highlights:**
- Excellent error handling
- Good use of sealed classes for states
- Clear separation of concerns

**Minor notes:**
- Consider extracting magic number on line 42 (non-blocking)

**Ready to merge!** 🚀

Great work @[agent]! Moving to next task.
```

---

## 🎯 Критерії для Merge

**Must Have (блокуючі):**
- ✅ Architecture correct
- ✅ Code compiles (verified by Claude)
- ✅ No critical bugs
- ✅ Breaking changes documented
- ✅ Tests present (or Issue created for Gemini)

**Nice to Have (не блокуючі):**
- 💡 Perfect code style
- 💡 100% coverage
- 💡 Performance optimizations
- 💡 Extra documentation

**Якщо "Must Have" not met → Request Changes**  
**Якщо тільки "Nice to Have" → Approve з suggestions**

---

## 📊 Review Metrics

Track для кожного AI:

| AI Agent | PRs Reviewed | Approved 1st Time | Changes Requested | Average Time to Approve |
|----------|--------------|-------------------|-------------------|------------------------|
| Copilot | 0 | - | - | - |
| Claude | 0 | - | - | - |
| Codex | 0 | - | - | - |
| Gemini | 0 | - | - | - |

**Goal:** > 80% approval rate на перший раз

---

**Reviewer:** Cursor AI  
**Updated:** 2025-10-17  
**Version:** 1.0
