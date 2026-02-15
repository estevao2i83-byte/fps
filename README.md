-- [[ CONFIGURAÇÕES DE ACESSO ]] --
local VALID_KEY = "7f9c2a81-b4d3-4e6f-9a2c-5d8e1b7c3f90"
local DISCORD_LINK = "https://discord.gg/nQ3YqHFPCw"

local Players = game:GetService("Players")
local RunService = game:GetService("RunService")
local UserInputService = game:GetService("UserInputService")
local LocalPlayer = Players.LocalPlayer
local Camera = workspace.CurrentCamera

-- Configurações de Funções
local Config = {
    Aimbot = false,
    ESP = false,
    Speed = false,
    FOV = 200,
    Visible = true
}

-- [[ INTERFACE PRINCIPAL ]] --
local ScreenGui = Instance.new("ScreenGui")
ScreenGui.Name = "UltraHubSystem"
ScreenGui.Parent = game:GetService("CoreGui")
ScreenGui.ResetOnSpawn = false

-- --- TELA DE KEY (SISTEMA DE LOGIN) --- --
local KeyFrame = Instance.new("Frame")
KeyFrame.Name = "KeyFrame"
KeyFrame.Parent = ScreenGui
KeyFrame.BackgroundColor3 = Color3.fromRGB(20, 20, 20)
KeyFrame.Size = UDim2.new(0, 300, 0, 180)
KeyFrame.Position = UDim2.new(0.5, -150, 0.5, -90)
KeyFrame.Active = true
KeyFrame.Draggable = true

local KeyTitle = Instance.new("TextLabel")
KeyTitle.Parent = KeyFrame
KeyTitle.Size = UDim2.new(1, 0, 0, 30)
KeyTitle.Text = "SISTEMA DE ACESSO"
KeyTitle.BackgroundColor3 = Color3.fromRGB(255, 0, 0)
KeyTitle.TextColor3 = Color3.fromRGB(255, 255, 255)

local KeyInput = Instance.new("TextBox")
KeyInput.Parent = KeyFrame
KeyInput.Size = UDim2.new(0.8, 0, 0, 30)
KeyInput.Position = UDim2.new(0.1, 0, 0.25, 0)
KeyInput.PlaceholderText = "Digite a Key..."
KeyInput.Text = ""
KeyInput.BackgroundColor3 = Color3.fromRGB(40, 40, 40)
KeyInput.TextColor3 = Color3.fromRGB(255, 255, 255)

local KeyBtn = Instance.new("TextButton")
KeyBtn.Parent = KeyFrame
KeyBtn.Size = UDim2.new(0.8, 0, 0, 30)
KeyBtn.Position = UDim2.new(0.1, 0, 0.5, 0)
KeyBtn.Text = "VERIFICAR KEY"
KeyBtn.BackgroundColor3 = Color3.fromRGB(0, 150, 0)
KeyBtn.TextColor3 = Color3.fromRGB(255, 255, 255)

local CopyDiscordBtn = Instance.new("TextButton")
CopyDiscordBtn.Parent = KeyFrame
CopyDiscordBtn.Size = UDim2.new(0.8, 0, 0, 25)
CopyDiscordBtn.Position = UDim2.new(0.1, 0, 0.75, 0)
CopyDiscordBtn.Text = "Copiar Link do Discord"
CopyDiscordBtn.BackgroundColor3 = Color3.fromRGB(88, 101, 242)
CopyDiscordBtn.TextColor3 = Color3.fromRGB(255, 255, 255)

-- Função para copiar o link
CopyDiscordBtn.MouseButton1Click:Connect(function()
    setclipboard(DISCORD_LINK)
    CopyDiscordBtn.Text = "LINK COPIADO!"
    wait(2)
    CopyDiscordBtn.Text = "Copiar Link do Discord"
end)

-- --- PAINEL DE FUNÇÕES (FPS HUB) --- --
local MainFrame = Instance.new("Frame")
MainFrame.Parent = ScreenGui
MainFrame.BackgroundColor3 = Color3.fromRGB(15, 15, 15)
MainFrame.BorderSizePixel = 2
MainFrame.Position = UDim2.new(0.1, 0, 0.1, 0)
MainFrame.Size = UDim2.new(0, 200, 0, 220)
MainFrame.Visible = false 
MainFrame.Draggable = true
MainFrame.Active = true

local Title = Instance.new("TextLabel")
Title.Parent = MainFrame
Title.Size = UDim2.new(1, 0, 0, 30)
Title.Text = "FPS KILLER (K)"
Title.BackgroundColor3 = Color3.fromRGB(255, 0, 0)
Title.TextColor3 = Color3.fromRGB(255, 255, 255)

local function CreateToggle(text, pos, config_key)
    local Btn = Instance.new("TextButton")
    Btn.Parent = MainFrame
    Btn.Size = UDim2.new(0.9, 0, 0, 40)
    Btn.Position = pos
    Btn.Text = text .. ": OFF"
    Btn.BackgroundColor3 = Color3.fromRGB(30, 30, 30)
    Btn.TextColor3 = Color3.fromRGB(255, 255, 255)
    
    Btn.MouseButton1Click:Connect(function()
        Config[config_key] = not Config[config_key]
        Btn.Text = text .. (Config[config_key] and ": ON" or ": OFF")
        Btn.BackgroundColor3 = Config[config_key] and Color3.fromRGB(0, 150, 0) or Color3.fromRGB(30, 30, 30)
    end)
end

CreateToggle("AIMBOT", UDim2.new(0.05, 0, 0.2, 0), "Aimbot")
CreateToggle("ESP BOX", UDim2.new(0.05, 0, 0.45, 0), "ESP")
CreateToggle("SUPER SPEED", UDim2.new(0.05, 0, 0.7, 0), "Speed")

-- Lógica de Validação da Key
KeyBtn.MouseButton1Click:Connect(function()
    if KeyInput.Text == VALID_KEY then
        KeyFrame.Visible = false
        MainFrame.Visible = true
        print("Acesso Garantido!")
    else
        KeyBtn.Text = "KEY INCORRETA!"
        wait(2)
        KeyBtn.Text = "VERIFICAR KEY"
    end
end)

-- Atalho K (Só funciona após validar a Key)
UserInputService.InputBegan:Connect(function(input)
    if input.KeyCode == Enum.KeyCode.K and not KeyFrame.Visible then
        Config.Visible = not Config.Visible
        MainFrame.Visible = Config.Visible
    end
end)

-- --- LÓGICA TÉCNICA (FPS) --- --
local function GetClosestToMouse()
    local Target = nil
    local MaxDist = Config.FOV
    for _, v in pairs(Players:GetPlayers()) do
        if v ~= LocalPlayer and v.Character and v.Character:FindFirstChild("Head") then
            local Hum = v.Character:FindFirstChildOfClass("Humanoid")
            if Hum and Hum.Health > 0 then
                local Pos, OnScreen = Camera:WorldToViewportPoint(v.Character.Head.Position)
                if OnScreen then
                    local MousePos = UserInputService:GetMouseLocation()
                    local Dist = (Vector2.new(Pos.X, Pos.Y) - MousePos).Magnitude
                    if Dist < MaxDist then
                        Target = v.Character.Head
                        MaxDist = Dist
                    end
                end
            end
        end
    end
    return Target
end

RunService.RenderStepped:Connect(function()
    -- Speed Hack
    if Config.Speed and LocalPlayer.Character and LocalPlayer.Character:FindFirstChild("Humanoid") then
        LocalPlayer.Character.Humanoid.WalkSpeed = 60
    elseif not Config.Speed and LocalPlayer.Character and LocalPlayer.Character:FindFirstChild("Humanoid") then
        LocalPlayer.Character.Humanoid.WalkSpeed = 16
    end

    -- Aimbot Puxador (Segurando Botão Direito do Mouse)
    if Config.Aimbot then
        local Target = GetClosestToMouse()
        if Target and UserInputService:IsMouseButtonPressed(Enum.UserInputType.MouseButton2) then
            local TargetPos = Camera:WorldToViewportPoint(Target.Position)
            local MousePos = UserInputService:GetMouseLocation()
            -- O comando 'mousemoverel' faz a mira "puxar" de verdade no FPS
            if mousemoverel then
                mousemoverel((TargetPos.X - MousePos.X) * 0.5, (TargetPos.Y - MousePos.Y) * 0.5)
            else
                Camera.CFrame = CFrame.new(Camera.CFrame.Position, Target.Position)
            end
        end
    end
end)
