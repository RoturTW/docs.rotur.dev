# Groups

`rotur.groups` manages groups and their members, roles, invites, join requests, announcements, events, tips, products, and bans. `top()`, `announcements()`, and `productOwnership()` need no token; every other method does.

Every method takes the group's tag as `grouptag`. Groups are returned as `GroupPublic`: `{ id, tag, name, description, readme, rules, icon_url, banner_url, owner_user_id, public, join_policy, entry_fee, created_at, credits_balance, member_count }`. `join_policy` is `"OPEN"`, `"REQUEST"`, or `"INVITE"`.

Member methods (`kick`, `ban`, `assignRole`, and so on) take a user ID, not a username.

## rotur.groups.mine()

Lists the groups you are in.

**Auth:** Required. Sub-tokens need `groups:view`.

```ts
const groups = await rotur.groups.mine();
```

**Returns:** `GroupPublic[]`

## rotur.groups.search(query)

Searches groups.

**Auth:** Required. Sub-tokens need `groups:view`.

```ts
const groups = await rotur.groups.search("gaming");
```

**Returns:** `GroupPublic[]`

## rotur.groups.top(limit?)

Lists top groups. `limit` defaults to `10`.

**Auth:** None.

```ts
const groups = await rotur.groups.top(10);
```

**Returns:** `GroupPublic[]`

## rotur.groups.get(grouptag)

Gets a group.

**Auth:** Required. No specific permission.

```ts
const group = await rotur.groups.get("mygrp");
```

**Returns:** `GroupPublic`

## rotur.groups.create(tag, name, options?)

Creates a group.

**Auth:** Required. Sub-tokens need `groups:manage`.

| Name | Type | Required | Description |
| --- | --- | --- | --- |
| `tag` | string | Yes | Group tag |
| `name` | string | Yes | Display name |
| `options.description` | string | No | Description |
| `options.iconUrl` | string | No | Icon URL |
| `options.bannerUrl` | string | No | Banner URL |
| `options.public` | boolean | No | Whether the group is public |
| `options.joinPolicy` | string | No | `"OPEN"`, `"REQUEST"`, or `"INVITE"` |

```ts
const group = await rotur.groups.create("mygrp", "My Group", {
  description: "A group for stuff",
  public: true,
  joinPolicy: "OPEN",
});
```

**Returns:** `GroupPublic`

## rotur.groups.update(grouptag, updates)

Changes group fields. `updates` is sent as the request body.

**Auth:** Required. Sub-tokens need `groups:manage`.

```ts
const group = await rotur.groups.update("mygrp", { description: "New description" });
```

**Returns:** `GroupPublic`

## rotur.groups.uploadIcon(grouptag, file)

Uploads a group icon from a `Blob`.

**Auth:** Required. Not listed in `METHOD_PERMISSIONS`.

```ts
const { icon_url } = await rotur.groups.uploadIcon("mygrp", file);
```

**Returns:** `{ message, icon_url }`

## rotur.groups.uploadBanner(grouptag, file)

Uploads a group banner from a `Blob`.

**Auth:** Required. Not listed in `METHOD_PERMISSIONS`.

```ts
const { banner_url } = await rotur.groups.uploadBanner("mygrp", file);
```

**Returns:** `{ message, banner_url }`

## rotur.groups.delete(grouptag)

Deletes a group.

**Auth:** Required. Sub-tokens need `groups:manage`.

```ts
await rotur.groups.delete("mygrp");
```

**Returns:** `{ message }`

## rotur.groups.transferOwnership(grouptag, userId)

Makes another member the owner of the group.

**Auth:** Required. Sub-tokens need `groups:manage`.

```ts
await rotur.groups.transferOwnership("mygrp", "user-id");
```

**Returns:** `{ message, group }`

## rotur.groups.join(grouptag)

Joins a group.

**Auth:** Required. Sub-tokens need `groups:join`.

```ts
await rotur.groups.join("mygrp");
```

**Returns:** `GroupPublic`

## rotur.groups.requestJoin(grouptag, message?)

Asks to join a group whose join policy is `REQUEST`.

**Auth:** Required. Sub-tokens need `groups:join`.

```ts
await rotur.groups.requestJoin("mygrp", "Hi, I'd like to join");
```

**Returns:** `{ id, group_tag, user_id, username, message, status, created_at }`. `status` is `"PENDING"`, `"ACCEPTED"`, or `"DECLINED"`.

## rotur.groups.leave(grouptag)

Leaves a group.

**Auth:** Required. Sub-tokens need `groups:leave`.

```ts
await rotur.groups.leave("mygrp");
```

**Returns:** `GroupPublic`

## rotur.groups.represent(grouptag)

Shows the group on your profile.

**Auth:** Required. Sub-tokens need `account:settings`.

```ts
await rotur.groups.represent("mygrp");
```

**Returns:** `{ message }`

## rotur.groups.disrepresent(grouptag)

Stops showing the group on your profile.

**Auth:** Required. Sub-tokens need `account:settings`.

```ts
await rotur.groups.disrepresent("mygrp");
```

**Returns:** `{ message }`

## rotur.groups.report(grouptag)

Reports a group.

**Auth:** Required. Sub-tokens need `groups:view`.

```ts
await rotur.groups.report("mygrp");
```

**Returns:** `{ message }`

## rotur.groups.members(grouptag, options?)

Lists a group's members, one page at a time.

**Auth:** Required. Sub-tokens need `groups:members.view`.

| Option | Type | Description |
| --- | --- | --- |
| `page` | number | Page number |
| `perPage` | number | Members per page |
| `search` | string | Filter by username |

```ts
const { members, page, pages, total } = await rotur.groups.members("mygrp", {
  page: 1,
  perPage: 50,
  search: "ali",
});
```

**Returns:** `{ members, page, per_page, total, pages }`. Each member is `{ id, group_tag, user_id, username, role_ids, joined_at, muted_announcements }`.

## rotur.groups.memberInfo(grouptag, userId)

Gets one member with their roles, permissions, and benefits.

**Auth:** Required. Sub-tokens need `groups:members.view`.

```ts
const { member, roles, permissions, benefits } = await rotur.groups.memberInfo("mygrp", "user-id");
```

**Returns:** `{ member, roles, permissions: string[], benefits: string[] }`

## rotur.groups.kick(grouptag, userId)

Removes a member from the group.

**Auth:** Required. Sub-tokens need `groups:manage`.

```ts
await rotur.groups.kick("mygrp", "user-id");
```

**Returns:** `{ message, group }`

## rotur.groups.ban(grouptag, userId, reason?)

Bans a user from the group.

**Auth:** Required. Sub-tokens need `groups:manage`.

```ts
const { ban } = await rotur.groups.ban("mygrp", "user-id", "Spamming");
```

**Returns:** `{ message, ban }`. `ban` is `{ id, group_tag, user_id, username, banned_by_id, banned_by, reason, created_at }`.

## rotur.groups.unban(grouptag, userId)

Lifts a ban.

**Auth:** Required. Sub-tokens need `groups:manage`.

```ts
await rotur.groups.unban("mygrp", "user-id");
```

**Returns:** `{ message }`

## rotur.groups.bans(grouptag)

Lists the group's bans.

**Auth:** Required. Sub-tokens need `groups:manage`.

```ts
const bans = await rotur.groups.bans("mygrp");
```

**Returns:** an array of bans, shaped like `ban` above.

## rotur.groups.checkBan(grouptag, userId)

Checks whether a user is banned from the group.

**Auth:** Required. Sub-tokens need `groups:members.view`.

```ts
const { banned, ban } = await rotur.groups.checkBan("mygrp", "user-id");
```

**Returns:** `{ banned, ban? }`

## rotur.groups.invite(grouptag, username)

Invites a user to the group.

**Auth:** Required. Sub-tokens need `groups:invite`.

```ts
await rotur.groups.invite("mygrp", "alice");
```

**Returns:** `{ id, group_tag, from_user_id, from_username, to_user_id, to_username, status, created_at }`. `status` is `"PENDING"`, `"ACCEPTED"`, `"DECLINED"`, or `"REVOKED"`.

## rotur.groups.invites(grouptag)

Lists the group's invites.

**Auth:** Required. Sub-tokens need `groups:invite`.

```ts
const invites = await rotur.groups.invites("mygrp");
```

**Returns:** an array of invites, shaped like the `invite()` response.

## rotur.groups.revokeInvite(grouptag, inviteId)

Revokes an invite.

**Auth:** Required. Sub-tokens need `groups:invite`.

```ts
await rotur.groups.revokeInvite("mygrp", "invite-id");
```

**Returns:** `{ message }`

## rotur.groups.myInvites()

Lists invites sent to you.

**Auth:** Required. Sub-tokens need `groups:view`.

```ts
const invites = await rotur.groups.myInvites();
```

**Returns:** an array of invites, shaped like the `invite()` response.

## rotur.groups.acceptInvite(grouptag, inviteId)

Accepts an invite sent to you.

**Auth:** Required. Sub-tokens need `groups:join`.

```ts
await rotur.groups.acceptInvite("mygrp", "invite-id");
```

**Returns:** `{ message }`

## rotur.groups.declineInvite(grouptag, inviteId)

Declines an invite sent to you.

**Auth:** Required. Sub-tokens need `groups:join`.

```ts
await rotur.groups.declineInvite("mygrp", "invite-id");
```

**Returns:** `{ message }`

## rotur.groups.joinRequests(grouptag)

Lists requests to join the group.

**Auth:** Required. Sub-tokens need `groups:invite`.

```ts
const requests = await rotur.groups.joinRequests("mygrp");
```

**Returns:** an array of join requests, shaped like the `requestJoin()` response.

## rotur.groups.acceptJoinRequest(grouptag, requestId)

Accepts a join request.

**Auth:** Required. Sub-tokens need `groups:invite`.

```ts
await rotur.groups.acceptJoinRequest("mygrp", "request-id");
```

**Returns:** `{ message }`

## rotur.groups.declineJoinRequest(grouptag, requestId)

Declines a join request.

**Auth:** Required. Sub-tokens need `groups:invite`.

```ts
await rotur.groups.declineJoinRequest("mygrp", "request-id");
```

**Returns:** `{ message }`

## rotur.groups.roles(grouptag)

Lists the group's roles.

**Auth:** Required. Sub-tokens need `groups:view`.

```ts
const roles = await rotur.groups.roles("mygrp");
```

**Returns:** `GroupRole[]`: `{ id, group_tag, name, description, assign_on_join, self_assignable, benefits, permissions }`.

## rotur.groups.createRole(grouptag, role)

Creates a role. `role` takes any `GroupRole` fields.

**Auth:** Required. Sub-tokens need `groups:manage`.

```ts
const role = await rotur.groups.createRole("mygrp", {
  name: "Moderator",
  description: "Can moderate content",
  assign_on_join: false,
  self_assignable: false,
  benefits: ["mod_tools"],
  permissions: ["manage_posts"],
});
```

**Returns:** `GroupRole`

## rotur.groups.updateRole(grouptag, roleId, updates)

Changes a role.

**Auth:** Required. Sub-tokens need `groups:manage`.

```ts
await rotur.groups.updateRole("mygrp", "role-id", { name: "Admin" });
```

**Returns:** `GroupRole`

## rotur.groups.deleteRole(grouptag, roleId)

Deletes a role.

**Auth:** Required. Sub-tokens need `groups:manage`.

```ts
await rotur.groups.deleteRole("mygrp", "role-id");
```

**Returns:** `{ message }`

## rotur.groups.userRoles(grouptag, userId)

Lists a member's roles.

**Auth:** Required. Sub-tokens need `groups:view`.

```ts
const roles = await rotur.groups.userRoles("mygrp", "user-id");
```

**Returns:** `GroupRole[]`

## rotur.groups.userPermissions(grouptag, userId)

Lists a member's permissions from their roles.

**Auth:** Required. Sub-tokens need `groups:view`.

```ts
const permissions = await rotur.groups.userPermissions("mygrp", "user-id");
```

**Returns:** `string[]`

## rotur.groups.userBenefits(grouptag, userId)

Lists a member's benefits from their roles.

**Auth:** Required. Sub-tokens need `groups:view`.

```ts
const benefits = await rotur.groups.userBenefits("mygrp", "user-id");
```

**Returns:** `string[]`

## rotur.groups.assignRole(grouptag, userId, roleId)

Gives a member a role.

**Auth:** Required. Sub-tokens need `groups:manage`.

```ts
await rotur.groups.assignRole("mygrp", "user-id", "role-id");
```

**Returns:** `{ message }`

## rotur.groups.removeRole(grouptag, userId, roleId)

Takes a role away from a member.

**Auth:** Required. Sub-tokens need `groups:manage`.

```ts
await rotur.groups.removeRole("mygrp", "user-id", "role-id");
```

**Returns:** `{ message }`

## rotur.groups.announcements(grouptag)

Lists the group's announcements.

**Auth:** None.

```ts
const announcements = await rotur.groups.announcements("mygrp");
```

**Returns:** an array of `{ id, group_tag, title, body, author_user_id, created_at, ping_members }`.

## rotur.groups.createAnnouncement(grouptag, title, body, options?)

Posts an announcement. Set `options.pingMembers` to notify members.

**Auth:** Required. Sub-tokens need `groups:manage`.

```ts
await rotur.groups.createAnnouncement("mygrp", "Title", "Body text", {
  pingMembers: true,
});
```

**Returns:** the created announcement.

## rotur.groups.deleteAnnouncement(grouptag, id)

Deletes an announcement.

**Auth:** Required. Sub-tokens need `groups:manage`.

```ts
await rotur.groups.deleteAnnouncement("mygrp", "announcement-id");
```

**Returns:** `{ message }`

## rotur.groups.muteAnnouncements(grouptag)

Mutes the group's announcements for you.

**Auth:** Required. Sub-tokens need `groups:manage`.

```ts
await rotur.groups.muteAnnouncements("mygrp");
```

**Returns:** `{ message }`

## rotur.groups.events(grouptag)

Lists the group's events.

**Auth:** Required. Sub-tokens need `groups:view`.

```ts
const events = await rotur.groups.events("mygrp");
```

**Returns:** an array of `{ id, group_tag, title, description, start_time, end_time, location, visibility, created_by, published }`. `visibility` is `"MEMBERS"` or `"PUBLIC"`.

## rotur.groups.createEvent(grouptag, event)

Creates an event. `event` takes any event fields.

**Auth:** Required. Sub-tokens need `groups:manage`.

```ts
const event = await rotur.groups.createEvent("mygrp", {
  title: "Game night",
  description: "Weekly game night",
  start_time: Date.now(),
  end_time: Date.now() + 3600000,
  location: "Online",
  visibility: "PUBLIC",
  published: true,
});
```

**Returns:** the created event.

## rotur.groups.updateEvent(grouptag, eventId, updates)

Changes an event.

**Auth:** Required. Sub-tokens need `groups:manage`.

```ts
await rotur.groups.updateEvent("mygrp", "event-id", { title: "New title" });
```

**Returns:** the updated event.

## rotur.groups.deleteEvent(grouptag, eventId)

Deletes an event.

**Auth:** Required. Sub-tokens need `groups:manage`.

```ts
await rotur.groups.deleteEvent("mygrp", "event-id");
```

**Returns:** `{ message }`

## rotur.groups.tips(grouptag)

Lists tips sent to the group.

**Auth:** Required. Sub-tokens need `groups:view`.

```ts
const tips = await rotur.groups.tips("mygrp");
```

**Returns:** an array of `{ id, group_tag, from_user_id, from_username?, amount_credits, note?, created_at }`.

## rotur.groups.sendTip(grouptag, amount, note?)

Sends credits to the group.

**Auth:** Required. Sub-tokens need `credits:manage`.

```ts
await rotur.groups.sendTip("mygrp", 10, "Great community");
```

**Returns:** the created tip.

## rotur.groups.withdrawTip(grouptag, amount)

Withdraws credits from the group's tip balance.

**Auth:** Required. Sub-tokens need `credits:manage`.

```ts
const withdrawal = await rotur.groups.withdrawTip("mygrp", 50);
```

**Returns:** `{ id, group_tag, to_username, amount_credits, created_at }`

## rotur.groups.withdrawals(grouptag, limit?)

Lists withdrawals from the group's tip balance. `limit` defaults to `20`.

**Auth:** Required. Sub-tokens need `groups:view`.

```ts
const withdrawals = await rotur.groups.withdrawals("mygrp");
```

**Returns:** an array of withdrawals, shaped like the `withdrawTip()` response.

## rotur.groups.products(grouptag)

Lists the group's products. A product grants a role, either once or as a subscription.

**Auth:** Required. Sub-tokens need `groups:view`.

```ts
const products = await rotur.groups.products("mygrp");
```

**Returns:** an array of `{ id, group_tag, name, description, price_credits, subscription, role_granted_id?, role_name?, benefit_granted?, frequency?, period? }`.

## rotur.groups.createProduct(grouptag, product)

Creates a product.

**Auth:** Required. Sub-tokens need `groups:manage`.

| Field | Type | Required | Description |
| --- | --- | --- | --- |
| `name` | string | Yes | Product name |
| `priceCredits` | number | Yes | Price in credits |
| `roleId` | string | Yes | Role granted to buyers |
| `description` | string | No | Description |
| `subscription` | boolean | No | Charge on a schedule |
| `frequency` | number | No | Billing frequency |
| `period` | string | No | `"day"`, `"week"`, `"month"`, or `"year"` |

```ts
const product = await rotur.groups.createProduct("mygrp", {
  name: "VIP",
  priceCredits: 100,
  roleId: "role-id",
  subscription: true,
  frequency: 1,
  period: "month",
});
```

**Returns:** the created product.

## rotur.groups.deleteProduct(grouptag, productId)

Deletes a product.

**Auth:** Required. Sub-tokens need `groups:manage`.

```ts
await rotur.groups.deleteProduct("mygrp", "product-id");
```

**Returns:** `{ message }`

## rotur.groups.purchaseProduct(grouptag, productId)

Buys a product, or starts a subscription to it.

**Auth:** Required. Sub-tokens need `credits:manage`.

```ts
const { product, subscription } = await rotur.groups.purchaseProduct("mygrp", "product-id");
```

**Returns:** `{ message, product, subscription?, group }`

## rotur.groups.cancelProductSubscription(grouptag, productId)

Cancels your subscription to a product.

**Auth:** Required. Sub-tokens need `credits:manage`.

```ts
const { cancel_at } = await rotur.groups.cancelProductSubscription("mygrp", "product-id");
```

**Returns:** `{ status, cancel_at, subscription }`

## rotur.groups.productOwnership(grouptag, productId, username)

Checks whether a user owns a product.

**Auth:** None.

```ts
const { owned } = await rotur.groups.productOwnership("mygrp", "product-id", "alice");
```

**Returns:** `{ owned, username, group_tag, product, subscription? }`

## rotur.groups.myProductSubscriptions()

Lists your product subscriptions across all groups.

**Auth:** Required. Sub-tokens need `groups:view`.

```ts
const subscriptions = await rotur.groups.myProductSubscriptions();
```

**Returns:** an array of `{ id, group_tag, product_id, product_name, username, role_id, role_name, started_at, next_billing, cancel_at?, active }`.
