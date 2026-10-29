# My Novel — отправная точка для вашей собственной визуальной новеллы

My Novel — самая маленькая законченная визуальная новелла в проекте: вечерний город, бар и дом, связанные выборами. Здесь нет персонажей, предметов и боёв — только сцены, короткие файлы сценариев и их переводы. Мод задуман как образец для копирования: переименуйте его и вырастите собственную историю из этих трёх мест.

Запускается через **Играть → My Novel → Новая игра**. Это контент игры **My Novel** (коллекция `novel_template`).

![Бар в My Novel: фон бара и строка «В баре тепло и шумно, но сегодня здесь нечего делать.».](assets/bar.jpg)

## Что внутри

```filestree
my_novel_game/
├── manifest.json
├── scenes/
│   ├── my_novel_town.json      въезд в город, игра начинается здесь
│   ├── my_novel_bar.json       бар со своим фоновым звуком
│   └── my_novel_home.json      прихожая дома
├── scenarios/
│   ├── my_novel_town.scenario
│   ├── my_novel_bar.scenario
│   └── my_novel_home.scenario
└── locale/
    ├── en.properties           имя и описание мода
    └── scenarios_<lang>.properties   переводы строк сценария
```

Картинок и звука бара в этом моде нет: они берутся из **Town Pack**, чтобы шаблон оставался маленьким и показывал, как игра переиспользует общий контент. Музыка — из **Music Pack**; моды-движки здесь — 2D-оболочка, диалоговый движок и звуковой движок.

## Как течёт история

Город — центр. Каждый визит показывает один и тот же выбор; бар и дом каждый раз ведут обратно.

```text
            ┌────────────────────┐
            │   my_novel_town    │  (старт)
            │   «Куда пойти?»    │
            └──┬──────────────┬──┘
     Зайти в бар             Вернуться домой
               ▼              ▼
     ┌──────────────┐  ┌──────────────┐
     │ my_novel_bar │  │ my_novel_home│
     └──────┬───────┘  └──────┬───────┘
            └─ Вернуться в город ─┘
```

![Въезд в город в My Novel — первая сцена — и строка «Вечерний город. Фонари уже горят, по мостовой тянет прохладой.».](assets/town.jpg)

Файл сцены называет фон и фоновый звук:

```json
{
  "name": "my_novel_bar",
  "defaultBackground": "bar/bar",
  "defaultAmbient": "bar/bar_ambient"
}
```

Сценарий с тем же именем, что у сцены, запускается при входе в неё:

```scenario
scene: my_novel_town
priority: 10
---
# The town is the hub: every visit shows the same choice.
text "Evening town. The street lamps are already lit and a cool draught runs along the cobbles." key=my_novel/town/intro
select "Where to go?" key=my_novel/town/prompt
option "Go into the bar" key=my_novel/town/opt_bar -> :bar
option "Go back home" key=my_novel/town/opt_home -> :home

:bar
switch_scene my_novel_bar
end

:home
switch_scene my_novel_home
end
```

Английский текст написан прямо в сценарии. Каждый `key=` называет перевод строки в `locale/scenarios_<lang>.properties`, например испанский:

```properties
my_novel/town/intro = La ciudad al anochecer. Las farolas ya están encendidas y una corriente fresca recorre los adoquines.
```

## Сделайте её своей

1. **Скопируйте и переименуйте мод.** Назовите его по своей игре с суффиксом `_game`, например `harbor_game`. Папку и поле `id` в `manifest.json` смените на одно и то же значение, а ключи `mod_my_novel_game_name` / `_desc` в `locale/*.properties` — на `mod_harbor_game_name` / `_desc`.
2. **Сделайте для него игру.** В **Mods & Collections → Коллекции** откройте схему My Novel и нажмите **Дублировать**, затем в блоке «Сюжет» замените `my_novel_game` на свой мод. Стартовая сцена задаётся в коллекции (`"entry": { "scene": "my_novel_town" }`).
3. **Переименуйте сцены**, если хотите: имя сцены в поле `name`, её файл, заголовок `scene:` её сценария и каждый `switch_scene`, который туда ведёт, должны совпадать.
4. **Оставьте или замените графику.** Оставьте `town_pack` в `depends`, чтобы сохранить город, — или добавьте собственные `backgrounds/` и `sounds/` и направьте `defaultBackground` / `defaultAmbient` на них.

## Первое изменение: добавьте место

Это настоящая сессия в редакторах, запущенная внутри My Novel с подключённым модом Editor Tools. Редактор сцен создал сцену `my_novel_park`, а в сценарий города вписали один вариант и одну метку:

![Редактор сценариев с открытым my_novel_town.scenario: новая строка `option "Go to the park" -> :park` и новый блок `:park / switch_scene my_novel_park / end`; строка состояния сообщает, что файл сохранён.](assets/edit-town-scenario.jpg)

```scenario
option "Go to the park" -> :park

:park
switch_scene my_novel_park
end
```

После сохранения город сразу предлагает новый путь, и выбор открывает новую сцену:

![Выбор в городе теперь из трёх вариантов: Зайти в бар, Go to the park, Вернуться домой.](assets/new-choice.jpg)

Чтобы редакторы были в вашей игре, добавьте в её схему мод **Editor Tools** или запустите игру на один сеанс с ними:

```text
godot --path . -- --game=novel_template --new --with-mods=editor_tools
```

Новая строка без `key=` показывает свой буквальный текст на любом языке; добавьте ключ и переводы, когда история будет готова к переводу.

## Если что-то не работает

1. **Выбор ведёт в никуда.** Имя в `switch_scene` должно совпадать с полем `name` целевой сцены, включая регистр.
2. **У сцены нет текста.** Заголовок `scene:` её сценария должен называть именно эту сцену.
3. **На другом языке текст английский.** У строки нет `key=`, либо ключ отсутствует в `scenarios_<lang>.properties` этого языка.
4. **Чёрный фон.** Имя фона написано с ошибкой, или мод с картинкой не указан в `depends`.
