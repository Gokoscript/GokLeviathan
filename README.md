-- Blox Fruits Leviathan Auto-Hunt Script (Simples e Educativo)
-- GUI + funções básicas | by ChatGPT

-- GUI Setup
local ScreenGui = Instance.new("ScreenGui")
local Frame = Instance.new("Frame")
local FindBtn = Instance.new("TextButton")
local AttackBtn = Instance.new("TextButton")
local LeviathanFound = false
local AutoAttack = false

-- Função para localizar o Leviathan (simulação)
function FindLeviathan()
    for _, v in pairs(game.Workspace:GetDescendants()) do
        if v.Name:lower():find("leviathan") then
            LeviathanFound = true
            return v
        end
    end
    LeviathanFound = false
    return nil
end

-- Função de ataque às caudas
function AttackLeviathan()
    while AutoAttack do
        local boss = FindLeviathan()
        if boss and boss:FindFirstChild("Tail") then
            local player = game.Players.LocalPlayer
            local char = player.Character
            if char and char:FindFirstChild("HumanoidRootPart") then
                char.HumanoidRootPart.CFrame = boss.Tail.CFrame * CFrame.new(0, 0, 5)
                -- Simula ataque (pressiona tecla Z por exemplo)
                keypress(0x5A)
                wait(0.1)
                keyrelease(0x5A)
            end
        end
        wait(1)
    end
end

-- GUI Appearance
ScreenGui.Name = "LeviathanHuntGui"
ScreenGui.Parent = game.CoreGui
Frame.Size = UDim2.new(0, 250, 0, 150)
Frame.Position = UDim2.new(0, 100, 0, 100)
Frame.BackgroundColor3 = Color3.fromRGB(35, 35, 35)
Frame.Parent = ScreenGui

FindBtn.Size = UDim2.new(0, 230, 0, 40)
FindBtn.Position = UDim2.new(0, 10, 0, 20)
FindBtn.Text = "🔍 Procurar Leviathan"
FindBtn.BackgroundColor3 = Color3.fromRGB(70, 70, 70)
FindBtn.TextColor3 = Color3.new(1, 1, 1)
FindBtn.Parent = Frame

AttackBtn.Size = UDim2.new(0, 230, 0, 40)
AttackBtn.Position = UDim2.new(0, 10, 0, 80)
AttackBtn.Text = "⚔️ Atacar Leviathan"
AttackBtn.BackgroundColor3 = Color3.fromRGB(120, 70, 70)
AttackBtn.TextColor3 = Color3.new(1, 1, 1)
AttackBtn.Parent = Frame

-- Botões
FindBtn.MouseButton1Click:Connect(function()
    local boss = FindLeviathan()
    if boss then
        FindBtn.Text = "✅ Leviathan Encontrado!"
    else
        FindBtn.Text = "❌ Não encontrado"
    end
end)

AttackBtn.MouseButton1Click:Connect(function()
    AutoAttack = not AutoAttack
    if AutoAttack then
        AttackBtn.Text = "🛑 Parar Ataque"
        AttackLeviathan()
    else
        AttackBtn.Text = "⚔️ Atacar Leviathan"
    end
end)
