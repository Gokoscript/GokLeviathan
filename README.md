-- GokLeviathan | Script Avançado de Caça ao Leviathan | by ChatGPT

-- Variáveis
local LeviathanFound = false
local AutoAttack = false
local AutoSpy = false
local BoatMoving = false

local Players = game:GetService("Players")
local ReplicatedStorage = game:GetService("ReplicatedStorage")
local player = Players.LocalPlayer

-- GUI Setup
local ScreenGui = Instance.new("ScreenGui")
local Frame = Instance.new("Frame")
local FindBtn = Instance.new("TextButton")
local AttackBtn = Instance.new("TextButton")
local SpyBtn = Instance.new("TextButton")
local TravelBtn = Instance.new("TextButton")

ScreenGui.Name = "GokLeviathanGui"
ScreenGui.Parent = game.CoreGui

Frame.Size = UDim2.new(0, 280, 0, 220)
Frame.Position = UDim2.new(0, 100, 0, 100)
Frame.BackgroundColor3 = Color3.fromRGB(30, 30, 30)
Frame.Parent = ScreenGui

local function criarBotao(texto, y, cor)
    local btn = Instance.new("TextButton")
    btn.Size = UDim2.new(0, 250, 0, 40)
    btn.Position = UDim2.new(0, 15, 0, y)
    btn.Text = texto
    btn.BackgroundColor3 = cor or Color3.fromRGB(70, 70, 70)
    btn.TextColor3 = Color3.new(1, 1, 1)
    btn.Parent = Frame
    return btn
end

-- Funções

function SubornarSpy()
    for _, npc in pairs(workspace:GetDescendants()) do
        if npc:IsA("Model") and npc.Name:lower():find("spy") and npc:FindFirstChild("Head") then
            local detector = npc.Head:FindFirstChildOfClass("ClickDetector")
            if detector then
                fireclickdetector(detector)
                wait(1)
                print("[GokLeviathan] Subornando o Spy...")
                local args = {
                    [1] = "Spy",
                    [2] = "Leviathan"
                }
                ReplicatedStorage.Remotes:FindFirstChild("Subornar"):FireServer(unpack(args))
                break
            end
        end
    end
end

function ViajarParaSea6()
    print("[GokLeviathan] Indo para o Sea 6...")
    -- Você pode substituir isso por teleporte se estiver usando script loader
    if workspace:FindFirstChild("Boats") then
        local barco = workspace.Boats:FindFirstChild(player.Name)
        if barco and barco:FindFirstChild("VehicleSeat") then
            BoatMoving = true
            barco.VehicleSeat.Throttle = 1
            print("[GokLeviathan] Barco em movimento...")
        end
    end
end

function PararBarco()
    if workspace:FindFirstChild("Boats") then
        local barco = workspace.Boats:FindFirstChild(player.Name)
        if barco and barco:FindFirstChild("VehicleSeat") then
            barco.VehicleSeat.Throttle = 0
            print("[GokLeviathan] Barco parado em frente à ilha!")
        end
    end
end

function DetectarIlhaLeviathan()
    while true do
        wait(10)
        for _, obj in pairs(workspace:GetChildren()) do
            if obj.Name == "LeviathanIsland" then
                print("[GokLeviathan] Ilha do Leviathan localizada!")
                if BoatMoving then
                    wait(2)
                    PararBarco()
                end
                return
            end
        end
    end
end

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

function AttackLeviathan()
    while AutoAttack do
        local boss = FindLeviathan()
        if boss and boss:FindFirstChild("Tail") then
            local char = player.Character
            if char and char:FindFirstChild("HumanoidRootPart") then
                char.HumanoidRootPart.CFrame = boss.Tail.CFrame * CFrame.new(0, 0, 5)
                -- Simula ataque (tecla Z)
                keypress(0x5A)
                wait(0.1)
                keyrelease(0x5A)
            end
        end
        wait(1)
    end
end

-- Criar botões
FindBtn = criarBotao("🔍 Procurar Leviathan", 20)
AttackBtn = criarBotao("⚔️ Atacar Leviathan", 70, Color3.fromRGB(120, 70, 70))
SpyBtn = criarBotao("🕵️ Subornar Spy", 120, Color3.fromRGB(80, 80, 120))
TravelBtn = criarBotao("🚢 Ir para o Sea 6", 170, Color3.fromRGB(60, 100, 120))

-- Conectar eventos
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

SpyBtn.MouseButton1Click:Connect(function()
    SubornarSpy()
    SpyBtn.Text = "✅ Spy Subornado"
end)

TravelBtn.MouseButton1Click:Connect(function()
    ViajarParaSea6()
    task.spawn(DetectarIlhaLeviathan)
end)
