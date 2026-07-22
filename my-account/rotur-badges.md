# Rotur Badges

Badges are small icons shown on your profile for other people to see.

The server calculates your badges automatically. They are stored in the `sys.badges` array on your account and refreshed every time you log in. They also appear on your public profile. Each badge is an object with a `name`, an `icon` and a `description`.

## Automatic Badges

These are earned automatically based on your account:

| Badge | How to earn it |
|---|---|
| System badge | Shows the system your account was created on (for example originOS) |
| `rich` | Have 1,000 or more Rotur Credits |
| `friendly` | Have 10 or more friends on Rotur |
| `discord` | Link your Discord account to Rotur |
| `pro` | Have a Pro subscription or higher |

## Manually Granted Badges

Some badges are granted by hand, for example for members of the Rotur dev team, app developers, and creators of Rotur operating systems. These live in the badges JSON file below, along with their icon images. This makes it easy to parse and display the badges your user has.

{% @github-files/github-code-block url="https://github.com/RoturTW/Badges/blob/main/badges.json" %}
