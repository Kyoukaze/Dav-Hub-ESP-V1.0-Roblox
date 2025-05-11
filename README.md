-- Dav Hub ESP V1.0 by Davi
-- Suporte básico para ESP Tracer com Team Check e Hub interativo

-- Serviços
local Players = game:GetService("Players")
local RunService = game:GetService("RunService")
local LocalPlayer = Players.LocalPlayer

-- Variáveis
local Tracers = {}
local ESP_Ativo = true
local TeamCheck = true

-- Função de criar Tracer
local function CriarTracer(player)
    local tracer = Drawing.new("Line")
    tracer.Thickness = 1.5
    tracer.Color = Color3.new(1, 0, 0)
    tracer.Transparency = 1
    tracer.Visible = false
    Tracers[player] = tracer
end

-- Remover Tracer
local function RemoverTracer(player)
    if Tracers[player] then
        Tracers[player]:Remove()
        Tracers[player] = nil
    end
end

-- Loop de atualização
RunService.RenderStepped:Connect(function()
    if not ESP_Ativo then
        for _, tracer in pairs(Tracers) do
            tracer.Visible = false
        end
        return
    end

    for _, player in pairs(Players:GetPlayers()) do
        if player ~= LocalPlayer and player.Character and player.Character:FindFirstChild("HumanoidRootPart") then
            if not Tracers[player] then
                CriarTracer(player)
            end

            local tracer = Tracers[player]
            local hrp = player.Character.HumanoidRootPart
            local pos, onScreen = workspace.CurrentCamera:WorldToViewportPoint(hrp.Position)

            tracer.Visible = onScreen and (not TeamCheck or player.Team ~= LocalPlayer.Team)
            tracer.From = Vector2.new(workspace.CurrentCamera.ViewportSize.X / 2, workspace.CurrentCamera.ViewportSize.Y)
            tracer.To = Vector2.new(pos.X, pos.Y)
        end
    end
end)

-- Remover Tracers ao sair
Players.PlayerRemoving:Connect(RemoverTracer)

-- UI
local ScreenGui = Instance.new("ScreenGui", game.CoreGui)
ScreenGui.Name = "DavHubESP"

local HubFrame = Instance.new("Frame", ScreenGui)
HubFrame.Size = UDim2.new(0, 200, 0, 100)
HubFrame.Position = UDim2.new(0.1, 0, 0.1, 0)
HubFrame.BackgroundColor3 = Color3.fromRGB(20, 20, 20)
HubFrame.BorderSizePixel = 0
HubFrame.Active = true
HubFrame.Draggable = true

local ToggleButton = Instance.new("TextButton", HubFrame)
ToggleButton.Size = UDim2.new(1, 0, 0.3, 0)
ToggleButton.Position = UDim2.new(0, 0, 0, 0)
ToggleButton.Text = "Toggle ESP"
ToggleButton.BackgroundColor3 = Color3.fromRGB(40, 40, 40)
ToggleButton.TextColor3 = Color3.new(1, 1, 1)
ToggleButton.MouseButton1Click:Connect(function()
    ESP_Ativo = not ESP_Ativo
    ToggleButton.Text = "ESP: " .. (ESP_Ativo and "ON" or "OFF")
end)

local BubbleButton = Instance.new("ImageButton", ScreenGui)
BubbleButton.Size = UDim2.new(0, 50, 0, 50)
BubbleButton.Position = UDim2.new(0, 20, 0.5, 0)
BubbleButton.BackgroundTransparency = 1
BubbleButton.Image = "rbxassetid://UPLOAD_IMAGE_HERE" -- substitua pelo ID correto após upload

BubbleButton.Active = true
BubbleButton.Draggable = true
BubbleButton.MouseButton1Click:Connect(function()
    HubFrame.Visible = not HubFrame.Visible
end)
