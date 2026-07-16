---
name: villager-trade
description: Logs a networking encounter from the VC Builders' Breakfast — who you met, and a note on what they're building — directly into the shared encounters table. This is the ONLY way to log a trade; the website has no form for it. Use when the user says "log a trade", "I just met someone", "add a trade", "log who I met at the breakfast", or "trade with [name]".
metadata:
  author: Love Ventures
  version: 1.0
  category: events
---

# Villager Trade

Log a person you just met at the breakfast, straight from your own Claude — the Minecraft-native version of a networking exchange (trading with a villager instead of swapping business cards). There is no form on the website for this; the site (`index.html`) only ever displays the live leaderboard, it never collects anything.

## CRITICAL: this posts to a public, shared table

The **Top Traders** leaderboard on the room's screen shows everyone how many trades each person has logged, live. **Always preview exactly what will be submitted and get an explicit yes before sending it.** Also: this is about a real person who isn't in this conversation to consent — don't log a trade for someone based on a guess about who they are; only log what the user actually tells you.

---

## Config (fill in once before sharing this skill with guests — same values as `compounding/SKILL.md`)

```
SUPABASE_URL = "https://wsyrlpnczabwvflvlkdg.supabase.co"
SUPABASE_ANON_KEY = "eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJpc3MiOiJzdXBhYmFzZSIsInJlZiI6IndzeXJscG5jemFid3ZmbHZsa2RnIiwicm9sZSI6ImFub24iLCJpYXQiOjE3ODM4NDY0NTcsImV4cCI6MjA5OTQyMjQ1N30.oyUwAxHzZnFJ24SD3GS14Jam5LfrcCykns_f-zeRf-8"
ROOM_CODE = "BREW-07"   # must match the ROOM_CODE constant in index.html for this event
```

---

## Step 0: Confirm the room code

Same join step as `/compounding` — ask which room code is on the event screen right now if it isn't already established earlier in this conversation. Don't guess or reuse an old one from a previous event.

## Step 1: Get who they met

Ask directly if it's not already clear from the conversation:

> "Who did you just meet?"

Required:
- **Their name**

Optional, but worth asking in one go rather than one field at a time:
- **Email** — "makes the follow-up real, but skip it if you don't have it"
- **Company / fund**
- **Role** — Partner, Founder, Ops, etc.
- **One-line note** — what they're building or working on, max 160 characters

## Step 2: Get the catcher's name

If the user's own name hasn't come up yet in this conversation, ask:

> "And what name should this trade show up under on the leaderboard?"

## Step 3: Preview before sending — mandatory

> Here's the trade I'll log:
> - **Room:** BREW-07
> - **Logged by:** Priya
> - **Met:** Alex Kim, Partner at Salica
> - **Note:** "Building an internal deal-scoring agent"
>
> Send it?

Do not proceed without a clear yes. If any field is a placeholder or clearly made up (e.g. "test", "asdf"), flag it back to the person rather than sending it — a fake row still triggers the post-event enrichment pipeline into Affinity/Notion/Slack.

## Step 4: Submit

```bash
curl -sS -X POST "$SUPABASE_URL/rest/v1/encounters" \
  -H "apikey: $SUPABASE_ANON_KEY" \
  -H "Authorization: Bearer $SUPABASE_ANON_KEY" \
  -H "Content-Type: application/json" \
  -H "Prefer: return=minimal" \
  -d '{
    "room_code": "BREW-07",
    "catcher_name": "Priya",
    "target_name": "Alex Kim",
    "target_email": "alex@salica.vc",
    "target_company": "Salica",
    "target_role": "Partner",
    "note": "Building an internal deal-scoring agent"
  }'
```

Omit `target_email`/`target_company`/`target_role`/`note` entirely (don't send empty strings) if the person didn't give them — the table allows them to be blank.

## Step 5: Confirm

Tell them it's logged and, if they're curious, that it'll show up on the **Top Traders** leaderboard on the event screen within a few seconds — and on their own `profile.html?name=<their name>` account page, which tracks trades alongside anything they've built. No separate scorecard link for a trade on its own — that's only generated when they run `/compounding` (see `compounding/SKILL.md`).

---

## If this environment can't reach Supabase directly

Same situation as `/compounding` — don't dead-end, and don't try to build a URL with the fields as query parameters (there's no page on the site that accepts that). Instead, encode it as a redeem code:

```bash
python3 -c "
import json, base64
payload = {
    'k': 'trade',
    'r': 'BREW-07',
    'c': 'Priya',
    'tn': 'Alex Kim',
    'te': 'alex@salica.vc',
    'tc': 'Salica',
    'tr': 'Partner',
    'no': 'Building an internal deal-scoring agent'
}
print(base64.b64encode(json.dumps(payload).encode('utf-8')).decode('ascii'))
"
```

Omit any key entirely from `payload` that the person didn't give (don't include it as an empty string). Give them the printed code:

> "This environment can't reach Supabase directly, but nothing's lost — here's your redeem code. Paste it into the **Redeem a Code** box near the bottom of the room's page:"
>
> `<the printed code>`

The website decodes it, shows the same preview, and only submits once they confirm there — same trust model as submitting directly.

---

## Error handling

| Problem | What it means | What to do |
|---|---|---|
| HTTP 401 / "permission denied for table" | Wrong anon key, or the anon role hasn't been granted table access (see `schema.sql`) | Don't guess a replacement key — flag it |
| HTTP 42501 / RLS error | Insert policy missing on `encounters` | Tell the person to confirm with Orissa |
| Person can't remember the other person's name correctly | — | Log it as best-known; the post-event enrichment skill flags low-confidence matches rather than guessing further |
