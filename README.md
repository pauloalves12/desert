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

**Light.** The sky is rendered a second time into its own scene and convolved
into an irradiance probe, so every surface receives directional skylight — cool
from the zenith, warm from the sun's side, sand bounce from below — instead of one
averaged hemisphere colour. Dune self-shadowing and sky occlusion are both
ray-marched over the height grid on the CPU, blurred, and packed into a
two-channel texture: the first channel drops whole dune faces into shade at low
sun, the second darkens only the indirect term so hollows read as hollows. The
animals cast real shadow-map shadows from a frustum that follows the stretch of
caravan the camera is looking at, which buys about triple the texel density.

**Camels.** One rigged skeleton is evaluated per animal each frame and its bone
matrices are written into fourteen `InstancedMesh` parts. The gait is a *pace* —
the legs on one side swing together — which is what gives a walking camel its
side-to-side rock. Heads turn now and then; riders shift in the saddle.

**Sand.** Ripples are procedural at two scales — centimetre ripples up close,
metre-scale megaripples that carry the texture into the distance — with analytic
normal perturbation and distance, slope and aliasing fades. Slip faces get
avalanche grooves instead of ripples. Backlit sand forward-scatters, so crests
glow when you look into the sun, and dry quartz keeps a faint sheen at glancing
angles. Footprints are a recycled ring of decals that fade with age and erode
faster in wind; each planted hoof presses its own contact patch into the sand.
Grains stream off the crests as screen-aligned streaks.

**Ground.** Tussocks of dried grass and small stones are scattered across the
sheet, but only where the sand is gentle enough to hold anything — never on a
slip face. They are what give the dunes their scale. The scatter is rebuilt
whenever the terrain recenters, so it travels with the caravan.

**Sky and air.** A gradient dome with a large sun disk, horizon glow, dust haze
matched to the scene fog, and stars that come out as it darkens. High cirrus
drifts overhead, projected onto a notional plane and stretched along the wind,
lit from the sun's side and thin enough for the sun to burn through. Haze is
stratified by height, dense in the troughs and thin over the crests, so distant
dunes stack in layers rather than fading uniformly. Five time-of-day keyframes
are interpolated for sky, sun, cloud, fill light, fog, exposure and shadow tint.

## Controls

| Control | |
|---|---|
| Time | afternoon → golden hour → sunset → dusk |
| Caravan | 10–40 camels |
| Wind | ripple strength, blowing sand, haze, how fast prints erode |
| Camera | Dune overview (orbit with the mouse), Alongside, Lead rider, Aerial, Silhouette |

Keys `1`–`5` switch cameras; `H` collapses the panel.

## Performance

21 draw calls and ~700k triangles with the full 40-camel caravan; CPU simulation
costs about 1.0 ms per frame (1.9 ms at the 95th percentile, 3.9 ms worst case
while a terrain slice rebakes). The sky is drawn after the opaque pass, so the
cloud layer only shades pixels that are actually sky. Device pixel ratio is
clamped to 2 and steps down automatically if the frame rate drops.
