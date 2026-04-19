--[[
    RAKOOF'S DEATH TEST - HUB PREMIUM (VERSÃO FINAL FUNCIONAL)
    - Interface ScreenGui nativa, leve e responsiva
    - Botão minimizar e flutuante para celular
    - Auto Farm com pausa inteligente na transformação
    - Suporte opcional para ajudar amigo
    - Scanner de armas completo (tabela com +30 armas)
    - God Mode, Stamina Infinita, ESP, Matar Instantâneo, Teleporte
]]

-- ===========================================================================
-- CONFIGURAÇÃO INICIAL E SERVIÇOS
-- ===========================================================================
local player = game.Players.LocalPlayer
local replicatedStorage = game:GetService("ReplicatedStorage")
local starterGui = game:GetService("StarterGui")
local debris = game:GetService("Debris")

-- Aguarda o personagem carregar
repeat wait() until player.Character and player.Character:FindFirstChild("HumanoidRootPart")

-- ===========================================================================
-- CRIAÇÃO DA INTERFACE (SCREENGUI) – OTIMIZADA PARA CELULAR
-- ===========================================================================
local screenGui = Instance.new("ScreenGui")
screenGui.Name = "RakoofHub"
screenGui.ResetOnSpawn = false
screenGui.Parent = player.PlayerGui

-- Painel principal (centralizado e tamanho adequado para mobile)
local mainFrame = Instance.new("Frame")
mainFrame.Size = UDim2.new(0, 300, 0, 400)
mainFrame.AnchorPoint = Vector2.new(0.5, 0.5)
mainFrame.Position = UDim2.new(0.5, 0, 0.5, 0)
mainFrame.BackgroundColor3 = Color3.fromRGB(20, 20, 25)
mainFrame.BorderSizePixel = 0
mainFrame.BackgroundTransparency = 0.02
mainFrame.ClipsDescendants = true
mainFrame.Active = true
mainFrame.Draggable = true
mainFrame.Parent = screenGui

-- Gradiente estético
local gradient = Instance.new("UIGradient")
gradient.Color = ColorSequence.new({
    ColorSequenceKeypoint.new(0, Color3.fromRGB(28, 28, 36)),
    ColorSequenceKeypoint.new(1, Color3.fromRGB(18, 18, 24))
})
gradient.Rotation = 45
gradient.Parent = mainFrame

-- Barra superior
local titleBar = Instance.new("Frame")
titleBar.Size = UDim2.new(1, 0, 0, 38)
titleBar.BackgroundColor3 = Color3.fromRGB(32, 32, 42)
titleBar.BorderSizePixel = 0
titleBar.Parent = mainFrame

local title = Instance.new("TextLabel")
title.Size = UDim2.new(0, 200, 1, 0)
title.Position = UDim2.new(0, 12, 0, 0)
title.BackgroundTransparency = 1
title.TextColor3 = Color3.fromRGB(255, 255, 255)
title.Text = "🔥 RAKOOF HUB"
title.Font = Enum.Font.GothamBold
title.TextSize = 14
title.TextXAlignment = Enum.TextXAlignment.Left
title.Parent = titleBar

-- Botão fechar
local closeButton = Instance.new("TextButton")
closeButton.Size = UDim2.new(0, 32, 0, 32)
closeButton.Position = UDim2.new(1, -38, 0, 3)
closeButton.BackgroundColor3 = Color3.fromRGB(220, 60, 60)
closeButton.TextColor3 = Color3.fromRGB(255, 255, 255)
closeButton.Text = "✕"
closeButton.Font = Enum.Font.GothamBold
closeButton.TextSize = 18
closeButton.BorderSizePixel = 0
closeButton.AutoButtonColor = true
closeButton.Parent = titleBar
closeButton.MouseButton1Click:Connect(function()
    screenGui:Destroy()
end)

-- Botão minimizar
local isMinimized = false
local minimizeButton = Instance.new("TextButton")
minimizeButton.Size = UDim2.new(0, 32, 0, 32)
minimizeButton.Position = UDim2.new(1, -75, 0, 3)
minimizeButton.BackgroundColor3 = Color3.fromRGB(100, 100, 120)
minimizeButton.TextColor3 = Color3.fromRGB(255, 255, 255)
minimizeButton.Text = "–"
minimizeButton.Font = Enum.Font.GothamBold
minimizeButton.TextSize = 24
minimizeButton.BorderSizePixel = 0
minimizeButton.AutoButtonColor = true
minimizeButton.Parent = titleBar

-- Botão flutuante (para reabrir)
local floatButton = Instance.new("TextButton")
floatButton.Size = UDim2.new(0, 50, 0, 50)
floatButton.AnchorPoint = Vector2.new(1, 1)
floatButton.Position = UDim2.new(1, -20, 1, -20)
floatButton.BackgroundColor3 = Color3.fromRGB(35, 35, 45)
floatButton.TextColor3 = Color3.fromRGB(255, 255, 255)
floatButton.Text = "⚡"
floatButton.Font = Enum.Font.GothamBold
floatButton.TextSize = 26
floatButton.BorderSizePixel = 0
floatButton.Visible = false
floatButton.Active = true
floatButton.Draggable = true
floatButton.Parent = screenGui

minimizeButton.MouseButton1Click:Connect(function()
    isMinimized = true
    mainFrame.Visible = false
    floatButton.Visible = true
end)

floatButton.MouseButton1Click:Connect(function()
    isMinimized = false
    mainFrame.Visible = true
    floatButton.Visible = false
end)

-- Container de abas (menu lateral)
local tabContainer = Instance.new("Frame")
tabContainer.Size = UDim2.new(0, 100, 1, -38)
tabContainer.Position = UDim2.new(0, 0, 0, 38)
tabContainer.BackgroundColor3 = Color3.fromRGB(24, 24, 30)
tabContainer.BorderSizePixel = 0
tabContainer.Parent = mainFrame

-- Container de conteúdo (área principal)
local contentContainer = Instance.new("Frame")
contentContainer.Size = UDim2.new(1, -100, 1, -38)
contentContainer.Position = UDim2.new(0, 100, 0, 38)
contentContainer.BackgroundColor3 = Color3.fromRGB(22, 22, 28)
contentContainer.BorderSizePixel = 0
contentContainer.Parent = mainFrame

-- ===========================================================================
-- SISTEMA DE ABAS
-- ===========================================================================
local tabs = {}
local function createTab(name, icon)
    local btn = Instance.new("TextButton")
    btn.Size = UDim2.new(1, 0, 0, 40)
    btn.BackgroundColor3 = Color3.fromRGB(24, 24, 30)
    btn.TextColor3 = Color3.fromRGB(180, 180, 200)
    btn.Text = icon .. "  " .. name
    btn.Font = Enum.Font.GothamSemibold
    btn.TextSize = 12
    btn.BorderSizePixel = 0
    btn.TextXAlignment = Enum.TextXAlignment.Left
    btn.Parent = tabContainer
    
    local frame = Instance.new("ScrollingFrame")
    frame.Size = UDim2.new(1, 0, 1, 0)
    frame.BackgroundTransparency = 1
    frame.BorderSizePixel = 0
    frame.ScrollBarThickness = 4
    frame.CanvasSize = UDim2.new(0, 0, 0, 0)
    frame.Visible = false
    frame.Parent = contentContainer
    
    btn.MouseButton1Click:Connect(function()
        for _, f in pairs(tabs) do
            f.frame.Visible = false
            f.btn.BackgroundColor3 = Color3.fromRGB(24, 24, 30)
            f.btn.TextColor3 = Color3.fromRGB(180, 180, 200)
        end
        frame.Visible = true
        btn.BackgroundColor3 = Color3.fromRGB(50, 50, 70)
        btn.TextColor3 = Color3.fromRGB(255, 255, 255)
    end)
    
    table.insert(tabs, {btn = btn, frame = frame})
    return frame
end

-- Criar abas
local homeTab = createTab("HOME", "🏠")
local farmTab = createTab("FARM", "⚔️")
local weaponTab = createTab("ARMAS", "🔪")
local combatTab = createTab("AÇÕES", "⚡")
local visualTab = createTab("ESP", "👁️")

-- Ativar primeira aba
tabs[1].btn.BackgroundColor3 = Color3.fromRGB(50, 50, 70)
tabs[1].btn.TextColor3 = Color3.fromRGB(255, 255, 255)
tabs[1].frame.Visible = true

-- ===========================================================================
-- FUNÇÕES AUXILIARES DO JOGO
-- ===========================================================================
local function getRakoof()
    for _, obj in pairs(workspace:GetDescendants()) do
        local name = obj.Name
        if name == "RakOOF" or name == "Rake" or name:lower():find("rakoof") then
            local hum = obj:FindFirstChildOfClass("Humanoid")
            if hum and hum.Health > 0 then
                return obj, hum
            end
        end
    end
    return nil, nil
end

local function getScratcher()
    for _, obj in pairs(workspace:GetDescendants()) do
        local name = obj.Name
        if name == "Scratcher" or name:lower():find("scratch") then
            local hum = obj:FindFirstChildOfClass("Humanoid")
            if hum and hum.Health > 0 then
                return obj, hum
            end
        end
    end
    return nil, nil
end

local function isRakoofTransforming()
    local rakoof, _ = getRakoof()
    if rakoof then
        local effect = rakoof:FindFirstChild("Transforming") or rakoof:FindFirstChild("Rage")
        if effect and effect:IsA("BoolValue") and effect.Value == true then
            return true
        end
    end
    return false
end

-- ===========================================================================
-- SCANNER DE ARMAS
-- ===========================================================================
local weaponStatsDB = {
    kunai = {50, 1.5}, katana = {55, 1.3}, sword = {40, 1.0}, espada = {40, 1.0},
    axe = {60, 0.8}, machado = {60, 0.8}, hammer = {65, 0.7}, martelo = {65, 0.7},
    cheese = {45, 0.9}, taser = {30, 1.2}, gun = {30, 2.0}, pistol = {30, 2.0},
    rifle = {70, 0.5}, shotgun = {80, 0.4}, bow = {40, 0.8}, arco = {40, 0.8},
    crossbow = {55, 0.6}, balestra = {55, 0.6}, dagger = {35, 2.0}, adaga = {35, 2.0},
    mace = {50, 0.7}, clava = {50, 0.7}, knife = {25, 2.0}, faca = {25, 2.0},
    spear = {45, 0.9}, lança = {45, 0.9}, scythe = {70, 0.6}, foice = {70, 0.6}
}

local nonWeapons = {"lantern", "flashlight", "vest", "night", "medkit", "bandage", "apple", "cola", "water", "food", "drink", "map", "compass", "watch", "radio", "phone", "camera", "binoculars"}

local function isValidWeapon(tool)
    if not tool:IsA("Tool") then return false end
    local name = tool.Name:lower()
    for _, nw in ipairs(nonWeapons) do
        if name:find(nw) then return false end
    end
    if tool:FindFirstChild("Damage") or tool:FindFirstChild("Swing") then return true end
    for keyword, _ in pairs(weaponStatsDB) do
        if name:find(keyword) then return true end
    end
    return false
end

local function getWeaponStats(tool)
    local dmg, spd = 20, 1.0
    local name = tool.Name:lower()
    for keyword, stats in pairs(weaponStatsDB) do
        if name:find(keyword) then dmg, spd = stats[1], stats[2]; break end
    end
    local dmgAttr = tool:FindFirstChild("Damage")
    if dmgAttr and tonumber(dmgAttr.Value) then dmg = tonumber(dmgAttr.Value) end
    return dmg, spd
end

local function scanBestWeapon()
    local best, bestDPS = nil, 0
    local weapons = {}
    
    local function scan(c)
        for _, i in ipairs(c:GetChildren()) do if isValidWeapon(i) then table.insert(weapons, i) end end
    end
    
    local bp = player:FindFirstChild("Backpack")
    if bp then scan(bp) end
    if player.Character then scan(player.Character) end
    
    if player.Character and player.Character:FindFirstChild("HumanoidRootPart") then
        local root = player.Character.HumanoidRootPart.Position
        for _, obj in ipairs(workspace:GetDescendants()) do
            if obj:IsA("Tool") and obj.Parent ~= player.Character and obj.Parent ~= bp then
                if (obj.Position - root).Magnitude < 150 and isValidWeapon(obj) then
                    table.insert(weapons, obj)
                end
            end
        end
    end
    
    for _, w in ipairs(weapons) do
        local dmg, spd = getWeaponStats(w)
        local dps = dmg * spd
        if dps > bestDPS then best, bestDPS = w, dps end
    end
    return best, bestDPS
end

local function equipBestWeapon()
    local weapon, dps = scanBestWeapon()
    if not weapon then return false end
    local char = player.Character
    if not char or not char:FindFirstChildOfClass("Humanoid") then return false end
    
    if weapon.Parent ~= char and weapon.Parent ~= player:FindFirstChild("Backpack") then
        firetouchinterest(char.HumanoidRootPart, weapon, 0)
        wait(0.2)
        firetouchinterest(char.HumanoidRootPart, weapon, 1)
        wait(0.3)
    end
    
    if weapon.Parent == player:FindFirstChild("Backpack") or weapon.Parent == char then
        char.Humanoid:EquipTool(weapon)
        starterGui:SetCore("SendNotification", {
            Title = "Arma Equipada",
            Text = string.format("%s (DPS: %.1f)", weapon.Name, dps),
            Duration = 3
        })
        return true
    end
    return false
end

-- ===========================================================================
-- ELEMENTOS DA UI (BOTÕES, TOGGLES)
-- ===========================================================================
local function addLabel(parent, text, y)
    local lbl = Instance.new("TextLabel")
    lbl.Size = UDim2.new(1, -10, 0, 22)
    lbl.Position = UDim2.new(0, 5, 0, y)
    lbl.BackgroundTransparency = 1
    lbl.TextColor3 = Color3.fromRGB(210, 210, 220)
    lbl.Text = text
    lbl.Font = Enum.Font.GothamSemibold
    lbl.TextSize = 12
    lbl.TextXAlignment = Enum.TextXAlignment.Left
    lbl.Parent = parent
    return lbl
end

local function addToggle(parent, text, y, callback)
    local btn = Instance.new("TextButton")
    btn.Size = UDim2.new(1, -10, 0, 32)
    btn.Position = UDim2.new(0, 5, 0, y)
    btn.BackgroundColor3 = Color3.fromRGB(45, 45, 55)
    btn.TextColor3 = Color3.fromRGB(255, 255, 255)
    btn.Text = "⚪ " .. text
    btn.Font = Enum.Font.GothamSemibold
    btn.TextSize = 12
    btn.BorderSizePixel = 0
    btn.AutoButtonColor = false
    btn.Parent = parent
    
    local enabled = false
    btn.MouseButton1Click:Connect(function()
        enabled = not enabled
        btn.Text = (enabled and "🔵" or "⚪") .. " " .. text
        btn.BackgroundColor3 = enabled and Color3.fromRGB(65, 65, 85) or Color3.fromRGB(45, 45, 55)
        callback(enabled)
    end)
    return btn
end

local function addButton(parent, text, y, callback)
    local btn = Instance.new("TextButton")
    btn.Size = UDim2.new(1, -10, 0, 32)
    btn.Position = UDim2.new(0, 5, 0, y)
    btn.BackgroundColor3 = Color3.fromRGB(55, 55, 75)
    btn.TextColor3 = Color3.fromRGB(255, 255, 255)
    btn.Text = text
    btn.Font = Enum.Font.GothamSemibold
    btn.TextSize = 12
    btn.BorderSizePixel = 0
    btn.AutoButtonColor = true
    btn.Parent = parent
    btn.MouseButton1Click:Connect(callback)
    return btn
end

local function addTextBox(parent, placeholder, y)
    local box = Instance.new("TextBox")
    box.Size = UDim2.new(1, -10, 0, 32)
    box.Position = UDim2.new(0, 5, 0, y)
    box.BackgroundColor3 = Color3.fromRGB(45, 45, 55)
    box.TextColor3 = Color3.fromRGB(255, 255, 255)
    box.PlaceholderText = placeholder
    box.Font = Enum.Font.Gotham
    box.TextSize = 12
    box.BorderSizePixel = 0
    box.Parent = parent
    return box
end

-- ===========================================================================
-- VARIÁVEIS DE CONTROLE
-- ===========================================================================
local autoFarmEnabled = false
local helpFriendEnabled = false
local godModeEnabled = false
local infiniteStaminaEnabled = false
local autoScanEnabled = false
local espEnabled = false
local pauseFarm = false
local targetFriendName = ""
local killThreshold = 5

-- ===========================================================================
-- PREENCHIMENTO DAS ABAS
-- ===========================================================================
local y = 8

-- ABA HOME
addLabel(homeTab, "🛡️ PROTEÇÃO", y); y = y + 22
addToggle(homeTab, "God Mode", y, function(v)
    godModeEnabled = v
    if v then
        spawn(function() while godModeEnabled do
            local c = player.Character
            if c and c:FindFirstChildOfClass("Humanoid") then c.Humanoid.Health = c.Humanoid.MaxHealth end
            wait(0.1)
        end end)
    end
end); y = y + 37
addToggle(homeTab, "Stamina Infinita", y, function(v)
    infiniteStaminaEnabled = v
    if v then
        spawn(function() while infiniteStaminaEnabled do
            local c = player.Character
            if c then
                local h = c:FindFirstChildOfClass("Humanoid")
                if h then
                    local s = h:FindFirstChild("Stamina")
                    if s and s:IsA("NumberValue") then s.Value = 100 end
                    h:SetStateEnabled(Enum.HumanoidStateType.Running, true)
                end
            end
            wait(0.1)
        end end)
    end
end); y = y + 37
addLabel(homeTab, "🔪 ARMAS", y); y = y + 22
addToggle(homeTab, "Auto Scan", y, function(v) autoScanEnabled = v end); y = y + 37
addButton(homeTab, "Equipar Melhor", y, equipBestWeapon); y = y + 42

-- ABA FARM
y = 8
addLabel(farmTab, "⚔️ AUTO FARM", y); y = y + 22
addToggle(farmTab, "Auto Farm Rakoof", y, function(v)
    autoFarmEnabled = v
    if v then
        spawn(function() while autoFarmEnabled do
            if not pauseFarm then
                if autoScanEnabled then equipBestWeapon() end
                local target = nil
                if helpFriendEnabled and targetFriendName ~= "" then
                    local tp = game.Players:FindFirstChild(targetFriendName)
                    if tp then
                        local ls = tp:FindFirstChild("leaderstats")
                        local kills = ls and ls:FindFirstChild("Kills")
                        if kills and kills.Value < killThreshold then
                            target = getScratcher()
                        end
                    end
                end
                target = target or getRakoof()
                if target then
                    pcall(function()
                        replicatedStorage:FindFirstChild("WeaponEvent"):FireServer("Swing", target)
                    end)
                end
            end
            wait(0.25)
        end end)
    end
end); y = y + 37
addLabel(farmTab, "🤝 AJUDAR AMIGO", y); y = y + 22
local friendBox = addTextBox(farmTab, "Nome do amigo", y); y = y + 37
local thresholdBox = addTextBox(farmTab, "Meta de kills (ex: 10)", y); thresholdBox.Text = "5"; y = y + 37
addToggle(farmTab, "Ativar Ajuda", y, function(v)
    helpFriendEnabled = v
    if v then targetFriendName = friendBox.Text; killThreshold = tonumber(thresholdBox.Text) or 5 end
end); y = y + 42

-- Monitor de transformação (pausa farm)
spawn(function() while true do
    if autoFarmEnabled then pauseFarm = isRakoofTransforming() end
    wait(0.5)
end end)

-- ABA ARMAS
y = 8
addButton(weaponTab, "🔎 Ver Melhor (Console)", y, function()
    local w, dps = scanBestWeapon()
    if w then print("🏆 Melhor: "..w.Name.." | DPS: "..string.format("%.1f", dps)) end
end); y = y + 37
addButton(weaponTab, "⚡ Equipar Melhor", y, equipBestWeapon); y = y + 42

-- ABA AÇÕES
y = 8
addButton(combatTab, "☠️ Matar Rakoof", y, function()
    local r, h = getRakoof()
    if r and h then h.Health = 0; starterGui:SetCore("SendNotification", {Title="Sucesso", Text="Rakoof eliminado!", Duration=3}) end
end); y = y + 37
addButton(combatTab, "🚀 Teleporte Seguro", y, function()
    local c = player.Character
    if c and c:FindFirstChild("HumanoidRootPart") then
        c.HumanoidRootPart.CFrame = CFrame.new(0, 30, 0)
        starterGui:SetCore("SendNotification", {Title="Teleporte", Text="Você foi teleportado.", Duration=3})
    end
end); y = y + 42

-- ABA VISUAL
y = 8
addToggle(visualTab, "ESP Rakoof (Vermelho)", y, function(v)
    espEnabled = v
    if v then
        spawn(function() while espEnabled do
            local r, _ = getRakoof()
            if r then
                local hl = Instance.new("Highlight")
                hl.Parent = r
                hl.FillColor = Color3.new(1,0,0)
                hl.OutlineColor = Color3.new(1,1,1)
                hl.FillTransparency = 0.3
                debris:AddItem(hl, 0.5)
            end
            wait(0.5)
        end end)
    end
end); y = y + 42

-- Ajuste do CanvasSize
for _, tab in ipairs(tabs) do
    tab.frame.CanvasSize = UDim2.new(0, 0, 0, y + 30)
end

-- ===========================================================================
-- NOTIFICAÇÃO FINAL
-- ===========================================================================
starterGui:SetCore("SendNotification", {
    Title = "Rakoof Hub Premium",
    Text = "Script carregado! Use '–' para minimizar.",
    Duration = 5,
    Icon = "rbxassetid://4483362458"
})
print("✅ Rakoof Hub carregado com sucesso!")
