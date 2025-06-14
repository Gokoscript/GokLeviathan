-- 📌 Function to create buttons inside tabs
local function CreateButton(tab, text, callback)
    local Button = Instance.new("TextButton")
    Button.Size = UDim2.new(0, 200, 0, 35)
    Button.Position = UDim2.new(0, 10, 0, (#tab:GetChildren() * 40))
    Button.BackgroundColor3 = Color3.fromRGB(60, 60, 60)
    Button.TextColor3 = Color3.new(1, 1, 1)
    Button.Text = text
    Button.Font = Enum.Font.Gotham
    Button.TextSize = 14
    Button.Parent = tab

    Button.MouseButton1Click:Connect(callback)

    return Button
end

-- 📌 Function to create sliders
local function CreateSlider(tab, text, min, max, default, callback)
    local Label = Instance.new("TextLabel")
    Label.Size = UDim2.new(0, 200, 0, 20)
    Label.Position = UDim2.new(0, 10, 0, (#tab:GetChildren() * 40))
    Label.BackgroundTransparency = 1
    Label.Text = text .. ": " .. tostring(default)
    Label.TextColor3 = Color3.new(1,1,1)
    Label.Font = Enum.Font.Gotham
    Label.TextSize = 14
    Label.Parent = tab

    local Slider = Instance.new("TextButton")
    Slider.Size = UDim2.new(0, 200, 0, 20)
    Slider.Position = Label.Position + UDim2.new(0, 0, 0, 20)
    Slider.BackgroundColor3 = Color3.fromRGB(70,70,70)
    Slider.Text = "Change"
    Slider.TextColor3 = Color3.new(1,1,1)
    Slider.Font = Enum.Font.Gotham
    Slider.TextSize = 12
    Slider.Parent = tab

    Slider.MouseButton1Click:Connect(function()
        local input = tonumber(game:GetService("Players").LocalPlayer.PlayerGui:Prompt("Set "..text, tostring(default)))
        if input and input >= min and input <= max then
            callback(input)
            Label.Text = text .. ": " .. tostring(input)
        end
    end)
end

----------------------------------------------------------------
-- 🔥 MAIN TAB BUTTONS 🔥

-- ✅ Find Leviathan
CreateButton(MainTab, "🔍 Find Leviathan", function()
    local found = false
    for _, v in pairs(workspace:GetDescendants()) do
        if v.Name:lower():find("leviathan") then
            found = true
            player.Character.HumanoidRootPart.CFrame = v.Tail.CFrame + Vector3.new(0, 5, 0)
            print("[GokLeviathan] Leviathan Found and Moved to it!")
            break
        end
    end
    if not found then
        print("[GokLeviathan] Leviathan not found.")
    end
end)

-- ✅ Travel to Sea 6 (Boat Fly)
CreateButton(MainTab, "🚢 Travel to Sea 6", function()
    if workspace:FindFirstChild("Boats") then
        local boat = workspace.Boats:FindFirstChild(player.Name)
        if boat and boat:FindFirstChild("VehicleSeat") then
            BoatMoving = true
            while BoatMoving do
                boat:SetPrimaryPartCFrame(boat.PrimaryPart.CFrame + Vector3.new(0, 1.5, 5)) -- Smooth fly forward
                task.wait(0.05)
            end
        end
    end
end)

-- ✅ Stop Boat
CreateButton(MainTab, "🛑 Stop Boat", function()
    BoatMoving = false
end)

-- ✅ Kill Aura (against any NPC)
CreateButton(MainTab, "⚔️ Toggle Kill Aura", function()
    KillAura = not KillAura
    print("[GokLeviathan] Kill Aura", KillAura and "Enabled" or "Disabled")
    task.spawn(function()
        while KillAura do
            for _, npc in pairs(workspace:GetDescendants()) do
                if npc:IsA("Model") and npc:FindFirstChild("Humanoid") and npc ~= player.Character then
                    player.Character.HumanoidRootPart.CFrame = npc.HumanoidRootPart.CFrame * CFrame.new(0, 0, 3)
                    -- Simulate attack (key Z)
                    keypress(0x5A)
                    task.wait(0.1)
                    keyrelease(0x5A)
                end
            end
            task.wait(1)
        end
    end)
end)

-- ✅ Auto Attack Leviathan
CreateButton(MainTab, "💥 Toggle Auto Attack", function()
    AutoAttack = not AutoAttack
    print("[GokLeviathan] Auto Attack", AutoAttack and "Enabled" or "Disabled")
    task.spawn(function()
        while AutoAttack do
            for _, v in pairs(workspace:GetDescendants()) do
                if v.Name:lower():find("leviathan") and v:FindFirstChild("Tail") then
                    player.Character.HumanoidRootPart.CFrame = v.Tail.CFrame + Vector3.new(0, 0, 5)
                    keypress(0x5A)
                    task.wait(0.1)
                    keyrelease(0x5A)
                end
            end
            task.wait(1)
        end
    end)
end)

-- ✅ Auto Heal (with safe height)
CreateButton(MainTab, "❤️ Toggle Auto Heal", function()
    AutoHeal = not AutoHeal
    print("[GokLeviathan] Auto Heal", AutoHeal and "Enabled" or "Disabled")
    task.spawn(function()
        while AutoHeal do
            local char = player.Character
            if char and char:FindFirstChild("Humanoid") and char:FindFirstChild("HumanoidRootPart") then
                if char.Humanoid.Health <= (char.Humanoid.MaxHealth * 0.4) then
                    -- Go up to safe height
                    char.HumanoidRootPart.CFrame = char.HumanoidRootPart.CFrame + Vector3.new(0, HealHeight, 0)
                    repeat
                        task.wait(0.5)
                    until char.Humanoid.Health >= (char.Humanoid.MaxHealth * 0.8)
                    print("[GokLeviathan] Healed, returning to fight!")
                end
            end
            task.wait(1)
        end
    end)
end)

-- ✅ Safe Height Slider
CreateSlider(MainTab, "Safe Height", 50, 500, HealHeight, function(val)
    HealHeight = val
end)
