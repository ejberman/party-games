# Rooms & Players

_Party Games · Features_

Rooms are the foundation of every session. Before any game can be played, someone creates a room and the others join it. Everything in a room is ephemeral — it lives only in server memory while at least one player is connected.

## Starting a new game

From the home page (`/`), players see a card for each available game. Clicking a card immediately:

1. Generates a random **room code** (a short uppercase string).
2. Navigates the creator's browser to `/room/<CODE>?game=<gameId>`.

That navigation is what "creates" the room — the room itself does not exist on the server until someone successfully joins it with a valid `gameId`. Only the first joiner (who has the `?game=` parameter in their URL) can create a room; anyone who types the code without that parameter into a room that doesn't yet exist will see a "That room doesn't exist" error.

Games marked **Coming soon** in the catalog are disabled — their cards are grayed out and can't be clicked.

## Joining a room

There are two ways to join an existing room:

- **Paste a room code** into the input on the home page and click Join.
- **Follow a share link** — the room page has a "Room code · tap to share" button that copies the full URL to the clipboard and briefly shows "Copied!" for 1.5 seconds.

Both routes land the browser at `/room/<CODE>`. Because there is no authentication, the first thing the room page asks is: **"What should we call you?"** If the player's browser already has a saved name (stored in `localStorage`), it is pre-filled automatically.

Once a name is confirmed:

1. The client opens a **WebSocket connection** (Socket.IO) to the server.
2. It emits a `room:join` event carrying the room code, the player's name, and — if present in the URL — the `gameId`.
3. The server looks up the room. If it exists, the player is added. If it doesn't exist and a `gameId` was supplied, the room is created and the game is initialized.
4. The server broadcasts the updated **room state** to every connected player, so all participants see the new member appear immediately.

Player names are trimmed to 24 characters. A nameless join falls back to "Guest".

## Player list and host role

The room page shows a sidebar listing every connected player. The first player to join (the creator) becomes the **host** and gains extra controls in most games — starting rounds, forcing phase advances, and so on. If the host disconnects, the server automatically promotes the next connected player to host.

## Renaming yourself

Players can update their display name at any time. The server validates that the player is still in a room, updates their member record, and broadcasts the change to everyone.

## Sharing the room

The "Room code · tap to share" button in the room header copies the full join URL to the clipboard. Share it however you like — anyone who opens it is taken straight to the name-entry screen for that room.

## Disconnection and cleanup

When a player's browser disconnects (tab closed, network drop, navigation away):

- The server removes them from the room's member list.
- If the room is now **empty**, it is deleted entirely — all game state is gone.
- If players remain, the server runs any game-specific cleanup (e.g., reassigning host, checking whether a round can auto-advance) and broadcasts the updated state.

There is no reconnection grace period — a disconnected player who returns must re-enter their name and rejoin, though their saved name will be pre-filled from `localStorage`.

## What to watch out for

- **Room codes are case-insensitive** when typed, but are stored and displayed in uppercase.
- **No persistence** — rooms vanish the moment the last player leaves. There is no way to resume a session.
- **No authentication** — anyone with the room code or link can join. Names are self-reported and not unique.
- **Game initialization happens on first join** — if the creator's browser navigates away before anyone else joins, the room never forms.
