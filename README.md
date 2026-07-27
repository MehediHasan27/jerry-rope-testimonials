# Rope-hauled testimonial clothesline

A testimonial section where a rigged 3D character hauls a rope hand over hand. The testimonials hang from that rope on loops like laundry — each one is a
real cloth mesh, so it ripples, billows and furls its free edge in a travelling wind field.
Drag the line (or use the arrows, dots, or ← →): he pulls, the front card travels off canvas,
and the one peeking at the right takes its place.

Open `index.html` — it runs from `file://`, no build step and no network.

## What is actually simulated

| Piece | How it works |
| --- | --- |
| The rope | 56-point verlet chain sprung to a designed baseline. The visible rope is real braid geometry: three strands helically wound around a dark core at a constant lay, each strand its own swept tube, resampled every frame at ~4 px so the twist holds at any zoom. Wind, the weight of each hanging card, and the puller's yanks all deviate the line. |
| The cards | 24×14 cloth grids pinned at their two top corners. The top edge rides the real rope curve between its loops; folds travel across the surface, the hem undulates, the bottom corners furl. Normals are recomputed per frame, so lighting and shadows follow the deformation. |
| The puller | A rigged humanoid GLB, skeleton only — every pose is authored. Hips drop and travel back under load, the lean is spread across three spine joints over tucked hips, arms are solved with CCD IK plus an elbow pole onto two grip points that slide along the rope half a cycle apart, and the legs are solved onto planted feet so the knees absorb the weight shift. Fingers are curled into fists once at load. |
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

- **The character asset is a Mixamo (Adobe) humanoid**, included here as
  `assets/puller.glb.js` with its animations stripped. Mixamo's terms cover use in your own
  projects; redistributing the raw character is a grey area, so swap it for a character you
  own before shipping this commercially. Any standard humanoid rig drops in — the bone lookup
  just strips a `mixamorig` prefix, and `poseHuman()` drives everything through those names.
- `vendor/three.bundle.js` is [three.js](https://threejs.org) r185 (MIT, © three.js authors)
  repacked as a classic script exposing `window.THREE`, because ES-module imports do not work
  over `file://`.
- `vendor/GLTFLoader.bundle.js` is the three.js GLTFLoader (MIT) with its two util deps,
  repacked the same way.
- The character is base64 inside a classic script rather than a plain `.glb`, because
  `fetch()` is blocked on `file://` — that keeps the double-click-to-open behaviour.
