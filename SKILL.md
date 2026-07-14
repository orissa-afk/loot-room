---
name: loot-scan
description: Silently scans the person's own Claude Code environment (repo, CLAUDE.md, commands, memory, integrations), scores it across six dimensions, assigns a Minecraft-flavored character archetype, and presents one branded, screenshottable player card — then condenses the result into a loot card for the VC Builders' Breakfast Compounding Loot Room and submits it to the shared live table. Also generates a personal offline HTML scorecard file, suggests concrete improvements, and points out complementary builders already in the room. This is the ONLY way to add loot — the website has no data-entry form, it's spectator-only, like a Kahoot screen showing a game PIN. Use when the user runs "/loot-scan", or says "submit my loot", "add my build to the loot room", "join the loot room", "scan me", or "log my breakfast loot".
metadata:
  author: Love Ventures
  version: 5.0
  category: events
---

# /loot-scan

> Build things that build things.

One silent scan of the person's own Claude Code environment. No interview about what they built — you work that out yourself from what's actually there, score it, and hand back one branded card, a matching personal HTML file, a couple of concrete next steps, and — where useful — a nudge toward someone else in the room worth talking to. The only deliberate exception to "no questions" is a single confirm right before anything hits the public board (Step 9) — everything else is silent.

**Tone**: nerdy, warm, quietly delighted, a little concerned. The smartest person at the breakfast who's also the funniest. Genuinely impressed by what you find, but also asking "are you okay?" about the 1am commits. Zero corporate voice. A friend who read the whole git log and is now giving you an affectionate, specific roast — not a LinkedIn endorsement.

---

## CRITICAL: never expose anything sensitive

The scan touches a real environment that may hold real deal or client data. **Describe what they built and how it works, never the specific proprietary content.** No `.env` contents, API keys, tokens, secrets, company names from deal pipelines, portfolio details, fund names, deal sizes, or figures like ARR/valuations — even if the scan turns them up. This applies to the room-matchmaking step too (Step 8) — only ever describe what's already visible on the public board, never anything from someone else's private repo.

## CRITICAL: keep the scan shallow — metadata, not content

This isn't just about not repeating what's found — it's about not reading deeply enough to find it in the first place. Stay at the surface:

- **Never open the body of any file** except `CLAUDE.md`, and even then read only the first ~20 lines for tone/personality — not the whole thing.
- **Never open `.env`, credentials files, config files with secrets, or anything inside a folder that looks like deal, client, or portfolio data** (e.g. `deals/`, `portfolio/`, `clients/`) — for those, only note that the folder exists and roughly how many files it has, nothing else.
- **Domain signal comes from file and folder *names*, and commit message *subjects* only** — not from grepping inside file contents. A folder named `sourcing/` is enough of a signal; there's no need to open what's in it.
- **Commit messages**: read subjects only, never diffs. Never quote one verbatim in the output — paraphrase the pattern instead ("a run of late-night fix commits," not the actual message text).
- If anything about the environment looks unusually sensitive on its face (a folder literally named after a live deal, for instance), don't investigate further — just don't factor it into scoring rather than tiptoeing around describing it.

## CRITICAL: this still posts to a public, shared table

Everyone at the breakfast sees every submission live. The scan and card-building are silent, but **Step 9 (preview + explicit yes) is mandatory before anything is sent.**

## CRITICAL: always include the room link

The room link (and the profile link, once the name is known) must appear **twice**: immediately in Step 9's card message — don't wait for a submission to happen first, the name is already known by then — and again at the very end in Step 15. This has been missed before because it was only ever placed at the end of a long flow that sometimes doesn't get that far; putting it in Step 9 as well means it's never dependent on reaching the last step.

---

## Config (fill in once before sharing this skill with guests)

```
SUPABASE_URL = "https://wsyrlpnczabwvflvlkdg.supabase.co"
SUPABASE_ANON_KEY = "eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJpc3MiOiJzdXBhYmFzZSIsInJlZiI6IndzeXJscG5jemFid3ZmbHZsa2RnIiwicm9sZSI6ImFub24iLCJpYXQiOjE3ODM4NDY0NTcsImV4cCI6MjA5OTQyMjQ1N30.oyUwAxHzZnFJ24SD3GS14Jam5LfrcCykns_f-zeRf-8"
ROOM_CODE = "BREW-07"   # must match the ROOM_CODE constant in index.html for this event
```

Nothing secret about the URL or anon key — `schema.sql`'s row-level-security policies cap what it can do. Never put the Supabase **service_role** key here.

---

## Step 0: Confirm the room code

Ask, unless already established in this conversation:

> "What's today's room code? It should have been emailed to you before the event — check there (it's also shown on the event screen if you can't find the email)."

## Step 1: Scan — silent, no output yet

Use bash, glob, read, and grep — but stay shallow throughout (see the CRITICAL rule above). Collect everything below before saying or showing anything.

**Claude Code setup**
- `CLAUDE.md` — exists? line count? first ~20 lines only, for tone/personality — not the whole file.
- `.claude/commands/` — count of `.md` files, and read their *names* only.
- `.claude/settings.json` / `settings.local.json` — count MCP servers configured (names, not their auth details).
- Hooks — do any exist? Just a yes/no and rough count.
- Memory — a `.claude/memory/` folder or similar — file count only, don't open them.
- Auto-memory (e.g. `MEMORY.md`) — exists? line count only.

**The repo, if there is one**
- Total commits: `git rev-list --count HEAD`
- Age: date of the first commit
- Tracked file count
- Commit-time patterns: weekend-vs-weekday ratio, late-night ratio (~9pm–6am), busiest single day — all derivable from commit timestamps alone
- Last ~15 commit message *subjects* only (never diffs, never quoted verbatim later)
- Longest streak of consecutive days with at least one commit

**Sibling projects** — `ls -d ~/*/` or nearest equivalent, check each for `.git/` — name and commit count only, don't open any files inside.

**Domain signal** — from file/folder *names* and commit message *subjects* only, never by opening file contents to search inside them:
- Investment/VC: deal, memo, thesis, pipeline, portfolio, LP, IC, sourcing
- Product/Engineering: roadmap, sprint, backlog, feature, release, deploy
- Operations: workflow, process, SOP, runbook, playbook, compliance
- Research/Knowledge: sector, market, landscape, competitor, intelligence
- Sales/GTM: prospect, outreach, pipeline, CRM, lead, funnel
- Automation: cron, feed, webhook, agent, scan, monitor, ETL, hook
- Knowledge systems: pattern, learning, framework, memory, example

**Integrations** — names of MCP servers, external tools, databases, APIs referenced in config — not what they're used for or connected to.

**Builder vs. Operator signal** — >70% tracked files as source code + minimal workflow signal leans "ships code." Look for small seeds of the other side regardless, from file names alone.

**The weird stuff** — unusual file/folder/command names, README count — surface-level only, nothing that requires opening a file to notice. Becomes "fun finds" later.

If essentially nothing turns up, skip straight to the **New Spawn** card in Step 4 and don't submit anything.

## Step 2: Detect the domain

Pick the best fit: Investment/VC, Founder/Product, Engineering/DevOps, Operations, Research/Analysis, Sales/GTM, Personal Productivity, or General/Mixed.

## Step 3: Score six dimensions (0–100 each, internal — show only the bars, not the arithmetic)

**Crafting Table** (baseline setup) — `CLAUDE.md` exists +15; >100 lines +10; >300 lines +10. Commands exist +10; 5+ +10; 10+ +10. MCP servers exist +10; 2+ +10. Memory folder exists +10. Hooks exist +5.

**Furnace** (workflow — repeatable process) — clear recurring pattern +25; multiple distinct workflows +15; templates in use +20; recurring processes evidenced +20; consistent output patterns +10; real finished work product found +10.

**Redstone Circuit** (automation — fires on its own) — hooks/triggers +20; session automation +15; scheduled feeds +20; agent-like processes +20; cron/scheduled jobs +15; daily/weekly cadence +10.

**Ender Eye** (intel — sees what's coming) — email/calendar integration +20; CRM or similar tool +20; a database +20; 2+ databases +10; enrichment/API calls +15; document processing pipeline +15.

**Bookshelf** (knowledge — what's been learned and kept) — memory files exist +15; >10 files +15; >30 files +10; explicit "learnings" evidenced +20; reusable frameworks +15; system references its own past knowledge +15; visible iteration over time +10.

**Render Distance** (reach — scale and duration) — >50 commits +10; >100 +10; >500 +10; >1000 +10. Active >3 months +10; >6 months +10. Multiple related repos +15; clear doc structure +10; breadth across domains +15.

## Step 4: Gate check

At least ONE must be true: `CLAUDE.md` exists, 1+ custom command, 1+ clear workflow signal, or 10+ commits.

**If none hold**, present this and stop — don't submit anything:

```
┌──────────────────────────────────────────────────────────────────────────────┐
│                                                                              │
│                    C O M P O U N D I N G   L O O T   R O O M                │
│                        build things that build things                       │
│                                                                              │
└──────────────────────────────────────────────────────────────────────────────┘

                                 NEW SPAWN

    You're early. Like, spawned-ten-seconds-ago early.
    No CLAUDE.md, no commands, no patterns yet. That's
    fine — you ran /loot-scan, which means the
    instinct is already there.

    Quickstart:
    1. Create a CLAUDE.md (even 10 lines)
    2. Build one command that saves you time
    3. Run /loot-scan again

    Crafting Table   ░░░░░░░░░░░░░░░░░░░░    0
    Furnace          ░░░░░░░░░░░░░░░░░░░░    0
    Redstone Circuit ░░░░░░░░░░░░░░░░░░░░    0
    Ender Eye        ░░░░░░░░░░░░░░░░░░░░    0
    Bookshelf        ░░░░░░░░░░░░░░░░░░░░    0
    Render Distance  ░░░░░░░░░░░░░░░░░░░░    0

    Not a fit yet. That's a timestamp, not a verdict.
    Go build something, then come back.

┌──────────────────────────────────────────────────────────────────────────────┐
│                                /loot-scan                                  │
└──────────────────────────────────────────────────────────────────────────────┘
```

Then go straight to Step 15 — the room link is still owed to them even here, so they can watch the table while they go build something.

## Step 5: Get their name, and check if they're a returning builder

Ask, unless already established in this conversation:

> "And what name should this show up under?"

Then — using the anon key, which is read-only-safe for this — check for existing entries:

```bash
curl -sS "$SUPABASE_URL/rest/v1/loot?room_code=eq.$ROOM_CODE&select=builder,name,player_tag" \
  -H "apikey: $SUPABASE_ANON_KEY" -H "Authorization: Bearer $SUPABASE_ANON_KEY"
```

Compare the returned `builder` values to their name **case-insensitively** (so "Priya" and "priya" count as the same person). If one or more matches exist:

> "Welcome back — this'll be your Nth build tonight. Nice."

Use the exact casing they used the first time for this submission too, so everything keeps aggregating onto the same `profile.html` account rather than fragmenting into near-duplicate names. Don't block a second submission — multiple builds from the same person over the event are expected and good; see the note on this in the room's README if you want the full reasoning.

## Step 6: Determine a character

Pick exactly one, using real scan numbers and the detected domain — write a fresh 3–6 line paragraph each time, don't reuse a stock line verbatim.

| Character | Trigger | Vibe to land on |
|---|---|---|
| **The Elder Guardian** | all dims >70, or (all >50 and overall >85) | Disbelief at the scale/pace, land on "a masterclass hiding in a `.git` folder." |
| **The Redstone Engine** | all dims >50 (not Elder Guardian) | "You didn't build a tool, you built a thing that builds things." |
| **The Librarian** | Bookshelf >60, Furnace >50, Redstone Circuit <40 | "Your knowledge compounds. Your operations... don't. Yet." |
| **The Phantom** | Redstone Circuit >50, Bookshelf <40 | "Automated processes running. You might not remember all of them. Your system has a nightlife." |
| **The Skeleton** | low files, low commands, one precise workflow | "That's it. You brought a bow to a cathedral-building contest. Respect." |
| **The Creeper** | high diversity, commit bursts | "You don't iterate, you erupt. This is where the best stuff comes from." |
| **The Stonemason** | Crafting Table >70, Render Distance >40, Furnace <50 | "Infrastructure so clean you could eat off it. Have you actually used it yet?" |
| **The Enchanter** | Bookshelf >50, Furnace >40, Redstone Circuit <30 | "Your analysis would hold up at a conference. Your inbox would not hold up in court." |
| **The Farmer** | Furnace >60, Redstone Circuit <40, Bookshelf <40 | "You do the work nobody talks about but everybody depends on." |
| **The Beacon** | Redstone Circuit >60, Ender Eye >60 | "Your system knows things before you wake up. Please tell us you sleep." |
| **The Torch** | overall <25, passes gate | "Fresh terminal. Clean repo. Infinite potential." |
| **The Blacksmith** | >70% source code, minimal workflow | "You ship. You haven't pointed that at your own workflow yet. The moment you do — scary good." |

## Step 7: Generate a Player Tag

Format: `CHARACTER-STAT-HASH`
- **CHARACTER** — GUARDIAN, ENGINE, LIBRARIAN, PHANTOM, SKELETON, CREEPER, STONEMASON, ENCHANTER, FARMER, BEACON, TORCH, BLACKSMITH
- **STAT** — the single most impressive real number from the scan
- **HASH** — first 2 characters of `git rev-parse --short HEAD` (uppercase) if there's a repo, otherwise 2 random uppercase alphanumeric characters

## Step 8: Work out suggestions and a room match

**Two concrete suggestions** — look at the two lowest-scoring dimensions and turn each into one specific, doable next step, not generic advice:
- Low Bookshelf → "You've got no memory file — even a running `MEMORY.md` of decisions made would compound fast."
- Low Redstone Circuit → "Everything here is manual — a single hook on session-end would close the loop."
- Low Ender Eye → "No integrations detected — hooking up even one (calendar, a CRM) would change what this can see."
- (Adapt to whichever two are actually weakest — don't force these exact ones if others are lower.)

**Who else is in the room** — this is the part that ties back into why the breakfast exists. Query the shared board (same anon key, read-only):

```bash
curl -sS "$SUPABASE_URL/rest/v1/loot?room_code=eq.$ROOM_CODE&select=builder,name,type,description" \
  -H "apikey: $SUPABASE_ANON_KEY" -H "Authorization: Bearer $SUPABASE_ANON_KEY"
```

Look for 1–2 people whose `type` covers this person's *weakest* dimension (e.g. this person is low on Ender Eye → someone with `type: compass` in the room is worth a conversation) or whose domain looks complementary. Only ever reference what's already public on that board — the builder name, item name, class, and description that person already chose to put on the shared table. Don't say anything about the room match beyond that.

> "Might be worth finding **[name]** — they're the [class] over there ([item name]), which is exactly the gap you're carrying."

If nothing meaningfully complementary is in the room yet (early in the event, or truly nothing fits), skip this rather than forcing a weak match.

## Step 9: Present the card, suggestions, and match — one message

**The Player Card:**

```
┌──────────────────────────────────────────────────────────────────────────────┐
│                                                                              │
│                    C O M P O U N D I N G   L O O T   R O O M                │
│                        build things that build things                       │
│                                                                              │
└──────────────────────────────────────────────────────────────────────────────┘

                              THE [CHARACTER]

    "[3-6 line personalized paragraph, real scan numbers,
     domain-aware, ends on the character's punchline.]"

    Crafting Table   ████████████████████  100
    Furnace          ████████████████████  100
    Redstone Circuit ████████░░░░░░░░░░░░   40
    Ender Eye        ████░░░░░░░░░░░░░░░░   20
    Bookshelf        ██████████████░░░░░░   70
    Render Distance  ████████████░░░░░░░░   60

    [STAT] · [STAT] · [STAT] · [STAT]

    PLAYER TAG: [player tag from Step 7]

┌──────────────────────────────────────────────────────────────────────────────┐
│                                /loot-scan                                  │
└──────────────────────────────────────────────────────────────────────────────┘
```

Bars 20 chars wide, `█`/`░`, raw numbers (no `%`), ~78-char boxes, 4 spaces indent, spacious.

**Then, in the same message:**
- **WHAT THESE MEAN** — a one-line plain-English definition for each of the six bars, so nobody has to guess what they're being scored on:
  - *Crafting Table* — how much basic setup exists: a `CLAUDE.md`, custom commands, integrations.
  - *Furnace* — how repeatable your workflow is: patterns and templates you actually reuse.
  - *Redstone Circuit* — how much runs on its own without you triggering it by hand.
  - *Ender Eye* — how much this setup can see coming: integrations that surface information to you.
  - *Bookshelf* — how much gets remembered and reused rather than relearned each time.
  - *Render Distance* — scale and duration: how long this has been running and how far it reaches.
- **WHY THIS CARD** — one or two sentences connecting the character/rarity choice back to the actual numbers (e.g. "Furnace and Bookshelf both cleared 60, and nothing else did, which is exactly the Librarian shape"). This is what makes the card feel earned rather than random.
- **THINGS WE NOTICED** — 2–3 warm, specific, non-sensitive fun finds.
- **LEVEL UP** — the two suggestions from Step 8.
- **WHO TO FIND IN THE ROOM** — the match from Step 8, if there is one.
- **WHAT'S IN YOUR INVENTORY** — a checklist of what's actually wired up, honest about gaps.
- **THE ROOM, LIVE** — paste these two links, exactly, as their own lines, before the closing question. Don't skip this even though nothing's been submitted yet — the name is already known by this point, so there's no reason to wait:
  ```
  🔗 https://orissa-afk.github.io/minecards/
  🔗 https://orissa-afk.github.io/minecards/profile.html?name=<their name, URL-encoded>
  ```
- Close with: "Want me to send this to the room, and save you a copy?"

## Step 10: Preview + confirm — mandatory, the one real gate

Restate plainly what actually gets submitted, separate from the full card above:

> Here's what goes to the room:
> - **Room:** BREW-07 · **Builder:** Priya · **Item:** [name] · **Class:** [class] · **Rarity:** [rarity] · **Player Tag:** [tag]
> - **Description:** [one upbeat line, ≤160 chars, nothing sensitive]
>
> Send it?

**Class mapping** (dominant signal, independent of character):

| Class | Fits... |
|---|---|
| `compass` | Sourcing, signal-finding (high Ender Eye) |
| `redstone` | Automation (high Redstone Circuit) |
| `map` | Research, structured knowledge (high Bookshelf) |
| `anvil` | Forges scattered input into one output (high Furnace) |
| `potion` | Distills something messy into something simple (mixed/general) |

**Rarity mapping** (from character tier, always erring generous):
- Elder Guardian, Redstone Engine, Beacon → `Legendary`
- Librarian, Stonemason, Enchanter, Blacksmith (with seeds) → `Epic`
- Phantom, Skeleton, Creeper, Farmer, Blacksmith (no seeds) → `Rare`
- Torch → `Uncommon`, description still enthusiastic

Don't proceed without a clear yes.

## Step 11: Submit

```bash
curl -sS -X POST "$SUPABASE_URL/rest/v1/loot" \
  -H "apikey: $SUPABASE_ANON_KEY" \
  -H "Authorization: Bearer $SUPABASE_ANON_KEY" \
  -H "Content-Type: application/json" \
  -H "Prefer: return=representation" \
  -d '{
    "room_code": "BREW-07",
    "player_tag": "GUARDIAN-412-C6",
    "builder": "Priya",
    "name": "LP Update Anvil",
    "type": "anvil",
    "rarity": "Epic",
    "description": "Turns messy portfolio notes into a clean LP update draft in one pass."
  }'
```

Leave out `"stats"`. A successful response is a JSON array with the new row, including its `id`. If this succeeds, go to Step 13.

## Step 12: If the direct submission fails — generate a redeem code

If you see a connection/DNS/network error (not an HTTP response), encode it instead of losing it:

```bash
python3 -c "
import json, base64
payload = {
    'k': 'loot', 'r': 'BREW-07', 'p': 'GUARDIAN-412-C6', 'b': 'Priya',
    'n': 'LP Update Anvil', 't': 'anvil', 'y': 'Epic',
    'd': 'Turns messy portfolio notes into a clean LP update draft in one pass.'
}
print(base64.b64encode(json.dumps(payload).encode('utf-8')).decode('ascii'))
"
```

> "This environment can't reach Supabase directly — here's your redeem code, nothing's lost. Paste it into **Redeem A Code** on the room's page:"
> `<the printed code>`

## Step 13: Generate a personal offline HTML scorecard

Regardless of which submission path was used, always create a real local file — this works even with zero network access, since nothing about it needs Supabase.

Fill in this template (swap every `{{TOKEN}}`, insert the one matching SVG icon block from the reference set below, and drop in the bars/finds/suggestions already generated) and write it with your file-creation tool to something like `loot-scan-scorecard.html` in the current directory:

```html
<!doctype html>
<html lang="en"><head><meta charset="utf-8"><meta name="viewport" content="width=device-width, initial-scale=1">
<title>{{ITEM_NAME}} — Compounding Loot Room</title>
<style>
  :root { --ink:#241914; --paper:#fff7e7; --moss:#476944; --gold:#f4b84a; --rarity:{{RARITY_COLOR}}; }
  * { box-sizing: border-box; }
  body { margin:0; min-height:100vh; display:grid; place-items:start center; padding:24px;
    font-family: ui-monospace, Menlo, Consolas, monospace; color:var(--ink);
    background: linear-gradient(90deg, rgba(36,25,20,.05) 1px, transparent 1px), linear-gradient(rgba(36,25,20,.05) 1px, transparent 1px), #e7d1ad;
    background-size: 32px 32px; }
  .wrap { width:min(460px,100%); display:grid; gap:14px; }
  .card { border:5px solid var(--ink); background:var(--paper); box-shadow:10px 10px 0 rgba(36,25,20,.25); overflow:hidden; }
  .top { padding:16px; background:var(--rarity); border-bottom:4px solid var(--ink); }
  .top small { display:block; font-size:11px; font-weight:900; text-transform:uppercase; margin-bottom:6px; }
  .top h1 { margin:0; font-size:24px; }
  .body { padding:16px; display:grid; gap:12px; }
  .icon-wrap { display:grid; place-items:center; width:72px; height:72px; margin:0 auto; border:4px solid var(--ink); background:#f4c28a; }
  .icon { width:56px; height:56px; image-rendering:pixelated; }
  .chip { display:inline-block; padding:4px 8px; border:2px solid var(--ink); background:#fff; font-size:11px; font-weight:900; margin:2px; }
  .desc { text-align:center; font-size:13px; line-height:1.5; }
  .stat { display:grid; grid-template-columns:120px 1fr 34px; align-items:center; gap:7px; margin:4px 0; font-size:11px; font-weight:900; }
  .bar { height:12px; border:2px solid var(--ink); background:#e2d2b9; }
  .bar span { display:block; height:100%; background:var(--moss); }
  .section { border:3px solid var(--ink); background:rgba(36,25,20,.03); padding:12px; font-size:12px; line-height:1.6; }
  .section h2 { margin:0 0 8px; font-size:12px; text-transform:uppercase; }
  .tag { text-align:center; font-size:11px; opacity:.8; }
</style></head>
<body><div class="wrap">
  <div class="card">
    <div class="top"><small>{{RARITY}} {{CLASS_LABEL}} Item</small><h1>{{ITEM_NAME}}</h1></div>
    <div class="body">
      <div class="icon-wrap">{{ICON_SVG}}</div>
      <div style="text-align:center"><span class="chip">Built by {{BUILDER}}</span><span class="chip">{{CHARACTER}}</span></div>
      <p class="desc">{{DESCRIPTION}}</p>
      {{STAT_BARS_HTML}}
      <div class="tag">PLAYER TAG: {{PLAYER_TAG}}</div>
    </div>
  </div>
  <div class="section"><h2>Things We Noticed</h2>{{FUN_FINDS_HTML}}</div>
  <div class="section"><h2>Level Up</h2>{{SUGGESTIONS_HTML}}</div>
  {{ROOM_MATCH_SECTION_HTML}}
</div></body></html>
```

Build `{{STAT_BARS_HTML}}` as repeated `<div class="stat"><span>Label</span><div class="bar"><span style="width:VALUE%"></span></div><span>VALUE</span></div>` blocks. Build `{{FUN_FINDS_HTML}}`/`{{SUGGESTIONS_HTML}}` as short `<p>` per line. Only include `{{ROOM_MATCH_SECTION_HTML}}` (same `.section` shape, heading "Who To Find In The Room") if Step 8 actually found one. `{{RARITY_COLOR}}`: Uncommon `#57d8a5`, Rare `#4fa3ff`, Epic `#a971ff`, Legendary `#ffb13b`.

**Icon reference — insert the one matching `{{CLASS}}` verbatim as `{{ICON_SVG}}`:**

```
compass: <svg class="icon" viewBox="0 0 32 32"><path fill="#d9f1ff" d="M7 7h18v18H7z"/><path fill="#f4b84a" d="M12 12h8v8h-8z"/><path fill="#241914" d="M12 3h8v3h6v6h3v8h-3v6h-6v3h-8v-3H6v-6H3v-8h3V6h6zm-2 7v12h12V10z"/><path fill="#b4523a" d="M18 8h3l-4 9h-3z"/><path fill="#476944" d="M14 15h3l-4 9h-3z"/></svg>
redstone: <svg class="icon" viewBox="0 0 32 32"><path fill="#743025" d="M6 8h20v16H6z"/><path fill="#b4523a" d="M8 6h16v20H8z"/><path fill="#241914" d="M6 4h20v4h2v18h-4v2H6v-4H4V6h2zm2 4v16h16V8z"/><path fill="#ff5b48" d="M11 11h4v4h-4zm7 0h3v3h-3zm-2 6h5v4h-5zm-6 2h3v3h-3z"/></svg>
map: <svg class="icon" viewBox="0 0 32 32"><path fill="#f4d58c" d="M3 5h8v22H3z"/><path fill="#7fb069" d="M11 7h10v20H11z"/><path fill="#f4d58c" d="M21 5h8v22h-8z"/><path fill="#241914" d="M2 4h10v2H4v20h8v2H2zm9 2h2v20h-2zm9 0h2v20h-2zm1-2h9v24h-9v-2h7V6h-7z"/><path fill="#4c8eb8" d="M14 10h4v4h4v4h-6v-4h-2z"/><path fill="#b4523a" d="M6 9h3v3H6zm17 11h3v3h-3z"/></svg>
anvil: <svg class="icon" viewBox="0 0 32 32"><path fill="#5b6068" d="M5 10h22v6h-7v4h4v5H8v-5h4v-4H5z"/><path fill="#8a929c" d="M8 7h15v5H8z"/><path fill="#241914" d="M7 5h18v7h4v6h-7v2h4v7H6v-7h4v-2H3v-8h4zm2 4v3H7v2h22v-2h-6V9zm5 9v4h-4v1h12v-1h-4v-4z"/><path fill="#f4b84a" d="M22 6h4v3h-4z"/></svg>
potion: <svg class="icon" viewBox="0 0 32 32"><path fill="#d9f1ff" d="M13 4h6v7l5 7v8H8v-8l5-7z"/><path fill="#f47ad0" d="M9 19h14v6H9z"/><path fill="#a971ff" d="M11 16h10v4H11z"/><path fill="#241914" d="M11 2h10v4h-2v5l6 7v10H7V18l6-7V6h-2zm4 4v6l-5 7v6h12v-6l-5-7V6z"/><path fill="#fff7e7" d="M18 18h3v3h-3z"/></svg>
```

Tell them where the file landed and that it's theirs to keep — it works offline, doesn't need the room's Supabase project at all, and is safe to email to themselves or drag onto a desktop.

## Step 14: Give them their personal links

```
https://orissa-afk.github.io/minecards/card.html?id=<id>       (only if you have the id from a direct submission)
https://orissa-afk.github.io/minecards/profile.html?name=<their name, URL-encoded>
```

`profile.html` aggregates everything under that name for the whole event — every build, every trade, all together.

## Step 15: Always end with the room link — this is not optional

**Regardless of anything else — whether they submitted, hit New Spawn in Step 4, used the redeem-code fallback, or changed their mind at Step 10 — the literal last line of your reply must be:**

```
🔗 https://orissa-afk.github.io/minecards/
```

Not paraphrased, not summarized as "you can view the room on the website," not folded into a sentence — that exact line, on its own, at the very end. This is the one thing in this whole skill that must never get skipped or buried under other output.

---

## Error handling

| Problem | What it means | What to do |
|---|---|---|
| HTTP 401 | Wrong or missing anon key | Check `SUPABASE_ANON_KEY` — don't guess a replacement |
| HTTP 42501 / RLS error | Policies weren't run, or ran on a different project | Tell them to confirm with Orissa |
| Connection/DNS/network error | Can't reach the Supabase host | Go to Step 12 |
| `curl`/bash unavailable entirely | No code execution here at all | Say so plainly; still can't scan either, so nothing to fall back to |
| Nothing found in the scan | Environment's essentially empty | Step 4's New Spawn card, don't submit |
| Person changes their mind at Step 10 | — | Don't submit. The offline HTML file (Step 13) can still be made regardless |
