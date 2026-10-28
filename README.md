<div align="center">

# OCTANE / API REFERENCE

### A community-made developer reference

**Endpoints. Trading data. Game integrations.**

[**Explore Octane**](#octane-api) &nbsp; · &nbsp; [**Explore Octomon's**](#octomons-api) &nbsp; · &nbsp; [**Studio Guide**](#studio--game-guide)

`OCTANE` &nbsp; / &nbsp; `OCTOMON’S` &nbsp; / &nbsp; `STUDIO`

<sub>UNOFFICIAL COMMUNITY DOCUMENTATION · LAST CHECKED OCTOBER 2026</sub>

</div>

---

> **Studio use cases:** See the [Studio & Game Guide](#studio--game-guide) for practical Lua examples. [API Examples](#api-examples) contains additional endpoint request templates.

## Start here

| I want to… | Go to |
|:---|:---|
| Find users, inventories, groups, or trades | [Octane API](#octane-api) |
| Access item data, prices, or player search | [Octomon's API](#octomons-api) |
| Use an endpoint inside a game | [Studio & Game Guide](#studio--game-guide) |
| Explore more community-captured paths | [Community Endpoint Index](#community-endpoint-index) |

> [!IMPORTANT]
> This is an **unofficial, incomplete** reference. Some endpoints were discovered in client code rather than tested directly. They can change, require authentication, or stop working without notice. Octomon's is a separate site from Octane.

---

## Octane API

**More endpoints:** See [Community Endpoint Index](#community-endpoint-index) for the user-supplied unofficial reference, including chat, badges, catalog, legacy paths, and more. Some original paths lack confirmed hosts.

> User profiles, inventories, avatars, groups, games, and trades.

**Base URL**

```text
https://octane.wtf/apisite
```

Append the paths below to the base URL. Parameters such as `{userId}` are placeholders; replace them with actual values. Query parameters shown are examples used by the referenced client, not necessarily an exhaustive list.

### Users and presence

| Method | Path | Description |
|---|---|---|
| GET | `/users/v1/users/{userId}` | User profile. Tested with user ID 1: HTTP 200. |
| GET | `/users/v1/users/search?keyword={keyword}&limit=10` | Search users. Tested with `keyword=test`: HTTP 200. |
| GET | `/friends/v1/users/{userId}/friends` | List a user's friends. |
| POST | `/presence/v1/presence/users` | Get presence for users. Example body: `{"userIds":[1,2]}`. |

### Additional endpoints observed in a signed-in session

These paths were supplied from a browser resource log. **The log did not include HTTP methods, response bodies, or status codes**, so those details and authentication requirements remain unverified. Some may concern the current signed-in user or require specific query parameters.

| Path | Apparent purpose |
|---|---|
| `/accountsettings/v1/themes/user` | User theme settings. |
| `/api/alerts/alert-info` | Alert information. |
| `/friends/v1/user/following-exists` | Following relationship check. |
| `/friends/v1/users/{userId}/followers/count` | Follower count. |
| `/friends/v1/users/{userId}/followings/count` | Following count. |
| `/friends/v1/users/{userId}/friends/statuses` | Friend relationship statuses. |
| `/groups/v1/users/{userId}/groups/roles` | User group roles; a separate v1 path from the v2 endpoint below. |
| `/users/v1/users/{userId}/status` | User status. |
| `/users/v1/users/{userId}/username-history` | Username history. |
| `/friends/v1/user/friend-requests/count` | Friend request count. |
| `/privatemessages/v1/messages/unread/count` | Unread private-message count. |
| `/trades/v1/trades/inbound/count` | Inbound trade count. |

### Inventory and catalog

| Method | Path | Description |
|---|---|---|
| GET | `/inventory/v1/users/{userId}/assets/collectibles?limit=100&sortOrder=Desc` | Collectibles; supports a `cursor` for pagination. |
| GET | `/inventory/v2/users/{userId}/inventory?assetTypes={types}&limit=100&sortOrder=Desc` | Inventory by comma-separated asset types; supports `cursor`. |
| GET | `/inventory/v1/users/{userId}/items/Asset/{assetId}` | Check a user's inventory for an asset. |
| GET | `/catalog/v1/search/items?{query}` | Search catalog items; available query parameters not fully documented here. |
| POST | `/catalog/v1/catalog/items/details` | Get catalog details. Example body: `{"items":[{"itemType":"Asset","id":123}]}`. |

### Avatars and thumbnails

| Method | Path | Description |
|---|---|---|
| GET | `/avatar/v1/avatar-rules` | Avatar rules. |
| GET | `/avatar/v1/users/{userId}/avatar` | User's avatar. |
| GET | `/thumbnails/v1/users/avatar-headshot?userIds={ids}&size=48x48&format=png` | Headshot thumbnails for comma-separated user IDs. |
| GET | `/thumbnails/v1/avatar/try-on?assetId={assetId}&userId={userId}&size=420x420&format=Png` | Try-on thumbnail. |
| GET | `/thumbnails/v1/games/icons?universeIds={ids}&size=150x150&format=Png` | Game icons for comma-separated universe IDs. |

### Groups

| Method | Path | Description |
|---|---|---|
| GET | `/groups/v2/users/{userId}/groups/roles` | User's groups and roles. |
| GET | `/groups/v1/groups/{groupId}` | Group information. |
| GET | `/groups/v1/groups/{groupId}/roles` | Group roles. |
| GET | `/groups/v1/groups/{groupId}/users?limit=100&sortOrder=Desc` | Group members; supports `cursor`. |
| GET | `/groups/v1/groups/{groupId}/audit-log?limit=100&sortOrder=Desc` | Group audit log; may require permissions. |
| DELETE | `/groups/v1/groups/{groupId}/users/{userId}` | Remove a group member; requires appropriate permissions. **Modifies data.** |

### Games, economy and trades

| Method | Path | Description |
|---|---|---|
| GET | `/games/v1/games?universeIds={ids}` | Game details for comma-separated universe IDs. |
| GET | `/economy/v1/assets/{assetId}/resellers?limit=25` | Asset resellers. |
| GET | `/economy/v1/users/{userId}/currency` | User currency; access may be restricted. |
| GET | `/trades/v1/trades/{type}?limit={limit}&sortOrder=Desc` | Trades by type; valid type values were not independently verified. |
| GET | `/trades/v1/trades/{tradeId}` | Individual trade; access may be restricted. |

### Other Octane paths

These use `https://octane.wtf` directly, **without** `/apisite`:

| Method | Path | Description |
|---|---|---|
| GET | `/account/settings/countries` | Countries for account settings; observed HTTP 200 in a user-provided screenshot. |
| GET | `/users/{userId}` | Legacy user data, according to the client. |
| GET | `/users/favorites/list-json?assetTypeId=9&itemsPerPage=24&pageNumber=1&userId={userId}` | User favorites; example parameters from the client. |

### Additional endpoints found in Nexus

These URLs were found in the third-party [Nexus extension](https://github.com/zykieboi/nexus). Checks below were made without login on 29 October 2026; the status is for the specific example only, not a guarantee for all users or assets.

| Method | URL | Purpose and check |
|---|---|---|
| GET | `https://octane.wtf/apisite/economy/v2/assets/{assetId}/details` | Asset/product details. Asset `121969`: HTTP 200. |
| GET | `https://octane.wtf/apisite/games/v1/games/list?keyword={keyword}&maxRows=25` | Game search. Keyword `test`: HTTP 200. |
| GET | `https://octane.wtf/apisite/games/v1/games/multiget-playability-status?universeIds={ids}` | Playability for comma-separated universe IDs. ID `1`: HTTP 200. |
| GET | `https://octane.wtf/apisite/thumbnails/v1/assets?assetIds={ids}&size=150x150&format=Png` | Asset thumbnails. Asset `121969`: HTTP 200. |
| GET | `https://inventory.octane.wtf/v1/users/{userId}/assets/collectibles?sortOrder=Desc&limit=100` | Collectibles on the inventory service. User `1`: HTTP 403; inventory permissions may apply. |
| GET | `https://inventory.octane.wtf/v2/users/{userId}/inventory?assetTypes={types}` | Inventory by asset type on the inventory service. User `1`, asset type `8`: HTTP 403; inventory permissions may apply. |
| POST | `https://octane.wtf/apisite/friends/v1/users/{userId}/unfriend` | Remove a friend. Nexus sends JSON `targetUserId`; state-changing and **not tested**. Requires appropriate authentication/authorization. |

### Additional service hosts

The following full URLs were observed in a user-supplied browser resource log. **Methods, query parameters, status codes, and response bodies were not captured.** A recorded request may have failed; an apparent counterpart under `/apisite` does not establish identical behavior. Descriptions are inferred from path names.

| Host | Path | Apparent purpose |
|---|---|---|
| `friends.octane.wtf` | `/v1/users/{userId}/friends` | User friends. |
| `games.octane.wtf` | `/v1/games/list` | Game list. |
| `games.octane.wtf` | `/v1/games/multiget-place-details` | Multiple place details. |
| `games.octane.wtf` | `/v1/games/sorts` | Game sorts. |
| `metrics.octane.wtf` | `/v1/performance/send-measurement` | Performance measurement. |
| `metrics.octane.wtf` | `/v1/thumbnails/metadata` | Thumbnail metrics metadata. |
| `notifications.octane.wtf` | `/v2/notifications/get-settings` | Notification settings. |
| `presence.octane.wtf` | `/v1/presence/register-app-presence` | App presence registration. |
| `presence.octane.wtf` | `/v1/presence/users` | User presence. |
| `privatemessages.octane.wtf` | `/v1/announcements/metadata` | Announcement metadata. |
| `privatemessages.octane.wtf` | `/v1/messages` | Private messages. |
| `realtime.octane.wtf` | `/notifications/negotiate` | Real-time notifications negotiation. |
| `realtime.octane.wtf` | `/notifications/start` | Start real-time notifications. |
| `thumbnails.octane.wtf` | `/v1/games/icons` | Game icon thumbnails. |
| `thumbnails.octane.wtf` | `/v1/users/avatar-headshot` | Avatar headshots. |
| `trades.octane.wtf` | `/v1/trades/inbound` | Inbound trades. |

Prefix each host with `https://` and append its path. Do not assume these host-specific URLs accept the same parameters, authentication, or methods as similarly named paths on other hosts.

### Inventory service on a separate host

| Method | URL | Description |
|---|---|---|
| GET | `https://inventory.octane.wtf/v2/assets/{assetId}/owners` | Asset owners; tested with asset ID `121969` and received HTTP 200. A user-provided third-party changelog reports pagination via `nextPageCursor` and fields including `owner`, `serialNumber`, `created`, and `updated`; those fields and pagination have not been independently verified. |

### Additional account and privacy paths

These paths use `https://octane.wtf` directly, **without** `/apisite`. They were captured in a signed-in browser's Fetch/XHR resource log. Their methods, status codes, query parameters, responses, and authentication requirements were not captured; descriptions are inferred from the paths. Some may read or modify sensitive settings, so do not assume they are public or safe to change.

| Path | Apparent purpose |
|---|---|
| `/account/settings/account-country` | Account country. |
| `/account/settings/account-restrictions` | Account restrictions. |
| `/account/settings/app-chat-privacy` | App chat privacy. |
| `/account/settings/follow-me-privacy` | Follow-me privacy. |
| `/account/settings/game-chat-privacy` | Game chat privacy. |
| `/account/settings/private-message-privacy` | Private message privacy. |
| `/account/settings/private-server-invite-privacy` | Private server invite privacy. |
| `/account/settings/settings-groups` | Account settings groups. |
| `/my/settings/json` | Account settings JSON. |
| `/user-sponsorship/2` | Sponsorship-related request; purpose and meaning of `2` are unverified. |
| `/usercheck/show-tos` | Terms-of-service check. |
| `/v1/inventory-privacy` | Inventory privacy. |
| `/v1/privacy` | Privacy settings. |
| `/v1/privacy/info` | Privacy information. |
| `/v1/social/connected-providers` | Connected social providers. |
| `/v1/themes/types` | Available theme types. |
| `/v1/themes/user` | User theme. |
| `/v1/trade-privacy` | Trade privacy. |
| `/v1/trade-value` | Trade value settings. |
| `/v1/xbox/connection` | Xbox connection. |
| `/v2/notifications/get-settings` | Notification settings. |

### Example

```bash
curl 'https://octane.wtf/apisite/users/v1/users/1'
```

For authenticated calls, the referenced client includes the user's session credentials. Its POST and DELETE helper also handles a CSRF challenge: a 403 response may provide an `x-csrf-token` header, which the client sends back as `X-CSRF-TOKEN` on retry. Authentication and token requirements vary by endpoint. Never publish session cookies or CSRF tokens.

---

## Octomon's API

> Player search, item details, thumbnails, and trading data.

**Base URL**

```text
https://octomons.wtf
```

The following paths were found in Octomon's own browser scripts. HTTP results below are unauthenticated checks performed on 29 October 2026; HTTP 200 alone does not guarantee complete or useful data.

| Method | Path | Description | Check |
|---|---|---|---|
| GET | `/api/items/v3/itemdetails` | Item details used by global search. | 200 |
| GET | `/api/itemthumbs/v2/thumbssm` | Small item thumbnails used by global search. | 200 |
| GET | `/api/market/v2/dealactivity` | Market deal activity used by the deals page. | 200 |
| GET | `/api/market/v2/saleactivity` | Market sale activity. | 200 |
| GET | `/api/tradeads/v1/getrecentads` | Recent trade ads. The trades page describes these as ads created in the past three minutes. | 200 |
| GET | `/api/players/v1/playersearch?searchstring={query}` | Search players. | 200 with `test` |
| GET | `/api/players/v1/playerassets/{userId}` | Live player assets. | 200 with ID 1 |
| GET | `/thumbs/avatar?userIds={ids}&size=150x150` | Full avatar thumbnails; comma-separated user IDs. Client also uses `420x420`. | 200 with ID 1 |
| GET | `/thumbs/avatarbust?userIds={ids}&size=150x150` | Avatar bust thumbnails; client also uses `420x420`. | 200 with ID 1 |
| GET | `/api/games/v1/gamelist` | Game list referenced by search script. | **404** when checked |
| GET | `/api/groups/v1/groupsearch?searchstring={query}` | Group search referenced by search script. | **404** with `test` |

The last two paths appear in client code but returned 404 in these checks; they may be unavailable, outdated, or subject to conditions not established here.

### Additional state-changing endpoints

These were found during a broader scan of Octomon's browser scripts. **They were not tested** because they change data. The site client sends credentials with each request; authentication and permission requirements have not been independently verified.

| Method | Path | Observed purpose | Observed request body |
|---|---|---|---|
| POST | `/api/players/v1/addplayerbyname` | Add a player by username. | JSON object containing `player_name`. |
| POST | `/api/tradeads/v1/createad` | Create a trade ad. | JSON object containing `player_id`, `offer_item_ids`, `request_item_ids`, `request_tags`, and optionally `offer_robux`. |
| POST | `/api/tradeads/v1/removead/{adId}` | Remove a trade ad. | No body seen in the client call. |

Only use these actions with authorization. A trade ad is not the same thing as submitting a trade on Octane.

### Example

```bash
curl 'https://octomons.wtf/api/items/v3/itemdetails'
curl 'https://octomons.wtf/api/players/v1/playersearch?searchstring=test'
```

### Catalog page data (not a JSON API)

`GET https://octomons.wtf/catalog` returns HTML containing a `var item_details` object. The [Kerosene client](https://github.com/wozers0/kerosene/blob/main/shared/values.js) parses this embedded data for item values and documents its observed array indexes, including name (0), best price (5), RAP (8), value (16), demand (17), trend (18), projected (19), rare (21), thumbnail (23), item type (24), and ID (25). This is an implementation detail, not a stable public API; check the current page and scripts before relying on it.

---

## Studio & Game Guide

> Practical Lua examples for making requests from a game server.

These examples use Lua and `HttpService`, the HTTP and JSON service available in Roblox-style clients. **Compatibility with Octane's 2020-based Studio and game servers has not been independently verified.** The revival may restrict HTTP requests, block particular hosts, or behave differently from Roblox. HTTP must be enabled in the experience's game settings, and the server must be allowed to reach the host. Test both in Studio and in a published game; a successful `curl` request does not prove a game server can connect.

Use a regular **Script** in `ServerScriptService`, not a LocalScript. Keep session cookies, API keys and other credentials off clients and out of published source code. The examples use public GET requests only; they do not log in or perform trades. If the engine refuses direct requests to these domains, use a server-side proxy you control **only if Octane permits it**, with appropriate access controls and caching. Do not use a proxy to bypass restrictions or expose authenticated endpoints.

### 01 — Get an Octane user

1. Open your experience in Studio and enable **Allow HTTP Requests** in Game Settings (if the option is available).
2. Add a **Script** under `ServerScriptService`.
3. Paste the code below and run the experience. Check the server Output window.

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

### 02 — Search Octomon's players

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

### 03 — Display a player's friends

**Use case:** Build a friends panel or show which friends are online. Put this in a server Script and replace `userId` with the Octane user ID you want to look up. The sample prints the results; you can send the fields you need to a UI using a RemoteEvent.

```lua
local HttpService = game:GetService("HttpService")
local userId = 1
local url = "https://octane.wtf/apisite/friends/v1/users/"
    .. tostring(userId) .. "/friends"

local ok, response = pcall(function()
    return HttpService:JSONDecode(HttpService:GetAsync(url))
end)

if not ok then
    warn("Could not load friends: " .. tostring(response))
    return
end

for _, friend in ipairs(response.data or {}) do
    print(friend.displayName or friend.name, "Online:", friend.isOnline)
end
```

A successful request may still return an empty list, and the `isOnline` field may not indicate exactly where the user is. Check the response in your environment before building around it.

### 04 — Show limited item names

**Use case:** Make a trading-information board that lists items tracked by Octomon's. This example reads the item names; it does not make trades or purchase anything.

```lua
local HttpService = game:GetService("HttpService")
local url = "https://octomons.wtf/api/items/v3/itemdetails"

local ok, response = pcall(function()
    return HttpService:JSONDecode(HttpService:GetAsync(url))
end)

if not ok then
    warn("Could not load items: " .. tostring(response))
    return
end

if not response.success or type(response.assets) ~= "table" then
    warn("Unexpected item response")
    return
end

for itemId, details in pairs(response.assets) do
    -- The first element was the item name in the response checked.
    print("Item:", itemId, "Name:", details[1])
end
```

The endpoint returned an `assets` object keyed by item ID, with the item name first in each array when checked. Other array positions aren't documented here; verify them before using them as prices or values. Cache the response rather than fetching it separately for every player.

### 05 — Get an avatar headshot URL

**Use case:** Look up a headshot for a player directory or profile panel. This prints the returned URL; whether an Octane `ImageLabel` can display an external URL depends on the revival's client and content rules.

```lua
local HttpService = game:GetService("HttpService")
local userId = 1
local url = "https://octane.wtf/apisite/thumbnails/v1/users/avatar-headshot?userIds="
    .. tostring(userId) .. "&size=48x48&format=png"

local ok, response = pcall(function()
    return HttpService:JSONDecode(HttpService:GetAsync(url))
end)

if not ok then
    warn("Could not load headshot: " .. tostring(response))
    return
end

local thumbnail = response.data and response.data[1]
if thumbnail and thumbnail.state == "Completed" and thumbnail.imageUrl then
    print("Headshot URL:", thumbnail.imageUrl)
else
    warn("Headshot isn't ready or wasn't returned")
end
```

### 06 — Send a POST request

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

This example is based on a third-party client and **has not been tested in Octane Studio**. It may require a session or CSRF handling, which `PostAsync` alone does not implement. Do not put an account cookie in a game script to make it work. For authenticated or state-changing operations, use an officially supported server-side integration if one is available, and follow the service's rules.

---

## Sources & Limitations

- [Nexus browser extension](https://github.com/zykieboi/nexus), a third-party client.
- User-supplied browser resource log from a signed-in Octane session (paths only; methods and statuses not included).
- [Octane](https://octane.wtf/) and [Octomon's](https://octomons.wtf/) public pages and their browser scripts. A follow-up scan covered 90 reachable Octomon's page paths, their inline scripts, and 20 referenced non-library scripts; it does not cover every page or authenticated workflow.
- [Kerosene Octane API helpers](https://github.com/wozers0/kerosene/blob/main/shared/settings.js), [avatar client](https://github.com/wozers0/kerosene/blob/main/content/avatar-main.js), and [Octomon's value parser](https://github.com/wozers0/kerosene/blob/main/shared/values.js). These are third-party implementations, not official API documentation.
- Octane's main site redirects unauthenticated visitors to login. Authenticated-only pages and their network traffic were not inspected; therefore additional endpoints likely exist.
- Respect the sites' terms, privacy settings, and rate limits. Do not share credentials or make state-changing requests without authorization.

---

<div align="center">

**BUILT FOR THE OCTANE COMMUNITY**

*Not affiliated with Octane, Octomon’s, or Roblox.*

[Back to top](#octane--api-reference)

</div>

---

## Community Endpoint Index

This index is transcribed from an **unofficial reference supplied by a user**, attributed in the supplied text to H█I. Methods and parameter notes are claims from that reference, not independently verified here. The original document says the API list is incomplete and many responses were not captured.

> **Host ambiguity:** The supplied reference says its base URL is `https://octane.wtf/`, but many paths such as `/v1/users/{userId}/friends` appear under service-specific sections without a host. Other captures show service subdomains, and the reference explicitly names `privatemessages.octane.wtf` for one section. Do **not** assume that every relative path works on the root host or substitute a service hostname without confirming the original request. Paths below preserve what the reference provides. Where an explicit `/apisite/` or legacy root path is given, it is reproduced as supplied, but still needs testing.

Response examples have not been copied: some contain account data, and some are truncated or malformed. The original claims some examples are captures; their accuracy and shapes have not been independently checked. Query parameters, authentication and response bodies may be missing.

### Account information and promotion channels

| Method | Path | Notes from supplied reference |
|---|---|---|
| GET, POST | `/v1/description` | Account description. |
| GET | `/v1/birthdate` | Birthdate. |
| GET, POST | `/v1/gender` | Gender. |
| GET | `/v1/metadata` | Account metadata; path occurs in multiple service sections, so host matters. |
| GET | `/v1/phone` | Phone settings. |
| GET, POST | `/v1/promotion-channels` | Promotion channels. |
| GET | `/v1/users/{userId}/promotion-channels` | User promotion channels. |

### Avatar

| Method | Path |
|---|---|
| GET | `/v1/avatar` |
| GET | `/v1/avatar-rules` |
| GET | `/v1/avatar/metadata` |
| GET | `/v1/recent-items/all/list` |
| GET | `/v1/users/{userId}/avatar` |
| POST | `/v1/avatar/assets/{assetId}/wear` |
| POST | `/v1/avatar/set-wearing-assets` |
| POST | `/v1/avatar/redraw-thumbnail` |

### Badges

| Method | Path | Notes |
|---|---|---|
| GET | `/v1/universes/{universeId}/badges` | `cursor`, `limit`, `sortOrder`. |
| DELETE | `/v1/user/badges/{badgeId}` | State-changing. |

### Catalog

| Method | Path | Notes |
|---|---|---|
| GET | `/v1/catalog/metadata` | |
| GET | `/v1/categories` | |
| GET | `/v1/subcategories` | |
| GET | `/v1/search/navigation-menu-items` | |
| GET | `/v1/search/items` | `category`, `subcategory`, `keyword`, `limit`. |
| GET | `/v1/recommendations/metadata` | |
| POST | `/v1/catalog/items/details` | |

### Chat and contacts

| Method | Path | Notes |
|---|---|---|
| GET | `/v2/get-messages` | |
| POST | `/v2/send-message` | State-changing. |
| POST | `/v2/update-user-typing-status` | |
| GET | `/v2/metadata` | Chat metadata. |
| GET | `/v2/get-unread-conversation-count` | |
| GET | `/v2/get-user-conversations` | `pageNumber`, `pageSize`. |
| GET | `/v2/multi-get-latest-messages` | `conversationIds`, `pageSize`. |
| POST | `/v1/user/get-tags` | |
| GET | `/v1/contacts/metadata` | |

### Games

The user's separate browser log observed some of these paths on `games.octane.wtf`; see the main README. That does not verify every path or method below.

| Method | Path | Notes |
|---|---|---|
| GET | `/v1/games` | Requires `universeIds`. |
| GET | `/v1/games/list` | |
| GET | `/v1/games/multiget-place-details` | Requires `placeIds`. |
| GET | `/v1/games/multiget-playability-status` | Requires `universeIds`. |
| GET | `/v1/games/sorts` | |
| GET | `/v1/games/votes` | Requires `universeIds`; reference says missing it returns 400. |
| GET | `/v1/games/{universeId}/favorites` | |
| GET | `/v1/games/{universeId}/social-links/list` | |
| GET | `/v2/users/{userId}/games` | |
| GET | `/v2/groups/{groupId}/games` | |
| GET | `/v2/games/{universeId}/media` | |

### Groups

| Method | Path | Notes |
|---|---|---|
| GET | `/v1/groups/{groupId}` | |
| GET | `/v1/groups/{groupId}/membership` | |
| GET | `/v1/groups/{groupId}/roles` | |
| GET | `/v1/groups/{groupId}/roles/{roleId}/users` | |
| GET | `/v1/groups/{groupId}/social-links` | |
| GET | `/v1/groups/{groupId}/payouts` | |
| GET | `/v1/groups/metadata` | |
| GET | `/v1/groups/search` | `keyword`, `limit`, `cursor`, `sortOrder`. |
| GET | `/v1/users/{userId}/groups/primary/role` | |
| GET | `/v1/users/{userId}/groups/roles` | |
| GET | `/v2/groups/{groupId}/wall/posts` | |

### Friends, inventory, economy

| Method | Path | Notes |
|---|---|---|
| GET | `/v1/users/{userId}/friends` | Observed separately on `friends.octane.wtf`. |
| POST | `/v1/users/{userId}/unfriend` | State-changing. |
| GET | `/v1/users/{userId}/friends/statuses` | `userIds`. |
| GET | `/v1/users/{userId}/assets/collectibles` | `assetType`, `cursor`, `limit`, `sortOrder`. |
| GET | `/v2/recommendations/{userId}` | |
| GET | `/v1/users/{userId}/currency` | |
| GET | `/v1/users/{userId}/revenue/summary/day` | |

### Locale, metrics, presence, notifications

| Method | Path | Notes |
|---|---|---|
| GET | `/v1/locales/user-localization-locus-supported-locales` | |
| GET | `/v1/locales` | |
| GET | `/v1/thumbnails/metadata` | Observed separately on `metrics.octane.wtf`. |
| POST | `/v1/performance/send-measurement` | Observed separately on `metrics.octane.wtf`. |
| GET | `/v2/notifications/get-settings` | Observed separately on `notifications.octane.wtf`. |
| GET | `/notifications/negotiate` | Observed separately on `realtime.octane.wtf`. |
| GET | `/notifications/start` | Observed separately on `realtime.octane.wtf`. |
| POST | `/notifications/abort` | |
| POST | `/v1/presence/users` | Observed separately on `presence.octane.wtf`. |
| POST | `/v1/presence/register-app-presence` | Observed separately on `presence.octane.wtf`. |

### Thumbnails

| Method | Path | Notes |
|---|---|---|
| GET | `/v1/assets` | `assetIds`, `format`, `size`, `returnPolicy`. |
| GET | `/v1/badges/icons` | |
| GET | `/v1/games/icons` | Observed separately on `thumbnails.octane.wtf`. |
| GET | `/v1/games/multiget/thumbnail` | |
| GET | `/v1/groups/icons` | |
| GET | `/v1/places/gameicons` | |
| GET | `/v1/users/avatar` | |
| GET | `/v1/users/avatar-headshot` | Observed separately on `thumbnails.octane.wtf`. |

### Trades, premium, two-step and users

| Method | Path | Notes |
|---|---|---|
| GET | `/v1/trades/inbound` | Observed separately on `trades.octane.wtf`. |
| GET | `/v1/trades/outbound` | |
| GET | `/v1/trades/inactive` | |
| GET | `/v1/trades/{tradeId}` | |
| POST | `/v1/trades/send` | State-changing. |
| POST | `/v1/trades/{tradeId}/decline` | State-changing. |
| GET | `/v1/users/{userId}/subscriptions` | Premium subscription. |
| GET | `/v1/users/{userId}/configuration` | Two-step verification configuration. |
| GET | `/v1/users/{userId}` | User details. |
| PATCH | `/v1/users/{userId}/display-names` | State-changing. |
| POST | `/v1/users` | Source describes this as internal only. |

### Explicitly rooted paths from the supplied reference

These are paths under `https://octane.wtf` as provided. Methods are attributed to the supplied reference, not independently verified. Never send sensitive or state-changing requests without permission.

| Method | Path | Notes |
|---|---|---|
| GET | `/account/settings/account-country` | |
| GET | `/account/settings/account-restrictions` | |
| GET | `/account/settings/app-chat-privacy` | |
| GET | `/account/settings/follow-me-privacy` | |
| GET | `/account/settings/game-chat-privacy` | |
| GET | `/account/settings/private-message-privacy` | |
| GET | `/account/settings/private-server-invite-privacy` | |
| POST | `/home/updatestatus` | State-changing. |
| POST | `/apisite/economy/v1/purchases/products/{productId}` | Purchase action; state-changing. |
| GET | `/apisite/friends/v1/users/{userId}/followers/count` | |
| GET | `/apisite/friends/v1/users/{userId}/followings/count` | |
| GET | `/apisite/friends/v1/user/friend-requests/count` | |
| POST | `/apisite/friends/v1/user/following-exists` | |
| GET | `/apisite/privatemessages/v1/messages/unread/count` | |
| GET | `/apisite/privatemessages/v1/messages/{userId}/can-message` | |
| GET | `/apisite/trades/v1/trades/inbound/count` | |
| GET | `/apisite/trades/v1/users/{userId}/can-trade-with` | |
| GET | `/apisite/users/v1/users/authenticated` | |
| GET | `/apisite/users/v1/users/{userId}/status` | |
| GET | `/apisite/users/v1/users/{userId}/username-history` | |

### Private messages — explicit service host

Host: `https://privatemessages.octane.wtf`. Methods attributed to the supplied reference.

| Method | Path | Notes |
|---|---|---|
| GET | `/v1/announcements/metadata` | |
| GET | `/v1/messages` | |
| POST | `/v1/messages/send` | Sends a message; state-changing. |

### Themes and privacy

These relative paths appear under the reference's stated root base URL, and several were also observed at the root in the user-supplied browser log. Methods remain third-party claims.

| Method | Path |
|---|---|
| GET | `/v1/themes/types` |
| GET, PATCH | `/v1/themes/user` |
| GET | `/v1/privacy` |
| GET | `/v1/privacy/info` |
| GET, POST | `/v1/inventory-privacy` |
| GET, POST | `/v1/trade-privacy` |
| GET | `/v1/trade-value` |
| GET | `/v1/social/connected-providers` |
| GET | `/v1/xbox/connection` |

### Legacy paths — stated root base

| Method | Path |
|---|---|
| GET | `/asset/profile-status` |
| GET | `/avatar-thumbnail-3d/json` |
| GET | `/build/universes` |
| GET | `/comments/get-json` |
| GET | `/game/get-join-script` |
| GET | `/games/getfriendsgameinstances` |
| GET | `/games/getgameinstancesjson` |
| GET | `/games/votingservice/{id}` |
| GET | `/my/settings/json` |
| GET | `/private-server/instance-list-json` |
| GET | `/recipe?iteration` |
| GET | `/referral/v1/me` |
| GET | `/search/users/metadata` |
| GET | `/search/users/results` |
| GET | `/usercheck/show-tos` |
| GET | `/users/favorites/list-json` |
| GET | `/users/inventory/list-json` |
| GET | `/users/profile/playerassets-json` |
| GET | `/users/profile/robloxcollections-json` |
| POST | `/abuse-report/v1/report` |
| POST | `/authentication/signoutfromallsessionsandreauthenticate` |
| POST | `/cdn-cgi/rum` |
| POST | `/money.aspx/getmytransactions` |
| POST | `/promocodes/redeem` |
| POST | `/v1/username` |

### Source note

The supplied reference lists common HTTP status codes and mentions `429` and `Retry-After`; no universal rate limit or guaranteed header behavior has been independently verified. It also documents `GET https://inventory.octane.wtf/v2/assets/{assetId}/owners`, which is included in the main README and was independently checked with an example asset. For further confirmation, obtain the original host, method and sanitized request details in browser DevTools. Never publish authentication headers, cookies, CSRF tokens, private messages, birthdates, or phone numbers.

---

## API Examples

Examples for every endpoint entry in the two reference files. These are **request templates**, not a promise that an endpoint is public or works in Octane Studio. Methods and hosts are only filled in when the source provides them; query strings, JSON bodies and permissions often still need confirmation. Placeholder values must be replaced.

### Studio setup

Enable HTTP requests if supported and put the following in a **server Script** in `ServerScriptService`. Compatibility with Octane Studio has not been verified. Replace `url` and (for non-GET requests) `method` and `body` with a confirmed example from below. Do not send credentials from a game client.

```lua
local HttpService = game:GetService("HttpService")

local function request(method, url, body)
    local options = {Url = url, Method = method, Headers = {Accept = "application/json"}}
    if body ~= nil then
        options.Headers["Content-Type"] = "application/json"
        options.Body = HttpService:JSONEncode(body)
    end
    local ok, result = pcall(function() return HttpService:RequestAsync(options) end)
    if not ok then warn("Request failed: " .. tostring(result)); return end
    print("HTTP status:", result.StatusCode)
    local decoded, data = pcall(function() return HttpService:JSONDecode(result.Body) end)
    if decoded then return data end
    return result.Body
end

local result = request("GET", "https://octane.wtf/apisite/users/v1/users/1")
if result then print(result.name) end
```

`RequestAsync` support, HTTPS restrictions and authentication vary by revival. For GET-only environments, `HttpService:GetAsync` can be used instead. For endpoints without a known body or host, inspect your own browser requests first; **do not guess or publish cookies, CSRF tokens, or personal data**. State-changing endpoints can buy items, send messages, or alter accounts; only use them deliberately and with permission.


### Main reference


#### Users and presence · `/users/v1/users/{userId}`

```text
GET https://octane.wtf/apisite/users/v1/users/{userId}
```


#### Users and presence · `/users/v1/users/search?keyword={keyword}&limit=10`

```text
GET https://octane.wtf/apisite/users/v1/users/search?keyword={keyword}&limit=10
```


#### Users and presence · `/friends/v1/users/{userId}/friends`

```text
GET https://octane.wtf/apisite/friends/v1/users/{userId}/friends
```


#### Users and presence · `/presence/v1/presence/users`

```text
POST https://octane.wtf/apisite/presence/v1/presence/users
```

Check the request body, authentication, CSRF requirements, and permissions before use; these details were not established for this template.


#### Additional endpoints observed in a signed-in session · `/accountsettings/v1/themes/user`

**Method not captured.** Confirm it in DevTools before making a request.

```text
<METHOD> https://octane.wtf/apisite/accountsettings/v1/themes/user
```


#### Additional endpoints observed in a signed-in session · `/api/alerts/alert-info`

**Method not captured.** Confirm it in DevTools before making a request.

```text
<METHOD> https://octane.wtf/apisite/api/alerts/alert-info
```


#### Additional endpoints observed in a signed-in session · `/friends/v1/user/following-exists`

**Method not captured.** Confirm it in DevTools before making a request.

```text
<METHOD> https://octane.wtf/apisite/friends/v1/user/following-exists
```


#### Additional endpoints observed in a signed-in session · `/friends/v1/users/{userId}/followers/count`

**Method not captured.** Confirm it in DevTools before making a request.

```text
<METHOD> https://octane.wtf/apisite/friends/v1/users/{userId}/followers/count
```


#### Additional endpoints observed in a signed-in session · `/friends/v1/users/{userId}/followings/count`

**Method not captured.** Confirm it in DevTools before making a request.

```text
<METHOD> https://octane.wtf/apisite/friends/v1/users/{userId}/followings/count
```


#### Additional endpoints observed in a signed-in session · `/friends/v1/users/{userId}/friends/statuses`

**Method not captured.** Confirm it in DevTools before making a request.

```text
<METHOD> https://octane.wtf/apisite/friends/v1/users/{userId}/friends/statuses
```


#### Additional endpoints observed in a signed-in session · `/groups/v1/users/{userId}/groups/roles`

**Method not captured.** Confirm it in DevTools before making a request.

```text
<METHOD> https://octane.wtf/apisite/groups/v1/users/{userId}/groups/roles
```


#### Additional endpoints observed in a signed-in session · `/users/v1/users/{userId}/status`

**Method not captured.** Confirm it in DevTools before making a request.

```text
<METHOD> https://octane.wtf/apisite/users/v1/users/{userId}/status
```


#### Additional endpoints observed in a signed-in session · `/users/v1/users/{userId}/username-history`

**Method not captured.** Confirm it in DevTools before making a request.

```text
<METHOD> https://octane.wtf/apisite/users/v1/users/{userId}/username-history
```


#### Additional endpoints observed in a signed-in session · `/friends/v1/user/friend-requests/count`

**Method not captured.** Confirm it in DevTools before making a request.

```text
<METHOD> https://octane.wtf/apisite/friends/v1/user/friend-requests/count
```


#### Additional endpoints observed in a signed-in session · `/privatemessages/v1/messages/unread/count`

**Method not captured.** Confirm it in DevTools before making a request.

```text
<METHOD> https://octane.wtf/apisite/privatemessages/v1/messages/unread/count
```


#### Additional endpoints observed in a signed-in session · `/trades/v1/trades/inbound/count`

**Method not captured.** Confirm it in DevTools before making a request.

```text
<METHOD> https://octane.wtf/apisite/trades/v1/trades/inbound/count
```


#### Inventory and catalog · `/inventory/v1/users/{userId}/assets/collectibles?limit=100&sortOrder=Desc`

```text
GET https://octane.wtf/apisite/inventory/v1/users/{userId}/assets/collectibles?limit=100&sortOrder=Desc
```


#### Inventory and catalog · `/inventory/v2/users/{userId}/inventory?assetTypes={types}&limit=100&sortOrder=Desc`

```text
GET https://octane.wtf/apisite/inventory/v2/users/{userId}/inventory?assetTypes={types}&limit=100&sortOrder=Desc
```


#### Inventory and catalog · `/inventory/v1/users/{userId}/items/Asset/{assetId}`

```text
GET https://octane.wtf/apisite/inventory/v1/users/{userId}/items/Asset/{assetId}
```


#### Inventory and catalog · `/catalog/v1/search/items?{query}`

```text
GET https://octane.wtf/apisite/catalog/v1/search/items?{query}
```


#### Inventory and catalog · `/catalog/v1/catalog/items/details`

```text
POST https://octane.wtf/apisite/catalog/v1/catalog/items/details
```

Check the request body, authentication, CSRF requirements, and permissions before use; these details were not established for this template.


#### Avatars and thumbnails · `/avatar/v1/avatar-rules`

```text
GET https://octane.wtf/apisite/avatar/v1/avatar-rules
```


#### Avatars and thumbnails · `/avatar/v1/users/{userId}/avatar`

```text
GET https://octane.wtf/apisite/avatar/v1/users/{userId}/avatar
```


#### Avatars and thumbnails · `/thumbnails/v1/users/avatar-headshot?userIds={ids}&size=48x48&format=png`

```text
GET https://octane.wtf/apisite/thumbnails/v1/users/avatar-headshot?userIds={ids}&size=48x48&format=png
```


#### Avatars and thumbnails · `/thumbnails/v1/avatar/try-on?assetId={assetId}&userId={userId}&size=420x420&format=Png`

```text
GET https://octane.wtf/apisite/thumbnails/v1/avatar/try-on?assetId={assetId}&userId={userId}&size=420x420&format=Png
```


#### Avatars and thumbnails · `/thumbnails/v1/games/icons?universeIds={ids}&size=150x150&format=Png`

```text
GET https://octane.wtf/apisite/thumbnails/v1/games/icons?universeIds={ids}&size=150x150&format=Png
```


#### Groups · `/groups/v2/users/{userId}/groups/roles`

```text
GET https://octane.wtf/apisite/groups/v2/users/{userId}/groups/roles
```


#### Groups · `/groups/v1/groups/{groupId}`

```text
GET https://octane.wtf/apisite/groups/v1/groups/{groupId}
```


#### Groups · `/groups/v1/groups/{groupId}/roles`

```text
GET https://octane.wtf/apisite/groups/v1/groups/{groupId}/roles
```


#### Groups · `/groups/v1/groups/{groupId}/users?limit=100&sortOrder=Desc`

```text
GET https://octane.wtf/apisite/groups/v1/groups/{groupId}/users?limit=100&sortOrder=Desc
```


#### Groups · `/groups/v1/groups/{groupId}/audit-log?limit=100&sortOrder=Desc`

```text
GET https://octane.wtf/apisite/groups/v1/groups/{groupId}/audit-log?limit=100&sortOrder=Desc
```


#### Groups · `/groups/v1/groups/{groupId}/users/{userId}`

```text
DELETE https://octane.wtf/apisite/groups/v1/groups/{groupId}/users/{userId}
```

Check the request body, authentication, CSRF requirements, and permissions before use; these details were not established for this template.


#### Games, economy and trades · `/games/v1/games?universeIds={ids}`

```text
GET https://octane.wtf/apisite/games/v1/games?universeIds={ids}
```


#### Games, economy and trades · `/economy/v1/assets/{assetId}/resellers?limit=25`

```text
GET https://octane.wtf/apisite/economy/v1/assets/{assetId}/resellers?limit=25
```


#### Games, economy and trades · `/economy/v1/users/{userId}/currency`

```text
GET https://octane.wtf/apisite/economy/v1/users/{userId}/currency
```


#### Games, economy and trades · `/trades/v1/trades/{type}?limit={limit}&sortOrder=Desc`

```text
GET https://octane.wtf/apisite/trades/v1/trades/{type}?limit={limit}&sortOrder=Desc
```


#### Games, economy and trades · `/trades/v1/trades/{tradeId}`

```text
GET https://octane.wtf/apisite/trades/v1/trades/{tradeId}
```


#### Other Octane paths · `/account/settings/countries`

```text
GET https://octane.wtf/account/settings/countries
```


#### Other Octane paths · `/users/{userId}`

```text
GET https://octane.wtf/users/{userId}
```


#### Other Octane paths · `/users/favorites/list-json?assetTypeId=9&itemsPerPage=24&pageNumber=1&userId={userId}`

```text
GET https://octane.wtf/users/favorites/list-json?assetTypeId=9&itemsPerPage=24&pageNumber=1&userId={userId}
```


#### Additional endpoints found in Nexus · `https://octane.wtf/apisite/economy/v2/assets/{assetId}/details`

```text
GET https://octane.wtf/apisite/economy/v2/assets/{assetId}/details
```


#### Additional endpoints found in Nexus · `https://octane.wtf/apisite/games/v1/games/list?keyword={keyword}&maxRows=25`

```text
GET https://octane.wtf/apisite/games/v1/games/list?keyword={keyword}&maxRows=25
```


#### Additional endpoints found in Nexus · `https://octane.wtf/apisite/games/v1/games/multiget-playability-status?universeIds={ids}`

```text
GET https://octane.wtf/apisite/games/v1/games/multiget-playability-status?universeIds={ids}
```


#### Additional endpoints found in Nexus · `https://octane.wtf/apisite/thumbnails/v1/assets?assetIds={ids}&size=150x150&format=Png`

```text
GET https://octane.wtf/apisite/thumbnails/v1/assets?assetIds={ids}&size=150x150&format=Png
```


#### Additional endpoints found in Nexus · `https://inventory.octane.wtf/v1/users/{userId}/assets/collectibles?sortOrder=Desc&limit=100`

```text
GET https://inventory.octane.wtf/v1/users/{userId}/assets/collectibles?sortOrder=Desc&limit=100
```


#### Additional endpoints found in Nexus · `https://inventory.octane.wtf/v2/users/{userId}/inventory?assetTypes={types}`

```text
GET https://inventory.octane.wtf/v2/users/{userId}/inventory?assetTypes={types}
```


#### Additional endpoints found in Nexus · `https://octane.wtf/apisite/friends/v1/users/{userId}/unfriend`

```text
POST https://octane.wtf/apisite/friends/v1/users/{userId}/unfriend
```

Check the request body, authentication, CSRF requirements, and permissions before use; these details were not established for this template.


#### Additional service hosts · `/v1/users/{userId}/friends`

**Method not captured.** Confirm it in DevTools before making a request.

```text
<METHOD> https://friends.octane.wtf/v1/users/{userId}/friends
```


#### Additional service hosts · `/v1/games/list`

**Method not captured.** Confirm it in DevTools before making a request.

```text
<METHOD> https://games.octane.wtf/v1/games/list
```


#### Additional service hosts · `/v1/games/multiget-place-details`

**Method not captured.** Confirm it in DevTools before making a request.

```text
<METHOD> https://games.octane.wtf/v1/games/multiget-place-details
```


#### Additional service hosts · `/v1/games/sorts`

**Method not captured.** Confirm it in DevTools before making a request.

```text
<METHOD> https://games.octane.wtf/v1/games/sorts
```


#### Additional service hosts · `/v1/performance/send-measurement`

**Method not captured.** Confirm it in DevTools before making a request.

```text
<METHOD> https://metrics.octane.wtf/v1/performance/send-measurement
```


#### Additional service hosts · `/v1/thumbnails/metadata`

**Method not captured.** Confirm it in DevTools before making a request.

```text
<METHOD> https://metrics.octane.wtf/v1/thumbnails/metadata
```


#### Additional service hosts · `/v2/notifications/get-settings`

**Method not captured.** Confirm it in DevTools before making a request.

```text
<METHOD> https://notifications.octane.wtf/v2/notifications/get-settings
```


#### Additional service hosts · `/v1/presence/register-app-presence`

**Method not captured.** Confirm it in DevTools before making a request.

```text
<METHOD> https://presence.octane.wtf/v1/presence/register-app-presence
```


#### Additional service hosts · `/v1/presence/users`

**Method not captured.** Confirm it in DevTools before making a request.

```text
<METHOD> https://presence.octane.wtf/v1/presence/users
```


#### Additional service hosts · `/v1/announcements/metadata`

**Method not captured.** Confirm it in DevTools before making a request.

```text
<METHOD> https://privatemessages.octane.wtf/v1/announcements/metadata
```


#### Additional service hosts · `/v1/messages`

**Method not captured.** Confirm it in DevTools before making a request.

```text
<METHOD> https://privatemessages.octane.wtf/v1/messages
```


#### Additional service hosts · `/notifications/negotiate`

**Method not captured.** Confirm it in DevTools before making a request.

```text
<METHOD> https://realtime.octane.wtf/notifications/negotiate
```


#### Additional service hosts · `/notifications/start`

**Method not captured.** Confirm it in DevTools before making a request.

```text
<METHOD> https://realtime.octane.wtf/notifications/start
```


#### Additional service hosts · `/v1/games/icons`

**Method not captured.** Confirm it in DevTools before making a request.

```text
<METHOD> https://thumbnails.octane.wtf/v1/games/icons
```


#### Additional service hosts · `/v1/users/avatar-headshot`

**Method not captured.** Confirm it in DevTools before making a request.

```text
<METHOD> https://thumbnails.octane.wtf/v1/users/avatar-headshot
```


#### Additional service hosts · `/v1/trades/inbound`

**Method not captured.** Confirm it in DevTools before making a request.

```text
<METHOD> https://trades.octane.wtf/v1/trades/inbound
```


#### Inventory service on a separate host · `https://inventory.octane.wtf/v2/assets/{assetId}/owners`

```text
GET https://inventory.octane.wtf/v2/assets/{assetId}/owners
```


#### Additional account and privacy paths · `/account/settings/account-country`

**Method not captured.** Confirm it in DevTools before making a request.

```text
<METHOD> https://octane.wtf/account/settings/account-country
```


#### Additional account and privacy paths · `/account/settings/account-restrictions`

**Method not captured.** Confirm it in DevTools before making a request.

```text
<METHOD> https://octane.wtf/account/settings/account-restrictions
```


#### Additional account and privacy paths · `/account/settings/app-chat-privacy`

**Method not captured.** Confirm it in DevTools before making a request.

```text
<METHOD> https://octane.wtf/account/settings/app-chat-privacy
```


#### Additional account and privacy paths · `/account/settings/follow-me-privacy`

**Method not captured.** Confirm it in DevTools before making a request.

```text
<METHOD> https://octane.wtf/account/settings/follow-me-privacy
```


#### Additional account and privacy paths · `/account/settings/game-chat-privacy`

**Method not captured.** Confirm it in DevTools before making a request.

```text
<METHOD> https://octane.wtf/account/settings/game-chat-privacy
```


#### Additional account and privacy paths · `/account/settings/private-message-privacy`

**Method not captured.** Confirm it in DevTools before making a request.

```text
<METHOD> https://octane.wtf/account/settings/private-message-privacy
```


#### Additional account and privacy paths · `/account/settings/private-server-invite-privacy`

**Method not captured.** Confirm it in DevTools before making a request.

```text
<METHOD> https://octane.wtf/account/settings/private-server-invite-privacy
```


#### Additional account and privacy paths · `/account/settings/settings-groups`

**Method not captured.** Confirm it in DevTools before making a request.

```text
<METHOD> https://octane.wtf/account/settings/settings-groups
```


#### Additional account and privacy paths · `/my/settings/json`

**Method not captured.** Confirm it in DevTools before making a request.

```text
<METHOD> https://octane.wtf/my/settings/json
```


#### Additional account and privacy paths · `/user-sponsorship/2`

**Method not captured.** Confirm it in DevTools before making a request.

```text
<METHOD> https://octane.wtf/user-sponsorship/2
```


#### Additional account and privacy paths · `/usercheck/show-tos`

**Method not captured.** Confirm it in DevTools before making a request.

```text
<METHOD> https://octane.wtf/usercheck/show-tos
```


#### Additional account and privacy paths · `/v1/inventory-privacy`

**Method not captured.** Confirm it in DevTools before making a request.

```text
<METHOD> https://octane.wtf/v1/inventory-privacy
```


#### Additional account and privacy paths · `/v1/privacy`

**Method not captured.** Confirm it in DevTools before making a request.

```text
<METHOD> https://octane.wtf/v1/privacy
```


#### Additional account and privacy paths · `/v1/privacy/info`

**Method not captured.** Confirm it in DevTools before making a request.

```text
<METHOD> https://octane.wtf/v1/privacy/info
```


#### Additional account and privacy paths · `/v1/social/connected-providers`

**Method not captured.** Confirm it in DevTools before making a request.

```text
<METHOD> https://octane.wtf/v1/social/connected-providers
```


#### Additional account and privacy paths · `/v1/themes/types`

**Method not captured.** Confirm it in DevTools before making a request.

```text
<METHOD> https://octane.wtf/v1/themes/types
```


#### Additional account and privacy paths · `/v1/themes/user`

**Method not captured.** Confirm it in DevTools before making a request.

```text
<METHOD> https://octane.wtf/v1/themes/user
```


#### Additional account and privacy paths · `/v1/trade-privacy`

**Method not captured.** Confirm it in DevTools before making a request.

```text
<METHOD> https://octane.wtf/v1/trade-privacy
```


#### Additional account and privacy paths · `/v1/trade-value`

**Method not captured.** Confirm it in DevTools before making a request.

```text
<METHOD> https://octane.wtf/v1/trade-value
```


#### Additional account and privacy paths · `/v1/xbox/connection`

**Method not captured.** Confirm it in DevTools before making a request.

```text
<METHOD> https://octane.wtf/v1/xbox/connection
```


#### Additional account and privacy paths · `/v2/notifications/get-settings`

**Method not captured.** Confirm it in DevTools before making a request.

```text
<METHOD> https://octane.wtf/v2/notifications/get-settings
```


#### Octomon's API · `/api/items/v3/itemdetails`

```text
GET https://octomons.wtf/api/items/v3/itemdetails
```


#### Octomon's API · `/api/itemthumbs/v2/thumbssm`

```text
GET https://octomons.wtf/api/itemthumbs/v2/thumbssm
```


#### Octomon's API · `/api/market/v2/dealactivity`

```text
GET https://octomons.wtf/api/market/v2/dealactivity
```


#### Octomon's API · `/api/market/v2/saleactivity`

```text
GET https://octomons.wtf/api/market/v2/saleactivity
```


#### Octomon's API · `/api/tradeads/v1/getrecentads`

```text
GET https://octomons.wtf/api/tradeads/v1/getrecentads
```


#### Octomon's API · `/api/players/v1/playersearch?searchstring={query}`

```text
GET https://octomons.wtf/api/players/v1/playersearch?searchstring={query}
```


#### Octomon's API · `/api/players/v1/playerassets/{userId}`

```text
GET https://octomons.wtf/api/players/v1/playerassets/{userId}
```


#### Octomon's API · `/thumbs/avatar?userIds={ids}&size=150x150`

```text
GET https://octomons.wtf/thumbs/avatar?userIds={ids}&size=150x150
```


#### Octomon's API · `/thumbs/avatarbust?userIds={ids}&size=150x150`

```text
GET https://octomons.wtf/thumbs/avatarbust?userIds={ids}&size=150x150
```


#### Octomon's API · `/api/games/v1/gamelist`

```text
GET https://octomons.wtf/api/games/v1/gamelist
```


#### Octomon's API · `/api/groups/v1/groupsearch?searchstring={query}`

```text
GET https://octomons.wtf/api/groups/v1/groupsearch?searchstring={query}
```


#### Additional state-changing endpoints · `/api/players/v1/addplayerbyname`

```text
POST https://octomons.wtf/api/players/v1/addplayerbyname
```

Check the request body, authentication, CSRF requirements, and permissions before use; these details were not established for this template.


#### Additional state-changing endpoints · `/api/tradeads/v1/createad`

```text
POST https://octomons.wtf/api/tradeads/v1/createad
```

Check the request body, authentication, CSRF requirements, and permissions before use; these details were not established for this template.


#### Additional state-changing endpoints · `/api/tradeads/v1/removead/{adId}`

```text
POST https://octomons.wtf/api/tradeads/v1/removead/{adId}
```

Check the request body, authentication, CSRF requirements, and permissions before use; these details were not established for this template.


*99 endpoint entries in this section, including separate host variants.*


### Community reference


#### Account information and promotion channels · `/v1/description`

**Host must be established from the original request.** Do not treat the placeholder as a URL.

```text
GET <CONFIRM_HOST>/v1/description
```

```text
POST <CONFIRM_HOST>/v1/description
```

Check the request body, authentication, CSRF requirements, and permissions before use; these details were not established for this template.


#### Account information and promotion channels · `/v1/birthdate`

**Host must be established from the original request.** Do not treat the placeholder as a URL.

```text
GET <CONFIRM_HOST>/v1/birthdate
```


#### Account information and promotion channels · `/v1/gender`

**Host must be established from the original request.** Do not treat the placeholder as a URL.

```text
GET <CONFIRM_HOST>/v1/gender
```

```text
POST <CONFIRM_HOST>/v1/gender
```

Check the request body, authentication, CSRF requirements, and permissions before use; these details were not established for this template.


#### Account information and promotion channels · `/v1/metadata`

**Host must be established from the original request.** Do not treat the placeholder as a URL.

```text
GET <CONFIRM_HOST>/v1/metadata
```


#### Account information and promotion channels · `/v1/phone`

**Host must be established from the original request.** Do not treat the placeholder as a URL.

```text
GET <CONFIRM_HOST>/v1/phone
```


#### Account information and promotion channels · `/v1/promotion-channels`

**Host must be established from the original request.** Do not treat the placeholder as a URL.

```text
GET <CONFIRM_HOST>/v1/promotion-channels
```

```text
POST <CONFIRM_HOST>/v1/promotion-channels
```

Check the request body, authentication, CSRF requirements, and permissions before use; these details were not established for this template.


#### Account information and promotion channels · `/v1/users/{userId}/promotion-channels`

**Host must be established from the original request.** Do not treat the placeholder as a URL.

```text
GET <CONFIRM_HOST>/v1/users/{userId}/promotion-channels
```


#### Avatar · `/v1/avatar`

**Host must be established from the original request.** Do not treat the placeholder as a URL.

```text
GET <CONFIRM_HOST>/v1/avatar
```


#### Avatar · `/v1/avatar-rules`

**Host must be established from the original request.** Do not treat the placeholder as a URL.

```text
GET <CONFIRM_HOST>/v1/avatar-rules
```


#### Avatar · `/v1/avatar/metadata`

**Host must be established from the original request.** Do not treat the placeholder as a URL.

```text
GET <CONFIRM_HOST>/v1/avatar/metadata
```


#### Avatar · `/v1/recent-items/all/list`

**Host must be established from the original request.** Do not treat the placeholder as a URL.

```text
GET <CONFIRM_HOST>/v1/recent-items/all/list
```


#### Avatar · `/v1/users/{userId}/avatar`

**Host must be established from the original request.** Do not treat the placeholder as a URL.

```text
GET <CONFIRM_HOST>/v1/users/{userId}/avatar
```


#### Avatar · `/v1/avatar/assets/{assetId}/wear`

**Host must be established from the original request.** Do not treat the placeholder as a URL.

```text
POST <CONFIRM_HOST>/v1/avatar/assets/{assetId}/wear
```

Check the request body, authentication, CSRF requirements, and permissions before use; these details were not established for this template.


#### Avatar · `/v1/avatar/set-wearing-assets`

**Host must be established from the original request.** Do not treat the placeholder as a URL.

```text
POST <CONFIRM_HOST>/v1/avatar/set-wearing-assets
```

Check the request body, authentication, CSRF requirements, and permissions before use; these details were not established for this template.


#### Avatar · `/v1/avatar/redraw-thumbnail`

**Host must be established from the original request.** Do not treat the placeholder as a URL.

```text
POST <CONFIRM_HOST>/v1/avatar/redraw-thumbnail
```

Check the request body, authentication, CSRF requirements, and permissions before use; these details were not established for this template.


#### Badges · `/v1/universes/{universeId}/badges`

**Host must be established from the original request.** Do not treat the placeholder as a URL.

```text
GET <CONFIRM_HOST>/v1/universes/{universeId}/badges
```


#### Badges · `/v1/user/badges/{badgeId}`

**Host must be established from the original request.** Do not treat the placeholder as a URL.

```text
DELETE <CONFIRM_HOST>/v1/user/badges/{badgeId}
```

Check the request body, authentication, CSRF requirements, and permissions before use; these details were not established for this template.


#### Catalog · `/v1/catalog/metadata`

**Host must be established from the original request.** Do not treat the placeholder as a URL.

```text
GET <CONFIRM_HOST>/v1/catalog/metadata
```


#### Catalog · `/v1/categories`

**Host must be established from the original request.** Do not treat the placeholder as a URL.

```text
GET <CONFIRM_HOST>/v1/categories
```


#### Catalog · `/v1/subcategories`

**Host must be established from the original request.** Do not treat the placeholder as a URL.

```text
GET <CONFIRM_HOST>/v1/subcategories
```


#### Catalog · `/v1/search/navigation-menu-items`

**Host must be established from the original request.** Do not treat the placeholder as a URL.

```text
GET <CONFIRM_HOST>/v1/search/navigation-menu-items
```


#### Catalog · `/v1/search/items`

**Host must be established from the original request.** Do not treat the placeholder as a URL.

```text
GET <CONFIRM_HOST>/v1/search/items
```


#### Catalog · `/v1/recommendations/metadata`

**Host must be established from the original request.** Do not treat the placeholder as a URL.

```text
GET <CONFIRM_HOST>/v1/recommendations/metadata
```


#### Catalog · `/v1/catalog/items/details`

**Host must be established from the original request.** Do not treat the placeholder as a URL.

```text
POST <CONFIRM_HOST>/v1/catalog/items/details
```

Check the request body, authentication, CSRF requirements, and permissions before use; these details were not established for this template.


#### Chat and contacts · `/v2/get-messages`

**Host must be established from the original request.** Do not treat the placeholder as a URL.

```text
GET <CONFIRM_HOST>/v2/get-messages
```


#### Chat and contacts · `/v2/send-message`

**Host must be established from the original request.** Do not treat the placeholder as a URL.

```text
POST <CONFIRM_HOST>/v2/send-message
```

Check the request body, authentication, CSRF requirements, and permissions before use; these details were not established for this template.


#### Chat and contacts · `/v2/update-user-typing-status`

**Host must be established from the original request.** Do not treat the placeholder as a URL.

```text
POST <CONFIRM_HOST>/v2/update-user-typing-status
```

Check the request body, authentication, CSRF requirements, and permissions before use; these details were not established for this template.


#### Chat and contacts · `/v2/metadata`

**Host must be established from the original request.** Do not treat the placeholder as a URL.

```text
GET <CONFIRM_HOST>/v2/metadata
```


#### Chat and contacts · `/v2/get-unread-conversation-count`

**Host must be established from the original request.** Do not treat the placeholder as a URL.

```text
GET <CONFIRM_HOST>/v2/get-unread-conversation-count
```


#### Chat and contacts · `/v2/get-user-conversations`

**Host must be established from the original request.** Do not treat the placeholder as a URL.

```text
GET <CONFIRM_HOST>/v2/get-user-conversations
```


#### Chat and contacts · `/v2/multi-get-latest-messages`

**Host must be established from the original request.** Do not treat the placeholder as a URL.

```text
GET <CONFIRM_HOST>/v2/multi-get-latest-messages
```


#### Chat and contacts · `/v1/user/get-tags`

**Host must be established from the original request.** Do not treat the placeholder as a URL.

```text
POST <CONFIRM_HOST>/v1/user/get-tags
```

Check the request body, authentication, CSRF requirements, and permissions before use; these details were not established for this template.


#### Chat and contacts · `/v1/contacts/metadata`

**Host must be established from the original request.** Do not treat the placeholder as a URL.

```text
GET <CONFIRM_HOST>/v1/contacts/metadata
```


#### Games · `/v1/games`

**Host must be established from the original request.** Do not treat the placeholder as a URL.

```text
GET <CONFIRM_HOST>/v1/games
```


#### Games · `/v1/games/list`

**Host must be established from the original request.** Do not treat the placeholder as a URL.

```text
GET <CONFIRM_HOST>/v1/games/list
```


#### Games · `/v1/games/multiget-place-details`

**Host must be established from the original request.** Do not treat the placeholder as a URL.

```text
GET <CONFIRM_HOST>/v1/games/multiget-place-details
```


#### Games · `/v1/games/multiget-playability-status`

**Host must be established from the original request.** Do not treat the placeholder as a URL.

```text
GET <CONFIRM_HOST>/v1/games/multiget-playability-status
```


#### Games · `/v1/games/sorts`

**Host must be established from the original request.** Do not treat the placeholder as a URL.

```text
GET <CONFIRM_HOST>/v1/games/sorts
```


#### Games · `/v1/games/votes`

**Host must be established from the original request.** Do not treat the placeholder as a URL.

```text
GET <CONFIRM_HOST>/v1/games/votes
```


#### Games · `/v1/games/{universeId}/favorites`

**Host must be established from the original request.** Do not treat the placeholder as a URL.

```text
GET <CONFIRM_HOST>/v1/games/{universeId}/favorites
```


#### Games · `/v1/games/{universeId}/social-links/list`

**Host must be established from the original request.** Do not treat the placeholder as a URL.

```text
GET <CONFIRM_HOST>/v1/games/{universeId}/social-links/list
```


#### Games · `/v2/users/{userId}/games`

**Host must be established from the original request.** Do not treat the placeholder as a URL.

```text
GET <CONFIRM_HOST>/v2/users/{userId}/games
```


#### Games · `/v2/groups/{groupId}/games`

**Host must be established from the original request.** Do not treat the placeholder as a URL.

```text
GET <CONFIRM_HOST>/v2/groups/{groupId}/games
```


#### Games · `/v2/games/{universeId}/media`

**Host must be established from the original request.** Do not treat the placeholder as a URL.

```text
GET <CONFIRM_HOST>/v2/games/{universeId}/media
```


#### Groups · `/v1/groups/{groupId}`

**Host must be established from the original request.** Do not treat the placeholder as a URL.

```text
GET <CONFIRM_HOST>/v1/groups/{groupId}
```


#### Groups · `/v1/groups/{groupId}/membership`

**Host must be established from the original request.** Do not treat the placeholder as a URL.

```text
GET <CONFIRM_HOST>/v1/groups/{groupId}/membership
```


#### Groups · `/v1/groups/{groupId}/roles`

**Host must be established from the original request.** Do not treat the placeholder as a URL.

```text
GET <CONFIRM_HOST>/v1/groups/{groupId}/roles
```


#### Groups · `/v1/groups/{groupId}/roles/{roleId}/users`

**Host must be established from the original request.** Do not treat the placeholder as a URL.

```text
GET <CONFIRM_HOST>/v1/groups/{groupId}/roles/{roleId}/users
```


#### Groups · `/v1/groups/{groupId}/social-links`

**Host must be established from the original request.** Do not treat the placeholder as a URL.

```text
GET <CONFIRM_HOST>/v1/groups/{groupId}/social-links
```


#### Groups · `/v1/groups/{groupId}/payouts`

**Host must be established from the original request.** Do not treat the placeholder as a URL.

```text
GET <CONFIRM_HOST>/v1/groups/{groupId}/payouts
```


#### Groups · `/v1/groups/metadata`

**Host must be established from the original request.** Do not treat the placeholder as a URL.

```text
GET <CONFIRM_HOST>/v1/groups/metadata
```


#### Groups · `/v1/groups/search`

**Host must be established from the original request.** Do not treat the placeholder as a URL.

```text
GET <CONFIRM_HOST>/v1/groups/search
```


#### Groups · `/v1/users/{userId}/groups/primary/role`

**Host must be established from the original request.** Do not treat the placeholder as a URL.

```text
GET <CONFIRM_HOST>/v1/users/{userId}/groups/primary/role
```


#### Groups · `/v1/users/{userId}/groups/roles`

**Host must be established from the original request.** Do not treat the placeholder as a URL.

```text
GET <CONFIRM_HOST>/v1/users/{userId}/groups/roles
```


#### Groups · `/v2/groups/{groupId}/wall/posts`

**Host must be established from the original request.** Do not treat the placeholder as a URL.

```text
GET <CONFIRM_HOST>/v2/groups/{groupId}/wall/posts
```


#### Friends, inventory, economy · `/v1/users/{userId}/friends`

**Host must be established from the original request.** Do not treat the placeholder as a URL.

```text
GET <CONFIRM_HOST>/v1/users/{userId}/friends
```


#### Friends, inventory, economy · `/v1/users/{userId}/unfriend`

**Host must be established from the original request.** Do not treat the placeholder as a URL.

```text
POST <CONFIRM_HOST>/v1/users/{userId}/unfriend
```

Check the request body, authentication, CSRF requirements, and permissions before use; these details were not established for this template.


#### Friends, inventory, economy · `/v1/users/{userId}/friends/statuses`

**Host must be established from the original request.** Do not treat the placeholder as a URL.

```text
GET <CONFIRM_HOST>/v1/users/{userId}/friends/statuses
```


#### Friends, inventory, economy · `/v1/users/{userId}/assets/collectibles`

**Host must be established from the original request.** Do not treat the placeholder as a URL.

```text
GET <CONFIRM_HOST>/v1/users/{userId}/assets/collectibles
```


#### Friends, inventory, economy · `/v2/recommendations/{userId}`

**Host must be established from the original request.** Do not treat the placeholder as a URL.

```text
GET <CONFIRM_HOST>/v2/recommendations/{userId}
```


#### Friends, inventory, economy · `/v1/users/{userId}/currency`

**Host must be established from the original request.** Do not treat the placeholder as a URL.

```text
GET <CONFIRM_HOST>/v1/users/{userId}/currency
```


#### Friends, inventory, economy · `/v1/users/{userId}/revenue/summary/day`

**Host must be established from the original request.** Do not treat the placeholder as a URL.

```text
GET <CONFIRM_HOST>/v1/users/{userId}/revenue/summary/day
```


#### Locale, metrics, presence, notifications · `/v1/locales/user-localization-locus-supported-locales`

**Host must be established from the original request.** Do not treat the placeholder as a URL.

```text
GET <CONFIRM_HOST>/v1/locales/user-localization-locus-supported-locales
```


#### Locale, metrics, presence, notifications · `/v1/locales`

**Host must be established from the original request.** Do not treat the placeholder as a URL.

```text
GET <CONFIRM_HOST>/v1/locales
```


#### Locale, metrics, presence, notifications · `/v1/thumbnails/metadata`

**Host must be established from the original request.** Do not treat the placeholder as a URL.

```text
GET <CONFIRM_HOST>/v1/thumbnails/metadata
```


#### Locale, metrics, presence, notifications · `/v1/performance/send-measurement`

**Host must be established from the original request.** Do not treat the placeholder as a URL.

```text
POST <CONFIRM_HOST>/v1/performance/send-measurement
```

Check the request body, authentication, CSRF requirements, and permissions before use; these details were not established for this template.


#### Locale, metrics, presence, notifications · `/v2/notifications/get-settings`

**Host must be established from the original request.** Do not treat the placeholder as a URL.

```text
GET <CONFIRM_HOST>/v2/notifications/get-settings
```


#### Locale, metrics, presence, notifications · `/notifications/negotiate`

**Host must be established from the original request.** Do not treat the placeholder as a URL.

```text
GET <CONFIRM_HOST>/notifications/negotiate
```


#### Locale, metrics, presence, notifications · `/notifications/start`

**Host must be established from the original request.** Do not treat the placeholder as a URL.

```text
GET <CONFIRM_HOST>/notifications/start
```


#### Locale, metrics, presence, notifications · `/notifications/abort`

**Host must be established from the original request.** Do not treat the placeholder as a URL.

```text
POST <CONFIRM_HOST>/notifications/abort
```

Check the request body, authentication, CSRF requirements, and permissions before use; these details were not established for this template.


#### Locale, metrics, presence, notifications · `/v1/presence/users`

**Host must be established from the original request.** Do not treat the placeholder as a URL.

```text
POST <CONFIRM_HOST>/v1/presence/users
```

Check the request body, authentication, CSRF requirements, and permissions before use; these details were not established for this template.


#### Locale, metrics, presence, notifications · `/v1/presence/register-app-presence`

**Host must be established from the original request.** Do not treat the placeholder as a URL.

```text
POST <CONFIRM_HOST>/v1/presence/register-app-presence
```

Check the request body, authentication, CSRF requirements, and permissions before use; these details were not established for this template.


#### Thumbnails · `/v1/assets`

**Host must be established from the original request.** Do not treat the placeholder as a URL.

```text
GET <CONFIRM_HOST>/v1/assets
```


#### Thumbnails · `/v1/badges/icons`

**Host must be established from the original request.** Do not treat the placeholder as a URL.

```text
GET <CONFIRM_HOST>/v1/badges/icons
```


#### Thumbnails · `/v1/games/icons`

**Host must be established from the original request.** Do not treat the placeholder as a URL.

```text
GET <CONFIRM_HOST>/v1/games/icons
```


#### Thumbnails · `/v1/games/multiget/thumbnail`

**Host must be established from the original request.** Do not treat the placeholder as a URL.

```text
GET <CONFIRM_HOST>/v1/games/multiget/thumbnail
```


#### Thumbnails · `/v1/groups/icons`

**Host must be established from the original request.** Do not treat the placeholder as a URL.

```text
GET <CONFIRM_HOST>/v1/groups/icons
```


#### Thumbnails · `/v1/places/gameicons`

**Host must be established from the original request.** Do not treat the placeholder as a URL.

```text
GET <CONFIRM_HOST>/v1/places/gameicons
```


#### Thumbnails · `/v1/users/avatar`

**Host must be established from the original request.** Do not treat the placeholder as a URL.

```text
GET <CONFIRM_HOST>/v1/users/avatar
```


#### Thumbnails · `/v1/users/avatar-headshot`

**Host must be established from the original request.** Do not treat the placeholder as a URL.

```text
GET <CONFIRM_HOST>/v1/users/avatar-headshot
```


#### Trades, premium, two-step and users · `/v1/trades/inbound`

**Host must be established from the original request.** Do not treat the placeholder as a URL.

```text
GET <CONFIRM_HOST>/v1/trades/inbound
```


#### Trades, premium, two-step and users · `/v1/trades/outbound`

**Host must be established from the original request.** Do not treat the placeholder as a URL.

```text
GET <CONFIRM_HOST>/v1/trades/outbound
```


#### Trades, premium, two-step and users · `/v1/trades/inactive`

**Host must be established from the original request.** Do not treat the placeholder as a URL.

```text
GET <CONFIRM_HOST>/v1/trades/inactive
```


#### Trades, premium, two-step and users · `/v1/trades/{tradeId}`

**Host must be established from the original request.** Do not treat the placeholder as a URL.

```text
GET <CONFIRM_HOST>/v1/trades/{tradeId}
```


#### Trades, premium, two-step and users · `/v1/trades/send`

**Host must be established from the original request.** Do not treat the placeholder as a URL.

```text
POST <CONFIRM_HOST>/v1/trades/send
```

Check the request body, authentication, CSRF requirements, and permissions before use; these details were not established for this template.


#### Trades, premium, two-step and users · `/v1/trades/{tradeId}/decline`

**Host must be established from the original request.** Do not treat the placeholder as a URL.

```text
POST <CONFIRM_HOST>/v1/trades/{tradeId}/decline
```

Check the request body, authentication, CSRF requirements, and permissions before use; these details were not established for this template.


#### Trades, premium, two-step and users · `/v1/users/{userId}/subscriptions`

**Host must be established from the original request.** Do not treat the placeholder as a URL.

```text
GET <CONFIRM_HOST>/v1/users/{userId}/subscriptions
```


#### Trades, premium, two-step and users · `/v1/users/{userId}/configuration`

**Host must be established from the original request.** Do not treat the placeholder as a URL.

```text
GET <CONFIRM_HOST>/v1/users/{userId}/configuration
```


#### Trades, premium, two-step and users · `/v1/users/{userId}`

**Host must be established from the original request.** Do not treat the placeholder as a URL.

```text
GET <CONFIRM_HOST>/v1/users/{userId}
```


#### Trades, premium, two-step and users · `/v1/users/{userId}/display-names`

**Host must be established from the original request.** Do not treat the placeholder as a URL.

```text
PATCH <CONFIRM_HOST>/v1/users/{userId}/display-names
```

Check the request body, authentication, CSRF requirements, and permissions before use; these details were not established for this template.


#### Trades, premium, two-step and users · `/v1/users`

**Host must be established from the original request.** Do not treat the placeholder as a URL.

```text
POST <CONFIRM_HOST>/v1/users
```

Check the request body, authentication, CSRF requirements, and permissions before use; these details were not established for this template.


#### Explicitly rooted paths from the supplied reference · `/account/settings/account-country`

```text
GET https://octane.wtf/account/settings/account-country
```


#### Explicitly rooted paths from the supplied reference · `/account/settings/account-restrictions`

```text
GET https://octane.wtf/account/settings/account-restrictions
```


#### Explicitly rooted paths from the supplied reference · `/account/settings/app-chat-privacy`

```text
GET https://octane.wtf/account/settings/app-chat-privacy
```


#### Explicitly rooted paths from the supplied reference · `/account/settings/follow-me-privacy`

```text
GET https://octane.wtf/account/settings/follow-me-privacy
```


#### Explicitly rooted paths from the supplied reference · `/account/settings/game-chat-privacy`

```text
GET https://octane.wtf/account/settings/game-chat-privacy
```


#### Explicitly rooted paths from the supplied reference · `/account/settings/private-message-privacy`

```text
GET https://octane.wtf/account/settings/private-message-privacy
```


#### Explicitly rooted paths from the supplied reference · `/account/settings/private-server-invite-privacy`

```text
GET https://octane.wtf/account/settings/private-server-invite-privacy
```


#### Explicitly rooted paths from the supplied reference · `/home/updatestatus`

```text
POST https://octane.wtf/home/updatestatus
```

Check the request body, authentication, CSRF requirements, and permissions before use; these details were not established for this template.


#### Explicitly rooted paths from the supplied reference · `/apisite/economy/v1/purchases/products/{productId}`

```text
POST https://octane.wtf/apisite/economy/v1/purchases/products/{productId}
```

Check the request body, authentication, CSRF requirements, and permissions before use; these details were not established for this template.


#### Explicitly rooted paths from the supplied reference · `/apisite/friends/v1/users/{userId}/followers/count`

```text
GET https://octane.wtf/apisite/friends/v1/users/{userId}/followers/count
```


#### Explicitly rooted paths from the supplied reference · `/apisite/friends/v1/users/{userId}/followings/count`

```text
GET https://octane.wtf/apisite/friends/v1/users/{userId}/followings/count
```


#### Explicitly rooted paths from the supplied reference · `/apisite/friends/v1/user/friend-requests/count`

```text
GET https://octane.wtf/apisite/friends/v1/user/friend-requests/count
```


#### Explicitly rooted paths from the supplied reference · `/apisite/friends/v1/user/following-exists`

```text
POST https://octane.wtf/apisite/friends/v1/user/following-exists
```

Check the request body, authentication, CSRF requirements, and permissions before use; these details were not established for this template.


#### Explicitly rooted paths from the supplied reference · `/apisite/privatemessages/v1/messages/unread/count`

```text
GET https://octane.wtf/apisite/privatemessages/v1/messages/unread/count
```


#### Explicitly rooted paths from the supplied reference · `/apisite/privatemessages/v1/messages/{userId}/can-message`

```text
GET https://octane.wtf/apisite/privatemessages/v1/messages/{userId}/can-message
```


#### Explicitly rooted paths from the supplied reference · `/apisite/trades/v1/trades/inbound/count`

```text
GET https://octane.wtf/apisite/trades/v1/trades/inbound/count
```


#### Explicitly rooted paths from the supplied reference · `/apisite/trades/v1/users/{userId}/can-trade-with`

```text
GET https://octane.wtf/apisite/trades/v1/users/{userId}/can-trade-with
```


#### Explicitly rooted paths from the supplied reference · `/apisite/users/v1/users/authenticated`

```text
GET https://octane.wtf/apisite/users/v1/users/authenticated
```


#### Explicitly rooted paths from the supplied reference · `/apisite/users/v1/users/{userId}/status`

```text
GET https://octane.wtf/apisite/users/v1/users/{userId}/status
```


#### Explicitly rooted paths from the supplied reference · `/apisite/users/v1/users/{userId}/username-history`

```text
GET https://octane.wtf/apisite/users/v1/users/{userId}/username-history
```


#### Private messages — explicit service host · `/v1/announcements/metadata`

```text
GET https://privatemessages.octane.wtf/v1/announcements/metadata
```


#### Private messages — explicit service host · `/v1/messages`

```text
GET https://privatemessages.octane.wtf/v1/messages
```


#### Private messages — explicit service host · `/v1/messages/send`

```text
POST https://privatemessages.octane.wtf/v1/messages/send
```

Check the request body, authentication, CSRF requirements, and permissions before use; these details were not established for this template.


#### Themes and privacy · `/v1/themes/types`

```text
GET https://octane.wtf/v1/themes/types
```


#### Themes and privacy · `/v1/themes/user`

```text
GET https://octane.wtf/v1/themes/user
```

```text
PATCH https://octane.wtf/v1/themes/user
```

Check the request body, authentication, CSRF requirements, and permissions before use; these details were not established for this template.


#### Themes and privacy · `/v1/privacy`

```text
GET https://octane.wtf/v1/privacy
```


#### Themes and privacy · `/v1/privacy/info`

```text
GET https://octane.wtf/v1/privacy/info
```


#### Themes and privacy · `/v1/inventory-privacy`

```text
GET https://octane.wtf/v1/inventory-privacy
```

```text
POST https://octane.wtf/v1/inventory-privacy
```

Check the request body, authentication, CSRF requirements, and permissions before use; these details were not established for this template.


#### Themes and privacy · `/v1/trade-privacy`

```text
GET https://octane.wtf/v1/trade-privacy
```

```text
POST https://octane.wtf/v1/trade-privacy
```

Check the request body, authentication, CSRF requirements, and permissions before use; these details were not established for this template.


#### Themes and privacy · `/v1/trade-value`

```text
GET https://octane.wtf/v1/trade-value
```


#### Themes and privacy · `/v1/social/connected-providers`

```text
GET https://octane.wtf/v1/social/connected-providers
```


#### Themes and privacy · `/v1/xbox/connection`

```text
GET https://octane.wtf/v1/xbox/connection
```


#### Legacy paths — stated root base · `/asset/profile-status`

```text
GET https://octane.wtf/asset/profile-status
```


#### Legacy paths — stated root base · `/avatar-thumbnail-3d/json`

```text
GET https://octane.wtf/avatar-thumbnail-3d/json
```


#### Legacy paths — stated root base · `/build/universes`

```text
GET https://octane.wtf/build/universes
```


#### Legacy paths — stated root base · `/comments/get-json`

```text
GET https://octane.wtf/comments/get-json
```


#### Legacy paths — stated root base · `/game/get-join-script`

```text
GET https://octane.wtf/game/get-join-script
```


#### Legacy paths — stated root base · `/games/getfriendsgameinstances`

```text
GET https://octane.wtf/games/getfriendsgameinstances
```


#### Legacy paths — stated root base · `/games/getgameinstancesjson`

```text
GET https://octane.wtf/games/getgameinstancesjson
```


#### Legacy paths — stated root base · `/games/votingservice/{id}`

```text
GET https://octane.wtf/games/votingservice/{id}
```


#### Legacy paths — stated root base · `/my/settings/json`

```text
GET https://octane.wtf/my/settings/json
```


#### Legacy paths — stated root base · `/private-server/instance-list-json`

```text
GET https://octane.wtf/private-server/instance-list-json
```


#### Legacy paths — stated root base · `/recipe?iteration`

```text
GET https://octane.wtf/recipe?iteration
```


#### Legacy paths — stated root base · `/referral/v1/me`

```text
GET https://octane.wtf/referral/v1/me
```


#### Legacy paths — stated root base · `/search/users/metadata`

```text
GET https://octane.wtf/search/users/metadata
```


#### Legacy paths — stated root base · `/search/users/results`

```text
GET https://octane.wtf/search/users/results
```


#### Legacy paths — stated root base · `/usercheck/show-tos`

```text
GET https://octane.wtf/usercheck/show-tos
```


#### Legacy paths — stated root base · `/users/favorites/list-json`

```text
GET https://octane.wtf/users/favorites/list-json
```


#### Legacy paths — stated root base · `/users/inventory/list-json`

```text
GET https://octane.wtf/users/inventory/list-json
```


#### Legacy paths — stated root base · `/users/profile/playerassets-json`

```text
GET https://octane.wtf/users/profile/playerassets-json
```


#### Legacy paths — stated root base · `/users/profile/robloxcollections-json`

```text
GET https://octane.wtf/users/profile/robloxcollections-json
```


#### Legacy paths — stated root base · `/abuse-report/v1/report`

```text
POST https://octane.wtf/abuse-report/v1/report
```

Check the request body, authentication, CSRF requirements, and permissions before use; these details were not established for this template.


#### Legacy paths — stated root base · `/authentication/signoutfromallsessionsandreauthenticate`

```text
POST https://octane.wtf/authentication/signoutfromallsessionsandreauthenticate
```

Check the request body, authentication, CSRF requirements, and permissions before use; these details were not established for this template.


#### Legacy paths — stated root base · `/cdn-cgi/rum`

```text
POST https://octane.wtf/cdn-cgi/rum
```

Check the request body, authentication, CSRF requirements, and permissions before use; these details were not established for this template.


#### Legacy paths — stated root base · `/money.aspx/getmytransactions`

```text
POST https://octane.wtf/money.aspx/getmytransactions
```

Check the request body, authentication, CSRF requirements, and permissions before use; these details were not established for this template.


#### Legacy paths — stated root base · `/promocodes/redeem`

```text
POST https://octane.wtf/promocodes/redeem
```

Check the request body, authentication, CSRF requirements, and permissions before use; these details were not established for this template.


#### Legacy paths — stated root base · `/v1/username`

```text
POST https://octane.wtf/v1/username
```

Check the request body, authentication, CSRF requirements, and permissions before use; these details were not established for this template.


*148 endpoint entries in this section, including separate host variants.*
