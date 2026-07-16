# /loot-scan

> Build things that build things.

A one-shot, no-questions scan of your own Claude Code environment that turns what you've actually built into a fun character card — then drops it straight onto the shared live table at the VC Builders' Breakfast.

## What it does

1. **Confirms the room code** (sent to you by email before the event — also shown on the event screen).
2. **Scans silently** — your `CLAUDE.md`, custom commands, memory, integrations, git history, and any sibling projects. No file bodies get opened beyond the first ~20 lines of `CLAUDE.md`; everything else is counts, names, and timestamps only — see "Privacy" below.
3. **Scores six dimensions** (Crafting Table, Furnace, Redstone Circuit, Ender Eye, Bookshelf, Render Distance) and assigns one of twelve Minecraft-flavored characters (The Elder Guardian, The Phantom, The Creeper, and so on).
4. **Presents one card** — the character, the bars with plain-English definitions, why you got that card, a couple of specific "fun finds," two concrete suggestions for what to build next, and — if there's a good fit — a nudge toward someone else already at the table worth talking to. The live room link and your personal account link are included immediately, right alongside the card.
5. **Shows you exactly what's about to be submitted and asks first.** Nothing goes to the shared table without an explicit yes.
6. **Submits it**, or — if this environment can't reach the internet — hands you a short code to paste into the **Redeem A Code** step instead. Either way, nothing is lost.
7. **Writes you an offline HTML scorecard file**, a personal keepsake that works with zero network access.

## Setup (one-time, before the event)

Open `SKILL.md` and fill in the three values near the top:

```
SUPABASE_URL = ""
SUPABASE_ANON_KEY = ""
ROOM_CODE = "BREW-07"
```

Same three values used everywhere else in this project. Nothing here is secret — the anon key can only do what the site's database permissions allow.

## Installing it

Drop this whole `loot-scan` folder wherever your Claude looks for custom skills, then say `/loot-scan` (or "scan me," "submit my loot," "join the loot room").

## Privacy

- Scans metadata (counts, names, timestamps) — not file contents, deal specifics, secrets, or credentials.
- Never quotes commit messages verbatim, never opens `.env` files or anything inside a folder that looks like client/deal/portfolio data.
- Always previews before posting anything publicly, and only ever references what's already visible on the shared table when suggesting who else to talk to.

## Links you'll get back

- The live room: `https://orissa-afk.github.io/minecards/`
- Your account for the whole event: `profile.html?name=<you>`
- This specific build's card (once submitted): `card.html?id=<...>`
