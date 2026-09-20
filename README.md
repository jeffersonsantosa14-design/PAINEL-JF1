```lua
--========================================================
-- PAINEL FLY + INVISIBLE + ESP
-- PARA USAR NO SEU PRÓPRIO JOGO
-- LocalScript em StarterPlayer > StarterPlayerScripts
--========================================================

local Players = game:GetService("Players")
local UserInputService = game:GetService("UserInputService")
local RunService = game:GetService("RunService")

local Player = Players.LocalPlayer
local Camera = workspace.CurrentCamera

--========================================================
-- CONFIGURAÇÕES
--========================================================

local FlyAtivo = false
local InvisibleAtivo = false
local ESPAtivo = false

local FlySpeed = 60

local Character
local Humanoid
local RootPart

local FlyConnection
local FlyVelocity
local FlyGyro

local ESPs = {}

--========================================================
-- CHARACTER
--========================================================

local function AtualizarCharacter()
	Character = Player.Character or Player.CharacterAdded:Wait()
	Humanoid = Character:WaitForChild("Humanoid")
	RootPart = Character:WaitForChild("HumanoidRootPart")
end

AtualizarCharacter()

Player.CharacterAdded:Connect(function()
	task.wait(1)
	AtualizarCharacter()

	if InvisibleAtivo then
		task.wait(0.2)

		for _, obj in ipairs(Character:GetDescendants()) do
			if obj:IsA("BasePart") then
				obj.LocalTransparencyModifier = 1
			elseif obj:IsA("Decal") then
				obj.Transparency = 1
			end
		end
	end
end)

--========================================================
-- GUI
--========================================================

local ScreenGui = Instance.new("ScreenGui")
ScreenGui.Name = "JF_MurderPainel"
ScreenGui.ResetOnSpawn = false
ScreenGui.Parent = Player:WaitForChild("PlayerGui")

local Main = Instance.new("Frame")
Main.Name = "Main"
Main.Size = UDim2.new(0, 300, 0, 250)
Main.Position = UDim2.new(0.5, -150, 0.5, -125)
Main.BackgroundColor3 = Color3.fromRGB(25, 25, 25)
Main.BorderSizePixel = 0
Main.Parent = ScreenGui

local Corner = Instance.new("UICorner")
Corner.CornerRadius = UDim.new(0, 10)
Corner.Parent = Main

local Stroke = Instance.new("UIStroke")
Stroke.Color = Color3.fromRGB(70, 70, 70)
Stroke.Thickness = 2
Stroke.Parent = Main

--========================================================
-- TÍTULO
--========================================================

local Title = Instance.new("TextLabel")
Title.Size = UDim2.new(1, 0, 0, 45)
Title.BackgroundTransparency = 1
Title.Text = "☠  MURDER PANEL"
Title.TextColor3 = Color3.fromRGB(255, 255, 255)
Title.TextSize = 22
Title.Font = Enum.Font.GothamBold
Title.Parent = Main

--========================================================
-- FUNÇÃO BOTÃO
--========================================================

local function CriarBotao(nome, posicao)
	local Button = Instance.new("TextButton")

	Button.Size = UDim2.new(1, -30, 0, 48)
	Button.Position = posicao

	Button.BackgroundColor3 = Color3.fromRGB(40, 40, 40)
	Button.BorderSizePixel = 0

	Button.Text = nome .. "  [OFF]"
	Button.TextColor3 = Color3.fromRGB(255, 255, 255)
	Button.TextSize = 17
	Button.Font = Enum.Font.GothamBold

	Button.AutoButtonColor = true
	Button.Parent = Main

	local C = Instance.new("UICorner")
	C.CornerRadius = UDim.new(0, 7)
	C.Parent = Button

	return Button
end

local FlyButton = CriarBotao(
	"✈️ FLY",
	UDim2.new(0, 15, 0, 55)
)

local InvisibleButton = CriarBotao(
	"👻 INVISIBLE",
	UDim2.new(0, 15, 0, 110)
)

local ESPButton = CriarBotao(
	"👁 ESP",
	UDim2.new(0, 15, 0, 165)
)

--========================================================
-- FLY
--========================================================

local function PararFly()
	FlyAtivo = false

	if FlyConnection then
		FlyConnection:Disconnect()
		FlyConnection = nil
	end

	if FlyVelocity then
		FlyVelocity:Destroy()
		FlyVelocity = nil
	end

	if FlyGyro then
		FlyGyro:Destroy()
		FlyGyro = nil
	end

	if Humanoid then
		Humanoid.PlatformStand = false
	end

	FlyButton.Text = "✈️ FLY  [OFF]"
	FlyButton.BackgroundColor3 = Color3.fromRGB(40, 40, 40)
end

local function IniciarFly()
	if not Character or not RootPart or not Humanoid then
		return
	end

	FlyAtivo = true

	Humanoid.PlatformStand = true

	FlyVelocity = Instance.new("BodyVelocity")
	FlyVelocity.Name = "JF_FlyVelocity"
	FlyVelocity.MaxForce = Vector3.new(
		math.huge,
		math.huge,
		math.huge
	)
	FlyVelocity.Velocity = Vector3.zero
	FlyVelocity.Parent = RootPart

	FlyGyro = Instance.new("BodyGyro")
	FlyGyro.Name = "JF_FlyGyro"
	FlyGyro.MaxTorque = Vector3.new(
		math.huge,
		math.huge,
		math.huge
	)
	FlyGyro.P = 50000
	FlyGyro.Parent = RootPart

	FlyButton.Text = "✈️ FLY  [ON]"
	FlyButton.BackgroundColor3 = Color3.fromRGB(0, 130, 70)

	FlyConnection = RunService.RenderStepped:Connect(function()
		if not FlyAtivo then
			return
		end

		if not RootPart or not RootPart.Parent then
			return
		end

		local Direction = Vector3.zero

		if UserInputService:IsKeyDown(Enum.KeyCode.W) then
			Direction += Camera.CFrame.LookVector
		end

		if UserInputService:IsKeyDown(Enum.KeyCode.S) then
			Direction -= Camera.CFrame.LookVector
		end

		if UserInputService:IsKeyDown(Enum.KeyCode.D) then
			Direction += Camera.CFrame.RightVector
		end

		if UserInputService:IsKeyDown(Enum.KeyCode.A) then
			Direction -= Camera.CFrame.RightVector
		end

		if UserInputService:IsKeyDown(Enum.KeyCode.Space) then
			Direction += Vector3.new(0, 1, 0)
		end

		if UserInputService:IsKeyDown(Enum.KeyCode.LeftControl) then
			Direction -= Vector3.new(0, 1, 0)
		end

		if Direction.Magnitude > 0 then
			Direction = Direction.Unit
		end

		FlyVelocity.Velocity = Direction * FlySpeed

		FlyGyro.CFrame = Camera.CFrame
	end)
end

FlyButton.MouseButton1Click:Connect(function()
	if FlyAtivo then
		PararFly()
	else
		IniciarFly()
	end
end)

--========================================================
-- TECLA F PARA FLY
--========================================================

UserInputService.InputBegan:Connect(function(Input, Processed)
	if Processed then
		return
	end

	if Input.KeyCode == Enum.KeyCode.F then
		if FlyAtivo then
			PararFly()
		else
			IniciarFly()
		end
	end
end)

--========================================================
-- INVISIBLE
--========================================================

local function AplicarInvisible()
	if not Character then
		return
	end

	for _, obj in ipairs(Character:GetDescendants()) do

		if obj:IsA("BasePart") then
			obj.LocalTransparencyModifier = 1

		elseif obj:IsA("Decal") then
			obj.Transparency = 1

		elseif obj:IsA("ParticleEmitter") then
			obj.Enabled = false

		elseif obj:IsA("Trail") then
			obj.Enabled = false
		end
	end
end

local function RemoverInvisible()
	if not Character then
		return
	end

	for _, obj in ipairs(Character:GetDescendants()) do

		if obj:IsA("BasePart") then
			obj.LocalTransparencyModifier = 0

		elseif obj:IsA("Decal") then
			obj.Transparency = 0

		elseif obj:IsA("ParticleEmitter") then
			obj.Enabled = true

		elseif obj:IsA("Trail") then
			obj.Enabled = true
		end
	end
end

InvisibleButton.MouseButton1Click:Connect(function()

	InvisibleAtivo = not InvisibleAtivo

	if InvisibleAtivo then

		AplicarInvisible()

		InvisibleButton.Text = "👻 INVISIBLE  [ON]"
		InvisibleButton.BackgroundColor3 =
			Color3.fromRGB(0, 130, 70)

	else

		RemoverInvisible()

		InvisibleButton.Text = "👻 INVISIBLE  [OFF]"
		InvisibleButton.BackgroundColor3 =
			Color3.fromRGB(40, 40, 40)
	end
end)

--========================================================
-- ESP
--========================================================

local function RemoverESP(PlayerAlvo)

	if ESPs[PlayerAlvo] then

		if ESPs[PlayerAlvo].Highlight then
			ESPs[PlayerAlvo].Highlight:Destroy()
		end

		if ESPs[PlayerAlvo].Billboard then
			ESPs[PlayerAlvo].Billboard:Destroy()
		end

		ESPs[PlayerAlvo] = nil
	end
end

local function CriarESP(PlayerAlvo)

	if PlayerAlvo == Player then
		return
	end

	if not PlayerAlvo.Character then
		return
	end

	local Char = PlayerAlvo.Character
	local Root = Char:FindFirstChild("HumanoidRootPart")

	if not Root then
		return
	end

	RemoverESP(PlayerAlvo)

	-- HIGHLIGHT

	local Highlight = Instance.new("Highlight")
	Highlight.Name = "JF_ESP"
	Highlight.FillTransparency = 0.65
	Highlight.OutlineTransparency = 0
	Highlight.DepthMode = Enum.HighlightDepthMode.AlwaysOnTop
	Highlight.Parent = Char

	-- NOME

	local Billboard = Instance.new("BillboardGui")
	Billboard.Name = "JF_ESP_Nome"
	Billboard.Size = UDim2.new(0, 150, 0, 35)
	Billboard.StudsOffset = Vector3.new(0, 3, 0)
	Billboard.AlwaysOnTop = true
	Billboard.Parent = Root

	local Text = Instance.new("TextLabel")
	Text.Size = UDim2.new(1, 0, 1, 0)
	Text.BackgroundTransparency = 1
	Text.Text = PlayerAlvo.DisplayName
	Text.TextColor3 = Color3.fromRGB(255, 255, 255)
	Text.TextStrokeTransparency = 0
	Text.TextSize = 14
	Text.Font = Enum.Font.GothamBold
	Text.Parent = Billboard

	ESPs[PlayerAlvo] = {
		Highlight = Highlight,
		Billboard = Billboard
	}
end

local function AtualizarESP()

	for _, PlayerAlvo in ipairs(Players:GetPlayers()) do

		if PlayerAlvo ~= Player then

			if PlayerAlvo.Character then
				CriarESP(PlayerAlvo)
			end

			PlayerAlvo.CharacterAdded:Connect(function()
				task.wait(0.5)

				if ESPAtivo then
					CriarESP(PlayerAlvo)
				end
			end)
		end
	end
end

ESPButton.MouseButton1Click:Connect(function()

	ESPAtivo = not ESPAtivo

	if ESPAtivo then

		ESPButton.Text = "👁 ESP  [ON]"
		ESPButton.BackgroundColor3 =
			Color3.fromRGB(0, 130, 70)

		AtualizarESP()

	else

		ESPButton.Text = "👁 ESP  [OFF]"
		ESPButton.BackgroundColor3 =
			Color3.fromRGB(40, 40, 40)

		for PlayerAlvo in pairs(ESPs) do
			RemoverESP(PlayerAlvo)
		end
	end
end)

Players.PlayerRemoving:Connect(function(PlayerAlvo)
	RemoverESP(PlayerAlvo)
end)

--========================================================
-- PAINEL ARRASTÁVEL
--========================================================

local Arrastando = false
local Inicio
local PosicaoInicial

Title.InputBegan:Connect(function(Input)

	if Input.UserInputType == Enum.UserInputType.MouseButton1
		or Input.UserInputType == Enum.UserInputType.Touch then

		Arrastando = true
		Inicio = Input.Position
		PosicaoInicial = Main.Position
	end
end)

Title.InputChanged:Connect(function(Input)

	if Input.UserInputType == Enum.UserInputType.MouseMovement
		or Input.UserInputType == Enum.UserInputType.Touch then

		local Movimento = Input

		Movimento.Changed:Connect(function()

			if Movimento.UserInputState ==
				Enum.UserInputState.End then

				Arrastando = false
			end
		end)
	end
end)

UserInputService.InputChanged:Connect(function(Input)

	if not Arrastando then
		return
	end

	if Input.UserInputType ~= Enum.UserInputType.MouseMovement
		and Input.UserInputType ~= Enum.UserInputType.Touch then
		return
	end

	local Delta = Input.Position - Inicio

	Main.Position = UDim2.new(
		PosicaoInicial.X.Scale,
		PosicaoInicial.X.Offset + Delta.X,
		PosicaoInicial.Y.Scale,
		PosicaoInicial.Y.Offset + Delta.Y
	)
end)

--========================================================
-- BOTÃO FECHAR
--========================================================

local Close = Instance.new("TextButton")
Close.Size = UDim2.new(0, 32, 0, 32)
Close.Position = UDim2.new(1, -38, 0, 7)
Close.BackgroundColor3 = Color3.fromRGB(120, 30, 30)
Close.Text = "X"
Close.TextColor3 = Color3.fromRGB(255, 255, 255)
Close.TextSize = 16
Close.Font = Enum.Font.GothamBold
Close.Parent = Main

local CloseCorner = Instance.new("UICorner")
CloseCorner.CornerRadius = UDim.new(0, 7)
CloseCorner.Parent = Close

Close.MouseButton1Click:Connect(function()
	Main.Visible = false
end)

--========================================================
-- BOTÃO ABRIR
--========================================================

local OpenButton = Instance.new("TextButton")
OpenButton.Size = UDim2.new(0, 55, 0, 55)
OpenButton.Position = UDim2.new(0, 15, 0.5, -27)
OpenButton.BackgroundColor3 = Color3.fromRGB(25, 25, 25)
OpenButton.Text = "☠"
OpenButton.TextColor3 = Color3.fromRGB(255, 255, 255)
OpenButton.TextSize = 24
OpenButton.Font = Enum.Font.GothamBold
OpenButton.Visible = false
OpenButton.Parent = ScreenGui

local OpenCorner = Instance.new("UICorner")
OpenCorner.CornerRadius = UDim.new(1, 0)
OpenCorner.Parent = OpenButton

OpenButton.MouseButton1Click:Connect(function()
	Main.Visible = true
	OpenButton.Visible = false
end)

Close.MouseButton1Click:Connect(function()
	Main.Visible = false
	OpenButton.Visible = true
end)

--========================================================
-- FIM
--========================================================
```
