local Players = game:GetService("Players")
local UserInputService = game:GetService("UserInputService")
local RunService = game:GetService("RunService")
local player = Players.LocalPlayer
local character = player.Character or player.CharacterAdded:Wait()
local humanoid = character:WaitForChild("Humanoid")
local rootPart = character:WaitForChild("HumanoidRootPart")
local speed = 16
local sprintSpeed = 16
local noclip = false
local infiniteJump = false
local slowFall = false

-- Criar GUI
local ScreenGui = Instance.new("ScreenGui", player:WaitForChild("PlayerGui"))
ScreenGui.ResetOnSpawn = false
local Frame = Instance.new("Frame", ScreenGui)
Frame.Size = UDim2.new(0, 200, 0, 180)
Frame.Position = UDim2.new(0.1, 0, 0.1, 0)
Frame.BackgroundColor3 = Color3.new(0, 0, 0)
Frame.Active = true
Frame.Draggable = true
Frame.ClipsDescendants = true

-- Botão de Minimizar
local MinimizeButton = Instance.new("TextButton", Frame)
MinimizeButton.Size = UDim2.new(0, 200, 0, 20)
MinimizeButton.Position = UDim2.new(0, 0, 0, 0)
MinimizeButton.Text = "Minimizar"
MinimizeButton.BackgroundColor3 = Color3.new(1, 0, 0)
MinimizeButton.MouseButton1Click:Connect(function()
    Frame.Visible = not Frame.Visible
end)

-- Campo de Speed
local SpeedBox = Instance.new("TextBox", Frame)
SpeedBox.Size = UDim2.new(0, 180, 0, 30)
SpeedBox.Position = UDim2.new(0, 10, 0, 30)
SpeedBox.PlaceholderText = "Digite a velocidade"
SpeedBox.Text = "16"
SpeedBox.BackgroundColor3 = Color3.new(1, 1, 1)
SpeedBox.FocusLost:Connect(function()
    local newSpeed = tonumber(SpeedBox.Text)
    if newSpeed then
        sprintSpeed = newSpeed
    end
end)

-- Controle de Sprint com Shift
UserInputService.InputBegan:Connect(function(input, gameProcessed)
    if input.KeyCode == Enum.KeyCode.LeftShift and not gameProcessed then
        humanoid.WalkSpeed = sprintSpeed
    end
end)
UserInputService.InputEnded:Connect(function(input, gameProcessed)
    if input.KeyCode == Enum.KeyCode.LeftShift and not gameProcessed then
        humanoid.WalkSpeed = speed
    end
end)

-- Botão Pulo Infinito
local JumpButton = Instance.new("TextButton", Frame)
JumpButton.Size = UDim2.new(0, 180, 0, 30)
JumpButton.Position = UDim2.new(0, 10, 0, 70)
JumpButton.Text = "Ativar Pulo Infinito"
JumpButton.BackgroundColor3 = Color3.new(0, 1, 0)
JumpButton.MouseButton1Click:Connect(function()
    infiniteJump = not infiniteJump
    JumpButton.Text = infiniteJump and "Desativar Pulo Infinito" or "Ativar Pulo Infinito"
end)
UserInputService.JumpRequest:Connect(function()
    if infiniteJump then
        humanoid:ChangeState(Enum.HumanoidStateType.Jumping)
    end
end)

-- Botão Noclip
local NoclipButton = Instance.new("TextButton", Frame)
NoclipButton.Size = UDim2.new(0, 180, 0, 30)
NoclipButton.Position = UDim2.new(0, 10, 0, 110)
NoclipButton.Text = "Ativar Noclip"
NoclipButton.BackgroundColor3 = Color3.new(0, 0, 1)
NoclipButton.MouseButton1Click:Connect(function()
    noclip = not noclip
    NoclipButton.Text = noclip and "Desativar Noclip" or "Ativar Noclip"
end)
RunService.Stepped:Connect(function()
    if noclip then
        for _, part in pairs(character:GetDescendants()) do
            if part:IsA("BasePart") then
                part.CanCollide = false
            end
        end
    end
end)

-- Botão Queda Lenta
local SlowFallButton = Instance.new("TextButton", Frame)
SlowFallButton.Size = UDim2.new(0, 180, 0, 30)
SlowFallButton.Position = UDim2.new(0, 10, 0, 150)
SlowFallButton.Text = "Ativar Queda Lenta"
SlowFallButton.BackgroundColor3 = Color3.new(1, 1, 0)
SlowFallButton.MouseButton1Click:Connect(function()
    slowFall = not slowFall
    SlowFallButton.Text = slowFall and "Desativar Queda Lenta" or "Ativar Queda Lenta"
end)
RunService.Heartbeat:Connect(function()
    if slowFall and humanoid.FloorMaterial == Enum.Material.Air then
        rootPart.Velocity = Vector3.new(rootPart.Velocity.X, math.max(rootPart.Velocity.Y, -5), rootPart.Velocity.Z)
    end
end)
