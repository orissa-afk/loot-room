# villager-trade

> Trading with a villager instead of swapping business cards.

Logs someone you just met at the VC Builders' Breakfast — straight from your own Claude, mid-conversation, no forms and no business cards.

## What it does

1. **Confirms the room code** (sent to you by email before the event — also shown on the event screen).
2. **Asks who you met** — their name (required), and optionally their email, company, role, and a one-line note on what they're building. All in one go, not one field at a time.
3. **Asks what name to log it under** — yours, so it lands on your own `profile.html` account alongside anything you've built.
4. **Shows you exactly what's about to be submitted and asks first.** If anything looks like a placeholder or made-up test entry, it'll flag that back to you rather than silently sending it — a fake row still ends up getting "enriched" and pushed into real systems after the event.
5. **Submits it**, or — if this environment can't reach the internet — hands you a short code to paste into the **Redeem A Code** step instead. Either way, nothing is lost.
6. **Confirms it's logged**, tells you it'll show up on the **Top Traders** leaderboard within a few seconds, and gives you the live room link.

## Setup (one-time, before the event)

Open `SKILL.md` and fill in the same three values used everywhere else in this project:

```
SUPABASE_URL = ""
SUPABASE_ANON_KEY = ""
ROOM_CODE = "BREW-07"
```

## Installing it

Drop this whole `villager-trade` folder wherever your Claude looks for custom skills, then say something like "log a trade with Alex" or "I just met someone" whenever you've had a good conversation.

## A note on consent

The person you met isn't in this conversation to agree to being logged — so this only ever records what you actually tell your Claude, never a guess at who someone might be. Keep it to what you'd be comfortable saying to their face: a name, maybe contact details, and a line on what they're working on.

## What happens afterward

Trades don't just sit on a leaderboard — after the event, `encounter-enrichment/SKILL.md` verifies each one and pushes it into Affinity, Notion, and Slack from a trusted context, turning "logged a name at breakfast" into an actual usable contact.
