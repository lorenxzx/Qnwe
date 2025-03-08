-- // Aurora Pro UI Library (ModuleScript)
local AuroraPro = {}
AuroraPro.__index = AuroraPro

-- Tema com efeitos de vidro (glassmorphism)
AuroraPro.Theme = {
    Primary = Color3.fromRGB(30, 30, 30),
    Secondary = Color3.fromRGB(40, 40, 40),
    Accent = Color3.fromRGB(100, 150, 255),
    Text = Color3.fromRGB(255, 255, 255),
    Background = Color3.fromRGB(20, 20, 20),
    GlassEffect = {
        Transparency = 0.2,
        Blur = 15
    },
    BorderRadius = UDim.new(0, 20),
    Padding = 20,
    Shadow = {
        Color = Color3.fromRGB(0, 0, 0),
        Transparency = 0.3,
        Offset = Vector2.new(4, 4),
        Blur = 8
    }
}

-- Configurações avançadas
AuroraPro.Animation = {
    Smoothness = 0.3,
    Bounce = true,
    ParticleCount = 10
}

-- Inicializa a biblioteca
function AuroraPro.new()
    local self = setmetatable({}, AuroraPro)
    self.ScreenGui = Instance.new("ScreenGui")
    self.ScreenGui.IgnoreGuiInset = true
    self.ScreenGui.ResetOnSpawn = false
    return self
end

-- Cria uma janela com efeito de vidro e sombras dinâmicas
function AuroraPro:CreateWindow(title, size)
    local window = {
        Components = {}
    }

    -- Container principal com efeito de vidro
    local container = self:Create("Frame", {
        Size = size or UDim2.new(0, 700, 0, 500),
        Position = UDim2.new(0.5, -350, 0.5, -250),
        BackgroundColor3 = self.Theme.Primary,
        BorderSizePixel = 0,
        ClipsDescendants = true
    }, self.ScreenGui)

    -- Efeito de vidro (glassmorphism)
    self:Create("UIGradient", {
        Color = ColorSequence.new({
            ColorSequenceKeypoint.new(0, Color3.fromRGB(0, 0, 0)),
            ColorSequenceKeypoint.new(1, Color3.fromRGB(255, 255, 255))
        }),
        Rotation = 90,
        Parent = container
    })
    local blur = self:Create("BlurEffect", {
        Size = self.Theme.GlassEffect.Blur,
        Parent = container
    })
    container.BackgroundTransparency = self.Theme.GlassEffect.Transparency

    -- Sombra dinâmica
    self:Create("UIStroke", {
        Color = self.Theme.Shadow.Color,
        Thickness = 2,
        Transparency = self.Theme.Shadow.Transparency,
        ApplyStrokeMode = Enum.ApplyStrokeMode.Border,
        Parent = container
    })

    -- Barra de título com ícone
    local titleBar = self:Create("Frame", {
        Size = UDim2.new(1, 0, 0, 60),
        BackgroundColor3 = self.Theme.Secondary,
        BackgroundTransparency = 0.3
    }, container)

    -- Ícone da janela
    local icon = self:Create("ImageLabel", {
        Image = "rbxassetid://10307049535", -- Ícone personalizado
        ImageColor3 = self.Theme.Text,
        Size = UDim2.new(0, 30, 0, 30),
        Position = UDim2.new(0, 20, 0, 15),
        Parent = titleBar
    })

    -- Título
    self:Create("TextLabel", {
        Text = title,
        Font = Enum.Font.GothamBold,
        TextColor3 = self.Theme.Text,
        TextSize = 22,
        Position = UDim2.new(0, 60, 0, 10),
        Size = UDim2.new(1, -80, 1, 0),
        BackgroundTransparency = 1
    }, titleBar)

    -- Botão de fechar com animação de partículas
    local closeButton = self:Create("ImageButton", {
        Image = "rbxassetid://10307049535",
        ImageColor3 = self.Theme.Text,
        Size = UDim2.new(0, 40, 0, 40),
        Position = UDim2.new(1, -60, 0, 10),
        BackgroundTransparency = 1,
        Parent = titleBar
    })

    -- Container de conteúdo
    local content = self:Create("ScrollingFrame", {
        Size = UDim2.new(1, 0, 1, -60),
        Position = UDim2.new(0, 0, 0, 60),
        BackgroundColor3 = self.Theme.Background,
        ScrollBarThickness = 8,
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
            self:Tween(container, {Position = newPosition}, 0.15)
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

    -- Fecha a janela com animação de partículas
    closeButton.MouseButton1Click:Connect(function()
        local particles = self:Create("ParticleEmitter", {
            Rate = 50,
            Lifetime = 2,
            Speed = 100,
            Size = 5,
            Color = self.Theme.Accent,
            Parent = container
        })
        self:Tween(container, {Size = UDim2.new(0, 0, 0, 0)}, 0.3, function()
            container:Destroy()
        end)
    end)

    -- Métodos da janela
    window.AddSearch = function(_) return self:CreateSearch(content) end
    window.AddSection = function(_) return self:CreateSection(content) end

    return window
end

-- Cria uma barra de pesquisa com filtragem
function AuroraPro:CreateSearch(parent)
    local search = {
        Value = "",
        Active = false
    }

    local container = self:Create("Frame", {
        Size = UDim2.new(1, -40, 0, 60),
        Position = UDim2.new(0, 20, 0, 20),
        BackgroundColor3 = self.Theme.Secondary,
        BackgroundTransparency = 0.3,
        Parent = parent
    })

    local textBox = self:Create("TextBox", {
        Text = "",
        PlaceholderText = "Pesquisar...",
        Font = Enum.Font.Gotham,
        TextColor3 = self.Theme.Text,
        TextSize = 18,
        Size = UDim2.new(1, -50, 1, 0),
        Position = UDim2.new(0, 20, 0, 0),
        Parent = container
    })

    local clearButton = self:Create("ImageButton", {
        Image = "rbxassetid://10307049535",
        ImageColor3 = self.Theme.Text,
        Size = UDim2.new(0, 30, 0, 30),
        Position = UDim2.new(1, -50, 0, 15),
        Parent = container
    })

    textBox.Focused:Connect(function()
        self:Tween(container, {BackgroundColor3 = self.Theme.Accent}, 0.2)
        search.Active = true
    end)
    textBox.FocusLost:Connect(function()
        self:Tween(container, {BackgroundColor3 = self.Theme.Secondary}, 0.2)
        search.Active = false
    end)

    textBox:GetPropertyChangedSignal("Text"):Connect(function()
        search.Value = textBox.Text
    end)

    clearButton.MouseButton1Click:Connect(function()
        textBox.Text = ""
    end)

    return search
end

-- Cria uma seção expansível
function AuroraPro:CreateSection(parent, title)
    local section = {
        Expanded = true,
        Content = {}
    }

    local header = self:Create("TextButton", {
        Text = title,
        Font = Enum.Font.GothamBold,
        TextColor3 = self.Theme.Text,
        TextSize = 18,
        Size = UDim2.new(1, -40, 0, 40),
        Position = UDim2.new(0, 20, 0, 10),
        BackgroundColor3 = self.Theme.Secondary,
        BackgroundTransparency = 0.3,
        Parent = parent
    })

    local contentFrame = self:Create("Frame", {
        Size = UDim2.new(1, -40, 0, 0),
        Position = UDim2.new(0, 20, 0, 50),
        BackgroundColor3 = self.Theme.Secondary,
        BackgroundTransparency = 0.3,
        Parent = parent
    })

    header.MouseButton1Click:Connect(function()
        section.Expanded = not section.Expanded
        self:Tween(contentFrame, {
            Size = UDim2.new(1, -40, 0, section.Expanded and 200 or 0)
        }, 0.3)
    end)

    section.AddButton = function(text, callback)
        local button = self:CreateButton(contentFrame, text, callback)
        table.insert(section.Content, button)
        return button
    end

    return section
end

-- Botão com ícone
function AuroraPro:CreateButton(parent, text, callback)
    local button = self:Create("TextButton", {
        Text = text,
        Font = Enum.Font.GothamSemibold,
        TextColor3 = self.Theme.Text,
        TextSize = 16,
        Size = UDim2.new(1, -40, 0, 50),
        Position = UDim2.new(0, 20, 0, 10),
        BackgroundColor3 = self.Theme.Secondary,
        BackgroundTransparency = 0.3,
        Parent = parent
    })

    local icon = self:Create("ImageLabel", {
        Image = "rbxassetid://10307049535",
        ImageColor3 = self.Theme.Text,
        Size = UDim2.new(0, 25, 0, 25),
        Position = UDim2.new(0, 10, 0.5, -12.5),
        Parent = button
    })

    button.MouseEnter:Connect(function()
        self:Tween(button, {BackgroundColor3 = self.Theme.Accent}, 0.2)
    end)
    button.MouseLeave:Connect(function()
        self:Tween(button, {BackgroundColor3 = self.Theme.Secondary}, 0.2)
    end)

    button.MouseButton1Click:Connect(callback)

    return button
end

-- Animação avançada
function AuroraPro:Tween(instance, properties, duration, callback)
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

return AuroraPro
