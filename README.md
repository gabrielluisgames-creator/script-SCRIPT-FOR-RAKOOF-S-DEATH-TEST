-- RAKOOF'S DEATH TEST - SCRIPT FURTIVO (TENTATIVA AVANÇADA DE BYPASS)

-- ===========================================================================
-- CAMADA DE PROTEÇÃO INICIAL (EXECUTADA ANTES DE TUDO)
-- ===========================================================================
local function applyStealthMeasures()
    local success, err = pcall(function()
        local Players = game:GetService("Players")
        local LocalPlayer = Players.LocalPlayer
        
        -- 1. Hook mais robusto da função Kick
        if LocalPlayer then
            local Kick = LocalPlayer.Kick
            LocalPlayer.Kick = function(...) 
                -- Não faz nada, apenas engole a tentativa de kick
                return nil 
            end
        end

        -- 2. Desativa scripts suspeitos de forma mais ampla
        for _, obj in ipairs(game:GetDescendants()) do
            if obj:IsA("LocalScript") or obj:IsA("Script") or obj:IsA("ModuleScript") then
                local name = obj.Name:lower()
                -- Palavras-chave comuns em anti-cheats
                if name:find("anti") or name:find("kick") or name:find("ban") or 
                   name:find("detect") or name:find("cheat") or name:find("hack") or 
                   name:find("exploit") or name:find("guard") or name:find("watch") then
                    obj.Enabled = false
                end
            elseif obj:IsA("RemoteEvent") or obj:IsA("RemoteFunction") then
                local name = obj.Name:lower()
                if name:find("kick") or name:find("ban") or name:find("report") or 
                   name:find("modcall") or name:find("admin") then
                    obj:Destroy()
                end
            elseif obj:IsA("ScreenGui") then
                -- Destroi GUIs de admin frequentemente usadas para kickar
                local name = obj.Name:lower()
                if name:find("admin") or name:find("mod") or name:find("cmd") then
                    obj.Enabled = false
                end
            end
        end
        
        -- 3. Remove listeners de logs suspeitos
        if _G then
            _G.print = function() end
            _G.warn = function() end
        end
    end)
    if not success then
        print("Aviso: Algumas proteções não puderam ser aplicadas.")
    end
end

applyStealthMeasures()

-- ===========================================================================
-- CONFIGURAÇÃO DE SEGURANÇA DO SCRIPT
-- ===========================================================================
local player = game.Players.LocalPlayer
repeat wait() until player.Character and player.Character:FindFirstChild("HumanoidRootPart")

-- Variáveis de controle de execução segura
local stealthMode = true        -- Ativa ações mais lentas e pausas aleatórias
local maxFarmTime = 120         -- Tempo máximo de farm contínuo (segundos)
local farmStartTime = 0
local shouldStopFarm = false

-- Interface simples e direta (sem elementos chamativos)
local screenGui = Instance.new("ScreenGui", player.PlayerGui)
screenGui.Name = "HubFurtivo"

local main = Instance.new("Frame", screenGui)
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
title.Text = "Rakoof Hub (Furtivo)"
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
close.MouseButton1Click:Connect(function() 
    shouldStopFarm = true
    screenGui:Destroy() 
end)

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

-- ===========================================================================
-- LÓGICA DO JOGO COM ATRASOS E SEGURANÇA
-- ===========================================================================
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

-- Auto Farm com limites de tempo e pausas aleatórias
addToggle("⚡ Auto Farm (Limitado)", function(v)
    if v then
        farmStartTime = tick()
        shouldStopFarm = false
        spawn(function()
            while v and not shouldStopFarm do
                -- Verifica tempo máximo para não ficar farmando eternamente
                if stealthMode and (tick() - farmStartTime > maxFarmTime) then
                    print("Auto Farm pausado por segurança. Reative se desejar.")
                    break
                end
                
                local boss = getBoss()
                if boss and weaponEvent then
                    pcall(function()
                        weaponEvent:FireServer("Swing", boss)
                    end)
                end
                
                -- Intervalo base mais longo
                local waitTime = stealthMode and 0.8 or 0.4
                wait(waitTime)
                
                -- Pausa aleatória para simular jogador humano
                if stealthMode and math.random(1, 100) <= 15 then
                    wait(math.random(15, 30) / 10) -- 1.5 a 3 segundos
                end
            end
        end)
    else
        shouldStopFarm = true
    end
end)

addToggle("🛡️ God Mode", function(v)
    spawn(function()
        while v do
            local c = player.Character
            if c and c:FindFirstChildOfClass("Humanoid") then
                c.Humanoid.Health = c.Humanoid.MaxHealth
            end
            wait(0.2)
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
            wait(0.2)
        end
    end)
end)

addButton("🔪 Equipar Melhor", equipBest)

addButton("🎯 Matar Rakoof", function()
    local boss = getBoss()
    if boss then boss.Humanoid.Health = 0 end
end)

addButton("🚀 Teleporte Seguro", function()
    local c = player.Character
    if c and c:FindFirstChild("HumanoidRootPart") then
        c.HumanoidRootPart.CFrame = CFrame.new(0, 30, 0)
    end
end)

addButton("⏹️ Parar Tudo e Limpar", function()
    shouldStopFarm = true
    -- Desativa todas as corrotinas ativas (simples)
    screenGui:Destroy()
    print("Script finalizado e rastros minimizados.")
end)

print("✅ Script Furtivo carregado. Use com moderação.")
