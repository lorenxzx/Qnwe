local Library = {}
Library.Elements = {}

local theme = {
    main = Color3.fromRGB(25, 25, 25),
    secondary = Color3.fromRGB(35, 35, 35),
    accent = Color3.fromRGB(92, 164, 255),
    text = Color3.new(1, 1, 1),
    cornerRadius = UDim.new(0, 12),
    shadow = Color3.fromRGB(0, 0, 0),
    font = Enum.Font.GothamSemibold
}

function Library:CreateWindow(name)
    local window = Instance.new("ScreenGui")
    window.Name = "HubWindow"
    window.Parent = game.CoreGui

    local mainFrame = Instance.new("Frame")
    mainFrame.Size = UDim2.new(0, 700, 0, 450)
    mainFrame.Position = UDim2.new(0.5, -350, 0.5, -225)
    mainFrame.BackgroundColor3 = theme.main
    mainFrame.ClipsDescendants = true
    mainFrame.Parent = window

    -- Sombra do fundo
    local shadow = Instance.new("ImageLabel")
    shadow.Image = "rbxassetid://1316045217"
    shadow.ImageColor3 = theme.shadow
    shadow.ImageTransparency = 0.8
    shadow.BackgroundTransparency = 1
    shadow.Size = UDim2.new(1, 20, 1, 20)
    shadow.Position = UDim2.new(0, -10, 0, -10)
    shadow.ZIndex = 0
    shadow.Parent = mainFrame

    -- Cabeçalho
    local header = Instance.new("Frame")
    header.Size = UDim2.new(1, 0, 0, 50)
    header.BackgroundColor3 = theme.secondary
    header.Parent = mainFrame

    local title = Instance.new("TextLabel")
    title.Text = name
    title.TextColor3 = theme.text
    title.Font = theme.font
    title.TextSize = 18
    title.BackgroundTransparency = 1
    title.Size = UDim2.new(1, -120, 1, 0)
    title.Position = UDim2.new(0, 20, 0, 0)
    title.Parent = header

    -- Botões de controle
    local closeButton = Instance.new("TextButton")
    closeButton.Text = "×"
    closeButton.TextColor3 = Color3.new(1, 0, 0)
    closeButton.Font = Enum.Font.GothamBold
    closeButton.TextSize = 22
    closeButton.BackgroundTransparency = 1
    closeButton.Size = UDim2.new(0, 40, 1, 0)
    closeButton.Position = UDim2.new(1, -40, 0, 0)
    closeButton.Parent = header

    local minimizeButton = Instance.new("TextButton")
    minimizeButton.Text = "−"
    minimizeButton.TextColor3 = Color3.new(1, 1, 0)
    minimizeButton.Font = Enum.Font.GothamBold
    minimizeButton.TextSize = 26
    minimizeButton.BackgroundTransparency = 1
    minimizeButton.Size = UDim2.new(0, 40, 1, 0)
    minimizeButton.Position = UDim2.new(1, -80, 0, 0)
    minimizeButton.Parent = header

    -- Container de abas
    local tabContainer = Instance.new("ScrollingFrame")
    tabContainer.Size = UDim2.new(0, 180, 1, -50)
    tabContainer.Position = UDim2.new(0, 0, 0, 50)
    tabContainer.BackgroundTransparency = 1
    tabContainer.CanvasSize = UDim2.new(0, 0, 0, 0)
    tabContainer.ScrollBarThickness = 0
    tabContainer.Parent = mainFrame

    local tabList = Instance.new("UIListLayout")
    tabList.Padding = UDim.new(0, 5)
    tabList.SortOrder = Enum.SortOrder.LayoutOrder
    tabList.Parent = tabContainer

    -- Conteúdo das abas
    local contentFrame = Instance.new("Frame")
    contentFrame.Size = UDim2.new(1, -180, 1, -50)
    contentFrame.Position = UDim2.new(0, 180, 0, 50)
    contentFrame.BackgroundTransparency = 1
    contentFrame.Parent = mainFrame

    -- Cantos arredondados
    for _, frame in ipairs({mainFrame, header}) do
        local corner = Instance.new("UICorner")
        corner.CornerRadius = theme.cornerRadius
        corner.Parent = frame
    end

    -- Função para criar abas
    function Library:CreateTab(icon, gameName)
        local tabButton = Instance.new("TextButton")
        tabButton.Text = ""
        tabButton.BackgroundTransparency = 1
        tabButton.Size = UDim2.new(1, -10, 0, 50)
        tabButton.Parent = tabContainer

        local iconLabel = Instance.new("ImageLabel")
        iconLabel.Image = icon
        iconLabel.Size = UDim2.new(0, 36, 0, 36)
        iconLabel.Position = UDim2.new(0, 10, 0.5, -18)
        iconLabel.BackgroundTransparency = 1
        iconLabel.Parent = tabButton

        local tabName = Instance.new("TextLabel")
        tabName.Text = gameName
        tabName.TextColor3 = theme.text
        tabName.Font = theme.font
        tabName.TextSize = 14
        tabName.BackgroundTransparency = 1
        tabName.Position = UDim2.new(0, 55, 0.5, -7)
        tabName.Size = UDim2.new(1, -65, 0, 14)
        tabName.Parent = tabButton

        -- Efeito de seleção
        local selection = Instance.new("Frame")
        selection.Size = UDim2.new(0, 4, 1, 0)
        selection.BackgroundColor3 = theme.accent
        selection.Visible = false
        selection.Parent = tabButton

        local selectionCorner = Instance.new("UICorner")
        selectionCorner.CornerRadius = UDim.new(1, 0)
        selectionCorner.Parent = selection

        -- Conteúdo da aba
        local tabContent = Instance.new("ScrollingFrame")
        tabContent.Size = UDim2.new(1, -20, 1, -20)
        tabContent.Position = UDim2.new(0, 10, 0, 10)
        tabContent.BackgroundTransparency = 1
        tabContent.CanvasSize = UDim2.new(0, 0, 0, 0)
        tabContent.ScrollBarThickness = 5
        tabContent.Visible = false
        tabContent.Parent = contentFrame

        local contentLayout = Instance.new("UIListLayout")
        contentLayout.Padding = UDim.new(0, 10)
        contentLayout.SortOrder = Enum.SortOrder.LayoutOrder
        contentLayout.Parent = tabContent

        -- Barra de pesquisa
        local searchFrame = Instance.new("Frame")
        searchFrame.Size = UDim2.new(1, 0, 0, 40)
        searchFrame.BackgroundTransparency = 1
        searchFrame.Parent = tabContent

        local searchBar = Instance.new("TextBox")
        searchBar.PlaceholderText = "Pesquisar scripts..."
        searchBar.Text = ""
        searchBar.TextColor3 = theme.text
        searchBar.Font = theme.font
        searchBar.TextSize = 14
        searchBar.BackgroundColor3 = theme.secondary
        searchBar.Size = UDim2.new(1, -40, 1, 0)
        searchBar.Position = UDim2.new(0, 10, 0, 0)
        searchBar.Parent = searchFrame

        local searchCorner = Instance.new("UICorner")
        searchCorner.CornerRadius = UDim.new(0, 6)
        searchCorner.Parent = searchBar

        local clearButton = Instance.new("TextButton")
        clearButton.Text = "×"
        clearButton.TextColor3 = Color3.new(1, 0, 0)
        clearButton.Font = Enum.Font.GothamBold
        clearButton.TextSize = 18
        clearButton.BackgroundTransparency = 1
        clearButton.Size = UDim2.new(0, 30, 1, 0)
        clearButton.Position = UDim2.new(1, -30, 0, 0)
        clearButton.Parent = searchBar

        -- Função para adicionar scripts
        local function addScript(name, callback)
            local scriptButton = Instance.new("TextButton")
            scriptButton.Text = name
            scriptButton.TextColor3 = theme.text
            scriptButton.Font = theme.font
            scriptButton.TextSize = 14
            scriptButton.BackgroundColor3 = theme.secondary
            scriptButton.Size = UDim2.new(1, 0, 0, 40)
            scriptButton.Parent = tabContent

            local btnCorner = Instance.new("UICorner")
            btnCorner.CornerRadius = UDim.new(0, 8)
            btnCorner.Parent = scriptButton

            -- Efeito de hover
            scriptButton.MouseEnter:Connect(function()
                scriptButton.BackgroundColor3 = theme.accent:Lerp(Color3.new(1,1,1), 0.15)
            end)
            scriptButton.MouseLeave:Connect(function()
                scriptButton.BackgroundColor3 = theme.secondary
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

        -- Lógica de seleção de aba
        tabButton.MouseButton1Click:Connect(function()
            for _, tab in ipairs(tabContainer:GetChildren()) do
                if tab:IsA("TextButton") then
                    tab.selection.Visible = false
                end
            end
            for _, content in ipairs(contentFrame:GetChildren()) do
                if content:IsA("ScrollingFrame") then
                    content.Visible = false
                end
            end
            tabContent.Visible = true
            selection.Visible = true
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
