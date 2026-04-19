--[[
    RAKOOF'S DEATH TEST - SCRIPT REFORÇADO
    - Diferencia itens de armas reais
    - Pega a melhor arma, até as que estão no chão
    - Sistema de farm com proteção contra falhas
--]]

-- 1. Interface (já testada e funcional)
local player = game.Players.LocalPlayer
local screenGui = Instance.new("ScreenGui")
screenGui.Name = "RakoofHubUI"
screenGui.Parent = player:WaitForChild("PlayerGui")

local mainFrame = Instance.new("Frame")
mainFrame.Size = UDim2.new(0, 300, 0, 450)
mainFrame.Position = UDim2.new(0.5, -150, 0.5, -225)
mainFrame.BackgroundColor3 = Color3.fromRGB(30, 30, 30)
mainFrame.BorderSizePixel = 0
mainFrame.Active = true
mainFrame.Draggable = true
mainFrame.Parent = screenGui

local title = Instance.new("TextLabel")
title.Size = UDim2.new(1, 0, 0, 30)
title.BackgroundColor3 = Color3.fromRGB(50, 50, 50)
title.TextColor3 = Color3.fromRGB(255, 255, 255)
title.Text = "⚡ RAKOOF HUB"
title.Font = Enum.Font.SourceSansBold
title.TextSize = 18
title.Parent = mainFrame

local toggleButton = Instance.new("TextButton")
toggleButton.Size = UDim2.new(0, 60, 0, 25)
toggleButton.Position = UDim2.new(1, -65, 0, 5)
toggleButton.BackgroundColor3 = Color3.fromRGB(200, 50, 50)
toggleButton.TextColor3 = Color3.fromRGB(255, 255, 255)
toggleButton.Text = "Ocultar"
toggleButton.Font = Enum.Font.SourceSansBold
toggleButton.TextSize = 14
toggleButton.Parent = mainFrame

local panelVisible = true
toggleButton.MouseButton1Click:Connect(function()
    panelVisible = not panelVisible
    mainFrame.Visible = panelVisible
    toggleButton.Text = panelVisible and "Ocultar" or "Mostrar"
end)

local contentFrame = Instance.new("ScrollingFrame")
contentFrame.Size = UDim2.new(1, 0, 1, -35)
contentFrame.Position = UDim2.new(0, 0, 0, 35)
contentFrame.BackgroundTransparency = 1
contentFrame.BorderSizePixel = 0
contentFrame.ScrollBarThickness = 8
contentFrame.CanvasSize = UDim2.new(0, 0, 0, 600)
contentFrame.Parent = mainFrame

-- 2. Funções Utilitárias
local function createLabel(text, y)
    local lbl = Instance.new("TextLabel")
    lbl.Size = UDim2.new(1, -10, 0, 20)
    lbl.Position = UDim2.new(0, 5, 0, y)
    lbl.BackgroundTransparency = 1
    lbl.TextColor3 = Color3.fromRGB(255, 255, 255)
    lbl.Text = text
    lbl.Font = Enum.Font.SourceSans
    lbl.TextSize = 14
    lbl.TextXAlignment = Enum.TextXAlignment.Left
    lbl.Parent = contentFrame
    return lbl
end

-- 3. Detecção inteligente de armas (agora diferenciando itens inúteis)
local function isWeapon(tool)
    if not tool:IsA("Tool") then return false end
    
    -- Lista de nomes de itens que NÃO são armas
    local nonWeapons = {"lantern", "flashlight", "vest", "night", "vision", "tool", "key", "battery", "medkit", "bandage", "apple", "cola", "water", "food", "drink", "map", "compass", "watch", "radio", "phone", "camera", "binoculars"}
    local name = tool.Name:lower()
    for _, nw in ipairs(nonWeapons) do
        if name:find(nw) then return false end
    end
    
    -- Lista de palavras que indicam ser uma arma
    local weapons = {"kunai", "sword", "espada", "hammer", "martelo", "axe", "machado", "gun", "pistol", "rifle", "shotgun", "taser", "cheese", "katana", "spear", "lança", "bow", "arco", "dagger", "adaga", "mace", "clava", "club", "porrete", "knife", "faca", "canhão", "cannon"}
    for _, w in ipairs(weapons) do
        if name:find(w) then return true end
    end
    
    -- Se tiver um script de ataque ou atributo de dano
    if tool:FindFirstChild("Damage") or tool:FindFirstChild("Attack") or tool:FindFirstChild("Swing") or tool:FindFirstChild("Fire") then
        return true
    end
    
    return false
end

local function getWeaponDamage(tool)
    local damage = 0
    local speed = 1
    
    local name = tool.Name:lower()
    if name:find("kunai") then damage = 50; speed = 1.5
    elseif name:find("sword") or name:find("espada") then damage = 40; speed = 1.0
    elseif name:find("hammer") or name:find("martelo") then damage = 60; speed = 0.7
    elseif name:find("axe") or name:find("machado") then damage = 55; speed = 0.8
    elseif name:find("taser") then damage = 30; speed = 1.2
    elseif name:find("cheese") then damage = 45; speed = 0.9
    elseif name:find("gun") or name:find("pistol") then damage = 30; speed = 2.0
    elseif name:find("rifle") then damage = 70; speed = 0.5
    elseif name:find("shotgun") then damage = 80; speed = 0.4
    elseif name:find("katana") then damage = 55; speed = 1.3
    elseif name:find("spear") or name:find("lança") then damage = 45; speed = 0.9
    elseif name:find("bow") or name:find("arco") then damage = 40; speed = 0.8
    elseif name:find("dagger") or name:find("adaga") then damage = 35; speed = 2.0
    elseif name:find("mace") or name:find("clava") then damage = 50; speed = 0.7
    elseif name:find("knife") or name:find("faca") then damage = 25; speed = 2.0
    else damage = 20 end
    
    return damage, speed
end

-- Scanner de melhor arma (inclui itens no chão)
local function scanBestWeapon()
    local bestWeapon = nil
    local bestDPS = 0
    local items = {}
    
    -- Mochila e personagem
    local backpack = player:FindFirstChild("Backpack")
    if backpack then
        for _, item in ipairs(backpack:GetChildren()) do
            if isWeapon(item) then table.insert(items, item) end
        end
    end
    if player.Character then
        for _, item in ipairs(player.Character:GetChildren()) do
            if isWeapon(item) then table.insert(items, item) end
        end
    end
    
    -- Itens no chão (próximos ao jogador)
    for _, v in ipairs(workspace:GetDescendants()) do
        if v:IsA("Tool") and isWeapon(v) and v.Parent ~= player.Character and v.Parent ~= backpack then
            local dist = (v.Position - player.Character.HumanoidRootPart.Position).Magnitude
            if dist < 50 then -- Alcance de 50 studs
                table.insert(items, v)
            end
        end
    end
    
    for _, tool in ipairs(items) do
        local damage, speed = getWeaponDamage(tool)
        local dps = damage * speed
        if dps > bestDPS then
            bestDPS = dps
            bestWeapon = tool
        end
    end
    
    return bestWeapon
end

-- Função para equipar a melhor arma (pega do chão se necessário)
local function equipBestWeapon()
    local weapon = scanBestWeapon()
    if not weapon then return false end
    
    local character = player.Character
    if not character or not character:FindFirstChildOfClass("Humanoid") then return false end
    
    -- Se a arma não estiver no personagem nem na mochila, tenta pegar do chão
    if weapon.Parent ~= character and weapon.Parent ~= player:FindFirstChild("Backpack") then
        firetouchinterest(player.Character.HumanoidRootPart, weapon, 0)
        wait(0.2)
        firetouchinterest(player.Character.HumanoidRootPart, weapon, 1)
    end
    
    if weapon.Parent == player:FindFirstChild("Backpack") or weapon.Parent == character then
        character.Humanoid:EquipTool(weapon)
        return true
    end
    return false
end

-- 4. Lógica de Farm Robusta
local Farming = false
local PauseFarming = false
local TargetFriendName = ""
local KillThreshold = 5
local statusLabel

local function GetBoss()
    for _, v in pairs(workspace:GetDescendants()) do
        if v.Name == "RakOOF" or v.Name == "Rake" or v.Name:lower():find("rakoof") then
            local hum = v:FindFirstChildOfClass("Humanoid")
            if hum and hum.Health > 0 then return v, hum end
        end
    end
    return nil, nil
end

local function GetScratcher()
    for _, v in pairs(workspace:GetDescendants()) do
        if v.Name == "Scratcher" or v.Name:lower():find("scratch") then
            local hum = v:FindFirstChildOfClass("Humanoid")
            if hum and hum.Health > 0 then return v, hum end
        end
    end
    return nil, nil
end

-- 5. Construção da UI
local yOffset = 10
createLabel("👤 Nome do Amigo:", yOffset)
yOffset = yOffset + 20

local friendBox = Instance.new("TextBox")
friendBox.Size = UDim2.new(1, -10, 0, 30)
friendBox.Position = UDim2.new(0, 5, 0, yOffset)
friendBox.BackgroundColor3 = Color3.fromRGB(50, 50, 50)
friendBox.TextColor3 = Color3.fromRGB(255, 255, 255)
friendBox.PlaceholderText = "Digite o nome exato"
friendBox.Text = ""
friendBox.Font = Enum.Font.SourceSans
friendBox.TextSize = 14
friendBox.Parent = contentFrame
yOffset = yOffset + 35

createLabel("🎯 Kills necessárias:", yOffset)
yOffset = yOffset + 20

local thresholdBox = Instance.new("TextBox")
thresholdBox.Size = UDim2.new(1, -10, 0, 30)
thresholdBox.Position = UDim2.new(0, 5, 0, yOffset)
thresholdBox.BackgroundColor3 = Color3.fromRGB(50, 50, 50)
thresholdBox.TextColor3 = Color3.fromRGB(255, 255, 255)
thresholdBox.PlaceholderText = "Ex: 10"
thresholdBox.Text = "5"
thresholdBox.Font = Enum.Font.SourceSans
thresholdBox.TextSize = 14
thresholdBox.Parent = contentFrame
yOffset = yOffset + 40

local scanButton = Instance.new("TextButton")
scanButton.Size = UDim2.new(1, -10, 0, 35)
scanButton.Position = UDim2.new(0, 5, 0, yOffset)
scanButton.BackgroundColor3 = Color3.fromRGB(0, 150, 200)
scanButton.TextColor3 = Color3.fromRGB(255, 255, 255)
scanButton.Text = "🔍 Escanear Melhor Arma"
scanButton.Font = Enum.Font.SourceSansBold
scanButton.TextSize = 14
scanButton.Parent = contentFrame
yOffset = yOffset + 40

local equipButton = Instance.new("TextButton")
equipButton.Size = UDim2.new(1, -10, 0, 35)
equipButton.Position = UDim2.new(0, 5, 0, yOffset)
equipButton.BackgroundColor3 = Color3.fromRGB(0, 200, 100)
equipButton.TextColor3 = Color3.fromRGB(255, 255, 255)
equipButton.Text = "⚔️ Equipar Melhor Arma"
equipButton.Font = Enum.Font.SourceSansBold
equipButton.TextSize = 14
equipButton.Parent = contentFrame
yOffset = yOffset + 45

statusLabel = Instance.new("TextLabel")
statusLabel.Size = UDim2.new(1, -10, 0, 20)
statusLabel.Position = UDim2.new(0, 5, 0, yOffset)
statusLabel.BackgroundTransparency = 1
statusLabel.TextColor3 = Color3.fromRGB(200, 200, 200)
statusLabel.Text = "Status: Parado"
statusLabel.Font = Enum.Font.SourceSans
statusLabel.TextSize = 13
statusLabel.TextXAlignment = Enum.TextXAlignment.Left
statusLabel.Parent = contentFrame
yOffset = yOffset + 25

local farmToggle = Instance.new("TextButton")
farmToggle.Size = UDim2.new(1, -10, 0, 40)
farmToggle.Position = UDim2.new(0, 5, 0, yOffset)
farmToggle.BackgroundColor3 = Color3.fromRGB(200, 50, 50)
farmToggle.TextColor3 = Color3.fromRGB(255, 255, 255)
farmToggle.Text = "▶ INICIAR FARM"
farmToggle.Font = Enum.Font.SourceSansBold
farmToggle.TextSize = 16
farmToggle.Parent = contentFrame
yOffset = yOffset + 50

contentFrame.CanvasSize = UDim2.new(0, 0, 0, yOffset + 10)

-- 6. Eventos
scanButton.MouseButton1Click:Connect(function()
    local weapon = scanBestWeapon()
    if weapon then
        local dmg, spd = getWeaponDamage(weapon)
        statusLabel.Text = string.format("Melhor arma: %s (Dano: %d, Vel: %.1f)", weapon.Name, dmg, spd)
    else
        statusLabel.Text = "Nenhuma arma encontrada."
    end
end)

equipButton.MouseButton1Click:Connect(function()
    if equipBestWeapon() then
        local weapon = scanBestWeapon()
        statusLabel.Text = "Equipada: " .. weapon.Name
    else
        statusLabel.Text = "Falha ao equipar."
    end
end)

farmToggle.MouseButton1Click:Connect(function()
    Farming = not Farming
    if Farming then
        TargetFriendName = friendBox.Text
        KillThreshold = tonumber(thresholdBox.Text) or 5
        
        farmToggle.Text = "⏸ PARAR FARM"
        farmToggle.BackgroundColor3 = Color3.fromRGB(50, 200, 50)
        statusLabel.Text = "Farm ATIVO."
        
        spawn(function()
            while Farming do
                pcall(function()
                    if PauseFarming then wait(0.5); return end
                    
                    equipBestWeapon()
                    
                    local shouldAttackBoss = false
                    if TargetFriendName ~= "" then
                        local targetPlayer = game.Players:FindFirstChild(TargetFriendName)
                        if targetPlayer then
                            local leaderstats = targetPlayer:FindFirstChild("leaderstats")
                            local kills = leaderstats and leaderstats:FindFirstChild("Kills")
                            if kills and kills.Value >= KillThreshold then
                                shouldAttackBoss = true
                            end
                        end
                    end
                    
                    if shouldAttackBoss then
                        local boss, hum = GetBoss()
                        if boss and hum then
                            local args = {[1] = "Swing", [2] = boss}
                            game:GetService("ReplicatedStorage"):FindFirstChild("WeaponEvent"):FireServer(unpack(args))
                            statusLabel.Text = "Atacando RAKOOF!"
                        end
                    else
                        local scratcher, humS = GetScratcher()
                        if scratcher and humS then
                            local args = {[1] = "Swing", [2] = scratcher}
                            game:GetService("ReplicatedStorage"):FindFirstChild("WeaponEvent"):FireServer(unpack(args))
                            statusLabel.Text = "Farmando Scratchers..."
                        end
                    end
                end)
                wait(0.3)
            end
        end)
    else
        farmToggle.Text = "▶ INICIAR FARM"
        farmToggle.BackgroundColor3 = Color3.fromRGB(200, 50, 50)
        statusLabel.Text = "Farm PARADO."
    end
end)

-- Monitor de Transformação
spawn(function()
    while true do
        if Farming then
            local boss, _ = GetBoss()
            if boss then
                local transformEffect = boss:FindFirstChild("Transforming") or boss:FindFirstChild("Rage")
                if transformEffect and transformEffect.Value == true then
                    if not PauseFarming then
                        PauseFarming = true
                        statusLabel.Text = "⚠️ TRANSFORMAÇÃO! Pausado."
                    end
                else
                    if PauseFarming then
                        PauseFarming = false
                        statusLabel.Text = "Retomando farm..."
                    end
                end
            end
        end
        wait(0.5)
    end
end)

-- Botão Flutuante
local floatButton = Instance.new("TextButton")
floatButton.Size = UDim2.new(0, 45, 0, 45)
floatButton.Position = UDim2.new(1, -55, 0.5, -22)
floatButton.BackgroundColor3 = Color3.fromRGB(30, 30, 30)
floatButton.TextColor3 = Color3.fromRGB(255, 255, 255)
floatButton.Text = "⚡"
floatButton.Font = Enum.Font.SourceSansBold
floatButton.TextSize = 22
floatButton.Parent = screenGui
floatButton.Active = true
floatButton.Draggable = true

floatButton.MouseButton1Click:Connect(function()
    panelVisible = not panelVisible
    mainFrame.Visible = panelVisible
    toggleButton.Text = panelVisible and "Ocultar" or "Mostrar"
end)

game:GetService("StarterGui"):SetCore("SendNotification", {
    Title = "Rakoof Hub",
    Text = "Interface carregada!",
    Duration = 5,
})

print("✅ Rakoof Hub reforçado carregado!")
