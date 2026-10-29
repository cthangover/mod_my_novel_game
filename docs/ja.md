# My Novel — 自分のビジュアルノベルの出発点

My Novel はプロジェクトでいちばん小さな完成したビジュアルノベルです。夕暮れの町、バー、家が選択肢でつながっています。キャラクターもアイテムも戦闘もなく、あるのはシーン、短いシナリオファイル、その翻訳だけ。コピーされることを前提としたモッドです。名前を変えて、この三つの場所から自分の物語を育ててください。

**プレイ → My Novel → ニューゲーム** から始めます。**My Novel** ゲーム(コレクション `novel_template`)のコンテンツです。

![My Novel のバー:バーの背景と「バーは暖かくにぎやかだが、今夜ここですることは何もない。」の行。](assets/bar.jpg)

## 中身

```filestree
my_novel_game/
├── manifest.json
├── scenes/
│   ├── my_novel_town.json      町の入り口。ゲームはここから始まる
│   ├── my_novel_bar.json       環境音付きのバー
│   └── my_novel_home.json      家の玄関
├── scenarios/
│   ├── my_novel_town.scenario
│   ├── my_novel_bar.scenario
│   └── my_novel_home.scenario
└── locale/
    ├── en.properties           MOD の名前と説明
    └── scenarios_<lang>.properties   シナリオの各行の翻訳
```

画像とバーの音はこの MOD には入っていません。**Town Pack** から来るので、テンプレートは小さなまま、ゲームが共有コンテンツを再利用するようすを見せられます。音楽は **Music Pack** から。エンジン MOD は 2D シェル、会話エンジン、オーディオエンジンです。

## 物語の流れ

町が中心です。訪れるたびに同じ選択肢が現れ、バーも家も必ず戻ってきます。

```text
            ┌────────────────────┐
            │   my_novel_town    │  (開始)
            │  「どこへ行く？」   │
            └──┬──────────────┬──┘
      バーに入る            家に帰る
               ▼              ▼
     ┌──────────────┐  ┌──────────────┐
     │ my_novel_bar │  │ my_novel_home│
     └──────┬───────┘  └──────┬───────┘
            └─ 町に戻る ─┘
```

![My Novel の最初のシーンである町の入り口と、「夕暮れの町。街灯はもうともり、石畳を涼しい風が吹き抜けていく。」の行。](assets/town.jpg)

シーンのファイルは背景と環境音を指名します:

```json
{
  "name": "my_novel_bar",
  "defaultBackground": "bar/bar",
  "defaultAmbient": "bar/bar_ambient"
}
```

シーンと同じ名前のシナリオは、そのシーンに入ったときに再生されます:

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

英語のテキストはシナリオに直接書かれています。各 `key=` はその行の翻訳を `locale/scenarios_<lang>.properties` で指名します。たとえばスペイン語では:

```properties
my_novel/town/intro = La ciudad al anochecer. Las farolas ya están encendidas y una corriente fresca recorre los adoquines.
```

## 自分のものにする

1. **MOD をコピーして名前を変える。** 自分のゲームの名前に `_game` 接尾辞を付けたものにします。たとえば `harbor_game`。フォルダー名と `manifest.json` の `id` を同じ値に変え、`locale/*.properties` のキー `mod_my_novel_game_name` / `_desc` を `mod_harbor_game_name` / `_desc` に変えます。
2. **ゲームを作る。** **Mods & Collections → コレクション** で My Novel の構成を開いて **複製** を押し、**ストーリー** ブロックの `my_novel_game` を自分の MOD に置き換えます。開始シーンはコレクションで指定します(`"entry": { "scene": "my_novel_town" }`)。
3. **シーンの名前を変える(任意)。** シーンの `name`、そのファイル、シナリオの `scene:` ヘッダー、そこへ通じるすべての `switch_scene` が一致していなければなりません。
4. **アートを保つか置き換える。** 町を残すなら `depends` に `town_pack` を残します。自分の `backgrounds/` と `sounds/` を追加し、`defaultBackground` / `defaultAmbient` をそちらへ向けてもかまいません。

## 最初の変更:場所を一つ追加する

これはエディターでの実際の作業です。My Novel に Editor Tools MOD を追加した状態で行いました。シーンエディターがシーン `my_novel_park` を作り、町のシナリオに選択肢を一つとラベルを一つ打ち込みました:

![my_novel_town.scenario を開いたシナリオエディター:新しい行 `option "Go to the park" -> :park` と新しいブロック `:park / switch_scene my_novel_park / end`。ステータスバーはファイルが保存されたと伝えています。](assets/edit-town-scenario.jpg)

```scenario
option "Go to the park" -> :park

:park
switch_scene my_novel_park
end
```

保存すると、町はすぐに新しい道を示し、選ぶと新しいシーンが開きます:

![町の選択肢は今や三つ:バーに入る、Go to the park、家に帰る。](assets/new-choice.jpg)

自分のゲームでエディターを使うには、構成で **Editor Tools** MOD を追加するか、一ゲーム分だけそれらと一緒に起動します:

```text
godot --path . -- --game=novel_template --new --with-mods=editor_tools
```

`key=` のない新しい行はどの言語でもリテラルのテキストを表示します。物語を翻訳する準備ができたら、キーと翻訳を追加してください。

## うまくいかないとき

1. **選択肢がどこにも行かない。** `switch_scene` の名前は、大文字小文字を含めて対象シーンの `name` と一致しなければなりません。
2. **シーンにテキストがない。** そのシナリオの `scene:` ヘッダーが、そのシーンの名前を指している必要があります。
3. **別の言語でテキストが英語のまま。** 行に `key=` がないか、その言語の `scenarios_<lang>.properties` にキーがありません。
4. **背景が真っ暗。** 背景名のつづりが間違っているか、画像を持つ MOD が `depends` に入っていません。
