# Groups

Accessed via `rotur.groups`. Groups support members, roles, permissions, announcements, events, and tips.

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
  visibility: "PUBLIC",
  published: true,
});
```

## Tips

```ts
const tips = await rotur.groups.tips("mygrp");
await rotur.groups.sendTip("mygrp", 10);
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
