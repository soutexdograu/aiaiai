-- Configurações do ESP
local ESPColor = Color3.new(1, 1, 1) -- Branco
local LineThickness = 2 -- Espessura das bordas
local ESPEnabled = true -- Ativar/Desativar ESP

-- Função para criar as bordas ao redor de um jogador
local function createESP(player)
    if player.Character and player.Character:FindFirstChild("HumanoidRootPart") then
        local humanoidPart = player.Character.HumanoidRootPart

        -- Criar linhas para formar as bordas da box
        local topLine = Drawing.new("Line")
        local bottomLine = Drawing.new("Line")
        local leftLine = Drawing.new("Line")
        local rightLine = Drawing.new("Line")

        -- Configurações das linhas
        for _, line in pairs({topLine, bottomLine, leftLine, rightLine}) do
            line.Color = ESPColor
            line.Thickness = LineThickness
            line.Transparency = 1
            line.Visible = false
        end

        -- Atualizar as bordas durante o jogo
        game:GetService("RunService").RenderStepped:Connect(function()
            if ESPEnabled and humanoidPart and humanoidPart.Parent then
                local screenPosition, onScreen = workspace.CurrentCamera:WorldToViewportPoint(humanoidPart.Position)

                if onScreen then
                    local boxWidth = 100 -- Largura da box
                    local boxHeight = 150 -- Altura da box
                    local topLeft = Vector2.new(screenPosition.X - boxWidth / 2, screenPosition.Y - boxHeight / 2)
                    local topRight = Vector2.new(screenPosition.X + boxWidth / 2, screenPosition.Y - boxHeight / 2)
                    local bottomLeft = Vector2.new(screenPosition.X - boxWidth / 2, screenPosition.Y + boxHeight / 2)
                    local bottomRight = Vector2.new(screenPosition.X + boxWidth / 2, screenPosition.Y + boxHeight / 2)

                    -- Atualizar as posições das linhas
                    topLine.From = topLeft
                    topLine.To = topRight
                    bottomLine.From = bottomLeft
                    bottomLine.To = bottomRight
                    leftLine.From = topLeft
                    leftLine.To = bottomLeft
                    rightLine.From = topRight
                    rightLine.To = bottomRight

                    -- Tornar as linhas visíveis
                    for _, line in pairs({topLine, bottomLine, leftLine, rightLine}) do
                        line.Visible = true
                    end
                else
                    -- Tornar as linhas invisíveis
                    for _, line in pairs({topLine, bottomLine, leftLine, rightLine}) do
                        line.Visible = false
                    end
                end
            else
                -- Tornar as linhas invisíveis
                for _, line in pairs({topLine, bottomLine, leftLine, rightLine}) do
                    line.Visible = false
                end
            end
        end)
    end
end

-- Aplicar ESP para todos os jogadores no jogo
for _, player in pairs(game.Players:GetPlayers()) do
    if player ~= game.Players.LocalPlayer then
        createESP(player)
    end
end

-- Detectar novos jogadores entrando no jogo
game.Players.PlayerAdded:Connect(function(player)
    if player ~= game.Players.LocalPlayer then
        player.CharacterAdded:Connect(function()
            createESP(player)
        end)
    end
end)

