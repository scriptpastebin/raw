local SELL_TRESHOLD = nil -- ONLY CHANGE THIS IF YOU HAVE INF BACKPACK.
 
local LocalPlayer = game.Players.LocalPlayer
local Rebirths = LocalPlayer.leaderstats.Rebirths
 
print("Loading Mining Simulator GUI")
local OldGui = game.Players.LocalPlayer.PlayerGui:FindFirstChild("Nice Flex But OK")
if OldGui then
	OldGui:Destroy()
end
 
local ScreenGUI = game:GetObjects("rbxassetid://4835603667")[1]
ScreenGUI.Enabled = true
ScreenGUI.Parent = LocalPlayer.PlayerGui
ScreenGUI.Name = "Nice Flex But OK"
 
local TopBar = ScreenGUI.Frame
local MainFrame = TopBar.Frame
local ExitButton = TopBar.ExitButton
local OpenButton = TopBar.Parent.OpenButton
 
local AutoSellToggle = MainFrame.AutoSellToggle
local FastMineToggle = MainFrame.FastMineToggle
local AutoMineToggle = MainFrame.AutoMineToggle
local AutoBackpackToggle = MainFrame.AutoBackpackToggle
local AutoToolsToggle = MainFrame.AutoToolsToggle
local AutoRebirthToggle = MainFrame.AutoRebirthToggle
 
local Remote = game.ReplicatedStorage.Network:InvokeServer()
 
local Toggles = {
	AutoSell = false,
	FastMine = false,
	AutoMine = false,
	AutoBackpack = false,
	AutoTools = false,
	AutoRebirth = false
}
 
local InventoryAmount = LocalPlayer.PlayerGui.ScreenGui.StatsFrame2.Inventory.Amount
local CoinsAmount = game.Players.LocalPlayer.leaderstats.Coins
 
local function GetCoinsAmount()
	local Amount = CoinsAmount.Value
	Amount = Amount:gsub(',', '')
 
	return tonumber(Amount)
end
 
local function GetInventoryAmount()
	local Amount = InventoryAmount.Text
	local Amount2 = InventoryAmount.Text
	Amount = Amount:gsub('%s+', '')
	Amount2 = Amount2:gsub('%s+', '')
	Amount = Amount:gsub(',', '')
 
	local stringTable = Amount:split("/")
	local stringTable2 = Amount2:split("/")
	return tonumber(stringTable[1]), tonumber(stringTable[2]), stringTable2[1], stringTable2[2]
end
 
local Remote = game.ReplicatedStorage.Network:InvokeServer()
 
local Blocks = game.Workspace.Blocks
 
AutoMineToggle.MouseButton1Click:Connect(function()
	if Remote then
		Toggles["AutoMine"] = not Toggles["AutoMine"]
		if Toggles["AutoMine"] == true then
			AutoMineToggle.Text = "Disable"
			while Toggles["AutoMine"] do
				local Character = LocalPlayer.Character
				local HumanoidRootPart = Character:FindFirstChild("HumanoidRootPart")
				if HumanoidRootPart then
					local min = HumanoidRootPart.CFrame + Vector3.new(-10,-10,-10)
					local max = HumanoidRootPart.CFrame + Vector3.new(10,10,10)
					local region = Region3.new(min.Position, max.Position)
					local parts = workspace:FindPartsInRegion3WithWhiteList(region, {game.Workspace.Blocks}, 100) --  ignore part
 
					for each, block in pairs(parts) do
						if block:IsA("BasePart") then
							local BlockModel = block.Parent
				            Remote:FireServer("MineBlock",{{BlockModel}})
							wait()
						end
					end
				end
				if not ScreenGUI then
					break
				end
				wait()
			end
		else
			AutoMineToggle.Text = "Enable"
		end
	end
end)
AutoRebirthToggle.MouseButton1Click:Connect(function()
	if Remote then
		Toggles["AutoRebirth"] = not Toggles["AutoRebirth"]
		if Toggles["AutoRebirth"] == true then
			AutoRebirthToggle.Text = "Disable"
		else
			AutoRebirthToggle.Text = "Enable"
		end
	end
end)
AutoBackpackToggle.MouseButton1Click:Connect(function()
	if Remote then
		Toggles["AutoBackpack"] = not Toggles["AutoBackpack"]
		if Toggles["AutoBackpack"] == true then
			AutoBackpackToggle.Text = "Disable"
		else
			AutoBackpackToggle.Text = "Enable"
		end
	end
end)
AutoToolsToggle.MouseButton1Click:Connect(function()
	if Remote then
		Toggles["AutoTools"] = not Toggles["AutoTools"]
		if Toggles["AutoTools"] == true then
			AutoToolsToggle.Text = "Disable"
		else
			AutoToolsToggle.Text = "Enable"
		end
	end
end)
AutoSellToggle.MouseButton1Click:Connect(function()
	if Remote then
		Toggles["AutoSell"] = not Toggles["AutoSell"]
		if Toggles["AutoSell"] == true then
			AutoSellToggle.Text = "Disable"
		else
			AutoSellToggle.Text = "Enable"
		end
	end
end)
FastMineToggle.MouseButton1Click:Connect(function()
	if Remote then
		Toggles["FastMine"] = not Toggles["FastMine"]
		if Toggles["FastMine"] == true then
			FastMineToggle.Text = "Disable"
			for each, block in pairs(game.Workspace.Blocks:GetChildren()) do
				local Stats = block:FindFirstChild("Stats")
				if Stats then
					local Multiplier = Stats:FindFirstChild("Multiplier")
					if Multiplier then
						local ActualMultiplier = Stats:FindFirstChild("ActualMultiplier")
						if not ActualMultiplier then
							local ActualMultiplier = Multiplier:Clone()
							ActualMultiplier.Name = "ActualMultiplier"
							ActualMultiplier.Parent = Stats
						end
						Multiplier.Value = -1337
					end
				end
			end
		else
			FastMineToggle.Text = "Enable"
 
			for each, block in pairs(game.Workspace.Blocks:GetChildren()) do
				local Stats = block:FindFirstChild("Stats")
				if Stats then
					local Multiplier = Stats:FindFirstChild("Multiplier")
					if Multiplier then
						local ActualMultiplier = Stats:FindFirstChild("ActualMultiplier")
						if ActualMultiplier then
							Multiplier.Value = ActualMultiplier.Value
						end
					end
				end
			end
		end
	end
end)
CoinsAmount.Changed:Connect(function(Change)
	if ScreenGUI then
		if Toggles["AutoRebirth"] then
			local Amount = GetCoinsAmount()
			if Amount >= (10000000 * (Rebirths.Value + 1)) then
				Remote:FireServer("Rebirth",{{					                }})
			end
		end
		if Toggles["AutoBackpack"] then
			for i = 3, 50 do
				Remote:FireServer("BuyItem",{{"Backpack",i}})
				wait(0.1)			
			end
		end
		if Toggles["AutoTools"] then
			for i = 1, 50 do
				Remote:FireServer("BuyItem",{{"Tools",i}})
				wait(0.1)			
			end
		end
	end
end)
 
InventoryAmount.Changed:Connect(function(Change)
	if ScreenGUI then
		if Change == "Text" then
			if Toggles["AutoSell"] then
				local Amount, MaxAmount, AmountComma, MaxAmountComma2 = GetInventoryAmount()
				if SELL_TRESHOLD ~= nil then
					MaxAmount = SELL_TRESHOLD
				end
				if Amount >= MaxAmount then
					local Character = LocalPlayer.Character
					if Character then
						local HumanoidRootPart = Character:FindFirstChild("HumanoidRootPart")
						if HumanoidRootPart then
							local SavedLocation = HumanoidRootPart.CFrame
							local SavedText = InventoryAmount.Text
							while InventoryAmount.Text == SavedText do
								HumanoidRootPart.CFrame = CFrame.new(-116, 13, 38)
								Remote:FireServer("SellItems",{{               }})
								wait()
						 	end
							HumanoidRootPart.Anchored = true
							while HumanoidRootPart.CFrame ~= SavedLocation do
								HumanoidRootPart.CFrame = SavedLocation
							wait()
							end
							HumanoidRootPart.Anchored = false
						end
					end
				end
			end
		end
	end
end)
 
game.Workspace.Blocks.ChildAdded:Connect(function(block)
	if ScreenGUI then
		if Toggles["FastMine"] then
			local Stats = block:WaitForChild("Stats")
			if Stats then
				local Multiplier = Stats:WaitForChild("Multiplier")
				if Multiplier then
					local ActualMultiplier = Stats:FindFirstChild("ActualMultiplier")
					if not ActualMultiplier then
						local ActualMultiplier = Multiplier:Clone()
						ActualMultiplier.Name = "ActualMultiplier"
						ActualMultiplier.Parent = Stats
					end
					Multiplier.Value = -1337
				end
			end
		end
	end
end)
 
ExitButton.MouseButton1Click:Connect(function()
	TopBar.Visible = false
	OpenButton.Visible = true
end)
 
OpenButton.MouseButton1Click:Connect(function()
	TopBar.Visible = true
	OpenButton.Visible = false
end)
print("Follow Trush on ytb")
