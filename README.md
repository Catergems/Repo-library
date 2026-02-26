# 📦 LibraryGUI v2
> A clean, lightweight Roblox UI Library for building script hubs and GUIs — with tabs, sections, buttons, toggles, sliders, dropboxes, textboxes, labels, keybinds, notifications, and config save/load.

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
  - [Slider](#slider)
  - [Dropbox](#dropbox)
  - [Textbox](#textbox)
  - [Label](#label)
  - [Keybind](#keybind)
  - [Notifications](#notifications)
  - [Config Save & Load](#config-save--load)
- [Full Example](#-full-example)

---

## 📖 API Reference

---

### Library

#### `lib:CreateWindow(title)`
Creates the main GUI window.

| Parameter | Type | Description |
|-----------|------|-------------|
| `title` | `string` | Title shown at the top of the tab bar |

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
| `name` | `string` | Tab button label |

**Returns:** `Tab`

```lua
local tab = window:CreateTab("Player")
```

---

#### `window:hide()`
Hides the window with a smooth shrink animation. Works great with keybinds and mobile buttons.

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

-- Keybind
game:GetService("UserInputService").InputBegan:Connect(function(input, gpe)
    if gpe then return end
    if input.KeyCode == Enum.KeyCode.RightShift then
        visible = not visible
        if visible then window:show() else window:hide() end
    end
end)

-- Button
sect:CreateButton("Toggle GUI", "Show or hide the window", function()
    visible = not visible
    if visible then window:show() else window:hide() end
end)
```

---

#### `window:Notify(title, message, duration)`
Shows a toast notification that slides in from the bottom-right corner with a countdown progress bar, then auto-dismisses.

| Parameter | Type | Description |
|-----------|------|-------------|
| `title` | `string` | Bold header text |
| `message` | `string` | Body text |
| `duration` | `number` | Seconds before auto-dismiss (default: `3`) |

```lua
window:Notify("Speed Boost", "Activated!", 3)
window:Notify("Error", "Player not found.", 2.5)
```

---

#### `window:SaveConfig(name)`
Saves all toggle, slider, and textbox values to a JSON file using `writefile`.

| Parameter | Type | Description |
|-----------|------|-------------|
| `name` | `string` | File name without extension (e.g. `"myconfig"` → `myconfig.json`) |

```lua
window:SaveConfig("myconfig")
```

> ⚠️ Requires an executor that supports `writefile`.

---

#### `window:LoadConfig(name)`
Loads and applies saved values from a JSON config file using `readfile`.

| Parameter | Type | Description |
|-----------|------|-------------|
| `name` | `string` | File name without extension |

```lua
window:LoadConfig("myconfig")
```

> 💡 Toggles, sliders, and textboxes are **automatically tracked** — no extra setup needed. Just call `SaveConfig` and `LoadConfig`.

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
A clickable button. Tooltip appears at mouse position on hover.

| Parameter | Type | Description |
|-----------|------|-------------|
| `name` | `string` | Button label |
| `description` | `string` | Tooltip shown on hover |
| `callback` | `function` | Called on click |

```lua
sect:CreateButton("Teleport", "Teleports to spawn", function()
    -- your code
end)
```

---

#### `sect:CreateToggle(name, description, default, callback)`
An animated on/off toggle. Tooltip appears on hover. **Auto-saved by config system.**

| Parameter | Type | Description |
|-----------|------|-------------|
| `name` | `string` | Toggle label |
| `description` | `string` | Tooltip shown on hover |
| `default` | `boolean` | Starting state (`true` = on) |
| `callback` | `function(state: boolean)` | Called when toggled |

**Returns:** `ToggleObject`

```lua
local toggle = sect:CreateToggle("Speed Boost", "Doubles walkspeed", false, function(state)
    game.Players.LocalPlayer.Character.Humanoid.WalkSpeed = state and 60 or 16
end)

toggle:GetState()      -- returns boolean
toggle:SetState(true)  -- force set
```

---

#### `sect:CreateSlider(name, description, min, max, default, callback)`
A draggable slider with live value display. Supports mouse and touch. **Auto-saved by config system.**

| Parameter | Type | Description |
|-----------|------|-------------|
| `name` | `string` | Slider label |
| `description` | `string` | Tooltip shown on hover |
| `min` | `number` | Minimum value |
| `max` | `number` | Maximum value |
| `default` | `number` | Starting value |
| `callback` | `function(value: number)` | Called when value changes |

**Returns:** `SliderObject`

```lua
local slider = sect:CreateSlider("Walk Speed", "Set walk speed", 16, 200, 16, function(val)
    game.Players.LocalPlayer.Character.Humanoid.WalkSpeed = val
end)

slider:GetValue()      -- returns number
slider:SetValue(100)   -- force set
```

---

#### `sect:CreateDropbox(name, description, items, callback)`
A dropdown selector with animated open/close. Tooltip appears on hover.

| Parameter | Type | Description |
|-----------|------|-------------|
| `name` | `string` | Dropbox label |
| `description` | `string` | Tooltip shown on hover |
| `items` | `table` | List of string options |
| `callback` | `function(selected: string)` | Called when item is picked |

**Returns:** `DropboxObject`

```lua
local drop = sect:CreateDropbox("Game Mode", "Choose a mode", {"Easy","Medium","Hard"}, function(sel)
    print("Selected:", sel)
end)

drop:refresh(newItems)   -- swap the item list
drop:GetSelected()       -- returns current string
```

**Live player list example:**
```lua
local function refresh()
    local names = {}
    for _, p in pairs(game.Players:GetPlayers()) do
        table.insert(names, p.Name)
    end
    drop:refresh(names)
end
refresh()
game.Players.PlayerAdded:Connect(refresh)
game.Players.PlayerRemoving:Connect(refresh)
```

---

#### `sect:CreateTextbox(name, description, placeholder, callback)`
A text input box. Fires callback when the user unfocuses or presses Enter. **Auto-saved by config system.**

| Parameter | Type | Description |
|-----------|------|-------------|
| `name` | `string` | Label above the input |
| `description` | `string` | Tooltip shown on hover |
| `placeholder` | `string` | Grey hint text when empty |
| `callback` | `function(text: string, enterPressed: boolean)` | Called on focus lost |

**Returns:** `TextboxObject`

```lua
local box = sect:CreateTextbox("Target Name", "Enter a player name", "e.g. Roblox", function(text, entered)
    if entered then
        print("Searching for:", text)
    end
end)

box:GetText()        -- returns string
box:SetText("hello") -- set text programmatically
box:Clear()          -- clears input
```

---

#### `sect:CreateLabel(text)`
A simple read-only text line inside a section. Useful for displaying info or version numbers.

| Parameter | Type | Description |
|-----------|------|-------------|
| `text` | `string` | Text to display |

**Returns:** `LabelObject`

```lua
local lbl = sect:CreateLabel("Version: 2.0")

lbl:SetText("Version: 2.1")  -- update text
lbl:GetText()                -- returns string
```

---

#### `sect:keybind(name, defaultKey, callback)`
A rebindable keybind row. Click the key button to listen for a new key. Fires callback every time the bound key is pressed.

| Parameter | Type | Description |
|-----------|------|-------------|
| `name` | `string` | Keybind label |
| `defaultKey` | `Enum.KeyCode` | Starting keybind |
| `callback` | `function` | Called when key is pressed |

**Returns:** `KeybindObject`

```lua
local kb = sect:keybind("Fly Toggle", Enum.KeyCode.F, function()
    print("Fly!")
end)

kb:GetKey()                   -- returns Enum.KeyCode
kb:SetKey(Enum.KeyCode.G)     -- force change key
```

> 💡 While listening for a new key, the button turns **yellow** and shows `...`. Press any key to confirm.

---

### Notifications

```lua
window:Notify("Title", "Message", duration)
```

Notifications slide in from the bottom-right with a purple accent bar and a shrinking progress bar. Multiple notifications stack automatically.

```lua
window:Notify("Welcome!", "Hub loaded successfully.", 4)
window:Notify("Toggle", "Speed Boost enabled.", 2)
window:Notify("Error", "Something went wrong.", 3)
```

---

### Config Save & Load

Toggles, sliders, and textboxes are **automatically tracked** internally. Just call save/load when needed.

```lua
-- Save button
sect:CreateButton("Save Config", "Save current settings", function()
    window:SaveConfig("myconfig")
end)

-- Load button
sect:CreateButton("Load Config", "Load saved settings", function()
    window:LoadConfig("myconfig")
end)
```

Config is saved as `myconfig.json` via `writefile` — requires an executor that supports the filesystem API.

---

## 🧪 Full Example

```lua
local lib = loadstring(game:HttpGet("https://raw.githubusercontent.com/Catergems/Repo-library/refs/heads/main/library"))()

local window = lib:CreateWindow("My Hub")

-- ── Tab 1: Player ──────────────────────────────────────
local playerTab = window:CreateTab("Player")
local moveSect  = playerTab:CreateSection("Movement")

moveSect:CreateSlider("Walk Speed", "Adjust walk speed", 16, 200, 16, function(val)
    local char = game.Players.LocalPlayer.Character
    if char then char.Humanoid.WalkSpeed = val end
end)

moveSect:CreateSlider("Jump Power", "Adjust jump power", 50, 500, 50, function(val)
    local char = game.Players.LocalPlayer.Character
    if char then char.Humanoid.JumpPower = val end
end)

moveSect:CreateToggle("Infinite Jump", "Jump any number of times", false, function(state)
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

-- ── Tab 2: Visual ──────────────────────────────────────
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

visSect:CreateButton("Refresh List", "Manually refresh", refreshList)

local infoSect = visualTab:CreateSection("Info")
local statusLabel = infoSect:CreateLabel("Status: Idle")

-- ── Tab 3: Misc ────────────────────────────────────────
local miscTab  = window:CreateTab("Misc")
local miscSect = miscTab:CreateSection("Settings")

miscSect:CreateTextbox("Custom Tag", "Set a display tag", "e.g. [VIP]", function(text)
    statusLabel:SetText("Tag: " .. text)
end)

miscSect:CreateButton("Save Config", "Save all settings", function()
    window:SaveConfig("myhub")
end)

miscSect:CreateButton("Load Config", "Load saved settings", function()
    window:LoadConfig("myhub")
end)

miscSect:CreateButton("Close GUI", "Destroys the window", function()
    window:delete()
end)

local visible = true
miscSect:keybind("Toggle GUI", Enum.KeyCode.RightShift, function()
    visible = not visible
    if visible then window:show() else window:hide() end
end)

-- Startup notification
window:Notify("My Hub", "Loaded successfully! Press RightShift to toggle.", 4)
```

---

## 📄 License
Free to use and modify. Credit appreciated but not required.
