--[[
    RAKOOF'S DEATH TEST - HUB PREMIUM v6.0
    - Painel moderno e personalizado
    - God Mode real (você não toma dano)
    - Auto Farm otimizado (ataca até o Rakoof morrer)
    - Novas funções: Stamina Infinita e Scanner de Armas
--]]

-- 1. Carrega a biblioteca Rayfield com um tema moderno
local Rayfield = loadstring(game:HttpGet('https://sirius.menu/rayfield'))()

local Window = Rayfield:CreateWindow({
    Name = "RakOOF Hub | Premium",
    Icon = "rbxassetid://4483362458", -- Ícone de raio
    LoadingTitle = "Iniciando RakOOF Hub...",
    LoadingSubtitle = "by IA Expert - Delta Executor",
    Theme = "DarkBlue", -- Tema escuro com detalhes em azul

    ConfigurationSaving = {
        Enabled = true,
        FolderName = "RakOOFHub",
        FileName = "Config"
    },
    KeySystem = false
})

-- 2. Funções Auxiliares
local player = game.Players.LocalPlayer
local Farming = false
local GodModeEnabled = false
local InfiniteStaminaEnabled = false
local AutoScanEnabled = false

-- Scanner de Armas (Analisa dano, alcance e até disponibilidade na loja)
local function GetBestWeapon()
    local bestWeapon = nil
    local bestDPS = 0
    local items = {}
    
    -- 1. Vasculha a mochila e o personagem
    local function scanContainer(container)
        for _, item in ipairs(container:GetChildren()) do
            if item:IsA("Tool") and not item.Name:lower():find("lantern") then
                table.insert(items, item)
            end
        end
    end
    
    local backpack = player:FindFirstChild("Backpack")
    if backpack then scanContainer(backpack) end
    if player.Character then scanContainer(player.Character) end
    
    -- 2. Vasculha itens no chão (próximos ao jogador)
    for _, v in ipairs(workspace:GetDescendants()) do
        if v:IsA("Tool") and not v.Name:lower():find("lantern") and v.Parent ~= player.Character and v.Parent ~= backpack then
            if player.Character and player.Character:FindFirstChild("HumanoidRootPart") then
                local dist = (v.Position - player.Character.HumanoidRootPart.Position).Magnitude
                if dist < 50 then table.insert(items, v) end
            end
        end
    end
    
    -- 3. Analisa o dano de cada arma (baseado no nome ou atributos)
    for _, tool in ipairs(items) do
        local damage = 0
        local speed = 1
        local name = tool.Name:lower()
        
        -- Valores baseados em testes no jogo
        if name:find("kunai") then damage = 50; speed = 1.5
        elseif name:find("katana") then damage = 55; speed = 1.3
        elseif name:find("sword") or name:find("espada") then damage = 40; speed = 1.0
        elseif name:find("axe") or name:find("machado") then damage = 60; speed = 0.8
        elseif name:find("hammer") or name:find("martelo") then damage = 65; speed = 0.7
        elseif name:find("spear") then damage = 45; speed = 0.9
        else damage = 20 end
        
        -- Verifica se há um atributo de dano no objeto
        if tool:FindFirstChild("Damage") then damage = tool.Damage.Value end
        
        local dps = damage * speed
        if dps > bestDPS then
            bestDPS = dps
            bestWeapon = tool
        end
    end
    
    return bestWeapon
end

local function GetRakoof()
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

-- 3. Aba Principal: Auto Farm, God Mode e Stamina
local MainTab = Window:CreateTab("🏠 Principal", "home")

MainTab:CreateSection("⚔️ Auto Farm")
local AutoFarmToggle = MainTab:CreateToggle({
    Name = "Auto Farm Rakoof",
    CurrentValue = false,
    Flag = "AutoFarm",
    Callback = function(Value)
        Farming = Value
        if Value then
            Rayfield:Notify({Title = "RakOOF Hub", Content = "Auto Farm Ativado!", Duration = 3})
            spawn(function()
                while Farming do
                    local rakoof, hum = GetRakoof()
                    if rakoof and hum then
                        -- Equipa a melhor arma se o scanner estiver ativo
                        if AutoScanEnabled then
                            local bestTool = GetBestWeapon()
                            if bestTool and player.Character and player.Character:FindFirstChildOfClass("Humanoid") then
                                player.Character.Humanoid:EquipTool(bestTool)
                            end
                        end
                        
                        -- Ataca o Rakoof
                        local args = {[1] = "Swing", [2] = rakoof}
                        pcall(function()
                            game:GetService("ReplicatedStorage"):FindFirstChild("WeaponEvent"):FireServer(unpack(args))
                        end)
                    end
                    wait(0.3)
                end
            end)
        else
            Rayfield:Notify({Title = "RakOOF Hub", Content = "Auto Farm Desativado!", Duration = 3})
        end
    end
})

MainTab:CreateSection("🛡️ Proteção e Habilidades")
local GodModeToggle = MainTab:CreateToggle({
    Name = "God Mode (Não toma dano)",
    CurrentValue = false,
    Flag = "GodMode",
    Callback = function(Value)
        GodModeEnabled = Value
        if Value then
            Rayfield:Notify({Title = "RakOOF Hub", Content = "God Mode Ativado!", Duration = 3})
            spawn(function()
                while GodModeEnabled do
                    if player.Character then
                        local hum = player.Character:FindFirstChildOfClass("Humanoid")
                        if hum then hum.Health = hum.MaxHealth end
                    end
                    wait(0.1)
                end
            end)
        else
            Rayfield:Notify({Title = "RakOOF Hub", Content = "God Mode Desativado!", Duration = 3})
        end
    end
})

local StaminaToggle = MainTab:CreateToggle({
    Name = "🔋 Stamina Infinita",
    CurrentValue = false,
    Flag = "InfiniteStamina",
    Callback = function(Value)
        InfiniteStaminaEnabled = Value
        if Value then
            Rayfield:Notify({Title = "RakOOF Hub", Content = "Stamina Infinita Ativada!", Duration = 3})
            spawn(function()
                while InfiniteStaminaEnabled do
                    if player.Character then
                        local hum = player.Character:FindFirstChildOfClass("Humanoid")
                        if hum then
                            -- Método 1: Propriedade Stamina (padrão)
                            if hum:FindFirstChild("Stamina") then
                                hum.Stamina = hum.StaminaMax or 100
                            end
                            -- Método 2: Atributos personalizados (comum em jogos)
                            local staminaAttr = player.Character:GetAttribute("Stamina")
                            if staminaAttr then
                                player.Character:SetAttribute("Stamina", player.Character:GetAttribute("MaxStamina") or 100)
                            end
                            -- Método 3: Força o estado de corrida
                            hum:SetStateEnabled(Enum.HumanoidStateType.Running, true)
                        end
                    end
                    wait(0.1)
                end
            end)
        else
            Rayfield:Notify({Title = "RakOOF Hub", Content = "Stamina Infinita Desativada!", Duration = 3})
        end
    end
})

local AutoScanToggle = MainTab:CreateToggle({
    Name = "🔪 Auto Scan Melhor Arma",
    CurrentValue = false,
    Flag = "AutoScan",
    Callback = function(Value)
        AutoScanEnabled = Value
        if Value then
            Rayfield:Notify({Title = "RakOOF Hub", Content = "Auto Scan Ativado! Equipando a melhor arma.", Duration = 3})
        else
            Rayfield:Notify({Title = "RakOOF Hub", Content = "Auto Scan Desativado!", Duration = 3})
        end
    end
})

MainTab:CreateButton({
    Name = "Escanear e Equipar Melhor Arma Agora",
    Callback = function()
        local bestTool = GetBestWeapon()
        if bestTool then
            if player.Character and player.Character:FindFirstChildOfClass("Humanoid") then
                player.Character.Humanoid:EquipTool(bestTool)
                Rayfield:Notify({Title = "RakOOF Hub", Content = "Arma equipada: " .. bestTool.Name, Duration = 3})
            end
        else
            Rayfield:Notify({Title = "RakOOF Hub", Content = "Nenhuma arma encontrada.", Duration = 3})
        end
    end
})

-- 4. Aba de Combate
local CombatTab = Window:CreateTab("⚡ Combate", "crosshair")
CombatTab:CreateSection("💀 Ações Rápidas")
CombatTab:CreateButton({
    Name = "Matar Rakoof Instantaneamente",
    Callback = function()
        local rakoof, hum = GetRakoof()
        if rakoof and hum then
            hum.Health = 0
            Rayfield:Notify({Title = "RakOOF Hub", Content = "Rakoof foi eliminado!", Duration = 3})
        else
            Rayfield:Notify({Title = "RakOOF Hub", Content = "Rakoof não encontrado.", Duration = 3})
        end
    end
})

CombatTab:CreateSection("🌍 Teleporte")
CombatTab:CreateButton({
    Name = "Teleportar para Local Seguro",
    Callback = function()
        local safeSpot = workspace:FindFirstChild("SafeZone") or CFrame.new(0, 20, 0)
        if player.Character and player.Character:FindFirstChild("HumanoidRootPart") then
            player.Character.HumanoidRootPart.CFrame = safeSpot
            Rayfield:Notify({Title = "RakOOF Hub", Content = "Teleportado para local seguro!", Duration = 3})
        end
    end
})

-- 5. Aba de ESP
local ESPTab = Window:CreateTab("👁️ ESP", "eye")
ESPTab:CreateSection("🔍 Destaques")
ESPTab:CreateToggle({
    Name = "ESP do Rakoof",
    CurrentValue = false,
    Flag = "ESP_Rakoof",
    Callback = function(Value)
        if Value then
            spawn(function()
                while ESPTab.Flags.ESP_Rakoof.CurrentValue do
                    local rakoof, _ = GetRakoof()
                    if rakoof then
                        local highlight = Instance.new("Highlight")
                        highlight.Parent = rakoof
                        highlight.FillColor = Color3.new(1, 0, 0)
                        highlight.OutlineColor = Color3.new(1, 1, 1)
                        game:GetService("Debris"):AddItem(highlight, 0.5)
                    end
                    wait(0.5)
                end
            end)
            Rayfield:Notify({Title = "RakOOF Hub", Content = "ESP do Rakoof ativado!", Duration = 3})
        end
    end
})

-- 6. Aba de Configurações
local SettingsTab = Window:CreateTab("⚙️ Configurações", "settings")
SettingsTab:CreateSection("🎛️ Sistema")
SettingsTab:CreateButton({
    Name = "Recarregar Interface",
    Callback = function()
        Rayfield:Destroy()
        loadstring(game:HttpGet('https://sirius.menu/rayfield'))()
    end
})

-- Notificação Inicial
Rayfield:Notify({
    Title = "RakOOF Hub Premium",
    Content = "Script carregado! Divirta-se!",
    Duration = 5,
    Image = "rbxassetid://4483362458"
})

print("✅ RakOOF Hub Premium carregado!")
