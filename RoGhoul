--[[
	FrostHook RoGhoul Trainer (OPEN SOURCED)
	2020/10/29
	Care for Potential Bugs, this source hasn't been touched in a long while.
]]

local function GetExecutor()
    if SentinelBuy then
        return "Sentinel";
    elseif LUAPROTECT ~= nil then
        return "SirHurt";
    elseif AES ~= nil then
        return "ProtoSmasher";
    elseif syn_crypt_encrypt ~= nil then
        return "Synapse";
    else
        return "UNDEFINED";
    end
end

CoreGui = game:GetService("CoreGui")
HttpService = game:GetService("HttpService")
LocalPlayer = game:GetService("Players").LocalPlayer

Keys = {
	key_event = "",
	train_event = "",
}

NumberTextValues = {
	[0] = "Zero",
	[1] = "One",
	[2] = "Two",
	[3] = "Three",
	[4] = "Four",
	[5] = "Five",
	[6] = "Six",
	[7] = "Seven",
	[8] = "Eight",
	[9] = "Nine"
}

VERSION = "4.0.1"
client_log = "";
DEV = 1;

local _S,_S2,_S3 = "klimrettuB","der kraD","YTIROIRP_ETAIDEMMI"

-- BEFORE RUN OPERATIONS

if CoreGui:FindFirstChild("[FH] Ro-Ghoul Trainer") or CoreGui:FindFirstChild("Preloading") then
	print("Ro-Ghoul Trainer is already running.");
	return
end

-- VERIFY TEAM SELECTED

do
	local PlayerFolder = LocalPlayer:WaitForChild("PlayerFolder", 0.5);
	if not PlayerFolder then
		local LoadingTemplate = Instance.new("ScreenGui", CoreGui);
		LoadingTemplate.Name = "Preloading"
		local Label = Instance.new("TextLabel", LoadingTemplate)
		Label.Text = "[FH] Ro-Ghoull Trainer - Please Select a Team."
		Label.Size = UDim2.new(1,0,0,50);
		Label.Position = UDim2.new(0,0,0,125)
		Label.TextStrokeTransparency = 0.6;
		Label.Transparency = 0.7;
		Label.TextColor3 = Color3.new(1,1,1)
		Label.TextSize = 18;
		Label.Font = 4;

		repeat wait() until LocalPlayer:FindFirstChild("PlayerFolder")

		LoadingTemplate:Destroy();
	end
end

-- RUNTIME DEFINITIONS

Remotes = game:GetService("ReplicatedStorage"):WaitForChild("Remotes", 1);
CharacterRemotes = LocalPlayer.Character:FindFirstChild("Remotes") or LocalPlayer.Character:WaitForChild("Remotes", 5);
KeyEvent = CharacterRemotes.KeyEvent;
KillScript,ClosestTarget = nil,nil;
AutoFarmToggle,AutoTraining,StatFocusToggle = false,false,false;

stats = {
	RC = {
		Start = 0,
		Gain = 0
	},
	Yen = {
		Start = 0,
		Gain = 0
	},
	Rep = {
		Start = 0,
		Gain = 0
	},
	Kill = {
		Start = 0,
		Gain = 0
	}
}

function appendLog(string)
	if string.len(client_log) > 200000 then
		client_log = "[CLIENT LOG RESET]";
	end
	client_log = client_log..tostring(string).."\n"
end

_G.GrabLog = function()
	syn.write_clipboard(client_log);
end

-- MODULES

local interface, PlayerMover, trainer, farm, NPCEsp, config;

-- INTERFACE MODULE

interface = {
	Screen = game:GetObjects("rbxassetid://3473501099")[1],
	CompleteTrainerFrame = nil,
	ExampleCTCLabel = nil,
	CompleteTrainerCount = nil,
	StartTrainingBtn = nil,
	StopTrainingBtn = nil,
	TrainerProgressBar = nil,
	AutoTrainStatus = nil,
	AutoFarmStatus = nil,
	ReqFrame = nil,
	P3Contents = nil,
	RunTimeDisp = nil,
	QuestOutput = nil,
	StopFarmingBtn = nil,
	StartFarmingBtn = nil,
	MonsterTag = nil,
	PlayerTag = nil,
	ShopIndicatorNames = {
		["Ghoul"] = {"SurgeonIndicator", "Kakuhou Surgeon", nil},
		["CCG"] = {"ShopIndicator", "Drawer", nil},
		["Mask"] = {"ShopIndicator", "UtaMaskShop", nil},
	},
	StatsToView = {"Weapon","Durability","Physical","Speed"},
	Connections = {},
	StatConnections = {},
	VPRotationConstant = 0,
	ViewingModel = nil,
	VPCamera = nil,
}

do -- metadata for interface.
	local metadata = {}
	local methods = {
		Destroy = function(self)
			if self.Screen then
				self.Screen:Destroy();
			end
			for a, b in next, self.Connections do
				b:Disconnect();
			end
			for a,b in next, self.StatConnections do
				b:Disconnect();
			end
			setmetatable(self, {})
			interface = nil;
		end,
		StopFarmButtonClick = function(self)
			self.StopFarmingBtn.Visible = false;
			self.StartFarmingBtn.Visible = true;

			stats.RC.Start = 0;
			stats.RC.Gain = 0;
			stats.Yen.Start = 0;
			stats.Yen.Gain = 0;
			stats.Rep.Start = 0;
			stats.Rep.Gain = 0;
			stats.Kill.Start = 0;
			stats.Kill.Gain = 0;

			farm.RunTime = nil

			AutoFarmToggle = false;
			farm:Start();
		end,
		StartFarmButtonClick = function(self)
			self.StopFarmingBtn.Visible = true;
			self.StartFarmingBtn.Visible = false;

			stats.Kill.Start = 0;
			stats.RC.Start = tonumber(farm.PlayerStats.RC.Value);
			stats.Yen.Start = tonumber(farm.PlayerStats.Yen.Value);
			stats.Rep.Start = tonumber(farm.PlayerStats.Reputation.Value);

			farm.RunTime = tick();
			AutoFarmToggle = true;
			farm:Start();
		end,
		UpdateCTFrame = function(self)
			local CompleteTrainerFrame = self.CompleteTrainerFrame;
			local ExampleCTCLabel = self.ExampleCTCLabel;
			local CompleteTrainerCount = self.CompleteTrainerCount;
			local StartTrainingBtn = self.StartTrainingBtn;
			local StopTrainingBtn = self.StopTrainingBtn;

			for _, object in next, CompleteTrainerFrame:GetChildren() do
				if not object:IsA("UIListLayout") and not object:IsA("UIPadding") then
					object:Destroy();
				end
			end

			if tonumber(trainer.Position) == #trainer.Trainers.Available and tonumber(trainer.Progress) >= 100 then
				for i = 1, trainer.Position, 1 do
					local Label = ExampleCTCLabel:Clone();
					Label.Parent = CompleteTrainerFrame;
					Label.Text = tostring(trainer.Trainers.Available[i]);
				end
				CompleteTrainerCount.Text = tostring(#trainer.Trainers.Available).."/"..tostring(#trainer.Trainers.Available);
				CompleteTrainerFrame.CanvasSize = UDim2.new(0,0,0,#trainer.Trainers.Available*25)
				StartTrainingBtn.Visible = false;
				StopTrainingBtn.Visible = false;
			else
				for i = 1, trainer.Position-1, 1 do
					local Label = ExampleCTCLabel:Clone();
					Label.Parent = CompleteTrainerFrame;
					Label.Text = tostring(trainer.Trainers.Available[i]);
				end
				CompleteTrainerCount.Text = tostring(trainer.Position-1).."/"..tostring(#trainer.Trainers.Available);
				CompleteTrainerFrame.CanvasSize = UDim2.new(0,0,0,(trainer.Position-1)*25)
			end
		end,
		SetTrainStatus = function(self, string)
			self.AutoTrainStatus.Text = tostring(string);
		end,
		UpdateProgressBar = function(self)
			local TrainerProgressBar = self.TrainerProgressBar;
			local PercentLabel = TrainerProgressBar.TPBPercentDisplay;
			local Bar = TrainerProgressBar.TrainerProgressBar;
			PercentLabel.Text = tostring(trainer.Progress).."%"
			Bar.Size = UDim2.new((trainer.Progress/100),0,1,0)
		end,
		ShowStats = function(self, Player)
			for a,b in next, self.StatConnections do
				pcall(function()
					b:Disconnect();
				end);
			end
			self.StatConnections = {};

			local PlayerName = self.StatFrame.Title;
			PlayerName.Text = tostring(Player);

			local TheirFolder = Player.PlayerFolder;
			local TheirStats = TheirFolder.Stats;
			local MaxPoints = config.MaxLevel * 3;
			local ContentFrame = self.StatFrame.Contents;

			local CharacterPort = ContentFrame.CharacterPort;

			if CharacterPort:FindFirstChild("PlayerModel") then
				CharacterPort.PlayerModel:Destroy();
			end

			Player.Character.Archivable = true;
			self.ViewingModel = Player.Character:Clone();
			self.ViewingModel.Name = "PlayerModel";
			Player.Character.Archivable = false;

			for _,Object in pairs(self.ViewingModel:GetChildren()) do
				if not Object:IsA("BasePart") and not Object:IsA("Accoutrement") and not Object:IsA("Clothing") and not Object:IsA("Model") then
					Object:Destroy();
				end
			end

			if self.ViewingModel.PrimaryPart then
				
				self.ViewingModel:SetPrimaryPartCFrame(CFrame.new(0,0,0))
				self.ViewingModel.Parent = CharacterPort;
				self.VPCamera.Parent = CharacterPort;
				self.VPCamera.CameraSubject = self.ViewingModel;
				CharacterPort.CurrentCamera = self.VPCamera;

				local LevelDisplay = ContentFrame.Level;
				LevelDisplay.Value.Text = TheirStats.Level.Value;
				table.insert(self.StatConnections, TheirStats.Level.Changed:connect(function(new)
					LevelDisplay.Value.Text = new;
				end))

				local XpDisplay = ContentFrame.XpBar;

				local function UpdateXpBar()
					if XpDisplay:FindFirstChild("TextLabel") then
						local CurrentXp = tonumber(TheirStats.Experience.Value)
						local MaxXp = tonumber(TheirStats.Level.Value) * 20;
						if CurrentXp/MaxXp ~= 1 then
							XpDisplay.TextLabel.Text = toSuffixString(CurrentXp).."/"..toSuffixString(MaxXp)
						else
							XpDisplay.TextLabel.Text = "MAX"
						end
						XpDisplay.Set.Size = UDim2.new(CurrentXp/MaxXp,0,1,0)
					end
				end

				UpdateXpBar();

				table.insert(self.StatConnections, TheirStats.Experience.Changed:connect(UpdateXpBar))

				local YenDisplay = ContentFrame.Yen;
				YenDisplay.Value.Text = toSuffixString(tonumber(TheirStats.Yen.Value));
				table.insert(self.StatConnections, TheirStats.Yen.Changed:connect(function(new)
					YenDisplay.Value.Text = toSuffixString(tonumber(new));
				end))

				local RCDisplay = ContentFrame.RC;
				RCDisplay.Value.Text = toSuffixString(tonumber(TheirStats.RC.Value));
				table.insert(self.StatConnections, TheirStats.RC.Changed:connect(function(new)
					RCDisplay.Value.Text = toSuffixString(tonumber(new));
				end))

				local ReputationDisplay = ContentFrame.Reputation;
				ReputationDisplay.Value.Text = toSuffixString(tonumber(TheirStats.Reputation.Value));
				table.insert(self.StatConnections, TheirStats.Reputation.Changed:connect(function(new)
					ReputationDisplay.Value.Text = toSuffixString(tonumber(new));
				end))

				local WeaponDisplay = ContentFrame.Weapon;
				WeaponDisplay.Text = TheirFolder.Customization.Weapon.Value;
				table.insert(self.StatConnections, TheirFolder.Customization.Weapon.Changed:connect(function(new)
					WeaponDisplay.Text = new;
				end))

				local StatsDisplay = ContentFrame.Stats;

				for _,Stat in pairs(self.StatsToView) do
					local TheirStat = TheirStats:FindFirstChild(Stat)
					local LocalStat = StatsDisplay:FindFirstChild(Stat)
					if TheirStat and LocalStat then
						LocalStat.Bar.Set:TweenSize(UDim2.new((tonumber(TheirStat.Value)/MaxPoints),0,1,0), "Out", "Quad", 0.5, true)
						LocalStat.Bar.Actual.Text = TheirStat.Value;
						table.insert(self.StatConnections, TheirStat.Changed:connect(function(new)
							LocalStat.Bar.Set:TweenSize(UDim2.new((tonumber(new)/MaxPoints),0,1,0), "Out", "Quad", 0.5, true)
							LocalStat.Bar.Actual.Text = new;
						end))
					end
				end

				local BreakLoop = false;

				table.insert(self.StatConnections, self.MoveTo.MouseButton1Down:connect(function()
					if Player and not farm.RunTime then
						if Player.Character and LocalPlayer.Character then
							BreakLoop = false;
							local TargetRoot = Player.Character.PrimaryPart;
							local SelfRoot = LocalPlayer.Character.PrimaryPart;
							if TargetRoot and SelfRoot then
								local Distance = math.huge;
								PlayerMover:Enable();
								self.MoveTo.Visible = false;
								self.MoveToCancel.Visible = true;
								repeat
									wait();
									PlayerMover:NextLocation(TargetRoot.CFrame);
									Distance = (SelfRoot.CFrame.p - TargetRoot.CFrame.p).magnitude
									if BreakLoop or not Player.Character or not LocalPlayer.Character then
										break;
									end
								until Distance <= 10 or not SelfRoot or not TargetRoot or self.Dead
								self.MoveTo.Visible = true;
								self.MoveToCancel.Visible = false;
								PlayerMover:Disable();
							end
						end
					end
				end))

				table.insert(self.StatConnections, self.MoveToCancel.MouseButton1Down:connect(function()
					BreakLoop = true;
				end))

				table.insert(self.StatConnections, Player.AncestryChanged:connect(function()
					StatFrame.Visible = false;
					for i,Connection in pairs(self.StatConnections) do
						pcall(function()
							Connection:Disconnect();
						end)
					end
					self.StatConnections = {};
				end))

				self.StatFrame.Visible = true;
			end
		end,
		OpenShop = function(self, ShopType)
			local ShopInd, ShopIndData;
			if ShopType == "Weapon" then
				ShopIndData = self.ShopIndicatorNames[trainer.TeamString]
			elseif ShopType == "Mask" then
				ShopIndData = self.ShopIndicatorNames["Mask"]
			end

			ShopInd = ShopIndData[3]

			if ShopInd then
				fireclickdetector(ShopInd);
			else
				warn("[N]: Shop Indicator Not Found..?")
			end
		end,
		SetFarmStatus = function(self, string)
			self.AutoFarmStatus.Text = tostring(string);
		end
	}

	metadata.__index = function(self, i)
		if methods[i] then
			return function(s, ...)
				return methods[i](self, ...)
			end
		end
	end

	metadata.__call = function(self)

		-- Make sure doesnt get init twice.
		if CoreGui:FindFirstChild("[FH] Ro-Ghoul Trainer") then
			print("Interface is already initialized.");
			return false;
		end

		if not self.Screen:FindFirstChild("VerticalLinee", true) then
			--self.Screen:Destroy();
		else
			print("Found it.")
		end

		self.Screen.Name = "[FH] Ro-Ghoul Trainer"
		self.Screen.Parent = CoreGui;
		local Window = self.Screen.Window;
		Window.Active = true;
		Window.Draggable = true;
		local WindowContents = Window.Contents;
		do
			local vers = WindowContents.Brand.Version;
			local windowsize = WindowContents.Brand.Version.Size
			vers.Size = UDim2.new(0, 40, windowsize.Height.Scale,windowsize.Height.Offset);
			vers.Text = "v"..tostring(VERSION);
		end
		local PageTabs = WindowContents.PageList;
		local InterfacePages = WindowContents.InterfacePages;
		InterfacePages.InterfacePageLayout.ScrollWheelInputEnabled = false;
		InterfacePages.InterfacePageLayout.TouchInputEnabled = false;
		InterfacePages.InterfacePageLayout.GamepadInputEnabled = false;
		local Pages = InterfacePages.Pages;
		local Info = WindowContents.Info;
		local Dev = WindowContents.Dev;
		local MainWindowClose = WindowContents.Close;
		local MainWindowOpen = self.Screen.Open;
		local KillSwitch = InterfacePages.Developer.KillSwitch;
		local ReturnMenuBtns = {
			Btn1 = InterfacePages.Developer.ReturnBtn_Dev;
			Btn2 = InterfacePages.Info.ReturnBtn_Info;
		}

		local SecondsToHMS;
		local toSuffixString;

		-- Viewports;

		self.VPCamera = Instance.new("Camera")
		self.VPCamera.Name = "FH_VPC"

		-- Other ELements

		MainWindowClose.MouseButton1Down:connect(function()
			MainWindowOpen.Visible = true;
			Window.Visible = false;
		end)

		MainWindowOpen.MouseButton1Down:connect(function()
			MainWindowOpen.Visible = false;
			Window.Visible = true;
		end)

		-- Page 1 Variables (General)
		local Page1 = Pages.Page1;
		local MaskFrame = Page1.MaskFrame;
		local MaskInput = MaskFrame.MaskInputFrame.M_Input;
		local MaskApply = MaskFrame.M_Apply;
		local MaskNameFiltered = MaskFrame.Filtered_Out;

		local ShopFrame = Page1.ShopFrame;
		local ShopOpen = ShopFrame.OpenShop;

		local MaskShopFrame = Page1.MaskShopFrame;
		local MaskShopOpen = MaskShopFrame.OpenMaskShop;

		local MaskFunction = Remotes.HySy.Nickname;

		local EspFrame = Page1.ESPFrame;
		EspFrame.Visible = true;
		local PlayerTag = EspFrame.PlayerGui;
		PlayerTag.Parent = nil;
		local MonsterTag = EspFrame.MonsterGui;
		MonsterTag.Parent = nil;
		MonsterTag.MonsterMain.Active = false;
		MonsterTag.MaxDistance = 1000;
		local EspDistanceToggle = EspFrame.ESPDistanceToggle;
		local EspToggleFrame = EspFrame.ESPTFrame;
		EspToggleFrame.ClipsDescendants = true;
		local ExampleToggle = EspToggleFrame.ExampleEspToggle;
		ExampleToggle.Parent = nil;
		local EspTierToggle = EspFrame.NPCTierToggle;
		local NametagToggle = EspFrame.ESPNametagToggle;
		local PlayerAbilityToggle = EspFrame.PABToggle;
		local PlayerHealthToggle = EspFrame.PHPToggle;


		local StatFrame = self.Screen.StatFrame;
		local StatFrameClose = StatFrame.Close;
		local MoveTo = StatFrame.MoveTo;
		local MoveToCancel = StatFrame.Cancel;
		MoveTo.Visible = true;
		MoveToCancel.Visible = false;
		local ExampleStatFrame = StatFrame.Contents.Stats.ExampleStat;

		-- Page 2 Variables; (Training)

		local Page2 = Pages.Page2;
		local StartTrainingBtn = Page2.StartTrain;
		local StopTrainingBtn = Page2.StopTrain;
		local CurrentTrainerLbl = Page2.Trainer_Out;
		local AutoTrainStatus = Page2.Status_Out;
		local TrainerProgressBar = Page2.TrainerProgressBar;
		local CompleteTrainerFrame = Page2.CTC_List;
		local CompleteTrainerCount = Page2.CT_LabelCount;
		local ExampleCTCLabel = CompleteTrainerFrame.CTC_Label:Clone();

		local UpdateCTFrame;
		local UpdateProgressBar;
		local SetTrainStatus;

		-- Page 3 Variables (Farming)
		local Page3 = Pages.Page3;
		local ReqFrame = Page3.Page3_ReqFrame;
		local P3Contents = Page3.Page3_WorkingContents;

		local ReqBypass = ReqFrame.Bypass_Btn;
		local ReqList = ReqFrame.ReqFrame_Disp;
		local AtkFrame = ReqList.Atk_Frame;
		local DefFrame = ReqList.Def_Frame;

		local AtkOut = AtkFrame.Atk_Out;
		local DefOut = DefFrame.Def_Out;

		local StartFarmingBtn = P3Contents.StartFarm;
		local StopFarmingBtn = P3Contents.StopFarm;
		local AutoFarmStatus = P3Contents.Status3_Out;

		local AccumResources = P3Contents.AccumulatedResources
		local YenFrame = AccumResources.Yen_Gain;
		local YenDisp = YenFrame.Yen_Out;
		local RCFrame = AccumResources.RC_Gain;
		local RCDisp = RCFrame.RC_Out;
		local RDFrame = AccumResources.Rep_Gain;
		local RDDisp = RDFrame.Rep_Out;
		local RunFrame = AccumResources.RunTime;
		local RunTimeDisp = RunFrame.Runtime_Out;
		local KillFrame = AccumResources.Kill_Gain;
		local KillDisp = KillFrame.Kill_Out;

		local TypeSelectFrame = P3Contents.TypeFrame.Type_Select;
		local ExampleTypeFrame = TypeSelectFrame.SelectFrame:Clone();
		local P3Toggles = P3Contents.Page3_Toggles;
		local RepFarmFrame = P3Toggles.Toggle_Select.RepFarm;
		local RepCheckBx = RepFarmFrame.Rep_CheckBox;
		local CorpseCollectFrame = P3Toggles.Toggle_Select.CorpseCollect;
		local CorpseCheckBx = CorpseCollectFrame.Corpse_CheckBox;
		local BreakFrame = P3Toggles.Toggle_Select.PreventKC;
		local BreakCheckBx = BreakFrame.KC_CheckBox;
		local EtoFarmFrame = P3Toggles.Toggle_Select.EtoFarm;
		local EtoCheckBox = EtoFarmFrame.Eto_CheckBox;

		if tonumber(farm.PlayerStats.Level.Value) >= 750 then
			EtoFarmFrame.Strike:Destroy();
		else
			config.ClientPersistentSettings["EtoFarm"] = false;
		end

		local FarmingDistanceFrame = P3Contents.FarmingDistance;
		local FarmingDistanceInput = FarmingDistanceFrame.FarmingDistance_Input;

		local FarmingStageFrame = P3Contents.FarmingStage;
		local FarmingStageInput = FarmingStageFrame.FarmingDistance_Input;

		local QuestFrame = P3Contents.QuestFrame;
		local QuestOutput = QuestFrame.NPC_Label;

		local SetFarmStatus;

		-- Page 4 Variables (Stat Focusing)

		local Page4 = Pages.Page4;
		local StatPanels = Page4.StatPanels;
		local StatObject = StatPanels.StatFocPanel;
		StatObject.Parent = nil;
		local CurrentFocusLabel = Page4.CurFoc_Out;
		local StartFocus = Page4.StartFocus;
		local StopFocus = Page4.StopFocus;

		-- Page 5 Variables; (Settings)

		local Page5 = Pages.Page5;
		local BuildingToggleFrame = Page5.Building_View.ToggleBuildings;
		local BuildingToggle = BuildingToggleFrame.Building_CheckBox;
		local AfkToggleFrame = Page5.Anti_Afk.AntiAfk;
		local AfkToggle = AfkToggleFrame.AA_CheckBox;
		local StreamerToggleFrame = Page5.Steamer_View.StreamerToggle;
		local StreamerToggle = StreamerToggleFrame.Streamer_CheckBox;

		self.MonsterTag = MonsterTag;
		self.PlayerTag = PlayerTag;
		self.StatFrame = StatFrame;
		self.MoveTo = MoveTo;	self.MoveToCancel = MoveToCancel;
		self.CompleteTrainerFrame = CompleteTrainerFrame;	self.ExampleCTCLabel = ExampleCTCLabel;
		self.CompleteTrainerCount = CompleteTrainerCount;	self.StartTrainingBtn = StartTrainingBtn;
		self.StopTrainingBtn = StopTrainingBtn;				self.TrainerProgressBar = TrainerProgressBar;
		self.AutoTrainStatus = AutoTrainStatus;				self.AutoFarmStatus = AutoFarmStatus;

		self.ReqFrame = ReqFrame;
		self.P3Contents = P3Contents;
		self.RunTimeDisp = RunTimeDisp;

		self.YenDisp = YenDisp;
		self.RCDisp = RCDisp;
		self.RDDisp = RDDisp;
		self.KillDisp = KillDisp;
		self.QuestOutput = QuestOutput;

		self.StopFarmingBtn = StopFarmingBtn;
		self.StartFarmingBtn = StartFarmingBtn;

		-- Page 6 Player Info

		local Page6 = Pages.Page6;
		local Page6Contents = Page6.Contents;
		local OnlinePlayers = Page6.OnlinePlayers;
		local MoveToButton = Page6.MoveTo;
		local CancelButton = Page6.Cancel;

		do -- GUI: Faded Lines
			local function CreateFadedLineH(holder)
				local globtrans = 0
				holder.BackgroundTransparency = 1;
				for i=holder.AbsoluteSize.X/2,holder.AbsoluteSize.X,1 do
					globtrans = globtrans + (1/(holder.AbsoluteSize.X/2))
					local p = Instance.new("Frame", holder)
					p.Size = UDim2.new(0,1,1,0)
					p.BorderSizePixel = 0;
					p.Parent = holder
					p.BackgroundColor3 = holder.BackgroundColor3
					p.Position = UDim2.new(0,i,0,0)
					p.BackgroundTransparency = globtrans
				end
				globtrans = 1
				for i=0,holder.AbsoluteSize.X/2,1 do
					globtrans = globtrans - (1/(holder.AbsoluteSize.X/2))
					local p = Instance.new("Frame", holder)
					p.Size = UDim2.new(0,1,1,0)
					p.BorderSizePixel = 0;
					p.Parent = holder
					p.BackgroundColor3 = holder.BackgroundColor3
					p.Position = UDim2.new(0,i,0,0)
					p.BackgroundTransparency = globtrans
				end
			end

			local function CreateFadedLineV(holder)
				local globtrans = 0
				holder.BackgroundTransparency = 1;
				for i=holder.AbsoluteSize.Y/2,holder.AbsoluteSize.Y,1 do
					globtrans = globtrans + (1/(holder.AbsoluteSize.Y/2))
					local p = Instance.new("Frame", holder)
					p.Size = UDim2.new(1,0,0,1)
					p.BorderSizePixel = 0;
					p.Parent = holder
					p.BackgroundColor3 = holder.BackgroundColor3
					p.Position = UDim2.new(0,0,0,i)
					p.BackgroundTransparency = globtrans
				end
				globtrans = 1
				for i=0,holder.AbsoluteSize.Y/2,1 do
					globtrans = globtrans - (1/(holder.AbsoluteSize.Y/2))
					local p = Instance.new("Frame", holder)
					p.Size = UDim2.new(1,0,0,1)
					p.BorderSizePixel = 0;
					p.Parent = holder
					p.BackgroundColor3 = holder.BackgroundColor3
					p.Position = UDim2.new(0,0,0,i)
					p.BackgroundTransparency = globtrans
				end
			end


			for _, obj in next, self.Screen:GetDescendants() do
				if obj.Name == "HorizontalLine" then
					CreateFadedLineH(obj);
				end
			end
			for _, obj in next, self.Screen:GetDescendants() do
				if obj.Name == "VerticalLine" then
					CreateFadedLineV(obj);
				end
			end
		end

		do	-- Image Button Colors

			for _,GuiObject in next, Window:GetDescendants() do

				if GuiObject:IsA("ImageButton") and GuiObject.Parent ~= PageTabs then
					local PrimaryColor = GuiObject.ImageColor3;
					local HoverColor = Color3.new(PrimaryColor.R-0.2,PrimaryColor.G-0.2,PrimaryColor.B-0.2)
					local ClickColor = Color3.new(PrimaryColor.R-0.3,PrimaryColor.G-0.3,PrimaryColor.B-0.3)

					table.insert(self.Connections,GuiObject.MouseEnter:connect(function()
						GuiObject.ImageColor3 = HoverColor;
					end))

					table.insert(self.Connections, GuiObject.MouseLeave:connect(function()
						GuiObject.ImageColor3 = PrimaryColor;
					end))

					table.insert(self.Connections, GuiObject.MouseButton1Down:connect(function()
						GuiObject.ImageColor3 = ClickColor;
					end))

					table.insert(self.Connections, GuiObject.MouseButton1Up:connect(function()
						GuiObject.ImageColor3 = HoverColor;
					end))
				elseif GuiObject:IsA("TextButton") and GuiObject.Parent ~= PageTabs then
					local PrimaryColor = GuiObject.TextColor3;
					local HoverColor = Color3.new(PrimaryColor.R-0.2,PrimaryColor.G-0.2,PrimaryColor.B-0.2)
					local ClickColor = Color3.new(PrimaryColor.R-0.3,PrimaryColor.G-0.3,PrimaryColor.B-0.3)
					table.insert(self.Connections, GuiObject.MouseEnter:connect(function()
						GuiObject.TextColor3 = HoverColor;
					end))

					table.insert(self.Connections, GuiObject.MouseLeave:connect(function()
						GuiObject.TextColor3 = PrimaryColor;
					end))

					table.insert(self.Connections, GuiObject.MouseButton1Down:connect(function()
						GuiObject.TextColor3 = ClickColor;
					end))

					table.insert(self.Connections, GuiObject.MouseButton1Up:connect(function()
						GuiObject.TextColor3 = HoverColor;
					end))
				end
			end
		end

		do -- Page Swapping
			local TweenService = game:GetService("TweenService");
			local SelectedPage = 1;
			local MinSize = 0.85;
			local TweenTime = 0.25
			local Tabs = {};
			local PageLayout = Pages.PageLayout;
			PageLayout.ScrollWheelInputEnabled = false;
			PageLayout.TouchInputEnabled = false;
			PageLayout.GamepadInputEnabled = false;
			local SelectPage;
			local TabTween = TweenInfo.new(TweenTime, Enum.EasingStyle.Linear, Enum.EasingDirection.Out);

			local SelectColor = Color3.new(85/255, 170/255, 255/255)
			local HoverColor = Color3.new(42/255, 84/255, 127/255)
			local DefaultColor = Color3.new(109/255, 109/255, 109/255)

			for _,Object in pairs(PageTabs:GetChildren()) do
				if Object:IsA("ImageButton") then
					table.insert(Tabs, Object);
				end
			end

			function SelectPage(Number)
				for _,Tab in pairs(Tabs) do
					if Tab.LayoutOrder ~= Number then
						local TabDefaultTween = TweenService:Create(Tab, TabTween, {BackgroundColor3 = DefaultColor})
						TabDefaultTween:Play();
						Tab:TweenSize(UDim2.new(MinSize,0,0,20), "Out", "Quad", TweenTime, true)
					end
				end
				PageLayout:JumpToIndex(SelectedPage-1);
			end

			for _,Tab in pairs(Tabs) do
				local TabPage = Tab.LayoutOrder;
				local HasMiniStatus,MiniStatus,StatusType = false,nil,nil,nil;
				local TabSelectTween = TweenService:Create(Tab, TabTween, {BackgroundColor3 = SelectColor})
				local TabHoverTween = TweenService:Create(Tab, TabTween, {BackgroundColor3 = HoverColor})
				local TabDefaultTween = TweenService:Create(Tab, TabTween, {BackgroundColor3 = DefaultColor})

				for _,Child in pairs(Tab:GetChildren()) do
					if Child.Name:find("MinStatus") then
						HasMiniStatus = true;
						MiniStatus = Child;
						MiniStatus.Visible = true;
						StatusType = Tab.Title.Text:gsub(" ", "");
					end
				end

				if HasMiniStatus then
					spawn(function()
						while MiniStatus do
							wait();
							local CheckVariable = nil;
							if StatusType == "Farming" then
								CheckVariable = AutoFarmToggle;
							elseif StatusType == "Trainer" then
								CheckVariable = AutoTraining;
							elseif StatusType == "Focusing" then
								CheckVariable = StatFocusToggle
							end

							if CheckVariable == true then
								MiniStatus.Image = "rbxassetid://"..config.StatusSymbols.Running;
								MiniStatus.Rotation = MiniStatus.Rotation - 5;
							else
								MiniStatus.Image = "rbxassetid://"..config.StatusSymbols.Stopped;
								MiniStatus.Rotation = 0;
							end
						end
					end)
				end

				table.insert(self.Connections, Tab.MouseEnter:connect(function()
					if SelectedPage == TabPage then
						return;
					end
					TabHoverTween:Play();
					Tab:TweenSize(UDim2.new(1,0,0,20), "Out", "Quad", TweenTime, true)
				end))
				table.insert(self.Connections, Tab.MouseLeave:connect(function()
					if SelectedPage ~= TabPage then
						TabDefaultTween:Play();
						Tab:TweenSize(UDim2.new(MinSize,0,0,20), "Out", "Quad", TweenTime, true)
					end
				end))
				table.insert(self.Connections, Tab.MouseButton1Down:connect(function()
					SelectedPage = TabPage;
					TabSelectTween:Play();
					Tab:TweenSize(UDim2.new(1,0,0,20), "Out", "Quad", TweenTime, true)
					SelectPage(TabPage);
				end))
			end

			SelectPage(SelectedPage);
		end

		do
			table.insert(self.Connections, Info.MouseButton1Down:connect(function()
				InterfacePages.InterfacePageLayout:JumpToIndex(2)
			end))

			table.insert(self.Connections, Dev.MouseButton1Down:connect(function()
				InterfacePages.InterfacePageLayout:JumpToIndex(1)
			end))

			for _,Return in pairs(ReturnMenuBtns) do
				table.insert(self.Connections, Return.MouseButton1Down:connect(function()
					InterfacePages.InterfacePageLayout:JumpToIndex(0)
				end))
			end

			InterfacePages.InterfacePageLayout:JumpToIndex(0)
		end

		for index,Statistic in pairs(self.StatsToView) do
			local ThisStat = ExampleStatFrame:Clone();
			ThisStat.LayoutOrder = index;
			ThisStat.Name = tostring(Statistic)
			ThisStat.Stat.Text = tostring(Statistic)
			ThisStat.Visible = true;
			ThisStat.Parent = StatFrame.Contents.Stats;
		end

		table.insert(self.Connections, StatFrameClose.MouseButton1Down:connect(function()
			StatFrame.Visible = false;
			for i,Connection in pairs(self.StatConnections) do
				Connection:Disconnect();
				table.remove(self.StatConnections, i);
			end
		end))

		table.insert(self.Connections, KillSwitch.MouseButton1Down:Connect(KillScript));

		-- Page 1 ( Training )
		do
			CurrentTrainerLbl.Text = tostring(trainer.NPC);
			table.insert(self.Connections, CurrentTrainerLbl.Changed:Connect(function()
				if trainer then
					CurrentTrainerLbl.Text = tostring(trainer.NPC);
				end
			end))

			table.insert(self.Connections, StartTrainingBtn.MouseButton1Down:Connect(function()
				StartTrainingBtn.Visible = false;
				StopTrainingBtn.Visible = true;
				AutoTraining = true;
			end))

			table.insert(self.Connections, StopTrainingBtn.MouseButton1Down:Connect(function()
				StartTrainingBtn.Visible = true;
				StopTrainingBtn.Visible = false;
				AutoTraining = false;
				self:SetTrainStatus("Off");
			end))

			self:SetTrainStatus("Off");
			self:UpdateProgressBar();
			self:UpdateCTFrame()

		end

		-- Page 2 ( General )
		do
			MaskInput.Text = "Input Name";
			table.insert(self.Connections, MaskInput.FocusLost:Connect(function()
				local toMaskServer = MaskFunction:InvokeServer("Check", MaskInput.Text);
				MaskInput.Text = toMaskServer;
				if MaskInput.Text:find("#") then
					MaskNameFiltered.Visible = true;
				end
			end))

			table.insert(self.Connections, MaskInput.Focused:Connect(function()
				MaskNameFiltered.Visible = false;
			end))

			table.insert(self.Connections, MaskApply.MouseButton1Down:Connect(function()
				local toMask = tostring(MaskInput.Text);
				MaskInput.Text = "Input Name";
				MaskFunction:InvokeServer("Confirm", toMask);
			end))

			table.insert(self.Connections, ShopOpen.MouseButton1Down:Connect(function()
				self:OpenShop("Weapon");
			end))

			table.insert(self.Connections, MaskShopOpen.MouseButton1Down:Connect(function()
				self:OpenShop("Mask");
			end))

			for _,Object in pairs(workspace:GetDescendants()) do
				for Team, ShopData in pairs(self.ShopIndicatorNames) do
					if Object.Name == ShopData[1] and Object:FindFirstChild("ClickDetector") ~= nil then
						local ParentHolder = tostring(Object.Parent.Name);
						if ParentHolder == ShopData[2] then
							self.ShopIndicatorNames[Team] = {
								ShopData[1],
								ShopData[2],
								Object:FindFirstChild("ClickDetector"),
							};
						end
					end
				end
			end

			local EspTypes = {"Players","Eto"}

			table.foreach(farm.NPCTypes, function(i,v)
				table.insert(EspTypes, v)
			end)

			local SendTypes = {}

			EspDistanceToggle.Visible = true;
			table.insert(self.Connections, EspDistanceToggle.ESPDistance_CheckBox.MouseButton1Down:connect(function()
				EspDistanceToggle.ESPDistance_CheckBox.ImageTransparency = EspDistanceToggle.ESPDistance_CheckBox.ImageTransparency==1 and 0 or 1;
				if EspDistanceToggle.ESPDistance_CheckBox.ImageTransparency == 1 then
					NPCEsp:UpdateIndicators(false)
				else
					NPCEsp:UpdateIndicators(true)
				end
			end))

			EspDistanceToggle.ESPDistance_CheckBox.ImageTransparency = config.ClientPersistentSettings.Esp.DistanceIndicator==true and 0 or 1;

			EspTierToggle.Visible = true;
			table.insert(self.Connections, EspTierToggle.NPCTier_CheckBox.MouseButton1Down:connect(function()
				EspTierToggle.NPCTier_CheckBox.ImageTransparency = EspTierToggle.NPCTier_CheckBox.ImageTransparency==1 and 0 or 1;
				if EspTierToggle.NPCTier_CheckBox.ImageTransparency == 1 then
					NPCEsp:UpdateTierLabels(false)
				else
					NPCEsp:UpdateTierLabels(true)
				end
			end))

			EspTierToggle.NPCTier_CheckBox.ImageTransparency = config.ClientPersistentSettings.Esp.NPCTierLabel==true and 0 or 1;

			NametagToggle.Visible = true;
			table.insert(self.Connections, NametagToggle.ESPNametag_CheckBox.MouseButton1Down:connect(function()
				NametagToggle.ESPNametag_CheckBox.ImageTransparency = NametagToggle.ESPNametag_CheckBox.ImageTransparency==1 and 0 or 1;
				if NametagToggle.ESPNametag_CheckBox.ImageTransparency == 1 then
					NPCEsp:UpdateNametags(false)
				else
					NPCEsp:UpdateNametags(true)
				end
			end))

			NametagToggle.ESPNametag_CheckBox.ImageTransparency = config.ClientPersistentSettings.Esp.Nametags==true and 0 or 1;
			
			for _,NPC in pairs(EspTypes) do
				local Toggle = ExampleToggle:Clone();
				Toggle.ExEsp_Label.Text = NPC;
				Toggle.Visible = true;
				Toggle.Parent = EspToggleFrame;

				local CheckBox = Toggle.ExEsp_CheckBox;
				table.insert(self.Connections, CheckBox.MouseButton1Down:connect(function()
					CheckBox.ImageTransparency = CheckBox.ImageTransparency==1 and 0 or 1;
					if CheckBox.ImageTransparency == 0 then
						table.insert(config.ClientPersistentSettings.Esp.ViewTypes, NPC)
					else
						for i,v in pairs(config.ClientPersistentSettings.Esp.ViewTypes) do
							if v == NPC then
								table.remove(config.ClientPersistentSettings.Esp.ViewTypes, i)
							end
						end
					end

					NPCEsp:ShowTagTypes(config.ClientPersistentSettings.Esp.ViewTypes)

					config:Save();
				end))

				CheckBox.ImageTransparency = 1;
				for i,Type in pairs(config.ClientPersistentSettings.Esp.ViewTypes) do
					if Type == NPC then
						CheckBox.ImageTransparency = 0;
					end
				end
			end

		end

		-- Page 3 ( Farming )
		do
			RepCheckBx.ImageTransparency = config.ClientPersistentSettings.ReputationFarm==true and 0 or 1;
			table.insert(self.Connections, RepCheckBx.MouseButton1Down:connect(function()
				config.ClientPersistentSettings.ReputationFarm = not config.ClientPersistentSettings.ReputationFarm;
				RepCheckBx.ImageTransparency = config.ClientPersistentSettings.ReputationFarm==true and 0 or 1;

				config:Save();
			end))

			CorpseCheckBx.ImageTransparency = config.ClientPersistentSettings.CollectCorpses==true and 0 or 1;
			table.insert(self.Connections, CorpseCheckBx.MouseButton1Down:connect(function()
				config.ClientPersistentSettings.CollectCorpses = not config.ClientPersistentSettings.CollectCorpses;
				CorpseCheckBx.ImageTransparency = config.ClientPersistentSettings.CollectCorpses==true and 0 or 1;

				config:Save();
			end))

			BreakCheckBx.ImageTransparency = config.ClientPersistentSettings.FarmingBreak==true and 0 or 1;
			table.insert(self.Connections, BreakCheckBx.MouseButton1Down:connect(function()
				config.ClientPersistentSettings.FarmingBreak = not config.ClientPersistentSettings.FarmingBreak;
				BreakCheckBx.ImageTransparency = config.ClientPersistentSettings.FarmingBreak==true and 0 or 1;

				config:Save();
			end))

			EtoCheckBox.ImageTransparency = config.ClientPersistentSettings.EtoFarm==true and 0 or 1;
			table.insert(self.Connections, EtoCheckBox.MouseButton1Down:connect(function()
				if tonumber(farm.PlayerStats.Level.Value) >= 600 then
					config.ClientPersistentSettings.EtoFarm = not config.ClientPersistentSettings.EtoFarm;
					EtoCheckBox.ImageTransparency = config.ClientPersistentSettings.EtoFarm==true and 0 or 1;

					config:Save();
				end
			end))

			table.insert(self.Connections, StartFarmingBtn.MouseButton1Down:connect(function()
				self:StartFarmButtonClick();
			end))

			table.insert(self.Connections, StopFarmingBtn.MouseButton1Down:connect(function()
				self:StopFarmButtonClick();
			end))

			table.insert(self.Connections, farm.PlayerStats.RC.Changed:connect(function(new)
				if farm and farm.RunTime then
					stats.RC.Gain = tonumber(new) - stats.RC.Start;
				end
			end))

			table.insert(self.Connections, farm.PlayerStats.Yen.Changed:connect(function(new)
				if farm and farm.RunTime then
					stats.Yen.Gain = tonumber(new) - stats.Yen.Start;
				end
			end))

			table.insert(self.Connections, farm.PlayerStats.Reputation.Changed:connect(function(new)
				if farm and farm.RunTime then
					stats.Rep.Gain = tonumber(new) - stats.Rep.Start;
				end
			end))

			table.insert(self.Connections, farm.PlayerFolder.KC.Changed:connect(function(new)
				if farm and farm.RunTime and tonumber(new) ~= 0 then
					stats.Kill.Gain = stats.Kill.Gain + 1;
				end
			end))

			table.insert(self.Connections, FarmingDistanceInput.FocusLost:Connect(function(enter)
				if enter then
					local maxFD = 13;
					local minFD = 3;
					local toSet = tonumber(FarmingDistanceInput.Text)
					if toSet then
						toSet = toSet >= maxFD and maxFD or toSet;
						toSet = toSet <= minFD and minFD or toSet;
						config.FarmingDist = toSet;
					end
					FarmingDistanceInput.Text = tostring(toSet);
				end
			end))

			FarmingDistanceInput.Text = tostring(config.FarmingDist);

			table.insert(self.Connections, FarmingStageInput.FocusLost:Connect(function(enter)
				if enter then
					local maxFS = 9;
					local minFS = 1;
					local toSet = tonumber(FarmingStageInput.Text)
					if toSet then
						toSet = toSet >= maxFS and maxFS or toSet;
						toSet = toSet <= minFS and minFS or toSet;
						config.FarmingStage = toSet;
					end
					FarmingStageInput.Text = tostring(toSet);
				end
			end))

			FarmingStageInput.Text = tostring(config.FarmingStage)

			for _, Object in pairs(TypeSelectFrame:GetChildren()) do
				if not Object:IsA("UIListLayout") and not Object:IsA("UIPadding") then
					Object:Destroy();
				end
			end

			for _, NPCType in pairs(farm.NPCTypes) do
				local Type = ExampleTypeFrame:Clone();
				Type.Parent = TypeSelectFrame;
				Type.Visible = true;
				Type.Select_Label.Text = tostring(NPCType)

				local ThisTypeToggle = false;

				for i,setType in pairs(config.ClientPersistentSettings.TargetTypes) do
					if tostring(setType) == tostring(NPCType) then
						Type.CheckBox.ImageTransparency = 0;
						ThisTypeToggle = true;
					end
				end

				TypeSelectFrame.CanvasSize = UDim2.new(1,0,0,(20*#farm.NPCTypes) + 10)

				table.insert(self.Connections, Type.CheckBox.MouseButton1Down:connect(function()
					ThisTypeToggle = not ThisTypeToggle;
					Type.CheckBox.ImageTransparency = (ThisTypeToggle==true and 0 or 1);
					if ThisTypeToggle then
						table.insert(config.ClientPersistentSettings.TargetTypes, tostring(NPCType))
					else
						for i,oldType in pairs(config.ClientPersistentSettings.TargetTypes) do
							if tostring(oldType) == tostring(NPCType) then
								table.remove(config.ClientPersistentSettings.TargetTypes, i)
							end
						end

						if farm.AddingTargets then
							repeat wait() until not farm.AddingTargets
						end

						for index, b in next, farm.Targets do
							if b.Name:lower():find(tostring(NPCType):lower()) then
								table.remove(farm.Targets, index);
							end
						end

					end

					config:Save();
				end))
			end

			for Stat, MinReq in pairs(config.StatRequirements) do
				local PStat = farm.PlayerStats:FindFirstChild(Stat)
				table.insert(self.Connections, PStat.Changed:connect(function()
					if config.ReqBypass then
						return;
					end

					if tonumber(PStat.Value) >= MinReq[1] then
						config.StatRequirements[Stat] = {MinReq[1], true};
					end

					if Stat == "Weapon" then
						AtkOut.Text = tostring(PStat.Value).."/"..MinReq[1]
					elseif Stat == "Durability" then
						DefOut.Text = tostring(PStat.Value).."/"..MinReq[1]
					end
				end))

				if Stat == "Weapon" then
					AtkOut.Text = tostring(PStat.Value).."/"..MinReq[1]
				elseif Stat == "Durability" then
					DefOut.Text = tostring(PStat.Value).."/"..MinReq[1]
				end

				if tonumber(PStat.Value) >= MinReq[1] then
					config.StatRequirements[Stat] = {MinReq[1], true};
				end
			end

			table.insert(self.Connections, ReqBypass.MouseButton1Down:connect(function()
				config.StatBypass = true;
			end))

			self:SetFarmStatus("Off")
		end

		-- Page 4 ( Stat Focusing )
		do
			local TotalFocusDistribution = 0;
			local PlayerFolder = LocalPlayer:WaitForChild("PlayerFolder");
			local StatsFunction = PlayerFolder.StatsFunction;
			local Distribution = {};

			for index,Stat in pairs(farm.PlayerStats:GetChildren()) do
				if Stat:FindFirstChild("Inc") then
					local newObject = StatObject:Clone()
					newObject.StatLabel.Text = tostring(Stat.Name)
					newObject.Name = tostring(Stat.Name)
					newObject.Parent = StatPanels
					newObject.LayoutOrder = index
					local ShiftLeft = newObject.ValueChange.ShiftLeft;
					local ShiftRight = newObject.ValueChange.ShiftRight;
					local currentDistribution = newObject.ValueChange.CurrentValue;
					Distribution[newObject] = tonumber(currentDistribution.Text);

					local function UpdateDistribution(val)
						local thisDist = tonumber(currentDistribution.Text);
						if thisDist + val < 0 or thisDist + val > 3 then
							return
						end
						thisDist = thisDist + val;
						if TotalFocusDistribution + val < 0 or TotalFocusDistribution + val > 3  then
							return
						end
						TotalFocusDistribution = TotalFocusDistribution + val;
						currentDistribution.Text = tostring(thisDist)
						Distribution[newObject] = thisDist;
					end

					table.insert(self.Connections, ShiftLeft.MouseButton1Down:connect(function()
						UpdateDistribution(-1)
					end))
					table.insert(self.Connections, ShiftRight.MouseButton1Down:connect(function()
						UpdateDistribution(1)
					end))
				end
			end

			local PlayerFocus = farm.PlayerStats:WaitForChild("Focus")
			CurrentFocusLabel.Text = tostring(PlayerFocus.Value)
			local Calculating = false;
			table.insert(self.Connections, PlayerFocus:GetPropertyChangedSignal("Value"):connect(function()
				CurrentFocusLabel.Text = tostring(PlayerFocus.Value)
				if StatFocusToggle and tonumber(PlayerFocus.Value) >= TotalFocusDistribution and not Calculating then
					Calculating = true;
					local Points = PlayerFocus.Value;
					local Multiplier = math.floor(Points/TotalFocusDistribution)

					if TotalFocusDistribution == 0 then
						return
					end

					local accumulatedValue = 0;
					for Object, PointValue in pairs(Distribution) do
						local TotalPointValue = PointValue*Multiplier;
						if TotalPointValue > 0 then
							StatsFunction:InvokeServer("Focus", tostring(Object).."AddButton", TotalPointValue)
						end
					end
					Calculating = false;
				end
			end))

			local function ToggleStartFocus()
				StatFocusToggle = not StatFocusToggle;
				if StatFocusToggle then
					StartFocus.Visible = false;
					StopFocus.Visible = true;

					local Points = PlayerFocus.Value;
					local Multiplier = math.floor(Points/TotalFocusDistribution)

					if TotalFocusDistribution == 0 then
						return
					end

					local accumulatedValue = 0;

					for Object, PointValue in pairs(Distribution) do
						local TotalPointValue = PointValue*Multiplier;
						if TotalPointValue > 0 then
							StatsFunction:InvokeServer("Focus", tostring(Object).."AddButton", TotalPointValue)
						end
					end
				else
					StartFocus.Visible = true
					StopFocus.Visible = false;
				end
			end

			table.insert(self.Connections, StartFocus.MouseButton1Down:connect(ToggleStartFocus));
			table.insert(self.Connections, StopFocus.MouseButton1Down:connect(ToggleStartFocus));
		end

		-- Page 5 ( Settings )
		do
			local function SetAfkButton(toggle)
				AfkToggle.ImageTransparency = (toggle==true) and 0 or 1;
			end

			local function SetStreamerMode(toggle)
				StreamerToggle.ImageTransparency = (toggle==true) and 0 or 1;
				local RbxCoreGui = CoreGui.RobloxGui;
				local PlayerListContainer = RbxCoreGui:FindFirstChild("PlayerListMaster")
				local TopBarContainer = RbxCoreGui:FindFirstChild("TopBarContainer")

				if PlayerListContainer and TopBarContainer then
					for _,Object in pairs(PlayerListContainer:GetDescendants()) do
						if Object.Name == LocalPlayer.Name and Object:FindFirstChild("ChildrenFrame") then
							local ChildFrameDesc = Object.ChildrenFrame:GetDescendants();
							for _,Desc in pairs(ChildFrameDesc) do
								if Desc.Name == "PlayerName" and Desc:FindFirstChild("PlayerName") then
									Desc.PlayerName.Text = (toggle==true) and "[CENSORED]" or LocalPlayer.Name;
									break;
								end
							end
							break;
						end
					end

					TopBarContainer.NameHealthContainer.Username.Text = (toggle==true) and "[CENSORED]" or LocalPlayer.Name;

				end
			end

			local function SetBuildingTrans(toggle)
				BuildingToggle.ImageTransparency = (toggle==true) and 0 or 1;
				for _,Object in pairs(config.CCParts) do
					Object[1].Transparency = (toggle==true) and Object[2] or 1;
					for _,Texture in pairs(Object[1]:GetChildren()) do
						if Texture:IsA("Texture") then
							Texture.Transparency = (Object[1].Transparency == 0) and 0.25 or 1;
						end
		 			end
				end

				local StreetLights = workspace:FindFirstChild("StreetLights")
				for _,Object in pairs(StreetLights:GetChildren()) do
					if Object:IsA("Model") and Object.Name == "StreetLight" then
						Object.StreetLight.Transparency = (toggle==true) and 0 or 1;
					end
				end
			end

			SetBuildingTrans(config.ClientPersistentSettings.BuildingTransparencyToggle)
			SetAfkButton(config.ClientPersistentSettings.AfkToggle)
			SetStreamerMode(config.ClientPersistentSettings.StreamerToggle)

			table.insert(self.Connections, BuildingToggle.MouseButton1Down:connect(function()
				config.ClientPersistentSettings.BuildingTransparencyToggle = not config.ClientPersistentSettings.BuildingTransparencyToggle;
				SetBuildingTrans(config.ClientPersistentSettings.BuildingTransparencyToggle)
				config:Save();
			end))

			table.insert(self.Connections, AfkToggle.MouseButton1Down:connect(function()
				config.ClientPersistentSettings.AfkToggle = not config.ClientPersistentSettings.AfkToggle
				SetAfkButton(config.ClientPersistentSettings.AfkToggle)
				config:Save();
			end))

			table.insert(self.Connections, StreamerToggle.MouseButton1Down:connect(function()
				config.ClientPersistentSettings.StreamerToggle = not config.ClientPersistentSettings.StreamerToggle
				SetStreamerMode(config.ClientPersistentSettings.StreamerToggle)
				config:Save();
			end))
		end

		return true;
	end
	setmetatable(interface, metadata);
end

-- INTERFACE MODULE END

-- PLAYER MOVER MODULE

PlayerMover = {
	TargetLocation = Instance.new("CFrameValue"),
	currentTween = nil,
	Character = nil,
	Dead = true,
	Enabled = false,
}

do -- Metatable for PlayerMover Object
	local metadata = {}
	local methods = {
		NextLocation = function(self, location)
			if not location then
				return
			end

			if self.currentTween then
				self.currentTween:Cancel();
			end

			local PP = self.Character.PrimaryPart;

			if PP then
				local DistanceToTravel = (PP.Position - location.p).magnitude
				local TimeToReach = DistanceToTravel/145
				--TimeToReach = TimeToReach < 0.05 and 0.05 or TimeToReach -- Lil bit of dampening

				self.currentTween = game:GetService("TweenService"):Create(self.TargetLocation, TweenInfo.new(TimeToReach,Enum.EasingStyle.Linear,Enum.EasingDirection.Out,0,false,0), {Value = location})
				self.currentTween:Play();
			end
		end,
		GetBehindTargetLocation = function(self, Target, distance)
			distance = (distance==nil) and 3 or distance
			if Target then
				local TPP = Target.PrimaryPart
				if TPP then
					local TargetCF = TPP.CFrame
					--return (TargetCF + Vector3.new(0,-(distance + 2), 0)) * CFrame.Angles(math.rad(90),0,0)
					return (TPP.CFrame + Vector3.new(0,1.25,0)) + TPP.CFrame.lookVector * -distance
				end
			end
			return nil
		end,
		GetForwardTargetLocation = function(self, Target, distance)
			distance = (distance==nil) and 3 or distance
			if Target then
				local TPP = Target.PrimaryPart
				if TPP then
					local TargetCF = TPP.CFrame
					--return (TargetCF + Vector3.new(0,-(distance + 2), 0)) * CFrame.Angles(math.rad(90),0,0)
					return ((TPP.CFrame + Vector3.new(0,1.25,0)) + TPP.CFrame.lookVector * distance) * CFrame.Angles(0,math.rad(180),0)
				end
			end
			return nil
		end,
		Enable = function(self)
			self();
			self.Enabled = true;

			game:GetService("RunService"):BindToRenderStep("player_mover", 102, function(delta)
				if self.Enabled and self.Character and not self.Dead then
					local PrimaryPart = self.Character.PrimaryPart;
					if PrimaryPart and self.Character then
						self.Character:SetPrimaryPartCFrame(self.TargetLocation.Value)
					end
				end
			end)
		end,
		Disable = function(self)
			self.Enabled = false;
			if self.currentTween then
				self.currentTween:Cancel();
				self.currentTween = nil;
			end
			game:GetService("RunService"):UnbindFromRenderStep("player_mover");
		end,
	}

	metadata.__index = function(self, i)
		if methods[i] then
			return function(s, ...)
				return methods[i](self, ...)
			end
		end
	end

	metadata.__call = function(self)

		self:Disable(); -- Make sure nothing is already running.
		self.Character = LocalPlayer.Character
		if self.Character and self.Character.PrimaryPart then
			self.TargetLocation.Value = self.Character.PrimaryPart.CFrame;
		else
			self.TargetLocation.Value = CFrame.new();
		end

		local Humanoid = self.Character:FindFirstChild("Humanoid")
		if Humanoid then
			if Humanoid.Health > 0 then
				self.Dead = false;
				Humanoid.Died:connect(function()
					self.Dead = true;
				end)
			end
		end
	end

	setmetatable(PlayerMover, metadata);
end

-- PLAYER MOVER MODULE END

-- TRAINER MODULE

trainer = {
	Trainers = {
		All = {
			["CCG"] = {
				"(S1) Kureo Mado",
				"(S1) Koutarou Amon",
				"(Suit) Yukinori Shinohara",
				"(Battle) Yukinori Shinohara",
				"(S1) Hideyoshi Nagachika",
				"(S1) Juuzou Suzuya",
				"(Arata) Yukinori Shinohara",
			},
			["Ghoul"] = {
				"(S1) Ken Kaneki",
				"(S1) Renji Yomo",
				"(S1) Touka Kirishima",
				"(S1) Nishiki Nishio",
				"(S1) Shuu Tsukiyama",
				"(S1) Kuzen Yoshimura"
			}
		},
		Completed = {},
		Available = nil
	},
	NPC = nil;
	Position = 0;
	Progress = nil;
	AutoTrainingCheckFrequency = 1;
	LastTrainingCheck = tick();
	CheckTrainer = Remotes.Trainers.RequestTraining,
	ChangeTrainer = Remotes.Trainers.ChangeTrainer,
	TeamString = ""
}

do
	local metadata = {}
	local methods = {
		Destroy = function(self)
			if self.CheckTrainer then
				self.CheckTrainer = nil;
			end
			if self.ChangeTrainer then
				self.ChangeTrainer = nil;
			end
			AutoTraining = false;
			setmetatable(self, {})
			trainer = nil;
		end,
		Task = function(self, Character, NPC)
			appendLog("-- Task function invoked. --")
			local TaskRemote;
			local RepCashRemote = Remotes.ReputationCashOut;

			if self.TeamString == "CCG" then
				TaskRemote = Remotes:FindFirstChild("Yoshitoki")
			else
				TaskRemote = Remotes:FindFirstChild("Yoshimura")
			end

			appendLog("Chose "..self.TeamString.." remote"..TaskRemote.Name)

			if TaskRemote and RepCashRemote and (Character:GetModelCFrame().p-NPC:GetModelCFrame().p).magnitude <= 10 then
				appendLog("Invoking TaskRemote to server...")
				-- Freezing here...
				TaskRemote.Task:InvokeServer();
				RepCashRemote:InvokeServer();
				return true;
			else
				appendLog("Failed to meet requirements for TaskRemote: ", tostring(TaskRemote==nil), tostring((Character:GetModelCFrame().p-NPC:GetModelCFrame().p).magnitude))
			end
			return false;
		end,
		Train = function(self, Request)
			local PlayerFolder = LocalPlayer:WaitForChild("PlayerFolder");
			if not Request and interface then
				local thisTrainer = self:Train("FindTrainer");
				interface:UpdateCTFrame();
				interface:UpdateProgressBar();
				if thisTrainer then
					local CurrentSession;
					LocalPlayer.Backpack.DescendantAdded:connect(function(Child)
						if Child.Name == "TSCodeVal" then
							Keys.train_event = Child.Value;
							Child.Parent.Disabled = true
						elseif Child.Name == "TrainingSession" then
							CurrentSession = Child.Value;
						end
					end)

					repeat wait() until not farm.Collecting;

					local ReturnCFrame;
					local TrainStatus = self.CheckTrainer:InvokeServer(thisTrainer);

					if LocalPlayer.Character.Parent then
						ReturnCFrame = LocalPlayer.Character:GetModelCFrame();
					end

					if TrainStatus == "TRAINING" then
						if Keys.train_event == "" then
							repeat wait() until Keys.train_event ~= "";
						end
						if CurrentSession then
							wait();
							local HasCharacter = LocalPlayer.Character.Parent;
							repeat
								HasCharacter = LocalPlayer.Character.Parent;
								wait()
							until HasCharacter == nil
							farm.DeathLocation = ReturnCFrame;
							CurrentSession.Comm:FireServer("Finished", Keys.train_event, false);
							self.Progess = PlayerFolder.Trainers[thisTrainer].Progress.Value;
							interface:SetTrainStatus("Completed Training.")
							wait(0.25);
						end
					elseif TrainStatus == "TRAINING COMPLETE" then
						interface:SetTrainStatus(tostring(thisTrainer).." completed.")
						wait(1)
					else
						interface:SetTrainStatus(TrainStatus);
					end
				else
					interface:SetTrainStatus("All Trainers Completed.");
					return
				end
			elseif Request == "FindTrainer" then
				local CompleteAll = true;
				for index, _ in next, self.Trainers.Available do
					if not self.Trainers.Completed[self.Trainers.Available[index]] then
						self.ChangeTrainer:InvokeServer(self.Trainers.Available[index]);

						self.NPC = PlayerFolder.Trainers:FindFirstChild(self.TeamString .. "Trainer").Value;
						self.Progress = PlayerFolder.Trainers[self.NPC].Progress.Value;
						if tonumber(self.Progress) ~= 100 then
							return self.NPC
						else
							self.Trainers.Completed[self.Trainers.Available[index]] = true;
						end
					end
				end

				return
			end
		end
	}

	metadata.__index = function(self, i)
		if methods[i] then
			return function(s, ...)
				return methods[i](self, ...)
			end
		end
	end

	metadata.__call = function(self)
		-- initialization of trainer object.
		if self.Trainers.Available ~= nil or self.NPC ~= nil then
			print("Trainer object already initialized.");
			return false;
		end
		local PlayerFolder = LocalPlayer:WaitForChild("PlayerFolder");
		self.TeamString = PlayerFolder.Customization.Team.Value;
		self.Trainers.Available = self.Trainers.All[self.TeamString];
		self.NPC = PlayerFolder.Trainers:FindFirstChild(self.TeamString .. "Trainer").Value;
		self.Progress = PlayerFolder.Trainers[self.NPC].Progress.Value;
		-- CalculateTrainerPosition
		for index, trainer in pairs(self.Trainers.Available) do
			if tostring(trainer) == tostring(self.NPC) then
				self.Position = index;
			end
		end

	end

	setmetatable(trainer, metadata);
end

-- TRAINER MODULE END

-- FARM MODULE

farm = {
	PlayerStats = nil,
	PlayerFolder = nil,
	NPCDirectory = workspace.NPCSpawns,
	QuestDirectory = nil,
	Dead = false,
	DeathLocation = nil,
	LastTouchedCFrame = nil,
	TimeFromLastTouch = tick(),
	Collecting = false,
	FightingBoss = false,
	ClosestTarget = nil,
	RunTime = nil,
	AddingTargets = false,
	TargetDistance = math.huge;
	NPCTypes = {
		"Investigator",
		"Aogiri",
		"Athlete",
		"Human"
	},
	NPCTable = {},
	Targets = {},
	Connections = {}
}

do -- metadata for farm object.

	local metadata = {}
	local methods = {
		Destroy = function(self)
			for a, b in next, self.Connections do
				b:Disconnect();
			end
			if interface then
				interface:StopFarmButtonClick();
			end
			setmetatable(self, {})
			farm = nil;
		end,
		WeaponEquipped = function(self, character)
			if character then
				local ServerControl = character:WaitForChild("ServerControl")
				local CurrentWep = ServerControl:WaitForChild("CurrentWep")
				if CurrentWep.Value ~= "" then
					return true;
				end
			end
			return false;
		end,
		GetCurrentQuest = function(self)
			for _,Quest in pairs(self.QuestDirectory:GetChildren()) do
				if Quest.Name ~= "Reward" then
					return Quest
				end
			end

			return nil
		end,
		Start = function(self)
			local Character = LocalPlayer.Character;
			local CurrentCamera = game:GetService("Workspace").CurrentCamera;
			if Character then
				local Humanoid = Character:FindFirstChildWhichIsA("Humanoid")
				local HumanoidRootPart = Character:FindFirstChild("HumanoidRootPart")
				if Humanoid and HumanoidRootPart then
					if AutoFarmToggle then
						pcall(function() --  Remove BodyPosition for RoGhoul
							HumanoidRootPart.bp:Destroy();
						end)

						PlayerMover:Enable();
						spawn(function()
							local PreviousNPC;

							while farm and AutoFarmToggle and self.Dead == false do
								local SkipToBeginning = false;
								if self:WeaponEquipped(Character) == false then
									appendLog("Weapon equipped");
									KeyEvent:FireServer(Keys.key_event, tostring(NumberTextValues[config.FarmingStage]), "Down", nil, false, CFrame.new()) -- Make this dynamic later
								end

								if config.ClientPersistentSettings.FarmingBreak and self.Dead == false and AutoFarmToggle == true then
									appendLog("Taking a break")
									local PlayerFolder = LocalPlayer.PlayerFolder;
									if tonumber(PlayerFolder.KC.Value) >= (config.KCMax-1) then
										repeat
											interface:SetFarmStatus("Taking a break...")
											wait()
										until tonumber(PlayerFolder.KC.Value) <= 2 or self.Dead or AutoFarmToggle == false
									end
								elseif self.Dead == true or AutoFarmToggle == false then
									appendLog("SkipToBeginning = true")
									SkipToBeginning = true;
								end

								if config.ClientPersistentSettings.EtoFarm and SkipToBeginning == false and AutoFarmToggle == true and self.Dead == false then
									appendLog("> EtoFarm")
									local Eto;
									for _, Spawn in next, self.NPCDirectory:GetChildren() do
										if Spawn.Name == "BossSpawns" and #Spawn:GetChildren() > 0 then
											Eto = Spawn:GetChildren()[1];
										end
									end

									if Eto and Eto:FindFirstChildWhichIsA("Humanoid", true) and Eto:FindFirstChild("HitboxGroup") then
										appendLog("> Found Eto")
										local HitBoxes = {
											Main = Eto.HitboxGroup:FindFirstChild("HB2"),
											Other = Eto.HitboxGroup:FindFirstChild("HB1")
										}

										for _,OldData in pairs(Eto:GetChildren()) do
											if OldData:IsA("SelectionBox") then
												OldData:Destroy();
											elseif OldData:IsA("BasePart") then
												local ClientSelectionBox = Instance.new("SelectionBox", Eto)
												ClientSelectionBox.Name = "";
												ClientSelectionBox.SurfaceTransparency = 0.7;
												ClientSelectionBox.SurfaceColor3 = Color3.new(0.3,0.3,1)
												ClientSelectionBox.Transparency = 1;
												ClientSelectionBox.Adornee = OldData;
											end
										end

										local EtoHumanoid = Eto:FindFirstChildWhichIsA("Humanoid", true)

										if EtoHumanoid.Health >= 0 and HitBoxes.Main and HitBoxes.Other then
											interface:SetFarmStatus("Moving to boss.");
										end

										local EtoDead = false;
										local NPCFolder = Eto.NPCFolder;
										local BossMovePosition;
										local ClickLag,SpikeLag,EvadeLag = tick(),tick(),nil;
										local Jumping,SideSwipes,SpikeThrow,ClickAttack,CanAct = false,false,false,false,false;
										local JumpDodge = false;
										local LastAttack = tick();

										local EtoDiedConnection = EtoHumanoid.Died:connect(function()
											EtoDead = true;
										end)

										local jumpSignal = NPCFolder.Special3CD.Changed:Connect(function()
											Jumping = (NPCFolder.Special3CD.Value == "DownTime") and true or false;
										end)

										local sideSwipSignal = NPCFolder.Special2CD.Changed:Connect(function()
											SideSwipes = (NPCFolder.Special2CD.Value == "DownTime") and true or false;
										end)

										local SpikeThrowSignal = NPCFolder.Special1CD.Changed:Connect(function()
											SpikeThrow = (NPCFolder.Special1CD.Value == "DownTime") and true or false;
											if not Jumping then
												SpikeLag = tick()
											end
										end)

										local CanActSignal = NPCFolder.CanAct.Changed:Connect(function()
											CanAct = (NPCFolder.CanAct.Value == "DownTime") and true or false;
										end)

										local ClickSignal = NPCFolder.ClickCD.Changed:connect(function()
											ClickAttack = (NPCFolder.ClickCD.Value == "DownTime") and true or false;
											if not ClickAttack then
												ClickLag = tick();
											end
										end)

										appendLog("> Closing Gap")
										local DistanceFromEto = math.huge;
										while DistanceFromEto >= 10 and self.Dead == false and AutoFarmToggle == true and EtoHumanoid.Health >= 0 do
											PlayerMover:NextLocation(HitBoxes.Main.CFrame);
											DistanceFromEto = (Character:GetModelCFrame().p-HitBoxes.Main.CFrame.p).magnitude;
											wait();
										end

										if EtoHumanoid.Health >= 0 then
											appendLog("> Latching to Eto")
											interface:SetFarmStatus("Latching to Eto");
											PlayerMover:Disable();

											CurrentCamera.CameraType = 4;
											CurrentCamera.CameraSubject = EtoHumanoid;

											self.FightingBoss = true;

											local Initial_Interaction = true;

											game:GetService("RunService"):BindToRenderStep("boss_fight", 101, function(delta)
												if not self.Dead and EtoHumanoid then
													local MoveAway,Mount = false,false;

													if Jumping and not Initial_Interaction then
														Initial_Interaction = false;
														MoveAway = true;
													end

													if SpikeThrow or tick()-SpikeLag < 0.15 then
														Mount = true;
													end

													if not MoveAway or Mount then
														if EtoHumanoid.WalkSpeed ~= 0.1 then
															KeyEvent:FireServer(Keys.key_event, "Mouse1", "Down", nil, false, CFrame.new());
														end
													end

													if MoveAway then
														BossMovePosition = CFrame.new(HitBoxes.Main.Position + (HitBoxes.Main.CFrame.upVector * -(HitBoxes.Main.Size.X*10)), HitBoxes.Main.Position)
													elseif Mount then
														BossMovePosition = CFrame.new(HitBoxes.Main.Position + (HitBoxes.Main.CFrame.upVector * (HitBoxes.Main.Size.X/2) + (HitBoxes.Main.CFrame.lookVector * -(HitBoxes.Main.Size.X/2))), HitBoxes.Main.Position)
													else
														BossMovePosition = CFrame.new(HitBoxes.Main.Position + (HitBoxes.Main.CFrame.upVector * -(HitBoxes.Main.Size.X + config.FarmingDist) + (HitBoxes.Main.CFrame.lookVector * -(HitBoxes.Main.Size.X/2))), HitBoxes.Main.Position)
													end

													HumanoidRootPart.CFrame = BossMovePosition;
													interface:SetFarmStatus("Eto ("..toSuffixString(math.floor(EtoHumanoid.Health+0.5)).."/"..toSuffixString(EtoHumanoid.MaxHealth)..") HP");
												end
											end)

											appendLog("> Killing Eto")

											while farm and Eto and EtoHumanoid and self.Dead == false and AutoFarmToggle == true do
												wait();
												if EtoHumanoid.Health <= 0 or EtoDead then
													interface:SetFarmStatus("Killed Eto")
													break
												end
											end

											if self.Dead and Eto and EtoHumanoid and EtoHumanoid.Health > 0 then
												if Eto.PrimaryPart then
													self.DeathLocation = Eto.PrimaryPart.CFrame;
												end
											end

											self.FightingBoss = false;

											if Eto and EtoHumanoid then
												if EtoHumanoid.Health <= 0 or EtoDead then
													--Eto:Destroy(); -- Clean it up on the client, don't need this anymore.
												end
											end

											CurrentCamera.CameraType = Enum.CameraType.Custom;
											CurrentCamera.CameraSubject = LocalPlayer.Character.Humanoid;

											pcall(function()
												game:GetService("RunService"):UnbindFromRenderStep("boss_fight");
												EtoDiedConnection:Disconnect();
												sideSwipSignal:Disconnect();
												jumpSignal:Disconnect();
												CanActSignal:Disconnect();
												SpikeThrowSignal:Disconnect();
												ClickSignal:Disconnect();
											end)

											PlayerMover:Enable();
											appendLog("> Exit EtoFarm")
										end

									end
								elseif self.Dead or AutoFarmToggle == false then
									appendLog("SkipToBeginning = true")
									SkipToBeginning = true;
								end

								if config.ClientPersistentSettings.ReputationFarm and SkipToBeginning == false and self.Dead == false and AutoFarmToggle == true then
									appendLog("> ReputationFarm")
									local CurrentQuest = self:GetCurrentQuest();
									local Team = trainer.TeamString;
									local TeamNPC = config.ReputationLocations[Team];

									local TravelToNpc = false

									if CurrentQuest then
										if CurrentQuest.Value == CurrentQuest.Max.Value then -- If Quest is Complete, Turn in
											TravelToNpc = true;
										end
									else -- No Current Quest, Let's pick one up.
										TravelToNpc = true;
									end

									if TravelToNpc then
										appendLog("> Traveling to Trainer")
										interface:SetFarmStatus("Moving to Quest NPC")
										PlayerMover:NextLocation(TeamNPC:GetModelCFrame())

										self.Collecting = true; -- Stop the Farm from grabbing new closest targets for now

										local DistanceFromNPC = math.huge;
										while DistanceFromNPC >= 10 and not self.Dead and AutoFarmToggle == true do
											DistanceFromNPC = (Character:GetModelCFrame().p - TeamNPC:GetModelCFrame().p).magnitude
											wait();
										end

										appendLog("> Arrived at Trainer")

										if not self.Dead and AutoFarmToggle then
											if self:GetCurrentQuest() == nil then
												appendLog("> Grabbing Quest")
												while self:GetCurrentQuest() == nil and self.Dead == false and AutoFarmToggle == true do
													interface:SetFarmStatus("Grabbing Quest")
													trainer:Task(Character, TeamNPC);
													wait();
												end
											elseif self:GetCurrentQuest() ~= nil then
												local AcceptedAlready = false;
												appendLog("> Turning in Quest")
												while self:GetCurrentQuest() == CurrentQuest and self.Dead == false and AutoFarmToggle == true do
													interface:SetFarmStatus("Turning in Quest")

													local potentialQuest = self:GetCurrentQuest()
													if potentialQuest then
														appendLog("> Potential Quest")
														if potentialQuest.Value ~= potentialQuest.Max.Value then
															print("> Accepted already.. breaking")
															AcceptedAlready = true;
															break;
														end
													end
													trainer:Task(Character, TeamNPC);
													wait(0.5);
												end

												if not AcceptedAlready then
													appendLog("> Now grabbing Quest")
													while self:GetCurrentQuest() == nil and self.Dead == false and AutoFarmToggle == true do
														interface:SetFarmStatus("Grabbing Quest")
														trainer:Task(Character, TeamNPC);
														wait();
													end
												end
											end
										else
											break;
										end

										self.Collecting = false;
									end
								elseif self.Dead or AutoFarmToggle == false then
									appendLog("SkipToBeginning = true")
									SkipToBeginning = true;
								end

								if self and self.Dead == false and SkipToBeginning == false and AutoFarmToggle == true and self.ClosestTarget and not self.FightingBoss then
									if PreviousNPC then
										if PreviousNPC == self.ClosestTarget then
											return
										end
									end

									local TargetCompile = {
										Model = nil,
										Dead = true,
										Connections = {},
									};
									do
										local metadata = {}
										local methods = {
											GetDistance = function(_self)
												if _self.Model then
													if Character then
														return (Character:GetModelCFrame().p - _self.Model:GetModelCFrame().p).magnitude
													end
												else
													_self.Dead = true;
												end
												return 0
											end,
											GetHealth = function(_self)
												if _self.Model then
													local selfHumanoid = _self.Model:FindFirstChildWhichIsA("Humanoid")
													if selfHumanoid then
														return selfHumanoid.Health
													end
												end
												return 0
											end,
											GetCFrame = function(_self)
												if _self.Model then
													local selfHumanoidRootPart = _self.Model:FindFirstChild("HumanoidRootPart")
													if selfHumanoidRootPart then
														return selfHumanoidRootPart.CFrame;
													end
												end
												return nil
											end,
											GetMaxHealth = function(_self)
												if _self.Model then
													local selfHumanoid = _self.Model:FindFirstChildWhichIsA("Humanoid")
													if selfHumanoid then
														return selfHumanoid.MaxHealth
													end
												end
												return 0
											end,
											Destroy = function(_self)
												if _self.Connections then
													for _,Connection in pairs(_self.Connections) do
														Connection:Disconnect();
													end
													_self.Connections = nil;
												end
												_self.Dead = true;
												setmetatable(TargetCompile, {})
											end,
										}

										metadata.__index = function(_self, i)
											if methods[i] then
												return function(s, ...)
													return methods[i](_self, ...)
												end
											end
										end

										metadata.__call = function(_self)
											_self.Model = self.ClosestTarget
											if _self.Model then
												local selfHumanoid = _self.Model:FindFirstChildWhichIsA("Humanoid")
												if selfHumanoid then
													table.insert(_self.Connections, selfHumanoid.Died:connect(function()
														_self.Dead = true;
													end))
													table.insert(_self.Connections, selfHumanoid.AncestryChanged:connect(function()
														_self.Dead = true;
													end))
													_self.Dead = false;
												end

												spawn(function()
													while _self.Model == self.ClosestTarget do
														wait()
													end
													if _self.Destroy then
													 	_self:Destroy();
													end
												end)

												return TargetCompile;
											end
											return nil
										end

										setmetatable(TargetCompile, metadata)
									end

									--GetHealth, GetMaxHealth, GetCFrame, Destroy, GetDistance
									-- Model index to get the Target Model;
									appendLog("> Compiling Target")
									local MyTarget = TargetCompile();
									local LastAttack = tick();

									if MyTarget.Model and not MyTarget.Dead then
										appendLog("> Moving to next Target")
										while MyTarget.Dead == false and self.Dead == false and AutoFarmToggle == true and self.FightingBoss == false and MyTarget:GetDistance() >= 12 do
											interface:SetFarmStatus("Moving to next target");
											PlayerMover:NextLocation(PlayerMover:GetBehindTargetLocation(MyTarget.Model, config.FarmingDist))
											wait();
										end
										appendLog("> Killing Target")
										while MyTarget.Dead == false and MyTarget.Model and self.Dead == false and AutoFarmToggle == true do
											interface:SetFarmStatus("Killing target");
											if tick()-LastAttack >= 0.15 then
												LastAttack = tick();
												KeyEvent:FireServer(Keys.key_event, "Mouse1", "Down", MyTarget.Model, false, CFrame.new());
											end

											if (MyTarget:GetHealth()/MyTarget:GetHealth()) <= 0.2 then
												PlayerMover:NextLocation(PlayerMover:GetForwardTargetLocation(MyTarget.Model, config.FarmingDist))
											else
												PlayerMover:NextLocation(PlayerMover:GetBehindTargetLocation(MyTarget.Model, config.FarmingDist))
											end
											wait();
										end

										if config.ClientPersistentSettings.CollectCorpses and MyTarget.Model and Character and self.Dead == false and AutoFarmToggle == true then
											appendLog("> Collecting Target Corpse")
											if MyTarget.Model and (MyTarget.Model:GetModelCFrame().p - Character:GetModelCFrame().p).magnitude <= 15 then
												self.Collecting = true;

												for _,child in next, MyTarget.Model:GetDescendants() do
													if child.Name == "HumanoidRootPart" then
														PlayerMover:NextLocation(child.CFrame)
													end
												end
											end
										end

										local CollectDelay = tick();

										while self.Collecting and tick()-CollectDelay <= 1.25 and self.Dead == false and AutoFarmToggle == true do
											interface:SetFarmStatus("Collecting Corpse");
											if MyTarget.Model then
												for _, child in next, MyTarget.Model:GetDescendants() do
													if child:IsA("ClickDetector") and config.ClientPersistentSettings.CollectCorpses then
														ClickDetectorFire(child);
													end
												end
												wait();
											else
												break;
											end
										end

										if MyTarget.Destroy then
											MyTarget:Destroy();
										end
										interface:SetFarmStatus("");
										self.TargetDistance = math.huge;
										self.Collecting = false;
										appendLog("> Target died, cleaning up.")
									else
										if TargetCompile.Destroy then
											TargetCompile:Destroy();
										end
										appendLog("> Destroying Target")
									end

									-- Target Loop Inside
								end

								wait();
								-- END INSIDE WHILE
							end
							appendLog("Set status to Off, we dead jim.")
							-- END OUTSIDE WHILE
							interface:SetFarmStatus("Off");
						end)
					else
						appendLog("Turn off farm partition.")
						interface:SetFarmStatus("Off");
						CurrentCamera.CameraType = Enum.CameraType.Custom;
						CurrentCamera.CameraSubject = LocalPlayer.Character.Humanoid;
						game:GetService("RunService"):UnbindFromRenderStep("boss_fight");
						PlayerMover:Disable();
					end
				end
			end
			-- END
		end
	}

	metadata.__index = function(self, i)
		if methods[i] then
			return function(s, ...)
				return methods[i](self, ...)
			end
		end
	end

	metadata.__call = function(self)

		if self.PlayerStats ~= nil or self.QuestDirectory ~= nil or #self.Connections > 0 then
			print("farm object already initialized.");
			return;
		end

		local PlayerFolder = LocalPlayer:WaitForChild("PlayerFolder");
		self.PlayerFolder = PlayerFolder;
		self.PlayerStats = PlayerFolder:WaitForChild("Stats");
		self.QuestDirectory = PlayerFolder.CurrentQuest.Complete;

		local idleConnection = LocalPlayer.Idled:Connect(function()
			if config.ClientPersistentSettings.AfkToggle then
				local VirtualUser = game:GetService("VirtualUser");
				VirtualUser:CaptureController()
				VirtualUser:ClickButton2(Vector2.new())
			end
		end)

		local function RecursePatchCC(Upper)
			for _,Child in pairs(Upper:GetChildren()) do
				if Child:IsA("BasePart") then
					--local PrevCC = Child.CanCollide;
					--local Hook = Child:AddGetHook("CanCollide", PrevCC)
					table.insert(config.CCParts, {Child, Child.Transparency})
				end
				if #Child:GetChildren() > 0 then
					RecursePatchCC(Child)
				end
			end
		end

		for _, Building in pairs(config.CanCollideBuildings) do
			local CBuilding = workspace:FindFirstChild(Building)
			if CBuilding then
				RecursePatchCC(CBuilding)
			else
				warn("[N]: Missed Building Asset ("..Building..")")
			end
		end

		table.insert(self.Connections, idleConnection);
	end

	setmetatable(farm, metadata);
end

-- FARM MODULE END

-- ESP MODULE

NPCEsp = {
	Tags = {},
	TagHolder = nil,
	TierList = {
		["First Class"] = "H",
		["Rank 1"] = "M",
		["Rank 2"] = "L",
		["High"] = "H",
		["Mid"] = "M",
		["Low"] = "L",
	},
	NPCTypes = {
		"Investigator",
		"Aogiri",
		"Athlete",
		"Human",
		"Eto"
	},
	Connections = {},
}

do
	local metadata = {}
	local methods = {
		UpdateIndicators = function(self, bool)
			config.ClientPersistentSettings.Esp.DistanceIndicator = bool;
			config:Save();
			for index,Tag in pairs(self.Tags) do
				if Tag then
					if Tag.Adornee and Tag.Name == "MONSTER" then
						local MonsterMain = Tag:FindFirstChild("MonsterMain")
						if MonsterMain then
							MonsterMain.M_Distance.Visible = bool;
							self:ResizeTag(Tag);
						else
							table.remove(self.Tags, index)
							Tag:Destroy();
						end
					end
				else
					table.remove(self.Tags, index)
				end
			end
		end,
		UpdateTierLabels = function(self, bool)
			config.ClientPersistentSettings.Esp.NPCTierLabel = bool;
			config:Save();
			for index,Tag in pairs(self.Tags) do
				if Tag then
					if Tag.Adornee and Tag.Name == "MONSTER" then
						local MonsterMain = Tag:FindFirstChild("MonsterMain")
						local GUID = Tag:FindFirstChild("GUID")
						if MonsterMain and GUID then
							if GUID.Value:IsA("Player") == false then
								MonsterMain.M_Tier.Visible = bool;
								self:ResizeTag(Tag);
							end
						else
							table.remove(self.Tags, index)
							Tag:Destroy();
						end
					else
						table.remove(self.Tags, index)
						Tag:Destroy();
					end
				else
					table.remove(self.Tags, index)
				end
			end
		end,
		UpdateNametags = function(self, bool)
			config.ClientPersistentSettings.Esp.Nametags = bool;
			config:Save();
			for index,Tag in pairs(self.Tags) do
				if Tag then
					if Tag.Adornee and Tag.Name == "MONSTER" then
						local MonsterMain = Tag:FindFirstChild("MonsterMain")
						if MonsterMain then
							MonsterMain.MonsterName.Visible = bool;
							self:ResizeTag(Tag);
						else
							table.remove(self.Tags, index)
							Tag:Destroy();
						end
					end
				else
					table.remove(self.Tags, index)
				end
			end
		end,
		UpdatePlayerHealth = function(self, bool)
			
		end,
		UpdatePlayerAbilities = function(self, bool)
			
		end,
		GetTier = function(self, name)
			local TierMatch;
			for Query,Result in pairs(self.TierList) do
				if name:lower():find(Query:lower()) then
					return Result;
				end
			end
			return "?";
		end,
		ResizeTag = function(self, Tag)
			if Tag then
				Tag.Size = UDim2.new(0,Tag.MonsterMain.UIListLayout.AbsoluteContentSize.X,0,25)
			else
				table.remove(self.Tags, index)
				Tag:Destroy();
			end
		end,
		ShowTagTypes = function(self, Types)
			for _,Tag in pairs(self.Tags) do
				local GUID = Tag:FindFirstChild("GUID")
				if GUID then
					local Target = GUID.Value
					if Target then
						local Head = Target:IsA("Player") and Target.Character:FindFirstChild("Head") or Target:FindFirstChild("Head")
						local Humanoid = Target:IsA("Player") and Target.Character:FindFirstChildWhichIsA("Humanoid") or Target:FindFirstChildWhichIsA("Humanoid")

						if Humanoid and Head then
							Humanoid.DisplayDistanceType = "Subject"
							Tag.Enabled = false;
							for _,Viewing in pairs(Types) do
								if not Target:IsA("Player") then
									if tostring(Target):lower():find(Viewing:lower()) then
										Humanoid.DisplayDistanceType = "None"
										Tag.Enabled = true;
									end
								else
									if Viewing == "Players" then
										Humanoid.DisplayDistanceType = "None"
										Tag.Enabled = true;
									end
								end
							end
						end
					end
				end
			end
		end,
		GetTag = function(self, target)
			for _,Tag in pairs(self.TagHolder:GetChildren()) do
				local GUID = Tag:FindFirstChild("GUID")
				if GUID then
					if GUID.Value == target then
						return Tag;
					end
				end
			end
			return nil
		end,
		CreatePlayerTag = function(self, Player, Adornee)
			if Player and Adornee then
				local Tag = interface.PlayerTag:Clone();
				Tag.Name = "PLAYER"
				local GUID = Instance.new("ObjectValue", Tag)
				GUID.Name = "GUID";
				GUID.Value = Player;
				Tag.Adornee = Adornee;

				local Humanoid = Player.Character:WaitForChild("Humanoid", 2)
				local PlayerFolder = Player:WaitForChild("PlayerFolder", 2);
				local Head = Player.Character:WaitForChild("Head", 2)

				local PlayerStatus = Head:FindFirstChild("PlayerStatus");

				if Humanoid and PlayerFolder then
					local HealthFrame = Tag:FindFirstChild("HealthFrame")
					local AbilityFrame = Tag:FindFirstChild("AbilityFrame")

					local HealthCon = Humanoid:GetPropertyChangedSignal("Health"):connect(function()
						if HealthFrame and Humanoid then
							local Fill = HealthFrame:FindFirstChild("Fill")
							if Fill then
								Fill.Size = UDim2.new(Humanoid.Health/Humanoid.MaxHealth,0,1,0)
							end
						else
							HealthCon:Disconnect();
						end
					end)

					if HealthFrame then
						HealthFrame.Fill.Size = UDim2.new(Humanoid.Health/Humanoid.MaxHealth,0,1,0)
						HealthFrame.Visible = true;
					end

					if PlayerStatus then
						PlayerStatus.Enabled = false;
					end

					local ModelExtents = Player.Character:GetExtentsSize()
					Tag.StudsOffsetWorldSpace = Vector3.new(0, ModelExtents.Y - 5, 0) -- take off 5 for the normal height of a Character

					--Tag.AbilityFrame.Visible = false;
					Tag.MaxDistance = 250;

					Tag.Parent = self.TagHolder
					table.insert(self.Tags, Tag)
					return Tag;
				end

				Tag:Destroy();
				return nil;
			end
		end,
		CreateTag = function(self, Target, adornee)
			if Target and adornee then
				local Tag = interface.MonsterTag:Clone()
				Tag.Name = "MONSTER"
				local GUID = Instance.new("ObjectValue", Tag)
				GUID.Name = "GUID";
				GUID.Value = Target;
				Tag.Adornee = adornee;

				local Humanoid = Target:IsA("Player") and Target.Character:WaitForChild("Humanoid", 2) or Target:WaitForChild("Humanoid", 2)

				if Humanoid then
					if not Target:IsA("Player") then
						local NPC_Tier = NPCEsp:GetTier(tostring(Target.Name))
						if NPC_Tier ~= "?" then
							Tag.MonsterMain.M_Tier.Text = NPC_Tier;
							Tag.MonsterMain.M_Tier.Visible = true;
						else
							Tag.MonsterMain.M_Tier.Visible = false;
						end
					else
						Tag.MonsterMain.M_Tier.Visible = false;
					end

					local DisplayName,DisplayColor = "null",Color3.new(1,0,0);

					Tag.MonsterMain.M_Distance.Visible = config.ClientPersistentSettings.Esp.DistanceIndicator;
					Tag.MonsterMain.MonsterName.Visible = config.ClientPersistentSettings.Esp.Nametags;
					if not Target:IsA("Player") then
						Tag.MonsterMain.M_Tier.Visible = config.ClientPersistentSettings.Esp.NPCTierLabel;
					end

					for _,TargetName in pairs(self.NPCTypes) do
						if not Target:IsA("Player") then
							if tostring(Target):lower():find(TargetName:lower()) then
								DisplayName = tostring(TargetName)
								DisplayColor = TargetName == "Eto" and Color3.new(1,0.3,0.3) or Color3.new(1,1,1)
								Tag.MonsterMain.MonsterName.Active = false;
								break;
							end
						else
							DisplayName = tostring(Target.Name)
							DisplayColor = Color3.new(1, 170/255, 127/255)
							break;
						end
					end

					Tag.Enabled = false;

					for _,Viewing in pairs(config.ClientPersistentSettings.Esp.ViewTypes) do
						if not Target:IsA("Player") then
							if tostring(Target):lower():find(Viewing:lower()) then
								Tag.Enabled = true;
							end
						else
							if Viewing == "Players" then
								Tag.Enabled = true;
							end
						end
					end

					if Tag.Enabled then
						Humanoid.DisplayDistanceType = "None"
					end

					Tag.MonsterMain.MonsterName.Text = tostring(DisplayName);
					Tag.MonsterMain.MonsterName.TextColor3 = DisplayColor;
					Tag.MonsterMain.MonsterName.LayoutOrder = 1;
					Tag.MonsterMain.MonsterName.Size = UDim2.new(0,game:GetService("TextService"):GetTextSize(DisplayName, Tag.MonsterMain.MonsterName.TextSize, Tag.MonsterMain.MonsterName.Font, Vector2.new(0,0)).X,1,0)

					local ModelExtents = Target:IsA("Player") and Target.Character:GetExtentsSize() or Target:GetExtentsSize();
					Tag.StudsOffsetWorldSpace = Vector3.new(0, ModelExtents.Y - 5, 0) -- take off 5 for the normal height of a Character

					self:ResizeTag(Tag);

					Tag.Parent = self.TagHolder
					table.insert(self.Tags, Tag)
					return Tag
				end
				Tag:Destroy();
			end
			return nil
		end,
		Destroy = function(self)
			self.TagHolder:Destroy();
			setmetatable(NPCEsp, {})
			NPCEsp = nil
		end,
	}

	metadata.__index = function(self, i)
		if methods[i] then
			return function(s, ...)
				return methods[i](self, ...)
			end
		end
	end

	metadata.__call = function(self)
		self.TagHolder = Instance.new("ScreenGui")
		self.TagHolder.Name = "[FH] Tags"
		self.TagHolder.Parent = game:GetService("CoreGui")

		self.TagHolder.ChildAdded:connect(function(Tag)
			local Target = Tag:FindFirstChild("GUID").Value;

			local function DeleteTag(child, parent)
				if parent == nil then
					Tag:Destroy();
				end
			end

			if Target:IsA("Model") then
				Target.AncestryChanged:connect(DeleteTag)
			elseif Target:IsA("Player") then
				Target.Character.AncestryChanged:connect(DeleteTag)
			end

			if Tag.Name == "MONSTER" then
				Tag.MonsterMain.MonsterName.MouseButton1Down:connect(function()
					if Target:IsA("Player") then
						interface:ShowStats(Target);
					end
				end)
			end
		end)
	end

	setmetatable(NPCEsp, metadata)
end

-- ESP MODULE END

-- CONFIG MODULE

-- Configuration settings object.
config = {
	FilePath = "FH_RoGhoul.cfg",
	FarmingDist = 3,
	FarmingStage = 1,
	KCMax = 8,
	MaxLevel = 2000,
	StatusSymbols = {["Running"] = 1510759484, ["Stopped"] = 54479706},
	AutoTraining = false,
	QuestOverride = false,
	StatRequirements = {
		["Weapon"] = {350,false},
		["Durability"] = {50,false},
	},
	StatBypass = false,
	-- Afk Prevention Variables
	ClientPersistentSettings = {
		["Version"] = VERSION;
		["TargetTypes"] = {"Investigator"},
		["CollectCorpses"] = true,
		["FarmingBreak"] = true,
		["ReputationFarm"] = false,
		["BuildingTransparencyToggle"] = true,
		["AfkToggle"] = true,
		["StreamerToggle"] = false,
		["EtoFarm"] = false,
		["Esp"] = {
			["DistanceIndicator"] = true,
			["NPCTierLabel"] = true,
			["Nametags"] = true,
			["PlayerAbilities"] = false,
			["PlayerHealth"] = true,
			["ViewTypes"] = {},
		},
	},
	CanCollideBuildings = {
		"CCGBuilding",
		"Rural",
		"Storage",
		"Parking Garage",
		"Kamii",
		"YamoriTowerOutside",
		"CCGBuilding",
		"CCGLab",
		"UtaMaskShop",
		"Anteiku",
		"KakuhouSurgeonWarehouse",
		"Track And Field",
		"ClosedApartment",
		"Ruins",
		"Buildings",
		"Railroad",
		"Park",
		"Blocks",
		"11thWardBase",
		"GasStation",
		"Rural", -- Patch for floor?
		"Asphalt" -- Patch for floor?
	},
	ReputationLocations = {
		["CCG"] = workspace.CCGBuilding.Yoshitoki,
		["Ghoul"] = workspace.Anteiku.Yoshimura,
	},
	CCParts = {}
}

do
	local metadata = {};
	local methods = {
		Save = function(self)
			local CurrentSettings = self.ClientPersistentSettings;
			local EncodedData = game:GetService("HttpService"):JSONEncode(CurrentSettings)
			if type(EncodedData) == "string" then
				writefile(self.FilePath, EncodedData);
			end
		end,
		Load = function(self)
			local ConfigFile
			pcall(function()
				ConfigFile = readfile(self.FilePath)
			end)
			local NewSettings;
			if ConfigFile then
				local DecodedData = game:GetService("HttpService"):JSONDecode(ConfigFile)
				NewSettings = DecodedData

				if NewSettings.Version == nil then
					self:Save();
					warn("[N] No Version found, which indicates old trainer. Using defaults.")
					return
				elseif NewSettings.Version and NewSettings.Version ~= VERSION then
					self:Save();
					warn("Update identified, using defaults.")
					return
				end
				self.ClientPersistentSettings = NewSettings;
			else
				warn("[N]: No Config file found. Using defaults.")
				self:Save();
			end
		end
	};

	metadata.__index = function(self, i)
		if methods[i] then
			return function(s, ...)
				return methods[i](self, ...)
			end
		end
	end
	setmetatable(config, metadata);
end

-- CONFIG MODULE END

-- MODULES END

function ClickDetectorFire(detector)
	if fireclickdetector then -- Synapse
		fireclickdetector(detector, 1);
	elseif click_detector then -- ProtoSmasher
		click_detector(detector, 1);
	else
		config.ClientPersistentSettings.CollectCorpses = false;
	end
end

KillScript = function()
	pcall(function() KillKey() end);
	pcall(function() trainer:Destroy() end);
	pcall(function() farm:Destroy() end);
	pcall(function() interface:Destroy() end);
	pcall(function() NPCEsp:Destroy() end)
	error("Killed Ro-Ghoul trainer.", 0);
end

local function luau()
	if is_luau then
		return is_luau();
	else
		return isluau();
	end

	warn("No function was found to confirm luau.")
	return nil;
end

local function readonly(table, boolean)
	if make_writeable and not boolean then
		make_writeable(table);
	elseif make_readonly and not boolean then
		make_readonly(table);
	elseif setreadonly then
		setreadonly(table, boolean);
	else
		print("Could not find a valid table to set read only... (Meta)")
	end
end

mouse1click = mouse1click or Input.LeftClick;
newcclosure = newcclosure or protect_function
getnamecallmethod = getnamecallmethod or get_namecall_method

if luau() then
	local KeyEventMeta = getrawmetatable(KeyEvent)
	readonly(KeyEventMeta, false)

	local oldNC = KeyEventMeta.__namecall
	if PROTOSMASHER_LOADED then -- Proto doesn't support newcclosure, oh well.
		KeyEventMeta.__namecall = function(self, ...)
			local Method = getnamecallmethod();
			local Arguments = {...}

			if self == KeyEvent and Method == "FireServer" then
				Keys.key_event = Arguments[1];
			end

			return oldNC(self, unpack(Arguments))
		end
	else
		KeyEventMeta.__namecall = newcclosure(function(self, ...)
			local Method = getnamecallmethod();
			local Arguments = {...}

			if self == KeyEvent and Method == "FireServer" then
				Keys.key_event = Arguments[1];
			end

			return oldNC(self, unpack(Arguments))
		end)
	end

	repeat
		mouse1click();
		wait();
	until Keys.key_event ~= ""
	KeyEventMeta.__namecall = oldNC;
else
	local KeyEventMeta = getrawmetatable(KeyEvent)
	readonly(KeyEventMeta, false)
	local oldNC = KeyEventMeta.__namecall
	if PROTOSMASHER_LOADED then
		KeyEventMeta.__namecall = function(self, ...)
			local Arguments = {...}
			if self == KeyEvent then
				Keys.key_event = Arguments[1];
			end
			return oldNC(self, ...)
		end
	else
		KeyEventMeta.__namecall = newcclosure(function(self, ...)
			local Arguments = {...}
			if self == KeyEvent then
				Keys.key_event = Arguments[1];
			end
			return oldNC(self, ...)
		end)
	end

	repeat
		mouse1click();
		wait();
	until Keys.key_event ~= ""
	KeyEventMeta.__namecall = oldNC;
end


-- RUN MODULES

NPCEsp();
config:Load();
farm();
trainer();
interface();

-- OTHER

do
	local suffixes = {"K", "M", "B", "T", "Q", "Qu", "S", "Se", "O", "N", "D"}
	SecondsToHMS = function(Seconds)
		local Minutes = (Seconds - Seconds%60)/60
		Seconds = Seconds - Minutes*60
		local Hours = (Minutes - Minutes%60)/60
		Minutes = Minutes - Hours*60
		return string.format("%02i", Hours)..":"..string.format("%02i", Minutes)..":"..string.format("%02i", Seconds)
	end

	toSuffixString = function(n)
		for i = #suffixes, 1, -1 do
			local v = math.pow(10, i * 3)
			if n >= v then
				return ("%.2f"):format(n / v) .. suffixes[i]
			end
		end
		return tostring(n)
	end
end

local function recurse(parent)
    for index, obj in pairs(parent:GetChildren()) do
        if obj:IsA("BasePart") and #obj:GetChildren() == 0 then
            obj.CanCollide = false
        elseif #obj:GetChildren() >= 1 then
            if obj:IsA("BasePart") then
                obj.CanCollide = false
            end
            recurse(obj)
        end
    end
end

if LocalPlayer.Character and LocalPlayer.Character:FindFirstChild("Humanoid") then
	local deathConnection = LocalPlayer.Character.Humanoid.Died:Connect(function()
		farm.Dead = true;
		if farm.LastTouchedCFrame then
			farm.DeathLocation = farm.LastTouchedCFrame + Vector3.new(0, 3, 0);
		end
	end)
	table.insert(farm.Connections, deathConnection);
end

local onCharAdded;
onCharAdded = LocalPlayer.CharacterAdded:Connect(function(newCharacter)
	local Humanoid = newCharacter:WaitForChild("Humanoid");
	local deathConnection = Humanoid.Died:Connect(function()
		farm.Dead = true;
		if farm.DeathLocation and farm.LastTouchedCFrame then
			farm.DeathLocation = farm.LastTouchedCFrame + Vector3.new(0, 3, 0);
		end
	end)
	table.insert(farm.Connections, deathConnection);

	farm.Dead = false

	if farm.DeathLocation and AutoFarmToggle then
		repeat
			if newCharacter.PrimaryPart then
				newCharacter:SetPrimaryPartCFrame(farm.DeathLocation);
			end
			wait();
		until newCharacter.PrimaryPart and math.abs((farm.DeathLocation.p-newCharacter.PrimaryPart.Position).magnitude) <= 10;
		farm.DeathLocation = nil;
	end

	CharacterRemotes = LocalPlayer.Character:WaitForChild("Remotes", 2);
	KeyEvent = CharacterRemotes:WaitForChild("KeyEvent", 2);
	if AutoFarmToggle then
		farm:Start();
	end
end)

do
	for _, Spawn in pairs(farm.NPCDirectory:GetChildren()) do
		table.insert(farm.Connections, Spawn.ChildAdded:connect(function(Target)
			local Target_Head = Target:WaitForChild("Head", 7)
			if Target_Head and NPCEsp then
				local Tag = NPCEsp:CreateTag(Target, Target_Head)
				table.insert(farm.NPCTable, Target);
			end
		end))

		table.insert(farm.Connections, Spawn.ChildRemoved:connect(function(Target)
			for index,Userdata in pairs(farm.NPCTable) do
				if Userdata == Target and NPCEsp then
					local OldTag = NPCEsp:GetTag(Target)
					if OldTag then
						OldTag:Destroy();
					end

					table.remove(farm.NPCTable, index);
				end
			end
		end))

		-- Get Current;
		for _,Target in pairs(Spawn:GetChildren()) do
			local Target_Head = Target:FindFirstChild("Head")
			if Target_Head and NPCEsp then
				local Tag = NPCEsp:CreateTag(Target, Target_Head)
				table.insert(farm.NPCTable, Target);
			end
		end
	end

	for _,RoPlayer in pairs(game:GetService("Players"):GetChildren()) do
		if RoPlayer ~= LocalPlayer then
			RoPlayer.CharacterAdded:connect(function(TargetCharacter)
				local Target_Head = TargetCharacter:WaitForChild("Head", 7)
				if Target_Head and NPCEsp then
					if NPCEsp.CreateTag and NPCEsp.CreatePlayerTag then
						local Tag = NPCEsp:CreateTag(RoPlayer, Target_Head)
						local PTag = NPCEsp:CreatePlayerTag(RoPlayer, Target_Head)
						if Tag and PTag then
							TargetCharacter.AncestryChanged:connect(function()
								Tag:Destroy();
								PTag:Destroy();
							end)
						end
					end
				end
			end)

			if RoPlayer.Character and RoPlayer.Character:FindFirstChildWhichIsA("Humanoid") then
				local Target_Head = RoPlayer.Character:FindFirstChild("Head")
				if Target_Head and NPCEsp then
					local Tag = NPCEsp:CreateTag(RoPlayer, Target_Head)
					local PTag = NPCEsp:CreatePlayerTag(RoPlayer, Target_Head)
					if Tag and PTag then
						RoPlayer.Character.AncestryChanged:connect(function()
							Tag:Destroy();
							PTag:Destroy();
						end)
					end
				end
			end
		end
	end

	table.insert(farm.Connections, game:GetService("Players").PlayerAdded:connect(function(NewPlayer)
		NewPlayer.CharacterAdded:connect(function(TargetCharacter)
			local Target_Head = TargetCharacter:WaitForChild("Head", 7)
			if Target_Head and NPCEsp then
				if NPCEsp.CreateTag and NPCEsp.CreatePlayerTag then
					local Tag = NPCEsp:CreateTag(NewPlayer, Target_Head)
					local PTag = NPCEsp:CreatePlayerTag(RoPlayer, Target_Head)
					if Tag and PTag then
						TargetCharacter.AncestryChanged:connect(function()
							Tag:Destroy();
							PTag:Destroy();
						end)
					end
				end
			end
		end)
	end))
end

local mainLoop;
local labelUpdateStamp = tick();

mainLoop = game:GetService('RunService').RenderStepped:Connect(function()

	interface.VPRotationConstant = (interface.VPRotationConstant >= 360) and 0 or interface.VPRotationConstant + 1;
	if interface.ViewingModel then
		local ExtentSize = interface.ViewingModel:GetExtentsSize()
		interface.VPCamera.CFrame = CFrame.new() * CFrame.Angles(0,math.rad(interface.VPRotationConstant),0) * CFrame.new(Vector3.new(0, 0, ExtentSize.Y), interface.ViewingModel:GetModelCFrame().p)
	end

	if (tick()-trainer.LastTrainingCheck >= trainer.AutoTrainingCheckFrequency) then
		trainer.LastTrainingCheck = tick();
		if AutoTraining then
			trainer:Train();
		end
	end


	if farm.RunTime ~= nil then
		interface.RunTimeDisp.Text = SecondsToHMS(math.floor(tick()-farm.RunTime)+0.5)
	else
		interface.RunTimeDisp.Text = SecondsToHMS(0);
	end

	interface.YenDisp.Text = toSuffixString(stats.Yen.Gain);
	interface.RCDisp.Text = toSuffixString(stats.RC.Gain);
	interface.RDDisp.Text = toSuffixString(stats.Rep.Gain);
	interface.KillDisp.Text = toSuffixString(stats.Kill.Gain);

	local CurrentQuest = farm:GetCurrentQuest();

	if CurrentQuest then
		interface.QuestOutput.Text = tostring(CurrentQuest.Value).."/"..tostring(CurrentQuest.Max.Value)
	else
		interface.QuestOutput.Text = "None"
	end

	-- Stat Requirements.
	do
		if config.StatBypass then
			interface.ReqFrame.Visible = false;
			interface.P3Contents.Visible = true;
		end

		local StatCount = 0;
		local PassedStats = {};

		for Stat, MinReq in pairs(config.StatRequirements) do
			StatCount = StatCount + 1;
			if MinReq[2] == true then
				table.insert(PassedStats, Stat)
			end
		end

		if #PassedStats == StatCount then
			config.StatBypass = true;
		end
	end

	-- NameTags
	do
		local SelfChar = LocalPlayer.Character;
		if SelfChar then
			local SelfHead = SelfChar:FindFirstChild("Head")

			if SelfHead then
				for index,Tag in pairs(NPCEsp.Tags) do
					if Tag then
						local GUID = Tag:FindFirstChild("GUID")
						if Tag.Name == "MONSTER" and GUID then
							local MonsterMain = Tag:FindFirstChild("MonsterMain")
							local Target = GUID.Value;
							if Target and MonsterMain then
								local ModelExtents;

								if Target:IsA("Player") and Target.Character then
									ModelExtents = Target.Character:GetExtentsSize()
								elseif Target then
									ModelExtents = Target:GetExtentsSize();
								end

								if ModelExtents then
									if Tag.StudsOffsetWorldSpace.Y ~= (ModelExtents.Y - 5) then
										Tag.StudsOffsetWorldSpace = Vector3.new(0,(ModelExtents.Y - 5), 0)
									end

									if MonsterMain.M_Distance.Visible and Tag then
										if Tag.Adornee then
											local TargetSelfMagnitude = math.floor((Tag.Adornee.CFrame.p - SelfHead.CFrame.p).magnitude);
											MonsterMain.M_Distance.Text = tostring(TargetSelfMagnitude).."m"
											MonsterMain.M_Distance.Size = UDim2.new(0,MonsterMain.M_Distance.TextBounds.X + 12,1,0)
											NPCEsp:ResizeTag(Tag);
										else
											table.remove(NPCEsp.Tags, index);
										end
									end
								else
									table.remove(NPCEsp.Tags, index);
								end
							else
								table.remove(NPCEsp.Tags, index);
							end
						elseif Tag.Name == "PLAYER" and GUID then
							local Target = GUID.Value;
							if Target.Character then
								local ModelExtents = Target.Character:GetExtentsSize()

								if ModelExtents then
									if Tag.StudsOffsetWorldSpace.Y ~= (ModelExtents.Y - 5) then
										Tag.StudsOffsetWorldSpace = Vector3.new(0,(ModelExtents.Y - 5), 0)
									end
								end
							end
						else
							table.remove(NPCEsp.Tags, index);
						end
					else
						table.remove(NPCEsp.Tags, index);
						Tag:Destroy();
					end
				end
			end
		end
	end


	-- AutoFarming

	do
		if LocalPlayer.Character == nil or farm.Collecting then
			return;
		end

		farm.Targets = {};

		farm.AddingTargets = true;
		for _,Target in pairs(farm.NPCTable) do
			for _,Type in pairs(config.ClientPersistentSettings.TargetTypes) do
				if tostring(Target):lower():find(Type:lower()) then
					table.insert(farm.Targets, Target)
				end
			end
		end
		farm.AddingTargets = false;

		--if all the targets are unchecked.
		if #farm.Targets == 0 and AutoFarmToggle and #config.ClientPersistentSettings.TargetTypes == 0 then
			interface:StopFarmButtonClick();
		end

		-- no targets so lets no let the character fall to death.
		if #farm.Targets == 0 and AutoFarmToggle then
			local Character = LocalPlayer.Character;
			if Character then
				Character.PrimaryPart.Anchored = true;
				interface:SetFarmStatus("There are no NPCS available to kill.. Waiting..");
			end
			return
		end

		local Character = LocalPlayer.Character;
		if Character then
			pcall(function()
				Character.PrimaryPart.Anchored = false;
			end)
		end

		local ClosestTarget = nil;
		local Distance = math.huge;

		for _, Target in pairs(farm.Targets) do
			local tHumanoid = Target:FindFirstChild("Humanoid")
			if tHumanoid and tHumanoid.Health > 0 then
				local tDistance = (LocalPlayer.Character:GetModelCFrame().p-Target:GetModelCFrame().p).magnitude;
				if tDistance <= Distance then
					Distance = tDistance;
					ClosestTarget = Target;
				end
			end
		end

		if ClosestTarget and Distance <= farm.TargetDistance then
			if farm.ClosestTarget ~= nil then
				if farm.ClosestTarget == ClosestTarget then
					return
				end
			end

			farm.ClosestTarget = ClosestTarget;
			farm.TargetDistance = Distance;
		end
	end
end);

table.insert(farm.Connections, mainLoop);
table.insert(farm.Connections, onCharAdded);

do -- Stops character from getting stuck undermap. Also saves the last cframe of character.
	spawn(function()
		local belowZeroTimeStamp = tick();
		while farm do
			-- Persistence
			if AutoFarmToggle then
				local Character = LocalPlayer.Character
				if Character then
					local IgnoreList = {Character}
					local PP = Character.PrimaryPart;
					if PP then
						local DownCast = Ray.new(PP.Position, PP.CFrame.upVector * -25)
						local Object,Position = workspace:FindPartOnRayWithIgnoreList(DownCast, IgnoreList)

						if (Object or farm.FightingBoss) and (tick()-farm.TimeFromLastTouch) >= 0.5 and PP then
							farm.TimeFromLastTouch = tick();
							farm.LastTouchedCFrame = PP.CFrame;
						end
					end
				end
			end
			wait()
		end
	end)
end
