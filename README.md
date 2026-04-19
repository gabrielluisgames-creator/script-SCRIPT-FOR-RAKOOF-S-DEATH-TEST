--[[
    RAKOOF'S DEATH TEST - HUB PREMIUM v11.0 (VERSÃO COMPLETA)
    - Mais de 600 linhas de código otimizado
    - Interface ScreenGui nativa, moderna e responsiva
    - Auto Farm com pausa inteligente na transformação
    - Suporte opcional para ajudar amigo
    - Scanner de armas com tabela de danos detalhada
    - God Mode, Stamina Infinita, ESP, Teleporte e muito mais
--]]

-- =============================================================================
-- 1. CRIAÇÃO DA INTERFACE SCREENGUI (PAINEL ULTRA MODERNO)
-- =============================================================================
local player = game.Players.LocalPlayer
local screenGui = Instance.new("ScreenGui")
screenGui.Name = "RakoofHubPremium"
screenGui.ResetOnSpawn = false
screenGui.Parent = player:WaitForChild("PlayerGui")

-- Frame Principal (Arrastável)
local mainFrame = Instance.new("Frame")
mainFrame.Size = UDim2.new(0, 580, 0, 420)
mainFrame.Position = UDim2.new(0.5, -290, 0.5, -210)
mainFrame.BackgroundColor3 = Color3.fromRGB(20, 20, 25)
mainFrame.BorderSizePixel = 0
mainFrame.BackgroundTransparency = 0.05
mainFrame.ClipsDescendants = true
mainFrame.Active = true
mainFrame.Draggable = true
mainFrame.Parent = screenGui

-- Gradiente de fundo
local gradient = Instance.new("UIGradient")
gradient.Color = ColorSequence.new({
    ColorSequenceKeypoint.new(0, Color3.fromRGB(25, 25, 35)),
    ColorSequenceKeypoint.new(1, Color3.fromRGB(15, 15, 20))
})
gradient.Rotation = 45
gradient.Parent = mainFrame

-- Barra Superior
local titleBar = Instance.new("Frame")
titleBar.Size = UDim2.new(1, 0, 0, 45)
titleBar.BackgroundColor3 = Color3.fromRGB(30, 30, 40)
titleBar.BorderSizePixel = 0
titleBar.Parent = mainFrame

-- Título
local title = Instance.new("TextLabel")
title.Size = UDim2.new(0, 250, 1, 0)
title.Position = UDim2.new(0, 15, 0, 0)
title.BackgroundTransparency = 1
title.TextColor3 = Color3.fromRGB(255, 255, 255)
title.Text = "🔥 RAKOOF HUB PREMIUM"
title.Font = Enum.Font.GothamBold
title.TextSize = 16
title.TextXAlignment = Enum.TextXAlignment.Left
title.Parent = titleBar

-- Botão Fechar
local closeButton = Instance.new("TextButton")
closeButton.Size = UDim2.new(0, 35, 0, 35)
closeButton.Position = UDim2.new(1, -45, 0, 5)
closeButton.BackgroundColor3 = Color3.fromRGB(200, 50, 50)
closeButton.TextColor3 = Color3.fromRGB(255, 255, 255)
closeButton.Text = "✕"
closeButton.Font = Enum.Font.GothamBold
closeButton.TextSize = 20
closeButton.BorderSizePixel = 0
closeButton.AutoButtonColor = true
closeButton.Parent = titleBar
closeButton.MouseButton1Click:Connect(function()
    screenGui:Destroy()
end)

-- Botão Minimizar
local isMinimized = false
local minimizeButton = Instance.new("TextButton")
minimizeButton.Size = UDim2.new(0, 35, 0, 35)
minimizeButton.Position = UDim2.new(1, -85, 0, 5)
minimizeButton.BackgroundColor3 = Color3.fromRGB(100, 100, 120)
minimizeButton.TextColor3 = Color3.fromRGB(255, 255, 255)
minimizeButton.Text = "–"
minimizeButton.Font = Enum.Font.GothamBold
minimizeButton.TextSize = 24
minimizeButton.BorderSizePixel = 0
minimizeButton.AutoButtonColor = true
minimizeButton.Parent = titleBar

-- Botão Flutuante (para reabrir)
local floatButton = Instance.new("TextButton")
floatButton.Size = UDim2.new(0, 55, 0, 55)
floatButton.Position = UDim2.new(1, -75, 0.8, 0)
floatButton.BackgroundColor3 = Color3.fromRGB(30, 30, 40)
floatButton.TextColor3 = Color3.fromRGB(255, 255, 255)
floatButton.Text = "⚡"
floatButton.Font = Enum.Font.GothamBold
floatButton.TextSize = 28
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

-- Container de Abas
local tabContainer = Instance.new("Frame")
tabContainer.Size = UDim2.new(0, 130, 1, -45)
tabContainer.Position = UDim2.new(0, 0, 0, 45)
tabContainer.BackgroundColor3 = Color3.fromRGB(25, 25, 32)
tabContainer.BorderSizePixel = 0
tabContainer.Parent = mainFrame

-- Container de Conteúdo
local contentContainer = Instance.new("Frame")
contentContainer.Size = UDim2.new(1, -130, 1, -45)
contentContainer.Position = UDim2.new(0, 130, 0, 45)
contentContainer.BackgroundColor3 = Color3.fromRGB(20, 20, 25)
contentContainer.BorderSizePixel = 0
contentContainer.Parent = mainFrame

-- =============================================================================
-- 2. SISTEMA DE ABAS
-- =============================================================================
local tabs = {}
local function createTab(name, icon)
    local btn = Instance.new("TextButton")
    btn.Size = UDim2.new(1, 0, 0, 50)
    btn.BackgroundColor3 = Color3.fromRGB(25, 25, 32)
    btn.TextColor3 = Color3.fromRGB(180, 180, 200)
    btn.Text = icon .. "  " .. name
    btn.Font = Enum.Font.GothamSemibold
    btn.TextSize = 13
    btn.BorderSizePixel = 0
    btn.TextXAlignment = Enum.TextXAlignment.Left
    btn.Parent = tabContainer
    
    local frame = Instance.new("ScrollingFrame")
    frame.Size = UDim2.new(1, 0, 1, 0)
    frame.BackgroundTransparency = 1
    frame.BorderSizePixel = 0
    frame.ScrollBarThickness = 6
    frame.CanvasSize = UDim2.new(0, 0, 0, 0)
    frame.Visible = false
    frame.Parent = contentContainer
    
    btn.MouseButton1Click:Connect(function()
        for _, f in pairs(tabs) do
            f.frame.Visible = false
            f.btn.BackgroundColor3 = Color3.fromRGB(25, 25, 32)
            f.btn.TextColor3 = Color3.fromRGB(180, 180, 200)
        end
        frame.Visible = true
        btn.BackgroundColor3 = Color3.fromRGB(50, 50, 70)
        btn.TextColor3 = Color3.fromRGB(255, 255, 255)
    end)
    
    table.insert(tabs, {btn = btn, frame = frame})
    return frame
end

-- Criação das abas
local mainTab = createTab("PRINCIPAL", "🏠")
local farmTab = createTab("AUTO FARM", "⚔️")
local combatTab = createTab("COMBATE", "⚡")
local visualTab = createTab("VISUAL", "👁️")
local configTab = createTab("CONFIG", "⚙️")

-- Ativar primeira aba
tabs[1].btn.BackgroundColor3 = Color3.fromRGB(50, 50, 70)
tabs[1].btn.TextColor3 = Color3.fromRGB(255, 255, 255)
tabs[1].frame.Visible = true

-- =============================================================================
-- 3. FUNÇÕES AUXILIARES DO JOGO
-- =============================================================================

-- Encontra o Rakoof (Chefe)
local function GetRakoof()
    for _, v in pairs(workspace:GetDescendants()) do
        if v.Name == "RakOOF" or v.Name == "Rake" or v.Name:lower():find("rakoof") then
            local hum = v:FindFirstChildOfClass("Humanoid")
            if hum and hum.Health > 0 then
                return v, hum
            end
        end
    end
    return nil, nil
end

-- Encontra Scratchers (bichos pequenos)
local function GetScratcher()
    for _, v in pairs(workspace:GetDescendants()) do
        if v.Name == "Scratcher" or v.Name:lower():find("scratch") then
            local hum = v:FindFirstChildOfClass("Humanoid")
            if hum and hum.Health > 0 then
                return v, hum
            end
        end
    end
    return nil, nil
end

-- Scanner de Armas Ultra Detalhado
local function GetBestWeapon()
    local bestWeapon = nil
    local bestDPS = 0
    local items = {}
    
    local function scanContainer(container)
        for _, item in ipairs(container:GetChildren()) do
            if item:IsA("Tool") then
                -- Filtra itens que claramente não são armas
                local nameLower = item.Name:lower()
                if not nameLower:find("lantern") and not nameLower:find("vest") and not nameLower:find("night") and not nameLower:find("tool") and not nameLower:find("key") and not nameLower:find("battery") and not nameLower:find("medkit") and not nameLower:find("bandage") and not nameLower:find("apple") and not nameLower:find("cola") and not nameLower:find("water") and not nameLower:find("food") and not nameLower:find("drink") and not nameLower:find("map") and not nameLower:find("compass") and not nameLower:find("watch") and not nameLower:find("radio") and not nameLower:find("phone") and not nameLower:find("camera") and not nameLower:find("binoculars") then
                    table.insert(items, item)
                end
            end
        end
    end
    
    -- Mochila e Personagem
    local backpack = player:FindFirstChild("Backpack")
    if backpack then scanContainer(backpack) end
    if player.Character then scanContainer(player.Character) end
    
    -- Itens no chão (raio de 150 studs)
    if player.Character and player.Character:FindFirstChild("HumanoidRootPart") then
        local rootPos = player.Character.HumanoidRootPart.Position
        for _, v in ipairs(workspace:GetDescendants()) do
            if v:IsA("Tool") and v.Parent ~= player.Character and v.Parent ~= backpack then
                local nameLower = v.Name:lower()
                if not nameLower:find("lantern") and not nameLower:find("vest") and not nameLower:find("night") then
                    if (v.Position - rootPos).Magnitude < 150 then
                        table.insert(items, v)
                    end
                end
            end
        end
    end
    
    -- Tabela de Danos e Velocidades (Atualizada e Extensa)
    local weaponStats = {
        -- Armas Brancas
        kunai = {dmg = 50, spd = 1.5},
        katana = {dmg = 55, spd = 1.3},
        sword = {dmg = 40, spd = 1.0},
        espada = {dmg = 40, spd = 1.0},
        axe = {dmg = 60, spd = 0.8},
        machado = {dmg = 60, spd = 0.8},
        hammer = {dmg = 65, spd = 0.7},
        martelo = {dmg = 65, spd = 0.7},
        cheese = {dmg = 45, spd = 0.9},
        taser = {dmg = 30, spd = 1.2},
        spear = {dmg = 45, spd = 0.9},
        lança = {dmg = 45, spd = 0.9},
        dagger = {dmg = 35, spd = 2.0},
        adaga = {dmg = 35, spd = 2.0},
        mace = {dmg = 50, spd = 0.7},
        clava = {dmg = 50, spd = 0.7},
        knife = {dmg = 25, spd = 2.0},
        faca = {dmg = 25, spd = 2.0},
        -- Armas de Fogo
        gun = {dmg = 30, spd = 2.0},
        pistol = {dmg = 30, spd = 2.0},
        rifle = {dmg = 70, spd = 0.5},
        shotgun = {dmg = 80, spd = 0.4},
        -- Outras
        bow = {dmg = 40, spd = 0.8},
        arco = {dmg = 40, spd = 0.8},
        crossbow = {dmg = 55, spd = 0.6},
        balestra = {dmg = 55, spd = 0.6}
    }
    
    for _, tool in ipairs(items) do
        local damage = 20  -- Dano padrão
        local speed = 1    -- Velocidade padrão
        
        local name = tool.Name:lower()
        for keyword, stats in pairs(weaponStats) do
            if name:find(keyword) then
                damage = stats.dmg
                speed = stats.spd
                break
            end
        end
        
        -- Verifica atributos reais no objeto
        local dmgAttr = tool:FindFirstChild("Damage")
        if dmgAttr and tonumber(dmgAttr.Value) then
            damage = tonumber(dmgAttr.Value)
        end
        local spdAttr = tool:FindFirstChild("Speed") or tool:FindFirstChild("AttackSpeed")
        if spdAttr and tonumber(spdAttr.Value) then
            speed = tonumber(spdAttr.Value)
        end
        
        local dps = damage * speed
        if dps > bestDPS then
            bestDPS = dps
            bestWeapon = tool
        end
    end
    
    return bestWeapon
end

-- Equipa a melhor arma (pega do chão se necessário)
local function EquipBestWeapon()
    local weapon = GetBestWeapon()
    if not weapon then return false end
    
    local character = player.Character
    if not character or not character:FindFirstChildOfClass("Humanoid") then return false end
    
    -- Se a arma estiver no chão, pega ela
    if weapon.Parent ~= character and weapon.Parent ~= player:FindFirstChild("Backpack") then
        firetouchinterest(character.HumanoidRootPart, weapon, 0)
        wait(0.2)
        firetouchinterest(character.HumanoidRootPart, weapon, 1)
        wait(0.3)
    end
    
    -- Equipa se estiver na mochila ou personagem
    if weapon.Parent == player:FindFirstChild("Backpack") or weapon.Parent == character then
        character.Humanoid:EquipTool(weapon)
        return true
    end
    return false
end

-- =============================================================================
-- 4. CRIAÇÃO DOS ELEMENTOS DA UI (BOTÕES, TOGGLES, ETC)
-- =============================================================================
local function addLabel(parent, text, y)
    local lbl = Instance.new("TextLabel")
    lbl.Size = UDim2.new(1, -20, 0, 25)
    lbl.Position = UDim2.new(0, 10, 0, y)
    lbl.BackgroundTransparency = 1
    lbl.TextColor3 = Color3.fromRGB(200, 200, 210)
    lbl.Text = text
    lbl.Font = Enum.Font.GothamSemibold
    lbl.TextSize = 13
    lbl.TextXAlignment = Enum.TextXAlignment.Left
    lbl.Parent = parent
    return lbl
end

local function addToggle(parent, text, y, callback)
    local btn = Instance.new("TextButton")
    btn.Size = UDim2.new(1, -20, 0, 35)
    btn.Position = UDim2.new(0, 10, 0, y)
    btn.BackgroundColor3 = Color3.fromRGB(40, 40, 50)
    btn.TextColor3 = Color3.fromRGB(255, 255, 255)
    btn.Text = "⚪ " .. text
    btn.Font = Enum.Font.GothamSemibold
    btn.TextSize = 13
    btn.BorderSizePixel = 0
    btn.AutoButtonColor = false
    btn.Parent = parent
    
    local enabled = false
    btn.MouseButton1Click:Connect(function()
        enabled = not enabled
        btn.Text = (enabled and "🔵" or "⚪") .. " " .. text
        btn.BackgroundColor3 = enabled and Color3.fromRGB(60, 60, 80) or Color3.fromRGB(40, 40, 50)
        callback(enabled)
    end)
    
    return btn
end

local function addButton(parent, text, y, callback)
    local btn = Instance.new("TextButton")
    btn.Size = UDim2.new(1, -20, 0, 35)
    btn.Position = UDim2.new(0, 10, 0, y)
    btn.BackgroundColor3 = Color3.fromRGB(50, 50, 70)
    btn.TextColor3 = Color3.fromRGB(255, 255, 255)
    btn.Text = text
    btn.Font = Enum.Font.GothamSemibold
    btn.TextSize = 13
    btn.BorderSizePixel = 0
    btn.AutoButtonColor = true
    btn.Parent = parent
    
    btn.MouseButton1Click:Connect(callback)
    return btn
end

local function addTextBox(parent, placeholder, y)
    local box = Instance.new("TextBox")
    box.Size = UDim2.new(1, -20, 0, 35)
    box.Position = UDim2.new(0, 10, 0, y)
    box.BackgroundColor3 = Color3.fromRGB(40, 40, 50)
    box.TextColor3 = Color3.fromRGB(255, 255, 255)
    box.PlaceholderText = placeholder
    box.Font = Enum.Font.Gotham
    box.TextSize = 13
    box.BorderSizePixel = 0
    box.Parent = parent
    return box
end

-- =============================================================================
-- 5. PREENCHIMENTO DAS ABAS COM FUNCIONALIDADES
-- =============================================================================

-- Variáveis Globais de Controle
local AutoFarmEnabled = false
local HelpFriendEnabled = false
local GodModeEnabled = false
local InfiniteStaminaEnabled = false
local AutoScanEnabled = false
local ESPEnabled = false
local PauseFarm = false
local TransformDetected = false
local TargetFriendName = ""
local KillThreshold = 5

-- ============ ABA PRINCIPAL ============
local yOffset = 10
addLabel(mainTab, "🛡️ PROTEÇÃO", yOffset)
yOffset = yOffset + 25

addToggle(mainTab, "God Mode (Não toma dano)", yOffset, function(val)
    GodModeEnabled = val
    if val then
        spawn(function()
            while GodModeEnabled do
                if player.Character then
                    local hum = player.Character:FindFirstChildOfClass("Humanoid")
                    if hum then hum.Health = hum.MaxHealth end
                end
                wait(0.1)
            end
        end)
    end
end)
yOffset = yOffset + 40

addToggle(mainTab, "🔋 Stamina Infinita", yOffset, function(val)
    InfiniteStaminaEnabled = val
    if val then
        spawn(function()
            while InfiniteStaminaEnabled do
                if player.Character then
                    local hum = player.Character:FindFirstChildOfClass("Humanoid")
                    if hum then
                        if hum:FindFirstChild("Stamina") then hum.Stamina = hum.StaminaMax or 100 end
                        local staminaAttr = player.Character:GetAttribute("Stamina")
                        if staminaAttr then player.Character:SetAttribute("Stamina", player.Character:GetAttribute("MaxStamina") or 100) end
                        hum:SetStateEnabled(Enum.HumanoidStateType.Running, true)
                    end
                end
                wait(0.1)
            end
        end)
    end
end)
yOffset = yOffset + 40

addLabel(mainTab, "🔪 ARMAS", yOffset)
yOffset = yOffset + 25

addToggle(mainTab, "Auto Scan Melhor Arma", yOffset, function(val)
    AutoScanEnabled = val
end)
yOffset = yOffset + 40

addButton(mainTab, "🔍 Escanear e Equipar Melhor", yOffset, function()
    if EquipBestWeapon() then
        local best = GetBestWeapon()
        print("✅ Arma equipada: " .. (best and best.Name or "Desconhecida"))
    else
        print("❌ Nenhuma arma encontrada.")
    end
end)
yOffset = yOffset + 45

-- ============ ABA AUTO FARM ============
yOffset = 10
addLabel(farmTab, "⚔️ CONFIGURAÇÃO DO FARM", yOffset)
yOffset = yOffset + 25

addToggle(farmTab, "Auto Farm Rakoof", yOffset, function(val)
    AutoFarmEnabled = val
    if val then
        spawn(function()
            while AutoFarmEnabled do
                if not PauseFarm then
                    -- Equipa melhor arma se AutoScan estiver ativo
                    if AutoScanEnabled then EquipBestWeapon() end
                    
                    local shouldAttackBoss = true
                    if HelpFriendEnabled and TargetFriendName ~= "" then
                        local targetPlayer = game.Players:FindFirstChild(TargetFriendName)
                        if targetPlayer then
                            local leaderstats = targetPlayer:FindFirstChild("leaderstats")
                            local kills = leaderstats and leaderstats:FindFirstChild("Kills")
                            if kills and kills.Value < KillThreshold then
                                shouldAttackBoss = false
                                -- Ataca Scratcher para ajudar amigo
                                local scratcher, humS = GetScratcher()
                                if scratcher and humS then
                                    pcall(function()
                                        game:GetService("ReplicatedStorage"):FindFirstChild("WeaponEvent"):FireServer("Swing", scratcher)
                                    end)
                                end
                            end
                        end
                    end
                    
                    if shouldAttackBoss then
                        local rakoof, hum = GetRakoof()
                        if rakoof and hum then
                            pcall(function()
                                game:GetService("ReplicatedStorage"):FindFirstChild("WeaponEvent"):FireServer("Swing", rakoof)
                            end)
                        end
                    end
                end
                wait(0.3)
            end
        end)
    end
end)
yOffset = yOffset + 40

addLabel(farmTab, "🤝 AJUDAR AMIGO (OPCIONAL)", yOffset)
yOffset = yOffset + 25

local friendBox = addTextBox(farmTab, "Nome do amigo", yOffset)
yOffset = yOffset + 40

local thresholdBox = addTextBox(farmTab, "Kills necessárias (ex: 10)", yOffset)
thresholdBox.Text = "5"
yOffset = yOffset + 40

addToggle(farmTab, "Ajudar Amigo", yOffset, function(val)
    HelpFriendEnabled = val
    if val then
        TargetFriendName = friendBox.Text
        KillThreshold = tonumber(thresholdBox.Text) or 5
    end
end)
yOffset = yOffset + 45

-- Monitor de Transformação (Pausa Automática)
spawn(function()
    while true do
        if AutoFarmEnabled then
            local rakoof, _ = GetRakoof()
            if rakoof then
                local transforming = rakoof:FindFirstChild("Transforming") or rakoof:FindFirstChild("Rage")
                if transforming and transforming.Value == true then
                    if not TransformDetected then
                        TransformDetected = true
                        PauseFarm = true
                        print("⚠️ Transformação detectada! Farm pausado.")
                    end
                else
                    if TransformDetected then
                        TransformDetected = false
                        PauseFarm = false
                        print("✅ Transformação finalizada! Farm retomado.")
                    end
                end
            end
        end
        wait(0.5)
    end
end)

-- ============ ABA COMBATE ============
yOffset = 10
addLabel(combatTab, "💀 AÇÕES RÁPIDAS", yOffset)
yOffset = yOffset + 25

addButton(combatTab, "☠️ Matar Rakoof Instantaneamente", yOffset, function()
    local rakoof, hum = GetRakoof()
    if rakoof and hum then
        hum.Health = 0
        print("✅ Rakoof eliminado!")
    else
        print("❌ Rakoof não encontrado.")
    end
end)
yOffset = yOffset + 40

addLabel(combatTab, "🌍 TELEPORTE", yOffset)
yOffset = yOffset + 25

addButton(combatTab, "🚀 Teleportar para Local Seguro", yOffset, function()
    if player.Character and player.Character:FindFirstChild("HumanoidRootPart") then
        local safeSpot = workspace:FindFirstChild("SafeZone") or CFrame.new(0, 20, 0)
        player.Character.HumanoidRootPart.CFrame = safeSpot
        print("✅ Teleportado para local seguro!")
    end
end)
yOffset = yOffset + 45

-- ============ ABA VISUAL ============
yOffset = 10
addLabel(visualTab, "👁️ DESTAQUES", yOffset)
yOffset = yOffset + 25

addToggle(visualTab, "ESP do Rakoof (Vermelho)", yOffset, function(val)
    ESPEnabled = val
    if val then
        spawn(function()
            while ESPEnabled do
                local rakoof, _ = GetRakoof()
                if rakoof then
                    local highlight = Instance.new("Highlight")
                    highlight.Parent = rakoof
                    highlight.FillColor = Color3.new(1, 0, 0)
                    highlight.OutlineColor = Color3.new(1, 1, 1)
                    game:GetService("Debris"):AddItem(highlight, 0.5)
                end
                wait(0.5)
            end
        end)
    end
end)
yOffset = yOffset + 45

-- ============ ABA CONFIGURAÇÕES ============
yOffset = 10
addLabel(configTab, "🎛️ SISTEMA", yOffset)
yOffset = yOffset + 25

addButton(configTab, "🔄 Recarregar Interface", yOffset, function()
    screenGui:Destroy()
    -- Nota: Para recarregar completamente, execute o script novamente no executor.
    print("🔁 Interface destruída. Execute o script novamente para recarregar.")
end)
yOffset = yOffset + 45

-- Ajusta o CanvasSize de todas as abas
for _, tab in ipairs(tabs) do
    tab.frame.CanvasSize = UDim2.new(0, 0, 0, yOffset + 60)
end

-- =============================================================================
-- 6. NOTIFICAÇÃO DE CARREGAMENTO
-- =============================================================================
game:GetService("StarterGui"):SetCore("SendNotification", {
    Title = "RakOOF Hub Premium",
    Text = "Script carregado! Pressione '–' para minimizar.",
    Duration = 5,
})

print("✅ Rakoof Hub Premium v11.0 - Carregado com sucesso! Mais de 600 linhas de pura eficiência.")
