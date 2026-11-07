<div align="center">

<img src="assets/banner.png" alt="Octane APIs" width="100%">

# OCTANE APIs

**121 verified routes across three API surfaces — 83 readable, 38 mutation.**

[![Endpoints](https://img.shields.io/badge/routes-121%20verified-22D3EE?style=flat-square&labelColor=120C2B)](#verification)
[![Probed](https://img.shields.io/badge/probed-143-6C5CE7?style=flat-square&labelColor=120C2B)](#verification)
[![Auth](https://img.shields.io/badge/auth-none%20required-3BBBC9?style=flat-square&labelColor=120C2B)](#quick-start)
[![Checked](https://img.shields.io/badge/checked-7%20nov%202026-8B7BFF?style=flat-square&labelColor=120C2B)](#verification)
[![License](https://img.shields.io/badge/license-MIT-4A9BD6?style=flat-square&labelColor=120C2B)](LICENSE)

<a href="#quick-start">Quick start</a> ·
<a href="#architecture">Architecture</a> ·
<a href="#endpoint-index">Endpoint index</a> ·
<a href="#surface-1--apisite">/apisite</a> ·
<a href="#surface-2--root-host">Root host</a> ·
<a href="#verification">Verification</a> ·
<a href="#appendix">Appendix</a>

<sub><b>UNOFFICIAL COMMUNITY DOCUMENTATION</b> · NOT AFFILIATED WITH OCTANE OR ROBLOX</sub>

</div>

---

## What this is

A complete, live-verified map of the HTTP API behind **[octane.wtf](https://octane.wtf/)**.

Octane is a Roblox-compatible platform. Its API is undocumented — there is no developer portal,
no OpenAPI spec, no `/docs`. Everything here was recovered by reading client code and probing the
live server with unauthenticated requests, then recording the actual status code for every path.

Every endpoint in the verified sections was **confirmed working from a clean sandbox with no account**.

<div align="center">

| 121 | 374 | 88 | 0 |
|:--:|:--:|:--:|:--:|
| **live endpoints** | **paths probed** | **gated routes** | **auth needed to start** |

</div>

---

## Quick start

Three working calls. No key, no account, no setup.

```bash
# 1 — a player profile
curl 'https://octane.wtf/apisite/users/v1/users/1'

# 2 — search players
curl 'https://octane.wtf/apisite/users/v1/users/search?keyword=octane&limit=5'

# 3 — a headshot image URL
curl 'https://octane.wtf/apisite/thumbnails/v1/users/avatar-headshot?userIds=1&size=48x48&format=png'
```

That's the whole idea: plain GET requests, JSON back.

> [!TIP]
> Player IDs start at `1` and count up. Change the `1` in the first call to any number and
> you get a different player. As of November 2026 the highest ID is around **39,123**.

---

## Architecture

<img src="assets/architecture.png" alt="Three API surfaces: /apisite, root host, CDN" width="100%">

There are **three independent surfaces** on the same host, and they do not behave alike.

| Surface | Base | Shape | Why it matters |
| :-- | :-- | :-- | :-- |
| **Modern** | `https://octane.wtf/apisite` | camelCase JSON | 48 endpoints. The main surface. |
| **Legacy** | `https://octane.wtf/` | PascalCase JSON, some HTML | 29 endpoints. Different payloads for the same data. |
| **CDN** | `cdn.` / `tcdn.octane.wtf` | binary + image URLs | Asset files and avatars. |

The same player through two surfaces:

```jsonc
// /apisite — modern
{"id":1,"name":"Octane","displayName":"Octane","isBanned":false}

// root host — legacy
{"Id":1,"Username":"Octane","AvatarUri":null,"Online":false}
```

> [!IMPORTANT]
> **Check `content-type` on the root host.** Roughly 25 paths there return `200 text/html` — that is
> the React app's catch-all, not an endpoint. A `200` alone means nothing on that surface.

> [!NOTE]
> **Service subdomains are not required.** `friends.`, `games.`, `presence.`, `thumbnails.` and the
> rest are wildcard DNS. A bogus `zznotreal.octane.wtf` resolves identically. Every path answers on
> `/apisite` without them.

---

## Status codes

| Code | Meaning | What to do |
| :-- | :-- | :-- |
| `200` | Public | Works with no account. |
| `400` | Bad parameters | Route exists. Fix the request. |
| `401` | Session required | Route exists. Needs auth. |
| `403` | Blocked or CSRF | Needs a token or permission. **Does not prove the route exists.** |
| `404` | **Does not exist** | Body is literally `Not found`. |
| `405` | Wrong method | **Route confirmed.** Read the `allow` header. |
| `429` | Rate limited | Back off. |
| `500` | Server error | Route exists, crashes. |

> [!IMPORTANT]
> **`403 Token Validation Failed` proves nothing.** That middleware runs *before* routing, so a
> path that does not exist returns the identical `403`. `POST /zzz-fake-logout` and
> `POST /apisite/auth/v9/logout` both return it.
>
> **The reliable test is `GET`:** a real POST-only route answers `405` with an `allow` header;
> a fake one answers `404`. This is how every mutation route below was confirmed.

> [!WARNING]
> **`OPTIONS` is useless here.** Every route returns `204` with the same blanket
> `allow: GET, POST, PUT, PATCH, DELETE` header — it even claims `DELETE` on `/auth/v1/login`.
> That is CORS middleware, not route metadata. Don't use it to enumerate methods.

---

## Endpoint index

| Surface | Namespace | Live | Gated | Docs |
| :-- | :-- | --: | --: | :-- |
| `/apisite` | Users | 4 | 2 | [→](#users) |
| `/apisite` | Friends & presence | 5 | 3 | [→](#friends--presence) |
| `/apisite` | Avatar & thumbnails | 9 | 3 | [→](#avatar--thumbnails) |
| `/apisite` | Games | 9 | 1 | [→](#games) |
| `/apisite` | Catalog | 7 | 1 | [→](#catalog) |
| `/apisite` | Assets & economy | 4 | 2 | [→](#assets--economy) |
| `/apisite` | Inventory | 2 | 3 | [→](#inventory) |
| `/apisite` | Groups | 6 | 5 | [→](#groups) |
| `/apisite` | Badges | 2 | 0 | [→](#badges) |
| `/apisite` | Trades | 0 | 6 | [→](#trades) |
| `/apisite` | Chat & messages | 2 | 4 | [→](#chat--messages) |
| `/apisite` | Notifications & realtime | 3 | 1 | [→](#notifications--realtime) |
| `/apisite` | Account | 4 | 3 | [→](#account) |
| `/apisite` | Locale & metrics | 3 | 0 | [→](#locale--metrics) |
| `/apisite` | Auth | 0 | 3 | [→](#auth) |
| root | Legacy API | 15 | 8 | [→](#surface-2--root-host) |
| root | `/v1`, `/v2` | 14 | 10 | [→](#root-v1-and-v2) |

---

# Surface 1 — `/apisite`

```text
BASE   https://octane.wtf/apisite
AUTH   none for public paths
```

## Users

| Method | Path | Code | Notes |
| :-- | :-- | :-: | :-- |
| `GET` | `/users/v1/users/{userId}` | `200` | Full profile. |
| `GET` | `/users/v1/users/search?keyword={q}&limit=10` | `200` | Min 3 chars. Cursor = integer offset. |
| `GET` | `/users/v1/users/{userId}/status` | `200` | `{"status":null}` |
| `GET` | `/users/v1/users/{userId}/username-history` | `200` | Paginated. |
| `GET` | `/users/v1/users/authenticated` | `401` | Current user. |
| `POST` | `/users/v1/usernames/users` | `403` | Batch username → id. CSRF. |
| `PATCH` | `/users/v1/users/{userId}/display-names` | `405` | |

```bash
curl 'https://octane.wtf/apisite/users/v1/users/1'
```

```json
{"id":1,"name":"Octane","displayName":"Octane","description":"","created":"2026-09-21T17:50:24.821657Z",
 "isBanned":false,"hasVerifiedBadge":true,"isPremium":false,"hasAdminBadge":true,"hasStarCreatorBadge":false}
```

<sub>`/subscriptions`, `/configuration` and `/promotion-channels` return `404` — they do not exist.</sub>

## Friends & presence

| Method | Path | Code | Notes |
| :-- | :-- | :-: | :-- |
| `GET` | `/friends/v1/users/{userId}/friends` | `200` | |
| `GET` | `/friends/v1/users/{userId}/followers` | `200` | **Full follower list**, paginated. |
| `GET` | `/friends/v1/users/{userId}/followings` | `200` | **Full following list**, paginated. |
| `GET` | `/friends/v1/users/{userId}/followers/count` | `200` | |
| `GET` | `/friends/v1/users/{userId}/followings/count` | `200` | |
| `GET` | `/friends/v1/users/{userId}/friends/count` | `200` | `{"count":0}` |
| `GET` | `/friends/v1/users/{userId}/friends/statuses?userIds=` | `401` | |
| `GET` | `/friends/v1/user/friend-requests/count` | `401` | |
| `POST` | `/friends/v1/user/following-exists` | `405` | |
| `POST` | `/friends/v1/users/{userId}/unfriend` | `405` | Body `targetUserId`. **Modifies data.** |
| `POST` | `/presence/v1/presence/users` | `405` | Body `{"userIds":[1,2]}`. |

```bash
curl 'https://octane.wtf/apisite/friends/v1/users/1/followers?limit=10&sortOrder=Desc'
```

Each entry carries `isOnline`, `isDeleted`, `isBanned`, `presenceType`, `friendFrequentScore`.

## Avatar & thumbnails

| Method | Path | Code | Notes |
| :-- | :-- | :-: | :-- |
| `GET` | `/avatar/v1/users/{userId}/avatar` | `200` | R6/R15, scales, body colors, worn assets. |
| `GET` | `/avatar/v1/avatar-rules` | `200` | |
| `GET` | `/avatar/v1/avatar/metadata` | `200` | Feature flags. |
| `GET` | `/avatar/v1/users/{userId}/outfits` | `200` | Saved outfits. |
| `GET` | `/thumbnails/v1/users/avatar-headshot?userIds=1,2` | `200` | Comma-batched. |
| `GET` | `/thumbnails/v1/users/avatar?userIds=1,2` | `200` | Full body. |
| `GET` | `/thumbnails/v1/avatar/try-on?assetId=&userId=` | `200` | |
| `GET` | `/thumbnails/v1/games/icons?universeIds=` | `200` | |
| `GET` | `/thumbnails/v1/places/gameicons?placeIds=` | `200` | |
| `GET` | `/thumbnails/v1/badges/icons?badgeIds=` | `200` | |
| `GET` | `/thumbnails/v1/groups/icons?groupIds=` | `200` | |
| `GET` | `/thumbnails/v1/games/multiget/thumbnail?universeIds=` | `200` | |
| `GET` | `/avatar/v1/avatar` | `401` | Own avatar. |
| `GET` | `/avatar/v1/recent-items/all/list` | `401` | |
| `POST` | `/avatar/v1/avatar/redraw-thumbnail` | `405` | |
| `POST` | `/thumbnails/v1/batch` | `403` | Batched. CSRF. |

```bash
curl 'https://octane.wtf/apisite/thumbnails/v1/users/avatar?userIds=1'
# {"data":[{"targetId":1,"state":"Completed","imageUrl":"https://tcdn.octane.wtf/..."}]}
```

## Games

| Method | Path | Code | Notes |
| :-- | :-- | :-: | :-- |
| `GET` | `/games/v1/games?universeIds={ids}` | `200` | |
| `GET` | `/games/v1/games/list?keyword={q}&maxRows=25` | `200` | Returns `universeId` **and** `placeId`. |
| `GET` | `/games/v1/games/sorts` | `200` | `popular`, `mostFavorited`, … |
| `GET` | `/games/v1/games/votes?universeIds={ids}` | `200` | |
| `GET` | `/games/v1/games/{universeId}/favorites` | `200` | |
| `GET` | `/games/v1/games/{universeId}/social-links/list` | `200` | |
| `GET` | `/games/v2/games/{universeId}/media` | `200` | |
| `GET` | `/games/v1/games/multiget-place-details?placeIds={ids}` | `200` | Builder, price, playability. |
| `GET` | `/games/v1/games/multiget-playability-status?universeIds=` | `200` | |
| `GET` | `/develop/v1/universes/{universeId}/places` | `200` | |
| `GET` | `/develop/v1/universes/{universeId}` | `200` | `rootPlaceId`, `privacyType`. |
| `GET` | `/develop/v1/universes/{universeId}/teamcreate` | `200` | `{"isEnabled":false}` |
| `GET` | `/develop/v1/universes/{universeId}/icon` | `200` | `{"imageId":null,"isApproved":true}` |
| `GET` | `/develop/v1/universes/{universeId}/thumbnails` | `401` | |
| `GET` | `/develop/v1/places/{placeId}` | `405` | |
| `POST` | `/gamejoin/v1/join-game` | `405` | **New namespace.** |
| `POST` | `/gamejoin/v1/join-game-instance` | `405` | |
| `GET` | `/games/v1/games/recommendations/game/{universeId}` | `401` | |

```bash
curl 'https://octane.wtf/apisite/games/v1/games/votes?universeIds=6782'
# {"data":[{"id":6782,"upVotes":0,"downVotes":2}]}
```

<sub>`/games/{u}/servers`, `/games/servers/public`, `/games/{u}/private-servers` → `404`.</sub>

## Catalog

| Method | Path | Code | Notes |
| :-- | :-- | :-: | :-- |
| `GET` | `/catalog/v1/search/items?keyword={q}&limit=10` | `200` | IDs only. |
| `GET` | `/catalog/v1/search/items/details?keyword={q}` | `200` | **Full item data inline.** |
| `GET` | `/catalog/v2/search/items/details?keyword={q}` | `200` | **v2** variant. |
| `GET` | `/catalog/v1/categories` | `200` | 16 categories → ids. |
| `GET` | `/catalog/v1/subcategories` | `200` | |
| `GET` | `/catalog/v1/catalog/metadata` | `200` | Feature flags. |
| `GET` | `/catalog/v1/recommendations/metadata` | `200` | |
| `GET` | `/catalog/v1/search/navigation-menu-items` | `200` | |
| `GET` | `/catalog/v1/bundles/{bundleId}/details` | `400` | Real route — `"Invalid bundle"`, not `Not found`. |
| `POST` | `/catalog/v1/catalog/items/details` | `405` | `allow: POST`. Body `{"items":[{"itemType":"Asset","id":1}]}` |

```bash
curl 'https://octane.wtf/apisite/catalog/v1/search/items/details?keyword=hat&limit=5'
```

<sub>`search/items/details` skips the `POST` round trip — item names and descriptions come back inline.</sub>

## Assets & economy

| Method | Path | Code | Notes |
| :-- | :-- | :-: | :-- |
| `GET` | `/assetdelivery/v1/asset/?id={assetId}` | `200` | **Binary** `<roblox!` file. 302 → `cdn.octane.wtf`. |
| `GET` | `/economy/v2/assets/{assetId}/details` | `200` | Full record incl. `PriceInRobux`. |
| `GET` | `/economy/v1/assets/{assetId}/resellers` | `200` | Prices and serials. |
| `GET` | `/economy/v1/assets/{assetId}/details` | `200` | |
| `GET` | `/economy/v1/users/{userId}/currency` | `403` | Restricted. |
| `GET` | `/inventory/v2/assets/{assetId}/owners` | `200` | Owners + `serialNumber`, cursor. |
| `POST` | `/economy/v1/purchases/products/{productId}` | — | **Modifies data.** |

```bash
curl -s 'https://octane.wtf/apisite/assetdelivery/v1/asset/?id=121969' -o asset.rbxm
file asset.rbxm    # Roblox binary model
```

<sub>The only endpoint that returns something other than JSON.</sub>

## Inventory

| Method | Path | Code | Notes |
| :-- | :-- | :-: | :-- |
| `GET` | `/inventory/v1/users/{userId}/items/Asset/{assetId}` | `200` | Ownership check. |
| `GET` | `/inventory/v2/assets/{assetId}/owners` | `200` | |
| `GET` | `/inventory/v1/users/{userId}/assets/collectibles` | `403` | Owner-only. |
| `GET` | `/inventory/v2/users/{userId}/inventory?assetTypes=` | `403` | Owner-only. |
| `GET` | `/economy/v2/users/{userId}/transactions` | `400` | Needs params. |
| `GET` | `/inventory/v1/assets/{assetId}` | `405` | |

## Groups

| Method | Path | Code | Notes |
| :-- | :-- | :-: | :-- |
| `GET` | `/groups/v1/groups/{groupId}` | `200` | |
| `GET` | `/groups/v1/groups/search?keyword={q}` | `200` | |
| `GET` | `/groups/v1/groups/metadata` | `200` | Limits and policy flags. |
| `GET` | `/groups/v1/groups/{groupId}/roles` | `200` | Roles + member counts. |
| `GET` | `/groups/v1/groups/{groupId}/users` | `200` | Members, paginated. |
| `GET` | `/groups/v1/groups/{groupId}/social-links` | `200` | e.g. Discord URL. |
| `GET` | `/groups/v1/groups/{groupId}/roles/{roleId}/users` | `200` | Members of one role. |
| `GET` | `/groups/v1/groups/{groupId}/audit-log?limit=10` | `403` | Permissions. |
| `GET` | `/groups/v1/groups/{groupId}/social-links/list` | `405` | |
| `GET` | `/groups/v2/users/{userId}/groups/roles` | `200` | A user's groups. |
| `GET` | `/groups/v1/groups/{groupId}/membership` | `401` | Own membership. |
| `GET` | `/groups/v1/groups/{groupId}/join-requests` | `403` | Permissions. |
| `GET` | `/groups/v1/groups/{groupId}/payouts` | `403` | Permissions. |
| `POST` | `/groups/v2/groups/{groupId}/wall/posts` | `403` | |
| `DELETE` | `/groups/v1/groups/{groupId}/users/{userId}` | `405` | **Modifies data.** |

```bash
curl 'https://octane.wtf/apisite/groups/v1/groups/search?keyword=octane&limit=5'
```

## Badges

| Method | Path | Code | Notes |
| :-- | :-- | :-: | :-- |
| `GET` | `/badges/v1/users/{userId}/badges` | `200` | Awarded badges. |
| `GET` | `/badges/v1/badges/{badgeId}` | `200` | Single badge by id. |
| `GET` | `/badges/v1/universes/{universeId}/badges?limit=10` | `200` | Needs real universe id. |

```bash
curl 'https://octane.wtf/apisite/badges/v1/universes/6782/badges?limit=5'
```

## Trades

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

## Chat & messages

| Method | Path | Code | Notes |
| :-- | :-- | :-: | :-- |
| `GET` | `/chat/v2/metadata` | `200` | Chat limits and config. |
| `GET` | `/privatemessages/v1/announcements/metadata` | `200` | `{"numOfAnnouncements":1}` |
| `GET` | `/contacts/v1/user/get-tags` | `200` | Returns `[]`. |
| `GET` | `/chat/v2/get-user-conversations` | `401` | **Chat ≠ private messages.** |
| `GET` | `/chat/v2/get-messages?conversationId=` | `401` | |
| `GET` | `/chat/v2/get-unread-conversation-count` | `401` | |
| `GET` | `/privatemessages/v1/messages` | `400` | Needs params. |
| `GET` | `/privatemessages/v1/messages/unread/count` | `500` | Route exists, crashes. |

## Notifications & realtime

| Method | Path | Code | Notes |
| :-- | :-- | :-: | :-- |
| `POST` | `/realtime/notifications/negotiate` | `200` | SignalR handshake. Unauthenticated. |
| `GET` | `/notifications/v2/notifications/get-settings` | `200` | |
| `GET` | `/metrics/v1/thumbnails/metadata` | `200` | |
| `POST` | `/metrics/v1/performance/measurements` | `405` | |
| `POST` | `/metrics/v1/performance/send-measurement` | `405` | |

```bash
curl 'https://octane.wtf/apisite/realtime/notifications/negotiate'
# {"Url":"/notifications","ConnectionToken":"…","ProtocolVersion":"1.5","TryWebSockets":true}
```

## Account

| Method | Path | Code | Notes |
| :-- | :-- | :-: | :-- |
| `GET` | `/accountsettings/v1/themes/types` | `200` | `{"data":["Light","Dark"]}` |
| `GET` | `/premiumfeatures/v1/users/{userId}/validate-membership` | `200` | Raw `0` / `1`. |
| `GET` | `/accountinformation/v1/birthdate` | `401` | **Not** `/users/v1`. |
| `GET` | `/accountinformation/v1/gender` | `401` | |
| `GET` | `/accountinformation/v1/phone` | `200` | `{"phone":null,"isVerified":false,…}` |
| `GET` | `/accountinformation/v1/metadata` | `200` | Account feature flags. |
| `GET` | `/accountinformation/v1/description` | `401` | |
| `GET` | `/accountinformation/v1/promotion-channels` | `401` | |
| `POST` | `/accountinformation/v1/username` | `405` | |
| `GET` | `/accountsettings/v1/themes/user` | `401` | |

```bash
curl 'https://octane.wtf/apisite/premiumfeatures/v1/users/1/validate-membership'
# 0
```

## Locale & metrics

| Method | Path | Code |
| :-- | :-- | :-: |
| `GET` | `/locale/v1/locales` | `200` |
| `GET` | `/locale/v1/locales/user-localization-locus-supported-locales` | `200` |
| `GET` | `/metrics/v1/thumbnails/metadata` | `200` |

## Auth

Present, undocumented, not for automation.

| Method | Path | Code |
| :-- | :-- | :-: |
| `GET`/`POST` | `/auth/v1/login` | `405` on GET |
| `GET`/`POST` | `/auth/v2/login` | `405` on GET |
| `POST` | `/auth/v1/logout` | `405` on GET · **this is the sign-out endpoint** |

---

# Surface 2 — Root host

> A **second, parallel API surface** with a different payload shape. Previously undocumented.

| Method | Path | Code | Notes |
| :-- | :-- | :-: | :-- |
| `GET` | `/users/{userId}` | `200` | `{"Id","Username","AvatarUri","Online"}` |
| `GET` | `/users/inventory/list-json?userId={id}` | `200` | |
| `GET` | `/users/favorites/list-json?userId={id}` | `200` | |
| `GET` | `/users/profile/robloxcollections-json?userId={id}` | `200` | |
| `GET` | `/search/users/results?keyword={q}` | `200` | Legacy search shape. |
| `GET` | `/search/users/metadata` | `200` | Search config. |
| `GET` | `/universes/get-universe-places?universeId={id}` | `200` | |
| `GET` | `/games/getgameinstancesjson?placeId={id}` | `200` | Live server instances. |
| `GET` | `/comments/get-json?assetId={id}` | `200` | Asset comments. |
| `GET` | `/avatar-thumbnail-3d/json?userId={id}` | `200` | **3D avatar manifest.** |
| `GET` | `/mobileapi/check-app-version?appVersion={v}` | `200` | |
| `GET` | `/usercheck/show-tos` | `200` | |
| `GET` | `/version` | `200` | Plain text `2`. |
| `GET` | `/games/votingservice/{id}` | `200` | HTML fragment. |
| `GET` | `/account/settings/countries` | `200` | |

Gated: `/my/settings/json` `401` · `/referral/v1/me` `401` · `/asset/profile-status` `401` ·
`/build/universes` `401` · `/game/get-join-script` `401` · `/games/getfriendsgameinstances` `401` ·
`/private-server/instance-list-json` `403`

```bash
curl 'https://octane.wtf/users/1'
curl 'https://octane.wtf/avatar-thumbnail-3d/json?userId=1'
```

## Root `/v1` and `/v2`

| Method | Path | Code | Notes |
| :-- | :-- | :-: | :-- |
| `GET` | `/v1/users/{userId}` | `200` | |
| `GET` | `/v1/users/{userId}/friends` | `200` | |
| `GET` | `/v1/users/{userId}/groups/roles` | `200` | |
| `GET` | `/v1/users/{userId}/username-history` | `200` | |
| `GET` | `/v1/users/{userId}/status` | `200` | |
| `GET` | `/v1/avatar-rules` | `200` | R6/R15 + scale ranges. |
| `GET` | `/v1/games/list` | `200` | |
| `GET` | `/v1/games/sorts` | `200` | |
| `GET` | `/v1/themes/types` | `200` | |
| `GET` | `/v1/privacy` | `200` | **Public here** — `401` under `/apisite`. |
| `GET` | `/v1/privacy/info` | `200` | **Public here.** |
| `GET` | `/v1/social/connected-providers` | `200` | **Public here.** |
| `GET` | `/v1/xbox/connection` | `200` | **Public here.** |
| `GET` | `/v2/notifications/get-settings` | `200` | |

```bash
curl 'https://octane.wtf/v1/privacy'
# {"isUnder13":false,"isPrivacyModeEnabled":false}
```

> [!TIP]
> Four routes — `privacy`, `privacy/info`, `social/connected-providers`, `xbox/connection` — are
> **public on the root host** but `401` under `/apisite`. Use the root form.

---

# Surface 3 — CDN

| Host | Serves | Example |
| :-- | :-- | :-- |
| `cdn.octane.wtf` | Binary asset files | `assetdelivery` 302s here |
| `tcdn.octane.wtf` | Thumbnails, 3D manifests | Returned inside thumbnail JSON |

Thumbnail JSON always hands back an absolute `tcdn` URL:

```json
{"targetId":1,"state":"Completed","imageUrl":"https://tcdn.octane.wtf/79ce…_thumbnail.png"}
```

---

# Mutation routes

> **38 POST / PATCH / DELETE routes, confirmed by `GET` returning `405` with an `allow` header.**
> All require a session. None were executed. The method in each row is the exact `allow` value.

| Path | `allow` | What it does |
| :-- | :-- | :-- |
| `/auth/v1/login` | `POST` | Sign in. |
| `/auth/v2/login` | `POST` | Sign in (v2). |
| `/auth/v1/logout` | `POST` | **Sign out.** |
| `/users/v1/users` | `POST` | Create account. |
| `/users/v1/usernames/users` | `POST` | Batch username → id lookup. |
| `/users/v1/users/{userId}/display-names` | `PATCH` | Change display name. |
| `/accountinformation/v1/username` | `POST` | Change username. |
| `/avatar/v1/avatar/set-wearing-assets` | `POST` | Set worn assets. |
| `/avatar/v1/avatar/assets/{assetId}/wear` | `POST` | Wear one asset. |
| `/avatar/v1/avatar/redraw-thumbnail` | `POST` | Regenerate avatar thumbnail. |
| `/avatar/v1/outfits/{outfitId}` | `PATCH` | Edit an outfit. |
| `/friends/v1/users/{userId}/follow` | `POST` | Follow a user. |
| `/friends/v1/users/{userId}/unfriend` | `POST` | Remove a friend. |
| `/friends/v1/user/following-exists` | `POST` | Check following relationship. |
| `/presence/v1/presence/users` | `POST` | Batch presence lookup. |
| `/presence/v1/presence/register-app-presence` | `POST` | Register presence. |
| `/groups/v1/groups/{groupId}/users/{userId}` | `DELETE`, `PATCH` | Remove or edit a member. |
| `/groups/v1/groups/{groupId}/description` | `PATCH`, `POST` | Edit group description. |
| `/groups/v1/groups/{groupId}/status` | `PATCH` | Edit group status. |
| `/groups/v1/groups/{groupId}/wall/posts` | `POST` | Post to the group wall. |
| `/trades/v1/trades/{tradeId}/accept` | `POST` | Accept a trade. |
| `/trades/v1/trades/{tradeId}/decline` | `POST` | Decline a trade. |
| `/trades/v1/trades/{tradeId}/counter` | `POST` | Counter a trade. |
| `/chat/v2/send-message` | `POST` | Send a chat message. |
| `/chat/v2/add-to-conversation` | `POST` | Add someone to a conversation. |
| `/chat/v2/remove-from-conversation` | `POST` | Remove someone. |
| `/chat/v2/update-user-typing-status` | `POST` | Typing indicator. |
| `/privatemessages/v1/messages/send` | `POST` | Send a private message. |
| `/economy/v1/purchases/products/{productId}` | `POST` | **Purchase a product.** |
| `/inventory/v1/assets/{assetId}` | `PATCH` | Edit an owned asset. |
| `/catalog/v1/catalog/items/details` | `POST` | Batch item details. |
| `/thumbnails/v1/batch` | `POST` | Batch thumbnails. |
| `/badges/v1/user/badges/{badgeId}` | `DELETE` | Remove a badge. |
| `/develop/v1/places/{placeId}` | `PATCH`, `POST` | Update a place. |
| `/gamejoin/v1/join-game` | `POST` | Join a game server. |
| `/gamejoin/v1/join-game-instance` | `POST` | Join a specific instance. |
| `/metrics/v1/performance/measurements` | `POST` | Submit telemetry. |
| `/metrics/v1/performance/send-measurement` | `POST` | Submit telemetry. |

```bash
# confirm a route yourself — 405 means it exists, 404 means it doesn't
curl -s -o /dev/null -w '%{http_code}\n' 'https://octane.wtf/apisite/auth/v1/logout'   # 405
curl -s -o /dev/null -w '%{http_code}\n' 'https://octane.wtf/apisite/auth/v1/nope'     # 404
```

> [!WARNING]
> These change data and were **not** executed. `economy/v1/purchases/products/{id}` spends
> currency, the trade routes move items, and `DELETE /badges/v1/user/badges/{id}` is destructive.

---

## Verification

Probed unauthenticated from a clean sandbox on **7 November 2026**.

| Metric | Value |
| :-- | :-- |
| Paths probed | **374** |
| Returned `200` | **83** |
| Confirmed mutation routes (`405`) | **38** |
| Gated (`401` / `403` / `400` / `500`) | **50** |
| Confirmed absent (`404`) | **27** |
| Confirmed absent (`404`) | **60** |
| Resolved on its own CDN | **2** |

Reproduce any row:

```bash
curl -s -o /dev/null -w '%{http_code}\n' 'https://octane.wtf/apisite/users/v1/users/1'
```

### Confirmed absent

These namespaces **do not exist** on `/apisite` — do not document them:

`adconfiguration` · `ageverification` · `assetpermissions` · `avatarshop` · `billing` ·
`buildersclub` · `contentstore` · `engage` · `ownership` · `permissions` · `policy` · `premium` ·
`toolbox` · `web` · `universes/v1` · `gameserver` · `gamejoin` · `localizationtables` ·
`translationroles` · `usermanagement` · `verifiedbadges` · `moderation` · `players/v1`

Also: **no GraphQL. No Swagger or OpenAPI. No websocket metadata.**

---

## Appendix

> **Not verified.** Third-party reference material. Host, method and response unknown for most.
> Do not rely on these without checking.

<details>
<summary><b>Account information and promotion channels</b> — 7 paths</summary>

`/v1/description` · `/v1/birthdate` · `/v1/gender` · `/v1/metadata` · `/v1/phone`
`/v1/promotion-channels` · `/v1/users/{userId}/promotion-channels`

</details>

<details>
<summary><b>Avatar</b> — 8 paths</summary>

`/v1/avatar` · `/v1/avatar-rules` · `/v1/avatar/metadata` · `/v1/recent-items/all/list`
`/v1/users/{userId}/avatar` · `POST /v1/avatar/assets/{assetId}/wear`
`POST /v1/avatar/set-wearing-assets` · `POST /v1/avatar/redraw-thumbnail`

</details>

<details>
<summary><b>Chat and contacts</b> — 9 paths</summary>

`/v2/get-messages` · `POST /v2/send-message` · `POST /v2/update-user-typing-status`
`/v2/metadata` · `/v2/get-unread-conversation-count` · `/v2/get-user-conversations`
`/v2/multi-get-latest-messages` · `POST /v1/user/get-tags` · `/v1/contacts/metadata`

</details>

<details>
<summary><b>Games</b> — 11 paths</summary>

`/v1/games` · `/v1/games/list` · `/v1/games/multiget-place-details`
`/v1/games/multiget-playability-status` · `/v1/games/sorts` · `/v1/games/votes`
`/v1/games/{universeId}/favorites` · `/v1/games/{universeId}/social-links/list`
`/v2/users/{userId}/games` · `/v2/groups/{groupId}/games` · `/v2/games/{universeId}/media`

</details>

<details>
<summary><b>Groups</b> — 11 paths</summary>

`/v1/groups/{groupId}` · `/v1/groups/{groupId}/membership` · `/v1/groups/{groupId}/roles`
`/v1/groups/{groupId}/roles/{roleId}/users` · `/v1/groups/{groupId}/social-links`
`/v1/groups/{groupId}/payouts` · `/v1/groups/metadata` · `/v1/groups/search`
`/v1/users/{userId}/groups/primary/role` · `/v1/users/{userId}/groups/roles`
`/v2/groups/{groupId}/wall/posts`

</details>

<details>
<summary><b>Friends, inventory, economy</b> — 7 paths</summary>

`/v1/users/{userId}/friends` · `POST /v1/users/{userId}/unfriend`
`/v1/users/{userId}/friends/statuses` · `/v1/users/{userId}/assets/collectibles`
`/v2/recommendations/{userId}` · `/v1/users/{userId}/currency`
`/v1/users/{userId}/revenue/summary/day`

</details>

<details>
<summary><b>Trades, premium, two-step, users</b> — 11 paths</summary>

`/v1/trades/inbound` · `/v1/trades/outbound` · `/v1/trades/inactive` · `/v1/trades/{tradeId}`
`POST /v1/trades/send` · `POST /v1/trades/{tradeId}/decline` · `/v1/users/{userId}/subscriptions`
`/v1/users/{userId}/configuration` · `/v1/users/{userId}`
`PATCH /v1/users/{userId}/display-names` · `POST /v1/users`

</details>

<details>
<summary><b>Themes and privacy</b> — 9 paths</summary>

`/v1/themes/types` · `/v1/themes/user` · `/v1/privacy` · `/v1/privacy/info`
`/v1/inventory-privacy` · `/v1/trade-privacy` · `/v1/trade-value`
`/v1/social/connected-providers` · `/v1/xbox/connection`

</details>


<details>
<summary><b>Absent namespaces — do not document</b> — 23 namespaces, 90+ paths</summary>

`adconfiguration` · `ageverification` · `assetpermissions` · `avatarshop` · `billing` ·
`buildersclub` · `contentstore` · `engage` · `ownership` · `permissions` · `policy` ·
`premium` · `toolbox` · `web` · `universes/v1` · `gameserver` · `localizationtables` ·
`translationroles` · `usermanagement` · `verifiedbadges` · `moderation` · `players/v1` ·
`devproducts` · `gamepasses`

Also confirmed absent: `avatar/v2/*`, `locale/v2/locales`, `thumbnails/v2/assets`,
`gamejoin/v1/join-script`, `users/v1/users/{id}/{followers,followings,badges,avatar}`,
`groups/v2/groups/{id}/membership`, `notifications/v1/*`, `premiumfeatures/v1/users/{id}/features`.

</details>

<details>
<summary><b>Legacy root paths</b> — 25 paths</summary>

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

## Related tooling

| File | Purpose |
| :-- | :-- |
| [`crawl.py`](crawl.py) | Resumable SQLite crawler for the full user index. |
| [`recon/verify.py`](recon/verify.py) | Re-probes every endpoint and prints a live status table. |
| [`recon/probe-round1.py`](recon/probe-round1.py) · [`round2`](recon/probe-round2.py) · [`probe-get.py`](recon/probe-get.py) | The discovery harnesses. |
| [`tools/make_banner.py`](tools/make_banner.py) · [`make_arch.py`](tools/make_arch.py) | Regenerate the artwork. |
| [`docs/discovery-01.md`](docs/discovery-01.md) · [`02`](docs/discovery-02.md) · [`03`](docs/discovery-03.md) · [`04`](docs/discovery-04.md) | How each round of endpoints was found. |
| [`docs/api-map.md`](docs/api-map.md) | Earlier scratch map, kept for reference. |

Re-verify the whole reference in one command:

```bash
python3 recon/verify.py
```

---

## Responsible use

<div align="center">

**Not affiliated with Octane or Roblox.**

Endpoints are undocumented, unsupported, and can change without warning.
Respect the site's terms, privacy settings, and rate limits.
Never publish session cookies, CSRF tokens, or personal data.

Nothing here was tested against a signed-in account. No state-changing request was made.

<a href="#octane-apis">Back to top</a>

</div>
