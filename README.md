-- Script Path: game:GetService("StarterPlayer").StarterPlayerScripts.CombatClient
-- Took 0.13s to decompile.

local v_u_1 = game:GetService("Players")
local v_u_2 = game:GetService("UserInputService")
local v_u_3 = game:GetService("ReplicatedStorage")
local v_u_4 = game:GetService("TweenService")
local v_u_5 = game:GetService("RunService")
local v_u_6 = require(v_u_3:WaitForChild("AttackData"))
local v_u_7 = require(v_u_3:WaitForChild("Controls"))
local v_u_8 = require(v_u_3:WaitForChild("BotActors"))
local v_u_9 = require(v_u_3:WaitForChild("StdPunch"))
local v_u_10 = v_u_3:WaitForChild("Remotes")
local v_u_11 = v_u_1.LocalPlayer
v_u_11:GetMouse()
local function v_u_17() -- name: lookTarget
    -- upvalues: (copy) v_u_11
    local v12 = workspace.CurrentCamera
    local v13 = RaycastParams.new()
    v13.FilterType = Enum.RaycastFilterType.Exclude
    v13.FilterDescendantsInstances = { v_u_11.Character }
    local v14 = v12.CFrame.Position
    local v15 = v12.CFrame.LookVector * 600
    local v16 = workspace:Raycast(v14, v15, v13)
    return v16 and v16.Position or v14 + v15
end
local function v_u_23() -- name: laserTarget
    -- upvalues: (copy) v_u_11, (copy) v_u_17
    local v18 = v_u_11.Character
    if v18 then
        v18 = v18:FindFirstChild("HumanoidRootPart")
    end
    local v19 = workspace.CurrentCamera.CFrame.LookVector
    local v20 = v19.X
    local v21 = v19.Z
    local v22 = Vector3.new(v20, 0, v21)
    if not v18 then
        return v_u_17()
    end
    if v22.Magnitude < 0.01 then
        v22 = v18.CFrame.LookVector
    end
    return v18.Position + v22.Unit * 1000
end
local function v_u_28() -- name: levelTarget
    -- upvalues: (copy) v_u_11, (copy) v_u_23
    local v24 = workspace.CurrentCamera
    local v25 = RaycastParams.new()
    v25.FilterType = Enum.RaycastFilterType.Exclude
    v25.FilterDescendantsInstances = { v_u_11.Character }
    local v26 = workspace:Raycast(v24.CFrame.Position, v24.CFrame.LookVector * 600, v25)
    if v26 then
        local v27 = v26.Instance:FindFirstAncestorOfClass("Model")
        if v27 then
            v27 = v27:FindFirstChildOfClass("Humanoid")
        end
        if v27 and v27.Health > 0 then
            return v26.Position
        end
    end
    return v_u_23()
end
local function v_u_40() -- name: hammerTarget
    -- upvalues: (copy) v_u_11
    local v29 = workspace.CurrentCamera
    local v30 = RaycastParams.new()
    v30.FilterType = Enum.RaycastFilterType.Exclude
    v30.FilterDescendantsInstances = { v_u_11.Character }
    local v31 = v29.CFrame.Position
    local v32 = v29.CFrame.LookVector
    local v33 = workspace:Raycast(v31, v32 * 1000, v30)
    if v33 then
        local v34 = v33.Instance:FindFirstAncestorOfClass("Model")
        if v34 and v34:FindFirstChildOfClass("Humanoid") then
            return v33.Position
        end
    end
    local v35 = v32.X
    local v36 = v32.Y
    local v37 = math.max(v36, 0.02)
    local v38 = v32.Z
    local v39 = Vector3.new(v35, v37, v38)
    if v39.Magnitude > 0.01 then
        v32 = v39.Unit or v32
    end
    return v31 + v32 * 1000
end
local function v_u_46() -- name: mouseTarget
    -- upvalues: (copy) v_u_7, (copy) v_u_17, (copy) v_u_2, (copy) v_u_11
    local v41 = workspace.CurrentCamera
    if v_u_7.mode() ~= "Keyboard" then
        return v_u_17()
    end
    local v42 = v_u_2:GetMouseLocation()
    if v42.X < 0 or (v42.Y < 0 or (v42.X > v41.ViewportSize.X or v42.Y > v41.ViewportSize.Y)) then
        return v_u_17()
    end
    local v43 = v41:ViewportPointToRay(v42.X, v42.Y)
    local v44 = RaycastParams.new()
    v44.FilterType = Enum.RaycastFilterType.Exclude
    v44.FilterDescendantsInstances = { v_u_11.Character }
    local v45 = workspace:Raycast(v43.Origin, v43.Direction * 1000, v44)
    return v45 and v45.Position or v43.Origin + v43.Direction * 1000
end
local v_u_47 = false
local function v_u_60(p48, p_u_49, p50)
    -- upvalues: (ref) v_u_47, (copy) v_u_5, (copy) v_u_11, (copy) v_u_23, (copy) v_u_10
    if not v_u_47 then
        v_u_47 = true
        local v_u_51 = os.clock() + (p50 or (p48.Charge or 0.5)) + 0.15
        local v_u_52 = 0
        local v_u_53 = nil
        v_u_53 = v_u_5.RenderStepped:Connect(function()
            -- upvalues: (ref) v_u_11, (copy) v_u_51, (ref) v_u_53, (ref) v_u_47, (copy) p_u_49, (ref) v_u_23, (ref) v_u_52, (ref) v_u_10
            local v54 = v_u_11.Character
            local v55
            if v54 then
                v55 = v54:FindFirstChild("HumanoidRootPart")
            else
                v55 = v54
            end
            if v55 and v_u_51 >= os.clock() then
                local v56 = (p_u_49 or v_u_23)()
                local v57 = v56.X - v55.Position.X
                local v58 = v56.Z - v55.Position.Z
                local v59 = Vector3.new(v57, 0, v58)
                if v59.Magnitude > 0.1 and not v54:GetAttribute("Ragdolled") then
                    v55.CFrame = CFrame.lookAt(v55.Position, v55.Position + v59)
                end
                if os.clock() - v_u_52 > 0.05 then
                    v_u_52 = os.clock()
                    v_u_10.Aim:FireServer(v56)
                end
            else
                v_u_53:Disconnect()
                v_u_47 = false
            end
        end)
    end
end
local v_u_61 = setmetatable({}, {
    ["__mode"] = "k"
})
local function v_u_69(p62) -- name: hookTool
    -- upvalues: (copy) v_u_61, (copy) v_u_6, (copy) v_u_11, (ref) v_u_60, (copy) v_u_40, (copy) v_u_17, (copy) v_u_23, (copy) v_u_46, (copy) v_u_9, (copy) v_u_10
    if p62:IsA("Tool") and not v_u_61[p62] then
        v_u_61[p62] = true
        p62.Activated:Connect(function()
            -- upvalues: (ref) v_u_6, (ref) v_u_11, (ref) v_u_60, (ref) v_u_40, (ref) v_u_17, (ref) v_u_23, (ref) v_u_46, (ref) v_u_9, (ref) v_u_10
            local v63 = v_u_6.Get(v_u_11:GetAttribute("EquippedAttack"))
            if v63 and (v63.Kind == "Laser" or v63.Kind == "Plasma") and v_u_11:GetAttribute("InFFA") then
                local v64 = v_u_60
                local v65
                if v63.Kind == "Plasma" then
                    v65 = v_u_40 or nil
                else
                    v65 = nil
                end
                v64(v63, v65)
            end
            local v66 = v_u_17
            if v63 and v63.Kind == "Laser" then
                v66 = v_u_23
            elseif v63 and (v63.Kind == "Hammer" or v63.Kind == "Plasma") then
                v66 = v_u_40
            elseif v63 and v63.Kind == "Orbital" then
                v66 = v_u_46
            end
            if v_u_11.Character and v_u_11.Character:GetAttribute("AimMouse") then
                v66 = v_u_46
            end
            local v67, v68 = v_u_9.click(v63)
            if v67 ~= false then
                v_u_10.Attack:FireServer(v66(), v67, v68)
            end
        end)
    end
end
v_u_11.CharacterAdded:Connect(function(p70)
    -- upvalues: (copy) v_u_61, (copy) v_u_6, (copy) v_u_11, (ref) v_u_60, (copy) v_u_40, (copy) v_u_17, (copy) v_u_23, (copy) v_u_46, (copy) v_u_9, (copy) v_u_10, (copy) v_u_69
    for _, v71 in p70:GetChildren() do
        if not v71:IsA("Tool") then
            continue
        end
        if v_u_61[v71] then
            continue
        end
        v_u_61[v71] = true
        v71.Activated:Connect(function()
            -- upvalues: (ref) v_u_6, (ref) v_u_11, (ref) v_u_60, (ref) v_u_40, (ref) v_u_17, (ref) v_u_23, (ref) v_u_46, (ref) v_u_9, (ref) v_u_10
            local v72 = v_u_6.Get(v_u_11:GetAttribute("EquippedAttack"))
            if v72 and (v72.Kind == "Laser" or v72.Kind == "Plasma") and v_u_11:GetAttribute("InFFA") then
                local v73 = v_u_60
                local v74
                if v72.Kind == "Plasma" then
                    v74 = v_u_40 or nil
                else
                    v74 = nil
                end
                v73(v72, v74)
            end
            local v75 = v_u_17
            if v72 and v72.Kind == "Laser" then
                v75 = v_u_23
            elseif v72 and (v72.Kind == "Hammer" or v72.Kind == "Plasma") then
                v75 = v_u_40
            elseif v72 and v72.Kind == "Orbital" then
                v75 = v_u_46
            end
            if v_u_11.Character and v_u_11.Character:GetAttribute("AimMouse") then
                v75 = v_u_46
            end
            local v76, v77 = v_u_9.click(v72)
            if v76 ~= false then
                v_u_10.Attack:FireServer(v75(), v76, v77)
            end
        end)
    end
    p70.ChildAdded:Connect(v_u_69)
    local v78 = v_u_11:WaitForChild("Backpack")
    for _, v79 in v78:GetChildren() do
        if not v79:IsA("Tool") then
            continue
        end
        if v_u_61[v79] then
            continue
        end
        v_u_61[v79] = true
        v79.Activated:Connect(function()
            -- upvalues: (ref) v_u_6, (ref) v_u_11, (ref) v_u_60, (ref) v_u_40, (ref) v_u_17, (ref) v_u_23, (ref) v_u_46, (ref) v_u_9, (ref) v_u_10
            local v80 = v_u_6.Get(v_u_11:GetAttribute("EquippedAttack"))
            if v80 and (v80.Kind == "Laser" or v80.Kind == "Plasma") and v_u_11:GetAttribute("InFFA") then
                local v81 = v_u_60
                local v82
                if v80.Kind == "Plasma" then
                    v82 = v_u_40 or nil
                else
                    v82 = nil
                end
                v81(v80, v82)
            end
            local v83 = v_u_17
            if v80 and v80.Kind == "Laser" then
                v83 = v_u_23
            elseif v80 and (v80.Kind == "Hammer" or v80.Kind == "Plasma") then
                v83 = v_u_40
            elseif v80 and v80.Kind == "Orbital" then
                v83 = v_u_46
            end
            if v_u_11.Character and v_u_11.Character:GetAttribute("AimMouse") then
                v83 = v_u_46
            end
            local v84, v85 = v_u_9.click(v80)
            if v84 ~= false then
                v_u_10.Attack:FireServer(v83(), v84, v85)
            end
        end)
    end
    v78.ChildAdded:Connect(v_u_69)
end)
local v_u_86
if v_u_11.Character then
    local v87 = v_u_11.Character
    v_u_86 = v_u_60
    for _, v88 in v87:GetChildren() do
        if not v88:IsA("Tool") then
            continue
        end
        if v_u_61[v88] then
            continue
        end
        v_u_61[v88] = true
        v88.Activated:Connect(function()
            -- upvalues: (copy) v_u_6, (copy) v_u_11, (ref) v_u_86, (copy) v_u_40, (copy) v_u_17, (copy) v_u_23, (copy) v_u_46, (copy) v_u_9, (copy) v_u_10
            local v89 = v_u_6.Get(v_u_11:GetAttribute("EquippedAttack"))
            if v89 and (v89.Kind == "Laser" or v89.Kind == "Plasma") and v_u_11:GetAttribute("InFFA") then
                local v90 = v_u_86
                local v91
                if v89.Kind == "Plasma" then
                    v91 = v_u_40 or nil
                else
                    v91 = nil
                end
                v90(v89, v91)
            end
            local v92 = v_u_17
            if v89 and v89.Kind == "Laser" then
                v92 = v_u_23
            elseif v89 and (v89.Kind == "Hammer" or v89.Kind == "Plasma") then
                v92 = v_u_40
            elseif v89 and v89.Kind == "Orbital" then
                v92 = v_u_46
            end
            if v_u_11.Character and v_u_11.Character:GetAttribute("AimMouse") then
                v92 = v_u_46
            end
            local v93, v94 = v_u_9.click(v89)
            if v93 ~= false then
                v_u_10.Attack:FireServer(v92(), v93, v94)
            end
        end)
    end
    v87.ChildAdded:Connect(v_u_69)
else
    v_u_86 = v_u_60
end
local v95 = v_u_11:WaitForChild("Backpack")
for _, v96 in v95:GetChildren() do
    if not v96:IsA("Tool") then
        continue
    end
    if v_u_61[v96] then
        continue
    end
    v_u_61[v96] = true
    v96.Activated:Connect(function()
        -- upvalues: (copy) v_u_6, (copy) v_u_11, (ref) v_u_86, (copy) v_u_40, (copy) v_u_17, (copy) v_u_23, (copy) v_u_46, (copy) v_u_9, (copy) v_u_10
        local v97 = v_u_6.Get(v_u_11:GetAttribute("EquippedAttack"))
        if v97 and (v97.Kind == "Laser" or v97.Kind == "Plasma") and v_u_11:GetAttribute("InFFA") then
            local v98 = v_u_86
            local v99
            if v97.Kind == "Plasma" then
                v99 = v_u_40 or nil
            else
                v99 = nil
            end
            v98(v97, v99)
        end
        local v100 = v_u_17
        if v97 and v97.Kind == "Laser" then
            v100 = v_u_23
        elseif v97 and (v97.Kind == "Hammer" or v97.Kind == "Plasma") then
            v100 = v_u_40
        elseif v97 and v97.Kind == "Orbital" then
            v100 = v_u_46
        end
        if v_u_11.Character and v_u_11.Character:GetAttribute("AimMouse") then
            v100 = v_u_46
        end
        local v101, v102 = v_u_9.click(v97)
        if v101 ~= false then
            v_u_10.Attack:FireServer(v100(), v101, v102)
        end
    end)
end
v95.ChildAdded:Connect(v_u_69)
v_u_7.onInput(function(p103, p104)
    -- upvalues: (copy) v_u_6, (copy) v_u_11, (ref) v_u_86, (copy) v_u_28, (copy) v_u_10, (copy) v_u_23, (copy) v_u_46, (copy) v_u_17
    if not p104 then
        if p103.KeyCode == Enum.KeyCode.E then
            local v105 = v_u_6.Get(v_u_11:GetAttribute("EquippedAttack"))
            if v105 and v105.Ability then
                local v106 = v105.Ability
                if v106.Aim == "Level" then
                    if v_u_11:GetAttribute("InFFA") and ((v_u_11:GetAttribute("AbilityReadyAt") or 0) <= workspace:GetServerTimeNow() and (not v106.MinPhase or (v_u_11:GetAttribute("AttackPhase") or 1) >= v106.MinPhase)) then
                        v_u_86(v106, v_u_28, (v106.Windup or 0) + ((v106.Count or 1) - 1) * (v106.Gap or 0) + 0.1)
                    end
                    v_u_10.Ability:FireServer((v_u_28()))
                    return
                end
                local v107 = v105.Ability.Kind == "NinjaDash" and true or v105.Ability.Kind == "PhaseDash"
                v_u_10.Ability:FireServer(v107 and v_u_23() or (v105.Ability.Kind == "OrbitalBarrage" and v_u_46() or v_u_17()))
            end
        end
    end
end)
local v_u_108 = Instance.new("ScreenGui")
v_u_108.Name = "CombatHUD"
v_u_108.ResetOnSpawn = false
v_u_108.IgnoreGuiInset = true
v_u_108.Parent = v_u_11:WaitForChild("PlayerGui")
local v_u_109 = Instance.new("Frame")
v_u_109.AnchorPoint = Vector2.new(0.5, 1)
v_u_109.Position = UDim2.new(0.5, 0, 1, -90)
v_u_109.Size = UDim2.fromOffset(0, 40)
v_u_109.AutomaticSize = Enum.AutomaticSize.X
v_u_109.Name = "ChipBar"
v_u_109.BackgroundTransparency = 1
v_u_109.Parent = v_u_108
local v_u_110 = Instance.new("UIListLayout")
v_u_110.FillDirection = Enum.FillDirection.Horizontal
v_u_110.Padding = UDim.new(0, 10)
v_u_110.HorizontalAlignment = Enum.HorizontalAlignment.Center
v_u_110.Parent = v_u_109
local function v117(p111) -- name: chip
    -- upvalues: (copy) v_u_109, (copy) v_u_7
    local v112 = Instance.new("TextLabel")
    v112.Size = UDim2.fromOffset(170, 40)
    v112.BackgroundColor3 = Color3.fromRGB(45, 45, 45)
    v112.TextColor3 = Color3.new(1, 1, 1)
    v112.Font = Enum.Font.Arcade
    v112.TextSize = 20
    v112.Text = p111
    local v113 = Instance.new("UICorner")
    v113.CornerRadius = UDim.new(0, 8)
    v113.Parent = v112
    local v114 = Instance.new("UIStroke")
    v114.Thickness = 3
    v114.Color = Color3.fromRGB(20, 20, 20)
    v114.ApplyStrokeMode = Enum.ApplyStrokeMode.Border
    v114.Parent = v112
    local v115 = Instance.new("Frame")
    v115.Name = "Cooldown"
    v115.BackgroundColor3 = Color3.new(0, 0, 0)
    v115.BackgroundTransparency = 0.45
    v115.BorderSizePixel = 0
    v115.Size = UDim2.fromScale(0, 1)
    local v116 = Instance.new("UICorner")
    v116.CornerRadius = UDim.new(0, 8)
    v116.Parent = v115
    v115.Parent = v112
    v112.Parent = v_u_109
    v_u_7.watchText(v112)
    return v112
end
local v_u_118 = {}
local function v_u_123(p119, p120) -- name: fitSize
    -- upvalues: (copy) v_u_118
    local v121 = p119 .. "|" .. p120
    if v_u_118[v121] then
        return v_u_118[v121]
    end
    local v122 = game:GetService("TextService")
    while p120 > 9 and v122:GetTextSize(p119, p120, Enum.Font.Arcade, Vector2.new(10000, 100)).X > 158 do
        p120 = p120 - 1
    end
    v_u_118[v121] = p120
    return p120
end
local v_u_124 = v117("[E] Shockwave")
local v_u_125 = v117("USES 3/3")
v_u_125.LayoutOrder = 3
v_u_125.TextColor3 = Color3.fromRGB(215, 170, 255)
v_u_5.RenderStepped:Connect(function()
    -- upvalues: (copy) v_u_6, (copy) v_u_11, (copy) v_u_125
    local v126 = v_u_6.Get(v_u_11:GetAttribute("EquippedAttack"))
    local v127 = v_u_125
    local v128
    if v126 == nil then
        v128 = false
    else
        v128 = v126.Uses ~= nil
    end
    v127.Visible = v128
    if v_u_125.Visible then
        local v129 = v_u_11:GetAttribute("WeaponUses") or v126.Uses
        v_u_125.Text = string.format("USES %d/%d", v129, v126.Uses)
        v_u_125.Cooldown.Size = UDim2.fromScale(1 - v129 / v126.Uses, 1)
    end
end)
v_u_5.RenderStepped:Connect(function()
    -- upvalues: (copy) v_u_6, (copy) v_u_11, (copy) v_u_124, (copy) v_u_123
    local v130 = v_u_6.Get(v_u_11:GetAttribute("EquippedAttack"))
    local v131 = v_u_124
    local v132
    if v130 == nil or v130.Ability == nil then
        v132 = false
    else
        local v133 = v130.Ability
        local v134 = v133 ~= nil and v133.HideWhileLocked
        if v134 then
            if v133.MinPhase == nil then
                v134 = false
            else
                v134 = (v_u_11:GetAttribute("AttackPhase") or 1) < v133.MinPhase
            end
        end
        v132 = not v134
    end
    v131.Visible = v132
    if v_u_124.Visible then
        local v135 = (v_u_11:GetAttribute("AbilityReadyAt") or 0) - workspace:GetServerTimeNow()
        local v136 = v_u_124.Cooldown
        local v137 = UDim2.fromScale
        local v138 = v135 / (v_u_11:GetAttribute("AbilityCooldown") or v130.Ability.Cooldown)
        v136.Size = v137(math.clamp(v138, 0, 1), 1)
        local v139 = (v_u_11:GetAttribute("EnragedUntil") or 0) - workspace:GetServerTimeNow()
        if v_u_11:GetAttribute("Enraged") and v139 > 0 then
            v_u_124.Text = string.format("RAGE! %.0fs", v139)
        elseif (v_u_11:GetAttribute("CounterUntil") or 0) > workspace:GetServerTimeNow() then
            v_u_124.Text = "COUNTER!"
        else
            v_u_124.Text = v135 > 0 and string.format("[E] %.1fs", v135) or "[E] " .. (v_u_11:GetAttribute("AbilityLabel") or (v130.Ability.Label or v130.Name))
        end
        v_u_124.TextSize = #v_u_124.Text > 15 and v_u_123(v_u_124.Text, 16) or 20
    end
end)
local v_u_140 = Instance.new("TextLabel")
v_u_140.Name = "SafeZoneLabel"
v_u_140.AnchorPoint = Vector2.new(0.5, 1)
v_u_140.Position = UDim2.new(0.5, 0, 1, -140)
v_u_140.Size = UDim2.fromOffset(420, 34)
v_u_140.BackgroundColor3 = Color3.fromRGB(30, 60, 35)
v_u_140.BackgroundTransparency = 0.15
v_u_140.TextColor3 = Color3.fromRGB(120, 255, 120)
v_u_140.Font = Enum.Font.Arcade
v_u_140.TextSize = 20
v_u_140.Text = "SAFE ZONE - jump down to fight!"
local v141 = Instance.new("UICorner")
v141.CornerRadius = UDim.new(0, 8)
v141.Parent = v_u_140
local v142 = Instance.new("UIStroke")
v142.Thickness = 3
v142.Color = Color3.fromRGB(15, 30, 15)
v142.ApplyStrokeMode = Enum.ApplyStrokeMode.Border
v142.Parent = v_u_140
local v_u_143 = Instance.new("UIScale")
v_u_143.Parent = v_u_140
v_u_140.Parent = v_u_108
v_u_11:GetAttributeChangedSignal("InFFA"):Connect(function() -- name: refreshSafe
    -- upvalues: (copy) v_u_140, (copy) v_u_11
    v_u_140.Visible = v_u_11:GetAttribute("InFFA") ~= true
end)
v_u_140.Visible = v_u_11:GetAttribute("InFFA") ~= true
local function v_u_144() -- name: nope
    -- upvalues: (copy) v_u_140, (copy) v_u_143, (copy) v_u_4
    if v_u_140.Visible then
        v_u_143.Scale = 1.15
        v_u_4:Create(v_u_143, TweenInfo.new(0.2, Enum.EasingStyle.Back), {
            ["Scale"] = 1
        }):Play()
    end
end
local function v_u_146(p145) -- name: hookNope
    -- upvalues: (copy) v_u_144
    if p145:IsA("Tool") then
        p145.Activated:Connect(v_u_144)
    end
end
for _, v147 in v_u_11:WaitForChild("Backpack"):GetChildren() do
    if not v147:IsA("Tool") then
        continue
    end
    v147.Activated:Connect(v_u_144)
end
v_u_11.Backpack.ChildAdded:Connect(v_u_146)
v_u_11.CharacterAdded:Connect(function()
    -- upvalues: (copy) v_u_11, (copy) v_u_144, (copy) v_u_146
    local v148 = v_u_11:WaitForChild("Backpack")
    for _, v149 in v148:GetChildren() do
        if not v149:IsA("Tool") then
            continue
        end
        v149.Activated:Connect(v_u_144)
    end
    v148.ChildAdded:Connect(v_u_146)
end)
v_u_7.onInput(function(p150, p151)
    -- upvalues: (copy) v_u_144
    if not p151 and p150.KeyCode == Enum.KeyCode.E then
        v_u_144()
    end
end)
local v_u_152 = v117("[Q] Brain 0/2")
v_u_152.LayoutOrder = 5
local v_u_153 = Instance.new("ColorCorrectionEffect")
v_u_153.Name = "MaxRageTint"
v_u_153.Parent = game:GetService("Lighting")
local v_u_154 = Color3.fromRGB(255, 215, 160)
local v_u_155 = 0
local function v_u_158(p156) -- name: setTint
    -- upvalues: (ref) v_u_155, (copy) v_u_153, (copy) v_u_154
    v_u_155 = v_u_155 + (p156 - v_u_155) * 0.08
    local v157 = p156 - v_u_155
    if math.abs(v157) < 0.002 then
        v_u_155 = p156
    end
    v_u_153.TintColor = Color3.new(1, 1, 1):Lerp(v_u_154, v_u_155)
    v_u_153.Saturation = v_u_155 * 0.15
    v_u_153.Contrast = v_u_155 * 0.05
end
local v_u_159 = Instance.new("Frame")
v_u_159.Name = "RageMeterBar"
v_u_159.AnchorPoint = Vector2.new(0.5, 1)
v_u_159.Position = UDim2.new(0.5, 0, 1, -182)
v_u_159.Size = UDim2.fromOffset(360, 30)
v_u_159.BackgroundColor3 = Color3.fromRGB(35, 30, 20)
local v160 = Instance.new("UICorner")
v160.CornerRadius = UDim.new(0, 8)
v160.Parent = v_u_159
local v161 = Instance.new("UIStroke")
v161.Thickness = 3
v161.Color = Color3.fromRGB(20, 15, 5)
v161.ApplyStrokeMode = Enum.ApplyStrokeMode.Border
v161.Parent = v_u_159
local v_u_162 = Instance.new("Frame")
v_u_162.Name = "Fill"
v_u_162.BorderSizePixel = 0
v_u_162.Size = UDim2.fromScale(0, 1)
v_u_162.BackgroundColor3 = Color3.fromRGB(255, 200, 40)
local v163 = Instance.new("UICorner")
v163.CornerRadius = UDim.new(0, 8)
v163.Parent = v_u_162
local v_u_164 = Instance.new("UIGradient")
v_u_164.Color = ColorSequence.new(Color3.fromRGB(255, 120, 30), Color3.fromRGB(255, 235, 90))
v_u_164.Parent = v_u_162
v_u_162.Parent = v_u_159
local v_u_165 = Instance.new("TextLabel")
v_u_165.BackgroundTransparency = 1
v_u_165.Size = UDim2.fromScale(1, 1)
v_u_165.ZIndex = 3
v_u_165.Font = Enum.Font.Arcade
v_u_165.TextSize = 18
v_u_165.TextColor3 = Color3.new(1, 1, 1)
v_u_165.Text = "RAGE 0%"
local v166 = Instance.new("UIStroke")
v166.Thickness = 1.5
v166.Parent = v_u_165
v_u_165.Parent = v_u_159
v_u_159.Parent = v_u_108
v_u_7.onInput(function(p167, p168)
    -- upvalues: (copy) v_u_6, (copy) v_u_11, (copy) v_u_17, (copy) v_u_23, (ref) v_u_86, (copy) v_u_10
    if p168 or p167.KeyCode ~= Enum.KeyCode.Q then
        return
    else
        local v169 = v_u_6.Get(v_u_11:GetAttribute("EquippedAttack"))
        if v169 and v169.Ability2 then
            local v170 = v169.Ability2
            local v171
            if v170 == nil or v170.MinLevel == nil then
                v171 = false
            else
                local v172 = v_u_11.Character
                v171 = (v172 and (v172:GetAttribute(v170.LevelAttr or "Level") or 1) or 1) < v170.MinLevel
            end
            if not v171 then
                local v173 = v170.Aim == "Look" and v_u_17 or v_u_23
                if v170.AimWhileCharging and (v170.Charge and (v_u_11:GetAttribute("InFFA") and (v_u_11:GetAttribute("Ability2ReadyAt") or 0) <= workspace:GetServerTimeNow())) then
                    v_u_86(v170, v173)
                end
                v_u_10.Ability2:FireServer(v173())
            end
        else
            local v174 = v169 and v169.Phases
            if v174 then
                v174 = v169.Phases[v_u_11:GetAttribute("VerityPhase") or 1]
            end
            if v174 and v174.Brain then
                v_u_10.Ability2:FireServer()
            end
            return
        end
    end
end)
local v_u_175 = v117("[Q] Explode")
v_u_175.LayoutOrder = 4
v_u_5.RenderStepped:Connect(function()
    -- upvalues: (copy) v_u_6, (copy) v_u_11, (copy) v_u_175, (copy) v_u_123
    local v176 = v_u_6.Get(v_u_11:GetAttribute("EquippedAttack"))
    if v176 then
        v176 = v176.Ability2
    end
    local v177 = v_u_175
    local v178
    if v176 == nil then
        v178 = false
    else
        local v179 = v176 ~= nil and v176.HideWhileLocked
        if v179 then
            if v176.MinPhase == nil then
                v179 = false
            else
                v179 = (v_u_11:GetAttribute("AttackPhase") or 1) < v176.MinPhase
            end
        end
        v178 = not v179
    end
    v177.Visible = v178
    if v_u_175.Visible then
        local v180 = workspace:GetServerTimeNow()
        local v181 = (v_u_11:GetAttribute("ExplodeUntil") or 0) - v180
        if v181 > 0 then
            v_u_175.Text = string.format("BOOM IN %.1f", v181)
            v_u_175.TextColor3 = Color3.fromRGB(255, 90, 60)
            v_u_175.Cooldown.Size = UDim2.fromScale(0, 1)
        else
            local v182
            if v176 == nil or v176.MinLevel == nil then
                v182 = false
            else
                local v183 = v_u_11.Character
                v182 = (v183 and (v183:GetAttribute(v176.LevelAttr or "Level") or 1) or 1) < v176.MinLevel
            end
            if v182 then
                v_u_175.Text = string.format("[Q] LV %d", v176.MinLevel)
                v_u_175.TextColor3 = Color3.fromRGB(150, 150, 150)
                v_u_175.Cooldown.Size = UDim2.fromScale(1, 1)
            else
                local v184 = (v_u_11:GetAttribute("Ability2ReadyAt") or 0) - v180
                v_u_175.TextColor3 = Color3.new(1, 1, 1)
                local v185 = v_u_175.Cooldown
                local v186 = UDim2.fromScale
                local v187 = v184 / (v_u_11:GetAttribute("Ability2Cooldown") or v176.Cooldown)
                v185.Size = v186(math.clamp(v187, 0, 1), 1)
                v_u_175.Text = v184 > 0 and string.format("[Q] %.1fs", v184) or "[Q] " .. (v_u_11:GetAttribute("Ability2Label") or (v176.Label or "Ability"))
            end
        end
        v_u_175.TextSize = #v_u_175.Text > 15 and v_u_123(v_u_175.Text, 16) or 20
    end
end)
local v_u_188 = v_u_10:WaitForChild("Ability3", 10)
v_u_7.onInput(function(p189, p190)
    -- upvalues: (ref) v_u_188, (copy) v_u_10, (copy) v_u_6, (copy) v_u_11, (copy) v_u_144, (ref) v_u_86, (copy) v_u_17, (copy) v_u_23, (copy) v_u_46
    if p190 or p189.UserInputType ~= Enum.UserInputType.Keyboard then
        return
    else
        v_u_188 = v_u_188 or v_u_10:FindFirstChild("Ability3")
        if v_u_188 then
            local v191 = v_u_6.Get(v_u_11:GetAttribute("EquippedAttack"))
            if v191 then
                v191 = v191.Ability3
            end
            if v191 and p189.KeyCode.Name == (v191.Key or "V") then
                if v_u_11:GetAttribute("InFFA") then
                    if v191.MinPhase and (v_u_11:GetAttribute("AttackPhase") or 1) < v191.MinPhase then
                        return
                    else
                        local v192
                        if v191 == nil or v191.MinLevel == nil then
                            v192 = false
                        else
                            local v193 = v_u_11.Character
                            v192 = (v193 and (v193:GetAttribute(v191.LevelAttr or "Level") or 1) or 1) < v191.MinLevel
                        end
                        if not v192 then
                            if v191.AimWhileCharging and (v191.Charge and (v_u_11:GetAttribute("Ability3ReadyAt") or 0) <= workspace:GetServerTimeNow()) then
                                v_u_86(v191, v191.Aim == "Look" and v_u_17 or v_u_23)
                            end
                            v_u_188:FireServer(v191.Aim == "Mouse" and v_u_46() or v_u_23())
                        end
                    end
                else
                    v_u_144()
                    return
                end
            else
                return
            end
        else
            return
        end
    end
end)
task.spawn(function()
    -- upvalues: (copy) v_u_10, (copy) v_u_46
    local v194 = v_u_10:WaitForChild("SukunaFX", 30)
    if v194 then
        v194.OnClientEvent:Connect(function(p195, p196)
            -- upvalues: (ref) v_u_10, (ref) v_u_46
            if p195 == "Steer" then
                local v_u_197 = os.clock() + (tonumber(p196) or 3)
                task.spawn(function()
                    -- upvalues: (copy) v_u_197, (ref) v_u_10, (ref) v_u_46
                    while os.clock() < v_u_197 do
                        v_u_10.Aim:FireServer((v_u_46()))
                        task.wait(0.05)
                    end
                end)
            end
        end)
    end
end)
local v_u_198 = v117("[V] Ability")
v_u_198.LayoutOrder = 5
local v_u_199 = v_u_10:WaitForChild("Ability4", 10)
v_u_7.onInput(function(p200, p201)
    -- upvalues: (ref) v_u_199, (copy) v_u_10, (copy) v_u_6, (copy) v_u_11, (copy) v_u_144, (copy) v_u_17
    if p201 or p200.UserInputType ~= Enum.UserInputType.Keyboard then
        return
    else
        v_u_199 = v_u_199 or v_u_10:FindFirstChild("Ability4")
        if v_u_199 then
            local v202 = v_u_6.Get(v_u_11:GetAttribute("EquippedAttack"))
            if v202 then
                v202 = v202.Ability4
            end
            if v202 and p200.KeyCode.Name == (v202.Key or "X") then
                if p200.KeyCode == Enum.KeyCode.X and v_u_11:GetAttribute("RollConfirmOpen") then
                    return
                elseif v_u_11:GetAttribute("InFFA") then
                    if v202.MinPhase and (v_u_11:GetAttribute("AttackPhase") or 1) < v202.MinPhase then
                        return
                    else
                        local v203
                        if v202 == nil or v202.MinLevel == nil then
                            v203 = false
                        else
                            local v204 = v_u_11.Character
                            v203 = (v204 and (v204:GetAttribute(v202.LevelAttr or "Level") or 1) or 1) < v202.MinLevel
                        end
                        if not v203 then
                            v_u_199:FireServer((v_u_17()))
                        end
                    end
                else
                    v_u_144()
                    return
                end
            else
                return
            end
        else
            return
        end
    end
end)
local v_u_205 = v117("[X] Ability")
v_u_205.LayoutOrder = 6
local v_u_206 = v_u_10:WaitForChild("Ability5", 10)
v_u_7.onInput(function(p207, p208)
    -- upvalues: (ref) v_u_206, (copy) v_u_10, (copy) v_u_6, (copy) v_u_11, (copy) v_u_144, (copy) v_u_46, (copy) v_u_17
    if p208 or p207.UserInputType ~= Enum.UserInputType.Keyboard then
        return
    else
        v_u_206 = v_u_206 or v_u_10:FindFirstChild("Ability5")
        if v_u_206 then
            local v209 = v_u_6.Get(v_u_11:GetAttribute("EquippedAttack"))
            if v209 then
                v209 = v209.Ability5
            end
            if v209 and p207.KeyCode.Name == (v209.Key or "C") then
                if v_u_11:GetAttribute("InFFA") then
                    if v209.MinPhase and (v_u_11:GetAttribute("AttackPhase") or 1) < v209.MinPhase then
                        return
                    else
                        local v210
                        if v209 == nil or v209.MinLevel == nil then
                            v210 = false
                        else
                            local v211 = v_u_11.Character
                            v210 = (v211 and (v211:GetAttribute(v209.LevelAttr or "Level") or 1) or 1) < v209.MinLevel
                        end
                        if not v210 then
                            v_u_206:FireServer(v209.Aim == "Mouse" and v_u_46() or v_u_17())
                        end
                    end
                else
                    v_u_144()
                    return
                end
            else
                return
            end
        else
            return
        end
    end
end)
local v_u_212 = v117("[C] Ability")
v_u_212.LayoutOrder = 7
local v_u_213 = v_u_10:WaitForChild("Ability6", 10)
v_u_7.onInput(function(p214, p215)
    -- upvalues: (ref) v_u_213, (copy) v_u_10, (copy) v_u_6, (copy) v_u_11, (copy) v_u_144, (copy) v_u_46, (copy) v_u_17
    if p215 or p214.UserInputType ~= Enum.UserInputType.Keyboard then
        return
    else
        v_u_213 = v_u_213 or v_u_10:FindFirstChild("Ability6")
        if v_u_213 then
            local v216 = v_u_6.Get(v_u_11:GetAttribute("EquippedAttack"))
            if v216 then
                v216 = v216.Ability6
            end
            if v216 and p214.KeyCode.Name == (v216.Key or "Z") then
                if p214.KeyCode == Enum.KeyCode.X and v_u_11:GetAttribute("RollConfirmOpen") then
                    return
                elseif v_u_11:GetAttribute("InFFA") then
                    if v216.MinPhase and (v_u_11:GetAttribute("AttackPhase") or 1) < v216.MinPhase then
                        return
                    else
                        local v217
                        if v216 == nil or v216.MinLevel == nil then
                            v217 = false
                        else
                            local v218 = v_u_11.Character
                            v217 = (v218 and (v218:GetAttribute(v216.LevelAttr or "Level") or 1) or 1) < v216.MinLevel
                        end
                        if not v217 then
                            v_u_213:FireServer(v216.Aim == "Mouse" and v_u_46() or v_u_17())
                        end
                    end
                else
                    v_u_144()
                    return
                end
            else
                return
            end
        else
            return
        end
    end
end)
local v_u_219 = v117("[Z] Ability")
v_u_219.LayoutOrder = 8
local v_u_220 = v_u_10:WaitForChild("Ability7", 10)
v_u_7.onInput(function(p221, p222)
    -- upvalues: (ref) v_u_220, (copy) v_u_10, (copy) v_u_6, (copy) v_u_11, (copy) v_u_144, (copy) v_u_46, (copy) v_u_17
    if p222 or p221.UserInputType ~= Enum.UserInputType.Keyboard then
        return
    else
        v_u_220 = v_u_220 or v_u_10:FindFirstChild("Ability7")
        if v_u_220 then
            local v223 = v_u_6.Get(v_u_11:GetAttribute("EquippedAttack"))
            if v223 then
                v223 = v223.Ability7
            end
            if v223 and p221.KeyCode.Name == (v223.Key or "B") then
                if p221.KeyCode == Enum.KeyCode.X and v_u_11:GetAttribute("RollConfirmOpen") then
                    return
                elseif v_u_11:GetAttribute("InFFA") then
                    if v223.MinPhase and (v_u_11:GetAttribute("AttackPhase") or 1) < v223.MinPhase then
                        return
                    else
                        local v224
                        if v223 == nil or v223.MinLevel == nil then
                            v224 = false
                        else
                            local v225 = v_u_11.Character
                            v224 = (v225 and (v225:GetAttribute(v223.LevelAttr or "Level") or 1) or 1) < v223.MinLevel
                        end
                        if not v224 then
                            v_u_220:FireServer(v223.Aim == "Mouse" and v_u_46() or v_u_17())
                        end
                    end
                else
                    v_u_144()
                    return
                end
            else
                return
            end
        else
            return
        end
    end
end)
local v_u_226 = v117("[B] Ability")
v_u_226.LayoutOrder = 9
local v_u_227 = v117("[M1] Ability")
v_u_227.LayoutOrder = -1
v_u_5.RenderStepped:Connect(function()
    -- upvalues: (copy) v_u_6, (copy) v_u_11, (copy) v_u_227
    local v228 = v_u_6.Get(v_u_11:GetAttribute("EquippedAttack"))
    local v229 = v228 and v228.HitCooldown and ((v_u_11:GetAttribute("AttackReadyAt") or 0) - workspace:GetServerTimeNow() or 0) or 0
    v_u_227.Visible = v229 > 0.6
    if v_u_227.Visible then
        local v230 = v_u_227.Cooldown
        local v231 = UDim2.fromScale
        local v232 = v229 / (v_u_11:GetAttribute("AttackCooldown") or v228.HitCooldown)
        v230.Size = v231(math.clamp(v232, 0, 1), 1)
        v_u_227.Text = string.format("[M1] %.1fs", v229)
        v_u_227.TextColor3 = Color3.new(1, 1, 1)
        v_u_227.TextSize = 20
    end
end)
local function v_u_263(p233, p234, p235, p236, p237) -- name: extraChip
    -- upvalues: (copy) v_u_11, (copy) v_u_123
    local v238
    if p234 == nil then
        v238 = false
    else
        local v239 = p234 ~= nil and p234.HideWhileLocked
        if v239 then
            if p234.MinPhase == nil then
                v239 = false
            else
                v239 = (v_u_11:GetAttribute("AttackPhase") or 1) < p234.MinPhase
            end
        end
        v238 = not v239
    end
    p233.Visible = v238
    if not p233.Visible then
        return
    end
    local v240 = p233:GetAttribute("BaseOrder") or p233.LayoutOrder
    p233:SetAttribute("BaseOrder", v240)
    local v241 = p234.ChipOrder or v240
    if p233.LayoutOrder ~= v241 then
        p233.LayoutOrder = v241
    end
    local v242 = p234.Key or p237
    local v243 = nil
    local v244 = p234.Mode and (p234.Mode.Label and (v_u_11:GetAttribute("AttackPhase") or 1) < 2) and p234.Mode.Label or p234.Label
    local v245 = v_u_11:GetAttribute(string.gsub(p235, "ReadyAt$", "") .. "Label") or v244
    local v246 = p234.ArmedAttr
    if v246 then
        v246 = (v_u_11:GetAttribute(p234.ArmedAttr) or 0) - workspace:GetServerTimeNow()
    end
    local v247 = p234.ActiveAttr
    if v247 then
        v247 = (v_u_11:GetAttribute(p234.ActiveAttr) or 0) - workspace:GetServerTimeNow()
    end
    local v248, v249
    if p234.MinPhase and (v_u_11:GetAttribute("AttackPhase") or 1) < p234.MinPhase then
        v248 = p234.LockedLabel and string.format("[%s] %s", v242, p234.LockedLabel) or string.format("[%s] PHASE %d", v242, p234.MinPhase)
        v243 = Color3.fromRGB(150, 150, 150)
        v249 = 1
        p233.Cooldown.Size = UDim2.fromScale(v249, 1)
        p233.TextColor3 = v243 or Color3.new(1, 1, 1)
        p233.Text = v248
        p233.TextSize = #v248 > 15 and v_u_123(v248, 16) or 20
        return
    else
        local v250
        if p234 == nil or p234.MinLevel == nil then
            v250 = false
        else
            local v251 = v_u_11.Character
            v250 = (v251 and (v251:GetAttribute(p234.LevelAttr or "Level") or 1) or 1) < p234.MinLevel
        end
        if v250 then
            v248 = string.format("[%s] LV %d", v242, p234.MinLevel)
            v243 = Color3.fromRGB(150, 150, 150)
            v249 = 1
            p233.Cooldown.Size = UDim2.fromScale(v249, 1)
            p233.TextColor3 = v243 or Color3.new(1, 1, 1)
            p233.Text = v248
            p233.TextSize = #v248 > 15 and v_u_123(v248, 16) or 20
            return
        elseif v247 and v247 > 0 then
            local v252, v253
            if p234.ActiveLenAttr then
                local v254 = v_u_11
                local v255 = p234.ActiveLenAttr
                v252 = tonumber(v254:GetAttribute(v255))
                if v252 then
                    v248 = string.format("[%s] %s %ds", v242, p234.ActiveLabel or "ACTIVE", (math.ceil(v247)))
                    v253 = v247 / math.max(v252, 0.1)
                    v249 = 1 - math.clamp(v253, 0, 1)
                    v243 = p234.ActiveRainbow and Color3.fromHSV(os.clock() * 0.5 % 1, 0.45, 1) or Color3.fromRGB(255, 220, 120)
                else
                    v252 = p234.Duration or 30
                    v248 = string.format("[%s] %s %ds", v242, p234.ActiveLabel or "ACTIVE", (math.ceil(v247)))
                    v253 = v247 / math.max(v252, 0.1)
                    v249 = 1 - math.clamp(v253, 0, 1)
                    v243 = p234.ActiveRainbow and Color3.fromHSV(os.clock() * 0.5 % 1, 0.45, 1) or Color3.fromRGB(255, 220, 120)
                end
            else
                v252 = p234.Duration or 30
                v248 = string.format("[%s] %s %ds", v242, p234.ActiveLabel or "ACTIVE", (math.ceil(v247)))
                v253 = v247 / math.max(v252, 0.1)
                v249 = 1 - math.clamp(v253, 0, 1)
                v243 = p234.ActiveRainbow and Color3.fromHSV(os.clock() * 0.5 % 1, 0.45, 1) or Color3.fromRGB(255, 220, 120)
            end
            p233.Cooldown.Size = UDim2.fromScale(v249, 1)
            p233.TextColor3 = v243 or Color3.new(1, 1, 1)
            p233.Text = v248
            p233.TextSize = #v248 > 15 and v_u_123(v248, 16) or 20
        else
            if v246 and v246 > 0 then
                v248 = string.format("[%s] %s", v242, p234.ArmedLabel or "AGAIN!")
                local v256 = v246 / (p234.Window or 5)
                v249 = 1 - math.clamp(v256, 0, 1)
                v243 = Color3.fromRGB(120, 255, 240)
            else
                local v257 = (v_u_11:GetAttribute(p235) or 0) - workspace:GetServerTimeNow()
                local v258 = p234.Meter
                local v259 = v258 and (v_u_11:GetAttribute(p234.Meter) or 0) or v258
                if v259 and v259 < 1 then
                    local v260 = string.format
                    local v261 = v259 * 100
                    v248 = v260("[%s] %d%%", v242, (math.floor(v261)))
                    v249 = 1 - v259
                else
                    local v262 = v257 / (v_u_11:GetAttribute(p236) or p234.Cooldown)
                    v249 = math.clamp(v262, 0, 1)
                    v248 = v257 > 0 and string.format("[%s] %.1fs", v242, v257) or "[" .. v242 .. "] " .. (v245 or "Ability")
                end
            end
            p233.Cooldown.Size = UDim2.fromScale(v249, 1)
            p233.TextColor3 = v243 or Color3.new(1, 1, 1)
            p233.Text = v248
            p233.TextSize = #v248 > 15 and v_u_123(v248, 16) or 20
        end
    end
end
v_u_5.RenderStepped:Connect(function()
    -- upvalues: (copy) v_u_6, (copy) v_u_11, (copy) v_u_110, (copy) v_u_263, (copy) v_u_198, (copy) v_u_205, (copy) v_u_212, (copy) v_u_219, (copy) v_u_226
    local v264 = v_u_6.Get(v_u_11:GetAttribute("EquippedAttack"))
    v_u_110.SortOrder = v264 and v264.ChipSort and Enum.SortOrder.LayoutOrder or Enum.SortOrder.Name
    local v265 = v_u_263
    local v266 = v_u_198
    local v267
    if v264 then
        v267 = v264.Ability3
    else
        v267 = v264
    end
    v265(v266, v267, "Ability3ReadyAt", "Ability3Cooldown", "V")
    local v268 = v_u_263
    local v269 = v_u_205
    local v270
    if v264 then
        v270 = v264.Ability4
    else
        v270 = v264
    end
    v268(v269, v270, "Ability4ReadyAt", "Ability4Cooldown", "X")
    local v271 = v_u_263
    local v272 = v_u_212
    local v273
    if v264 then
        v273 = v264.Ability5
    else
        v273 = v264
    end
    v271(v272, v273, "Ability5ReadyAt", "Ability5Cooldown", "C")
    local v274 = v_u_263
    local v275 = v_u_219
    local v276
    if v264 then
        v276 = v264.Ability6
    else
        v276 = v264
    end
    v274(v275, v276, "Ability6ReadyAt", "Ability6Cooldown", "Z")
    local v277 = v_u_263
    local v278 = v_u_226
    if v264 then
        v264 = v264.Ability7
    end
    v277(v278, v264, "Ability7ReadyAt", "Ability7Cooldown", "B")
end)
v_u_5.RenderStepped:Connect(function()
    -- upvalues: (copy) v_u_6, (copy) v_u_11, (copy) v_u_159, (copy) v_u_162, (copy) v_u_165, (copy) v_u_164, (copy) v_u_152, (copy) v_u_123, (copy) v_u_158
    local v279 = v_u_6.Get(v_u_11:GetAttribute("EquippedAttack"))
    local v280 = v_u_11:GetAttribute("VerityPhase") or 1
    local v281 = v279 and v279.Phases
    if v281 then
        v281 = v279.Phases[v280]
    end
    local v282
    if v279 then
        v282 = v279.RageMeter
    else
        v282 = v279
    end
    v_u_159.Visible = v282 ~= nil
    if v_u_159.Visible then
        local v283 = (v_u_11:GetAttribute("VerityRage") or 0) / (v281 and v281.RageMax or v282.Max)
        local v284 = math.clamp(v283, 0, 1)
        v_u_162.Size = UDim2.fromScale(v284, 1)
        local v285 = v280 == #v279.Phases
        local v286
        if v281 then
            local v287 = (v281.Damage or 0) * (v_u_11:GetAttribute("VerityDmgMult") or 1) + 0.5
            v286 = math.floor(v287)
        else
            v286 = v281
        end
        if v285 and v284 >= 1 then
            v_u_165.Text = string.format("MAX RAGE!  %d DMG", v286)
            v_u_164.Color = ColorSequence.new(Color3.fromRGB(255, 40, 20), Color3.fromRGB(255, 160, 40))
        elseif v285 then
            local v288 = v_u_165
            local v289 = string.format
            local v290 = v284 * 100
            v288.Text = v289("RAGE %d%%  -  %d DMG", math.floor(v290), v286)
            v_u_164.Color = ColorSequence.new(Color3.fromRGB(255, 120, 30), Color3.fromRGB(255, 235, 90))
        else
            local v291 = v_u_165
            local v292 = string.format
            local v293 = v284 * 100
            v291.Text = v292("RAGE %d%%  -  PHASE %d", math.floor(v293), v280)
            v_u_164.Color = ColorSequence.new(Color3.fromRGB(255, 120, 30), Color3.fromRGB(255, 235, 90))
        end
    end
    local v294 = v_u_152
    local v295
    if v281 == nil then
        v295 = false
    else
        v295 = v281.Brain ~= nil
    end
    v294.Visible = v295
    if v_u_152.Visible then
        local v296 = (v_u_11:GetAttribute("BiteReadyAt") or 0) - workspace:GetServerTimeNow()
        local v297 = v_u_11:GetAttribute("VerityBrains") or 0
        local v298 = v_u_152.Cooldown
        local v299 = UDim2.fromScale
        local v300
        if v297 > 0 then
            local v301 = v296 / (v281.Brain.Cooldown or 2.4)
            v300 = math.clamp(v301, 0, 1) or 1
        else
            v300 = 1
        end
        v298.Size = v299(v300, 1)
        v_u_152.Text = string.format("[Q] Brain %d/%d", v297, v281.Brain.Max or 2) .. (v296 > 0 and (string.format(" %ds", (math.ceil(v296))) or "") or "")
        v_u_152.TextSize = v296 > 0 and v_u_123("[Q] Brain 0/2 88s", 16) or 20
    end
    local v302 = 0
    if v_u_159.Visible and (v279 and v280 == #v279.Phases) then
        local v303 = (v_u_11:GetAttribute("VerityRage") or 0) / (v281 and v281.RageMax or v282.Max)
        local v304 = math.clamp(v303, 0, 1)
        if v304 >= 0.9 then
            v302 = (v304 - 0.9) * 0.65 / 0.1 + 0.35
        end
    end
    v_u_158(v302)
end)
local v_u_305 = Instance.new("Frame")
v_u_305.Name = "StreakBar"
v_u_305.AnchorPoint = Vector2.new(0.5, 1)
v_u_305.Position = UDim2.new(0.5, 0, 1, -182)
v_u_305.Size = UDim2.fromOffset(360, 30)
v_u_305.BackgroundColor3 = Color3.fromRGB(18, 35, 20)
v_u_305.Visible = false
local v306 = Instance.new("UICorner")
v306.CornerRadius = UDim.new(0, 8)
v306.Parent = v_u_305
local v_u_307 = Instance.new("UIStroke")
v_u_307.Thickness = 3
v_u_307.Color = Color3.fromRGB(5, 20, 8)
v_u_307.ApplyStrokeMode = Enum.ApplyStrokeMode.Border
v_u_307.Parent = v_u_305
local v_u_308 = Instance.new("Frame")
v_u_308.BorderSizePixel = 0
v_u_308.Size = UDim2.fromScale(0, 1)
v_u_308.BackgroundColor3 = Color3.fromRGB(80, 255, 100)
local v309 = Instance.new("UICorner")
v309.CornerRadius = UDim.new(0, 8)
v309.Parent = v_u_308
local v_u_310 = Instance.new("UIGradient")
v_u_310.Color = ColorSequence.new(Color3.fromRGB(40, 190, 70), Color3.fromRGB(150, 255, 150))
v_u_310.Parent = v_u_308
v_u_308.Parent = v_u_305
local v_u_311 = Instance.new("TextLabel")
v_u_311.BackgroundTransparency = 1
v_u_311.Size = UDim2.fromScale(1, 1)
v_u_311.ZIndex = 3
v_u_311.Font = Enum.Font.Arcade
v_u_311.TextSize = 18
v_u_311.TextColor3 = Color3.new(1, 1, 1)
v_u_311.Text = "STREAK"
local v312 = Instance.new("UIStroke")
v312.Thickness = 1.5
v312.Parent = v_u_311
v_u_311.Parent = v_u_305
v_u_305.Parent = v_u_108
v_u_5.RenderStepped:Connect(function()
    -- upvalues: (copy) v_u_6, (copy) v_u_11, (copy) v_u_305, (copy) v_u_310, (copy) v_u_307, (copy) v_u_308, (copy) v_u_311
    local v313 = v_u_6.Get(v_u_11:GetAttribute("EquippedAttack"))
    if v313 then
        v313 = v313.Streak
    end
    v_u_305.Visible = v313 ~= nil
    if v313 then
        local v314 = v_u_11:GetAttribute("StreakPhase") or 1
        local v315 = v_u_11:GetAttribute("StreakHits") or 0
        local v316 = v313.Phases[v314]
        local v317 = v_u_11:GetAttribute("StreakExpire")
        local v318 = v317 and v317 - workspace:GetServerTimeNow() or nil
        local v319
        if v318 then
            v319 = v318 > 0
        else
            v319 = v318
        end
        local v320 = v319 and (string.format("  (%ds)", (math.ceil(v318))) or "") or ""
        if v319 then
            v319 = v318 <= 3
        end
        v_u_310.Color = v319 and ColorSequence.new(Color3.fromRGB(170, 20, 20), Color3.fromRGB(255, 90, 90)) or ColorSequence.new(Color3.fromRGB(40, 190, 70), Color3.fromRGB(150, 255, 150))
        v_u_307.Color = v319 and Color3.fromRGB(90, 0, 0) or Color3.fromRGB(5, 20, 8)
        if v316 and v316.Hits then
            local v321 = v_u_308
            local v322 = UDim2.fromScale
            local v323 = v315 / v316.Hits
            v321.Size = v322(math.clamp(v323, 0, 1), 1)
            v_u_311.Text = string.format("STREAK PHASE %d  -  %d/%d HITS", v314, v315, v316.Hits) .. v320
        else
            v_u_308.Size = UDim2.fromScale(1, 1)
            v_u_311.Text = "MAX STREAK!  PHASE " .. v314 .. v320
        end
    else
        return
    end
end)
local v_u_324 = Instance.new("Frame")
v_u_324.Name = "MangoMeterBar"
v_u_324.AnchorPoint = Vector2.new(0.5, 1)
v_u_324.Position = UDim2.new(0.5, 0, 1, -182)
v_u_324.Size = UDim2.fromOffset(360, 30)
v_u_324.BackgroundColor3 = Color3.fromRGB(45, 28, 12)
v_u_324.Visible = false
v_u_324:SetAttribute("FitOwnPos", true)
local v325 = Instance.new("UICorner")
v325.CornerRadius = UDim.new(0, 8)
v325.Parent = v_u_324
local v_u_326 = Instance.new("UIStroke")
v_u_326.Thickness = 3
v_u_326.Color = Color3.fromRGB(25, 12, 0)
v_u_326.ApplyStrokeMode = Enum.ApplyStrokeMode.Border
v_u_326.Parent = v_u_324
local v_u_327 = Instance.new("Frame")
v_u_327.BorderSizePixel = 0
v_u_327.Size = UDim2.fromScale(0, 1)
local v328 = Instance.new("UICorner")
v328.CornerRadius = UDim.new(0, 8)
v328.Parent = v_u_327
local v_u_329 = Instance.new("UIGradient")
v_u_329.Rotation = 90
v_u_329.Parent = v_u_327
v_u_327.Parent = v_u_324
local v_u_330 = Instance.new("TextLabel")
v_u_330.BackgroundTransparency = 1
v_u_330.Size = UDim2.fromScale(1, 1)
v_u_330.ZIndex = 3
v_u_330.Font = Enum.Font.Arcade
v_u_330.TextSize = 18
v_u_330.TextColor3 = Color3.new(1, 1, 1)
v_u_330.Text = "MANGO 0%"
local v331 = Instance.new("UIStroke")
v331.Thickness = 1.5
v331.Parent = v_u_330
v_u_330.Parent = v_u_324
local v_u_332 = Instance.new("ImageLabel")
v_u_332.AnchorPoint = Vector2.new(0.5, 0.5)
v_u_332.Position = UDim2.new(0, -4, 0.5, 0)
v_u_332.Size = UDim2.fromOffset(54, 54)
v_u_332.BackgroundTransparency = 1
v_u_332.Image = "rbxassetid://106675753781843"
v_u_332.ZIndex = 4
local v_u_333 = Instance.new("UIScale")
v_u_333.Parent = v_u_332
v_u_332.Parent = v_u_324
v_u_324.Parent = v_u_108
local v_u_334 = {
    { 0, Color3.fromRGB(110, 200, 60) },
    { 0.4, Color3.fromRGB(255, 215, 60) },
    { 0.75, Color3.fromRGB(255, 140, 30) },
    { 1, Color3.fromRGB(235, 45, 30) }
}
local function v_u_339(p335) -- name: ripe
    -- upvalues: (copy) v_u_334
    for v336 = 2, #v_u_334 do
        local v337 = v_u_334[v336 - 1]
        local v338 = v_u_334[v336]
        if p335 > v338[1] then
            continue
        end
        return v337[2]:Lerp(v338[2], (p335 - v337[1]) / (v338[1] - v337[1]))
    end
    return v_u_334[#v_u_334][2]
end
local v_u_340 = 0
local v_u_341 = 0
v_u_5.RenderStepped:Connect(function(p342)
    -- upvalues: (copy) v_u_6, (copy) v_u_11, (copy) v_u_324, (ref) v_u_340, (copy) v_u_339, (copy) v_u_327, (copy) v_u_329, (ref) v_u_341, (copy) v_u_3, (copy) v_u_326, (copy) v_u_333, (copy) v_u_332, (copy) v_u_330
    local v343 = v_u_6.Get(v_u_11:GetAttribute("EquippedAttack"))
    if v343 then
        v343 = v343.MangoMeter
    end
    v_u_324.Visible = v343 ~= nil
    if v343 then
        local v344 = v_u_11:GetAttribute("MangoMeter") or 0
        local v345 = math.clamp(v344, 0, 1)
        local v346 = v_u_340
        local v347 = v345 - v_u_340
        local v348 = p342 * 10
        v_u_340 = v346 + v347 * math.min(1, v348)
        local v349 = v_u_339(v_u_340)
        v_u_327.Size = UDim2.fromScale(v_u_340, 1)
        v_u_327.BackgroundColor3 = v349
        v_u_329.Color = ColorSequence.new(v349:Lerp(Color3.new(1, 1, 1), 0.3), v349)
        local v350 = v_u_341 - p342 * 3
        v_u_341 = math.max(0, v350)
        local v351 = os.clock()
        local v352 = (v_u_340 - 0.8) / 0.2
        local v353 = math.clamp(v352, 0, 1)
        local v354 = v353 * 3 + (v345 >= 1 and 4 or 0)
        v_u_324.Position = UDim2.new(0.5, (math.random() - 0.5) * v354, 1, require(v_u_3.ScreenFit).bottomOffset(-182) + (math.random() - 0.5) * v354)
        local v355
        if v353 > 0 then
            local v356 = v351 * (v353 * 10 + 8)
            v355 = (math.sin(v356) * 0.5 + 0.5) * v353 or 0
        else
            v355 = 0
        end
        v_u_326.Color = Color3.fromRGB(25, 12, 0):Lerp(Color3.fromRGB(255, 40, 30), v355):Lerp(Color3.new(1, 1, 1), v_u_341)
        v_u_333.Scale = v_u_340 * 0.12 + 1 + 0.12 * v355 + v_u_341 * 0.3
        local v357 = v_u_332
        local v358 = v351 * 2
        v357.Rotation = math.sin(v358) * 6 + (math.random() - 0.5) * 10 * v353
        if v345 >= 1 then
            v_u_330.Text = "IT\'S GONNA BURST!!"
            return
        elseif v353 > 0 then
            local v359 = v_u_330
            local v360 = string.format
            local v361 = v345 * 100
            v359.Text = v360("MANGO %d%%  -  KILL SOMEONE!", (math.floor(v361)))
        else
            local v362 = v_u_330
            local v363 = string.format
            local v364 = v345 * 100
            v362.Text = v363("MANGO %d%%", (math.floor(v364)))
        end
    else
        v_u_340 = 0
        return
    end
end)
task.spawn(function()
    -- upvalues: (copy) v_u_10, (ref) v_u_341, (copy) v_u_324, (copy) v_u_4
    local v365 = v_u_10:WaitForChild("MangoFX", 30)
    if v365 then
        v365.OnClientEvent:Connect(function(p366, p367)
            -- upvalues: (ref) v_u_341, (ref) v_u_324, (ref) v_u_4
            if p366 == "Drained" and typeof(p367) == "number" then
                v_u_341 = 1
                local v368 = Instance.new("TextLabel")
                v368.AnchorPoint = Vector2.new(0.5, 1)
                v368.Position = UDim2.new(0.5, 0, 0, -4)
                v368.Size = UDim2.fromOffset(160, 30)
                v368.BackgroundTransparency = 1
                v368.Font = Enum.Font.Arcade
                v368.TextSize = 26
                v368.ZIndex = 5
                v368.TextColor3 = Color3.fromRGB(140, 255, 120)
                local v369 = string.format
                local v370 = p367 * 100 + 0.5
                v368.Text = v369("-%d%%", (math.floor(v370)))
                local v371 = Instance.new("UIStroke")
                v371.Thickness = 2
                v371.Parent = v368
                v368.Parent = v_u_324
                v_u_4:Create(v368, TweenInfo.new(0.9, Enum.EasingStyle.Quad, Enum.EasingDirection.Out), {
                    ["Position"] = nil,
                    ["TextTransparency"] = 1,
                    ["Position"] = UDim2.new(0.5, 0, 0, -42)
                }):Play()
                v_u_4:Create(v371, TweenInfo.new(0.9), {
                    ["Transparency"] = 1
                }):Play()
                game:GetService("Debris"):AddItem(v368, 1)
            end
        end)
    end
end)
local v_u_372 = Instance.new("TextLabel")
v_u_372.AnchorPoint = Vector2.new(0.5, 1)
v_u_372.Position = UDim2.new(0.5, 0, 1, -140)
v_u_372.Size = UDim2.fromOffset(360, 28)
v_u_372.BackgroundTransparency = 1
v_u_372.Font = Enum.Font.Arcade
v_u_372.TextSize = 20
v_u_372.TextColor3 = Color3.fromRGB(255, 120, 110)
v_u_372.Text = ""
v_u_372.TextTransparency = 1
local v_u_373 = Instance.new("UIStroke")
v_u_373.Thickness = 2
v_u_373.Parent = v_u_372
v_u_372.Parent = v_u_108
local v_u_374 = 0
local function v_u_387(p375, p376, p377, p378) -- name: bigBanner
    -- upvalues: (copy) v_u_108, (copy) v_u_7, (copy) v_u_4
    local v_u_379 = Instance.new("Frame")
    v_u_379.AnchorPoint = Vector2.new(0.5, 0.5)
    v_u_379.Position = UDim2.fromScale(0.5, 0.3)
    v_u_379.Size = UDim2.fromOffset(640, 110)
    v_u_379.BackgroundTransparency = 1
    v_u_379.Parent = v_u_108
    local v_u_380 = Instance.new("TextLabel")
    v_u_380.BackgroundTransparency = 1
    v_u_380.Size = UDim2.new(1, 0, 0.6, 0)
    v_u_380.Font = Enum.Font.Arcade
    v_u_380.TextScaled = true
    v_u_380.Text = p375
    v_u_380.TextColor3 = p377
    v_u_380.Parent = v_u_379
    local v_u_381 = Instance.new("UIStroke")
    v_u_381.Thickness = 3
    v_u_381.Color = Color3.fromRGB(15, 10, 25)
    v_u_381.Parent = v_u_380
    local v_u_382 = Instance.new("TextLabel")
    v_u_382.BackgroundTransparency = 1
    v_u_382.Position = UDim2.fromScale(0, 0.62)
    v_u_382.Size = UDim2.new(1, 0, 0.32, 0)
    v_u_382.Font = Enum.Font.Arcade
    v_u_382.TextScaled = true
    v_u_382.Text = v_u_7.text(p376)
    v_u_382.TextColor3 = Color3.new(1, 1, 1)
    v_u_382.Parent = v_u_379
    local v_u_383 = Instance.new("UIStroke")
    v_u_383.Thickness = 2
    v_u_383.Color = Color3.fromRGB(15, 10, 25)
    v_u_383.Parent = v_u_382
    require(game:GetService("ReplicatedStorage"):WaitForChild("ScreenFit")).title(v_u_379)
    local v384 = Instance.new("UIScale")
    v384.Scale = 0.4
    v384.Parent = v_u_379
    v_u_4:Create(v384, TweenInfo.new(0.35, Enum.EasingStyle.Back, Enum.EasingDirection.Out), {
        ["Scale"] = 1
    }):Play()
    task.delay(p378 or 2.5, function()
        -- upvalues: (copy) v_u_380, (copy) v_u_382, (ref) v_u_4, (copy) v_u_381, (copy) v_u_383, (copy) v_u_379
        for _, v385 in { v_u_380, v_u_382 } do
            v_u_4:Create(v385, TweenInfo.new(0.5), {
                ["TextTransparency"] = 1
            }):Play()
        end
        for _, v386 in { v_u_381, v_u_383 } do
            v_u_4:Create(v386, TweenInfo.new(0.5), {
                ["Transparency"] = 1
            }):Play()
        end
        task.delay(0.6, function()
            -- upvalues: (ref) v_u_379
            v_u_379:Destroy()
        end)
    end)
end
local v_u_388 = v_u_6.Get("Thor Hammer")
local v_u_389
if v_u_388 then
    v_u_389 = v_u_388.Hammer
else
    v_u_389 = v_u_388
end
local v_u_390
if v_u_389 then
    v_u_390 = v_u_10:WaitForChild("ThorAbility", 10)
else
    v_u_390 = v_u_389
end
local v391
if v_u_389 then
    v391 = v_u_10:WaitForChild("ThorFX", 10)
else
    v391 = v_u_389
end
local v_u_392 = Color3.fromRGB(110, 195, 255)
local v_u_393 = v117("[CLICK] THROW")
v_u_393.LayoutOrder = 6
v_u_393.Visible = false
local v_u_394 = v117("[E] STOMP 0/4")
v_u_394.LayoutOrder = 7
v_u_394.Size = UDim2.fromOffset(230, 40)
v_u_394.Visible = false
v_u_5.RenderStepped:Connect(function()
    -- upvalues: (copy) v_u_388, (copy) v_u_11, (copy) v_u_393, (copy) v_u_394, (copy) v_u_389, (copy) v_u_392
    local v395
    if v_u_388 == nil then
        v395 = false
    else
        v395 = v_u_11:GetAttribute("EquippedAttack") == v_u_388.Name
    end
    v_u_393.Visible = v395
    v_u_394.Visible = v395
    if v395 then
        local v396 = v_u_11:GetAttribute("ThorState") or "Hand"
        local v397 = 0
        local v398 = "[CLICK] THROW"
        local v399 = Color3.new(1, 1, 1)
        if v396 == "Away" then
            local v400 = (v_u_11:GetAttribute("ThorReturnAt") or 0) - workspace:GetServerTimeNow()
            local v401 = math.max(v400, 0)
            local v402 = v401 / v_u_389.ReturnAfter
            v397 = math.clamp(v402, 0, 1)
            v398 = string.format("BACK IN %.1fs", v401)
            v399 = Color3.fromRGB(190, 200, 215)
        elseif v396 == "Return" then
            v399 = v_u_392
            v398 = "RETURNING..."
        elseif v396 == "Summon" then
            v399 = v_u_392
            v398 = "SUMMONING..."
            v397 = 1
        elseif v396 == "Stomp" then
            v399 = v_u_392
            v398 = "STOMP!"
        end
        v_u_393.Text = v398
        v_u_393.TextColor3 = v399
        v_u_393.Cooldown.Size = UDim2.fromScale(v397, 1)
        local v403 = v_u_389.HitsToCharge
        if v_u_11:GetAttribute("ThorCharged") then
            v_u_394.Text = (v396 == "Hand" or v396 == "Stomp") and "[E] THUNDER STOMP!" or "[E] STOMP - NO HAMMER"
            local v404 = v_u_394
            local v405 = v_u_392
            local v406 = Color3.new(1, 1, 1)
            local v407 = os.clock() * 7
            v404.TextColor3 = v405:Lerp(v406, (math.sin(v407) + 1) / 2)
            v_u_394.Cooldown.Size = UDim2.fromScale(0, 1)
        else
            local v408 = v_u_11:GetAttribute("ThorHits") or 0
            v_u_394.Text = string.format("[E] STOMP %d/%d HITS", v408, v403)
            v_u_394.TextColor3 = Color3.fromRGB(150, 180, 215)
            local v409 = v_u_394.Cooldown
            local v410 = UDim2.fromScale
            local v411 = 1 - v408 / v403
            v409.Size = v410(math.clamp(v411, 0, 1), 1)
        end
    else
        return
    end
end)
v_u_7.onInput(function(p412, p413)
    -- upvalues: (copy) v_u_389, (copy) v_u_388, (copy) v_u_11, (copy) v_u_390, (ref) v_u_374, (copy) v_u_372, (copy) v_u_373, (copy) v_u_4
    if not p413 and (v_u_389 and p412.KeyCode == Enum.KeyCode[v_u_389.Stomp.Key]) then
        local v414
        if v_u_388 == nil then
            v414 = false
        else
            v414 = v_u_11:GetAttribute("EquippedAttack") == v_u_388.Name
        end
        if v414 and v_u_390 then
            if v_u_11:GetAttribute("InFFA") then
                if v_u_11:GetAttribute("ThorCharged") then
                    if (v_u_11:GetAttribute("ThorState") or "Hand") == "Hand" then
                        v_u_390:FireServer("Stomp")
                    else
                        v_u_374 = v_u_374 + 1
                        local v_u_415 = v_u_374
                        v_u_372.Text = "Mjolnir isn\'t in your hand!"
                        v_u_372.TextTransparency = 0
                        v_u_373.Transparency = 0
                        task.delay(1.4, function()
                            -- upvalues: (ref) v_u_374, (copy) v_u_415, (ref) v_u_4, (ref) v_u_372, (ref) v_u_373
                            if v_u_374 == v_u_415 then
                                v_u_4:Create(v_u_372, TweenInfo.new(0.3), {
                                    ["TextTransparency"] = 1
                                }):Play()
                                v_u_4:Create(v_u_373, TweenInfo.new(0.3), {
                                    ["Transparency"] = 1
                                }):Play()
                            end
                        end)
                    end
                else
                    local v416 = string.format("%d more hammer hits!", v_u_389.HitsToCharge - (v_u_11:GetAttribute("ThorHits") or 0))
                    v_u_374 = v_u_374 + 1
                    local v_u_417 = v_u_374
                    v_u_372.Text = v416
                    v_u_372.TextTransparency = 0
                    v_u_373.Transparency = 0
                    task.delay(1.4, function()
                        -- upvalues: (ref) v_u_374, (copy) v_u_417, (ref) v_u_4, (ref) v_u_372, (ref) v_u_373
                        if v_u_374 == v_u_417 then
                            v_u_4:Create(v_u_372, TweenInfo.new(0.3), {
                                ["TextTransparency"] = 1
                            }):Play()
                            v_u_4:Create(v_u_373, TweenInfo.new(0.3), {
                                ["Transparency"] = 1
                            }):Play()
                        end
                    end)
                    return
                end
            else
                return
            end
        end
    end
end)
local v_u_418 = 0
local v_u_419 = 0
v_u_5:BindToRenderStep("ThorShake", Enum.RenderPriority.Camera.Value + 1, function()
    -- upvalues: (ref) v_u_418, (ref) v_u_419
    if game:GetService("Players").LocalPlayer:GetAttribute("Setting_CameraShake") == false then
        return
    else
        local v420 = v_u_418 - os.clock()
        if v420 > 0 then
            local v421 = v_u_419
            local v422 = v420 * 2
            local v423 = v421 * math.min(1, v422)
            local v424 = workspace.CurrentCamera
            local v425 = v424.CFrame
            local v426 = CFrame.Angles
            local v427 = (math.random() - 0.5) * v423
            local v428 = math.rad(v427)
            local v429 = (math.random() - 0.5) * v423
            v424.CFrame = v425 * v426(v428, math.rad(v429), 0)
        end
    end
end)
if v391 then
    local v_u_430 = nil
    task.spawn(function()
        -- upvalues: (ref) v_u_430, (copy) v_u_3
        v_u_430 = require(v_u_3:WaitForChild("LibVFXKit"))
    end)
    local v_u_431 = {}
    v391.OnClientEvent:Connect(function(p432, p_u_433, p434, _)
        -- upvalues: (copy) v_u_387, (copy) v_u_389, (copy) v_u_392, (ref) v_u_418, (ref) v_u_419, (ref) v_u_430, (copy) v_u_431
        if p432 == "Summoned" then
            v_u_387("MJOLNIR!", "Click: throw it  -  " .. v_u_389.HitsToCharge .. " hits = [" .. v_u_389.Stomp.Key .. "] Thunder Stomp", v_u_392, 2.5)
            if v_u_418 < os.clock() or v_u_419 < 2.5 then
                v_u_419 = 2.5
            end
            local v435 = v_u_418
            local v436 = os.clock() + 0.5
            v_u_418 = math.max(v435, v436)
            return
        elseif p432 == "Charged" then
            v_u_387("CHARGED!", "[" .. v_u_389.Stomp.Key .. "] THUNDER STOMP unlocked", v_u_392, 2.5)
            if v_u_418 < os.clock() or v_u_419 < 2 then
                v_u_419 = 2
            end
            local v437 = v_u_418
            local v438 = os.clock() + 0.4
            v_u_418 = math.max(v437, v438)
            return
        elseif p432 == "Caught" then
            if v_u_418 < os.clock() or v_u_419 < 1.2 then
                v_u_419 = 1.2
            end
            local v439 = v_u_418
            local v440 = os.clock() + 0.2
            v_u_418 = math.max(v439, v440)
        elseif p432 == "Shake" and typeof(p_u_433) == "Vector3" then
            local v441 = 1 - (workspace.CurrentCamera.CFrame.Position - p_u_433).Magnitude / 120
            local v442 = math.clamp(v441, 0, 1)
            if v442 > 0 then
                local v443 = v442 * 5
                local v444 = v442 * 0.5 + 0.4
                if v_u_418 < os.clock() or v_u_419 < v443 then
                    v_u_419 = v443
                end
                local v445 = v_u_418
                local v446 = os.clock() + v444
                v_u_418 = math.max(v445, v446)
                return
            end
        else
            if p432 == "Tether" and (typeof(p_u_433) == "Instance" and typeof(p434) == "Instance") then
                local v447 = p_u_433:FindFirstChild("Right Arm")
                local v448 = p434:FindFirstChild("Handle")
                if v448 then
                    v448 = v448:FindFirstChild("HeadCenter")
                end
                if v447 and (v448 and v_u_430) then
                    local v449 = v_u_431[p_u_433]
                    if v449 then
                        v_u_431[p_u_433] = nil
                        if v449.beam then
                            v449.beam.stop(0.15)
                        end
                        game:GetService("Debris"):AddItem(v449.att, 0.2)
                    end
                    local v450 = Instance.new("Attachment")
                    v450.Name = "ThorTether"
                    v450.Position = Vector3.new(0, -1, 0)
                    v450.Parent = v447
                    local v_u_451 = {
                        ["att"] = v450
                    }
                    v_u_431[p_u_433] = v_u_451
                    v_u_451.beam = v_u_430.beam("LightningBeam", v450, v448, {
                        ["width"] = 0.3
                    })
                    task.delay(4, function()
                        -- upvalues: (ref) v_u_431, (copy) p_u_433, (copy) v_u_451
                        if v_u_431[p_u_433] == v_u_451 then
                            local v452 = p_u_433
                            local v453 = v_u_431[v452]
                            if not v453 then
                                return
                            end
                            v_u_431[v452] = nil
                            if v453.beam then
                                v453.beam.stop(0.15)
                            end
                            game:GetService("Debris"):AddItem(v453.att, 0.2)
                        end
                    end)
                end
            end
            if p432 == "TetherEnd" and typeof(p_u_433) == "Instance" then
                local v454 = v_u_431[p_u_433]
                if not v454 then
                    return
                end
                v_u_431[p_u_433] = nil
                if v454.beam then
                    v454.beam.stop(0.15)
                end
                game:GetService("Debris"):AddItem(v454.att, 0.2)
            end
        end
    end)
end
local v_u_455 = Instance.new("Folder")
v_u_455.Name = "ThorArcs"
v_u_455.Parent = workspace.CurrentCamera
local v_u_456 = {
    "Torso",
    "Right Arm",
    "Left Arm",
    "Right Leg",
    "Left Leg",
    "Head"
}
local function v_u_469(p457, p458) -- name: localArc
    -- upvalues: (copy) v_u_392, (copy) v_u_455
    local v459 = p458 - p457
    local v460 = p457
    for v461 = 1, 4 do
        local v462
        if v461 < 4 then
            local v463 = p457 + v459 * (v461 / 4)
            local v464 = math.random() - 0.5
            local v465 = math.random() - 0.5
            local v466 = math.random() - 0.5
            v462 = v463 + Vector3.new(v464, v465, v466) * v459.Magnitude * 0.35
        else
            v462 = p458
        end
        local v467 = Instance.new("Part")
        v467.Anchored = true
        v467.CanCollide = false
        v467.CanQuery = false
        v467.CanTouch = false
        v467.CastShadow = false
        v467.Material = Enum.Material.Neon
        v467.Color = math.random() < 0.5 and Color3.fromRGB(215, 240, 255) or v_u_392
        local v468 = (v462 - v460).Magnitude
        v467.Size = Vector3.new(0.09, 0.09, v468)
        v467.CFrame = CFrame.lookAt((v460 + v462) / 2, v462)
        v467.Parent = v_u_455
        game:GetService("Debris"):AddItem(v467, 0.07)
        v460 = v462
    end
end
local v_u_470 = 0
v_u_5.Heartbeat:Connect(function()
    -- upvalues: (copy) v_u_388, (ref) v_u_470, (copy) v_u_8, (copy) v_u_1, (copy) v_u_456, (copy) v_u_469
    if v_u_388 and os.clock() >= v_u_470 then
        v_u_470 = os.clock() + 0.07
        local v471 = workspace.CurrentCamera.CFrame.Position
        for _, v472 in v_u_8.withBots(v_u_1:GetPlayers()) do
            if not v472:GetAttribute("ThorCharged") or (v472:GetAttribute("EquippedAttack") ~= v_u_388.Name or math.random() >= 0.75) then
                continue
            end
            local v473 = v472.Character
            if v473 then
                v473 = v472.Character:FindFirstChild(v_u_456[math.random(#v_u_456)])
            end
            if not v473 or (v473.Position - v471).Magnitude >= 220 then
                continue
            end
            local v474 = v473.Size / 2
            local v475 = (v473.CFrame * CFrame.new((math.random() * 2 - 1) * v474.X, (math.random() * 2 - 1) * v474.Y, (math.random() * 2 - 1) * v474.Z)).Position
            local v476 = math.random() - 0.5
            local v477 = math.random() - 0.5
            local v478 = math.random() - 0.5
            local v479 = Vector3.new(v476, v477, v478)
            if v479.Magnitude <= 0.05 then
                continue
            end
            v_u_469(v475, v475 + v479.Unit * (1.2 + math.random() * 1.6))
        end
    end
end)
if v_u_388 then
    task.spawn(function()
        -- upvalues: (copy) v_u_3, (copy) v_u_5, (copy) v_u_8, (copy) v_u_1, (copy) v_u_388
        local v_u_480 = require(v_u_3:WaitForChild("JJKAnim"))
        local v481 = v_u_480.ANIMS.ThorIdle
        if v481 and not v_u_480.ANIMS.ThorIdleArms then
            local v482 = table.clone(v481.Tracks)
            v482.Head = nil
            v_u_480.ANIMS.ThorIdleArms = {
                ["Length"] = v481.Length,
                ["Loop"] = v481.Loop,
                ["Tracks"] = v482
            }
        end
        local v_u_483 = {}
        local v_u_484 = {}
        local v_u_485 = 0
        v_u_5.Heartbeat:Connect(function()
            -- upvalues: (ref) v_u_485, (ref) v_u_8, (ref) v_u_1, (ref) v_u_388, (copy) v_u_480, (copy) v_u_484, (copy) v_u_483
            if os.clock() >= v_u_485 then
                v_u_485 = os.clock() + 0.1
                local v486 = {}
                for _, v487 in v_u_8.withBots(v_u_1:GetPlayers()) do
                    local v488 = v487.Character
                    local v489
                    if v488 then
                        v489 = v488:FindFirstChildOfClass("Humanoid")
                    else
                        v489 = v488
                    end
                    local v490
                    if v488 then
                        v490 = v488:FindFirstChild("HumanoidRootPart")
                    else
                        v490 = v488
                    end
                    if v489 and (v490 and (v489.Health > 0 and (v487:GetAttribute("EquippedAttack") == v_u_388.Name and (v487:GetAttribute("ThorState") == "Hand" and (v488:FindFirstChild("Mjolnir") and (v489.MoveDirection.Magnitude < 0.1 and not v_u_480.busy(v488))))))) then
                        local v491 = v490.AssemblyLinearVelocity
                        local v492 = v491.X
                        local v493 = v491.Z
                        if Vector3.new(v492, 0, v493).Magnitude < 1.5 then
                            local v494 = v491.Y
                            if math.abs(v494) < 6 and (not v488:GetAttribute("Ragdolled") and (not v489.Sit and (not v489.PlatformStand and v488:GetAttribute("EmoteId") == nil))) then
                                v486[v488] = true
                            end
                        end
                    end
                    if v488 and v487:GetAttribute("ThorState") == "Hand" then
                        v_u_484[v488] = v_u_484[v488] or os.clock()
                        if os.clock() - v_u_484[v488] >= 0.45 then
                            continue
                        end
                        v486[v488] = nil
                    else
                        if not v488 then
                            continue
                        end
                        v_u_484[v488] = nil
                    end
                end
                for v495 in v_u_484 do
                    if v495.Parent then
                        continue
                    end
                    v_u_484[v495] = nil
                end
                for v496, v497 in v_u_483 do
                    if v486[v496] and not v497.stopAt then
                        continue
                    end
                    v_u_480.stop(v497, 0.25)
                    v_u_483[v496] = nil
                end
                for v498 in v486 do
                    if v_u_483[v498] then
                        continue
                    end
                    v_u_483[v498] = v_u_480.play(v498, v_u_480.ANIMS.ThorIdleArms and "ThorIdleArms" or "ThorIdle", {
                        ["base"] = true,
                        ["loop"] = true,
                        ["upper"] = true,
                        ["fadeIn"] = 0.3,
                        ["fadeOut"] = 0.3
                    })
                end
            end
        end)
    end)
end
if v_u_6.Get("Itachi") then
    task.spawn(function()
        -- upvalues: (copy) v_u_10, (ref) v_u_374, (copy) v_u_372, (copy) v_u_373, (copy) v_u_4, (copy) v_u_11
        local v499 = v_u_10:WaitForChild("ItachiFX", 10)
        if v499 then
            v499.OnClientEvent:Connect(function(p500, p501)
                -- upvalues: (ref) v_u_374, (ref) v_u_372, (ref) v_u_373, (ref) v_u_4, (ref) v_u_11
                if p500 == "NoTarget" then
                    local v502 = typeof(p501) == "string" and p501 and p501 or "No enemy nearby!"
                    v_u_374 = v_u_374 + 1
                    local v_u_503 = v_u_374
                    v_u_372.Text = v502
                    v_u_372.TextTransparency = 0
                    v_u_373.Transparency = 0
                    task.delay(1.4, function()
                        -- upvalues: (ref) v_u_374, (copy) v_u_503, (ref) v_u_4, (ref) v_u_372, (ref) v_u_373
                        if v_u_374 == v_u_503 then
                            v_u_4:Create(v_u_372, TweenInfo.new(0.3), {
                                ["TextTransparency"] = 1
                            }):Play()
                            v_u_4:Create(v_u_373, TweenInfo.new(0.3), {
                                ["Transparency"] = 1
                            }):Play()
                        end
                    end)
                elseif p500 == "Face" and typeof(p501) == "Vector3" then
                    local v504 = v_u_11.Character
                    local v505
                    if v504 then
                        v505 = v504:FindFirstChild("HumanoidRootPart")
                    else
                        v505 = v504
                    end
                    if not v505 or (v505.Anchored or v504:GetAttribute("Ragdolled")) then
                        return
                    end
                    local v506 = p501.X - v505.Position.X
                    local v507 = p501.Z - v505.Position.Z
                    local v508 = Vector3.new(v506, 0, v507)
                    if v508.Magnitude > 0.1 then
                        v505.CFrame = CFrame.lookAt(v505.Position, v505.Position + v508)
                    end
                end
            end)
        end
    end)
end
if v_u_6.Get("Roaring Knight") then
    task.spawn(function()
        -- upvalues: (copy) v_u_10, (copy) v_u_11, (copy) v_u_4, (ref) v_u_418, (ref) v_u_419, (copy) v_u_5, (copy) v_u_2, (copy) v_u_108
        local v509 = v_u_10:WaitForChild("KnightFX", 10)
        if v509 then
            local v_u_510 = Instance.new("ScreenGui")
            v_u_510.Name = "KnightScreen"
            v_u_510.IgnoreGuiInset = true
            v_u_510.ResetOnSpawn = false
            v_u_510.DisplayOrder = 70
            v_u_510.Enabled = false
            v_u_510.Parent = v_u_11:WaitForChild("PlayerGui")
            local v_u_511 = Instance.new("Frame")
            v_u_511.Size = UDim2.fromScale(1, 1)
            v_u_511.BorderSizePixel = 0
            v_u_511.BackgroundColor3 = Color3.new(0, 0, 0)
            v_u_511.BackgroundTransparency = 1
            v_u_511.Parent = v_u_510
            local v_u_512 = Instance.new("ImageLabel")
            v_u_512.AnchorPoint = Vector2.new(0.5, 0.5)
            v_u_512.Position = UDim2.fromScale(0.46, 0.52)
            v_u_512.Size = UDim2.fromScale(0.98, 0.16)
            v_u_512.BackgroundTransparency = 1
            v_u_512.Image = "rbxassetid://82500316371865"
            v_u_512.ScaleType = Enum.ScaleType.Stretch
            v_u_512.Rotation = -7
            v_u_512.ImageTransparency = 1
            v_u_512.Parent = v_u_511
            local v_u_513 = Instance.new("UIGradient")
            v_u_513.Transparency = NumberSequence.new({
                NumberSequenceKeypoint.new(0, 0),
                NumberSequenceKeypoint.new(0.49, 0),
                NumberSequenceKeypoint.new(0.51, 1),
                NumberSequenceKeypoint.new(1, 1)
            })
            v_u_513.Parent = v_u_512
            local v_u_514 = Instance.new("UIScale")
            v_u_514.Parent = v_u_512
            task.spawn(function()
                -- upvalues: (copy) v_u_512
                local function v516()
                    -- upvalues: (ref) v_u_512
                    local v515 = { v_u_512 }
                    game:GetService("ContentProvider"):PreloadAsync(v515)
                end
                pcall(v516)
            end)
            local v_u_517 = 0
            local function v_u_522() -- name: ultraSlash
                -- upvalues: (ref) v_u_517, (copy) v_u_510, (copy) v_u_511, (copy) v_u_512, (copy) v_u_513, (copy) v_u_514, (ref) v_u_4, (ref) v_u_418, (ref) v_u_419
                v_u_517 = v_u_517 + 1
                local v518 = v_u_517
                v_u_510.Enabled = true
                v_u_511.BackgroundTransparency = 0
                v_u_512.ImageTransparency = 0
                v_u_513.Offset = Vector2.new(-0.52, 0)
                v_u_514.Scale = 0.97
                v_u_4:Create(v_u_513, TweenInfo.new(0.13, Enum.EasingStyle.Quad, Enum.EasingDirection.Out), {
                    ["Offset"] = Vector2.new(0.52, 0)
                }):Play()
                v_u_4:Create(v_u_514, TweenInfo.new(0.7, Enum.EasingStyle.Quad, Enum.EasingDirection.Out), {
                    ["Scale"] = 1.04
                }):Play()
                task.wait(0.75)
                if v518 == v_u_517 then
                    if v_u_418 < os.clock() or v_u_419 < 3 then
                        v_u_419 = 3
                    end
                    local v519 = v_u_418
                    local v520 = os.clock() + 0.4
                    v_u_418 = math.max(v519, v520)
                    v_u_4:Create(v_u_511, TweenInfo.new(0.45), {
                        ["BackgroundTransparency"] = 1
                    }):Play()
                    local v521 = v_u_4:Create(v_u_512, TweenInfo.new(0.45), {
                        ["ImageTransparency"] = 1
                    })
                    v521:Play()
                    v521.Completed:Wait()
                    if v518 == v_u_517 then
                        v_u_510.Enabled = false
                    end
                end
            end
            local v_u_523 = Instance.new("ScreenGui")
            v_u_523.Name = "KnightUltraAim"
            v_u_523.IgnoreGuiInset = true
            v_u_523.ResetOnSpawn = false
            v_u_523.DisplayOrder = 3
            v_u_523.Enabled = false
            v_u_523.Parent = v_u_11:WaitForChild("PlayerGui")
            local function v531(p524, p525, p526, p527) -- name: aimCircle
                -- upvalues: (copy) v_u_523
                local v528 = Instance.new("Frame")
                v528.AnchorPoint = Vector2.new(0.5, 0.5)
                v528.Position = UDim2.fromScale(0.5, 0.5)
                v528.Size = UDim2.fromOffset(p527, p527)
                v528.BackgroundTransparency = 1
                v528.BorderSizePixel = 0
                v528.ZIndex = p524
                local v529 = Instance.new("UICorner")
                v529.CornerRadius = UDim.new(1, 0)
                v529.Parent = v528
                local v530 = Instance.new("UIStroke")
                v530.Thickness = p525
                v530.Color = p526
                v530.Transparency = 1
                v530.Parent = v528
                v528.Parent = v_u_523
                return v528, v530
            end
            local v_u_532, v_u_533 = v531(1, 6, Color3.new(0, 0, 0), 80)
            local v_u_534, v_u_535 = v531(2, 2.5, Color3.new(1, 1, 1), 80)
            local v_u_536, v_u_537 = v531(3, 1.5, Color3.new(0, 0, 0), 6)
            v_u_536.BackgroundColor3 = Color3.new(1, 1, 1)
            local v_u_538 = Color3.fromRGB(150, 150, 160)
            local v_u_539 = Color3.fromRGB(255, 40, 55)
            local v_u_540 = nil
            local function v_u_571(p_u_541, p_u_542) -- name: ultraAim
                -- upvalues: (ref) v_u_11, (ref) v_u_540, (copy) v_u_523, (ref) v_u_5, (ref) v_u_2, (copy) v_u_539, (copy) v_u_538, (copy) v_u_534, (copy) v_u_532, (copy) v_u_535, (copy) v_u_533, (copy) v_u_536, (copy) v_u_537
                local v_u_543 = v_u_11.Character
                local v_u_544
                if v_u_543 then
                    v_u_544 = v_u_543:FindFirstChild("HumanoidRootPart")
                else
                    v_u_544 = v_u_543
                end
                local v_u_545
                if v_u_543 then
                    v_u_545 = v_u_543:FindFirstChildOfClass("Humanoid")
                else
                    v_u_545 = v_u_543
                end
                if v_u_544 and (v_u_545 and (p_u_541 and p_u_542)) then
                    if v_u_540 then
                        v_u_540.finish()
                    end
                    local v_u_546 = workspace.CurrentCamera
                    local v_u_547 = v_u_546.FieldOfView < 60 and 70 or v_u_546.FieldOfView
                    local v_u_548 = {
                        ["w"] = 0
                    }
                    v_u_540 = v_u_548
                    v_u_546:SetAttribute("ZoomHold", true)
                    v_u_523.Enabled = true
                    function v_u_548.finish() -- name: finish
                        -- upvalues: (copy) v_u_548, (ref) v_u_540, (ref) v_u_5, (ref) v_u_523, (ref) v_u_2, (copy) v_u_545, (copy) v_u_546, (copy) v_u_547
                        if not v_u_548.done then
                            v_u_548.done = true
                            if v_u_540 == v_u_548 then
                                v_u_540 = nil
                            end
                            local function v549()
                                -- upvalues: (ref) v_u_5
                                v_u_5:UnbindFromRenderStep("KnightUltraAim")
                            end
                            pcall(v549)
                            v_u_523.Enabled = false
                            v_u_2.MouseBehavior = Enum.MouseBehavior.Default
                            if v_u_545.Parent then
                                v_u_545.CameraOffset = Vector3.new(0, 0, 0)
                            end
                            v_u_546.FieldOfView = v_u_547
                            v_u_546:SetAttribute("ZoomHold", nil)
                        end
                    end
                    v_u_5:BindToRenderStep("KnightUltraAim", Enum.RenderPriority.Camera.Value + 2, function(p550)
                        -- upvalues: (copy) p_u_541, (copy) v_u_544, (ref) v_u_11, (copy) v_u_543, (copy) v_u_545, (copy) v_u_548, (copy) p_u_542, (ref) v_u_2, (copy) v_u_546, (copy) v_u_547, (ref) v_u_539, (ref) v_u_538, (ref) v_u_534, (ref) v_u_532, (ref) v_u_535, (ref) v_u_533, (ref) v_u_536, (ref) v_u_537
                        local v551 = workspace:GetServerTimeNow() - p_u_541
                        if v_u_544.Parent and (v_u_11.Character == v_u_543 and v_u_545.Health > 0) then
                            local v552 = p_u_542 + 0.4 < v551
                            local v553 = v_u_548
                            local v554 = v553.w
                            local v555 = (v552 and 0 or 1) - v_u_548.w
                            local v556 = -p550 / 0.09
                            v553.w = v554 + v555 * (1 - math.exp(v556))
                            if v552 and v_u_548.w < 0.03 then
                                v_u_548.finish()
                            else
                                local v557 = v_u_548.w
                                v_u_2.MouseBehavior = Enum.MouseBehavior.LockCenter
                                v_u_545.CameraOffset = Vector3.new(3, 1, 0) * v557
                                local v558 = v_u_546.CFrame.LookVector
                                local v559 = (v_u_546.CFrame.Position - v_u_546.Focus.Position).Magnitude
                                if v559 > 13 then
                                    local v560 = v_u_546
                                    v560.CFrame = v560.CFrame + v558 * ((v559 - 13) * v557)
                                end
                                local v561, v562, v563
                                if v551 < p_u_542 then
                                    local v564 = v551 / p_u_542
                                    local v565 = math.clamp(v564, 0, 1)
                                    local v566 = 1 - (1 - v565) * (1 - v565)
                                    v561 = v_u_547 - v566 * 10
                                    v562 = 84 - v566 * 54
                                    v563 = v565 >= 0.95 and v_u_539 or v_u_538:Lerp(Color3.new(1, 1, 1), v565)
                                else
                                    local v567 = v551 - p_u_542
                                    v561 = v567 < 0.08 and v_u_547 - 10 + 24 * v567 / 0.08
                                    if not v561 then
                                        local v568 = v_u_547
                                        local v569 = -(v567 - 0.08) / 0.25
                                        v561 = v568 + math.exp(v569) * 14
                                    end
                                    local v570 = -v567 / 0.12
                                    v562 = math.exp(v570) * 30 + 22
                                    v563 = Color3.new(1, 1, 1)
                                end
                                v_u_546.FieldOfView = v_u_547 + (v561 - v_u_547) * v557
                                v_u_534.Size = UDim2.fromOffset(v562, v562)
                                v_u_532.Size = v_u_534.Size
                                v_u_535.Color = v563
                                v_u_535.Transparency = 1 - 0.9 * v557
                                v_u_533.Transparency = 1 - 0.45 * v557
                                v_u_536.BackgroundTransparency = 1 - v557
                                v_u_537.Transparency = 1 - v557
                            end
                        else
                            v_u_548.finish()
                            return
                        end
                    end)
                end
            end
            local v_u_572 = Instance.new("Frame")
            v_u_572.Name = "KnightAppeared"
            v_u_572.AnchorPoint = Vector2.new(0.5, 1)
            v_u_572.Position = UDim2.new(0.5, 0, 1, -200)
            v_u_572.Size = UDim2.new(0.62, 0, 0, 66)
            v_u_572.BackgroundColor3 = Color3.new(0, 0, 0)
            v_u_572.BackgroundTransparency = 0.1
            v_u_572.Visible = false
            local v573 = Instance.new("UISizeConstraint")
            v573.MaxSize = Vector2.new(780, 66)
            v573.Parent = v_u_572
            local v_u_574 = Instance.new("UIStroke")
            v_u_574.Thickness = 3
            v_u_574.Color = Color3.new(1, 1, 1)
            v_u_574.ApplyStrokeMode = Enum.ApplyStrokeMode.Border
            v_u_574.Parent = v_u_572
            local v_u_575 = Instance.new("TextLabel")
            v_u_575.BackgroundTransparency = 1
            v_u_575.Position = UDim2.fromOffset(22, 10)
            v_u_575.Size = UDim2.new(1, -44, 1, -20)
            v_u_575.Font = Enum.Font.Arcade
            v_u_575.TextScaled = true
            v_u_575.TextColor3 = Color3.new(1, 1, 1)
            v_u_575.TextXAlignment = Enum.TextXAlignment.Left
            v_u_575.RichText = true
            v_u_575.Text = "The <font color=\"#ff2a2a\">Roaring Knight</font> appeared."
            v_u_575.Parent = v_u_572
            v_u_572.Parent = v_u_108
            local v_u_576 = 0
            local function v_u_579() -- name: appeared
                -- upvalues: (ref) v_u_576, (copy) v_u_572, (copy) v_u_574, (copy) v_u_575, (ref) v_u_4
                v_u_576 = v_u_576 + 1
                local v577 = v_u_576
                v_u_572.Visible = true
                v_u_572.BackgroundTransparency = 0.1
                v_u_574.Transparency = 0
                v_u_575.TextTransparency = 0
                for v578 = 0, utf8.len(v_u_575.ContentText) or 30 do
                    if v577 ~= v_u_576 then
                        return
                    end
                    v_u_575.MaxVisibleGraphemes = v578
                    task.wait(0.035)
                end
                task.wait(2.2)
                if v577 == v_u_576 then
                    v_u_4:Create(v_u_572, TweenInfo.new(0.4), {
                        ["BackgroundTransparency"] = 1
                    }):Play()
                    v_u_4:Create(v_u_574, TweenInfo.new(0.4), {
                        ["Transparency"] = 1
                    }):Play()
                    v_u_4:Create(v_u_575, TweenInfo.new(0.4), {
                        ["TextTransparency"] = 1
                    }):Play()
                    task.wait(0.4)
                    if v577 == v_u_576 then
                        v_u_572.Visible = false
                    end
                end
            end
            v509.OnClientEvent:Connect(function(p580, p581, p582)
                -- upvalues: (copy) v_u_522, (copy) v_u_571, (ref) v_u_418, (ref) v_u_419, (ref) v_u_11, (copy) v_u_579
                if p580 == "UltraSlash" then
                    task.spawn(v_u_522)
                    return
                elseif p580 == "UltraAim" then
                    v_u_571(tonumber(p581), (tonumber(p582)))
                    return
                elseif p580 == "Shake" and typeof(p581) == "number" then
                    local v583 = math.clamp(p581, 0, 8)
                    if v_u_418 < os.clock() or v_u_419 < v583 then
                        v_u_419 = v583
                    end
                    local v584 = v_u_418
                    local v585 = os.clock() + 0.35
                    v_u_418 = math.max(v584, v585)
                elseif p580 == "Appeared" then
                    local v586 = v_u_11.PlayerGui:FindFirstChild("KnightBlackout")
                    if not (v586 and v586.Enabled) then
                        task.spawn(v_u_579)
                    end
                end
            end)
        end
    end)
end
local v_u_587 = Instance.new("UIScale")
v_u_587.Parent = v_u_109
v_u_5.RenderStepped:Connect(function()
    -- upvalues: (copy) v_u_109, (copy) v_u_587
    local v588 = v_u_109.AbsoluteSize.X
    local v589 = v_u_587.Scale
    local v590 = v588 / math.max(v589, 0.01)
    local v591 = v_u_587
    local v592 = (workspace.CurrentCamera.ViewportSize.X - 24) / math.max(v590, 1)
    v591.Scale = math.clamp(v592, 0.45, 1)
end)
local function v595(p593) -- name: noTrip
    local v594 = p593:WaitForChild("Humanoid", 10)
    if v594 then
        v594:SetStateEnabled(Enum.HumanoidStateType.FallingDown, false)
        v594:SetStateEnabled(Enum.HumanoidStateType.Ragdoll, false)
    end
end
v_u_11.CharacterAdded:Connect(v595)
if v_u_11.Character then
    task.spawn(v595, v_u_11.Character)
end
local function v600(p596) -- name: knockTimer
    local v597 = p596:WaitForChild("HumanoidRootPart", 10)
    if v597 then
        v597.ChildAdded:Connect(function(p_u_598)
            local v599 = p_u_598:IsA("LinearVelocity") and p_u_598:GetAttribute("KnockPush")
            if v599 then
                v599 = p_u_598:GetAttribute("KnockLife")
            end
            if type(v599) == "number" then
                task.delay(v599, function()
                    -- upvalues: (copy) p_u_598
                    if p_u_598.Parent then
                        p_u_598:Destroy()
                    end
                end)
            end
        end)
    end
end
v_u_11.CharacterAdded:Connect(v600)
if v_u_11.Character then
    task.spawn(v600, v_u_11.Character)
end
v_u_11.CharacterAdded:Connect(function(p601) -- name: followCamera
    local v602 = p601:WaitForChild("Humanoid", 10)
    if v602 then
        local v603 = workspace.CurrentCamera
        for _ = 1, 3 do
            v603.CameraType = Enum.CameraType.Custom
            v603.CameraSubject = v602
            task.wait(0.1)
        end
    end
end)
local v_u_604 = require(script.Parent:WaitForChild("KnightFlight"))
local v_u_605 = CFrame.new()
v_u_5.Stepped:Connect(function(_, p606)
    -- upvalues: (copy) v_u_8, (copy) v_u_1, (copy) v_u_605, (copy) v_u_604
    for _, v607 in v_u_8.withBots(v_u_1:GetPlayers()) do
        local v608 = v607.Character
        local v609
        if v608 then
            v609 = v608:FindFirstChild("VerityMorph") or v608:FindFirstChild("KnightMorph")
        else
            v609 = v608
        end
        local v610
        if v608 then
            v610 = v608:FindFirstChild("Torso")
        else
            v610 = v608
        end
        if not (v609 and v610) then
            continue
        end
        for _, v611 in v609:GetDescendants() do
            if not v611:IsA("Motor6D") then
                continue
            end
            local v612 = v611:GetAttribute("GhostOf")
            if v612 then
                v612 = v610:FindFirstChild(v612)
            end
            if v612 then
                local v613 = v611:GetAttribute("GhostScale")
                v611.Transform = v613 and v_u_605:Lerp(v612.Transform, v613) or v612.Transform
                if v611:GetAttribute("FollowC0") then
                    v611.C0 = v612.C0
                end
            end
            local v614 = v611:GetAttribute("KneeOf")
            if v614 then
                v614 = v608:FindFirstChild(v614)
            end
            if not v614 then
                continue
            end
            local v615 = v610.CFrame:VectorToObjectSpace(-v614.CFrame.UpVector).Z
            local v616 = math.max(0, v615)
            v611.Transform = CFrame.Angles(-(0.12 + v616 * 1.6 * (v611:GetAttribute("GhostScale") or 1)), 0, 0)
        end
        v_u_604.Step(v607, v608, v609, v610, p606)
    end
    v_u_604.EndFrame()
end)
task.spawn(function()
    -- upvalues: (copy) v_u_10, (copy) v_u_11, (copy) v_u_4
    local v617 = v_u_10:WaitForChild("BountyFX", 30)
    if v617 then
        local v_u_618 = Color3.fromRGB(234, 214, 166)
        local v_u_619 = Color3.fromRGB(78, 46, 18)
        local v_u_620 = Color3.fromRGB(200, 30, 30)
        local v_u_621 = Font.new("rbxasset://fonts/families/Merriweather.json", Enum.FontWeight.Bold)
        local v_u_622 = Font.new("rbxasset://fonts/families/SpecialElite.json")
        local v_u_623 = Instance.new("ScreenGui")
        v_u_623.Name = "BountyPoster"
        v_u_623.ResetOnSpawn = false
        v_u_623.IgnoreGuiInset = true
        v_u_623.DisplayOrder = 40
        v_u_623.Parent = v_u_11:WaitForChild("PlayerGui")
        local v_u_624 = nil
        local v_u_625 = 0
        local function v_u_636(p626, p_u_627, p628) -- name: face
            if p628 then
                local v629 = Instance.new("ImageLabel")
                v629.Size = UDim2.fromScale(1, 1)
                v629.BackgroundTransparency = 1
                v629.Image = "rbxthumb://type=AvatarHeadShot&id=" .. p628 .. "&w=420&h=420"
                v629.ImageColor3 = Color3.fromRGB(255, 236, 200)
                v629.Parent = p626
                return
            elseif typeof(p_u_627) == "Instance" and p_u_627.Parent then
                local v_u_630 = Instance.new("ViewportFrame")
                v_u_630.Size = UDim2.fromScale(1, 1)
                v_u_630.BackgroundTransparency = 1
                v_u_630.Ambient = Color3.fromRGB(190, 175, 150)
                v_u_630.LightColor = Color3.fromRGB(255, 240, 210)
                v_u_630.LightDirection = Vector3.new(-1, -1, -1)
                v_u_630.Parent = p626
                pcall(function()
                    -- upvalues: (copy) p_u_627, (copy) v_u_630
                    local v631 = p_u_627.Archivable
                    p_u_627.Archivable = true
                    local v632 = p_u_627:Clone()
                    p_u_627.Archivable = v631
                    for _, v633 in v632:GetDescendants() do
                        if not (v633:IsA("LuaSourceContainer") or (v633:IsA("BillboardGui") or (v633:IsA("ParticleEmitter") or (v633:IsA("Sound") or v633:IsA("Highlight"))))) then
                            continue
                        end
                        v633:Destroy()
                    end
                    v632.Parent = v_u_630
                    local v634 = v632:FindFirstChild("Head")
                    local v635 = Instance.new("Camera")
                    v635.FieldOfView = 30
                    if v634 then
                        v635.CFrame = CFrame.lookAt((v634.CFrame * CFrame.new(0, 0.15, -4.5)).Position, v634.Position)
                    end
                    v635.Parent = v_u_630
                    v_u_630.CurrentCamera = v635
                end)
            end
        end
        local function v_u_661(p637, p638, p639, p640) -- name: makePoster
            -- upvalues: (copy) v_u_618, (copy) v_u_619, (copy) v_u_621, (copy) v_u_636, (copy) v_u_622, (copy) v_u_623
            local v641 = workspace.CurrentCamera.ViewportSize.Y / 900
            local v642 = math.clamp(v641, 0.55, 1)
            local v643 = Instance.new("Frame")
            v643.Name = "Poster"
            v643.AnchorPoint = Vector2.new(0.5, 0.5)
            v643.Position = UDim2.fromScale(0.5, -0.4)
            v643.Size = UDim2.fromOffset(260, 340)
            v643.BackgroundColor3 = v_u_618
            v643.BorderSizePixel = 0
            v643.Rotation = -8
            local v644 = Instance.new("UICorner")
            v644.CornerRadius = UDim.new(0, 6)
            v644.Parent = v643
            local v645 = Instance.new("UIStroke")
            v645.Thickness = 5
            v645.Color = v_u_619
            v645.Parent = v643
            local v646 = Instance.new("UIScale")
            v646.Scale = v642
            v646.Parent = v643
            local v647 = {
                ["Name"] = "Title",
                ["Position"] = nil,
                ["Size"] = nil,
                ["Text"] = "WANTED",
                ["FontFace"] = nil,
                ["Position"] = UDim2.fromOffset(12, 8),
                ["Size"] = UDim2.new(1, -24, 0, 56),
                ["FontFace"] = v_u_621
            }
            local v648 = Instance.new("TextLabel")
            v648.BackgroundTransparency = 1
            v648.TextColor3 = v_u_619
            v648.TextScaled = true
            for v649, v650 in v647 do
                v648[v649] = v650
            end
            v648.Parent = v643
            local v651 = Instance.new("Frame")
            v651.Name = "Face"
            v651.Position = UDim2.fromOffset(40, 70)
            v651.Size = UDim2.fromOffset(180, 180)
            v651.BackgroundColor3 = Color3.fromRGB(205, 182, 135)
            v651.BorderSizePixel = 0
            v651.ClipsDescendants = true
            local v652 = Instance.new("UIStroke")
            v652.Thickness = 3
            v652.Color = v_u_619
            v652.Parent = v651
            v651.Parent = v643
            v_u_636(v651, p637, p638)
            local v653 = {
                ["Name"] = "Who",
                ["Position"] = nil,
                ["Size"] = nil,
                ["Text"] = nil,
                ["FontFace"] = nil,
                ["Position"] = UDim2.fromOffset(12, 258),
                ["Size"] = UDim2.new(1, -24, 0, 34),
                ["Text"] = string.upper((tostring(p639))),
                ["FontFace"] = v_u_622
            }
            local v654 = Instance.new("TextLabel")
            v654.BackgroundTransparency = 1
            v654.TextColor3 = v_u_619
            v654.TextScaled = true
            for v655, v656 in v653 do
                v654[v655] = v656
            end
            v654.Parent = v643
            local v657 = {
                ["Name"] = "Reward",
                ["Position"] = nil,
                ["Size"] = nil,
                ["Text"] = nil,
                ["FontFace"] = nil,
                ["TextColor3"] = nil,
                ["Position"] = UDim2.fromOffset(12, 296),
                ["Size"] = UDim2.new(1, -24, 0, 30),
                ["Text"] = tostring(p640),
                ["FontFace"] = v_u_622,
                ["TextColor3"] = Color3.fromRGB(150, 40, 20)
            }
            local v658 = Instance.new("TextLabel")
            v658.BackgroundTransparency = 1
            v658.TextColor3 = v_u_619
            v658.TextScaled = true
            for v659, v660 in v657 do
                v658[v659] = v660
            end
            v658.Parent = v643
            v643.Parent = v_u_623
            return v643, v646, v642
        end
        local function v_u_665(p_u_662, p663, p664) -- name: flyAway
            -- upvalues: (ref) v_u_4
            v_u_4:Create(p_u_662, TweenInfo.new(p664, Enum.EasingStyle.Quad, Enum.EasingDirection.In), p663):Play()
            task.delay(p664, function()
                -- upvalues: (copy) p_u_662
                p_u_662:Destroy()
            end)
        end
        v617.OnClientEvent:Connect(function(p666, p667, p668, p669, p670)
            -- upvalues: (ref) v_u_625, (ref) v_u_624, (copy) v_u_661, (ref) v_u_4, (copy) v_u_621, (copy) v_u_620, (copy) v_u_619, (copy) v_u_665
            if p666 == "Poster" then
                v_u_625 = v_u_625 + 1
                local v_u_671 = v_u_625
                if v_u_624 then
                    v_u_624.frame:Destroy()
                end
                local v_u_672, v_u_673, v_u_674 = v_u_661(p667, p668, p669, p670)
                v_u_624 = {
                    ["frame"] = v_u_672,
                    ["scale"] = v_u_673
                }
                v_u_4:Create(v_u_672, TweenInfo.new(0.45, Enum.EasingStyle.Back, Enum.EasingDirection.Out), {
                    ["Position"] = UDim2.fromScale(0.5, 0.45)
                }):Play()
                v_u_4:Create(v_u_672, TweenInfo.new(0.9, Enum.EasingStyle.Elastic, Enum.EasingDirection.Out), {
                    ["Rotation"] = 3
                }):Play()
                task.delay(2.2, function()
                    -- upvalues: (copy) v_u_671, (ref) v_u_625, (copy) v_u_672, (ref) v_u_4, (copy) v_u_673, (copy) v_u_674
                    if v_u_671 == v_u_625 and v_u_672.Parent then
                        v_u_4:Create(v_u_672, TweenInfo.new(0.5, Enum.EasingStyle.Quad, Enum.EasingDirection.InOut), {
                            ["Position"] = nil,
                            ["Rotation"] = -5,
                            ["Position"] = UDim2.new(1, -90, 0.45, 0)
                        }):Play()
                        v_u_4:Create(v_u_673, TweenInfo.new(0.5, Enum.EasingStyle.Quad, Enum.EasingDirection.InOut), {
                            ["Scale"] = v_u_674 * 0.5
                        }):Play()
                    end
                end)
                return
            elseif p666 == "Collected" and v_u_624 then
                v_u_625 = v_u_625 + 1
                local v_u_675 = v_u_624.frame
                v_u_624 = nil
                local v676 = {
                    ["Name"] = "Stamp",
                    ["AnchorPoint"] = nil,
                    ["Position"] = nil,
                    ["Size"] = nil,
                    ["Text"] = "COLLECTED",
                    ["FontFace"] = nil,
                    ["TextColor3"] = nil,
                    ["Rotation"] = -18,
                    ["ZIndex"] = 5,
                    ["AnchorPoint"] = Vector2.new(0.5, 0.5),
                    ["Position"] = UDim2.fromScale(0.5, 0.5),
                    ["Size"] = UDim2.fromOffset(250, 70),
                    ["FontFace"] = v_u_621,
                    ["TextColor3"] = v_u_620
                }
                local v677 = Instance.new("TextLabel")
                v677.BackgroundTransparency = 1
                v677.TextColor3 = v_u_619
                v677.TextScaled = true
                for v678, v679 in v676 do
                    v677[v678] = v679
                end
                v677.Parent = v_u_675
                local v680 = Instance.new("UIStroke")
                v680.ApplyStrokeMode = Enum.ApplyStrokeMode.Border
                v680.Thickness = 5
                v680.Color = v_u_620
                v680.Parent = v677
                local v681 = Instance.new("UIScale")
                v681.Scale = 2.2
                v681.Parent = v677
                v_u_4:Create(v681, TweenInfo.new(0.18, Enum.EasingStyle.Quad, Enum.EasingDirection.In), {
                    ["Scale"] = 1
                }):Play()
                task.delay(1.1, function()
                    -- upvalues: (copy) v_u_675, (ref) v_u_665
                    if v_u_675.Parent then
                        v_u_665(v_u_675, {
                            ["Position"] = v_u_675.Position - UDim2.fromScale(0, 0.8),
                            ["Rotation"] = v_u_675.Rotation + 25
                        }, 0.5)
                    end
                end)
            elseif p666 == "Gone" and v_u_624 then
                v_u_625 = v_u_625 + 1
                local v682 = v_u_624.frame
                v_u_624 = nil
                v_u_665(v682, {
                    ["Position"] = v682.Position + UDim2.fromScale(0.35, 0),
                    ["Rotation"] = v682.Rotation + 15
                }, 0.45)
            end
        end)
    end
end)
local function v_u_687() -- name: takeWeapon
    -- upvalues: (copy) v_u_11
    local v683 = v_u_11.Character
    local v684
    if v683 then
        v684 = v683:FindFirstChildOfClass("Humanoid")
    else
        v684 = v683
    end
    local v685 = v_u_11:FindFirstChildOfClass("Backpack")
    if v684 and (v684.Health > 0 and (v685 and not v683:FindFirstChildOfClass("Tool"))) then
        for _, v686 in v685:GetChildren() do
            if not (v686:IsA("Tool") and v686:GetAttribute("AttackTool")) then
                continue
            end
            v684:EquipTool(v686)
            return
        end
    end
end
v_u_11:WaitForChild("Backpack").ChildAdded:Connect(function(p688)
    -- upvalues: (copy) v_u_7, (copy) v_u_687
    if p688:IsA("Tool") and p688:GetAttribute("AttackTool") then
        local v689 = v_u_7.mode()
        if v689 == "Gamepad" or v689 == "Touch" then
            task.defer(v_u_687)
        end
    end
end)
v_u_11.CharacterAdded:Connect(function()
    -- upvalues: (copy) v_u_11, (copy) v_u_7, (copy) v_u_687
    v_u_11:WaitForChild("Backpack").ChildAdded:Connect(function(p690)
        -- upvalues: (ref) v_u_7, (ref) v_u_687
        if p690:IsA("Tool") and p690:GetAttribute("AttackTool") then
            local v691 = v_u_7.mode()
            if v691 == "Gamepad" or v691 == "Touch" then
                task.defer(v_u_687)
            end
        end
    end)
    local v692 = v_u_7.mode()
    if v692 == "Gamepad" or v692 == "Touch" then
        task.defer(v_u_687)
    end
end)
v_u_7.ModeChanged:Connect(function() -- name: padWeapon
    -- upvalues: (copy) v_u_7, (copy) v_u_687
    local v693 = v_u_7.mode()
    if v693 == "Gamepad" or v693 == "Touch" then
        task.defer(v_u_687)
    end
end)
v_u_2.InputBegan:Connect(function(p694)
    -- upvalues: (copy) v_u_687
    if p694.KeyCode == Enum.KeyCode.ButtonR2 then
        v_u_687()
    end
end)
local v695 = v_u_7.mode()
if v695 == "Gamepad" or v695 == "Touch" then
    task.defer(v_u_687)
end

-- Script Path: game:GetService("Players").ROBZINHO666.PlayerScripts.CombatClient
-- Took 0.14s to decompile.

local v_u_1 = game:GetService("Players")
local v_u_2 = game:GetService("UserInputService")
local v_u_3 = game:GetService("ReplicatedStorage")
local v_u_4 = game:GetService("TweenService")
local v_u_5 = game:GetService("RunService")
local v_u_6 = require(v_u_3:WaitForChild("AttackData"))
local v_u_7 = require(v_u_3:WaitForChild("Controls"))
local v_u_8 = require(v_u_3:WaitForChild("BotActors"))
local v_u_9 = require(v_u_3:WaitForChild("StdPunch"))
local v_u_10 = v_u_3:WaitForChild("Remotes")
local v_u_11 = v_u_1.LocalPlayer
v_u_11:GetMouse()
local function v_u_17() -- name: lookTarget
    -- upvalues: (copy) v_u_11
    local v12 = workspace.CurrentCamera
    local v13 = RaycastParams.new()
    v13.FilterType = Enum.RaycastFilterType.Exclude
    v13.FilterDescendantsInstances = { v_u_11.Character }
    local v14 = v12.CFrame.Position
    local v15 = v12.CFrame.LookVector * 600
    local v16 = workspace:Raycast(v14, v15, v13)
    return v16 and v16.Position or v14 + v15
end
local function v_u_23() -- name: laserTarget
    -- upvalues: (copy) v_u_11, (copy) v_u_17
    local v18 = v_u_11.Character
    if v18 then
        v18 = v18:FindFirstChild("HumanoidRootPart")
    end
    local v19 = workspace.CurrentCamera.CFrame.LookVector
    local v20 = v19.X
    local v21 = v19.Z
    local v22 = Vector3.new(v20, 0, v21)
    if not v18 then
        return v_u_17()
    end
    if v22.Magnitude < 0.01 then
        v22 = v18.CFrame.LookVector
    end
    return v18.Position + v22.Unit * 1000
end
local function v_u_28() -- name: levelTarget
    -- upvalues: (copy) v_u_11, (copy) v_u_23
    local v24 = workspace.CurrentCamera
    local v25 = RaycastParams.new()
    v25.FilterType = Enum.RaycastFilterType.Exclude
    v25.FilterDescendantsInstances = { v_u_11.Character }
    local v26 = workspace:Raycast(v24.CFrame.Position, v24.CFrame.LookVector * 600, v25)
    if v26 then
        local v27 = v26.Instance:FindFirstAncestorOfClass("Model")
        if v27 then
            v27 = v27:FindFirstChildOfClass("Humanoid")
        end
        if v27 and v27.Health > 0 then
            return v26.Position
        end
    end
    return v_u_23()
end
local function v_u_40() -- name: hammerTarget
    -- upvalues: (copy) v_u_11
    local v29 = workspace.CurrentCamera
    local v30 = RaycastParams.new()
    v30.FilterType = Enum.RaycastFilterType.Exclude
    v30.FilterDescendantsInstances = { v_u_11.Character }
    local v31 = v29.CFrame.Position
    local v32 = v29.CFrame.LookVector
    local v33 = workspace:Raycast(v31, v32 * 1000, v30)
    if v33 then
        local v34 = v33.Instance:FindFirstAncestorOfClass("Model")
        if v34 and v34:FindFirstChildOfClass("Humanoid") then
            return v33.Position
        end
    end
    local v35 = v32.X
    local v36 = v32.Y
    local v37 = math.max(v36, 0.02)
    local v38 = v32.Z
    local v39 = Vector3.new(v35, v37, v38)
    if v39.Magnitude > 0.01 then
        v32 = v39.Unit or v32
    end
    return v31 + v32 * 1000
end
local function v_u_46() -- name: mouseTarget
    -- upvalues: (copy) v_u_7, (copy) v_u_17, (copy) v_u_2, (copy) v_u_11
    local v41 = workspace.CurrentCamera
    if v_u_7.mode() ~= "Keyboard" then
        return v_u_17()
    end
    local v42 = v_u_2:GetMouseLocation()
    if v42.X < 0 or (v42.Y < 0 or (v42.X > v41.ViewportSize.X or v42.Y > v41.ViewportSize.Y)) then
        return v_u_17()
    end
    local v43 = v41:ViewportPointToRay(v42.X, v42.Y)
    local v44 = RaycastParams.new()
    v44.FilterType = Enum.RaycastFilterType.Exclude
    v44.FilterDescendantsInstances = { v_u_11.Character }
    local v45 = workspace:Raycast(v43.Origin, v43.Direction * 1000, v44)
    return v45 and v45.Position or v43.Origin + v43.Direction * 1000
end
local v_u_47 = false
local function v_u_60(p48, p_u_49, p50)
    -- upvalues: (ref) v_u_47, (copy) v_u_5, (copy) v_u_11, (copy) v_u_23, (copy) v_u_10
    if not v_u_47 then
        v_u_47 = true
        local v_u_51 = os.clock() + (p50 or (p48.Charge or 0.5)) + 0.15
        local v_u_52 = 0
        local v_u_53 = nil
        v_u_53 = v_u_5.RenderStepped:Connect(function()
            -- upvalues: (ref) v_u_11, (copy) v_u_51, (ref) v_u_53, (ref) v_u_47, (copy) p_u_49, (ref) v_u_23, (ref) v_u_52, (ref) v_u_10
            local v54 = v_u_11.Character
            local v55
            if v54 then
                v55 = v54:FindFirstChild("HumanoidRootPart")
            else
                v55 = v54
            end
            if v55 and v_u_51 >= os.clock() then
                local v56 = (p_u_49 or v_u_23)()
                local v57 = v56.X - v55.Position.X
                local v58 = v56.Z - v55.Position.Z
                local v59 = Vector3.new(v57, 0, v58)
                if v59.Magnitude > 0.1 and not v54:GetAttribute("Ragdolled") then
                    v55.CFrame = CFrame.lookAt(v55.Position, v55.Position + v59)
                end
                if os.clock() - v_u_52 > 0.05 then
                    v_u_52 = os.clock()
                    v_u_10.Aim:FireServer(v56)
                end
            else
                v_u_53:Disconnect()
                v_u_47 = false
            end
        end)
    end
end
local v_u_61 = setmetatable({}, {
    ["__mode"] = "k"
})
local function v_u_69(p62) -- name: hookTool
    -- upvalues: (copy) v_u_61, (copy) v_u_6, (copy) v_u_11, (ref) v_u_60, (copy) v_u_40, (copy) v_u_17, (copy) v_u_23, (copy) v_u_46, (copy) v_u_9, (copy) v_u_10
    if p62:IsA("Tool") and not v_u_61[p62] then
        v_u_61[p62] = true
        p62.Activated:Connect(function()
            -- upvalues: (ref) v_u_6, (ref) v_u_11, (ref) v_u_60, (ref) v_u_40, (ref) v_u_17, (ref) v_u_23, (ref) v_u_46, (ref) v_u_9, (ref) v_u_10
            local v63 = v_u_6.Get(v_u_11:GetAttribute("EquippedAttack"))
            if v63 and (v63.Kind == "Laser" or v63.Kind == "Plasma") and v_u_11:GetAttribute("InFFA") then
                local v64 = v_u_60
                local v65
                if v63.Kind == "Plasma" then
                    v65 = v_u_40 or nil
                else
                    v65 = nil
                end
                v64(v63, v65)
            end
            local v66 = v_u_17
            if v63 and v63.Kind == "Laser" then
                v66 = v_u_23
            elseif v63 and (v63.Kind == "Hammer" or v63.Kind == "Plasma") then
                v66 = v_u_40
            elseif v63 and v63.Kind == "Orbital" then
                v66 = v_u_46
            end
            if v_u_11.Character and v_u_11.Character:GetAttribute("AimMouse") then
                v66 = v_u_46
            end
            local v67, v68 = v_u_9.click(v63)
            if v67 ~= false then
                v_u_10.Attack:FireServer(v66(), v67, v68)
            end
        end)
    end
end
v_u_11.CharacterAdded:Connect(function(p70)
    -- upvalues: (copy) v_u_61, (copy) v_u_6, (copy) v_u_11, (ref) v_u_60, (copy) v_u_40, (copy) v_u_17, (copy) v_u_23, (copy) v_u_46, (copy) v_u_9, (copy) v_u_10, (copy) v_u_69
    for _, v71 in p70:GetChildren() do
        if not v71:IsA("Tool") then
            continue
        end
        if v_u_61[v71] then
            continue
        end
        v_u_61[v71] = true
        v71.Activated:Connect(function()
            -- upvalues: (ref) v_u_6, (ref) v_u_11, (ref) v_u_60, (ref) v_u_40, (ref) v_u_17, (ref) v_u_23, (ref) v_u_46, (ref) v_u_9, (ref) v_u_10
            local v72 = v_u_6.Get(v_u_11:GetAttribute("EquippedAttack"))
            if v72 and (v72.Kind == "Laser" or v72.Kind == "Plasma") and v_u_11:GetAttribute("InFFA") then
                local v73 = v_u_60
                local v74
                if v72.Kind == "Plasma" then
                    v74 = v_u_40 or nil
                else
                    v74 = nil
                end
                v73(v72, v74)
            end
            local v75 = v_u_17
            if v72 and v72.Kind == "Laser" then
                v75 = v_u_23
            elseif v72 and (v72.Kind == "Hammer" or v72.Kind == "Plasma") then
                v75 = v_u_40
            elseif v72 and v72.Kind == "Orbital" then
                v75 = v_u_46
            end
            if v_u_11.Character and v_u_11.Character:GetAttribute("AimMouse") then
                v75 = v_u_46
            end
            local v76, v77 = v_u_9.click(v72)
            if v76 ~= false then
                v_u_10.Attack:FireServer(v75(), v76, v77)
            end
        end)
    end
    p70.ChildAdded:Connect(v_u_69)
    local v78 = v_u_11:WaitForChild("Backpack")
    for _, v79 in v78:GetChildren() do
        if not v79:IsA("Tool") then
            continue
        end
        if v_u_61[v79] then
            continue
        end
        v_u_61[v79] = true
        v79.Activated:Connect(function()
            -- upvalues: (ref) v_u_6, (ref) v_u_11, (ref) v_u_60, (ref) v_u_40, (ref) v_u_17, (ref) v_u_23, (ref) v_u_46, (ref) v_u_9, (ref) v_u_10
            local v80 = v_u_6.Get(v_u_11:GetAttribute("EquippedAttack"))
            if v80 and (v80.Kind == "Laser" or v80.Kind == "Plasma") and v_u_11:GetAttribute("InFFA") then
                local v81 = v_u_60
                local v82
                if v80.Kind == "Plasma" then
                    v82 = v_u_40 or nil
                else
                    v82 = nil
                end
                v81(v80, v82)
            end
            local v83 = v_u_17
            if v80 and v80.Kind == "Laser" then
                v83 = v_u_23
            elseif v80 and (v80.Kind == "Hammer" or v80.Kind == "Plasma") then
                v83 = v_u_40
            elseif v80 and v80.Kind == "Orbital" then
                v83 = v_u_46
            end
            if v_u_11.Character and v_u_11.Character:GetAttribute("AimMouse") then
                v83 = v_u_46
            end
            local v84, v85 = v_u_9.click(v80)
            if v84 ~= false then
                v_u_10.Attack:FireServer(v83(), v84, v85)
            end
        end)
    end
    v78.ChildAdded:Connect(v_u_69)
end)
local v_u_86
if v_u_11.Character then
    local v87 = v_u_11.Character
    v_u_86 = v_u_60
    for _, v88 in v87:GetChildren() do
        if not v88:IsA("Tool") then
            continue
        end
        if v_u_61[v88] then
            continue
        end
        v_u_61[v88] = true
        v88.Activated:Connect(function()
            -- upvalues: (copy) v_u_6, (copy) v_u_11, (ref) v_u_86, (copy) v_u_40, (copy) v_u_17, (copy) v_u_23, (copy) v_u_46, (copy) v_u_9, (copy) v_u_10
            local v89 = v_u_6.Get(v_u_11:GetAttribute("EquippedAttack"))
            if v89 and (v89.Kind == "Laser" or v89.Kind == "Plasma") and v_u_11:GetAttribute("InFFA") then
                local v90 = v_u_86
                local v91
                if v89.Kind == "Plasma" then
                    v91 = v_u_40 or nil
                else
                    v91 = nil
                end
                v90(v89, v91)
            end
            local v92 = v_u_17
            if v89 and v89.Kind == "Laser" then
                v92 = v_u_23
            elseif v89 and (v89.Kind == "Hammer" or v89.Kind == "Plasma") then
                v92 = v_u_40
            elseif v89 and v89.Kind == "Orbital" then
                v92 = v_u_46
            end
            if v_u_11.Character and v_u_11.Character:GetAttribute("AimMouse") then
                v92 = v_u_46
            end
            local v93, v94 = v_u_9.click(v89)
            if v93 ~= false then
                v_u_10.Attack:FireServer(v92(), v93, v94)
            end
        end)
    end
    v87.ChildAdded:Connect(v_u_69)
else
    v_u_86 = v_u_60
end
local v95 = v_u_11:WaitForChild("Backpack")
for _, v96 in v95:GetChildren() do
    if not v96:IsA("Tool") then
        continue
    end
    if v_u_61[v96] then
        continue
    end
    v_u_61[v96] = true
    v96.Activated:Connect(function()
        -- upvalues: (copy) v_u_6, (copy) v_u_11, (ref) v_u_86, (copy) v_u_40, (copy) v_u_17, (copy) v_u_23, (copy) v_u_46, (copy) v_u_9, (copy) v_u_10
        local v97 = v_u_6.Get(v_u_11:GetAttribute("EquippedAttack"))
        if v97 and (v97.Kind == "Laser" or v97.Kind == "Plasma") and v_u_11:GetAttribute("InFFA") then
            local v98 = v_u_86
            local v99
            if v97.Kind == "Plasma" then
                v99 = v_u_40 or nil
            else
                v99 = nil
            end
            v98(v97, v99)
        end
        local v100 = v_u_17
        if v97 and v97.Kind == "Laser" then
            v100 = v_u_23
        elseif v97 and (v97.Kind == "Hammer" or v97.Kind == "Plasma") then
            v100 = v_u_40
        elseif v97 and v97.Kind == "Orbital" then
            v100 = v_u_46
        end
        if v_u_11.Character and v_u_11.Character:GetAttribute("AimMouse") then
            v100 = v_u_46
        end
        local v101, v102 = v_u_9.click(v97)
        if v101 ~= false then
            v_u_10.Attack:FireServer(v100(), v101, v102)
        end
    end)
end
v95.ChildAdded:Connect(v_u_69)
v_u_7.onInput(function(p103, p104)
    -- upvalues: (copy) v_u_6, (copy) v_u_11, (ref) v_u_86, (copy) v_u_28, (copy) v_u_10, (copy) v_u_23, (copy) v_u_46, (copy) v_u_17
    if not p104 then
        if p103.KeyCode == Enum.KeyCode.E then
            local v105 = v_u_6.Get(v_u_11:GetAttribute("EquippedAttack"))
            if v105 and v105.Ability then
                local v106 = v105.Ability
                if v106.Aim == "Level" then
                    if v_u_11:GetAttribute("InFFA") and ((v_u_11:GetAttribute("AbilityReadyAt") or 0) <= workspace:GetServerTimeNow() and (not v106.MinPhase or (v_u_11:GetAttribute("AttackPhase") or 1) >= v106.MinPhase)) then
                        v_u_86(v106, v_u_28, (v106.Windup or 0) + ((v106.Count or 1) - 1) * (v106.Gap or 0) + 0.1)
                    end
                    v_u_10.Ability:FireServer((v_u_28()))
                    return
                end
                local v107 = v105.Ability.Kind == "NinjaDash" and true or v105.Ability.Kind == "PhaseDash"
                v_u_10.Ability:FireServer(v107 and v_u_23() or (v105.Ability.Kind == "OrbitalBarrage" and v_u_46() or v_u_17()))
            end
        end
    end
end)
local v_u_108 = Instance.new("ScreenGui")
v_u_108.Name = "CombatHUD"
v_u_108.ResetOnSpawn = false
v_u_108.IgnoreGuiInset = true
v_u_108.Parent = v_u_11:WaitForChild("PlayerGui")
local v_u_109 = Instance.new("Frame")
v_u_109.AnchorPoint = Vector2.new(0.5, 1)
v_u_109.Position = UDim2.new(0.5, 0, 1, -90)
v_u_109.Size = UDim2.fromOffset(0, 40)
v_u_109.AutomaticSize = Enum.AutomaticSize.X
v_u_109.Name = "ChipBar"
v_u_109.BackgroundTransparency = 1
v_u_109.Parent = v_u_108
local v_u_110 = Instance.new("UIListLayout")
v_u_110.FillDirection = Enum.FillDirection.Horizontal
v_u_110.Padding = UDim.new(0, 10)
v_u_110.HorizontalAlignment = Enum.HorizontalAlignment.Center
v_u_110.Parent = v_u_109
local function v117(p111) -- name: chip
    -- upvalues: (copy) v_u_109, (copy) v_u_7
    local v112 = Instance.new("TextLabel")
    v112.Size = UDim2.fromOffset(170, 40)
    v112.BackgroundColor3 = Color3.fromRGB(45, 45, 45)
    v112.TextColor3 = Color3.new(1, 1, 1)
    v112.Font = Enum.Font.Arcade
    v112.TextSize = 20
    v112.Text = p111
    local v113 = Instance.new("UICorner")
    v113.CornerRadius = UDim.new(0, 8)
    v113.Parent = v112
    local v114 = Instance.new("UIStroke")
    v114.Thickness = 3
    v114.Color = Color3.fromRGB(20, 20, 20)
    v114.ApplyStrokeMode = Enum.ApplyStrokeMode.Border
    v114.Parent = v112
    local v115 = Instance.new("Frame")
    v115.Name = "Cooldown"
    v115.BackgroundColor3 = Color3.new(0, 0, 0)
    v115.BackgroundTransparency = 0.45
    v115.BorderSizePixel = 0
    v115.Size = UDim2.fromScale(0, 1)
    local v116 = Instance.new("UICorner")
    v116.CornerRadius = UDim.new(0, 8)
    v116.Parent = v115
    v115.Parent = v112
    v112.Parent = v_u_109
    v_u_7.watchText(v112)
    return v112
end
local v_u_118 = {}
local function v_u_123(p119, p120) -- name: fitSize
    -- upvalues: (copy) v_u_118
    local v121 = p119 .. "|" .. p120
    if v_u_118[v121] then
        return v_u_118[v121]
    end
    local v122 = game:GetService("TextService")
    while p120 > 9 and v122:GetTextSize(p119, p120, Enum.Font.Arcade, Vector2.new(10000, 100)).X > 158 do
        p120 = p120 - 1
    end
    v_u_118[v121] = p120
    return p120
end
local v_u_124 = v117("[E] Shockwave")
local v_u_125 = v117("USES 3/3")
v_u_125.LayoutOrder = 3
v_u_125.TextColor3 = Color3.fromRGB(215, 170, 255)
v_u_5.RenderStepped:Connect(function()
    -- upvalues: (copy) v_u_6, (copy) v_u_11, (copy) v_u_125
    local v126 = v_u_6.Get(v_u_11:GetAttribute("EquippedAttack"))
    local v127 = v_u_125
    local v128
    if v126 == nil then
        v128 = false
    else
        v128 = v126.Uses ~= nil
    end
    v127.Visible = v128
    if v_u_125.Visible then
        local v129 = v_u_11:GetAttribute("WeaponUses") or v126.Uses
        v_u_125.Text = string.format("USES %d/%d", v129, v126.Uses)
        v_u_125.Cooldown.Size = UDim2.fromScale(1 - v129 / v126.Uses, 1)
    end
end)
v_u_5.RenderStepped:Connect(function()
    -- upvalues: (copy) v_u_6, (copy) v_u_11, (copy) v_u_124, (copy) v_u_123
    local v130 = v_u_6.Get(v_u_11:GetAttribute("EquippedAttack"))
    local v131 = v_u_124
    local v132
    if v130 == nil or v130.Ability == nil then
        v132 = false
    else
        local v133 = v130.Ability
        local v134 = v133 ~= nil and v133.HideWhileLocked
        if v134 then
            if v133.MinPhase == nil then
                v134 = false
            else
                v134 = (v_u_11:GetAttribute("AttackPhase") or 1) < v133.MinPhase
            end
        end
        v132 = not v134
    end
    v131.Visible = v132
    if v_u_124.Visible then
        local v135 = (v_u_11:GetAttribute("AbilityReadyAt") or 0) - workspace:GetServerTimeNow()
        local v136 = v_u_124.Cooldown
        local v137 = UDim2.fromScale
        local v138 = v135 / (v_u_11:GetAttribute("AbilityCooldown") or v130.Ability.Cooldown)
        v136.Size = v137(math.clamp(v138, 0, 1), 1)
        local v139 = (v_u_11:GetAttribute("EnragedUntil") or 0) - workspace:GetServerTimeNow()
        if v_u_11:GetAttribute("Enraged") and v139 > 0 then
            v_u_124.Text = string.format("RAGE! %.0fs", v139)
        elseif (v_u_11:GetAttribute("CounterUntil") or 0) > workspace:GetServerTimeNow() then
            v_u_124.Text = "COUNTER!"
        else
            v_u_124.Text = v135 > 0 and string.format("[E] %.1fs", v135) or "[E] " .. (v_u_11:GetAttribute("AbilityLabel") or (v130.Ability.Label or v130.Name))
        end
        v_u_124.TextSize = #v_u_124.Text > 15 and v_u_123(v_u_124.Text, 16) or 20
    end
end)
local v_u_140 = Instance.new("TextLabel")
v_u_140.Name = "SafeZoneLabel"
v_u_140.AnchorPoint = Vector2.new(0.5, 1)
v_u_140.Position = UDim2.new(0.5, 0, 1, -140)
v_u_140.Size = UDim2.fromOffset(420, 34)
v_u_140.BackgroundColor3 = Color3.fromRGB(30, 60, 35)
v_u_140.BackgroundTransparency = 0.15
v_u_140.TextColor3 = Color3.fromRGB(120, 255, 120)
v_u_140.Font = Enum.Font.Arcade
v_u_140.TextSize = 20
v_u_140.Text = "SAFE ZONE - jump down to fight!"
local v141 = Instance.new("UICorner")
v141.CornerRadius = UDim.new(0, 8)
v141.Parent = v_u_140
local v142 = Instance.new("UIStroke")
v142.Thickness = 3
v142.Color = Color3.fromRGB(15, 30, 15)
v142.ApplyStrokeMode = Enum.ApplyStrokeMode.Border
v142.Parent = v_u_140
local v_u_143 = Instance.new("UIScale")
v_u_143.Parent = v_u_140
v_u_140.Parent = v_u_108
v_u_11:GetAttributeChangedSignal("InFFA"):Connect(function() -- name: refreshSafe
    -- upvalues: (copy) v_u_140, (copy) v_u_11
    v_u_140.Visible = v_u_11:GetAttribute("InFFA") ~= true
end)
v_u_140.Visible = v_u_11:GetAttribute("InFFA") ~= true
local function v_u_144() -- name: nope
    -- upvalues: (copy) v_u_140, (copy) v_u_143, (copy) v_u_4
    if v_u_140.Visible then
        v_u_143.Scale = 1.15
        v_u_4:Create(v_u_143, TweenInfo.new(0.2, Enum.EasingStyle.Back), {
            ["Scale"] = 1
        }):Play()
    end
end
local function v_u_146(p145) -- name: hookNope
    -- upvalues: (copy) v_u_144
    if p145:IsA("Tool") then
        p145.Activated:Connect(v_u_144)
    end
end
for _, v147 in v_u_11:WaitForChild("Backpack"):GetChildren() do
    if not v147:IsA("Tool") then
        continue
    end
    v147.Activated:Connect(v_u_144)
end
v_u_11.Backpack.ChildAdded:Connect(v_u_146)
v_u_11.CharacterAdded:Connect(function()
    -- upvalues: (copy) v_u_11, (copy) v_u_144, (copy) v_u_146
    local v148 = v_u_11:WaitForChild("Backpack")
    for _, v149 in v148:GetChildren() do
        if not v149:IsA("Tool") then
            continue
        end
        v149.Activated:Connect(v_u_144)
    end
    v148.ChildAdded:Connect(v_u_146)
end)
v_u_7.onInput(function(p150, p151)
    -- upvalues: (copy) v_u_144
    if not p151 and p150.KeyCode == Enum.KeyCode.E then
        v_u_144()
    end
end)
local v_u_152 = v117("[Q] Brain 0/2")
v_u_152.LayoutOrder = 5
local v_u_153 = Instance.new("ColorCorrectionEffect")
v_u_153.Name = "MaxRageTint"
v_u_153.Parent = game:GetService("Lighting")
local v_u_154 = Color3.fromRGB(255, 215, 160)
local v_u_155 = 0
local function v_u_158(p156) -- name: setTint
    -- upvalues: (ref) v_u_155, (copy) v_u_153, (copy) v_u_154
    v_u_155 = v_u_155 + (p156 - v_u_155) * 0.08
    local v157 = p156 - v_u_155
    if math.abs(v157) < 0.002 then
        v_u_155 = p156
    end
    v_u_153.TintColor = Color3.new(1, 1, 1):Lerp(v_u_154, v_u_155)
    v_u_153.Saturation = v_u_155 * 0.15
    v_u_153.Contrast = v_u_155 * 0.05
end
local v_u_159 = Instance.new("Frame")
v_u_159.Name = "RageMeterBar"
v_u_159.AnchorPoint = Vector2.new(0.5, 1)
v_u_159.Position = UDim2.new(0.5, 0, 1, -182)
v_u_159.Size = UDim2.fromOffset(360, 30)
v_u_159.BackgroundColor3 = Color3.fromRGB(35, 30, 20)
local v160 = Instance.new("UICorner")
v160.CornerRadius = UDim.new(0, 8)
v160.Parent = v_u_159
local v161 = Instance.new("UIStroke")
v161.Thickness = 3
v161.Color = Color3.fromRGB(20, 15, 5)
v161.ApplyStrokeMode = Enum.ApplyStrokeMode.Border
v161.Parent = v_u_159
local v_u_162 = Instance.new("Frame")
v_u_162.Name = "Fill"
v_u_162.BorderSizePixel = 0
v_u_162.Size = UDim2.fromScale(0, 1)
v_u_162.BackgroundColor3 = Color3.fromRGB(255, 200, 40)
local v163 = Instance.new("UICorner")
v163.CornerRadius = UDim.new(0, 8)
v163.Parent = v_u_162
local v_u_164 = Instance.new("UIGradient")
v_u_164.Color = ColorSequence.new(Color3.fromRGB(255, 120, 30), Color3.fromRGB(255, 235, 90))
v_u_164.Parent = v_u_162
v_u_162.Parent = v_u_159
local v_u_165 = Instance.new("TextLabel")
v_u_165.BackgroundTransparency = 1
v_u_165.Size = UDim2.fromScale(1, 1)
v_u_165.ZIndex = 3
v_u_165.Font = Enum.Font.Arcade
v_u_165.TextSize = 18
v_u_165.TextColor3 = Color3.new(1, 1, 1)
v_u_165.Text = "RAGE 0%"
local v166 = Instance.new("UIStroke")
v166.Thickness = 1.5
v166.Parent = v_u_165
v_u_165.Parent = v_u_159
v_u_159.Parent = v_u_108
v_u_7.onInput(function(p167, p168)
    -- upvalues: (copy) v_u_6, (copy) v_u_11, (copy) v_u_17, (copy) v_u_23, (ref) v_u_86, (copy) v_u_10
    if p168 or p167.KeyCode ~= Enum.KeyCode.Q then
        return
    else
        local v169 = v_u_6.Get(v_u_11:GetAttribute("EquippedAttack"))
        if v169 and v169.Ability2 then
            local v170 = v169.Ability2
            local v171
            if v170 == nil or v170.MinLevel == nil then
                v171 = false
            else
                local v172 = v_u_11.Character
                v171 = (v172 and (v172:GetAttribute(v170.LevelAttr or "Level") or 1) or 1) < v170.MinLevel
            end
            if not v171 then
                local v173 = v170.Aim == "Look" and v_u_17 or v_u_23
                if v170.AimWhileCharging and (v170.Charge and (v_u_11:GetAttribute("InFFA") and (v_u_11:GetAttribute("Ability2ReadyAt") or 0) <= workspace:GetServerTimeNow())) then
                    v_u_86(v170, v173)
                end
                v_u_10.Ability2:FireServer(v173())
            end
        else
            local v174 = v169 and v169.Phases
            if v174 then
                v174 = v169.Phases[v_u_11:GetAttribute("VerityPhase") or 1]
            end
            if v174 and v174.Brain then
                v_u_10.Ability2:FireServer()
            end
            return
        end
    end
end)
local v_u_175 = v117("[Q] Explode")
v_u_175.LayoutOrder = 4
v_u_5.RenderStepped:Connect(function()
    -- upvalues: (copy) v_u_6, (copy) v_u_11, (copy) v_u_175, (copy) v_u_123
    local v176 = v_u_6.Get(v_u_11:GetAttribute("EquippedAttack"))
    if v176 then
        v176 = v176.Ability2
    end
    local v177 = v_u_175
    local v178
    if v176 == nil then
        v178 = false
    else
        local v179 = v176 ~= nil and v176.HideWhileLocked
        if v179 then
            if v176.MinPhase == nil then
                v179 = false
            else
                v179 = (v_u_11:GetAttribute("AttackPhase") or 1) < v176.MinPhase
            end
        end
        v178 = not v179
    end
    v177.Visible = v178
    if v_u_175.Visible then
        local v180 = workspace:GetServerTimeNow()
        local v181 = (v_u_11:GetAttribute("ExplodeUntil") or 0) - v180
        if v181 > 0 then
            v_u_175.Text = string.format("BOOM IN %.1f", v181)
            v_u_175.TextColor3 = Color3.fromRGB(255, 90, 60)
            v_u_175.Cooldown.Size = UDim2.fromScale(0, 1)
        else
            local v182
            if v176 == nil or v176.MinLevel == nil then
                v182 = false
            else
                local v183 = v_u_11.Character
                v182 = (v183 and (v183:GetAttribute(v176.LevelAttr or "Level") or 1) or 1) < v176.MinLevel
            end
            if v182 then
                v_u_175.Text = string.format("[Q] LV %d", v176.MinLevel)
                v_u_175.TextColor3 = Color3.fromRGB(150, 150, 150)
                v_u_175.Cooldown.Size = UDim2.fromScale(1, 1)
            else
                local v184 = (v_u_11:GetAttribute("Ability2ReadyAt") or 0) - v180
                v_u_175.TextColor3 = Color3.new(1, 1, 1)
                local v185 = v_u_175.Cooldown
                local v186 = UDim2.fromScale
                local v187 = v184 / (v_u_11:GetAttribute("Ability2Cooldown") or v176.Cooldown)
                v185.Size = v186(math.clamp(v187, 0, 1), 1)
                v_u_175.Text = v184 > 0 and string.format("[Q] %.1fs", v184) or "[Q] " .. (v_u_11:GetAttribute("Ability2Label") or (v176.Label or "Ability"))
            end
        end
        v_u_175.TextSize = #v_u_175.Text > 15 and v_u_123(v_u_175.Text, 16) or 20
    end
end)
local v_u_188 = v_u_10:WaitForChild("Ability3", 10)
v_u_7.onInput(function(p189, p190)
    -- upvalues: (ref) v_u_188, (copy) v_u_10, (copy) v_u_6, (copy) v_u_11, (copy) v_u_144, (ref) v_u_86, (copy) v_u_17, (copy) v_u_23, (copy) v_u_46
    if p190 or p189.UserInputType ~= Enum.UserInputType.Keyboard then
        return
    else
        v_u_188 = v_u_188 or v_u_10:FindFirstChild("Ability3")
        if v_u_188 then
            local v191 = v_u_6.Get(v_u_11:GetAttribute("EquippedAttack"))
            if v191 then
                v191 = v191.Ability3
            end
            if v191 and p189.KeyCode.Name == (v191.Key or "V") then
                if v_u_11:GetAttribute("InFFA") then
                    if v191.MinPhase and (v_u_11:GetAttribute("AttackPhase") or 1) < v191.MinPhase then
                        return
                    else
                        local v192
                        if v191 == nil or v191.MinLevel == nil then
                            v192 = false
                        else
                            local v193 = v_u_11.Character
                            v192 = (v193 and (v193:GetAttribute(v191.LevelAttr or "Level") or 1) or 1) < v191.MinLevel
                        end
                        if not v192 then
                            if v191.AimWhileCharging and (v191.Charge and (v_u_11:GetAttribute("Ability3ReadyAt") or 0) <= workspace:GetServerTimeNow()) then
                                v_u_86(v191, v191.Aim == "Look" and v_u_17 or v_u_23)
                            end
                            v_u_188:FireServer(v191.Aim == "Mouse" and v_u_46() or v_u_23())
                        end
                    end
                else
                    v_u_144()
                    return
                end
            else
                return
            end
        else
            return
        end
    end
end)
task.spawn(function()
    -- upvalues: (copy) v_u_10, (copy) v_u_46
    local v194 = v_u_10:WaitForChild("SukunaFX", 30)
    if v194 then
        v194.OnClientEvent:Connect(function(p195, p196)
            -- upvalues: (ref) v_u_10, (ref) v_u_46
            if p195 == "Steer" then
                local v_u_197 = os.clock() + (tonumber(p196) or 3)
                task.spawn(function()
                    -- upvalues: (copy) v_u_197, (ref) v_u_10, (ref) v_u_46
                    while os.clock() < v_u_197 do
                        v_u_10.Aim:FireServer((v_u_46()))
                        task.wait(0.05)
                    end
                end)
            end
        end)
    end
end)
local v_u_198 = v117("[V] Ability")
v_u_198.LayoutOrder = 5
local v_u_199 = v_u_10:WaitForChild("Ability4", 10)
v_u_7.onInput(function(p200, p201)
    -- upvalues: (ref) v_u_199, (copy) v_u_10, (copy) v_u_6, (copy) v_u_11, (copy) v_u_144, (copy) v_u_17
    if p201 or p200.UserInputType ~= Enum.UserInputType.Keyboard then
        return
    else
        v_u_199 = v_u_199 or v_u_10:FindFirstChild("Ability4")
        if v_u_199 then
            local v202 = v_u_6.Get(v_u_11:GetAttribute("EquippedAttack"))
            if v202 then
                v202 = v202.Ability4
            end
            if v202 and p200.KeyCode.Name == (v202.Key or "X") then
                if p200.KeyCode == Enum.KeyCode.X and v_u_11:GetAttribute("RollConfirmOpen") then
                    return
                elseif v_u_11:GetAttribute("InFFA") then
                    if v202.MinPhase and (v_u_11:GetAttribute("AttackPhase") or 1) < v202.MinPhase then
                        return
                    else
                        local v203
                        if v202 == nil or v202.MinLevel == nil then
                            v203 = false
                        else
                            local v204 = v_u_11.Character
                            v203 = (v204 and (v204:GetAttribute(v202.LevelAttr or "Level") or 1) or 1) < v202.MinLevel
                        end
                        if not v203 then
                            v_u_199:FireServer((v_u_17()))
                        end
                    end
                else
                    v_u_144()
                    return
                end
            else
                return
            end
        else
            return
        end
    end
end)
local v_u_205 = v117("[X] Ability")
v_u_205.LayoutOrder = 6
local v_u_206 = v_u_10:WaitForChild("Ability5", 10)
v_u_7.onInput(function(p207, p208)
    -- upvalues: (ref) v_u_206, (copy) v_u_10, (copy) v_u_6, (copy) v_u_11, (copy) v_u_144, (copy) v_u_46, (copy) v_u_17
    if p208 or p207.UserInputType ~= Enum.UserInputType.Keyboard then
        return
    else
        v_u_206 = v_u_206 or v_u_10:FindFirstChild("Ability5")
        if v_u_206 then
            local v209 = v_u_6.Get(v_u_11:GetAttribute("EquippedAttack"))
            if v209 then
                v209 = v209.Ability5
            end
            if v209 and p207.KeyCode.Name == (v209.Key or "C") then
                if v_u_11:GetAttribute("InFFA") then
                    if v209.MinPhase and (v_u_11:GetAttribute("AttackPhase") or 1) < v209.MinPhase then
                        return
                    else
                        local v210
                        if v209 == nil or v209.MinLevel == nil then
                            v210 = false
                        else
                            local v211 = v_u_11.Character
                            v210 = (v211 and (v211:GetAttribute(v209.LevelAttr or "Level") or 1) or 1) < v209.MinLevel
                        end
                        if not v210 then
                            v_u_206:FireServer(v209.Aim == "Mouse" and v_u_46() or v_u_17())
                        end
                    end
                else
                    v_u_144()
                    return
                end
            else
                return
            end
        else
            return
        end
    end
end)
local v_u_212 = v117("[C] Ability")
v_u_212.LayoutOrder = 7
local v_u_213 = v_u_10:WaitForChild("Ability6", 10)
v_u_7.onInput(function(p214, p215)
    -- upvalues: (ref) v_u_213, (copy) v_u_10, (copy) v_u_6, (copy) v_u_11, (copy) v_u_144, (copy) v_u_46, (copy) v_u_17
    if p215 or p214.UserInputType ~= Enum.UserInputType.Keyboard then
        return
    else
        v_u_213 = v_u_213 or v_u_10:FindFirstChild("Ability6")
        if v_u_213 then
            local v216 = v_u_6.Get(v_u_11:GetAttribute("EquippedAttack"))
            if v216 then
                v216 = v216.Ability6
            end
            if v216 and p214.KeyCode.Name == (v216.Key or "Z") then
                if p214.KeyCode == Enum.KeyCode.X and v_u_11:GetAttribute("RollConfirmOpen") then
                    return
                elseif v_u_11:GetAttribute("InFFA") then
                    if v216.MinPhase and (v_u_11:GetAttribute("AttackPhase") or 1) < v216.MinPhase then
                        return
                    else
                        local v217
                        if v216 == nil or v216.MinLevel == nil then
                            v217 = false
                        else
                            local v218 = v_u_11.Character
                            v217 = (v218 and (v218:GetAttribute(v216.LevelAttr or "Level") or 1) or 1) < v216.MinLevel
                        end
                        if not v217 then
                            v_u_213:FireServer(v216.Aim == "Mouse" and v_u_46() or v_u_17())
                        end
                    end
                else
                    v_u_144()
                    return
                end
            else
                return
            end
        else
            return
        end
    end
end)
local v_u_219 = v117("[Z] Ability")
v_u_219.LayoutOrder = 8
local v_u_220 = v_u_10:WaitForChild("Ability7", 10)
v_u_7.onInput(function(p221, p222)
    -- upvalues: (ref) v_u_220, (copy) v_u_10, (copy) v_u_6, (copy) v_u_11, (copy) v_u_144, (copy) v_u_46, (copy) v_u_17
    if p222 or p221.UserInputType ~= Enum.UserInputType.Keyboard then
        return
    else
        v_u_220 = v_u_220 or v_u_10:FindFirstChild("Ability7")
        if v_u_220 then
            local v223 = v_u_6.Get(v_u_11:GetAttribute("EquippedAttack"))
            if v223 then
                v223 = v223.Ability7
            end
            if v223 and p221.KeyCode.Name == (v223.Key or "B") then
                if p221.KeyCode == Enum.KeyCode.X and v_u_11:GetAttribute("RollConfirmOpen") then
                    return
                elseif v_u_11:GetAttribute("InFFA") then
                    if v223.MinPhase and (v_u_11:GetAttribute("AttackPhase") or 1) < v223.MinPhase then
                        return
                    else
                        local v224
                        if v223 == nil or v223.MinLevel == nil then
                            v224 = false
                        else
                            local v225 = v_u_11.Character
                            v224 = (v225 and (v225:GetAttribute(v223.LevelAttr or "Level") or 1) or 1) < v223.MinLevel
                        end
                        if not v224 then
                            v_u_220:FireServer(v223.Aim == "Mouse" and v_u_46() or v_u_17())
                        end
                    end
                else
                    v_u_144()
                    return
                end
            else
                return
            end
        else
            return
        end
    end
end)
local v_u_226 = v117("[B] Ability")
v_u_226.LayoutOrder = 9
local v_u_227 = v117("[M1] Ability")
v_u_227.LayoutOrder = -1
v_u_5.RenderStepped:Connect(function()
    -- upvalues: (copy) v_u_6, (copy) v_u_11, (copy) v_u_227
    local v228 = v_u_6.Get(v_u_11:GetAttribute("EquippedAttack"))
    local v229 = v228 and v228.HitCooldown and ((v_u_11:GetAttribute("AttackReadyAt") or 0) - workspace:GetServerTimeNow() or 0) or 0
    v_u_227.Visible = v229 > 0.6
    if v_u_227.Visible then
        local v230 = v_u_227.Cooldown
        local v231 = UDim2.fromScale
        local v232 = v229 / (v_u_11:GetAttribute("AttackCooldown") or v228.HitCooldown)
        v230.Size = v231(math.clamp(v232, 0, 1), 1)
        v_u_227.Text = string.format("[M1] %.1fs", v229)
        v_u_227.TextColor3 = Color3.new(1, 1, 1)
        v_u_227.TextSize = 20
    end
end)
local function v_u_263(p233, p234, p235, p236, p237) -- name: extraChip
    -- upvalues: (copy) v_u_11, (copy) v_u_123
    local v238
    if p234 == nil then
        v238 = false
    else
        local v239 = p234 ~= nil and p234.HideWhileLocked
        if v239 then
            if p234.MinPhase == nil then
                v239 = false
            else
                v239 = (v_u_11:GetAttribute("AttackPhase") or 1) < p234.MinPhase
            end
        end
        v238 = not v239
    end
    p233.Visible = v238
    if not p233.Visible then
        return
    end
    local v240 = p233:GetAttribute("BaseOrder") or p233.LayoutOrder
    p233:SetAttribute("BaseOrder", v240)
    local v241 = p234.ChipOrder or v240
    if p233.LayoutOrder ~= v241 then
        p233.LayoutOrder = v241
    end
    local v242 = p234.Key or p237
    local v243 = nil
    local v244 = p234.Mode and (p234.Mode.Label and (v_u_11:GetAttribute("AttackPhase") or 1) < 2) and p234.Mode.Label or p234.Label
    local v245 = v_u_11:GetAttribute(string.gsub(p235, "ReadyAt$", "") .. "Label") or v244
    local v246 = p234.ArmedAttr
    if v246 then
        v246 = (v_u_11:GetAttribute(p234.ArmedAttr) or 0) - workspace:GetServerTimeNow()
    end
    local v247 = p234.ActiveAttr
    if v247 then
        v247 = (v_u_11:GetAttribute(p234.ActiveAttr) or 0) - workspace:GetServerTimeNow()
    end
    local v248, v249
    if p234.MinPhase and (v_u_11:GetAttribute("AttackPhase") or 1) < p234.MinPhase then
        v248 = p234.LockedLabel and string.format("[%s] %s", v242, p234.LockedLabel) or string.format("[%s] PHASE %d", v242, p234.MinPhase)
        v243 = Color3.fromRGB(150, 150, 150)
        v249 = 1
        p233.Cooldown.Size = UDim2.fromScale(v249, 1)
        p233.TextColor3 = v243 or Color3.new(1, 1, 1)
        p233.Text = v248
        p233.TextSize = #v248 > 15 and v_u_123(v248, 16) or 20
        return
    else
        local v250
        if p234 == nil or p234.MinLevel == nil then
            v250 = false
        else
            local v251 = v_u_11.Character
            v250 = (v251 and (v251:GetAttribute(p234.LevelAttr or "Level") or 1) or 1) < p234.MinLevel
        end
        if v250 then
            v248 = string.format("[%s] LV %d", v242, p234.MinLevel)
            v243 = Color3.fromRGB(150, 150, 150)
            v249 = 1
            p233.Cooldown.Size = UDim2.fromScale(v249, 1)
            p233.TextColor3 = v243 or Color3.new(1, 1, 1)
            p233.Text = v248
            p233.TextSize = #v248 > 15 and v_u_123(v248, 16) or 20
            return
        elseif v247 and v247 > 0 then
            local v252, v253
            if p234.ActiveLenAttr then
                local v254 = v_u_11
                local v255 = p234.ActiveLenAttr
                v252 = tonumber(v254:GetAttribute(v255))
                if v252 then
                    v248 = string.format("[%s] %s %ds", v242, p234.ActiveLabel or "ACTIVE", (math.ceil(v247)))
                    v253 = v247 / math.max(v252, 0.1)
                    v249 = 1 - math.clamp(v253, 0, 1)
                    v243 = p234.ActiveRainbow and Color3.fromHSV(os.clock() * 0.5 % 1, 0.45, 1) or Color3.fromRGB(255, 220, 120)
                else
                    v252 = p234.Duration or 30
                    v248 = string.format("[%s] %s %ds", v242, p234.ActiveLabel or "ACTIVE", (math.ceil(v247)))
                    v253 = v247 / math.max(v252, 0.1)
                    v249 = 1 - math.clamp(v253, 0, 1)
                    v243 = p234.ActiveRainbow and Color3.fromHSV(os.clock() * 0.5 % 1, 0.45, 1) or Color3.fromRGB(255, 220, 120)
                end
            else
                v252 = p234.Duration or 30
                v248 = string.format("[%s] %s %ds", v242, p234.ActiveLabel or "ACTIVE", (math.ceil(v247)))
                v253 = v247 / math.max(v252, 0.1)
                v249 = 1 - math.clamp(v253, 0, 1)
                v243 = p234.ActiveRainbow and Color3.fromHSV(os.clock() * 0.5 % 1, 0.45, 1) or Color3.fromRGB(255, 220, 120)
            end
            p233.Cooldown.Size = UDim2.fromScale(v249, 1)
            p233.TextColor3 = v243 or Color3.new(1, 1, 1)
            p233.Text = v248
            p233.TextSize = #v248 > 15 and v_u_123(v248, 16) or 20
        else
            if v246 and v246 > 0 then
                v248 = string.format("[%s] %s", v242, p234.ArmedLabel or "AGAIN!")
                local v256 = v246 / (p234.Window or 5)
                v249 = 1 - math.clamp(v256, 0, 1)
                v243 = Color3.fromRGB(120, 255, 240)
            else
                local v257 = (v_u_11:GetAttribute(p235) or 0) - workspace:GetServerTimeNow()
                local v258 = p234.Meter
                local v259 = v258 and (v_u_11:GetAttribute(p234.Meter) or 0) or v258
                if v259 and v259 < 1 then
                    local v260 = string.format
                    local v261 = v259 * 100
                    v248 = v260("[%s] %d%%", v242, (math.floor(v261)))
                    v249 = 1 - v259
                else
                    local v262 = v257 / (v_u_11:GetAttribute(p236) or p234.Cooldown)
                    v249 = math.clamp(v262, 0, 1)
                    v248 = v257 > 0 and string.format("[%s] %.1fs", v242, v257) or "[" .. v242 .. "] " .. (v245 or "Ability")
                end
            end
            p233.Cooldown.Size = UDim2.fromScale(v249, 1)
            p233.TextColor3 = v243 or Color3.new(1, 1, 1)
            p233.Text = v248
            p233.TextSize = #v248 > 15 and v_u_123(v248, 16) or 20
        end
    end
end
v_u_5.RenderStepped:Connect(function()
    -- upvalues: (copy) v_u_6, (copy) v_u_11, (copy) v_u_110, (copy) v_u_263, (copy) v_u_198, (copy) v_u_205, (copy) v_u_212, (copy) v_u_219, (copy) v_u_226
    local v264 = v_u_6.Get(v_u_11:GetAttribute("EquippedAttack"))
    v_u_110.SortOrder = v264 and v264.ChipSort and Enum.SortOrder.LayoutOrder or Enum.SortOrder.Name
    local v265 = v_u_263
    local v266 = v_u_198
    local v267
    if v264 then
        v267 = v264.Ability3
    else
        v267 = v264
    end
    v265(v266, v267, "Ability3ReadyAt", "Ability3Cooldown", "V")
    local v268 = v_u_263
    local v269 = v_u_205
    local v270
    if v264 then
        v270 = v264.Ability4
    else
        v270 = v264
    end
    v268(v269, v270, "Ability4ReadyAt", "Ability4Cooldown", "X")
    local v271 = v_u_263
    local v272 = v_u_212
    local v273
    if v264 then
        v273 = v264.Ability5
    else
        v273 = v264
    end
    v271(v272, v273, "Ability5ReadyAt", "Ability5Cooldown", "C")
    local v274 = v_u_263
    local v275 = v_u_219
    local v276
    if v264 then
        v276 = v264.Ability6
    else
        v276 = v264
    end
    v274(v275, v276, "Ability6ReadyAt", "Ability6Cooldown", "Z")
    local v277 = v_u_263
    local v278 = v_u_226
    if v264 then
        v264 = v264.Ability7
    end
    v277(v278, v264, "Ability7ReadyAt", "Ability7Cooldown", "B")
end)
v_u_5.RenderStepped:Connect(function()
    -- upvalues: (copy) v_u_6, (copy) v_u_11, (copy) v_u_159, (copy) v_u_162, (copy) v_u_165, (copy) v_u_164, (copy) v_u_152, (copy) v_u_123, (copy) v_u_158
    local v279 = v_u_6.Get(v_u_11:GetAttribute("EquippedAttack"))
    local v280 = v_u_11:GetAttribute("VerityPhase") or 1
    local v281 = v279 and v279.Phases
    if v281 then
        v281 = v279.Phases[v280]
    end
    local v282
    if v279 then
        v282 = v279.RageMeter
    else
        v282 = v279
    end
    v_u_159.Visible = v282 ~= nil
    if v_u_159.Visible then
        local v283 = (v_u_11:GetAttribute("VerityRage") or 0) / (v281 and v281.RageMax or v282.Max)
        local v284 = math.clamp(v283, 0, 1)
        v_u_162.Size = UDim2.fromScale(v284, 1)
        local v285 = v280 == #v279.Phases
        local v286
        if v281 then
            local v287 = (v281.Damage or 0) * (v_u_11:GetAttribute("VerityDmgMult") or 1) + 0.5
            v286 = math.floor(v287)
        else
            v286 = v281
        end
        if v285 and v284 >= 1 then
            v_u_165.Text = string.format("MAX RAGE!  %d DMG", v286)
            v_u_164.Color = ColorSequence.new(Color3.fromRGB(255, 40, 20), Color3.fromRGB(255, 160, 40))
        elseif v285 then
            local v288 = v_u_165
            local v289 = string.format
            local v290 = v284 * 100
            v288.Text = v289("RAGE %d%%  -  %d DMG", math.floor(v290), v286)
            v_u_164.Color = ColorSequence.new(Color3.fromRGB(255, 120, 30), Color3.fromRGB(255, 235, 90))
        else
            local v291 = v_u_165
            local v292 = string.format
            local v293 = v284 * 100
            v291.Text = v292("RAGE %d%%  -  PHASE %d", math.floor(v293), v280)
            v_u_164.Color = ColorSequence.new(Color3.fromRGB(255, 120, 30), Color3.fromRGB(255, 235, 90))
        end
    end
    local v294 = v_u_152
    local v295
    if v281 == nil then
        v295 = false
    else
        v295 = v281.Brain ~= nil
    end
    v294.Visible = v295
    if v_u_152.Visible then
        local v296 = (v_u_11:GetAttribute("BiteReadyAt") or 0) - workspace:GetServerTimeNow()
        local v297 = v_u_11:GetAttribute("VerityBrains") or 0
        local v298 = v_u_152.Cooldown
        local v299 = UDim2.fromScale
        local v300
        if v297 > 0 then
            local v301 = v296 / (v281.Brain.Cooldown or 2.4)
            v300 = math.clamp(v301, 0, 1) or 1
        else
            v300 = 1
        end
        v298.Size = v299(v300, 1)
        v_u_152.Text = string.format("[Q] Brain %d/%d", v297, v281.Brain.Max or 2) .. (v296 > 0 and (string.format(" %ds", (math.ceil(v296))) or "") or "")
        v_u_152.TextSize = v296 > 0 and v_u_123("[Q] Brain 0/2 88s", 16) or 20
    end
    local v302 = 0
    if v_u_159.Visible and (v279 and v280 == #v279.Phases) then
        local v303 = (v_u_11:GetAttribute("VerityRage") or 0) / (v281 and v281.RageMax or v282.Max)
        local v304 = math.clamp(v303, 0, 1)
        if v304 >= 0.9 then
            v302 = (v304 - 0.9) * 0.65 / 0.1 + 0.35
        end
    end
    v_u_158(v302)
end)
local v_u_305 = Instance.new("Frame")
v_u_305.Name = "StreakBar"
v_u_305.AnchorPoint = Vector2.new(0.5, 1)
v_u_305.Position = UDim2.new(0.5, 0, 1, -182)
v_u_305.Size = UDim2.fromOffset(360, 30)
v_u_305.BackgroundColor3 = Color3.fromRGB(18, 35, 20)
v_u_305.Visible = false
local v306 = Instance.new("UICorner")
v306.CornerRadius = UDim.new(0, 8)
v306.Parent = v_u_305
local v_u_307 = Instance.new("UIStroke")
v_u_307.Thickness = 3
v_u_307.Color = Color3.fromRGB(5, 20, 8)
v_u_307.ApplyStrokeMode = Enum.ApplyStrokeMode.Border
v_u_307.Parent = v_u_305
local v_u_308 = Instance.new("Frame")
v_u_308.BorderSizePixel = 0
v_u_308.Size = UDim2.fromScale(0, 1)
v_u_308.BackgroundColor3 = Color3.fromRGB(80, 255, 100)
local v309 = Instance.new("UICorner")
v309.CornerRadius = UDim.new(0, 8)
v309.Parent = v_u_308
local v_u_310 = Instance.new("UIGradient")
v_u_310.Color = ColorSequence.new(Color3.fromRGB(40, 190, 70), Color3.fromRGB(150, 255, 150))
v_u_310.Parent = v_u_308
v_u_308.Parent = v_u_305
local v_u_311 = Instance.new("TextLabel")
v_u_311.BackgroundTransparency = 1
v_u_311.Size = UDim2.fromScale(1, 1)
v_u_311.ZIndex = 3
v_u_311.Font = Enum.Font.Arcade
v_u_311.TextSize = 18
v_u_311.TextColor3 = Color3.new(1, 1, 1)
v_u_311.Text = "STREAK"
local v312 = Instance.new("UIStroke")
v312.Thickness = 1.5
v312.Parent = v_u_311
v_u_311.Parent = v_u_305
v_u_305.Parent = v_u_108
v_u_5.RenderStepped:Connect(function()
    -- upvalues: (copy) v_u_6, (copy) v_u_11, (copy) v_u_305, (copy) v_u_310, (copy) v_u_307, (copy) v_u_308, (copy) v_u_311
    local v313 = v_u_6.Get(v_u_11:GetAttribute("EquippedAttack"))
    if v313 then
        v313 = v313.Streak
    end
    v_u_305.Visible = v313 ~= nil
    if v313 then
        local v314 = v_u_11:GetAttribute("StreakPhase") or 1
        local v315 = v_u_11:GetAttribute("StreakHits") or 0
        local v316 = v313.Phases[v314]
        local v317 = v_u_11:GetAttribute("StreakExpire")
        local v318 = v317 and v317 - workspace:GetServerTimeNow() or nil
        local v319
        if v318 then
            v319 = v318 > 0
        else
            v319 = v318
        end
        local v320 = v319 and (string.format("  (%ds)", (math.ceil(v318))) or "") or ""
        if v319 then
            v319 = v318 <= 3
        end
        v_u_310.Color = v319 and ColorSequence.new(Color3.fromRGB(170, 20, 20), Color3.fromRGB(255, 90, 90)) or ColorSequence.new(Color3.fromRGB(40, 190, 70), Color3.fromRGB(150, 255, 150))
        v_u_307.Color = v319 and Color3.fromRGB(90, 0, 0) or Color3.fromRGB(5, 20, 8)
        if v316 and v316.Hits then
            local v321 = v_u_308
            local v322 = UDim2.fromScale
            local v323 = v315 / v316.Hits
            v321.Size = v322(math.clamp(v323, 0, 1), 1)
            v_u_311.Text = string.format("STREAK PHASE %d  -  %d/%d HITS", v314, v315, v316.Hits) .. v320
        else
            v_u_308.Size = UDim2.fromScale(1, 1)
            v_u_311.Text = "MAX STREAK!  PHASE " .. v314 .. v320
        end
    else
        return
    end
end)
local v_u_324 = Instance.new("Frame")
v_u_324.Name = "MangoMeterBar"
v_u_324.AnchorPoint = Vector2.new(0.5, 1)
v_u_324.Position = UDim2.new(0.5, 0, 1, -182)
v_u_324.Size = UDim2.fromOffset(360, 30)
v_u_324.BackgroundColor3 = Color3.fromRGB(45, 28, 12)
v_u_324.Visible = false
v_u_324:SetAttribute("FitOwnPos", true)
local v325 = Instance.new("UICorner")
v325.CornerRadius = UDim.new(0, 8)
v325.Parent = v_u_324
local v_u_326 = Instance.new("UIStroke")
v_u_326.Thickness = 3
v_u_326.Color = Color3.fromRGB(25, 12, 0)
v_u_326.ApplyStrokeMode = Enum.ApplyStrokeMode.Border
v_u_326.Parent = v_u_324
local v_u_327 = Instance.new("Frame")
v_u_327.BorderSizePixel = 0
v_u_327.Size = UDim2.fromScale(0, 1)
local v328 = Instance.new("UICorner")
v328.CornerRadius = UDim.new(0, 8)
v328.Parent = v_u_327
local v_u_329 = Instance.new("UIGradient")
v_u_329.Rotation = 90
v_u_329.Parent = v_u_327
v_u_327.Parent = v_u_324
local v_u_330 = Instance.new("TextLabel")
v_u_330.BackgroundTransparency = 1
v_u_330.Size = UDim2.fromScale(1, 1)
v_u_330.ZIndex = 3
v_u_330.Font = Enum.Font.Arcade
v_u_330.TextSize = 18
v_u_330.TextColor3 = Color3.new(1, 1, 1)
v_u_330.Text = "MANGO 0%"
local v331 = Instance.new("UIStroke")
v331.Thickness = 1.5
v331.Parent = v_u_330
v_u_330.Parent = v_u_324
local v_u_332 = Instance.new("ImageLabel")
v_u_332.AnchorPoint = Vector2.new(0.5, 0.5)
v_u_332.Position = UDim2.new(0, -4, 0.5, 0)
v_u_332.Size = UDim2.fromOffset(54, 54)
v_u_332.BackgroundTransparency = 1
v_u_332.Image = "rbxassetid://106675753781843"
v_u_332.ZIndex = 4
local v_u_333 = Instance.new("UIScale")
v_u_333.Parent = v_u_332
v_u_332.Parent = v_u_324
v_u_324.Parent = v_u_108
local v_u_334 = {
    { 0, Color3.fromRGB(110, 200, 60) },
    { 0.4, Color3.fromRGB(255, 215, 60) },
    { 0.75, Color3.fromRGB(255, 140, 30) },
    { 1, Color3.fromRGB(235, 45, 30) }
}
local function v_u_339(p335) -- name: ripe
    -- upvalues: (copy) v_u_334
    for v336 = 2, #v_u_334 do
        local v337 = v_u_334[v336 - 1]
        local v338 = v_u_334[v336]
        if p335 > v338[1] then
            continue
        end
        return v337[2]:Lerp(v338[2], (p335 - v337[1]) / (v338[1] - v337[1]))
    end
    return v_u_334[#v_u_334][2]
end
local v_u_340 = 0
local v_u_341 = 0
v_u_5.RenderStepped:Connect(function(p342)
    -- upvalues: (copy) v_u_6, (copy) v_u_11, (copy) v_u_324, (ref) v_u_340, (copy) v_u_339, (copy) v_u_327, (copy) v_u_329, (ref) v_u_341, (copy) v_u_3, (copy) v_u_326, (copy) v_u_333, (copy) v_u_332, (copy) v_u_330
    local v343 = v_u_6.Get(v_u_11:GetAttribute("EquippedAttack"))
    if v343 then
        v343 = v343.MangoMeter
    end
    v_u_324.Visible = v343 ~= nil
    if v343 then
        local v344 = v_u_11:GetAttribute("MangoMeter") or 0
        local v345 = math.clamp(v344, 0, 1)
        local v346 = v_u_340
        local v347 = v345 - v_u_340
        local v348 = p342 * 10
        v_u_340 = v346 + v347 * math.min(1, v348)
        local v349 = v_u_339(v_u_340)
        v_u_327.Size = UDim2.fromScale(v_u_340, 1)
        v_u_327.BackgroundColor3 = v349
        v_u_329.Color = ColorSequence.new(v349:Lerp(Color3.new(1, 1, 1), 0.3), v349)
        local v350 = v_u_341 - p342 * 3
        v_u_341 = math.max(0, v350)
        local v351 = os.clock()
        local v352 = (v_u_340 - 0.8) / 0.2
        local v353 = math.clamp(v352, 0, 1)
        local v354 = v353 * 3 + (v345 >= 1 and 4 or 0)
        v_u_324.Position = UDim2.new(0.5, (math.random() - 0.5) * v354, 1, require(v_u_3.ScreenFit).bottomOffset(-182) + (math.random() - 0.5) * v354)
        local v355
        if v353 > 0 then
            local v356 = v351 * (v353 * 10 + 8)
            v355 = (math.sin(v356) * 0.5 + 0.5) * v353 or 0
        else
            v355 = 0
        end
        v_u_326.Color = Color3.fromRGB(25, 12, 0):Lerp(Color3.fromRGB(255, 40, 30), v355):Lerp(Color3.new(1, 1, 1), v_u_341)
        v_u_333.Scale = v_u_340 * 0.12 + 1 + 0.12 * v355 + v_u_341 * 0.3
        local v357 = v_u_332
        local v358 = v351 * 2
        v357.Rotation = math.sin(v358) * 6 + (math.random() - 0.5) * 10 * v353
        if v345 >= 1 then
            v_u_330.Text = "IT\'S GONNA BURST!!"
            return
        elseif v353 > 0 then
            local v359 = v_u_330
            local v360 = string.format
            local v361 = v345 * 100
            v359.Text = v360("MANGO %d%%  -  KILL SOMEONE!", (math.floor(v361)))
        else
            local v362 = v_u_330
            local v363 = string.format
            local v364 = v345 * 100
            v362.Text = v363("MANGO %d%%", (math.floor(v364)))
        end
    else
        v_u_340 = 0
        return
    end
end)
task.spawn(function()
    -- upvalues: (copy) v_u_10, (ref) v_u_341, (copy) v_u_324, (copy) v_u_4
    local v365 = v_u_10:WaitForChild("MangoFX", 30)
    if v365 then
        v365.OnClientEvent:Connect(function(p366, p367)
            -- upvalues: (ref) v_u_341, (ref) v_u_324, (ref) v_u_4
            if p366 == "Drained" and typeof(p367) == "number" then
                v_u_341 = 1
                local v368 = Instance.new("TextLabel")
                v368.AnchorPoint = Vector2.new(0.5, 1)
                v368.Position = UDim2.new(0.5, 0, 0, -4)
                v368.Size = UDim2.fromOffset(160, 30)
                v368.BackgroundTransparency = 1
                v368.Font = Enum.Font.Arcade
                v368.TextSize = 26
                v368.ZIndex = 5
                v368.TextColor3 = Color3.fromRGB(140, 255, 120)
                local v369 = string.format
                local v370 = p367 * 100 + 0.5
                v368.Text = v369("-%d%%", (math.floor(v370)))
                local v371 = Instance.new("UIStroke")
                v371.Thickness = 2
                v371.Parent = v368
                v368.Parent = v_u_324
                v_u_4:Create(v368, TweenInfo.new(0.9, Enum.EasingStyle.Quad, Enum.EasingDirection.Out), {
                    ["Position"] = nil,
                    ["TextTransparency"] = 1,
                    ["Position"] = UDim2.new(0.5, 0, 0, -42)
                }):Play()
                v_u_4:Create(v371, TweenInfo.new(0.9), {
                    ["Transparency"] = 1
                }):Play()
                game:GetService("Debris"):AddItem(v368, 1)
            end
        end)
    end
end)
local v_u_372 = Instance.new("TextLabel")
v_u_372.AnchorPoint = Vector2.new(0.5, 1)
v_u_372.Position = UDim2.new(0.5, 0, 1, -140)
v_u_372.Size = UDim2.fromOffset(360, 28)
v_u_372.BackgroundTransparency = 1
v_u_372.Font = Enum.Font.Arcade
v_u_372.TextSize = 20
v_u_372.TextColor3 = Color3.fromRGB(255, 120, 110)
v_u_372.Text = ""
v_u_372.TextTransparency = 1
local v_u_373 = Instance.new("UIStroke")
v_u_373.Thickness = 2
v_u_373.Parent = v_u_372
v_u_372.Parent = v_u_108
local v_u_374 = 0
local function v_u_387(p375, p376, p377, p378) -- name: bigBanner
    -- upvalues: (copy) v_u_108, (copy) v_u_7, (copy) v_u_4
    local v_u_379 = Instance.new("Frame")
    v_u_379.AnchorPoint = Vector2.new(0.5, 0.5)
    v_u_379.Position = UDim2.fromScale(0.5, 0.3)
    v_u_379.Size = UDim2.fromOffset(640, 110)
    v_u_379.BackgroundTransparency = 1
    v_u_379.Parent = v_u_108
    local v_u_380 = Instance.new("TextLabel")
    v_u_380.BackgroundTransparency = 1
    v_u_380.Size = UDim2.new(1, 0, 0.6, 0)
    v_u_380.Font = Enum.Font.Arcade
    v_u_380.TextScaled = true
    v_u_380.Text = p375
    v_u_380.TextColor3 = p377
    v_u_380.Parent = v_u_379
    local v_u_381 = Instance.new("UIStroke")
    v_u_381.Thickness = 3
    v_u_381.Color = Color3.fromRGB(15, 10, 25)
    v_u_381.Parent = v_u_380
    local v_u_382 = Instance.new("TextLabel")
    v_u_382.BackgroundTransparency = 1
    v_u_382.Position = UDim2.fromScale(0, 0.62)
    v_u_382.Size = UDim2.new(1, 0, 0.32, 0)
    v_u_382.Font = Enum.Font.Arcade
    v_u_382.TextScaled = true
    v_u_382.Text = v_u_7.text(p376)
    v_u_382.TextColor3 = Color3.new(1, 1, 1)
    v_u_382.Parent = v_u_379
    local v_u_383 = Instance.new("UIStroke")
    v_u_383.Thickness = 2
    v_u_383.Color = Color3.fromRGB(15, 10, 25)
    v_u_383.Parent = v_u_382
    require(game:GetService("ReplicatedStorage"):WaitForChild("ScreenFit")).title(v_u_379)
    local v384 = Instance.new("UIScale")
    v384.Scale = 0.4
    v384.Parent = v_u_379
    v_u_4:Create(v384, TweenInfo.new(0.35, Enum.EasingStyle.Back, Enum.EasingDirection.Out), {
        ["Scale"] = 1
    }):Play()
    task.delay(p378 or 2.5, function()
        -- upvalues: (copy) v_u_380, (copy) v_u_382, (ref) v_u_4, (copy) v_u_381, (copy) v_u_383, (copy) v_u_379
        for _, v385 in { v_u_380, v_u_382 } do
            v_u_4:Create(v385, TweenInfo.new(0.5), {
                ["TextTransparency"] = 1
            }):Play()
        end
        for _, v386 in { v_u_381, v_u_383 } do
            v_u_4:Create(v386, TweenInfo.new(0.5), {
                ["Transparency"] = 1
            }):Play()
        end
        task.delay(0.6, function()
            -- upvalues: (ref) v_u_379
            v_u_379:Destroy()
        end)
    end)
end
local v_u_388 = v_u_6.Get("Thor Hammer")
local v_u_389
if v_u_388 then
    v_u_389 = v_u_388.Hammer
else
    v_u_389 = v_u_388
end
local v_u_390
if v_u_389 then
    v_u_390 = v_u_10:WaitForChild("ThorAbility", 10)
else
    v_u_390 = v_u_389
end
local v391
if v_u_389 then
    v391 = v_u_10:WaitForChild("ThorFX", 10)
else
    v391 = v_u_389
end
local v_u_392 = Color3.fromRGB(110, 195, 255)
local v_u_393 = v117("[CLICK] THROW")
v_u_393.LayoutOrder = 6
v_u_393.Visible = false
local v_u_394 = v117("[E] STOMP 0/4")
v_u_394.LayoutOrder = 7
v_u_394.Size = UDim2.fromOffset(230, 40)
v_u_394.Visible = false
v_u_5.RenderStepped:Connect(function()
    -- upvalues: (copy) v_u_388, (copy) v_u_11, (copy) v_u_393, (copy) v_u_394, (copy) v_u_389, (copy) v_u_392
    local v395
    if v_u_388 == nil then
        v395 = false
    else
        v395 = v_u_11:GetAttribute("EquippedAttack") == v_u_388.Name
    end
    v_u_393.Visible = v395
    v_u_394.Visible = v395
    if v395 then
        local v396 = v_u_11:GetAttribute("ThorState") or "Hand"
        local v397 = 0
        local v398 = "[CLICK] THROW"
        local v399 = Color3.new(1, 1, 1)
        if v396 == "Away" then
            local v400 = (v_u_11:GetAttribute("ThorReturnAt") or 0) - workspace:GetServerTimeNow()
            local v401 = math.max(v400, 0)
            local v402 = v401 / v_u_389.ReturnAfter
            v397 = math.clamp(v402, 0, 1)
            v398 = string.format("BACK IN %.1fs", v401)
            v399 = Color3.fromRGB(190, 200, 215)
        elseif v396 == "Return" then
            v399 = v_u_392
            v398 = "RETURNING..."
        elseif v396 == "Summon" then
            v399 = v_u_392
            v398 = "SUMMONING..."
            v397 = 1
        elseif v396 == "Stomp" then
            v399 = v_u_392
            v398 = "STOMP!"
        end
        v_u_393.Text = v398
        v_u_393.TextColor3 = v399
        v_u_393.Cooldown.Size = UDim2.fromScale(v397, 1)
        local v403 = v_u_389.HitsToCharge
        if v_u_11:GetAttribute("ThorCharged") then
            v_u_394.Text = (v396 == "Hand" or v396 == "Stomp") and "[E] THUNDER STOMP!" or "[E] STOMP - NO HAMMER"
            local v404 = v_u_394
            local v405 = v_u_392
            local v406 = Color3.new(1, 1, 1)
            local v407 = os.clock() * 7
            v404.TextColor3 = v405:Lerp(v406, (math.sin(v407) + 1) / 2)
            v_u_394.Cooldown.Size = UDim2.fromScale(0, 1)
        else
            local v408 = v_u_11:GetAttribute("ThorHits") or 0
            v_u_394.Text = string.format("[E] STOMP %d/%d HITS", v408, v403)
            v_u_394.TextColor3 = Color3.fromRGB(150, 180, 215)
            local v409 = v_u_394.Cooldown
            local v410 = UDim2.fromScale
            local v411 = 1 - v408 / v403
            v409.Size = v410(math.clamp(v411, 0, 1), 1)
        end
    else
        return
    end
end)
v_u_7.onInput(function(p412, p413)
    -- upvalues: (copy) v_u_389, (copy) v_u_388, (copy) v_u_11, (copy) v_u_390, (ref) v_u_374, (copy) v_u_372, (copy) v_u_373, (copy) v_u_4
    if not p413 and (v_u_389 and p412.KeyCode == Enum.KeyCode[v_u_389.Stomp.Key]) then
        local v414
        if v_u_388 == nil then
            v414 = false
        else
            v414 = v_u_11:GetAttribute("EquippedAttack") == v_u_388.Name
        end
        if v414 and v_u_390 then
            if v_u_11:GetAttribute("InFFA") then
                if v_u_11:GetAttribute("ThorCharged") then
                    if (v_u_11:GetAttribute("ThorState") or "Hand") == "Hand" then
                        v_u_390:FireServer("Stomp")
                    else
                        v_u_374 = v_u_374 + 1
                        local v_u_415 = v_u_374
                        v_u_372.Text = "Mjolnir isn\'t in your hand!"
                        v_u_372.TextTransparency = 0
                        v_u_373.Transparency = 0
                        task.delay(1.4, function()
                            -- upvalues: (ref) v_u_374, (copy) v_u_415, (ref) v_u_4, (ref) v_u_372, (ref) v_u_373
                            if v_u_374 == v_u_415 then
                                v_u_4:Create(v_u_372, TweenInfo.new(0.3), {
                                    ["TextTransparency"] = 1
                                }):Play()
                                v_u_4:Create(v_u_373, TweenInfo.new(0.3), {
                                    ["Transparency"] = 1
                                }):Play()
                            end
                        end)
                    end
                else
                    local v416 = string.format("%d more hammer hits!", v_u_389.HitsToCharge - (v_u_11:GetAttribute("ThorHits") or 0))
                    v_u_374 = v_u_374 + 1
                    local v_u_417 = v_u_374
                    v_u_372.Text = v416
                    v_u_372.TextTransparency = 0
                    v_u_373.Transparency = 0
                    task.delay(1.4, function()
                        -- upvalues: (ref) v_u_374, (copy) v_u_417, (ref) v_u_4, (ref) v_u_372, (ref) v_u_373
                        if v_u_374 == v_u_417 then
                            v_u_4:Create(v_u_372, TweenInfo.new(0.3), {
                                ["TextTransparency"] = 1
                            }):Play()
                            v_u_4:Create(v_u_373, TweenInfo.new(0.3), {
                                ["Transparency"] = 1
                            }):Play()
                        end
                    end)
                    return
                end
            else
                return
            end
        end
    end
end)
local v_u_418 = 0
local v_u_419 = 0
v_u_5:BindToRenderStep("ThorShake", Enum.RenderPriority.Camera.Value + 1, function()
    -- upvalues: (ref) v_u_418, (ref) v_u_419
    if game:GetService("Players").LocalPlayer:GetAttribute("Setting_CameraShake") == false then
        return
    else
        local v420 = v_u_418 - os.clock()
        if v420 > 0 then
            local v421 = v_u_419
            local v422 = v420 * 2
            local v423 = v421 * math.min(1, v422)
            local v424 = workspace.CurrentCamera
            local v425 = v424.CFrame
            local v426 = CFrame.Angles
            local v427 = (math.random() - 0.5) * v423
            local v428 = math.rad(v427)
            local v429 = (math.random() - 0.5) * v423
            v424.CFrame = v425 * v426(v428, math.rad(v429), 0)
        end
    end
end)
if v391 then
    local v_u_430 = nil
    task.spawn(function()
        -- upvalues: (ref) v_u_430, (copy) v_u_3
        v_u_430 = require(v_u_3:WaitForChild("LibVFXKit"))
    end)
    local v_u_431 = {}
    v391.OnClientEvent:Connect(function(p432, p_u_433, p434, _)
        -- upvalues: (copy) v_u_387, (copy) v_u_389, (copy) v_u_392, (ref) v_u_418, (ref) v_u_419, (ref) v_u_430, (copy) v_u_431
        if p432 == "Summoned" then
            v_u_387("MJOLNIR!", "Click: throw it  -  " .. v_u_389.HitsToCharge .. " hits = [" .. v_u_389.Stomp.Key .. "] Thunder Stomp", v_u_392, 2.5)
            if v_u_418 < os.clock() or v_u_419 < 2.5 then
                v_u_419 = 2.5
            end
            local v435 = v_u_418
            local v436 = os.clock() + 0.5
            v_u_418 = math.max(v435, v436)
            return
        elseif p432 == "Charged" then
            v_u_387("CHARGED!", "[" .. v_u_389.Stomp.Key .. "] THUNDER STOMP unlocked", v_u_392, 2.5)
            if v_u_418 < os.clock() or v_u_419 < 2 then
                v_u_419 = 2
            end
            local v437 = v_u_418
            local v438 = os.clock() + 0.4
            v_u_418 = math.max(v437, v438)
            return
        elseif p432 == "Caught" then
            if v_u_418 < os.clock() or v_u_419 < 1.2 then
                v_u_419 = 1.2
            end
            local v439 = v_u_418
            local v440 = os.clock() + 0.2
            v_u_418 = math.max(v439, v440)
        elseif p432 == "Shake" and typeof(p_u_433) == "Vector3" then
            local v441 = 1 - (workspace.CurrentCamera.CFrame.Position - p_u_433).Magnitude / 120
            local v442 = math.clamp(v441, 0, 1)
            if v442 > 0 then
                local v443 = v442 * 5
                local v444 = v442 * 0.5 + 0.4
                if v_u_418 < os.clock() or v_u_419 < v443 then
                    v_u_419 = v443
                end
                local v445 = v_u_418
                local v446 = os.clock() + v444
                v_u_418 = math.max(v445, v446)
                return
            end
        else
            if p432 == "Tether" and (typeof(p_u_433) == "Instance" and typeof(p434) == "Instance") then
                local v447 = p_u_433:FindFirstChild("Right Arm")
                local v448 = p434:FindFirstChild("Handle")
                if v448 then
                    v448 = v448:FindFirstChild("HeadCenter")
                end
                if v447 and (v448 and v_u_430) then
                    local v449 = v_u_431[p_u_433]
                    if v449 then
                        v_u_431[p_u_433] = nil
                        if v449.beam then
                            v449.beam.stop(0.15)
                        end
                        game:GetService("Debris"):AddItem(v449.att, 0.2)
                    end
                    local v450 = Instance.new("Attachment")
                    v450.Name = "ThorTether"
                    v450.Position = Vector3.new(0, -1, 0)
                    v450.Parent = v447
                    local v_u_451 = {
                        ["att"] = v450
                    }
                    v_u_431[p_u_433] = v_u_451
                    v_u_451.beam = v_u_430.beam("LightningBeam", v450, v448, {
                        ["width"] = 0.3
                    })
                    task.delay(4, function()
                        -- upvalues: (ref) v_u_431, (copy) p_u_433, (copy) v_u_451
                        if v_u_431[p_u_433] == v_u_451 then
                            local v452 = p_u_433
                            local v453 = v_u_431[v452]
                            if not v453 then
                                return
                            end
                            v_u_431[v452] = nil
                            if v453.beam then
                                v453.beam.stop(0.15)
                            end
                            game:GetService("Debris"):AddItem(v453.att, 0.2)
                        end
                    end)
                end
            end
            if p432 == "TetherEnd" and typeof(p_u_433) == "Instance" then
                local v454 = v_u_431[p_u_433]
                if not v454 then
                    return
                end
                v_u_431[p_u_433] = nil
                if v454.beam then
                    v454.beam.stop(0.15)
                end
                game:GetService("Debris"):AddItem(v454.att, 0.2)
            end
        end
    end)
end
local v_u_455 = Instance.new("Folder")
v_u_455.Name = "ThorArcs"
v_u_455.Parent = workspace.CurrentCamera
local v_u_456 = {
    "Torso",
    "Right Arm",
    "Left Arm",
    "Right Leg",
    "Left Leg",
    "Head"
}
local function v_u_469(p457, p458) -- name: localArc
    -- upvalues: (copy) v_u_392, (copy) v_u_455
    local v459 = p458 - p457
    local v460 = p457
    for v461 = 1, 4 do
        local v462
        if v461 < 4 then
            local v463 = p457 + v459 * (v461 / 4)
            local v464 = math.random() - 0.5
            local v465 = math.random() - 0.5
            local v466 = math.random() - 0.5
            v462 = v463 + Vector3.new(v464, v465, v466) * v459.Magnitude * 0.35
        else
            v462 = p458
        end
        local v467 = Instance.new("Part")
        v467.Anchored = true
        v467.CanCollide = false
        v467.CanQuery = false
        v467.CanTouch = false
        v467.CastShadow = false
        v467.Material = Enum.Material.Neon
        v467.Color = math.random() < 0.5 and Color3.fromRGB(215, 240, 255) or v_u_392
        local v468 = (v462 - v460).Magnitude
        v467.Size = Vector3.new(0.09, 0.09, v468)
        v467.CFrame = CFrame.lookAt((v460 + v462) / 2, v462)
        v467.Parent = v_u_455
        game:GetService("Debris"):AddItem(v467, 0.07)
        v460 = v462
    end
end
local v_u_470 = 0
v_u_5.Heartbeat:Connect(function()
    -- upvalues: (copy) v_u_388, (ref) v_u_470, (copy) v_u_8, (copy) v_u_1, (copy) v_u_456, (copy) v_u_469
    if v_u_388 and os.clock() >= v_u_470 then
        v_u_470 = os.clock() + 0.07
        local v471 = workspace.CurrentCamera.CFrame.Position
        for _, v472 in v_u_8.withBots(v_u_1:GetPlayers()) do
            if not v472:GetAttribute("ThorCharged") or (v472:GetAttribute("EquippedAttack") ~= v_u_388.Name or math.random() >= 0.75) then
                continue
            end
            local v473 = v472.Character
            if v473 then
                v473 = v472.Character:FindFirstChild(v_u_456[math.random(#v_u_456)])
            end
            if not v473 or (v473.Position - v471).Magnitude >= 220 then
                continue
            end
            local v474 = v473.Size / 2
            local v475 = (v473.CFrame * CFrame.new((math.random() * 2 - 1) * v474.X, (math.random() * 2 - 1) * v474.Y, (math.random() * 2 - 1) * v474.Z)).Position
            local v476 = math.random() - 0.5
            local v477 = math.random() - 0.5
            local v478 = math.random() - 0.5
            local v479 = Vector3.new(v476, v477, v478)
            if v479.Magnitude <= 0.05 then
                continue
            end
            v_u_469(v475, v475 + v479.Unit * (1.2 + math.random() * 1.6))
        end
    end
end)
if v_u_388 then
    task.spawn(function()
        -- upvalues: (copy) v_u_3, (copy) v_u_5, (copy) v_u_8, (copy) v_u_1, (copy) v_u_388
        local v_u_480 = require(v_u_3:WaitForChild("JJKAnim"))
        local v481 = v_u_480.ANIMS.ThorIdle
        if v481 and not v_u_480.ANIMS.ThorIdleArms then
            local v482 = table.clone(v481.Tracks)
            v482.Head = nil
            v_u_480.ANIMS.ThorIdleArms = {
                ["Length"] = v481.Length,
                ["Loop"] = v481.Loop,
                ["Tracks"] = v482
            }
        end
        local v_u_483 = {}
        local v_u_484 = {}
        local v_u_485 = 0
        v_u_5.Heartbeat:Connect(function()
            -- upvalues: (ref) v_u_485, (ref) v_u_8, (ref) v_u_1, (ref) v_u_388, (copy) v_u_480, (copy) v_u_484, (copy) v_u_483
            if os.clock() >= v_u_485 then
                v_u_485 = os.clock() + 0.1
                local v486 = {}
                for _, v487 in v_u_8.withBots(v_u_1:GetPlayers()) do
                    local v488 = v487.Character
                    local v489
                    if v488 then
                        v489 = v488:FindFirstChildOfClass("Humanoid")
                    else
                        v489 = v488
                    end
                    local v490
                    if v488 then
                        v490 = v488:FindFirstChild("HumanoidRootPart")
                    else
                        v490 = v488
                    end
                    if v489 and (v490 and (v489.Health > 0 and (v487:GetAttribute("EquippedAttack") == v_u_388.Name and (v487:GetAttribute("ThorState") == "Hand" and (v488:FindFirstChild("Mjolnir") and (v489.MoveDirection.Magnitude < 0.1 and not v_u_480.busy(v488))))))) then
                        local v491 = v490.AssemblyLinearVelocity
                        local v492 = v491.X
                        local v493 = v491.Z
                        if Vector3.new(v492, 0, v493).Magnitude < 1.5 then
                            local v494 = v491.Y
                            if math.abs(v494) < 6 and (not v488:GetAttribute("Ragdolled") and (not v489.Sit and (not v489.PlatformStand and v488:GetAttribute("EmoteId") == nil))) then
                                v486[v488] = true
                            end
                        end
                    end
                    if v488 and v487:GetAttribute("ThorState") == "Hand" then
                        v_u_484[v488] = v_u_484[v488] or os.clock()
                        if os.clock() - v_u_484[v488] >= 0.45 then
                            continue
                        end
                        v486[v488] = nil
                    else
                        if not v488 then
                            continue
                        end
                        v_u_484[v488] = nil
                    end
                end
                for v495 in v_u_484 do
                    if v495.Parent then
                        continue
                    end
                    v_u_484[v495] = nil
                end
                for v496, v497 in v_u_483 do
                    if v486[v496] and not v497.stopAt then
                        continue
                    end
                    v_u_480.stop(v497, 0.25)
                    v_u_483[v496] = nil
                end
                for v498 in v486 do
                    if v_u_483[v498] then
                        continue
                    end
                    v_u_483[v498] = v_u_480.play(v498, v_u_480.ANIMS.ThorIdleArms and "ThorIdleArms" or "ThorIdle", {
                        ["base"] = true,
                        ["loop"] = true,
                        ["upper"] = true,
                        ["fadeIn"] = 0.3,
                        ["fadeOut"] = 0.3
                    })
                end
            end
        end)
    end)
end
if v_u_6.Get("Itachi") then
    task.spawn(function()
        -- upvalues: (copy) v_u_10, (ref) v_u_374, (copy) v_u_372, (copy) v_u_373, (copy) v_u_4, (copy) v_u_11
        local v499 = v_u_10:WaitForChild("ItachiFX", 10)
        if v499 then
            v499.OnClientEvent:Connect(function(p500, p501)
                -- upvalues: (ref) v_u_374, (ref) v_u_372, (ref) v_u_373, (ref) v_u_4, (ref) v_u_11
                if p500 == "NoTarget" then
                    local v502 = typeof(p501) == "string" and p501 and p501 or "No enemy nearby!"
                    v_u_374 = v_u_374 + 1
                    local v_u_503 = v_u_374
                    v_u_372.Text = v502
                    v_u_372.TextTransparency = 0
                    v_u_373.Transparency = 0
                    task.delay(1.4, function()
                        -- upvalues: (ref) v_u_374, (copy) v_u_503, (ref) v_u_4, (ref) v_u_372, (ref) v_u_373
                        if v_u_374 == v_u_503 then
                            v_u_4:Create(v_u_372, TweenInfo.new(0.3), {
                                ["TextTransparency"] = 1
                            }):Play()
                            v_u_4:Create(v_u_373, TweenInfo.new(0.3), {
                                ["Transparency"] = 1
                            }):Play()
                        end
                    end)
                elseif p500 == "Face" and typeof(p501) == "Vector3" then
                    local v504 = v_u_11.Character
                    local v505
                    if v504 then
                        v505 = v504:FindFirstChild("HumanoidRootPart")
                    else
                        v505 = v504
                    end
                    if not v505 or (v505.Anchored or v504:GetAttribute("Ragdolled")) then
                        return
                    end
                    local v506 = p501.X - v505.Position.X
                    local v507 = p501.Z - v505.Position.Z
                    local v508 = Vector3.new(v506, 0, v507)
                    if v508.Magnitude > 0.1 then
                        v505.CFrame = CFrame.lookAt(v505.Position, v505.Position + v508)
                    end
                end
            end)
        end
    end)
end
if v_u_6.Get("Roaring Knight") then
    task.spawn(function()
        -- upvalues: (copy) v_u_10, (copy) v_u_11, (copy) v_u_4, (ref) v_u_418, (ref) v_u_419, (copy) v_u_5, (copy) v_u_2, (copy) v_u_108
        local v509 = v_u_10:WaitForChild("KnightFX", 10)
        if v509 then
            local v_u_510 = Instance.new("ScreenGui")
            v_u_510.Name = "KnightScreen"
            v_u_510.IgnoreGuiInset = true
            v_u_510.ResetOnSpawn = false
            v_u_510.DisplayOrder = 70
            v_u_510.Enabled = false
            v_u_510.Parent = v_u_11:WaitForChild("PlayerGui")
            local v_u_511 = Instance.new("Frame")
            v_u_511.Size = UDim2.fromScale(1, 1)
            v_u_511.BorderSizePixel = 0
            v_u_511.BackgroundColor3 = Color3.new(0, 0, 0)
            v_u_511.BackgroundTransparency = 1
            v_u_511.Parent = v_u_510
            local v_u_512 = Instance.new("ImageLabel")
            v_u_512.AnchorPoint = Vector2.new(0.5, 0.5)
            v_u_512.Position = UDim2.fromScale(0.46, 0.52)
            v_u_512.Size = UDim2.fromScale(0.98, 0.16)
            v_u_512.BackgroundTransparency = 1
            v_u_512.Image = "rbxassetid://82500316371865"
            v_u_512.ScaleType = Enum.ScaleType.Stretch
            v_u_512.Rotation = -7
            v_u_512.ImageTransparency = 1
            v_u_512.Parent = v_u_511
            local v_u_513 = Instance.new("UIGradient")
            v_u_513.Transparency = NumberSequence.new({
                NumberSequenceKeypoint.new(0, 0),
                NumberSequenceKeypoint.new(0.49, 0),
                NumberSequenceKeypoint.new(0.51, 1),
                NumberSequenceKeypoint.new(1, 1)
            })
            v_u_513.Parent = v_u_512
            local v_u_514 = Instance.new("UIScale")
            v_u_514.Parent = v_u_512
            task.spawn(function()
                -- upvalues: (copy) v_u_512
                local function v516()
                    -- upvalues: (ref) v_u_512
                    local v515 = { v_u_512 }
                    game:GetService("ContentProvider"):PreloadAsync(v515)
                end
                pcall(v516)
            end)
            local v_u_517 = 0
            local function v_u_522() -- name: ultraSlash
                -- upvalues: (ref) v_u_517, (copy) v_u_510, (copy) v_u_511, (copy) v_u_512, (copy) v_u_513, (copy) v_u_514, (ref) v_u_4, (ref) v_u_418, (ref) v_u_419
                v_u_517 = v_u_517 + 1
                local v518 = v_u_517
                v_u_510.Enabled = true
                v_u_511.BackgroundTransparency = 0
                v_u_512.ImageTransparency = 0
                v_u_513.Offset = Vector2.new(-0.52, 0)
                v_u_514.Scale = 0.97
                v_u_4:Create(v_u_513, TweenInfo.new(0.13, Enum.EasingStyle.Quad, Enum.EasingDirection.Out), {
                    ["Offset"] = Vector2.new(0.52, 0)
                }):Play()
                v_u_4:Create(v_u_514, TweenInfo.new(0.7, Enum.EasingStyle.Quad, Enum.EasingDirection.Out), {
                    ["Scale"] = 1.04
                }):Play()
                task.wait(0.75)
                if v518 == v_u_517 then
                    if v_u_418 < os.clock() or v_u_419 < 3 then
                        v_u_419 = 3
                    end
                    local v519 = v_u_418
                    local v520 = os.clock() + 0.4
                    v_u_418 = math.max(v519, v520)
                    v_u_4:Create(v_u_511, TweenInfo.new(0.45), {
                        ["BackgroundTransparency"] = 1
                    }):Play()
                    local v521 = v_u_4:Create(v_u_512, TweenInfo.new(0.45), {
                        ["ImageTransparency"] = 1
                    })
                    v521:Play()
                    v521.Completed:Wait()
                    if v518 == v_u_517 then
                        v_u_510.Enabled = false
                    end
                end
            end
            local v_u_523 = Instance.new("ScreenGui")
            v_u_523.Name = "KnightUltraAim"
            v_u_523.IgnoreGuiInset = true
            v_u_523.ResetOnSpawn = false
            v_u_523.DisplayOrder = 3
            v_u_523.Enabled = false
            v_u_523.Parent = v_u_11:WaitForChild("PlayerGui")
            local function v531(p524, p525, p526, p527) -- name: aimCircle
                -- upvalues: (copy) v_u_523
                local v528 = Instance.new("Frame")
                v528.AnchorPoint = Vector2.new(0.5, 0.5)
                v528.Position = UDim2.fromScale(0.5, 0.5)
                v528.Size = UDim2.fromOffset(p527, p527)
                v528.BackgroundTransparency = 1
                v528.BorderSizePixel = 0
                v528.ZIndex = p524
                local v529 = Instance.new("UICorner")
                v529.CornerRadius = UDim.new(1, 0)
                v529.Parent = v528
                local v530 = Instance.new("UIStroke")
                v530.Thickness = p525
                v530.Color = p526
                v530.Transparency = 1
                v530.Parent = v528
                v528.Parent = v_u_523
                return v528, v530
            end
            local v_u_532, v_u_533 = v531(1, 6, Color3.new(0, 0, 0), 80)
            local v_u_534, v_u_535 = v531(2, 2.5, Color3.new(1, 1, 1), 80)
            local v_u_536, v_u_537 = v531(3, 1.5, Color3.new(0, 0, 0), 6)
            v_u_536.BackgroundColor3 = Color3.new(1, 1, 1)
            local v_u_538 = Color3.fromRGB(150, 150, 160)
            local v_u_539 = Color3.fromRGB(255, 40, 55)
            local v_u_540 = nil
            local function v_u_571(p_u_541, p_u_542) -- name: ultraAim
                -- upvalues: (ref) v_u_11, (ref) v_u_540, (copy) v_u_523, (ref) v_u_5, (ref) v_u_2, (copy) v_u_539, (copy) v_u_538, (copy) v_u_534, (copy) v_u_532, (copy) v_u_535, (copy) v_u_533, (copy) v_u_536, (copy) v_u_537
                local v_u_543 = v_u_11.Character
                local v_u_544
                if v_u_543 then
                    v_u_544 = v_u_543:FindFirstChild("HumanoidRootPart")
                else
                    v_u_544 = v_u_543
                end
                local v_u_545
                if v_u_543 then
                    v_u_545 = v_u_543:FindFirstChildOfClass("Humanoid")
                else
                    v_u_545 = v_u_543
                end
                if v_u_544 and (v_u_545 and (p_u_541 and p_u_542)) then
                    if v_u_540 then
                        v_u_540.finish()
                    end
                    local v_u_546 = workspace.CurrentCamera
                    local v_u_547 = v_u_546.FieldOfView < 60 and 70 or v_u_546.FieldOfView
                    local v_u_548 = {
                        ["w"] = 0
                    }
                    v_u_540 = v_u_548
                    v_u_546:SetAttribute("ZoomHold", true)
                    v_u_523.Enabled = true
                    function v_u_548.finish() -- name: finish
                        -- upvalues: (copy) v_u_548, (ref) v_u_540, (ref) v_u_5, (ref) v_u_523, (ref) v_u_2, (copy) v_u_545, (copy) v_u_546, (copy) v_u_547
                        if not v_u_548.done then
                            v_u_548.done = true
                            if v_u_540 == v_u_548 then
                                v_u_540 = nil
                            end
                            local function v549()
                                -- upvalues: (ref) v_u_5
                                v_u_5:UnbindFromRenderStep("KnightUltraAim")
                            end
                            pcall(v549)
                            v_u_523.Enabled = false
                            v_u_2.MouseBehavior = Enum.MouseBehavior.Default
                            if v_u_545.Parent then
                                v_u_545.CameraOffset = Vector3.new(0, 0, 0)
                            end
                            v_u_546.FieldOfView = v_u_547
                            v_u_546:SetAttribute("ZoomHold", nil)
                        end
                    end
                    v_u_5:BindToRenderStep("KnightUltraAim", Enum.RenderPriority.Camera.Value + 2, function(p550)
                        -- upvalues: (copy) p_u_541, (copy) v_u_544, (ref) v_u_11, (copy) v_u_543, (copy) v_u_545, (copy) v_u_548, (copy) p_u_542, (ref) v_u_2, (copy) v_u_546, (copy) v_u_547, (ref) v_u_539, (ref) v_u_538, (ref) v_u_534, (ref) v_u_532, (ref) v_u_535, (ref) v_u_533, (ref) v_u_536, (ref) v_u_537
                        local v551 = workspace:GetServerTimeNow() - p_u_541
                        if v_u_544.Parent and (v_u_11.Character == v_u_543 and v_u_545.Health > 0) then
                            local v552 = p_u_542 + 0.4 < v551
                            local v553 = v_u_548
                            local v554 = v553.w
                            local v555 = (v552 and 0 or 1) - v_u_548.w
                            local v556 = -p550 / 0.09
                            v553.w = v554 + v555 * (1 - math.exp(v556))
                            if v552 and v_u_548.w < 0.03 then
                                v_u_548.finish()
                            else
                                local v557 = v_u_548.w
                                v_u_2.MouseBehavior = Enum.MouseBehavior.LockCenter
                                v_u_545.CameraOffset = Vector3.new(3, 1, 0) * v557
                                local v558 = v_u_546.CFrame.LookVector
                                local v559 = (v_u_546.CFrame.Position - v_u_546.Focus.Position).Magnitude
                                if v559 > 13 then
                                    local v560 = v_u_546
                                    v560.CFrame = v560.CFrame + v558 * ((v559 - 13) * v557)
                                end
                                local v561, v562, v563
                                if v551 < p_u_542 then
                                    local v564 = v551 / p_u_542
                                    local v565 = math.clamp(v564, 0, 1)
                                    local v566 = 1 - (1 - v565) * (1 - v565)
                                    v561 = v_u_547 - v566 * 10
                                    v562 = 84 - v566 * 54
                                    v563 = v565 >= 0.95 and v_u_539 or v_u_538:Lerp(Color3.new(1, 1, 1), v565)
                                else
                                    local v567 = v551 - p_u_542
                                    v561 = v567 < 0.08 and v_u_547 - 10 + 24 * v567 / 0.08
                                    if not v561 then
                                        local v568 = v_u_547
                                        local v569 = -(v567 - 0.08) / 0.25
                                        v561 = v568 + math.exp(v569) * 14
                                    end
                                    local v570 = -v567 / 0.12
                                    v562 = math.exp(v570) * 30 + 22
                                    v563 = Color3.new(1, 1, 1)
                                end
                                v_u_546.FieldOfView = v_u_547 + (v561 - v_u_547) * v557
                                v_u_534.Size = UDim2.fromOffset(v562, v562)
                                v_u_532.Size = v_u_534.Size
                                v_u_535.Color = v563
                                v_u_535.Transparency = 1 - 0.9 * v557
                                v_u_533.Transparency = 1 - 0.45 * v557
                                v_u_536.BackgroundTransparency = 1 - v557
                                v_u_537.Transparency = 1 - v557
                            end
                        else
                            v_u_548.finish()
                            return
                        end
                    end)
                end
            end
            local v_u_572 = Instance.new("Frame")
            v_u_572.Name = "KnightAppeared"
            v_u_572.AnchorPoint = Vector2.new(0.5, 1)
            v_u_572.Position = UDim2.new(0.5, 0, 1, -200)
            v_u_572.Size = UDim2.new(0.62, 0, 0, 66)
            v_u_572.BackgroundColor3 = Color3.new(0, 0, 0)
            v_u_572.BackgroundTransparency = 0.1
            v_u_572.Visible = false
            local v573 = Instance.new("UISizeConstraint")
            v573.MaxSize = Vector2.new(780, 66)
            v573.Parent = v_u_572
            local v_u_574 = Instance.new("UIStroke")
            v_u_574.Thickness = 3
            v_u_574.Color = Color3.new(1, 1, 1)
            v_u_574.ApplyStrokeMode = Enum.ApplyStrokeMode.Border
            v_u_574.Parent = v_u_572
            local v_u_575 = Instance.new("TextLabel")
            v_u_575.BackgroundTransparency = 1
            v_u_575.Position = UDim2.fromOffset(22, 10)
            v_u_575.Size = UDim2.new(1, -44, 1, -20)
            v_u_575.Font = Enum.Font.Arcade
            v_u_575.TextScaled = true
            v_u_575.TextColor3 = Color3.new(1, 1, 1)
            v_u_575.TextXAlignment = Enum.TextXAlignment.Left
            v_u_575.RichText = true
            v_u_575.Text = "The <font color=\"#ff2a2a\">Roaring Knight</font> appeared."
            v_u_575.Parent = v_u_572
            v_u_572.Parent = v_u_108
            local v_u_576 = 0
            local function v_u_579() -- name: appeared
                -- upvalues: (ref) v_u_576, (copy) v_u_572, (copy) v_u_574, (copy) v_u_575, (ref) v_u_4
                v_u_576 = v_u_576 + 1
                local v577 = v_u_576
                v_u_572.Visible = true
                v_u_572.BackgroundTransparency = 0.1
                v_u_574.Transparency = 0
                v_u_575.TextTransparency = 0
                for v578 = 0, utf8.len(v_u_575.ContentText) or 30 do
                    if v577 ~= v_u_576 then
                        return
                    end
                    v_u_575.MaxVisibleGraphemes = v578
                    task.wait(0.035)
                end
                task.wait(2.2)
                if v577 == v_u_576 then
                    v_u_4:Create(v_u_572, TweenInfo.new(0.4), {
                        ["BackgroundTransparency"] = 1
                    }):Play()
                    v_u_4:Create(v_u_574, TweenInfo.new(0.4), {
                        ["Transparency"] = 1
                    }):Play()
                    v_u_4:Create(v_u_575, TweenInfo.new(0.4), {
                        ["TextTransparency"] = 1
                    }):Play()
                    task.wait(0.4)
                    if v577 == v_u_576 then
                        v_u_572.Visible = false
                    end
                end
            end
            v509.OnClientEvent:Connect(function(p580, p581, p582)
                -- upvalues: (copy) v_u_522, (copy) v_u_571, (ref) v_u_418, (ref) v_u_419, (ref) v_u_11, (copy) v_u_579
                if p580 == "UltraSlash" then
                    task.spawn(v_u_522)
                    return
                elseif p580 == "UltraAim" then
                    v_u_571(tonumber(p581), (tonumber(p582)))
                    return
                elseif p580 == "Shake" and typeof(p581) == "number" then
                    local v583 = math.clamp(p581, 0, 8)
                    if v_u_418 < os.clock() or v_u_419 < v583 then
                        v_u_419 = v583
                    end
                    local v584 = v_u_418
                    local v585 = os.clock() + 0.35
                    v_u_418 = math.max(v584, v585)
                elseif p580 == "Appeared" then
                    local v586 = v_u_11.PlayerGui:FindFirstChild("KnightBlackout")
                    if not (v586 and v586.Enabled) then
                        task.spawn(v_u_579)
                    end
                end
            end)
        end
    end)
end
local v_u_587 = Instance.new("UIScale")
v_u_587.Parent = v_u_109
v_u_5.RenderStepped:Connect(function()
    -- upvalues: (copy) v_u_109, (copy) v_u_587
    local v588 = v_u_109.AbsoluteSize.X
    local v589 = v_u_587.Scale
    local v590 = v588 / math.max(v589, 0.01)
    local v591 = v_u_587
    local v592 = (workspace.CurrentCamera.ViewportSize.X - 24) / math.max(v590, 1)
    v591.Scale = math.clamp(v592, 0.45, 1)
end)
local function v595(p593) -- name: noTrip
    local v594 = p593:WaitForChild("Humanoid", 10)
    if v594 then
        v594:SetStateEnabled(Enum.HumanoidStateType.FallingDown, false)
        v594:SetStateEnabled(Enum.HumanoidStateType.Ragdoll, false)
    end
end
v_u_11.CharacterAdded:Connect(v595)
if v_u_11.Character then
    task.spawn(v595, v_u_11.Character)
end
local function v600(p596) -- name: knockTimer
    local v597 = p596:WaitForChild("HumanoidRootPart", 10)
    if v597 then
        v597.ChildAdded:Connect(function(p_u_598)
            local v599 = p_u_598:IsA("LinearVelocity") and p_u_598:GetAttribute("KnockPush")
            if v599 then
                v599 = p_u_598:GetAttribute("KnockLife")
            end
            if type(v599) == "number" then
                task.delay(v599, function()
                    -- upvalues: (copy) p_u_598
                    if p_u_598.Parent then
                        p_u_598:Destroy()
                    end
                end)
            end
        end)
    end
end
v_u_11.CharacterAdded:Connect(v600)
if v_u_11.Character then
    task.spawn(v600, v_u_11.Character)
end
v_u_11.CharacterAdded:Connect(function(p601) -- name: followCamera
    local v602 = p601:WaitForChild("Humanoid", 10)
    if v602 then
        local v603 = workspace.CurrentCamera
        for _ = 1, 3 do
            v603.CameraType = Enum.CameraType.Custom
            v603.CameraSubject = v602
            task.wait(0.1)
        end
    end
end)
local v_u_604 = require(script.Parent:WaitForChild("KnightFlight"))
local v_u_605 = CFrame.new()
v_u_5.Stepped:Connect(function(_, p606)
    -- upvalues: (copy) v_u_8, (copy) v_u_1, (copy) v_u_605, (copy) v_u_604
    for _, v607 in v_u_8.withBots(v_u_1:GetPlayers()) do
        local v608 = v607.Character
        local v609
        if v608 then
            v609 = v608:FindFirstChild("VerityMorph") or v608:FindFirstChild("KnightMorph")
        else
            v609 = v608
        end
        local v610
        if v608 then
            v610 = v608:FindFirstChild("Torso")
        else
            v610 = v608
        end
        if not (v609 and v610) then
            continue
        end
        for _, v611 in v609:GetDescendants() do
            if not v611:IsA("Motor6D") then
                continue
            end
            local v612 = v611:GetAttribute("GhostOf")
            if v612 then
                v612 = v610:FindFirstChild(v612)
            end
            if v612 then
                local v613 = v611:GetAttribute("GhostScale")
                v611.Transform = v613 and v_u_605:Lerp(v612.Transform, v613) or v612.Transform
                if v611:GetAttribute("FollowC0") then
                    v611.C0 = v612.C0
                end
            end
            local v614 = v611:GetAttribute("KneeOf")
            if v614 then
                v614 = v608:FindFirstChild(v614)
            end
            if not v614 then
                continue
            end
            local v615 = v610.CFrame:VectorToObjectSpace(-v614.CFrame.UpVector).Z
            local v616 = math.max(0, v615)
            v611.Transform = CFrame.Angles(-(0.12 + v616 * 1.6 * (v611:GetAttribute("GhostScale") or 1)), 0, 0)
        end
        v_u_604.Step(v607, v608, v609, v610, p606)
    end
    v_u_604.EndFrame()
end)
task.spawn(function()
    -- upvalues: (copy) v_u_10, (copy) v_u_11, (copy) v_u_4
    local v617 = v_u_10:WaitForChild("BountyFX", 30)
    if v617 then
        local v_u_618 = Color3.fromRGB(234, 214, 166)
        local v_u_619 = Color3.fromRGB(78, 46, 18)
        local v_u_620 = Color3.fromRGB(200, 30, 30)
        local v_u_621 = Font.new("rbxasset://fonts/families/Merriweather.json", Enum.FontWeight.Bold)
        local v_u_622 = Font.new("rbxasset://fonts/families/SpecialElite.json")
        local v_u_623 = Instance.new("ScreenGui")
        v_u_623.Name = "BountyPoster"
        v_u_623.ResetOnSpawn = false
        v_u_623.IgnoreGuiInset = true
        v_u_623.DisplayOrder = 40
        v_u_623.Parent = v_u_11:WaitForChild("PlayerGui")
        local v_u_624 = nil
        local v_u_625 = 0
        local function v_u_636(p626, p_u_627, p628) -- name: face
            if p628 then
                local v629 = Instance.new("ImageLabel")
                v629.Size = UDim2.fromScale(1, 1)
                v629.BackgroundTransparency = 1
                v629.Image = "rbxthumb://type=AvatarHeadShot&id=" .. p628 .. "&w=420&h=420"
                v629.ImageColor3 = Color3.fromRGB(255, 236, 200)
                v629.Parent = p626
                return
            elseif typeof(p_u_627) == "Instance" and p_u_627.Parent then
                local v_u_630 = Instance.new("ViewportFrame")
                v_u_630.Size = UDim2.fromScale(1, 1)
                v_u_630.BackgroundTransparency = 1
                v_u_630.Ambient = Color3.fromRGB(190, 175, 150)
                v_u_630.LightColor = Color3.fromRGB(255, 240, 210)
                v_u_630.LightDirection = Vector3.new(-1, -1, -1)
                v_u_630.Parent = p626
                pcall(function()
                    -- upvalues: (copy) p_u_627, (copy) v_u_630
                    local v631 = p_u_627.Archivable
                    p_u_627.Archivable = true
                    local v632 = p_u_627:Clone()
                    p_u_627.Archivable = v631
                    for _, v633 in v632:GetDescendants() do
                        if not (v633:IsA("LuaSourceContainer") or (v633:IsA("BillboardGui") or (v633:IsA("ParticleEmitter") or (v633:IsA("Sound") or v633:IsA("Highlight"))))) then
                            continue
                        end
                        v633:Destroy()
                    end
                    v632.Parent = v_u_630
                    local v634 = v632:FindFirstChild("Head")
                    local v635 = Instance.new("Camera")
                    v635.FieldOfView = 30
                    if v634 then
                        v635.CFrame = CFrame.lookAt((v634.CFrame * CFrame.new(0, 0.15, -4.5)).Position, v634.Position)
                    end
                    v635.Parent = v_u_630
                    v_u_630.CurrentCamera = v635
                end)
            end
        end
        local function v_u_661(p637, p638, p639, p640) -- name: makePoster
            -- upvalues: (copy) v_u_618, (copy) v_u_619, (copy) v_u_621, (copy) v_u_636, (copy) v_u_622, (copy) v_u_623
            local v641 = workspace.CurrentCamera.ViewportSize.Y / 900
            local v642 = math.clamp(v641, 0.55, 1)
            local v643 = Instance.new("Frame")
            v643.Name = "Poster"
            v643.AnchorPoint = Vector2.new(0.5, 0.5)
            v643.Position = UDim2.fromScale(0.5, -0.4)
            v643.Size = UDim2.fromOffset(260, 340)
            v643.BackgroundColor3 = v_u_618
            v643.BorderSizePixel = 0
            v643.Rotation = -8
            local v644 = Instance.new("UICorner")
            v644.CornerRadius = UDim.new(0, 6)
            v644.Parent = v643
            local v645 = Instance.new("UIStroke")
            v645.Thickness = 5
            v645.Color = v_u_619
            v645.Parent = v643
            local v646 = Instance.new("UIScale")
            v646.Scale = v642
            v646.Parent = v643
            local v647 = {
                ["Name"] = "Title",
                ["Position"] = nil,
                ["Size"] = nil,
                ["Text"] = "WANTED",
                ["FontFace"] = nil,
                ["Position"] = UDim2.fromOffset(12, 8),
                ["Size"] = UDim2.new(1, -24, 0, 56),
                ["FontFace"] = v_u_621
            }
            local v648 = Instance.new("TextLabel")
            v648.BackgroundTransparency = 1
            v648.TextColor3 = v_u_619
            v648.TextScaled = true
            for v649, v650 in v647 do
                v648[v649] = v650
            end
            v648.Parent = v643
            local v651 = Instance.new("Frame")
            v651.Name = "Face"
            v651.Position = UDim2.fromOffset(40, 70)
            v651.Size = UDim2.fromOffset(180, 180)
            v651.BackgroundColor3 = Color3.fromRGB(205, 182, 135)
            v651.BorderSizePixel = 0
            v651.ClipsDescendants = true
            local v652 = Instance.new("UIStroke")
            v652.Thickness = 3
            v652.Color = v_u_619
            v652.Parent = v651
            v651.Parent = v643
            v_u_636(v651, p637, p638)
            local v653 = {
                ["Name"] = "Who",
                ["Position"] = nil,
                ["Size"] = nil,
                ["Text"] = nil,
                ["FontFace"] = nil,
                ["Position"] = UDim2.fromOffset(12, 258),
                ["Size"] = UDim2.new(1, -24, 0, 34),
                ["Text"] = string.upper((tostring(p639))),
                ["FontFace"] = v_u_622
            }
            local v654 = Instance.new("TextLabel")
            v654.BackgroundTransparency = 1
            v654.TextColor3 = v_u_619
            v654.TextScaled = true
            for v655, v656 in v653 do
                v654[v655] = v656
            end
            v654.Parent = v643
            local v657 = {
                ["Name"] = "Reward",
                ["Position"] = nil,
                ["Size"] = nil,
                ["Text"] = nil,
                ["FontFace"] = nil,
                ["TextColor3"] = nil,
                ["Position"] = UDim2.fromOffset(12, 296),
                ["Size"] = UDim2.new(1, -24, 0, 30),
                ["Text"] = tostring(p640),
                ["FontFace"] = v_u_622,
                ["TextColor3"] = Color3.fromRGB(150, 40, 20)
            }
            local v658 = Instance.new("TextLabel")
            v658.BackgroundTransparency = 1
            v658.TextColor3 = v_u_619
            v658.TextScaled = true
            for v659, v660 in v657 do
                v658[v659] = v660
            end
            v658.Parent = v643
            v643.Parent = v_u_623
            return v643, v646, v642
        end
        local function v_u_665(p_u_662, p663, p664) -- name: flyAway
            -- upvalues: (ref) v_u_4
            v_u_4:Create(p_u_662, TweenInfo.new(p664, Enum.EasingStyle.Quad, Enum.EasingDirection.In), p663):Play()
            task.delay(p664, function()
                -- upvalues: (copy) p_u_662
                p_u_662:Destroy()
            end)
        end
        v617.OnClientEvent:Connect(function(p666, p667, p668, p669, p670)
            -- upvalues: (ref) v_u_625, (ref) v_u_624, (copy) v_u_661, (ref) v_u_4, (copy) v_u_621, (copy) v_u_620, (copy) v_u_619, (copy) v_u_665
            if p666 == "Poster" then
                v_u_625 = v_u_625 + 1
                local v_u_671 = v_u_625
                if v_u_624 then
                    v_u_624.frame:Destroy()
                end
                local v_u_672, v_u_673, v_u_674 = v_u_661(p667, p668, p669, p670)
                v_u_624 = {
                    ["frame"] = v_u_672,
                    ["scale"] = v_u_673
                }
                v_u_4:Create(v_u_672, TweenInfo.new(0.45, Enum.EasingStyle.Back, Enum.EasingDirection.Out), {
                    ["Position"] = UDim2.fromScale(0.5, 0.45)
                }):Play()
                v_u_4:Create(v_u_672, TweenInfo.new(0.9, Enum.EasingStyle.Elastic, Enum.EasingDirection.Out), {
                    ["Rotation"] = 3
                }):Play()
                task.delay(2.2, function()
                    -- upvalues: (copy) v_u_671, (ref) v_u_625, (copy) v_u_672, (ref) v_u_4, (copy) v_u_673, (copy) v_u_674
                    if v_u_671 == v_u_625 and v_u_672.Parent then
                        v_u_4:Create(v_u_672, TweenInfo.new(0.5, Enum.EasingStyle.Quad, Enum.EasingDirection.InOut), {
                            ["Position"] = nil,
                            ["Rotation"] = -5,
                            ["Position"] = UDim2.new(1, -90, 0.45, 0)
                        }):Play()
                        v_u_4:Create(v_u_673, TweenInfo.new(0.5, Enum.EasingStyle.Quad, Enum.EasingDirection.InOut), {
                            ["Scale"] = v_u_674 * 0.5
                        }):Play()
                    end
                end)
                return
            elseif p666 == "Collected" and v_u_624 then
                v_u_625 = v_u_625 + 1
                local v_u_675 = v_u_624.frame
                v_u_624 = nil
                local v676 = {
                    ["Name"] = "Stamp",
                    ["AnchorPoint"] = nil,
                    ["Position"] = nil,
                    ["Size"] = nil,
                    ["Text"] = "COLLECTED",
                    ["FontFace"] = nil,
                    ["TextColor3"] = nil,
                    ["Rotation"] = -18,
                    ["ZIndex"] = 5,
                    ["AnchorPoint"] = Vector2.new(0.5, 0.5),
                    ["Position"] = UDim2.fromScale(0.5, 0.5),
                    ["Size"] = UDim2.fromOffset(250, 70),
                    ["FontFace"] = v_u_621,
                    ["TextColor3"] = v_u_620
                }
                local v677 = Instance.new("TextLabel")
                v677.BackgroundTransparency = 1
                v677.TextColor3 = v_u_619
                v677.TextScaled = true
                for v678, v679 in v676 do
                    v677[v678] = v679
                end
                v677.Parent = v_u_675
                local v680 = Instance.new("UIStroke")
                v680.ApplyStrokeMode = Enum.ApplyStrokeMode.Border
                v680.Thickness = 5
                v680.Color = v_u_620
                v680.Parent = v677
                local v681 = Instance.new("UIScale")
                v681.Scale = 2.2
                v681.Parent = v677
                v_u_4:Create(v681, TweenInfo.new(0.18, Enum.EasingStyle.Quad, Enum.EasingDirection.In), {
                    ["Scale"] = 1
                }):Play()
                task.delay(1.1, function()
                    -- upvalues: (copy) v_u_675, (ref) v_u_665
                    if v_u_675.Parent then
                        v_u_665(v_u_675, {
                            ["Position"] = v_u_675.Position - UDim2.fromScale(0, 0.8),
                            ["Rotation"] = v_u_675.Rotation + 25
                        }, 0.5)
                    end
                end)
            elseif p666 == "Gone" and v_u_624 then
                v_u_625 = v_u_625 + 1
                local v682 = v_u_624.frame
                v_u_624 = nil
                v_u_665(v682, {
                    ["Position"] = v682.Position + UDim2.fromScale(0.35, 0),
                    ["Rotation"] = v682.Rotation + 15
                }, 0.45)
            end
        end)
    end
end)
local function v_u_687() -- name: takeWeapon
    -- upvalues: (copy) v_u_11
    local v683 = v_u_11.Character
    local v684
    if v683 then
        v684 = v683:FindFirstChildOfClass("Humanoid")
    else
        v684 = v683
    end
    local v685 = v_u_11:FindFirstChildOfClass("Backpack")
    if v684 and (v684.Health > 0 and (v685 and not v683:FindFirstChildOfClass("Tool"))) then
        for _, v686 in v685:GetChildren() do
            if not (v686:IsA("Tool") and v686:GetAttribute("AttackTool")) then
                continue
            end
            v684:EquipTool(v686)
            return
        end
    end
end
v_u_11:WaitForChild("Backpack").ChildAdded:Connect(function(p688)
    -- upvalues: (copy) v_u_7, (copy) v_u_687
    if p688:IsA("Tool") and p688:GetAttribute("AttackTool") then
        local v689 = v_u_7.mode()
        if v689 == "Gamepad" or v689 == "Touch" then
            task.defer(v_u_687)
        end
    end
end)
v_u_11.CharacterAdded:Connect(function()
    -- upvalues: (copy) v_u_11, (copy) v_u_7, (copy) v_u_687
    v_u_11:WaitForChild("Backpack").ChildAdded:Connect(function(p690)
        -- upvalues: (ref) v_u_7, (ref) v_u_687
        if p690:IsA("Tool") and p690:GetAttribute("AttackTool") then
            local v691 = v_u_7.mode()
            if v691 == "Gamepad" or v691 == "Touch" then
                task.defer(v_u_687)
            end
        end
    end)
    local v692 = v_u_7.mode()
    if v692 == "Gamepad" or v692 == "Touch" then
        task.defer(v_u_687)
    end
end)
v_u_7.ModeChanged:Connect(function() -- name: padWeapon
    -- upvalues: (copy) v_u_7, (copy) v_u_687
    local v693 = v_u_7.mode()
    if v693 == "Gamepad" or v693 == "Touch" then
        task.defer(v_u_687)
    end
end)
v_u_2.InputBegan:Connect(function(p694)
    -- upvalues: (copy) v_u_687
    if p694.KeyCode == Enum.KeyCode.ButtonR2 then
        v_u_687()
    end
end)
local v695 = v_u_7.mode()
if v695 == "Gamepad" or v695 == "Touch" then
    task.defer(v_u_687)
end
