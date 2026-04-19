-- RAKOOF'S DEATH TEST - SCRIPT FURTIVO E ANTI-KICK
-- ATENÇÃO: Abra o Roblox e entre no jogo PRIMEIRO. Depois execute este script.

-- ===========================================================================
-- CAMADA 1: CAMUFLAGEM DA INTERFACE (ESCONDENDO O PAINEL DO JOGO)
-- ===========================================================================
local player = game.Players.LocalPlayer
local safeContainer

-- Tenta usar a função 'gethui' para um local secreto que o jogo não consegue ver
local success, err = pcall(function()
    if gethui then
        safeContainer = gethui()
    elseif cloneref then
        local coreGui = cloneref(game:GetService("CoreGui"))
        local hiddenFolder = coreGui:FindFirstChild("__hidden_gui")
        if not hiddenFolder then
            hiddenFolder = Instance.new("Folder")
            hiddenFolder.Name = "__hidden_gui"
            hiddenFolder.Parent = coreGui
        end
        safeContainer = hiddenFolder
    else
        error("Nenhum local secreto encontrado, usando PlayerGui.")
    end
end)

-- Se nenhum local seguro foi encontrado, usa o PlayerGui padrão (mas com nomes aleatórios)
if not success or not safeContainer then
    safeContainer = player:WaitForChild("PlayerGui")
    print("Aviso: gethui não encontrado. Usando PlayerGui com nomes aleatórios.")
end

-- Aguarda o personagem carregar completamente
repeat wait() until player.Character and player.Character:FindFirstChild("HumanoidRootPart")

-- Função para gerar nomes aleatórios e dificultar a detecção da interface
local function randomName()
    local chars = "abcdefghijklmnopqrstuvwxyzABCDEFGHIJKLMNOPQRSTUVWXYZ0123456789"
    local name = ""
    for i = 1, 15 do name = name .. chars:sub(math.random(1, #chars), math.random(1, #chars)) end
    return name
end

-- ===========================================================================
-- CAMADA 2: BYPASS DE ANTI-CHEAT (NEUTRALIZANDO AS DEFESAS DO JOGO)
-- ===========================================================================
spawn(function()
    pcall(function()
        -- 1. Hook da função Kick (impede que você seja expulso)
        local LocalPlayer = player
        if LocalPlayer then
            local Kick = LocalPlayer.Kick
            LocalPlayer.Kick = function(...) return nil end
        end

        -- 2. Desativa scripts com nomes suspeitos (Anti-Cheats comuns)
        for _, obj in pairs(game:GetDescendants()) do
            if obj:IsA("LocalScript") or obj:IsA("Script") or obj:IsA("ModuleScript") then
                local name = obj.Name:lower()
                if name:find("anti") or name:find("kick") or name:find("ban") or name:find("detect") or name:find("cheat") or name:find("hack") or name:find("exploit") or name:find("guard") then
                    obj.Enabled = false
                end
            elseif obj:IsA("RemoteEvent") or obj:IsA("RemoteFunction") then
                local name = obj.Name:lower()
                if name:find("kick") or name:find("ban") or name:find("report") or name:find("modcall") then
                    obj:Destroy()
                end
            end
        end
        
        -- 3. (Opcional) Tenta desativar logs do jogo
        if _G then _G.print = function() end; _G.warn = function() end end
    end)
end)

-- ===========================================================================
-- INTERFACE (COM NOMES ALEATÓRIOS PARA CAMUFLAGEM)
-- ===========================================================================
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
title.Text = "Painel"
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

-- Função para adicionar botões e toggles (com nomes aleatórios)
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
-- FUNÇÕES DO JOGO (COM HIT KILL E COMPORTAMENTO MAIS NATURAL)
-- ===========================================================================
local rs = game:GetService("ReplicatedStorage")
local weaponEvent = rs:FindFirstChild("WeaponEvent")

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
-- GOD MODE (COM VIDA REAL DE 20K)
-- ===========================================================================
local godModeEnabled = false
local godModeConnection

local function enableGodMode()
    if godModeConnection then godModeConnection:Disconnect() end
    local char = player.Character
    if not char then return end
    local hum = char:FindFirstChildOfClass("Humanoid")
    if not hum then return end
    
    hum.MaxHealth = 20000
    hum.Health = 20000
    
    godModeConnection = hum.HealthChanged:Connect(function(newHealth)
        if godModeEnabled and newHealth < 20000 then
            hum.Health = 20000
        end
    end)
end

player.CharacterAdded:Connect(enableGodMode) -- Reaplica god mode ao renascer

-- ===========================================================================
-- AUTO FARM (COMPORTAMENTO MAIS LENTO E "HUMANO")
-- ===========================================================================
local autoFarmEnabled = false
spawn(function()
    while true do
        if autoFarmEnabled then
            local hostiles = findAllHostiles()
            for _, enemy in ipairs(hostiles) do
                hitKill(enemy)
                wait(0.05) -- Pequena pausa entre cada kill
            end
            -- Pausa maior e aleatória para não parecer um robô
            wait(math.random(25, 40) / 10) -- Pausa entre 2.5 e 4 segundos
        else
            wait(1)
        end
    end
end)

-- ===========================================================================
-- INTERFACE (BOTÕES E TOGGLES)
-- ===========================================================================
addToggle("🛡️ God Mode (20k real)", function(v)
    godModeEnabled = v
    if v then
        enableGodMode()
    else
        if godModeConnection then godModeConnection:Disconnect() end
    end
end)

addToggle("⚡ Auto Farm (Hit Kill)", function(v)
    autoFarmEnabled = v
end)

addButton("🔪 Equipar Melhor Arma", function()
    local tool = nil
    if player.Backpack then for _, t in ipairs(player.Backpack:GetChildren()) do if t:IsA("Tool") then tool = t; break end end end
    if not tool and player.Character then for _, t in ipairs(player.Character:GetChildren()) do if t:IsA("Tool") then tool = t; break end end end
    if tool and player.Character then player.Character.Humanoid:EquipTool(tool) end
end)

addButton("🎯 Teleportar até Rakoof", function()
    local rakoof = nil
    for _, obj in ipairs(workspace:GetDescendants()) do
        if obj:IsA("Model") and (obj.Name:lower():find("rakoof") or obj.Name == "Rake") and obj:FindFirstChildOfClass("Humanoid") then
            rakoof = obj; break
        end
    end
    if rakoof and player.Character and player.Character:FindFirstChild("HumanoidRootPart") then
        local root = rakoof:FindFirstChild("HumanoidRootPart") or rakoof:FindFirstChild("Torso") or rakoof:FindFirstChild("Head")
        if root then player.Character.HumanoidRootPart.CFrame = root.CFrame * CFrame.new(0, 5, 0) end
    end
end)

addButton("☠️ Matar Rakoof Agora", function()
    local rakoof = nil
    for _, obj in ipairs(workspace:GetDescendants()) do
        if obj:IsA("Model") and (obj.Name:lower():find("rakoof") or obj.Name == "Rake") and obj:FindFirstChildOfClass("Humanoid") then
            rakoof = obj; break
        end
    end
    if rakoof then hitKill(rakoof) end
end)

-- Notificação de carregamento (apenas no console, para não chamar atenção)
print("✅ Script furtivo carregado. God Mode, Auto Farm e mais ativos.")
