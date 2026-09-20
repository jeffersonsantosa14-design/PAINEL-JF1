--========================================================
-- 🔪 MURDER PANEL - COMPLETO
-- UM ÚNICO LOCAL SCRIPT
--========================================================

local Players = game:GetService("Players")
local UserInputService = game:GetService("UserInputService")
local RunService = game:GetService("RunService")

local LocalPlayer = Players.LocalPlayer

--========================================================
-- CONFIGURAÇÕES
--========================================================

local FlyAtivo = false
local Invisivel = false
local ESPAtivo = false
local FlySpeed = 70

local JogadorSelecionado = nil
local RoleLocal = "Inocente"

local ESPs = {}

--========================================================
-- GUI
--========================================================

local Gui = Instance.new("ScreenGui")
Gui.Name = "MurderPanel"
Gui.ResetOnSpawn = false
Gui.IgnoreGuiInset = true
Gui.Parent = LocalPlayer:WaitForChild("PlayerGui")

--========================================================
-- PAINEL PRINCIPAL
--========================================================

local Main = Instance.new("Frame")
Main.Name = "Main"
Main.Size = UDim2.new(0, 680, 0, 530)
Main.Position = UDim2.new(0.5, -340, 0.5, -265)
Main.BackgroundColor3 = Color3.fromRGB(25, 25, 25)
Main.BorderSizePixel = 0
Main.Parent = Gui

local MainCorner = Instance.new("UICorner")
MainCorner.CornerRadius = UDim.new(0, 10)
MainCorner.Parent = Main

--========================================================
-- TOPO
--========================================================

local Top = Instance.new("Frame")
Top.Name = "Top"
Top.Size = UDim2.new(1, 0, 0, 48)
Top.BackgroundColor3 = Color3.fromRGB(35, 35, 35)
Top.BorderSizePixel = 0
Top.Parent = Main

local TopCorner = Instance.new("UICorner")
TopCorner.CornerRadius = UDim.new(0, 10)
TopCorner.Parent = Top

local Title = Instance.new("TextLabel")
Title.Size = UDim2.new(1, -60, 1, 0)
Title.Position = UDim2.new(0, 15, 0, 0)
Title.BackgroundTransparency = 1
Title.Text = "🔪 MURDER PANEL"
Title.TextColor3 = Color3.fromRGB(255, 255, 255)
Title.TextSize = 20
Title.Font = Enum.Font.GothamBold
Title.TextXAlignment = Enum.TextXAlignment.Left
Title.Parent = Top

local Close = Instance.new("TextButton")
Close.Size = UDim2.new(0, 42, 0, 34)
Close.Position = UDim2.new(1, -48, 0, 7)
Close.BackgroundColor3 = Color3.fromRGB(170, 40, 40)
Close.Text = "X"
Close.TextColor3 = Color3.fromRGB(255, 255, 255)
Close.TextSize = 18
Close.Font = Enum.Font.GothamBold
Close.BorderSizePixel = 0
Close.Parent = Top

local CloseCorner = Instance.new("UICorner")
CloseCorner.CornerRadius = UDim.new(0, 7)
CloseCorner.Parent = Close

--========================================================
-- BOTÃO PARA ABRIR
--========================================================

local Abrir = Instance.new("TextButton")
Abrir.Size = UDim2.new(0, 100, 0, 38)
Abrir.Position = UDim2.new(0, 15, 0.5, -19)
Abrir.BackgroundColor3 = Color3.fromRGB(35, 35, 35)
Abrir.Text = "🔪 ABRIR"
Abrir.TextColor3 = Color3.fromRGB(255, 255, 255)
Abrir.TextSize = 14
Abrir.Font = Enum.Font.GothamBold
Abrir.Visible = false
Abrir.BorderSizePixel = 0
Abrir.Parent = Gui

local AbrirCorner = Instance.new("UICorner")
AbrirCorner.CornerRadius = UDim.new(0, 8)
AbrirCorner.Parent = Abrir

--========================================================
-- ÁREA DAS ABAS
--========================================================

local Tabs = Instance.new("Frame")
Tabs.Size = UDim2.new(1, -20, 0, 42)
Tabs.Position = UDim2.new(0, 10, 0, 58)
Tabs.BackgroundTransparency = 1
Tabs.Parent = Main

local TabFuncoes = Instance.new("TextButton")
TabFuncoes.Size = UDim2.new(0, 140, 0, 38)
TabFuncoes.Position = UDim2.new(0, 0, 0, 0)
TabFuncoes.BackgroundColor3 = Color3.fromRGB(50, 50, 50)
TabFuncoes.Text = "⚙ FUNÇÕES"
TabFuncoes.TextColor3 = Color3.fromRGB(255, 255, 255)
TabFuncoes.TextSize = 14
TabFuncoes.Font = Enum.Font.GothamBold
TabFuncoes.BorderSizePixel = 0
TabFuncoes.Parent = Tabs

local TabTP = Instance.new("TextButton")
TabTP.Size = UDim2.new(0, 140, 0, 38)
TabTP.Position = UDim2.new(0, 150, 0, 0)
TabTP.BackgroundColor3 = Color3.fromRGB(40, 40, 40)
TabTP.Text = "👤 TP PLAYER"
TabTP.TextColor3 = Color3.fromRGB(255, 255, 255)
TabTP.TextSize = 14
TabTP.Font = Enum.Font.GothamBold
TabTP.BorderSizePixel = 0
TabTP.Parent = Tabs

local TabInvestigar = Instance.new("TextButton")
TabInvestigar.Size = UDim2.new(0, 150, 0, 38)
TabInvestigar.Position = UDim2.new(0, 300, 0, 0)
TabInvestigar.BackgroundColor3 = Color3.fromRGB(40, 40, 40)
TabInvestigar.Text = "🔍 INVESTIGAR"
TabInvestigar.TextColor3 = Color3.fromRGB(255, 255, 255)
TabInvestigar.TextSize = 14
TabInvestigar.Font = Enum.Font.GothamBold
TabInvestigar.BorderSizePixel = 0
TabInvestigar.Parent = Tabs

local TabKill = Instance.new("TextButton")
TabKill.Size = UDim2.new(0, 100, 0, 38)
TabKill.Position = UDim2.new(0, 460, 0, 0)
TabKill.BackgroundColor3 = Color3.fromRGB(40, 40, 40)
TabKill.Text = "☠ KILL"
TabKill.TextColor3 = Color3.fromRGB(255, 255, 255)
TabKill.TextSize = 14
TabKill.Font = Enum.Font.GothamBold
TabKill.BorderSizePixel = 0
TabKill.Parent = Tabs

--========================================================
-- FUNÇÕES
--========================================================

local Funcoes = Instance.new("Frame")
Funcoes.Size = UDim2.new(1, -20, 1, -115)
Funcoes.Position = UDim2.new(0, 10, 0, 105)
Funcoes.BackgroundTransparency = 1
Funcoes.Parent = Main

local function CriarBotao(parent, nome, posicao, cor)
	local Botao = Instance.new("TextButton")
	Botao.Size = UDim2.new(0, 200, 0, 45)
	Botao.Position = posicao
	Botao.BackgroundColor3 = cor or Color3.fromRGB(45, 45, 45)
	Botao.Text = nome
	Botao.TextColor3 = Color3.fromRGB(255, 255, 255)
	Botao.TextSize = 15
	Botao.Font = Enum.Font.GothamBold
	Botao.BorderSizePixel = 0
	Botao.Parent = parent

	local Corner = Instance.new("UICorner")
	Corner.CornerRadius = UDim.new(0, 7)
	Corner.Parent = Botao

	return Botao
end

local FlyBtn = CriarBotao(
	Funcoes,
	"✈ FLY: DESATIVADO",
	UDim2.new(0, 10, 0, 15)
)

local InvisBtn = CriarBotao(
	Funcoes,
	"👻 INVISÍVEL: DESATIVADO",
	UDim2.new(0, 220, 0, 15)
)

local ESPBtn = CriarBotao(
	Funcoes,
	"👁 ESP: DESATIVADO",
	UDim2.new(0, 430, 0, 15)
)

local RoleLabel = Instance.new("TextLabel")
RoleLabel.Size = UDim2.new(0, 300, 0, 40)
RoleLabel.Position = UDim2.new(0, 10, 0, 80)
RoleLabel.BackgroundColor3 = Color3.fromRGB(40, 40, 40)
RoleLabel.Text = "🎭 CARGO: INOCENTE"
RoleLabel.TextColor3 = Color3.fromRGB(255, 255, 255)
RoleLabel.TextSize = 15
RoleLabel.Font = Enum.Font.GothamBold
RoleLabel.BorderSizePixel = 0
RoleLabel.Parent = Funcoes

local RoleCorner = Instance.new("UICorner")
RoleCorner.CornerRadius = UDim.new(0, 7)
RoleCorner.Parent = RoleLabel

--========================================================
-- TP PLAYER
--========================================================

local TPFrame = Instance.new("Frame")
TPFrame.Size = UDim2.new(1, -20, 1, -115)
TPFrame.Position = UDim2.new(0, 10, 0, 105)
TPFrame.BackgroundTransparency = 1
TPFrame.Visible = false
TPFrame.Parent = Main

local PesquisaTP = Instance.new("TextBox")
PesquisaTP.Size = UDim2.new(0, 640, 0, 38)
PesquisaTP.Position = UDim2.new(0, 10, 0, 5)
PesquisaTP.BackgroundColor3 = Color3.fromRGB(40, 40, 40)
PesquisaTP.PlaceholderText = "🔎 Pesquisar jogador..."
PesquisaTP.Text = ""
PesquisaTP.TextColor3 = Color3.fromRGB(255, 255, 255)
PesquisaTP.PlaceholderColor3 = Color3.fromRGB(150, 150, 150)
PesquisaTP.TextSize = 14
PesquisaTP.Font = Enum.Font.Gotham
PesquisaTP.BorderSizePixel = 0
PesquisaTP.Parent = TPFrame

local PesquisaCorner = Instance.new("UICorner")
PesquisaCorner.CornerRadius = UDim.new(0, 7)
PesquisaCorner.Parent = PesquisaTP

local ListaTP = Instance.new("ScrollingFrame")
ListaTP.Size = UDim2.new(0, 640, 0, 245)
ListaTP.Position = UDim2.new(0, 10, 0, 50)
ListaTP.BackgroundColor3 = Color3.fromRGB(30, 30, 30)
ListaTP.BorderSizePixel = 0
ListaTP.ScrollBarThickness = 5
ListaTP.CanvasSize = UDim2.new(0, 0, 0, 0)
ListaTP.Parent = TPFrame

local LayoutTP = Instance.new("UIListLayout")
LayoutTP.Padding = UDim.new(0, 5)
LayoutTP.Parent = ListaTP

local TPBtn = CriarBotao(
	TPFrame,
	"🚀 TELEPORTAR",
	UDim2.new(0, 10, 0, 305)
)

local PullBtn = CriarBotao(
	TPFrame,
	"🧲 PUXAR PLAYER",
	UDim2.new(0, 220, 0, 305)
)

local TPStatus = Instance.new("TextLabel")
TPStatus.Size = UDim2.new(0, 400, 0, 35)
TPStatus.Position = UDim2.new(0, 10, 0, 360)
TPStatus.BackgroundTransparency = 1
TPStatus.Text = "Nenhum jogador selecionado."
TPStatus.TextColor3 = Color3.fromRGB(180, 180, 180)
TPStatus.TextSize = 14
TPStatus.Font = Enum.Font.Gotham
TPStatus.TextXAlignment = Enum.TextXAlignment.Left
TPStatus.Parent = TPFrame

--========================================================
-- INVESTIGAR
--========================================================

local Investigar = Instance.new("Frame")
Investigar.Size = UDim2.new(1, -20, 1, -115)
Investigar.Position = UDim2.new(0, 10, 0, 105)
Investigar.BackgroundTransparency = 1
Investigar.Visible = false
Investigar.Parent = Main

local PesquisaInv = Instance.new("TextBox")
PesquisaInv.Size = UDim2.new(0, 640, 0, 38)
PesquisaInv.Position = UDim2.new(0, 10, 0, 5)
PesquisaInv.BackgroundColor3 = Color3.fromRGB(40, 40, 40)
PesquisaInv.PlaceholderText = "🔎 Pesquisar jogador..."
PesquisaInv.Text = ""
PesquisaInv.TextColor3 = Color3.fromRGB(255, 255, 255)
PesquisaInv.PlaceholderColor3 = Color3.fromRGB(150, 150, 150)
PesquisaInv.TextSize = 14
PesquisaInv.Font = Enum.Font.Gotham
PesquisaInv.BorderSizePixel = 0
PesquisaInv.Parent = Investigar

local InvCorner = Instance.new("UICorner")
InvCorner.CornerRadius = UDim.new(0, 7)
InvCorner.Parent = PesquisaInv

local ListaInv = Instance.new("ScrollingFrame")
ListaInv.Size = UDim2.new(0, 640, 0, 190)
ListaInv.Position = UDim2.new(0, 10, 0, 50)
ListaInv.BackgroundColor3 = Color3.fromRGB(30, 30, 30)
ListaInv.BorderSizePixel = 0
ListaInv.ScrollBarThickness = 5
ListaInv.CanvasSize = UDim2.new(0, 0, 0, 0)
ListaInv.Parent = Investigar

local LayoutInv = Instance.new("UIListLayout")
LayoutInv.Padding = UDim.new(0, 5)
LayoutInv.Parent = ListaInv

local Resultado = Instance.new("Frame")
Resultado.Size = UDim2.new(0, 640, 0, 135)
Resultado.Position = UDim2.new(0, 10, 0, 250)
Resultado.BackgroundColor3 = Color3.fromRGB(35, 35, 35)
Resultado.BorderSizePixel = 0
Resultado.Parent = Investigar

local ResultadoCorner = Instance.new("UICorner")
ResultadoCorner.CornerRadius = UDim.new(0, 8)
ResultadoCorner.Parent = Resultado

local ResultadoTexto = Instance.new("TextLabel")
ResultadoTexto.Size = UDim2.new(1, -20, 1, -20)
ResultadoTexto.Position = UDim2.new(0, 10, 0, 10)
ResultadoTexto.BackgroundTransparency = 1
ResultadoTexto.Text = "Selecione um jogador para investigar."
ResultadoTexto.TextColor3 = Color3.fromRGB(255, 255, 255)
ResultadoTexto.TextSize = 14
ResultadoTexto.Font = Enum.Font.Gotham
ResultadoTexto.TextWrapped = true
ResultadoTexto.TextXAlignment = Enum.TextXAlignment.Left
ResultadoTexto.TextYAlignment = Enum.TextYAlignment.Top
ResultadoTexto.Parent = Resultado

local AtualizarItens = CriarBotao(
	Investigar,
	"🔍 INVESTIGAR ITENS",
	UDim2.new(0, 10, 0, 395)
)

--========================================================
-- BOTÕES DE CARGO
--========================================================

local Sortear = CriarBotao(
	Investigar,
	"🎲 SORTEAR CARGO",
	UDim2.new(0, 225, 0, 395)
)

local Assassino = CriarBotao(
	Investigar,
	"🔪 ASSASSINO",
	UDim2.new(0, 440, 0, 395)
)

local Sheriff = CriarBotao(
	Investigar,
	"🔫 XERIFE",
	UDim2.new(0, 225, 0, 445)
)

local Inocente = CriarBotao(
	Investigar,
	"🙂 INOCENTE",
	UDim2.new(0, 440, 0, 445)
)

--========================================================
-- KILL
--========================================================

local KillFrame = Instance.new("Frame")
KillFrame.Size = UDim2.new(1, -20, 1, -115)
KillFrame.Position = UDim2.new(0, 10, 0, 105)
KillFrame.BackgroundTransparency = 1
KillFrame.Visible = false
KillFrame.Parent = Main

local PesquisaKill = Instance.new("TextBox")
PesquisaKill.Size = UDim2.new(0, 640, 0, 40)
PesquisaKill.Position = UDim2.new(0, 10, 0, 5)
PesquisaKill.BackgroundColor3 = Color3.fromRGB(40, 40, 40)
PesquisaKill.PlaceholderText = "🔎 Pesquisar jogador para matar..."
PesquisaKill.Text = ""
PesquisaKill.TextColor3 = Color3.fromRGB(255, 255, 255)
PesquisaKill.PlaceholderColor3 = Color3.fromRGB(150, 150, 150)
PesquisaKill.TextSize = 14
PesquisaKill.Font = Enum.Font.Gotham
PesquisaKill.BorderSizePixel = 0
PesquisaKill.Parent = KillFrame

local KillPesquisaCorner = Instance.new("UICorner")
KillPesquisaCorner.CornerRadius = UDim.new(0, 7)
KillPesquisaCorner.Parent = PesquisaKill

local ListaKill = Instance.new("ScrollingFrame")
ListaKill.Size = UDim2.new(0, 640, 0, 260)
ListaKill.Position = UDim2.new(0, 10, 0, 55)
ListaKill.BackgroundColor3 = Color3.fromRGB(30, 30, 30)
ListaKill.BorderSizePixel = 0
ListaKill.ScrollBarThickness = 5
ListaKill.CanvasSize = UDim2.new(0, 0, 0, 0)
ListaKill.Parent = KillFrame

local LayoutKill = Instance.new("UIListLayout")
LayoutKill.Padding = UDim.new(0, 5)
LayoutKill.Parent = ListaKill

local KillBtn = CriarBotao(
	KillFrame,
	"☠ DAR KILL NO PLAYER",
	UDim2.new(0, 10, 0, 330),
	Color3.fromRGB(150, 35, 35)
)

local KillStatus = Instance.new("TextLabel")
KillStatus.Size = UDim2.new(0, 640, 0, 50)
KillStatus.Position = UDim2.new(0, 10, 0, 385)
KillStatus.BackgroundTransparency = 1
KillStatus.Text = "Nenhum jogador selecionado."
KillStatus.TextColor3 = Color3.fromRGB(180, 180, 180)
KillStatus.TextSize = 14
KillStatus.Font = Enum.Font.Gotham
KillStatus.TextXAlignment = Enum.TextXAlignment.Left
KillStatus.TextWrapped = true
KillStatus.Parent = KillFrame

--========================================================
-- ABA
--========================================================

local function Aba(nome)

	Funcoes.Visible = nome == "FUNCOES"
	TPFrame.Visible = nome == "TP"
	Investigar.Visible = nome == "INVESTIGAR"
	KillFrame.Visible = nome == "KILL"

	TabFuncoes.BackgroundColor3 =
		nome == "FUNCOES"
		and Color3.fromRGB(60, 60, 60)
		or Color3.fromRGB(40, 40, 40)

	TabTP.BackgroundColor3 =
		nome == "TP"
		and Color3.fromRGB(60, 60, 60)
		or Color3.fromRGB(40, 40, 40)

	TabInvestigar.BackgroundColor3 =
		nome == "INVESTIGAR"
		and Color3.fromRGB(60, 60, 60)
		or Color3.fromRGB(40, 40, 40)

	TabKill.BackgroundColor3 =
		nome == "KILL"
		and Color3.fromRGB(90, 35, 35)
		or Color3.fromRGB(40, 40, 40)
end

TabFuncoes.MouseButton1Click:Connect(function()
	Aba("FUNCOES")
end)

TabTP.MouseButton1Click:Connect(function()
	Aba("TP")
end)

TabInvestigar.MouseButton1Click:Connect(function()
	Aba("INVESTIGAR")
end)

TabKill.MouseButton1Click:Connect(function()
	Aba("KILL")
end)

--========================================================
-- DRAG DO PAINEL
--========================================================

local Arrastando = false
local InicioMouse
local InicioPos

Top.InputBegan:Connect(function(input)

	if input.UserInputType == Enum.UserInputType.MouseButton1
		or input.UserInputType == Enum.UserInputType.Touch then

		Arrastando = true
		InicioMouse = input.Position
		InicioPos = Main.Position

	end

end)

Top.InputEnded:Connect(function(input)

	if input.UserInputType == Enum.UserInputType.MouseButton1
		or input.UserInputType == Enum.UserInputType.Touch then

		Arrastando = false

	end

end)

UserInputService.InputChanged:Connect(function(input)

	if not Arrastando then
		return
	end

	if input.UserInputType ~= Enum.UserInputType.MouseMovement
		and input.UserInputType ~= Enum.UserInputType.Touch then
		return
	end

	local Delta = input.Position - InicioMouse

	Main.Position = UDim2.new(
		InicioPos.X.Scale,
		InicioPos.X.Offset + Delta.X,
		InicioPos.Y.Scale,
		InicioPos.Y.Offset + Delta.Y
	)

end)

--========================================================
-- FECHAR / ABRIR
--========================================================

Close.MouseButton1Click:Connect(function()
	Main.Visible = false
	Abrir.Visible = true
end)

Abrir.MouseButton1Click:Connect(function()
	Main.Visible = true
	Abrir.Visible = false
end)

--========================================================
-- FLY
--========================================================

local FlyConnection

FlyBtn.MouseButton1Click:Connect(function()

	FlyAtivo = not FlyAtivo

	if FlyAtivo then

		FlyBtn.Text = "✈ FLY: ATIVADO"
		FlyBtn.BackgroundColor3 = Color3.fromRGB(35, 130, 70)

		FlyConnection = RunService.RenderStepped:Connect(function()

			local Character = LocalPlayer.Character

			if not Character then
				return
			end

			local Humanoid = Character:FindFirstChildOfClass("Humanoid")
			local Root = Character:FindFirstChild("HumanoidRootPart")

			if not Humanoid or not Root then
				return
			end

			local Camera = workspace.CurrentCamera

			local Direcao = Vector3.zero

			if UserInputService:IsKeyDown(Enum.KeyCode.W) then
				Direcao += Camera.CFrame.LookVector
			end

			if UserInputService:IsKeyDown(Enum.KeyCode.S) then
				Direcao -= Camera.CFrame.LookVector
			end

			if UserInputService:IsKeyDown(Enum.KeyCode.D) then
				Direcao += Camera.CFrame.RightVector
			end

			if UserInputService:IsKeyDown(Enum.KeyCode.A) then
				Direcao -= Camera.CFrame.RightVector
			end

			if UserInputService:IsKeyDown(Enum.KeyCode.Space) then
				Direcao += Vector3.new(0, 1, 0)
			end

			if UserInputService:IsKeyDown(Enum.KeyCode.LeftControl) then
				Direcao -= Vector3.new(0, 1, 0)
			end

			if Direcao.Magnitude > 0 then
				Direcao = Direcao.Unit
			end

			Root.AssemblyLinearVelocity = Direcao * FlySpeed
			Humanoid.AutoRotate = false

		end)

	else

		FlyBtn.Text = "✈ FLY: DESATIVADO"
		FlyBtn.BackgroundColor3 = Color3.fromRGB(45, 45, 45)

		if FlyConnection then
			FlyConnection:Disconnect()
			FlyConnection = nil
		end

		local Character = LocalPlayer.Character

		if Character then

			local Humanoid = Character:FindFirstChildOfClass("Humanoid")
			local Root = Character:FindFirstChild("HumanoidRootPart")

			if Humanoid then
				Humanoid.AutoRotate = true
			end

			if Root then
				Root.AssemblyLinearVelocity = Vector3.zero
			end

		end

	end

end)

--========================================================
-- INVISÍVEL
--========================================================

local function AplicarInvisibilidade()

	local Character = LocalPlayer.Character

	if not Character then
		return
	end

	for _, Obj in ipairs(Character:GetDescendants()) do

		if Obj:IsA("BasePart") then

			Obj.LocalTransparencyModifier = Invisivel and 1 or 0

		elseif Obj:IsA("Decal") then

			Obj.Transparency = Invisivel and 1 or 0

		end

	end

end

InvisBtn.MouseButton1Click:Connect(function()

	Invisivel = not Invisivel

	if Invisivel then

		InvisBtn.Text = "👻 INVISÍVEL: ATIVADO"
		InvisBtn.BackgroundColor3 = Color3.fromRGB(35, 130, 70)

	else

		InvisBtn.Text = "👻 INVISÍVEL: DESATIVADO"
		InvisBtn.BackgroundColor3 = Color3.fromRGB(45, 45, 45)

	end

	AplicarInvisibilidade()

end)

--========================================================
-- ESP
--========================================================

local function RemoverESP(Player)

	local Dados = ESPs[Player]

	if Dados then

		for _, Obj in ipairs(Dados) do

			if Obj and Obj.Parent then
				Obj:Destroy()
			end

		end

		ESPs[Player] = nil

	end

end

local function CriarESP(Player)

	if Player == LocalPlayer then
		return
	end

	RemoverESP(Player)

	local Character = Player.Character

	if not Character then
		return
	end

	local Highlight = Instance.new("Highlight")
	Highlight.Name = "MurderESP"
	Highlight.FillTransparency = 0.65
	Highlight.OutlineTransparency = 0
	Highlight.Adornee = Character
	Highlight.Parent = Character

	local Billboard = Instance.new("BillboardGui")
	Billboard.Name = "MurderESPName"
	Billboard.Size = UDim2.new(0, 220, 0, 50)
	Billboard.StudsOffset = Vector3.new(0, 3, 0)
	Billboard.AlwaysOnTop = true
	Billboard.Adornee = Character:FindFirstChild("Head")
	Billboard.Parent = Character

	local Text = Instance.new("TextLabel")
	Text.Size = UDim2.new(1, 0, 1, 0)
	Text.BackgroundTransparency = 1
	Text.Text = Player.DisplayName .. "  @" .. Player.Name
	Text.TextColor3 = Color3.fromRGB(255, 255, 255)
	Text.TextStrokeTransparency = 0
	Text.TextSize = 14
	Text.Font = Enum.Font.GothamBold
	Text.Parent = Billboard

	ESPs[Player] = {
		Highlight,
		Billboard
	}

end

ESPBtn.MouseButton1Click:Connect(function()

	ESPAtivo = not ESPAtivo

	if ESPAtivo then

		ESPBtn.Text = "👁 ESP: ATIVADO"
		ESPBtn.BackgroundColor3 = Color3.fromRGB(35, 130, 70)

		for _, Player in ipairs(Players:GetPlayers()) do

			if Player ~= LocalPlayer then
				CriarESP(Player)
			end

		end

	else

		ESPBtn.Text = "👁 ESP: DESATIVADO"
		ESPBtn.BackgroundColor3 = Color3.fromRGB(45, 45, 45)

		for Player in pairs(ESPs) do
			RemoverESP(Player)
		end

	end

end)

--========================================================
-- FUNÇÃO PARA COMPARAR PESQUISA
--========================================================

local function Combina(Player, Pesquisa)

	Pesquisa = string.lower(Pesquisa or "")

	if Pesquisa == "" then
		return true
	end

	local Nome = string.lower(Player.Name)
	local Display = string.lower(Player.DisplayName)

	return string.find(Nome, Pesquisa, 1, true) ~= nil
		or string.find(Display, Pesquisa, 1, true) ~= nil

end

--========================================================
-- LIMPAR LISTAS
--========================================================

local function LimparLista(Frame)

	for _, Obj in ipairs(Frame:GetChildren()) do

		if Obj:IsA("TextButton") then
			Obj:Destroy()
		end

	end

end

--========================================================
-- SELECIONAR PLAYER
--========================================================

local function SelecionarPlayer(Player, StatusLabel)

	JogadorSelecionado = Player

	if StatusLabel then

		StatusLabel.Text =
			"Selecionado: "
			.. Player.DisplayName
			.. "  @"
			.. Player.Name

	end

end

--========================================================
-- CRIAR BOTÃO DE PLAYER
--========================================================

local function CriarPlayerButton(Player, Parent, StatusLabel)

	local Botao = Instance.new("TextButton")

	Botao.Size = UDim2.new(1, -10, 0, 38)
	Botao.BackgroundColor3 = Color3.fromRGB(45, 45, 45)

	Botao.Text =
		Player.DisplayName
		.. "  @"
		.. Player.Name

	Botao.TextColor3 = Color3.fromRGB(255, 255, 255)
	Botao.TextSize = 14
	Botao.Font = Enum.Font.GothamBold
	Botao.BorderSizePixel = 0
	Botao.Parent = Parent

	local Corner = Instance.new("UICorner")
	Corner.CornerRadius = UDim.new(0, 6)
	Corner.Parent = Botao

	Botao.MouseButton1Click:Connect(function()

		SelecionarPlayer(Player, StatusLabel)

		Botao.BackgroundColor3 = Color3.fromRGB(65, 100, 65)

	end)

	return Botao

end

--========================================================
-- ATUALIZAR TP
--========================================================

local function AtualizarTPLista()

	LimparLista(ListaTP)

	for _, Player in ipairs(Players:GetPlayers()) do

		if Player ~= LocalPlayer and Combina(Player, PesquisaTP.Text) then

			CriarPlayerButton(
				Player,
				ListaTP,
				TPStatus
			)

		end

	end

	task.wait()

	ListaTP.CanvasSize = UDim2.new(
		0,
		0,
		0,
		LayoutTP.AbsoluteContentSize.Y + 10
	)

end

PesquisaTP:GetPropertyChangedSignal("Text"):Connect(function()
	AtualizarTPLista()
end)

--========================================================
-- TP
--========================================================

TPBtn.MouseButton1Click:Connect(function()

	local Target = JogadorSelecionado

	if not Target then

		TPStatus.Text = "❌ Selecione um jogador."

		return

	end

	local MeuCharacter = LocalPlayer.Character
	local AlvoCharacter = Target.Character

	if not MeuCharacter or not AlvoCharacter then

		TPStatus.Text = "❌ Personagem não encontrado."

		return

	end

	local MeuRoot = MeuCharacter:FindFirstChild("HumanoidRootPart")
	local AlvoRoot = AlvoCharacter:FindFirstChild("HumanoidRootPart")

	if not MeuRoot or not AlvoRoot then

		TPStatus.Text = "❌ HumanoidRootPart não encontrado."

		return

	end

	MeuRoot.CFrame =
		AlvoRoot.CFrame * CFrame.new(3, 0, 0)

	TPStatus.Text =
		"✅ Teleportado para "
		.. Target.DisplayName

end)

--========================================================
-- PUXAR
--========================================================

PullBtn.MouseButton1Click:Connect(function()

	local Target = JogadorSelecionado

	if not Target then

		TPStatus.Text = "❌ Selecione um jogador."

		return

	end

	local MeuCharacter = LocalPlayer.Character
	local AlvoCharacter = Target.Character

	if not MeuCharacter or not AlvoCharacter then
		return
	end

	local MeuRoot = MeuCharacter:FindFirstChild("HumanoidRootPart")
	local AlvoRoot = AlvoCharacter:FindFirstChild("HumanoidRootPart")

	if not MeuRoot or not AlvoRoot then
		return
	end

	AlvoRoot.CFrame =
		MeuRoot.CFrame * CFrame.new(3, 0, 0)

	TPStatus.Text =
		"🧲 Tentativa de puxar "
		.. Target.DisplayName

end)

--========================================================
-- INVESTIGAÇÃO
--========================================================

local function NormalizarNome(Nome)

	Nome = string.lower(Nome)

	Nome = string.gsub(Nome, "%s+", "")
	Nome = string.gsub(Nome, "_", "")
	Nome = string.gsub(Nome, "-", "")

	return Nome

end

local function InvestigarPlayer(Player)

	if not Player then

		ResultadoTexto.Text =
			"❌ Nenhum jogador selecionado."

		return

	end

	local Itens = {}
	local TemAssassino = false
	local TemSheriff = false

	local Backpack = Player:FindFirstChildOfClass("Backpack")

	if Backpack then

		for _, Obj in ipairs(Backpack:GetChildren()) do

			if Obj:IsA("Tool") then
				table.insert(Itens, Obj.Name)
			end

		end

	end

	local Character = Player.Character

	if Character then

		for _, Obj in ipairs(Character:GetChildren()) do

			if Obj:IsA("Tool") then
				table.insert(Itens, Obj.Name)
			end

		end

	end

	for _, Nome in ipairs(Itens) do

		local N = NormalizarNome(Nome)

		if N == "knife"
			or N == "faca"
			or N == "murderknife"
			or N == "assassin"
			or N == "dagger"
			or string.find(N, "knife", 1, true)
			or string.find(N, "faca", 1, true) then

			TemAssassino = true

		end

		if N == "gun"
			or N == "pistol"
			or N == "sheriffgun"
			or N == "sheriff"
			or N == "revolver"
			or N == "arma"
			or string.find(N, "pistol", 1, true)
			or string.find(N, "revolver", 1, true)
			or string.find(N, "sheriff", 1, true) then

			TemSheriff = true

		end

	end

	local Texto = ""

	Texto =
		"👤 JOGADOR: "
		.. Player.DisplayName
		.. "\n\n"

	if #Itens == 0 then

		Texto =
			Texto
			.. "📦 Itens encontrados: nenhum\n"

	else

		Texto =
			Texto
			.. "📦 ITENS:\n"

		for _, Nome in ipairs(Itens) do

			Texto =
				Texto
				.. "• "
				.. Nome
				.. "\n"

		end

	end

	Texto = Texto .. "\n🎭 POSSÍVEL CARGO: "

	if TemAssassino then

		Texto = Texto .. "🔪 ASSASSINO"

	elseif TemSheriff then

		Texto = Texto .. "🔫 XERIFE"

	else

		Texto = Texto .. "❔ NÃO IDENTIFICADO"

	end

	ResultadoTexto.Text = Texto

end

local function AtualizarInvLista()

	LimparLista(ListaInv)

	for _, Player in ipairs(Players:GetPlayers()) do

		if Player ~= LocalPlayer
			and Combina(Player, PesquisaInv.Text) then

			CriarPlayerButton(
				Player,
				ListaInv,
				nil
			)

		end

	end

	task.wait()

	ListaInv.CanvasSize = UDim2.new(
		0,
		0,
		0,
		LayoutInv.AbsoluteContentSize.Y + 10
	)

end

PesquisaInv:GetPropertyChangedSignal("Text"):Connect(function()

	AtualizarInvLista()

end)

AtualizarItens.MouseButton1Click:Connect(function()

	InvestigarPlayer(JogadorSelecionado)

end)

--========================================================
-- CARGOS LOCAIS
--========================================================

local function MostrarRole()

	RoleLabel.Text =
		"🎭 CARGO: "
		.. string.upper(RoleLocal)

end

Sortear.MouseButton1Click:Connect(function()

	local Roles = {
		"Assassino",
		"Xerife",
		"Inocente"
	}

	RoleLocal =
		Roles[math.random(1, #Roles)]

	MostrarRole()

	ResultadoTexto.Text =
		"🎲 Cargo local sorteado: "
		.. RoleLocal

end)

Assassino.MouseButton1Click:Connect(function()

	RoleLocal = "Assassino"

	MostrarRole()

	ResultadoTexto.Text =
		"🔪 Cargo local definido como ASSASSINO."

end)

Sheriff.MouseButton1Click:Connect(function()

	RoleLocal = "Xerife"

	MostrarRole()

	ResultadoTexto.Text =
		"🔫 Cargo local definido como XERIFE."

end)

Inocente.MouseButton1Click:Connect(function()

	RoleLocal = "Inocente"

	MostrarRole()

	ResultadoTexto.Text =
		"🙂 Cargo local definido como INOCENTE."

end)

--========================================================
-- ATUALIZAR KILL
--========================================================

local function AtualizarKillLista()

	LimparLista(ListaKill)

	for _, Player in ipairs(Players:GetPlayers()) do

		if Player ~= LocalPlayer
			and Combina(Player, PesquisaKill.Text) then

			local Botao = CriarPlayerButton(
				Player,
				ListaKill,
				KillStatus
			)

			Botao.MouseButton1Click:Connect(function()

				JogadorSelecionado = Player

				KillStatus.Text =
					"☠ Selecionado para KILL: "
					.. Player.DisplayName
					.. "  @"
					.. Player.Name

			end)

		end

	end

	task.wait()

	ListaKill.CanvasSize = UDim2.new(
		0,
		0,
		0,
		LayoutKill.AbsoluteContentSize.Y + 10
	)

end

PesquisaKill:GetPropertyChangedSignal("Text"):Connect(function()

	AtualizarKillLista()

end)

--========================================================
-- KILL
--========================================================

KillBtn.MouseButton1Click:Connect(function()

	local Target = JogadorSelecionado

	if not Target then

		KillStatus.Text =
			"❌ Selecione um jogador primeiro."

		return

	end

	if Target == LocalPlayer then

		KillStatus.Text =
			"❌ Você não pode selecionar você mesmo."

		return

	end

	local Character = Target.Character

	if not Character then

		KillStatus.Text =
			"❌ Personagem do jogador não encontrado."

		return

	end

	local Humanoid =
		Character:FindFirstChildOfClass("Humanoid")

	if not Humanoid then

		KillStatus.Text =
			"❌ Humanoid não encontrado."

		return

	end

	--====================================================
	-- TENTATIVA LOCAL
	--====================================================

	Humanoid.Health = 0

	KillStatus.Text =
		"☠ Tentativa de KILL enviada para "
		.. Target.DisplayName
		.. ". Se o servidor controlar o dano, será necessário um RemoteEvent."
end)

--========================================================
-- PLAYERS
--========================================================

Players.PlayerAdded:Connect(function(Player)

	Player.CharacterAdded:Connect(function()

		task.wait(1)

		AtualizarTPLista()
		AtualizarInvLista()
		AtualizarKillLista()

		if ESPAtivo then
			CriarESP(Player)
		end

	end)

	AtualizarTPLista()
	AtualizarInvLista()
	AtualizarKillLista()

end)

Players.PlayerRemoving:Connect(function(Player)

	RemoverESP(Player)

	if JogadorSelecionado == Player then

		JogadorSelecionado = nil

		TPStatus.Text =
			"Nenhum jogador selecionado."

		KillStatus.Text =
			"Nenhum jogador selecionado."

	end

	AtualizarTPLista()
	AtualizarInvLista()
	AtualizarKillLista()

end)

--========================================================
-- RESPAWN
--========================================================

LocalPlayer.CharacterAdded:Connect(function()

	task.wait(1)

	if Invisivel then
		AplicarInvisibilidade()
	end

	if FlyAtivo then

		local Character = LocalPlayer.Character
		local Humanoid = Character and Character:FindFirstChildOfClass("Humanoid")

		if Humanoid then
			Humanoid.AutoRotate = false
		end

	end

end)

--========================================================
-- ATUALIZAÇÃO INICIAL
--========================================================

AtualizarTPLista()
AtualizarInvLista()
AtualizarKillLista()
MostrarRole()
Aba("FUNCOES")

--========================================================
-- FIM
--========================================================
