--========================================================
-- 🔪 MURDER PANEL
-- LOCAL SCRIPT ÚNICO
--========================================================

local Players = game:GetService("Players")
local UIS = game:GetService("UserInputService")
local RunService = game:GetService("RunService")

local LP = Players.LocalPlayer
local PlayerGui = LP:WaitForChild("PlayerGui")

--========================================================
-- LIMPAR PAINEL ANTIGO
--========================================================

local old = PlayerGui:FindFirstChild("MurderPanel")
if old then
	old:Destroy()
end

--========================================================
-- CONFIG
--========================================================

local Fly = false
local Invis = false
local ESP = false
local Speed = 70

local SelectedPlayer = nil
local LocalRole = "Inocente"

local ESPData = {}

--========================================================
-- GUI
--========================================================

local Gui = Instance.new("ScreenGui")
Gui.Name = "MurderPanel"
Gui.ResetOnSpawn = false
Gui.IgnoreGuiInset = true
Gui.ZIndexBehavior = Enum.ZIndexBehavior.Sibling
Gui.Parent = PlayerGui

--========================================================
-- FUNÇÕES GUI
--========================================================

local function Corner(obj, radius)
	local c = Instance.new("UICorner")
	c.CornerRadius = UDim.new(0, radius or 7)
	c.Parent = obj
end

local function Button(parent, text, size, position)
	local b = Instance.new("TextButton")

	b.Size = size
	b.Position = position

	b.BackgroundColor3 = Color3.fromRGB(45,45,50)
	b.BorderSizePixel = 0

	b.Text = text
	b.TextColor3 = Color3.fromRGB(255,255,255)
	b.TextSize = 13
	b.Font = Enum.Font.GothamBold

	b.AutoButtonColor = true

	b.Parent = parent

	Corner(b)

	return b
end

local function SetButton(button, name, state)
	if state then
		button.Text = name .. ": LIGADO"
		button.BackgroundColor3 = Color3.fromRGB(40,140,65)
	else
		button.Text = name .. ": DESLIGADO"
		button.BackgroundColor3 = Color3.fromRGB(45,45,50)
	end
end

--========================================================
-- BOTÃO ABRIR
--========================================================

local OpenButton = Button(
	Gui,
	"☰",
	UDim2.fromOffset(45,45),
	UDim2.new(0,15,0.5,-22)
)

OpenButton.Visible = false

--========================================================
-- PAINEL
--========================================================

local Main = Instance.new("Frame")

Main.Size = UDim2.fromOffset(700,540)
Main.Position = UDim2.new(0.5,-350,0.5,-270)

Main.BackgroundColor3 = Color3.fromRGB(27,27,31)
Main.BorderSizePixel = 0

Main.Active = true
Main.Parent = Gui

Corner(Main,10)

--========================================================
-- CABEÇALHO
--========================================================

local Header = Instance.new("Frame")

Header.Size = UDim2.new(1,0,0,50)

Header.BackgroundColor3 = Color3.fromRGB(17,17,20)
Header.BorderSizePixel = 0

Header.Active = true
Header.Parent = Main

Corner(Header,10)

local Title = Instance.new("TextLabel")

Title.Size = UDim2.new(1,-65,1,0)
Title.Position = UDim2.fromOffset(15,0)

Title.BackgroundTransparency = 1

Title.Text = "🔪 MURDER PANEL"
Title.TextColor3 = Color3.fromRGB(255,255,255)
Title.TextSize = 18
Title.Font = Enum.Font.GothamBold

Title.TextXAlignment = Enum.TextXAlignment.Left

Title.Parent = Header

local Close = Button(
	Header,
	"X",
	UDim2.fromOffset(38,36),
	UDim2.new(1,-45,0,7)
)

--========================================================
-- ARRASTAR PAINEL
--========================================================

local Dragging = false
local DragStart
local StartPosition

local function StartDrag(input)
	Dragging = true
	DragStart = input.Position
	StartPosition = Main.Position
end

local function UpdateDrag(input)

	if not Dragging then
		return
	end

	local Delta = input.Position - DragStart

	Main.Position = UDim2.new(
		StartPosition.X.Scale,
		StartPosition.X.Offset + Delta.X,

		StartPosition.Y.Scale,
		StartPosition.Y.Offset + Delta.Y
	)
end

Header.InputBegan:Connect(function(input)

	if input.UserInputType == Enum.UserInputType.MouseButton1
		or input.UserInputType == Enum.UserInputType.Touch then

		StartDrag(input)

	end

end)

UIS.InputChanged:Connect(function(input)

	if input.UserInputType == Enum.UserInputType.MouseMovement
		or input.UserInputType == Enum.UserInputType.Touch then

		UpdateDrag(input)

	end

end)

UIS.InputEnded:Connect(function(input)

	if input.UserInputType == Enum.UserInputType.MouseButton1
		or input.UserInputType == Enum.UserInputType.Touch then

		Dragging = false

	end

end)

--========================================================
-- ABAS
--========================================================

local Tabs = Instance.new("Frame")

Tabs.Size = UDim2.new(1,-20,0,42)
Tabs.Position = UDim2.fromOffset(10,60)

Tabs.BackgroundTransparency = 1
Tabs.Parent = Main

local FunctionsTab = Button(
	Tabs,
	"⚙ FUNÇÕES",
	UDim2.fromOffset(150,40),
	UDim2.fromOffset(0,0)
)

local PlayersTab = Button(
	Tabs,
	"👤 PLAYERS",
	UDim2.fromOffset(150,40),
	UDim2.fromOffset(160,0)
)

local InvestigateTab = Button(
	Tabs,
	"🔍 INVESTIGAR",
	UDim2.fromOffset(150,40),
	UDim2.fromOffset(320,0)
)

--========================================================
-- CONTEÚDO
--========================================================

local Content = Instance.new("Frame")

Content.Size = UDim2.new(1,-20,1,-115)
Content.Position = UDim2.fromOffset(10,110)

Content.BackgroundTransparency = 1
Content.Parent = Main

--========================================================
-- ABA FUNÇÕES
--========================================================

local Functions = Instance.new("Frame")

Functions.Size = UDim2.fromScale(1,1)
Functions.BackgroundTransparency = 1
Functions.Parent = Content

local FlyButton = Button(
	Functions,
	"✈ FLY: DESLIGADO",
	UDim2.fromOffset(205,45),
	UDim2.fromOffset(10,10)
)

local InvisButton = Button(
	Functions,
	"👻 INVISÍVEL: DESLIGADO",
	UDim2.fromOffset(205,45),
	UDim2.fromOffset(225,10)
)

local ESPButton = Button(
	Functions,
	"👁 ESP: DESLIGADO",
	UDim2.fromOffset(205,45),
	UDim2.fromOffset(440,10)
)

local Help = Instance.new("TextLabel")

Help.Size = UDim2.new(1,-20,0,150)
Help.Position = UDim2.fromOffset(10,75)

Help.BackgroundTransparency = 1

Help.Text =
	"✈ FLY\n" ..
	"WASD = mover\n" ..
	"ESPAÇO = subir\n" ..
	"CTRL = descer\n\n" ..
	"👻 Invisibilidade local\n" ..
	"👁 ESP dos jogadores"

Help.TextColor3 = Color3.fromRGB(190,190,190)
Help.TextSize = 14
Help.Font = Enum.Font.Gotham

Help.TextXAlignment = Enum.TextXAlignment.Left
Help.TextYAlignment = Enum.TextYAlignment.Top

Help.Parent = Functions

--========================================================
-- ABA PLAYERS
--========================================================

local PlayersFrame = Instance.new("Frame")

PlayersFrame.Size = UDim2.fromScale(1,1)
PlayersFrame.BackgroundTransparency = 1
PlayersFrame.Visible = false
PlayersFrame.Parent = Content

local SearchPlayers = Instance.new("TextBox")

SearchPlayers.Size = UDim2.new(1,-20,0,40)
SearchPlayers.Position = UDim2.fromOffset(10,5)

SearchPlayers.BackgroundColor3 = Color3.fromRGB(38,38,43)
SearchPlayers.BorderSizePixel = 0

SearchPlayers.PlaceholderText = "🔎 Pesquisar jogador..."
SearchPlayers.PlaceholderColor3 = Color3.fromRGB(150,150,150)

SearchPlayers.Text = ""
SearchPlayers.TextColor3 = Color3.fromRGB(255,255,255)
SearchPlayers.TextSize = 14
SearchPlayers.Font = Enum.Font.Gotham

SearchPlayers.Parent = PlayersFrame

Corner(SearchPlayers)

local PlayerList = Instance.new("ScrollingFrame")

PlayerList.Size = UDim2.new(1,-20,0,280)
PlayerList.Position = UDim2.fromOffset(10,55)

PlayerList.BackgroundColor3 = Color3.fromRGB(20,20,23)
PlayerList.BorderSizePixel = 0

PlayerList.ScrollBarThickness = 5
PlayerList.CanvasSize = UDim2.new()

PlayerList.Parent = PlayersFrame

Corner(PlayerList)

local PlayerLayout = Instance.new("UIListLayout")

PlayerLayout.Padding = UDim.new(0,4)
PlayerLayout.Parent = PlayerList

local TPButton = Button(
	PlayersFrame,
	"📍 IR ATÉ O PLAYER",
	UDim2.new(0.48,-5,0,45),
	UDim2.new(0,10,1,-55)
)

local PullButton = Button(
	PlayersFrame,
	"🧲 PUXAR PLAYER",
	UDim2.new(0.48,-5,0,45),
	UDim2.new(0.52,0,1,-55)
)

--========================================================
-- ABA INVESTIGAR
--========================================================

local Investigate = Instance.new("Frame")

Investigate.Size = UDim2.fromScale(1,1)
Investigate.BackgroundTransparency = 1
Investigate.Visible = false
Investigate.Parent = Content

local SearchInvestigate = Instance.new("TextBox")

SearchInvestigate.Size = UDim2.new(0.42,-10,0,40)
SearchInvestigate.Position = UDim2.fromOffset(10,5)

SearchInvestigate.BackgroundColor3 = Color3.fromRGB(38,38,43)
SearchInvestigate.BorderSizePixel = 0

SearchInvestigate.PlaceholderText = "🔎 Pesquisar jogador..."
SearchInvestigate.PlaceholderColor3 = Color3.fromRGB(150,150,150)

SearchInvestigate.Text = ""
SearchInvestigate.TextColor3 = Color3.fromRGB(255,255,255)
SearchInvestigate.TextSize = 14
SearchInvestigate.Font = Enum.Font.Gotham

SearchInvestigate.Parent = Investigate

Corner(SearchInvestigate)

local InvestigateList = Instance.new("ScrollingFrame")

InvestigateList.Size = UDim2.new(0.42,-10,0,235)
InvestigateList.Position = UDim2.fromOffset(10,55)

InvestigateList.BackgroundColor3 = Color3.fromRGB(20,20,23)
InvestigateList.BorderSizePixel = 0

InvestigateList.ScrollBarThickness = 5
InvestigateList.CanvasSize = UDim2.new()

InvestigateList.Parent = Investigate

Corner(InvestigateList)

local InvestigateLayout = Instance.new("UIListLayout")

InvestigateLayout.Padding = UDim.new(0,4)
InvestigateLayout.Parent = InvestigateList

local Result = Instance.new("Frame")

Result.Size = UDim2.new(0.58,-10,0,235)
Result.Position = UDim2.new(0.42,0,0,55)

Result.BackgroundColor3 = Color3.fromRGB(21,21,25)
Result.BorderSizePixel = 0

Result.Parent = Investigate

Corner(Result)

local ResultText = Instance.new("TextLabel")

ResultText.Size = UDim2.new(1,-20,1,-20)
ResultText.Position = UDim2.fromOffset(10,10)

ResultText.BackgroundTransparency = 1

ResultText.Text = "🔍\n\nSelecione um jogador."

ResultText.TextColor3 = Color3.fromRGB(235,235,235)
ResultText.TextSize = 14
ResultText.Font = Enum.Font.Gotham

ResultText.TextWrapped = true
ResultText.TextXAlignment = Enum.TextXAlignment.Left
ResultText.TextYAlignment = Enum.TextYAlignment.Top

ResultText.Parent = Result

local RefreshItems = Button(
	Investigate,
	"🔄 ATUALIZAR ITENS",
	UDim2.new(0.42,-10,0,40),
	UDim2.fromOffset(10,300)
)

local RandomRole = Button(
	Investigate,
	"🎲 SORTEAR",
	UDim2.new(0.27,-5,0,40),
	UDim2.new(0.42,0,0,300)
)

local AssassinRole = Button(
	Investigate,
	"🗡 ASSASSINO",
	UDim2.new(0.27,-5,0,40),
	UDim2.new(0.71,0,0,300)
)

local SheriffRole = Button(
	Investigate,
	"🔫 XERIFE",
	UDim2.new(0.27,-5,0,40),
	UDim2.new(0.42,0,0,350)
)

local InnocentRole = Button(
	Investigate,
	"👤 INOCENTE",
	UDim2.new(0.27,-5,0,40),
	UDim2.new(0.71,0,0,350)
)

--========================================================
-- LISTAS
--========================================================

local function ClearList(list)

	for _,obj in ipairs(list:GetChildren()) do

		if obj:IsA("TextButton") then
			obj:Destroy()
		end

	end

end

local function MatchPlayer(player,text)

	if text == "" then
		return true
	end

	text = string.lower(text)

	return
		string.find(string.lower(player.Name),text,1,true)
		or
		string.find(string.lower(player.DisplayName),text,1,true)

end

local function SelectPlayer(player,list)

	SelectedPlayer = player

	for _,obj in ipairs(list:GetChildren()) do

		if obj:IsA("TextButton") then

			if obj:GetAttribute("PlayerName") == player.Name then
				obj.BackgroundColor3 = Color3.fromRGB(40,140,65)
			else
				obj.BackgroundColor3 = Color3.fromRGB(45,45,50)
			end

		end

	end

	if list == InvestigateList then
		InvestigatePlayer()
	end

end

local function CreatePlayerButton(player,list)

	local b = Button(
		list,
		player.DisplayName .. "  @" .. player.Name,
		UDim2.new(1,-10,0,36),
		UDim2.new()
	)

	b:SetAttribute("PlayerName",player.Name)

	if player == SelectedPlayer then
		b.BackgroundColor3 = Color3.fromRGB(40,140,65)
	end

	b.MouseButton1Click:Connect(function()
		SelectPlayer(player,list)
	end)

end

local function UpdatePlayerList()

	ClearList(PlayerList)

	local search = SearchPlayers.Text

	for _,player in ipairs(Players:GetPlayers()) do

		if player ~= LP and MatchPlayer(player,search) then
			CreatePlayerButton(player,PlayerList)
		end

	end

	task.wait()

	PlayerList.CanvasSize =
		UDim2.fromOffset(0,PlayerLayout.AbsoluteContentSize.Y + 10)

end

local function UpdateInvestigateList()

	ClearList(InvestigateList)

	local search = SearchInvestigate.Text

	for _,player in ipairs(Players:GetPlayers()) do

		if MatchPlayer(player,search) then
			CreatePlayerButton(player,InvestigateList)
		end

	end

	task.wait()

	InvestigateList.CanvasSize =
		UDim2.fromOffset(
			0,
			InvestigateLayout.AbsoluteContentSize.Y + 10
		)

end

--========================================================
-- INVESTIGAÇÃO
--========================================================

function InvestigatePlayer()

	if not SelectedPlayer then

		ResultText.Text =
			"🔍\n\nSelecione um jogador."

		return

	end

	local tools = {}
	local count = {}

	local function ReadTools(container)

		if not container then
			return
		end

		for _,obj in ipairs(container:GetChildren()) do

			if obj:IsA("Tool") then

				count[obj.Name] =
					(count[obj.Name] or 0) + 1

			end

		end

	end

	ReadTools(SelectedPlayer:FindFirstChild("Backpack"))
	ReadTools(SelectedPlayer.Character)

	for name,amount in pairs(count) do

		table.insert(
			tools,
			{
				Name = name,
				Amount = amount
			}
		)

	end

	table.sort(tools,function(a,b)

		return string.lower(a.Name)
			<
			string.lower(b.Name)

	end)

	local detected = "NÃO IDENTIFICADO"

	for _,tool in ipairs(tools) do

		local name =
			string.lower(
				string.gsub(tool.Name,"%s+","")
			)

		if
			name == "knife"
			or name == "faca"
			or name == "murderknife"
			or name == "assassin"
			or name == "dagger"
		then

			detected = "ASSASSINO"

		elseif
			name == "gun"
			or name == "pistol"
			or name == "sheriff"
			or name == "sheriffgun"
			or name == "revolver"
			or name == "arma"
		then

			detected = "XERIFE"

		end

	end

	local text =
		"👤 " ..
		SelectedPlayer.DisplayName ..
		"\n@" ..
		SelectedPlayer.Name ..
		"\n\n" ..

		"🔎 POSSÍVEL ROLE: " ..
		detected ..
		"\n\n" ..

		"🎒 ITENS:\n"

	if #tools == 0 then

		text = text ..
			"Nenhuma Tool encontrada."

	else

		for _,tool in ipairs(tools) do

			text = text ..
				"• " ..
				tool.Name ..
				" x" ..
				tool.Amount ..
				"\n"

		end

	end

	ResultText.Text = text

end

--========================================================
-- TP
--========================================================

TPButton.MouseButton1Click:Connect(function()

	if not SelectedPlayer then
		return
	end

	local myChar = LP.Character
	local targetChar = SelectedPlayer.Character

	if not myChar or not targetChar then
		return
	end

	local myRoot = myChar:FindFirstChild("HumanoidRootPart")
	local targetRoot = targetChar:FindFirstChild("HumanoidRootPart")

	if myRoot and targetRoot then

		myRoot.CFrame =
			targetRoot.CFrame *
			CFrame.new(3,0,0)

	end

end)

--========================================================
-- PUXAR
--========================================================

PullButton.MouseButton1Click:Connect(function()

	if not SelectedPlayer then
		return
	end

	local myChar = LP.Character
	local targetChar = SelectedPlayer.Character

	if not myChar or not targetChar then
		return
	end

	local myRoot = myChar:FindFirstChild("HumanoidRootPart")
	local targetRoot = targetChar:FindFirstChild("HumanoidRootPart")

	if myRoot and targetRoot then

		-- Tentativa local para jogos onde o cliente
		-- possui a network ownership necessária.
		targetRoot.CFrame =
			myRoot.CFrame *
			CFrame.new(3,0,0)

	end

end)

--========================================================
-- FLY
--========================================================

local FlyConnection

local function EnableFly()

	Fly = true

	SetButton(FlyButton,"✈ FLY",true)

	if FlyConnection then
		FlyConnection:Disconnect()
	end

	FlyConnection =
		RunService.RenderStepped:Connect(function()

			if not Fly then
				return
			end

			local char = LP.Character

			if not char then
				return
			end

			local humanoid =
				char:FindFirstChildOfClass("Humanoid")

			local root =
				char:FindFirstChild("HumanoidRootPart")

			if not humanoid or not root then
				return
			end

			local camera =
				workspace.CurrentCamera

			local direction = Vector3.zero

			if UIS:IsKeyDown(Enum.KeyCode.W) then
				direction += camera.CFrame.LookVector
			end

			if UIS:IsKeyDown(Enum.KeyCode.S) then
				direction -= camera.CFrame.LookVector
			end

			if UIS:IsKeyDown(Enum.KeyCode.A) then
				direction -= camera.CFrame.RightVector
			end

			if UIS:IsKeyDown(Enum.KeyCode.D) then
				direction += camera.CFrame.RightVector
			end

			if UIS:IsKeyDown(Enum.KeyCode.Space) then
				direction += Vector3.yAxis
			end

			if UIS:IsKeyDown(Enum.KeyCode.LeftControl) then
				direction -= Vector3.yAxis
			end

			if direction.Magnitude > 0 then

				root.AssemblyLinearVelocity =
					direction.Unit * Speed

			else

				root.AssemblyLinearVelocity =
					Vector3.zero

			end

			humanoid.AutoRotate = false

		end)

end

local function DisableFly()

	Fly = false

	SetButton(FlyButton,"✈ FLY",false)

	if FlyConnection then

		FlyConnection:Disconnect()
		FlyConnection = nil

	end

	local char = LP.Character

	if char then

		local humanoid =
			char:FindFirstChildOfClass("Humanoid")

		local root =
			char:FindFirstChild("HumanoidRootPart")

		if humanoid then
			humanoid.AutoRotate = true
		end

		if root then
			root.AssemblyLinearVelocity =
				Vector3.zero
		end

	end

end

FlyButton.MouseButton1Click:Connect(function()

	if Fly then
		DisableFly()
	else
		EnableFly()
	end

end)

--========================================================
-- INVISIBILIDADE
--========================================================

local function ApplyInvisible()

	local char = LP.Character

	if not char then
		return
	end

	for _,obj in ipairs(char:GetDescendants()) do

		if obj:IsA("BasePart") then

			obj.LocalTransparencyModifier =
				Invis and 1 or 0

		elseif obj:IsA("Decal") then

			obj.Transparency =
				Invis and 1 or 0

		end

	end

end

InvisButton.MouseButton1Click:Connect(function()

	Invis = not Invis

	SetButton(
		InvisButton,
		"👻 INVISÍVEL",
		Invis
	)

	ApplyInvisible()

end)

LP.CharacterAdded:Connect(function(character)

	character:WaitForChild("HumanoidRootPart")

	task.wait(0.2)

	if Invis then
		ApplyInvisible()
	end

end)

--========================================================
-- ESP
--========================================================

local function RemoveESP(player)

	local data = ESPData[player]

	if not data then
		return
	end

	for _,obj in ipairs(data) do

		if obj and obj.Parent then
			obj:Destroy()
		end

	end

	ESPData[player] = nil

end

local function CreateESP(player)

	if player == LP then
		return
	end

	RemoveESP(player)

	local char = player.Character

	if not char then
		return
	end

	local highlight = Instance.new("Highlight")

	highlight.Name = "MurderESP"
	highlight.FillTransparency = 0.65
	highlight.OutlineTransparency = 0

	highlight.Adornee = char
	highlight.Parent = char

	local billboard

	local head = char:FindFirstChild("Head")

	if head then

		billboard =
			Instance.new("BillboardGui")

		billboard.Name = "MurderName"

		billboard.Size =
			UDim2.fromOffset(180,40)

		billboard.StudsOffset =
			Vector3.new(0,3,0)

		billboard.AlwaysOnTop = true
		billboard.Adornee = head
		billboard.Parent = char

		local label =
			Instance.new("TextLabel")

		label.Size =
			UDim2.fromScale(1,1)

		label.BackgroundTransparency = 1

		label.Text =
			player.DisplayName ..
			"\n@" ..
			player.Name

		label.TextColor3 =
			Color3.fromRGB(255,255,255)

		label.TextStrokeTransparency = 0

		label.TextSize = 13
		label.Font = Enum.Font.GothamBold

		label.Parent = billboard

	end

	ESPData[player] = {
		highlight,
		billboard
	}

end

local function UpdateESP()

	if not ESP then

		for player in pairs(ESPData) do
			RemoveESP(player)
		end

		return

	end

	for _,player in ipairs(Players:GetPlayers()) do

		if player ~= LP then
			CreateESP(player)
		end

	end

end

ESPButton.MouseButton1Click:Connect(function()

	ESP = not ESP

	SetButton(
		ESPButton,
		"👁 ESP",
		ESP
	)

	UpdateESP()

end)

--========================================================
-- ROLES LOCAIS
--========================================================

local function ShowRole()

	local name = SelectedPlayer
		and SelectedPlayer.DisplayName
		or LP.DisplayName

	ResultText.Text =
		"🎭 ROLE\n\n" ..
		"👤 " .. name ..
		"\n\n" ..
		"ROLE ESCOLHIDA:\n" ..
		string.upper(LocalRole) ..
		"\n\n" ..
		"⚠ Escolha local."

end

RandomRole.MouseButton1Click:Connect(function()

	if math.random(1,2) == 1 then
		LocalRole = "Assassino"
	else
		LocalRole = "Xerife"
	end

	ShowRole()

end)

AssassinRole.MouseButton1Click:Connect(function()

	LocalRole = "Assassino"

	ShowRole()

end)

SheriffRole.MouseButton1Click:Connect(function()

	LocalRole = "Xerife"

	ShowRole()

end)

InnocentRole.MouseButton1Click:Connect(function()

	LocalRole = "Inocente"

	ShowRole()

end)

RefreshItems.MouseButton1Click:Connect(function()
	InvestigatePlayer()
end)

--========================================================
-- PESQUISA
--========================================================

SearchPlayers:GetPropertyChangedSignal("Text"):Connect(
	UpdatePlayerList
)

SearchInvestigate:GetPropertyChangedSignal("Text"):Connect(
	UpdateInvestigateList
)

--========================================================
-- ABAS
--========================================================

local function ShowTab(tab)

	Functions.Visible =
		tab == "FUNCTIONS"

	PlayersFrame.Visible =
		tab == "PLAYERS"

	Investigate.Visible =
		tab == "INVESTIGATE"

end

FunctionsTab.MouseButton1Click:Connect(function()
	ShowTab("FUNCTIONS")
end)

PlayersTab.MouseButton1Click:Connect(function()
	ShowTab("PLAYERS")
end)

InvestigateTab.MouseButton1Click:Connect(function()
	ShowTab("INVESTIGATE")
end)

--========================================================
-- PLAYERS
--========================================================

Players.PlayerAdded:Connect(function(player)

	player.CharacterAdded:Connect(function()

		task.wait(1)

		if ESP then
			CreateESP(player)
		end

	end)

	task.wait(0.2)

	UpdatePlayerList()
	UpdateInvestigateList()

end)

Players.PlayerRemoving:Connect(function(player)

	RemoveESP(player)

	if SelectedPlayer == player then

		SelectedPlayer = nil

		ResultText.Text =
			"🔍\n\nO jogador saiu."

	end

	task.wait()

	UpdatePlayerList()
	UpdateInvestigateList()

end)

--========================================================
-- ABRIR / FECHAR
--========================================================

Close.MouseButton1Click:Connect(function()

	Main.Visible = false
	OpenButton.Visible = true

end)

OpenButton.MouseButton1Click:Connect(function()

	Main.Visible = true
	OpenButton.Visible = false

end)

--========================================================
-- INICIALIZAÇÃO
--========================================================

ShowTab("FUNCTIONS")

UpdatePlayerList()
UpdateInvestigateList()

SetButton(FlyButton,"✈ FLY",false)
SetButton(InvisButton,"👻 INVISÍVEL",false)
SetButton(ESPButton,"👁 ESP",false)
