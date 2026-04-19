-- RAKOOF'S DEATH TEST - SCRIPT OTIMIZADO COM HIT KILL

-- ===========================================================================
-- 1. CONFIGURAÇÃO DE CAMUFLAGEM (ANTI-DETECÇÃO DE GUI)
-- ===========================================================================
local player = game:GetService("Players").LocalPlayer
local success, safeContainer = pcall(function()
    -- Tenta usar a função 'gethui' para um local secreto
    if gethui then
        return gethui()
    -- Se não existir, tenta criar uma pasta oculta no CoreGui
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
    -- Caso tudo falhe, usa o PlayerGui (mas com nomes aleatórios)
    error("Usando fallback para PlayerGui")
end)

-- Se nenhum local seguro foi encontrado, usa o PlayerGui padrão
if not success or not safeContainer then
    safeContainer = player:WaitForChild("PlayerGui")
    print("Aviso: gethui não encontrado. Usando PlayerGui com nomes aleatórios.")
end

-- Aguarda o personagem carregar
repeat wait() until player.Character and player.Character:FindFirstChild("HumanoidRootPart")

-- Função para gerar nomes aleatórios (descartáveis)
local function randomName()
    local chars = "abcdefghijklmnopqrstuvwxyzABCDEFGHIJKLMNOPQRSTUVWXYZ0123456789"
    local name = ""
    for i = 1, 12 do
        name = name .. chars:sub(math.random(1, #chars), math.random(1, #chars))
    end
    return name
end

-- ===========================================================================
-- 2. CRIAÇÃO DA INTERFACE (COM NOMES ALEATÓRIOS OU DENTRO DO GETHUI)
-- ===========================================================================
local screenGui = Instance.new("ScreenGui")
screenGui.Name = randomName() .. "_Hub"
screenGui.ResetOnSpawn = false -- CRUCIAL: Impede que o jogo recrie a GUI
screenGui.Parent = safeContainer

-- Painel principal
local main = Instance.new("Frame", screenGui)
main.Name = randomName() .. "_Main"
main.Size = UDim2.new(0, 250, 0, 250)
main.Position = UDim2.new(0.5, -125, 0.5, -125)
main.BackgroundColor3 = Color3.fromRGB(30, 30, 30)
main.BorderSizePixel = 0
main.Active = true
main.Draggable = true

-- Barra de título
local titleBar = Instance.new("Frame", main)
titleBar.Name = randomName() .. "_Title"
titleBar.Size = UDim2.new(1, 0, 0, 30)
titleBar.BackgroundColor3 = Color3.fromRGB(20, 20, 20)
titleBar.BorderSizePixel = 0

local title = Instance.new("TextLabel", titleBar)
title.Size = UDim2.new(1, 0, 1, 0)
title.Text = "Painel"
title.TextColor3 = Color3.fromRGB(255, 255, 255)
title.BackgroundTransparency = 1
title.Font = Enum.Font.SourceSansBold
title.TextSize = 16

local close = Instance.new("TextButton", titleBar)
close.Name = randomName() .. "_Close"
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

-- Função para adicionar toggle
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
-- 3. LÓGICA DO JOGO (OTIMIZADA COM HIT KILL)
-- ===========================================================================
local rs = game:GetService("ReplicatedStorage")
local weaponEvent = rs:FindFirstChild("WeaponEvent")

-- Função para encontrar o Rakoof (chefe principal)
local function getBoss()
    for _, v in pairs(workspace:GetDescendants()) do
        if v.Name:lower():find("rakoof") or v.Name == "Rake" then
            local hum = v:FindFirstChildOfClass("Humanoid")
            if hum and hum.Health > 0 then return v, hum end
        end
    end
    return nil, nil
end

-- Função para encontrar outros NPCs que podem causar dano
local function getOtherHostileNPCs()
    local hostileNPCs = {}
    for _, v in pairs(workspace:GetDescendants()) do
        -- Verifica se é um NPC (tem Humanoid) e se não é o jogador
        if v:FindFirstChildOfClass("Humanoid") and not game.Players:GetPlayerFromCharacter(v) then
            -- Verifica se o nome é comum para NPCs hostis (Scratcher, Zombie, etc.)
            local name = v.Name:lower()
            if name:find("scratcher") or name:find("zombie") or name:find("monster") or name:find("enemy") then
                local hum = v:FindFirstChildOfClass("Humanoid")
                if hum and hum.Health > 0 then
                    table.insert(hostileNPCs, v)
                end
            end
        end
    end
    return hostileNPCs
end

-- Função de Hit Kill: Mata instantaneamente o alvo
local function hitKill(target)
    if target then
        local hum = target:FindFirstChildOfClass("Humanoid")
        if hum then
            hum.Health = 0
            return true
        end
    end
    return false
end

-- Função para equipar a melhor arma (apenas para garantir que o jogador tenha uma ferramenta equipada)
local function equipBest()
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

-- ===========================================================================
-- 4. INTERFACE DO USUÁRIO (BOTÕES E TOGGLES)
-- ===========================================================================

-- God Mode com vida em 10k e regeneração instantânea
addToggle("🛡️ God Mode (10k Vida)", function(v)
    spawn(function()
        while v do
            local c = player.Character
            if c and c:FindFirstChildOfClass("Humanoid") then
                local hum = c.Humanoid
                hum.MaxHealth = 10000
                hum.Health = 10000
            end
            wait(0.1) -- Regeneração instantânea
        end
    end)
end)

-- Auto Farm com Hit Kill (procura NPCs hostis e os mata)
addToggle("⚡ Auto Farm (Hit Kill)", function(v)
    spawn(function()
        while v do
            local boss, _ = getBoss()
            local hostileNPCs = getOtherHostileNPCs()
            
            -- Prioriza o Rakoof
            if boss then
                hitKill(boss)
            -- Depois mata outros NPCs hostis
            elseif #hostileNPCs > 0 then
                for _, npc in ipairs(hostileNPCs) do
                    hitKill(npc)
                end
            end
            wait(0.5) -- Intervalo maior para evitar suspeitas
        end
    end)
end)

addButton("🔪 Equipar Melhor Arma", equipBest)

addButton("🚀 Teleporte Seguro", function()
    local c = player.Character
    if c and c:FindFirstChild("HumanoidRootPart") then
        c.HumanoidRootPart.CFrame = CFrame.new(0, 30, 0)
    end
end)

print("✅ Script Otimizado carregado! God Mode (10k), Auto Farm (Hit Kill) e Equipar Arma ativos.")
