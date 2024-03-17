# WAIS-EMOTES
- #### Author: Ayazwai
- #### Linkedin: [Ayaz Ekrem](https://www.linkedin.com/in/ayaz-ekrem-770305212/)
- #### Instagram: [Ayaz Ekrem](https://www.instagram.com/ayaz.ekremm/)
- ### Sold exclusively at [0resmon](0resmon.tebex.io) tebex.

# How to set it up

- Do not change the name of the script
- Put the script in the resources folder, do not use any folderisation.
- Prefer to start manually in server.cfg as follows.

```
ensure wais-compatibility
ensure [other wais scripts]
ensure wais-emotes

```

# How do I activate the Notification system

- It will be enough to send the message and type by exporting or triggering the Framework or Notification script you are using
- Don't forget to install export according to the framework you are using

```
local ESX = exports['es_extended']:getSharedObject()
local QBCore = exports['qb-core']:GetCoreObject()

Config.Notification = function(message, types)
    exports['okokNotify']:Alert('Title', message, 2500, types, false)
    ESX.ShowNotification(message)
    QBCore.Functions.Notify(message, types)
end
```

# Clothing system integration

- First of all, this system is used for the previewpos system
- skinchanger and ak47_clothing are not supported. Only fivem-appearance, illenium-appearance, codem-appearance and qb-clothing support is available
- Not using this system will not cost you anything. Only the representative character created in the previewpos system will not receive your clothes and content

- For appearances:
- - Replace Appearance name with the name of your own appearance script!

```
    Config.SetExamplePedSkin = function(ePed)
        local ped = PlayerPedId()
        local skin = exports["appearance-name"]:getPedAppearance(ped)
        exports["appearance-name"]:setPedAppearance(ePed, skin)
    end

    Config.GetPedModel = function()
        local ped = PlayerPedId()
        local model = exports["appearance-name"]:getPedModel(ped)
        return joaat(model)
    end

    -- If you don't want to use it, or if none of them are compatible, use
    Config.GetPedModel = function()
        return 1885233650
    end
```

- For qb-clothing:

```
local QBCore = exports['qb-core']:GetCoreObject()

Config.SetExamplePedSkin = function(ePed)
    while next(Config.Skins) == nil do
        Wait(500)
    end
    TriggerEvent('qb-clothing:client:loadPlayerClothing', Config.Skins.skin, ePed)
end

Config.GetPedModel = function()
    TriggerServerEvent('wais:getSkin', QBCore.Functions.GetPlayerData().citizenid)
    while next(Config.Skins) == nil do
        Wait(500)
    end
    return joaat(Config.Skins.model)
end

-- You have to add this function.

function Config.GetPlayerSkin(citizenid, cb)
    local p = promise:new()
    MySQL.Async.fetchAll('SELECT * FROM playerskins WHERE citizenid = @citizenid AND active = @active', {
        ['@citizenid'] = citizenid,
        ['@active'] = 1
    }, function(result)
        if result[1] ~= nil then
            p:resolve({model = tonumber(result[1].model), skin = json.decode(result[1].skin)})
        else
            p:resolve(nil)
        end
    end)
    return Citizen.Await(p)
end
```

- The following edit should be made in fxmanifest for qb-clothing.

```
server_scripts {
    'config.lua',
    '@mysql-async/lib/MySQL.lua',
    'server.lua',
    'converter.lua',
}
```

# How to get the name of the players in shared animations

- You can integrate the framework you use by finding the function below or you can pull the steam name

```
-- For Steam name
Config.GetPlayerNames = function(source)
    local name = GetPlayerName(source)
    return name
end

-- For ESX Framework
local ESX = exports['es_extended']:getSharedObject()
Config.GetPlayerNames = function(source)
    local player = ESX.GetPlayerFromId(source)
    return ('%s - %s[%s]'):format(player.get('firstName'), player.get('lastName'), source)
end

-- For QBCore Framework
local QBCore = exports['qb-core']:GetCoreObject()
Config.GetPlayerNames = function(source)
    local player = QBCore.Functions.GetPlayer(source)
    return ('%s - %s[%s]'):format(player.PlayerData.charinfo.firstname, player.PlayerData.charinfo.lastname, source)
end
```

# How to add an animation category

- Go inside the Config.Animations variable and paste the following table structure one line below the last line

```
-- Icons: home, bookmark, bookmark-fill, confetti, faces, walks, shared, dances, gang, exit, tank

["tablename"] = {
    ["type"] = "emote",
    ["icon"] = "bookmark-fill",
    ["label"] = "Table label",
    ["selected"] = false, -- Don't touch
    ["id"] = 8, -- What is the last category id, add 1 on top of it and write
    ["anims"] = {

    }
},
```

# How can i add animation

- Go to the anims table of the category you want to add
- Inside the Anims table, create a new table below the last table and edit and add the table structures as follows

```
-- Face Animations
["facial_name"] = {
    ["type"] = "face",
    ["anim"] = "", -- Keep it empty
    ["anim_dict"] = "", -- Keep it empty
    ["expression"] = "mood_aiming_1", -- Base name of the facial expression you will add
    ["scenario"] = "", -- Keep it empty
    ["command"] = "anim_name", -- Command name like /e faceanim1
    ["label"] = "Label",
},

-- Walk Animations
["walk_name"] = {
    ["type"] = "walk",
    ["anim"] = "", -- Keep it empty
    ["anim_dict"] = "", -- Keep it empty
    ["scenario"] = "move_m@alien", -- Base name of the scenario you will add
    ["expression"] = "", -- Keep it empty
    ["command"] = "anim_name", -- Command name like /e walkanim
    ["label"] = "Label",
},

-- Default Animastions
["anim_name"] = {
    ["type"] = "animation",    
    ["anim"] = "idle_b", -- animDictionary name of the animation to be made in the base
    ["anim_dict"] = "timetable@denice@ig_1", -- animDictionary base name
    ["scenario"] = "", -- Keep it empty
    ["expression"] = "", -- Keep it empty
    ["command"] = "xxxx", -- Command name like /e xxxx
    ["label"] = "Label",    
    ["options"] = {          
        ["moving"] = false, -- Can you walk while animating? `true`@boolean `false`@boolean         
        ["loop"] = true, -- `true` if it is an animation made forever and `false` if it is not
        ["duration"] = 0, -- If the animation has a duration, enter 1000 = 1 second
    }
},

-- Shared Animations
["anim_name"] = {
    ["type"] = "shared",
    ["anim"] = "chad_armsaround_chad", -- animDictionary name of the animation to be made in the base
    ["anim_dict"] = "misscarsteal2chad_goodbye", -- animDictionary base name
    ["scenario"] = "", -- Keep it empty
    ["expression"] = "", -- Keep it empty
    ["command"] = "xxxx", -- Command name like /e xxxx
    ["label"] = "Shared Anim Label",
    ["options"] = {
        ["moving"] = false, -- Can you walk while animating? `true`@boolean `false`@boolean         
        ["loop"] = false, -- `true` if it is an animation made forever and `false` if it is not
        ["duration"] = 5000, -- If the animation has a duration, enter 1000 = 1 second
        ["target"] = {
            ["anim"] = "chad_armsaround_girl", -- animDictionary name of the animation to be made in the base
            ["anim_dict"] = "misscarsteal2chad_goodbye", -- animDictionary base name
            ["target_positions"] = {
                ["x"] = 0.52,
                ["y"] = -0.05,
                ["z"] = 0.0,
                ["rx"] = 0.0,
                ["ry"] = 0.0,
                ["rz"] = 0.0,
            },
        }
    }
},
```
