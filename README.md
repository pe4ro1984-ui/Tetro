# DBDGenerators — Dead by Daylight «Взаперти» для Minecraft

**Версия:** 1.0.0-RELEASE  
**Совместимость:** Spigot / Paper / Purpur / Folia · Java 8 · MC 1.16.5 – 1.20.1  
**Пакет:** `ru.metalpipe.dbdgenerators`

---

## Быстрый старт

### Компиляция (требуется Java 8+ и Maven 3.6+)

```bash
cd DBDGenerators
mvn clean package
```

JAR появится в `target/DBDGenerators-1.0.0-RELEASE.jar`.  
Скопируйте его в папку `plugins/` вашего сервера.

### Первая настройка

1. Запустите сервер — плагин создаст `plugins/DBDGenerators/`
2. Войдите на сервер и зайдите в место генератора
3. `/dbd gen add` — добавьте генератор в текущей позиции (повторите нужное количество раз)
4. `/dbd loot create basic` — создайте лут-таблицу
5. `/dbd loot edit basic` — откройте GUI, перетащите предметы
6. `/dbd selfcheck` — проверьте конфигурацию
7. `/dbd start` — запустите первый матч!

---

## Структура проекта

```
DBDGenerators/
├── pom.xml
├── README.md
└── src/main/
    ├── resources/
    │   ├── plugin.yml
    │   ├── config.yml
    │   ├── messages.yml
    │   ├── generators.yml
    │   └── lootchests.yml
    └── java/ru/metalpipe/dbdgenerators/
        ├── DBDGeneratorsPlugin.java        ← Главный класс
        ├── config/ConfigManager.java       ← Управление конфигами
        ├── managers/
        │   ├── MatchManager.java           ← Жизненный цикл матча
        │   ├── GeneratorManager.java       ← Логика генераторов
        │   ├── PlayerStateManager.java     ← Состояния выживших
        │   └── LootManager.java            ← Лут-система
        ├── model/
        │   ├── MatchPhase.java             ← Enum фаз: LOBBY→IN_GAME→COLLAPSE→FINISHED
        │   ├── PlayerRole.java             ← Enum ролей: SURVIVOR/HUNTER/SPECTATOR
        │   ├── PlayerState.java            ← Enum состояний: HEALTHY/INJURED/DOWNED/HOOKED/DEAD
        │   ├── Match.java                  ← Данные активного матча
        │   ├── Generator.java              ← Данные генератора
        │   └── LootChest.java              ← Данные лут-таблицы
        ├── visual/
        │   ├── VisualProvider.java         ← Интерфейс провайдера
        │   ├── NativeBlockProvider.java    ← Блок + голограмма (без зависимостей)
        │   ├── NativeMobProvider.java      ← ArmorStand + голограмма (без зависимостей)
        │   ├── ModelEngineProvider.java    ← Soft: ModelEngine
        │   ├── ItemsAdderProvider.java     ← Soft: ItemsAdder
        │   └── VisualProviderRegistry.java ← Реестр провайдеров
        ├── commands/DBDCommand.java        ← Все подкоманды + Tab Complete
        ├── listeners/
        │   ├── GeneratorListener.java      ← ПКМ генератор, Shift skill-check
        │   ├── PlayerListener.java         ← Урон, смерть, движение, выход
        │   └── LootChestListener.java      ← Открытие сундуков, закрытие GUI
        ├── gui/LootEditorGUI.java          ← GUI-редактор лут-таблиц
        ├── task/
        │   ├── MatchTask.java              ← Основной тик матча
        │   ├── SkillCheckTask.java         ← BossBar skill-check механика
        │   └── CollapseTask.java           ← Фаза коллапса + звуки
        └── util/
            ├── MessageUtil.java            ← Локализованные сообщения
            └── ParticleUtil.java           ← Частицы ремонта, vision, коллапса
```

---

## Команды

| Команда | Право | Описание |
|---------|-------|----------|
| `/dbd start` | `dbdgenerators.start` | Запустить матч |
| `/dbd stop` | `dbdgenerators.stop` | Остановить матч |
| `/dbd reload` | `dbdgenerators.reload` | Перезагрузить конфиг |
| `/dbd status` | `dbdgenerators.status` | Статус текущего матча |
| `/dbd debug` | `dbdgenerators.debug` | Отладочная информация |
| `/dbd selfcheck` | `dbdgenerators.selfcheck` | Проверка конфигурации |
| `/dbd gen add` | `dbdgenerators.setup` | Добавить генератор (позиция игрока) |
| `/dbd gen remove <id>` | `dbdgenerators.setup` | Удалить генератор |
| `/dbd gen list` | `dbdgenerators.setup` | Список всех генераторов |
| `/dbd loot create <n>` | `dbdgenerators.loot.create` | Создать лут-таблицу |
| `/dbd loot delete <n>` | `dbdgenerators.loot.delete` | Удалить лут-таблицу |
| `/dbd loot list` | `dbdgenerators.loot.list` | Список лут-таблиц |
| `/dbd loot edit <n>` | `dbdgenerators.loot.create` | Открыть GUI-редактор |
| `/dbd loot additem <n>` | `dbdgenerators.loot.create` | Добавить предмет из руки |
| `/dbd vision on\|off` | `dbdgenerators.vision` | Режим наблюдателя с частицами |
| `/dbd provider <n>` | `dbdgenerators.provider` | Сменить визуальный провайдер |

**Алиасы:** `/generator`, `/gens`, `/dbdg`

---

## Права

| Право | Кому выдано по умолчанию |
|-------|--------------------------|
| `dbdgenerators.*` | OP |
| `dbdgenerators.start/stop/reload` | OP |
| `dbdgenerators.status/debug/selfcheck` | OP |
| `dbdgenerators.setup` | OP |
| `dbdgenerators.vision/provider` | OP |
| `dbdgenerators.loot.*` | OP |
| `dbdgenerators.repair` | Все (true) |

---

## Визуальные провайдеры

| ID | Описание | Зависимость |
|----|----------|-------------|
| `NATIVE_BLOCK` | Ванильный блок (Furnace) + голограмма | Нет |
| `NATIVE_MOB` | ArmorStand с головой + голограмма | Нет |
| `MODEL_ENGINE` | Кастомная 3D-модель | ModelEngine (мягкая) |
| `ITEMS_ADDER` | Кастомный блок | ItemsAdder (мягкая) |

Смена провайдера: `/dbd provider NATIVE_BLOCK`

---

## Механика матча

```
LOBBY ──(min-players достигнут + lobby-countdown)──► IN_GAME
                                                         │
                           ┌─────────────────────────────┘
                           │
                    ┌──────▼──────┐
                    │  Генераторы  │  Выжившие чинят (1% / сек)
                    │  Skill-Check │  Охотник саботирует
                    │  Роли/Лут    │  Мрачные Сигилы за действия
                    └──────┬──────┘
                           │
              ┌────────────▼───────────────┐
              │  generators-required чинить │
              └────────────┬───────────────┘
                           │
                   ENDGAME_COLLAPSE
                  (collapse-duration сек)
                           │
                ┌──────────┴──────────┐
                │                     │
         Выжившие спаслись      Таймаут / все мертвы
              SURVIVED               HUNTER WON
```

---

## Конфиг: ключевые параметры

| Параметр | Описание | По умолчанию |
|----------|----------|--------------|
| `match.min-players` | Мин. игроков для старта | `2` |
| `match.generators-required` | Генераторов для открытия ворот | `5` |
| `match.match-duration` | Длина матча (сек) | `600` |
| `generators.repair-speed` | % ремонта в секунду | `1.0` |
| `generators.sabotage-speed` | % саботажа в секунду | `5.0` |
| `generators.regression-speed` | Авторегрессия (% / сек) | `0.5` |
| `generators.skill-check.enabled` | Включить skill-check | `true` |
| `loot.sigil-reward-escape` | Сигилов за эвакуацию | `100` |
| `visual.provider` | Визуальный провайдер | `NATIVE_MOB` |

---

## Soft-зависимости

Плагин работает **без** ModelEngine и ItemsAdder.  
При их наличии автоматически становятся доступны соответствующие провайдеры.

Для ModelEngine: создайте модель с именем `dbd_generator` и настройте `visual.model-engine-model-id`.  
Для ItemsAdder: создайте кастомный блок `dbdgenerators:generator` и настройте `visual.items-adder-model-id`.

---

## Лицензия

MIT — используйте, модифицируйте, распространяйте свободно.
