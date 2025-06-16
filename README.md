-- Kill Aura com teleporte de zumbi para perto do jogador (mas em distância segura)

local player = game.Players.LocalPlayer
local character = player.Character or player.CharacterAdded:Wait()
local humanoidRootPart = character:WaitForChild("HumanoidRootPart")
local attackRange = 15      -- Distância para atacar
local safeDistance = 10     -- Distância que o zumbi será teleportado à sua frente

-- RemoteEvent do ataque (ajuste o nome se necessário)
local function getWeaponEvent()
    local rs = game:GetService("ReplicatedStorage")
    return rs:FindFirstChild("GunEvent") or rs:FindFirstChild("KnifeEvent") or rs:FindFirstChild("WeaponEvent")
end

-- Busca todos os zumbis na workspace
local function getZombies()
    local zombies = {}
    for _, obj in pairs(workspace:GetChildren()) do
        if obj:FindFirstChild("Head") and obj.Name:lower():find("zombie") then
            table.insert(zombies, obj)
        end
    end
    return zombies
end

-- Teleporta o zumbi para uma posição à frente do jogador, mas a uma distância segura
local function teleportZombieSafe(zombie)
    if zombie:FindFirstChild("HumanoidRootPart") then
        local forward = humanoidRootPart.CFrame.LookVector
        local safePos = humanoidRootPart.Position + forward * safeDistance
        zombie.HumanoidRootPart.CFrame = CFrame.new(safePos)
    end
end

-- Ataca a cabeça do zumbi
local function attackZombieHead(zombie)
    local weaponEvent = getWeaponEvent()
    if weaponEvent and zombie:FindFirstChild("Head") then
        weaponEvent:FireServer(zombie.Head)
    end
end

while wait(0.2) do
    for _, zombie in pairs(getZombies()) do
        if zombie:FindFirstChild("HumanoidRootPart") then
            local distance = (humanoidRootPart.Position - zombie.HumanoidRootPart.Position).Magnitude
            -- Se o zumbi estiver longe, teleporta para perto (mas não muito perto)
            if distance > safeDistance + 2 then
                teleportZombieSafe(zombie)
            -- Se estiver na distância de ataque, ataca
            elseif distance <= attackRange then
                attackZombieHead(zombie)
            end
        end
    end
end
