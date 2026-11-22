local Player = game:GetService("Players").LocalPlayer
local RunService = game:GetService("RunService")
local UserInputService = game:GetService("UserInputService")

local ScreenGui = Instance.new("ScreenGui")
ScreenGui.Name = "DucAnhzzGodMode"
ScreenGui.ResetOnSpawn = false
ScreenGui.Parent = Player:WaitForChild("PlayerGui")

-- Khung chữ nhật bé, bo tròn không góc nhọn
local MainFrame = Instance.new("Frame")
MainFrame.Size = UDim2.new(0, 150, 0, 82)
MainFrame.Position = UDim2.new(0.5, -75, 0.25, 0)
MainFrame.BackgroundColor3 = Color3.fromRGB(20, 20, 26)
MainFrame.BorderSizePixel = 0
MainFrame.Parent = ScreenGui

local MainCorner = Instance.new("UICorner")
MainCorner.CornerRadius = UDim.new(0, 16)
MainCorner.Parent = MainFrame

local MainStroke = Instance.new("UIStroke")
MainStroke.Thickness = 2
MainStroke.Color = Color3.fromRGB(45, 45, 58)
MainStroke.Parent = MainFrame

-- Dòng chữ DucAnhzz hiệu ứng Rainbow ở trên
local Title = Instance.new("TextLabel")
Title.Size = UDim2.new(1, 0, 0, 30)
Title.Position = UDim2.new(0, 0, 0, 4)
Title.BackgroundTransparency = 1
Title.Text = "DucAnhzz"
Title.Font = Enum.Font.GothamBlack
Title.TextSize = 15
Title.Parent = MainFrame

-- Nút Bật / Tắt bo tròn bên dưới (Màu Đỏ khi tắt, Xanh khi bật)
local ToggleBtn = Instance.new("TextButton")
ToggleBtn.Size = UDim2.new(0.84, 0, 0, 36)
ToggleBtn.Position = UDim2.new(0.08, 0, 0, 38)
ToggleBtn.BackgroundColor3 = Color3.fromRGB(215, 55, 55)
ToggleBtn.Text = "GOD MODE: TẮT"
ToggleBtn.TextColor3 = Color3.fromRGB(255, 255, 255)
ToggleBtn.Font = Enum.Font.GothamBold
ToggleBtn.TextSize = 12
ToggleBtn.AutoButtonColor = false
ToggleBtn.Parent = MainFrame

local BtnCorner = Instance.new("UICorner")
BtnCorner.CornerRadius = UDim.new(0, 14)
BtnCorner.Parent = ToggleBtn

-- Hiệu ứng chữ DucAnhzz đổi màu Rainbow mượt mà
RunService.RenderStepped:Connect(function()
	local hue = (tick() * 0.4) % 1
	Title.TextColor3 = Color3.fromHSV(hue, 0.85, 1)
end)

-- Kéo thả menu mượt trên cả cảm ứng điện thoại lẫn chuột
local dragging, dragInput, dragStart, startPos
MainFrame.InputBegan:Connect(function(input)
	if input.UserInputType == Enum.UserInputType.MouseButton1 or input.UserInputType == Enum.UserInputType.Touch then
		dragging = true
		dragStart = input.Position
		startPos = MainFrame.Position
		input.Changed:Connect(function()
			if input.UserInputState == Enum.UserInputState.End then
				dragging = false
			end
		end)
	end
end)

MainFrame.InputChanged:Connect(function(input)
	if input.UserInputType == Enum.UserInputType.MouseMovement or input.UserInputType == Enum.UserInputType.Touch then
		dragInput = input
	end
end)

UserInputService.InputChanged:Connect(function(input)
	if input == dragInput and dragging then
		local delta = input.Position - dragStart
		MainFrame.Position = UDim2.new(startPos.X.Scale, startPos.X.Offset + delta.X, startPos.Y.Scale, startPos.Y.Offset + delta.Y)
	end
end)

-- Logic God Mode
local isGod = false
local healthConn

local function setGodMode(character)
	local humanoid = character:WaitForChild("Humanoid")
	if healthConn then
		healthConn:Disconnect()
		healthConn = nil
	end

	if isGod then
		humanoid.MaxHealth = math.huge
		humanoid.Health = math.huge
		healthConn = humanoid.HealthChanged:Connect(function()
			if isGod and humanoid.Health < humanoid.MaxHealth then
				humanoid.Health = math.huge
			end
		end)
	else
		humanoid.MaxHealth = 100
		humanoid.Health = 100
	end
end

ToggleBtn.MouseButton1Click:Connect(function()
	isGod = not isGod
	if isGod then
		ToggleBtn.Text = "GOD MODE: BẬT"
		ToggleBtn.BackgroundColor3 = Color3.fromRGB(45, 195, 85)
	else
		ToggleBtn.Text = "GOD MODE: TẮT"
		ToggleBtn.BackgroundColor3 = Color3.fromRGB(215, 55, 55)
	end

	if Player.Character then
		setGodMode(Player.Character)
	end
end)

Player.CharacterAdded:Connect(function(char)
	task.wait(0.2)
	if isGod then
		setGodMode(char)
	end
end)
