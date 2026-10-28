# UnflyUi

Темная монохромная UI-библиотека для Roblox (Luau) с боковым меню, карточками в две колонки, выпадающими списками поверх строк, баннером, настройками и небольшой круглой кнопкой открытия. Адаптирована под телефоны.

Издатель: UnflyTm. Подпись «Created by UnflyTm» зашита в библиотеку и изменению не подлежит.

## Возможности

- Окно с шапкой, боковым меню, баннером, двумя колонками карточек и нижней панелью.
- Вкладки создаются самим разработчиком скрипта. В библиотеке нет готовых вкладок, кроме служебной страницы настроек.
- Элементы: тумблер, ползунок, выпадающий список (одиночный и множественный выбор), выбор цвета, поле ввода, бинд клавиши, кнопка, текстовая метка.
- Небольшая круглая кнопка для показа и скрытия интерфейса (перетаскивается). Кольцо по краю сияет: по нему бежит световой блик, вокруг мягкое свечение.
- Сворачивание в верхнюю полоску: кнопка «—» в шапке складывает окно в плашку с иконкой, иконка раскрывает его обратно, крестик на плашке убирает интерфейс совсем.
- Свой логотип: ссылка на картинку или id ассета, задается в коде или в настройках.
- Блокировка названия и логотипа: разработчик скрипта может запретить пользователям менять их в настройках.
- Ссылки на Discord, Telegram и любые другие ресурсы в настройках, у каждой кнопка Copy.
- Набор из 40 иконок, нарисованных элементами интерфейса, плюс поддержка своих иконок по id ассета.
- Свое название интерфейса (скрипта) в коде или в настройках.
- Расширенный API для скриптов: короткая запись элементов, циклы на тумблерах, подписки на значения, описание интерфейса таблицей, автозагрузка конфига.
- Встроенные настройки: прозрачность, цвет акцента, цвет неона, название, логотип, клавиша открытия, сброс позиции, конфиги.
- Ползунок «Размер GUI», изменение размера окна за уголок, перетаскивание окна за шапку или за нижнюю плашку.
- Сохранение и загрузка конфигов по флагам (`Flag`).

## Требования

- Executor с поддержкой `loadstring` и `game:HttpGet`.
- Для сохранения настроек и конфигов нужны `writefile`, `readfile`, `isfile`, `isfolder`, `makefolder`, `listfiles`, `delfile`. Без них данные живут только до закрытия игры.
- Для логотипа по ссылке (`https://...`) дополнительно нужны `getcustomasset` (или `getsynasset`) и `writefile`. Логотип по id ассета этих функций не требует.

## Загрузка через HttpGet

1. Выложите файл `UnflyUi.lua` туда, где его можно получить как чистый текст (GitHub raw, Pastebin raw, свой хостинг).
2. Подставьте свою ссылку в `URL`.

```lua
local URL = "https://raw.githubusercontent.com/USERNAME/REPOSITORY/main/UnflyUi.lua"

local ok, Library = pcall(function()
	return loadstring(game:HttpGet(URL))()
end)

if not ok then
	warn("[UnflyUi] load failed: " .. tostring(Library))
	return
end
```

Ссылка должна вести на сырой текст файла. Для GitHub это адрес вида `raw.githubusercontent.com/...`, а не страница `github.com/.../blob/...`. GitHub кэширует raw-файлы на несколько минут, поэтому после обновления может отображаться старая версия.

Загрузка с диска, если файл лежит в папке executor:

```lua
local Library = loadstring(readfile("UnflyUi.lua"))()
```

## Быстрый старт

```lua
local Window = Library:CreateWindow({
	Name = "My Script",
	Tagline = "Better UI - Better Experience",
	Logo = "rbxassetid://0",
	ToggleKey = Enum.KeyCode.RightShift,
})

local Main = Window:AddTab({ Name = "Main", Icon = "home" })
Main:AddBanner({ Title = "Welcome to", Subtitle = "Your perfect script hub for Roblox" })

local Left = Main:AddSection({ Name = "Player", Icon = "gear", Position = "left" })

Left:AddToggle({
	Name = "Enable Feature",
	Icon = "bolt",
	Flag = "feature",
	Callback = function(value)
		print("feature", value)
	end,
})

Left:AddSlider({ Name = "Speed", Icon = "bolt", Min = 0, Max = 500, Default = 250, Flag = "speed" })
Left:AddDropdown({ Name = "Mode", Icon = "target", Options = { "Normal", "Fast", "Safe" }, Default = "Normal", Flag = "mode" })
```

Полный пример находится в `UnflyUiExample.lua`.

## Продвинутый API

### Короткая запись элементов

Не нужно писать таблицы настроек. Флаг создается автоматически из названия (`"Auto Farm"` превращается в `auto_farm`), поэтому конфиги работают сразу.

```lua
local Tab = Window:Tab("Main", "home")
local Sec = Tab:Section("Player", "left", "gear")

Sec:Toggle("Auto Farm", false, function(value) end)
Sec:Slider("Speed", 0, 500, 250, function(value) end)
Sec:Dropdown("Mode", { "Normal", "Fast" }, "Normal", function(value) end)
Sec:Input("Target", "", function(text) end)
Sec:Color("Color", Color3.new(1, 1, 1), function(color) end)
Sec:Key("Panic key", Enum.KeyCode.X, function() end)
Sec:Button("Run", function() end)
Sec:Label("Any text")
```

`Window:Tab(name, icon)` и `Tab:Section(name, position, icon)` возвращают уже созданный объект, если он есть, иначе создают новый. Поэтому их можно вызывать из разных частей скрипта.

### Цикл, привязанный к тумблеру

Поле `Loop` запускает функцию каждый кадр, пока тумблер включен, и останавливает при выключении или выгрузке интерфейса.

```lua
Sec:AddToggle({
	Name = "Auto Farm",
	Flag = "farm",
	Loop = function(dt)
	end,
})
```

### Доступ к значениям и подписки

```lua
Library.Flags.speed
Library:Get("speed")
Library:Set("speed", 300)
Library.Options.speed:Set(300, true)

local disconnect = Library:OnChanged("speed", function(value)
	print(value)
end)
disconnect()
```

`Library.Options` хранит объекты элементов по их `Flag`. `OnChanged` срабатывает при любом изменении значения пользователем, конфигом или через `Set` (кроме вызова с `silent = true`) и возвращает функцию отписки.

### Описание интерфейса одной таблицей

`Window:Build(schema)` создает вкладки, секции и элементы по описанию и возвращает таблицу созданных элементов по флагам.

```lua
local made = Window:Build({
	{
		Tab = "Misc",
		Icon = "dots",
		Banner = { Title = "Welcome to", Subtitle = "Misc tools" },
		Sections = {
			{
				Name = "Tools",
				Position = "left",
				Icon = "bolt",
				Items = {
					{ Type = "Toggle", Name = "Fullbright", Flag = "fullbright" },
					{ Type = "Slider", Name = "FOV", Min = 40, Max = 120, Default = 70 },
					{ Type = "Dropdown", Name = "Mode", Options = { "A", "B" }, Default = "A" },
					{ Type = "Button", Name = "Run", ButtonText = "Go", Callback = function() end },
					{ Type = "Label", Text = "Any text" },
				},
			},
		},
	},
})
```

Доступные типы: `Toggle`, `Slider`, `Dropdown`, `Color`, `Input`, `Key`, `Button`, `Label`. Поля элемента те же, что у методов `Add...`. Если `Flag` не указан, он создается автоматически. В конце `Build` вызывает `Window:Ready()`.

### Очистка при выгрузке

```lua
Window:OnUnload(function()
	print("unloaded")
end)
```

Функции вызываются при `Window:Destroy()`, при кнопке Unload в настройках и при повторном запуске скрипта.

### Автозагрузка конфига

Укажите `AutoLoad = "default"` в `CreateWindow` и вызовите `Window:Ready()` после создания всех элементов (или используйте `Window:Build`). Конфиг `default` применится автоматически.

### Повторный запуск

Если скрипт запущен повторно, предыдущее окно удаляется автоматически, а `Library.Flags`, `Library.Options` и подписки очищаются. Дубликаты интерфейса не появляются.

## Управление

| Действие | Как |
| --- | --- |
| Показать или скрыть интерфейс | Круглая кнопка или клавиша `ToggleKey` (по умолчанию RightShift) |
| Свернуть в полоску | Кнопка «—» в шапке окна |
| Раскрыть из полоски | Нажать иконку на верхней плашке |
| Убрать интерфейс совсем | Крестик на верхней плашке. Вернуть можно круглой кнопкой |
| Переместить окно | Перетащить шапку или нижнюю плашку «Перетащите чтобы переместить» |
| Переместить кнопку или плашку | Перетащить пальцем или мышью |
| Изменить размер окна | Перетащить уголок в правом нижнем углу |
| Масштаб интерфейса | Ползунок «Размер GUI» внизу (50-150%) |
| Настройки | Пункт Settings с иконкой шестеренки внизу бокового меню |
| Выгрузить интерфейс | Кнопка «X» в шапке, нажать два раза подряд |

## Ссылки сообщества

Если указать `Discord`, `Telegram` или `Links`, в настройках появится секция Community. Для каждой ссылки показывается название, адрес и кнопка Copy. Если executor не поддерживает буфер обмена (`setclipboard`), приходит уведомление, а адрес остается на экране для ручного копирования.

```lua
local Window = Library:CreateWindow({
	Name = "My Script",
	Discord = "https://discord.gg/yourinvite",
	Telegram = "https://t.me/yourchannel",
	Links = { Site = "https://example.com" },
	LockBranding = true,
})

Window:AddLink({ Name = "Docs", Url = "https://example.com/docs", Icon = "code" })
```

## Название, логотип и издатель

- Название (`Name`) показывается в шапке, в левой карточке, на верхней плашке и в баннере. Меняется в `CreateWindow`, через `Window:SetName("...")` или в настройках (секция Branding, поле UI name).
- Логотип (`Logo`) принимает ссылку на картинку (`https://...`), `rbxassetid://123` или просто число. Он подставляется в шапку, левую карточку, круглую кнопку и верхнюю плашку. Задается в `CreateWindow`, через `Window:SetLogo("...")` или в настройках (секция Branding, поле Logo image). Выбор из настроек запоминается.
- Издатель всегда UnflyTm. Параметра для его изменения нет.
- Если в `CreateWindow` указать `LockBranding = true`, поля названия и логотипа в настройках заменяются пометкой «locked by the script author», а ранее сохраненный пользователем логотип игнорируется. Сам разработчик по-прежнему может менять их из кода через `SetName` и `SetLogo`. Для раздельной блокировки используйте `LockName` и `LockLogo`.

## API

### Library

| Метод | Описание |
| --- | --- |
| `Library:CreateWindow(config)` | Создает окно и возвращает объект окна |
| `Library:Notify({ Title, Content, Duration })` | Показывает уведомление справа внизу |
| `Library:Copy(text)` | Копирует текст в буфер обмена. Возвращает `false`, если executor не поддерживает буфер |
| `Library.Flags` | Таблица текущих значений элементов по их `Flag` |
| `Library.Options` | Таблица объектов элементов по их `Flag` |
| `Library:Get(flag)` / `Library:Set(flag, value, silent)` | Чтение и запись значения по флагу |
| `Library:OnChanged(flag, fn)` | Подписка на изменение значения, возвращает функцию отписки |

### CreateWindow: параметры

| Параметр | Тип | По умолчанию | Описание |
| --- | --- | --- | --- |
| `Name` | string | `"UnflyUi"` | Название интерфейса или скрипта |
| `Logo` | string | пусто | Ссылка на картинку, `rbxassetid://...` или число. Пусто означает встроенный логотип |
| `Tagline` | string | `"Better UI - Better Experience"` | Слоган в шапке (на узких окнах скрыт) |
| `User` | string | ник игрока | Имя в плашке пользователя |
| `Role` | string | `"Creator"` | Подпись под именем |
| `SizeLabel` | string | `"Размер GUI"` | Подпись ползунка размера |
| `DragLabel` | string | `"Перетащите чтобы переместить"` | Текст на плашке перемещения |
| `ToggleKey` | KeyCode | `RightShift` | Клавиша показа и скрытия |
| `Size` | Vector2 | 900x590 (ПК), 720x430 (телефон) | Базовый размер окна |
| `Accent` | Color3 | серо-белый | Цвет акцента |
| `Neon` | Color3 | голубовато-белый | Цвет кольца круглой кнопки |
| `ConfigFolder` | string | `"UnflyUi"` | Папка для настроек и конфигов |
| `AutoLoad` | string | пусто | Имя конфига, который применяется в `Window:Ready()` |
| `LockBranding` | boolean | `false` | Запрещает пользователям менять название и логотип в настройках |
| `LockName` | boolean | `false` | Запрещает менять только название |
| `LockLogo` | boolean | `false` | Запрещает менять только логотип |
| `Discord` | string | пусто | Ссылка на Discord для секции Community |
| `Telegram` | string | пусто | Ссылка на Telegram для секции Community |
| `Links` | table | пусто | Дополнительные ссылки: `{ Site = "https://..." }` или `{ { Name = "Site", Url = "https://...", Icon = "globe" } }` |

### Окно

| Метод | Описание |
| --- | --- |
| `Window:AddTab({ Name, Icon })` | Добавляет вкладку |
| `Window:Tab(name, icon)` | Возвращает вкладку с таким названием или создает ее |
| `Window:Build(schema)` | Создает интерфейс по таблице и возвращает созданные элементы |
| `Window:Ready()` | Применяет `AutoLoad`. Вызывается автоматически в `Build` |
| `Window:OnUnload(fn)` | Функция очистки при выгрузке интерфейса |
| `Window:AddLink({ Name, Url, Icon })` | Добавляет ссылку с кнопкой Copy в секцию Community настроек |
| `Window:SetName(text)` | Меняет название интерфейса |
| `Window:SetLogo(text)` | Меняет логотип. Пустая строка возвращает встроенный. Возвращает `true` или `false` |
| `Window:SetState("open" / "collapsed" / "hidden")` | Открыть, свернуть в полоску или убрать |
| `Window:Toggle()` / `Window:SetVisible(bool)` | Показать или скрыть окно |
| `Window:SetScale(number)` | Масштаб от 0.5 до 1.5 |
| `Window:SetTransparency(number)` | Прозрачность от 0 до 0.85 |
| `Window:SetAccent(Color3)` | Цвет акцента |
| `Window:SetNeon(Color3)` | Цвет неонового кольца |
| `Window:SetFloatButton(bool)` | Показать или скрыть круглую кнопку |
| `Window:Notify({ Title, Content, Duration })` | Уведомление |
| `Window:SaveConfig(name)` / `LoadConfig(name)` / `DeleteConfig(name)` | Работа с конфигами |
| `Window:ListConfigs()` | Список имен сохраненных конфигов |
| `Window:Destroy()` | Полностью удаляет интерфейс |

### Вкладка

| Метод | Описание |
| --- | --- |
| `Tab:AddBanner({ Title, Highlight, Subtitle })` | Баннер сверху страницы. Если `Highlight` не указан, показывается название интерфейса |
| `Tab:AddSection({ Name, Icon, Position })` | Карточка. `Position` равно `"left"` или `"right"`. На узких экранах колонка одна |
| `Tab:Section(name, position, icon)` | Возвращает секцию с таким названием или создает ее |

Элементы можно добавлять и прямо во вкладку: они попадут в автоматически созданную карточку «General».

### Элементы

Все методы вызываются у секции (`Section:AddToggle(...)`). У каждого элемента есть необязательные поля `Icon` (название иконки), `Flag` (ключ для конфигов и `Library.Flags`) и `Callback`.

| Метод | Поля |
| --- | --- |
| `AddToggle` | `Name`, `Default`, `Loop` (функция каждый кадр, пока включено) |
| `AddSlider` | `Name`, `Min`, `Max`, `Default`, `Increment` (или `Rounding`), `Suffix` |
| `AddDropdown` | `Name`, `Options`, `Default`, `Multi`, `OnOpen` |
| `AddColorPicker` | `Name`, `Default` |
| `AddTextBox` | `Name`, `Default`, `Placeholder` |
| `AddKeybind` | `Name`, `Default`, `Callback` (нажатие клавиши), `Changed` (смена бинда) |
| `AddButton` | `Name`, `ButtonText`, `Callback` |
| `AddLabel(text)` | Текстовая строка |
| `AddLink` | `Name`, `Url`, `Icon`. Строка со ссылкой и кнопкой Copy |

Короткие формы: `Toggle`, `Slider`, `Dropdown`, `Input`, `Color`, `Key`, `Button`, `Label`, `Link` (см. раздел «Продвинутый API»).

Объекты элементов (кроме кнопки и метки) имеют методы `:Set(value, silent)` и `:Get()`. У выпадающего списка есть еще `:SetOptions(table)`. У метки есть `:Set(text)`.

### Иконки

Название иконки указывается в поле `Icon` у вкладок, секций и элементов.

`ring`, `home`, `gear`, `eye`, `target`, `person`, `users`, `pin`, `dots`, `bolt`, `shield`, `expand`, `lock`, `chevron`, `check`, `minus`, `cross`, `plus`, `search`, `bell`, `clock`, `folder`, `code`, `link`, `copy`, `chat`, `send`, `info`, `sliders`, `palette`, `globe`, `trash`, `flag`, `star`, `sword`, `wrench`, `map`, `key`, `image`, `download`.

Есть синонимы: `settings` (gear), `close` (cross), `crosshair` и `aim` (target), `user` (person), `players` (users), `discord` (chat), `telegram` (send), `combat` (sword), `teleport` и `teleports` (pin), `visuals` (eye), `misc` (dots), `movement` (bolt), `tools` (wrench), `world` (globe), `config` (folder), `save` (download), `add` (plus), `delete` (trash), `find` (search), `notification` (bell), `time` (clock), `script` (code), `theme` (palette), `warning` (info). Для неизвестного названия используется `ring`.

Своя иконка: вместо названия укажите `rbxassetid://123` или число. Картинка окрашивается в цвет иконок интерфейса, поэтому лучше использовать белые или монохромные изображения.

## Конфиги

Значения всех элементов с `Flag` сохраняются в `UnflyUi/configs/<имя>.json`. В настройках есть секция Configs: поле имени, кнопка сохранения и список для загрузки. То же самое доступно из кода:

```lua
Window:SaveConfig("default")
Window:LoadConfig("default")
```

Настройки интерфейса (масштаб, прозрачность, цвета, кнопка, логотип) хранятся в `UnflyUi/settings.json`.

## Телефоны

- Окно меньше, строки, тумблеры и ползунки крупнее, окно автоматически подгоняется под экран.
- Во время перетаскивания ползунка прокрутка страницы отключается.
- Круглую кнопку и верхнюю плашку можно перетаскивать пальцем.

## Частые проблемы

| Проблема | Что проверить |
| --- | --- |
| `HttpGet` возвращает ошибку или HTML | Ссылка должна вести на raw-текст, а не на страницу сайта |
| Интерфейса не видно | Нажмите круглую кнопку или клавишу `ToggleKey`. Если окно свернуто, нажмите иконку на верхней плашке |
| Логотип по ссылке не грузится | Нужна прямая ссылка на png или jpg и поддержка `getcustomasset` и `writefile` в executor. Попробуйте id ассета |
| Логотип по id не показывается | Нужен id именно изображения (Image), а не декали |
| Конфиги не сохраняются между запусками | Executor не поддерживает файловые функции |
| После обновления файла видна старая версия | Подождите несколько минут или добавьте к ссылке параметр, например `?v=2` |

## Авторы

UnflyUi, издатель UnflyTm.
