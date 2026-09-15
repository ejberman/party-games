# Disordered Order

_Party Games · Features_

Disordered Order is an emoji Mastermind puzzle. The server hides a secret sequence of distinct emojis; players know *which* emojis are in play but not their order. Each guess tells you only how many are in the correct position. It's a race: the first player to crack the sequence wins.

## Setup

The host picks a **puzzle length** (4–8 emojis) from a selector on the lobby screen, then clicks **Start round**. The server:

1. Draws that many emojis at random from a pool of 28 visually distinct characters.
2. Shuffles them into a secret order (stored privately — never sent to clients).
3. Sends every client the same **palette** — the set of emojis in sorted order, with no position information.

## Playing a round

Each player gets their own independent board pre-shuffled into a random starting order. Players rearrange their board to match what they think the secret is, then submit a guess.

**Moving emojis on the board:**

- **Drag and drop** — press and hold a slot, drag it over another, and release to swap the two.
- **Tap to swap** — tap one slot to select it (it highlights), then tap another to swap them. Tapping the same slot again deselects it.
- **Lock a slot** — click the lock button on any slot to pin it in place. Locked slots cannot be dragged or tapped into a swap. Useful once you're confident about a position.

**Submitting a guess:**

Click **Submit guess**. The server validates that the submitted arrangement is a permutation of the palette, then counts how many emojis are in exactly the right position and returns private feedback — only you see your own guess history and score. The room-wide leaderboard (attempt count, solved status) updates for everyone.

A correct guess (all positions right) triggers a fanfare sound and a solved toast for that player.

## Host controls

- **Reveal answer** — ends the round immediately, publishes the secret sequence to all players, and moves to the "revealed" phase. Use this when you want to move on even if not everyone has solved it.
- **New round** — starts a fresh round with the same puzzle length. The host can also change the length before starting.

## Scoring & competition

The game runs in **race mode**: everyone shares one secret sequence, but each player works their own independent board. There is no turn order. Attempt counts and solve times are tracked per player in the room's [Game state](#doc:data-model/game-state), so you can see who solved it and in how many tries.

## What to be careful about

- **Puzzle state resets on new round.** Locked slots, selected slots, guess history — all cleared for every player when a new round starts. The server increments a `roundId` so the client knows when to reset.
- **Guess feedback is private.** Only you see your own guess history. Other players see only whether you've solved it.
- **Minimum and maximum size.** The server clamps `n` to the range 4–8 regardless of what the client sends.
