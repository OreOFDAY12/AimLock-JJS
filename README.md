local player = game.Players.LocalPlayer
local mouse = player:GetMouse()
local camera = game.Workspace.CurrentCamera
local UIS = game:GetService("UserInputService")
local RunService = game:GetService("RunService")
local isFollowing = false
local targetPlayer = nil
local function toggleCameraFollow()
    isFollowing = not isFollowing
    if not isFollowing then
        targetPlayer = nil -- Reseta o jogador alvo ao desativar
    end
end
UIS.InputBegan:Connect(function(input, gameProcessedEvent)
    if not gameProcessedEvent and input.KeyCode == Enum.KeyCode.U then
        toggleCameraFollow()
    end
end)
local function findClosestPlayer()
    local closestPlayer = nil
    local shortestDistance = math.huge

    if player.Character and player.Character:FindFirstChild("HumanoidRootPart") then
        local playerPosition = player.Character.HumanoidRootPart.Position

        for _, otherPlayer in pairs(game.Players:GetPlayers()) do
            if otherPlayer ~= player and otherPlayer.Character then
                local humanoidRootPart = otherPlayer.Character:FindFirstChild("HumanoidRootPart")
                if humanoidRootPart then
                    local distance = (playerPosition - humanoidRootPart.Position).Magnitude
                    if distance < shortestDistance then
                        shortestDistance = distance
                        closestPlayer = otherPlayer
                    end
                end
            end
        end
    end
    return closestPlayer
end

RunService.Heartbeat:Connect(function()
    if isFollowing then
        if not targetPlayer or not targetPlayer.Character or not targetPlayer.Character:FindFirstChild("HumanoidRootPart") then
            targetPlayer = findClosestPlayer()
        end
        if targetPlayer and targetPlayer.Character then
            local humanoidRootPart = targetPlayer.Character:FindFirstChild("HumanoidRootPart")
            if humanoidRootPart then
                camera.CFrame = CFrame.new(camera.CFrame.Position, humanoidRootPart.Position)
            end
        end
    end
end)
