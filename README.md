-- Criar tela principal
local ScreenGui = Instance.new("ScreenGui")
ScreenGui.Name = "PlayerESP"
ScreenGui.Parent = game.Players.LocalPlayer:WaitForChild("PlayerGui")

-- Frame para botão ON/OFF
local Frame = Instance.new("Frame")
Frame.Size = UDim2.new(0.2, 0, 0.1, 0)
Frame.Position = UDim2.new(0.4, 0, 0.85, 0)
Frame.BackgroundTransparency = 0.5
Frame.BackgroundColor3 = Color3.fromRGB(0, 0, 0)
Frame.Parent = ScreenGui

local ToggleButton = Instance.new("TextButton")
ToggleButton.Size = UDim2.new(1, 0, 1, 0)
ToggleButton.BackgroundTransparency = 1
ToggleButton.TextScaled = true
ToggleButton.Text = "ON"
ToggleButton.TextColor3 = Color3.fromRGB(255, 255, 255)
ToggleButton.Parent = Frame

-- Ajuste de iluminação dinâmico
local function adjustLighting()
    local lighting = game.Lighting

    -- Evita que blocos e paredes fiquem completamente brancos
    lighting.Brightness = 1.5
    lighting.Ambient = Color3.fromRGB(80, 80, 80) -- Mantém sombras naturais
    lighting.OutdoorAmbient = Color3.fromRGB(150, 150, 150)

    -- Adiciona luz no jogador apenas em lugares escuros
    local player = game.Players.LocalPlayer
    if player.Character then
        local head = player.Character:FindFirstChild("Head")
        if head then
            local light = head:FindFirstChild("ESP_Light")
            if not light then
                light = Instance.new("PointLight")
                light.Name = "ESP_Light"
                light.Parent = head
                light.Brightness = 1.5 -- Brilho moderado
                light.Range = 15 -- Alcance suficiente sem exagero
                light.Color = Color3.fromRGB(255, 255, 255) -- Luz branca natural
            end
            
            -- Ajusta intensidade com base na escuridão do ambiente
            if lighting.Brightness < 1 then
                light.Brightness = 2
                light.Range = 25
            else
                light.Brightness = 1.5
                light.Range = 15
            end
        end
    end
end

-- Monitora mudanças de iluminação
game:GetService("RunService").RenderStepped:Connect(adjustLighting)

-- Toggle do ESP
local showESP = true
local function toggleESP()
    showESP = not showESP
    for _, player in pairs(game.Players:GetPlayers()) do
        if player ~= game.Players.LocalPlayer and player.Character then
            local esp = player.Character:FindFirstChild("Head"):FindFirstChild("ESP")
            if esp then esp.Enabled = showESP end

            local highlight = player.Character:FindFirstChild("ESP_Highlight")
            if highlight then highlight.Enabled = showESP end
        end
    end
    ToggleButton.Text = showESP and "ON" or "OFF"
end

ToggleButton.MouseButton1Click:Connect(toggleESP)
