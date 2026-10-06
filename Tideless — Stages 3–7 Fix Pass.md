# TIDELESS — Stages 3–7 Fix Pass

*Code is **Written (unverified)**. It has not been compiled or run. I can't compile it here, and I won't say it compiles until you've built it in Unreal.*

## 1. Result for every audit item

| ID | Issue | Result |
| --- | --- | --- |
| C1 | Phone controls depended on the default joysticks | **Fixed in code.** A deliberate phone HUD now has a move stick, a look pad, and 7 buttons placed from screen size. The default joysticks are turned off. Layout checked by script at 5 aspect ratios (no overlaps, nothing off-screen, buttons ≥ 0.15 of screen height). **Not tested on a phone.** |
| C2, M9 | Attacks hit through walls | **Fixed.** Attacks need a clear line to the target, and enemies don't swing without one. |
| C3, M3 | No pathfinding | **Improved, not fully solved.** See section 2. |
| M1 | Lines ignored time of day | **Fixed.** Every standing now has a day and a night line. |
| M2, m15 | NPCs popped into place | **Fixed.** Far NPCs move to their scheduled spot while hidden. |
| M4 | Hidden zones kept collision | **Fixed (this round).** See section 2. |
| M5, M6 | Perf CSV folder and reset | **Fixed.** |
| M7 | Combat HUD forced on | **Fixed (this round).** Off by default, switched by `Tideless.CombatHud 1`. |
| M8 | NPC death lifecycle | **Fixed.** NPC and enemy deaths are permanent. Only the player and training dummies revive. |
| M10 | Crabs had no leash | **Fixed.** Every enemy has a leash and walks home when it goes too far. |
| M11 | Token timeout | **Fixed.** Tokens refresh during a swing. |
| M12, M13 | Witnesses | **Fixed.** Dead NPCs report nothing, and visibility is checked from where the hit happened. |
| M14 | Reputation test commands | **Fixed.** New `Tideless.RepSet` sets an absolute value. |
| m1, m2 | Zone count and "one draw call" | **Corrected in the docs:** 15 zones, and draw-call figures are estimates. |
| m3 | Last rooftop jump | **Fixed.** The audit's 482 cm figure was measured wrongly, but the real flaw was a jump with no landing overlap. The roof moved and the check now requires 200 cm of overlap. |
| m4 | Layout never physically verified | **Still true.** Only you can verify this in the editor. |
| m5–m10 | Clock event, enum checks, restore reset, gold label, NPC offsets, marker length | **Fixed.** |
| m11 | Old Stage 3 builder | **Removed.** |
| m12 | Interaction | **Fixed.** It picks the best interactable in a 60° cone. |
| m13, m14, m16 | Debug-only UI, mechanical NPC motion, scalability of zones | **Left as is.** They're design limits, not bugs, at this scale. |

## 2. This round's four items

1. **Zone collision.** Zones now switch collision with visibility. Enemies freeze beyond 55 m and NPCs hide beyond 60 m, which is inside the zone's 60 m padding, so nothing should fall through. **Risk:** switching collision on many instances might hitch on a phone. If it does, set `bToggleCollision` to false on the builder's zone streamer.
2. **Pathfinding.** NPCs and enemies now use real **NavMesh pathing** when the level has a NavMesh, and fall back to obstacle avoidance when it doesn't. I can't create or test a NavMesh from here, so the NavMesh route is unverified. See the setup steps below.
3. **Combat HUD.** The forced-on line is removed. It is off by default, and `Tideless.CombatHud 1` turns it on for the player only.
4. **Markers.** Off by default. `Tideless.Markers 1` shows them.

## 3. What you must do manually (needs a PC)

1. Replace `Source` and `Config` with `Tideless_stage7_fixed2.zip`, regenerate project files, and build.
2. **NavMesh setup (for real pathfinding):**
   - Project Settings → Navigation Mesh → set **Runtime Generation** to **Dynamic**, because the level is built at runtime.
   - Drag a **NavMeshBoundsVolume** into the map and scale it to cover the whole slice (about 600 m by 200 m by 40 m).
   - Press Play, then press P in the viewport to see the green NavMesh. If it's missing, pathfinding falls back to avoidance.
3. Console commands: `Tideless.CombatHud 1`, `Tideless.Markers 1`, `Tideless.UseNavMesh 0|1`, `Tideless.RepSet 0 -60`, `Tideless.SetTime 22`.

## 4. Verification checklist (report each result to me)

**Build**

- [ ] The project builds with **no errors** (paste any errors).
- [ ] All 16 automated tests pass.

**Phone (first)**

- [ ] The stick moves you, the pad turns the camera, and all 7 buttons work.
- [ ] No overlaps on your phone in landscape. Rotate it: the HUD should rebuild.
- [ ] Look speed and direction feel right. If not, tell me and I'll adjust.
- [ ] A short drag on the look pad doesn't lag or jump.

**World**

- [ ] Walking away from a zone makes it vanish with no hitch, and nothing falls through.
- [ ] Enemies far away stay in place and wake up when you return.
- [ ] Attacks don't land through the arena walls or the workshop walls.
- [ ] With the NavMesh: NPCs walk around stalls. Without it: they sidestep or recover when stuck.

**Systems**

- [ ] `Tideless.CombatHud 1` shows combat numbers, and the default shows nothing.
- [ ] Rooftop route: all 14 roofs can be jumped, including the last one onto terrace 3.

## 5. Still true

- I can't confirm a compile, and I can't confirm any of the new Slate widget or NavMesh code works until you run it.
- The stick shows its push only by getting brighter. A real thumb graphic needs art.
- Look direction and speed are first guesses.
- There is still no Stage 8 work. I agree with the audit: compile and test on a phone first.