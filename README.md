--[[
    RAKOOF'S DEATH TEST - HUB PREMIUM v3.0 (Corrigido)
    - Compatível com Delta Executor (PC e Mobile)
    - Inclui modo de segurança para evitar detecção
    - URL da UI atualizada
]]

-- === CORREÇÃO 1: Ativa o Modo Seguro da Rayfield (anti-crash/detecção) ===
getgenv().SecureMode = true

-- === CORREÇÃO 2: Usa uma URL alternativa e mais estável da Rayfield ===
local Rayfield = loadstring(game:HttpGet('https://raw.githubusercontent.com/shlexware/Rayfield/main/source'))()
-- Caso a de cima falhe, tenta a original
if not Rayfield then
    Rayfield = loadstring(game:HttpGet('https://sirius.menu/rayfield'))()
end

-- Verifica se a biblioteca carregou, se não, para o script.
if not Rayfield then
    warn("Rayfield failed to load - script stopping.")
    return
end

-- 2. Cria a janela principal
local Window = Rayfield:CreateWindow({
    Name = "Rakoof Hub | Premium+",
    LoadingTitle = "Iniciando Rakoof Hub...",
    LoadingSubtitle = "by IA Expert - Delta Executor",
    ConfigurationSaving = {
        Enabled = true,
        FolderName = "RakoofHub",
        FileName = "Config"
    },
    KeySystem = false
})

-- ========== CONFIGURAÇÃO PARA CELULAR (BOTÃO FLUTUANTE) ==========
Window:SetMinimizeButton({
    Image = "rbxassetid://4483362458",   -- Ícone de seta
    Size = {35, 35},                     -- Tamanho para toque fácil
    Color = Color3.fromRGB(255, 255, 255),
    CornerRadius = UDim.new(0, 8)
})
Window:ToggleKeybind(Enum.KeyCode.F4)    -- Necessário para ativar o botão flutuante
Window:MakeDraggable(true)               -- Permite arrastar o botão na tela
-- =================================================================

-- Variáveis de controle do farm
local Farming = false
local TransformDetected = false
local PauseFarming = false
local TargetFriendName = ""
local KillThreshold = 5

-- Função para localizar o Rakoof (chefe)
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

-- Função para localizar os Scratchers (bichos pequenos)
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

-- 3. Aba Principal (Auto-Farm)
local MainTab = Window:CreateTab("⚡ Auto-Farm Premium", 4483362458)

MainTab:CreateSection("🎯 Configuração de Ajuda")

local FriendFarmInput = MainTab:CreateInput({
    Name = "Nome do Amigo",
    PlaceholderText = "Ex: joaozinho123",
    Flag = "FriendName"
})

local KillThresholdInput = MainTab:CreateInput({
    Name = "Kills necessárias para matar o boss",
    PlaceholderText = "Ex: 10",
    Flag = "KillThreshold"
})

MainTab:CreateSection("⚙️ Controle do Farm")

local AutoFarmToggle = MainTab:CreateToggle({
    Name = "Iniciar Farm Automático",
    CurrentValue = false,
    Flag = "AutoFarm",
    Callback = function(Value)
        Farming = Value
        if Value then
            TargetFriendName = FriendFarmInput.CurrentValue or ""
            KillThreshold = tonumber(KillThresholdInput.CurrentValue) or 5

            Rayfield:Notify({
                Title = "Rakoof Hub",
                Content = "Farm ativado! Ajudando "..TargetFriendName.." até "..KillThreshold.." kills.",
                Duration = 3,
                Image = 4483362458
            })

            -- Thread principal do farm
            spawn(function()
                while Farming do
                    if PauseFarming then
                        wait(1)
                        continue
                    end

                    local player = game.Players.LocalPlayer
                    local character = player.Character or player.CharacterAdded:Wait()

                    -- 1. Equipa a melhor arma (Kunai ou espada)
                    local tool = character:FindFirstChild("Kunai") or character:FindFirstChild("Sword") or character:FindFirstChildOfClass("Tool")
                    if tool then
                        player.Character.Humanoid:EquipTool(tool)
                    end

                    -- 2. Verifica se o amigo já bateu a meta de kills
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

                    -- 3. Ataca o alvo correto
                    if shouldAttackBoss then
                        local boss, hum = GetBoss()
                        if boss and hum then
                            -- Simula ataque com arma
                            local args = {
                                [1] = "Swing",
                                [2] = boss
                            }
                            pcall(function()
                                game:GetService("ReplicatedStorage"):FindFirstChild("WeaponEvent"):FireServer(unpack(args))
                            end)
                        end
                    else
                        local scratcher, humS = GetScratcher()
                        if scratcher and humS then
                            local args = {
                                [1] = "Swing",
                                [2] = scratcher
                            }
                            pcall(function()
                                game:GetService("ReplicatedStorage"):FindFirstChild("WeaponEvent"):FireServer(unpack(args))
                            end)
                        end
                    end

                    wait(0.3) -- Intervalo entre ataques
                end
            end)
        else
            Rayfield:Notify({
                Title = "Rakoof Hub",
                Content = "Farm automático desativado.",
                Duration = 3,
                Image = 4483362458
            })
        end
    end
})

-- 4. Monitor de Transformação (Thread separada)
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
                        Rayfield:Notify({
                            Title = "⚠️ Transformação",
                            Content = "Rakoof está se transformando! Pausando ataques...",
                            Duration = 3,
                            Image = 4483362458
                        })
                    end
                else
                    if TransformDetected then
                        TransformDetected = false
                        PauseFarming = false
                        Rayfield:Notify({
                            Title = "✅ Retomando",
                            Content = "Transformação finalizada! Voltando a atacar.",
                            Duration = 3,
                            Image = 4483362458
                        })
                    end
                end
            end
        end
        wait(0.5)
    end
end)

-- 5. Aba de Utilitários
local UtilTab = Window:CreateTab("🛠️ Utilitários", 4483362458)

UtilTab:CreateSection("🎒 Armas")
UtilTab:CreateButton({
    Name = "Equipar Kunai (Arma Forte)",
    Callback = function()
        local player = game.Players.LocalPlayer
        local character = player.Character or player.CharacterAdded:Wait()
        local tool = character:FindFirstChild("Kunai") or character:FindFirstChild("Sword")
        if tool then
            player.Character.Humanoid:EquipTool(tool)
            Rayfield:Notify({
                Title = "Arma",
                Content = "Kunai equipada!",
                Duration = 3,
                Image = 4483362458
            })
        else
            Rayfield:Notify({
                Title = "Erro",
                Content = "Você não possui Kunai ou Espada.",
                Duration = 3,
                Image = 4483362458
            })
        end
    end
})

-- 6. Aba de Configurações
local ConfigTab = Window:CreateTab("⚙️ Configurações", 4483362458)

ConfigTab:CreateSection("🎛️ Sistema")
ConfigTab:CreateButton({
    Name = "Recarregar Interface",
    Callback = function()
        Rayfield:Destroy()
        loadstring(game:HttpGet('https://sirius.menu/rayfield'))()
    end
})

ConfigTab:CreateLabel("Dica: Use o botão flutuante para esconder/mostrar o menu no celular.", 4483362458)

-- 7. Notificação de boas-vindas
Rayfield:Notify({
    Title = "Rakoof Hub Premium+",
    Content = "Script carregado! Configure o nome do amigo e as kills para iniciar o farm.",
    Duration = 5,
    Image = 4483362458
})
