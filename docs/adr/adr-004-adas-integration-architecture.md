при# ADR-004: Інтеграція ADAS та Калібрування | ADAS Integration and Calibration

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

Впроваджено підтримку систем допомоги водію (ADAS) з можливістю калібрування камер, радарів та сенсорів для професійного рівня діагностики.

*Implemented Advanced Driver Assistance Systems (ADAS) support with camera, radar, and sensor calibration capabilities for professional diagnostic level.*

---

## 🎯 Контекст | Context

### Бізнес-потреби | Business Needs

Згідно з аналізом (docs/relise.md):
- **ADAS стає обов'язковим** у нових авто (2025+)
- **Незалежні СТО потребують калібрування** після ремонтів
- **Ринок калібрувального обладнання** росте на 25% щорічно
- **Професійний інструмент** має підтримувати ADAS

### Технічні виклики | Technical Challenges

1. **Точність вимірювань** - субміліметрові вимоги до калібрування
2. **Складність сенсорів** - камери, радари, LiDAR, ультразвук
3. **Безпека** - неправильне калібрування може спричинити аварії
4. **Різноманітність систем** - різні протоколи від виробників

---

## 🔍 Розглянуті альтернативи | Alternatives Considered

### Альтернатива 1: Без ADAS підтримки

**Опис:** Фокус тільки на базовій діагностиці двигунів та систем.

**Плюси (+):**
- ✅ Простіше впровадження
- ✅ Менші ризики
- ✅ Швидший запуск

**Мінуси (-):**
- ❌ Відставання від ринку через 1-2 роки
- ❌ Втрата клієнтів з ADAS авто
- ❌ Не професійний рівень

**Чому відхилено:** Не відповідає трендам та професійним стандартам.

---

### Альтернатива 2: OEM-only калібрування

**Опис:** Підтримка тільки через офіційні дилерські інструменти.

**Плюси (+):**
- ✅ Максимальна точність
- ✅ Офіційна підтримка
- ✅ Гарантія якості

**Мінуси (-):**
- ❌ Дуже дорога ліцензія
- ❌ Недоступна для незалежних СТО
- ❌ Не автономна

**Чому відхилено:** Не підходить для цільового ринку незалежних сервісів.

---

### Альтернатива 3: Базове калібрування

**Опис:** Мінімальна підтримка ADAS через універсальні протоколи.

**Плюси (+):**
- ✅ Швидше впровадження
- ✅ Доступніше рішення
- ✅ Базова функціональність

**Мінуси (-):**
- ❌ Недостатньо точне для професійного використання
- ❌ Не підтримує складні системи
- ❌ Конкуренти мають кращі рішення

**Чому відхилено:** Недостатньо для позиціонування як професійного інструменту.

---

## ✅ Обране рішення | Decision

### Комплексна ADAS підтримка з професійним калібруванням

**Підтримувані ADAS компоненти:**
1. **Камери** - передні, задні, бокові, 360° системи
2. **Радар** - довгохвильовий, короткохвильовий
3. **LiDAR** - лазерні сенсори
4. **Ультразвук** - паркувальні сенсори
5. **Інфрачервоні сенсори** - нічні системи

**Функції калібрування:**
1. **Статичне калібрування** - з використанням мішеней
2. **Динамічне калібрування** - на дорозі
3. **Самокалібрування** - моніторинг та корекція
4. **Діагностика сенсорів** - перевірка працездатності

---

### Архітектурна інтеграція | Architecture Integration

```kotlin
// Domain Layer - ADAS Use Cases
interface AdasCalibrationUseCase {
    suspend fun calibrateCamera(targets: List<CalibrationTarget>): Result<CalibrationResult>
    suspend fun checkSensorHealth(sensorId: String): Result<SensorStatus>
    suspend fun performDynamicCalibration(): Flow<CalibrationProgress>
}

// Infrastructure Layer - ADAS Hardware
class AdasCalibrationManager(
    private val cameraController: CameraController,
    private val radarInterface: RadarInterface,
    private val targetDetector: TargetDetector
) {

    suspend fun calibrateCamera(targets: List<CalibrationTarget>): Result<CalibrationResult> {
        // 1. Перевірка умов калібрування
        validateCalibrationConditions()

        // 2. Розпізнавання мішеней
        val detectedTargets = targetDetector.detectTargets()

        // 3. Розрахунок параметрів
        val calibrationParams = calculateCalibrationParameters(detectedTargets)

        // 4. Застосування калібрування
        return cameraController.applyCalibration(calibrationParams)
    }
}
```

---

### Точність та безпека | Accuracy and Safety

#### Вимоги до точності:
1. **Камери:** ±0.1° кутові похибки
2. **Радар:** ±0.1м діапазон, ±0.1° кут
3. **Ультразвук:** ±1см точність
4. **LiDAR:** ±5см точність

#### Протоколи безпеки:
```kotlin
class AdasSafetyManager {

    suspend fun validateCalibrationConditions(): Result<Unit> {
        // Перевірка температури
        if (!temperatureInRange()) {
            return Result.failure(CalibrationException("Temperature out of range"))
        }

        // Перевірка освітлення
        if (!lightingAdequate()) {
            return Result.failure(CalibrationException("Inadequate lighting"))
        }

        // Перевірка поверхні
        if (!surfaceSuitable()) {
            return Result.failure(CalibrationException("Unsuitable surface"))
        }

        return Result.success(Unit)
    }
}
```

---

## 📊 Наслідки | Consequences

### Позитивні (+) | Positive

1. **✅ Професійний рівень**
   - Повноцінне калібрування ADAS
   - Конкурентна перевага
   - Доступ до преміум сегменту

2. **✅ Ринкова актуальність**
   - Підтримка сучасних авто з ADAS
   - Зростаючий ринок калібрування
   - Незалежність від дилерів

3. **✅ Бізнес-розвиток**
   - Нові джерела доходу (калібрування)
   - Преміум позиціонування
   - Лояльність клієнтів

### Негативні (-) | Negative

1. **❌ Складність реалізації**
   - Високі вимоги до точності
   - Складне обладнання для тестування
   - Потреба в експертизі

   **Мітігація:** Співпраця з ADAS спеціалістами, поступове впровадження

2. **❌ Ризики безпеки**
   - Неправильне калібрування може бути небезпечним
   - Юридичні наслідки

   **Мітігація:** Строгі протоколи, сертифікація, disclaimer

3. **❌ Вартість розробки**
   - Дороге обладнання та софт
   - Довгий цикл розробки

   **Мітігація:** Фокус на MVP, партнерства з постачальниками

---

### Ризики та їх мітігація | Risks and Mitigation

| Ризик | Імовірність | Вплив | Мітігація |
|-------|-------------|-------|-----------|
| Неточність калібрування | Висока | Критичний | Строгі тести, валідація, експертний огляд |
| Безпека ADAS систем | Висока | Критичний | Протоколи безпеки, emergency stop, сертифікація |
| Складність сенсорів | Висока | Високий | Модульна архітектура, поступове додавання |
| Вартість обладнання | Висока | Високий | Партнерства, оренда, фокус на софт |

---

## 📈 Метрики успіху | Success Metrics

**Через 3 місяці:**
- ✅ Базове калібрування камер
- ✅ Діагностика ADAS сенсорів
- ✅ Безпека калібрувальних процесів

**Через 6 місяців:**
- ⏳ Повне калібрування (камери, радари, ультразвук)
- ⏳ Підтримка 20+ ADAS систем
- ⏳ Інтеграція з AI для оптимізації

---

## 🔄 Коли переглядати | Review Triggers

Це рішення буде переглянуте якщо:
1. Змінюються стандарти ADAS
2. З'являються нові сенсори/технології
3. Ринок вимагає вищої точності
4. Технічні труднощі занадто великі

---

## 📚 Пов'язані рішення | Related Decisions

- [ADR-001: Initial Architecture](adr-001-initial-architecture.md) - Базова архітектура
- [ADR-002: AI Diagnostics](adr-002-ai-diagnostics-architecture.md) - AI підтримка
- [ADR-003: EV Support](adr-003-ev-support-architecture.md) - EV підтримка

---

## 📖 Додаткові матеріали | Additional Resources

### Документація проєкту:
- [Modular Architecture Guide](../MODULAR_ARCHITECTURE_GUIDE.md) - архітектура
- [Security Policy](../SECURITY_POLICY.md) - безпека ADAS
- [Testing Guidelines](../testing-guidelines.md) - тестування ADAS

### Зовнішні ресурси:
- [ADAS Calibration Standards](https://www.sae.org/standards) - SAE стандарти
- [Camera Calibration Guide](https://www.opencv.org/) - OpenCV калібрування
- [Automotive ADAS](https://www.nhtsa.gov/) - NHTSA ADAS вимоги

---

## ✍️ Підписи | Signatures

**Запропоновано:** Infrastructure Agent
**Схвалено:** Product Owner
**Дата схвалення:** 2025-01

---

## 📝 Історія змін | Change Log

| Дата | Версія | Зміни | Автор |
|------|--------|-------|-------|
| 2025-01 | 1.0 | Початкове рішення про ADAS підтримку | Infrastructure Agent |

---

**Статус:** ✅ **ACCEPTED** - Впроваджується в поточній версії

**Повернутися до:** [ADR Index](../index.md#architecture-decision-records-adr) | [Architecture Overview](../architecture.md)
