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
- Строки элементов без иконок: название слева, элемент справа. Иконки в строках можно вернуть через `ElementIcons = true`. Иконки вкладок и заголовков секций остаются.
- Живой дизайн: по окну поднимаются мягкие частицы, градиент и рамка переливаются, по шапке проходит блик, логотип пульсирует, блики на баннере дрейфуют, страницы вкладок плавно выезжают, окно «пружинит» при открытии, тумблер и ползунок реагируют на нажатие.
- Защита от случайных нажатий: пока окно, свернутая плашка или кнопка открытия на экране, нажатия и прокрутка в их зоне не доходят до игры и других кнопок под ними.
- Блокировка названия и логотипа: разработчик скрипта может запретить пользователям менять их в настройках.
- Ссылки на Discord, Telegram и любые другие ресурсы в настройках, у каждой кнопка Copy.
- Иконки из встроенного в Roblox шрифта BuilderIcons (около 490 штук), с запасным набором нарисованных иконок и поддержкой своих по id ассета.
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

В настройках всегда есть секция Community со строками Discord и Telegram. Для каждой ссылки показывается название, адрес и кнопка Copy.

- Если ссылка задана, кнопка копирует ее в буфер обмена. Если executor не поддерживает буфер (`setclipboard`), приходит уведомление, а адрес остается на экране для ручного копирования.
- Если ссылка не задана, вместо адреса показывается «Not set», а кнопка Copy выводит уведомление «The community link is not set, please try again later.»
- Чтобы убрать пустые строки, укажите `Community = false`. Тогда секция появится только при заданных ссылках.
- Любые другие ссылки добавляются через `Links` или `Window:AddLink`. Адрес можно сменить позже через `Window:SetLink("Discord", "https://...")`.

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
| `ElementIcons` | boolean | `false` | Показывать иконки рядом с названиями элементов. По умолчанию строки без иконок |
| `Discord` | string | пусто | Ссылка на Discord для секции Community |
| `Telegram` | string | пусто | Ссылка на Telegram для секции Community |
| `Community` | boolean | `true` | Показывать строки Discord и Telegram в настройках, даже если ссылки не заданы |
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
| `Window:SetLink(name, url)` | Меняет адрес уже созданной ссылки, например `"Discord"` |
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

Все методы вызываются у секции (`Section:AddToggle(...)`). У каждого элемента есть необязательные поля `Icon` (название иконки, показывается только при `ElementIcons = true`), `Flag` (ключ для конфигов и `Library.Flags`) и `Callback`.

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

Иконки берутся из встроенного в Roblox шрифта BuilderIcons (он есть в клиенте игры, дополнительные файлы не нужны). Можно указывать как короткие имена библиотеки, так и любое имя глифа этого шрифта:

```lua
Window:Tab("Main", "home")
Window:Tab("Combat", "sword")
Window:Tab("Movement", "person-running")
Window:Tab("Shop", "shopping-cart")
```

Короткие имена библиотеки и соответствующие глифы: `home` (house), `gear`, `eye`, `target` (crosshairs), `person`, `users` (two-people), `pin` (location-pin), `dots` (three-dots-horizontal), `bolt` (lightning-bolt), `shield` (shield-check), `lock` (lock-closed), `check`, `minus`, `cross` (x), `plus` (plus-large), `search` (magnifying-glass), `bell`, `clock`, `folder`, `code`, `link` (chain-link), `copy` (two-stacked-squares), `chat` (speech-bubble-round), `send` (paper-airplane), `info` (circle-i), `sliders` (three-sliders-horizontal), `palette` (paint-brush), `globe` (globe-simplified), `trash` (trash-can), `flag`, `star`, `sword`, `wrench` (hammer-code), `map` (location-pin-map), `key`, `image`, `download` (arrow-down-to-line), `discord`, `telegram` (paper-airplane).

Синонимы: `settings`, `close`, `crosshair`, `aim`, `user`, `players`, `combat`, `teleport`, `teleports`, `visuals`, `misc`, `movement`, `tools`, `world`, `config`, `save`, `add`, `delete`, `find`, `notification`, `time`, `script`, `theme`, `warning`.

Другие полезные глифы: `person-teleport`, `person-walking`, `person-jumping`, `chart-line`, `chart-pie`, `trophy`, `heart`, `moon`, `sun`, `flame`, `pumpkin`, `compass`, `hammer-code`, `shopping-cart`, `wallet`, `microphone`, `speaker`, `video-camera`, `calendar`, `bookmark`, `cloud`, `envelope`, `fingerprint`, `list-bulleted`, `grid`, `tag-sparkle`.

Если шрифт недоступен в вашем клиенте, библиотека автоматически переключается на запасной набор нарисованных иконок (для коротких имен).

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

## Ключ-система (KeySystem.lua)

Отдельный загрузчик с проверкой ключа. Он не подгружается отдельно от скрипта: после проверки ключа сам запускает ваш скрипт. Дизайн в том же черном стиле: сверху название, ниже закругленная полоса ввода, кнопки Check и Get Key, внизу приглушенная надпись KeySystem.

### Настройка

В начале файла находится таблица `Config`:

| Поле | По умолчанию | Описание |
| --- | --- | --- |
| `Title` | `"UnflyProject"` | Заголовок сверху |
| `Subtitle` | `"KeySystem"` | Приглушенная надпись внизу |
| `Keys` | `{ "UnKey-Free-JE71KWL16MEM091" }` | Список допустимых ключей |
| `GetKeyUrl` | пусто | Ссылка для кнопки Get Key. Пока не задана, кнопка работает как заглушка |
| `ScriptUrl` | шаблон | Ссылка на raw-текст вашего скрипта, он загружается после проверки ключа |
| `OnSuccess` | `nil` | Функция, которая вызывается вместо загрузки по ссылке |
| `SaveKey` | `true` | Запоминать принятый ключ в файл |
| `AutoLogin` | `true` | Если сохраненный ключ верен, окно не показывается и скрипт запускается сразу |
| `Folder` | `"UnflyProject"` | Папка для файла с ключом |
| `Neon` | `Color3.fromRGB(150, 210, 255)` | Цвет бегущих неоновых лучей по бокам панели |

### Подключение скрипта

```lua
local Config = {
	ScriptUrl = "https://raw.githubusercontent.com/USERNAME/REPOSITORY/main/Script.lua",
}
```

Либо запуск своего кода напрямую:

```lua
OnSuccess = function()
	loadstring(game:HttpGet("https://raw.githubusercontent.com/USERNAME/REPOSITORY/main/Script.lua"))()
end,
```

Если `ScriptUrl` не задан и `OnSuccess` пуст, после принятия ключа в консоль выводится предупреждение.

### Поведение

- Check проверяет ключ (также срабатывает по Enter). При пустом поле окно подсказывает ввести ключ, при неверном ключе показывает «Invalid key» и трясется.
- Get Key копирует `GetKeyUrl` в буфер обмена. Пока ссылка не задана, показывается «Get Key is not available yet».
- Живые детали: по боковым краям панели бегут неоновые лучи (цвет задаётся в `Neon`), по фону поднимаются мягкие частицы, градиент панели медленно «переливается», вокруг логотипа бежит световая дуга, по рамке поля ключа скользит блик, на кнопках проходит лёгкий отсвет, кнопка Check мягко «дышит», элементы появляются со сдвигом, значок ключа «подпрыгивает» при вводе.
- Оформление: чёрная панель с мягким бликом, который медленно скользит по фону, светящимся логотипом и тонкой линией под заголовком. Поле ключа подсвечивается при вводе, в нём есть кнопка очистки. Строка статуса с цветным индикатором, кнопки с эффектом нажатия, а во время проверки внизу панели заполняется полоса прогресса (зелёная при успехе, красная при ошибке).
- Крест в правом верхнем углу панели (без круга, вшит в дизайн, при наведении становится ярче) выгружает загрузчик: окно плавно исчезает, скрипт не запускается. Панель и крест всегда помещаются в экран, на телефоне размер подбирается автоматически.
- Окно нельзя перемещать. Пока оно открыто, остальной интерфейс и игра заблокированы: нажатия, камера и клавиши блокируются, активна только сама ключ-система.
- На телефоне окно подгоняется под экран и смещено выше, чтобы клавиатура не закрывала поле.

### Важно

Ключи хранятся прямо в файле загрузчика, поэтому такую проверку можно обойти, прочитав код. Для реальной защиты проверку ключа нужно делать на вашем сервере.
