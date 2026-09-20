--========================================================
-- 🔪 MURDER PANEL
-- LOCAL SCRIPT ÚNICO
--
-- FUNÇÕES:
-- • FLY
-- • INVISIBILIDADE LOCAL
-- • ESP
-- • TP PARA PLAYER
-- • PUXAR PLAYER (LOCAL)
-- • PESQUISA DE PLAYER
-- • VER TOOLS DOS PLAYERS
-- • IDENTIFICAR POSSÍVEL ASSASSINO/XERIFE POR ITEM
-- • ESCOLHER ROLE LOCALMENTE
-- • SORTEAR ASSASSINO/XERIFE
--========================================================

local Players = game:GetService("Players")
local UserInputService = game:GetService("UserInputService")
local RunService = game:GetService("RunService")

local LocalPlayer = Players.LocalPlayer

--========================================================
-- CONFIGURAÇÃO
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

local PlayerGui = LocalPlayer:WaitForChild("PlayerGui")

local Antigo = PlayerGui:FindFirstChild("MurderPanel")
if Antigo then
	Antigo:Destroy()
end

local Gui = Instance.new("ScreenGui")
Gui.Name = "MurderPanel"
Gui.ResetOnSpawn = false
Gui.IgnoreGuiInset = true
Gui.Parent = PlayerGui

--========================================================
-- FUNÇÕES DE GUI
--========================================================

local function Corner(obj, raio)
	local c = Instance.new("UICorner")
	c.CornerRadius = UDim.new(0, raio or 7)
	c.Parent = obj
end

local function Botao(parent, texto, tamanho, posicao)
	local b = Instance.new("TextButton")

	b.Size = tamanho
	b.Position = posicao

	b.BackgroundColor3 = Color3.fromRGB(45, 45, 50)
	b.BorderSizePixel = 0

	b.Text = texto
	b.TextColor3 = Color3.fromRGB(255, 255, 255)
	b.TextSize = 13
	b.Font = Enum.Font.GothamBold

	b.AutoButtonColor = true

	b.Parent = parent

	Corner(b, 7)

	return b
end

local function AtualizarBotao(botao, nome, ativo)
	if ativo then
		botao.Text = nome .. ": LIGADO"
		botao.BackgroundColor3 = Color3.fromRGB(40, 135, 65)
	else
		botao.Text = nome .. ": DESLIGADO"
		botao.BackgroundColor3 = Color3.fromRGB(45, 45, 50)
	end
end

--========================================================
-- BOTÃO ABRIR
--========================================================

local Abrir = Botao(
	Gui,
	"☰",
	UDim2.fromOffset(45,45),
	UDim2.new(0,15,0.5,-22)
)

Abrir.Visible = false

--========================================================
-- PAINEL
--========================================================

local Main = Instance.new("Frame")

Main.Size = UDim2.fromOffset(680,530)
Main.Position = UDim2.new(0.5,-340,0.5,-265)

Main.BackgroundColor3 = Color3.fromRGB(27,27,31)
Main.BorderSizePixel = 0

Main.Parent = Gui

Corner(Main,10)

--========================================================
-- TOPO
--========================================================

local Top = Instance.new("Frame")

Top.Size = UDim2.new(1,0,0,48)

Top.BackgroundColor3 = Color3.fromRGB(18,18,21)
Top.BorderSizePixel = 0

Top.Parent = Main

Corner(Top,10)

local Titulo = Instance.new("TextLabel")

Titulo.Size = UDim2.new(1,-70,1,0)
Titulo.Position = UDim2.fromOffset(15,0)

Titulo.BackgroundTransparency = 1

Titulo.Text = "🔪 MURDER PANEL"
Titulo.TextColor3 = Color3.fromRGB(255,255,255)
Titulo.TextSize = 18
Titulo.Font = Enum.Font.GothamBold

Titulo.TextXAlignment = Enum.TextXAlignment.Left

Titulo.Parent = Top

local Fechar = Botao(
	Top,
	"X",
	UDim2.fromOffset(38,35),
	UDim2.new(1,-45,0,6)
)

--========================================================
-- ABAS
--========================================================

local Tabs = Instance.new("Frame")

Tabs.Size = UDim2.new(1,-20,0,42)
Tabs.Position = UDim2.fromOffset(10,58)

Tabs.BackgroundTransparency = 1
Tabs.Parent = Main

local TabFuncoes = Botao(
	Tabs,
	"⚙ FUNÇÕES",
	UDim2.fromOffset(140,40),
	UDim2.fromOffset(0,0)
)

local TabTP = Botao(
	Tabs,
	"👤 TP PLAYER",
	UDim2.fromOffset(140,40),
	UDim2.fromOffset(150,0)
)

local TabInvestigar = Botao(
	Tabs,
	"🔍 INVESTIGAR",
	UDim2.fromOffset(150,40),
	UDim2.fromOffset(300,0)
)

--========================================================
-- CONTEÚDO
--========================================================

local Conteudo = Instance.new("Frame")

Conteudo.Size = UDim2.new(1,-20,1,-115)
Conteudo.Position = UDim2.fromOffset(10,108)

Conteudo.BackgroundTransparency = 1
Conteudo.Parent = Main

--========================================================
-- FUNÇÕES
--========================================================

local Funcoes = Instance.new("Frame")

Funcoes.Size = UDim2.fromScale(1,1)
Funcoes.BackgroundTransparency = 1
Funcoes.Parent = Conteudo

local FlyBtn = Botao(
	Funcoes,
	"✈ FLY: DESLIGADO",
	UDim2.fromOffset(200,45),
	UDim2.fromOffset(10,10)
)

local InvisBtn = Botao(
	Funcoes,
	"👻 INVISÍVEL: DESLIGADO",
	UDim2.fromOffset(200,45),
	UDim2.fromOffset(220,10)
)

local ESPBtn = Botao(
	Funcoes,
	"👁 ESP: DESLIGADO",
	UDim2.fromOffset(200,45),
	UDim2.fromOffset(430,10)
)

local Info = Instance.new("TextLabel")

Info.Size = UDim2.new(1,-20,0,120)
Info.Position = UDim2.fromOffset(10,70)

Info.BackgroundTransparency = 1

Info.Text =
	"✈ FLY\n" ..
	"WASD = mover   |   ESPAÇO = subir   |   CTRL = descer\n\n" ..
	"👻 INVISÍVEL = esconde seu personagem apenas no seu cliente.\n" ..
	"👁 ESP = mostra jogadores através do mapa."

Info.TextColor3 = Color3.fromRGB(190,190,190)
Info.TextSize = 14
Info.Font = Enum.Font.Gotham

Info.TextWrapped = true
Info.TextXAlignment = Enum.TextXAlignment.Left
Info.TextYAlignment = Enum.TextYAlignment.Top

Info.Parent = Funcoes

--========================================================
-- TP PLAYER
--========================================================

local TPFrame = Instance.new("Frame")

TPFrame.Size = UDim2.fromScale(1,1)
TPFrame.BackgroundTransparency = 1
TPFrame.Visible = false

TPFrame.Parent = Conteudo

local PesquisaTP = Instance.new("TextBox")

PesquisaTP.Size = UDim2.new(1,-20,0,40)
PesquisaTP.Position = UDim2.fromOffset(10,5)

PesquisaTP.BackgroundColor3 = Color3.fromRGB(38,38,43)
PesquisaTP.BorderSizePixel = 0

PesquisaTP.PlaceholderText = "🔎 Pesquisar jogador..."
PesquisaTP.PlaceholderColor3 = Color3.fromRGB(145,145,145)

PesquisaTP.Text = ""
PesquisaTP.TextColor3 = Color3.fromRGB(255,255,255)
PesquisaTP.TextSize = 14
PesquisaTP.Font = Enum.Font.Gotham

PesquisaTP.Parent = TPFrame

Corner(PesquisaTP,7)

local ListaTP = Instance.new("ScrollingFrame")

ListaTP.Size = UDim2.new(1,-20,0,245)
ListaTP.Position = UDim2.fromOffset(10,55)

ListaTP.BackgroundColor3 = Color3.fromRGB(21,21,24)
ListaTP.BorderSizePixel = 0

ListaTP.ScrollBarThickness = 5
ListaTP.CanvasSize = UDim2.new()

ListaTP.Parent = TPFrame

Corner(ListaTP,7)

local LayoutTP = Instance.new("UIListLayout")

LayoutTP.Padding = UDim.new(0,4)
LayoutTP.Parent = ListaTP

local TPBtn = Botao(
	TPFrame,
	"📍 IR ATÉ O JOGADOR",
	UDim2.new(0.48,-5,0,45),
	UDim2.new(0,10,1,-55)
)

local PullBtn = Botao(
	TPFrame,
	"🧲 PUXAR LOCALMENTE",
	UDim2.new(0.48,-5,0,45),
	UDim2.new(0.52,0,1,-55)
)

--========================================================
-- INVESTIGAR
--========================================================

local Investigar = Instance.new("Frame")

Investigar.Size = UDim2.fromScale(1,1)
Investigar.BackgroundTransparency = 1
Investigar.Visible = false

Investigar.Parent = Conteudo

local PesquisaInv = Instance.new("TextBox")

PesquisaInv.Size = UDim2.new(0.42,-10,0,40)
PesquisaInv.Position = UDim2.fromOffset(10,5)

PesquisaInv.BackgroundColor3 = Color3.fromRGB(38,38,43)
PesquisaInv.BorderSizePixel = 0

PesquisaInv.PlaceholderText = "🔎 Pesquisar jogador..."
PesquisaInv.PlaceholderColor3 = Color3.fromRGB(145,145,145)

PesquisaInv.Text = ""
PesquisaInv.TextColor3 = Color3.fromRGB(255,255,255)
PesquisaInv.TextSize = 14
PesquisaInv.Font = Enum.Font.Gotham

PesquisaInv.Parent = Investigar

Corner(PesquisaInv,7)

local ListaInv = Instance.new("ScrollingFrame")

ListaInv.Size = UDim2.new(0.42,-10,0,230)
ListaInv.Position = UDim2.fromOffset(10,55)

ListaInv.BackgroundColor3 = Color3.fromRGB(21,21,24)
ListaInv.BorderSizePixel = 0

ListaInv.ScrollBarThickness = 5
ListaInv.CanvasSize = UDim2.new()

ListaInv.Parent = Investigar

Corner(ListaInv,7)

local LayoutInv = Instance.new("UIListLayout")

LayoutInv.Padding = UDim.new(0,4)
LayoutInv.Parent = ListaInv

local Resultado = Instance.new("Frame")

Resultado.Size = UDim2.new(0.58,-10,0,230)
Resultado.Position = UDim2.new(0.42,0,0,55)

Resultado.BackgroundColor3 = Color3.fromRGB(22,22,26)
Resultado.BorderSizePixel = 0

Resultado.Parent = Investigar

Corner(Resultado,7)

local ResultadoTexto = Instance.new("TextLabel")

Resultado.Size = UDim2.new(1,-20,1,-20)
Resultado.Position = UDim2.fromOffset(10,10)

Resultado.BackgroundTransparency = 1

Resultado.Text =
	"🔍 INVESTIGAÇÃO\n\n" ..
	"Selecione um jogador."

Resultado.TextColor3 = Color3.fromRGB(235,235,235)
Resultado.TextSize = 14
Resultado.Font = Enum.Font.Gotham

Resultado.TextWrapped = true
Resultado.TextXAlignment = Enum.TextXAlignment.Left
Resultado.TextYAlignment = Enum.TextYAlignment.Top

Resultado.Parent = Resultado

--========================================================
-- BOTÕES DE ROLE
--========================================================

local AtualizarItens = Botao(
	Investigar,
	"🔄 ATUALIZAR ITENS",
	UDim2.new(0.42,-10,0,40),
	UDim2.fromOffset(10,295)
)

local Sortear = Botao(
	Investigar,
	"🎲 SORTEAR",
	UDim2.new(0.28,-5,0,40),
	UDim2.new(0.42,0,0,295)
)

local Assassino = Botao(
	Investigar,
	"🗡 ASSASSINO",
	UDim2.new(0.28,-5,0,40),
	UDim2.new(0.71,0,0,295)
)

local Sheriff = Botao(
	Investigar,
	"🔫 XERIFE",
	UDim2.new(0.28,-5,0,40),
	UDim2.new(0.42,0,0,345)
)

local Inocente = Botao(
	Investigar,
	"👤 INOCENTE",
	UDim2.new(0.28,-5,0,40),
	UDim2.new(0.71,0,0,345)
)

--========================================================
-- PLAYER SELECIONADO
--========================================================

local function Combina(player, pesquisa)
	if pesquisa == "" then
		return true
	end

	pesquisa = string.lower(pesquisa)

	return string.find(string.lower(player.Name),pesquisa,1,true)
		or string.find(string.lower(player.DisplayName),pesquisa,1,true)
end

local function LimparLista(lista)
	for _,obj in ipairs(lista:GetChildren()) do
		if obj:IsA("TextButton") then
			obj:Destroy()
		end
	end
end

local function SelecionarPlayer(player, lista)

	JogadorSelecionado = player

	for _,obj in ipairs(lista:GetChildren()) do
		if obj:IsA("TextButton") then
			obj.BackgroundColor3 = Color3.fromRGB(45,45,50)
		end
	end

	for _,obj in ipairs(lista:GetChildren()) do
		if obj:IsA("TextButton") then
			if obj:GetAttribute("PlayerName") == player.Name then
				obj.BackgroundColor3 = Color3.fromRGB(45,130,65)
			end
		end
	end

	if lista == ListaInv then
		InvestigarPlayer()
	end
end

function CriarPlayerButton(player,lista)

	local b = Botao(
		lista,
		player.DisplayName .. "  @" .. player.Name,
		UDim2.new(1,-10,0,36),
		UDim2.new()
	)

	b:SetAttribute("PlayerName",player.Name)

	if player == JogadorSelecionado then
		b.BackgroundColor3 = Color3.fromRGB(45,130,65)
	end

	b.MouseButton1Click:Connect(function()
		SelecionarPlayer(player,lista)
	end)
end

local function AtualizarTPLista()

	LimparLista(ListaTP)

	local pesquisa = PesquisaTP.Text

	for _,player in ipairs(Players:GetPlayers()) do

		if player ~= LocalPlayer and Combina(player,pesquisa) then
			CriarPlayerButton(player,ListaTP)
		end

	end

	task.wait()

	ListaTP.CanvasSize = UDim2.fromOffset(
		0,
		LayoutTP.AbsoluteContentSize.Y + 10
	)
end

local function AtualizarInvLista()

	LimparLista(ListaInv)

	local pesquisa = PesquisaInv.Text

	for _,player in ipairs(Players:GetPlayers()) do

		if Combina(player,pesquisa) then
			CriarPlayerButton(player,ListaInv)
		end

	end

	task.wait()

	ListaInv.CanvasSize = UDim2.fromOffset(
		0,
		LayoutInv.AbsoluteContentSize.Y + 10
	)
end

--========================================================
-- INVESTIGAR PLAYER
--========================================================

function InvestigarPlayer()

	if not JogadorSelecionado then

		ResultadoTexto.Text =
			"🔍 INVESTIGAÇÃO\n\n" ..
			"Selecione um jogador."

		return
	end

	local player = JogadorSelecionado

	local itens = {}

	local contador = {}

	local function Ler(container)

		if not container then
			return
		end

		for _,obj in ipairs(container:GetChildren()) do

			if obj:IsA("Tool") then

				contador[obj.Name] =
					(contador[obj.Name] or 0) + 1

			end

		end

	end

	Ler(player:FindFirstChild("Backpack"))
	Ler(player.Character)

	for nome,quantidade in pairs(contador) do

		table.insert(
			itens,
			{
				nome = nome,
				quantidade = quantidade
			}
		)

	end

	table.sort(itens,function(a,b)
		return string.lower(a.nome) < string.lower(b.nome)
	end)

	local detectado = "NÃO IDENTIFICADO"

	for _,item in ipairs(itens) do

		local nome = string.lower(
			string.gsub(item.nome,"%s+","")
		)

		if
			nome == "knife"
			or nome == "faca"
			or nome == "murderknife"
			or nome == "assassin"
			or nome == "dagger"
		then

			detectado = "ASSASSINO"

		elseif
			nome == "gun"
			or nome == "pistol"
			or nome == "sheriffgun"
			or nome == "sheriff"
			or nome == "revolver"
			or nome == "arma"
		then

			detectado = "XERIFE"

		end

	end

	local texto =
		"👤 PLAYER\n" ..
		player.DisplayName ..
		"  @" ..
		player.Name ..
		"\n\n" ..

		"🔎 POSSÍVEL ROLE\n" ..
		detectado ..
		"\n\n" ..

		"🎒 ITENS ENCONTRADOS\n"

	if #itens == 0 then

		texto = texto ..
			"Nenhuma Tool encontrada."

	else

		for _,item in ipairs(itens) do

			texto = texto ..
				"• " ..
				item.nome ..
				" x" ..
				item.quantidade ..
				"\n"

		end

	end

	ResultadoTexto.Text = texto
end

--========================================================
-- ROLE LOCAL
--========================================================

local function MostrarRole()

	local nome = JogadorSelecionado
		and JogadorSelecionado.DisplayName
		or LocalPlayer.DisplayName

	ResultadoTexto.Text =
		"🎭 ROLE ESCOLHIDA\n\n" ..
		"👤 " .. nome .. "\n\n" ..
		"ROLE: " .. string.upper(RoleLocal) ..
		"\n\n" ..
		"⚠ Esta escolha é local."

end

Sortear.MouseButton1Click:Connect(function()

	local numero = math.random(1,2)

	if numero == 1 then
		RoleLocal = "Assassino"
	else
		RoleLocal = "Xerife"
	end

	MostrarRole()

end)

Assassino.MouseButton1Click:Connect(function()

	RoleLocal = "Assassino"

	MostrarRole()

end)

Sheriff.MouseButton1Click:Connect(function()

	RoleLocal = "Xerife"

	MostrarRole()

end)

Inocente.MouseButton1Click:Connect(function()

	RoleLocal = "Inocente"

	MostrarRole()

end)

AtualizarItens.MouseButton1Click:Connect(function()
	InvestigarPlayer()
end)

--========================================================
-- TP
--========================================================

TPBtn.MouseButton1Click:Connect(function()

	if not JogadorSelecionado then
		return
	end

	local meuChar = LocalPlayer.Character
	local alvoChar = JogadorSelecionado.Character

	if not meuChar or not alvoChar then
		return
	end

	local meuRoot = meuChar:FindFirstChild("HumanoidRootPart")
	local alvoRoot = alvoChar:FindFirstChild("HumanoidRootPart")

	if meuRoot and alvoRoot then
		meuRoot.CFrame =
			alvoRoot.CFrame * CFrame.new(3,0,0)
	end

end)

--========================================================
-- PUXAR LOCALMENTE
--========================================================

PullBtn.MouseButton1Click:Connect(function()

	if not JogadorSelecionado then
		return
	end

	local meuChar = LocalPlayer.Character
	local alvoChar = JogadorSelecionado.Character

	if not meuChar or not alvoChar then
		return
	end

	local meuRoot = meuChar:FindFirstChild("HumanoidRootPart")
	local alvoRoot = alvoChar:FindFirstChild("HumanoidRootPart")

	if meuRoot and alvoRoot then

		-- Em LocalScript isso só pode ser representado
		-- localmente e não altera o servidor de verdade.

		alvoRoot.CFrame =
			meuRoot.CFrame * CFrame.new(3,0,0)

	end

end)

--========================================================
-- FLY
--========================================================

local FlyConnection

local function LigarFly()

	FlyAtivo = true

	AtualizarBotao(
		FlyBtn,
		"✈ FLY",
		true
	)

	if FlyConnection then
		FlyConnection:Disconnect()
	end

	FlyConnection =
		RunService.RenderStepped:Connect(function()

			if not FlyAtivo then
				return
			end

			local char = LocalPlayer.Character

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

			local direcao = Vector3.zero

			if UserInputService:IsKeyDown(Enum.KeyCode.W) then
				direcao += camera.CFrame.LookVector
			end

			if UserInputService:IsKeyDown(Enum.KeyCode.S) then
				direcao -= camera.CFrame.LookVector
			end

			if UserInputService:IsKeyDown(Enum.KeyCode.A) then
				direcao -= camera.CFrame.RightVector
			end

			if UserInputService:IsKeyDown(Enum.KeyCode.D) then
				direcao += camera.CFrame.RightVector
			end

			if UserInputService:IsKeyDown(Enum.KeyCode.Space) then
				direcao += Vector3.yAxis
			end

			if UserInputService:IsKeyDown(Enum.KeyCode.LeftControl) then
				direcao -= Vector3.yAxis
			end

			if direcao.Magnitude > 0 then

				root.AssemblyLinearVelocity =
					direcao.Unit * FlySpeed

			else

				root.AssemblyLinearVelocity =
					Vector3.zero

			end

			humanoid.AutoRotate = false

		end)

end

local function DesligarFly()

	FlyAtivo = false

	AtualizarBotao(
		FlyBtn,
		"✈ FLY",
		false
	)

	if FlyConnection then

		FlyConnection:Disconnect()
		FlyConnection = nil

	end

	local char = LocalPlayer.Character

	if char then

		local humanoid =
			char:FindFirstChildOfClass("Humanoid")

		local root =
			char:FindFirstChild("HumanoidRootPart")

		if humanoid then
			humanoid.AutoRotate = true
		end

		if root then
			root.AssemblyLinearVelocity = Vector3.zero
		end

	end

end

FlyBtn.MouseButton1Click:Connect(function()

	if FlyAtivo then
		DesligarFly()
	else
		LigarFly()
	end

end)

--========================================================
-- INVISIBILIDADE
--========================================================

local function AplicarInvisibilidade()

	local char = LocalPlayer.Character

	if not char then
		return
	end

	for _,obj in ipairs(char:GetDescendants()) do

		if obj:IsA("BasePart") then

			obj.LocalTransparencyModifier =
				Invisivel and 1 or 0

		elseif obj:IsA("Decal") then

			obj.Transparency =
				Invisivel and 1 or 0

		end

	end

end

InvisBtn.MouseButton1Click:Connect(function()

	Invisivel = not Invisivel

	AtualizarBotao(
		InvisBtn,
		"👻 INVISÍVEL",
		Invisivel
	)

	AplicarInvisibilidade()

end)

LocalPlayer.CharacterAdded:Connect(function(char)

	char:WaitForChild("HumanoidRootPart")

	task.wait(0.2)

	if Invisivel then
		AplicarInvisibilidade()
	end

end)

--========================================================
-- ESP
--========================================================

local function RemoverESP(player)

	local objetos = ESPs[player]

	if not objetos then
		return
	end

	for _,obj in ipairs(objetos) do

		if obj and obj.Parent then
			obj:Destroy()
		end

	end

	ESPs[player] = nil

end

local function CriarESP(player)

	if player == LocalPlayer then
		return
	end

	RemoverESP(player)

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

	local head = char:FindFirstChild("Head")

	local billboard

	if head then

		billboard =
			Instance.new("BillboardGui")

		billboard.Name = "MurderESPName"

		billboard.Size =
			UDim2.fromOffset(180,40)

		billboard.StudsOffset =
			Vector3.new(0,3,0)

		billboard.AlwaysOnTop = true
		billboard.Adornee = head
		billboard.Parent = char

		local label =
			Instance.new("TextLabel")

		label.Size = UDim2.fromScale(1,1)

		label.BackgroundTransparency = 1

		label.Text =
			player.DisplayName ..
			"\n@" ..
			player.Name

		label.TextColor3 =
			Color3.fromRGB(255,255,255)

		label.TextStrokeTransparency = 0

		label.TextSize = 13

		label.Font =
			Enum.Font.GothamBold

		label.Parent = billboard

	end

	ESPs[player] = {
		highlight,
		billboard
	}

end

local function AtualizarESP()

	if not ESPAtivo then

		for player in pairs(ESPs) do
			RemoverESP(player)
		end

		return

	end

	for _,player in ipairs(Players:GetPlayers()) do

		if player ~= LocalPlayer then
			CriarESP(player)
		end

	end

end

ESPBtn.MouseButton1Click:Connect(function()

	ESPAtivo = not ESPAtivo

	AtualizarBotao(
		ESPBtn,
		"👁 ESP",
		ESPAtivo
	)

	AtualizarESP()

end)

--========================================================
-- ABAS
--========================================================

local function Aba(nome)

	Funcoes.Visible =
		nome == "FUNCOES"

	TPFrame.Visible =
		nome == "TP"

	Investigar.Visible =
		nome == "INVESTIGAR"

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

--========================================================
-- PESQUISA
--========================================================

PesquisaTP:GetPropertyChangedSignal("Text"):Connect(
	AtualizarTPLista
)

PesquisaInv:GetPropertyChangedSignal("Text"):Connect(
	AtualizarInvLista
)

--========================================================
-- PLAYERS
--========================================================

Players.PlayerAdded:Connect(function(player)

	player.CharacterAdded:Connect(function()

		task.wait(1)

		if ESPAtivo then
			CriarESP(player)
		end

	end)

	task.wait(0.2)

	AtualizarTPLista()
	AtualizarInvLista()

end)

Players.PlayerRemoving:Connect(function(player)

	RemoverESP(player)

	if JogadorSelecionado == player then

		JogadorSelecionado = nil

		ResultadoTexto.Text =
			"🔍 INVESTIGAÇÃO\n\n" ..
			"O jogador selecionado saiu."

	end

	task.wait()

	AtualizarTPLista()
	AtualizarInvLista()

end)

--========================================================
-- FECHAR / ABRIR
--========================================================

Fechar.MouseButton1Click:Connect(function()

	Main.Visible = false
	Abrir.Visible = true

end)

Abrir.MouseButton1Click:Connect(function()

	Main.Visible = true
	Abrir.Visible = false

end)

--========================================================
-- ARRASTAR
--========================================================

local Arrastando = false
local InicioMouse
local InicioPos

Top.InputBegan:Connect(function(input)

	if
		input.UserInputType ==
			Enum.UserInputType.MouseButton1

		or

		input.UserInputType ==
			Enum.UserInputType.Touch
	then

		Arrastando = true

		InicioMouse = input.Position
		InicioPos = Main.Position

	end

end)

UserInputService.InputChanged:Connect(function(input)

	if not Arrastando then
		return
	end

	if
		input.UserInputType ==
			Enum.UserInputType.MouseMovement

		or

		input.UserInputType ==
			Enum.UserInputType.Touch
	then

		local delta =
			input.Position - InicioMouse

		Main.Position = UDim2.new(
			InicioPos.X.Scale,
			InicioPos.X.Offset + delta.X,

			InicioPos.Y.Scale,
			InicioPos.Y.Offset + delta.Y
		)

	end

end)

UserInputService.InputEnded:Connect(function(input)

	if
		input.UserInputType ==
			Enum.UserInputType.MouseButton1

		or

		input.UserInputType ==
			Enum.UserInputType.Touch
	then

		Arrastando = false

	end

end)

--========================================================
-- INICIAR
--========================================================

Aba("FUNCOES")

AtualizarTPLista()
AtualizarInvLista()

AtualizarBotao(
	FlyBtn,
	"✈ FLY",
	false
)

AtualizarBotao(
	InvisBtn,
	"👻 INVISÍVEL",
	false
)

AtualizarBotao(
	ESPBtn,
	"👁 ESP",
	false
)
