local Library = {}

function Library:CreateSearchBar(parent)
    local searchBar = Instance.new("Frame")
    searchBar.Size = UDim2.new(0, 400, 0, 40)
    searchBar.BackgroundTransparency = 0.1
    searchBar.BackgroundColor3 = Color3.fromRGB(40, 40, 40)
    searchBar.BorderSizePixel = 0
    searchBar.Parent = parent

    -- Efeito de canto arredondado
    local corner = Instance.new("UICorner")
    corner.CornerRadius = UDim.new(0, 8)
    corner.Parent = searchBar

    -- Caixa de texto
    local searchBox = Instance.new("TextBox")
    searchBox.PlaceholderText = "Pesquisar..."
    searchBox.Text = ""
    searchBox.TextColor3 = Color3.new(1, 1, 1)
    searchBox.Font = Enum.Font.Gotham
    searchBox.TextSize = 14
    searchBox.BackgroundTransparency = 1
    searchBox.Size = UDim2.new(1, -40, 1, 0)
    searchBox.Position = UDim2.new(0, 10, 0, 0)
    searchBox.Parent = searchBar

    -- Botão de limpar (opcional)
    local clearButton = Instance.new("TextButton")
    clearButton.Text = "×"
    clearButton.TextColor3 = Color3.new(1, 1, 1)
    clearButton.Font = Enum.Font.GothamBold
    clearButton.TextSize = 18
    clearButton.BackgroundTransparency = 1
    clearButton.Size = UDim2.new(0, 24, 1, 0)
    clearButton.Position = UDim2.new(1, -34, 0, 0)
    clearButton.Parent = searchBar

    -- Frame para resultados da pesquisa
    local resultsFrame = Instance.new("ScrollingFrame")
    resultsFrame.Size = UDim2.new(1, 0, 0, 200)
    resultsFrame.Position = UDim2.new(0, 0, 1, 5)
    resultsFrame.BackgroundTransparency = 0.2
    resultsFrame.BackgroundColor3 = Color3.fromRGB(30, 30, 30)
    resultsFrame.BorderSizePixel = 0
    resultsFrame.Visible = false
    resultsFrame.Parent = searchBar

    local resultsList = Instance.new("UIListLayout")
    resultsList.Padding = UDim.new(0, 5)
    resultsList.Parent = resultsFrame

    -- Função de pesquisa
    local function updateResults(text)
        -- Limpa resultados anteriores
        for _, child in ipairs(resultsFrame:GetChildren()) do
            if child:IsA("TextButton") then
                child:Destroy()
            end
        end

        -- Simula resultados (substitua pela sua lógica)
        if text ~= "" then
            for i = 1, 5 do
                local resultBtn = Instance.new("TextButton")
                resultBtn.Text = "Resultado " .. i
                resultBtn.TextColor3 = Color3.new(1, 1, 1)
                resultBtn.BackgroundColor3 = Color3.fromRGB(45, 45, 45)
                resultBtn.Size = UDim2.new(1, 0, 0, 30)
                resultBtn.Parent = resultsFrame

                -- Efeito de hover
                resultBtn.MouseEnter:Connect(function()
                    resultBtn.BackgroundColor3 = Color3.fromRGB(60, 60, 60)
                end)
                resultBtn.MouseLeave:Connect(function()
                    resultBtn.BackgroundColor3 = Color3.fromRGB(45, 45, 45)
                end)
            end
            resultsFrame.Visible = true
        else
            resultsFrame.Visible = false
        end
    end

    -- Eventos
    searchBox:GetPropertyChangedSignal("Text"):Connect(function()
        updateResults(searchBox.Text)
    end)

    clearButton.MouseButton1Click:Connect(function()
        searchBox.Text = ""
        updateResults("")
    end)

    return searchBar
end

return Library
