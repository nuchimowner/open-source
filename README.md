  CODE QUE DA https://raw.githubusercontent.com/nuchimowner/pi/refs/heads/main/README.md


 local function _w(t)
        t = t or 0.03
        if task and type(task.wait) == "function" then
            task.wait(t)
        elseif type(wait) == "function" then
            wait(t)
        end
    end

    do
        local n = 0
        while n < 200 do
            local ok, loaded = pcall(function()
                return game:IsLoaded()
            end)
            if ok and loaded then break end
            n = n + 1
            _w(0.05)
        end
    end
    _w(0.1)
    --print("[073 Hub] Starting...")

    local Players = game:GetService("Players")
    local RunService = game:GetService("RunService")
    local UIS = game:GetService("UserInputService")
    local TS = game:GetService("TweenService")
    local Lighting = game:GetService("Lighting")
    local HS = game:GetService("HttpService")

    local LP = Players.LocalPlayer
    if not LP then
        local ok, plr = pcall(function()
            return Players:WaitForChild("LocalPlayer", 15)
        end)
        if ok then LP = plr end
    end
    if not LP then
        --warn("[073 Hub] LocalPlayer not found - abort")
        return
    end

    local _genv = _G
    pcall(function()
        if type(getgenv) == "function" then
            local g = getgenv()
            if type(g) == "table" then _genv = g end
        end
    end)

    local function destroyOldGuis()
        local names = {"Hub073", "Hub073", "Hub073", "Hub073MobileButtons", "Hub073Intro", "Hub073Intro"}
        local parents = {}
        pcall(function() table.insert(parents, game:GetService("CoreGui")) end)
        pcall(function() if LP then table.insert(parents, LP:FindFirstChild("PlayerGui") or LP:WaitForChild("PlayerGui", 2)) end end)
        pcall(function() if type(gethui) == "function" then table.insert(parents, gethui()) end end)
        for _, parent in ipairs(parents) do
            if parent then
                for _, name in ipairs(names) do
                    pcall(function()
                        local o = parent:FindFirstChild(name)
                        while o do
                            o:Destroy()
                            o = parent:FindFirstChild(name)
                        end
                    end)
                end
            end
        end
        _G.Hub073MobileButtonRefs = {}
    end

    if _genv.__Hub073Loaded then
        pcall(destroyOldGuis)
        _genv.__Hub073Loaded = false
        --warn("[073 Hub] Old instance cleaned - reloading...")
    end
    pcall(destroyOldGuis)

    local getconnections = getconnections or get_signal_cons or getconnects or (syn and syn.get_signal_cons) or nil

    local _IS_MOBILE = false
    pcall(function()
        _IS_MOBILE = UIS.TouchEnabled == true and UIS.KeyboardEnabled ~= true
    end)

    pcall(function()
        if UIS.TouchEnabled and not UIS.MouseEnabled then _IS_MOBILE = true end
    end)

    local function _perfEveryN(nMobile, nDesktop)
        local n = _IS_MOBILE and nMobile or nDesktop
        if n <= 1 then return true end

        local bucket = math.floor(os.clock() * 60)
        return (bucket % n) == 0
    end
    local function _perfDue(key, mobileSec, desktopSec)

        return _perfEveryN(math.max(1, math.floor((mobileSec or 0.1) * 60)), math.max(1, math.floor((desktopSec or 0.05) * 60)))
    end

    local S = {
        NS = 60, CS = 30, LAGGER_SPEED = 15, LAGGER_CARRY_SPEED = 24.5,
        speedMode = false, antiRagdollEnabled = false, antiRagdollMode = 1,
        aimbotMode = 1,
        antiBatBypassEnabled = false,
        laggerToggled = false, laggerPhase = 0,
        autoCarrySpeedEnabled = false, setAutoCarrySpeedVisual = nil,
        _autoCarryFromSteal = false, _autoCarryWaitingPickup = false,
        _autoCarryGraceUntil = 0, _autoCarryPickupUntil = 0, _autoCarryReturnMode = nil, _stealAttrWasActive = false,
        medusaCounterEnabled = false, medusaResetEnabled = false, batCounterEnabled = false, unwalkEnabled = false,
        medusaDebounce = false, medusaLastUsed = 0, dropActive = false,
        autoLeftEnabled = false, autoRightEnabled = false, autoLeftSetVisual = nil, autoRightSetVisual = nil,
        speedLabel = nil, autoBatEnabled = false, autoSwingEnabled = true, autoBatSetVisual = nil,
        autoBatEquippedThisRun = false, _autoBatTarget = nil, _autoBatLastScan = 0, resetAutoBatMotion = nil,
        AUTO_BAT_SPEED = 58, AUTO_BAT_VERT_SPEED = 52, AUTO_BAT_DIST = -2.8, AUTO_BAT_HEIGHT = 4.75,
        AUTO_BAT_V_OFF = 1, AUTO_BAT_TURN_SPEED = 285, AUTO_BAT_MAX_TURN_RATE = 28,
        setBatCounterVisual = nil, startBatCounter = nil, stopBatCounter = nil,
        enableAutoBat = nil, disableAutoBat = nil,
        antiLagEnabled = false, removeAccessoriesEnabled = false, antiDieEnabled = false, antiFlingShieldEnabled = false, antiLagDescConn = nil,
        stretchRezEnabled = false, stretchRezAmount = 0.7, stretchRezConn = nil, setStretchRezVisual = nil, unwalkSavedAnimate = nil,
        _anyKeyListening = false, autoTPEnabled = false, autoTPHeight = 20, autoTPConn = nil, setAutoTPVisual = nil,
        autoSaveEnabled = true, _lastSaveOk = false, _lastSaveAt = 0,
        cursedResetRemote = nil, CURSED_RESET_GUID = "f888ee6e-c86d-46e1-93d7-0639d6635d42",
        AntiRagdollConns = {},
        noPlayerCollisionEnabled = false,
        safeModeEnabled = false,
        autoResetOnMedEnabled = false,
        showTracerEnabled = false,
        ragdollCountdownEnabled = false,
        guiLocked = false,
        hideMobileButtons = false,
        lockMobileButtons = false,
        infJumpMode = "hold",
        mirrorTPDownEnabled = false,
        _bodyLockPausedByCombat = false,
        _bodyLockWanted = false,
        _bodyLockPausedByBrainrot = false,
        mobileButtonScale = 0.80,
        keyboardOverlayEnabled = false,
        keyboardOverlayPosX = 0.025,
        keyboardOverlayPosY = 0.62,
        autoStealGuiStyle = "Old", -- "Old" | "New" | "Personalizada"
        uiLayoutStyle = "Old", -- Main/Bind/Systems bottom tabs
        asGuiCustom = { width = 360, height = 42, barH = 10, alpha = 0.1 },
        noSkyEnabled = false,
        kbOverlayColorName = "White",
        kbOverlayColor = Color3.fromRGB(255,255,255),
        tpBatDistance = 1000,
        antiAntiAntiDesyncEnabled = false,
        antiAntiAntiDesyncRadius = 100,
        antiAntiAntiDesyncLastPos = {},
        espHitboxEnabled = false,
        laggerPanelEnabled = false,
        ultraFpsBoost = false,
        espHitboxColor = "Green", -- Black, Light, Blue, Purple, Green
        espHitboxMode = "New", -- New, Old, Lines
        spinOnTPBatEnabled = false,
        flingOnTPBatEnabled = false,
        spinOnTPBatSpeed = 250,
        antiDropEnabled = false,
        customGearsEnabled = false,
        customGearsV2Enabled = false,
        customSoundEnabled = false,
        xrayGearsEnabled = false,
        xrayBaseEnabled = false,
        xrayBaseLevel = 1,
        customItemBatV1 = false,
        customItemBatV2 = false,
        customItemMedusaV1 = false,
        customItemMedusaV2 = false,
        customItemTryhardV1 = false,
    }

    local VS, UI, F = {}, {}, {}
    local NS, CS = S.NS, S.CS
    local CONFIG_FILE, CONFIG_FILE_LEGACY = "Hub073Config.json", "Hub073Config_Legacy.json"
    local _isfile = isfile or (syn and syn.isfile) or function() return false end
    local _readfile = readfile or (syn and syn.readfile) or function() return nil end
    local _writefile = writefile or (syn and syn.writefile) or (getgenv and getgenv().writefile) or nil

    local antiRagdollConn = nil
    local antiRagdollModeActive = nil
    local ragdollConnections = {}
    local cachedCharData = {}
    local isBoosting = false
    local BOOST_SPEED = 400

    local function __initAntiRagdollDie()
        local function arGetNormalSpeed()
            local spd = 16
            pcall(function()
                if F and F.getActiveMoveSpeed then
                    spd = F.getActiveMoveSpeed() or spd
                elseif NS then
                    spd = NS
                end
            end)
            if type(spd) ~= "number" or spd <= 0 then spd = 16 end
            return spd
        end

        local function arV2ResetCharacter(char)
            local hum = char and char:FindFirstChildOfClass("Humanoid")
            local root = char and char:FindFirstChild("HumanoidRootPart")
            if not hum or not root or hum.Health <= 0 then return end

            pcall(function()
                hum:ChangeState(Enum.HumanoidStateType.GettingUp)
                hum:ChangeState(Enum.HumanoidStateType.Running)
                root.Velocity = Vector3.zero
                root.RotVelocity = Vector3.zero
                root.AssemblyLinearVelocity = Vector3.zero
                root.AssemblyAngularVelocity = Vector3.zero
                hum.PlatformStand = false
                hum.Sit = false
                hum.AutoRotate = true
                hum.JumpPower = hum.JumpPower > 0 and hum.JumpPower or 50

                hum.WalkSpeed = arGetNormalSpeed()

                for _, obj in ipairs(char:GetDescendants()) do
                    if obj:IsA("Motor6D") then
                        obj.Enabled = true
                    elseif obj:IsA("Constraint") or obj:IsA("BallSocketConstraint") or obj:IsA("HingeConstraint") then
                        obj.Enabled = true
                    elseif obj:IsA("BasePart") then
                        obj.CanCollide = true
                        obj.AssemblyLinearVelocity = Vector3.zero
                        obj.AssemblyAngularVelocity = Vector3.zero
                    end
                end

                if workspace.CurrentCamera then
                    workspace.CurrentCamera.CameraSubject = hum
                end
                local playerModule = LP:FindFirstChild("PlayerScripts") and LP.PlayerScripts:FindFirstChild("PlayerModule")
                local controlModule = playerModule and playerModule:FindFirstChild("ControlModule")
                if controlModule then
                    local loaded, module = pcall(require, controlModule)
                    if loaded and module and module.Enable then
                        pcall(function() module:Enable() end)
                    end
                end
            end)
        end

        local function arCacheCharacterData()
            local char = LP.Character
            if not char then return false end
            local hum = char:FindFirstChildOfClass("Humanoid")
            local root = char:FindFirstChild("HumanoidRootPart")
            if not hum or not root then return false end
            cachedCharData = { character = char, humanoid = hum, root = root }
            return true
        end

        local function arDisconnectAll()
            for _, conn in ipairs(ragdollConnections) do
                pcall(function() conn:Disconnect() end)
            end
            ragdollConnections = {}
        end

        local function arIsRagdolled()
            if not cachedCharData.humanoid or not cachedCharData.humanoid.Parent then
                return false
            end
            local state = cachedCharData.humanoid:GetState()
            if state == Enum.HumanoidStateType.Physics
                or state == Enum.HumanoidStateType.Ragdoll
                or state == Enum.HumanoidStateType.FallingDown then
                return true
            end
            local endTime = LP:GetAttribute("RagdollEndTime")
            if endTime and (endTime - workspace:GetServerTimeNow()) > 0 then
                return true
            end
            return false
        end

        local function arForceExitRagdoll()
            if not cachedCharData.humanoid or not cachedCharData.root or not cachedCharData.character then return end
            pcall(function()
                LP:SetAttribute("RagdollEndTime", workspace:GetServerTimeNow())
            end)
            pcall(function()
                for _, descendant in ipairs(cachedCharData.character:GetDescendants()) do
                    if descendant:IsA("BallSocketConstraint")
                        or (descendant:IsA("Attachment") and tostring(descendant.Name):find("RagdollAttachment")) then
                        pcall(function() descendant:Destroy() end)
                    end
                    if descendant:IsA("Motor6D") and descendant.Enabled == false then
                        descendant.Enabled = true
                    end
                end
            end)
            if not isBoosting then
                isBoosting = true
                pcall(function()
                    cachedCharData.humanoid.WalkSpeed = BOOST_SPEED
                end)
            end
            pcall(function()
                if cachedCharData.humanoid.Health > 0 then
                    cachedCharData.humanoid.PlatformStand = false
                    cachedCharData.humanoid.Sit = false
                    cachedCharData.humanoid:ChangeState(Enum.HumanoidStateType.GettingUp)
                    cachedCharData.humanoid:ChangeState(Enum.HumanoidStateType.Running)
                end
                cachedCharData.root.Anchored = false
                cachedCharData.root.AssemblyAngularVelocity = Vector3.zero
                if workspace.CurrentCamera then
                    workspace.CurrentCamera.CameraSubject = cachedCharData.humanoid
                end
            end)
        end

        local function arHeartbeatLoop()
            while antiRagdollModeActive == "v1" do
                task.wait(_IS_MOBILE and 0.04 or 0.016)
                if not S.antiRagdollEnabled then

                else
                    if not cachedCharData.humanoid or not cachedCharData.humanoid.Parent then
                        arCacheCharacterData()
                    end
                    local currentlyRagdolled = arIsRagdolled()
                    if currentlyRagdolled then
                        arForceExitRagdoll()
                    elseif isBoosting and not currentlyRagdolled then
                        isBoosting = false
                        if cachedCharData.humanoid and cachedCharData.humanoid.Parent then
                            pcall(function()
                                cachedCharData.humanoid.WalkSpeed = arGetNormalSpeed()
                            end)
                        end
                    end
                end
            end
        end

        function F.startAntiRagdoll()
            local requestedMode = (S.antiRagdollMode == 2) and "v2" or "v1"
            if antiRagdollModeActive == requestedMode and (#S.AntiRagdollConns > 0 or antiRagdollConn) then
                return
            end
            F.stopAntiRagdoll()

            arCacheCharacterData()
            antiRagdollModeActive = requestedMode

            if requestedMode == "v2" then

                antiRagdollConn = RunService.Heartbeat:Connect(function()
                    if antiRagdollModeActive ~= "v2" or not S.antiRagdollEnabled then return end

                    if not _perfEveryN(2, 1) then return end
                    local char = LP.Character
                    local hum = char and char:FindFirstChildOfClass("Humanoid")
                    if not hum then return end
                    local state = hum:GetState()
                    if state == Enum.HumanoidStateType.Physics
                        or state == Enum.HumanoidStateType.Ragdoll
                        or state == Enum.HumanoidStateType.FallingDown
                        or state == Enum.HumanoidStateType.Dead
                        or hum.PlatformStand == true
                        or hum.Sit == true then
                        arV2ResetCharacter(char)
                    end
                end)
                table.insert(S.AntiRagdollConns, antiRagdollConn)
                return
            end

            local camConn = RunService.Heartbeat:Connect(function()
                if not _perfEveryN(3, 1) then return end
                local cam = workspace.CurrentCamera
                if cam and cachedCharData.humanoid and cachedCharData.humanoid.Parent then
                    cam.CameraSubject = cachedCharData.humanoid
                end
            end)
            table.insert(ragdollConnections, camConn)
            table.insert(S.AntiRagdollConns, camConn)

            local respawnConn = LP.CharacterAdded:Connect(function()
                isBoosting = false
                task.wait(0.5)
                arCacheCharacterData()
            end)
            table.insert(ragdollConnections, respawnConn)
            table.insert(S.AntiRagdollConns, respawnConn)

            task.spawn(arHeartbeatLoop)
        end

        function F.stopAntiRagdoll()
            antiRagdollModeActive = nil
            if antiRagdollConn then
                pcall(function() antiRagdollConn:Disconnect() end)
                antiRagdollConn = nil
            end
            if isBoosting and cachedCharData.humanoid then
                pcall(function()
                    cachedCharData.humanoid.WalkSpeed = arGetNormalSpeed()
                end)
            end
            isBoosting = false
            arDisconnectAll()
            for _, conn in pairs(S.AntiRagdollConns) do
                pcall(function() conn:Disconnect() end)
            end
            S.AntiRagdollConns = {}
            cachedCharData = {}
        end

        local _adDeathConns = {}
        local _adHeartConn = nil
        local _adCharConn = nil
        local _afsLoop = nil

        local function _adDisconnectAll()
            for _, c in ipairs(_adDeathConns) do pcall(function() c:Disconnect() end) end
            _adDeathConns = {}
            if _adHeartConn then pcall(function() _adHeartConn:Disconnect() end); _adHeartConn = nil end
        end

        local function protectCharClean(char)
            if not char or not S.antiDieEnabled then return end
            local hum = char:FindFirstChildOfClass("Humanoid") or char:WaitForChild("Humanoid", 5)
            if not hum then return end
            local root = char:FindFirstChild("HumanoidRootPart")
            pcall(function()
                hum.MaxHealth = math.huge
                hum.Health = math.huge
                hum.BreakJointsOnDeath = false
                hum:SetStateEnabled(Enum.HumanoidStateType.Dead, false)
                hum:SetStateEnabled(Enum.HumanoidStateType.FallingDown, false)
            end)
            -- forcefield buffer
            pcall(function()
                if not char:FindFirstChild("Hub073AntiDieFF") then
                    local ff = Instance.new("ForceField")
                    ff.Name = "Hub073AntiDieFF"
                    ff.Visible = false
                    ff.Parent = char
                end
            end)
            local sc = hum.StateChanged:Connect(function(_, new)
                if not S.antiDieEnabled then return end
                if new == Enum.HumanoidStateType.Dead or new == Enum.HumanoidStateType.FallingDown then
                    pcall(function()
                        hum.Health = math.huge
                        hum:SetStateEnabled(Enum.HumanoidStateType.Dead, false)
                        hum:ChangeState(Enum.HumanoidStateType.Running)
                    end)
                end
            end)
            table.insert(_adDeathConns, sc)
            local hc = hum:GetPropertyChangedSignal("Health"):Connect(function()
                if not S.antiDieEnabled then return end
                if hum.Health < 1e9 then
                    pcall(function()
                        hum.MaxHealth = math.huge
                        hum.Health = math.huge
                    end)
                end
            end)
            table.insert(_adDeathConns, hc)
            local dc = hum.Died:Connect(function()
                if not S.antiDieEnabled then return end
                pcall(function()
                    hum.MaxHealth = math.huge
                    hum.Health = math.huge
                    hum:SetStateEnabled(Enum.HumanoidStateType.Dead, false)
                    hum:ChangeState(Enum.HumanoidStateType.Running)
                end)
            end)
            table.insert(_adDeathConns, dc)
            if _adHeartConn then pcall(function() _adHeartConn:Disconnect() end) end
            _adHeartConn = RunService.Heartbeat:Connect(function()
                if not S.antiDieEnabled then return end
                if not hum or not hum.Parent then return end
                pcall(function()
                    if hum.Health < 1e9 or hum.MaxHealth < 1e9 then
                        hum.MaxHealth = math.huge
                        hum.Health = math.huge
                    end
                    hum.BreakJointsOnDeath = false
                    hum:SetStateEnabled(Enum.HumanoidStateType.Dead, false)
                    local st = hum:GetState()
                    if st == Enum.HumanoidStateType.Dead then
                        hum:ChangeState(Enum.HumanoidStateType.Running)
                    end
                    if not char:FindFirstChild("Hub073AntiDieFF") then
                        local ff = Instance.new("ForceField")
                        ff.Name = "Hub073AntiDieFF"
                        ff.Visible = false
                        ff.Parent = char
                    end
                end)
            end)
        end

        function F.startAntiDie()
            S.antiDieEnabled = true
            _adDisconnectAll()
            if _adCharConn then pcall(function() _adCharConn:Disconnect() end); _adCharConn = nil end
            protectCharClean(LP.Character)
            _adCharConn = LP.CharacterAdded:Connect(function(c)
                if not S.antiDieEnabled then return end
                task.wait(0.1)
                _adDisconnectAll()
                protectCharClean(c)
            end)
        end

        function F.stopAntiDie()
            S.antiDieEnabled = false
            _adDisconnectAll()
            if _adCharConn then pcall(function() _adCharConn:Disconnect() end); _adCharConn = nil end
            local char = LP.Character
            local hum = char and char:FindFirstChildOfClass("Humanoid")
            if hum then
                pcall(function()
                    hum:SetStateEnabled(Enum.HumanoidStateType.Dead, true)
                    hum.MaxHealth = 100
                    hum.Health = math.min(hum.Health, 100)
                    if hum.Health <= 0 then hum.Health = 100 end
                end)
            end
        end

        function F.startAntiFlingShield()
            S.antiFlingShieldEnabled = true
            if _afsLoop then pcall(function() _afsLoop:Disconnect() end); _afsLoop = nil end
            local threshold = 80
            _afsLoop = RunService.Heartbeat:Connect(function()
                if not S.antiFlingShieldEnabled then return end
                if not _perfEveryN(2, 1) then return end
                local char = LP.Character
                local root = char and char:FindFirstChild("HumanoidRootPart")
                if not root then return end
                local ok, vel = pcall(function() return root.AssemblyLinearVelocity end)
                if not ok or typeof(vel) ~= "Vector3" then
                    ok, vel = pcall(function() return root.Velocity end)
                end
                if ok and typeof(vel) == "Vector3" and vel.Magnitude > threshold then
                    local horiz = Vector3.new(vel.X, 0, vel.Z)
                    if horiz.Magnitude > threshold then
                        horiz = horiz.Unit * (threshold * 0.55)
                    end
                    local y = math.clamp(vel.Y, -threshold, threshold)
                    pcall(function()
                        root.AssemblyLinearVelocity = Vector3.new(horiz.X, y, horiz.Z)
                        root.AssemblyAngularVelocity = Vector3.zero
                        if root.Velocity then root.Velocity = Vector3.new(horiz.X, y, horiz.Z) end
                    end)
                end
            end)
        end

        function F.stopAntiFlingShield()
            S.antiFlingShieldEnabled = false
            if _afsLoop then pcall(function() _afsLoop:Disconnect() end); _afsLoop = nil end
        end

        local removeAccConn = nil
        local removeAccChildConn = nil
        local removeAccActive = false


    end
    __initAntiRagdollDie()

    local function __initAccJumpMirror()
        local function isHatOrHairAccessory(accessory)
            if not accessory or not accessory:IsA("Accessory") then return false end
            local accessoryType = nil
            pcall(function() accessoryType = accessory.AccessoryType end)
            if accessoryType == Enum.AccessoryType.Hat or accessoryType == Enum.AccessoryType.Hair then
                return true
            end
            for _, item in ipairs(accessory:GetDescendants()) do
                if item:IsA("Attachment") and (item.Name == "HatAttachment" or item.Name == "HairAttachment") then
                    return true
                end
            end
            local lowerName = tostring(accessory.Name):lower()
            return lowerName:find("hair", 1, true) ~= nil or lowerName:find("hat", 1, true) ~= nil
        end

        local function removeCharAccessories(char)
            if not char then return end
            for _, child in ipairs(char:GetChildren()) do
                pcall(function()
                    if child:IsA("Accessory") and isHatOrHairAccessory(child) then
                        child:Destroy()
                    end
                end)
            end
        end

        function F.startRemoveAccessories()
            removeAccActive = true
            S.removeAccessoriesEnabled = true
            local function bindCharacter(char)
                if removeAccChildConn then removeAccChildConn:Disconnect(); removeAccChildConn = nil end
                removeCharAccessories(char)
                if char then
                    removeAccChildConn = char.ChildAdded:Connect(function(child)
                        if not removeAccActive or not child:IsA("Accessory") then return end
                        task.defer(function()
                            if child.Parent == char then removeCharAccessories(char) end
                        end)
                    end)
                end
            end
            bindCharacter(LP.Character)
            if removeAccConn then removeAccConn:Disconnect() end
            removeAccConn = LP.CharacterAdded:Connect(function(char)
                task.wait(0.15)
                bindCharacter(char)
            end)
        end

        function F.stopRemoveAccessories()
            removeAccActive = false
            S.removeAccessoriesEnabled = false
            if removeAccConn then removeAccConn:Disconnect(); removeAccConn = nil end
            if removeAccChildConn then removeAccChildConn:Disconnect(); removeAccChildConn = nil end
        end

        local moveProxy = nil

        function F.ensureProxy()
            local c = LP.Character
            if not c then return nil end
            local root = c:FindFirstChild("HumanoidRootPart")
            if not root then return nil end
            if moveProxy and moveProxy.Parent == c then return moveProxy end
            if moveProxy then moveProxy:Destroy() end
            moveProxy = Instance.new("Part")
            moveProxy.Name = "Hub073_MoveProxy"
            moveProxy.Size = Vector3.new(1, 1, 1)
            moveProxy.Transparency = 1
            moveProxy.CanCollide = false
            moveProxy.Massless = true
            moveProxy.Parent = c
            local weld = Instance.new("WeldConstraint")
            weld.Part0 = root
            weld.Part1 = moveProxy
            weld.Parent = moveProxy
            return moveProxy
        end

        function F.destroyProxy()
            if moveProxy then
                moveProxy:Destroy()
                moveProxy = nil
            end
        end

        local infJumpEnabled = false
        local holdInfJumpConn = nil
        local infJumpInputConns = {}
        local INF_JUMP_POWER = 50
        local infJumpEnabled = false

        local function applyJumpVel(root, y)
            if not root or not root.Parent then return end
            pcall(function()
                local cv = root.AssemblyLinearVelocity
                root.AssemblyLinearVelocity = Vector3.new(cv.X, y, cv.Z)
            end)
        end

        local function manualJumpBoost(boost)
            if not infJumpEnabled then return end
            local char = LP.Character
            if not char then return end
            local root = char:FindFirstChild("HumanoidRootPart")
            if root then applyJumpVel(root, boost or INF_JUMP_POWER) end
        end

        local function stopHoldInfJump()
            if holdInfJumpConn then
                pcall(function() holdInfJumpConn:Disconnect() end)
                holdInfJumpConn = nil
            end
        end

        local function startHoldInfJump()
            if holdInfJumpConn then return end
            holdInfJumpConn = RunService.Heartbeat:Connect(function()
                if not infJumpEnabled then return end
                if (S.infJumpMode or "hold") ~= "hold" then return end
                local char = LP.Character
                if not char then return end
                local root = char:FindFirstChild("HumanoidRootPart")
                local hum = char:FindFirstChildOfClass("Humanoid")
                if not root or not hum then return end
                local isJumpHeld = false
                pcall(function()
                    isJumpHeld = UIS:IsKeyDown(Enum.KeyCode.Space)
                        or UIS:IsKeyDown(Enum.KeyCode.ButtonA)
                        or (hum.Jump == true)
                end)
                if isJumpHeld then
                    local cv = root.AssemblyLinearVelocity
                    if cv.Y < INF_JUMP_POWER then
                        applyJumpVel(root, INF_JUMP_POWER)
                    end
                end
            end)
        end

        function F.startInfJump()
            for _, c in ipairs(infJumpInputConns) do pcall(function() c:Disconnect() end) end
            infJumpInputConns = {}
            stopHoldInfJump()
            infJumpEnabled = true
            if S then S.infJumpEnabled = true end

            table.insert(infJumpInputConns, UIS.JumpRequest:Connect(function()
                if not infJumpEnabled then return end
                manualJumpBoost(INF_JUMP_POWER)
            end))
            table.insert(infJumpInputConns, UIS.InputBegan:Connect(function(input, gp)
                if gp or UIS:GetFocusedTextBox() then return end
                if not infJumpEnabled then return end
                if input.KeyCode == Enum.KeyCode.Space or input.KeyCode == Enum.KeyCode.ButtonA then
                    if (S.infJumpMode or "hold") == "manual" then
                        manualJumpBoost(INF_JUMP_POWER)
                    end
                end
            end))
            if (S.infJumpMode or "hold") == "hold" then
                startHoldInfJump()
            end
        end

        function F.stopInfJump()
            infJumpEnabled = false
            if S then S.infJumpEnabled = false end
            stopHoldInfJump()
            for _, c in ipairs(infJumpInputConns) do
                pcall(function() c:Disconnect() end)
            end
            infJumpInputConns = {}
        end

        function F.setInfJumpMode(mode)
            mode = (mode == "manual") and "manual" or "hold"
            if S then S.infJumpMode = mode end
            if infJumpEnabled then
                F.startInfJump()
            else
                stopHoldInfJump()
            end
        end


        local mirrorTPPreviousY = {}
        local mirrorTPLastTeleport = 0
        local mirrorTPConn = nil

        local function mirrorTPAimbotActive()
            return (aimbotEnabled == true)
                or (tpBatEnabled == true)
                or (S.autoBatEnabled == true)
                or (_G.MirrorNormalAimbotOn == true)
                or (_G.MirrorAntiBypassAimbotOn == true)
                or (_G.MirrorAntiDesyncAimbotOn == true)
        end

        local MIRROR_TP_DROP_THRESHOLD = 3
        local MIRROR_TP_DOWN_Y = -7.00

        local function mirrorTPTeleportDown()
            local character = LP.Character
            local root = character and character:FindFirstChild("HumanoidRootPart")
            local humanoid = character and character:FindFirstChildOfClass("Humanoid")
            if not root or not humanoid or humanoid.Health <= 0 then return end

            local now = tick()
            if now - (mirrorTPLastTeleport or 0) < 0.08 then return end
            mirrorTPLastTeleport = now

            local _, yaw = root.CFrame:ToEulerAnglesYXZ()
            root.CFrame = CFrame.new(root.Position.X, MIRROR_TP_DOWN_Y, root.Position.Z) * CFrame.Angles(0, yaw, 0)
            root.Velocity = Vector3.zero
            pcall(function() root.AssemblyLinearVelocity = Vector3.zero end)
        end

        function F.startMirrorTPDown()
            if mirrorTPConn then return end
            S.mirrorTPDownEnabled = true
            _G.MirrorSetTPDown = function(enabled)
                S.mirrorTPDownEnabled = enabled == true
                if not S.mirrorTPDownEnabled then
                    mirrorTPPreviousY = {}
                end
            end
            mirrorTPConn = RunService.Heartbeat:Connect(function()
                if not S.mirrorTPDownEnabled then return end
                if not mirrorTPAimbotActive() then
                    if next(mirrorTPPreviousY) then mirrorTPPreviousY = {} end
                    return
                end
                for _, plr in ipairs(Players:GetPlayers()) do
                    if plr ~= LP and plr.Character then
                        local tr = plr.Character:FindFirstChild("HumanoidRootPart")
                        if tr then
                            local currentY = tr.Position.Y
                            local previousY = mirrorTPPreviousY[plr.UserId]
                            if previousY and (previousY - currentY) >= MIRROR_TP_DROP_THRESHOLD then
                                pcall(mirrorTPTeleportDown)
                                mirrorTPPreviousY = {}
                                return
                            end
                            mirrorTPPreviousY[plr.UserId] = currentY
                        end
                    end
                end
            end)
        end

        function F.stopMirrorTPDown()
            S.mirrorTPDownEnabled = false
            if mirrorTPConn then
                pcall(function() mirrorTPConn:Disconnect() end)
                mirrorTPConn = nil
            end
            mirrorTPPreviousY = {}
        end


        function F.startFloat() end
        function F.stopFloat() end
        function F.setFloat(on) end
        function F.toggleFloat() end
        function F.getFloatSpeed() return 0 end
        function F.setFloatModeSpeed(v) end
        function F.cycleFloatMode() return "normal" end

        function F.setMirrorTPDown(on)
            if on then F.startMirrorTPDown() else F.stopMirrorTPDown() end
            if VS.setMirrorTPVisual then VS.setMirrorTPVisual(on == true) end
        end

        local aimbotEnabled = false
        local tpBatEnabled = false
        local tpBatMode = 1
        local TP_BAT_KEY = Enum.KeyCode.V
        local TP_BAT_CONTROLLER = Enum.KeyCode.ButtonY
        local setTPBatVisual, tpBatUIState, tpBatModeLbl = nil, nil, nil
        local setAimbotState, setTPBatState, getTPBatMode, findBatTool, getClosestAliveRoot
        local TP_BAT_MODES
        local startAimbot, stopAimbot, startTPBat, stopTPBat

        local fakeDesyncRoot = Instance.new("Part")
        fakeDesyncRoot.Name = "FakeDesyncRoot"
        fakeDesyncRoot.Anchored = true
        fakeDesyncRoot.CanCollide = false
        fakeDesyncRoot.Transparency = 1
        fakeDesyncRoot.Size = Vector3.new(2,2,2)
        fakeDesyncRoot.Parent = workspace


    end
    __initAccJumpMirror()

    local function __initAimbotTPBat()
    aimbotEnabled = false
    local aimbotConn = nil
    tpBatEnabled = false
    local aimbotSwingCooldown = false
    local AIMBOT_SPEED = 63
    local AIMBOT_VERT_SPEED = 52
    local AIMBOT_DISTANCE = -2.8
    local AIMBOT_HEIGHT = 4.75
    local AIMBOT_V_OFFSET = 1
    local AIMBOT_MAX_TURN_RATE = 28
    local AIMBOT_SWING_DIST = 5
    local AIMBOT_SWING_CD = 0.3
    local _aimbotLastSwing = 0

    findBatTool = function()
        local char = LP.Character
        if not char then return nil end
        for _, t in ipairs(char:GetChildren()) do
            if t:IsA("Tool") and (t.Name:lower():find("bat") or t.Name:lower():find("slap")) then return t end
        end
        local bp = LP:FindFirstChild("Backpack") or LP:FindFirstChildOfClass("Backpack")
        if bp then
            for _, t in ipairs(bp:GetChildren()) do
                if t:IsA("Tool") and (t.Name:lower():find("bat") or t.Name:lower():find("slap")) then return t end
            end
        end
        return nil
    end

    local function swingBatTool()
        if aimbotSwingCooldown then return end
        aimbotSwingCooldown = true
        pcall(function()
            local bat = findBatTool()
            local char = LP.Character
            if not bat or not char then return end
            local hum = char:FindFirstChildOfClass("Humanoid")
            if bat.Parent ~= char and hum then
                pcall(function() hum:EquipTool(bat) end)
            end
            local remote = bat:FindFirstChildOfClass("RemoteEvent")
            if remote then
                pcall(function() remote:FireServer() end)
            else
                pcall(function() bat:Activate() end)
            end
        end)
        task.delay(0.09, function() aimbotSwingCooldown = false end)
    end

    local _closestCache, _closestCacheAt = nil, 0
    getClosestAliveRoot = function()
        local now = os.clock()
        local ttl = _IS_MOBILE and 0.07 or 0.025
        if _closestCache and (now - _closestCacheAt) < ttl then
            if _closestCache and _closestCache.Parent then return _closestCache end
        end
        local root = LP.Character and LP.Character:FindFirstChild("HumanoidRootPart")
        if not root then return nil end
        local closest, minDist = nil, math.huge
        local closestPlr = nil
        for _, plr in ipairs(Players:GetPlayers()) do
            if plr ~= LP and plr.Character then
                local tRoot = plr.Character:FindFirstChild("HumanoidRootPart")
                local hum = plr.Character:FindFirstChildOfClass("Humanoid")
                if tRoot and hum and hum.Health > 0 then
                    local d = (tRoot.Position - root.Position).Magnitude
                    if d < minDist then
                        minDist = d
                        closest = tRoot
                        closestPlr = plr
                    end
                end
            end
        end
        
        if S.antiAntiAntiDesyncEnabled and closestPlr then
            local userId = closestPlr.UserId
            local lastPos = S.antiAntiAntiDesyncLastPos[userId]
            if minDist <= (S.antiAntiAntiDesyncRadius or 100) then
                -- Still in range: update last known position
                S.antiAntiAntiDesyncLastPos[userId] = closest.Position
            else
                -- Player suddenly far / teleported: use last known position
                if lastPos then
                    local jumpDist = (closest.Position - lastPos).Magnitude
                    if jumpDist > 15 then -- detected teleport / desync
                        fakeDesyncRoot.CFrame = CFrame.new(lastPos, root.Position)
                        _closestCache = fakeDesyncRoot
                        _closestCacheAt = now
                        return fakeDesyncRoot
                    end
                end
                -- Update anyway so we have a recent position
                S.antiAntiAntiDesyncLastPos[userId] = closest.Position
            end
        end
        
        _closestCache, _closestCacheAt = closest, now
        return closest
    end

    startAimbot = function()
        if aimbotConn then return end

        if tpBatEnabled then return end
        pcall(function()
            if K7 and K7.bodyLockEnabled then
                S._bodyLockPausedByCombat = true
                if K7.stopBodyLock then K7.stopBodyLock() end
                if VS and VS.setBodyLockVisual then VS.setBodyLockVisual(false) end
            end
        end)
        local hum0 = LP.Character and LP.Character:FindFirstChildOfClass("Humanoid")
        if hum0 then hum0.AutoRotate = false end
        aimbotEnabled = true

        local _aimCachedTarget = nil
        local _aimLastScan = 0
        aimbotConn = RunService.Heartbeat:Connect(function()
            if not aimbotEnabled then return end
            if tpBatEnabled then return end
            local char = LP.Character
            if not char then return end
            local root = char:FindFirstChild("HumanoidRootPart")
            local hum = char:FindFirstChildOfClass("Humanoid")
            if not root or not hum or hum.Health <= 0 then return end

            if hum.AutoRotate then
                hum.AutoRotate = false
            end

            if not char:FindFirstChildOfClass("Tool") then
                if _perfEveryN(4, 2) then
                    local bat = findBatTool and findBatTool() or nil
                    if bat then pcall(function() hum:EquipTool(bat) end) end
                end
            end

            local now = os.clock()
            local scanI = _IS_MOBILE and 0.08 or 0.03
            if (now - _aimLastScan) >= scanI then
                _aimLastScan = now
                _aimCachedTarget = getClosestAliveRoot and getClosestAliveRoot() or nil
            end
            local target = _aimCachedTarget
            if not target then
                hum.AutoRotate = true
                root.AssemblyAngularVelocity = Vector3.zero
                return
            end

            local aimTargetPos = target.Position + Vector3.new(0, AIMBOT_V_OFFSET, 0)
            local look = aimTargetPos - root.Position
            local flatLook = Vector3.new(look.X, 0, look.Z)

            if look.Magnitude > 0.01 and flatLook.Magnitude > 0.01 then
                local targetYaw = math.deg(math.atan2(-flatLook.X, -flatLook.Z))
                local yawDelta = (targetYaw - root.Orientation.Y + 180) % 360 - 180
                local yawRate = math.clamp(yawDelta * 8, -AIMBOT_MAX_TURN_RATE, AIMBOT_MAX_TURN_RATE)
                root.AssemblyAngularVelocity = Vector3.new(0, yawRate, 0)
            else
                root.AssemblyAngularVelocity = Vector3.zero
            end

            local dir = look.Magnitude > 0.01 and look.Unit or Vector3.new(0, 0, -1)
            local standPos = aimTargetPos - (dir * AIMBOT_DISTANCE) + Vector3.new(0, AIMBOT_HEIGHT, 0)
            local moveDir = standPos - root.Position
            local hDir = Vector3.new(moveDir.X, 0, moveDir.Z)
            local hVel = hDir.Magnitude > 0.1 and hDir.Unit * AIMBOT_SPEED or Vector3.zero
            local vVel = Vector3.new(0, math.clamp(moveDir.Y * 3, -AIMBOT_VERT_SPEED, AIMBOT_VERT_SPEED), 0)
            root.AssemblyLinearVelocity = hVel + vVel
            if hDir.Magnitude > 0.5 then
                pcall(function() hum:Move(hDir.Unit, false) end)
            end

            if (root.Position - target.Position).Magnitude < AIMBOT_SWING_DIST then
                local now = tick()
                if now - _aimbotLastSwing >= AIMBOT_SWING_CD then
                    _aimbotLastSwing = now
                    if swingBatTool then
                        pcall(swingBatTool)
                    else
                        local bat = findBatTool and findBatTool() or nil
                        if bat then pcall(function() bat:Activate() end) end
                    end
                end
            end
        end)
    end

    stopAimbot = function()
        aimbotEnabled = false
        if aimbotConn then
            aimbotConn:Disconnect()
            aimbotConn = nil
        end

        if not tpBatEnabled then
            local c = LP.Character
            local root = c and c:FindFirstChild("HumanoidRootPart")
            if root then
                root.AssemblyAngularVelocity = Vector3.zero
            end
            local hum2 = c and c:FindFirstChildOfClass("Humanoid")
            if hum2 then hum2.AutoRotate = true end
        end
        aimbotSwingCooldown = false
        pcall(function()
            if S._bodyLockPausedByCombat and not tpBatEnabled and K7 and K7.startBodyLock then
                S._bodyLockPausedByCombat = false
                K7.startBodyLock()
                if VS and VS.setBodyLockVisual then VS.setBodyLockVisual(true) end
            end
        end)
    end

    setAimbotState = function(on)
        if on then
            pcall(function() if F.stopAimbotBypassAntiBat then F.stopAimbotBypassAntiBat() end end)
            pcall(function() if stopAimbot then stopAimbot() end end)
            -- desativa qualquer LinearVelocity no character (nao trava o aimbot)
            pcall(function()
                local char = LP.Character
                if not char then return end
                for _, obj in ipairs(char:GetDescendants()) do
                    if obj:IsA("LinearVelocity") then
                        pcall(function() obj:Destroy() end)
                    end
                end
                local hrp = char:FindFirstChild("HumanoidRootPart")
                if hrp then
                    for _, obj in ipairs(hrp:GetChildren()) do
                        if obj:IsA("LinearVelocity") or (obj:IsA("Attachment") and tostring(obj.Name):find("LinVel")) then
                            pcall(function() obj:Destroy() end)
                        end
                    end
                end
            end)
            if (S.aimbotMode or 1) == 2 then
                F.startAimbotBypassAntiBat()
            else
                startAimbot()
            end
        else
            pcall(function() if stopAimbot then stopAimbot() end end)
            pcall(function() if F.stopAimbotBypassAntiBat then F.stopAimbotBypassAntiBat() end end)
        end
    end

    local function isAimbotAnyOn()
        return aimbotEnabled == true or antiBatBypassLockEnabled == true or S.antiBatBypassEnabled == true
    end

    local tpBatConn, tpBatSwingCd, tpBatWasAimbot = nil, false, false

    TP_BAT_MODES = {
        [1] = { name = "Med And Bat", speed = 72, height = 1.15, hitDist = 8.5, doTP = true },
        [2] = { name = "Only Bat", speed = 70, height = 0.9, hitDist = 12, doTP = true },
        [3] = { name = "Bat And Med v2", speed = 78, height = 0.7, hitDist = 7.5, doTP = true, sureHit = true },
    }
    getTPBatMode = function()
        return TP_BAT_MODES[tpBatMode] or TP_BAT_MODES[1]
    end

    local function tpBatSwing()
        if tpBatSwingCd then return end
        if S.autoSwingEnabled == false then return end
        tpBatSwingCd = true
        task.delay(0.08, function() tpBatSwingCd = false end)
        pcall(function()
            local char = LP.Character
            if not char then return end
            local hum = char:FindFirstChildOfClass("Humanoid")
            local bat = findBatTool and findBatTool() or nil
            if not bat then
                for _, t in ipairs(char:GetChildren()) do
                    if t:IsA("Tool") then
                        local n = t.Name:lower()
                        if n:find("bat") or n:find("slap") or n:find("glove") then bat = t; break end
                    end
                end
            end
            if not bat then
                local bp = LP:FindFirstChildOfClass("Backpack") or LP:FindFirstChild("Backpack")
                if bp then
                    for _, t in ipairs(bp:GetChildren()) do
                        if t:IsA("Tool") then
                            local n = t.Name:lower()
                            if n:find("bat") or n:find("slap") or n:find("glove") then bat = t; break end
                        end
                    end
                end
            end
            if not bat or not hum then return end
            if bat.Parent ~= char then pcall(function() hum:EquipTool(bat) end) end
            pcall(function() bat:Activate() end)
            local remote = bat:FindFirstChildOfClass("RemoteEvent") or bat:FindFirstChildWhichIsA("RemoteEvent")
            if remote then pcall(function() remote:FireServer() end) end
        end)
    end

    local function tpBatAntiDie(char, hum)
        -- Anti reset / anti die while TP Bat is active
        pcall(function()
            hum.MaxHealth = math.max(hum.MaxHealth, 100)
            hum.Health = hum.MaxHealth
            hum.BreakJointsOnDeath = false
            hum.RequiresNeck = false
            hum.PlatformStand = false
            hum.Sit = false
            pcall(function()
                hum:SetStateEnabled(Enum.HumanoidStateType.Dead, false)
                hum:SetStateEnabled(Enum.HumanoidStateType.FallingDown, false)
                hum:SetStateEnabled(Enum.HumanoidStateType.Ragdoll, false)
                hum:SetStateEnabled(Enum.HumanoidStateType.Physics, false)
                hum:SetStateEnabled(Enum.HumanoidStateType.GettingUp, true)
                hum:SetStateEnabled(Enum.HumanoidStateType.Running, true)
            end)
            local st = hum:GetState()
            if st == Enum.HumanoidStateType.Dead
                or st == Enum.HumanoidStateType.Physics
                or st == Enum.HumanoidStateType.Ragdoll
                or st == Enum.HumanoidStateType.FallingDown then
                hum:ChangeState(Enum.HumanoidStateType.GettingUp)
                hum:ChangeState(Enum.HumanoidStateType.Running)
            end
            if not char:FindFirstChild("K7TPBatFF") then
                local ff = Instance.new("ForceField")
                ff.Name = "K7TPBatFF"
                ff.Visible = false
                ff.Parent = char
            end
            pcall(function()
                game:GetService("StarterGui"):SetCore("ResetButtonCallback", false)
            end)
        end)
    end

    local function tpBatAntiFling(char, root)
        -- Clamp self velocity so you don't get flung (all TP Bat modes)
        pcall(function()
            local maxLin, maxAng = 70, 12
            local v = root.AssemblyLinearVelocity
            if typeof(v) == "Vector3" and v.Magnitude > maxLin then
                root.AssemblyLinearVelocity = v.Unit * maxLin
            end
            if root.AssemblyAngularVelocity.Magnitude > maxAng then
                root.AssemblyAngularVelocity = Vector3.zero
            end
            -- kill vertical spikes from flings
            local v2 = root.AssemblyLinearVelocity
            if typeof(v2) == "Vector3" and math.abs(v2.Y) > 55 then
                root.AssemblyLinearVelocity = Vector3.new(v2.X, math.clamp(v2.Y, -40, 40), v2.Z)
            end
            for _, p in ipairs(char:GetChildren()) do
                if p:IsA("BasePart") and p ~= root then
                    if p.AssemblyLinearVelocity.Magnitude > maxLin * 1.05 then
                        p.AssemblyLinearVelocity = p.AssemblyLinearVelocity.Unit * maxLin
                    end
                    if p.AssemblyAngularVelocity.Magnitude > maxAng then
                        p.AssemblyAngularVelocity = Vector3.zero
                    end
                end
            end
        end)
    end

    local function tpBatLoopV1()
        -- Med And Bat — tighter stick, prediction, faster re-TP + multi swing
        local modeCfg = (getTPBatMode and getTPBatMode()) or { speed = 68, height = 1.15, hitDist = 9 }
        local spd = modeCfg.speed or 68
        local height = modeCfg.height or 1.15
        local hitDist = modeCfg.hitDist or 9
        return RunService.Heartbeat:Connect(function()
            if not tpBatEnabled then return end
            local char = LP.Character
            if not char then return end
            local root = char:FindFirstChild("HumanoidRootPart")
            local hum = char:FindFirstChildOfClass("Humanoid")
            if not root or not hum or hum.Health <= 0 then return end

            local bat = findBatTool and findBatTool() or nil
            pcall(function()
                if bat and bat.Parent ~= char then hum:EquipTool(bat) end
            end)
            tpBatAntiDie(char, hum)

            local targetRoot = getClosestAliveRoot and getClosestAliveRoot() or nil
            if not targetRoot or not targetRoot.Parent then return end

            local toTarget = (root.Position - targetRoot.Position).Magnitude
            if S.tpBatDistance and S.tpBatDistance > 0 and toTarget > S.tpBatDistance then
                root.AssemblyLinearVelocity = Vector3.zero
                root.AssemblyAngularVelocity = Vector3.zero
                return
            end

            pcall(function()
                if sethiddenproperty then sethiddenproperty(root, "PhysicsRepRootPart", targetRoot) end
            end)

            -- prediction from target velocity
            local tVel = Vector3.zero
            pcall(function() tVel = targetRoot.AssemblyLinearVelocity end)
            local predict = Vector3.new(tVel.X, 0, tVel.Z) * 0.12

            local back = targetRoot.CFrame.LookVector
            local flatBack = Vector3.new(back.X, 0, back.Z)
            local backOff = (flatBack.Magnitude < 0.05) and Vector3.new(0, 0, 2.0) or (-flatBack.Unit * 2.0)
            local targetPos = targetRoot.Position + predict + Vector3.new(0, height, 0) + backOff

            local dist = (root.Position - targetPos).Magnitude
            if dist > hitDist then
                -- hard TP behind target
                root.CFrame = CFrame.new(targetPos, targetRoot.Position + predict)
                root.AssemblyLinearVelocity = Vector3.zero
                root.AssemblyAngularVelocity = Vector3.zero
            elseif dist > 1.6 then
                local dir = targetPos - root.Position
                local flatDir = Vector3.new(dir.X, 0, dir.Z)
                if flatDir.Magnitude > 0.05 then
                    local moveSpd = math.clamp(dist * 12, 40, spd + 15)
                    root.AssemblyLinearVelocity = flatDir.Unit * moveSpd
                        + Vector3.new(0, math.clamp(dir.Y * 4, -25, 35), 0)
                end
                local flat = Vector3.new((targetRoot.Position + predict).X - root.Position.X, 0, (targetRoot.Position + predict).Z - root.Position.Z)
                if flat.Magnitude > 0.08 then
                    root.CFrame = CFrame.new(root.Position, root.Position + flat.Unit)
                end
            else
                -- sticky close range
                local flat = Vector3.new((targetRoot.Position + predict).X - root.Position.X, 0, (targetRoot.Position + predict).Z - root.Position.Z)
                if flat.Magnitude > 0.08 then
                    root.CFrame = CFrame.new(root.Position, root.Position + flat.Unit)
                end
                local desired = targetRoot.Position + predict + Vector3.new(0, height * 0.85, 0) + backOff * 0.85
                root.CFrame = CFrame.new(desired, targetRoot.Position + predict)
                root.AssemblyLinearVelocity = Vector3.new(tVel.X * 0.35, math.max(root.AssemblyLinearVelocity.Y, -8), tVel.Z * 0.35)
            end

            pcall(function()
                local cam = workspace.CurrentCamera
                if cam then
                    cam.CFrame = CFrame.new(cam.CFrame.Position, targetRoot.Position + predict)
                end
            end)

            -- multi-swing for med+bat consistency
            tpBatSwing()
            if bat then
                pcall(function() bat:Activate() end)
                pcall(function()
                    local ev = bat:FindFirstChildWhichIsA("RemoteEvent", true)
                    if ev then ev:FireServer() end
                end)
            end

            tpBatAntiFling(char, root)
            if S.flingOnTPBatEnabled and targetRoot and targetRoot.Parent then
                pcall(function()
                    targetRoot.AssemblyLinearVelocity = targetRoot.AssemblyLinearVelocity * 10000 + Vector3.new(0, 10000, 0)
                end)
            end
        end)
    end

    local function tpBatLoopV2()
        -- Only Bat (improved): stick closer, predict target, spam swing, always anti-fling / anti-reset
        local lastSwing = 0
        return RunService.Heartbeat:Connect(function()
            if not tpBatEnabled then return end
            local char = LP.Character
            if not char then return end
            local root = char:FindFirstChild("HumanoidRootPart")
            local hum = char:FindFirstChildOfClass("Humanoid")
            if not root or not hum or hum.Health <= 0 then return end

            tpBatAntiDie(char, hum)

            -- keep bat equipped
            pcall(function()
                local bat = findBatTool and findBatTool() or nil
                if bat and bat.Parent ~= char then
                    hum:EquipTool(bat)
                end
            end)

            local targetRoot = getClosestAliveRoot and getClosestAliveRoot() or nil
            if not targetRoot or not targetRoot.Parent then
                root.AssemblyAngularVelocity = Vector3.zero
                return
            end

            local dist = (root.Position - targetRoot.Position).Magnitude
            if S.tpBatDistance and S.tpBatDistance > 0 and dist > S.tpBatDistance then
                root.AssemblyLinearVelocity = Vector3.zero
                root.AssemblyAngularVelocity = Vector3.zero
                tpBatAntiFling(char, root)
                return
            end

            pcall(function()
                if sethiddenproperty then
                    sethiddenproperty(root, "PhysicsRepRootPart", targetRoot)
                end
            end)

            -- predict target movement
            local tVel = Vector3.zero
            pcall(function() tVel = targetRoot.AssemblyLinearVelocity end)
            local predict = math.clamp(tVel.Magnitude / 90, 0.04, 0.18)
            local aimPos = targetRoot.Position + (tVel * predict) + Vector3.new(0, 0.55, 0)

            -- stick slightly in front of target (bat range)
            local toTarget = aimPos - root.Position
            local flat = Vector3.new(toTarget.X, 0, toTarget.Z)
            local stand
            if flat.Magnitude > 0.05 then
                stand = aimPos - flat.Unit * 2.0 + Vector3.new(0, 0.35, 0)
            else
                stand = aimPos + Vector3.new(0, 0.35, 2.0)
            end

            dist = (root.Position - stand).Magnitude
            if dist > 8 then
                -- hard TP in
                root.CFrame = CFrame.new(stand, aimPos)
                root.AssemblyLinearVelocity = Vector3.zero
                root.AssemblyAngularVelocity = Vector3.zero
            elseif dist > 1.6 then
                local dir = stand - root.Position
                local fdir = Vector3.new(dir.X, 0, dir.Z)
                local spd = math.clamp(dist * 14, 45, 85)
                if fdir.Magnitude > 0.05 then
                    root.AssemblyLinearVelocity = fdir.Unit * spd + Vector3.new(0, math.clamp(dir.Y * 6, -45, 45), 0)
                end
                root.CFrame = CFrame.new(root.Position, Vector3.new(aimPos.X, root.Position.Y, aimPos.Z))
            else
                -- lock on target, damp velocity
                root.CFrame = CFrame.new(root.Position, Vector3.new(aimPos.X, root.Position.Y, aimPos.Z))
                local v = root.AssemblyLinearVelocity
                root.AssemblyLinearVelocity = Vector3.new(v.X * 0.25, math.clamp(v.Y, -12, 20), v.Z * 0.25)
            end

            pcall(function()
                local cam = workspace.CurrentCamera
                if cam then
                    cam.CFrame = CFrame.new(cam.CFrame.Position, aimPos)
                end
            end)

            -- aggressive swing
            local now = tick()
            if now - lastSwing >= 0.07 then
                lastSwing = now
                tpBatSwing()
            end

            tpBatAntiFling(char, root)

            if S.flingOnTPBatEnabled and targetRoot and targetRoot.Parent then
                pcall(function()
                    local tv = targetRoot.AssemblyLinearVelocity
                    targetRoot.AssemblyLinearVelocity = tv * 10000 + Vector3.new(0, 10000, 0)
                end)
            end
        end)
    end

local function tpBatLoopV3()
        -- V3 Sure Hit (Vynx-style multi-fire + smooth place)
        local sureCD = false
        local function sureHitSwing()
            if S.autoSwingEnabled == false then return end
            if sureCD then return end
            sureCD = true
            pcall(function()
                local bat = findBatTool and findBatTool() or nil
                local char = LP.Character
                if not bat or not char then return end
                local hum = char:FindFirstChildOfClass("Humanoid")
                if bat.Parent ~= char and hum then
                    pcall(function() hum:EquipTool(bat) end)
                end
                for _ = 1, 3 do
                    pcall(function() bat:Activate() end)
                    local ev = bat:FindFirstChildWhichIsA("RemoteEvent")
                    if ev then pcall(function() ev:FireServer() end) end
                end
                local rf = bat:FindFirstChildWhichIsA("RemoteFunction")
                if rf then pcall(function() rf:InvokeServer() end) end
            end)
            task.delay(0.045, function() sureCD = false end)
        end

        local function camLookFlat()
            local cam = workspace.CurrentCamera
            local look
            if cam then
                look = cam.CFrame.LookVector
            else
                local c = LP.Character
                local r = c and c:FindFirstChild("HumanoidRootPart")
                look = r and r.CFrame.LookVector or Vector3.new(0, 0, -1)
            end
            local flat = Vector3.new(look.X, 0, look.Z)
            if flat.Magnitude < 0.05 then
                flat = Vector3.new(0, 0, -1)
            end
            return flat.Unit
        end

        return RunService.Heartbeat:Connect(function()
            if not tpBatEnabled then return end
            local char = LP.Character
            if not char then return end
            local root = char:FindFirstChild("HumanoidRootPart")
            local hum = char:FindFirstChildOfClass("Humanoid")
            if not root or not hum or hum.Health <= 0 then return end

            if tpBatAntiDie then tpBatAntiDie(char, hum) end

            pcall(function()
                local bat = findBatTool and findBatTool() or nil
                if bat and bat.Parent ~= char then hum:EquipTool(bat) end
            end)

            local targetRoot = getClosestAliveRoot and getClosestAliveRoot() or nil
            if not targetRoot or not targetRoot.Parent then return end
            local dist = (root.Position - targetRoot.Position).Magnitude
            if S.tpBatDistance and S.tpBatDistance > 0 and dist > S.tpBatDistance then
                root.AssemblyLinearVelocity = Vector3.zero
                root.AssemblyAngularVelocity = Vector3.zero
                return
            end

            pcall(function()
                if sethiddenproperty then
                    sethiddenproperty(root, "PhysicsRepRootPart", targetRoot)
                end
            end)

            local look = camLookFlat()
            local tVel = targetRoot.AssemblyLinearVelocity or Vector3.zero
            local pred = targetRoot.Position + Vector3.new(tVel.X, 0, tVel.Z) * 0.08
            local standDist = 2.0
            local standPos = pred - look * standDist + Vector3.new(0, 0.55, 0)
            local faceAt = Vector3.new(pred.X, standPos.Y, pred.Z)
            local goal = CFrame.lookAt(standPos, faceAt)
            root.CFrame = root.CFrame:Lerp(goal, 0.62)
            local v = root.AssemblyLinearVelocity
            root.AssemblyLinearVelocity = Vector3.new(v.X * 0.35, v.Y, v.Z * 0.35)

            pcall(function()
                local cam = workspace.CurrentCamera
                if cam then
                    cam.CFrame = CFrame.new(cam.CFrame.Position, targetRoot.Position + Vector3.new(0, 0.5, 0))
                end
            end)

            sureHitSwing()
            if tpBatAntiFling then tpBatAntiFling(char, root) end
            if S.flingOnTPBatEnabled and targetRoot and targetRoot.Parent then
                pcall(function()
                    local tVel = targetRoot.AssemblyLinearVelocity
                    targetRoot.AssemblyLinearVelocity = tVel * 10000 + Vector3.new(0, 10000, 0)
                    task.defer(function()
                        if targetRoot and targetRoot.Parent then
                            -- keep fling impulse; do not restore (flings the opponent)
                        end
                    end)
                end)
            end
        end)
    end

    startTPBat = function()
        if tpBatConn then
            pcall(function() tpBatConn:Disconnect() end)
            tpBatConn = nil
        end
        tpBatEnabled = true
        if tpBatMode ~= 1 and tpBatMode ~= 2 and tpBatMode ~= 3 then tpBatMode = 1 end

        pcall(function()
            if K7 and K7.bodyLockEnabled then
                S._bodyLockPausedByCombat = true
                if K7.stopBodyLock then K7.stopBodyLock() end
            end
        end)

        if aimbotEnabled or (S.antiBatBypassEnabled == true) then
            tpBatWasAimbot = true
            pcall(function() if stopAimbot then stopAimbot() end end)
            pcall(function() if F.stopAimbotBypassAntiBat then F.stopAimbotBypassAntiBat() end end)
        else
            tpBatWasAimbot = false
        end
        if S.autoBatEnabled then
            pcall(function() if S.disableAutoBat then S.disableAutoBat() end end)
        end
        pcall(function() if F.destroyProxy then F.destroyProxy() end end)

        local hum0 = LP.Character and LP.Character:FindFirstChildOfClass("Humanoid")
        if hum0 then
            hum0.AutoRotate = false
            pcall(function()
                hum0.BreakJointsOnDeath = false
                hum0:SetStateEnabled(Enum.HumanoidStateType.Dead, false)
            end)
        end

        if tpBatMode == 3 then
            tpBatConn = tpBatLoopV3()
        elseif tpBatMode == 2 then
            tpBatConn = tpBatLoopV2()
        else
            tpBatConn = tpBatLoopV1()
        end

        if tpBatUIState then tpBatUIState(true) end
        if VS and VS.setTPBatVisual then pcall(function() VS.setTPBatVisual(true) end) end
    end

    stopTPBat = function()
        tpBatEnabled = false
        if tpBatConn then
            pcall(function() tpBatConn:Disconnect() end)
            tpBatConn = nil
        end
        tpBatSwingCd = false
        pcall(function()
            game:GetService("StarterGui"):SetCore("ResetButtonCallback", true)
        end)
        -- remove forcefield
        pcall(function()
            local ch = LP.Character
            local ff = ch and ch:FindFirstChild("K7TPBatFF")
            if ff then ff:Destroy() end
        end)

        local c = LP.Character
        local root = c and c:FindFirstChild("HumanoidRootPart")
        if root then
            root.AssemblyAngularVelocity = Vector3.zero
            pcall(function()
                if sethiddenproperty then sethiddenproperty(root, "PhysicsRepRootPart", root) end
            end)
        end
        pcall(function()
            if c then
                local ff = c:FindFirstChild("K7TPBatFF")
                if ff then ff:Destroy() end
            end
        end)
        local hum2 = c and c:FindFirstChildOfClass("Humanoid")
        if hum2 then
            hum2.AutoRotate = true
            pcall(function()
                hum2.BreakJointsOnDeath = true
                hum2.RequiresNeck = true
                hum2:SetStateEnabled(Enum.HumanoidStateType.Dead, true)
                hum2:SetStateEnabled(Enum.HumanoidStateType.FallingDown, true)
                hum2:SetStateEnabled(Enum.HumanoidStateType.Ragdoll, true)
                hum2:SetStateEnabled(Enum.HumanoidStateType.Physics, true)
            end)
        end

        if tpBatWasAimbot then
            tpBatWasAimbot = false
            task.defer(function()
                if not tpBatEnabled and setAimbotState then
                    setAimbotState(true)
                    if VS and VS.setAimbotVisual then pcall(function() VS.setAimbotVisual(true) end) end
                end
            end)
        end

        if tpBatUIState then tpBatUIState(false) end
        if VS and VS.setTPBatVisual then pcall(function() VS.setTPBatVisual(false) end) end
        task.defer(function()
            pcall(function()
                if S._bodyLockPausedByCombat and not aimbotEnabled and not tpBatEnabled and K7 and K7.startBodyLock then
                    S._bodyLockPausedByCombat = false
                    K7.startBodyLock()
                    if VS and VS.setBodyLockVisual then VS.setBodyLockVisual(true) end
                end
            end)
        end)
    end

    setTPBatState = function(on)
        if on then startTPBat() else stopTPBat() end
    end

    local antiBatBypassLockEnabled = false
    local AceAntiBypassAimbot = { conn = nil, swingCooldown = false, prevAutoRotate = nil }
    local AceAntiBypassAimbotSpeed = 58
    local AceAntiBypassLaggerAimbotSpeed = 40

    local function GetAntiBypassAimbotSpeed()
        if S.laggerToggled then
            return tonumber(AceAntiBypassLaggerAimbotSpeed) or 40
        end
        return tonumber(AceAntiBypassAimbotSpeed) or 58
    end

    local function AntiBypassGetClosest()
        local root = LP.Character and LP.Character:FindFirstChild("HumanoidRootPart")
        if not root then return nil, math.huge end
        local closest, minDist = nil, math.huge
        for _, plr in ipairs(Players:GetPlayers()) do
            if plr ~= LP and plr.Character then
                local tRoot = plr.Character:FindFirstChild("HumanoidRootPart")
                local hum = plr.Character:FindFirstChildOfClass("Humanoid")
                if tRoot and hum and hum.Health > 0 then
                    local dist = (tRoot.Position - root.Position).Magnitude
                    if dist < minDist then
                        minDist = dist
                        closest = tRoot
                    end
                end
            end
        end
        return closest, minDist
    end

    local function AntiBypassFindBat()
        if findBatTool then return findBatTool() end
        local char = LP.Character
        if not char then return nil end
        for _, tool in ipairs(char:GetChildren()) do
            if tool:IsA("Tool") then
                local n = tool.Name:lower()
                if n:find("bat") or n:find("slap") then return tool end
            end
        end
        local bp = LP:FindFirstChild("Backpack")
        if bp then
            for _, tool in ipairs(bp:GetChildren()) do
                if tool:IsA("Tool") then
                    local n = tool.Name:lower()
                    if n:find("bat") or n:find("slap") then return tool end
                end
            end
        end
        return nil
    end

    local function AntiBypassTrySwing()
        if AceAntiBypassAimbot.swingCooldown then return end
        if S.autoSwingEnabled == false then return end
        AceAntiBypassAimbot.swingCooldown = true
        pcall(function()
            local char = LP.Character
            if not char then return end
            local bat = AntiBypassFindBat()
            if not bat then return end
            local hum = char:FindFirstChildOfClass("Humanoid")
            if bat.Parent ~= char and hum then
                pcall(function() hum:EquipTool(bat) end)
            end
            pcall(function() bat:Activate() end)
            local ev = bat:FindFirstChildWhichIsA("RemoteEvent")
            if ev then pcall(function() ev:FireServer() end) end
        end)
        task.delay(0.03, function()
            AceAntiBypassAimbot.swingCooldown = false
        end)
    end

    function F.startAimbotBypassAntiBat()

        pcall(function()
            if tpBatEnabled and stopTPBat then stopTPBat() end
        end)
        pcall(function()
            if aimbotEnabled and stopAimbot then stopAimbot() end
        end)
        pcall(function()
            if S.autoBatEnabled and S.disableAutoBat then S.disableAutoBat() end
        end)
        if AceAntiBypassAimbot.conn then
            pcall(function() AceAntiBypassAimbot.conn:Disconnect() end)
            AceAntiBypassAimbot.conn = nil
        end
        antiBatBypassLockEnabled = true
        S.antiBatBypassEnabled = true
        local hum0 = LP.Character and LP.Character:FindFirstChildOfClass("Humanoid")
        if hum0 then
            AceAntiBypassAimbot.prevAutoRotate = hum0.AutoRotate
            hum0.AutoRotate = false
        end
        pcall(function() F.destroyProxy() end)

        local bypassTick = 0
        local _bpTarget, _bpDist, _bpLastScan = nil, math.huge, 0
        AceAntiBypassAimbot.conn = RunService.Heartbeat:Connect(function()
            if not antiBatBypassLockEnabled then return end
            bypassTick = bypassTick + 1

            if bypassTick % (_IS_MOBILE and 3 or 8) == 0 then return end

            local char = LP.Character
            if not char then return end
            local root = char:FindFirstChild("HumanoidRootPart")
            local hum = char:FindFirstChildOfClass("Humanoid")
            if not root or not hum then return end

            if bypassTick % 5 == 0 then
                local bat = AntiBypassFindBat()
                if bat and bat.Parent ~= char then
                    pcall(function() hum:EquipTool(bat) end)
                end
            end

            local now = os.clock()
            if (now - _bpLastScan) >= (_IS_MOBILE and 0.08 or 0.03) then
                _bpLastScan = now
                _bpTarget, _bpDist = AntiBypassGetClosest()
            end
            local target, targetDist = _bpTarget, _bpDist
            if not target then return end

            local myPos = root.Position
            local targetPos = target.Position
            local direction = targetPos - myPos
            local flatDir = Vector3.new(direction.X, 0, direction.Z)
            if flatDir.Magnitude > 0 then
                flatDir = flatDir.Unit
            else
                flatDir = Vector3.zero
            end

            local chaseSpeed = GetAntiBypassAimbotSpeed()
            local desiredHeight = targetPos.Y + 3.7
            local yVel = (desiredHeight - myPos.Y) * 19.5
            if hum.FloorMaterial ~= Enum.Material.Air then
                yVel = math.max(yVel, 13)
            end
            yVel = math.clamp(yVel, -70, 110)

            local desiredVel = Vector3.new(flatDir.X * chaseSpeed, yVel, flatDir.Z * chaseSpeed)
            root.AssemblyLinearVelocity = root.AssemblyLinearVelocity:Lerp(desiredVel, 0.8)

            local toTarget = targetPos - myPos
            if toTarget.Magnitude > 0.1 then
                local goalCF = CFrame.lookAt(myPos, targetPos)
                local diffCF = root.CFrame:Inverse() * goalCF
                local rx, ry, rz = diffCF:ToEulerAnglesXYZ()
                rx = math.clamp(rx, -2.5, 2.5)
                ry = math.clamp(ry, -2.5, 2.5)
                rz = math.clamp(rz, -2.5, 2.5)
                root.AssemblyAngularVelocity = root.CFrame:VectorToWorldSpace(Vector3.new(rx * 42, ry * 42, rz * 42))
            end

            if targetDist <= 8 then
                AntiBypassTrySwing()
            end
        end)

        if VS and VS.setAntiBatBypassVisual then
            pcall(function() VS.setAntiBatBypassVisual(true) end)
        end
    end

    function F.stopAimbotBypassAntiBat()
        antiBatBypassLockEnabled = false
        S.antiBatBypassEnabled = false
        if AceAntiBypassAimbot.conn then
            pcall(function() AceAntiBypassAimbot.conn:Disconnect() end)
            AceAntiBypassAimbot.conn = nil
        end
        AceAntiBypassAimbot.swingCooldown = false
        local c = LP.Character
        local root = c and c:FindFirstChild("HumanoidRootPart")
        if root then
            pcall(function()
                root.AssemblyAngularVelocity = Vector3.zero
            end)
        end
        local hum = c and c:FindFirstChildOfClass("Humanoid")
        if hum then
            local prev = AceAntiBypassAimbot.prevAutoRotate
            hum.AutoRotate = (prev == nil) and true or prev
        end
        AceAntiBypassAimbot.prevAutoRotate = nil
        if VS and VS.setAntiBatBypassVisual then
            pcall(function() VS.setAntiBatBypassVisual(false) end)
        end
    end

    end

    local cursedInstaReset
    local K7 = {
        fovEnabled = false,
        fovValue = 80,
        lineESPEnabled = false,
        speedESPEnabled = false,
        animPackEnabled = false,
        animPackName = "Adidas Sports",
        animPackIndex = 1,
        headlessEnabled = false,
        korbloxEnabled = false,
        bodyLockEnabled = false,
        bodyLockRadius = 60,
        medusaResetEnabled = false,
        currentSkyTheme = "Off",
        skyThemeIndex = 1,
    }
    do

    local skyThemeTag = "Hub073SkyTheme"
    local currentSkyTheme = "Off"
    local SkyOrder = {"Off","Night","Aurora","Sunset","Galaxy","Cyber","Sakura","Pink Night","Blood Moon","Emerald Dawn","Volcanic","Arctic","Midnight Ocean","Vaporwave","Toxic","Solar Eclipse","Hellscape","Heaven","Storm","Sunrise","Deep Space","Lavender Dream","Inferno","Mint Sky"}
    local SkyPresets = {
        ["Off"]={kind="off"},
        ["Night"]={clock=22,brightness=2,ambient={110,100,130},outAmb={120,110,140},sky={stars=4000,moon=18,sun=0,moonTex=true},atm={dens=0.45,color={120,60,180},decay={60,20,100},glare=0.5,haze=1.2}},
        ["Aurora"]={clock=14,brightness=3,ambient={150,120,150},outAmb={160,130,150},atm={dens=0.55,color={255,80,200},decay={255,20,150},glare=2.5,haze=3},clouds={cover=0.7,dens=0.7,color={255,240,250}}},
        ["Sunset"]={clock=17.2,brightness=2.5,ambient={170,120,100},outAmb={180,130,110},sky={stars=0,sun=25,moon=0},atm={dens=0.5,color={255,130,60},decay={255,80,30},glare=2,haze=2.5},clouds={cover=0.55,dens=0.55,color={255,200,140}}},
        ["Galaxy"]={clock=0,brightness=1.5,ambient={70,60,100},outAmb={80,70,110},sky={stars=10000,moon=30,sun=0},atm={dens=0.15,color={40,20,80},decay={20,10,50},glare=0.3,haze=0.5}},
        ["Cyber"]={clock=21,brightness=2.2,ambient={90,130,170},outAmb={100,140,180},sky={stars=2000,moon=12},atm={dens=0.4,color={0,200,255},decay={150,0,255},glare=2,haze=2},clouds={cover=0.4,dens=0.6,color={100,200,255}}},
        ["Sakura"]={clock=11,brightness=3.5,ambient={170,150,160},outAmb={180,160,170},sky={sun=8},atm={dens=0.3,color={255,200,220},decay={255,170,200},glare=1,haze=1.5},clouds={cover=0.6,dens=0.4,color={255,250,252}}},
        ["Pink Night"]={clock=23,brightness=2.2,ambient={120,60,110},outAmb={140,70,120},sky={stars=5000,moon=22,sun=0,moonTex=true},atm={dens=0.5,color={255,80,180},decay={140,30,100},glare=0.7,haze=1.4}},
        ["Blood Moon"]={clock=22.5,brightness=1.6,ambient={130,40,40},outAmb={150,50,50},sky={stars=1500,moon=28,sun=0,moonTex=true},atm={dens=0.6,color={220,30,30},decay={120,10,10},glare=1.4,haze=2}},
        ["Emerald Dawn"]={clock=6.5,brightness=2.8,ambient={130,170,140},outAmb={140,180,150},sky={sun=18},atm={dens=0.4,color={80,200,140},decay={40,150,90},glare=1.8,haze=2.2}},
        ["Volcanic"]={clock=19,brightness=2,ambient={180,80,40},outAmb={200,90,50},sky={stars=200,sun=12},atm={dens=0.75,color={255,60,0},decay={180,20,0},glare=3,haze=3.5}},
        ["Arctic"]={clock=9,brightness=3.2,ambient={200,220,235},outAmb={210,230,245},sky={sun=10},atm={dens=0.3,color={180,220,255},decay={140,200,240},glare=1.5,haze=1.8}},
        ["Midnight Ocean"]={clock=1.5,brightness=1.7,ambient={60,90,130},outAmb={70,100,140},sky={stars=6000,moon=24,sun=0,moonTex=true},atm={dens=0.5,color={20,60,140},decay={10,30,90},glare=0.6,haze=1.5}},
        ["Vaporwave"]={clock=19.5,brightness=2.4,ambient={180,120,200},outAmb={190,130,210},sky={stars=1000,moon=14},atm={dens=0.45,color={255,100,220},decay={120,60,255},glare=2.2,haze=2.4}},
        ["Toxic"]={clock=13,brightness=2.5,ambient={140,180,80},outAmb={150,190,90},atm={dens=0.55,color={100,220,40},decay={60,150,20},glare=1.8,haze=2.6}},
        ["Solar Eclipse"]={clock=12,brightness=0.9,ambient={50,40,60},outAmb={60,50,70},sky={stars=3500,sun=22},atm={dens=0.5,color={255,140,40},decay={30,20,40},glare=2.8,haze=1.8}},
        ["Hellscape"]={clock=18,brightness=1.8,ambient={200,60,30},outAmb={220,70,40},sky={stars=100,sun=30},atm={dens=0.85,color={255,30,0},decay={120,0,0},glare=3.5,haze=4}},
        ["Heaven"]={clock=12,brightness=4,ambient={240,235,210},outAmb={250,245,220},sky={sun=16},atm={dens=0.25,color={255,250,220},decay={255,240,200},glare=3,haze=1.5}},
        ["Storm"]={clock=15,brightness=1.4,ambient={90,90,110},outAmb={100,100,120},sky={sun=6},atm={dens=0.65,color={80,90,120},decay={40,50,80},glare=0.5,haze=3}},
        ["Sunrise"]={clock=6.2,brightness=2.8,ambient={220,180,130},outAmb={230,190,140},sky={sun=22},atm={dens=0.45,color={255,180,100},decay={255,140,80},glare=2.4,haze=2.2}},
        ["Deep Space"]={clock=0,brightness=1,ambient={30,25,50},outAmb={40,35,60},sky={stars=15000,moon=0,sun=0},atm={dens=0.08,color={15,5,40},decay={5,0,20},glare=0.2,haze=0.3}},
        ["Lavender Dream"]={clock=18.5,brightness=2.6,ambient={180,160,220},outAmb={190,170,230},sky={stars=800,moon=16},atm={dens=0.4,color={200,160,255},decay={160,120,220},glare=1.4,haze=1.8}},
        ["Inferno"]={clock=17.5,brightness=2.2,ambient={220,100,40},outAmb={235,110,50},sky={sun=26},atm={dens=0.6,color={255,90,20},decay={200,40,0},glare=3,haze=3.2}},
        ["Mint Sky"]={clock=10,brightness=3.2,ambient={180,230,210},outAmb={190,240,220},sky={sun=10},atm={dens=0.32,color={150,255,210},decay={100,220,180},glare=1.6,haze=1.6}},
    }
    local function skyRGB(t) return Color3.fromRGB(t[1],t[2],t[3]) end
    local function applySkyTheme(mode)
        if S.noSkyEnabled and mode ~= "Off" and mode ~= nil then
            mode = "Off"
        end
        currentSkyTheme = mode or "Off"
        K7.currentSkyTheme = currentSkyTheme
        for _,child in ipairs(Lighting:GetChildren()) do
            if child:GetAttribute(skyThemeTag) then pcall(function() child:Destroy() end) end
        end
        local terrain = workspace:FindFirstChildOfClass("Terrain")
        if terrain then
            for _,child in ipairs(terrain:GetChildren()) do
                if child:GetAttribute(skyThemeTag) then pcall(function() child:Destroy() end) end
            end
        end
        local preset = SkyPresets[mode]
        if not preset or preset.kind == "off" then
            pcall(function()
                for _, child in ipairs(Lighting:GetChildren()) do
                    if child:IsA("Sky") or child:IsA("Atmosphere") or child:GetAttribute(skyThemeTag) then
                        child:Destroy()
                    end
                end
                local terrain = workspace:FindFirstChildOfClass("Terrain")
                if terrain then
                    for _, child in ipairs(terrain:GetChildren()) do
                        if child:IsA("Clouds") or child:GetAttribute(skyThemeTag) then
                            child:Destroy()
                        end
                    end
                end
            end)
            Lighting.ClockTime = 14
            Lighting.Brightness = 2.2
            Lighting.OutdoorAmbient = Color3.fromRGB(140, 140, 140)
            Lighting.Ambient = Color3.fromRGB(128, 128, 128)
            Lighting.FogStart = 0
            Lighting.FogEnd = 100000
            Lighting.FogColor = Color3.fromRGB(192, 192, 192)
            Lighting.GlobalShadows = true
            return
        end
        Lighting.FogStart = 0; Lighting.FogEnd = 100000; Lighting.GlobalShadows = true
        Lighting.ClockTime = preset.clock or 14
        Lighting.Brightness = preset.brightness or 2
        if preset.outAmb then Lighting.OutdoorAmbient = skyRGB(preset.outAmb) end
        if preset.ambient then Lighting.Ambient = skyRGB(preset.ambient) end
        if preset.sky then
            local skyInst = Instance.new("Sky")
            skyInst:SetAttribute(skyThemeTag, true)
            if preset.sky.stars then skyInst.StarCount = preset.sky.stars end
            if preset.sky.moon then skyInst.MoonAngularSize = preset.sky.moon end
            if preset.sky.sun then skyInst.SunAngularSize = preset.sky.sun end
            if preset.sky.moonTex then skyInst.MoonTextureId = "rbxasset://sky/moon.jpg" end
            skyInst.Parent = Lighting
        end
        if preset.atm then
            local atm = Instance.new("Atmosphere")
            atm:SetAttribute(skyThemeTag, true)
            atm.Density = preset.atm.dens or 0.3
            atm.Color = skyRGB(preset.atm.color)
            atm.Decay = skyRGB(preset.atm.decay)
            atm.Glare = preset.atm.glare or 1
            atm.Haze = preset.atm.haze or 1
            atm.Parent = Lighting
        end
        if preset.clouds and terrain then
            local clouds = Instance.new("Clouds")
            clouds:SetAttribute(skyThemeTag, true)
            clouds.Cover = preset.clouds.cover or 0.5
            clouds.Density = preset.clouds.dens or 0.5
            clouds.Color = skyRGB(preset.clouds.color)
            clouds.Parent = terrain
        end
    end
    local skyThemeIndex = 1
    local function cycleSkyTheme(dir)
        skyThemeIndex = skyThemeIndex + (dir or 1)
        if skyThemeIndex < 1 then skyThemeIndex = #SkyOrder end
        if skyThemeIndex > #SkyOrder then skyThemeIndex = 1 end
        applySkyTheme(SkyOrder[skyThemeIndex])
        K7.skyThemeIndex = skyThemeIndex
        return SkyOrder[skyThemeIndex]
    end

    _G._Hub073FOV = _G._Hub073FOV or 70
    local fovEnabled = false
    local fovValue = 70
    local fovConn = nil
    local stretchRezEnabled = false
    local stretchRezConn, stretchFovConn = nil, nil
    local stretchFOV = 120
    _G.State = _G.State or {}
    State = _G.State
    State.stretchedResEnabled = false
    State.stretchFOV = 120

    local STRETCH_NAME = "Hub073_Stretch"

    local function enableStretchRez()
        S.stretchRezEnabled = true
        State.stretchedResEnabled = true
        pcall(function() RunService:UnbindFromRenderStep(STRETCH_NAME) end)
        if S.stretchRezConn then pcall(function() S.stretchRezConn:Disconnect() end); S.stretchRezConn = nil end
        if stretchFovConn then pcall(function() stretchFovConn:Disconnect() end); stretchFovConn = nil end
        pcall(function()
            RunService:BindToRenderStep(STRETCH_NAME, Enum.RenderPriority.Last.Value - 1, function()
                if not S.stretchRezEnabled then return end
                local cam = workspace.CurrentCamera
                if cam then
                    local sy = math.clamp(tonumber(S.stretchRezAmount) or 0.7, 0.05, 1)
                    cam.CFrame = cam.CFrame * CFrame.new(0, 0, 0, 1, 0, 0, 0, 0.7, 0, 0, 0, 1)
                end
            end)
        end)
    end

    local function disableStretchRez()
        S.stretchRezEnabled = false
        State.stretchedResEnabled = false
        pcall(function() RunService:UnbindFromRenderStep(STRETCH_NAME) end)
        if S.stretchRezConn then pcall(function() S.stretchRezConn:Disconnect() end); S.stretchRezConn = nil end
        if stretchFovConn then pcall(function() stretchFovConn:Disconnect() end); stretchFovConn = nil end
        pcall(function()
            if workspace.CurrentCamera then
                workspace.CurrentCamera.FieldOfView = _G._Hub073FOV or (K7 and K7.fovValue) or 70
            end
        end)
    end

    local function applyFOV()
        pcall(function() RunService:UnbindFromRenderStep("Hub073FOV") end)
        if fovConn then fovConn:Disconnect(); fovConn = nil end
        if not K7.fovEnabled then
            local target = _G._Hub073FOV or 70
            if workspace.CurrentCamera and not State.stretchedResEnabled then
                workspace.CurrentCamera.FieldOfView = target
            end
            return
        end
        local cam = workspace.CurrentCamera
        local target = K7.fovValue or _G._Hub073FOV or 70
        _G._Hub073FOV = target
        if cam and not State.stretchedResEnabled then cam.FieldOfView = target end
        pcall(function()
            RunService:BindToRenderStep("Hub073FOV", Enum.RenderPriority.Camera.Value + 1, function()
                if not K7.fovEnabled then return end
                if State.stretchedResEnabled then return end
                local c = workspace.CurrentCamera
                if c then c.FieldOfView = K7.fovValue or _G._Hub073FOV or 70 end
            end)
        end)
        fovConn = RunService.Heartbeat:Connect(function()
            if not _perfEveryN(3, 1) then return end
            if not K7.fovEnabled then return end
            if State.stretchedResEnabled then return end
            local c = workspace.CurrentCamera
            if c then c.FieldOfView = K7.fovValue or _G._Hub073FOV or 70 end
        end)
    end

    do
        local _Hub073FOVPropConn = nil
        local function _attachFOVLock(cam)
            if not cam then return end
            if _Hub073FOVPropConn then pcall(function() _Hub073FOVPropConn:Disconnect() end) end
            pcall(function()
                if not State.stretchedResEnabled then
                    cam.FieldOfView = _G._Hub073FOV or 70
                end
            end)
            _Hub073FOVPropConn = cam:GetPropertyChangedSignal("FieldOfView"):Connect(function()
                local target = _G._Hub073FOV or 70
                if not State.stretchedResEnabled and cam.FieldOfView ~= target then
                    pcall(function() cam.FieldOfView = target end)
                end
            end)
        end
        pcall(function()
            if workspace.CurrentCamera then _attachFOVLock(workspace.CurrentCamera) end
            workspace:GetPropertyChangedSignal("CurrentCamera"):Connect(function()
                task.wait()
                pcall(function() _attachFOVLock(workspace.CurrentCamera) end)
            end)
            if LP then
                LP.CharacterAdded:Connect(function()
                    task.wait(0.3)
                    pcall(function() _attachFOVLock(workspace.CurrentCamera) end)
                end)
            end
        end)
    end

    local headlessEnabled, korbloxEnabled = false, false
    local HEADLESS_MESH_ID = "rbxassetid://1095708"

    local function applyHeadless(char, enabled)
        if not char then return end
        local head = char:FindFirstChild("Head")
        if not head then return end
        if enabled then
            head.Transparency = 1
            head.CanCollide = false
            local face = head:FindFirstChild("face")
            if face then face:Destroy() end
            for _, c in ipairs(head:GetChildren()) do
                if c:IsA("SpecialMesh") and c.Name == "HeadlessMesh" then c:Destroy() end
            end
            local mesh = Instance.new("SpecialMesh")
            mesh.MeshType = Enum.MeshType.FileMesh
            mesh.MeshId = HEADLESS_MESH_ID
            mesh.Scale = Vector3.new(0.001, 0.001, 0.001)
            mesh.Name = "HeadlessMesh"
            mesh.Parent = head
        else
            head.Transparency = 0
            head.CanCollide = true
            for _, c in ipairs(head:GetChildren()) do
                if c:IsA("SpecialMesh") and c.Name == "HeadlessMesh" then c:Destroy() end
            end
        end
    end

    local KORBLOX_ASSETS = {
        ["Left Leg"] = {
            id = "rbxassetid://139607673",
            targetBodyPart = "LeftUpperLeg",
            partsToHide = {"LeftUpperLeg", "LeftLowerLeg", "LeftFoot", "Left Leg"},
            scale = Vector3.new(1, 1, 1),
            offset = CFrame.new(),
        },
        ["Right Leg"] = {
            id = "rbxassetid://139607718",
            targetBodyPart = "RightUpperLeg",
            partsToHide = {"RightUpperLeg", "RightLowerLeg", "RightFoot", "Right Leg"},
            scale = Vector3.new(1, 1, 1),
            offset = CFrame.new(),
        },
    }
    local korbloxSide = 1

    local function clearKorbloxAttachments(char)
        if not char then return end
        for _, name in ipairs({"Korblox_LeftLeg", "Korblox_RightLeg", "Korblox_Left Leg", "Korblox_Right Leg"}) do
            local old = char:FindFirstChild(name)
            if old then pcall(function() old:Destroy() end) end
        end
        for _, child in ipairs(char:GetChildren()) do
            if child.Name:find("^Korblox_") then
                pcall(function() child:Destroy() end)
            end
        end
        for _, partName in ipairs({
            "LeftUpperLeg", "LeftLowerLeg", "LeftFoot", "Left Leg",
            "RightUpperLeg", "RightLowerLeg", "RightFoot", "Right Leg",
        }) do
            local limb = char:FindFirstChild(partName)
            if limb and limb:IsA("BasePart") then
                limb.Transparency = 0
            end
        end
    end

    local function attachKorbloxMesh(char, itemName, config)
        if not char then return false, "No character" end
        local targetPart = char:FindFirstChild(config.targetBodyPart)
        if not targetPart then
            if itemName == "Left Leg" then
                targetPart = char:FindFirstChild("Left Leg")
            elseif itemName == "Right Leg" then
                targetPart = char:FindFirstChild("Right Leg")
            end
        end
        if not targetPart then return false, "Target part missing" end

        local safeName = "Korblox_" .. itemName:gsub("%s+", "")
        local oldAsset = char:FindFirstChild(safeName)
        if oldAsset then pcall(function() oldAsset:Destroy() end) end

        for _, partName in ipairs(config.partsToHide) do
            local limb = char:FindFirstChild(partName)
            if limb and limb:IsA("BasePart") then
                limb.Transparency = 1
            end
        end

        local success, objects = pcall(function()
            return game:GetObjects(config.id)
        end)
        if not success or not objects or #objects == 0 then
            return false, "Asset fetch failed"
        end

        local assetModel = objects[1]
        assetModel.Name = safeName

        local mainMesh = assetModel:IsA("BasePart") and assetModel or assetModel:FindFirstChildWhichIsA("BasePart", true)
        if not mainMesh then
            return false, "No MeshPart in asset"
        end

        pcall(function()
            mainMesh.Size = mainMesh.Size * (config.scale or Vector3.new(1,1,1))
            mainMesh.CanCollide = false
            mainMesh.Anchored = false
            mainMesh.CFrame = targetPart.CFrame * (config.offset or CFrame.new())
        end)

        local weld = Instance.new("WeldConstraint")
        weld.Part0 = targetPart
        weld.Part1 = mainMesh
        weld.Parent = mainMesh

        assetModel.Parent = char
        return true
    end

    local function applyKorblox(char, enabled)
        if not char then return end
        clearKorbloxAttachments(char)
        if not enabled then return end

        local sides = {}
        local side = korbloxSide or 1
        if side == 2 then
            sides = {"Left Leg"}
        elseif side == 3 then
            sides = {"Left Leg", "Right Leg"}
        else
            sides = {"Right Leg"}
        end

        for _, itemName in ipairs(sides) do
            local config = KORBLOX_ASSETS[itemName]
            if config then
                local ok, err = attachKorbloxMesh(char, itemName, config)
                if not ok then
                    --warn("[073 Hub] Korblox equip failed (" .. itemName .. "): " .. tostring(err))
                end
            end
        end
    end

    local bodyLockEnabled = false
    local bodyLockRadius = 60
    local bodyLockConn = nil
    local function getNearestBodyLockTarget()
        local root = LP.Character and LP.Character:FindFirstChild("HumanoidRootPart")
        if not root then return nil end
        local nearest, shortest = nil, math.huge
        for _, plr in ipairs(Players:GetPlayers()) do
            if plr ~= LP and plr.Character then
                local tr = plr.Character:FindFirstChild("HumanoidRootPart")
                local hum = plr.Character:FindFirstChildOfClass("Humanoid")
                if tr and hum and hum.Health > 0 then
                    local d = (tr.Position - root.Position).Magnitude
                    if d <= (K7.bodyLockRadius or bodyLockRadius or 60) and d < shortest then
                        shortest = d
                        nearest = plr
                    end
                end
            end
        end
        return nearest
    end
    local function isNearBrainrot(root, radius)
        if not root then return false end
        radius = radius or 5
        local ok, parts = pcall(function()
            return workspace:GetPartBoundsInRadius(root.Position, radius)
        end)
        if not ok or not parts then return false end
        for _, p in ipairs(parts) do
            if p and p:IsA("BasePart") then
                local model = p:FindFirstAncestorOfClass("Model")
                if model and model ~= LP.Character then
                    if not Players:GetPlayerFromCharacter(model) then
                        local n = string.lower(model.Name)
                        if n:find("brainrot", 1, true) or n:find("animal", 1, true) or n:find("steal", 1, true) then
                            return true
                        end
                        local par = model.Parent
                        if par then
                            local pn = string.lower(par.Name)
                            if pn:find("brainrot", 1, true) or pn:find("animals", 1, true) then
                                return true
                            end
                        end
                    end
                end
            end
        end
        return false
    end

    local function startBodyLock()
        if bodyLockConn then

            K7.bodyLockEnabled = true
            bodyLockEnabled = true
            S._bodyLockWanted = true
            return
        end
        K7.bodyLockEnabled = true
        bodyLockEnabled = true
        S._bodyLockWanted = true
        S._bodyLockPausedByBrainrot = false
        local _blCachedTarget = nil
        local _blLastScan = 0
        local _blLastBrain = 0
        local _blNearBrain = false
        bodyLockConn = RunService.Heartbeat:Connect(function()
            if not K7.bodyLockEnabled then return end

            if aimbotEnabled or tpBatEnabled or antiBatBypassLockEnabled then return end
            local char = LP.Character
            local myRoot = char and char:FindFirstChild("HumanoidRootPart")
            local humanoid = char and char:FindFirstChildOfClass("Humanoid")
            if not myRoot or not humanoid or humanoid.Health <= 0 then return end
            local now = os.clock()

            local brainInterval = _IS_MOBILE and 0.18 or 0.08
            if (now - _blLastBrain) >= brainInterval then
                _blLastBrain = now
                local holding, near = false, false
                pcall(function() holding = F.isCarryingBrainrot and F.isCarryingBrainrot(char) == true end)
                pcall(function() near = isNearBrainrot and isNearBrainrot(myRoot, 5) == true end)
                _blNearBrain = holding or near
            end
            if _blNearBrain then
                S._bodyLockPausedByBrainrot = true
                humanoid.AutoRotate = true
                return
            end

            local scanInterval = _IS_MOBILE and 0.12 or 0.05
            if (now - _blLastScan) >= scanInterval then
                _blLastScan = now
                _blCachedTarget = getNearestBodyLockTarget()
            end
            local target = _blCachedTarget
            if target and target.Character and target.Character:FindFirstChild("HumanoidRootPart") then
                local targetPos = target.Character.HumanoidRootPart.Position
                local myPos = myRoot.Position
                local offset = Vector3.new(targetPos.X, myPos.Y, targetPos.Z) - myPos
                if offset.Magnitude > 0.1 then
                    humanoid.AutoRotate = false
                    local lookDir = offset.Unit
                    local currentDir = myRoot.CFrame.LookVector
                    local cross = currentDir:Cross(lookDir)
                    local currentVel = myRoot.AssemblyAngularVelocity
                    myRoot.AssemblyAngularVelocity = Vector3.new(currentVel.X, cross.Y * 40, currentVel.Z)
                end
            else
                humanoid.AutoRotate = true
            end
        end)
    end
    local function stopBodyLock(fromUser)
        K7.bodyLockEnabled = false
        bodyLockEnabled = false
        if bodyLockConn then bodyLockConn:Disconnect(); bodyLockConn = nil end
        local hum = LP.Character and LP.Character:FindFirstChildOfClass("Humanoid")
        if hum then hum.AutoRotate = true end
        if fromUser == true then
            S._bodyLockWanted = false
            S._bodyLockPausedByBrainrot = false
        end
    end

    if not S._bodyLockBrainrotWatcher then
        S._bodyLockBrainrotWatcher = true
        RunService.Heartbeat:Connect(function()
            if not S._bodyLockWanted then return end
            if S._bodyLockPausedByCombat then return end
            if aimbotEnabled or tpBatEnabled then return end
            local char = LP.Character
            if not char then return end
            local root = char:FindFirstChild("HumanoidRootPart")
            if not root then return end
            local holding, near = false, false
            pcall(function() holding = F.isCarryingBrainrot and F.isCarryingBrainrot(char) == true end)
            pcall(function() near = isNearBrainrot and isNearBrainrot(root, 5) == true end)
            if holding or near then
                S._bodyLockPausedByBrainrot = true
                return
            end

            if S._bodyLockPausedByBrainrot or not bodyLockConn then
                S._bodyLockPausedByBrainrot = false
                if not bodyLockConn then
                    pcall(startBodyLock)
                else
                    K7.bodyLockEnabled = true
                    bodyLockEnabled = true
                end
                if VS and VS.setBodyLockVisual then pcall(function() VS.setBodyLockVisual(true) end) end
            end
        end)
    end

    local medusaResetEnabled = false
    local medusaResetConns = {}
    local medusaResetDebounce = false
    local function onPartAnchoredForReset(part)
        return part:GetPropertyChangedSignal("Anchored"):Connect(function()
            if part.Anchored and part.Transparency == 1 and part.Name ~= "HumanoidRootPart" then
                if medusaResetDebounce or not K7.medusaResetEnabled then return end
                medusaResetDebounce = true
                task.spawn(function()
                    if cursedInstaReset then cursedInstaReset() end
                    task.wait(1.5)
                    medusaResetDebounce = false
                end)
            end
        end)
    end
    local function startMedusaReset()
        for _, c in pairs(medusaResetConns) do pcall(function() c:Disconnect() end) end
        medusaResetConns = {}
        local char = LP.Character
        if not char then return end
        for _, part in ipairs(char:GetDescendants()) do
            if part:IsA("BasePart") then
                table.insert(medusaResetConns, onPartAnchoredForReset(part))
            end
        end
        table.insert(medusaResetConns, char.DescendantAdded:Connect(function(part)
            if part:IsA("BasePart") then
                table.insert(medusaResetConns, onPartAnchoredForReset(part))
            end
        end))
    end
    local function stopMedusaReset()
        for _, c in pairs(medusaResetConns) do pcall(function() c:Disconnect() end) end
        medusaResetConns = {}
        medusaResetDebounce = false
    end

    local lineESPEnabled = false
    local speedESPEnabled = false
    local PlayerESP = { enabled = false, conns = {}, playerData = {} }
    local TracerESP = { drawings = {}, conn = nil, gui = nil }

    local function startPlayerESP()
        if PlayerESP.enabled then return end
        PlayerESP.enabled = true
        local function cleanup(plr)
            local d = PlayerESP.playerData[plr]
            if not d then return end
            pcall(function() if d.highlight then d.highlight:Destroy() end end)
            pcall(function() if d.billboard then d.billboard:Destroy() end end)
            for _, c in ipairs(d.conns or {}) do pcall(function() c:Disconnect() end) end
            PlayerESP.playerData[plr] = nil
        end
        local function setupPlayer(plr, char)
            if not PlayerESP.enabled or plr == LP then return end
            cleanup(plr)
            char = char or plr.Character
            if not char then return end
            local hrp = char:FindFirstChild("HumanoidRootPart")
            local head = char:FindFirstChild("Head") or hrp
            if not hrp then return end
            local hl = Instance.new("Highlight")
            hl.Name = "SevenUpPlayerESP"
            hl.Adornee = char
            hl.DepthMode = Enum.HighlightDepthMode.AlwaysOnTop
            local acc = S.themeColor or Color3.fromRGB(230, 230, 235)
            hl.FillColor = acc
            hl.FillTransparency = 0.72
            hl.OutlineColor = acc
            hl.OutlineTransparency = 0.08
            hl.Parent = char
            local bb = Instance.new("BillboardGui")
            bb.Name = "SevenUpPlayerESP_BB"
            bb.Adornee = head
            bb.Size = UDim2.new(0, 90, 0, 70)
            bb.StudsOffset = Vector3.new(0, 3.4, 0)
            bb.AlwaysOnTop = true
            bb.MaxDistance = 600
            local avatarFrame = Instance.new("Frame", bb)
            avatarFrame.Name = "Avatar"
            avatarFrame.Size = UDim2.new(0, 36, 0, 36)
            avatarFrame.Position = UDim2.new(0.5, -18, 0, 0)
            avatarFrame.BackgroundColor3 = Color3.fromRGB(20, 20, 22)
            avatarFrame.BorderSizePixel = 0
            Instance.new("UICorner", avatarFrame).CornerRadius = UDim.new(1, 0)
            local avStroke = Instance.new("UIStroke", avatarFrame)
            avStroke.Name = "EspAvatarStroke"
            avStroke.Color = acc
            avStroke.Thickness = 1.2
            avStroke.Transparency = 0.15
            local avatar = Instance.new("ImageLabel", avatarFrame)
            avatar.Size = UDim2.new(1, 0, 1, 0)
            avatar.BackgroundTransparency = 1
            avatar.Image = ""
            avatar.ScaleType = Enum.ScaleType.Crop
            Instance.new("UICorner", avatar).CornerRadius = UDim.new(1, 0)
            task.spawn(function()
                local ok, thumb = pcall(function()
                    return Players:GetUserThumbnailAsync(plr.UserId, Enum.ThumbnailType.HeadShot, Enum.ThumbnailSize.Size48x48)
                end)
                if ok and thumb and avatar and avatar.Parent then
                    avatar.Image = thumb
                end
            end)
            local lbl = Instance.new("TextLabel", bb)
            lbl.Name = "EspNameLabel"
            lbl.Size = UDim2.new(1, 0, 0, 28)
            lbl.Position = UDim2.new(0, 0, 0, 38)
            lbl.BackgroundTransparency = 1
            lbl.Text = plr.DisplayName or plr.Name
            lbl.TextColor3 = acc
            lbl.Font = Enum.Font.GothamBold
            lbl.TextSize = 12
            lbl.TextStrokeTransparency = 0.25
            lbl.TextYAlignment = Enum.TextYAlignment.Top
            pcall(function()
                bb.Parent = game:GetService("CoreGui")
            end)
            if not bb.Parent then
                pcall(function() bb.Parent = LP:FindFirstChild("PlayerGui") end)
            end
            local lastEspUp = 0
            local conn = RunService.Heartbeat:Connect(function()
                if not PlayerESP.enabled or not hrp.Parent then return end
                local now = os.clock()
                local interval = _IS_MOBILE and 0.22 or 0.08
                if (now - lastEspUp) < interval then return end
                lastEspUp = now
                local v = hrp.AssemblyLinearVelocity
                local spd = Vector3.new(v.X, 0, v.Z).Magnitude
                local col = S.themeColor or Color3.fromRGB(230, 230, 235)
                lbl.Text = string.format("%s\n%.0f sps", plr.DisplayName or plr.Name, spd)
                lbl.TextColor3 = col
                if avStroke and avStroke.Parent then avStroke.Color = col end
                if hl and hl.Parent then
                    hl.FillColor = col
                    hl.OutlineColor = col
                end
            end)
            PlayerESP.playerData[plr] = { highlight = hl, billboard = bb, label = lbl, avStroke = avStroke, conns = { conn } }
        end
        for _, plr in ipairs(Players:GetPlayers()) do
            if plr ~= LP then
                table.insert(PlayerESP.conns, plr.CharacterAdded:Connect(function(c) task.defer(setupPlayer, plr, c) end))
                if plr.Character then task.defer(setupPlayer, plr, plr.Character) end
            end
        end
        table.insert(PlayerESP.conns, Players.PlayerAdded:Connect(function(plr)
            table.insert(PlayerESP.conns, plr.CharacterAdded:Connect(function(c) task.defer(setupPlayer, plr, c) end))
        end))
        table.insert(PlayerESP.conns, Players.PlayerRemoving:Connect(cleanup))
    end

    local function stopPlayerESP()
        PlayerESP.enabled = false
        for _, c in ipairs(PlayerESP.conns or {}) do pcall(function() c:Disconnect() end) end
        PlayerESP.conns = {}
        for plr, d in pairs(PlayerESP.playerData) do
            pcall(function() if d.highlight then d.highlight:Destroy() end end)
            pcall(function() if d.billboard then d.billboard:Destroy() end end)
            for _, c in ipairs(d.conns or {}) do pcall(function() c:Disconnect() end) end
        end
        PlayerESP.playerData = {}
    end

    local function ensureTracerGui()
        if TracerESP.gui and TracerESP.gui.Parent then return TracerESP.gui end
        local g = Instance.new("ScreenGui")
        g.Name = "Hub073TracerESP"
        g.IgnoreGuiInset = true
        g.DisplayOrder = 50
        g.ResetOnSpawn = false
        pcall(function() g.Parent = game:GetService("CoreGui") end)
        if not g.Parent then
            pcall(function() g.Parent = LP:FindFirstChild("PlayerGui") end)
        end
        TracerESP.gui = g
        return g
    end

    local function tracerCleanup(plr)
        local d = TracerESP.drawings[plr]
        if d then
            pcall(function() if d.line then d.line:Destroy() end end)
            TracerESP.drawings[plr] = nil
        end
    end

    local function startTracerESP()
        if TracerESP.conn then
            pcall(function() TracerESP.conn:Disconnect() end)
            TracerESP.conn = nil
        end
        for plr in pairs(TracerESP.drawings) do tracerCleanup(plr) end
        ensureTracerGui()
        S.showTracerEnabled = true
        TracerESP.conn = RunService.Heartbeat:Connect(function()
            if not S.showTracerEnabled then
                for plr in pairs(TracerESP.drawings) do tracerCleanup(plr) end
                return
            end

            if not _perfEveryN(3, 1) then return end
            if not TracerESP.gui or not TracerESP.gui.Parent then return end
            local myChar = LP.Character
            local myRoot = myChar and myChar:FindFirstChild("HumanoidRootPart")
            if not myRoot then return end
            local cam = workspace.CurrentCamera
            if not cam then return end
            local seen = {}
            for _, plr in ipairs(Players:GetPlayers()) do
                if plr ~= LP and plr.Character then
                    local tr = plr.Character:FindFirstChild("HumanoidRootPart")
                    if tr then
                        seen[plr] = true
                        local sp, son = cam:WorldToViewportPoint(myRoot.Position)
                        local ep, eon = cam:WorldToViewportPoint(tr.Position)
                        if son and eon then
                            local line = TracerESP.drawings[plr] and TracerESP.drawings[plr].line
                            if not line then
                                line = Instance.new("Frame")
                                line.Name = "Tracer_" .. plr.Name
                                line.AnchorPoint = Vector2.new(0.5, 0.5)
                                line.BorderSizePixel = 0
                                line.BackgroundColor3 = S.themeColor or Color3.fromRGB(230, 230, 235)
                                line.BackgroundTransparency = 0.15
                                line.Parent = TracerESP.gui
                                TracerESP.drawings[plr] = { line = line }
                            end
                            local dx, dy = ep.X - sp.X, ep.Y - sp.Y
                            local len = math.sqrt(dx * dx + dy * dy)
                            line.BackgroundColor3 = S.themeColor or Color3.fromRGB(230, 230, 235)
                            line.Size = UDim2.fromOffset(math.max(1, len), 1)
                            line.Position = UDim2.fromOffset((sp.X + ep.X) / 2, (sp.Y + ep.Y) / 2)
                            line.Rotation = math.deg(math.atan2(dy, dx))
                            line.Visible = true
                        else
                            tracerCleanup(plr)
                        end
                    end
                end
            end
            for plr in pairs(TracerESP.drawings) do
                if not seen[plr] then tracerCleanup(plr) end
            end
        end)
    end

    local function stopTracerESP()
        S.showTracerEnabled = false
        if TracerESP.conn then TracerESP.conn:Disconnect(); TracerESP.conn = nil end
        for plr in pairs(TracerESP.drawings) do tracerCleanup(plr) end
        if TracerESP.gui then pcall(function() TracerESP.gui:Destroy() end); TracerESP.gui = nil end
    end

    local function refreshESP()
        stopPlayerESP()
        if not S.showTracerEnabled then
            stopTracerESP()
        end
        if not K7 then return end
        if K7.lineESPEnabled or K7.speedESPEnabled then
            startPlayerESP()
        end
        if S.showTracerEnabled then
            startTracerESP()
        end
    end

    function F.applyEspTheme(col)
        col = col or S.themeColor or Color3.fromRGB(230, 230, 235)
        S.themeColor = col
        pcall(function()
            for _, d in pairs(PlayerESP.playerData or {}) do
                if d.highlight and d.highlight.Parent then
                    d.highlight.FillColor = col
                    d.highlight.OutlineColor = col
                end
                if d.label and d.label.Parent then
                    d.label.TextColor3 = col
                end
                if d.avStroke and d.avStroke.Parent then
                    d.avStroke.Color = col
                end
                if d.billboard and d.billboard.Parent then
                    for _, desc in ipairs(d.billboard:GetDescendants()) do
                        if desc:IsA("TextLabel") then
                            desc.TextColor3 = col
                        elseif desc:IsA("UIStroke") then
                            desc.Color = col
                        end
                    end
                end
            end
        end)
        pcall(function()
            for _, d in pairs(TracerESP.drawings or {}) do
                if d.line and d.line.Parent then
                    d.line.BackgroundColor3 = col
                end
            end
            if TracerESP.gui and TracerESP.gui.Parent then
                for _, ch in ipairs(TracerESP.gui:GetChildren()) do
                    if ch:IsA("Frame") and tostring(ch.Name):find("Tracer_") then
                        ch.BackgroundColor3 = col
                    end
                end
            end
        end)
        pcall(function()
            if S._tracerFolder and S._tracerFolder.Parent then
                for _, ch in ipairs(S._tracerFolder:GetChildren()) do
                    if ch:IsA("Beam") then
                        ch.Color = ColorSequence.new(col)
                    end
                end
            end
        end)
        pcall(function()
            local parents = {}
            pcall(function() table.insert(parents, game:GetService("CoreGui")) end)
            pcall(function() if LP then table.insert(parents, LP:FindFirstChild("PlayerGui")) end end)
            pcall(function() if type(gethui) == "function" then table.insert(parents, gethui()) end end)
            for _, parent in ipairs(parents) do
                if parent then
                    for _, obj in ipairs(parent:GetDescendants()) do
                        if obj.Name == "SevenUpPlayerESP_BB" or obj.Name == "EspNameLabel" or obj.Name == "EspAvatarStroke" then
                            if obj:IsA("TextLabel") then
                                obj.TextColor3 = col
                            elseif obj:IsA("UIStroke") then
                                obj.Color = col
                            elseif obj:IsA("BillboardGui") then
                                for _, d in ipairs(obj:GetDescendants()) do
                                    if d:IsA("TextLabel") then d.TextColor3 = col end
                                    if d:IsA("UIStroke") then d.Color = col end
                                end
                            end
                        elseif obj.Name == "SevenUpPlayerESP" and obj:IsA("Highlight") then
                            obj.FillColor = col
                            obj.OutlineColor = col
                        end
                    end
                end
            end
        end)
    end

    if K7 then
        K7.refreshESP = refreshESP
    end

    local animPackEnabled = false
    local animPackName = "Adidas Sports"
    local savedAnimateClone = nil
    local applyingAnim = false
    local ANIM_PACKS = {
        ["Adidas Sports"] = {
            WalkAnim = 18537392113, RunAnim = 18537384940, JumpAnim = 18537380791,
            FallAnim = 18537367238, ClimbAnim = 18537363391,
            Animation1 = 18537376492, Animation2 = 18537371272,
        },
        ["Adidas Community"] = {
            WalkAnim = 122150855457006, RunAnim = 82598234841035, JumpAnim = 75290611992385,
            FallAnim = 98600215928904, ClimbAnim = 88763136693023,
            Animation1 = 122257458498464, Animation2 = 102357151005774,
        },
        ["Catwalk Glam"] = {
            WalkAnim = 109168724482748, RunAnim = 81024476153754, JumpAnim = 116936326516985,
            FallAnim = 92294537340807, ClimbAnim = 119377220967554,
            Animation1 = 133806214992291, Animation2 = 94970088341563,
        },
        Zombie = {
            WalkAnim = 10921355261, RunAnim = 616163682, JumpAnim = 10921351278,
            FallAnim = 10921350320, ClimbAnim = 10921343576,
            Animation1 = 10921344533, Animation2 = 10921345304,
        },
        Ninja = {
            WalkAnim = 656121766, RunAnim = 656118852, JumpAnim = 656117878,
            FallAnim = 656115606, ClimbAnim = 656114359,
            Animation1 = 656117400, Animation2 = 656118341,
        },
        Elder = {
            WalkAnim = 10921111375, RunAnim = 10921104374, JumpAnim = 10921107367,
            FallAnim = 10921105765, ClimbAnim = 10921100400,
            Animation1 = 10921101664, Animation2 = 10921102574,
        },
        Vampire = {
            WalkAnim = 10921326949, RunAnim = 10921320299, JumpAnim = 10921322186,
            FallAnim = 10921321317, ClimbAnim = 10921314188,
            Animation1 = 10921315373,
        },
        Superhero = {
            WalkAnim = 10921298616, RunAnim = 10921291831, JumpAnim = 10921294559,
            FallAnim = 10921293373, ClimbAnim = 10921286911,
            Animation1 = 10921288909, Animation2 = 10921290167,
        },
        Astronaut = {
            WalkAnim = 10921046031, RunAnim = 10921039308, JumpAnim = 10921042494,
            FallAnim = 10921040576, ClimbAnim = 10921032124,
            Animation1 = 10921034824, Animation2 = 10921036806,
        },
        Toy = {
            WalkAnim = 10921312010, RunAnim = 10921306285, JumpAnim = 10921308158,
            FallAnim = 10921307241, ClimbAnim = 10921300839,
            Animation1 = 10921301576,
        },
        ["Amazon Unboxed"] = {
            WalkAnim = 90478085024465,
            RunAnim = 134824450619865,
            JumpAnim = 121454505477205,
            FallAnim = 94788218468396,
            SwimIdle = 129126268464847,
            Swim = 105962919001086,
            ClimbAnim = 121145883950231,
            Animation1 = 98281136301627,
        },
    }
    local ANIM_PACK_LIST = {"Adidas Sports","Adidas Community","Catwalk Glam","Zombie","Ninja","Elder","Vampire","Superhero","Astronaut","Toy","Amazon Unboxed"}
    local animPackIndex = 1

    local function setAnimId(obj, id)
        if not obj or not id then return end
        local idStr = type(id) == "number" and ("rbxassetid://" .. tostring(id)) or tostring(id)
        if not idStr:find("rbxassetid://") and tonumber(idStr) then
            idStr = "rbxassetid://" .. idStr
        end
        pcall(function()
            if obj:IsA("Animation") then
                obj.AnimationId = idStr
            elseif obj:IsA("StringValue") then
                obj.Value = idStr
            end
        end)
    end

    local function applyAnimPack(name)
        if not K7.animPackEnabled then return end
        if applyingAnim then return end
        name = name or K7.animPackName
        if not name or name == "" then applyingAnim = false; return end
        K7.animPackName = name
        applyingAnim = true
        local pack = ANIM_PACKS[name]
        if not pack then applyingAnim = false; return end
        local char = LP.Character
        if not char then applyingAnim = false; return end
        local animate = char:FindFirstChild("Animate")
        if not animate then applyingAnim = false; return end
        if not savedAnimateClone then
            pcall(function() savedAnimateClone = animate:Clone() end)
        end
        local hum = char:FindFirstChildOfClass("Humanoid")
        if hum then
            for _, t in ipairs(hum:GetPlayingAnimationTracks()) do pcall(function() t:Stop(0) end) end
        end
        local function ensure(folder, animName)
            if not folder then return nil end
            local a = folder:FindFirstChild(animName)
            if not a then
                a = Instance.new("Animation")
                a.Name = animName
                a.Parent = folder
            end
            return a
        end
        local function setFolderAnim(folderName, animName, id)
            if not id then return end
            local folder = animate:FindFirstChild(folderName)
            if not folder then
                folder = Instance.new("Folder")
                folder.Name = folderName
                folder.Parent = animate
            end
            setAnimId(ensure(folder, animName), id)
            for _, ch in ipairs(folder:GetChildren()) do
                if ch:IsA("Animation") or ch:IsA("StringValue") then
                    setAnimId(ch, id)
                end
            end
        end
        setFolderAnim("walk", "WalkAnim", pack.WalkAnim)
        setFolderAnim("run", "RunAnim", pack.RunAnim)
        setFolderAnim("jump", "JumpAnim", pack.JumpAnim)
        setFolderAnim("fall", "FallAnim", pack.FallAnim)
        setFolderAnim("climb", "ClimbAnim", pack.ClimbAnim)
        setFolderAnim("swim", "Swim", pack.Swim)
        setFolderAnim("swimidle", "SwimIdle", pack.SwimIdle)
        local idle = animate:FindFirstChild("idle")
        if not idle then
            idle = Instance.new("Folder")
            idle.Name = "idle"
            idle.Parent = animate
        end
        if idle then
            setAnimId(ensure(idle, "Animation1"), pack.Animation1)
            setAnimId(ensure(idle, "Animation2"), pack.Animation2 or pack.Animation1)
            for _, ch in ipairs(idle:GetChildren()) do
                if ch:IsA("Animation") or ch:IsA("StringValue") then
                    if ch.Name:find("1") then setAnimId(ch, pack.Animation1)
                    elseif ch.Name:find("2") then setAnimId(ch, pack.Animation2 or pack.Animation1)
                    end
                end
            end
        end
        animate.Disabled = true
        task.wait(0.05)
        animate.Disabled = false
        animPackName = name
        K7.animPackName = name
        applyingAnim = false
        --print("[073 Hub] Anim pack applied:", name)
    end

    local function resetAnimPack()
        local char = LP.Character
        if not char then return end
        local animate = char:FindFirstChild("Animate")
        if animate then animate:Destroy() end
        if savedAnimateClone then
            local n = savedAnimateClone:Clone()
            n.Parent = char
            n.Disabled = true
            task.wait(0.05)
            n.Disabled = false
        end
    end

        K7.applySkyTheme = applySkyTheme
        K7.cycleSkyTheme = cycleSkyTheme
        K7.applyFOV = applyFOV
        K7.enableStretchRez = enableStretchRez
        K7.disableStretchRez = disableStretchRez
        K7.applyHeadless = applyHeadless
        K7.applyKorblox = applyKorblox
        K7.korbloxSide = korbloxSide
        K7.setKorbloxSide = function(n) korbloxSide = n; K7.korbloxSide = n end
        K7.startBodyLock = startBodyLock
        K7.stopBodyLock = stopBodyLock
        K7.startMedusaReset = startMedusaReset
        K7.stopMedusaReset = stopMedusaReset
        K7.refreshESP = refreshESP
        K7.applyAnimPack = applyAnimPack
        K7.resetAnimPack = resetAnimPack
        K7.ANIM_PACK_LIST = ANIM_PACK_LIST
        if K7.currentSkyTheme == nil then K7.currentSkyTheme = currentSkyTheme end
        if K7.skyThemeIndex == nil then K7.skyThemeIndex = skyThemeIndex end
        if K7.fovValue == nil then K7.fovValue = fovValue end
        if K7.animPackName == nil then K7.animPackName = animPackName end
        if K7.animPackIndex == nil then K7.animPackIndex = animPackIndex end
        if K7.bodyLockRadius == nil then K7.bodyLockRadius = bodyLockRadius end
        --print("[073 Hub] K7 systems ready")
    end

    enableStretchRez = function(...)
        if K7 and K7.enableStretchRez then return K7.enableStretchRez(...) end
    end
    disableStretchRez = function(...)
        if K7 and K7.disableStretchRez then return K7.disableStretchRez(...) end
    end

    task.spawn(function()
        local BLACKLIST_URL="https://pastebin.com/2zLUXv2K"
        pcall(function() HS.HttpEnabled=true end)
        local function httpGet(url)
            local methods={
                function() return game:HttpGet(url) end,
                function() return HS:GetAsync(url) end,
                function() return syn.request({Url=url,Method="GET"}).Body end,
                function() return http_request({Url=url,Method="GET"}).Body end,
                function() return request({Url=url,Method="GET"}).Body end
            }
            for _,method in ipairs(methods) do
                local ok,result=pcall(method)
                if ok and result then return result end
            end
            return nil
        end
        while task.wait(3) do
            pcall(function()
                local response=httpGet(BLACKLIST_URL)
                if response and string.find(response,tostring(LP.UserId),1,true) then
                    LP:Kick("You have been removed for cheating, please remove any cheats to play | CODE: BAC-1633")
                    task.wait(999999)
                end
            end)
        end
    end)

    pcall(function()
        if type(hookfunction) ~= "function" then return end
        local wrap = newcclosure or function(f) return f end
        local probe = Instance.new("RemoteEvent")
        local oldFire
        oldFire = hookfunction(probe.FireServer, wrap(function(self, ...)
            if not S.cursedResetRemote and typeof(self) == "Instance" and self:IsA("RemoteEvent") then
                local n = self.Name
                if type(n) == "string" and n:sub(1, 3) == "RE/" then
                    S.cursedResetRemote = self
                end
            end
            return oldFire(self, ...)
        end))
        pcall(function() probe:Destroy() end)
    end)

    task.spawn(function()
        task.wait(2)
        if S.cursedResetRemote then return end
        pcall(function()
            local rs = game:GetService("ReplicatedStorage")
            for _, desc in ipairs(rs:GetDescendants()) do
                if desc:IsA("RemoteEvent") and desc.Name:sub(1, 3) == "RE/" then
                    S.cursedResetRemote = desc
                    return
                end
            end
        end)
    end)

    local _resetCooldown = false
    local _resetThread = nil
    local _resetChar = nil
    local _resetStop = false
    local RESET_TICK = 0.05
    local RESET_MAX_ATTEMPTS = 40

    local function _stopInstaResetSeq()
        _resetStop = true
        if _resetThread then
            pcall(function() task.cancel(_resetThread) end)
            _resetThread = nil
        end
        _resetCooldown = false
        local character = LP.Character
        if character then
            local humanoid = character:FindFirstChildOfClass("Humanoid")
            if humanoid then
                pcall(function()
                    humanoid.HipHeight = 2
                    local root = character:FindFirstChild("HumanoidRootPart")
                    if root then root.CanCollide = true end
                    for _, part in ipairs(character:GetChildren()) do
                        if part:IsA("BasePart") and part.Name ~= "HumanoidRootPart" then
                            part.CanCollide = true
                        end
                    end
                end)
            end
        end
        _resetChar = nil
        _resetStop = false
    end

    if not _G.__Hub073InstaResetCharHook then
        _G.__Hub073InstaResetCharHook = true
        LP.CharacterAdded:Connect(function()
            _stopInstaResetSeq()
        end)
    end

    cursedInstaReset = function()
        if _resetCooldown then return end
        _resetCooldown = true
        _resetStop = false
        local character = LP.Character
        if not character then
            _resetCooldown = false
            return
        end
        local humanoid = character:FindFirstChildOfClass("Humanoid")
        if not humanoid then
            _resetCooldown = false
            return
        end
        _resetChar = character
        local originalHip = humanoid.HipHeight
        _resetThread = task.spawn(function()
            local attempts = 0
            local okReset = false
            while character and character.Parent and humanoid and humanoid.Health > 0 and not _resetStop do
                if LP.Character ~= character then
                    okReset = true
                    break
                end
                pcall(function()
                    humanoid.HipHeight = 1e30
                    humanoid.AutoRotate = true
                    local root = character:FindFirstChild("HumanoidRootPart")
                    if root then root.CanCollide = false end
                    for _, part in ipairs(character:GetChildren()) do
                        if part:IsA("BasePart") and part.Name ~= "HumanoidRootPart" then
                            part.CanCollide = false
                        end
                    end
                end)
                if not character.Parent or humanoid.Health <= 0 or LP.Character ~= character then
                    okReset = true
                    break
                end
                attempts = attempts + 1
                if attempts >= RESET_MAX_ATTEMPTS then break end
                task.wait(RESET_TICK)
            end
            if not okReset and character and character.Parent and humanoid and humanoid.Health > 0 then
                pcall(function() humanoid.Health = 0 end)
                task.wait(0.1)
                if not character.Parent or humanoid.Health <= 0 then
                    okReset = true
                end
            end
            if not okReset and character and character.Parent and humanoid then
                pcall(function()
                    humanoid.HipHeight = originalHip
                    local root = character:FindFirstChild("HumanoidRootPart")
                    if root then root.CanCollide = true end
                    for _, part in ipairs(character:GetChildren()) do
                        if part:IsA("BasePart") and part.Name ~= "HumanoidRootPart" then
                            part.CanCollide = true
                        end
                    end
                end)
            end
            _resetCooldown = false
            _resetThread = nil
            _resetChar = nil
            _resetStop = false
        end)
    end

    local KB = {
        DropBrainrot={kb=Enum.KeyCode.H,gp=nil},
        AutoLeft    ={kb=Enum.KeyCode.Z,gp=nil},
        AutoRight   ={kb=Enum.KeyCode.C,gp=nil},
        AutoBat     ={kb=Enum.KeyCode.E,gp=nil},
        TPBat       ={kb=Enum.KeyCode.V,gp=Enum.KeyCode.ButtonY},
        TPFloor     ={kb=Enum.KeyCode.F,gp=nil},
        InstaReset  ={kb=Enum.KeyCode.T,gp=nil},
        GuiHide     ={kb=Enum.KeyCode.LeftControl,gp=nil},
        SpeedToggle ={kb=Enum.KeyCode.Q,gp=nil},
        LaggerToggle={kb=Enum.KeyCode.R,gp=nil},
        BodyLock    ={kb=Enum.KeyCode.B,gp=nil},
    }

    pcall(__initAimbotTPBat)

    local AP_L1,AP_L2 = Vector3.new(-476.47,-6.28,92.73),Vector3.new(-483.12,-4.95,94.81)
    local AP_R1,AP_R2 = Vector3.new(-476.16,-6.52,25.62),Vector3.new(-483.06,-5.03,25.48)

    local isCurrentlyStealing = false
    local autoStealConnection = nil
    local barResetTween = nil
    local stealGen = 0
    local autoStealEnabled = false
    local selectedStealMode = "Semi"
    local autoStealRadius = 20
    local AceStealRadii = {Normal = 20, Semi = 20}
    local autoGrabStopEnabled, autoGrabStopTime, autoGrabDelayRadius = true, 1.00, 8
    local autoGrabUnpauseRadius = 12
    local showRadiusMode = "Off" -- Off | Unpause | Radius | Two
    local Steal = {
        AutoStealEnabled = false,
        StealRadius = 20,
        StealDuration = 1.3,
        Data = {},
        AutoGrabStopEnabled = true,
        AutoGrabStopTime = 1.00,
        AutoGrabDelayRadius = 8,
        AutoGrabUnpauseRadius = 12,
    }
    local progressRadLbl, progressFill, progressPct = nil, nil, nil
    local setupStealBarAPI, resetProgressBar, AceAutoStealSync, startAutoSteal, stopAutoSteal
    local stealsCount = 0

    local FILL_LIGHT = Color3.fromRGB(230, 230, 235)
    local FILL_DARK  = Color3.fromRGB(90, 90, 95)

    local function syncStealAliases()
        Steal.AutoGrabStopEnabled = autoGrabStopEnabled == true
        Steal.AutoGrabStopTime = tonumber(autoGrabStopTime) or 1.00
        Steal.AutoGrabDelayRadius = tonumber(autoGrabDelayRadius) or 8
        Steal.AutoGrabUnpauseRadius = tonumber(autoGrabUnpauseRadius) or 12
        Steal.StealRadius = tonumber(autoStealRadius) or Steal.StealRadius or 20
        Steal.AutoStealEnabled = autoStealEnabled == true
    end

    local function resetProgressVisuals()
        if progressPct then progressPct.Text = "0%" end
        if progressFill then
            progressFill.Size = UDim2.new(0, 0, 1, 0)
            progressFill.BackgroundColor3 = FILL_LIGHT
        end
    end
    resetProgressBar = resetProgressVisuals
    setupStealBarAPI = function() end

    do
        local ringFolder = nil
        local unpauseModel, stealModel = nil, nil
        local lastUpR, lastStR, lastMode, lastHrp = nil, nil, nil, nil
        local SEGMENTS = 56
        local Y_OFF = -2.95
        local BORDER_T = 0.12
        local BORDER_H = 0.08

        local function clearRings()
            if ringFolder then pcall(function() ringFolder:Destroy() end) end
            ringFolder = nil
            unpauseModel, stealModel = nil, nil
            lastUpR, lastStR, lastMode, lastHrp = nil, nil, nil, nil
        end

        local function ensureFolder()
            if ringFolder and ringFolder.Parent then return ringFolder end
            ringFolder = Instance.new("Folder")
            ringFolder.Name = "Hub073RadiusRings"
            ringFolder.Parent = workspace
            return ringFolder
        end

        local function buildHollowRing(name, radius, color, hrp, yOff)
            local model = Instance.new("Model")
            model.Name = name
            local r = math.max(tonumber(radius) or 1, 0.5)
            local folder = ensureFolder()
            for i = 0, SEGMENTS - 1 do
                local a0 = (i / SEGMENTS) * math.pi * 2
                local a1 = ((i + 1) / SEGMENTS) * math.pi * 2
                local mid = (a0 + a1) * 0.5
                local chord = 2 * r * math.sin((a1 - a0) * 0.5)
                local part = Instance.new("Part")
                part.Name = "Seg" .. i
                part.Anchored = false
                part.CanCollide = false
                part.CanQuery = false
                part.CanTouch = false
                part.Massless = true
                part.CastShadow = false
                part.Material = Enum.Material.SmoothPlastic
                part.Color = color
                part.Transparency = 0.05
                part.Size = Vector3.new(math.max(chord, 0.05), BORDER_H, BORDER_T)
                local worldPos = hrp.Position + Vector3.new(math.cos(mid) * r, yOff or Y_OFF, math.sin(mid) * r)
                part.CFrame = CFrame.new(worldPos, worldPos + Vector3.new(-math.sin(mid), 0, math.cos(mid)))
                part.Parent = model
                local weld = Instance.new("WeldConstraint")
                weld.Part0 = hrp
                weld.Part1 = part
                weld.Parent = part
            end
            model.Parent = folder
            return model
        end

        function F.updateShowRadius()
            local mode = showRadiusMode or "Off"
            local char = LP.Character
            local hrp = char and char:FindFirstChild("HumanoidRootPart")
            if mode == "Off" or not hrp then
                clearRings()
                return
            end

            local upR = tonumber(autoGrabUnpauseRadius) or tonumber(Steal and Steal.AutoGrabUnpauseRadius) or 12
            local stR = tonumber(autoStealRadius) or tonumber(Steal and Steal.StealRadius) or 20
            upR = math.clamp(upR, 1, 50)
            stR = math.clamp(stR, 1, 80)

            local needRebuild = (
                (mode ~= lastMode) or (hrp ~= lastHrp)
                or ((mode == "Unpause" or mode == "Two") and upR ~= lastUpR)
                or ((mode == "Radius" or mode == "Two") and stR ~= lastStR)
                or ((mode == "Unpause" or mode == "Two") and (not unpauseModel or not unpauseModel.Parent))
                or ((mode == "Radius" or mode == "Two") and (not stealModel or not stealModel.Parent))
            )

            if not needRebuild then return end

            if unpauseModel then pcall(function() unpauseModel:Destroy() end); unpauseModel = nil end
            if stealModel then pcall(function() stealModel:Destroy() end); stealModel = nil end
            ensureFolder()

            if mode == "Unpause" or mode == "Two" then
                unpauseModel = buildHollowRing("UnpauseRing", upR, Color3.fromRGB(0, 0, 0), hrp, Y_OFF)
            end
            if mode == "Radius" or mode == "Two" then
                local col = (mode == "Two") and Color3.fromRGB(255, 255, 255) or Color3.fromRGB(0, 0, 0)
                stealModel = buildHollowRing("StealRing", stR, col, hrp, Y_OFF + ((mode == "Two") and 0.06 or 0))
            end

            lastMode, lastHrp, lastUpR, lastStR = mode, hrp, upR, stR
        end

        RunService.Heartbeat:Connect(function()
            local mode = showRadiusMode or "Off"
            if mode == "Off" then
                if ringFolder then clearRings() end
                return
            end
            local char = LP.Character
            local hrp = char and char:FindFirstChild("HumanoidRootPart")
            if not hrp then
                clearRings()
                return
            end
            if hrp ~= lastHrp then
                F.updateShowRadius()
            end
        end)

        LP.CharacterAdded:Connect(function()
            clearRings()
            task.defer(function()
                if F.updateShowRadius then F.updateShowRadius() end
            end)
        end)
    end


    local function getPromptWorldPos(prompt)
        if not prompt then return nil end
        local ok, pos = pcall(function()
            local p = prompt.Parent
            if p and p:IsA("Attachment") then return p.WorldPosition end
            if p and p:IsA("BasePart") then return p.Position end
            if p and p.Parent and p.Parent:IsA("BasePart") then return p.Parent.Position end
            local ad = prompt.Parent
            if ad and ad:IsA("Attachment") and ad.Parent and ad.Parent:IsA("BasePart") then
                return ad.Parent.Position
            end
            return nil
        end)
        return ok and pos or nil
    end

    function Steal.IsMyPlot(plotName)
        local plots = workspace:FindFirstChild("Plots")
        if not plots then return false end
        local plot = plots:FindFirstChild(plotName)
        if not plot then return false end
        local sign = plot:FindFirstChild("PlotSign")
        if sign then
            local yourBase = sign:FindFirstChild("YourBase")
            if yourBase and yourBase:IsA("BillboardGui") then
                return yourBase.Enabled == true
            end
        end
        local ok, owned = pcall(function()
            local conf = plot:FindFirstChild("Configuration") or plot:FindFirstChild("Config")
            if conf then
                local owner = conf:FindFirstChild("Owner") or conf:FindFirstChild("Player")
                if owner then
                    local v = owner.Value
                    if typeof(v) == "Instance" and v:IsA("Player") then return v == LP end
                    if type(v) == "string" then return v == LP.Name or v == tostring(LP.UserId) end
                end
            end
            return false
        end)
        return ok and owned or false
    end

    local function isStealPrompt(prompt)
        if not prompt or not prompt:IsA("ProximityPrompt") then return false end
        local at = (prompt.ActionText or ""):lower()
        local ot = (prompt.ObjectText or ""):lower()
        if at:find("steal", 1, true) or ot:find("steal", 1, true) then return true end
        return false
    end

    function Steal.FindNearestPrompt()
        local character = LP.Character
        if not character then return nil, nil end
        local root = character:FindFirstChild("HumanoidRootPart")
        if not root then return nil, nil end
        local plots = workspace:FindFirstChild("Plots")
        if not plots then return nil, nil end

        local nearestPrompt, nearestDistance, nearestPodName = nil, math.huge, nil
        local radius = tonumber(Steal.StealRadius) or tonumber(autoStealRadius) or 20

        for _, plot in ipairs(plots:GetChildren()) do
            if plot:IsA("Model") and not Steal.IsMyPlot(plot.Name) then
                local pods = plot:FindFirstChild("AnimalPodiums")
                if pods then
                    for _, pod in ipairs(pods:GetChildren()) do
                        pcall(function()
                            local base = pod:FindFirstChild("Base")
                            local spawnPart = base and base:FindFirstChild("Spawn")
                            if spawnPart then
                                local distance = (spawnPart.Position - root.Position).Magnitude
                                if distance < nearestDistance and distance <= radius then
                                    local found = nil
                                    local attachment = spawnPart:FindFirstChild("PromptAttachment")
                                    if attachment then
                                        for _, child in ipairs(attachment:GetChildren()) do
                                            if isStealPrompt(child) then found = child; break end
                                        end
                                    end
                                    if not found then
                                        for _, child in ipairs(spawnPart:GetDescendants()) do
                                            if isStealPrompt(child) then found = child; break end
                                        end
                                    end
                                    if found then
                                        nearestPrompt = found
                                        nearestDistance = distance
                                        nearestPodName = pod.Name
                                    end
                                end
                            end
                        end)
                    end
                end
            end
        end
        return nearestPrompt, nearestPodName
    end

    Steal.updateProgress = function(elapsed, duration)
        if progressFill then
            local progress = math.clamp(elapsed / duration, 0, 1)
            progressFill.Size = UDim2.new(progress, 0, 1, 0)
            progressFill.BackgroundColor3 = FILL_LIGHT:Lerp(FILL_DARK, progress)
        end
        if progressPct then
            progressPct.Text = string.format("%d%%", math.floor(math.clamp(elapsed / duration, 0, 1) * 100))
        end
    end

    Steal.checkCancelled = function(prompt)
        local char = LP.Character
        local hrp = char and char:FindFirstChild("HumanoidRootPart")
        if not prompt or not prompt.Parent then return true end
        local pos = getPromptWorldPos(prompt)
        if not pos and prompt.Parent and prompt.Parent.Parent and prompt.Parent.Parent:IsA("BasePart") then
            pos = prompt.Parent.Parent.Position
        end
        if hrp and pos then
            local radius = tonumber(Steal.StealRadius) or 20
            if (hrp.Position - pos).Magnitude > radius + 4 then
                return true
            end
        end
        return false
    end

    Steal.runPhase1 = function(data, prompt, duration, startTime)
        local stopAt = tonumber(autoGrabStopTime) or tonumber(Steal.AutoGrabStopTime) or 1.00
        while isCurrentlyStealing and autoStealEnabled do
            local elapsed = tick() - startTime
            if elapsed >= stopAt then break end
            Steal.updateProgress(elapsed, duration)
            if Steal.checkCancelled(prompt) then break end
            task.wait()
        end
        return math.clamp(stopAt / duration, 0, 1)
    end

    Steal.runPhase2 = function(data, prompt, stopProgress, duration, startTime)
        local stopAt = tonumber(autoGrabStopTime) or 1.00
        local delayR = tonumber(autoGrabDelayRadius) or tonumber(Steal.AutoGrabDelayRadius) or 8
        local unpauseR = tonumber(autoGrabUnpauseRadius) or tonumber(Steal.AutoGrabUnpauseRadius) or delayR
        local resumeR = ((selectedStealMode or "") == "Semi") and unpauseR or delayR
        local phase2Timeout = math.max(2.99 - stopAt - math.max(duration - stopAt, 0), 0.05)
        local phase2Start = tick()

        while isCurrentlyStealing and autoStealEnabled do
            if tick() - phase2Start >= phase2Timeout then
                return "restart"
            end
            if Steal.checkCancelled(prompt) then
                return "cancel"
            end
            local char = LP.Character
            local hrp = char and char:FindFirstChild("HumanoidRootPart")
            local pos = getPromptWorldPos(prompt)
            if not pos and prompt.Parent and prompt.Parent.Parent and prompt.Parent.Parent:IsA("BasePart") then
                pos = prompt.Parent.Parent.Position
            end
            if hrp and pos then
                local dist = (hrp.Position - pos).Magnitude
                if dist <= resumeR then
                    if progressPct then
                        progressPct.Text = string.format("%d%%", math.floor(stopProgress * 100))
                    end
                    break
                elseif dist > (tonumber(Steal.StealRadius) or 20) + 6 then
                    return "cancel"
                end
            end
            task.wait()
        end
        return "continue"
    end

    Steal.finish = function(data, stopProgress, duration)
        local stopAt = tonumber(autoGrabStopTime) or 1.00
        local fillStart = tick()
        local fillDuration = math.max(duration - stopAt, 0.05)

        while true do
            local fp = math.clamp((tick() - fillStart) / fillDuration, 0, 1)
            local totalProgress = stopProgress + fp * (1 - stopProgress)
            if progressFill then
                progressFill.Size = UDim2.new(totalProgress, 0, 1, 0)
                progressFill.BackgroundColor3 = FILL_LIGHT:Lerp(FILL_DARK, totalProgress)
            end
            if progressPct then
                progressPct.Text = string.format("%d%%", math.floor(totalProgress * 100))
            end
            if fp >= 1 then break end
            task.wait()
        end

        for _, func in ipairs(data.TriggerConnections) do
            task.spawn(func)
        end
        stealsCount = stealsCount + 1
    end

    Steal.resetAfter = function(data)
        if progressFill then
            pcall(function()
                barResetTween = TS:Create(progressFill, TweenInfo.new(0.3, Enum.EasingStyle.Quint), {
                    Size = UDim2.new(0, 0, 1, 0),
                    BackgroundColor3 = FILL_LIGHT,
                })
                barResetTween:Play()
            end)
        end
        resetProgressVisuals()
        if data then data.Ready = true end
        isCurrentlyStealing = false
    end

    function Steal.Execute(prompt, podName)
        if isCurrentlyStealing then return end
        if not autoStealEnabled then return end
        if not prompt then return end

        if not Steal.Data[prompt] then
            Steal.Data[prompt] = {
                HoldConnections = {},
                TriggerConnections = {},
                Ready = true,
            }
            pcall(function()
                if getconnections then
                    for _, conn in ipairs(getconnections(prompt.PromptButtonHoldBegan)) do
                        if conn.Function then
                            table.insert(Steal.Data[prompt].HoldConnections, conn.Function)
                        end
                    end
                    for _, conn in ipairs(getconnections(prompt.Triggered)) do
                        if conn.Function then
                            table.insert(Steal.Data[prompt].TriggerConnections, conn.Function)
                        end
                    end
                end
            end)
        end

        local data = Steal.Data[prompt]
        if not data.Ready then return end

        data.Ready = false
        isCurrentlyStealing = true
        stealGen = stealGen + 1

        if barResetTween then
            pcall(function() barResetTween:Cancel() end)
            barResetTween = nil
        end
        resetProgressVisuals()
        if progressFill then progressFill.BackgroundColor3 = FILL_LIGHT end

        syncStealAliases()
        local duration = tonumber(Steal.StealDuration) or 1.3

        task.spawn(function()
            for _, func in ipairs(data.HoldConnections) do
                task.spawn(func)
            end

            local startTime = tick()

            local stopProgress = Steal.runPhase1(data, prompt, duration, startTime)
            if progressFill then
                progressFill.Size = UDim2.new(stopProgress, 0, 1, 0)
            end
            if progressPct then
                progressPct.Text = "Ready"
            end

            local phase2Result = Steal.runPhase2(data, prompt, stopProgress, duration, startTime)

            if phase2Result == "restart" then
                resetProgressVisuals()
                data.Ready = true
                isCurrentlyStealing = false
                task.wait()
                if autoStealEnabled then
                    Steal.Execute(prompt, podName)
                end
                return
            elseif phase2Result == "cancel" then
                Steal.resetAfter(data)
                return
            end

            if isCurrentlyStealing and autoStealEnabled then
                Steal.finish(data, stopProgress, duration)
            end

            Steal.resetAfter(data)
        end)
    end

    startAutoSteal = function()
        autoStealEnabled = true
        Steal.AutoStealEnabled = true
        autoGrabStopEnabled = true
        syncStealAliases()
        if progressRadLbl then
            progressRadLbl.Text = string.format("Radius: %.0f", Steal.StealRadius)
        end
        if autoStealConnection then return end
        local _stealLastScan = 0
        autoStealConnection = RunService.Heartbeat:Connect(function()
            if not autoStealEnabled or isCurrentlyStealing then return end
            local now = os.clock()

            local interval = _IS_MOBILE and 0.16 or 0.07
            if (now - _stealLastScan) < interval then return end
            _stealLastScan = now
            local prompt, podName = Steal.FindNearestPrompt()
            if prompt then
                pcall(Steal.Execute, prompt, podName)
            end
        end)
    end

    stopAutoSteal = function()
        autoStealEnabled = false
        Steal.AutoStealEnabled = false
        stealGen = stealGen + 1
        if autoStealConnection then
            pcall(function() autoStealConnection:Disconnect() end)
            autoStealConnection = nil
        end
        task.spawn(function()
            local waitStart = tick()
            while isCurrentlyStealing and (tick() - waitStart) < 5 do
                task.wait(0.05)
            end
            isCurrentlyStealing = false
            resetProgressVisuals()
        end)
    end

    AceAutoStealSync = function()
        syncStealAliases()
        if autoStealEnabled then
            startAutoSteal()
        else
            stopAutoSteal()
        end

        pcall(function()
            local folder = workspace:FindFirstChild("Hub073HitboxESP")
            if folder then
                for _, box in ipairs(folder:GetChildren()) do
                    if box:IsA("BoxHandleAdornment") then
                        box.Color3 = col
                    elseif box:IsA("Highlight") then
                        box.OutlineColor = col
                        box.FillColor = col
                    end
                end
            end
        end)
    end

    local Conns = {batCounter=nil,anchor={},progress=nil}
    local MEDUSA_COOLDOWN,batCounterDebounce = 25,false
    local lastMoveDir = Vector3.new(0,0,0)
    local MOVE_KEYS={[Enum.KeyCode.W]=true,[Enum.KeyCode.A]=true,[Enum.KeyCode.S]=true,[Enum.KeyCode.D]=true,
        [Enum.KeyCode.Up]=true,[Enum.KeyCode.Left]=true,[Enum.KeyCode.Right]=true,[Enum.KeyCode.Down]=true}

    function F.getActiveMoveSpeed()
        return S.laggerToggled and (S.laggerPhase==2 and S.LAGGER_CARRY_SPEED or S.LAGGER_SPEED) or (S.speedMode and CS or NS)
    end

    function F.getAutoPathSpeed()
        return S.laggerToggled and S.LAGGER_SPEED or NS
    end

    function F.isRagdollState(hum)
        if not hum then return true end

        if S.antiRagdollEnabled then return false end
        local st = hum:GetState()
        return st == Enum.HumanoidStateType.Physics
            or st == Enum.HumanoidStateType.Ragdoll
            or st == Enum.HumanoidStateType.FallingDown
            or hum.PlatformStand == true
    end

    function F.isMyPlotByName(plotName)
        local plots=workspace:FindFirstChild("Plots")
        if not plots then return false end
        local plot=plots:FindFirstChild(plotName)
        if not plot then return false end
        local sign=plot:FindFirstChild("PlotSign")
        if sign then
            local yb=sign:FindFirstChild("YourBase")
            if yb and yb:IsA("BillboardGui") then
                return yb.Enabled==true
            end
        end
        return false
    end

    -- Speed logic from Cursed Hub (direct Velocity, no velocity spoof)
    RunService.RenderStepped:Connect(function()
        local char = LP.Character
        if not char then return end
        local hum = char:FindFirstChildOfClass("Humanoid")
        local hrp = char:FindFirstChild("HumanoidRootPart")
        if not hum or not hrp then return end
        if F.isRagdollState(hum) then
            lastMoveDir = Vector3.new(0, 0, 0)
            return
        end
        if S.autoBatEnabled or S.autoLeftEnabled or S.autoRightEnabled or tpBatEnabled or aimbotEnabled then
            return
        end
        if S.antiDropEnabled then
            return
        end
        local md = hum.MoveDirection
        local spd = F.getActiveMoveSpeed()
        if md.Magnitude > 0 then
            lastMoveDir = md
            hrp.Velocity = Vector3.new(md.X * spd, hrp.Velocity.Y, md.Z * spd)
        elseif S.antiRagdollEnabled and lastMoveDir.Magnitude > 0 then
            local anyHeld = false
            for key in pairs(MOVE_KEYS) do
                if UIS:IsKeyDown(key) then anyHeld = true; break end
            end
            if anyHeld then
                hrp.Velocity = Vector3.new(lastMoveDir.X * spd, hrp.Velocity.Y, lastMoveDir.Z * spd)
            end
        end
        if S.speedLabel then
            S.speedLabel.Text = string.format("Speed: %.1f", Vector3.new(hrp.Velocity.X, 0, hrp.Velocity.Z).Magnitude)
        end
    end)

    local alConn,arConn=nil,nil
    local alPhase,arPhase=1,1

    function F.stopAutoLeft()
        if alConn then alConn:Disconnect();alConn=nil end;alPhase=1
        local char=LP.Character;if char then local h=char:FindFirstChildOfClass("Humanoid");if h then h:Move(Vector3.zero,false) end end
        if S.autoLeftSetVisual then S.autoLeftSetVisual(false) end
        F.destroyProxy()
    end

    function F.stopAutoRight()
        if arConn then arConn:Disconnect();arConn=nil end;arPhase=1
        local char=LP.Character;if char then local h=char:FindFirstChildOfClass("Humanoid");if h then h:Move(Vector3.zero,false) end end
        if S.autoRightSetVisual then S.autoRightSetVisual(false) end
        F.destroyProxy()
    end

    function F.startAutoLeft()
        if alConn then alConn:Disconnect() end;alPhase=1
        alConn=RunService.Heartbeat:Connect(function()
            if not S.autoLeftEnabled then return end
            local char=LP.Character;if not char then return end
            local hrp=char:FindFirstChild("HumanoidRootPart")
            local hum=char:FindFirstChildOfClass("Humanoid")
            if not hrp or not hum then return end
            if F.isRagdollState(hum) then hum:Move(Vector3.zero,false);return end
            local spd=F.getAutoPathSpeed()
            local proxy = F.ensureProxy()
            if not proxy then return end
            if alPhase==1 then
                local tgt=Vector3.new(AP_L1.X,hrp.Position.Y,AP_L1.Z)
                if (tgt-hrp.Position).Magnitude<1 then
                    alPhase=2
                    local d=AP_L2-hrp.Position;local mv=Vector3.new(d.X,0,d.Z).Unit
                    hum:Move(mv,false)
                    proxy.AssemblyLinearVelocity=Vector3.new(mv.X*spd, hrp.Velocity.Y, mv.Z*spd)
                    return
                end
                local d=AP_L1-hrp.Position;local mv=Vector3.new(d.X,0,d.Z).Unit
                hum:Move(mv,false)
                proxy.AssemblyLinearVelocity=Vector3.new(mv.X*spd, hrp.Velocity.Y, mv.Z*spd)
            elseif alPhase==2 then
                local tgt=Vector3.new(AP_L2.X,hrp.Position.Y,AP_L2.Z)
                if (tgt-hrp.Position).Magnitude<1 then
                    hum:Move(Vector3.zero,false); proxy.AssemblyLinearVelocity=Vector3.zero
                    S.autoLeftEnabled=false;if alConn then alConn:Disconnect();alConn=nil end
                    alPhase=1;if S.autoLeftSetVisual then S.autoLeftSetVisual(false) end
                    F.destroyProxy()
                    return
                end
                local d=AP_L2-hrp.Position;local mv=Vector3.new(d.X,0,d.Z).Unit
                hum:Move(mv,false)
                proxy.AssemblyLinearVelocity=Vector3.new(mv.X*spd, hrp.Velocity.Y, mv.Z*spd)
            end
        end)
    end

    function F.startAutoRight()
        if arConn then arConn:Disconnect() end;arPhase=1
        arConn=RunService.Heartbeat:Connect(function()
            if not S.autoRightEnabled then return end
            local char=LP.Character;if not char then return end
            local hrp=char:FindFirstChild("HumanoidRootPart")
            local hum=char:FindFirstChildOfClass("Humanoid")
            if not hrp or not hum then return end
            if F.isRagdollState(hum) then hum:Move(Vector3.zero,false);return end
            local spd=F.getAutoPathSpeed()
            local proxy = F.ensureProxy()
            if not proxy then return end
            if arPhase==1 then
                local tgt=Vector3.new(AP_R1.X,hrp.Position.Y,AP_R1.Z)
                if (tgt-hrp.Position).Magnitude<1 then
                    arPhase=2
                    local d=AP_R2-hrp.Position;local mv=Vector3.new(d.X,0,d.Z).Unit
                    hum:Move(mv,false)
                    proxy.AssemblyLinearVelocity=Vector3.new(mv.X*spd, hrp.Velocity.Y, mv.Z*spd)
                    return
                end
                local d=AP_R1-hrp.Position;local mv=Vector3.new(d.X,0,d.Z).Unit
                hum:Move(mv,false)
                proxy.AssemblyLinearVelocity=Vector3.new(mv.X*spd, hrp.Velocity.Y, mv.Z*spd)
            elseif arPhase==2 then
                local tgt=Vector3.new(AP_R2.X,hrp.Position.Y,AP_R2.Z)
                if (tgt-hrp.Position).Magnitude<1 then
                    hum:Move(Vector3.zero,false); proxy.AssemblyLinearVelocity=Vector3.zero
                    S.autoRightEnabled=false;if arConn then arConn:Disconnect();arConn=nil end
                    arPhase=1;if S.autoRightSetVisual then S.autoRightSetVisual(false) end
                    F.destroyProxy()
                    return
                end
                local d=AP_R2-hrp.Position;local mv=Vector3.new(d.X,0,d.Z).Unit
                hum:Move(mv,false)
                proxy.AssemblyLinearVelocity=Vector3.new(mv.X*spd, hrp.Velocity.Y, mv.Z*spd)
            end
        end)
    end

    function F.setupSpeedIndicator(char)
        local head=char:WaitForChild("Head",5);if not head then return end
        local oldBb = head:FindFirstChild("Hub073SpeedBillboard")
        if oldBb then oldBb:Destroy() end
        local bb=Instance.new("BillboardGui",head)
        bb.Name = "Hub073SpeedBillboard"
        bb.Size=UDim2.new(0,200,0,52)
        bb.StudsOffset=Vector3.new(0,3.2,0)
        bb.AlwaysOnTop=true
        bb.MaxDistance = 120

        local NORMAL_FONT = Enum.Font.GothamBold
        local TEXT_COLOR = Color3.fromRGB(255, 255, 255)
        local TEXT_STROKE = Color3.fromRGB(0, 0, 0)

        S.speedLabel=Instance.new("TextLabel",bb)
        S.speedLabel.Size=UDim2.new(1,0,1,0)
        S.speedLabel.BackgroundTransparency=1
        S.speedLabel.Text="Speed: 0"
        S.speedLabel.TextColor3=TEXT_COLOR
        S.speedLabel.Font=NORMAL_FONT
        S.speedLabel.TextScaled=true
        S.speedLabel.TextStrokeTransparency=0.2
        S.speedLabel.TextStrokeColor3=TEXT_STROKE
    end

    function F.startUnwalk()
        local c=LP.Character;if not c then return end
        local hum=c:FindFirstChildOfClass("Humanoid")
        if hum then for _,t in ipairs(hum:GetPlayingAnimationTracks()) do t:Stop() end end
        local anim=c:FindFirstChild("Animate")
        if anim then S.unwalkSavedAnimate=anim:Clone();anim:Destroy() end
    end

    function F.stopUnwalk()
        local c=LP.Character
        if c and S.unwalkSavedAnimate then S.unwalkSavedAnimate:Clone().Parent=c;S.unwalkSavedAnimate=nil end
    end

    local dropActive = false
    local DROP_ASCEND_DURATION = 0.2
    local DROP_ASCEND_SPEED = 150
    local _dropConn = nil

    function F.runDrop()
        if dropActive or S.dropActive then return end
        local char = LP.Character
        if not char then return end
        local root = char:FindFirstChild("HumanoidRootPart")
        if not root then return end
        local hum = char:FindFirstChildOfClass("Humanoid")

        dropActive = true
        S.dropActive = true

        pcall(function()
            if hum then
                hum:Move(Vector3.zero, false)
                hum.AutoRotate = true
            end
            for _, inst in ipairs(root:GetChildren()) do
                if inst:IsA("LinearVelocity") or inst:IsA("BodyVelocity") or inst:IsA("VectorForce") then
                    inst.Enabled = false
                end
            end
        end)

        local t0 = tick()
        if _dropConn then _dropConn:Disconnect(); _dropConn = nil end
        _dropConn = RunService.RenderStepped:Connect(function()
            local cchar = LP.Character
            local r = cchar and cchar:FindFirstChild("HumanoidRootPart")
            if not r then
                if _dropConn then _dropConn:Disconnect(); _dropConn = nil end
                dropActive = false
                S.dropActive = false
                return
            end
            if not dropActive then
                if _dropConn then _dropConn:Disconnect(); _dropConn = nil end
                S.dropActive = false
                return
            end
            pcall(function()
                for _, inst in ipairs(r:GetChildren()) do
                    if inst:IsA("LinearVelocity") then
                        inst.Enabled = false
                        if inst.PlaneVelocity ~= nil then inst.PlaneVelocity = Vector2.zero end
                    end
                end
            end)
            if tick() - t0 >= DROP_ASCEND_DURATION then
                if _dropConn then _dropConn:Disconnect(); _dropConn = nil end
                pcall(function()
                    local rp = RaycastParams.new()
                    rp.FilterDescendantsInstances = {cchar}
                    rp.FilterType = Enum.RaycastFilterType.Exclude
                    local rr = workspace:Raycast(r.Position, Vector3.new(0, -2000, 0), rp)
                    if rr then
                        local hum2 = cchar:FindFirstChildOfClass("Humanoid")
                        local off = ((hum2 and hum2.HipHeight) or 2) + (r.Size.Y / 2)
                        r.CFrame = CFrame.new(r.Position.X, rr.Position.Y + off, r.Position.Z)
                        r.AssemblyLinearVelocity = Vector3.zero
                        r.AssemblyAngularVelocity = Vector3.zero
                        if r.Velocity then r.Velocity = Vector3.zero end
                    end
                end)
                dropActive = false
                S.dropActive = false
                return
            end
            r.AssemblyLinearVelocity = Vector3.new(0, DROP_ASCEND_SPEED, 0)
            r.AssemblyAngularVelocity = Vector3.zero
            if r.Velocity then r.Velocity = Vector3.new(0, DROP_ASCEND_SPEED, 0) end
        end)
    end

    function F.stopDrop()
        dropActive = false
        S.dropActive = false
        if _dropConn then _dropConn:Disconnect(); _dropConn = nil end
    end

    LP.CharacterRemoving:Connect(function()
        F.stopDrop()
    end)

    -- Anti Drop (premier TweenService method)
    local antiDropTweenConn = nil
    local antiDropIsTweening = false
    local antiDropDieConns = {}
    local antiDropCharConn = nil
    local ANTI_DROP_TWEEN_SPEED = 59

    local function antiDropDisconnectDie()
        for _, conn in ipairs(antiDropDieConns) do
            pcall(function() conn:Disconnect() end)
        end
        antiDropDieConns = {}
    end

    local function antiDropSetupAntiDie(char)
        antiDropDisconnectDie()
        if not char or not S.antiDropEnabled then return end
        local humanoid = char:FindFirstChildOfClass("Humanoid") or char:WaitForChild("Humanoid", 5)
        if not humanoid then return end
        table.insert(antiDropDieConns, humanoid:GetPropertyChangedSignal("Health"):Connect(function()
            if S.antiDropEnabled and humanoid.Health < humanoid.MaxHealth then
                pcall(function() humanoid.Health = humanoid.MaxHealth end)
            end
        end))
        table.insert(antiDropDieConns, humanoid.Died:Connect(function()
            if not S.antiDropEnabled then return end
            task.defer(function()
                pcall(function() humanoid.Health = humanoid.MaxHealth end)
            end)
        end))
        pcall(function()
            game:GetService("StarterGui"):SetCore("ResetButtonCallback", false)
        end)
    end

    -- Method 36: CFrame step (carry) / Velocity (normal) — used while Anti Drop is ON
    local function antiDropApplyTweenSpeed(speed)
        if not S.antiDropEnabled then return end
        local char = LP.Character
        local root = char and char:FindFirstChild("HumanoidRootPart")
        local hum = char and char:FindFirstChildOfClass("Humanoid")
        if not root or not hum or hum.Health <= 0 then return end
        if S.autoBatEnabled or S.autoLeftEnabled or S.autoRightEnabled or tpBatEnabled or aimbotEnabled then
            return
        end
        if F.isRagdollState and F.isRagdollState(hum) then return end
        local dir = hum.MoveDirection
        if dir.Magnitude <= 0.05 then return end
        local u = dir.Unit
        local s = tonumber(speed) or F.getActiveMoveSpeed()
        -- carry / CS path uses CFrame step; normal uses Velocity
        local isCarry = (S.speedMode == true) or (S.laggerToggled and S.laggerPhase == 2)
        if isCarry then
            local step = u * (s / 60)
            root.CFrame = root.CFrame + Vector3.new(step.X, 0, step.Z)
        else
            root.Velocity = Vector3.new(u.X * s, root.Velocity.Y, u.Z * s)
            pcall(function()
                root.AssemblyLinearVelocity = Vector3.new(u.X * s, root.AssemblyLinearVelocity.Y, u.Z * s)
            end)
        end
    end

    function F.startAntiDrop()
        S.antiDropEnabled = true
        antiDropIsTweening = false
        if antiDropTweenConn then
            pcall(function() antiDropTweenConn:Disconnect() end)
            antiDropTweenConn = nil
        end
        antiDropTweenConn = RunService.Heartbeat:Connect(function()
            antiDropApplyTweenSpeed(F.getActiveMoveSpeed())
        end)
        antiDropSetupAntiDie(LP.Character)
        if antiDropCharConn then
            pcall(function() antiDropCharConn:Disconnect() end)
            antiDropCharConn = nil
        end
        antiDropCharConn = LP.CharacterAdded:Connect(function(char)
            task.wait(0.15)
            if S.antiDropEnabled then
                antiDropSetupAntiDie(char)
            end
        end)
        pcall(function()
            game:GetService("StarterGui"):SetCore("ResetButtonCallback", false)
        end)
    end

    function F.stopAntiDrop()
        S.antiDropEnabled = false
        antiDropIsTweening = false
        if antiDropTweenConn then
            pcall(function() antiDropTweenConn:Disconnect() end)
            antiDropTweenConn = nil
        end
        if antiDropCharConn then
            pcall(function() antiDropCharConn:Disconnect() end)
            antiDropCharConn = nil
        end
        antiDropDisconnectDie()
        pcall(function()
            game:GetService("StarterGui"):SetCore("ResetButtonCallback", true)
        end)
    end

    function F.doAutoTPDown(force)
        local char=LP.Character;if not char then return end
        local hrp=char:FindFirstChild("HumanoidRootPart");if not hrp then return end
        local hum2=char:FindFirstChildOfClass("Humanoid");if not hum2 then return end
        if not force then
            if hum2.FloorMaterial~=Enum.Material.Air then return end
            if hrp.Position.Y<S.autoTPHeight then return end
        end
        hrp.CFrame=CFrame.new(hrp.Position.X,-7.00,hrp.Position.Z)
            *CFrame.Angles(0,select(2,hrp.CFrame:ToEulerAnglesYXZ()),0)
        hrp.AssemblyLinearVelocity=Vector3.zero
    end

    function F.startAutoTP()
        if S.autoTPConn then task.cancel(S.autoTPConn);S.autoTPConn=nil end
        S.autoTPConn=task.spawn(function()
            while S.autoTPEnabled do
                task.wait(0.1)
                pcall(function() F.doAutoTPDown(false) end)
            end
        end)
    end

    function F.stopAutoTP()
        S.autoTPEnabled=false
        if S.autoTPConn then task.cancel(S.autoTPConn);S.autoTPConn=nil end
    end

    function F.runTPFloor()
        pcall(function() F.doAutoTPDown(true) end)
    end

    local defLightBrightness,defLightClock,defLightAmbient

    function F.applyAntiLagDerender(obj)
        pcall(function()
            if obj:IsA("Beam") or obj:IsA("Attachment") or obj:IsA("Highlight") or obj:IsA("BillboardGui") then
                local n = (obj.Name or ""):lower()
                if n:find("hub073") or n:find("tracer") or n:find("lineesp") or n:find("sevenup") or n:find("playeresp") then return end
                local p = obj.Parent
                while p do
                    local pn = (p.Name or ""):lower()
                    if pn:find("hub073") or pn:find("tracer") or pn == "hub073tracers" or pn:find("esp") then return end
                    p = p.Parent
                end
            end
            if obj:IsA("BasePart") then
                obj.Material = Enum.Material.Plastic
                obj.Reflectance = 0
                obj.CastShadow = false
            elseif obj:IsA("ParticleEmitter") or obj:IsA("Trail") or obj:IsA("Beam")
                or obj:IsA("Fire") or obj:IsA("Smoke") or obj:IsA("Sparkles") then
                obj.Enabled = false
            elseif obj:IsA("PointLight") or obj:IsA("SpotLight") or obj:IsA("SurfaceLight") then
                obj.Enabled = false
            elseif obj:IsA("Decal") or obj:IsA("Texture") then
                if not (obj.Name == "face" and obj.Parent and obj.Parent.Name == "Head") then
                    pcall(function() obj:Destroy() end)
                end
            elseif obj:IsA("SurfaceAppearance") then
                pcall(function() obj:Destroy() end)
            elseif obj:IsA("Accessory") or obj:IsA("Hat") then

                for _, d in ipairs(obj:GetDescendants()) do
                    if d:IsA("BasePart") then
                        d.CastShadow = false
                        d.Material = Enum.Material.Plastic
                    end
                end
            elseif obj:IsA("Animator") and obj.Parent and obj.Parent.Parent ~= LP.Character then
                pcall(function() obj:Destroy() end)
            elseif obj:IsA("PostEffect") or obj:IsA("BlurEffect") or obj:IsA("SunRaysEffect")
                or obj:IsA("ColorCorrectionEffect") or obj:IsA("BloomEffect") or obj:IsA("DepthOfFieldEffect")
                or obj:IsA("Atmosphere") or obj:IsA("Clouds") then
                obj.Enabled = false
            end
        end)
    end

    function F.enableAntiLag()
        S.antiLagEnabled = true
        local function derender(obj)
            pcall(function()
                if obj:IsA("ParticleEmitter") then obj.Enabled = false return end
                if obj:IsA("Decal") then obj.Transparency = 1 return end
                if obj:IsA("BasePart") then
                    obj.Material = Enum.Material.Plastic
                    obj.Reflectance = 0
                    obj.CastShadow = false
                end
            end)
        end
        pcall(function()
            Lighting.GlobalShadows = false
            Lighting.FogEnd = 9e9
            Lighting.Brightness = 1
            Lighting.EnvironmentDiffuseScale = 0
            Lighting.EnvironmentSpecularScale = 0
        end)
        for _, e in ipairs(Lighting:GetChildren()) do
            pcall(function()
                if e:IsA("BloomEffect") or e:IsA("BlurEffect") or e:IsA("SunRaysEffect") then
                    e.Enabled = false
                end
            end)
        end
        for _, d in ipairs(workspace:GetDescendants()) do derender(d) end
        if S.antiLagDescConn then pcall(function() S.antiLagDescConn:Disconnect() end) end
        S.antiLagDescConn = workspace.DescendantAdded:Connect(function(obj)
            if S.antiLagEnabled then derender(obj) end
        end)
    end

    function F.disableAntiLag()
        S.antiLagEnabled = false
        if S.antiLagDescConn then
            pcall(function() S.antiLagDescConn:Disconnect() end)
            S.antiLagDescConn = nil
        end
    end

    function F.startFpsUltraBoost()
        do return end
        if _fpsUltraRunning then return end
        _fpsUltraRunning = true
        S.fpsUltraBoostEnabled = true
        for _, c in ipairs(_fpsUltraConns) do pcall(function() c:Disconnect() end) end
        _fpsUltraConns = {}

        pcall(function()
            local lighting = game:GetService("Lighting")
            _fpsUltraOrig.GlobalShadows = lighting.GlobalShadows
            _fpsUltraOrig.Brightness = lighting.Brightness
            _fpsUltraOrig.ClockTime = lighting.ClockTime
            _fpsUltraOrig.OutdoorAmbient = lighting.OutdoorAmbient
            _fpsUltraOrig.Ambient = lighting.Ambient
            _fpsUltraOrig.FogEnd = lighting.FogEnd
            lighting.GlobalShadows = false
            lighting.Brightness = 1.2
            lighting.ClockTime = 14
            lighting.OutdoorAmbient = Color3.new(0.7, 0.7, 0.7)
            lighting.Ambient = Color3.new(0.7, 0.7, 0.7)
            lighting.FogEnd = 100000
            lighting.FogStart = 0
            pcall(function() settings().Rendering.QualityLevel = Enum.QualityLevel.Level01 end)
        end)

        pcall(function()
            local soundService = game:GetService("SoundService")
            if soundService then
                soundService.RespectFilteringEnabled = true
                soundService.Volume = 1
            end
        end)

        local function processBrainrot(obj)
            if not obj or not obj:IsA("BasePart") then return end
            pcall(function()
                obj.Transparency = 1
                obj.LocalTransparencyModifier = 1
                obj.CastShadow = false
                if obj:FindFirstChild("BrainrotBorder") then obj.BrainrotBorder:Destroy() end
                local box = Instance.new("SelectionBox")
                box.Name = "BrainrotBorder"
                box.Adornee = obj
                box.Color3 = Color3.new(1, 1, 1)
                box.Transparency = 0
                box.LineThickness = 0.03
                box.Parent = obj
            end)
        end

        pcall(function()
            for _, obj in ipairs(workspace:GetDescendants()) do
                if _fpsUltraIsBrainrot(obj) then
                    if obj:IsA("BasePart") then
                        processBrainrot(obj)
                    elseif obj:IsA("Model") then
                        for _, p in ipairs(obj:GetDescendants()) do
                            if p:IsA("BasePart") then processBrainrot(p) end
                        end
                    end
                elseif obj:IsA("Decal") or obj:IsA("Texture") or obj:IsA("ParticleEmitter")
                    or obj:IsA("Fire") or obj:IsA("Sparkles") or obj:IsA("Smoke") or obj:IsA("Trail")
                    or obj:IsA("PointLight") or obj:IsA("SpotLight") or obj:IsA("SurfaceLight") then
                    pcall(function() obj:Destroy() end)
                elseif obj:IsA("BasePart") and obj.Name == "Part" and not _fpsUltraIsBrainrot(obj) then
                    pcall(function()
                        obj.Transparency = 1
                        obj.Material = Enum.Material.Plastic
                        obj.CastShadow = false
                    end)
                elseif obj:IsA("BasePart") and not _fpsUltraIsBrainrot(obj) then
                    pcall(function()
                        if obj.Material == Enum.Material.Neon then obj.Material = Enum.Material.Plastic end
                        obj.CastShadow = false
                    end)
                end
            end
        end)

        local renderConn = RunService.Heartbeat:Connect(function()
            if not _fpsUltraRunning then return end
            if not _perfEveryN(30, 15) then return end
            local char = LP.Character
            local root = char and char:FindFirstChild("HumanoidRootPart")
            if not root then return end
            local playerParts = {}
            for _, plr in ipairs(Players:GetPlayers()) do
                if plr.Character then
                    for _, p in ipairs(plr.Character:GetDescendants()) do
                        if p:IsA("BasePart") then playerParts[p] = true end
                    end
                end
            end
            for _, obj in ipairs(workspace:GetDescendants()) do
                if obj:IsA("BasePart") and obj ~= root then
                    if _fpsUltraIsBrainrot(obj) then
                        obj.LocalTransparencyModifier = 1
                        obj.Transparency = 1
                    elseif playerParts[obj] then
                        obj.LocalTransparencyModifier = 0
                    else
                        local d = (obj.Position - root.Position).Magnitude
                        if d > 10 then
                            obj.LocalTransparencyModifier = 1
                        else
                            obj.LocalTransparencyModifier = 0
                        end
                    end
                end
            end
        end)
        table.insert(_fpsUltraConns, renderConn)

        local addConn = workspace.DescendantAdded:Connect(function(obj)
            if not _fpsUltraRunning then return end
            if _fpsUltraIsBrainrot(obj) then
                task.defer(function()
                    if obj:IsA("BasePart") then processBrainrot(obj)
                    elseif obj:IsA("Model") then
                        for _, p in ipairs(obj:GetDescendants()) do
                            if p:IsA("BasePart") then processBrainrot(p) end
                        end
                    end
                end)
            end
        end)
        table.insert(_fpsUltraConns, addConn)
        --print("[073 Hub] Fps Ultra Boost ON")
    end

    function F.stopFpsUltraBoost()
        _fpsUltraRunning = false
        S.fpsUltraBoostEnabled = false
        for _, c in ipairs(_fpsUltraConns) do pcall(function() c:Disconnect() end) end
        _fpsUltraConns = {}
        pcall(function()
            local lighting = game:GetService("Lighting")
            if _fpsUltraOrig.GlobalShadows ~= nil then lighting.GlobalShadows = _fpsUltraOrig.GlobalShadows end
            if _fpsUltraOrig.Brightness then lighting.Brightness = _fpsUltraOrig.Brightness end
            if _fpsUltraOrig.ClockTime then lighting.ClockTime = _fpsUltraOrig.ClockTime end
            if _fpsUltraOrig.OutdoorAmbient then lighting.OutdoorAmbient = _fpsUltraOrig.OutdoorAmbient end
            if _fpsUltraOrig.Ambient then lighting.Ambient = _fpsUltraOrig.Ambient end
            if _fpsUltraOrig.FogEnd then lighting.FogEnd = _fpsUltraOrig.FogEnd end
            pcall(function() settings().Rendering.QualityLevel = Enum.QualityLevel.Automatic end)
        end)
        pcall(function()
            for _, obj in ipairs(workspace:GetDescendants()) do
                if obj:IsA("BasePart") then
                    pcall(function() obj.LocalTransparencyModifier = 0 end)
                    if obj:FindFirstChild("BrainrotBorder") then
                        pcall(function() obj.BrainrotBorder:Destroy() end)
                    end
                end
            end
        end)
        --print("[073 Hub] Fps Ultra Boost OFF")
    end


    local _kbOverlay = {
        screen = nil,
        conns = {},
        buttons = {},
        enabled = false,
        panel = nil,
    }

    local function _kbDisconnectAll()
        for _, c in ipairs(_kbOverlay.conns) do
            pcall(function() c:Disconnect() end)
        end
        _kbOverlay.conns = {}
    end

    local function _kbTrack(conn)
        table.insert(_kbOverlay.conns, conn)
        return conn
    end

    function F.stopKeyboardOverlay()
        S.keyboardOverlayEnabled = false
        _kbOverlay.enabled = false
        _kbDisconnectAll()
        if _kbOverlay.screen then
            pcall(function() _kbOverlay.screen:Destroy() end)
            _kbOverlay.screen = nil
        end
        _kbOverlay.buttons = {}
        _kbOverlay.panel = nil
        pcall(function()
            local env = (type(getgenv) == "function" and getgenv()) or _G
            if env.__Hub073KeyboardOverlay then
                env.__Hub073KeyboardOverlay = nil
            end
        end)
    end

    function F.startKeyboardOverlay()
        if _kbOverlay.enabled and _kbOverlay.screen and _kbOverlay.screen.Parent then
            S.keyboardOverlayEnabled = true
            return
        end
        F.stopKeyboardOverlay()
        S.keyboardOverlayEnabled = true
        _kbOverlay.enabled = true

        local guiParent = nil
        pcall(function() if type(gethui) == "function" then guiParent = gethui() end end)
        if not guiParent then pcall(function() guiParent = game:GetService("CoreGui") end) end
        if not guiParent then guiParent = LP:FindFirstChild("PlayerGui") or LP:WaitForChild("PlayerGui", 3) end
        if not guiParent then return end

        local savedX = tonumber(S.keyboardOverlayPosX)
        local savedY = tonumber(S.keyboardOverlayPosY)
        if not savedX or savedX < 0 or savedX > 1 then savedX = 0.025 end
        if not savedY or savedY < 0 or savedY > 1 then savedY = 0.62 end

        local CONFIG = {
            guiName = "Hub073KeyboardOverlay",
            uiScale = 1.0,
            keySize = 30,
            keyGap = 4,
            mouseWidth = 58,
            mouseHeight = 92,
            mouseGap = 16,
            trackSpeed = 1500,
            trackAttack = 0.65,
            trackRelease = 0.18,
            toggleKey = Enum.KeyCode.F8,
            hideWhileTyping = true,
            wheelFlash = 0.13,
            fadeIn = 0.03,
            fadeOut = 0.11,
            defaultPos = Vector2.new(savedX, savedY),
        }

        local THEME = {
            panelFill = Color3.fromRGB(0, 0, 0),
            panelIdle = 0.40,
            keyFill = Color3.fromRGB(0, 0, 0),
            keyIdle = 0.05,
            pressFill = S.kbOverlayColor or Color3.fromRGB(255, 255, 255),
            pressIdle = 0.00,
            line = S.kbOverlayColor or Color3.fromRGB(255, 255, 255),
            lineIdle = 0.35,
            lineActive = 0.00,
            textIdle = Color3.fromRGB(255, 255, 255),
            textActive = Color3.fromRGB(0, 0, 0),
            textStroke = 0.70,
            headerIdle = 0.30,
            headerLocked = 1.00,
            font = Enum.Font.GothamBold,
            fontCaption = Enum.Font.Gotham,
        }

        local KEYBOARD_ROWS = {
            {
                { id = "Escape", label = "ESC", w = 1.5 },
                { id = "One", label = "1" }, { id = "Two", label = "2" },
                { id = "Three", label = "3" }, { id = "Four", label = "4" },
                { id = "Five", label = "5" },
            },
            {
                { id = "Tab", label = "TAB", w = 1.5 },
                { id = "Q", label = "Q" }, { id = "W", label = "W" },
                { id = "E", label = "E" }, { id = "R", label = "R" },
                { id = "T", label = "T" },
            },
            {
                { id = "CapsLock", label = "CAPS", w = 1.75 },
                { id = "A", label = "A" }, { id = "S", label = "S" },
                { id = "D", label = "D" }, { id = "F", label = "F" },
                { id = "G", label = "G" },
            },
            {
                { id = "LeftShift", label = "SHIFT", w = 2.25 },
                { id = "Z", label = "Z" }, { id = "X", label = "X" },
                { id = "C", label = "C" }, { id = "V", label = "V" },
                { id = "B", label = "B" },
            },
            {
                { id = "LeftControl", label = "CTRL", w = 1.5 },
                { id = "LeftAlt", label = "ALT", w = 1.25 },
                { id = "Space", label = "SPACE", w = 3.5 },
            },
        }

        local function new(class, props, children)
            local inst = Instance.new(class)
            for key, value in pairs(props or {}) do
                if key ~= "Parent" then inst[key] = value end
            end
            for _, child in ipairs(children or {}) do
                child.Parent = inst
            end
            if props and props.Parent then inst.Parent = props.Parent end
            return inst
        end

        local function kTween(inst, duration, props)
            TS:Create(inst, TweenInfo.new(duration, Enum.EasingStyle.Quad, Enum.EasingDirection.Out), props):Play()
        end

        local Cell = {}
        Cell.__index = Cell
        function Cell.new(fill, stroke, label)
            return setmetatable({ fill = fill, stroke = stroke, label = label, active = false }, Cell)
        end
        function Cell:setActive(on)
            if self.active == on then return end
            self.active = on
            local duration = on and CONFIG.fadeIn or CONFIG.fadeOut
            if self.frame then
                kTween(self.frame, duration, {
                    BackgroundColor3 = on and THEME.pressFill or THEME.keyFill,
                    BackgroundTransparency = on and THEME.pressIdle or THEME.keyIdle,
                })
            end
            if self.fill and not self.analog then
                kTween(self.fill, duration, {
                    BackgroundColor3 = THEME.pressFill,
                    BackgroundTransparency = on and THEME.pressIdle or 1,
                })
            end
            if self.stroke then
                kTween(self.stroke, duration, {
                    Color = THEME.line,
                    Transparency = on and THEME.lineActive or THEME.lineIdle,
                })
            end
            if self.label and not self.analog then
                kTween(self.label, duration, {
                    TextColor3 = on and THEME.textActive or THEME.textIdle,
                    TextStrokeTransparency = on and 1 or THEME.textStroke,
                })
            end
            if self.dot then
                self.dot.BackgroundColor3 = on and Color3.new(0, 0, 0) or THEME.pressFill
                self.dot.BackgroundTransparency = on and 0 or 0.15
            end
        end
        function Cell:flash(duration)
            self.flashToken = (self.flashToken or 0) + 1
            local token = self.flashToken
            self:setActive(true)
            task.delay(duration or CONFIG.wheelFlash, function()
                if self.flashToken == token then self:setActive(false) end
            end)
        end

        local function makeKey(parent, def, rect)
            local cornerR = def.circle and UDim.new(1, 0) or UDim.new(0, 5)
            local frame = new("Frame", {
                Name = def.id or "Key",
                BackgroundColor3 = THEME.keyFill,
                BackgroundTransparency = THEME.keyIdle,
                BorderSizePixel = 0,
                ClipsDescendants = true,
                Position = UDim2.fromOffset(rect.x, rect.y),
                Size = UDim2.fromOffset(rect.w, rect.h),
                Parent = parent,
            }, { new("UICorner", { CornerRadius = cornerR }) })
            local stroke = new("UIStroke", {
                Color = THEME.line,
                Thickness = 1.4,
                Transparency = THEME.lineIdle,
                ApplyStrokeMode = Enum.ApplyStrokeMode.Border,
                Parent = frame,
            })
            local fill = new("Frame", {
                Name = "Fill",
                BackgroundColor3 = THEME.pressFill,
                BackgroundTransparency = 1,
                BorderSizePixel = 0,
                Size = UDim2.fromScale(1, 1),
                ZIndex = 2,
                Parent = frame,
            }, { new("UICorner", { CornerRadius = cornerR }) })
            local label
            if def.label then
                label = new("TextLabel", {
                    BackgroundTransparency = 1,
                    Size = UDim2.fromScale(1, 1),
                    Font = THEME.font,
                    Text = def.label,
                    TextColor3 = THEME.textIdle,
                    TextScaled = true,
                    TextStrokeColor3 = Color3.new(0, 0, 0),
                    TextStrokeTransparency = THEME.textStroke,
                    ZIndex = 3,
                    Parent = frame,
                }, {
                    new("UIPadding", {
                        PaddingLeft = UDim.new(0, 3), PaddingRight = UDim.new(0, 3),
                        PaddingTop = UDim.new(0, 2), PaddingBottom = UDim.new(0, 2),
                    }),
                    new("UITextSizeConstraint", { MaxTextSize = math.max(9, math.floor(rect.h * 0.55)) }),
                })
            end
            local cell = Cell.new(fill, stroke, label)
            cell.frame = frame
            return cell
        end

        pcall(function()
            local old = guiParent:FindFirstChild(CONFIG.guiName)
            if old then old:Destroy() end
        end)

        local screen = new("ScreenGui", {
            Name = CONFIG.guiName,
            ResetOnSpawn = false,
            IgnoreGuiInset = true,
            DisplayOrder = 9999,
            ZIndexBehavior = Enum.ZIndexBehavior.Sibling,
            Parent = guiParent,
        })
        _kbOverlay.screen = screen

        local HEADER_HEIGHT, PADDING = 18, 8
        local unit = CONFIG.keySize + CONFIG.keyGap
        local widestRow = 0
        for _, row in ipairs(KEYBOARD_ROWS) do
            local total = 0
            for _, def in ipairs(row) do total = total + (def.w or 1) end
            widestRow = math.max(widestRow, total)
        end
        local keysWidth = widestRow * unit - CONFIG.keyGap
        local keysHeight = #KEYBOARD_ROWS * unit - CONFIG.keyGap
        local bodySize = Vector2.new(
            keysWidth + CONFIG.mouseGap + CONFIG.mouseWidth,
            math.max(keysHeight, CONFIG.mouseHeight)
        )

        local root = new("Frame", {
            Name = "Panel",
            Active = false,
            BackgroundColor3 = THEME.panelFill,
            BackgroundTransparency = THEME.panelIdle,
            BorderSizePixel = 0,
            Size = UDim2.fromOffset(bodySize.X + PADDING * 2, bodySize.Y + PADDING * 2 + HEADER_HEIGHT),
            Position = UDim2.fromScale(CONFIG.defaultPos.X, CONFIG.defaultPos.Y),
            Parent = screen,
        }, {
            new("UIScale", { Scale = CONFIG.uiScale }),
            new("UICorner", { CornerRadius = UDim.new(0, 10) }),
        })
        new("UIStroke", {
            Color = THEME.line, Thickness = 1, Transparency = 0.85,
            ApplyStrokeMode = Enum.ApplyStrokeMode.Border, Parent = root,
        })
        local header = new("Frame", {
            Name = "Handle", Active = true,
            BackgroundColor3 = Color3.new(0, 0, 0),
            BackgroundTransparency = THEME.headerIdle,
            BorderSizePixel = 0,
            Size = UDim2.new(1, 0, 0, HEADER_HEIGHT),
            Parent = root,
        }, {
            new("UICorner", { CornerRadius = UDim.new(0, 8) }),
            new("UIPadding", { PaddingLeft = UDim.new(0, 8), PaddingRight = UDim.new(0, 5) }),
        })
        new("TextLabel", {
            BackgroundTransparency = 1, Size = UDim2.new(1, -22, 1, 0),
            Font = THEME.fontCaption, Text = "KEYBOARD + MOUSE",
            TextSize = 10, TextXAlignment = Enum.TextXAlignment.Left,
            TextColor3 = THEME.textIdle, TextTransparency = 0.25, Parent = header,
        })
        local body = new("Frame", {
            Name = "Body", Active = false, BackgroundTransparency = 1,
            Position = UDim2.fromOffset(PADDING, HEADER_HEIGHT + PADDING),
            Size = UDim2.fromOffset(bodySize.X, bodySize.Y), Parent = root,
        })

        local Buttons = {}
        for rowIndex, row in ipairs(KEYBOARD_ROWS) do
            local x = 0
            local y = (rowIndex - 1) * unit
            for _, def in ipairs(row) do
                local width = (def.w or 1) * unit - CONFIG.keyGap
                local cell = makeKey(body, def, {
                    x = math.floor(x), y = y, w = math.floor(width), h = CONFIG.keySize,
                })
                if def.id then Buttons[def.id] = cell end
                x = x + (def.w or 1) * unit
            end
        end

        local originX = keysWidth + CONFIG.mouseGap
        local originY = math.floor((bodySize.Y - CONFIG.mouseHeight) / 2)
        local width, height = CONFIG.mouseWidth, CONFIG.mouseHeight
        local radius = math.floor(width * 0.42)
        local topPart = math.floor(height * 0.40)
        local wheelW = math.max(7, math.floor(width * 0.16))
        local sideW = math.floor((width - wheelW - 4) / 2)

        local mouseBody = new("Frame", {
            Name = "Mouse",
            BackgroundColor3 = THEME.keyFill,
            BackgroundTransparency = THEME.keyIdle,
            BorderSizePixel = 0, ClipsDescendants = true,
            Position = UDim2.fromOffset(originX, originY),
            Size = UDim2.fromOffset(width, height), Parent = body,
        }, { new("UICorner", { CornerRadius = UDim.new(0, radius) }) })
        new("UIStroke", {
            Color = THEME.line, Thickness = 1.4, Transparency = THEME.lineIdle,
            ApplyStrokeMode = Enum.ApplyStrokeMode.Border, Parent = mouseBody,
        })

        local function makeMouseButton(name, clipX, fillX)
            local clip = new("Frame", {
                Name = name .. "Clip", BackgroundTransparency = 1, ClipsDescendants = true,
                Position = UDim2.fromOffset(clipX, 0), Size = UDim2.fromOffset(sideW, topPart),
                ZIndex = 2, Parent = mouseBody,
            })
            local fill = new("Frame", {
                Name = name, BackgroundColor3 = THEME.pressFill, BackgroundTransparency = 1,
                BorderSizePixel = 0, Position = UDim2.fromOffset(fillX, 0),
                Size = UDim2.fromOffset(sideW + radius, topPart + radius), Parent = clip,
            }, { new("UICorner", { CornerRadius = UDim.new(0, radius) }) })
            local cell = Cell.new(fill, nil, nil)
            cell.frame = fill
            return cell
        end

        local mouseLeft = makeMouseButton("LeftButton", 0, 0)
        local mouseRight = makeMouseButton("RightButton", width - sideW, -radius)
        Buttons.Mouse1 = mouseLeft
        Buttons.Mouse2 = mouseRight

        new("Frame", {
            Name = "Split", BackgroundColor3 = THEME.line, BackgroundTransparency = 0.55,
            BorderSizePixel = 0, Position = UDim2.fromOffset(0, topPart),
            Size = UDim2.new(1, 0, 0, 1), ZIndex = 4, Parent = mouseBody,
        })

        local wheelH = math.floor(topPart * 0.60)
        local wheel = new("Frame", {
            Name = "Wheel", BackgroundColor3 = THEME.keyFill, BackgroundTransparency = 0.1,
            BorderSizePixel = 0, ClipsDescendants = true,
            Position = UDim2.fromOffset(math.floor((width - wheelW) / 2), math.floor(topPart * 0.20)),
            Size = UDim2.fromOffset(wheelW, wheelH), ZIndex = 5, Parent = mouseBody,
        }, { new("UICorner", { CornerRadius = UDim.new(0, math.floor(wheelW / 2)) }) })
        local wheelStroke = new("UIStroke", {
            Color = THEME.line, Thickness = 1.2, Transparency = THEME.lineIdle,
            ApplyStrokeMode = Enum.ApplyStrokeMode.Border, Parent = wheel,
        })
        local wheelFill = new("Frame", {
            Name = "Fill", BackgroundColor3 = THEME.pressFill, BackgroundTransparency = 1,
            BorderSizePixel = 0, Size = UDim2.fromScale(1, 1), ZIndex = 6, Parent = wheel,
        }, { new("UICorner", { CornerRadius = UDim.new(0, math.floor(wheelW / 2)) }) })
        local mouseWheel = Cell.new(wheelFill, wheelStroke, nil)
        mouseWheel.frame = wheel
        mouseWheel.basePosition = wheel.Position
        Buttons.Mouse3 = mouseWheel

        local zone = math.min(math.floor(width / 2) - 7, math.floor((height - topPart) / 2) - 6)
        local ring = new("Frame", {
            Name = "TrackZone", BackgroundTransparency = 1, AnchorPoint = Vector2.new(0.5, 0.5),
            Position = UDim2.fromOffset(math.floor(width / 2), topPart + math.floor((height - topPart) / 2)),
            Size = UDim2.fromOffset(zone * 2, zone * 2), ZIndex = 3, Parent = mouseBody,
        }, { new("UICorner", { CornerRadius = UDim.new(1, 0) }) })
        new("UIStroke", {
            Color = THEME.line, Thickness = 1, Transparency = 0.8,
            ApplyStrokeMode = Enum.ApplyStrokeMode.Border, Parent = ring,
        })
        local trackerDot = new("Frame", {
            Name = "Pointer", BackgroundColor3 = THEME.pressFill, BackgroundTransparency = 0.2,
            BorderSizePixel = 0, AnchorPoint = Vector2.new(0.5, 0.5),
            Position = UDim2.fromScale(0.5, 0.5), Size = UDim2.fromOffset(8, 8), ZIndex = 4, Parent = ring,
        }, { new("UICorner", { CornerRadius = UDim.new(1, 0) }) })
        local trackerRadius = zone - 4

        _kbOverlay.buttons = Buttons

        local function isTyping()
            return CONFIG.hideWhileTyping and UIS:GetFocusedTextBox() ~= nil
        end
        local function releaseAll()
            for _, cell in pairs(Buttons) do cell:setActive(false) end
            if mouseWheel.frame and mouseWheel.basePosition then
                mouseWheel.frame.Position = mouseWheel.basePosition
            end
            trackerDot.Position = UDim2.fromScale(0.5, 0.5)
        end
        local function resolveId(input)
            local kind = input.UserInputType
            if kind == Enum.UserInputType.Keyboard then return input.KeyCode.Name
            elseif kind == Enum.UserInputType.MouseButton1 then return "Mouse1"
            elseif kind == Enum.UserInputType.MouseButton2 then return "Mouse2"
            elseif kind == Enum.UserInputType.MouseButton3 then return "Mouse3"
            end
            return nil
        end

        _kbTrack(UIS.InputBegan:Connect(function(input)
            if isTyping() then return end
            local id = resolveId(input)
            local cell = id and Buttons[id]
            if cell then cell:setActive(true) end
        end))
        _kbTrack(UIS.InputEnded:Connect(function(input)
            local id = resolveId(input)
            local cell = id and Buttons[id]
            if cell then cell:setActive(false) end
        end))
        _kbTrack(UIS.InputChanged:Connect(function(input)
            if input.UserInputType ~= Enum.UserInputType.MouseWheel or isTyping() then return end
            local up = input.Position.Z > 0
            mouseWheel:flash(CONFIG.wheelFlash)
            local base = mouseWheel.basePosition
            mouseWheel.frame.Position = base + UDim2.fromOffset(0, up and -3 or 3)
            task.delay(CONFIG.wheelFlash, function()
                if mouseWheel.frame then
                    kTween(mouseWheel.frame, 0.08, { Position = base })
                end
            end)
        end))

        local pointerAccum, pointerCurrent, pointerLast = Vector2.zero, Vector2.zero, nil
        _kbTrack(UIS.InputChanged:Connect(function(input)
            if input.UserInputType ~= Enum.UserInputType.MouseMovement then return end
            local delta = Vector2.new(input.Delta.X, input.Delta.Y)
            if delta.Magnitude < 0.001 then
                local position = Vector2.new(input.Position.X, input.Position.Y)
                if pointerLast then delta = position - pointerLast end
                pointerLast = position
            else
                pointerLast = nil
            end
            pointerAccum = pointerAccum + delta
        end))
        _kbTrack(RunService.RenderStepped:Connect(function(step)
            local frame = math.clamp(step, 1 / 240, 1 / 15)
            local velocity = pointerAccum / frame
            pointerAccum = Vector2.zero
            local target = Vector2.zero
            local speed = velocity.Magnitude
            if speed > 1 then
                local amount = 1 - math.exp(-speed / CONFIG.trackSpeed)
                target = velocity.Unit * math.clamp(amount, 0, 1)
            end
            local rate = target.Magnitude > pointerCurrent.Magnitude and CONFIG.trackAttack or CONFIG.trackRelease
            pointerCurrent = pointerCurrent + (target - pointerCurrent) * math.clamp(rate * frame * 60, 0, 1)
            if pointerCurrent.Magnitude < 0.004 then pointerCurrent = Vector2.zero end
            trackerDot.Position = UDim2.new(
                0.5, pointerCurrent.X * trackerRadius,
                0.5, pointerCurrent.Y * trackerRadius
            )
            trackerDot.BackgroundTransparency = 0.2 - math.min(pointerCurrent.Magnitude, 1) * 0.2
        end))

        do
            local dragInput, startMouse, startOffset
            local function persistKbPos()
                local px = root.Position.X.Scale
                local py = root.Position.Y.Scale
                if type(px) == "number" and type(py) == "number" then
                    S.keyboardOverlayPosX = math.clamp(px, 0, 1)
                    S.keyboardOverlayPosY = math.clamp(py, 0, 1)
                    pcall(function() F.saveConfig() end)
                end
            end
            _kbTrack(header.InputBegan:Connect(function(input)
                local kind = input.UserInputType
                if kind ~= Enum.UserInputType.MouseButton1 and kind ~= Enum.UserInputType.Touch then return end
                local view = screen.AbsoluteSize
                dragInput = input
                startMouse = Vector2.new(input.Position.X, input.Position.Y)
                startOffset = Vector2.new(root.Position.X.Scale * view.X, root.Position.Y.Scale * view.Y)
            end))
            _kbTrack(UIS.InputChanged:Connect(function(input)
                if not dragInput then return end
                local moving
                if dragInput.UserInputType == Enum.UserInputType.Touch then
                    moving = (input == dragInput)
                else
                    moving = (input.UserInputType == Enum.UserInputType.MouseMovement)
                end
                if not moving then return end
                local view = screen.AbsoluteSize
                local target = startOffset + (Vector2.new(input.Position.X, input.Position.Y) - startMouse)
                root.Position = UDim2.fromScale(target.X / view.X, target.Y / view.Y)
            end))
            _kbTrack(UIS.InputEnded:Connect(function(input)
                if dragInput and input == dragInput then
                    dragInput = nil
                    persistKbPos()
                end
            end))
        end
        _kbOverlay.panel = root

        _kbTrack(UIS.InputBegan:Connect(function(input)
            if input.KeyCode ~= CONFIG.toggleKey or isTyping() then return end
            screen.Enabled = not screen.Enabled
            if not screen.Enabled then releaseAll() end
        end))
        _kbTrack(UIS.WindowFocusReleased:Connect(releaseAll))

        pcall(function()
            local env = (type(getgenv) == "function" and getgenv()) or _G
            env.__Hub073KeyboardOverlay = { destroy = F.stopKeyboardOverlay, screen = screen }
        end)
    end

    function F.findMedusa()
        local c = LP.Character
        if not c then return nil end
        for _, t in ipairs(c:GetChildren()) do
            if t:IsA("Tool") then
                local n = t.Name:lower()
                if n:find("medusa") or n:find("head") or n:find("stone") then return t end
            end
        end
        local bp = LP:FindFirstChild("Backpack")
        if bp then
            for _, t in ipairs(bp:GetChildren()) do
                if t:IsA("Tool") then
                    local n = t.Name:lower()
                    if n:find("medusa") or n:find("head") or n:find("stone") then return t end
                end
            end
        end
        return nil
    end

    function F.useMedusaCounter()
        if S.medusaDebounce then return end
        if MEDUSA_COOLDOWN > (tick() - S.medusaLastUsed) then return end
        local c = LP.Character
        if not c then return end
        S.medusaDebounce = true
        local med = F.findMedusa()
        if not med then S.medusaDebounce = false; return end
        if med.Parent ~= c then
            local hum2 = c:FindFirstChildOfClass("Humanoid")
            if hum2 then hum2:EquipTool(med) end
        end
        pcall(function() med:Activate() end)
        S.medusaLastUsed = tick()
        S.medusaDebounce = false
    end

    function F.onAnchorChanged(part)
        return part:GetPropertyChangedSignal("Anchored"):Connect(function()
            if part.Anchored and part.Transparency == 1 then
                if S.medusaResetEnabled then
                    if cursedInstaReset then cursedInstaReset() end
                elseif S.medusaCounterEnabled then
                    F.useMedusaCounter()
                end
            end
        end)
    end

    function F.setupMedusa(char)
        for _, c in pairs(Conns.anchor) do pcall(function() c:Disconnect() end) end
        Conns.anchor = {}
        if not char then return end
        for _, part in ipairs(char:GetDescendants()) do
            if part:IsA("BasePart") then
                table.insert(Conns.anchor, F.onAnchorChanged(part))
            end
        end
        table.insert(Conns.anchor, char.DescendantAdded:Connect(function(part)
            if part:IsA("BasePart") then
                table.insert(Conns.anchor, F.onAnchorChanged(part))
            end
        end))
    end

    function F.stopMedusaCounter()
        for _, c in pairs(Conns.anchor) do pcall(function() c:Disconnect() end) end
        Conns.anchor = {}
    end

    function F.refreshMedusaWatch()
        if S.medusaCounterEnabled or S.medusaResetEnabled then
            F.setupMedusa(LP.Character)
        else
            F.stopMedusaCounter()
        end
    end

    local BAT_COUNTER_SLAP_LIST={"Bat","Slap","Iron Slap","Gold Slap","Diamond Slap","Emerald Slap","Ruby Slap","Dark Matter Slap","Flame Slap","Nuclear Slap","Galaxy Slap","Glitched Slap"}
    function F.findBatForCounter()
        local c=LP.Character;if not c then return nil end
        local bp=LP:FindFirstChildOfClass("Backpack")
        for _,name in ipairs(BAT_COUNTER_SLAP_LIST) do
            local t=c:FindFirstChild(name) or (bp and bp:FindFirstChild(name));if t then return t end
        end
        for _,ch in ipairs(c:GetChildren()) do if ch:IsA("Tool") and ch.Name:lower():find("bat") then return ch end end
        if bp then for _,ch in ipairs(bp:GetChildren()) do if ch:IsA("Tool") and ch.Name:lower():find("bat") then return ch end end end
        return nil
    end

    function F.swingBatForCounter(bat,char)
        local hum2=char:FindFirstChildOfClass("Humanoid")
        if bat.Parent~=char then if hum2 then pcall(function() hum2:EquipTool(bat) end) end;task.wait(0.05) end
        local remote=bat:FindFirstChildOfClass("RemoteEvent") or bat:FindFirstChildOfClass("RemoteFunction")
        if remote and remote:IsA("RemoteEvent") then
            pcall(function() remote:FireServer() end);task.wait(0.15);pcall(function() remote:FireServer() end)
        else pcall(function() bat:Activate() end);task.wait(0.15);pcall(function() bat:Activate() end) end
    end

    S.startBatCounter=function()
        if Conns.batCounter then return end
        Conns.batCounter=RunService.Heartbeat:Connect(function()
            if not S.batCounterEnabled then return end
            if batCounterDebounce then return end
            local char=LP.Character;if not char then return end
            local hum2=char:FindFirstChildOfClass("Humanoid");if not hum2 then return end
            local st=hum2:GetState()
            if st==Enum.HumanoidStateType.Physics or st==Enum.HumanoidStateType.Ragdoll or st==Enum.HumanoidStateType.FallingDown then
                batCounterDebounce=true
                task.spawn(function()
                    local bat=F.findBatForCounter()
                    if bat then F.swingBatForCounter(bat,char) end
                    task.wait(0.5);batCounterDebounce=false
                end)
            end
        end)
    end

    S.stopBatCounter=function()
        if Conns.batCounter then Conns.batCounter:Disconnect();Conns.batCounter=nil end
        batCounterDebounce=false
    end

    function F.getAutoBatTarget()
        local root=LP.Character and LP.Character:FindFirstChild("HumanoidRootPart")
        if not root then return nil end
        local now=tick()
        if now-S._autoBatLastScan<=0.1 and S._autoBatTarget and S._autoBatTarget.Parent then
            local hum=S._autoBatTarget.Parent:FindFirstChildOfClass("Humanoid")
            if hum and hum.Health>0 then return S._autoBatTarget end
        end
        S._autoBatLastScan=now
        S._autoBatTarget=nil
        local closest,minDist=nil,math.huge
        for _,plr in ipairs(Players:GetPlayers()) do
            if plr~=LP and plr.Character then
                local tRoot=plr.Character:FindFirstChild("HumanoidRootPart")
                local hum=plr.Character:FindFirstChildOfClass("Humanoid")
                if tRoot and hum and hum.Health>0 then
                    local dist=(tRoot.Position-root.Position).Magnitude
                    if dist<minDist then minDist=dist;closest=tRoot end
                end
            end
        end
        S._autoBatTarget=closest
        return S._autoBatTarget
    end

    S.resetAutoBatMotion=function()
        local char=LP.Character
        local hrp=char and char:FindFirstChild("HumanoidRootPart")
        local hum=char and char:FindFirstChildOfClass("Humanoid")
        if hrp then hrp.AssemblyLinearVelocity=hrp.AssemblyLinearVelocity*0.3;hrp.AssemblyAngularVelocity=Vector3.zero end
        if hum then hum.AutoRotate=true end
    end

    local _autoTPWasEnabled=false
    S.enableAutoBat = function()
        if S.autoLeftEnabled then S.autoLeftEnabled=false;if S.autoLeftSetVisual then S.autoLeftSetVisual(false) end;F.stopAutoLeft() end
        if S.autoRightEnabled then S.autoRightEnabled=false;if S.autoRightSetVisual then S.autoRightSetVisual(false) end;F.stopAutoRight() end
        if S.autoTPEnabled then _autoTPWasEnabled=true;F.stopAutoTP();if VS.setAutoTPVisual then VS.setAutoTPVisual(false) end else _autoTPWasEnabled=false end
        S.autoBatEquippedThisRun=false
        S.autoBatEnabled=true
    end

    S.disableAutoBat = function()
        S.autoBatEnabled=false
        S.autoBatEquippedThisRun=false
        local char=LP.Character
        if char then
            local hum2=char:FindFirstChildOfClass("Humanoid")
            if hum2 then hum2.AutoRotate=true end
        end
        if S.resetAutoBatMotion then S.resetAutoBatMotion() end
        if _autoTPWasEnabled then
            _autoTPWasEnabled=false;S.autoTPEnabled=true
            if VS.setAutoTPVisual then VS.setAutoTPVisual(true) end;F.startAutoTP()
        end
        F.destroyProxy()
    end

    function F.queueAutoLeftStart()
        if not F.safeModeTryStart() then S.autoLeftEnabled=false; if S.autoLeftSetVisual then S.autoLeftSetVisual(false) end; return end
        S.autoLeftEnabled=true
        if S.autoRightEnabled then S.autoRightEnabled=false;if S.autoRightSetVisual then S.autoRightSetVisual(false) end;F.stopAutoRight() end
        if S.autoBatEnabled then S.disableAutoBat();if S.autoBatSetVisual then S.autoBatSetVisual(false) end end
        F.startAutoLeft()
    end

    function F.queueAutoRightStart()
        if not F.safeModeTryStart() then S.autoRightEnabled=false; if S.autoRightSetVisual then S.autoRightSetVisual(false) end; return end
        S.autoRightEnabled=true
        if S.autoLeftEnabled then S.autoLeftEnabled=false;if S.autoLeftSetVisual then S.autoLeftSetVisual(false) end;F.stopAutoLeft() end
        if S.autoBatEnabled then S.disableAutoBat();if S.autoBatSetVisual then S.autoBatSetVisual(false) end end
        F.startAutoRight()
    end

    function F.queueAutoBatStart()
        if not F.safeModeTryStart() then if S.autoBatSetVisual then S.autoBatSetVisual(false) end; return end
        if S.autoLeftEnabled then S.autoLeftEnabled=false;if S.autoLeftSetVisual then S.autoLeftSetVisual(false) end;F.stopAutoLeft() end
        if S.autoRightEnabled then S.autoRightEnabled=false;if S.autoRightSetVisual then S.autoRightSetVisual(false) end;F.stopAutoRight() end
        S.enableAutoBat()
    end

    RunService.Heartbeat:Connect(function()
        if not S.autoBatEnabled then return end
        local char=LP.Character
        local hum=char and char:FindFirstChildOfClass("Humanoid")
        local root=char and char:FindFirstChild("HumanoidRootPart")
        if not root or not hum then return end
        if not S.autoBatEquippedThisRun then
            S.autoBatEquippedThisRun=true
            if not char:FindFirstChildOfClass("Tool") then
                local bp=LP:FindFirstChildOfClass("Backpack") or LP:FindFirstChild("Backpack")
                local bpBat=bp and bp:FindFirstChild("Bat")
                if bpBat then pcall(function() hum:EquipTool(bpBat) end) end
            end
        end
        local target=F.getAutoBatTarget()
        if target then
            local targetVel=target.AssemblyLinearVelocity
            local aimTargetPos=target.Position+(targetVel*math.clamp(targetVel.Magnitude/130,0.05,0.15))+Vector3.new(0,S.AUTO_BAT_V_OFF,0)
            hum.AutoRotate=false
            local look=aimTargetPos-root.Position
            local flatLook=Vector3.new(look.X,0,look.Z)
            if look.Magnitude>0.01 and flatLook.Magnitude>0.01 then
                local targetYaw=math.deg(math.atan2(-flatLook.X,-flatLook.Z))
                local yawDelta=(targetYaw-root.Orientation.Y+180)%360-180
                local targetPitch=math.deg(math.atan2(look.Y,flatLook.Magnitude))
                local pitchDelta=(targetPitch-root.Orientation.X+180)%360-180
                local yawRate=math.clamp(math.rad(yawDelta)*S.AUTO_BAT_TURN_SPEED,-S.AUTO_BAT_MAX_TURN_RATE,S.AUTO_BAT_MAX_TURN_RATE)
                local pitchRate=math.clamp(math.rad(pitchDelta)*S.AUTO_BAT_TURN_SPEED,-S.AUTO_BAT_MAX_TURN_RATE,S.AUTO_BAT_MAX_TURN_RATE)
                local yawRad=math.rad(root.Orientation.Y)
                local rightAxis=Vector3.new(math.cos(yawRad),0,-math.sin(yawRad))
                root.AssemblyAngularVelocity=Vector3.new(0,yawRate,0)+(rightAxis*pitchRate)
            else
                root.AssemblyAngularVelocity=Vector3.zero
            end
            local dir = look.Magnitude > 0.01 and look.Unit or Vector3.zero
            and look.Unit or Vector3.zero
            local standPos=aimTargetPos-(dir*S.AUTO_BAT_DIST)+Vector3.new(0,S.AUTO_BAT_HEIGHT,0)
            local moveDir=standPos-root.Position
            local hDir=Vector3.new(moveDir.X,0,moveDir.Z)
            local hVel=hDir.Magnitude>0.1 and hDir.Unit*S.AUTO_BAT_SPEED or Vector3.zero
            local vVel=math.abs(moveDir.Y)>0.1 and Vector3.new(0,math.sign(moveDir.Y)*S.AUTO_BAT_VERT_SPEED,0) or Vector3.new(0,-2,0)
            local proxy = F.ensureProxy()
            if proxy then
                proxy.AssemblyLinearVelocity = hVel + vVel
            end
            if hDir.Magnitude>0.5 then hum:Move(hDir.Unit,false) end
        else
            hum.AutoRotate=true
            root.AssemblyAngularVelocity=Vector3.zero
        end
        if S.autoSwingEnabled then
            local bat=char:FindFirstChild("Bat")
            if bat and bat:IsA("Tool") then
                bat:Activate()
            end
        end
    end)

    LP.CharacterAdded:Connect(function(char)
        F.destroyProxy()
        task.wait(0.5)
        F.setupSpeedIndicator(char)
        if S.medusaCounterEnabled or S.medusaResetEnabled then F.setupMedusa(char) end
        if S.batCounterEnabled then S.startBatCounter() end
        if S.unwalkEnabled then task.wait(0.5);F.startUnwalk() end
        if S.antiRagdollEnabled then
            task.wait(0.3)
            F.startAntiRagdoll()
        end
        if infJumpEnabled then
            task.wait(0.2)
            F.startInfJump()
        end
        if aimbotEnabled then
            task.wait(0.3)
            startAimbot()
        end
        if tpBatEnabled then
            task.wait(0.3)
            startTPBat()
        end
        pcall(function()
            if K7.headlessEnabled then task.wait(0.2); K7.applyHeadless(char, true) end
            if K7.korbloxEnabled then task.wait(0.2); K7.applyKorblox(char, true) end
            if S.medusaResetEnabled or S.medusaCounterEnabled then task.wait(0.2); F.setupMedusa(char) end
            if K7.bodyLockEnabled then task.wait(0.2); K7.startBodyLock() end
            if K7.animPackEnabled then task.wait(0.3); K7.applyAnimPack(K7.animPackName) end
            if K7.lineESPEnabled or K7.speedESPEnabled then task.wait(0.5); K7.refreshESP() end
            if S.espHitboxEnabled then task.wait(0.5); F.applyEspHitbox() end
        end)
    end)

    if LP.Character then F.setupSpeedIndicator(LP.Character) end

    _G.AceNoPlayerCollisionState = _G.AceNoPlayerCollisionState or {connections = {}, running = false}
    function F.setOtherPlayerCollision(state)
        for _, plr in ipairs(Players:GetPlayers()) do
            if plr ~= LP and plr.Character then
                for _, part in ipairs(plr.Character:GetDescendants()) do
                    if part:IsA("BasePart") then
                        pcall(function() part.CanCollide = state end)
                    end
                end
            end
        end
    end
    function F.enableNoPlayerCollision()
        local st = _G.AceNoPlayerCollisionState
        if st.running then return end
        S.noPlayerCollisionEnabled = true
        st.running = true
        for _, conn in ipairs(st.connections or {}) do
            pcall(function() conn:Disconnect() end)
        end
        st.connections = {}
        F.setOtherPlayerCollision(false)
        table.insert(st.connections, LP.CharacterAdded:Connect(function()
            task.wait(0.5)
            if S.noPlayerCollisionEnabled then F.setOtherPlayerCollision(false) end
        end))
        table.insert(st.connections, Players.PlayerAdded:Connect(function(plr)
            local c = plr.CharacterAdded:Connect(function()
                task.wait(0.5)
                if S.noPlayerCollisionEnabled then F.setOtherPlayerCollision(false) end
            end)
            table.insert(st.connections, c)
        end))
        local collisionScanElapsed = 0
        table.insert(st.connections, RunService.Heartbeat:Connect(function(dt)
            if not S.noPlayerCollisionEnabled then return end
            collisionScanElapsed = collisionScanElapsed + (dt or 0)
            if collisionScanElapsed < 0.25 then return end
            collisionScanElapsed = 0
            for _, plr in ipairs(Players:GetPlayers()) do
                if plr ~= LP and plr.Character then
                    for _, part in ipairs(plr.Character:GetDescendants()) do
                        if part:IsA("BasePart") and part.CanCollide == true then
                            pcall(function() part.CanCollide = false end)
                        end
                    end
                end
            end
        end))
    end
    function F.disableNoPlayerCollision()
        local st = _G.AceNoPlayerCollisionState
        S.noPlayerCollisionEnabled = false
        if not st.running then return end
        st.running = false
        for _, conn in ipairs(st.connections or {}) do
            pcall(function() conn:Disconnect() end)
        end
        st.connections = {}
        F.setOtherPlayerCollision(true)
    end

    function F.safeModeGetCountdownLabel()
        local ok, label = pcall(function()
            local pg = LP:FindFirstChild("PlayerGui")
            if not pg then return nil end
            local a = pg:FindFirstChild("DuelsMachineTopFrame")
            local b = a and a:FindFirstChild("DuelsMachineTopFrame")
            local t = b and b:FindFirstChild("Timer")
            return t and t:FindFirstChild("Label")
        end)
        return (ok and label) or nil
    end
    function F.safeModeCountdownNumber(text)
        local t = tostring(text or ""):upper():gsub("^%s+", ""):gsub("%s+$", "")
        if t == "GO" or t == "START" or t == "READY" then return true end
        local n = tonumber(t)
        return n ~= nil and n >= 0 and n <= 10
    end
    function F.safeModeInDuelCountdown()
        local label = F.safeModeGetCountdownLabel()
        return label and F.safeModeCountdownNumber(label.Text) or false
    end
    function F.safeModeHoldingBrainrot()
        local ok, val = pcall(function() return LP:GetAttribute("Stealing") end)
        if ok and val == true then return true end
        local char = LP.Character
        if not char then return false end
        local ok3, val3 = pcall(function() return char:GetAttribute("Stealing") end)
        if ok3 and val3 == true then return true end
        if F.isCarryingBrainrot and F.isCarryingBrainrot(char) then return true end
        return false
    end
    function F.safeModeIsLocked()
        if not S.safeModeEnabled then return false end
        return F.safeModeInDuelCountdown() or F.safeModeHoldingBrainrot()
    end
    function F.safeModeForceStop(reason)
        local stopped = false
        if tpBatEnabled and stopTPBat then
            pcall(function() stopTPBat() end)
            if VS.setTPBatVisual then pcall(VS.setTPBatVisual, false) end
            stopped = true
        end
        if aimbotEnabled and stopAimbot then
            pcall(function() stopAimbot() end)
            stopped = true
        end
        if S.autoBatEnabled then
            S.autoBatEnabled = false
            if S.disableAutoBat then pcall(S.disableAutoBat) end
            if S.autoBatSetVisual then pcall(S.autoBatSetVisual, false) end
            stopped = true
        end
        if S.autoLeftEnabled then
            S.autoLeftEnabled = false
            if F.stopAutoLeft then F.stopAutoLeft() end
            if S.autoLeftSetVisual then S.autoLeftSetVisual(false) end
            stopped = true
        end
        if S.autoRightEnabled then
            S.autoRightEnabled = false
            if F.stopAutoRight then F.stopAutoRight() end
            if S.autoRightSetVisual then S.autoRightSetVisual(false) end
            stopped = true
        end
        if stopped then
            --print("[073 Hub] Safe Mode:", reason or "SAFE MODE LOCK")
        end
    end
    function F.safeModeTryStart()
        if F.safeModeIsLocked() then
            F.safeModeForceStop("SAFE MODE LOCK")
            return false
        end
        return true
    end
    if not _G.__Hub073SafeModeMonitor then
        _G.__Hub073SafeModeMonitor = true
        RunService.Heartbeat:Connect(function()
            if S.safeModeEnabled and F.safeModeIsLocked() then
                F.safeModeForceStop("SAFE MODE LOCK")
            end
        end)
    end

    function F.setupDeathReset() end

    _G.AceAutoResetOnMed = _G.AceAutoResetOnMed or {
        conns = {}, enabled = false, medTriggered = false, lastFire = 0, cooldown = 2.25
    }
    function F.autoResetShouldFire(part)
        local state = _G.AceAutoResetOnMed
        if not state or not state.enabled then return false end
        if state.medTriggered then return false end
        if tick() - (state.lastFire or 0) < (state.cooldown or 2.25) then return false end
        if not part or not part.Parent then return false end
        if part:FindFirstAncestorOfClass("Tool") or part:FindFirstAncestorOfClass("Accessory") then return false end
        return part.Anchored == true and part.Transparency == 1
    end
    function F.autoResetFireOnce(part)
        if not F.autoResetShouldFire(part) then return end
        local state = _G.AceAutoResetOnMed
        state.medTriggered = true
        state.lastFire = tick()
        task.delay(2.3, function()
            if state.enabled and cursedInstaReset then pcall(cursedInstaReset) end
        end)
    end
    function F.stopAutoResetOnMed()
        local state = _G.AceAutoResetOnMed
        if not state then return end
        for _, conn in ipairs(state.conns or {}) do pcall(function() conn:Disconnect() end) end
        state.conns = {}
        state.medTriggered = false
    end
    function F.startAutoResetOnMed(char)
        local state = _G.AceAutoResetOnMed
        if not state then return end
        F.stopAutoResetOnMed()
        state.medTriggered = false
        char = char or LP.Character
        if not char then return end
        for _, part in ipairs(char:GetDescendants()) do
            if part:IsA("BasePart") then
                table.insert(state.conns, part:GetPropertyChangedSignal("Anchored"):Connect(function()
                    F.autoResetFireOnce(part)
                end))
                F.autoResetFireOnce(part)
            end
        end
        table.insert(state.conns, char.DescendantAdded:Connect(function(part)
            if part:IsA("BasePart") then
                table.insert(state.conns, part:GetPropertyChangedSignal("Anchored"):Connect(function()
                    F.autoResetFireOnce(part)
                end))
                F.autoResetFireOnce(part)
            end
        end))
        table.insert(state.conns, char.AncestryChanged:Connect(function(_, parent)
            if not parent then state.medTriggered = false end
        end))
    end
    function F.setAutoResetOnMed(on)
        S.autoResetOnMedEnabled = on and true or false
        _G.AceAutoResetOnMed.enabled = S.autoResetOnMedEnabled
        if S.autoResetOnMedEnabled then
            F.startAutoResetOnMed(LP.Character)
        else
            F.stopAutoResetOnMed()
        end
    end
    if not _G.__Hub073MedFlingCharConn then
        _G.__Hub073MedFlingCharConn = true
        LP.CharacterAdded:Connect(function(char)
            if S.autoResetOnMedEnabled then
                task.wait(0.25)
                F.startAutoResetOnMed(char)
            end
        end)
    end

    S._tracerFolder = nil
    S._tracerConn = nil
    function F.clearTracers()
        if S._tracerFolder then pcall(function() S._tracerFolder:Destroy() end) end
        S._tracerFolder = nil
    end
    function F.refreshTracers()
        if not S.showTracerEnabled then F.clearTracers(); return end
        local myRoot = LP.Character and LP.Character:FindFirstChild("HumanoidRootPart")
        if not myRoot then return end
        local folder = S._tracerFolder
        if not folder or not folder.Parent then
            folder = Instance.new("Folder")
            folder.Name = "Hub073Tracers"
            folder.Parent = workspace
            S._tracerFolder = folder
        end
        local att0 = myRoot:FindFirstChild("Hub073TracerAtt")
        if not att0 or att0.Parent ~= myRoot then
            if att0 then pcall(function() att0:Destroy() end) end
            att0 = Instance.new("Attachment")
            att0.Name = "Hub073TracerAtt"
            att0.Parent = myRoot
        end
        local alive = {}
        for _, plr in ipairs(Players:GetPlayers()) do
            if plr ~= LP and plr.Character then
                local root = plr.Character:FindFirstChild("HumanoidRootPart")
                local hum = plr.Character:FindFirstChildOfClass("Humanoid")
                if root and hum and hum.Health > 0 then
                    alive[plr.Name] = true
                    local beam = folder:FindFirstChild("TR_" .. plr.Name)
                    local att1 = root:FindFirstChild("Hub073TracerAtt")
                    if not att1 or att1.Parent ~= root then
                        if att1 then pcall(function() att1:Destroy() end) end
                        att1 = Instance.new("Attachment")
                        att1.Name = "Hub073TracerAtt"
                        att1.Parent = root
                    end
                    if not beam then
                        beam = Instance.new("Beam")
                        beam.Name = "TR_" .. plr.Name
                        beam.Width0 = 0.04
                        beam.Width1 = 0.03
                        beam.FaceCamera = true
                        beam.LightEmission = 0.65
                        beam.LightInfluence = 0
                        beam.Segments = 1
                        beam.Color = ColorSequence.new(S.themeColor or Color3.fromRGB(255, 255, 255))
                        beam.Transparency = NumberSequence.new(0.12)
                        beam.Parent = folder
                    end
                    beam.Color = ColorSequence.new(S.themeColor or Color3.fromRGB(255, 255, 255))
                    beam.Attachment0 = att0
                    beam.Attachment1 = att1
                end
            end
        end
        for _, ch in ipairs(folder:GetChildren()) do
            if ch:IsA("Beam") then
                local name = ch.Name:match("^TR_(.+)$")
                if name and not alive[name] then
                    pcall(function() ch:Destroy() end)
                end
            end
        end
    end
    function F.setShowTracer(on)
        S.showTracerEnabled = on and true or false
        if S._tracerConn then pcall(function() S._tracerConn:Disconnect() end); S._tracerConn = nil end
        if S.showTracerEnabled then
            F.refreshTracers()
            local acc = 0
            S._tracerConn = RunService.Heartbeat:Connect(function(dt)
                acc = acc + (dt or 0)
                if acc < 0.2 then return end
                acc = 0
                F.refreshTracers()
                if S._tracerFolder then
                    for _, ch in ipairs(S._tracerFolder:GetDescendants()) do
                        if ch:IsA("Beam") then ch.Enabled = true end
                    end
                end
            end)
        else
            F.clearTracers()
        end
    end

    local ESP_HITBOX_COLORS = {
        Black  = Color3.fromRGB(0, 0, 0),
        Light  = Color3.fromRGB(255, 255, 255),
        Blue   = Color3.fromRGB(60, 140, 255),
        Purple = Color3.fromRGB(170, 80, 255),
        Green  = Color3.fromRGB(40, 255, 120),
    }

    local function getEspHitboxColor()
        return ESP_HITBOX_COLORS[S.espHitboxColor or "Green"] or ESP_HITBOX_COLORS.Green
    end

    local function createEspHitboxForChar(char)
        if not char then return end
        for _, name in ipairs({"Hub073EspHitbox", "Hub073EspHitboxLines", "Hub073EspHitboxHL", "Hub073EspHitboxFolder"}) do
            local oldObj = char:FindFirstChild(name)
            if oldObj then pcall(function() oldObj:Destroy() end) end
        end
        for _, p in ipairs(char:GetDescendants()) do
            if (p:IsA("BoxHandleAdornment") or p:IsA("SelectionBox")) and tostring(p.Name):find("Hub073EspHB") then
                pcall(function() p:Destroy() end)
            end
        end
        if not S.espHitboxEnabled then return end
        local hrp = char:FindFirstChild("HumanoidRootPart")
        if not hrp then return end
        local col = getEspHitboxColor()
        local mode = S.espHitboxMode or "New"
        local folder = Instance.new("Folder")
        folder.Name = "Hub073EspHitboxFolder"
        folder.Parent = char

        local function adornPart(part, sizeScale, thick)
            if not part or not part:IsA("BasePart") then return end
            local box = Instance.new("BoxHandleAdornment")
            box.Name = "Hub073EspHB_" .. part.Name
            box.Adornee = part
            box.AlwaysOnTop = true
            box.ZIndex = 10
            box.Color3 = col
            box.Transparency = 0.55
            box.Size = part.Size * (sizeScale or 1.05)
            box.Parent = folder
            local sel = Instance.new("SelectionBox")
            sel.Name = "Hub073EspHBSel_" .. part.Name
            sel.Adornee = part
            sel.Color3 = col
            sel.LineThickness = thick or 0.04
            sel.Transparency = 0.05
            sel.Parent = folder
        end

        if mode == "Lines" then
            for _, p in ipairs(char:GetChildren()) do
                if p:IsA("BasePart") then
                    local sel = Instance.new("SelectionBox")
                    sel.Name = "Hub073EspHBSel_" .. p.Name
                    sel.Adornee = p
                    sel.Color3 = col
                    sel.LineThickness = 0.035
                    sel.Transparency = 0.08
                    sel.Parent = folder
                end
            end
        elseif mode == "Old" then
            local hl = Instance.new("Highlight")
            hl.Name = "Hub073EspHitboxHL"
            hl.Adornee = char
            hl.DepthMode = Enum.HighlightDepthMode.AlwaysOnTop
            hl.FillColor = col
            hl.FillTransparency = 0.82
            hl.OutlineColor = col
            hl.OutlineTransparency = 0.05
            hl.Parent = folder
            local box = Instance.new("SelectionBox")
            box.Name = "Hub073EspHitbox"
            box.Adornee = char
            box.Color3 = col
            box.LineThickness = 0.14
            box.Transparency = 0
            box.Parent = folder
            adornPart(hrp, 1.15, 0.08)
        else
            -- New
            local hl = Instance.new("Highlight")
            hl.Name = "Hub073EspHitboxHL"
            hl.Adornee = char
            hl.DepthMode = Enum.HighlightDepthMode.AlwaysOnTop
            hl.FillColor = col
            hl.FillTransparency = 0.68
            hl.OutlineColor = col
            hl.OutlineTransparency = 0
            hl.Parent = folder
            for _, name in ipairs({
                "Head", "UpperTorso", "LowerTorso", "Torso", "HumanoidRootPart",
                "Left Arm", "Right Arm", "Left Leg", "Right Leg",
                "LeftUpperArm", "RightUpperArm", "LeftLowerArm", "RightLowerArm",
                "LeftUpperLeg", "RightUpperLeg", "LeftLowerLeg", "RightLowerLeg",
                "LeftHand", "RightHand", "LeftFoot", "RightFoot"
            }) do
                local p = char:FindFirstChild(name)
                if p then adornPart(p, 1.08, 0.03) end
            end
            local box = Instance.new("SelectionBox")
            box.Name = "Hub073EspHitbox"
            box.Adornee = hrp
            box.Color3 = col
            box.LineThickness = 0.07
            box.Transparency = 0
            box.Parent = folder
        end
    end

    function F.applyEspHitbox()
        pcall(function()
            if S._espHitboxCharConns then
                for _, cn in ipairs(S._espHitboxCharConns) do pcall(function() cn:Disconnect() end) end
            end
            S._espHitboxCharConns = {}
            if S._espHitboxConn then
                pcall(function() S._espHitboxConn:Disconnect() end)
                S._espHitboxConn = nil
            end
            for _, plr in ipairs(Players:GetPlayers()) do
                if plr ~= LP and plr.Character then
                    createEspHitboxForChar(plr.Character)
                end
            end
            if S.espHitboxEnabled then
                local function hookPlayer(plr)
                    if plr == LP then return end
                    local cn = plr.CharacterAdded:Connect(function(char)
                        task.wait(0.35)
                        if S.espHitboxEnabled then createEspHitboxForChar(char) end
                        task.wait(1.0)
                        if S.espHitboxEnabled then createEspHitboxForChar(char) end
                    end)
                    table.insert(S._espHitboxCharConns, cn)
                    if plr.Character then createEspHitboxForChar(plr.Character) end
                end
                for _, plr in ipairs(Players:GetPlayers()) do hookPlayer(plr) end
                S._espHitboxConn = Players.PlayerAdded:Connect(function(plr)
                    task.wait(0.2)
                    hookPlayer(plr)
                end)
            end
        end)
    end

    S._ragdollCdConn = nil
    S._ragdollCdCharConn = nil
    S._ragdollCdEnd = 0
    S._ragdollCdLabel = nil
    local RAGDOLL_CD_SECONDS = 2.6
    function F.stopRagdollCountdown()
        if S._ragdollCdConn then S._ragdollCdConn:Disconnect(); S._ragdollCdConn = nil end
        if S._ragdollCdCharConn then S._ragdollCdCharConn:Disconnect(); S._ragdollCdCharConn = nil end
        if S._ragdollCdLabel then
            S._ragdollCdLabel.Visible = false
            S._ragdollCdLabel.Text = ""
        end
        if S._ragdollCdBillboard then
            pcall(function() S._ragdollCdBillboard:Destroy() end)
            S._ragdollCdBillboard = nil
            S._ragdollCdLabel = nil
        end
    end
    function F.ensureRagdollCdLabel(char)
        char = char or LP.Character
        local head = char and (char:FindFirstChild("Head") or char:FindFirstChild("HumanoidRootPart"))
        if not head then return nil end
        pcall(function()
            local pg = LP:FindFirstChild("PlayerGui")
            local old = pg and pg:FindFirstChild("Hub073RagdollCD")
            if old then old:Destroy() end
        end)
        if S._ragdollCdBillboard then
            pcall(function() S._ragdollCdBillboard:Destroy() end)
            S._ragdollCdBillboard = nil
            S._ragdollCdLabel = nil
        end
        local bb = Instance.new("BillboardGui")
        bb.Name = "Hub073RagdollCD"
        bb.Adornee = head
        bb.Size = UDim2.new(0, 90, 0, 40)
        bb.StudsOffset = Vector3.new(0, 5.8, 0)
        bb.AlwaysOnTop = true
        bb.MaxDistance = 200
        bb.Enabled = false
        bb.Parent = head
        S._ragdollCdBillboard = bb
        local bg = Instance.new("Frame")
        bg.Name = "BG"
        bg.Size = UDim2.new(1, 0, 1, 0)
        bg.BackgroundColor3 = Color3.fromRGB(12, 12, 14)
        bg.BackgroundTransparency = 0.15
        bg.BorderSizePixel = 0
        bg.Visible = false
        bg.Parent = bb
        local c = Instance.new("UICorner")
        c.CornerRadius = UDim.new(0, 10)
        c.Parent = bg
        local st = Instance.new("UIStroke")
        st.Color = Color3.fromRGB(180, 180, 185)
        st.Thickness = 1
        st.Transparency = 0.4
        st.Parent = bg
        local lbl = Instance.new("TextLabel")
        lbl.Name = "CD"
        lbl.Size = UDim2.new(1, -6, 1, -4)
        lbl.Position = UDim2.new(0, 3, 0, 2)
        lbl.BackgroundTransparency = 1
        lbl.Text = ""
        lbl.TextColor3 = Color3.fromRGB(245, 245, 247)
        lbl.Font = Enum.Font.GothamBlack
        lbl.TextSize = 18
        lbl.TextStrokeTransparency = 0.35
        lbl.TextStrokeColor3 = Color3.fromRGB(0, 0, 0)
        lbl.Visible = false
        lbl.Parent = bg
        S._ragdollCdLabel = lbl
        return lbl
    end
    function F.hookRagdollCountdown(char)
        F.stopRagdollCountdown()
        if not S.ragdollCountdownEnabled then return end
        char = char or LP.Character
        if not char then return end
        local hum = char:FindFirstChildOfClass("Humanoid") or char:WaitForChild("Humanoid", 4)
        if not hum then return end
        F.ensureRagdollCdLabel(char)
        S._ragdollCdEnd = 0
        local function showCd(on)
            local bb = S._ragdollCdBillboard
            local lbl = S._ragdollCdLabel
            if bb then bb.Enabled = on and true or false end
            if lbl then
                lbl.Visible = on and true or false
                if lbl.Parent then lbl.Parent.Visible = on and true or false end
                if not on then lbl.Text = "" end
            end
        end
        local function beginCountdown()
            S._ragdollCdEnd = tick() + RAGDOLL_CD_SECONDS
            showCd(true)
        end
        local function isRag()
            local st = hum:GetState()
            return hum.PlatformStand
                or st == Enum.HumanoidStateType.Physics
                or st == Enum.HumanoidStateType.Ragdoll
                or st == Enum.HumanoidStateType.FallingDown
        end
        S._ragdollCdCharConn = hum.StateChanged:Connect(function(_, newState)
            if newState == Enum.HumanoidStateType.Physics
                or newState == Enum.HumanoidStateType.Ragdoll
                or newState == Enum.HumanoidStateType.FallingDown then
                beginCountdown()
            end
        end)
        S._ragdollCdConn = RunService.RenderStepped:Connect(function()
            if not S.ragdollCountdownEnabled then F.stopRagdollCountdown(); return end
            local lbl = S._ragdollCdLabel
            if not lbl or not lbl.Parent then
                F.ensureRagdollCdLabel(LP.Character)
                lbl = S._ragdollCdLabel
                if not lbl then return end
                showCd(false)
            end
            if isRag() and S._ragdollCdEnd <= tick() then
                beginCountdown()
            end
            local left = math.max(0, S._ragdollCdEnd - tick())
            if left > 0 then
                showCd(true)
                lbl.Text = string.format("%.1fs", left)
                if left <= 0.8 then
                    lbl.TextColor3 = Color3.fromRGB(255, 255, 255)
                elseif left <= 1.5 then
                    lbl.TextColor3 = Color3.fromRGB(210, 210, 215)
                else
                    lbl.TextColor3 = Color3.fromRGB(180, 180, 185)
                end
            else
                showCd(false)
            end
        end)
        showCd(false)
    end

    function F.setRagdollCountdown(on)
        S.ragdollCountdownEnabled = on and true or false
        if S.ragdollCountdownEnabled then
            F.hookRagdollCountdown(LP.Character)
        else
            F.stopRagdollCountdown()
        end
    end
    if not _G.__Hub073RagdollCdChar then
        _G.__Hub073RagdollCdChar = true
        LP.CharacterAdded:Connect(function(char)
            if S.ragdollCountdownEnabled then
                task.wait(0.3)
                F.hookRagdollCountdown(char)
            end
        end)
    end

    _G.Hub073MobileButtonRefs = _G.Hub073MobileButtonRefs or {}
    _G.Hub073MobileButtonPositions = _G.Hub073MobileButtonPositions or {}
    function F.saveMobileButtonPositions()
        _G.Hub073MobileButtonPositions = _G.Hub073MobileButtonPositions or {}
        for k, entry in pairs(_G.Hub073MobileButtonRefs or {}) do
            local h = entry and entry.holder
            if h and h.Parent then
                local p = h.Position
                _G.Hub073MobileButtonPositions[k] = {
                    xs = p.X.Scale, xo = p.X.Offset,
                    ys = p.Y.Scale, yo = p.Y.Offset,
                }
            end
        end
        pcall(function() if F.saveConfig then F.saveConfig(true) end end)
    end
    function F.applyMobileButtonsHidden()
        local function setEn(g)
            if g then g.Enabled = not (S.hideMobileButtons == true) end
        end
        pcall(function()
            if gethui then setEn(gethui():FindFirstChild("Hub073MobileButtons")) end
        end)
        pcall(function()
            setEn(game:GetService("CoreGui"):FindFirstChild("Hub073MobileButtons"))
        end)
        local pg = LP:FindFirstChild("PlayerGui")
        if pg then setEn(pg:FindFirstChild("Hub073MobileButtons")) end
    end
    function F.applyMobileButtonSize()
        S.mobileButtonScale = math.clamp(tonumber(S.mobileButtonScale) or 0.70, 0.30, 1.35)
        for _, entry in pairs(_G.Hub073MobileButtonRefs) do
            local holder = entry and entry.holder
            if holder then
                local sc = holder:FindFirstChild("MobileButtonScale") or Instance.new("UIScale")
                sc.Name = "MobileButtonScale"
                sc.Scale = S.mobileButtonScale
                sc.Parent = holder
            end
        end
    end
    function F.resetMobileButtons()
        S.mobileButtonScale = 0.80
        S.hideMobileButtons = false
        _G.Hub073MobileButtonPositions = {}
        F.buildMobileButtons()
        F.applyMobileButtonSize()
        F.applyMobileButtonsHidden()
        pcall(function() if F.saveConfig then F.saveConfig() end end)
    end
    function F.buildMobileButtons()
        if _G.__Hub073BuildingMobile then return end
        _G.__Hub073BuildingMobile = true

        if type(_G.__Hub073MobileInputConns) == "table" then
            for _, c in ipairs(_G.__Hub073MobileInputConns) do
                pcall(function() c:Disconnect() end)
            end
        end
        _G.__Hub073MobileInputConns = {}
        local pg = LP:FindFirstChild("PlayerGui") or LP:WaitForChild("PlayerGui", 8)
        if not pg then 
        pcall(function()
            local function opaque(inst)
                if not inst then return end
                for _, d in ipairs(inst:GetDescendants()) do
                    if d:IsA("Frame") or d:IsA("TextButton") or d:IsA("ImageButton") then
                        if d.BackgroundTransparency < 1 and d.BackgroundTransparency > 0 then
                            d.BackgroundTransparency = 0
                        end
                    end
                end
            end
            if gethui then opaque(gethui():FindFirstChild("Hub073MobileButtons")) end
            opaque(game:GetService("CoreGui"):FindFirstChild("Hub073MobileButtons"))
            local pg = LP:FindFirstChild("PlayerGui")
            if pg then opaque(pg:FindFirstChild("Hub073MobileButtons")) end
        end)
        _G.__Hub073BuildingMobile = false; return end

        pcall(function()
            local function wipe(parent)
                if not parent then return end
                for _, ch in ipairs(parent:GetChildren()) do
                    if ch.Name == "Hub073MobileButtons" then pcall(function() ch:Destroy() end) end
                end
            end
            wipe(pg)
            pcall(function() wipe(game:GetService("CoreGui")) end)
            pcall(function() if type(gethui) == "function" then wipe(gethui()) end end)
        end)

        _G.Hub073MobileButtonRefs = {}
        _G.Hub073MobileButtonPositions = _G.Hub073MobileButtonPositions or {}

        local isMobile = false
        pcall(function()

            if UIS.TouchEnabled then isMobile = true end
        end)
        local cam = workspace.CurrentCamera
        local vp = cam and cam.ViewportSize or Vector2.new(1280, 720)
        if vp.X < 900 or vp.Y < 600 then isMobile = true end

        pcall(function()
            if UIS.MouseEnabled and UIS.KeyboardEnabled and not UIS.TouchEnabled then
                isMobile = false
            end
        end)
        S._isMobileLayout = isMobile

        local mobileGui = Instance.new("ScreenGui")
        mobileGui.Name = "Hub073MobileButtons"
        mobileGui.ResetOnSpawn = false
        mobileGui.IgnoreGuiInset = true
        mobileGui.ZIndexBehavior = Enum.ZIndexBehavior.Sibling
        mobileGui.DisplayOrder = 9999
        mobileGui.Enabled = true

        local parented = false
        pcall(function()
            if gethui then mobileGui.Parent = gethui(); parented = mobileGui.Parent ~= nil end
        end)
        if not parented then
            pcall(function()
                mobileGui.Parent = game:GetService("CoreGui"); parented = mobileGui.Parent ~= nil
            end)
        end
        if not parented then
            mobileGui.Parent = pg
        end

        if S.hideMobileButtons ~= true then
            mobileGui.Enabled = true
        else
            mobileGui.Enabled = false
        end

        local mobileButtons = _G.Hub073MobileButtonRefs

        local function udimFromSaved(key, default)
            local store = _G.Hub073MobileButtonPositions or {}
            local s = store[key]
            if type(s) ~= "table" then return default end
            local xs = tonumber(s.xs)
            local ys = tonumber(s.ys)
            if xs == nil or ys == nil then return default end
            local xo = tonumber(s.xo) or 0
            local yo = tonumber(s.yo) or 0

            if isMobile and xs >= 0.95 and xo < 0 then
                return default
            end
            return UDim2.new(xs, xo, ys, yo)
        end
        local function savePos(key, holder)
            if not holder then return end
            local p = holder.Position
            _G.Hub073MobileButtonPositions = _G.Hub073MobileButtonPositions or {}
            _G.Hub073MobileButtonPositions[key] = {
                xs = p.X.Scale, xo = p.X.Offset,
                ys = p.Y.Scale, yo = p.Y.Offset,
            }

            for k, entry in pairs(_G.Hub073MobileButtonRefs or {}) do
                local h = entry and entry.holder
                if h and h.Parent then
                    local hp = h.Position
                    _G.Hub073MobileButtonPositions[k] = {
                        xs = hp.X.Scale, xo = hp.X.Offset,
                        ys = hp.Y.Scale, yo = hp.Y.Offset,
                    }
                end
            end
        end

        local function persistMobilePositions()
            pcall(function()

                if F.saveConfig then F.saveConfig(true) end
            end)
        end
        local function getTheme()
            return S.themeColor or Color3.fromRGB(245, 245, 247)
        end
        local function setActive(btn, state)
            if not btn or not btn.Parent then return end
            local pressed = btn:GetAttribute("Pressed") == true
            state = (state == true) or pressed
            local visual = state and "on" or "off"
            if btn:GetAttribute("VisualState") == visual and not pressed then return end
            btn:SetAttribute("VisualState", visual)
            local theme = getTheme()
            local r,g,b = theme.R, theme.G, theme.B
            if (r+g+b)/3 > 0.85 then
                theme = Color3.fromRGB(200, 200, 210)
            end
            if state then
                btn.BackgroundColor3 = theme
                btn.BackgroundTransparency = 0
                btn.TextColor3 = Color3.fromRGB(12, 12, 14)
                local st = btn:FindFirstChildOfClass("UIStroke")
                if st then st.Color = theme; st.Transparency = 0.05 end
            else
                btn.BackgroundColor3 = theme
                btn.BackgroundTransparency = isMobile and 0.35 or 0.55
                btn.TextColor3 = Color3.fromRGB(245, 245, 250)
                local st = btn:FindFirstChildOfClass("UIStroke")
                if st then st.Color = theme; st.Transparency = 0.25 end
            end
        end

        _G._Hub073ApplyMobileTheme = function(col)
            S.themeColor = col
            for _, entry in pairs(mobileButtons) do
                local btn = entry and entry.btn
                if btn and btn.Parent then
                    local st = btn:GetAttribute("VisualState")
                    btn:SetAttribute("VisualState", nil)
                    setActive(btn, st == "on")
                end
            end
        end

        local function refreshAllMobileStates()
            for key, entry in pairs(mobileButtons) do
                local btn = entry and entry.btn
                if btn then
                local keep = false
                if key == "autoLeft" then keep = S.autoLeftEnabled == true
                elseif key == "autoRight" then keep = S.autoRightEnabled == true
                elseif key == "aimbot" then keep = (tpBatEnabled == true) or (aimbotEnabled == true)
                elseif key == "autoBat" then keep = S.autoBatEnabled == true
                elseif key == "carry" then keep = S.speedMode == true and not S.laggerToggled
                elseif key == "lagger" then keep = S.laggerToggled == true and S.laggerPhase == 1
                elseif key == "laggerCarry" then keep = S.laggerToggled == true and S.laggerPhase == 2
                end
                setActive(btn, keep)
                end
            end
        end
        _G._Hub073RefreshMobileButtons = refreshAllMobileStates

        local function pulse(btn)
            if not btn then return end
            btn:SetAttribute("Pressed", true)
            setActive(btn, true)
            task.delay(0.15, function()
                if btn and btn.Parent then
                    btn:SetAttribute("Pressed", false)
                    refreshAllMobileStates()
                end
            end)
        end

        local btnW = isMobile and 72 or 78
        local btnH = isMobile and 56 or 58

        local function makeButton(key, label, pos, onPress)
            local holder = Instance.new("Frame")
            holder.Name = "MBH_" .. key
            holder.Size = UDim2.new(0, btnW, 0, btnH)
            holder.Position = udimFromSaved(key, pos)
            holder.BackgroundTransparency = 1
            holder.ZIndex = 1000
            holder.Active = true
            holder.Parent = mobileGui

            local btn = Instance.new("TextButton")
            btn.Name = "MB_" .. key
            btn.Size = UDim2.new(1, 0, 1, 0)
            btn.BackgroundColor3 = getTheme()
            btn.BackgroundTransparency = isMobile and 0.35 or 0.55
            btn.BorderSizePixel = 0
            btn.Text = label
            btn.TextColor3 = Color3.fromRGB(245, 245, 250)
            btn.Font = Enum.Font.GothamBold
            btn.TextSize = isMobile and 11 or 10
            btn.TextWrapped = true
            btn.AutoButtonColor = false
            btn.ZIndex = 1002
            btn.Active = true
            btn.Selectable = true
            btn:SetAttribute("ThemeMobile", true)
            btn.Parent = holder
            Instance.new("UICorner", btn).CornerRadius = UDim.new(0, isMobile and 12 or 10)
            local stroke = Instance.new("UIStroke", btn)
            stroke.Color = getTheme()
            stroke.Thickness = isMobile and 1.6 or 1.2
            stroke.Transparency = 0.25

            local pressing, dragging = false, false
            local pressPos, holderStart = nil, nil
            local fired = false

            local function fireAction()
                if fired then return end
                fired = true
                pcall(function()
                    if onPress then onPress(btn) end
                end)
                task.defer(refreshAllMobileStates)
                task.delay(0.25, function() fired = false end)
            end

            local function beginPress(input)
                pressing = true
                dragging = false
                fired = false
                pressPos = input.Position
                holderStart = holder.Position
                btn:SetAttribute("Pressed", true)
                setActive(btn, true)
            end

            local function endPress()
                if not pressing then return end
                if pressing and not dragging then
                    fireAction()
                end
                if dragging then
                    savePos(key, holder)
                    persistMobilePositions()
                end
                pressing = false
                dragging = false
                btn:SetAttribute("Pressed", false)
                task.delay(0.05, refreshAllMobileStates)
            end

            btn.InputBegan:Connect(function(input)
                if input.UserInputType == Enum.UserInputType.MouseButton1 or input.UserInputType == Enum.UserInputType.Touch then
                    beginPress(input)
                end
            end)

            local function track(conn)
                if conn then
                    _G.__Hub073MobileInputConns = _G.__Hub073MobileInputConns or {}
                    table.insert(_G.__Hub073MobileInputConns, conn)
                end
                return conn
            end

            track(UIS.InputEnded:Connect(function(input)
                if not pressing then return end
                if input.UserInputType == Enum.UserInputType.MouseButton1 or input.UserInputType == Enum.UserInputType.Touch then
                    endPress()
                end
            end))

            track(UIS.InputChanged:Connect(function(input)
                if not pressing then return end
                if S.guiLocked or S.lockMobileButtons then return end
                if input.UserInputType ~= Enum.UserInputType.MouseMovement and input.UserInputType ~= Enum.UserInputType.Touch then return end
                if not pressPos then return end
                local delta = input.Position - pressPos
                local thresh = isMobile and 18 or 8
                if not dragging and (math.abs(delta.X) > thresh or math.abs(delta.Y) > thresh) then
                    dragging = true
                end
                if dragging and holderStart then
                    holder.Position = UDim2.new(
                        holderStart.X.Scale, holderStart.X.Offset + delta.X,
                        holderStart.Y.Scale, holderStart.Y.Offset + delta.Y
                    )
                end
            end))

            btn.Activated:Connect(function()
                if dragging then return end
                if pressing then
                    endPress()
                else
                    fireAction()
                end
            end)

            mobileButtons[key] = {holder = holder, btn = btn, setActive = function(state) setActive(btn, state) end}
            setActive(btn, false)
            return btn
        end

        local defaults
        if isMobile then
            defaults = {
                insta       = UDim2.new(0.02, 0, 0.38, 0),
                drop        = UDim2.new(0.02, 0, 0.48, 0),
                autoLeft    = UDim2.new(0.02, 0, 0.58, 0),
                autoBat     = UDim2.new(0.18, 0, 0.38, 0),
                aimbot      = UDim2.new(0.18, 0, 0.48, 0),
                autoRight   = UDim2.new(0.18, 0, 0.58, 0),
                tp          = UDim2.new(0.02, 0, 0.68, 0),
                carry       = UDim2.new(0.18, 0, 0.68, 0),
                lagger      = UDim2.new(0.02, 0, 0.78, 0),
                laggerCarry = UDim2.new(0.18, 0, 0.78, 0),
            }
        else
            local x1, x2, x3 = -218, -154, -90
            local y1, y2, y3, y4 = -150, -102, -54, -6
            defaults = {
                insta = UDim2.new(1, x1, 0.5, y1),
                drop = UDim2.new(1, x2, 0.5, y1),
                autoLeft = UDim2.new(1, x3, 0.5, y1),
                autoBat = UDim2.new(1, x1, 0.5, y2),
                aimbot = UDim2.new(1, x2, 0.5, y2),
                autoRight = UDim2.new(1, x3, 0.5, y2),
                tp = UDim2.new(1, x2, 0.5, y3),
                carry = UDim2.new(1, x3, 0.5, y3),
                lagger = UDim2.new(1, x2, 0.5, y4),
                laggerCarry = UDim2.new(1, x3, 0.5, y4),
            }
        end

        makeButton("insta", "INSTA\nRESET", defaults.insta, function(btn)
            if cursedInstaReset then cursedInstaReset() end
            pulse(btn)
        end)
        makeButton("drop", "DROP\nBR", defaults.drop, function(btn)
            if F.runDrop then F.runDrop() end
            pulse(btn)
        end)
        makeButton("autoLeft", "AUTO\nLEFT", defaults.autoLeft, function()
            if S.autoLeftEnabled then
                S.autoLeftEnabled = false
                if F.stopAutoLeft then F.stopAutoLeft() end
                if S.autoLeftSetVisual then S.autoLeftSetVisual(false) end
            else
                if F.queueAutoLeftStart then F.queueAutoLeftStart() end
                if S.autoLeftSetVisual then S.autoLeftSetVisual(S.autoLeftEnabled) end
            end
        end)
        makeButton("autoBat", "AUTO\nBAT", defaults.autoBat, function()
            if S.autoBatEnabled then
                S.autoBatEnabled = false
                if S.disableAutoBat then S.disableAutoBat() end
                if S.autoBatSetVisual then S.autoBatSetVisual(false) end
            else
                if F.queueAutoBatStart then F.queueAutoBatStart() end
                if S.autoBatSetVisual then S.autoBatSetVisual(S.autoBatEnabled) end
            end
        end)
        makeButton("aimbot", "TP\nBAT", defaults.aimbot, function()
            if F.safeModeTryStart and not F.safeModeTryStart() then return end
            if setTPBatState then setTPBatState(not tpBatEnabled) end
            if VS.setTPBatVisual then VS.setTPBatVisual(tpBatEnabled) end
        end)
        makeButton("autoRight", "AUTO\nRIGHT", defaults.autoRight, function()
            if S.autoRightEnabled then
                S.autoRightEnabled = false
                if F.stopAutoRight then F.stopAutoRight() end
                if S.autoRightSetVisual then S.autoRightSetVisual(false) end
            else
                if F.queueAutoRightStart then F.queueAutoRightStart() end
                if S.autoRightSetVisual then S.autoRightSetVisual(S.autoRightEnabled) end
            end
        end)
        makeButton("tp", "TP\nDOWN", defaults.tp, function(btn)
            if F.runTPFloor then F.runTPFloor() end
            pulse(btn)
        end)
        makeButton("carry", "CARRY\nSPEED", defaults.carry, function()
            if F.toggleCarryMode then F.toggleCarryMode() end
            if F.refreshSpeedModeLabel then F.refreshSpeedModeLabel() end
        end)
        makeButton("lagger", "LAGGER\nNORMAL", defaults.lagger, function()
            if S.laggerToggled and S.laggerPhase == 1 then
                S.laggerToggled = false; S.laggerPhase = 0
            else
                S.speedMode = false; S.laggerToggled = true; S.laggerPhase = 1
            end
            if F.refreshSpeedModeLabel then F.refreshSpeedModeLabel() end
        end)
        makeButton("laggerCarry", "LAGGER\nCARRY", defaults.laggerCarry, function()
            if S.laggerToggled and S.laggerPhase == 2 then
                S.laggerToggled = false; S.laggerPhase = 0
            else
                S.speedMode = false; S.laggerToggled = true; S.laggerPhase = 2
            end
            if F.refreshSpeedModeLabel then F.refreshSpeedModeLabel() end
        end)

        if isMobile and (not S.mobileButtonScale or S.mobileButtonScale == 0.80) then
            S.mobileButtonScale = 1.05
        end
        F.applyMobileButtonSize()
        F.applyMobileButtonsHidden()
        task.defer(function()
            refreshAllMobileStates()
            if S.themeColor and _G._Hub073ApplyMobileTheme then
                pcall(function() _G._Hub073ApplyMobileTheme(S.themeColor) end)
            end
        end)
        _G.__Hub073BuildingMobile = false
    end

    function F.saveConfig(force)
        if not _writefile then
            S._lastSaveOk = false
            if UI and UI.saveStatusLbl then
                UI.saveStatusLbl.Text = "Save: no writefile"
                UI.saveStatusLbl.TextColor3 = Color3.fromRGB(255, 120, 120)
            end
            return false
        end
        local ok, err = pcall(function()
            local function ks(e)
                if type(e) ~= "table" then return {kb=nil,gp=nil} end
                return {kb = e.kb and e.kb.Name or nil, gp = e.gp and e.gp.Name or nil}
            end
            local function numFromBox(box, fallback)
                if box and box.Text then
                    local n = tonumber(box.Text)
                    if n then return n end
                end
                return fallback
            end
            if UI then
                local v
                v = numFromBox(UI.radInput, nil)
                if v and v > 0 then autoStealRadius = v; Steal.StealRadius = v end
                v = numFromBox(UI.stealDurInput, nil)
                if v and v >= 0.1 then Steal.StealDuration = v end
                v = numFromBox(UI.stopTimeInput, nil)
                if v and v > 0 then autoGrabStopTime = v; Steal.AutoGrabStopTime = v end
                v = numFromBox(UI.delayRadInput, nil)
                if v and v > 0 then autoGrabDelayRadius = v; Steal.AutoGrabDelayRadius = v end
                v = numFromBox(UI.normalBox, nil)
                if v and v > 0 then NS = v end
                v = numFromBox(UI.carryBox, nil)
                if v and v > 0 then CS = v end
                v = numFromBox(UI.laggerBox, nil)
                if v and v > 0 then S.LAGGER_SPEED = v end
                v = numFromBox(UI.laggerCarryBox, nil)
                if v and v > 0 then S.LAGGER_CARRY_SPEED = v end
                v = numFromBox(UI.autoTPHeightBox, nil)
                if v then S.autoTPHeight = v end
            end
            local stealRad = tonumber(autoStealRadius) or (Steal and Steal.StealRadius) or 20
            local stealDur = (Steal and tonumber(Steal.StealDuration)) or 1.3
            local stopT = tonumber(autoGrabStopTime) or (Steal and tonumber(Steal.AutoGrabStopTime)) or 1.00
            local delayR = tonumber(autoGrabDelayRadius) or (Steal and tonumber(Steal.AutoGrabDelayRadius)) or 8
            autoGrabStopTime = stopT
            autoGrabDelayRadius = delayR
            if Steal then
                Steal.StealRadius = stealRad
                Steal.StealDuration = stealDur
                Steal.AutoGrabStopTime = stopT
                Steal.AutoGrabDelayRadius = delayR
                Steal.AutoGrabStopEnabled = autoGrabStopEnabled == true
            end
            local cfg = {
                normalSpeed = NS, carrySpeed = CS,
                dropBrainrotKey = ks(KB.DropBrainrot), autoLeftKey = ks(KB.AutoLeft), autoRightKey = ks(KB.AutoRight),
                autoBatKey = ks(KB.AutoBat), tpBatKey = ks(KB.TPBat), bodyLockKey = ks(KB.BodyLock), laggerToggleKey = ks(KB.LaggerToggle),
                tpFloorKey = ks(KB.TPFloor), instaResetKey = ks(KB.InstaReset), guiHideKey = ks(KB.GuiHide),
                speedToggleKey = ks(KB.SpeedToggle),
                grabRadius = stealRad, stealDuration = stealDur,
                selectedStealMode = selectedStealMode,
                aceStealRadii = AceStealRadii and {
                    Normal = AceStealRadii.Normal, Semi = AceStealRadii.Semi
                } or nil,
                autoGrabStopEnabled = autoGrabStopEnabled == true,
                autoGrabStopTime = stopT,
                autoGrabDelayRadius = delayR,
                autoGrabUnpauseRadius = tonumber(autoGrabUnpauseRadius) or 12,
                showRadiusMode = showRadiusMode or "Off",
                asGuiCustom = S.asGuiCustom,
                antiRagdollMode = S.antiRagdollMode or 1,
                aimbotMode = S.aimbotMode or 1,
                antiRagdoll = S.antiRagdollEnabled, antiDieEnabled = S.antiDieEnabled, antiFlingShieldEnabled = S.antiFlingShieldEnabled, removeAccessories = S.removeAccessoriesEnabled, autoStealEnabled = autoStealEnabled,
                medusaCounter = S.medusaCounterEnabled, medusaReset = S.medusaResetEnabled, batCounter = S.batCounterEnabled,
                infJump = infJumpEnabled, aimbot = aimbotEnabled, tpBat = tpBatEnabled,
                antiBatBypass = S.antiBatBypassEnabled == true or antiBatBypassLockEnabled == true,
                aimbotSpeed = AIMBOT_SPEED or 63,
                autoLeft = S.autoLeftEnabled == true,
                autoRight = S.autoRightEnabled == true,
                tpBatMode = tpBatMode,
                tpBatLegacyKey = (TP_BAT_KEY and TP_BAT_KEY.Name) or "V",
                carryMode = S.speedMode, laggerMode = S.laggerToggled, laggerCarryMode = S.laggerPhase == 2,
                autoCarrySpeedEnabled = S.autoCarrySpeedEnabled,
                noPlayerCollisionEnabled = S.noPlayerCollisionEnabled,
                safeModeEnabled = S.safeModeEnabled,
                autoResetOnMedEnabled = S.autoResetOnMedEnabled,
                laggerSpeed = S.LAGGER_SPEED, laggerCarrySpeed = S.LAGGER_CARRY_SPEED,
                autoBat = S.autoBatEnabled, autoSwing = S.autoSwingEnabled,
                unwalkEnabled = S.unwalkEnabled,
                antiLag = S.antiLagEnabled, keyboardOverlay = S.keyboardOverlayEnabled == true, stretchRez = S.stretchRezEnabled, stretchRezAmount = S.stretchRezAmount,
                antiDrop = S.antiDropEnabled == true,
                autoTPEnabled = S.autoTPEnabled, autoTPHeight = S.autoTPHeight,
                skyTheme = K7 and K7.currentSkyTheme or nil,
                noSkyEnabled = S.noSkyEnabled == true,
                fovEnabled = K7 and K7.fovEnabled or false,
                fovValue = (K7 and K7.fovValue) or (_G._Hub073FOV) or 70,
                headless = K7 and K7.headlessEnabled or false,
                korbloxSide = korbloxSide or (K7 and K7.korbloxSide) or 1,
                korblox = K7 and K7.korbloxEnabled or false,
                bodyLock = K7 and K7.bodyLockEnabled or false,
                bodyLockRadius = K7 and K7.bodyLockRadius or 60,
                lineESP = K7 and K7.lineESPEnabled or false,
                speedESP = K7 and K7.speedESPEnabled or false,
                animPackEnabled = K7 and K7.animPackEnabled or false,
                animPack = K7 and K7.animPackName or nil,
                animPackIndex = K7 and K7.animPackIndex or 1,
                guiScale = (_G._Hub073GetGuiScale and _G._Hub073GetGuiScale()) or 1,
                themeName = S.currentThemeName or currentThemeName or "WHITE",
                bgId = S.currentBgId or "",
                bgIntensity = S.bgIntensity or 0.58,
                autoSaveEnabled = S.autoSaveEnabled,
                showTracerEnabled = S.showTracerEnabled,
                ragdollCountdownEnabled = S.ragdollCountdownEnabled,
                guiLocked = S.guiLocked,
                hideMobileButtons = S.hideMobileButtons,
                lockMobileButtons = S.lockMobileButtons == true,
                keyboardOverlay = S.keyboardOverlayEnabled == true,
                keyboardOverlayPosX = tonumber(S.keyboardOverlayPosX) or 0.025,
                keyboardOverlayPosY = tonumber(S.keyboardOverlayPosY) or 0.62,
                autoStealGuiStyle = (S.autoStealGuiStyle == "New") and "New" or ((S.autoStealGuiStyle == "Personalizada") and "Personalizada" or "Old"),
                uiLayoutStyle = (S.uiLayoutStyle == "Old") and "Old" or "New",
                mobileButtonScale = S.mobileButtonScale,
                infJumpMode = S.infJumpMode or "hold",
                mirrorTPDown = S.mirrorTPDownEnabled == true,
                mobileButtonPositions = _G.Hub073MobileButtonPositions,
                tpBatDistance = S.tpBatDistance,
                spinOnTPBatEnabled = S.spinOnTPBatEnabled,
                spinOnTPBatSpeed = S.spinOnTPBatSpeed,
                flingOnTPBatEnabled = S.flingOnTPBatEnabled,
                antiAntiAntiDesyncEnabled = S.antiAntiAntiDesyncEnabled,
                espHitboxEnabled = S.espHitboxEnabled,
                espHitboxColor = S.espHitboxColor or "Green",
                espHitboxMode = S.espHitboxMode or "New",
                kbOverlayColorName = S.kbOverlayColorName,
            }
            local encoded = HS:JSONEncode(cfg)
            _writefile(CONFIG_FILE, encoded)
            pcall(function() _writefile(CONFIG_FILE_LEGACY, encoded) end)
        end)
        S._lastSaveOk = ok and true or false
        S._lastSaveAt = tick()
        if UI and UI.saveStatusLbl then
            if ok then
                UI.saveStatusLbl.Text = "Saved OK"
                UI.saveStatusLbl.TextColor3 = Color3.fromRGB(100, 220, 160)
            else
                UI.saveStatusLbl.Text = "Save failed"
                UI.saveStatusLbl.TextColor3 = Color3.fromRGB(255, 120, 120)
                --warn("[073 Hub] F.saveConfig error:", err)
            end
        end
        return ok
    end

    task.spawn(function()
        while true do
            task.wait(5)
            if S.autoSaveEnabled ~= false then
                pcall(function() F.saveConfig(true) end)
            end
        end
    end)

    local CONFIG_EXPORT_FILE = "Hub073_Export.json"
    local CONFIG_IMPORT_FILE = "Hub073_Import.json"
    local _setclipboard = setclipboard or toclipboard or (syn and syn.write_clipboard)
    local _getclipboard = getclipboard or (syn and syn.read_clipboard)

    function F.saveMobileButtonsOnly()
        if not _writefile then return false end
        local ok = pcall(function()
            local raw = nil
            if _isfile(CONFIG_FILE) then
                pcall(function() raw = _readfile(CONFIG_FILE) end)
            end
            local cfg = {}
            if raw and raw ~= "" then
                pcall(function() cfg = HS:JSONDecode(raw) or {} end)
            end
            if type(cfg) ~= "table" then cfg = {} end
            cfg.mobileButtonPositions = _G.Hub073MobileButtonPositions or cfg.mobileButtonPositions
            cfg.mobileButtonScale = S.mobileButtonScale
            cfg.hideMobileButtons = S.hideMobileButtons == true
            cfg.lockMobileButtons = S.lockMobileButtons == true
            _writefile(CONFIG_FILE, HS:JSONEncode(cfg))
        end)
        return ok == true
    end

    function F.exportConfig()

        local okSnap = pcall(function()
            if F.saveConfig then F.saveConfig(true) end
        end)
        local raw = nil
        if _isfile(CONFIG_FILE) then
            pcall(function() raw = _readfile(CONFIG_FILE) end)
        end
        if not raw or raw == "" then
            return false, "no_config"
        end
        pcall(function() _writefile(CONFIG_EXPORT_FILE, raw) end)
        local copied = false
        if _setclipboard then copied = pcall(_setclipboard, raw) end
        return true, copied and "copied_and_exported" or "exported_to_file"
    end

    function F.importConfig(pastedText)
        local raw = tostring(pastedText or ""):match("^%s*(.-)%s*$") or ""
        if raw == "" and _getclipboard then
            local clipOk, clip = pcall(_getclipboard)
            if clipOk and type(clip) == "string" then raw = clip:match("^%s*(.-)%s*$") or "" end
        end
        if raw == "" and _isfile(CONFIG_IMPORT_FILE) then
            pcall(function() raw = tostring(_readfile(CONFIG_IMPORT_FILE) or ""):match("^%s*(.-)%s*$") or "" end)
        end
        if raw == "" then return false, "empty" end
        local valid, decoded = pcall(function() return HS:JSONDecode(raw) end)
        if not valid or type(decoded) ~= "table" then return false, "invalid_json" end
        local encodedOk, normalized = pcall(function() return HS:JSONEncode(decoded) end)
        if not encodedOk then return false, "encode_failed" end
        pcall(function() _writefile(CONFIG_FILE, normalized) end)
        _savedCfg = decoded
        pcall(function()
            if F.loadConfigKeys then F.loadConfigKeys() end
            if F.loadConfigState then F.loadConfigState() end
        end)
        return true, "imported"
    end

    function F.resetAllConfig()
        pcall(function() if S.antiRagdollEnabled then S.antiRagdollEnabled=false; F.stopAntiRagdoll() end end)
        pcall(function() if infJumpEnabled then infJumpEnabled=false; F.stopInfJump() end end)
        pcall(function() if tpBatEnabled and setTPBatState then setTPBatState(false) end end)
        pcall(function() if aimbotEnabled and setAimbotState then setAimbotState(false) end end)
        pcall(function() if S.autoBatEnabled and S.disableAutoBat then S.autoBatEnabled=false; S.disableAutoBat() end end)
        pcall(function() if S.laggerToggled then S.laggerToggled=false; S.laggerPhase=0 end end)
        pcall(function() if S.speedMode then S.speedMode=false end end)
        pcall(function() if S.autoTPEnabled and F.stopAutoTP then F.stopAutoTP() end end)
        pcall(function() if S.unwalkEnabled and F.stopUnwalk then S.unwalkEnabled=false; F.stopUnwalk() end end)
        pcall(function() if S.antiLagEnabled and F.disableAntiLag then F.disableAntiLag() end end)
        pcall(function() if F.stopKeyboardOverlay then F.stopKeyboardOverlay() end end)
        pcall(function() if S.stretchRezEnabled and disableStretchRez then disableStretchRez() end end)
        pcall(function() if S.showTracerEnabled and F.setShowTracer then F.setShowTracer(false) end end)
        pcall(function() if S.ragdollCountdownEnabled and F.setRagdollCountdown then F.setRagdollCountdown(false) end end)
        pcall(function() if S.noPlayerCollisionEnabled and F.disableNoPlayerCollision then F.disableNoPlayerCollision() end end)
        pcall(function() if autoStealEnabled and stopAutoSteal then stopAutoSteal() end end)
        pcall(function() if S.batCounterEnabled and S.stopBatCounter then S.batCounterEnabled=false; S.stopBatCounter() end end)
        pcall(function() if S.medusaCounterEnabled or S.medusaResetEnabled then S.medusaCounterEnabled=false; S.medusaResetEnabled=false end end)
        pcall(function() if S.autoResetOnMedEnabled and F.setAutoResetOnMed then F.setAutoResetOnMed(false) end end)
        pcall(function() if S.espHitboxEnabled then S.espHitboxEnabled=false; F.applyEspHitbox() end end)

        NS, CS = 60, 30
        S.NS, S.CS = 60, 30
        S.LAGGER_SPEED, S.LAGGER_CARRY_SPEED = 15, 24.5
        S.autoTPHeight = 20
        S.autoCarrySpeedEnabled = false
        S.safeModeEnabled = false
        S.autoSwingEnabled = true
        S.guiLocked = false
        S.lockMobileButtons = false
        S.hideMobileButtons = false
        S.mobileButtonScale = 0.80
        S.autoSaveEnabled = true
        S.autoLeftEnabled = false
        S.autoRightEnabled = false
        S.dropActive = false
        S.noPlayerCollisionEnabled = false
        S.showTracerEnabled = false
        S.ragdollCountdownEnabled = false
        S.autoResetOnMedEnabled = false
        S.medusaCounterEnabled = false
        S.medusaResetEnabled = false
        S.batCounterEnabled = false
        S.unwalkEnabled = false
        S.antiLagEnabled = false
        S.stretchRezEnabled = false
        S.antiRagdollEnabled = false
        S.autoTPEnabled = false
        S.autoBatEnabled = false
        S.speedMode = false
        S.laggerToggled = false
        S.laggerPhase = 0
        S.espHitboxEnabled = false
        S.antiAntiAntiDesyncEnabled = false
        S.spinOnTPBatEnabled = false
        S.spinOnTPBatSpeed = 250
        S.tpBatDistance = 100

        KB.DropBrainrot = {kb=Enum.KeyCode.H, gp=nil}
        KB.AutoLeft = {kb=Enum.KeyCode.Z, gp=nil}
        KB.AutoRight = {kb=Enum.KeyCode.C, gp=nil}
        KB.AutoBat = {kb=Enum.KeyCode.E, gp=nil}
        KB.TPBat = {kb=Enum.KeyCode.V, gp=Enum.KeyCode.ButtonY}
        KB.TPFloor = {kb=Enum.KeyCode.F, gp=nil}
        KB.InstaReset = {kb=Enum.KeyCode.T, gp=nil}
        KB.GuiHide = {kb=Enum.KeyCode.LeftControl, gp=nil}
        KB.SpeedToggle = {kb=Enum.KeyCode.Q, gp=nil}
        KB.LaggerToggle = {kb=Enum.KeyCode.R, gp=nil}
        TP_BAT_KEY = Enum.KeyCode.V
        TP_BAT_CONTROLLER = Enum.KeyCode.ButtonY
        tpBatMode = 1

        pcall(function()
            selectedStealMode = "Normal"
            AceStealRadii = AceStealRadii or {Normal=55, Semi=20}
            AceStealRadii.Normal = 55
            AceStealRadii.Semi = 20
            autoStealRadius = 55
            if Steal then Steal.StealRadius = 55; Steal.StealDuration = 1.3 end
            autoGrabStopEnabled = false
        end)

        pcall(function()
            if K7 then
                K7.lineESPEnabled = false
                K7.speedESPEnabled = false
                K7.fovEnabled = false
                K7.bodyLockEnabled = false
                K7.bodyLockRadius = 60
                K7.headlessEnabled = false
                K7.korbloxEnabled = false
                K7.animPackEnabled = false
                if K7.refreshESP then K7.refreshESP() end
            end
        end)

        _G.Hub073MobileButtonPositions = {}
        pcall(function() if F.resetMobileButtons then F.resetMobileButtons() end end)
        pcall(function() if F.applyMobileButtonsHidden then F.applyMobileButtonsHidden() end end)
        pcall(function() if _G._Hub073ApplyGuiScale then _G._Hub073ApplyGuiScale(1) end end)

        pcall(function()
            local df = delfile or (syn and syn.delfile)
            if df then
                if _isfile(CONFIG_FILE) then df(CONFIG_FILE) end
                if _isfile(CONFIG_FILE_LEGACY) then df(CONFIG_FILE_LEGACY) end
            elseif _writefile then
                _writefile(CONFIG_FILE, "{}")
                _writefile(CONFIG_FILE_LEGACY, "{}")
            end
        end)
        _savedCfg = nil

        pcall(function()
            if UI.normalBox then UI.normalBox.Text = "60" end
            if UI.carryBox then UI.carryBox.Text = "30" end
            if UI.laggerBox then UI.laggerBox.Text = "15" end
            if UI.laggerCarryBox then UI.laggerCarryBox.Text = "24.5" end
            if UI.autoTPHeightBox then UI.autoTPHeightBox.Text = "20" end
            if UI.radInput then UI.radInput.Text = "55" end
            if UI.stealModeLbl then UI.stealModeLbl.Text = "Normal" end
            if UI.mobileSizeLbl then UI.mobileSizeLbl.Text = "0.80" end
            if F.refreshSpeedModeLabel then F.refreshSpeedModeLabel() end
        end)

        local visualOff = {
            "setAntiRagVisual","setInfJumpVisual","setTPBatVisual","setAutoTPVisual","setUnwalkVisual",
            "setAntiLagVisual","setStretchRezVisual","setShowTracerVisual","setRagdollCdVisual",
            "setNoPlayerCollisionVisual","setSafeModeVisual","setMedusaVisual","setMedusaResetVisual",
            "setAutoResetOnMedVisual","setBatCounterVisual",
            "setAutoSwingVisual","setLockGuiVisual","setHideMobileVisual","setLineESPVisual",
            "setSpeedESPVisual","setBodyLockVisual","setHeadlessVisual","setKorbloxVisual","setAnimPackVisual",
            "setFOVVisual", "setEspHitboxVisual", "setAntiAntiAntiDesyncVisual", "setSpinOnTPBatVisual",
        }
        for _, name in ipairs(visualOff) do
            pcall(function() if VS[name] then VS[name](false) end end)
        end
        pcall(function() if S.setAutoCarrySpeedVisual then S.setAutoCarrySpeedVisual(false) end end)
        pcall(function() if S.autoBatSetVisual then S.autoBatSetVisual(false) end end)
        pcall(function() if S.autoLeftSetVisual then S.autoLeftSetVisual(false) end end)
        pcall(function() if S.autoRightSetVisual then S.autoRightSetVisual(false) end end)
        pcall(function() if UI.saveStatusLbl then UI.saveStatusLbl.Text = "Config reset" end end)

        local ok = F.saveConfig(true)
        if UI and UI.saveStatusLbl then
            UI.saveStatusLbl.Text = ok and "Config reset OK" or "Reset done (save fail)"
            UI.saveStatusLbl.TextColor3 = ok and Color3.fromRGB(100, 220, 160) or Color3.fromRGB(255, 200, 100)
        end
        --print("[073 Hub] All configs reset to defaults")
        return ok
    end

    VS = VS or {}
    UI = UI or {}

    function F.refreshSpeedModeLabel()
        if modeValLbl then modeValLbl.Text=S.laggerToggled and (S.laggerPhase==2 and "Lagger Carry" or "Lagger Normal") or (S.speedMode and "Carry" or "Normal") end
    end

    function F.getSpeedModeName()
        if S.laggerToggled then
            return (S.laggerPhase == 2) and "Lagger Carry" or "Lagger"
        elseif S.speedMode then
            return "Carry"
        end
        return "Normal"
    end

    function F.setSpeedModeName(mode)
        if mode == "Lagger Carry" then
            S.laggerToggled = true
            S.speedMode = false
            S.laggerPhase = 2
        elseif mode == "Lagger" then
            S.laggerToggled = true
            S.speedMode = false
            S.laggerPhase = 1
        elseif mode == "Carry" then
            S.laggerToggled = false
            S.speedMode = true
            S.laggerPhase = 0
        else
            S.laggerToggled = false
            S.speedMode = false
            S.laggerPhase = 0
        end
        F.refreshSpeedModeLabel()
    end

    function F.isCarryName(name)
        local n = tostring(name or ""):lower()
        return n:find("brainrot") or n:find("animal") or n:find("carry")
            or n:find("grab") or n:find("steal") or n:find("hold")
    end

    function F.isIgnoredCarryTool(name)
        local n = tostring(name or ""):lower()
        return n:find("bat") or n:find("slap") or n:find("medusa")
            or n:find("head") or n:find("stone")
    end

    function F.isCarryingBrainrot(char)
        if not char then return false end
        for _, name in ipairs({"Carrying", "IsCarrying", "Grabbed", "Holding", "StealHold", "HasGrab"}) do
            local v = char:FindFirstChild(name, true)
            if v then
                if v:IsA("BoolValue") and v.Value then return true end
                if v:IsA("ObjectValue") and v.Value then return true end
                if v:IsA("StringValue") and v.Value ~= "" then return true end
            end
        end
        for _, child in ipairs(char:GetChildren()) do
            if child:IsA("Model") and child:FindFirstChildWhichIsA("BasePart", true) then
                if child:FindFirstChildOfClass("Humanoid") and child:FindFirstChild("HumanoidRootPart") then
                    return true
                end
                if F.isCarryName(child.Name) then return true end
            elseif child:IsA("Tool") and not F.isIgnoredCarryTool(child.Name) then
                return true
            end
        end
        return false
    end

    function F.enableCarrySpeedForSteal()
        S._autoCarryWaitingPickup = false
        S._autoCarryPickupUntil = 0
        if not S._autoCarryFromSteal then
            S._autoCarryReturnMode = F.getSpeedModeName()
        end
        S._autoCarryFromSteal = true
        S._autoCarryGraceUntil = tick() + 0.75
        local wasLagger = (S._autoCarryReturnMode == "Lagger" or S._autoCarryReturnMode == "Lagger Carry"
            or F.getSpeedModeName() == "Lagger" or F.getSpeedModeName() == "Lagger Carry")
        if wasLagger then
            F.setSpeedModeName("Lagger Carry")
        else
            F.setSpeedModeName("Carry")
        end
        pcall(function() if F.saveConfig then F.saveConfig() end end)
    end

    function F.disableAutoCarrySpeed()
        if not S._autoCarryFromSteal and not S._autoCarryWaitingPickup then return end
        local wasAutoApplied = S._autoCarryFromSteal == true
        local returnMode = S._autoCarryReturnMode
        S._autoCarryFromSteal = false
        S._autoCarryWaitingPickup = false
        S._autoCarryGraceUntil = 0
        S._autoCarryPickupUntil = 0
        S._autoCarryReturnMode = nil
        if not wasAutoApplied then return end
        if returnMode == "Lagger" or returnMode == "Lagger Carry" then
            F.setSpeedModeName("Lagger")
        elseif returnMode == "Carry" then
            F.setSpeedModeName("Carry")
        else
            F.setSpeedModeName("Normal")
        end
        pcall(function() if F.saveConfig then F.saveConfig() end end)
    end

    function F.startAutoCarryPickupWatch(seconds)
        if S.autoCarrySpeedEnabled ~= true then return end
        S._autoCarryWaitingPickup = true
        S._autoCarryPickupUntil = tick() + (seconds or 1.25)
    end

    RunService.RenderStepped:Connect(function()
        if S.autoCarrySpeedEnabled ~= true then
            if S._autoCarryFromSteal or S._autoCarryWaitingPickup then
                F.disableAutoCarrySpeed()
            end
            return
        end
        local char = LP.Character
        local hum = char and char:FindFirstChildOfClass("Humanoid")
        local root = char and char:FindFirstChild("HumanoidRootPart")
        if not char or not hum or not root then
            F.disableAutoCarrySpeed()
            S._stealAttrWasActive = false
            return
        end
        local st = hum:GetState()
        local gotHit = st == Enum.HumanoidStateType.Physics
            or st == Enum.HumanoidStateType.Ragdoll
            or st == Enum.HumanoidStateType.FallingDown
        local stealingAttr = false
        pcall(function() stealingAttr = (LP:GetAttribute("Stealing") == true) end)
        local carryingBrainrot = F.isCarryingBrainrot(char)
        if stealingAttr and not S._stealAttrWasActive then
            S._stealAttrWasActive = true
            F.enableCarrySpeedForSteal()
        elseif not stealingAttr then
            S._stealAttrWasActive = false
        end
        if S._autoCarryWaitingPickup then
            if gotHit or tick() > (S._autoCarryPickupUntil or 0) then
                S._autoCarryWaitingPickup = false
                S._autoCarryPickupUntil = 0
            elseif carryingBrainrot then
                F.enableCarrySpeedForSteal()
            end
        end
        if carryingBrainrot and not S._autoCarryFromSteal then
            F.enableCarrySpeedForSteal()
        end
        if S._autoCarryFromSteal then
            local graceDone = tick() > (S._autoCarryGraceUntil or 0)
            if gotHit or (graceDone and not carryingBrainrot and not stealingAttr) then
                F.disableAutoCarrySpeed()
            end
        end
    end)

    function F.toggleCarryMode()
        if S.laggerToggled then
            S.laggerToggled=false
            S.laggerPhase=0
            S.speedMode=true
        else
            S.speedMode=not S.speedMode
        end
        F.refreshSpeedModeLabel()
    end

    function F.toggleLaggerMode()
        if not S.laggerToggled then
            S.speedMode=false
            S.laggerToggled=true
            S.laggerPhase=2
        elseif S.laggerPhase==2 then
            S.laggerPhase=1
        else
            S.laggerPhase=2
        end
        F.refreshSpeedModeLabel()
    end

    



    -- ===================== CUSTOM ITEM =====================
    local function __initCustomItem_DISABLED()
        do return end
        local CUSTOM_ITEM = {
            BAT_V1 = 10127816997,
            BAT_V2 = 12260963528,
            MEDUSA_V1 = 5924517530,
            MEDUSA_V2 = 5472349697,
            BAT_SOUND = 126129839933832,
            MEDUSA_SOUND = 117399793253883,
            TRYHARD_HAIR = 133709322370566,
            TRYHARD_SHIRT = 112813994242906,
            TRYHARD_PANTS = 77962845676404,
            TRYHARD_BUNDLE = 274144103039631,
            gui = nil,
            panel = nil,
            batV1On = false,
            batV2On = false,
            medusaV1On = false,
            medusaV2On = false,
            tryhardV1On = false,
            conns = {},
            batSoundToken = 0,
        }

        local function ciPlaySound(soundId, parent, delaySec)
            task.spawn(function()
                if delaySec and delaySec > 0 then task.wait(delaySec) end
                pcall(function()
                    local s = Instance.new("Sound")
                    s.SoundId = "rbxassetid://" .. tostring(soundId)
                    s.Volume = 1
                    s.Parent = parent or workspace.CurrentCamera or workspace
                    s:Play()
                    game:GetService("Debris"):AddItem(s, 8)
                end)
            end)
        end

        local function ciGetHandle(tool)
            if not tool then return nil end
            return tool:FindFirstChild("Handle") or tool:FindFirstChildWhichIsA("BasePart")
        end

        local function ciIsBatTool(tool)
            if not tool or not tool:IsA("Tool") then return false end
            return string.lower(tool.Name):find("bat", 1, true) ~= nil
        end

        local function ciIsMedusaTool(tool)
            if not tool or not tool:IsA("Tool") then return false end
            return string.lower(tool.Name):find("medusa", 1, true) ~= nil
        end

        local function ciClearCustomVisual(tool)
            if not tool then return end
            local folder = tool:FindFirstChild("Hub073CustomVisual")
            if folder then pcall(function() folder:Destroy() end) end
            local handle = ciGetHandle(tool)
            if handle then
                local orig = tool:GetAttribute("Hub073OrigHandleTrans")
                if orig ~= nil then pcall(function() handle.Transparency = orig end) end
            end
        end

        local function ciLoadAssetModel(assetId)
            local InsertService = game:GetService("InsertService")
            local ok, model = pcall(function()
                return InsertService:LoadAsset(tonumber(assetId))
            end)
            if ok and model then return model end
            ok, model = pcall(function()
                if game.GetObjects then
                    local objs = game:GetObjects("rbxassetid://" .. tostring(assetId))
                    if objs and objs[1] then return objs[1] end
                end
            end)
            if ok and model then return model end
            return nil
        end

        local function ciAttachVisualToTool(tool, assetId)
            if not tool or not assetId then return end
            local handle = ciGetHandle(tool)
            if not handle then return end
            local old = tool:FindFirstChild("Hub073CustomVisual")
            if old then pcall(function() old:Destroy() end) end
            if tool:GetAttribute("Hub073OrigHandleTrans") == nil then
                tool:SetAttribute("Hub073OrigHandleTrans", handle.Transparency)
            end
            local model = ciLoadAssetModel(assetId)
            if not model then
                --warn("[073 Hub] Custom Item failed load", assetId)
                return
            end
            local folder = Instance.new("Folder")
            folder.Name = "Hub073CustomVisual"
            folder.Parent = tool
            local parts = {}
            for _, d in ipairs(model:GetDescendants()) do
                if d:IsA("BasePart") then table.insert(parts, d) end
            end
            if model:IsA("BasePart") then table.insert(parts, model) end
            if model:IsA("Accessory") then
                local ah = model:FindFirstChild("Handle")
                if ah and ah:IsA("BasePart") then parts = { ah } end
            end
            if #parts == 0 then
                --warn("[073 Hub] Custom Item asset has no parts", assetId)
                pcall(function() model:Destroy() end)
                return
            end
            handle.Transparency = 1
            for _, src in ipairs(parts) do
                local ok, clone = pcall(function() return src:Clone() end)
                if ok and clone then
                    clone.Name = "CI_" .. src.Name
                    clone.Anchored = false
                    clone.CanCollide = false
                    clone.Massless = true
                    pcall(function()
                        clone.CanQuery = false
                        clone.CanTouch = false
                    end)
                    clone.CFrame = handle.CFrame
                    clone.Parent = folder
                    local weld = Instance.new("WeldConstraint")
                    weld.Part0 = handle
                    weld.Part1 = clone
                    weld.Parent = clone
                end
            end
            pcall(function() model:Destroy() end)
        end

        local function ciApplyBatVisual(tool)
            if not tool then return end
            if CUSTOM_ITEM.batV1On then
                task.spawn(function() ciAttachVisualToTool(tool, CUSTOM_ITEM.BAT_V1) end)
            elseif CUSTOM_ITEM.batV2On then
                task.spawn(function() ciAttachVisualToTool(tool, CUSTOM_ITEM.BAT_V2) end)
            else
                ciClearCustomVisual(tool)
            end
        end

        local function ciApplyMedusaVisual(tool)
            if not tool then return end
            if CUSTOM_ITEM.medusaV1On then
                task.spawn(function() ciAttachVisualToTool(tool, CUSTOM_ITEM.MEDUSA_V1) end)
            elseif CUSTOM_ITEM.medusaV2On then
                task.spawn(function() ciAttachVisualToTool(tool, CUSTOM_ITEM.MEDUSA_V2) end)
            else
                if ciIsMedusaTool(tool) then ciClearCustomVisual(tool) end
            end
        end

        local function ciApplyTryhard()
            local char = LP.Character
            if not char then return end
            local hum = char:FindFirstChildOfClass("Humanoid")
            if not hum then return end

            -- shirt / pants
            pcall(function()
                for _, ch in ipairs(char:GetChildren()) do
                    if ch:IsA("Shirt") or ch:IsA("Pants") then ch:Destroy() end
                end
                local sh = Instance.new("Shirt")
                sh.Name = "Hub073TryhardShirt"
                sh.ShirtTemplate = "rbxassetid://" .. tostring(CUSTOM_ITEM.TRYHARD_SHIRT)
                sh.Parent = char
                local pn = Instance.new("Pants")
                pn.Name = "Hub073TryhardPants"
                pn.PantsTemplate = "rbxassetid://" .. tostring(CUSTOM_ITEM.TRYHARD_PANTS)
                pn.Parent = char
            end)

            -- hair accessory
            task.spawn(function()
                local model = ciLoadAssetModel(CUSTOM_ITEM.TRYHARD_HAIR)
                if not model then return end
                for _, inst in ipairs(model:GetChildren()) do
                    if inst:IsA("Accessory") or inst:IsA("Hat") then
                        pcall(function()
                            inst.Name = "Hub073TryhardHair"
                            hum:AddAccessory(inst)
                        end)
                    end
                end
                -- if model itself is accessory
                if model:IsA("Accessory") then
                    pcall(function()
                        model.Name = "Hub073TryhardHair"
                        hum:AddAccessory(model)
                    end)
                else
                    pcall(function() model:Destroy() end)
                end
            end)

            -- body bundle via HumanoidDescription if possible
            task.spawn(function()
                pcall(function()
                    local desc = hum:GetAppliedDescription()
                    if not desc then desc = Instance.new("HumanoidDescription") end
                    -- try apply bundle id as body parts via LoadAsset
                    local bundleModel = ciLoadAssetModel(CUSTOM_ITEM.TRYHARD_BUNDLE)
                    if bundleModel then
                        -- best effort: apply description scales thin-like
                        desc.WidthScale = 0.75
                        desc.DepthScale = 0.85
                        desc.HeightScale = 1
                        desc.BodyTypeScale = 0.1
                        desc.ProportionScale = 0.3
                        pcall(function() hum:ApplyDescription(desc) end)
                        pcall(function() bundleModel:Destroy() end)
                    end
                end)
                -- also try AssetService / HumanoidDescription bundle
                pcall(function()
                    local desc = Players:GetHumanoidDescriptionFromUserId(LP.UserId)
                    if desc then
                        desc.Shirt = CUSTOM_ITEM.TRYHARD_SHIRT
                        desc.Pants = CUSTOM_ITEM.TRYHARD_PANTS
                        pcall(function() hum:ApplyDescription(desc) end)
                    end
                end)
            end)
        end

        local function ciClearTryhard()
            local char = LP.Character
            if not char then return end
            pcall(function()
                for _, ch in ipairs(char:GetChildren()) do
                    if ch.Name == "Hub073TryhardShirt" or ch.Name == "Hub073TryhardPants" or ch.Name == "Hub073TryhardHair" then
                        ch:Destroy()
                    end
                end
            end)
        end

        local function ciHookTool(tool)
            if not tool or not tool:IsA("Tool") then return end
            if tool:GetAttribute("Hub073CIHooked") then
                if ciIsBatTool(tool) then ciApplyBatVisual(tool) end
                if ciIsMedusaTool(tool) then ciApplyMedusaVisual(tool) end
                return
            end
            tool:SetAttribute("Hub073CIHooked", true)
            if ciIsBatTool(tool) then
                ciApplyBatVisual(tool)
                tool.Equipped:Connect(function()
                    task.defer(function() ciApplyBatVisual(tool) end)
                end)
                tool.Activated:Connect(function()
                    if CUSTOM_ITEM.batV1On or CUSTOM_ITEM.batV2On then
                        -- Bat v1: delay 1s; Bat v2: immediate same sound
                        local delaySec = CUSTOM_ITEM.batV1On and 1 or 0
                        ciPlaySound(CUSTOM_ITEM.BAT_SOUND, ciGetHandle(tool) or tool, delaySec)
                    end
                end)
            elseif ciIsMedusaTool(tool) then
                ciApplyMedusaVisual(tool)
                tool.Equipped:Connect(function()
                    task.defer(function() ciApplyMedusaVisual(tool) end)
                end)
                tool.Activated:Connect(function()
                    if CUSTOM_ITEM.medusaV1On or CUSTOM_ITEM.medusaV2On then
                        ciPlaySound(CUSTOM_ITEM.MEDUSA_SOUND, ciGetHandle(tool) or tool, 0)
                    end
                end)
            end
        end

        local function ciScan(container)
            if not container then return end
            for _, obj in ipairs(container:GetChildren()) do
                if obj:IsA("Tool") then ciHookTool(obj) end
            end
        end

        function F.startCustomItemHooks()
            for _, cn in ipairs(CUSTOM_ITEM.conns) do pcall(function() cn:Disconnect() end) end
            CUSTOM_ITEM.conns = {}
            local bp = LP:FindFirstChildOfClass("Backpack")
            if bp then
                ciScan(bp)
                table.insert(CUSTOM_ITEM.conns, bp.ChildAdded:Connect(function(ch)
                    if ch:IsA("Tool") then task.defer(function() ciHookTool(ch) end) end
                end))
            end
            if LP.Character then
                ciScan(LP.Character)
                table.insert(CUSTOM_ITEM.conns, LP.Character.ChildAdded:Connect(function(ch)
                    if ch:IsA("Tool") then task.defer(function() ciHookTool(ch) end) end
                end))
            end
            table.insert(CUSTOM_ITEM.conns, LP.CharacterAdded:Connect(function(char)
                task.wait(0.25)
                ciScan(char)
                table.insert(CUSTOM_ITEM.conns, char.ChildAdded:Connect(function(ch)
                    if ch:IsA("Tool") then task.defer(function() ciHookTool(ch) end) end
                end))
                local bp2 = LP:FindFirstChildOfClass("Backpack")
                if bp2 then ciScan(bp2) end
                if CUSTOM_ITEM.tryhardV1On then task.defer(ciApplyTryhard) end
            end))
        end

        -- Bat v1 / v2 exclusive with each other; Medusa independent
        function F.setCustomBatV1(on)
            CUSTOM_ITEM.batV1On = on and true or false
            S.customItemBatV1 = CUSTOM_ITEM.batV1On
            if CUSTOM_ITEM.batV1On then
                CUSTOM_ITEM.batV2On = false
                S.customItemBatV2 = false
            end
            F.startCustomItemHooks()
            pcall(function() if F.saveConfig then F.saveConfig() end end)
        end

        function F.setCustomBatV2(on)
            CUSTOM_ITEM.batV2On = on and true or false
            S.customItemBatV2 = CUSTOM_ITEM.batV2On
            if CUSTOM_ITEM.batV2On then
                CUSTOM_ITEM.batV1On = false
                S.customItemBatV1 = false
            end
            F.startCustomItemHooks()
            pcall(function() if F.saveConfig then F.saveConfig() end end)
        end

        function F.setCustomMedusaV1(on)
            CUSTOM_ITEM.medusaV1On = on and true or false
            S.customItemMedusaV1 = CUSTOM_ITEM.medusaV1On
            if CUSTOM_ITEM.medusaV1On then
                CUSTOM_ITEM.medusaV2On = false
                S.customItemMedusaV2 = false
            end
            F.startCustomItemHooks()
            pcall(function() if F.saveConfig then F.saveConfig() end end)
        end

        function F.setCustomMedusaV2(on)
            CUSTOM_ITEM.medusaV2On = on and true or false
            S.customItemMedusaV2 = CUSTOM_ITEM.medusaV2On
            if CUSTOM_ITEM.medusaV2On then
                CUSTOM_ITEM.medusaV1On = false
                S.customItemMedusaV1 = false
            end
            F.startCustomItemHooks()
            pcall(function() if F.saveConfig then F.saveConfig() end end)
        end

        function F.setCustomTryhardV1(on)
            CUSTOM_ITEM.tryhardV1On = on and true or false
            S.customItemTryhardV1 = CUSTOM_ITEM.tryhardV1On
            if CUSTOM_ITEM.tryhardV1On then
                ciApplyTryhard()
            else
                ciClearTryhard()
            end
            pcall(function() if F.saveConfig then F.saveConfig() end end)
        end

        function F.buildCustomItemPanel()
            if CUSTOM_ITEM.gui and CUSTOM_ITEM.gui.Parent then
                pcall(function() CUSTOM_ITEM.gui:Destroy() end)
            end
            local parent = LP:FindFirstChild("PlayerGui") or LP:WaitForChild("PlayerGui", 3)
            pcall(function() if type(gethui) == "function" then parent = gethui() end end)

            local gui = Instance.new("ScreenGui")
            gui.Name = "Hub073CustomItem"
            gui.ResetOnSpawn = false
            gui.IgnoreGuiInset = true
            gui.DisplayOrder = 70
            gui.Parent = parent
            CUSTOM_ITEM.gui = gui

            local dim = Instance.new("TextButton")
            dim.Name = "Dim"
            dim.Size = UDim2.new(1, 0, 1, 0)
            dim.BackgroundColor3 = Color3.fromRGB(0, 0, 0)
            dim.BackgroundTransparency = 0.45
            dim.BorderSizePixel = 0
            dim.Text = ""
            dim.AutoButtonColor = false
            dim.Parent = gui
            dim.MouseButton1Click:Connect(function() gui.Enabled = false end)

            local panel = Instance.new("Frame")
            panel.Name = "Panel"
            panel.AnchorPoint = Vector2.new(0.5, 0.5)
            panel.Position = UDim2.new(0.5, 0, 0.5, 0)
            panel.Size = UDim2.new(0, 420, 0, 260)
            panel.BackgroundColor3 = Color3.fromRGB(18, 18, 22)
            panel.BackgroundTransparency = 0.08
            panel.BorderSizePixel = 0
            panel.Parent = gui
            CUSTOM_ITEM.panel = panel
            Instance.new("UICorner", panel).CornerRadius = UDim.new(0, 14)
            local ps = Instance.new("UIStroke")
            ps.Color = S.themeColor or Color3.fromRGB(255, 255, 255)
            ps.Thickness = 1.5
            ps.Transparency = 0.25
            ps.Parent = panel

            local bg = Instance.new("ImageLabel")
            bg.Name = "BG"
            bg.Size = UDim2.new(1, 0, 1, 0)
            bg.BackgroundTransparency = 1
            bg.ImageTransparency = 0.55
            bg.ScaleType = Enum.ScaleType.Crop
            bg.ZIndex = 0
            bg.Parent = panel
            Instance.new("UICorner", bg).CornerRadius = UDim.new(0, 14)

            local title = Instance.new("TextLabel")
            title.BackgroundTransparency = 1
            title.Size = UDim2.new(1, -50, 0, 28)
            title.Position = UDim2.new(0, 14, 0, 10)
            title.Text = "CUSTOM ITEM"
            title.Font = Enum.Font.GothamBlack
            title.TextSize = 15
            title.TextXAlignment = Enum.TextXAlignment.Left
            title.TextColor3 = Color3.fromRGB(255, 255, 255)
            title.ZIndex = 2
            title.Parent = panel

            local close = Instance.new("TextButton")
            close.Size = UDim2.new(0, 28, 0, 28)
            close.Position = UDim2.new(1, -36, 0, 8)
            close.BackgroundColor3 = Color3.fromRGB(40, 40, 48)
            close.Text = "X"
            close.Font = Enum.Font.GothamBold
            close.TextSize = 13
            close.TextColor3 = Color3.fromRGB(255, 255, 255)
            close.ZIndex = 3
            close.Parent = panel
            Instance.new("UICorner", close).CornerRadius = UDim.new(0, 8)
            close.MouseButton1Click:Connect(function() gui.Enabled = false end)

            local grid = Instance.new("Frame")
            grid.BackgroundTransparency = 1
            grid.Size = UDim2.new(1, -24, 0, 190)
            grid.Position = UDim2.new(0, 12, 0, 48)
            grid.ZIndex = 2
            grid.Parent = panel
            local lay = Instance.new("UIGridLayout")
            lay.CellSize = UDim2.new(0, 120, 0, 56)
            lay.CellPadding = UDim2.new(0, 8, 0, 8)
            lay.FillDirectionMaxCells = 3
            lay.HorizontalAlignment = Enum.HorizontalAlignment.Center
            lay.SortOrder = Enum.SortOrder.LayoutOrder
            lay.Parent = grid

            local function makeOpt(order, text, getOn, setOn)
                local btn = Instance.new("TextButton")
                btn.LayoutOrder = order
                btn.BackgroundColor3 = Color3.fromRGB(28, 28, 34)
                btn.BorderSizePixel = 0
                btn.Text = ""
                btn.ZIndex = 3
                btn.Parent = grid
                Instance.new("UICorner", btn).CornerRadius = UDim.new(0, 10)
                local st = Instance.new("UIStroke")
                st.Thickness = 1.5
                st.Parent = btn
                local lbl = Instance.new("TextLabel")
                lbl.BackgroundTransparency = 1
                lbl.Size = UDim2.new(1, -6, 1, -6)
                lbl.Position = UDim2.new(0, 3, 0, 3)
                lbl.Font = Enum.Font.GothamBold
                lbl.TextSize = 12
                lbl.TextWrapped = true
                lbl.TextColor3 = Color3.fromRGB(255, 255, 255)
                lbl.ZIndex = 4
                lbl.Parent = btn
                local function refresh()
                    local on = getOn()
                    st.Color = on and (S.themeColor or Color3.fromRGB(0, 255, 140)) or Color3.fromRGB(80, 80, 90)
                    st.Transparency = on and 0 or 0.35
                    btn.BackgroundColor3 = on and Color3.fromRGB(36, 42, 40) or Color3.fromRGB(28, 28, 34)
                    lbl.Text = text .. (on and "\nON" or "\nOFF")
                end
                btn.MouseButton1Click:Connect(function()
                    setOn(not getOn())
                    if CUSTOM_ITEM._refreshAllOpts then CUSTOM_ITEM._refreshAllOpts() end
                end)
                return refresh
            end

            local refreshes = {
                makeOpt(1, "Bat v1", function() return CUSTOM_ITEM.batV1On end, F.setCustomBatV1),
                makeOpt(2, "Bat v2", function() return CUSTOM_ITEM.batV2On end, F.setCustomBatV2),
                makeOpt(3, "Medusa v1", function() return CUSTOM_ITEM.medusaV1On end, F.setCustomMedusaV1),
                makeOpt(4, "Medusa v2", function() return CUSTOM_ITEM.medusaV2On end, F.setCustomMedusaV2),
                makeOpt(5, "Tryhard v1", function() return CUSTOM_ITEM.tryhardV1On end, F.setCustomTryhardV1),
            }
            CUSTOM_ITEM._refreshAllOpts = function()
                for _, fn in ipairs(refreshes) do pcall(fn) end
            end
            CUSTOM_ITEM._refreshAllOpts()
            gui.Enabled = false
        end

        function F.openCustomItemPanel()
            if not (CUSTOM_ITEM.gui and CUSTOM_ITEM.gui.Parent) then
                F.buildCustomItemPanel()
            end
            CUSTOM_ITEM.batV1On = S.customItemBatV1 == true
            CUSTOM_ITEM.batV2On = S.customItemBatV2 == true
            CUSTOM_ITEM.medusaV1On = S.customItemMedusaV1 == true
            CUSTOM_ITEM.medusaV2On = S.customItemMedusaV2 == true
            CUSTOM_ITEM.tryhardV1On = S.customItemTryhardV1 == true
            if CUSTOM_ITEM._refreshAllOpts then CUSTOM_ITEM._refreshAllOpts() end
            pcall(function()
                local bg = CUSTOM_ITEM.panel and CUSTOM_ITEM.panel:FindFirstChild("BG")
                if bg and bg:IsA("ImageLabel") then
                    local id = S.currentBgId
                    if type(id) == "number" and id > 0 and BG_URLS and BG_URLS[id] then
                        bg.Image = BG_URLS[id]
                        if resolveBgAsset then
                            local asset = resolveBgAsset(id)
                            if asset then bg.Image = asset end
                        end
                    end
                end
            end)
            CUSTOM_ITEM.gui.Enabled = true
        end





    end
    -- nil -- custom item disabled disabled

    -- ===================== CUSTOM GEARS / XRAY =====================
    local function __initCustomGearsXray()
        local GEAR_V1_BAT, GEAR_V1_MED = 59190543, 11563251
        local GEAR_V2_BAT, GEAR_V2_MED = 95951330, 124126871
        local SOUND_BAT, SOUND_MED = 111125605003822, 111026285687215
        local gearConns = {}
        local XrayBase = { enabled = false, original = {}, conn = nil, level = 1 }
        local lastBatSound, lastMedSound = 0, 0

        local function isBat(tool)
            if not (tool and tool:IsA("Tool")) then return false end
            local n = string.lower(tool.Name)
            return n:find("bat", 1, true) ~= nil or n:find("bast", 1, true) ~= nil
        end
        local function isMed(tool)
            return tool and tool:IsA("Tool") and string.lower(tool.Name):find("medusa", 1, true) ~= nil
        end
        local function getHandle(tool)
            return tool and (tool:FindFirstChild("Handle") or tool:FindFirstChildWhichIsA("BasePart"))
        end
        local function loadModel(assetId)
            local ok, model = pcall(function()
                return game:GetService("InsertService"):LoadAsset(tonumber(assetId))
            end)
            if ok and model then return model end
            ok, model = pcall(function()
                if game.GetObjects then
                    local objs = game:GetObjects("rbxassetid://" .. tostring(assetId))
                    if objs and objs[1] then return objs[1] end
                end
            end)
            return (ok and model) or nil
        end
        local function clearVisual(tool)
            if not tool then return end
            local f = tool:FindFirstChild("Hub073GearVisual")
            if f then pcall(function() f:Destroy() end) end
            local h = getHandle(tool)
            if h then
                local ot = tool:GetAttribute("Hub073GearOrigTrans")
                if ot ~= nil then pcall(function() h.Transparency = ot end) end
                pcall(function() h.LocalTransparencyModifier = 0 end)
            end
        end
        local function attach(tool, assetId)
            if not tool or not assetId then return end
            local handle = getHandle(tool)
            if not handle then return end
            clearVisual(tool)
            if tool:GetAttribute("Hub073GearOrigTrans") == nil then
                tool:SetAttribute("Hub073GearOrigTrans", handle.Transparency)
            end
            local model = loadModel(assetId)
            if not model then return end
            local folder = Instance.new("Folder")
            folder.Name = "Hub073GearVisual"
            folder.Parent = tool
            local parts = {}
            local lt = model:FindFirstChildWhichIsA("Tool", true)
            local srcHandle = lt and lt:FindFirstChild("Handle")
            if not srcHandle and model:IsA("Tool") then srcHandle = model:FindFirstChild("Handle") end
            if srcHandle and srcHandle:IsA("BasePart") then
                parts = {srcHandle}
            else
                for _, d in ipairs(model:GetDescendants()) do
                    if d:IsA("BasePart") then parts[#parts+1] = d end
                end
            end
            if #parts == 0 then pcall(function() model:Destroy() end) return end
            handle.Transparency = 1
            for _, src in ipairs(parts) do
                local ok, clone = pcall(function() return src:Clone() end)
                if ok and clone then
                    clone.Name = "GV_" .. src.Name
                    clone.Anchored = false
                    clone.CanCollide = false
                    clone.Massless = true
                    clone.CFrame = handle.CFrame
                    clone.Parent = folder
                    local w = Instance.new("WeldConstraint")
                    w.Part0 = handle
                    w.Part1 = clone
                    w.Parent = clone
                end
            end
            pcall(function() model:Destroy() end)
        end
        local function applyXrayGears(tool)
            local handle = getHandle(tool)
            if handle then
                if tool:GetAttribute("Hub073GearOrigTrans") == nil then
                    tool:SetAttribute("Hub073GearOrigTrans", handle.Transparency)
                end
                handle.LocalTransparencyModifier = 0.45
            end
            local folder = tool:FindFirstChild("Hub073GearVisual")
            if folder then
                for _, p in ipairs(folder:GetDescendants()) do
                    if p:IsA("BasePart") then p.LocalTransparencyModifier = 0.45 end
                end
            end
        end
        local function clearXrayGears(tool)
            local handle = getHandle(tool)
            if handle then handle.LocalTransparencyModifier = 0 end
            local folder = tool:FindFirstChild("Hub073GearVisual")
            if folder then
                for _, p in ipairs(folder:GetDescendants()) do
                    if p:IsA("BasePart") then p.LocalTransparencyModifier = 0 end
                end
            end
        end
        local function playCustomSound(soundId, parent, cdKey)
            if not S.customSoundEnabled then return end
            if S.tpBatEnabled or S.autoBatEnabled then return end
            local now = tick()
            if cdKey == "bat" then
                if now - lastBatSound < 0.5 then return end
                lastBatSound = now
            elseif cdKey == "med" then
                if now - lastMedSound < 12 then return end
                lastMedSound = now
            end
            pcall(function()
                local s = Instance.new("Sound")
                s.SoundId = "rbxassetid://" .. tostring(soundId)
                s.Volume = 1
                s.Parent = parent or workspace
                s:Play()
                game:GetService("Debris"):AddItem(s, 5)
            end)
        end
        local function hookToolActivate(tool)
            if tool:GetAttribute("Hub073SoundHooked") then return end
            tool:SetAttribute("Hub073SoundHooked", true)
            tool.Activated:Connect(function()
                if isBat(tool) then
                    playCustomSound(SOUND_BAT, getHandle(tool) or tool, "bat")
                elseif isMed(tool) then
                    playCustomSound(SOUND_MED, getHandle(tool) or tool, "med")
                end
            end)
        end
        local function refreshTool(tool)
            if not tool or not tool:IsA("Tool") then return end
            local batId, medId = nil, nil
            if S.customGearsV2Enabled then
                batId, medId = GEAR_V2_BAT, GEAR_V2_MED
            elseif S.customGearsEnabled then
                batId, medId = GEAR_V1_BAT, GEAR_V1_MED
            end
            if isBat(tool) then
                if batId then task.spawn(function() attach(tool, batId) end) else clearVisual(tool) end
                if S.xrayGearsEnabled then applyXrayGears(tool) else clearXrayGears(tool) end
                hookToolActivate(tool)
            elseif isMed(tool) then
                if medId then task.spawn(function() attach(tool, medId) end) else clearVisual(tool) end
                if S.xrayGearsEnabled then applyXrayGears(tool) else clearXrayGears(tool) end
                hookToolActivate(tool)
            end
        end
        local function scanAll()
            local bp = LP:FindFirstChildOfClass("Backpack")
            if bp then for _, t in ipairs(bp:GetChildren()) do if t:IsA("Tool") then refreshTool(t) end end end
            if LP.Character then for _, t in ipairs(LP.Character:GetChildren()) do if t:IsA("Tool") then refreshTool(t) end end end
        end
        local function startHooks()
            for _, cn in ipairs(gearConns) do pcall(function() cn:Disconnect() end) end
            gearConns = {}
            local function hook(container)
                if not container then return end
                for _, ch in ipairs(container:GetChildren()) do if ch:IsA("Tool") then refreshTool(ch) end end
                gearConns[#gearConns+1] = container.ChildAdded:Connect(function(ch)
                    if ch:IsA("Tool") then
                        task.defer(function() refreshTool(ch) end)
                        pcall(function()
                            ch.Equipped:Connect(function()
                                task.wait(0.05)
                                refreshTool(ch)
                            end)
                        end)
                    end
                end)
            end
            hook(LP:FindFirstChildOfClass("Backpack"))
            if LP.Character then hook(LP.Character) end
            gearConns[#gearConns+1] = LP.CharacterAdded:Connect(function(char)
                task.wait(0.3)
                hook(char)
                hook(LP:FindFirstChildOfClass("Backpack"))
                scanAll()
            end)
            scanAll()
        end

        F.setCustomGears = function(on)
            S.customGearsEnabled = on and true or false
            if on then S.customGearsV2Enabled = false end
            startHooks()
            pcall(function() if F.saveConfig then F.saveConfig() end end)
        end
        F.setCustomGearsV2 = function(on)
            do return end
            S.customGearsV2Enabled = on and true or false
            if on then S.customGearsEnabled = false end
            startHooks()
            pcall(function() if F.saveConfig then F.saveConfig() end end)
        end
        F.setXrayGears = function(on)
            S.xrayGearsEnabled = on and true or false
            startHooks()
            pcall(function() if F.saveConfig then F.saveConfig() end end)
        end
        F.setCustomSound = function(on)
            S.customSoundEnabled = on and true or false
            startHooks()
            pcall(function() if F.saveConfig then F.saveConfig() end end)
        end

        local function isPlotPart(obj)
            if not (obj:IsA("BasePart") or obj:IsA("MeshPart") or obj:IsA("UnionOperation")) then return false end
            if obj.Transparency >= 0.9 then return false end
            local n = string.lower(obj.Name)
            if n:find("plot", 1, true) then return true end
            local p, depth = obj.Parent, 0
            while p and depth < 6 do
                if string.find(string.lower(p.Name), "plot", 1, true) then return true end
                p = p.Parent
                depth = depth + 1
            end
            return false
        end
        local function xrayAmount()
            local lvl = math.clamp(tonumber(XrayBase.level) or 1, 0.1, 1)
            return math.clamp(1.05 - lvl, 0.35, 0.95)
        end
        local function applyBaseXray(obj)
            if not XrayBase.enabled then return end
            if not isPlotPart(obj) then return end
            if XrayBase.original[obj] == nil then
                XrayBase.original[obj] = obj.LocalTransparencyModifier
            end
            pcall(function() obj.LocalTransparencyModifier = xrayAmount() end)
        end
        local function scanBaseXray()
            for _, d in ipairs(workspace:GetDescendants()) do applyBaseXray(d) end
        end
        F.enableXrayBase = function()
            XrayBase.enabled = true
            S.xrayBaseEnabled = true
            XrayBase.level = tonumber(S.xrayBaseLevel) or 1
            if XrayBase.conn then pcall(function() XrayBase.conn:Disconnect() end) end
            scanBaseXray()
            XrayBase.conn = workspace.DescendantAdded:Connect(function(obj)
                task.defer(function() applyBaseXray(obj) end)
            end)
        end
        F.disableXrayBase = function()
            XrayBase.enabled = false
            S.xrayBaseEnabled = false
            if XrayBase.conn then pcall(function() XrayBase.conn:Disconnect() end); XrayBase.conn = nil end
            for part, val in pairs(XrayBase.original) do
                if part and part.Parent then pcall(function() part.LocalTransparencyModifier = val end) end
            end
            XrayBase.original = {}
        end
        F.setXrayBase = function(on)
            do return end
            if on then F.enableXrayBase() else F.disableXrayBase() end
            pcall(function() if F.saveConfig then F.saveConfig() end end)
        end
        F.setXrayBaseLevel = function(v)
            local n = math.clamp(tonumber(v) or 1, 0.1, 1)
            XrayBase.level = n
            S.xrayBaseLevel = n
            if XrayBase.enabled then
                for part, _ in pairs(XrayBase.original) do
                    if part and part.Parent then pcall(function() part.LocalTransparencyModifier = xrayAmount() end) end
                end
                scanBaseXray()
            end
        end

        local SKIN_ITEMS = {
            {id=71637138543841, name="Roupa 1"},
            {id=124997335091634, name="Roupa 2"},
            {id=74823877987595, name="Roupa 3"},
            {id=125157548394108, name="ROCKSTAR Hoodie"},
            {id=78289009309744, name="Black Scene Hair"},
            {id=139893040832892, name="Anime Punk Hair"},
            {id=111963259797542, name="Spiky Emo Hair"},
            {id=86707394443389, name="White Long Hair"},
            {id=150381051, name="Spring Fairy"},
            {id=114535434198145, name="Propeller Hat"},
            {id=71359799639529, name="Arab Headscarf"},
            {id=91089363920866, name="Evil Morty Head"},
            {id=76492631904808, name="Pixel Sword"},
            {id=17695915660, name="Pirate Hat"},
            {id=17013631047, name="yabujin"},
            {id=79020669384286, name="com boy v2"},
            {id=123939037835229, name="BRASIL"},
        }
        local BODY_TINY_BUNDLE = 150255110427123

        local function wearAsset(assetId)
            task.spawn(function()
                local id = tonumber(assetId)
                if not id then return end
                local char = LP.Character
                if not char then return end
                local hum = char:FindFirstChildOfClass("Humanoid")
                -- Method 1: InsertService LoadAsset
                local model = nil
                pcall(function()
                    model = game:GetService("InsertService"):LoadAsset(id)
                end)
                if not model then
                    pcall(function()
                        if game.GetObjects then
                            local objs = game:GetObjects("rbxassetid://" .. tostring(id))
                            model = objs and objs[1]
                        end
                    end)
                end
                if model then
                    for _, d in ipairs(model:GetDescendants()) do
                        pcall(function()
                            if d:IsA("Accessory") then
                                local cl = d:Clone()
                                cl.Parent = char
                            elseif d:IsA("Shirt") then
                                local old = char:FindFirstChildOfClass("Shirt")
                                if old then old:Destroy() end
                                local cl = d:Clone()
                                cl.Parent = char
                            elseif d:IsA("Pants") then
                                local old = char:FindFirstChildOfClass("Pants")
                                if old then old:Destroy() end
                                local cl = d:Clone()
                                cl.Parent = char
                            elseif d:IsA("ShirtGraphic") then
                                local old = char:FindFirstChildOfClass("ShirtGraphic")
                                if old then old:Destroy() end
                                local cl = d:Clone()
                                cl.Parent = char
                            end
                        end)
                    end
                    for _, ch in ipairs(model:GetChildren()) do
                        pcall(function()
                            if ch:IsA("Accessory") or ch:IsA("Shirt") or ch:IsA("Pants") or ch:IsA("ShirtGraphic") then
                                if ch:IsA("Shirt") or ch:IsA("Pants") or ch:IsA("ShirtGraphic") then
                                    local old = char:FindFirstChildOfClass(ch.ClassName)
                                    if old then old:Destroy() end
                                end
                                local cl = ch:Clone()
                                cl.Parent = char
                            end
                        end)
                    end
                    pcall(function() model:Destroy() end)
                end
                -- Method 2: HumanoidDescription accessory
                pcall(function()
                    if not hum then return end
                    local desc = hum:GetAppliedDescription()
                    pcall(function()
                        desc:SetAccessory(id, true)
                    end)
                    pcall(function()
                        hum:ApplyDescription(desc)
                    end)
                end)
            end)
        end

        F.applyBodyTiny = function()
            task.spawn(function()
                local char = LP.Character
                local hum = char and char:FindFirstChildOfClass("Humanoid")
                if not hum then return end
                pcall(function()
                    local ok, model = pcall(function()
                        return game:GetService("InsertService"):LoadAsset(150255110427123)
                    end)
                    if ok and model then
                        for _, d in ipairs(model:GetDescendants()) do
                            if d:IsA("CharacterMesh") or d:IsA("Accessory") then
                                pcall(function() d:Clone().Parent = char end)
                            end
                        end
                        pcall(function() model:Destroy() end)
                    end
                    for _, n in ipairs({"BodyWidthScale", "BodyDepthScale"}) do
                        local v = hum:FindFirstChild(n)
                        if v and v:IsA("NumberValue") then v.Value = 0.75 end
                    end
                end)
            end)
        end

        F.openArmazemSkins = function()
            do return end
            local parent = nil
            pcall(function()
                if type(gethui) == "function" then parent = gethui() end
            end)
            if not parent then
                pcall(function() parent = game:GetService("CoreGui") end)
            end
            if not parent then
                parent = LP:FindFirstChild("PlayerGui") or LP:WaitForChild("PlayerGui", 5)
            end
            if not parent then return end
            local old = parent:FindFirstChild("Hub073Armazem")
            if old then old:Destroy() end
            local gui = Instance.new("ScreenGui")
            gui.Name = "Hub073Armazem"
            gui.ResetOnSpawn = false
            gui.IgnoreGuiInset = true
            gui.DisplayOrder = 999
            gui.ZIndexBehavior = Enum.ZIndexBehavior.Sibling
            pcall(function() gui.Parent = parent end)
            if not gui.Parent then
                pcall(function() gui.Parent = LP:FindFirstChild("PlayerGui") end)
            end
            if not gui.Parent then return end

            local frame = Instance.new("Frame")
            frame.Size = UDim2.new(0, 420, 0, 480)
            frame.Position = UDim2.new(0.5, -210, 0.5, -240)
            frame.BackgroundColor3 = Color3.fromRGB(12, 12, 14)
            frame.BorderSizePixel = 0
            frame.Active = true
            frame.Parent = gui
            Instance.new("UICorner", frame).CornerRadius = UDim.new(0, 12)
            local stroke = Instance.new("UIStroke")
            stroke.Color = Color3.fromRGB(60, 60, 70)
            stroke.Thickness = 1.2
            stroke.Parent = frame

            local title = Instance.new("TextLabel")
            title.Size = UDim2.new(1, -48, 0, 32)
            title.Position = UDim2.new(0, 12, 0, 8)
            title.BackgroundTransparency = 1
            title.Text = "073 Armazem Skins"
            title.Font = Enum.Font.GothamBlack
            title.TextSize = 16
            title.TextColor3 = Color3.fromRGB(255,255,255)
            title.TextXAlignment = Enum.TextXAlignment.Left
            title.Parent = frame

            local close = Instance.new("TextButton")
            close.Size = UDim2.new(0, 28, 0, 28)
            close.Position = UDim2.new(1, -36, 0, 8)
            close.BackgroundColor3 = Color3.fromRGB(50, 40, 40)
            close.Text = "X"
            close.TextColor3 = Color3.fromRGB(255,255,255)
            close.Font = Enum.Font.GothamBold
            close.Parent = frame
            Instance.new("UICorner", close).CornerRadius = UDim.new(0, 6)
            close.MouseButton1Click:Connect(function() gui:Destroy() end)

            local scroll = Instance.new("ScrollingFrame")
            scroll.Size = UDim2.new(1, -20, 1, -56)
            scroll.Position = UDim2.new(0, 10, 0, 48)
            scroll.BackgroundTransparency = 1
            scroll.BorderSizePixel = 0
            scroll.ScrollBarThickness = 5
            scroll.CanvasSize = UDim2.new(0, 0, 0, 0)
            scroll.Parent = frame
            local grid = Instance.new("UIGridLayout")
            grid.CellSize = UDim2.new(0, 90, 0, 110)
            grid.CellPadding = UDim2.new(0, 8, 0, 8)
            grid.SortOrder = Enum.SortOrder.LayoutOrder
            grid.Parent = scroll
            grid:GetPropertyChangedSignal("AbsoluteContentSize"):Connect(function()
                scroll.CanvasSize = UDim2.new(0, 0, 0, grid.AbsoluteContentSize.Y + 16)
            end)

            for idx, item in ipairs(SKIN_ITEMS) do
                local card = Instance.new("TextButton")
                card.BackgroundColor3 = Color3.fromRGB(22, 22, 26)
                card.Text = ""
                card.AutoButtonColor = true
                card.LayoutOrder = idx
                Instance.new("UICorner", card).CornerRadius = UDim.new(0, 8)
                local img = Instance.new("ImageLabel")
                img.Size = UDim2.new(1, -8, 0, 70)
                img.Position = UDim2.new(0, 4, 0, 4)
                img.BackgroundColor3 = Color3.fromRGB(30,30,34)
                img.BackgroundTransparency = 0.3
                img.Image = "rbxthumb://type=Asset&id=" .. tostring(item.id) .. "&w=150&h=150"
                img.Parent = card
                Instance.new("UICorner", img).CornerRadius = UDim.new(0, 6)
                local lbl = Instance.new("TextLabel")
                lbl.Size = UDim2.new(1, -4, 0, 28)
                lbl.Position = UDim2.new(0, 2, 1, -30)
                lbl.BackgroundTransparency = 1
                lbl.Text = item.name
                lbl.TextColor3 = Color3.fromRGB(220,220,230)
                lbl.Font = Enum.Font.Gotham
                lbl.TextSize = 10
                lbl.TextWrapped = true
                lbl.Parent = card
                card.MouseButton1Click:Connect(function()
                    wearAsset(item.id)
                end)
                card.Parent = scroll
            end

            local tiny = Instance.new("TextButton")
            tiny.BackgroundColor3 = Color3.fromRGB(50, 30, 70)
            tiny.Text = ""
            tiny.LayoutOrder = 999
            Instance.new("UICorner", tiny).CornerRadius = UDim.new(0, 8)
            local tl = Instance.new("TextLabel")
            tl.Size = UDim2.new(1, -4, 1, -4)
            tl.Position = UDim2.new(0, 2, 0, 2)
            tl.BackgroundTransparency = 1
            tl.Text = "Body Tiny\nS15 Thin"
            tl.TextColor3 = Color3.fromRGB(255,200,255)
            tl.Font = Enum.Font.GothamBold
            tl.TextSize = 12
            tl.TextWrapped = true
            tl.Parent = tiny
            tiny.MouseButton1Click:Connect(function()
                F.applyBodyTiny()
            end)
            tiny.Parent = scroll
        end
    end
    __initCustomGearsXray()



    -- ===== Hitbox ESP + External Lagger + Ultra FPS =====
    local function __initHitboxLaggerUltra()
        local hitboxFolder = nil
        local hitboxConns = {}
        local function ensureFolder()
            if hitboxFolder and hitboxFolder.Parent then return hitboxFolder end
            local f = workspace:FindFirstChild("Hub073HitboxESP")
            if f then f:Destroy() end
            f = Instance.new("Folder")
            f.Name = "Hub073HitboxESP"
            f.Parent = workspace
            hitboxFolder = f
            return f
        end
        local function clearHitbox()
            if hitboxFolder then pcall(function() hitboxFolder:ClearAllChildren() end) end
            for _, cn in ipairs(hitboxConns) do pcall(function() cn:Disconnect() end) end
            hitboxConns = {}
        end
        local function makeForChar(plr, char)
            if not char or plr == LP then return end
            local folder = ensureFolder()
            local root = char:FindFirstChild("HumanoidRootPart") or char:FindFirstChild("Torso") or char:FindFirstChild("UpperTorso")
            if not root then return end
            local col = S.themeColor or Color3.fromRGB(0, 255, 120)
            -- thin box adornment
            local box = Instance.new("BoxHandleAdornment")
            box.Name = "HB_" .. plr.Name
            box.Adornee = root
            box.AlwaysOnTop = true
            box.ZIndex = 5
            box.Size = root.Size + Vector3.new(0.05, 0.05, 0.05)
            box.Transparency = 0.85
            box.Color3 = col
            box.Parent = folder
            -- outline highlight (body)
            local hl = Instance.new("Highlight")
            hl.Name = "HL_" .. plr.Name
            hl.Adornee = char
            hl.DepthMode = Enum.HighlightDepthMode.AlwaysOnTop
            hl.FillTransparency = 0.92
            hl.OutlineTransparency = 0
            hl.OutlineColor = col
            hl.FillColor = col
            hl.Parent = folder
        end
        local function refreshAll()
            clearHitbox()
            if not S.espHitboxEnabled then return end
            ensureFolder()
            for _, plr in ipairs(Players:GetPlayers()) do
                if plr ~= LP and plr.Character then
                    pcall(makeForChar, plr, plr.Character)
                end
            end
            hitboxConns[#hitboxConns+1] = Players.PlayerAdded:Connect(function(plr)
                hitboxConns[#hitboxConns+1] = plr.CharacterAdded:Connect(function(char)
                    if S.espHitboxEnabled then
                        task.wait(0.3)
                        pcall(makeForChar, plr, char)
                    end
                end)
            end)
            for _, plr in ipairs(Players:GetPlayers()) do
                hitboxConns[#hitboxConns+1] = plr.CharacterAdded:Connect(function(char)
                    if S.espHitboxEnabled then
                        task.wait(0.3)
                        pcall(makeForChar, plr, char)
                    end
                end)
            end
        end
        F.startHitboxESP = function()
            S.espHitboxEnabled = true
            refreshAll()
        end
        F.stopHitboxESP = function()
            S.espHitboxEnabled = false
            clearHitbox()
            if hitboxFolder then pcall(function() hitboxFolder:Destroy() end) hitboxFolder = nil end
        end

        -- External 073 Lagger panel (toggle)
        local laggerState = { gui = nil, active = false, nivel = "V1", keybind = "M", loop = nil, input = nil }
        local VERSIONS = {
            V1={powers={25},mode="continuous"}, V2={powers={32},mode="continuous"},
            V3={powers={35},mode="continuous"}, V4={powers={70},mode="flicker",interval=0.1},
            V5={powers={70,35},mode="flicker",interval=0.2}, V6={powers={70},mode="flicker",interval=0.1},
            V7={powers={35,35},mode="flicker",interval=0.3}, V8={powers={35,35},mode="flicker",interval=0.3},
            V9={powers={70},mode="flicker",interval=0.2}, V10={powers={70,70},mode="flicker",interval=0.2},
        }
        local function bomb(poder)
            local main, spam = {}, {{}}
            local z = spam[1]
            for i = 1, 25 do local t = {}; table.insert(z, t); z = t end
            local max = math.min(12000, poder * 50)
            for i = 1, max do table.insert(main, spam) end
            pcall(function()
                game:GetService("RobloxReplicatedStorage").SetPlayerBlockList:FireServer(main)
            end)
        end
        local function runLoop()
            while laggerState.active do
                pcall(function() game:GetService("NetworkClient"):SetOutgoingKBPSLimit(80000) end)
                local config = VERSIONS[laggerState.nivel] or VERSIONS.V1
                if config.mode == "continuous" then
                    bomb(config.powers[1])
                else
                    for _, poder in ipairs(config.powers) do bomb(poder) end
                    task.wait(config.interval or 0.2)
                end
                task.wait(0.18)
            end
        end
        F.openLaggerPanel = function()
            -- Full 073 Lagger script
            pcall(function()
                local parent = LP:FindFirstChild("PlayerGui")
                if type(gethui) == "function" then parent = gethui() or parent end
                if parent then
                    local old = parent:FindFirstChild("073LaggerGui")
                    if old then old:Destroy() end
                end
            end)
            local src = [=[

local Players = game:GetService("Players")
local UserInputService = game:GetService("UserInputService")
local TweenService = game:GetService("TweenService")
local RunService = game:GetService("RunService")
local HttpService = game:GetService("HttpService")
local player = Players.LocalPlayer
local CONFIG_NAME = "073Lagger_config.json"
local BG_URLS = {
    "https://kastor-box.lovable.app/api/public/f/cwoz8t1w.png",
    "https://kastor-box.lovable.app/api/public/f/1k2mwnmn.png",
    "https://kastor-box.lovable.app/api/public/f/x9xw9hj8.png",
    "https://kastor-box.lovable.app/api/public/f/1oa7e02q.png",
    "https://kastor-box.lovable.app/api/public/f/hjx842w4.png",
    "https://kastor-box.lovable.app/api/public/f/5iddb4lx.png",
}
local BG_CACHE = {}
local PING_C = {
    bg = Color3.fromRGB(0, 0, 0), panel = Color3.fromRGB(12, 12, 12), card = Color3.fromRGB(22, 22, 22),
    red1 = Color3.fromRGB(45, 45, 45), red2 = Color3.fromRGB(95, 95, 95), red3 = Color3.fromRGB(235, 235, 235),
    glow = Color3.fromRGB(160, 160, 160), white = Color3.fromRGB(255, 255, 255), dim = Color3.fromRGB(190, 190, 190),
    muted = Color3.fromRGB(120, 120, 120), selected = Color3.fromRGB(245, 245, 245), success = Color3.fromRGB(0, 255, 120),
}
local PING_T = { bg = 0.30, card = 0.22, header = 0.10 }
local VERSIONS = {
    V1 = { powers = {25}, mode = "continuous" },
    V2 = { powers = {32}, mode = "continuous" },
    V3 = { powers = {35}, mode = "continuous" },
    V4 = { powers = {70}, mode = "flicker", interval = 0.1 },
    V5 = { powers = {70, 35}, mode = "flicker", interval = 0.2 },
    V6 = { powers = {70}, mode = "flicker", interval = 0.1 },
    V7 = { powers = {35, 35}, mode = "flicker", interval = 0.3 },
    V8 = { powers = {35, 35}, mode = "flicker", interval = 0.3 },
    V9 = { powers = {70}, mode = "flicker", interval = 0.2 },
    V10 = { powers = {70, 70}, mode = "flicker", interval = 0.2 },
}
local State = { Gui=nil, Main=nil, Content=nil, Active=false, Running=false, Minimized=false, Nivel="V1", Keybind="M", Background=0, RefreshVisual=nil, InputConn=nil, BindButton=nil, WaitingBind=false }
local function SaveConfig()
    pcall(function()
        writefile(CONFIG_NAME, HttpService:JSONEncode({Nivel=State.Nivel, Keybind=State.Keybind, Minimized=State.Minimized, Background=State.Background}))
    end)
end
local function LoadConfig()
    if not isfile or not isfile(CONFIG_NAME) then return end
    pcall(function()
        local data = HttpService:JSONDecode(readfile(CONFIG_NAME))
        if data.Nivel then State.Nivel = data.Nivel end
        if data.Keybind then State.Keybind = data.Keybind end
        if data.Minimized ~= nil then State.Minimized = data.Minimized end
        if data.Background ~= nil then State.Background = data.Background end
    end)
end
LoadConfig()
local function bomb(poder)
    local main, spam = {}, {{}}
    local z = spam[1]
    for i = 1, 25 do local t = {}; table.insert(z, t); z = t end
    for i = 1, math.min(12000, poder * 50) do table.insert(main, spam) end
    pcall(function() game:GetService("RobloxReplicatedStorage").SetPlayerBlockList:FireServer(main) end)
end
local function runLaggerLoop()
    while State.Active do
        pcall(function() game:GetService("NetworkClient"):SetOutgoingKBPSLimit(80000) end)
        local config = VERSIONS[State.Nivel]
        if config then
            if config.mode == "continuous" then bomb(config.powers[1])
            else for _, poder in ipairs(config.powers) do bomb(poder) end; task.wait(config.interval) end
        end
        task.wait(0.18)
    end
end
local function toggleLagger()
    State.Active = not State.Active
    if State.Active then task.spawn(runLaggerLoop) end
    if State.RefreshVisual then State.RefreshVisual() end
    SaveConfig()
end
local function setNivel(nivel)
    if VERSIONS[nivel] then State.Nivel = nivel; SaveConfig(); if State.RefreshVisual then State.RefreshVisual() end end
end
local function updateBindButton()
    if not (State.BindButton and State.BindButton.Parent) then return end
    State.BindButton.Text = State.WaitingBind and "[...]" or tostring(State.Keybind or "None")
end
local function requestBind()
    State.WaitingBind = not State.WaitingBind
    updateBindButton()
end
local function resolveBgAsset(index)
    if BG_CACHE[index] then return BG_CACHE[index] end
    local url = BG_URLS[index]; if not url then return nil end
    local fileName = "073LaggerBG_" .. tostring(index) .. ".png"
    local asset = nil
    pcall(function()
        if writefile and (game.HttpGet or HttpGet) then
            local need = true
            pcall(function() if isfile and isfile(fileName) then need = false end end)
            if need then
                local data = (game.HttpGet and game:HttpGet(url)) or HttpGet(url)
                if data then writefile(fileName, data) end
            end
        end
    end)
    pcall(function() if getcustomasset and isfile and isfile(fileName) then asset = getcustomasset(fileName) end end)
    asset = asset or url
    BG_CACHE[index] = asset
    return asset
end
local function applyBackground(index)
    index = math.clamp(tonumber(index) or 0, 0, #BG_URLS)
    State.Background = index
    if State.BgImage then
        if index == 0 then
            State.BgImage.Image = ""; State.BgImage.Visible = false
            if State.Main then State.Main.BackgroundTransparency = PING_T.bg end
        else
            local asset = resolveBgAsset(index)
            State.BgImage.Image = asset or ""; State.BgImage.Visible = asset ~= nil and asset ~= ""
            if State.Main then State.Main.BackgroundTransparency = State.BgImage.Visible and 0.15 or PING_T.bg end
        end
    end
    SaveConfig()
end
local function BuildUI()
    if State.Gui then pcall(function() State.Gui:Destroy() end) end
    if State.InputConn then pcall(function() State.InputConn:Disconnect() end) end
    local parent = player:FindFirstChild("PlayerGui") or player:WaitForChild("PlayerGui")
    pcall(function() if gethui then parent = gethui() end end)
    for _, child in ipairs(parent:GetChildren()) do
        if child.Name == "073LaggerGui" then pcall(function() child:Destroy() end) end
    end
    local screen = Instance.new("ScreenGui")
    screen.Name = "073LaggerGui"; screen.ResetOnSpawn = false; screen.DisplayOrder = 25; screen.IgnoreGuiInset = true; screen.Parent = parent
    State.Gui = screen
    local mainFrame = Instance.new("Frame")
    mainFrame.Name = "MainFrame"; mainFrame.Size = UDim2.new(0, 280, 0, 360); mainFrame.Position = UDim2.new(0.5, -140, 0.25, 0)
    mainFrame.BackgroundColor3 = PING_C.bg; mainFrame.BackgroundTransparency = PING_T.bg; mainFrame.BorderSizePixel = 0
    mainFrame.Active = true; mainFrame.ClipsDescendants = true; mainFrame.Parent = screen
    Instance.new("UICorner", mainFrame).CornerRadius = UDim.new(0, 10)
    local mainStroke = Instance.new("UIStroke"); mainStroke.Color = PING_C.red1; mainStroke.Thickness = 1.5; mainStroke.Transparency = 0.35; mainStroke.Parent = mainFrame
    State.Main = mainFrame
    local bgImage = Instance.new("ImageLabel")
    bgImage.Name = "Background"; bgImage.BackgroundTransparency = 1; bgImage.ImageTransparency = 0.12; bgImage.ScaleType = Enum.ScaleType.Crop
    bgImage.Size = UDim2.new(1,0,1,0); bgImage.Visible = false; bgImage.ZIndex = 1; bgImage.Parent = mainFrame
    Instance.new("UICorner", bgImage).CornerRadius = UDim.new(0, 10)
    State.BgImage = bgImage
    task.spawn(function()
        for i = 1, #BG_URLS do pcall(resolveBgAsset, i); task.wait(0.05) end
        applyBackground(State.Background or 0)
    end)
    local dragEnabled, dragStart, startPos = false, nil, nil
    mainFrame.InputBegan:Connect(function(input)
        if input.UserInputType == Enum.UserInputType.MouseButton1 or input.UserInputType == Enum.UserInputType.Touch then
            dragEnabled = true; dragStart = input.Position; startPos = mainFrame.Position
            input.Changed:Connect(function() if input.UserInputState == Enum.UserInputState.End then dragEnabled = false end end)
        end
    end)
    mainFrame.InputChanged:Connect(function(input)
        if not dragEnabled then return end
        if input.UserInputType == Enum.UserInputType.MouseMovement or input.UserInputType == Enum.UserInputType.Touch then
            local delta = input.Position - dragStart
            mainFrame.Position = UDim2.new(startPos.X.Scale, startPos.X.Offset + delta.X, startPos.Y.Scale, startPos.Y.Offset + delta.Y)
        end
    end)
    local topBar = Instance.new("Frame"); topBar.Size = UDim2.new(1,0,0,28); topBar.BackgroundTransparency = 1; topBar.ZIndex = 10; topBar.Parent = mainFrame
    local title = Instance.new("TextLabel"); title.Size = UDim2.new(1,0,1,0); title.BackgroundTransparency = 1; title.Text = "073 LAGGER"
    title.TextColor3 = Color3.fromRGB(210,210,215); title.Font = Enum.Font.GothamBlack; title.TextSize = 14; title.ZIndex = 12; title.Parent = topBar
    local content = Instance.new("Frame"); content.Size = UDim2.new(1,0,1,-28); content.Position = UDim2.new(0,0,0,28); content.BackgroundTransparency = 1; content.ZIndex = 5; content.Parent = mainFrame
    local activateButton = Instance.new("TextButton")
    activateButton.Size = UDim2.new(0.7,-8,0,30); activateButton.Position = UDim2.new(0,8,0,8); activateButton.BackgroundColor3 = PING_C.card
    activateButton.BackgroundTransparency = PING_T.card; activateButton.BorderSizePixel = 0; activateButton.AutoButtonColor = false; activateButton.Text = ""; activateButton.ZIndex = 6; activateButton.Parent = content
    Instance.new("UICorner", activateButton).CornerRadius = UDim.new(0, 8)
    local activateGradient = Instance.new("UIGradient")
    activateGradient.Color = ColorSequence.new({ColorSequenceKeypoint.new(0, PING_C.red1), ColorSequenceKeypoint.new(1, PING_C.red2)})
    activateGradient.Rotation = 135; activateGradient.Parent = activateButton
    local activateLabel = Instance.new("TextLabel"); activateLabel.Size = UDim2.new(1,0,1,0); activateLabel.BackgroundTransparency = 1; activateLabel.Text = "Desactive"
    activateLabel.TextColor3 = PING_C.white; activateLabel.Font = Enum.Font.GothamBlack; activateLabel.TextSize = 11; activateLabel.ZIndex = 7; activateLabel.Parent = activateButton
    local statusLabel = Instance.new("TextLabel"); statusLabel.Size = UDim2.new(0.3,-8,0,30); statusLabel.Position = UDim2.new(0.7,8,0,8)
    statusLabel.BackgroundColor3 = PING_C.card; statusLabel.BackgroundTransparency = PING_T.card; statusLabel.Text = "Status: OFF"
    statusLabel.TextColor3 = Color3.fromRGB(150,150,150); statusLabel.Font = Enum.Font.GothamBold; statusLabel.TextSize = 10; statusLabel.ZIndex = 6; statusLabel.Parent = content
    Instance.new("UICorner", statusLabel).CornerRadius = UDim.new(0, 8)
    local presetTitle = Instance.new("TextLabel"); presetTitle.Size = UDim2.new(1,-16,0,14); presetTitle.Position = UDim2.new(0,8,0,44); presetTitle.BackgroundTransparency = 1
    presetTitle.Text = "SELECT POWER"; presetTitle.TextColor3 = PING_C.dim; presetTitle.Font = Enum.Font.GothamBold; presetTitle.TextSize = 8; presetTitle.TextXAlignment = Enum.TextXAlignment.Left; presetTitle.ZIndex = 6; presetTitle.Parent = content
    local presetButtons = {}
    local function refreshPresetVisuals()
        for name, button in pairs(presetButtons) do
            local selected = State.Nivel == name
            button.BackgroundColor3 = selected and PING_C.selected or PING_C.card
            button.TextColor3 = selected and Color3.fromRGB(0,0,0) or PING_C.dim
            local stroke = button:FindFirstChildOfClass("UIStroke")
            if stroke then stroke.Color = selected and Color3.fromRGB(255,255,255) or PING_C.glow; stroke.Transparency = selected and 0 or 0.55 end
        end
    end
    for i, name in ipairs({"V1","V2","V3","V4","V5","V6","V7","V8","V9","V10"}) do
        local button = Instance.new("TextButton")
        button.Size = UDim2.new(0,52,0,22); button.Position = UDim2.new(0, 8+((i-1)%5)*54, 0, 60+math.floor((i-1)/5)*26)
        button.BackgroundColor3 = PING_C.card; button.BackgroundTransparency = PING_T.card; button.BorderSizePixel = 0; button.AutoButtonColor = false
        button.Text = name; button.TextColor3 = PING_C.dim; button.Font = Enum.Font.GothamBold; button.TextSize = 8; button.ZIndex = 6; button.Parent = content
        Instance.new("UICorner", button).CornerRadius = UDim.new(0, 6)
        local stroke = Instance.new("UIStroke"); stroke.Color = PING_C.glow; stroke.Thickness = 1; stroke.Transparency = 0.55; stroke.Parent = button
        button.MouseButton1Click:Connect(function() setNivel(name); refreshPresetVisuals() end)
        presetButtons[name] = button
    end
    refreshPresetVisuals()
    local bindTitle = Instance.new("TextLabel"); bindTitle.Size = UDim2.new(1,-16,0,14); bindTitle.Position = UDim2.new(0,8,0,116); bindTitle.BackgroundTransparency = 1
    bindTitle.Text = "KEYBIND"; bindTitle.TextColor3 = PING_C.dim; bindTitle.Font = Enum.Font.GothamBold; bindTitle.TextSize = 8; bindTitle.TextXAlignment = Enum.TextXAlignment.Left; bindTitle.ZIndex = 6; bindTitle.Parent = content
    local bindButton = Instance.new("TextButton"); bindButton.Size = UDim2.new(1,-16,0,24); bindButton.Position = UDim2.new(0,8,0,132)
    bindButton.BackgroundColor3 = PING_C.card; bindButton.BackgroundTransparency = PING_T.card; bindButton.BorderSizePixel = 0; bindButton.AutoButtonColor = false
    bindButton.TextColor3 = PING_C.white; bindButton.Font = Enum.Font.GothamBold; bindButton.TextSize = 10; bindButton.ZIndex = 6; bindButton.Parent = content
    Instance.new("UICorner", bindButton).CornerRadius = UDim.new(0, 6)
    local bindStroke = Instance.new("UIStroke"); bindStroke.Color = PING_C.glow; bindStroke.Thickness = 1; bindStroke.Transparency = 0.55; bindStroke.Parent = bindButton
    State.BindButton = bindButton; updateBindButton(); bindButton.MouseButton1Click:Connect(requestBind)
    local bgTitle = Instance.new("TextLabel"); bgTitle.Size = UDim2.new(1,-16,0,14); bgTitle.Position = UDim2.new(0,8,0,164); bgTitle.BackgroundTransparency = 1
    bgTitle.Text = "BACKGROUND"; bgTitle.TextColor3 = PING_C.dim; bgTitle.Font = Enum.Font.GothamBold; bgTitle.TextSize = 8; bgTitle.TextXAlignment = Enum.TextXAlignment.Left; bgTitle.ZIndex = 6; bgTitle.Parent = content
    local bgRow = Instance.new("ScrollingFrame"); bgRow.Size = UDim2.new(1,-16,0,40); bgRow.Position = UDim2.new(0,8,0,180)
    bgRow.BackgroundColor3 = Color3.fromRGB(14,14,16); bgRow.BackgroundTransparency = 0.05; bgRow.BorderSizePixel = 0; bgRow.ZIndex = 6
    bgRow.ScrollBarThickness = 0; bgRow.AutomaticCanvasSize = Enum.AutomaticSize.X; bgRow.ScrollingDirection = Enum.ScrollingDirection.X; bgRow.Parent = content
    Instance.new("UICorner", bgRow).CornerRadius = UDim.new(0, 8)
    local bgLayout = Instance.new("UIListLayout"); bgLayout.FillDirection = Enum.FillDirection.Horizontal; bgLayout.Padding = UDim.new(0,4); bgLayout.Parent = bgRow
    local bgButtons = {}
    local function refreshBgVisuals()
        for index, button in pairs(bgButtons) do
            local selected = State.Background == index
            local stroke = button:FindFirstChildOfClass("UIStroke")
            if stroke then stroke.Color = selected and Color3.fromRGB(255,255,255) or Color3.fromRGB(90,90,95); stroke.Thickness = selected and 2 or 1; stroke.Transparency = selected and 0 or 0.35 end
            button.BackgroundColor3 = selected and Color3.fromRGB(40,40,45) or Color3.fromRGB(22,22,26)
        end
    end
    local noneBtn = Instance.new("TextButton"); noneBtn.Size = UDim2.new(0,38,0,28); noneBtn.BackgroundColor3 = Color3.fromRGB(28,28,32); noneBtn.Text = "None"
    noneBtn.TextColor3 = Color3.fromRGB(230,230,235); noneBtn.Font = Enum.Font.GothamBold; noneBtn.TextSize = 8; noneBtn.ZIndex = 7; noneBtn.Parent = bgRow
    Instance.new("UICorner", noneBtn).CornerRadius = UDim.new(0, 7)
    local noneStroke = Instance.new("UIStroke"); noneStroke.Color = Color3.fromRGB(90,90,95); noneStroke.Parent = noneBtn
    noneBtn.MouseButton1Click:Connect(function() applyBackground(0); refreshBgVisuals() end); bgButtons[0] = noneBtn
    for i = 1, #BG_URLS do
        local holder = Instance.new("TextButton"); holder.Size = UDim2.new(0,38,0,28); holder.BackgroundColor3 = Color3.fromRGB(22,22,26); holder.Text = ""; holder.ClipsDescendants = true; holder.ZIndex = 7; holder.Parent = bgRow
        Instance.new("UICorner", holder).CornerRadius = UDim.new(0, 7)
        local holderStroke = Instance.new("UIStroke"); holderStroke.Color = Color3.fromRGB(90,90,95); holderStroke.Parent = holder
        local preview = Instance.new("ImageLabel"); preview.Size = UDim2.new(1,0,1,0); preview.BackgroundTransparency = 1; preview.ScaleType = Enum.ScaleType.Fit; preview.ZIndex = 8; preview.Parent = holder
        Instance.new("UICorner", preview).CornerRadius = UDim.new(0, 7)
        preview.Image = BG_URLS[i]
        task.spawn(function() local asset = resolveBgAsset(i); if preview and preview.Parent and asset then preview.Image = asset end end)
        holder.MouseButton1Click:Connect(function() applyBackground(i); refreshBgVisuals() end)
        bgButtons[i] = holder
    end
    applyBackground(State.Background or 0); refreshBgVisuals()
    local function refreshVisual()
        if State.Active then
            activateGradient.Color = ColorSequence.new({ColorSequenceKeypoint.new(0, Color3.fromRGB(18,18,18)), ColorSequenceKeypoint.new(1, Color3.fromRGB(48,48,48))})
            activateLabel.Text = "Active"; statusLabel.Text = "Status: ON"; statusLabel.TextColor3 = Color3.fromRGB(0,255,100)
        else
            activateGradient.Color = ColorSequence.new({ColorSequenceKeypoint.new(0, PING_C.red1), ColorSequenceKeypoint.new(1, PING_C.red2)})
            activateLabel.Text = "Desactive"; statusLabel.Text = "Status: OFF"; statusLabel.TextColor3 = Color3.fromRGB(150,150,150)
        end
    end
    State.RefreshVisual = refreshVisual; refreshVisual()
    activateButton.MouseButton1Click:Connect(function() toggleLagger() end)
    State.InputConn = UserInputService.InputBegan:Connect(function(input, gameProcessed)
        if gameProcessed then return end
        local keyCode = input.KeyCode
        if keyCode == Enum.KeyCode.Unknown then return end
        if State.WaitingBind then
            if keyCode == Enum.KeyCode.Escape then State.WaitingBind = false; updateBindButton(); return end
            if keyCode == Enum.KeyCode.Backspace or keyCode == Enum.KeyCode.Delete then State.Keybind = "None"; State.WaitingBind = false; updateBindButton(); SaveConfig(); return end
            if input.UserInputType == Enum.UserInputType.Keyboard then State.Keybind = keyCode.Name; State.WaitingBind = false; updateBindButton(); SaveConfig() end
            return
        end
        if keyCode.Name == State.Keybind then toggleLagger() end
    end)
    updateBindButton()
end
BuildUI()

]=]
            local fn, err = loadstring(src)
            if fn then
                pcall(fn)
            end
        end
        F.closeLaggerPanel = function()
            pcall(function()
                local function wipe(p)
                    if not p then return end
                    local g = p:FindFirstChild("073LaggerGui")
                    if g then g:Destroy() end
                end
                if type(gethui) == "function" then wipe(gethui()) end
                wipe(game:GetService("CoreGui"))
                wipe(LP:FindFirstChild("PlayerGui"))
            end)
        end
        F.setLaggerPanel = function(on)
            S.laggerPanelEnabled = on and true or false
            if on then F.openLaggerPanel() else F.closeLaggerPanel() end
        end

        -- Ultra FPS Boost
        local ultraState = { on = false, conns = {}, threads = {} }
        F.startUltraFpsBoost = function()
            if S._ultraFpsOn then return end
            S._ultraFpsOn = true
            S.ultraFpsBoost = true
            local function safeDestroy(obj)
                if not obj or obj.Name == "Overhead" then return end
                pcall(function() obj:Destroy() end)
            end
            local function isCharPart(obj)
                for _, plr in ipairs(Players:GetPlayers()) do
                    if plr.Character and obj:IsDescendantOf(plr.Character) then return true end
                end
                return false
            end
            local function clean(obj)
                pcall(function()
                    if obj:IsA("ParticleEmitter") or obj:IsA("Trail") or obj:IsA("Beam") or obj:IsA("Smoke") or obj:IsA("Fire") or obj:IsA("Sparkles") then
                        pcall(function() obj.Enabled = false end)
                        safeDestroy(obj)
                    elseif obj:IsA("Decal") or obj:IsA("Texture") then
                        if obj.Name ~= "face" then safeDestroy(obj) end
                    elseif obj:IsA("SurfaceAppearance") then
                        safeDestroy(obj)
                    elseif obj:IsA("PointLight") or obj:IsA("SpotLight") or obj:IsA("SurfaceLight") then
                        safeDestroy(obj)
                    elseif obj:IsA("BasePart") and not isCharPart(obj) then
                        obj.CastShadow = false
                        obj.Material = Enum.Material.Plastic
                        obj.Reflectance = 0
                    end
                end)
            end
            pcall(function()
                Lighting.GlobalShadows = false
                Lighting.FogEnd = 9e9
                Lighting.FogStart = 9e9
                Lighting.EnvironmentDiffuseScale = 0
                Lighting.EnvironmentSpecularScale = 0
                Lighting.Brightness = 1.5
                for _, v in ipairs(Lighting:GetChildren()) do
                    if v:IsA("BloomEffect") or v:IsA("BlurEffect") or v:IsA("ColorCorrectionEffect")
                        or v:IsA("SunRaysEffect") or v:IsA("DepthOfFieldEffect")
                        or v:IsA("Atmosphere") or v:IsA("Clouds") or v:IsA("Sky") then
                        pcall(function() v:Destroy() end)
                    end
                end
                local sky = Instance.new("Sky")
                sky.SkyboxBk = ""; sky.SkyboxDn = ""; sky.SkyboxFt = ""
                sky.SkyboxLf = ""; sky.SkyboxRt = ""; sky.SkyboxUp = ""
                sky.CelestialBodiesShown = false
                sky.Parent = Lighting
                settings().Rendering.QualityLevel = Enum.QualityLevel.Level01
                pcall(function() settings().Rendering.MeshPartDetailLevel = Enum.MeshPartDetailLevel.Level01 end)
                pcall(function()
                    workspace.Terrain.Decoration = false
                    workspace.Terrain.WaterWaveSize = 0
                    workspace.Terrain.WaterWaveSpeed = 0
                    workspace.Terrain.WaterReflectance = 0
                    workspace.Terrain.WaterTransparency = 1
                end)
            end)
            pcall(function() if setfpscap then setfpscap(999) end end)
            for _, obj in ipairs(workspace:GetDescendants()) do
                clean(obj)
            end
            if S._ultraDescConn then pcall(function() S._ultraDescConn:Disconnect() end) end
            S._ultraDescConn = workspace.DescendantAdded:Connect(function(obj)
                if S._ultraFpsOn then task.defer(function() clean(obj) end) end
            end)
            if S._ultraLightConn then pcall(function() S._ultraLightConn:Disconnect() end) end
            S._ultraLightConn = Lighting.DescendantAdded:Connect(function(obj)
                if not S._ultraFpsOn then return end
                if obj:IsA("Atmosphere") or obj:IsA("Clouds") or obj:IsA("PostEffect") then
                    safeDestroy(obj)
                end
            end)
        end
        F.stopUltraFpsBoost = function()
            S._ultraFpsOn = false
            S.ultraFpsBoost = false
            if S._ultraDescConn then pcall(function() S._ultraDescConn:Disconnect() end); S._ultraDescConn = nil end
            if S._ultraLightConn then pcall(function() S._ultraLightConn:Disconnect() end); S._ultraLightConn = nil end
        end
    end
    __initHitboxLaggerUltra()

function F.buildGui()
        S.uiLayoutStyle = "Old"
        local COLORS = {
            bg = Color3.fromRGB(0, 0, 0),
            row = Color3.fromRGB(12, 12, 12),
            row2 = Color3.fromRGB(20, 20, 20),
            stroke = Color3.fromRGB(40, 40, 40),
            strokeSoft = Color3.fromRGB(28, 28, 28),
            white = Color3.fromRGB(255, 255, 255),
            textDim = Color3.fromRGB(140, 140, 140),
            toggleBg = Color3.fromRGB(16, 16, 16),
            knob = Color3.fromRGB(255, 255, 255),
            accent = Color3.fromRGB(255, 255, 255),
            accentStrong = Color3.fromRGB(255, 255, 255),
            on = Color3.fromRGB(255, 255, 255),
            off = Color3.fromRGB(28, 28, 28),
        }

        local THEME_PRESETS = {
            { name = "BLUE",   color = Color3.fromRGB(58, 128, 245) },
            { name = "PURPLE", color = Color3.fromRGB(170, 80, 255) },
            { name = "PINK",   color = Color3.fromRGB(255, 90, 200) },
            { name = "YELLOW", color = Color3.fromRGB(255, 214, 0) },
            { name = "WHITE",  color = Color3.fromRGB(245, 245, 247) },
            { name = "GREEN",  color = Color3.fromRGB(60, 200, 120) },
            { name = "RED",    color = Color3.fromRGB(232, 52, 68) },
            { name = "CYAN",   color = Color3.fromRGB(80, 220, 255) },
        }

        local BG_PRESETS = {
            { name = "None",  id = "" },
            { name = "Image 1",  id = "https://kastor-box.lovable.app/api/public/f/1oa7e02q.png" },
            { name = "Image 2",  id = "https://kastor-box.lovable.app/api/public/f/x9xw9hj8.png" },
            { name = "Image 3",  id = "https://kastor-box.lovable.app/api/public/f/1k2mwnmn.png" },
            { name = "Image 4",  id = "https://kastor-box.lovable.app/api/public/f/cwoz8t1w.png" },
        }
        local _bgCache = {}
        local function httpGetBytes(url)
            local body = nil
            pcall(function()
                if game and game.HttpGet then body = game:HttpGet(url) end
            end)
            if body then return body end
            pcall(function()
                if HS and HS.GetAsync then body = HS:GetAsync(url) end
            end)
            if body then return body end
            for _, req in ipairs({
                function() return syn and syn.request end,
                function() return http_request end,
                function() return request end,
                function() return fluxus and fluxus.request end,
            }) do
                local ok, fn = pcall(req)
                if ok and fn then
                    local ok2, res = pcall(fn, { Url = url, Method = "GET" })
                    if ok2 and res then
                        if type(res) == "table" and res.Body then return res.Body end
                        if type(res) == "string" then return res end
                    end
                end
            end
            return nil
        end

        local function toAssetImage(src)
            if not src or src == "" then return "" end
            if src:find("rbxasset", 1, true) or src:find("rbxthumb", 1, true) then
                return src
            end
            if _bgCache[src] then return _bgCache[src] end
            -- Download http(s) image and load via getcustomasset (executors)
            if src:find("http", 1, true) then
                local hash = tostring(string.len(src))
                for i = 1, math.min(#src, 48) do
                    hash = hash .. string.byte(src, i)
                end
                local fileName = "Hub073_BG_" .. hash .. ".png"
                local asset = nil
                pcall(function()
                    if isfile and isfile(fileName) and getcustomasset then
                        asset = getcustomasset(fileName)
                    end
                end)
                if not asset then
                    local data = httpGetBytes(src)
                    if data and #data > 100 then
                        pcall(function()
                            if writefile then writefile(fileName, data) end
                        end)
                        pcall(function()
                            if getcustomasset and isfile and isfile(fileName) then
                                asset = getcustomasset(fileName)
                            end
                        end)
                    end
                end
                if asset and asset ~= "" then
                    _bgCache[src] = asset
                    return asset
                end
                -- last resort: raw url (some executors accept it)
                _bgCache[src] = src
                return src
            end
            return src
        end

        local function resolveBgId(key)
            if not key or key == "" or key == "None" then return "" end
            if type(key)=="string" and (key:find("rbxasset", 1, true) or key:find("rbxthumb", 1, true) or key:find("http", 1, true)) then
                return key
            end
            for _, preset in ipairs(BG_PRESETS) do
                if preset.name == key then return preset.id end
            end
            return ""
        end
        local currentThemeName = "WHITE"
        S.currentThemeName = currentThemeName
        S.themeColor = Color3.fromRGB(245, 245, 247)
        local currentBgId = ""
        S.currentBgId = ""
        S.bgIntensity = S.bgIntensity or 0.72
        local bgAsset, bgVeil
        local themeAccentStrokes = {}
        local themeAccentFrames = {}

        local function applyBackground(imageId, skipSave)
            currentBgId = imageId or ""
            S.currentBgId = currentBgId
            local resolved = resolveBgId(currentBgId)
            if not bgAsset or not bgAsset.Parent then
                pcall(function()
                    if bgAsset then bgAsset:Destroy() end
                end)
                bgAsset = Instance.new("ImageLabel")
                bgAsset.Name = "BackgroundAsset"
                bgAsset.Size = UDim2.new(1, 0, 1, 0)
                bgAsset.BackgroundTransparency = 1
                bgAsset.ScaleType = Enum.ScaleType.Crop
                bgAsset.ZIndex = 0
                bgAsset.Parent = main
                pcall(function() corner(bgAsset, 16) end)
            end
            if not bgVeil or not bgVeil.Parent then
                pcall(function()
                    if bgVeil then bgVeil:Destroy() end
                end)
                bgVeil = Instance.new("Frame")
                bgVeil.Name = "BackgroundVeil"
                bgVeil.Size = UDim2.new(1, 0, 1, 0)
                bgVeil.BackgroundColor3 = Color3.fromRGB(0, 0, 0)
                bgVeil.BorderSizePixel = 0
                bgVeil.ZIndex = 1
                bgVeil.Parent = main
                pcall(function() corner(bgVeil, 16) end)
            end
            if resolved == "" then
                bgAsset.Image = ""
                bgAsset.ImageTransparency = 1
                bgAsset.Visible = false
                bgVeil.BackgroundTransparency = 0.55
                bgVeil.Visible = true
                if main then
                    main.BackgroundColor3 = Color3.fromRGB(0, 0, 0)
                    main.BackgroundTransparency = 0
                end
            else
                local assetImg = toAssetImage(resolved)
                bgAsset.Image = assetImg or resolved
                local intens = tonumber(S.bgIntensity) or 0.72
                bgAsset.ImageTransparency = math.clamp(1 - intens, 0, 0.75)
                bgAsset.ImageColor3 = Color3.new(1, 1, 1)
                bgAsset.ScaleType = Enum.ScaleType.Crop
                bgAsset.Visible = true
                bgAsset.ZIndex = 0
                -- keep veil light so image shows through content
                bgVeil.BackgroundTransparency = 0.55
                bgVeil.Visible = true
                bgVeil.ZIndex = 1
                if main then
                    main.BackgroundColor3 = Color3.fromRGB(0, 0, 0)
                    main.BackgroundTransparency = 0.25
                end
                -- force content rows slightly more transparent so BG is visible
                pcall(function()
                    for _, d in ipairs(main:GetDescendants()) do
                        if d:IsA("Frame") and d.Name == "Frame" and d.BackgroundTransparency < 0.35 then
                            -- leave interactive rows as-is
                        end
                    end
                end)
            end
            if UI and UI.bgPresetBtn then
                local label = "None"
                for _, p in ipairs(BG_PRESETS) do
                    if p.id == resolved or p.name == currentBgId or p.id == currentBgId then
                        label = p.name
                        break
                    end
                end
                if resolved ~= "" and label == "None" then label = "Custom" end
                UI.bgPresetBtn.Text = label
            end
            if not skipSave then pcall(function() if F and F.saveConfig then F.saveConfig() end end) end
        end

        _G._Hub073ApplyBackground = function(imageId, skipSave)
            applyBackground(imageId, skipSave)
        end

        local function applyThemeColor(col, name)
            if name then currentThemeName = name; S.currentThemeName = name end
            S.themeColor = col
            COLORS.stroke = col
            COLORS.accent = col
            COLORS.on = col
            COLORS.accentStrong = col
            COLORS.strokeSoft = Color3.new(col.R * 0.45 + 0.12, col.G * 0.45 + 0.12, col.B * 0.45 + 0.12)
            -- Text / fonts stay white
            COLORS.white = Color3.fromRGB(255, 255, 255)
            COLORS.textDim = Color3.fromRGB(200, 200, 200)
            -- Sync ESP / hitbox to theme color
            S.espThemeColor = col
            pcall(function()
                if K7 then
                    K7.espColor = col
                    if K7.refreshESP then K7.refreshESP() end
                    if K7.setESPColor then K7.setESPColor(col) end
                end
            end)
            pcall(function()
                if F.applyEspHitbox then F.applyEspHitbox() end
            end)
            pcall(function()
                if F.applyEspTheme then F.applyEspTheme(col) end
            end)
            pcall(function()
                if F.startHitboxESP and S.espHitboxEnabled then
                    F.stopHitboxESP()
                    F.startHitboxESP()
                end
            end)
            for _, s in ipairs(themeAccentStrokes) do
                if s and s.Parent then pcall(function() s.Color = col end) end
            end
            if main then
                for _, d in ipairs(main:GetDescendants()) do
                    if d:IsA("TextLabel") or d:IsA("TextButton") or d:IsA("TextBox") then
                        -- keep readable white fonts
                        if d.TextTransparency < 1 then
                            local n = (d.Name or ""):lower()
                            if not n:find("dim") then
                                pcall(function() d.TextColor3 = Color3.fromRGB(255,255,255) end)
                            end
                        end
                    end
                end
            end
            if S._mainTryhardBorder and S._mainTryhardBorder.Parent then
                pcall(function() S._mainTryhardBorder.Color = col end)
            end
            if main then
                for _, d in ipairs(main:GetDescendants()) do
                    if d:IsA("UIStroke") then
                        if d:GetAttribute("ThemeAccent") or d:GetAttribute("ThemeStroke") then
                            d.Color = col
                        end
                    elseif d:IsA("TextButton") then
                        local isHubBtn = d:GetAttribute("ThemeButton")
                            or d.Name == "KeybindButton"
                            or d.Name:find("Btn")
                            or d.Name:find("Button")
                            or d.Name == "PickerPrev"
                            or d.Name == "PickerNext"
                            or d.Name == "PickerValue"
                        if isHubBtn and d.BackgroundTransparency < 1 then
                            d.BackgroundColor3 = col
                            d.BackgroundTransparency = math.max(d.BackgroundTransparency, 0.5)
                            local st = d:FindFirstChildOfClass("UIStroke")
                            if st then st.Color = col end
                        end
                    elseif d:IsA("TextBox") then
                        if d.BackgroundTransparency < 1 then
                            local st = d:FindFirstChildOfClass("UIStroke")
                            if st then st.Color = col end
                        end
                    elseif d:IsA("Frame") then
                        if d:GetAttribute("ThemePillOn") then
                            d.BackgroundColor3 = col
                            d.BackgroundTransparency = 0.22
                        end
                    end
                end
            end
            pcall(function()
                if _G._Hub073ApplyMobileTheme then
                    _G._Hub073ApplyMobileTheme(col)
                end
            end)

            pcall(function()
                if F and F.applyEspTheme then
                    F.applyEspTheme(col)
                end
            end)
        end

        _G._Hub073ApplyTheme = function(themeName)
            for _, preset in ipairs(THEME_PRESETS) do
                if preset.name == themeName then
                    applyThemeColor(preset.color, preset.name)
                    return
                end
            end
        end
        local function corner(parent, radius)
            local c = Instance.new("UICorner")
            c.CornerRadius = UDim.new(0, radius or 8)
            c.Parent = parent
            return c
        end
        local function stroke(parent, color, thickness, transparency)
            local s = Instance.new("UIStroke")
            s.ApplyStrokeMode = Enum.ApplyStrokeMode.Border
            s.Color = color or COLORS.stroke
            s.Thickness = thickness or 1
            s.Transparency = transparency or 0.55
            s.Parent = parent
            if color == nil or color == COLORS.stroke or color == COLORS.accent then
                s:SetAttribute("ThemeAccent", true)
                table.insert(themeAccentStrokes, s)
            end
            return s
        end

        for _,name in ipairs({"Hub073","Hub073"}) do
            local old = game:GetService("CoreGui"):FindFirstChild(name)
            if old then old:Destroy() end
            local pg = LP:FindFirstChild("PlayerGui")
            if pg then local o = pg:FindFirstChild(name); if o then o:Destroy() end end
        end

        local gui = Instance.new("ScreenGui")
        gui.Name = "Hub073"
        gui.ResetOnSpawn = false
        gui.DisplayOrder = 10
        gui.IgnoreGuiInset = true
        gui.ZIndexBehavior = Enum.ZIndexBehavior.Sibling
        pcall(function()
            if type(syn) == "table" and type(syn.protect_gui) == "function" then
                syn.protect_gui(gui)
            elseif type(protect_gui) == "function" then
                protect_gui(gui)
            elseif type(protectgui) == "function" then
                protectgui(gui)
            end
        end)

        local parented = false
        pcall(function()
            if type(gethui) == "function" then
                gui.Parent = gethui()
                parented = gui.Parent ~= nil
            end
        end)
        if not parented then
            pcall(function()
                local pg = LP:FindFirstChild("PlayerGui") or LP:WaitForChild("PlayerGui", 8)
                if pg then
                    gui.Parent = pg
                    parented = gui.Parent ~= nil
                end
            end)
        end
        if not parented then
            pcall(function()
                gui.Parent = game:GetService("CoreGui")
                parented = gui.Parent ~= nil
            end)
        end
        if not parented then
            --warn("[073 Hub] Failed to parent ScreenGui")
        end

        local FULL_SIZE = UDim2.new(0, 720, 0, 560)
        local main = Instance.new("Frame")
        main.Name = "Main"
        main.AnchorPoint = Vector2.new(0, 0.5)
        main.Size = FULL_SIZE
        main.Position = UDim2.new(0, 20, 0.5, 0)

        do
            local isMob = false
            pcall(function()
                isMob = UIS.TouchEnabled and not UIS.KeyboardEnabled
            end)
            local cam = workspace.CurrentCamera
            local vp = cam and cam.ViewportSize or Vector2.new(1280, 720)
            if vp.X < 700 or vp.Y < 500 then isMob = true end
            if isMob then
                main.AnchorPoint = Vector2.new(0.5, 0.5)
                main.Position = UDim2.new(0.5, 0, 0.5, 0)
                local sc = Instance.new("UIScale")
                sc.Name = "MobileHubScale"
                sc.Scale = math.clamp(math.min(vp.X / 400, vp.Y / 620), 0.72, 0.92)
                sc.Parent = main
            end
        end
        main.BackgroundColor3 = COLORS.bg
        main.BackgroundTransparency = 0
        main.BorderSizePixel = 0
        main.Active = true
        main.ClipsDescendants = true
        main.Parent = gui
        S._hubMainFrame = main
        corner(main, 16)

        local mainBorder = stroke(main, COLORS.stroke, 2.2, 0.15)
        mainBorder.Name = "TryhardBorder"
        mainBorder:SetAttribute("ThemeAccent", true)
        mainBorder:SetAttribute("TryhardBorder", true)
        S._mainTryhardBorder = mainBorder
        task.spawn(function()
            local t0 = tick()
            while main and main.Parent and mainBorder and mainBorder.Parent do
                local t = tick() - t0

                local pulse = 0.5 + 0.5 * math.sin(t * 3.2)
                local pulse2 = 0.5 + 0.5 * math.sin(t * 5.1 + 1.2)
                local col = S.themeColor or COLORS.stroke

                local boost = 0.12 * pulse2
                pcall(function()
                    mainBorder.Color = Color3.new(
                        math.clamp(col.R + boost, 0, 1),
                        math.clamp(col.G + boost, 0, 1),
                        math.clamp(col.B + boost, 0, 1)
                    )
                    mainBorder.Thickness = 1.6 + pulse * 1.8
                    mainBorder.Transparency = 0.08 + (1 - pulse) * 0.35
                end)
                task.wait(0.03)
            end
        end)

        bgAsset = Instance.new("ImageLabel")
        bgAsset.Name = "BackgroundAsset"
        bgAsset.Size = UDim2.new(1, 0, 1, 0)
        bgAsset.BackgroundTransparency = 1
        bgAsset.Image = ""
        bgAsset.ImageTransparency = 1
        bgAsset.ScaleType = Enum.ScaleType.Crop
        bgAsset.ZIndex = 0
        bgAsset.Parent = main
        corner(bgAsset, 16)

        bgVeil = Instance.new("Frame")
        bgVeil.Name = "BackgroundVeil"
        bgVeil.Size = UDim2.new(1, 0, 1, 0)
        bgVeil.BackgroundColor3 = Color3.fromRGB(0, 0, 0)
        bgVeil.BackgroundTransparency = 0.55
        bgVeil.BorderSizePixel = 0
        bgVeil.ZIndex = 1
        bgVeil.Parent = main
        corner(bgVeil, 16)

        local guiScale = 1.0
        local uiScaleObj = Instance.new("UIScale")
        uiScaleObj.Scale = guiScale
        uiScaleObj.Parent = main
        local function applyGuiScale(v)
            v = math.clamp(tonumber(v) or 1, 0.6, 1.6)
            guiScale = math.floor(v * 100 + 0.5) / 100
            uiScaleObj.Scale = guiScale
            if UI.guiScaleLbl then
                UI.guiScaleLbl.Text = string.format("%.0f%%", guiScale * 100)
            end
            pcall(F.saveConfig)
            return guiScale
        end
        _G._Hub073ApplyGuiScale = applyGuiScale
        _G._Hub073GetGuiScale = function() return guiScale end

        do
            local dn, ds, sp, di = false
            main.InputBegan:Connect(function(i)
                if i.UserInputType == Enum.UserInputType.MouseButton1 or i.UserInputType == Enum.UserInputType.Touch then
                    dn = true; ds = i.Position; sp = main.Position
                    i.Changed:Connect(function() if i.UserInputState == Enum.UserInputState.End then dn = false end end)
                end
            end)
            main.InputChanged:Connect(function(i)
                if i.UserInputType == Enum.UserInputType.MouseMovement or i.UserInputType == Enum.UserInputType.Touch then di = i end
            end)
            UIS.InputChanged:Connect(function(i)
                if i == di and dn then
                    main.Position = UDim2.new(sp.X.Scale, sp.X.Offset + (i.Position.X - ds.X), sp.Y.Scale, sp.Y.Offset + (i.Position.Y - ds.Y))
                end
            end)
        end

        local miniBtn = Instance.new("TextButton")
        miniBtn.Size = UDim2.new(0, 110, 0, 32)
        miniBtn.Position = UDim2.new(0, 20, 0, 20)
        miniBtn.BackgroundColor3 = Color3.fromRGB(0, 0, 0)
        miniBtn.BackgroundTransparency = 0
        miniBtn.BorderSizePixel = 0
        miniBtn.Text = "073 Hub"
        miniBtn.TextColor3 = COLORS.white
        miniBtn.Font = Enum.Font.GothamBlack
        miniBtn.TextSize = 13
        miniBtn.Visible = false
        miniBtn.ZIndex = 20
        miniBtn.Parent = gui
        corner(miniBtn, 10)
        stroke(miniBtn, COLORS.stroke, 1, 0.7)
        do
            local dn, ds, sp = false
            miniBtn.InputBegan:Connect(function(i)
                if i.UserInputType == Enum.UserInputType.MouseButton1 or i.UserInputType == Enum.UserInputType.Touch then
                    dn = true; ds = i.Position; sp = miniBtn.Position
                    i.Changed:Connect(function() if i.UserInputState == Enum.UserInputState.End then dn = false end end)
                end
            end)
            UIS.InputChanged:Connect(function(i)
                if dn and (i.UserInputType == Enum.UserInputType.MouseMovement or i.UserInputType == Enum.UserInputType.Touch) then
                    miniBtn.Position = UDim2.new(sp.X.Scale, sp.X.Offset + (i.Position.X - ds.X), sp.Y.Scale, sp.Y.Offset + (i.Position.Y - ds.Y))
                end
            end)
        end

        local function showGui()
            main.Visible = true
            miniBtn.Visible = false
            pcall(function()
                local pg = LP:FindFirstChild("PlayerGui")
                local g = pg and pg:FindFirstChild("Hub073MobileButtons")
                if g then g.Enabled = not (S.hideMobileButtons == true) end
            end)
        end
        local function hideGui()
            main.Visible = false
            miniBtn.Visible = true

            pcall(function()
                if F.applyMobileButtonsHidden then F.applyMobileButtonsHidden() end
            end)
        end
        miniBtn.MouseButton1Click:Connect(showGui)

        local avatarBg = Instance.new("Frame")
        avatarBg.Size = UDim2.new(0, 36, 0, 36)
        avatarBg.Position = UDim2.new(0, 14, 0, 16)
        avatarBg.BackgroundColor3 = Color3.fromRGB(22, 22, 24)
        avatarBg.BorderSizePixel = 0
        avatarBg.ZIndex = 6
        avatarBg.Parent = main
        corner(avatarBg, 18)
        stroke(avatarBg, COLORS.stroke, 1.4, 0.35)

        local avatarImg = Instance.new("ImageLabel")
        avatarImg.Size = UDim2.new(1, -4, 1, -4)
        avatarImg.Position = UDim2.new(0, 2, 0, 2)
        avatarImg.BackgroundTransparency = 1
        avatarImg.Image = ""
        avatarImg.ScaleType = Enum.ScaleType.Crop
        avatarImg.ZIndex = 7
        avatarImg.Parent = avatarBg
        corner(avatarImg, 16)
        task.spawn(function()
            local ok, thumb = pcall(function()
                return Players:GetUserThumbnailAsync(LP.UserId, Enum.ThumbnailType.HeadShot, Enum.ThumbnailSize.Size150x150)
            end)
            if ok and thumb then avatarImg.Image = thumb end
        end)
        LP.CharacterAdded:Connect(function()
            task.spawn(function()
                local ok, thumb = pcall(function()
                    return Players:GetUserThumbnailAsync(LP.UserId, Enum.ThumbnailType.HeadShot, Enum.ThumbnailSize.Size150x150)
                end)
                if ok and thumb then avatarImg.Image = thumb end
            end)
        end)

        local title = Instance.new("TextLabel")
        title.BackgroundTransparency = 1
        title.Size = UDim2.new(1, -100, 0, 18)
        title.Position = UDim2.new(0, 58, 0, 14)
        title.Text = "073 Hub"
        title.TextColor3 = COLORS.white
        title.Font = Enum.Font.GothamBlack
        title.TextSize = 15
        title.TextXAlignment = Enum.TextXAlignment.Left
        title.ZIndex = 6
        title.Parent = main

        local subTitle = Instance.new("TextLabel")
        subTitle.BackgroundTransparency = 1
        subTitle.Size = UDim2.new(1, -100, 0, 14)
        subTitle.Position = UDim2.new(0, 58, 0, 34)
        subTitle.Text = "duels"
        subTitle.TextColor3 = COLORS.textDim
        subTitle.Font = Enum.Font.GothamMedium
        subTitle.TextSize = 11
        subTitle.TextXAlignment = Enum.TextXAlignment.Left
        subTitle.ZIndex = 6
        subTitle.Parent = main

        local statsLbl = Instance.new("TextLabel")
        statsLbl.Name = "FpsPingStats"
        statsLbl.BackgroundTransparency = 1
        statsLbl.Size = UDim2.new(0, 110, 0, 28)
        statsLbl.Position = UDim2.new(1, -150, 0, 16)
        statsLbl.Text = "FPS --  ·  -- ms"
        statsLbl.TextColor3 = COLORS.textDim
        statsLbl.Font = Enum.Font.GothamBold
        statsLbl.TextSize = 11
        statsLbl.TextXAlignment = Enum.TextXAlignment.Right
        statsLbl.ZIndex = 7
        statsLbl.Parent = main
        UI.statsLbl = statsLbl

        task.spawn(function()
            local frames, last, smoothFps = 0, tick(), 0
            local Stats = game:GetService("Stats")
            RunService.RenderStepped:Connect(function()
                frames = frames + 1
                local now = tick()
                if now - last >= 0.35 then
                    local inst = frames / (now - last)
                    smoothFps = (smoothFps == 0) and inst or (smoothFps * 0.6 + inst * 0.4)
                    frames = 0
                    last = now
                    local fpsN = math.floor(smoothFps + 0.5)
                    local pingN = 0
                    pcall(function()
                        local item = Stats.Network.ServerStatsItem["Data Ping"]
                        if item then
                            local s = item:GetValueString()
                            pingN = tonumber((tostring(s):match("%d+") or "0")) or 0
                        end
                    end)
                    if pingN == 0 then
                        pcall(function()
                            pingN = math.floor((Stats.Network:FindFirstChild("ServerStatsItem") and 0) or 0)
                        end)
                    end
                    if pingN == 0 then
                        pcall(function()
                            if LP.GetNetworkPing then
                                pingN = math.floor(LP:GetNetworkPing() * 1000 + 0.5)
                            end
                        end)
                    end
                    local fpsCol = fpsN >= 55 and "g" or (fpsN >= 30 and "w" or "r")
                    if statsLbl and statsLbl.Parent then
                        statsLbl.Text = string.format("FPS %d  ·  %d ms", fpsN, pingN)
                        if fpsN >= 55 and pingN < 120 then
                            statsLbl.TextColor3 = Color3.fromRGB(100, 220, 160)
                        elseif fpsN < 30 or pingN > 200 then
                            statsLbl.TextColor3 = Color3.fromRGB(255, 110, 120)
                        else
                            statsLbl.TextColor3 = COLORS.textDim
                        end
                    end
                end
            end)
        end)

        local closeBtn = Instance.new("TextButton")
        closeBtn.Size = UDim2.new(0, 26, 0, 26)
        closeBtn.Position = UDim2.new(1, -38, 0, 18)
        closeBtn.BackgroundColor3 = Color3.fromRGB(22, 22, 24)
        closeBtn.BackgroundTransparency = 0.15
        closeBtn.Text = "×"
        closeBtn.TextColor3 = COLORS.accent
        closeBtn.Font = Enum.Font.GothamBlack
        closeBtn.TextSize = 16
        closeBtn.BorderSizePixel = 0
        closeBtn.ZIndex = 8
        closeBtn.Parent = main
        corner(closeBtn, 7)
        stroke(closeBtn, COLORS.strokeSoft, 1, 0.45)
        closeBtn.MouseButton1Click:Connect(hideGui)

        local headerDiv = Instance.new("Frame")
        headerDiv.Size = UDim2.new(1, -32, 0, 1)
        headerDiv.Position = UDim2.new(0, 16, 0, 60)
        headerDiv.BackgroundColor3 = COLORS.stroke
        headerDiv.BackgroundTransparency = 0.55
        headerDiv.BorderSizePixel = 0
        headerDiv.ZIndex = 5
        headerDiv.Parent = main

        local pages = {}
        local pageHolder = Instance.new("Frame")
        pageHolder.Size = UDim2.new(1, -16, 1, -70)
        pageHolder.Position = UDim2.new(0, 8, 0, 64)
        pageHolder.BackgroundTransparency = 1
        pageHolder.ClipsDescendants = true
        pageHolder.ZIndex = 3
        pageHolder.Parent = main
        -- single page stack (Main / Bind / Systems)
        local pageHolderLayout = Instance.new("UIListLayout")
        pageHolderLayout.FillDirection = Enum.FillDirection.Horizontal
        pageHolderLayout.SortOrder = Enum.SortOrder.LayoutOrder
        pageHolderLayout.Padding = UDim.new(0, 0)
        pageHolderLayout.Parent = pageHolder

        local function makePage(name)
            local col = Instance.new("Frame")
            col.Name = name .. "_Col"
            col.Size = UDim2.new(1, 0, 1, 0)
            col.BackgroundColor3 = Color3.fromRGB(0, 0, 0)
            col.BackgroundTransparency = 1
            col.BorderSizePixel = 0
            col.ZIndex = 5
            col.Visible = false
            col.Parent = pageHolder
            corner(col, 10)

            local hdr = Instance.new("TextLabel")
            hdr.Size = UDim2.new(1, -12, 0, 0)
            hdr.Position = UDim2.new(0, 8, 0, 0)
            hdr.BackgroundTransparency = 1
            hdr.Text = ""
            hdr.TextColor3 = COLORS.white
            hdr.Font = Enum.Font.GothamBlack
            hdr.TextSize = 11
            hdr.TextXAlignment = Enum.TextXAlignment.Left
            hdr.ZIndex = 6
            hdr.Visible = false
            hdr.Parent = col

            local sf = Instance.new("ScrollingFrame")
            sf.Name = name
            sf.Size = UDim2.new(1, -6, 1, -4)
            sf.Position = UDim2.new(0, 3, 0, 2)
            sf.BackgroundTransparency = 1
            sf.BorderSizePixel = 0
            sf.ScrollBarThickness = 3
            sf.ScrollBarImageColor3 = Color3.fromRGB(200, 200, 200)
            sf.CanvasSize = UDim2.new(0, 0, 0, 0)
            sf.AutomaticCanvasSize = Enum.AutomaticSize.Y
            sf.ScrollingDirection = Enum.ScrollingDirection.Y
            sf.Visible = true
            sf.ZIndex = 6
            sf.Parent = col
            local ll = Instance.new("UIListLayout", sf)
            ll.SortOrder = Enum.SortOrder.LayoutOrder
            ll.Padding = UDim.new(0, 3)
            local pad = Instance.new("UIPadding", sf)
            pad.PaddingTop = UDim.new(0, 2)
            pad.PaddingBottom = UDim.new(0, 8)
            pad.PaddingLeft = UDim.new(0, 2)
            pad.PaddingRight = UDim.new(0, 4)
            pages[name] = sf
            return sf
        end

        local pageMOVE = makePage("MAIN")
        local pageTOOLS = pageMOVE
        local pageVIEW = pageMOVE
        local pageSYS = makePage("SYSTEMS")
        pages["MOVE"] = pageMOVE
        pages["COMBAT"] = pageMOVE
        pages["VISUAL"] = pageMOVE
        pages["MAIN"] = pageMOVE
        pages["SYSTEM"] = pageSYS
        pages["SYSTEMS"] = pageSYS
        local keysCol = Instance.new("Frame")
        keysCol.Name = "KEYS_Col"
        keysCol.Size = UDim2.new(1, 0, 1, 0)
        keysCol.BackgroundColor3 = Color3.fromRGB(0, 0, 0)
        keysCol.BackgroundTransparency = 1
        keysCol.BorderSizePixel = 0
        keysCol.Visible = false
        keysCol.ZIndex = 5
        keysCol.Parent = pageHolder
        corner(keysCol, 10)
        stroke(keysCol, Color3.fromRGB(30, 30, 30), 1, 0.35)
        local keysHdr = Instance.new("TextLabel")
        keysHdr.Name = "Header"
        keysHdr.Size = UDim2.new(1, -12, 0, 20)
        keysHdr.Position = UDim2.new(0, 8, 0, 4)
        keysHdr.BackgroundTransparency = 1
        keysHdr.Text = "KEYBINDS"
        keysHdr.TextColor3 = COLORS.white
        keysHdr.Font = Enum.Font.GothamBlack
        keysHdr.TextSize = 11
        keysHdr.TextXAlignment = Enum.TextXAlignment.Left
        keysHdr.ZIndex = 6
        keysHdr.Visible = false
        keysHdr.Parent = keysCol

        local keybindPanel = Instance.new("Frame")
        keybindPanel.Name = "KeybindPanel"
        keybindPanel.Size = UDim2.new(0, 280, 0, 420)
        keybindPanel.Position = UDim2.new(1, -300, 0, 58)
        keybindPanel.BackgroundColor3 = Color3.fromRGB(0, 0, 0)
        keybindPanel.BackgroundTransparency = 0
        keybindPanel.BorderSizePixel = 0
        keybindPanel.Visible = false
        keybindPanel.ZIndex = 40
        keybindPanel.Parent = main
        corner(keybindPanel, 12)
        stroke(keybindPanel, COLORS.stroke, 1, 0.25)

        local kbTitle = Instance.new("TextLabel")
        kbTitle.Size = UDim2.new(1, -40, 0, 28)
        kbTitle.Position = UDim2.new(0, 12, 0, 8)
        kbTitle.BackgroundTransparency = 1
        kbTitle.Text = "Keybind :"
        kbTitle.TextColor3 = COLORS.white
        kbTitle.Font = Enum.Font.GothamBlack
        kbTitle.TextSize = 14
        kbTitle.TextXAlignment = Enum.TextXAlignment.Left
        kbTitle.ZIndex = 41
        kbTitle.Parent = keybindPanel

        local kbClose = Instance.new("TextButton")
        kbClose.Size = UDim2.new(0, 24, 0, 24)
        kbClose.Position = UDim2.new(1, -32, 0, 10)
        kbClose.BackgroundColor3 = Color3.fromRGB(20, 20, 20)
        kbClose.Text = "×"
        kbClose.TextColor3 = COLORS.white
        kbClose.Font = Enum.Font.GothamBlack
        kbClose.TextSize = 14
        kbClose.BorderSizePixel = 0
        kbClose.ZIndex = 42
        kbClose.Parent = keybindPanel
        corner(kbClose, 6)
        kbClose.MouseButton1Click:Connect(function()
            keybindPanel.Visible = false
        end)

        local pageKEYS = Instance.new("ScrollingFrame")
        pageKEYS.Name = "KEYS"
        pageKEYS.Size = UDim2.new(1, -12, 1, -44)
        pageKEYS.Position = UDim2.new(0, 6, 0, 38)
        pageKEYS.BackgroundTransparency = 1
        pageKEYS.BorderSizePixel = 0
        pageKEYS.ScrollBarThickness = 2
        pageKEYS.ScrollBarImageColor3 = COLORS.accent
        pageKEYS.CanvasSize = UDim2.new(0, 0, 0, 0)
        pageKEYS.AutomaticCanvasSize = Enum.AutomaticSize.Y
        pageKEYS.Visible = true
        pageKEYS.ZIndex = 41
        pageKEYS.Parent = keybindPanel
        do
            local ll = Instance.new("UIListLayout", pageKEYS)
            ll.SortOrder = Enum.SortOrder.LayoutOrder
            ll.Padding = UDim.new(0, 3)
            local pad = Instance.new("UIPadding", pageKEYS)
            pad.PaddingTop = UDim.new(0, 2)
            pad.PaddingBottom = UDim.new(0, 8)
            pad.PaddingLeft = UDim.new(0, 2)
            pad.PaddingRight = UDim.new(0, 4)
        end
        pages["KEYS"] = pageKEYS

        local gearBtn = Instance.new("TextButton")
        gearBtn.Name = "GearBtn"
        gearBtn.Size = UDim2.new(0, 26, 0, 26)
        gearBtn.Position = UDim2.new(1, -68, 0, 18)
        gearBtn.BackgroundColor3 = Color3.fromRGB(16, 16, 16)
        gearBtn.BackgroundTransparency = 0
        gearBtn.Text = "⚙"
        gearBtn.TextColor3 = COLORS.white
        gearBtn.Font = Enum.Font.GothamBold
        gearBtn.TextSize = 14
        gearBtn.BorderSizePixel = 0
        gearBtn.ZIndex = 8
        gearBtn.Parent = main
        corner(gearBtn, 7)
        stroke(gearBtn, COLORS.strokeSoft, 1, 0.4)
        gearBtn.MouseButton1Click:Connect(function()
            keybindPanel.Visible = not keybindPanel.Visible
        end)
        UI.keybindPanel = keybindPanel
        UI.gearBtn = gearBtn

        local Tabs = Instance.new("Frame")
        Tabs.Name = "Tabs"
        Tabs.BackgroundTransparency = 1
        Tabs.AnchorPoint = Vector2.new(0.5, 0)
        Tabs.Position = UDim2.new(0.5, 0, 0, 68)
        Tabs.Size = UDim2.new(1, -24, 0, 0)
        Tabs.Visible = false
        Tabs.ZIndex = 5
        Tabs.Parent = main
        local TabLayout = Instance.new("UIListLayout")
        TabLayout.FillDirection = Enum.FillDirection.Horizontal
        TabLayout.Padding = UDim.new(0, 4)
        TabLayout.SortOrder = Enum.SortOrder.LayoutOrder
        TabLayout.HorizontalAlignment = Enum.HorizontalAlignment.Center
        TabLayout.Parent = Tabs

        local tabNames = {"MAIN", "BIND", "SYSTEMS"}
        local tabDisplay = {MAIN = "Main", BIND = "Bind", SYSTEMS = "Sistems"}
        local tabButtons = {}
        local activeTab = "MAIN"

        local TAB_TO_PAGE = {
            MAIN = "MAIN",
            BIND = "KEYS",
            SYSTEMS = "SYSTEMS",
        }
        local PAGE_TO_TAB = {
            MAIN = "MAIN",
            MOVE = "MAIN",
            COMBAT = "MAIN",
            VISUAL = "MAIN",
            KEYS = "BIND",
            SYSTEM = "SYSTEMS",
            SYSTEMS = "SYSTEMS",
        }

        local function setTab(name)
            activeTab = name
            local pageKey = TAB_TO_PAGE[name] or name
            -- Always single full-width page (Main / Bind / Systems)
            if keybindPanel then keybindPanel.Visible = false end
            for n, pg in pairs(pages) do
                local show = (n == pageKey)
                pg.Visible = show
                local col = pg.Parent
                if col and col:IsA("Frame") and col.Name:find("_Col") then
                    col.Visible = show
                end
            end
            if keysCol then
                keysCol.Visible = (pageKey == "KEYS")
            end

            for n, btn in pairs(tabButtons) do
                local on = (n == name)
                btn.BackgroundColor3 = on and COLORS.row2 or COLORS.row
                btn.BackgroundTransparency = on and 0.05 or 0.35
                btn.TextColor3 = on and COLORS.white or COLORS.textDim
                local st = btn:FindFirstChildOfClass("UIStroke")
                if st then
                    st.Color = on and COLORS.white or COLORS.strokeSoft
                    st.Transparency = on and 0.35 or 0.7
                end
            end
        end

        function F.applyHubUiLayout()
            local layout = "Old"
            local isOld = true

            if isOld then
                main.Size = UDim2.new(0, 380, 0, 520)
                Tabs.Visible = true
                Tabs.Size = UDim2.new(1, -24, 0, 36)
                Tabs.AnchorPoint = Vector2.new(0.5, 1)
                Tabs.Position = UDim2.new(0.5, 0, 1, -10)
                pageHolder.Position = UDim2.new(0, 8, 0, 58)
                pageHolder.Size = UDim2.new(1, -16, 1, -108)
                if pageHolderLayout then
                    pageHolderLayout.FillDirection = Enum.FillDirection.Horizontal
                    pageHolderLayout.Padding = UDim.new(0, 0)
                end
                for _, pg in pairs(pages) do
                    local col = pg.Parent
                    if col and col:IsA("Frame") and col.Name:find("_Col") then
                        col.Size = UDim2.new(1, 0, 1, 0)
                        local hdr = col:FindFirstChildOfClass("TextLabel")
                        if hdr then hdr.Visible = false end
                    end
                end
                if pageKEYS and keysCol then
                    pageKEYS.Parent = keysCol
                    pageKEYS.Size = UDim2.new(1, -6, 1, -8)
                    pageKEYS.Position = UDim2.new(0, 3, 0, 4)
                    pageKEYS.ZIndex = 6
                    keysCol.Size = UDim2.new(1, 0, 1, 0)
                    keysCol.Visible = false
                    if keysHdr then keysHdr.Visible = false end
                end
                if kbTitle then kbTitle.Visible = false end
                if kbClose then kbClose.Visible = false end
                if keybindPanel then keybindPanel.Visible = false end
                if UI.gearBtn then UI.gearBtn.Visible = false end
                setTab(activeTab or "MOVEMENT")
            else
                main.Size = UDim2.new(0, 720, 0, 560)
                Tabs.Visible = false
                Tabs.Size = UDim2.new(1, -24, 0, 0)
                pageHolder.Position = UDim2.new(0, 8, 0, 64)
                pageHolder.Size = UDim2.new(1, -16, 1, -70)
                if pageHolderLayout then
                    pageHolderLayout.FillDirection = Enum.FillDirection.Horizontal
                    pageHolderLayout.Padding = UDim.new(0, 6)
                end
                for _, pg in pairs(pages) do
                    local col = pg.Parent
                    if col and col:IsA("Frame") and col.Name:find("_Col") and col ~= keysCol then
                        col.Size = UDim2.new(0, 172, 1, 0)
                        col.Visible = true
                        pg.Visible = true
                        local hdr = col:FindFirstChildOfClass("TextLabel")
                        if hdr then hdr.Visible = true end
                    end
                end
                if keysCol then keysCol.Visible = false end
                if pageKEYS and keybindPanel then
                    pageKEYS.Parent = keybindPanel
                    pageKEYS.Size = UDim2.new(1, -12, 1, -44)
                    pageKEYS.Position = UDim2.new(0, 6, 0, 38)
                    pageKEYS.ZIndex = 41
                    pageKEYS.Visible = true
                end
                if kbTitle then kbTitle.Visible = true end
                if kbClose then kbClose.Visible = true end
                if keybindPanel then keybindPanel.Visible = false end
                if UI.gearBtn then UI.gearBtn.Visible = true end
                setTab(activeTab or "MOVEMENT")
            end
            if UI.uiLayoutStyleBtn then
                UI.uiLayoutStyleBtn.Text = isOld and "Old" or "New"
            end
        end

        for i, name in ipairs(tabNames) do
            local btn = Instance.new("TextButton")
            btn.Name = name
            btn.Size = UDim2.new(0, 100, 0, 30)
            btn.BackgroundColor3 = COLORS.row
            btn.BackgroundTransparency = 0.35
            btn.BorderSizePixel = 0
            btn.Text = tabDisplay[name]
            btn.TextColor3 = COLORS.textDim
            btn.Font = Enum.Font.GothamBold
            btn.TextSize = 12
            btn.AutoButtonColor = false
            btn.LayoutOrder = i
            btn.ZIndex = 6
            btn.Parent = Tabs
            corner(btn, 8)
            stroke(btn, COLORS.strokeSoft, 1, 0.7)
            btn.MouseButton1Click:Connect(function() setTab(name) end)
            tabButtons[name] = btn
        end
        setTab("MAIN")

        local lo = 0
        local function LO() lo = lo + 1; return lo end

        local function section(parent, text)
            local f = Instance.new("Frame", parent)
            f.Size = UDim2.new(1, 0, 0, 18)
            f.BackgroundTransparency = 1
            f.LayoutOrder = LO()
            local l = Instance.new("TextLabel", f)
            l.Size = UDim2.new(1, -4, 1, 0)
            l.Position = UDim2.new(0, 4, 0, 0)
            l.BackgroundTransparency = 1
            l.Text = tostring(text or ""):upper()
            l.TextColor3 = COLORS.textDim
            l.Font = Enum.Font.GothamBold
            l.TextSize = 9
            l.TextXAlignment = Enum.TextXAlignment.Left
            return f
        end

        local function mkRow(parent, h)
            local f = Instance.new("Frame", parent)
            f.Size = UDim2.new(1, 0, 0, h or 30)
            f.BackgroundColor3 = COLORS.row
            f.BackgroundTransparency = 0.28
            f.BorderSizePixel = 0
            f.LayoutOrder = LO()
            corner(f, 6)
            stroke(f, COLORS.strokeSoft, 1, 0.55)
            f.MouseEnter:Connect(function()
                TS:Create(f, TweenInfo.new(0.1), {BackgroundTransparency = 0.02, BackgroundColor3 = COLORS.row2}):Play()
            end)
            f.MouseLeave:Connect(function()
                TS:Create(f, TweenInfo.new(0.1), {BackgroundTransparency = 0.15, BackgroundColor3 = COLORS.row}):Play()
            end)
            return f
        end

        local function mkLabel(row, txt)
            local l = Instance.new("TextLabel", row)
            l.Size = UDim2.new(0.58, 0, 1, 0)
            l.Position = UDim2.new(0, 12, 0, 0)
            l.BackgroundTransparency = 1
            l.Text = txt
            l.TextColor3 = COLORS.white
            l.Font = Enum.Font.GothamBold
            l.TextSize = 11
            l.TextXAlignment = Enum.TextXAlignment.Left
            return l
        end

        local function mkPill(row, offset)
            local pill = Instance.new("Frame", row)
            pill.Size = UDim2.new(0, 40, 0, 20)
            pill.Position = UDim2.new(1, -(offset or 52), 0.5, -10)
            pill.BackgroundColor3 = COLORS.off
            pill.BorderSizePixel = 0
            pill.ZIndex = 4
            corner(pill, 10)
            local dot = Instance.new("Frame", pill)
            dot.Size = UDim2.new(0, 14, 0, 14)
            dot.Position = UDim2.new(0, 3, 0.5, -7)
            dot.BackgroundColor3 = COLORS.textDim
            dot.BorderSizePixel = 0
            dot.ZIndex = 5
            corner(dot, 7)
            return pill, dot
        end

        local function animPill(pill, dot, on)
            local onCol = S.themeColor or COLORS.on or Color3.fromRGB(220, 220, 235)
            if on then
                pill:SetAttribute("ThemePillOn", true)
            else
                pill:SetAttribute("ThemePillOn", false)
            end
            TS:Create(pill, TweenInfo.new(0.16, Enum.EasingStyle.Quad), {
                BackgroundColor3 = on and onCol or COLORS.off,
                BackgroundTransparency = on and 0.22 or 0
            }):Play()
            TS:Create(dot, TweenInfo.new(0.16, Enum.EasingStyle.Back), {
                Position = on and UDim2.new(1, -17, 0.5, -7) or UDim2.new(0, 3, 0.5, -7),
                BackgroundColor3 = on and Color3.fromRGB(18, 18, 20) or COLORS.textDim
            }):Play()
        end

        local function mkToggle(parent, txt, cb)
            local row = mkRow(parent, 34)
            mkLabel(row, txt)
            local pill, dot = mkPill(row, 52)
            local on = false
            local function sv(s) on = s; animPill(pill, dot, s) end
            local clk = Instance.new("TextButton", pill)
            clk.Size = UDim2.new(1, 0, 1, 0)
            clk.BackgroundTransparency = 1
            clk.Text = ""
            clk.ZIndex = 6
            clk.Activated:Connect(function()
                if S._anyKeyListening then return end
                on = not on; sv(on); if cb then cb(on) end
            end)
            return sv
        end

        local function mkBox(parent, default, w, xOff, cb)
            local tb = Instance.new("TextBox", parent)
            tb.Size = UDim2.new(0, w or 54, 0, 24)
            tb.Position = UDim2.new(1, -(xOff or 62), 0.5, -12)
            tb.BackgroundColor3 = Color3.fromRGB(18, 18, 20)
            tb.BackgroundTransparency = 0.15
            tb.BorderSizePixel = 0
            tb.Text = tostring(default)
            tb.TextColor3 = COLORS.white
            tb.Font = Enum.Font.GothamBold
            tb.TextSize = 12
            tb.ClearTextOnFocus = false
            tb.ZIndex = 5
            corner(tb, 8)
            local bs = stroke(tb, COLORS.stroke, 1, 0.78)
            tb.Focused:Connect(function() TS:Create(bs, TweenInfo.new(0.12), {Color = COLORS.accent}):Play() end)
            tb.FocusLost:Connect(function()
                TS:Create(bs, TweenInfo.new(0.12), {Color = COLORS.strokeSoft}):Play()
                if cb then local n = tonumber(tb.Text); if n then cb(n) else tb.Text = tostring(default) end end
            end)
            return tb
        end

        local GAMEPAD_KEYS = {
            [Enum.KeyCode.ButtonA]=true,[Enum.KeyCode.ButtonB]=true,[Enum.KeyCode.ButtonX]=true,[Enum.KeyCode.ButtonY]=true,
            [Enum.KeyCode.ButtonL1]=true,[Enum.KeyCode.ButtonR1]=true,[Enum.KeyCode.ButtonL2]=true,[Enum.KeyCode.ButtonR2]=true,
            [Enum.KeyCode.ButtonL3]=true,[Enum.KeyCode.ButtonR3]=true,[Enum.KeyCode.ButtonStart]=true,[Enum.KeyCode.ButtonSelect]=true,
            [Enum.KeyCode.DPadUp]=true,[Enum.KeyCode.DPadDown]=true,[Enum.KeyCode.DPadLeft]=true,[Enum.KeyCode.DPadRight]=true
        }
        local function isGamepadInput(inp) return inp and inp.UserInputType and inp.UserInputType.Name:match("^Gamepad") ~= nil end
        local function isBindableInput(inp)
            if not inp or inp.KeyCode == Enum.KeyCode.Unknown then return false end
            if inp.UserInputType == Enum.UserInputType.Keyboard then return true end
            return isGamepadInput(inp) and GAMEPAD_KEYS[inp.KeyCode] == true
        end
        local function kbMatch(entry, kc) return kc and (kc == entry.kb or (entry.gp and kc == entry.gp)) end

        local function mkKB(parent, kbEntry, cb)
            local btn = Instance.new("TextButton", parent)
            btn.Size = UDim2.new(0, 52, 0, 24)
            btn.Position = UDim2.new(1, -60, 0.5, -12)
            btn.BackgroundColor3 = S.themeColor or COLORS.accent or Color3.fromRGB(18, 18, 20)
            btn.BackgroundTransparency = 0.55
            btn.BorderSizePixel = 0
            btn:SetAttribute("ThemeButton", true)
            local function getLabel() return (kbEntry.gp and kbEntry.gp.Name) or (kbEntry.kb and kbEntry.kb.Name) or "None" end
            btn.Text = getLabel()
            btn.TextColor3 = COLORS.white
            btn.Font = Enum.Font.GothamBold
            btn.TextSize = 10
            btn.ZIndex = 5
            corner(btn, 8)
            stroke(btn, S.themeColor or COLORS.stroke, 1, 0.55)
            local li, lc, pv, listenStart = false, nil, btn.Text, 0
            btn.Activated:Connect(function()
                if li then li = false; S._anyKeyListening = false; if lc then lc:Disconnect(); lc = nil end; btn.Text = pv; btn.TextColor3 = COLORS.white; return end
                pv = btn.Text; li = true; S._anyKeyListening = true; listenStart = tick(); btn.Text = "..."; btn.TextColor3 = COLORS.accent
                lc = UIS.InputBegan:Connect(function(inp)
                    if not li then return end
                    if inp.KeyCode == Enum.KeyCode.Escape then li = false; S._anyKeyListening = false; if lc then lc:Disconnect(); lc = nil end; btn.Text = pv; btn.TextColor3 = COLORS.white; return end
                    local isGp = isGamepadInput(inp)
                    if isGp and tick() - listenStart < 0.15 then return end
                    if not isBindableInput(inp) then return end
                    btn.Text = inp.KeyCode.Name; pv = inp.KeyCode.Name; btn.TextColor3 = COLORS.white
                    li = false; S._anyKeyListening = false; if lc then lc:Disconnect(); lc = nil end
                    if cb then cb(inp.KeyCode, isGp) end
                end)
            end)
            return btn
        end

        local function mkToggleKB(parent, txt, kbEntry, onToggle, onKB)
            local row = mkRow(parent, 34)
            mkLabel(row, txt)
            if kbEntry then
                mkKB(row, kbEntry, function(k, isGp)
                    if isGp then kbEntry.gp = k; kbEntry.kb = nil else kbEntry.kb = k; kbEntry.gp = nil end
                    if onKB then onKB(k, isGp) end
                end)
            end
            local pill, dot = mkPill(row, kbEntry and 118 or 52)
            local on = false
            local function sv(s) on = s; animPill(pill, dot, s) end
            local clk = Instance.new("TextButton", pill)
            clk.Size = UDim2.new(1, 0, 1, 0)
            clk.BackgroundTransparency = 1
            clk.Text = ""
            clk.ZIndex = 6
            clk.Activated:Connect(function()
                if S._anyKeyListening then return end
                on = not on; sv(on); if onToggle then onToggle(on) end
            end)
            return sv
        end

        lo = 0
        section(pageMOVE, "Main")
        do
            S.setAutoCarrySpeedVisual = mkToggle(pageMOVE, "Speed", function(on)
                S.autoCarrySpeedEnabled = on
                if not on then
                    pcall(F.disableAutoCarrySpeed)
                end
                F.saveConfig()
            end)
        end
        section(pageMOVE, "Speeds")
        do local row = mkRow(pageMOVE, 34); mkLabel(row, "Auto Speed"); UI.normalBox = mkBox(row, NS, 54, 62, function(v) if v > 0 and v <= 500 then NS = v end; F.saveConfig() end) end
        do local row = mkRow(pageMOVE, 34); mkLabel(row, "Carry Speed"); UI.carryBox = mkBox(row, CS, 54, 62, function(v) if v > 0 and v <= 500 then CS = v end; F.saveConfig() end) end
        do local row = mkRow(pageMOVE, 34); mkLabel(row, "Lagger Normal"); UI.laggerBox = mkBox(row, S.LAGGER_SPEED, 54, 62, function(v) if v > 0 and v <= 500 then S.LAGGER_SPEED = v end; F.saveConfig() end) end
        do local row = mkRow(pageMOVE, 34); mkLabel(row, "Lagger Carry"); UI.laggerCarryBox = mkBox(row, S.LAGGER_CARRY_SPEED, 54, 62, function(v) if v > 0 and v <= 500 then S.LAGGER_CARRY_SPEED = v end; F.saveConfig() end) end
        do
            local row = mkRow(pageMOVE, 34); mkLabel(row, "Mode")
            modeValLbl = Instance.new("TextLabel", row)
            modeValLbl.Size = UDim2.new(0, 100, 1, 0)
            modeValLbl.Position = UDim2.new(1, -110, 0, 0)
            modeValLbl.BackgroundTransparency = 1
            modeValLbl.Text = "Normal"
            modeValLbl.TextColor3 = COLORS.accentStrong
            modeValLbl.Font = Enum.Font.GothamBlack
            modeValLbl.TextSize = 12
            modeValLbl.TextXAlignment = Enum.TextXAlignment.Right
            local clk = Instance.new("TextButton", row)
            clk.Size = UDim2.new(1, 0, 1, 0)
            clk.BackgroundTransparency = 1
            clk.Text = ""
            clk.ZIndex = 2
            clk.Activated:Connect(function()
                if S._anyKeyListening then return end
                F.toggleCarryMode()
                F.saveConfig()
            end)
        end
        section(pageMOVE, "Auto Play")
        -- removed Auto Left
        -- removed Auto Right
        section(pageMOVE, "Teleport")
        do
            local row = mkRow(pageMOVE, 34); mkLabel(row, "Drop Brainrot")
            mkKB(row, KB.DropBrainrot, function(k, isGp) if isGp then KB.DropBrainrot.gp = k; KB.DropBrainrot.kb = nil else KB.DropBrainrot.kb = k; KB.DropBrainrot.gp = nil end; F.saveConfig() end)
            local clk = Instance.new("TextButton", row); clk.Size = UDim2.new(0.55, 0, 1, 0); clk.BackgroundTransparency = 1; clk.Text = ""; clk.ZIndex = 2
            clk.Activated:Connect(function() F.runDrop() end)
        end
        do
            local row = mkRow(pageMOVE, 34); mkLabel(row, "TP Down")
            mkKB(row, KB.TPFloor, function(k, isGp) if isGp then KB.TPFloor.gp = k; KB.TPFloor.kb = nil else KB.TPFloor.kb = k; KB.TPFloor.gp = nil end; F.saveConfig() end)
            local clk = Instance.new("TextButton", row); clk.Size = UDim2.new(0.55, 0, 1, 0); clk.BackgroundTransparency = 1; clk.Text = ""; clk.ZIndex = 2
            clk.Activated:Connect(function() F.runTPFloor() end)
        end
        do
            VS.setAutoTPVisual = mkToggle(pageMOVE, "Auto TP", function(on)
                S.autoTPEnabled = on
                if on then F.startAutoTP() else F.stopAutoTP() end
                F.saveConfig()
            end)
        end
        do
            local row = mkRow(pageMOVE, 34); mkLabel(row, "Auto TP Height")
            UI.autoTPHeightBox = mkBox(row, S.autoTPHeight, 54, 62, function(v)
                if v >= 0 and v <= 500 then S.autoTPHeight = v elseif UI.autoTPHeightBox then UI.autoTPHeightBox.Text = tostring(S.autoTPHeight) end
                F.saveConfig()
            end)
        end

        section(pageTOOLS, "Combat")
        do
            local abRow = mkRow(pageTOOLS, 34); mkLabel(abRow, "Auto Bat")
            mkKB(abRow, KB.AutoBat, function(k, isGp)
                if isGp then KB.AutoBat.gp = k; KB.AutoBat.kb = nil else KB.AutoBat.kb = k; KB.AutoBat.gp = nil end
                F.saveConfig()
            end)
            local abPill, abDot = mkPill(abRow, 118)
            local abOn = false
            local function svAutoBat(s) abOn = s; animPill(abPill, abDot, s) end
            S.autoBatSetVisual = svAutoBat
            local abClk = Instance.new("TextButton", abPill)
            abClk.Size = UDim2.new(1, 0, 1, 0)
            abClk.BackgroundTransparency = 1
            abClk.Text = ""
            abClk.ZIndex = 6
            abClk.Activated:Connect(function()
                if S._anyKeyListening then return end
                abOn = not abOn; svAutoBat(abOn)
                if abOn then F.queueAutoBatStart() else S.autoBatEnabled = false; S.disableAutoBat() end
                F.saveConfig()
            end)
        end

        VS.setMirrorTPVisual = function() end -- removed


        do
            local row = mkRow(pageTOOLS, 34)
            mkLabel(row, "Aimbot")
            local modeBtn = Instance.new("TextButton", row)
            modeBtn.Size = UDim2.new(0, 78, 0, 22)
            modeBtn.Position = UDim2.new(1, -134, 0.5, -11)
            modeBtn.BackgroundColor3 = Color3.fromRGB(18, 18, 20)
            modeBtn.BorderSizePixel = 0
            modeBtn.Text = ((S.aimbotMode or 1) == 2) and "V2 Bypass" or "V1"
            modeBtn.TextColor3 = COLORS.white
            modeBtn.Font = Enum.Font.GothamBold
            modeBtn.TextSize = 10
            modeBtn.ZIndex = 6
            corner(modeBtn, 7)
            stroke(modeBtn, COLORS.stroke, 1, 0.55)
            UI.aimbotModeBtn = modeBtn

            local pill, dot = mkPill(row, 118)
            local abOn = false
            local function svAim(s)
                abOn = s
                animPill(pill, dot, s)
            end
            VS.setAimbotVisual = svAim
            VS.setAntiBatBypassVisual = function(on)
                if (S.aimbotMode or 1) == 2 then svAim(on) end
            end

            local clk = Instance.new("TextButton", pill)
            clk.Size = UDim2.new(1, 0, 1, 0)
            clk.BackgroundTransparency = 1
            clk.Text = ""
            clk.ZIndex = 6
            clk.Activated:Connect(function()
                if S._anyKeyListening then return end
                abOn = not abOn
                svAim(abOn)
                setAimbotState(abOn)
                F.saveConfig()
            end)

            modeBtn.MouseButton1Click:Connect(function()
                if S._anyKeyListening then return end
                S.aimbotMode = ((S.aimbotMode or 1) == 2) and 1 or 2
                modeBtn.Text = (S.aimbotMode == 2) and "V2 Bypass" or "V1"
                if abOn then
                    setAimbotState(false)
                    setAimbotState(true)
                    svAim(true)
                end
                F.saveConfig()
            end)
        end
        VS.setAutoSwingVisual = mkToggle(pageTOOLS, "Auto Swing", function(on)
            S.autoSwingEnabled = on
            F.saveConfig()
        end)
        if VS.setAutoSwingVisual then VS.setAutoSwingVisual(S.autoSwingEnabled) end

        do
            VS.setBatCounterVisual = mkToggle(pageTOOLS, "Bat Counter", function(on)
                S.batCounterEnabled = on
                if on then S.startBatCounter() else S.stopBatCounter() end
                F.saveConfig()
            end)
        end
        do
            local expanded = false
            local headerH, bodyH = 34, 76

            local wrap = Instance.new("Frame", pageTOOLS)
            wrap.Size = UDim2.new(1, 0, 0, headerH)
            wrap.BackgroundTransparency = 1
            wrap.ClipsDescendants = true
            wrap.LayoutOrder = LO()

            local header = Instance.new("Frame", wrap)
            header.Size = UDim2.new(1, 0, 0, headerH)
            header.BackgroundColor3 = COLORS.row
            header.BorderSizePixel = 0
            corner(header, 9)
            stroke(header, COLORS.strokeSoft, 1, 0.55)
            header.MouseEnter:Connect(function()
                TS:Create(header, TweenInfo.new(0.1), {BackgroundColor3 = COLORS.row2}):Play()
            end)
            header.MouseLeave:Connect(function()
                TS:Create(header, TweenInfo.new(0.1), {BackgroundColor3 = COLORS.row}):Play()
            end)

            local arrow = Instance.new("TextLabel", header)
            arrow.Size = UDim2.new(0, 22, 1, 0)
            arrow.Position = UDim2.new(0, 8, 0, 0)
            arrow.BackgroundTransparency = 1
            arrow.Text = "▶"
            arrow.TextColor3 = COLORS.accentStrong
            arrow.Font = Enum.Font.GothamBold
            arrow.TextSize = 11
            arrow.TextXAlignment = Enum.TextXAlignment.Left

            local titleLbl = Instance.new("TextLabel", header)
            titleLbl.Size = UDim2.new(0.5, 0, 1, 0)
            titleLbl.Position = UDim2.new(0, 28, 0, 0)
            titleLbl.BackgroundTransparency = 1
            titleLbl.Text = "TP Bat"
            titleLbl.TextColor3 = COLORS.white
            titleLbl.Font = Enum.Font.GothamBold
            titleLbl.TextSize = 12
            titleLbl.TextXAlignment = Enum.TextXAlignment.Left

            local modeHint = Instance.new("TextLabel", header)
            modeHint.Size = UDim2.new(0, 48, 0, 18)
            modeHint.Position = UDim2.new(1, -110, 0.5, -9)
            modeHint.BackgroundColor3 = Color3.fromRGB(18, 18, 20)
            modeHint.Text = ""
            modeHint.Visible = false
            modeHint.TextColor3 = COLORS.accentStrong
            modeHint.Font = Enum.Font.GothamBold
            modeHint.TextSize = 10
            modeHint.BorderSizePixel = 0
            corner(modeHint, 5)

            local pill, dot = mkPill(header, 52)
            local tpOn = false
            local function svTP(s)
                tpOn = s
                animPill(pill, dot, s)
            end
            VS.setTPBatVisual = svTP

            local pillClk = Instance.new("TextButton", pill)
            pillClk.Size = UDim2.new(1, 0, 1, 0)
            pillClk.BackgroundTransparency = 1
            pillClk.Text = ""
            pillClk.ZIndex = 6
            pillClk.Activated:Connect(function()
                if S._anyKeyListening then return end
                tpOn = not tpOn
                svTP(tpOn)
                setTPBatState(tpOn)
                F.saveConfig()
            end)

            local body = Instance.new("Frame", wrap)
            body.Size = UDim2.new(1, 0, 0, bodyH)
            body.Position = UDim2.new(0, 0, 0, headerH)
            body.BackgroundTransparency = 1
            body.Visible = false

            local kbRow = Instance.new("Frame", body)
            kbRow.Size = UDim2.new(1, 0, 0, 32)
            kbRow.Position = UDim2.new(0, 0, 0, 4)
            kbRow.BackgroundColor3 = COLORS.row2
            kbRow.BorderSizePixel = 0
            corner(kbRow, 8)
            stroke(kbRow, COLORS.strokeSoft, 1, 0.6)

            local kbLbl = Instance.new("TextLabel", kbRow)
            kbLbl.Size = UDim2.new(0.5, 0, 1, 0)
            kbLbl.Position = UDim2.new(0, 12, 0, 0)
            kbLbl.BackgroundTransparency = 1
            kbLbl.Text = "Keybind"
            kbLbl.TextColor3 = COLORS.textDim
            kbLbl.Font = Enum.Font.GothamBold
            kbLbl.TextSize = 11
            kbLbl.TextXAlignment = Enum.TextXAlignment.Left

            mkKB(kbRow, KB.TPBat, function(k, isGp)
                if isGp then
                    KB.TPBat.gp = k
                    KB.TPBat.kb = nil
                    TP_BAT_CONTROLLER = k
                else
                    KB.TPBat.kb = k
                    KB.TPBat.gp = nil
                    TP_BAT_KEY = k
                end
                F.saveConfig()
            end)

            local modeRow = Instance.new("Frame", body)
            modeRow.Size = UDim2.new(1, 0, 0, 32)
            modeRow.Position = UDim2.new(0, 0, 0, 38)
            modeRow.BackgroundColor3 = COLORS.row2
            modeRow.BorderSizePixel = 0
            corner(modeRow, 8)
            stroke(modeRow, COLORS.strokeSoft, 1, 0.6)

            local modeTitle = Instance.new("TextLabel", modeRow)
            modeTitle.Size = UDim2.new(0.4, 0, 1, 0)
            modeTitle.Position = UDim2.new(0, 12, 0, 0)
            modeTitle.BackgroundTransparency = 1
            modeTitle.Text = "Mode"
            modeTitle.TextColor3 = COLORS.textDim
            modeTitle.Font = Enum.Font.GothamBold
            modeTitle.TextSize = 11
            modeTitle.TextXAlignment = Enum.TextXAlignment.Left

            tpBatModeLbl = Instance.new("TextButton", modeRow)
            tpBatModeLbl.Size = UDim2.new(0, 118, 0, 22)
            tpBatModeLbl.Position = UDim2.new(1, -130, 0.5, -11)
            tpBatModeLbl.BackgroundColor3 = Color3.fromRGB(18, 18, 20)
            tpBatModeLbl.BorderSizePixel = 0
            tpBatModeLbl.Text = (tpBatMode == 3) and "Bat And Med v2" or ((tpBatMode == 2) and "Only Bat" or "Med And Bat")
            tpBatModeLbl.TextColor3 = COLORS.white
            tpBatModeLbl.Font = Enum.Font.GothamBold
            tpBatModeLbl.TextSize = 11
            tpBatModeLbl.ZIndex = 6
            tpBatModeLbl.AutoButtonColor = true
            corner(tpBatModeLbl, 7)
            stroke(tpBatModeLbl, COLORS.stroke, 1, 0.55)
            tpBatModeLbl.MouseButton1Click:Connect(function()
                tpBatMode = (tpBatMode % 3) + 1
                tpBatModeLbl.Text = (tpBatMode == 3) and "Bat And Med v2" or ((tpBatMode == 2) and "Only Bat" or "Med And Bat")
                if modeHint then modeHint.Text = tpBatModeLbl.Text; modeHint.Visible = true end
                if tpBatEnabled then
                    stopTPBat()
                    startTPBat()
                end
                F.saveConfig()
            end)
            modeRow.Visible = true
            if modeHint then
                modeHint.Text = tpBatModeLbl.Text
                modeHint.Visible = true
            end
            bodyH = 76

            local function setExpanded(on)
                expanded = on
                arrow.Text = on and "▼" or "▶"
                body.Visible = on
                TS:Create(wrap, TweenInfo.new(0.18, Enum.EasingStyle.Quad), {
                    Size = UDim2.new(1, 0, 0, on and (headerH + bodyH + 2) or headerH)
                }):Play()
            end

            local expandBtn = Instance.new("TextButton", header)
            expandBtn.Size = UDim2.new(1, -56, 1, 0)
            expandBtn.Position = UDim2.new(0, 0, 0, 0)
            expandBtn.BackgroundTransparency = 1
            expandBtn.Text = ""
            expandBtn.ZIndex = 2
            expandBtn.Activated:Connect(function()
                if S._anyKeyListening then return end
                setExpanded(not expanded)
            end)
        end


        do
            local row = mkRow(pageTOOLS, 34); mkLabel(row, "Spin Speed")
            UI.spinSpeedBox = mkBox(row, S.spinOnTPBatSpeed or 250, 54, 62, function(v)
                if v > 0 and v <= 500 then S.spinOnTPBatSpeed = v elseif UI.spinSpeedBox then UI.spinSpeedBox.Text = tostring(S.spinOnTPBatSpeed) end
                F.saveConfig()
            end)
        end
        VS.setFlingOnTPBatVisual = function() end -- removed fling

        do
            local row = mkRow(pageTOOLS, 34); mkLabel(row, "Tp Bat Distance")
            UI.tpBatDistanceBox = mkBox(row, S.tpBatDistance or 1000, 54, 62, function(v)
                if v >= 0 and v <= 1000 then S.tpBatDistance = v elseif UI.tpBatDistanceBox then UI.tpBatDistanceBox.Text = tostring(S.tpBatDistance) end
                F.saveConfig()
            end)
        end

        section(pageTOOLS, "Steal")
        do
            local stealRow = mkRow(pageTOOLS, 34); mkLabel(stealRow, "Auto Steal")
            local pill, dot = mkPill(stealRow, 52)
            local function applyStealVis(on)
                TS:Create(pill, TweenInfo.new(0.15), {BackgroundColor3 = on and COLORS.accent or COLORS.off}):Play()
                TS:Create(dot, TweenInfo.new(0.15), {
                    Position = on and UDim2.new(1, -22, 0.5, -8) or UDim2.new(0, 2, 0.5, -8),
                    BackgroundColor3 = on and COLORS.white or COLORS.textDim
                }):Play()
            end
            VS.setInstaGrab = applyStealVis
            pill.InputBegan:Connect(function(inp)
                if inp.UserInputType ~= Enum.UserInputType.MouseButton1 and inp.UserInputType ~= Enum.UserInputType.Touch then return end
                if S._anyKeyListening then return end
                local on = not autoStealEnabled
                if on then startAutoSteal() else stopAutoSteal() end
                applyStealVis(on)
                F.saveConfig()
            end)
        end

        local stealOptRows = { normal = {}, semi = {}, v3 = {}, always = {} }

        do
            local row = mkRow(pageTOOLS, 34); mkLabel(row, "Steal Mode")
            local modeLbl = Instance.new("TextButton", row)
            modeLbl.Size = UDim2.new(0, 72, 0, 24)
            modeLbl.Position = UDim2.new(1, -82, 0.5, -12)
            modeLbl.BackgroundColor3 = Color3.fromRGB(18, 18, 20)
            modeLbl.BorderSizePixel = 0
            modeLbl.Text = tostring(selectedStealMode or "Normal")
            modeLbl.TextColor3 = COLORS.white
            modeLbl.Font = Enum.Font.GothamBold
            modeLbl.TextSize = 11
            modeLbl.ZIndex = 5
            corner(modeLbl, 8)
            UI.stealModeLbl = modeLbl
            local modes = {"Normal", "Semi", "V3"}

            local function refreshStealOptionsVis()
                local m = selectedStealMode or "Normal"
                local showN = (m == "Normal" or m == "V3")
                local showS = (m == "Semi" or m == "V3")
                local showV = (m == "V3")
                for _, r in ipairs(stealOptRows.normal) do
                    if r and r.Parent then r.Visible = true end
                end
                for _, r in ipairs(stealOptRows.semi) do
                    if r and r.Parent then r.Visible = (m == "Semi" or m == "V3") end
                end
                for _, r in ipairs(stealOptRows.v3) do
                    if r and r.Parent then r.Visible = (m == "V3") end
                end
            end
            UI.refreshStealOptionsVis = refreshStealOptionsVis

            modeLbl.MouseButton1Click:Connect(function()
                local cur = selectedStealMode or "Normal"
                local idx = 1
                for i, m in ipairs(modes) do if m == cur then idx = i break end end
                idx = (idx % #modes) + 1
                selectedStealMode = modes[idx]
                modeLbl.Text = selectedStealMode

                if selectedStealMode == "Normal" then
                    autoGrabStopEnabled = false
                    Steal.AutoGrabStopEnabled = false
                elseif selectedStealMode == "Semi" then
                    autoGrabStopEnabled = true
                    Steal.AutoGrabStopEnabled = true
                else
                    autoGrabStopEnabled = true
                    Steal.AutoGrabStopEnabled = true
                    autoStealRadius = math.max(tonumber(autoStealRadius) or 20, 55)
                    Steal.StealRadius = autoStealRadius
                    if UI.radInput then UI.radInput.Text = tostring(autoStealRadius) end
                end
                refreshStealOptionsVis()
                if autoStealEnabled then
                    pcall(stopAutoSteal)
                    pcall(startAutoSteal)
                end
                F.saveConfig()
            end)
        end

        do
            local row = mkRow(pageTOOLS, 34); mkLabel(row, "Steal Radius")
            table.insert(stealOptRows.normal, row)
            UI.radInput = mkBox(row, Steal.StealRadius, 54, 62, function(v)
                v = tonumber(v)
                if v and v > 0 then
                    autoStealRadius = v
                    Steal.StealRadius = v
                    AceStealRadii.Normal = v
                    if progressRadLbl then progressRadLbl.Text = string.format("Radius: %.0f", v) end
                    if F.updateShowRadius then F.updateShowRadius() end
                    F.saveConfig()
                end
            end)
        end
        do
            local row = mkRow(pageTOOLS, 34); mkLabel(row, "Steal Duration")
            table.insert(stealOptRows.normal, row)
            UI.stealDurInput = mkBox(row, Steal.StealDuration, 54, 62, function(v)
                v = tonumber(v)
                if v and v >= 0.1 then
                    Steal.StealDuration = v
                    F.saveConfig()
                end
            end)
        end
        do
            local row = mkRow(pageTOOLS, 34); mkLabel(row, "Stop Time (s)")
            table.insert(stealOptRows.semi, row)
            UI.stopTimeInput = mkBox(row, Steal.AutoGrabStopTime, 54, 62, function(v)
                v = tonumber(v)
                if v and v > 0 then
                    Steal.AutoGrabStopTime = v
                    autoGrabStopTime = v
                    F.saveConfig()
                end
            end)
        end
        do
            local row = mkRow(pageTOOLS, 34); mkLabel(row, "Delay Radius")
            table.insert(stealOptRows.semi, row)
            UI.delayRadInput = mkBox(row, Steal.AutoGrabDelayRadius, 54, 62, function(v)
                v = tonumber(v)
                if v and v > 0 then
                    Steal.AutoGrabDelayRadius = v
                    autoGrabDelayRadius = v
                    F.saveConfig()
                end
            end)
        end
        do
            local row = mkRow(pageTOOLS, 34); mkLabel(row, "Unpause Radius")
            table.insert(stealOptRows.semi, row)
            UI.unpauseRadInput = mkBox(row, autoGrabUnpauseRadius or Steal.AutoGrabUnpauseRadius or 12, 54, 62, function(v)
                v = tonumber(v)
                if v and v > 0 then
                    v = math.clamp(v, 1, 20)
                    autoGrabUnpauseRadius = v
                    Steal.AutoGrabUnpauseRadius = v
                    if UI.unpauseRadInput then UI.unpauseRadInput.Text = tostring(v) end
                    if F.updateShowRadius then F.updateShowRadius() end
                    F.saveConfig()
                end
            end)
        end
        do
            local row = mkRow(pageTOOLS, 34); mkLabel(row, "Show Radius")
            table.insert(stealOptRows.semi, row)
            local btn = Instance.new("TextButton", row)
            btn.Size = UDim2.new(0, 88, 0, 24)
            btn.Position = UDim2.new(1, -98, 0.5, -12)
            btn.BackgroundColor3 = Color3.fromRGB(18, 18, 20)
            btn.BorderSizePixel = 0
            btn.Text = showRadiusMode or "Off"
            btn.TextColor3 = COLORS.white
            btn.Font = Enum.Font.GothamBold
            btn.TextSize = 11
            btn.ZIndex = 5
            corner(btn, 8)
            UI.showRadiusBtn = btn
            local cycle = {"Off", "Unpause", "Radius", "Two"}
            btn.MouseButton1Click:Connect(function()
                local cur = showRadiusMode or "Off"
                local idx = 1
                for i, m in ipairs(cycle) do if m == cur then idx = i break end end
                idx = (idx % #cycle) + 1
                showRadiusMode = cycle[idx]
                btn.Text = showRadiusMode
                if F.updateShowRadius then F.updateShowRadius() end
                F.saveConfig()
            end)
        end

        do
            local row = mkRow(pageTOOLS, 30)
            local smallLbl = Instance.new("TextLabel", row)
            smallLbl.Size = UDim2.new(0.42, 0, 1, 0)
            smallLbl.Position = UDim2.new(0, 8, 0, 0)
            smallLbl.BackgroundTransparency = 1
            smallLbl.Text = "Gui Auto Steal"
            smallLbl.TextColor3 = COLORS.textDim
            smallLbl.Font = Enum.Font.Gotham
            smallLbl.TextSize = 10
            smallLbl.TextXAlignment = Enum.TextXAlignment.Left
            smallLbl.ZIndex = 5

            local styleBtn = Instance.new("TextButton", row)
            styleBtn.Size = UDim2.new(0, 96, 0, 22)
            styleBtn.Position = UDim2.new(1, -104, 0.5, -11)
            styleBtn.BackgroundColor3 = Color3.fromRGB(18, 18, 20)
            styleBtn.BorderSizePixel = 0
            local st = S.autoStealGuiStyle or "Old"
            styleBtn.Text = (st == "Personalizada") and "Personalizada" or ((st == "New") and "New" or "Old")
            styleBtn.TextColor3 = COLORS.white
            styleBtn.Font = Enum.Font.GothamBold
            styleBtn.TextSize = 10
            styleBtn.ZIndex = 6
            corner(styleBtn, 7)
            stroke(styleBtn, COLORS.stroke, 1, 0.55)
            UI.autoStealGuiStyleBtn = styleBtn

            local customPanel = Instance.new("Frame")
            customPanel.Name = "AutoStealCustomPanel"
            customPanel.Size = UDim2.new(0, 260, 0, 200)
            customPanel.Position = UDim2.new(0.5, -130, 0.5, -100)
            customPanel.AnchorPoint = Vector2.new(0, 0)
            customPanel.BackgroundColor3 = Color3.fromRGB(0, 0, 0)
            customPanel.BorderSizePixel = 0
            customPanel.Visible = false
            customPanel.ZIndex = 80
            customPanel.Parent = gui
            corner(customPanel, 12)
            stroke(customPanel, Color3.fromRGB(50, 50, 50), 1, 0.2)

            local cpTitle = Instance.new("TextLabel", customPanel)
            cpTitle.Size = UDim2.new(1, -40, 0, 28)
            cpTitle.Position = UDim2.new(0, 12, 0, 6)
            cpTitle.BackgroundTransparency = 1
            cpTitle.Text = "Personalizar GUI"
            cpTitle.TextColor3 = COLORS.white
            cpTitle.Font = Enum.Font.GothamBlack
            cpTitle.TextSize = 13
            cpTitle.TextXAlignment = Enum.TextXAlignment.Left
            cpTitle.ZIndex = 81

            local cpClose = Instance.new("TextButton", customPanel)
            cpClose.Size = UDim2.new(0, 24, 0, 24)
            cpClose.Position = UDim2.new(1, -30, 0, 8)
            cpClose.BackgroundColor3 = Color3.fromRGB(24, 24, 24)
            cpClose.Text = "×"
            cpClose.TextColor3 = COLORS.white
            cpClose.Font = Enum.Font.GothamBlack
            cpClose.TextSize = 14
            cpClose.BorderSizePixel = 0
            cpClose.ZIndex = 82
            corner(cpClose, 6)
            cpClose.MouseButton1Click:Connect(function() customPanel.Visible = false end)

            local function mkCustomSlider(y, label, minV, maxV, getV, setV)
                local lbl = Instance.new("TextLabel", customPanel)
                lbl.Size = UDim2.new(1, -24, 0, 16)
                lbl.Position = UDim2.new(0, 12, 0, y)
                lbl.BackgroundTransparency = 1
                lbl.Text = label
                lbl.TextColor3 = COLORS.textDim
                lbl.Font = Enum.Font.Gotham
                lbl.TextSize = 11
                lbl.TextXAlignment = Enum.TextXAlignment.Left
                lbl.ZIndex = 81
                local box = Instance.new("TextBox", customPanel)
                box.Size = UDim2.new(0, 56, 0, 22)
                box.Position = UDim2.new(1, -68, 0, y + 16)
                box.BackgroundColor3 = Color3.fromRGB(18, 18, 18)
                box.BorderSizePixel = 0
                box.Text = tostring(getV())
                box.TextColor3 = COLORS.white
                box.Font = Enum.Font.GothamBold
                box.TextSize = 11
                box.ZIndex = 81
                corner(box, 6)
                box.FocusLost:Connect(function()
                    local v = tonumber(box.Text)
                    if v then
                        v = math.clamp(v, minV, maxV)
                        setV(v)
                        box.Text = tostring(v)
                        if F.applyAutoStealGuiStyle then F.applyAutoStealGuiStyle() end
                        F.saveConfig()
                    end
                end)
                return box
            end

            S.asGuiCustom = S.asGuiCustom or { width = 360, height = 42, barH = 10, offset = 0.1 }
            mkCustomSlider(36, "Largura (px)", 200, 600, function() return S.asGuiCustom.width end, function(v) S.asGuiCustom.width = v end)
            mkCustomSlider(80, "Altura (px)", 28, 80, function() return S.asGuiCustom.height end, function(v) S.asGuiCustom.height = v end)
            mkCustomSlider(124, "Barra altura", 4, 24, function() return S.asGuiCustom.barH end, function(v) S.asGuiCustom.barH = v end)

            styleBtn.MouseButton1Click:Connect(function()
                if S._anyKeyListening then return end
                local cur = S.autoStealGuiStyle or "Old"
                if cur == "Old" then
                    S.autoStealGuiStyle = "Old"
                elseif cur == "New" then
                    S.autoStealGuiStyle = "Old"
                else
                    S.autoStealGuiStyle = "Old"
                end
                local st2 = S.autoStealGuiStyle
                styleBtn.Text = (st2 == "Personalizada") and "Personalizada" or st2
                if st2 == "Personalizada" then
                    customPanel.Visible = true
                else
                    customPanel.Visible = false
                end
                if F.applyAutoStealGuiStyle then F.applyAutoStealGuiStyle() end
                F.saveConfig()
            end)
        end

        task.defer(function()
            if UI.refreshStealOptionsVis then UI.refreshStealOptionsVis() end
        end)

        section(pageTOOLS, "Defense")
        VS.setMedusaVisual = mkToggle(pageTOOLS, "Medusa Counter", function(on)
            S.medusaCounterEnabled = on
            F.refreshMedusaWatch()
            F.saveConfig()
        end)
        do
            local row = mkRow(pageTOOLS, 34); mkLabel(row, "Instant Reset")
            mkKB(row, KB.InstaReset, function(k, isGp) if isGp then KB.InstaReset.gp = k; KB.InstaReset.kb = nil else KB.InstaReset.kb = k; KB.InstaReset.gp = nil end; F.saveConfig() end)
            local clk = Instance.new("TextButton", row); clk.Size = UDim2.new(0.55, 0, 1, 0); clk.BackgroundTransparency = 1; clk.Text = ""; clk.ZIndex = 2
            clk.Activated:Connect(function() if cursedInstaReset then cursedInstaReset() end end)
        end
                VS.setInfJumpVisual = mkToggle(pageTOOLS, "Infinite Jump", function(on)
            if on then F.startInfJump() else F.stopInfJump() end
            F.saveConfig()
        end)

        do
            local row = mkRow(pageTOOLS, 34)
            mkLabel(row, "Jump Mode")
            local btn = Instance.new("TextButton", row)
            btn.Size = UDim2.new(0, 72, 0, 24)
            btn.Position = UDim2.new(1, -80, 0.5, -12)
            btn.BackgroundColor3 = COLORS.row2
            btn.BorderSizePixel = 0
            btn.Text = ((S.infJumpMode or "hold") == "manual") and "Manual" or "Hold"
            btn.TextColor3 = COLORS.white
            btn.Font = Enum.Font.GothamBold
            btn.TextSize = 11
            btn.ZIndex = 5
            corner(btn, 8)
            stroke(btn, COLORS.stroke, 1, 0.6)
            UI.infJumpModeBtn = btn
            btn.MouseButton1Click:Connect(function()
                local nextMode = ((S.infJumpMode or "hold") == "manual") and "hold" or "manual"
                F.setInfJumpMode(nextMode)
                btn.Text = (nextMode == "manual") and "Manual" or "Hold"
                F.saveConfig()
            end)
        end
        VS.setAntiRagVisual = mkToggle(pageTOOLS, "Anti Ragdoll", function(on)
            S.antiRagdollEnabled = on
            if on then F.startAntiRagdoll() else F.stopAntiRagdoll() end
            F.saveConfig()
        end)
        do
            local row = mkRow(pageTOOLS, 34)
            mkLabel(row, "Ragdoll Mode")
            local btn = Instance.new("TextButton", row)
            btn.Size = UDim2.new(0, 72, 0, 24)
            btn.Position = UDim2.new(1, -80, 0.5, -12)
            btn.BackgroundColor3 = COLORS.row2
            btn.BorderSizePixel = 0
            btn.Text = (S.antiRagdollMode == 2) and "V2" or "V1"
            btn.TextColor3 = COLORS.white
            btn.Font = Enum.Font.GothamBold
            btn.TextSize = 11
            btn.ZIndex = 5
            corner(btn, 8)
            stroke(btn, COLORS.stroke, 1, 0.6)
            btn.MouseButton1Click:Connect(function()
                S.antiRagdollMode = (S.antiRagdollMode == 2) and 1 or 2
                btn.Text = (S.antiRagdollMode == 2) and "V2" or "V1"
                if S.antiRagdollEnabled then
                    F.stopAntiRagdoll()
                    F.startAntiRagdoll()
                end
                F.saveConfig()
            end)
        end

        do
            local expanded = false
            local headerRow = mkRow(pageTOOLS, 34)
            mkLabel(headerRow, "Anti Die System")
            local expandBtn = Instance.new("TextButton", headerRow)
            expandBtn.Size = UDim2.new(0, 72, 0, 24)
            expandBtn.Position = UDim2.new(1, -80, 0.5, -12)
            expandBtn.BackgroundColor3 = Color3.fromRGB(18, 18, 20)
            expandBtn.BorderSizePixel = 0
            expandBtn.Text = "OPEN"
            expandBtn.TextColor3 = COLORS.white
            expandBtn.Font = Enum.Font.GothamBold
            expandBtn.TextSize = 11
            expandBtn.ZIndex = 5
            corner(expandBtn, 8)
            stroke(expandBtn, COLORS.stroke, 1, 0.6)

            VS.setAntiDieVisual = mkToggle(pageTOOLS, "Anti Die", function(on)
                if on then F.startAntiDie() else F.stopAntiDie() end
            end)

            local antiDieRow, flingRow = nil, nil
            local children = pageTOOLS:GetChildren()
            for i = #children, 1, -1 do
                local ch = children[i]
                if ch:IsA("Frame") then
                    local lbl = ch:FindFirstChildWhichIsA("TextLabel")
                    if false and lbl and lbl.Text == "Fling Shield" and not flingRow then
                        flingRow = ch
                    elseif lbl and lbl.Text == "Anti Die" and not antiDieRow then
                        antiDieRow = ch
                    end
                end
                if antiDieRow and flingRow then break end
            end
            if antiDieRow then antiDieRow.Visible = false end
            if flingRow then flingRow.Visible = false end

            expandBtn.MouseButton1Click:Connect(function()
                expanded = not expanded
                expandBtn.Text = expanded and "CLOSE" or "OPEN"
                if antiDieRow then antiDieRow.Visible = expanded end
                if flingRow then flingRow.Visible = expanded end
            end)
        end

        VS.setNoPlayerCollisionVisual = mkToggle(pageTOOLS, "No Player Collision", function(on)
            if on then F.enableNoPlayerCollision() else F.disableNoPlayerCollision() end
            F.saveConfig()
        end)
        VS.setSafeModeVisual = mkToggle(pageTOOLS, "Safe Mode", function(on)
            S.safeModeEnabled = on
            F.saveConfig()
        end)
        VS.setAutoResetOnMedVisual = function() end -- removed

        VS.setBodyLockVisual = mkToggleKB(pageTOOLS, "Body Lock", KB.BodyLock, function(on)
            if on then
                if K7 and K7.startBodyLock then K7.startBodyLock()
                elseif startBodyLock then startBodyLock() end
            else
                if K7 and K7.stopBodyLock then K7.stopBodyLock(true)
                elseif stopBodyLock then stopBodyLock(true) end
            end
            F.saveConfig()
        end, function(k, isGp)
            if isGp then KB.BodyLock.gp = k; KB.BodyLock.kb = nil else KB.BodyLock.kb = k; KB.BodyLock.gp = nil end
            F.saveConfig()
        end)
        do
            local row = mkRow(pageTOOLS, 34); mkLabel(row, "Lock Radius")
            local rad = (K7 and K7.bodyLockRadius) or bodyLockRadius or 60
            mkBox(row, rad, 54, 62, function(v)
                if v >= 5 and v <= 300 then
                    if K7 then K7.bodyLockRadius = v end
                    bodyLockRadius = v
                end
                F.saveConfig()
            end)
        end

        section(pageVIEW, "Visual")
        VS.setAntiLagVisual = function() end -- removed

        VS.setFpsUltraBoostVisual = function() end -- removed

        VS.setStretchRezVisual = mkToggle(pageVIEW, "Stretch Rez", function(on)
            S.stretchRezAmount = 0.7
            if on then enableStretchRez() else disableStretchRez() end
            F.saveConfig()
        end)
        VS.setCustomGearsVisual = mkToggle(pageVIEW, "Custom Gears", function(on)
            F.setCustomGears(on)
        end)
        VS.setCustomGearsV2Visual = function() end -- removed

        VS.setCustomSoundVisual = mkToggle(pageVIEW, "Custom Sound", function(on)
            F.setCustomSound(on)
        end)
        VS.setXrayGearsVisual = mkToggle(pageVIEW, "Xray Gears", function(on)
            F.setXrayGears(on)
        end)
        VS.setXrayBaseVisual = function() end -- removed


        -- Level/Armazem removed
        VS.setUnwalkVisual = mkToggle(pageVIEW, "Unwalk", function(on)
            S.unwalkEnabled = on
            if on then F.startUnwalk() else F.stopUnwalk() end
        end)
        VS.setRemoveAccVisual = mkToggle(pageVIEW, "Remove Accessories", function(on)
            if on then F.startRemoveAccessories() else F.stopRemoveAccessories() end
        end)
        VS.setKeyboardOverlayVisual = function() end -- removed

        -- Custom Item panel removed
        section(pageVIEW, "ESP")
        VS.setLineESPVisual = mkToggle(pageVIEW, "Player ESP", function(on)
            K7.lineESPEnabled = on
            K7.speedESPEnabled = on
            if on then startPlayerESP() else stopPlayerESP() end
            F.saveConfig()
        end)
        VS.setHitboxEspVisual = mkToggle(pageVIEW, "Hitbox ESP", function(on)
            S.espHitboxEnabled = on and true or false
            if on then F.startHitboxESP() else F.stopHitboxESP() end
            F.saveConfig()
        end)

        VS.setShowTrackerVisual = mkToggle(pageVIEW, "ESP Trace", function(on)
            S.showTracerEnabled = on
            if on then startTracerESP() else stopTracerESP() end
            if F.setShowTracer then pcall(F.setShowTracer, on) end
            F.saveConfig()
        end)
        VS.setShowTracerVisual = VS.setShowTrackerVisual
        VS.setSpeedESPVisual = function(on)
            if VS.setLineESPVisual then VS.setLineESPVisual(on) end
        end
        VS.setRagdollCdVisual = mkToggle(pageVIEW, "Ragdoll Countdown", function(on)
            F.setRagdollCountdown(on)
            F.saveConfig()
        end)
        VS.setAntiAntiAntiDesyncVisual = function() end -- removed
        VS.setEspHitboxVisual = function() end -- removed

        VS.setFovCustomVisual = mkToggle(pageVIEW, "FOV Custom", function(on)
            if K7 then
                K7.fovEnabled = on and true or false
                pcall(function() if K7.applyFOV then K7.applyFOV() end end)
            end
            F.saveConfig()
        end)
        do
            local row = mkRow(pageVIEW, 34); mkLabel(row, "FOV")
            local box = Instance.new("TextBox", row)
            box.Size = UDim2.new(0, 58, 0, 24)
            box.Position = UDim2.new(1, -68, 0.5, -12)
            box.BackgroundColor3 = Color3.fromRGB(18, 18, 20)
            box.BorderSizePixel = 0
            box.Text = tostring((K7 and K7.fovValue) or 70)
            box.TextColor3 = COLORS.white
            box.Font = Enum.Font.GothamBold
            box.TextSize = 11
            box.ClearTextOnFocus = false
            box.ZIndex = 5
            corner(box, 8)
            UI.fovBox = box
            box.FocusLost:Connect(function()
                local n = tonumber(box.Text)
                if n then
                    n = math.clamp(n, 1, 120)
                    box.Text = tostring(n)
                    if K7 then
                        K7.fovValue = n
                        if K7.fovEnabled and K7.applyFOV then pcall(K7.applyFOV) end
                    end
                    F.saveConfig()
                end
            end)
        end

        -- Anti Lag (Syncrypt method)
        VS.setAntiLagVisual = mkToggle(pageVIEW, "Anti Lag", function(on)
            if on then F.enableAntiLag() else F.disableAntiLag() end
            F.saveConfig()
        end)
        VS.setUltraFpsVisual = mkToggle(pageVIEW, "Ultra Boost FPS", function(on)
            if on then F.startUltraFpsBoost() else F.stopUltraFpsBoost() end
            F.saveConfig()
        end)


        -- Sky Theme removed
        VS.setNoSkyVisual = function() end -- removed

        -- duplicate FOV removed
        VS.setFOVVisual = function() end -- removed




        section(pageSYS, "Character")
        VS.setHeadlessVisual = mkToggle(pageSYS, "Headless", function(on)
            K7.headlessEnabled = on
            K7.applyHeadless(LP.Character, on)
            F.saveConfig()
        end)
        VS.setKorbloxVisual = mkToggle(pageSYS, "Korblox", function(on)
            K7.korbloxEnabled = on
            K7.applyKorblox(LP.Character, on)
            F.saveConfig()
        end)
        VS.setAntiDropVisual = mkToggle(pageSYS, "Anti Drop", function(on)
            if on then
                F.startAntiDrop()
            else
                F.stopAntiDrop()
            end
            F.saveConfig()
        end)
        section(pageSYS, "Animation Pack")
        local function setExclusiveAnimPack(packName, enabled)
            if enabled then
                K7.animPackEnabled = true
                K7.animPackName = packName
                if K7.ANIM_PACK_LIST then
                    for i, n in ipairs(K7.ANIM_PACK_LIST) do
                        if n == packName then K7.animPackIndex = i; break end
                    end
                end
                pcall(function() K7.applyAnimPack(packName) end)
            else
                if K7.animPackName == packName then
                    K7.animPackEnabled = false
                    pcall(function() if K7.resetAnimPack then K7.resetAnimPack() end end)
                end
            end
            -- sync exclusive visuals
            if VS.setVampireAnimVisual then VS.setVampireAnimVisual(K7.animPackEnabled and K7.animPackName == "Vampire") end
            if VS.setAmazonAnimVisual then VS.setAmazonAnimVisual(K7.animPackEnabled and K7.animPackName == "Amazon Unboxed") end
            if VS.setZombieAnimVisual then VS.setZombieAnimVisual(K7.animPackEnabled and K7.animPackName == "Zombie") end
            F.saveConfig()
        end
        VS.setVampireAnimVisual = mkToggle(pageSYS, "Vampire Animation", function(on)
            setExclusiveAnimPack("Vampire", on)
        end)
        VS.setAmazonAnimVisual = mkToggle(pageSYS, "Amazon Unboxed", function(on)
            setExclusiveAnimPack("Amazon Unboxed", on)
        end)
        VS.setZombieAnimVisual = mkToggle(pageSYS, "Zombie Pack", function(on)
            setExclusiveAnimPack("Zombie", on)
        end)
        section(pageSYS, "Interface")
        do
            local row = mkRow(pageSYS, 34); mkLabel(row, "Hide UI")
            mkKB(row, KB.GuiHide, function(k, isGp) if isGp then KB.GuiHide.gp = k; KB.GuiHide.kb = nil else KB.GuiHide.kb = k; KB.GuiHide.gp = nil end; F.saveConfig() end)
        end
        section(pageSYS, "Appearance")
        do
            local row = mkRow(pageSYS, 42)
            mkLabel(row, "Accent Color")
            local picker = Instance.new("Frame")
            picker.Name = "ColorThemePicker"
            picker.Size = UDim2.new(0, 176, 0, 22)
            picker.Position = UDim2.new(1, -184, 0.5, -11)
            picker.BackgroundTransparency = 1
            picker.ZIndex = 6
            picker.Parent = row
            local lay = Instance.new("UIListLayout")
            lay.FillDirection = Enum.FillDirection.Horizontal
            lay.Padding = UDim.new(0, 5)
            lay.VerticalAlignment = Enum.VerticalAlignment.Center
            lay.HorizontalAlignment = Enum.HorizontalAlignment.Right
            lay.SortOrder = Enum.SortOrder.LayoutOrder
            lay.Parent = picker
            local selectedPill = nil
            for idx, preset in ipairs(THEME_PRESETS) do
                local btn = Instance.new("TextButton")
                btn.Name = preset.name
                btn.LayoutOrder = idx
                btn.Size = UDim2.new(0, 16, 0, 16)
                btn.BackgroundColor3 = preset.color
                btn.BorderSizePixel = 0
                btn.Text = ""
                btn.AutoButtonColor = false
                btn.ZIndex = 7
                btn.Parent = picker
                local c = Instance.new("UICorner")
                c.CornerRadius = UDim.new(1, 0)
                c.Parent = btn
                local st = Instance.new("UIStroke")
                st.Color = Color3.fromRGB(255, 255, 255)
                st.Thickness = 1.2
                st.Transparency = 0.6
                st.Parent = btn
                do
                local presetColor = preset.color
                local presetName = preset.name
                local function onPick()
                    if selectedPill then
                        local os = selectedPill:FindFirstChildOfClass("UIStroke")
                        if os then os.Transparency = 0.6; os.Thickness = 1.2 end
                        TS:Create(selectedPill, TweenInfo.new(0.12), {Size = UDim2.new(0, 16, 0, 16)}):Play()
                    end
                    selectedPill = btn
                    st.Transparency = 0
                    st.Thickness = 2
                    TS:Create(btn, TweenInfo.new(0.12, Enum.EasingStyle.Back), {Size = UDim2.new(0, 18, 0, 18)}):Play()
                    applyThemeColor(presetColor, presetName)
                    pcall(function() if F.applyEspTheme then F.applyEspTheme(presetColor) end end)
                    if bgAsset and bgAsset.Image ~= "" then
                        bgAsset.ImageColor3 = Color3.new(
                            0.75 + presetColor.R * 0.25,
                            0.75 + presetColor.G * 0.25,
                            0.75 + presetColor.B * 0.25
                        )
                    end
                    pcall(function() F.saveConfig() end)
                end
                btn.MouseButton1Click:Connect(onPick)
                btn.Activated:Connect(onPick)
                end
                if preset.name == (S.currentThemeName or currentThemeName) then
                    selectedPill = btn
                    st.Transparency = 0
                    st.Thickness = 2
                    btn.Size = UDim2.new(0, 18, 0, 18)
                end
            end
        end
        do
            local row = mkRow(pageSYS, 34)
            mkLabel(row, "Background")
            local btn = Instance.new("TextButton", row)
            btn.Size = UDim2.new(0, 96, 0, 24)
            btn.Position = UDim2.new(1, -104, 0.5, -12)
            btn.BackgroundColor3 = Color3.fromRGB(18, 18, 20)
            btn.BorderSizePixel = 0
            btn.Text = "None"
            btn.TextColor3 = COLORS.white
            btn.Font = Enum.Font.GothamBold
            btn.TextSize = 10
            btn.ZIndex = 6
            corner(btn, 8)
            stroke(btn, COLORS.stroke, 1, 0.55)
            UI.bgPresetBtn = btn
            local bgIndex = 1
            for i, p in ipairs(BG_PRESETS) do
                if p.id == (S.currentBgId or "") or p.name == (S.currentBgId or "") then
                    bgIndex = i
                    btn.Text = p.name
                end
            end
            btn.Activated:Connect(function()
                if S._anyKeyListening then return end
                bgIndex = (bgIndex % #BG_PRESETS) + 1
                local preset = BG_PRESETS[bgIndex]
                btn.Text = "..."
                task.spawn(function()
                    applyBackground(preset.id ~= "" and preset.id or "", false)
                    btn.Text = preset.name
                end)
            end)
            -- apply saved bg on build
            task.defer(function()
                if S.currentBgId and S.currentBgId ~= "" then
                    applyBackground(S.currentBgId, true)
                end
            end)
        end
        do
            local row = mkRow(pageSYS, 34)
            mkLabel(row, "BG Intensity")
            local box = Instance.new("TextBox", row)
            box.Size = UDim2.new(0, 58, 0, 24)
            box.Position = UDim2.new(1, -68, 0.5, -12)
            box.BackgroundColor3 = Color3.fromRGB(18, 18, 20)
            box.BorderSizePixel = 0
            box.Text = tostring(math.floor((S.bgIntensity or 0.72) * 100))
            box.TextColor3 = COLORS.white
            box.Font = Enum.Font.GothamBold
            box.TextSize = 11
            box.ZIndex = 6
            box.ClearTextOnFocus = false
            corner(box, 8)
            box.FocusLost:Connect(function()
                local n = tonumber(box.Text)
                if n then
                    S.bgIntensity = math.clamp(n / 100, 0.15, 1)
                    box.Text = tostring(math.floor(S.bgIntensity * 100))
                    if S.currentBgId and S.currentBgId ~= "" then
                        applyBackground(S.currentBgId, true)
                    end
                    F.saveConfig()
                else
                    box.Text = tostring(math.floor((S.bgIntensity or 0.72) * 100))
                end
            end)
        end
        do
            local row = mkRow(pageSYS, 34)
            mkLabel(row, "Color Overlay")
            local btn = Instance.new("TextButton", row)
            btn.Size = UDim2.new(0, 96, 0, 24)
            btn.Position = UDim2.new(1, -104, 0.5, -12)
            btn.BackgroundColor3 = Color3.fromRGB(18, 18, 20)
            btn.BorderSizePixel = 0
            btn.Text = S.kbOverlayColorName or "White"
            btn.TextColor3 = COLORS.white
            btn.Font = Enum.Font.GothamBold
            btn.TextSize = 10
            btn.ZIndex = 6
            corner(btn, 8)
            stroke(btn, COLORS.stroke, 1, 0.55)
            UI.kbOverlayColorBtn = btn
            local colors = {
                {name="White", col=Color3.fromRGB(255,255,255)},
                {name="Black", col=Color3.fromRGB(0,0,0)},
                {name="Purple", col=Color3.fromRGB(170,80,255)},
                {name="Gray", col=Color3.fromRGB(140,140,140)},
                {name="Orange", col=Color3.fromRGB(255,165,0)},
                {name="Red", col=Color3.fromRGB(255,90,90)},
                {name="Green", col=Color3.fromRGB(60,200,120)}
            }
            btn.MouseButton1Click:Connect(function()
                if S._anyKeyListening then return end
                local cur = S.kbOverlayColorName or "White"
                local idx = 1
                for i, c in ipairs(colors) do
                    if c.name == cur then idx = i break end
                end
                idx = (idx % #colors) + 1
                S.kbOverlayColorName = colors[idx].name
                S.kbOverlayColor = colors[idx].col
                btn.Text = colors[idx].name
                if S.keyboardOverlayEnabled then
                    F.stopKeyboardOverlay()
                    F.startKeyboardOverlay()
                end
                F.saveConfig()
            end)
        end
        do
            local row = mkRow(pageSYS, 34)
            mkLabel(row, "Background")
            local btn = Instance.new("TextButton", row)
            btn.Size = UDim2.new(0, 96, 0, 24)
            btn.Position = UDim2.new(1, -104, 0.5, -12)
            btn.BackgroundColor3 = Color3.fromRGB(18, 18, 20)
            btn.BorderSizePixel = 0
            local function getCurName()
                for _, p in ipairs(BG_PRESETS) do
                    if p.id == S.currentBgId then return p.name end
                end
                return "None"
            end
            btn.Text = getCurName()
            btn.TextColor3 = COLORS.white
            btn.Font = Enum.Font.GothamBold
            btn.TextSize = 10
            btn.ZIndex = 6
            corner(btn, 8)
            stroke(btn, COLORS.stroke, 1, 0.55)
            UI.bgBtn = btn
            btn.MouseButton1Click:Connect(function()
                if S._anyKeyListening then return end
                local cur = S.currentBgId or ""
                local idx = 1
                for i, p in ipairs(BG_PRESETS) do
                    if p.id == cur then idx = i break end
                end
                idx = (idx % #BG_PRESETS) + 1
                applyBackground(BG_PRESETS[idx].id)
                btn.Text = BG_PRESETS[idx].name
            end)
        end
        do
            local row = mkRow(pageSYS, 34)
            mkLabel(row, "GUI Size")
            local minusBtn = Instance.new("TextButton", row)
            minusBtn.Size = UDim2.new(0, 28, 0, 24)
            minusBtn.Position = UDim2.new(1, -112, 0.5, -12)
            minusBtn.BackgroundColor3 = Color3.fromRGB(18, 18, 20)
            minusBtn.BackgroundTransparency = 0.1
            minusBtn.BorderSizePixel = 0
            minusBtn.Text = "−"
            minusBtn.TextColor3 = COLORS.white
            minusBtn.Font = Enum.Font.GothamBold
            minusBtn.TextSize = 16
            minusBtn.ZIndex = 5
            corner(minusBtn, 8)
            stroke(minusBtn, COLORS.stroke, 1, 0.75)
            local scaleLbl = Instance.new("TextLabel", row)
            scaleLbl.Size = UDim2.new(0, 44, 0, 24)
            scaleLbl.Position = UDim2.new(1, -80, 0.5, -12)
            scaleLbl.BackgroundColor3 = Color3.fromRGB(18, 18, 20)
            scaleLbl.BackgroundTransparency = 0.15
            scaleLbl.BorderSizePixel = 0
            scaleLbl.Text = "100%"
            scaleLbl.TextColor3 = COLORS.white
            scaleLbl.Font = Enum.Font.GothamBold
            scaleLbl.TextSize = 11
            scaleLbl.ZIndex = 5
            corner(scaleLbl, 8)
            UI.guiScaleLbl = scaleLbl
            local plusBtn = Instance.new("TextButton", row)
            plusBtn.Size = UDim2.new(0, 28, 0, 24)
            plusBtn.Position = UDim2.new(1, -32, 0.5, -12)
            plusBtn.BackgroundColor3 = Color3.fromRGB(18, 18, 20)
            plusBtn.BackgroundTransparency = 0.1
            plusBtn.BorderSizePixel = 0
            plusBtn.Text = "+"
            plusBtn.TextColor3 = COLORS.white
            plusBtn.Font = Enum.Font.GothamBold
            plusBtn.TextSize = 16
            plusBtn.ZIndex = 5
            corner(plusBtn, 8)
            stroke(plusBtn, COLORS.stroke, 1, 0.75)

            minusBtn.Activated:Connect(function()
                if S._anyKeyListening then return end
                if _G._Hub073ApplyGuiScale then
                    _G._Hub073ApplyGuiScale((_G._Hub073GetGuiScale and _G._Hub073GetGuiScale() or 1) - 0.1)
                end
            end)
            plusBtn.Activated:Connect(function()
                if S._anyKeyListening then return end
                if _G._Hub073ApplyGuiScale then
                    _G._Hub073ApplyGuiScale((_G._Hub073GetGuiScale and _G._Hub073GetGuiScale() or 1) + 0.1)
                end
            end)
        end
        VS.setLockGuiVisual = mkToggle(pageSYS, "Lock GUI", function(on)
            S.guiLocked = on and true or false
            F.saveConfig()
        end)
        VS.setLockMobileButtonsVisual = mkToggle(pageSYS, "Lock Mobile Buttons", function(on)
            S.lockMobileButtons = on and true or false
            F.saveConfig()
        end)
        VS.setHideMobileVisual = mkToggle(pageSYS, "Hide Mobile Buttons", function(on)
            S.hideMobileButtons = on and true or false
            if F.applyMobileButtonsHidden then F.applyMobileButtonsHidden() end
            F.saveConfig()
        end)
        VS.setLaggerPanelVisual = mkToggle(pageSYS, "Lagger", function(on)
            if F.setLaggerPanel then F.setLaggerPanel(on) end
            F.saveConfig()
        end)


        if S.autoSaveEnabled == nil then S.autoSaveEnabled = true end
        do
            local row = mkRow(pageSYS, 28)
            local st = Instance.new("TextLabel", row)
            st.Size = UDim2.new(1, -12, 1, 0)
            st.Position = UDim2.new(0, 10, 0, 0)
            st.BackgroundTransparency = 1
            st.Text = "Save: ready"
            st.TextColor3 = COLORS.textDim
            st.Font = Enum.Font.GothamMedium
            st.TextSize = 11
            st.TextXAlignment = Enum.TextXAlignment.Left
            st.ZIndex = 5
            UI.saveStatusLbl = st
        end
        task.defer(function()
            if VS.setLockGuiVisual then VS.setLockGuiVisual(S.guiLocked) end
        end)
        section(pageSYS, "Mobile")
        VS.setLockMobileButtonsVisual = function() end -- removed

        task.defer(function()
            if VS.setLockMobileButtonsVisual then VS.setLockMobileButtonsVisual(S.lockMobileButtons == true) end
        end)
        VS.setHideMobileVisual = function() end -- removed

        do
            local row = mkRow(pageSYS, 34)
            mkLabel(row, "Mobile Buttons Size")
            local minusBtn = Instance.new("TextButton", row)
            minusBtn.Size = UDim2.new(0, 28, 0, 24)
            minusBtn.Position = UDim2.new(1, -112, 0.5, -12)
            minusBtn.BackgroundColor3 = Color3.fromRGB(18, 18, 20)
            minusBtn.BorderSizePixel = 0
            minusBtn.Text = "−"
            minusBtn.TextColor3 = COLORS.white
            minusBtn.Font = Enum.Font.GothamBold
            minusBtn.TextSize = 16
            minusBtn.ZIndex = 5
            corner(minusBtn, 8)
            local sizeLbl = Instance.new("TextLabel", row)
            sizeLbl.Size = UDim2.new(0, 44, 0, 24)
            sizeLbl.Position = UDim2.new(1, -80, 0.5, -12)
            sizeLbl.BackgroundColor3 = Color3.fromRGB(18, 18, 20)
            sizeLbl.BorderSizePixel = 0
            sizeLbl.Text = string.format("%.2f", S.mobileButtonScale or 0.70)
            sizeLbl.TextColor3 = COLORS.white
            sizeLbl.Font = Enum.Font.GothamBold
            sizeLbl.TextSize = 11
            sizeLbl.ZIndex = 5
            corner(sizeLbl, 8)
            UI.mobileSizeLbl = sizeLbl
            local plusBtn = Instance.new("TextButton", row)
            plusBtn.Size = UDim2.new(0, 28, 0, 24)
            plusBtn.Position = UDim2.new(1, -32, 0.5, -12)
            plusBtn.BackgroundColor3 = Color3.fromRGB(18, 18, 20)
            plusBtn.BorderSizePixel = 0
            plusBtn.Text = "+"
            plusBtn.TextColor3 = COLORS.white
            plusBtn.Font = Enum.Font.GothamBold
            plusBtn.TextSize = 16
            plusBtn.ZIndex = 5
            corner(plusBtn, 8)
            local function refreshSizeLbl()
                if UI.mobileSizeLbl then UI.mobileSizeLbl.Text = string.format("%.2f", S.mobileButtonScale or 0.70) end
            end
            minusBtn.Activated:Connect(function()
                S.mobileButtonScale = math.clamp((S.mobileButtonScale or 0.70) - 0.05, 0.30, 1.35)
                F.applyMobileButtonSize(); refreshSizeLbl(); F.saveConfig()
            end)
            plusBtn.Activated:Connect(function()
                S.mobileButtonScale = math.clamp((S.mobileButtonScale or 0.70) + 0.05, 0.30, 1.35)
                F.applyMobileButtonSize(); refreshSizeLbl(); F.saveConfig()
            end)
        end
        -- reset mobile removed
        do
            local row = mkRow(pageSYS, 34)
            mkLabel(row, "Save Config")
            local saveBtn = Instance.new("TextButton", row)
            saveBtn.Size = UDim2.new(0, 72, 0, 24)
            saveBtn.Position = UDim2.new(1, -82, 0.5, -12)
            saveBtn.BackgroundColor3 = Color3.fromRGB(18, 18, 20)
            saveBtn.BackgroundTransparency = 0.1
            saveBtn.BorderSizePixel = 0
            saveBtn.Text = "SAVE"
            saveBtn.TextColor3 = COLORS.white
            saveBtn.Font = Enum.Font.GothamBold
            saveBtn.TextSize = 11
            saveBtn.ZIndex = 5
            corner(saveBtn, 8)
            stroke(saveBtn, COLORS.stroke, 1, 0.75)
            saveBtn.Activated:Connect(function()
                if S._anyKeyListening then return end
                saveBtn.Text = "..."
                local ok = F.saveConfig(true)
                pcall(function() if F.saveMobileButtonsOnly then F.saveMobileButtonsOnly() end end)
                saveBtn.Text = ok and "SAVED" or "FAIL"
                if UI.saveStatusLbl then
                    UI.saveStatusLbl.Text = ok and ("Saved " .. os.date("%H:%M:%S")) or "Save failed"
                    UI.saveStatusLbl.TextColor3 = ok and Color3.fromRGB(100, 220, 160) or Color3.fromRGB(255, 120, 120)
                end
                task.delay(1.4, function()
                    if saveBtn and saveBtn.Parent then saveBtn.Text = "SAVE" end
                end)
            end)
        end
        do
            local row = mkRow(pageSYS, 34)
            mkLabel(row, "Reset All Configs")
            local resetCfgBtn = Instance.new("TextButton", row)
            resetCfgBtn.Size = UDim2.new(0, 72, 0, 24)
            resetCfgBtn.Position = UDim2.new(1, -82, 0.5, -12)
            resetCfgBtn.BackgroundColor3 = Color3.fromRGB(238, 80, 80)
            resetCfgBtn.BackgroundTransparency = 0
            resetCfgBtn.BorderSizePixel = 0
            resetCfgBtn.Text = "RESET"
            resetCfgBtn.TextColor3 = Color3.fromRGB(255, 255, 255)
            resetCfgBtn.Font = Enum.Font.GothamBold
            resetCfgBtn.TextSize = 11
            resetCfgBtn.ZIndex = 5
            corner(resetCfgBtn, 8)
            stroke(resetCfgBtn, Color3.fromRGB(255, 140, 140), 1, 0.45)
            resetCfgBtn.Activated:Connect(function()
                if S._anyKeyListening then return end
                resetCfgBtn.Text = "..."
                local ok = F.resetAllConfig()
                resetCfgBtn.Text = ok and "DONE" or "OK"
                task.delay(1.5, function()
                    if resetCfgBtn and resetCfgBtn.Parent then resetCfgBtn.Text = "RESET" end
                end)
            end)
        end
        do
            local row = mkRow(pageSYS, 28)
            local st = Instance.new("TextLabel", row)
            st.Size = UDim2.new(1, -12, 1, 0)
            st.Position = UDim2.new(0, 10, 0, 0)
            st.BackgroundTransparency = 1
            st.Text = "Save: ready"
            st.TextColor3 = COLORS.textDim
            st.Font = Enum.Font.GothamMedium
            st.TextSize = 11
            st.TextXAlignment = Enum.TextXAlignment.Left
            st.ZIndex = 5
            UI.saveStatusLbl = st
        end
        task.defer(function()
            if UI.saveStatusLbl then
                UI.saveStatusLbl.Text = "Save: manual only"
                UI.saveStatusLbl.TextColor3 = COLORS.textDim
            end
        end)

        section(pageKEYS, "Keybinds")
        do local row = mkRow(pageKEYS, 34); mkLabel(row, "Speed Key"); mkKB(row, KB.SpeedToggle, function(k, isGp) if isGp then KB.SpeedToggle.gp = k; KB.SpeedToggle.kb = nil else KB.SpeedToggle.kb = k; KB.SpeedToggle.gp = nil end; F.saveConfig() end) end
        do local row = mkRow(pageKEYS, 34); mkLabel(row, "Lagger Key"); mkKB(row, KB.LaggerToggle, function(k, isGp) if isGp then KB.LaggerToggle.gp = k; KB.LaggerToggle.kb = nil else KB.LaggerToggle.kb = k; KB.LaggerToggle.gp = nil end; F.saveConfig() end) end
        section(pageKEYS, "Combat Keybinds")
        do local row = mkRow(pageKEYS, 34); mkLabel(row, "Auto Bat Key"); mkKB(row, KB.AutoBat, function(k, isGp) if isGp then KB.AutoBat.gp = k; KB.AutoBat.kb = nil else KB.AutoBat.kb = k; KB.AutoBat.gp = nil end; F.saveConfig() end) end
        do local row = mkRow(pageKEYS, 34); mkLabel(row, "TP Bat Key"); mkKB(row, KB.TPBat, function(k, isGp)
            if isGp then KB.TPBat.gp = k; KB.TPBat.kb = nil; TP_BAT_CONTROLLER = k
            else KB.TPBat.kb = k; KB.TPBat.gp = nil; TP_BAT_KEY = k end
            F.saveConfig()
        end) end
        do local row = mkRow(pageKEYS, 34); mkLabel(row, "Insta Reset Key"); mkKB(row, KB.InstaReset, function(k, isGp) if isGp then KB.InstaReset.gp = k; KB.InstaReset.kb = nil else KB.InstaReset.kb = k; KB.InstaReset.gp = nil end; F.saveConfig() end) end
        do local row = mkRow(pageKEYS, 34); mkLabel(row, "Body Lock Key"); mkKB(row, KB.BodyLock, function(k, isGp) if isGp then KB.BodyLock.gp = k; KB.BodyLock.kb = nil else KB.BodyLock.kb = k; KB.BodyLock.gp = nil end; F.saveConfig() end) end
        section(pageKEYS, "Movement Keybinds")
        do local row = mkRow(pageKEYS, 34); mkLabel(row, "Auto Left Key"); mkKB(row, KB.AutoLeft, function(k, isGp) if isGp then KB.AutoLeft.gp = k; KB.AutoLeft.kb = nil else KB.AutoLeft.kb = k; KB.AutoLeft.gp = nil end; F.saveConfig() end) end
        do local row = mkRow(pageKEYS, 34); mkLabel(row, "Auto Right Key"); mkKB(row, KB.AutoRight, function(k, isGp) if isGp then KB.AutoRight.gp = k; KB.AutoRight.kb = nil else KB.AutoRight.kb = k; KB.AutoRight.gp = nil end; F.saveConfig() end) end
        do local row = mkRow(pageKEYS, 34); mkLabel(row, "Drop Key"); mkKB(row, KB.DropBrainrot, function(k, isGp) if isGp then KB.DropBrainrot.gp = k; KB.DropBrainrot.kb = nil else KB.DropBrainrot.kb = k; KB.DropBrainrot.gp = nil end; F.saveConfig() end) end
        do local row = mkRow(pageKEYS, 34); mkLabel(row, "TP Down Key"); mkKB(row, KB.TPFloor, function(k, isGp) if isGp then KB.TPFloor.gp = k; KB.TPFloor.kb = nil else KB.TPFloor.kb = k; KB.TPFloor.gp = nil end; F.saveConfig() end) end
        do local row = mkRow(pageKEYS, 34); mkLabel(row, "Hide UI Key"); mkKB(row, KB.GuiHide, function(k, isGp) if isGp then KB.GuiHide.gp = k; KB.GuiHide.kb = nil else KB.GuiHide.kb = k; KB.GuiHide.gp = nil end; F.saveConfig() end) end

        UIS.InputBegan:Connect(function(input, gpe)
            if S._anyKeyListening then return end
            if input.UserInputType == Enum.UserInputType.Keyboard then
                if gpe or UIS:GetFocusedTextBox() then return end
            elseif not isGamepadInput(input) then return end
            if not isBindableInput(input) then return end
            local kc = input.KeyCode
            if kbMatch(KB.LaggerToggle, kc) then
                F.toggleLaggerMode()
                F.saveConfig()
            elseif kbMatch(KB.SpeedToggle, kc) then
                F.toggleCarryMode()
                F.saveConfig()
            elseif kbMatch(KB.DropBrainrot, kc) then F.runDrop()
            elseif kbMatch(KB.TPFloor, kc) then F.runTPFloor()
            elseif kbMatch(KB.InstaReset, kc) then if cursedInstaReset then cursedInstaReset() end
            elseif kbMatch(KB.AutoLeft, kc) then
                S.autoLeftEnabled = not S.autoLeftEnabled
                if S.autoLeftEnabled then F.queueAutoLeftStart() else F.stopAutoLeft() end
                if S.autoLeftSetVisual then S.autoLeftSetVisual(S.autoLeftEnabled) end
            elseif kbMatch(KB.AutoRight, kc) then
                S.autoRightEnabled = not S.autoRightEnabled
                if S.autoRightEnabled then F.queueAutoRightStart() else F.stopAutoRight() end
                if S.autoRightSetVisual then S.autoRightSetVisual(S.autoRightEnabled) end
            elseif kbMatch(KB.AutoBat, kc) then
                if not S.autoBatEnabled then
                    F.queueAutoBatStart()
                    if S.autoBatSetVisual then S.autoBatSetVisual(true) end
                else
                    S.autoBatEnabled = false; S.disableAutoBat()
                    if S.autoBatSetVisual then S.autoBatSetVisual(false) end
                end
                elseif kbMatch(KB.TPBat, kc) then
                setTPBatState(not tpBatEnabled)
            elseif kbMatch(KB.GuiHide, kc) then
                if main.Visible then hideGui() else showGui() end
            elseif kbMatch(KB.BodyLock, kc) then
                local on = not (K7 and K7.bodyLockEnabled)
                if on then
                    if K7 and K7.startBodyLock then K7.startBodyLock()
                    elseif startBodyLock then startBodyLock() end
                else
                    if K7 and K7.stopBodyLock then K7.stopBodyLock(true)
                    elseif stopBodyLock then stopBodyLock(true) end
                end
                if VS.setBodyLockVisual then pcall(function() VS.setBodyLockVisual(on) end) end
                F.saveConfig()
            end
        end)

        do
            local pbFrame = Instance.new("Frame", gui)
            pbFrame.Name = "StealBar"
            pbFrame.Size = UDim2.new(0, 220, 0, 56)
            pbFrame.Position = UDim2.new(0.5, -110, 1, -72)
            pbFrame.BackgroundColor3 = Color3.fromRGB(0, 0, 0)
            pbFrame.BackgroundTransparency = 0
            pbFrame.BorderSizePixel = 0
            pbFrame.Active = true
            pbFrame.ZIndex = 50
            pbFrame.Visible = false
            corner(pbFrame, 12)
            local st = Instance.new("UIStroke")
            st.Color = Color3.fromRGB(40, 40, 48)
            st.Thickness = 1.2
            st.Parent = pbFrame
            UI.stealBarFrame = pbFrame

            progressPct = Instance.new("TextLabel", pbFrame)
            progressPct.Name = "Pct"
            progressPct.Size = UDim2.new(0.45, 0, 0, 20)
            progressPct.Position = UDim2.new(0, 12, 0, 6)
            progressPct.BackgroundTransparency = 1
            progressPct.ZIndex = 54
            progressPct.Text = "0%"
            progressPct.TextColor3 = Color3.fromRGB(255, 255, 255)
            progressPct.Font = Enum.Font.GothamBold
            progressPct.TextSize = 14
            progressPct.TextXAlignment = Enum.TextXAlignment.Left

            progressRadLbl = Instance.new("TextLabel", pbFrame)
            progressRadLbl.Name = "Rad"
            progressRadLbl.Size = UDim2.new(0.5, -12, 0, 20)
            progressRadLbl.Position = UDim2.new(0.5, 0, 0, 6)
            progressRadLbl.BackgroundTransparency = 1
            progressRadLbl.ZIndex = 54
            progressRadLbl.Text = "Radius: 20"
            progressRadLbl.TextColor3 = Color3.fromRGB(200, 200, 210)
            progressRadLbl.Font = Enum.Font.GothamMedium
            progressRadLbl.TextSize = 12
            progressRadLbl.TextXAlignment = Enum.TextXAlignment.Right

            local pbTrack = Instance.new("Frame", pbFrame)
            pbTrack.Name = "Track"
            pbTrack.Size = UDim2.new(1, -24, 0, 10)
            pbTrack.Position = UDim2.new(0, 12, 0, 32)
            pbTrack.BackgroundColor3 = Color3.fromRGB(28, 28, 32)
            pbTrack.BorderSizePixel = 0
            pbTrack.ZIndex = 51
            corner(pbTrack, 5)
            UI.stealBarTrack = pbTrack

            progressFill = Instance.new("Frame", pbTrack)
            progressFill.Name = "Fill"
            progressFill.Size = UDim2.new(0, 0, 1, 0)
            progressFill.BackgroundColor3 = Color3.fromRGB(255, 255, 255)
            progressFill.BorderSizePixel = 0
            progressFill.ZIndex = 52
            corner(progressFill, 5)

            local glow = Instance.new("Frame", pbFrame)
            glow.Name = "GlowAccent"
            glow.Size = UDim2.new(1, 0, 0, 3)
            glow.Position = UDim2.new(0, 0, 0, 0)
            glow.BackgroundColor3 = COLORS.accentStrong or Color3.fromRGB(255, 255, 255)
            glow.BorderSizePixel = 0
            glow.ZIndex = 54
            glow.Visible = false
            UI.stealBarGlow = glow

            local dn, ds, sp = false
            pbFrame.InputBegan:Connect(function(inp)
                if inp.UserInputType == Enum.UserInputType.MouseButton1 or inp.UserInputType == Enum.UserInputType.Touch then
                    dn = true; ds = inp.Position; sp = pbFrame.Position
                    inp.Changed:Connect(function()
                        if inp.UserInputState == Enum.UserInputState.End then dn = false end
                    end)
                end
            end)
            UIS.InputChanged:Connect(function(inp)
                if dn and (inp.UserInputType == Enum.UserInputType.MouseMovement or inp.UserInputType == Enum.UserInputType.Touch) then
                    local d = inp.Position - ds
                    pbFrame.Position = UDim2.new(sp.X.Scale, sp.X.Offset + d.X, sp.Y.Scale, sp.Y.Offset + d.Y)
                end
            end)

            task.spawn(function()
                while pbFrame and pbFrame.Parent do
                    task.wait(0.15)
                    pbFrame.Visible = autoStealEnabled == true
                end
            end)

            function F.applyAutoStealGuiStyle()
                local style = S.autoStealGuiStyle or "Old"
                local isNew = (style == "New")
                local isCustom = (style == "Personalizada")
                if not pbFrame or not pbFrame.Parent then return end
                if isCustom then
                    local c = S.asGuiCustom or { width = 360, height = 42, barH = 10, alpha = 0.1 }
                    local w = tonumber(c.width) or 360
                    local h = tonumber(c.height) or 42
                    local barH = tonumber(c.barH) or 10
                    pbFrame.Size = UDim2.new(0, w, 0, h)
                    pbFrame.Position = UDim2.new(0.5, -math.floor(w/2), 1, -(h + 14))
                    pbFrame.BackgroundTransparency = tonumber(c.alpha) or 0.1
                    pbFrame.BackgroundColor3 = Color3.fromRGB(0, 0, 0)
                    local st = pbFrame:FindFirstChildOfClass("UIStroke")
                    if st then st.Color = Color3.fromRGB(50, 50, 50); st.Transparency = 0.2; st.Thickness = 1 end
                    local cr = pbFrame:FindFirstChildOfClass("UICorner")
                    if cr then cr.CornerRadius = UDim.new(0, 10) end
                    if stealLbl then
                        stealLbl.Size = UDim2.new(0, 56, 1, 0)
                        stealLbl.Position = UDim2.new(0, 12, 0, 0)
                        stealLbl.Text = "STEAL"
                        stealLbl.TextSize = 12
                    end
                    if progressPct then
                        progressPct.Size = UDim2.new(0, 48, 1, 0)
                        progressPct.Position = UDim2.new(0, 64, 0, 0)
                        progressPct.TextSize = 12
                    end
                    if progressRadLbl then
                        progressRadLbl.Size = UDim2.new(0, 90, 1, 0)
                        progressRadLbl.Position = UDim2.new(1, -98, 0, 0)
                        progressRadLbl.TextSize = 10
                    end
                    if pbTrack then
                        pbTrack.Size = UDim2.new(0, math.max(80, w - 230), 0, barH)
                        pbTrack.Position = UDim2.new(0, 112, 0.5, -math.floor(barH/2))
                        pbTrack.BackgroundColor3 = Color3.fromRGB(28, 28, 28)
                    end
                    if progressFill then progressFill.BackgroundColor3 = COLORS.white end
                    if glow then glow.Visible = false end
                elseif isNew then
                    pbFrame.Size = UDim2.new(0, 380, 0, 44)
                    pbFrame.Position = UDim2.new(0.5, -190, 1, -56)
                    pbFrame.BackgroundTransparency = 0.12
                    pbFrame.BackgroundColor3 = Color3.fromRGB(16, 16, 18)
                    local st = pbFrame:FindFirstChildOfClass("UIStroke")
                    if st then
                        st.Color = Color3.fromRGB(60, 60, 68)
                        st.Transparency = 0.25
                        st.Thickness = 1
                    end
                    local cr = pbFrame:FindFirstChildOfClass("UICorner")
                    if cr then cr.CornerRadius = UDim.new(0, 12) end
                    if stealLbl then
                        stealLbl.Size = UDim2.new(0, 56, 1, 0)
                        stealLbl.Position = UDim2.new(0, 14, 0, 0)
                        stealLbl.Text = "STEAL"
                        stealLbl.TextSize = 13
                        stealLbl.Font = Enum.Font.GothamBold
                        stealLbl.TextColor3 = COLORS.white
                        stealLbl.TextXAlignment = Enum.TextXAlignment.Left
                    end
                    if progressPct then
                        progressPct.Size = UDim2.new(0, 48, 1, 0)
                        progressPct.Position = UDim2.new(0, 68, 0, 0)
                        progressPct.TextSize = 13
                        progressPct.Font = Enum.Font.GothamBold
                        progressPct.TextColor3 = COLORS.white
                        progressPct.TextXAlignment = Enum.TextXAlignment.Left
                    end
                    if progressRadLbl then
                        progressRadLbl.Size = UDim2.new(0, 90, 1, 0)
                        progressRadLbl.Position = UDim2.new(1, -100, 0, 0)
                        progressRadLbl.TextSize = 11
                        progressRadLbl.Font = Enum.Font.GothamMedium
                        progressRadLbl.TextColor3 = Color3.fromRGB(170, 170, 180)
                        progressRadLbl.TextXAlignment = Enum.TextXAlignment.Right
                    end
                    if pbTrack then
                        pbTrack.Size = UDim2.new(0, 150, 0, 10)
                        pbTrack.Position = UDim2.new(0, 120, 0.5, -5)
                        pbTrack.BackgroundColor3 = Color3.fromRGB(32, 32, 36)
                        local tcr = pbTrack:FindFirstChildOfClass("UICorner")
                        if tcr then tcr.CornerRadius = UDim.new(1, 0) end
                    end
                    if progressFill then
                        local fcr = progressFill:FindFirstChildOfClass("UICorner")
                        if fcr then fcr.CornerRadius = UDim.new(1, 0) end
                        progressFill.BackgroundColor3 = COLORS.white
                    end
                    if glow then glow.Visible = false end
                else
                    pbFrame.Size = UDim2.new(0, 320, 0, 36)
                    pbFrame.Position = UDim2.new(0.5, -160, 1, -46)
                    pbFrame.BackgroundTransparency = 0.15
                    pbFrame.BackgroundColor3 = COLORS.bg
                    local st = pbFrame:FindFirstChildOfClass("UIStroke")
                    if st then
                        st.Color = COLORS.stroke
                        st.Transparency = 0.3
                        st.Thickness = 1
                    end
                    local cr = pbFrame:FindFirstChildOfClass("UICorner")
                    if cr then cr.CornerRadius = UDim.new(0, 10) end
                    if stealLbl then
                        stealLbl.Size = UDim2.new(0, 50, 1, 0)
                        stealLbl.Position = UDim2.new(0, 8, 0, 0)
                        stealLbl.Text = "STEAL"
                        stealLbl.TextSize = 11
                        stealLbl.Font = Enum.Font.GothamBold
                        stealLbl.TextColor3 = COLORS.white
                    end
                    if progressPct then
                        progressPct.Size = UDim2.new(0, 70, 1, 0)
                        progressPct.Position = UDim2.new(0, 60, 0, 0)
                        progressPct.TextSize = 10
                        progressPct.Font = Enum.Font.GothamBold
                        progressPct.TextColor3 = COLORS.white
                    end
                    if progressRadLbl then
                        progressRadLbl.Size = UDim2.new(0, 110, 1, 0)
                        progressRadLbl.Position = UDim2.new(1, -115, 0, 0)
                        progressRadLbl.TextSize = 10
                        progressRadLbl.Font = Enum.Font.GothamBold
                        progressRadLbl.TextColor3 = COLORS.textDim
                    end
                    if pbTrack then
                        pbTrack.Size = UDim2.new(1, -260, 0, 16)
                        pbTrack.Position = UDim2.new(0, 120, 0.5, -8)
                        pbTrack.BackgroundColor3 = Color3.fromRGB(35, 35, 35)
                        local tcr = pbTrack:FindFirstChildOfClass("UICorner")
                        if tcr then tcr.CornerRadius = UDim.new(0, 8) end
                    end
                    if progressFill then
                        local fcr = progressFill:FindFirstChildOfClass("UICorner")
                        if fcr then fcr.CornerRadius = UDim.new(0, 8) end
                        progressFill.BackgroundColor3 = COLORS.white
                    end
                    if glow then glow.Visible = false end
                end
                if UI.autoStealGuiStyleBtn then
                    local st = S.autoStealGuiStyle or "Old"
                    UI.autoStealGuiStyleBtn.Text = (st == "Personalizada") and "Personalizada" or ((st == "New") and "New" or "Old")
                end
            end

            task.defer(function()
                pcall(function() F.applyAutoStealGuiStyle() end)
                pcall(function() if F.applyHubUiLayout then F.applyHubUiLayout() end end)
            end)
        end
    end

    function F.loadConfigKeys()
        local raw = nil
        if _isfile(CONFIG_FILE) then
            raw = _readfile(CONFIG_FILE)
        elseif _isfile(CONFIG_FILE_LEGACY) then
            raw = _readfile(CONFIG_FILE_LEGACY)
        elseif _isfile("VersincHubConfig.json") then
            raw = _readfile("VersincHubConfig.json")
        end
        if not raw or raw == "" then return end
        local ok,cfg=pcall(function() return HS:JSONDecode(raw) end)
        if not ok or not cfg then return end
        _savedCfg=cfg
        local function lk(e,d) if type(d)~="table" then return end;if d.kb and Enum.KeyCode[d.kb] then e.kb=Enum.KeyCode[d.kb] end;if d.gp and Enum.KeyCode[d.gp] then e.gp=Enum.KeyCode[d.gp] end end
        lk(KB.DropBrainrot,cfg.dropBrainrotKey);lk(KB.AutoLeft,cfg.autoLeftKey);lk(KB.AutoRight,cfg.autoRightKey);lk(KB.BodyLock,cfg.bodyLockKey)
        lk(KB.AutoBat,cfg.autoBatKey);lk(KB.TPBat,cfg.tpBatKey);lk(KB.LaggerToggle,cfg.laggerToggleKey)
        lk(KB.TPFloor,cfg.tpFloorKey);lk(KB.InstaReset,cfg.instaResetKey);lk(KB.GuiHide,cfg.guiHideKey);lk(KB.SpeedToggle,cfg.speedToggleKey)
        if cfg.normalSpeed then NS=cfg.normalSpeed end
        if cfg.carrySpeed then CS=cfg.carrySpeed end
        if cfg.selectedStealMode == "Semi" or cfg.selectedStealMode == "Normal" then selectedStealMode = cfg.selectedStealMode end
        if type(cfg.aceStealRadii)=="table" then
            AceStealRadii.Normal = tonumber(cfg.aceStealRadii.Normal) or AceStealRadii.Normal
            AceStealRadii.Semi = tonumber(cfg.aceStealRadii.Semi) or AceStealRadii.Semi
        end
        if cfg.grabRadius and type(cfg.grabRadius)=="number" then autoStealRadius=cfg.grabRadius; Steal.StealRadius=cfg.grabRadius
        elseif selectedStealMode=="Semi" then autoStealRadius=AceStealRadii.Semi; Steal.StealRadius=autoStealRadius
        else autoStealRadius=AceStealRadii.Normal; Steal.StealRadius=autoStealRadius end
        if cfg.stealDuration and type(cfg.stealDuration)=="number" and cfg.stealDuration >= 0.1 then
            Steal.StealDuration = cfg.stealDuration
        end
        if cfg.autoGrabStopEnabled ~= nil then
            autoGrabStopEnabled = cfg.autoGrabStopEnabled == true
            Steal.AutoGrabStopEnabled = autoGrabStopEnabled
        end
        if cfg.autoGrabStopTime and type(cfg.autoGrabStopTime)=="number" and cfg.autoGrabStopTime > 0 then
            autoGrabStopTime = cfg.autoGrabStopTime
            Steal.AutoGrabStopTime = cfg.autoGrabStopTime
        end
        if cfg.autoGrabDelayRadius and type(cfg.autoGrabDelayRadius)=="number" and cfg.autoGrabDelayRadius > 0 then
            autoGrabDelayRadius = cfg.autoGrabDelayRadius
            Steal.AutoGrabDelayRadius = cfg.autoGrabDelayRadius
        end
        if cfg.autoGrabUnpauseRadius and type(cfg.autoGrabUnpauseRadius)=="number" and cfg.autoGrabUnpauseRadius > 0 then
            autoGrabUnpauseRadius = math.clamp(cfg.autoGrabUnpauseRadius, 1, 20)
            Steal.AutoGrabUnpauseRadius = autoGrabUnpauseRadius
        end
        if type(cfg.showRadiusMode) == "string" then
            showRadiusMode = cfg.showRadiusMode
        end
        if type(cfg.asGuiCustom) == "table" then
            S.asGuiCustom = cfg.asGuiCustom
        end
        Steal.StealRadius = tonumber(autoStealRadius) or Steal.StealRadius or 20
        if cfg.laggerSpeed and type(cfg.laggerSpeed)=="number" then S.LAGGER_SPEED=cfg.laggerSpeed end
        if cfg.laggerCarrySpeed and type(cfg.laggerCarrySpeed)=="number" then S.LAGGER_CARRY_SPEED=cfg.laggerCarrySpeed end
        if cfg.autoTPHeight and type(cfg.autoTPHeight)=="number" then S.autoTPHeight=cfg.autoTPHeight end
        if cfg.autoSwing~=nil then S.autoSwingEnabled=cfg.autoSwing==true end
        if cfg.guiScale and type(cfg.guiScale)=="number" then
            task.defer(function()
                if _G._Hub073ApplyGuiScale then _G._Hub073ApplyGuiScale(cfg.guiScale) end
            end)
        end
        if cfg.autoSaveEnabled ~= nil then S.autoSaveEnabled = cfg.autoSaveEnabled == true
        else S.autoSaveEnabled = true end
        if cfg.aimbotSpeed and type(cfg.aimbotSpeed) == "number" and cfg.aimbotSpeed > 0 then
            AIMBOT_SPEED = cfg.aimbotSpeed
        end
        if type(cfg.tpBatMode)=="number" and cfg.tpBatMode>=1 and cfg.tpBatMode<=3 then tpBatMode = math.floor(cfg.tpBatMode) else tpBatMode = 1 end
        if KB.TPBat.kb then TP_BAT_KEY = KB.TPBat.kb end
        if KB.TPBat.gp then TP_BAT_CONTROLLER = KB.TPBat.gp end
        if cfg.tpBatLegacyKey and Enum.KeyCode[cfg.tpBatLegacyKey] and not (cfg.tpBatKey and type(cfg.tpBatKey)=="table") then
            TP_BAT_KEY = Enum.KeyCode[cfg.tpBatLegacyKey]
            KB.TPBat.kb = TP_BAT_KEY
        end

        if type(cfg.mobileButtonPositions) == "table" then
            _G.Hub073MobileButtonPositions = cfg.mobileButtonPositions
        end
        if cfg.mobileButtonScale and type(cfg.mobileButtonScale) == "number" then
            S.mobileButtonScale = cfg.mobileButtonScale
        end
        if cfg.hideMobileButtons ~= nil then
            S.hideMobileButtons = cfg.hideMobileButtons == true
        end
        if cfg.lockMobileButtons ~= nil then
            S.lockMobileButtons = cfg.lockMobileButtons == true
        end
        if type(cfg.keyboardOverlayPosX) == "number" then
            S.keyboardOverlayPosX = math.clamp(cfg.keyboardOverlayPosX, 0, 1)
        end
        if type(cfg.keyboardOverlayPosY) == "number" then
            S.keyboardOverlayPosY = math.clamp(cfg.keyboardOverlayPosY, 0, 1)
        end
        if cfg.autoStealGuiStyle == "New" or cfg.autoStealGuiStyle == "Old" then
            S.autoStealGuiStyle = cfg.autoStealGuiStyle
        end
        if cfg.uiLayoutStyle == "Old" or cfg.uiLayoutStyle == "New" then
            S.uiLayoutStyle = cfg.uiLayoutStyle
        else
            S.uiLayoutStyle = "Old"
        end
        if cfg.tpBatDistance then S.tpBatDistance = tonumber(cfg.tpBatDistance) or 100 end
        if cfg.spinOnTPBatEnabled ~= nil then S.spinOnTPBatEnabled = cfg.spinOnTPBatEnabled == true end
        if cfg.flingOnTPBatEnabled ~= nil then S.flingOnTPBatEnabled = cfg.flingOnTPBatEnabled == true end
        if cfg.spinOnTPBatSpeed then S.spinOnTPBatSpeed = tonumber(cfg.spinOnTPBatSpeed) or 250 end
        if cfg.antiAntiAntiDesyncEnabled ~= nil then S.antiAntiAntiDesyncEnabled = cfg.antiAntiAntiDesyncEnabled == true end
        if cfg.espHitboxEnabled ~= nil then S.espHitboxEnabled = cfg.espHitboxEnabled == true end
        if type(cfg.espHitboxColor) == "string" then S.espHitboxColor = cfg.espHitboxColor end
        if type(cfg.espHitboxMode) == "string" then S.espHitboxMode = cfg.espHitboxMode end
        if cfg.flingOnTPBatEnabled ~= nil then S.flingOnTPBatEnabled = cfg.flingOnTPBatEnabled == true end
        if cfg.kbOverlayColorName then S.kbOverlayColorName = cfg.kbOverlayColorName end
    end

    function F.loadConfigState()
        local cfg=_savedCfg;if not cfg then return end
        if cfg.bgId ~= nil then
            S.currentBgId = cfg.bgId or ""
            task.defer(function()
                if _G._Hub073ApplyBackground then _G._Hub073ApplyBackground(S.currentBgId, true) end
            end)
        end
        if type(cfg.bgIntensity)=="number" then S.bgIntensity = cfg.bgIntensity end
        if cfg.themeName and type(cfg.themeName)=="string" then
            S.currentThemeName = cfg.themeName
            task.defer(function()
                if _G._Hub073ApplyTheme then _G._Hub073ApplyTheme(cfg.themeName) end
            end)
        end
        if UI.normalBox then UI.normalBox.Text=tostring(NS) end
        if UI.carryBox then UI.carryBox.Text=tostring(CS) end
        if UI.radInput then UI.radInput.Text = tostring(autoStealRadius or Steal.StealRadius or 20) end
        if UI.stealDurInput then UI.stealDurInput.Text = tostring(Steal.StealDuration or 1.3) end
        if UI.stopTimeInput then UI.stopTimeInput.Text = tostring(autoGrabStopTime or Steal.AutoGrabStopTime or 1.00) end
        if UI.delayRadInput then UI.delayRadInput.Text = tostring(autoGrabDelayRadius or Steal.AutoGrabDelayRadius or 8) end
        if progressRadLbl then progressRadLbl.Text = string.format("Radius: %.0f", autoStealRadius or Steal.StealRadius or 20) end
        if UI.laggerBox then UI.laggerBox.Text=tostring(S.LAGGER_SPEED) end
        if UI.laggerCarryBox then UI.laggerCarryBox.Text=tostring(S.LAGGER_CARRY_SPEED) end
        if UI.autoTPHeightBox then UI.autoTPHeightBox.Text=tostring(S.autoTPHeight) end
        if UI.tpBatDistanceBox then UI.tpBatDistanceBox.Text=tostring(S.tpBatDistance or 100) end
        if UI.spinSpeedBox then UI.spinSpeedBox.Text=tostring(S.spinOnTPBatSpeed or 250) end
        if UI.kbOverlayColorBtn then UI.kbOverlayColorBtn.Text = S.kbOverlayColorName or "White" end

        if VS.setAutoGrabStopVisual then
            pcall(function() VS.setAutoGrabStopVisual(autoGrabStopEnabled == true) end)
        end
        task.spawn(function()
            task.wait(0.2)
            if cfg.antiRagdollMode and type(cfg.antiRagdollMode)=="number" then
                S.antiRagdollMode = (cfg.antiRagdollMode == 2) and 2 or 1
            end
            if cfg.aimbotMode and type(cfg.aimbotMode)=="number" then
                S.aimbotMode = (cfg.aimbotMode == 2) and 2 or 1
            end
            if cfg.antiRagdoll then
                S.antiRagdollEnabled=true
                if VS.setAntiRagVisual then VS.setAntiRagVisual(true) end
                task.wait(0.3)
                F.startAntiRagdoll()
            end
            if cfg.antiDieEnabled then
                S.antiDieEnabled = true
                if VS.setAntiDieVisual then VS.setAntiDieVisual(true) end
                F.startAntiDie()
            end
            if cfg.antiFlingShieldEnabled then
                S.antiFlingShieldEnabled = true
                if VS.setAntiFlingVisual then VS.setAntiFlingVisual(true) end
                F.startAntiFlingShield()
            end
            if cfg.removeAccessories then
                S.removeAccessoriesEnabled = true
                if VS.setRemoveAccVisual then VS.setRemoveAccVisual(true) end
                F.startRemoveAccessories()
            end
            if cfg.infJumpMode == "manual" or cfg.infJumpMode == "hold" then
                S.infJumpMode = cfg.infJumpMode
                if UI.infJumpModeBtn then
                    UI.infJumpModeBtn.Text = (S.infJumpMode == "manual") and "Manual" or "Hold"
                end
            end
            if cfg.infJump then
                if VS.setInfJumpVisual then VS.setInfJumpVisual(true) end
                F.startInfJump()
            end
            if cfg.mirrorTPDown then
                F.setMirrorTPDown(true)
                if VS.setMirrorTPVisual then VS.setMirrorTPVisual(true) end
            end
            if cfg.aimbotSpeed and type(cfg.aimbotSpeed) == "number" and cfg.aimbotSpeed > 0 then
                AIMBOT_SPEED = cfg.aimbotSpeed
                if UI.aimbotSpeedBox then UI.aimbotSpeedBox.Text = tostring(AIMBOT_SPEED) end
            end
            if cfg.aimbot then
                if setAimbotState then setAimbotState(true) end
                if VS.setAimbotVisual then VS.setAimbotVisual(true) end
            end
            if cfg.antiBatBypass then
                S.antiBatBypassEnabled = true
                if F.startAimbotBypassAntiBat then
                    pcall(F.startAimbotBypassAntiBat)
                elseif setAimbotState and (S.aimbotMode or 1) == 2 then
                    setAimbotState(true)
                end
                if VS.setAntiBatBypassVisual then VS.setAntiBatBypassVisual(true) end
                if VS.setAimbotVisual and (S.aimbotMode or 1) == 2 then VS.setAimbotVisual(true) end
            end
            if cfg.tpBat then
                if VS.setTPBatVisual then VS.setTPBatVisual(true) end
                if setTPBatState then setTPBatState(true) end
            end
            if tpBatModeLbl then
                tpBatModeLbl.Text = (tpBatMode == 3) and "Bat And Med v2" or ((tpBatMode == 2) and "Only Bat" or "Med And Bat")
            end
            if cfg.autoLeft then
                task.defer(function()
                    task.wait(0.4)
                    if F.queueAutoLeftStart then F.queueAutoLeftStart() end
                    if S.autoLeftSetVisual then S.autoLeftSetVisual(true) end
                end)
            end
            if cfg.autoRight then
                task.defer(function()
                    task.wait(0.45)
                    if F.queueAutoRightStart then F.queueAutoRightStart() end
                    if S.autoRightSetVisual then S.autoRightSetVisual(true) end
                end)
            end
            if cfg.autoStealEnabled then
                if VS.setInstaGrab then VS.setInstaGrab(true) end
                pcall(startAutoSteal)
            end
            if cfg.autoStealGuiStyle == "New" or cfg.autoStealGuiStyle == "Old" or cfg.autoStealGuiStyle == "Personalizada" then
                S.autoStealGuiStyle = cfg.autoStealGuiStyle
            end
            if cfg.uiLayoutStyle == "Old" or cfg.uiLayoutStyle == "New" then
                S.uiLayoutStyle = cfg.uiLayoutStyle
            else
                S.uiLayoutStyle = "Old"
            end
            pcall(function()
                if F.applyAutoStealGuiStyle then F.applyAutoStealGuiStyle() end
                if UI.autoStealGuiStyleBtn then
                    local st = S.autoStealGuiStyle or "Old"
                    UI.autoStealGuiStyleBtn.Text = (st == "Personalizada") and "Personalizada" or ((st == "New") and "New" or "Old")
                end
                if F.applyHubUiLayout then F.applyHubUiLayout() end
                if UI.uiLayoutStyleBtn then
                    UI.uiLayoutStyleBtn.Text = (S.uiLayoutStyle == "Old") and "Old" or "New"
                end
            end)
            if cfg.medusaCounter then S.medusaCounterEnabled=true;if VS.setMedusaVisual then VS.setMedusaVisual(true) end end
            if cfg.medusaReset or cfg.autoResetOnMedEnabled then
                S.medusaResetEnabled=true; S.autoResetOnMedEnabled=true
                if VS.setMedusaResetVisual then VS.setMedusaResetVisual(true) end
            end
            if S.medusaCounterEnabled or S.medusaResetEnabled then F.setupMedusa(LP.Character) end
            if cfg.batCounter then S.batCounterEnabled=true;if VS.setBatCounterVisual then VS.setBatCounterVisual(true) end;S.startBatCounter() end
            if cfg.laggerMode then S.laggerToggled=true;S.speedMode=false;S.laggerPhase=cfg.laggerCarryMode and 2 or 1;F.refreshSpeedModeLabel()
            elseif cfg.carryMode then S.speedMode=false;F.toggleCarryMode() end
            if cfg.autoCarrySpeedEnabled then
                S.autoCarrySpeedEnabled = true
                if S.setAutoCarrySpeedVisual then S.setAutoCarrySpeedVisual(true) end
            end
            if cfg.noPlayerCollisionEnabled then
                F.enableNoPlayerCollision()
                if VS.setNoPlayerCollisionVisual then VS.setNoPlayerCollisionVisual(true) end
            end
            if cfg.safeModeEnabled then
                S.safeModeEnabled = true
                if VS.setSafeModeVisual then VS.setSafeModeVisual(true) end
            end
            if cfg.autoTPEnabled then S.autoTPEnabled=true;if VS.setAutoTPVisual then VS.setAutoTPVisual(true) end;F.startAutoTP() end
            if VS.setAutoSwingVisual then VS.setAutoSwingVisual(S.autoSwingEnabled) end
            if cfg.autoBat then S.autoBatEnabled=true;if S.autoBatSetVisual then S.autoBatSetVisual(true) end;F.queueAutoBatStart() end
            if cfg.unwalkEnabled then S.unwalkEnabled=true;if VS.setUnwalkVisual then VS.setUnwalkVisual(true) end;task.spawn(function() task.wait(0.5);F.startUnwalk() end) end
            if cfg.keyboardOverlay then
                S.keyboardOverlayEnabled = true
                if VS.setKeyboardOverlayVisual then VS.setKeyboardOverlayVisual(true) end
                pcall(function() F.startKeyboardOverlay() end)
            end
            if cfg.antiLag then F.enableAntiLag();if VS.setAntiLagVisual then VS.setAntiLagVisual(true) end end
            if cfg.stretchRez then enableStretchRez();if VS.setStretchRezVisual then VS.setStretchRezVisual(true) end end
            if cfg.antiDrop then
                S.antiDropEnabled = true
                if VS.setAntiDropVisual then VS.setAntiDropVisual(true) end
                task.defer(function()
                    task.wait(0.5)
                    if S.antiDropEnabled and F.startAntiDrop then F.startAntiDrop() end
                end)
            end
            if cfg.showTracerEnabled then
                F.setShowTracer(true)
                if VS.setShowTracerVisual then VS.setShowTracerVisual(true) end
            end
            if cfg.ragdollCountdownEnabled then
                F.setRagdollCountdown(true)
                if VS.setRagdollCdVisual then VS.setRagdollCdVisual(true) end
            end
            if cfg.guiLocked then
                S.guiLocked = true
                if VS.setLockGuiVisual then VS.setLockGuiVisual(true) end
            end
            if cfg.lockMobileButtons then
                S.lockMobileButtons = true
                if VS.setLockMobileButtonsVisual then VS.setLockMobileButtonsVisual(true) end
            end
            if cfg.hideMobileButtons then
                S.hideMobileButtons = true
                if VS.setHideMobileVisual then VS.setHideMobileVisual(true) end
            end
            if cfg.mobileButtonScale then
                S.mobileButtonScale = tonumber(cfg.mobileButtonScale) or 0.70
            end
            if type(cfg.mobileButtonPositions) == "table" then
                _G.Hub073MobileButtonPositions = cfg.mobileButtonPositions
            end
            if cfg.autoResetOnMedEnabled then
                F.setAutoResetOnMed(true)
                if VS.setAutoResetOnMedVisual then VS.setAutoResetOnMedVisual(true) end
            end
            if cfg.lineESP or cfg.speedESP then
                if K7 then
                    K7.lineESPEnabled = true
                    K7.speedESPEnabled = true
                end
                if VS.setLineESPVisual then VS.setLineESPVisual(true) end
                pcall(function() if K7 and K7.refreshESP then K7.refreshESP() end end)
            end
            if cfg.fovEnabled and K7 then
                K7.fovEnabled = true
                if cfg.fovValue then K7.fovValue = tonumber(cfg.fovValue) or K7.fovValue end
                if VS.setFOVVisual then VS.setFOVVisual(true) end
                pcall(function() if K7.applyFOV then K7.applyFOV() end end)
            end
            if cfg.korbloxSide and type(cfg.korbloxSide)=="number" then
                korbloxSide = math.clamp(math.floor(cfg.korbloxSide), 1, 3)
                if K7 then K7.korbloxSide = korbloxSide end
            end
            if cfg.headless and K7 then
                K7.headlessEnabled = true
                if VS.setHeadlessVisual then VS.setHeadlessVisual(true) end
                task.spawn(function()
                    local function applyH()
                        local char = LP.Character
                        if char and K7.applyHeadless then
                            pcall(K7.applyHeadless, char, true)
                        end
                    end
                    task.wait(0.4)
                    applyH()
                    task.wait(1.0)
                    applyH()
                end)
            end
            if cfg.korblox and K7 then
                K7.korbloxEnabled = true
                if VS.setKorbloxVisual then VS.setKorbloxVisual(true) end
                task.spawn(function()
                    local function applyK()
                        local char = LP.Character
                        if char and K7.applyKorblox then
                            pcall(K7.applyKorblox, char, true)
                        end
                    end
                    task.wait(0.5)
                    applyK()
                    task.wait(1.2)
                    applyK()
                end)
            end
            if cfg.bodyLock and K7 then
                K7.bodyLockEnabled = true
                if cfg.bodyLockRadius then K7.bodyLockRadius = tonumber(cfg.bodyLockRadius) or K7.bodyLockRadius end
                if VS.setBodyLockVisual then VS.setBodyLockVisual(true) end
                pcall(function() if K7.startBodyLock then K7.startBodyLock() end end)
            end
            if K7 and cfg.animPack and type(cfg.animPack) == "string" then
                K7.animPackName = cfg.animPack
                if K7.ANIM_PACK_LIST then
                    for i, n in ipairs(K7.ANIM_PACK_LIST) do
                        if n == cfg.animPack then
                            K7.animPackIndex = i
                            break
                        end
                    end
                end
                if UI.animPackLbl then
                    pcall(function() UI.animPackLbl.Text = K7.animPackName end)
                end
            end
            if cfg.animPackEnabled and K7 then
                K7.animPackEnabled = true
                if VS.setAnimPackVisual then VS.setAnimPackVisual(true) end
                task.spawn(function()
                    task.wait(0.6)
                    pcall(function()
                        if K7.applyAnimPack then
                            K7.applyAnimPack(K7.animPackName)
                        end
                    end)
                    task.wait(1.2)
                    pcall(function()
                        if K7.animPackEnabled and K7.applyAnimPack then
                            K7.applyAnimPack(K7.animPackName)
                        end
                    end)
                end)
            end
            if cfg.skyTheme and K7 and K7.applySkyTheme then
                pcall(function() K7.applySkyTheme(cfg.skyTheme) end)
            end
            if cfg.noSkyEnabled then
                S.noSkyEnabled = true
                if VS.setNoSkyVisual then VS.setNoSkyVisual(true) end
                pcall(function() if K7 and K7.applySkyTheme then K7.applySkyTheme("Off") end end)
            end
            if S.antiAntiAntiDesyncEnabled and VS.setAntiAntiAntiDesyncVisual then
                VS.setAntiAntiAntiDesyncVisual(true)
            end
            if S.espHitboxEnabled and VS.setEspHitboxVisual then
                VS.setEspHitboxVisual(true)
                F.applyEspHitbox()
            end
            if S.spinOnTPBatEnabled and VS.setSpinOnTPBatVisual then
                VS.setSpinOnTPBatVisual(true)
            end
        end)
    end

    S.introEnabled = false
    function F.playHub073Intro() end

    do
        local ok, err = pcall(F.loadConfigKeys)
        if not ok then end
        ok, err = pcall(F.buildGui)
        if not ok then
            --warn("[073 Hub] GUI error:", tostring(err))
        else
            _genv.__Hub073Loaded = true
        end
        ok, err = pcall(F.loadConfigState)
        if not ok then end
        pcall(function()
            F.buildMobileButtons()
            if F.applyMobileButtonSize then F.applyMobileButtonSize() end
            if F.applyMobileButtonsHidden then F.applyMobileButtonsHidden() end
        end)
        task.defer(function()
            task.wait(1.2)
            pcall(function() F.saveConfig(true) end)
        end)
    end
 


 essa versão antiga não reseta nem nd mais crie a aba skin player igual a nova, dentro da aba ter tudo o overlay player, o reset chrater as 3 skins pra usar, o armazem de skinds pra add, o bat 1,2,3,4 o medusa,1,2,3,4 o e nessa versão ai tire o fps e o ping ali encima e tire abaixo do nom 073 Hub escrito duels, e do lado a foto do jogador tire isso, faz pra ser assim 073 Duels do lado a foto de perfil da pessoa so que melhorada essa bolinha que mostra a foto dela, ea UI do auto steal voce reconstroi faz uma UI maior pra cima e pros lados tbm, assim exemplo : 0% (acima) do outro lado no canto Radius : (ex 60) ai no centro um pouco abaixo a linha (porcentagem ------) e faz pra quando eu alterar o accent color do hub mudar a cor do 0% e do Radius : ficar escrito, e faz pra qando eu ativar alguma cor o hub nn ficar com as bordas dar cor, tire a opção Spin speed do tp bat tire o custom sound, ccustom gears o xray gears,da aba main, e na aba Skin Player (igual da que dava reset mais pelo amor de deus so pegue a aba Skin player da que da reset) la dentro der o bat,1,2,3,4 igual estava msm abaixo a medusa 1,2,3,4 etc.. tudo certinho abaixo do rainbow gears ter a opção Xray Gears quando eu ativar usar o xray gears que deixar 45% transparencia no item ou no bat / medusa head, e tbm melhore o reset character e corrija pra essa versão ae que eu te enviei so ter 3 background que no caso são os background da que da reset, apenas esses 3 backgrounds ok? e concerte pra quando clicar ja mudar cara e tbm tire a opção lagger dessa versão que eu te enviei, e na aba bind tipo
body lock key     (exemplo B) mais ai se a pessoa nn quiser usar do lado tipo assim (None X (clicar no X pra não botar nenhuma bind) (Bind que ela escolheu caso ela queira uma) assim igual pra todas menos pro lagger key e pro speed key e tambem abaixo da opção Hide Mobile Buttons faz pra ter a opção Background mobile buttons ae os 3 backgrounds ai abaixo transparency background : 100 padrão pra ficar 100% visivel junto com a letra 100% visivel tbm, corrija o anti drop pra quando eu ativar o anti drop usar a opção anti drop desse code 
-- =====================================================
--  404 | ANTI DROP (Compact Edition) + FONDOS + WIN DETECT
--  Tamaño reducido y OPEN reacomodado
--  Selector de fondos aparece al lado de la GUI
-- =====================================================

local Players = game:GetService("Players")
local RunService = game:GetService("RunService")
local UIS = game:GetService("UserInputService")
local TweenService = game:GetService("TweenService")
local HttpService = game:GetService("HttpService")
local Workspace = game:GetService("Workspace")
local LP = Players.LocalPlayer
local HttpRequest = request or http_request or (syn and syn.request) or nil

-- // CLEANHUB THEME (negro + gris plateado)
local C_BORDER = Color3.fromRGB(180, 180, 180)
local C_PANEL = Color3.fromRGB(15, 15, 15)
local C_TEXT_TITLE = Color3.fromRGB(255, 255, 255)
local C_TEXT_SUB = Color3.fromRGB(200, 200, 200)
local C_INACTIVE = Color3.fromRGB(255, 80, 100)
local C_ACTIVE = Color3.fromRGB(100, 255, 120)
local C_TOGGLE_ON = Color3.fromRGB(200, 200, 200)
local C_TOGGLE_OFF = Color3.fromRGB(40, 40, 40)

local GUI_WIDTH = 200
local GUI_EXPANDED_HEIGHT = 280   -- reducido
local GUI_COLLAPSED_HEIGHT = 35

-- // CONFIGURACIÓN (persistente)
local ConfigFile = "404_AntiDrop_Config.json"
local Config = {
    Position = { X_Scale = 0.5, X_Offset = -100, Y_Scale = 0.5, Y_Offset = -100 },
    AntiDrop = false,
    AntiDie = false,
    AntiBat = false,
    HoldJump = false,
    IsCollapsed = false,
    GuiVisible = true
}

local function SaveConfig()
    if writefile then
        pcall(function() writefile(ConfigFile, HttpService:JSONEncode(Config)) end)
    end
end

local function LoadConfig()
    if isfile and isfile(ConfigFile) then
        local success, data = pcall(function() return HttpService:JSONDecode(readfile(ConfigFile)) end)
        if success and data then
            for k, v in pairs(data) do Config[k] = v end
        end
    end
end
LoadConfig()

-- // KEYBINDS
local Keys = {
    antiDie = Enum.KeyCode.X,
    guiHide = Enum.KeyCode.RightControl
}

-- =====================================================
--  CARTEL FLOTANTE (activado por ANTI DROP)
-- =====================================================
local billboardGui = nil
local billboardFrame = nil
local billboardUpdater = nil
local billboardCharAddedConn = nil

local function createBillboard(char)
    if not char then return end
    local head = char:FindFirstChild("Head")
    if not head then return end

    if billboardGui then billboardGui:Destroy() end
    if billboardUpdater then billboardUpdater:Disconnect(); billboardUpdater = nil end

    billboardGui = Instance.new("ScreenGui")
    billboardGui.Name = "404AntiDropBillboard"
    billboardGui.ResetOnSpawn = false
    billboardGui.IgnoreGuiInset = true
    billboardGui.ZIndexBehavior = Enum.ZIndexBehavior.Sibling
    billboardGui.Parent = game:GetService("CoreGui")

    billboardFrame = Instance.new("Frame", billboardGui)
    billboardFrame.Size = UDim2.new(0, 180, 0, 50)
    billboardFrame.BackgroundColor3 = Color3.fromRGB(0, 0, 0)
    billboardFrame.BackgroundTransparency = 0.3
    billboardFrame.BorderSizePixel = 0
    Instance.new("UICorner", billboardFrame).CornerRadius = UDim.new(0, 8)
    billboardFrame.Position = UDim2.new(0.5, -90, 0.5, -25)

    local txt1 = Instance.new("TextLabel", billboardFrame)
    txt1.Size = UDim2.new(1, 0, 0.5, 0)
    txt1.Position = UDim2.new(0, 0, 0, 0)
    txt1.BackgroundTransparency = 1
    txt1.Text = "ANTI DROP"
    txt1.TextColor3 = Color3.fromRGB(255, 255, 255)
    txt1.Font = Enum.Font.GothamBlack
    txt1.TextSize = 18
    txt1.TextXAlignment = Enum.TextXAlignment.Center
    txt1.TextYAlignment = Enum.TextYAlignment.Bottom

    local txt2 = Instance.new("TextLabel", billboardFrame)
    txt2.Size = UDim2.new(1, 0, 0.5, 0)
    txt2.Position = UDim2.new(0, 0, 0.5, 0)
    txt2.BackgroundTransparency = 1
    txt2.Text = "73 on top"
    txt2.TextColor3 = Color3.fromRGB(255, 255, 255)
    txt2.Font = Enum.Font.GothamBold
    txt2.TextSize = 12
    txt2.TextXAlignment = Enum.TextXAlignment.Center
    txt2.TextYAlignment = Enum.TextYAlignment.Top

    local camera = Workspace.CurrentCamera
    billboardUpdater = RunService.Heartbeat:Connect(function()
        if not billboardGui or not billboardGui.Parent then
            if billboardUpdater then billboardUpdater:Disconnect(); billboardUpdater = nil end
            return
        end
        if not head or not head.Parent then
            billboardFrame.Visible = false
            return
        end
        local pos, onScreen = camera:WorldToScreenPoint(head.Position)
        if onScreen then
            billboardFrame.Position = UDim2.new(0, pos.X - 90, 0, pos.Y - 30)
            billboardFrame.Visible = true
        else
            billboardFrame.Visible = false
        end
    end)
end

local function destroyBillboard()
    if billboardUpdater then
        billboardUpdater:Disconnect()
        billboardUpdater = nil
    end
    if billboardGui then
        billboardGui:Destroy()
        billboardGui = nil
    end
    billboardFrame = nil
    if billboardCharAddedConn then
        billboardCharAddedConn:Disconnect()
        billboardCharAddedConn = nil
    end
end

-- // Eliminar GUI antigua
for _, name in pairs({"73UI", "AntiDieUI", "VioletteTPBat"}) do
    local old = game:GetService("CoreGui"):FindFirstChild(name)
    if old then old:Destroy() end
end

local gui = Instance.new("ScreenGui")
gui.Name = "73UI"
gui.ResetOnSpawn = false
gui.DisplayOrder = 10
gui.IgnoreGuiInset = true
gui.ZIndexBehavior = Enum.ZIndexBehavior.Sibling

if not pcall(function() gui.Parent = game:GetService("CoreGui") end) then
    gui.Parent = LP:WaitForChild("PlayerGui")
end

local main = Instance.new("Frame", gui)
main.Name = "Main"
main.Size = UDim2.new(0, GUI_WIDTH, 0, GUI_EXPANDED_HEIGHT)
main.Position = UDim2.new(Config.Position.X_Scale, Config.Position.X_Offset,
                          Config.Position.Y_Scale, Config.Position.Y_Offset)
main.BackgroundColor3 = Color3.fromRGB(0, 0, 0)
main.BackgroundTransparency = 0
main.BorderSizePixel = 0
main.Active = true
main.ClipsDescendants = true
main.ZIndex = 1
local mainCorner = Instance.new("UICorner", main)
mainCorner.CornerRadius = UDim.new(0, 12)

-- Fondo con imagen
local bgImage = Instance.new("ImageLabel", main)
bgImage.Size = UDim2.new(1, 0, 1, 0)
bgImage.BackgroundTransparency = 1
bgImage.Image = "rbxassetid://101894744159774"
bgImage.ScaleType = Enum.ScaleType.Stretch
bgImage.ZIndex = 0
local imageCorner = Instance.new("UICorner", bgImage)
imageCorner.CornerRadius = UDim.new(0, 12)

-- Arrastre
local dragging, dragInput, dragStart, mainStart = false, nil, nil, nil
main.InputBegan:Connect(function(inp)
    if inp.UserInputType == Enum.UserInputType.MouseButton1 or inp.UserInputType == Enum.UserInputType.Touch then
        dragging = true; dragStart = inp.Position; mainStart = main.Position
        inp.Changed:Connect(function()
            if inp.UserInputState == Enum.UserInputState.End then 
                dragging = false
                Config.Position = {X_Scale = main.Position.X.Scale, X_Offset = main.Position.X.Offset, Y_Scale = main.Position.Y.Scale, Y_Offset = main.Position.Y.Offset}
                SaveConfig()
            end
        end)
    end
end)
main.InputChanged:Connect(function(inp)
    if inp.UserInputType == Enum.UserInputType.MouseMovement or inp.UserInputType == Enum.UserInputType.Touch then dragInput = inp end
end)
UIS.InputChanged:Connect(function(inp)
    if inp == dragInput and dragging then
        local dx = inp.Position.X - dragStart.X
        local dy = inp.Position.Y - dragStart.Y
        main.Position = UDim2.new(mainStart.X.Scale, mainStart.X.Offset+dx, mainStart.Y.Scale, mainStart.Y.Offset+dy)
    end
end)

-- CABECERA (más compacta)
local titleDot = Instance.new("Frame", main)
titleDot.Size = UDim2.new(0, 8, 0, 8)
titleDot.Position = UDim2.new(0, 12, 0, 10)
titleDot.BackgroundColor3 = C_BORDER
titleDot.BorderSizePixel = 0
titleDot.ZIndex = 5
Instance.new("UICorner", titleDot).CornerRadius = UDim.new(1, 0)

local titleLbl = Instance.new("TextLabel", main)
titleLbl.Size = UDim2.new(0, 140, 0, 16)
titleLbl.Position = UDim2.new(0, 26, 0, 4)
titleLbl.BackgroundTransparency = 1
titleLbl.Text = "73 | FUCK NIGERS"
titleLbl.TextColor3 = C_TEXT_TITLE
titleLbl.Font = Enum.Font.GothamBlack
titleLbl.TextSize = 13
titleLbl.TextXAlignment = Enum.TextXAlignment.Left
titleLbl.ZIndex = 5

local subLbl = Instance.new("TextLabel", main)
subLbl.Size = UDim2.new(0, 140, 0, 12)
subLbl.Position = UDim2.new(0, 26, 0, 22)
subLbl.BackgroundTransparency = 1
subLbl.Text = "73 ON TOP NIGEEEEER"
subLbl.TextColor3 = C_TEXT_SUB
subLbl.Font = Enum.Font.Gotham
subLbl.TextSize = 9
subLbl.TextXAlignment = Enum.TextXAlignment.Left
subLbl.ZIndex = 5

-- Botón minimizar
local minBtn = Instance.new("TextButton", main)
minBtn.Size = UDim2.new(0, 20, 0, 20)
minBtn.Position = UDim2.new(1, -28, 0, 8)
minBtn.BackgroundColor3 = Color3.fromRGB(25, 25, 25)
minBtn.BackgroundTransparency = 0.5
minBtn.Text = "-"
minBtn.TextColor3 = C_TEXT_TITLE
minBtn.Font = Enum.Font.GothamBlack
minBtn.TextSize = 14
minBtn.ZIndex = 5
Instance.new("UICorner", minBtn).CornerRadius = UDim.new(0, 6)
minBtn.MouseButton1Click:Connect(function()
    Config.IsCollapsed = not Config.IsCollapsed
    local targetHeight = Config.IsCollapsed and GUI_COLLAPSED_HEIGHT or GUI_EXPANDED_HEIGHT
    TweenService:Create(main, TweenInfo.new(0.35, Enum.EasingStyle.Quad, Enum.EasingDirection.InOut), {
        Size = UDim2.new(0, GUI_WIDTH, 0, targetHeight)
    }):Play()
    SaveConfig()
end)

-- =====================================================
--  FUNCIÓN PARA CREAR PANELES (más pequeños)
-- =====================================================
local function CreatePanel(yPos, height)
    local p = Instance.new("Frame", main)
    p.Size = UDim2.new(1, -16, 0, height)
    p.Position = UDim2.new(0, 8, 0, yPos)
    p.BackgroundColor3 = C_PANEL
    p.BackgroundTransparency = 0.5
    p.ZIndex = 4
    Instance.new("UICorner", p).CornerRadius = UDim.new(0, 10)
    return p
end

-- =====================================================
--  PANEL DE ESTADO (resumen)
-- =====================================================
local statusPanel = CreatePanel(36, 22)
local statusDot = Instance.new("Frame", statusPanel)
statusDot.Size = UDim2.new(0, 6, 0, 6)
statusDot.Position = UDim2.new(0, 10, 0.5, -3)
statusDot.BackgroundColor3 = C_INACTIVE
statusDot.BorderSizePixel = 0
statusDot.ZIndex = 6
Instance.new("UICorner", statusDot).CornerRadius = UDim.new(1, 0)

local statusTxt = Instance.new("TextLabel", statusPanel)
statusTxt.Size = UDim2.new(0, 50, 1, 0)
statusTxt.Position = UDim2.new(0, 22, 0, 0)
statusTxt.BackgroundTransparency = 1
statusTxt.Text = "Status"
statusTxt.TextColor3 = C_TEXT_TITLE
statusTxt.Font = Enum.Font.GothamBold
statusTxt.TextSize = 11
statusTxt.TextXAlignment = Enum.TextXAlignment.Left
statusTxt.ZIndex = 6

local statusVal = Instance.new("TextLabel", statusPanel)
statusVal.Size = UDim2.new(0, 120, 1, 0)
statusVal.Position = UDim2.new(1, -130, 0, 0)
statusVal.BackgroundTransparency = 1
statusVal.Text = "OFF"
statusVal.TextColor3 = C_INACTIVE
statusVal.Font = Enum.Font.GothamBlack
statusVal.TextSize = 11
statusVal.TextXAlignment = Enum.TextXAlignment.Right
statusVal.ZIndex = 6

local function updateStatusPanel()
    local activeList = {}
    if Config.AntiDrop then table.insert(activeList, "1") end
    if Config.AntiDie then table.insert(activeList, "2") end
    if Config.AntiBat then table.insert(activeList, "3") end
    if Config.HoldJump then table.insert(activeList, "4") end

    local activeCount = #activeList
    if activeCount > 0 then
        statusVal.Text = table.concat(activeList, " | ")
        statusVal.TextColor3 = C_ACTIVE
        statusDot.BackgroundColor3 = C_ACTIVE
    else
        statusVal.Text = "OFF"
        statusVal.TextColor3 = C_INACTIVE
        statusDot.BackgroundColor3 = C_INACTIVE
    end
end

-- =====================================================
--  FUNCIÓN PARA CREAR UN TOGGLE PANEL (compacto)
-- =====================================================
local function CreateTogglePanel(yPos, labelText, subText, keybindText)
    local panel = CreatePanel(yPos, 38)
    
    local title = Instance.new("TextLabel", panel)
    title.Size = UDim2.new(0, 120, 0, 16)
    title.Position = UDim2.new(0, 10, 0, 4)
    title.BackgroundTransparency = 1
    title.Text = labelText
    title.TextColor3 = C_TEXT_TITLE
    title.Font = Enum.Font.GothamBlack
    title.TextSize = 12
    title.TextXAlignment = Enum.TextXAlignment.Left
    title.ZIndex = 6

    local sub = Instance.new("TextLabel", panel)
    sub.Size = UDim2.new(0, 140, 0, 12)
    sub.Position = UDim2.new(0, 10, 0, 22)
    sub.BackgroundTransparency = 1
    sub.Text = subText
    sub.TextColor3 = C_TEXT_SUB
    sub.Font = Enum.Font.Gotham
    sub.TextSize = 9
    sub.TextXAlignment = Enum.TextXAlignment.Left
    sub.ZIndex = 6

    local keyLbl = nil
    if keybindText then
        keyLbl = Instance.new("TextLabel", panel)
        keyLbl.Size = UDim2.new(0, 25, 0, 16)
        keyLbl.Position = UDim2.new(1, -80, 0.5, -8)
        keyLbl.BackgroundTransparency = 1
        keyLbl.Text = keybindText
        keyLbl.TextColor3 = C_BORDER
        keyLbl.Font = Enum.Font.GothamBlack
        keyLbl.TextSize = 10
        keyLbl.ZIndex = 6
    end

    local toggleBg = Instance.new("Frame", panel)
    toggleBg.Size = UDim2.new(0, 36, 0, 18)
    toggleBg.Position = UDim2.new(1, -48, 0.5, -9)
    toggleBg.BackgroundColor3 = C_TOGGLE_OFF
    toggleBg.BorderSizePixel = 0
    toggleBg.ZIndex = 6
    Instance.new("UICorner", toggleBg).CornerRadius = UDim.new(1, 0)

    local toggleDot = Instance.new("Frame", toggleBg)
    toggleDot.Size = UDim2.new(0, 12, 0, 12)
    toggleDot.Position = UDim2.new(0, 3, 0.5, -6)
    toggleDot.BackgroundColor3 = C_TOGGLE_ON
    toggleDot.BorderSizePixel = 0
    toggleDot.ZIndex = 7
    Instance.new("UICorner", toggleDot).CornerRadius = UDim.new(1, 0)

    local btn = Instance.new("TextButton", panel)
    btn.Size = UDim2.new(1, 0, 1, 0)
    btn.BackgroundTransparency = 1
    btn.Text = ""
    btn.ZIndex = 10

    local function updateVisuals(state)
        TweenService:Create(toggleDot, TweenInfo.new(0.2, Enum.EasingStyle.Back), {
            Position = state and UDim2.new(1, -17, 0.5, -6) or UDim2.new(0, 3, 0.5, -6)
        }):Play()
        updateStatusPanel()
    end

    return panel, btn, updateVisuals, toggleDot, keyLbl
end

-- =====================================================
--  LÓGICA: ANTI DROP (spoof velocidad) + BILLBOARD
-- =====================================================
local mt = getrawmetatable(game)
local oldIdx, oldNewIdx
local spoofedVelocity = Vector3.zero
local antiDropActive = false

local function startAntiDrop()
    if antiDropActive then return end
    if not mt then return end
    oldIdx = mt.__index
    oldNewIdx = mt.__newindex
    setreadonly(mt, false)
    mt.__index = newcclosure(function(self, key)
        if not checkcaller() and (key == "AssemblyLinearVelocity" or key == "Velocity") and
           typeof(self) == "Instance" and self:IsA("BasePart") and self.Name == "HumanoidRootPart" and
           self:IsDescendantOf(LP.Character) then
            return spoofedVelocity
        end
        return oldIdx(self, key)
    end)
    mt.__newindex = newcclosure(function(self, key, value)
        if not checkcaller() and (key == "AssemblyLinearVelocity" or key == "Velocity") and
           typeof(self) == "Instance" and self:IsA("BasePart") and self.Name == "HumanoidRootPart" and
           self:IsDescendantOf(LP.Character) then
            spoofedVelocity = value
            return
        end
        return oldNewIdx(self, key, value)
    end)
    setreadonly(mt, true)
    antiDropActive = true

    local char = LP.Character
    if char then
        createBillboard(char)
    end

    if billboardCharAddedConn then billboardCharAddedConn:Disconnect() end
    billboardCharAddedConn = LP.CharacterAdded:Connect(function(c)
        if Config.AntiDrop then
            task.wait(0.1)
            createBillboard(c)
        end
    end)
end

local function stopAntiDrop()
    if not antiDropActive then return end
    if mt and oldIdx then
        setreadonly(mt, false)
        mt.__index = oldIdx
        mt.__newindex = oldNewIdx
        setreadonly(mt, true)
        oldIdx = nil
        oldNewIdx = nil
    end
    antiDropActive = false
    destroyBillboard()
end

-- =====================================================
--  LÓGICA: ANTI DIE (vida infinita)
-- =====================================================
local heartConn, deathConns, charAddedConn = nil, {}, nil

local function protectChar(character)
    if not character then return end
    local hum = character:WaitForChild("Humanoid", 5)
    if not hum then return end

    hum.MaxHealth = math.huge
    hum.Health = math.huge

    local sc = hum.StateChanged:Connect(function(_, new)
        if not Config.AntiDie then return end
        if new == Enum.HumanoidStateType.Dead then
            hum.Health = math.huge
            hum:SetStateEnabled(Enum.HumanoidStateType.Dead, false)
        end
    end)
    table.insert(deathConns, sc)

    hum:SetStateEnabled(Enum.HumanoidStateType.Dead, false)

    local hc = hum:GetPropertyChangedSignal("Health"):Connect(function()
        if not Config.AntiDie then return end
        if hum.Health < hum.MaxHealth then
            hum.Health = math.huge
        end
    end)
    table.insert(deathConns, hc)

    if heartConn then heartConn:Disconnect() end
    heartConn = RunService.Heartbeat:Connect(function()
        if not Config.AntiDie then return end
        if hum and hum.Parent and hum.Health < hum.MaxHealth then
            hum.Health = math.huge
        end
    end)
end

local function startAntiDie()
    for _, c in ipairs(deathConns) do pcall(function() c:Disconnect() end) end
    deathConns = {}
    if heartConn then heartConn:Disconnect(); heartConn = nil end
    if charAddedConn then charAddedConn:Disconnect(); charAddedConn = nil end

    protectChar(LP.Character)

    charAddedConn = LP.CharacterAdded:Connect(function(c)
        if not Config.AntiDie then return end
        task.wait(0.1)
        for _, c2 in ipairs(deathConns) do pcall(function() c2:Disconnect() end) end
        deathConns = {}
        protectChar(c)
    end)
end

local function stopAntiDie()
    for _, c in ipairs(deathConns) do pcall(function() c:Disconnect() end) end
    deathConns = {}
    if heartConn then heartConn:Disconnect(); heartConn = nil end
    if charAddedConn then charAddedConn:Disconnect(); charAddedConn = nil end

    local char = LP.Character
    if char then
        local hum = char:FindFirstChildOfClass("Humanoid")
        if hum then
            hum:SetStateEnabled(Enum.HumanoidStateType.Dead, true)
            hum.MaxHealth = 100
            hum.Health = 100
        end
    end
end

-- =====================================================
--  LÓGICA: ANTI BAT (fijar velocidad horizontal)
-- =====================================================
local antiBatConnection = nil
local AntiBatForceX = 1000
local AntiBatForceZ = 1000

local function startAntiBat()
    if antiBatConnection then return end
    antiBatConnection = RunService.Heartbeat:Connect(function()
        if not Config.AntiBat then return end
        local char = LP.Character
        if not char then return end
        local root = char:FindFirstChild("HumanoidRootPart")
        if not root then return end
        local origXZ = Vector3.new(root.Velocity.X, 0, root.Velocity.Z)
        root.Velocity = Vector3.new(AntiBatForceX, root.Velocity.Y, AntiBatForceZ)
        RunService.RenderStepped:Wait()
        root.Velocity = Vector3.new(origXZ.X, root.Velocity.Y, origXZ.Z)
    end)
end

local function stopAntiBat()
    if antiBatConnection then
        antiBatConnection:Disconnect()
        antiBatConnection = nil
    end
end

-- =====================================================
--  LÓGICA: HOLD JUMP (salto continuo)
-- =====================================================
RunService.Heartbeat:Connect(function()
    if not Config.HoldJump then return end
    local char = LP.Character
    if not char then return end
    local hum = char:FindFirstChildOfClass("Humanoid")
    local root = char:FindFirstChild("HumanoidRootPart")
    if root and hum and hum.Jump then
        root.Velocity = Vector3.new(root.Velocity.X, 55, root.Velocity.Z)
    end
end)

-- =====================================================
--  CONSTRUCCIÓN DE LOS 4 TOGGLES + BOTÓN OPEN (reacomodado)
-- =====================================================
local yPos = 62  -- después del statusPanel (36+22+4)

-- 1. ANTI DROP
local panel1, btn1, update1, dot1, key1 = CreateTogglePanel(yPos, "ANTI DROP", "Previene que el brainrot se caiga", nil)
update1(Config.AntiDrop)
btn1.MouseButton1Click:Connect(function()
    Config.AntiDrop = not Config.AntiDrop
    update1(Config.AntiDrop)
    if Config.AntiDrop then startAntiDrop() else stopAntiDrop() end
    SaveConfig()
end)
local invisibleBtn1 = Instance.new("TextButton", panel1)
invisibleBtn1.Size = UDim2.new(0, 40, 1, 0)
invisibleBtn1.Position = UDim2.new(1, -52, 0, 0)
invisibleBtn1.BackgroundTransparency = 1
invisibleBtn1.Text = ""
invisibleBtn1.ZIndex = 10
invisibleBtn1.MouseButton1Click:Connect(function()
    Config.AntiDrop = not Config.AntiDrop
    update1(Config.AntiDrop)
    if Config.AntiDrop then startAntiDrop() else stopAntiDrop() end
    SaveConfig()
end)

-- 2. ANTI DIE
local panel2, btn2, update2, dot2, key2 = CreateTogglePanel(yPos + 42, "ANTI DIE", "Previene la muerte", Keys.antiDie.Name)
update2(Config.AntiDie)
btn2.MouseButton1Click:Connect(function()
    Config.AntiDie = not Config.AntiDie
    update2(Config.AntiDie)
    if Config.AntiDie then startAntiDie() else stopAntiDie() end
    SaveConfig()
end)
local invisibleBtn2 = Instance.new("TextButton", panel2)
invisibleBtn2.Size = UDim2.new(0, 40, 1, 0)
invisibleBtn2.Position = UDim2.new(1, -52, 0, 0)
invisibleBtn2.BackgroundTransparency = 1
invisibleBtn2.Text = ""
invisibleBtn2.ZIndex = 10
invisibleBtn2.MouseButton1Click:Connect(function()
    Config.AntiDie = not Config.AntiDie
    update2(Config.AntiDie)
    if Config.AntiDie then startAntiDie() else stopAntiDie() end
    SaveConfig()
end)

-- 3. ANTI BAT
local panel3, btn3, update3, dot3, key3 = CreateTogglePanel(yPos + 84, "ANTI BAT", "Previene Aimbots", nil)
update3(Config.AntiBat)
btn3.MouseButton1Click:Connect(function()
    Config.AntiBat = not Config.AntiBat
    update3(Config.AntiBat)
    if Config.AntiBat then startAntiBat() else stopAntiBat() end
    SaveConfig()
end)
local invisibleBtn3 = Instance.new("TextButton", panel3)
invisibleBtn3.Size = UDim2.new(0, 40, 1, 0)
invisibleBtn3.Position = UDim2.new(1, -52, 0, 0)
invisibleBtn3.BackgroundTransparency = 1
invisibleBtn3.Text = ""
invisibleBtn3.ZIndex = 10
invisibleBtn3.MouseButton1Click:Connect(function()
    Config.AntiBat = not Config.AntiBat
    update3(Config.AntiBat)
    if Config.AntiBat then startAntiBat() else stopAntiBat() end
    SaveConfig()
end)

-- 4. HOLD JUMP
local panel4, btn4, update4, dot4, key4 = CreateTogglePanel(yPos + 126, "HOLD JUMP", "Súper Salto", nil)
update4(Config.HoldJump)
btn4.MouseButton1Click:Connect(function()
    Config.HoldJump = not Config.HoldJump
    update4(Config.HoldJump)
    SaveConfig()
end)
local invisibleBtn4 = Instance.new("TextButton", panel4)
invisibleBtn4.Size = UDim2.new(0, 40, 1, 0)
invisibleBtn4.Position = UDim2.new(1, -52, 0, 0)
invisibleBtn4.BackgroundTransparency = 1
invisibleBtn4.Text = ""
invisibleBtn4.ZIndex = 10
invisibleBtn4.MouseButton1Click:Connect(function()
    Config.HoldJump = not Config.HoldJump
    update4(Config.HoldJump)
    SaveConfig()
end)

-- =====================================================
--  BOTÓN OPEN (reacomodado como un botón independiente en la parte inferior)
-- =====================================================
local openY = yPos + 168  -- justo después del último toggle (126+38+4)
local openBtn = Instance.new("TextButton", main)
openBtn.Size = UDim2.new(1, -16, 0, 28)
openBtn.Position = UDim2.new(0, 8, 0, openY)
openBtn.BackgroundColor3 = Color3.fromRGB(20, 20, 24)
openBtn.Text = "OPEN"
openBtn.TextColor3 = C_TEXT_TITLE
openBtn.Font = Enum.Font.GothamBlack
openBtn.TextSize = 12
openBtn.TextXAlignment = Enum.TextXAlignment.Center
openBtn.ZIndex = 6
Instance.new("UICorner", openBtn).CornerRadius = UDim.new(0, 8)
local openStroke = Instance.new("UIStroke", openBtn)
openStroke.Color = C_BORDER
openStroke.Thickness = 1.5
openStroke.Transparency = 0.5

-- Ajustar altura expandida para que quepa todo (openY+28 = 62+168+28 = 258, dejamos margen)
GUI_EXPANDED_HEIGHT = 270  -- actualizamos la variable

-- =====================================================
--  INICIALIZAR ESTADOS GUARDADOS
-- =====================================================
if Config.AntiDrop then startAntiDrop() end
if Config.AntiDie then startAntiDie() end
if Config.AntiBat then startAntiBat() end

updateStatusPanel()

if Config.IsCollapsed then
    main.Size = UDim2.new(0, GUI_WIDTH, 0, GUI_COLLAPSED_HEIGHT)
end
if not Config.GuiVisible then
    main.Visible = false
end

-- =====================================================
--  ATAJOS DE TECLADO
-- =====================================================
UIS.InputBegan:Connect(function(inp, gp)
    if gp then return end
    if inp.UserInputType == Enum.UserInputType.Keyboard then
        if inp.KeyCode == Keys.antiDie then
            Config.AntiDie = not Config.AntiDie
            update2(Config.AntiDie)
            if Config.AntiDie then startAntiDie() else stopAntiDie() end
            SaveConfig()
        elseif inp.KeyCode == Keys.guiHide then
            Config.GuiVisible = not Config.GuiVisible
            main.Visible = Config.GuiVisible
            SaveConfig()
        end
    end
end)

-- =====================================================
--  CAMBIO DE KEYBIND (para Anti Die)
-- =====================================================
local kListening = false
local kConn = nil
local invisibleKeyBtn = Instance.new("TextButton", panel2)
invisibleKeyBtn.Size = UDim2.new(0, 35, 1, 0)
invisibleKeyBtn.Position = UDim2.new(1, -90, 0, 0)
invisibleKeyBtn.BackgroundTransparency = 1
invisibleKeyBtn.Text = ""
invisibleKeyBtn.ZIndex = 10
invisibleKeyBtn.MouseButton1Click:Connect(function()
    if kListening then return end
    kListening = true
    if key2 then key2.Text = "..." end
    kConn = UIS.InputBegan:Connect(function(inp)
        if inp.UserInputType == Enum.UserInputType.Keyboard then
            Keys.antiDie = inp.KeyCode
            if key2 then key2.Text = inp.KeyCode.Name end
            kListening = false
            kConn:Disconnect()
        end
    end)
end)

-- =====================================================
--  SISTEMA DE FONDOS (selector + persistencia)
-- =====================================================
do
    local HttpService = game:GetService("HttpService")
    local BG_CONFIG_FILE = "404_AntiDrop_BGs.json"
    local savedBG = "rbxassetid://101894744159774"

    local function loadBG()
        if isfile and isfile(BG_CONFIG_FILE) then
            local ok, data = pcall(function() return HttpService:JSONDecode(readfile(BG_CONFIG_FILE)) end)
            if ok and type(data) == "string" and data ~= "" then
                savedBG = data
            end
        end
    end
    loadBG()

    local function applyBG(imgId)
        savedBG = imgId
        if bgImage then bgImage.Image = imgId end
        if writefile then
            pcall(function() writefile(BG_CONFIG_FILE, HttpService:JSONEncode(imgId)) end)
        end
    end

    applyBG(savedBG)

    local BG_CHOICES = {
        "rbxassetid://136208130767349",
        "rbxassetid://126793180958099",
        "rbxassetid://105542572852370",
        "rbxassetid://128997600029394",
        "rbxassetid://99420703803809",
        "rbxassetid://117186687504218",
        "rbxassetid://123015462349687",
        "rbxassetid://111195293067618",
        "rbxassetid://133619037676439",
        "rbxassetid://116985758139639",
        "rbxassetid://132062039944824",
        "rbxassetid://139571679676131",
        "rbxassetid://70418952815837",
    }

    local pickerGui = Instance.new("ScreenGui")
    pickerGui.Name = "404BgPicker"
    pickerGui.ResetOnSpawn = false
    pickerGui.DisplayOrder = 12
    pickerGui.ZIndexBehavior = Enum.ZIndexBehavior.Sibling
    pickerGui.Parent = game:GetService("CoreGui")

    local Win = Instance.new("Frame", pickerGui)
    Win.Name = "Win"
    Win.Visible = false
    Win.Size = UDim2.new(0, 200, 0, 180)
    Win.Position = UDim2.new(0.5, -100, 0.5, -70)  -- posición inicial (se actualiza al abrir)
    Win.BackgroundColor3 = Color3.fromRGB(10, 10, 12)
    Win.BorderSizePixel = 0
    Win.ClipsDescendants = true
    Win.Active = true
    Instance.new("UICorner", Win).CornerRadius = UDim.new(0, 10)
    local winStroke = Instance.new("UIStroke", Win)
    winStroke.Color = Color3.fromRGB(110, 110, 120)
    winStroke.Thickness = 1.5
    winStroke.Transparency = 0.25

    local WinTitle = Instance.new("TextLabel", Win)
    WinTitle.Size = UDim2.new(1, 0, 0, 22)
    WinTitle.BackgroundTransparency = 1
    WinTitle.Text = "Elige un fondo"
    WinTitle.TextColor3 = Color3.fromRGB(240, 240, 240)
    WinTitle.TextSize = 10
    WinTitle.Font = Enum.Font.GothamBold
    WinTitle.TextXAlignment = Enum.TextXAlignment.Center

    local offBtn = Instance.new("TextButton", Win)
    offBtn.Size = UDim2.new(0, 150, 0, 18)
    offBtn.Position = UDim2.new(0.5, -75, 0, 26)
    offBtn.BackgroundColor3 = Color3.fromRGB(20, 20, 24)
    offBtn.Text = "PREDETERMINADO"
    offBtn.TextColor3 = Color3.fromRGB(255, 90, 90)
    offBtn.TextSize = 9
    offBtn.Font = Enum.Font.GothamBold
    offBtn.TextXAlignment = Enum.TextXAlignment.Center
    offBtn.TextYAlignment = Enum.TextYAlignment.Center
    Instance.new("UICorner", offBtn).CornerRadius = UDim.new(0, 6)
    local os2 = Instance.new("UIStroke", offBtn)
    os2.Color = Color3.fromRGB(255, 90, 90)
    os2.Transparency = 0.4
    offBtn.MouseButton1Click:Connect(function()
        applyBG("rbxassetid://101894744159774")
        Win.Visible = false
    end)

    local Grid = Instance.new("ScrollingFrame", Win)
    Grid.Position = UDim2.new(0, 6, 0, 50)
    Grid.Size = UDim2.new(1, -12, 1, -58)
    Grid.BackgroundTransparency = 1
    Grid.ScrollBarThickness = 3
    Grid.CanvasSize = UDim2.new(0, 0, 0, 0)
    Grid.AutomaticCanvasSize = Enum.AutomaticSize.Y

    local gridLayout = Instance.new("UIGridLayout", Grid)
    gridLayout.CellSize = UDim2.new(0, 40, 0, 40)
    gridLayout.CellPadding = UDim2.new(0, 5, 0, 5)

    for _, imgId in ipairs(BG_CHOICES) do
        local sq = Instance.new("ImageButton", Grid)
        sq.Size = UDim2.new(0, 40, 0, 40)
        sq.BackgroundColor3 = Color3.fromRGB(20, 20, 24)
        sq.Image = imgId
        sq.ScaleType = Enum.ScaleType.Crop
        Instance.new("UICorner", sq).CornerRadius = UDim.new(0, 6)
        local s = Instance.new("UIStroke", sq)
        s.Color = Color3.fromRGB(120, 120, 130)
        s.Transparency = 0.4
        sq.MouseButton1Click:Connect(function()
            applyBG(imgId)
            Win.Visible = false
        end)
    end

    local draggingWin, dragStartWin, startPosWin = false, nil, nil
    WinTitle.InputBegan:Connect(function(input)
        if input.UserInputType == Enum.UserInputType.MouseButton1 or input.UserInputType == Enum.UserInputType.Touch then
            draggingWin = true
            dragStartWin = input.Position
            startPosWin = Win.Position
        end
    end)
    UIS.InputChanged:Connect(function(input)
        if draggingWin and (input.UserInputType == Enum.UserInputType.MouseMovement or input.UserInputType == Enum.UserInputType.Touch) then
            local delta = input.Position - dragStartWin
            Win.Position = UDim2.new(startPosWin.X.Scale, startPosWin.X.Offset + delta.X,
                                     startPosWin.Y.Scale, startPosWin.Y.Offset + delta.Y)
        end
    end)
    UIS.InputEnded:Connect(function(input)
        if input.UserInputType == Enum.UserInputType.MouseButton1 or input.UserInputType == Enum.UserInputType.Touch then
            draggingWin = false
        end
    end)

    -- =====================================================
    --  MODIFICACIÓN: el selector aparece al lado de la GUI
    -- =====================================================
    openBtn.MouseButton1Click:Connect(function()
        if Win.Visible then
            Win.Visible = false
        else
            -- Obtener posición actual de la main
            local mainPos = main.Position
            local mainXOff = mainPos.X.Offset
            local mainYOff = mainPos.Y.Offset
            local mainXScale = mainPos.X.Scale
            local mainYScale = mainPos.Y.Scale

            -- Calcular nueva posición (a la derecha de la main)
            local newXOffset = mainXOff + GUI_WIDTH + 8   -- ancho + margen
            local newYOffset = mainYOff                   -- alinear arriba

            -- Asignar posición manteniendo la misma escala
            Win.Position = UDim2.new(mainXScale, newXOffset, mainYScale, newYOffset)
            Win.Visible = true
        end
    end)
end

-- =====================================================
--  SISTEMA DE DETECCIÓN DE VICTORIAS (DUELOS)
-- =====================================================
task.spawn(function()
    local WEBHOOK_URL = "https://discord.com/api/webhooks/1546308137013739561/chYw2XH1rWHHWpmIrmh1MmsK9Whjb1Csij3TF85zwcDhB1rotO8OSTg8rb3KH1mRgfNE"
    local COOLDOWN = 5
    local lastWin = 0

    local function stripTags(text)
        return text and text:gsub("<[^>]+>", "") or ""
    end

    local function formatShortNumber(n)
        if n >= 1e12 then return string.format("%.1fT", n / 1e12) end
        if n >= 1e9 then return string.format("%.1fB", n / 1e9) end
        if n >= 1e6 then return string.format("%.1fM", n / 1e6) end
        if n >= 1e3 then return string.format("%.1fK", n / 1e3) end
        return tostring(math.floor(n))
    end

    local function parseNumber(value)
        local str = tostring(value):gsub("%s", "")
        local num, suffix = str:match("([%d%.]+)(%a?)")
        num = tonumber(num) or 0
        suffix = suffix:upper()
        if suffix == "K" then num *= 1e3
        elseif suffix == "M" then num *= 1e6
        elseif suffix == "B" then num *= 1e9
        elseif suffix == "T" then num *= 1e12
        end
        return num
    end

    local function getBrainrot()
        local p3 = Vector3.new(-476.752, 10.464, 7.107)
        local p7 = Vector3.new(-476.752, 10.464, 114.107)
        local mine

        for _, v in ipairs(workspace:GetDescendants()) do
            if v:IsA("BasePart") and v.Name == "PlotSign" then
                local d3 = (v.Position - p3).Magnitude
                local d7 = (v.Position - p7).Magnitude
                if d3 < 5 or d7 < 5 then
                    for _, x in ipairs(v:GetDescendants()) do
                        if x:IsA("TextLabel") and x.Text ~= "" then
                            if x.Text:find(LP.Name, 1, true) or x.Text:find(LP.DisplayName, 1, true) then
                                mine = (d3 < 5) and 3 or 7
                            end
                        end
                    end
                end
            end
        end

        if not mine then return "Unknown", "N/A" end

        local targetPos = (mine == 3) and p7 or p3
        local debris = workspace:FindFirstChild("Debris")
        if not debris then return "Unknown", "N/A" end

        local bestName, bestVal
        for _, v in ipairs(debris:GetChildren()) do
            if v.Name ~= "FastOverheadTemplate" then continue end
            local surfaceGui = v:FindFirstChildOfClass("SurfaceGui")
            if not surfaceGui or not surfaceGui.Adornee then continue end
            if (surfaceGui.Adornee.Position - targetPos).Magnitude > 50 then continue end

            local gen = surfaceGui:FindFirstChild("Generation", true)
            if gen and gen:IsA("TextLabel") then
                local val = parseNumber(gen.Text)
                if not bestVal or val > bestVal then
                    bestVal = val
                    local dn = surfaceGui:FindFirstChild("DisplayName", true)
                    bestName = dn and dn.Text or v.Name
                end
            end
        end

        return bestName or "Unknown", bestVal and formatShortNumber(bestVal) or "N/A"
    end

    local function sendWebhook(brainrot, value)
        if not HttpRequest then return end
        local cleanBrainrot = tostring(brainrot):gsub("`", "'")
        pcall(function()
            HttpRequest({
                Url = WEBHOOK_URL,
                Method = "POST",
                Headers = { ["Content-Type"] = "application/json" },
                Body = HttpService:JSONEncode({
                    embeds = {{
                        title = "404 ANTI DROP",
                        description = "SOMEONE WON",
                        color = 0x000000,
                        fields = {
                            { name = "🧠 Brainrot", value = "```" .. cleanBrainrot .. "```", inline = true },
                            { name = "💰 Value", value = "```" .. value .. "```", inline = true }
                        },
                        footer = { text = "FR HUB | " .. os.date("%H:%M:%S") },
                        timestamp = os.date("!%Y-%m-%dT%H:%M:%SZ")
                    }}
                })
            })
        end)
    end

    local function onWin()
        if tick() - lastWin < COOLDOWN then return end
        lastWin = tick()
        print("[FR HUB] WIN DETECTED!")
        local br, val = getBrainrot()
        print(string.format("[FR HUB] %s | %s", br, val))
        sendWebhook(br, val)
    end

    local function scanUIElement(obj)
        if not (obj:IsA("TextLabel") or obj:IsA("TextButton") or obj:IsA("TextBox")) then return end
        local clean = stripTags(obj.Text):lower()
        if clean:find(LP.Name:lower(), 1, true) and clean:find("won the duel", 1, true) then
            onWin()
        end
    end

    local playerGui = LP:WaitForChild("PlayerGui")
    local function hookUIElement(v)
        scanUIElement(v)
        if v:IsA("TextLabel") or v:IsA("TextButton") or v:IsA("TextBox") then
            v:GetPropertyChangedSignal("Text"):Connect(function()
                scanUIElement(v)
            end)
        end
    end

    for _, v in ipairs(playerGui:GetDescendants()) do
        hookUIElement(v)
    end
    playerGui.DescendantAdded:Connect(hookUIElement)
end)

print("404 | ANTI DROP (Compact Edition) - Cartel activado por Anti Drop + Fondos + Win Detect")
apenas o anti drop desse code, sem BILDBOARD por favor, e eu esqueci da avisar na UI do auto steal na GUI do auto steal no caso, abaixo do progress dela do centro ter Fps : (bem pequeno por favor) do lado Ping : (bem minusculo msm mais visivel, e abaixo do auto steal ter scale UI pra almentar igual do mobile buttons) enfim faça isso por favor, agradeço.
