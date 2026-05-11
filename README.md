--// Misa🌟 ULTRA FAST + TRIPLE

local player = game.Players.LocalPlayer
local backpack = player:WaitForChild("Backpack")

local TweenService = game:GetService("TweenService")
local VirtualUser = game:GetService("VirtualUser")

--================ CHARACTER FIX ================--

local character
local humanoid

local function setupCharacter(char)

	character = char
	humanoid = char:WaitForChild("Humanoid")
end

setupCharacter(player.Character or player.CharacterAdded:Wait())

player.CharacterAdded:Connect(function(char)

	setupCharacter(char)
end)

--================ STATES ================--

local weightEnabled = false
local doubleEnabled = false
local dropEnabled = false

local minimized = false
local dropping = false

-- velocidades
local clickDelay = 0.01
local equipDelay = 0.01

--================ GUI ================--

local gui = Instance.new("ScreenGui")
gui.Parent = game.CoreGui
gui.Name = "MisaStarUI"
gui.ResetOnSpawn = false

local main = Instance.new("Frame")
main.Parent = gui
main.Size = UDim2.new(0,260,0,220)
main.Position = UDim2.new(0.5,-130,0.6,0)
main.BackgroundColor3 = Color3.fromRGB(20,20,20)
main.BorderSizePixel = 0
main.Active = true
main.Draggable = true

Instance.new("UICorner", main).CornerRadius = UDim.new(0,16)

local stroke = Instance.new("UIStroke")
stroke.Parent = main
stroke.Color = Color3.fromRGB(70,70,70)

--================ TITLE ================--

local title = Instance.new("TextLabel")
title.Parent = main
title.Size = UDim2.new(1,0,0,40)
title.BackgroundTransparency = 1
title.Text = "Misa🌟"
title.TextScaled = true
title.Font = Enum.Font.GothamBold
title.TextColor3 = Color3.fromRGB(255,255,255)

--================ MINIMIZE ================--

local minimize = Instance.new("TextButton")
minimize.Parent = main
minimize.Size = UDim2.new(0,30,0,30)
minimize.Position = UDim2.new(1,-35,0,5)
minimize.Text = "-"
minimize.TextScaled = true
minimize.Font = Enum.Font.GothamBold
minimize.BackgroundColor3 = Color3.fromRGB(40,40,40)
minimize.TextColor3 = Color3.fromRGB(255,255,255)
minimize.BorderSizePixel = 0

Instance.new("UICorner", minimize).CornerRadius = UDim.new(1,0)

--================ CREATE BUTTON ================--

local function createButton(text,y)

	local btn = Instance.new("TextButton")
	btn.Parent = main
	btn.Size = UDim2.new(1,-20,0,42)
	btn.Position = UDim2.new(0,10,0,y)
	btn.BackgroundColor3 = Color3.fromRGB(35,35,35)
	btn.BorderSizePixel = 0
	btn.Text = text .. ": OFF"
	btn.TextScaled = true
	btn.Font = Enum.Font.GothamBold
	btn.TextColor3 = Color3.fromRGB(255,255,255)

	Instance.new("UICorner", btn).CornerRadius = UDim.new(0,12)

	btn.MouseEnter:Connect(function()

		TweenService:Create(btn,TweenInfo.new(0.15),{
			BackgroundColor3 = Color3.fromRGB(50,50,50)
		}):Play()
	end)

	btn.MouseLeave:Connect(function()

		if not string.find(btn.Text,"ON") then

			TweenService:Create(btn,TweenInfo.new(0.15),{
				BackgroundColor3 = Color3.fromRGB(35,35,35)
			}):Play()
		end
	end)

	return btn
end

local weightBtn = createButton("WEIGHT AUTO",55)
local doubleBtn = createButton("DOUBLE/TRIPLE",105)
local dropBtn = createButton("AUTO DROP",155)

--================ TOGGLE ================--

local function toggle(btn,state,name)

	state = not state

	btn.Text = name .. (state and ": ON" or ": OFF")

	TweenService:Create(btn,TweenInfo.new(0.2),{
		BackgroundColor3 = state
			and Color3.fromRGB(0,170,90)
			or Color3.fromRGB(35,35,35)
	}):Play()

	return state
end

weightBtn.MouseButton1Click:Connect(function()

	weightEnabled =
		toggle(weightBtn,weightEnabled,"WEIGHT AUTO")
end)

doubleBtn.MouseButton1Click:Connect(function()

	doubleEnabled =
		toggle(doubleBtn,doubleEnabled,"DOUBLE/TRIPLE")
end)

dropBtn.MouseButton1Click:Connect(function()

	dropEnabled =
		toggle(dropBtn,dropEnabled,"AUTO DROP")
end)

--================ MINIMIZE UI ================--

minimize.MouseButton1Click:Connect(function()

	minimized = not minimized

	if minimized then

		TweenService:Create(main,TweenInfo.new(0.25),{
			Size = UDim2.new(0,260,0,45)
		}):Play()

		weightBtn.Visible = false
		doubleBtn.Visible = false
		dropBtn.Visible = false

		minimize.Text = "+"

	else

		TweenService:Create(main,TweenInfo.new(0.25),{
			Size = UDim2.new(0,260,0,220)
		}):Play()

		weightBtn.Visible = true
		doubleBtn.Visible = true
		dropBtn.Visible = true

		minimize.Text = "-"
	end
end)

--================ FAST CLICK TOOL ================--

local equippedTool = nil
local currentIndex = 1

local function clickTool(tool)

	if not tool or not humanoid then
		return
	end

	if equippedTool ~= tool then

		humanoid:EquipTool(tool)
		equippedTool = tool

		task.wait(equipDelay)
	end

	pcall(function()
		tool:Activate()
	end)

	VirtualUser:CaptureController()

	VirtualUser:Button1Down(
		Vector2.new(9999,9999),
		workspace.CurrentCamera.CFrame
	)

	task.wait()

	VirtualUser:Button1Up(
		Vector2.new(9999,9999),
		workspace.CurrentCamera.CFrame
	)
end

--================ AUTO WEIGHT ================--

task.spawn(function()

	while true do

		if weightEnabled and not dropping then

			local tool =
				backpack:FindFirstChild("Weight")
				or backpack:FindFirstChild("weight")
				or backpack:FindFirstChild("Peso")
				or backpack:FindFirstChild("peso")

			if tool then
				clickTool(tool)
			end
		end

		task.wait(clickDelay)
	end
end)

--================ DOUBLE + TRIPLE AUTO ================--

task.spawn(function()

	while true do

		if doubleEnabled and not dropping then

			local tools = {}

			local double =
				backpack:FindFirstChild("Double Weight")
				or backpack:FindFirstChild("peso duplo")

			local triple =
				backpack:FindFirstChild("Triple Weight")
				or backpack:FindFirstChild("peso triplo")

			if double then
				table.insert(tools,double)
			end

			if triple then
				table.insert(tools,triple)
			end

			if #tools > 0 then

				if currentIndex > #tools then
					currentIndex = 1
				end

				clickTool(tools[currentIndex])

				currentIndex += 1
			end
		end

		task.wait(clickDelay)
	end
end)

--================ AUTO DROP FIXED ================--

task.spawn(function()

	while true do

		if dropEnabled and humanoid then

			for _, item in ipairs(backpack:GetChildren()) do

				if item:IsA("Tool")
				and (
					item.Name == "Double Weight"
					or item.Name == "peso duplo"
					or item.Name == "Triple Weight"
					or item.Name == "peso triplo"
				) then

					dropping = true

					equippedTool = nil

					humanoid:UnequipTools()

					task.wait(0.05)

					humanoid:EquipTool(item)

					task.wait(0.05)

					item.Parent = workspace

					task.wait(0.15)

					dropping = false
				end
			end
		end

		task.wait(0.3)
	end
end)
