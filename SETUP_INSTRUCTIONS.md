# Инструкции по настройке hikko Engine

## Статус проверки инструментов (17.06.2026)

### ✅ Установлено:
- **Git** — C:\Program Files\Git\cmd\git.exe
- **Python** — 3.12.7
- **pip** — 24.2
- **SCons** — 4.10.1 (путь: %APPDATA%\Python\Python312\Scripts\scons.exe)
- **GitHub CLI (gh)** — 2.93.0 (авторизован как DMloaderJava)

### ❌ Отсутствует:
1. **Visual Studio Community** — не найдена (C++ компилятор не обнаружен)
2. **Организация на GitHub** — ещё не создана

---

## Что нужно сделать вручную:

### Шаг 1: Установить Visual Studio Community
1. Скачать: https://visualstudio.microsoft.com/ru/vs/community/
2. Запустить установщик
3. Выбрать рабочую нагрузку: **"Разработка классических приложений на C++"**
4. Убедиться, что выбраны компоненты:
   - MSVC v143 - VS 2022 C++ x64/x86 build tools
   - Windows 10/11 SDK
   - C++ CMake tools for Windows

### Шаг 2: Создать организацию на GitHub
1. Перейти: https://github.com/organizations/plan
2. Выбрать Free план
3. Название: **hikko-games**

### Шаг 3: Ответить на вопросы:
1. Какое название организации создали?
2. Какую версию Godot форкать? (рекомендуется **4.4 stable** или **4.3 stable**)
3. Какие фирменные цвета hikko GAMES? (hex-кода, например #8B5CF6 фиолетовый)
4. Какие модули отключить? (mono, webxr, mobile_vr, openxr, etc?)
5. Есть ли готовый логотип? (.ico .icns .png .svg)

---

После выполнения этих шагов — продолжим автоматизацию в этом терминале.
