--TheUnknownUser12344 HITBOX MODULE--

local hitbox = {HitboxPart = Instance.new("Part",workspace), Damage = 10, ApplyKB = false, KB_VEL = 3}
local RunService = game:GetService("RunService")

local function has_Value(tab, value)
	for i, v in tab do
		if v == value then
			return true
		end
	end
	
	return false
end

function hitbox:new(t)
	t = t or {}
	setmetatable(t,self)
	self.__index = self
	return t
end

local function ApplyKnockback(self, Obj, Character)
	local Connection
	local Time = 0
	local RaycastFilter = RaycastParams.new()
	RaycastFilter.FilterDescendantsInstances = {Obj}
	RaycastFilter.FilterType = Enum.RaycastFilterType.Blacklist
	task.spawn(function()
		Connection = RunService.Heartbeat:Connect(function(dt)
			if Obj then
				Time += dt
				local Position = Obj:GetPivot().Position
				local REAL_VELOCITY = Character.LookVector * self.KB_VEL
				local Gravity = Vector3.new(0,-self.KB_VEL,0) 
				local Raycast = workspace:Raycast(Position, Position + REAL_VELOCITY * dt, RaycastFilter)	
				if not Raycast then
					Obj:PivotTo(CFrame.new(Position + REAL_VELOCITY * dt))
					REAL_VELOCITY = REAL_VELOCITY + Gravity * dt
				else
					Connection:Disconnect()
					return
				end

				if Time >= self.KB_VEL/3 then
					Connection:Disconnect()
					return
				end
			end
		end)
	end)
end

function hitbox:ApplyDamage(FilterParams:{}, missSound:Sound, hitSound, lifeTime, Character, KnockBack, otherFunc)
	local foundHumanoid = false
	local Time = 0
	local Count = 0
	local isHumanoidEqual = false
	local HumanoidsTable = {}
	FilterParams = FilterParams or {}
	local OverlapParam = OverlapParams.new()
	OverlapParam.FilterDescendantsInstances = FilterParams
	OverlapParam.FilterType = Enum.RaycastFilterType.Blacklist

	task.spawn(function()
		repeat
			local dt = task.wait()
			local Box = workspace:GetPartBoundsInBox(self.HitboxPart.CFrame,self.HitboxPart.Size, OverlapParam)
			Time+=dt
			for index, Parts in Box do
				local Humanoid = Parts.Parent:FindFirstChild("Humanoid")
				if Humanoid then
					if not has_Value(HumanoidsTable, Humanoid.Parent) then
						Humanoid:TakeDamage(self.Damage)
						task.spawn(function()
							if otherFunc then
								otherFunc(Humanoid.Parent)
							end
						end)
						table.insert(HumanoidsTable, Humanoid.Parent)
						if self.ApplyKB and Character then
							if KnockBack then
								KnockBack(Humanoid.Parent)
							else
								ApplyKnockback(self, Humanoid.Parent, Character)
							end
						end
						if missSound then
							if missSound.IsPlaying then 
								missSound:Stop()
							end
						end

						if hitSound then
							hitSound:Play()
						end
					end
				end				
			end
		until Time >= lifeTime

		if missSound and Time >= lifeTime and #HumanoidsTable == 0 then
			if hitSound then
				if hitSound.IsPlaying then
					hitSound:Stop()
				end
			end
			missSound:Play()
		end
	end)
end

return hitbox
