-- RAKOOF'S DEATH TEST - SCRIPT NUCLEAR (REFEITO E OTIMIZADO)
-- ATENÇÃO: Executar com o Roblox já aberto no jogo.

-- 1. Aguarda o básico
local player = game.Players.LocalPlayer
repeat wait() until player.Character and player.Character:FindFirstChild("HumanoidRootPart")

-- 2. Cria a interface mais simples do mundo
local screenGui = Instance.new("ScreenGui", player.PlayerGui)
screenGui.Name = "HubRakoof"

-- Painel principal (pequeno e leve)
local main = Instance.new("Frame", screenGui)
main.Size = UDim2.new(0, 250, 0, 250)
main.Position = UDim2.new(0.5, -125, 0.5, -125)
main.BackgroundColor3 = Color3.fromRGB(30, 30, 30)
main.BorderSizePixel = 0
main.Active = true
main.Draggable = true

-- Barra de título
local titleBar = Instance.new("Frame", main)
titleBar.Size = UDim2.new(1, 0, 0, 30)
titleBar.BackgroundColor3 = Color3.fromRGB(20, 20, 20)
titleBar.BorderSizePixel = 0

local title = Instance.new("TextLabel", titleBar)
title.Size = UDim2.new(1, 0, 1, 0)
title.Text = "Rakoof Hub"
title.TextColor3 = Color3.fromRGB(255, 255, 255)
title.BackgroundTransparency = 1
title.Font = Enum.Font.SourceSansBold
title.TextSize = 16

local close = Instance.new("TextButton", titleBar)
close.Size = UDim2.new(0, 30, 0, 30)
close.Position = UDim2.new(1, -30, 0, 0)
close.Text = "X"
close.TextColor3 = Color3.fromRGB(255, 255, 255)
close.BackgroundColor3 = Color3.fromRGB(200, 50, 50)
close.BorderSizePixel = 0
close.Font = Enum.Font.SourceSansBold
close.TextSize = 16
close.MouseButton1Click:Connect(function() screenGui:Destroy() end)

-- Conteúdo com scroll
local content = Instance.new("ScrollingFrame", main)
content.Size = UDim2.new(1, 0, 1, -30)
content.Position = UDim2.new(0, 0, 0, 30)
content.BackgroundTransparency = 1
content.BorderSizePixel = 0
content.ScrollBarThickness = 5
content.CanvasSize = UDim2.new(0, 0, 0, 0)

local yPos = 10

-- Função para adicionar botão
local function addButton(text, callback)
    local btn = Instance.new("TextButton")
    btn.Size = UDim2.new(1, -20, 0, 35)
    btn.Position = UDim2.new(0, 10, 0, yPos)
    btn.Text = text
    btn.BackgroundColor3 = Color3.fromRGB(50, 50, 70)
    btn.TextColor3 = Color3.fromRGB(255, 255, 255)
    btn.BorderSizePixel = 0
    btn.Font = Enum.Font.SourceSansBold
    btn.TextSize = 14
    btn.Parent = content
    btn.MouseButton1Click:Connect(callback)
    yPos = yPos + 40
    content.CanvasSize = UDim2.new(0, 0, 0, yPos + 20)
    return btn
end

-- Função para adicionar toggle
local function addToggle(text, callback)
    local btn = Instance.new("TextButton")
    btn.Size = UDim2.new(1, -20, 0, 35)
    btn.Position = UDim2.new(0, 10, 0, yPos)
    btn.Text = "⚪ " .. text
    btn.BackgroundColor3 = Color3.fromRGB(50, 50, 70)
    btn.TextColor3 = Color3.fromRGB(255, 255, 255)
    btn.BorderSizePixel = 0
    btn.Font = Enum.Font.SourceSansBold
    btn.TextSize = 14
    btn.Parent = content
    local enabled = false
    btn.MouseButton1Click:Connect(function()
        enabled = not enabled
        btn.Text = (enabled and "🔵 " or "⚪ ") .. text
        callback(enabled)
    end)
    yPos = yPos + 40
    content.CanvasSize = UDim2.new(0, 0, 0, yPos + 20)
    return btn
end

-- 3. Lógica do jogo
local rs = game:GetService("ReplicatedStorage")
local weaponEvent = rs:FindFirstChild("WeaponEvent")

local function getBoss()
    for _, v in pairs(workspace:GetDescendants()) do
        if v.Name:lower():find("rakoof") or v.Name == "Rake" then
            local hum = v:FindFirstChildOfClass("Humanoid")
            if hum and hum.Health > 0 then return v end
        end
    end
    return nil
end

local function equipBest()
    local best, bestDps = nil, 0
    local items = {}
    
    -- Junta tudo
    local function scan(cont)
        for _, t in pairs(cont:GetChildren()) do
            if t:IsA("Tool") then table.insert(items, t) end
        end
    end
    if player.Backpack then scan(player.Backpack) end
    if player.Character then scan(player.Character) end
    
    for _, t in pairs(items) do
        local dps = 20 -- valor genérico
        if t.Name:lower():find("kunai") then dps = 75
        elseif t.Name:lower():find("katana") then dps = 70
        elseif t.Name:lower():find("sword") then dps = 40
        elseif t.Name:lower():find("axe") then dps = 48
        elseif t.Name:lower():find("hammer") then dps = 45
        end
        if dps > bestDps then best, bestDps = t, dps end
    end
    if best and player.Character then
        player.Character.Humanoid:EquipTool(best)
    end
end

-- 4. Botões e Funções
addButton("🎯 Matar Rakoof", function()
    local boss = getBoss()
    if boss then boss.Humanoid.Health = 0 end
end)

addButton("🔪 Equipar Melhor", equipBest)

addToggle("🛡️ God Mode", function(v)
    spawn(function()
        while v do
            local c = player.Character
            if c and c:FindFirstChildOfClass("Humanoid") then
                c.Humanoid.Health = c.Humanoid.MaxHealth
            end
            wait(0.1)
        end
    end)
end)

addToggle("⚡ Auto Farm", function(v)
    spawn(function()
        while v do
            local boss = getBoss()
            if boss and weaponEvent then
                pcall(function()
                    weaponEvent:FireServer("Swing", boss)
                end)
            end
            wait(0.3)
        end
    end)
end)

addToggle("🔋 Stamina Infinita", function(v)
    spawn(function()
        while v do
            local c = player.Character
            if c then
                local h = c:FindFirstChildOfClass("Humanoid")
                if h then
                    local s = h:FindFirstChild("Stamina")
                    if s then s.Value = 100 end
                    h:SetStateEnabled(Enum.HumanoidStateType.Running, true)
                end
            end
            wait(0.1)
        end
    end)
end)

addButton("🚀 Teleporte Seguro", function()
    local c = player.Character
    if c and c:FindFirstChild("HumanoidRootPart") then
        c.HumanoidRootPart.CFrame = CFrame.new(0, 30, 0)
    end
end)

print("✅ Script Nuclear carregado!")
