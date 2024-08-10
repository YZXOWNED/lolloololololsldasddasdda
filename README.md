_G.HSz = 3.5
_G.Dsbl = true

local u = game:GetService("UserInputService")
local r = game:GetService("RunService")
local p = game:GetService("Players")
local l = p.LocalPlayer

local g = Instance.new("ScreenGui")
g.Parent = game.CoreGui

local f = Instance.new("Frame")
f.Size = UDim2.new(0, 200, 0, 150)
f.Position = UDim2.new(0, 10, 0, 10)
f.BackgroundColor3 = Color3.new(0.1, 0.1, 0.1)
f.Visible = false
f.Parent = g

local t = Instance.new("TextButton")
t.Size = UDim2.new(0, 180, 0, 50)
t.Position = UDim2.new(0, 10, 0, 10)
t.Text = "Toggle head"
t.Parent = f

local hl = Instance.new("TextLabel")
hl.Size = UDim2.new(0, 180, 0, 30)
hl.Position = UDim2.new(0, 10, 0, 70)
hl.Text = "Head Size: " .. _G.HSz
hl.Parent = f

local hs = Instance.new("TextButton")
hs.Size = UDim2.new(0, 180, 0, 30)
hs.Position = UDim2.new(0, 10, 0, 110)
hs.Text = "Adjust Head Size"
hs.Parent = f

u.InputBegan:Connect(function(i, gP)
    if not gP and i.KeyCode == Enum.KeyCode.K then
        f.Visible = not f.Visible
    end
end)

t.MouseButton1Click:Connect(function()
    _G.Dsbl = not _G.Dsbl
end)

hs.MouseButton1Click:Connect(function()
    if _G.HSz < 25 then
        _G.HSz = _G.HSz + 0.5
    else
        _G.HSz = 0.5
    end
    hl.Text = "Head Size: " .. _G.HSz
end)

r.RenderStepped:Connect(function()
    if not _G.Dsbl then
        for _, v in next, p:GetPlayers() do
            if v ~= l then
                pcall(function()
                    if v.Character and v.Character:FindFirstChild("Head") then
                        v.Character.Head.Size = Vector3.new(_G.HSz, _G.HSz, _G.HSz)
                        v.Character.Head.Transparency = 0.4
                        v.Character.Head.BrickColor = BrickColor.new("Red")
                        v.Character.Head.Material = "Neon"
                        v.Character.Head.CanCollide = false
                        v.Character.Head.Massless = true
                    end
                end)
            end
        end
    end
end)

local b = {}

function b.c(p)
    if p == l then return end
    if b[p] then return end

    local ch = p.Character
    if ch and ch:IsDescendantOf(workspace) and ch:FindFirstChild("Humanoid") and ch.Humanoid.Health > 0 then
        local box = Drawing.new("Square")
        box.Visible = false
        box.Color = Color3.fromRGB(255, 255, 255)
        box.Filled = false
        box.Thickness = 1

        local con
        con = r.RenderStepped:Connect(function()
            if ch and ch:IsDescendantOf(workspace) and ch:FindFirstChild("Humanoid") and ch.Humanoid.Health > 0 then
                local pos, vis = workspace.CurrentCamera:WorldToViewportPoint(ch.HumanoidRootPart.Position)
                local sf = 1 / (pos.Z * math.tan(math.rad(workspace.CurrentCamera.FieldOfView * 0.5)) * 2) * 100
                local w, h = math.floor(40 * sf), math.floor(62 * sf)

                if vis then
                    box.Size = Vector2.new(w, h)
                    box.Position = Vector2.new(pos.X - box.Size.X / 2, pos.Y - box.Size.Y / 2)
                    box.Visible = true
                else
                    box.Visible = false
                end
            else
                box:Destroy()
                con:Disconnect()
                b[p] = nil
            end
        end)

        b[p] = {
            Box = box,
            Connection = con
        }
    end
end

local function uE()
    for _, p in pairs(p:GetPlayers()) do
        if p.Character then
            b.c(p)
        end
    end
end

r.RenderStepped:Connect(uE)

p.PlayerRemoving:Connect(function(p)
    if b[p] then
        b[p].Box:Destroy()
        b[p].Connection:Disconnect()
        b[p] = nil
    end
end)

l.CharacterAdded:Connect(function()
    if b[l] then
        b[l].Box:Destroy()
        b[l].Connection:Disconnect()
        b[l] = nil
    end
end)

local function API_Check()
    if Drawing == nil then
        return "No"
    else
        return "Yes"
    end
end

local Find_Required = API_Check()

if Find_Required == "No" then
    game:GetService("StarterGui"):SetCore("SendNotification",{
        Title = "Exunys Developer";
        Text = "Crosshair script could not be loaded because your exploit is unsupported.";
        Duration = math.huge;
        Button1 = "OK"
    })

    return
end

local Players = game:GetService("Players")
local RunService = game:GetService("RunService")
local UserInputService = game:GetService("UserInputService")
local Camera = workspace.CurrentCamera

local Typing = false

local ViewportSize_ = Camera.ViewportSize / 2
local Axis_X, Axis_Y = ViewportSize_.X, ViewportSize_.Y

local HorizontalLine = Drawing.new("Line")
local VerticalLine = Drawing.new("Line")

_G.SendNotifications = true   -- If set to true then the script would notify you frequently on any changes applied and when loaded / errored. (If a game can detect this, it is recommended to set it to false)
_G.DefaultSettings = false   -- If set to true then the script would create a crosshair with the default settings regardless of any changes.
_G.ToMouse = false   -- If set to true then the crosshair will be positioned to your mouse cursor's position. If set to false it will be positioned to the center of your screen.

_G.CrosshairVisible = true   -- If set to true then the crosshair would be visible and vice versa.
_G.CrosshairSize = 20   -- The size of the crosshair.
_G.CrosshairThickness = 1   -- The thickness of the crosshair.
_G.CrosshairColor = Color3.fromRGB(0, 255, 0)   -- The color of the crosshair
_G.CrosshairTransparency = 1   -- The transparency of the crosshair.

_G.DisableKey = Enum.KeyCode.Q   -- The key that enables / disables the crosshair.

RunService.RenderStepped:Connect(function()
    local Real_Size = _G.CrosshairSize / 2

    HorizontalLine.Color = _G.CrosshairColor
    HorizontalLine.Thickness = _G.CrosshairThickness
    HorizontalLine.Visible = _G.CrosshairVisible
    HorizontalLine.Transparency = _G.CrosshairTransparency
    
    VerticalLine.Color = _G.CrosshairColor
    VerticalLine.Thickness = _G.CrosshairThickness
    VerticalLine.Visible = _G.CrosshairVisible
    VerticalLine.Transparency = _G.CrosshairTransparency
    
    if _G.ToMouse == true then
        HorizontalLine.From = Vector2.new(UserInputService:GetMouseLocation().X - Real_Size, UserInputService:GetMouseLocation().Y)
        HorizontalLine.To = Vector2.new(UserInputService:GetMouseLocation().X + Real_Size, UserInputService:GetMouseLocation().Y)
        
        VerticalLine.From = Vector2.new(UserInputService:GetMouseLocation().X, UserInputService:GetMouseLocation().Y - Real_Size)
        VerticalLine.To = Vector2.new(UserInputService:GetMouseLocation().X, UserInputService:GetMouseLocation().Y + Real_Size)
    elseif _G.ToMouse == false then
        HorizontalLine.From = Vector2.new(Axis_X - Real_Size, Axis_Y)
        HorizontalLine.To = Vector2.new(Axis_X + Real_Size, Axis_Y)
    
        VerticalLine.From = Vector2.new(Axis_X, Axis_Y - Real_Size)
        VerticalLine.To = Vector2.new(Axis_X, Axis_Y + Real_Size)
    end
end)

if _G.DefaultSettings == true then
    _G.CrosshairVisible = true
    _G.CrosshairSize = 25
    _G.CrosshairThickness = 1
    _G.CrosshairColor = Color3.fromRGB(40, 90, 255)
    _G.CrosshairTransparency = 0.15
    _G.DisableKey = Enum.KeyCode.Q
end

UserInputService.TextBoxFocused:Connect(function()
    Typing = true
end)

UserInputService.TextBoxFocusReleased:Connect(function()
    Typing = false
end)

UserInputService.InputBegan:Connect(function(Input)
    if Input.KeyCode == _G.DisableKey and Typing == false then
        _G.CrosshairVisible = not _G.CrosshairVisible
        
        if _G.SendNotifications == true then
            game:GetService("StarterGui"):SetCore("SendNotification",{
                Title = "Exunys Developer";
                Text = "The crosshair's visibility is now set to "..tostring(_G.CrosshairVisible)..".";
                Duration = 5;
            })
        end
    end
end)
