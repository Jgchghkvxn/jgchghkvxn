local Players = game:GetService("Players")
local UserInputService = game:GetService("UserInputService")
local RunService = game:GetService("RunService")
local localPlayer = Players.LocalPlayer
local camera = workspace.CurrentCamera

local playerGui = localPlayer:WaitForChild("PlayerGui")

local Spectate = {}
Spectate.Gui = nil
Spectate.Target = nil
Spectate.MainFrame = nil
Spectate.MiniFrame = nil
Spectate.Scroll = nil

function Spectate:UpdateList()
	if not self.Scroll then return end
	for _, child in pairs(self.Scroll:GetChildren()) do
		if child:IsA("TextButton") then child:Destroy() end
	end
	for _, player in ipairs(Players:GetPlayers()) do
		if player ~= localPlayer then
			local btn = Instance.new("TextButton")
			btn.Size = UDim2.new(1, 0, 0, 26)
			btn.BackgroundColor3 = self.Target == player and Color3.fromRGB(0,120,200) or Color3.fromRGB(50,50,50)
			btn.Text = (self.Target == player and "👁 " or "") .. player.Name
			btn.TextColor3 = Color3.fromRGB(255,255,255)
			btn.Font = Enum.Font.SourceSans
			btn.TextSize = 13
			btn.BorderSizePixel = 0
			btn.Parent = self.Scroll
			btn.MouseButton1Click:Connect(function()
				Spectate:Watch(player)
			end)
		end
	end
	local layout = self.Scroll:FindFirstChildOfClass("UIListLayout")
	if layout then
		self.Scroll.CanvasSize = UDim2.new(0, 0, 0, layout.AbsoluteContentSize.Y + 5)
	end
end

function Spectate:Watch(player)
	if not player then return end
	local char = player.Character
	if not char then return end
	local hum = char:FindFirstChildOfClass("Humanoid")
	if not hum then return end
	self.Target = player
	camera.CameraSubject = hum
	camera.CameraType = Enum.CameraType.Custom
	self:UpdateList()
end

function Spectate:StopWatch()
	self.Target = nil
	local char = localPlayer.Character
	if char then
		local hum = char:FindFirstChildOfClass("Humanoid")
		if hum then
			camera.CameraSubject = hum
			camera.CameraType = Enum.CameraType.Custom
		end
	end
	self:UpdateList()
end

function Spectate:Create()
	if self.Gui then self.Gui:Destroy() end
	local gui = Instance.new("ScreenGui")
	gui.Name = "SpectatePanel"
	gui.ResetOnSpawn = false
	gui.Parent = playerGui
	self.Gui = gui

	local main = Instance.new("Frame")
	main.Size = UDim2.new(0, 200, 0, 280)
	main.Position = UDim2.new(0.5, -100, 0.5, -140)
	main.BackgroundColor3 = Color3.fromRGB(35,35,35)
	main.BorderSizePixel = 0
	main.Active = false
	main.Parent = gui
	self.MainFrame = main

	local corner = Instance.new("UICorner")
	corner.CornerRadius = UDim.new(0, 6)
	corner.Parent = main

	local title = Instance.new("TextLabel")
	title.Size = UDim2.new(1, -40, 0, 30)
	title.Position = UDim2.new(0, 35, 0, 0)
	title.BackgroundTransparency = 1
	title.Text = "观察玩家"
	title.TextColor3 = Color3.fromRGB(255,255,255)
	title.Font = Enum.Font.SourceSansBold
	title.TextSize = 14
	title.TextXAlignment = Enum.TextXAlignment.Left
	title.Parent = main

	local minBtn = Instance.new("TextButton")
	minBtn.Size = UDim2.new(0, 30, 0, 24)
	minBtn.Position = UDim2.new(0, 4, 0, 3)
	minBtn.BackgroundColor3 = Color3.fromRGB(100, 180, 100)
	minBtn.Text = "—"
	minBtn.TextColor3 = Color3.fromRGB(255,255,255)
	minBtn.Font = Enum.Font.SourceSansBold
	minBtn.TextSize = 18
	minBtn.BorderSizePixel = 0
	minBtn.Parent = main

	local scroll = Instance.new("ScrollingFrame")
	scroll.Size = UDim2.new(1, -10, 1, -135)
	scroll.Position = UDim2.new(0, 5, 0, 35)
	scroll.BackgroundTransparency = 1
	scroll.CanvasSize = UDim2.new(0, 0, 0, 0)
	scroll.ScrollBarThickness = 3
	scroll.BorderSizePixel = 0
	scroll.Parent = main
	self.Scroll = scroll

	local layout = Instance.new("UIListLayout")
	layout.Padding = UDim.new(0, 3)
	layout.Parent = scroll

	local refreshBtn = Instance.new("TextButton")
	refreshBtn.Size = UDim2.new(1, -10, 0, 26)
	refreshBtn.Position = UDim2.new(0, 5, 1, -70)
	refreshBtn.BackgroundColor3 = Color3.fromRGB(70, 130, 180)
	refreshBtn.Text = "刷新列表"
	refreshBtn.TextColor3 = Color3.fromRGB(255,255,255)
	refreshBtn.Font = Enum.Font.SourceSansBold
	refreshBtn.TextSize = 13
	refreshBtn.BorderSizePixel = 0
	refreshBtn.Parent = main

	local stopBtn = Instance.new("TextButton")
	stopBtn.Size = UDim2.new(1, -10, 0, 26)
	stopBtn.Position = UDim2.new(0, 5, 1, -38)
	stopBtn.BackgroundColor3 = Color3.fromRGB(200, 50, 50)
	stopBtn.Text = "停止观察"
	stopBtn.TextColor3 = Color3.fromRGB(255,255,255)
	stopBtn.Font = Enum.Font.SourceSansBold
	stopBtn.TextSize = 13
	stopBtn.BorderSizePixel = 0
	stopBtn.Parent = main

	local mini = Instance.new("Frame")
	mini.Size = UDim2.new(0, 36, 0, 33)
	mini.Position = UDim2.new(1, -150, 0, 10)
	mini.BackgroundColor3 = Color3.fromRGB(35,35,35)
	mini.BorderSizePixel = 0
	mini.Visible = false
	mini.Active = false
	mini.Parent = gui
	self.MiniFrame = mini

	local miniCorner = Instance.new("UICorner")
	miniCorner.CornerRadius = UDim.new(0, 4)
	miniCorner.Parent = mini

	local miniPlus = Instance.new("TextButton")
	miniPlus.Size = UDim2.new(1, 0, 1, 0)
	miniPlus.Position = UDim2.new(0, 0, 0, 0)
	miniPlus.BackgroundColor3 = Color3.fromRGB(100, 180, 100)
	miniPlus.Text = "＋"
	miniPlus.TextColor3 = Color3.fromRGB(255,255,255)
	miniPlus.Font = Enum.Font.SourceSansBold
	miniPlus.TextSize = 18
	miniPlus.BorderSizePixel = 0
	miniPlus.Parent = mini

	minBtn.MouseButton1Click:Connect(function()
		main.Visible = false
		mini.Visible = true
	end)

	miniPlus.MouseButton1Click:Connect(function()
		main.Visible = true
		mini.Visible = false
	end)

	refreshBtn.MouseButton1Click:Connect(function()
		Spectate:UpdateList()
	end)

	stopBtn.MouseButton1Click:Connect(function()
		Spectate:StopWatch()
	end)

	Players.PlayerAdded:Connect(function()
		Spectate:UpdateList()
	end)
	Players.PlayerRemoving:Connect(function(player)
		if player == Spectate.Target then
			Spectate:StopWatch()
		end
		Spectate:UpdateList()
	end)

	self:UpdateList()
end

UserInputService.InputBegan:Connect(function(input, gameProcessed)
	if gameProcessed then return end
	if input.KeyCode == Enum.KeyCode.F9 then
		if not Spectate.Gui then
			Spectate:Create()
		else
			if Spectate.MainFrame and Spectate.MiniFrame then
				local mainVis = Spectate.MainFrame.Visible
				Spectate.MainFrame.Visible = not mainVis
				Spectate.MiniFrame.Visible = false
			end
		end
	end
end)

Spectate:Create()