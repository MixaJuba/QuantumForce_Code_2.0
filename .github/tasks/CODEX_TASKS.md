# 🤖 OpenAI Codex - Завдання | Tasks

**AI Agent:** OpenAI Codex  
**Середовище:** OpenAI Playground / API з Git connector  
**Спеціалізація:** Infrastructure & Protocol Development  
**Координатор:** Cursor AI

---

## 🎯 Ваша Зона Відповідальності

### Директорії де ви працюєте:
```
hardware/transport/     # Hardware communication layer
protocols/obd/          # OBD-II and diagnostic protocols
```

### Ваша експертиза:
- 🔌 **Hardware Integration** - Bluetooth, USB, TCP
- 📡 **Protocol Implementation** - OBD-II, ELM327, UDS, KWP2000
- 🔧 **Low-level Communication** - Hex parsing, byte manipulation
- 🚗 **Automotive Standards** - ISO 9141, ISO 14230, ISO 15765

---

## 📋 Поточні Завдання (Priority Order)

### 🟡 MEDIUM PRIORITY (Чекає на Domain/Data готовність)

#### Task 1: Create Port Interface Implementations
**Estimated Time:** 4-6 hours  
**Dependencies:** Domain layer готовий

**Files to Create:**
- `hardware/transport/src/main/kotlin/com/quantumforce_code/hardware/transport/Port.kt` (interface)
- `hardware/transport/src/main/kotlin/com/quantumforce_code/hardware/transport/bluetooth/BluetoothPort.kt`
- `hardware/transport/src/main/kotlin/com/quantumforce_code/hardware/transport/usb/UsbSerialPort.kt`
- `hardware/transport/src/main/kotlin/com/quantumforce_code/hardware/transport/tcp/TcpPort.kt`

**Documentation:**
- Read: `docs/IMPLEMENTATION_EXAMPLES.md` (section 3 - Transport Layer)
- Read: `docs/INTERFACE_CONTRACTS.md` (Transport Layer Interfaces)

**Requirements:**
- Implement Port interface для кожного типу
- Bluetooth: використовуйте BluetoothSocket (Android SDK)
- USB: використовуйте usb-serial-for-android library
- TCP: Socket для WiFi OBD адаптерів
- Proper error handling
- Timeouts для всіх операцій

**Acceptance Criteria:**
- [ ] Port interface визначений
- [ ] BluetoothPort реалізований (SPP profile)
- [ ] UsbSerialPort реалізований (CH340/FTDI)
- [ ] TcpPort реалізований
- [ ] All ports handle connect/disconnect/read/write
- [ ] Timeout handling
- [ ] Error handling with Result<T>
- [ ] Compiles successfully

**Промпт для себе:**
```
Створи Transport Layer для automotive diagnostic app:

1. Port interface:
   - suspend fun open(): Result<Boolean>
   - suspend fun close()
   - suspend fun write(data: ByteArray): Result<Boolean>
   - suspend fun read(timeout: Long): Result<ByteArray>
   - val isConnected: Boolean

2. BluetoothPort implementation:
   - Android BluetoothSocket
   - SPP profile (UUID: 00001101-0000-1000-8000-00805F9B34FB)
   - Coroutines for IO
   - Proper error handling

3. UsbSerialPort implementation:
   - usb-serial-for-android library
   - Support CH340, FTDI, CP2102 chips
   - Baud rate 38400 (ELM327 standard)

4. TcpPort implementation:
   - Socket connection
   - Default port 35000 (WiFi OBD)
   - Timeout handling

Використай приклад з docs/IMPLEMENTATION_EXAMPLES.md розділ 3.
```

---

#### Task 2: Create Connection Manager
**Estimated Time:** 3-4 hours  
**Dependencies:** Task 1 completed

**Files to Create:**
- `hardware/transport/src/main/kotlin/com/quantumforce_code/hardware/transport/ConnectionManager.kt`
- `hardware/transport/src/main/kotlin/com/quantumforce_code/hardware/transport/ConnectionManagerImpl.kt`

**Documentation:**
- Read: `docs/INTERFACE_CONTRACTS.md` (ConnectionManager interface)
- Read: `docs/IMPLEMENTATION_EXAMPLES.md` (section 3)

**Requirements:**
- Manage connection lifecycle
- StateFlow для connection state
- Auto-reconnect logic
- Device scanning (Bluetooth/USB)
- Connection pooling

**Acceptance Criteria:**
- [ ] ConnectionManager interface реалізований
- [ ] ConnectionState sealed class (Disconnected, Connecting, Connected, Error)
- [ ] scanDevices() для Bluetooth та USB
- [ ] connect() з auto-retry
- [ ] disconnect() gracefully
- [ ] StateFlow updates UI
- [ ] Compiles successfully

---

### 🔴 HIGH PRIORITY (Після Task 1-2)

#### Task 3: Implement ELM327 Protocol Adapter
**Estimated Time:** 6-8 hours  
**Dependencies:** Task 1, Task 2 completed

**Files to Create:**
- `protocols/obd/src/main/kotlin/com/quantumforce_code/protocols/obd/ObdInterface.kt`
- `protocols/obd/src/main/kotlin/com/quantumforce_code/protocols/obd/elm327/Elm327Adapter.kt`
- `protocols/obd/src/main/kotlin/com/quantumforce_code/protocols/obd/elm327/Elm327Commands.kt`
- `protocols/obd/src/main/kotlin/com/quantumforce_code/protocols/obd/parser/DtcParser.kt`
- `protocols/obd/src/main/kotlin/com/quantumforce_code/protocols/obd/parser/PidParser.kt`

**Documentation:**
- Read: `docs/IMPLEMENTATION_EXAMPLES.md` (section 4 - Protocol Layer)
- Read: `docs/guides/automotive-diagnostic-software-guide.md` (OBD-II Protocol Details)
- External: [ELM327 Datasheet](https://www.elmelectronics.com/wp-content/uploads/2017/01/ELM327DS.pdf)

**Requirements:**
- Implement ObdInterface
- ELM327 initialization sequence:
  ```
  ATZ          # Reset
  ATE0         # Echo off
  ATL0         # Linefeeds off
  ATSP0        # Auto protocol
  0100         # Test PID support
  ```
- Read DTCs: Mode 03
- Clear DTCs: Mode 04
- Read PIDs: Mode 01
- Parse hex responses to meaningful data

**Critical Commands:**
```kotlin
// ELM327 AT Commands
ATZ         // Reset
ATE0        // Echo off
ATL0        // Linefeeds off
ATS0        // Spaces off
ATSP0       // Set protocol auto
ATH1        // Headers on
ATDP        // Describe current protocol

// OBD-II Modes
01          // Show current data (PIDs)
02          // Show freeze frame data
03          // Show stored DTCs
04          // Clear DTCs and stored values
05          // O2 sensor test results
06          // On-board monitoring test results
07          // Show pending DTCs
09          // Request vehicle information
```

**DTC Parsing:**
```kotlin
// Response: "43 02 01 33 02 31"
// Breakdown:
// 43 = Mode 03 response
// 02 = 2 codes
// 01 33 = P0133 (O2 Sensor Circuit Slow Response)
// 02 31 = P0231 (Fuel Pump Secondary Circuit Low)

// Convert to user-friendly format
```

**Acceptance Criteria:**
- [ ] Elm327Adapter реалізований
- [ ] Initialization sequence працює
- [ ] readDtcCodes() парсить коди правильно
- [ ] clearDtcCodes() очищає помилки
- [ ] readPid() читає параметри (RPM, Speed, Temp)
- [ ] Timeout handling для кожної команди
- [ ] Retry logic для failed commands
- [ ] Proper hex → decimal conversion
- [ ] Compiles successfully

**Промпт для себе:**
```
Створи ELM327 Protocol Adapter:

1. Elm327Adapter клас:
   - Конструктор приймає Port
   - initialize() - ATZ, ATE0, ATL0, ATSP0 sequence
   - sendCommand() - відправка AT/OBD команд
   - readDtcCodes() - Mode 03, парсинг hex response
   - clearDtcCodes() - Mode 04
   - readPid() - Mode 01, парсинг значень

2. DtcParser:
   - parse(hexString): List<String>
   - Конвертація hex → DTC codes
   - P0XXX, C0XXX, B0XXX, U0XXX formats

3. PidParser:
   - parse(pid: String, hexValue: String): PidData
   - Формули для конвертації:
     RPM = ((A * 256) + B) / 4
     Speed = A
     Temp = A - 40

4. Error handling:
   - "NO DATA" responses
   - "?" = unrecognized command
   - "UNABLE TO CONNECT"
   - Timeouts

Використай повний приклад з docs/IMPLEMENTATION_EXAMPLES.md розділ 4.
```

---

### 🟢 LOW PRIORITY (Future)

#### Task 4: Implement Additional Protocol Adapters
**Estimated Time:** 4-6 hours each

**Files to Create:**
- `protocols/obd/src/main/kotlin/com/quantumforce_code/protocols/obd/kwp2000/KWP2000Adapter.kt`
- `protocols/obd/src/main/kotlin/com/quantumforce_code/protocols/obd/uds/UDSAdapter.kt`

**Note:** Тільки після успішного ELM327

---

## 🛠️ Як виконувати завдання

### 1. Підготовка:
```bash
# Clone repo
git clone https://github.com/MixaJuba/QuantumForce_Code.git
cd QuantumForce_Code
```

### 2. Візьміть завдання:
- Коментар в Issue: "Starting Task 1. ETA: 6 hours"

### 3. Створіть гілку:
```bash
git checkout -b feature/transport-layer-codex
```

### 4. Використовуйте Codex для генерації:
```
Промпт для OpenAI Codex:

Я розробляю automotive diagnostic Android app.
Потрібно створити Transport Layer для комунікації з OBD адаптерами.

Вимоги:
- Port interface з suspend методами
- BluetoothPort для Bluetooth SPP
- UsbSerialPort для USB OBD
- TcpPort для WiFi адаптерів

Архітектура: Clean Architecture
Документація: [attach docs/IMPLEMENTATION_EXAMPLES.md]

Generate code for BluetoothPort implementation.
```

### 5. Тестування (з симулятором):
```python
# Створіть простий ELM327 simulator для тестів
# elm327_simulator.py

import serial

def main():
    ser = serial.Serial('COM3', 38400)
    
    while True:
        cmd = ser.readline().decode().strip()
        
        if cmd == "ATZ":
            ser.write(b"ELM327 v2.2\r\r>")
        elif cmd == "ATE0":
            ser.write(b"OK\r\r>")
        elif cmd == "03":  # Read DTCs
            ser.write(b"43 02 01 33 02 31\r\r>")
        else:
            ser.write(b"OK\r\r>")
```

### 6. Створіть PR:
```bash
git add .
git commit -m "feat(transport): Implement Bluetooth/USB/TCP ports"
git push origin feature/transport-layer-codex

# Create PR, tag @cursor and @claude for review
```

---

## 🎯 Тестування з реальним обладнанням

### Що потрібно купити:
1. **ELM327 Bluetooth adapter** (~$15-20)
   - Рекомендація: Vgate iCar Pro або аналог
   - Версія chipset: v1.5 або v2.1

2. **USB OBD-II cable** (~$20-30) (опціонально)
   - З CH340 або FTDI чіпом

### Тестування:
1. **Simulator testing** (перша фаза)
   - Python ELM327 simulator
   - Перевірка команд та парсингу

2. **Real adapter testing** (друга фаза)
   - З'єднання з Bluetooth
   - Відправка AT команд
   - Читання відповідей

3. **Vehicle testing** (фінальна фаза)
   - Підключення до реального авто
   - Зчитування DTC
   - Моніторинг PIDs

---

## ✅ Чек-лист перед PR

**Code Quality:**
- [ ] Всі hex операції коментовані
- [ ] Timeout для кожної IO операції
- [ ] Error handling comprehensive
- [ ] KDoc для всіх public методів

**Protocol Correctness:**
- [ ] ELM327 команди правильні
- [ ] DTC parsing коректний
- [ ] PID формули вірні
- [ ] Checksum validation (якщо потрібно)

**Testing:**
- [ ] Unit tests для парсерів
- [ ] Integration tests з mock Port
- [ ] Simulator testing пройдено
- [ ] Real hardware tested (якщо можливо)

**Documentation:**
- [ ] Protocol commands documented
- [ ] Response formats explained
- [ ] Examples provided

---

## 📚 Ресурси для вас

**Must Read:**
1. `docs/IMPLEMENTATION_EXAMPLES.md` (sections 3, 4)
2. `docs/guides/automotive-diagnostic-software-guide.md`
3. ELM327 Datasheet (PDF)

**External Resources:**
- [OBD-II PIDs](https://en.wikipedia.org/wiki/OBD-II_PIDs)
- [ISO 15765-4 (CAN)](https://www.iso.org/standard/66369.html)
- [ELM327 Commands](https://www.elmelectronics.com/wp-content/uploads/2017/01/ELM327DS.pdf)

**Examples:**
- `docs/IMPLEMENTATION_EXAMPLES.md` section 3 - BluetoothPort повний приклад
- `docs/IMPLEMENTATION_EXAMPLES.md` section 4 - Elm327Adapter повний приклад

---

## 🔧 Специфічні технічні вимоги

### Bluetooth Requirements:
```kotlin
// SPP UUID
val SPP_UUID = UUID.fromString("00001101-0000-1000-8000-00805F9B34FB")

// Connection parameters
val CONNECT_TIMEOUT = 10000L  // 10 seconds
val READ_TIMEOUT = 2000L       // 2 seconds
val WRITE_TIMEOUT = 1000L      // 1 second

// Retry logic
val MAX_RETRIES = 3
val RETRY_DELAY = 500L         // 500ms
```

### USB Serial Requirements:
```kotlin
// Supported chips
- CH340/CH341
- FTDI FT232R
- CP2102/CP2109

// Settings
baudRate = 38400       // ELM327 standard
dataBits = 8
stopBits = 1
parity = NONE
flowControl = NONE
```

### ELM327 Protocol:
```kotlin
// Response terminators
val PROMPT = ">"
val LINE_FEED = "\r"

// Timeouts
val INIT_TIMEOUT = 3000L
val COMMAND_TIMEOUT = 2000L
val OBD_TIMEOUT = 5000L        // For vehicle response

// Max retries
val MAX_COMMAND_RETRIES = 3
```

---

## 💬 Комунікація

**Питання по Domain:**
- Tag `@copilot` - він створив Domain entities

**Питання по компіляції:**
- Tag `@claude` - він компілює проект

**Архітектурні питання:**
- Tag `@cursor` - координатор

**Технічні питання:**
- Створіть Issue з тегом `protocol-question`
- Опишіть специфіку протоколу
- Tag `@cursor` для допомоги

---

## 📊 Ваш Прогрес

**Поточний статус:** 🟡 WAITING FOR DEPENDENCIES  
**Призначені завдання:** 4 tasks  
**Estimated Total Time:** 17-24 hours  
**Blocked by:** Domain та Data layers (Copilot, Claude)

**Можна почати:**
- ✅ Читати документацію
- ✅ Вивчати ELM327 protocol
- ✅ Підготувати симулятор для тестів
- ✅ Створити Port interface structure

**Чекаємо:**
- ⏳ Domain entities (Copilot Task 1)
- ⏳ Build environment (Claude Task 0)

---

## 🎯 Очікування від вас

Як **Infrastructure та Protocol Expert**:

✅ **Робіть:**
- Створюйте robust hardware communication
- Імплементуйте протоколи за стандартами
- Тестуйте з реальним обладнанням
- Документуйте protocol specifics
- Будьте експертом в OBD-II/ELM327

❌ **Не робіть:**
- Не змінюйте Domain layer (це Copilot)
- Не працюйте з UI (це Claude)
- Не додавайте бізнес-логіку в Protocol layer
- Не ігноруйте automotive standards

---

## 🚀 Наступні кроки

**Зараз:**
1. Прочитайте всю документацію
2. Вивчіть ELM327 datasheet
3. Підготуйте тестовий симулятор
4. Створіть Issue якщо є питання

**Коли готові:**
1. Повідомте `@cursor` що готові почати
2. Візьміть Task 1 (Port implementations)
3. Створіть Draft PR рано для feedback

---

**Координатор:** @cursor  
**Build Help:** @claude  
**Technical Questions:** Create Issue з тегом `@cursor @codex`

Ви - хребет системи! Без вас немає зв'язку з авто! 🚗⚡
