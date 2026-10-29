# My Novel — điểm khởi đầu cho tiểu thuyết tương tác của riêng bạn

My Novel là tiểu thuyết tương tác hoàn chỉnh nhỏ nhất trong dự án: một thị trấn lúc chiều tối, một quán bar và một ngôi nhà, nối với nhau bằng các lựa chọn. Nó không có nhân vật, vật phẩm hay chiến đấu — chỉ có cảnh, những tệp kịch bản ngắn và bản dịch của chúng. Mod được làm ra để copy: đổi tên nó, rồi nuôi lớn câu chuyện của riêng bạn từ ba địa điểm này.

Chơi từ **Chơi → My Novel → Trò chơi mới**. Đây là nội dung của trò chơi **My Novel** (bộ sưu tập `novel_template`).

![Quán bar trong My Novel: nền quán bar với dòng “Quán bar ấm áp và ồn ào, nhưng tối nay chẳng có gì để làm ở đây.”.](assets/bar.jpg)

## Bên trong có gì

```filestree
my_novel_game/
├── manifest.json
├── scenes/
│   ├── my_novel_town.json      lối vào thị trấn, trò chơi bắt đầu ở đây
│   ├── my_novel_bar.json       quán bar với âm thanh nền riêng
│   └── my_novel_home.json      tiền sảnh ngôi nhà
├── scenarios/
│   ├── my_novel_town.scenario
│   ├── my_novel_bar.scenario
│   └── my_novel_home.scenario
└── locale/
    ├── en.properties           tên và mô tả mod
    └── scenarios_<lang>.properties   bản dịch các dòng kịch bản
```

Hình ảnh và âm thanh quán bar không nằm trong mod này: chúng đến từ **Town Pack**, để mẫu vẫn nhỏ gọn và cho thấy một trò chơi tái sử dụng nội dung chung thế nào. Nhạc đến từ **Music Pack**; các mod engine là Vỏ 2D, engine hội thoại và engine âm thanh.

## Câu chuyện chảy thế nào

Thị trấn là điểm trung chuyển. Mỗi lần ghé lại thấy cùng một lựa chọn; quán bar và ngôi nhà đều dẫn đường quay về.

```text
            ┌────────────────────┐
            │   my_novel_town    │  (bắt đầu)
            │    "Đi đâu đây?"   │
            └──┬──────────────┬──┘
     Vào quán bar            Về nhà
               ▼              ▼
     ┌──────────────┐  ┌──────────────┐
     │ my_novel_bar │  │ my_novel_home│
     └──────┬───────┘  └──────┬───────┘
            └─ Quay lại thị trấn ─┘
```

![Lối vào thị trấn trong My Novel, cảnh đầu tiên, với dòng “Thị trấn lúc chiều tối. Đèn đường đã sáng và một làn gió mát lùa dọc những viên đá lát.”.](assets/town.jpg)

Tệp cảnh nêu tên nền và âm thanh nền:

```json
{
  "name": "my_novel_bar",
  "defaultBackground": "bar/bar",
  "defaultAmbient": "bar/bar_ambient"
}
```

Kịch bản trùng tên với cảnh sẽ chạy khi bước vào cảnh đó:

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

Văn bản tiếng Anh được viết ngay trong kịch bản. Mỗi `key=` gọi tên bản dịch của dòng đó trong `locale/scenarios_<lang>.properties`, ví dụ bản tiếng Tây Ban Nha:

```properties
my_novel/town/intro = La ciudad al anochecer. Las farolas ya están encendidas y una corriente fresca recorre los adoquines.
```

## Biến nó thành của bạn

1. **Copy và đổi tên mod.** Đặt theo tên trò chơi của bạn với hậu tố `_game`, ví dụ `harbor_game`. Đổi tên thư mục và `id` trong `manifest.json` thành cùng một giá trị, và đổi các khóa `mod_my_novel_game_name` / `_desc` trong `locale/*.properties` thành `mod_harbor_game_name` / `_desc`.
2. **Làm cho nó một trò chơi.** Trong **Mods & Collections → Bộ sưu tập**, mở sơ đồ của My Novel và bấm **Nhân bản**, rồi thay `my_novel_game` bằng mod của bạn trong khối **Câu chuyện**. Cảnh mở đầu được đặt trong bộ sưu tập (`"entry": { "scene": "my_novel_town" }`).
3. **Đổi tên các cảnh** nếu muốn: `name` của cảnh, tệp của nó, dòng đầu `scene:` trong kịch bản của nó và mọi `switch_scene` dẫn tới nó phải khớp nhau.
4. **Giữ hoặc thay phần hình ảnh.** Giữ `town_pack` trong `depends` để giữ nguyên thị trấn, hoặc thêm `backgrounds/` và `sounds/` của riêng bạn rồi trỏ `defaultBackground` / `defaultAmbient` vào đó.

## Thay đổi đầu tiên: thêm một địa điểm

Đây là một buổi làm việc thật trong trình soạn thảo, chạy bên trong My Novel với mod Editor Tools được thêm vào. Trình soạn cảnh đã tạo cảnh `my_novel_park`, và một lựa chọn cùng một nhãn được gõ vào kịch bản của thị trấn:

![Trình soạn kịch bản đang mở my_novel_town.scenario: dòng mới `option "Go to the park" -> :park` và khối mới `:park / switch_scene my_novel_park / end`; thanh trạng thái báo tệp đã được lưu.](assets/edit-town-scenario.jpg)

```scenario
option "Go to the park" -> :park

:park
switch_scene my_novel_park
end
```

Sau khi lưu, thị trấn lập tức đưa ra lối mới, và chọn nó sẽ mở cảnh mới:

![Lựa chọn của thị trấn giờ có ba mục: Vào quán bar, Go to the park, Về nhà.](assets/new-choice.jpg)

Để có trình soạn thảo trong trò chơi của riêng bạn, thêm mod **Editor Tools** vào sơ đồ, hoặc khởi động trò chơi một phiên cùng chúng:

```text
godot --path . -- --game=novel_template --new --with-mods=editor_tools
```

Dòng mới không có `key=` hiện văn bản nguyên văn trong mọi ngôn ngữ; thêm khóa và bản dịch khi câu chuyện sẵn sàng để dịch.

## Khi không hoạt động

1. **Một lựa chọn không dẫn đi đâu.** Tên trong `switch_scene` phải bằng `name` của cảnh đích, kể cả chữ hoa chữ thường.
2. **Một cảnh không có văn bản.** Dòng đầu `scene:` trong kịch bản của nó phải gọi đúng tên cảnh đó.
3. **Văn bản hiện tiếng Anh ở ngôn ngữ khác.** Dòng đó không có `key=`, hoặc khóa thiếu trong `scenarios_<lang>.properties` của ngôn ngữ đó.
4. **Nền đen thui.** Tên nền viết sai chính tả, hoặc mod đang giữ bức hình không có trong `depends`.
