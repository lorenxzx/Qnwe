-- // Aurora UI Library (ModuleScript)
local Aurora = {}
Aurora.__index = Aurora

-- Tema personalizável (cores profissionais)
Aurora.Theme = {
    Primary = Color3.fromRGB(30, 30, 30),
    Secondary = Color3.fromRGB(40, 40, 40),
    Accent = Color3.fromRGB(100, 150, 255),
    Text = Color3.fromRGB(255, 255, 255),
    Success = Color3.fromRGB(0, 200, 100),
    Error = Color3.fromRGB(255, 100, 100),
    Background = Color3.fromRGB(20, 20, 20),
    BorderRadius = UDim.new(0, 12),
    Padding = 15,
    Shadow = {
        Color = Color3.fromRGB(0, 0, 0),
        Transparency = 0.3,
        Offset = Vector2.new(4, 4),
        Blur = 8
    }
}

-- Configurações de animação
Aurora.Animation = {
    Smoothness = 0.25,
    Bounce = true
}

-- Inicializa a biblioteca
function Aurora.new()
    local self = setmetatable({}, Aurora)
    self.ScreenGui = Instance.new("ScreenGui")
    self.ScreenGui.IgnoreGuiInset = true
    self.ScreenGui.ResetOnSpawn = false
    return self
end

-- Cria uma janela com efeito de arrastar, sombra e animação
function Aurora:CreateWindow(title, size)
    local window = {
        Components = {},
        SearchResults = {}
    }

    -- Container principal
    local container = self:Create("Frame", {
        Size = size or UDim2.new(0, 600, 0, 450),
        Position = UDim2.new(0.5, -300, 0.5, -225),
        BackgroundColor3 = self.Theme.Primary,
        BorderSizePixel = 0,
        ClipsDescendants = true
    }, self.ScreenGui)

    -- Sombra com gradiente
    self:Create("UIStroke", {
        Color = self.Theme.Shadow.Color,
        Thickness = 2,
        Transparency = self.Theme.Shadow.Transparency,
        ApplyStrokeMode = Enum.ApplyStrokeMode.Border
    }, container)

    -- Barra de título
    local titleBar = self:Create("Frame", {
        Size = UDim2.new(1, 0, 0, 60),
        BackgroundColor3 = self.Theme.Secondary
    }, container)

    -- Título
    self:Create("TextLabel", {
        Text = title,
        Font = Enum.Font.GothamBold,
        TextColor3 = self.Theme.Text,
        TextSize = 20,
        Size = UDim2.new(1, 0, 1, 0),
        BackgroundTransparency = 1
    }, titleBar)

    -- Botão de fechar
    local closeButton = self:Create("ImageButton", {
        Image = "rbxassetid://10307049535",  -- Ícone de X moderno
        ImageColor3 = self.Theme.Text,
        Size = UDim2.new(0, 35, 0, 35),
        Position = UDim2.new(1, -40, 0, 12),
        BackgroundTransparency = 1
    }, titleBar)

    -- Barra de pesquisa
    local searchBox = self:CreateSearchBar(container)

    -- Container de conteúdo com Scroll
    local content = self:Create("ScrollingFrame", {
        Size = UDim2.new(1, 0, 1, -100),
        Position = UDim2.new(0, 0, 0, 60),
        BackgroundColor3 = self.Theme.Background,
        ScrollBarThickness = 6,
        CanvasSize = UDim2.new(0, 0, 0, 0)
    }, container)

    -- Configurações de arrastar
    local dragging = false
    local dragStart = Vector2.new()
    local startPos = Vector2.new()

    titleBar.InputBegan:Connect(function(input)
        if input.UserInputType == Enum.UserInputType.MouseButton1 then
            dragging = true
            dragStart = input.Position
            startPos = container.Position
        end
    end)

    titleBar.InputChanged:Connect(function(input)
        if input.UserInputType == Enum.UserInputType.MouseMovement and dragging then
            local delta = input.Position - dragStart
            local newPosition = startPos + UDim2.new(0, delta.X, 0, delta.Y)
            self:Tween(container, {Position = newPosition}, 0.1)
        end
    end)

    titleBar.InputEnded:Connect(function(input)
        if input.UserInputType == Enum.UserInputType.MouseButton1 then
            dragging = false
        end
    end)

    -- Animação de hover no botão de fechar
    closeButton.MouseEnter:Connect(function()
        self:Tween(closeButton, {ImageColor3 = self.Theme.Accent}, 0.2)
    end)
    closeButton.MouseLeave:Connect(function()
        self:Tween(closeButton, {ImageColor3 = self.Theme.Text}, 0.2)
    end)

    -- Fecha a janela com animação
    closeButton.MouseButton1Click:Connect(function()
        self:Tween(container, {Size = UDim2.new(0, 0, 0, 0)}, 0.3, function()
            container:Destroy()
        end)
    end)

    -- Métodos da janela
    window.AddButton = function(_, text, callback)
        local button = self:CreateButton(content, text, callback)
        return button
    end

    window.AddToggle = function(_, text, default, callback)
        local toggle = self:CreateToggle(content, text, default, callback)
        return toggle
    end

    window.AddSearchBar = function(_)
        return searchBox
    end

    return window
end

-- Cria uma barra de pesquisa estilizada
function Aurora:CreateSearchBar(parent)
    local searchBar = {
        Value = "",
        Active = true
    }

    -- Container da barra
    local searchContainer = self:Create("Frame", {
        Size = UDim2.new(1, -40, 0, 45),
        Position = UDim2.new(0, 20, 0, 20),
        BackgroundColor3 = self.Theme.Secondary,
        BorderSizePixel = 0
    }, parent)

    -- Caixa de texto
    local textBox = self:Create("TextBox", {
        Text = "",
        PlaceholderText = "Pesquisar...",
        Font = Enum.Font.Gotham,
        TextColor3 = self.Theme.Text,
        TextSize = 16,
        BackgroundTransparency = 1,
        Size = UDim2.new(1, -40, 1, 0),
        Position = UDim2.new(0, 15, 0, 0),
        ClearTextOnFocus = false
    }, searchContainer)

    -- Botão de limpar
    local clearButton = self:Create("ImageButton", {
        Image = "rbxassetid://10307049535",  -- Ícone de X
        ImageColor3 = self.Theme.Text,
        Size = UDim2.new(0, 25, 0, 25),
        Position = UDim2.new(1, -30, 0, 10),
        BackgroundTransparency = 1
    }, searchContainer)

    -- Efeito de foco
    textBox.Focused:Connect(function()
        self:Tween(searchContainer, {BackgroundColor3 = self.Theme.Accent}, 0.2)
    end)
    textBox.FocusLost:Connect(function()
        self:Tween(searchContainer, {BackgroundColor3 = self.Theme.Secondary}, 0.2)
    end)

    -- Atualiza o valor
    textBox:GetPropertyChangedSignal("Text"):Connect(function()
        searchBar.Value = textBox.Text
    end)

    -- Limpa o texto
    clearButton.MouseButton1Click:Connect(function()
        textBox.Text = ""
    end)

    return searchBar
end

-- Cria um botão com efeito de clique
function Aurora:CreateButton(parent, text, callback)
    local button = self:Create("TextButton", {
        Text = text,
        Font = Enum.Font.GothamSemibold,
        TextColor3 = self.Theme.Text,
        TextSize = 16,
        Size = UDim2.new(1, -40, 0, 50),
        Position = UDim2.new(0, 20, 0, 10),
        BackgroundColor3 = self.Theme.Secondary,
        AutoButtonColor = false
    }, parent)

    -- Efeito de hover
    button.MouseEnter:Connect(function()
        self:Tween(button, {BackgroundColor3 = self.Theme.Accent}, 0.2)
    end)
    button.MouseLeave:Connect(function()
        self:Tween(button, {BackgroundColor3 = self.Theme.Secondary}, 0.2)
    end)

    -- Efeito de clique
    button.MouseButton1Down:Connect(function()
        self:Tween(button, {BackgroundColor3 = Color3.fromRGB(80, 80, 80)}, 0.1)
    end)
    button.MouseButton1Up:Connect(function()
        self:Tween(button, {BackgroundColor3 = self.Theme.Accent}, 0.1)
        callback()
    end)

    return button
end

-- Cria um toggle com animação
function Aurora:CreateToggle(parent, text, default, callback)
    local toggle = {
        Value = default,
        Button = self:Create("TextButton", {
            Text = text,
            Font = Enum.Font.GothamSemibold,
            TextColor3 = self.Theme.Text,
            TextSize = 16,
            Size = UDim2.new(1, -40, 0, 50),
            Position = UDim2.new(0, 20, 0, 10),
            BackgroundColor3 = self.Theme.Secondary,
            AutoButtonColor = false
        }, parent)
    }

    -- Indicador de toggle
    local indicator = self:Create("Frame", {
        Size = UDim2.new(0, 30, 0, 30),
        Position = UDim2.new(1, -40, 0, 10),
        BackgroundColor3 = self.Theme.Accent,
        BorderSizePixel = 0
    }, toggle.Button)

    -- Animação inicial
    if not default then
        indicator.BackgroundColor3 = Color3.fromRGB(60, 60, 60)
    end

    -- Efeito de clique
    toggle.Button.MouseButton1Click:Connect(function()
        toggle.Value = not toggle.Value
        self:Tween(indicator, {
            BackgroundColor3 = toggle.Value and self.Theme.Accent or Color3.fromRGB(60, 60, 60)
        }, 0.2)
        callback(toggle.Value)
    end)

    return toggle
end

-- Função auxiliar para criar instâncias
function Aurora:Create(className, properties, parent)
    local instance = Instance.new(className)
    for prop, value in pairs(properties) do
        instance[prop] = value
    end
    instance.Parent = parent
    return instance
end

-- Sistema de animação avançado
function Aurora:Tween(instance, properties, duration, callback)
    local tweenInfo = TweenInfo.new(
        duration or 0.5,
        self.Animation.Bounce and Enum.EasingStyle.Elastic or Enum.EasingStyle.Quad,
        Enum.EasingDirection.Out
    )
    local tween = game:GetService("TweenService"):Create(instance, tweenInfo, properties)
    tween.Completed:Connect(function()
        if callback then callback() end
    end)
    tween:Play()
    return tween
end

return Aurora
