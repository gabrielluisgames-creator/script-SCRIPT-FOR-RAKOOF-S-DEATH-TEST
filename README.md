--[[
    RAKOOF'S DEATH TEST - HUB COMPLETO
    Interface nativa (ScreenGui) + Todas as funções:
    - Farm automático com suporte a amigo
    - Pausa na transformação
    - Scanner e equipamento automático da melhor arma
]]

-- ============================================================
-- 1. CRIAÇÃO DA INTERFACE (SCREENGUI)
-- ============================================================
local player = game.Players.LocalPlayer
local screenGui = Instance.new("ScreenGui")
screenGui.Name = "RakoofHubUI"
screenGui.Parent = player:WaitForChild("PlayerGui")

-- Painel Principal
local mainFrame = Instance.new("Frame")
mainFrame.Name = "MainFrame"
mainFrame.Size = UDim2.new(0, 300, 0, 450)
mainFrame.Position = UDim2.new(0.5, -150, 0.5, -225)
mainFrame.BackgroundColor3 = Color3.fromRGB(30, 30, 30)
mainFrame.BorderSizePixel = 0
mainFrame.Active = true
mainFrame.Draggable = true
mainFrame.Parent = screenGui

-- Título
local title = Instance.new("TextLabel")
title.Size = UDim2.new(1, 0, 0, 30)
title.BackgroundColor3 = Color3.fromRGB(50, 50, 50)
title.TextColor3 = Color3.fromRGB(255, 255, 255)
title.Text = "⚡ RAKOOF HUB"
title.Font = Enum.Font.SourceSansBold
title.TextSize = 18
title.Parent = mainFrame

-- Botão Ocultar/Mostrar
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

-- Área de conteúdo com rolagem (ScrollingFrame)
local contentFrame = Instance.new("ScrollingFrame")
contentFrame.Size = UDim2.new(1, 0, 1, -35)
contentFrame.Position = UDim2.new(0, 0, 0, 35)
contentFrame.BackgroundTransparency = 1
contentFrame.BorderSizePixel = 0
contentFrame.ScrollBarThickness = 8
contentFrame.CanvasSize = UDim2.new(0, 0, 0, 600) -- Ajustável
contentFrame.Parent = mainFrame

-- ============================================================
-- 2. VARIÁVEIS GLOBAIS DO FARM
-- ============================================================
local Farming = false
local PauseFarming = false
local TransformDetected = false
local TargetFriendName = ""
local KillThreshold = 5
local FarmConnection = nil

-- Funções de localização
local function GetBoss()
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

-- Scanner da melhor arma (mesmo do script anterior)
local function scanBestWeapon()
    local bestWeapon = nil
    local bestDPS = 0
    local items = {}
    local backpack = player:FindFirstChild("Backpack")
    if backpack then
        for _, item in ipairs(backpack:GetChildren()) do
            if item:IsA("Tool") then table.insert(items, item) end
        end
    end
    if player.Character then
        for _, item in ipairs(player.Character:GetChildren()) do
            if item:IsA("Tool") then table.insert(items, item) end
        end
    end
    for _, tool in ipairs(items) do
        local damage = 0
        local speed = 1
        local name = tool.Name:lower()
        if name:find("kunai") then damage = 50; speed = 1.5
        elseif name:find("sword") or name:find("espada") then damage = 40; speed = 1.0
        elseif name:find("hammer") or name:find("martelo") then damage = 60; speed = 0.7
        elseif name:find("axe") or name:find("machado") then damage = 55; speed = 0.8
        elseif name:find("gun") or name:find("pistol") then damage = 30; speed = 2.0
        else damage = 20 end
        local dps = damage * speed
        if dps > bestDPS then bestDPS = dps; bestWeapon = tool end
    end
    return bestWeapon
end

-- ============================================================
-- 3. CONSTRUÇÃO DOS ELEMENTOS DA UI
-- ============================================================
local yOffset = 10

-- Função auxiliar para criar labels
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

-- Seção: Configuração de Amigo
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

-- Seção: Scanner de Armas
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

-- Status
local statusLabel = Instance.new("TextLabel")
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

-- Toggle do Farm
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

-- Ajusta o CanvasSize
contentFrame.CanvasSize = UDim2.new(0, 0, 0, yOffset + 10)

-- ============================================================
-- 4. LÓGICA DOS BOTÕES E FARM
-- ============================================================
scanButton.MouseButton1Click:Connect(function()
    local weapon = scanBestWeapon()
    if weapon then
        statusLabel.Text = "Melhor arma: " .. weapon.Name
    else
        statusLabel.Text = "Nenhuma arma encontrada."
    end
end)

equipButton.MouseButton1Click:Connect(function()
    local weapon = scanBestWeapon()
    if weapon then
        if player.Character and player.Character:FindFirstChildOfClass("Humanoid") then
            player.Character.Humanoid:EquipTool(weapon)
            statusLabel.Text = "Equipada: " .. weapon.Name
        end
    else
        statusLabel.Text = "Nenhuma arma para equipar."
    end
end)

farmToggle.MouseButton1Click:Connect(function()
    Farming = not Farming
    if Farming then
        -- Lê os valores dos campos
        TargetFriendName = friendBox.Text
        KillThreshold = tonumber(thresholdBox.Text) or 5
        
        farmToggle.Text = "⏸ PARAR FARM"
        farmToggle.BackgroundColor3 = Color3.fromRGB(50, 200, 50)
        statusLabel.Text = "Farm ATIVO. Ajudando " .. (TargetFriendName ~= "" and TargetFriendName or "amigo")
        
        -- Inicia a thread do farm
        spawn(function()
            while Farming do
                if PauseFarming then
                    wait(0.5)
                    continue
                end
                
                -- Equipa automaticamente a melhor arma
                local best = scanBestWeapon()
                if best and player.Character and player.Character:FindFirstChildOfClass("Humanoid") then
                    player.Character.Humanoid:EquipTool(best)
                end
                
                -- Verifica se deve atacar o boss
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
                
                -- Ataca o alvo apropriado
                if shouldAttackBoss then
                    local boss, hum = GetBoss()
                    if boss and hum then
                        local args = {[1] = "Swing", [2] = boss}
                        pcall(function()
                            game:GetService("ReplicatedStorage"):FindFirstChild("WeaponEvent"):FireServer(unpack(args))
                        end)
                        statusLabel.Text = "Atacando RAKOOF!"
                    end
                else
                    local scratcher, humS = GetScratcher()
                    if scratcher and humS then
                        local args = {[1] = "Swing", [2] = scratcher}
                        pcall(function()
                            game:GetService("ReplicatedStorage"):FindFirstChild("WeaponEvent"):FireServer(unpack(args))
                        end)
                        statusLabel.Text = "Farmando Scratchers..."
                    else
                        statusLabel.Text = "Procurando inimigos..."
                    end
                end
                
                wait(0.3)
            end
        end)
    else
        farmToggle.Text = "▶ INICIAR FARM"
        farmToggle.BackgroundColor3 = Color3.fromRGB(200, 50, 50)
        statusLabel.Text = "Farm PARADO."
    end
end)

-- ============================================================
-- 5. MONITOR DE TRANSFORMAÇÃO (PAUSA AUTOMÁTICA)
-- ============================================================
spawn(function()
    while true do
        if Farming then
            local boss, _ = GetBoss()
            if boss then
                local transformEffect = boss:FindFirstChild("Transforming") or boss:FindFirstChild("Rage")
                if transformEffect and transformEffect.Value == true then
                    if not TransformDetected then
                        TransformDetected = true
                        PauseFarming = true
                        statusLabel.Text = "⚠️ TRANSFORMAÇÃO! Pausado."
                    end
                else
                    if TransformDetected then
                        TransformDetected = false
                        PauseFarming = false
                        statusLabel.Text = "Transformação finalizada. Retomando..."
                    end
                end
            end
        end
        wait(0.5)
    end
end)

-- ============================================================
-- 6. BOTÃO FLUTUANTE PARA MOBILE
-- ============================================================
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

-- Notificação
game:GetService("StarterGui"):SetCore("SendNotification", {
    Title = "Rakoof Hub",
    Text = "Interface carregada! Configure amigo e inicie o farm.",
    Duration = 5,
})

print("✅ Rakoof Hub completo carregado com sucesso!")
