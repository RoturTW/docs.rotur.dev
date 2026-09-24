# What is Claw

Claw is Rotur's social network. Users write short posts, reply, like, repost and follow each other, and every post goes into a single public feed ordered by time, with no ranking algorithm. Rotur client operating systems use Claw to add social features.

Claw is part of the main Rotur API at `https://api.rotur.dev`, and every action is a plain HTTP request, so you can build a Claw client in anything that can make a web request. For live updates, the `/claw/ws` WebSocket pushes new posts, edits, likes and deletions as they happen.

See [API endpoints](api-endpoints/README.md) for the full reference.
