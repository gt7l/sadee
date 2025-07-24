local Players = game:GetService("Players")
local LocalPlayer = Players.LocalPlayer
local RunService = game:GetService("RunService")

-- GUI الأساسي
local gui = Instance.new("ScreenGui", LocalPlayer:WaitForChild("PlayerGui"))
gui.Name = "AbuAtabGUI"
gui.ResetOnSpawn = false

-- زر فتح/إغلاق (ثابت)
local toggleButton = Instance.new("TextButton")
toggleButton.Size = UDim2.new(0, 80, 0, 30)
toggleButton.Position = UDim2.new(0, 10, 1, -40)
toggleButton.BackgroundColor3 = Color3.fromRGB(50, 50, 50)
toggleButton.TextColor3 = Color3.fromRGB(255, 255, 255)
toggleButton.Text = "القائمة"
toggleButton.Parent = gui
toggleButton.Active = true
toggleButton.Draggable = false -- الزر غير قابل للسحب

-- نافذة القائمة (قابلة للسحب)
local mainFrame = Instance.new("Frame", gui)
mainFrame.Size = UDim2.new(0, 300, 0, 250)
mainFrame.Position = UDim2.new(0.35, 0, 0.25, 0)
mainFrame.BackgroundColor3 = Color3.fromRGB(120, 0, 0) -- أحمر غامق
mainFrame.BorderSizePixel = 0
mainFrame.Visible = false
mainFrame.Active = true
mainFrame.Draggable = true -- القائمة قابلة للسحب

-- العنوان
local header = Instance.new("TextLabel", mainFrame)
header.Size = UDim2.new(1, 0, 0, 40)
header.BackgroundTransparency = 1
header.Text = "سكربت ابو عتب"
header.Font = Enum.Font.GothamBold
header.TextSize = 24
header.TextColor3 = Color3.fromRGB(255, 255, 255)

-- إدخال الاسم
local nameBox = Instance.new("TextBox", mainFrame)
nameBox.Size = UDim2.new(0.8, 0, 0, 30)
nameBox.Position = UDim2.new(0.1, 0, 0.15, 0)
nameBox.PlaceholderText = "اكتب جزء من الاسم"
nameBox.Text = ""
nameBox.BackgroundColor3 = Color3.fromRGB(30, 30, 30)
nameBox.TextColor3 = Color3.fromRGB(255, 255, 255)
nameBox.Font = Enum.Font.Gotham

-- زر التأكيد
local confirm = Instance.new("TextButton", mainFrame)
confirm.Size = UDim2.new(0.8, 0, 0, 30)
confirm.Position = UDim2.new(0.1, 0, 0.28, 0)
confirm.Text = "✅"
confirm.Font = Enum.Font.GothamBold
confirm.TextSize = 20
confirm.BackgroundColor3 = Color3.fromRGB(50, 50, 50)
confirm.TextColor3 = Color3.fromRGB(255, 255, 255)

-- عدادات العرض
local enteredLabel = Instance.new("TextLabel", mainFrame)
enteredLabel.Size = UDim2.new(1, 0, 0, 25)
enteredLabel.Position = UDim2.new(0, 0, 0.42, 0)
enteredLabel.BackgroundTransparency = 1
enteredLabel.Font = Enum.Font.Gotham
enteredLabel.TextSize = 18
enteredLabel.TextColor3 = Color3.fromRGB(0, 255, 255)
enteredLabel.Text = "دخول: 0"

local leftLabel = Instance.new("TextLabel", mainFrame)
leftLabel.Size = UDim2.new(1, 0, 0, 25)
leftLabel.Position = UDim2.new(0, 0, 0.52, 0)
leftLabel.BackgroundTransparency = 1
leftLabel.Font = Enum.Font.Gotham
leftLabel.TextSize = 18
leftLabel.TextColor3 = Color3.fromRGB(255, 85, 85)
leftLabel.Text = "خروج: 0"

local durationLabel = Instance.new("TextLabel", mainFrame)
durationLabel.Size = UDim2.new(1, 0, 0, 25)
durationLabel.Position = UDim2.new(0, 0, 0.62, 0)
durationLabel.BackgroundTransparency = 1
durationLabel.Font = Enum.Font.Gotham
durationLabel.TextSize = 18
durationLabel.TextColor3 = Color3.fromRGB(255, 255, 0)
durationLabel.Text = "مدة الهدف: 00:00:00"

local myTimeLabel = Instance.new("TextLabel", mainFrame)
myTimeLabel.Size = UDim2.new(1, 0, 0, 25)
myTimeLabel.Position = UDim2.new(0, 0, 0.72, 0)
myTimeLabel.BackgroundTransparency = 1
myTimeLabel.Font = Enum.Font.Gotham
myTimeLabel.TextSize = 18
myTimeLabel.TextColor3 = Color3.fromRGB(0, 255, 0)
myTimeLabel.Text = "وقتي بالسيرفر: 00:00:00"

-- التوقيع
local signature = Instance.new("TextLabel", mainFrame)
signature.Size = UDim2.new(1, 0, 0, 20)
signature.Position = UDim2.new(0, 0, 0.88, 0)
signature.BackgroundTransparency = 1
signature.Text = "By Zeus"
signature.Font = Enum.Font.GothamBold
signature.TextSize = 16
signature.TextColor3 = Color3.fromRGB(255, 255, 255)
signature.TextXAlignment = Enum.TextXAlignment.Center

-- متغيرات
local tracking = ""
local enterCount, leaveCount = 0, 0
local startTrackingTime = 0
local myStartTime = tick()

-- بحث بالأحرف الأولى وتحديث تلقائي
confirm.MouseButton1Click:Connect(function()
	local input = nameBox.Text:lower()
	for _, plr in pairs(Players:GetPlayers()) do
		if plr ~= LocalPlayer and plr.Name:lower():sub(1, #input) == input then
			tracking = plr.Name
			enterCount = 0
			leaveCount = 0
			startTrackingTime = 0
			break
		end
	end
end)

-- تتبع الدخول والخروج والمدة
RunService.Heartbeat:Connect(function()
	if tracking ~= "" then
		local target = Players:FindFirstChild(tracking)
		if target then
			if startTrackingTime == 0 then
				startTrackingTime = tick()
				enterCount += 1
			end
			local dt = tick() - startTrackingTime
			local h = math.floor(dt / 3600)
			local m = math.floor((dt % 3600) / 60)
			local s = math.floor(dt % 60)
			durationLabel.Text = string.format("مدة الهدف: %02d:%02d:%02d", h, m, s)
		else
			if startTrackingTime ~= 0 then
				startTrackingTime = 0
				leaveCount += 1
			end
			durationLabel.Text = "مدة الهدف: 00:00:00"
		end
		enteredLabel.Text = "دخول: " .. enterCount
		leftLabel.Text = "خروج: " .. leaveCount
	end

	local myTime = tick() - myStartTime
	local h = math.floor(myTime / 3600)
	local m = math.floor((myTime % 3600) / 60)
	local s = math.floor(myTime % 60)
	myTimeLabel.Text = string.format("وقتي بالسيرفر: %02d:%02d:%02d", h, m, s)
end)

-- فتح/إغلاق القائمة
toggleButton.MouseButton1Click:Connect(function()
	mainFrame.Visible = not mainFrame.Visible
end)
