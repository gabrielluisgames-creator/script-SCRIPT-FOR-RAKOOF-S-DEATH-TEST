--[[
    RAKOOF'S DEATH TEST - SCRIPT COM INTERFACE DIRETA E SCANNER
    - Cria uma ScreenGui do zero para garantir compatibilidade
    - Scanner de itens para encontrar a arma mais forte e rápida
    - Botão para esconder/mostrar a interface
]]

-- 1. Criação da Interface Gráfica (ScreenGui)
local player = game.Players.LocalPlayer
local screenGui = Instance.new("ScreenGui")
screenGui.Name = "RakoofHubUI"
screenGui.Parent = player:WaitForChild("PlayerGui")

-- Frame Principal (o painel)
local mainFrame = Instance.new("Frame")
mainFrame.Name = "MainFrame"
mainFrame.Size = UDim2.new(0, 300, 0, 400)
mainFrame.Position = UDim2.new(0.5, -150, 0.5, -200)
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

-- Botão para Fechar/Abrir
local toggleButton = Instance.new("TextButton")
toggleButton.Size = UDim2.new(0, 60, 0, 25)
toggleButton.Position = UDim2.new(1, -65, 0, 5)
toggleButton.BackgroundColor3 = Color3.fromRGB(200, 50, 50)
toggleButton.TextColor3 = Color3.fromRGB(255, 255, 255)
toggleButton.Text = "Ocultar"
toggleButton.Font = Enum.Font.SourceSansBold
toggleButton.TextSize = 14
toggleButton.Parent = mainFrame

-- Estado do painel (visível ou não)
local panelVisible = true
toggleButton.MouseButton1Click:Connect(function()
    panelVisible = not panelVisible
    mainFrame.Visible = panelVisible
    toggleButton.Text = panelVisible and "Ocultar" or "Mostrar"
end)

-- Área de Conteúdo
local contentFrame = Instance.new("Frame")
contentFrame.Size = UDim2.new(1, 0, 1, -35)
contentFrame.Position = UDim2.new(0, 0, 0, 35)
contentFrame.BackgroundTransparency = 1
contentFrame.Parent = mainFrame

-- 2. Função do Scanner de Armas (Encontra a mais forte e rápida)
local function scanBestWeapon()
    local bestWeapon = nil
    local bestDPS = 0
    local player = game.Players.LocalPlayer
    local backpack = player:FindFirstChild("Backpack")
    
    -- Lista de itens para verificar (mochila e personagem)
    local items = {}
    if backpack then
        for _, item in ipairs(backpack:GetChildren()) do
            if item:IsA("Tool") then
                table.insert(items, item)
            end
        end
    end
    if player.Character then
        for _, item in ipairs(player.Character:GetChildren()) do
            if item:IsA("Tool") then
                table.insert(items, item)
            end
        end
    end
    
    -- Para cada ferramenta, tenta estimar o dano (DPS)
    for _, tool in ipairs(items) do
        -- Tenta encontrar um valor de dano no nome ou em atributos
        local damage = 0
        local speed = 1 -- Velocidade base
        
        -- Heurística para identificar dano baseado no nome da arma
        local name = tool.Name:lower()
        if name:find("kunai") then
            damage = 50
            speed = 1.5
        elseif name:find("sword") or name:find("espada") then
            damage = 40
            speed = 1.0
        elseif name:find("hammer") or name:find("martelo") then
            damage = 60
            speed = 0.7
        elseif name:find("axe") or name:find("machado") then
            damage = 55
            speed = 0.8
        elseif name:find("gun") or name:find("pistol") then
            damage = 30
            speed = 2.0
        else
            damage = 20 -- Valor padrão
        end
        
        local dps = damage * speed
        if dps > bestDPS then
            bestDPS = dps
            bestWeapon = tool
        end
    end
    
    return bestWeapon
end

-- 3. Elementos da Interface
local statusLabel = Instance.new("TextLabel")
statusLabel.Size = UDim2.new(1, -10, 0, 25)
statusLabel.Position = UDim2.new(0, 5, 0, 10)
statusLabel.BackgroundTransparency = 1
statusLabel.TextColor3 = Color3.fromRGB(255, 255, 255)
statusLabel.Text = "Status: Aguardando"
statusLabel.Font = Enum.Font.SourceSans
statusLabel.TextSize = 14
statusLabel.TextXAlignment = Enum.TextXAlignment.Left
statusLabel.Parent = contentFrame

local scanButton = Instance.new("TextButton")
scanButton.Size = UDim2.new(1, -10, 0, 30)
scanButton.Position = UDim2.new(0, 5, 0, 45)
scanButton.BackgroundColor3 = Color3.fromRGB(0, 150, 200)
scanButton.TextColor3 = Color3.fromRGB(255, 255, 255)
scanButton.Text = "🔍 Escanear Melhor Arma"
scanButton.Font = Enum.Font.SourceSansBold
scanButton.TextSize = 14
scanButton.Parent = contentFrame

scanButton.MouseButton1Click:Connect(function()
    local weapon = scanBestWeapon()
    if weapon then
        statusLabel.Text = "Melhor arma: " .. weapon.Name .. " (Equipando...)"
        player.Character.Humanoid:EquipTool(weapon)
    else
        statusLabel.Text = "Nenhuma arma encontrada."
    end
end)

local equipButton = Instance.new("TextButton")
equipButton.Size = UDim2.new(1, -10, 0, 30)
equipButton.Position = UDim2.new(0, 5, 0, 85)
equipButton.BackgroundColor3 = Color3.fromRGB(0, 200, 100)
equipButton.TextColor3 = Color3.fromRGB(255, 255, 255)
equipButton.Text = "⚔️ Equipar Melhor Arma"
equipButton.Font = Enum.Font.SourceSansBold
equipButton.TextSize = 14
equipButton.Parent = contentFrame

equipButton.MouseButton1Click:Connect(function()
    local weapon = scanBestWeapon()
    if weapon then
        player.Character.Humanoid:EquipTool(weapon)
        statusLabel.Text = "Arma equipada: " .. weapon.Name
    else
        statusLabel.Text = "Nenhuma arma para equipar."
    end
end)

-- 4. Botão para esconder/mostrar o painel flutuante (para celular)
local floatButton = Instance.new("TextButton")
floatButton.Size = UDim2.new(0, 40, 0, 40)
floatButton.Position = UDim2.new(1, -50, 0.5, -20)
floatButton.BackgroundColor3 = Color3.fromRGB(30, 30, 30)
floatButton.TextColor3 = Color3.fromRGB(255, 255, 255)
floatButton.Text = "⚡"
floatButton.Font = Enum.Font.SourceSansBold
floatButton.TextSize = 20
floatButton.Parent = screenGui
floatButton.Active = true
floatButton.Draggable = true

floatButton.MouseButton1Click:Connect(function()
    panelVisible = not panelVisible
    mainFrame.Visible = panelVisible
    toggleButton.Text = panelVisible and "Ocultar" or "Mostrar"
end)

-- Notificação Inicial
game:GetService("StarterGui"):SetCore("SendNotification", {
    Title = "Rakoof Hub",
    Text = "Interface carregada! Use os botões para escanear e equipar a melhor arma.",
    Duration = 5,
})

print("✅ Rakoof Hub com interface direta carregado!")
