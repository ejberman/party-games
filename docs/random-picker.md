# Random Picker (wheel)

_Party Games · Features_

Random Picker is the simplest game in the app. It renders a color-coded wheel divided into one segment per player and lets anyone spin it to randomly select a winner. It's useful any time you need to pick who goes first in another game, or settle any arbitrary group decision.

## How it works

Every player currently in the [Room](#doc:data-model/room) gets a slice of the wheel, labeled with their name. The wheel needs **at least two players** before it can spin.

**Spinning:**
1. Any player clicks the **Spin** button.
2. The request goes to the server, which picks the winner at random and calculates the exact final rotation angle.
3. The server broadcasts the target rotation and animation duration (4.5 seconds) to every client simultaneously.
4. All clients animate to the same final position — everyone sees the same result at the same time.
5. After the animation completes, the server emits the winner's name, which appears prominently on screen.

Because the winner is decided server-side before the animation starts, there is no possibility of different players seeing different outcomes. Late joiners or refreshed browsers also sync to the current rotation instantly.

## What to be careful about

- **Only one spin at a time.** While a spin is in progress the Spin button is disabled. Clicking it has no effect.
- **Player list is live.** The wheel redraws whenever someone joins or leaves. If a player disconnects mid-spin, the server guards against operating on a torn-down room, but the visual result may look odd if the player count changes while the wheel is still animating.
- **No scoring or history.** The app does not record who won previous spins. Once you spin again, the previous winner is gone.
