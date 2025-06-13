local Lighting = game:GetService("Lighting")
local Players = game:GetService("Players")
local LocalPlayer = Players.LocalPlayer

-- Kiểm tra hiệu ứng có liên quan đến chiêu thức của mình không
local function isSkillEffect(obj)
	if obj:IsDescendantOf(LocalPlayer.Character) then
		return true
	end

	-- Nếu hiệu ứng có tên chứa từ khoá kỹ năng hoặc player name
	local name = obj.Name:lower()
	local charName = LocalPlayer.Name:lower()
	if name:find("effect") or name:find("skill") or name:find("attack") or name:find(charName) then
		return true
	end

	return false
end

for _, obj in ipairs(game:GetDescendants()) do
	-- Giảm vật liệu
	if obj:IsA("BasePart") then
		obj.Material = Enum.Material.SmoothPlastic
		obj.Reflectance = 0
		obj.CastShadow = false
	end

	-- Xóa hình ảnh không cần thiết
	if obj:IsA("Decal") or obj:IsA("Texture") then
		pcall(function()
			obj:Destroy()
		end)
	end

	-- Tắt hiệu ứng không phải kỹ năng
	if (obj:IsA("ParticleEmitter") or obj:IsA("Trail") or obj:IsA("Beam")) and not isSkillEffect(obj) then
		obj.Enabled = false
	end
end

-- Tối ưu ánh sáng
Lighting.GlobalShadows = false
Lighting.FogEnd = 100000
Lighting.Brightness = 1
Lighting.OutdoorAmbient = Color3.fromRGB(127, 127, 127)

-- Giảm chất lượng render
pcall(function()
	settings().Rendering.QualityLevel = Enum.QualityLevel.Level01
end)local RunService = game:GetService("RunService")

-- Tạo GUI
local fpsGui = Instance.new("ScreenGui")
fpsGui.Name = "FPSDisplay"
fpsGui.Parent = game:GetService("CoreGui")

local fpsLabel = Instance.new("TextLabel")
fpsLabel.Size = UDim2.new(0, 150, 0, 35)
fpsLabel.Position = UDim2.new(0, 10, 0, 10)
fpsLabel.BackgroundTransparency = 0.4
fpsLabel.BackgroundColor3 = Color3.fromRGB(0, 0, 0)
fpsLabel.BorderSizePixel = 0
fpsLabel.TextColor3 = Color3.fromRGB(0, 255, 0)
fpsLabel.Font = Enum.Font.SourceSansBold
fpsLabel.TextSize = 22
fpsLabel.Text = "FPS: ..."
fpsLabel.Parent = fpsGui

-- Tính toán FPS
local fps = 0
local frames = 0
local lastTime = tick()

RunService.RenderStepped:Connect(function()
	frames = frames + 1
	if tick() - lastTime >= 1 then
		fps = frames
		frames = 0
		lastTime = tick()
		fpsLabel.Text = "FPS: "..fps
	end
end)
