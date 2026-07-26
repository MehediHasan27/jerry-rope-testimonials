# Rope-hauled testimonial clothesline

A testimonial section where a 3D mouse leans in from the edge of the frame and hauls a rope
hand over hand. The testimonials hang from that rope on loops like laundry — each one is a
real cloth mesh, so it ripples, billows and furls its free edge in a travelling wind field.
Drag the line (or use the arrows, dots, or ← →): he pulls, the front card travels off canvas,
and the one peeking at the right takes its place.

Open `index.html` — it runs from `file://`, no build step and no network.

## What is actually simulated

| Piece | How it works |
| --- | --- |
| The line | 56-point verlet chain sprung to a designed baseline, swept into a lit tube. Wind, the weight of each hanging card, and the character's yanks all deviate it. |
| The cards | 24×14 cloth grids pinned at their two top corners. The top edge rides the real rope curve between its loops; folds travel across the surface, the hem undulates, the bottom corners furl. Normals are recomputed per frame, so lighting and shadows follow the deformation. |
| The character | Procedural rig: lathed torso and snout, tapered limbs with joint balls, two-bone IK arms that grip the rope, hand-over-hand pull cycle, braced legs, swept tail, strain brows, sweat. |
| The interaction | Spring-snapped infinite carousel. Drag distance and flick velocity decide the next index; the character's effort — lean depth, pull speed, sweat rate, rope tension — is driven by that same value. |

Card text is painted to a canvas texture (so it deforms with the cloth) and duplicated in
the DOM, visually hidden, for screen readers. `prefers-reduced-motion` stills the wind and
the flutter and shortens the transition.

## Tuning

Everything worth changing is in `CFG` at the top of the script: rope stiffness, sag and
gust strength; cloth wave amplitude, wavelength and curl; drag thresholds and how many
hand-over-hand cycles a card advance costs. Replace `DATA` with your own quotes — the
sample copy is placeholder.

Poke at the running scene from the console via `window.__jerry`
(`state`, `rope`, `cards`, `rig`, `renderInfo()`).

## Notes

- The character is an original stylized mouse built in `buildMouse()`. It is **not** the
  Warner Bros. character, deliberately — swap in your own GLB there and map its joints onto
  the same names in `rig`; `poseJerry()` drives everything through those.
- `vendor/three.bundle.js` is [three.js](https://threejs.org) r185 (MIT, © three.js authors)
  repacked as a classic script exposing `window.THREE`, because ES-module imports do not work
  over `file://`.
- ~60k triangles, ~150 draw calls, and the simulation costs ~0.4 ms of CPU per frame.
