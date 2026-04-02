# Библиотека для парсинга логов

`analysis` — это Rust-библиотека для парсинга и анализа структурированных логов. Она позволяет читать логи различных типов, фильтровать их по критериям и извлекать значимую информацию.

## Возможности

- Парсинг логов приложения и системных логов
- Поддержка различных типов событий:
  - Ошибки (сетевые, доступа и др.)
  - Трассировка запросов и ответов
  - Журнал бизнес-событий (создание пользователей, операции с активами и т.д.)
- Фильтрация по типу лога:
  - Все записи
  - Только ошибки
  - Только операции с активами
- Поддержка фильтрации по request ID
- Гибкий парсер на основе комбинаторов

## Использование

```rust
use analysis::read_log;
use analysis::ReadMode;
use std::num::NonZeroU32;
use std::io::Cursor;

let log_data = "System::Error NetworkError \"url unknown\" requestid=1\nApp::Error SystemError \"network\" requestid=1";
let cursor = Cursor::new(log_data);

// Чтение всех логов
let all_logs = read_log(cursor, ReadMode::All, vec![]);

// Чтение только ошибок
let error_logs = read_log(cursor, ReadMode::Errors, vec![]);

// Чтение с фильтрацией по request ID
let filtered_logs = read_log(cursor, ReadMode::All, vec![NonZeroU32::new(1).unwrap()]);
```

## Архитектура

Библиотека использует комбинаторный подход к парсингу, вдохновлённый библиотекой `nom`. Основные компоненты:

- `parse.rs` — реализация парсера на основе комбинаторов
- `lib.rs` — основной API для работы с логами
- Различные типы событий представлены перечислениями (enums)

## Типы логов

### Системные логи
- `System::Error NetworkError` — сетевые ошибки
- `System::Error AccessDenied` — ошибки доступа
- `System::Trace SendRequest` — отправка запросов
- `System::Trace GetResponse` — получение ответов

### Логи приложения
- `App::Error` — ошибки приложения
- `App::Trace` — трассировка операций
- `App::Journal` — журнал бизнес-событий:
  - `CreateUser` — создание пользователя
  - `RegisterAsset` — регистрация актива
  - `BuyAsset` — покупка актива
  - `SellAsset` — продажа актива
  - `WithdrawCash` — снятие средств

## Сборка и тестирование

```bash
# Сборка
cargo build

# Запуск тестов
cargo test
```