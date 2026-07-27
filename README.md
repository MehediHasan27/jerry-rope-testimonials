# Rope-hauled testimonial clothesline

A testimonial section where a rigged 3D character hauls a rope hand over hand. The testimonials hang from that rope on loops like laundry — each one is a
real cloth mesh, so it ripples, billows and furls its free edge in a travelling wind field.
Drag the line (or use the arrows, dots, or ← →): he pulls, the front card travels off canvas,
and the one peeking at the right takes its place.

Open `index.html` — it runs from `file://`, no build step and no network.

## What is actually simulated

| Piece | How it works |
| --- | --- |
| The rope | 56-point verlet chain sprung to a designed baseline. The visible rope is real braid geometry: three strands helically wound around a dark core at a constant lay, each strand its own swept tube, resampled every frame at ~4 px so the twist holds at any zoom. The lay shifts by exactly the length hauled, so the rope is seen **running through her hands** rather than sitting still while the cards move; slack pays out of the loose end as she pulls. A loaded fist bends the line into it. |
| The cards | 24×14 cloth grids pinned at their two top corners, raked back as they hang so a sheet can never pass through the character's legs. The top edge rides the real rope curve between its loops; folds travel across the surface, the hem undulates, the bottom corners furl. Normals are recomputed per frame, so lighting and shadows follow the deformation. |
| The puller | A rigged humanoid GLB, skeleton only — every pose is authored, worked out against filmed reference of someone actually hauling a line. The rope is gripped on a **diagonal across the body**: leading fist high in front of the sternum where the taut line arrives, trailing fist low by the belly, slack falling away from it. Each hand hauls down that diagonal, releases, and swings back to the catch — both hands on the line nearly all the time, with a short tight recovery. Hips drop and travel back under load, the lean is spread across three spine joints over tucked hips, arms are solved with CCD IK plus an elbow pole onto two grip points that slide along the rope half a cycle apart. Each fist **opens on its recovery and clamps shut as it takes load**. Under tension her planted feet are dragged toward the rope, and past a threshold she lifts the trailing foot and **re-braces** — feet alternating, so she holds her ground over a long haul. Legs are solved analytically in world space — knee position worked out in a chosen plane, each bone set to its bind orientation rotated to aim — so no twist can accumulate down the limb and the foot returns to a flat sole however the leg bends. They solve onto those plants in a wide brace — front leg long, back knee deep, both soles flat — so the knees absorb the weight shift. The brace narrows on phones, where a full-width stance does not fit alongside a readable card. |
| The music | Three grooves are synthesised in WebAudio — no audio files, no licence, and the scheduler knows the tempo. Or load your own track and it is routed through the same analyser. For the built-in grooves the sequencer hands the animation its own downbeat times, so the body is phase-locked to the music rather than chasing bass spikes; a loaded track has no clock, so there the analyser listens and learns the tempo. her head nod, shoulder sway and the sound arcs leaving her headphones all read from that, so she is moving to the actual audio. Damped while she is hauling — she is working, after all. |
| The interaction | Spring-snapped infinite carousel. Drag distance and flick velocity decide the next index; the character's effort — lean depth, pull speed, sweat rate, rope tension — is driven by that same value. |

Card text is painted to a canvas texture (so it deforms with the cloth) and duplicated in
the DOM, visually hidden, for screen readers. `prefers-reduced-motion` stills the wind and
the flutter and shortens the transition.

## How it is made to feel like weight

Posture runs on a spring fed by effort, not straight off it, so a released haul lets the body
ride past its mark and settle instead of stopping dead. The head lags the chest by a beat
rather than moving as one rigid piece. Each stroke starts with a beat of anticipation — the
hand reaches a touch further out before it drives back. Between hauls she is never inert: the
weight rocks slowly foot to foot and she breathes.

## Playing something

Nothing plays until you press play (autoplay is blocked, correctly). The three built-in
grooves are generated from oscillators and a noise buffer — kick, snare, hat, bass and a pad
on a 16-step sequencer at 82 / 124 / 104 bpm. "Use your own track" takes any local audio file;
it never leaves your machine.

Under `prefers-reduced-motion` the music still plays but the dancing and the sound arcs are
damped down.

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
