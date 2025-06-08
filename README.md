-- Fly e Speed Manager com interface gráfica e ajuste dinâmico

local player = game.Players.LocalPlayer
local char = player.Character or player.CharacterAdded:Wait()
local hum = char:WaitForChild("Humanoid")

-- Variáveis
local flySpeed = 50
local flying = false
local flyConn
local bv, bg
local walkSpeed = hum.WalkSpeed

-- GUI Construction
local gui = Instance.new("ScreenGui")
gui.Name = "FlySpeedControl"
gui.ResetOnSpawn = false
gui.Parent = player:WaitForChild("PlayerGui")

local frame = Instance.new("Frame")
frame.Size = UDim2.new(0, 260, 0, 180)
frame.Position = UDim2.new(0, 20, 0.5, -90)
frame.BackgroundColor3 = Color3.fromRGB(30, 30, 30)
frame.BackgroundTransparency = 0.12
frame.BorderSizePixel = 0
frame.AnchorPoint = Vector2.new(0,0)
frame.Parent = gui

local title = Instance.new("TextLabel")
title.Size = UDim2.new(1, 0, 0, 34)
title.Position = UDim2.new(0, 0, 0, 0)
title.BackgroundTransparency = 1
title.Text = "Fly & Speed Control"
title.Font = Enum.Font.SourceSansBold
title.TextSize = 22
title.TextColor3 = Color3.fromRGB(255,255,205)
title.Parent = frame

-- SPEED
local speedLabel = Instance.new("TextLabel")
speedLabel.Size = UDim2.new(0.6, 0, 0, 32)
speedLabel.Position = UDim2.new(0.05, 0, 0, 44)
speedLabel.BackgroundTransparency = 1
speedLabel.Text = "Speed: "..walkSpeed
speedLabel.Font = Enum.Font.SourceSans
speedLabel.TextSize = 19
speedLabel.TextColor3 = Color3.fromRGB(160,255,160)
speedLabel.TextXAlignment = Enum.TextXAlignment.Left
speedLabel.Parent = frame

local speedMinus = Instance.new("TextButton")
speedMinus.Size = UDim2.new(0, 32, 0, 32)
speedMinus.Position = UDim2.new(0.65, 0, 0, 44)
speedMinus.Text = "-"
speedMinus.Font = Enum.Font.SourceSansBold
speedMinus.TextSize = 25
speedMinus.BackgroundColor3 = Color3.fromRGB(60,60,60)
speedMinus.TextColor3 = Color3.new(1,1,1)
speedMinus.Parent = frame

local speedPlus = Instance.new("TextButton")
speedPlus.Size = UDim2.new(0, 32, 0, 32)
speedPlus.Position = UDim2.new(0.8, 0, 0, 44)
speedPlus.Text = "+"
speedPlus.Font = Enum.Font.SourceSansBold
speedPlus.TextSize = 25
speedPlus.BackgroundColor3 = Color3.fromRGB(60,60,60)
speedPlus.TextColor3 = Color3.new(1,1,1)
speedPlus.Parent = frame

-- FLY SPEED
local flyLabel = Instance.new("TextLabel")
flyLabel.Size = UDim2.new(0.6, 0, 0, 32)
flyLabel.Position = UDim2.new(0.05, 0, 0, 92)
flyLabel.BackgroundTransparency = 1
flyLabel.Text = "Fly Speed: "..flySpeed
flyLabel.Font = Enum.Font.SourceSans
flyLabel.TextSize = 19
flyLabel.TextColor3 = Color3.fromRGB(160,220,255)
flyLabel.TextXAlignment = Enum.TextXAlignment.Left
flyLabel.Parent = frame

local flyMinus = Instance.new("TextButton")
flyMinus.Size = UDim2.new(0, 32, 0, 32)
flyMinus.Position = UDim2.new(0.65, 0, 0, 92)
flyMinus.Text = "-"
flyMinus.Font = Enum.Font.SourceSansBold
flyMinus.TextSize = 25
flyMinus.BackgroundColor3 = Color3.fromRGB(60,60,60)
flyMinus.TextColor3 = Color3.new(1,1,1)
flyMinus.Parent = frame

local flyPlus = Instance.new("TextButton")
flyPlus.Size = UDim2.new(0, 32, 0, 32)
flyPlus.Position = UDim2.new(0.8, 0, 0, 92)
flyPlus.Text = "+"
flyPlus.Font = Enum.Font.SourceSansBold
flyPlus.TextSize = 25
flyPlus.BackgroundColor3 = Color3.fromRGB(60,60,60)
flyPlus.TextColor3 = Color3.new(1,1,1)
flyPlus.Parent = frame

-- FLY TOGGLE BUTTON
local flyButton = Instance.new("TextButton")
flyButton.Size = UDim2.new(0.82, 0, 0, 38)
flyButton.Position = UDim2.new(0.09, 0, 0, 140)
flyButton.BackgroundColor3 = Color3.fromRGB(85,135,255)
flyButton.Text = "Ativar Fly"
flyButton.Font = Enum.Font.SourceSansBold
flyButton.TextSize = 21
flyButton.TextColor3 = Color3.new(1,1,1)
flyButton.Parent = frame

-- Funções de atualização
local function updateInfo()
    local hum = char and char:FindFirstChildOfClass("Humanoid")
    speedLabel.Text = "Speed: "..(hum and math.floor(hum.WalkSpeed) or "-")
    flyLabel.Text = "Fly Speed: "..flySpeed
    flyButton.Text = flying and "Desativar Fly" or "Ativar Fly"
    flyButton.BackgroundColor3 = flying and Color3.fromRGB(255,90,90) or Color3.fromRGB(85,135,255)
end

-- Função Fly
local uis = game:GetService("UserInputService")
local rs = game:GetService("RunService")

local function startFly()
    if flying then return end
    flying = true
    bv = Instance.new("BodyVelocity")
    bv.MaxForce = Vector3.new(1e5,1e5,1e5)
    bv.Velocity = Vector3.new(0,0,0)
    bv.Parent = char:WaitForChild("HumanoidRootPart")

    bg = Instance.new("BodyGyro")
    bg.MaxTorque = Vector3.new(1e5,1e5,1e5)
    bg.CFrame = char.HumanoidRootPart.CFrame
    bg.Parent = char:WaitForChild("HumanoidRootPart")

    flyConn = rs.RenderStepped:Connect(function()
        local move = Vector3.new()
        local cam = workspace.CurrentCamera
        if uis:IsKeyDown(Enum.KeyCode.W) then move = move + cam.CFrame.LookVector end
        if uis:IsKeyDown(Enum.KeyCode.S) then move = move - cam.CFrame.LookVector end
        if uis:IsKeyDown(Enum.KeyCode.A) then move = move - cam.CFrame.RightVector end
        if uis:IsKeyDown(Enum.KeyCode.D) then move = move + cam.CFrame.RightVector end
        if uis:IsKeyDown(Enum.KeyCode.Space) then move = move + cam.CFrame.UpVector end
        if uis:IsKeyDown(Enum.KeyCode.LeftControl) then move = move - cam.CFrame.UpVector end
        if move.Magnitude > 0 then
            move = move.Unit * flySpeed
        end
        bv.Velocity = move
        bg.CFrame = cam.CFrame
    end)
    updateInfo()
end

local function stopFly()
    flying = false
    if bv then bv:Destroy() bv = nil end
    if bg then bg:Destroy() bg = nil end
    if flyConn then flyConn:Disconnect() flyConn = nil end
    updateInfo()
end

-- Botões
speedPlus.MouseButton1Click:Connect(function()
    walkSpeed = walkSpeed + 5
    local hum = char and char:FindFirstChildOfClass("Humanoid")
    if hum then hum.WalkSpeed = walkSpeed end
    updateInfo()
end)
speedMinus.MouseButton1Click:Connect(function()
    walkSpeed = math.max(5, walkSpeed - 5)
    local hum = char and char:FindFirstChildOfClass("Humanoid")
    if hum then hum.WalkSpeed = walkSpeed end
    updateInfo()
end)
flyPlus.MouseButton1Click:Connect(function()
    flySpeed = flySpeed + 5
    updateInfo()
end)
flyMinus.MouseButton1Click:Connect(function()
    flySpeed = math.max(5, flySpeed - 5)
    updateInfo()
end)
flyButton.MouseButton1Click:Connect(function()
    if flying then stopFly() else startFly() end
end)

-- Atualiza valores se personagem morrer/resetar
player.CharacterAdded:Connect(function(newChar)
    char = newChar
    hum = char:WaitForChild("Humanoid")
    walkSpeed = hum.WalkSpeed
    stopFly()
    updateInfo()
end)

updateInfo()
