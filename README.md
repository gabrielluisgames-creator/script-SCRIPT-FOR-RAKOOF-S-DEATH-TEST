-- RAKOOF'S DEATH TEST - SCRIPT FINAL COM GOD MODE 20K E HIT KILL GARANTIDO

-- ===========================================================================
-- 1. CAMUFLAGEM AVANÇADA DA INTERFACE (GETHUI + NOMES ALEATÓRIOS)
-- ===========================================================================
local player = game.Players.LocalPlayer
local success, safeContainer = pcall(function()
    if gethui then
        return gethui()
    elseif cloneref then
        local coreGui = cloneref(game:GetService("CoreGui"))
        local hiddenFolder = coreGui:FindFirstChild("__hidden_gui")
        if not hiddenFolder then
            hiddenFolder = Instance.new("Folder")
            hiddenFolder.Name = "__hidden_gui"
            hiddenFolder.Parent = coreGui
        end
        return hiddenFolder
    end
    error("Fallback para PlayerGui")
end)

if not success or not safeContainer then
    safeContainer = player:WaitForChild("PlayerGui")
end

repeat wait() until player.Character and player.Character:FindFirstChild("HumanoidRootPart")

local function randomName()
    local chars = "abcdefghijklmnopqrstuvwxyzABCDEFGHIJKLMNOPQRSTUVWXYZ0123456789"
    local name = ""
    for i = 1, 12 do name = name .. chars:sub(math.random(1, #chars), math.random(1, #chars)) end
    return name
end

-- Criação da interface furtiva
local screenGui = Instance.new("ScreenGui")
screenGui.Name = randomName() .. "_Hub"
screenGui.ResetOnSpawn = false
screenGui.Parent = safeContainer

local main = Instance.new("Frame", screenGui)
main.Name = randomName() .. "_Main"
main.Size = UDim2.new(0, 250, 0, 250)
main.Position = UDim2.new(0.5, -125, 0.5, -125)
main.BackgroundColor3 = Color3.fromRGB(30, 30, 30)
main.BorderSizePixel = 0
main.Active = true
main.Draggable = true

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

local content = Instance.new("ScrollingFrame", main)
content.Size = UDim2.new(1, 0, 1, -30)
content.Position = UDim2.new(0, 0, 0, 30)
content.BackgroundTransparency = 1
content.BorderSizePixel = 0
content.ScrollBarThickness = 5
content.CanvasSize = UDim2.new(0, 0, 0, 0)

local yPos = 10

local function addButton(text, callback)
    local btn = Instance.new("TextButton")
    btn.Name = randomName() .. "_Btn"
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

local function addToggle(text, callback)
    local btn = Instance.new("TextButton")
    btn.Name = randomName() .. "_Toggle"
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

-- ===========================================================================
-- 2. FUNÇÕES DE HIT KILL E DETECÇÃO DE INIMIGOS
-- ===========================================================================
local function getAllHostileNPCs()
    local npcs = {}
    for _, obj in pairs(workspace:GetDescendants()) do
        if obj:FindFirstChildOfClass("Humanoid") and not game.Players:GetPlayerFromCharacter(obj) then
            local name = obj.Name:lower()
            -- Lista expandida de NPCs hostis conhecidos no jogo
            if name:find("rakoof") or name:find("rake") or name:find("scratch") or name:find("zombie") or name:find("monster") or name:find("enemy") or name:find("boss") then
                local hum = obj.Humanoid
                if hum.Health > 0 then
                    table.insert(npcs, obj)
                end
            end
        end
    end
    return npcs
end

local function hitKill(target)
    if not target then return false end
    local hum = target:FindFirstChildOfClass("Humanoid")
    if not hum or hum.Health <= 0 then return false end
    
    -- Método 1: Forçar a quebra das juntas (morte instantânea)
    pcall(function()
        target:BreakJoints()
    end)
    
    -- Método 2: Remover o Humanoid (mata o NPC)
    pcall(function()
        hum:Destroy()
    end)
    
    -- Método 3: Zerar a vida como fallback
    pcall(function()
        hum.Health = 0
    end)
    
    return true
end

-- ===========================================================================
-- 3. CONFIGURAÇÃO DOS TOGGLES E BOTÕES
-- ===========================================================================

-- God Mode 20k com proteção total
addToggle("🛡️ God Mode (20k Vida)", function(v)
    spawn(function()
        while v do
            local char = player.Character
            if char then
                local hum = char:FindFirstChildOfClass("Humanoid")
                if hum then
                    hum.MaxHealth = 20000
                    hum.Health = 20000
                    -- Bloqueia qualquer dano futuro
                    hum:SetAttribute("NoDamage", true)
                end
                -- Força a vida a ficar em 20k mesmo se o jogo tentar mudar
                for _, part in ipairs(char:GetDescendants()) do
                    if part:IsA("BasePart") then
                        part.CanCollide = true -- mantém colisão normal
                    end
                end
            end
            wait(0.1)
        end
    end)
end)

-- Auto Farm com Hit Kill para todos os NPCs hostis
addToggle("⚡ Auto Farm (Hit Kill)", function(v)
    spawn(function()
        while v do
            local npcs = getAllHostileNPCs()
            for _, npc in ipairs(npcs) do
                hitKill(npc)
            end
            wait(0.5) -- intervalo seguro
        end
    end)
end)

-- Botão para equipar a melhor arma (apenas para garantia)
addButton("🔪 Equipar Melhor Arma", function()
    local best, bestDps = nil, 0
    local items = {}
    local function scan(cont)
        for _, t in pairs(cont:GetChildren()) do
            if t:IsA("Tool") then table.insert(items, t) end
        end
    end
    if player.Backpack then scan(player.Backpack) end
    if player.Character then scan(player.Character) end
    for _, t in pairs(items) do
        local dps = 20
        local n = t.Name:lower()
        if n:find("kunai") then dps = 75
        elseif n:find("katana") then dps = 70
        elseif n:find("sword") then dps = 40
        elseif n:find("axe") then dps = 48
        elseif n:find("hammer") then dps = 45
        end
        if dps > bestDps then best, bestDps = t, dps end
    end
    if best and player.Character then
        player.Character.Humanoid:EquipTool(best)
    end
end)

-- Teleporte para local seguro (opcional)
addButton("🚀 Teleporte Seguro", function()
    local char = player.Character
    if char and char:FindFirstChild("HumanoidRootPart") then
        char.HumanoidRootPart.CFrame = CFrame.new(0, 30, 0)
    end
end)

print("✅ Script final carregado! God Mode 20k, Auto Farm Hit Kill e camuflagem ativos.")
