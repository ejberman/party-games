# Beopardy

_Party Games · Features_

Beopardy is a Jeopardy!-style trivia game designed for groups playing from their phones. Players buzz in to claim clues, wager on Daily Doubles, and finish with a Final Beopardy round. The clever part: instead of a separate game-show host, the app randomly assigns one player as a **verifier** for each clue — they privately see the answer and judge whether the buzzer's response is correct or wrong.

## Roles

- **Host** — the first socket into the room. Controls game start, pack selection, and can skip or force-advance phases. The host also *plays* — they are not locked out of buzzing.
- **Verifier** — randomly assigned per clue from players who didn't buzz on that clue. Receives the answer privately and clicks Correct or Wrong. The verifier cannot buzz on the same clue (to prevent self-judging).
- **Controller** — the player who most recently answered correctly; they pick the next clue from the board.

## Setup

The host selects a **question pack** from those bundled with the app. Clicking a pack immediately:

- Builds the board from that pack's categories and clue values.
- Randomly designates one clue (never the cheapest row) as the **Daily Double** — hidden until selected.
- Registers all currently connected players with a score of 0.
- Assigns board control to the host.

## Board phase

The board is a grid of categories × clue values. The player with **board control** (and the host, always) can click any unused cell. Selecting a cell reveals the clue text and moves to the appropriate answering phase.

**Normal clue:**
1. Clue text is shown to everyone.
2. Any player (except the verifier and anyone locked out from a wrong answer on this clue) can click **Buzz**.
3. Buzzing assigns a random verifier from the remaining players and moves to the **judging** phase.
4. The verifier privately receives the clue and answer. They can choose to **Reveal answer** (unblur it on their card) before judging.
5. Verifier clicks **Correct** (score +value, that player gets board control) or **Wrong** (score −value, they're locked out, buzzing reopens for others).
6. If everyone eligible is locked out, the clue is resolved without a winner and the verifier gets board control.

**Daily Double:**
1. When selected, only the controller can answer — everyone else is locked out for this clue.
2. The controller enters a **wager** (minimum 100, maximum their current score or the highest clue value, whichever is larger). There is also a **True Daily Double** button that wagers the maximum automatically.
3. The verifier sees the answer privately and judges correct/wrong the same way as a normal clue.

**Skip clue:** The controller (or host) can skip a clue, resolving it without any score change.

## Final Beopardy

When all clues are used (or the host clicks **Skip to Final**), the game enters three sub-phases:

1. **Final Wager** — All players privately enter a wager (0 to their score, or 0 if their score is negative). The next phase begins when everyone locks in, or the host force-continues.
2. **Final Answer** — The final category and clue are shown. A 60-second countdown runs on each player's screen. Players type and lock in their answer. The host can force-continue.
3. **Final Judging** — The host sees every player's answer and wager. They mark each one Correct or Incorrect, then click **Apply scores** to add/subtract wagers and move to game-over.

## Game over & new game

A fanfare plays when the game-over phase is reached. The host can start a new game (returns to pack selection, resets all scores).

## What to be careful about

- **Player identity is name-based.** If you refresh mid-game and re-enter the same name, your score is restored. If you change your name, you get a new player record and lose your old score.
- **Verifier assignment is random.** In a two-player game the players verify each other's answers — that's intentional.
- **Wagers are private.** No one sees another player's Final wager until judging begins.
- **Board control does not equal host.** Only the host can start games, skip to Final, or force-advance phases. The controller just picks the next clue.
- **Data lives in memory.** See the [Data model](#doc:data-model) chapter — if the server restarts, all scores are gone.
