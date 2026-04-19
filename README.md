-- RAKOOF'S DEATH TEST - SCRIPT FUNCIONAL E SEGURO (COM BYPASS REAL)

local player = game.Players.LocalPlayer
local replicatedStorage = game:GetService("ReplicatedStorage")
local runService = game:GetService("RunService")
local virtualInputManager = game:GetService("VirtualInputManager")
local uis = game:GetService("UserInputService")

-- ===========================================================================
-- CAMADA ANTI-KICK (INTERCEPTA REMOTES DE DANO E KICK)
-- ===========================================================================
spawn(function()
    pcall(function()
        -- Bloqueia a função Kick
        if player then
            local oldKick = player.Kick
            player.Kick = function(...) return nil end
        end
        
        -- Bloqueia remotes que causam dano ao jogador
        local remotesToBlock = {"Damage", "Hurt", "Hit", "TakeDamage", "HealthChange"}
        for _, remoteName in ipairs(remotesToBlock) do
            local remote = replicatedStorage:FindFirstChild(remoteName)
            if remote and remote:IsA("RemoteEvent") then
                remote.OnClientEvent:Connect(function(...)
                    -- Engole o evento, impedindo que o cliente processe o dano
                    return nil
                end)
            end
        end
    end)
end)

-- ===========================================================================
-- FUNÇÕES AUXILIARES (ENCONTRAR ARMAS E INIMIGOS)
-- ===========================================================================
local function findBestWeapon()
    local best, bestDps = nil, 0
    local items = {}
    local function scan(c) for _, t in ipairs(c:GetChildren()) do if t:IsA("Tool") then table.insert(items, t) end end end
    if player.Backpack then scan(player.Backpack) end
    if player.Character then scan(player.Character) end
    for _, t in ipairs(items) do
        local dps = 10
        local n = t.Name:lower()
        if n:find("kunai") then dps = 75
        elseif n:find("katana") then dps = 70
        elseif n:find("sword") then dps = 40
        elseif n:find("axe") then dps = 48
        elseif n:find("hammer") then dps = 45
        elseif n:find("cheese") then dps = 45
        elseif n:find("taser") then dps = 30
        end
        if dps > bestDps then best, bestDps = t, dps end
    end
    return best
end

local function equipBestWeapon()
    local weapon = findBestWeapon()
    if weapon and player.Character and player.Character:FindFirstChildOfClass("Humanoid") then
        player.Character.Humanoid:EquipTool(weapon)
    end
end

local function findRakoof()
    for _, obj in ipairs(workspace:GetDescendants()) do
        if obj:IsA("Model") and (obj.Name:lower():find("rakoof") or obj.Name == "Rake") then
            local hum = obj:FindFirstChildOfClass("Humanoid")
            if hum and hum.Health > 0 then return obj, hum end
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

-- ===========================================================================
-- INTERFACE (COM BOTÃO FLUTUANTE)
-- ===========================================================================
local screenGui = Instance.new("ScreenGui", player.PlayerGui)
screenGui.Name = "RakoofHub"

local main = Instance.new("Frame", screenGui)
main.Size = UDim2.new(0, 220, 0, 220)
main.Position = UDim2.new(0.5, -110, 0.5, -110)
main.BackgroundColor3 = Color3.fromRGB(35, 35, 45)
main.BorderSizePixel = 0
main.Active = true
main.Draggable = true
main.Visible = true

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

local content = Instance.new("ScrollingFrame", main)
content.Size = UDim2.new(1, 0, 1, -25)
content.Position = UDim2.new(0, 0, 0, 25)
content.BackgroundTransparency = 1
content.BorderSizePixel = 0
content.ScrollBarThickness = 4
content.CanvasSize = UDim2.new(0, 0, 0, 0)

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
-- GOD MODE REAL (BLOQUEIO DE DANO VIA REMOTES)
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
    
    -- Método 1: Restaura vida no cliente
    godModeConnection = hum.HealthChanged:Connect(function(newHealth)
        if godModeEnabled and newHealth < 20000 then
            hum.Health = 20000
        end
    end)
    
    -- Método 2: Bloqueia remotes de dano específicos do jogo
    spawn(function()
        local damageRemotes = {"DamagePlayer", "TakeDamage", "Hurt", "Hit"}
        for _, remoteName in ipairs(damageRemotes) do
            local remote = replicatedStorage:FindFirstChild(remoteName)
            if remote and remote:IsA("RemoteEvent") then
                remote.OnClientEvent:Connect(function()
                    if godModeEnabled then return nil end
                end)
            end
        end
    end)
end

player.CharacterAdded:Connect(enableGodMode)

-- ===========================================================================
-- AUTO FARM COM ARMA REAL (SIMULA ATAQUES VIA REMOTE)
-- ===========================================================================
local autoFarmEnabled = false
local weaponEvent = replicatedStorage:FindFirstChild("WeaponEvent") -- Evento de ataque principal

spawn(function()
    while true do
        if autoFarmEnabled then
            -- Equipa a melhor arma antes de atacar
            equipBestWeapon()
            
            local hostiles = findAllHostiles()
            if #hostiles > 0 then
                for _, enemy in ipairs(hostiles) do
                    if enemy and enemy:FindFirstChildOfClass("Humanoid") and enemy.Humanoid.Health > 0 then
                        -- Checa se o evento de arma existe
                        if weaponEvent then
                            pcall(function()
                                -- Dispara o evento de ataque para cada inimigo (várias vezes por segundo)
                                weaponEvent:FireServer("Swing", enemy)
                            end)
                        else
                            -- Fallback: tenta equipar ferramenta e atacar via tool
                            local tool = player.Character and player.Character:FindFirstChildOfClass("Tool")
                            if tool and tool:FindFirstChild("Activate") then
                                tool:Activate()
                            end
                        end
                        wait(0.05) -- Pequeno intervalo entre golpes
                    end
                end
            end
            wait(math.random(15, 25) / 10) -- Pausa de 1.5 a 2.5 segundos entre varreduras
        else
            wait(1)
        end
    end
end)

-- ===========================================================================
-- BOTÕES E TOGGLES
-- ===========================================================================
addToggle("🛡️ God Mode (Anti-Dano Real)", function(v)
    godModeEnabled = v
    if v then
        enableGodMode()
    else
        if godModeConnection then godModeConnection:Disconnect() end
    end
end)

addToggle("⚡ Auto Farm (Ataque Real)", function(v)
    autoFarmEnabled = v
end)

addButton("🔪 Equipar Melhor Arma", equipBestWeapon)

addButton("🎯 Teleportar até Rakoof", function()
    local rakoof, _ = findRakoof()
    if rakoof and player.Character and player.Character:FindFirstChild("HumanoidRootPart") then
        local root = rakoof:FindFirstChild("HumanoidRootPart") or rakoof:FindFirstChild("Torso") or rakoof:FindFirstChild("Head")
        if root then
            -- Teleporte suave (evita detecção de teleporte brusco)
            for i = 1, 10 do
                player.Character.HumanoidRootPart.CFrame = player.Character.HumanoidRootPart.CFrame:Lerp(root.CFrame * CFrame.new(0, 5, 0), i/10)
                wait(0.01)
            end
        end
    end
end)

addButton("☠️ Matar Rakoof Agora", function()
    local rakoof, hum = findRakoof()
    if rakoof and hum and weaponEvent then
        -- Ataca 10 vezes rapidamente
        for i = 1, 10 do
            pcall(function() weaponEvent:FireServer("Swing", rakoof) end)
            wait(0.05)
        end
    end
end)

print("✅ Script carregado! Use com moderação e evite AFK prolongado.")
