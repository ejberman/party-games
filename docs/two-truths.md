# Two Truths & a Lie

_Party Games · Features_

Two Truths & a Lie (labelled **Three Truths & a Lie** in the UI) is a social deduction party game. Every player writes four personal statements and secretly marks one as a lie. Then, one player at a time steps into the spotlight while everyone else tries to sniff out their lie. Good liars score big; good lie-detectors score too.

The game is **name-keyed** — if a player refreshes their browser, the server restores their record as long as their name stays the same. Statements, lie positions, and in-flight votes are kept in private server state and never sent to clients until the reveal.

---

## Game phases

The game moves through five phases in order: **collect → guess → reveal** (repeated once per player) **→ gameover**.

---

## Collect phase — writing statements

Every player sees four text inputs and a row of "truth" / "🤥 the lie" toggle buttons.

**Step by step:**
1. Each player types four statements about themselves — maximum 140 characters each.
2. Next to each statement is a button labelled **truth**. Click it once and it switches to **🤥 the lie**, marking that statement as your lie. Only one statement can be the lie at a time; clicking another row moves the mark.
3. When all four statements are filled in and a lie is selected, the **Lock it in** button becomes active. Clicking it sends the submission and shows a "🔒 Locked it in!" toast for 2.5 seconds.

**What the server does on submission:**
- Validates that exactly four non-empty statements exist (each ≤ 140 characters) and that the lie index is one of 0–3.
- **Shuffles the four statements** into a random order before storing them, then remaps the lie index to match the new order. This means the position of your lie on screen doesn't correspond to the position you typed it in — neither you nor anyone else can infer the lie from its slot.
- Marks the player as submitted and checks whether the game can auto-start.

After submitting, a player sees a waiting screen listing who hasn't submitted yet.

**Starting the round (host):** Once at least one player has submitted and there are at least 2 members in the room, the host sees a **"Start guessing with N players →"** button. Clicking it emits `tt:begin` and moves directly to the guess phase — players who haven't submitted yet are skipped. The game also auto-starts without the button if every present player submits.

> **Note:** The host can force-start with as few as 1 submission plus 2 room members. Players who haven't submitted will simply never be the "featured" player that round.

---

## Guess phase — voting on the lie

One player is chosen as the **featured player** for the round. Their four (shuffled) statements are shown to everyone.

- The featured player sees their own statements but cannot vote and is told to "act natural."
- All other players see the prompt **"Which is [Name]'s lie?"** and tap the statement they suspect.
- Voting is one-shot: once you tap a statement, your choice is locked (the button is disabled for you). A "👈 your pick" label confirms your selection, and as other players vote their names appear as ✅ checkmarks below.

**Auto-reveal:** When every eligible (non-featured) present player has voted, the server automatically triggers the reveal.

**Host force-reveal:** The host sees an **"Everyone's in — reveal now"** button at any time during the guess phase. Clicking it immediately reveals the answer regardless of how many players have voted (`tt:force`). Use this if someone is taking too long.

---

## Reveal phase — seeing the results

All four statements flip to show which was the lie (highlighted in red with **🤥 THE LIE**) and which are truths (green with **✓ truth**). Each statement also shows a vote count.

**Scoring on reveal:**
- Each non-featured player who picked the correct statement earns **+100 points**.
- The featured player earns **+50 points for every person they fooled** (i.e., every wrong vote).
- Sound effects play: a correct-answer chime if you found the lie, a wrong-answer tone if you were fooled.

A summary line announces how many people the featured player fooled and their bonus. The host then sees either **"Next player →"** or **"Final standings →"** depending on whether more rounds remain.

**Player disconnect during guess phase:** If the featured player disconnects, the server immediately reveals the round with whatever votes were cast. If a non-featured voter disconnects, the server checks whether everyone remaining has now voted — if so, the reveal triggers automatically.

---

## Advancing rounds (host)

After each reveal the host clicks **Next player →** (`tt:next`) to move to the next featured player. This repeats until every player who submitted has had their turn. After the last round, the button changes to **Final standings →**, and clicking it ends the game.

---

## Gameover phase

A leaderboard shows all players sorted by score, with medal emojis for the top three. The host sees a **Play again** button (`tt:newGame`) that wipes all statements, votes, scores, and submissions and returns every current room member to the collect phase for a fresh game.

---

## Host controls summary

| Button | Phase | What it does |
|---|---|---|
| Start guessing → | Collect | Force-starts the guess phase with submitted players |
| Everyone's in — reveal now | Guess | Immediately reveals the lie |
| Next player → / Final standings → | Reveal | Advances to next round or gameover |
| Play again | Gameover | Full reset, new game |

---

## Scoring quick reference

| Event | Points |
|---|---|
| Correctly identify the lie | +100 |
| Featured player fools a guesser | +50 per person fooled |
