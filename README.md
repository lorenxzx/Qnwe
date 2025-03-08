-- CoolUILibrary.lua (ModuleScript)
local CoolUI = {}
CoolUI.__index = CoolUI

-- Tema padrão (cores modernas)
local theme = {
    Primary = Color3.fromRGB(35, 35, 35),
    Secondary = Color3.fromRGB(50, 50, 50),
    Accent = Color3.fromRGB(0, 162, 255),
    Text = Color3.fromRGB(255, 255, 255),
    BorderRadius = UDim.new(0, 10),
    Padding = 10,
    Shadow = Color3.fromRGB(0, 0, 0)
}

-- Configura um elemento UI com estilo
function CoolUI:StyleElement(element)
    element.BackgroundColor3 = theme.Primary
    element.BorderSizePixel = 0
    element.BackgroundTransparency = 0.1
    element.ClipsDescendants = true
    return element
end

-- Cria uma janela arrastável
function CoolUI:CreateWindow(title, size)
    local window = self:StyleElement(Instance.new("Frame"))
    window.Size = size or UDim2.new(0, 400, 0, 300)
    window.Position = UDim2.new(0.5, -200, 0.5, -150)
    window.Parent = self.screenGui

    -- Sombra
    local shadow = Instance.new("UIStroke")
    shadow.Color = theme.Shadow
    shadow.Thickness = 3
    shadow.ApplyStrokeMode = Enum.ApplyStrokeMode.Border
    shadow.Parent = window

    -- Barra de título
    local titleBar = self:StyleElement(Instance.new("Frame"))
    titleBar.Size = UDim2.new(1, 0, 0, 40)
    titleBar.Position = UDim2.new(0, 0, 0, 0)
    titleBar.BackgroundColor3 = theme.Secondary
    titleBar.Parent = window

    -- Título
    local titleLabel = Instance.new("TextLabel")
    titleLabel.Text = title
    titleLabel.Font = Enum.Font.GothamBold
    titleLabel.TextColor3 = theme.Text
    titleLabel.TextSize = 18
    titleLabel.BackgroundTransparency = 1
    titleLabel.Size = UDim2.new(1, 0, 1, 0)
    titleLabel.Parent = titleBar

    -- Botão de fechar
    local closeButton = Instance.new("TextButton")
    closeButton.Text = "×"
    closeButton.Font = Enum.Font.GothamBold
    closeButton.TextColor3 = theme.Text
    closeButton.TextSize = 24
    closeButton.BackgroundTransparency = 1
    closeButton.Size = UDim2.new(0, 40, 1, 0)
    closeButton.Position = UDim2.new(1, -40, 0, 0)
    closeButton.Parent = titleBar

    -- Container de conteúdo
    local content = self:StyleElement(Instance.new("Frame"))
    content.Size = UDim2.new(1, 0, 1, -40)
    content.Position = UDim2.new(0, 0, 0, 40)
    content.Parent = window

    -- Permitir arrastar
    local dragging = false
    titleBar.InputBegan:Connect(function(input)
        if input.UserInputType == Enum.UserInputType.MouseButton1 then
            dragging = true
        end
    end)
    titleBar.InputEnded:Connect(function(input)
        if input.UserInputType == Enum.UserInputType.MouseButton1 then
            dragging = false
        end
    end)
    titleBar.InputChanged:Connect(function(input)
        if dragging and input.UserInputType == Enum.UserInputType.MouseMovement then
            local delta = input.Position - input.PositionPrevious
            window.Position = window.Position + UDim2.new(0, delta.X, 0, delta.Y)
        end
    end)

    -- Adicionar efeito de hover no botão de fechar
    closeButton.MouseEnter:Connect(function()
        closeButton.TextColor3 = theme.Accent
    end)
    closeButton.MouseLeave:Connect(function()
        closeButton.TextColor3 = theme.Text
    end)

    -- Retornar a janela e o conteúdo
    return {
        Window = window,
        Content = content,
        CloseButton = closeButton
    }
end

-- Cria um botão estilizado
function CoolUI:CreateButton(parent, text, callback)
    local button = self:StyleElement(Instance.new("TextButton"))
    button.Text = text
    button.Font = Enum.Font.GothamSemibold
    button.TextColor3 = theme.Text
    button.TextSize = 16
    button.Size = UDim2.new(1, -20, 0, 40)
    button.Position = UDim2.new(0, 10, 0, 10)
    button.Parent = parent

    -- Efeito de clique
    button.MouseButton1Down:Connect(function()
        button.BackgroundColor3 = theme.Accent
    end)
    button.MouseButton1Up:Connect(function()
        button.BackgroundColor3 = theme.Primary
        callback()
    end)
    button.MouseLeave:Connect(function()
        button.BackgroundColor3 = theme.Primary
    end)

    return button
end

-- Inicializa a biblioteca
function CoolUI.new()
    local self = setmetatable({}, CoolUI)
    self.screenGui = Instance.new("ScreenGui")
    self.screenGui.IgnoreGuiInset = true
    self.screenGui.ResetOnSpawn = false
    return self
end

return CoolUI
