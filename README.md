<div align="center">
⚡ Octane APIs
An unofficial developer's guide to Octane and Octomon's endpoints
Discover endpoints · Build in Studio · Explore trading data
Octane · Octomon's · Studio examples
</div>
---
> [!IMPORTANT]
> This is a **community-made starting point**, not a complete or official API reference. Endpoints may change, require authentication, or have undocumented limits. Octomon's is a separate trading and analytics website.
>
> **Last researched:** 28 October 2026. Unless marked as tested, endpoints were found in client code and have not been independently tested.
📚 Contents
Octane endpoints
Users and presence · Inventory and catalog · Avatars and thumbnails
Groups · Games, economy and trades · Other paths
Octomon's endpoints
Using APIs in Studio or a game
Sources and limitations
---
⚡ Octane
Base URL
```text
https://octane.wtf/apisite
```
Append the paths below to the base URL. Parameters such as `{userId}` are placeholders; replace them with actual values. Query parameters shown are examples used by the referenced client, not necessarily an exhaustive list.
Users and presence
Method	Path	Description
GET	`/users/v1/users/{userId}`	User profile. Tested with user ID 1: HTTP 200.
GET	`/users/v1/users/search?keyword={keyword}&limit=10`	Search users. Tested with `keyword=test`: HTTP 200.
GET	`/friends/v1/users/{userId}/friends`	List a user's friends.
POST	`/presence/v1/presence/users`	Get presence for users. Example body: `{"userIds":[1,2]}`.
Inventory and catalog
Method	Path	Description
GET	`/inventory/v1/users/{userId}/assets/collectibles?limit=100&sortOrder=Desc`	Collectibles; supports a `cursor` for pagination.
GET	`/inventory/v2/users/{userId}/inventory?assetTypes={types}&limit=100&sortOrder=Desc`	Inventory by comma-separated asset types; supports `cursor`.
GET	`/inventory/v1/users/{userId}/items/Asset/{assetId}`	Check a user's inventory for an asset.
GET	`/catalog/v1/search/items?{query}`	Search catalog items; available query parameters not fully documented here.
POST	`/catalog/v1/catalog/items/details`	Get catalog details. Example body: `{"items":[{"itemType":"Asset","id":123}]}`.
Avatars and thumbnails
Method	Path	Description
GET	`/avatar/v1/avatar-rules`	Avatar rules.
GET	`/avatar/v1/users/{userId}/avatar`	User's avatar.
GET	`/thumbnails/v1/users/avatar-headshot?userIds={ids}&size=48x48&format=png`	Headshot thumbnails for comma-separated user IDs.
GET	`/thumbnails/v1/avatar/try-on?assetId={assetId}&userId={userId}&size=420x420&format=Png`	Try-on thumbnail.
GET	`/thumbnails/v1/games/icons?universeIds={ids}&size=150x150&format=Png`	Game icons for comma-separated universe IDs.
Groups
Method	Path	Description
GET	`/groups/v2/users/{userId}/groups/roles`	User's groups and roles.
GET	`/groups/v1/groups/{groupId}`	Group information.
GET	`/groups/v1/groups/{groupId}/roles`	Group roles.
GET	`/groups/v1/groups/{groupId}/users?limit=100&sortOrder=Desc`	Group members; supports `cursor`.
GET	`/groups/v1/groups/{groupId}/audit-log?limit=100&sortOrder=Desc`	Group audit log; may require permissions.
DELETE	`/groups/v1/groups/{groupId}/users/{userId}`	Remove a group member; requires appropriate permissions. ⚠️ Modifies data.
Games, economy and trades
Method	Path	Description
GET	`/games/v1/games?universeIds={ids}`	Game details for comma-separated universe IDs.
GET	`/economy/v1/assets/{assetId}/resellers?limit=25`	Asset resellers.
GET	`/economy/v1/users/{userId}/currency`	User currency; access may be restricted.
GET	`/trades/v1/trades/{type}?limit={limit}&sortOrder=Desc`	Trades by type; valid type values were not independently verified.
GET	`/trades/v1/trades/{tradeId}`	Individual trade; access may be restricted.
Other Octane paths
These use `https://octane.wtf` directly, without `/apisite`:
Method	Path	Description
GET	`/users/{userId}`	Legacy user data, according to the client.
GET	`/users/favorites/list-json?assetTypeId=9&itemsPerPage=24&pageNumber=1&userId={userId}`	User favorites; example parameters from the client.
Example
```bash
curl 'https://octane.wtf/apisite/users/v1/users/1'
```
For authenticated calls, the referenced client includes the user's session credentials. Its POST and DELETE helper also handles a CSRF challenge: a 403 response may provide an `x-csrf-token` header, which the client sends back as `X-CSRF-TOKEN` on retry. Authentication and token requirements vary by endpoint. Never publish session cookies or CSRF tokens.
---
🐙 Octomon's
Base URL
```text
https://octomons.wtf
```
The following paths were found in Octomon's own browser scripts. HTTP results below are unauthenticated checks performed on 28 October 2026; HTTP 200 alone does not guarantee complete or useful data.
Method	Path	Description	Check
GET	`/api/items/v3/itemdetails`	Item details used by global search.	200
GET	`/api/itemthumbs/v2/thumbssm`	Small item thumbnails used by global search.	200
GET	`/api/market/v2/dealactivity`	Market deal activity used by the deals page.	200
GET	`/api/players/v1/playersearch?searchstring={query}`	Search players.	200 with `test`
GET	`/api/players/v1/playerassets/{userId}`	Live player assets.	200 with ID 1
GET	`/thumbs/avatar?userIds={ids}&size=150x150`	Full avatar thumbnails; comma-separated user IDs. Client also uses `420x420`.	200 with ID 1
GET	`/thumbs/avatarbust?userIds={ids}&size=150x150`	Avatar bust thumbnails; client also uses `420x420`.	200 with ID 1
GET	`/api/games/v1/gamelist`	Game list referenced by search script.	404 when checked
GET	`/api/groups/v1/groupsearch?searchstring={query}`	Group search referenced by search script.	404 with `test`
The last two paths appear in client code but returned 404 in these checks; they may be unavailable, outdated, or subject to conditions not established here.
Example
```bash
curl 'https://octomons.wtf/api/items/v3/itemdetails'
curl 'https://octomons.wtf/api/players/v1/playersearch?searchstring=test'
```
Catalog page data (not a JSON API)
`GET https://octomons.wtf/catalog` returns HTML containing a `var item_details` object. The Kerosene client parses this embedded data for item values and documents its observed array indexes, including name (0), best price (5), RAP (8), value (16), demand (17), trend (18), projected (19), rare (21), thumbnail (23), item type (24), and ID (25). This is an implementation detail, not a stable public API; check the current page and scripts before relying on it.
---
🎮 Using these APIs in Studio or a game
These examples use Lua and `HttpService`, the HTTP and JSON service available in Roblox-style clients. Compatibility with Octane's 2020-based Studio and game servers has not been independently verified. The revival may restrict HTTP requests, block particular hosts, or behave differently from Roblox. HTTP must be enabled in the experience's game settings, and the server must be allowed to reach the host. Test both in Studio and in a published game; a successful `curl` request does not prove a game server can connect.
Use a regular Script in `ServerScriptService`, not a LocalScript. Keep session cookies, API keys and other credentials off clients and out of published source code. The examples use public GET requests only; they do not log in or perform trades. If the engine refuses direct requests to these domains, use a server-side proxy you control only if Octane permits it, with appropriate access controls and caching. Do not use a proxy to bypass restrictions or expose authenticated endpoints.
Example 1: get an Octane user
Open your experience in Studio and enable Allow HTTP Requests in Game Settings (if the option is available).
Add a Script under `ServerScriptService`.
Paste the code below and run the experience. Check the server Output window.
```lua
local HttpService = game:GetService("HttpService")
local userId = 1
local url = "https://octane.wtf/apisite/users/v1/users/" .. tostring(userId)

local ok, result = pcall(function()
    local body = HttpService:GetAsync(url)
    return HttpService:JSONDecode(body)
end)

if not ok then
    warn("Octane request failed: " .. tostring(result))
    return
end

print("User ID:", result.id)
print("Username:", result.name)
print("Display name:", result.displayName)
```
The response for ID 1 included `id`, `name`, and `displayName` when checked. Treat these as observed fields, not a guaranteed schema. Requests can fail because HTTP is disabled, the host is blocked, the endpoint changes, or the service is unavailable.
Example 2: search Octomon's players
Use this in a server Script as well. `UrlEncode` is important when putting user-supplied text in a query string.
```lua
local HttpService = game:GetService("HttpService")
local query = "test"
local url = "https://octomons.wtf/api/players/v1/playersearch?searchstring="
    .. HttpService:UrlEncode(query)

local ok, result = pcall(function()
    return HttpService:JSONDecode(HttpService:GetAsync(url))
end)

if not ok then
    warn("Octomon's search failed: " .. tostring(result))
    return
end

if result.success and result.players then
    for _, player in ipairs(result.players) do
        -- Observed format: [userId, username]. Lua arrays start at 1.
        print("ID:", player[1], "Username:", player[2])
    end
else
    warn("Search returned an unsuccessful or unexpected response")
end
```
The player array format was observed with `searchstring=test`; responses may change. For UI, have the server send only the necessary results to players using a `RemoteEvent` or `RemoteFunction`. Validate and rate-limit requests on the server, cache results where practical, and never let clients choose arbitrary URLs.
POST requests
For a public JSON POST endpoint that the game server is authorized to use, encode the body with `HttpService:JSONEncode` and use `PostAsync` with `Enum.HttpContentType.ApplicationJson`:
```lua
local HttpService = game:GetService("HttpService")
local url = "https://octane.wtf/apisite/presence/v1/presence/users"
local body = HttpService:JSONEncode({ userIds = { 1, 2 } })

local ok, result = pcall(function()
    local response = HttpService:PostAsync(url, body, Enum.HttpContentType.ApplicationJson)
    return HttpService:JSONDecode(response)
end)

if ok then
    print("Presence response received")
else
    warn("Presence request failed: " .. tostring(result))
end
```
This example is based on a third-party client and has not been tested in Octane Studio. It may require a session or CSRF handling, which `PostAsync` alone does not implement. Do not put an account cookie in a game script to make it work. For authenticated or state-changing operations, use an officially supported server-side integration if one is available, and follow the service's rules.
---
📌 Sources and limitations
Octane and Octomon's public pages and their browser scripts.
Kerosene Octane API helpers, avatar client, and Octomon's value parser. These are third-party implementations, not official API documentation.
Octane's main site redirects unauthenticated visitors to login. Authenticated-only pages and their network traffic were not inspected; therefore additional endpoints likely exist.
Respect the sites' terms, privacy settings, and rate limits. Do not share credentials or make state-changing requests without authorization.
