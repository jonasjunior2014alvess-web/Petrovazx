-- Petrova.Y Main Suite (Blue/Baby Blue Theme + Custom Audio/Old Panel)
local Players = game:GetService("Players")
local RunService = game:GetService("RunService")
local UserInputService = game:GetService("UserInputService")
local Workspace = game:GetService("Workspace")
local Lighting = game:GetService("Lighting")
local LocalPlayer = Players.LocalPlayer

-- Remove interface antiga se já existir
if game.CoreGui:FindFirstChild("PetrovaMM2GUI") then
    game.CoreGui.PetrovaMM2GUI:Destroy()
end

-- Variáveis de Estado
local speedMultiplier = 60
local isDraggingSpeed = false
local lastCameraCFrame = Workspace.CurrentCamera.CFrame

-- Interface ScreenGui
local ScreenGui = Instance.new("ScreenGui")
ScreenGui.Name = "PetrovaMM2GUI"
ScreenGui.ResetOnSpawn = false -- Não some ao morrer/reiniciar
ScreenGui.Parent = game.CoreGui

-- Janela Principal (Tema Azul Escuro)
local MainFrame = Instance.new("Frame")
MainFrame.Name = "MainFrame"
MainFrame.Size = UDim2.new(0, 260, 0, 390)
MainFrame.Position = UDim2.new(0.5, -130, 0.25, 0)
MainFrame.BackgroundColor3 = Color3.fromRGB(10, 18, 32) -- Azul Bem Escuro
MainFrame.BorderSizePixel = 2
MainFrame.BorderColor3 = Color3.fromRGB(137, 207, 240) -- Borda Azul Bebê
MainFrame.Active = true
MainFrame.Draggable = true
MainFrame.Parent = ScreenGui

local UICorner = Instance.new("UICorner")
UICorner.CornerRadius = UDim.new(0, 8)
UICorner.Parent = MainFrame

-- Topo / Pasta "Painel Petrova"
local TitleLabel = Instance.new("TextLabel")
TitleLabel.Size = UDim2.new(1, 0, 0, 35)
TitleLabel.BackgroundTransparency = 1
TitleLabel.Text = "📁 Painel Petrova"
TitleLabel.TextColor3 = Color3.fromRGB(137, 207, 240) -- Azul Bebê
TitleLabel.TextSize = 18
TitleLabel.Font = Enum.Font.GothamBold
TitleLabel.Parent = MainFrame

--------------------------------------------------------------------------------
-- 1. SEÇÃO SPEED ADJUSTABLE
--------------------------------------------------------------------------------
local SpeedLabel = Instance.new("TextLabel")
SpeedLabel.Size = UDim2.new(1, 0, 0, 20)
SpeedLabel.Position = UDim2.new(0, 0, 0, 38)
SpeedLabel.BackgroundTransparency = 1
SpeedLabel.Text = "Speed: " .. tostring(speedMultiplier)
SpeedLabel.TextColor3 = Color3.fromRGB(200, 230, 255)
SpeedLabel.TextSize = 13
SpeedLabel.Font = Enum.Font.GothamMedium
SpeedLabel.Parent = MainFrame

local SpeedLine = Instance.new("Frame")
SpeedLine.Size = UDim2.new(0, 170, 0, 4)
SpeedLine.Position = UDim2.new(0.5, -85, 0, 65)
SpeedLine.BackgroundColor3 = Color3.fromRGB(30, 60, 90)
SpeedLine.Parent = MainFrame

local SpeedCircle = Instance.new("TextButton")
SpeedCircle.Size = UDim2.new(0, 16, 0, 16)
SpeedCircle.Position = UDim2.new(0.5, -8, 0.5, -8)
SpeedCircle.BackgroundColor3 = Color3.fromRGB(137, 207, 240) -- Azul Bebê
SpeedCircle.Text = ""
SpeedCircle.Parent = SpeedLine

local SpeedCircleCorner = Instance.new("UICorner")
SpeedCircleCorner.CornerRadius = UDim.new(1, 0)
SpeedCircleCorner.Parent = SpeedCircle

local SpeedMinus = Instance.new("TextButton")
SpeedMinus.Size = UDim2.new(0, 25, 0, 25)
SpeedMinus.Position = UDim2.new(0, 10, 0, 55)
SpeedMinus.BackgroundColor3 = Color3.fromRGB(20, 40, 65)
SpeedMinus.TextColor3 = Color3.fromRGB(137, 207, 240)
SpeedMinus.Text = "-"
SpeedMinus.Font = Enum.Font.GothamBold
SpeedMinus.Parent = MainFrame

local SpeedPlus = Instance.new("TextButton")
SpeedPlus.Size = UDim2.new(0, 25, 0, 25)
SpeedPlus.Position = UDim2.new(1, -35, 0, 55)
SpeedPlus.BackgroundColor3 = Color3.fromRGB(20, 40, 65)
SpeedPlus.TextColor3 = Color3.fromRGB(137, 207, 240)
SpeedPlus.Text = "+"
SpeedPlus.Font = Enum.Font.GothamBold
SpeedPlus.Parent = MainFrame

local function updateSpeed(val)
    speedMultiplier = math.clamp(math.floor(val), 0, 250)
    SpeedLabel.Text = "Speed: " .. tostring(speedMultiplier)
    SpeedCircle.Position = UDim2.new(speedMultiplier / 250, -8, 0.5, -8)
end

SpeedCircle.InputBegan:Connect(function(input)
    if input.UserInputType == Enum.UserInputType.MouseButton1 or input.UserInputType == Enum.UserInputType.Touch then
        isDraggingSpeed = true
    end
end)

UserInputService.InputEnded:Connect(function(input)
    if input.UserInputType == Enum.UserInputType.MouseButton1 or input.UserInputType == Enum.UserInputType.Touch then
        isDraggingSpeed = false
    end
end)

UserInputService.InputChanged:Connect(function(input)
    if isDraggingSpeed and (input.UserInputType == Enum.UserInputType.MouseMovement or input.UserInputType == Enum.UserInputType.Touch) then
        local rel = math.clamp(input.Position.X - SpeedLine.AbsolutePosition.X, 0, SpeedLine.AbsoluteSize.X)
        updateSpeed((rel / SpeedLine.AbsoluteSize.X) * 250)
    end
end)

SpeedMinus.MouseButton1Click:Connect(function() updateSpeed(speedMultiplier - 10) end)
SpeedPlus.MouseButton1Click:Connect(function() updateSpeed(speedMultiplier + 10) end)

--------------------------------------------------------------------------------
-- FUNÇÃO AUXILIAR PARA CRIAR BOTÕES NO TEMA AZUL
--------------------------------------------------------------------------------
local function createBlueButton(name, text, posY)
    local btn = Instance.new("TextButton")
    btn.Name = name
    btn.Size = UDim2.new(0, 220, 0, 32)
    btn.Position = UDim2.new(0.5, -110, 0, posY)
    btn.BackgroundColor3 = Color3.fromRGB(20, 45, 75) -- Azul Médio/Escuro
    btn.BorderSizePixel = 1
    btn.BorderColor3 = Color3.fromRGB(137, 207, 240) -- Borda Azul Bebê
    btn.Text = text
    btn.TextColor3 = Color3.fromRGB(137, 207, 240) -- Texto Azul Bebê
    btn.Font = Enum.Font.GothamBold
    btn.TextSize = 13
    btn.Parent = MainFrame

    local corner = Instance.new("UICorner")
    corner.CornerRadius = UDim.new(0, 6)
    corner.Parent = btn

    return btn
end

--------------------------------------------------------------------------------
-- 2. BOTÃO BOMB
--------------------------------------------------------------------------------
local BombBtn = createBlueButton("BombBtn", "Bomb", 100)

BombBtn.MouseButton1Click:Connect(function()
    local Player = game.Players.LocalPlayer
    local Mouse = Player:GetMouse()
    local PlayerGui = Player:WaitForChild("PlayerGui")

    local Config = { Cooldown = 2, CanUse = true }
    local CurrentDroppedBomb = nil

    local function BuildC4()
        local Main = Instance.new("Part")
        Main.Name = "Handle"
        Main.Size = Vector3.new(1.8, 0.7, 1.2)
        Main.Color = Color3.fromRGB(137, 207, 240)
        Main.Material = Enum.Material.Metal
        Main.CanCollide = true
        return Main
    end

    if PlayerGui:FindFirstChild("C4_Final_Menu") then
        PlayerGui.C4_Final_Menu:Destroy()
    end

    local sg = Instance.new("ScreenGui", PlayerGui)
    sg.Name = "C4_Final_Menu"
    sg.ResetOnSpawn = false

    local toggle = Instance.new("TextButton", sg)
    toggle.Size = UDim2.new(0, 60, 0, 40)
    toggle.Position = UDim2.new(0, 10, 0.5, 0)
    toggle.Text = "MENU"
    toggle.BackgroundColor3 = Color3.fromRGB(137, 207, 240)
    toggle.Draggable = true

    local frame = Instance.new("Frame", sg)
    frame.Size = UDim2.new(0, 180, 0, 100)
    frame.Position = UDim2.new(0.5, -90, 0.4, 0)
    frame.BackgroundColor3 = Color3.fromRGB(10, 18, 32)
    frame.Visible = false

    local input = Instance.new("TextBox", frame)
    input.Size = UDim2.new(0.8, 0, 0, 30)
    input.Position = UDim2.new(0.1, 0, 0.2, 0)
    input.Text = tostring(Config.Cooldown)
    input.PlaceholderText = "Giây hồi..."

    local btn = Instance.new("TextButton", frame)
    btn.Size = UDim2.new(0.8, 0, 0, 30)
    btn.Position = UDim2.new(0.1, 0, 0.6, 0)
    btn.Text = "LƯU"
    btn.BackgroundColor3 = Color3.fromRGB(137, 207, 240)

    toggle.MouseButton1Click:Connect(function() frame.Visible = not frame.Visible end)
    btn.MouseButton1Click:Connect(function() 
        Config.Cooldown = tonumber(input.Text) or 0
        frame.Visible = false
    end)

    local function GiveTool()
        local Tool = Instance.new("Tool")
        Tool.Name = "Gold C4 Bomb"
        Tool.RequiresHandle = true
        Tool.CanBeDropped = false
        Tool.Grip = CFrame.new(0, -0.2, 0.2) * CFrame.Angles(0, math.rad(180), 0)
        
        local C4Part = BuildC4()
        C4Part.Parent = Tool
        
        Tool.Activated:Connect(function()
            if not Config.CanUse then return end
            local char = Player.Character
            local hrp = char and char:FindFirstChild("HumanoidRootPart")
            if not hrp then return end
            
            Config.CanUse = false
            if CurrentDroppedBomb then CurrentDroppedBomb:Destroy() end
            
            local d_handle = BuildC4()
            d_handle.CFrame = hrp.CFrame * CFrame.new(0, -3.2, 0)
            d_handle.Parent = game.Workspace
            CurrentDroppedBomb = d_handle

            local bv = Instance.new("BodyVelocity", d_handle)
            bv.MaxForce = Vector3.new(1e5, 1e5, 1e5)
            bv.Velocity = ((Mouse.Hit.p - hrp.Position).Unit * 25) + Vector3.new(0, 10, 0)
            game.Debris:AddItem(bv, 0.1)
            
            local parts = {}
            for _, p in pairs(Tool:GetChildren()) do
                if p:IsA("BasePart") then parts[p] = p.Transparency p.Transparency = 1 end
            end
            
            task.wait(Config.Cooldown)
            if CurrentDroppedBomb then CurrentDroppedBomb:Destroy() CurrentDroppedBomb = nil end
            for p, trans in pairs(parts) do if p then p.Transparency = trans end end
            Config.CanUse = true
        end)
        
        Tool.Parent = Player.Backpack
    end

    Player.CharacterAdded:Connect(function() task.wait(1) GiveTool() end)
    GiveTool()
end)

--------------------------------------------------------------------------------
-- 3. BOTÃO NO SOM!
--------------------------------------------------------------------------------
local NoSomBtn = createBlueButton("NoSomBtn", "No som!", 140)

NoSomBtn.MouseButton1Click:Connect(function()
    loadstring(game:HttpGet("https://raw.githubusercontent.com/ZoeWarOnTop/System-V3.3/refs/heads/main/System-V3.3"))()
end)

--------------------------------------------------------------------------------
-- 4. BOTÃO LOBBY - HALLOWEEN
--------------------------------------------------------------------------------
local LobbyHalloweenBtn = createBlueButton("LobbyHalloweenBtn", "lobby - Halloween", 180)

LobbyHalloweenBtn.MouseButton1Click:Connect(function()
    for _, obj in pairs(Workspace:GetChildren()) do
        if obj:IsA("Model") or obj:IsA("Folder") or obj:IsA("BasePart") then
            if not obj:IsDescendantOf(LocalPlayer.Character) and obj.Name ~= "Camera" then
                for _, descendant in pairs(obj:GetDescendants()) do
                    if descendant:IsA("BasePart") then
                        descendant.Transparency = 1
                    end
                end
            end
        end
    end

    local hrp = LocalPlayer.Character and LocalPlayer.Character:FindFirstChild("HumanoidRootPart")
    if hrp then
        local success, objects = pcall(function()
            return game:GetObjects("rbxassetid://12858778196")
        end)

        if success and objects and #objects > 0 then
            local mapModel = objects[1]
            mapModel.Name = "CustomHalloweenLobby_Loaded"
            
            if not mapModel.PrimaryPart then
                local primary = mapModel:FindFirstChildWhichIsA("BasePart", true)
                if primary then
                    mapModel.PrimaryPart = primary
                end
            end

            if mapModel.PrimaryPart then
                local feetPosition = hrp.Position - Vector3.new(0, 3, 0)
                mapModel:SetPrimaryPartCFrame(CFrame.new(feetPosition))
            else
                mapModel:MoveTo(hrp.Position - Vector3.new(0, 3, 0))
            end

            mapModel.Parent = Workspace
        end
    end

    Lighting.GlobalShadows = true
    Lighting.Brightness = 0.3
    Lighting.OutdoorAmbient = Color3.fromRGB(100, 30, 120)
    Lighting.Ambient = Color3.fromRGB(50, 15, 60)
    Lighting.FogColor = Color3.fromRGB(25, 5, 35)
    Lighting.FogStart = 10
    Lighting.FogEnd = 250
end)

--------------------------------------------------------------------------------
-- 5. BOTÃO LOBBY - SUMMER
--------------------------------------------------------------------------------
local LobbySummerBtn = createBlueButton("LobbySummerBtn", "lobby - Summer", 220)

LobbySummerBtn.MouseButton1Click:Connect(function()
    local ASSET_ID = "14534220661"
    local ALTITUDE = 2000

    local function setupHighLobby()
        local char = LocalPlayer.Character or LocalPlayer.CharacterAdded:Wait()
        local hrp = char:WaitForChild("HumanoidRootPart", 5)
        if not hrp then return end

        for _, obj in pairs(Workspace:GetChildren()) do
            if obj.Name:lower():find("lobby") and obj.Name ~= "CustomSummerLobby" then
                for _, part in pairs(obj:GetDescendants()) do
                    if part:IsA("BasePart") then
                        part.Transparency = 1
                    end
                end
            end
        end

        local customLobby = Workspace:FindFirstChild("CustomSummerLobby")
        if not customLobby then
            local success, objects = pcall(function()
                return game:GetObjects("rbxassetid://" .. ASSET_ID)
            end)

            if success and objects and #objects > 0 then
                customLobby = objects[1]
                customLobby.Name = "CustomSummerLobby"

                for _, part in pairs(customLobby:GetDescendants()) do
                    if part:IsA("BasePart") then
                        part.CanCollide = true
                    end
                end

                customLobby:PivotTo(CFrame.new(0, ALTITUDE, 0))
                customLobby.Parent = Workspace
            end
        end

        if customLobby then
            local basePart = customLobby.PrimaryPart or customLobby:FindFirstChildWhichIsA("BasePart", true)
            if basePart then
                hrp.CFrame = basePart.CFrame * CFrame.new(0, 10, 0)
            else
                hrp.CFrame = CFrame.new(0, ALTITUDE + 10, 0)
            end
        end

        Lighting.GlobalShadows = true
        Lighting.Brightness = 0.3
        Lighting.OutdoorAmbient = Color3.fromRGB(100, 30, 120)
        Lighting.Ambient = Color3.fromRGB(50, 15, 60)
    end

    task.wait(1)
    setupHighLobby()

    LocalPlayer.CharacterAdded:Connect(function()
        task.wait(1.5)
        setupHighLobby()
    end)
end)

--------------------------------------------------------------------------------
-- 6. BOTÃO MURDER - XERIFE
--------------------------------------------------------------------------------
local MurderXerifeBtn = createBlueButton("MurderXerifeBtn", "murder - xerife", 260)

MurderXerifeBtn.MouseButton1Click:Connect(function()
    loadstring(game:HttpGet("https://raw.githubusercontent.com/S-K-I-D-S/Loaders/refs/heads/main/v3"))()
end)

--------------------------------------------------------------------------------
-- 7. BOTÃO PAINEL ANTIGO...
--------------------------------------------------------------------------------
local PainelAntigoBtn = createBlueButton("PainelAntigoBtn", "Painel antigo...", 300)

PainelAntigoBtn.MouseButton1Click:Connect(function()
    -- 1. EFEITO DE TIRO ABAFADO EM SEGUNDO PLANO (Proporção 2:1)
    task.spawn(function()
        local function aplicarAjuste(som)
            if som:IsA("Sound") then
                if som:FindFirstChild("Configurado") then return end
                
                local marcador = Instance.new("BoolValue")
                marcador.Name = "Configurado"
                marcador.Parent = som

                som.Volume = 1
                
                -- Equalizador (2 partes abafado / grave, 1 parte fino / nítido)
                local eq = Instance.new("EqualizerSoundEffect")
                eq.LowGain = 8     -- Proporção 2: Peso grave do silenciador
                eq.MidGain = -10   -- Limpeza para o som não embolar
                eq.HighGain = 4    -- Proporção 1: Estalo fino do tiro
                eq.Parent = som
                
                -- Compressor para o impacto inicial
                local comp = Instance.new("CompressorSoundEffect")
                comp.Threshold = -20
                comp.Ratio = 8
                comp.Attack = 0.005
                comp.Release = 0.1
                comp.Parent = som
            end
        end

        -- Aplica nos sons já existentes
        for _, v in pairs(game:GetDescendants()) do
            aplicarAjuste(v)
        end

        -- Detecta e aplica em novos sons criados ao atirar
        game.DescendantAdded:Connect(aplicarAjuste)
    end)

    -- 2. CARREGA O PRIMEIRO SCRIPT (Petrova)
    task.spawn(function()
        pcall(function()
            loadstring(game:HttpGet("https://raw.githubusercontent.com/luizgomessousa727-max/12kxhy-/refs/heads/main/12kxhy"))()
        end)
    end)

    -- 3. CARREGA O PAINEL (-System-v3.2)
    loadstring(game:HttpGet("https://raw.githubusercontent.com/12kxhyOnTop/System/refs/heads/main/My-System-v3.2"))()
end)

--------------------------------------------------------------------------------
-- FÍSICA DO SPEED GLITCH (LOOP DE EXECUÇÃO)
--------------------------------------------------------------------------------
RunService.Heartbeat:Connect(function()
    local char = LocalPlayer.Character
    if not char then return end
    
    local hrp = char:FindFirstChild("HumanoidRootPart")
    local hum = char:FindFirstChild("Humanoid")
    if not hrp or not hum then return end

    local isJumping = hum.FloorMaterial == Enum.Material.Air
    local isClimbing = hum:GetState() == Enum.HumanoidStateType.Climbing
    local isSwimming = hum:GetState() == Enum.HumanoidStateType.Swimming or hum.FloorMaterial == Enum.Material.Water
    local isMoving = hum.MoveDirection.Magnitude > 0

    local currentCamCFrame = Workspace.CurrentCamera.CFrame
    local camRotationDelta = (currentCamCFrame.LookVector - lastCameraCFrame.LookVector).Magnitude
    lastCameraCFrame = currentCamCFrame

    if (isClimbing or isSwimming) and UserInputService:IsKeyDown(Enum.KeyCode.Space) then
        hrp.Velocity = Vector3.new(hrp.Velocity.X, 85, hrp.Velocity.Z)
    end

    if isJumping and isMoving and speedMultiplier > 0 then
        local camBonus = 1 + (camRotationDelta * 3.5)
        local rawSpeed = speedMultiplier * camBonus * 1.5
        local cappedSpeed = math.min(rawSpeed, 350)
        
        local targetVelocity = hum.MoveDirection * cappedSpeed
        hrp.Velocity = Vector3.new(targetVelocity.X, hrp.Velocity.Y, targetVelocity.Z)
    end
end)

updateSpeed(60)
