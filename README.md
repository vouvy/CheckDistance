<table>
<tr>
<td>
This script is a basic example of the Lua programming language. It was created for personal use, but feel free to use or study it
</td>
</tr>
</table>

---

## Features
- Setup: Defines a maximum distance and sets up services and URLs for fetching server information.
- ListServers Function: Retrieves a list of public game servers.
- HopServer Function: Chooses a random server from the list and teleports the local player to that server.
- Main Loop: Periodically checks if any other players are within a specified distance from the local player. If so, it initiates a server hop; otherwise, it continues checking every 60 seconds.

## Code Example:
```lua
-- Define the maximum distance to check between players
local maxDistance = 10

-- Get the required services from Roblox
local Http = game:GetService("HttpService")
local TPS = game:GetService("TeleportService")

-- Define the API endpoint to get the list of servers
local Api = "https://games.roblox.com/v1/games/"
local _place = game.PlaceId
local _servers = Api.._place.."/servers/Public?sortOrder=Asc&limit=100"

-- Function to list servers, optionally using a cursor for pagination
function ListServers(cursor)
    local Raw = game:HttpGet(_servers .. ((cursor and "&cursor="..cursor) or ""))
    return Http:JSONDecode(Raw)
end

-- Function to hop (teleport) to a different server
local function HopServer()
    local Server, Next
    repeat
        -- Get a list of servers, potentially using the nextPageCursor for pagination
        local Servers = ListServers(Next)
        local serverIndex = math.random(1, #Servers.data)
        Server = Servers.data[serverIndex]
        Next = Servers.nextPageCursor
    until Server
    -- Teleport the local player to the chosen server
    TPS:TeleportToPlaceInstance(_place, Server.id, game:GetService('Players').LocalPlayer)
end

-- Get the local player
local localPlayer = game.Players.LocalPlayer

-- If the local player is found, start the following loop
if localPlayer then
    spawn(function()
        while true do
            -- Get the position of the local player
            local localPosition = localPlayer.Character.HumanoidRootPart.Position
            local shouldHop = false

            -- Iterate over all players in the game
            for _, player in pairs(game.Players:GetChildren()) do
                -- Check if the player is not the local player and has a HumanoidRootPart
                if player ~= localPlayer and player.Character and player.Character:FindFirstChild("HumanoidRootPart") then
                    -- Get the position of the other player
                    local targetPosition = player.Character.HumanoidRootPart.Position
                    -- Calculate the distance between the local player and the other player
                    local distance = (targetPosition - localPosition).Magnitude

                    -- If the distance is less than or equal to the maximum distance, set the flag to hop servers
                    if distance <= maxDistance then
                        shouldHop = true
                        break
                    end
                end
            end

            -- If the flag is set, hop servers
            if shouldHop then
                print("A player is within " .. maxDistance .. " distance. Hopping server...")
                HopServer()
                break
            else
                print("No players within " .. maxDistance .. " distance.")
            end

            -- Wait for 60 seconds before checking again
            wait(60)
        end
    end)
else
    -- Print an error message if the local player is not found
    print("Local player not found")
end
```
