-- RAKOOF'S DEATH TEST - SCRIPT FUNCIONAL E DIRETO (GOD MODE REAL + HIT KILL)

local player = game.Players.LocalPlayer
local replicatedStorage = game:GetService("ReplicatedStorage")
local runService = game:GetService("RunService")

-- Aguarda personagem
repeat wait() until player.Character and player.Character:FindFirstChild("HumanoidRootPart")

-- ===========================================================================
-- INTERFACE SIMPLES (COM BOTÃO FLUTUANTE)
-- ===========================================================================
local screenGui = Instance.new("ScreenGui", player.PlayerGui)
screenGui.Name = "RakoofHub"

-- Painel principal
local main = Instance.new("Frame", screenGui)
main.Size = UDim2.new(0, 220, 0, 200)
main.Position = UDim2.new(0.5, -110, 0.5, -100)
main.BackgroundColor3 = Color3.fromRGB(35, 35, 45)
main.BorderSizePixel = 0
main.Active = true
main.Draggable = true
main.Visible = true

-- Barra de título
local titleBar = Instance.new("Frame", main)
titleBar.Size = UDim2.new(1, 0, 0, 25)
titleBar.BackgroundColor3 = Color3.fromRGB(25, 25, 35)
titleBar.BorderSizePixel = 0

local title = Instance.new("TextLabel", titleBar)
title.Size = UDim2.new(1, 0, 1, 0)
title.Text = "Rakoof Hub"
title.TextColor3 = Color3.fromRGB(255, 255, 255)
title.BackgroundTransparency = 1
title.Font = Enum.Font.SourceSansBold
title.TextSize = 14

local close = Instance.new("TextButton", titleBar)
close.Size = UDim2.new(0, 25, 0, 25)
close.Position = UDim2.new(1, -25, 0, 0)
close.Text = "X"
close.TextColor3 = Color3.fromRGB(255, 255, 255)
close.BackgroundColor3 = Color3.fromRGB(200, 60, 60)
close.BorderSizePixel = 0
close.Font = Enum.Font.SourceSansBold
close.TextSize = 14
close.MouseButton1Click:Connect(function() screenGui:Destroy() end)

-- Conteúdo
local content = Instance.new("ScrollingFrame", main)
content.Size = UDim2.new(1, 0, 1, -25)
content.Position = UDim2.new(0, 0, 0, 25)
content.BackgroundTransparency = 1
content.BorderSizePixel = 0
content.ScrollBarThickness = 4
content.CanvasSize = UDim2.new(0, 0, 0, 0)

-- Botão flutuante (minimizar/abrir)
local floatBtn = Instance.new("TextButton", screenGui)
floatBtn.Size = UDim2.new(0, 45, 0, 45)
floatBtn.Position = UDim2.new(1, -60, 0.8, 0)
floatBtn.BackgroundColor3 = Color3.fromRGB(45, 45, 55)
floatBtn.TextColor3 = Color3.fromRGB(255, 255, 255)
floatBtn.Text = "⚡"
floatBtn.Font = Enum.Font.SourceSansBold
floatBtn.TextSize = 22
floatBtn.BorderSizePixel = 0
floatBtn.Visible = false
floatBtn.Active = true
floatBtn.Draggable = true
floatBtn.MouseButton1Click:Connect(function()
    main.Visible = true
    floatBtn.Visible = false
end)

-- Botão minimizar no painel
local minimize = Instance.new("TextButton", titleBar)
minimize.Size = UDim2.new(0, 25, 0, 25)
minimize.Position = UDim2.new(1, -55, 0, 0)
minimize.Text = "–"
minimize.TextColor3 = Color3.fromRGB(255, 255, 255)
minimize.BackgroundColor3 = Color3.fromRGB(100, 100, 120)
minimize.BorderSizePixel = 0
minimize.Font = Enum.Font.SourceSansBold
minimize.TextSize = 18
minimize.MouseButton1Click:Connect(function()
    main.Visible = false
    floatBtn.Visible = true
end)

local yPos = 10
local function addButton(text, callback)
    local btn = Instance.new("TextButton")
    btn.Size = UDim2.new(1, -20, 0, 30)
    btn.Position = UDim2.new(0, 10, 0, yPos)
    btn.Text = text
    btn.BackgroundColor3 = Color3.fromRGB(60, 60, 80)
    btn.TextColor3 = Color3.fromRGB(255, 255, 255)
    btn.BorderSizePixel = 0
    btn.Font = Enum.Font.SourceSansBold
    btn.TextSize = 13
    btn.Parent = content
    btn.MouseButton1Click:Connect(callback)
    yPos = yPos + 35
    content.CanvasSize = UDim2.new(0, 0, 0, yPos + 10)
    return btn
end

local function addToggle(text, callback)
    local btn = Instance.new("TextButton")
    btn.Size = UDim2.new(1, -20, 0, 30)
    btn.Position = UDim2.new(0, 10, 0, yPos)
    btn.Text = "⚪ " .. text
    btn.BackgroundColor3 = Color3.fromRGB(60, 60, 80)
    btn.TextColor3 = Color3.fromRGB(255, 255, 255)
    btn.BorderSizePixel = 0
    btn.Font = Enum.Font.SourceSansBold
    btn.TextSize = 13
    btn.Parent = content
    local enabled = false
    btn.MouseButton1Click:Connect(function()
        enabled = not enabled
        btn.Text = (enabled and "🔵 " or "⚪ ") .. text
        callback(enabled)
    end)
    yPos = yPos + 35
    content.CanvasSize = UDim2.new(0, 0, 0, yPos + 10)
    return btn
end

-- ===========================================================================
-- FUNÇÕES DE JOGO (HIT KILL REAL)
-- ===========================================================================
local function findRakoof()
    for _, obj in ipairs(workspace:GetDescendants()) do
        if obj:IsA("Model") and (obj.Name:lower():find("rakoof") or obj.Name == "Rake") then
            local hum = obj:FindFirstChildOfClass("Humanoid")
            if hum and hum.Health > 0 then
                return obj, hum
            end
        end
    end
    return nil, nil
end

local function findAllHostiles()
    local hostiles = {}
    for _, obj in ipairs(workspace:GetDescendants()) do
        if obj:IsA("Model") and obj:FindFirstChildOfClass("Humanoid") then
            local hum = obj.Humanoid
            if hum.Health > 0 and not game.Players:GetPlayerFromCharacter(obj) then
                local name = obj.Name:lower()
                if name:find("rakoof") or name:find("rake") or name:find("scratch") or name:find("zombie") or name:find("monster") or name:find("enemy") then
                    table.insert(hostiles, obj)
                end
            end
        end
    end
    return hostiles
end

local function hitKill(target)
    if not target then return false end
    local hum = target:FindFirstChildOfClass("Humanoid")
    if hum and hum.Health > 0 then
        hum.Health = 0
        return true
    end
    return false
end

-- ===========================================================================
-- GOD MODE REAL (restaura vida para 20k a cada dano)
-- ===========================================================================
local godModeEnabled = false
local godModeConnection

local function enableGodMode()
    if godModeConnection then godModeConnection:Disconnect() end
    local char = player.Character
    if not char then return end
    local hum = char:FindFirstChildOfClass("Humanoid")
    if not hum then return end
    
    -- Define vida máxima para 20k
    hum.MaxHealth = 20000
    hum.Health = 20000
    
    -- Bloqueia dano real
    godModeConnection = hum.HealthChanged:Connect(function(newHealth)
        if godModeEnabled and newHealth < 20000 then
            hum.Health = 20000
        end
    end)
end

-- ===========================================================================
-- AUTO FARM (HIT KILL EM TODOS OS HOSTIS)
-- ===========================================================================
local autoFarmEnabled = false
spawn(function()
    while true do
        if autoFarmEnabled then
            local hostiles = findAllHostiles()
            for _, enemy in ipairs(hostiles) do
                hitKill(enemy)
            end
        end
        wait(0.3) -- Intervalo curto para matar rápido
    end
end)

-- ===========================================================================
-- BOTÕES E TOGGLES
-- ===========================================================================
addToggle("🛡️ God Mode (20k real)", function(v)
    godModeEnabled = v
    if v then
        enableGodMode()
    else
        if godModeConnection then
            godModeConnection:Disconnect()
            godModeConnection = nil
        end
    end
end)

addToggle("⚡ Auto Farm (Hit Kill)", function(v)
    autoFarmEnabled = v
end)

addButton("🔪 Equipar Melhor Arma", function()
    -- Função simples de equipar qualquer arma disponível
    local tool = nil
    if player.Backpack then
        for _, t in ipairs(player.Backpack:GetChildren()) do
            if t:IsA("Tool") then tool = t; break end
        end
    end
    if not tool and player.Character then
        for _, t in ipairs(player.Character:GetChildren()) do
            if t:IsA("Tool") then tool = t; break end
        end
    end
    if tool and player.Character then
        player.Character.Humanoid:EquipTool(tool)
    end
end)

addButton("🎯 Teleportar até Rakoof", function()
    local rakoof, _ = findRakoof()
    if rakoof and player.Character and player.Character:FindFirstChild("HumanoidRootPart") then
        local root = rakoof:FindFirstChild("HumanoidRootPart") or rakoof:FindFirstChild("Torso") or rakoof:FindFirstChild("Head")
        if root then
            player.Character.HumanoidRootPart.CFrame = root.CFrame * CFrame.new(0, 5, 0)
        end
    end
end)

addButton("☠️ Matar Rakoof Agora", function()
    local rakoof, hum = findRakoof()
    if rakoof and hum then
        hum.Health = 0
    end
end)

-- Ajuste inicial do personagem
if player.Character then
    local hum = player.Character:FindFirstChildOfClass("Humanoid")
    if hum then
        hum.MaxHealth = 100 -- valor padrão do jogo, mas será sobrescrito pelo God Mode se ativado
    end
end

print("✅ Script carregado! Use o botão '–' para minimizar.")
