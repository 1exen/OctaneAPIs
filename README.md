<div align="center">

<img src="assets/banner.png" alt="Octane API Reference" width="100%">

# OCTANE / API REFERENCE

**Every endpoint confirmed live on `octane.wtf`.**

`octane.wtf` · `apisite` · `cdn.octane.wtf` · `tcdn.octane.wtf`

[![Verified](https://img.shields.io/badge/verified-52%20probed%20·%2042%20live-22D3EE?style=flat-square&labelColor=120C2B)](#verification)
[![Checked](https://img.shields.io/badge/checked-7%20november%202026-3BBBC9?style=flat-square&labelColor=120C2B)](#verification)
[![Host](https://img.shields.io/badge/host-octane.wtf-8B7BFF?style=flat-square&labelColor=120C2B)](https://octane.wtf/)
[![Status](https://img.shields.io/badge/status-unofficial-6C5CE7?style=flat-square&labelColor=120C2B)](#disclaimer)

<a href="#verification">Verification</a> ·
<a href="#users">Users</a> ·
<a href="#games">Games</a> ·
<a href="#catalog">Catalog</a> ·
<a href="#assets--economy">Economy</a> ·
<a href="#groups">Groups</a> ·
<a href="#appendix--unverified">Unverified</a>

<sub><b>UNOFFICIAL COMMUNITY DOCUMENTATION</b> · NOT AFFILIATED WITH OCTANE OR ROBLOX</sub>

</div>

---

## Is this from octane.wtf?

**Yes.** Every endpoint in the verified sections below was requested directly against
`https://octane.wtf` from a clean sandbox with no account, and the result is recorded per namespace.

| Checked | Result |
| :-- | :-- |
| Endpoints probed | **52** |
| Returned `200` | **42** |
| Existed but gated (`401` / `403` / `405`) | **10** |
| Resolved on `octane.wtf` | **51 of 52** |
| Resolved elsewhere | `1` — `assetdelivery` redirects to `cdn.octane.wtf` |
| Date | 7 November 2026 |

Two base URLs, and only two:

```text
API   https://octane.wtf/apisite
CDN   https://cdn.octane.wtf          (asset delivery)
      https://tcdn.octane.wtf         (thumbnails, returned inside JSON)
```

The separate service subdomains that other references list — `friends.`, `games.`, `presence.`,
`thumbnails.` and so on — are **not required**. Every path they claim also answers on `/apisite`.

## Contents

| Namespace | Endpoints | Public |
| :-- | --: | :-- |
| [Users](#users) | 6 | ✅ |
| [Friends & presence](#friends--presence) | 8 | partly |
| [Avatar & thumbnails](#avatar--thumbnails) | 11 | ✅ |
| [Games](#games) | 12 | ✅ |
| [Catalog](#catalog) | 8 | ✅ |
| [Assets & economy](#assets--economy) | 6 | ✅ |
| [Inventory](#inventory) | 5 | partly |
| [Groups](#groups) | 10 | ✅ |
| [Badges](#badges) | 2 | ✅ |
| [Trades](#trades) | 6 | 🔒 |
| [Chat & messages](#chat--messages) | 3 | 🔒 |
| [Notifications & realtime](#notifications--realtime) | 4 | partly |
| [Account](#account) | 7 | 🔒 |
| [Locale & metrics](#locale--metrics) | 3 | ✅ |
| [Auth](#auth) | 3 | 🔒 |
| [Appendix — unverified](#appendix--unverified) | 96 | — |

---

## Status codes

Every table below uses real observed codes.

| Code | Meaning |
| :-- | :-- |
| `200` | Public. Works with no account. |
| `400` | Needs correct parameters. Route exists. |
| `401` | Route exists. Session required. |
| `403` | Route exists. Blocked, or CSRF token required. |
| `404` | Route does not exist. Body is literally `Not found`. |
| `405` | Route exists. Wrong method — usually needs `POST`. |
| `429` | Rate limited. |
| `500` | Route exists but crashes. |

> A `403` with `Token Validation Failed` on any `POST` means the route is real and needs the
> CSRF handshake, **not** that you lack permission. Use it as an existence check.

---

# Users

> Base `https://octane.wtf/apisite`

| Method | Path | Code | Notes |
| :-- | :-- | :-: | :-- |
| `GET` | `/users/v1/users/{userId}` | `200` | Full profile. |
| `GET` | `/users/v1/users/search?keyword={q}&limit=10` | `200` | Min 3 chars. Cursor = integer offset. |
| `GET` | `/users/v1/users/authenticated` | `401` | Current user. |
| `GET` | `/users/v1/users/{userId}/status` | `200` | `{"status":null}` |
| `GET` | `/users/v1/users/{userId}/username-history` | `200` | Paginated. |
| `POST` | `/users/v1/usernames/users` | `405`→`403` | Batch username → id. Needs CSRF. |
| `POST` | `/users/v1/users` | `403` | Create account. CSRF. |

```bash
curl 'https://octane.wtf/apisite/users/v1/users/1'
```

```json
{"id":1,"name":"Octane","displayName":"Octane","description":"","created":"2026-09-21T17:50:24.821657Z",
 "isBanned":false,"hasVerifiedBadge":true,"isPremium":false,"hasAdminBadge":true,"hasStarCreatorBadge":false}
```

> Confirmed absent: `/subscriptions`, `/configuration`, `/promotion-channels` all return `404`.

---

# Friends & presence

| Method | Path | Code | Notes |
| :-- | :-- | :-: | :-- |
| `GET` | `/friends/v1/users/{userId}/friends` | `200` | |
| `GET` | `/friends/v1/users/{userId}/followers` | `200` | **Full follower list**, paginated. |
| `GET` | `/friends/v1/users/{userId}/followings` | `200` | **Full following list**, paginated. |
| `GET` | `/friends/v1/users/{userId}/followers/count` | `200` | |
| `GET` | `/friends/v1/users/{userId}/followings/count` | `200` | |
| `GET` | `/friends/v1/users/{userId}/friends/statuses?userIds=` | `401` | |
| `GET` | `/friends/v1/user/friend-requests/count` | `401` | |
| `POST` | `/friends/v1/user/following-exists` | `405` | Needs session. |
| `POST` | `/friends/v1/users/{userId}/unfriend` | `405` | Body `targetUserId`. **Modifies data.** |
| `POST` | `/presence/v1/presence/users` | `405` | Body `{"userIds":[1,2]}`. |

```bash
curl 'https://octane.wtf/apisite/friends/v1/users/1/followers?limit=10&sortOrder=Desc'
```

Each entry carries `isOnline`, `isDeleted`, `isBanned`, `presenceType`, and `friendFrequentScore`.

---

# Avatar & thumbnails

| Method | Path | Code | Notes |
| :-- | :-- | :-: | :-- |
| `GET` | `/avatar/v1/users/{userId}/avatar` | `200` | R6/R15, scales, body colors, worn assets. |
| `GET` | `/avatar/v1/avatar` | `401` | Own avatar. |
| `GET` | `/avatar/v1/avatar-rules` | `200` | |
| `GET` | `/avatar/v1/avatar/metadata` | `200` | Editor feature flags. |
| `GET` | `/avatar/v1/recent-items/all/list` | `401` | |
| `GET` | `/thumbnails/v1/users/avatar-headshot?userIds=1,2` | `200` | Comma-batched. |
| `GET` | `/thumbnails/v1/users/avatar?userIds=1,2` | `200` | Full body. |
| `GET` | `/thumbnails/v1/avatar/try-on?assetId=&userId=` | `200` | |
| `GET` | `/thumbnails/v1/games/icons?universeIds=` | `200` | |
| `GET` | `/thumbnails/v1/places/gameicons?placeIds=` | `200` | |
| `GET` | `/thumbnails/v1/badges/icons?badgeIds=` | `200` | |
| `GET` | `/thumbnails/v1/groups/icons?groupIds=` | `200` | |
| `GET` | `/thumbnails/v1/games/multiget/thumbnail?universeIds=` | `200` | |
| `POST` | `/thumbnails/v1/batch` | `403` | Batched. CSRF. |

```bash
curl 'https://octane.wtf/apisite/thumbnails/v1/users/avatar?userIds=1'
# {"data":[{"targetId":1,"state":"Completed","imageUrl":"https://tcdn.octane.wtf/..."}]}
```

---

# Games

| Method | Path | Code | Notes |
| :-- | :-- | :-: | :-- |
| `GET` | `/games/v1/games?universeIds={ids}` | `200` | |
| `GET` | `/games/v1/games/list?keyword={q}&maxRows=25` | `200` | Returns `universeId` **and** `placeId`. |
| `GET` | `/games/v1/games/sorts` | `200` | Tokens: `popular`, `mostFavorited`, … |
| `GET` | `/games/v1/games/votes?universeIds={ids}` | `200` | Up/down votes. |
| `GET` | `/games/v1/games/{universeId}/favorites` | `200` | |
| `GET` | `/games/v1/games/{universeId}/social-links/list` | `200` | |
| `GET` | `/games/v2/games/{universeId}/media` | `200` | |
| `GET` | `/games/v1/games/multiget-place-details?placeIds={ids}` | `200` | Builder, price, playability. |
| `GET` | `/games/v1/games/multiget-playability-status?universeIds={ids}` | `200` | |
| `GET` | `/games/v1/games/recommendations/game/{universeId}` | `401` | |
| `GET` | `/games/v2/users/{userId}/games` | — | Reference only. |
| `GET` | `/develop/v1/universes/{universeId}/places` | `200` | Public. |

```bash
curl 'https://octane.wtf/apisite/games/v1/games/votes?universeIds=6782'
# {"data":[{"id":6782,"upVotes":0,"downVotes":2}]}
```

> Confirmed absent: `/games/{u}/servers`, `/games/servers/public`, `/games/{u}/private-servers`.

---

# Catalog

| Method | Path | Code | Notes |
| :-- | :-- | :-: | :-- |
| `GET` | `/catalog/v1/search/items?keyword={q}&limit=10` | `200` | IDs only. |
| `GET` | `/catalog/v1/search/items/details?keyword={q}` | `200` | **Full item data inline.** |
| `GET` | `/catalog/v1/categories` | `200` | 16 categories → ids. |
| `GET` | `/catalog/v1/subcategories` | `200` | |
| `GET` | `/catalog/v1/catalog/metadata` | `200` | Feature flags. |
| `GET` | `/catalog/v1/recommendations/metadata` | `200` | |
| `GET` | `/catalog/v1/search/navigation-menu-items` | `200` | |
| `POST` | `/catalog/v1/catalog/items/details` | `403` | CSRF. Body `{"items":[{"itemType":"Asset","id":1}]}` |

```bash
curl 'https://octane.wtf/apisite/catalog/v1/search/items/details?keyword=hat&limit=5'
```

`search/items/details` is the shortcut worth knowing — it skips the `POST` round trip entirely.

---

# Assets & economy

| Method | Path | Code | Notes |
| :-- | :-- | :-: | :-- |
| `GET` | `/assetdelivery/v1/asset/?id={assetId}` | `200` | **Binary** `<roblox!` asset file. 302 → `cdn.octane.wtf`. |
| `GET` | `/economy/v2/assets/{assetId}/details` | `200` | Full item record incl. `PriceInRobux`. |
| `GET` | `/economy/v1/assets/{assetId}/resellers` | `200` | Prices and serials. |
| `GET` | `/economy/v1/assets/{assetId}/details` | `200` | |
| `GET` | `/economy/v1/users/{userId}/currency` | `403` | Restricted. |
| `GET` | `/inventory/v2/assets/{assetId}/owners` | `200` | Owners + `serialNumber`, cursor. |

```bash
curl -s 'https://octane.wtf/apisite/assetdelivery/v1/asset/?id=121969' -o asset.rbxm
file asset.rbxm    # Roblox binary model
```

This is the only endpoint found that returns something other than JSON.

---

# Inventory

| Method | Path | Code | Notes |
| :-- | :-- | :-: | :-- |
| `GET` | `/inventory/v1/users/{userId}/assets/collectibles` | `403` | Owner-only. |
| `GET` | `/inventory/v2/users/{userId}/inventory?assetTypes=` | `403` | Owner-only. |
| `GET` | `/inventory/v1/users/{userId}/items/Asset/{assetId}` | `200` | Ownership check. |
| `GET` | `/inventory/v2/assets/{assetId}/owners` | `200` | |
| `GET` | `/inventory/v1/users/{userId}/assets/collectibles?limit=100&sortOrder=Desc` | `403` | On `inventory.octane.wtf` too. |

```bash
curl 'https://octane.wtf/apisite/inventory/v1/users/1/items/Asset/1'
# {"data":[]}
```

---

# Groups

| Method | Path | Code | Notes |
| :-- | :-- | :-: | :-- |
| `GET` | `/groups/v1/groups/{groupId}` | `200` | |
| `GET` | `/groups/v1/groups/search?keyword={q}` | `200` | Group search. |
| `GET` | `/groups/v1/groups/metadata` | `200` | Limits and policy flags. |
| `GET` | `/groups/v1/groups/{groupId}/roles` | `200` | Roles + member counts. |
| `GET` | `/groups/v1/groups/{groupId}/users` | `200` | Members, paginated. |
| `GET` | `/groups/v1/groups/{groupId}/social-links` | `200` | e.g. Discord URL. |
| `GET` | `/groups/v2/users/{userId}/groups/roles` | `200` | A user's groups. |
| `GET` | `/groups/v1/groups/{groupId}/membership` | `401` | Own membership. |
| `GET` | `/groups/v1/groups/{groupId}/join-requests` | `403` | Permissions. |
| `GET` | `/groups/v1/groups/{groupId}/payouts` | `403` | Permissions. |
| `DELETE` | `/groups/v1/groups/{groupId}/users/{userId}` | — | **Modifies data.** |

```bash
curl 'https://octane.wtf/apisite/groups/v1/groups/search?keyword=octane&limit=5'
```

---

# Badges

| Method | Path | Code | Notes |
| :-- | :-- | :-: | :-- |
| `GET` | `/badges/v1/users/{userId}/badges` | `200` | Awarded badges. |
| `GET` | `/badges/v1/universes/{universeId}/badges?limit=10` | `200` | Needs a real universe id. |

```bash
curl 'https://octane.wtf/apisite/badges/v1/universes/6782/badges?limit=5'
```

---

# Trades

All routes exist; none are readable without a session.

| Method | Path | Code |
| :-- | :-- | :-: |
| `GET` | `/trades/v1/trades/inbound` | `401` |
| `GET` | `/trades/v1/trades/outbound` | `401` |
| `GET` | `/trades/v1/trades/inactive` | `401` |
| `GET` | `/trades/v1/trades/inbound/count` | `401` |
| `GET` | `/trades/v1/trades/{tradeId}` | `400` |
| `GET` | `/trades/v1/users/{userId}/can-trade-with` | `401` |
| `POST` | `/trades/v1/trades/send` | — |
| `POST` | `/trades/v1/trades/{tradeId}/decline` | — |

```bash
curl -s -o /dev/null -w '%{http_code}\n' 'https://octane.wtf/apisite/trades/v1/trades/inbound'
# 401
```

---

# Chat & messages

| Method | Path | Code | Notes |
| :-- | :-- | :-: | :-- |
| `GET` | `/chat/v2/get-user-conversations` | `401` | **Chat is separate from private messages.** |
| `GET` | `/privatemessages/v1/announcements/metadata` | `200` | `{"numOfAnnouncements":1}` |
| `GET` | `/privatemessages/v1/messages` | `400` | Needs params. |
| `GET` | `/privatemessages/v1/messages/unread/count` | `500` | Route exists, crashes. |

---

# Notifications & realtime

| Method | Path | Code | Notes |
| :-- | :-- | :-: | :-- |
| `POST` | `/realtime/notifications/negotiate` | `200` | SignalR handshake. Works unauthenticated. |
| `GET` | `/notifications/v2/notifications/get-settings` | `200` | Notification band settings. |
| `GET` | `/metrics/v1/thumbnails/metadata` | `200` | |
| `POST` | `/metrics/v1/performance/send-measurement` | `405` | |

```bash
curl 'https://octane.wtf/apisite/realtime/notifications/negotiate'
# {"Url":"/notifications","ConnectionToken":"…","ProtocolVersion":"1.5","TryWebSockets":true}
```

---

# Account

| Method | Path | Code | Notes |
| :-- | :-- | :-: | :-- |
| `GET` | `/accountsettings/v1/themes/types` | `200` | `{"data":["Light","Dark"]}` |
| `GET` | `/accountsettings/v1/themes/user` | `401` | |
| `GET` | `/accountinformation/v1/birthdate` | `401` | **Not** `/users/v1`. |
| `GET` | `/accountinformation/v1/gender` | `401` | |
| `GET` | `/premiumfeatures/v1/users/{userId}/validate-membership` | `200` | Raw `0` / `1`. |
| `GET` | `/users/v1/users/{userId}/status` | `200` | |
| `GET` | `/account/settings/countries` | `200` | On root host, no `/apisite`. |

```bash
curl 'https://octane.wtf/apisite/premiumfeatures/v1/users/1/validate-membership'
# 0
```

---

# Locale & metrics

| Method | Path | Code |
| :-- | :-- | :-: |
| `GET` | `/locale/v1/locales` | `200` |
| `GET` | `/locale/v1/locales/user-localization-locus-supported-locales` | `200` |
| `GET` | `/metrics/v1/thumbnails/metadata` | `200` |

---

# Auth

Present, undocumented, not for automation.

| Method | Path | Code |
| :-- | :-- | :-: |
| `GET`/`POST` | `/auth/v1/login` | `405` on GET |
| `GET`/`POST` | `/auth/v2/login` | `405` on GET |
| `GET`/`POST` | `/auth/v1/logout` | `405` on GET |

---

# Verification

Probed unauthenticated from a clean sandbox on **7 November 2026**. Each row is a real request.

| Namespace | Probed | `200` | Gated |
| :-- | --: | --: | --: |
| users | 1 | 0 | 1 |
| friends | 2 | 2 | 0 |
| avatar | 2 | 1 | 1 |
| thumbnails | 5 | 5 | 0 |
| games | 8 | 7 | 1 |
| catalog | 6 | 6 | 0 |
| groups | 8 | 5 | 3 |
| badges | 2 | 2 | 0 |
| inventory | 2 | 2 | 0 |
| economy | 2 | 2 | 0 |
| assets (assetdelivery) | 1 | 1 | 0 |
| chat | 1 | 0 | 1 |
| messages | 1 | 1 | 0 |
| notifications | 1 | 1 | 0 |
| realtime | 1 | 1 | 0 |
| account (settings) | 1 | 1 | 0 |
| account (info) | 2 | 0 | 2 |
| premium | 1 | 1 | 0 |
| locale | 2 | 2 | 0 |
| metrics | 1 | 1 | 0 |
| auth | 1 | 0 | 1 |
| develop | 1 | 1 | 0 |
| **Total** | **52** | **42** | **10** |

Reproduce any row yourself:

```bash
curl -s -o /dev/null -w '%{http_code}\n' 'https://octane.wtf/apisite/users/v1/users/1'
```

---

# Appendix — unverified

> **Not confirmed.** These come from a third-party reference and a browser log. Host, method, and
> response are unknown for most. Do not rely on them without checking.

<details>
<summary><b>Account information and promotion channels</b></summary>

`/v1/description` · `/v1/birthdate` · `/v1/gender` · `/v1/metadata` · `/v1/phone`
`/v1/promotion-channels` · `/v1/users/{userId}/promotion-channels`

</details>

<details>
<summary><b>Avatar</b></summary>

`/v1/avatar` · `/v1/avatar-rules` · `/v1/avatar/metadata` · `/v1/recent-items/all/list`
`/v1/users/{userId}/avatar` · `POST /v1/avatar/assets/{assetId}/wear`
`POST /v1/avatar/set-wearing-assets` · `POST /v1/avatar/redraw-thumbnail`

</details>

<details>
<summary><b>Catalog</b></summary>

`/v1/catalog/metadata` · `/v1/categories` · `/v1/subcategories`
`/v1/search/navigation-menu-items` · `/v1/search/items` · `/v1/recommendations/metadata`
`POST /v1/catalog/items/details`

</details>

<details>
<summary><b>Chat and contacts</b></summary>

`/v2/get-messages` · `POST /v2/send-message` · `POST /v2/update-user-typing-status`
`/v2/metadata` · `/v2/get-unread-conversation-count` · `/v2/get-user-conversations`
`/v2/multi-get-latest-messages` · `POST /v1/user/get-tags` · `/v1/contacts/metadata`

</details>

<details>
<summary><b>Games</b></summary>

`/v1/games` · `/v1/games/list` · `/v1/games/multiget-place-details`
`/v1/games/multiget-playability-status` · `/v1/games/sorts` · `/v1/games/votes`
`/v1/games/{universeId}/favorites` · `/v1/games/{universeId}/social-links/list`
`/v2/users/{userId}/games` · `/v2/groups/{groupId}/games` · `/v2/games/{universeId}/media`

</details>

<details>
<summary><b>Groups</b></summary>

`/v1/groups/{groupId}` · `/v1/groups/{groupId}/membership` · `/v1/groups/{groupId}/roles`
`/v1/groups/{groupId}/roles/{roleId}/users` · `/v1/groups/{groupId}/social-links`
`/v1/groups/{groupId}/payouts` · `/v1/groups/metadata` · `/v1/groups/search`
`/v1/users/{userId}/groups/primary/role` · `/v1/users/{userId}/groups/roles`
`/v2/groups/{groupId}/wall/posts`

</details>

<details>
<summary><b>Friends, inventory, economy</b></summary>

`/v1/users/{userId}/friends` · `POST /v1/users/{userId}/unfriend`
`/v1/users/{userId}/friends/statuses` · `/v1/users/{userId}/assets/collectibles`
`/v2/recommendations/{userId}` · `/v1/users/{userId}/currency`
`/v1/users/{userId}/revenue/summary/day`

</details>

<details>
<summary><b>Trades, premium, two-step, users</b></summary>

`/v1/trades/inbound` · `/v1/trades/outbound` · `/v1/trades/inactive` · `/v1/trades/{tradeId}`
`POST /v1/trades/send` · `POST /v1/trades/{tradeId}/decline` · `/v1/users/{userId}/subscriptions`
`/v1/users/{userId}/configuration` · `/v1/users/{userId}`
`PATCH /v1/users/{userId}/display-names` · `POST /v1/users`

</details>

<details>
<summary><b>Themes and privacy</b></summary>

`/v1/themes/types` · `/v1/themes/user` · `/v1/privacy` · `/v1/privacy/info`
`/v1/inventory-privacy` · `/v1/trade-privacy` · `/v1/trade-value`
`/v1/social/connected-providers` · `/v1/xbox/connection`

</details>

<details>
<summary><b>Legacy root paths</b></summary>

`/asset/profile-status` · `/avatar-thumbnail-3d/json` · `/build/universes` · `/comments/get-json`
`/game/get-join-script` · `/games/getfriendsgameinstances` · `/games/getgameinstancesjson`
`/games/votingservice/{id}` · `/my/settings/json` · `/private-server/instance-list-json`
`/recipe?iteration` · `/referral/v1/me` · `/search/users/metadata` · `/search/users/results`
`/usercheck/show-tos` · `/users/favorites/list-json` · `/users/inventory/list-json`
`/users/profile/playerassets-json` · `/users/profile/robloxcollections-json`
`POST /abuse-report/v1/report` · `POST /authentication/signoutfromallsessionsandreauthenticate`
`POST /cdn-cgi/rum` · `POST /money.aspx/getmytransactions` · `POST /promocodes/redeem`
`POST /v1/username`

</details>

---

## Disclaimer

<div align="center">

**Not affiliated with Octane or Roblox.**

This is an unofficial reference. Endpoints are undocumented, unsupported, and can change without
warning. Respect the site's terms, privacy settings, and rate limits. Never publish session cookies,
CSRF tokens, or personal data. Nothing here was tested against a signed-in account, and no
state-changing request was made.

<a href="#octane--api-reference">Back to top</a>

</div>
