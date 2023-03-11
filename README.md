# qb-panicbutton

* Add 10-13a and 10-13b panic buttons to the pre-existing police job and radial menu

* Integrated with [ps-mdt]([https://github.com/Project-Sloth/ps-mdt](https://github.com/Project-Sloth/ps-dispatch))

**Dependencies:**

[qb-core](https://github.com/qbcore-framework/qb-core)

[qb-radialmenu](https://github.com/qbcore-framework/qb-radialmenu)

[qb-policejob](https://github.com/qbcore-framework/qb-policejob)

[ps-dispatch](https://github.com/Project-Sloth/ps-dispatch)



**goto qb-radialmenu/client/main.lua**
**Find this local function SetupRadialMenu() and replace it with**

```lua
local function SetupRadialMenu() 
     FinalMenuItems = {} 
     if (IsDowned() and IsPoliceOrEMS()) then 
             FinalMenuItems = { 
                 [1] = { 
                     id = 'emergencybutton', 
                     title = "10-13a", 
                     icon = 'circle-exclamation', 
                     type = 'client', 
                     event = 'police:client:SendPoliceEmergencyAlert', 
                     shouldClose = true, 
                 }, 
				 [2] = { 
                     id = 'emergencybutton2', 
                     title = "10-13b", 
                     icon = 'circle-exclamation', 
                     type = 'client', 
                     event = 'police:client:SendPoliceEmergencyAlert2', 
                     shouldClose = true, 
                 }, 
             } 
     else 
         SetupSubItems() 
         FinalMenuItems = deepcopy(Config.MenuItems) 
         for _, v in pairs(DynamicMenuItems) do 
             FinalMenuItems[#FinalMenuItems+1] = v 
         end 
  
     end 
 end
```

**qb-radialmenu/config.lua**
**Find [police] and replace with**

```lua
["police"] = {
			{
            id = 'emergencybuttons',
            title = 'Panic Buttons',
            icon = 'bell',
            items = {
				{
					id = 'emergencybutton',
					title = '10-13a',
					icon = 'bell',
					type = 'client',
					event = 'police:client:SendPoliceEmergencyAlert',
					shouldClose = true
				}, {
					id = 'emergencybutton2',
					title = '10-13b',
					icon = 'bell',
					type = 'client',
					event = 'police:client:SendPoliceEmergencyAlert2',
					shouldClose = true
				}
			},
		},	{
            id = 'checkvehstatus',
            title = 'Check Tune Status',
            icon = 'circle-info',
            type = 'client',
            event = 'qb-tunerchip:client:TuneStatus',
            shouldClose = true
        }, {
            id = 'resethouse',
            title = 'Reset house lock',
            icon = 'key',
            type = 'client',
            event = 'qb-houses:client:ResetHouse',
            shouldClose = true
        }, {
            id = 'takedriverlicense',
            title = 'Revoke Drivers License',
            icon = 'id-card',
            type = 'client',
            event = 'police:client:SeizeDriverLicense',
            shouldClose = true
        }, {
            id = 'policeinteraction',
            title = 'Police Actions',
            icon = 'list-check',
            items = {
                {
                    id = 'statuscheck',
                    title = 'Check Health Status',
                    icon = 'heart-pulse',
                    type = 'client',
                    event = 'hospital:client:CheckStatus',
                    shouldClose = true
                }, {
                    id = 'checkstatus',
                    title = 'Check status',
                    icon = 'question',
                    type = 'client',
                    event = 'police:client:CheckStatus',
                    shouldClose = true
                }, {
                    id = 'escort',
                    title = 'Escort',
                    icon = 'user-group',
                    type = 'client',
                    event = 'police:client:EscortPlayer',
                    shouldClose = true
                }, {
                    id = 'searchplayer',
                    title = 'Search',
                    icon = 'magnifying-glass',
                    type = 'client',
                    event = 'police:client:SearchPlayer',
                    shouldClose = true
                }, {
                    id = 'jailplayer',
                    title = 'Jail',
                    icon = 'user-lock',
                    type = 'client',
                    event = 'police:client:JailPlayer',
                    shouldClose = true
                }
            }
        }, {
            id = 'policeobjects',
            title = 'Objects',
            icon = 'road',
            items = {
                {
                    id = 'spawnpion',
                    title = 'Cone',
                    icon = 'triangle-exclamation',
                    type = 'client',
                    event = 'police:client:spawnCone',
                    shouldClose = false
                }, {
                    id = 'spawnhek',
                    title = 'Gate',
                    icon = 'torii-gate',
                    type = 'client',
                    event = 'police:client:spawnBarrier',
                    shouldClose = false
                }, {
                    id = 'spawnschotten',
                    title = 'Speed Limit Sign',
                    icon = 'sign-hanging',
                    type = 'client',
                    event = 'police:client:spawnRoadSign',
                    shouldClose = false
                }, {
                    id = 'spawntent',
                    title = 'Tent',
                    icon = 'campground',
                    type = 'client',
                    event = 'police:client:spawnTent',
                    shouldClose = false
                }, {
                    id = 'spawnverlichting',
                    title = 'Lighting',
                    icon = 'lightbulb',
                    type = 'client',
                    event = 'police:client:spawnLight',
                    shouldClose = false
                }, {
                    id = 'spikestrip',
                    title = 'Spike Strips',
                    icon = 'caret-up',
                    type = 'client',
                    event = 'police:client:SpawnSpikeStrip',
                    shouldClose = false
                }, {
                    id = 'deleteobject',
                    title = 'Remove object',
                    icon = 'trash',
                    type = 'client',
                    event = 'police:client:deleteObject',
                    shouldClose = false
                }
            }
        }
    },
```


**ps-dispatch/client/cl_events.lua**
**Find**

```lua
local function OfficerDown()
    local plyData = QBCore.Functions.GetPlayerData()
    local currentPos = GetEntityCoords(PlayerPedId())
    local locationInfo = getStreetandZone(currentPos)
    local callsign = QBCore.Functions.GetPlayerData().metadata["callsign"]
    TriggerServerEvent("dispatch:server:notify", {
        dispatchcodename = "officerdown", -- has to match the codes in sv_dispatchcodes.lua so that it generates the right blip
        dispatchCode = "10-13a",
        firstStreet = locationInfo,
        name = "COP - " .. plyData.charinfo.firstname:sub(1, 1):upper() .. plyData.charinfo.firstname:sub(2) .. " " .. plyData.charinfo.lastname:sub(1, 1):upper() .. plyData.charinfo.lastname:sub(2),
        model = nil,
        plate = nil,
        callsign = callsign,
        priority = 1, -- priority
        firstColor = nil,
        automaticGunfire = false,
        origin = {
            x = currentPos.x,
            y = currentPos.y,
            z = currentPos.z
        },
        dispatchMessage = _U('officerdown'), -- message
        job = { "police" } -- jobs that will get the alerts
    })
end

exports('OfficerDown', OfficerDown)

RegisterNetEvent("ps-dispatch:client:officerdown", function ()
    OfficerDown()
end)
```

**Replace it with:**

```lua
local function OfficerDown()
    local plyData = QBCore.Functions.GetPlayerData()
    local currentPos = GetEntityCoords(PlayerPedId())
    local locationInfo = getStreetandZone(currentPos)
    local callsign = QBCore.Functions.GetPlayerData().metadata["callsign"]
    TriggerServerEvent("dispatch:server:notify", {
        dispatchcodename = "officerdown", -- has to match the codes in sv_dispatchcodes.lua so that it generates the right blip
        dispatchCode = "10-13A",
        firstStreet = locationInfo,
        model = nil,
        plate = nil,
        callsign = callsign,
        priority = 2, -- priority
        firstColor = nil,
        automaticGunfire = false,
        origin = {
            x = currentPos.x,
            y = currentPos.y,
            z = currentPos.z
        },
        dispatchMessage = _U('officerdown'), -- message
        job = { "ambulance", "police" } -- jobs that will get the alerts
    })
end

exports('OfficerDown', OfficerDown)

RegisterNetEvent("ps-dispatch:client:officerdown", function ()
    OfficerDown()
end)

local function OfficerDown2()
    local plyData = QBCore.Functions.GetPlayerData()
    local currentPos = GetEntityCoords(PlayerPedId())
    local locationInfo = getStreetandZone(currentPos)
    local callsign = QBCore.Functions.GetPlayerData().metadata["callsign"]
    TriggerServerEvent("dispatch:server:notify", {
        dispatchcodename = "officerdown2", -- has to match the codes in sv_dispatchcodes.lua so that it generates the right blip
        dispatchCode = "10-13B",
        firstStreet = locationInfo,
        model = nil,
        plate = nil,
        callsign = callsign,
        priority = 2, -- priority
        firstColor = nil,
        automaticGunfire = false,
        origin = {
            x = currentPos.x,
            y = currentPos.y,
            z = currentPos.z
        },
        dispatchMessage = _U('officerdown2'), -- message
        job = { "ambulance", "police" } -- jobs that will get the alerts
    })
end

exports('OfficerDown2', OfficerDown2)

RegisterNetEvent("ps-dispatch:client:officerdown2", function ()
    OfficerDown2()
end)
```

**ps-dispatch/locales/locales.lua**
**find ['officerdown'] and replace with**

```lua
        ['officerdown'] = "Emergency Panic",
        ['officerdown2'] = "Non-Emergency Panic",
```

**ps-dispatch/server/sv_dispatchcodes.lua**
**find ["officerdown"] and replace with**

```lua
	["officerdown"] =  {displayCode = '10-13a', description = "Officer Down", radius = 15.0, recipientList = {'police'}, blipSprite = 526, blipColour = 1, blipScale = 1.5, blipLength = 2, sound = "panicbutton", offset = "false", blipflash = "false"},
	["officerdown2"] =  {displayCode = '10-13b', description = "Officer Down", radius = 15.0, recipientList = {'police'}, blipSprite = 526, blipColour = 1, blipScale = 1.5, blipLength = 2, sound = "panicbutton", offset = "false", blipflash = "false"},
```
