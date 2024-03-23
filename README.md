local executedRemote = false

local OrionLib = loadstring(game:HttpGet(('https://raw.githubusercontent.com/ionlyusegithubformcmods/1-Line-Scripts/main/Mobile%20Friendly%20Orion')))() 
local Window = OrionLib:MakeWindow({
    Name = "Survive and Kill the Killers in Area 51 !!!",
    HidePremium = false,
    SaveConfig = true,
    ConfigFolder = "OrionTest",
    IntroEnabled = true,
    IntroText = "WIP SATTK PRE BETA",
    Icon = "rbxassetid://4483345998",
    CloseCallback = function()
        -- Add any callback logic when the window is closed
    end
})

local Tab = Window:MakeTab({
    Name = "Tab 1",
    Icon = "rbxassetid://4483345998",
    PremiumOnly = false
})

local AutoHealToggle = false

Tab:AddToggle({
    Name = "Auto Heal",
    Default = false,
    Callback = function(Value)
        AutoHealToggle = Value
        if AutoHealToggle and not executedRemote then
            executedRemote = true
            while true do
                local lowHealthThreshold = 50  
                if game.Players.LocalPlayer.Character and game.Players.LocalPlayer.Character:FindFirstChild("Humanoid") then
                    local currentHealth = game.Players.LocalPlayer.Character.Humanoid.Health
                    if currentHealth <= lowHealthThreshold then
                        game:GetService("Players").LocalPlayer.Backpack.Energy.Drank:FireServer()
                    end
                end
                wait()  
            end
        end
    end    
})

local broadcastHPToggle = false

Tab:AddToggle({
    Name = "Broadcast HP in chat",
    Default = false,
    Callback = function(Value)
        broadcastHPToggle = Value  
        while broadcastHPToggle do
            if game.Players.LocalPlayer.Character and game.Players.LocalPlayer.Character:FindFirstChild("Humanoid") then
                local currentHealth = game.Players.LocalPlayer.Character.Humanoid.Health
                local lowHealthThreshold = 50  
                if currentHealth <= lowHealthThreshold then
                    game:GetService("ReplicatedStorage").DefaultChatSystemChatEvents.SayMessageRequest:FireServer("My HP: " .. currentHealth, "All")
                end
            end
            wait()  
        end
    end    
})

-- New Tab for NPCs
local TabNPCs = Window:MakeTab({
    Name = "NPCs",
    Icon = "rbxassetid://4483345998",
    PremiumOnly = false
})

local redBoxesToggle = false
local boxSize = 2
local distanceToggle = false
local showNamesToggle = false
local showDistanceToggle = false
local showSayFrameToggle = false

TabNPCs:AddToggle({
    Name = "Enable Red Boxes",
    Default = false,
    Callback = function(Value)
        redBoxesToggle = Value  
        local killersFolder = game.Workspace:FindFirstChild("Killers")
        if not killersFolder then
            print("Killers folder not found.")
            return
        end

        local killersFound = false
        while redBoxesToggle do
            for _, npc in pairs(killersFolder:GetChildren()) do
                if npc:IsA("Model") and npc:FindFirstChild("HumanoidRootPart") then
                    killersFound = true
                    local redBox = npc:FindFirstChild("RedBox") or Instance.new("BoxHandleAdornment")
                    redBox.Name = "RedBox"
                    redBox.Adornee = npc.HumanoidRootPart
                    redBox.Size = Vector3.new(boxSize, boxSize, boxSize)
                    redBox.AlwaysOnTop = true
                    redBox.ZIndex = 5
                    redBox.Transparency = 0.5
                    redBox.Color3 = Color3.new(1, 0, 0)
                    redBox.Parent = redBoxesToggle and npc or nil
                    
                    local nameLabel = npc:FindFirstChild("NameLabel") or Instance.new("BillboardGui")
                    nameLabel.Name = "NameLabel"
                    nameLabel.Size = UDim2.new(0, 100, 0, 50)
                    nameLabel.AlwaysOnTop = true
                    nameLabel.StudsOffset = Vector3.new(0, 3, 0)
                    
                    local textLabel = nameLabel:FindFirstChild("TextLabel") or Instance.new("TextLabel")
                    textLabel.Name = "TextLabel"
                    textLabel.Size = UDim2.new(1, 0, 1, 0)
                    textLabel.Text = showNamesToggle and npc.Name or ""
                    textLabel.TextColor3 = Color3.new(1, 0, 0)
                    textLabel.Parent = showNamesToggle and nameLabel or nil
                    nameLabel.Parent = showNamesToggle and npc or nil

                    if distanceToggle then
                        local distance = (npc.HumanoidRootPart.Position - game.Players.LocalPlayer.Character.HumanoidRootPart.Position).Magnitude
                        if showDistanceToggle then
                            local distanceLabel = npc:FindFirstChild("DistanceLabel") or Instance.new("BillboardGui")
                            distanceLabel.Name = "DistanceLabel"
                            distanceLabel.Size = UDim2.new(0, 100, 0, 50)
                            distanceLabel.AlwaysOnTop = true
                            distanceLabel.StudsOffset = Vector3.new(0, 3, 0)
                            
                            local distanceTextLabel = distanceLabel:FindFirstChild("TextLabel") or Instance.new("TextLabel")
                            distanceTextLabel.Name = "TextLabel"
                            distanceTextLabel.Size = UDim2.new(1, 0, 1, 0)
                            distanceTextLabel.Text = "Distance: " .. distance
                            distanceTextLabel.TextColor3 = Color3.new(1, 0, 0)
                            distanceTextLabel.Parent = showDistanceToggle and distanceLabel or nil
                            distanceLabel.Parent = showDistanceToggle and npc or nil
                        end
                    end

                    if showSayFrameToggle then
                        local sayFrame = npc:FindFirstChild("SayFrame") or Instance.new("BillboardGui")
                        sayFrame.Name = "SayFrame"
                        sayFrame.Size = UDim2.new(0, 200, 0, 50)
                        sayFrame.StudsOffset = Vector3.new(0, 3, 0)
                        
                        local sayTextLabel = sayFrame:FindFirstChild("TextLabel") or Instance.new("TextLabel")
                        sayTextLabel.Name = "TextLabel"
                        sayTextLabel.Size = UDim2.new(1, 0, 1, 0)
                        sayTextLabel.Text = "npc!"
                        sayTextLabel.TextColor3 = Color3.new(1, 1, 1)
                        sayTextLabel.Parent = sayFrame
                        sayFrame.Parent = showSayFrameToggle and npc or nil
                    end
                end
            end

            if not killersFound then
                print("No killer NPCs found in the Killers folder.")
            end

            wait()  
        end
    end    
})

TabNPCs:AddToggle({
    Name = "Enable Distance Check",
    Default = false,
    Callback = function(Value)
        distanceToggle = Value
    end    
})

TabNPCs:AddToggle({
    Name = "Enable Name Display",
    Default = false,
    Callback = function(Value)
        showNamesToggle = Value
    end    
})

TabNPCs:AddToggle({
    Name = "Enable Distance Display",
    Default = false,
    Callback = function(Value)
        showDistanceToggle = Value
    end    
})

TabNPCs:AddToggle({
    Name = "Enable Show frame",
    Default = false,
    Callback = function(Value)
        showSayFrameToggle = Value
    end    
})

TabNPCs:AddSlider({
    Name = "Box Size",
    Default = 2,
    Min = 1,
    Max = 5,
    Callback = function(Value)
        boxSize = Value
    end
})

-- The rest of the script remains unchanged
