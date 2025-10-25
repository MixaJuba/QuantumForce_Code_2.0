# ADR-003: Підтримка Електромобілів (EV) та Сучасних Протоколів | EV Support and Modern Protocols

## Метадані | Metadata

| Параметр | Значення |
|----------|----------|
| **Статус** | ✅ Прийнято (Accepted) |
| **Дата рішення** | 2025-01 |
| **Автор** | Infrastructure Agent |
| **Рев'юєри** | Development Team |
| **Затверджено** | Product Owner |
| **Остання зміна** | 2025-01 |

---

## 📋 Короткий опис | Summary

Впроваджено повну підтримку електромобілів та сучасних діагностичних протоколів (CAN, UDS, DoIP) з акцентом на високовольтні системи та батареї.

*Implemented full support for electric vehicles and modern diagnostic protocols (CAN, UDS, DoIP) with focus on high-voltage systems and batteries.*

---

## 🎯 Контекст | Context

### Бізнес-потреби | Business Needs

Згідно з аналізом (docs/relise.md):
- **EV ринок в Україні росте на 142,9%** (2025)
- **Потреба в універсальному сканері** для всіх типів авто
- **Незалежність від дилерських систем** критична
- **Професійний рівень** порівнянний з Launch X431

### Технічні виклики | Technical Challenges

1. **Високовольтна безпека** - робота з 400V+ системами
2. **Складність протоколів** - CAN, UDS, DoIP замість простого OBD-II
3. **Різноманітність EV** - різні BMS, інвертори, системи зарядки
4. **Реальний час** - моніторинг батарей та систем в режимі реального часу

---

## 🔍 Розглянуті альтернативи | Alternatives Considered

### Альтернатива 1: Тільки OBD-II (традиційні авто)

**Опис:** Фокус тільки на бензинових/дизельних авто з OBD-II.

**Плюси (+):**
- ✅ Простота реалізації
- ✅ Швидкий запуск MVP
- ✅ Менше ризиків безпеки

**Мінуси (-):**
- ❌ Пропуск ростучого EV ринку
- ❌ Не конкурентно через 2-3 роки
- ❌ Обмежена функціональність

**Чому відхилено:** Не відповідає трендам та бізнес-цілям.

---

### Альтернатива 2: OEM-специфічні рішення

**Опис:** Підтримка тільки через офіційні дилерські інтерфейси.

**Плюси (+):**
- ✅ Максимальна функціональність
- ✅ Офіційна підтримка виробників
- ✅ Гарантія точності даних

**Мінуси (-):**
- ❌ Залежність від ліцензій
- ❌ Висока вартість
- ❌ Обмежена доступність в Україні
- ❌ Не автономно

**Чому відхилено:** Не підходить для незалежних СТО та українського ринку.

---

### Альтернатива 3: Базова EV підтримка

**Опис:** Мінімальна підтримка EV через стандартні протоколи.

**Плюси (+):**
- ✅ Швидше впровадження
- ✅ Менші ризики
- ✅ Достатньо для початкового ринку

**Мінуси (-):**
- ❌ Недостатньо для професійної діагностики
- ❌ Не підтримує складні системи
- ❌ Конкуренти мають глибшу підтримку

**Чому відхилено:** Недостатньо для позиціонування як професійного інструменту.

---

## ✅ Обране рішення | Decision

### Комплексна EV підтримка з сучасними протоколами

**Ключові протоколи:**
1. **CAN (Controller Area Network)** - основний для сучасних авто
2. **UDS (Unified Diagnostic Services)** - уніфікована діагностика
3. **DoIP (Diagnostics over IP)** - діагностика через Ethernet
4. **OBD-II** - для сумісності зі старими авто

**EV-специфічні системи:**
1. **BMS (Battery Management System)** - управління батареєю
2. **Inverter/Motor Controller** - інвертор та двигун
3. **Charging System** - зарядка (AC/DC, CCS, CHAdeMO)
4. **Thermal Management** - температурний контроль
5. **High-Voltage Safety** - системи безпеки

---

### Архітектурна інтеграція | Architecture Integration

```kotlin
// Infrastructure Layer - Protocol Adapters
interface EvProtocol {
    suspend fun readBatteryStatus(): Result<BatteryData>
    suspend fun monitorCharging(): Flow<ChargingData>
    suspend fun checkHighVoltageSafety(): Result<SafetyStatus>
}

class CanEvAdapter(
    private val canPort: CanPort
) : EvProtocol {

    override suspend fun readBatteryStatus(): Result<BatteryData> {
        // Читання BMS даних через CAN
        val canFrame = canPort.sendAndReceive(CanFrame.BMS_STATUS)
        return parseBatteryData(canFrame)
    }
}

// Domain Layer - EV Use Cases
class GetBatteryHealthUseCase(
    private val evRepository: EvRepository
) {
    suspend operator fun invoke(): Result<BatteryHealth> {
        return evRepository.getBatteryHealth()
    }
}
```

---

### Безпека високовольтних систем | High-Voltage Safety

#### Критичні вимоги безпеки:
1. **Ізоляція кіл** - перевірка ізоляції перед діагностикою
2. **Контактор контроль** - безпечне відключення високої напруги
3. **Температурний моніторинг** - контроль перегріву
4. **Emergency shutdown** - аварійне відключення
5. **User warnings** - попередження про небезпеку

#### Архітектура безпеки:
```kotlin
class HighVoltageSafetyManager(
    private val isolationTester: IsolationTester,
    private val contactorController: ContactorController
) {
    suspend fun prepareForDiagnostics(): Result<Unit> {
        // 1. Перевірка ізоляції
        val isolationOk = isolationTester.checkIsolation()
        if (!isolationOk) return Result.failure(SafetyException())

        // 2. Безпечне підключення
        contactorController.safeConnect()

        // 3. Моніторинг температури
        temperatureMonitor.startMonitoring()

        return Result.success(Unit)
    }
}
```

---

## 📊 Наслідки | Consequences

### Позитивні (+) | Positive

1. **✅ Ринкова актуальність**
   - Підтримка ростучого EV сегменту
   - Конкурентна перевага в Україні
   - Майбутнє-орієнтований продукт

2. **✅ Технічна перевага**
   - Сучасні протоколи для всіх типів авто
   - Професійний рівень діагностики
   - Розширюваність на нові технології

3. **✅ Бізнес-розвиток**
   - Доступ до нових клієнтів (EV власники)
   - Преміум позиціонування
   - Довгострокова життєздатність

### Негативні (-) | Negative

1. **❌ Складність реалізації**
   - Складні протоколи потребують експертизи
   - Більше часу на розробку
   - Вищі вимоги до тестування

   **Мітігація:** Модульна архітектура, поступове впровадження

2. **❌ Ризики безпеки**
   - Потенціал пошкодження дорогих EV компонентів
   - Юридичні ризики при неправильній діагностиці

   **Мітігація:** Суворі протоколи безпеки, експертна валідація

3. **❌ Вартість розробки**
   - Дорожче обладнання для тестування
   - Потреба в спеціалізованих інструментах

   **Мітігація:** Співпраця з EV сервісами, оренда обладнання

---

### Ризики та їх мітігація | Risks and Mitigation

| Ризик | Імовірність | Вплив | Мітігація |
|-------|-------------|-------|-----------|
| Безпека високовольтних систем | Висока | Критичний | Суворі протоколи, експертний огляд, emergency shutdown |
| Складність протоколів | Висока | Високий | Модульна архітектура, поступове впровадження |
| Недостатня експертиза команди | Висока | Високий | Консультації з EV експертами, навчання |
| Вартість розробки | Середня | Високий | Фокус на MVP, поступове розширення |

---

## 📈 Метрики успіху | Success Metrics

**Через 3 місяці:**
- ✅ Підтримка основних CAN/UDS протоколів
- ✅ Базова BMS діагностика
- ✅ Безпека високовольтних систем

**Через 6 місяців:**
- ⏳ Повна EV діагностика (BMS, інвертор, зарядка)
- ⏳ Підтримка 10+ EV моделей
- ⏳ Інтеграція з AI для EV-діагностики

---

## 🔄 Коли переглядати | Review Triggers

Це рішення буде переглянуте якщо:
1. Змінюються стандарти EV протоколів
2. З'являються нові технології (800V системи)
3. Ринок EV розвивається інакше ніж прогнозовано
4. Технічні труднощі занадто великі

---

## 📚 Пов'язані рішення | Related Decisions

- [ADR-001: Initial Architecture](adr-001-initial-architecture.md) - Базова архітектура
- [ADR-002: AI Diagnostics](adr-002-ai-diagnostics-architecture.md) - AI підтримка
- [ADR-004: ADAS Integration](adr-004-adas-integration.md) - (Planned) ADAS підтримка

---

## 📖 Додаткові матеріали | Additional Resources

### Документація проєкту:
- [Modular Architecture Guide](../MODULAR_ARCHITECTURE_GUIDE.md) - архітектура
- [Security Policy](../SECURITY_POLICY.md) - безпека EV
- [Testing Guidelines](../testing-guidelines.md) - тестування EV

### Зовнішні ресурси:
- [ISO 14229 (UDS)](https://www.iso.org/standard/72439.html) - UDS стандарт
- [ISO 11898 (CAN)](https://www.iso.org/standard/63648.html) - CAN стандарт
- [EV Diagnostic Standards](https://www.sae.org/standards) - SAE EV стандарти

---

## ✍️ Підписи | Signatures

**Запропоновано:** Infrastructure Agent
**Схвалено:** Product Owner
**Дата схвалення:** 2025-01

---

## 📝 Історія змін | Change Log

| Дата | Версія | Зміни | Автор |
|------|--------|-------|-------|
| 2025-01 | 1.0 | Початкове рішення про EV підтримку | Infrastructure Agent |

---

**Статус:** ✅ **ACCEPTED** - Впроваджується в поточній версії

**Повернутися до:** [ADR Index](../index.md#architecture-decision-records-adr) | [Architecture Overview](../architecture.md)
