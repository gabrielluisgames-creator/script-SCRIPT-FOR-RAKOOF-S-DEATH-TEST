--[[
    RAKOOF'S DEATH TEST - HUB PREMIUM v9.0 (FINAL E COMPLETO)
    - Interface 100% ScreenGui nativa (sem Rayfield) para máxima compatibilidade.
    - Scanner de armas inteligente que analisa DPS (Dano por Segundo).
    - Pega a melhor arma do chão automaticamente.
    - Auto Farm, God Mode, Stamina Infinita, ESP e muito mais.
--]]

-- 1. CRIAÇÃO DA INTERFACE SCREENGUI (PAINEL MODERNO)
local player = game.Players.LocalPlayer
local screenGui = Instance.new("ScreenGui")
screenGui.Name = "RakoofHubPremium"
screenGui.ResetOnSpawn = false
screenGui.Parent = player:WaitForChild("PlayerGui")

-- Frame Principal (Painel Arrastável)
local mainFrame = Instance.new("Frame")
mainFrame.Size = UDim2.new(0, 550, 0, 380)
mainFrame.Position = UDim2.new(0.5, -275, 0.5, -190)
mainFrame.BackgroundColor3 = Color3.fromRGB(20, 20, 25)
mainFrame.BorderSizePixel = 0
mainFrame.BackgroundTransparency = 0.05
mainFrame.ClipsDescendants = true
mainFrame.Active = true
mainFrame.Draggable = true
mainFrame.Parent = screenGui

-- Barra Superior
local titleBar = Instance.new("Frame")
titleBar.Size = UDim2.new(1, 0, 0, 40)
titleBar.BackgroundColor3 = Color3.fromRGB(30, 30, 40)
titleBar.BorderSizePixel = 0
titleBar.Parent = mainFrame

local title = Instance.new("TextLabel")
title.Size = UDim2.new(0, 200, 1, 0)
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
closeButton.Size = UDim2.new(0, 30, 0, 30)
closeButton.Position = UDim2.new(1, -40, 0, 5)
closeButton.BackgroundColor3 = Color3.fromRGB(200, 50, 50)
closeButton.TextColor3 = Color3.fromRGB(255, 255, 255)
closeButton.Text = "✕"
closeButton.Font = Enum.Font.GothamBold
closeButton.TextSize = 18
closeButton.BorderSizePixel = 0
closeButton.Parent = titleBar

closeButton.MouseButton1Click:Connect(function()
    screenGui:Destroy()
end)

-- Botão Minimizar (esconde o painel e mostra botão flutuante)
local isMinimized = false
local minimizeButton = Instance.new("TextButton")
minimizeButton.Size = UDim2.new(0, 30, 0, 30)
minimizeButton.Position = UDim2.new(1, -75, 0, 5)
minimizeButton.BackgroundColor3 = Color3.fromRGB(100, 100, 120)
minimizeButton.TextColor3 = Color3.fromRGB(255, 255, 255)
minimizeButton.Text = "–"
minimizeButton.Font = Enum.Font.GothamBold
minimizeButton.TextSize = 22
minimizeButton.BorderSizePixel = 0
minimizeButton.Parent = titleBar

-- Botão Flutuante (para reabrir o painel)
local floatButton = Instance.new("TextButton")
floatButton.Size = UDim2.new(0, 50, 0, 50)
floatButton.Position = UDim2.new(1, -70, 0.8, 0)
floatButton.BackgroundColor3 = Color3.fromRGB(30, 30, 40)
floatButton.TextColor3 = Color3.fromRGB(255, 255, 255)
floatButton.Text = "⚡"
floatButton.Font = Enum.Font.GothamBold
floatButton.TextSize = 24
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
tabContainer.Size = UDim2.new(0, 120, 1, -40)
tabContainer.Position = UDim2.new(0, 0, 0, 40)
tabContainer.BackgroundColor3 = Color3.fromRGB(25, 25, 32)
tabContainer.BorderSizePixel = 0
tabContainer.Parent = mainFrame

-- Container de Conteúdo
local contentContainer = Instance.new("Frame")
contentContainer.Size = UDim2.new(1, -120, 1, -40)
contentContainer.Position = UDim2.new(0, 120, 0, 40)
contentContainer.BackgroundColor3 = Color3.fromRGB(20, 20, 25)
contentContainer.BorderSizePixel = 0
contentContainer.Parent = mainFrame

-- Função para criar abas
local tabs = {}
local function createTab(name, icon)
    local btn = Instance.new("TextButton")
    btn.Size = UDim2.new(1, 0, 0, 45)
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
    frame.ScrollBarThickness = 5
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

-- 2. CRIAÇÃO DAS ABAS
local mainTab = createTab("PRINCIPAL", "🏠")
local combatTab = createTab("COMBATE", "⚡")
local espTab = createTab("VISUAL", "👁️")
local configTab = createTab("CONFIG", "⚙️")

-- Ativar primeira aba
tabs[1].btn.BackgroundColor3 = Color3.fromRGB(50, 50, 70)
tabs[1].btn.TextColor3 = Color3.fromRGB(255, 255, 255)
tabs[1].frame.Visible = true

-- 3. FUNÇÕES AUXILIARES DO JOGO
-- Encontra o Rakoof (Chefe)
local function GetRakoof()
    for _, v in pairs(workspace:GetDescendants()) do
        if v.Name == "RakOOF" or v.Name == "Rake" or v.Name:lower():find("rakoof") then
            local hum = v:FindFirstChildOfClass("Humanoid")
            if hum and hum.Health > 0 then return v, hum end
        end
    end
    return nil, nil
end

-- SCANNER DE ARMAS INTELIGENTE (Analisa DPS e pega do chão)
local function GetBestWeapon()
    local bestWeapon = nil
    local bestDPS = 0
    local items = {}
    
    -- Função para escanear um container (Mochila ou Personagem)
    local function scanContainer(container)
        for _, item in ipairs(container:GetChildren()) do
            if item:IsA("Tool") and not item.Name:lower():find("lantern") then
                table.insert(items, item)
            end
        end
    end
    
    -- 1. Escaneia a Mochila
    local backpack = player:FindFirstChild("Backpack")
    if backpack then scanContainer(backpack) end
    
    -- 2. Escaneia o Personagem (arma já equipada)
    if player.Character then scanContainer(player.Character) end
    
    -- 3. Escaneia itens no CHÃO (próximos ao jogador)
    for _, v in ipairs(workspace:GetDescendants()) do
        if v:IsA("Tool") and not v.Name:lower():find("lantern") then
            -- Verifica se o item não está com o jogador
            if v.Parent ~= player.Character and v.Parent ~= backpack then
                if player.Character and player.Character:FindFirstChild("HumanoidRootPart") then
                    local dist = (v.Position - player.Character.HumanoidRootPart.Position).Magnitude
                    if dist < 100 then -- Alcance de 100 studs
                        table.insert(items, v)
                    end
                end
            end
        end
    end
    
    -- 4. Analisa cada arma e calcula o DPS (Dano * Velocidade)
    for _, tool in ipairs(items) do
        local damage = 20 -- Dano padrão
        local speed = 1   -- Velocidade padrão
        
        local name = tool.Name:lower()
        
        -- Lista de armas conhecidas com seus respectivos danos e velocidades
        if name:find("kunai") then
            damage = 50
            speed = 1.5
        elseif name:find("katana") then
            damage = 55
            speed = 1.3
        elseif name:find("sword") or name:find("espada") then
            damage = 40
            speed = 1.0
        elseif name:find("axe") or name:find("machado") then
            damage = 60
            speed = 0.8
        elseif name:find("hammer") or name:find("martelo") then
            damage = 65
            speed = 0.7
        elseif name:find("cheese") then
            damage = 45
            speed = 0.9
        elseif name:find("taser") then
            damage = 30
            speed = 1.2
        elseif name:find("gun") or name:find("pistol") then
            damage = 30
            speed = 2.0
        end
        
        -- Verifica se a ferramenta tem um atributo de dano real (usado por alguns jogos)
        local dmgAttr = tool:FindFirstChild("Damage")
        if dmgAttr and tonumber(dmgAttr.Value) then
            damage = tonumber(dmgAttr.Value)
        end
        
        local dps = damage * speed
        
        if dps > bestDPS then
            bestDPS = dps
            bestWeapon = tool
        end
    end
    
    return bestWeapon
end

-- Função para equipar a melhor arma (pega do chão se necessário)
local function EquipBestWeapon()
    local weapon = GetBestWeapon()
    if not weapon then return false end
    
    local character = player.Character
    if not character or not character:FindFirstChildOfClass("Humanoid") then return false end
    
    -- Se a arma estiver no chão (não está na mochila nem equipada), tenta pegá-la
    if weapon.Parent ~= character and weapon.Parent ~= player:FindFirstChild("Backpack") then
        -- Simula toque para pegar o item do chão
        firetouchinterest(player.Character.HumanoidRootPart, weapon, 0)
        wait(0.2)
        firetouchinterest(player.Character.HumanoidRootPart, weapon, 1)
    end
    
    -- Aguarda um pouco para o item chegar na mochila
    wait(0.3)
    
    -- Verifica novamente se a arma está na mochila ou personagem
    if weapon.Parent == player:FindFirstChild("Backpack") or weapon.Parent == character then
        character.Humanoid:EquipTool(weapon)
        return true
    end
    return false
end

-- 4. CRIAÇÃO DOS ELEMENTOS DA UI (Botões, Toggles, etc.)
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

-- 5. PREENCHENDO AS ABAS COM FUNCIONALIDADES
local yOffset = 10

-- ============ ABA PRINCIPAL ============
addLabel(mainTab, "⚔️ AUTO FARM", yOffset)
yOffset = yOffset + 25

local autoFarmEnabled = false
addToggle(mainTab, "Auto Farm Rakoof", yOffset, function(val)
    autoFarmEnabled = val
    if val then
        spawn(function()
            while autoFarmEnabled do
                -- Equipa a melhor arma disponível
                EquipBestWeapon()
                
                local rakoof, hum = GetRakoof()
                if rakoof and hum then
                    local args = {[1] = "Swing", [2] = rakoof}
                    pcall(function()
                        game:GetService("ReplicatedStorage"):FindFirstChild("WeaponEvent"):FireServer(unpack(args))
                    end)
                end
                wait(0.3)
            end
        end)
    end
end)
yOffset = yOffset + 40

addLabel(mainTab, "🛡️ PROTEÇÃO", yOffset)
yOffset = yOffset + 25

addToggle(mainTab, "God Mode (Não toma dano)", yOffset, function(val)
    if val then
        spawn(function()
            while val do
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
    if val then
        spawn(function()
            while val do
                if player.Character then
                    local hum = player.Character:FindFirstChildOfClass("Humanoid")
                    if hum then
                        -- Métodos comuns de stamina em jogos Roblox
                        if hum:FindFirstChild("Stamina") then
                            hum.Stamina = hum.StaminaMax or 100
                        end
                        local staminaAttr = player.Character:GetAttribute("Stamina")
                        if staminaAttr then
                            player.Character:SetAttribute("Stamina", player.Character:GetAttribute("MaxStamina") or 100)
                        end
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

addButton(mainTab, "🔍 Escanear Melhor Arma", yOffset, function()
    local best = GetBestWeapon()
    if best then
        print("Melhor arma encontrada: " .. best.Name)
    else
        print("Nenhuma arma encontrada.")
    end
end)
yOffset = yOffset + 40

addButton(mainTab, "⚔️ Equipar Melhor Arma", yOffset, function()
    if EquipBestWeapon() then
        print("Arma equipada com sucesso!")
    else
        print("Falha ao equipar arma.")
    end
end)
yOffset = yOffset + 45

-- ============ ABA COMBATE ============
yOffset = 10
addLabel(combatTab, "💀 AÇÕES RÁPIDAS", yOffset)
yOffset = yOffset + 25

addButton(combatTab, "☠️ Matar Rakoof Instantaneamente", yOffset, function()
    local rakoof, hum = GetRakoof()
    if rakoof and hum then
        hum.Health = 0
    end
end)
yOffset = yOffset + 40

addLabel(combatTab, "🌍 TELEPORTE", yOffset)
yOffset = yOffset + 25

addButton(combatTab, "🚀 Teleportar para Local Seguro", yOffset, function()
    if player.Character and player.Character:FindFirstChild("HumanoidRootPart") then
        -- Tenta encontrar um local seguro (ajuste as coordenadas se souber um ponto melhor)
        local safeSpot = workspace:FindFirstChild("SafeZone") or CFrame.new(0, 20, 0)
        player.Character.HumanoidRootPart.CFrame = safeSpot
    end
end)
yOffset = yOffset + 45

-- ============ ABA VISUAL (ESP) ============
yOffset = 10
addLabel(espTab, "👁️ DESTAQUES", yOffset)
yOffset = yOffset + 25

addToggle(espTab, "ESP do Rakoof (Vermelho)", yOffset, function(val)
    if val then
        spawn(function()
            while val do
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
    -- Recarrega o script (se você quiser, pode colar o código novamente, mas é mais fácil reexecutar)
end)

-- Ajusta o CanvasSize de todas as abas
for _, tab in ipairs(tabs) do
    tab.frame.CanvasSize = UDim2.new(0, 0, 0, yOffset + 50)
end

-- Notificação de carregamento
game:GetService("StarterGui"):SetCore("SendNotification", {
    Title = "Rakoof Hub Premium",
    Text = "Script carregado! Pressione 'Ocultar' para esconder o painel.",
    Duration = 5,
})

print("✅ Rakoof Hub Premium carregado com sucesso!")
