-- ============================================================
-- TECLADO PARA CARROS - Botones de cruzar más grandes
-- Pegar en Delta Executor
-- ============================================================

local Players = game:GetService("Players")
local VIM = game:GetService("VirtualInputManager")
local UIS = game:GetService("UserInputService")
local player = Players.LocalPlayer
local playerGui = player:WaitForChild("PlayerGui")

for _, v in ipairs(playerGui:GetChildren()) do
    if v.Name == "TecladoCarros" then v:Destroy() end
end

local gui = Instance.new("ScreenGui")
gui.Name = "TecladoCarros"
gui.ResetOnSpawn = false
gui.IgnoreGuiInset = true
gui.ZIndexBehavior = Enum.ZIndexBehavior.Sibling
gui.Parent = playerGui

task.spawn(function()
    while gui.Parent do
        local touchGui = playerGui:FindFirstChild("TouchGui")
        if touchGui and not touchGui.Enabled then touchGui.Enabled = true end
        task.wait(0.5)
    end
end)

-- ============ SISTEMA DE OCULTAR / MOSTRAR ============
local botonesGuardados = {}

local teclasUsadas = {
    Enum.KeyCode.W, Enum.KeyCode.A, Enum.KeyCode.S, Enum.KeyCode.D,
    Enum.KeyCode.E, Enum.KeyCode.Q, Enum.KeyCode.F, Enum.KeyCode.P,
    Enum.KeyCode.Space, Enum.KeyCode.Escape
}

local function soltarTodasLasTeclas()
    for _, key in ipairs(teclasUsadas) do
        pcall(function()
            VIM:SendKeyEvent(false, key, false, game)
        end)
    end
end

local function guardarYocultar()
    botonesGuardados = {}
    for _, guiObj in ipairs(playerGui:GetChildren()) do
        if guiObj:IsA("ScreenGui") and guiObj.Name ~= "TecladoCarros" then
            for _, obj in ipairs(guiObj:GetDescendants()) do
                if obj:IsA("GuiObject") and obj.Visible then
                    table.insert(botonesGuardados, obj)
                    obj.Visible = false
                end
            end
        end
    end
end

local function mostrarTodo()
    for _, obj in ipairs(botonesGuardados) do
        if obj and obj.Parent then
            obj.Visible = true
        end
    end
    botonesGuardados = {}
end

-- ================= BOTÓN ⌨ FIJO =================
local toggle = Instance.new("TextButton")
toggle.Size = UDim2.new(0, 45, 0, 45)
toggle.Position = UDim2.new(0, 15, 0, 100)
toggle.Text = "✖"
toggle.TextSize = 22
toggle.Font = Enum.Font.GothamBold
toggle.BackgroundColor3 = Color3.fromRGB(35, 35, 40)
toggle.TextColor3 = Color3.fromRGB(255, 255, 255)
toggle.BorderSizePixel = 0
toggle.AutoButtonColor = false
toggle.Active = true
toggle.Parent = gui

local cT = Instance.new("UICorner")
cT.CornerRadius = UDim.new(1, 0)
cT.Parent = toggle

local sT = Instance.new("UIStroke")
sT.Color = Color3.fromRGB(255, 80, 80)
sT.Thickness = 2
sT.Parent = toggle

local panel = Instance.new("Frame")
panel.Size = UDim2.new(1, 0, 1, 0)
panel.BackgroundTransparency = 1
panel.Visible = true
panel.Parent = gui

-- ============ FUNCIÓN DE BOTÓN ============
local function crearBoton(texto, keycode, pos, tam, colorBase, tamTexto)
    local btn = Instance.new("TextButton")
    btn.Text = texto
    btn.TextSize = tamTexto or 20
    btn.Font = Enum.Font.GothamBold
    btn.BackgroundColor3 = colorBase
    btn.BackgroundTransparency = 0.1
    btn.TextColor3 = Color3.fromRGB(255, 255, 255)
    btn.BorderSizePixel = 0
    btn.Size = tam
    btn.Position = pos
    btn.AutoButtonColor = false
    btn.Parent = panel

    local c = Instance.new("UICorner")
    c.CornerRadius = UDim.new(1, 0)
    c.Parent = btn

    local stroke = Instance.new("UIStroke")
    stroke.Color = Color3.fromRGB(255, 255, 255)
    stroke.Transparency = 0.6
    stroke.Thickness = 2
    stroke.Parent = btn

    local dedoActivo = nil

    btn.InputBegan:Connect(function(input)
        if input.UserInputType == Enum.UserInputType.Touch
        or input.UserInputType == Enum.UserInputType.MouseButton1 then
            if dedoActivo then return end
            dedoActivo = input
            btn.BackgroundColor3 = Color3.fromRGB(0, 220, 120)
            pcall(function()
                VIM:SendKeyEvent(true, keycode, false, game)
            end)
        end
    end)

    local function soltar(input)
        if dedoActivo == input then
            dedoActivo = nil
            btn.BackgroundColor3 = colorBase
            pcall(function()
                VIM:SendKeyEvent(false, keycode, false, game)
            end)
        end
    end

    btn.InputEnded:Connect(function(input)
        if input.UserInputType == Enum.UserInputType.Touch
        or input.UserInputType == Enum.UserInputType.MouseButton1 then
            soltar(input)
        end
    end)

    UIS.InputEnded:Connect(function(input)
        soltar(input)
    end)
end

-- ==================== IZQUIERDA: Cruzar (MÁS GRANDES) ============
crearBoton("←", Enum.KeyCode.A, UDim2.new(0, 15, 1, -170),
    UDim2.new(0, 95, 0, 95), Color3.fromRGB(60, 100, 180), 32)

crearBoton("→", Enum.KeyCode.D, UDim2.new(0, 120, 1, -170),
    UDim2.new(0, 95, 0, 95), Color3.fromRGB(60, 100, 180), 32)

-- CAMBIOS (arriba de las flechas)
crearBoton("CAMBIOS", Enum.KeyCode.E, UDim2.new(0, 15, 1, -265),
    UDim2.new(0, 95, 0, 75), Color3.fromRGB(30, 130, 220), 13)

crearBoton("CAMBIOS", Enum.KeyCode.Q, UDim2.new(0, 120, 1, -265),
    UDim2.new(0, 95, 0, 75), Color3.fromRGB(230, 80, 20), 13)

-- ==================== DERECHA: Arrancar / Retroceso ============
crearBoton("▲", Enum.KeyCode.W, UDim2.new(1, -105, 1, -235),
    UDim2.new(0, 90, 0, 90), Color3.fromRGB(30, 140, 60), 28)

crearBoton("▼", Enum.KeyCode.S, UDim2.new(1, -105, 1, -135),
    UDim2.new(0, 90, 0, 90), Color3.fromRGB(180, 40, 40), 28)

-- ==================== ARRIBA DERECHA ============
crearBoton("PRENDER", Enum.KeyCode.F, UDim2.new(1, -95, 0, 20),
    UDim2.new(0, 80, 0, 42), Color3.fromRGB(220, 50, 50), 13)

crearBoton("FRENO", Enum.KeyCode.P, UDim2.new(1, -165, 0, 20),
    UDim2.new(0, 65, 0, 42), Color3.fromRGB(130, 40, 200), 13)

crearBoton("⛔", Enum.KeyCode.Space, UDim2.new(1, -220, 0, 20),
    UDim2.new(0, 42, 0, 42), Color3.fromRGB(220, 120, 20), 14)

-- ESC
crearBoton("ESC", Enum.KeyCode.Escape, UDim2.new(0, 15, 0, 15),
    UDim2.new(0, 45, 0, 32), Color3.fromRGB(70, 70, 80), 12)

-- ================= CLICK DEL BOTÓN ⌨ ==========
local toggleUsado = false

toggle.InputBegan:Connect(function(input)
    if input.UserInputType == Enum.UserInputType.Touch
    or input.UserInputType == Enum.UserInputType.MouseButton1 then
        if toggleUsado then return end
        toggleUsado = true
        panel.Visible = not panel.Visible
        if panel.Visible then
            toggle.Text = "✖"
            sT.Color = Color3.fromRGB(255, 80, 80)
            guardarYocultar()
        else
            soltarTodasLasTeclas()
            toggle.Text = "⌨"
            sT.Color = Color3.fromRGB(0, 220, 120)
            mostrarTodo()
        end
    end
end)

toggle.InputEnded:Connect(function(input)
    if input.UserInputType == Enum.UserInputType.Touch
    or input.UserInputType == Enum.UserInputType.MouseButton1 then
        toggleUsado = false
    end
end)

print("[TecladoCarros] Listo. Botones de cruzar más grandes.")