```lua
--========================================================
-- MURDER PANEL
-- FLY + INVISIBLE + ESP + TP PLAYER + PUXAR PLAYER
--
-- LOCAL SCRIPT
-- StarterPlayer > StarterPlayerScripts
--========================================================

local Players = game:GetService("Players")
local UserInputService = game:GetService("UserInputService")
local RunService = game:GetService("RunService")
local ReplicatedStorage = game:GetService("ReplicatedStorage")

local Player = Players.LocalPlayer
local Camera = workspace.CurrentCamera

--========================================================
-- REMOTE
--========================================================

local RemoteFolder = ReplicatedStorage:WaitForChild("MurderPanelRemotes")
local TPRemote = RemoteFolder:WaitForChild("TPPlayer")

--========================================================
-- VARIÁVEIS
--========================================================

local Character
local Humanoid
local RootPart

local FlyAtivo = false
local InvisibleAtivo = false
local ESPAtivo = false

local FlySpeed = 60

local FlyConnection
local FlyVelocity
local FlyGyro

local ESPs = {}

local JogadorSelecionado = nil

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
ScreenGui.Name = "MurderPanel"
ScreenGui.ResetOnSpawn = false
ScreenGui.Parent = Player:WaitForChild("PlayerGui")

local Main = Instance.new("Frame")
Main.Name = "Main"
Main.Size = UDim2.new(0, 420, 0, 430)
Main.Position = UDim2.new(0.5, -210, 0.5, -215)
Main.BackgroundColor3 = Color3.fromRGB(23, 23, 23)
Main.BorderSizePixel = 0
Main.Parent = ScreenGui

local MainCorner = Instance.new("UICorner")
MainCorner.CornerRadius = UDim.new(0, 10)
MainCorner.Parent = Main

local MainStroke = Instance.new("UIStroke")
MainStroke.Color = Color3.fromRGB(70, 70, 70)
MainStroke.Thickness = 2
MainStroke.Parent = Main

--========================================================
-- TÍTULO
--========================================================

local Title = Instance.new("TextLabel")
Title.Size = UDim2.new(1, -60, 0, 45)
Title.Position = UDim2.new(0, 15, 0, 0)
Title.BackgroundTransparency = 1
Title.Text = "☠  MURDER PANEL"
Title.TextColor3 = Color3.fromRGB(255, 255, 255)
Title.TextSize = 22
Title.Font = Enum.Font.GothamBold
Title.TextXAlignment = Enum.TextXAlignment.Left
Title.Parent = Main

--========================================================
-- FECHAR
--========================================================

local Close = Instance.new("TextButton")
Close.Size = UDim2.new(0, 32, 0, 32)
Close.Position = UDim2.new(1, -40, 0, 7)
Close.BackgroundColor3 = Color3.fromRGB(120, 30, 30)
Close.Text = "X"
Close.TextColor3 = Color3.fromRGB(255, 255, 255)
Close.TextSize = 16
Close.Font = Enum.Font.GothamBold
Close.Parent = Main

local CloseCorner = Instance.new("UICorner")
CloseCorner.CornerRadius = UDim.new(0, 7)
CloseCorner.Parent = Close

--========================================================
-- ABRIR
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

Close.MouseButton1Click:Connect(function()
	Main.Visible = false
	OpenButton.Visible = true
end)

OpenButton.MouseButton1Click:Connect(function()
	Main.Visible = true
	OpenButton.Visible = false
end)

--========================================================
-- ABAS
--========================================================

local TabArea = Instance.new("Frame")
TabArea.Size = UDim2.new(1, -20, 0, 40)
TabArea.Position = UDim2.new(0, 10, 0, 48)
TabArea.BackgroundTransparency = 1
TabArea.Parent = Main

local function CriarAba(Texto, Posicao)
	local Button = Instance.new("TextButton")

	Button.Size = UDim2.new(0, 125, 1, 0)
	Button.Position = Posicao
	Button.BackgroundColor3 = Color3.fromRGB(40, 40, 40)
	Button.Text = Texto
	Button.TextColor3 = Color3.fromRGB(255, 255, 255)
	Button.TextSize = 15
	Button.Font = Enum.Font.GothamBold
	Button.Parent = TabArea

	local Corner = Instance.new("UICorner")
	Corner.CornerRadius = UDim.new(0, 7)
	Corner.Parent = Button

	return Button
end

local MainTab = CriarAba(
	"⚙ FUNÇÕES",
	UDim2.new(0, 0, 0, 0)
)

local TPTab = CriarAba(
	"👤 TP PLAYER",
	UDim2.new(0, 135, 0, 0)
)

--========================================================
-- PÁGINA FUNÇÕES
--========================================================

local FuncoesPage = Instance.new("Frame")
FuncoesPage.Size = UDim2.new(1, -20, 1, -100)
FuncoesPage.Position = UDim2.new(0, 10, 0, 95)
FuncoesPage.BackgroundTransparency = 1
FuncoesPage.Parent = Main

local function CriarBotao(Nome, Posicao)
	local Button = Instance.new("TextButton")

	Button.Size = UDim2.new(1, 0, 0, 55)
	Button.Position = Posicao
	Button.BackgroundColor3 = Color3.fromRGB(40, 40, 40)
	Button.BorderSizePixel = 0
	Button.Text = Nome .. "  [OFF]"
	Button.TextColor3 = Color3.fromRGB(255, 255, 255)
	Button.TextSize = 17
	Button.Font = Enum.Font.GothamBold
	Button.Parent = FuncoesPage

	local Corner = Instance.new("UICorner")
	Corner.CornerRadius = UDim.new(0, 7)
	Corner.Parent = Button

	return Button
end

local FlyButton = CriarBotao(
	"✈️ FLY",
	UDim2.new(0, 0, 0, 10)
)

local InvisibleButton = CriarBotao(
	"👻 INVISIBLE",
	UDim2.new(0, 0, 0, 75)
)

local ESPButton = CriarBotao(
	"👁 ESP",
	UDim2.new(0, 0, 0, 140)
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
	FlyVelocity.Name = "MurderFlyVelocity"
	FlyVelocity.MaxForce = Vector3.new(
		math.huge,
		math.huge,
		math.huge
	)
	FlyVelocity.Velocity = Vector3.zero
	FlyVelocity.Parent = RootPart

	FlyGyro = Instance.new("BodyGyro")
	FlyGyro.Name = "MurderFlyGyro"
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

	local Highlight = Instance.new("Highlight")
	Highlight.Name = "MurderESP"
	Highlight.FillTransparency = 0.65
	Highlight.OutlineTransparency = 0
	Highlight.DepthMode = Enum.HighlightDepthMode.AlwaysOnTop
	Highlight.Parent = Char

	local Billboard = Instance.new("BillboardGui")
	Billboard.Name = "MurderESP_Nome"
	Billboard.Size = UDim2.new(0, 170, 0, 35)
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

Players.PlayerAdded:Connect(function(PlayerAlvo)

	PlayerAlvo.CharacterAdded:Connect(function()

		task.wait(0.5)

		if ESPAtivo then
			CriarESP(PlayerAlvo)
		end
	end)
end)

Players.PlayerRemoving:Connect(function(PlayerAlvo)
	RemoverESP(PlayerAlvo)
end)

--========================================================
-- PÁGINA TP
--========================================================

local TPPage = Instance.new("Frame")
TPPage.Size = UDim2.new(1, -20, 1, -100)
TPPage.Position = UDim2.new(0, 10, 0, 95)
TPPage.BackgroundTransparency = 1
TPPage.Visible = false
TPPage.Parent = Main

--========================================================
-- PESQUISA
--========================================================

local SearchBox = Instance.new("TextBox")
SearchBox.Size = UDim2.new(1, 0, 0, 40)
SearchBox.Position = UDim2.new(0, 0, 0, 0)
SearchBox.BackgroundColor3 = Color3.fromRGB(35, 35, 35)
SearchBox.BorderSizePixel = 0
SearchBox.PlaceholderText = "🔎 Pesquisar jogador..."
SearchBox.PlaceholderColor3 = Color3.fromRGB(160, 160, 160)
SearchBox.Text = ""
SearchBox.TextColor3 = Color3.fromRGB(255, 255, 255)
SearchBox.TextSize = 15
SearchBox.Font = Enum.Font.Gotham
SearchBox.ClearTextOnFocus = false
SearchBox.Parent = TPPage

local SearchCorner = Instance.new("UICorner")
SearchCorner.CornerRadius = UDim.new(0, 7)
SearchCorner.Parent = SearchBox

--========================================================
-- SELECIONADO
--========================================================

local SelectedLabel = Instance.new("TextLabel")
SelectedLabel.Size = UDim2.new(1, 0, 0, 28)
SelectedLabel.Position = UDim2.new(0, 0, 0, 45)
SelectedLabel.BackgroundTransparency = 1
SelectedLabel.Text = "Nenhum jogador selecionado"
SelectedLabel.TextColor3 = Color3.fromRGB(190, 190, 190)
SelectedLabel.TextSize = 14
SelectedLabel.Font = Enum.Font.Gotham
SelectedLabel.TextXAlignment = Enum.TextXAlignment.Left
SelectedLabel.Parent = TPPage

--========================================================
-- LISTA
--========================================================

local PlayerList = Instance.new("ScrollingFrame")
PlayerList.Size = UDim2.new(1, 0, 1, -190)
PlayerList.Position = UDim2.new(0, 0, 0, 78)
PlayerList.BackgroundColor3 = Color3.fromRGB(30, 30, 30)
PlayerList.BorderSizePixel = 0
PlayerList.ScrollBarThickness = 5
PlayerList.CanvasSize = UDim2.new(0, 0, 0, 0)
PlayerList.AutomaticCanvasSize = Enum.AutomaticSize.Y
PlayerList.Parent = TPPage

local ListCorner = Instance.new("UICorner")
ListCorner.CornerRadius = UDim.new(0, 7)
ListCorner.Parent = PlayerList

local ListLayout = Instance.new("UIListLayout")
ListLayout.Padding = UDim.new(0, 5)
ListLayout.SortOrder = Enum.SortOrder.Name
ListLayout.Parent = PlayerList

local ListPadding = Instance.new("UIPadding")
ListPadding.PaddingTop = UDim.new(0, 6)
ListPadding.PaddingBottom = UDim.new(0, 6)
ListPadding.PaddingLeft = UDim.new(0, 6)
ListPadding.PaddingRight = UDim.new(0, 6)
ListPadding.Parent = PlayerList

--========================================================
-- BOTÃO TP
--========================================================

local TPButton = Instance.new("TextButton")
TPButton.Size = UDim2.new(1, 0, 0, 45)
TPButton.Position = UDim2.new(0, 0, 1, -105)
TPButton.BackgroundColor3 = Color3.fromRGB(0, 100, 160)
TPButton.BorderSizePixel = 0
TPButton.Text = "📍 DAR TP NO JOGADOR"
TPButton.TextColor3 = Color3.fromRGB(255, 255, 255)
TPButton.TextSize = 15
TPButton.Font = Enum.Font.GothamBold
TPButton.Parent = TPPage

local TPCorner = Instance.new("UICorner")
TPCorner.CornerRadius = UDim.new(0, 7)
TPCorner.Parent = TPButton

--========================================================
-- BOTÃO PUXAR
--========================================================

local PullButton = Instance.new("TextButton")
PullButton.Size = UDim2.new(1, 0, 0, 45)
PullButton.Position = UDim2.new(0, 0, 1, -52)
PullButton.BackgroundColor3 = Color3.fromRGB(130, 70, 0)
PullButton.BorderSizePixel = 0
PullButton.Text = "🧲 PUXAR PLAYER ATÉ MIM"
PullButton.TextColor3 = Color3.fromRGB(255, 255, 255)
PullButton.TextSize = 15
PullButton.Font = Enum.Font.GothamBold
PullButton.Parent = TPPage

local PullCorner = Instance.new("UICorner")
PullCorner.CornerRadius = UDim.new(0, 7)
PullCorner.Parent = PullButton

--========================================================
-- CRIAR PLAYER NA LISTA
--========================================================

local function CriarPlayerButton(PlayerAlvo)

	local Button = Instance.new("TextButton")

	Button.Name = PlayerAlvo.Name
	Button.Size = UDim2.new(1, -2, 0, 42)
	Button.BackgroundColor3 = Color3.fromRGB(42, 42, 42)
	Button.BorderSizePixel = 0

	Button.Text =
		PlayerAlvo.DisplayName ..
		"  @" ..
		PlayerAlvo.Name

	Button.TextColor3 = Color3.fromRGB(255, 255, 255)
	Button.TextSize = 14
	Button.Font = Enum.Font.Gotham
	Button.Parent = PlayerList

	local Corner = Instance.new("UICorner")
	Corner.CornerRadius = UDim.new(0, 6)
	Corner.Parent = Button

	Button.MouseButton1Click:Connect(function()

		JogadorSelecionado = PlayerAlvo

		SelectedLabel.Text =
			"Selecionado: " ..
			PlayerAlvo.DisplayName ..
			"  (@" ..
			PlayerAlvo.Name ..
			")"

		for _, Outro in ipairs(PlayerList:GetChildren()) do

			if Outro:IsA("TextButton") then
				Outro.BackgroundColor3 =
					Color3.fromRGB(42, 42, 42)
			end
		end

		Button.BackgroundColor3 =
			Color3.fromRGB(0, 130, 70)
	end)
end

--========================================================
-- ATUALIZAR LISTA
--========================================================

local function AtualizarLista()

	for _, Obj in ipairs(PlayerList:GetChildren()) do

		if Obj:IsA("TextButton") then
			Obj:Destroy()
		end
	end

	local Pesquisa = string.lower(SearchBox.Text)

	for _, PlayerAlvo in ipairs(Players:GetPlayers()) do

		if PlayerAlvo ~= Player then

			local Nome = string.lower(PlayerAlvo.Name)
			local DisplayName =
				string.lower(PlayerAlvo.DisplayName)

			if Pesquisa == ""
				or string.find(Nome, Pesquisa, 1, true)
				or string.find(DisplayName, Pesquisa, 1, true) then

				CriarPlayerButton(PlayerAlvo)
			end
		end
	end
end

SearchBox:GetPropertyChangedSignal("Text"):Connect(function()
	AtualizarLista()
end)

Players.PlayerAdded:Connect(function()
	task.wait(0.1)
	AtualizarLista()
end)

Players.PlayerRemoving:Connect(function(PlayerAlvo)

	if JogadorSelecionado == PlayerAlvo then
		JogadorSelecionado = nil
		SelectedLabel.Text = "Nenhum jogador selecionado"
	end

	task.wait(0.1)
	AtualizarLista()
end)

--========================================================
-- DAR TP NO PLAYER
--========================================================

TPButton.MouseButton1Click:Connect(function()

	if not JogadorSelecionado then

		SelectedLabel.Text =
			"⚠ Selecione um jogador primeiro!"

		return
	end

	if JogadorSelecionado == Player then
		return
	end

	TPRemote:FireServer(
		"TP",
		JogadorSelecionado
	)
end)

--========================================================
-- PUXAR PLAYER
--========================================================

PullButton.MouseButton1Click:Connect(function()

	if not JogadorSelecionado then

		SelectedLabel.Text =
			"⚠ Selecione um jogador primeiro!"

		return
	end

	if JogadorSelecionado == Player then
		return
	end

	TPRemote:FireServer(
		"PULL",
		JogadorSelecionado
	)
end)

--========================================================
-- RETORNO DO SERVIDOR
--========================================================

TPRemote.OnClientEvent:Connect(function(Sucesso, Mensagem)

	if Sucesso then

		SelectedLabel.Text =
			"✓ " .. tostring(Mensagem)

	else

		SelectedLabel.Text =
			"⚠ " .. tostring(Mensagem)
	end
end)

--========================================================
-- TROCAR ABAS
--========================================================

MainTab.MouseButton1Click:Connect(function()

	FuncoesPage.Visible = true
	TPPage.Visible = false

	MainTab.BackgroundColor3 =
		Color3.fromRGB(0, 130, 70)

	TPTab.BackgroundColor3 =
		Color3.fromRGB(40, 40, 40)
end)

TPTab.MouseButton1Click:Connect(function()

	FuncoesPage.Visible = false
	TPPage.Visible = true

	MainTab.BackgroundColor3 =
		Color3.fromRGB(40, 40, 40)

	TPTab.BackgroundColor3 =
		Color3.fromRGB(0, 130, 70)

	AtualizarLista()
end)

--========================================================
-- ARRASTAR PAINEL
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

UserInputService.InputEnded:Connect(function(Input)

	if Input.UserInputType == Enum.UserInputType.MouseButton1
		or Input.UserInputType == Enum.UserInputType.Touch then

		Arrastando = false
	end
end)

--========================================================
-- INICIALIZAÇÃO
--========================================================

MainTab.BackgroundColor3 =
	Color3.fromRGB(0, 130, 70)

AtualizarLista()

--========================================================
-- FIM
--========================================================
```
