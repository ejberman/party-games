# Overview

_Party Games · Overview_

Party Games is a browser-based multiplayer game hub. Players land on a home page, pick one of several party games or join a friend's room with a short code, and immediately play together in real time — no accounts, no downloads, no waiting for a server to spin up a database.

## What the app is

The app packages five fully playable games and one coming-soon placeholder under a single roof:

| Game | Emoji | Min players | What it is |
|---|---|---|---|
| **Random Picker** | 🎡 | 2 | A wheel that randomly selects one player |
| **Disordered Order** | 🔀 | 1 | Emoji Mastermind — crack the hidden order |
| **Beopardy** | 🧠 | 2 | Buzz-in trivia with Daily Doubles and a Final round |
| **Two Truths & a Lie** | 🕵️ | 3 | Bluff your friends with two real facts and one lie |
| **Punchline** | 🎤 | 3 | Quiplash-style writing game — funniest answer wins |
| Buzzword Bingo | 🟦 | 2 | *(coming soon — not yet playable)* |

## How it's organized

Every game shares the same room infrastructure: one player starts a game room (getting a random code), others join by entering that code on the home page, and everyone stays in sync through a WebSocket connection. There are no user accounts — players identify themselves with a display name typed on arrival.

All multiplayer state lives **in the server's memory** for the duration of a session. When everyone leaves a room, the data is gone. Nothing is written to a database or external service.

The first player into a room becomes the **host**. The host has extra controls in most games (starting rounds, forcing advances, revealing answers). If the host disconnects the role transfers automatically to the next connected player.

## How to read this manual

- **[Rooms & Players](#doc:rooms-and-players)** — how rooms, codes, and player names work for every game.
- One chapter per game — covers what players do, what the host controls, and anything to watch out for.
- **[Data model](#doc:data-model)** — where data lives and what the server actually stores.
- **[Services & infrastructure](#doc:services)** — what outside dependencies the app has (spoiler: essentially none).
