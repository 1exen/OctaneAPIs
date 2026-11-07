<div align="center">

<img src="assets/banner.png" alt="Octane API Reference" width="100%">

# OCTANE / API REFERENCE

**Every endpoint. One file.**

`OCTANE` · `OCTOMON'S` · `SERVICE HOSTS` · `LEGACY` · `STUDIO`

[![Unofficial](https://img.shields.io/badge/status-unofficial-22D3EE?style=flat-square&labelColor=120C2B)](https://octane.wtf/)
[![Endpoints](https://img.shields.io/badge/endpoints-200%2B-6C5CE7?style=flat-square&labelColor=120C2B)](#octane-api)
[![Octane](https://img.shields.io/badge/octane.wtf-live-8B7BFF?style=flat-square&labelColor=120C2B)](https://octane.wtf/)
[![Octomon's](https://img.shields.io/badge/octomons.wtf-live-8B7BFF?style=flat-square&labelColor=120C2B)](https://octomons.wtf/)
[![Checked](https://img.shields.io/badge/last%20checked-november%202026-3BBBC9?style=flat-square&labelColor=120C2B)](#live-checks)

<a href="#octane-api">Octane</a> ·
<a href="#octomons-api">Octomon's</a> ·
<a href="#service-hosts">Service Hosts</a> ·
<a href="#additional-octane-endpoints">Unconfirmed Hosts</a> ·
<a href="#legacy-paths">Legacy</a> ·
<a href="#live-checks">Live Checks</a>

<sub><b>UNOFFICIAL COMMUNITY DOCUMENTATION</b> · NOT AFFILIATED WITH OCTANE, OCTOMON'S, OR ROBLOX</sub>

</div>

---

> **Scope.** This file lists every endpoint found across both sites, with one runnable example per section.
> Nothing here is officially documented or supported. Endpoints were discovered from client code and
> live requests; some are unverified and may require authentication.

## Contents

| Section | Base | Entries |
| :-- | :-- | --: |
| [Octane API](#octane-api) | `https://octane.wtf/apisite` | 46 |
| [Octomon's API](#octomons-api) | `https://octomons.wtf` | 14 |
| [Service hosts](#service-hosts) | `https://<service>.octane.wtf` | 16 |
| [Additional Octane endpoints](#additional-octane-endpoints) | host unconfirmed | 96 |
| [Legacy paths](#legacy-paths) | `https://octane.wtf` | 25 |
| [Live checks](#live-checks) | — | status results |

---

# Octane API

> Profiles, inventories, avatars, groups, games, trades.

```text
BASE  https://octane.wtf/apisite
AUTH  none required for the public paths · all others require a session
```

Placeholders such as `{userId}` are replaced with real values. Query parameters shown are examples taken
from the client, not an exhaustive list.

### Users and presence

| Method | Path | Description |
| :-- | :-- | :-- |
| `GET` | `/users/v1/users/{userId}` | User profile. |
| `GET` | `/users/v1/users/search?keyword={keyword}&limit=10` | Search users. |
| `GET` | `/friends/v1/users/{userId}/friends` | List a user's friends. |
| `POST` | `/presence/v1/presence/users` | Presence for users. Body: `{"userIds":[1,2]}` |

```bash
curl 'https://octane.wtf/apisite/users/v1/users/1'
```

### Observed in a signed-in session

Paths captured from a browser resource log. Methods, bodies, and status codes were not recorded.

| Path | Apparent purpose |
| :-- | :-- |
| `/accountsettings/v1/themes/user` | User theme settings. |
| `/api/alerts/alert-info` | Alert information. |
| `/friends/v1/user/following-exists` | Following relationship check. |
| `/friends/v1/users/{userId}/followers/count` | Follower count. |
| `/friends/v1/users/{userId}/followings/count` | Following count. |
| `/friends/v1/users/{userId}/friends/statuses` | Friend relationship statuses. |
| `/groups/v1/users/{userId}/groups/roles` | User group roles. |
| `/users/v1/users/{userId}/status` | User status. |
| `/users/v1/users/{userId}/username-history` | Username history. |
| `/friends/v1/user/friend-requests/count` | Friend request count. |
| `/privatemessages/v1/messages/unread/count` | Unread private-message count. |
| `/trades/v1/trades/inbound/count` | Inbound trade count. |

```bash
curl 'https://octane.wtf/apisite/friends/v1/users/1/followers/count'
```

### Inventory and catalog

| Method | Path | Description |
| :-- | :-- | :-- |
| `GET` | `/inventory/v1/users/{userId}/assets/collectibles?limit=100&sortOrder=Desc` | Collectibles; supports `cursor`. |
| `GET` | `/inventory/v2/users/{userId}/inventory?assetTypes={types}&limit=100&sortOrder=Desc` | Inventory by asset type; supports `cursor`. |
| `GET` | `/inventory/v1/users/{userId}/items/Asset/{assetId}` | Check a user's inventory for an asset. |
| `GET` | `/catalog/v1/search/items?{query}` | Search catalog items. |
| `POST` | `/catalog/v1/catalog/items/details` | Catalog details. Body: `{"items":[{"itemType":"Asset","id":123}]}` |

```bash
curl 'https://octane.wtf/apisite/catalog/v1/search/items?keyword=hat&limit=10'
```

### Avatars and thumbnails

| Method | Path | Description |
| :-- | :-- | :-- |
| `GET` | `/avatar/v1/avatar-rules` | Avatar rules. |
| `GET` | `/avatar/v1/users/{userId}/avatar` | A user's avatar. |
| `GET` | `/thumbnails/v1/users/avatar-headshot?userIds={ids}&size=48x48&format=png` | Headshots for comma-separated IDs. |
| `GET` | `/thumbnails/v1/avatar/try-on?assetId={assetId}&userId={userId}&size=420x420&format=Png` | Try-on thumbnail. |
| `GET` | `/thumbnails/v1/games/icons?universeIds={ids}&size=150x150&format=Png` | Game icons. |

```bash
curl 'https://octane.wtf/apisite/thumbnails/v1/users/avatar-headshot?userIds=1,2&size=48x48&format=png'
```

### Groups

| Method | Path | Description |
| :-- | :-- | :-- |
| `GET` | `/groups/v2/users/{userId}/groups/roles` | A user's groups and roles. |
| `GET` | `/groups/v1/groups/{groupId}` | Group information. |
| `GET` | `/groups/v1/groups/{groupId}/roles` | Group roles. |
| `GET` | `/groups/v1/groups/{groupId}/users?limit=100&sortOrder=Desc` | Group members; supports `cursor`. |
| `GET` | `/groups/v1/groups/{groupId}/audit-log?limit=100&sortOrder=Desc` | Group audit log; may require permissions. |
| `DELETE` | `/groups/v1/groups/{groupId}/users/{userId}` | Remove a member. **Modifies data.** |

```bash
curl 'https://octane.wtf/apisite/groups/v1/groups/1'
```

### Games, economy, and trades

| Method | Path | Description |
| :-- | :-- | :-- |
| `GET` | `/games/v1/games?universeIds={ids}` | Game details. |
| `GET` | `/economy/v1/assets/{assetId}/resellers?limit=25` | Asset resellers. |
| `GET` | `/economy/v1/users/{userId}/currency` | User currency; access may be restricted. |
| `GET` | `/trades/v1/trades/{type}?limit={limit}&sortOrder=Desc` | Trades by type. |
| `GET` | `/trades/v1/trades/{tradeId}` | Individual trade; access may be restricted. |

```bash
curl 'https://octane.wtf/apisite/games/v1/games?universeIds=1'
```

### Root paths — no `/apisite`

| Method | Path | Description |
| :-- | :-- | :-- |
| `GET` | `/account/settings/countries` | Countries for account settings. |
| `GET` | `/users/{userId}` | Legacy user data. |
| `GET` | `/users/favorites/list-json?assetTypeId=9&itemsPerPage=24&pageNumber=1&userId={userId}` | User favorites. |

```bash
curl 'https://octane.wtf/users/1'
```

### Verified additional endpoints

Checked unauthenticated. Status applies to the example only.

| Method | URL | Result |
| :-- | :-- | :-- |
| `GET` | `https://octane.wtf/apisite/economy/v2/assets/{assetId}/details` | Asset `121969` → `200` |
| `GET` | `https://octane.wtf/apisite/games/v1/games/list?keyword={keyword}&maxRows=25` | `test` → `200` |
| `GET` | `https://octane.wtf/apisite/games/v1/games/multiget-playability-status?universeIds={ids}` | `1` → `200` |
| `GET` | `https://octane.wtf/apisite/thumbnails/v1/assets?assetIds={ids}&size=150x150&format=Png` | `121969` → `200` |
| `GET` | `https://inventory.octane.wtf/v1/users/{userId}/assets/collectibles?sortOrder=Desc&limit=100` | `1` → `403` |
| `GET` | `https://inventory.octane.wtf/v2/users/{userId}/inventory?assetTypes={types}` | `1`, type `8` → `403` |
| `POST` | `https://octane.wtf/apisite/friends/v1/users/{userId}/unfriend` | body `targetUserId`; untested |

```bash
curl 'https://octane.wtf/apisite/games/v1/games/list?keyword=test&maxRows=25'
```

### Account and privacy paths

Root host, no `/apisite`. Captured in a signed-in Fetch/XHR log; methods and responses not recorded.
Some of these read or modify sensitive settings.

| Path | Apparent purpose |
| :-- | :-- |
| `/account/settings/account-country` | Account country. |
| `/account/settings/account-restrictions` | Account restrictions. |
| `/account/settings/app-chat-privacy` | App chat privacy. |
| `/account/settings/follow-me-privacy` | Follow-me privacy. |
| `/account/settings/game-chat-privacy` | Game chat privacy. |
| `/account/settings/private-message-privacy` | Private message privacy. |
| `/account/settings/private-server-invite-privacy` | Private server invite privacy. |
| `/account/settings/settings-groups` | Account settings groups. |
| `/my/settings/json` | Account settings JSON. |
| `/user-sponsorship/2` | Sponsorship request; meaning of `2` unverified. |
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

```bash
curl 'https://octane.wtf/account/settings/countries'
```

---

# Octomon's API

> Player search, item details, thumbnails, and market activity.

```text
BASE  https://octomons.wtf
AUTH  none for reads · writes require the site session
```

| Method | Path | Description | Check |
| :-- | :-- | :-- | :-- |
| `GET` | `/api/items/v3/itemdetails` | Item details for global search. | `200` |
| `GET` | `/api/itemthumbs/v2/thumbssm` | Small item thumbnails. | `200` |
| `GET` | `/api/market/v2/dealactivity` | Market deal activity. | `200` |
| `GET` | `/api/market/v2/saleactivity` | Market sale activity. | `200` |
| `GET` | `/api/tradeads/v1/getrecentads` | Recent trade ads, past three minutes. | `200` |
| `GET` | `/api/players/v1/playersearch?searchstring={query}` | Search players. | `200` with `test` |
| `GET` | `/api/players/v1/playerassets/{userId}` | Live player assets. | `200` with `1` |
| `GET` | `/thumbs/avatar?userIds={ids}&size=150x150` | Avatar thumbnails; also `420x420`. | `200` with `1` |
| `GET` | `/thumbs/avatarbust?userIds={ids}&size=150x150` | Avatar bust thumbnails; also `420x420`. | `200` with `1` |
| `GET` | `/api/games/v1/gamelist` | Game list. | `404` |
| `GET` | `/api/groups/v1/groupsearch?searchstring={query}` | Group search. | `404` with `test` |

The final two appear in client code but returned `404` when checked.

### State-changing

Not tested — they modify data.

| Method | Path | Body |
| :-- | :-- | :-- |
| `POST` | `/api/players/v1/addplayerbyname` | `player_name` |
| `POST` | `/api/tradeads/v1/createad` | `player_id`, `offer_item_ids`, `request_item_ids`, `request_tags`, optional `offer_robux` |
| `POST` | `/api/tradeads/v1/removead/{adId}` | none observed |

```bash
curl 'https://octomons.wtf/api/players/v1/playersearch?searchstring=test'
```

### Catalog page data

`GET https://octomons.wtf/catalog` returns HTML containing `var item_details`. Observed array indexes:
name `0`, best price `5`, RAP `8`, value `16`, demand `17`, trend `18`, projected `19`, rare `21`,
thumbnail `23`, item type `24`, ID `25`. Implementation detail, not a stable API.

```bash
curl 'https://octomons.wtf/api/items/v3/itemdetails'
```

---

# Service hosts

> Per-service subdomains. Prefix each host with `https://`.

Methods, parameters, and response bodies were **not captured** for this set. Descriptions are inferred
from path names. Do not assume a service path accepts the same parameters as a similarly named `/apisite` path.

| Host | Path | Apparent purpose |
| :-- | :-- | :-- |
| `friends.octane.wtf` | `/v1/users/{userId}/friends` | User friends. |
| `games.octane.wtf` | `/v1/games/list` | Game list. |
| `games.octane.wtf` | `/v1/games/multiget-place-details` | Multiple place details. |
| `games.octane.wtf` | `/v1/games/sorts` | Game sorts. |
| `inventory.octane.wtf` | `/v2/assets/{assetId}/owners` | Asset owners; `121969` → `200`. |
| `metrics.octane.wtf` | `/v1/performance/send-measurement` | Performance measurement. |
| `metrics.octane.wtf` | `/v1/thumbnails/metadata` | Thumbnail metrics metadata. |
| `notifications.octane.wtf` | `/v2/notifications/get-settings` | Notification settings. |
| `presence.octane.wtf` | `/v1/presence/register-app-presence` | App presence registration. |
| `presence.octane.wtf` | `/v1/presence/users` | User presence. |
| `privatemessages.octane.wtf` | `/v1/announcements/metadata` | Announcement metadata. |
| `privatemessages.octane.wtf` | `/v1/messages` | Private messages. |
| `privatemessages.octane.wtf` | `/v1/messages/send` | Send a message. **Modifies data.** |
| `realtime.octane.wtf` | `/notifications/negotiate` | Real-time notification negotiation. |
| `realtime.octane.wtf` | `/notifications/start` | Start real-time notifications. |
| `thumbnails.octane.wtf` | `/v1/games/icons` | Game icon thumbnails. |
| `thumbnails.octane.wtf` | `/v1/users/avatar-headshot` | Avatar headshots. |
| `trades.octane.wtf` | `/v1/trades/inbound` | Inbound trades. |

```bash
curl 'https://inventory.octane.wtf/v2/assets/121969/owners'
```

---

# Additional Octane endpoints

> Paths from a supplied third-party reference. Host is **not confirmed** for most entries.

```text
HOST  ambiguous — the source gives https://octane.wtf/ but many paths appear
      under service-specific sections. Confirm the original request before use.
```

<details open>
<summary><b>Account information and promotion channels</b> — 7</summary>

| Method | Path |
| :-- | :-- |
| `GET` `POST` | `/v1/description` |
| `GET` | `/v1/birthdate` |
| `GET` `POST` | `/v1/gender` |
| `GET` | `/v1/metadata` |
| `GET` | `/v1/phone` |
| `GET` `POST` | `/v1/promotion-channels` |
| `GET` | `/v1/users/{userId}/promotion-channels` |

</details>

<details>
<summary><b>Avatar</b> — 8</summary>

| Method | Path |
| :-- | :-- |
| `GET` | `/v1/avatar` |
| `GET` | `/v1/avatar-rules` |
| `GET` | `/v1/avatar/metadata` |
| `GET` | `/v1/recent-items/all/list` |
| `GET` | `/v1/users/{userId}/avatar` |
| `POST` | `/v1/avatar/assets/{assetId}/wear` |
| `POST` | `/v1/avatar/set-wearing-assets` |
| `POST` | `/v1/avatar/redraw-thumbnail` |

</details>

<details>
<summary><b>Badges</b> — 2</summary>

| Method | Path | Notes |
| :-- | :-- | :-- |
| `GET` | `/v1/universes/{universeId}/badges` | `cursor`, `limit`, `sortOrder`. |
| `DELETE` | `/v1/user/badges/{badgeId}` | State-changing. |

</details>

<details>
<summary><b>Catalog</b> — 7</summary>

| Method | Path | Notes |
| :-- | :-- | :-- |
| `GET` | `/v1/catalog/metadata` | |
| `GET` | `/v1/categories` | |
| `GET` | `/v1/subcategories` | |
| `GET` | `/v1/search/navigation-menu-items` | |
| `GET` | `/v1/search/items` | `category`, `subcategory`, `keyword`, `limit`. |
| `GET` | `/v1/recommendations/metadata` | |
| `POST` | `/v1/catalog/items/details` | |

</details>

<details>
<summary><b>Chat and contacts</b> — 9</summary>

| Method | Path | Notes |
| :-- | :-- | :-- |
| `GET` | `/v2/get-messages` | |
| `POST` | `/v2/send-message` | State-changing. |
| `POST` | `/v2/update-user-typing-status` | |
| `GET` | `/v2/metadata` | Chat metadata. |
| `GET` | `/v2/get-unread-conversation-count` | |
| `GET` | `/v2/get-user-conversations` | `pageNumber`, `pageSize`. |
| `GET` | `/v2/multi-get-latest-messages` | `conversationIds`, `pageSize`. |
| `POST` | `/v1/user/get-tags` | |
| `GET` | `/v1/contacts/metadata` | |

</details>

<details>
<summary><b>Games</b> — 11</summary>

| Method | Path | Notes |
| :-- | :-- | :-- |
| `GET` | `/v1/games` | Requires `universeIds`. |
| `GET` | `/v1/games/list` | |
| `GET` | `/v1/games/multiget-place-details` | Requires `placeIds`. |
| `GET` | `/v1/games/multiget-playability-status` | Requires `universeIds`. |
| `GET` | `/v1/games/sorts` | |
| `GET` | `/v1/games/votes` | Requires `universeIds`; missing → `400`. |
| `GET` | `/v1/games/{universeId}/favorites` | |
| `GET` | `/v1/games/{universeId}/social-links/list` | |
| `GET` | `/v2/users/{userId}/games` | |
| `GET` | `/v2/groups/{groupId}/games` | |
| `GET` | `/v2/games/{universeId}/media` | |

</details>

<details>
<summary><b>Groups</b> — 11</summary>

| Method | Path | Notes |
| :-- | :-- | :-- |
| `GET` | `/v1/groups/{groupId}` | |
| `GET` | `/v1/groups/{groupId}/membership` | |
| `GET` | `/v1/groups/{groupId}/roles` | |
| `GET` | `/v1/groups/{groupId}/roles/{roleId}/users` | |
| `GET` | `/v1/groups/{groupId}/social-links` | |
| `GET` | `/v1/groups/{groupId}/payouts` | |
| `GET` | `/v1/groups/metadata` | |
| `GET` | `/v1/groups/search` | `keyword`, `limit`, `cursor`, `sortOrder`. |
| `GET` | `/v1/users/{userId}/groups/primary/role` | |
| `GET` | `/v1/users/{userId}/groups/roles` | |
| `GET` | `/v2/groups/{groupId}/wall/posts` | |

</details>

<details>
<summary><b>Friends, inventory, economy</b> — 7</summary>

| Method | Path | Notes |
| :-- | :-- | :-- |
| `GET` | `/v1/users/{userId}/friends` | Also seen on `friends.octane.wtf`. |
| `POST` | `/v1/users/{userId}/unfriend` | State-changing. |
| `GET` | `/v1/users/{userId}/friends/statuses` | `userIds`. |
| `GET` | `/v1/users/{userId}/assets/collectibles` | `assetType`, `cursor`, `limit`, `sortOrder`. |
| `GET` | `/v2/recommendations/{userId}` | |
| `GET` | `/v1/users/{userId}/currency` | |
| `GET` | `/v1/users/{userId}/revenue/summary/day` | |

</details>

<details>
<summary><b>Locale, metrics, presence, notifications</b> — 10</summary>

| Method | Path | Notes |
| :-- | :-- | :-- |
| `GET` | `/v1/locales/user-localization-locus-supported-locales` | |
| `GET` | `/v1/locales` | |
| `GET` | `/v1/thumbnails/metadata` | Also seen on `metrics.octane.wtf`. |
| `POST` | `/v1/performance/send-measurement` | Also seen on `metrics.octane.wtf`. |
| `GET` | `/v2/notifications/get-settings` | Also seen on `notifications.octane.wtf`. |
| `GET` | `/notifications/negotiate` | Also seen on `realtime.octane.wtf`. |
| `GET` | `/notifications/start` | Also seen on `realtime.octane.wtf`. |
| `POST` | `/notifications/abort` | |
| `POST` | `/v1/presence/users` | Also seen on `presence.octane.wtf`. |
| `POST` | `/v1/presence/register-app-presence` | Also seen on `presence.octane.wtf`. |

</details>

<details>
<summary><b>Thumbnails</b> — 8</summary>

| Method | Path | Notes |
| :-- | :-- | :-- |
| `GET` | `/v1/assets` | `assetIds`, `format`, `size`, `returnPolicy`. |
| `GET` | `/v1/badges/icons` | |
| `GET` | `/v1/games/icons` | Also seen on `thumbnails.octane.wtf`. |
| `GET` | `/v1/games/multiget/thumbnail` | |
| `GET` | `/v1/groups/icons` | |
| `GET` | `/v1/places/gameicons` | |
| `GET` | `/v1/users/avatar` | |
| `GET` | `/v1/users/avatar-headshot` | Also seen on `thumbnails.octane.wtf`. |

</details>

<details>
<summary><b>Trades, premium, two-step, users</b> — 11</summary>

| Method | Path | Notes |
| :-- | :-- | :-- |
| `GET` | `/v1/trades/inbound` | Also seen on `trades.octane.wtf`. |
| `GET` | `/v1/trades/outbound` | |
| `GET` | `/v1/trades/inactive` | |
| `GET` | `/v1/trades/{tradeId}` | |
| `POST` | `/v1/trades/send` | State-changing. |
| `POST` | `/v1/trades/{tradeId}/decline` | State-changing. |
| `GET` | `/v1/users/{userId}/subscriptions` | Premium subscription. |
| `GET` | `/v1/users/{userId}/configuration` | Two-step verification config. |
| `GET` | `/v1/users/{userId}` | User details. |
| `PATCH` | `/v1/users/{userId}/display-names` | State-changing. |
| `POST` | `/v1/users` | Source describes as internal only. |

</details>

<details>
<summary><b>Explicitly rooted paths</b> — 20</summary>

Methods attributed to the supplied reference.

| Method | Path | Notes |
| :-- | :-- | :-- |
| `GET` | `/account/settings/account-country` | |
| `GET` | `/account/settings/account-restrictions` | |
| `GET` | `/account/settings/app-chat-privacy` | |
| `GET` | `/account/settings/follow-me-privacy` | |
| `GET` | `/account/settings/game-chat-privacy` | |
| `GET` | `/account/settings/private-message-privacy` | |
| `GET` | `/account/settings/private-server-invite-privacy` | |
| `POST` | `/home/updatestatus` | State-changing. |
| `POST` | `/apisite/economy/v1/purchases/products/{productId}` | Purchase; state-changing. |
| `GET` | `/apisite/friends/v1/users/{userId}/followers/count` | |
| `GET` | `/apisite/friends/v1/users/{userId}/followings/count` | |
| `GET` | `/apisite/friends/v1/user/friend-requests/count` | |
| `POST` | `/apisite/friends/v1/user/following-exists` | |
| `GET` | `/apisite/privatemessages/v1/messages/unread/count` | |
| `GET` | `/apisite/privatemessages/v1/messages/{userId}/can-message` | |
| `GET` | `/apisite/trades/v1/trades/inbound/count` | |
| `GET` | `/apisite/trades/v1/users/{userId}/can-trade-with` | |
| `GET` | `/apisite/users/v1/users/authenticated` | |
| `GET` | `/apisite/users/v1/users/{userId}/status` | |
| `GET` | `/apisite/users/v1/users/{userId}/username-history` | |

```bash
curl 'https://octane.wtf/apisite/users/v1/users/authenticated'
```

</details>

<details>
<summary><b>Themes and privacy</b> — 9</summary>

| Method | Path |
| :-- | :-- |
| `GET` | `/v1/themes/types` |
| `GET` `PATCH` | `/v1/themes/user` |
| `GET` | `/v1/privacy` |
| `GET` | `/v1/privacy/info` |
| `GET` `POST` | `/v1/inventory-privacy` |
| `GET` `POST` | `/v1/trade-privacy` |
| `GET` | `/v1/trade-value` |
| `GET` | `/v1/social/connected-providers` |
| `GET` | `/v1/xbox/connection` |

</details>

---

# Legacy paths

> Old Roblox-era paths preserved on the root host.

| Method | Path |
| :-- | :-- |
| `GET` | `/asset/profile-status` |
| `GET` | `/avatar-thumbnail-3d/json` |
| `GET` | `/build/universes` |
| `GET` | `/comments/get-json` |
| `GET` | `/game/get-join-script` |
| `GET` | `/games/getfriendsgameinstances` |
| `GET` | `/games/getgameinstancesjson` |
| `GET` | `/games/votingservice/{id}` |
| `GET` | `/my/settings/json` |
| `GET` | `/private-server/instance-list-json` |
| `GET` | `/recipe?iteration` |
| `GET` | `/referral/v1/me` |
| `GET` | `/search/users/metadata` |
| `GET` | `/search/users/results` |
| `GET` | `/usercheck/show-tos` |
| `GET` | `/users/favorites/list-json` |
| `GET` | `/users/inventory/list-json` |
| `GET` | `/users/profile/playerassets-json` |
| `GET` | `/users/profile/robloxcollections-json` |
| `POST` | `/abuse-report/v1/report` |
| `POST` | `/authentication/signoutfromallsessionsandreauthenticate` |
| `POST` | `/cdn-cgi/rum` |
| `POST` | `/money.aspx/getmytransactions` |
| `POST` | `/promocodes/redeem` |
| `POST` | `/v1/username` |

```bash
curl 'https://octane.wtf/search/users/results?keyword=test'
```

---

# Live checks

> Unauthenticated requests made directly against the live servers. A result describes that one
> example, not every user or asset.

### Confirmed working — `200`

`/users/v1/users/{id}` · `/users/v1/users/search` · `/users/v1/users/{id}/status` ·
`/users/v1/users/{id}/username-history` · `/friends/v1/users/{id}/friends` ·
`/friends/v1/users/{id}/followers/count` · `/followings/count` · `/avatar/v1/users/{id}/avatar` ·
`/avatar/v1/avatar-rules` · `/thumbnails/v1/users/avatar-headshot` · `/groups/v2/users/{id}/groups/roles` ·
`/groups/v1/groups/{id}` · `/catalog/v1/search/items`

### Exists but gated

| Path | Status | Reason |
| :-- | :-- | :-- |
| `/users/v1/users/authenticated` | `401` | Needs a session. |
| `/presence/v1/presence/users` | `405` on `GET` | Needs `POST`. |
| `/friends/v1/user/following-exists` | `405` on `GET` | Needs `POST`. |
| `/friends/v1/users/{id}/friends/statuses` | `401` | Needs a session. |
| `/inventory/v1/users/{id}/assets/collectibles` | `403` | Owner-only. |
| `/inventory/v2/users/{id}/inventory` | `403` | Owner-only. |
| `/economy/v1/users/{id}/currency` | `403` | Restricted. |
| `/trades/v1/trades/inbound/count` | `401` | Needs a session. |
| `/accountsettings/v1/themes/user` | `401` | Needs a session. |
| `/users/v1/users` (`POST`) | `403` | CSRF token required. |

### Documented elsewhere, returned `404` here

| Path | Note |
| :-- | :-- |
| `/users/v1/users/{id}/subscriptions` | Not present on `/apisite`. |
| `/users/v1/users/{id}/configuration` | Not present on `/apisite`. |
| `/users/v1/users/{id}/promotion-channels` | Not present on `/apisite`. |
| `/users/v1/users/bulk` · `/all` · `/list` | No bulk-listing endpoint exists. |

```bash
curl -s -o /dev/null -w '%{http_code}\n' 'https://octane.wtf/apisite/users/v1/users/1'
```

### Status codes

`200` ok · `400` bad or missing parameters · `401` session required · `403` blocked or CSRF
`404` literal `Not found`, path does not exist · `405` wrong method · `429` rate limited · `500` server error

> A `404` body is exactly `Not found` — the most reliable signal for whether a path exists at all.

---

<div align="center">

### Not affiliated with Octane, Octomon's, or Roblox.

*Respect the sites' terms, privacy settings, and rate limits.*
*Never publish session cookies, CSRF tokens, or personal data.*

<a href="#contents">Back to top</a>

</div>
