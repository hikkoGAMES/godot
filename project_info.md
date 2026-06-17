my_hikko_game/                   # Корневая папка вашей игры
├── .hikko/                      # Скрытая папка кэша импорта движка (аналог .godot)
├── assets/                      # Исходные тяжелые ресурсы (до импорта)
│   ├── audio/                   # Звуковые эффекты и музыка (.wav, .ogg, .mp3)
│   ├── textures/                # Изображения, UI-спрайты, текстуры (.png, .tga)
│   └── models/                  # 3D-модели и анимации (.gltf, .fbx)
├── src/                         # Скрипты и логика кода (GDScript / C#)
│   ├── autoload/                # Глобальные скрипты (синглтоны, например, global_state.gd)
│   ├── actors/                  # Логика персонажей (player.gd, enemy.gd)
│   └── ui/                      # Логика меню и интерфейсов
├── scenes/                      # Файлы сцен (.tscn)
│   ├── levels/                  # Игровые локации и уровни (level_1.tscn)
│   ├── prefabs/                 # Шаблоны объектов (player_instance.tscn)
│   └── ui_scenes/               # Экран главного меню, HUD, экраны настроек
├── resources/                   # Файлы ресурсов движка (.tres)
│   ├── materials/               # Материалы для 3D объектов (.tres)
│   ├── themes/                  # Настройки тем и стилей UI (.tres)
│   └── curves/                  # Кривые анимаций или путей (.tres)
└── project_info.hikko           # Главный конфигурационный файл (замена project.godot)




Часть 2. Внутренняя структура файла project_info.hikko
Движок Godot использует формат ConfigFile (аналог .ini файлов) для хранения настроек проекта[1]. Для hikko Engine структура этого файла может выглядеть следующим образом (ее можно редактировать как в блокноте, так и через меню настроек вашего редактора):
code
Ini
; hikko Engine project configuration file.
; Рекомендуется редактировать через интерфейс hikko Editor,
; чтобы настройки сохраняли правильный синтаксис.

config_version=5

[application]

config/name="Hikko Adventure"
config/description="Новый хит от студии hikko GAMES"
config/version="1.0.0"
run/main_scene="res://scenes/levels/main_menu.tscn"
config/features=PackedStringArray("4.x", "Forward Plus")
config/icon="res://assets/textures/brand/icon.png"

[display]

window/size/viewport_width=1920
window/size/viewport_height=1080
window/size/resizable=true
window/stretch/mode="canvas_items"
window/stretch/aspect="expand"

[rendering]

renderer/rendering_method="forward_plus"
textures/vram_compression/import_etc2_astc=true

[hikko_games_metadata]

; Кастомные поля вашей студии, которые вы можете считывать в коде
studio/developer_name="hikko GAMES"
security/encrypt_assets=true
analytics/enable_telemetry=false
api/server_url="https://api.hikkogames.com/v1"
Часть 3. Как научить ваш C++ движок читать project_info.hikko вместо project.godot
Чтобы ваш скомпилированный движок понимал, что корнем проекта является папка с файлом project_info.hikko (и чтобы он создавал именно этот файл при создании нового проекта), вам необходимо внести правки в исходный код C++ перед компиляцией.
Основное имя файла жестко закодировано в нескольких местах[2][3]. Найдите и замените строку "project.godot" на "project_info.hikko" в следующих файлах исходного кода Godot:
core/config/project_settings.cpp
Внутри этого файла обрабатывается чтение и запись настроек[2]. Найдите все упоминания project.godot (обычно в методах сохранения настроек save(), get_resource_path() или _load_settings_text()) и замените их на имя вашего файла:
code
C++
// Пример в коде C++:
last_save_time = FileAccess::get_modified_time(get_resource_path().path_join("project_info.hikko"));
main/main.cpp
Здесь движок инициализируется при запуске и проверяет, находится ли он в игровой папке[4]. Измените дефолтное имя файла, которое движок ищет для старта:
code
C++
// Пример в main.cpp:
if (FileAccess::exists("project_info.hikko")) {
    // Загрузка проекта...
}
editor/project_manager.cpp (или editor/editor_node.cpp в зависимости от версии)
Здесь настраивается поведение Менеджера проектов (Project Manager)[5]. Чтобы менеджер проектов сканировал диски и импортировал папки именно с вашим файлом настроек, измените сигнатуру поиска в коде сканирования:
code
C++
// Пример в коде сканирования проектов:
if (dir->file_exists("project_info.hikko")) {
    // Добавить проект в список...
}
