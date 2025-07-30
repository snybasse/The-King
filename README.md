-- King Legacy - Auto Farm Seaking/Hydra + EpicChest + Interface Completa (Update 9)
repeat wait() until game:IsLoaded()
print("Jogo carregado, iniciando script...")

-- Variáveis
local player = game.Players.LocalPlayer
local workspace = game:GetService("Workspace")
local httpService = game:GetService("HttpService")
local teleportService = game:GetService("TeleportService")
local replicatedStorage = game:GetService("ReplicatedStorage")
local playerGui = player.PlayerGui
local virtualUser = game:GetService("VirtualUser")
local rodando = false
_G.AutoFarm = false
_G.AutoChest = false
_G.AutoKill = false
local chestCount = 0
local seakingCount = 0
local lastError = "Nenhum erro"
local CustomDistance = getgenv().CustomDistance or 12 -- Distância configurável

-- GUI
print("Criando GUI...")
local ScreenGui = Instance.new("ScreenGui")
ScreenGui.Name = "SeaKingAutoFarmGui"
ScreenGui.ZIndexBehavior = Enum.ZIndexBehavior.Sibling
ScreenGui.Parent = playerGui
print("ScreenGui criada, Parent: " .. tostring(ScreenGui.Parent))

local Frame = Instance.new("Frame")
Frame.Size = UDim2.new(0, 300, 0, 260)
Frame.Position = UDim2.new(0.05, 0, 0.1, 0)
Frame.BackgroundColor3 = Color3.fromRGB(25, 25, 25)
Frame.BorderSizePixel = 0
Frame.Active = true
Frame.Draggable = true
Frame.Parent = ScreenGui
print("Frame criado")

local UIStroke = Instance.new("UIStroke")
UIStroke.Thickness = 1
UIStroke.Color = Color3.fromRGB(100, 100, 100)
UIStroke.Parent = Frame

local UIScale = Instance.new("UIScale")
UIScale.Scale = player:GetAttribute("Device") == "Mobile" and 0.8 or 1
UIScale.Parent = Frame

local Titulo = Instance.new("TextLabel")
Titulo.Size = UDim2.new(1, 0, 0, 22)
Titulo.Position = UDim2.new(0, 0, 0, 2)
Titulo.BackgroundTransparency = 1
Titulo.Text = "Sea King/Hydra Auto Farm"
Titulo.Font = Enum.Font.GothamBold
Titulo.TextColor3 = Color3.fromRGB(160, 255, 200)
Titulo.TextSize = 18
Titulo.Parent = Frame
print("Titulo criado")

local TempoSK = Instance.new("TextLabel")
TempoSK.Size = UDim2.new(1, 0, 0, 18)
TempoSK.Position = UDim2.new(0, 0, 0, 24)
TempoSK.BackgroundTransparency = 1
TempoSK.Text = "SK/Hydra: --:--"
TempoSK.Font = Enum.Font.GothamBold
TempoSK.TextColor3 = Color3.fromRGB(0, 180, 255)
TempoSK.TextSize = 16
TempoSK.Parent = Frame

local TimeSource = Instance.new("TextLabel")
TimeSource.Size = UDim2.new(1, 0, 0, 18)
TimeSource.Position = UDim2.new(0, 0, 0, 42)
TimeSource.BackgroundTransparency = 1
TimeSource.Text = "Fonte: Desconhecida"
TimeSource.Font = Enum.Font.Gotham
TimeSource.TextColor3 = Color3.fromRGB(200, 200, 200)
TimeSource.TextSize = 14
TimeSource.Parent = Frame

local ServerInfo = Instance.new("TextLabel")
ServerInfo.Size = UDim2.new(1, 0, 0, 18)
ServerInfo.Position = UDim2.new(0, 0, 0, 60)
ServerInfo.BackgroundTransparency = 1
ServerInfo.Text = "Servidor: " .. #game.Players:GetPlayers() .. "/" .. game.Players.MaxPlayers
ServerInfo.Font = Enum.Font.Gotham
ServerInfo.TextColor3 = Color3.fromRGB(200, 200, 200)
ServerInfo.TextSize = 14
ServerInfo.Parent = Frame

local Status = Instance.new("TextLabel")
Status.Size = UDim2.new(1, 0, 0, 18)
Status.Position = UDim2.new(0, 0, 0, 78)
Status.BackgroundTransparency = 1
Status.Text = "Status: DESATIVADO"
Status.Font = Enum.Font.Gotham
Status.TextColor3 = Color3.fromRGB(255, 105, 105)
Status.TextSize = 14
Status.Parent = Frame

local ChestStatus = Instance.new("TextLabel")
ChestStatus.Size = UDim2.new(1, 0, 0, 18)
ChestStatus.Position = UDim2.new(0, 0, 0, 96)
ChestStatus.BackgroundTransparency = 1
ChestStatus.Text = "Chest: Não encontrado"
ChestStatus.Font = Enum.Font.Gotham
ChestStatus.TextColor3 = Color3.fromRGB(255, 105, 105)
ChestStatus.TextSize = 14
ChestStatus.Parent = Frame

local ChestCountLabel = Instance.new("TextLabel")
ChestCountLabel.Size = UDim2.new(1, 0, 0, 18)
ChestCountLabel.Position = UDim2.new(0, 0, 0, 114)
ChestCountLabel.BackgroundTransparency = 1
ChestCountLabel.Text = "Baús Coletados: 0"
ChestCountLabel.Font = Enum.Font.Gotham
ChestCountLabel.TextColor3 = Color3.fromRGB(200, 200, 200)
ChestCountLabel.TextSize = 14
ChestCountLabel.Parent = Frame

local SeakingCountLabel = Instance.new("TextLabel")
SeakingCountLabel.Size = UDim2.new(1, 0, 0, 18)
SeakingCountLabel.Position = UDim2.new(0, 0, 0, 132)
SeakingCountLabel.BackgroundTransparency = 1
SeakingCountLabel.Text = "Sea Kings/Hydras Derrotados: 0"
SeakingCountLabel.Font = Enum.Font.Gotham
SeakingCountLabel.TextSize = 14
SeakingCountLabel.Parent = Frame

local ErrorLabel = Instance.new("TextLabel")
ErrorLabel.Size = UDim2.new(1, -4, 0, 36)
ErrorLabel.Position = UDim2.new(0, 2, 0, 150)
ErrorLabel.BackgroundTransparency = 1
ErrorLabel.Text = "Erro: Nenhum"
ErrorLabel.Font = Enum.Font.Gotham
ErrorLabel.TextColor3 = Color3.fromRGB(255, 105, 105)
ErrorLabel.TextSize = 12
ErrorLabel.TextWrapped = true
ErrorLabel.Parent = Frame

local AutoFarmButton = Instance.new("TextButton")
AutoFarmButton.Size = UDim2.new(0.45, 0, 0, 30)
AutoFarmButton.Position = UDim2.new(0.05, 0, 0, 190)
AutoFarmButton.BackgroundColor3 = Color3.fromRGB(35, 150, 95)
AutoFarmButton.Text = "AUTO FARM: OFF"
AutoFarmButton.Font = Enum.Font.GothamSemibold
AutoFarmButton.TextColor3 = Color3.fromRGB(255, 255, 255)
AutoFarmButton.TextSize = 14
AutoFarmButton.Parent = Frame
print("Botões criados")

local AutoChestButton = Instance.new("TextButton")
AutoChestButton.Size = UDim2.new(0.45, 0, 0, 30)
AutoChestButton.Position = UDim2.new(0.5, 0, 0, 190)
AutoChestButton.BackgroundColor3 = Color3.fromRGB(35, 150, 95)
AutoChestButton.Text = "AUTO CHEST: OFF"
AutoChestButton.Font = Enum.Font.GothamSemibold
AutoChestButton.TextColor3 = Color3.fromRGB(255, 255, 255)
AutoChestButton.TextSize = 14
AutoChestButton.Parent = Frame

local AutoKillButton = Instance.new("TextButton")
AutoKillButton.Size = UDim2.new(0.45, 0, 0, 30)
AutoKillButton.Position = UDim2.new(0.05, 0, 0, 224)
AutoKillButton.BackgroundColor3 = Color3.fromRGB(35, 150, 95)
AutoKillButton.Text = "AUTO KILL: OFF"
AutoKillButton.Font = Enum.Font.GothamSemibold
AutoKillButton.TextColor3 = Color3.fromRGB(255, 255, 255)
AutoKillButton.TextSize = 14
AutoKillButton.Parent = Frame

local ServerHopButton = Instance.new("TextButton")
ServerHopButton.Size = UDim2.new(0.45, 0, 0, 30)
ServerHopButton.Position = UDim2.new(0.5, 0, 0, 224)
ServerHopButton.BackgroundColor3 = Color3.fromRGB(35, 150, 95)
ServerHopButton.Text = "SERVER HOP"
ServerHopButton.Font = Enum.Font.GothamSemibold
ServerHopButton.TextColor3 = Color3.fromRGB(255, 255, 255)
ServerHopButton.TextSize = 14
ServerHopButton.Parent = Frame

local UICorner = Instance.new("UICorner")
UICorner.CornerRadius = UDim.new(0, 8)
UICorner:Clone().Parent = AutoFarmButton
UICorner:Clone().Parent = AutoChestButton
UICorner:Clone().Parent = AutoKillButton
UICorner:Clone().Parent = ServerHopButton

-- Função Anti-AFK
local function antiAFK()
    spawn(function()
        while _G.AutoFarm or _G.AutoChest or _G.AutoKill do
            pcall(function()
                virtualUser:CaptureController()
                virtualUser:SetKeyDown("w")
                wait(0.1)
                virtualUser:SetKeyUp("w")
            end)
            wait(60)
        end
    end)
end

-- Função visor de tempo SK/Hydra
local function AtualizaTempoSK()
    while true do
        pcall(function()
            local timeleft = replicatedStorage:FindFirstChild("Timeleft")
            if timeleft and timeleft.ClassName == "StringValue" then
                TempoSK.Text = "SK/Hydra: " .. timeleft.Value
                TimeSource.Text = "Fonte: Timeleft (StringValue)"
                print("TempoSK atualizado: " .. timeleft.Value)
            else
                local mainGui = playerGui:FindFirstChild("MainGui")
                local starterFrame = mainGui and mainGui:FindFirstChild("StarterFrame")
                local legacyPoseFrame = starterFrame and starterFrame:FindFirstChild("LegacyPoseFrame")
                local secondSea = legacyPoseFrame and legacyPoseFrame:FindFirstChild("SecondSea")
                local lbl = secondSea and secondSea:FindFirstChild("SkTimeLabel")
                if lbl then
                    TempoSK.Text = "SK/Hydra: " .. lbl.Text
                    TimeSource.Text = "Fonte: SkTimeLabel (TextLabel)"
                    print("TempoSK atualizado via SkTimeLabel: " .. lbl.Text)
                else
                    TempoSK.Text = "SK/Hydra: --:--"
                    TimeSource.Text = "Fonte: Não encontrada"
                    lastError = "Erro: Timeleft ou SkTimeLabel não encontrado"
                    ErrorLabel.Text = lastError
                    print("Erro: Timeleft ou SkTimeLabel não encontrado")
                end
            end
        end)
        wait(1)
    end
end
spawn(AtualizaTempoSK)

-- Atualiza informações do servidor
local function AtualizaServerInfo()
    while true do
        pcall(function()
            ServerInfo.Text = "Servidor: " .. #game.Players:GetPlayers() .. "/" .. game.Players.MaxPlayers
        end)
        wait(5)
    end
end
spawn(AtualizaServerInfo)

-- Pega o tempo do Sea King/Hydra
local function GetSKTimeLeftByLabel()
    local sktimeleft
    pcall(function()
        local timeleft = replicatedStorage:FindFirstChild("Timeleft")
        if timeleft and timeleft.ClassName == "StringValue" then
            local timeStr = timeleft.Value
            if timeStr and timeStr:find(":") then
                local min, sec, ms = timeStr:match("(%d+):(%d+):(%d+)")
                if min and sec then
                    sktimeleft = tonumber(min) * 60 + tonumber(sec)
                end
            end
        else
            local mainGui = playerGui:FindFirstChild("MainGui")
            local starterFrame = mainGui and mainGui:FindFirstChild("StarterFrame")
            local legacyPoseFrame = starterFrame and starterFrame:FindFirstChild("LegacyPoseFrame")
            local secondSea = legacyPoseFrame and legacyPoseFrame:FindFirstChild("SecondSea")
            local lbl = secondSea and secondSea:FindFirstChild("SkTimeLabel")
            if lbl and lbl.Text and lbl.Text:find(":") then
                local min, sec = lbl.Text:match("(%d+):(%d+)")
                if min and sec then
                    sktimeleft = tonumber(min) * 60 + tonumber(sec)
                end
            end
        end
    end)
    return sktimeleft
end

-- Função para encontrar o Sea King/Hydra
local function FindSeaKing()
    local seaMonster = workspace:FindFirstChild("SeaMonster")
    if seaMonster then
        for _, obj in ipairs(seaMonster:GetChildren()) do
            if obj:IsA("Model") and obj:FindFirstChild("Humanoid") and obj.Humanoid.Health > 0 then
                print("SeaKing encontrado: " .. obj.Name)
                return obj
            end
        end
    end
    print("SeaKing não encontrado")
    return nil
end

-- Função para atacar o Sea King/Hydra
local function AttackSeaKing(seaking)
    local success, err = pcall(function()
        local targetPart = seaking:FindFirstChild("FireHead") or seaking:FindFirstChild("UpperTorso") or seaking:FindFirstChild("HumanoidRootPart")
        if targetPart and player.Character and player.Character:FindFirstChild("HumanoidRootPart") then
            player.Character.HumanoidRootPart.CFrame = targetPart.CFrame + Vector3.new(0, CustomDistance, 0)
            local combatEvent
            for _, obj in ipairs(replicatedStorage:GetChildren()) do
                if obj:IsA("RemoteEvent") and (obj.Name:lower():find("combat") or obj.Name:lower():find("hit") or obj.Name:lower():find("attack")) then
                    combatEvent = obj
                    print("CombatEvent encontrado: " .. obj.Name)
                    break
                end
            end
            if combatEvent then
                combatEvent:FireServer(seaking.Humanoid)
                wait(0.5)
                combatEvent:FireServer()
            else
                virtualUser:CaptureController()
                virtualUser:Button1Down(Vector2.new(0, 0), workspace.CurrentCamera.CFrame)
                wait(0.4)
                virtualUser:Button1Up(Vector2.new(0, 0), workspace.CurrentCamera.CFrame)
                print("Usando VirtualUser para ataque")
            end
        else
            error("TargetPart ou HumanoidRootPart não encontrado")
        end
    end)
    if not success then
        lastError = "Erro Auto Kill: " .. tostring(err)
        ErrorLabel.Text = lastError
        print(lastError)
    end
end

-- Função para teleporte ao EpicChest
local function TeleportToChest()
    local success = false
    local chest
    local success, err = pcall(function()
        local eventSpawns = workspace:FindFirstChild("EventSpawns")
        if eventSpawns then
            for _, obj in ipairs(eventSpawns:GetChildren()) do
                if (obj:IsA("Model") or obj:IsA("Part")) and (obj.Name:lower():find("chest") or obj.Name:lower():find("treasure")) then
                    chest = obj:IsA("Model") and (obj:FindFirstChildOfClass("Part") or obj) or obj
                    print("Chest encontrado em EventSpawns: " .. obj.Name)
                    break
                end
            end
        end
        if not chest then
            for _, obj in ipairs(workspace:GetDescendants()) do
                if (obj:IsA("Model") or obj:IsA("Part")) and (obj.Name:lower():find("epicchest") or obj.Name:lower():find("chest") or obj.Name:lower():find("chesttop") or obj.Name:lower():find("treasure")) then
                    chest = obj:IsA("Model") and (obj:FindFirstChild("ChestTop") or obj:FindFirstChild("Bottum") or obj:FindFirstChild("Base") or obj:FindFirstChildOfClass("Part")) or obj
                    print("Chest encontrado em Workspace: " .. obj.Name)
                    break
                end
            end
        end
        if chest and player.Character and player.Character:FindFirstChild("HumanoidRootPart") then
            player.Character.HumanoidRootPart.CFrame = chest.CFrame + Vector3.new(0, 4, 0)
            firetouchinterest(player.Character.HumanoidRootPart, chest:FindFirstChild("TouchInterest") or chest, 0)
            wait(0.5)
            firetouchinterest(player.Character.HumanoidRootPart, chest:FindFirstChild("TouchInterest") or chest, 1)
            success = true
            print("Teleportado para chest: " .. chest.Name)
        end
        local chestEvent
        for _, obj in ipairs(replicatedStorage:GetChildren()) do
            if obj:IsA("RemoteEvent") and (obj.Name:lower():find("chest") or obj.Name:lower():find("treasure") or obj.Name:lower():find("collect")) then
                chestEvent = obj
                print("ChestEvent encontrado: " .. obj.Name)
                break
            end
        end
        if chestEvent then
            chestEvent:FireServer(chest)
            wait(0.5)
            chestEvent:FireServer()
            success = true
            print("ChestEvent acionado")
        end
    end)
    if success then
        Status.Text = "Pegando EpicChest!"
        Status.TextColor3 = Color3.fromRGB(120, 200, 255)
        ChestStatus.Text = "Chest: Encontrado"
        ChestStatus.TextColor3 = Color3.fromRGB(120, 200, 255)
        chestCount = chestCount + 1
        ChestCountLabel.Text = "Baús Coletados: " .. chestCount
        print("Chest coletado, total: " .. chestCount)
    else
        Status.Text = "EpicChest não encontrado!"
        Status.TextColor3 = Color3.fromRGB(255, 105, 105)
        ChestStatus.Text = "Chest: Não encontrado"
        ChestStatus.TextColor3 = Color3.fromRGB(255, 105, 105)
        lastError = "Erro Auto Chest: " .. (err or "Desconhecido")
        ErrorLabel.Text = lastError
        print(lastError)
    end
    return success
end

-- Função para Server Hop
local function ServerHop()
    local success, err = pcall(function()
        local url = "https://games.roblox.com/v1/games/" .. game.PlaceId .. "/servers/Public?sortOrder=Asc&limit=10"
        local servers = httpService:JSONDecode(game:HttpGet(url))
        for _, v in ipairs(servers.data) do
            if v.playing <= v.maxPlayers * 0.4 and v.id ~= game.JobId then
                Status.Text = "Trocando para server: " .. v.id
                Status.TextColor3 = Color3.fromRGB(255, 255, 100)
                print("Trocando para server: " .. v.id)
                teleportService:TeleportToPlaceInstance(game.PlaceId, v.id, player)
                break
            end
        end
    end)
    if not success then
        lastError = "Erro Server Hop: " .. tostring(err)
        ErrorLabel.Text = lastError
        Status.Text = "Erro na troca de servidor!"
        Status.TextColor3 = Color3.fromRGB(255, 105, 105)
        print(lastError)
    end
end

-- Função Auto Kill
local function AutoKillLoop()
    while _G.AutoKill do
        local found = FindSeaKing()
        if found then
            Status.Text = "Status: FARMANDO"
            Status.TextColor3 = Color3.fromRGB(120, 255, 120)
            while FindSeaKing() and _G.AutoKill and wait(0.5) do
                AttackSeaKing(found)
            end
            seakingCount = seakingCount + 1
            SeakingCountLabel.Text = "Sea Kings/Hydras Derrotados: " .. seakingCount
            print("SeaKing derrotado, total: " .. seakingCount)
        end
        wait(1)
    end
end

-- Função Auto Chest
local function AutoChestLoop()
    while _G.AutoChest do
        local success = TeleportToChest()
        if not success then
            wait(2)
        end
    end
end

-- Função Auto Farm
local function FarmSeaKing()
    rodando = true
    while _G.AutoFarm do
        local found = FindSeaKing()
        local sktimeleft = GetSKTimeLeftByLabel()
        if found then
            Status.Text = "Status: FARMANDO"
            Status.TextColor3 = Color3.fromRGB(120, 255, 120)
            while FindSeaKing() and _G.AutoFarm and wait(0.5) do
                AttackSeaKing(found)
            end
            seakingCount = seakingCount + 1
            SeakingCountLabel.Text = "Sea Kings/Hydras Derrotados: " .. seakingCount
            print("SeaKing derrotado, total: " .. seakingCount)
            Status.Text = "Seaking/Hydra derrotado! Procurando EpicChest..."
            Status.TextColor3 = Color3.fromRGB(120, 200, 255)
            local tempoTentando = 0
            local maxTempo = 30
            repeat
                if TeleportToChest() then
                    chestCount = chestCount + 1
                    ChestCountLabel.Text = "Baús Coletados: " .. chestCount
                    break
                end
                wait(1.5)
                tempoTentando = tempoTentando + 1.5
            until (not _G.AutoFarm) or tempoTentando > maxTempo
            Status.Text = "Status: PROCURANDO SERVER"
            Status.TextColor3 = Color3.fromRGB(255, 255, 100)
            ServerHop()
        elseif sktimeleft and sktimeleft < 600 then
            Status.Text = "Aguardando Seaking/Hydra (" .. math.floor(sktimeleft / 60) .. " min)"
            Status.TextColor3 = Color3.fromRGB(200, 200, 255)
            print("Aguardando SeaKing, tempo restante: " .. sktimeleft .. " segundos")
            repeat
                wait(10)
                found = FindSeaKing()
                sktimeleft = GetSKTimeLeftByLabel()
            until not _G.AutoFarm or (sktimeleft and sktimeleft >= 600) or found
        else
            Status.Text = "Status: PROCURANDO SERVER"
            Status.TextColor3 = Color3.fromRGB(255, 255, 100)
            ServerHop()
        end
        wait(0.5)
    end
    Status.Text = "Status: DESATIVADO"
    Status.TextColor3 = Color3.fromRGB(255, 105, 105)
    rodando = false
end

-- Feedback visual nos botões
local function UpdateButton(button, state)
    button.BackgroundColor3 = state and Color3.fromRGB(200, 55, 55) or Color3.fromRGB(35, 150, 95)
end

AutoFarmButton.MouseEnter:Connect(function() AutoFarmButton.BackgroundColor3 = Color3.fromRGB(50, 170, 110) end)
AutoFarmButton.MouseLeave:Connect(function() UpdateButton(AutoF
