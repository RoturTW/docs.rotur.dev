# Groups

Accessed via `rotur.groups`. Groups support members, roles, permissions, announcements, events, tips, products, invites, and moderation.

## List Your Groups

```ts
const groups = await rotur.groups.mine();
```

## Search Groups

```ts
const groups = await rotur.groups.search("gaming");
```

## Create a Group

```ts
const group = await rotur.groups.create("mygrp", "My Group", {
  description: "A group for stuff",
  iconUrl: "https://example.com/icon.png",
  bannerUrl: "https://example.com/banner.png",
  public: true,
  joinPolicy: "OPEN",     // "OPEN" | "REQUEST" | "INVITE"
});
```

## Get / Update / Delete

```ts
const group = await rotur.groups.get("mygrp");
const updated = await rotur.groups.update("mygrp", { description: "New desc" });
await rotur.groups.delete("mygrp");
```

## Join / Leave

```ts
await rotur.groups.join("mygrp");
await rotur.groups.leave("mygrp");
```

If the group's join policy is `REQUEST`, ask to join instead:

```ts
await rotur.groups.requestJoin("mygrp", "Hi, I'd like to join!");
```

## Join Requests (Managers)

```ts
const requests = await rotur.groups.joinRequests("mygrp");
await rotur.groups.acceptJoinRequest("mygrp", "request-id");
await rotur.groups.declineJoinRequest("mygrp", "request-id");
```

## Invites

```ts
// Invite a user
await rotur.groups.invite("mygrp", "alice");

// List / revoke a group's invites
const invites = await rotur.groups.invites("mygrp");
await rotur.groups.revokeInvite("mygrp", "invite-id");

// Your own invites
const myInvites = await rotur.groups.myInvites();
await rotur.groups.acceptInvite("mygrp", "invite-id");
await rotur.groups.declineInvite("mygrp", "invite-id");
```

## Members

```ts
const { members, page, pages, total } = await rotur.groups.members("mygrp", {
  page: 1,
  perPage: 50,
  search: "ali",
});
```

## Represent / Disrepresent

Show the group on your profile:

```ts
await rotur.groups.represent("mygrp");
await rotur.groups.disrepresent("mygrp");
```

## Report

```ts
await rotur.groups.report("mygrp");
```

## Announcements

```ts
// List (public)
const announcements = await rotur.groups.announcements("mygrp");

// Create
const ann = await rotur.groups.createAnnouncement("mygrp", "Title", "Body text", {
  pingMembers: true,
});

// Delete
await rotur.groups.deleteAnnouncement("mygrp", "announcement-id");

// Mute
await rotur.groups.muteAnnouncements("mygrp");
```

## Events

```ts
const events = await rotur.groups.events("mygrp");

const event = await rotur.groups.createEvent("mygrp", {
  title: "Game Night",
  description: "Weekly game night",
  start_time: Date.now(),
  end_time: Date.now() + 3600000,
  location: "Online",
  visibility: "PUBLIC",   // "PUBLIC" | "MEMBERS"
  published: true,
});

await rotur.groups.updateEvent("mygrp", "event-id", { title: "New title" });
await rotur.groups.deleteEvent("mygrp", "event-id");
```

## Tips

```ts
const tips = await rotur.groups.tips("mygrp");
await rotur.groups.sendTip("mygrp", 10, "Great community!");
```

## Products

Groups can sell products that grant a role, either one-off or as a subscription:

```ts
// List a group's products
const products = await rotur.groups.products("mygrp");

// Create a product
const product = await rotur.groups.createProduct("mygrp", {
  name: "VIP",
  priceCredits: 100,
  roleId: "role-id",
  description: "VIP role access",
  subscription: true,
  frequency: 1,
  period: "month",   // "day" | "week" | "month" | "year"
});

// Buy / cancel
await rotur.groups.purchaseProduct("mygrp", "product-id");
await rotur.groups.cancelProductSubscription("mygrp", "product-id");

// Delete a product
await rotur.groups.deleteProduct("mygrp", "product-id");

// Check if a user owns a product (no auth required)
const { owned } = await rotur.groups.productOwnership("mygrp", "product-id", "alice");

// Your active product subscriptions across all groups
const subs = await rotur.groups.myProductSubscriptions();
```

## Roles & Permissions

```ts
// List roles
const roles = await rotur.groups.roles("mygrp");

// Create a role
const role = await rotur.groups.createRole("mygrp", {
  name: "Moderator",
  description: "Can moderate content",
  assign_on_join: false,
  self_assignable: false,
  benefits: ["mod_tools"],
  permissions: ["manage_posts"],
});

// Update / delete
await rotur.groups.updateRole("mygrp", "role-id", { name: "Admin" });
await rotur.groups.deleteRole("mygrp", "role-id");
```

## Member Roles

```ts
// Get a member's roles
const roles = await rotur.groups.userRoles("mygrp", "user-id");

// Get permissions/benefits
const perms = await rotur.groups.userPermissions("mygrp", "user-id");
const benefits = await rotur.groups.userBenefits("mygrp", "user-id");

// Assign / remove role
await rotur.groups.assignRole("mygrp", "user-id", "role-id");
await rotur.groups.removeRole("mygrp", "user-id", "role-id");
```

## Moderation

```ts
// Kick a member
await rotur.groups.kick("mygrp", "user-id");

// Ban / unban
await rotur.groups.ban("mygrp", "user-id", "Spamming");
await rotur.groups.unban("mygrp", "user-id");

// List bans
const bans = await rotur.groups.bans("mygrp");
```

## Transfer Ownership

```ts
await rotur.groups.transferOwnership("mygrp", "user-id");
```
