--[[
    Aurora UI Library
    Documentação:

    1. Inicialização:
        local ui = require(game.ReplicatedStorage.AuroraUI).new()

    2. Criar janela:
        local window = ui:CreateWindow("Título", UDim2.new(0, 600, 0, 400))

    3. Adicionar componentes:
        window:AddButton("Texto do Botão", function()
            print("Clicado!")
        end)

        local toggle = window:AddToggle("Texto do Toggle", false, function(value)
            print("Toggle:", value)
        end)

        local search = window:AddSearch()

    4. Acessar valores:
        print("Pesquisa atual:", search:GetText())
]]

local AuroraUI = {}
AuroraUI.__index = AuroraUI

-- Tema padrão
AuroraUI.Theme = {
    Background = Color3.fromRGB(30, 30, 30),
    Secondary = Color3.fromRGB(40, 40, 40),
    Accent = Color3.fromRGB(0, 162, 255),
    Text = Color3.fromRGB(255, 255, 255),
    BorderRadius = UDim.new(0, 10),
    Padding = 10
}

-- Inicializa a interface
function AuroraUI.new()
    local self = setmetatable({}, AuroraUI)
    
    -- Cria ScreenGui
    self.ScreenGui = Instance.new("ScreenGui")
    self.ScreenGui.IgnoreGuiInset = true
    self.ScreenGui.ResetOnSpawn = false
    self.ScreenGui.Parent = game.Players.LocalPlayer:WaitForChild("PlayerGui")
    
    return self
end

-- Cria uma janela
function AuroraUI:CreateWindow(title, size)
    local window = {}

    -- Container principal
    local container = Instance.new("Frame")
    container.Size = size or UDim2.new(0, 600, 0, 400)
    container.Position = UDim2.new(0.5, -300, 0.5, -200)
    container.BackgroundTransparency = 0.2
    container.BackgroundColor3 = self.Theme.Background
    container.BorderSizePixel = 0
    container.ClipsDescendants = true
    container.Parent = self.ScreenGui

    -- Barra de título
    local titleBar = Instance.new("Frame")
    titleBar.Size = UDim2.new(1, 0, 0, 40)
    titleBar.BackgroundColor3 = self.Theme.Secondary
    titleBar.Parent = container

    -- Título
    local titleLabel = Instance.new("TextLabel")
    titleLabel.Text = title
    titleLabel.Font = Enum.Font.GothamBold
    titleLabel.TextColor3 = self.Theme.Text
    titleLabel.TextSize = 18
    titleLabel.BackgroundTransparency = 1
    titleLabel.Size = UDim2.new(1, 0, 1, 0)
    titleLabel.Parent = titleBar

    -- Botão de fechar
    local closeButton = Instance.new("TextButton")
    closeButton.Text = "×"
    closeButton.Font = Enum.Font.GothamBold
    closeButton.TextColor3 = self.Theme.Text
    closeButton.TextSize = 24
    closeButton.BackgroundTransparency = 1
    closeButton.Size = UDim2.new(0, 40, 1, 0)
    closeButton.Position = UDim2.new(1, -40, 0, 0)
    closeButton.Parent = titleBar

    -- Container de conteúdo
    local content = Instance.new("ScrollingFrame")
    content.Size = UDim2.new(1, 0, 1, -40)
    content.Position = UDim2.new(0, 0, 0, 40)
    content.BackgroundTransparency = 1
    content.ScrollBarThickness = 5
    content.CanvasSize = UDim2.new(0, 0, 0, 0)
    content.Parent = container

    -- Lista de componentes
    local componentList = Instance.new("UIListLayout")
    componentList.Padding = UDim.new(0, 10)
    componentList.Parent = content

    -- Função para atualizar o tamanho do canvas
    local function updateCanvas()
        content.CanvasSize = UDim2.new(0, 0, 0, componentList.AbsoluteContentSize.Y)
    end
    componentList.Changed:Connect(updateCanvas)

    -- Métodos da janela
    window.AddButton = function(_, text, callback)
        local button = Instance.new("TextButton")
        button.Text = text
        button.Font = Enum.Font.GothamSemibold
        button.TextColor3 = self.Theme.Text
        button.TextSize = 16
        button.Size = UDim2.new(1, -20, 0, 40)
        button.Position = UDim2.new(0, 10, 0, 10)
        button.BackgroundColor3 = self.Theme.Secondary
        button.Parent = content

        button.MouseButton1Click:Connect(callback)
        return button
    end

    window.AddToggle = function(_, text, default, callback)
        local toggle = {
            Value = default
        }

        local button = Instance.new("TextButton")
        button.Text = text
        button.Font = Enum.Font.GothamSemibold
        button.TextColor3 = self.Theme.Text
        button.TextSize = 16
        button.Size = UDim2.new(1, -20, 0, 40)
        button.Position = UDim2.new(0, 10, 0, 10)
        button.BackgroundColor3 = self.Theme.Secondary
        button.Parent = content

        local indicator = Instance.new("Frame")
        indicator.Size = UDim2.new(0, 20, 0, 20)
        indicator.Position = UDim2.new(1, -30, 0, 10)
        indicator.BackgroundColor3 = toggle.Value and self.Theme.Accent or Color3.fromRGB(100, 100, 100)
        indicator.Parent = button

        button.MouseButton1Click:Connect(function()
            toggle.Value = not toggle.Value
            indicator.BackgroundColor3 = toggle.Value and self.Theme.Accent or Color3.fromRGB(100, 100, 100)
            callback(toggle.Value)
        end)

        return toggle
    end

    window.AddSearch = function(_)
        local search = {
            Value = ""
        }

        local searchBox = Instance.new("TextBox")
        searchBox.PlaceholderText = "Pesquisar..."
        searchBox.Font = Enum.Font.Gotham
        searchBox.TextColor3 = self.Theme.Text
        searchBox.TextSize = 16
        searchBox.Size = UDim2.new(1, -20, 0, 40)
        searchBox.Position = UDim2.new(0, 10, 0, 10)
        searchBox.BackgroundColor3 = self.Theme.Secondary
        searchBox.Parent = content

        searchBox:GetPropertyChangedSignal("Text"):Connect(function()
            search.Value = searchBox.Text
        end)

        return search
    end

    -- Fecha a janela
    closeButton.MouseButton1Click:Connect(function()
        container:Destroy()
    end)

    return window
end

return AuroraUI
