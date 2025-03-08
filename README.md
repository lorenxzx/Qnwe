-- // UI Library (ModuleScript)
local Library = {}
Library.__index = Library

-- Tema padrão (cores modernas)
Library.Theme = {
    Primary = Color3.fromRGB(35, 35, 35),
    Secondary = Color3.fromRGB(45, 45, 45),
    Accent = Color3.fromRGB(90, 160, 255),
    Text = Color3.fromRGB(255, 255, 255),
    Background = Color3.fromRGB(25, 25, 25),
    BorderRadius = UDim.new(0, 10),
    Padding = 10,
    Shadow = {
        Color = Color3.fromRGB(0, 0, 0),
        Transparency = 0.5,
        Offset = Vector2.new(3, 3)
    }
}

-- Configurações de animação
Library.Animation = {
    Smoothness = 0.2,  -- 0 = instantâneo, 1 = lento
    Bounce = false
}

-- Inicializa a biblioteca
function Library.new()
    local self = setmetatable({}, Library)
    self.ScreenGui = Instance.new("ScreenGui")
    self.ScreenGui.IgnoreGuiInset = true
    self.ScreenGui.ResetOnSpawn = false
    return self
end

-- Cria uma janela com efeito de arrastar e animação
function Library:CreateWindow(title, size)
    local window = {
        Components = {},
        Connections = {}
    }

    -- Container principal
    local container = self:Create("Frame", {
        Size = size or UDim2.new(0, 500, 0, 350),
        Position = UDim2.new(0.5, -250, 0.5, -175),
        BackgroundColor3 = self.Theme.Primary,
        BorderSizePixel = 0,
        ClipsDescendants = true
    }, self.ScreenGui)

    -- Sombra
    self:Create("UIStroke", {
        Color = self.Theme.Shadow.Color,
        Thickness = 2,
        Transparency = self.Theme.Shadow.Transparency,
        ApplyStrokeMode = Enum.ApplyStrokeMode.Border
    }, container)

    -- Barra de título
    local titleBar = self:Create("Frame", {
        Size = UDim2.new(1, 0, 0, 45),
        BackgroundColor3 = self.Theme.Secondary
    }, container)

    -- Título
    self:Create("TextLabel", {
        Text = title,
        Font = Enum.Font.GothamBold,
        TextColor3 = self.Theme.Text,
        TextSize = 18,
        Size = UDim2.new(1, 0, 1, 0),
        BackgroundTransparency = 1
    }, titleBar)

    -- Botão de fechar
    local closeButton = self:Create("TextButton", {
        Text = "×",
        Font = Enum.Font.GothamBold,
        TextColor3 = self.Theme.Text,
        TextSize = 24,
        Size = UDim2.new(0, 45, 1, 0),
        Position = UDim2.new(1, -45, 0, 0),
        BackgroundTransparency = 1
    }, titleBar)

    -- Container de conteúdo
    local content = self:Create("ScrollingFrame", {
        Size = UDim2.new(1, 0, 1, -45),
        Position = UDim2.new(0, 0, 0, 45),
        BackgroundColor3 = self.Theme.Background,
        ScrollBarThickness = 5,
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
        self:Tween(closeButton, {TextColor3 = self.Theme.Accent}, 0.2)
    end)
    closeButton.MouseLeave:Connect(function()
        self:Tween(closeButton, {TextColor3 = self.Theme.Text}, 0.2)
    end)

    -- Fecha a janela
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

    window.AddLabel = function(_, text)
        local label = self:CreateLabel(content, text)
        return label
    end

    return window
end

-- Cria um botão estilizado
function Library:CreateButton(parent, text, callback)
    local button = self:Create("TextButton", {
        Text = text,
        Font = Enum.Font.GothamSemibold,
        TextColor3 = self.Theme.Text,
        TextSize = 16,
        Size = UDim2.new(1, -20, 0, 40),
        BackgroundColor3 = self.Theme.Secondary,
        Position = UDim2.new(0, 10, 0, 10)
    }, parent)

    button.MouseButton1Click:Connect(function()
        self:Tween(button, {BackgroundColor3 = self.Theme.Accent}, 0.1)
        callback()
        self:Tween(button, {BackgroundColor3 = self.Theme.Secondary}, 0.1)
    end)

    return button
end

-- Cria um toggle
function Library:CreateToggle(parent, text, default, callback)
    local toggle = {
        Value = default,
        Button = self:Create("TextButton", {
            Text = text,
            Font = Enum.Font.GothamSemibold,
            TextColor3 = self.Theme.Text,
            TextSize = 16,
            Size = UDim2.new(1, -20, 0, 40),
            BackgroundColor3 = self.Theme.Secondary,
            Position = UDim2.new(0, 10, 0, 10)
        }, parent)
    }

    local indicator = self:Create("Frame", {
        Size = UDim2.new(0, 20, 0, 20),
        Position = UDim2.new(1, -30, 0, 10),
        BackgroundColor3 = self.Theme.Accent,
        AnchorPoint = Vector2.new(1, 0)
    }, toggle.Button)

    if not default then
        indicator.BackgroundColor3 = Color3.fromRGB(100, 100, 100)
    end

    toggle.Button.MouseButton1Click:Connect(function()
        toggle.Value = not toggle.Value
        self:Tween(indicator, {
            BackgroundColor3 = toggle.Value and self.Theme.Accent or Color3.fromRGB(100, 100, 100)
        }, 0.2)
        callback(toggle.Value)
    end)

    return toggle
end

-- Cria uma label simples
function Library:CreateLabel(parent, text)
    return self:Create("TextLabel", {
        Text = text,
        Font = Enum.Font.Gotham,
        TextColor3 = self.Theme.Text,
        TextSize = 14,
        BackgroundTransparency = 1,
        Size = UDim2.new(1, -20, 0, 30),
        Position = UDim2.new(0, 10, 0, 10)
    }, parent)
end

-- Função auxiliar para criar instâncias
function Library:Create(className, properties, parent)
    local instance = Instance.new(className)
    for prop, value in pairs(properties) do
        instance[prop] = value
    end
    instance.Parent = parent
    return instance
end

-- Sistema de animação suave
function Library:Tween(instance, properties, duration, callback)
    local tweenInfo = TweenInfo.new(
        duration or 0.5,
        Enum.EasingStyle.Quad,
        Enum.EasingDirection.Out
    )
    local tween = game:GetService("TweenService"):Create(instance, tweenInfo, properties)
    tween.Completed:Connect(function()
        if callback then callback() end
    end)
    tween:Play()
    return tween
end

return Library
