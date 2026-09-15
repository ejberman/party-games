# Data model

_Party Games · Data_

All data in this app lives in the **server process's memory** for the lifetime of a room session. There is no database, no file storage, and nothing is written to disk. When everyone leaves a room, the data is deleted. If the server restarts, everything is gone.

## Room

A room is the top-level container for a session. It is created the moment the first player joins with a valid game ID and deleted the moment the last player disconnects.

**Fields stored per room:**
- `code` — the uppercase short code players use to join (e.g. `"XKCD"`).
- `gameId` — which game is loaded in this room (`"wheel"`, `"disordered"`, `"beopardy"`, `"two-truths"`, or `"punchline"`).
- `members` — a Map from socket ID → `{ id, name }`. This is the live roster of connected players.
- `game` — the **public** game state object. Everything here is broadcast to all clients on every state update.
- `private` — the **secret** game state object. Contents are never broadcast to all clients; game modules send pieces of it to specific sockets only (e.g. the verifier's answer card in Beopardy, or a player's guess feedback in Disordered Order).
- `createdAt` — timestamp of room creation.

The `publicState()` function strips `private` and maps `members` to plain `{ id, name }` pairs before broadcasting — clients never see raw socket IDs beyond their own or secret data of any kind.

**Touched by:** [Rooms & Players](#doc:rooms-and-players), every game feature.

## Member

A member is an entry in the room's `members` Map, keyed by the player's socket ID.

**Fields:** `id` (socket ID), `name` (display name, max 24 characters).

Members are added on join and removed on disconnect. Because some games key scoring by name rather than socket ID, a player who reconnects with the same name can recover their game record even though their socket ID is new.

**Touched by:** [Rooms & Players](#doc:rooms-and-players).

## Game state

Each game module owns and writes its own slice of `room.game` (public) and `room.private` (secret). What's in those objects depends on which game is running.

### Wheel state

`room.game` contains: `rotation` (cumulative degrees, so animations always spin forward), `spinning` (boolean lock), `winner` (the last winning Member object or null).

**Touched by:** [Random Picker](#doc:random-picker).

### Disordered state

`room.game` contains: `phase` (`"setup"` | `"playing"` | `"revealed"`), `roundId`, `n` (puzzle size), `palette` (the sorted emoji set — known to all), `answer` (null until the host reveals), `players` (map of socket ID → `{ attempts, solved, solvedAt }`), `hostId`.

`room.private` contains: `secret` (the actual hidden emoji sequence — never broadcast).

Guess feedback is emitted **only** to the guessing socket, never broadcast to the room. Other players see only attempts and solved status from the `players` map.

**Touched by:** [Disordered Order](#doc:disordered-order).

### Beopardy state

`room.game` contains: `phase`, `players` (name key → `{ name, score }`), `board` (category/clue grid with `used` flags), `active` (current clue coordinates + value), `buzzedKey`, `verifierKey`, `lockedKeys`, `controlKey`, `wager`, `revealedAnswer`, `final` (Final Beopardy data), `hostId`, `packs` (list of available packs for selection), `packId`, `packTitle`.

`room.private` contains: the full `pack` object (all question text and answers), `dd` (Daily Double coordinates), `finalWagers`, `finalAnswers`. Answer text for the active clue is sent privately only to the verifier via `beopardy:answerinfo`.

Player records are keyed by **lowercase name** (`nameKey`), making them reconnect-proof.

**Touched by:** [Beopardy](#doc:beopardy).

### Two Truths state

`room.game` contains: `phase` (`"collect"` | `"guess"` | `"reveal"` | `"gameover"`), `players` (name key → `{ name, score }`), `submitted` (list of name keys who have locked in statements), `order` (shuffled play order), `roundIdx`, `featuredKey`, `statements` (the featured player's shuffled statements — public during guess phase), `voted` (list of who has voted), `reveal` (vote counts and lie index — shown after reveal), `hostId`.

`room.private` contains: `statements` (all players' shuffled statements — only the featured player's moves to public during their round), `lies` (each player's true lie index — never public until reveal), `votes` (each voter's choice — never public until reveal).

**Touched by:** [Two Truths & a Lie](#doc:two-truths).

### Punchline state

`room.game` contains: `phase` (`"lobby"` | `"write"` | `"vote"` | `"results"` | `"gameover"`), `players` (name key → `{ name, score }`), `round` (counter), `prompt` (current prompt text), `answered` (list of name keys who have answered), `voted` (list of name keys who have voted), `gallery` (anonymized answer list for voting — `[{ aid, text }]`), `reveals` (full answer list with authors and vote counts — only in results phase), `hostId`.

`room.private` contains: `answers` (name key → answer text), `authors` (answer ID → name key), `votes` (name key → answer ID voted for), `usedPrompts` (indices into the prompt list, to avoid repeats).

During the vote phase, answer authorship stays in `room.private.authors` and is only merged into the public `reveals` list when the results phase begins.

**Touched by:** [Punchline](#doc:punchline).

## Browser storage

The player's display name is saved to **browser `localStorage`** under a simple key. This is the only data that persists across page loads on the client side. It is not sent anywhere beyond being used to pre-fill the name form.

## What leaves the app

Nothing. There are no outbound HTTP calls, no analytics, no third-party APIs. All communication is WebSocket traffic between the browser and the same Node.js process that serves the Next.js pages.
