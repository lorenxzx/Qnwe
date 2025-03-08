local Library = {}
Library.Elements = {}

-- Configurações visuais
local theme = {
    main = Color3.fromRGB(35, 35, 35),
    secondary = Color3.fromRGB(45, 45, 45),
    accent = Color3.fromRGB(0, 150, 255),
    text = Color3.new(1, 1, 1),
    cornerRadius = UDim.new(0, 8)
}

function Library:CreateWindow(name)
    local window = Instance.new("ScreenGui")
    window.Name = "HubWindow"
    window.Parent = game.CoreGui

    local mainFrame = Instance.new("Frame")
    mainFrame.Size = UDim2.new(0, 600, 0, 400)
    mainFrame.Position = UDim2.new(0.5, -300, 0.5, -200)
    mainFrame.BackgroundColor3 = theme.main
    mainFrame.BorderSizePixel = 0
    mainFrame.Parent = window

    -- Cabeçalho
    local header = Instance.new("Frame")
    header.Size = UDim2.new(1, 0, 0, 40)
    header.BackgroundColor3 = theme.secondary
    header.Parent = mainFrame

    local title = Instance.new("TextLabel")
    title.Text = name
    title.TextColor3 = theme.text
    title.Font = Enum.Font.GothamBold
    title.TextSize = 16
    title.BackgroundTransparency = 1
    title.Size = UDim2.new(1, -80, 1, 0)
    title.Position = UDim2.new(0, 10, 0, 0)
    title.Parent = header

    -- Botões de fechar/minimizar
    local closeButton = Instance.new("TextButton")
    closeButton.Text = "×"
    closeButton.TextColor3 = Color3.new(1, 0, 0)
    closeButton.Font = Enum.Font.GothamBold
    closeButton.TextSize = 20
    closeButton.BackgroundTransparency = 1
    closeButton.Size = UDim2.new(0, 30, 1, 0)
    closeButton.Position = UDim2.new(1, -30, 0, 0)
    closeButton.Parent = header

    local minimizeButton = Instance.new("TextButton")
    minimizeButton.Text = "−"
    minimizeButton.TextColor3 = Color3.new(1, 1, 0)
    minimizeButton.Font = Enum.Font.GothamBold
    minimizeButton.TextSize = 24
    minimizeButton.BackgroundTransparency = 1
    minimizeButton.Size = UDim2.new(0, 30, 1, 0)
    minimizeButton.Position = UDim2.new(1, -60, 0, 0)
    minimizeButton.Parent = header

    -- Container para abas e conteúdo
    local tabContainer = Instance.new("Frame")
    tabContainer.Size = UDim2.new(0, 150, 1, -40)
    tabContainer.Position = UDim2.new(0, 0, 0, 40)
    tabContainer.BackgroundTransparency = 1
    tabContainer.Parent = mainFrame

    local contentFrame = Instance.new("Frame")
    contentFrame.Size = UDim2.new(1, -150, 1, -40)
    contentFrame.Position = UDim2.new(0, 150, 0, 40)
    contentFrame.BackgroundColor3 = theme.secondary
    contentFrame.Parent = mainFrame

    -- Configurar cantos arredondados
    for _, frame in ipairs({mainFrame, header, contentFrame}) do
        local corner = Instance.new("UICorner")
        corner.CornerRadius = theme.cornerRadius
        corner.Parent = frame
    end

    -- Função para criar abas
    function Library:CreateTab(icon, gameName)
        local tabButton = Instance.new("TextButton")
        tabButton.Text = ""
        tabButton.BackgroundTransparency = 1
        tabButton.Size = UDim2.new(1, 0, 0, 40)
        tabButton.Parent = tabContainer

        local iconLabel = Instance.new("ImageLabel")
        iconLabel.Image = icon
        iconLabel.Size = UDim2.new(0, 32, 0, 32)
        iconLabel.Position = UDim2.new(0, 10, 0.5, -16)
        iconLabel.BackgroundTransparency = 1
        iconLabel.Parent = tabButton

        local tabName = Instance.new("TextLabel")
        tabName.Text = gameName
        tabName.TextColor3 = theme.text
        tabName.Font = Enum.Font.Gotham
        tabName.TextSize = 14
        tabName.BackgroundTransparency = 1
        tabName.Position = UDim2.new(0, 50, 0.5, -7)
        tabName.Size = UDim2.new(1, -60, 0, 14)
        tabName.Parent = tabButton

        -- Conteúdo da aba
        local tabContent = Instance.new("ScrollingFrame")
        tabContent.Size = UDim2.new(1, 0, 1, 0)
        tabContent.Position = UDim2.new(0, 0, 0, 40)
        tabContent.BackgroundTransparency = 1
        tabContent.CanvasSize = UDim2.new(0, 0, 0, 0)
        tabContent.ScrollBarThickness = 5
        tabContent.Visible = false
        tabContent.Parent = contentFrame

        local listLayout = Instance.new("UIListLayout")
        listLayout.Padding = UDim.new(0, 10)
        listLayout.Parent = tabContent

        -- Barra de pesquisa dentro do conteúdo da aba
        local searchBar = Instance.new("TextBox")
        searchBar.PlaceholderText = "Pesquisar scripts..."
        searchBar.Text = ""
        searchBar.TextColor3 = theme.text
        searchBar.Font = Enum.Font.Gotham
        searchBar.TextSize = 14
        searchBar.BackgroundTransparency = 0.2
        searchBar.BackgroundColor3 = theme.secondary
        searchBar.Size = UDim2.new(1, -20, 0, 30)
        searchBar.Position = UDim2.new(0, 10, 0, 5)
        searchBar.Parent = contentFrame

        -- Função para adicionar scripts à aba
        local function addScript(name, callback)
            local scriptButton = Instance.new("TextButton")
            scriptButton.Text = name
            scriptButton.TextColor3 = theme.text
            scriptButton.Font = Enum.Font.GothamSemibold
            scriptButton.TextSize = 14
            scriptButton.BackgroundColor3 = theme.accent
            scriptButton.Size = UDim2.new(1, -20, 0, 35)
            scriptButton.Parent = tabContent

            -- Efeito de hover
            scriptButton.MouseEnter:Connect(function()
                scriptButton.BackgroundColor3 = theme.accent:Lerp(Color3.new(1,1,1), 0.2)
            end)
            scriptButton.MouseLeave:Connect(function()
                scriptButton.BackgroundColor3 = theme.accent
            end)

            scriptButton.MouseButton1Click:Connect(callback)
        end

        -- Sistema de pesquisa
        searchBar:GetPropertyChangedSignal("Text"):Connect(function()
            local search = string.lower(searchBar.Text)
            for _, child in ipairs(tabContent:GetChildren()) do
                if child:IsA("TextButton") then
                    if search == "" or string.find(string.lower(child.Text), search) then
                        child.Visible = true
                    else
                        child.Visible = false
                    end
                end
            end
        end)

        -- Mostrar aba ao clicar
        tabButton.MouseButton1Click:Connect(function()
            for _, child in ipairs(contentFrame:GetChildren()) do
                if child:IsA("ScrollingFrame") then
                    child.Visible = false
                end
            end
            tabContent.Visible = true
        end)

        return {
            AddScript = addScript
        }
    end

    -- Fechar/minimizar
    closeButton.MouseButton1Click:Connect(function()
        window:Destroy()
    end)

    minimizeButton.MouseButton1Click:Connect(function()
        mainFrame.Visible = not mainFrame.Visible
    end)

    return Library
end

return Library
