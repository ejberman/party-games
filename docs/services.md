# Services & infrastructure

_Party Games · Outside services_

Party Games runs entirely self-contained. It makes no calls to outside services and has no third-party integrations of any kind.

## What the app uses

| Concern | What handles it |
|---|---|
| Web server & routing | **Next.js 14** (App Router), run through a custom Node.js entry point (`server.js`) |
| Real-time multiplayer | **Socket.IO 4.8** — both the server library and the browser client library run within the same codebase and process |
| All game data & state | In-process memory (a plain JavaScript `Map` in `server/rooms.js`) |
| Player name persistence | Browser `localStorage` — no server storage |

## No outside services

There is no:

- **Database** — all room and game state is in the server's memory. It does not survive a server restart.
- **Authentication / sign-in provider** — players are identified only by the display name they type. No accounts, no sessions, no tokens.
- **AI or external content API** — Punchline's prompts come from a static JSON file bundled in the repo. There is a comment in the code describing AI generation as a "future seam," but it is not wired up.
- **Email or push notifications** — none.
- **CDN or object storage** — all assets are served by Next.js directly.
- **Analytics or error tracking** — none.

## Scaling note

The code comment in `server/rooms.js` explicitly flags a single-instance constraint: *"Add Redis here if you ever scale to multiple replicas."* The in-memory room store means that if you run more than one server process (e.g. behind a load balancer), players will be split across instances and cannot see each other. The app works correctly on a single server or a single container.
