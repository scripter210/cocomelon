local player = game.Players.LocalPlayer
local playerGui = player:WaitForChild("PlayerGui")
local screenGui = nil
local painelAberto = false  -- Inicializa o painel fechado
local hitboxPart = nil  -- Declaração da hitbox
local espHighlights = {}  -- Para armazenar os highlights de outros jogadores

-- Função para criar a tela de Dflay
function criarTelaDflay()
    if screenGui then return end  -- Evita criar a tela mais de uma vez

    screenGui = Instance.new("ScreenGui")
    screenGui.Name = "Dflay"
    screenGui.ResetOnSpawn = false
    screenGui.Parent = playerGui

    local frame = Instance.new("Frame")
    frame.Size = UDim2.new(0.25, 0, 0.25, 0)
    frame.Position = UDim2.new(0.375, 0, 0.375, 0)
    frame.BackgroundColor3 = Color3.fromRGB(0, 0, 0)
    frame.BorderSizePixel = 0
    frame.ClipsDescendants = true
    frame.Parent = screenGui

    local corner = Instance.new("UICorner")
    corner.CornerRadius = UDim.new(0.1, 0)
    corner.Parent = frame

    local textLabel = Instance.new("TextLabel")
    textLabel.Size = UDim2.new(1, 0, 0.25, 0)
    textLabel.Position = UDim2.new(0, 0, 0, 0)
    textLabel.Text = "Dflay"
    textLabel.TextColor3 = Color3.fromRGB(255, 255, 255)
    textLabel.TextScaled = true
    textLabel.BackgroundTransparency = 1
    textLabel.Parent = frame

    local passwordBox = Instance.new("TextBox")
    passwordBox.Size = UDim2.new(0.8, 0, 0.15, 0)
    passwordBox.Position = UDim2.new(0.1, 0, 0.25, 0)
    passwordBox.PlaceholderText = "Digite a senha"
    passwordBox.TextColor3 = Color3.fromRGB(0, 0, 0)
    passwordBox.TextSize = 20
    passwordBox.BackgroundColor3 = Color3.fromRGB(255, 255, 255)
    passwordBox.ClearTextOnFocus = true
    passwordBox.Parent = frame

    local submitButton = Instance.new("TextButton")
    submitButton.Size = UDim2.new(0.8, 0, 0.15, 0)
    submitButton.Position = UDim2.new(0.1, 0, 0.45, 0)
    submitButton.Text = "Submit"
    submitButton.TextColor3 = Color3.fromRGB(0, 0, 0)
    submitButton.TextSize = 20
    submitButton.BackgroundColor3 = Color3.fromRGB(255, 255, 255)
    submitButton.Parent = frame

    -- Função para verificar a senha
    local function verificarSenha()
        -- Remover o feedback anterior, se existir
        for _, child in pairs(frame:GetChildren()) do
            if child:IsA("TextLabel") and child.Name == "Feedback" then
                child:Destroy()
            end
        end

        local feedbackLabel = Instance.new("TextLabel")
        feedbackLabel.Name = "Feedback"
        feedbackLabel.Size = UDim2.new(1, 0, 0.2, 0)
        feedbackLabel.Position = UDim2.new(0, 0, 0.7, 0)
        feedbackLabel.BackgroundTransparency = 1
        feedbackLabel.TextScaled = true
        feedbackLabel.TextSize = 20
        feedbackLabel.Parent = frame

        -- Verifica se a senha é "Staff2"
        if passwordBox.Text == "Staff2" then
            -- Senha correta: "Staff2"
            -- Remover a caixa de senha e o botão de submit
            submitButton:Destroy()
            passwordBox:Destroy()

            -- Exibir mensagem de sucesso no painel
            feedbackLabel.Text = "Senha aceita. Bom proveito, criadores Dflay."
            feedbackLabel.TextColor3 = Color3.fromRGB(0, 255, 0)  -- Texto verde para sucesso

            -- Criar a "telinha" de mensagem dentro do painel
            local mensagemFrame = Instance.new("Frame")
            mensagemFrame.Size = UDim2.new(0.8, 0, 0.1, 0)  -- Ajustar o tamanho da mensagem
            mensagemFrame.Position = UDim2.new(0.1, 0, 0.7, 0)  -- Colocar abaixo do título
            mensagemFrame.BackgroundColor3 = Color3.fromRGB(0, 0, 0)  -- Fundo preto
            mensagemFrame.BackgroundTransparency = 0.5  -- Transparência
            mensagemFrame.BorderSizePixel = 0
            mensagemFrame.Parent = frame

            -- Criar o texto dentro da "telinha"
            local mensagem = Instance.new("TextLabel")
            mensagem.Size = UDim2.new(1, 0, 1, 0)
            mensagem.Position = UDim2.new(0, 0, 0, 0)
            mensagem.BackgroundTransparency = 1
            mensagem.Text = "[Key aceita bom proveito, criadores Dflay.]"
            mensagem.TextColor3 = Color3.fromRGB(255, 255, 255)  -- Cor branca para o texto
            mensagem.TextSize = 12  -- Texto menor
            mensagem.TextScaled = true
            mensagem.Parent = mensagemFrame

            -- Apagar a "telinha" e a frase verde após 10 segundos
            game:GetService("Debris"):AddItem(mensagemFrame, 10)  -- A "telinha" desaparece após 10 segundos
            game:GetService("Debris"):AddItem(feedbackLabel, 10)  -- A frase verde desaparece após 10 segundos

            -- Aumentar o tamanho do menu
            frame.Size = UDim2.new(0.5, 0, 0.5, 0)  -- Aumentando o tamanho do painel
            frame.Position = UDim2.new(0.25, 0, 0.25, 0)

            -- Exibir opções após a senha correta
            criarOpcoesMenu(frame)
        else
            feedbackLabel.Text = "Senha incorreta. Tente novamente."
            feedbackLabel.TextColor3 = Color3.fromRGB(255, 0, 0)  -- Texto vermelho para erro
        end
    end

    -- Submeter ao pressionar Enter ou clicando no botão Submit
    passwordBox.FocusLost:Connect(function(enterPressed)
        if enterPressed then
            verificarSenha()
        end
    end)

    -- Conectar o botão de Submit ao processo de verificação
    submitButton.MouseButton1Click:Connect(function()
        verificarSenha()
    end)
end

-- Função para criar as opções após a senha ser aceita
function criarOpcoesMenu(parentFrame)
    -- Criar botão de ESP
    local espButton = Instance.new("TextButton")
    espButton.Size = UDim2.new(0.8, 0, 0.1, 0)
    espButton.Position = UDim2.new(0.1, 0, 0.25, 0)
    espButton.Text = "[ESP] OFF"
    espButton.TextColor3 = Color3.fromRGB(255, 255, 255)
    espButton.TextSize = 20
    espButton.BackgroundColor3 = Color3.fromRGB(50, 50, 50)
    espButton.Parent = parentFrame

    -- Criar botão de Hitbox
    local hitboxButton = Instance.new("TextButton")
    hitboxButton.Size = UDim2.new(0.8, 0, 0.1, 0)
    hitboxButton.Position = UDim2.new(0.1, 0, 0.4, 0)
    hitboxButton.Text = "[Hitbox] Ativar"
    hitboxButton.TextColor3 = Color3.fromRGB(255, 255, 255)
    hitboxButton.TextSize = 20
    hitboxButton.BackgroundColor3 = Color3.fromRGB(50, 50, 50)
    hitboxButton.Parent = parentFrame

    -- Função para ativar/desativar a hitbox
    hitboxButton.MouseButton1Click:Connect(function()
        if hitboxPart then
            hitboxPart:Destroy()  -- Se a hitbox já existir, destrói ela
            hitboxPart = nil
            hitboxButton.Text = "[Hitbox] Ativar"
        else
            ativarHitbox()  -- Cria a hitbox
            hitboxButton.Text = "[Hitbox] Desativar"
        end
    end)

    -- Conectar o botão de ESP
    espButton.MouseButton1Click:Connect(function()
        if espButton.Text == "[ESP] OFF" then
            espButton.Text = "[ESP] ON"
            ativarESP()  -- Habilitar ESP
        else
            espButton.Text = "[ESP] OFF"
            desativarESP()  -- Desabilitar ESP
        end
    end)
end

-- Função para ativar a ESP (Highlight Azul)
function ativarESP()
    for _, otherPlayer in pairs(game.Players:GetPlayers()) do
        if otherPlayer ~= player then
            local character = otherPlayer.Character
            if character and character:FindFirstChild("HumanoidRootPart") then
                local highlight = Instance.new("Highlight")
                highlight.Name = "ESP"
                highlight.Adornee = character
                highlight.FillColor = Color3.fromRGB(0, 0, 255)  -- Azul
                highlight.OutlineColor = Color3.fromRGB(0, 0, 255)  -- Azul
                highlight.Parent = game.Workspace
                espHighlights[otherPlayer.UserId] = highlight  -- Salvar o highlight
            end
        end
    end
end

-- Função para desativar o ESP (Remover os Highlights)
function desativarESP()
    for _, highlight in pairs(espHighlights) do
        highlight:Destroy()  -- Destruir todos os highlights
    end
    espHighlights = {}  -- Limpar a tabela de highlights
end

-- Função para ativar a Hitbox
function ativarHitbox()
    hitboxPart = Instance.new("Part")
    hitboxPart.Size = Vector3.new(10, 10, 10)  -- Tamanho da hitbox
    hitboxPart.Position = player.Character.HumanoidRootPart.Position + Vector3.new(0, 5, 0)  -- Coloca acima do personagem
    hitboxPart.Shape = Enum.PartType.Ball  -- Forma esférica
    hitboxPart.Color = Color3.fromRGB
