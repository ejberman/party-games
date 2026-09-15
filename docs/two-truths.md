# Three Truths & a Lie

_Party Games · Features_

Three Truths & a Lie (emoji 🕵️) is a bluffing game where each player submits four personal statements — **three true, one lie** — and the rest of the room tries to sniff out the fake. It requires at least 3 players.

## How a game plays out

### 1. Submission phase (collect)
Every player writes four statements about themselves and marks which one is the lie. When you submit, the server **silently shuffles the order** of your four statements so the lie's original position gives no hint to other players. The true lie index is stored privately on the server and is never sent to anyone until the reveal.

The game auto-advances to the guessing phase as soon as every present player has submitted. The host can also force an early start with the **Begin** button, as long as at least one submission exists and there are two or more players.

> **Watch out:** Statements are capped at 140 characters each. Any empty or whitespace-only statement is rejected. You cannot edit your submission once it's sent.

### 2. Guess phase (guess)
Players take turns being **featured**. When it's your turn, your four (shuffled) statements are shown to everyone else, who each tap the one they think is the lie. You cannot vote on your own round.

Once every eligible player has voted, the reveal happens automatically. The host can click **Force Reveal** to skip waiting if someone is slow.

### 3. Reveal
The correct lie is highlighted and everyone's individual guesses are shown. Scoring:
- **Correct guesser** → +100 points
- **Featured player** → +50 points for every player they fooled (i.e., every wrong guess)

After reviewing results, the host clicks **Next** to move to the next featured player. This continues until everyone has been featured.

### 4. Game over
After the last featured player's round ends, the game moves to a final scoreboard. The host can click **New Game** to reset everything and start a fresh round of submissions.

## Host controls

| Button | When available | What it does |
|---|---|---|
| Begin | Collect phase, ≥ 1 submission + ≥ 2 players | Forces the game to start without waiting for all to submit |
| Force Reveal | Guess phase | Triggers the reveal before all eligible players have voted |
| Next | Reveal phase | Advances to the next featured player (or ends the game) |
| New Game | Game-over phase | Clears all statements, votes, and scores and returns to collection |

## Things to know

- **Name-keyed identity** — like [Beopardy](#doc:beopardy), player records are keyed by display name rather than socket ID. If a player refreshes, their submission is preserved as long as they rejoin with the same name.
- **Private data** — statements, lie indices, and in-flight votes are kept in server-side private state and are never broadcast to clients until the reveal. The public game state only shows who has submitted (not what they said).
- **Player departure during guessing** — if the featured player disconnects mid-round, the server immediately reveals the results with whatever votes have been collected so far. If a non-featured voter disconnects, the server checks whether the remaining eligible players have all voted and auto-reveals if so.
- **Minimum two submitted to begin** — the auto-start only fires when all present players have submitted **and** there are at least two submissions. If you have only one submitter the host must wait for more or the game cannot proceed.

## Data touched

This game reads and writes the [Room](#doc:data-model/room) entity (member list, host assignment, game phase) and uses server-private state for statements, lie indices, and votes. See the [Data model](#doc:data-model) for details on where this information lives.
