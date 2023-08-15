# QBCore Dispatch

Entegreli [ps-mdt](https://github.com/Project-Sloth/ps-mdt)

Destek icin [Discord]Ritalin. 
Bu bir edittir orjinali project sloth adinda birinindir

# Kurulum
* ZIP Indirin
* Sunucu dosyaniza surukleyin
*  server.cfg Server.cfg'den ensure verin
* sounds dosyasina atin interact-sound\client\html\sounds
* Restart your server.

# Uyarilari exports etme.

```lua
- exports['ps-dispatch']:VehicleShooting(vehicle)

- exports['ps-dispatch']:Shooting()

- exports['ps-dispatch']:OfficerDown()

- exports['ps-dispatch']:SpeedingVehicle(vehicle)

- exports['ps-dispatch']:Fight()

- exports['ps-dispatch']:InjuriedPerson()

- exports['ps-dispatch']:DeceasedPerson()

- exports['ps-dispatch']:StoreRobbery(camId)

- exports['ps-dispatch']:FleecaBankRobbery(camId)

- exports['ps-dispatch']:PaletoBankRobbery(camId)

- exports['ps-dispatch']:PacificBankRobbery(camId)

- exports['ps-dispatch']:PrisonBreak()

- exports['ps-dispatch']:VangelicoRobbery(camId)

- exports['ps-dispatch']:HouseRobbery()

- exports['ps-dispatch']:DrugSale()

- exports['ps-dispatch']:ArtGalleryRobbery()

- exports['ps-dispatch']:HumaneRobery()

- exports['ps-dispatch']:TrainRobbery()

- exports['ps-dispatch']:VanRobbery()

- exports['ps-dispatch']:UndergroundRobbery()

- exports['ps-dispatch']:DrugBoatRobbery()

- exports['ps-dispatch']:UnionRobbery()

- exports['ps-dispatch']:YachtHeist()

- exports['ps-dispatch']:CarBoosting(vehicle)

- exports['ps-dispatch']:CarJacking(vehicle)

- exports['ps-dispatch']:VehicleTheft(vehicle)

- exports['ps-dispatch']:SuspiciousActivity()

- exports['ps-dispatch']:SignRobbery()
```

# Basit Preview
![image](https://user-images.githubusercontent.com/82112471/224476364-a362b703-f673-4c34-bd4a-f2b15aa15cb1.png)

# Departman yapilandirma

1. config.lua ac ve alert icin kullanmak istediginiz tum joblari ekleyin

```lua
Config.AuthorizedJobs = {
    LEO = { -- bu, sadece polis memurları için doğru sonuç vermesi gereken job kontrolu içindir
        Jobs = {['police'] = true, ['fib'] = true, ['sheriff'] = true},
        Types = {['police'] = true, ['leo'] = true},
        ...
    },
    EMS = { -- bu, yalnızca ems çalışanları için true döndürmesi gereken job kontrolleri içinse
        Jobs = {['ambulance'] = true, ['fire'] = true},
        Types = {['ambulance'] = true, ['fire'] = true, ['ems'] = true},
        ...
    },
    ...
}
```

- `Jobs` departman için kontrol edilecek joblarin listesidir
- `Types` departman için kontrol edilecek job türlerinin listesidir
-  `Check` ve `FirstResponder` tablolar **not** duzenlenebilir

## Departman ozel alerts

1. sv_dispatchcodes.lua ac ve almak isteginiz alert icin listeye eklemek istediginiz departmani ekleyin

```lua
    -- All jobs with the LEO type will receive this alert
    ["shooting"] =  {displayCode = '10-13', description ="Shots Fired", radius = 0, recipientList = {'LEO', 'police'}, blipSprite = 110, blipColour = 1, blipScale = 1.5, blipLength = 2, sound = "Lose_1st", sound2 = "GTAO_FM_Events_Soundset", offset = "false", blipflash = "false"},
    
    -- Police and FIB will receive this alert, but not other LEO jobs
    ["shooting"] =  {displayCode = '10-13', description ="Shots Fired", radius = 0, recipientList = {'police', 'fib'}, blipSprite = 110, blipColour = 1, blipScale = 1.5, blipLength = 2, sound = "Lose_1st", sound2 = "GTAO_FM_Events_Soundset", offset = "false", blipflash = "false"},
```

- `recipientList` alerts alacak departmanların listesidir

2. cl_events.lua'yı açın ve departmanı yukarıda düzenlediğiniz alerts'e karşılık gelen alerts'e ekleyin

```lua
local function Shooting()
    local currentPos = GetEntityCoords(PlayerPedId())
    local locationInfo = getStreetandZone(currentPos)
    local gender = GetPedGender()
    local PlayerPed = PlayerPedId()
    local CurrentWeapon = GetSelectedPedWeapon(PlayerPed)
    local weapon = WeaponTable[CurrentWeapon] or "UNKNOWN"
    TriggerServerEvent("dispatch:server:notify", {
        dispatchcodename = "shooting", -- sv_dispatchcodes.lua dosyasindaki kodlarla eslesmesi gerekir, boylece dogru bip sesi uretir
        dispatchCode = "10-11",
        firstStreet = locationInfo,
        gender = gender,
        weapon = weapon,
        model = nil,
        plate = nil,
        priority = 2,
        firstColor = nil,
        automaticGunfire = false,
        origin = {
            x = currentPos.x,
            y = currentPos.y,
            z = currentPos.z
        },
        dispatchMessage = _U('shooting'),
        job = {"police", "fib"} -- sv_dispatchcodes.lua dosyasindaki recipientList ile eslesmelidir
    })
end
```

- `job` Alerts alacak departmanların listesidir

# Yeni Alerts Olustur

1. İstediğiniz script dosyasından tetiklenecek bir client etkinliği oluşturun

```lua
local function FleecaBankRobbery(camId)
    local currentPos = GetEntityCoords(PlayerPedId())
    local locationInfo = getStreetandZone(currentPos)
    local gender = GetPedGender()
    TriggerServerEvent("dispatch:server:notify",{
        dispatchcodename = "bankrobbery", -- sv_dispatchcodes.lua dosyasindaki kodlarla eslesmesi gerekir, boylece dogru bip sesi uretir
        dispatchCode = "10-90",
        firstStreet = locationInfo,
        gender = gender,
        camId = camId,
        model = nil,
        plate = nil,
        priority = 2, -- priority
        firstColor = nil,
        automaticGunfire = false,
        origin = {
            x = currentPos.x,
            y = currentPos.y,
            z = currentPos.z
        },
        dispatchMessage = "Fleeca Bank Robbery", -- message
        job = {"police"} -- uyarilari alacak joblar
    })
end exports('FleecaBankRobbery', FleecaBankRobbery)
```

2. Blip'i goruntulemek icin belirli bir soygun icin sv_dispatchcodes.lua ya sevk kodu ekleyin

`["storerobbery"] is the dispatchcodename you passed with the TriggerServerEvent in step 1`
```lua
	["bankrobbery"] =  {displayCode = '10-90', description = "Fleeca Bank Robbery In Progress", radius = 0, recipientList = {'police'}, blipSprite = 500, blipColour = 2, blipScale = 1.5, blipLength = 2, sound = "robberysound"},
```
Her parametre hakkında bilgi dosyadadır.

# Arac bilgileri ve alerts
1. Belirli bir alerts ile araç bilgilerini görüntülemek istiyorsanız, exports ile birlikte aracı bu şekilde iletmeniz gerekir.
```lua 
exports['ps-dispatch']:TestVehicleAlert(vehicle)
```

ve ps-dispatch'teki function şöyle görünürdü

```lua
local function TestVehicleAlert(vehicle)
    local vehdata = vehicleData(vehicle)
    local currentPos = GetEntityCoords(PlayerPedId())
    local locationInfo = getStreetandZone(currentPos)
    local heading = getCardinalDirectionFromHeading()
    TriggerServerEvent("dispatch:server:notify",{
        dispatchcodename = "speeding", -- has to match the codes in sv_dispatchcodes.lua so that it generates the right blip
        dispatchCode = "10-11",
        firstStreet = locationInfo,
        model = vehdata.name, -- vehicle name
        plate = vehdata.plate, -- vehicle plate
        priority = 2, 
        firstColor = vehdata.colour, -- vehicle color
        heading = heading, 
        automaticGunfire = false,
        origin = {
            x = currentPos.x,
            y = currentPos.y,
            z = currentPos.z
        },
        dispatchMessage = "Speeding Vehicle",
        job = {"police"}
    })
end 

exports('SpeedingVehicle', SpeedingVehicle)
```

# Ozel Alerts Handler
```lua
exports["ps-dispatch"]:CustomAlert({
    coords = vector3(0.0, 0.0, 0.0),
    message = "Criminal Activity",
    dispatchCode = "10-4 Rubber Ducky",
    description = "Blip Name here",
    radius = 0,
    sprite = 64,
    color = 2,
    scale = 1.0,
    length = 3,
})

Table Arguements:

displayCode -- The code for the alert ( 10-4, 10-11, 400-9, etc)
message -- Alert message
gender -- TRUE/FALSE  to enable gender data on the alert
plate  -- Plate of a vehicle
priority -- Priority of the alert
firstColor -- Color of the vehicle
automaticGunfire -- TRUE/FALSE Automatic weapon
camId -- Camera ID
callsign -- Callsign on the player
name -- Name of the player
doorCount -- Number of doors the vehicle has
heading -- Heading of an entity
description -- Name of the blip 
radius -- if the blip has a radius
recipientList -- { "police", "ems", "pbso" } Jobs that get the alert 
blipSprite -- Blip Sprite
blipColour -- Blip Color
blipScale -- Blip Color 
blipLength -- Blip Length : How long it stays on the map
offset -- Offset of the blip
blipflash -- If the blip flashes or not
sound -- GTA sound to play 
sound2 -- GTA sound to play

```

# Credits
* Castar#5040 for the waypoint snippet.
* Ritalin snippet icin.
