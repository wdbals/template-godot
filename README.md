# Puppies Game Template

Plantilla base para proyectos de Godot 4 que usan
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
`config/name` en `project.godot`, el icono en `game_content/icon.png` y los
controles en **Project > Project Settings > Input Map**.

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

Los componentes `Hitbox` y `HurtBox` de Puppy Core no deciden equipos. Configura
cada instancia para que el ataque observe únicamente la hurtbox contraria. Por
ejemplo, `PLAYER_HITBOX` usa una máscara para `ENEMY_HURTBOX`, y
`ENEMY_HITBOX` una máscara para `PLAYER_HURTBOX`. Las hurtboxes pueden dejar su
máscara vacía porque son monitorizadas por las hitboxes.
