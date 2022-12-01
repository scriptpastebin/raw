function TP(Point)
	local Ply = game.Players.LocalPlayer
	local Char = Ply.Character
	repeat
		Char.Humanoid:ChangeState(15)
		Char.HumanoidRootPart.CFrame = Point
		wait()
		Char.HumanoidRootPart.CFrame = Point
	until (Char.HumanoidRootPart.Position - Point.Position).Magnitude <= 20
end

spawn(function()
while task.wait() do
pcall(function()
if _G.AutoConfetti then
for i,v in pairs(game.Workspace.NPCs:GetChildren()) do
	game:GetService("ReplicatedStorage").Remotes.Celebration:InvokeServer('TalkNpc',workspace.NPCs[v.Name])
end
end
end)
end
end)

spawn(function()
while task.wait() do
pcall(function()
	if _G.AutoConfetti then
		TP(CFrame.new(-259.380126953125, 6.764979839324951, 5254.09912109375))
	--	wait(.5)
		TP(CFrame.new(-1932.3507080078125, 13.824933052062988, -11636.8515625))
	--	wait(.5)
		TP(CFrame.new(-929.170166015625, 7.802646160125732, -10826.3876953125))
	--	wait(.5)
		TP(CFrame.new(497.3184509277344, 24.76936149597168, -12418.55859375))
	--	wait(.5)
		TP(CFrame.new(-47.42597961425781, 16.97955322265625, -11992.779296875))
	--	wait(.5)
		TP(CFrame.new(2245.78271484375, 12.776296615600586, -6353.974609375))
		--wait(.5)
		TP(CFrame.new(-9515.0009765625, 142.1398468017578, 5534.05029296875))
	--	wait(.5)
		TP(CFrame.new(-9512.8896484375, 21.139892578125, 4641.02978515625))
	--	wait(.5)
		TP(CFrame.new(4732.6533203125, 51.589698791503906, -1414.252197265625))
	--	wait(.5)
		TP(CFrame.new(5277.3388671875, 602.0785522460938, 363.35223388671875))
		--wait(.5)
		TP(CFrame.new(3371.987060546875, 38.98302459716797, 1593.1405029296875))
	--	wait(.5)
		TP(CFrame.new(-2045.0103759765625, 38.138248443603516, -10041.935546875))
		--wait(.5)
		TP(CFrame.new(-5116.79931640625, 314.550537109375, -2964.70068359375))
	--	wait(.5)
		TP(CFrame.new(-4598.1025390625, 16.455780029296875, -2705.09619140625))
	--	wait(.5)
		TP(CFrame.new(-6069.4306640625, 16.455780029296875, -2160.60205078125))
	--	wait(.5)
		TP(CFrame.new(-11385.6767578125, 331.75823974609375, -10405.916015625))
	--	wait(.5)
		TP(CFrame.new(-9603.1591796875, 46.55657196044922, -8365.3828125))
	--	wait(.5)
		TP(CFrame.new(-12545.2353515625, 337.20330810546875, -7454.07470703125))
	--	wait(.5)
	end
end)
end
end)
spawn(function()
pcall(function()
local vu = game:GetService("VirtualUser")
game:GetService("Players").LocalPlayer.Idled:connect(function()
	vu:Button2Down(Vector2.new(0,0),workspace.CurrentCamera.CFrame)
	wait(1)
	vu:Button2Up(Vector2.new(0,0),workspace.CurrentCamera.CFrame)
end)
end)
end)
