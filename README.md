-- Teste Rápido de Diagnóstico
local p = game.Players.LocalPlayer
print("1. Jogador: " .. p.Name)

local sg = Instance.new("ScreenGui")
sg.Name = "TesteDelta"
sg.Parent = p:WaitForChild("PlayerGui")

local f = Instance.new("Frame", sg)
f.Size = UDim2.new(0, 200, 0, 200)
f.Position = UDim2.new(0.5, -100, 0.5, -100)
f.BackgroundColor3 = Color3.fromRGB(255, 0, 0)

local t = Instance.new("TextLabel", f)
t.Size = UDim2.new(1, 0, 1, 0)
t.Text = "Se você está vendo isso, o Delta funciona!"
t.TextColor3 = Color3.fromRGB(255, 255, 255)
t.TextScaled = true

print("2. Tela criada. Deveria ter um quadrado vermelho na sua frente.")
