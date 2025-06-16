--[[
Script Roblox Lua: Auto Headshot com arma "Pistol" em bots
Este script deve ser executado localmente (LocalScript) no Roblox Studio.
Ele detecta bots (NPCs) e acerta headshots automaticamente usando a arma "Pistol" equipada pelo jogador.
IMPORTANTE: Use apenas para estudos e testes em ambientes privados. Não use para trapacear em jogos de terceiros.
]]

local player = game.Players.LocalPlayer
local mouse = player:GetMouse()
local weaponName = "Pistol" -- Nome exato da arma

-- Função auxiliar para encontrar todos NPCs/bots
local function getBots()
    local bots = {}
    for _, obj in pairs(workspace:GetChildren()) do
        if obj:IsA("Model") and obj ~= player.Character and obj:FindFirstChild("Head") and obj:FindFirstChild("Humanoid") then
            -- Ignora jogadores
            if not game.Players:GetPlayerFromCharacter(obj) then
                table.insert(bots, obj)
            end
        end
    end
    return bots
end

-- Função para mirar e acertar o headshot
local function headshotBot(bot)
    local weapon = player.Backpack:FindFirstChild(weaponName) or (player.Character and player.Character:FindFirstChild(weaponName))
    if not weapon then return end

    -- Equipa a arma se não estiver equipada
    if not player.Character:FindFirstChild(weaponName) then
        weapon.Parent = player.Character
        wait(0.2)
    end

    local head = bot:FindFirstChild("Head")
    if not head then return end

    -- Mova o mouse até a cabeça do bot (visual, não funcional para atirar, só para simular)
    mouse.Target = head

    -- Se a arma tiver um RemoteEvent de disparo, tente ativar
    local fireEvent = weapon:FindFirstChildWhichIsA("RemoteEvent") or weapon:FindFirstChildWhichIsA("RemoteFunction")
    if fireEvent then
        local pos = head.Position
        fireEvent:FireServer(pos, head)
    elseif weapon:FindFirstChild("Activate") then
        weapon:Activate()
    end
end

-- Loop para headshot automático
while true do
    local bots = getBots()
    for _, bot in ipairs(bots) do
        if bot:FindFirstChild("Humanoid") and bot.Humanoid.Health > 0 then
            headshotBot(bot)
            wait(0.1)
        end
    end
    wait(0.5)
end
