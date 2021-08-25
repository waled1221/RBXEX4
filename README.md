--[[

Made by Gerard#0001
https://v3rmillion.net/member.php?action=profile&uid=211143
Don't be pp head thx

]]

--antiafk lol
local VirtualUser = game:GetService("VirtualUser")
  	game:GetService("Players").LocalPlayer.Idled:connect(function()
    VirtualUser:Button2Down(Vector2.new(0, 0), workspace.CurrentCamera.CFrame)
    wait(1)
    VirtualUser:Button2Up(Vector2.new(0, 0), workspace.CurrentCamera.CFrame)
end)

-- Literally just get the Warehouse
local Warehouse = nil
repeat wait(0.1)
    for i,v in pairs(game:GetService("Workspace").GameLogic.Warehouses:GetChildren()) do
        if v.Owner.Value == game.Players.LocalPlayer then
            Warehouse = v
            break
        end
    end
until Warehouse ~= nil

--Makes sure that 'Boxes' is part of your character as otherwise it can mess with the script
if not game.Players.LocalPlayer.Character:FindFirstChild("Boxes") then
    repeat wait(0.5)
        if syn then 
            messagebox("Please: -Pick up any BOX from your RACK\n-PLACE IT BACK\nFor the script to work!!!\nFailure to do this will make it bug out", "GOD DAMN READ", 0) 
        else
            print("Please: -Pick up any BOX from your RACK\n-PLACE IT BACK\nFor the script to work!!!\nFailure to do this will make it bug out")
        end
    until game.Players.LocalPlayer.Character:FindFirstChild("Boxes") and #game.Players.LocalPlayer.Character:FindFirstChild("Boxes"):GetChildren() == 0
end

--Collect all a Box
function Box(typeofbox)
    local JackNumber = string.split(game:GetService("Players").LocalPlayer.PlayerGui.Main.Jack.Capacity.Text, "/")
    local JackNumber2 = string.split(game:GetService("Players").LocalPlayer.PlayerGui.Main.Jack.Capacity.Text, "/")
    if _G.Enabled and _G.Stock and tonumber(JackNumber[1]) < tonumber(JackNumber[2]) and tonumber(JackNumber[1]) < CountEmptySpace() then
        repeat wait()
            local Box = nil
            repeat wait()
                if typeofbox == nil or typeofbox == 'all' or typeofbox == 'All' then
                    Box = game:GetService("Workspace").GameLogic.SpawnedBoxes:GetChildren()[#game:GetService("Workspace").GameLogic.SpawnedBoxes:GetChildren()]
                else
                    Box = game:GetService("Workspace").GameLogic.SpawnedBoxes:WaitForChild(tostring(typeofbox))
                end
            until Box ~= nil
            local boxparent = Box.Parent
            JackNumber = string.split(game:GetService("Players").LocalPlayer.PlayerGui.Main.Jack.Capacity.Text, "/")
            JackNumber2 = string.split(game:GetService("Players").LocalPlayer.PlayerGui.Main.Jack.Capacity.Text, "/")
            if _G.Enabled and _G.Stock and tonumber(JackNumber[1]) < tonumber(JackNumber[2]) and tonumber(JackNumber[1]) < CountEmptySpace() then
                repeat wait() 
                    game.Players.LocalPlayer.Character:WaitForChild("HumanoidRootPart").CFrame = Box.MeshPart.CFrame
                    game:GetService("ReplicatedStorage").Remotes.PickupBox:InvokeServer(Box)
                    JackNumber2 = string.split(game:GetService("Players").LocalPlayer.PlayerGui.Main.Jack.Capacity.Text, "/")
                until tonumber(JackNumber2[1]) >= tonumber(JackNumber[1]) or tonumber(JackNumber2[1]) >= CountEmptySpace() or Box.Parent ~= boxparent or Box == nil or not _G.Stock or not _G.Enabled
            end
        until tonumber(JackNumber[1]) >= tonumber(JackNumber[2]) or tonumber(JackNumber[1]) >= CountEmptySpace() or not _G.Stock or not _G.Enabled
    end
end

--Gets the number of free storage rack spaces
function CountEmptySpace()
    local total = 0 
    for i,v in pairs(Warehouse.Racks:GetChildren()) do
        for z,x in pairs(v:GetChildren()) do
            if x.Name == 'PackagePosition' and x:FindFirstChild("Package") and x.Package.Value == nil then
                total = total + 1
            end
        end
    end
    return total
end

--Picks up boxes but with 'sellall' (set sellall to nil or false to only pick up one) as true it will sell every single box (or at least attempt to) that you own
function PickupBox(sellall)
    if _G.Enabled and _G.Deliver then
        for i,v in pairs(Warehouse.Racks:GetChildren()) do
            if _G.Enabled and _G.Deliver then
                for z,x in pairs(v.Boxes:GetChildren()) do
                    if _G.Enabled and _G.Deliver and game.Players.LocalPlayer.Character:FindFirstChild("Boxes") then
                        local Box = x
                        repeat wait(0.1)
                            game.Players.LocalPlayer.Character:WaitForChild("HumanoidRootPart").CFrame = Box.Main.CFrame
                            game:GetService("ReplicatedStorage").Remotes.PickupBox:InvokeServer(x)
                        until #game.Players.LocalPlayer.Character.Boxes:GetChildren() ~= 0 or not _G.Enabled or not _G.Stock
                    end
                    if _G.Enabled and _G.Deliver and sellall == true then
                        Deliver()
                    else
                        return
                    end
                end
            end
        end
    end
end

--Put any collect boxes to your rack to the max
function Stack(typeofbox) --Typeof can be a specific box. I might work on that in future
    if _G.Enabled and _G.Stock then
        for i = 1,2 do
            if _G.Enabled and _G.Stock then
                for i,v in pairs(Warehouse.Racks:GetChildren()) do
                    wait(1)
                    local AAA = string.split(v.Center.RackCapacity.Capacity.Text, "/")
                    if _G.Enabled and _G.Stock and AAA[1] <= AAA[2] then
                        repeat wait()
                            AAA = string.split(v.Center.RackCapacity.Capacity.Text, "/")
                            Box(typeofbox)
                            game.Players.LocalPlayer.Character:WaitForChild("HumanoidRootPart").CFrame = v.Center.CFrame
                            game:GetService("ReplicatedStorage").Remotes.StackRack:InvokeServer(v)
                        until AAA[1] == AAA[2] or not _G.Stock or not _G.Enabled
                        wait(1)
                    end
                end
                game.Players.LocalPlayer.Character:WaitForChild("HumanoidRootPart").CFrame = Warehouse.Teleport.CFrame
            end
        end
    end
end

function GetDelivery()
    for i,v in pairs(game:GetService("Workspace").GameLogic.DeliveryPoints:GetChildren()) do
        if  v:FindFirstChild("SelectionBox").Visible == true then
            return v
        end
    end
end

function Deliver()
    if _G.Enabled and _G.Deliver and #game.Players.LocalPlayer.Character.Boxes:GetChildren() > 0 and GetDelivery() and game.Players.LocalPlayer.Character:WaitForChild("HumanoidRootPart").CFrame then
        repeat
            wait(0.1)
            local d = GetDelivery()
            if d then
                game.Players.LocalPlayer.Character:WaitForChild("HumanoidRootPart").CFrame = d.CFrame
                game:GetService("ReplicatedStorage").Remotes.SellBox:InvokeServer(d)
            else
                game.Players.LocalPlayer.Character:WaitForChild("HumanoidRootPart").CFrame = CFrame.new(-387.181885, -0.876028776, 756.432495, 0.759318531, -8.05840443e-08, -0.650719106, 6.34102619e-08, 1, -4.98455499e-08, 0.650719106, -3.41361917e-09, 0.759318531)
            end
        until #game.Players.LocalPlayer.Character.Boxes:GetChildren() < 1 or not _G.Enabled or not _G.Deliver
    else
        repeat wait(0.1)
            game.Players.LocalPlayer.Character:WaitForChild("HumanoidRootPart").CFrame = CFrame.new(-387.181885, -0.876028776, 756.432495, 0.759318531, -8.05840443e-08, -0.650719106, 6.34102619e-08, 1, -4.98455499e-08, 0.650719106, -3.41361917e-09, 0.759318531)
        until GetDelivery()
        Deliver()
    end
end

function GetVehicle()
    local a = game:GetService("Players").LocalPlayer:FindFirstChild("PlayerGui"):FindFirstChild("VehicleMarker")
    local b = a.Adornee
    return b.Parent
end

while wait() and _G.Enabled do
    if _G.Deliver then
        PickupBox(true)
    end
    if _G.Stock then
        Stack('all')
    end
end
