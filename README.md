# roblox-gun-script-mapping
scale = 2 / 20 -----What you want to reduce it to. Most guns, or maps, are around 20-30

local representation = Instance.new("Model")
representation.Parent = script.Parent
representation.archivable = false
representation.Name = "Model"

function makeRepresentation(obj, cf, name)
	if not obj.archivable then
		return 
	end
	if ((obj.className == "Part") or (obj.className == "Seat") or (obj.className == "SpawnLocation")) then
		local rep = Instance.new("Part")
		rep.formFactor = "Symmetric"
		rep.Size = obj.Size * scale
		rep.Transparency = obj.Transparency
		rep.Reflectance = obj.Reflectance
		rep.BrickColor = obj.brickColor
		rep.Anchored = true
		rep.Locked = true
		rep.TopSurface = Enum.SurfaceType.Smooth
		rep.BottomSurface = Enum.SurfaceType.Smooth
		rep.Shape = obj.Shape
		rep.Name = obj.Name
		mesh = Instance.new("SpecialMesh")
		mesh.Scale = obj.Size * scale / rep.Size
		local list = obj:GetChildren()
		local objMesh = nil
		for x = 1, #list do
			if (list[x].className == "Decal") then
				local copy = list[x]:Clone()
				copy.Parent = rep
			elseif (list[x].className == "SpecialMesh") or (list[x].className == "CylinderMesh") or (list[x].className == "BlockMesh") then
				objMesh = list[x]
			end
		end
		if objMesh ~= nil then
			mesh:Remove()
			mesh = objMesh:Clone()
			if (objMesh.className == "SpecialMesh") and (objMesh.MeshType == Enum.MeshType.FileMesh) then
				mesh.Scale = objMesh.Scale * scale
				mesh.Offset = objMesh.Offset * scale
			else
				mesh.Scale = objMesh.Scale * obj.Size * scale / rep.Size
				mesh.Offset = objMesh.Offset * scale
			end
		elseif obj.Shape == Enum.PartType.Ball then
			mesh.MeshType = Enum.MeshType.Sphere
		elseif obj.Shape == Enum.PartType.Block then
			mesh:Remove()
			mesh = Instance.new("BlockMesh")
			mesh.Scale = obj.Size * scale / rep.Size
			--mesh.MeshType = Enum.MeshType.Brick
		else
			mesh.MeshType = Enum.MeshType.Cylinder
			rep.Shape = Enum.PartType.Ball
		end
		mesh.Parent = rep
		rep.CFrame = cf:toWorldSpace(obj.CFrame + obj.Position * (scale - 1))
		rep.Parent = representation
	else
		if (obj == workspace) or (((obj.className == "Model") or (obj.className == "Tool") or (obj.className == "Hat")) and (obj ~= representation)) then
			local list = obj:GetChildren()
			for x = 1, #list do
				wait()
				makeRepresentation(list[x], cf, name .. "->" .. list[x].Name)
			end
		end
	end
end

local _, t = wait()
print("Reducing...")
makeRepresentation(workspace, CFrame.new(0, 1.2, 0), "Workspace")
local _2, t2 = wait()
print("Done! Time taken: " .. t2 - t .. " seconds.")
