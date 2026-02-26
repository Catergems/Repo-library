# 📦 LibraryGUI v2
> A clean, lightweight Roblox UI Library for building script hubs and GUIs — with tabs, sections, buttons, toggles, dropboxes, sliders, and keybinds.

---

## 🚀 Load via URL

```lua
local lib = loadstring(game:HttpGet("https://raw.githubusercontent.com/Catergems/Repo-library/refs/heads/main/library"))()
```
---

## ⚡ Quick Start

```lua
local lib = loadstring(game:HttpGet("https://raw.githubusercontent.com/Catergems/Repo-library/refs/heads/main/library"))()

local window = lib:CreateWindow("My Hub")
local tab    = window:CreateTab("Player")
local sect   = tab:CreateSection("Movement")

sect:CreateButton("Say Hi", "Prints hello", function()
    print("Hello!")
end)
```

---

## 📋 Table of Contents
- [Load via URL](#-load-via-url)
- [Quick Start](#-quick-start)
- [API Reference](#-api-reference)
  - [Library](#library)
  - [Window](#window)
  - [Tab](#tab)
  - [Section](#section)
  - [Button](#button)
  - [Toggle](#toggle)
  - [Dropbox](#dropbox)
  - [Keybind](#keybind)
- [Full Example](#-full-example)

---

## 📖 API Reference

---

### Library

#### `lib:CreateWindow(title)`
Creates the main GUI window.

| Parameter | Type | Description |
|-----------|------|-------------|
| `title` | `string` | The title shown at the top of the tab bar |

**Returns:** `Window`

```lua
local window = lib:CreateWindow("My Hub")
```

---

### Window

#### `window:CreateTab(name)`
Adds a new tab to the window.

| Parameter | Type | Description |
|-----------|------|-------------|
| `name` | `string` | The tab button label |

**Returns:** `Tab`

```lua
local tab = window:CreateTab("Player")
```

---

#### `window:hide()`
Hides the window with a smooth shrink animation. Useful for keybinds and mobile buttons.

```lua
window:hide()
```

---

#### `window:show()`
Shows the window with a smooth expand animation.

```lua
window:show()
```

---

#### `window:delete()`
Completely destroys the GUI. Can be triggered by a button or keybind.

```lua
window:delete()
```

**Toggle visibility example:**
```lua
local visible = true

-- Via keybind
game:GetService("UserInputService").InputBegan:Connect(function(input, gpe)
    if gpe then return end
    if input.KeyCode == Enum.KeyCode.RightShift then
        visible = not visible
        if visible then window:show() else window:hide() end
    end
end)

-- Via button
sect:CreateButton("Toggle GUI", "Show or hide the window", function()
    visible = not visible
    if visible then window:show() else window:hide() end
end)
```

---

### Tab

#### `tab:CreateSection(name)`
Creates a labeled section inside a tab.

| Parameter | Type | Description |
|-----------|------|-------------|
| `name` | `string` | Section header text |

**Returns:** `Section`

```lua
local sect = tab:CreateSection("Movement")
```

---

### Section

#### `sect:set(newName)`
Updates the section header text dynamically.

```lua
sect:set("Movement (Active)")
```

---

#### `sect:CreateButton(name, description, callback)`
Adds a clickable button. A tooltip appears at the mouse position on hover.

| Parameter | Type | Description |
|-----------|------|-------------|
| `name` | `string` | Button label |
| `description` | `string` | Tooltip text shown on hover |
| `callback` | `function` | Called when clicked |

```lua
sect:CreateButton("Teleport", "Teleports you to spawn", function()
    -- your code
end)
```

---

#### `sect:CreateToggle(name, description, default, callback)`
Adds an animated on/off toggle. Tooltip appears on hover.

| Parameter | Type | Description |
|-----------|------|-------------|
| `name` | `string` | Toggle label |
| `description` | `string` | Tooltip text shown on hover |
| `default` | `boolean` | Initial state (`true` = on) |
| `callback` | `function(state: boolean)` | Called when toggled |

**Returns:** `ToggleObject`

```lua
local myToggle = sect:CreateToggle("Speed Boost", "Doubles your walkspeed", false, function(state)
    game.Players.LocalPlayer.Character.Humanoid.WalkSpeed = state and 60 or 16
end)

-- Methods:
myToggle:GetState()      -- returns current boolean
myToggle:SetState(true)  -- force set state
```

---

#### `sect:CreateDropbox(name, description, items, callback)`
Adds a dropdown selector. Tooltip appears on hover.

| Parameter | Type | Description |
|-----------|------|-------------|
| `name` | `string` | Dropbox label |
| `description` | `string` | Tooltip text shown on hover |
| `items` | `table` | List of string options |
| `callback` | `function(selected: string)` | Called when an item is picked |

**Returns:** `DropboxObject`

```lua
local drop = sect:CreateDropbox("Game Mode", "Select a game mode", {"Easy","Medium","Hard"}, function(selected)
    print("Picked:", selected)
end)

-- Methods:
drop:refresh(newItemsTable)  -- replace the item list entirely
drop:GetSelected()           -- returns currently selected string
```

**Refresh example (live player list):**
```lua
local function refreshPlayers()
    local names = {}
    for _, p in pairs(game.Players:GetPlayers()) do
        table.insert(names, p.Name)
    end
    drop:refresh(names)
end

refreshPlayers()
game.Players.PlayerAdded:Connect(refreshPlayers)
game.Players.PlayerRemoving:Connect(refreshPlayers)
```

---

#### `sect:keybind(name, defaultKey, callback)`
Adds a rebindable keybind row. Click the key button to listen for a new key. Fires the callback every time the bound key is pressed.

| Parameter | Type | Description |
|-----------|------|-------------|
| `name` | `string` | Keybind label |
| `defaultKey` | `Enum.KeyCode` | The starting keybind |
| `callback` | `function` | Called when the key is pressed |

**Returns:** `KeybindObject`

```lua
local kb = sect:keybind("Fly", Enum.KeyCode.F, function()
    print("Fly key pressed!")
end)

-- Methods:
kb:GetKey()               -- returns current Enum.KeyCode
kb:SetKey(Enum.KeyCode.G) -- force change the key
```

> 💡 While rebinding, the button turns **yellow** and shows `...`. Press any key to confirm.

---

## 🧪 Full Example

```lua
local lib = loadstring(game:HttpGet("https://raw.githubusercontent.com/Catergems/Repo-library/refs/heads/main/library"))()

-- Window
local window = lib:CreateWindow("My Hub")

-- ── Tab 1: Player ──────────────────────────────
local playerTab = window:CreateTab("Player")
local moveSect  = playerTab:CreateSection("Movement")

moveSect:CreateToggle("Speed Boost", "Toggle speed boost", false, function(state)
    local char = game.Players.LocalPlayer.Character
    if char then char.Humanoid.WalkSpeed = state and 60 or 16 end
end)

moveSect:CreateToggle("Infinite Jump", "Jump as many times as you want", false, function(state)
    _G.InfJump = state
end)

game:GetService("UserInputService").JumpRequest:Connect(function()
    if _G.InfJump then
        local char = game.Players.LocalPlayer.Character
        if char then char.Humanoid:ChangeState(Enum.HumanoidStateType.Jumping) end
    end
end)

moveSect:keybind("Fly Toggle", Enum.KeyCode.F, function()
    print("Fly toggled!")
end)

-- ── Tab 2: Visual ──────────────────────────────
local visualTab = window:CreateTab("Visual")
local visSect   = visualTab:CreateSection("Players")

local playerDrop = visSect:CreateDropbox("Target", "Select a player", {}, function(sel)
    print("Targeting:", sel)
end)

local function refreshList()
    local t = {}
    for _, p in pairs(game.Players:GetPlayers()) do table.insert(t, p.Name) end
    playerDrop:refresh(t)
end
refreshList()
game.Players.PlayerAdded:Connect(refreshList)
game.Players.PlayerRemoving:Connect(refreshList)

visSect:CreateButton("Refresh", "Manually refresh player list", refreshList)

-- ── Tab 3: Misc ────────────────────────────────
local miscTab  = window:CreateTab("Misc")
local miscSect = miscTab:CreateSection("Actions")

miscSect:CreateButton("Close GUI", "Destroys the window", function()
    window:delete()
end)

-- Keybind to hide/show
local visible = true
miscSect:keybind("Toggle GUI", Enum.KeyCode.RightShift, function()
    visible = not visible
    if visible then window:show() else window:hide() end
end)
```

---

## 📄 License
Free to use and modify. Credit appreciated but not required.
