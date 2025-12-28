-- ESP FRUTA BLOX FRUITS - SOMENTE FRUTA SPAWNADA PELO JOGO

local Players = game:GetService("Players")
local RunService = game:GetService("RunService")
local player = Players.LocalPlayer

-- GUI
local gui = Instance.new("ScreenGui")
gui.Name = "FruitESP_UI"
gui.ResetOnSpawn = false
gui.Parent = player:WaitForChild("PlayerGui")

-- ===== GOST HUB (3 SEGUNDOS) =====
local aviso = Instance.new("TextLabel")
aviso.Size = UDim2.new(0, 300, 0, 30)
aviso.Position = UDim2.new(0.5, -150, 0.45, 0)
aviso.BackgroundTransparency = 1
aviso.TextColor3 = Color3.fromRGB(255, 255, 255)
aviso.TextSize = 18
aviso.Font = Enum.Font.GothamBold
aviso.Text = "GOST HUB"
aviso.Parent = gui

task.delay(3, function()
    aviso:Destroy()
end)
-- ================================

local label = Instance.new("TextLabel")
label.Size = UDim2.new(0, 360, 0, 20)
label.Position = UDim2.new(0.5, -180, 1, -110)
label.BackgroundTransparency = 1
label.TextColor3 = Color3.fromRGB(255, 255, 255)
label.TextSize = 14
label.Font = Enum.Font.Gotham
label.Visible = false
label.Parent = gui

-- Variáveis
local fruits = {}
local lastUpdate = 0

-- 🔒 FILTRO EXATO (SOMENTE FRUTA SPAWNADA)
local function isSpawnedFruit(obj)
    return obj:IsA("Model")
        and obj.Name == "Fruit"
        and not obj:FindFirstChildOfClass("Humanoid")
end

-- Posição da fruta
local function getFruitPosition(model)
    local part = model:FindFirstChildWhichIsA("BasePart", true)
    return part and part.Position
end

-- Adicionar fruta
local function addFruit(obj)
    if fruits[obj] then return end
    if isSpawnedFruit(obj) then
        fruits[obj] = true
    end
end

-- Remover fruta
local function removeFruit(obj)
    fruits[obj] = nil
end

-- Scan inicial
for _, v in ipairs(workspace:GetDescendants()) do
    addFruit(v)
end

-- Fruta spawnada
workspace.DescendantAdded:Connect(function(obj)
    task.wait(0.2)
    addFruit(obj)
end)

-- Fruta pega
workspace.DescendantRemoving:Connect(function(obj)
    removeFruit(obj)
end)

-- Atualização leve
RunService.Heartbeat:Connect(function()
    if tick() - lastUpdate < 0.5 then return end
    lastUpdate = tick()

    local char = player.Character
    local hrp = char and char:FindFirstChild("HumanoidRootPart")
    if not hrp then return end

    local closest = math.huge
    local found = false

    for fruit in pairs(fruits) do
        local pos = getFruitPosition(fruit)
        if pos then
            local dist = (hrp.Position - pos).Magnitude
            if dist < closest then
                closest = dist
                found = true
            end
        end
    end

    if found then
        label.Visible = true
        label.Text = "FRUTA DETECTADA: " .. math.floor(closest) .. " metros de distância"
    else
        label.Visible = false
    end
end)
