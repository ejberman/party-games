# Punchline

_Party Games · Features_

Punchline is a Quiplash-style writing game. Every round, the server deals one comedy prompt to the whole room. Everyone writes their funniest answer. The answers are revealed anonymously and players vote for their favorite — you can't vote for your own. Votes become points.

## How a round works

**Write phase:**
1. A prompt is shown to all players simultaneously (drawn randomly from a built-in list of prompts, without repeating until the list is exhausted).
2. Each player types their answer and clicks **Lock** (or presses Enter). Empty answers are rejected.
3. The server records the answer privately (it is never broadcast with authorship attached) and marks the player as answered.
4. When all present players have answered, the game advances automatically to the vote phase. The host can also click **Close submissions** to force-advance (requires at least 2 answers to exist).

**Vote phase:**
1. All answers are shown in a randomized, anonymous gallery.
2. Each player clicks the answer they think is funniest. You cannot vote for your own answer — the server rejects self-votes and the UI prevents clicking your own entry.
3. When all present players have voted, the game advances automatically to results. The host can click **Close voting** to force-advance.

**Results phase:**
- Answers are revealed with their true authors and vote counts.
- Each vote a player received adds to their cumulative score.
- The host clicks **Next round** to play another round, or **Finish** to move to game-over.

## Game over & new game

The host's Finish button ends the game and shows final scores. The host can click **New Game** to reset all state (scores, answers, vote history, prompt history) and return everyone to the lobby for a fresh start.

## What to be careful about

- **Authorship is anonymous during voting.** Answers appear without names in the vote phase — authors are only revealed in results. This is enforced server-side: the public gallery contains answer text only, with authorship stored in `room.private`.
- **No self-votes.** Both the server and client block voting for your own answer. If you try, the vote is simply not recorded.
- **Prompt pool is finite.** There is a fixed list of prompts bundled in `server/games/punchline/prompts.json`. The server shuffles through the whole list before repeating. Adding more prompts means editing that file.
- **Force-advance requires at least 2 answers.** The host cannot force to vote phase if fewer than 2 answers have been submitted — there's nothing to vote on.
- **Scores are cumulative across rounds.** Starting a new game resets scores to zero. There is no mid-game score reset.
