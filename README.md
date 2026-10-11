# Puppies Game Template

Plantilla base para proyectos de Godot 4.5 o posterior que usan
[Puppy Core](https://github.com/wdbals/puppy_core) como submódulo. El código
reutilizable vive en `puppy_core/`; las reglas, escenas y recursos propios de cada
juego viven en `game_content/`.

## Inicializar y arrancar

```bash
gh auth login
gh repo create wdbals/mi-juego \
  --template wdbals/template-godot \
  --private \
  --clone
cd mi-juego
git submodule update --init --recursive
godot --editor --path .
```

Usa `--public` en lugar de `--private` si el juego será público. Configura
`config/name` en `project.godot` y el icono en `game_content/icon.png`. El mapa
de entrada ya incluye WASD y flechas para moverse, Shift para correr, Space para
saltar, E para interactuar, clic izquierdo para atacar y Escape para pausar.
También incluye stick izquierdo y cruceta para moverse, stick presionado para
correr, A para saltar, X para interactuar, gatillo derecho para atacar y Start
para pausar. Puedes cambiar las asignaciones en **Project > Project Settings >
Input Map**.

```bash
git add .
git commit -m "chore: initialize game from template"
git push
```

Desde GitHub, usa **Use this template** y clona el juego así:

```bash
git clone --recurse-submodules git@github.com:wdbals/mi-juego.git
cd mi-juego
godot --editor --path .
```

## Estructura

```text
.
├── puppy_core/                    # Submódulo: servicios y componentes comunes
├── game_content/                  # Código y contenido específico del juego
│   ├── scenes/
│   │   ├── entities/
│   │   │   ├── player/
│   │   │   ├── enemies/
│   │   │   ├── items/
│   │   │   └── props/
│   │   ├── world/
│   │   │   ├── levels/
│   │   │   └── environment/
│   │   ├── ui/
│   │   │   ├── hud/
│   │   │   ├── menus/
│   │   │   └── common/
│   │   └── main.tscn
│   ├── autoloads/                 # Coordinadores específicos del juego
│   ├── modules/                   # Módulos específicos del juego
│   ├── assets/
│   │   ├── images/
│   │   ├── audio/
│   │   ├── music/
│   │   ├── fonts/
│   │   └── materials/
│   └── icon.png
├── tests/                         # Pruebas locales; ignorado por Git
├── exports/                       # Builds; ignorado por Git
├── godot.svg
└── project.godot
```

Los directorios vacíos versionados contienen un `.gitkeep`, que se puede borrar al
añadir el primer archivo real.

Como recomendación, guarda los recursos exclusivos de una escena junto a ella, en
subcarpetas locales como `assets/`. Reserva `game_content/assets/` para recursos
compartidos entre varias escenas o sistemas.

## Puppy Core

No hay autoloads de Puppy Core activados por defecto. Añade a `[autoload]` en
`project.godot` únicamente los servicios que use el juego.

El layout de audio apunta directamente a
`res://puppy_core/audio/default_bus_layout.tres` y aporta los buses `Master`,
`SFX`, `UI`, `Music` y `Voice`.

## Capas de colisión

Las mismas capas están declaradas para física 2D y 3D:

| Capa | Nombre | Uso previsto |
| ---: | --- | --- |
| 1 | `WORLD` | Suelo y geometría estática |
| 2 | `PLAYER_BODY` | Cuerpo físico del jugador |
| 3 | `ENEMY_BODY` | Cuerpos físicos de enemigos |
| 4 | `PLAYER_HURTBOX` | Zona que recibe daño el jugador |
| 5 | `PLAYER_HITBOX` | Ataques del jugador |
| 6 | `ENEMY_HURTBOX` | Zona que recibe daño un enemigo |
| 7 | `ENEMY_HITBOX` | Ataques enemigos |
| 8 | `PLAYER_PROJECTILE` | Proyectiles del jugador |
| 9 | `ENEMY_PROJECTILE` | Proyectiles enemigos |
| 10 | `ITEMS` | Objetos recogibles |
| 11 | `PROPS` | Objetos físicos del mundo |
| 12 | `INTERACTABLES` | Objetos con interacción |
| 13 | `TRIGGERS` | Áreas de eventos y detección |

Los componentes `Hitbox`/`HurtBox` y `Hitbox3D`/`HurtBox3D` de Puppy Core no
deciden equipos. Configura cada instancia para que el ataque observe únicamente
la hurtbox contraria. Por ejemplo, `PLAYER_HITBOX` usa una máscara para
`ENEMY_HURTBOX`, y `ENEMY_HITBOX` una máscara para `PLAYER_HURTBOX`. Las hurtboxes
pueden dejar su máscara vacía porque son monitorizadas por las hitboxes.

## Capas visuales

Las capas de render son independientes de las capas físicas. Los nombres de 2D
ofrecen `DEFAULT`, `BACKGROUND`, `WORLD`, `CHARACTERS`, `FOREGROUND`, `EFFECTS`
y `UI`; los de 3D ofrecen `DEFAULT`, `WORLD`, `PLAYER`, `ENEMIES`, `EFFECTS` y
`UI`. Son etiquetas para organizar `CanvasItem`/`VisualInstance3D` y las máscaras
de `Camera2D`/`Camera3D`; cámbialas o amplíalas según el juego.
