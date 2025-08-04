local Players = game:GetService("Players")
local LocalPlayer = Players.LocalPlayer
local RunService = game:GetService("RunService")

-- Function to handle when a player equips a tool
local function onToolEquipped(player, tool)
    print("[⚡] " .. player.Name .. " equipped: " .. tool.Name)
end

-- Function to watch a tool and trigger the onToolEquipped function when it's equipped
local function watchTool(tool, player)
    if tool:IsA("Tool") then
        tool.Equipped:Connect(function()
            onToolEquipped(player, tool)
        end)
        onToolEquipped(player, tool)  -- Run onToolEquipped immediately for tools that are already equipped
    end
end

-- Function to watch tools for a specific player in a container
local function handleTools(player)
    local function scan(container)
        for _, item in pairs(container:GetChildren()) do
            watchTool(item, player)
        end
        container.ChildAdded:Connect(function(child)
            watchTool(child, player)
        end)
    end
    
    -- Assuming tools are located in the player's backpack
    scan(player.Backpack)
end

-- Function to handle interaction with GearGiver1
local function Get()
    local Root = LocalPlayer.Character:FindFirstChild("HumanoidRootPart")
    for i, v in next, workspace.Tycoons:GetDescendants() do
        if v:IsA("TouchTransmitter") and v.Parent.Parent.Name:find("GearGiver1") then
            firetouchinterest(Root, v.Parent, 0)
            firetouchinterest(Root, v.Parent, 1)
        end
    end
end

-- Watch for tools when the player’s character is added
LocalPlayer.CharacterAdded:Connect(function()
    LocalPlayer.Character:WaitForChild("HumanoidRootPart")
    RunService.Stepped:Wait()
    Get()  -- Interact with GearGiver1
    handleTools(LocalPlayer)  -- Watch tools
end)

-- Initial function call to trigger Get() for the first time
Get()
