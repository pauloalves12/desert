# desert

A Tuareg camel caravan crossing the Saharan dunes at golden hour — a single-file,
real-time WebGL scene built with Three.js.

Open `index.html` in a browser. No build step and nothing to install: Three.js r180
is pulled from a CDN through an import map.

## What's in the scene

**Dunes.** The terrain is a procedural heightfield: an asymmetric sawtooth dune
profile — long windward slope, short steep slip face — warped by fractal noise so
the crests meander. A second, angled dune train and a broad basin-and-swell layer
sit on top. The mesh scrolls with the caravan and is rebuilt in time-budgeted
slices, so the journey is effectively endless.

**Shadows.** Two kinds. Dune self-shadowing is ray-marched over the height grid on
the CPU, blurred, and uploaded as a texture the sand shader samples — that is what
drops whole dune faces into shade as the sun gets low. The animals cast real
shadow-map shadows, and the shadow frustum widens as the sun drops so their long
shadows do not clip.

**Camels.** One rigged skeleton is evaluated per animal each frame and its bone
matrices are written into fourteen `InstancedMesh` parts. The gait is a *pace* —
the legs on one side swing together — which is what gives a walking camel its
side-to-side rock. Heads turn now and then; riders shift in the saddle.

**Sand.** Wind ripples are procedural in the sand shader, with an analytic normal
perturbation plus distance and slope fades. Footprints are a recycled ring of
decals that fade with age and erode faster in wind. Grains stream off the crests
as screen-aligned streaks.

**Sky.** A gradient dome with a large sun disk, horizon glow, dust haze matched to
the scene fog, and stars that come out as it darkens. Five time-of-day keyframes
are interpolated for sky, sun, fill light, fog, exposure and shadow tint.

## Controls

| Control | |
|---|---|
| Time | afternoon → golden hour → sunset → dusk |
| Caravan | 10–40 camels |
| Wind | ripple strength, blowing sand, haze, how fast prints erode |
| Camera | Dune overview (orbit with the mouse), Alongside, Lead rider, Aerial, Silhouette |

Keys `1`–`5` switch cameras; `H` collapses the panel.

## Performance

18 draw calls and ~320k triangles with the full 40-camel caravan; CPU simulation
costs about 0.8 ms per frame. Device pixel ratio is clamped to 2 and steps down
automatically if the frame rate drops.
