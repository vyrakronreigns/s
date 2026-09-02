--[[
╔══════════════════════════════════════════════════════════════════════╗
║                         ARG CORE V7                                 ║
║                     ENGINEERED BY ALEX                              ║
║               LOCAL / STABLE BUILD / BLUE + GOLD                    ║
╚══════════════════════════════════════════════════════════════════════╝

PUT THIS ONE LOCALSCRIPT IN:

StarterPlayer
└── StarterPlayerScripts
    └── ARG_CORE_V7

NOTES:
- LocalScript only.
- Combat logic is intentionally limited to Workspace.ARG_TestDummies
- Blue + gold refresh with cleaner/stabler layout.
]]

-- ================================================================
-- SERVICES
-- ================================================================
local Players      = game:GetService("Players")
local RunService   = game:GetService("RunService")
local TweenService = game:GetService("TweenService")
local UIS          = game:GetService("UserInputService")
local Lighting     = game:GetService("Lighting")
local SoundService = game:GetService("SoundService")
local Workspace    = game:GetService("Workspace")

local LP        = Players.LocalPlayer
local PlayerGui = LP:WaitForChild("PlayerGui")
local Camera    = Workspace.CurrentCamera

-- ================================================================
-- REMOVE OLD COPY
-- ================================================================
do
	local old = PlayerGui:FindFirstChild("ARG_CORE_V7")
	if old then
		old:Destroy()
	end

	local oldMusic = SoundService:FindFirstChild("ARG_HIP_HOP_PLAYER")
	if oldMusic then
		oldMusic:Destroy()
	end
end

-- ================================================================
-- SETTINGS
-- ================================================================
local ARG = {
	-- COMBAT LAB
	KillAura     = false,
	KillPower    = 900000,
	KillRange    = 700,

	MurderAura   = false,
	MurderPower  = 5000,

	NoChaseAura  = false,
	NoChasePower = 30000,

	AttackDelay  = 0.10,

	-- MOVEMENT
	TPWalk       = false,
	TPSpeed      = 30,
	TPStrength   = 5,

	Fly          = false,
	FlySpeed     = 100,

	FlyGodMode   = false,
	FlyGodSpeed  = 160,

	Dash         = true,
	DashPower    = 120,

	Sprint       = false,
	SprintSpeed  = 32,

	JumpPower    = 100,
	InfiniteJump = false,
	Noclip       = false,

	-- PLAYER
	GodMode        = false,
	GodModeEmote   = false,
	GodEmoteId     = "rbxassetid://507771019",

	AntiGrabGlitch = false,
	AntiRagdoll    = false,
	AntiFling      = false,
	AutoRecover    = false,

	-- ESP
	ESP          = false,
	ESPNames     = true,
	ESPDistance  = true,
	ESPHealth    = true,
	ESPHighlight = true,

	-- WORLD
	FullBright = false,
	NightMode  = false,
	RemoveFog  = false,

	-- TARGET
	TargetName = "",

	-- LISTS
	Friends = {},
	Targets = {},
	Protect = {},
	Bypass  = {},

	-- LOCAL NOTIFY
	NotifyPreview  = false,
	NotifyMessage  = "ARG CORE V7",
	NotifyInterval = 1,

	-- MUSIC
	MusicEnabled = false,
	MusicVolume  = 55,
	MusicIndex   = 1,

	Playlist = {
		{ Name = "HIP HOP 01", Id = "" },
		{ Name = "HIP HOP 02", Id = "" },
		{ Name = "HIP HOP 03", Id = "" },
		{ Name = "HIP HOP 04", Id = "" },
		{ Name = "HIP HOP 05", Id = "" },
	}
}

-- ================================================================
-- THEME // BLUE + GOLD
-- ================================================================
local T = {
	BG       = Color3.fromRGB(3, 8, 18),
	BG2      = Color3.fromRGB(7, 15, 28),
	SIDEBAR  = Color3.fromRGB(5, 12, 24),
	CARD     = Color3.fromRGB(11, 21, 38),
	RAISED   = Color3.fromRGB(18, 31, 52),
	BORDER   = Color3.fromRGB(32, 77, 146),

	BLUE     = Color3.fromRGB(35, 132, 255),
	BLUE2    = Color3.fromRGB(17, 88, 212),
	DARKBLUE = Color3.fromRGB(7, 41, 102),

	GOLD     = Color3.fromRGB(255, 201, 64),
	GOLD2    = Color3.fromRGB(212, 163, 35),

	TEXT     = Color3.fromRGB(244, 247, 255),
	MUTED    = Color3.fromRGB(157, 174, 201),
	DIM      = Color3.fromRGB(91, 110, 140),
	OFF      = Color3.fromRGB(56, 68, 87),
}

-- ================================================================
-- CONNECTION MANAGER
-- ================================================================
local Alive = true
local Connections = {}

local function Bind(signal, callback)
	local connection = signal:Connect(callback)
	table.insert(Connections, connection)
	return connection
end

local function DisconnectEverything()
	for i = #Connections, 1, -1 do
		local connection = Connections[i]
		Connections[i] = nil

		pcall(function()
			connection:Disconnect()
		end)
	end
end

-- ================================================================
-- CHARACTER
-- ================================================================
local Character
local Humanoid
local HRP
local GodTrack

local BaseWalkSpeed = 16
local BaseJumpPower = 50
local BaseMaxHealth = 100

local CollisionCache = {}

local function RestoreCollision()
	for part, oldValue in pairs(CollisionCache) do
		if part and part.Parent then
			pcall(function()
				part.CanCollide = oldValue
			end)
		end
	end

	table.clear(CollisionCache)
end

local function StopGodEmote()
	if GodTrack then
		pcall(function()
			GodTrack:Stop(0.15)
		end)
		GodTrack = nil
	end
end

local function LoadCharacter(character)
	RestoreCollision()
	StopGodEmote()

	Character = character
	Humanoid = character:WaitForChild("Humanoid")
	HRP = character:WaitForChild("HumanoidRootPart")

	BaseWalkSpeed = Humanoid.WalkSpeed
	BaseJumpPower = Humanoid.JumpPower
	BaseMaxHealth = Humanoid.MaxHealth

	Humanoid.UseJumpPower = true
	Humanoid.JumpPower = ARG.JumpPower
end

if LP.Character then
	LoadCharacter(LP.Character)
end

Bind(LP.CharacterAdded, LoadCharacter)

-- ================================================================
-- TEST DUMMIES
-- ================================================================
local DummyFolder = Workspace:FindFirstChild("ARG_TestDummies")

if not DummyFolder then
	DummyFolder = Instance.new("Folder")
	DummyFolder.Name = "ARG_TestDummies"
	DummyFolder.Parent = Workspace
end

-- ================================================================
-- LIGHTING BACKUP
-- ================================================================
local OriginalLighting = {
	Brightness     = Lighting.Brightness,
	ClockTime      = Lighting.ClockTime,
	FogStart       = Lighting.FogStart,
	FogEnd         = Lighting.FogEnd,
	Ambient        = Lighting.Ambient,
	OutdoorAmbient = Lighting.OutdoorAmbient,
	Gravity        = Workspace.Gravity,
}

local function ApplyLighting()
	Lighting.Brightness = OriginalLighting.Brightness
	Lighting.ClockTime = OriginalLighting.ClockTime
	Lighting.FogStart = OriginalLighting.FogStart
	Lighting.FogEnd = OriginalLighting.FogEnd
	Lighting.Ambient = OriginalLighting.Ambient
	Lighting.OutdoorAmbient = OriginalLighting.OutdoorAmbient

	if ARG.FullBright then
		Lighting.Brightness = 3
		Lighting.ClockTime = 14
		Lighting.Ambient = Color3.fromRGB(185, 195, 210)
		Lighting.OutdoorAmbient = Color3.fromRGB(185, 195, 210)

	elseif ARG.NightMode then
		Lighting.Brightness = 1
		Lighting.ClockTime = 0
		Lighting.Ambient = Color3.fromRGB(40, 55, 82)
		Lighting.OutdoorAmbient = Color3.fromRGB(30, 40, 60)
	end

	if ARG.RemoveFog then
		Lighting.FogStart = 0
		Lighting.FogEnd = 1000000
	end
end

-- ================================================================
-- HELPERS
-- ================================================================
local function Lower(value)
	return string.lower(tostring(value or ""))
end

local function ParseList(text)
	local result = {}
	local seen = {}

	text = tostring(text or ""):gsub(",", " ")

	for word in text:gmatch("%S+") do
		local name = Lower(word)

		if name ~= "" and not seen[name] then
			seen[name] = true
			table.insert(result, name)
		end
	end

	return result
end

local function ListContains(list, player)
	if not player then
		return false
	end

	local username = Lower(player.Name)
	local display = Lower(player.DisplayName)

	for _, entry in ipairs(list) do
		if username == entry or display == entry then
			return true
		end
	end

	return false
end

local function PlayerAllowed(player)
	if not player or player == LP then
		return false
	end

	if ListContains(ARG.Protect, player) then
		return false
	end

	if ListContains(ARG.Bypass, player) then
		return true
	end

	if ListContains(ARG.Friends, player) then
		return false
	end

	if #ARG.Targets > 0 then
		return ListContains(ARG.Targets, player)
	end

	return true
end

local function FindPlayer(query)
	query = Lower(query)

	if query == "" then
		return nil
	end

	for _, player in ipairs(Players:GetPlayers()) do
		if player ~= LP and (
			Lower(player.Name) == query
			or Lower(player.DisplayName) == query
		) then
			return player
		end
	end

	for _, player in ipairs(Players:GetPlayers()) do
		if player ~= LP and (
			Lower(player.Name):find(query, 1, true)
			or Lower(player.DisplayName):find(query, 1, true)
		) then
			return player
		end
	end

	return nil
end

-- ================================================================
-- UI HELPERS
-- ================================================================
local function Corner(parent, radius)
	local object = Instance.new("UICorner")
	object.CornerRadius = UDim.new(0, radius or 8)
	object.Parent = parent
	return object
end

local function Stroke(parent, color, thickness, transparency)
	local object = Instance.new("UIStroke")
	object.Color = color or T.BORDER
	object.Thickness = thickness or 1
	object.Transparency = transparency or 0
	object.Parent = parent
	return object
end

local function Tween(object, properties, time)
	if not Alive or not object or not object.Parent then
		return
	end

	local tween = TweenService:Create(
		object,
		TweenInfo.new(
			time or 0.15,
			Enum.EasingStyle.Quint,
			Enum.EasingDirection.Out
		),
		properties
	)

	tween:Play()
	return tween
end

local function Label(parent, text, size, color, font)
	local object = Instance.new("TextLabel")

	object.BackgroundTransparency = 1
	object.Text = text or ""
	object.TextSize = size or 10
	object.TextColor3 = color or T.TEXT
	object.Font = font or Enum.Font.Gotham
	object.TextXAlignment = Enum.TextXAlignment.Left
	object.Parent = parent

	return object
end

-- ================================================================
-- MAIN GUI
-- ================================================================
local GUI = Instance.new("ScreenGui")

GUI.Name = "ARG_CORE_V7"
GUI.ResetOnSpawn = false
GUI.ZIndexBehavior = Enum.ZIndexBehavior.Sibling
GUI.DisplayOrder = 50
GUI.Parent = PlayerGui

local UIScale = Instance.new("UIScale")
UIScale.Parent = GUI

local BaseScale = 1

local function UpdateScale()
	Camera = Workspace.CurrentCamera or Camera

	if not Camera then
		return
	end

	local size = Camera.ViewportSize

	UIScale.Scale = math.clamp(
		math.min(
			size.X / 1500,
			size.Y / 850
		) * BaseScale,
		0.48,
		1.1
	)
end

UpdateScale()

if Camera then
	Bind(
		Camera:GetPropertyChangedSignal("ViewportSize"),
		UpdateScale
	)
end

Bind(
	Workspace:GetPropertyChangedSignal("CurrentCamera"),
	function()
		Camera = Workspace.CurrentCamera
		UpdateScale()
	end
)

-- ================================================================
-- MAIN WINDOW
-- ================================================================
local WW = 1220
local WH = 700

local Main = Instance.new("Frame")

Main.Size = UDim2.fromOffset(WW, WH)
Main.Position = UDim2.new(0.5, -WW / 2, 0.5, -WH / 2)
Main.BackgroundColor3 = T.BG
Main.BorderSizePixel = 0
Main.ClipsDescendants = true
Main.Parent = GUI

Corner(Main, 18)
Stroke(Main, T.BLUE2, 1.5, 0.02)

local OuterGlow = Instance.new("UIStroke")

OuterGlow.Color = T.GOLD
OuterGlow.Thickness = 3
OuterGlow.Transparency = 0.84
OuterGlow.Parent = Main

-- ================================================================
-- HEADER
-- ================================================================
local Header = Instance.new("Frame")

Header.Size = UDim2.new(1, 0, 0, 74)
Header.BackgroundTransparency = 1
Header.Active = true
Header.Parent = Main

-- ================================================================
-- LOGO
-- ================================================================
local LogoGlow = Instance.new("Frame")

LogoGlow.Size = UDim2.fromOffset(62, 62)
LogoGlow.Position = UDim2.fromOffset(14, 6)
LogoGlow.BackgroundColor3 = T.BLUE
LogoGlow.BackgroundTransparency = 0.72
LogoGlow.BorderSizePixel = 0
LogoGlow.Parent = Header

Corner(LogoGlow, 13)

local Logo = Instance.new("Frame")

Logo.Size = UDim2.fromOffset(50, 50)
Logo.Position = UDim2.fromOffset(20, 12)
Logo.BackgroundColor3 = Color3.fromRGB(8, 16, 34)
Logo.BorderSizePixel = 0
Logo.Parent = Header

Corner(Logo, 10)
Stroke(Logo, T.GOLD, 2.5, 0)

local LogoText = Label(
	Logo,
	"A",
	28,
	T.GOLD,
	Enum.Font.GothamBlack
)

LogoText.Size = UDim2.fromScale(1, 1)
LogoText.TextXAlignment = Enum.TextXAlignment.Center
LogoText.TextYAlignment = Enum.TextYAlignment.Center

-- ================================================================
-- TITLE
-- ================================================================
local Title = Label(
	Header,
	"ARG CORE V7",
	25,
	T.TEXT,
	Enum.Font.GothamBlack
)

Title.Size = UDim2.fromOffset(320, 30)
Title.Position = UDim2.fromOffset(88, 11)

local Credit = Label(
	Header,
	"ENGINEERED BY ALEX",
	10,
	T.GOLD,
	Enum.Font.GothamBold
)

Credit.Size = UDim2.fromOffset(320, 16)
Credit.Position = UDim2.fromOffset(90, 43)

local Status = Label(
	Header,
	"SYSTEM ONLINE",
	9,
	T.BLUE,
	Enum.Font.GothamBold
)

Status.Size = UDim2.fromOffset(360, 30)
Status.Position = UDim2.new(0.5, -145, 0, 22)

-- ================================================================
-- SEARCH
-- ================================================================
local Search = Instance.new("TextBox")

Search.Size = UDim2.fromOffset(245, 38)
Search.Position = UDim2.new(1, -375, 0, 17)
Search.BackgroundColor3 = T.BG2
Search.Text = ""
Search.PlaceholderText = "Search features..."
Search.PlaceholderColor3 = T.DIM
Search.TextColor3 = T.TEXT
Search.TextSize = 10
Search.Font = Enum.Font.Gotham
Search.ClearTextOnFocus = false
Search.BorderSizePixel = 0
Search.Parent = Header

Corner(Search, 8)
Stroke(Search, T.BORDER, 1, 0.15)

-- ================================================================
-- WINDOW BUTTONS
-- ================================================================
local Hide = Instance.new("TextButton")

Hide.Size = UDim2.fromOffset(42, 38)
Hide.Position = UDim2.new(1, -115, 0, 17)
Hide.BackgroundColor3 = T.BG2
Hide.Text = "-"
Hide.TextColor3 = T.GOLD
Hide.TextSize = 16
Hide.Font = Enum.Font.GothamBold
Hide.BorderSizePixel = 0
Hide.Parent = Header

Corner(Hide, 8)

local Close = Instance.new("TextButton")

Close.Size = UDim2.fromOffset(42, 38)
Close.Position = UDim2.new(1, -63, 0, 17)
Close.BackgroundColor3 = T.BG2
Close.Text = "X"
Close.TextColor3 = T.GOLD
Close.TextSize = 16
Close.Font = Enum.Font.GothamBold
Close.BorderSizePixel = 0
Close.Parent = Header

Corner(Close, 8)

-- ================================================================
-- SIDEBAR
-- ================================================================
local Sidebar = Instance.new("ScrollingFrame")

Sidebar.Size = UDim2.new(0, 190, 1, -74)
Sidebar.Position = UDim2.fromOffset(0, 74)
Sidebar.BackgroundColor3 = T.SIDEBAR
Sidebar.BorderSizePixel = 0
Sidebar.ScrollBarThickness = 2
Sidebar.ScrollBarImageColor3 = T.GOLD
Sidebar.AutomaticCanvasSize = Enum.AutomaticSize.Y
Sidebar.CanvasSize = UDim2.new()
Sidebar.Parent = Main

local SidePadding = Instance.new("UIPadding")

SidePadding.PaddingLeft = UDim.new(0, 12)
SidePadding.PaddingRight = UDim.new(0, 12)
SidePadding.PaddingTop = UDim.new(0, 10)
SidePadding.Parent = Sidebar

local SideLayout = Instance.new("UIListLayout")

SideLayout.Padding = UDim.new(0, 5)
SideLayout.Parent = Sidebar

-- ================================================================
-- CONTENT
-- ================================================================
local Content = Instance.new("Frame")

Content.Size = UDim2.new(1, -190, 1, -74)
Content.Position = UDim2.fromOffset(190, 74)
Content.BackgroundTransparency = 1
Content.ClipsDescendants = true
Content.Parent = Main

local TabData = {
	"HOME",
	"COMBAT",
	"MOVEMENT",
	"PLAYER",
	"VISUALS",
	"TARGETS",
	"WHITELISTS",
	"MUSIC",
	"WORLD",
	"SETTINGS",
	"CREDITS"
}

local Panels = {}
local TabButtons = {}
local SearchRows = {}
local ToggleSetters = {}

local function Register(row, name)
	table.insert(
		SearchRows,
		{
			Row = row,
			Name = Lower(name)
		}
	)
end

local function OpenTab(index)
	for i, panel in ipairs(Panels) do
		panel.Visible = i == index

		local button = TabButtons[i]

		if button then
			Tween(
				button,
				{
					BackgroundTransparency = i == index and 0.05 or 1,
					BackgroundColor3 = i == index and T.DARKBLUE or T.SIDEBAR
				},
				0.13
			)
		end
	end
end

for index, name in ipairs(TabData) do
	local button = Instance.new("TextButton")

	button.Size = UDim2.new(1, 0, 0, 40)
	button.BackgroundColor3 = T.SIDEBAR
	button.BackgroundTransparency = 1
	button.Text = "   " .. name
	button.TextColor3 = T.TEXT
	button.TextSize = 10
	button.Font = Enum.Font.GothamBold
	button.TextXAlignment = Enum.TextXAlignment.Left
	button.BorderSizePixel = 0
	button.Parent = Sidebar

	Corner(button, 7)

	local panel = Instance.new("ScrollingFrame")

	panel.Size = UDim2.fromScale(1, 1)
	panel.BackgroundTransparency = 1
	panel.BorderSizePixel = 0
	panel.ScrollBarThickness = 3
	panel.ScrollBarImageColor3 = T.GOLD
	panel.AutomaticCanvasSize = Enum.AutomaticSize.Y
	panel.CanvasSize = UDim2.new()
	panel.Visible = false
	panel.Parent = Content

	local padding = Instance.new("UIPadding")

	padding.PaddingLeft = UDim.new(0, 14)
	padding.PaddingRight = UDim.new(0, 14)
	padding.PaddingTop = UDim.new(0, 12)
	padding.PaddingBottom = UDim.new(0, 20)
	padding.Parent = panel

	local layout = Instance.new("UIListLayout")

	layout.Padding = UDim.new(0, 8)
	layout.SortOrder = Enum.SortOrder.LayoutOrder
	layout.Parent = panel

	Panels[index] = panel
	TabButtons[index] = button

	Bind(
		button.MouseButton1Click,
		function()
			OpenTab(index)
		end
	)
end

-- ================================================================
-- SECTION
-- ================================================================
local function Section(parent, name)
	local frame = Instance.new("Frame")

	frame.Size = UDim2.new(1, -6, 0, 0)
	frame.AutomaticSize = Enum.AutomaticSize.Y
	frame.BackgroundColor3 = T.BG2
	frame.BorderSizePixel = 0
	frame.Parent = parent

	Corner(frame, 10)
	Stroke(frame, T.BORDER, 1, 0.18)

	local layout = Instance.new("UIListLayout")

	layout.Padding = UDim.new(0, 4)
	layout.Parent = frame

	local padding = Instance.new("UIPadding")

	padding.PaddingLeft = UDim.new(0, 10)
	padding.PaddingRight = UDim.new(0, 10)
	padding.PaddingTop = UDim.new(0, 8)
	padding.PaddingBottom = UDim.new(0, 10)
	padding.Parent = frame

	local head = Instance.new("Frame")

	head.Size = UDim2.new(1, 0, 0, 25)
	head.BackgroundTransparency = 1
	head.Parent = frame

	local title = Label(
		head,
		name,
		11,
		T.GOLD,
		Enum.Font.GothamBold
	)

	title.Size = UDim2.fromOffset(300, 25)
	title.TextYAlignment = Enum.TextYAlignment.Center

	local line = Instance.new("Frame")

	line.Size = UDim2.new(1, -305, 0, 1)
	line.Position = UDim2.new(0, 305, 0.5, 0)
	line.BackgroundColor3 = T.BLUE
	line.BackgroundTransparency = 0.28
	line.BorderSizePixel = 0
	line.Parent = head

	return frame
end

-- ================================================================
-- GLOBAL INPUT STATE
-- ================================================================
local ActiveSliderUpdate = nil
local DraggingWindow = false
local DragStart = nil
local DragPosition = nil

-- ================================================================
-- TOGGLE
-- ================================================================
local function Toggle(parent, name, default, callback)
	local row = Instance.new("Frame")

	row.Size = UDim2.new(1, 0, 0, 42)
	row.BackgroundColor3 = T.CARD
	row.BorderSizePixel = 0
	row.Parent = parent

	Corner(row, 7)
	Stroke(row, T.BORDER, 1, 0.48)
	Register(row, name)

	local text = Label(
		row,
		name,
		10,
		T.TEXT,
		Enum.Font.GothamBold
	)

	text.Size = UDim2.new(1, -80, 1, 0)
	text.Position = UDim2.fromOffset(13, 0)
	text.TextYAlignment = Enum.TextYAlignment.Center

	local switch = Instance.new("TextButton")

	switch.Size = UDim2.fromOffset(46, 22)
	switch.Position = UDim2.new(1, -58, 0.5, -11)
	switch.BackgroundColor3 = T.OFF
	switch.Text = ""
	switch.BorderSizePixel = 0
	switch.Parent = row

	Corner(switch, 11)

	local knob = Instance.new("Frame")

	knob.Size = UDim2.fromOffset(16, 16)
	knob.Position = UDim2.fromOffset(3, 3)
	knob.BackgroundColor3 = Color3.fromRGB(191, 197, 208)
	knob.BorderSizePixel = 0
	knob.Parent = switch

	Corner(knob, 8)

	local state = default == true

	local function Set(value, fire)
		state = value == true

		Tween(
			switch,
			{
				BackgroundColor3 = state and T.BLUE2 or T.OFF
			},
			0.12
		)

		Tween(
			knob,
			{
				Position = state
					and UDim2.new(1, -19, 0, 3)
					or UDim2.fromOffset(3, 3),

				BackgroundColor3 = state
					and T.GOLD
					or Color3.fromRGB(191, 197, 208)
			},
			0.12
		)

		if fire ~= false and callback then
			callback(state)
		end
	end

	ToggleSetters[name] = Set

	Bind(
		switch.MouseButton1Click,
		function()
			Set(not state, true)
		end
	)

	Set(state, false)

	return Set
end

local function SetToggle(name, value, fire)
	local setter = ToggleSetters[name]

	if setter then
		setter(value, fire)
	end
end

-- ================================================================
-- SLIDER
-- ================================================================
local function Slider(parent, name, minimum, maximum, default, callback)
	local row = Instance.new("Frame")

	row.Size = UDim2.new(1, 0, 0, 62)
	row.BackgroundColor3 = T.CARD
	row.BorderSizePixel = 0
	row.Parent = parent

	Corner(row, 7)
	Stroke(row, T.BORDER, 1, 0.48)
	Register(row, name)

	local nameLabel = Label(
		row,
		name,
		9,
		T.MUTED,
		Enum.Font.GothamBold
	)

	nameLabel.Size = UDim2.new(1, -100, 0, 20)
	nameLabel.Position = UDim2.fromOffset(12, 5)

	local valueLabel = Label(
		row,
		tostring(default),
		10,
		T.TEXT,
		Enum.Font.GothamBold
	)

	valueLabel.Size = UDim2.fromOffset(90, 20)
	valueLabel.Position = UDim2.new(1, -100, 0, 5)
	valueLabel.TextXAlignment = Enum.TextXAlignment.Right

	local bar = Instance.new("Frame")

	bar.Size = UDim2.new(1, -24, 0, 5)
	bar.Position = UDim2.fromOffset(12, 42)
	bar.BackgroundColor3 = T.RAISED
	bar.BorderSizePixel = 0
	bar.Parent = row

	Corner(bar, 3)

	local percent = math.clamp(
		(default - minimum) / (maximum - minimum),
		0,
		1
	)

	local fill = Instance.new("Frame")

	fill.Size = UDim2.new(percent, 0, 1, 0)
	fill.BackgroundColor3 = T.BLUE
	fill.BorderSizePixel = 0
	fill.Parent = bar

	Corner(fill, 3)

	local knob = Instance.new("Frame")

	knob.Size = UDim2.fromOffset(14, 14)
	knob.Position = UDim2.new(percent, -7, 0.5, -7)
	knob.BackgroundColor3 = T.GOLD
	knob.BorderSizePixel = 0
	knob.Parent = bar

	Corner(knob, 7)

	local input = Instance.new("TextButton")

	input.Size = UDim2.new(1, 0, 0, 35)
	input.Position = UDim2.new(0, 0, 1, -35)
	input.BackgroundTransparency = 1
	input.Text = ""
	input.Parent = row

	local function Update(x)
		if not bar.Parent then
			return
		end

		local width = bar.AbsoluteSize.X

		if width <= 0 then
			return
		end

		local p = math.clamp(
			(x - bar.AbsolutePosition.X) / width,
			0,
			1
		)

		local value = math.floor(
			minimum
				+ p * (maximum - minimum)
				+ 0.5
		)

		fill.Size = UDim2.new(p, 0, 1, 0)
		knob.Position = UDim2.new(p, -7, 0.5, -7)
		valueLabel.Text = tostring(value)

		if callback then
			callback(value)
		end
	end

	Bind(
		input.InputBegan,
		function(i)
			if i.UserInputType == Enum.UserInputType.MouseButton1
				or i.UserInputType == Enum.UserInputType.Touch
			then
				ActiveSliderUpdate = Update
				Update(i.Position.X)
			end
		end
	)
end

-- ================================================================
-- INPUT BOX
-- ================================================================
local function Input(parent, name, placeholder, callback)
	local row = Instance.new("Frame")

	row.Size = UDim2.new(1, 0, 0, 52)
	row.BackgroundColor3 = T.CARD
	row.BorderSizePixel = 0
	row.Parent = parent

	Corner(row, 7)
	Stroke(row, T.BORDER, 1, 0.48)
	Register(row, name)

	local title = Label(
		row,
		name,
		9,
		T.MUTED,
		Enum.Font.GothamBold
	)

	title.Size = UDim2.new(0.38, 0, 1, 0)
	title.Position = UDim2.fromOffset(12, 0)
	title.TextYAlignment = Enum.TextYAlignment.Center

	local box = Instance.new("TextBox")

	box.Size = UDim2.new(0.58, -12, 0, 30)
	box.Position = UDim2.new(0.42, 0, 0.5, -15)
	box.BackgroundColor3 = T.RAISED
	box.Text = ""
	box.PlaceholderText = placeholder
	box.PlaceholderColor3 = T.DIM
	box.TextColor3 = T.TEXT
	box.TextSize = 9
	box.Font = Enum.Font.Gotham
	box.ClearTextOnFocus = false
	box.BorderSizePixel = 0
	box.Parent = row

	Corner(box, 5)

	Bind(
		box.FocusLost,
		function()
			if callback then
				callback(box.Text)
			end
		end
	)

	return box
end

-- ================================================================
-- BUTTON
-- ================================================================
local function Button(parent, name, buttonText, callback)
	local row = Instance.new("Frame")

	row.Size = UDim2.new(1, 0, 0, 42)
	row.BackgroundColor3 = T.CARD
	row.BorderSizePixel = 0
	row.Parent = parent

	Corner(row, 7)
	Stroke(row, T.BORDER, 1, 0.48)
	Register(row, name)

	local title = Label(
		row,
		name,
		10,
		T.TEXT,
		Enum.Font.GothamBold
	)

	title.Size = UDim2.new(1, -115, 1, 0)
	title.Position = UDim2.fromOffset(12, 0)
	title.TextYAlignment = Enum.TextYAlignment.Center

	local button = Instance.new("TextButton")

	button.Size = UDim2.fromOffset(90, 25)
	button.Position = UDim2.new(1, -101, 0.5, -12)
	button.BackgroundColor3 = T.BLUE2
	button.Text = buttonText or "RUN"
	button.TextColor3 = T.TEXT
	button.TextSize = 8
	button.Font = Enum.Font.GothamBold
	button.BorderSizePixel = 0
	button.Parent = row

	Corner(button, 5)

	Bind(
		button.MouseButton1Click,
		function()
			if callback then
				callback()
			end
		end
	)

	return button
end

-- ================================================================
-- NOTIFICATION SYSTEM
-- ================================================================
local NotifyHolder = Instance.new("Frame")

NotifyHolder.Size = UDim2.fromOffset(290, 500)
NotifyHolder.Position = UDim2.new(1, -305, 0, 80)
NotifyHolder.BackgroundTransparency = 1
NotifyHolder.Parent = GUI

local NotifyLayout = Instance.new("UIListLayout")

NotifyLayout.Padding = UDim.new(0, 7)
NotifyLayout.VerticalAlignment = Enum.VerticalAlignment.Bottom
NotifyLayout.Parent = NotifyHolder

local NotificationCards = {}

local function Notify(title, body)
	if not Alive or not GUI.Parent then
		return
	end

	while #NotificationCards >= 4 do
		local oldest = table.remove(NotificationCards, 1)

		if oldest and oldest.Parent then
			oldest:Destroy()
		end
	end

	local card = Instance.new("Frame")

	card.Size = UDim2.new(1, 0, 0, 58)
	card.BackgroundColor3 = T.CARD
	card.BorderSizePixel = 0
	card.Parent = NotifyHolder

	Corner(card, 9)
	Stroke(card, T.GOLD, 1.3, 0.08)

	local titleText = Label(
		card,
		title,
		11,
		T.TEXT,
		Enum.Font.GothamBold
	)

	titleText.Size = UDim2.new(1, -25, 0, 18)
	titleText.Position = UDim2.fromOffset(14, 7)

	local bodyText = Label(
		card,
		body,
		9,
		T.MUTED,
		Enum.Font.Gotham
	)

	bodyText.Size = UDim2.new(1, -25, 0, 24)
	bodyText.Position = UDim2.fromOffset(14, 28)
	bodyText.TextTruncate = Enum.TextTruncate.AtEnd

	table.insert(NotificationCards, card)

	task.delay(
		4,
		function()
			if card and card.Parent then
				card:Destroy()
			end

			local index = table.find(
				NotificationCards,
				card
			)

			if index then
				table.remove(
					NotificationCards,
					index
				)
			end
		end
	)
end

-- ================================================================
-- MUSIC OBJECT
-- ================================================================
local Music = Instance.new("Sound")

Music.Name = "ARG_HIP_HOP_PLAYER"
Music.Volume = ARG.MusicVolume / 100
Music.Looped = false
Music.Parent = SoundService

-- ================================================================
-- HOME
-- ================================================================
local PanicNames = {
	"KILL AURA",
	"MURDER AURA",
	"NO-CHASE AURA",
	"TP WALK",
	"FLY",
	"FLY GOD MODE",
	"SPRINT",
	"INFINITE JUMP",
	"NOCLIP",
	"GOD MODE",
	"GOD MODE EMOTE",
	"ANTI-GRAB GLITCH",
	"ANTI-RAGDOLL",
	"ANTI-FLING",
	"AUTO RECOVER",
	"ESP",
	"LOCAL NOTIFY PREVIEW",
	"FULL BRIGHT",
	"NIGHT MODE",
	"REMOVE FOG"
}

do
	local S = Section(
		Panels[1],
		"ARG CORE V7"
	)

	local welcome = Label(
		S,
		"WELCOME BACK, "
			.. string.upper(LP.DisplayName),
		19,
		T.TEXT,
		Enum.Font.GothamBlack
	)

	welcome.Size = UDim2.new(1, 0, 0, 32)

	local info = Label(
		S,
		"LOCAL STABLE BUILD  //  BLUE + GOLD  //  ENGINEERED BY ALEX",
		10,
		T.GOLD,
		Enum.Font.GothamBold
	)

	info.Size = UDim2.new(1, 0, 0, 25)

	Button(
		S,
		"PANIC DISABLE",
		"DISABLE",
		function()
			for _, name in ipairs(PanicNames) do
				local setter = ToggleSetters[name]

				if setter then
					setter(false, true)
				end
			end

			ARG.MusicEnabled = false
			Music:Stop()

			Notify(
				"ARG CORE V7",
				"Active modules disabled."
			)
		end
	)
end

-- ================================================================
-- COMBAT
-- ================================================================
do
	local S = Section(
		Panels[2],
		"COMBAT LAB // TEST DUMMIES"
	)

	Toggle(
		S,
		"KILL AURA",
		false,
		function(v)
			ARG.KillAura = v
		end
	)

	Slider(
		S,
		"KILL AURA POWER",
		1,
		900000,
		900000,
		function(v)
			ARG.KillPower = v
		end
	)

	Slider(
		S,
		"KILL RANGE",
		1,
		700,
		700,
		function(v)
			ARG.KillRange = v
		end
	)

	Toggle(
		S,
		"MURDER AURA",
		false,
		function(v)
			ARG.MurderAura = v
		end
	)

	Slider(
		S,
		"MURDER AURA POWER",
		1,
		5000,
		5000,
		function(v)
			ARG.MurderPower = v
		end
	)

	Toggle(
		S,
		"NO-CHASE AURA",
		false,
		function(v)
			ARG.NoChaseAura = v
		end
	)

	Slider(
		S,
		"NO-CHASE POWER",
		1,
		30000,
		30000,
		function(v)
			ARG.NoChasePower = v
		end
	)

	Button(
		S,
		"RALLY + WIPE TEST DUMMIES",
		"RUN",
		function()
			if not HRP then
				return
			end

			local dummies = {}

			for _, model in ipairs(
				DummyFolder:GetChildren()
			) do
				if model:IsA("Model") then
					local hum = model:FindFirstChildOfClass(
						"Humanoid"
					)

					local root = model:FindFirstChild(
						"HumanoidRootPart"
					)

					if hum
						and root
						and hum.Health > 0
					then
						table.insert(
							dummies,
							{
								Humanoid = hum,
								Root = root
							}
						)
					end
				end
			end

			local count = #dummies

			for index, data in ipairs(dummies) do
				local angle =
					(index / math.max(count, 1))
					* math.pi
					* 2

				local position =
					HRP.Position
					+ Vector3.new(
						math.cos(angle) * 8,
						0,
						math.sin(angle) * 8
					)

				data.Root.CFrame =
					CFrame.new(
						position,
						HRP.Position
					)

				data.Root.AssemblyLinearVelocity =
					Vector3.zero

				data.Root.AssemblyAngularVelocity =
					Vector3.zero
			end

			task.delay(
				0.45,
				function()
					if not Alive then
						return
					end

					for _, data in ipairs(dummies) do
						if data.Humanoid
							and data.Humanoid.Parent
						then
							data.Humanoid.Health = 0
						end
					end
				end
			)
		end
	)
end

-- ================================================================
-- MOVEMENT
-- ================================================================
do
	local S = Section(
		Panels[3],
		"MOVEMENT"
	)

	Toggle(
		S,
		"TP WALK",
		false,
		function(v)
			ARG.TPWalk = v
		end
	)

	Slider(
		S,
		"TP SPEED",
		1,
		250,
		30,
		function(v)
			ARG.TPSpeed = v
		end
	)

	Slider(
		S,
		"TP STRENGTH",
		1,
		100,
		5,
		function(v)
			ARG.TPStrength = v
		end
	)

	Toggle(
		S,
		"FLY",
		false,
		function(v)
			ARG.Fly = v

			if not v
				and HRP
				and not ARG.FlyGodMode
			then
				HRP.AssemblyLinearVelocity =
					Vector3.zero
			end
		end
	)

	Slider(
		S,
		"FLY SPEED",
		1,
		350,
		100,
		function(v)
			ARG.FlySpeed = v
		end
	)

	Toggle(
		S,
		"FLY GOD MODE",
		false,
		function(v)
			ARG.FlyGodMode = v

			if not v
				and HRP
				and not ARG.Fly
			then
				HRP.AssemblyLinearVelocity =
					Vector3.zero
			end
		end
	)

	Slider(
		S,
		"FLY GOD SPEED",
		25,
		500,
		160,
		function(v)
			ARG.FlyGodSpeed = v
		end
	)

	Toggle(
		S,
		"DASH + GOLD TRAILS",
		true,
		function(v)
			ARG.Dash = v
		end
	)

	Slider(
		S,
		"DASH POWER",
		20,
		300,
		120,
		function(v)
			ARG.DashPower = v
		end
	)

	Toggle(
		S,
		"SPRINT",
		false,
		function(v)
			ARG.Sprint = v

			if Humanoid and not v then
				Humanoid.WalkSpeed =
					BaseWalkSpeed
			end
		end
	)

	Slider(
		S,
		"WALK SPEED",
		16,
		120,
		32,
		function(v)
			ARG.SprintSpeed = v
		end
	)

	Slider(
		S,
		"JUMP POWER",
		50,
		250,
		100,
		function(v)
			ARG.JumpPower = v

			if Humanoid then
				Humanoid.UseJumpPower = true
				Humanoid.JumpPower = v
			end
		end
	)

	Toggle(
		S,
		"INFINITE JUMP",
		false,
		function(v)
			ARG.InfiniteJump = v
		end
	)

	Toggle(
		S,
		"NOCLIP",
		false,
		function(v)
			ARG.Noclip = v

			if not v then
				RestoreCollision()
			end
		end
	)
end

-- ================================================================
-- PLAYER
-- ================================================================
do
	local S = Section(
		Panels[4],
		"PLAYER / DEFENSE"
	)

	Toggle(
		S,
		"GOD MODE",
		false,
		function(v)
			ARG.GodMode = v

			if Humanoid
				and not v
				and not ARG.FlyGodMode
			then
				Humanoid.MaxHealth =
					BaseMaxHealth

				if Humanoid.Health
					> BaseMaxHealth
				then
					Humanoid.Health =
						BaseMaxHealth
				end
			end
		end
	)

	Toggle(
		S,
		"GOD MODE EMOTE",
		false,
		function(v)
			ARG.GodModeEmote = v

			if not v then
				StopGodEmote()
			end
		end
	)

	Input(
		S,
		"GOD EMOTE ID",
		"507771019",
		function(text)
			local id =
				tostring(text):
				gsub("%D", "")

			if id ~= "" then
				ARG.GodEmoteId =
					"rbxassetid://"
					.. id

				StopGodEmote()
			end
		end
	)

	Toggle(
		S,
		"ANTI-GRAB GLITCH",
		false,
		function(v)
			ARG.AntiGrabGlitch = v
		end
	)

	Toggle(
		S,
		"ANTI-RAGDOLL",
		false,
		function(v)
			ARG.AntiRagdoll = v
		end
	)

	Toggle(
		S,
		"ANTI-FLING",
		false,
		function(v)
			ARG.AntiFling = v
		end
	)

	Toggle(
		S,
		"AUTO RECOVER",
		false,
		function(v)
			ARG.AutoRecover = v
		end
	)
end

-- ================================================================
-- VISUALS
-- ================================================================
do
	local S = Section(
		Panels[5],
		"VISUALS"
	)

	Toggle(
		S,
		"ESP",
		false,
		function(v)
			ARG.ESP = v
		end
	)

	Toggle(
		S,
		"ESP NAMES",
		true,
		function(v)
			ARG.ESPNames = v
		end
	)

	Toggle(
		S,
		"ESP DISTANCE",
		true,
		function(v)
			ARG.ESPDistance = v
		end
	)

	Toggle(
		S,
		"ESP HEALTH",
		true,
		function(v)
			ARG.ESPHealth = v
		end
	)

	Toggle(
		S,
		"ESP HIGHLIGHT",
		true,
		function(v)
			ARG.ESPHighlight = v
		end
	)

	Toggle(
		S,
		"FULL BRIGHT",
		false,
		function(v)
			ARG.FullBright = v

			if v and ARG.NightMode then
				ARG.NightMode = false

				SetToggle(
					"NIGHT MODE",
					false,
					false
				)
			end

			ApplyLighting()
		end
	)

	Toggle(
		S,
		"NIGHT MODE",
		false,
		function(v)
			ARG.NightMode = v

			if v and ARG.FullBright then
				ARG.FullBright = false

				SetToggle(
					"FULL BRIGHT",
					false,
					false
				)
			end

			ApplyLighting()
		end
	)
end

-- ================================================================
-- TARGETS
-- ================================================================
do
	local S = Section(
		Panels[6],
		"TARGET CONTROLS"
	)

	Input(
		S,
		"TARGET PLAYER",
		"PlayerName",
		function(text)
			ARG.TargetName = text
		end
	)

	Button(
		S,
		"TELEPORT SELF TO TARGET",
		"TP",
		function()
			local target =
				FindPlayer(ARG.TargetName)

			local root =
				target
				and target.Character
				and target.Character:
					FindFirstChild(
						"HumanoidRootPart"
					)

			if HRP and root then
				HRP.CFrame =
					root.CFrame
					*
					CFrame.new(
						0,
						0,
						4
					)
			else
				Notify(
					"TARGET",
					"Target not found."
				)
			end
		end
	)

	Button(
		S,
		"SPECTATE TARGET",
		"WATCH",
		function()
			local target =
				FindPlayer(ARG.TargetName)

			local hum =
				target
				and target.Character
				and target.Character:
					FindFirstChildOfClass(
						"Humanoid"
					)

			if Camera and hum then
				Camera.CameraSubject = hum
			else
				Notify(
					"TARGET",
					"Target not found."
				)
			end
		end
	)

	Button(
		S,
		"STOP SPECTATING",
		"STOP",
		function()
			if Camera and Humanoid then
				Camera.CameraSubject =
					Humanoid
			end
		end
	)

	Toggle(
		S,
		"LOCAL NOTIFY PREVIEW",
		false,
		function(v)
			ARG.NotifyPreview = v
		end
	)

	Input(
		S,
		"NOTIFY MESSAGE",
		"ARG CORE V7",
		function(text)
			if text ~= "" then
				ARG.NotifyMessage = text
			end
		end
	)

	Slider(
		S,
		"NOTIFY INTERVAL",
		1,
		10,
		1,
		function(v)
			ARG.NotifyInterval = v
		end
	)
end

-- ================================================================
-- WHITELISTS
-- ================================================================
do
	local P = Panels[7]

	local FriendSection = Section(
		P,
		"FRIENDS WHITELIST"
	)

	Input(
		FriendSection,
		"FRIENDS",
		"friend1 friend2 friend3",
		function(text)
			ARG.Friends =
				ParseList(text)
		end
	)

	Button(
		FriendSection,
		"IMPORT FRIENDS IN SERVER",
		"IMPORT",
		function()
			local list = {}

			for _, player in ipairs(
				Players:GetPlayers()
			) do
				if player ~= LP then
					local ok, result =
						pcall(
							function()
								return LP:
									IsFriendsWith(
										player.UserId
									)
							end
						)

					if ok and result then
						table.insert(
							list,
							Lower(player.Name)
						)
					end
				end
			end

			ARG.Friends = list

			Notify(
				"WHITELIST",
				"Imported "
					.. #list
					.. " friend(s)."
			)
		end
	)

	local TargetSection = Section(
		P,
		"TARGET WHITELIST"
	)

	Input(
		TargetSection,
		"TARGETS",
		"target1 target2",
		function(text)
			ARG.Targets =
				ParseList(text)
		end
	)

	local ProtectSection = Section(
		P,
		"PROTECT WHITELIST"
	)

	Input(
		ProtectSection,
		"PROTECTED PLAYERS",
		"player1 player2",
		function(text)
			ARG.Protect =
				ParseList(text)
		end
	)

	local BypassSection = Section(
		P,
		"BYPASS LIST"
	)

	Input(
		BypassSection,
		"BYPASS PLAYERS",
		"player1 player2",
		function(text)
			ARG.Bypass =
				ParseList(text)
		end
	)
end

-- ================================================================
-- MUSIC
-- ================================================================
local MusicStatus

local function LoadTrack(index, autoplay)
	local count = #ARG.Playlist

	if count <= 0 then
		return
	end

	index =
		((index - 1) % count) + 1

	ARG.MusicIndex = index

	local track = ARG.Playlist[index]

	local id =
		tostring(track.Id):
		gsub("%D", "")

	if id == "" then
		Notify(
			"MUSIC",
			"Add a permitted Roblox audio ID for "
				.. track.Name
				.. "."
		)

		return
	end

	Music:Stop()

	Music.SoundId =
		"rbxassetid://"
		.. id

	Music.TimePosition = 0

	if MusicStatus then
		MusicStatus.Text =
			"NOW PLAYING // "
			.. track.Name
	end

	if autoplay ~= false then
		ARG.MusicEnabled = true
		Music:Play()
	end
end

Bind(
	Music.Ended,
	function()
		if Alive
			and ARG.MusicEnabled
		then
			LoadTrack(
				ARG.MusicIndex + 1,
				true
			)
		end
	end
)

do
	local S = Section(
		Panels[8],
		"ARG HIP-HOP MUSIC PLAYER"
	)

	MusicStatus = Label(
		S,
		"NOW PLAYING // NO TRACK",
		11,
		T.GOLD,
		Enum.Font.GothamBold
	)

	MusicStatus.Size =
		UDim2.new(
			1,
			0,
			0,
			30
		)

	for i = 1, 5 do
		local index = i

		Input(
			S,
			"HIP-HOP TRACK "
				.. i
				.. " AUDIO ID",
			"Roblox audio ID",
			function(text)
				ARG.Playlist[index].Id =
					tostring(text):
					gsub("%D", "")
			end
		)
	end

	Button(
		S,
		"PLAY",
		"PLAY",
		function()
			if Music.SoundId == "" then
				LoadTrack(
					ARG.MusicIndex,
					true
				)
			else
				ARG.MusicEnabled = true

				local ok =
					pcall(
						function()
							Music:Resume()
						end
					)

				if not ok then
					Music:Play()
				end
			end
		end
	)

	Button(
		S,
		"PAUSE",
		"PAUSE",
		function()
			ARG.MusicEnabled = false
			Music:Pause()
		end
	)

	Button(
		S,
		"STOP",
		"STOP",
		function()
			ARG.MusicEnabled = false
			Music:Stop()
			Music.TimePosition = 0
		end
	)

	Button(
		S,
		"PREVIOUS TRACK",
		"PREV",
		function()
			LoadTrack(
				ARG.MusicIndex - 1,
				true
			)
		end
	)

	Button(
		S,
		"NEXT TRACK",
		"NEXT",
		function()
			LoadTrack(
				ARG.MusicIndex + 1,
				true
			)
		end
	)

	Slider(
		S,
		"MUSIC VOLUME",
		0,
		100,
		55,
		function(v)
			ARG.MusicVolume = v
			Music.Volume = v / 100
		end
	)
end

-- ================================================================
-- WORLD
-- ================================================================
do
	local S = Section(
		Panels[9],
		"WORLD"
	)

	Slider(
		S,
		"GRAVITY",
		0,
		600,
		math.floor(
			Workspace.Gravity + 0.5
		),
		function(v)
			Workspace.Gravity = v
		end
	)

	Toggle(
		S,
		"REMOVE FOG",
		false,
		function(v)
			ARG.RemoveFog = v
			ApplyLighting()
		end
	)
end

-- ================================================================
-- SETTINGS
-- ================================================================
do
	local S = Section(
		Panels[10],
		"SETTINGS"
	)

	Button(
		S,
		"CENTER WINDOW",
		"CENTER",
		function()
			Main.Position =
				UDim2.new(
					0.5,
					-WW / 2,
					0.5,
					-WH / 2
				)
		end
	)

	Slider(
		S,
		"MENU SCALE",
		50,
		100,
		100,
		function(v)
			BaseScale = v / 100
			UpdateScale()
		end
	)

	Button(
		S,
		"HIDE MENU",
		"HIDE",
		function()
			Main.Visible = false
		end
	)
end

-- ================================================================
-- CREDITS
-- ================================================================
do
	local S = Section(
		Panels[11],
		"CREDITS"
	)

	local text = Label(
		S,
		"ARG CORE V7\nENGINEERED BY ALEX\n\nLOCAL STABLE BUILD\nBLUE // GOLD // GLOW",
		19,
		T.TEXT,
		Enum.Font.GothamBlack
	)

	text.Size =
		UDim2.new(
			1,
			0,
			0,
			150
		)

	text.TextWrapped = true
	text.TextYAlignment =
		Enum.TextYAlignment.Center
end

-- ================================================================
-- ESP
-- ================================================================
local ESPObjects = {}

local function RemoveESP(player)
	local data = ESPObjects[player]

	if not data then
		return
	end

	for _, object in pairs(data) do
		if object then
			pcall(
				function()
					object:Destroy()
				end
			)
		end
	end

	ESPObjects[player] = nil
end

local function ClearESP()
	local list = {}

	for player in pairs(ESPObjects) do
		table.insert(list, player)
	end

	for _, player in ipairs(list) do
		RemoveESP(player)
	end
end

local function MakeESP(player)
	if player == LP
		or not player.Character
		or not PlayerAllowed(player)
	then
		return
	end

	RemoveESP(player)

	local character = player.Character

	local head =
		character:
		FindFirstChild("Head")

	if not head then
		return
	end

	local highlight = Instance.new("Highlight")

	highlight.Name =
		"ARG_ESP_HIGHLIGHT"

	highlight.Adornee = character
	highlight.FillColor = T.BLUE
	highlight.OutlineColor = T.GOLD
	highlight.FillTransparency = 0.82
	highlight.OutlineTransparency = 0.1
	highlight.DepthMode =
		Enum.HighlightDepthMode.AlwaysOnTop
	highlight.Parent = character

	local billboard = Instance.new("BillboardGui")

	billboard.Name =
		"ARG_ESP_BILLBOARD"

	billboard.Adornee = head
	billboard.Size =
		UDim2.fromOffset(
			220,
			50
		)

	billboard.StudsOffset =
		Vector3.new(
			0,
			3.2,
			0
		)

	billboard.AlwaysOnTop = true
	billboard.Parent = GUI

	local text = Label(
		billboard,
		player.DisplayName,
		11,
		T.GOLD,
		Enum.Font.GothamBold
	)

	text.Size = UDim2.fromScale(1, 1)
	text.TextXAlignment =
		Enum.TextXAlignment.Center
	text.TextYAlignment =
		Enum.TextYAlignment.Center

	ESPObjects[player] = {
		Highlight = highlight,
		Billboard = billboard,
		Text = text
	}
end

local function WatchPlayer(player)
	if player == LP then
		return
	end

	Bind(
		player.CharacterAdded,
		function()
			RemoveESP(player)
		end
	)
end

for _, player in ipairs(
	Players:GetPlayers()
) do
	WatchPlayer(player)
end

Bind(
	Players.PlayerAdded,
	function(player)
		WatchPlayer(player)
	end
)

Bind(
	Players.PlayerRemoving,
	function(player)
		RemoveESP(player)
	end
)

-- ================================================================
-- GOD MODE EMOTE
-- ================================================================
local function StartGodEmote()
	if not Humanoid then
		return
	end

	StopGodEmote()

	local animator =
		Humanoid:
		FindFirstChildOfClass(
			"Animator"
		)

	if not animator then
		animator =
			Instance.new("Animator")

		animator.Parent =
			Humanoid
	end

	local animation =
		Instance.new("Animation")

	animation.AnimationId =
		ARG.GodEmoteId

	local ok, track =
		pcall(
			function()
				return animator:
					LoadAnimation(
						animation
					)
			end
		)

	animation:Destroy()

	if ok and track then
		GodTrack = track

		track.Looped = true
		track.Priority =
			Enum.AnimationPriority.Action

		track:Play(0.15)
	end
end

-- ================================================================
-- DUMMY TARGETS
-- ================================================================
local function GetDummies()
	local result = {}

	if not HRP
		or not DummyFolder
		or not DummyFolder.Parent
	then
		return result
	end

	for _, model in ipairs(
		DummyFolder:GetChildren()
	) do
		if model:IsA("Model") then
			local hum =
				model:
				FindFirstChildOfClass(
					"Humanoid"
				)

			local root =
				model:
				FindFirstChild(
					"HumanoidRootPart"
				)

			if hum
				and root
				and hum.Health > 0
			then
				local distance =
					(
						root.Position
						-
						HRP.Position
					).Magnitude

				if distance <= ARG.KillRange then
					table.insert(
						result,
						{
							Humanoid = hum,
							Root = root,
							Distance = distance
						}
					)
				end
			end
		end
	end

	table.sort(
		result,
		function(a, b)
			return a.Distance < b.Distance
		end
	)

	return result
end

-- ================================================================
-- DASH
-- ================================================================
local LastDash = 0

local function MakeDashTrail()
	if not HRP then
		return
	end

	local attachment0 =
		Instance.new("Attachment")

	local attachment1 =
		Instance.new("Attachment")

	attachment0.Position =
		Vector3.new(-1, 0, 0)

	attachment1.Position =
		Vector3.new(1, 0, 0)

	attachment0.Parent = HRP
	attachment1.Parent = HRP

	local trail =
		Instance.new("Trail")

	trail.Attachment0 = attachment0
	trail.Attachment1 = attachment1

	trail.Color =
		ColorSequence.new({
			ColorSequenceKeypoint.new(
				0,
				T.GOLD
			),
			ColorSequenceKeypoint.new(
				1,
				T.BLUE
			)
		})

	trail.LightEmission = 1
	trail.Lifetime = 0.18
	trail.Parent = HRP

	task.delay(
		0.30,
		function()
			pcall(
				function()
					trail:Destroy()
				end
			)

			pcall(
				function()
					attachment0:Destroy()
				end
			)

			pcall(
				function()
					attachment1:Destroy()
				end
			)
		end
	)
end

-- ================================================================
-- FLOATING A ICON
-- ================================================================
local FloatGlow = Instance.new("Frame")

FloatGlow.Size =
	UDim2.fromOffset(
		64,
		64
	)

FloatGlow.Position =
	UDim2.new(
		1,
		-82,
		0,
		18
	)

FloatGlow.BackgroundColor3 = T.BLUE
FloatGlow.BackgroundTransparency = 0.74
FloatGlow.BorderSizePixel = 0
FloatGlow.ZIndex = 499
FloatGlow.Parent = GUI

Corner(FloatGlow, 13)

local Float = Instance.new("TextButton")

Float.Size =
	UDim2.fromOffset(
		54,
		54
	)

Float.Position =
	UDim2.new(
		1,
		-77,
		0,
		23
	)

Float.BackgroundColor3 = T.BG
Float.Text = "A"
Float.TextColor3 = T.GOLD
Float.TextSize = 26
Float.Font = Enum.Font.GothamBlack
Float.BorderSizePixel = 0
Float.ZIndex = 500
Float.Parent = GUI

Corner(Float, 10)
Stroke(Float, T.GOLD, 2.5, 0)

-- ================================================================
-- SEARCH
-- ================================================================
Bind(
	Search:
	GetPropertyChangedSignal(
		"Text"
	),
	function()
		local query =
			Lower(Search.Text)

		for _, entry in ipairs(
			SearchRows
		) do
			if entry.Row
				and entry.Row.Parent
			then
				entry.Row.Visible =
					query == ""
					or entry.Name:
						find(
							query,
							1,
							true
						)
						~= nil
			end
		end
	end
)

-- ================================================================
-- WINDOW DRAGGING
-- ================================================================
Bind(
	Header.InputBegan,
	function(input)
		if input.UserInputType
				== Enum.UserInputType.MouseButton1
			or input.UserInputType
				== Enum.UserInputType.Touch
		then
			DraggingWindow = true
			DragStart = input.Position
			DragPosition = Main.Position
		end
	end
)

Bind(
	UIS.InputChanged,
	function(input)
		if ActiveSliderUpdate
			and (
				input.UserInputType
					== Enum.UserInputType.MouseMovement
				or input.UserInputType
					== Enum.UserInputType.Touch
			)
		then
			ActiveSliderUpdate(
				input.Position.X
			)
		end

		if DraggingWindow
			and DragStart
			and DragPosition
			and (
				input.UserInputType
					== Enum.UserInputType.MouseMovement
				or input.UserInputType
					== Enum.UserInputType.Touch
			)
		then
			local delta =
				input.Position
				-
				DragStart

			Main.Position =
				UDim2.new(
					DragPosition.X.Scale,
					DragPosition.X.Offset + delta.X,
					DragPosition.Y.Scale,
					DragPosition.Y.Offset + delta.Y
				)
		end
	end
)

Bind(
	UIS.InputEnded,
	function(input)
		if input.UserInputType
				== Enum.UserInputType.MouseButton1
			or input.UserInputType
				== Enum.UserInputType.Touch
		then
			ActiveSliderUpdate = nil
			DraggingWindow = false
		end
	end
)

-- ================================================================
-- INFINITE JUMP
-- ================================================================
Bind(
	UIS.JumpRequest,
	function()
		if Alive
			and ARG.InfiniteJump
			and Humanoid
		then
			Humanoid:
				ChangeState(
					Enum.HumanoidStateType.Jumping
				)
		end
	end
)

-- ================================================================
-- KEYS
-- ================================================================
Bind(
	UIS.InputBegan,
	function(input, gameProcessed)
		if gameProcessed
			or not Alive
		then
			return
		end

		if input.KeyCode
			== Enum.KeyCode.Insert
		then
			Main.Visible =
				not Main.Visible
			return
		end

		if input.KeyCode == Enum.KeyCode.Q
			and ARG.Dash
			and HRP
		then
			local now = os.clock()

			if now - LastDash >= 0.25 then
				LastDash = now

				HRP.AssemblyLinearVelocity =
					HRP.CFrame.LookVector
					*
					ARG.DashPower
					+
					Vector3.new(
						0,
						HRP.AssemblyLinearVelocity.Y,
						0
					)

				MakeDashTrail()
			end
		end
	end
)

-- ================================================================
-- UPDATE CLOCKS
-- ================================================================
local CombatClock = 0
local ESPClock = 0
local NoclipClock = 0
local EmoteClock = 0
local GlowClock = 0
local StatsClock = 0
local Frames = 0
local FPS = 0

-- ================================================================
-- RENDER LOOP
-- ================================================================
Bind(
	RunService.RenderStepped,
	function(dt)
		if not Alive then
			return
		end

		Camera =
			Workspace.CurrentCamera
			or Camera

		Frames += 1
		StatsClock += dt

		if StatsClock >= 1 then
			FPS = Frames
			Frames = 0
			StatsClock = 0

			Status.Text =
				"PLAYERS "
				.. #Players:GetPlayers()
				.. "   //   FPS "
				.. FPS
				.. "   //   SYSTEM ONLINE"
		end

		GlowClock += dt

		local pulse =
			(
				math.sin(
					GlowClock * 3
				)
				+
				1
			)
			/
			2

		if LogoGlow.Parent then
			LogoGlow.BackgroundTransparency =
				0.78
				-
				pulse * 0.20
		end

		if FloatGlow.Parent then
			FloatGlow.BackgroundTransparency =
				0.80
				-
				pulse * 0.22
		end

		if Main.Parent then
			OuterGlow.Transparency =
				0.88
				-
				pulse * 0.08
		end

		if ARG.TPWalk
			and Character
			and Humanoid
			and HRP
		then
			local direction =
				Humanoid.MoveDirection

			if direction.Magnitude > 0.01 then
				local step =
					math.min(
						dt,
						1 / 30
					)

				Character:
					PivotTo(
						Character:GetPivot()
						+
						direction.Unit
						*
						ARG.TPSpeed
						*
						ARG.TPStrength
						*
						step
					)
			end
		end

		if HRP
			and (
				ARG.Fly
				or ARG.FlyGodMode
			)
			and Camera
		then
			local speed =
				ARG.FlyGodMode
				and ARG.FlyGodSpeed
				or ARG.FlySpeed

			local direction =
				Vector3.zero

			if UIS:IsKeyDown(
				Enum.KeyCode.W
			) then
				direction +=
					Camera.CFrame.LookVector
			end

			if UIS:IsKeyDown(
				Enum.KeyCode.S
			) then
				direction -=
					Camera.CFrame.LookVector
			end

			if UIS:IsKeyDown(
				Enum.KeyCode.A
			) then
				direction -=
					Camera.CFrame.RightVector
			end

			if UIS:IsKeyDown(
				Enum.KeyCode.D
			) then
				direction +=
					Camera.CFrame.RightVector
			end

			if UIS:IsKeyDown(
				Enum.KeyCode.Space
			) then
				direction +=
					Vector3.new(
						0,
						1,
						0
					)
			end

			if UIS:IsKeyDown(
				Enum.KeyCode.LeftShift
			) then
				direction -=
					Vector3.new(
						0,
						1,
						0
					)
			end

			if direction.Magnitude > 0 then
				HRP.AssemblyLinearVelocity =
					direction.Unit
					*
					speed
			else
				HRP.AssemblyLinearVelocity =
					Vector3.zero
			end
		end

		ESPClock += dt

		if ESPClock >= 0.10 then
			ESPClock = 0

			if not ARG.ESP then
				ClearESP()
			else
				for _, player in ipairs(
					Players:GetPlayers()
				) do
					if player ~= LP then
						if not PlayerAllowed(player) then
							RemoveESP(player)

						elseif player.Character then
							local data =
								ESPObjects[player]

							if not data
								or not data.Billboard
								or not data.Billboard.Parent
							then
								MakeESP(player)
								data =
									ESPObjects[player]
							end

							if data then
								local root =
									player.Character:
									FindFirstChild(
										"HumanoidRootPart"
									)

								local hum =
									player.Character:
									FindFirstChildOfClass(
										"Humanoid"
									)

								if root and HRP then
									data.Highlight.Enabled =
										ARG.ESPHighlight

									local output = {}

									if ARG.ESPNames then
										table.insert(
											output,
											player.DisplayName
										)
									end

									if ARG.ESPDistance then
										table.insert(
											output,
											tostring(
												math.floor(
													(
														root.Position
														-
														HRP.Position
													).Magnitude
												)
											)
											..
											" studs"
										)
									end

									if ARG.ESPHealth
										and hum
									then
										table.insert(
											output,
											"HP "
												..
												math.floor(
													hum.Health
												)
										)
									end

									data.Text.Text =
										table.concat(
											output,
											" // "
										)
								end
							end
						end
					end
				end
			end
		end
	end
)

-- ================================================================
-- HEARTBEAT
-- ================================================================
Bind(
	RunService.Heartbeat,
	function(dt)
		if not Alive
			or not Humanoid
			or not HRP
		then
			return
		end

		Humanoid.UseJumpPower = true
		Humanoid.JumpPower = ARG.JumpPower

		if ARG.Sprint then
			Humanoid.WalkSpeed =
				ARG.SprintSpeed
		else
			Humanoid.WalkSpeed =
				BaseWalkSpeed
		end

		if ARG.GodMode
			or ARG.FlyGodMode
		then
			Humanoid.MaxHealth =
				1000000000

			Humanoid.Health =
				Humanoid.MaxHealth
		end

		if ARG.AntiGrabGlitch
			or ARG.AntiRagdoll
			or ARG.AutoRecover
			or ARG.FlyGodMode
		then
			local state =
				Humanoid:GetState()

			if Humanoid.PlatformStand
				or state
					== Enum.HumanoidStateType.Ragdoll
				or state
					== Enum.HumanoidStateType.FallingDown
				or state
					== Enum.HumanoidStateType.Physics
			then
				Humanoid.PlatformStand = false
				Humanoid.Sit = false
				Humanoid.AutoRotate = true

				pcall(
					function()
						Humanoid:
							ChangeState(
								Enum.HumanoidStateType.GettingUp
							)
					end
				)

				HRP.AssemblyAngularVelocity =
					Vector3.zero

				if not ARG.Fly
					and not ARG.FlyGodMode
				then
					HRP.AssemblyLinearVelocity =
						Vector3.zero
				end
			end
		end

		if ARG.AntiFling
			or ARG.FlyGodMode
		then
			if HRP.AssemblyAngularVelocity.Magnitude
				> 25
			then
				HRP.AssemblyAngularVelocity =
					Vector3.zero
			end

			if not ARG.FlyGodMode
				and HRP.AssemblyLinearVelocity.Magnitude
					> 180
			then
				HRP.AssemblyLinearVelocity =
					Vector3.zero
			end
		end

		NoclipClock += dt

		if NoclipClock >= 0.10 then
			NoclipClock = 0

			if ARG.Noclip
				and Character
			then
				for _, object in ipairs(
					Character:GetDescendants()
				) do
					if object:IsA("BasePart") then
						if CollisionCache[object] == nil then
							CollisionCache[object] =
								object.CanCollide
						end

						object.CanCollide =
							false
					end
				end
			end
		end

		EmoteClock += dt

		if EmoteClock >= 0.25 then
			EmoteClock = 0

			if ARG.GodModeEmote then
				if not GodTrack
					or not GodTrack.IsPlaying
				then
					StartGodEmote()
				end
			elseif GodTrack then
				StopGodEmote()
			end
		end

		CombatClock += dt

		if CombatClock >= ARG.AttackDelay then
			CombatClock = 0

			if ARG.KillAura
				or ARG.MurderAura
				or ARG.NoChaseAura
			then
				local targets =
					GetDummies()

				if ARG.KillAura
					and targets[1]
				then
					targets[1].Humanoid.Health =
						math.max(
							0,
							targets[1].Humanoid.Health
								-
								ARG.KillPower
						)
				end

				if ARG.MurderAura then
					for _, target in ipairs(
						targets
					) do
						target.Humanoid.Health =
							math.max(
								0,
								target.Humanoid.Health
									-
									ARG.MurderPower
							)
					end
				end

				if ARG.NoChaseAura then
					for _, target in ipairs(
						targets
					) do
						target.Humanoid.Health =
							math.max(
								0,
								target.Humanoid.Health
									-
									ARG.NoChasePower
							)
					end
				end
			end
		end
	end
)

-- ================================================================
-- LOCAL NOTIFY PREVIEW
-- ================================================================
task.spawn(
	function()
		while Alive do
			if ARG.NotifyPreview then
				local target =
					ARG.TargetName ~= ""
					and ARG.TargetName
					or "TARGET"

				Notify(
					"ARG // " .. target,
					ARG.NotifyMessage
				)
			end

			task.wait(
				math.max(
					1,
					ARG.NotifyInterval
				)
			)
		end
	end
)

-- ================================================================
-- CLEANUP
-- ================================================================
local function Cleanup()
	if not Alive then
		return
	end

	Alive = false

	ARG.NotifyPreview = false
	ARG.MusicEnabled = false
	ARG.Fly = false
	ARG.FlyGodMode = false
	ARG.TPWalk = false
	ARG.Noclip = false

	StopGodEmote()
	ClearESP()
	RestoreCollision()

	pcall(
		function()
			Lighting.Brightness =
				OriginalLighting.Brightness

			Lighting.ClockTime =
				OriginalLighting.ClockTime

			Lighting.FogStart =
				OriginalLighting.FogStart

			Lighting.FogEnd =
				OriginalLighting.FogEnd

			Lighting.Ambient =
				OriginalLighting.Ambient

			Lighting.OutdoorAmbient =
				OriginalLighting.OutdoorAmbient

			Workspace.Gravity =
				OriginalLighting.Gravity
		end
	)

	if Humanoid then
		pcall(
			function()
				Humanoid.WalkSpeed =
					BaseWalkSpeed

				Humanoid.UseJumpPower =
					true

				Humanoid.JumpPower =
					BaseJumpPower

				Humanoid.MaxHealth =
					BaseMaxHealth

				if Humanoid.Health
					> BaseMaxHealth
				then
					Humanoid.Health =
						BaseMaxHealth
				end
			end
		)
	end

	if HRP then
		pcall(
			function()
				HRP.AssemblyAngularVelocity =
					Vector3.zero

				HRP.AssemblyLinearVelocity =
					Vector3.zero
			end
		)
	end

	pcall(
		function()
			Music:Stop()
		end
	)

	pcall(
		function()
			Music:Destroy()
		end
	)

	DisconnectEverything()

	if GUI and GUI.Parent then
		GUI:Destroy()
	end
end

-- ================================================================
-- BUTTON CONNECTIONS
-- ================================================================
Bind(
	Float.MouseButton1Click,
	function()
		Main.Visible =
			not Main.Visible
	end
)

Bind(
	Hide.MouseButton1Click,
	function()
		Main.Visible = false
	end
)

Bind(
	Close.MouseButton1Click,
	Cleanup
)

-- ================================================================
-- START
-- ================================================================
OpenTab(1)

print("============================================")
print("ARG CORE V7 - LOCAL STABLE BUILD")
print("ENGINEERED BY ALEX")
print("THEME: BLUE + GOLD")
print("KILL AURA: 900000")
print("KILL RANGE: 700")
print("MURDER AURA: 5000")
print("NO-CHASE POWER: 30000")
print("============================================")