# My Novel — the starting point for your own visual novel

My Novel is the smallest complete visual novel in the game: an evening town, a bar and a home, connected by choices. It has no characters, items or battles — only scenes, short scenario files and their translations. It is meant to be copied: rename it, then grow your own story from these three places.

Play it from **Play → My Novel → New game**. It is the content of the **My Novel** game (collection `novel_template`).

![The bar in My Novel: the bar background with the line "The bar is warm and noisy, but there is nothing to do here tonight."](assets/bar.jpg)

## What it contains

```filestree
my_novel_game/
├── manifest.json
├── scenes/
│   ├── my_novel_town.json      town entrance, the game starts here
│   ├── my_novel_bar.json       bar with its ambient sound
│   └── my_novel_home.json      home vestibule
├── scenarios/
│   ├── my_novel_town.scenario
│   ├── my_novel_bar.scenario
│   └── my_novel_home.scenario
└── locale/
    ├── en.properties           mod name and description
    └── scenarios_<lang>.properties   translations of the scenario lines
```

The pictures and the bar sound are not in this mod: they come from **Town Pack**, so the template stays small and shows how a game reuses shared content. The music comes from **Music Pack**; the engine mods are the 2D Shell, Dialog Engine and Audio Engine.

## How the story flows

The town is the hub. Every visit shows the same choice; the bar and the home each lead back.

```text
            ┌────────────────────┐
            │   my_novel_town    │  (start)
            │  "Where to go?"    │
            └──┬──────────────┬──┘
   Go into the bar         Go back home
               ▼              ▼
     ┌──────────────┐  ┌──────────────┐
     │ my_novel_bar │  │ my_novel_home│
     └──────┬───────┘  └──────┬───────┘
            └─ Back to the town ─┘
```

![The town entrance in My Novel, the first scene, with the line "Evening town. The street lamps are already lit and a cool draught runs along the cobbles."](assets/town.jpg)

A scene file names the background and the ambient sound:

```json
{
  "name": "my_novel_bar",
  "defaultBackground": "bar/bar",
  "defaultAmbient": "bar/bar_ambient"
}
```

The scenario with the same scene name runs when the scene is entered:

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

The English text is written directly in the scenario. Each `key=` names the line's translation in `locale/scenarios_<lang>.properties`, for example the Spanish one:

```properties
my_novel/town/intro = La ciudad al anochecer. Las farolas ya están encendidas y una corriente fresca recorre los adoquines.
```

## Make it your own

1. **Copy and rename the mod.** Name it after your game with the `_game` suffix, for example `harbor_game`. Change the folder name and `id` in `manifest.json` to the same value, and rename the `mod_my_novel_game_name` / `_desc` keys in `locale/*.properties` to `mod_harbor_game_name` / `_desc`.
2. **Make a game for it.** In **Mods & Collections → Collections**, open My Novel's scheme and press **Duplicate**, then replace `my_novel_game` with your mod in the **Story** block. The entry scene is set in the collection (`"entry": { "scene": "my_novel_town" }`).
3. **Rename the scenes** if you like: the scene's `name`, its file, the `scene:` header of its scenario and every `switch_scene` that leads to it must match.
4. **Keep or replace the art.** Leave `town_pack` in `depends` to keep the town, or add your own `backgrounds/` and `sounds/` and point `defaultBackground` / `defaultAmbient` at them.

## A first change: add a place

This is a real session in the editors, run inside My Novel with the Editor Tools mod added. The scene editor created a `my_novel_park` scene, and one option plus one label were typed into the town's scenario:

![The scenario editor with my_novel_town.scenario open: a new line `option "Go to the park" -> :park` and a new block `:park / switch_scene my_novel_park / end`; the status bar says the file was saved.](assets/edit-town-scenario.jpg)

```scenario
option "Go to the park" -> :park

:park
switch_scene my_novel_park
end
```

After saving, the town immediately offers the new way, and choosing it opens the new scene:

![The town's choice now has three options: Go into the bar, Go to the park, Go back home.](assets/new-choice.jpg)

To get the editors in your own game, add the **Editor Tools** mod to it in the scheme, or launch the game for one session with them:

```text
godot --path . -- --game=novel_template --new --with-mods=editor_tools
```

A new line without `key=` shows its literal text in every language; add a key and translations when the story is ready to be translated.

## If something does not work

1. **A choice leads nowhere.** The `switch_scene` name must equal the target scene's `name`, including case.
2. **A scene has no text.** Its scenario's `scene:` header must name that scene.
3. **The text is English in another language.** The line has no `key=`, or the key is missing in that language's `scenarios_<lang>.properties`.
4. **A black background.** The background name is misspelt, or the mod that has the picture is not in `depends`.
