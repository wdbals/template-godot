# Puppies Game Template

Plantilla base para proyectos de Godot 4 que usan
[Puppy Core](https://github.com/wdbals/puppy_core) como submódulo. El código
reutilizable vive en `puppy_core/`; las reglas, escenas y recursos propios de cada
juego viven en `game_content/`.

## Crear un proyecto desde la plantilla

Después de crear o clonar el repositorio del juego:

```bash
git submodule update --init --recursive
godot --editor --path .
```

Cambia `application/config/name` en `project.godot`, sustituye
`game_content/icon.png` y configura las acciones de entrada en **Project > Project
Settings > Input Map**. La plantilla declara las acciones que esperan los
componentes de movimiento de Puppy Core (`move_left`, `move_right`,
`move_forward`, `move_backward` y `sprint`), pero las deja sin teclas para que cada
juego defina sus controles.

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

La plantilla activa los servicios generales `AudioManager`, `InputManager`,
`PauseManager`, `SaveManager`, `SceneManager` y `VideoManager`. Todos son
opcionales: elimina de `[autoload]` en `project.godot` los que el proyecto no use.

`UISoundManager` se deja desactivado porque necesita un perfil de sonidos propio
del juego. Si se usa, hay que registrarlo después de `AudioManager`. El overlay de
depuración también es optativo:

```ini
[autoload]

AudioManager="*res://puppy_core/autoloads/audio_manager.gd"
UISoundManager="*res://puppy_core/autoloads/ui_sound_manager.gd"
DebugOverlay="*res://puppy_core/autoloads/debug_overlay.tscn"
```

El layout de audio apunta directamente a
`res://puppy_core/audio/default_bus_layout.tres` y aporta los buses `Master`,
`SFX`, `UI`, `Music` y `Voice`.

No añadas cambios propios del juego dentro de `puppy_core/`. Para actualizar la
versión fijada por el proyecto:

```bash
git -C puppy_core fetch --tags
git -C puppy_core checkout <tag-o-commit>
git add puppy_core
git commit -m "build: update puppy_core"
```

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
