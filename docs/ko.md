# My Novel — 나만의 비주얼 노벨의 출발점

My Novel은 프로젝트에서 가장 작은 완성된 비주얼 노벨입니다. 저녁의 마을, 바, 집이 선택지로 이어져 있습니다. 캐릭터도 아이템도 전투도 없고, 장면과 짧은 시나리오 파일, 그 번역만 있을 뿐입니다. 이 모드는 복제하기 위해 만들어졌습니다. 이름을 바꾸고, 이 세 장소에서 나만의 이야기를 키우세요.

**플레이 → My Novel → 새 게임**에서 시작합니다. **My Novel** 게임(컬렉션 `novel_template`)의 콘텐츠입니다.

![My Novel의 바: 바 배경과 “바는 따뜻하고 시끌벅적하지만, 오늘 밤 여기서 할 일은 없다.” 줄.](assets/bar.jpg)

## 들어 있는 것

```filestree
my_novel_game/
├── manifest.json
├── scenes/
│   ├── my_novel_town.json      마을 입구, 게임이 여기서 시작됨
│   ├── my_novel_bar.json       자체 환경음이 있는 바
│   └── my_novel_home.json      집 현관
├── scenarios/
│   ├── my_novel_town.scenario
│   ├── my_novel_bar.scenario
│   └── my_novel_home.scenario
└── locale/
    ├── en.properties           모드 이름과 설명
    └── scenarios_<lang>.properties   시나리오 줄의 번역
```

그림과 바 소리는 이 모드에 없습니다. **Town Pack**에서 오기 때문에, 템플릿은 작게 유지되면서 게임이 공유 콘텐츠를 재사용하는 방식을 보여 줍니다. 음악은 **Music Pack**에서 옵니다. 엔진 모드는 2D 셸, 대화 엔진, 오디오 엔진입니다.

## 이야기가 흐르는 방식

마을이 중심입니다. 방문할 때마다 같은 선택지가 나타나고, 바와 집은 언제나 돌아가는 길로 이어집니다.

```text
            ┌────────────────────┐
            │   my_novel_town    │  (시작)
            │    “어디로 갈까?”    │
            └──┬──────────────┬──┘
     바에 들어간다           집으로 돌아간다
               ▼              ▼
     ┌──────────────┐  ┌──────────────┐
     │ my_novel_bar │  │ my_novel_home│
     └──────┬───────┘  └──────┬───────┘
            └─ 마을로 돌아간다 ─┘
```

![My Novel의 첫 장면인 마을 입구와 “저녁의 마을. 가로등은 벌써 켜졌고, 시원한 바람이 자갈길을 따라 분다.” 줄.](assets/town.jpg)

장면 파일은 배경과 환경음을 지정합니다:

```json
{
  "name": "my_novel_bar",
  "defaultBackground": "bar/bar",
  "defaultAmbient": "bar/bar_ambient"
}
```

장면과 같은 이름의 시나리오는 그 장면에 들어설 때 실행됩니다:

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

영어 텍스트는 시나리오에 직접 쓰여 있습니다. 각 `key=`는 그 줄의 번역을 `locale/scenarios_<lang>.properties`에서 가리킵니다. 예를 들어 스페인어는:

```properties
my_novel/town/intro = La ciudad al anochecer. Las farolas ya están encendidas y una corriente fresca recorre los adoquines.
```

## 내 것으로 만들기

1. **모드를 복제하고 이름을 바꾸십시오.** 게임 이름에 `_game` 접미사를 붙여 지으세요. 예: `harbor_game`. 폴더 이름과 `manifest.json`의 `id`를 같은 값으로 바꾸고, `locale/*.properties`의 키 `mod_my_novel_game_name` / `_desc`를 `mod_harbor_game_name` / `_desc`로 바꾸십시오.
2. **게임을 만드십시오.** **Mods & Collections → 컬렉션**에서 My Novel의 구성을 열고 **복제**를 누른 뒤, **스토리** 블록의 `my_novel_game`을 내 모드로 바꾸십시오. 시작 장면은 컬렉션에서 정합니다(`"entry": { "scene": "my_novel_town" }`).
3. **장면 이름을 바꾸십시오(선택).** 장면의 `name`, 그 파일, 시나리오의 `scene:` 헤더, 그곳으로 이어지는 모든 `switch_scene`이 일치해야 합니다.
4. **아트를 유지하거나 교체하십시오.** 마을을 그대로 두려면 `depends`에 `town_pack`을 남기세요. 아니면 자신의 `backgrounds/`와 `sounds/`를 추가하고 `defaultBackground` / `defaultAmbient`가 그쪽을 가리키게 하세요.

## 첫 번째 변경: 장소 추가하기

이것은 편집기에서의 실제 세션입니다. My Novel에 Editor Tools 모드를 추가한 상태에서 진행했습니다. 장면 편집기가 `my_novel_park` 장면을 만들고, 마을 시나리오에 선택지 하나와 라벨 하나를 입력했습니다:

![my_novel_town.scenario가 열린 시나리오 편집기: 새 줄 `option "Go to the park" -> :park`과 새 블록 `:park / switch_scene my_novel_park / end`. 상태 표시줄은 파일이 저장되었다고 알립니다.](assets/edit-town-scenario.jpg)

```scenario
option "Go to the park" -> :park

:park
switch_scene my_novel_park
end
```

저장하면 마을이 곧바로 새 길을 내놓고, 그것을 고르면 새 장면이 열립니다:

![마을의 선택지는 이제 세 개: 바에 들어간다, Go to the park, 집으로 돌아간다.](assets/new-choice.jpg)

자신의 게임에서 편집기를 쓰려면 구성에서 **Editor Tools** 모드를 추가하거나, 한 세션 동안 그것들과 함께 게임을 실행하세요:

```text
godot --path . -- --game=novel_template --new --with-mods=editor_tools
```

`key=` 없는 새 줄은 모든 언어에서 글자 그대로의 텍스트를 보여 줍니다. 이야기를 번역할 준비가 되면 키와 번역을 추가하세요.

## 잘 안 될 때

1. **어떤 선택지가 아무 데도 가지 않습니다.** `switch_scene`의 이름은 대소문자까지 대상 장면의 `name`과 같아야 합니다.
2. **장면에 텍스트가 없습니다.** 그 시나리오의 `scene:` 헤더가 바로 그 장면의 이름을 가리켜야 합니다.
3. **다른 언어에서 텍스트가 영어입니다.** 줄에 `key=`가 없거나, 그 언어의 `scenarios_<lang>.properties`에 키가 없습니다.
4. **검은 배경.** 배경 이름의 철자가 틀렸거나, 그림을 가진 모드가 `depends`에 없습니다.
