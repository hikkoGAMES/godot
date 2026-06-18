# Hikko Engine - Внутренняя Вики для Разработчиков

## Содержание

1. [Обзор Hikko Engine](#обзор-hikko-engine)
2. [Скачивание и сборка движка](#скачивание-и-сборка-движка)
3. [Отличия от Godot](#отличия-от-godot)
4. [Синхронизация с Upstream](#синхронизация-с-upstream)
5. [Правила сборки проектов](#правила-сборки-проектов)
6. [Кастомные функции](#кастомные-функции)
7. [Troubleshooting](#troubleshooting)

---

## Обзор Hikko Engine

**Hikko Engine** — это форк Godot Engine с кастомизированным брендингом и дополнительными функциями для студии hikko GAMES.

### Текущая версия
- **Версия**: 1.0.0-stable
- **Бренд**: hikko Engine (hikko)
- **Сайт**: https://hikkogames.com
- **GitHub**: https://github.com/hikkoGAMES/godot

### Компоненты движка
- Редактор (Editor) — визуальная среда разработки
- Рантайм (Runtime) — исполняемый файл для запуска игр
- Экспортные шаблоны (Export Templates) — для сборки под платформы

---

## Скачивание и сборка движка

### Вариант 1: Скачивание готовых сборок

Готовые сборки доступны на GitHub в разделе Releases:

```powershell
# Скачать последнюю версию с GitHub
$REPO = "hikkoGAMES/godot"
$TAG = "hikko-v1.0.0-stable"  # или последний тег

# Перейдите на страницу релизов:
# https://github.com/hikkoGAMES/godot/releases
```

### Вариант 2: Сборка из исходного кода

#### Требования для сборки

| Платформа | Требования |
|-----------|-------------|
| Windows | Visual Studio 2022, Python 3.8+, SCons |
| Linux | GCC/Clang, Python 3.8+, SCons |
| macOS | Xcode, Python 3.8+, SCons |

#### Сборка для Windows

```powershell
# Клонирование репозитория
git clone https://github.com/hikkoGAMES/godot.git
cd godot

# Сборка редактора
scons platform=windows target=template_debug tools=yes -j$(nproc)
scons platform=windows target=template_release tools=yes -j$(nproc)

# Результат: bin/godot.windows.editor.exe
```

#### Сборка для Linux

```bash
git clone https://github.com/hikkoGAMES/godot.git
cd godot

scons platform=linux target=template_debug tools=yes -j$(nproc)
scons platform=linux target=template_release tools=yes -j$(nproc)

# Результат: bin/godot.linux.editor
```

#### Сборка для macOS

```bash
git clone https://github.com/hikkoGAMES/godot.git
cd godot

scons platform=macos target=template_debug tools=yes -j$(nproc)
scons platform=macos target=template_release tools=yes -j$(nproc)

# Результат: bin/godot.macos.editor.universal
```

---

## Отличия от Godot

### Сравнительная таблица

| Параметр | Godot | Hikko Engine |
|----------|-------|-------------|
| Название | Godot Engine | Hikko Engine |
|_short name | Godot | hikko |
| Версия | 4.x | 1.0.0 |
| Сайт | godotengine.org | hikkogames.com |
| Файл проекта | project.godot | project_info.hikko |
| Модуль ядра | Стандартный | hikko_core |
| Шифровани�� PCK | Нет (опционально) | Да (встроено) |

### Добавленные функции

#### 1. HikkoAuth (Аутентификация)
```gdscript
# Пример использования
var auth = HikkoAuth.new()
auth.login("username", "password")
if auth.is_logged_in():
    print("User ID: ", auth.get_user_id())
```

#### 2. HikkoAnalytics (Аналитика)
```gdscript
var analytics = HikkoAnalytics.new()
analytics.set_game_id("my_game_001")
analytics.set_game_version("1.0.0")
analytics.track_scene_change("MainMenu")
analytics.track_event("level_complete", {"level": 1, "score": 5000})
analytics.flush_events()
```

#### 3. HikkoAutoUpdate (Автообновления)
```gdscript
var updater = HikkoAutoUpdate.new()
updater.check_for_updates()
if updater.is_update_available():
    print("Доступна версия: ", updater.get_latest_version())
    updater.download_update()
    updater.install_update()
```

#### 4. HikkoCrypto (Шифрование)
```gdscript
# Шифрование строки
var encrypted = HikkoCrypto.encrypt_string("secret_data", "my_key")
var decrypted = HikkoCrypto.decrypt_string(encrypted, "my_key")

# Хеширование
var hash = HikkoCrypto.hash_sha256("password")
```

### Кастомное шифрование PCK

Hikko Engine использует кастомный ключ шифрования для .pck файлов:

```cpp
// В core/io/file_access_pack.cpp
#define HIKKO_ENCRYPTION_KEY "HikkoGames_SecureKey_2024"
```

Это обеспечивает защиту игровых ресурсов от распаковки стандартными инструментами.

---

## Синхронизация с Upstream

### Настройка Git

Репозиторий уже настроен с двумя remote:

```powershell
# Просмотр remote
git remote -v

# Результат:
# origin    https://github.com/hikkoGAMES/godot.git (fetch)
# origin    https://github.com/hikkoGAMES/godot.git (push)
# upstream https://github.com/godotengine/godot.git (fetch)
# upstream https://github.com/godotengine/godot.git (push)
```

### Процесс синхронизации

#### Шаг 1: Обновление upstream

```powershell
git fetch upstream
```

#### Шаг 2: Переключение на master

```powershell
git checkout master
```

#### Шаг 3: Слияние с upstream

```powershell
git merge upstream/master
```

#### Шаг 4: Разрешение конфликтов (если есть)

Если возникли конфликты:

```powershell
# Посмотреть конфликты
git status

# Разрешить конфликты вручную
# Затем пометить как разрешенные
git add .
git commit -m "Merge: Resolved conflicts with upstream"
```

#### Шаг 5: Отправка в origin

```powershell
git push origin master
```

### Важные замечания

1. **Сохраняйте кастомные изменения**: После merge могут перезаписаться файлы брендинга, их нужно восстановить:
   - `core/version_generated.gen.h`
   - `modules/hikko_core/`
   - Кастомные файлы движка

2. **Тестирование**: После синхронизации обязательно проверьте сборку:
   ```powershell
   scons platform=windows target=template_debug tools=yes -j$(nproc)
   ```

3. **CI/CD**: GitHub Actions автоматически соберёт и опубликует новую версию при push в master

---

## Правила сборки проектов

### Создание нового проект��

```powershell
# Создайте папку проекта
mkdir MyHikkoGame
cd MyHikkoGame

# Создайте файл project_info.hikko (НЕ project.godot!)
```

### Конфигурация project_info.hikko

```ini
; Конфигурация Hikko Engine проекта
config_version=5

[application]
config/name="My Hikko Game"
config/description="Описание игры"
run/main_scene="res://scenes/main.tscn"
config/features=PackedStringArray("4.x", "Forward Plus")
config/icon="res://icon.png"

[display]
window/size/viewport_width=1920
window/size/viewport_height=1080

[rendering]
renderer/rendering_method="forward_plus"
```

### Экспорт под платформы

#### Windows

```powershell
# Экспорт через редактор
# Export -> Windows -> .exe

# Или через CLI (дополнительная настройка)
```

#### macOS

```bash
# Требуется Xcode для подписи
# Export -> macOS -> .app / .dmg
```

#### Linux

```bash
# Экспорт в AppImage
# Export -> Linux -> AppImage
```

#### iOS/Android

```bash
# iOS требует macOS с Xcode
# Android требует Android SDK
```

---

## Кастомные функции

### HikkoAuth API

| Метод | Описание |
|-------|----------|
| `login(username, password)` | Аутентификация пользователя |
| `logout()` | Выход из системы |
| `is_logged_in()` | Проверка статуса |
| `get_auth_token()` | Получение токена |
| `refresh_token()` | Обновление токена |

### HikkoAnalytics API

| Метод | Описание |
|-------|----------|
| `set_game_id(id)` | Установка ID игры |
| `track_event(name, params)` | Отслеживание события |
| `track_scene_change(scene)` | Смена сцены |
| `flush_events()` | Отправка данных на сервер |

### HikkoAutoUpdate API

| Метод | Описание |
|-------|----------|
| `check_for_updates()` | Проверка обновлений |
| `is_update_available()` | Наличие обновления |
| `download_update()` | Скачивание обновления |
| `install_update()` | Установка обновления |

### HikkoCrypto API

| Метод | Описание |
|-------|----------|
| `encrypt_string(text, key)` | Шифрование строки |
| `decrypt_string(text, key)` | Расшифровка строки |
| `encrypt_file(source, dest, key)` | Шифрование файла |
| `hash_sha256(text)` | SHA256 хеш |

---

## Troubleshooting

### Ошибки компиляции

#### Ошибка: "SCons not found"
```powershell
# Установите SCons
pip install scons
```

#### Ошибка: "Visual Studio not found"
```powershell
# Запустите из Developer Command Prompt
# или установите Visual Studio 2022
```

### Ошибки Git

#### Конфликты при merge
```powershell
# Вручную разрешите конфликты
# Затем
git add .
git commit -m "Resolved merge conflicts"
```

#### Remote already exists
```powershell
# Если remote уже существует
git remote set-url upstream https://github.com/godotengine/godot.git
```

### Ошибки рантайма

#### Ошибка: "Encryption key not found"
```powershell
# Убедитесь, что HIKKO_ENCRYPTION_KEY определен
# Проверьте core/io/file_access_pack.cpp
```

---

## Контакты и поддержка

- **GitHub Issues**: https://github.com/hikkoGAMES/godot/issues
- **Документация**: https://docs.godotengine.org
- **Студия**: https://hikkogames.com

---

*Документация создана для внутреннего использования Hikko Games*
*Обновлено: 2024*
