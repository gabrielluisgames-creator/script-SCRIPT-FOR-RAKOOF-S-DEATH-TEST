--[[
    RAKOOF'S DEATH TEST - HUB ULTRA COMPLETO v13.0
    -----------------------------------------------------------
    🎯 FUNCIONALIDADES INCLUÍDAS:
        - Auto Farm Rakoof com pausa automática durante transformação
        - Sistema opcional de ajuda a amigo (mata Scratchers até meta)
        - God Mode (vida infinita)
        - Stamina Infinita (3 métodos diferentes)
        - Scanner de Armas Avançado (tabela com +30 armas)
        - Auto Equip da melhor arma (incluindo pegar do chão)
        - ESP do Rakoof (highlight vermelho)
        - Matar Rakoof instantaneamente
        - Teleporte para local seguro
        - Painel 100% ScreenGui nativo, arrastável, com minimizar
        - Botão flutuante móvel para celular
        - Interface adaptada para qualquer resolução
    -----------------------------------------------------------
    📱 OTIMIZADO PARA DELTA EXECUTOR MOBILE
    ⚠️  Script extenso para máxima personalização.
--]]

-- ===========================================================================
-- SEÇÃO 1: CONFIGURAÇÃO INICIAL E SERVIÇOS
-- ===========================================================================
local player = game.Players.LocalPlayer
local runService = game:GetService("RunService")
local replicatedStorage = game:GetService("ReplicatedStorage")
local userInputService = game:GetService("UserInputService")
local debris = game:GetService("Debris")
local starterGui = game:GetService("StarterGui")

-- Aguarda o personagem carregar (importante para mobile)
repeat wait() until player.Character and player.Character:FindFirstChild("HumanoidRootPart")

-- ===========================================================================
-- SEÇÃO 2: CRIAÇÃO DA INTERFACE SCREENGUI (OTIMIZADA PARA CELULAR)
-- ===========================================================================
local screenGui = Instance.new("ScreenGui")
screenGui.Name = "RakoofHubUltra"
screenGui.ResetOnSpawn = false
screenGui.Parent = player:WaitForChild("PlayerGui")

-- Função para obter dimensões seguras da tela (evita cortes em notches)
local function getSafeScreenSize()
    local camera = workspace.CurrentCamera
    if camera then
        return camera.ViewportSize.X, camera.ViewportSize.Y
    end
    return 1080, 1920  -- fallback
end

local screenWidth, screenHeight = getSafeScreenSize()

-- Painel Principal (tamanho otimizado para mobile)
local mainFrame = Instance.new("Frame")
mainFrame.Size = UDim2.new(0, 320, 0, 420)
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

-- Barra Superior
local titleBar = Instance.new("Frame")
titleBar.Size = UDim2.new(1, 0, 0, 38)
titleBar.BackgroundColor3 = Color3.fromRGB(32, 32, 42)
titleBar.BorderSizePixel = 0
titleBar.Parent = mainFrame

-- Título
local title = Instance.new("TextLabel")
title.Size = UDim2.new(0, 200, 1, 0)
title.Position = UDim2.new(0, 12, 0, 0)
title.BackgroundTransparency = 1
title.TextColor3 = Color3.fromRGB(255, 255, 255)
title.Text = "🔥 RAKOOF HUB ULTRA"
title.Font = Enum.Font.GothamBold
title.TextSize = 14
title.TextXAlignment = Enum.TextXAlignment.Left
title.Parent = titleBar

-- Botão Fechar (destrói a UI)
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

-- Botão Minimizar (esconde painel e mostra botão flutuante)
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

-- Botão Flutuante (para reabrir o painel)
local floatButton = Instance.new("TextButton")
floatButton.Size = UDim2.new(0, 55, 0, 55)
floatButton.AnchorPoint = Vector2.new(1, 1)
floatButton.Position = UDim2.new(1, -20, 1, -20)  -- canto inferior direito com margem
floatButton.BackgroundColor3 = Color3.fromRGB(35, 35, 45)
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

-- Container de Abas (menu lateral)
local tabContainer = Instance.new("Frame")
tabContainer.Size = UDim2.new(0, 105, 1, -38)
tabContainer.Position = UDim2.new(0, 0, 0, 38)
tabContainer.BackgroundColor3 = Color3.fromRGB(24, 24, 30)
tabContainer.BorderSizePixel = 0
tabContainer.Parent = mainFrame

-- Container de Conteúdo (área principal)
local contentContainer = Instance.new("Frame")
contentContainer.Size = UDim2.new(1, -105, 1, -38)
contentContainer.Position = UDim2.new(0, 105, 0, 38)
contentContainer.BackgroundColor3 = Color3.fromRGB(22, 22, 28)
contentContainer.BorderSizePixel = 0
contentContainer.Parent = mainFrame

-- ===========================================================================
-- SEÇÃO 3: SISTEMA DE ABAS
-- ===========================================================================
local tabs = {}
local function createTab(name, icon)
    local btn = Instance.new("TextButton")
    btn.Size = UDim2.new(1, 0, 0, 42)
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
    frame.ScrollBarThickness = 5
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

-- Criação das 5 abas
local homeTab = createTab("HOME", "🏠")
local farmTab = createTab("FARM", "⚔️")
local weaponTab = createTab("ARMAS", "🔪")
local combatTab = createTab("AÇÕES", "⚡")
local visualTab = createTab("ESP", "👁️")

-- Ativar primeira aba por padrão
tabs[1].btn.BackgroundColor3 = Color3.fromRGB(50, 50, 70)
tabs[1].btn.TextColor3 = Color3.fromRGB(255, 255, 255)
tabs[1].frame.Visible = true

-- ===========================================================================
-- SEÇÃO 4: FUNÇÕES AUXILIARES DO JOGO (DETECÇÃO E UTILITÁRIOS)
-- ===========================================================================

-- 4.1 Encontra o Rakoof (chefe principal)
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

-- 4.2 Encontra Scratchers (inimigos comuns)
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

-- 4.3 Verifica se o Rakoof está se transformando
local function isRakoofTransforming()
    local rakoof, _ = getRakoof()
    if rakoof then
        local effect = rakoof:FindFirstChild("Transforming") or rakoof:FindFirstChild("Rage") or rakoof:FindFirstChild("Enrage")
        if effect and effect:IsA("BoolValue") and effect.Value == true then
            return true
        end
    end
    return false
end

-- ===========================================================================
-- SEÇÃO 5: SCANNER DE ARMAS ULTRA COMPLETO (TABELA COM +40 ARMAS)
-- ===========================================================================

-- Tabela de estatísticas de armas conhecidas (dano, velocidade)
local weaponDatabase = {
    -- Armas brancas comuns
    kunai = {damage = 50, speed = 1.5},
    katana = {damage = 55, speed = 1.3},
    sword = {damage = 40, speed = 1.0},
    espada = {damage = 40, speed = 1.0},
    axe = {damage = 60, speed = 0.8},
    machado = {damage = 60, speed = 0.8},
    hammer = {damage = 65, speed = 0.7},
    martelo = {damage = 65, speed = 0.7},
    cheese = {damage = 45, speed = 0.9},
    taser = {damage = 30, speed = 1.2},
    spear = {damage = 45, speed = 0.9},
    lança = {damage = 45, speed = 0.9},
    dagger = {damage = 35, speed = 2.0},
    adaga = {damage = 35, speed = 2.0},
    mace = {damage = 50, speed = 0.7},
    clava = {damage = 50, speed = 0.7},
    knife = {damage = 25, speed = 2.0},
    faca = {damage = 25, speed = 2.0},
    -- Armas especiais
    scythe = {damage = 70, speed = 0.6},
    foice = {damage = 70, speed = 0.6},
    greatsword = {damage = 80, speed = 0.5},
    montante = {damage = 80, speed = 0.5},
    -- Armas de fogo
    gun = {damage = 30, speed = 2.0},
    pistol = {damage = 30, speed = 2.0},
    revolver = {damage = 45, speed = 1.5},
    rifle = {damage = 70, speed = 0.5},
    shotgun = {damage = 80, speed = 0.4},
    -- Arcos e bestas
    bow = {damage = 40, speed = 0.8},
    arco = {damage = 40, speed = 0.8},
    crossbow = {damage = 55, speed = 0.6},
    balestra = {damage = 55, speed = 0.6},
    -- Arremessáveis
    shuriken = {damage = 35, speed = 2.5},
    throwingknife = {damage = 30, speed = 2.5},
    -- Outras
    torch = {damage = 15, speed = 1.0},
    stick = {damage = 10, speed = 1.5},
    branch = {damage = 8, speed = 1.8},
    pipe = {damage = 25, speed = 1.2},
    wrench = {damage = 20, speed = 1.4},
    crowbar = {damage = 30, speed = 1.1}
}

-- Lista de itens que NÃO são armas (ignorar no scanner)
local nonWeapons = {
    "lantern", "flashlight", "vest", "night", "vision", "tool", "key",
    "battery", "medkit", "bandage", "apple", "cola", "water", "food",
    "drink", "map", "compass", "watch", "radio", "phone", "camera",
    "binoculars", "backpack", "armor", "helmet", "shield", "escudo"
}

-- Função que verifica se um objeto é uma arma válida
local function isValidWeapon(tool)
    if not tool:IsA("Tool") then return false end
    local nameLower = tool.Name:lower()
    for _, nw in ipairs(nonWeapons) do
        if nameLower:find(nw) then return false end
    end
    -- Se tiver um script de ataque ou atributo Damage, considera arma
    if tool:FindFirstChild("Damage") or tool:FindFirstChild("Swing") or tool:FindFirstChild("Fire") then
        return true
    end
    -- Verifica na tabela de armas conhecidas
    for keyword, _ in pairs(weaponDatabase) do
        if nameLower:find(keyword) then return true end
    end
    return false
end

-- Função que retorna o dano e velocidade de uma arma
local function getWeaponStats(tool)
    local damage = 20   -- valor padrão
    local speed = 1.0
    local nameLower = tool.Name:lower()
    
    -- Procura na base de dados
    for keyword, stats in pairs(weaponDatabase) do
        if nameLower:find(keyword) then
            damage = stats.damage
            speed = stats.speed
            break
        end
    end
    
    -- Sobrescreve com atributos reais se existirem
    local dmgAttr = tool:FindFirstChild("Damage")
    if dmgAttr then
        if dmgAttr:IsA("NumberValue") or dmgAttr:IsA("IntValue") then
            damage = dmgAttr.Value
        elseif tonumber(dmgAttr.Value) then
            damage = tonumber(dmgAttr.Value)
        end
    end
    
    local spdAttr = tool:FindFirstChild("Speed") or tool:FindFirstChild("AttackSpeed")
    if spdAttr then
        if spdAttr:IsA("NumberValue") or spdAttr:IsA("IntValue") then
            speed = spdAttr.Value
        elseif tonumber(spdAttr.Value) then
            speed = tonumber(spdAttr.Value)
        end
    end
    
    return damage, speed
end

-- Scanner principal: retorna a melhor arma baseada em DPS (dano * velocidade)
local function scanBestWeapon()
    local bestWeapon = nil
    local bestDPS = 0
    local weaponsList = {}
    
    -- Função auxiliar para escanear um container (mochila ou personagem)
    local function scanContainer(container)
        for _, item in ipairs(container:GetChildren()) do
            if isValidWeapon(item) then
                table.insert(weaponsList, item)
            end
        end
    end
    
    -- Escaneia mochila
    local backpack = player:FindFirstChild("Backpack")
    if backpack then scanContainer(backpack) end
    
    -- Escaneia personagem (arma equipada)
    if player.Character then scanContainer(player.Character) end
    
    -- Escaneia itens no chão (raio de 150 studs)
    if player.Character and player.Character:FindFirstChild("HumanoidRootPart") then
        local rootPos = player.Character.HumanoidRootPart.Position
        for _, obj in ipairs(workspace:GetDescendants()) do
            if obj:IsA("Tool") and obj.Parent ~= player.Character and obj.Parent ~= backpack then
                if (obj.Position - rootPos).Magnitude < 150 then
                    if isValidWeapon(obj) then
                        table.insert(weaponsList, obj)
                    end
                end
            end
        end
    end
    
    -- Calcula DPS de cada arma
    for _, weapon in ipairs(weaponsList) do
        local dmg, spd = getWeaponStats(weapon)
        local dps = dmg * spd
        if dps > bestDPS then
            bestDPS = dps
            bestWeapon = weapon
        end
    end
    
    return bestWeapon, bestDPS
end

-- Função para equipar a melhor arma (pega do chão automaticamente se necessário)
local function equipBestWeapon()
    local weapon, dps = scanBestWeapon()
    if not weapon then
        starterGui:SetCore("SendNotification", {Title = "Scanner", Text = "Nenhuma arma encontrada.", Duration = 3})
        return false
    end
    
    local character = player.Character
    if not character or not character:FindFirstChildOfClass("Humanoid") then return false end
    
    -- Se a arma estiver no chão, tenta pegá-la
    if weapon.Parent ~= character and weapon.Parent ~= player:FindFirstChild("Backpack") then
        -- Simula toque para coletar o item
        firetouchinterest(character.HumanoidRootPart, weapon, 0)
        wait(0.2)
        firetouchinterest(character.HumanoidRootPart, weapon, 1)
        wait(0.3)
    end
    
    -- Verifica se agora está na mochila ou personagem
    if weapon.Parent == player:FindFirstChild("Backpack") or weapon.Parent == character then
        character.Humanoid:EquipTool(weapon)
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
-- SEÇÃO 6: CRIAÇÃO DOS ELEMENTOS DA UI (BOTÕES, TOGGLES, ETC.)
-- ===========================================================================
local function addLabel(parent, text, yPos)
    local lbl = Instance.new("TextLabel")
    lbl.Size = UDim2.new(1, -10, 0, 22)
    lbl.Position = UDim2.new(0, 5, 0, yPos)
    lbl.BackgroundTransparency = 1
    lbl.TextColor3 = Color3.fromRGB(210, 210, 220)
    lbl.Text = text
    lbl.Font = Enum.Font.GothamSemibold
    lbl.TextSize = 12
    lbl.TextXAlignment = Enum.TextXAlignment.Left
    lbl.Parent = parent
    return lbl
end

local function addToggle(parent, text, yPos, callback)
    local btn = Instance.new("TextButton")
    btn.Size = UDim2.new(1, -10, 0, 32)
    btn.Position = UDim2.new(0, 5, 0, yPos)
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

local function addButton(parent, text, yPos, callback)
    local btn = Instance.new("TextButton")
    btn.Size = UDim2.new(1, -10, 0, 32)
    btn.Position = UDim2.new(0, 5, 0, yPos)
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

local function addTextBox(parent, placeholder, yPos)
    local box = Instance.new("TextBox")
    box.Size = UDim2.new(1, -10, 0, 32)
    box.Position = UDim2.new(0, 5, 0, yPos)
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
-- SEÇÃO 7: VARIÁVEIS DE ESTADO GLOBAL
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
-- SEÇÃO 8: PREENCHIMENTO DAS ABAS COM FUNCIONALIDADES
-- ===========================================================================
local yPos = 8

-- 8.1 ABA HOME (Proteção e Habilidades Básicas)
addLabel(homeTab, "🛡️ PROTEÇÃO", yPos)
yPos = yPos + 22
addToggle(homeTab, "God Mode (Imortal)", yPos, function(val)
    godModeEnabled = val
    if val then
        spawn(function()
            while godModeEnabled do
                local char = player.Character
                if char then
                    local hum = char:FindFirstChildOfClass("Humanoid")
                    if hum then hum.Health = hum.MaxHealth end
                end
                wait(0.1)
            end
        end)
    end
end)
yPos = yPos + 37
addToggle(homeTab, "Stamina Infinita", yPos, function(val)
    infiniteStaminaEnabled = val
    if val then
        spawn(function()
            while infiniteStaminaEnabled do
                local char = player.Character
                if char then
                    local hum = char:FindFirstChildOfClass("Humanoid")
                    if hum then
                        -- Método 1: propriedade Stamina
                        local stamina = hum:FindFirstChild("Stamina")
                        if stamina and stamina:IsA("NumberValue") then
                            stamina.Value = stamina:FindFirstChild("MaxStamina") and stamina.MaxStamina.Value or 100
                        end
                        -- Método 2: atributo personalizado
                        local staminaAttr = char:GetAttribute("Stamina")
                        if staminaAttr then
                            char:SetAttribute("Stamina", char:GetAttribute("MaxStamina") or 100)
                        end
                        -- Garante que possa correr
                        hum:SetStateEnabled(Enum.HumanoidStateType.Running, true)
                    end
                end
                wait(0.1)
            end
        end)
    end
end)
yPos = yPos + 37
addLabel(homeTab, "🔪 SCANNER DE ARMAS", yPos)
yPos = yPos + 22
addToggle(homeTab, "Auto Scan (equipa melhor)", yPos, function(val)
    autoScanEnabled = val
end)
yPos = yPos + 37
addButton(homeTab, "🔍 Escanear e Equipar Agora", yPos, function()
    equipBestWeapon()
end)
yPos = yPos + 42

-- 8.2 ABA FARM (Auto Farm e Ajuda a Amigo)
yPos = 8
addLabel(farmTab, "⚔️ AUTO FARM", yPos)
yPos = yPos + 22
addToggle(farmTab, "Auto Farm Rakoof", yPos, function(val)
    autoFarmEnabled = val
    if val then
        spawn(function()
            while autoFarmEnabled do
                if not pauseFarm then
                    -- Equipa melhor arma se opção ativa
                    if autoScanEnabled then equipBestWeapon() end
                    
                    local shouldAttackBoss = true
                    
                    -- Lógica de ajuda a amigo
                    if helpFriendEnabled and targetFriendName ~= "" then
                        local targetPlayer = game.Players:FindFirstChild(targetFriendName)
                        if targetPlayer then
                            local leaderstats = targetPlayer:FindFirstChild("leaderstats")
                            local kills = leaderstats and leaderstats:FindFirstChild("Kills")
                            if kills and kills.Value < killThreshold then
                                shouldAttackBoss = false
                                -- Ataca Scratchers para dar kill ao amigo
                                local scratcher, _ = getScratcher()
                                if scratcher then
                                    pcall(function()
                                        replicatedStorage:FindFirstChild("WeaponEvent"):FireServer("Swing", scratcher)
                                    end)
                                end
                            end
                        end
                    end
                    
                    -- Ataca o Rakoof se for o caso
                    if shouldAttackBoss then
                        local rakoof, _ = getRakoof()
                        if rakoof then
                            pcall(function()
                                replicatedStorage:FindFirstChild("WeaponEvent"):FireServer("Swing", rakoof)
                            end)
                        end
                    end
                end
                wait(0.25)
            end
        end)
    end
end)
yPos = yPos + 37
addLabel(farmTab, "🤝 AJUDAR AMIGO (opcional)", yPos)
yPos = yPos + 22
local friendBox = addTextBox(farmTab, "Nome exato do amigo", yPos)
yPos = yPos + 37
local thresholdBox = addTextBox(farmTab, "Meta de kills (ex: 10)", yPos)
thresholdBox.Text = "5"
yPos = yPos + 37
addToggle(farmTab, "Ativar Ajuda a Amigo", yPos, function(val)
    helpFriendEnabled = val
    if val then
        targetFriendName = friendBox.Text
        killThreshold = tonumber(thresholdBox.Text) or 5
    end
end)
yPos = yPos + 42

-- Monitor de Transformação (pausa o farm durante rage)
spawn(function()
    while true do
        if autoFarmEnabled then
            pauseFarm = isRakoofTransforming()
        end
        wait(0.5)
    end
end)

-- 8.3 ABA ARMAS (Scanner e equipamento manual)
yPos = 8
addLabel(weaponTab, "📊 INFORMAÇÕES DA ARMA", yPos)
yPos = yPos + 22
addButton(weaponTab, "🔎 Ver Melhor Arma (Console)", yPos, function()
    local weapon, dps = scanBestWeapon()
    if weapon then
        print(string.format("🏆 Melhor arma: %s | DPS: %.1f", weapon.Name, dps))
        starterGui:SetCore("SendNotification", {Title = "Scanner", Text = "Ver console (F9) para detalhes.", Duration = 4})
    else
        print("❌ Nenhuma arma encontrada.")
    end
end)
yPos = yPos + 37
addButton(weaponTab, "⚡ Equipar Melhor Arma", yPos, function()
    equipBestWeapon()
end)
yPos = yPos + 37
addLabel(weaponTab, "🗡️ ARMAS DISPONÍVEIS", yPos)
yPos = yPos + 22
addButton(weaponTab, "📋 Listar Armas Próximas", yPos, function()
    local count = 0
    local text = ""
    for _, obj in ipairs(workspace:GetDescendants()) do
        if obj:IsA("Tool") and isValidWeapon(obj) then
            local dist = "?"
            if player.Character and player.Character:FindFirstChild("HumanoidRootPart") then
                dist = string.format("%.0f", (obj.Position - player.Character.HumanoidRootPart.Position).Magnitude)
            end
            text = text .. string.format("%s (dist: %s)\n", obj.Name, dist)
            count = count + 1
        end
    end
    print("Armas encontradas: " .. count)
    print(text)
    starterGui:SetCore("SendNotification", {Title = "Scanner", Text = count .. " armas listadas no console.", Duration = 4})
end)
yPos = yPos + 42

-- 8.4 ABA AÇÕES (Combate e Utilidades)
yPos = 8
addLabel(combatTab, "💀 AÇÕES RÁPIDAS", yPos)
yPos = yPos + 22
addButton(combatTab, "☠️ Matar Rakoof Agora", yPos, function()
    local rakoof, hum = getRakoof()
    if rakoof and hum then
        hum.Health = 0
        starterGui:SetCore("SendNotification", {Title = "Sucesso", Text = "Rakoof eliminado!", Duration = 3})
    else
        starterGui:SetCore("SendNotification", {Title = "Erro", Text = "Rakoof não encontrado.", Duration = 3})
    end
end)
yPos = yPos + 37
addLabel(combatTab, "🌍 TELEPORTE", yPos)
yPos = yPos + 22
addButton(combatTab, "🚀 Ir para Local Seguro", yPos, function()
    local char = player.Character
    if char and char:FindFirstChild("HumanoidRootPart") then
        -- Tenta encontrar um local seguro (padrão: coordenada alta no mapa)
        local safeCFrame = workspace:FindFirstChild("SafeZone") and workspace.SafeZone.CFrame or CFrame.new(0, 30, 0)
        char.HumanoidRootPart.CFrame = safeCFrame
        starterGui:SetCore("SendNotification", {Title = "Teleporte", Text = "Você foi teleportado.", Duration = 3})
    end
end)
yPos = yPos + 42

-- 8.5 ABA VISUAL (ESP e Destaques)
yPos = 8
addLabel(visualTab, "👁️ DESTAQUES", yPos)
yPos = yPos + 22
addToggle(visualTab, "ESP do Rakoof (Vermelho)", yPos, function(val)
    espEnabled = val
    if val then
        spawn(function()
            while espEnabled do
                local rakoof, _ = getRakoof()
                if rakoof then
                    local highlight = Instance.new("Highlight")
                    highlight.Parent = rakoof
                    highlight.FillColor = Color3.new(1, 0, 0)
                    highlight.OutlineColor = Color3.new(1, 1, 1)
                    highlight.FillTransparency = 0.3
                    debris:AddItem(highlight, 0.5)
                end
                wait(0.5)
            end
        end)
    end
end)
yPos = yPos + 42

-- Ajusta o CanvasSize de todas as abas para acomodar o conteúdo
for _, tab in ipairs(tabs) do
    tab.frame.CanvasSize = UDim2.new(0, 0, 0, yPos + 30)
end

-- ===========================================================================
-- SEÇÃO 9: NOTIFICAÇÃO DE CARREGAMENTO E FINALIZAÇÃO
-- ===========================================================================
starterGui:SetCore("SendNotification", {
    Title = "🔥 RAKOOF HUB ULTRA",
    Text = "Script carregado! Use o botão '–' para minimizar.",
    Duration = 6,
    Icon = "rbxassetid://4483362458"
})

print("========================================")
print("   RAKOOF HUB ULTRA v13.0 CARREGADO")
print("   Mais de 800 linhas de puro poder!")
print("========================================")
