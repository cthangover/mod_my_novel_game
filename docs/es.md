# My Novel — el punto de partida para tu propia novela visual

My Novel es la novela visual completa más pequeña del proyecto: un pueblo al atardecer, un bar y una casa, unidos por decisiones. No tiene personajes, objetos ni combates: solo escenas, archivos de escenario breves y sus traducciones. Está pensado para copiarse: renómbralo y haz crecer tu propia historia desde estos tres lugares.

Se juega desde **Jugar → My Novel → Nueva partida**. Es el contenido del juego **My Novel** (colección `novel_template`).

![El bar de My Novel: el fondo del bar con la línea «El bar es cálido y ruidoso, pero esta noche aquí no hay nada que hacer.».](assets/bar.jpg)

## Qué contiene

```filestree
my_novel_game/
├── manifest.json
├── scenes/
│   ├── my_novel_town.json      entrada del pueblo, el juego empieza aquí
│   ├── my_novel_bar.json       el bar con su sonido ambiental
│   └── my_novel_home.json      el vestíbulo de la casa
├── scenarios/
│   ├── my_novel_town.scenario
│   ├── my_novel_bar.scenario
│   └── my_novel_home.scenario
└── locale/
    ├── en.properties           nombre y descripción del mod
    └── scenarios_<lang>.properties   traducciones de las líneas del escenario
```

Las imágenes y el sonido del bar no están en este mod: vienen de **Town Pack**, para que la plantilla siga siendo pequeña y muestre cómo un juego reutiliza contenido compartido. La música viene de **Music Pack**; los mods de motor son la Carcasa 2D, el motor de diálogos y el motor de audio.

## Cómo fluye la historia

El pueblo es el centro. Cada visita muestra la misma decisión; el bar y la casa siempre llevan de vuelta.

```text
            ┌────────────────────┐
            │   my_novel_town    │  (inicio)
            │   "¿Adónde ir?"    │
            └──┬──────────────┬──┘
   Entrar en el bar         Volver a casa
               ▼              ▼
     ┌──────────────┐  ┌──────────────┐
     │ my_novel_bar │  │ my_novel_home│
     └──────┬───────┘  └──────┬───────┘
            └─ Volver a la ciudad ─┘
```

![La entrada del pueblo en My Novel, la primera escena, con la línea «La ciudad al anochecer. Las farolas ya están encendidas y una corriente fresca recorre los adoquines.».](assets/town.jpg)

El archivo de la escena nombra el fondo y el sonido ambiental:

```json
{
  "name": "my_novel_bar",
  "defaultBackground": "bar/bar",
  "defaultAmbient": "bar/bar_ambient"
}
```

El escenario con el mismo nombre de la escena se ejecuta al entrar en ella:

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

El texto en inglés está escrito directamente en el escenario. Cada `key=` nombra la traducción de la línea en `locale/scenarios_<lang>.properties`; por ejemplo, la española:

```properties
my_novel/town/intro = La ciudad al anochecer. Las farolas ya están encendidas y una corriente fresca recorre los adoquines.
```

## Hazlo tuyo

1. **Copia y renombra el mod.** Ponle el nombre de tu juego con el sufijo `_game`, por ejemplo `harbor_game`. Cambia el nombre de la carpeta y el `id` de `manifest.json` al mismo valor, y renombra las claves `mod_my_novel_game_name` / `_desc` de `locale/*.properties` a `mod_harbor_game_name` / `_desc`.
2. **Crea un juego para él.** En **Mods & Collections → Colecciones**, abre el esquema de My Novel y pulsa **Duplicar**; después sustituye `my_novel_game` por tu mod en el bloque **Historia**. La escena de entrada se fija en la colección (`"entry": { "scene": "my_novel_town" }`).
3. **Renombra las escenas** si quieres: el `name` de la escena, su archivo, la cabecera `scene:` de su escenario y cada `switch_scene` que lleve a ella deben coincidir.
4. **Conserva o sustituye el arte.** Deja `town_pack` en `depends` para quedarte con el pueblo, o añade tus propios `backgrounds/` y `sounds/` y apunta a ellos con `defaultBackground` / `defaultAmbient`.

## Un primer cambio: añade un lugar

Esta es una sesión real en los editores, hecha dentro de My Novel con el mod Editor Tools añadido. El editor de escenas creó una escena `my_novel_park`, y en el escenario del pueblo se teclearon una opción y una etiqueta:

![El editor de escenarios con my_novel_town.scenario abierto: la línea nueva `option "Go to the park" -> :park` y el bloque nuevo `:park / switch_scene my_novel_park / end`; la barra de estado dice que el archivo se guardó.](assets/edit-town-scenario.jpg)

```scenario
option "Go to the park" -> :park

:park
switch_scene my_novel_park
end
```

Tras guardar, el pueblo ofrece de inmediato el nuevo camino, y elegirlo abre la escena nueva:

![La decisión del pueblo ahora tiene tres opciones: Entrar en el bar, Go to the park, Volver a casa.](assets/new-choice.jpg)

Para tener los editores en tu propio juego, añádele el mod **Editor Tools** en el esquema, o lanza el juego por una sesión con ellos:

```text
godot --path . -- --game=novel_template --new --with-mods=editor_tools
```

Una línea nueva sin `key=` muestra su texto literal en todos los idiomas; añade una clave y traducciones cuando la historia esté lista para traducirse.

## Si algo no funciona

1. **Una decisión no lleva a ninguna parte.** El nombre del `switch_scene` debe ser igual al `name` de la escena destino, mayúsculas incluidas.
2. **Una escena no tiene texto.** La cabecera `scene:` de su escenario debe nombrar esa escena.
3. **El texto sale en inglés en otro idioma.** La línea no tiene `key=`, o la clave falta en el `scenarios_<lang>.properties` de ese idioma.
4. **Fondo negro.** El nombre del fondo está mal escrito, o el mod que tiene la imagen no está en `depends`.
